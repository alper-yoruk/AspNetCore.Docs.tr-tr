---
title: ASP.NET Core MVC uygulamasına model ekleme
author: rick-anderson
description: Basit bir ASP.NET Core uygulamasına bir model ekleyin.
ms.author: riande
ms.date: 01/13/2020
uid: tutorials/first-mvc-app/adding-model
ms.openlocfilehash: e7fc0496438734e13cfafcecf432da4a94737897
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79434518"
---
# <a name="add-a-model-to-an-aspnet-core-mvc-app"></a>ASP.NET Core MVC uygulamasına model ekleme

Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Tom Dykstra](https://github.com/tdykstra)

Bu bölümde, bir veritabanında film yönetmek için sınıflar ekleyin. Bu sınıflar **M**VC uygulamasının **"M**odel" parçası olacaktır.

Bir veritabanı ile çalışmak için Bu sınıfları [Entity Framework Core](/ef/core) (EF Core) ile kullanırsınız. EF Core, yazmanız gereken veri erişim kodunu basitleştiren nesne ilişkisisel eşleme (ORM) çerçevesidir.

Oluşturduğunuz model sınıfları POCO sınıfları olarak bilinir **(P**lain **O**ld **C**LR **O**bjects'ten) çünkü EF Core'a herhangi bir bağımlılıkları yoktur. Yalnızca veritabanında depolanacak verilerin özelliklerini tanımlarlar.

Bu öğreticide, önce model sınıflarını yazarsınız ve EF Core veritabanını oluşturur.

::: moniker range=">= aspnetcore-3.0"

## <a name="add-a-data-model-class"></a>Veri modeli sınıfı ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Sınıf** **Ekle** > > *Modeller* klasörüne sağ tıklayın. Dosyayı *Movie.cs.*

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

*Modeller* klasörüne *Movie.cs* adlı bir dosya ekleyin.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

**Yeni**Sınıf > **Boş Sınıf** **Ekle** > > *Modeller* klasörüne sağ tıklayın. Dosyayı *Movie.cs.*

---

*Movie.cs* dosyasını aşağıdaki kodla güncelleştirin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/Movie.cs)]

Sınıf, `Movie` birincil `Id` anahtar için veritabanı tarafından gerekli olan bir alan içerir.

Veri <xref:System.ComponentModel.DataAnnotations.DataType> türüne `ReleaseDate` ilişkin öznitelik (`Date`) Bu öznitelik ile:

* Kullanıcının tarih alanına saat bilgilerini girmesi gerekmez.
* Yalnızca tarih görüntülenir, saat bilgisi değil.

[DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) daha sonraki bir öğretici ele alınmıştır.

## <a name="add-nuget-packages"></a>NuGet paketlerini ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Araçlar** menüsünden **NuGet Paket Yöneticisi** > **Paket Yöneticisi Konsolu'nu** (PMC) seçin.

![PMC menüsü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

PMC'de aşağıdaki komutu çalıştırın:

```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

Önceki komut, EF Core SQL Server sağlayıcısını ekler. Sağlayıcı paketi, EF Core paketini bağımlılık olarak yükler. Ek paketler, öğreticinin ilerleyen saatlerinde iskele adımına otomatik olarak yüklenir.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/add-EF-NuGet-SQLite-CLI.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

**Proje** menüsünden **NuGet Paketlerini Yönet'i**seçin.

Sağ üstteki **Arama** alanına girin `Microsoft.EntityFrameworkCore.SQLite` ve aramak için **İade** tuşuna basın. Eşleşen NuGet paketini seçin ve **Paket Ekle** düğmesine basın.

![Varlık Çerçeve Çekirdek NuGet Paketi Ekle](~/tutorials/first-mvc-app-mac/adding-model/_static/add-nuget-packages.png)

**Projeleri Seç** iletişim kutusu, `MvcMovie` proje seçili olarak görüntülenir. **Tamam** düğmesine basın.

**Lisans Kabul** iletişim kutusu görüntülenir. Lisansları istediğiniz gibi gözden geçirin ve **ardından Kabul Et** düğmesini tıklatın.

Aşağıdaki NuGet paketlerini yüklemek için yukarıdaki adımları tekrarlayın:

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Design`

---

<a name="dc"></a>

## <a name="create-a-database-context-class"></a>Veritabanı bağlam sınıfı oluşturma

`Movie` Model için EF Core işlevselliğini (Oluştur, Oku, Güncelle, Sil) koordine etmek için bir veritabanı bağlam ı gereklidir. Veritabanı bağlamı [Microsoft.EntityFrameworkCore.DbContext'den](/dotnet/api/microsoft.entityframeworkcore.dbcontext) türetilmiştir ve veri modeline dahil olacak varlıkları belirtir.

Bir *Veri* klasörü oluşturun.

Aşağıdaki kodile bir *Veri/MvcMovieContext.cs* dosyası ekleyin: 

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/zDocOnly/MvcMovieContext.cs?name=snippet)]

