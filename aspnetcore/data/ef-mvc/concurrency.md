---
title: "Öğretici: EF Core eşzamanlılık-ASP.NET MVC 'yi Işleme"
description: Bu öğreticide, birden fazla kullanıcı aynı anda aynı varlığı güncelleştirilişinde çakışmaların nasıl işleneceği gösterilmektedir.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/concurrency
ms.openlocfilehash: f97d551348ca31cc35ab5b04493ea702311a069e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88012968"
---
# <a name="tutorial-handle-concurrency---aspnet-mvc-with-ef-core"></a>Öğretici: EF Core eşzamanlılık-ASP.NET MVC 'yi Işleme

Önceki öğreticilerde, verileri güncelleştirme hakkında daha fazla öğrendiniz. Bu öğreticide, birden fazla kullanıcı aynı anda aynı varlığı güncelleştirilişinde çakışmaların nasıl işleneceği gösterilmektedir.

Departman varlığıyla çalışan ve eşzamanlılık hatalarını işleyecek Web sayfaları oluşturacaksınız. Aşağıdaki çizimler, bir eşzamanlılık çakışması oluşursa görüntülenen bazı iletiler dahil olmak üzere, düzenleme ve silme sayfalarını gösterir.

![Bölüm düzenleme sayfası](concurrency/_static/edit-error.png)

![Departmanı silme sayfası](concurrency/_static/delete-error.png)

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Eşzamanlılık çakışmaları hakkında bilgi edinin
> * İzleme özelliği Ekle
> * Departmanlar denetleyicisi ve görünümleri oluşturma
> * Dizin görünümünü Güncelleştir
> * Düzenleme yöntemlerini Güncelleştir
> * Güncelleştirme düzenleme görünümü
> * Eşzamanlılık çakışmalarını test et
> * Silme sayfasını Güncelleştir
> * Güncelleştirme ayrıntıları ve görünüm oluşturma

## <a name="prerequisites"></a>Önkoşullar

* [İlgili verileri güncelleştirme](update-related-data.md)

## <a name="concurrency-conflicts"></a>Eşzamanlılık çakışmaları

Bir Kullanıcı bir varlığın verilerini düzenlemek için bir varlık verileri görüntülediğinde bir eşzamanlılık çakışması oluşur ve sonra, ilk kullanıcının değişikliği veritabanına yazılmadan önce diğer Kullanıcı aynı varlığın verilerini günceller. Bu tür çakışmaların algılanmasını etkinleştirmezseniz, veritabanını güncelleştirme son olarak diğer kullanıcının değişikliklerinin üzerine yazar. Birçok uygulamada, bu risk kabul edilebilir: birkaç Kullanıcı veya birkaç güncelleştirme varsa veya bazı değişikliklerin üzerine yazılırsa gerçekten önemli değilse, eşzamanlılık için programlama maliyeti avantajdan yararlanabilir. Bu durumda, uygulamayı eşzamanlılık çakışmalarını işleyecek şekilde yapılandırmanız gerekmez.

### <a name="pessimistic-concurrency-locking"></a>Kötümser eşzamanlılık (kilitleme)

Uygulamanızın eşzamanlılık senaryolarında yanlışlıkla veri kaybını önlemesi gerekiyorsa, bunu yapmanın bir yolu veritabanı kilitlerini kullanmaktır. Bu, Kötümser eşzamanlılık olarak adlandırılır. Örneğin, bir veritabanından bir satırı okuyabilmeniz için, salt okunurdur veya güncelleştirme erişimi için bir kilit isteyin. Bir satırı güncelleştirme erişimi için kilitlerseniz, başka hiçbir kullanıcının satırı değiştirme sürecinde olan verilerin bir kopyasını alması için salt okunurdur veya güncelleştirme erişimi için bu satırı kilitlemesine izin verilmez. Bir satırı salt okuma erişimi için kilitlerseniz, diğerleri dosyayı salt okuma erişimi için de kilitleyip güncelleştirme için de kilitleyebilirler.

Kilitleri yönetmek dezavantajlara sahiptir. Program, karmaşık olabilir. Önemli veritabanı yönetim kaynakları gerektirir ve bir uygulamanın kullanıcı sayısı arttıkça performans sorunlarına neden olabilir. Bu nedenlerden dolayı, tüm veritabanı yönetim sistemleri Kötümser eşzamanlılık 'yi desteklemez. Entity Framework Core, BT için yerleşik destek sağlamaz ve bu öğretici bunu nasıl uygulayacağınızı göstermez.

