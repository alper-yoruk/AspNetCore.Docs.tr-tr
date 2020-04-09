---
title: 'Öğretici: Geçişler özelliğini kullanma - EF Core ile mvc ASP.NET'
description: Bu eğitimde, ASP.NET Core MVC uygulamasında veri modeli değişikliklerini yönetmek için EF Core geçişleri özelliğini kullanmaya başlarsınız.
author: rick-anderson
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/migrations
ms.openlocfilehash: 8b3417205457a5ce5fa16994701a06e2a4d7d350
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78665727"
---
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a>Öğretici: Geçişler özelliğini kullanma - EF Core ile mvc ASP.NET

Bu eğitimde, veri modeli değişikliklerini yönetmek için EF Core geçişleri özelliğini kullanmaya başlarsınız. Daha sonraki öğreticilerde, veri modelini değiştirdikçe daha fazla geçiş eklersiniz.

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Geçişler hakkında bilgi edinin
> * Bağlantı dizesini değiştirme
> * İlk geçiş oluşturma
> * Yukarı ve Aşağı yöntemlerini inceleyin
> * Veri modeli anlık görüntüsü hakkında bilgi edinin
> * Geçişi uygulayın

## <a name="prerequisites"></a>Ön koşullar

* [Sıralama, filtreleme ve sayfalama](sort-filter-page.md)

## <a name="about-migrations"></a>Geçişler hakkında

Yeni bir uygulama geliştirdiğinizde, veri modeliniz sık sık değişir ve model her değiştiğinde veritabanıyla eşitlenir. Bu öğreticileri, yoksa veritabanını oluşturmak üzere Varlık Çerçevesi'ni yapılandırarak başlattınız. Ardından, veri modelini her değiştirdiğinizde -varlık sınıflarını ekle, kaldırveya değiştirin veya DbContext sınıfınızı değiştirin - veritabanını silebilir ve EF modelle eşleşen yeni bir model oluşturur ve bunu test verileriyle tohumlar.

Veritabanını veri modeliyle eşit tutma yöntemi, siz uygulamayı üretime dağıtana kadar iyi çalışır. Uygulama üretimde çalışırken genellikle saklamak istediğiniz verileri depolamaktır ve yeni bir sütun eklemek gibi her değişiklik yaptığınızda her şeyi kaybetmek istemezsiniz. EF Çekirdek Geçişleri özelliği, EF'nin yeni bir veritabanı oluşturmak yerine veritabanı şemasını güncelleştirmesini sağlayarak bu sorunu çözer.

