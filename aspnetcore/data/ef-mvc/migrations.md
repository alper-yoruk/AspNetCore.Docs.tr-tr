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
# <a name="tutorial-part-5-apply-migrations-to-the-contoso-university-sample"></a><span data-ttu-id="dee24-104">Öğretici: 5. kısım, Contoso Üniversitesi örneğine geçişler uygulama</span><span class="sxs-lookup"><span data-stu-id="dee24-104">Tutorial: Part 5, apply migrations to the Contoso University sample</span></span>

<span data-ttu-id="dee24-105">Bu öğreticide, veri modeli değişikliklerini yönetmek için EF Core geçişleri özelliğini kullanmaya başlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="dee24-105">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="dee24-106">Sonraki öğreticilerde, veri modelini değiştirirken daha fazla geçiş ekleyeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="dee24-106">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="dee24-107">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="dee24-107">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="dee24-108">Geçişler hakkında bilgi edinin</span><span class="sxs-lookup"><span data-stu-id="dee24-108">Learn about migrations</span></span>
> * <span data-ttu-id="dee24-109">İlk geçiş oluşturma</span><span class="sxs-lookup"><span data-stu-id="dee24-109">Create an initial migration</span></span>
> * <span data-ttu-id="dee24-110">Yukarı ve aşağı yöntemleri inceleyin</span><span class="sxs-lookup"><span data-stu-id="dee24-110">Examine Up and Down methods</span></span>
> * <span data-ttu-id="dee24-111">Veri modeli anlık görüntüsü hakkında bilgi edinin</span><span class="sxs-lookup"><span data-stu-id="dee24-111">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="dee24-112">Geçişi Uygula</span><span class="sxs-lookup"><span data-stu-id="dee24-112">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="dee24-113">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="dee24-113">Prerequisites</span></span>