### <a name="optimistic-concurrency"></a>İyimser Eşzamanlılık

Kötümser eşzamanlılık yerine iyimser eşzamanlılık yapılır. İyimser eşzamanlılık, eşzamanlılık çakışmalarının gerçekleşmesine ve sonra uygun şekilde yeniden davranmasını sağlar. Örneğin, Gamze departman düzenleme sayfasını ziyaret ettiğinde, Ingilizce departmanı $350.000,00 olan bütçe tutarını $0,00 olarak değiştirir.

![Bütçeyi 0 olarak değiştirme](concurrency/_static/change-budget.png)

Kemal, **Kaydet**' i tıklamadan önce, John aynı sayfayı ziyaret ettiğinde başlangıç tarihi alanını 9/1/2007 ' den 9/1/2013 ' e değiştirir.

![Başlangıç tarihini 2013 olarak değiştirme](concurrency/_static/change-date.png)

Gamze önce **Kaydet** ' i tıklatır ve tarayıcı dizin sayfasına döndüğünde değişikliği görür.

![Bütçe sıfıra değişti](concurrency/_static/budget-zero.png)

Ardından John, hala $350.000,00 bütçesini gösteren bir düzenleme sayfasında **Kaydet** ' e tıklamakta. Sonraki durum eşzamanlılık çakışmalarını nasıl işleydiğinize göre belirlenir.

Bazı seçenekler şunlardır:

* Bir kullanıcının hangi özelliği değiştirdiği ve yalnızca ilgili sütunları veritabanında güncelleştirdiğinden haberdar olabilirsiniz.

     Örnek senaryoda, iki kullanıcı tarafından farklı özellikler güncelleştirildiğinden hiçbir veri kaybolmaz. Ingilizce bölüme bir dahaki sefer ilk kez gözattığında, hem gamze 'nin hem de John 'un değişikliklerini görür; başlangıç tarihi 9/1/2013 ve sıfır dolar bir bütçe olur. Bu güncelleştirme yöntemi, veri kaybına neden olabilecek çakışmaların sayısını azaltabilir, ancak bir varlığın aynı özelliğinde rekabet değişiklikleri yapılırsa veri kaybını önleyebilir. Entity Framework bu şekilde çalışıp çalışmadığını, güncelleştirme kodunuzu nasıl uygulayadığınıza bağlıdır. Genellikle bir Web uygulamasında pratik değildir, çünkü bir varlığın tüm özgün özellik değerlerini ve yeni değerleri izlemek için büyük miktarlarda durum tutmanızı gerektirebilir. Büyük miktarlarda durum bulundurma, uygulama performansını etkileyebilir çünkü sunucu kaynakları gerektirir ya da Web sayfasının kendisine (örneğin, gizli alanlarda) veya bir içinde yer almalıdır cookie .

* John 'un değişikliğini kemal 'in değişikliğini geçersiz kılabilirsiniz.

     Ingilizce bölüme bir dahaki sefer gözattığında, 9/1/2013 ve geri yüklenen $350.000,00 değerini görür. Bu, *Istemci WINS* veya *son WINS* senaryosu olarak adlandırılır. (İstemciden gelen tüm değerler veri deposunda yer alacak şekilde önceliklidir.) Bu bölümün giriş bölümünde belirtildiği gibi, eşzamanlılık işleme için herhangi bir kodlama yapmazsanız, bu otomatik olarak gerçekleşir.

* John 'un değişikliğini veritabanında güncelleştirilmesini engelleyebilirsiniz.

     Genellikle bir hata iletisi görüntüler, verilerin geçerli durumunu gösterir ve yine de bunu yapmak istiyorsa, yaptığı değişiklikleri yeniden uygular. Buna *Mağaza WINS* senaryosu denir. (Veri deposu değerleri, istemci tarafından gönderilen değerlere göre önceliklidir.) Bu öğreticide mağaza WINS senaryosunu uygulayacaksınız. Bu yöntem, bir kullanıcının neler olduğunu bildirmeden önce hiçbir değişikliğin üzerine yazılmamasını sağlar.

### <a name="detecting-concurrency-conflicts"></a>Eşzamanlılık çakışmalarını algılama

`DbConcurrencyException`Entity Framework oluşturduğu özel durumları işleyerek çakışmaları çözebilirsiniz. Bu özel durumların ne zaman throw hakkında bilgi edinmek için Entity Framework çakışmaları algılayabilmelidir. Bu nedenle, veritabanını ve veri modelini uygun şekilde yapılandırmanız gerekir. Çakışma algılamayı etkinleştirmeye yönelik bazı seçenekler şunlardır:

