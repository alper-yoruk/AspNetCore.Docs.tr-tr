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
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a><span data-ttu-id="fa6b9-103">Öğretici: Geçişler özelliğini kullanma - EF Core ile mvc ASP.NET</span><span class="sxs-lookup"><span data-stu-id="fa6b9-103">Tutorial: Using the migrations feature - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="fa6b9-104">Bu eğitimde, veri modeli değişikliklerini yönetmek için EF Core geçişleri özelliğini kullanmaya başlarsınız.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-104">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="fa6b9-105">Daha sonraki öğreticilerde, veri modelini değiştirdikçe daha fazla geçiş eklersiniz.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-105">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="fa6b9-106">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="fa6b9-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fa6b9-107">Geçişler hakkında bilgi edinin</span><span class="sxs-lookup"><span data-stu-id="fa6b9-107">Learn about migrations</span></span>
> * <span data-ttu-id="fa6b9-108">Bağlantı dizesini değiştirme</span><span class="sxs-lookup"><span data-stu-id="fa6b9-108">Change the connection string</span></span>
> * <span data-ttu-id="fa6b9-109">İlk geçiş oluşturma</span><span class="sxs-lookup"><span data-stu-id="fa6b9-109">Create an initial migration</span></span>
> * <span data-ttu-id="fa6b9-110">Yukarı ve Aşağı yöntemlerini inceleyin</span><span class="sxs-lookup"><span data-stu-id="fa6b9-110">Examine Up and Down methods</span></span>
> * <span data-ttu-id="fa6b9-111">Veri modeli anlık görüntüsü hakkında bilgi edinin</span><span class="sxs-lookup"><span data-stu-id="fa6b9-111">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="fa6b9-112">Geçişi uygulayın</span><span class="sxs-lookup"><span data-stu-id="fa6b9-112">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fa6b9-113">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="fa6b9-113">Prerequisites</span></span>