Önceki kod, varlık kümesi için bir [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur. Varlık Çerçevesi terminolojisinde, bir varlık kümesi genellikle bir veritabanı tablosuna karşılık gelir. Bir varlık tablodaki bir satıra karşılık gelir.

<a name="reg"></a>

## <a name="register-the-database-context"></a>Veritabanı bağlamını kaydetme

ASP.NET Core [bağımlılık enjeksiyonu (DI)](xref:fundamentals/dependency-injection)ile inşa edilmiştir. Hizmetler (EF Core DB bağlamı gibi) uygulama nın başlatılması sırasında DI'ye kaydedilmelidir. Bu hizmetleri gerektiren bileşenlere (Jilet Sayfaları gibi) bu hizmetler yapıcı parametreler aracılığıyla sağlanır. DB bağlam örneğini alan kurucu kodu daha sonra öğreticide gösterilir. Bu bölümde, veritabanı bağlamını DI kapsayıcısı ile kaydedebilirsiniz.

Startup.cs üst `using` kısmında aşağıdaki *Startup.cs*ifadeleri ekleyin:

```csharp
using MvcMovie.Data;
using Microsoft.EntityFrameworkCore;
```

Aşağıdaki vurgulanan kodu `Startup.ConfigureServices`ekleyin:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=6-7)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=6-7)]

---

Bağlantı dizesinin adı, [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesindeki bir yöntem çağırılarak içeriğe aktarılır. Yerel geliştirme [için, ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) *appsettings.json* dosyasından bağlantı dizesini okur.

<a name="cs"></a>

## <a name="add-a-database-connection-string"></a>Veritabanı bağlantı dizesi ekleme

*appsettings.json* dosyasına bağlantı dizesi ekleyin:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/appsettings_SQLite.json?highlight=10-12)]

---

Derleyici hataları için bir denetim olarak proje oluşturun.

## <a name="scaffold-movie-pages"></a>İskele film sayfaları

Film modeli için Oluştur, Oku, Güncelleştir ve Sil (CRUD) sayfalarını oluşturmak için iskele aracını kullanın.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Çözüm Gezgini'nde,** Yeni İskele Öğesi > **ekle>** *Denetleyiciler* klasörüne sağ tıklayın.

![yukarıdaki adımın görünümü](adding-model/_static/add_controller21.png)

İskele **Ekle** iletişim kutusunda, **Entity Framework > Add'i kullanarak görünümlü MVC**Denetleyicisi'ni seçin.

![İskele iletişim kutusu ekle](adding-model/_static/add_scaffold21.png)

Denetleyici **Ekle** iletişim kutusunu tamamlayın:

* **Model sınıfı:** *Film (MvcMovie.Models)*
* **Veri bağlamı sınıfı:** *MvcMovieContext (MvcMovie.Data)*

![Veri bağlamı ekleme](adding-model/_static/dc3.png)

* **Görünümler:** Her seçeneğin varsayılanını işaretli tutun
* **Denetleyici adı:** Varsayılan *MoviesController'ı* tutun
* **Ekle**’yi seçin

Visual Studio oluşturur:

* Bir film denetleyicisi (*Denetleyiciler/MoviesController.cs*)
* Oluşturma, Silme, Ayrıntılar, Düzenle ve Dizin sayfaları için jilet görüntüleme dosyaları (*Görünümler/Filmler/\*.cshtml*)

Bu dosyaların otomatik oluşturulması *iskele*olarak bilinir.

### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code) 

* Proje dizininde bir komut penceresi açın *(Program.cs,* *Startup.cs*ve *.csproj* dosyalarını içeren dizin).