* Veritabanı tablosunda, bir satırın ne zaman değiştirildiğini belirlemede kullanılabilecek bir izleme sütunu ekleyin. Daha sonra Entity Framework SQL Update veya delete komutlarının WHERE yan tümcesinde bu sütunu içerecek şekilde yapılandırabilirsiniz.

     İzleme sütununun veri türü genellikle olur `rowversion` . `rowversion`Değer, satır her güncelleştirildiği zaman artılan sıralı bir sayıdır. Bir Update veya delete komutunda WHERE yan tümcesi, izleme sütununun (orijinal satır sürümü) orijinal değerini içerir. Güncelleştirilmekte olan satır başka bir kullanıcı tarafından değiştirilmişse, `rowversion` sütundaki değer özgün değerden farklıdır, bu nedenle Update veya DELETE deyimi WHERE yan tümcesi nedeniyle güncelleştirilecek satırı bulamaz. Entity Framework, Update veya delete komutuyla hiçbir satır güncelleştirilmediğini bulduğunda (yani, etkilenen satır sayısı sıfır olduğunda), bunu bir eşzamanlılık çakışması olarak yorumlar.

* Entity Framework, Update ve DELETE komutlarının WHERE yan tümcesindeki tablodaki her sütunun özgün değerlerini içerecek şekilde yapılandırın.

     İlk seçenekte olduğu gibi, satırdaki herhangi bir şey satırın ilk okuduğundan beri değiştiyse WHERE yan tümcesi güncelleştirilecek bir satır döndürmez, bu da Entity Framework eşzamanlılık çakışması olarak yorumlar. Birçok sütunu olan veritabanı tablolarında, bu yaklaşım çok büyük WHERE yan tümceleriyle sonuçlanabilir ve büyük miktarlarda durum bulundurmasını gerektirebilir. Daha önce belirtildiği gibi, büyük miktarlarda durumu korumak uygulama performansını etkileyebilir. Bu nedenle bu yaklaşım genellikle önerilmez ve bu öğreticide kullanılan yöntem değildir.

     Bu yaklaşımı eşzamanlılık 'e uygulamak istiyorsanız, kendisine özniteliği ekleyerek eşzamanlılık izlemek istediğiniz varlıktaki tüm birincil anahtar olmayan Özellikleri işaretlemeniz gerekir `ConcurrencyCheck` . Bu değişiklik Entity Framework, tüm sütunları Update ve DELETE deyimlerinin SQL WHERE yan tümcesinde içermesini sağlar.

Bu öğreticinin geri kalanında, `rowversion` Departman varlığına bir izleme özelliği ekleyecek, denetleyici ve görünümler oluşturacak ve her şeyin doğru şekilde çalıştığını doğrulamak için test edeceksiniz.

## <a name="add-a-tracking-property"></a>İzleme özelliği Ekle

*Modeller/departman. cs*' de, rowversion adlı bir izleme özelliği ekleyin:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

`Timestamp`Özniteliği, bu sütunun veritabanına gönderilen WHERE yan tümcesine ve DELETE komutlarına dahil edileceğini belirtir. Özniteliği, `Timestamp` önceki SQL Server sürümleri SQL `timestamp` veri türü tarafından değiştirilmeden önce kullanıldığından, bu öznitelik çağrılır `rowversion` . İçin .NET türü `rowversion` bir bayt dizisidir.

Fluent API kullanmayı tercih ediyorsanız, `IsConcurrencyToken` Aşağıdaki örnekte gösterildiği gibi, izleme özelliğini belirtmek için yöntemini ( *Data/SchoolContext. cs*) kullanabilirsiniz:

```csharp
modelBuilder.Entity<Department>()
    .Property(p => p.RowVersion).IsConcurrencyToken();
```

Bir özellik ekleyerek, veritabanı modelini değiştirdiğiniz için başka bir geçiş yapmanız gerekir.

Değişikliklerinizi kaydedin ve projeyi derleyin ve ardından komut penceresine aşağıdaki komutları girin:

```dotnetcli
dotnet ef migrations add RowVersion
```

```dotnetcli
dotnet ef database update
```

## <a name="create-departments-controller-and-views"></a>Departmanlar denetleyicisi ve görünümleri oluşturma

Daha önce öğrenciler, Kurslar ve Eğitmenler için yaptığınız gibi bir departman denetleyicisini ve görünümlerini derden katlayın.

