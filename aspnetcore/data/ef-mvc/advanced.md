---
title: 'Öğretici: Gelişmiş senaryolar hakkında bilgi edinin-EF Core ASP.NET MVC'
description: Bu öğreticide, Entity Framework Core kullanan ASP.NET Core Web uygulamaları geliştirmeye yönelik temel bilgilerin ötesinde yararlı konular sunulmaktadır.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/advanced
ms.openlocfilehash: 9f02165f54d3cd3328496710dc92ebc86c4640d6
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626837"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a>Öğretici: Gelişmiş senaryolar hakkında bilgi edinin-EF Core ASP.NET MVC

Önceki öğreticide, tablo başına devralma devralmayı uyguladık. Bu öğreticide, Entity Framework Core kullanan ASP.NET Core Web uygulamaları geliştirme hakkında temel bilgileri aşdığınızda dikkat etmeniz yararlı olan birkaç konu sunulmaktadır.

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Ham SQL sorguları gerçekleştirme
> * Varlıkları döndürmek için bir sorgu çağırın
> * Başka türler döndürmek için bir sorgu çağırın
> * Güncelleştirme sorgusu çağırma
> * SQL sorgularını inceleyin
> * Soyutlama katmanı oluşturma
> * Otomatik değişiklik algılama hakkında bilgi edinin
> * EF Core kaynak kodu ve geliştirme planları hakkında bilgi edinin
> * Kodu basitleştirmek için dinamik LINQ kullanmayı öğrenin

## <a name="prerequisites"></a>Ön koşullar

* [Devralmayı Uygula](inheritance.md)

## <a name="perform-raw-sql-queries"></a>Ham SQL sorguları gerçekleştirme

Entity Framework kullanmanın avantajlarından biri, kodunuzun veri depolarken belirli bir yönteme çok benzemesidir. Bunu sizin için SQL sorguları ve komutları oluşturarak yapar, bu da sizi kendiniz yazmak zorunda kalmaktan kurtarır. Ancak el ile oluşturduğunuz belirli SQL sorgularını çalıştırmanız gerektiğinde olağanüstü senaryolar vardır. Bu senaryolar için Entity Framework Code First API 'SI, SQL komutlarını doğrudan veritabanına geçirmenize olanak sağlayan yöntemleri içerir. EF Core 1,0 ' de aşağıdaki seçenekleriniz vardır:

