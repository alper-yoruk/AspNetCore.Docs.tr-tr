---
title: ASP.NET Core'da Varlık Çerçeve Çekirdekli Jilet Sayfaları - 8'in 1'i Öğretici
author: rick-anderson
description: Entity Framework Core'u kullanarak Jilet Sayfaları uygulamasının nasıl oluşturuluruz olduğunu gösterir
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 07faf5e596e7ea8b134d13caa0259c1e9d74ff1b
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661624"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a>ASP.NET Core'da Varlık Çerçeve Çekirdekli Jilet Sayfaları - 8'in 1'i Öğretici

Yazar: [Tom Dykstra](https://github.com/tdykstra) ve [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Bu, [ASP.NET Core Razor Pages](xref:razor-pages/index) uygulamasında Entity Framework (EF) Core'un nasıl kullanılacağını gösteren bir dizi öğreticinin ilkidir. Öğreticiler kurgusal bir Contoso Üniversitesi için bir web sitesi oluşturmak. Site, öğrenci kabulü, ders oluşturma ve eğitmen atamaları gibi işlevleri içerir. Öğretici kod ilk yaklaşım kullanır. Veritabanı ilk yaklaşımı kullanarak bu öğretici aşağıdaki hakkında bilgi için, [bu Github sorunu](https://github.com/dotnet/AspNetCore.Docs/issues/16897)bakın.

[Tamamlanan uygulamayı indirin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Talimatları indirin.](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Ön koşullar

* Razor Pages'de yeniyseniz, bu sayfaya başlamadan önce Razor Pages öğretici [serisiyle başlayın.](xref:tutorials/razor-pages/razor-pages-start)

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a>Veritabanı motorları

Visual Studio yönergeleri, SQL Server Express'in yalnızca Windows'da çalışan bir sürümü olan [SQL Server LocalDB'ı](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)kullanır.

Visual Studio Code yönergeleri, platformlar arası veritabanı motoru [olan SQLite'ı](https://www.sqlite.org/)kullanır.

SQLite'yi kullanmayı seçerseniz, [SQLite için DB Browser](https://sqlitebrowser.org/)gibi bir SQLite veritabanını yönetmek ve görüntülemek için üçüncü taraf bir araç indirin ve yükleyin.

## <a name="troubleshooting"></a>Sorun giderme

Çözemediğiniz bir sorunla karşılaştıysanız, kodunuzu [tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)karşılaştırın. Yardım almak için iyi bir yol StackOverflow.com için bir soru göndererek, [ASP.NET Core etiketi](https://stackoverflow.com/questions/tagged/asp.net-core) veya EF Core [etiketi](https://stackoverflow.com/questions/tagged/entity-framework-core)ni kullanarak.

## <a name="the-sample-app"></a>Örnek uygulama

Bu eğitimlerde yerleşik olan uygulama temel bir üniversite web sitesidir. Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleyebilir. Burada öğretici oluşturulan ekranlardan birkaçı vardır.

![Öğrenci Endeksi sayfası](intro/_static/students-index30.png)

![Öğrenciler Sayfayı Edit](intro/_static/student-edit30.png)

Bu sitenin UI stili yerleşik proje şablonlarını temel almaktadır. Öğreticinin odak noktası, UI'nin nasıl özelleştirilene değil, EF Core'un nasıl kullanılacağıdır.

Tamamlanan projenin kaynak kodunu almak için sayfanın üst kısmındaki bağlantıyı izleyin. *Cu30* klasörü öğretici ASP.NET Core 3.0 sürümü için kod vardır. 1-7 öğreticiler için kodun durumunu yansıtan dosyalar *cu30snapshots* klasöründe bulunabilir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Tamamlanan projeyi indirdikten sonra uygulamayı çalıştırmak için:

* Adında *SQLite* olan üç dosyayı ve bir klasörü silin.
* Projeyi derleyin.
* Paket Yöneticisi Konsolunda (PMC) aşağıdaki komutu çalıştırın:

  ```powershell
  Update-Database
  ```

* Veritabanıtohum için proje çalıştırın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Tamamlanan projeyi indirdikten sonra uygulamayı çalıştırmak için:

* *ContosoUniversity.csproj*silin ve *ContosoUniversitySQLite.csproj* *ContosoUniversity.csproj*için yeniden adlandırmak .
* *Startup.cs*silin ve *StartupSQLite.cs* *Startup.cs*olarak yeniden adlandırın.
* *AppSettings.json'u*silin ve *appSettingsSQLite.json'ı* *appSettings.json*olarak yeniden adlandırın.
* *Geçişler* klasörünü silin ve *MigrationsSQL'i* *Geçişler*klasörüne yeniden adlandırın.
* Projeyi derleyin.
* Proje klasöründeki bir komut isteminde aşağıdaki komutları çalıştırın:

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* SQLite aracınızda aşağıdaki SQL deyimini çalıştırın:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Veritabanıtohum için proje çalıştırın.

---

## <a name="create-the-web-app-project"></a>Web uygulaması projesini oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio **File** menüsünden **Yeni** > **Proje'yi**seçin.
* **Core Web Uygulaması ASP.NET**seçin.
* Proje *ContosoUniversity*adı . Büyük harf de dahil olmak üzere bu tam adı kullanmak önemlidir, böylece kod kopyalanıp yapıştırıldığında ad boşlukları eşleşir.
* Açılan lar arasında **.NET Core** ve **ASP.NET Core 3.0'ı** seçin ve ardından **Web Uygulaması'nı**seçin.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Bir terminalde, proje klasörü oluşturulması gereken klasöre gidin.

* Razor Pages projesi oluşturmak için aşağıdaki `cd` komutları çalıştırın ve yeni proje klasörüne:

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a>Site stilini ayarlama

*Sayfalar/Paylaşılan/_Layout.cshtml'i*güncelleyerek site üstbilgisini, altbilgisini ve menüsünü ayarlama:

* "ContosoUniversity"in her oluşumunu "Contoso Üniversitesi" olarak değiştirin. Üç olay var.

* **Ev** ve **Gizlilik** menü girişlerini silin ve **Hakkında,** **Öğrenciler,** **Kurslar,** Eğitmenler ve **Bölümler**için **girişler**ekleyin.

Değişiklikler vurgulanır.

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

*Pages/Index.cshtml'de,* ASP.NET Core hakkındaki metni bu uygulamayla ilgili metinle değiştirmek için dosyanın içeriğini aşağıdaki kodla değiştirin:

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

Ana sayfanın görüntülenediğini doğrulamak için uygulamayı çalıştırın.

## <a name="the-data-model"></a>Veri modeli

Aşağıdaki bölümlerde bir veri modeli oluşturulur:

![Ders-Kayıt-Öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

Bir öğrenci herhangi bir sayıda kursa kaydolabilir ve bir kursa herhangi bir sayıda öğrenci kaydolabilir.

## <a name="the-student-entity"></a>Öğrenci varlığı

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

* Proje klasöründe bir *Modeller* klasörü oluşturun. 

* Aşağıdaki kodile *Modeller/Student.cs* oluşturun:

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

Özellik, `ID` veritabanı tablosunun bu sınıfa karşılık gelen birincil anahtar sütunu olur. Varsayılan olarak, EF Core adlı veya `ID` `classnameID` birincil anahtar olarak bir özelliği yorumlar. Yani `Student` sınıf birincil anahtarı `StudentID`için otomatik olarak tanınan ad . Daha fazla bilgi için [BKZ.](/ef/core/modeling/keys?tabs=data-annotations)

Özellik `Enrollments` bir [navigasyon özelliğidir.](/ef/core/modeling/relationships) Gezinti özellikleri, bu varlıkla ilişkili diğer varlıkları tutar. Bu durumda, `Enrollments` bir `Student` varlığın mülkiyeti, `Enrollment` o Öğrenci ile ilgili tüm varlıkları tutar. Örneğin, veritabanındaki bir Öğrenci satırında ilgili iki Kayıt `Enrollments` satırı varsa, gezinti özelliği bu iki Kayıt varlığını içerir. 

Veritabanında, Öğrenci Kimliği sütununda öğrencinin kimlik değeri varsa, Öğrenci satırı öğrenci satırıyla ilişkilidir. Örneğin, bir Öğrenci satırında ID=1 olduğunu varsayalım. İlgili Kayıt satırları StudentID = 1 olacaktır. StudentID, Kayıt tablosundaki yabancı bir *anahtardır.* 

Özellik, `Enrollments` birden `ICollection<Enrollment>` çok ilişkili Kayıt varlığı olabileceği için tanımlanır. Diğer koleksiyon türlerini kullanabilirsiniz, `HashSet<Enrollment>`örneğin. `List<Enrollment>` Kullanıldığında, `ICollection<Enrollment>` EF Core varsayılan `HashSet<Enrollment>` olarak bir koleksiyon oluşturur.

## <a name="the-enrollment-entity"></a>Kayıt varlığı

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

Aşağıdaki kodile *Modeller/Enrollment.cs* oluşturun:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

Özellik `EnrollmentID` birincil anahtardır; bu varlık `classnameID` kendi başına `ID` yerine deseni kullanır. Üretim veri modeli için bir desen seçin ve tutarlı bir şekilde kullanın. Bu öğretici sadece her iki iş göstermek için hem kullanır. `ID` Kullanmadan `classname` kullanmak, bazı veri modeli değişikliklerinin uygulanmasını kolaylaştırır.

Özellik `Grade` bir `enum`. Tür bildiriminden `Grade` sonraki soru işareti `Grade` özelliğin [geçersiz](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/)olduğunu gösterir. Sıfır notu null'dan farklı olan&mdash;bir not, bir notun bilinmediği veya henüz atanmadığı anlamına gelir.

Özellik `StudentID` yabancı bir anahtardır ve ilgili `Student`navigasyon özelliği . Bir `Enrollment` varlık tek `Student` bir varlıkla ilişkilidir, `Student` bu nedenle özellik tek bir varlık içerir.

Özellik `CourseID` yabancı bir anahtardır ve ilgili `Course`navigasyon özelliği . Bir `Enrollment` varlık tek `Course` bir varlıkla ilişkilidir.

EF Core, bir özelliği yabancı `<navigation property name><primary key property name>`bir anahtar olarak yorumluyorsa. Örneğin,`StudentID` `Student` `Student` varlığın birincil anahtarı `ID`. Yabancı anahtar özellikleri de `<primary key property name>`adlandırılabilir. Örneğin, `CourseID` varlığın `Course` birincil anahtarı `CourseID`.

## <a name="the-course-entity"></a>Kurs varlığı

![Ders varlık diyagramı](intro/_static/course-entity.png)

Aşağıdaki kodile *Modeller/Course.cs* oluşturun:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

Tesis `Enrollments` bir navigasyon özelliğidir. Bir `Course` varlık herhangi bir sayıda `Enrollment` varlıkla ilişkili olabilir.

Öznitelik, `DatabaseGenerated` uygulamanın veritabanının oluşturması yerine birincil anahtarı belirtmesine olanak tanır.

Derleyici hatası olmadığını doğrulamak için projeyi oluşturun.

## <a name="scaffold-student-pages"></a>İskele Öğrenci sayfaları

Bu bölümde, oluşturmak için ASP.NET Core iskele aracını kullanırsınız:

* Bir EF Core *bağlam* sınıfı. Bağlam, belirli bir veri modeli için Varlık Çerçevesi işlevselliğini koordine eden ana sınıftır. Sınıftan `Microsoft.EntityFrameworkCore.DbContext` türetilmiştir.
* `Student` Varlık için Oluştur, Oku, Güncelleştir ve Sil (CRUD) işlemlerini işleyen jilet sayfaları.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* *Sayfalar* klasöründe bir *Öğrenci* klasörü oluşturun.
* **Çözüm Gezgini'nde,** *Sayfalar/Öğrenciler* klasörüne sağ tıklayın ve **Yeni İskele Öğesi** **Ekle'yi** > seçin.
* İskele **Ekle** iletişim kutusunda, **Entity Framework (CRUD)** > **ADD**kullanarak Jilet Sayfaları'nı seçin.
* Varlık **Çerçevesi (CRUD)** iletişim kutusunu kullanarak Jilet Sayfaları Ekle iletişim kutusunda:
  * Model **sınıfı** açılır açılır, **Öğrenci (ContosoUniversity.Models)** seçin.
  * Veri **bağlamı sınıf** satırında **+** (artı) işaretini seçin.
  * *ContosoUniversity.Models.ContosoUniversityContext'den* *ContosoUniversity.Data.SchoolContext'a*veri bağlamı adını değiştirin.
  * **Add (Ekle)** seçeneğini belirleyin.

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

  Microsoft.VisualStudio.Web.CodeGeneration.Design paketi iskele için gereklidir. Uygulama SQL Server kullanmasa da, iskele aracının SQL Server paketine ihtiyacı vardır.

* *Sayfalar/Öğrenciler* klasörü oluşturun.

* [Aspnet-codegenerator iskele aracını](xref:fundamentals/tools/dotnet-aspnet-codegenerator)yüklemek için aşağıdaki komutu çalıştırın.

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* Öğrenci sayfalarını iskeleye aşağıdaki komutu çalıştırın.

  **Windows üzerinde**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  **macOS veya Linux'ta**

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

Önceki adımla ilgili bir sorununuz varsa, projeyi oluşturun ve iskele adımını yeniden deneyin.

İskele işlemi:

* *Sayfalar/Öğrenciler* klasöründe Jilet sayfaları oluşturur:
  * *Create.cshtml* ve *Create.cshtml.cs*
  * *Delete.cshtml* ve *Delete.cshtml.cs*
  * *Ayrıntılar.cshtml* ve *Details.cshtml.cs*
  * *Edit.cshtml* ve *Edit.cshtml.cs*
  * *Index.cshtml* ve *Index.cshtml.cs*
* *Veri/SchoolContext.cs*oluşturur.
* *Startup.cs*bağımlılık enjeksiyonuna bağlam ekler.
* *appsettings.json'a*veritabanı bağlantı dizesi ekler.

## <a name="database-connection-string"></a>Veritabanı bağlantı dizesi

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Bağlantı dizesi [SQL Server LocalDB'yi](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)belirtir. 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

LocalDB, SQL Server Express Veritabanı Altyapısının hafif bir sürümüdür ve üretim kullanımı için değil, uygulama geliştirme için tasarlanmıştır. Varsayılan olarak, LocalDB `C:/Users/<user>` dizinde *.mdf* dosyaları oluşturur.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Bağlantı dizesini *CU.db*adlı bir SQLite veritabanı dosyasına işaret edecek şekilde değiştirin:

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a>Veritabanı bağlam ı sınıfını güncelleştirme

Belirli bir veri modeli için EF Core işlevini koordine eden ana sınıf veritabanı bağlam sınıfıdır. İçerik [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)türetilmiştir. Bağlam, veri modeline hangi varlıkların dahil edildiğini belirtir. Bu projede sınıfadlandırılmış. `SchoolContext`

Aşağıdaki kodla *SchoolContext.cs* güncelleştirin:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

Vurgulanan kod, her varlık kümesi için bir [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur. EF Çekirdek terminolojisinde:

* Bir varlık kümesi genellikle bir veritabanı tablosuna karşılık gelir.
* Bir varlık tablodaki bir satıra karşılık gelir.

Bir varlık kümesi birden çok varlık içerdiğinden, DBSet özellikleri çoğul adlar olmalıdır. İskele aracı bir`Student` DBSet oluşturduğundan, bu adım `Students`onu çoğul olarak değiştirir. 

Razor Pages kodunun yeni DBSet adı ile eşleşmesini `_context.Student` sağlamak için, `_context.Students`tüm projede '' için küresel bir değişiklik yapın.  8 olay var.

Derleyici hatası olmadığını doğrulamak için projeyi oluşturun.

## <a name="startupcs"></a>Startup.cs

ASP.NET Core [bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection)ile inşa edilmiştir. Hizmetler (EF Core veritabanı bağlamı gibi) uygulama başlatma sırasında bağımlılık enjeksiyonu ile kaydedilir. Bu hizmetleri gerektiren bileşenlere (Jilet Sayfaları gibi) bu hizmetler yapıcı parametreler aracılığıyla sağlanır. Veritabanı bağlamı örneğini alan oluşturucu kodu daha sonra öğreticide gösterilir.

İskele aracı, bağlam sınıfını bağımlılık enjeksiyon konteyneri ile otomatik olarak kaydetti.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* , `ConfigureServices`vurgulanan satırlar iskele tarafından eklendi:

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* In `ConfigureServices`, iskele aramaları `UseSqlite`tarafından eklenen kodu emin olun.

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

Bağlantı dizesinin adı, [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesindeki bir yöntem çağırılarak içeriğe aktarılır. Yerel geliştirme [için, ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) *appsettings.json* dosyasından bağlantı dizesini okur.

## <a name="create-the-database"></a>Veritabanını oluşturma

Veritabanı yoksa oluşturmak için *Program.cs* güncelleştirin:

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

İçerik için bir veritabanı [varsa, Oluşturulanları Sağlama](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) yöntemi hiçbir eylemde bulunmaz. Veritabanı yoksa, veritabanı ve şema oluşturur. `EnsureCreated`veri modeli değişikliklerini işlemek için aşağıdaki iş akışını sağlar:

* Veritabanını silin. Varolan tüm veriler kaybolur.
* Veri modelini değiştirin. Örneğin, bir `EmailAddress` alan ekleyin.
* Uygulamayı çalıştırın.
* `EnsureCreated`yeni şema ile bir veritabanı oluşturur.

Bu iş akışı, verileri korumanız gerekmediğiniz sürece şema hızla geliştiğinde geliştirmede çok erken çalışır. Veritabanına girilen verilerin korunması gerektiğinde durum farklıdır. Bu durumda, geçişleri kullanın.

Öğretici serinin ilerleyen saatlerinde, oluşturulan `EnsureCreated` veritabanını siler ve bunun yerine geçişleri kullanırsınız. Geçişler kullanılarak `EnsureCreated` oluşturulan bir veritabanı güncelleştirilemez.

### <a name="test-the-app"></a>Uygulamayı test edin

* Uygulamayı çalıştırın.
* **Öğrenciler** bağlantısını seçin ve ardından Yeni Oluştur' seçeneğini **belirleyin.**
* Düzenle, Ayrıntılar ve Sil bağlantılarını test edin.

## <a name="seed-the-database"></a>Veritabanını tohumla

Yöntem `EnsureCreated` boş bir veritabanı oluşturur. Bu bölümde, veritabanını test verileriyle dolduran kod ekler.

Aşağıdaki kodile *Veri/DbInitializer.cs* oluşturun:

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  Kod, veritabanında öğrenci olup olmadığını denetler. Öğrenci yoksa, veritabanına test verileri ekler. Performansı en iyi duruma getirmek için `List<T>` koleksiyonlar yerine diziler halinde test verilerini oluşturur.

* *Program.cs,* aramayı `EnsureCreated` bir `DbInitializer.Initialize` çağrıyla değiştirin:

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Çalışıyorsa uygulamayı durdurun ve **Paket Yöneticisi Konsolu'nda** (PMC) aşağıdaki komutu çalıştırın:

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Çalışıyorsa uygulamayı durdurun ve *CU.db* dosyasını silin.

---

* Uygulamayı yeniden başlatın.

* Tohumlu verileri görmek için Öğrenciler sayfasını seçin.

## <a name="view-the-database"></a>Veritabanını görüntüleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual **Studio'daki** **Görünüm** menüsünden SQL Server Object Explorer'ı (SSOX) açın.
* SSOX'ta **SchoolContext-{GUID} > (localdb)\MSSQLLocalDB > Databases'i**seçin. Veritabanı adı, daha önce sağladığınız bağlam adından artı bir tire ve guid'den oluşturulur.
* **Tablolar** düğümlerini genişletin.
* Oluşturulan sütunları ve tabloya eklenen satırları görmek için **Öğrenci** tablosuna sağ tıklayın ve **Verileri Görüntüle'yi** tıklatın.
* **Modelin** tablo şemasını nasıl eşleştebildiğini `Student` görmek `Student` için Öğrenci tablosuna sağ tıklayın ve **Kodu Görüntüle'yi** tıklatın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Veritabanı şemasını ve tohumlu verileri görüntülemek için SQLite aracınızı kullanın. Veritabanı dosyası *CU.db* olarak adlandırılır ve proje klasöründe bulunur.

---

## <a name="asynchronous-code"></a>Asynchronous kodu

Asynchronous programlama ASP.NET Core ve EF Core için varsayılan moddur.

Bir web sunucusunda sınırlı sayıda iş parçacığı vardır ve yüksek yük durumlarında kullanılabilir tüm iş parçacıkları kullanılıyor olabilir. Bu durumda, iş parçacıkları serbest bırakılıncaya kadar sunucu yeni istekleri işleyebilir. Senkron kodla, G/Ç'nin tamamlanmasını bekledikleri için birçok iş parçacığı aslında herhangi bir iş yapmasalar da bağlanabilir. Bir işlem G/Ç'nin tamamlanmasını beklerken, iş parçacığı sunucunun diğer istekleri işlemek için kullanması için serbest bırakılır. Sonuç olarak, eşzamanlı kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucu gecikmeden daha fazla trafiği işleyebilir.

Asynchronous kodu çalışma zamanında az miktarda ek yükü tanıtmak yok. Düşük trafik durumları için, performans isabet ihmal edilebilir, yüksek trafik durumlarda ise, potansiyel performans iyileştirme önemli.

Aşağıdaki kodda, [async](/dotnet/csharp/language-reference/keywords/async) anahtar `Task<T>` kelime, `await` iade değeri, `ToListAsync` anahtar kelime ve yöntem kodu eşzamanlı olarak yürütmek olun.

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* Anahtar `async` kelime derleyiciye şunları söyler:
  * Yöntem gövdesinin parçaları için geri arama lar oluşturun.
  * Döndürülen [Görev](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) nesnesini oluşturun.
* İade `Task<T>` türü devam eden çalışmayı temsil eder.
* Anahtar `await` kelime derleyicinin yöntemi iki bölüme bölmesine neden olur. İlk bölüm, eş zamanlı olarak başlatılan işlemle sona erer. İkinci bölüm, işlem tamamlandığında çağrılan bir geri arama yöntemine konur.
* `ToListAsync`uzantı yönteminin `ToList` eşzamanlı sürümüdür.

EF Core kullanan eşzamanlı kod yazarken dikkat edilmesi gereken bazı noktalar:

* Yalnızca sorguların veya komutların veritabanına gönderilmesine neden olan ifadeler eşsenkronize olarak yürütülür. Buna `ToListAsync`, `SingleOrDefaultAsync` `FirstOrDefaultAsync`, `SaveChangesAsync`, ve . Bu sadece bir `IQueryable`değiştirmek ifadeler içermez , `var students = context.Students.Where(s => s.LastName == "Davolio")`gibi .
* EF Core bağlamı iş parçacığı güvenli değildir: paralel olarak birden çok işlem yapmaya çalışmayın.
* Async kodunun performans avantajlarından yararlanmak için, kitaplık paketlerinin (sayfalama gibi) veritabanına sorgu gönderen EF Core yöntemlerini aramaları durumunda async kullandığını doğrulayın.

.NET'te asynchronous programlama hakkında daha fazla bilgi için [Async Genel Bakış](/dotnet/standard/async) ve [Asynchronous programlamaya](/dotnet/csharp/programming-guide/concepts/async/)bakın ve sizi bekliyor.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="step-by-step"]
> [Sonraki öğretici](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Contoso Üniversitesi örnek web uygulaması, Entity Framework (EF) Core kullanarak ASP.NET Core Razor Pages uygulamasının nasıl oluşturulabildiğini gösterir.

Örnek uygulama kurgusal bir Contoso Üniversitesi için bir web sitesidir. Öğrenci kabulü, ders oluşturma ve eğitmen atamaları gibi işlevleri içerir. Bu sayfa, Contoso Üniversitesi örnek uygulamasının nasıl inşa edilebildiğini açıklayan bir dizi öğreticinin ilkidir.

[Tamamlanan uygulamayı indirin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [Talimatları indirin.](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>Ön koşullar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

[Jilet Sayfaları](xref:razor-pages/index)ile Aşinalık . Yeni programcılar bu seriye başlamadan önce [Razor Pages ile başlayın](xref:tutorials/razor-pages/razor-pages-start) tamamlamalıdır.

## <a name="troubleshooting"></a>Sorun giderme

Çözemediğiniz bir sorunla karşınıza çıkarsa, kodunuzu [tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)karşılaştırarak genellikle çözümü bulabilirsiniz. Yardım almak için iyi bir yol ASP.NET [Core](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core)için [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) için bir soru göndererek.

## <a name="the-contoso-university-web-app"></a>Contoso Üniversitesi web uygulaması

Bu eğitimlerde yerleşik olan uygulama temel bir üniversite web sitesidir.

Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleyebilir. Burada öğretici oluşturulan ekranlardan birkaçı vardır.

![Öğrenci Endeksi sayfası](intro/_static/students-index.png)

![Öğrenciler Sayfayı Edit](intro/_static/student-edit.png)

Bu sitenin UI stili, yerleşik şablonlar tarafından oluşturulana yakındır. Öğretici odak Razor Pages ile EF Core değil, UI.

## <a name="create-the-contosouniversity-razor-pages-web-app"></a>ContosoUniversity Razor Pages web uygulamasını oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Visual Studio **File** menüsünden **Yeni** > **Proje'yi**seçin.
* Yeni bir ASP.NET Core Web Uygulaması oluşturun. Proje **ContosoUniversity**adı . Kod kopyalandığında/yapıştırıldığında ad boşluklarının eşleşmesi için *projecontosoUniversity* adını vermek önemlidir.
* Açılır ASP.NET **Core 2.1'i** seçin ve ardından **Web Uygulaması'nı**seçin.

Önceki adımların görüntüleri için [bkz.](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app)
Uygulamayı çalıştırın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a>Site stilini ayarlama

Site menüsünü, düzeni ve ana sayfayı birkaç değişiklik ayarla. *Sayfaları/Paylaşılan/_Layout.cshtml'i* aşağıdaki değişikliklerle güncelleştirin:

* "ContosoUniversity"in her oluşumunu "Contoso Üniversitesi" olarak değiştirin. Üç olay var.

* **Öğrenciler,** **Kurslar,** Eğitmenler ve **Bölümler**için menü **girişlerini**ekleyin ve **İletişim** menüsü girişini silin.

Değişiklikler vurgulanır. (Tüm biçimlendirme *görüntülenmez.)*

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

*Pages/Index.cshtml'de,* ASP.NET ve MVC hakkındaki metni bu uygulamayla ilgili metinle değiştirmek için dosyanın içeriğini aşağıdaki kodla değiştirin:

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a>Veri modelini oluşturma

Contoso Üniversitesi uygulaması için varlık sınıfları oluşturun. Aşağıdaki üç varlıkla başlayın:

![Ders-Kayıt-Öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

Varlıklarla arasında `Student` `Enrollment` bir-çok ilişki vardır. Varlıklarla arasında `Course` `Enrollment` bir-çok ilişki vardır. Bir öğrenci herhangi bir sayıda kursa kaydolabilir. Bir kursa herhangi bir sayıda öğrenci kaydolabilir.

Aşağıdaki bölümlerde, bu varlıkların her biri için bir sınıf oluşturulur.

### <a name="the-student-entity"></a>Öğrenci varlığı

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

*Modeller* klasörü oluşturun. *Modeller* klasöründe, aşağıdaki kodla *Student.cs* adlı bir sınıf dosyası oluşturun:

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

Özellik, `ID` veritabanı (DB) tablosunun bu sınıfa karşılık gelen birincil anahtar sütunu olur. Varsayılan olarak, EF Core adlı veya `ID` `classnameID` birincil anahtar olarak bir özelliği yorumlar. In `classnameID` `classname` , sınıfın adıdır. Otomatik olarak tanınan birincil `StudentID` anahtar seçeneği yukarıdaki örnektedir.

Özellik `Enrollments` bir [navigasyon özelliğidir.](/ef/core/modeling/relationships) Gezinti özellikleri, bu varlıkla ilişkili diğer varlıklara bağlanır. Bu durumda, `Enrollments` bir `Student entity` özelliği ile ilgili `Enrollment` tüm varlıkların `Student`tutar. Örneğin, DB'deki bir Öğrenci satırında ilgili iki `Enrollments` Kayıt satırı varsa, gezinti özelliği bu iki `Enrollment` varlığı içerir. İlişkili `Enrollment` satır, `StudentID` sütundaki öğrencinin birincil anahtar değerini içeren bir satırdır. Örneğin, ID=1'e sahip öğrencinin `Enrollment` tabloda iki satırı olduğunu varsayalım. Tabloda `Enrollment` = 1 `StudentID` ile iki satır vardır. `StudentID``Student` tablodaki öğrenciyi `Enrollment` belirten yabancı bir anahtardır.

Bir gezinti özelliği birden çok varlık tutabiliyorsa, gezinti `ICollection<T>`özelliği ' nin liste türü olması gerekir. `ICollection<T>`belirtilebilir, ya da gibi `List<T>` `HashSet<T>`bir tür ya da . Kullanıldığında, `ICollection<T>` EF Core varsayılan `HashSet<T>` olarak bir koleksiyon oluşturur. Birden çok varlığı barındıran gezinti özellikleri, çok-çok ve bir-çok ilişkisinden gelir.

### <a name="the-enrollment-entity"></a>Kayıt varlığı

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

*Modeller* klasöründe, aşağıdaki kodla *Enrollment.cs* oluşturun:

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

Özellik `EnrollmentID` birincil anahtardır. Bu `Student` varlık, `classnameID` varlık `ID` gibi değil deseni kullanır. Genellikle geliştiriciler bir desen seçin ve veri modeli boyunca kullanabilirsiniz. Daha sonraki bir öğreticide, sınıf adı olmadan kimlik kullanılması, veri modelinde devralmayı daha kolay uygulamak için gösterilir.

Özellik `Grade` bir `enum`. Tür bildiriminden `Grade` sonraki soru işareti `Grade` özelliğin geçersiz olduğunu gösterir. Sıfır notundan farklı olan bir not - null, bir notun bilinmediği veya henüz atanmadığı anlamına gelir.

Özellik `StudentID` yabancı bir anahtardır ve ilgili `Student`navigasyon özelliği . Bir `Enrollment` varlık tek `Student` bir varlıkla ilişkilidir, `Student` bu nedenle özellik tek bir varlık içerir. Varlık, `Student` birden çok `Student.Enrollments` `Enrollment` varlık içeren gezinti özelliğinden farklıdır.

Özellik `CourseID` yabancı bir anahtardır ve ilgili `Course`navigasyon özelliği . Bir `Enrollment` varlık tek `Course` bir varlıkla ilişkilidir.

EF Core, bir özelliği yabancı `<navigation property name><primary key property name>`bir anahtar olarak yorumluyorsa. Örneğin,`StudentID` `Student` `Student` varlığın birincil anahtarı . `ID` Yabancı anahtar özellikleri de `<primary key property name>`adlandırılabilir. Örneğin, `CourseID` varlığın `Course` birincil anahtarı `CourseID`.

### <a name="the-course-entity"></a>Kurs varlığı

![Ders varlık diyagramı](intro/_static/course-entity.png)

*Modeller* klasöründe aşağıdaki kodla *Course.cs* oluşturun:

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

Tesis `Enrollments` bir navigasyon özelliğidir. Bir `Course` varlık herhangi bir sayıda `Enrollment` varlıkla ilişkili olabilir.

Öznitelik, `DatabaseGenerated` uygulamanın DB'nin oluşturması yerine birincil anahtarı belirtmesine olanak tanır.

## <a name="scaffold-the-student-model"></a>İskele öğrenci modeli

Bu bölümde, öğrenci modeli iskeleli. Diğer bir zamanda, iskele aracı öğrenci modeli için Oluştur, Oku, Güncelleştir ve Sil (CRUD) işlemleri için sayfalar oluşturur.

* Projeyi derleyin.
* *Sayfalar/Öğrenciler* klasörünü oluşturun.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Solution Explorer'da,** Yeni **İskele**Öğesi **Ekle** > > *Sayfalar/Öğrenciler* klasörüne sağ tıklayın.
* İskele **Ekle** iletişim kutusunda, **Entity Framework (CRUD)** > **ADD**kullanarak Jilet Sayfaları'nı seçin.

Varlık **Çerçevesi (CRUD)** iletişim kutusunu kullanarak Jilet Sayfaları Ekle'yi tamamlayın:

* Model **sınıfı** açılır açılır, **Öğrenci (ContosoUniversity.Models)** seçin.
* Veri **bağlamında sınıf** satırında(artı) işaretini **+** seçin ve oluşturulan adı **ContosoUniversity.Models.SchoolContext olarak değiştirin.**
* Veri **bağlamında sınıf** açılır, **ContosoUniversity.Models.SchoolContext'ı** seçin
* **Add (Ekle)** seçeneğini belirleyin.

![CRUD iletişim kutusu](intro/_static/s1.png)

Bir önceki adımla ilgili bir sorununvarsa [film modelini İskele'ye](xref:tutorials/razor-pages/model#scaffold-the-movie-model) bakın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Öğrenci modelini iskeleye getirmek için aşağıdaki komutları çalıştırın.

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

İskele işlemi aşağıdaki dosyaları oluşturdu ve değiştirdi:

### <a name="files-created"></a>Oluşturulan dosyalar

* *Sayfalar / Öğrenciler* Oluştur, Sil, Ayrıntılar, Düzenle, Dizin.
* *Veri/SchoolContext.cs*

### <a name="file-updates"></a>Dosya güncellemeleri

* *Startup.cs* : Bu dosyadaki değişiklikler sonraki bölümde ayrıntılı olarak açıklanmaktadır.
* *appsettings.json* : Yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesi eklenir.

## <a name="examine-the-context-registered-with-dependency-injection"></a>Bağımlılık enjeksiyonu ile kayıtlı bağlamı inceleyin

ASP.NET Core [bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection)ile inşa edilmiştir. Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık enjeksiyonu ile kaydedilir. Bu hizmetleri gerektiren bileşenlere (Jilet Sayfaları gibi) bu hizmetler yapıcı parametreler aracılığıyla sağlanır. DB bağlam örneğini alan yapıcı kod daha sonra öğreticide gösterilir.

İskele aracı otomatik olarak bir DB Bağlamı oluşturdu ve bağımlılık enjeksiyon konteyneri ile kaydetti.

yöntemi Startup.cs inceleyin. *Startup.cs* `ConfigureServices` Vurgulanan satır iskele tarafından eklendi:

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

Bağlantı dizesinin adı, [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesindeki bir yöntem çağırılarak içeriğe aktarılır. Yerel geliştirme [için, ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) *appsettings.json* dosyasından bağlantı dizesini okur.

## <a name="update-main"></a>Ana güncelleştirme

*Program.cs,* `Main` aşağıdakileri yapmak için yöntemi değiştirin:

* Bağımlılık enjeksiyon kapsayıcısından bir DB bağlam örneği alın.
* [EnsureCreated'ı](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)arayın.
* Yöntem tamamlandığında bağlamı `EnsureCreated` atın.

Aşağıdaki kod güncelleştirilmiş *Program.cs* dosyasını gösterir.

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

`EnsureCreated`bağlam için veritabanının var olmasını sağlar. Varsa, hiçbir işlem yapılmaz. Yoksa, veritabanı ve tüm şema oluşturulur. `EnsureCreated`veritabanını oluşturmak için geçişler kullanmaz. Daha `EnsureCreated` sonra geçişler kullanılarak oluşturulan bir veritabanı güncelleştirilemez.

`EnsureCreated`aşağıdaki iş akışını sağlayan uygulama başlangıcında çağrılır:

* DB'yi silin.
* DB şemasını değiştirin (örneğin, `EmailAddress` bir alan ekleyin).
* Uygulamayı çalıştırın.
* `EnsureCreated``EmailAddress` sütunile bir DB oluşturur.

`EnsureCreated`şema hızla geliştiğinde gelişmenin erken dönemlerinde uygundur. Daha sonra öğreticide DB silinir ve geçişler kullanılır.

### <a name="test-the-app"></a>Uygulamayı test edin

Uygulamayı çalıştırın ve çerez ilkesini kabul edin. Bu uygulama kişisel bilgileri saklamaz. Ab Genel Veri Koruma [Yönetmeliği (GDPR) desteğinde](xref:security/gdpr)çerez politikası hakkında bilgi edinebilirsiniz.

* **Öğrenciler** bağlantısını seçin ve ardından Yeni Oluştur' seçeneğini **belirleyin.**
* Düzenle, Ayrıntılar ve Sil bağlantılarını test edin.

## <a name="examine-the-schoolcontext-db-context"></a>SchoolContext DB bağlamını inceleyin

Belirli bir veri modeli için EF Core işlevini koordine eden ana sınıf DB bağlam sınıfıdır. Veri bağlamı [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)türetilmiştir. Veri bağlamı, veri modeline hangi varlıkların dahil edildiğini belirtir. Bu projede sınıfadlandırılmış. `SchoolContext`

Aşağıdaki kodla *SchoolContext.cs* güncelleştirin:

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

Vurgulanan kod, her varlık kümesi için bir [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur. EF Çekirdek terminolojisinde:

* Bir varlık kümesi genellikle bir DB tablosuna karşılık gelir.
* Bir varlık tablodaki bir satıra karşılık gelir.

`DbSet<Enrollment>`ve `DbSet<Course>` atlanabilir. `Student` VARLıK `Enrollment` varlığa, `Enrollment` varlık da `Course` varlığa atıfta bulunduğundan, EF Core bunları dolaylı olarak içerir. Bu öğretici için, `DbSet<Course>` tutmak `SchoolContext` `DbSet<Enrollment>` ve .

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

Bağlantı dizesi [SQL Server LocalDB'yi](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)belirtir. LocalDB, SQL Server Express Veritabanı Altyapısının hafif bir sürümüdür ve üretim kullanımı için değil, uygulama geliştirme için tasarlanmıştır. LocalDB isteğe bağlı olarak başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur. Varsayılan olarak, LocalDB `C:/Users/<user>` dizinde *.mdf* DB dosyaları oluşturur.

## <a name="add-code-to-initialize-the-db-with-test-data"></a>Test verileriyle DB'yi başlatmaya kod ekleme

EF Core boş bir DB oluşturur. Bu bölümde, `Initialize` test verileri ile doldurmak için bir yöntem yazılır.

*Veri* *klasöründe, DbInitializer.cs* adında yeni bir sınıf dosyası oluşturun ve aşağıdaki kodu ekleyin:

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

Not: Önceki kod, `Models` `Data`ad alanı`namespace ContosoUniversity.Models`( ) yerine . `Models`iskele tarafından oluşturulan kodla tutarlıdır. Daha fazla bilgi için [bu GitHub iskele sorununa](https://github.com/aspnet/Scaffolding/issues/822)bakın.

Kod, DB'de öğrenci olup olmadığını kontrol eder. DB'de öğrenci yoksa, DB test verileriyle başolarak başolarak başlatEdilir. Performansı en iyi duruma getirmek `List<T>` için test verilerini koleksiyonlar yerine dizilere yükler.

Yöntem `EnsureCreated` otomatik olarak DB bağlamı için DB oluşturur. DB varsa, `EnsureCreated` DB'yi değiştirmeden döndürür.

Program.cs *Program.cs*yılında, `Main` aramak `Initialize`için yöntemi değiştirmek:

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Çalışıyorsa uygulamayı durdurun ve **Paket Yöneticisi Konsolu'nda** (PMC) aşağıdaki komutu çalıştırın:

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Çalışıyorsa uygulamayı durdurun ve *CU.db* dosyasını silin.

---

## <a name="view-the-db"></a>DB'yi görüntüleyin

Veritabanı adı, daha önce sağladığınız bağlam adından artı bir tire ve guid'den oluşturulur. Böylece, veritabanı adı "SchoolContext-{GUID}" olacaktır. GUID her kullanıcı için farklı olacaktır.
Visual **Studio'daki** **Görünüm** menüsünden SQL Server Object Explorer'ı (SSOX) açın.
SSOX'ta **(localdb)\MSSQLLocalDB > Veritabanları > SchoolContext-{GUID}** seçeneğini tıklayın.

**Tablolar** düğümlerini genişletin.

Oluşturulan sütunları ve tabloya eklenen satırları görmek için **Öğrenci** tablosuna sağ tıklayın ve **Verileri Görüntüle'yi** tıklatın.

## <a name="asynchronous-code"></a>Asynchronous kodu

Asynchronous programlama ASP.NET Core ve EF Core için varsayılan moddur.

Bir web sunucusunda sınırlı sayıda iş parçacığı vardır ve yüksek yük durumlarında kullanılabilir tüm iş parçacıkları kullanılıyor olabilir. Bu durumda, iş parçacıkları serbest bırakılıncaya kadar sunucu yeni istekleri işleyebilir. Senkron kodla, G/Ç'nin tamamlanmasını bekledikleri için birçok iş parçacığı aslında herhangi bir iş yapmasalar da bağlanabilir. Bir işlem G/Ç'nin tamamlanmasını beklerken, iş parçacığı sunucunun diğer istekleri işlemek için kullanması için serbest bırakılır. Sonuç olarak, eşzamanlı kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucunun gecikmeden daha fazla trafiği işlemesi sağlanır.

Asynchronous kodu çalışma zamanında az miktarda ek yükü tanıtmak yok. Düşük trafik durumları için, performans isabet ihmal edilebilir, yüksek trafik durumlarda ise, potansiyel performans iyileştirme önemli.

Aşağıdaki kodda, [async](/dotnet/csharp/language-reference/keywords/async) anahtar `Task<T>` kelime, `await` iade değeri, `ToListAsync` anahtar kelime ve yöntem kodu eşzamanlı olarak yürütmek olun.

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* Anahtar `async` kelime derleyiciye şunları söyler:
  * Yöntem gövdesinin parçaları için geri arama lar oluşturun.
  * Döndürülen [Görev](/dotnet/api/system.threading.tasks.task) nesnesini otomatik olarak oluşturun. Daha fazla bilgi için [Görev İade Türü'ne](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)bakın.

* Örtük iade `Task` türü devam eden çalışmayı temsil eder.
* Anahtar `await` kelime derleyicinin yöntemi iki bölüme bölmesine neden olur. İlk bölüm, eş zamanlı olarak başlatılan işlemle sona erer. İkinci bölüm, işlem tamamlandığında çağrılan bir geri arama yöntemine konur.
* `ToListAsync`uzantı yönteminin `ToList` eşzamanlı sürümüdür.

EF Core kullanan eşzamanlı kod yazarken dikkat edilmesi gereken bazı noktalar:

* Yalnızca Sorguların veya komutların DB'ye gönderilmesine neden olan ifadeler eşsenkronize olarak yürütülür. Buna `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, `SaveChangesAsync`ve . Bu sadece bir `IQueryable`değiştirmek ifadeler içermez , `var students = context.Students.Where(s => s.LastName == "Davolio")`gibi .
* EF Core bağlamı iş parçacığı güvenli değildir: paralel olarak birden çok işlem yapmaya çalışmayın.
* Async kodunun performans avantajlarından yararlanmak için, kitaplık paketlerinin (sayfalama gibi) DB'ye sorgu gönderen EF Core yöntemlerini aramaları durumunda async kullandığını doğrulayın.

.NET'te asynchronous programlama hakkında daha fazla bilgi için [Async Genel Bakış](/dotnet/standard/async) ve [Asynchronous programlamaya](/dotnet/csharp/programming-guide/concepts/async/)bakın ve sizi bekliyor.

Bir sonraki öğreticide, temel CRUD (oluşturma, okuma, güncelleme, silme) işlemleri incelenir.



## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [Sonraki](xref:data/ef-rp/crud)

::: moniker-end
