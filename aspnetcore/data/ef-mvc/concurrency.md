---
title: 'Öğretici: Eşpara kolu - EF Core ile ASP.NET MVC'
description: Bu öğretici, birden çok kullanıcı aynı varlığı aynı anda güncelleştirdiğinde çakışmaları nasıl işleyeceğini gösterir.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/concurrency
ms.openlocfilehash: 6839e383093b993ff55095f26cf88cd68708f001
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78657397"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a>Öğretici: Eşpara kolu - EF Core ile ASP.NET MVC

Önceki öğreticilerde, verileri nasıl güncelleştirini öğrendiniz. Bu öğretici, birden çok kullanıcı aynı varlığı aynı anda güncelleştirdiğinde çakışmaları nasıl işleyeceğini gösterir.

Bölüm kuruluşuyla çalışan ve eşzamanlılık hatalarını işleyen web sayfaları oluşturursunuz. Aşağıdaki çizimler, eşzamanlılık çakışması oluşursa görüntülenen bazı iletileri de içeren Düzenle ve Sil sayfalarını gösterir.

![Bölüm Edit sayfası](concurrency/_static/edit-error.png)

![Bölüm Silme sayfası](concurrency/_static/delete-error.png)

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Eşzamanlılık çakışmaları hakkında bilgi edinin
> * İzleme özelliği ekleme
> * Departmanlar denetleyicisi ve görünümleri oluşturma
> * Dizin görünümünü güncelleştir
> * Güncelle yöntemlerini güncelleştir
> * Görünümü Güncelleştir
> * Eşzamanlılık çakışmalarını test edin
> * Sil sayfasını güncelleştir
> * Ayrıntıları Güncelleştir ve Görünüm Oluştur

## <a name="prerequisites"></a>Ön koşullar

* [İlgili verileri güncelleştirme](update-related-data.md)

## <a name="concurrency-conflicts"></a>Eşzamanlılık çakışmaları

Eşzamanlılık çakışması, bir kullanıcı bir varlığın verilerini düzenlemek için görüntülediğinde ve ilk kullanıcı değişikliği veritabanına yazılmadan önce aynı varlığın verilerini güncelleştirir. Bu tür çakışmaların algılanmasını etkinleştiremezseniz, veritabanını en son güncelalan kişi diğer kullanıcının değişikliklerinin üzerine yazar. Birçok uygulamada, bu risk kabul edilebilir: az sayıda kullanıcı veya birkaç güncelleştirme varsa veya bazı değişiklikler üzerine yazılmışsa gerçekten kritik değilse, eşzamanlılık için programlama maliyeti yarar dan daha ağır basabilir. Bu durumda, eşzamanlılık çakışmaları işlemek için uygulamayı yapılandırmanız gerekmez.

### <a name="pessimistic-concurrency-locking"></a>Kötümser eşzamanlılık (kilitleme)

Uygulamanızın eşzamanlılık senaryolarında yanlışlıkla veri kaybını önlemesi gerekiyorsa, bunu yapmanın bir yolu veritabanı kilitlerini kullanmaktır. Buna kötümser eşzamanlılık denir. Örneğin, veritabanındaki bir satırı okumadan önce, salt okunur veya güncelleştirme erişimi için bir kilit istersiniz. Güncelleştirme erişimi için bir satırı kilitlerseniz, değiştirilme sürecindeolan verilerin bir kopyasını alacakları için başka kullanıcıların satırı salt okunur veya güncelleştirme erişimi için kilitlemesine izin verilmez. Yalnızca okunur erişim için bir satırı kilitlerseniz, diğerleri yalnızca okunur erişim için de kilitleyebilir, ancak güncelleştirme için kilitlenmez.

Kilitleri yönetmenin dezavantajları vardır. Bu program için karmaşık olabilir. Önemli veritabanı yönetim kaynakları gerektirir ve bir uygulamanın kullanıcı sayısı arttıkça performans sorunlarına neden olabilir. Bu nedenlerden dolayı, tüm veritabanı yönetim sistemleri kötümser eşzamanlılığı desteklemez. Entity Framework Core bunun için yerleşik destek sağlamaz ve bu öğretici bunu nasıl uygulayacağınızı göstermez.

### <a name="optimistic-concurrency"></a>İyimser Eşzamanlılık

