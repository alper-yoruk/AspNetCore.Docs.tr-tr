---
title: 8. bölüm, Razor ASP.NET Core EF Core olan sayfalar-eşzamanlılık
author: rick-anderson
description: RazorSayfaların 8. bölümü ve Entity Framework öğretici serisi.
ms.author: riande
ms.custom: mvc
ms.date: 07/22/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-rp/concurrency
ms.openlocfilehash: ff9e01df002ac0fc94ced6d5d093099d66a14f36
ms.sourcegitcommit: 14c3d111f9d656c86af36ecb786037bf214f435c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86176285"
---
# <a name="part-8-razor-pages-with-ef-core-in-aspnet-core---concurrency"></a>8. bölüm, Razor ASP.NET Core EF Core olan sayfalar-eşzamanlılık

By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra)ve [Jon P Smith](https://twitter.com/thereformedprog)

[!INCLUDE [about the series](../../includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

Bu öğreticide, birden çok kullanıcı bir varlığı eşzamanlı olarak (aynı zamanda) güncelleştirilişinde çakışmaların nasıl işleneceği gösterilmektedir.

## <a name="concurrency-conflicts"></a>Eşzamanlılık çakışmaları

Şu durumlarda bir eşzamanlılık çakışması oluşur:

* Bir Kullanıcı bir varlık için düzenleme sayfasına gider.
* İlk kullanıcının değişikliği veritabanına yazılmadan önce, başka bir kullanıcı aynı varlığı güncelleştirir.

Eşzamanlılık algılama etkinleştirilmemişse, veritabanını güncelleştirme son olarak diğer kullanıcının değişikliklerinin üzerine yazılır. Bu risk kabul edilebilir ise, eşzamanlılık için programlama maliyeti avantaja aykırı bir ücret verebilir.

### <a name="pessimistic-concurrency-locking"></a>Kötümser eşzamanlılık (kilitleme)

Eşzamanlılık çakışmalarını önlemenin bir yolu veritabanı kilitlerini kullanmaktır. Bu, Kötümser eşzamanlılık olarak adlandırılır. Uygulama, güncelleştirmeyi amaçladığı bir veritabanı satırını okumadan önce bir kilit ister. Bir satır, güncelleştirme erişimi için kilitlendiğinde, ilk kilit yayımlanıncaya kadar başka hiçbir kullanıcının satırı kilitlemesine izin verilmez.

Kilitleri yönetmek dezavantajlara sahiptir. Program, karmaşık olabilir ve Kullanıcı sayısı arttıkça performans sorunlarına neden olabilir. Entity Framework Core, BT için yerleşik destek sağlamaz ve bu öğretici Bu öğreticinin nasıl uygulanacağını göstermez.

### <a name="optimistic-concurrency"></a>İyimser eşzamanlılık

İyimser eşzamanlılık eşzamanlılık çakışmalarının gerçekleşmesini sağlar ve ardından, ne zaman uygun şekilde yeniden çalışır. Örneğin, Gamze departman düzenleme sayfasını ziyaret ettiğinde, Ingilizce bölümünün bütçesini $350.000,00 ' den $0,00 ' e değiştiriyor.

![Bütçeyi 0 olarak değiştirme](concurrency/_static/change-budget30.png)

Kemal, **Kaydet**' i tıklamadan önce, John aynı sayfayı ziyaret ettiğinde başlangıç tarihi alanını 9/1/2007 ' den 9/1/2013 ' e değiştirir.

![Başlangıç tarihini 2013 olarak değiştirme](concurrency/_static/change-date30.png)

Gamze önce **Kaydet** ' i tıklatır ve değişiklik etkin duruma geçer çünkü tarayıcı Dizin sayfasını bütçe miktarı olarak sıfır ile görüntülüyor.

John, hala $350.000,00 bütçesini gösteren bir düzenleme sayfasında **Kaydet** ' i tıklatır. Sonraki durum eşzamanlılık çakışmalarını nasıl işleydiğinize göre belirlenir:

* Bir kullanıcının hangi özelliği değiştirdiği ve yalnızca ilgili sütunları veritabanında güncelleştirdiğinden haberdar olabilirsiniz.

  Senaryosunda hiçbir veri kaybolmaz. Farklı özellikler iki kullanıcı tarafından güncelleştirildi. Ingilizce bölüme bir dahaki sefer gözattığında, hem gamze 'nin hem de John 'un değişikliklerini görürler. Bu güncelleştirme yöntemi, veri kaybına neden olabilecek çakışmaların sayısını azaltabilir. Bu yaklaşım bazı dezavantajlara sahiptir:
 
  * Aynı özellikte rekabet değişiklikleri yapılmışsa veri kaybını önleyebilirsiniz.
  * Genellikle bir Web uygulamasında pratik değildir. Tüm getirilen değerleri ve yeni değerleri izlemek için önemli durumun sürdürülmesi gerekir. Büyük miktarlarda durum bulundurma, uygulama performansını etkileyebilir.
  * , Bir varlıkta eşzamanlılık algılama ile karşılaştırıldığında uygulama karmaşıklığını artırabilir.

* John 'un değişikliğini kemal 'in değişikliğini geçersiz kılabilirsiniz.

  Ingilizce bölüme bir dahaki sefer gözattığında, 9/1/2013 ve getirilen $350.000,00 değerini görür. Bu yaklaşım, *Istemci WINS* veya *son WINS* senaryosu olarak adlandırılır. (İstemciden gelen tüm değerler veri deposunda yer alacak şekilde önceliklidir.) Eşzamanlılık işleme için herhangi bir kodlama yapmazsanız, Istemci WINS otomatik olarak gerçekleşir.

* John 'un değişikliğini veritabanında güncelleştirilmesini engelleyebilirsiniz. Uygulama genellikle şu şekilde olur:

  * Bir hata iletisi görüntüler.
  * Verilerin geçerli durumunu gösterir.
  * Kullanıcının değişiklikleri yeniden uygulamasına izin verin.

  Buna *Mağaza WINS* senaryosu denir. (Veri deposu değerleri, istemci tarafından gönderilen değerlere göre önceliklidir.) Bu öğreticide mağaza WINS senaryosunu uygulamanız gerekir. Bu yöntem, bir Kullanıcı uyarı vermeden hiçbir değişikliğin üzerine yazılmamasını sağlar.

## <a name="conflict-detection-in-ef-core"></a>EF Core çakışma algılama

EF Core, `DbConcurrencyException` Çakışmalar algıladığında özel durum oluşturur. Çakışma algılamayı etkinleştirmek için veri modeli yapılandırılmalıdır. Çakışma algılamayı etkinleştirme seçenekleri şunlardır:

* EF Core, Update ve DELETE komutlarının WHERE yan tümcesinde [eşzamanlılık belirteçleri](/ef/core/modeling/concurrency) olarak yapılandırılan sütunların özgün değerlerini içerecek şekilde yapılandırın.

  `SaveChanges`Çağrıldığında WHERE yan tümcesi, özniteliğiyle birlikte açıklanan özelliklerin özgün değerlerini arar <xref:System.ComponentModel.DataAnnotations.ConcurrencyCheckAttribute> . Update deyimleri, satır ilk okunduğundan bu yana eşzamanlılık belirteci özelliklerinden herhangi biri değiştiyse güncelleştirilecek bir satır bulmayacaktır. EF Core eşzamanlılık çakışması olarak yorumlar. Birçok sütunu olan veritabanı tablolarında bu yaklaşım çok büyük WHERE yan tümceleriyle sonuçlanabilir ve büyük miktarlarda durum gerektirebilir. Bu nedenle bu yaklaşım genellikle önerilmez ve bu öğreticide kullanılan yöntem değildir.

* Veritabanı tablosunda, bir satırın ne zaman değiştirildiğini belirlemede kullanılabilecek bir izleme sütunu ekleyin.

  SQL Server veritabanında, izleme sütununun veri türü olur `rowversion` . `rowversion`Değer, satır her güncelleştirildiği zaman artılan sıralı bir sayıdır. Bir Update veya delete komutunda WHERE yan tümcesi, izleme sütununun (orijinal satır sürüm numarası) orijinal değerini içerir. Güncelleştirilmekte olan satır başka bir kullanıcı tarafından değiştirilmişse `rowversion` sütunundaki değer özgün değerden farklı olur. Bu durumda, Update veya DELETE deyimi WHERE yan tümcesi nedeniyle güncelleştirilecek satırı bulamıyor. Bir Update veya delete komutundan hiçbir satır etkilenmeden EF Core eşzamanlılık özel durumu oluşturur.

## <a name="add-a-tracking-property"></a>İzleme özelliği Ekle

*Modeller/departman. cs*' de, rowversion adlı bir izleme özelliği ekleyin:

[!code-csharp[](intro/samples/cu30/Models/Department.cs?highlight=26,27)]

<xref:System.ComponentModel.DataAnnotations.TimestampAttribute>Özniteliği sütunu eşzamanlılık izleme sütunu olarak tanımlar. Fluent API, izleme özelliğini belirtmenin alternatif bir yoludur:

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Bir SQL Server veritabanı için, bir `[Timestamp]` varlık özelliği üzerindeki özniteliği byte dizisi olarak tanımlanır:

* Sütunun WHERE yan tümceleri DELETE ve UPDATE 'e dahil edilmesini sağlar.
* Veritabanındaki sütun türünü [rowversion](/sql/t-sql/data-types/rowversion-transact-sql)olarak ayarlar.

Veritabanı, satır her güncelleştirildiği zaman artılan sıralı bir satır sürüm numarası oluşturur. `Update`Veya `Delete` komutunda, `Where` yan tümce getirilen satır sürümü değerini içerir. Güncelleştirilmekte olan satır getirilmesinden sonra değiştiyse:

* Geçerli satır sürümü değeri getirilen değerle eşleşmiyor.
* `Update` `Delete` `Where` Yan tümce getirilen satır sürümü değerini aradığı için veya komutları bir satır bulmayın.
* Bir `DbUpdateConcurrencyException` oluşturulur.

Aşağıdaki kod, Bölüm adı güncelleştirildiği zaman EF Core tarafından oluşturulan T-SQL ' in bir bölümünü gösterir:

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=2-3)]

Önceki vurgulanan kod, `WHERE` içeren yan tümceyi gösterir `RowVersion` . Veritabanı `RowVersion` `RowVersion` () parametresine eşit değilse `@p2` , hiçbir satır güncellenmez.

Aşağıdaki vurgulanan kod, tam olarak bir satırın güncelleştirildiğini doğrulayan T-SQL ' i göstermektedir:

[!code-sql[](intro/samples/cu30snapshots/8-concurrency/sql.txt?highlight=4-6)]

[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) Son deyimden etkilenen satır sayısını döndürür. Hiçbir satır güncellenmemişse EF Core bir oluşturur `DbUpdateConcurrencyException` .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Bir SQLite veritabanı için, `[Timestamp]` bir varlık özelliği üzerindeki özniteliği byte dizisi olarak tanımlanır:

* Sütunun WHERE yan tümceleri DELETE ve UPDATE 'e dahil edilmesini sağlar.
* BLOB sütun türüyle eşlenir.

Veritabanı Tetikleyicileri satır her güncelleştirildiğinde, RowVersion sütununu yeni bir rastgele bayt dizisiyle güncelleştirir. `Update`Or `Delete` komutunda, `Where` yan tümce rowversion sütununun getirilen değerini içerir. Güncelleştirilmekte olan satır getirilmesinden sonra değiştiyse:

* Geçerli satır sürümü değeri getirilen değerle eşleşmiyor.
* `Update`Or `Delete` komutu bir satır bulmadığından, `Where` yan tümce orijinal satır sürümü değerini arar.
* Bir `DbUpdateConcurrencyException` oluşturulur.

---

### <a name="update-the-database"></a>Veritabanını güncelleştirme

Özelliği eklendiğinde `RowVersion` geçiş gerektiren veri modeli değişir.

Projeyi derleyin. 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* PMC 'de şu komutu çalıştırın:

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

* *Geçişleri/{zaman damgası} _RowVersion. cs* geçiş dosyasını oluşturur.
* *Geçişler/SchoolContextModelSnapshot. cs* dosyasını güncelleştirir. Bu güncelleştirme yöntemine aşağıdaki vurgulanmış kodu ekler `BuildModel` :

  [!code-csharp[](intro/samples/cu30/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=15-17)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* PMC 'de şu komutu çalıştırın:

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Dosyayı açın `Migrations/<timestamp>_RowVersion.cs` ve vurgulanmış kodu ekleyin:

  [!code-csharp[](intro/samples/cu30/MigrationsSQLite/20190722151951_RowVersion.cs?highlight=16-42)]

  Yukarıdaki kod:

  * Mevcut satırları rastgele blob değerleriyle güncelleştirir.
  * Satır her güncelleştirildiğinde ROWVERSION sütununu rastgele bir blob değerine ayarlamış veritabanı Tetikleyicileri ekler.

* Bir terminalde aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet ef database update
  ```

---

<a name="scaffold"></a>

## <a name="scaffold-department-pages"></a>Yapı iskelesi departmanı sayfaları

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Aşağıdaki özel durumlarla birlikte [Yapı Fkatlama öğrenci sayfalarındaki](xref:data/ef-rp/intro#scaffold-student-pages) yönergeleri izleyin:

* *Sayfalar/departmanlar* klasörü oluşturun.  
* `Department`Model sınıfı için kullanın.
  * Yeni bir tane oluşturmak yerine var olan bağlam sınıfını kullanın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Sayfalar/departmanlar* klasörü oluşturun.

* Bölüm sayfalarını iskele almak için aşağıdaki komutu çalıştırın.

  **Windows 'da:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

  **Linux veya macOS 'ta:**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages/Departments --referenceScriptLibraries
  ```

---

Projeyi derleyin.

## <a name="update-the-index-page"></a>Dizin sayfasını Güncelleştir

Scafkatlama aracı `RowVersion` Dizin sayfası için bir sütun oluşturdu, ancak bu alan bir üretim uygulamasında gösterilmemelidir. Bu öğreticide, `RowVersion` eşzamanlılık işlemenin nasıl çalıştığını göstermeye yardımcı olmak için öğesinin son baytı görüntülenir. Son baytın kendi kendine benzersiz olması garanti edilmez.

*Pages\Departments\Index.cshtml* sayfasını güncelleştir:

* Dizini departmanlar ile değiştirin.
* `RowVersion`Yalnızca bayt dizisinin son baytını göstermek için içeren kodu değiştirin.
* FirstMidName öğesini FullName ile değiştirin.

Aşağıdaki kod, güncelleştirilmiş sayfayı gösterir:

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Index.cshtml?highlight=5,8,29,48,51)]

