---
title: 'Öğretici: Gelişmiş senaryolar hakkında bilgi edinin - EF Core ile mvc ASP.NET'
description: Bu öğretici, Entity Framework Core'u kullanan ASP.NET Core web uygulamaları geliştirmenin temellerinin ötesine geçerek yararlı konular sunar.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/advanced
ms.openlocfilehash: fc6f8d8c4ab09848cf316be2e522bf5ce3b9ac76
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79416235"
---
# <a name="tutorial-learn-about-advanced-scenarios---aspnet-mvc-with-ef-core"></a>Öğretici: Gelişmiş senaryolar hakkında bilgi edinin - EF Core ile mvc ASP.NET

Önceki öğreticide, hiyerarşi başına tablo kalıtımını uyguladınız. Bu öğretici, Entity Framework Core'u kullanan ASP.NET Temel web uygulamaları geliştirmetemellerinin ötesine geçtiğinde farkında olmak için yararlı olan çeşitli konuları tanıtır.

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Ham SQL sorguları gerçekleştirin
> * Varlıkları döndürmek için sorgu çağırma
> * Diğer türleri döndürmek için sorgu çağırma
> * Güncelleştirme sorgusu çağırma
> * SQL sorgularını inceleme
> * Soyutlama katmanı oluşturma
> * Otomatik değişiklik algılama hakkında bilgi edinin
> * EF Core kaynak kodu ve geliştirme planları hakkında bilgi edinin
> * Kodu basitleştirmek için dinamik LINQ'yi nasıl kullanacağınızı öğrenin

## <a name="prerequisites"></a>Ön koşullar

* [Kalıtım Uygula](inheritance.md)

## <a name="perform-raw-sql-queries"></a>Ham SQL sorguları gerçekleştirin

Varlık Çerçevesi'ni kullanmanın avantajlarından biri, kodunuzu belirli bir veri depolama yöntemine çok yakın bağlamaktan kaçınmasıdır. Bunu sizin için SQL sorguları ve komutları oluşturarak yapar, bu da onları kendiniz yazmak zorunda sizi kurtarır. Ancak, el ile oluşturduğunuz belirli SQL sorgularını çalıştırmanız gerektiğinde olağanüstü senaryolar vardır. Bu senaryolar için Varlık Çerçeve Kodu İlk API, SQL komutlarını doğrudan veritabanına geçirmenizi sağlayan yöntemler içerir. EF Core 1.0'da aşağıdaki seçeneklere sahipsiniz:

* Varlık `DbSet.FromSql` türlerini döndüren sorgular için yöntemi kullanın. Döndürülen nesneler `DbSet` nesne tarafından beklenen türden olmalıdır ve siz izlemeyi [kapatmadığınız](crud.md#no-tracking-queries)sürece veritabanı bağlamı tarafından otomatik olarak izlenirler.

* Sorgu `Database.ExecuteSqlCommand` olmayan komutlar için kullanın.

Varlık olmayan türleri döndüren bir sorgu çalıştırmanız gerekiyorsa, EF tarafından sağlanan veritabanı bağlantısıyla ADO.NET kullanabilirsiniz. Döndürülen veriler, varlık türlerini almak için bu yöntemi kullansanız bile veritabanı bağlamı tarafından izlenmez.

Bir web uygulamasında SQL komutlarını uyguladığınızda her zaman olduğu gibi, sitenizi SQL enjeksiyon saldırılarına karşı korumak için önlemler almanız gerekir. Bunu yapmanın bir yolu, bir web sayfası tarafından gönderilen dizelerin SQL komutları olarak yorumlanamayacağından emin olmak için parametreli sorgular kullanmaktır. Bu öğreticide, kullanıcı girişini bir sorguya tümleştirerken parametreli sorgular kullanırsınız.

## <a name="call-a-query-to-return-entities"></a>Varlıkları döndürmek için sorgu çağırma

Sınıf, `DbSet<TEntity>` türünden `TEntity`bir varlık döndüren bir sorgu yürütmek için kullanabileceğiniz bir yöntem sağlar. Bunun nasıl çalıştığını görmek için Bölüm denetleyicisi yöntemindeki `Details` kodu değiştirirsiniz.

*DepartmentsController.cs,* `Details` yöntemde, aşağıdaki vurgulanan kodda gösterildiği `FromSql` gibi, bir bölümü bir yöntem çağrısıyla alan kodu değiştirin:

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10)]

