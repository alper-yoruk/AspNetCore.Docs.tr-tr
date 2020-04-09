---
title: ASP.NET Core'daki Razor Pages uygulamasına model ekleme
author: rick-anderson
description: Entity Framework Core (EF Core) kullanarak bir veritabanında film yönetimi için sınıfları nasıl ekleyeceğinizi keşfedin.
ms.author: riande
ms.date: 12/05/2019
uid: tutorials/razor-pages/model
ms.openlocfilehash: f6dbac81b4efceb30c379ab06dd715005d879228
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658937"
---
# <a name="add-a-model-to-a-razor-pages-app-in-aspnet-core"></a>ASP.NET Core'daki Razor Pages uygulamasına model ekleme

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

Bu bölümde, bir çapraz platform [SQLite veritabanında](https://www.sqlite.org/index.html)film yönetmek için sınıflar eklenir. ASP.NET Core şablonundan oluşturulan uygulamalar bir SQLite veritabanı kullanır. Uygulamanın model sınıfları, veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) [(SQLite EF Core Database Provider)](/ef/core/providers/sqlite)ile birlikte kullanılır. EF Core, veri erişimini kolaylaştıran bir nesne ilişkisieşleme (ORM) çerçevesidir.

MODEL sınıfları POCO sınıfları ("düz eski CLR nesnelerinden") olarak bilinir, çünkü EF Core'a herhangi bir bağımlılıkları yoktur. Veritabanında depolanan verilerin özelliklerini tanımlarlar.

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Veri modeli ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**RazorPagesMovie** projesine sağ tıklayın >**Yeni Klasör** **Ekle.** >  *Klasör*Modelleri'ni adlandırın.

*Modeller* klasörüne sağ tıklayın. **Sınıf** **Ekle'yi** > seçin. Sınıf **Film**adı .

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Modeller*adlı bir klasör ekleyin.
* *Movie.cs*adlı *Modeller* klasörüne bir sınıf ekleyin.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* Solution Pad'de **RazorPagesMovie** projesine sağ tıklayın ve **ardından Yeni Klasör Ekle'yi** **Add** > seçin... seçeneğini belirleyin. *Klasör*Modelleri'ni adlandırın.
* *Modeller* klasörüne sağ tıklayın ve **ardından Yeni Dosya Ekle'yi** **Add** > seçin... seçeneğini belirleyin.
* Yeni **Dosya** iletişim kutusunda:

  * Sol bölmede **Genel'i** seçin.
  * Orta bölmede **Boş Sınıf'ı** seçin.
  * Sınıf **Film** adını ve **Yeni**seçin.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Derleme hatası olmadığını doğrulamak için projeyi oluşturun.

## <a name="scaffold-the-movie-model"></a>İskele film modeli

Bu bölümde, film modeli iskele. Diğer bir zamanda, iskele aracı film modeli için Oluştur, Oku, Güncelleştir ve Sil (CRUD) işlemleri için sayfalar oluşturur.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

*Sayfalar/Filmler* klasörü oluşturun:

* **Yeni Klasör** **Ekle** > > *Sayfalar* klasörüne sağ tıklayın.
* *Klasörü Filmler'e* adlandır

*Sayfalar/Filmler* klasörüne sağ tıklayın > **Yeni İskele Öğesi** **Ekle.** >

![Önceki talimatlardan görüntü.](model/_static/sca.png)

İskele **Ekle** iletişim kutusunda, Entity Framework > **(CRUD) Ekle'yi kullanarak Jilet Sayfaları'nı** seçin. **Add**

![Önceki talimatlardan görüntü.](model/_static/add_scaffold.png)

Varlık **Çerçevesi (CRUD)** iletişim kutusunu kullanarak Jilet Sayfaları Ekle'yi tamamlayın:

* Model **sınıfında** açılan film **(RazorPagesMovie.Models) seçeneğini belirleyin.**
* Veri **bağlamı sınıf** satırında(artı) işaretini **+** seçin ve RazorPagesMovie'den oluşturulan adı değiştirin. **Modeller**. RazorPagesMovieContext için RazorPagesMovieMovie. **Veri**. RazorPagesMovieContext. [Bu değişiklik](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) gerekli değildir. Doğru ad alanı yla veritabanı bağlam sınıfını oluşturur.
* **Ekle'yi**seçin.

![Önceki talimatlardan görüntü.](model/_static/3/arp.png)

*Appsettings.json* dosyası, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesi ile güncelleştirilir.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Proje dizininde bir komut penceresi açın *(Program.cs,* *Startup.cs*ve *.csproj* dosyalarını içeren dizin).
* İskele aracını yükleyin:

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* **Windows için**: Aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **macOS ve Linux için**: Aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

*Sayfalar/Filmler* klasörü oluşturun:

* **Yeni Klasör** **Ekle** > > *Sayfalar* klasörüne sağ tıklayın.
* *Klasörü Filmler'e* adlandır

*Sayfalar/Filmler* klasörüne sağ tıklayın > Yeni İskele **Ekle...** > **New Scaffolding...**.

![Önceki talimatlardan görüntü.](model/_static/scaMac.png)

Yeni **İskele** iletişim kutusunda, Entity Framework > **(CRUD) Next'i kullanarak Razor Pages'ı** seçin. **Next**

![Önceki talimatlardan görüntü.](model/_static/add_scaffoldMac.png)

Varlık **Çerçevesi (CRUD)** iletişim kutusunu kullanarak Jilet Sayfaları Ekle'yi tamamlayın:

* Model **sınıfında** film **(RazorPagesMovie.Models)** aşağı, seçin veya yazın.
* Veri **bağlamında sınıf** satırında, yeni sınıfın adını yazın, RazorPagesMovie. **Veri**. RazorPagesMovieContext. [Bu değişiklik](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) gerekli değildir. Doğru ad alanı yla veritabanı bağlam sınıfını oluşturur.
* **Ekle'yi**seçin.

![Önceki talimatlardan görüntü.](model/_static/arpMac.png)

*Appsettings.json* dosyası, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesi ile güncelleştirilir.

### <a name="add-ef-tools"></a>EF araçları ekleme

Aşağıdaki .NET Core CLI komutunu çalıştırın:

```dotnetcli
dotnet tool install --global dotnet-ef
```

Önceki komut .NET Core CLI için Entity Framework Core Tools ekler.

---

### <a name="files-created"></a>Oluşturulan dosyalar

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

İskele işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:

* *Sayfalar/Filmler*: Oluştur, Sil, Ayrıntılar, Düzenle ve Dizini.
* *Veri/RazorPagesMovieContext.cs*

### <a name="updated"></a>Güncelleştirildi

* *Startup.cs*

Oluşturulan ve güncelleştirilen dosyalar sonraki bölümde açıklanır.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

İskele işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:

* *Sayfalar/Filmler*: Oluştur, Sil, Ayrıntılar, Düzenle ve Dizini.
* *Veri/RazorPagesMovieContext.cs*

### <a name="updated"></a>Güncelleştirildi

* *Startup.cs*

Oluşturulan ve güncelleştirilen dosyalar sonraki bölümde açıklanır.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

İskele işlemi aşağıdaki dosyaları oluşturur:

* *Sayfalar/Filmler*: Oluştur, Sil, Ayrıntılar, Düzenle ve Dizini.

Oluşturulan dosyalar sonraki bölümde açıklanır.

---

<a name="pmc"></a>

## <a name="initial-migration"></a>İlk geçiş

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Bu bölümde, Paket Yöneticisi Konsolu (PMC) aşağıdakileri yapmak için kullanılır:

* İlk geçiş ekleyin.
* Veritabanını ilk geçişle güncelleştirin.

**Araçlar** menüsünden **NuGet Paket Yöneticisi** > **Paket Yöneticisi Konsolu'nu**seçin.

  ![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

PMC'de aşağıdaki komutları girin:

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

Önceki komutlar şu uyarıyı oluşturur: "Ondalık sütun 'Fiyat' için varlık türü 'Film' üzerinde hiçbir tür belirtilmedi. Bu, varsayılan kesinlik ve skalaya sığmazsa değerlerin sessizce kesildirilmesine neden olur. 'HasColumnType()' kullanarak tüm değerleri barındırabilecek SQL sunucu sütun türünü açıkça belirtin."

Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilir.

Geçişler komutu, ilk veritabanı şemasını oluşturmak için kod oluşturur. Şema, 'de `DbContext`belirtilen modele dayanır. Bağımsız `InitialCreate` değişken, geçişleri adlandırmak için kullanılır. Herhangi bir ad kullanılabilir, ancak kural olarak geçişi açıklayan bir ad seçilir.

Komut, `update` uygulanmamış geçişlerde `Up` yöntemi çalıştırAr. Bu durumda, `update` veritabanını `Up` oluşturan *Geçişler/\<zaman damgası>_InitialCreate.cs* dosyasında yöntem çalışır.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Bağımlılık enjeksiyonu ile kayıtlı bağlamı inceleyin

ASP.NET Core [bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection)ile inşa edilmiştir. Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık enjeksiyonu ile kaydedilir. Bu hizmetleri gerektiren bileşenlere (Jilet Sayfaları gibi) bu hizmetler yapıcı parametreler aracılığıyla sağlanır. DB bağlam örneğini alan kurucu kodu daha sonra öğreticide gösterilir.

İskele aracı otomatik olarak bir DB bağlamı oluşturdu ve bağımlılık enjeksiyon konteyneri ile kaydetti.

`Startup.ConfigureServices` Yöntemi inceleyin. Vurgulanan satır iskele tarafından eklendi:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

Model için EF Core işlevselliğini (Oluştur, Oku, Güncelle, Sil, vb.) `RazorPagesMovieContext` koordinatları. `Movie` Veri bağlamı`RazorPagesMovieContext`( ) [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)türetilmiştir. Veri bağlamı, veri modeline hangi varlıkların dahil edildiğini belirtir.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Önceki kod, varlık kümesi için bir [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur. Varlık Çerçevesi terminolojisinde, bir varlık kümesi genellikle bir veritabanı tablosuna karşılık gelir. Bir varlık tablodaki bir satıra karşılık gelir.

Bağlantı dizesinin adı, [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesindeki bir yöntem çağırılarak içeriğe aktarılır. Yerel geliştirme [için, ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) *appsettings.json* dosyasından bağlantı dizesini okur.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

`Up` Yöntemi inceleyin.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

`Up` Yöntemi inceleyin.

---

<a name="test"></a>

### <a name="test-the-app"></a>Uygulamayı test edin

* Uygulamayı çalıştırın ve `/Movies` tarayıcıdaki URL'ye`http://localhost:port/movies`ek ( ).

Hata alırsanız:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

[Göçler adımını](#pmc)kaçırdın.

* **Oluştur** bağlantısını test edin.

  ![Sayfa oluşturma](model/_static/conan.png)

  > [!NOTE]
  > `Price` Alana ondalık virgül giremeyebilirsiniz. Ondalık bir nokta için virgül (",") kullanan İngilizce olmayan yerel halkiçin jQuery doğrulaması ve ABD İngilizce olmayan tarih biçimleri için [jQuery doğrulaması](https://jqueryvalidation.org/) desteklemek için uygulamanın genelleştirilmiş olması gerekir. Küreselleşme yönergeleri için [bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.

* **Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.

Sonraki öğretici iskele tarafından oluşturulan dosyaları açıklar.

## <a name="additional-resources"></a>Ek kaynaklar

> [!div class="step-by-step"]
> [Önceki:](xref:tutorials/razor-pages/razor-pages-start)
> [Sonraki Başlayın: İskeleLi Jilet Sayfaları](xref:tutorials/razor-pages/page)

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

Bu bölümde, bir çapraz platform [SQLite veritabanında](https://www.sqlite.org/index.html)film yönetmek için sınıflar eklenir. ASP.NET Core şablonundan oluşturulan uygulamalar bir SQLite veritabanı kullanır. Uygulamanın model sınıfları, veritabanıyla çalışmak için [Entity Framework Core (EF Core)](/ef/core) [(SQLite EF Core Database Provider)](/ef/core/providers/sqlite)ile birlikte kullanılır. EF Core, veri erişimini kolaylaştıran bir nesne ilişkisieşleme (ORM) çerçevesidir.

MODEL sınıfları POCO sınıfları ("düz eski CLR nesnelerinden") olarak bilinir, çünkü EF Core'a herhangi bir bağımlılıkları yoktur. Veritabanında depolanan verilerin özelliklerini tanımlarlar.

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a>Veri modeli ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

**RazorPagesMovie** projesine sağ tıklayın >**Yeni Klasör** **Ekle.** >  *Klasör*Modelleri'ni adlandırın.

*Modeller* klasörüne sağ tıklayın. **Sınıf** **Ekle'yi** > seçin. Sınıf **Film**adı .

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* *Modeller*adlı bir klasör ekleyin.
* *Movie.cs*adlı *Modeller* klasörüne bir sınıf ekleyin.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

* Solution Explorer'da **RazorPagesMovie** projesini sağ tıklatın ve ardından**Yeni Klasör** **Ekle'yi** > seçin. *Klasör*Modelleri'ni adlandırın.
* *Modeller* klasörüne sağ tıklayın ve ardından **Yeni Dosya** **Ekle'yi** > seçin.
* Yeni **Dosya** iletişim kutusunda:

  * Sol bölmede **Genel'i** seçin.
  * Orta bölmede **Boş Sınıf'ı** seçin.
  * Sınıf **Film** adını ve **Yeni**seçin.

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

Derleme hatası olmadığını doğrulamak için projeyi oluşturun.

## <a name="scaffold-the-movie-model"></a>İskele film modeli

Bu bölümde, film modeli iskele. Diğer bir zamanda, iskele aracı film modeli için Oluştur, Oku, Güncelleştir ve Sil (CRUD) işlemleri için sayfalar oluşturur.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

*Sayfalar/Filmler* klasörü oluşturun:

* **Yeni Klasör** **Ekle** > > *Sayfalar* klasörüne sağ tıklayın.
* *Klasörü Filmler'e* adlandır

*Sayfalar/Filmler* klasörüne sağ tıklayın > **Yeni İskele Öğesi** **Ekle.** >

![Önceki talimatlardan görüntü.](model/_static/sca.png)

İskele **Ekle** iletişim kutusunda, Entity Framework > **(CRUD) Ekle'yi kullanarak Jilet Sayfaları'nı** seçin. **Add**

![Önceki talimatlardan görüntü.](model/_static/add_scaffold.png)

Varlık **Çerçevesi (CRUD)** iletişim kutusunu kullanarak Jilet Sayfaları Ekle'yi tamamlayın:
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* Model **sınıfında** açılan film **(RazorPagesMovie.Models) seçeneğini belirleyin.**
* Veri **bağlamında sınıf** satırında(artı) işaretini **+** seçin ve oluşturulan **razorPagesMovie.Models.RazorPagesMovieContext**adını kabul edin.
* **Ekle'yi**seçin.

![Önceki talimatlardan görüntü.](model/_static/arp.png)

*Appsettings.json* dosyası, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesi ile güncelleştirilir.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* Proje dizininde bir komut penceresi açın *(Program.cs,* *Startup.cs*ve *.csproj* dosyalarını içeren dizin).

* **Windows için**: Aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* **macOS ve Linux için**: Aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

*Sayfalar/Filmler* klasörü oluşturun:

* **Yeni Klasör** **Ekle** > > *Sayfalar* klasörüne sağ tıklayın.
* *Klasörü Filmler'e* adlandır

*Sayfalar/Filmler* klasörüne sağ tıklayın > **Yeni İskele Öğesi** **Ekle.** >

![Önceki talimatlardan görüntü.](model/_static/scaMac.png)

Yeni **İskele Ekle** iletişim kutusunda, **Entity Framework (CRUD)** > **Ekle'yi**kullanarak Jilet Sayfaları'nı seçin.

![Önceki talimatlardan görüntü.](model/_static/add_scaffoldMac.png)

Varlık **Çerçevesi (CRUD)** iletişim kutusunu kullanarak Jilet Sayfaları Ekle'yi tamamlayın:

* Model **sınıfında** aşağı açılan, **Film**seçin veya yazın.
* Veri **bağlamı sınıf** satırında, **RazorPagesMovieContext'ı** yazın, bu da doğru ad alanına sahip yeni bir db bağlam sınıfı oluşturur. Bu durumda **RazorPagesMovie.Models.RazorPagesMovieContext**olacak .
* **Ekle'yi**seçin.

![Önceki talimatlardan görüntü.](model/_static/arpMac.png)

*Appsettings.json* dosyası, yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesi ile güncelleştirilir.

---

İskele işlemi aşağıdaki dosyaları oluşturur ve güncelleştirir:

### <a name="files-created"></a>Oluşturulan dosyalar

* *Sayfalar/Filmler*: Oluştur, Sil, Ayrıntılar, Düzenle ve Dizini.
* *Veri/RazorPagesMovieContext.cs*

### <a name="file-updated"></a>Dosya güncellendi

* *Startup.cs*

Oluşturulan ve güncelleştirilen dosyalar sonraki bölümde açıklanır.

<a name="pmc"></a>

## <a name="initial-migration"></a>İlk geçiş

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Bu bölümde, Paket Yöneticisi Konsolu (PMC) aşağıdakileri yapmak için kullanılır:

* İlk geçiş ekleyin.
* Veritabanını ilk geçişle güncelleştirin.

**Araçlar** menüsünden **NuGet Paket Yöneticisi** > **Paket Yöneticisi Konsolu'nu**seçin.

  ![PMC menüsü](../first-mvc-app/adding-model/_static/pmc.png)

PMC'de aşağıdaki komutları girin:

```powershell
Add-Migration Initial
Update-Database
```

Komut, `Add-Migration` ilk veritabanı şemasını oluşturmak için kod oluşturur. Şema,(RazorPagesMovieContext.cs dosyasında) belirtilen modele dayanır. *RazorPagesMovieContext.cs* `DbContext` Bağımsız `InitialCreate` değişken, geçişin adını vermek için kullanılır. Herhangi bir ad kullanılabilir, ancak kuralıile geçiş açıklayan bir ad kullanılır. Daha fazla bilgi için bkz. <xref:data/ef-mvc/migrations>.

Komut, `Update-Database` `Up` *Geçişler/\<zaman damgası>_InitialCreate.cs* dosyasında yöntemi çalıştırAr. Yöntem `Up` veritabanını oluşturur.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> Önceki komutlar şu uyarıyı oluşturur: "*Ondalık sütun 'Fiyat' için varlık türü 'Film' üzerinde hiçbir tür belirtilmedi. Bu, varsayılan kesinlik ve skalaya sığmazsa değerlerin sessizce kesildirilmesine neden olur. 'HasColumnType()' kullanarak tüm değerleri barındırabilecek SQL sunucu sütun türünü açıkça belirtin.* Bu uyarıyı yoksayabilirsiniz, daha sonraki bir öğreticide düzeltilir.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a>Bağımlılık enjeksiyonu ile kayıtlı bağlamı inceleyin

ASP.NET Core [bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection)ile inşa edilmiştir. Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık enjeksiyonu ile kaydedilir. Bu hizmetleri gerektiren bileşenlere (Jilet Sayfaları gibi) bu hizmetler yapıcı parametreler aracılığıyla sağlanır. DB bağlam örneğini alan kurucu kodu daha sonra öğreticide gösterilir.

İskele aracı otomatik olarak bir DB bağlamı oluşturdu ve bağımlılık enjeksiyon konteyneri ile kaydetti.

`Startup.ConfigureServices` Yöntemi inceleyin. Vurgulanan satır iskele tarafından eklendi:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

Model için EF Core işlevselliğini (Oluştur, Oku, Güncelle, Sil, vb.) `RazorPagesMovieContext` koordinatları. `Movie` Veri bağlamı`RazorPagesMovieContext`( ) [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)türetilmiştir. Veri bağlamı, veri modeline hangi varlıkların dahil edildiğini belirtir.

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

Önceki kod, varlık kümesi için bir [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur. Varlık Çerçevesi terminolojisinde, bir varlık kümesi genellikle bir veritabanı tablosuna karşılık gelir. Bir varlık tablodaki bir satıra karşılık gelir.

Bağlantı dizesinin adı, [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesindeki bir yöntem çağırılarak içeriğe aktarılır. Yerel geliştirme [için, ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) *appsettings.json* dosyasından bağlantı dizesini okur.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

`Up` Yöntemi inceleyin.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

`Up` Yöntemi inceleyin.

---

<a name="test"></a>

### <a name="test-the-app"></a>Uygulamayı test edin

* Uygulamayı çalıştırın ve `/Movies` tarayıcıdaki URL'ye`http://localhost:port/movies`ek ( ).

Hata alırsanız:

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

[Göçler adımını](#pmc)kaçırdın.

* **Oluştur** bağlantısını test edin.

  ![Sayfa oluşturma](model/_static/conan.png)

  > [!NOTE]
  > `Price` Alana ondalık virgül giremeyebilirsiniz. Ondalık bir nokta için virgül (",") kullanan İngilizce olmayan yerel halkiçin jQuery doğrulaması ve ABD İngilizce olmayan tarih biçimleri için [jQuery doğrulaması](https://jqueryvalidation.org/) desteklemek için uygulamanın genelleştirilmiş olması gerekir. Küreselleşme yönergeleri için [bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)bakın.

* **Düzenle**, **Ayrıntılar** ve **Sil** bağlantılarını test edin.

Sonraki öğretici iskele tarafından oluşturulan dosyaları açıklar.

## <a name="additional-resources"></a>Ek kaynaklar

> [!div class="step-by-step"]
> [Önceki:](xref:tutorials/razor-pages/razor-pages-start)
> [Sonraki Başlayın: İskeleLi Jilet Sayfaları](xref:tutorials/razor-pages/page)

::: moniker-end
