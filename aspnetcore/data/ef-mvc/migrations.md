---
title: Eğitim Bölümü 5, Contoso University örneğine geçişler uygulama
description: Contoso Üniversitesi öğreticisi serisinin 5. bölümü. ASP.NET Core MVC uygulamasında veri modeli değişikliklerini yönetmek için EF Core geçişleri özelliğini kullanın.
author: rick-anderson
ms.author: riande
ms.custom: contperfq2
ms.date: 11/13/2020
ms.topic: tutorial
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
uid: data/ef-mvc/migrations
ms.openlocfilehash: ab5be222416e61fcff90c5130ca91ad4a2a5c9b0
ms.sourcegitcommit: bce62ceaac7782e22d185814f2e8532c84efa472
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94674010"
---
# <a name="tutorial-part-5-apply-migrations-to-the-contoso-university-sample"></a>Öğretici: 5. kısım, Contoso Üniversitesi örneğine geçişler uygulama

Bu öğreticide, veri modeli değişikliklerini yönetmek için EF Core geçişleri özelliğini kullanmaya başlayabilirsiniz. Sonraki öğreticilerde, veri modelini değiştirirken daha fazla geçiş ekleyeceksiniz.

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Geçişler hakkında bilgi edinin
> * İlk geçiş oluşturma
> * Yukarı ve aşağı yöntemleri inceleyin
> * Veri modeli anlık görüntüsü hakkında bilgi edinin
> * Geçişi Uygula

## <a name="prerequisites"></a>Önkoşullar

* [Sıralama, filtreleme ve sayfalama](sort-filter-page.md)

## <a name="about-migrations"></a>Geçişler hakkında

Yeni bir uygulama geliştirirken, veri modeliniz sıklıkla değişir ve model her değiştiğinde veritabanıyla eşitlenmemiş olur. Mevcut değilse veritabanını oluşturmak için Entity Framework yapılandırarak bu öğreticileri başlatmış olursunuz. Veri modelini her değiştirdiğinizde (varlık sınıfları ekleyin, kaldırın veya değiştirin ya da DbContext sınıfınızı değiştirin); veritabanını silebilir ve EF, modelle eşleşen yeni bir tane oluşturur ve test verileriyle birlikte olur.

Veritabanını veri modeliyle eşitlenmiş halde tutma yöntemi, uygulamayı üretime dağıtana kadar iyi çalışır. Uygulama üretimde çalışırken, genellikle tutmak istediğiniz verileri saklar ve yeni sütun ekleme gibi her değişiklik yaptığınızda her şeyi kaybetmek istemezsiniz. EF Core geçişleri özelliği, yeni bir veritabanı oluşturmak yerine EF 'in veritabanı şemasını güncelleştirmesine olanak sağlayarak bu sorunu çözer.

