---
title: ASP.NET Core MVC uygulamasına model ekleme
author: rick-anderson
description: Basit bir ASP.NET Core uygulamasına model ekleyin.
ms.author: riande
ms.date: 01/13/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: 1347659ee25e2b85b0a479f6bbcc5eb1a956fab2
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776766"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a>ASP.NET Core MVC uygulamasına model ekleme

[Rick Anderson](https://twitter.com/RickAndMSFT) ve [Tom Dykstra](https://github.com/tdykstra) tarafından

Bu bölümde, bir veritabanında film yönetmeye yönelik sınıflar eklersiniz. Bu sınıflar, **d**VC uygulamasının "**d**odel" parçası olacaktır.

Bu sınıfları bir veritabanıyla çalışmak için [Entity Framework Core](/ef/core) (EF Core) ile birlikte kullanırsınız. EF Core, yazmanız gereken veri erişim kodunu kolaylaştıran bir nesne ilişkisel eşleme (ORM) çerçevesidir.

Oluşturduğunuz model sınıfları, EF Core hiçbir bağımlılığı olmadığından, POCO sınıfları olarak bilinir ( **P**Lain **C** **O**) **O**. Yalnızca veritabanında depolanacak verilerin özelliklerini tanımlar.

Bu öğreticide, önce model sınıflarını yazdığınızda EF Core veritabanını oluşturur.

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a>Veri modeli sınıfı ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Sınıf** **Ekle** > > *modeller* klasörüne sağ tıklayın. Dosyayı *Movie.cs*olarak adlandırın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

*Modeller* klasörüne *Movie.cs* adlı bir dosya ekleyin.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

*Modeller* klasörüne sağ tıklayıp**Yeni sınıf** > **boş sınıfı** **Ekle** > >. Dosyayı *Movie.cs*olarak adlandırın.

---

*Movie.cs* dosyasını aşağıdaki kodla güncelleştirin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

`Movie` Sınıfı, birincil anahtar `Id` için veritabanı için gerekli olan bir alanı içerir.

Üzerindeki <xref:System.ComponentModel.DataAnnotations.DataType> `ReleaseDate` özniteliği, veri türünü belirtir (`Date`). Bu öznitelikle:

* Kullanıcının Tarih alanına saat bilgilerini girmesi gerekli değildir.
* Zaman bilgisi değil yalnızca tarih görüntülenir.

[Veri açıklamaları](/dotnet/api/system.componentmodel.dataannotations) sonraki bir öğreticide ele alınmıştır.

## <a name="add-nuget-packages"></a>NuGet paketlerini ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.

![PMC menüsü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

PMC 'de şu komutu çalıştırın:

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

Yukarıdaki komut, EF Core SQL Server sağlayıcısını ekler. Sağlayıcı paketi, EF Core paketini bir bağımlılık olarak yüklüyor. Ek paketler, öğreticinin sonraki bölümlerinde bulunan yapı iskelesi adımında otomatik olarak yüklenir.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

**Proje** menüsünde, **NuGet Paketlerini Yönet**' i seçin.

Sağ üst köşedeki **Ara** alanına girip `Microsoft.EntityFrameworkCore.SQLite` aramak için **dönüş** tuşuna basın. Eşleşen NuGet paketini seçin ve **paket Ekle** düğmesine basın.

![Entity Framework Core NuGet paketi Ekle](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

**Projeleri Seç** iletişim kutusu, `MvcMovie` proje seçiliyken görüntülenir. **Tamam** düğmesine basın.

Bir **Lisans kabul** iletişim kutusu görüntülenir. Lisansları istenen şekilde gözden geçirin ve ardından **kabul et** düğmesine tıklayın.

Aşağıdaki NuGet paketlerini yüklemek için yukarıdaki adımları yineleyin:

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a>Veritabanı bağlamı sınıfı oluşturma

`Movie` Model için EF Core Işlevselliği (oluşturma, okuma, güncelleştirme, silme) koordine etmek için bir veritabanı bağlamı sınıfı gerekir. Veritabanı bağlamı [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) öğesinden türetilir ve veri modeline dahil edilecek varlıkları belirtir.

Bir *veri* klasörü oluşturun.

Aşağıdaki kodla bir *Data/MvcMovieContext. cs* dosyası ekleyin: 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

Önceki kod, varlık kümesi için bir [Dbset\<filmi>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur. Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir. Bir varlık, tablodaki bir satıra karşılık gelir.

<a name="reg"></a>

## <a name="register-the-database-context"></a>Veritabanı bağlamını kaydetme

ASP.NET Core, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile oluşturulmuştur. Hizmetlerin (EF Core DB bağlamı gibi) uygulama başlatma sırasında DI ile kayıtlı olması gerekir. Bu hizmetleri gerektiren bileşenler (örneğin Razor Pages), bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır. Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir. Bu bölümde, veritabanı bağlamını dı kapsayıcısına kaydedersiniz.

Aşağıdaki `using` deyimlerini *Startup.cs*üst kısmına ekleyin:

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Aşağıdaki Vurgulanan kodu içine `Startup.ConfigureServices`ekleyin:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir. Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) , *appSettings. JSON* dosyasından bağlantı dizesini okur.

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a>Veritabanı bağlantı dizesi Ekle

*AppSettings. JSON* dosyasına bir bağlantı dizesi ekleyin:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

Projeyi derleyici hatalarına yönelik bir denetim olarak derleyin.

## <a name="scaffold-movie-pages"></a>Yapı iskelesi film sayfaları

Film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) sayfaları üretmek için scafkatlama aracını kullanın.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Çözüm Gezgini**, *denetleyiciler* klasörüne sağ tıklayıp **yeni > yapı iskelesi> öğesi ekleyin**.

![Yukarıdaki adımın görünümü](adding-model/_static/add_controller21.png)

**Yapı Ekle** iletişim kutusunda, **Entity Framework > Ekle ' yi kullanarak views ile MVC denetleyicisi '** ni seçin.

![Yapı Iskelesi Ekle iletişim kutusu](adding-model/_static/add_scaffold21.png)

**Denetleyici Ekle** iletişim kutusunu doldurun:

* **Model sınıfı:** *Film (mvcmovie. modeller)*
* **Veri bağlamı sınıfı:** *mvcmoviecontext (mvcmovie. Data)*

![Veri bağlamı Ekle](adding-model/_static/dc3.png)

* **Görünümler:** Her seçeneğin varsayılan kısmını işaretli tut
* **Denetleyici adı:** Varsayılan *MoviesController* tut
* **Ekle** 'yi seçin

Visual Studio şunları oluşturur:

* Bir filmler denetleyicisi (*denetleyiciler/MoviesController. cs*)
* Razor oluşturma, silme, ayrıntılar, düzenleme ve dizin sayfaları için dosyaları görüntüleme (*Görünümler/filmler/\*. cshtml*)

Bu dosyaların otomatik olarak oluşturulması, *Yapı iskelesi*olarak bilinir.

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs*ve *. csproj* dosyalarını içeren dizin).

* Linux 'ta, scafkatlama aracı yolunu dışarı aktarın:

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Şu komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs*ve *. csproj* dosyalarını içeren dizin).

