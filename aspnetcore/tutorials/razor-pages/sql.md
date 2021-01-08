---
title: 4. bölüm, bir veritabanıyla çalışma
author: rick-anderson
description: Sayfalardaki eğitim serisinin 4. bölümü Razor .
ms.author: riande
ms.date: 01/05/2021
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
uid: tutorials/razor-pages/sql
ms.openlocfilehash: 8c9d0d9c24e0ce81925ccde463bcf085531b665e
ms.sourcegitcommit: 8b0e9a72c1599ce21830c843558a661ba908ce32
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98024747"
---
# <a name="part-4-of-tutorial-series-on-no-locrazor-pages"></a>Sayfalardaki Eğitim Serisi Bölüm 4 Razor

By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [ali Audette](https://twitter.com/joeaudette)

::: moniker range=">= aspnetcore-5.0"

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

`RazorPagesMovieContext`Nesnesi veritabanına bağlanma ve `Movie` nesneleri veritabanı kayıtlarına eşleme görevini işler. Veritabanı bağlamı, Startup.cs içindeki yöntemde [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedilir `ConfigureServices` : 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

ASP.NET Core [yapılandırma](xref:fundamentals/configuration/index) sistemi `ConnectionString` anahtarı okur. Yerel geliştirme için, yapılandırma bağlantı dizesini *appsettings.json* dosyadan alır.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Oluşturulan bağlantı dizesi aşağıdaki JSON ile benzerdir:

[!code-json[](razor-pages-start/sample/RazorPagesMovie50/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50/appsettings_SQLite.json?highlight=10-12)]

---

Uygulama bir test veya üretim sunucusuna dağıtıldığında, bağlantı dizesini bir test veya üretim veritabanı sunucusuna ayarlamak için bir ortam değişkeni kullanılabilir. Daha fazla bilgi için bkz. [yapılandırma](xref:fundamentals/configuration/index).

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB, program geliştirmeye yönelik SQL Server Express veritabanı altyapısının hafif bir sürümüdür. LocalDB, istek üzerine başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur. Varsayılan olarak, LocalDB veritabanı `*.mdf` dizinde dosya oluşturur `C:\Users\<user>\` .

<a name="ssox"></a>
1. **Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.

   ![Görünüm menüsü](sql/_static/5/ssox.png)

1. Tabloya sağ tıklayıp `Movie` **Görünüm Tasarımcısı**' nı seçin:

   ![Film tablosunda açık bağlamsal menüler](sql/_static/5/design.png)

   ![Tasarımcı 'da açık film tabloları](sql/_static/dv.png)

   Seçeneğinin yanında bulunan anahtar simgesine göz önünde edin `ID` . Varsayılan olarak, EF birincil anahtar için adlı bir özellik oluşturur `ID` .

1. Tabloya sağ tıklayın `Movie` ve **verileri görüntüle**' yi seçin:

   ![Tablo verilerini gösteren film tablosu açma](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a>SQLite

[SQLite](https://www.sqlite.org/) Web sitesi şunları belirtir:

> SQLite, kendinden bağımsız, yüksek güvenilirlik, gömülü, tam özellikli, genel etki alanı, SQL veritabanı altyapısı. SQLite, dünyanın en çok kullanılan veritabanı altyapısıdır.

Bir SQLite veritabanını yönetmek ve görüntülemek için indirebileceğiniz birçok üçüncü taraf araç vardır. Aşağıdaki görüntü, [SQLite Için veritabanı tarayıcısıdır](https://sqlitebrowser.org/). En sevdiğiniz bir SQLite aracınız varsa, onunla ilgili olarak istediğiniz gibi bir yorum bırakın.

![Film veritabanını gösteren SQLite için DB tarayıcısı](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> Bu öğreticide, Entity Framework Core *geçişleri* özelliği mümkün olan yerlerde kullanılır. Geçişler, veritabanı şemasını veri modelindeki değişikliklerle eşleşecek şekilde güncelleştirir. Ancak, geçişler yalnızca EF Core sağlayıcının desteklediği değişiklik türlerini yapabilir ve SQLite sağlayıcının özellikleri sınırlıdır. Örneğin, bir sütun ekleme desteklenir, ancak bir sütunun kaldırılması veya değiştirilmesi desteklenmez. Bir sütunu kaldırmak veya değiştirmek için bir geçiş oluşturulursa, `ef migrations add` komut başarılı olur ancak `ef database update` komut başarısız olur. Bu kısıtlamalar nedeniyle, bu öğretici SQLite şema değişiklikleri için geçişleri kullanmaz. Bunun yerine, şema değiştiğinde veritabanı bırakılır ve yeniden oluşturulur.
>
>SQLite sınırlamalarına yönelik geçici çözüm, tablodaki bir şeyler değiştiğinde tablo yeniden oluşturmak için geçiş kodunu el ile yazmak için kullanılır. Tablo yeniden oluşturma şunları içerir:
>
>* Yeni bir tablo oluşturuluyor.
>* Eski tablodaki veriler yeni tabloya kopyalanıyor.
>* Eski tablo bırakılıyor.
>* Yeni tablo yeniden adlandırılıyor.
>
>Daha fazla bilgi için aşağıdaki kaynaklara bakın:
> * [SQLite EF Core veritabanı sağlayıcısı sınırlamaları](/ef/core/providers/sqlite/limitations)
> * [Geçiş kodunu özelleştirme](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Veri çekirdeği oluşturma](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE ifadesi](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a>Veritabanını çekirdek

`SeedData` *Modeller* klasöründe aşağıdaki kodla adlı yeni bir sınıf oluşturun:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

Veritabanında herhangi bir film varsa, tohum başlatıcısı döner ve hiçbir film eklenmez.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Tohum başlatıcısı ekleme

*Program.cs* dosyasının içeriğini aşağıdaki kodla değiştirin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie50/Program.cs)]

Önceki kodda, `Main` yöntemi aşağıdaki şekilde değiştirilmiştir:

* Bağımlılık ekleme kapsayıcısından bir veritabanı bağlamı örneği alın.
* Yöntemini çağırın `seedData.Initialize` , bunu veritabanı bağlamı örneğine geçirerek.
* Çekirdek yöntemi tamamlandığında bağlamı atın. [Using deyimleri](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) , içeriğin atılmasını sağlar.

Çalıştırılmayan aşağıdaki özel durum oluşur `Update-Database` :

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>Uygulamayı test etme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. Veritabanındaki tüm kayıtları silin. Tarayıcıda veya [Ssox](xref:tutorials/razor-pages/new-field#ssox) 'ten silme bağlantılarını kullanma

1. Sınıfında yöntemleri çağırarak uygulamayı başlamaya zorlayın `Startup` , böylece çekirdek yöntemi çalışır. Başlatmayı zorlamak için IIS Express durdurulup yeniden başlatılması gerekir. Aşağıdaki yaklaşımlardan biriyle IIS 'yi durdurun ve yeniden başlatın:

   1. Bildirim alanında IIS Express sistem tepsisi simgesine sağ tıklayın ve **Çıkış** veya **siteyi durdur**' u seçin:

      ![IIS Express sistem tepsisi simgesi](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

      ![Bağlamsal menü](sql/_static/stopIIS.png)

   1. Uygulama hata ayıklamasız modda çalışıyorsa, hata ayıklama modunda çalıştırmak için <kbd>F5</kbd> tuşuna basın.
   1. Uygulama hata ayıklama modunda olduğunda, hata ayıklayıcıyı durdurun ve <kbd>F5</kbd>tuşuna basın.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

Çekirdek yöntemi çalışacak şekilde veritabanındaki tüm kayıtları silin. Veritabanını temel alarak uygulamayı durdurup başlatın.

---

Uygulama, sağlanan verileri gösterir:

![Film verilerini gösteren tarayıcıda açık film uygulaması](sql/_static/5/m55.png)

## <a name="additional-resources"></a>Ek kaynaklar

> [!div class="step-by-step"]
> [Önceki: Scafkatlanmış Razor ](xref:tutorials/razor-pages/page) 
>  [Sonraki sayfalar: sayfaları güncelleştirme](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-5.0 >= aspnetcore-3.0"

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

`RazorPagesMovieContext`Nesnesi veritabanına bağlanma ve `Movie` nesneleri veritabanı kayıtlarına eşleme görevini işler. Veritabanı bağlamı, Startup.cs içindeki yöntemde [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedilir `ConfigureServices` : 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_UseSqlite&highlight=5-6)]

---

ASP.NET Core [yapılandırma](xref:fundamentals/configuration/index) sistemi `ConnectionString` anahtarı okur. Yerel geliştirme için, yapılandırma bağlantı dizesini *appsettings.json* dosyadan alır.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Oluşturulan bağlantı dizesi aşağıdakine benzer olacaktır:

[!code-json[](razor-pages-start/sample/RazorPagesMovie30/appsettings.json?highlight=10-12)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/appsettings_SQLite.json?highlight=10-12)]

---

Uygulama bir test veya üretim sunucusuna dağıtıldığında, bağlantı dizesini bir test veya üretim veritabanı sunucusuna ayarlamak için bir ortam değişkeni kullanılabilir. Daha fazla bilgi için bkz. [yapılandırma](xref:fundamentals/configuration/index) .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB, program geliştirmeye yönelik SQL Server Express veritabanı altyapısının hafif bir sürümüdür. LocalDB, istek üzerine başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur. Varsayılan olarak, LocalDB veritabanı `*.mdf` dizinde dosya oluşturur `C:\Users\<user>\` .

<a name="ssox"></a>
* **Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.

  ![Görünüm menüsü](sql/_static/ssox.png)

* Tabloya sağ tıklayıp `Movie` **Görünüm Tasarımcısı**' nı seçin:

  ![Film tablosunda açık bağlamsal menüler](sql/_static/design.png)

  ![Tasarımcı 'da açık film tabloları](sql/_static/dv.png)

Seçeneğinin yanında bulunan anahtar simgesine göz önünde edin `ID` . Varsayılan olarak, EF birincil anahtar için adlı bir özellik oluşturur `ID` .

* Tabloya sağ tıklayın `Movie` ve **verileri görüntüle**' yi seçin:

  ![Tablo verilerini gösteren film tablosu açma](sql/_static/vd22.png)

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

## <a name="sqlite"></a>SQLite

[SQLite](https://www.sqlite.org/) Web sitesi şunları belirtir:

> SQLite, kendinden bağımsız, yüksek güvenilirlik, gömülü, tam özellikli, genel etki alanı, SQL veritabanı altyapısı. SQLite, dünyanın en çok kullanılan veritabanı altyapısıdır.

Bir SQLite veritabanını yönetmek ve görüntülemek için indirebileceğiniz birçok üçüncü taraf araç vardır. Aşağıdaki görüntü, [SQLite Için veritabanı tarayıcısıdır](https://sqlitebrowser.org/). En sevdiğiniz bir SQLite aracınız varsa, onunla ilgili olarak istediğiniz gibi bir yorum bırakın.

![Film veritabanını gösteren SQLite için DB tarayıcısı](~/tutorials/first-mvc-app-xplat/working-with-sql/_static/dbb.png)

> [!NOTE]
> Bu öğreticide, Entity Framework Core *geçişleri* özelliği mümkün olan yerlerde kullanılır. Geçişler, veritabanı şemasını veri modelindeki değişikliklerle eşleşecek şekilde güncelleştirir. Ancak, geçişler yalnızca EF Core sağlayıcının desteklediği değişiklik türlerini yapabilir ve SQLite sağlayıcının özellikleri sınırlıdır. Örneğin, bir sütun ekleme desteklenir, ancak bir sütunun kaldırılması veya değiştirilmesi desteklenmez. Bir sütunu kaldırmak veya değiştirmek için bir geçiş oluşturulursa, `ef migrations add` komut başarılı olur ancak `ef database update` komut başarısız olur. Bu kısıtlamalar nedeniyle, bu öğretici SQLite şema değişiklikleri için geçişleri kullanmaz. Bunun yerine, şema değiştiğinde veritabanı bırakılır ve yeniden oluşturulur.
>
>SQLite sınırlamalarına yönelik geçici çözüm, tablodaki bir şeyler değiştiğinde tablo yeniden oluşturmak için geçiş kodunu el ile yazmak için kullanılır. Tablo yeniden oluşturma şunları içerir:
>
>* Yeni bir tablo oluşturuluyor.
>* Eski tablodaki veriler yeni tabloya kopyalanıyor.
>* Eski tablo bırakılıyor.
>* Yeni tablo yeniden adlandırılıyor.
>
>Daha fazla bilgi için aşağıdaki kaynaklara bakın:
> * [SQLite EF Core veritabanı sağlayıcısı sınırlamaları](/ef/core/providers/sqlite/limitations)
> * [Geçiş kodunu özelleştirme](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [Veri çekirdeği oluşturma](/ef/core/modeling/data-seeding)
> * [SQLite ALTER TABLE ifadesi](https://sqlite.org/lang_altertable.html)

---

## <a name="seed-the-database"></a>Veritabanını çekirdek

`SeedData` *Modeller* klasöründe aşağıdaki kodla adlı yeni bir sınıf oluşturun:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedData.cs?name=snippet_1)]

Veritabanında herhangi bir film varsa, tohum başlatıcısı döner ve hiçbir film eklenmez.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Tohum başlatıcısı ekleme

*Program.cs* dosyasının içeriğini aşağıdaki kodla değiştirin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Program.cs)]

Önceki kodda, `Main` yöntemi aşağıdaki şekilde değiştirilmiştir:

* Bağımlılık ekleme kapsayıcısından bir veritabanı bağlamı örneği alın.
* Yöntemini çağırın `seedData.Initialize` , bunu veritabanı bağlamı örneğine geçirerek.
* Çekirdek yöntemi tamamlandığında bağlamı atın. [Using deyimleri](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) , içeriğin atılmasını sağlar.

Çalıştırılmayan aşağıdaki özel durum oluşur `Update-Database` :

> `SqlException: Cannot open database "RazorPagesMovieContext-" requested by the login. The login failed.`
> `Login failed for user 'user name'.`

### <a name="test-the-app"></a>Uygulamayı test etme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Veritabanındaki tüm kayıtları silin. Tarayıcıda veya [Ssox](xref:tutorials/razor-pages/new-field#ssox)'ten silme bağlantılarını kullanın.
* Sınıfında yöntemleri çağırarak uygulamayı başlamaya zorlayın `Startup` , böylece çekirdek yöntemi çalışır. Başlatmayı zorlamak için IIS Express durdurulup yeniden başlatılması gerekir. Aşağıdaki yaklaşımlardan biriyle IIS 'yi durdurun ve yeniden başlatın:

  * Bildirim alanında IIS Express sistem tepsisi simgesine sağ tıklayın ve **Çıkış** veya **siteyi durdur**' a dokunun:

    ![IIS Express sistem tepsisi simgesi](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Bağlamsal menü](sql/_static/stopIIS.png)

    * Uygulama hata ayıklamasız modda çalışıyorsa, hata ayıklama modunda çalıştırmak için <kbd>F5</kbd> tuşuna basın.
    * Uygulama hata ayıklama modunda olduğunda, hata ayıklayıcıyı durdurun ve <kbd>F5</kbd>tuşuna basın.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

Çekirdek yöntemi çalışacak şekilde veritabanındaki tüm kayıtları silin. Veritabanını temel alarak uygulamayı durdurup başlatın.

---

Uygulama, sağlanan verileri gösterir:

![Film verilerini gösteren Chrome 'da film uygulaması açık](sql/_static/m55https.png)

## <a name="additional-resources"></a>Ek kaynaklar

> [!div class="step-by-step"]
> [Önceki: Scafkatlanmış Razor ](xref:tutorials/razor-pages/page) 
>  [Sonraki sayfalar: sayfaları güncelleştirme](xref:tutorials/razor-pages/da1)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start) ([nasıl indirilir](xref:index#how-to-download-a-sample)).

`RazorPagesMovieContext`Nesnesi veritabanına bağlanma ve `Movie` nesneleri veritabanı kayıtlarına eşleme görevini işler. Veritabanı bağlamı, Startup.cs içindeki yöntemde [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına kaydedilir `ConfigureServices` : 

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-17)]

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_UseSqlite&highlight=11-12)]

---

İçinde kullanılan yöntemler hakkında daha fazla bilgi için `ConfigureServices` bkz.:

* [ASP.NET Core Için ab genel veri koruma yönetmeliği (GDPR) desteği](xref:security/gdpr) `CookiePolicyOptions` .
* [SetCompatibilityVersion](xref:mvc/compatibility-version)

ASP.NET Core [yapılandırma](xref:fundamentals/configuration/index) sistemi `ConnectionString` anahtarı okur. Yerel geliştirme için, yapılandırma bağlantı dizesini *appsettings.json* dosyadan alır.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Oluşturulan bağlantı dizesi aşağıdakine benzer olacaktır:

[!code-json[](razor-pages-start/sample/RazorPagesMovie22/appsettings.json?highlight=8-10)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!code-json[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/appsettings_SQLite.json?highlight=8-10)]

---

Uygulama bir test veya üretim sunucusuna dağıtıldığında, bağlantı dizesini bir test veya üretim veritabanı sunucusuna ayarlamak için bir ortam değişkeni kullanılabilir. Daha fazla bilgi için bkz. [yapılandırma](xref:fundamentals/configuration/index) .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

## <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

LocalDB, program geliştirmeye yönelik SQL Server Express veritabanı altyapısının hafif bir sürümüdür. LocalDB, istek üzerine başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur. Varsayılan olarak, LocalDB veritabanı `*.mdf` dizinde dosya oluşturur `C:/Users/<user/>` .

<a name="ssox"></a>
* **Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.

  ![Görünüm menüsü](sql/_static/ssox.png)

* Tabloya sağ tıklayıp `Movie` **Görünüm Tasarımcısı**' nı seçin:

  ![Film tablosunda bağlam menüsü açık](sql/_static/design.png)

  ![Tasarımcıda film tablosu aç](sql/_static/dv.png)

Seçeneğinin yanında bulunan anahtar simgesine göz önünde edin `ID` . Varsayılan olarak, EF birincil anahtar için adlı bir özellik oluşturur `ID` .

* Tabloya sağ tıklayın `Movie` ve **verileri görüntüle**' yi seçin:

  ![Tablo verilerini gösteren film tablosu açma](sql/_static/vd22.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/rp/sqlite.md)]
[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

---

## <a name="seed-the-database"></a>Veritabanını çekirdek

`SeedData` *Modeller* klasöründe aşağıdaki kodla adlı yeni bir sınıf oluşturun:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedData.cs?name=snippet_1)]

Veritabanında herhangi bir film varsa, tohum başlatıcısı döner ve hiçbir film eklenmez.

```csharp
if (context.Movie.Any())
{
    return;
}
```

<a name="si"></a>

### <a name="add-the-seed-initializer"></a>Tohum başlatıcısı ekleme

*Program.cs* dosyasının içeriğini aşağıdaki kodla değiştirin:

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Program.cs)]

Önceki kodda, `Main` yöntemi aşağıdaki şekilde değiştirilmiştir:

* Bağımlılık ekleme kapsayıcısından bir veritabanı bağlamı örneği alın.
* Yöntemini çağırın `seedData.Initialize` , bunu veritabanı bağlamı örneğine geçirerek.
* Çekirdek yöntemi tamamlandığında bağlamı atın. [Using deyimleri](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using-statement) , içeriğin atılmasını sağlar.

Bir üretim uygulaması çağırmaz `Database.Migrate` . Çalıştırılmayan aşağıdaki özel durumu engellemek için önceki koda eklenir `Update-Database` :

SqlException: Razor oturum açma tarafından istenen "pagesmoviecontext-21" veritabanı açılamıyor. Oturum açılamadı.
' Kullanıcı adı ' kullanıcısı için oturum açma başarısız.

### <a name="test-the-app"></a>Uygulamayı test etme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Veritabanındaki tüm kayıtları silin. Bunu, tarayıcıda veya [Ssox](xref:tutorials/razor-pages/new-field#ssox) 'ten silme bağlantılarıyla yapabilirsiniz
* Sınıfında yöntemleri çağırarak uygulamayı başlamaya zorlayın `Startup` , böylece çekirdek yöntemi çalışır. Başlatmayı zorlamak için IIS Express durdurulup yeniden başlatılması gerekir. Bunu aşağıdaki yaklaşımlardan biriyle yapabilirsiniz:

  * Bildirim alanında IIS Express sistem tepsisi simgesine sağ tıklayın ve **Çıkış** veya **siteyi durdur**' a dokunun:

    ![IIS Express sistem tepsisi simgesi](../first-mvc-app/working-with-sql/_static/iisExIcon.png)

    ![Bağlamsal menü](sql/_static/stopIIS.png)

    * Uygulama hata ayıklamasız modda çalışıyorsa, hata ayıklama modunda çalıştırmak için <kbd>F5</kbd> tuşuna basın.
    * Uygulama hata ayıklama modunda olduğunda, hata ayıklayıcıyı durdurun ve <kbd>F5</kbd>tuşuna basın.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Çekirdek yöntemi çalışacak şekilde veritabanındaki tüm kayıtları silin. Veritabanını temel alarak uygulamayı durdurup başlatın.

# <a name="visual-studio-for-mac"></a>[Mac için Visual Studio](#tab/visual-studio-mac)

Çekirdek yöntemi çalışacak şekilde veritabanındaki tüm kayıtları silin. Veritabanını temel alarak uygulamayı durdurup başlatın.

---

Uygulama, sağlanan verileri gösterir:

![Film verilerini gösteren Chrome 'da film uygulaması açık](sql/_static/m55https.png)

Sonraki öğretici, verilerin sunumunu temizler.

## <a name="additional-resources"></a>Ek kaynaklar

* [Bu öğreticinin YouTube sürümü](https://youtu.be/A_5ff11sDHY)

> [!div class="step-by-step"]
> [Önceki: Scafkatlanmış Razor ](xref:tutorials/razor-pages/page) 
>  [Sonraki sayfalar: sayfaları güncelleştirme](xref:tutorials/razor-pages/da1)

::: moniker-end
