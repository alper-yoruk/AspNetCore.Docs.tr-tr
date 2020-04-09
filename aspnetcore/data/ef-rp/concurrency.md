---
title: ASP.NET Core'da EF Çekirdekli Jilet Sayfaları - Eşzamanlılık - 8/8
author: rick-anderson
description: Bu öğretici, birden çok kullanıcı aynı varlığı aynı anda güncelleştirdiğinde çakışmaları nasıl işleyeceğini gösterir.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
uid: data/ef-rp/concurrency
ms.openlocfilehash: c4d43f26ba80e7922c3cbd37d9a5f8e1561b11ad
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656914"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---concurrency---8-of-8"></a>ASP.NET Core'da EF Çekirdekli Jilet Sayfaları - Eşzamanlılık - 8/8

Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), ve [Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Bu öğretici, birden çok kullanıcı bir varlığı aynı anda güncelleştirdiğinde (aynı anda) çakışmaları nasıl işleyeceğini gösterir.

## <a name="concurrency-conflicts"></a>Eşzamanlılık çakışmaları

Bir eşzamanlılık çakışması oluşur:

* Kullanıcı, bir varlığın edin'le mecasayfasına doğru yol alar.
* Başka bir kullanıcı, ilk kullanıcı değişikliği veritabanına yazılmadan önce aynı varlığı güncelleştirir.

Eşzamanlılık algılaması etkinleştirilemezse, veritabanını en son güncelleren kişi diğer kullanıcının değişikliklerinin üzerine yazar. Bu risk kabul edilebilirse, eşzamanlılık için programlama maliyeti yarar ağır basabilir.

### <a name="pessimistic-concurrency-locking"></a>Kötümser eşzamanlılık (kilitleme)

Eşzamanlılık çakışmalarını önlemenin bir yolu veritabanı kilitlerini kullanmaktır. Buna kötümser eşzamanlılık denir. Uygulama, güncelleştirmeyi planladığı bir veritabanı satırını okumadan önce bir kilit ister. Güncelleştirme erişimi için bir satır kilitlendikten sonra, ilk kilit serbest bırakılıncaya kadar başka hiçbir kullanıcının satırı kilitlemesine izin verilmez.

Kilitleri yönetmenin dezavantajları vardır. Program karmaşık olabilir ve kullanıcı sayısı arttıkça performans sorunlarına neden olabilir. Entity Framework Core bunun için yerleşik destek sağlamaz ve bu öğretici bunu nasıl uygulayacağımı göstermez.

### <a name="optimistic-concurrency"></a>İyimser eşzamanlılık

İyimser eşzamanlılık eşzamanlılık eşzamanlılık çakışmaların gerçekleşmesine izin verir ve bunu yaptığında uygun şekilde tepki verir. Örneğin, Jane Bölüm'ün edit sayfasını ziyaret eder ve İngilizce bölümü bütçesini 350.000,00 TL'den 0,00 TL'ye değiştirir.

![Bütçeyi 0 olarak değiştirme](concurrency/_static/change-budget30.png)

Jane **Kaydet'i**tıklatmadan önce, John aynı sayfayı ziyaret eder ve Başlangıç Tarihi alanını 9/1/2007'den 9/1/2013'e değiştirir.

![Başlangıç tarihini 2013 olarak değiştirme](concurrency/_static/change-date30.png)

Jane önce **Kaydet'i** tıklatıyor ve tarayıcı Bütçe tutarı olarak Dizin sayfasını sıfırla gösterdiğinden, değişikliğinin etkili olduğunu görür.

John, 350.000,00 TL bütçeyi gösteren bir Edit sayfasında **Kaydet'i** tıklatıyor. Bundan sonra ne olacağı eşzamanlılık çakışmalarını nasıl ele aldığınıza göre belirlenir:

* Bir kullanıcının hangi özelliği değiştirdiğini izleyebilir ve yalnızca veritabanındaki ilgili sütunları güncelleştirebilirsiniz.

  Senaryoda, hiçbir veri kaybolmaz. Farklı özellikler iki kullanıcı tarafından güncelleştirildi. Bir dahaki sefere birisi İngilizce bölümüne göz atsa, hem Jane'in hem de John'un değişikliklerini görecek. Bu güncelleştirme yöntemi, veri kaybına neden olabilecek çakışma sayısını azaltabilir. Bu yaklaşımın bazı dezavantajları vardır:
 
  * Aynı özellikte rakip değişiklikler yapılırsa veri kaybını önleyemezsiniz.
  * Genellikle bir web uygulamasında pratik değildir. Getirilen tüm değerleri ve yeni değerleri izlemek için önemli bir durum korumayı gerektirir. Büyük miktarda durum bakımı uygulama performansını etkileyebilir.
  * Bir varlık üzerinde eşzamanlılık algılamaile karşılaştırıldığında uygulama karmaşıklığını artırabilir.

* John'un değişmesine izin verebilirsin Jane'in bozuk paralarını yazabilirsin.

  Birisi İngilizce bölümüne bir daha ki sefere göz atanın, 9/1/2013 ve getirilen 350.000,00 $ değerini göreceksiniz. Bu *yaklaşıma, Kazananların Sonuncusu* veya Son *Kazananlar* senaryosu olarak adlandırılır. (İstemcinin tüm değerleri veri deposunda bulunanlardan önceliklidir.) Eşzamanlılık işleme için herhangi bir kodlama yapmazsanız, İstemci Kazançları otomatik olarak gerçekleşir.

* John'un değişikliğinin veritabanında güncelleştirilen olmasını engelleyebilirsiniz. Genellikle, uygulama:

  * Bir hata iletisi görüntüleyin.
  * Verilerin geçerli durumunu gösterin.
  * Kullanıcının değişiklikleri yeniden uygulamasına izin verin.

  Buna Mağaza *Kazançları* senaryosu denir. (Veri depolama değerleri istemci tarafından gönderilen değerlerden önceliklidir.) Bu öğreticide Mağaza Kazançları senaryosunu uygularsınız. Bu yöntem, kullanıcı uyarılmadan hiçbir değişikliğin üzerine yazılmamasını sağlar.

## <a name="conflict-detection-in-ef-core"></a>EF Core'da çakışma algılama

EF Core `DbConcurrencyException` çakışmaları algıladığında özel durumlar atar. Veri modeli çakışma algılamayı etkinleştirmek için yapılandırılmalıdır. Çakışma algılamayı etkinleştirme seçenekleri şunlardır:

* EF Core'u, Güncelleştirme ve Sil komutlarının Nerede yan tümcesi'nde [eşzamanlılık belirteçleri](/ef/core/modeling/concurrency) olarak yapılandırılan sütunların özgün değerlerini içerecek şekilde yapılandırın.

  Çağrıldığında, `SaveChanges` Where yan tümcesi [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute) özniteliği ile açıklamalı tüm özelliklerin özgün değerlerini arar. Güncelleştirme deyimi, satır ilk okundundan beri eşzamanlılık belirteç özelliklerinden herhangi biri değiştirilirse güncelleştirilen bir satır bulamaz. EF Core bunu eşzamanlılık çatışması olarak yorumluyor. Çok sayıda sütunu olan veritabanı tabloları için bu yaklaşım, çok büyük Where yan tümcelerine neden olabilir ve büyük miktarda durum gerektirebilir. Bu nedenle bu yaklaşım genellikle önerilmez ve bu öğreticide kullanılan yöntem değildir.

* Veritabanı tablosunda, bir satırın ne zaman değiştirildiğini belirlemek için kullanılabilecek bir izleme sütunu ekleyin.

  BIR SQL Server veritabanında, izleme sütununun `rowversion`veri türü . Değer, `rowversion` satır her güncelleştiride artılan sıralı bir sayıdır. Bir Güncelleştirme veya Sil komutunda, Where yan tümcesi izleme sütununun (özgün satır sürüm numarası) özgün değerini içerir. Güncelleştirilen satır başka bir kullanıcı tarafından değiştirildiyse, `rowversion` sütundaki değer özgün değerden farklıdır. Bu durumda, Güncelleştirme veya Sil deyimi, Where yan tümcesi nedeniyle güncelleştirilemeyecek satırı bulamaz. EF Core, güncelleştirme veya silme komutundan hiçbir satır etkilenmediğinde eşzamanlılık özel durumu oluşturur.

## <a name="add-a-tracking-property"></a>İzleme özelliği ekleme

*Modeller/Department.cs*olarak, RowVersion adlı bir izleme özelliği ekleyin:

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

[Zaman damgası](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) özniteliği, sütunu eşzamanlı izleme sütunu olarak tanımlayan şeydir. Akıcı API izleme özelliğini belirtmek için alternatif bir yoldur:

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

BIR SQL Server veritabanı `[Timestamp]` için, bayt dizisi olarak tanımlanan bir varlık özelliğindeki öznitelik:

* Sütunun DELETE ve UPDATE WHERE yan tümcelerine eklenmesine neden olur.
* Veritabanındaki sütun türünü [satır sürümüne](/sql/t-sql/data-types/rowversion-transact-sql)ayarlar.

Veritabanı, satır her güncelleştiride artımlı bir sıralı satır sürüm numarası oluşturur. Bir `Update` veya `Delete` komutta, `Where` yan tümce getirilen satır sürüm değerini içerir. Güncelleştirilen satır, getirilen satırdan sonra değiştiyse:

* Geçerli satır sürüm değeri getirilen değerle eşleşmiyor.
* Yan tümce getirilen satır sürüm değerini `Update` aradığından, veya `Delete` komutları satır bulamaz. `Where`
* A `DbUpdateConcurrencyException` atılır.

Aşağıdaki kod, Bölüm adı güncelleştirildiğinde EF Core tarafından oluşturulan T-SQL'in bir bölümünü gösterir:

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

Önceki vurgulanan kod, `WHERE` 'yi `RowVersion`içeren yan tümceyi gösterir. Veritabanı `RowVersion` `RowVersion` parametreye eşit değilse (`@p2`), satır güncelleştirilmez.

Aşağıdaki vurgulanan kod, tam olarak bir satırın güncelleştirilen T-SQL'i gösterir:

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

[@@ROWCOUNT ](/sql/t-sql/functions/rowcount-transact-sql) son ifadeden etkilenen satır sayısını döndürür. Hiçbir satır güncelleştirmezse, EF Core `DbUpdateConcurrencyException`bir .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Bir SQLite veritabanı `[Timestamp]` için, bayt dizisi olarak tanımlanan bir varlık özelliğindeki öznitelik:

* Sütunun DELETE ve UPDATE WHERE yan tümcelerine eklenmesine neden olur.
* BLOB sütun türüne eşler.

Veritabanı, rowVersion sütununa, bir satır güncelleştirildiğinde yeni bir rasgele bayt dizisiyle güncelleştirilir. Bir `Update` veya `Delete` komutta, `Where` yan tümce RowVersion sütununun getirilen değerini içerir. Güncelleştirilen satır, getirilen satırdan sonra değiştiyse:

* Geçerli satır sürüm değeri getirilen değerle eşleşmiyor.
* Yan tümce özgün satır sürüm değerini `Update` aradığından, orkomut `Delete` satır bulamaz. `Where`
* A `DbUpdateConcurrencyException` atılır.

---

### <a name="update-the-database"></a>Veritabanını güncelleştirme

`RowVersion` Özellik ekleme, geçiş gerektiren veri modelini değiştirir.

Projeyi derleyin. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* PMC'de aşağıdaki komutu çalıştırın:

  ```powershell
  Add-Migration RowVersion
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Bir terminalde aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet ef migrations add RowVersion
  ```

---

Şu komut:

* *Geçişler/{zaman damgası}_RowVersion.cs* geçiş dosyasını oluşturur.
* *Geçişler/SchoolContextModelSnapshot.cs* dosyasını güncelleştirir. Güncelleştirme, `BuildModel` yönteme aşağıdaki vurgulanmış kodu ekler:

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* PMC'de aşağıdaki komutu çalıştırın:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Dosyayı `Migrations/<timestamp>_RowVersion.cs` açın ve vurgulanan kodu ekleyin:

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  Yukarıdaki kod:

  * Varolan satırları rasgele blob değerleriyle güncelleştirir.
  * RowVersion sütununa bir satır güncelleştirildiğinde rasgele bir blob değeriayarveritabanı tetikleyicileri ekler.

* Bir terminalde aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a>İskele Bölümü sayfaları

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Aşağıdaki istisnalar dışında [İskele Öğrenci sayfalarındaki](xref:data/ef-rp/intro#scaffold-student-pages) talimatları izleyin:

* *Sayfalar/Bölümler* klasörü oluşturun.  
* Model `Department` sınıfı için kullanın.
  * Yeni bir bağlam oluşturmak yerine varolan bağlam sınıfını kullanın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Sayfalar/Bölümler* klasörü oluşturun.

* Bölüm sayfalarını iskeleye getirmek için aşağıdaki komutu çalıştırın.

  **Windows'da:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  **Linux veya macOS'ta:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

Projeyi derleyin.

## <a name="update-the-index-page"></a>Dizin sayfasını güncelleştirme

İskele aracı Dizin `RowVersion` sayfası için bir sütun oluşturdu, ancak bu alan bir üretim uygulamasında görüntülenmez. Bu öğreticide, eşzamanlılık işlemenin `RowVersion` nasıl çalıştığını göstermeye yardımcı olmak için son bayt görüntülenir. Son bayt ın tek başına benzersiz olacağı garanti değildir.

*Sayfaları Güncelleştir\Bölümler\Index.cshtml* sayfası:

* Dizini Bölümlerle Değiştirin.
* Bayt dizisinin son baytını göstermek için içeren `RowVersion` kodu değiştirin.
* FirstMidName'i FullName ile değiştirin.

Aşağıdaki kod güncelleştirilmiş sayfayı gösterir:

[!code-html[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a>Sayfayı Güncelle modelini güncelleştir

*Sayfaları Güncelleştir\Bölümler\Edit.cshtml.cs* aşağıdaki kodla:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

`OnGet` [OriginalValue,](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) yöntemde `rowVersion` getirilirken varlığın değeriyle güncelleştirilir. EF Core, özgün `RowVersion` değeri içeren bir WHERE yan tümcesi içeren bir SQL UPDATE komutu oluşturur. GÜNCELLEME komutundan hiçbir satır etkilenmezse (hiçbir `RowVersion` satır `DbUpdateConcurrencyException` orijinal değere sahip değilse), bir özel durum atılır.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

Önceki vurgulanan kodda:

* `Department.RowVersion` Değer, edit sayfası için Al isteğinde ilk olarak getirilen varlıkta ne olduğudur. Değer, düzenlenecek varlığı `OnPost` görüntüleyen Razor sayfasındaki gizli bir alan tarafından yönteme sağlanır. Gizli alan değeri model `Department.RowVersion` bağlayıcısı tarafından kopyalanır.
* `OriginalValue`EF Core'un Where yan tümcesinde kullanacağı şeydir. Vurgulanan kod satırı yürütülmeden önce, `OriginalValue` bu yöntemde `FirstOrDefaultAsync` çağrıldığında veritabanında bulunan ve Düzenle sayfasında görüntülenenden farklı olabilecek değere sahiptir.
* Vurgulanan kod, EF Core'un `RowVersion` SQL UPDATE `Department` deyiminin Where yan tümcesi'nde görüntülenen varlığın özgün değerini kullandığından emin olmasını sağlar.

Eşzamanlılık hatası olduğunda, aşağıdaki vurgulanan kod istemci değerlerini (bu yönteme deftere nakledilen değerler) ve veritabanı değerlerini alır.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

Aşağıdaki kod, veritabanı değerlerine sahip her sütun için, deftere `OnPostAsync`nakledilenden farklı bir özel hata iletisi ekler:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

Aşağıdaki vurgulanan kod `RowVersion` veritabanından alınan yeni değerin değerini ayarlar. Kullanıcı **Kaydet'i**bir sonraki tıklatıldığında, yalnızca Edit sayfasının son ekranından oluşan eşzamanlılık hataları yakalanır.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

Eski `ModelState.Remove` `RowVersion` değere `ModelState` sahip olduğundan ifade gereklidir. Razor Page'de, `ModelState` bir alanın değeri, her ikisi de mevcut olduğunda model özellik değerlerinin üzerinde önceliklidir.

### <a name="update-the-razor-page"></a>Razor sayfasını güncelleştirin

*Sayfaları/Bölümleri/Edit.cshtml'i* aşağıdaki kodla güncelleştirin:

[!code-html[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

Yukarıdaki kod:

* Yönergeyi `page` `@page` `@page "{id:int}"`' den .
* Gizli satır sürümü ekler. `RowVersion`post back değeri bağlar böylece eklenmelidir.
* Hata ayıklama amacıyla `RowVersion` son baytını görüntüler.
* Güçlü `ViewData` bir şekilde yazılanla `InstructorNameSL`değiştirir.

### <a name="test-concurrency-conflicts-with-the-edit-page"></a>Eşpara birimi çakışmalarını Edit sayfasıyla test edin

İngilizce bölümünde Edit'in iki tarayıcı örneğini açın:

* Uygulamayı çalıştırın ve Bölümleri seçin.
* İngilizce bölümü için **Edit** köprüye sağ tıklayın ve **yeni sekmede Aç'ı**seçin.
* İlk sekmede, İngilizce bölümü için **Edit** köprübağlantısını tıklatın.

İki tarayıcı sekmesi aynı bilgileri görüntüler.

İlk tarayıcı sekmesinde adı değiştirin ve **Kaydet'i**tıklatın.

![Bölüm Değiştir'den sonra sayfa 1'i edit](concurrency/_static/edit-after-change-130.png)

Tarayıcı, değiştirilen değeri ve güncelleştirilmiş rowVersion göstergesiyle Dizin sayfasını gösterir. Güncelleştirilmiş satırSürüm göstergesine dikkat edin, diğer sekmedeki ikinci posta geri sinde görüntülenir.

İkinci tarayıcı sekmesinde farklı bir alanı değiştirin.

![Bölüm Değiştir'den sonra sayfa 2'yi edit](concurrency/_static/edit-after-change-230.png)

**Kaydet**'e tıklayın. Veritabanı değerleriyle eşleşmeyan tüm alanlar için hata iletileri görürsünüz:

![Bölüm Sayfa Yıkla hata iletisi](concurrency/_static/edit-error30.png)

Bu tarayıcı penceresi Ad alanını değiştirmek niyetinde değildi. Geçerli değeri (Diller) Ad alanına kopyalayın ve yapıştırın. Sekme dışarı. İstemci tarafı doğrulama hata iletisini kaldırır.

Yeniden **Kaydet'i** tıklatın. İkinci tarayıcı sekmesine girdiğiniz değer kaydedilir. Dizin sayfasında kaydedilen değerleri görürsünüz.

## <a name="update-the-delete-page"></a>Sil sayfasını güncelleştir

*Sayfaları/Bölümleri/Delete.cshtml.cs'leri* aşağıdaki kodla güncelleyin:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

Sil sayfası, varlık alındıktan sonra değiştiğinde eşzamanlılık çakışmalarını algılar. `Department.RowVersion`varlığın getirili satır sürümüdür. EF Core SQL DELETE komutunu oluşturduğunda, `RowVersion`'. SQL DELETE komutu etkilenen sıfır satırla sonuçlanırsa:

* `RowVersion` SQL DELETE komutu veritabanında `RowVersion` eşleşmez.
* Bir DbUpdateConcurrencyException özel durum atılır.
* `OnGetAsync`ile `concurrencyError`denir.

### <a name="update-the-delete-razor-page"></a>Razor'u sil sayfasını güncelleştir

*Sayfaları/Bölümleri/Delete.cshtml'i* aşağıdaki kodla güncelleştirin:

[!code-html[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

Önceki kod aşağıdaki değişiklikleri yapar:

* Yönergeyi `page` `@page` `@page "{id:int}"`' den .
* Bir hata iletisi ekler.
* Yönetici **alanında** FirstMidName'i FullName ile değiştirir.
* Son `RowVersion` bayt görüntülemek için değişiklikler.
* Gizli satır sürümü ekler. `RowVersion`postgit değeri bağlar geri eklemek böylece eklenmelidir.

### <a name="test-concurrency-conflicts"></a>Eşzamanlılık çakışmalarını test edin

Bir test departmanı oluşturun.

Test bölümünde Sil'in iki tarayıcı örneğini açın:

* Uygulamayı çalıştırın ve Bölümleri seçin.
* Test bölümü için **Sil** köprüünü sağ tıklatın ve **yeni sekmede Aç'ı**seçin.
* Test bölümü için **Edit** köprübağlantısını tıklatın.

İki tarayıcı sekmesi aynı bilgileri görüntüler.

İlk tarayıcı sekmesinde bütçeyi değiştirin ve **Kaydet'i**tıklatın.

Tarayıcı, değiştirilen değeri ve güncelleştirilmiş rowVersion göstergesiyle Dizin sayfasını gösterir. Güncelleştirilmiş satırSürüm göstergesine dikkat edin, diğer sekmedeki ikinci posta geri sinde görüntülenir.

İkinci sekmeden test bölümünü silin. Eşzamanlılık hatası veritabanındaki geçerli değerlerle görüntülenir. **Sil'i** tıklattığınızda, `RowVersion` güncelleştirilmedikçe varlığı siler.bölüm silinir.

## <a name="additional-resources"></a>Ek kaynaklar

* [EF Core'da Eşzamanlılık Belirteçleri](/ef/core/modeling/concurrency)
* [EF Core'da eşzamanlılık işleme](/ef/core/saving/concurrency)
* [Core 2.x kaynak ASP.NET hata ayıklama](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a>Sonraki adımlar

Bu serinin son öğretici. Ek konular bu [öğretici serisinin MVC sürümünde](xref:data/ef-mvc/index)ele alınmıştır.

> [!div class="step-by-step"]
> [Önceki öğretici](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu öğretici, birden çok kullanıcı bir varlığı aynı anda güncelleştirdiğinde (aynı anda) çakışmaları nasıl işleyeceğini gösterir. Çözemediğiniz sorunlarla karşılaştıysanız, [tamamlanan uygulamayı indirin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Talimatları indirin.](xref:index#how-to-download-a-sample)

## <a name="concurrency-conflicts"></a>Eşzamanlılık çakışmaları

Bir eşzamanlılık çakışması oluşur:

* Kullanıcı, bir varlığın edin'le mecasayfasına doğru yol alar.
* Başka bir kullanıcı, ilk kullanıcı değişikliği DB'ye yazılmadan önce aynı varlığı güncelleştirir.

Eşzamanlılık algılaması etkinleştirilemezse, eşzamanlı güncelleştirmeler oluştuğunda:

* Son güncelleştirme kazanır. Diğer bir süre, son güncelleştirme değerleri DB'ye kaydedilir.
* Geçerli güncelleştirmelerin ilki kaybolur.

### <a name="optimistic-concurrency"></a>İyimser eşzamanlılık

İyimser eşzamanlılık eşzamanlılık eşzamanlılık çakışmaların gerçekleşmesine izin verir ve bunu yaptığında uygun şekilde tepki verir. Örneğin, Jane Bölüm'ün edit sayfasını ziyaret eder ve İngilizce bölümü bütçesini 350.000,00 TL'den 0,00 TL'ye değiştirir.

![Bütçeyi 0 olarak değiştirme](concurrency/_static/change-budget.png)

Jane **Kaydet'i**tıklatmadan önce, John aynı sayfayı ziyaret eder ve Başlangıç Tarihi alanını 9/1/2007'den 9/1/2013'e değiştirir.

![Başlangıç tarihini 2013 olarak değiştirme](concurrency/_static/change-date.png)

Jane önce **Kaydet'i** tıklatıyor ve tarayıcı Dizin sayfasını görüntülediğinde değişikliğini görür.

![Bütçe sıfıra değiştirildi](concurrency/_static/budget-zero.png)

John, 350.000,00 TL bütçeyi gösteren bir Edit sayfasında **Kaydet'i** tıklatıyor. Bundan sonra ne olacağı eşzamanlılık çakışmaları nasıl ele aldığınıza göre belirlenir.

İyimser eşzamanlılık aşağıdaki seçenekleri içerir:

* Bir kullanıcının hangi özelliği değiştirdiğini izleyebilir ve yalnızca DB'deki ilgili sütunları güncelleştirebilirsiniz.

  Senaryoda, hiçbir veri kaybolmaz. Farklı özellikler iki kullanıcı tarafından güncelleştirildi. Bir dahaki sefere birisi İngilizce bölümüne göz atsa, hem Jane'in hem de John'un değişikliklerini görecek. Bu güncelleştirme yöntemi, veri kaybına neden olabilecek çakışma sayısını azaltabilir. Bu yaklaşım:
 
  * Aynı özellikte rakip değişiklikler yapılırsa veri kaybını önleyemezsiniz.
  * Genellikle bir web uygulamasında pratik değildir. Getirilen tüm değerleri ve yeni değerleri izlemek için önemli bir durum korumayı gerektirir. Büyük miktarda durum bakımı uygulama performansını etkileyebilir.
  * Bir varlık üzerinde eşzamanlılık algılamaile karşılaştırıldığında uygulama karmaşıklığını artırabilir.

* John'un değişmesine izin verebilirsin Jane'in bozuk paralarını yazabilirsin.

  Birisi İngilizce bölümüne bir daha ki sefere göz atanın, 9/1/2013 ve getirilen 350.000,00 $ değerini göreceksiniz. Bu *yaklaşıma, Kazananların Sonuncusu* veya Son *Kazananlar* senaryosu olarak adlandırılır. (İstemcinin tüm değerleri veri deposunda bulunanlardan önceliklidir.) Eşzamanlılık işleme için herhangi bir kodlama yapmazsanız, İstemci Kazançları otomatik olarak gerçekleşir.

* John'un değişikliğinin DB'de güncelleştirilen olmasını engelleyebilirsiniz. Genellikle, uygulama:

  * Bir hata iletisi görüntüleyin.
  * Verilerin geçerli durumunu gösterin.
  * Kullanıcının değişiklikleri yeniden uygulamasına izin verin.

  Buna Mağaza *Kazançları* senaryosu denir. (Veri depolama değerleri istemci tarafından gönderilen değerlerden önceliklidir.) Bu öğreticide Mağaza Kazançları senaryosunu uygularsınız. Bu yöntem, kullanıcı uyarılmadan hiçbir değişikliğin üzerine yazılmamasını sağlar.

## <a name="handling-concurrency"></a>Eşzamanlılık işleme 

Bir özellik [eşzamanlılık belirteci](/ef/core/modeling/concurrency)olarak yapılandırıldığınızda:

* EF Core, özelliğin alındıktan sonra değiştirilmediğini doğrular. Denetim, [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) veya [SaveChangesAsync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) çağrıldığında oluşur.
* Özellik alındıktan sonra değiştirildiyse, bir [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) atılır. 

DB ve veri modeli atma `DbUpdateConcurrencyException`desteklemek için yapılandırılmalıdır.

### <a name="detecting-concurrency-conflicts-on-a-property"></a>Bir özelliküzerinde eşzamanlılık çakışmalarını algılama

Eşzamanlılık çakışmaları [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) özniteliği ile özellik düzeyinde algılanabilir. Öznitelik modeldeki birden çok özelliğe uygulanabilir. Daha fazla bilgi için [Bkz. Veri Ek Açıklamaları-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).

Öznitelik `[ConcurrencyCheck]` bu öğreticide kullanılmaz.

### <a name="detecting-concurrency-conflicts-on-a-row"></a>Bir satırda eşzamanlılık çakışmalarını algılama

Eşzamanlılık çakışmalarını algılamak için modele bir [satır çevirme](/sql/t-sql/data-types/rowversion-transact-sql) izleme sütunu eklenir.  `rowversion` :

* SQL Server'a özgüdür. Diğer veritabanları benzer bir özellik sağlamayabilir.
* DB'den getirilen bir varlığın değiştirilmediğini belirlemek için kullanılır. 

DB, satır her `rowversion` güncelleştiride artımlı sıralı bir sayı oluşturur. Bir `Update` veya `Delete` komutta, `Where` yan tümce. `rowversion` Güncelleştirilen satır değiştiyse:

* `rowversion`getirilen değerle eşleşmiyor.
* `Where` Yan `Update` `Delete` tümcesi getirilen `rowversion`ilerlediği için satır veya komutlar bulmuyor.
* A `DbUpdateConcurrencyException` atılır.

EF Core'da, bir `Update` satır veya `Delete` komut tarafından güncelleştirildiğinde, eşzamanlılık özel durumu atılır.

### <a name="add-a-tracking-property-to-the-department-entity"></a>Bölüm kuruluşuna izleme özelliği ekleme

*Modeller/Department.cs*olarak, RowVersion adlı bir izleme özelliği ekleyin:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

[Zaman Damgası](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) özniteliği, bu sütunun `Where` `Update` yan tümcesi ve `Delete` komutları dahil olduğunu belirtir. SQL Server'ın `Timestamp` önceki sürümleri, SQL `rowversion` türü `timestamp` değiştirmeden önce bir SQL veri türü kullandığından öznitelik çağrılır.

Akıcı API izleme özelliğini de belirtebilir:

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

Aşağıdaki kod, Bölüm adı güncelleştirildiğinde EF Core tarafından oluşturulan T-SQL'in bir bölümünü gösterir:

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

Önceki vurgulanan kod, `WHERE` 'yi `RowVersion`içeren yan tümceyi gösterir. DB `RowVersion` `RowVersion` parametreye eşit değilse (`@p2`), satır güncelleştirilmez.

Aşağıdaki vurgulanan kod, tam olarak bir satırın güncelleştirilen T-SQL'i gösterir:

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

[@@ROWCOUNT ](/sql/t-sql/functions/rowcount-transact-sql) son ifadeden etkilenen satır sayısını döndürür. Hiçbir satırda güncellenir, EF Core `DbUpdateConcurrencyException`atar.

Visual Studio'nun çıkış penceresinde T-SQL EF Core'un oluşturduğunu görebilirsiniz.

### <a name="update-the-db"></a>DB'yi güncelleştir

`RowVersion` Özellik ekleme, geçiş gerektiren DB modelini değiştirir.

Projeyi derleyin. Komut penceresine aşağıdakileri girin:

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

Önceki komutlar:

* *Geçişler/{zaman damgası}_RowVersion.cs* geçiş dosyası ekler.
* *Geçişler/SchoolContextModelSnapshot.cs* dosyasını güncelleştirir. Güncelleştirme, `BuildModel` yönteme aşağıdaki vurgulanmış kodu ekler:

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* DB'yi güncelleştirmek için geçişleri çalıştırır.

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a>İskele Bölümler modeli

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

[İskele'deki](xref:data/ef-rp/intro#scaffold-student-pages) talimatları uygulayın öğrenci modeli `Department` ve model sınıfı için kullanın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Şu komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

Önceki komut `Department` modeli iskeleler. Projeyi Visual Studio'da açın.

Projeyi derleyin.

### <a name="update-the-departments-index-page"></a>Departmanlar Dizini sayfasını güncelleştir

İskele altyapısı Dizin `RowVersion` sayfası için bir sütun oluşturdu, ancak bu alan görüntülenmemelidir. Bu öğreticide, eşzamanlılığı anlamalarına yardımcı olmak için son bayt `RowVersion` görüntülenir. Son baytın benzersiz olacağı garanti değildir. Gerçek bir uygulama görüntülenmezdi `RowVersion` veya son `RowVersion`bayt.

Dizin sayfasını güncelleştirin:

* Dizini Bölümlerle Değiştirin.
* İçinde `RowVersion` ki işaretlemeyi son bayt `RowVersion`ile değiştirin.
* FirstMidName'i FullName ile değiştirin.

Aşağıdaki biçimlendirme güncelleştirilmiş sayfayı gösterir:

[!code-html[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a>Sayfayı Güncelle modelini güncelleştir

*Sayfaları Güncelleştir\Bölümler\Edit.cshtml.cs* aşağıdaki kodla:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

Eşzamanlılık sorununu algılamak için [OriginalValue,](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) `rowVersion` getirilen varlıktan gelen değerle güncelleştirilir. EF Core, özgün `RowVersion` değeri içeren bir WHERE yan tümcesi içeren bir SQL UPDATE komutu oluşturur. GÜNCELLEME komutundan hiçbir satır etkilenmezse (hiçbir `RowVersion` satır `DbUpdateConcurrencyException` orijinal değere sahip değilse), bir özel durum atılır.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

Önceki kodda, `Department.RowVersion` varlık getirili değeridir. `OriginalValue`bu yöntemde çağrıldığında `FirstOrDefaultAsync` DB değeridir.

Aşağıdaki kod istemci değerlerini (bu yönteme deftere nakledilen değerler) ve DB değerlerini alır:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

Aşağıdaki kod, gönderilenden farklı DB değerleri olan her sütun için `OnPostAsync`özel bir hata iletisi ekler:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

Aşağıdaki vurgulanan kod, `RowVersion` DB'den alınan yeni değerin değerini ayarlar. Kullanıcı **Kaydet'i**bir sonraki tıklatıldığında, yalnızca Edit sayfasının son ekranından oluşan eşzamanlılık hataları yakalanır.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

Eski `ModelState.Remove` `RowVersion` değere `ModelState` sahip olduğundan ifade gereklidir. Razor Page'de, `ModelState` bir alanın değeri, her ikisi de mevcut olduğunda model özellik değerlerinin üzerinde önceliklidir.

## <a name="update-the-edit-page"></a>Edit sayfasını güncelleştir

*Sayfaları/Bölümleri/Edit.cshtml'i* aşağıdaki biçimlendirmeyle güncelleştirin:

[!code-html[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

Önceki biçimlendirme:

* Yönergeyi `page` `@page` `@page "{id:int}"`' den .
* Gizli satır sürümü ekler. `RowVersion`post back değeri bağlar böylece eklenmelidir.
* Hata ayıklama amacıyla `RowVersion` son baytını görüntüler.
* Güçlü `ViewData` bir şekilde yazılanla `InstructorNameSL`değiştirir.

## <a name="test-concurrency-conflicts-with-the-edit-page"></a>Eşpara birimi çakışmalarını Edit sayfasıyla test edin

İngilizce bölümünde Edit'in iki tarayıcı örneğini açın:

* Uygulamayı çalıştırın ve Bölümleri seçin.
* İngilizce bölümü için **Edit** köprüye sağ tıklayın ve **yeni sekmede Aç'ı**seçin.
* İlk sekmede, İngilizce bölümü için **Edit** köprübağlantısını tıklatın.

İki tarayıcı sekmesi aynı bilgileri görüntüler.

İlk tarayıcı sekmesinde adı değiştirin ve **Kaydet'i**tıklatın.

![Bölüm Değiştir'den sonra sayfa 1'i edit](concurrency/_static/edit-after-change-1.png)

Tarayıcı, değiştirilen değeri ve güncelleştirilmiş rowVersion göstergesiyle Dizin sayfasını gösterir. Güncelleştirilmiş satırSürüm göstergesine dikkat edin, diğer sekmedeki ikinci posta geri sinde görüntülenir.

İkinci tarayıcı sekmesinde farklı bir alanı değiştirin.

![Bölüm Değiştir'den sonra sayfa 2'yi edit](concurrency/_static/edit-after-change-2.png)

**Kaydet**'e tıklayın. DB değerleriyle eşleşmeyan tüm alanlar için hata iletileri görürsünüz:

![Bölüm Sayfa Yıkla hata iletisi](concurrency/_static/edit-error.png)

Bu tarayıcı penceresi Ad alanını değiştirmek niyetinde değildi. Geçerli değeri (Diller) Ad alanına kopyalayın ve yapıştırın. Sekme dışarı. İstemci tarafı doğrulama hata iletisini kaldırır.

![Bölüm Sayfa Yıkla hata iletisi](concurrency/_static/cv.png)

Yeniden **Kaydet'i** tıklatın. İkinci tarayıcı sekmesine girdiğiniz değer kaydedilir. Dizin sayfasında kaydedilen değerleri görürsünüz.

## <a name="update-the-delete-page"></a>Sil sayfasını güncelleştir

Sayfasil modelini aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

Sil sayfası, varlık alındıktan sonra değiştiğinde eşzamanlılık çakışmalarını algılar. `Department.RowVersion`varlığın getirili satır sürümüdür. EF Core SQL DELETE komutunu oluşturduğunda, `RowVersion`'. SQL DELETE komutu etkilenen sıfır satırla sonuçlanırsa:

* `RowVersion` SQL DELETE komutu DB `RowVersion` ile eşleşmiyor.
* Bir DbUpdateConcurrencyException özel durum atılır.
* `OnGetAsync`ile `concurrencyError`denir.

### <a name="update-the-delete-page"></a>Sil sayfasını güncelleştir

*Sayfaları/Bölümleri/Delete.cshtml'i* aşağıdaki kodla güncelleştirin:

[!code-html[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

Önceki kod aşağıdaki değişiklikleri yapar:

* Yönergeyi `page` `@page` `@page "{id:int}"`' den .
* Bir hata iletisi ekler.
* Yönetici **alanında** FirstMidName'i FullName ile değiştirir.
* Son `RowVersion` bayt görüntülemek için değişiklikler.
* Gizli satır sürümü ekler. `RowVersion`post back değeri bağlar böylece eklenmelidir.

### <a name="test-concurrency-conflicts-with-the-delete-page"></a>Sil sayfasıyla eşzamanlılık çakışmalarını test edin

Bir test departmanı oluşturun.

Test bölümünde Sil'in iki tarayıcı örneğini açın:

* Uygulamayı çalıştırın ve Bölümleri seçin.
* Test bölümü için **Sil** köprüünü sağ tıklatın ve **yeni sekmede Aç'ı**seçin.
* Test bölümü için **Edit** köprübağlantısını tıklatın.

İki tarayıcı sekmesi aynı bilgileri görüntüler.

İlk tarayıcı sekmesinde bütçeyi değiştirin ve **Kaydet'i**tıklatın.

Tarayıcı, değiştirilen değeri ve güncelleştirilmiş rowVersion göstergesiyle Dizin sayfasını gösterir. Güncelleştirilmiş satırSürüm göstergesine dikkat edin, diğer sekmedeki ikinci posta geri sinde görüntülenir.

İkinci sekmeden test bölümünü silin. Eşzamanlılık hatası, DB'nin geçerli değerleriyle görüntülenir. **Sil'i** tıklattığınızda, `RowVersion` güncelleştirilmedikçe varlığı siler.bölüm silinir.

Bir [Inheritance](xref:data/ef-mvc/inheritance) veri modelinin nasıl devralilenene ilişkin bkz.

### <a name="additional-resources"></a>Ek kaynaklar

* [EF Core'da Eşzamanlılık Belirteçleri](/ef/core/modeling/concurrency)
* [EF Core'da eşzamanlılık işleme](/ef/core/saving/concurrency)
* [Bu öğreticinin YouTube sürümü (Eşzamanlılık Çakışmalarını Taşıma)](https://youtu.be/EosxHTFgYps)
* [Bu öğretici YouTube sürümü (Bölüm 2)](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [Bu öğretici YouTube sürümü (Bölüm 3)](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [Önceki](xref:data/ef-rp/update-related-data)

::: moniker-end