Kötümser eşzamanlılık alternatif iyimser eşzamanlılık olduğunu. İyimser eşzamanlılık, eşzamanlılık çakışmalarına izin vermek ve varsa uygun şekilde tepki vermek anlamına gelir. Örneğin, Jane Bölüm Edit sayfasını ziyaret eder ve İngilizce bölümü için bütçe tutarını 350.000,00 TL'den 0,00 TL'ye değiştirir.

![Bütçeyi 0 olarak değiştirme](concurrency/_static/change-budget.png)

Jane **Kaydet'i**tıklatmadan önce, John aynı sayfayı ziyaret eder ve Başlangıç Tarihi alanını 9/1/2007'den 9/1/2013'e değiştirir.

![Başlangıç tarihini 2013 olarak değiştirme](concurrency/_static/change-date.png)

Jane önce **Kaydet'i** tıklatıyor ve tarayıcı Dizin sayfasına döndüğünde değişikliğini görür.

![Bütçe sıfıra değiştirildi](concurrency/_static/budget-zero.png)

Ardından John, 350.000,00 TL bütçegösteren bir Edit sayfasında **Kaydet'i** tıklatır. Bundan sonra ne olacağı eşzamanlılık çakışmaları nasıl ele aldığınıza göre belirlenir.

Seçeneklerden bazıları şunlardır:

* Bir kullanıcının hangi özelliği değiştirdiğini izleyebilir ve yalnızca veritabanındaki ilgili sütunları güncelleştirebilirsiniz.

     Örnek senaryoda, farklı özellikler iki kullanıcı tarafından güncelleştirilmeden hiçbir veri kaybolmaz. Bir dahaki sefere birisi İngilizce bölümüne göz atsa, jane ve John'un değişikliklerini görecekler -- 9/1/2013 başlangıç tarihi ve sıfır dolar lık bir bütçe. Bu güncelleştirme yöntemi, veri kaybına neden olabilecek çakışma sayısını azaltabilir, ancak bir varlığın aynı özelliğinde rakip değişiklikler yapılırsa veri kaybını önleyemez. Varlık Çerçevesi'nin bu şekilde çalışıp çalışmadığına, güncelleştirme kodunuzu nasıl uyguladığınıza bağlıdır. Bir kuruluşun tüm özgün özellik değerlerinin yanı sıra yeni değerleri izlemek için büyük miktarda durum korumanızı gerektirebileceğinden, web uygulamasında genellikle pratik değildir. Sunucu kaynakları gerektirdiğinden veya web sayfasının kendisine (örneğin, gizli alanlarda) veya bir çereze eklenmesi gerektiğinden, büyük miktarda durum korumak uygulama performansını etkileyebilir.

* John'un değişmesine izin verebilirsin Jane'in bozuk paralarını yazabilirsin.

     Birisi İngilizce bölümüne bir daha ki sefere göz atanın, 9/1/2013 ve geri yüklenen $350,000.00 değerini göreceksiniz. Buna Müşteri *Kazançları* veya *Son Kazanç* senaryosu denir. (İstemcinin tüm değerleri veri deposunda bulunanlardan önceliklidir.) Bu bölümün girişinde belirtildiği gibi, eşzamanlılık işleme için herhangi bir kodlama yapmazsanız, bu otomatik olarak gerçekleşir.

* John'un değişikliğinin veritabanında güncelleştirilen olmasını engelleyebilirsiniz.

     Genellikle, bir hata iletisi görüntüler, ona verilerin geçerli durumunu gösterir ve hala bunları yapmak istiyorsa değişikliklerini yeniden uygulamasına izin verirsiniz. Buna Mağaza *Kazançları* senaryosu denir. (Veri depolama değerleri istemci tarafından gönderilen değerlerden önceliklidir.) Bu eğitimde Mağaza Kazançları senaryosunu uygularsınız. Bu yöntem, bir kullanıcı ne olup bittiği konusunda uyarılmadan hiçbir değişikliğin üzerine yazılmamasını sağlar.

### <a name="detecting-concurrency-conflicts"></a>Eşzamanlılık çakışmalarını algılama