* Linux'ta, iskele araç yolunu dışa aktarın:

  ```console
  export PATH=$HOME/.dotnet/tools:$PATH
  ```

* Şu komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

### <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* Proje dizininde bir komut penceresi açın *(Program.cs,* *Startup.cs*ve *.csproj* dosyalarını içeren dizin).

* Şu komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator controller -name MoviesController -m Movie -dc MvcMovieContext --relativeFolderPath Controllers --useDefaultLayout --referenceScriptLibraries
  ```

  [!INCLUDE [explains scaffold generated params](~/includes/mvc-intro/model4.md)]

---

<!-- End of tabs                  -->

Veritabanı yok çünkü henüz iskele sayfaları kullanamazsınız. Uygulamayı çalıştırıp **Film Uygulaması** bağlantısına tıklarsanız, *açılamaz veritabanı* alırsınız veya böyle bir tablo *olmaz: Film* hatası iletisi.

<a name="migration"></a>

## <a name="initial-migration"></a>İlk geçiş

Veritabanını oluşturmak için EF Çekirdek [Geçişleri](xref:data/ef-mvc/migrations) özelliğini kullanın. Geçişler, veri modelinize uyacak şekilde bir veritabanı oluşturmanıza ve güncelleştirmenize izin veren araçlar kümesidir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Araçlar** menüsünden **NuGet Paket Yöneticisi** > **Paket Yöneticisi Konsolu'nu** (PMC) seçin.

PMC'de aşağıdaki komutları girin:

```powershell
Add-Migration InitialCreate
Update-Database
```

* `Add-Migration InitialCreate`: *Geçişler/{timestamp}_InitialCreate.cs* geçiş dosyası oluşturur. Bağımsız `InitialCreate` değişken geçiş adıdır. Herhangi bir ad kullanılabilir, ancak kural olarak, geçişi açıklayan bir ad seçilir. Bu ilk geçiş olduğundan, oluşturulan sınıf veritabanı şemasını oluşturmak için kod içerir. Veritabanı şeması, sınıfta belirtilen modele `MvcMovieContext` dayanır.

* `Update-Database`: Veritabanını önceki komutun oluşturduğu en son geçişle güncelleştirir. Bu komut, `Up` veritabanını oluşturan *Geçişler/{zaman damgası}_InitialCreate.cs* dosyasında yöntemi çalıştırAr.

  Veritabanı güncelleştirme komutu aşağıdaki uyarıyı oluşturur: 

  > Varlık türü 'Film'deki ondalık sütun 'Fiyat' için hiçbir tür belirtilmedi. Bu, varsayılan kesinlik ve skalaya sığmazsa değerlerin sessizce kesildirilmesine neden olur. 'HasColumnType()' kullanarak tüm değerleri barındırabilecek SQL sunucu sütun türünü açıkça belirtin.

  Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilir.

[!INCLUDE [more information on the PMC tools for EF Core](~/includes/ef-pmc.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

Aşağıdaki .NET Core CLI komutlarını çalıştırın:

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

* `ef migrations add InitialCreate`: *Geçişler/{timestamp}_InitialCreate.cs* geçiş dosyası oluşturur. Bağımsız `InitialCreate` değişken geçiş adıdır. Herhangi bir ad kullanılabilir, ancak kural olarak, geçişi açıklayan bir ad seçilir. Bu ilk geçiş olduğundan, oluşturulan sınıf veritabanı şemasını oluşturmak için kod içerir. Veritabanı şeması `MvcMovieContext` sınıfta belirtilen modele dayanır *(Data/MvcMovieContext.cs* dosyasında).

* `ef database update`: Veritabanını önceki komutun oluşturduğu en son geçişle güncelleştirir. Bu komut, `Up` veritabanını oluşturan *Geçişler/{zaman damgası}_InitialCreate.cs* dosyasında yöntemi çalıştırAr.

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

---

### <a name="the-initialcreate-class"></a>InitialCreate sınıfı

*Geçişler/{timestamp}_InitialCreate.cs* geçiş dosyasını inceleyin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Migrations/20190805165915_InitialCreate.cs?name=snippet)]

Yöntem `Up` Film tablosunu oluşturur ve `Id` birincil anahtar olarak yapılandırır. Yöntem, `Down` geçiş tarafından yapılan şema değişikliklerini `Up` geri alır.

<a name="test"></a>

## <a name="test-the-app"></a>Uygulamayı test edin

* Uygulamayı çalıştırın ve **Film Uygulaması** bağlantısını tıklayın.

  Aşağıdakilerden birine benzer bir özel durum alırsanız:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

  ```console
  SqlException: Cannot open database "MvcMovieContext-1" requested by the login. The login failed.
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

  ```console
  SqliteException: SQLite Error 1: 'no such table: Movie'.
  ```

