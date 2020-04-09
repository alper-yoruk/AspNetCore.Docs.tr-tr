---
title: ASP.NET Core MVC uygulamasında SQL ile çalışma
author: rick-anderson
description: ASP.NET Core MVC uygulamasında SQL Server LocalDB veya SQLite'yi kullanma hakkında bilgi edinin.
ms.author: riande
ms.date: 8/16/2019
uid: tutorials/first-mvc-app/working-with-sql
ms.openlocfilehash: d556f07111fb2022a1c2f1a066459566e302835d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665041"
---
# <a name="work-with-sql-in-aspnet-core"></a>ASP.NET Core'da SQL ile çalışma

::: moniker range=">= aspnetcore-3.0"

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Nesne, `MvcMovieContext` veritabanına bağlanma ve nesneleri veritabanı `Movie` kayıtlarına eşleme görevini işler. Veritabanı *bağlamı, Startup.cs* dosyasındaki `ConfigureServices` yöntemde [Bağımlılık Enjeksiyonu](xref:fundamentals/dependency-injection) kapsayıcısı ile kaydedilir:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

ASP.NET Core [Configuration](xref:fundamentals/configuration/index) sistemi `ConnectionString`okur. Yerel geliştirme için, *appsettings.json* dosyasından bağlantı dizesini alır:

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

ASP.NET Core [Configuration](xref:fundamentals/configuration/index) sistemi `ConnectionString`okur. Yerel geliştirme için, *appsettings.json* dosyasından bağlantı dizesini alır:

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

Uygulama bir test veya üretim sunucusuna dağıtıldığında, bağlantı dizesini üretim SQL Server'a ayarlamak için bir ortam değişkeni kullanılabilir. Daha fazla bilgi için [Yapılandırma'ya](xref:fundamentals/configuration/index) bakın.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB, SQL Server Express Veritabanı Altyapısının program geliştirme yi hedefleyen hafif bir sürümüdür. LocalDB isteğe bağlı olarak başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur. Varsayılan olarak, LocalDB veritabanı *C:/Users/{user}* dizininde *.mdf* dosyaları oluşturur.

* **Görünüm** menüsünden **SQL Server Object Explorer'ı** (SSOX) açın.

  ![Görünüm menüsü](working-with-sql/_static/ssox.png)

* **Tasarımcıyı**> `Movie` tabloya sağ tıklayın

  ![Film masasında bağlamsal menü açılır](working-with-sql/_static/design.png)

  ![Designer'da film masası açıldı](working-with-sql/_static/dv.png)

Yanındaki anahtar simgesine `ID`dikkat edin. Varsayılan olarak, EF birincil `ID` anahtar adlı bir özellik yapar.

* Verileri Görüntüleme `Movie` **>** tabloya sağ tıklayın

  ![Film masasında bağlamsal menü açılır](working-with-sql/_static/ssox2.png)

  ![Tablo verilerini gösteren film tablosu açık](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a>Veritabanını tohumla

Modeller klasöründe `SeedData` yeni *Models* bir sınıf oluşturun. Oluşturulan kodu aşağıdakilerle değiştirin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Models/SeedData.cs?name=snippet_1)]

DB'de film varsa, tohum başdöndürür ve film eklenmez.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Tohum başharfekle

*Program.cs* içeriğini aşağıdaki kodla değiştirin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie3/Program.cs)]

Uygulamayı test edin

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* DB'deki tüm kayıtları silin. Bunu tarayıcıdaki silme bağlantılarıyla veya SSOX'tan yapabilirsiniz.
* Tohum yönteminin çalıştırabilmesi için uygulamayı `Startup` başlatmaya (sınıftaki yöntemleri çağırın) zorlar. Başlatmayı zorlamak için IIS Express durdurulmalı ve yeniden başlatılmalıdır. Bunu aşağıdaki yaklaşımlardan herhangi biriyle yapabilirsiniz:

  * Bildirim alanındaki IIS Express sistem tepsisi simgesine sağ tıklayın ve **Siteden Çık** veya **Durdur'a** dokunun

    ![IIS Express sistem tepsisi simgesi](working-with-sql/_static/iisExIcon.png)

    ![Bağlamsal menü](working-with-sql/_static/stopIIS.png)

    * Hata ayıklama modunda VS çalıştırıyorsanız, hata ayıklama modunda çalıştırmak için F5 tuşuna basın
    * Hata ayıklama modunda VS çalıştırıyorsanız, hata ayıklamayı durdurun ve F5 tuşuna basın

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

DB'deki tüm kayıtları silin (Böylece tohum yöntemi çalışacaktır). Veritabanını tohumlamak için uygulamayı durdurun ve başlatın.

---

Uygulama, tohumlu verileri gösterir.