## <a name="update-the-edit-page-model"></a>Düzenleme sayfası modelini güncelleştirme

Aşağıdaki kodla *Pages\Departments\Edit.cshtml.cs* güncelleştirin:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_All)]

, <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.OriginalValue> `rowVersion` Yönteminde alındığı sırada varlıktaki değerle güncelleştirilir `OnGet` . EF Core, özgün değeri içeren WHERE yan tümcesiyle bir SQL UPDATE komutu oluşturur `RowVersion` . UPDATE komutundan hiçbir satır etkilenmeden (hiçbir satır özgün değere sahip değilse `RowVersion` ), bir `DbUpdateConcurrencyException` özel durum oluşturulur.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_RowVersion&highlight=17-18)]

Vurgulanan kodda:

* İçindeki değeri, `Department.RowVersion` ilk olarak düzenleme sayfasına yönelik GET isteğine getirilen varlıkta olduğu şeydir. Değer, `OnPost` Razor düzenlenecek varlığı görüntüleyen sayfadaki bir gizli alanla yöntemine sağlanır. Gizli alan değeri `Department.RowVersion` model cildi tarafından öğesine kopyalanır.
* `OriginalValue`WHERE yan tümcesinde EF Core kullanılacak şeydir. Vurgulanan kod satırından önce, `OriginalValue` Bu yöntemde çağrıldığında veritabanında bulunan değeri, `FirstOrDefaultAsync` düzenleme sayfasında görüntülendiklerden farklı olabilir.
* Vurgulanan kod, EF Core `RowVersion` `Department` SQL Update ifadesinin WHERE yan tümcesinde görüntülenen varlıktaki özgün değeri kullandığından emin olur.