* Şu komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

Veritabanı mevcut olmadığından, scafkatmış sayfaları henüz kullanamazsınız. Uygulamayı çalıştırır ve **film uygulaması** bağlantısına tıklarsanız, bir *veritabanı* açılamıyor veya *böyle bir tablo yok: film* hata iletisi.

<a name="migration"></a>

## <a name="initial-migration"></a>İlk geçiş

Veritabanını oluşturmak için EF Core [geçişleri](xref:data/ef-mvc/migrations) özelliğini kullanın. Geçişler, veri modelinizle eşleşecek bir veritabanı oluşturmanıza ve güncelleştirmenize olanak sağlayan bir araç kümesidir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.

PMC 'de aşağıdaki komutları girin:

```powershell
Add-Migration InitialCreate
Update-Database
```

* `Add-Migration InitialCreate`: Bir *geçişler/{timestamp} _InitialCreate. cs* geçiş dosyası oluşturur. `InitialCreate` Bağımsız değişkeni geçiş adıdır. Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir. Bu ilk geçiş olduğundan, oluşturulan sınıf veritabanı şemasını oluşturmak için kod içerir. Veritabanı şeması, `MvcMovieContext` sınıfında belirtilen modeli temel alır.

* `Update-Database`: Veritabanını, önceki komutun oluşturulduğu en son geçişe güncelleştirir. Bu komut, veritabanını `Up` oluşturan *geçişler/{Time-damga} _InitialCreate. cs* dosyasında yöntemini çalıştırır.

  Database Update komutu aşağıdaki uyarıyı üretir: 

  > ' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi. Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur. ' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açıkça belirtin.

  Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilecektir.

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