---
  Muhtemelen göç [adımını kaçırmışsınızdır.](#migration)

* **Oluştur** sayfasını test edin. Veri girin ve gönderin.

  > [!NOTE]
  > `Price` Alana ondalık virgül giremeyebilirsiniz. Ondalık bir nokta için virgül (",") kullanan İngilizce olmayan yerel halkiçin jQuery doğrulaması ve ABD İngilizce olmayan tarih biçimleri için [jQuery doğrulaması](https://jqueryvalidation.org/) desteklemek için uygulamanın genelleştirilmiş olması gerekir. Küreselleşme yönergeleri için [bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.

* **Düzenle,** **Ayrıntıları**ve **Sayfalarını Sil'i** test edin.

## <a name="dependency-injection-in-the-controller"></a>Denetleyicide bağımlılık enjeksiyonu

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

*Denetleyiciler/MoviesController.cs* dosyasını açın ve oluşturucuyu inceleyin:

<!-- l.. Make copy of Movies controller (or use the old one as I did in the 3.0 upgrade) because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Oluşturucu, veritabanı bağlamını denetleyiciye`MvcMovieContext`enjekte etmek için Bağımlılık [Enjeksiyonu](xref:fundamentals/dependency-injection) kullanır. Veritabanı bağlamı denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinin her birinde kullanılır.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Oluşturucu, veritabanı bağlamını denetleyiciye`MvcMovieContext`enjekte etmek için Bağımlılık [Enjeksiyonu](xref:fundamentals/dependency-injection) kullanır. Veritabanı bağlamı denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinin her birinde kullanılır.

### <a name="use-sqlite-for-development-sql-server-for-production"></a>Geliştirme için SQLite'ı, üretim için SQL Server'ı kullanın

SQLite seçildiğinde, şablon oluşturulan kod geliştirmeye hazırdır. Aşağıdaki kod, Başlangıç'a nasıl enjekte <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> ediletilir gösterir. `IWebHostEnvironment`böylece geliştirme `ConfigureServices` ve SQL Server üretimde SQLite kullanabilirsiniz enjekte edilir.

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/StartupDevProd.cs?name=snippet_StartupClass&highlight=5,10,16-28)]

---
<!-- end of tabs --->

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Güçlü bir şekilde yazılan @model modeller ve anahtar kelime

Bu öğreticinin başlarında, bir denetleyicinin `ViewData` sözlüğü kullanarak verileri veya nesneleri görünüme nasıl geçirebileceğini gördün. Sözlük, `ViewData` bilgileri bir görünüme aktarmak için uygun bir geç bağlı yol sağlayan dinamik bir nesnedir.

MVC ayrıca güçlü bir şekilde yazılan model nesnelerini bir görünüme geçirme olanağı da sağlar. Bu güçlü bir şekilde yazılan yaklaşım, zaman kodu denetiminin derlenmesine olanak tanır. İskele mekanizması sınıf ve görünümler ile `MoviesController` bu yaklaşımı (yani, güçlü bir şekilde yazılan bir model geçen) kullanılır.

`Details` *Denetleyiciler/MoviesController.cs* dosyasında oluşturulan yöntemi inceleyin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

`id` Parametre genellikle rota verisi olarak geçirilir. Örneğin `https://localhost:5001/movies/details/1` kümeler:

* Denetleyiciye `movies` denetleyici (ilk URL segmenti).
* Eylem `details` (ikinci URL segmenti).
* Kimlik 1 (son URL segmenti).

Ayrıca aşağıdaki gibi `id` bir sorgu dize ile geçirebilirsiniz:

`https://localhost:5001/movies/details?id=1`

Bir `id` kimlik değeri sağlanmadığı durumlarda`int?`parametre , () geçersiz bir [tür](/dotnet/csharp/programming-guide/nullable-types/index) olarak tanımlanır.