Bir eşzamanlılık hatası oluştuğunda, aşağıdaki vurgulanmış kod istemci değerlerini (Bu yönteme gönderilen değerler) ve veritabanı değerlerini alır.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=14,23)]

Aşağıdaki kod, ' a gönderilen değerden farklı veritabanı değerleri olan her bir sütun için özel bir hata iletisi ekler `OnPostAsync` :

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_Error)]

Aşağıdaki vurgulanan kod, `RowVersion` değeri veritabanından alınan yeni değer olarak ayarlar. Kullanıcının bir dahaki sefer **Kaydet**' i tıklatması durumunda, yalnızca düzenleme sayfasının son görüntüsü bu yana gerçekleşen eşzamanlılık hataları yakalanacaktır.

[!code-csharp[](intro/samples/cu30/Pages/Departments/Edit.cshtml.cs?name=snippet_TryUpdateModel&highlight=28)]

`ModelState.Remove`Eski değere sahip olduğundan deyimin olması gerekir `ModelState` `RowVersion` . RazorSayfada, `ModelState` bir alanın değeri, her ikisi de varsa Model Özellik değerlerinden önceliklidir.

### <a name="update-the-edit-page"></a>Düzenleme sayfasını Güncelleştir

*Sayfaları/departmanları/Düzenle. cshtml* 'yi aşağıdaki kodla güncelleştirin:

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