Aşağıdaki .NET Core CLI komutları çalıştırın:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* `ef migrations add InitialCreate`: Bir *geçişler/{timestamp} _InitialCreate. cs* geçiş dosyası oluşturur. `InitialCreate` Bağımsız değişkeni geçiş adıdır. Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir. Bu ilk geçiş olduğundan, oluşturulan sınıf veritabanı şemasını oluşturmak için kod içerir. Veritabanı şeması `MvcMovieContext` sınıfında belirtilen modeli temel alır ( *Data/MvcMovieContext. cs* dosyasında).

* `ef database update`: Veritabanını, önceki komutun oluşturulduğu en son geçişe güncelleştirir. Bu komut, veritabanını `Up` oluşturan *geçişler/{Time-damga} _InitialCreate. cs* dosyasında yöntemini çalıştırır.

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

---

### <a name="the-initialcreate-class"></a>Initialcreate sınıfı

*Geçişleri/{timestamp} _InitialCreate. cs* geçiş dosyasını inceleyin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

`Up` Yöntemi, film tablosunu oluşturur ve birincil anahtar `Id` olarak yapılandırır. `Down` Yöntemi, `Up` geçiş tarafından yapılan şema değişikliklerini geri alır.

<a name="test"></a>

## <a name="test-the-app"></a>Uygulamayı test edin

