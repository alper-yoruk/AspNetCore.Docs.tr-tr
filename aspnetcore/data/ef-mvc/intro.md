---
title: 'Öğretici: bir ASP.NET MVC web uygulamasında EF Core kullanmaya başlama'
description: Bu sayfa, Contoso Üniversitesi örnek EF/MVC uygulamasının nasıl oluşturulacağını açıklayan bir öğretici serisinin ilk ilkisidir "
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 11/06/2020
ms.topic: tutorial
no-loc:
- appsettings.json
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
uid: data/ef-mvc/intro
ms.openlocfilehash: c0623de3c8031b6dbb518a6d25623b55a6500af5
ms.sourcegitcommit: 8b867c4cb0c3b39bbc4d2d87815610d2ef858ae7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94703741"
---
# <a name="tutorial-get-started-with-ef-core-in-an-aspnet-mvc-web-app"></a>Öğretici: bir ASP.NET MVC web uygulamasında EF Core kullanmaya başlama

, [Tom Dykstra](https://github.com/tdykstra) ve [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

Contoso Üniversitesi örnek Web uygulaması, Entity Framework (EF) Core ve Visual Studio kullanarak ASP.NET Core MVC web uygulaması oluşturmayı gösterir.

Örnek uygulama, kurgusal bir Contoso Üniversitesi için bir Web sitesidir. Öğrenci giriş, kurs oluşturma ve eğitmen atamaları gibi işlevleri içerir. Bu, Contoso Üniversitesi örnek uygulamasının nasıl oluşturulacağını açıklayan bir öğretici serisinin ilkisidir.

## <a name="prerequisites"></a>Önkoşullar

* ASP.NET Core MVC 'yi yeni kullanmaya başladıysanız, bunu başlatmadan önce [ASP.NET Core MVC](xref:tutorials/first-mvc-app/start-mvc) öğreticisini kullanmaya başlayın.

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

## <a name="database-engines"></a>Veritabanı altyapıları

Visual Studio yönergeleri, yalnızca Windows üzerinde çalışan bir SQL Server Express sürümü olan [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)'yi kullanır.

<!--
The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.

If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).
-->

## <a name="solve-problems-and-troubleshoot"></a>Sorunları çözün ve sorun giderin