![Yapı iskelesi departmanı](concurrency/_static/add-departments-controller.png)

*DepartmentsController.cs* dosyasında, "FirstMidName" sözcüğünün dört yinelemesini "FullName" olarak değiştirin, böylece Departman Yöneticisi açılan listeleri yalnızca soyadı yerine eğitmenin tam adını içerecektir.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_Dropdown)]

## <a name="update-index-view"></a>Dizin görünümünü Güncelleştir

Yapı iskelesi altyapısı dizin görünümünde bir ROWVERSION sütunu oluşturdu, ancak bu alan gösterilmemelidir.

*Views/departmanlar/Index. cshtml* içindeki kodu aşağıdaki kodla değiştirin.

[!code-cshtml[](intro/samples/cu/Views/Departments/Index.cshtml?highlight=4,7,44)]

Bu, başlığı "departmanlar" olarak değiştirir, RowVersion sütununu siler ve yöneticinin adı yerine tam adı gösterir.

## <a name="update-edit-methods"></a>Düzenleme yöntemlerini Güncelleştir

Hem HttpGet yönteminde hem de `Edit` `Details` yönteminde, öğesini ekleyin `AsNoTracking` . HttpGet `Edit` yönteminde, yönetici için Eager yüklemesi ekleyin.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EagerLoading)]

HttpPost yöntemi için mevcut kodu `Edit` şu kodla değiştirin:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_EditPost)]

Kod, güncellenen departmanı okumaya çalışırken başlar. `FirstOrDefaultAsync`Yöntem null döndürürse, departman başka bir kullanıcı tarafından silindi. Bu durumda, kod, düzenleme sayfasının bir hata iletisiyle yeniden görüntülenebilmesi için bir departman varlığı oluşturmak üzere postalanan form değerlerini kullanır. Alternatif olarak, departman alanlarını yeniden görüntülemeden yalnızca bir hata iletisi görüntülediğinizde, departman varlığını yeniden oluşturmanız gerekmez.

Görünüm özgün `RowVersion` değeri gizli bir alanda depolar ve bu yöntem parametresindeki değeri alır `rowVersion` . ' İ çağırmadan önce `SaveChanges` , söz konusu özgün `RowVersion` özellik değerini `OriginalValues` varlığa yönelik koleksiyonuna koymanız gerekir.

```csharp
_context.Entry(departmentToUpdate).Property("RowVersion").OriginalValue = rowVersion;
```

Entity Framework bir SQL UPDATE komutu oluşturduğunda, bu komut özgün değere sahip bir satırı aramak için bir WHERE yan tümcesi içerecektir `RowVersion` . UPDATE komutundan hiçbir satır etkilenmiyorsa (özgün değere sahip hiçbir satır yoksa `RowVersion` ) Entity Framework bir `DbUpdateConcurrencyException` özel durum oluşturur.

Bu özel durum için catch bloğundaki kod, `Entries` özel durum nesnesindeki özelliğinden güncelleştirilmiş değerlere sahip etkilenen departman varlığını alır.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=164)]

`Entries`Koleksiyonda yalnızca bir `EntityEntry` nesne olacak.  Kullanıcı tarafından girilen yeni değerleri ve geçerli veritabanı değerlerini almak için bu nesneyi kullanabilirsiniz.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=165-166)]

Kod, kullanıcının düzenleme sayfasına girdikten farklı veritabanı değerleri olan her bir sütun için özel bir hata iletisi ekler (yalnızca bir alan, kısaltma için burada gösterilir).

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=174-178)]

Son olarak, kod `RowVersion` değerini `departmentToUpdate` veritabanından alınan yeni değere ayarlar. Bu yeni `RowVersion` değer, düzenleme sayfası yeniden görüntülenirken gizli alanda saklanır ve Kullanıcı **Kaydet**' i tıkladığında, düzenleme sayfasının yeniden görüntülenmesinden bu yana yalnızca gerçekleşen eşzamanlılık hataları yakalanacaktır.

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?range=199-200)]

`ModelState.Remove`Eski değere sahip olduğundan deyimin olması gerekir `ModelState` `RowVersion` . Görünümde, `ModelState` bir alanın değeri, her ikisi de varsa Model Özellik değerlerinden önceliklidir.

## <a name="update-edit-view"></a>Güncelleştirme düzenleme görünümü

*Görünümler/departmanlar/Düzenle. cshtml*'de aşağıdaki değişiklikleri yapın:

* Özellik değerini kaydetmek için `RowVersion` , özelliği için gizli alandan hemen sonra bir gizli alan ekleyin `DepartmentID` .

* Açılan listeye "Yönetici Seç" seçeneği ekleyin.

[!code-cshtml[](intro/samples/cu/Views/Departments/Edit.cshtml?highlight=16,34-36)]

## <a name="test-concurrency-conflicts"></a>Eşzamanlılık çakışmalarını test et

Uygulamayı çalıştırın ve departmanlar dizini sayfasına gidin. Ingilizce departman için **düzenleme** köprüsüne sağ tıklayın ve **Yeni sekmesinde aç**' ı seçin ve ardından İngilizce bölümünün **düzenleme** Köprüsü ' ne tıklayın. İki tarayıcı sekmesi artık aynı bilgileri görüntüler.

İlk tarayıcı sekmesinde bir alanı değiştirin ve **Kaydet**' e tıklayın.

![Bölüm Düzenle sayfa 1 değişiklikten sonra](concurrency/_static/edit-after-change-1.png)

Tarayıcı, değiştirilen değeri olan dizin sayfasını gösterir.

İkinci tarayıcı sekmesinden bir alanı değiştirin.

![Değişiklik sonrasında bölüm düzenleme sayfası 2](concurrency/_static/edit-after-change-2.png)

**Kaydet**’e tıklayın. Bir hata iletisi görürsünüz:

![Bölüm düzenleme sayfası hata iletisi](concurrency/_static/edit-error.png)

Yeniden **Kaydet** ' e tıklayın. İkinci tarayıcı sekmesine girdiğiniz değer kaydedilir. Dizin sayfası göründüğünde kaydedilen değerleri görürsünüz.

## <a name="update-the-delete-page"></a>Silme sayfasını Güncelleştir

Silme sayfası için Entity Framework, başka birinin departmanı benzer bir şekilde düzenlemesinden kaynaklanan eşzamanlılık çakışmalarını algılar. HttpGet `Delete` yönteminde onay görünümü görüntülendiğinde görünüm, `RowVersion` gizli bir alanda orijinal değeri içerir. Bu değer daha sonra `Delete` Kullanıcı silmeyi onayladığında çağrılan HttpPost yöntemi için kullanılabilir. Entity Framework, SQL DELETE komutunu oluşturduğunda, özgün değeri olan bir WHERE yan tümcesi içerir `RowVersion` . Komut, sıfır satır etkilenirse (satır silme onayı sayfası görüntülendikten sonra değiştirildiğinde), bir eşzamanlılık özel durumu oluşturulur ve `Delete` onay sayfasını bir hata iletisiyle yeniden görüntülemek Için HttpGet yöntemi bir hata bayrağı true olarak çağırılır. Satır başka bir kullanıcı tarafından silindiğinden, bu durumda herhangi bir hata iletisi görüntülenmediğinden sıfır satırların etkilenmesi de mümkündür.

### <a name="update-the-delete-methods-in-the-departments-controller"></a>Departmanlar denetleyicisindeki silme yöntemlerini güncelleştirme

*DepartmentsController.cs*' de, HttpGet `Delete` yöntemini aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeleteGet&highlight=1,10,14-17,21-29)]

Yöntemi, sayfanın bir eşzamanlılık hatasından sonra yeniden görüntülenip görüntülenmeyeceğini belirten isteğe bağlı bir parametresini kabul eder. Bu bayrak true ise ve belirtilen departman artık mevcut değilse, başka bir kullanıcı tarafından silindi. Bu durumda, kod dizin sayfasına yeniden yönlendirir.  Bu bayrak true ise ve departman varsa, başka bir kullanıcı tarafından değiştirilmiştir. Bu durumda, kod kullanarak görünüme bir hata mesajı gönderir `ViewData` .

HttpPost `Delete` yöntemindeki (adlı `DeleteConfirmed` ) kodu şu kodla değiştirin:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_DeletePost&highlight=1,3,5-8,11-18)]

Az önce değiştirdiğiniz scafkatlanmış kodda, bu yöntem yalnızca bir kayıt KIMLIĞI kabul etti:

```csharp
public async Task<IActionResult> DeleteConfirmed(int id)
```

Bu parametreyi model Ciltçi tarafından oluşturulan bir departman varlığı örneğine değiştirdiniz. Bu, kayıt anahtarına ek olarak ROWVERSION özellik değerine EF erişimi verir.

```csharp
public async Task<IActionResult> Delete(Department department)
```