* Uygulamayı çalıştırın ve **film uygulaması** bağlantısına tıklayın.

  Aşağıdakilerden birine benzer bir özel durum alırsanız:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  Muhtemelen [geçişler adımını](#migration)kaçırdınız.

* **Oluştur** sayfasını test edin. Veri girin ve gönderebilirsiniz.

  > [!NOTE]
  > `Price` Alana ondalık virgüller giremeyebilirsiniz. Ondalık bir nokta ve ABD Ingilizcesi olmayan tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir. Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.

* **Düzenleme**, **Ayrıntılar**ve **silme** sayfalarını test edin.

## <a name="dependency-injection-in-the-controller"></a>Denetleyiciye bağımlılık ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

*Controllers/MoviesController. cs* dosyasını açın ve oluşturucuyu inceleyin:

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Oluşturucu, veritabanı bağlamını (`MvcMovieContext`) denetleyiciye eklemek Için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır. Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Oluşturucu, veritabanı bağlamını (`MvcMovieContext`) denetleyiciye eklemek Için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır. Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Geliştirme için SQLite kullanın, üretim için SQL Server

SQLite seçildiğinde, şablon tarafından oluşturulan kod geliştirme için hazırlayın. Aşağıdaki kod, başlangıca nasıl ekleneceğini <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> gösterir. `IWebHostEnvironment`eklendiğinde, geliştirme `ConfigureServices` ve üretimde SQL Server için SQLite kullanılabilir.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Türü kesin belirlenmiş modeller ve @model anahtar sözcüğü

Bu öğreticide daha önce, bir denetleyicinin `ViewData` sözlüğü kullanarak bir görünüme nasıl veri veya nesne geçirekullanabileceğinizi gördünüz. Sözlük `ViewData` , bir görünüme bilgi geçirmek için uygun, geç bağlanan bir yol sağlayan dinamik bir nesnedir.

MVC Ayrıca, kesin olarak belirlenmiş model nesnelerini bir görünüme geçirmeye olanak tanır. Bu kesin türü belirtilmiş yaklaşım derleme zamanı kodu denetimini sunar. Yapı iskelesi mekanizması bu yaklaşımı (yani, türü kesin belirlenmiş bir model geçirerek) `MoviesController` sınıf ve görünümlerle kullandı.

`Details` *Controllers/MoviesController. cs* dosyasındaki oluşturulan yöntemi inceleyin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

`id` Parametre genellikle rota verileri olarak geçirilir. Örneğin `https://localhost:5001/movies/details/1` :

* `movies` Denetleyicinin denetleyicisi (ilk URL segmenti).
* Eylem `details` (ikinci URL segmenti).
* Kimliği 1 ' e (son URL segmenti).

Ayrıca, `id` aşağıdaki gibi bir sorgu dizesi ile de geçiş yapabilirsiniz:

`https://localhost:5001/movies/details?id=1`

Parametre `id` , bir kimlik değeri sağlanmadığından [null yapılabilir](/dotnet/csharp/programming-guide/nullable-types/index) bir`int?`tür () olarak tanımlanır.

Bir [lambda ifadesi](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) , rota verileriyle veya `FirstOrDefaultAsync` sorgu dizesi değeriyle eşleşen film varlıklarını seçmek için öğesine geçirilir.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Bir film bulunursa, `Movie` `Details` görünüme bir model örneği geçirilir:

```csharp
return View(movie);
```

*Görünümler/filmler/ayrıntılar. cshtml* dosyasının içeriğini inceleyin:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

Görünüm `@model` dosyasının en üstündeki ifade, görünümün beklediği nesne türünü belirtir. Film denetleyicisi oluşturulduğunda, aşağıdaki `@model` ifade eklenmiştir:

```cshtml
@model MvcMovie.Models.Movie
```

Bu `@model` yönerge, denetleyicinin görünüme geçirildiği filme erişimine izin verir. `Model` Nesne kesin olarak belirlenmiş. Örneğin, *details. cshtml* görünümünde, kod her film alanını `DisplayNameFor` türü kesin belirlenmiş `DisplayFor` `Model` nesne ile HTML yardımcılarının içine geçirir. `Create` Ve `Edit` yöntemleri ve görünümleri de bir `Movie` model nesnesi iletir.

*Index. cshtml* görünümünü ve filmler denetleyicisindeki `Index` yöntemini inceleyin. `View` Yöntemi çağırdığında kodun nasıl bir `List` nesne oluşturduğunu fark edin. Kod bu `Movies` listeyi `Index` eylem yönteminden görünüme geçirir:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Film denetleyicisi oluşturulduğunda, yapı iskelesi *Index. cshtml* dosyasının en üstüne `@model` aşağıdaki ifadeyi içeriyordu:

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

`@model` Yönergesi, kesin olarak belirlenmiş bir `Model` nesne kullanarak denetleyicinin görünüme geçirildiği film listesine erişmenizi sağlar. Örneğin, *Index. cshtml* görünümünde, kod kesin türü belirtilmiş `foreach` `Model` nesne üzerinde bir deyimle birlikte filmlerde döngü yapılır:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

`Model` Nesne türü kesin belirlenmiş olduğundan (bir `IEnumerable<Movie>` nesne olarak), döngüdeki her öğe olarak `Movie`yazılır. Diğer avantajların yanı sıra, kodu derleme zaman denetimini alacağınız anlamına gelir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)
* [Genelleştirme ve yerelleştirme](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Daha önce](adding-view.md)
> [SQL ile çalışan](working-with-sql.md) bir görünüm ekleme

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a>Veri modeli sınıfı ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Sınıf** **Ekle** > > *modeller* klasörüne sağ tıklayın. Sınıf **filmi**olarak adlandırın.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