Varlık Çerçevesi'nin `DbConcurrencyException` attığı özel durumları işleyerek çakışmaları çözebilirsiniz. Bu özel durumların ne zaman atılacağını bilmek için Varlık Çerçevesi'nin çakışmaları algılayabilmesi gerekir. Bu nedenle, veritabanını ve veri modelini uygun şekilde yapılandırmanız gerekir. Çakışma algılamayı etkinleştirmek için bazı seçenekler şunlardır:

* Veritabanı tablosunda, bir satırın ne zaman değiştirildiğini belirlemek için kullanılabilecek bir izleme sütunu ekleyin. Daha sonra Varlık Çerçevesi'ni SQL Update veya Delete komutlarının Where yan tümcesine bu sütunu ekecek şekilde yapılandırabilirsiniz.

     İzleme sütununun veri türü genellikle. `rowversion` Değer, `rowversion` satır her güncelleştiride artılan sıralı bir sayıdır. Bir Güncelleştirme veya Sil komutunda, Where yan tümcesi izleme sütununun (özgün satır sürümü) özgün değerini içerir. Güncelleştirilen satır başka bir kullanıcı tarafından değiştirildiyse, `rowversion` sütundaki değer özgün değerden farklıdır, bu nedenle Güncelleştirme veya Sil deyimi Where yan tümcesi nedeniyle güncellenecek satırı bulamıyor. Varlık Çerçevesi, Güncelleştirme veya Sil komutu tarafından hiçbir satırın güncelleştirilmediğini fark ettiğinde (diğer bir süre, etkilenen satır sayısı sıfır olduğunda), bunu eşzamanlılık çakışması olarak yorumlar.

* Varlık Çerçevesi'ni, Güncelleştirme ve Sil komutlarının Bulunduğu Yer yan tümcesindeki tablodaki her sütunun özgün değerlerini içerecek şekilde yapılandırın.

     İlk seçenekte olduğu gibi, satır ilk okundundan beri satırdaki herhangi bir şey değiştiyse, Nerede yan tümcesi güncelleştirmek için bir satır döndürmez ve Varlık Çerçevesi eşzamanlılık çakışması olarak yorumlar. Çok sayıda sütunu olan veritabanı tabloları için, bu yaklaşım çok büyük Where yan tümcelerine neden olabilir ve büyük miktarda durum korumanızı gerektirebilir. Daha önce de belirtildiği gibi, büyük miktarda durum bakımı uygulama performansını etkileyebilir. Bu nedenle bu yaklaşım genellikle önerilmez ve bu öğreticide kullanılan yöntem değildir.

     Eşzamanlılık için bu yaklaşımı uygulamak istiyorsanız, eşzamanlılığı izlemek istediğiniz varlıktaki tüm birincil anahtar olmayan özellikleri bunlara `ConcurrencyCheck` öznitelik ekleyerek işaretlemeniz gerekir. Bu değişiklik, Varlık Çerçevesi'nin Güncelleştirme ve Silme deyimlerinin SQL Where yan tümcesine tüm sütunları eklemesini sağlar.

Bu öğreticinin geri kalanında Bölüm `rowversion` kuruluşuna bir izleme özelliği ekler, bir denetleyici ve görünümler oluşturur ve her şeyin doğru çalıştığını doğrulamak için sınama sağlarsınız.

## <a name="add-a-tracking-property"></a>İzleme özelliği ekleme

*Modeller/Department.cs*olarak, RowVersion adlı bir izleme özelliği ekleyin:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

Öznitelik, `Timestamp` bu sütunun veritabanına gönderilen Güncelleştirme ve Sil komutlarının Nerede yan tümcesine dahil olacağını belirtir. SQL Server'ın `Timestamp` önceki sürümleri, SQL `rowversion` yerine `timestamp` gelmeden önce bir SQL veri türü kullandığından öznitelik çağrılır. .NET türü `rowversion` bir bayt dizisidir.

Akıcı API'yi kullanmayı tercih ederseniz, aşağıdaki `IsConcurrencyToken` örnekte gösterildiği gibi izleme özelliğini belirtmek için *(Data/SchoolContext.cs'de)* yöntemini kullanabilirsiniz:

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

Bir özellik ekleyerek veritabanı modelini değiştirdiniz, bu nedenle başka bir geçiş yapmanız gerekir.