Ayrıca, eylem yöntemi adını `DeleteConfirmed` olarak olarak değiştirdiniz `Delete` . Yapı iskelesi kodu, `DeleteConfirmed` HttpPost yöntemine benzersiz bir imza vermek için adı kullandı. (CLR aşırı yüklenmiş yöntemlerin farklı yöntem parametrelerine sahip olmasını gerektirir.) İmzalar benzersiz olduğuna göre, MVC kuralını seçebilir ve HttpPost ve HttpGet silme yöntemleri için aynı adı kullanabilirsiniz.

Departman zaten silinirse, `AnyAsync` Yöntem false döndürür ve uygulama yalnızca dizin yöntemine geri döner.

Bir eşzamanlılık hatası yakalanmışsa, kod silme onayı sayfasını yeniden görüntüler ve bir eşzamanlılık hata mesajı görüntülemesi gerektiğini belirten bir bayrak sağlar.

### <a name="update-the-delete-view"></a>Silme görünümünü Güncelleştir

*Views/departmanlar/delete. cshtml*içinde, scafkatkli kodunu, DepartmentID ve rowversion özellikleri için bir hata iletisi alanı ve gizli alanları ekleyen aşağıdaki kodla değiştirin. Değişiklikler vurgulanır.

[!code-cshtml[](intro/samples/cu/Views/Departments/Delete.cshtml?highlight=9,38,44,45,48)]

Bu, aşağıdaki değişiklikleri yapar:

* Ve başlıkları arasına bir hata mesajı `h2` ekler `h3` .

* FirstMidName öğesini, **yönetici** alanındaki FullName ile değiştirir.

* RowVersion alanını kaldırır.

* Özelliği için gizli bir alan ekler `RowVersion` .

Uygulamayı çalıştırın ve departmanlar dizini sayfasına gidin. Ingilizce departman için **Sil** köprüsünü sağ tıklayın ve **Yeni sekmede aç**' ı seçin ve ardından ilk sekmede İngilizce departman için **düzenleme** Köprüsü ' ne tıklayın.

İlk pencerede, değerlerden birini değiştirin ve **Kaydet**' e tıklayın:

![Silmeden önce değişiklikten sonra departman düzenleme sayfası](concurrency/_static/edit-after-change-for-delete.png)

İkinci sekmede **Sil**' e tıklayın. Eşzamanlılık hata iletisini görürsünüz ve departman değerleri şu anda veritabanında olan ile yenilenir.

![Eşzamanlılık hatası olan departman silme onayı sayfası](concurrency/_static/delete-error.png)

Yeniden **Sil** ' e tıklarsanız, departmanın silindiğini gösteren dizin sayfasına yönlendirilirsiniz.

## <a name="update-details-and-create-views"></a>Güncelleştirme ayrıntıları ve görünüm oluşturma

İsteğe bağlı olarak ayrıntılarda bulunan kodu temizleyebilir ve görünümler oluşturabilirsiniz.

RowVersion sütununu silmek ve yöneticinin tam adını göstermek için *views/departmanlar/details. cshtml* içindeki kodu değiştirin.

[!code-cshtml[](intro/samples/cu/Views/Departments/Details.cshtml?highlight=35)]

Açılan listeye bir SELECT seçeneği eklemek için *views/departmanlar/Create. cshtml* içindeki kodu değiştirin.

[!code-cshtml[](intro/samples/cu/Views/Departments/Create.cshtml?highlight=32-34)]

## <a name="get-the-code"></a>Kodu alma

[Tamamlanmış uygulamayı indirin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Ek kaynaklar

 EF Core eşzamanlılık işleme hakkında daha fazla bilgi için bkz. [eşzamanlılık çakışmaları](/ef/core/saving/concurrency).

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Eşzamanlılık çakışmaları hakkında öğrenildi
> * İzleme özelliği eklendi
> * Oluşturulan departmanlar denetleyicisi ve görünümleri
> * Güncelleştirilmiş dizin görünümü
> * Güncelleştirilmiş düzenleme yöntemleri
> * Güncelleştirilmiş düzenleme görünümü
> * Test edilen eşzamanlılık çakışmaları
> * Silme sayfası güncelleştirildi
> * Güncelleştirilmiş Ayrıntılar ve görünüm oluşturma

Eğitmen ve öğrenci varlıkları için tablo başına devralma devralmayı nasıl uygulayacağınızı öğrenmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Sonraki: tablo-hiyerarşi devralmayı Uygula](inheritance.md)