Yukarıdaki kod:

* `page`İçindeki yönergesini ' a `@page` güncelleştirir `@page "{id:int}"` .
* Gizli satır sürümü ekler. `RowVersion`, geri gönderme, değeri bağlayan şekilde eklenmelidir.
* `RowVersion`Hata ayıklama amacıyla son baytını görüntüler.
* `ViewData`Türü kesin belirlenmiş olan ile değiştirilir `InstructorNameSL` .

### <a name="test-concurrency-conflicts-with-the-edit-page"></a>Düzenleme sayfasıyla eşzamanlılık çakışmalarını test etme

Ingilizce bölümünde düzenleme öğesinin iki tarayıcı örneğini açın:

* Uygulamayı çalıştırın ve Departmanlar ' ı seçin.
* Ingilizce departman için **düzenleme** köprüsüne sağ tıklayın ve **Yeni sekmede aç**' ı seçin.
* İlk sekmede, Ingilizce bölümünün **düzenleme** Köprüsü ' ne tıklayın.

İki tarayıcı sekmesi aynı bilgileri görüntüler.

İlk tarayıcı sekmesinde adı değiştirin ve **Kaydet**' e tıklayın.

![Bölüm Düzenle sayfa 1 değişiklikten sonra](concurrency/_static/edit-after-change-130.png)

Tarayıcı, değiştirilen değeri olan dizin sayfasını gösterir ve rowVersion göstergesi güncellenir. Güncelleştirilmiş ROWVERSION göstergesinin, diğer sekmede ikinci geri göndermede görüntülendiğini aklınızda edin.

İkinci tarayıcı sekmesinden farklı bir alanı değiştirin.

![Değişiklik sonrasında bölüm düzenleme sayfası 2](concurrency/_static/edit-after-change-230.png)

**Kaydet**’e tıklayın. Veritabanı değerleriyle eşleşmeyen tüm alanlar için hata iletileri görürsünüz:

![Bölüm düzenleme sayfası hata iletisi](concurrency/_static/edit-error30.png)

Bu tarayıcı penceresi, ad alanını değiştirmeyi planlamadı. Geçerli değeri (diller) kopyalayın ve ad alanına yapıştırın. Sekme genişletme. İstemci tarafı doğrulaması hata iletisini kaldırır.

Yeniden **Kaydet** ' e tıklayın. İkinci tarayıcı sekmesine girdiğiniz değer kaydedilir. Kaydedilen değerleri Dizin sayfasında görürsünüz.

## <a name="update-the-delete-page-model"></a>Sayfa silme modelini Güncelleştir

*Sayfaları/departmanları/delete. cshtml. cs* öğesini aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu30/Pages/Departments/Delete.cshtml.cs)]

Sil sayfası, varlık alındıktan sonra değiştirildiğinde eşzamanlılık çakışmalarını algılar. `Department.RowVersion`, varlık getirilen satır sürümüdür. EF Core, SQL DELETE komutunu oluşturduğunda, içeren bir WHERE yan tümcesi içerir `RowVersion` . SQL DELETE komutu sıfır satırla etkileniyorsa:

* `RowVersion`SQL DELETE komutunda bulunan, `RowVersion` veritabanında eşleşmiyor.
* Bir DbUpdateConcurrencyException özel durumu oluşturulur.
* `OnGetAsync`ile çağırılır `concurrencyError` .

### <a name="update-the-delete-page"></a>Silme sayfasını Güncelleştir

*Sayfaları/departmanları/delete. cshtml* 'yi aşağıdaki kodla güncelleştirin:

[!code-cshtml[](intro/samples/cu30/Pages/Departments/Delete.cshtml?highlight=1,10,39,42,51)]

Yukarıdaki kod aşağıdaki değişiklikleri yapar:

* `page`İçindeki yönergesini ' a `@page` güncelleştirir `@page "{id:int}"` .
* Bir hata iletisi ekler.
* FirstMidName öğesini, **yönetici** alanındaki FullName ile değiştirir.
* `RowVersion`Son baytın görüntüleneceği değişiklikler.
* Gizli satır sürümü ekler. `RowVersion`, geri gönderme, değeri bağlayan şekilde eklenmelidir.

### <a name="test-concurrency-conflicts"></a>Eşzamanlılık çakışmalarını test et

Test departmanı oluşturun.

Test departmanı üzerinde silmenin iki tarayıcı örneğini açın:

* Uygulamayı çalıştırın ve Departmanlar ' ı seçin.
* Test departmanı için **silme** köprüsünü sağ tıklayın ve **Yeni sekmede aç**' ı seçin.
* Test departmanı için **düzenleme** köprüsüne tıklayın.