* *Movie.cs*adlı *modeller* klasörüne bir sınıf ekleyin.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a>Film modelini dolandırın

Bu bölümde, film modeli scafkatdır. Diğer bir deyişle, scafkatlama aracı film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Çözüm Gezgini**, *denetleyiciler* klasörüne sağ tıklayıp **yeni > yapı iskelesi> öğesi ekleyin**.

![Yukarıdaki adımın görünümü](adding-model/_static/add_controller21.png)

**Yapı Ekle** iletişim kutusunda, **Entity Framework > Ekle ' yi kullanarak views ile MVC denetleyicisi '** ni seçin.

![Yapı Iskelesi Ekle iletişim kutusu](adding-model/_static/add_scaffold21.png)

**Denetleyici Ekle** iletişim kutusunu doldurun:

* **Model sınıfı:** *Film (mvcmovie. modeller)*
* **Veri bağlamı sınıfı:** **+** Simgeyi seçin ve varsayılan **Mvcmovie. modeller. MvcMovieContext** öğesini ekleyin

![Veri bağlamı Ekle](adding-model/_static/dc.png)

* **Görünümler:** Her seçeneğin varsayılan kısmını işaretli tut
* **Denetleyici adı:** Varsayılan *MoviesController* tut
* **Ekle** 'yi seçin

![Denetleyici Ekle iletişim kutusu](adding-model/_static/add_controller2.png)

Visual Studio şunları oluşturur:

* Entity Framework Core [veritabanı bağlam sınıfı](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext. cs*)
* Bir filmler denetleyicisi (*denetleyiciler/MoviesController. cs*)
* Razor oluşturma, silme, ayrıntılar, düzenleme ve dizin sayfaları için dosyaları görüntüleme (*Görünümler/filmler/\*. cshtml*)

Veritabanı bağlamı ve [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) (oluşturma, okuma, güncelleştirme ve silme) eylem yöntemlerinin ve görünümlerinin otomatik olarak oluşturulması, *Yapı iskelesi*olarak bilinir.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs*ve *. csproj* dosyalarını içeren dizin).
* Scafkatlama aracını yükler:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* Linux 'ta, scafkatlama aracı yolunu dışarı aktarın:

  ```console
    export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Şu komutu çalıştırın:

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

<!-- Mac -------------------------->

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs*ve *. csproj* dosyalarını içeren dizin).
* Scafkatlama aracını yükler:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* Şu komutu çalıştırın:

  ```dotnetcli
   dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of VS tabs                  -->

Uygulamayı çalıştırır ve **MVC filmi** bağlantısına tıklarsanız aşağıdakine benzer bir hata alırsınız:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

Veritabanını oluşturmanız ve bunu yapmak için EF Core [geçişleri](xref:data/ef-mvc/migrations) özelliğini kullanmanız gerekir. Geçişler veri modelinize uyan bir veritabanı oluşturmanıza ve veri modeliniz değiştiğinde veritabanı şemasını güncelleştirmenize olanak tanır.

<a name="pmc"></a>

## <a name="initial-migration"></a>İlk geçiş

Bu bölümde, aşağıdaki görevler tamamlanır:

* İlk geçiş ekleyin.
* Veritabanını ilk geçişle güncelleştirin.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu** (PMC) öğesini seçin.

   ![PMC menüsü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. PMC 'de aşağıdaki komutları girin:

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   Komut `Add-Migration` , ilk veritabanı şemasını oluşturmak için kod üretir.

   Veritabanı şeması, `MvcMovieContext` sınıfında belirtilen modeli temel alır. `Initial` Bağımsız değişkeni geçiş adıdır. Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad kullanılır. Daha fazla bilgi için bkz. <xref:data/ef-mvc/migrations>.

   `Update-Database` Komutu, veritabanını oluşturan `Up` *geçişler/{Time-damga} _InitialCreate. cs* dosyasında yöntemini çalıştırır.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

Komut `ef migrations add InitialCreate` , ilk veritabanı şemasını oluşturmak için kod üretir.

Veritabanı şeması `MvcMovieContext` sınıfında belirtilen modeli temel alır ( *Data/MvcMovieContext. cs* dosyasında). `InitialCreate` Bağımsız değişkeni geçiş adıdır. Herhangi bir ad kullanılabilir, ancak kurala göre, geçişi açıklayan bir ad seçilidir.

---

## <a name="examine-the-context-registered-with-dependency-injection"></a>Bağımlılık ekleme ile kaydedilen bağlamı inceleyin

ASP.NET Core, [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection)ile oluşturulmuştur. Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında dı ile kaydedilir. Bu hizmetleri gerektiren bileşenler (örneğin Razor Pages), bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır. Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Scafkatlama aracı otomatik olarak bir DB bağlamı oluşturup dı kapsayıcısına kaydetti.

Aşağıdaki `Startup.ConfigureServices` yöntemi inceleyin. Vurgulanan satır, scaffolder tarafından eklendi:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

`MvcMovieContext` Koordinatlar, `Movie` model Için işlevleri EF Core (oluşturma, okuma, güncelleştirme, silme, vb.). Veri bağlamı (`MvcMovieContext`) [Microsoft. Entityframeworkcore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir. Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

Önceki kod, varlık kümesi için bir [Dbset\<filmi>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur. Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir. Bir varlık, tablodaki bir satıra karşılık gelir.

Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir. Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) , *appSettings. JSON* dosyasından bağlantı dizesini okur.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

Bir DB bağlamı oluşturdunuz ve bunu DI kapsayıcısı ile kaydettiniz.

---

<a name="test"></a>

### <a name="test-the-app"></a>Uygulamayı test edin

* Uygulamayı çalıştırın ve tarayıcıdaki URL `/Movies` 'ye (`http://localhost:port/movies`) ekleyin.

Aşağıdakine benzer bir veritabanı özel durumu alırsanız:

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

[Geçişler adımını](#pmc)kaçırdınız.

* **Oluştur** bağlantısını test edin. Veri girin ve gönderebilirsiniz.

  > [!NOTE]
  > `Price` Alana ondalık virgüller giremeyebilirsiniz. Ondalık bir nokta ve ABD Ingilizcesi olmayan tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir. Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.

* **Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.

`Startup` Sınıfı inceleyin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

Önceki vurgulanan kod, [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına eklenen film veritabanı bağlamını gösterir:

* `services.AddDbContext<MvcMovieContext>(options =>`kullanılacak veritabanını ve bağlantı dizesini belirtir.
* `=>`bir [lambda operatörü](/dotnet/articles/csharp/language-reference/operators/lambda-operator)

*Controllers/MoviesController. cs* dosyasını açın ve oluşturucuyu inceleyin:

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Oluşturucu, veritabanı bağlamını (`MvcMovieContext`) denetleyiciye eklemek Için [bağımlılık ekleme](xref:fundamentals/dependency-injection) işlemini kullanır. Veritabanı bağlamı, denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinde her birinde kullanılır.

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Türü kesin belirlenmiş modeller ve @model anahtar sözcüğü

Bu öğreticide daha önce, bir denetleyicinin `ViewData` sözlüğü kullanarak bir görünüme nasıl veri veya nesne geçirekullanabileceğinizi gördünüz. Sözlük `ViewData` , bir görünüme bilgi geçirmek için uygun, geç bağlanan bir yol sağlayan dinamik bir nesnedir.

MVC Ayrıca, kesin olarak belirlenmiş model nesnelerini bir görünüme geçirmeye olanak tanır. Bu kesin türü belirtilmiş yaklaşım, kodunuzun daha iyi derleme zaman denetimini sunar. Yapı iskelesi mekanizması, bu yaklaşımı (yani, türü kesin belirlenmiş bir model geçirerek), yöntem ve `MoviesController` görünümleri oluştururken sınıfı ve görünümleri ile kullandı.

`Details` *Controllers/MoviesController. cs* dosyasındaki oluşturulan yöntemi inceleyin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

`id` Parametre genellikle rota verileri olarak geçirilir. Örneğin `https://localhost:5001/movies/details/1` :

* `movies` Denetleyicinin denetleyicisi (ilk URL segmenti).
* Eylem `details` (ikinci URL segmenti).
* Kimliği 1 ' e (son URL segmenti).

Ayrıca, `id` aşağıdaki gibi bir sorgu dizesi ile de geçiş yapabilirsiniz:

`https://localhost:5001/movies/details?id=1`

Parametre `id` , bir kimlik değeri sağlanmadığından [null yapılabilir](/dotnet/csharp/programming-guide/nullable-types/index) bir`int?`tür () olarak tanımlanır.

Bir [lambda ifadesi](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) , rota verileriyle veya `FirstOrDefaultAsync` sorgu dizesi değeriyle eşleşen film varlıklarını seçmek için öğesine geçirilir.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Bir film bulunursa, `Movie` `Details` görünüme bir model örneği geçirilir:

```csharp
return View(movie);
   ```

*Görünümler/filmler/ayrıntılar. cshtml* dosyasının içeriğini inceleyin:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

Görünüm dosyasının üst `@model` kısmına bir ifade ekleyerek, görünümün beklediği nesne türünü belirtebilirsiniz. Film denetleyicisini oluştururken, *Ayrıntılar. cshtml* dosyasının en `@model` üstüne aşağıdaki ifade otomatik olarak eklenmiştir:

```cshtml
@model MvcMovie.Models.Movie
```

Bu `@model` yönerge, kesin olarak belirlenmiş bir `Model` nesne kullanarak denetleyicinin görünüme geçirildiği filme erişmenizi sağlar. Örneğin, *details. cshtml* görünümünde, kod her film alanını `DisplayNameFor` türü kesin belirlenmiş `DisplayFor` `Model` nesne ile HTML yardımcılarının içine geçirir. `Create` Ve `Edit` yöntemleri ve görünümleri de bir `Movie` model nesnesi iletir.

*Index. cshtml* görünümünü ve filmler denetleyicisindeki `Index` yöntemini inceleyin. `View` Yöntemi çağırdığında kodun nasıl bir `List` nesne oluşturduğunu fark edin. Kod bu `Movies` listeyi `Index` eylem yönteminden görünüme geçirir:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Film denetleyicisini oluştururken, yapı iskelesi *Index. cshtml* dosyasının en üstüne aşağıdaki `@model` ifadeyi otomatik olarak dahil edin:

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

`@model` Yönergesi, kesin olarak belirlenmiş bir `Model` nesne kullanarak denetleyicinin görünüme geçirildiği film listesine erişmenizi sağlar. Örneğin, *Index. cshtml* görünümünde, kod kesin türü belirtilmiş `foreach` `Model` nesne üzerinde bir deyimle birlikte filmlerde döngü yapılır:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

`Model` Nesne türü kesin belirlenmiş olduğundan (bir `IEnumerable<Movie>` nesne olarak), döngüdeki her öğe olarak `Movie`yazılır. Diğer avantajların yanı sıra, kodun derleme zaman denetimini alacağınız anlamına gelir:

## <a name="additional-resources"></a>Ek kaynaklar

* [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)
* [Genelleştirme ve yerelleştirme](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Daha önce](adding-view.md)
> [bir veritabanı ile çalışan](working-with-sql.md) bir görünüm ekleme

::: moniker-end
