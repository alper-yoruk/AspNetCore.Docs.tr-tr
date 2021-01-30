---
title: Razor ASP.NET Core Entity Framework Core olan sayfalar-öğretici 1/8
author: rick-anderson
description: RazorEntity Framework Core kullanarak bir sayfalar uygulamasının nasıl oluşturulacağını gösterir
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
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
uid: data/ef-rp/intro
ms.openlocfilehash: 2f6299511b568a70c638dd5d4c735bf22adb5e95
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057439"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="95499-103">Razor ASP.NET Core Entity Framework Core olan sayfalar-öğretici 1/8</span><span class="sxs-lookup"><span data-stu-id="95499-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="95499-104">, [Tom Dykstra](https://github.com/tdykstra) ve [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından</span><span class="sxs-lookup"><span data-stu-id="95499-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="95499-105">Bu, bir [ASP.NET Core Razor sayfaları](xref:razor-pages/index) UYGULAMASıNDA Entity Framework (EF) çekirdeğini nasıl kullanacağınızı gösteren bir öğretici serisinin ilkisidir.</span><span class="sxs-lookup"><span data-stu-id="95499-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="95499-106">Öğreticiler, kurgusal bir Contoso Üniversitesi için bir Web sitesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="95499-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="95499-107">Site, öğrenci giriş, kurs oluşturma ve eğitmen atamaları gibi işlevleri içerir.</span><span class="sxs-lookup"><span data-stu-id="95499-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="95499-108">Öğretici ilk kod yaklaşımını kullanır.</span><span class="sxs-lookup"><span data-stu-id="95499-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="95499-109">Bu öğreticiyi veritabanı ilk yaklaşımı kullanarak takip eden bilgiler için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/16897)bakın.</span><span class="sxs-lookup"><span data-stu-id="95499-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="95499-110">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="95499-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="95499-111">[Yönergeleri indirin](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="95499-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="95499-112">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="95499-112">Prerequisites</span></span>

* <span data-ttu-id="95499-113">Sayfalarla yeni başladıysanız Razor , bunu başlatmadan önce [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) öğreticisini izleyin.</span><span class="sxs-lookup"><span data-stu-id="95499-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95499-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95499-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="95499-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95499-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="95499-116">Veritabanı altyapıları</span><span class="sxs-lookup"><span data-stu-id="95499-116">Database engines</span></span>

<span data-ttu-id="95499-117">Visual Studio yönergeleri, yalnızca Windows üzerinde çalışan bir SQL Server Express sürümü olan [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)'yi kullanır.</span><span class="sxs-lookup"><span data-stu-id="95499-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="95499-118">Visual Studio Code yönergeler, platformlar arası bir veritabanı altyapısı olan [SQLite](https://www.sqlite.org/)kullanır.</span><span class="sxs-lookup"><span data-stu-id="95499-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="95499-119">SQLite kullanmayı seçerseniz, SQLite [Için DB tarayıcısı](https://sqlitebrowser.org/)gibi bir SQLite veritabanını yönetmek ve görüntülemek için üçüncü taraf bir araç indirip yükleyin.</span><span class="sxs-lookup"><span data-stu-id="95499-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="95499-120">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="95499-120">Troubleshooting</span></span>

<span data-ttu-id="95499-121">Giderebileceğiniz bir sorunla karşılaşırsanız, kodunuzu [Tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="95499-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="95499-122">Yardım almanın iyi bir yolu, [ASP.NET Core etiketi](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF Core etiketi](https://stackoverflow.com/questions/tagged/entity-framework-core)kullanılarak StackOverflow.com 'e bir soru göndererek.</span><span class="sxs-lookup"><span data-stu-id="95499-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="95499-123">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="95499-123">The sample app</span></span>

<span data-ttu-id="95499-124">Bu öğreticilerde oluşturulan uygulama, temel bir üniversite web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="95499-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="95499-125">Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="95499-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="95499-126">Öğreticide oluşturulan ekranlardan bazıları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="95499-126">Here are a few of the screens created in the tutorial.</span></span>

![Öğrenciler Dizin sayfası](intro/_static/students-index30.png)

![Öğrenciler düzenleme sayfası](intro/_static/student-edit30.png)

<span data-ttu-id="95499-129">Bu sitenin kullanıcı arabirimi stili yerleşik proje şablonlarına dayalıdır.</span><span class="sxs-lookup"><span data-stu-id="95499-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="95499-130">Öğreticinin odağı, Kullanıcı arabirimini nasıl özelleştireceğinizi değil, ASP.NET Core EF Core nasıl kullanacağınızı kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="95499-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

<!-- 
Follow the link at the top of the page to get the source code for the completed project. The *cu50* folder has the code for the ASP.NET Core 5.0 version of the tutorial. Files that reflect the state of the code for tutorials 1-7 can be found in the *cu50snapshots* folder.

# [Visual Studio](#tab/visual-studio)

To run the app after downloading the completed project:

* Build the project.
* In Package Manager Console (PMC) run the following command:

  ```powershell
  Update-Database
  ```

* Run the project to seed the database.

# [Visual Studio Code](#tab/visual-studio-code)

To run the app after downloading the completed project:

* In *Program.cs*, remove the comments from `// webBuilder.UseStartup<StartupSQLite>();`  so `StartupSQLite` is used.
* Copy the contents of *appSettingsSQLite.json* into *appSettings.json*.
* Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.
* Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.
* Build the project.
* At a command prompt in the project folder, run the following commands:

  ```dotnetcli
  dotnet tool install --global dotnet-ef -v 5.0.0-*
  dotnet ef database update
  ```

* In your SQLite tool, run the following SQL statement:

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* Run the project to seed the database.

---

-->

## <a name="create-the-web-app-project"></a><span data-ttu-id="95499-131">Web uygulaması projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="95499-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95499-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95499-132">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="95499-133">Visual Studio 'Yu başlatın ve **Yeni proje oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="95499-133">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="95499-134">**Yeni proje oluştur** iletişim kutusunda **ASP.NET Core Web uygulaması** > **İleri**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="95499-134">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="95499-135">**Yeni projenizi yapılandırın** Iletişim kutusunda `ContosoUniversity` **Proje adı**' nı girin.</span><span class="sxs-lookup"><span data-stu-id="95499-135">In the **Configure your new project** dialog, enter `ContosoUniversity` for **Project name**.</span></span> <span data-ttu-id="95499-136">Büyük harfler de dahil olmak üzere bu tam adı kullanmak önemlidir. bu nedenle, `namespace` Kod kopyalanırken her biri eşleşir.</span><span class="sxs-lookup"><span data-stu-id="95499-136">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="95499-137">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="95499-137">Select **Create**.</span></span>
1. <span data-ttu-id="95499-138">**Yeni ASP.NET Core Web uygulaması oluştur** iletişim kutusunda şunları seçin:</span><span class="sxs-lookup"><span data-stu-id="95499-138">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="95499-139">**.NET Core** ve **ASP.NET Core 5,0** açılır.</span><span class="sxs-lookup"><span data-stu-id="95499-139">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="95499-140">**ASP.NET Core Web uygulaması**.</span><span class="sxs-lookup"><span data-stu-id="95499-140">**ASP.NET Core Web App**.</span></span>
    1. <span data-ttu-id="95499-141"> 
       Oluştur ![ Yeni ASP.NET Core projesi iletişim kutusu](~/data/ef-rp/intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="95499-141">**Create**
![New ASP.NET Core Project dialog](~/data/ef-rp/intro/_static/new-aspnet5.png)</span></span>
    
# <a name="visual-studio-code"></a>[<span data-ttu-id="95499-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95499-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="95499-143">Bir terminalde, proje klasörünün oluşturulması gereken klasöre gidin.</span><span class="sxs-lookup"><span data-stu-id="95499-143">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="95499-144">Bir Razor sayfa projesi ve yeni proje klasörü oluşturmak için aşağıdaki komutları çalıştırın `cd` :</span><span class="sxs-lookup"><span data-stu-id="95499-144">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="95499-145">Site stilini ayarlayın</span><span class="sxs-lookup"><span data-stu-id="95499-145">Set up the site style</span></span>

<span data-ttu-id="95499-146">Aşağıdaki kodu kopyalayıp *Sayfalar/paylaşılan/_Layout. cshtml* dosyasına yapıştırın:</span><span class="sxs-lookup"><span data-stu-id="95499-146">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="95499-147">Düzen dosyası site üst bilgisini, alt bilgisini ve menüsünü ayarlar.</span><span class="sxs-lookup"><span data-stu-id="95499-147">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="95499-148">Yukarıdaki kod aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="95499-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="95499-149">Her "ContosoUniversity" öğesinin "Contoso Üniversitesi" olarak her oluşumu.</span><span class="sxs-lookup"><span data-stu-id="95499-149">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="95499-150">Üç oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="95499-150">There are three occurrences.</span></span>
* <span data-ttu-id="95499-151">**Giriş** ve **Gizlilik** menü girişleri silinir.</span><span class="sxs-lookup"><span data-stu-id="95499-151">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="95499-152">, **Öğrenciler**, **Kurslar**, **eğitmenler** ve **Departmanlar** **için girişler** eklenir.</span><span class="sxs-lookup"><span data-stu-id="95499-152">Entries are added for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="95499-153">*Pages/Index. cshtml* dosyasında, dosyanın içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="95499-153">In *Pages/Index.cshtml*, replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="95499-154">Yukarıdaki kod, ASP.NET Core ile ilgili metnin yerine bu uygulamayla ilgili metni koyar.</span><span class="sxs-lookup"><span data-stu-id="95499-154">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="95499-155">Giriş sayfasının göründüğünü doğrulamak için uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="95499-155">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="95499-156">Veri modeli</span><span class="sxs-lookup"><span data-stu-id="95499-156">The data model</span></span>

<span data-ttu-id="95499-157">Aşağıdaki bölümler bir veri modeli oluşturur:</span><span class="sxs-lookup"><span data-stu-id="95499-157">The following sections create a data model:</span></span>

![Kurs-kayıt-öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

<span data-ttu-id="95499-159">Bir öğrenci herhangi bir sayıda kursa kaydolabilir ve bir kurs, kayıtlı sayıda öğrenciye sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="95499-159">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="95499-160">Öğrenci varlığı</span><span class="sxs-lookup"><span data-stu-id="95499-160">The Student entity</span></span>

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

* <span data-ttu-id="95499-162">Proje klasöründe bir *modeller* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="95499-162">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="95499-163">Aşağıdaki kodla *modeller/öğrenci. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="95499-163">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="95499-164">`ID`Özelliği, bu sınıfa karşılık gelen veritabanı tablosunun birincil anahtar sütunu olur.</span><span class="sxs-lookup"><span data-stu-id="95499-164">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="95499-165">Varsayılan olarak, EF Core `ID` birincil anahtar olarak adlandırılan bir özelliği Yorumlar `classnameID` .</span><span class="sxs-lookup"><span data-stu-id="95499-165">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="95499-166">Bu nedenle birincil anahtar sınıfı için alternatif otomatik olarak tanınan ad `Student` olur `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="95499-166">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="95499-167">Daha fazla bilgi için bkz. [EF Core-anahtarlar](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="95499-167">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="95499-168">`Enrollments`Özelliği bir [Gezinti özelliğidir](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="95499-168">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="95499-169">Gezinti özellikleri, bu varlıkla ilgili diğer varlıkları tutar.</span><span class="sxs-lookup"><span data-stu-id="95499-169">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="95499-170">Bu durumda, `Enrollments` bir `Student` varlığın özelliği `Enrollment` söz konusu öğrenciye ilişkin tüm varlıkları barındırır.</span><span class="sxs-lookup"><span data-stu-id="95499-170">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="95499-171">Örneğin, veritabanındaki bir öğrenci satırında iki ilişkili kayıt satırı varsa, `Enrollments` gezinti özelliği bu Iki kayıt varlığını içerir.</span><span class="sxs-lookup"><span data-stu-id="95499-171">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="95499-172">Veritabanında, bir kayıt satırı, Studentitıd sütunu öğrencinin ID değerini içeriyorsa bir öğrenci satırıyla ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="95499-172">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="95499-173">Örneğin, bir öğrenci satırının ID = 1 olduğunu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="95499-173">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="95499-174">İlgili kayıt satırları Studentitıd = 1 olacaktır.</span><span class="sxs-lookup"><span data-stu-id="95499-174">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="95499-175">Studentitıd, kayıt tablosundaki bir *yabancı anahtardır* .</span><span class="sxs-lookup"><span data-stu-id="95499-175">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="95499-176">`Enrollments`Özelliği, `ICollection<Enrollment>` birden çok ilgili kayıt varlığı olabileceğinden, olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="95499-176">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="95499-177">Veya gibi diğer koleksiyon türlerini kullanabilirsiniz `List<Enrollment>` `HashSet<Enrollment>` .</span><span class="sxs-lookup"><span data-stu-id="95499-177">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="95499-178">Kullanıldığında `ICollection<Enrollment>` , EF Core `HashSet<Enrollment>` Varsayılan olarak bir koleksiyon oluşturur.</span><span class="sxs-lookup"><span data-stu-id="95499-178">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="95499-179">Kayıt varlığı</span><span class="sxs-lookup"><span data-stu-id="95499-179">The Enrollment entity</span></span>

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

<span data-ttu-id="95499-181">Aşağıdaki kodla *modeller/kayıt. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="95499-181">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="95499-182">`EnrollmentID`Bu özellik birincil anahtardır; bu varlık kendi başına değil, `classnameID` kendi modelini kullanır `ID` .</span><span class="sxs-lookup"><span data-stu-id="95499-182">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="95499-183">Bir üretim veri modeli için bir model seçin ve bunu tutarlı bir şekilde kullanın.</span><span class="sxs-lookup"><span data-stu-id="95499-183">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="95499-184">Bu öğretici her ikisinin de yalnızca bir iş olduğunu göstermek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="95499-184">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="95499-185">`ID`Olmadan kullanmak `classname` , bazı veri modeli değişikliklerinin uygulanmasını kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="95499-185">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="95499-186">`Grade`Özelliği bir `enum` .</span><span class="sxs-lookup"><span data-stu-id="95499-186">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="95499-187">Tür bildiriminden sonraki soru işareti, `Grade` `Grade` özelliğin [null yapılabilir](/dotnet/csharp/programming-guide/nullable-types/)olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="95499-187">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="95499-188">Null olan bir sınıf sıfır bir sınıf null değerinden farklıdır &mdash; veya henüz atanmamış olur.</span><span class="sxs-lookup"><span data-stu-id="95499-188">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="95499-189">`StudentID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Student` .</span><span class="sxs-lookup"><span data-stu-id="95499-189">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="95499-190">Bir varlık `Enrollment` bir `Student` varlıkla ilişkilendirilir, bu nedenle özellik tek bir varlık içerir `Student` .</span><span class="sxs-lookup"><span data-stu-id="95499-190">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="95499-191">`CourseID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Course` .</span><span class="sxs-lookup"><span data-stu-id="95499-191">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="95499-192">Bir `Enrollment` varlık bir `Course` varlıkla ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="95499-192">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="95499-193">EF Core bir özelliği, adlandırılmış ise yabancı anahtar olarak yorumlar `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="95499-193">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="95499-194">Örneğin, `StudentID` `Student` `Student` nesnenin birincil anahtarı olduğundan, gezinti özelliği için yabancı anahtardır `ID` .</span><span class="sxs-lookup"><span data-stu-id="95499-194">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="95499-195">Yabancı anahtar özellikleri de adlandırılmış olabilir `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="95499-195">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="95499-196">Örneğin, `CourseID` `Course` varlığın birincil anahtarı olduğundan `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="95499-196">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="95499-197">Kurs varlığı</span><span class="sxs-lookup"><span data-stu-id="95499-197">The Course entity</span></span>

![Kurs varlık diyagramı](intro/_static/course-entity.png)

<span data-ttu-id="95499-199">Aşağıdaki kodla *modeller/kurs. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="95499-199">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="95499-200">`Enrollments`Özelliği bir gezinti özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="95499-200">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="95499-201">Bir `Course` varlık herhangi bir sayıda varlıkla ilişkili olabilir `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="95499-201">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="95499-202">`DatabaseGenerated`Özniteliği, uygulamanın veritabanını oluşturmak yerine birincil anahtarı belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="95499-202">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="95499-203">Derleyici hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="95499-203">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="95499-204">Yapı iskelesi öğrenci sayfaları</span><span class="sxs-lookup"><span data-stu-id="95499-204">Scaffold Student pages</span></span>

<span data-ttu-id="95499-205">Bu bölümde, oluşturmak için ASP.NET Core scafkatlama aracını kullanırsınız:</span><span class="sxs-lookup"><span data-stu-id="95499-205">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="95499-206">EF Core `DbContext` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="95499-206">An EF Core `DbContext` class.</span></span> <span data-ttu-id="95499-207">Bağlam, belirli bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="95499-207">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="95499-208"><xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>Sınıfından türetilir.</span><span class="sxs-lookup"><span data-stu-id="95499-208">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="95499-209">Razor varlık için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemlerini işleyen sayfalar `Student` .</span><span class="sxs-lookup"><span data-stu-id="95499-209">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95499-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95499-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="95499-211">*Sayfalar/öğrenciler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="95499-211">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="95499-212">**Çözüm Gezgini**, *Sayfalar/öğrenciler* klasörüne sağ tıklayın ve  > **yeni yapı iskelesi Ekle öğesini** seçin.</span><span class="sxs-lookup"><span data-stu-id="95499-212">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="95499-213">**Yeni yapı Iskelesi öğesi Ekle** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="95499-213">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="95499-214">Sol sekmede, **yüklü > ortak > Razor sayfaları** ' nı seçin</span><span class="sxs-lookup"><span data-stu-id="95499-214">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="95499-215">**Razor Entity Framework (CRUD) Ekle kullanarak sayfa** seçin > .</span><span class="sxs-lookup"><span data-stu-id="95499-215">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="95499-216">**Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="95499-216">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="95499-217">**Model sınıfı** açılır penceresinde **öğrenci (Contosouniversity. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="95499-217">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="95499-218">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin.</span><span class="sxs-lookup"><span data-stu-id="95499-218">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="95499-219">Veri bağlamı adını yerine End ile değiştirin `SchoolContext` `ContosoUniversityContext` .</span><span class="sxs-lookup"><span data-stu-id="95499-219">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="95499-220">Güncelleştirilmiş bağlam adı: `ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="95499-220">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
    * <span data-ttu-id="95499-221">Veri bağlamı sınıfını eklemeyi bitirmeden **Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="95499-221">Select **Add** to finish adding the data context class.</span></span>
   * <span data-ttu-id="95499-222">**Ekle** ' yi seçerek **Razor sayfa ekle** iletişim kutusunu sona erdirin.</span><span class="sxs-lookup"><span data-stu-id="95499-222">Select **Add** to finish the **Add Razor Pages** dialog.</span></span>

<span data-ttu-id="95499-223">Aşağıdaki paketler otomatik olarak yüklenir:</span><span class="sxs-lookup"><span data-stu-id="95499-223">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="95499-224">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95499-224">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="95499-225">Gerekli NuGet paketlerini yüklemek için aşağıdaki .NET Core CLI komutlarını çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="95499-225">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="95499-226">Yapı iskelesi için Microsoft. VisualStudio. Web. CodeGeneration. Design paketi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="95499-226">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="95499-227">Uygulama SQL Server kullanmayabilse de, scafkatlama aracı SQL Server paketine ihtiyaç duyuyor.</span><span class="sxs-lookup"><span data-stu-id="95499-227">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="95499-228">*Sayfalar/öğrenciler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="95499-228">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="95499-229">[ASPNET-CodeGenerator scafkatlama aracı](xref:fundamentals/tools/dotnet-aspnet-codegenerator)'nı yüklemek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="95499-229">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="95499-230">Fkatlama öğrenci sayfalarını iskele almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="95499-230">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="95499-231">**Windows üzerinde**</span><span class="sxs-lookup"><span data-stu-id="95499-231">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="95499-232">**MacOS veya Linux üzerinde**</span><span class="sxs-lookup"><span data-stu-id="95499-232">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="95499-233">Önceki adım başarısız olursa, projeyi derleyin ve iskele adımını yeniden deneyin.</span><span class="sxs-lookup"><span data-stu-id="95499-233">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="95499-234">Yapı iskelesi işlemi:</span><span class="sxs-lookup"><span data-stu-id="95499-234">The scaffolding process:</span></span>

* <span data-ttu-id="95499-235">Razor *Sayfalar/öğrenciler* klasöründe sayfa oluşturur:</span><span class="sxs-lookup"><span data-stu-id="95499-235">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="95499-236">*. Cshtml* ve *Create.cshtml.cs* oluşturma</span><span class="sxs-lookup"><span data-stu-id="95499-236">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="95499-237">*Delete. cshtml* ve *delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="95499-237">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="95499-238">*Details. cshtml* ve *details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="95499-238">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="95499-239">*. Cshtml* ve *Edit.cshtml.cs* Düzenle</span><span class="sxs-lookup"><span data-stu-id="95499-239">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="95499-240">*Index. cshtml* ve *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="95499-240">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="95499-241">*Data/SchoolContext. cs* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="95499-241">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="95499-242">*Startup.cs* içinde bağımlılık eklenmesine bağlam ekler.</span><span class="sxs-lookup"><span data-stu-id="95499-242">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="95499-243">Öğesine bir veritabanı bağlantı dizesi ekler *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="95499-243">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="95499-244">Veritabanı bağlantı dizesi</span><span class="sxs-lookup"><span data-stu-id="95499-244">Database connection string</span></span>

<span data-ttu-id="95499-245">Scafkatlama aracı dosyada bir bağlantı dizesi oluşturur *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="95499-245">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95499-246">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95499-246">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="95499-247">Bağlantı dizesi [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)belirtir:</span><span class="sxs-lookup"><span data-stu-id="95499-247">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="95499-248">LocalDB, SQL Server Express veritabanı altyapısının hafif bir sürümüdür ve üretim kullanımı için değil uygulama geliştirmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="95499-248">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="95499-249">Varsayılan olarak, LocalDB dizinde *. mdf* dosyaları oluşturur `C:/Users/<user>` .</span><span class="sxs-lookup"><span data-stu-id="95499-249">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="95499-250">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95499-250">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="95499-251">*. Db* için SQLite bağlantı dizesini kısaltın:</span><span class="sxs-lookup"><span data-stu-id="95499-251">Shorten the SQLite connection string to *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="95499-252">Veritabanı bağlam sınıfını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="95499-252">Update the database context class</span></span>

<span data-ttu-id="95499-253">Belirli bir veri modeli için EF Core işlevselliğini koordine eden ana sınıf veritabanı bağlamı sınıfıdır.</span><span class="sxs-lookup"><span data-stu-id="95499-253">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="95499-254">Bağlam [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="95499-254">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="95499-255">Bağlam, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="95499-255">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="95499-256">Bu projede, sınıfı olarak adlandırılır `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="95499-256">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="95499-257">*Data/SchoolContext. cs* öğesini şu kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="95499-257">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="95499-258">Yukarıdaki kod tekil ile çoğul arasında değişir `DbSet<Student> Student` `DbSet<Student> Students` .</span><span class="sxs-lookup"><span data-stu-id="95499-258">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="95499-259">RazorSayfa kodunun yeni adla eşleşmesini sağlamak için `DBSet` , ' den küresel bir değişiklik yapın:`_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="95499-259">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="95499-260">Hedef: `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="95499-260">to: `_context.Students.`</span></span>

<span data-ttu-id="95499-261">8 oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="95499-261">There are 8 occurrences.</span></span>

<span data-ttu-id="95499-262">Bir varlık kümesi birden çok varlık içerdiğinden birçok geliştirici, `DBSet` özellik adlarının çoğul olması gerektiğini tercih eder.</span><span class="sxs-lookup"><span data-stu-id="95499-262">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="95499-263">Vurgulanan kod:</span><span class="sxs-lookup"><span data-stu-id="95499-263">The highlighted code:</span></span>

* <span data-ttu-id="95499-264">Her bir varlık kümesi için bir [Dbset \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="95499-264">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="95499-265">EF Core terminoloji:</span><span class="sxs-lookup"><span data-stu-id="95499-265">In EF Core terminology:</span></span>
  * <span data-ttu-id="95499-266">Bir varlık kümesi, genellikle bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="95499-266">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="95499-267">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="95499-267">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="95499-268">Çağırır <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .</span><span class="sxs-lookup"><span data-stu-id="95499-268">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="95499-269">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="95499-269">`OnModelCreating`:</span></span>
  * <span data-ttu-id="95499-270">Başlatıldığı zaman çağrılır `SchoolContext` , ancak model kilitlenmeden önce ve bağlamı başlatmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="95499-270">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="95499-271">Daha sonraki öğreticide, `Student` varlığın diğer varlıklara başvuruları olacağı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="95499-271">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="95499-272">Derleyici hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="95499-272">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="95499-273">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="95499-273">Startup.cs</span></span>

<span data-ttu-id="95499-274">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="95499-274">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="95499-275">Gibi hizmetler, `SchoolContext` uygulamanın başlatılması sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="95499-275">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="95499-276">Sayfalar gibi bu hizmetleri gerektiren bileşenler Razor , bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="95499-276">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="95499-277">Bir veritabanı bağlamı örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="95499-277">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="95499-278">Scafkatlama Aracı, bağlam sınıfını bağımlılık ekleme kapsayıcısına otomatik olarak kaydetti.</span><span class="sxs-lookup"><span data-stu-id="95499-278">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95499-279">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95499-279">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="95499-280">Aşağıdaki Vurgulanan satırlar scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="95499-280">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="95499-281">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95499-281">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="95499-282">Desteği çağrıları tarafından eklenen kodu doğrulayın `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="95499-282">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="95499-283">Üretim veritabanını kullanma hakkında bilgi için bkz. [geliştirme Için SQLite kullanma, üretime yönelik SQL Server](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) .</span><span class="sxs-lookup"><span data-stu-id="95499-283">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="95499-284">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="95499-284">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="95499-285">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="95499-285">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="95499-286">Veritabanı özel durum filtresini ekleme</span><span class="sxs-lookup"><span data-stu-id="95499-286">Add the database exception filter</span></span>

<span data-ttu-id="95499-287"><xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> `ConfigureServices` Aşağıdaki kodda gösterildiği gibi öğesine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="95499-287">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95499-288">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95499-288">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="95499-289">[Microsoft. aspnetcore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="95499-289">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="95499-290">Şu şekilde, NuGet paketini eklemek için aşağıdakileri girin:</span><span class="sxs-lookup"><span data-stu-id="95499-290">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="95499-291">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95499-291">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="95499-292">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore`NuGet paketi, Entity Framework Core hata sayfaları için ASP.NET Core ara yazılım sağlar.</span><span class="sxs-lookup"><span data-stu-id="95499-292">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="95499-293">Bu ara yazılım Entity Framework Core geçişlerle hataları algılamaya ve tanılamanıza yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="95499-293">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="95499-294">, `AddDatabaseDeveloperPageExceptionFilter` [Geliştirme ortamında](xref:fundamentals/environments)yararlı hata bilgileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="95499-294">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="95499-295">Veritabanını oluşturma</span><span class="sxs-lookup"><span data-stu-id="95499-295">Create the database</span></span>

<span data-ttu-id="95499-296">Mevcut değilse veritabanını oluşturmak için *program.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="95499-296">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="95499-297">Bağlam için bir veritabanı varsa, [Ensuyeniden](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) oluşturma yöntemi hiçbir eylemde bulunmaz.</span><span class="sxs-lookup"><span data-stu-id="95499-297">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="95499-298">Veritabanı yoksa, veritabanını ve şemayı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="95499-298">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="95499-299">`EnsureCreated` veri modeli değişikliklerini işlemek için aşağıdaki iş akışını sunar:</span><span class="sxs-lookup"><span data-stu-id="95499-299">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="95499-300">Veritabanını silin.</span><span class="sxs-lookup"><span data-stu-id="95499-300">Delete the database.</span></span> <span data-ttu-id="95499-301">Mevcut veriler kaybolur.</span><span class="sxs-lookup"><span data-stu-id="95499-301">Any existing data is lost.</span></span>
* <span data-ttu-id="95499-302">Veri modelini değiştirin.</span><span class="sxs-lookup"><span data-stu-id="95499-302">Change the data model.</span></span> <span data-ttu-id="95499-303">Örneğin, bir alan ekleyin `EmailAddress` .</span><span class="sxs-lookup"><span data-stu-id="95499-303">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="95499-304">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="95499-304">Run the app.</span></span>
* <span data-ttu-id="95499-305">`EnsureCreated` yeni şemaya sahip bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="95499-305">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="95499-306">Bu iş akışı, verileri korumanıza gerek olmadığı sürece, şema hızlı bir şekilde gelişen zaman geliştirme aşamasında iyi bir şekilde gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="95499-306">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="95499-307">Veritabanına girilen verilerin korunması gerektiğinde bu durum farklıdır.</span><span class="sxs-lookup"><span data-stu-id="95499-307">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="95499-308">Bu durumda, geçişleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="95499-308">When that is the case, use migrations.</span></span>

<span data-ttu-id="95499-309">Öğretici serisinde daha sonra tarafından oluşturulan veritabanını siler `EnsureCreated` ve bunun yerine geçişleri kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="95499-309">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="95499-310">Tarafından oluşturulan bir veritabanı, `EnsureCreated` geçişler kullanılarak güncelleştirilemiyor.</span><span class="sxs-lookup"><span data-stu-id="95499-310">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="95499-311">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="95499-311">Test the app</span></span>

* <span data-ttu-id="95499-312">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="95499-312">Run the app.</span></span>
* <span data-ttu-id="95499-313">**Öğrenciler** bağlantısını seçin ve ardından **Yeni oluştur**.</span><span class="sxs-lookup"><span data-stu-id="95499-313">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="95499-314">Düzenle, Ayrıntılar ve Sil bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="95499-314">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="95499-315">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="95499-315">Seed the database</span></span>

<span data-ttu-id="95499-316">`EnsureCreated`Yöntemi boş bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="95499-316">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="95499-317">Bu bölüm, veritabanını test verileriyle dolduran kodu ekler.</span><span class="sxs-lookup"><span data-stu-id="95499-317">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="95499-318">Aşağıdaki kodla *veri/Dbınizer. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="95499-318">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="95499-319">Kod, veritabanında herhangi bir öğrenci olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="95499-319">The code checks if there are any students in the database.</span></span> <span data-ttu-id="95499-320">Öğrenci yoksa, veritabanına test verileri ekler.</span><span class="sxs-lookup"><span data-stu-id="95499-320">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="95499-321">Performansı iyileştirmek için Koleksiyonlar yerine diziler halinde test verileri oluşturur `List<T>` .</span><span class="sxs-lookup"><span data-stu-id="95499-321">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="95499-322">*Program.cs* içinde, `EnsureCreated` çağrıyı bir çağrı ile değiştirin `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="95499-322">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="95499-323">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95499-323">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="95499-324">Çalışıyorsa uygulamayı durdurun ve **Paket Yöneticisi konsolunda** (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="95499-324">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="95499-325">`Y`Veritabanını silmek için ile yanıtlayın.</span><span class="sxs-lookup"><span data-stu-id="95499-325">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="95499-326">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95499-326">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="95499-327">Çalışıyorsa uygulamayı durdurun ve *cu. db* dosyasını silin.</span><span class="sxs-lookup"><span data-stu-id="95499-327">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="95499-328">Uygulamayı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="95499-328">Restart the app.</span></span>
* <span data-ttu-id="95499-329">Sağlanan verileri görmek için öğrenciler sayfasını seçin.</span><span class="sxs-lookup"><span data-stu-id="95499-329">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="95499-330">Veritabanını görüntüleme</span><span class="sxs-lookup"><span data-stu-id="95499-330">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95499-331">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95499-331">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="95499-332">Visual Studio 'daki **Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="95499-332">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="95499-333">SSOX 'te, **(LocalDB) \MSSQLLocalDB > veritabanları > SchoolContext-{GUID}** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="95499-333">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="95499-334">Veritabanı adı, daha önce belirttiğiniz bağlam adından ve bir tire ve bir GUID ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="95499-334">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="95499-335">**Tables** düğümünü genişletin.</span><span class="sxs-lookup"><span data-stu-id="95499-335">Expand the **Tables** node.</span></span>
* <span data-ttu-id="95499-336">Oluşturulan sütunları ve tabloya yerleştirilen satırları görmek için **öğrenci** tablosuna sağ tıklayın ve **verileri görüntüle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="95499-336">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="95499-337">Modelin tablo şemasına nasıl eşlendiğini görmek için **öğrenci** tablosuna sağ tıklayın ve **kodu görüntüle** ' ye tıklayın `Student` `Student` .</span><span class="sxs-lookup"><span data-stu-id="95499-337">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="95499-338">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95499-338">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="95499-339">Veritabanı şemasını ve sağlanan verileri görüntülemek için SQLite aracınızı kullanın.</span><span class="sxs-lookup"><span data-stu-id="95499-339">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="95499-340">Veritabanı dosyası *cu. db* olarak adlandırılır ve proje klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="95499-340">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="95499-341">Zaman uyumsuz kod</span><span class="sxs-lookup"><span data-stu-id="95499-341">Asynchronous code</span></span>

<span data-ttu-id="95499-342">Zaman uyumsuz programlama, ASP.NET Core ve EF Core için varsayılan moddur.</span><span class="sxs-lookup"><span data-stu-id="95499-342">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="95499-343">Web sunucusunda sınırlı sayıda iş parçacığı bulunur ve yüksek yük durumlarında tüm kullanılabilir iş parçacıkları kullanımda olabilir.</span><span class="sxs-lookup"><span data-stu-id="95499-343">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="95499-344">Bu durumda, sunucu, iş parçacıkları boşaltılana kadar yeni istekleri işleyemez.</span><span class="sxs-lookup"><span data-stu-id="95499-344">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="95499-345">Zaman uyumlu kod ile, çok sayıda iş parçacığı g/ç 'nin tamamlanmasını beklediği için iş çalışmadıklarında bağlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="95499-345">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="95499-346">Zaman uyumsuz kod ile, bir işlem g/ç 'yi tamamlanmayı beklerken, sunucunun diğer istekleri işlemek için kullanması için iş parçacığı serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="95499-346">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="95499-347">Sonuç olarak, zaman uyumsuz kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucu gecikmeksizin daha fazla trafiği işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="95499-347">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="95499-348">Zaman uyumsuz kod, çalışma zamanında az miktarda yük getirir.</span><span class="sxs-lookup"><span data-stu-id="95499-348">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="95499-349">Düşük trafik durumlarında, performans artışı göz ardı edilebilir, ancak yüksek trafik durumları için olası performans iyileştirmesi oldukça önemlidir.</span><span class="sxs-lookup"><span data-stu-id="95499-349">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="95499-350">Aşağıdaki kodda, [Async](/dotnet/csharp/language-reference/keywords/async) anahtar sözcüğü, `Task` return değeri, `await` anahtar sözcüğü ve `ToListAsync` yöntemi kodu zaman uyumsuz olarak yürütür.</span><span class="sxs-lookup"><span data-stu-id="95499-350">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="95499-351">`async`Anahtar sözcüğü derleyiciye şunu söyler:</span><span class="sxs-lookup"><span data-stu-id="95499-351">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="95499-352">Yöntem gövdesinin parçaları için geri çağrılar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="95499-352">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="95499-353">Döndürülen [görev](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) nesnesini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="95499-353">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="95499-354">`Task`Dönüş türü, devam eden işi temsil eder.</span><span class="sxs-lookup"><span data-stu-id="95499-354">The `Task` return type represents ongoing work.</span></span>
* <span data-ttu-id="95499-355">`await`Anahtar sözcüğü, derleyicinin yöntemi iki parçaya böetmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="95499-355">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="95499-356">İlk bölüm, zaman uyumsuz olarak başlatılan işlemle biter.</span><span class="sxs-lookup"><span data-stu-id="95499-356">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="95499-357">İkinci bölüm, işlem tamamlandığında çağrılan bir geri çağırma yöntemine konur.</span><span class="sxs-lookup"><span data-stu-id="95499-357">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="95499-358">`ToListAsync` , Uzantı yönteminin zaman uyumsuz sürümüdür `ToList` .</span><span class="sxs-lookup"><span data-stu-id="95499-358">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="95499-359">EF Core kullanan zaman uyumsuz kodu yazarken dikkat edilmesi gereken bazı şeyler:</span><span class="sxs-lookup"><span data-stu-id="95499-359">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="95499-360">Yalnızca sorguları veya komutlarının veritabanına gönderilmesine neden olan deyimler zaman uyumsuz olarak yürütülür.</span><span class="sxs-lookup"><span data-stu-id="95499-360">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="95499-361">,, `ToListAsync` , `SingleOrDefaultAsync` `FirstOrDefaultAsync` Ve içerir `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="95499-361">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="95499-362">Yalnızca, gibi bir değiştiren deyimler içermez `IQueryable` `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="95499-362">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="95499-363">EF Core bağlamı iş parçacığı açısından güvenli değildir: paralel olarak birden çok işlem yapmayı denemeyin.</span><span class="sxs-lookup"><span data-stu-id="95499-363">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="95499-364">Zaman uyumsuz kodun performans avantajlarından yararlanmak için, veritabanına sorgu gönderen EF Core yöntemleri çağırıyorsa kitaplık paketlerinin (örneğin, sayfalama için) zaman uyumsuz olarak kullanılacağını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="95499-364">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="95499-365">.NET 'te zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. Async [and await ile](/dotnet/csharp/programming-guide/concepts/async/)zaman uyumsuz [genel bakış](/dotnet/standard/async) ve zaman uyumsuz programlama</span><span class="sxs-lookup"><span data-stu-id="95499-365">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="95499-366">Performansla ilgili önemli noktalar</span><span class="sxs-lookup"><span data-stu-id="95499-366">Performance considerations</span></span>

<span data-ttu-id="95499-367">Genel olarak, bir Web sayfası rastgele sayıda satır yüklememelidir.</span><span class="sxs-lookup"><span data-stu-id="95499-367">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="95499-368">Bir sorgu, sayfalama veya sınırlandırma yaklaşımını kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="95499-368">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="95499-369">Örneğin, önceki sorgu `Take` döndürülen satırları sınırlamak için kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="95499-369">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="95499-370">Bir görünümde büyük bir tablonun numaralandırılması, bir veritabanı özel durumu numaralandırma aracılığıyla bir bölüm oluşursa kısmen oluşturulmuş bir HTTP 200 yanıtı döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="95499-370">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="95499-371"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> Varsayılan olarak 1024 ' dir.</span><span class="sxs-lookup"><span data-stu-id="95499-371"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="95499-372">Aşağıdaki kod kümeleri `MaxModelBindingCollectionSize` :</span><span class="sxs-lookup"><span data-stu-id="95499-372">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="95499-373">Sayfalama daha sonra öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="95499-373">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="95499-374">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="95499-374">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="95499-375">Sonraki öğretici</span><span class="sxs-lookup"><span data-stu-id="95499-375">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="95499-376">Bu, bir [ASP.NET Core Razor sayfaları](xref:razor-pages/index) UYGULAMASıNDA Entity Framework (EF) çekirdeğini nasıl kullanacağınızı gösteren bir öğretici serisinin ilkisidir.</span><span class="sxs-lookup"><span data-stu-id="95499-376">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="95499-377">Öğreticiler, kurgusal bir Contoso Üniversitesi için bir Web sitesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="95499-377">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="95499-378">Site, öğrenci giriş, kurs oluşturma ve eğitmen atamaları gibi işlevleri içerir.</span><span class="sxs-lookup"><span data-stu-id="95499-378">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="95499-379">Öğretici ilk kod yaklaşımını kullanır.</span><span class="sxs-lookup"><span data-stu-id="95499-379">The tutorial uses the code first approach.</span></span> <span data-ttu-id="95499-380">Bu öğreticiyi veritabanı ilk yaklaşımı kullanarak takip eden bilgiler için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/16897)bakın.</span><span class="sxs-lookup"><span data-stu-id="95499-380">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="95499-381">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="95499-381">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="95499-382">[Yönergeleri indirin](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="95499-382">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="95499-383">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="95499-383">Prerequisites</span></span>

* <span data-ttu-id="95499-384">Sayfalarla yeni başladıysanız Razor , bunu başlatmadan önce [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) öğreticisini izleyin.</span><span class="sxs-lookup"><span data-stu-id="95499-384">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95499-385">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95499-385">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="95499-386">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95499-386">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="95499-387">Veritabanı altyapıları</span><span class="sxs-lookup"><span data-stu-id="95499-387">Database engines</span></span>

<span data-ttu-id="95499-388">Visual Studio yönergeleri, yalnızca Windows üzerinde çalışan bir SQL Server Express sürümü olan [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)'yi kullanır.</span><span class="sxs-lookup"><span data-stu-id="95499-388">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="95499-389">Visual Studio Code yönergeler, platformlar arası bir veritabanı altyapısı olan [SQLite](https://www.sqlite.org/)kullanır.</span><span class="sxs-lookup"><span data-stu-id="95499-389">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="95499-390">SQLite kullanmayı seçerseniz, SQLite [Için DB tarayıcısı](https://sqlitebrowser.org/)gibi bir SQLite veritabanını yönetmek ve görüntülemek için üçüncü taraf bir araç indirip yükleyin.</span><span class="sxs-lookup"><span data-stu-id="95499-390">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="95499-391">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="95499-391">Troubleshooting</span></span>

<span data-ttu-id="95499-392">Giderebileceğiniz bir sorunla karşılaşırsanız, kodunuzu [Tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="95499-392">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="95499-393">Yardım almanın iyi bir yolu, [ASP.NET Core etiketi](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF Core etiketi](https://stackoverflow.com/questions/tagged/entity-framework-core)kullanılarak StackOverflow.com 'e bir soru göndererek.</span><span class="sxs-lookup"><span data-stu-id="95499-393">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="95499-394">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="95499-394">The sample app</span></span>

<span data-ttu-id="95499-395">Bu öğreticilerde oluşturulan uygulama, temel bir üniversite web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="95499-395">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="95499-396">Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="95499-396">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="95499-397">Öğreticide oluşturulan ekranlardan bazıları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="95499-397">Here are a few of the screens created in the tutorial.</span></span>

![Öğrenciler Dizin sayfası](intro/_static/students-index30.png)

![Öğrenciler düzenleme sayfası](intro/_static/student-edit30.png)

<span data-ttu-id="95499-400">Bu sitenin kullanıcı arabirimi stili yerleşik proje şablonlarına dayalıdır.</span><span class="sxs-lookup"><span data-stu-id="95499-400">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="95499-401">Öğreticinin odağı, Kullanıcı arabirimini nasıl özelleştireceğinizi değil EF Core kullanma konusunda yer alır.</span><span class="sxs-lookup"><span data-stu-id="95499-401">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="95499-402">Tamamlanan projenin kaynak kodunu almak için sayfanın üst kısmındaki bağlantıyı izleyin.</span><span class="sxs-lookup"><span data-stu-id="95499-402">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="95499-403">*Cu30* klasörü, öğreticinin ASP.NET Core 3,0 sürümü için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="95499-403">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="95499-404">1-7 öğreticileri için kodun durumunu yansıtan dosyalar *cu30snapshots* klasöründe bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="95499-404">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95499-405">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95499-405">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="95499-406">Tamamlanmış projeyi indirdikten sonra uygulamayı çalıştırmak için:</span><span class="sxs-lookup"><span data-stu-id="95499-406">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="95499-407">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="95499-407">Build the project.</span></span>
* <span data-ttu-id="95499-408">Paket Yöneticisi konsolu 'nda (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="95499-408">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="95499-409">Veritabanını temel alarak projeyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="95499-409">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="95499-410">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95499-410">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="95499-411">Tamamlanmış projeyi indirdikten sonra uygulamayı çalıştırmak için:</span><span class="sxs-lookup"><span data-stu-id="95499-411">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="95499-412">*Contosouniversity. csproj* öğesini silin ve *Contosoüniversıtysqlite. csproj* öğesini *contosouniversity. csproj* olarak yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="95499-412">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="95499-413">*Program.cs*' de, açıklama dışarı bu `#define Startup` şekilde `StartupSQLite` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="95499-413">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="95499-414">*appSettings.js* silin ve üzerinde *appSettings.js* *appSettingsSQLite.js* yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="95499-414">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="95499-415">*Geçişler* klasörünü silin ve *migrationssql* öğesini *geçişlerle* yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="95499-415">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="95499-416">İçin genel arama yapın `#if SQLiteVersion` ve `#if SQLiteVersion` ilgili `#endif` ifadeyi kaldırın.</span><span class="sxs-lookup"><span data-stu-id="95499-416">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="95499-417">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="95499-417">Build the project.</span></span>
* <span data-ttu-id="95499-418">Proje klasöründeki bir komut isteminde aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="95499-418">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="95499-419">SQLite aracında şu SQL ifadesini çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="95499-419">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="95499-420">Veritabanını temel alarak projeyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="95499-420">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="95499-421">Web uygulaması projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="95499-421">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95499-422">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95499-422">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="95499-423">Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="95499-423">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="95499-424">**ASP.NET Core Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="95499-424">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="95499-425">Projeyi *Contosouniversity* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="95499-425">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="95499-426">Büyük harfler de dahil olmak üzere bu tam adı kullanmak önemlidir, bu nedenle kod kopyalanıp yapıştırılırken ad alanları eşleşir.</span><span class="sxs-lookup"><span data-stu-id="95499-426">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="95499-427">Açılan menüden **.NET Core** ve **3,0 ASP.NET Core** seçin ve ardından **Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="95499-427">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="95499-428">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95499-428">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="95499-429">Bir terminalde, proje klasörünün oluşturulması gereken klasöre gidin.</span><span class="sxs-lookup"><span data-stu-id="95499-429">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="95499-430">Bir Razor sayfa projesi ve yeni proje klasörü oluşturmak için aşağıdaki komutları çalıştırın `cd` :</span><span class="sxs-lookup"><span data-stu-id="95499-430">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="95499-431">Site stilini ayarlayın</span><span class="sxs-lookup"><span data-stu-id="95499-431">Set up the site style</span></span>

<span data-ttu-id="95499-432">*Sayfa/paylaşılan/_Layout. cshtml*'yi güncelleştirerek site üst bilgisini, alt bilgisini ve menüsünü ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="95499-432">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="95499-433">"ContosoUniversity" öğesinin her oluşumunu "Contoso Üniversitesi" olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="95499-433">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="95499-434">Üç oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="95499-434">There are three occurrences.</span></span>

* <span data-ttu-id="95499-435">**Giriş** ve **Gizlilik** menü girişlerini silin ve **hakkında**, **öğrenciler**, **Kurslar**, **eğitmenler** ve **Departmanlar** için girişler ekleyin.</span><span class="sxs-lookup"><span data-stu-id="95499-435">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="95499-436">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="95499-436">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="95499-437">*Pages/Index. cshtml* dosyasında, ASP.NET Core hakkındaki metni bu uygulamayla ilgili metinle değiştirmek için dosyanın içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="95499-437">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="95499-438">Giriş sayfasının göründüğünü doğrulamak için uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="95499-438">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="95499-439">Veri modeli</span><span class="sxs-lookup"><span data-stu-id="95499-439">The data model</span></span>

<span data-ttu-id="95499-440">Aşağıdaki bölümler bir veri modeli oluşturur:</span><span class="sxs-lookup"><span data-stu-id="95499-440">The following sections create a data model:</span></span>

![Kurs-kayıt-öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

<span data-ttu-id="95499-442">Bir öğrenci herhangi bir sayıda kursa kaydolabilir ve bir kurs, kayıtlı sayıda öğrenciye sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="95499-442">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="95499-443">Öğrenci varlığı</span><span class="sxs-lookup"><span data-stu-id="95499-443">The Student entity</span></span>

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

* <span data-ttu-id="95499-445">Proje klasöründe bir *modeller* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="95499-445">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="95499-446">Aşağıdaki kodla *modeller/öğrenci. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="95499-446">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="95499-447">`ID`Özelliği, bu sınıfa karşılık gelen veritabanı tablosunun birincil anahtar sütunu olur.</span><span class="sxs-lookup"><span data-stu-id="95499-447">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="95499-448">Varsayılan olarak, EF Core `ID` birincil anahtar olarak adlandırılan bir özelliği Yorumlar `classnameID` .</span><span class="sxs-lookup"><span data-stu-id="95499-448">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="95499-449">Bu nedenle birincil anahtar sınıfı için alternatif otomatik olarak tanınan ad `Student` olur `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="95499-449">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="95499-450">Daha fazla bilgi için bkz. [EF Core-anahtarlar](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="95499-450">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="95499-451">`Enrollments`Özelliği bir [Gezinti özelliğidir](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="95499-451">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="95499-452">Gezinti özellikleri, bu varlıkla ilgili diğer varlıkları tutar.</span><span class="sxs-lookup"><span data-stu-id="95499-452">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="95499-453">Bu durumda, `Enrollments` bir `Student` varlığın özelliği `Enrollment` söz konusu öğrenciye ilişkin tüm varlıkları barındırır.</span><span class="sxs-lookup"><span data-stu-id="95499-453">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="95499-454">Örneğin, veritabanındaki bir öğrenci satırında iki ilişkili kayıt satırı varsa, `Enrollments` gezinti özelliği bu Iki kayıt varlığını içerir.</span><span class="sxs-lookup"><span data-stu-id="95499-454">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="95499-455">Veritabanında, bir kayıt satırı, Studentitıd sütunu öğrencinin ID değerini içeriyorsa bir öğrenci satırıyla ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="95499-455">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="95499-456">Örneğin, bir öğrenci satırının ID = 1 olduğunu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="95499-456">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="95499-457">İlgili kayıt satırları Studentitıd = 1 olacaktır.</span><span class="sxs-lookup"><span data-stu-id="95499-457">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="95499-458">Studentitıd, kayıt tablosundaki bir *yabancı anahtardır* .</span><span class="sxs-lookup"><span data-stu-id="95499-458">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="95499-459">`Enrollments`Özelliği, `ICollection<Enrollment>` birden çok ilgili kayıt varlığı olabileceğinden, olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="95499-459">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="95499-460">Veya gibi diğer koleksiyon türlerini kullanabilirsiniz `List<Enrollment>` `HashSet<Enrollment>` .</span><span class="sxs-lookup"><span data-stu-id="95499-460">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="95499-461">Kullanıldığında `ICollection<Enrollment>` , EF Core `HashSet<Enrollment>` Varsayılan olarak bir koleksiyon oluşturur.</span><span class="sxs-lookup"><span data-stu-id="95499-461">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="95499-462">Kayıt varlığı</span><span class="sxs-lookup"><span data-stu-id="95499-462">The Enrollment entity</span></span>

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

<span data-ttu-id="95499-464">Aşağıdaki kodla *modeller/kayıt. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="95499-464">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="95499-465">`EnrollmentID`Bu özellik birincil anahtardır; bu varlık kendi başına değil, `classnameID` kendi modelini kullanır `ID` .</span><span class="sxs-lookup"><span data-stu-id="95499-465">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="95499-466">Bir üretim veri modeli için bir model seçin ve bunu tutarlı bir şekilde kullanın.</span><span class="sxs-lookup"><span data-stu-id="95499-466">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="95499-467">Bu öğretici her ikisinin de yalnızca bir iş olduğunu göstermek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="95499-467">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="95499-468">`ID`Olmadan kullanmak `classname` , bazı veri modeli değişikliklerinin uygulanmasını kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="95499-468">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="95499-469">`Grade`Özelliği bir `enum` .</span><span class="sxs-lookup"><span data-stu-id="95499-469">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="95499-470">Tür bildiriminden sonraki soru işareti, `Grade` `Grade` özelliğin [null yapılabilir](/dotnet/csharp/programming-guide/nullable-types/)olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="95499-470">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="95499-471">Null olan bir sınıf sıfır bir sınıf null değerinden farklıdır &mdash; veya henüz atanmamış olur.</span><span class="sxs-lookup"><span data-stu-id="95499-471">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="95499-472">`StudentID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Student` .</span><span class="sxs-lookup"><span data-stu-id="95499-472">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="95499-473">Bir varlık `Enrollment` bir `Student` varlıkla ilişkilendirilir, bu nedenle özellik tek bir varlık içerir `Student` .</span><span class="sxs-lookup"><span data-stu-id="95499-473">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="95499-474">`CourseID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Course` .</span><span class="sxs-lookup"><span data-stu-id="95499-474">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="95499-475">Bir `Enrollment` varlık bir `Course` varlıkla ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="95499-475">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="95499-476">EF Core bir özelliği, adlandırılmış ise yabancı anahtar olarak yorumlar `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="95499-476">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="95499-477">Örneğin, `StudentID` `Student` `Student` nesnenin birincil anahtarı olduğundan, gezinti özelliği için yabancı anahtardır `ID` .</span><span class="sxs-lookup"><span data-stu-id="95499-477">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="95499-478">Yabancı anahtar özellikleri de adlandırılmış olabilir `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="95499-478">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="95499-479">Örneğin, `CourseID` `Course` varlığın birincil anahtarı olduğundan `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="95499-479">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="95499-480">Kurs varlığı</span><span class="sxs-lookup"><span data-stu-id="95499-480">The Course entity</span></span>

![Kurs varlık diyagramı](intro/_static/course-entity.png)

<span data-ttu-id="95499-482">Aşağıdaki kodla *modeller/kurs. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="95499-482">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="95499-483">`Enrollments`Özelliği bir gezinti özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="95499-483">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="95499-484">Bir `Course` varlık herhangi bir sayıda varlıkla ilişkili olabilir `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="95499-484">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="95499-485">`DatabaseGenerated`Özniteliği, uygulamanın veritabanını oluşturmak yerine birincil anahtarı belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="95499-485">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="95499-486">Derleyici hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="95499-486">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="95499-487">Yapı iskelesi öğrenci sayfaları</span><span class="sxs-lookup"><span data-stu-id="95499-487">Scaffold Student pages</span></span>

<span data-ttu-id="95499-488">Bu bölümde, oluşturmak için ASP.NET Core scafkatlama aracını kullanırsınız:</span><span class="sxs-lookup"><span data-stu-id="95499-488">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="95499-489">EF Core *bağlamı* sınıfı.</span><span class="sxs-lookup"><span data-stu-id="95499-489">An EF Core *context* class.</span></span> <span data-ttu-id="95499-490">Bağlam, belirli bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="95499-490">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="95499-491">`Microsoft.EntityFrameworkCore.DbContext`Sınıfından türetilir.</span><span class="sxs-lookup"><span data-stu-id="95499-491">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="95499-492">Razor varlık için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemlerini işleyen sayfalar `Student` .</span><span class="sxs-lookup"><span data-stu-id="95499-492">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95499-493">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95499-493">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="95499-494">*Sayfalar* klasöründe bir *öğrenciler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="95499-494">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="95499-495">**Çözüm Gezgini**, *Sayfalar/öğrenciler* klasörüne sağ tıklayın ve  > **yeni yapı iskelesi Ekle öğesini** seçin.</span><span class="sxs-lookup"><span data-stu-id="95499-495">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="95499-496">**Yapı iskelesi Ekle** iletişim kutusunda **Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > .</span><span class="sxs-lookup"><span data-stu-id="95499-496">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="95499-497">**Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="95499-497">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="95499-498">**Model sınıfı** açılır penceresinde **öğrenci (Contosouniversity. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="95499-498">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="95499-499">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin.</span><span class="sxs-lookup"><span data-stu-id="95499-499">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="95499-500">*Contosouniversity. modeller. Contosoüniversıtycontext* olan veri bağlamı adını *Contosouniversity. Data. SchoolContext* olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="95499-500">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="95499-501">**Ekle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="95499-501">Select **Add**.</span></span>

<span data-ttu-id="95499-502">Aşağıdaki paketler otomatik olarak yüklenir:</span><span class="sxs-lookup"><span data-stu-id="95499-502">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="95499-503">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95499-503">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="95499-504">Gerekli NuGet paketlerini yüklemek için aşağıdaki .NET Core CLI komutlarını çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="95499-504">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
<!-- TO DO  After testing, Replace with
[!INCLUDE[](~/includes/includes/add-EF-NuGet-SQLite-CLI.md)]
remove dotnet tool install --global  below
 -->
  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer
  dotnet add package Microsoft.EntityFrameworkCore.Design
  dotnet add package Microsoft.EntityFrameworkCore.Tools
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
  dotnet add package Microsoft.Extensions.Logging.Debug
  ```

  <span data-ttu-id="95499-505">Yapı iskelesi için Microsoft. VisualStudio. Web. CodeGeneration. Design paketi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="95499-505">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="95499-506">Uygulama SQL Server kullanmayabilse de, scafkatlama aracı SQL Server paketine ihtiyaç duyuyor.</span><span class="sxs-lookup"><span data-stu-id="95499-506">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="95499-507">*Sayfalar/öğrenciler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="95499-507">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="95499-508">[ASPNET-CodeGenerator scafkatlama aracı](xref:fundamentals/tools/dotnet-aspnet-codegenerator)'nı yüklemek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="95499-508">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="95499-509">Fkatlama öğrenci sayfalarını iskele almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="95499-509">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="95499-510">**Windows üzerinde**</span><span class="sxs-lookup"><span data-stu-id="95499-510">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="95499-511">**MacOS veya Linux üzerinde**</span><span class="sxs-lookup"><span data-stu-id="95499-511">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="95499-512">Önceki adımla ilgili bir sorununuz varsa, projeyi derleyin ve yapı iskelesi adımını yeniden deneyin.</span><span class="sxs-lookup"><span data-stu-id="95499-512">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="95499-513">Yapı iskelesi işlemi:</span><span class="sxs-lookup"><span data-stu-id="95499-513">The scaffolding process:</span></span>

* <span data-ttu-id="95499-514">Razor *Sayfalar/öğrenciler* klasöründe sayfa oluşturur:</span><span class="sxs-lookup"><span data-stu-id="95499-514">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="95499-515">*. Cshtml* ve *Create.cshtml.cs* oluşturma</span><span class="sxs-lookup"><span data-stu-id="95499-515">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="95499-516">*Delete. cshtml* ve *delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="95499-516">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="95499-517">*Details. cshtml* ve *details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="95499-517">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="95499-518">*. Cshtml* ve *Edit.cshtml.cs* Düzenle</span><span class="sxs-lookup"><span data-stu-id="95499-518">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="95499-519">*Index. cshtml* ve *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="95499-519">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="95499-520">*Data/SchoolContext. cs* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="95499-520">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="95499-521">*Startup.cs* içinde bağımlılık eklenmesine bağlam ekler.</span><span class="sxs-lookup"><span data-stu-id="95499-521">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="95499-522">Öğesine bir veritabanı bağlantı dizesi ekler *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="95499-522">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="95499-523">Veritabanı bağlantı dizesi</span><span class="sxs-lookup"><span data-stu-id="95499-523">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95499-524">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95499-524">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="95499-525">*appsettings.json* Dosya, [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)bağlantı dizesini belirtir.</span><span class="sxs-lookup"><span data-stu-id="95499-525">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="95499-526">LocalDB, SQL Server Express veritabanı altyapısının hafif bir sürümüdür ve üretim kullanımı için değil uygulama geliştirmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="95499-526">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="95499-527">Varsayılan olarak, LocalDB dizinde *. mdf* dosyaları oluşturur `C:/Users/<user>` .</span><span class="sxs-lookup"><span data-stu-id="95499-527">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="95499-528">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95499-528">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="95499-529">Bağlantı dizesini *cu. db* adlı bir SQLite veritabanı dosyasını işaret etmek üzere değiştirin:</span><span class="sxs-lookup"><span data-stu-id="95499-529">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="95499-530">Veritabanı bağlam sınıfını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="95499-530">Update the database context class</span></span>

<span data-ttu-id="95499-531">Belirli bir veri modeli için EF Core işlevselliğini koordine eden ana sınıf veritabanı bağlamı sınıfıdır.</span><span class="sxs-lookup"><span data-stu-id="95499-531">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="95499-532">Bağlam [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="95499-532">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="95499-533">Bağlam, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="95499-533">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="95499-534">Bu projede, sınıfı olarak adlandırılır `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="95499-534">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="95499-535">*Data/SchoolContext. cs* öğesini şu kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="95499-535">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="95499-536">Vurgulanan kod, her bir varlık kümesi için bir [Dbset \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="95499-536">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="95499-537">EF Core terminoloji:</span><span class="sxs-lookup"><span data-stu-id="95499-537">In EF Core terminology:</span></span>

* <span data-ttu-id="95499-538">Bir varlık kümesi, genellikle bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="95499-538">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="95499-539">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="95499-539">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="95499-540">Bir varlık kümesi birden çok varlık içerdiğinden, DBSet özellikleri çoğul adlar olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="95499-540">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="95499-541">Yapı iskelesi aracı bir `Student` dbset oluşturduğundan, bu adım çoğul olarak değişir `Students` .</span><span class="sxs-lookup"><span data-stu-id="95499-541">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="95499-542">RazorSayfa kodunun yeni DBSet adıyla eşleşmesini sağlamak için, tüm projesi genelinde küresel bir değişiklik yapın `_context.Student` `_context.Students` .</span><span class="sxs-lookup"><span data-stu-id="95499-542">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="95499-543">8 oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="95499-543">There are 8 occurrences.</span></span>

<span data-ttu-id="95499-544">Derleyici hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="95499-544">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="95499-545">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="95499-545">Startup.cs</span></span>

<span data-ttu-id="95499-546">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="95499-546">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="95499-547">Hizmetler (EF Core veritabanı bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="95499-547">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="95499-548">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="95499-548">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="95499-549">Bir veritabanı bağlamı örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="95499-549">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="95499-550">Scafkatlama Aracı, bağlam sınıfını bağımlılık ekleme kapsayıcısına otomatik olarak kaydetti.</span><span class="sxs-lookup"><span data-stu-id="95499-550">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95499-551">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95499-551">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="95499-552">`ConfigureServices`' De, vurgulanan satırlar scaffolder tarafından eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="95499-552">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="95499-553">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95499-553">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="95499-554">' De `ConfigureServices` , desteği tarafından eklenen kodun çağrılarından emin olun `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="95499-554">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="95499-555">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="95499-555">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="95499-556">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="95499-556">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="95499-557">Veritabanını oluşturma</span><span class="sxs-lookup"><span data-stu-id="95499-557">Create the database</span></span>

<span data-ttu-id="95499-558">Mevcut değilse veritabanını oluşturmak için *program.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="95499-558">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="95499-559">Bağlam için bir veritabanı varsa, [Ensuyeniden](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) oluşturma yöntemi hiçbir eylemde bulunmaz.</span><span class="sxs-lookup"><span data-stu-id="95499-559">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="95499-560">Veritabanı yoksa, veritabanını ve şemayı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="95499-560">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="95499-561">`EnsureCreated` veri modeli değişikliklerini işlemek için aşağıdaki iş akışını sunar:</span><span class="sxs-lookup"><span data-stu-id="95499-561">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="95499-562">Veritabanını silin.</span><span class="sxs-lookup"><span data-stu-id="95499-562">Delete the database.</span></span> <span data-ttu-id="95499-563">Mevcut veriler kaybolur.</span><span class="sxs-lookup"><span data-stu-id="95499-563">Any existing data is lost.</span></span>
* <span data-ttu-id="95499-564">Veri modelini değiştirin.</span><span class="sxs-lookup"><span data-stu-id="95499-564">Change the data model.</span></span> <span data-ttu-id="95499-565">Örneğin, bir alan ekleyin `EmailAddress` .</span><span class="sxs-lookup"><span data-stu-id="95499-565">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="95499-566">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="95499-566">Run the app.</span></span>
* <span data-ttu-id="95499-567">`EnsureCreated` yeni şemaya sahip bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="95499-567">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="95499-568">Bu iş akışı, verileri korumanıza gerek olmadığı sürece, şema hızlı bir şekilde gelişen zaman geliştirme aşamasında iyi bir şekilde gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="95499-568">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="95499-569">Veritabanına girilen verilerin korunması gerektiğinde bu durum farklıdır.</span><span class="sxs-lookup"><span data-stu-id="95499-569">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="95499-570">Bu durumda, geçişleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="95499-570">When that is the case, use migrations.</span></span>

<span data-ttu-id="95499-571">Öğretici serisinde daha sonra tarafından oluşturulan veritabanını siler `EnsureCreated` ve bunun yerine geçişleri kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="95499-571">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="95499-572">Tarafından oluşturulan bir veritabanı, `EnsureCreated` geçişler kullanılarak güncelleştirilemiyor.</span><span class="sxs-lookup"><span data-stu-id="95499-572">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="95499-573">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="95499-573">Test the app</span></span>

* <span data-ttu-id="95499-574">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="95499-574">Run the app.</span></span>
* <span data-ttu-id="95499-575">**Öğrenciler** bağlantısını seçin ve ardından **Yeni oluştur**.</span><span class="sxs-lookup"><span data-stu-id="95499-575">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="95499-576">Düzenle, Ayrıntılar ve Sil bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="95499-576">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="95499-577">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="95499-577">Seed the database</span></span>

<span data-ttu-id="95499-578">`EnsureCreated`Yöntemi boş bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="95499-578">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="95499-579">Bu bölüm, veritabanını test verileriyle dolduran kodu ekler.</span><span class="sxs-lookup"><span data-stu-id="95499-579">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="95499-580">Aşağıdaki kodla *veri/Dbınizer. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="95499-580">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="95499-581">Kod, veritabanında herhangi bir öğrenci olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="95499-581">The code checks if there are any students in the database.</span></span> <span data-ttu-id="95499-582">Öğrenci yoksa, veritabanına test verileri ekler.</span><span class="sxs-lookup"><span data-stu-id="95499-582">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="95499-583">Performansı iyileştirmek için Koleksiyonlar yerine diziler halinde test verileri oluşturur `List<T>` .</span><span class="sxs-lookup"><span data-stu-id="95499-583">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="95499-584">*Program.cs* içinde, `EnsureCreated` çağrıyı bir çağrı ile değiştirin `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="95499-584">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="95499-585">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95499-585">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="95499-586">Çalışıyorsa uygulamayı durdurun ve **Paket Yöneticisi konsolunda** (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="95499-586">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="95499-587">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95499-587">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="95499-588">Çalışıyorsa uygulamayı durdurun ve *cu. db* dosyasını silin.</span><span class="sxs-lookup"><span data-stu-id="95499-588">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="95499-589">Uygulamayı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="95499-589">Restart the app.</span></span>

* <span data-ttu-id="95499-590">Sağlanan verileri görmek için öğrenciler sayfasını seçin.</span><span class="sxs-lookup"><span data-stu-id="95499-590">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="95499-591">Veritabanını görüntüleme</span><span class="sxs-lookup"><span data-stu-id="95499-591">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95499-592">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95499-592">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="95499-593">Visual Studio 'daki **Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="95499-593">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="95499-594">SSOX 'te, **(LocalDB) \MSSQLLocalDB > veritabanları > SchoolContext-{GUID}** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="95499-594">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="95499-595">Veritabanı adı, daha önce belirttiğiniz bağlam adından ve bir tire ve bir GUID ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="95499-595">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="95499-596">**Tables** düğümünü genişletin.</span><span class="sxs-lookup"><span data-stu-id="95499-596">Expand the **Tables** node.</span></span>
* <span data-ttu-id="95499-597">Oluşturulan sütunları ve tabloya yerleştirilen satırları görmek için **öğrenci** tablosuna sağ tıklayın ve **verileri görüntüle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="95499-597">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="95499-598">Modelin tablo şemasına nasıl eşlendiğini görmek için **öğrenci** tablosuna sağ tıklayın ve **kodu görüntüle** ' ye tıklayın `Student` `Student` .</span><span class="sxs-lookup"><span data-stu-id="95499-598">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="95499-599">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95499-599">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="95499-600">Veritabanı şemasını ve sağlanan verileri görüntülemek için SQLite aracınızı kullanın.</span><span class="sxs-lookup"><span data-stu-id="95499-600">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="95499-601">Veritabanı dosyası *cu. db* olarak adlandırılır ve proje klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="95499-601">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="95499-602">Zaman uyumsuz kod</span><span class="sxs-lookup"><span data-stu-id="95499-602">Asynchronous code</span></span>

<span data-ttu-id="95499-603">Zaman uyumsuz programlama, ASP.NET Core ve EF Core için varsayılan moddur.</span><span class="sxs-lookup"><span data-stu-id="95499-603">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="95499-604">Web sunucusunda sınırlı sayıda iş parçacığı bulunur ve yüksek yük durumlarında tüm kullanılabilir iş parçacıkları kullanımda olabilir.</span><span class="sxs-lookup"><span data-stu-id="95499-604">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="95499-605">Bu durumda, sunucu, iş parçacıkları boşaltılana kadar yeni istekleri işleyemez.</span><span class="sxs-lookup"><span data-stu-id="95499-605">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="95499-606">Zaman uyumlu kodla, çok sayıda iş parçacığı, g/ç 'nin tamamlanmasını beklediği için aslında herhangi bir iş yapmadıklarında bağlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="95499-606">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="95499-607">Zaman uyumsuz kod ile, bir işlem g/ç 'yi tamamlanmayı beklerken, sunucunun diğer istekleri işlemek için kullanması için iş parçacığı serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="95499-607">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="95499-608">Sonuç olarak, zaman uyumsuz kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucu gecikmeksizin daha fazla trafiği işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="95499-608">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="95499-609">Zaman uyumsuz kod, çalışma zamanında az miktarda yük getirir.</span><span class="sxs-lookup"><span data-stu-id="95499-609">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="95499-610">Düşük trafik durumlarında, performans artışı göz ardı edilebilir, ancak yüksek trafik durumları için olası performans iyileştirmesi oldukça önemlidir.</span><span class="sxs-lookup"><span data-stu-id="95499-610">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="95499-611">Aşağıdaki kodda, [Async](/dotnet/csharp/language-reference/keywords/async) anahtar sözcüğü, `Task<T>` return değeri, `await` anahtar sözcüğü ve `ToListAsync` yöntemi kodu zaman uyumsuz olarak yürütür.</span><span class="sxs-lookup"><span data-stu-id="95499-611">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="95499-612">`async`Anahtar sözcüğü derleyiciye şunu söyler:</span><span class="sxs-lookup"><span data-stu-id="95499-612">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="95499-613">Yöntem gövdesinin parçaları için geri çağrılar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="95499-613">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="95499-614">Döndürülen [görev](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) nesnesini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="95499-614">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="95499-615">`Task<T>`Dönüş türü, devam eden işi temsil eder.</span><span class="sxs-lookup"><span data-stu-id="95499-615">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="95499-616">`await`Anahtar sözcüğü, derleyicinin yöntemi iki parçaya böetmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="95499-616">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="95499-617">İlk bölüm, zaman uyumsuz olarak başlatılan işlemle biter.</span><span class="sxs-lookup"><span data-stu-id="95499-617">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="95499-618">İkinci bölüm, işlem tamamlandığında çağrılan bir geri çağırma yöntemine konur.</span><span class="sxs-lookup"><span data-stu-id="95499-618">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="95499-619">`ToListAsync` , Uzantı yönteminin zaman uyumsuz sürümüdür `ToList` .</span><span class="sxs-lookup"><span data-stu-id="95499-619">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="95499-620">EF Core kullanan zaman uyumsuz kodu yazarken dikkat edilmesi gereken bazı şeyler:</span><span class="sxs-lookup"><span data-stu-id="95499-620">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="95499-621">Yalnızca sorguları veya komutlarının veritabanına gönderilmesine neden olan deyimler zaman uyumsuz olarak yürütülür.</span><span class="sxs-lookup"><span data-stu-id="95499-621">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="95499-622">,, `ToListAsync` , `SingleOrDefaultAsync` `FirstOrDefaultAsync` Ve içerir `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="95499-622">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="95499-623">Yalnızca, gibi bir değiştiren deyimler içermez `IQueryable` `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="95499-623">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="95499-624">EF Core bağlamı iş parçacığı açısından güvenli değildir: paralel olarak birden çok işlem yapmayı denemeyin.</span><span class="sxs-lookup"><span data-stu-id="95499-624">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="95499-625">Zaman uyumsuz kodun performans avantajlarından yararlanmak için, veritabanına sorgu gönderen EF Core yöntemleri çağırıyorsa kitaplık paketlerinin (örneğin, sayfalama için) zaman uyumsuz olarak kullanılacağını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="95499-625">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="95499-626">.NET 'te zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. Async [and await ile](/dotnet/csharp/programming-guide/concepts/async/)zaman uyumsuz [genel bakış](/dotnet/standard/async) ve zaman uyumsuz programlama</span><span class="sxs-lookup"><span data-stu-id="95499-626">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="95499-627">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="95499-627">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="95499-628">Sonraki öğretici</span><span class="sxs-lookup"><span data-stu-id="95499-628">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="95499-629">Contoso Üniversitesi örnek Web uygulaması, Razor Entity Framework (EF) Core kullanarak ASP.NET Core sayfalı bir uygulamanın nasıl oluşturulacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="95499-629">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="95499-630">Örnek uygulama, kurgusal bir Contoso Üniversitesi için bir Web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="95499-630">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="95499-631">Öğrenci giriş, kurs oluşturma ve eğitmen atamaları gibi işlevleri içerir.</span><span class="sxs-lookup"><span data-stu-id="95499-631">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="95499-632">Bu sayfa, Contoso Üniversitesi örnek uygulamasının nasıl oluşturulacağını açıklayan bir öğretici serisinin ilkisidir.</span><span class="sxs-lookup"><span data-stu-id="95499-632">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="95499-633">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="95499-633">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="95499-634">[Yönergeleri indirin](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="95499-634">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="95499-635">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="95499-635">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95499-636">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95499-636">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="95499-637">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95499-637">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="95499-638">[ Razor Sayfalarla](xref:razor-pages/index)benzerlik.</span><span class="sxs-lookup"><span data-stu-id="95499-638">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="95499-639">Yeni programcılar, bu seriyi başlatmadan önce [sayfalarla çalışmaya başlama Razor sayfasını](xref:tutorials/razor-pages/razor-pages-start) tamamlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="95499-639">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="95499-640">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="95499-640">Troubleshooting</span></span>

<span data-ttu-id="95499-641">Çözemiyoruz bir sorunla karşılaşırsanız, kodunuzun [Tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)karşılaştırılmasıyla genellikle çözümü bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="95499-641">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="95499-642">[ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) [için bir](https://stackoverflow.com/questions/tagged/asp.net-core) soru göndererek yardım almanın iyi bir yolu.</span><span class="sxs-lookup"><span data-stu-id="95499-642">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="95499-643">Contoso Üniversitesi web uygulaması</span><span class="sxs-lookup"><span data-stu-id="95499-643">The Contoso University web app</span></span>

<span data-ttu-id="95499-644">Bu öğreticilerde oluşturulan uygulama, temel bir üniversite web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="95499-644">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="95499-645">Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="95499-645">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="95499-646">Öğreticide oluşturulan ekranlardan bazıları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="95499-646">Here are a few of the screens created in the tutorial.</span></span>

![Öğrenciler Dizin sayfası](intro/_static/students-index.png)

![Öğrenciler düzenleme sayfası](intro/_static/student-edit.png)

<span data-ttu-id="95499-649">Bu sitenin kullanıcı arabirimi stili yerleşik şablonlar tarafından üretilme kadar yakın.</span><span class="sxs-lookup"><span data-stu-id="95499-649">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="95499-650">Eğitim odağı, Razor kullanıcı arabiriminden değil, sayfalarla EF Core.</span><span class="sxs-lookup"><span data-stu-id="95499-650">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="95499-651">Contosoüniversitesi Razor sayfaları Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="95499-651">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95499-652">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95499-652">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="95499-653">Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="95499-653">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="95499-654">Yeni bir ASP.NET Core Web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="95499-654">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="95499-655">Projeyi **Contosouniversity** olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="95499-655">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="95499-656">Kod kopyalama/yapıştırma olduğunda, ad alanlarının eşleşmesi için *Contosouniversity* projesini adlandırmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="95499-656">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="95499-657">Açılan listede **ASP.NET Core 2,1** ' i seçin ve ardından **Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="95499-657">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="95499-658">Yukarıdaki adımların görüntüleri için bkz. [ Razor Web uygulaması oluşturma](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="95499-658">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="95499-659">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="95499-659">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="95499-660">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95499-660">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="95499-661">Site stilini ayarlayın</span><span class="sxs-lookup"><span data-stu-id="95499-661">Set up the site style</span></span>

<span data-ttu-id="95499-662">Site menüsünü, düzeni ve giriş sayfasını birkaç değişiklik ayarlar.</span><span class="sxs-lookup"><span data-stu-id="95499-662">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="95499-663">*Sayfaları/paylaşılan/_Layout. cshtml* 'yi aşağıdaki değişikliklerle güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="95499-663">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="95499-664">"ContosoUniversity" öğesinin her oluşumunu "Contoso Üniversitesi" olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="95499-664">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="95499-665">Üç oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="95499-665">There are three occurrences.</span></span>

* <span data-ttu-id="95499-666">**Öğrenciler**, **Kurslar**, **eğitmenler** ve **Departmanlar** için menü girişleri ekleyin ve **kişi** menü girişini silin.</span><span class="sxs-lookup"><span data-stu-id="95499-666">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="95499-667">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="95499-667">The changes are highlighted.</span></span> <span data-ttu-id="95499-668">(Tüm *biçimlendirme gösterilmez.* )</span><span class="sxs-lookup"><span data-stu-id="95499-668">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="95499-669">*Pages/Index. cshtml* dosyasında, ASP.net ve MVC hakkındaki metni bu uygulamayla ilgili metinle değiştirmek için dosyanın içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="95499-669">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="95499-670">Veri modelini oluşturma</span><span class="sxs-lookup"><span data-stu-id="95499-670">Create the data model</span></span>

<span data-ttu-id="95499-671">Contoso Üniversitesi uygulaması için varlık sınıfları oluşturma.</span><span class="sxs-lookup"><span data-stu-id="95499-671">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="95499-672">Aşağıdaki üç varlıkla başlayın:</span><span class="sxs-lookup"><span data-stu-id="95499-672">Start with the following three entities:</span></span>

![Kurs-kayıt-öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

<span data-ttu-id="95499-674">Ve varlıkları arasında bire çok ilişki vardır `Student` `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="95499-674">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="95499-675">Ve varlıkları arasında bire çok ilişki vardır `Course` `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="95499-675">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="95499-676">Bir öğrenci herhangi bir sayıda kursa kaydolabilir.</span><span class="sxs-lookup"><span data-stu-id="95499-676">A student can enroll in any number of courses.</span></span> <span data-ttu-id="95499-677">Bir kurs, kayıtlı sayıda öğrenciye sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="95499-677">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="95499-678">Aşağıdaki bölümlerde, bu varlıkların her biri için bir sınıf oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="95499-678">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="95499-679">Öğrenci varlığı</span><span class="sxs-lookup"><span data-stu-id="95499-679">The Student entity</span></span>

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

<span data-ttu-id="95499-681">*Modeller* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="95499-681">Create a *Models* folder.</span></span> <span data-ttu-id="95499-682">*Modeller* klasöründe, *Student.cs* adlı bir sınıf dosyasını aşağıdaki kodla oluşturun:</span><span class="sxs-lookup"><span data-stu-id="95499-682">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="95499-683">`ID`Özelliği, bu sınıfa karşılık gelen veritabanı (DB) tablosunun birincil anahtar sütunu olur.</span><span class="sxs-lookup"><span data-stu-id="95499-683">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="95499-684">Varsayılan olarak, EF Core `ID` birincil anahtar olarak adlandırılan bir özelliği Yorumlar `classnameID` .</span><span class="sxs-lookup"><span data-stu-id="95499-684">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="95499-685">`classnameID`' De, `classname` sınıfının adıdır.</span><span class="sxs-lookup"><span data-stu-id="95499-685">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="95499-686">Diğer otomatik olarak tanınan birincil anahtar, `StudentID` Önceki örnekte yer verilir.</span><span class="sxs-lookup"><span data-stu-id="95499-686">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="95499-687">`Enrollments`Özelliği bir [Gezinti özelliğidir](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="95499-687">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="95499-688">Gezinti özellikleri bu varlıkla ilgili diğer varlıkların bağlantısını sağlar.</span><span class="sxs-lookup"><span data-stu-id="95499-688">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="95499-689">Bu durumda, `Enrollments` öğesinin özelliği ile `Student entity` `Enrollment` ilgili tüm varlıkları barındırır `Student` .</span><span class="sxs-lookup"><span data-stu-id="95499-689">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="95499-690">Örneğin, VERITABANıNDAKI bir öğrenci satırında iki ilişkili kayıt satırı varsa, `Enrollments` gezinti özelliği bu iki `Enrollment` varlığı içerir.</span><span class="sxs-lookup"><span data-stu-id="95499-690">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="95499-691">İlgili `Enrollment` satır, söz konusu öğrencinin birincil anahtar değerini sütununda içeren bir satırdır `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="95499-691">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="95499-692">Örneğin, ID = 1 olan öğrencinin tabloda iki satır olduğunu varsayalım `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="95499-692">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="95499-693">`Enrollment`Tabloda = 1 olan iki satır vardır `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="95499-693">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="95499-694">`StudentID` , tablodaki `Enrollment` öğrencisi belirten bir yabancı anahtardır `Student` .</span><span class="sxs-lookup"><span data-stu-id="95499-694">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="95499-695">Bir gezinti özelliği birden çok varlık tutabileceğinden, gezinti özelliği gibi bir liste türü olmalıdır `ICollection<T>` .</span><span class="sxs-lookup"><span data-stu-id="95499-695">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="95499-696">`ICollection<T>` belirtilebilir veya veya gibi bir tür olabilir `List<T>` `HashSet<T>` .</span><span class="sxs-lookup"><span data-stu-id="95499-696">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="95499-697">Kullanıldığında `ICollection<T>` , EF Core `HashSet<T>` Varsayılan olarak bir koleksiyon oluşturur.</span><span class="sxs-lookup"><span data-stu-id="95499-697">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="95499-698">Birden çok varlığı tutan gezinti özellikleri, çoktan çoğa ve bire çok ilişkilerden gelir.</span><span class="sxs-lookup"><span data-stu-id="95499-698">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="95499-699">Kayıt varlığı</span><span class="sxs-lookup"><span data-stu-id="95499-699">The Enrollment entity</span></span>

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

<span data-ttu-id="95499-701">*Modeller* klasöründe, aşağıdaki kodla *enrollment.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="95499-701">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="95499-702">`EnrollmentID`Özelliği birincil anahtardır.</span><span class="sxs-lookup"><span data-stu-id="95499-702">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="95499-703">Bu varlık `classnameID` `ID` , varlığı beğenmek yerine, modelini kullanır `Student` .</span><span class="sxs-lookup"><span data-stu-id="95499-703">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="95499-704">Genellikle geliştiriciler bir model seçer ve bunu veri modeli boyunca kullanır.</span><span class="sxs-lookup"><span data-stu-id="95499-704">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="95499-705">Daha sonraki bir öğreticide, veri modelinde devralmayı daha kolay hale getirmek için ClassName olmadan ID kullanımı gösterilmemiştir.</span><span class="sxs-lookup"><span data-stu-id="95499-705">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="95499-706">`Grade`Özelliği bir `enum` .</span><span class="sxs-lookup"><span data-stu-id="95499-706">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="95499-707">Tür bildiriminden sonraki soru işareti, `Grade` `Grade` özelliğin null yapılabilir olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="95499-707">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="95499-708">Null olan bir sınıf sıfır bir sınıf ile farklıdır--null, henüz bir sınıf bilinmediğini veya henüz atanmadığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="95499-708">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="95499-709">`StudentID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Student` .</span><span class="sxs-lookup"><span data-stu-id="95499-709">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="95499-710">Bir varlık `Enrollment` bir `Student` varlıkla ilişkilendirilir, bu nedenle özellik tek bir varlık içerir `Student` .</span><span class="sxs-lookup"><span data-stu-id="95499-710">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="95499-711">`Student`Varlık, `Student.Enrollments` birden çok varlık içeren gezinti özelliğinden farklıdır `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="95499-711">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="95499-712">`CourseID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Course` .</span><span class="sxs-lookup"><span data-stu-id="95499-712">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="95499-713">Bir `Enrollment` varlık bir `Course` varlıkla ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="95499-713">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="95499-714">EF Core bir özelliği, adlandırılmış ise yabancı anahtar olarak yorumlar `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="95499-714">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="95499-715">Örneğin, `StudentID` `Student` gezinti özelliği için `Student` varlığın birincil anahtarı olduğundan `ID` .</span><span class="sxs-lookup"><span data-stu-id="95499-715">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="95499-716">Yabancı anahtar özellikleri de adlandırılmış olabilir `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="95499-716">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="95499-717">Örneğin, `CourseID` `Course` varlığın birincil anahtarı olduğundan `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="95499-717">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="95499-718">Kurs varlığı</span><span class="sxs-lookup"><span data-stu-id="95499-718">The Course entity</span></span>

![Kurs varlık diyagramı](intro/_static/course-entity.png)

<span data-ttu-id="95499-720">*Modeller* klasöründe, aşağıdaki kodla *Course.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="95499-720">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="95499-721">`Enrollments`Özelliği bir gezinti özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="95499-721">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="95499-722">Bir `Course` varlık herhangi bir sayıda varlıkla ilişkili olabilir `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="95499-722">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="95499-723">`DatabaseGenerated`Özniteliği, UYGULAMANıN DB oluşturmak yerine birincil anahtarı belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="95499-723">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="95499-724">Öğrenci modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="95499-724">Scaffold the student model</span></span>

<span data-ttu-id="95499-725">Bu bölümde öğrenci modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="95499-725">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="95499-726">Diğer bir deyişle, scafkatlama aracı öğrenci modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.</span><span class="sxs-lookup"><span data-stu-id="95499-726">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="95499-727">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="95499-727">Build the project.</span></span>
* <span data-ttu-id="95499-728">*Sayfalar/öğrenciler* klasörünü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="95499-728">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95499-729">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95499-729">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="95499-730">**Çözüm Gezgini**, *Sayfalar/öğrenciler* klasörüne sağ tıklayarak  > **yeni yapı iskelesi öğesi** ekleyin >.</span><span class="sxs-lookup"><span data-stu-id="95499-730">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="95499-731">**Yapı iskelesi Ekle** iletişim kutusunda **Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > .</span><span class="sxs-lookup"><span data-stu-id="95499-731">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="95499-732">**Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="95499-732">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="95499-733">**Model sınıfı** açılır penceresinde **öğrenci (Contosouniversity. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="95499-733">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="95499-734">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin ve üretilen adı **Contosouniversity. modeller. SchoolContext** olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="95499-734">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="95499-735">**Veri bağlamı sınıfı** açılır penceresinde **Contosouniversity. modeller. SchoolContext** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="95499-735">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="95499-736">**Ekle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="95499-736">Select **Add**.</span></span>

![CRUD iletişim kutusu](intro/_static/s1.png)

<span data-ttu-id="95499-738">Önceki adımla ilgili bir sorununuz varsa [Film modeli](xref:tutorials/razor-pages/model#scaffold-the-movie-model) ' ne bakın.</span><span class="sxs-lookup"><span data-stu-id="95499-738">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="95499-739">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95499-739">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="95499-740">Öğrenci modelini iskele almak için aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="95499-740">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="95499-741">Yapı iskelesi işlemi oluşturulur ve aşağıdaki dosyaları değiştirdi:</span><span class="sxs-lookup"><span data-stu-id="95499-741">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="95499-742">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="95499-742">Files created</span></span>

* <span data-ttu-id="95499-743">*Sayfalar/öğrenciler* Oluşturma, silme, ayrıntılar, düzenleme, dizin oluşturma.</span><span class="sxs-lookup"><span data-stu-id="95499-743">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="95499-744">*Data/SchoolContext. cs*</span><span class="sxs-lookup"><span data-stu-id="95499-744">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="95499-745">Dosya güncelleştirmeleri</span><span class="sxs-lookup"><span data-stu-id="95499-745">File updates</span></span>

* <span data-ttu-id="95499-746">*Startup.cs* : Bu dosyadaki değişiklikler sonraki bölümde ayrıntılıdır.</span><span class="sxs-lookup"><span data-stu-id="95499-746">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="95499-747">*appsettings.json* : Yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesi eklenir.</span><span class="sxs-lookup"><span data-stu-id="95499-747">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="95499-748">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="95499-748">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="95499-749">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="95499-749">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="95499-750">Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="95499-750">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="95499-751">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="95499-751">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="95499-752">Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="95499-752">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="95499-753">Scafkatlama aracı otomatik olarak bir DB bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.</span><span class="sxs-lookup"><span data-stu-id="95499-753">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="95499-754">`ConfigureServices` *Startup.cs* içindeki yöntemi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="95499-754">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="95499-755">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="95499-755">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="95499-756">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="95499-756">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="95499-757">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="95499-757">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="95499-758">Ana güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="95499-758">Update main</span></span>

<span data-ttu-id="95499-759">*Program.cs*' de, `Main` aşağıdakileri yapmak için yöntemini değiştirin:</span><span class="sxs-lookup"><span data-stu-id="95499-759">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="95499-760">Bağımlılık ekleme kapsayıcısından bir DB bağlam örneği alın.</span><span class="sxs-lookup"><span data-stu-id="95499-760">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="95499-761">Yeniden  [oluşturulmasını](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)çağırın.</span><span class="sxs-lookup"><span data-stu-id="95499-761">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="95499-762">`EnsureCreated`Yöntem tamamlandığında bağlamı atın.</span><span class="sxs-lookup"><span data-stu-id="95499-762">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="95499-763">Aşağıdaki kod güncelleştirilmiş *program.cs* dosyasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="95499-763">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="95499-764">`EnsureCreated` bağlam veritabanının mevcut olmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="95499-764">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="95499-765">Varsa, hiçbir eylem yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="95499-765">If it exists, no action is taken.</span></span> <span data-ttu-id="95499-766">Yoksa, veritabanı ve tüm şeması oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="95499-766">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="95499-767">`EnsureCreated` veritabanını oluşturmak için geçişleri kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="95499-767">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="95499-768">İle oluşturulan bir veritabanı `EnsureCreated` daha sonra geçişler kullanılarak güncelleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="95499-768">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="95499-769">`EnsureCreated` , aşağıdaki iş akışına izin veren uygulama başlatma sırasında çağrılır:</span><span class="sxs-lookup"><span data-stu-id="95499-769">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="95499-770">VERITABANıNı silin.</span><span class="sxs-lookup"><span data-stu-id="95499-770">Delete the DB.</span></span>
* <span data-ttu-id="95499-771">DB şemasını değiştirin (örneğin, bir `EmailAddress` alan ekleyin).</span><span class="sxs-lookup"><span data-stu-id="95499-771">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="95499-772">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="95499-772">Run the app.</span></span>
* <span data-ttu-id="95499-773">`EnsureCreated` sütunu ile bir VERITABANı oluşturur `EmailAddress` .</span><span class="sxs-lookup"><span data-stu-id="95499-773">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="95499-774">`EnsureCreated` şema hızlı bir şekilde gelişmede geliştirmede daha erken kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="95499-774">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="95499-775">Öğreticide daha sonra DB silinir ve geçişler kullanılır.</span><span class="sxs-lookup"><span data-stu-id="95499-775">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="95499-776">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="95499-776">Test the app</span></span>

<span data-ttu-id="95499-777">Uygulamayı çalıştırın ve ilkeyi kabul edin cookie .</span><span class="sxs-lookup"><span data-stu-id="95499-777">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="95499-778">Bu uygulama, kişisel bilgileri saklar.</span><span class="sxs-lookup"><span data-stu-id="95499-778">This app doesn't keep personal information.</span></span> <span data-ttu-id="95499-779">İlke hakkında, cookie [AB Genel VERI koruma YÖNETMELIĞI (GDPR) desteğiyle](xref:security/gdpr)ilgili bilgi edinebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="95499-779">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="95499-780">**Öğrenciler** bağlantısını seçin ve ardından **Yeni oluştur**.</span><span class="sxs-lookup"><span data-stu-id="95499-780">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="95499-781">Düzenle, Ayrıntılar ve Sil bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="95499-781">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="95499-782">SchoolContext DB bağlamını inceleyin</span><span class="sxs-lookup"><span data-stu-id="95499-782">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="95499-783">Belirli bir veri modeli için EF Core işlevselliğini koordine eden ana sınıf DB bağlam sınıfıdır.</span><span class="sxs-lookup"><span data-stu-id="95499-783">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="95499-784">Veri bağlamı [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="95499-784">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="95499-785">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="95499-785">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="95499-786">Bu projede, sınıfı olarak adlandırılır `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="95499-786">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="95499-787">Aşağıdaki kodla *SchoolContext.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="95499-787">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="95499-788">Vurgulanan kod, her bir varlık kümesi için bir [Dbset \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="95499-788">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="95499-789">EF Core terminoloji:</span><span class="sxs-lookup"><span data-stu-id="95499-789">In EF Core terminology:</span></span>

* <span data-ttu-id="95499-790">Bir varlık kümesi, genellikle bir DB tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="95499-790">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="95499-791">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="95499-791">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="95499-792">`DbSet<Enrollment>` ve `DbSet<Course>` atlanamaz.</span><span class="sxs-lookup"><span data-stu-id="95499-792">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="95499-793">EF Core `Student` `Enrollment` , varlık varlığa başvurduğundan ve varlık `Enrollment` varlığa başvurduğundan bunları örtülü olarak içerir `Course` .</span><span class="sxs-lookup"><span data-stu-id="95499-793">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="95499-794">Bu öğreticide, `DbSet<Enrollment>` ve `DbSet<Course>` içinde tutun `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="95499-794">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="95499-795">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="95499-795">SQL Server Express LocalDB</span></span>

<span data-ttu-id="95499-796">Bağlantı dizesi [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)belirtir.</span><span class="sxs-lookup"><span data-stu-id="95499-796">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="95499-797">LocalDB, SQL Server Express veritabanı altyapısının hafif bir sürümüdür ve üretim kullanımı için değil uygulama geliştirmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="95499-797">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="95499-798">LocalDB, istek üzerine başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur.</span><span class="sxs-lookup"><span data-stu-id="95499-798">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="95499-799">Varsayılan olarak, LocalDB dizinde *. mdf* DB dosyaları oluşturur `C:/Users/<user>` .</span><span class="sxs-lookup"><span data-stu-id="95499-799">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="95499-800">Test verileriyle VERITABANıNı başlatmak için kod ekleme</span><span class="sxs-lookup"><span data-stu-id="95499-800">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="95499-801">EF Core boş bir VERITABANı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="95499-801">EF Core creates an empty DB.</span></span> <span data-ttu-id="95499-802">Bu bölümde, `Initialize` Test verileriyle doldurmak için bir yöntem yazılır.</span><span class="sxs-lookup"><span data-stu-id="95499-802">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="95499-803">*Veri* klasöründe, *DbInitializer.cs* adlı yeni bir sınıf dosyası oluşturun ve aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="95499-803">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="95499-804">Note: önceki kod, `Models` yerine ad alanı için ( `namespace ContosoUniversity.Models` ) kullanır `Data` .</span><span class="sxs-lookup"><span data-stu-id="95499-804">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="95499-805">`Models` , scaffolder tarafından oluşturulan kodla tutarlıdır.</span><span class="sxs-lookup"><span data-stu-id="95499-805">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="95499-806">Daha fazla bilgi için bkz. [GitHub yapı iskelesi sorunu](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="95499-806">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="95499-807">Kod, VERITABANıNDA herhangi bir öğrenci olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="95499-807">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="95499-808">Veritabanında hiç öğrenci yoksa, DB test verileriyle başlatılır.</span><span class="sxs-lookup"><span data-stu-id="95499-808">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="95499-809">Performansı iyileştirmek için test verilerini koleksiyonlar yerine dizilere yükler `List<T>` .</span><span class="sxs-lookup"><span data-stu-id="95499-809">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="95499-810">`EnsureCreated`YÖNTEMI DB bağlamı için otomatik olarak DB oluşturur.</span><span class="sxs-lookup"><span data-stu-id="95499-810">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="95499-811">VERITABANı varsa, `EnsureCreated` veritabanını değiştirmeden döndürür.</span><span class="sxs-lookup"><span data-stu-id="95499-811">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="95499-812">*Program.cs* içinde, `Main` çağrısı yapılacak yöntemi değiştirin `Initialize` :</span><span class="sxs-lookup"><span data-stu-id="95499-812">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="95499-813">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95499-813">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="95499-814">Çalışıyorsa uygulamayı durdurun ve **Paket Yöneticisi konsolunda** (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="95499-814">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="95499-815">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95499-815">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="95499-816">Çalışıyorsa uygulamayı durdurun ve *cu. db* dosyasını silin.</span><span class="sxs-lookup"><span data-stu-id="95499-816">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="95499-817">VERITABANıNı görüntüleme</span><span class="sxs-lookup"><span data-stu-id="95499-817">View the DB</span></span>

<span data-ttu-id="95499-818">Veritabanı adı, daha önce belirttiğiniz bağlam adından ve bir tire ve bir GUID ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="95499-818">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="95499-819">Bu nedenle, veritabanı adı "SchoolContext-{GUID}" olacaktır.</span><span class="sxs-lookup"><span data-stu-id="95499-819">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="95499-820">GUID her kullanıcı için farklı olacaktır.</span><span class="sxs-lookup"><span data-stu-id="95499-820">The GUID will be different for each user.</span></span>
<span data-ttu-id="95499-821">Visual Studio 'daki **Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="95499-821">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="95499-822">SSOX 'te, **(LocalDB) \MSSQLLocalDB > veritabanları > SchoolContext-{GUID}**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="95499-822">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="95499-823">**Tables** düğümünü genişletin.</span><span class="sxs-lookup"><span data-stu-id="95499-823">Expand the **Tables** node.</span></span>

<span data-ttu-id="95499-824">Oluşturulan sütunları ve tabloya yerleştirilen satırları görmek için **öğrenci** tablosuna sağ tıklayın ve **verileri görüntüle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="95499-824">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="95499-825">Zaman uyumsuz kod</span><span class="sxs-lookup"><span data-stu-id="95499-825">Asynchronous code</span></span>

<span data-ttu-id="95499-826">Zaman uyumsuz programlama, ASP.NET Core ve EF Core için varsayılan moddur.</span><span class="sxs-lookup"><span data-stu-id="95499-826">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="95499-827">Web sunucusunda sınırlı sayıda iş parçacığı bulunur ve yüksek yük durumlarında tüm kullanılabilir iş parçacıkları kullanımda olabilir.</span><span class="sxs-lookup"><span data-stu-id="95499-827">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="95499-828">Bu durumda, sunucu, iş parçacıkları boşaltılana kadar yeni istekleri işleyemez.</span><span class="sxs-lookup"><span data-stu-id="95499-828">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="95499-829">Zaman uyumlu kodla, çok sayıda iş parçacığı, g/ç 'nin tamamlanmasını beklediği için aslında herhangi bir iş yapmadıklarında bağlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="95499-829">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="95499-830">Zaman uyumsuz kod ile, bir işlem g/ç 'yi tamamlanmayı beklerken, sunucunun diğer istekleri işlemek için kullanması için iş parçacığı serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="95499-830">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="95499-831">Sonuç olarak, zaman uyumsuz kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucu, gecikme olmadan daha fazla trafiği işlemeye etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="95499-831">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="95499-832">Zaman uyumsuz kod, çalışma zamanında az miktarda yük getirir.</span><span class="sxs-lookup"><span data-stu-id="95499-832">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="95499-833">Düşük trafik durumlarında, performans artışı göz ardı edilebilir, ancak yüksek trafik durumları için olası performans iyileştirmesi oldukça önemlidir.</span><span class="sxs-lookup"><span data-stu-id="95499-833">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="95499-834">Aşağıdaki kodda, [Async](/dotnet/csharp/language-reference/keywords/async) anahtar sözcüğü, `Task<T>` return değeri, `await` anahtar sözcüğü ve `ToListAsync` yöntemi kodu zaman uyumsuz olarak yürütür.</span><span class="sxs-lookup"><span data-stu-id="95499-834">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="95499-835">`async`Anahtar sözcüğü derleyiciye şunu söyler:</span><span class="sxs-lookup"><span data-stu-id="95499-835">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="95499-836">Yöntem gövdesinin parçaları için geri çağrılar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="95499-836">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="95499-837">Döndürülen [görev](/dotnet/api/system.threading.tasks.task) nesnesini otomatik olarak oluşturun.</span><span class="sxs-lookup"><span data-stu-id="95499-837">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="95499-838">Daha fazla bilgi için bkz. [görev dönüş türü](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="95499-838">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="95499-839">Örtük dönüş türü, `Task` devam eden işi temsil eder.</span><span class="sxs-lookup"><span data-stu-id="95499-839">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="95499-840">`await`Anahtar sözcüğü, derleyicinin yöntemi iki parçaya böetmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="95499-840">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="95499-841">İlk bölüm, zaman uyumsuz olarak başlatılan işlemle biter.</span><span class="sxs-lookup"><span data-stu-id="95499-841">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="95499-842">İkinci bölüm, işlem tamamlandığında çağrılan bir geri çağırma yöntemine konur.</span><span class="sxs-lookup"><span data-stu-id="95499-842">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="95499-843">`ToListAsync` , Uzantı yönteminin zaman uyumsuz sürümüdür `ToList` .</span><span class="sxs-lookup"><span data-stu-id="95499-843">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="95499-844">EF Core kullanan zaman uyumsuz kodu yazarken dikkat edilmesi gereken bazı şeyler:</span><span class="sxs-lookup"><span data-stu-id="95499-844">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="95499-845">Yalnızca sorguları veya komutlarının VERITABANıNA gönderilmesine neden olan deyimler zaman uyumsuz olarak yürütülür.</span><span class="sxs-lookup"><span data-stu-id="95499-845">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="95499-846">,,, `ToListAsync` `SingleOrDefaultAsync` Ve içerir `FirstOrDefaultAsync` `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="95499-846">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="95499-847">Yalnızca, gibi bir değiştiren deyimler içermez `IQueryable` `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="95499-847">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="95499-848">EF Core bağlamı iş parçacığı açısından güvenli değildir: paralel olarak birden çok işlem yapmayı denemeyin.</span><span class="sxs-lookup"><span data-stu-id="95499-848">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="95499-849">Zaman uyumsuz kodun performans avantajlarından yararlanmak için, VERITABANıNA sorgu gönderen EF Core yöntemlerini çağırıyorsa kitaplık paketlerinin (örneğin, sayfalama için) zaman uyumsuz olarak kullanılacağını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="95499-849">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="95499-850">.NET 'te zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. Async [and await ile](/dotnet/csharp/programming-guide/concepts/async/)zaman uyumsuz [genel bakış](/dotnet/standard/async) ve zaman uyumsuz programlama</span><span class="sxs-lookup"><span data-stu-id="95499-850">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="95499-851">Sonraki öğreticide, temel CRUD (oluşturma, okuma, güncelleştirme, silme) işlemleri incelenir.</span><span class="sxs-lookup"><span data-stu-id="95499-851">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="95499-852">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="95499-852">Additional resources</span></span>

* [<span data-ttu-id="95499-853">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="95499-853">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="95499-854">Sonraki</span><span class="sxs-lookup"><span data-stu-id="95499-854">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