Çözemiyoruz bir sorunla karşılaşırsanız, kodunuzun [Tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples)karşılaştırılmasıyla genellikle çözümü bulabilirsiniz. Yaygın hataların bir listesi ve bunların nasıl çözüleceği için, [serideki son öğreticinin sorun giderme bölümüne](advanced.md#common-errors)bakın. İhtiyacınız olanları bulamazsanız, [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core)için bir soru gönderebilirsiniz.

> [!TIP]
> Bu, her biri daha önceki öğreticilerde gerçekleştirilen bir dizi 10 öğreticisidir. Her başarılı öğreticinin tamamlanmasından sonra projenin bir kopyasını kaydetmeyi göz önünde bulundurun. Daha sonra sorunlarla karşılaşırsanız, tüm serinin başlangıcına dönmek yerine önceki öğreticiden baştan başlayabilirsiniz.

## <a name="contoso-university-web-app"></a>Contoso Üniversitesi web uygulaması

Bu öğreticilerde oluşturulan uygulama, temel bir üniversite web sitesidir.

Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleştirebilir. Uygulamadaki ekranlardan bazıları şunlardır:

![Öğrenciler Dizin sayfası](intro/_static/students-index.png)

![Öğrenciler düzenleme sayfası](intro/_static/student-edit.png)

## <a name="create-web-app"></a>Web uygulaması oluşturma

1. Visual Studio 'Yu başlatın ve **Yeni proje oluştur**' u seçin.
1. **Yeni proje oluştur** iletişim kutusunda **ASP.NET Core Web uygulaması** > **İleri**' yi seçin.
1. **Yeni projenizi yapılandırın** Iletişim kutusunda `ContosoUniversity` **Proje adı**' nı girin. Büyük harfler de dahil olmak üzere bu tam adı kullanmak önemlidir. bu nedenle, `namespace` Kod kopyalanırken her biri eşleşir.
1. **Oluştur**’u seçin.
1. **Yeni ASP.NET Core Web uygulaması oluştur** iletişim kutusunda şunları seçin:
    1. **.NET Core** ve **ASP.NET Core 5,0** açılır.
    1. **ASP.NET Core Web uygulaması (Model-View-Controller)**.
    1. **Create** 
       Oluştur ![ Yeni ASP.NET Core projesi iletişim kutusu](~/data/ef-mvc/intro/_static/new-aspnet5.png)

## <a name="set-up-the-site-style"></a>Site stilini ayarlayın

Birkaç temel değişiklik site menüsünü, düzeni ve giriş sayfasını ayarlar.

*Görünümler/paylaşılan/_Layout. cshtml* dosyasını açın ve aşağıdaki değişiklikleri yapın:

* Her oluşumunu ' a `ContosoUniversity` değiştirin `Contoso University` . Üç oluşum vardır.
* **Hakkında**, **öğrenciler**, **Kurslar**, **eğitmenler** ve **Departmanlar** için menü girişleri ekleyin ve **Gizlilik** menü girişini silin.

Yukarıdaki değişiklikler aşağıdaki kodda vurgulanır:

[!code-cshtml[](intro/samples/5cu/Views/Shared/_Layout.cshtml?highlight=6,24-38,52)]

*Görünümler/Home/Index. cshtml*'de, dosyanın içeriğini aşağıdaki biçimlendirmeyle değiştirin:

[!code-cshtml[](intro/samples/5cu/Views/Home/Index.cshtml)]

Projeyi çalıştırmak için CTRL + F5 tuşlarına basın veya menüden **hata ayıklama > Başlat** ' ı seçin. Giriş sayfası, bu öğreticide oluşturulan sayfaların sekmelerle birlikte görüntülenir.

![Contoso Üniversitesi ana sayfası](intro/_static/home-page5.png)

## <a name="ef-core-nuget-packages"></a>NuGet paketlerini EF Core

Bu öğretici SQL Server kullanır ve sağlayıcı paketi [Microsoft. EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)' dır.

EF SQL Server paketi ve bağımlılıkları `Microsoft.EntityFrameworkCore` ve `Microsoft.EntityFrameworkCore.Relational` , EF için çalışma zamanı desteği sağlar.

[Microsoft. AspNetCore. Diagnostics. entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet paketini ve [Microsoft. Aspnetcore. Diagnostics. entityframeworkcore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet paketini ekleyin. Paket Yöneticisi konsolunda (PMC), NuGet paketlerini eklemek için aşağıdaki komutları girin:

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore`NuGet paketi, EF Core hata sayfaları için ASP.NET Core ara yazılım sağlar. Bu ara yazılım EF Core geçişlerle hataları algılamaya ve tanılamanıza yardımcı olur.

EF Core için kullanılabilen diğer veritabanı sağlayıcıları hakkında daha fazla bilgi için bkz. [veritabanı sağlayıcıları](/ef/core/providers/).

## <a name="create-the-data-model"></a>Veri modelini oluşturma

Bu uygulama için aşağıdaki varlık sınıfları oluşturuldu:

![Kurs-kayıt-öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

Önceki varlıklarda aşağıdaki ilişkiler vardır:

* Ve varlıkları arasında bire çok ilişki `Student` `Enrollment` . Bir öğrenci, herhangi bir sayıda kursa kaydedilebilir.
* Ve varlıkları arasında bire çok ilişki `Course` `Enrollment` . Bir kurs, kayıtlı sayıda öğrenciye sahip olabilir.

Aşağıdaki bölümlerde, bu varlıkların her biri için bir sınıf oluşturulur.

### <a name="the-student-entity"></a>Öğrenci varlığı

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

*Modeller* klasöründe, `Student` sınıfı aşağıdaki kodla oluşturun:

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

`ID`Özelliği, bu sınıfa karşılık gelen veritabanı tablosunun birincil anahtar (**PK**) sütunudur. Varsayılan olarak, EF, `ID` birincil anahtar olarak veya adında bir özelliği Yorumlar `classnameID` . Örneğin, PK yerine adlandırılmış olabilir `StudentID` `ID` .

`Enrollments`Özelliği bir [Gezinti özelliğidir](/ef/core/modeling/relationships). Gezinti özellikleri, bu varlıkla ilgili diğer varlıkları tutar. `Enrollments`Bir `Student` varlığın özelliği:

* `Enrollment`Bu varlıkla ilgili tüm varlıkları içerir `Student` .
* Veritabanındaki belirli bir `Student` satırda iki ilişkili `Enrollment` satır varsa:
  * Bu `Student` varlığın `Enrollments` gezinti özelliği bu iki varlığı içerir `Enrollment` .
  
`Enrollment` satırlar `StudentID` yabancı anahtar (**FK**) sütununda öğrencinin PK değerini içerir.

Bir gezinti özelliği birden çok varlığı tutabilir:

 * Tür,, veya gibi bir liste olmalıdır `ICollection<T>` `List<T>` `HashSet<T>` .
 * Varlıklar eklenebilir, silinebilir ve güncelleştirilir.

Çoka çok ve bire çok gezinti ilişkileri birden çok varlık içerebilir. Kullanıldığında `ICollection<T>` , EF `HashSet<T>` Varsayılan olarak bir koleksiyon oluşturur.

### <a name="the-enrollment-entity"></a>Kayıt varlığı

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

*Modeller* klasöründe, `Enrollment` sınıfı aşağıdaki kodla oluşturun:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

`EnrollmentID`Özelliği, ba. Bu varlık `classnameID` kendi başına değil, kendi modelini kullanır `ID` . `Student`Varlık, bu `ID` kalıbı kullandı. Bazı geliştiriciler, veri modeli genelinde bir model kullanmayı tercih eder. Bu öğreticide, çeşitleme her iki düzenin de kullanılabileceğini gösterir. [Daha sonraki bir öğreticide](inheritance.md) `ID` , veri modelinde devralma uygulamayı nasıl daha kolay bir şekilde kullanacağınızı gösterir.

`Grade`Özelliği bir `enum` . `?` `Grade` Tür bildiriminden sonra, `Grade` özelliğin [null değer atanabilir](/dotnet/csharp/language-reference/builtin-types/nullable-value-types)olduğunu gösterir. Sıfır bir sınıfta farklı olan bir sınıf `null` . `null` bir sınıf henüz bilinmediğini veya henüz atanmamış anlamına gelir.

`StudentID`Özelliği bir yabancı anahtardır (FK) ve buna karşılık gelen gezinti özelliği olur `Student` . Bir varlık `Enrollment` bir `Student` varlıkla ilişkilendirilir, bu nedenle özellik yalnızca tek bir varlığı tutabilir `Student` . Bu, `Student.Enrollments` birden çok varlık içerebilen gezinti özelliğinden farklıdır `Enrollment` .

`CourseID`Özelliği BIR FK ve buna karşılık gelen gezinti özelliği `Course` . Bir `Enrollment` varlık bir `Course` varlıkla ilişkilendirilir.

Entity Framework, `<` gezinti özelliği adı `><` birincil anahtar özellik adı olarak adlandırılmışsa BIR özelliği FK özelliği olarak yorumlar `>` . Örneğin,, `StudentID` `Student` varlığın PK olduğundan, gezinti özelliği için `Student` `ID` . FK özellikleri,  `<` birincil anahtar özellik adı olarak da adlandırılabilir `>` . Örneğin, `CourseID` `Course` varlığın PK olduğu için `CourseID` .

### <a name="the-course-entity"></a>Kurs varlığı

![Kurs varlık diyagramı](intro/_static/course-entity.png)

*Modeller* klasöründe, `Course` sınıfı aşağıdaki kodla oluşturun:

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

`Enrollments`Özelliği bir gezinti özelliğidir. Bir `Course` varlık herhangi bir sayıda varlıkla ilişkili olabilir `Enrollment` .

[Databasegenerated](xref:System.ComponentModel.DataAnnotations.DatabaseGeneratedAttribute) özniteliği [sonraki bir öğreticide](complex-data-model.md)açıklanmıştır. Bu öznitelik, veritabanının oluşturmak yerine kursa için PK girmeye izin verir.

## <a name="create-the-database-context"></a>Veritabanı bağlamını oluşturma

Belirli bir veri modeli için EF işlevini koordine eden ana sınıf <xref:Microsoft.EntityFrameworkCore.DbContext> veritabanı bağlamı sınıfıdır. Bu sınıf sınıfından türeterek oluşturulur `Microsoft.EntityFrameworkCore.DbContext` . `DbContext`Türetilmiş sınıf, veri modeline hangi varlıkların ekleneceğini belirtir. Bazı EF davranışları özelleştirilebilir. Bu projede, sınıfı olarak adlandırılır `SchoolContext` .

Proje klasöründe adlı bir klasör oluşturun `Data` .

*Veri* klasöründe `SchoolContext` aşağıdaki kodla bir sınıf oluşturun:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Yukarıdaki kod, her bir `DbSet` varlık kümesi için bir özellik oluşturur. EF terimlerinde:

* Bir varlık kümesi, genellikle bir veritabanı tablosuna karşılık gelir.
* Bir varlık, tablodaki bir satıra karşılık gelir.

`DbSet<Enrollment>`Ve `DbSet<Course>` deyimleri atlanabilir ve aynı şekilde çalışır. EF, bunları örtük olarak dahil eder çünkü:

* `Student`Varlık `Enrollment` varlığa başvurur.
* `Enrollment`Varlık `Course` varlığa başvurur.

Veritabanı oluşturulduğunda EF, özellik adlarıyla aynı adlara sahip tablolar oluşturur `DbSet` . Koleksiyonlar için özellik adları genellikle plural ' dir. Örneğin, `Students` yerine `Student` . Geliştiriciler tablo adlarının plmış olup olmayacağını kabul etmez. Bu öğreticiler için varsayılan davranış, içinde tekil tablo adları belirtilerek geçersiz kılınır `DbContext` . Bunu yapmak için, son DbSet özelliğinden sonra aşağıdaki vurgulanmış kodu ekleyin.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

## <a name="register-the-schoolcontext"></a>Kayıt defteri `SchoolContext`

ASP.NET Core [bağımlılık ekleme](../../fundamentals/dependency-injection.md)işlemini içerir. EF veritabanı bağlamı gibi hizmetler, uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir. MVC denetleyicileri gibi bu hizmetleri gerektiren bileşenler, bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır. Bir bağlam örneğini alan denetleyici Oluşturucu kodu, Bu öğreticinin ilerleyen kısımlarında gösterilmektedir.

`SchoolContext`Hizmet olarak kaydetmek için *Startup.cs* açın ve vurgulanan satırları `ConfigureServices` yöntemine ekleyin.

[!code-csharp[](intro/samples/5cu-snap/Startup.cs?name=snippet&highlight=1-2,22-23)]

Bağlantı dizesinin adı bir nesne üzerinde bir yöntem çağırarak bağlama geçirilir `DbContextOptionsBuilder` . Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *appsettings.json* .

Dosyayı açın *appsettings.json* ve aşağıdaki biçimlendirmede gösterildiği gibi bir bağlantı dizesi ekleyin:

[!code-json[](./intro/samples/5cu/appsettings1.json?highlight=2-4)]

### <a name="add-the-database-exception-filter"></a>Veritabanı özel durum filtresini ekleme

<xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> `ConfigureServices` Aşağıdaki kodda gösterildiği gibi öğesine ekleyin:

[!code-csharp[](intro/samples/5cu/Startup.cs?name=snippet&highlight=6)]

, `AddDatabaseDeveloperPageExceptionFilter` [Geliştirme ortamında](xref:fundamentals/environments)yararlı hata bilgileri sağlar.

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Bağlantı dizesi [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)belirtir. LocalDB, SQL Server Express veritabanı altyapısının hafif bir sürümüdür ve üretim kullanımı için değil uygulama geliştirmeye yöneliktir. LocalDB, istek üzerine başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur. Varsayılan olarak, LocalDB dizinde *. mdf* DB dosyaları oluşturur `C:/Users/<user>` .

## <a name="initialize-db-with-test-data"></a>Test verileriyle VERITABANıNı başlatma

EF boş bir veritabanı oluşturur. Bu bölümde, test verileriyle doldurmak için veritabanı oluşturulduktan sonra çağrılan bir yöntem eklenir.

`EnsureCreated`Yöntemi, veritabanını otomatik olarak oluşturmak için kullanılır. Sonraki bir [öğreticide](migrations.md), veritabanını bırakıp yeniden oluşturmak yerine veritabanı şemasını değiştirmek için Code First Migrations kullanarak model değişikliklerini nasıl işleyeceğinizi görürsünüz.

*Veri* klasöründe, aşağıdaki kodla adlı yeni bir sınıf oluşturun `DbInitializer` :

[!code-csharp[DbInitializer](intro/samples/5cu-snap/DbInitializer.cs)]

Önceki kod, veritabanının mevcut olup olmadığını denetler:

* Veritabanı bulunamazsa;
  * Oluşturulur ve test verileriyle yüklenir. Performansı iyileştirmek için test verilerini koleksiyonlar yerine dizilere yükler `List<T>` .
* Bulunursa veritabanı hiçbir eylemde bulunmaz.

Aşağıdaki kodla *program.cs* güncelleştirin:

[!code-csharp[Program file](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-37)]

*Program.cs* , uygulamanın başlangıcında şunları yapar:

* Bağımlılık ekleme kapsayıcısından bir veritabanı bağlamı örneği alın.
* Yöntemini çağırın `DbInitializer.Initialize` .
* `Initialize`Yöntemi aşağıdaki kodda gösterildiği gibi tamamlandığında bağlamı atın:

[!code-csharp[](intro/samples/cu/Program.cs?name=snippet_Seed&highlight=5-18)]

Uygulama ilk kez çalıştırıldığında, veritabanı oluşturulur ve test verileriyle yüklenir. Veri modeli değiştiğinde:

* Veritabanını silin.
* Çekirdek yöntemini güncelleştirin ve baştan 'yi yeni bir veritabanı ile başlatın.

 Sonraki öğreticilerde, veri modeli silindiğinde ve yeniden oluşturmadan veritabanı değişir. Veri modeli değiştiğinde hiçbir veri kaybolmaz.

## <a name="create-controller-and-views"></a>Denetleyici ve görünüm oluşturma

Verileri sorgulamak ve kaydetmek için EF 'i kullanacak bir MVC denetleyicisi ve görünümleri eklemek için Visual Studio 'daki scafkatlama altyapısını kullanın.

[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) eylem yöntemlerinin ve görünümlerinin otomatik olarak oluşturulması, yapı iskelesi olarak bilinir.

* **Çözüm Gezgini**, klasöre sağ tıklayın `Controllers` ve **> yeni yapı iskelesi öğesi Ekle**' yi seçin.
* **Yapı Iskelesi Ekle** iletişim kutusunda:
  * **Entity Framework kullanarak, görünümlerle MVC denetleyicisi ' ni** seçin.
  * **Ekle**'ye tıklayın. **Görünümler Ile MVC denetleyicisi ekleme, Entity Framework kullanma** iletişim kutusu görünür: ![ Yapı iskelesi öğrenci](intro/_static/scaffold-student2.png)
  * **Model sınıfında** **öğrenci**' yi seçin.
  * **Veri bağlamı sınıfında** **SchoolContext** öğesini seçin.
  * Varsayılan **Studentscontroller** adını olarak kabul edin.
  * **Ekle**'ye tıklayın.

Visual Studio yapı iskelesi altyapısı, `StudentsController.cs` denetleyicisiyle birlikte çalışan bir dosya ve bir dizi görünüm ( `*.cshtml` Dosyalar) oluşturur.

Denetleyicinin bir `SchoolContext` Oluşturucu parametresi olarak aldığını unutmayın.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

ASP.NET Core bağımlılığı ekleme, denetleyiciye bir örneğini geçirme işlemini gerçekleştirir `SchoolContext` . Bunu `Startup` sınıfında yapılandırdınız.

Denetleyici, `Index` veritabanındaki tüm öğrencileri görüntüleyen bir Action yöntemi içerir. Yöntemi, `Students` veritabanı bağlamı örneğinin özelliğini okuyarak öğrenciler varlık kümesinden öğrencilerin bir listesini alır:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

Bu koddaki zaman uyumsuz programlama öğeleri öğreticide daha sonra açıklanmaktadır.

*Views/öğrenciler/Index. cshtml* görünümü bu listeyi bir tabloda görüntüler:

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

Projeyi çalıştırmak için CTRL + F5 tuşlarına basın veya menüden **hata ayıklama > Başlat** ' ı seçin.

Metodun eklendiği test verilerini görmek için öğrenciler sekmesine tıklayın `DbInitializer.Initialize` . Tarayıcı pencerenizin ne kadar dar olduğuna bağlı olarak `Students` sayfanın en üstünde sekme bağlantısını görürsünüz veya bağlantıyı görmek için sağ üst köşedeki gezinti simgesine tıklamanız gerekir.

![Contoso Üniversitesi giriş sayfası dar](intro/_static/home-page-narrow.png)

![Öğrenciler Dizin sayfası](intro/_static/students-index.png)

## <a name="view-the-database"></a>Veritabanını görüntüleme

Uygulama başlatıldığında, `DbInitializer.Initialize` yöntemi çağırır `EnsureCreated` . EF, veritabanı olmadığını gördük:

* Bu nedenle bir veritabanı oluşturdu.
* `Initialize`Yöntem kodu, veritabanını verilerle doldurmuş.

Visual Studio 'da veritabanını görüntülemek için **SQL Server Nesne Gezgini** (ssox) kullanın:

* Visual Studio 'daki **Görünüm** menüsünden **SQL Server Nesne Gezgini** ' yi seçin.
* SSOX 'te **(LocalDB) \MSSQLLocalDB > veritabanları**' nı seçin.
* `ContosoUniversity1`Dosyadaki bağlantı dizesinde bulunan veritabanı adı girişini seçin *appsettings.json* .
* Veritabanındaki tabloları görmek için **Tablolar** düğümünü genişletin.

![SSOX içindeki tablolar](intro/_static/ssox-tables.png)

**Öğrenci** tablosuna sağ tıklayın ve tablodaki verileri görmek Için **verileri görüntüle** ' ye tıklayın.

![SSOX 'te öğrenci tablosu](intro/_static/ssox-student-table.png)

`*.mdf`Ve `*.ldf` veritabanı dosyaları *C:\Users \\ \<username>* klasöründedir.

, `EnsureCreated` Uygulama başlatma sırasında çalışan Başlatıcı yönteminde çağrıldığı için şunları yapabilirsiniz:

* Sınıfında bir değişiklik yapın `Student` .
* Veritabanını silin.
* Durdurun ve uygulamayı başlatın. Veritabanı, değişikliği eşleştirmek için otomatik olarak yeniden oluşturulur.

Örneğin, `EmailAddress` sınıfına bir özellik eklenirse `Student` , `EmailAddress` yeniden oluşturulan tablodaki yeni bir sütun. Sınıflanmış görünüm yeni `EmailAddress` özelliği görüntülemez.

## <a name="conventions"></a>Kurallar

EF 'in bir bütün veritabanı oluşturması için yazılan kod miktarı, AŞV 'nin kullandığı kuralların kullanımı nedeniyle en az düzeydedir:

* `DbSet`Özelliklerin adları tablo adı olarak kullanılır. Bir özellik tarafından başvurulmayan varlıklar için `DbSet` , varlık sınıfı adları tablo adı olarak kullanılır.
* Varlık özelliği adları, sütun adları için kullanılır.
* Veya adlı varlık özellikleri `ID` `classnameID` PK özellikleri olarak tanınır.
* Bir özellik, `<` gezinti özelliği adı `><` PK Özellik adı olarak ADLANDıRıLMıŞSA, FK özelliği olarak yorumlanır `>` . Örneğin,, `StudentID` `Student` varlığın PK olduğundan, gezinti özelliği için `Student` `ID` . FK özellikleri, `<` birincil anahtar özellik adı olarak da adlandırılabilir `>` . Örneğin, `EnrollmentID` `Enrollment` varlığın PK olduğundan `EnrollmentID` .

Geleneksel davranış geçersiz kılınabilir. Örneğin, bu öğreticide daha önce gösterildiği gibi tablo adları açıkça belirtilebilir. Sütun adları ve herhangi bir özellik, bir PK veya FK olarak ayarlanabilir.

## <a name="asynchronous-code"></a>Zaman uyumsuz kod

Zaman uyumsuz programlama, ASP.NET Core ve EF Core için varsayılan moddur.

Web sunucusunda sınırlı sayıda iş parçacığı bulunur ve yüksek yük durumlarında tüm kullanılabilir iş parçacıkları kullanımda olabilir. Bu durumda, sunucu, iş parçacıkları boşaltılana kadar yeni istekleri işleyemez. Zaman uyumlu kodla, çok sayıda iş parçacığı, g/ç 'nin tamamlanmasını beklediği için aslında herhangi bir iş yapmadıklarında bağlı olabilir. Zaman uyumsuz kod ile, bir işlem g/ç 'yi tamamlanmayı beklerken, sunucunun diğer istekleri işlemek için kullanması için iş parçacığı serbest bırakılır. Sonuç olarak, zaman uyumsuz kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucu, gecikme olmadan daha fazla trafiği işlemeye etkinleştirilir.

Zaman uyumsuz kod, çalışma zamanında az miktarda yük getirir, ancak düşük trafik durumlarında performans artışı göz ardı edilebilir, ancak yüksek trafik durumları için olası performans iyileştirmesi oldukça önemlidir.

Aşağıdaki kodda,,, `async` `Task<T>` `await` ve `ToListAsync` kodu zaman uyumsuz olarak yürütür.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* `async`Anahtar sözcüğü, derleyiciye Yöntem gövdesinin parçaları için geri çağrılar oluşturmasını ve döndürülen nesneyi otomatik olarak oluşturmasını söyler `Task<IActionResult>` .
* Dönüş türü, `Task<IActionResult>` türünde bir sonuçla devam eden işi temsil eder `IActionResult` .
* `await`Anahtar sözcüğü, derleyicinin yöntemi iki parçaya böetmesine neden olur. İlk bölüm, zaman uyumsuz olarak başlatılan işlemle biter. İkinci bölüm, işlem tamamlandığında çağrılan bir geri çağırma yöntemine konur.
* `ToListAsync` , Uzantı yönteminin zaman uyumsuz sürümüdür `ToList` .

EF kullanan zaman uyumsuz kodu yazarken dikkat edilmesi gereken bazı şeyler:

* Yalnızca sorguları veya komutlarının veritabanına gönderilmesine neden olan deyimler zaman uyumsuz olarak yürütülür. Bu, örneğin,, `ToListAsync` ve içerir `SingleOrDefaultAsync` `SaveChangesAsync` . Örneğin, gibi yalnızca bir değiştiren deyimler içermez `IQueryable` `var students = context.Students.Where(s => s.LastName == "Davolio")` .
* EF bağlamı iş parçacığı açısından güvenli değildir: paralel olarak birden çok işlem yapmayı denemeyin. Herhangi bir zaman uyumsuz EF yöntemini çağırdığınızda, her zaman `await` anahtar sözcüğünü kullanın.
* Zaman uyumsuz kodun performans avantajlarından yararlanmak için, kullanılan tüm kitaplık paketlerinin, sorguların veritabanına gönderilmesine neden olan bir EF yöntemi çağırıyorsa, zaman uyumsuz olarak da kullanıldığından emin olun.

.NET ' te zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. [Async Overview](/dotnet/articles/standard/async).

## <a name="limit-entities-fetched"></a>Getirilen varlıkları sınırla

Bir sorgudan döndürülen sayı veya varlıkları sınırlama hakkında bilgi için bkz. [performans konuları](xref:data/ef-rp/intro) .

Temel CRUD (oluşturma, okuma, güncelleştirme, silme) işlemlerini nasıl gerçekleştireceğinizi öğrenmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Temel CRUD işlevlerini uygulama](crud.md)

::: moniker-end

::: moniker range="<= aspnetcore-3.1"

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc.md)]

Contoso Üniversitesi örnek Web uygulaması, Entity Framework (EF) Core 2,2 ve Visual Studio 2017 veya 2019 kullanarak ASP.NET Core 2,2 MVC web uygulamalarının nasıl oluşturulacağını gösterir.

Bu öğretici ASP.NET Core 3,1 için güncelleştirilmedi. [ASP.NET Core 5,0](xref:data/ef-mvc/intro?view=aspnetcore-5.0)için güncelleştirilmiştir.

Örnek uygulama, kurgusal bir Contoso Üniversitesi için bir Web sitesidir. Öğrenci giriş, kurs oluşturma ve eğitmen atamaları gibi işlevleri içerir. Bu, Contoso Üniversitesi örnek uygulamasının sıfırdan nasıl oluşturulacağını açıklayan bir öğretici serisinin ilkisidir.

## <a name="prerequisites"></a>Önkoşullar

* [.NET Core SDK 2,2](https://dotnet.microsoft.com/download)
* Aşağıdaki iş yükleriyle [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) :
  * **ASP.net ve Web geliştirme** iş yükü
  * **.NET Core platformlar arası geliştirme** iş yükü

## <a name="troubleshooting"></a>Sorun giderme

Çözemiyoruz bir sorunla karşılaşırsanız, kodunuzun [Tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples)karşılaştırılmasıyla genellikle çözümü bulabilirsiniz. Yaygın hataların bir listesi ve bunların nasıl çözüleceği için, [serideki son öğreticinin sorun giderme bölümüne](advanced.md#common-errors)bakın. İhtiyacınız olanları bulamazsanız, [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core)için bir soru gönderebilirsiniz.

> [!TIP]
> Bu, her biri daha önceki öğreticilerde gerçekleştirilen bir dizi 10 öğreticisidir. Her başarılı öğreticinin tamamlanmasından sonra projenin bir kopyasını kaydetmeyi göz önünde bulundurun. Daha sonra sorunlarla karşılaşırsanız, tüm serinin başlangıcına dönmek yerine önceki öğreticiden baştan başlayabilirsiniz.

## <a name="contoso-university-web-app"></a>Contoso Üniversitesi web uygulaması

Bu öğreticilerde oluşturacağınız uygulama basit bir üniversite web sitesidir.

Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleştirebilir. Oluşturacağınız ekranların bazıları aşağıda verilmiştir.

![Öğrenciler Dizin sayfası](intro/_static/students-index.png)

![Öğrenciler düzenleme sayfası](intro/_static/student-edit.png)

## <a name="create-web-app"></a>Web uygulaması oluşturma

* Visual Studio'yu açın.

* **Dosya** menüsünden **Yeni > proje**' yi seçin.

* Sol bölmeden, **yüklü > Visual C# > Web**' i seçin.

* **ASP.NET Core Web uygulaması** proje şablonunu seçin.

* Ad olarak **Contosouniversity** yazın ve **Tamam**' a tıklayın.

  ![Yeni Proje iletişim kutusu](intro/_static/new-project2.png)

* **Yeni ASP.NET Core Web uygulaması** iletişim kutusunun görünmesini bekleyin.

* **.NET Core**, **ASP.NET Core 2,2** ve **Web uygulaması (Model-View-Controller)** şablonu ' nu seçin.

* **Kimlik doğrulamanın** **kimlik doğrulaması yok** olarak ayarlandığından emin olun.

* **Tamam**'ı seçin

  ![Yeni ASP.NET Core projesi iletişim kutusu](intro/_static/new-aspnet2.png)

## <a name="set-up-the-site-style"></a>Site stilini ayarlayın

Birkaç basit değişiklik, site menüsünü, düzeni ve giriş sayfasını ayarlar.

*Görünümler/paylaşılan/_Layout. cshtml* dosyasını açın ve aşağıdaki değişiklikleri yapın:

* "ContosoUniversity" öğesinin her oluşumunu "Contoso Üniversitesi" olarak değiştirin. Üç oluşum vardır.

* **Hakkında**, **öğrenciler**, **Kurslar**, **eğitmenler** ve **Departmanlar** için menü girişleri ekleyin ve **Gizlilik** menü girişini silin.

Değişiklikler vurgulanır.

[!code-cshtml[](intro/samples/cu/Views/Shared/_Layout.cshtml?highlight=6,34-48,63)]

*Görünümler/Home/Index. cshtml*'de, ASP.net ve MVC hakkındaki metni bu uygulamayla ilgili metinle değiştirmek için dosyanın içeriğini aşağıdaki kodla değiştirin:

[!code-cshtml[](intro/samples/cu/Views/Home/Index.cshtml)]

Projeyi çalıştırmak için CTRL + F5 tuşlarına basın veya menüden **hata ayıklama > Başlat** ' ı seçin. Bu öğreticilerde oluşturacağınız sayfaların sekmelerini içeren giriş sayfasını görürsünüz.

![Contoso Üniversitesi ana sayfası](intro/_static/home-page.png)

## <a name="about-ef-core-nuget-packages"></a>NuGet paketleri EF Core hakkında

Bir projeye EF Core destek eklemek için hedeflemek istediğiniz veritabanı sağlayıcısını yükleyebilirsiniz. Bu öğretici SQL Server kullanır ve sağlayıcı paketi [Microsoft. EntityFrameworkCore. SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/)' dır. Bu paket [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunur, bu nedenle pakete başvurmanız gerekmez.

EF SQL Server paketi ve bağımlılıkları ( `Microsoft.EntityFrameworkCore` ve `Microsoft.EntityFrameworkCore.Relational` ) EF için çalışma zamanı desteği sağlar. [Geçiş](migrations.md) öğreticisinde daha sonra bir araç paketi ekleyeceksiniz.

Entity Framework Core için kullanılabilen diğer veritabanı sağlayıcıları hakkında daha fazla bilgi için bkz. [veritabanı sağlayıcıları](/ef/core/providers/).

## <a name="create-the-data-model"></a>Veri modelini oluşturma

Daha sonra Contoso Üniversitesi uygulaması için varlık sınıfları oluşturacaksınız. Aşağıdaki üç varlıkla başlayacaksınız.

![Kurs-kayıt-öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

Ve varlıkları arasında bire çok ilişki vardır `Student` `Enrollment` ve ile varlıklar arasında bire çok ilişki vardır `Course` `Enrollment` . Diğer bir deyişle, bir öğrenci herhangi bir sayıda kursa kaydedilebilir ve bir kurs, kayıtlı sayıda öğrenciye sahip olabilir.

Aşağıdaki bölümlerde, bu varlıkların her biri için bir sınıf oluşturacaksınız.

### <a name="the-student-entity"></a>Öğrenci varlığı

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

*Modeller* klasöründe, *Student.cs* adlı bir sınıf dosyası oluşturun ve şablon kodunu aşağıdaki kodla değiştirin.

[!code-csharp[](intro/samples/cu/Models/Student.cs?name=snippet_Intro)]

`ID`Özelliği, bu sınıfa karşılık gelen veritabanı tablosunun birincil anahtar sütunu olacak. Varsayılan olarak Entity Framework, `ID` veya `classnameID` olarak adlandırılan özellikleri birincil anahtar olarak algılar.

`Enrollments`Özelliği bir [Gezinti özelliğidir](/ef/core/modeling/relationships). Gezinti özellikleri, bu varlıkla ilgili diğer varlıkları tutar. Bu durumda, `Enrollments` öğesinin özelliği bu `Student entity` `Enrollment` varlıkla ilgili varlıkların tümünü tutacaktır `Student` . Diğer bir deyişle, veritabanındaki bir `Student` satırda iki ilişkili satır varsa (söz konusu `Enrollment` öğrencinin birincil anahtar değerini kendi Studentitıd yabancı anahtar sütununda içeren satırlar) varsa, bu `Student` varlığın `Enrollments` gezinti özelliği bu iki `Enrollment` varlığı içerecektir.

Bir gezinti özelliği birden çok varlığı tutabileceiyorsa (çok-çok veya bire çok ilişkilerde olduğu gibi), türü girişlerin eklenebileceği, silinebileceği ve güncelleştirilemeyebilir bir liste olmalıdır `ICollection<T>` . Veya gibi `ICollection<T>` bir tür belirtebilirsiniz `List<T>` `HashSet<T>` . Belirtirseniz `ICollection<T>` , EF `HashSet<T>` Varsayılan olarak bir koleksiyon oluşturur.

### <a name="the-enrollment-entity"></a>Kayıt varlığı

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

*Modeller* klasöründe *enrollment.cs* oluşturun ve mevcut kodu şu kodla değiştirin:

[!code-csharp[](intro/samples/cu/Models/Enrollment.cs?name=snippet_Intro)]

`EnrollmentID`Özelliği birincil anahtar olacaktır; bu varlık, `classnameID` `ID` varlıkta gördüğünüz gibi kendi başına bir model kullanır `Student` . Normalde tek bir model seçip veri modeliniz genelinde kullanabilirsiniz. Burada, değişim, her iki stili de kullanabileceğinizi gösterir. [Daha sonraki bir öğreticide](inheritance.md), ID 'yi ClassName olmadan kullanarak, veri modelinde devralma uygulamayı daha kolay hale getirir.

`Grade`Özelliği bir `enum` . Tür bildiriminden sonraki soru işareti, `Grade` `Grade` özelliğin null yapılabilir olduğunu gösterir. Null olan bir sınıf sıfır bir sınıf ile farklıdır--null, henüz bir sınıf bilinmediğini veya henüz atanmadığını belirtir.

`StudentID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Student` . Bir varlık `Enrollment` bir `Student` varlıkla ilişkilendirilir, bu nedenle özellik yalnızca tek bir varlığı tutabilir `Student` ( `Student.Enrollments` daha önce gördüğünüz gezinti özelliğinden farklı olarak, birden çok `Enrollment` varlık tutabilir).

`CourseID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Course` . Bir `Enrollment` varlık bir `Course` varlıkla ilişkilendirilir.

Entity Framework, bir özelliği bir yabancı anahtar özelliği olarak `<navigation property name><primary key property name>` (örneğin, `StudentID` `Student` varlığın birincil anahtarından bu yana gezinti özelliği için) olarak yorumlar `Student` `ID` . Yabancı anahtar özellikleri de yalnızca adlandırılmış olabilir `<primary key property name>` (örneğin, `CourseID` `Course` varlığın birincil anahtarı olduğundan `CourseID` ).

### <a name="the-course-entity"></a>Kurs varlığı

![Kurs varlık diyagramı](intro/_static/course-entity.png)

*Modeller* klasöründe *Course.cs* oluşturun ve mevcut kodu şu kodla değiştirin:

[!code-csharp[](intro/samples/cu/Models/Course.cs?name=snippet_Intro)]

`Enrollments`Özelliği bir gezinti özelliğidir. Bir `Course` varlık herhangi bir sayıda varlıkla ilişkili olabilir `Enrollment` .

`DatabaseGenerated`Bu serinin [sonraki bir öğreticide](complex-data-model.md) özniteliği hakkında daha fazla bilgi edineceksiniz. Temel olarak bu öznitelik, veritabanının oluşturması yerine kursa ait birincil anahtarı girmenize olanak sağlar.

## <a name="create-the-database-context"></a>Veritabanı bağlamını oluşturma

Belirli bir veri modeli için Entity Framework işlevselliğini koordine eden ana sınıf veritabanı bağlamı sınıfıdır. Sınıfından türeterek bu sınıfı oluşturursunuz `Microsoft.EntityFrameworkCore.DbContext` . Kodunuzda, veri modeline hangi varlıkların ekleneceğini belirtirsiniz. Ayrıca, belirli Entity Framework davranışlarını özelleştirebilirsiniz. Bu projede, sınıfı olarak adlandırılır `SchoolContext` .

Proje klasöründe, *veri* adlı bir klasör oluşturun.

*Veri* klasöründe, *SchoolContext.cs* adlı yeni bir sınıf dosyası oluşturun ve şablon kodunu şu kodla değiştirin:

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_Intro)]

Bu kod, `DbSet` her bir varlık kümesi için bir özellik oluşturur. Entity Framework terimlerinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir ve bir varlık tablodaki bir satıra karşılık gelir.

Ve deyimlerini atlamış olabilirsiniz `DbSet<Enrollment>` `DbSet<Course>` ve aynı şekilde çalışır. Entity Framework, `Student` varlık varlığa başvurduğundan ve varlık varlığa başvurduğundan bunları örtülü olarak içerebilir `Enrollment` `Enrollment` `Course` .

Veritabanı oluşturulduğunda EF, özellik adlarıyla aynı adlara sahip tablolar oluşturur `DbSet` . Koleksiyonlar için özellik adları genellikle plural (öğrenci yerine öğrenciler), ancak geliştiriciler tablo adlarının plurulululmasını kabul etmez. Bu öğreticiler için DbContext içinde tekil tablo adları belirterek varsayılan davranışı geçersiz kılarsınız. Bunu yapmak için, son DbSet özelliğinden sonra aşağıdaki vurgulanmış kodu ekleyin.

[!code-csharp[](intro/samples/cu/Data/SchoolContext.cs?name=snippet_TableNames&highlight=16-21)]

Projeyi derleyici hatalarına yönelik bir denetim olarak derleyin.

## <a name="register-the-schoolcontext"></a>SchoolContext kaydetme

ASP.NET Core, varsayılan olarak [bağımlılık ekleme](../../fundamentals/dependency-injection.md) işlemini uygular. Hizmetler (EF veritabanı bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir. Bu hizmetleri gerektiren bileşenler (MVC denetleyicileri gibi) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır. Bu öğreticide daha sonra bir bağlam örneği alan denetleyici Oluşturucu kodunu görürsünüz.

`SchoolContext`Hizmet olarak kaydetmek için *Startup.cs* açın ve vurgulanan satırları `ConfigureServices` yöntemine ekleyin.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_SchoolContext&highlight=9-10)]

Bağlantı dizesinin adı bir nesne üzerinde bir yöntem çağırarak bağlama geçirilir `DbContextOptionsBuilder` . Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *appsettings.json* .

`using` `ContosoUniversity.Data` Ve ad alanları için deyimler ekleyin `Microsoft.EntityFrameworkCore` ve ardından projeyi derleyin.

[!code-csharp[](intro/samples/cu/Startup.cs?name=snippet_Usings)]

Dosyayı açın *appsettings.json* ve aşağıdaki örnekte gösterildiği gibi bir bağlantı dizesi ekleyin.

[!code-json[](./intro/samples/cu/appsettings1.json?highlight=2-4)]

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Bağlantı dizesi bir SQL Server LocalDB veritabanı belirtir. LocalDB, SQL Server Express veritabanı altyapısının hafif bir sürümüdür ve üretim kullanımı için değil uygulama geliştirmeye yöneliktir. LocalDB, istek üzerine başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur. Varsayılan olarak, LocalDB dizinde *. mdf* veritabanı dosyaları oluşturur `C:/Users/<user>` .

## <a name="initialize-db-with-test-data"></a>Test verileriyle VERITABANıNı başlatma

Entity Framework, sizin için boş bir veritabanı oluşturacaktır. Bu bölümde, test verileriyle doldurmak için veritabanı oluşturulduktan sonra çağrılan bir yöntem yazarsınız.

Burada, `EnsureCreated` veritabanını otomatik olarak oluşturmak için yöntemini kullanacaksınız. Sonraki bir [öğreticide](migrations.md) , veritabanını bırakıp yeniden oluşturmak yerine veritabanı şemasını değiştirmek için Code First Migrations kullanarak model değişikliklerini nasıl işleyeceğinizi göreceksiniz.

*Veri* klasöründe, *DbInitializer.cs* adlı yeni bir sınıf dosyası oluşturun ve şablon kodunu, gerektiğinde bir veritabanının oluşturulmasına neden olan aşağıdaki kodla değiştirin ve test verilerini yeni veritabanına yükler.

[!code-csharp[](intro/samples/cu/Data/DbInitializer.cs?name=snippet_Intro)]

Kod, veritabanında herhangi bir öğrenci olup olmadığını denetler ve yoksa, veritabanının yeni olduğunu ve test verileriyle hazırlanması gerektiğini varsayar. Performansı iyileştirmek için test verilerini koleksiyonlar yerine dizilere yükler `List<T>` .

*Program.cs*' de, `Main` Uygulama başlangıcında aşağıdakini yapmak için yöntemini değiştirin:

* Bağımlılık ekleme kapsayıcısından bir veritabanı bağlamı örneği alın.
* Temel yöntemi çağırın ve bu yönteme geçerek bağlamı geçer.
* Çekirdek yöntemi tamamlandığında bağlamı atın.

[!code-csharp[](intro/samples/5cu-snap/Program.cs?highlight=1-2,14-18,21-38)]

Uygulamayı ilk kez çalıştırdığınızda veritabanı oluşturulur ve test verileriyle birlikte gösterilir. Veri modelini her değiştirdiğinizde:

 * Veritabanını silin.
 * Çekirdek yöntemini güncelleştirin ve yeni bir veritabanıyla aynı şekilde bir baştan başlatın.
 
Sonraki öğreticilerde, veri modeli değiştiğinde ve yeniden oluşturmadan veritabanını nasıl değiştireceğiniz hakkında bilgi edineceksiniz.

## <a name="create-controller-and-views"></a>Denetleyici ve görünüm oluşturma

Bu bölümde, verileri sorgulamak ve kaydetmek için EF 'i kullanacak bir MVC denetleyicisi ve görünümleri eklemek için Visual Studio 'daki scafkatlama altyapısı kullanılır.

CRUD eylem yöntemlerinin ve görünümlerinin otomatik olarak oluşturulması, yapı iskelesi olarak bilinir. Yapı iskelesi, yapı oluşturma işleminden farklı olarak, kendi gereksinimlerinize uyacak şekilde değiştirebileceğiniz bir başlangıç noktası ve genellikle oluşturulan kodu değiştirmezsiniz. Oluşturulan kodu özelleştirmeniz gerektiğinde, kısmi sınıfları kullanırsınız veya işlemler değiştiğinde kodu yeniden oluşturmanız gerekir.

* **Çözüm Gezgini** ' de **denetleyiciler** klasörüne sağ tıklayın ve **> yeni iskele öğe Ekle**' yi seçin.
* **Yapı Iskelesi Ekle** iletişim kutusunda:
  * **Entity Framework kullanarak, görünümlerle MVC denetleyicisi ' ni** seçin.
  * **Ekle**'ye tıklayın. **Görünümler Ile MVC denetleyicisi ekleme, Entity Framework kullanma** iletişim kutusu görünür: ![ Yapı iskelesi öğrenci](intro/_static/scaffold-student2.png)
  * **Model sınıfı** ' nda **öğrenci**' yi seçin.
  * **Veri bağlamı sınıfında** **SchoolContext** öğesini seçin.
  * Varsayılan **Studentscontroller** adını olarak kabul edin.
  * **Ekle**'ye tıklayın.

Visual Studio yapı iskelesi altyapısı, denetleyicisiyle birlikte çalışan bir *StudentsController.cs* dosyası ve bir dizi görünüm (*. cshtml* dosyası) oluşturur.

Denetleyicinin bir `SchoolContext` Oluşturucu parametresi olarak aldığını unutmayın.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_Context&highlight=5,7,9)]

ASP.NET Core bağımlılığı ekleme, denetleyiciye bir örneğini geçirme işlemini gerçekleştirir `SchoolContext` . Bu, *Startup.cs* dosyasında yapılandırılmıştır.

Denetleyici, `Index` veritabanındaki tüm öğrencileri görüntüleyen bir Action yöntemi içerir. Yöntemi, `Students` veritabanı bağlamı örneğinin özelliğini okuyarak öğrenciler varlık kümesinden öğrencilerin bir listesini alır:

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex&highlight=3)]

Bu koddaki zaman uyumsuz programlama öğelerini öğreticide daha sonra öğrenirsiniz.

*Views/öğrenciler/Index. cshtml* görünümü bu listeyi bir tabloda görüntüler:

[!code-cshtml[](intro/samples/cu/Views/Students/Index1.cshtml)]

Projeyi çalıştırmak için CTRL + F5 tuşlarına basın veya menüden **hata ayıklama > Başlat** ' ı seçin.

Metodun eklendiği test verilerini görmek için öğrenciler sekmesine tıklayın `DbInitializer.Initialize` . Tarayıcı pencerenizin ne kadar dar olduğuna bağlı olarak `Students` sayfanın en üstünde sekme bağlantısını görürsünüz veya bağlantıyı görmek için sağ üst köşedeki gezinti simgesine tıklamanız gerekir.

![Contoso Üniversitesi giriş sayfası dar](intro/_static/home-page-narrow.png)

![Öğrenciler Dizin sayfası](intro/_static/students-index.png)

## <a name="view-the-database"></a>Veritabanını görüntüleme

Uygulamayı başlattığınızda, `DbInitializer.Initialize` yöntemi çağırır `EnsureCreated` . EF, bir veritabanı olmadığını ve bu nedenle bir tane oluşturduğunu gördük, daha sonra `Initialize` Yöntem kodunun geri kalanı veritabanını verilerle doldurmuş. Visual Studio 'da veritabanını görüntülemek için **SQL Server Nesne Gezgini** (ssox) kullanabilirsiniz.

Tarayıcıyı kapatın.

SSOX penceresi henüz açık değilse, Visual Studio 'daki **Görünüm** menüsünden bunu seçin.

SSOX 'te **(LocalDB) \MSSQLLocalDB > veritabanları**' na tıklayın ve ardından dosyadaki bağlantı dizesinde bulunan veritabanı adı için girişe tıklayın *appsettings.json* .

Veritabanındaki tabloları görmek için **Tablolar** düğümünü genişletin.

![SSOX içindeki tablolar](intro/_static/ssox-tables.png)

Oluşturulan sütunları ve tabloya eklenmiş satırları görmek için **öğrenci** tablosuna sağ tıklayın ve **verileri görüntüle** ' ye tıklayın.

![SSOX 'te öğrenci tablosu](intro/_static/ssox-student-table.png)

*. Mdf* ve *. ldf* veritabanı dosyaları *\\ \<username> C:\Users* klasöründedir.

`EnsureCreated`Uygulama başlatma sırasında çalışan Başlatıcı metodunu çağırırken, artık sınıfta bir değişiklik yapabilir, `Student` veritabanını silebilir, uygulamayı yeniden çalıştırabilirsiniz ve veritabanı, değişikliklerinizi eşleştirmek için otomatik olarak yeniden oluşturulur. Örneğin, `EmailAddress` sınıfa bir özellik eklerseniz `Student` , `EmailAddress` yeniden oluşturulan tabloda yeni bir sütun görürsünüz.

## <a name="conventions"></a>Kurallar

Entity Framework, kuralların kullanımı veya Entity Framework varsayımlarıyla ilgili olarak en az bir veritabanı oluşturabilmesini sağlamak için yazmanız gereken kod miktarı.

* `DbSet`Özelliklerin adları tablo adı olarak kullanılır. Bir özellik tarafından başvurulmayan varlıklar için `DbSet` , varlık sınıfı adları tablo adı olarak kullanılır.
* Varlık özelliği adları, sütun adları için kullanılır.
* ID veya Classnameıd olarak adlandırılan varlık özellikleri, birincil anahtar özellikleri olarak tanınır.
* Bir özellik, adlandırılmış ise yabancı anahtar özelliği olarak yorumlanır *\<navigation property name>\<primary key property name>* (örneğin, `StudentID` `Student` `Student` varlığın birincil anahtarı olduğundan gezinti özelliği için `ID` ). Yabancı anahtar özellikleri de yalnızca adlandırılmış olabilir *\<primary key property name>* (örneğin, `EnrollmentID` `Enrollment` varlığın birincil anahtarı olduğundan `EnrollmentID` ).

Geleneksel davranış geçersiz kılınabilir. Örneğin, bu öğreticide daha önce gördüğünüz gibi tablo adlarını açıkça belirtebilirsiniz. Ayrıca, bu serinin [sonraki bir öğreticide](complex-data-model.md) göreceğiniz gibi, sütun adlarını ayarlayabilir ve herhangi bir özelliği birincil anahtar veya yabancı anahtar olarak ayarlayabilirsiniz.

## <a name="asynchronous-code"></a>Zaman uyumsuz kod

Zaman uyumsuz programlama, ASP.NET Core ve EF Core için varsayılan moddur.

Web sunucusunda sınırlı sayıda iş parçacığı bulunur ve yüksek yük durumlarında tüm kullanılabilir iş parçacıkları kullanımda olabilir. Bu durumda, sunucu, iş parçacıkları boşaltılana kadar yeni istekleri işleyemez. Zaman uyumlu kodla, çok sayıda iş parçacığı, g/ç 'nin tamamlanmasını beklediği için aslında herhangi bir iş yapmadıklarında bağlı olabilir. Zaman uyumsuz kod ile, bir işlem g/ç 'yi tamamlanmayı beklerken, sunucunun diğer istekleri işlemek için kullanması için iş parçacığı serbest bırakılır. Sonuç olarak, zaman uyumsuz kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucu, gecikme olmadan daha fazla trafiği işlemeye etkinleştirilir.

Zaman uyumsuz kod, çalışma zamanında az miktarda yük getirir, ancak düşük trafik durumlarında performans artışı göz ardı edilebilir, ancak yüksek trafik durumları için olası performans iyileştirmesi oldukça önemlidir.

Aşağıdaki kodda, `async` anahtar sözcüğü, `Task<T>` return değeri, `await` anahtar sözcüğü ve `ToListAsync` yöntemi kodu zaman uyumsuz olarak yürütür.

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_ScaffoldedIndex)]

* `async`Anahtar sözcüğü, derleyiciye Yöntem gövdesinin parçaları için geri çağrılar oluşturmasını ve döndürülen nesneyi otomatik olarak oluşturmasını söyler `Task<IActionResult>` .
* Dönüş türü, `Task<IActionResult>` türünde bir sonuçla devam eden işi temsil eder `IActionResult` .
* `await`Anahtar sözcüğü, derleyicinin yöntemi iki parçaya böetmesine neden olur. İlk bölüm, zaman uyumsuz olarak başlatılan işlemle biter. İkinci bölüm, işlem tamamlandığında çağrılan bir geri çağırma yöntemine konur.
* `ToListAsync` , Uzantı yönteminin zaman uyumsuz sürümüdür `ToList` .

Entity Framework kullanan zaman uyumsuz kod yazarken dikkat edilmesi gereken bazı şeyler:

* Yalnızca sorguları veya komutlarının veritabanına gönderilmesine neden olan deyimler zaman uyumsuz olarak yürütülür. Bu, örneğin,, `ToListAsync` ve içerir `SingleOrDefaultAsync` `SaveChangesAsync` . Örneğin, gibi yalnızca bir değiştiren deyimler içermez `IQueryable` `var students = context.Students.Where(s => s.LastName == "Davolio")` .
* EF bağlamı iş parçacığı açısından güvenli değildir: paralel olarak birden çok işlem yapmayı denemeyin. Herhangi bir zaman uyumsuz EF yöntemini çağırdığınızda, her zaman `await` anahtar sözcüğünü kullanın.
* Zaman uyumsuz kodun performans avantajlarından yararlanmak isterseniz, kullanmakta olduğunuz tüm kitaplık paketlerinin (örneğin, sayfalama için), sorguların veritabanına gönderilmesine neden olan herhangi bir Entity Framework yöntemini çağırıyorsa, zaman uyumsuz olarak da kullanılmasını sağlayın.

.NET ' te zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. [Async Overview](/dotnet/articles/standard/async).

## <a name="next-steps"></a>Sonraki adımlar

Temel CRUD (oluşturma, okuma, güncelleştirme, silme) işlemlerini nasıl gerçekleştireceğinizi öğrenmek için sonraki öğreticiye ilerleyin.

> [!div class="nextstepaction"]
> [Temel CRUD işlevlerini uygulama](crud.md)

::: moniker-end
