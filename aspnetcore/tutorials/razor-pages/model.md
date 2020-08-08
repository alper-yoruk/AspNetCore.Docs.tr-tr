---
title: Bölüm 2, Razor ASP.NET Core bir sayfalar uygulamasına bir model ekleyin
author: rick-anderson
description: Sayfalardaki eğitim serisinin 2. bölümü Razor .
ms.author: riande
ms.date: 12/05/2019
no-loc:
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
ms.openlocfilehash: 6b50f46863a6dabb01bcf0976a42abb504e6f7b7
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020463"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a>Bölüm 2, Razor ASP.NET Core bir sayfalar uygulamasına bir model ekleyin

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

Bu bölümde, film yönetimi için sınıflar eklenir. Uygulamanın model sınıfları veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) kullanır. EF Core, veri erişimini kolaylaştıran bir nesne ilişkisel Eşleyici (O/RM).

Model sınıfları, EF Core hiçbir bağımlılığı olmadığından, POCO sınıfları ("düz eski CLR nesnelerinden") olarak bilinir. Veritabanında depolanan verilerin özelliklerini tanımlar.

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Veri modeli ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Yeni klasör **eklemek**> ** Razor pagesmovie** projesine sağ tıklayın  >  **New Folder**. Klasör *modellerini*adlandırın.

*Modeller* klasörüne sağ tıklayın. Sınıf **Ekle**' yi seçin  >  **Class**. Sınıf **filmi**olarak adlandırın.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Modeller*adlı bir klasör ekleyin.
* *Movie.cs*adlı *modeller* klasörüne bir sınıf ekleyin.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* Çözüm bölmesi, ** Razor pagesmovie** projesine sağ tıklayın ve ardından **Add** > **Yeni klasör ekle...** seçeneğini belirleyin. Klasör *modellerini*adlandırın.
* *Modeller* klasörüne sağ tıklayın ve ardından **Add** > **yeni dosya Ekle...** seçeneğini belirleyin.
* **Yeni dosya** iletişim kutusunda:

  * Sol bölmedeki **genel** ' i seçin.
  * Orta bölmede **boş sınıf** ' ı seçin.
  * Sınıfı **filmi** adlandırın ve **Yeni**' yi seçin.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Derleme hatası olmadığını doğrulamak için projeyi derleyin.

## <a name="scaffold-the-movie-model"></a>Film modelini dolandırın

Bu bölümde, film modeli scafkatdır. Diğer bir deyişle, scafkatlama aracı film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

*Sayfalar/filmler* klasörü oluştur:

* Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.
* Klasör *filmlerini* adlandırın

*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi**eklemek > sayfalar/filmler klasörüne sağ tıklayın.

![Önceki yönergelerden görüntü.](model/_static/sca.png)

**Yapı iskelesi Ekle** iletişim kutusunda ** Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > **Add**.

![Önceki yönergelerden görüntü.](model/_static/add_scaffold.png)

** Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:

* **Model sınıfı** açılan kutusunda **Film ( Razor pagesmovie. modeller)** öğesini seçin.
* **Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin ve oluşturulan adı Razor pagesmovie 'den değiştirin.** Modeller**. Razor Pagesmovie 'e PagesMovieContext Razor .** Veri**. Razor PagesMovieContext. [Bu değişiklik](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) gerekli değildir. Doğru ad alanıyla veritabanı bağlamı sınıfını oluşturur.
* **Add (Ekle)** seçeneğini belirleyin.

![Önceki yönergelerden görüntü.](model/_static/3/arp.png)

Dosyadaki *appsettings.js* , yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs*ve *. csproj* dosyalarını içeren dizin).
* Scafkatlama aracını yükler:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* **Windows için**: aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **MacOS ve Linux için**: aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

*Sayfalar/filmler* klasörü oluştur:

* Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.
* Klasör *filmlerini* adlandırın

Yeni yapı iskelesi **eklemek** > *Sayfalar/filmler* klasörüne sağ tıklayın > **...**.

![Önceki yönergelerden görüntü.](model/_static/scaMac.png)

**Yeni yapı iskelesi** iletişim kutusunda, İleri ** Razor Entity Framework (CRUD) öğesini kullanarak sayfalar** ' ı seçin > **Next**.

![Önceki yönergelerden görüntü.](model/_static/add_scaffoldMac.png)

** Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:

* **Model sınıfı** açılan kutusunda, seçin veya yazın, **Film ( Razor pagesmovie. modeller)**.
* **Veri bağlamı sınıfı** satırına yeni sınıfın adını yazın, Razor pagesfilmi.** Veri**. Razor PagesMovieContext. [Bu değişiklik](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) gerekli değildir. Doğru ad alanıyla veritabanı bağlamı sınıfını oluşturur.
* **Add (Ekle)** seçeneğini belirleyin.

