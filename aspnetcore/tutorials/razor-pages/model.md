---
title: Bölüm 2, model ekleme
author: rick-anderson
description: Sayfalardaki eğitim serisinin 2. bölümü Razor . Bu bölümde, model sınıfları eklenir.
ms.author: riande
ms.date: 11/11/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
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
uid: tutorials/razor-pages/model
ms.openlocfilehash: 7ea28e0ecad410335c37c603c8ec1eb5e6e41d33
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97485998"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a>Bölüm 2, Razor ASP.NET Core bir sayfalar uygulamasına bir model ekleyin

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

Bu bölümde, bir veritabanında film yönetimi için sınıflar eklenir. Uygulamanın model sınıfları veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) kullanır. EF Core, veri erişimini kolaylaştıran bir nesne ilişkisel Eşleyici (O/RM). Önce model sınıflarını yazdığınızda EF Core veritabanını oluşturur.

Model sınıfları, EF Core bağımlılığı olmadığından POCO sınıfları ("**P** Lain-**O** ld **C** LR **o** bjects") olarak bilinir. Veritabanında depolanan verilerin özelliklerini tanımlar.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

## <a name="add-a-data-model"></a>Veri modeli ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **Çözüm Gezgini**, yeni klasör ekle > *Razor pagesmovie* projesine sağ tıklayın   >  . Klasör *modellerini* adlandırın.
1. *Modeller* klasörüne sağ tıklayın. Sınıf **Ekle**' yi seçin  >  . Sınıf *filmi* olarak adlandırın.
1. Sınıfına aşağıdaki özellikleri ekleyin `Movie` :

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie`Sınıfı şunları içerir:

* `ID`Alan, birincil anahtar için veritabanı için gereklidir.
* `[DataType(DataType.Date)]`: [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ). Bu öznitelikle:

  * Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.
  * Zaman bilgisi değil yalnızca tarih görüntülenir.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. *Modeller* adlı bir klasör ekleyin.
1. *Movie.cs* adlı *modeller* klasörüne bir sınıf ekleyin.

Sınıfına aşağıdaki özellikleri ekleyin `Movie` :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie`Sınıfı şunları içerir:

* `ID`Alan, birincil anahtar için veritabanı için gereklidir.
* `[DataType(DataType.Date)]`: [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ). Bu öznitelikle:

  * Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.
  * Zaman bilgisi değil yalnızca tarih görüntülenir.

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>NuGet paketleri ve EF araçları ekleme

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI-5.md)]

### <a name="add-a-database-context-class"></a>Veritabanı bağlamı sınıfı ekleme

1. *Razor Pagesmovie* projesinde, *Data* adlı bir klasör oluşturun.
1. *Veri* klasörüne aşağıdaki kodla *Razor PagesMovieContext.cs* adlı bir dosya ekleyin:

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

   Önceki kod, `DbSet` varlık kümesi için bir özellik oluşturur. Entity Framework terimlerinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir ve bir varlık tablodaki bir satıra karşılık gelir. Daha sonraki bir adımda bağımlılıklar eklenene kadar kod derlenmez.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Veritabanı bağlantı dizesi Ekle

*appsettings.json* Aşağıdaki Vurgulanan kodda gösterildiği gibi dosyaya bir bağlantı dizesi ekleyin:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Veritabanı bağlamını kaydetme

1. Aşağıdaki `using` deyimlerini *Startup.cs* üst kısmına ekleyin:

   ```csharp
   using RazorPagesMovie.Data;
   using Microsoft.EntityFrameworkCore;
   ```

