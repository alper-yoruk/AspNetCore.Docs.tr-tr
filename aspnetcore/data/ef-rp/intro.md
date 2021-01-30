---
title: Razor ASP.NET Core Entity Framework Core olan sayfalar-öğretici 1/8
author: rick-anderson
description: RazorEntity Framework Core kullanarak bir sayfalar uygulamasının nasıl oluşturulacağını gösterir
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
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
uid: data/ef-rp/intro
ms.openlocfilehash: 2f6299511b568a70c638dd5d4c735bf22adb5e95
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057439"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a>Razor ASP.NET Core Entity Framework Core olan sayfalar-öğretici 1/8

, [Tom Dykstra](https://github.com/tdykstra) ve [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından

::: moniker range=">= aspnetcore-5.0"

Bu, bir [ASP.NET Core Razor sayfaları](xref:razor-pages/index) UYGULAMASıNDA Entity Framework (EF) çekirdeğini nasıl kullanacağınızı gösteren bir öğretici serisinin ilkisidir. Öğreticiler, kurgusal bir Contoso Üniversitesi için bir Web sitesi oluşturur. Site, öğrenci giriş, kurs oluşturma ve eğitmen atamaları gibi işlevleri içerir. Öğretici ilk kod yaklaşımını kullanır. Bu öğreticiyi veritabanı ilk yaklaşımı kullanarak takip eden bilgiler için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/16897)bakın.