![Önceki yönergelerden görüntü.](model/_static/arpMac.png)

Dosyadaki *appsettings.js* , yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.

### <a name="add-ef-tools"></a>EF araçları ekleme

Aşağıdaki .NET Core CLI komutunu çalıştırın:

```dotnetcli
dotnet tool install --global dotnet-ef
```

Yukarıdaki komut, .NET Core CLI için Entity Framework Core araçları ekler.

---

### <a name="files-created"></a>Oluşturulan dosyalar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:

* *Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve dizin.
* *Veri/ Razor PagesMovieContext.cs*

### <a name="updated"></a>Güncelleştirildi

* *Startup.cs*

Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:

* *Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve dizin.
* *Veri/ Razor PagesMovieContext.cs*

### <a name="updated"></a>Güncelleştirildi

* *Startup.cs*

Oluşturulan ve güncelleştirilmiş dosyalar sonraki bölümde açıklanmaktadır.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur:

* *Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve dizin.

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

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

Yukarıdaki komutlar şu uyarıyı oluşturur: "' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi. Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur. ' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açık olarak belirtin. "

Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilecektir.

Geçişler komutu, ilk veritabanı şemasını oluşturmak için kod üretir. Şema, içinde belirtilen modeli temel alır `DbContext` . `InitialCreate`Bağımsız değişken, geçişleri adlandırmak için kullanılır. Herhangi bir ad kullanılabilir, ancak geçiş işlemini açıklayan bir ad seçilir.

`update`Komutu `Up` uygulanmamış geçişlerde yöntemini çalıştırır. Bu durumda, `update` `Up` veritabanını oluşturan *geçişler/ \<time-stamp> _InitialCreate. cs* dosyasında yöntemini çalıştırır.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Bağımlılık ekleme ile kaydedilen bağlamı inceleyin

ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur. Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir. Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır. Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.

Scafkatlama aracı otomatik olarak bir DB bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.

Yöntemini inceleyin `Startup.ConfigureServices` . Vurgulanan satır, scaffolder tarafından eklendi:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