![Microsoft Edge'de film verilerini gösteren MVC Film uygulaması açıldı](working-with-sql/_static/m55.png)

> [!div class="step-by-step"]
> [Önceki](adding-model.md)
> [Sonraki](controller-methods-views.md)
::: moniker-end

::: moniker range="< aspnetcore-3.0"

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Nesne, `MvcMovieContext` veritabanına bağlanma ve nesneleri veritabanı `Movie` kayıtlarına eşleme görevini işler. Veritabanı *bağlamı, Startup.cs* dosyasındaki `ConfigureServices` yöntemde [Bağımlılık Enjeksiyonu](xref:fundamentals/dependency-injection) kapsayıcısı ile kaydedilir:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=13-99)]

ASP.NET Core [Configuration](xref:fundamentals/configuration/index) sistemi `ConnectionString`okur. Yerel geliştirme için, *appsettings.json* dosyasından bağlantı dizesini alır:

[!code-json[](start-mvc/sample/MvcMovie/appsettings.json?highlight=2&range=8-10)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

ASP.NET Core [Configuration](xref:fundamentals/configuration/index) sistemi `ConnectionString`okur. Yerel geliştirme için, *appsettings.json* dosyasından bağlantı dizesini alır:

[!code-json[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/appsettingsSQLite.json?highlight=2&range=8-10)]

---

Uygulamayı bir test veya üretim sunucusuna dağıttığınızda, bağlantı dizesini gerçek bir SQL Server'a ayarlamak için bir ortam değişkeni veya başka bir yaklaşım kullanabilirsiniz. Daha fazla bilgi için [Yapılandırma'ya](xref:fundamentals/configuration/index) bakın.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB, SQL Server Express Veritabanı Altyapısının program geliştirme yi hedefleyen hafif bir sürümüdür. LocalDB isteğe bağlı olarak başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur. Varsayılan olarak, LocalDB veritabanı *C:/Users/{user}* dizininde *.mdf* dosyaları oluşturur.

* **Görünüm** menüsünden **SQL Server Object Explorer'ı** (SSOX) açın.

  ![Görünüm menüsü](working-with-sql/_static/ssox.png)

* **Tasarımcıyı**> `Movie` tabloya sağ tıklayın

  ![Film masasında bağlamsal menü açılır](working-with-sql/_static/design.png)

  ![Designer'da film masası açıldı](working-with-sql/_static/dv.png)

Yanındaki anahtar simgesine `ID`dikkat edin. Varsayılan olarak, EF birincil `ID` anahtar adlı bir özellik yapar.

* Verileri Görüntüleme `Movie` **>** tabloya sağ tıklayın

  ![Film masasında bağlamsal menü açılır](working-with-sql/_static/ssox2.png)

  ![Tablo verilerini gösteren film tablosu açık](working-with-sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---
<!-- End of VS tabs -->

## <a name="seed-the-database"></a>Veritabanını tohumla

Modeller klasöründe `SeedData` yeni *Models* bir sınıf oluşturun. Oluşturulan kodu aşağıdakilerle değiştirin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/SeedData.cs?name=snippet_1)]

DB'de film varsa, tohum başdöndürür ve film eklenmez.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Tohum başharfekle

*Program.cs* içeriğini aşağıdaki kodla değiştirin:

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Program.cs)]

Uygulamayı test edin

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* DB'deki tüm kayıtları silin. Bunu tarayıcıdaki silme bağlantılarıyla veya SSOX'tan yapabilirsiniz.
* Tohum yönteminin çalıştırabilmesi için uygulamayı `Startup` başlatmaya (sınıftaki yöntemleri çağırın) zorlar. Başlatmayı zorlamak için IIS Express durdurulmalı ve yeniden başlatılmalıdır. Bunu aşağıdaki yaklaşımlardan herhangi biriyle yapabilirsiniz:

  * Bildirim alanındaki IIS Express sistem tepsisi simgesine sağ tıklayın ve **Siteden Çık** veya **Durdur'a** dokunun

    ![IIS Express sistem tepsisi simgesi](working-with-sql/_static/iisExIcon.png)

    ![Bağlamsal menü](working-with-sql/_static/stopIIS.png)

    * Hata ayıklama modunda VS çalıştırıyorsanız, hata ayıklama modunda çalıştırmak için F5 tuşuna basın
    * Hata ayıklama modunda VS çalıştırıyorsanız, hata ayıklamayı durdurun ve F5 tuşuna basın

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

DB'deki tüm kayıtları silin (Böylece tohum yöntemi çalışacaktır). Veritabanını tohumlamak için uygulamayı durdurun ve başlatın.

---

Uygulama, tohumlu verileri gösterir.

![Microsoft Edge'de film verilerini gösteren MVC Film uygulaması açıldı](working-with-sql/_static/m55_mac.png)

> [!div class="step-by-step"]
> [Önceki](adding-model.md)
> [Sonraki](controller-methods-views.md)

::: moniker-end