1. Veritabanı bağlamını, içindeki [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına Kaydet `Startup.ConfigureServices` :

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. **Çözüm aracı penceresinde**, *Razor pagesmovie* projesine denetim yapın ve ardından  > **Yeni klasör ekle...** seçeneğini belirleyin. Klasör *modellerini* adlandırın.
1. *Modeller* klasörünü kontrol edin ve ardından  > **yeni dosya Ekle...** seçeneğini belirleyin.
1. **Yeni dosya** iletişim kutusunda:
   1. Sol bölmedeki **genel** ' i seçin.
   1. Orta bölmede **boş sınıf** ' ı seçin.
   1. Sınıfı **filmi** adlandırın ve **Yeni**' yi seçin.

1. Sınıfına aşağıdaki özellikleri ekleyin `Movie` :

   [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie`Sınıfı şunları içerir:

* `ID`Alan, birincil anahtar için veritabanı için gereklidir.
* `[DataType(DataType.Date)]`: [[DataType]](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ). Bu öznitelikle:

  * Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.
  * Zaman bilgisi değil yalnızca tarih görüntülenir.

---

[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.

Derleme hatası olmadığını doğrulamak için projeyi derleyin.

## <a name="scaffold-the-movie-model"></a>Film modelini dolandırın

Bu bölümde, film modeli scafkatdır. Diğer bir deyişle, scafkatlama aracı film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. *Sayfalar/filmler* klasörü oluştur:
   1. Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > .
   1. Klasör *filmlerini* adlandırın.

1.   > **Yeni yapı iskelesi öğesi** eklemek > sayfalar/filmler klasörüne sağ tıklayın.

   ![Önceki yönergelerden görüntü.](model/_static/5/sca.png)

1. **Yapı iskelesi Ekle** iletişim kutusunda **Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > .

   ![Önceki yönergelerden görüntü.](model/_static/add_scaffold.png)

1. **Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:
   1. **Model sınıfı** açılan kutusunda **Film ( Razor pagesmovie. modeller)** öğesini seçin.
   1. **Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin.
      1. **Veri bağlamı Ekle** iletişim kutusunda, *Razor pagemovie. Data sınıfını adlandırın. Razor PagesMovieContext* oluşturuldu.
   1. **Add (Ekle)** seçeneğini belirleyin.

   ![Önceki yönergelerden görüntü.](model/_static/3/arp.png)

*appsettings.json* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* *Program.cs*, *Startup.cs* ve *. csproj* dosyalarını içeren proje dizinine bir komut kabuğu açın.

* **Windows için**: aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **MacOS ve Linux için**: aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> Aşağıdaki tabloda ASP.NET Core kod Oluşturucu seçeneklerinin ayrıntıları verilmiştir.

| Seçenek               | Açıklama|
| ----------------- | ------------ |
| `-m`  | Modelin adı. |
| `-dc`  | `DbContext`Kullanılacak sınıf. |
| `-udl` | Varsayılan düzeni kullanın. |
| `-outDir` | Görünümleri oluşturmak için göreli çıkış klasörü yolu. |
| `--referenceScriptLibraries` | `_ValidationScriptsPartial`Sayfaları Düzenle ve oluştur 'a ekler |

`-h`Komutuyla ilgili yardım almak için seçeneğini kullanın `aspnet-codegenerator razorpage` :

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

Daha fazla bilgi için bkz. [DotNet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Geliştirme için SQLite kullanın, üretim için SQL Server

SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın. Aşağıdaki kod nasıl ekleneceğini gösterir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> `Startup` . `IWebHostEnvironment` , uygulamanın geliştirme ve üretimde SQL Server SQLite 'ı kullanabilmesi için eklenir.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

1. *Sayfalar/filmler* klasörü oluştur:
   1. Yeni klasör **Ekle** > *Sayfalar* klasörüne tıklayın > .
   1. Klasör *filmlerini* adlandırın.

1.   > **Yeni yapı iskelesi** eklemek > sayfalar/filmler klasörüne tıklayın.

   ![Önceki yönergelerden görüntü.](model/_static/scaMac.png)

1. **Yeni yapı iskelesi** iletişim kutusunda, İleri **Razor Entity Framework (CRUD) öğesini kullanarak sayfalar** ' ı seçin > .

   ![Önceki yönergelerden görüntü.](model/_static/add_scaffoldMac.png)

1. **Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:
   1. **Kullanılacak DbContext sınıfında:** Row, *Razor pagemovie. Data sınıfını adlandırın. Razor PagesMovieContext*.
   1. **Son**'u seçin.

   ![Önceki yönergelerden görüntü.](model/_static/5/arpMac.png)

*appsettings.json* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Geliştirme için SQLite kullanın, üretim için SQL Server

SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın. Aşağıdaki kod nasıl ekleneceğini gösterir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> `Startup` . `IWebHostEnvironment` , uygulamanın geliştirme ve üretimde SQL Server SQLite 'ı kullanabilmesi için eklenir.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a>Oluşturulan dosyalar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:

* *Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve Index .
* *Veri/ Razor PagesMovieContext.cs*

### <a name="updated"></a>Güncelleştirildi

* *Startup.cs*

Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur:

* *Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve Index .

Oluşturulan dosyalar sonraki bölümde açıklanmaktadır.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:

* *Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve Index .
* *Veri/ Razor PagesMovieContext.cs*

### <a name="updated"></a>Güncelleştirildi

* *Startup.cs*

Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.

---

<a name="pmc"></a>

## <a name="create-the-initial-database-schema-using-efs-migration-feature"></a>EF 'in geçiş özelliğini kullanarak ilk veritabanı şemasını oluşturma

Entity Framework Core geçiş özelliği şunları yapmak için bir yol sağlar:

* İlk veritabanı şemasını oluşturun.
* Veritabanı şemasını uygulamanın veri modeliyle eşitlenmiş halde tutmak için artımlı olarak güncelleştirin.  Veritabanındaki mevcut veriler korunur.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Bu bölümde, için **Paket Yöneticisi konsolu** (PMC) penceresi kullanılır:

* İlk geçiş ekleyin.
* Veritabanını ilk geçişle güncelleştirin.

1. **Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu**' nu seçin.

   ![PMC menüsü](model/_static/5/pmc.png)

1. PMC 'de aşağıdaki komutları girin:

   ```powershell
   Add-Migration InitialCreate
   Update-Database
   ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

* Aşağıdaki .NET CLı komutlarını çalıştırın:

  ```dotnetcli
  dotnet ef migrations add InitialCreate
  dotnet ef database update
  ```

---

Yukarıdaki komutlar şu uyarıyı oluşturur: "' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi. Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur. ' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açık olarak belirtin. "

Daha sonraki bir adımda değinilecek şekilde uyarıyı yoksayın.

`migrations`Komut, ilk veritabanı şemasını oluşturmak için kod üretir. Şema, içinde belirtilen modeli temel alır `DbContext` . `InitialCreate`Bağımsız değişken, geçişleri adlandırmak için kullanılır. Herhangi bir ad kullanılabilir, ancak geçiş işlemini açıklayan bir ad seçilir.

`update`Komutu `Up` uygulanmamış geçişlerde yöntemini çalıştırır. Bu durumda, `update` `Up` veritabanını oluşturan *geçişler/ \<time-stamp> _InitialCreate. cs* dosyasında yöntemini çalıştırır.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Bağımlılık ekleme ile kaydedilen bağlamı inceleyin

ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur. EF Core veritabanı bağlamı gibi hizmetler, uygulamanın başlatılması sırasında bağımlılık ekleme ile kaydedilir. Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır. Bir veritabanı bağlamı örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.

Scafkatlama aracı otomatik olarak bir veritabanı bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.

Yöntemini inceleyin `Startup.ConfigureServices` . Vurgulanan satır, scaffolder tarafından eklendi:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

`RazorPagesMovieContext`Model Için oluşturma, okuma, güncelleştirme ve silme gibi koordinatları EF Core işlevleri `Movie` . Veri bağlamı ( `RazorPagesMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)öğesinden türetilir. Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/Data/RazorPagesMovieContext.cs)]

Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](xref:Microsoft.EntityFrameworkCore.DbSet%601) özelliği oluşturur. Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir. Bir varlık, tablodaki bir satıra karşılık gelir.

Bağlantı dizesinin adı, [Dbcontextoptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) nesnesinde bir yöntem çağırarak bağlama geçirilir. Yerel geliştirme için [yapılandırma sistemi](xref:fundamentals/configuration/index) , bağlantı dizesini *appsettings.json* dosyadan okur.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

Yöntemini inceleyin `Up` .

---

<a name="test"></a>

## <a name="test-the-app"></a>Uygulamayı test etme

1. Uygulamayı çalıştırın ve `/Movies` TARAYıCıDAKI URL 'ye ( `http://localhost:port/movies` ) ekleyin.

   Aşağıdaki hata iletisini alırsanız:

   ```console
   SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
   Login failed for user 'User-name'.
   ```

   [Geçişler adımını](#pmc)kaçırdınız.

1. **Oluştur** bağlantısını test edin.

   ![Sayfa oluşturma](model/_static/conan5.png)

   > [!NOTE]
   > Alana ondalık virgüller giremeyebilirsiniz `Price` . Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir. Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.

1. **Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.

Sonraki öğreticide, yapı iskelesi tarafından oluşturulan dosyalar açıklanmaktadır.

## <a name="additional-resources"></a>Ek kaynaklar

> [!div class="step-by-step"]
> [Önceki: Başlarken](xref:tutorials/razor-pages/razor-pages-start) 
>  [Sonraki: yapı Iskelesi Razor Sayfalar](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: End of >= 5.0 moniker version   -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

Bu bölümde, film yönetimi için sınıflar eklenir. Uygulamanın model sınıfları veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) kullanır. EF Core, veri erişimini kolaylaştıran bir nesne ilişkisel Eşleyici (O/RM).

Model sınıfları, EF Core hiçbir bağımlılığı olmadığından, POCO sınıfları ("düz eski CLR nesnelerinden") olarak bilinir. Veritabanında depolanan verilerin özelliklerini tanımlar.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

## <a name="add-a-data-model"></a>Veri modeli ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Yeni klasör **eklemek**> **Razor pagesmovie** projesine sağ tıklayın  >  . Klasör *modellerini* adlandırın.

*Modeller* klasörüne sağ tıklayın. Sınıf **Ekle**' yi seçin  >  . Sınıf **filmi** olarak adlandırın.

Sınıfına aşağıdaki özellikleri ekleyin `Movie` :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie`Sınıfı şunları içerir:

* `ID`Alan, birincil anahtar için veritabanı için gereklidir.
* `[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ). Bu öznitelikle:

  * Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.
  * Zaman bilgisi değil yalnızca tarih görüntülenir.

[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Modeller* adlı bir klasör ekleyin.
* *Movie.cs* adlı *modeller* klasörüne bir sınıf ekleyin.

Sınıfına aşağıdaki özellikleri ekleyin `Movie` :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie`Sınıfı şunları içerir:

* `ID`Alan, birincil anahtar için veritabanı için gereklidir.
* `[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ). Bu öznitelikle:

  * Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.
  * Zaman bilgisi değil yalnızca tarih görüntülenir.

[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>NuGet paketleri ve EF araçları ekleme

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a>Veritabanı bağlamı sınıfı ekleme

* *Razor Pagesmovie* projesinde, *Data* adlı yeni bir klasör oluşturun.
* Aşağıdaki `RazorPagesMovieContext` sınıfı *veri* klasörüne ekleyin:

  [!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

Önceki kod, `DbSet` varlık kümesi için bir özellik oluşturur. Entity Framework terimlerinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir ve bir varlık tablodaki bir satıra karşılık gelir. Daha sonraki bir adımda bağımlılıklar eklenene kadar kod derlenmez.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Veritabanı bağlantı dizesi Ekle

*appsettings.json* Aşağıdaki Vurgulanan kodda gösterildiği gibi dosyaya bir bağlantı dizesi ekleyin:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

<a name="reg"></a>

### <a name="register-the-database-context"></a>Veritabanı bağlamını kaydetme

Aşağıdaki `using` deyimlerini *Startup.cs* üst kısmına ekleyin:

```csharp
using RazorPagesMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Veritabanı bağlamını içindeki [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedin `Startup.ConfigureServices` .

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Çözüm aracı penceresinde**, **Razor pagesmovie** projesine denetim yapın ve ardından  > **Yeni klasör ekle...** seçeneğini belirleyin. Klasör *modellerini* adlandırın.
* *Modeller* klasörüne sağ tıklayın ve ardından  > **yeni dosya Ekle...** seçeneğini belirleyin.
* **Yeni dosya** iletişim kutusunda:

  * Sol bölmedeki **genel** ' i seçin.
  * Orta bölmede **boş sınıf** ' ı seçin.
  * Sınıfı **filmi** adlandırın ve **Yeni**' yi seçin.

Sınıfına aşağıdaki özellikleri ekleyin `Movie` :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie`Sınıfı şunları içerir:

* `ID`Alan, birincil anahtar için veritabanı için gereklidir.
* `[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ). Bu öznitelikle:

  * Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.
  * Zaman bilgisi değil yalnızca tarih görüntülenir.

---

[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.

Derleme hatası olmadığını doğrulamak için projeyi derleyin.

## <a name="scaffold-the-movie-model"></a>Film modelini dolandırın

Bu bölümde, film modeli scafkatdır. Diğer bir deyişle, scafkatlama aracı film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

*Sayfalar/filmler* klasörü oluştur:

* Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > .
* Klasör *filmlerini* adlandırın.

  > **Yeni yapı iskelesi öğesi** eklemek > sayfalar/filmler klasörüne sağ tıklayın.

![Önceki yönergelerden görüntü.](model/_static/sca.png)

**Yapı iskelesi Ekle** iletişim kutusunda **Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > .

![Önceki yönergelerden görüntü.](model/_static/add_scaffold.png)

**Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:

* **Model sınıfı** açılan kutusunda **Film ( Razor pagesmovie. modeller)** öğesini seçin.
* **Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin ve oluşturulan adı Razor pagesmovie 'den değiştirin.**Modeller**. Razor Pagesmovie 'e PagesMovieContext Razor .**Veri**. Razor PagesMovieContext. [Bu değişiklik](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) gerekli değildir. Doğru ad alanıyla veritabanı bağlamı sınıfını oluşturur.
* **Add (Ekle)** seçeneğini belirleyin.

![Önceki yönergelerden görüntü.](model/_static/3/arp.png)

*appsettings.json* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* *Program.cs*, *Startup.cs* ve *. csproj* dosyalarını içeren proje dizininde bir komut penceresi açın.

* **Windows için**: aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **MacOS ve Linux için**: aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> Aşağıdaki tabloda ASP.NET Core kod Oluşturucu seçeneklerinin ayrıntıları verilmiştir:

| Seçenek               | Açıklama|
| ----------------- | ------------ |
| `-m`  | Modelin adı. |
| `-dc`  | `DbContext`Kullanılacak sınıf. |
| `-udl` | Varsayılan düzeni kullanın. |
| `-outDir` | Görünümleri oluşturmak için göreli çıkış klasörü yolu. |
| `--referenceScriptLibraries` | `_ValidationScriptsPartial`Sayfaları Düzenle ve oluştur 'a ekler |

`-h`Komutuyla ilgili yardım almak için seçeneğini kullanın `aspnet-codegenerator razorpage` :

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

Daha fazla bilgi için bkz. [DotNet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Geliştirme için SQLite kullanın, üretim için SQL Server

SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın. Aşağıdaki kod, başlangıca nasıl ekleneceğini gösterir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> . `IWebHostEnvironment` eklendiğinde, `ConfigureServices` geliştirme ve üretimde SQL Server Için SQLite kullanılabilir.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

*Sayfalar/filmler* klasörü oluştur:

* Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > .
* Klasör *filmlerini* adlandırın.

Yeni yapı iskelesi **eklemek** > *Sayfalar/filmler* klasörüne sağ tıklayın > **...**.

![Önceki yönergelerden görüntü.](model/_static/scaMac.png)

**Yeni yapı iskelesi** iletişim kutusunda, İleri **Razor Entity Framework (CRUD) öğesini kullanarak sayfalar** ' ı seçin > .

![Önceki yönergelerden görüntü.](model/_static/add_scaffoldMac.png)

**Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:

* **Model sınıfı** açılan kutusunda, seçin veya yazın, **Film ( Razor pagesmovie. modeller)**.
* **Veri bağlamı sınıfı** satırına yeni sınıfın adını yazın, Razor pagesfilmi.**Veri**. Razor PagesMovieContext. [Bu değişiklik](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) gerekli değildir. Doğru ad alanıyla veritabanı bağlamı sınıfını oluşturur.
* **Add (Ekle)** seçeneğini belirleyin.

![Önceki yönergelerden görüntü.](model/_static/arpMac.png)

*appsettings.json* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.

### <a name="add-ef-tools"></a>EF araçları ekleme

Aşağıdaki .NET Core CLI komutunu çalıştırın:

```dotnetcli
dotnet tool install --global dotnet-ef
```

Yukarıdaki komut, .NET Core CLI için Entity Framework Core araçları ekler. Daha fazla bilgi için bkz. [Entity Framework Core araçları başvurusu-.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Geliştirme için SQLite kullanın, üretim için SQL Server

SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın. Aşağıdaki kod, başlangıca nasıl ekleneceğini gösterir <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> . `IWebHostEnvironment` eklendiğinde, `ConfigureServices` geliştirme ve üretimde SQL Server Için SQLite kullanılabilir.

[!code-csharp[](~/includes/RP/code/StartupDevProd.cs?name=snippet&highlight=5,10,14)]

---

### <a name="files-created"></a>Oluşturulan dosyalar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:

* *Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve Index .
* *Veri/ Razor PagesMovieContext.cs*

### <a name="updated"></a>Güncelleştirildi

* *Startup.cs*

Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:

* *Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve Index .
* *Veri/ Razor PagesMovieContext.cs*

### <a name="updated"></a>Güncelleştirildi

* *Startup.cs*

Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur:

* *Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve Index .

Oluşturulan dosyalar sonraki bölümde açıklanmaktadır.

---

<a name="pmc"></a>

## <a name="initial-migration"></a>İlk geçiş

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Bu bölümde, Paket Yöneticisi Konsolu (PMC) şu şekilde kullanılır:

* İlk geçiş ekleyin.
* Veritabanını ilk geçişle güncelleştirin.

**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu**' nu seçin.

  ![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

PMC 'de aşağıdaki komutları girin:

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Aşağıdaki .NET Core CLI komutları çalıştırın:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

Yukarıdaki komutlar şu uyarıyı oluşturur: "' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi. Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur. ' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açık olarak belirtin. "

Daha sonraki bir adımda değinilecek şekilde uyarıyı yoksayın.

Geçişler komutu, ilk veritabanı şemasını oluşturmak için kod üretir. Şema, içinde belirtilen modeli temel alır `DbContext` . `InitialCreate`Bağımsız değişken, geçişleri adlandırmak için kullanılır. Herhangi bir ad kullanılabilir, ancak geçiş işlemini açıklayan bir ad seçilir.

`update`Komutu `Up` uygulanmamış geçişlerde yöntemini çalıştırır. Bu durumda, `update` `Up` veritabanını oluşturan  *geçişler/ \<time-stamp> _InitialCreate. cs* dosyasında yöntemini çalıştırır.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Bağımlılık ekleme ile kaydedilen bağlamı inceleyin

ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur. EF Core veritabanı bağlamı bağlamı gibi hizmetler, uygulamanın başlatılması sırasında bağımlılık ekleme ile kaydedilir. Sayfalar gibi bu hizmetleri gerektiren bileşenler Razor , bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır. Bir veritabanı bağlamı bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.

Scafkatlama aracı otomatik olarak bir veritabanı bağlamı bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.

Yöntemini inceleyin `Startup.ConfigureServices` . Vurgulanan satır, scaffolder tarafından eklendi:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

`RazorPagesMovieContext`Model Için oluşturma, okuma, güncelleştirme ve silme gibi koordinatları EF Core işlevleri `Movie` . Veri bağlamı ( `RazorPagesMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)öğesinden türetilir. Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur. Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir. Bir varlık, tablodaki bir satıra karşılık gelir.

Bağlantı dizesinin adı, [Dbcontextoptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) nesnesinde bir yöntem çağırarak bağlama geçirilir. Yerel geliştirme için [yapılandırma sistemi](xref:fundamentals/configuration/index) , bağlantı dizesini *appsettings.json* dosyadan okur.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

Yöntemini inceleyin `Up` .

---

<a name="test"></a>

### <a name="test-the-app"></a>Uygulamayı test etme

* Uygulamayı çalıştırın ve `/Movies` TARAYıCıDAKI URL 'ye ( `http://localhost:port/movies` ) ekleyin.

Şu hatayı alırsanız:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

[Geçişler adımını](#pmc)kaçırdınız.

* **Oluştur** bağlantısını test edin.

  ![Sayfa oluşturma](model/_static/conan5.png)

  > [!NOTE]
  > Alana ondalık virgüller giremeyebilirsiniz `Price` . Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir. Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.

* **Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.

Sonraki öğreticide, yapı iskelesi tarafından oluşturulan dosyalar açıklanmaktadır.

## <a name="additional-resources"></a>Ek kaynaklar

> [!div class="step-by-step"]
> [Önceki: Başlarken](xref:tutorials/razor-pages/razor-pages-start) 
>  [Sonraki: yapı Iskelesi Razor Sayfalar](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

Bu bölümde, platformlar arası bir [SQLite veritabanında](https://www.sqlite.org/index.html)film yönetimi için sınıflar eklenir. Bir ASP.NET Core şablondan oluşturulan uygulamalar bir SQLite veritabanı kullanır. Uygulamanın model sınıfları, veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core veritabanı sağlayıcısı](/ef/core/providers/sqlite)) ile kullanılır. EF Core, veri erişimini kolaylaştıran bir nesne ilişkisel eşleme (ORM) çerçevesidir.

Model sınıfları, EF Core hiçbir bağımlılığı olmadığından, POCO sınıfları ("düz eski CLR nesnelerinden") olarak bilinir. Veritabanında depolanan verilerin özelliklerini tanımlar.

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

## <a name="add-a-data-model"></a>Veri modeli ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Yeni klasör **eklemek**> **Razor pagesmovie** projesine sağ tıklayın  >  . Klasör *modellerini* adlandırın.

*Modeller* klasörüne sağ tıklayın. Sınıf **Ekle**' yi seçin  >  . Sınıf **filmi** olarak adlandırın.

Sınıfına aşağıdaki özellikleri ekleyin `Movie` :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie`Sınıfı şunları içerir:

* `ID`Alan, birincil anahtar için veritabanı için gereklidir.
* `[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ). Bu öznitelikle:

  * Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.
  * Zaman bilgisi değil yalnızca tarih görüntülenir.

[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Modeller* adlı bir klasör ekleyin.
* *Movie.cs* adlı *modeller* klasörüne bir sınıf ekleyin.

Sınıfına aşağıdaki özellikleri ekleyin `Movie` :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie`Sınıfı şunları içerir:

* `ID`Alan, birincil anahtar için veritabanı için gereklidir.
* `[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ). Bu öznitelikle:

  * Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.
  * Zaman bilgisi değil yalnızca tarih görüntülenir.

[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.

<a name="dc"></a>

### <a name="add-nuget-packages-and-ef-tools"></a>NuGet paketleri ve EF araçları ekleme

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

### <a name="add-a-database-context-class"></a>Veritabanı bağlamı sınıfı ekleme

RazorPagesmovie projesinde, *Data* adlı yeni bir klasör oluşturun. 
Aşağıdaki `RazorPagesMovieContext` sınıfı *veri* klasörüne ekleyin:

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Data/RazorPagesMovieContext.cs)]

Önceki kod, `DbSet` varlık kümesi için bir özellik oluşturur. Entity Framework terimlerinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir ve bir varlık tablodaki bir satıra karşılık gelir. Daha sonraki bir adımda bağımlılıklar eklenene kadar kod derlenmez.

<a name="cs"></a>

### <a name="add-a-database-connection-string"></a>Veritabanı bağlantı dizesi Ekle

*appsettings.json* Aşağıdaki Vurgulanan kodda gösterildiği gibi dosyaya bir bağlantı dizesi ekleyin:

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

### <a name="add-required-nuget-packages"></a>Gerekli NuGet paketlerini ekleyin

SQLite ve CodeGeneration eklemek için aşağıdaki .NET Core CLI komutunu çalıştırın. projeye tasarım:

```dotnetcli
dotnet add package Microsoft.EntityFrameworkCore.Sqlite --version 2.2.6
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.2.4
dotnet add package Microsoft.EntityFrameworkCore.Design --version 2.2.6
```

`Microsoft.VisualStudio.Web.CodeGeneration.Design`Paket, yapı iskelesi için gereklidir.

<a name="reg"></a>

### <a name="register-the-database-context"></a>Veritabanı bağlamını kaydetme

Aşağıdaki `using` deyimlerini *Startup.cs* üst kısmına ekleyin:

```csharp
using RazorPagesMovie.Models;
using Microsoft.EntityFrameworkCore;
```

Veritabanı bağlamını içindeki [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedin `Startup.ConfigureServices` .

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

Hataları denetlemek için projeyi bir denetim olarak derleyin.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* **Çözüm aracı penceresinde**, *Razor pagesmovie* projesine denetim yapın ve ardından   >  **Yeni klasör** Ekle ' yi seçin. Klasör *modellerini* adlandırın.
* *Modeller* klasörünü kontrol edin ve ardından  > **yeni dosya** Ekle ' yi seçin.
* **Yeni dosya** iletişim kutusunda:

  * Sol bölmedeki **genel** ' i seçin.
  * Orta bölmede **boş sınıf** ' ı seçin.
  * Sınıfı **filmi** adlandırın ve **Yeni**' yi seçin.

Sınıfına aşağıdaki özellikleri ekleyin `Movie` :

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/Movie.cs?name=snippet1)]

`Movie`Sınıfı şunları içerir:

* `ID`Alan, birincil anahtar için veritabanı için gereklidir.
* `[DataType(DataType.Date)]`: [DataType](xref:System.ComponentModel.DataAnnotations.DataTypeAttribute) özniteliği verilerin türünü belirtir ( `Date` ). Bu öznitelikle:

  * Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.
  * Zaman bilgisi değil yalnızca tarih görüntülenir.

[Veri açıklamaları](xref:System.ComponentModel.DataAnnotations) sonraki bir öğreticide ele alınmıştır.

---

Derleme hatası olmadığını doğrulamak için projeyi derleyin.

## <a name="scaffold-the-movie-model"></a>Film modelini dolandırın

Bu bölümde, film modeli scafkatdır. Diğer bir deyişle, scafkatlama aracı film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

*Sayfalar/filmler* klasörü oluştur:

* Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > .
* Klasör *filmlerini* adlandırın.

  > **Yeni yapı iskelesi öğesi** eklemek > sayfalar/filmler klasörüne sağ tıklayın.

![Önceki yönergelerden görüntü.](model/_static/sca.png)

**Yapı iskelesi Ekle** iletişim kutusunda **Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > .

![Önceki yönergelerden görüntü.](model/_static/add_scaffold.png)

**Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* **Model sınıfı** açılan kutusunda **Film ( Razor pagesmovie. modeller)** öğesini seçin.
* **Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin ve oluşturulan **Razor pagesmovie. modeller adlı adı kabul edin. Razor PagesMovieContext**.
* **Add (Ekle)** seçeneğini belirleyin.

![Önceki yönergelerden görüntü.](model/_static/arp.png)

*appsettings.json* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* *Program.cs*, *Startup.cs* ve *. csproj* dosyalarını içeren proje dizininde bir komut penceresi açın.

* **Windows için**: aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **MacOS ve Linux için**: aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet-aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

<a name="codegenerator"></a> Aşağıdaki tabloda ASP.NET Core kod Oluşturucu seçeneklerinin ayrıntıları verilmiştir:

| Seçenek               | Açıklama|
| ----------------- | ------------ |
| `-m`  | Modelin adı. |
| `-dc`  | `DbContext`Kullanılacak sınıf. |
| `-udl` | Varsayılan düzeni kullanın. |
| `-outDir` | Görünümleri oluşturmak için göreli çıkış klasörü yolu. |
| `--referenceScriptLibraries` | `_ValidationScriptsPartial`Sayfaları Düzenle ve oluştur 'a ekler |

`-h`Komutuyla ilgili yardım almak için seçeneğini kullanın `aspnet-codegenerator razorpage` :

```dotnetcli
dotnet-aspnet-codegenerator razorpage -h
```

Daha fazla bilgi için bkz. [DotNet-ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

*Sayfalar/filmler* klasörü oluştur:

* Yeni klasör **Ekle** > *Sayfalar* klasörüne tıklayın > .
* Klasör *filmlerini* adlandırın.

  > **Yeni yapı iskelesi öğesi** eklemek > sayfalar/filmler klasörüne tıklayın.

![Önceki yönergelerden görüntü.](model/_static/scaMac.png)

**Yeni yapı iskelesi Ekle** iletişim kutusunda **Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > .

![Önceki yönergelerden görüntü.](model/_static/add_scaffoldMac.png)

**Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:

* **Model sınıfı** açılan kutusunda **film**' ı seçin veya yazın.
* **Veri bağlamı sınıfı** satırında, **Razor pagesmoviecontext** ' i seçin. Bu, doğru ad alanıyla yeni bir veritabanı bağlamı sınıfı oluşturur. Bu durumda, **Razor pagesmovie. modeller olacaktır. Razor PagesMovieContext**.
* **Add (Ekle)** seçeneğini belirleyin.

![Önceki yönergelerden görüntü.](model/_static/arpMac.png)

*appsettings.json* Dosya, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.

---

Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:

### <a name="files-created"></a>Oluşturulan dosyalar

* *Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve Index .
* *Veri/ Razor PagesMovieContext.cs*

### <a name="file-updated"></a>Dosya güncelleştirildi

* *Startup.cs*

Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.

<a name="pmc"></a>

## <a name="initial-migration"></a>İlk geçiş

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Bu bölümde, Paket Yöneticisi Konsolu (PMC) şu şekilde kullanılır:

* İlk geçiş ekleyin.
* Veritabanını ilk geçişle güncelleştirin.

**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu**' nu seçin.

  ![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

PMC 'de aşağıdaki komutları girin:

```powershell
Add-Migration Initial
Update-Database
```

`Add-Migration`Komut, ilk veritabanı şemasını oluşturmak için kod üretir. Şema, `DbContext` *Razor PagesMovieContext.cs* dosyasında belirtilen modeli temel alır. `InitialCreate`Bağımsız değişkeni, geçişi adlandırmak için kullanılır. Herhangi bir ad kullanılabilir, ancak kurala göre geçiş işlemini açıklayan bir ad kullanılır. Daha fazla bilgi için bkz. <xref:data/ef-mvc/migrations>.

`Update-Database`Komutu, `Up` *geçişleri/ \<time-stamp> _InitialCreate. cs* dosyasında yöntemini çalıştırır. `Up`Yöntemi veritabanını oluşturur.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

Aşağıdaki .NET Core CLI komutları çalıştırın:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

Yukarıdaki komutlar şu uyarıyı oluşturur: "' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi. Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur. ' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açık olarak belirtin. "

Daha sonraki bir adımda değinilecek şekilde uyarıyı yoksayın.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Bağımlılık ekleme ile kaydedilen bağlamı inceleyin

ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur. Hizmetler (EF Core veritabanı bağlamı bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir. Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır. Bir veritabanı bağlamı contextB bağlam örneğini alan Oluşturucu kodu, daha sonra öğreticide gösterilmiştir.

Scafkatlama aracı otomatik olarak bir veritabanı bağlamı bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.

Yöntemini inceleyin `Startup.ConfigureServices` . Vurgulanan satır, scaffolder tarafından eklendi:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

`RazorPagesMovieContext`Model Için oluşturma, okuma, güncelleştirme ve silme gibi koordinatları EF Core işlevleri `Movie` . Veri bağlamı ( `RazorPagesMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](xref:Microsoft.EntityFrameworkCore.DbContext)öğesinden türetilir. Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur. Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir. Bir varlık, tablodaki bir satıra karşılık gelir.

Bağlantı dizesinin adı, [Dbcontextoptions](xref:Microsoft.EntityFrameworkCore.DbContextOptions) nesnesinde bir yöntem çağırarak bağlama geçirilir. Yerel geliştirme için [yapılandırma sistemi](xref:fundamentals/configuration/index) , bağlantı dizesini *appsettings.json* dosyadan okur.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

Yöntemini inceleyin `Up` .

---

<a name="test"></a>

### <a name="test-the-app"></a>Uygulamayı test etme

* Uygulamayı çalıştırın ve `/Movies` TARAYıCıDAKI URL 'ye ( `http://localhost:port/movies` ) ekleyin.

Şu hatayı alırsanız:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

[Geçişler adımını](#pmc)kaçırdınız.

* **Oluştur** bağlantısını test edin.

  ![Sayfa oluşturma](model/_static/conan.png)

  > [!NOTE]
  > Alana ondalık virgüller giremeyebilirsiniz `Price` . Ondalık bir nokta ve US-English tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir. Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.

* **Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.

Sonraki öğreticide, yapı iskelesi tarafından oluşturulan dosyalar açıklanmaktadır.

## <a name="additional-resources"></a>Ek kaynaklar

> [!div class="step-by-step"]
> [Önceki: Başlarken](xref:tutorials/razor-pages/razor-pages-start) 
>  [Sonraki: yapı Iskelesi Razor Sayfalar](xref:tutorials/razor-pages/page)

::: moniker-end