`RazorPagesMovieContext`Koordinatlar, model için işlevleri EF Core (oluşturma, okuma, güncelleştirme, silme, vb.) `Movie` . Veri bağlamı ( `RazorPagesMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir. Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur. Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir. Bir varlık, tablodaki bir satıra karşılık gelir.

Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir. Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki *appsettings.js* bağlantı dizesini okur.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Yöntemini inceleyin `Up` .

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

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
  > Alana ondalık virgüller giremeyebilirsiniz `Price` . Ondalık bir nokta ve ABD Ingilizcesi olmayan tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir. Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.

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

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Veri modeli ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Yeni klasör **eklemek**> ** Razor pagesmovie** projesine sağ tıklayın  >  **New Folder**. Klasör *modellerini*adlandırın.

*Modeller* klasörüne sağ tıklayın. Sınıf **Ekle**' yi seçin  >  **Class**. Sınıf **filmi**olarak adlandırın.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Modeller*adlı bir klasör ekleyin.
* *Movie.cs*adlı *modeller* klasörüne bir sınıf ekleyin.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* Çözüm Gezgini, ** Razor pagesmovie** projesine sağ tıklayın ve ardından **Add**  >  **Yeni klasör**Ekle ' yi seçin. Klasör *modellerini*adlandırın.
* *Modeller* klasörüne sağ tıklayın ve ardından **Add** > **yeni dosya**Ekle ' yi seçin.
* **Yeni dosya** iletişim kutusunda:

  * Sol bölmedeki **genel** ' i seçin.
  * Orta bölmede **boş sınıf** ' ı seçin.
  * Sınıfı **filmi** adlandırın ve **Yeni**' yi seçin.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Derleme hatası olmadığını doğrulamak için projeyi derleyin.

## <a name="scaffold-the-movie-model"></a>Film modelini dolandırın

Bu bölümde, film modeli scafkatdır. Diğer bir deyişle, scafkatlama aracı film modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

*Sayfalar/filmler* klasörü oluştur:

* Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.
* Klasör *filmlerini* adlandırın

*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi**eklemek > sayfalar/filmler klasörüne sağ tıklayın.

![Önceki yönergelerden görüntü.](model/_static/sca.png)

**Yapı iskelesi Ekle** iletişim kutusunda ** Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > **Add**.

![Önceki yönergelerden görüntü.](model/_static/add_scaffold.png)

** Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* **Model sınıfı** açılan kutusunda **Film ( Razor pagesmovie. modeller)** öğesini seçin.
* **Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin ve oluşturulan ** Razor pagesmovie. modeller adlı adı kabul edin. Razor PagesMovieContext**.
* **Add (Ekle)** seçeneğini belirleyin.

![Önceki yönergelerden görüntü.](model/_static/arp.png)

Dosyadaki *appsettings.js* , yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Proje dizininde bir komut penceresi açın ( *program.cs*, *Startup.cs*ve *. csproj* dosyalarını içeren dizin).

* **Windows için**: aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **MacOS ve Linux için**: aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

*Sayfalar/filmler* klasörü oluştur:

* Yeni klasör **eklemek** > *Sayfalar* klasörüne sağ tıklayın > **New Folder**.
* Klasör *filmlerini* adlandırın

*Pages/Movies* **Add** > **Yeni yapı iskelesi öğesi**eklemek > sayfalar/filmler klasörüne sağ tıklayın.

![Önceki yönergelerden görüntü.](model/_static/scaMac.png)

**Yeni yapı iskelesi Ekle** iletişim kutusunda ** Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > **Add**.

![Önceki yönergelerden görüntü.](model/_static/add_scaffoldMac.png)

** Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:

* **Model sınıfı** açılan kutusunda **film**' ı seçin veya yazın.
* **Veri bağlamı sınıfı** satırında, ** Razor pagesmoviecontext** ' i seçin. Bu, doğru ad alanı ile yeni bir DB bağlam sınıfı oluşturacaktır. Bu durumda, ** Razor pagesmovie. modeller olacaktır. Razor PagesMovieContext**.
* **Add (Ekle)** seçeneğini belirleyin.

![Önceki yönergelerden görüntü.](model/_static/arpMac.png)

Dosyadaki *appsettings.js* , yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesiyle güncelleştirilir.

---

Yapı iskelesi işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:

### <a name="files-created"></a>Oluşturulan dosyalar

* *Sayfalar/filmler*: oluşturma, silme, ayrıntılar, düzenleme ve dizin.
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

`Add-Migration`Komut, ilk veritabanı şemasını oluşturmak için kod üretir. Şema, öğesinde belirtilen modeli temel alır `DbContext` ( * Razor PagesMovieContext.cs* dosyasında). `InitialCreate`Bağımsız değişkeni, geçişi adlandırmak için kullanılır. Herhangi bir ad kullanılabilir, ancak kurala göre geçiş işlemini açıklayan bir ad kullanılır. Daha fazla bilgi için bkz. <xref:data/ef-mvc/migrations>.

`Update-Database`Komutu, `Up` *geçişleri/ \<time-stamp> _InitialCreate. cs* dosyasında yöntemini çalıştırır. `Up`Yöntemi veritabanını oluşturur.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> Yukarıdaki komutlar şu uyarıyı oluşturur: "*' Movie ' varlık türündeki ' Price ' ondalık sütunu için tür belirtilmedi. Bu, varsayılan duyarlık ve ölçeğe uygun olmadıkları takdirde değerlerin sessizce kesilmesine neden olur. ' Hasccolumntype () ' kullanarak tüm değerleri barındırabilecek SQL Server sütun türünü açık olarak belirtin.*" Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilecektir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Bağımlılık ekleme ile kaydedilen bağlamı inceleyin

ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur. Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir. Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır. Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.

Scafkatlama aracı otomatik olarak bir DB bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.

Yöntemini inceleyin `Startup.ConfigureServices` . Vurgulanan satır, scaffolder tarafından eklendi:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

`RazorPagesMovieContext`Koordinatlar, model için işlevleri EF Core (oluşturma, okuma, güncelleştirme, silme, vb.) `Movie` . Veri bağlamı ( `RazorPagesMovieContext` ) [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir. Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Önceki kod, varlık kümesi için bir [Dbset \<Movie> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur. Entity Framework terminolojisinde, genellikle bir varlık kümesi bir veritabanı tablosuna karşılık gelir. Bir varlık, tablodaki bir satıra karşılık gelir.

Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir. Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki *appsettings.js* bağlantı dizesini okur.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Yöntemini inceleyin `Up` .

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

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
  > Alana ondalık virgüller giremeyebilirsiniz `Price` . Ondalık bir nokta ve ABD Ingilizcesi olmayan tarih biçimleri için virgül (",") kullanan Ingilizce olmayan yerel ayarlarda [jQuery doğrulamasını](https://jqueryvalidation.org/) desteklemek için, uygulamanın Genelleştirilmiş olması gerekir. Genelleştirme yönergeleri için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.

* **Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.

Sonraki öğreticide, yapı iskelesi tarafından oluşturulan dosyalar açıklanmaktadır.

## <a name="additional-resources"></a>Ek kaynaklar

> [!div class="step-by-step"]
> [Önceki: Başlarken](xref:tutorials/razor-pages/razor-pages-start) 
>  [Sonraki: yapı Iskelesi Razor Sayfalar](xref:tutorials/razor-pages/page)

::: moniker-end
