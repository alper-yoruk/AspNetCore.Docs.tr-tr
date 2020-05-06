---
title: 'Öğretici: EF Core ile geçiş özelliğini kullanma-ASP.NET MVC'
description: Bu öğreticide, ASP.NET Core MVC uygulamasındaki veri modeli değişikliklerini yönetmek için EF Core geçişleri özelliğini kullanmaya başlayabilirsiniz.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: data/ef-mvc/migrations
ms.openlocfilehash: b8701687d97f5fe940e2f39fca9c3f98052660be
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82773529"
---
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a>Öğretici: EF Core ile geçiş özelliğini kullanma-ASP.NET MVC

Bu öğreticide, veri modeli değişikliklerini yönetmek için EF Core geçişleri özelliğini kullanmaya başlayabilirsiniz. Sonraki öğreticilerde, veri modelini değiştirirken daha fazla geçiş ekleyeceksiniz.

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Geçişler hakkında bilgi edinin
> * Bağlantı dizesini değiştirme
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

## <a name="change-the-connection-string"></a>Bağlantı dizesini değiştirme

*AppSettings. JSON* dosyasında, bağlantı dizesindeki veritabanının adını ContosoUniversity2 veya kullandığınız bilgisayarda kullanmadığınız başka bir ad olarak değiştirin.

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

Bu değişiklik projeyi ilk geçişin yeni bir veritabanı oluşturacak şekilde ayarlar. Bu, geçişleri kullanmaya başlamak için gerekli değildir, ancak daha sonra iyi bir fikir olduğunu göreceksiniz.

> [!NOTE]
> Veritabanı adını değiştirmeye alternatif olarak, veritabanını silebilirsiniz. **SQL Server Nesne Gezgini** (ssox) veya `database drop` CLI komutunu kullanın:
>
> ```dotnetcli
> dotnet ef database drop
> ```
>
> Aşağıdaki bölümde CLı komutlarının nasıl çalıştırılacağı açıklanmaktadır.

## <a name="create-an-initial-migration"></a>İlk geçiş oluşturma

Değişikliklerinizi kaydedin ve projeyi derleyin. Sonra bir komut penceresi açın ve proje klasörüne gidin. Bunu yapmanın hızlı bir yolu aşağıda verilmiştir:

* **Çözüm Gezgini**' de projeye sağ tıklayın ve bağlam menüsünden **klasörü dosya Gezgini 'nde aç** ' ı seçin.

  ![Dosya Gezgini menü öğesinde aç](migrations/_static/open-in-file-explorer.png)

* Adres çubuğuna "cmd" yazın ve ENTER tuşuna basın.

  ![Komut penceresini aç](migrations/_static/open-command-window.png)

Komut penceresine aşağıdaki komutu girin:

```dotnetcli
dotnet ef migrations add InitialCreate
```