Yol verileri veya sorgu `FirstOrDefaultAsync` dize değeriyle eşleşen film varlıklarını seçmek için [bir lambda ifadesi](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) geçirilir.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Bir film bulunursa, modelin `Movie` bir örneği `Details` görünüme aktarılır:

```csharp
return View(movie);
```

*Görünümler/Filmler/Ayrıntılar.cshtml* dosyasının içeriğini inceleyin:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

Görünüm `@model` dosyasının üst kısmındaki deyim, görünümün beklediği nesne türünü belirtir. Film denetleyicisi oluşturulduğunda `@model` aşağıdaki ifade eklenmiştir:

```cshtml
@model MvcMovie.Models.Movie
```

Bu `@model` yönerge, denetleyicinin görünüme geçtiği filme erişim sağlar. Nesne `Model` güçlü bir şekilde yazılır. Örneğin, *Details.cshtml* görünümünde, kod her film alanını `DisplayNameFor` `DisplayFor` güçlü bir şekilde yazılan `Model` nesneye sahip HTML Yardımcıları ve HTML Yardımcıları'na geçirir. Ve yöntemler ve görünümler `Movie` de bir model nesnesi geçmek. `Edit` `Create`

Filmler denetleyicisinde *Index.cshtml* görünümünü ve `Index` yöntemi inceleyin. Kodun yöntemi aradığında `List` nesneyi nasıl `View` oluşturduğuna dikkat edin. Kod bu `Movies` listeyi `Index` eylem yönteminden görünüme geçirir:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Film denetleyicisi oluşturulduğunda, iskele `@model` *Index.cshtml* dosyasının üst kısmında aşağıdaki ifadeyi içerir:

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

Yönerge, `@model` güçlü bir şekilde yazılan bir `Model` nesneyi kullanarak denetleyicinin görünüme geçtiği film listesine erişmenizi sağlar. Örneğin, *Index.cshtml* görünümünde, kod filmler arasında güçlü `foreach` bir şekilde yazılan `Model` nesnenin üzerinde bir ifadeyle döngüler:

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

`Model` Nesne güçlü bir şekilde `IEnumerable<Movie>` (nesne olarak) yazıldığı için, döngüdeki her öğe `Movie`. Diğer avantajların yanı sıra, bu kodun zaman denetimini derlediğiniz anlamına gelir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)
* [Genelleştirme ve yerelleştirme](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Önceki](adding-view.md)
> [SQL ile](working-with-sql.md) Sonraki Çalışma Görünümü Ekleme

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="add-a-data-model-class"></a>Veri modeli sınıfı ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Sınıf** **Ekle** > > *Modeller* klasörüne sağ tıklayın. Sınıf **Film**adı .

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

* *Movie.cs*adlı *Modeller* klasörüne bir sınıf ekleyin.

[!INCLUDE [model 1b](~/includes/mvc-intro/model1b.md)]
[!INCLUDE [model 2](~/includes/mvc-intro/model2.md)]

---

## <a name="scaffold-the-movie-model"></a>İskele film modeli

Bu bölümde, film modeli iskele. Diğer bir zamanda, iskele aracı film modeli için Oluştur, Oku, Güncelleştir ve Sil (CRUD) işlemleri için sayfalar oluşturur.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**Çözüm Gezgini'nde,** Yeni İskele Öğesi > **ekle>** *Denetleyiciler* klasörüne sağ tıklayın.

![yukarıdaki adımın görünümü](adding-model/_static/add_controller21.png)

İskele **Ekle** iletişim kutusunda, **Entity Framework > Add'i kullanarak görünümlü MVC**Denetleyicisi'ni seçin.

![İskele iletişim kutusu ekle](adding-model/_static/add_scaffold21.png)

Denetleyici **Ekle** iletişim kutusunu tamamlayın:

* **Model sınıfı:** *Film (MvcMovie.Models)*
* **Veri bağlamı sınıfı:** Simgeyi **+** seçin ve varsayılan **MvcMovie.Models.MvcMovieContext** ekleyin

![Veri bağlamı ekleme](adding-model/_static/dc.png)

* **Görünümler:** Her seçeneğin varsayılanını işaretli tutun
* **Denetleyici adı:** Varsayılan *MoviesController'ı* tutun
* **Ekle**’yi seçin

![Denetleyici ekle iletişim kutusu](adding-model/_static/add_controller2.png)

Visual Studio oluşturur:

* Varlık Çerçeve Çekirdek [veritabanı bağlam sınıfı](xref:data/ef-mvc/intro#create-the-database-context) (*Data/MvcMovieContext.cs*)
* Bir film denetleyicisi (*Denetleyiciler/MoviesController.cs*)
* Oluşturma, Silme, Ayrıntılar, Düzenle ve Dizin sayfaları için jilet görüntüleme dosyaları (*Görünümler/Filmler/\*.cshtml*)

Veritabanı bağlamı ve [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) otomatik oluşturma (oluşturmak, okumak, güncellemek ve sil) eylem yöntemleri ve görünümleri *iskele*olarak bilinir.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Proje dizininde bir komut penceresi açın *(Program.cs,* *Startup.cs*ve *.csproj* dosyalarını içeren dizin).
* İskele aracını yükleyin:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* Linux'ta, iskele araç yolunu dışa aktarın:

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

* Proje dizininde bir komut penceresi açın *(Program.cs,* *Startup.cs*ve *.csproj* dosyalarını içeren dizin).
* İskele aracını yükleyin:

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

Uygulamayı çalıştırıp **Mvc Film** bağlantısını tıklarsanız, aşağıdakilere benzer bir hata alırsınız:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```
An unhandled exception occurred while processing the request.

SqlException: Cannot open database "MvcMovieContext-<GUID removed>" requested by the login. The login failed.
Login failed for user 'Rick'.

System.Data.SqlClient.SqlInternalConnectionTds..ctor(DbConnectionPoolIdentity identity, SqlConnectionString
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

```
An unhandled exception occurred while processing the request.
SqliteException: SQLite Error 1: 'no such table: Movie'.
Microsoft.Data.Sqlite.SqliteException.ThrowExceptionForRC(int rc, sqlite3 db)

```

---

Veritabanını oluşturmanız gerekir ve bunu yapmak için EF Çekirdek [Geçişleri](xref:data/ef-mvc/migrations) özelliğini kullanırsınız. Geçişler, veri modelinize uyan bir veritabanı oluşturmanıza ve veri modeliniz değiştiğinde veritabanı şemasını güncelleştirmenize olanak tanır.

<a name="pmc"></a>

## <a name="initial-migration"></a>İlk geçiş

Bu bölümde, aşağıdaki görevler tamamlanır:

* İlk geçiş ekleyin.
* Veritabanını ilk geçişle güncelleştirin.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. **Araçlar** menüsünden **NuGet Paket Yöneticisi** > **Paket Yöneticisi Konsolu'nu** (PMC) seçin.

   ![PMC menüsü](~/tutorials/first-mvc-app/adding-model/_static/pmc.png)

1. PMC'de aşağıdaki komutları girin:

   ```powershell
   Add-Migration Initial
   Update-Database
   ```

   Komut, `Add-Migration` ilk veritabanı şemasını oluşturmak için kod oluşturur.

   Veritabanı şeması, sınıfta belirtilen modele `MvcMovieContext` dayanır. Bağımsız `Initial` değişken geçiş adıdır. Herhangi bir ad kullanılabilir, ancak kuralı yla, geçişi açıklayan bir ad kullanılır. Daha fazla bilgi için bkz. <xref:data/ef-mvc/migrations>.

   Komut, `Update-Database` veritabanını `Up` oluşturan *Geçişler/{zaman damgası}_InitialCreate.cs* dosyasında yöntemi çalıştırAr.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

Komut, `ef migrations add InitialCreate` ilk veritabanı şemasını oluşturmak için kod oluşturur.

Veritabanı şeması `MvcMovieContext` sınıfta belirtilen modele dayanır *(Data/MvcMovieContext.cs* dosyasında). Bağımsız `InitialCreate` değişken geçiş adıdır. Herhangi bir ad kullanılabilir, ancak kural olarak, geçişi açıklayan bir ad seçilir.

---

## <a name="examine-the-context-registered-with-dependency-injection"></a>Bağımlılık enjeksiyonu ile kayıtlı bağlamı inceleyin

ASP.NET Core [bağımlılık enjeksiyonu (DI)](xref:fundamentals/dependency-injection)ile inşa edilmiştir. Hizmetler (EF Core DB bağlamı gibi) uygulama nın başlatılması sırasında DI'ye kaydedilir. Bu hizmetleri gerektiren bileşenlere (Jilet Sayfaları gibi) bu hizmetler yapıcı parametreler aracılığıyla sağlanır. DB bağlam örneğini alan kurucu kodu daha sonra öğreticide gösterilir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

İskele aracı otomatik olarak bir DB bağlamı oluşturdu ve di kapsayıcıile kaydetti.

Aşağıdaki `Startup.ConfigureServices` yöntemi inceleyin. Vurgulanan satır iskele tarafından eklendi:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=14-15)]

Model için EF Core işlevselliğini (Oluştur, Oku, Güncelle, Sil, vb.) `MvcMovieContext` koordinatları. `Movie` Veri bağlamı`MvcMovieContext`( ) [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)türetilmiştir. Veri bağlamı, veri modeline hangi varlıkların dahil edildiğini belirtir:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Data/MvcMovieContext.cs)]

Önceki kod, varlık kümesi için bir [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur. Varlık Çerçevesi terminolojisinde, bir varlık kümesi genellikle bir veritabanı tablosuna karşılık gelir. Bir varlık tablodaki bir satıra karşılık gelir.

Bağlantı dizesinin adı, [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesindeki bir yöntem çağırılarak içeriğe aktarılır. Yerel geliştirme [için, ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) *appsettings.json* dosyasından bağlantı dizesini okur.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

Bir DB bağlamı oluşturdunuz ve DI kapsayıcısı ile kaydettiniz.

---

<a name="test"></a>

### <a name="test-the-app"></a>Uygulamayı test edin

* Uygulamayı çalıştırın ve `/Movies` tarayıcıdaki URL'ye`http://localhost:port/movies`ek ( ).

Aşağıdakilere benzer bir veritabanı özel durumu alırsanız:

```console
SqlException: Cannot open database "MvcMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

[Göçler adımını](#pmc)kaçırdın.

* **Oluştur** bağlantısını test edin. Veri girin ve gönderin.

  > [!NOTE]
  > `Price` Alana ondalık virgül giremeyebilirsiniz. Ondalık bir nokta için virgül (",") kullanan İngilizce olmayan yerel halkiçin jQuery doğrulaması ve ABD İngilizce olmayan tarih biçimleri için [jQuery doğrulaması](https://jqueryvalidation.org/) desteklemek için uygulamanın genelleştirilmiş olması gerekir. Küreselleşme yönergeleri için [bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.

* **Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.

`Startup` Sınıfı inceleyin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

Önceki vurgulanan [kod, Bağımlılık Enjeksiyonu](xref:fundamentals/dependency-injection) kapsayıcısına eklenen film veritabanı bağlamını gösterir:

* `services.AddDbContext<MvcMovieContext>(options =>`kullanılacak veritabanını ve bağlantı dizesini belirtir.
* `=>`bir [lambda operatörüdür](/dotnet/articles/csharp/language-reference/operators/lambda-operator)

*Denetleyiciler/MoviesController.cs* dosyasını açın ve oluşturucuyu inceleyin:

<!-- l.. Make copy of Movies controller because we comment out the initial index method and update it later  -->

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_1)]

Oluşturucu, veritabanı bağlamını denetleyiciye`MvcMovieContext`enjekte etmek için Bağımlılık [Enjeksiyonu](xref:fundamentals/dependency-injection) kullanır. Veritabanı bağlamı denetleyicideki [CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete) yöntemlerinin her birinde kullanılır.

<a name="strongly-typed-models-keyword-label"></a>
<a name="strongly-typed-models-and-the--keyword"></a>

## <a name="strongly-typed-models-and-the-model-keyword"></a>Güçlü bir şekilde yazılan @model modeller ve anahtar kelime

Bu öğreticinin başlarında, bir denetleyicinin `ViewData` sözlüğü kullanarak verileri veya nesneleri görünüme nasıl geçirebileceğini gördün. Sözlük, `ViewData` bilgileri bir görünüme aktarmak için uygun bir geç bağlı yol sağlayan dinamik bir nesnedir.

MVC ayrıca güçlü bir şekilde yazılan model nesnelerini bir görünüme geçirme olanağı da sağlar. Bu güçlü bir şekilde yazılan yaklaşım, kodunuzu daha iyi derlemesini sağlar. İskele mekanizması, yöntem ve görünümleri oluşturduğunda `MoviesController` sınıf ve görünümlerle bu yaklaşımı (yani güçlü bir şekilde yazılan bir modeli geçirerek) kullanmıştır.

`Details` *Denetleyiciler/MoviesController.cs* dosyasında oluşturulan yöntemi inceleyin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_details)]

`id` Parametre genellikle rota verisi olarak geçirilir. Örneğin `https://localhost:5001/movies/details/1` kümeler:

* Denetleyiciye `movies` denetleyici (ilk URL segmenti).
* Eylem `details` (ikinci URL segmenti).
* Kimlik 1 (son URL segmenti).

Ayrıca aşağıdaki gibi `id` bir sorgu dize ile geçirebilirsiniz:

`https://localhost:5001/movies/details?id=1`

Bir `id` kimlik değeri sağlanmadığı durumlarda`int?`parametre , () geçersiz bir [tür](/dotnet/csharp/programming-guide/nullable-types/index) olarak tanımlanır.

Yol verileri veya sorgu `FirstOrDefaultAsync` dize değeriyle eşleşen film varlıklarını seçmek için [bir lambda ifadesi](/dotnet/articles/csharp/programming-guide/statements-expressions-operators/lambda-expressions) geçirilir.

```csharp
var movie = await _context.Movie
    .FirstOrDefaultAsync(m => m.Id == id);
```

Bir film bulunursa, modelin `Movie` bir örneği `Details` görünüme aktarılır:

```csharp
return View(movie);
   ```

*Görünümler/Filmler/Ayrıntılar.cshtml* dosyasının içeriğini inceleyin:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/DetailsOriginal.cshtml)]