* `DbSet.FromSql`Varlık türleri döndüren sorgular için yöntemini kullanın. Döndürülen nesneler nesne tarafından beklenen türde olmalıdır `DbSet` ve [izlemeyi kapatmadığınız](crud.md#no-tracking-queries)takdirde veritabanı bağlamı tarafından otomatik olarak izlenir.

* `Database.ExecuteSqlCommand`Sorgu olmayan komutları için kullanın.

Varlık olmayan türleri döndüren bir sorgu çalıştırmanız gerekiyorsa, EF tarafından sunulan veritabanı bağlantısıyla ADO.NET kullanabilirsiniz. Döndürülen veriler, varlık türlerini almak için bu yöntemi kullanıyor olsanız bile veritabanı bağlamı tarafından izlenmez.

Her zaman doğru olduğu gibi, bir Web uygulamasında SQL komutları yürüttüğünüzde, sitenizi SQL ekleme saldırılarına karşı korumak için önlemler almalısınız. Bunu yapmanın bir yolu, bir Web sayfası tarafından gönderilen dizelerin SQL komutları olarak yorumlanamadığından emin olmak için parametreli sorgular kullanmaktır. Bu öğreticide Kullanıcı girişini bir sorguyla tümleştirdiğinizde parametreli sorgular kullanacaksınız.

## <a name="call-a-query-to-return-entities"></a>Varlıkları döndürmek için bir sorgu çağırın

`DbSet<TEntity>`Sınıfı, türünde bir varlık döndüren bir sorguyu yürütmek için kullanabileceğiniz bir yöntem sağlar `TEntity` . Bunun nasıl çalıştığını görmek için, `Details` bölüm denetleyicisinin yöntemindeki kodu değiştirirsiniz.

*DepartmentsController.cs*' de, `Details` yönteminde, `FromSql` aşağıdaki vurgulanmış kodda gösterildiği gibi, bir departmanı alan kodu bir yöntem çağrısıyla değiştirin:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

Yeni kodun doğru şekilde çalıştığını doğrulamak için **Departmanlar** sekmesini seçin ve sonra departmanlardan birine ilişkin **ayrıntıları** izleyin.

![Departman ayrıntıları](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a>Başka türler döndürmek için bir sorgu çağırın

Daha önce, yaklaşık bir kayıt tarihi için öğrenci sayısını gösteren hakkında sayfasında bir öğrenci istatistikleri Kılavuzu oluşturdunuz. Öğrenciler varlık kümesindeki () verileri aldınız `_context.Students` ve sonuçları `EnrollmentDateGroup` görüntüleme modeli nesneleri listesine eklemek için LINQ 'ı kullandınız. LINQ kullanmak yerine SQL 'in kendisini yazmak istediğinizi varsayalım. Bunu yapmak için, varlık nesnelerinden başka bir şey döndüren bir SQL sorgusu çalıştırmanız gerekir. EF Core 1,0 ' de, bunu yapmanın bir yolu ADO.NET kodunu yazıp EF 'ten veritabanı bağlantısı almanızı sağlar.

*HomeController.cs*içinde, `About` yöntemini aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

Using deyimleri ekleme:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

Uygulamayı çalıştırın ve hakkında sayfasına gidin. Daha önce yaptığımız verileri görüntüler.

![Sayfa hakkında](advanced/_static/about.png)

## <a name="call-an-update-query"></a>Güncelleştirme sorgusu çağırma

Contoso Üniversitesi yöneticilerinin veritabanında, her kurs için kredi sayısını değiştirme gibi genel değişiklikleri gerçekleştirmesini istediğini varsayalım. Üniversitenin çok sayıda kursu varsa, bunların tümünü varlıklar olarak almak ve tek tek değiştirmek verimsiz olur. Bu bölümde, kullanıcının tüm kurslar için kredi sayısını değiştirecek bir faktör belirtmesini sağlayan bir Web sayfası uygulayacaksınız ve bir SQL UPDATE ifadesini yürüterek değişikliği yaparsınız. Web sayfası aşağıdaki çizimde gösterildiği gibi görünür:

![Kurs kredileri sayfasını Güncelleştir](advanced/_static/update-credits.png)

*CoursesController.cs*' de, HttpGet ve HttpPost için UpdateCourseCredits yöntemleri ekleyin:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

Denetleyici bir HttpGet isteğini işlediğinde, içinde hiçbir şey döndürülmez `ViewData["RowsAffected"]` ve görünüm, önceki çizimde gösterildiği gibi boş bir metin kutusu ve bir Gönder düğmesi görüntüler.

**Update** düğmesine tıklandığında, HttpPost yöntemi çağırılır ve Multiplier metin kutusuna girilen değer vardır. Kod daha sonra, kursu güncelleştiren SQL 'i yürütür ve etkilenen satır sayısını içinde görünüme döndürür `ViewData` . Görünüm bir `RowsAffected` değer aldığında, güncelleştirilmiş satır sayısını görüntüler.

**Çözüm Gezgini**, *Görünümler/kurslar* klasörüne sağ tıklayın ve ardından **> yeni öğe Ekle**' ye tıklayın.

**Yeni öğe Ekle** iletişim kutusunda sol bölmede yüklü **ASP.NET Core** ' **Installed** a tıklayın, ** Razor görüntüle**' ye tıklayın ve yeni görünüm *UpdateCourseCredits. cshtml*olarak adlandırın.

*Views/kurslar/UpdateCourseCredits. cshtml*içinde, şablon kodunu şu kodla değiştirin:

[!code-cshtml[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

`UpdateCourseCredits` **Kurs** sekmesini seçerek yöntemi çalıştırın, sonra TARAYıCıNıN adres çubuğundaki URL 'nin sonuna "/UpdateCourseCredits" ekleyin (örneğin: `http://localhost:5813/Courses/UpdateCourseCredits` ). Metin kutusuna bir sayı girin:

![Kurs kredileri sayfasını Güncelleştir](advanced/_static/update-credits.png)

**Güncelleştir**’e tıklayın. Etkilenen satır sayısını görürsünüz:

![Güncelleştirme kursu kredileri sayfa satırları etkilendi](advanced/_static/update-credits-rows-affected.png)

Düzeltilen kredi sayısına sahip kurslar listesini görmek için **listeye geri** ' ye tıklayın.

Üretim kodunun güncelleştirmelerin her zaman geçerli verilerle sonuçlandığına emin olun. Burada gösterilen basitleştirilmiş kod, 5 ' ten fazla sayı ile sonuçlanacak kredilerin sayısını çarpamaz. ( `Credits` Özelliğin bir özniteliği vardır `[Range(0, 5)]` .) Güncelleştirme sorgusu çalışır, ancak geçersiz veriler sistemin diğer bölümlerinde, kredi sayısının 5 veya daha az olduğunu varsayacak beklenmedik sonuçlara neden olabilir.

Ham SQL sorguları hakkında daha fazla bilgi için bkz. [Ham SQL sorguları](/ef/core/querying/raw-sql).

## <a name="examine-sql-queries"></a>SQL sorgularını inceleyin

Bazen veritabanına gönderilen gerçek SQL sorgularını görmeniz yararlı olabilir. ASP.NET Core için yerleşik günlük işlevselliği, sorgular ve güncelleştirmeler için SQL içeren günlükleri yazmak üzere EF Core tarafından otomatik olarak kullanılır. Bu bölümde, SQL günlüğe kaydetme işleminin bazı örneklerini görürsünüz.

*StudentsController.cs* ' i açın ve `Details` yöntemi içinde, bildiriminde bir kesme noktası ayarlayın `if (student == null)` .

Uygulamayı hata ayıklama modunda çalıştırın ve bir öğrenci için ayrıntılar sayfasına gidin.

Hata ayıklama çıkışını gösteren **Çıkış** penceresine gidin ve sorguyu görürsünüz:

```
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (56ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT TOP(2) [s].[ID], [s].[Discriminator], [s].[FirstName], [s].[LastName], [s].[EnrollmentDate]
FROM [Person] AS [s]
WHERE ([s].[Discriminator] = N'Student') AND ([s].[ID] = @__id_0)
ORDER BY [s].[ID]
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (122ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT [s.Enrollments].[EnrollmentID], [s.Enrollments].[CourseID], [s.Enrollments].[Grade], [s.Enrollments].[StudentID], [e.Course].[CourseID], [e.Course].[Credits], [e.Course].[DepartmentID], [e.Course].[Title]
FROM [Enrollment] AS [s.Enrollments]
INNER JOIN [Course] AS [e.Course] ON [s.Enrollments].[CourseID] = [e.Course].[CourseID]
INNER JOIN (
    SELECT TOP(1) [s0].[ID]
    FROM [Person] AS [s0]
    WHERE ([s0].[Discriminator] = N'Student') AND ([s0].[ID] = @__id_0)
    ORDER BY [s0].[ID]
) AS [t] ON [s.Enrollments].[StudentID] = [t].[ID]
ORDER BY [t].[ID]
```

Size beklenmedik bir şekilde bir sorun olduğunu fark edeceksiniz: SQL, kişi tablosundan 2 ' ye kadar satır ( `TOP(2)` ) seçer. `SingleOrDefaultAsync`Yöntem, sunucuda 1 satıra çözümlenmiyor. Nedeni:

* Sorgu birden çok satır döndürürse, yöntemi null döndürür.
* Sorgunun birden çok satır döndürüp döndürmeyeceğini anlamak için EF 'in en az 2 değerini döndürüp döndürmediğini denetlemesi gerekir.

**Çıkış** penceresinde günlüğe kaydetme çıktısını almak için hata ayıklama modunu kullanmanız ve bir kesme noktasında durdurmanız gerekmediğini unutmayın. Bu, çıkışa bakmak istediğiniz noktada günlüğe kaydetmeyi durdurmak için kullanışlı bir yoldur. Bunu yapmazsanız, günlüğe kaydetme devam eder ve ilgilendiğiniz parçaları bulmak için geri kaydırmanız gerekir.

## <a name="create-an-abstraction-layer"></a>Soyutlama katmanı oluşturma

Birçok geliştirici, Entity Framework ile çalışan kodun etrafında bir sarmalayıcı olarak depo ve iş birimi düzenlerini uygulamak için kod yazar. Bu desenler, veri erişim katmanı ve bir uygulamanın iş mantığı katmanı arasında bir soyutlama katmanı oluşturmak için tasarlanmıştır. Bu desenleri uygulamak, uygulamanızın veri deposundaki değişikliklerden yalıtılmış hale getirmenize yardımcı olabilir ve otomatik birim testi veya test odaklı geliştirmeyi (TDD) kolaylaştırabilir. Ancak, bu desenleri uygulamak için ek kod yazmak, birkaç nedenden dolayı EF kullanan uygulamalar için her zaman en iyi seçimdir:

* EF bağlam sınıfının kendisi, veri deposuna özgü koddan kodunuzun kendisini uygular.

* EF bağlam sınıfı, EF kullanarak yaptığınız veritabanı güncelleştirmeleri için bir iş birimi sınıfı işlevi görebilir.

* EF, depo kodu yazmadan TDD uygulamaya yönelik özellikler içerir.

Deponun ve iş düzeni birimlerinin nasıl uygulanacağı hakkında bilgi için, [Bu öğretici serisinin Entity Framework 5 sürümüne](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application)bakın.

Entity Framework Core, test için kullanılabilecek bir bellek içi veritabanı sağlayıcısı uygular. Daha fazla bilgi için bkz. [InMemory Ile test](/ef/core/miscellaneous/testing/in-memory)etme.

## <a name="automatic-change-detection"></a>Otomatik değişiklik algılama

Entity Framework bir varlığın geçerli değerlerini özgün değerlerle karşılaştırarak bir varlığın nasıl değiştiğini (ve bu nedenle veritabanına gönderilmesi gereken güncelleştirmeleri) belirler. Özgün değerler, varlık sorgulandığında veya eklendiğinde saklanır. Otomatik değişiklik algılamaya neden olan yöntemlerin bazıları şunlardır:

* DbContext. SaveChanges

* DbContext. Entry

* ChangeTracker. Entries

Çok sayıda varlığı izliyorsanız ve bu yöntemlerden birini bir döngüde birçok kez çağırırsanız, özelliği kullanarak otomatik değişiklik algılamayı geçici olarak kapatarak önemli performans iyileştirmeleri alabilirsiniz `ChangeTracker.AutoDetectChangesEnabled` . Örnek:

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a>EF Core kaynak kodu ve geliştirme planları

Entity Framework Core kaynağı [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore) . EF Core deposu gecelik derlemeler, sorun izleme, özellik özellikleri, tasarım toplantısı notları ve [ileride geliştirmeye yönelik yol haritasını](https://github.com/dotnet/efcore/wiki/Roadmap)içerir. Hataları dosyalayabilirsiniz veya bulabilir ve katkıda bulunabilirsiniz.

Kaynak kodu açık olsa da Entity Framework Core, Microsoft ürünü olarak tam olarak desteklenmektedir. Microsoft Entity Framework ekibi, her bir yayının kalitesini sağlamak için, hangi katkıların kabul edildiğini denetler ve tüm kod değişikliklerini sınar.

## <a name="reverse-engineer-from-existing-database"></a>Mevcut veritabanından ters mühendislik

Mevcut bir veritabanından varlık sınıfları dahil bir veri modeline ters mühendislik uygulamak için [Scaffold-DbContext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) komutunu kullanın. Bkz. Başlangıç [öğreticisi](/ef/core/get-started/aspnetcore/existing-db).

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a>Kodu basitleştirmek için dinamik LINQ kullanma

[Bu serideki üçüncü öğretici](sort-filter-page.md) , bir bildirimde sabit kodlayarak sütun adları aracılığıyla LINQ kodunun nasıl yazılacağını gösterir `switch` . Arasından seçim yapabileceğiniz iki sütun varsa, bu sorunsuz bir şekilde yapılır, ancak çok sayıda sütununuzla karşılaşırsanız, kod ayrıntılı alabilir. Bu sorunu çözmek için `EF.Property` yöntemini kullanarak özelliğin adını bir dize olarak belirtebilirsiniz. Bu yaklaşımı denemek için `Index` , içindeki yöntemini `StudentsController` aşağıdaki kodla değiştirin.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a>Teşekkürler

Tom Dykstra ve Rick Anderson (Twitter @RickAndMSFT ) bu öğreticiyi yazdı. ROWA Miller, Diego Vega ve kod incelemeleri ile Entity Framework ekip yardımlı diğer üyeleri ve öğreticiler için kod yazarken oluşan sorunları ayıkladık. John Parente ve Paul Goldman, 2,2 ASP.NET Core öğreticisini güncelleştirmeye çalıştı.

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a>Sık karşılaşılan hataları giderme

### <a name="contosouniversitydll-used-by-another-process"></a>Başka bir işlem tarafından kullanılan ContosoUniversity.dll

Hata iletisi:

> ' ...bin\Debug\netcoreapp1.0\ContosoUniversity.dll ' yazma için açılamıyor--' başka bir işlem tarafından kullanıldığı için işlem ' ...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll ' dosyasına erişemiyor.

Çözüm:

IIS Express sitesini durdurun. Windows sistemi tepsisine IIS Express bulun ve simgesine sağ tıklayın, Contoso Üniversitesi sitesini seçin ve ardından **siteyi durdur**' a tıklayın.

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a>Yukarı ve aşağı metotlarda kod olmadan geçiş yapı iskelesi

Olası neden:

EF CLı komutları, kod dosyalarını otomatik olarak kapatmaz ve kaydetmez. Komutu çalıştırdığınızda kaydetmediğiniz değişiklikler varsa `migrations add` , EF değişikliklerinizi bulamaz.

Çözüm:

Komutunu çalıştırın `migrations remove` , kod değişikliklerinizi kaydedin ve komutu yeniden çalıştırın `migrations add` .

### <a name="errors-while-running-database-update"></a>Veritabanı güncelleştirmesi çalıştırılırken hatalar oluştu

Varolan verileri içeren bir veritabanında şema değişiklikleri yaparken başka hatalar almak mümkündür. Çözümleyemez geçiş hataları alırsanız, bağlantı dizesindeki veritabanı adını değiştirebilir veya veritabanını silebilirsiniz. Yeni bir veritabanı ile geçirilecek veri yoktur ve Update-Database komutunun hatasız tamamlanabilmesi çok daha yüksektir.

En basit yaklaşım, * üzerindeappsettings.js*veritabanının yeniden adlandırılmasına. Bir sonraki sefer çalıştırdığınızda `database update` Yeni bir veritabanı oluşturulur.

SSOX 'te bir veritabanını silmek için veritabanına sağ tıklayın, **Sil**' e tıklayın ve ardından **veritabanını sil** Iletişim kutusunda **varolan bağlantıları kapat** ' ı seçin ve **Tamam**' a tıklayın.

CLı kullanarak bir veritabanını silmek için `database drop` CLI komutunu çalıştırın:

```dotnetcli
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a>SQL Server örneği bulunurken hata oluştu

Hata İletisi:

> SQL Server ile bağlantı kurulmaya çalışılırken ağ ile ilişkili veya örneğe özgü bir hata oluştu. Sunucu bulunamadı veya erişilebilir değildi. Örnek adının doğru olduğundan ve SQL Server uzak bağlantılara izin verecek şekilde yapılandırıldığından emin olun. (sağlayıcı: SQL ağ arabirimleri, hata: 26-belirtilen sunucu/örnek bulunurken hata oluştu)

Çözüm:

Bağlantı dizesini denetleyin. Veritabanı dosyasını el ile sildiyseniz, oluşturma dizesindeki veritabanının adını yeni bir veritabanı ile başlatılacak şekilde değiştirin.

## <a name="get-the-code"></a>Kodu alma

[Tamamlanmış uygulamayı indirin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Ek kaynaklar

EF Core hakkında daha fazla bilgi için [Entity Framework Core belgelerine](/ef/core)bakın. Bir kitap da kullanılabilir: [Entity Framework Core eylem](https://www.manning.com/books/entity-framework-core-in-action).

Bir Web uygulamasının nasıl dağıtılacağı hakkında bilgi için bkz <xref:host-and-deploy/index> ..

Kimlik doğrulama ve yetkilendirme gibi ASP.NET Core MVC ile ilgili diğer konular hakkında daha fazla bilgi için bkz <xref:index> ..

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Ham SQL sorguları gerçekleştiriliyor
> * Varlıkları döndürmek için sorgu çağrıldı
> * Diğer türleri döndürmek için sorgu çağrıldı
> * Bir güncelleştirme sorgusu çağrıldı
> * İncelenen SQL sorguları
> * Soyutlama katmanı oluşturma
> * Otomatik değişiklik algılama hakkında bilgi edinildi
> * EF Core kaynak kodu ve geliştirme planları hakkında bilgi edinildi
> * Kodu basitleştirmek için dinamik LINQ kullanımı öğrenildi

Bu, ASP.NET Core MVC uygulamasında Entity Framework Core kullanımı hakkında bu öğretici serisini tamamlar. Bu seri yeni bir veritabanıyla çalıştı; bir alternatif, mevcut bir veritabanından bir modele tersine mühendislik kullanmaktır.

> [!div class="nextstepaction"]
> [Öğretici: MVC ile EF Core, var olan veritabanı](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