Komut penceresinde aşağıdakine benzer bir çıktı görürsünüz:

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> *"DotNet-EF" komutuyla eşleşen yürütülebilir dosya bulunamadı*hata iletisi görürseniz, sorun giderme konusunda yardım için [Bu blog gönderisine](https://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) bakın.

Bir hata iletisi görürseniz "*dosyaya erişilemiyor... ContosoUniversity. dll başka bir işlem tarafından kullanıldığı için.*", Windows sistem tepsisinde IIS Express simgesini bulun ve sağ tıklayın, sonra da **contosouniversity > siteyi durdur**' a tıklayın.

## <a name="examine-up-and-down-methods"></a>Yukarı ve aşağı yöntemleri inceleyin

`migrations add` Komutunu ÇALıŞTıRDıĞıNıZDA, EF, veritabanını sıfırdan oluşturacak kodu oluşturmuş olur. Bu kod, * \<zaman damgası>_InitialCreate. cs*adlı dosyada *geçişler* klasöründedir. Sınıfının yöntemi, veri modeli varlık kümelerine karşılık gelen veritabanı tablolarını oluşturur ve aşağıdaki örnekte gösterildiği gibi `Down` yöntemi onları siler. `Up` `InitialCreate`

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

Geçişler, `Up` geçiş için veri modeli değişikliklerini uygulamak üzere yöntemini çağırır. Güncelleştirmeyi geri almak için bir komut girdiğinizde, geçişler `Down` yöntemini çağırır.

Bu kod, `migrations add InitialCreate` komutunu girdiğinizde oluşturulan ilk geçişe yöneliktir. Geçiş adı parametresi (örnekteki "ınitialcreate") dosya adı için kullanılır ve istediğiniz her şey olabilir. Geçiş sırasında nelerin yapıldığını özetleyen bir sözcük veya tümcecik seçmek en iyisidir. Örneğin, "AddDepartmentTable" adlı bir geçişe daha sonra ad yazabilirsiniz.

Veritabanı zaten mevcut olduğunda ilk geçişi oluşturduysanız veritabanı oluşturma kodu oluşturulur, ancak veritabanı veri modeliyle zaten eşleştiğinden çalıştırması gerekmez. Uygulamayı, veritabanının mevcut olmadığı başka bir ortama dağıttığınızda, bu kod veritabanınızı oluşturmak için çalışır, bu nedenle ilk önce test etmek iyi bir fikirdir. Bu nedenle, bağlantı dizesinde veritabanının adını daha önce değiştirmiş olursunuz. böylece geçişler sıfırdan yeni bir tane oluşturabilir.

## <a name="the-data-model-snapshot"></a>Veri modeli anlık görüntüsü

Geçişler, *geçiş/SchoolContextModelSnapshot. cs*içinde geçerli veritabanı şemasının bir *anlık görüntüsünü* oluşturur. Bir geçiş eklediğinizde EF, veri modeli Snapshot dosyası ile karşılaştırılarak nelerin değiştirildiğini belirler.

Bir geçişi kaldırmak için [DotNet EF geçişleri kaldır](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) komutunu kullanın. `dotnet ef migrations remove`geçişi siler ve anlık görüntünün doğru şekilde sıfırlanmasını sağlar. `dotnet ef migrations remove` Başarısız olursa, hata `dotnet ef migrations remove -v` hakkında daha fazla bilgi almak için kullanın.

Anlık görüntü dosyasının nasıl kullanıldığı hakkında daha fazla bilgi için bkz. [Takım ortamlarında EF Core geçişleri](/ef/core/managing-schemas/migrations/teams) .

## <a name="apply-the-migration"></a>Geçişi Uygula

Komut penceresinde, veritabanı ve tabloları oluşturmak için aşağıdaki komutu girin.

```dotnetcli
dotnet ef database update
```

Komutun çıktısı, veritabanının ayarlandığı SQL komutlarının günlüklerini görbelirtilmedikçe, `migrations add` komuta benzer. Günlüklerin çoğu aşağıdaki örnek çıktıda atlanır. Günlük iletilerinde bu ayrıntı düzeyini görmemeyi tercih ediyorsanız, appSettings 'de günlük düzeyini değiştirebilirsiniz *. Development. JSON* dosyası. Daha fazla bilgi için bkz. <xref:fundamentals/logging/index>.

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
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
      VALUES (N'20190327172701_InitialCreate', N'2.2.0-rtm-35687');
Done.
```

İlk öğreticide yaptığınız gibi veritabanını incelemek için **SQL Server Nesne Gezgini** kullanın.  Hangi geçişlerin veritabanına uygulandığını izleyen bir \_ \_EFMigrationsHistory tablosunun eklenmesini fark edeceksiniz. Bu tablodaki verileri görüntüleyin ve ilk geçiş için bir satır görürsünüz. (Önceki CLı çıkış örneğinde son oturum, bu satırı oluşturan INSERT ifadesini gösterir.)

Her şeyin daha önce olduğu gibi çalıştığını doğrulamak için uygulamayı çalıştırın.

![Öğrenciler Dizin sayfası](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a>CLı ve PMC karşılaştırması

Geçişleri yönetmek için EF araçları, .NET Core CLI komutlardan veya Visual Studio **Paket Yöneticisi konsolu** (PMC) penceresindeki PowerShell cmdlet 'lerinde bulunabilir. Bu öğreticide, CLı 'nın nasıl kullanılacağı gösterilmektedir, ancak isterseniz PMC 'yi kullanabilirsiniz.

PMC komutlarına yönelik EF komutları [Microsoft. EntityFrameworkCore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) paketidir. Bu paket [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunur, bu nedenle uygulamanıza yönelik `Microsoft.AspNetCore.App`bir paket başvurusu varsa bir paket başvurusu eklemeniz gerekmez.

**Önemli:** Bu, *. csproj* dosyasını düzenleyerek CLI için yüklediğiniz paket ile aynı değildir. Bu adın adı `Tools`Ile biten CLI paketi adından farklı olarak `Tools.DotNet`.

CLı komutları hakkında daha fazla bilgi için bkz. [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).

PMC komutları hakkında daha fazla bilgi için bkz. [Paket Yöneticisi Konsolu (Visual Studio)](/ef/core/miscellaneous/cli/powershell).

## <a name="get-the-code"></a>Kodu alma

[Tamamlanmış uygulamayı indirin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a>Sonraki adım

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Geçişler hakkında öğrenildi
> * NuGet geçiş paketleri hakkında bilgi edinildi
> * Bağlantı dizesi değiştirildi
> * İlk geçiş oluşturuldu
> * Yukarı ve aşağı yöntemleri İnceleme
> * Veri modeli anlık görüntüsü hakkında bilgi edinildi
> * Geçiş uygulandı

Veri modelini genişletme hakkında daha gelişmiş konulara bakmak için sonraki öğreticiye ilerleyin. Ek geçişler oluşturma ve uygulama gibi.

> [!div class="nextstepaction"]
> [Ek geçişler oluşturma ve uygulama](complex-data-model.md)