Geçişlerle çalışmak için **Paket Yöneticisi konsolu 'nu** (PMC) veya CLI 'yi kullanabilirsiniz.  Bu öğreticiler CLı komutlarının nasıl kullanılacağını göstermektedir. PMC hakkındaki bilgiler [Bu öğreticinin sonunda](#pmc).

## <a name="drop-the-database"></a>Veritabanını bırak

Veritabanını silin. **SQL Server Nesne Gezgini** (ssox) veya `database drop` CLI komutunu kullanın:

 ```dotnetcli
 dotnet ef database drop
 ```

Aşağıdaki bölümde CLı komutlarının nasıl çalıştırılacağı açıklanmaktadır.

## <a name="create-an-initial-migration"></a>İlk geçiş oluşturma

Değişikliklerinizi kaydedin ve projeyi derleyin. Sonra bir komut penceresi açın ve proje klasörüne gidin. Bunu yapmanın hızlı bir yolu aşağıda verilmiştir:

* **Çözüm Gezgini**' de projeye sağ tıklayın ve bağlam menüsünden **klasörü dosya Gezgini 'nde aç** ' ı seçin.

  ![Dosya Gezgini menü öğesinde aç](migrations/_static/open-in-file-explorer.png)

* Adres çubuğuna "cmd" yazın ve ENTER tuşuna basın.

  ![Komut penceresini aç](migrations/_static/open-command-window.png)

Komut penceresine aşağıdaki komutu girin:

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet ef migrations add InitialCreate
```

`dotnet tool install --global dotnet-ef``dotnet ef` [genel bir araç](/ef/core/miscellaneous/cli/dotnet)olarak yüklenir.

Yukarıdaki komutlarda aşağıdakine benzer bir çıktı görüntülenir:

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

"*Başka bir işlem tarafından kullanıldığından," dosya... ContosoUniversity.dll dosyasına erişilemiyor*. ", Windows Sistem tepsisindeki IIS Express simgesini bulun ve sağ tıklayın, sonra da **Contosouniversity > siteyi durdur**' a tıklayın.

## <a name="examine-up-and-down-methods"></a>Yukarı ve aşağı yöntemleri inceleyin

`migrations add`Komutunu çalıştırdığınızda, EF, veritabanını sıfırdan oluşturacak kodu oluşturmuş olur. Bu kod, *\<timestamp> _InitialCreate. cs* adlı dosyadaki *geçişler* klasöründedir. `Up`Sınıfının yöntemi, `InitialCreate` veri modeli varlık kümelerine karşılık gelen veritabanı tablolarını oluşturur ve `Down` Aşağıdaki örnekte gösterildiği gibi yöntemi onları siler.

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

Geçişler, `Up` geçiş için veri modeli değişikliklerini uygulamak üzere yöntemini çağırır. Güncelleştirmeyi geri almak için bir komut girdiğinizde, geçişler `Down` yöntemini çağırır.

Bu kod, komutunu girdiğinizde oluşturulan ilk geçişe yöneliktir `migrations add InitialCreate` . Geçiş adı parametresi (örnekteki "ınitialcreate") dosya adı için kullanılır ve istediğiniz her şey olabilir. Geçiş sırasında nelerin yapıldığını özetleyen bir sözcük veya tümcecik seçmek en iyisidir. Örneğin, "AddDepartmentTable" adlı bir geçişe daha sonra ad yazabilirsiniz.

Veritabanı zaten mevcut olduğunda ilk geçişi oluşturduysanız veritabanı oluşturma kodu oluşturulur, ancak veritabanı veri modeliyle zaten eşleştiğinden çalıştırması gerekmez. Uygulamayı, veritabanının mevcut olmadığı başka bir ortama dağıttığınızda, bu kod veritabanınızı oluşturmak için çalışır, bu nedenle ilk önce test etmek iyi bir fikirdir. Bu nedenle, bağlantı dizesinde veritabanının adını daha önce değiştirmiş olursunuz. böylece geçişler sıfırdan yeni bir tane oluşturabilir.

## <a name="the-data-model-snapshot"></a>Veri modeli anlık görüntüsü

Geçişler, *geçiş/SchoolContextModelSnapshot. cs* içinde geçerli veritabanı şemasının bir *anlık görüntüsünü* oluşturur. Bir geçiş eklediğinizde EF, veri modeli Snapshot dosyası ile karşılaştırılarak nelerin değiştirildiğini belirler.

Bir geçişi kaldırmak için [DotNet EF geçişleri kaldır](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) komutunu kullanın. `dotnet ef migrations remove` geçişi siler ve anlık görüntünün doğru şekilde sıfırlanmasını sağlar. `dotnet ef migrations remove`Başarısız olursa, `dotnet ef migrations remove -v` hata hakkında daha fazla bilgi almak için kullanın.

Anlık görüntü dosyasının nasıl kullanıldığı hakkında daha fazla bilgi için bkz. [Takım ortamlarında EF Core geçişleri](/ef/core/managing-schemas/migrations/teams) .

## <a name="apply-the-migration"></a>Geçişi Uygula

Komut penceresinde, veritabanı ve tabloları oluşturmak için aşağıdaki komutu girin.

```dotnetcli
dotnet ef database update
```

Komutun çıktısı, `migrations add` veritabanının AYARLANDıĞı SQL komutlarının günlüklerini görbelirtilmedikçe, komuta benzer. Günlüklerin çoğu aşağıdaki örnek çıktıda atlanır. Günlük iletilerinde bu ayrıntı düzeyini görmemeyi tercih ediyorsanız, dosyadaki *appsettings.Development.js* günlük düzeyini değiştirebilirsiniz. Daha fazla bilgi için bkz. <xref:fundamentals/logging/index>.

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (274ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (60ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      IF SERVERPROPERTY('EngineEdition') <> 5
      BEGIN
          ALTER DATABASE [ContosoUniversity2] SET READ_COMMITTED_SNAPSHOT ON;
      END;
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (15ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20190327172701_InitialCreate', N'5.0-rtm');
Done.
```

İlk öğreticide yaptığınız gibi veritabanını incelemek için **SQL Server Nesne Gezgini** kullanın.  \_ \_ Hangi geçişlerin veritabanına uygulandığını izleyen bir EFMigrationsHistory tablosunun eklenmesini fark edeceksiniz. Bu tablodaki verileri görüntüleyin ve ilk geçiş için bir satır görürsünüz. (Önceki CLı çıkış örneğinde son oturum, bu satırı oluşturan INSERT ifadesini gösterir.)

Her şeyin daha önce olduğu gibi çalıştığını doğrulamak için uygulamayı çalıştırın.

![Öğrenciler Dizin sayfası](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a>CLı ve PMC karşılaştırması

Geçişleri yönetmek için EF araçları, .NET Core CLI komutlardan veya Visual Studio **Paket Yöneticisi konsolu** (PMC) penceresindeki PowerShell cmdlet 'lerinde bulunabilir. Bu öğreticide, CLı 'nın nasıl kullanılacağı gösterilmektedir, ancak isterseniz PMC 'yi kullanabilirsiniz.

PMC komutlarına yönelik EF komutları [Microsoft. EntityFrameworkCore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) paketidir. Bu paket [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunur, bu nedenle uygulamanıza yönelik bir paket başvurusu varsa bir paket başvurusu eklemeniz gerekmez `Microsoft.AspNetCore.App` .

**Önemli:** Bu, *. csproj* dosyasını düzenleyerek CLI için yüklediğiniz paket ile aynı değildir. Bu adın adı `Tools` ile BITEN CLI paketi adından farklı olarak `Tools.DotNet` .

CLı komutları hakkında daha fazla bilgi için bkz. [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).

PMC komutları hakkında daha fazla bilgi için bkz. [Paket Yöneticisi Konsolu (Visual Studio)](/ef/core/miscellaneous/cli/powershell).

## <a name="get-the-code"></a>Kodu alma

[Tamamlanmış uygulamayı indirin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples)

## <a name="next-step"></a>Sonraki adım

Veri modelini genişletme hakkında daha gelişmiş konulara bakmak için sonraki öğreticiye ilerleyin. Ek geçişler oluşturma ve uygulama gibi.

> [!div class="nextstepaction"]
> [Ek geçişler oluşturma ve uygulama](complex-data-model.md)