* [<span data-ttu-id="fa6b9-114">Sıralama, filtreleme ve sayfalama</span><span class="sxs-lookup"><span data-stu-id="fa6b9-114">Sorting, filtering, and paging</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="fa6b9-115">Geçişler hakkında</span><span class="sxs-lookup"><span data-stu-id="fa6b9-115">About migrations</span></span>

<span data-ttu-id="fa6b9-116">Yeni bir uygulama geliştirdiğinizde, veri modeliniz sık sık değişir ve model her değiştiğinde veritabanıyla eşitlenir.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-116">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="fa6b9-117">Bu öğreticileri, yoksa veritabanını oluşturmak üzere Varlık Çerçevesi'ni yapılandırarak başlattınız.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-117">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="fa6b9-118">Ardından, veri modelini her değiştirdiğinizde -varlık sınıflarını ekle, kaldırveya değiştirin veya DbContext sınıfınızı değiştirin - veritabanını silebilir ve EF modelle eşleşen yeni bir model oluşturur ve bunu test verileriyle tohumlar.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-118">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="fa6b9-119">Veritabanını veri modeliyle eşit tutma yöntemi, siz uygulamayı üretime dağıtana kadar iyi çalışır.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-119">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="fa6b9-120">Uygulama üretimde çalışırken genellikle saklamak istediğiniz verileri depolamaktır ve yeni bir sütun eklemek gibi her değişiklik yaptığınızda her şeyi kaybetmek istemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-120">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="fa6b9-121">EF Çekirdek Geçişleri özelliği, EF'nin yeni bir veritabanı oluşturmak yerine veritabanı şemasını güncelleştirmesini sağlayarak bu sorunu çözer.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-121">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

<span data-ttu-id="fa6b9-122">Geçişlerle çalışmak için **Paket Yöneticisi Konsolu** 'nu (PMC) veya CLI'yi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-122">To work with migrations, you can use the **Package Manager Console** (PMC) or the CLI.</span></span>  <span data-ttu-id="fa6b9-123">Bu öğreticiler CLI komutlarının nasıl kullanılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-123">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="fa6b9-124">PMC hakkında bilgi [bu eğitimin sonunda](#pmc)dır.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-124">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="fa6b9-125">Bağlantı dizesini değiştirme</span><span class="sxs-lookup"><span data-stu-id="fa6b9-125">Change the connection string</span></span>

<span data-ttu-id="fa6b9-126">*appsettings.json* dosyasında, bağlantı dizesindeki veritabanının adını ContosoUniversity2 veya kullanmakta olduğunuz bilgisayarda kullanmadığınız başka bir adla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-126">In the *appsettings.json* file, change the name of the database in the connection string to ContosoUniversity2 or some other name that you haven't used on the computer you're using.</span></span>

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

<span data-ttu-id="fa6b9-127">Bu değişiklik, ilk geçişin yeni bir veritabanı oluşturması için projeyi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-127">This change sets up the project so that the first migration will create a new database.</span></span> <span data-ttu-id="fa6b9-128">Bu geçişler ile başlamak için gerekli değildir, ancak daha sonra neden iyi bir fikir olduğunu görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-128">This isn't required to get started with migrations, but you'll see later why it's a good idea.</span></span>

> [!NOTE]
> <span data-ttu-id="fa6b9-129">Veritabanı adını değiştirmeye alternatif olarak veritabanını silebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-129">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="fa6b9-130">**SQL Server Object Explorer** (SSOX) veya CLI komutunu `database drop` kullanın:</span><span class="sxs-lookup"><span data-stu-id="fa6b9-130">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```dotnetcli
> dotnet ef database drop
> ```
>
> <span data-ttu-id="fa6b9-131">Aşağıdaki bölümde CLI komutları nasıl çalıştırılalır açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-131">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="fa6b9-132">İlk geçiş oluşturma</span><span class="sxs-lookup"><span data-stu-id="fa6b9-132">Create an initial migration</span></span>

<span data-ttu-id="fa6b9-133">Değişikliklerinizi kaydedin ve projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-133">Save your changes and build the project.</span></span> <span data-ttu-id="fa6b9-134">Ardından bir komut penceresi açın ve proje klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-134">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="fa6b9-135">Bunu yapmanın hızlı bir yolu aşağıda vereb:</span><span class="sxs-lookup"><span data-stu-id="fa6b9-135">Here's a quick way to do that:</span></span>

* <span data-ttu-id="fa6b9-136">**Çözüm Gezgini'nde,** projeyi sağ tıklatın ve bağlam menüsünden **Dosya Gezgini'nde Klasörü Aç'ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-136">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![Dosya Gezgini menü öğesinde aç](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="fa6b9-138">Adres çubuğuna "cmd" girin ve Enter tuşuna basın.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-138">Enter "cmd" in the address bar and press Enter.</span></span>

  ![Komut pencereni aç](migrations/_static/open-command-window.png)

<span data-ttu-id="fa6b9-140">Komut penceresine aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="fa6b9-140">Enter the following command in the command window:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="fa6b9-141">Komut penceresinde aşağıdaki gibi çıktı görürsünüz:</span><span class="sxs-lookup"><span data-stu-id="fa6b9-141">You see output like the following in the command window:</span></span>

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> <span data-ttu-id="fa6b9-142">Bir hata iletisi görürseniz *"dotnet-ef" eşleşen komutu bulunamadı,* yardım sorun giderme için [bu blog gönderisine](https://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) bakın.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-142">If you see an error message *No executable found matching command "dotnet-ef"*, see [this blog post](https://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) for help troubleshooting.</span></span>

<span data-ttu-id="fa6b9-143">Bir hata iletisi görürseniz "*dosyaya erişemiyor... ContosoUniversity.dll çünkü başka bir işlem tarafından kullanılıyor.*", Windows Sistem Tepsisi'nde IIS Express simgesini bulmak ve sağ tıklayın, sonra **ContosoUniversity > Stop Site**tıklayın.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-143">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="fa6b9-144">Yukarı ve Aşağı yöntemlerini inceleyin</span><span class="sxs-lookup"><span data-stu-id="fa6b9-144">Examine Up and Down methods</span></span>

<span data-ttu-id="fa6b9-145">Komutu `migrations add` çalıştırdığınızda, EF veritabanını sıfırdan oluşturacak kodu oluşturdu.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-145">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="fa6b9-146">Bu kod *Geçişler* klasöründe, zaman \* \<damgası>_InitialCreate.cs\*adlı dosyada.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-146">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="fa6b9-147">Sınıfın yöntemi, veri modeli varlık kümelerine karşılık gelen veritabanı tabloları `Down` oluşturur ve yöntem aşağıdaki örnekte gösterildiği gibi bunları siler. `Up` `InitialCreate`</span><span class="sxs-lookup"><span data-stu-id="fa6b9-147">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="fa6b9-148">Geçişler, `Up` geçiş için veri modeli değişikliklerini uygulamak için yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-148">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="fa6b9-149">Güncelleştirmeyi geri almak için bir komut girdiğinizde, Geçişler `Down` yöntemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-149">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="fa6b9-150">Bu `migrations add InitialCreate` kod, komutu girdiğinizde oluşturulan ilk geçiş içindir.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-150">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="fa6b9-151">Geçiş adı parametresi (örnekte "InitialCreate" ) dosya adı için kullanılır ve istediğiniz ne olabilir.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-151">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="fa6b9-152">Geçişte neler yapıldığını özetleyen bir sözcük veya tümcecik seçmek en iyisidir.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-152">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="fa6b9-153">Örneğin, daha sonraki bir geçiş "AddDepartmentTable" adlandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-153">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="fa6b9-154">Veritabanı zaten var olduğunda ilk geçiş oluşturduysanız, veritabanı oluşturma kodu oluşturulur, ancak veritabanı zaten veri modeli eşleşip çalışmaması gerekir.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-154">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="fa6b9-155">Uygulamayı veritabanının henüz var olmadığı başka bir ortama dağıtırken, bu kod veritabanınızı oluşturmak için çalışır, bu nedenle önce test etmek iyi bir fikirdir.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-155">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="fa6b9-156">Bu nedenle, geçişlerin sıfırdan yeni bir veritabanı oluşturabilmesi için bağlantı dizesindeki veritabanının adını daha önce değiştirdiniz.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-156">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="fa6b9-157">Veri modeli anlık görüntüsü</span><span class="sxs-lookup"><span data-stu-id="fa6b9-157">The data model snapshot</span></span>

<span data-ttu-id="fa6b9-158">*Geçişler, Geçişler/SchoolContextModelSnapshot.cs'deki*geçerli veritabanı şemasının *anlık görüntüsünü* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-158">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="fa6b9-159">Geçiş eklediğinizde, EF veri modelini anlık görüntü dosyasıyla karşılaştırarak nelerin değiştiğini belirler.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-159">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="fa6b9-160">Bir geçişi kaldırmak için [dotnet ef geçişleri kaldır](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) komutunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-160">Use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command to remove a migration.</span></span> <span data-ttu-id="fa6b9-161">`dotnet ef migrations remove`geçişi siler ve anlık görüntünün doğru şekilde sıfırlanın.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-161">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="fa6b9-162">Başarısız `dotnet ef migrations remove` olursa, `dotnet ef migrations remove -v` hata hakkında daha fazla bilgi almak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-162">If `dotnet ef migrations remove` fails, use `dotnet ef migrations remove -v` to get more information on the failure.</span></span>

<span data-ttu-id="fa6b9-163">Anlık görüntü dosyasının nasıl kullanıldığı hakkında daha fazla bilgi için [Ekip Ortamlarında EF Çekirdek Geçişleri'ne](/ef/core/managing-schemas/migrations/teams) bakın.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-163">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="fa6b9-164">Geçişi uygulayın</span><span class="sxs-lookup"><span data-stu-id="fa6b9-164">Apply the migration</span></span>

<span data-ttu-id="fa6b9-165">Komut penceresinde, veritabanı ve tablolar oluşturmak için aşağıdaki komutu girin.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-165">In the command window, enter the following command to create the database and tables in it.</span></span>

```dotnetcli
dotnet ef database update
```

<span data-ttu-id="fa6b9-166">Veritabanını kuran SQL komutlarının `migrations add` günlüklerini görmeniz dışında, komuttan çıkan çıktı komuta benzer.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-166">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="fa6b9-167">Günlüklerin çoğu aşağıdaki örnek çıktıda atlanır.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-167">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="fa6b9-168">Günlük iletilerinde bu ayrıntı düzeyini görmemeyi tercih ederseniz, uygulama ayarlarındaki günlük düzeyini *değiştirebilirsiniz. Development.json* dosyası.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-168">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="fa6b9-169">Daha fazla bilgi için bkz. <xref:fundamentals/logging/index>.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-169">For more information, see <xref:fundamentals/logging/index>.</span></span>

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

<span data-ttu-id="fa6b9-170">Veritabanını ilk öğreticide yaptığınız gibi incelemek için **SQL Server Object Explorer'ı** kullanın.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-170">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="fa6b9-171">Veritabanına hangi geçişlerin \_ \_uygulandığını izleyen bir EFMigrationsHistory tablosu nun eklendiğini fark edeceksiniz.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-171">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="fa6b9-172">Bu tablodaki verileri görüntüleve ilk geçiş için bir satır görürsünüz.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-172">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="fa6b9-173">(Önceki CLI çıktısı örneğindeki son günlük, bu satırı oluşturan INSERT deyimini gösterir.)</span><span class="sxs-lookup"><span data-stu-id="fa6b9-173">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="fa6b9-174">Her şeyin hala eskisi gibi çalıştığını doğrulamak için uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-174">Run the application to verify that everything still works the same as before.</span></span>

![Öğrenci Endeksi sayfası](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="fa6b9-176">CLI ve PMC karşılaştırın</span><span class="sxs-lookup"><span data-stu-id="fa6b9-176">Compare CLI and PMC</span></span>

<span data-ttu-id="fa6b9-177">Geçişleri yönetmek için EF aracı .NET Core CLI komutlarından veya Visual Studio **Package Manager Console** (PMC) penceresindeki PowerShell cmdlets'ten edinilebilir.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-177">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="fa6b9-178">Bu öğretici CLI'nin nasıl kullanılacağını gösterir, ancak isterseniz PMC'yi kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-178">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="fa6b9-179">PMC komutları için EF komutları [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) paketinde yer alır.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-179">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="fa6b9-180">Bu paket [Microsoft.AspNetCore.App metapaketinde](xref:fundamentals/metapackage-app)yer almaktadır, bu nedenle uygulamanızın bir paket başvurusu varsa `Microsoft.AspNetCore.App`paket başvurusu eklemenize gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-180">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="fa6b9-181">**Önemli:** Bu, *.csproj* dosyasını düzenleyerek CLI için yüklediğiniz paketle aynı paket değildir.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-181">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="fa6b9-182">Bu bir adı biter `Tools`, cli paket adı `Tools.DotNet`aksine biter .</span><span class="sxs-lookup"><span data-stu-id="fa6b9-182">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="fa6b9-183">CLI komutları hakkında daha fazla bilgi için [.NET Core CLI'](/ef/core/miscellaneous/cli/dotnet)ye bakın.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-183">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="fa6b9-184">PMC komutları hakkında daha fazla bilgi için [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell)bakın.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-184">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="fa6b9-185">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="fa6b9-185">Get the code</span></span>

[<span data-ttu-id="fa6b9-186">Tamamlanan uygulamayı karşıdan yükleyin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-186">Download or view the completed application.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a><span data-ttu-id="fa6b9-187">Sonraki adım</span><span class="sxs-lookup"><span data-stu-id="fa6b9-187">Next step</span></span>

<span data-ttu-id="fa6b9-188">Bu öğreticide şunları yaptınız:</span><span class="sxs-lookup"><span data-stu-id="fa6b9-188">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fa6b9-189">Göçler hakkında bilgi edinilmiş</span><span class="sxs-lookup"><span data-stu-id="fa6b9-189">Learned about migrations</span></span>
> * <span data-ttu-id="fa6b9-190">NuGet geçiş paketleri hakkında bilgi edinilmiş</span><span class="sxs-lookup"><span data-stu-id="fa6b9-190">Learned about NuGet migration packages</span></span>
> * <span data-ttu-id="fa6b9-191">Bağlantı dizesini değiştirme</span><span class="sxs-lookup"><span data-stu-id="fa6b9-191">Changed the connection string</span></span>
> * <span data-ttu-id="fa6b9-192">İlk geçiş oluşturuldu</span><span class="sxs-lookup"><span data-stu-id="fa6b9-192">Created an initial migration</span></span>
> * <span data-ttu-id="fa6b9-193">Yukarı ve Aşağı yöntemleri incelendi</span><span class="sxs-lookup"><span data-stu-id="fa6b9-193">Examined Up and Down methods</span></span>
> * <span data-ttu-id="fa6b9-194">Veri modeli anlık görüntüsü hakkında bilgi edinilmiş</span><span class="sxs-lookup"><span data-stu-id="fa6b9-194">Learned about the data model snapshot</span></span>
> * <span data-ttu-id="fa6b9-195">Geçiş uygulandı</span><span class="sxs-lookup"><span data-stu-id="fa6b9-195">Applied the migration</span></span>

<span data-ttu-id="fa6b9-196">Veri modelini genişletme hakkında daha gelişmiş konulara bakmaya başlamak için bir sonraki öğreticiye ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-196">Advance to the next tutorial to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="fa6b9-197">Yol boyunca ek geçişler oluşturacak ve uygulayacaksınız.</span><span class="sxs-lookup"><span data-stu-id="fa6b9-197">Along the way you'll create and apply additional migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="fa6b9-198">Ek geçişler oluşturma ve uygulama</span><span class="sxs-lookup"><span data-stu-id="fa6b9-198">Create and apply additional migrations</span></span>](complex-data-model.md)