Değişikliklerinizi kaydedin ve projeyi oluşturun ve ardından komut penceresinde aşağıdaki komutları girin:

```dotnetcli
dotnet ef migrations add RowVersion
```

```dotnetcli
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a>Departmanlar denetleyicisi ve görünümleri oluşturma

Daha önce Öğrenciler, Kurslar ve Eğitmenler için yaptığınız gibi bir Bölüm denetleyicisi ve görüşlerini iskele.

![İskele Bölümü](concurrency/_static/add-departments-controller.png)

*DepartmentsController.cs* dosyasında, bölüm yöneticisi açılır listelerin yalnızca soyadı yerine eğitmenin tam adını içermesini sağlayacak şekilde "FirstMidName" ile "FullName" dört olaylarını da değiştirin.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a>Dizin görünümünü güncelleştir

İskele altyapısı Dizin görünümünde bir RowVersion sütunu oluşturdu, ancak bu alan görüntülenmemelidir.

*Görünümler/Bölümler/Index.cshtml'deki* kodu aşağıdaki kodla değiştirin.

[!code-html[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

Bu, başlığı "Bölümler" olarak değiştirir, RowVersion sütununa silinir ve yöneticinin ilk adı yerine tam adı gösterir.

## <a name="update-edit-methods"></a>Güncelle yöntemlerini güncelleştir

Hem HttpGet `Edit` yönteminde `Details` hem de `AsNoTracking`yönteme . HttpGet `Edit` yönteminde, Yönetici için istekli yükleme ekleyin.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

HttpPost `Edit` yönteminin varolan kodunu aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

Kod güncellenecek bölümü okumaya çalışarak başlar. Yöntem `FirstOrDefaultAsync` null dönerse, bölüm başka bir kullanıcı tarafından silinir. Bu durumda kod, bir departman varlığı oluşturmak için deftere nakledilen form değerlerini kullanır, böylece Edit sayfası bir hata iletisi ile yeniden görüntülenebilir. Alternatif olarak, departman alanlarını yeniden görüntülemeden yalnızca bir hata iletisi görüntülerseniz, departman varlığını yeniden oluşturmanız gerekir.

Görünüm özgün `RowVersion` değeri gizli bir alanda depolar ve bu `rowVersion` yöntem bu değeri parametrede alır. Aramadan `SaveChanges`önce, varlığın koleksiyonuna `RowVersion` `OriginalValues` özgün özellik değerini koymanız gerekir.

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

Daha sonra Entity Framework bir SQL UPDATE komutu oluşturduğunda, bu komut özgün `RowVersion` değeri olan bir satırı arayan bir WHERE yan tümcesi içerir. Güncelleştirme komutundan hiçbir satır etkilenmezse (hiçbir `RowVersion` satır özgün değere `DbUpdateConcurrencyException` sahip değilse), Varlık Çerçevesi bir özel durum oluşturur.

Bu özel durum için catch bloğundaki kod, özel durum nesnesindeki `Entries` özellikten güncelleştirilmiş değerlere sahip etkilenen Departman varlığını alır.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

Koleksiyonda `Entries` yalnızca bir `EntityEntry` nesne olacaktır.  Kullanıcı tarafından girilen yeni değerleri ve geçerli veritabanı değerlerini almak için bu nesneyi kullanabilirsiniz.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

Kod, kullanıcının Edit sayfasına girdiklerinden farklı veritabanı değerleri olan her sütun için özel bir hata iletisi ekler (kısaltma için burada yalnızca bir alan gösterilir).

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

Son olarak, kod `RowVersion` veritabanından `departmentToUpdate` alınan yeni değerin değerini ayarlar. Bu `RowVersion` yeni değer, Edit sayfası yeniden görüntülendiğinde gizli alanda depolanır ve kullanıcı **Kaydet'i**bir sonraki tıklattığında, yalnızca Edit sayfasının yeniden görüntülenmesinden bu yana oluşan eşzamanlılık hataları olur.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

Eski `ModelState.Remove` `RowVersion` değere `ModelState` sahip olduğundan ifade gereklidir. Görünümde, bir `ModelState` alanın değeri, her ikisi de mevcut olduğunda model özellik değerlerinin üzerinde önceliklidir.

## <a name="update-edit-view"></a>Görünümü Güncelleştir

*Görünümler/Bölümler/Edit.cshtml*olarak aşağıdaki değişiklikleri yapın:

* `RowVersion` Özellik için gizli alanı hemen izleyen, özellik değerini kaydetmek için gizli bir alan `DepartmentID` ekleyin.

* Açılan listeye "Yönetici Seç" seçeneği ekleyin.

[!code-html[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a>Eşzamanlılık çakışmalarını test edin

Uygulamayı çalıştırın ve Bölümler Dizini sayfasına gidin. İngilizce bölümü için **Edit** köprüünü sağ tıklatın ve **yeni sekmede Aç'ı**seçin, ardından İngilizce bölümü için **Edit** köprüünü tıklatın. İki tarayıcı sekmesi artık aynı bilgileri görüntüler.

İlk tarayıcı sekmesinde bir alanı değiştirin ve **Kaydet'i**tıklatın.

![Bölüm Değiştir'den sonra sayfa 1'i edit](concurrency/_static/edit-after-change-1.png)

Tarayıcı, değiştirilen değeri içeren Dizin sayfasını gösterir.

İkinci tarayıcı sekmesinde bir alanı değiştirin.

![Bölüm Değiştir'den sonra sayfa 2'yi edit](concurrency/_static/edit-after-change-2.png)

**Kaydet**'e tıklayın. Bir hata iletisi görürsünüz:

![Bölüm Sayfa Yıkla hata iletisi](concurrency/_static/edit-error.png)

Yeniden **Kaydet'i** tıklatın. İkinci tarayıcı sekmesine girdiğiniz değer kaydedilir. Dizin sayfası görüntülendiğinde kaydedilen değerleri görürsünüz.

## <a name="update-the-delete-page"></a>Sil sayfasını güncelleştir

Sil sayfası için Varlık Çerçevesi, bölümü benzer bir şekilde düzenleyen başka birinin neden olduğu eşzamanlılık çakışmalarını algılar. HttpGet `Delete` yöntemi onay görünümünü görüntülediğinde, görünüm `RowVersion` gizli bir alandaki özgün değeri içerir. Bu değer daha sonra kullanıcı `Delete` silme onayladığı zaman çağrılan HttpPost yöntemi için kullanılabilir. Varlık Çerçevesi SQL DELETE komutunu oluşturduğunda, özgün `RowVersion` değeri olan bir WHERE yan tümcesi içerir. Komut etkilenen sıfır satırla sonuçlanırsa (yani satır Sil onay sayfası görüntülendikten sonra değiştirildi), eşzamanlılık özel durumu atılır ve Onay sayfasını bir hata iletisiyle yeniden görüntülemek için httpget `Delete` yöntemi gerçek olarak ayarlanmış bir hata bayrağıyla çağrılır. Satır başka bir kullanıcı tarafından silindiğinden sıfır satır etkilenmiş olabilir, bu nedenle bu durumda hata iletisi görüntülenmez.

### <a name="update-the-delete-methods-in-the-departments-controller"></a>Departmanlar denetleyicisinde Silme yöntemlerini güncelleştirme

*DepartmentsController.cs,* HttpGet `Delete` yöntemini aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

Yöntem, sayfanın eşzamanlılık hatasından sonra yeniden görüntülenip görüntülenmediğini gösteren isteğe bağlı bir parametre kabul eder. Bu bayrak doğruysa ve belirtilen departman artık yoksa, başka bir kullanıcı tarafından silindi. Bu durumda, kod Dizin sayfasına yönlendirir.  Bu bayrak doğruysa ve Bölüm varsa, başka bir kullanıcı tarafından değiştirildi. Bu durumda, kod kullanarak `ViewData`görünüme bir hata iletisi gönderir.

HttpPost `Delete` yöntemindeki kodu (adlandırılmış) `DeleteConfirmed`aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

Yeni değiştirdiğiniz iskele kodunda, bu yöntem yalnızca bir kayıt kimliği kabul etmiştir:

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

Bu parametreyi model bağlayıcısı tarafından oluşturulan bir Departman varlık örneği olarak değiştirdiniz. Bu, EF'nin kayıt anahtarına ek olarak RowVersion özellik değerine erişmesini sağlar.

```csharp
public async Task<IActionResult> Delete(Department department)
```

Ayrıca eylem yöntemi adını da `DeleteConfirmed` `Delete`' dan ' a değiştirdiniz İskele kodu, HttpPost yöntemine benzersiz bir imza vermek için bu adı `DeleteConfirmed` kullandı. (CLR farklı yöntem parametrelerine sahip olmak için aşırı yüklenen yöntemler gerektirir.) Artık imzalar benzersiz olduğuna göre, MVC kuralına bağlı kalabilir ve HttpPost ve HttpGet silme yöntemleri için aynı adı kullanabilirsiniz.

Bölüm zaten silinmişse, `AnyAsync` yöntem yanlış döndürür ve uygulama dizin yöntemine geri döner.

Eşzamanlılık hatası yakalanırsa, kod Sil onay sayfasını yeniden görüntüler ve eşzamanlılık hatası iletisi görüntülemesi gerektiğini belirten bir bayrak sağlar.

### <a name="update-the-delete-view"></a>Sil görünümünü güncelleştir

*Görünümler/Bölümler/Delete.cshtml'de,* İskele kodu, DepartmentID ve RowVersion özellikleri için bir hata iletisi alanı ve gizli alanlar ekleyen aşağıdaki kodla değiştirin. Değişiklikler vurgulanır.

[!code-html[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

Bu, aşağıdaki değişiklikleri yapar:

* Başlıklar arasında `h3` bir `h2` hata iletisi ekler.

* Yönetici **alanında** FirstMidName'i FullName ile değiştirir.

* RowVersion alanını kaldırır.

* `RowVersion` Özellik için gizli bir alan ekler.

Uygulamayı çalıştırın ve Bölümler Dizini sayfasına gidin. İngilizce bölümü için **Sil** köprüünü sağ tıklatın ve **yeni sekmede Aç'ı**seçin, ardından ilk sekmede İngilizce bölümü için **Düzenle** köprübağlantısını tıklatın.

İlk pencerede, değerlerden birini değiştirin ve **Kaydet'i**tıklatın:

![Silmeden önce değişiklikten sonra Bölüm Düzenle sayfası](concurrency/_static/edit-after-change-for-delete.png)

İkinci sekmede **Sil'i**tıklatın. Eşzamanlılık hata iletisini görürsünüz ve Departman değerleri veritabanında şu anda bulunanlarla yenilenir.

![Bölüm Eşzamanlılık hatası ile onay sayfasını silme](concurrency/_static/delete-error.png)

Yeniden **Sil'i** tıklattığınızda, bölümün silindiğini gösteren Dizin sayfasına yönlendirilirsiniz.

## <a name="update-details-and-create-views"></a>Ayrıntıları Güncelleştir ve Görünüm Oluştur

Ayrıntılar'daki iskele kodunu isteğe bağlı olarak temizleyebilir ve görünüm oluşturabilirsiniz.

RowVersion sütununa silmek ve Yöneticinin tam adını göstermek için *Görünümler/Bölümler/Details.cshtml'deki* kodu değiştirin.

[!code-html[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

Açılan listeye Bir Seç seçeneği eklemek için *Görünümler/Bölümler/Create.cshtml'deki* kodu değiştirin.

[!code-html[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a>Kodu alma

[Tamamlanan uygulamayı karşıdan yükleyin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Ek kaynaklar

 EF Core'da eşzamanlılık nasıl işleyeceğiniz hakkında daha fazla bilgi için [Eşzamanlılık çakışmaları'na](/ef/core/saving/concurrency)bakın.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Eşzamanlılık çakışmaları hakkında bilgi edinilmiş
> * İzleme özelliği eklendi
> * Oluşturulan Bölümler denetleyicisi ve görünümleri
> * Güncelleştirilmiş Dizin görünümü
> * Güncelleştirilmiş Edit yöntemleri
> * Güncelleştirilmiş Edit görünümü
> * Test edilmiş eşzamanlılık çakışmaları
> * Sil sayfasını güncelleştir
> * Güncelleştirilmiş Ayrıntılar ve Görünümler Oluştur

Eğitmen ve Öğrenci kuruluşları için hiyerarşi başına tablo kalıbını nasıl uygulayacağınızı öğrenmek için bir sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Sonraki: Tablo başına hiyerarşi kalıtım uygulama](inheritance.md)