Yeni kodun doğru çalıştığını doğrulamak için **Bölümler** sekmesini ve bölümlerden birinin **Ayrıntıları'nı** seçin.

![Bölüm Detayları](advanced/_static/department-details.png)

## <a name="call-a-query-to-return-other-types"></a>Diğer türleri döndürmek için sorgu çağırma

Daha önce, her kayıt tarihi için öğrenci sayısını gösteren Hakkında sayfası için bir öğrenci istatistikleri tablosu oluşturdunuz. Verileri Öğrenciler varlık kümesinden aldınız`_context.Students`ve sonuçları `EnrollmentDateGroup` görünüm modeli nesneleri listesine yansıtmak için LINQ'yi kullandınız. LINQ kullanmak yerine SQL'in kendisini yazmak istediğinizi varsayalım. Bunu yapmak için varlık nesneleri dışında bir şey döndüren bir SQL sorgusu çalıştırmak gerekir. EF Core 1.0'da bunu yapmanın bir yolu ADO.NET kodu yazmak ve veritabanı bağlantısını EF'den almaktır.

*HomeController.cs*olarak, `About` yöntemi aşağıdaki kodla değiştirin:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

Kullanarak ifade ekle:

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

Uygulamayı çalıştırın ve Hakkında sayfasına gidin. Daha önce yaptığı verileri görüntüler.

![Sayfa hakkında](advanced/_static/about.png)

## <a name="call-an-update-query"></a>Güncelleştirme sorgusu çağırma

Contoso Üniversitesi yöneticilerinin veritabanında her ders için kredi sayısını değiştirmek gibi genel değişiklikler gerçekleştirmek istediklerini varsayalım. Üniversitenin çok sayıda dersi varsa, hepsini kurum olarak alıp tek tek değiştirmek verimsiz olacaktır. Bu bölümde, kullanıcının tüm derslerin kredi sayısını değiştirmek için bir faktör belirtmesini sağlayan bir web sayfası uygularsınız ve bir SQL UPDATE bildirimi uygulayarak değişikliği yaparsınız. Web sayfası aşağıdaki çizimgibi görünecektir:

![Ders Kredileri sayfasını güncelle](advanced/_static/update-credits.png)

*CoursesController.cs*yılında, HttpGet ve HttpPost için UpdateCourseCredits yöntemleri ekleyin:

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

Denetleyici bir HttpGet isteğini işlediğinde, `ViewData["RowsAffected"]`hiçbir şey döndürülür ve görünüm önceki resimde gösterildiği gibi boş bir metin kutusu ve gönder düğmesi görüntüler.

**Güncelleştirme** düğmesi tıklandığında, HttpPost yöntemi çağrılır ve çarpan metin kutusuna girilen değere sahiptir. Kod daha sonra dersleri güncelleyen ve etkilenen satır sayısını görünüme `ViewData`döndüren SQL'i yürütür. Görünüm bir `RowsAffected` değer aldığında, güncelleştirilen satır sayısını görüntüler.

**Çözüm Gezgini'nde** *Görünümler/Kurslar* klasörüne sağ tıklayın ve ardından **Yeni Öğe > ekle'yi**tıklatın.

Yeni **Öğe Ekle** iletişim kutusunda, sol bölmede **Yüklü** ASP.NET **Core'u** tıklatın, **Razor View'ı**tıklatın ve yeni görünümü *UpdateCourseCredits.cshtml*adını alın.

*Görünümler/Kurslar/UpdateCourseCredits.cshtml'de*şablon kodunu aşağıdaki kodla değiştirin:

[!code-html[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

`UpdateCourseCredits` **Kurslar** sekmesini seçerek yöntemi çalıştırın ve ardından tarayıcının adres çubuğundaki URL'nin sonuna "/UpdateCourseCredits" ekleyerek (örneğin: `http://localhost:5813/Courses/UpdateCourseCredits`). Metin kutusuna bir numara girin:

![Ders Kredileri sayfasını güncelle](advanced/_static/update-credits.png)

**Güncelleştir**’e tıklayın. Etkilenen satır sayısını görürsünüz:

![Etkilenen Ders Kredileri sayfa satırlarını güncelleştirme](advanced/_static/update-credits-rows-affected.png)

Kredi sayısı revize edilmiş derslerin listesini görmek için **Listeye Geri Dön'ü** tıklayın.

Üretim kodunun güncelleştirmeleri her zaman geçerli verilerle sonuçlandırmasını sağlayacağını unutmayın. Burada gösterilen basitleştirilmiş kod, 5'ten büyük sayılarla sonuçlanmasıiçin yeterli olan kredi sayısını çarpabilir. (Özelliğin `Credits` bir `[Range(0, 5)]` özelliği vardır.) Güncelleştirme sorgusu çalışır, ancak geçersiz veriler, kredi sayısının 5 veya daha az olduğunu varsayan sistemin diğer bölümlerinde beklenmeyen sonuçlara neden olabilir.

Ham SQL sorguları hakkında daha fazla bilgi için [Raw SQL Sorguları'na](/ef/core/querying/raw-sql)bakın.

## <a name="examine-sql-queries"></a>SQL sorgularını inceleme

Bazen veritabanına gönderilen gerçek SQL sorgularını görebilmek yararlı olabilir. ASP.NET Core için yerleşik günlük işlevi, EF Core tarafından sorgular ve güncelleştirmeler için SQL içeren günlükleri yazmak için otomatik olarak kullanılır. Bu bölümde SQL günlük bazı örnekler görürsünüz.

*Açık StudentsController.cs* ve `Details` yöntemde `if (student == null)` deyimi bir kesme noktası ayarlayın.

Uygulamayı hata ayıklama modunda çalıştırın ve bir öğrencinin Ayrıntılar sayfasına gidin.

Hata ayıklama çıktısını gösteren **Çıktı** penceresine gidin ve sorguyu görürsünüz:

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

Burada sizi şaşırtabilecek bir şey fark edeceksiniz: SQL Kişi`TOP(2)`tablosundan en fazla 2 satır ( ) seçer. Yöntem, `SingleOrDefaultAsync` sunucuda 1 satıra çözülmüyor. Nedeni:

* Sorgu birden çok satır döndürürse, yöntem null döndürür.
* Sorgunun birden çok satır döndürüp döndürmeyeceğini belirlemek için, EF'nin en az 2 satır döndürüp döndürmediğini denetlemesi gerekiyor.

**Çıktı** penceresinde günlüğe kaydetme çıktısı almak için hata ayıklama modunu kullanmanız ve kesme noktasında durmanız gerekmediğini unutmayın. Bu, çıkışa bakmak istediğiniz noktada günlüğe kaydetmeyi durdurmanın kullanışlı bir yoludur. Bunu yapmazsanız, günlüğe kaydetme devam eder ve ilgilendiğiniz parçaları bulmak için geri kaydırmanız gerekir.

## <a name="create-an-abstraction-layer"></a>Soyutlama katmanı oluşturma

Birçok geliştirici, Varlık Çerçevesi ile çalışan kod etrafında bir sarmalayıcı olarak çalışma kalıplarının deposunu ve birimini uygulamak için kod yazar. Bu desenler, veri erişim katmanı ile uygulamanın iş mantığı katmanı arasında bir soyutlama katmanı oluşturmak için tasarlanmıştır. Bu desenlerin uygulanması, uygulamanızın veri deposundaki değişikliklerden izole edilmesine yardımcı olabilir ve otomatik birim testini veya test odaklı geliştirmeyi (TDD) kolaylaştırabilir. Ancak, bu desenleri uygulamak için ek kod yazmak, çeşitli nedenlerden dolayı EF kullanan uygulamalar için her zaman en iyi seçim değildir:

* EF bağlam sınıfının kendisi kodunuzu veri deposuna özgü koddan yalıtır.

* EF bağlam sınıfı, EF kullanarak yaptığınız veritabanı güncelleştirmeleri için bir çalışma birimi sınıfı olarak hareket edebilir.

* EF, depo kodu yazmadan TDD'yi uygulamak için özellikler içerir.

Çalışma kalıplarının deposu ve birimi hakkında bilgi için [bu öğretici serinin Entity Framework 5 sürümüne](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application)bakın.

Varlık Framework Core, sınama için kullanılabilecek bir bellek içi veritabanı sağlayıcısı uygular. Daha fazla bilgi için [InMemory ile Sınama'ya](/ef/core/miscellaneous/testing/in-memory)bakın.

## <a name="automatic-change-detection"></a>Otomatik değişiklik algılama

Varlık Çerçevesi, bir varlığın geçerli değerlerini özgün değerlerle karşılaştırarak bir varlığın nasıl değiştiğini (ve bu nedenle hangi güncelleştirmelerin veritabanına gönderilmesi gerektiğini) belirler. Varlık sorgulandığında veya eklendiğinde özgün değerler depolanır. Otomatik değişiklik algılamaya neden olan yöntemlerden bazıları şunlardır:

* DbContext.SaveChanges

* DbContext.Entry

* ChangeTracker.Girişleri

Çok sayıda varlığı takip ediyorsanız ve bu yöntemlerden birini döngü içinde birçok kez arıyorsanız, `ChangeTracker.AutoDetectChangesEnabled` özelliği kullanarak otomatik değiştirme algılamasını geçici olarak kapatarak önemli performans iyileştirmeleri elde edebilirsiniz. Örneğin:

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="ef-core-source-code-and-development-plans"></a>EF Çekirdek kaynak kodu ve geliştirme planları

Varlık Çerçeve Çekirdek kaynağı [https://github.com/dotnet/efcore](https://github.com/dotnet/efcore). EF Core deposu, gece yapılarını, sorun izlemeyi, özellik özelliklerini, tasarım toplantı notlarını ve [gelecekteki gelişim için yol haritasını](https://github.com/dotnet/efcore/wiki/Roadmap)içerir. Dosyalayabilir veya hataları bulabilir ve katkıda bulunabilirsiniz.

Kaynak kodu açık olmasına rağmen, Entity Framework Core bir Microsoft ürünü olarak tam olarak desteklenir. Microsoft Entity Framework ekibi, hangi katkıların kabul edildiği üzerinde denetim tutar ve her sürümün kalitesini sağlamak için tüm kod değişikliklerini sınar.

## <a name="reverse-engineer-from-existing-database"></a>Varolan veritabanından ters mühendis

Varolan bir veritabanındaki varlık sınıflarını içeren bir veri modelini tersine çevirmek için [iskele-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) komutunu kullanın. Başlangıç [öğreticisini](/ef/core/get-started/aspnetcore/existing-db)görün.

<a id="dynamic-linq"></a>

## <a name="use-dynamic-linq-to-simplify-code"></a>Kodu basitleştirmek için dinamik LINQ'yi kullanın

[Bu serinin üçüncü öğretici](sort-filter-page.md) nasıl bir `switch` deyim de sert kodlama sütun adlarıyla LINQ kodu yazmak için gösterir. Aralarından seçim yapabileceğiniz iki sütunla, bu iyi çalışır, ancak çok sayıda sütununuzun varsa kod ayrıntılı olabilir. Bu sorunu çözmek için, `EF.Property` özelliğin adını dize olarak belirtmek için yöntemi kullanabilirsiniz. Bu yaklaşımı denemek için, `Index` aşağıdaki `StudentsController` kodla yöntemi değiştirin.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="acknowledgments"></a>İlgili kaynaklar

Tom Dykstra ve Rick @RickAndMSFTAnderson (twitter) bu öğretici yazdı. Rowan Miller, Diego Vega ve Entity Framework ekibinin diğer üyeleri kod incelemeleri ile yardımcı oldu ve biz öğreticiler için kod yazarken ortaya çıkan hata ayıklama sorunları yardımcı oldu. John Parente ve Paul Goldman ASP.NET Core 2.2 için öğretici güncelleme üzerinde çalıştı.

<a id="common-errors"></a>

## <a name="troubleshoot-common-errors"></a>Sık karşılaşılan hataları giderme

### <a name="contosouniversitydll-used-by-another-process"></a>ContosoUniversity.dll başka bir işlem tarafından kullanılan

Hata iletisi:

> Açılmıyor '... bin\Debug\netcoreapp1.0\ContosoUniversity.dll' yazmak için -- 'İşlem başka bir işlem tarafından kullanıldığından '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' dosyasına erişemez.

Çözüm:

Siteyi IIS Express'te durdurun. Windows Sistem Tepsisi'ne gidin, IIS Express'i bulun ve simgesine sağ tıklayın, Contoso Üniversitesi sitesini seçin ve ardından **Siteyi Durdur'u**tıklatın.

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a>Yukarı ve Aşağı yöntemlerinde kod suz geçiş

Olası neden:

EF CLI komutları kod dosyalarını otomatik olarak kapatmaz ve kaydetmez. `migrations add` Komutu çalıştırdığınızda kaydedilmemiş değişiklikleriniz varsa, EF değişikliklerinizi bulamaz.

Çözüm:

Komutu `migrations remove` çalıştırın, kod değişikliklerinizi `migrations add` kaydedin ve komutu yeniden çalıştırın.

### <a name="errors-while-running-database-update"></a>Veritabanı güncelleştirmesi çalıştırırken hatalar

Varolan verilere sahip bir veritabanında şema değişiklikleri yaparken başka hatalar da almak mümkündür. Çözemediğiniz geçiş hataları alırsanız, bağlantı dizesinde veritabanı adını değiştirebilir veya veritabanını silebilirsiniz. Yeni bir veritabanıile, geçirilen veri yoktur ve güncelleştirme veritabanı komutu hatasız olarak tamamlanma olasılığı çok daha yüksektir.

En basit yaklaşım *appsettings.json*veritabanını yeniden adlandırmaktır. Bir sonraki çalıştırdığınızda, `database update`yeni bir veritabanı oluşturulur.

SSOX'ta bir veritabanını silmek için veritabanını sağ tıklatın, **Sil'i**tıklatın ve ardından **Veritabanını Sil** iletişim kutusunda **varolan bağlantıları kapat'ı** seçin ve **Tamam'ı**tıklatın.

CLI'yi kullanarak bir veritabanını `database drop` silmek için CLI komutunu çalıştırın:

```dotnetcli
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a>SQL Server örneğini bulma hatası

Hata İletisi:

> SQL Server ile bağlantı kurulmaya çalışılırken ağ ile ilişkili veya örneğe özgü bir hata oluştu. Sunucu bulunamadı veya erişilebilir değildi. Örnek adının doğru olduğunu ve SQL Server'ın uzak bağlantılara izin verecek şekilde yapılandırıldığından doğrulayın. (sağlayıcı: SQL Ağ Arayüzleri, hata: 26 - Hata Konumlandırma Server/Instance Specified)

Çözüm:

Bağlantı dizesini kontrol edin. Veritabanı dosyasını el ile sildiyseniz, yeni bir veritabanıyla baştan başlamak için yapı dizesinde veritabanının adını değiştirin.

## <a name="get-the-code"></a>Kodu alma

[Tamamlanan uygulamayı karşıdan yükleyin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="additional-resources"></a>Ek kaynaklar

EF Core hakkında daha fazla bilgi için [Entity Framework Core belgelerine](/ef/core)bakın. Bir kitap da mevcuttur: [Varlık Çerçeve Çekirdek Eylem](https://www.manning.com/books/entity-framework-core-in-action).

Bir web uygulamasının nasıl dağıtılanöğretilen hakkında bilgi için bkz. <xref:host-and-deploy/index>

Kimlik doğrulama ve yetkilendirme gibi core MVC ASP.NET ilgili diğer <xref:index>konular hakkında bilgi için bkz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Gerçekleştirilen ham SQL sorguları
> * Varlıkları döndürmek için sorgu çağrıldı
> * Diğer türleri döndürmek için sorgu çağrıldı
> * Güncelleştirme sorgusu olarak adlandırılır
> * İncelenen SQL sorguları
> * Soyutlama katmanı oluşturma
> * Otomatik değişiklik algılama hakkında bilgi edinilmiş
> * EF Core kaynak kodu ve geliştirme planları hakkında bilgi edinilmiş
> * Kodu basitleştirmek için dinamik LINQ'nun nasıl kullanılacağını öğrendi

Bu, ASP.NET Core MVC uygulamasında Entity Framework Core'u kullanma yla ilgili bu öğretici serisini tamamlar. Bu seri yeni bir veritabanı ile çalıştı; bir alternatif varolan bir veritabanından bir model ters mühendislik etmektir.

> [!div class="nextstepaction"]
> [Öğretici: MVC ile EF Core, mevcut veritabanı](/ef/core/get-started/aspnetcore/existing-db?toc=/aspnet/core/toc.json&bc=/aspnet/core/breadcrumb/toc.json)