İki tarayıcı sekmesi aynı bilgileri görüntüler.

İlk tarayıcı sekmesinde bütçeyi değiştirin ve **Kaydet**' e tıklayın.

Tarayıcı, değiştirilen değeri olan dizin sayfasını gösterir ve rowVersion göstergesi güncellenir. Güncelleştirilmiş ROWVERSION göstergesinin, diğer sekmede ikinci geri göndermede görüntülendiğini aklınızda edin.

İkinci sekmeden test departmanını silin. Veritabanında geçerli değerlerle birlikte bir eşzamanlılık hatası görüntülenir. **Sil** ' i tıklatmak, güncelleştirilmemiş olmadığı takdirde varlığı siler `RowVersion` .

## <a name="additional-resources"></a>Ek kaynaklar

* [EF Core eşzamanlılık belirteçleri](/ef/core/modeling/concurrency)
* [EF Core eşzamanlılık işle](/ef/core/saving/concurrency)
* [2. x kaynağı ASP.NET Core hata ayıklaması](https://github.com/dotnet/AspNetCore.Docs/issues/4155)

## <a name="next-steps"></a>Sonraki adımlar

Bu, serideki son öğreticidir. [Bu öğretici serisinin MVC sürümünde](xref:data/ef-mvc/index)ek konular ele alınmıştır.

> [!div class="step-by-step"]
> [Önceki öğretici](xref:data/ef-rp/update-related-data)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu öğreticide, birden çok kullanıcı bir varlığı eşzamanlı olarak (aynı zamanda) güncelleştirilişinde çakışmaların nasıl işleneceği gösterilmektedir. Sorun yaşıyorsanız, bu [uygulamayı indiremez veya görüntüleyemezsiniz.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Yönergeleri indirin](xref:index#how-to-download-a-sample).

## <a name="concurrency-conflicts"></a>Eşzamanlılık çakışmaları

Şu durumlarda bir eşzamanlılık çakışması oluşur:

* Bir Kullanıcı bir varlık için düzenleme sayfasına gider.
* İlk kullanıcının değişikliği VERITABANıNA yazılmadan önce, başka bir kullanıcı aynı varlığı güncelleştirir.

Eşzamanlılık algılama etkin değilse, eşzamanlı güncelleştirmeler gerçekleştiğinde:

* Son güncelleştirme kazanır. Diğer bir deyişle, son güncelleştirme değerleri VERITABANıNA kaydedilir.
* Geçerli güncelleştirmelerin ilki kaybedilir.

### <a name="optimistic-concurrency"></a>İyimser eşzamanlılık

İyimser eşzamanlılık eşzamanlılık çakışmalarının gerçekleşmesini sağlar ve ardından, ne zaman uygun şekilde yeniden çalışır. Örneğin, Gamze departman düzenleme sayfasını ziyaret ettiğinde, Ingilizce bölümünün bütçesini $350.000,00 ' den $0,00 ' e değiştiriyor.

![Bütçeyi 0 olarak değiştirme](concurrency/_static/change-budget.png)

Kemal, **Kaydet**' i tıklamadan önce, John aynı sayfayı ziyaret ettiğinde başlangıç tarihi alanını 9/1/2007 ' den 9/1/2013 ' e değiştirir.

![Başlangıç tarihini 2013 olarak değiştirme](concurrency/_static/change-date.png)

Gamze önce **Kaydet** ' i tıklatır ve tarayıcı Dizin sayfasını görüntülediğinde değişikliği görür.

![Bütçe sıfıra değişti](concurrency/_static/budget-zero.png)

John, hala $350.000,00 bütçesini gösteren bir düzenleme sayfasında **Kaydet** ' i tıklatır. Sonraki durum eşzamanlılık çakışmalarını nasıl işleydiğinize göre belirlenir.

İyimser eşzamanlılık aşağıdaki seçenekleri içerir:

* Bir kullanıcının hangi özelliği değiştirmiş olduğunu izleyebilir ve yalnızca VERITABANıNDAKI ilgili sütunları güncelleştirebilirsiniz.

  Senaryosunda hiçbir veri kaybolmaz. Farklı özellikler iki kullanıcı tarafından güncelleştirildi. Ingilizce bölüme bir dahaki sefer gözattığında, hem gamze 'nin hem de John 'un değişikliklerini görürler. Bu güncelleştirme yöntemi, veri kaybına neden olabilecek çakışmaların sayısını azaltabilir. Bu yaklaşım:
 
  * Aynı özellikte rekabet değişiklikleri yapılmışsa veri kaybını önleyebilirsiniz.
  * Genellikle bir Web uygulamasında pratik değildir. Tüm getirilen değerleri ve yeni değerleri izlemek için önemli durumun sürdürülmesi gerekir. Büyük miktarlarda durum bulundurma, uygulama performansını etkileyebilir.
  * , Bir varlıkta eşzamanlılık algılama ile karşılaştırıldığında uygulama karmaşıklığını artırabilir.

* John 'un değişikliğini kemal 'in değişikliğini geçersiz kılabilirsiniz.

  Ingilizce bölüme bir dahaki sefer gözattığında, 9/1/2013 ve getirilen $350.000,00 değerini görür. Bu yaklaşım, *Istemci WINS* veya *son WINS* senaryosu olarak adlandırılır. (İstemciden gelen tüm değerler veri deposunda yer alacak şekilde önceliklidir.) Eşzamanlılık işleme için herhangi bir kodlama yapmazsanız, Istemci WINS otomatik olarak gerçekleşir.

* John 'un değişikliğini VERITABANıNDA güncelleştirilmesini engelleyebilirsiniz. Uygulama genellikle şu şekilde olur:

  * Bir hata iletisi görüntüler.
  * Verilerin geçerli durumunu gösterir.
  * Kullanıcının değişiklikleri yeniden uygulamasına izin verin.

  Buna *Mağaza WINS* senaryosu denir. (Veri deposu değerleri, istemci tarafından gönderilen değerlere göre önceliklidir.) Bu öğreticide mağaza WINS senaryosunu uygulamanız gerekir. Bu yöntem, bir Kullanıcı uyarı vermeden hiçbir değişikliğin üzerine yazılmamasını sağlar.

## <a name="handling-concurrency"></a>Eşzamanlılık işleme 

Bir özellik [eşzamanlılık belirteci](/ef/core/modeling/concurrency)olarak yapılandırıldığında:

* EF Core, özelliğin getirildikten sonra değiştirilmediğini doğrular. Bu denetim, [SaveChanges](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechanges?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChanges) veya [Savechangesasync](/dotnet/api/microsoft.entityframeworkcore.dbcontext.savechangesasync?view=efcore-2.0#Microsoft_EntityFrameworkCore_DbContext_SaveChangesAsync_System_Threading_CancellationToken_) çağrıldığında gerçekleşir.
* Özellik alındıktan sonra değiştirilmişse, bir [DbUpdateConcurrencyException](/dotnet/api/microsoft.entityframeworkcore.dbupdateconcurrencyexception?view=efcore-2.0) oluşturulur. 

DB ve veri modeli, üretilmesini destekleyecek şekilde yapılandırılmalıdır `DbUpdateConcurrencyException` .

### <a name="detecting-concurrency-conflicts-on-a-property"></a>Bir özellikte eşzamanlılık çakışmalarını algılama

Eşzamanlılık çakışmaları özellik düzeyinde [ConcurrencyCheck](/dotnet/api/system.componentmodel.dataannotations.concurrencycheckattribute?view=netcore-2.0) özniteliğiyle algılanabilir. Öznitelik, modeldeki birden çok özelliğe uygulanabilir. Daha fazla bilgi için bkz. [veri ek açıklamaları-ConcurrencyCheck](/ef/core/modeling/concurrency#data-annotations).

`[ConcurrencyCheck]`Öznitelik bu öğreticide kullanılmıyor.

### <a name="detecting-concurrency-conflicts-on-a-row"></a>Bir satırda eşzamanlılık çakışmalarını algılama

Eşzamanlılık çakışmalarını algılamak için, modele bir [ROWVERSION](/sql/t-sql/data-types/rowversion-transact-sql) izleme sütunu eklenir.  `rowversion` :

* SQL Server özeldir. Diğer veritabanları benzer bir özellik sağlayamayabilir.
* , DB 'den getirilmesinden sonra bir varlığın değiştirilmediğini tespit etmek için kullanılır. 

DB, `rowversion` satır her güncelleştirildiği zaman arttırılan sıralı bir sayı üretir. `Update`Or `Delete` komutunda, `Where` yan tümcesi getirilen değerini içerir `rowversion` . Güncelleştirilmekte olan satır değiştiyse:

* `rowversion`getirilen değerle eşleşmez.
* `Update`Yan tümcesi getirilen ' i içerdiğinden veya `Delete` komutları bir satır bulmayın `Where` `rowversion` .
* Bir `DbUpdateConcurrencyException` oluşturulur.

EF Core, bir veya komutu tarafından hiçbir satır güncellenmemişse `Update` `Delete` , bir eşzamanlılık özel durumu oluşturulur.

### <a name="add-a-tracking-property-to-the-department-entity"></a>Bölüm varlığına bir izleme özelliği ekleyin

*Modeller/departman. cs*' de, rowversion adlı bir izleme özelliği ekleyin:

[!code-csharp[](intro/samples/cu/Models/Department.cs?name=snippet_Final&highlight=26,27)]

[Timestamp](/dotnet/api/system.componentmodel.dataannotations.timestampattribute) özniteliği, bu sütunun `Where` yan tümcesine `Update` ve komutlarına dahil edildiğini belirtir `Delete` . Özniteliği, `Timestamp` SQL Server önceki SÜRÜMLERI `timestamp` SQL türü DEĞIŞTIRILMEDEN önce SQL veri türü kullandığından çağrılır `rowversion` .

Fluent API izleme özelliğini de belirtebilir:

```csharp
modelBuilder.Entity<Department>()
  .Property<byte[]>("RowVersion")
  .IsRowVersion();
```

Aşağıdaki kod, Bölüm adı güncelleştirildiği zaman EF Core tarafından oluşturulan T-SQL ' in bir bölümünü gösterir:

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=2-3)]

Önceki vurgulanan kod, `WHERE` içeren yan tümceyi gösterir `RowVersion` . DB `RowVersion` `RowVersion` parametresi () değerine eşit değilse `@p2` , hiçbir satır güncellenmez.

Aşağıdaki vurgulanan kod, tam olarak bir satırın güncelleştirildiğini doğrulayan T-SQL ' i göstermektedir:

[!code-sql[](intro/samples/cu21snapshots/sql.txt?highlight=4-6)]

[@@ROWCOUNT](/sql/t-sql/functions/rowcount-transact-sql) Son deyimden etkilenen satır sayısını döndürür. Hiçbir satır güncelleştirilmemiş EF Core bir oluşturur `DbUpdateConcurrencyException` .

T-SQL EF Core, Visual Studio 'nun çıkış penceresinde görebilirsiniz.

### <a name="update-the-db"></a>DB 'yi güncelleştirme

Özelliği eklendiğinde `RowVersion` , geçiş GEREKTIREN DB modeli değişir.

Projeyi derleyin. Komut penceresine şunu girin:

```dotnetcli
dotnet ef migrations add RowVersion
dotnet ef database update
```

Önceki komutlar:

* *Geçişler/{zaman damgası} _RowVersion. cs* geçiş dosyasını ekler.
* *Geçişler/SchoolContextModelSnapshot. cs* dosyasını güncelleştirir. Bu güncelleştirme yöntemine aşağıdaki vurgulanmış kodu ekler `BuildModel` :

  [!code-csharp[](intro/samples/cu/Migrations/SchoolContextModelSnapshot.cs?name=snippet_Department&highlight=14-16)]

* VERITABANıNı güncelleştirmek için geçişleri çalıştırır.

<a name="scaffold"></a>

## <a name="scaffold-the-departments-model"></a>Departmanlar modelini temklesi

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio) 

[Öğrenci modelini Scafkatlama](xref:data/ef-rp/intro#scaffold-student-pages) ve `Department` model sınıfı için kullanma bölümündeki yönergeleri izleyin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

 Şu komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Department -dc SchoolContext -udl -outDir Pages\Departments --referenceScriptLibraries
  ```

---

Yukarıdaki komut, modeli bir daha yasaklıyor `Department` . Projeyi Visual Studio'da açın.

Projeyi derleyin.

### <a name="update-the-departments-index-page"></a>Departmanlar Dizin sayfasını Güncelleştir

Scafkatlama altyapısı `RowVersion` Dizin sayfası için bir sütun oluşturdu, ancak bu alan gösterilmemelidir. Bu öğreticide, `RowVersion` eşzamanlılık kavramasına yardımcı olmak için öğesinin son baytı görüntülenir. Son baytın benzersiz olması garanti edilmez. Gerçek bir uygulama, `RowVersion` veya son baytını göstermez `RowVersion` .

Dizin sayfasını güncelleştir:

* Dizini departmanlar ile değiştirin.
* İçeren biçimlendirmeyi `RowVersion` son baytla değiştirin `RowVersion` .
* FirstMidName öğesini FullName ile değiştirin.

Aşağıdaki biçimlendirme güncelleştirilmiş sayfayı göstermektedir:

[!code-cshtml[](intro/samples/cu/Pages/Departments/Index.cshtml?highlight=5,8,29,47,50)]

### <a name="update-the-edit-page-model"></a>Düzenleme sayfası modelini güncelleştirme

Aşağıdaki kodla *Pages\Departments\Edit.cshtml.cs* güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet)]

Bir eşzamanlılık sorunu tespit etmek için [OriginalValue](/dotnet/api/microsoft.entityframeworkcore.changetracking.propertyentry.originalvalue?view=efcore-2.0#Microsoft_EntityFrameworkCore_ChangeTracking_PropertyEntry_OriginalValue) , `rowVersion` getirilen varlıktaki değeri ile güncelleştirilir. EF Core, özgün değeri içeren WHERE yan tümcesiyle bir SQL UPDATE komutu oluşturur `RowVersion` . UPDATE komutundan hiçbir satır etkilenmeden (hiçbir satır özgün değere sahip değilse `RowVersion` ), bir `DbUpdateConcurrencyException` özel durum oluşturulur.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_rv&highlight=24-999)]

Önceki kodda, `Department.RowVersion` varlık getirilirken değeridir. `OriginalValue`Bu yöntemde çağrıldığında, DB 'deki değerdir `FirstOrDefaultAsync` .

Aşağıdaki kod, istemci değerlerini (Bu yönteme gönderilen değerler) ve DB değerlerini alır:

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=9,18)]

Aşağıdaki kod, ' den postalandıklarından farklı olarak DB değerleri olan her bir sütun için özel bir hata iletisi ekler `OnPostAsync` :

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_err)]

Aşağıdaki vurgulanan kod, `RowVersion` değeri, veritabanından alınan yeni değer olarak ayarlar. Kullanıcının bir dahaki sefer **Kaydet**' i tıklatması durumunda, yalnızca düzenleme sayfasının son görüntüsü bu yana gerçekleşen eşzamanlılık hataları yakalanacaktır.

[!code-csharp[](intro/samples/cu/Pages/Departments/Edit.cshtml.cs?name=snippet_try&highlight=23)]

`ModelState.Remove`Eski değere sahip olduğundan deyimin olması gerekir `ModelState` `RowVersion` . RazorSayfada, `ModelState` bir alanın değeri, her ikisi de varsa Model Özellik değerlerinden önceliklidir.

## <a name="update-the-edit-page"></a>Düzenleme sayfasını Güncelleştir

*Sayfaları/departmanları/Düzenle. cshtml* 'yi şu biçimlendirmeyle güncelleştirin:

[!code-cshtml[](intro/samples/cu/Pages/Departments/Edit.cshtml?highlight=1,14,16-17,37-39)]

Yukarıdaki biçimlendirme:

* `page`İçindeki yönergesini ' a `@page` güncelleştirir `@page "{id:int}"` .
* Gizli satır sürümü ekler. `RowVersion`, geri gönder değeri bağlayan için eklenmelidir.
* `RowVersion`Hata ayıklama amacıyla son baytını görüntüler.
* `ViewData`Türü kesin belirlenmiş olan ile değiştirilir `InstructorNameSL` .

## <a name="test-concurrency-conflicts-with-the-edit-page"></a>Düzenleme sayfasıyla eşzamanlılık çakışmalarını test etme

Ingilizce bölümünde düzenleme öğesinin iki tarayıcı örneğini açın:

* Uygulamayı çalıştırın ve Departmanlar ' ı seçin.
* Ingilizce departman için **düzenleme** köprüsüne sağ tıklayın ve **Yeni sekmede aç**' ı seçin.
* İlk sekmede, Ingilizce bölümünün **düzenleme** Köprüsü ' ne tıklayın.

İki tarayıcı sekmesi aynı bilgileri görüntüler.

İlk tarayıcı sekmesinde adı değiştirin ve **Kaydet**' e tıklayın.

![Bölüm Düzenle sayfa 1 değişiklikten sonra](concurrency/_static/edit-after-change-1.png)

Tarayıcı, değiştirilen değeri olan dizin sayfasını gösterir ve rowVersion göstergesi güncellenir. Güncelleştirilmiş ROWVERSION göstergesinin, diğer sekmede ikinci geri göndermede görüntülendiğini aklınızda edin.

İkinci tarayıcı sekmesinden farklı bir alanı değiştirin.

![Değişiklik sonrasında bölüm düzenleme sayfası 2](concurrency/_static/edit-after-change-2.png)

**Kaydet**’e tıklayın. DB değerleriyle eşleşmeyen tüm alanlar için hata iletileri görürsünüz:

![Bölüm düzenleme sayfası hata iletisi](concurrency/_static/edit-error.png)

Bu tarayıcı penceresi, ad alanını değiştirmeyi planlamadı. Geçerli değeri (diller) kopyalayın ve ad alanına yapıştırın. Sekme genişletme. İstemci tarafı doğrulaması hata iletisini kaldırır.

![Bölüm düzenleme sayfası hata iletisi](concurrency/_static/cv.png)

Yeniden **Kaydet** ' e tıklayın. İkinci tarayıcı sekmesine girdiğiniz değer kaydedilir. Kaydedilen değerleri Dizin sayfasında görürsünüz.

## <a name="update-the-delete-page"></a>Silme sayfasını Güncelleştir

Silme sayfası modelini aşağıdaki kodla güncelleştirin:

[!code-csharp[](intro/samples/cu/Pages/Departments/Delete.cshtml.cs)]

Sil sayfası, varlık alındıktan sonra değiştirildiğinde eşzamanlılık çakışmalarını algılar. `Department.RowVersion`, varlık getirilen satır sürümüdür. EF Core, SQL DELETE komutunu oluşturduğunda, içeren bir WHERE yan tümcesi içerir `RowVersion` . SQL DELETE komutu sıfır satırla etkileniyorsa:

* `RowVersion`SQL DELETE komutunda bulunan, DB ile eşleşmiyor `RowVersion` .
* Bir DbUpdateConcurrencyException özel durumu oluşturulur.
* `OnGetAsync`ile çağırılır `concurrencyError` .

### <a name="update-the-delete-page"></a>Silme sayfasını Güncelleştir

*Sayfaları/departmanları/delete. cshtml* 'yi aşağıdaki kodla güncelleştirin:

[!code-cshtml[](intro/samples/cu/Pages/Departments/Delete.cshtml?highlight=1,10,39,51)]

Yukarıdaki kod aşağıdaki değişiklikleri yapar:

* `page`İçindeki yönergesini ' a `@page` güncelleştirir `@page "{id:int}"` .
* Bir hata iletisi ekler.
* FirstMidName öğesini, **yönetici** alanındaki FullName ile değiştirir.
* `RowVersion`Son baytın görüntüleneceği değişiklikler.
* Gizli satır sürümü ekler. `RowVersion`, geri gönder değeri bağlayan için eklenmelidir.

### <a name="test-concurrency-conflicts-with-the-delete-page"></a>Silme sayfasıyla eşzamanlılık çakışmalarını test etme

Test departmanı oluşturun.

Test departmanı üzerinde silmenin iki tarayıcı örneğini açın:

* Uygulamayı çalıştırın ve Departmanlar ' ı seçin.
* Test departmanı için **silme** köprüsünü sağ tıklayın ve **Yeni sekmede aç**' ı seçin.
* Test departmanı için **düzenleme** köprüsüne tıklayın.

İki tarayıcı sekmesi aynı bilgileri görüntüler.

İlk tarayıcı sekmesinde bütçeyi değiştirin ve **Kaydet**' e tıklayın.

Tarayıcı, değiştirilen değeri olan dizin sayfasını gösterir ve rowVersion göstergesi güncellenir. Güncelleştirilmiş ROWVERSION göstergesinin, diğer sekmede ikinci geri göndermede görüntülendiğini aklınızda edin.

İkinci sekmeden test departmanını silin. Bir eşzamanlılık hatası, VERITABANıNDAKI geçerli değerlerle birlikte görüntülenir. **Sil** ' i tıklatmak, güncelleştirilmemiş olmadığı takdirde varlığı siler `RowVersion` .

Veri modelinin nasıl devralınacağını öğrenmek için bkz. [Devralma](xref:data/ef-mvc/inheritance) .

### <a name="additional-resources"></a>Ek kaynaklar

* [EF Core eşzamanlılık belirteçleri](/ef/core/modeling/concurrency)
* [EF Core eşzamanlılık işle](/ef/core/saving/concurrency)
* [Bu öğreticinin YouTube sürümü (eşzamanlılık çakışmalarını Işleme)](https://youtu.be/EosxHTFgYps)
* [Bu öğreticinin YouTube sürümü (Bölüm 2)](https://www.youtube.com/watch?v=kcxERLnaGO0)
* [Bu öğreticinin YouTube sürümü (Bölüm 3)](https://www.youtube.com/watch?v=d4RbpfvELRs)

> [!div class="step-by-step"]
> [Önceki](xref:data/ef-rp/update-related-data)

::: moniker-end