* [<span data-ttu-id="dee24-114">Sıralama, filtreleme ve sayfalama</span><span class="sxs-lookup"><span data-stu-id="dee24-114">Sorting, filtering, and paging</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="dee24-115">Geçişler hakkında</span><span class="sxs-lookup"><span data-stu-id="dee24-115">About migrations</span></span>

<span data-ttu-id="dee24-116">Yeni bir uygulama geliştirirken, veri modeliniz sıklıkla değişir ve model her değiştiğinde veritabanıyla eşitlenmemiş olur.</span><span class="sxs-lookup"><span data-stu-id="dee24-116">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="dee24-117">Mevcut değilse veritabanını oluşturmak için Entity Framework yapılandırarak bu öğreticileri başlatmış olursunuz.</span><span class="sxs-lookup"><span data-stu-id="dee24-117">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="dee24-118">Veri modelini her değiştirdiğinizde (varlık sınıfları ekleyin, kaldırın veya değiştirin ya da DbContext sınıfınızı değiştirin); veritabanını silebilir ve EF, modelle eşleşen yeni bir tane oluşturur ve test verileriyle birlikte olur.</span><span class="sxs-lookup"><span data-stu-id="dee24-118">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="dee24-119">Veritabanını veri modeliyle eşitlenmiş halde tutma yöntemi, uygulamayı üretime dağıtana kadar iyi çalışır.</span><span class="sxs-lookup"><span data-stu-id="dee24-119">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="dee24-120">Uygulama üretimde çalışırken, genellikle tutmak istediğiniz verileri saklar ve yeni sütun ekleme gibi her değişiklik yaptığınızda her şeyi kaybetmek istemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="dee24-120">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="dee24-121">EF Core geçişleri özelliği, yeni bir veritabanı oluşturmak yerine EF 'in veritabanı şemasını güncelleştirmesine olanak sağlayarak bu sorunu çözer.</span><span class="sxs-lookup"><span data-stu-id="dee24-121">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

<span data-ttu-id="dee24-122">Geçişlerle çalışmak için **Paket Yöneticisi konsolu 'nu** (PMC) veya CLI 'yi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="dee24-122">To work with migrations, you can use the **Package Manager Console** (PMC) or the CLI.</span></span>  <span data-ttu-id="dee24-123">Bu öğreticiler CLı komutlarının nasıl kullanılacağını göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="dee24-123">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="dee24-124">PMC hakkındaki bilgiler [Bu öğreticinin sonunda](#pmc).</span><span class="sxs-lookup"><span data-stu-id="dee24-124">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="dee24-125">Veritabanını bırak</span><span class="sxs-lookup"><span data-stu-id="dee24-125">Drop the database</span></span>

<span data-ttu-id="dee24-126">Veritabanını silin.</span><span class="sxs-lookup"><span data-stu-id="dee24-126">Delete the database.</span></span> <span data-ttu-id="dee24-127">**SQL Server Nesne Gezgini** (ssox) veya `database drop` CLI komutunu kullanın:</span><span class="sxs-lookup"><span data-stu-id="dee24-127">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>

 ```dotnetcli
 dotnet ef database drop
 ```

<span data-ttu-id="dee24-128">Aşağıdaki bölümde CLı komutlarının nasıl çalıştırılacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="dee24-128">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="dee24-129">İlk geçiş oluşturma</span><span class="sxs-lookup"><span data-stu-id="dee24-129">Create an initial migration</span></span>

<span data-ttu-id="dee24-130">Değişikliklerinizi kaydedin ve projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="dee24-130">Save your changes and build the project.</span></span> <span data-ttu-id="dee24-131">Sonra bir komut penceresi açın ve proje klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="dee24-131">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="dee24-132">Bunu yapmanın hızlı bir yolu aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="dee24-132">Here's a quick way to do that:</span></span>

* <span data-ttu-id="dee24-133">**Çözüm Gezgini**' de projeye sağ tıklayın ve bağlam menüsünden **klasörü dosya Gezgini 'nde aç** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="dee24-133">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![Dosya Gezgini menü öğesinde aç](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="dee24-135">Adres çubuğuna "cmd" yazın ve ENTER tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="dee24-135">Enter "cmd" in the address bar and press Enter.</span></span>

  ![Komut penceresini aç](migrations/_static/open-command-window.png)

<span data-ttu-id="dee24-137">Komut penceresine aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="dee24-137">Enter the following command in the command window:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="dee24-138">`dotnet tool install --global dotnet-ef``dotnet ef` [genel bir araç](/ef/core/miscellaneous/cli/dotnet)olarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="dee24-138">`dotnet tool install --global dotnet-ef` installs `dotnet ef` as a [global tool](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="dee24-139">Yukarıdaki komutlarda aşağıdakine benzer bir çıktı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="dee24-139">In the preceding commands, output similar to the following is displayed:</span></span>

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

<span data-ttu-id="dee24-140">"*Başka bir işlem tarafından kullanıldığından," dosya... ContosoUniversity.dll dosyasına erişilemiyor*. ", Windows Sistem tepsisindeki IIS Express simgesini bulun ve sağ tıklayın, sonra da **Contosouniversity > siteyi durdur**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="dee24-140">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="dee24-141">Yukarı ve aşağı yöntemleri inceleyin</span><span class="sxs-lookup"><span data-stu-id="dee24-141">Examine Up and Down methods</span></span>

<span data-ttu-id="dee24-142">`migrations add`Komutunu çalıştırdığınızda, EF, veritabanını sıfırdan oluşturacak kodu oluşturmuş olur.</span><span class="sxs-lookup"><span data-stu-id="dee24-142">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="dee24-143">Bu kod, *\<timestamp> _InitialCreate. cs* adlı dosyadaki *geçişler* klasöründedir.</span><span class="sxs-lookup"><span data-stu-id="dee24-143">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="dee24-144">`Up`Sınıfının yöntemi, `InitialCreate` veri modeli varlık kümelerine karşılık gelen veritabanı tablolarını oluşturur ve `Down` Aşağıdaki örnekte gösterildiği gibi yöntemi onları siler.</span><span class="sxs-lookup"><span data-stu-id="dee24-144">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="dee24-145">Geçişler, `Up` geçiş için veri modeli değişikliklerini uygulamak üzere yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="dee24-145">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="dee24-146">Güncelleştirmeyi geri almak için bir komut girdiğinizde, geçişler `Down` yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="dee24-146">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="dee24-147">Bu kod, komutunu girdiğinizde oluşturulan ilk geçişe yöneliktir `migrations add InitialCreate` .</span><span class="sxs-lookup"><span data-stu-id="dee24-147">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="dee24-148">Geçiş adı parametresi (örnekteki "ınitialcreate") dosya adı için kullanılır ve istediğiniz her şey olabilir.</span><span class="sxs-lookup"><span data-stu-id="dee24-148">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="dee24-149">Geçiş sırasında nelerin yapıldığını özetleyen bir sözcük veya tümcecik seçmek en iyisidir.</span><span class="sxs-lookup"><span data-stu-id="dee24-149">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="dee24-150">Örneğin, "AddDepartmentTable" adlı bir geçişe daha sonra ad yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="dee24-150">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="dee24-151">Veritabanı zaten mevcut olduğunda ilk geçişi oluşturduysanız veritabanı oluşturma kodu oluşturulur, ancak veritabanı veri modeliyle zaten eşleştiğinden çalıştırması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="dee24-151">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="dee24-152">Uygulamayı, veritabanının mevcut olmadığı başka bir ortama dağıttığınızda, bu kod veritabanınızı oluşturmak için çalışır, bu nedenle ilk önce test etmek iyi bir fikirdir.</span><span class="sxs-lookup"><span data-stu-id="dee24-152">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="dee24-153">Bu nedenle, bağlantı dizesinde veritabanının adını daha önce değiştirmiş olursunuz. böylece geçişler sıfırdan yeni bir tane oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="dee24-153">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="dee24-154">Veri modeli anlık görüntüsü</span><span class="sxs-lookup"><span data-stu-id="dee24-154">The data model snapshot</span></span>

<span data-ttu-id="dee24-155">Geçişler, *geçiş/SchoolContextModelSnapshot. cs* içinde geçerli veritabanı şemasının bir *anlık görüntüsünü* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="dee24-155">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="dee24-156">Bir geçiş eklediğinizde EF, veri modeli Snapshot dosyası ile karşılaştırılarak nelerin değiştirildiğini belirler.</span><span class="sxs-lookup"><span data-stu-id="dee24-156">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="dee24-157">Bir geçişi kaldırmak için [DotNet EF geçişleri kaldır](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) komutunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="dee24-157">Use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command to remove a migration.</span></span> <span data-ttu-id="dee24-158">`dotnet ef migrations remove` geçişi siler ve anlık görüntünün doğru şekilde sıfırlanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="dee24-158">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="dee24-159">`dotnet ef migrations remove`Başarısız olursa, `dotnet ef migrations remove -v` hata hakkında daha fazla bilgi almak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="dee24-159">If `dotnet ef migrations remove` fails, use `dotnet ef migrations remove -v` to get more information on the failure.</span></span>

<span data-ttu-id="dee24-160">Anlık görüntü dosyasının nasıl kullanıldığı hakkında daha fazla bilgi için bkz. [Takım ortamlarında EF Core geçişleri](/ef/core/managing-schemas/migrations/teams) .</span><span class="sxs-lookup"><span data-stu-id="dee24-160">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="dee24-161">Geçişi Uygula</span><span class="sxs-lookup"><span data-stu-id="dee24-161">Apply the migration</span></span>

<span data-ttu-id="dee24-162">Komut penceresinde, veritabanı ve tabloları oluşturmak için aşağıdaki komutu girin.</span><span class="sxs-lookup"><span data-stu-id="dee24-162">In the command window, enter the following command to create the database and tables in it.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="dee24-163">Komutun çıktısı, `migrations add` veritabanının AYARLANDıĞı SQL komutlarının günlüklerini görbelirtilmedikçe, komuta benzer.</span><span class="sxs-lookup"><span data-stu-id="dee24-163">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="dee24-164">Günlüklerin çoğu aşağıdaki örnek çıktıda atlanır.</span><span class="sxs-lookup"><span data-stu-id="dee24-164">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="dee24-165">Günlük iletilerinde bu ayrıntı düzeyini görmemeyi tercih ediyorsanız, dosyadaki *appsettings.Development.js* günlük düzeyini değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="dee24-165">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="dee24-166">Daha fazla bilgi için bkz. <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="dee24-166">For more information, see <xref:fundamentals/logging/index>.</span></span>

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

<span data-ttu-id="dee24-167">İlk öğreticide yaptığınız gibi veritabanını incelemek için **SQL Server Nesne Gezgini** kullanın.</span><span class="sxs-lookup"><span data-stu-id="dee24-167">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="dee24-168">\_ \_ Hangi geçişlerin veritabanına uygulandığını izleyen bir EFMigrationsHistory tablosunun eklenmesini fark edeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="dee24-168">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="dee24-169">Bu tablodaki verileri görüntüleyin ve ilk geçiş için bir satır görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="dee24-169">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="dee24-170">(Önceki CLı çıkış örneğinde son oturum, bu satırı oluşturan INSERT ifadesini gösterir.)</span><span class="sxs-lookup"><span data-stu-id="dee24-170">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="dee24-171">Her şeyin daha önce olduğu gibi çalıştığını doğrulamak için uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="dee24-171">Run the application to verify that everything still works the same as before.</span></span>

![Öğrenciler Dizin sayfası](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="dee24-173">CLı ve PMC karşılaştırması</span><span class="sxs-lookup"><span data-stu-id="dee24-173">Compare CLI and PMC</span></span>

<span data-ttu-id="dee24-174">Geçişleri yönetmek için EF araçları, .NET Core CLI komutlardan veya Visual Studio **Paket Yöneticisi konsolu** (PMC) penceresindeki PowerShell cmdlet 'lerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="dee24-174">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="dee24-175">Bu öğreticide, CLı 'nın nasıl kullanılacağı gösterilmektedir, ancak isterseniz PMC 'yi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="dee24-175">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="dee24-176">PMC komutlarına yönelik EF komutları [Microsoft. EntityFrameworkCore. Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) paketidir.</span><span class="sxs-lookup"><span data-stu-id="dee24-176">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="dee24-177">Bu paket [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunur, bu nedenle uygulamanıza yönelik bir paket başvurusu varsa bir paket başvurusu eklemeniz gerekmez `Microsoft.AspNetCore.App` .</span><span class="sxs-lookup"><span data-stu-id="dee24-177">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="dee24-178">**Önemli:** Bu, *. csproj* dosyasını düzenleyerek CLI için yüklediğiniz paket ile aynı değildir.</span><span class="sxs-lookup"><span data-stu-id="dee24-178">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="dee24-179">Bu adın adı `Tools` ile BITEN CLI paketi adından farklı olarak `Tools.DotNet` .</span><span class="sxs-lookup"><span data-stu-id="dee24-179">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="dee24-180">CLı komutları hakkında daha fazla bilgi için bkz. [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="dee24-180">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="dee24-181">PMC komutları hakkında daha fazla bilgi için bkz. [Paket Yöneticisi Konsolu (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span><span class="sxs-lookup"><span data-stu-id="dee24-181">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="dee24-182">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="dee24-182">Get the code</span></span>

[<span data-ttu-id="dee24-183">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="dee24-183">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples)

## <a name="next-step"></a><span data-ttu-id="dee24-184">Sonraki adım</span><span class="sxs-lookup"><span data-stu-id="dee24-184">Next step</span></span>

<span data-ttu-id="dee24-185">Veri modelini genişletme hakkında daha gelişmiş konulara bakmak için sonraki öğreticiye ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="dee24-185">Advance to the next tutorial to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="dee24-186">Ek geçişler oluşturma ve uygulama gibi.</span><span class="sxs-lookup"><span data-stu-id="dee24-186">Along the way you'll create and apply additional migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="dee24-187">Ek geçişler oluşturma ve uygulama</span><span class="sxs-lookup"><span data-stu-id="dee24-187">Create and apply additional migrations</span></span>](complex-data-model.md)