[Tamamlanmış uygulamayı indirin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Yönergeleri indirin](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Önkoşullar

* Sayfalarla yeni başladıysanız Razor , bunu başlatmadan önce [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) öğreticisini izleyin.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a>Veritabanı altyapıları

Visual Studio yönergeleri, yalnızca Windows üzerinde çalışan bir SQL Server Express sürümü olan [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)'yi kullanır.

Visual Studio Code yönergeler, platformlar arası bir veritabanı altyapısı olan [SQLite](https://www.sqlite.org/)kullanır.

SQLite kullanmayı seçerseniz, SQLite [Için DB tarayıcısı](https://sqlitebrowser.org/)gibi bir SQLite veritabanını yönetmek ve görüntülemek için üçüncü taraf bir araç indirip yükleyin.

## <a name="troubleshooting"></a>Sorun giderme

Giderebileceğiniz bir sorunla karşılaşırsanız, kodunuzu [Tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)karşılaştırın. Yardım almanın iyi bir yolu, [ASP.NET Core etiketi](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF Core etiketi](https://stackoverflow.com/questions/tagged/entity-framework-core)kullanılarak StackOverflow.com 'e bir soru göndererek.

## <a name="the-sample-app"></a>Örnek uygulama

Bu öğreticilerde oluşturulan uygulama, temel bir üniversite web sitesidir. Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleştirebilir. Öğreticide oluşturulan ekranlardan bazıları aşağıda verilmiştir.

![Öğrenciler Dizin sayfası](intro/_static/students-index30.png)

![Öğrenciler düzenleme sayfası](intro/_static/student-edit30.png)

Bu sitenin kullanıcı arabirimi stili yerleşik proje şablonlarına dayalıdır. Öğreticinin odağı, Kullanıcı arabirimini nasıl özelleştireceğinizi değil, ASP.NET Core EF Core nasıl kullanacağınızı kullanmaktır.

<!-- 
Follow the link at the top of the page to get the source code for the completed project. The *cu50* folder has the code for the ASP.NET Core 5.0 version of the tutorial. Files that reflect the state of the code for tutorials 1-7 can be found in the *cu50snapshots* folder.

# [Visual Studio](#tab/visual-studio)

To run the app after downloading the completed project:

* Build the project.
* In Package Manager Console (PMC) run the following command:

  ```powershell
  Update-Database
  ```

* Run the project to seed the database.

# [Visual Studio Code](#tab/visual-studio-code)

To run the app after downloading the completed project:

* In *Program.cs*, remove the comments from `// webBuilder.UseStartup<StartupSQLite>();`  so `StartupSQLite` is used.
* Copy the contents of *appSettingsSQLite.json* into *appSettings.json*.
* Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.
* Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.
* Build the project.
* At a command prompt in the project folder, run the following commands:

  ```dotnetcli
  dotnet tool install --global dotnet-ef -v 5.0.0-*
  dotnet ef database update
  ```

* In your SQLite tool, run the following SQL statement:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Run the project to seed the database.

---

-->

## <a name="create-the-web-app-project"></a>Web uygulaması projesi oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Visual Studio 'Yu başlatın ve **Yeni proje oluştur**' u seçin.
1. **Yeni proje oluştur** iletişim kutusunda **ASP.NET Core Web uygulaması** > **İleri**' yi seçin.
1. **Yeni projenizi yapılandırın** Iletişim kutusunda `ContosoUniversity` **Proje adı**' nı girin. Büyük harfler de dahil olmak üzere bu tam adı kullanmak önemlidir. bu nedenle, `namespace` Kod kopyalanırken her biri eşleşir.
1. **Oluştur**’u seçin.
1. **Yeni ASP.NET Core Web uygulaması oluştur** iletişim kutusunda şunları seçin:
    1. **.NET Core** ve **ASP.NET Core 5,0** açılır.
    1. **ASP.NET Core Web uygulaması**.
    1.  
       Oluştur ![ Yeni ASP.NET Core projesi iletişim kutusu](~/data/ef-rp/intro/_static/new-aspnet5.png)
    
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Bir terminalde, proje klasörünün oluşturulması gereken klasöre gidin.
* Bir Razor sayfa projesi ve yeni proje klasörü oluşturmak için aşağıdaki komutları çalıştırın `cd` :

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a>Site stilini ayarlayın

Aşağıdaki kodu kopyalayıp *Sayfalar/paylaşılan/_Layout. cshtml* dosyasına yapıştırın:

[!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

Düzen dosyası site üst bilgisini, alt bilgisini ve menüsünü ayarlar. Yukarıdaki kod aşağıdaki değişiklikleri yapar:

* Her "ContosoUniversity" öğesinin "Contoso Üniversitesi" olarak her oluşumu. Üç oluşum vardır.
* **Giriş** ve **Gizlilik** menü girişleri silinir.
* , **Öğrenciler**, **Kurslar**, **eğitmenler** ve **Departmanlar** **için girişler** eklenir.

*Pages/Index. cshtml* dosyasında, dosyanın içeriğini aşağıdaki kodla değiştirin:

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

Yukarıdaki kod, ASP.NET Core ile ilgili metnin yerine bu uygulamayla ilgili metni koyar.

Giriş sayfasının göründüğünü doğrulamak için uygulamayı çalıştırın.

## <a name="the-data-model"></a>Veri modeli

Aşağıdaki bölümler bir veri modeli oluşturur:

![Kurs-kayıt-öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

Bir öğrenci herhangi bir sayıda kursa kaydolabilir ve bir kurs, kayıtlı sayıda öğrenciye sahip olabilir.

## <a name="the-student-entity"></a>Öğrenci varlığı

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

* Proje klasöründe bir *modeller* klasörü oluşturun. 

* Aşağıdaki kodla *modeller/öğrenci. cs* oluşturun:

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

`ID`Özelliği, bu sınıfa karşılık gelen veritabanı tablosunun birincil anahtar sütunu olur. Varsayılan olarak, EF Core `ID` birincil anahtar olarak adlandırılan bir özelliği Yorumlar `classnameID` . Bu nedenle birincil anahtar sınıfı için alternatif otomatik olarak tanınan ad `Student` olur `StudentID` . Daha fazla bilgi için bkz. [EF Core-anahtarlar](/ef/core/modeling/keys?tabs=data-annotations).

`Enrollments`Özelliği bir [Gezinti özelliğidir](/ef/core/modeling/relationships). Gezinti özellikleri, bu varlıkla ilgili diğer varlıkları tutar. Bu durumda, `Enrollments` bir `Student` varlığın özelliği `Enrollment` söz konusu öğrenciye ilişkin tüm varlıkları barındırır. Örneğin, veritabanındaki bir öğrenci satırında iki ilişkili kayıt satırı varsa, `Enrollments` gezinti özelliği bu Iki kayıt varlığını içerir. 

Veritabanında, bir kayıt satırı, Studentitıd sütunu öğrencinin ID değerini içeriyorsa bir öğrenci satırıyla ilgilidir. Örneğin, bir öğrenci satırının ID = 1 olduğunu varsayalım. İlgili kayıt satırları Studentitıd = 1 olacaktır. Studentitıd, kayıt tablosundaki bir *yabancı anahtardır* . 

`Enrollments`Özelliği, `ICollection<Enrollment>` birden çok ilgili kayıt varlığı olabileceğinden, olarak tanımlanır. Veya gibi diğer koleksiyon türlerini kullanabilirsiniz `List<Enrollment>` `HashSet<Enrollment>` . Kullanıldığında `ICollection<Enrollment>` , EF Core `HashSet<Enrollment>` Varsayılan olarak bir koleksiyon oluşturur.

## <a name="the-enrollment-entity"></a>Kayıt varlığı

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

Aşağıdaki kodla *modeller/kayıt. cs* oluşturun:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

`EnrollmentID`Bu özellik birincil anahtardır; bu varlık kendi başına değil, `classnameID` kendi modelini kullanır `ID` . Bir üretim veri modeli için bir model seçin ve bunu tutarlı bir şekilde kullanın. Bu öğretici her ikisinin de yalnızca bir iş olduğunu göstermek için kullanır. `ID`Olmadan kullanmak `classname` , bazı veri modeli değişikliklerinin uygulanmasını kolaylaştırır.

`Grade`Özelliği bir `enum` . Tür bildiriminden sonraki soru işareti, `Grade` `Grade` özelliğin [null yapılabilir](/dotnet/csharp/programming-guide/nullable-types/)olduğunu gösterir. Null olan bir sınıf sıfır bir sınıf null değerinden farklıdır &mdash; veya henüz atanmamış olur.

`StudentID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Student` . Bir varlık `Enrollment` bir `Student` varlıkla ilişkilendirilir, bu nedenle özellik tek bir varlık içerir `Student` .

`CourseID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Course` . Bir `Enrollment` varlık bir `Course` varlıkla ilişkilendirilir.

EF Core bir özelliği, adlandırılmış ise yabancı anahtar olarak yorumlar `<navigation property name><primary key property name>` . Örneğin, `StudentID` `Student` `Student` nesnenin birincil anahtarı olduğundan, gezinti özelliği için yabancı anahtardır `ID` . Yabancı anahtar özellikleri de adlandırılmış olabilir `<primary key property name>` . Örneğin, `CourseID` `Course` varlığın birincil anahtarı olduğundan `CourseID` .

## <a name="the-course-entity"></a>Kurs varlığı

![Kurs varlık diyagramı](intro/_static/course-entity.png)

Aşağıdaki kodla *modeller/kurs. cs* oluşturun:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

`Enrollments`Özelliği bir gezinti özelliğidir. Bir `Course` varlık herhangi bir sayıda varlıkla ilişkili olabilir `Enrollment` .

`DatabaseGenerated`Özniteliği, uygulamanın veritabanını oluşturmak yerine birincil anahtarı belirtmesini sağlar.

Derleyici hatası olmadığını doğrulamak için projeyi derleyin.

## <a name="scaffold-student-pages"></a>Yapı iskelesi öğrenci sayfaları

Bu bölümde, oluşturmak için ASP.NET Core scafkatlama aracını kullanırsınız:

* EF Core `DbContext` sınıfı. Bağlam, belirli bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır. <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>Sınıfından türetilir.
* Razor varlık için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemlerini işleyen sayfalar `Student` .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Sayfalar/öğrenciler* klasörü oluşturun.
* **Çözüm Gezgini**, *Sayfalar/öğrenciler* klasörüne sağ tıklayın ve  > **yeni yapı iskelesi Ekle öğesini** seçin.
* **Yeni yapı Iskelesi öğesi Ekle** iletişim kutusunda:
  * Sol sekmede, **yüklü > ortak > Razor sayfaları** ' nı seçin
  * **Razor Entity Framework (CRUD) Ekle kullanarak sayfa** seçin > .
* **Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunda:
  * **Model sınıfı** açılır penceresinde **öğrenci (Contosouniversity. modeller)** öğesini seçin.
  * **Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin.
    * Veri bağlamı adını yerine End ile değiştirin `SchoolContext` `ContosoUniversityContext` . Güncelleştirilmiş bağlam adı: `ContosoUniversity.Data.SchoolContext`
    * Veri bağlamı sınıfını eklemeyi bitirmeden **Ekle** ' yi seçin.
   * **Ekle** ' yi seçerek **Razor sayfa ekle** iletişim kutusunu sona erdirin.

Aşağıdaki paketler otomatik olarak yüklenir:

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Gerekli NuGet paketlerini yüklemek için aşağıdaki .NET Core CLI komutlarını çalıştırın:

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   Yapı iskelesi için Microsoft. VisualStudio. Web. CodeGeneration. Design paketi gereklidir. Uygulama SQL Server kullanmayabilse de, scafkatlama aracı SQL Server paketine ihtiyaç duyuyor.

* *Sayfalar/öğrenciler* klasörü oluşturun.

* [ASPNET-CodeGenerator scafkatlama aracı](xref:fundamentals/tools/dotnet-aspnet-codegenerator)'nı yüklemek için aşağıdaki komutu çalıştırın.

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* Fkatlama öğrenci sayfalarını iskele almak için aşağıdaki komutu çalıştırın.

  **Windows üzerinde**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  **MacOS veya Linux üzerinde**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

Önceki adım başarısız olursa, projeyi derleyin ve iskele adımını yeniden deneyin.

Yapı iskelesi işlemi:

* Razor *Sayfalar/öğrenciler* klasöründe sayfa oluşturur:
  * *. Cshtml* ve *Create.cshtml.cs* oluşturma
  * *Delete. cshtml* ve *delete.cshtml.cs*
  * *Details. cshtml* ve *details.cshtml.cs*
  * *. Cshtml* ve *Edit.cshtml.cs* Düzenle
  * *Index. cshtml* ve *Index.cshtml.cs*
* *Data/SchoolContext. cs* oluşturur.
* *Startup.cs* içinde bağımlılık eklenmesine bağlam ekler.
* Öğesine bir veritabanı bağlantı dizesi ekler *appsettings.json* .

## <a name="database-connection-string"></a>Veritabanı bağlantı dizesi

Scafkatlama aracı dosyada bir bağlantı dizesi oluşturur *appsettings.json* .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Bağlantı dizesi [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)belirtir:

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

LocalDB, SQL Server Express veritabanı altyapısının hafif bir sürümüdür ve üretim kullanımı için değil uygulama geliştirmeye yöneliktir. Varsayılan olarak, LocalDB dizinde *. mdf* dosyaları oluşturur `C:/Users/<user>` .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

*. Db* için SQLite bağlantı dizesini kısaltın:

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a>Veritabanı bağlam sınıfını Güncelleştir

Belirli bir veri modeli için EF Core işlevselliğini koordine eden ana sınıf veritabanı bağlamı sınıfıdır. Bağlam [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir. Bağlam, veri modeline hangi varlıkların ekleneceğini belirtir. Bu projede, sınıfı olarak adlandırılır `SchoolContext` .

*Data/SchoolContext. cs* öğesini şu kodla güncelleştirin:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

Yukarıdaki kod tekil ile çoğul arasında değişir `DbSet<Student> Student` `DbSet<Student> Students` . RazorSayfa kodunun yeni adla eşleşmesini sağlamak için `DBSet` , ' den küresel bir değişiklik yapın:`_context.Student.`
Hedef: `_context.Students.`

8 oluşum vardır.

Bir varlık kümesi birden çok varlık içerdiğinden birçok geliştirici, `DBSet` özellik adlarının çoğul olması gerektiğini tercih eder.

Vurgulanan kod:

* Her bir varlık kümesi için bir [Dbset \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur. EF Core terminoloji:
  * Bir varlık kümesi, genellikle bir veritabanı tablosuna karşılık gelir.
  * Bir varlık, tablodaki bir satıra karşılık gelir.
* Çağırır <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> . `OnModelCreating`:
  * Başlatıldığı zaman çağrılır `SchoolContext` , ancak model kilitlenmeden önce ve bağlamı başlatmak için kullanılır.
  * Daha sonraki öğreticide, `Student` varlığın diğer varlıklara başvuruları olacağı için gereklidir.
  <!-- Review, OnModelCreating needs review -->

Derleyici hatası olmadığını doğrulamak için projeyi derleyin.

## <a name="startupcs"></a>Startup.cs

ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur. Gibi hizmetler, `SchoolContext` uygulamanın başlatılması sırasında bağımlılık ekleme ile kaydedilir. Sayfalar gibi bu hizmetleri gerektiren bileşenler Razor , bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır. Bir veritabanı bağlamı örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.

Scafkatlama Aracı, bağlam sınıfını bağımlılık ekleme kapsayıcısına otomatik olarak kaydetti.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Aşağıdaki Vurgulanan satırlar scaffolder tarafından eklendi:

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Desteği çağrıları tarafından eklenen kodu doğrulayın `UseSqlite` .

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

Üretim veritabanını kullanma hakkında bilgi için bkz. [geliştirme Için SQLite kullanma, üretime yönelik SQL Server](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) .

---

Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir. Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *appsettings.json* .

### <a name="add-the-database-exception-filter"></a>Veritabanı özel durum filtresini ekleme

<xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> `ConfigureServices` Aşağıdaki kodda gösterildiği gibi öğesine ekleyin:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

[Microsoft. aspnetcore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet paketini ekleyin.

Şu şekilde, NuGet paketini eklemek için aşağıdakileri girin:

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore`NuGet paketi, Entity Framework Core hata sayfaları için ASP.NET Core ara yazılım sağlar. Bu ara yazılım Entity Framework Core geçişlerle hataları algılamaya ve tanılamanıza yardımcı olur.

, `AddDatabaseDeveloperPageExceptionFilter` [Geliştirme ortamında](xref:fundamentals/environments)yararlı hata bilgileri sağlar.

## <a name="create-the-database"></a>Veritabanını oluşturma

Mevcut değilse veritabanını oluşturmak için *program.cs* güncelleştirin:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

Bağlam için bir veritabanı varsa, [Ensuyeniden](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) oluşturma yöntemi hiçbir eylemde bulunmaz. Veritabanı yoksa, veritabanını ve şemayı oluşturur. `EnsureCreated` veri modeli değişikliklerini işlemek için aşağıdaki iş akışını sunar:

* Veritabanını silin. Mevcut veriler kaybolur.
* Veri modelini değiştirin. Örneğin, bir alan ekleyin `EmailAddress` .
* Uygulamayı çalıştırın.
* `EnsureCreated` yeni şemaya sahip bir veritabanı oluşturur.

Bu iş akışı, verileri korumanıza gerek olmadığı sürece, şema hızlı bir şekilde gelişen zaman geliştirme aşamasında iyi bir şekilde gerçekleştirilir. Veritabanına girilen verilerin korunması gerektiğinde bu durum farklıdır. Bu durumda, geçişleri kullanın.

Öğretici serisinde daha sonra tarafından oluşturulan veritabanını siler `EnsureCreated` ve bunun yerine geçişleri kullanırsınız. Tarafından oluşturulan bir veritabanı, `EnsureCreated` geçişler kullanılarak güncelleştirilemiyor.

### <a name="test-the-app"></a>Uygulamayı test etme

* Uygulamayı çalıştırın.
* **Öğrenciler** bağlantısını seçin ve ardından **Yeni oluştur**.
* Düzenle, Ayrıntılar ve Sil bağlantılarını test edin.

## <a name="seed-the-database"></a>Veritabanını çekirdek

`EnsureCreated`Yöntemi boş bir veritabanı oluşturur. Bu bölüm, veritabanını test verileriyle dolduran kodu ekler.

Aşağıdaki kodla *veri/Dbınizer. cs* oluşturun:
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  Kod, veritabanında herhangi bir öğrenci olup olmadığını denetler. Öğrenci yoksa, veritabanına test verileri ekler. Performansı iyileştirmek için Koleksiyonlar yerine diziler halinde test verileri oluşturur `List<T>` .

*Program.cs* içinde, `EnsureCreated` çağrıyı bir çağrı ile değiştirin `DbInitializer.Initialize` :

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Çalışıyorsa uygulamayı durdurun ve **Paket Yöneticisi konsolunda** (PMC) aşağıdaki komutu çalıştırın:

```powershell
Drop-Database -Confirm
```

`Y`Veritabanını silmek için ile yanıtlayın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Çalışıyorsa uygulamayı durdurun ve *cu. db* dosyasını silin.

---

* Uygulamayı yeniden başlatın.
* Sağlanan verileri görmek için öğrenciler sayfasını seçin.

## <a name="view-the-database"></a>Veritabanını görüntüleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio 'daki **Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.
* SSOX 'te, **(LocalDB) \MSSQLLocalDB > veritabanları > SchoolContext-{GUID}** öğesini seçin. Veritabanı adı, daha önce belirttiğiniz bağlam adından ve bir tire ve bir GUID ile oluşturulur.
* **Tables** düğümünü genişletin.
* Oluşturulan sütunları ve tabloya yerleştirilen satırları görmek için **öğrenci** tablosuna sağ tıklayın ve **verileri görüntüle** ' ye tıklayın.
* Modelin tablo şemasına nasıl eşlendiğini görmek için **öğrenci** tablosuna sağ tıklayın ve **kodu görüntüle** ' ye tıklayın `Student` `Student` .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Veritabanı şemasını ve sağlanan verileri görüntülemek için SQLite aracınızı kullanın. Veritabanı dosyası *cu. db* olarak adlandırılır ve proje klasöründe bulunur.

---

## <a name="asynchronous-code"></a>Zaman uyumsuz kod

Zaman uyumsuz programlama, ASP.NET Core ve EF Core için varsayılan moddur.

Web sunucusunda sınırlı sayıda iş parçacığı bulunur ve yüksek yük durumlarında tüm kullanılabilir iş parçacıkları kullanımda olabilir. Bu durumda, sunucu, iş parçacıkları boşaltılana kadar yeni istekleri işleyemez. Zaman uyumlu kod ile, çok sayıda iş parçacığı g/ç 'nin tamamlanmasını beklediği için iş çalışmadıklarında bağlı olabilir. Zaman uyumsuz kod ile, bir işlem g/ç 'yi tamamlanmayı beklerken, sunucunun diğer istekleri işlemek için kullanması için iş parçacığı serbest bırakılır. Sonuç olarak, zaman uyumsuz kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucu gecikmeksizin daha fazla trafiği işleyebilir.

Zaman uyumsuz kod, çalışma zamanında az miktarda yük getirir. Düşük trafik durumlarında, performans artışı göz ardı edilebilir, ancak yüksek trafik durumları için olası performans iyileştirmesi oldukça önemlidir.

Aşağıdaki kodda, [Async](/dotnet/csharp/language-reference/keywords/async) anahtar sözcüğü, `Task` return değeri, `await` anahtar sözcüğü ve `ToListAsync` yöntemi kodu zaman uyumsuz olarak yürütür.

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* `async`Anahtar sözcüğü derleyiciye şunu söyler:
  * Yöntem gövdesinin parçaları için geri çağrılar oluşturun.
  * Döndürülen [görev](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) nesnesini oluşturun.
* `Task`Dönüş türü, devam eden işi temsil eder.
* `await`Anahtar sözcüğü, derleyicinin yöntemi iki parçaya böetmesine neden olur. İlk bölüm, zaman uyumsuz olarak başlatılan işlemle biter. İkinci bölüm, işlem tamamlandığında çağrılan bir geri çağırma yöntemine konur.
* `ToListAsync` , Uzantı yönteminin zaman uyumsuz sürümüdür `ToList` .

EF Core kullanan zaman uyumsuz kodu yazarken dikkat edilmesi gereken bazı şeyler:

* Yalnızca sorguları veya komutlarının veritabanına gönderilmesine neden olan deyimler zaman uyumsuz olarak yürütülür. ,, `ToListAsync` , `SingleOrDefaultAsync` `FirstOrDefaultAsync` Ve içerir `SaveChangesAsync` . Yalnızca, gibi bir değiştiren deyimler içermez `IQueryable` `var students = context.Students.Where(s => s.LastName == "Davolio")` .
* EF Core bağlamı iş parçacığı açısından güvenli değildir: paralel olarak birden çok işlem yapmayı denemeyin.
* Zaman uyumsuz kodun performans avantajlarından yararlanmak için, veritabanına sorgu gönderen EF Core yöntemleri çağırıyorsa kitaplık paketlerinin (örneğin, sayfalama için) zaman uyumsuz olarak kullanılacağını doğrulayın.

.NET 'te zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. Async [and await ile](/dotnet/csharp/programming-guide/concepts/async/)zaman uyumsuz [genel bakış](/dotnet/standard/async) ve zaman uyumsuz programlama

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a>Performansla ilgili önemli noktalar

Genel olarak, bir Web sayfası rastgele sayıda satır yüklememelidir. Bir sorgu, sayfalama veya sınırlandırma yaklaşımını kullanmalıdır. Örneğin, önceki sorgu `Take` döndürülen satırları sınırlamak için kullanabilir:

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

Bir görünümde büyük bir tablonun numaralandırılması, bir veritabanı özel durumu numaralandırma aracılığıyla bir bölüm oluşursa kısmen oluşturulmuş bir HTTP 200 yanıtı döndürebilir.

<xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> Varsayılan olarak 1024 ' dir. Aşağıdaki kod kümeleri `MaxModelBindingCollectionSize` :

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

Sayfalama daha sonra öğreticide ele alınmıştır.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="step-by-step"]
> [Sonraki öğretici](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

Bu, bir [ASP.NET Core Razor sayfaları](xref:razor-pages/index) UYGULAMASıNDA Entity Framework (EF) çekirdeğini nasıl kullanacağınızı gösteren bir öğretici serisinin ilkisidir. Öğreticiler, kurgusal bir Contoso Üniversitesi için bir Web sitesi oluşturur. Site, öğrenci giriş, kurs oluşturma ve eğitmen atamaları gibi işlevleri içerir. Öğretici ilk kod yaklaşımını kullanır. Bu öğreticiyi veritabanı ilk yaklaşımı kullanarak takip eden bilgiler için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/16897)bakın.

[Tamamlanmış uygulamayı indirin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Yönergeleri indirin](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Önkoşullar

* Sayfalarla yeni başladıysanız Razor , bunu başlatmadan önce [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) öğreticisini izleyin.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a>Veritabanı altyapıları

Visual Studio yönergeleri, yalnızca Windows üzerinde çalışan bir SQL Server Express sürümü olan [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)'yi kullanır.

Visual Studio Code yönergeler, platformlar arası bir veritabanı altyapısı olan [SQLite](https://www.sqlite.org/)kullanır.

SQLite kullanmayı seçerseniz, SQLite [Için DB tarayıcısı](https://sqlitebrowser.org/)gibi bir SQLite veritabanını yönetmek ve görüntülemek için üçüncü taraf bir araç indirip yükleyin.

## <a name="troubleshooting"></a>Sorun giderme

Giderebileceğiniz bir sorunla karşılaşırsanız, kodunuzu [Tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)karşılaştırın. Yardım almanın iyi bir yolu, [ASP.NET Core etiketi](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF Core etiketi](https://stackoverflow.com/questions/tagged/entity-framework-core)kullanılarak StackOverflow.com 'e bir soru göndererek.

## <a name="the-sample-app"></a>Örnek uygulama

Bu öğreticilerde oluşturulan uygulama, temel bir üniversite web sitesidir. Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleştirebilir. Öğreticide oluşturulan ekranlardan bazıları aşağıda verilmiştir.

![Öğrenciler Dizin sayfası](intro/_static/students-index30.png)

![Öğrenciler düzenleme sayfası](intro/_static/student-edit30.png)

Bu sitenin kullanıcı arabirimi stili yerleşik proje şablonlarına dayalıdır. Öğreticinin odağı, Kullanıcı arabirimini nasıl özelleştireceğinizi değil EF Core kullanma konusunda yer alır.

Tamamlanan projenin kaynak kodunu almak için sayfanın üst kısmındaki bağlantıyı izleyin. *Cu30* klasörü, öğreticinin ASP.NET Core 3,0 sürümü için kod içerir. 1-7 öğreticileri için kodun durumunu yansıtan dosyalar *cu30snapshots* klasöründe bulunabilir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Tamamlanmış projeyi indirdikten sonra uygulamayı çalıştırmak için:

* Projeyi derleyin.
* Paket Yöneticisi konsolu 'nda (PMC) aşağıdaki komutu çalıştırın:

  ```powershell
  Update-Database
  ```

* Veritabanını temel alarak projeyi çalıştırın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Tamamlanmış projeyi indirdikten sonra uygulamayı çalıştırmak için:

* *Contosouniversity. csproj* öğesini silin ve *Contosoüniversıtysqlite. csproj* öğesini *contosouniversity. csproj* olarak yeniden adlandırın.
* *Program.cs*' de, açıklama dışarı bu `#define Startup` şekilde `StartupSQLite` kullanılır.
* *appSettings.js* silin ve üzerinde *appSettings.js* *appSettingsSQLite.js* yeniden adlandırın.
* *Geçişler* klasörünü silin ve *migrationssql* öğesini *geçişlerle* yeniden adlandırın.
* İçin genel arama yapın `#if SQLiteVersion` ve `#if SQLiteVersion` ilgili `#endif` ifadeyi kaldırın.
* Projeyi derleyin.
* Proje klasöründeki bir komut isteminde aşağıdaki komutları çalıştırın:

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* SQLite aracında şu SQL ifadesini çalıştırın:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* Veritabanını temel alarak projeyi çalıştırın.

---

## <a name="create-the-web-app-project"></a>Web uygulaması projesi oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.
* **ASP.NET Core Web uygulaması**' nı seçin.
* Projeyi *Contosouniversity* olarak adlandırın. Büyük harfler de dahil olmak üzere bu tam adı kullanmak önemlidir, bu nedenle kod kopyalanıp yapıştırılırken ad alanları eşleşir.
* Açılan menüden **.NET Core** ve **3,0 ASP.NET Core** seçin ve ardından **Web uygulaması**' nı seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Bir terminalde, proje klasörünün oluşturulması gereken klasöre gidin.

* Bir Razor sayfa projesi ve yeni proje klasörü oluşturmak için aşağıdaki komutları çalıştırın `cd` :

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a>Site stilini ayarlayın

*Sayfa/paylaşılan/_Layout. cshtml*'yi güncelleştirerek site üst bilgisini, alt bilgisini ve menüsünü ayarlayın:

* "ContosoUniversity" öğesinin her oluşumunu "Contoso Üniversitesi" olarak değiştirin. Üç oluşum vardır.

* **Giriş** ve **Gizlilik** menü girişlerini silin ve **hakkında**, **öğrenciler**, **Kurslar**, **eğitmenler** ve **Departmanlar** için girişler ekleyin.

Değişiklikler vurgulanır.

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

*Pages/Index. cshtml* dosyasında, ASP.NET Core hakkındaki metni bu uygulamayla ilgili metinle değiştirmek için dosyanın içeriğini aşağıdaki kodla değiştirin:

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

Giriş sayfasının göründüğünü doğrulamak için uygulamayı çalıştırın.

## <a name="the-data-model"></a>Veri modeli

Aşağıdaki bölümler bir veri modeli oluşturur:

![Kurs-kayıt-öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

Bir öğrenci herhangi bir sayıda kursa kaydolabilir ve bir kurs, kayıtlı sayıda öğrenciye sahip olabilir.

## <a name="the-student-entity"></a>Öğrenci varlığı

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

* Proje klasöründe bir *modeller* klasörü oluşturun.
* Aşağıdaki kodla *modeller/öğrenci. cs* oluşturun:

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

`ID`Özelliği, bu sınıfa karşılık gelen veritabanı tablosunun birincil anahtar sütunu olur. Varsayılan olarak, EF Core `ID` birincil anahtar olarak adlandırılan bir özelliği Yorumlar `classnameID` . Bu nedenle birincil anahtar sınıfı için alternatif otomatik olarak tanınan ad `Student` olur `StudentID` . Daha fazla bilgi için bkz. [EF Core-anahtarlar](/ef/core/modeling/keys?tabs=data-annotations).

`Enrollments`Özelliği bir [Gezinti özelliğidir](/ef/core/modeling/relationships). Gezinti özellikleri, bu varlıkla ilgili diğer varlıkları tutar. Bu durumda, `Enrollments` bir `Student` varlığın özelliği `Enrollment` söz konusu öğrenciye ilişkin tüm varlıkları barındırır. Örneğin, veritabanındaki bir öğrenci satırında iki ilişkili kayıt satırı varsa, `Enrollments` gezinti özelliği bu Iki kayıt varlığını içerir. 

Veritabanında, bir kayıt satırı, Studentitıd sütunu öğrencinin ID değerini içeriyorsa bir öğrenci satırıyla ilgilidir. Örneğin, bir öğrenci satırının ID = 1 olduğunu varsayalım. İlgili kayıt satırları Studentitıd = 1 olacaktır. Studentitıd, kayıt tablosundaki bir *yabancı anahtardır* . 

`Enrollments`Özelliği, `ICollection<Enrollment>` birden çok ilgili kayıt varlığı olabileceğinden, olarak tanımlanır. Veya gibi diğer koleksiyon türlerini kullanabilirsiniz `List<Enrollment>` `HashSet<Enrollment>` . Kullanıldığında `ICollection<Enrollment>` , EF Core `HashSet<Enrollment>` Varsayılan olarak bir koleksiyon oluşturur.

## <a name="the-enrollment-entity"></a>Kayıt varlığı

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

Aşağıdaki kodla *modeller/kayıt. cs* oluşturun:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

`EnrollmentID`Bu özellik birincil anahtardır; bu varlık kendi başına değil, `classnameID` kendi modelini kullanır `ID` . Bir üretim veri modeli için bir model seçin ve bunu tutarlı bir şekilde kullanın. Bu öğretici her ikisinin de yalnızca bir iş olduğunu göstermek için kullanır. `ID`Olmadan kullanmak `classname` , bazı veri modeli değişikliklerinin uygulanmasını kolaylaştırır.

`Grade`Özelliği bir `enum` . Tür bildiriminden sonraki soru işareti, `Grade` `Grade` özelliğin [null yapılabilir](/dotnet/csharp/programming-guide/nullable-types/)olduğunu gösterir. Null olan bir sınıf sıfır bir sınıf null değerinden farklıdır &mdash; veya henüz atanmamış olur.

`StudentID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Student` . Bir varlık `Enrollment` bir `Student` varlıkla ilişkilendirilir, bu nedenle özellik tek bir varlık içerir `Student` .

`CourseID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Course` . Bir `Enrollment` varlık bir `Course` varlıkla ilişkilendirilir.

EF Core bir özelliği, adlandırılmış ise yabancı anahtar olarak yorumlar `<navigation property name><primary key property name>` . Örneğin, `StudentID` `Student` `Student` nesnenin birincil anahtarı olduğundan, gezinti özelliği için yabancı anahtardır `ID` . Yabancı anahtar özellikleri de adlandırılmış olabilir `<primary key property name>` . Örneğin, `CourseID` `Course` varlığın birincil anahtarı olduğundan `CourseID` .

## <a name="the-course-entity"></a>Kurs varlığı

![Kurs varlık diyagramı](intro/_static/course-entity.png)

Aşağıdaki kodla *modeller/kurs. cs* oluşturun:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

`Enrollments`Özelliği bir gezinti özelliğidir. Bir `Course` varlık herhangi bir sayıda varlıkla ilişkili olabilir `Enrollment` .

`DatabaseGenerated`Özniteliği, uygulamanın veritabanını oluşturmak yerine birincil anahtarı belirtmesini sağlar.

Derleyici hatası olmadığını doğrulamak için projeyi derleyin.

## <a name="scaffold-student-pages"></a>Yapı iskelesi öğrenci sayfaları

Bu bölümde, oluşturmak için ASP.NET Core scafkatlama aracını kullanırsınız:

* EF Core *bağlamı* sınıfı. Bağlam, belirli bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır. `Microsoft.EntityFrameworkCore.DbContext`Sınıfından türetilir.
* Razor varlık için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemlerini işleyen sayfalar `Student` .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Sayfalar* klasöründe bir *öğrenciler* klasörü oluşturun.
* **Çözüm Gezgini**, *Sayfalar/öğrenciler* klasörüne sağ tıklayın ve  > **yeni yapı iskelesi Ekle öğesini** seçin.
* **Yapı iskelesi Ekle** iletişim kutusunda **Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > .
* **Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunda:
  * **Model sınıfı** açılır penceresinde **öğrenci (Contosouniversity. modeller)** öğesini seçin.
  * **Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin.
  * *Contosouniversity. modeller. Contosoüniversıtycontext* olan veri bağlamı adını *Contosouniversity. Data. SchoolContext* olarak değiştirin.
  * **Ekle**’yi seçin.

Aşağıdaki paketler otomatik olarak yüklenir:

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Gerekli NuGet paketlerini yüklemek için aşağıdaki .NET Core CLI komutlarını çalıştırın:
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  Yapı iskelesi için Microsoft. VisualStudio. Web. CodeGeneration. Design paketi gereklidir. Uygulama SQL Server kullanmayabilse de, scafkatlama aracı SQL Server paketine ihtiyaç duyuyor.

* *Sayfalar/öğrenciler* klasörü oluşturun.

* [ASPNET-CodeGenerator scafkatlama aracı](xref:fundamentals/tools/dotnet-aspnet-codegenerator)'nı yüklemek için aşağıdaki komutu çalıştırın.

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* Fkatlama öğrenci sayfalarını iskele almak için aşağıdaki komutu çalıştırın.

  **Windows üzerinde**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  **MacOS veya Linux üzerinde**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

Önceki adımla ilgili bir sorununuz varsa, projeyi derleyin ve yapı iskelesi adımını yeniden deneyin.

Yapı iskelesi işlemi:

* Razor *Sayfalar/öğrenciler* klasöründe sayfa oluşturur:
  * *. Cshtml* ve *Create.cshtml.cs* oluşturma
  * *Delete. cshtml* ve *delete.cshtml.cs*
  * *Details. cshtml* ve *details.cshtml.cs*
  * *. Cshtml* ve *Edit.cshtml.cs* Düzenle
  * *Index. cshtml* ve *Index.cshtml.cs*
* *Data/SchoolContext. cs* oluşturur.
* *Startup.cs* içinde bağımlılık eklenmesine bağlam ekler.
* Öğesine bir veritabanı bağlantı dizesi ekler *appsettings.json* .

## <a name="database-connection-string"></a>Veritabanı bağlantı dizesi

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

*appsettings.json* Dosya, [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)bağlantı dizesini belirtir.

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

LocalDB, SQL Server Express veritabanı altyapısının hafif bir sürümüdür ve üretim kullanımı için değil uygulama geliştirmeye yöneliktir. Varsayılan olarak, LocalDB dizinde *. mdf* dosyaları oluşturur `C:/Users/<user>` .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Bağlantı dizesini *cu. db* adlı bir SQLite veritabanı dosyasını işaret etmek üzere değiştirin:

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a>Veritabanı bağlam sınıfını Güncelleştir

Belirli bir veri modeli için EF Core işlevselliğini koordine eden ana sınıf veritabanı bağlamı sınıfıdır. Bağlam [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir. Bağlam, veri modeline hangi varlıkların ekleneceğini belirtir. Bu projede, sınıfı olarak adlandırılır `SchoolContext` .

*Data/SchoolContext. cs* öğesini şu kodla güncelleştirin:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

Vurgulanan kod, her bir varlık kümesi için bir [Dbset \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur. EF Core terminoloji:

* Bir varlık kümesi, genellikle bir veritabanı tablosuna karşılık gelir.
* Bir varlık, tablodaki bir satıra karşılık gelir.

Bir varlık kümesi birden çok varlık içerdiğinden, DBSet özellikleri çoğul adlar olmalıdır. Yapı iskelesi aracı bir `Student` dbset oluşturduğundan, bu adım çoğul olarak değişir `Students` . 

RazorSayfa kodunun yeni DBSet adıyla eşleşmesini sağlamak için, tüm projesi genelinde küresel bir değişiklik yapın `_context.Student` `_context.Students` .  8 oluşum vardır.

Derleyici hatası olmadığını doğrulamak için projeyi derleyin.

## <a name="startupcs"></a>Startup.cs

ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur. Hizmetler (EF Core veritabanı bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir. Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır. Bir veritabanı bağlamı örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.

Scafkatlama Aracı, bağlam sınıfını bağımlılık ekleme kapsayıcısına otomatik olarak kaydetti.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* `ConfigureServices`' De, vurgulanan satırlar scaffolder tarafından eklenmiştir:

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* ' De `ConfigureServices` , desteği tarafından eklenen kodun çağrılarından emin olun `UseSqlite` .

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir. Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *appsettings.json* .

## <a name="create-the-database"></a>Veritabanını oluşturma

Mevcut değilse veritabanını oluşturmak için *program.cs* güncelleştirin:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

Bağlam için bir veritabanı varsa, [Ensuyeniden](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) oluşturma yöntemi hiçbir eylemde bulunmaz. Veritabanı yoksa, veritabanını ve şemayı oluşturur. `EnsureCreated` veri modeli değişikliklerini işlemek için aşağıdaki iş akışını sunar:

* Veritabanını silin. Mevcut veriler kaybolur.
* Veri modelini değiştirin. Örneğin, bir alan ekleyin `EmailAddress` .
* Uygulamayı çalıştırın.
* `EnsureCreated` yeni şemaya sahip bir veritabanı oluşturur.

Bu iş akışı, verileri korumanıza gerek olmadığı sürece, şema hızlı bir şekilde gelişen zaman geliştirme aşamasında iyi bir şekilde gerçekleştirilir. Veritabanına girilen verilerin korunması gerektiğinde bu durum farklıdır. Bu durumda, geçişleri kullanın.

Öğretici serisinde daha sonra tarafından oluşturulan veritabanını siler `EnsureCreated` ve bunun yerine geçişleri kullanırsınız. Tarafından oluşturulan bir veritabanı, `EnsureCreated` geçişler kullanılarak güncelleştirilemiyor.

### <a name="test-the-app"></a>Uygulamayı test etme

* Uygulamayı çalıştırın.
* **Öğrenciler** bağlantısını seçin ve ardından **Yeni oluştur**.
* Düzenle, Ayrıntılar ve Sil bağlantılarını test edin.

## <a name="seed-the-database"></a>Veritabanını çekirdek

`EnsureCreated`Yöntemi boş bir veritabanı oluşturur. Bu bölüm, veritabanını test verileriyle dolduran kodu ekler.

Aşağıdaki kodla *veri/Dbınizer. cs* oluşturun:
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  Kod, veritabanında herhangi bir öğrenci olup olmadığını denetler. Öğrenci yoksa, veritabanına test verileri ekler. Performansı iyileştirmek için Koleksiyonlar yerine diziler halinde test verileri oluşturur `List<T>` .

* *Program.cs* içinde, `EnsureCreated` çağrıyı bir çağrı ile değiştirin `DbInitializer.Initialize` :

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Çalışıyorsa uygulamayı durdurun ve **Paket Yöneticisi konsolunda** (PMC) aşağıdaki komutu çalıştırın:

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Çalışıyorsa uygulamayı durdurun ve *cu. db* dosyasını silin.

---

* Uygulamayı yeniden başlatın.

* Sağlanan verileri görmek için öğrenciler sayfasını seçin.

## <a name="view-the-database"></a>Veritabanını görüntüleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio 'daki **Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.
* SSOX 'te, **(LocalDB) \MSSQLLocalDB > veritabanları > SchoolContext-{GUID}** öğesini seçin. Veritabanı adı, daha önce belirttiğiniz bağlam adından ve bir tire ve bir GUID ile oluşturulur.
* **Tables** düğümünü genişletin.
* Oluşturulan sütunları ve tabloya yerleştirilen satırları görmek için **öğrenci** tablosuna sağ tıklayın ve **verileri görüntüle** ' ye tıklayın.
* Modelin tablo şemasına nasıl eşlendiğini görmek için **öğrenci** tablosuna sağ tıklayın ve **kodu görüntüle** ' ye tıklayın `Student` `Student` .

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Veritabanı şemasını ve sağlanan verileri görüntülemek için SQLite aracınızı kullanın. Veritabanı dosyası *cu. db* olarak adlandırılır ve proje klasöründe bulunur.

---

## <a name="asynchronous-code"></a>Zaman uyumsuz kod

Zaman uyumsuz programlama, ASP.NET Core ve EF Core için varsayılan moddur.

Web sunucusunda sınırlı sayıda iş parçacığı bulunur ve yüksek yük durumlarında tüm kullanılabilir iş parçacıkları kullanımda olabilir. Bu durumda, sunucu, iş parçacıkları boşaltılana kadar yeni istekleri işleyemez. Zaman uyumlu kodla, çok sayıda iş parçacığı, g/ç 'nin tamamlanmasını beklediği için aslında herhangi bir iş yapmadıklarında bağlı olabilir. Zaman uyumsuz kod ile, bir işlem g/ç 'yi tamamlanmayı beklerken, sunucunun diğer istekleri işlemek için kullanması için iş parçacığı serbest bırakılır. Sonuç olarak, zaman uyumsuz kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucu gecikmeksizin daha fazla trafiği işleyebilir.

Zaman uyumsuz kod, çalışma zamanında az miktarda yük getirir. Düşük trafik durumlarında, performans artışı göz ardı edilebilir, ancak yüksek trafik durumları için olası performans iyileştirmesi oldukça önemlidir.

Aşağıdaki kodda, [Async](/dotnet/csharp/language-reference/keywords/async) anahtar sözcüğü, `Task<T>` return değeri, `await` anahtar sözcüğü ve `ToListAsync` yöntemi kodu zaman uyumsuz olarak yürütür.

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* `async`Anahtar sözcüğü derleyiciye şunu söyler:
  * Yöntem gövdesinin parçaları için geri çağrılar oluşturun.
  * Döndürülen [görev](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) nesnesini oluşturun.
* `Task<T>`Dönüş türü, devam eden işi temsil eder.
* `await`Anahtar sözcüğü, derleyicinin yöntemi iki parçaya böetmesine neden olur. İlk bölüm, zaman uyumsuz olarak başlatılan işlemle biter. İkinci bölüm, işlem tamamlandığında çağrılan bir geri çağırma yöntemine konur.
* `ToListAsync` , Uzantı yönteminin zaman uyumsuz sürümüdür `ToList` .

EF Core kullanan zaman uyumsuz kodu yazarken dikkat edilmesi gereken bazı şeyler:

* Yalnızca sorguları veya komutlarının veritabanına gönderilmesine neden olan deyimler zaman uyumsuz olarak yürütülür. ,, `ToListAsync` , `SingleOrDefaultAsync` `FirstOrDefaultAsync` Ve içerir `SaveChangesAsync` . Yalnızca, gibi bir değiştiren deyimler içermez `IQueryable` `var students = context.Students.Where(s => s.LastName == "Davolio")` .
* EF Core bağlamı iş parçacığı açısından güvenli değildir: paralel olarak birden çok işlem yapmayı denemeyin.
* Zaman uyumsuz kodun performans avantajlarından yararlanmak için, veritabanına sorgu gönderen EF Core yöntemleri çağırıyorsa kitaplık paketlerinin (örneğin, sayfalama için) zaman uyumsuz olarak kullanılacağını doğrulayın.

.NET 'te zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. Async [and await ile](/dotnet/csharp/programming-guide/concepts/async/)zaman uyumsuz [genel bakış](/dotnet/standard/async) ve zaman uyumsuz programlama

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="step-by-step"]
> [Sonraki öğretici](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Contoso Üniversitesi örnek Web uygulaması, Razor Entity Framework (EF) Core kullanarak ASP.NET Core sayfalı bir uygulamanın nasıl oluşturulacağını gösterir.

Örnek uygulama, kurgusal bir Contoso Üniversitesi için bir Web sitesidir. Öğrenci giriş, kurs oluşturma ve eğitmen atamaları gibi işlevleri içerir. Bu sayfa, Contoso Üniversitesi örnek uygulamasının nasıl oluşturulacağını açıklayan bir öğretici serisinin ilkisidir.

[Tamamlanmış uygulamayı indirin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Yönergeleri indirin](xref:index#how-to-download-a-sample).

## <a name="prerequisites"></a>Önkoşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

[ Razor Sayfalarla](xref:razor-pages/index)benzerlik. Yeni programcılar, bu seriyi başlatmadan önce [sayfalarla çalışmaya başlama Razor sayfasını](xref:tutorials/razor-pages/razor-pages-start) tamamlamalıdır.

## <a name="troubleshooting"></a>Sorun giderme

Çözemiyoruz bir sorunla karşılaşırsanız, kodunuzun [Tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)karşılaştırılmasıyla genellikle çözümü bulabilirsiniz. [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) [için bir](https://stackoverflow.com/questions/tagged/asp.net-core) soru göndererek yardım almanın iyi bir yolu.

## <a name="the-contoso-university-web-app"></a>Contoso Üniversitesi web uygulaması

Bu öğreticilerde oluşturulan uygulama, temel bir üniversite web sitesidir.

Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleştirebilir. Öğreticide oluşturulan ekranlardan bazıları aşağıda verilmiştir.

![Öğrenciler Dizin sayfası](intro/_static/students-index.png)

![Öğrenciler düzenleme sayfası](intro/_static/student-edit.png)

Bu sitenin kullanıcı arabirimi stili yerleşik şablonlar tarafından üretilme kadar yakın. Eğitim odağı, Razor kullanıcı arabiriminden değil, sayfalarla EF Core.

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a>Contosoüniversitesi Razor sayfaları Web uygulaması oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.
* Yeni bir ASP.NET Core Web uygulaması oluşturun. Projeyi **Contosouniversity** olarak adlandırın. Kod kopyalama/yapıştırma olduğunda, ad alanlarının eşleşmesi için *Contosouniversity* projesini adlandırmak önemlidir.
* Açılan listede **ASP.NET Core 2,1** ' i seçin ve ardından **Web uygulaması**' nı seçin.

Yukarıdaki adımların görüntüleri için bkz. [ Razor Web uygulaması oluşturma](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).
Uygulamayı çalıştırın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a>Site stilini ayarlayın

Site menüsünü, düzeni ve giriş sayfasını birkaç değişiklik ayarlar. *Sayfaları/paylaşılan/_Layout. cshtml* 'yi aşağıdaki değişikliklerle güncelleştirin:

* "ContosoUniversity" öğesinin her oluşumunu "Contoso Üniversitesi" olarak değiştirin. Üç oluşum vardır.

* **Öğrenciler**, **Kurslar**, **eğitmenler** ve **Departmanlar** için menü girişleri ekleyin ve **kişi** menü girişini silin.

Değişiklikler vurgulanır. (Tüm *biçimlendirme gösterilmez.* )

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

*Pages/Index. cshtml* dosyasında, ASP.net ve MVC hakkındaki metni bu uygulamayla ilgili metinle değiştirmek için dosyanın içeriğini aşağıdaki kodla değiştirin:

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a>Veri modelini oluşturma

Contoso Üniversitesi uygulaması için varlık sınıfları oluşturma. Aşağıdaki üç varlıkla başlayın:

![Kurs-kayıt-öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

Ve varlıkları arasında bire çok ilişki vardır `Student` `Enrollment` . Ve varlıkları arasında bire çok ilişki vardır `Course` `Enrollment` . Bir öğrenci herhangi bir sayıda kursa kaydolabilir. Bir kurs, kayıtlı sayıda öğrenciye sahip olabilir.

Aşağıdaki bölümlerde, bu varlıkların her biri için bir sınıf oluşturulur.

### <a name="the-student-entity"></a>Öğrenci varlığı

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

*Modeller* klasörü oluşturun. *Modeller* klasöründe, *Student.cs* adlı bir sınıf dosyasını aşağıdaki kodla oluşturun:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

`ID`Özelliği, bu sınıfa karşılık gelen veritabanı (DB) tablosunun birincil anahtar sütunu olur. Varsayılan olarak, EF Core `ID` birincil anahtar olarak adlandırılan bir özelliği Yorumlar `classnameID` . `classnameID`' De, `classname` sınıfının adıdır. Diğer otomatik olarak tanınan birincil anahtar, `StudentID` Önceki örnekte yer verilir.

`Enrollments`Özelliği bir [Gezinti özelliğidir](/ef/core/modeling/relationships). Gezinti özellikleri bu varlıkla ilgili diğer varlıkların bağlantısını sağlar. Bu durumda, `Enrollments` öğesinin özelliği ile `Student entity` `Enrollment` ilgili tüm varlıkları barındırır `Student` . Örneğin, VERITABANıNDAKI bir öğrenci satırında iki ilişkili kayıt satırı varsa, `Enrollments` gezinti özelliği bu iki `Enrollment` varlığı içerir. İlgili `Enrollment` satır, söz konusu öğrencinin birincil anahtar değerini sütununda içeren bir satırdır `StudentID` . Örneğin, ID = 1 olan öğrencinin tabloda iki satır olduğunu varsayalım `Enrollment` . `Enrollment`Tabloda = 1 olan iki satır vardır `StudentID` . `StudentID` , tablodaki `Enrollment` öğrencisi belirten bir yabancı anahtardır `Student` .

Bir gezinti özelliği birden çok varlık tutabileceğinden, gezinti özelliği gibi bir liste türü olmalıdır `ICollection<T>` . `ICollection<T>` belirtilebilir veya veya gibi bir tür olabilir `List<T>` `HashSet<T>` . Kullanıldığında `ICollection<T>` , EF Core `HashSet<T>` Varsayılan olarak bir koleksiyon oluşturur. Birden çok varlığı tutan gezinti özellikleri, çoktan çoğa ve bire çok ilişkilerden gelir.

### <a name="the-enrollment-entity"></a>Kayıt varlığı

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

*Modeller* klasöründe, aşağıdaki kodla *enrollment.cs* oluşturun:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

`EnrollmentID`Özelliği birincil anahtardır. Bu varlık `classnameID` `ID` , varlığı beğenmek yerine, modelini kullanır `Student` . Genellikle geliştiriciler bir model seçer ve bunu veri modeli boyunca kullanır. Daha sonraki bir öğreticide, veri modelinde devralmayı daha kolay hale getirmek için ClassName olmadan ID kullanımı gösterilmemiştir.

`Grade`Özelliği bir `enum` . Tür bildiriminden sonraki soru işareti, `Grade` `Grade` özelliğin null yapılabilir olduğunu gösterir. Null olan bir sınıf sıfır bir sınıf ile farklıdır--null, henüz bir sınıf bilinmediğini veya henüz atanmadığını belirtir.

`StudentID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Student` . Bir varlık `Enrollment` bir `Student` varlıkla ilişkilendirilir, bu nedenle özellik tek bir varlık içerir `Student` . `Student`Varlık, `Student.Enrollments` birden çok varlık içeren gezinti özelliğinden farklıdır `Enrollment` .

`CourseID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Course` . Bir `Enrollment` varlık bir `Course` varlıkla ilişkilendirilir.

EF Core bir özelliği, adlandırılmış ise yabancı anahtar olarak yorumlar `<navigation property name><primary key property name>` . Örneğin, `StudentID` `Student` gezinti özelliği için `Student` varlığın birincil anahtarı olduğundan `ID` . Yabancı anahtar özellikleri de adlandırılmış olabilir `<primary key property name>` . Örneğin, `CourseID` `Course` varlığın birincil anahtarı olduğundan `CourseID` .

### <a name="the-course-entity"></a>Kurs varlığı

![Kurs varlık diyagramı](intro/_static/course-entity.png)

*Modeller* klasöründe, aşağıdaki kodla *Course.cs* oluşturun:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

`Enrollments`Özelliği bir gezinti özelliğidir. Bir `Course` varlık herhangi bir sayıda varlıkla ilişkili olabilir `Enrollment` .

`DatabaseGenerated`Özniteliği, UYGULAMANıN DB oluşturmak yerine birincil anahtarı belirtmesini sağlar.

## <a name="scaffold-the-student-model"></a>Öğrenci modelini dolandırın

Bu bölümde öğrenci modeli scafkatdır. Diğer bir deyişle, scafkatlama aracı öğrenci modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.

* Projeyi derleyin.
* *Sayfalar/öğrenciler* klasörünü oluşturun.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Çözüm Gezgini**, *Sayfalar/öğrenciler* klasörüne sağ tıklayarak  > **yeni yapı iskelesi öğesi** ekleyin >.
* **Yapı iskelesi Ekle** iletişim kutusunda **Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > .

**Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:

* **Model sınıfı** açılır penceresinde **öğrenci (Contosouniversity. modeller)** öğesini seçin.
* **Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin ve üretilen adı **Contosouniversity. modeller. SchoolContext** olarak değiştirin.
* **Veri bağlamı sınıfı** açılır penceresinde **Contosouniversity. modeller. SchoolContext** öğesini seçin.
* **Ekle**’yi seçin.

![CRUD iletişim kutusu](intro/_static/s1.png)

Önceki adımla ilgili bir sorununuz varsa [Film modeli](xref:tutorials/razor-pages/model#scaffold-the-movie-model) ' ne bakın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Öğrenci modelini iskele almak için aşağıdaki komutları çalıştırın.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

Yapı iskelesi işlemi oluşturulur ve aşağıdaki dosyaları değiştirdi:

### <a name="files-created"></a>Oluşturulan dosyalar

* *Sayfalar/öğrenciler* Oluşturma, silme, ayrıntılar, düzenleme, dizin oluşturma.
* *Data/SchoolContext. cs*

### <a name="file-updates"></a>Dosya güncelleştirmeleri

* *Startup.cs* : Bu dosyadaki değişiklikler sonraki bölümde ayrıntılıdır.
* *appsettings.json* : Yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesi eklenir.

## <a name="examine-the-context-registered-with-dependency-injection"></a>Bağımlılık ekleme ile kaydedilen bağlamı inceleyin

ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur. Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir. Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır. Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.

Scafkatlama aracı otomatik olarak bir DB bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.

`ConfigureServices` *Startup.cs* içindeki yöntemi inceleyin. Vurgulanan satır, scaffolder tarafından eklendi:

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir. Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *appsettings.json* .

## <a name="update-main"></a>Ana güncelleştirme

*Program.cs*' de, `Main` aşağıdakileri yapmak için yöntemini değiştirin:

* Bağımlılık ekleme kapsayıcısından bir DB bağlam örneği alın.
* Yeniden  [oluşturulmasını](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)çağırın.
* `EnsureCreated`Yöntem tamamlandığında bağlamı atın.

Aşağıdaki kod güncelleştirilmiş *program.cs* dosyasını gösterir.

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

`EnsureCreated` bağlam veritabanının mevcut olmasını sağlar. Varsa, hiçbir eylem yapılmaz. Yoksa, veritabanı ve tüm şeması oluşturulur. `EnsureCreated` veritabanını oluşturmak için geçişleri kullanmaz. İle oluşturulan bir veritabanı `EnsureCreated` daha sonra geçişler kullanılarak güncelleştirilemez.

`EnsureCreated` , aşağıdaki iş akışına izin veren uygulama başlatma sırasında çağrılır:

* VERITABANıNı silin.
* DB şemasını değiştirin (örneğin, bir `EmailAddress` alan ekleyin).
* Uygulamayı çalıştırın.
* `EnsureCreated` sütunu ile bir VERITABANı oluşturur `EmailAddress` .

`EnsureCreated` şema hızlı bir şekilde gelişmede geliştirmede daha erken kullanışlıdır. Öğreticide daha sonra DB silinir ve geçişler kullanılır.

### <a name="test-the-app"></a>Uygulamayı test etme

Uygulamayı çalıştırın ve ilkeyi kabul edin cookie . Bu uygulama, kişisel bilgileri saklar. İlke hakkında, cookie [AB Genel VERI koruma YÖNETMELIĞI (GDPR) desteğiyle](xref:security/gdpr)ilgili bilgi edinebilirsiniz.

* **Öğrenciler** bağlantısını seçin ve ardından **Yeni oluştur**.
* Düzenle, Ayrıntılar ve Sil bağlantılarını test edin.

## <a name="examine-the-schoolcontext-db-context"></a>SchoolContext DB bağlamını inceleyin

Belirli bir veri modeli için EF Core işlevselliğini koordine eden ana sınıf DB bağlam sınıfıdır. Veri bağlamı [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir. Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir. Bu projede, sınıfı olarak adlandırılır `SchoolContext` .

Aşağıdaki kodla *SchoolContext.cs* güncelleştirin:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

Vurgulanan kod, her bir varlık kümesi için bir [Dbset \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur. EF Core terminoloji:

* Bir varlık kümesi, genellikle bir DB tablosuna karşılık gelir.
* Bir varlık, tablodaki bir satıra karşılık gelir.

`DbSet<Enrollment>` ve `DbSet<Course>` atlanamaz. EF Core `Student` `Enrollment` , varlık varlığa başvurduğundan ve varlık `Enrollment` varlığa başvurduğundan bunları örtülü olarak içerir `Course` . Bu öğreticide, `DbSet<Enrollment>` ve `DbSet<Course>` içinde tutun `SchoolContext` .

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Bağlantı dizesi [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)belirtir. LocalDB, SQL Server Express veritabanı altyapısının hafif bir sürümüdür ve üretim kullanımı için değil uygulama geliştirmeye yöneliktir. LocalDB, istek üzerine başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur. Varsayılan olarak, LocalDB dizinde *. mdf* DB dosyaları oluşturur `C:/Users/<user>` .

## <a name="add-code-to-initialize-the-db-with-test-data"></a>Test verileriyle VERITABANıNı başlatmak için kod ekleme

EF Core boş bir VERITABANı oluşturur. Bu bölümde, `Initialize` Test verileriyle doldurmak için bir yöntem yazılır.

*Veri* klasöründe, *DbInitializer.cs* adlı yeni bir sınıf dosyası oluşturun ve aşağıdaki kodu ekleyin:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

Note: önceki kod, `Models` yerine ad alanı için ( `namespace ContosoUniversity.Models` ) kullanır `Data` . `Models` , scaffolder tarafından oluşturulan kodla tutarlıdır. Daha fazla bilgi için bkz. [GitHub yapı iskelesi sorunu](https://github.com/aspnet/Scaffolding/issues/822).

Kod, VERITABANıNDA herhangi bir öğrenci olup olmadığını denetler. Veritabanında hiç öğrenci yoksa, DB test verileriyle başlatılır. Performansı iyileştirmek için test verilerini koleksiyonlar yerine dizilere yükler `List<T>` .

`EnsureCreated`YÖNTEMI DB bağlamı için otomatik olarak DB oluşturur. VERITABANı varsa, `EnsureCreated` veritabanını değiştirmeden döndürür.

*Program.cs* içinde, `Main` çağrısı yapılacak yöntemi değiştirin `Initialize` :

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Çalışıyorsa uygulamayı durdurun ve **Paket Yöneticisi konsolunda** (PMC) aşağıdaki komutu çalıştırın:

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Çalışıyorsa uygulamayı durdurun ve *cu. db* dosyasını silin.

---

## <a name="view-the-db"></a>VERITABANıNı görüntüleme

Veritabanı adı, daha önce belirttiğiniz bağlam adından ve bir tire ve bir GUID ile oluşturulur. Bu nedenle, veritabanı adı "SchoolContext-{GUID}" olacaktır. GUID her kullanıcı için farklı olacaktır.
Visual Studio 'daki **Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.
SSOX 'te, **(LocalDB) \MSSQLLocalDB > veritabanları > SchoolContext-{GUID}**' a tıklayın.

**Tables** düğümünü genişletin.

Oluşturulan sütunları ve tabloya yerleştirilen satırları görmek için **öğrenci** tablosuna sağ tıklayın ve **verileri görüntüle** ' ye tıklayın.

## <a name="asynchronous-code"></a>Zaman uyumsuz kod

Zaman uyumsuz programlama, ASP.NET Core ve EF Core için varsayılan moddur.

Web sunucusunda sınırlı sayıda iş parçacığı bulunur ve yüksek yük durumlarında tüm kullanılabilir iş parçacıkları kullanımda olabilir. Bu durumda, sunucu, iş parçacıkları boşaltılana kadar yeni istekleri işleyemez. Zaman uyumlu kodla, çok sayıda iş parçacığı, g/ç 'nin tamamlanmasını beklediği için aslında herhangi bir iş yapmadıklarında bağlı olabilir. Zaman uyumsuz kod ile, bir işlem g/ç 'yi tamamlanmayı beklerken, sunucunun diğer istekleri işlemek için kullanması için iş parçacığı serbest bırakılır. Sonuç olarak, zaman uyumsuz kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucu, gecikme olmadan daha fazla trafiği işlemeye etkinleştirilir.

Zaman uyumsuz kod, çalışma zamanında az miktarda yük getirir. Düşük trafik durumlarında, performans artışı göz ardı edilebilir, ancak yüksek trafik durumları için olası performans iyileştirmesi oldukça önemlidir.

Aşağıdaki kodda, [Async](/dotnet/csharp/language-reference/keywords/async) anahtar sözcüğü, `Task<T>` return değeri, `await` anahtar sözcüğü ve `ToListAsync` yöntemi kodu zaman uyumsuz olarak yürütür.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* `async`Anahtar sözcüğü derleyiciye şunu söyler:
  * Yöntem gövdesinin parçaları için geri çağrılar oluşturun.
  * Döndürülen [görev](/dotnet/api/system.threading.tasks.task) nesnesini otomatik olarak oluşturun. Daha fazla bilgi için bkz. [görev dönüş türü](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).

* Örtük dönüş türü, `Task` devam eden işi temsil eder.
* `await`Anahtar sözcüğü, derleyicinin yöntemi iki parçaya böetmesine neden olur. İlk bölüm, zaman uyumsuz olarak başlatılan işlemle biter. İkinci bölüm, işlem tamamlandığında çağrılan bir geri çağırma yöntemine konur.
* `ToListAsync` , Uzantı yönteminin zaman uyumsuz sürümüdür `ToList` .

EF Core kullanan zaman uyumsuz kodu yazarken dikkat edilmesi gereken bazı şeyler:

* Yalnızca sorguları veya komutlarının VERITABANıNA gönderilmesine neden olan deyimler zaman uyumsuz olarak yürütülür. ,,, `ToListAsync` `SingleOrDefaultAsync` Ve içerir `FirstOrDefaultAsync` `SaveChangesAsync` . Yalnızca, gibi bir değiştiren deyimler içermez `IQueryable` `var students = context.Students.Where(s => s.LastName == "Davolio")` .
* EF Core bağlamı iş parçacığı açısından güvenli değildir: paralel olarak birden çok işlem yapmayı denemeyin.
* Zaman uyumsuz kodun performans avantajlarından yararlanmak için, VERITABANıNA sorgu gönderen EF Core yöntemlerini çağırıyorsa kitaplık paketlerinin (örneğin, sayfalama için) zaman uyumsuz olarak kullanılacağını doğrulayın.

.NET 'te zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. Async [and await ile](/dotnet/csharp/programming-guide/concepts/async/)zaman uyumsuz [genel bakış](/dotnet/standard/async) ve zaman uyumsuz programlama

Sonraki öğreticide, temel CRUD (oluşturma, okuma, güncelleştirme, silme) işlemleri incelenir.



## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [Sonraki](xref:data/ef-rp/crud)

::: moniker-end