Görünüm dosyasının `@model` üst bölümüne bir deyim ekleyerek, görünümün beklediği nesne türünü belirtebilirsiniz. Film denetleyicisini oluşturduğunuzda, `@model` aşağıdaki ifade otomatik olarak *Details.cshtml* dosyasının en üstüne eklenmiştir:

```cshtml
@model MvcMovie.Models.Movie
```

Bu `@model` yönerge, denetleyicinin güçlü bir şekilde yazılan bir `Model` nesneyi kullanarak görünüme geçtiği filme erişmenizi sağlar. Örneğin, *Details.cshtml* görünümünde, kod her film alanını `DisplayNameFor` `DisplayFor` güçlü bir şekilde yazılan `Model` nesneye sahip HTML Yardımcıları ve HTML Yardımcıları'na geçirir. Ve yöntemler ve görünümler `Movie` de bir model nesnesi geçmek. `Edit` `Create`

Filmler denetleyicisinde *Index.cshtml* görünümünü ve `Index` yöntemi inceleyin. Kodun yöntemi aradığında `List` nesneyi nasıl `View` oluşturduğuna dikkat edin. Kod bu `Movies` listeyi `Index` eylem yönteminden görünüme geçirir:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Controllers/MC1.cs?name=snippet_index)]

Film denetleyicisini oluşturduğunuzda, iskele otomatik olarak `@model` *Index.cshtml* dosyasının üst kısmında aşağıdaki deyimi içerir:

<!-- Copy Index.cshtml to IndexOriginal.cshtml -->

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?range=1)]

Yönerge, `@model` güçlü bir şekilde yazılan bir `Model` nesneyi kullanarak denetleyicinin görünüme geçtiği film listesine erişmenizi sağlar. Örneğin, *Index.cshtml* görünümünde, kod filmler arasında güçlü `foreach` bir şekilde yazılan `Model` nesnenin üzerinde bir ifadeyle döngüler:

[!code-html[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexOriginal.cshtml?highlight=1,31,34,37,40,43,46-48)]

`Model` Nesne güçlü bir şekilde `IEnumerable<Movie>` (nesne olarak) yazıldığı için, döngüdeki her öğe `Movie`. Diğer avantajların yanı sıra, bu kodun zaman denetimini derlediğiniz anlamına gelir:

## <a name="additional-resources"></a>Ek kaynaklar

* [Etiket Yardımcıları](xref:mvc/views/tag-helpers/intro)
* [Genelleştirme ve yerelleştirme](xref:fundamentals/localization)

> [!div class="step-by-step"]
> [Önceki](adding-view.md)
> [Veritabanıyla](working-with-sql.md) Sonraki Görünüm Ekleme

::: moniker-end