Geçişlerle çalışmak için **Paket Yöneticisi Konsolu** 'nu (PMC) veya CLI'yi kullanabilirsiniz.  Bu öğreticiler CLI komutlarının nasıl kullanılacağını gösterir. PMC hakkında bilgi [bu eğitimin sonunda](#pmc)dır.

## <a name="change-the-connection-string"></a>Bağlantı dizesini değiştirme

*appsettings.json* dosyasında, bağlantı dizesindeki veritabanının adını ContosoUniversity2 veya kullanmakta olduğunuz bilgisayarda kullanmadığınız başka bir adla değiştirin.

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

Bu değişiklik, ilk geçişin yeni bir veritabanı oluşturması için projeyi ayarlar. Bu geçişler ile başlamak için gerekli değildir, ancak daha sonra neden iyi bir fikir olduğunu görürsünüz.

> [!NOTE]
> Veritabanı adını değiştirmeye alternatif olarak veritabanını silebilirsiniz. **SQL Server Object Explorer** (SSOX) veya CLI komutunu `database drop` kullanın:
>
> ```dotnetcli
> dotnet ef database drop
> ```
>
> Aşağıdaki bölümde CLI komutları nasıl çalıştırılalır açıklanmaktadır.

## <a name="create-an-initial-migration"></a>İlk geçiş oluşturma

Değişikliklerinizi kaydedin ve projeyi oluşturun. Ardından bir komut penceresi açın ve proje klasörüne gidin. Bunu yapmanın hızlı bir yolu aşağıda vereb:

* **Çözüm Gezgini'nde,** projeyi sağ tıklatın ve bağlam menüsünden **Dosya Gezgini'nde Klasörü Aç'ı** seçin.

  ![Dosya Gezgini menü öğesinde aç](migrations/_static/open-in-file-explorer.png)

* Adres çubuğuna "cmd" girin ve Enter tuşuna basın.

  ![Komut pencereni aç](migrations/_static/open-command-window.png)

Komut penceresine aşağıdaki komutu girin:

```dotnetcli
dotnet ef migrations add InitialCreate
```

Komut penceresinde aşağıdaki gibi çıktı görürsünüz:

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> Bir hata iletisi görürseniz *"dotnet-ef" eşleşen komutu bulunamadı,* yardım sorun giderme için [bu blog gönderisine](https://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) bakın.

Bir hata iletisi görürseniz "*dosyaya erişemiyor... ContosoUniversity.dll çünkü başka bir işlem tarafından kullanılıyor.*", Windows Sistem Tepsisi'nde IIS Express simgesini bulmak ve sağ tıklayın, sonra **ContosoUniversity > Stop Site**tıklayın.

## <a name="examine-up-and-down-methods"></a>Yukarı ve Aşağı yöntemlerini inceleyin

Komutu `migrations add` çalıştırdığınızda, EF veritabanını sıfırdan oluşturacak kodu oluşturdu. Bu kod *Geçişler* klasöründe, zaman * \<damgası>_InitialCreate.cs*adlı dosyada. Sınıfın yöntemi, veri modeli varlık kümelerine karşılık gelen veritabanı tabloları `Down` oluşturur ve yöntem aşağıdaki örnekte gösterildiği gibi bunları siler. `Up` `InitialCreate`

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

Geçişler, `Up` geçiş için veri modeli değişikliklerini uygulamak için yöntemi çağırır. Güncelleştirmeyi geri almak için bir komut girdiğinizde, Geçişler `Down` yöntemi çağırır.

Bu `migrations add InitialCreate` kod, komutu girdiğinizde oluşturulan ilk geçiş içindir. Geçiş adı parametresi (örnekte "InitialCreate" ) dosya adı için kullanılır ve istediğiniz ne olabilir. Geçişte neler yapıldığını özetleyen bir sözcük veya tümcecik seçmek en iyisidir. Örneğin, daha sonraki bir geçiş "AddDepartmentTable" adlandırabilirsiniz.

Veritabanı zaten var olduğunda ilk geçiş oluşturduysanız, veritabanı oluşturma kodu oluşturulur, ancak veritabanı zaten veri modeli eşleşip çalışmaması gerekir. Uygulamayı veritabanının henüz var olmadığı başka bir ortama dağıtırken, bu kod veritabanınızı oluşturmak için çalışır, bu nedenle önce test etmek iyi bir fikirdir. Bu nedenle, geçişlerin sıfırdan yeni bir veritabanı oluşturabilmesi için bağlantı dizesindeki veritabanının adını daha önce değiştirdiniz.

## <a name="the-data-model-snapshot"></a>Veri modeli anlık görüntüsü

*Geçişler, Geçişler/SchoolContextModelSnapshot.cs'deki*geçerli veritabanı şemasının *anlık görüntüsünü* oluşturur. Geçiş eklediğinizde, EF veri modelini anlık görüntü dosyasıyla karşılaştırarak nelerin değiştiğini belirler.

Bir geçişi kaldırmak için [dotnet ef geçişleri kaldır](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) komutunu kullanın. `dotnet ef migrations remove`geçişi siler ve anlık görüntünün doğru şekilde sıfırlanın. Başarısız `dotnet ef migrations remove` olursa, `dotnet ef migrations remove -v` hata hakkında daha fazla bilgi almak için kullanın.

Anlık görüntü dosyasının nasıl kullanıldığı hakkında daha fazla bilgi için [Ekip Ortamlarında EF Çekirdek Geçişleri'ne](/ef/core/managing-schemas/migrations/teams) bakın.

## <a name="apply-the-migration"></a>Geçişi uygulayın

Komut penceresinde, veritabanı ve tablolar oluşturmak için aşağıdaki komutu girin.

```dotnetcli
dotnet ef database update
```

Veritabanını kuran SQL komutlarının `migrations add` günlüklerini görmeniz dışında, komuttan çıkan çıktı komuta benzer. Günlüklerin çoğu aşağıdaki örnek çıktıda atlanır. Günlük iletilerinde bu ayrıntı düzeyini görmemeyi tercih ederseniz, uygulama ayarlarındaki günlük düzeyini *değiştirebilirsiniz. Development.json* dosyası. Daha fazla bilgi için bkz. <xref:fundamentals/logging/index>.

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

Veritabanını ilk öğreticide yaptığınız gibi incelemek için **SQL Server Object Explorer'ı** kullanın.  Veritabanına hangi geçişlerin \_ \_uygulandığını izleyen bir EFMigrationsHistory tablosu nun eklendiğini fark edeceksiniz. Bu tablodaki verileri görüntüleve ilk geçiş için bir satır görürsünüz. (Önceki CLI çıktısı örneğindeki son günlük, bu satırı oluşturan INSERT deyimini gösterir.)

Her şeyin hala eskisi gibi çalıştığını doğrulamak için uygulamayı çalıştırın.

![Öğrenci Endeksi sayfası](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a>CLI ve PMC karşılaştırın

Geçişleri yönetmek için EF aracı .NET Core CLI komutlarından veya Visual Studio **Package Manager Console** (PMC) penceresindeki PowerShell cmdlets'ten edinilebilir. Bu öğretici CLI'nin nasıl kullanılacağını gösterir, ancak isterseniz PMC'yi kullanabilirsiniz.

PMC komutları için EF komutları [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) paketinde yer alır. Bu paket [Microsoft.AspNetCore.App metapaketinde](xref:fundamentals/metapackage-app)yer almaktadır, bu nedenle uygulamanızın bir paket başvurusu varsa `Microsoft.AspNetCore.App`paket başvurusu eklemenize gerek yoktur.

**Önemli:** Bu, *.csproj* dosyasını düzenleyerek CLI için yüklediğiniz paketle aynı paket değildir. Bu bir adı biter `Tools`, cli paket adı `Tools.DotNet`aksine biter .

CLI komutları hakkında daha fazla bilgi için [.NET Core CLI'](/ef/core/miscellaneous/cli/dotnet)ye bakın.

PMC komutları hakkında daha fazla bilgi için [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell)bakın.

## <a name="get-the-code"></a>Kodu alma

[Tamamlanan uygulamayı karşıdan yükleyin veya görüntüleyin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a>Sonraki adım

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Göçler hakkında bilgi edinilmiş
> * NuGet geçiş paketleri hakkında bilgi edinilmiş
> * Bağlantı dizesini değiştirme
> * İlk geçiş oluşturuldu
> * Yukarı ve Aşağı yöntemleri incelendi
> * Veri modeli anlık görüntüsü hakkında bilgi edinilmiş
> * Geçiş uygulandı

Veri modelini genişletme hakkında daha gelişmiş konulara bakmaya başlamak için bir sonraki öğreticiye ilerleyin. Yol boyunca ek geçişler oluşturacak ve uygulayacaksınız.

> [!div class="nextstepaction"]
> [Ek geçişler oluşturma ve uygulama](complex-data-model.md)
