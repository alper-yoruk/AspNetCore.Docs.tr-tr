---
title: Bir veritabanı ve ASP.NET Core ile çalışın
author: rick-anderson
description: Bir veritabanı ve ASP.NET Core ile çalışmayı açıklar.
ms.author: riande
ms.date: 7/22/2019
uid: tutorials/razor-pages/sql
ms.openlocfilehash: b5acb573f8fa39e5300ecdb359113d8697d78934
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664341"
---
# <a name="work-with-a-database-and-aspnet-core"></a>Bir veritabanı ve ASP.NET Core ile çalışın

Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Joe Audette](https://twitter.com/joeaudette)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Nesne, `RazorPagesMovieContext` veritabanına bağlanma ve nesneleri veritabanı `Movie` kayıtlarına eşleme görevini işler. Veritabanı *bağlamı, Startup.cs*yönteminde `ConfigureServices` [Bağımlılık Enjeksiyonu](xref:fundamentals/dependency-injection) kapsayıcısı ile kaydedilir:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

ASP.NET Core [Configuration](xref:fundamentals/configuration/index) sistemi `ConnectionString`okur. Yerel geliştirme için, *appsettings.json* dosyasından bağlantı dizesini alır.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Veritabanının ad değeri`Database={Database name}`( ) oluşturduğunuz kod için farklı olacaktır. Ad değeri rasgele.

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Uygulama bir test veya üretim sunucusuna dağıtıldığında, bağlantı dizesini gerçek bir veritabanı sunucusuna ayarlamak için bir ortam değişkeni kullanılabilir. Daha fazla bilgi için [Yapılandırma'ya](xref:fundamentals/configuration/index) bakın.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB, SQL Server Express veritabanı altyapısının program geliştirme yi hedefleyen hafif bir sürümüdür. LocalDB isteğe bağlı olarak başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur. Varsayılan olarak, LocalDB `*.mdf` veritabanı dizinde `C:\Users\<user>\` dosyalar oluşturur.

<a name="ssox"></a>
* **Görünüm** menüsünden **SQL Server Object Explorer'ı** (SSOX) açın.

  ![Görünüm menüsü](sql/_static/ssox.png)

* Tabloya sağ `Movie` tıklayın ve **Tasarımcıyı Görüntüle'yi**seçin:

  ![Film tablosunda bağlamsal menüler açılır](sql/_static/design.png)

  ![Designer'da film masaları açıldı](sql/_static/dv.png)

Yanındaki anahtar simgesine `ID`dikkat edin. Varsayılan olarak, EF birincil `ID` anahtar için adlandırılmış bir özellik oluşturur.

* Tabloya sağ `Movie` tıklayın ve **Verileri Görüntüle'yi**seçin:

  ![Tablo verilerini gösteren film tablosu açık](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Veritabanını tohumla

Modeller klasöründe `SeedData` aşağıdaki *Models* kodla adında yeni bir sınıf oluşturun:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

DB'de film varsa, tohum başdöndürür ve film eklenmez.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Tohum başharfekle

*Program.cs,* `Main` aşağıdakileri yapmak için yöntemi değiştirin:

* Bağımlılık enjeksiyon kapsayıcısından bir DB bağlam örneği alın.
* Tohum yöntemini çağırın, ona bağlamı geçirin.
* Tohum yöntemi tamamlandığında bağlamı atın.

Aşağıdaki kod güncelleştirilmiş *Program.cs* dosyasını gösterir.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

Aşağıdaki özel durum `Update-Database` çalıştırıldığında oluşur:

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>Uygulamayı test edin

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* DB'deki tüm kayıtları silin. Bunu tarayıcıdaki silme bağlantılarıyla veya [SSOX'tan](xref:tutorials/razor-pages/new-field#ssox) yapabilirsiniz
* Tohum yönteminin çalıştırabilmesi için uygulamayı `Startup` başlatmaya (sınıftaki yöntemleri çağırın) zorlar. Başlatmayı zorlamak için IIS Express durdurulmalı ve yeniden başlatılmalıdır. Bunu aşağıdaki yaklaşımlardan herhangi biriyle yapabilirsiniz:

  * Bildirim alanındaki IIS Express sistem tepsisi simgesine sağ tıklayın ve **Çıkış** veya **Durdur Sitesi'ne**dokunun:

    ![IIS Express sistem tepsisi simgesi](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Bağlamsal menü](sql/_static/stopIIS.png)

    * Hata ayıklama modunda VS çalıştırıyorsanız, hata ayıklama modunda çalıştırmak için F5 tuşuna basın.
    * Hata ayıklama modunda VS çalıştırıyorsanız, hata ayıklama durdurun ve F5 tuşuna basın.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

DB'deki tüm kayıtları silin (Böylece tohum yöntemi çalışacaktır). Veritabanını tohumlamak için uygulamayı durdurun ve başlatın.

Uygulama, tohumlu verileri gösterir.

---

Bir sonraki öğretici, verilerin sunumuna iyi gelecektir.

## <a name="additional-resources"></a>Ek kaynaklar

> [!div class="step-by-step"]
> [Önceki: İskeleli Jilet Sayfaları](xref:tutorials/razor-pages/page)
> [Sonraki: Sayfaları Güncelleme](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

Nesne, `RazorPagesMovieContext` veritabanına bağlanma ve nesneleri veritabanı `Movie` kayıtlarına eşleme görevini işler. Veritabanı *bağlamı, Startup.cs*yönteminde `ConfigureServices` [Bağımlılık Enjeksiyonu](xref:fundamentals/dependency-injection) kapsayıcısı ile kaydedilir:

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

Kullanılan `ConfigureServices`yöntemler hakkında daha fazla bilgi için bkz:

* [AB Genel Veri Koruma Yönetmeliği (GDPR) için ASP.NET Çekirdek](xref:security/gdpr) `CookiePolicyOptions`desteği.
* [SetCompatibilityVersion](xref:mvc/compatibility-version)

ASP.NET Core [Configuration](xref:fundamentals/configuration/index) sistemi `ConnectionString`okur. Yerel geliştirme için, *appsettings.json* dosyasından bağlantı dizesini alır.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Veritabanının ad değeri`Database={Database name}`( ) oluşturduğunuz kod için farklı olacaktır. Ad değeri rasgele.

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie/appsettings_SQLite.json?highlight=8-10)]

---

Uygulama bir test veya üretim sunucusuna dağıtıldığında, bağlantı dizesini gerçek bir veritabanı sunucusuna ayarlamak için bir ortam değişkeni kullanılabilir. Daha fazla bilgi için [Yapılandırma'ya](xref:fundamentals/configuration/index) bakın.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB, SQL Server Express veritabanı altyapısının program geliştirme yi hedefleyen hafif bir sürümüdür. LocalDB isteğe bağlı olarak başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur. Varsayılan olarak, LocalDB `*.mdf` veritabanı dizinde `C:/Users/<user/>` dosyalar oluşturur.

<a name="ssox"></a>
* **Görünüm** menüsünden **SQL Server Object Explorer'ı** (SSOX) açın.

  ![Görünüm menüsü](sql/_static/ssox.png)

* Tabloya sağ `Movie` tıklayın ve **Tasarımcıyı Görüntüle'yi**seçin:

  ![Film masasında bağlamsal menü açılır](sql/_static/design.png)

  ![Designer'da film masası açıldı](sql/_static/dv.png)

Yanındaki anahtar simgesine `ID`dikkat edin. Varsayılan olarak, EF birincil `ID` anahtar için adlandırılmış bir özellik oluşturur.

* Tabloya sağ `Movie` tıklayın ve **Verileri Görüntüle'yi**seçin:

  ![Tablo verilerini gösteren film tablosu açık](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Veritabanını tohumla

Modeller klasöründe `SeedData` aşağıdaki *Models* kodla adında yeni bir sınıf oluşturun:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

DB'de film varsa, tohum başdöndürür ve film eklenmez.

```csharp
if (context.Movie.Any())
{
    return;   // DB has been seeded.
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Tohum başharfekle

*Program.cs,* `Main` aşağıdakileri yapmak için yöntemi değiştirin:

* Bağımlılık enjeksiyon kapsayıcısından bir DB bağlam örneği alın.
* Tohum yöntemini çağırın, ona bağlamı geçirin.
* Tohum yöntemi tamamlandığında bağlamı atın.

Aşağıdaki kod güncelleştirilmiş *Program.cs* dosyasını gösterir.

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

Bir üretim uygulaması `Database.Migrate`aramaz. Çalıştırıldığında aşağıdaki özel durumu `Update-Database` önlemek için önceki koda eklenir:

SqlException: Giriş tarafından istenen "RazorPagesMovieContext-21" veritabanını açılamaz. Giriş başarısız oldu.
Giriş kullanıcı 'kullanıcı adı' için başarısız oldu.

### <a name="test-the-app"></a>Uygulamayı test edin

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* DB'deki tüm kayıtları silin. Bunu tarayıcıdaki silme bağlantılarıyla veya [SSOX'tan](xref:tutorials/razor-pages/new-field#ssox) yapabilirsiniz
* Tohum yönteminin çalıştırabilmesi için uygulamayı `Startup` başlatmaya (sınıftaki yöntemleri çağırın) zorlar. Başlatmayı zorlamak için IIS Express durdurulmalı ve yeniden başlatılmalıdır. Bunu aşağıdaki yaklaşımlardan herhangi biriyle yapabilirsiniz:

  * Bildirim alanındaki IIS Express sistem tepsisi simgesine sağ tıklayın ve **Çıkış** veya **Durdur Sitesi'ne**dokunun:

    ![IIS Express sistem tepsisi simgesi](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Bağlamsal menü](sql/_static/stopIIS.png)

    * Hata ayıklama modunda VS çalıştırıyorsanız, hata ayıklama modunda çalıştırmak için F5 tuşuna basın.
    * Hata ayıklama modunda VS çalıştırıyorsanız, hata ayıklama durdurun ve F5 tuşuna basın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

DB'deki tüm kayıtları silin (Böylece tohum yöntemi çalışacaktır). Veritabanını tohumlamak için uygulamayı durdurun ve başlatın.

Uygulama, tohumlu verileri gösterir.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

DB'deki tüm kayıtları silin (Böylece tohum yöntemi çalışacaktır). Veritabanını tohumlamak için uygulamayı durdurun ve başlatın.

Uygulama, tohumlu verileri gösterir.

---

Uygulama tohumlu verileri gösterir:

![Chrome'da film verilerini gösteren film uygulaması açıldı](sql/_static/m55.png)

Bir sonraki öğretici, verilerin sunumuna temizlenir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [Önceki: İskeleli Jilet Sayfaları](xref:tutorials/razor-pages/page)
> [Sonraki: Sayfaları Güncelleme](xref:tutorials/razor-pages/da1)

::: moniker-end
