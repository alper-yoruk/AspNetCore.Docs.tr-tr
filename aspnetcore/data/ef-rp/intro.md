---
title: 'Razor ASP.NET Core Entity Framework Core olan sayfalar-öğretici 1/8'
author: rick-anderson
description: RazorEntity Framework Core kullanarak bir sayfalar uygulamasının nasıl oluşturulacağını gösterir
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: data/ef-rp/intro
ms.openlocfilehash: 5849f4bfb9d0355177ceb5c2f4236c6d39dd9e92
ms.sourcegitcommit: bb475e69cb647f22cf6d2c6f93d0836c160080d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2020
ms.locfileid: "94340042"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="576dc-103">Razor ASP.NET Core Entity Framework Core olan sayfalar-öğretici 1/8</span><span class="sxs-lookup"><span data-stu-id="576dc-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="576dc-104">, [Tom Dykstra](https://github.com/tdykstra) ve [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından</span><span class="sxs-lookup"><span data-stu-id="576dc-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="576dc-105">Bu, bir [ASP.NET Core Razor sayfaları](xref:razor-pages/index) UYGULAMASıNDA Entity Framework (EF) çekirdeğini nasıl kullanacağınızı gösteren bir öğretici serisinin ilkisidir.</span><span class="sxs-lookup"><span data-stu-id="576dc-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="576dc-106">Öğreticiler, kurgusal bir Contoso Üniversitesi için bir Web sitesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="576dc-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="576dc-107">Site, öğrenci giriş, kurs oluşturma ve eğitmen atamaları gibi işlevleri içerir.</span><span class="sxs-lookup"><span data-stu-id="576dc-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="576dc-108">Öğretici ilk kod yaklaşımını kullanır.</span><span class="sxs-lookup"><span data-stu-id="576dc-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="576dc-109">Bu öğreticiyi veritabanı ilk yaklaşımı kullanarak takip eden bilgiler için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/16897)bakın.</span><span class="sxs-lookup"><span data-stu-id="576dc-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="576dc-110">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="576dc-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="576dc-111">[Yönergeleri indirin](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="576dc-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="576dc-112">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="576dc-112">Prerequisites</span></span>

* <span data-ttu-id="576dc-113">Sayfalarla yeni başladıysanız Razor , bunu başlatmadan önce [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) öğreticisini izleyin.</span><span class="sxs-lookup"><span data-stu-id="576dc-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="576dc-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="576dc-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="576dc-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="576dc-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="576dc-116">Veritabanı altyapıları</span><span class="sxs-lookup"><span data-stu-id="576dc-116">Database engines</span></span>

<span data-ttu-id="576dc-117">Visual Studio yönergeleri, yalnızca Windows üzerinde çalışan bir SQL Server Express sürümü olan [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)'yi kullanır.</span><span class="sxs-lookup"><span data-stu-id="576dc-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="576dc-118">Visual Studio Code yönergeler, platformlar arası bir veritabanı altyapısı olan [SQLite](https://www.sqlite.org/)kullanır.</span><span class="sxs-lookup"><span data-stu-id="576dc-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="576dc-119">SQLite kullanmayı seçerseniz, SQLite [Için DB tarayıcısı](https://sqlitebrowser.org/)gibi bir SQLite veritabanını yönetmek ve görüntülemek için üçüncü taraf bir araç indirip yükleyin.</span><span class="sxs-lookup"><span data-stu-id="576dc-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="576dc-120">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="576dc-120">Troubleshooting</span></span>

<span data-ttu-id="576dc-121">Giderebileceğiniz bir sorunla karşılaşırsanız, kodunuzu [Tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="576dc-122">Yardım almanın iyi bir yolu, [ASP.NET Core etiketi](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF Core etiketi](https://stackoverflow.com/questions/tagged/entity-framework-core)kullanılarak StackOverflow.com 'e bir soru göndererek.</span><span class="sxs-lookup"><span data-stu-id="576dc-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="576dc-123">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="576dc-123">The sample app</span></span>

<span data-ttu-id="576dc-124">Bu öğreticilerde oluşturulan uygulama, temel bir üniversite web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="576dc-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="576dc-125">Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="576dc-126">Öğreticide oluşturulan ekranlardan bazıları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="576dc-126">Here are a few of the screens created in the tutorial.</span></span>

![Öğrenciler Dizin sayfası](intro/_static/students-index30.png)

![Öğrenciler düzenleme sayfası](intro/_static/student-edit30.png)

<span data-ttu-id="576dc-129">Bu sitenin kullanıcı arabirimi stili yerleşik proje şablonlarına dayalıdır.</span><span class="sxs-lookup"><span data-stu-id="576dc-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="576dc-130">Öğreticinin odağı, Kullanıcı arabirimini nasıl özelleştireceğinizi değil, ASP.NET Core EF Core nasıl kullanacağınızı kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="576dc-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

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

## <a name="create-the-web-app-project"></a><span data-ttu-id="576dc-131">Web uygulaması projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="576dc-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="576dc-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="576dc-132">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="576dc-133">Visual Studio 'Yu başlatın ve **Yeni proje oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="576dc-133">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="576dc-134">**Yeni proje oluştur** iletişim kutusunda **ASP.NET Core Web uygulaması** > **İleri** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="576dc-134">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="576dc-135">**Yeni projenizi yapılandırın** Iletişim kutusunda `ContosoUniversity` **Proje adı** ' nı girin.</span><span class="sxs-lookup"><span data-stu-id="576dc-135">In the **Configure your new project** dialog, enter `ContosoUniversity` for **Project name**.</span></span> <span data-ttu-id="576dc-136">Büyük harfler de dahil olmak üzere bu tam adı kullanmak önemlidir. bu nedenle, `namespace` Kod kopyalanırken her biri eşleşir.</span><span class="sxs-lookup"><span data-stu-id="576dc-136">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="576dc-137">**Oluştur** ’u seçin.</span><span class="sxs-lookup"><span data-stu-id="576dc-137">Select **Create**.</span></span>
1. <span data-ttu-id="576dc-138">**Yeni ASP.NET Core Web uygulaması oluştur** iletişim kutusunda şunları seçin:</span><span class="sxs-lookup"><span data-stu-id="576dc-138">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="576dc-139">**.NET Core** ve **ASP.NET Core 5,0** açılır.</span><span class="sxs-lookup"><span data-stu-id="576dc-139">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="576dc-140">**ASP.NET Core Web uygulaması (Model-View-Controller)**.</span><span class="sxs-lookup"><span data-stu-id="576dc-140">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="576dc-141">**Create** 
       Oluştur ![ Yeni ASP.NET Core projesi iletişim kutusu](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span><span class="sxs-lookup"><span data-stu-id="576dc-141">**Create**
![New ASP.NET Core Project dialog](~/data/ef-mvc/intro/_static/new-aspnet5.png)</span></span>
    
# <a name="visual-studio-code"></a>[<span data-ttu-id="576dc-142">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="576dc-142">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="576dc-143">Bir terminalde, proje klasörünün oluşturulması gereken klasöre gidin.</span><span class="sxs-lookup"><span data-stu-id="576dc-143">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="576dc-144">Bir Razor sayfa projesi ve yeni proje klasörü oluşturmak için aşağıdaki komutları çalıştırın `cd` :</span><span class="sxs-lookup"><span data-stu-id="576dc-144">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="576dc-145">Site stilini ayarlayın</span><span class="sxs-lookup"><span data-stu-id="576dc-145">Set up the site style</span></span>

<span data-ttu-id="576dc-146">Aşağıdaki kodu kopyalayıp *Sayfalar/paylaşılan/_Layout. cshtml* dosyasına yapıştırın: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span><span class="sxs-lookup"><span data-stu-id="576dc-146">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="576dc-147">Düzen dosyası site üst bilgisini, alt bilgisini ve menüsünü ayarlar.</span><span class="sxs-lookup"><span data-stu-id="576dc-147">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="576dc-148">Yukarıdaki kod aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="576dc-148">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="576dc-149">Her "ContosoUniversity" öğesinin "Contoso Üniversitesi" olarak her oluşumu.</span><span class="sxs-lookup"><span data-stu-id="576dc-149">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="576dc-150">Üç oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="576dc-150">There are three occurrences.</span></span>
* <span data-ttu-id="576dc-151">**Giriş** ve **Gizlilik** menü girişleri silinir.</span><span class="sxs-lookup"><span data-stu-id="576dc-151">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="576dc-152">, **Öğrenciler** , **Kurslar** , **eğitmenler** ve **Departmanlar** **için girişler** eklenir.</span><span class="sxs-lookup"><span data-stu-id="576dc-152">Entries are added for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="576dc-153">*Pages/Index. cshtml* dosyasında, dosyanın içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="576dc-153">In *Pages/Index.cshtml* , replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="576dc-154">Yukarıdaki kod, ASP.NET Core ile ilgili metnin yerine bu uygulamayla ilgili metni koyar.</span><span class="sxs-lookup"><span data-stu-id="576dc-154">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="576dc-155">Giriş sayfasının göründüğünü doğrulamak için uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-155">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="576dc-156">Veri modeli</span><span class="sxs-lookup"><span data-stu-id="576dc-156">The data model</span></span>

<span data-ttu-id="576dc-157">Aşağıdaki bölümler bir veri modeli oluşturur:</span><span class="sxs-lookup"><span data-stu-id="576dc-157">The following sections create a data model:</span></span>

![Kurs-kayıt-öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

<span data-ttu-id="576dc-159">Bir öğrenci herhangi bir sayıda kursa kaydolabilir ve bir kurs, kayıtlı sayıda öğrenciye sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-159">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="576dc-160">Öğrenci varlığı</span><span class="sxs-lookup"><span data-stu-id="576dc-160">The Student entity</span></span>

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

* <span data-ttu-id="576dc-162">Proje klasöründe bir *modeller* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="576dc-162">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="576dc-163">Aşağıdaki kodla *modeller/öğrenci. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="576dc-163">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="576dc-164">`ID`Özelliği, bu sınıfa karşılık gelen veritabanı tablosunun birincil anahtar sütunu olur.</span><span class="sxs-lookup"><span data-stu-id="576dc-164">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="576dc-165">Varsayılan olarak, EF Core `ID` birincil anahtar olarak adlandırılan bir özelliği Yorumlar `classnameID` .</span><span class="sxs-lookup"><span data-stu-id="576dc-165">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="576dc-166">Bu nedenle birincil anahtar sınıfı için alternatif otomatik olarak tanınan ad `Student` olur `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="576dc-166">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="576dc-167">Daha fazla bilgi için bkz. [EF Core-anahtarlar](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="576dc-167">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="576dc-168">`Enrollments`Özelliği bir [Gezinti özelliğidir](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="576dc-168">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="576dc-169">Gezinti özellikleri, bu varlıkla ilgili diğer varlıkları tutar.</span><span class="sxs-lookup"><span data-stu-id="576dc-169">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="576dc-170">Bu durumda, `Enrollments` bir `Student` varlığın özelliği `Enrollment` söz konusu öğrenciye ilişkin tüm varlıkları barındırır.</span><span class="sxs-lookup"><span data-stu-id="576dc-170">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="576dc-171">Örneğin, veritabanındaki bir öğrenci satırında iki ilişkili kayıt satırı varsa, `Enrollments` gezinti özelliği bu Iki kayıt varlığını içerir.</span><span class="sxs-lookup"><span data-stu-id="576dc-171">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="576dc-172">Veritabanında, bir kayıt satırı, Studentitıd sütunu öğrencinin ID değerini içeriyorsa bir öğrenci satırıyla ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="576dc-172">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="576dc-173">Örneğin, bir öğrenci satırının ID = 1 olduğunu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="576dc-173">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="576dc-174">İlgili kayıt satırları Studentitıd = 1 olacaktır.</span><span class="sxs-lookup"><span data-stu-id="576dc-174">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="576dc-175">Studentitıd, kayıt tablosundaki bir *yabancı anahtardır* .</span><span class="sxs-lookup"><span data-stu-id="576dc-175">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="576dc-176">`Enrollments`Özelliği, `ICollection<Enrollment>` birden çok ilgili kayıt varlığı olabileceğinden, olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="576dc-176">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="576dc-177">Veya gibi diğer koleksiyon türlerini kullanabilirsiniz `List<Enrollment>` `HashSet<Enrollment>` .</span><span class="sxs-lookup"><span data-stu-id="576dc-177">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="576dc-178">Kullanıldığında `ICollection<Enrollment>` , EF Core `HashSet<Enrollment>` Varsayılan olarak bir koleksiyon oluşturur.</span><span class="sxs-lookup"><span data-stu-id="576dc-178">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="576dc-179">Kayıt varlığı</span><span class="sxs-lookup"><span data-stu-id="576dc-179">The Enrollment entity</span></span>

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

<span data-ttu-id="576dc-181">Aşağıdaki kodla *modeller/kayıt. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="576dc-181">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="576dc-182">`EnrollmentID`Bu özellik birincil anahtardır; bu varlık kendi başına değil, `classnameID` kendi modelini kullanır `ID` .</span><span class="sxs-lookup"><span data-stu-id="576dc-182">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="576dc-183">Bir üretim veri modeli için bir model seçin ve bunu tutarlı bir şekilde kullanın.</span><span class="sxs-lookup"><span data-stu-id="576dc-183">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="576dc-184">Bu öğretici her ikisinin de yalnızca bir iş olduğunu göstermek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="576dc-184">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="576dc-185">`ID`Olmadan kullanmak `classname` , bazı veri modeli değişikliklerinin uygulanmasını kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="576dc-185">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="576dc-186">`Grade`Özelliği bir `enum` .</span><span class="sxs-lookup"><span data-stu-id="576dc-186">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="576dc-187">Tür bildiriminden sonraki soru işareti, `Grade` `Grade` özelliğin [null yapılabilir](/dotnet/csharp/programming-guide/nullable-types/)olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="576dc-187">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="576dc-188">Null olan bir sınıf sıfır bir sınıf null değerinden farklıdır &mdash; veya henüz atanmamış olur.</span><span class="sxs-lookup"><span data-stu-id="576dc-188">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="576dc-189">`StudentID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Student` .</span><span class="sxs-lookup"><span data-stu-id="576dc-189">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="576dc-190">Bir varlık `Enrollment` bir `Student` varlıkla ilişkilendirilir, bu nedenle özellik tek bir varlık içerir `Student` .</span><span class="sxs-lookup"><span data-stu-id="576dc-190">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="576dc-191">`CourseID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Course` .</span><span class="sxs-lookup"><span data-stu-id="576dc-191">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="576dc-192">Bir `Enrollment` varlık bir `Course` varlıkla ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-192">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="576dc-193">EF Core bir özelliği, adlandırılmış ise yabancı anahtar olarak yorumlar `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="576dc-193">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="576dc-194">Örneğin, `StudentID` `Student` `Student` nesnenin birincil anahtarı olduğundan, gezinti özelliği için yabancı anahtardır `ID` .</span><span class="sxs-lookup"><span data-stu-id="576dc-194">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="576dc-195">Yabancı anahtar özellikleri de adlandırılmış olabilir `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="576dc-195">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="576dc-196">Örneğin, `CourseID` `Course` varlığın birincil anahtarı olduğundan `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="576dc-196">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="576dc-197">Kurs varlığı</span><span class="sxs-lookup"><span data-stu-id="576dc-197">The Course entity</span></span>

![Kurs varlık diyagramı](intro/_static/course-entity.png)

<span data-ttu-id="576dc-199">Aşağıdaki kodla *modeller/kurs. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="576dc-199">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="576dc-200">`Enrollments`Özelliği bir gezinti özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="576dc-200">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="576dc-201">Bir `Course` varlık herhangi bir sayıda varlıkla ilişkili olabilir `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="576dc-201">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="576dc-202">`DatabaseGenerated`Özniteliği, uygulamanın veritabanını oluşturmak yerine birincil anahtarı belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="576dc-202">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="576dc-203">Derleyici hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="576dc-203">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="576dc-204">Yapı iskelesi öğrenci sayfaları</span><span class="sxs-lookup"><span data-stu-id="576dc-204">Scaffold Student pages</span></span>

<span data-ttu-id="576dc-205">Bu bölümde, oluşturmak için ASP.NET Core scafkatlama aracını kullanırsınız:</span><span class="sxs-lookup"><span data-stu-id="576dc-205">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="576dc-206">EF Core `DbContext` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="576dc-206">An EF Core `DbContext` class.</span></span> <span data-ttu-id="576dc-207">Bağlam, belirli bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="576dc-207">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="576dc-208"><xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>Sınıfından türetilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-208">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="576dc-209">Razor varlık için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemlerini işleyen sayfalar `Student` .</span><span class="sxs-lookup"><span data-stu-id="576dc-209">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="576dc-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="576dc-210">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="576dc-211">*Sayfalar/öğrenciler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="576dc-211">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="576dc-212">**Çözüm Gezgini** , *Sayfalar/öğrenciler* klasörüne sağ tıklayın ve **Add** > **yeni yapı iskelesi Ekle öğesini** seçin.</span><span class="sxs-lookup"><span data-stu-id="576dc-212">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="576dc-213">**Yeni yapı Iskelesi öğesi Ekle** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="576dc-213">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="576dc-214">Sol sekmede, **yüklü > ortak > Razor sayfaları** ' nı seçin</span><span class="sxs-lookup"><span data-stu-id="576dc-214">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="576dc-215">**Razor Entity Framework (CRUD) Ekle kullanarak sayfa** seçin > **ADD**.</span><span class="sxs-lookup"><span data-stu-id="576dc-215">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="576dc-216">**Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="576dc-216">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="576dc-217">**Model sınıfı** açılır penceresinde **öğrenci (Contosouniversity. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="576dc-217">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="576dc-218">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin.</span><span class="sxs-lookup"><span data-stu-id="576dc-218">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="576dc-219">Veri bağlamı adını yerine End ile değiştirin `SchoolContext` `ContosoUniversityContext` .</span><span class="sxs-lookup"><span data-stu-id="576dc-219">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="576dc-220">Güncelleştirilmiş bağlam adı: `ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="576dc-220">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="576dc-221">**Ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="576dc-221">Select **Add**.</span></span>

<span data-ttu-id="576dc-222">Aşağıdaki paketler otomatik olarak yüklenir:</span><span class="sxs-lookup"><span data-stu-id="576dc-222">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="576dc-223">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="576dc-223">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="576dc-224">Gerekli NuGet paketlerini yüklemek için aşağıdaki .NET Core CLI komutlarını çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="576dc-224">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="576dc-225">Yapı iskelesi için Microsoft. VisualStudio. Web. CodeGeneration. Design paketi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="576dc-225">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="576dc-226">Uygulama SQL Server kullanmayabilse de, scafkatlama aracı SQL Server paketine ihtiyaç duyuyor.</span><span class="sxs-lookup"><span data-stu-id="576dc-226">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="576dc-227">*Sayfalar/öğrenciler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="576dc-227">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="576dc-228">[ASPNET-CodeGenerator scafkatlama aracı](xref:fundamentals/tools/dotnet-aspnet-codegenerator)'nı yüklemek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-228">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="576dc-229">Fkatlama öğrenci sayfalarını iskele almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-229">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="576dc-230">**Windows üzerinde**</span><span class="sxs-lookup"><span data-stu-id="576dc-230">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="576dc-231">**MacOS veya Linux üzerinde**</span><span class="sxs-lookup"><span data-stu-id="576dc-231">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="576dc-232">Önceki adım başarısız olursa, projeyi derleyin ve iskele adımını yeniden deneyin.</span><span class="sxs-lookup"><span data-stu-id="576dc-232">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="576dc-233">Yapı iskelesi işlemi:</span><span class="sxs-lookup"><span data-stu-id="576dc-233">The scaffolding process:</span></span>

* <span data-ttu-id="576dc-234">Razor *Sayfalar/öğrenciler* klasöründe sayfa oluşturur:</span><span class="sxs-lookup"><span data-stu-id="576dc-234">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="576dc-235">*. Cshtml* ve *Create.cshtml.cs* oluşturma</span><span class="sxs-lookup"><span data-stu-id="576dc-235">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="576dc-236">*Delete. cshtml* ve *delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="576dc-236">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="576dc-237">*Details. cshtml* ve *details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="576dc-237">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="576dc-238">*. Cshtml* ve *Edit.cshtml.cs* Düzenle</span><span class="sxs-lookup"><span data-stu-id="576dc-238">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="576dc-239">*Index. cshtml* ve *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="576dc-239">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="576dc-240">*Data/SchoolContext. cs* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="576dc-240">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="576dc-241">*Startup.cs* içinde bağımlılık eklenmesine bağlam ekler.</span><span class="sxs-lookup"><span data-stu-id="576dc-241">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="576dc-242">Öğesine bir veritabanı bağlantı dizesi ekler *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="576dc-242">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="576dc-243">Veritabanı bağlantı dizesi</span><span class="sxs-lookup"><span data-stu-id="576dc-243">Database connection string</span></span>

<span data-ttu-id="576dc-244">Scafkatlama aracı dosyada bir bağlantı dizesi oluşturur *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="576dc-244">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="576dc-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="576dc-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="576dc-246">Bağlantı dizesi [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)belirtir:</span><span class="sxs-lookup"><span data-stu-id="576dc-246">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="576dc-247">LocalDB, SQL Server Express veritabanı altyapısının hafif bir sürümüdür ve üretim kullanımı için değil uygulama geliştirmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="576dc-247">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="576dc-248">Varsayılan olarak, LocalDB dizinde *. mdf* dosyaları oluşturur `C:/Users/<user>` .</span><span class="sxs-lookup"><span data-stu-id="576dc-248">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="576dc-249">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="576dc-249">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="576dc-250">*. Db* için SQLite bağlantı dizesini kısaltın:</span><span class="sxs-lookup"><span data-stu-id="576dc-250">Shorten the SQLite connection string to *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="576dc-251">Veritabanı bağlam sınıfını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="576dc-251">Update the database context class</span></span>

<span data-ttu-id="576dc-252">Belirli bir veri modeli için EF Core işlevselliğini koordine eden ana sınıf veritabanı bağlamı sınıfıdır.</span><span class="sxs-lookup"><span data-stu-id="576dc-252">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="576dc-253">Bağlam [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-253">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="576dc-254">Bağlam, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="576dc-254">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="576dc-255">Bu projede, sınıfı olarak adlandırılır `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="576dc-255">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="576dc-256">*Data/SchoolContext. cs* öğesini şu kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="576dc-256">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="576dc-257">Yukarıdaki kod tekil ile çoğul arasında değişir `DbSet<Student> Student` `DbSet<Student> Students` .</span><span class="sxs-lookup"><span data-stu-id="576dc-257">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="576dc-258">RazorSayfa kodunun yeni adla eşleşmesini sağlamak için `DBSet` , ' den küresel bir değişiklik yapın:`_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="576dc-258">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="576dc-259">Hedef: `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="576dc-259">to: `_context.Students.`</span></span>

<span data-ttu-id="576dc-260">8 oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="576dc-260">There are 8 occurrences.</span></span>

<span data-ttu-id="576dc-261">Bir varlık kümesi birden çok varlık içerdiğinden birçok geliştirici, `DBSet` özellik adlarının çoğul olması gerektiğini tercih eder.</span><span class="sxs-lookup"><span data-stu-id="576dc-261">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="576dc-262">Vurgulanan kod:</span><span class="sxs-lookup"><span data-stu-id="576dc-262">The highlighted code:</span></span>

* <span data-ttu-id="576dc-263">Her bir varlık kümesi için bir [Dbset \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="576dc-263">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="576dc-264">EF Core terminoloji:</span><span class="sxs-lookup"><span data-stu-id="576dc-264">In EF Core terminology:</span></span>
  * <span data-ttu-id="576dc-265">Bir varlık kümesi, genellikle bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="576dc-265">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="576dc-266">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="576dc-266">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="576dc-267">Çağırır <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .</span><span class="sxs-lookup"><span data-stu-id="576dc-267">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="576dc-268">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="576dc-268">`OnModelCreating`:</span></span>
  * <span data-ttu-id="576dc-269">Başlatıldığı zaman çağrılır `SchoolContext` , ancak model kilitlenmeden önce ve bağlamı başlatmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="576dc-269">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="576dc-270">Daha sonraki öğreticide, `Student` varlığın diğer varlıklara başvuruları olacağı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="576dc-270">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="576dc-271">Derleyici hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="576dc-271">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="576dc-272">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="576dc-272">Startup.cs</span></span>

<span data-ttu-id="576dc-273">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="576dc-273">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="576dc-274">Gibi hizmetler, `SchoolContext` uygulamanın başlatılması sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-274">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="576dc-275">Sayfalar gibi bu hizmetleri gerektiren bileşenler Razor , bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="576dc-275">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="576dc-276">Bir veritabanı bağlamı örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="576dc-276">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="576dc-277">Scafkatlama Aracı, bağlam sınıfını bağımlılık ekleme kapsayıcısına otomatik olarak kaydetti.</span><span class="sxs-lookup"><span data-stu-id="576dc-277">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="576dc-278">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="576dc-278">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="576dc-279">Aşağıdaki Vurgulanan satırlar scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="576dc-279">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="576dc-280">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="576dc-280">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="576dc-281">Desteği çağrıları tarafından eklenen kodu doğrulayın `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="576dc-281">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="576dc-282">Üretim veritabanını kullanma hakkında bilgi için bkz. [geliştirme Için SQLite kullanma, üretime yönelik SQL Server](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) .</span><span class="sxs-lookup"><span data-stu-id="576dc-282">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="576dc-283">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-283">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="576dc-284">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="576dc-284">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="576dc-285">Veritabanı özel durum filtresini ekleme</span><span class="sxs-lookup"><span data-stu-id="576dc-285">Add the database exception filter</span></span>

<span data-ttu-id="576dc-286"><xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> `ConfigureServices` Aşağıdaki kodda gösterildiği gibi öğesine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="576dc-286">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="576dc-287">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="576dc-287">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="576dc-288">[Microsoft. aspnetcore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="576dc-288">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="576dc-289">Şu şekilde, NuGet paketini eklemek için aşağıdakileri girin:</span><span class="sxs-lookup"><span data-stu-id="576dc-289">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="576dc-290">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="576dc-290">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="576dc-291">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore`NuGet paketi, Entity Framework Core hata sayfaları için ASP.NET Core ara yazılım sağlar.</span><span class="sxs-lookup"><span data-stu-id="576dc-291">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="576dc-292">Bu ara yazılım Entity Framework Core geçişlerle hataları algılamaya ve tanılamanıza yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="576dc-292">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="576dc-293">, `AddDatabaseDeveloperPageExceptionFilter` [Geliştirme ortamında](xref:fundamentals/environments)yararlı hata bilgileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="576dc-293">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="576dc-294">Veritabanını oluşturma</span><span class="sxs-lookup"><span data-stu-id="576dc-294">Create the database</span></span>

<span data-ttu-id="576dc-295">Mevcut değilse veritabanını oluşturmak için *program.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="576dc-295">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="576dc-296">Bağlam için bir veritabanı varsa, [Ensuyeniden](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) oluşturma yöntemi hiçbir eylemde bulunmaz.</span><span class="sxs-lookup"><span data-stu-id="576dc-296">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="576dc-297">Veritabanı yoksa, veritabanını ve şemayı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="576dc-297">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="576dc-298">`EnsureCreated` veri modeli değişikliklerini işlemek için aşağıdaki iş akışını sunar:</span><span class="sxs-lookup"><span data-stu-id="576dc-298">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="576dc-299">Veritabanını silin.</span><span class="sxs-lookup"><span data-stu-id="576dc-299">Delete the database.</span></span> <span data-ttu-id="576dc-300">Mevcut veriler kaybolur.</span><span class="sxs-lookup"><span data-stu-id="576dc-300">Any existing data is lost.</span></span>
* <span data-ttu-id="576dc-301">Veri modelini değiştirin.</span><span class="sxs-lookup"><span data-stu-id="576dc-301">Change the data model.</span></span> <span data-ttu-id="576dc-302">Örneğin, bir alan ekleyin `EmailAddress` .</span><span class="sxs-lookup"><span data-stu-id="576dc-302">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="576dc-303">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-303">Run the app.</span></span>
* <span data-ttu-id="576dc-304">`EnsureCreated` yeni şemaya sahip bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="576dc-304">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="576dc-305">Bu iş akışı, verileri korumanıza gerek olmadığı sürece, şema hızlı bir şekilde gelişen zaman geliştirme aşamasında iyi bir şekilde gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-305">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="576dc-306">Veritabanına girilen verilerin korunması gerektiğinde bu durum farklıdır.</span><span class="sxs-lookup"><span data-stu-id="576dc-306">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="576dc-307">Bu durumda, geçişleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="576dc-307">When that is the case, use migrations.</span></span>

<span data-ttu-id="576dc-308">Öğretici serisinde daha sonra tarafından oluşturulan veritabanını siler `EnsureCreated` ve bunun yerine geçişleri kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="576dc-308">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="576dc-309">Tarafından oluşturulan bir veritabanı, `EnsureCreated` geçişler kullanılarak güncelleştirilemiyor.</span><span class="sxs-lookup"><span data-stu-id="576dc-309">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="576dc-310">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="576dc-310">Test the app</span></span>

* <span data-ttu-id="576dc-311">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-311">Run the app.</span></span>
* <span data-ttu-id="576dc-312">**Öğrenciler** bağlantısını seçin ve ardından **Yeni oluştur**.</span><span class="sxs-lookup"><span data-stu-id="576dc-312">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="576dc-313">Düzenle, Ayrıntılar ve Sil bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="576dc-313">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="576dc-314">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="576dc-314">Seed the database</span></span>

<span data-ttu-id="576dc-315">`EnsureCreated`Yöntemi boş bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="576dc-315">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="576dc-316">Bu bölüm, veritabanını test verileriyle dolduran kodu ekler.</span><span class="sxs-lookup"><span data-stu-id="576dc-316">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="576dc-317">Aşağıdaki kodla *veri/Dbınizer. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="576dc-317">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="576dc-318">Kod, veritabanında herhangi bir öğrenci olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="576dc-318">The code checks if there are any students in the database.</span></span> <span data-ttu-id="576dc-319">Öğrenci yoksa, veritabanına test verileri ekler.</span><span class="sxs-lookup"><span data-stu-id="576dc-319">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="576dc-320">Performansı iyileştirmek için Koleksiyonlar yerine diziler halinde test verileri oluşturur `List<T>` .</span><span class="sxs-lookup"><span data-stu-id="576dc-320">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="576dc-321">*Program.cs* içinde, `EnsureCreated` çağrıyı bir çağrı ile değiştirin `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="576dc-321">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="576dc-322">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="576dc-322">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="576dc-323">Çalışıyorsa uygulamayı durdurun ve **Paket Yöneticisi konsolunda** (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="576dc-323">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="576dc-324">`Y`Veritabanını silmek için ile yanıtlayın.</span><span class="sxs-lookup"><span data-stu-id="576dc-324">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="576dc-325">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="576dc-325">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="576dc-326">Çalışıyorsa uygulamayı durdurun ve *cu. db* dosyasını silin.</span><span class="sxs-lookup"><span data-stu-id="576dc-326">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="576dc-327">Uygulamayı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="576dc-327">Restart the app.</span></span>
* <span data-ttu-id="576dc-328">Sağlanan verileri görmek için öğrenciler sayfasını seçin.</span><span class="sxs-lookup"><span data-stu-id="576dc-328">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="576dc-329">Veritabanını görüntüleme</span><span class="sxs-lookup"><span data-stu-id="576dc-329">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="576dc-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="576dc-330">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="576dc-331">Visual Studio 'daki **Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="576dc-331">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="576dc-332">SSOX 'te, **(LocalDB) \MSSQLLocalDB > veritabanları > SchoolContext-{GUID}** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="576dc-332">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="576dc-333">Veritabanı adı, daha önce belirttiğiniz bağlam adından ve bir tire ve bir GUID ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="576dc-333">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="576dc-334">**Tables** düğümünü genişletin.</span><span class="sxs-lookup"><span data-stu-id="576dc-334">Expand the **Tables** node.</span></span>
* <span data-ttu-id="576dc-335">Oluşturulan sütunları ve tabloya yerleştirilen satırları görmek için **öğrenci** tablosuna sağ tıklayın ve **verileri görüntüle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="576dc-335">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="576dc-336">Modelin tablo şemasına nasıl eşlendiğini görmek için **öğrenci** tablosuna sağ tıklayın ve **kodu görüntüle** ' ye tıklayın `Student` `Student` .</span><span class="sxs-lookup"><span data-stu-id="576dc-336">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="576dc-337">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="576dc-337">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="576dc-338">Veritabanı şemasını ve sağlanan verileri görüntülemek için SQLite aracınızı kullanın.</span><span class="sxs-lookup"><span data-stu-id="576dc-338">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="576dc-339">Veritabanı dosyası *cu. db* olarak adlandırılır ve proje klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="576dc-339">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="576dc-340">Zaman uyumsuz kod</span><span class="sxs-lookup"><span data-stu-id="576dc-340">Asynchronous code</span></span>

<span data-ttu-id="576dc-341">Zaman uyumsuz programlama, ASP.NET Core ve EF Core için varsayılan moddur.</span><span class="sxs-lookup"><span data-stu-id="576dc-341">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="576dc-342">Web sunucusunda sınırlı sayıda iş parçacığı bulunur ve yüksek yük durumlarında tüm kullanılabilir iş parçacıkları kullanımda olabilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-342">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="576dc-343">Bu durumda, sunucu, iş parçacıkları boşaltılana kadar yeni istekleri işleyemez.</span><span class="sxs-lookup"><span data-stu-id="576dc-343">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="576dc-344">Zaman uyumlu kod ile, çok sayıda iş parçacığı g/ç 'nin tamamlanmasını beklediği için iş çalışmadıklarında bağlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-344">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="576dc-345">Zaman uyumsuz kod ile, bir işlem g/ç 'yi tamamlanmayı beklerken, sunucunun diğer istekleri işlemek için kullanması için iş parçacığı serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="576dc-345">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="576dc-346">Sonuç olarak, zaman uyumsuz kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucu gecikmeksizin daha fazla trafiği işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-346">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="576dc-347">Zaman uyumsuz kod, çalışma zamanında az miktarda yük getirir.</span><span class="sxs-lookup"><span data-stu-id="576dc-347">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="576dc-348">Düşük trafik durumlarında, performans artışı göz ardı edilebilir, ancak yüksek trafik durumları için olası performans iyileştirmesi oldukça önemlidir.</span><span class="sxs-lookup"><span data-stu-id="576dc-348">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="576dc-349">Aşağıdaki kodda, [Async](/dotnet/csharp/language-reference/keywords/async) anahtar sözcüğü, `Task<T>` return değeri, `await` anahtar sözcüğü ve `ToListAsync` yöntemi kodu zaman uyumsuz olarak yürütür.</span><span class="sxs-lookup"><span data-stu-id="576dc-349">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="576dc-350">`async`Anahtar sözcüğü derleyiciye şunu söyler:</span><span class="sxs-lookup"><span data-stu-id="576dc-350">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="576dc-351">Yöntem gövdesinin parçaları için geri çağrılar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="576dc-351">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="576dc-352">Döndürülen [görev](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) nesnesini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="576dc-352">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="576dc-353">`Task<T>`Dönüş türü, devam eden işi temsil eder.</span><span class="sxs-lookup"><span data-stu-id="576dc-353">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="576dc-354">`await`Anahtar sözcüğü, derleyicinin yöntemi iki parçaya böetmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="576dc-354">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="576dc-355">İlk bölüm, zaman uyumsuz olarak başlatılan işlemle biter.</span><span class="sxs-lookup"><span data-stu-id="576dc-355">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="576dc-356">İkinci bölüm, işlem tamamlandığında çağrılan bir geri çağırma yöntemine konur.</span><span class="sxs-lookup"><span data-stu-id="576dc-356">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="576dc-357">`ToListAsync` , Uzantı yönteminin zaman uyumsuz sürümüdür `ToList` .</span><span class="sxs-lookup"><span data-stu-id="576dc-357">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="576dc-358">EF Core kullanan zaman uyumsuz kodu yazarken dikkat edilmesi gereken bazı şeyler:</span><span class="sxs-lookup"><span data-stu-id="576dc-358">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="576dc-359">Yalnızca sorguları veya komutlarının veritabanına gönderilmesine neden olan deyimler zaman uyumsuz olarak yürütülür.</span><span class="sxs-lookup"><span data-stu-id="576dc-359">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="576dc-360">,, `ToListAsync` , `SingleOrDefaultAsync` `FirstOrDefaultAsync` Ve içerir `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="576dc-360">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="576dc-361">Yalnızca, gibi bir değiştiren deyimler içermez `IQueryable` `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="576dc-361">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="576dc-362">EF Core bağlamı iş parçacığı açısından güvenli değildir: paralel olarak birden çok işlem yapmayı denemeyin.</span><span class="sxs-lookup"><span data-stu-id="576dc-362">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="576dc-363">Zaman uyumsuz kodun performans avantajlarından yararlanmak için, veritabanına sorgu gönderen EF Core yöntemleri çağırıyorsa kitaplık paketlerinin (örneğin, sayfalama için) zaman uyumsuz olarak kullanılacağını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="576dc-363">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="576dc-364">.NET 'te zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. Async [and await ile](/dotnet/csharp/programming-guide/concepts/async/)zaman uyumsuz [genel bakış](/dotnet/standard/async) ve zaman uyumsuz programlama</span><span class="sxs-lookup"><span data-stu-id="576dc-364">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="576dc-365">Performansla ilgili önemli noktalar</span><span class="sxs-lookup"><span data-stu-id="576dc-365">Performance considerations</span></span>

<span data-ttu-id="576dc-366">Genel olarak, bir Web sayfası rastgele sayıda satır yüklememelidir.</span><span class="sxs-lookup"><span data-stu-id="576dc-366">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="576dc-367">Bir sorgu, sayfalama veya sınırlandırma yaklaşımını kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="576dc-367">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="576dc-368">Örneğin, önceki sorgu `Take` döndürülen satırları sınırlamak için kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="576dc-368">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="576dc-369">Bir görünümde büyük bir tablonun numaralandırılması, bir veritabanı özel durumu numaralandırma aracılığıyla bir bölüm oluşursa kısmen oluşturulmuş bir HTTP 200 yanıtı döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-369">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="576dc-370"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> Varsayılan olarak 1024 ' dir.</span><span class="sxs-lookup"><span data-stu-id="576dc-370"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="576dc-371">Aşağıdaki kod kümeleri `MaxModelBindingCollectionSize` :</span><span class="sxs-lookup"><span data-stu-id="576dc-371">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="576dc-372">Sayfalama daha sonra öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="576dc-372">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="576dc-373">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="576dc-373">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="576dc-374">Sonraki öğretici</span><span class="sxs-lookup"><span data-stu-id="576dc-374">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="576dc-375">Bu, bir [ASP.NET Core Razor sayfaları](xref:razor-pages/index) UYGULAMASıNDA Entity Framework (EF) çekirdeğini nasıl kullanacağınızı gösteren bir öğretici serisinin ilkisidir.</span><span class="sxs-lookup"><span data-stu-id="576dc-375">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="576dc-376">Öğreticiler, kurgusal bir Contoso Üniversitesi için bir Web sitesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="576dc-376">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="576dc-377">Site, öğrenci giriş, kurs oluşturma ve eğitmen atamaları gibi işlevleri içerir.</span><span class="sxs-lookup"><span data-stu-id="576dc-377">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="576dc-378">Öğretici ilk kod yaklaşımını kullanır.</span><span class="sxs-lookup"><span data-stu-id="576dc-378">The tutorial uses the code first approach.</span></span> <span data-ttu-id="576dc-379">Bu öğreticiyi veritabanı ilk yaklaşımı kullanarak takip eden bilgiler için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/16897)bakın.</span><span class="sxs-lookup"><span data-stu-id="576dc-379">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="576dc-380">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="576dc-380">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="576dc-381">[Yönergeleri indirin](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="576dc-381">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="576dc-382">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="576dc-382">Prerequisites</span></span>

* <span data-ttu-id="576dc-383">Sayfalarla yeni başladıysanız Razor , bunu başlatmadan önce [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) öğreticisini izleyin.</span><span class="sxs-lookup"><span data-stu-id="576dc-383">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="576dc-384">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="576dc-384">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="576dc-385">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="576dc-385">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="576dc-386">Veritabanı altyapıları</span><span class="sxs-lookup"><span data-stu-id="576dc-386">Database engines</span></span>

<span data-ttu-id="576dc-387">Visual Studio yönergeleri, yalnızca Windows üzerinde çalışan bir SQL Server Express sürümü olan [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)'yi kullanır.</span><span class="sxs-lookup"><span data-stu-id="576dc-387">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="576dc-388">Visual Studio Code yönergeler, platformlar arası bir veritabanı altyapısı olan [SQLite](https://www.sqlite.org/)kullanır.</span><span class="sxs-lookup"><span data-stu-id="576dc-388">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="576dc-389">SQLite kullanmayı seçerseniz, SQLite [Için DB tarayıcısı](https://sqlitebrowser.org/)gibi bir SQLite veritabanını yönetmek ve görüntülemek için üçüncü taraf bir araç indirip yükleyin.</span><span class="sxs-lookup"><span data-stu-id="576dc-389">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="576dc-390">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="576dc-390">Troubleshooting</span></span>

<span data-ttu-id="576dc-391">Giderebileceğiniz bir sorunla karşılaşırsanız, kodunuzu [Tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-391">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="576dc-392">Yardım almanın iyi bir yolu, [ASP.NET Core etiketi](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF Core etiketi](https://stackoverflow.com/questions/tagged/entity-framework-core)kullanılarak StackOverflow.com 'e bir soru göndererek.</span><span class="sxs-lookup"><span data-stu-id="576dc-392">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="576dc-393">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="576dc-393">The sample app</span></span>

<span data-ttu-id="576dc-394">Bu öğreticilerde oluşturulan uygulama, temel bir üniversite web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="576dc-394">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="576dc-395">Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-395">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="576dc-396">Öğreticide oluşturulan ekranlardan bazıları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="576dc-396">Here are a few of the screens created in the tutorial.</span></span>

![Öğrenciler Dizin sayfası](intro/_static/students-index30.png)

![Öğrenciler düzenleme sayfası](intro/_static/student-edit30.png)

<span data-ttu-id="576dc-399">Bu sitenin kullanıcı arabirimi stili yerleşik proje şablonlarına dayalıdır.</span><span class="sxs-lookup"><span data-stu-id="576dc-399">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="576dc-400">Öğreticinin odağı, Kullanıcı arabirimini nasıl özelleştireceğinizi değil EF Core kullanma konusunda yer alır.</span><span class="sxs-lookup"><span data-stu-id="576dc-400">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="576dc-401">Tamamlanan projenin kaynak kodunu almak için sayfanın üst kısmındaki bağlantıyı izleyin.</span><span class="sxs-lookup"><span data-stu-id="576dc-401">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="576dc-402">*Cu30* klasörü, öğreticinin ASP.NET Core 3,0 sürümü için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="576dc-402">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="576dc-403">1-7 öğreticileri için kodun durumunu yansıtan dosyalar *cu30snapshots* klasöründe bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-403">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="576dc-404">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="576dc-404">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="576dc-405">Tamamlanmış projeyi indirdikten sonra uygulamayı çalıştırmak için:</span><span class="sxs-lookup"><span data-stu-id="576dc-405">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="576dc-406">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="576dc-406">Build the project.</span></span>
* <span data-ttu-id="576dc-407">Paket Yöneticisi konsolu 'nda (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="576dc-407">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="576dc-408">Veritabanını temel alarak projeyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-408">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="576dc-409">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="576dc-409">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="576dc-410">Tamamlanmış projeyi indirdikten sonra uygulamayı çalıştırmak için:</span><span class="sxs-lookup"><span data-stu-id="576dc-410">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="576dc-411">*Contosouniversity. csproj* öğesini silin ve *Contosoüniversıtysqlite. csproj* öğesini *contosouniversity. csproj* olarak yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-411">Delete *ContosoUniversity.csproj* , and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="576dc-412">*Program.cs* ' de, açıklama dışarı bu `#define Startup` şekilde `StartupSQLite` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="576dc-412">In *Program.cs* , comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="576dc-413">*appSettings.js* silin ve üzerinde *appSettings.js* *appSettingsSQLite.js* yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-413">Delete *appSettings.json* , and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="576dc-414">*Geçişler* klasörünü silin ve *migrationssql* öğesini *geçişlerle* yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-414">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="576dc-415">İçin genel arama yapın `#if SQLiteVersion` ve `#if SQLiteVersion` ilgili `#endif` ifadeyi kaldırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-415">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="576dc-416">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="576dc-416">Build the project.</span></span>
* <span data-ttu-id="576dc-417">Proje klasöründeki bir komut isteminde aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="576dc-417">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="576dc-418">SQLite aracında şu SQL ifadesini çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="576dc-418">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="576dc-419">Veritabanını temel alarak projeyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-419">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="576dc-420">Web uygulaması projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="576dc-420">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="576dc-421">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="576dc-421">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="576dc-422">Visual Studio **Dosya** menüsünden **Yeni** > **Proje** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="576dc-422">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="576dc-423">**ASP.NET Core Web uygulaması** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="576dc-423">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="576dc-424">Projeyi *Contosouniversity* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-424">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="576dc-425">Büyük harfler de dahil olmak üzere bu tam adı kullanmak önemlidir, bu nedenle kod kopyalanıp yapıştırılırken ad alanları eşleşir.</span><span class="sxs-lookup"><span data-stu-id="576dc-425">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="576dc-426">Açılan menüden **.NET Core** ve **3,0 ASP.NET Core** seçin ve ardından **Web uygulaması** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="576dc-426">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="576dc-427">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="576dc-427">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="576dc-428">Bir terminalde, proje klasörünün oluşturulması gereken klasöre gidin.</span><span class="sxs-lookup"><span data-stu-id="576dc-428">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="576dc-429">Bir Razor sayfa projesi ve yeni proje klasörü oluşturmak için aşağıdaki komutları çalıştırın `cd` :</span><span class="sxs-lookup"><span data-stu-id="576dc-429">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="576dc-430">Site stilini ayarlayın</span><span class="sxs-lookup"><span data-stu-id="576dc-430">Set up the site style</span></span>

<span data-ttu-id="576dc-431">*Sayfa/paylaşılan/_Layout. cshtml* 'yi güncelleştirerek site üst bilgisini, alt bilgisini ve menüsünü ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="576dc-431">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml* :</span></span>

* <span data-ttu-id="576dc-432">"ContosoUniversity" öğesinin her oluşumunu "Contoso Üniversitesi" olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="576dc-432">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="576dc-433">Üç oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="576dc-433">There are three occurrences.</span></span>

* <span data-ttu-id="576dc-434">**Giriş** ve **Gizlilik** menü girişlerini silin ve **hakkında** , **öğrenciler** , **Kurslar** , **eğitmenler** ve **Departmanlar** için girişler ekleyin.</span><span class="sxs-lookup"><span data-stu-id="576dc-434">Delete the **Home** and **Privacy** menu entries, and add entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="576dc-435">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="576dc-435">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="576dc-436">*Pages/Index. cshtml* dosyasında, ASP.NET Core hakkındaki metni bu uygulamayla ilgili metinle değiştirmek için dosyanın içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="576dc-436">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="576dc-437">Giriş sayfasının göründüğünü doğrulamak için uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-437">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="576dc-438">Veri modeli</span><span class="sxs-lookup"><span data-stu-id="576dc-438">The data model</span></span>

<span data-ttu-id="576dc-439">Aşağıdaki bölümler bir veri modeli oluşturur:</span><span class="sxs-lookup"><span data-stu-id="576dc-439">The following sections create a data model:</span></span>

![Kurs-kayıt-öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

<span data-ttu-id="576dc-441">Bir öğrenci herhangi bir sayıda kursa kaydolabilir ve bir kurs, kayıtlı sayıda öğrenciye sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-441">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="576dc-442">Öğrenci varlığı</span><span class="sxs-lookup"><span data-stu-id="576dc-442">The Student entity</span></span>

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

* <span data-ttu-id="576dc-444">Proje klasöründe bir *modeller* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="576dc-444">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="576dc-445">Aşağıdaki kodla *modeller/öğrenci. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="576dc-445">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="576dc-446">`ID`Özelliği, bu sınıfa karşılık gelen veritabanı tablosunun birincil anahtar sütunu olur.</span><span class="sxs-lookup"><span data-stu-id="576dc-446">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="576dc-447">Varsayılan olarak, EF Core `ID` birincil anahtar olarak adlandırılan bir özelliği Yorumlar `classnameID` .</span><span class="sxs-lookup"><span data-stu-id="576dc-447">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="576dc-448">Bu nedenle birincil anahtar sınıfı için alternatif otomatik olarak tanınan ad `Student` olur `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="576dc-448">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="576dc-449">Daha fazla bilgi için bkz. [EF Core-anahtarlar](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="576dc-449">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="576dc-450">`Enrollments`Özelliği bir [Gezinti özelliğidir](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="576dc-450">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="576dc-451">Gezinti özellikleri, bu varlıkla ilgili diğer varlıkları tutar.</span><span class="sxs-lookup"><span data-stu-id="576dc-451">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="576dc-452">Bu durumda, `Enrollments` bir `Student` varlığın özelliği `Enrollment` söz konusu öğrenciye ilişkin tüm varlıkları barındırır.</span><span class="sxs-lookup"><span data-stu-id="576dc-452">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="576dc-453">Örneğin, veritabanındaki bir öğrenci satırında iki ilişkili kayıt satırı varsa, `Enrollments` gezinti özelliği bu Iki kayıt varlığını içerir.</span><span class="sxs-lookup"><span data-stu-id="576dc-453">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="576dc-454">Veritabanında, bir kayıt satırı, Studentitıd sütunu öğrencinin ID değerini içeriyorsa bir öğrenci satırıyla ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="576dc-454">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="576dc-455">Örneğin, bir öğrenci satırının ID = 1 olduğunu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="576dc-455">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="576dc-456">İlgili kayıt satırları Studentitıd = 1 olacaktır.</span><span class="sxs-lookup"><span data-stu-id="576dc-456">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="576dc-457">Studentitıd, kayıt tablosundaki bir *yabancı anahtardır* .</span><span class="sxs-lookup"><span data-stu-id="576dc-457">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="576dc-458">`Enrollments`Özelliği, `ICollection<Enrollment>` birden çok ilgili kayıt varlığı olabileceğinden, olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="576dc-458">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="576dc-459">Veya gibi diğer koleksiyon türlerini kullanabilirsiniz `List<Enrollment>` `HashSet<Enrollment>` .</span><span class="sxs-lookup"><span data-stu-id="576dc-459">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="576dc-460">Kullanıldığında `ICollection<Enrollment>` , EF Core `HashSet<Enrollment>` Varsayılan olarak bir koleksiyon oluşturur.</span><span class="sxs-lookup"><span data-stu-id="576dc-460">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="576dc-461">Kayıt varlığı</span><span class="sxs-lookup"><span data-stu-id="576dc-461">The Enrollment entity</span></span>

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

<span data-ttu-id="576dc-463">Aşağıdaki kodla *modeller/kayıt. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="576dc-463">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="576dc-464">`EnrollmentID`Bu özellik birincil anahtardır; bu varlık kendi başına değil, `classnameID` kendi modelini kullanır `ID` .</span><span class="sxs-lookup"><span data-stu-id="576dc-464">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="576dc-465">Bir üretim veri modeli için bir model seçin ve bunu tutarlı bir şekilde kullanın.</span><span class="sxs-lookup"><span data-stu-id="576dc-465">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="576dc-466">Bu öğretici her ikisinin de yalnızca bir iş olduğunu göstermek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="576dc-466">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="576dc-467">`ID`Olmadan kullanmak `classname` , bazı veri modeli değişikliklerinin uygulanmasını kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="576dc-467">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="576dc-468">`Grade`Özelliği bir `enum` .</span><span class="sxs-lookup"><span data-stu-id="576dc-468">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="576dc-469">Tür bildiriminden sonraki soru işareti, `Grade` `Grade` özelliğin [null yapılabilir](/dotnet/csharp/programming-guide/nullable-types/)olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="576dc-469">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="576dc-470">Null olan bir sınıf sıfır bir sınıf null değerinden farklıdır &mdash; veya henüz atanmamış olur.</span><span class="sxs-lookup"><span data-stu-id="576dc-470">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="576dc-471">`StudentID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Student` .</span><span class="sxs-lookup"><span data-stu-id="576dc-471">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="576dc-472">Bir varlık `Enrollment` bir `Student` varlıkla ilişkilendirilir, bu nedenle özellik tek bir varlık içerir `Student` .</span><span class="sxs-lookup"><span data-stu-id="576dc-472">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="576dc-473">`CourseID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Course` .</span><span class="sxs-lookup"><span data-stu-id="576dc-473">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="576dc-474">Bir `Enrollment` varlık bir `Course` varlıkla ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-474">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="576dc-475">EF Core bir özelliği, adlandırılmış ise yabancı anahtar olarak yorumlar `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="576dc-475">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="576dc-476">Örneğin, `StudentID` `Student` `Student` nesnenin birincil anahtarı olduğundan, gezinti özelliği için yabancı anahtardır `ID` .</span><span class="sxs-lookup"><span data-stu-id="576dc-476">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="576dc-477">Yabancı anahtar özellikleri de adlandırılmış olabilir `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="576dc-477">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="576dc-478">Örneğin, `CourseID` `Course` varlığın birincil anahtarı olduğundan `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="576dc-478">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="576dc-479">Kurs varlığı</span><span class="sxs-lookup"><span data-stu-id="576dc-479">The Course entity</span></span>

![Kurs varlık diyagramı](intro/_static/course-entity.png)

<span data-ttu-id="576dc-481">Aşağıdaki kodla *modeller/kurs. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="576dc-481">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="576dc-482">`Enrollments`Özelliği bir gezinti özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="576dc-482">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="576dc-483">Bir `Course` varlık herhangi bir sayıda varlıkla ilişkili olabilir `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="576dc-483">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="576dc-484">`DatabaseGenerated`Özniteliği, uygulamanın veritabanını oluşturmak yerine birincil anahtarı belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="576dc-484">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="576dc-485">Derleyici hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="576dc-485">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="576dc-486">Yapı iskelesi öğrenci sayfaları</span><span class="sxs-lookup"><span data-stu-id="576dc-486">Scaffold Student pages</span></span>

<span data-ttu-id="576dc-487">Bu bölümde, oluşturmak için ASP.NET Core scafkatlama aracını kullanırsınız:</span><span class="sxs-lookup"><span data-stu-id="576dc-487">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="576dc-488">EF Core *bağlamı* sınıfı.</span><span class="sxs-lookup"><span data-stu-id="576dc-488">An EF Core *context* class.</span></span> <span data-ttu-id="576dc-489">Bağlam, belirli bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="576dc-489">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="576dc-490">`Microsoft.EntityFrameworkCore.DbContext`Sınıfından türetilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-490">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="576dc-491">Razor varlık için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemlerini işleyen sayfalar `Student` .</span><span class="sxs-lookup"><span data-stu-id="576dc-491">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="576dc-492">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="576dc-492">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="576dc-493">*Sayfalar* klasöründe bir *öğrenciler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="576dc-493">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="576dc-494">**Çözüm Gezgini** , *Sayfalar/öğrenciler* klasörüne sağ tıklayın ve **Add** > **yeni yapı iskelesi Ekle öğesini** seçin.</span><span class="sxs-lookup"><span data-stu-id="576dc-494">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="576dc-495">**Yapı iskelesi Ekle** iletişim kutusunda **Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > **ADD**.</span><span class="sxs-lookup"><span data-stu-id="576dc-495">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="576dc-496">**Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="576dc-496">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="576dc-497">**Model sınıfı** açılır penceresinde **öğrenci (Contosouniversity. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="576dc-497">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="576dc-498">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin.</span><span class="sxs-lookup"><span data-stu-id="576dc-498">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="576dc-499">*Contosouniversity. modeller. Contosoüniversıtycontext* olan veri bağlamı adını *Contosouniversity. Data. SchoolContext* olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="576dc-499">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="576dc-500">**Ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="576dc-500">Select **Add**.</span></span>

<span data-ttu-id="576dc-501">Aşağıdaki paketler otomatik olarak yüklenir:</span><span class="sxs-lookup"><span data-stu-id="576dc-501">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="576dc-502">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="576dc-502">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="576dc-503">Gerekli NuGet paketlerini yüklemek için aşağıdaki .NET Core CLI komutlarını çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="576dc-503">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="576dc-504">Yapı iskelesi için Microsoft. VisualStudio. Web. CodeGeneration. Design paketi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="576dc-504">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="576dc-505">Uygulama SQL Server kullanmayabilse de, scafkatlama aracı SQL Server paketine ihtiyaç duyuyor.</span><span class="sxs-lookup"><span data-stu-id="576dc-505">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="576dc-506">*Sayfalar/öğrenciler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="576dc-506">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="576dc-507">[ASPNET-CodeGenerator scafkatlama aracı](xref:fundamentals/tools/dotnet-aspnet-codegenerator)'nı yüklemek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-507">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="576dc-508">Fkatlama öğrenci sayfalarını iskele almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-508">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="576dc-509">**Windows üzerinde**</span><span class="sxs-lookup"><span data-stu-id="576dc-509">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="576dc-510">**MacOS veya Linux üzerinde**</span><span class="sxs-lookup"><span data-stu-id="576dc-510">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="576dc-511">Önceki adımla ilgili bir sorununuz varsa, projeyi derleyin ve yapı iskelesi adımını yeniden deneyin.</span><span class="sxs-lookup"><span data-stu-id="576dc-511">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="576dc-512">Yapı iskelesi işlemi:</span><span class="sxs-lookup"><span data-stu-id="576dc-512">The scaffolding process:</span></span>

* <span data-ttu-id="576dc-513">Razor *Sayfalar/öğrenciler* klasöründe sayfa oluşturur:</span><span class="sxs-lookup"><span data-stu-id="576dc-513">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="576dc-514">*. Cshtml* ve *Create.cshtml.cs* oluşturma</span><span class="sxs-lookup"><span data-stu-id="576dc-514">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="576dc-515">*Delete. cshtml* ve *delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="576dc-515">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="576dc-516">*Details. cshtml* ve *details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="576dc-516">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="576dc-517">*. Cshtml* ve *Edit.cshtml.cs* Düzenle</span><span class="sxs-lookup"><span data-stu-id="576dc-517">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="576dc-518">*Index. cshtml* ve *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="576dc-518">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="576dc-519">*Data/SchoolContext. cs* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="576dc-519">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="576dc-520">*Startup.cs* içinde bağımlılık eklenmesine bağlam ekler.</span><span class="sxs-lookup"><span data-stu-id="576dc-520">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="576dc-521">Öğesine bir veritabanı bağlantı dizesi ekler *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="576dc-521">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="576dc-522">Veritabanı bağlantı dizesi</span><span class="sxs-lookup"><span data-stu-id="576dc-522">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="576dc-523">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="576dc-523">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="576dc-524">*appsettings.json* Dosya, [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)bağlantı dizesini belirtir.</span><span class="sxs-lookup"><span data-stu-id="576dc-524">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="576dc-525">LocalDB, SQL Server Express veritabanı altyapısının hafif bir sürümüdür ve üretim kullanımı için değil uygulama geliştirmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="576dc-525">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="576dc-526">Varsayılan olarak, LocalDB dizinde *. mdf* dosyaları oluşturur `C:/Users/<user>` .</span><span class="sxs-lookup"><span data-stu-id="576dc-526">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="576dc-527">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="576dc-527">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="576dc-528">Bağlantı dizesini *cu. db* adlı bir SQLite veritabanı dosyasını işaret etmek üzere değiştirin:</span><span class="sxs-lookup"><span data-stu-id="576dc-528">Change the connection string to point to a SQLite database file named *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="576dc-529">Veritabanı bağlam sınıfını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="576dc-529">Update the database context class</span></span>

<span data-ttu-id="576dc-530">Belirli bir veri modeli için EF Core işlevselliğini koordine eden ana sınıf veritabanı bağlamı sınıfıdır.</span><span class="sxs-lookup"><span data-stu-id="576dc-530">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="576dc-531">Bağlam [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-531">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="576dc-532">Bağlam, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="576dc-532">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="576dc-533">Bu projede, sınıfı olarak adlandırılır `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="576dc-533">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="576dc-534">*Data/SchoolContext. cs* öğesini şu kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="576dc-534">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="576dc-535">Vurgulanan kod, her bir varlık kümesi için bir [Dbset \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="576dc-535">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="576dc-536">EF Core terminoloji:</span><span class="sxs-lookup"><span data-stu-id="576dc-536">In EF Core terminology:</span></span>

* <span data-ttu-id="576dc-537">Bir varlık kümesi, genellikle bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="576dc-537">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="576dc-538">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="576dc-538">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="576dc-539">Bir varlık kümesi birden çok varlık içerdiğinden, DBSet özellikleri çoğul adlar olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="576dc-539">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="576dc-540">Yapı iskelesi aracı bir `Student` dbset oluşturduğundan, bu adım çoğul olarak değişir `Students` .</span><span class="sxs-lookup"><span data-stu-id="576dc-540">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="576dc-541">RazorSayfa kodunun yeni DBSet adıyla eşleşmesini sağlamak için, tüm projesi genelinde küresel bir değişiklik yapın `_context.Student` `_context.Students` .</span><span class="sxs-lookup"><span data-stu-id="576dc-541">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="576dc-542">8 oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="576dc-542">There are 8 occurrences.</span></span>

<span data-ttu-id="576dc-543">Derleyici hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="576dc-543">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="576dc-544">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="576dc-544">Startup.cs</span></span>

<span data-ttu-id="576dc-545">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="576dc-545">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="576dc-546">Hizmetler (EF Core veritabanı bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-546">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="576dc-547">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="576dc-547">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="576dc-548">Bir veritabanı bağlamı örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="576dc-548">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="576dc-549">Scafkatlama Aracı, bağlam sınıfını bağımlılık ekleme kapsayıcısına otomatik olarak kaydetti.</span><span class="sxs-lookup"><span data-stu-id="576dc-549">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="576dc-550">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="576dc-550">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="576dc-551">`ConfigureServices`' De, vurgulanan satırlar scaffolder tarafından eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="576dc-551">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="576dc-552">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="576dc-552">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="576dc-553">' De `ConfigureServices` , desteği tarafından eklenen kodun çağrılarından emin olun `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="576dc-553">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="576dc-554">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-554">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="576dc-555">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="576dc-555">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="576dc-556">Veritabanını oluşturma</span><span class="sxs-lookup"><span data-stu-id="576dc-556">Create the database</span></span>

<span data-ttu-id="576dc-557">Mevcut değilse veritabanını oluşturmak için *program.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="576dc-557">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="576dc-558">Bağlam için bir veritabanı varsa, [Ensuyeniden](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) oluşturma yöntemi hiçbir eylemde bulunmaz.</span><span class="sxs-lookup"><span data-stu-id="576dc-558">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="576dc-559">Veritabanı yoksa, veritabanını ve şemayı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="576dc-559">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="576dc-560">`EnsureCreated` veri modeli değişikliklerini işlemek için aşağıdaki iş akışını sunar:</span><span class="sxs-lookup"><span data-stu-id="576dc-560">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="576dc-561">Veritabanını silin.</span><span class="sxs-lookup"><span data-stu-id="576dc-561">Delete the database.</span></span> <span data-ttu-id="576dc-562">Mevcut veriler kaybolur.</span><span class="sxs-lookup"><span data-stu-id="576dc-562">Any existing data is lost.</span></span>
* <span data-ttu-id="576dc-563">Veri modelini değiştirin.</span><span class="sxs-lookup"><span data-stu-id="576dc-563">Change the data model.</span></span> <span data-ttu-id="576dc-564">Örneğin, bir alan ekleyin `EmailAddress` .</span><span class="sxs-lookup"><span data-stu-id="576dc-564">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="576dc-565">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-565">Run the app.</span></span>
* <span data-ttu-id="576dc-566">`EnsureCreated` yeni şemaya sahip bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="576dc-566">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="576dc-567">Bu iş akışı, verileri korumanıza gerek olmadığı sürece, şema hızlı bir şekilde gelişen zaman geliştirme aşamasında iyi bir şekilde gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-567">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="576dc-568">Veritabanına girilen verilerin korunması gerektiğinde bu durum farklıdır.</span><span class="sxs-lookup"><span data-stu-id="576dc-568">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="576dc-569">Bu durumda, geçişleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="576dc-569">When that is the case, use migrations.</span></span>

<span data-ttu-id="576dc-570">Öğretici serisinde daha sonra tarafından oluşturulan veritabanını siler `EnsureCreated` ve bunun yerine geçişleri kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="576dc-570">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="576dc-571">Tarafından oluşturulan bir veritabanı, `EnsureCreated` geçişler kullanılarak güncelleştirilemiyor.</span><span class="sxs-lookup"><span data-stu-id="576dc-571">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="576dc-572">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="576dc-572">Test the app</span></span>

* <span data-ttu-id="576dc-573">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-573">Run the app.</span></span>
* <span data-ttu-id="576dc-574">**Öğrenciler** bağlantısını seçin ve ardından **Yeni oluştur**.</span><span class="sxs-lookup"><span data-stu-id="576dc-574">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="576dc-575">Düzenle, Ayrıntılar ve Sil bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="576dc-575">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="576dc-576">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="576dc-576">Seed the database</span></span>

<span data-ttu-id="576dc-577">`EnsureCreated`Yöntemi boş bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="576dc-577">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="576dc-578">Bu bölüm, veritabanını test verileriyle dolduran kodu ekler.</span><span class="sxs-lookup"><span data-stu-id="576dc-578">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="576dc-579">Aşağıdaki kodla *veri/Dbınizer. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="576dc-579">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="576dc-580">Kod, veritabanında herhangi bir öğrenci olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="576dc-580">The code checks if there are any students in the database.</span></span> <span data-ttu-id="576dc-581">Öğrenci yoksa, veritabanına test verileri ekler.</span><span class="sxs-lookup"><span data-stu-id="576dc-581">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="576dc-582">Performansı iyileştirmek için Koleksiyonlar yerine diziler halinde test verileri oluşturur `List<T>` .</span><span class="sxs-lookup"><span data-stu-id="576dc-582">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="576dc-583">*Program.cs* içinde, `EnsureCreated` çağrıyı bir çağrı ile değiştirin `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="576dc-583">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="576dc-584">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="576dc-584">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="576dc-585">Çalışıyorsa uygulamayı durdurun ve **Paket Yöneticisi konsolunda** (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="576dc-585">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="576dc-586">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="576dc-586">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="576dc-587">Çalışıyorsa uygulamayı durdurun ve *cu. db* dosyasını silin.</span><span class="sxs-lookup"><span data-stu-id="576dc-587">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="576dc-588">Uygulamayı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="576dc-588">Restart the app.</span></span>

* <span data-ttu-id="576dc-589">Sağlanan verileri görmek için öğrenciler sayfasını seçin.</span><span class="sxs-lookup"><span data-stu-id="576dc-589">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="576dc-590">Veritabanını görüntüleme</span><span class="sxs-lookup"><span data-stu-id="576dc-590">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="576dc-591">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="576dc-591">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="576dc-592">Visual Studio 'daki **Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="576dc-592">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="576dc-593">SSOX 'te, **(LocalDB) \MSSQLLocalDB > veritabanları > SchoolContext-{GUID}** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="576dc-593">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="576dc-594">Veritabanı adı, daha önce belirttiğiniz bağlam adından ve bir tire ve bir GUID ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="576dc-594">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="576dc-595">**Tables** düğümünü genişletin.</span><span class="sxs-lookup"><span data-stu-id="576dc-595">Expand the **Tables** node.</span></span>
* <span data-ttu-id="576dc-596">Oluşturulan sütunları ve tabloya yerleştirilen satırları görmek için **öğrenci** tablosuna sağ tıklayın ve **verileri görüntüle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="576dc-596">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="576dc-597">Modelin tablo şemasına nasıl eşlendiğini görmek için **öğrenci** tablosuna sağ tıklayın ve **kodu görüntüle** ' ye tıklayın `Student` `Student` .</span><span class="sxs-lookup"><span data-stu-id="576dc-597">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="576dc-598">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="576dc-598">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="576dc-599">Veritabanı şemasını ve sağlanan verileri görüntülemek için SQLite aracınızı kullanın.</span><span class="sxs-lookup"><span data-stu-id="576dc-599">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="576dc-600">Veritabanı dosyası *cu. db* olarak adlandırılır ve proje klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="576dc-600">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="576dc-601">Zaman uyumsuz kod</span><span class="sxs-lookup"><span data-stu-id="576dc-601">Asynchronous code</span></span>

<span data-ttu-id="576dc-602">Zaman uyumsuz programlama, ASP.NET Core ve EF Core için varsayılan moddur.</span><span class="sxs-lookup"><span data-stu-id="576dc-602">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="576dc-603">Web sunucusunda sınırlı sayıda iş parçacığı bulunur ve yüksek yük durumlarında tüm kullanılabilir iş parçacıkları kullanımda olabilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-603">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="576dc-604">Bu durumda, sunucu, iş parçacıkları boşaltılana kadar yeni istekleri işleyemez.</span><span class="sxs-lookup"><span data-stu-id="576dc-604">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="576dc-605">Zaman uyumlu kodla, çok sayıda iş parçacığı, g/ç 'nin tamamlanmasını beklediği için aslında herhangi bir iş yapmadıklarında bağlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-605">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="576dc-606">Zaman uyumsuz kod ile, bir işlem g/ç 'yi tamamlanmayı beklerken, sunucunun diğer istekleri işlemek için kullanması için iş parçacığı serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="576dc-606">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="576dc-607">Sonuç olarak, zaman uyumsuz kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucu gecikmeksizin daha fazla trafiği işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-607">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="576dc-608">Zaman uyumsuz kod, çalışma zamanında az miktarda yük getirir.</span><span class="sxs-lookup"><span data-stu-id="576dc-608">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="576dc-609">Düşük trafik durumlarında, performans artışı göz ardı edilebilir, ancak yüksek trafik durumları için olası performans iyileştirmesi oldukça önemlidir.</span><span class="sxs-lookup"><span data-stu-id="576dc-609">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="576dc-610">Aşağıdaki kodda, [Async](/dotnet/csharp/language-reference/keywords/async) anahtar sözcüğü, `Task<T>` return değeri, `await` anahtar sözcüğü ve `ToListAsync` yöntemi kodu zaman uyumsuz olarak yürütür.</span><span class="sxs-lookup"><span data-stu-id="576dc-610">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="576dc-611">`async`Anahtar sözcüğü derleyiciye şunu söyler:</span><span class="sxs-lookup"><span data-stu-id="576dc-611">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="576dc-612">Yöntem gövdesinin parçaları için geri çağrılar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="576dc-612">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="576dc-613">Döndürülen [görev](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) nesnesini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="576dc-613">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="576dc-614">`Task<T>`Dönüş türü, devam eden işi temsil eder.</span><span class="sxs-lookup"><span data-stu-id="576dc-614">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="576dc-615">`await`Anahtar sözcüğü, derleyicinin yöntemi iki parçaya böetmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="576dc-615">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="576dc-616">İlk bölüm, zaman uyumsuz olarak başlatılan işlemle biter.</span><span class="sxs-lookup"><span data-stu-id="576dc-616">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="576dc-617">İkinci bölüm, işlem tamamlandığında çağrılan bir geri çağırma yöntemine konur.</span><span class="sxs-lookup"><span data-stu-id="576dc-617">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="576dc-618">`ToListAsync` , Uzantı yönteminin zaman uyumsuz sürümüdür `ToList` .</span><span class="sxs-lookup"><span data-stu-id="576dc-618">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="576dc-619">EF Core kullanan zaman uyumsuz kodu yazarken dikkat edilmesi gereken bazı şeyler:</span><span class="sxs-lookup"><span data-stu-id="576dc-619">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="576dc-620">Yalnızca sorguları veya komutlarının veritabanına gönderilmesine neden olan deyimler zaman uyumsuz olarak yürütülür.</span><span class="sxs-lookup"><span data-stu-id="576dc-620">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="576dc-621">,, `ToListAsync` , `SingleOrDefaultAsync` `FirstOrDefaultAsync` Ve içerir `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="576dc-621">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="576dc-622">Yalnızca, gibi bir değiştiren deyimler içermez `IQueryable` `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="576dc-622">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="576dc-623">EF Core bağlamı iş parçacığı açısından güvenli değildir: paralel olarak birden çok işlem yapmayı denemeyin.</span><span class="sxs-lookup"><span data-stu-id="576dc-623">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="576dc-624">Zaman uyumsuz kodun performans avantajlarından yararlanmak için, veritabanına sorgu gönderen EF Core yöntemleri çağırıyorsa kitaplık paketlerinin (örneğin, sayfalama için) zaman uyumsuz olarak kullanılacağını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="576dc-624">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="576dc-625">.NET 'te zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. Async [and await ile](/dotnet/csharp/programming-guide/concepts/async/)zaman uyumsuz [genel bakış](/dotnet/standard/async) ve zaman uyumsuz programlama</span><span class="sxs-lookup"><span data-stu-id="576dc-625">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="576dc-626">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="576dc-626">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="576dc-627">Sonraki öğretici</span><span class="sxs-lookup"><span data-stu-id="576dc-627">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="576dc-628">Contoso Üniversitesi örnek Web uygulaması, Razor Entity Framework (EF) Core kullanarak ASP.NET Core sayfalı bir uygulamanın nasıl oluşturulacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="576dc-628">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="576dc-629">Örnek uygulama, kurgusal bir Contoso Üniversitesi için bir Web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="576dc-629">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="576dc-630">Öğrenci giriş, kurs oluşturma ve eğitmen atamaları gibi işlevleri içerir.</span><span class="sxs-lookup"><span data-stu-id="576dc-630">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="576dc-631">Bu sayfa, Contoso Üniversitesi örnek uygulamasının nasıl oluşturulacağını açıklayan bir öğretici serisinin ilkisidir.</span><span class="sxs-lookup"><span data-stu-id="576dc-631">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="576dc-632">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="576dc-632">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="576dc-633">[Yönergeleri indirin](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="576dc-633">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="576dc-634">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="576dc-634">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="576dc-635">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="576dc-635">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="576dc-636">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="576dc-636">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="576dc-637">[ Razor Sayfalarla](xref:razor-pages/index)benzerlik.</span><span class="sxs-lookup"><span data-stu-id="576dc-637">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="576dc-638">Yeni programcılar, bu seriyi başlatmadan önce [sayfalarla çalışmaya başlama Razor sayfasını](xref:tutorials/razor-pages/razor-pages-start) tamamlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="576dc-638">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="576dc-639">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="576dc-639">Troubleshooting</span></span>

<span data-ttu-id="576dc-640">Çözemiyoruz bir sorunla karşılaşırsanız, kodunuzun [Tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)karşılaştırılmasıyla genellikle çözümü bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="576dc-640">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="576dc-641">[ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) [için bir](https://stackoverflow.com/questions/tagged/asp.net-core) soru göndererek yardım almanın iyi bir yolu.</span><span class="sxs-lookup"><span data-stu-id="576dc-641">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="576dc-642">Contoso Üniversitesi web uygulaması</span><span class="sxs-lookup"><span data-stu-id="576dc-642">The Contoso University web app</span></span>

<span data-ttu-id="576dc-643">Bu öğreticilerde oluşturulan uygulama, temel bir üniversite web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="576dc-643">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="576dc-644">Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-644">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="576dc-645">Öğreticide oluşturulan ekranlardan bazıları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="576dc-645">Here are a few of the screens created in the tutorial.</span></span>

![Öğrenciler Dizin sayfası](intro/_static/students-index.png)

![Öğrenciler düzenleme sayfası](intro/_static/student-edit.png)

<span data-ttu-id="576dc-648">Bu sitenin kullanıcı arabirimi stili yerleşik şablonlar tarafından üretilme kadar yakın.</span><span class="sxs-lookup"><span data-stu-id="576dc-648">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="576dc-649">Eğitim odağı, Razor kullanıcı arabiriminden değil, sayfalarla EF Core.</span><span class="sxs-lookup"><span data-stu-id="576dc-649">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="576dc-650">Contosoüniversitesi Razor sayfaları Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="576dc-650">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="576dc-651">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="576dc-651">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="576dc-652">Visual Studio **Dosya** menüsünden **Yeni** > **Proje** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="576dc-652">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="576dc-653">Yeni bir ASP.NET Core Web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="576dc-653">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="576dc-654">Projeyi **Contosouniversity** olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-654">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="576dc-655">Kod kopyalama/yapıştırma olduğunda, ad alanlarının eşleşmesi için *Contosouniversity* projesini adlandırmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="576dc-655">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="576dc-656">Açılan listede **ASP.NET Core 2,1** ' i seçin ve ardından **Web uygulaması** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="576dc-656">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="576dc-657">Yukarıdaki adımların görüntüleri için bkz. [ Razor Web uygulaması oluşturma](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="576dc-657">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="576dc-658">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-658">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="576dc-659">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="576dc-659">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="576dc-660">Site stilini ayarlayın</span><span class="sxs-lookup"><span data-stu-id="576dc-660">Set up the site style</span></span>

<span data-ttu-id="576dc-661">Site menüsünü, düzeni ve giriş sayfasını birkaç değişiklik ayarlar.</span><span class="sxs-lookup"><span data-stu-id="576dc-661">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="576dc-662">*Sayfaları/paylaşılan/_Layout. cshtml* 'yi aşağıdaki değişikliklerle güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="576dc-662">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="576dc-663">"ContosoUniversity" öğesinin her oluşumunu "Contoso Üniversitesi" olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="576dc-663">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="576dc-664">Üç oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="576dc-664">There are three occurrences.</span></span>

* <span data-ttu-id="576dc-665">**Öğrenciler** , **Kurslar** , **eğitmenler** ve **Departmanlar** için menü girişleri ekleyin ve **kişi** menü girişini silin.</span><span class="sxs-lookup"><span data-stu-id="576dc-665">Add menu entries for **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="576dc-666">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="576dc-666">The changes are highlighted.</span></span> <span data-ttu-id="576dc-667">(Tüm *biçimlendirme gösterilmez.* )</span><span class="sxs-lookup"><span data-stu-id="576dc-667">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="576dc-668">*Pages/Index. cshtml* dosyasında, ASP.net ve MVC hakkındaki metni bu uygulamayla ilgili metinle değiştirmek için dosyanın içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="576dc-668">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="576dc-669">Veri modelini oluşturma</span><span class="sxs-lookup"><span data-stu-id="576dc-669">Create the data model</span></span>

<span data-ttu-id="576dc-670">Contoso Üniversitesi uygulaması için varlık sınıfları oluşturma.</span><span class="sxs-lookup"><span data-stu-id="576dc-670">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="576dc-671">Aşağıdaki üç varlıkla başlayın:</span><span class="sxs-lookup"><span data-stu-id="576dc-671">Start with the following three entities:</span></span>

![Kurs-kayıt-öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

<span data-ttu-id="576dc-673">Ve varlıkları arasında bire çok ilişki vardır `Student` `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="576dc-673">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="576dc-674">Ve varlıkları arasında bire çok ilişki vardır `Course` `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="576dc-674">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="576dc-675">Bir öğrenci herhangi bir sayıda kursa kaydolabilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-675">A student can enroll in any number of courses.</span></span> <span data-ttu-id="576dc-676">Bir kurs, kayıtlı sayıda öğrenciye sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-676">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="576dc-677">Aşağıdaki bölümlerde, bu varlıkların her biri için bir sınıf oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="576dc-677">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="576dc-678">Öğrenci varlığı</span><span class="sxs-lookup"><span data-stu-id="576dc-678">The Student entity</span></span>

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

<span data-ttu-id="576dc-680">*Modeller* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="576dc-680">Create a *Models* folder.</span></span> <span data-ttu-id="576dc-681">*Modeller* klasöründe, *Student.cs* adlı bir sınıf dosyasını aşağıdaki kodla oluşturun:</span><span class="sxs-lookup"><span data-stu-id="576dc-681">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="576dc-682">`ID`Özelliği, bu sınıfa karşılık gelen veritabanı (DB) tablosunun birincil anahtar sütunu olur.</span><span class="sxs-lookup"><span data-stu-id="576dc-682">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="576dc-683">Varsayılan olarak, EF Core `ID` birincil anahtar olarak adlandırılan bir özelliği Yorumlar `classnameID` .</span><span class="sxs-lookup"><span data-stu-id="576dc-683">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="576dc-684">`classnameID`' De, `classname` sınıfının adıdır.</span><span class="sxs-lookup"><span data-stu-id="576dc-684">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="576dc-685">Diğer otomatik olarak tanınan birincil anahtar, `StudentID` Önceki örnekte yer verilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-685">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="576dc-686">`Enrollments`Özelliği bir [Gezinti özelliğidir](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="576dc-686">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="576dc-687">Gezinti özellikleri bu varlıkla ilgili diğer varlıkların bağlantısını sağlar.</span><span class="sxs-lookup"><span data-stu-id="576dc-687">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="576dc-688">Bu durumda, `Enrollments` öğesinin özelliği ile `Student entity` `Enrollment` ilgili tüm varlıkları barındırır `Student` .</span><span class="sxs-lookup"><span data-stu-id="576dc-688">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="576dc-689">Örneğin, VERITABANıNDAKI bir öğrenci satırında iki ilişkili kayıt satırı varsa, `Enrollments` gezinti özelliği bu iki `Enrollment` varlığı içerir.</span><span class="sxs-lookup"><span data-stu-id="576dc-689">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="576dc-690">İlgili `Enrollment` satır, söz konusu öğrencinin birincil anahtar değerini sütununda içeren bir satırdır `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="576dc-690">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="576dc-691">Örneğin, ID = 1 olan öğrencinin tabloda iki satır olduğunu varsayalım `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="576dc-691">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="576dc-692">`Enrollment`Tabloda = 1 olan iki satır vardır `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="576dc-692">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="576dc-693">`StudentID` , tablodaki `Enrollment` öğrencisi belirten bir yabancı anahtardır `Student` .</span><span class="sxs-lookup"><span data-stu-id="576dc-693">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="576dc-694">Bir gezinti özelliği birden çok varlık tutabileceğinden, gezinti özelliği gibi bir liste türü olmalıdır `ICollection<T>` .</span><span class="sxs-lookup"><span data-stu-id="576dc-694">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="576dc-695">`ICollection<T>` belirtilebilir veya veya gibi bir tür olabilir `List<T>` `HashSet<T>` .</span><span class="sxs-lookup"><span data-stu-id="576dc-695">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="576dc-696">Kullanıldığında `ICollection<T>` , EF Core `HashSet<T>` Varsayılan olarak bir koleksiyon oluşturur.</span><span class="sxs-lookup"><span data-stu-id="576dc-696">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="576dc-697">Birden çok varlığı tutan gezinti özellikleri, çoktan çoğa ve bire çok ilişkilerden gelir.</span><span class="sxs-lookup"><span data-stu-id="576dc-697">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="576dc-698">Kayıt varlığı</span><span class="sxs-lookup"><span data-stu-id="576dc-698">The Enrollment entity</span></span>

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

<span data-ttu-id="576dc-700">*Modeller* klasöründe, aşağıdaki kodla *enrollment.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="576dc-700">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="576dc-701">`EnrollmentID`Özelliği birincil anahtardır.</span><span class="sxs-lookup"><span data-stu-id="576dc-701">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="576dc-702">Bu varlık `classnameID` `ID` , varlığı beğenmek yerine, modelini kullanır `Student` .</span><span class="sxs-lookup"><span data-stu-id="576dc-702">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="576dc-703">Genellikle geliştiriciler bir model seçer ve bunu veri modeli boyunca kullanır.</span><span class="sxs-lookup"><span data-stu-id="576dc-703">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="576dc-704">Daha sonraki bir öğreticide, veri modelinde devralmayı daha kolay hale getirmek için ClassName olmadan ID kullanımı gösterilmemiştir.</span><span class="sxs-lookup"><span data-stu-id="576dc-704">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="576dc-705">`Grade`Özelliği bir `enum` .</span><span class="sxs-lookup"><span data-stu-id="576dc-705">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="576dc-706">Tür bildiriminden sonraki soru işareti, `Grade` `Grade` özelliğin null yapılabilir olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="576dc-706">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="576dc-707">Null olan bir sınıf sıfır bir sınıf ile farklıdır--null, henüz bir sınıf bilinmediğini veya henüz atanmadığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="576dc-707">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="576dc-708">`StudentID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Student` .</span><span class="sxs-lookup"><span data-stu-id="576dc-708">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="576dc-709">Bir varlık `Enrollment` bir `Student` varlıkla ilişkilendirilir, bu nedenle özellik tek bir varlık içerir `Student` .</span><span class="sxs-lookup"><span data-stu-id="576dc-709">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="576dc-710">`Student`Varlık, `Student.Enrollments` birden çok varlık içeren gezinti özelliğinden farklıdır `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="576dc-710">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="576dc-711">`CourseID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Course` .</span><span class="sxs-lookup"><span data-stu-id="576dc-711">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="576dc-712">Bir `Enrollment` varlık bir `Course` varlıkla ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-712">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="576dc-713">EF Core bir özelliği, adlandırılmış ise yabancı anahtar olarak yorumlar `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="576dc-713">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="576dc-714">Örneğin, `StudentID` `Student` gezinti özelliği için `Student` varlığın birincil anahtarı olduğundan `ID` .</span><span class="sxs-lookup"><span data-stu-id="576dc-714">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="576dc-715">Yabancı anahtar özellikleri de adlandırılmış olabilir `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="576dc-715">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="576dc-716">Örneğin, `CourseID` `Course` varlığın birincil anahtarı olduğundan `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="576dc-716">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="576dc-717">Kurs varlığı</span><span class="sxs-lookup"><span data-stu-id="576dc-717">The Course entity</span></span>

![Kurs varlık diyagramı](intro/_static/course-entity.png)

<span data-ttu-id="576dc-719">*Modeller* klasöründe, aşağıdaki kodla *Course.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="576dc-719">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="576dc-720">`Enrollments`Özelliği bir gezinti özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="576dc-720">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="576dc-721">Bir `Course` varlık herhangi bir sayıda varlıkla ilişkili olabilir `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="576dc-721">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="576dc-722">`DatabaseGenerated`Özniteliği, UYGULAMANıN DB oluşturmak yerine birincil anahtarı belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="576dc-722">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="576dc-723">Öğrenci modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="576dc-723">Scaffold the student model</span></span>

<span data-ttu-id="576dc-724">Bu bölümde öğrenci modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="576dc-724">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="576dc-725">Diğer bir deyişle, scafkatlama aracı öğrenci modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.</span><span class="sxs-lookup"><span data-stu-id="576dc-725">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="576dc-726">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="576dc-726">Build the project.</span></span>
* <span data-ttu-id="576dc-727">*Sayfalar/öğrenciler* klasörünü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="576dc-727">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="576dc-728">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="576dc-728">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="576dc-729">**Çözüm Gezgini** , *Sayfalar/öğrenciler* klasörüne sağ tıklayarak **Add** > **yeni yapı iskelesi öğesi** ekleyin >.</span><span class="sxs-lookup"><span data-stu-id="576dc-729">In **Solution Explorer** , right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="576dc-730">**Yapı iskelesi Ekle** iletişim kutusunda **Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > **ADD**.</span><span class="sxs-lookup"><span data-stu-id="576dc-730">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="576dc-731">**Razor Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="576dc-731">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="576dc-732">**Model sınıfı** açılır penceresinde **öğrenci (Contosouniversity. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="576dc-732">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="576dc-733">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin ve üretilen adı **Contosouniversity. modeller. SchoolContext** olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="576dc-733">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="576dc-734">**Veri bağlamı sınıfı** açılır penceresinde **Contosouniversity. modeller. SchoolContext** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="576dc-734">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="576dc-735">**Ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="576dc-735">Select **Add**.</span></span>

![CRUD iletişim kutusu](intro/_static/s1.png)

<span data-ttu-id="576dc-737">Önceki adımla ilgili bir sorununuz varsa [Film modeli](xref:tutorials/razor-pages/model#scaffold-the-movie-model) ' ne bakın.</span><span class="sxs-lookup"><span data-stu-id="576dc-737">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="576dc-738">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="576dc-738">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="576dc-739">Öğrenci modelini iskele almak için aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-739">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="576dc-740">Yapı iskelesi işlemi oluşturulur ve aşağıdaki dosyaları değiştirdi:</span><span class="sxs-lookup"><span data-stu-id="576dc-740">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="576dc-741">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="576dc-741">Files created</span></span>

* <span data-ttu-id="576dc-742">*Sayfalar/öğrenciler* Oluşturma, silme, ayrıntılar, düzenleme, dizin oluşturma.</span><span class="sxs-lookup"><span data-stu-id="576dc-742">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="576dc-743">*Data/SchoolContext. cs*</span><span class="sxs-lookup"><span data-stu-id="576dc-743">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="576dc-744">Dosya güncelleştirmeleri</span><span class="sxs-lookup"><span data-stu-id="576dc-744">File updates</span></span>

* <span data-ttu-id="576dc-745">*Startup.cs* : Bu dosyadaki değişiklikler sonraki bölümde ayrıntılıdır.</span><span class="sxs-lookup"><span data-stu-id="576dc-745">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="576dc-746">*appsettings.json* : Yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesi eklenir.</span><span class="sxs-lookup"><span data-stu-id="576dc-746">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="576dc-747">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="576dc-747">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="576dc-748">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="576dc-748">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="576dc-749">Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-749">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="576dc-750">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="576dc-750">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="576dc-751">Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="576dc-751">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="576dc-752">Scafkatlama aracı otomatik olarak bir DB bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.</span><span class="sxs-lookup"><span data-stu-id="576dc-752">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="576dc-753">`ConfigureServices` *Startup.cs* içindeki yöntemi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="576dc-753">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="576dc-754">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="576dc-754">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="576dc-755">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-755">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="576dc-756">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="576dc-756">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="576dc-757">Ana güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="576dc-757">Update main</span></span>

<span data-ttu-id="576dc-758">*Program.cs* ' de, `Main` aşağıdakileri yapmak için yöntemini değiştirin:</span><span class="sxs-lookup"><span data-stu-id="576dc-758">In *Program.cs* , modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="576dc-759">Bağımlılık ekleme kapsayıcısından bir DB bağlam örneği alın.</span><span class="sxs-lookup"><span data-stu-id="576dc-759">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="576dc-760">Yeniden  [oluşturulmasını](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)çağırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-760">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="576dc-761">`EnsureCreated`Yöntem tamamlandığında bağlamı atın.</span><span class="sxs-lookup"><span data-stu-id="576dc-761">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="576dc-762">Aşağıdaki kod güncelleştirilmiş *program.cs* dosyasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="576dc-762">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="576dc-763">`EnsureCreated` bağlam veritabanının mevcut olmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="576dc-763">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="576dc-764">Varsa, hiçbir eylem yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="576dc-764">If it exists, no action is taken.</span></span> <span data-ttu-id="576dc-765">Yoksa, veritabanı ve tüm şeması oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="576dc-765">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="576dc-766">`EnsureCreated` veritabanını oluşturmak için geçişleri kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="576dc-766">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="576dc-767">İle oluşturulan bir veritabanı `EnsureCreated` daha sonra geçişler kullanılarak güncelleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="576dc-767">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="576dc-768">`EnsureCreated` , aşağıdaki iş akışına izin veren uygulama başlatma sırasında çağrılır:</span><span class="sxs-lookup"><span data-stu-id="576dc-768">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="576dc-769">VERITABANıNı silin.</span><span class="sxs-lookup"><span data-stu-id="576dc-769">Delete the DB.</span></span>
* <span data-ttu-id="576dc-770">DB şemasını değiştirin (örneğin, bir `EmailAddress` alan ekleyin).</span><span class="sxs-lookup"><span data-stu-id="576dc-770">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="576dc-771">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="576dc-771">Run the app.</span></span>
* <span data-ttu-id="576dc-772">`EnsureCreated` sütunu ile bir VERITABANı oluşturur `EmailAddress` .</span><span class="sxs-lookup"><span data-stu-id="576dc-772">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="576dc-773">`EnsureCreated` şema hızlı bir şekilde gelişmede geliştirmede daha erken kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="576dc-773">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="576dc-774">Öğreticide daha sonra DB silinir ve geçişler kullanılır.</span><span class="sxs-lookup"><span data-stu-id="576dc-774">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="576dc-775">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="576dc-775">Test the app</span></span>

<span data-ttu-id="576dc-776">Uygulamayı çalıştırın ve ilkeyi kabul edin cookie .</span><span class="sxs-lookup"><span data-stu-id="576dc-776">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="576dc-777">Bu uygulama, kişisel bilgileri saklar.</span><span class="sxs-lookup"><span data-stu-id="576dc-777">This app doesn't keep personal information.</span></span> <span data-ttu-id="576dc-778">İlke hakkında, cookie [AB Genel VERI koruma YÖNETMELIĞI (GDPR) desteğiyle](xref:security/gdpr)ilgili bilgi edinebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="576dc-778">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="576dc-779">**Öğrenciler** bağlantısını seçin ve ardından **Yeni oluştur**.</span><span class="sxs-lookup"><span data-stu-id="576dc-779">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="576dc-780">Düzenle, Ayrıntılar ve Sil bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="576dc-780">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="576dc-781">SchoolContext DB bağlamını inceleyin</span><span class="sxs-lookup"><span data-stu-id="576dc-781">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="576dc-782">Belirli bir veri modeli için EF Core işlevselliğini koordine eden ana sınıf DB bağlam sınıfıdır.</span><span class="sxs-lookup"><span data-stu-id="576dc-782">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="576dc-783">Veri bağlamı [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-783">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="576dc-784">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="576dc-784">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="576dc-785">Bu projede, sınıfı olarak adlandırılır `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="576dc-785">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="576dc-786">Aşağıdaki kodla *SchoolContext.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="576dc-786">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="576dc-787">Vurgulanan kod, her bir varlık kümesi için bir [Dbset \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="576dc-787">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="576dc-788">EF Core terminoloji:</span><span class="sxs-lookup"><span data-stu-id="576dc-788">In EF Core terminology:</span></span>

* <span data-ttu-id="576dc-789">Bir varlık kümesi, genellikle bir DB tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="576dc-789">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="576dc-790">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="576dc-790">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="576dc-791">`DbSet<Enrollment>` ve `DbSet<Course>` atlanamaz.</span><span class="sxs-lookup"><span data-stu-id="576dc-791">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="576dc-792">EF Core `Student` `Enrollment` , varlık varlığa başvurduğundan ve varlık `Enrollment` varlığa başvurduğundan bunları örtülü olarak içerir `Course` .</span><span class="sxs-lookup"><span data-stu-id="576dc-792">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="576dc-793">Bu öğreticide, `DbSet<Enrollment>` ve `DbSet<Course>` içinde tutun `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="576dc-793">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="576dc-794">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="576dc-794">SQL Server Express LocalDB</span></span>

<span data-ttu-id="576dc-795">Bağlantı dizesi [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)belirtir.</span><span class="sxs-lookup"><span data-stu-id="576dc-795">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="576dc-796">LocalDB, SQL Server Express veritabanı altyapısının hafif bir sürümüdür ve üretim kullanımı için değil uygulama geliştirmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="576dc-796">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="576dc-797">LocalDB, istek üzerine başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur.</span><span class="sxs-lookup"><span data-stu-id="576dc-797">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="576dc-798">Varsayılan olarak, LocalDB dizinde *. mdf* DB dosyaları oluşturur `C:/Users/<user>` .</span><span class="sxs-lookup"><span data-stu-id="576dc-798">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="576dc-799">Test verileriyle VERITABANıNı başlatmak için kod ekleme</span><span class="sxs-lookup"><span data-stu-id="576dc-799">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="576dc-800">EF Core boş bir VERITABANı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="576dc-800">EF Core creates an empty DB.</span></span> <span data-ttu-id="576dc-801">Bu bölümde, `Initialize` Test verileriyle doldurmak için bir yöntem yazılır.</span><span class="sxs-lookup"><span data-stu-id="576dc-801">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="576dc-802">*Veri* klasöründe, *DbInitializer.cs* adlı yeni bir sınıf dosyası oluşturun ve aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="576dc-802">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="576dc-803">Note: önceki kod, `Models` yerine ad alanı için ( `namespace ContosoUniversity.Models` ) kullanır `Data` .</span><span class="sxs-lookup"><span data-stu-id="576dc-803">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="576dc-804">`Models` , scaffolder tarafından oluşturulan kodla tutarlıdır.</span><span class="sxs-lookup"><span data-stu-id="576dc-804">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="576dc-805">Daha fazla bilgi için bkz. [GitHub yapı iskelesi sorunu](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="576dc-805">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="576dc-806">Kod, VERITABANıNDA herhangi bir öğrenci olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="576dc-806">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="576dc-807">Veritabanında hiç öğrenci yoksa, DB test verileriyle başlatılır.</span><span class="sxs-lookup"><span data-stu-id="576dc-807">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="576dc-808">Performansı iyileştirmek için test verilerini koleksiyonlar yerine dizilere yükler `List<T>` .</span><span class="sxs-lookup"><span data-stu-id="576dc-808">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="576dc-809">`EnsureCreated`YÖNTEMI DB bağlamı için otomatik olarak DB oluşturur.</span><span class="sxs-lookup"><span data-stu-id="576dc-809">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="576dc-810">VERITABANı varsa, `EnsureCreated` veritabanını değiştirmeden döndürür.</span><span class="sxs-lookup"><span data-stu-id="576dc-810">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="576dc-811">*Program.cs* içinde, `Main` çağrısı yapılacak yöntemi değiştirin `Initialize` :</span><span class="sxs-lookup"><span data-stu-id="576dc-811">In *Program.cs* , modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="576dc-812">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="576dc-812">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="576dc-813">Çalışıyorsa uygulamayı durdurun ve **Paket Yöneticisi konsolunda** (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="576dc-813">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="576dc-814">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="576dc-814">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="576dc-815">Çalışıyorsa uygulamayı durdurun ve *cu. db* dosyasını silin.</span><span class="sxs-lookup"><span data-stu-id="576dc-815">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="576dc-816">VERITABANıNı görüntüleme</span><span class="sxs-lookup"><span data-stu-id="576dc-816">View the DB</span></span>

<span data-ttu-id="576dc-817">Veritabanı adı, daha önce belirttiğiniz bağlam adından ve bir tire ve bir GUID ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="576dc-817">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="576dc-818">Bu nedenle, veritabanı adı "SchoolContext-{GUID}" olacaktır.</span><span class="sxs-lookup"><span data-stu-id="576dc-818">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="576dc-819">GUID her kullanıcı için farklı olacaktır.</span><span class="sxs-lookup"><span data-stu-id="576dc-819">The GUID will be different for each user.</span></span>
<span data-ttu-id="576dc-820">Visual Studio 'daki **Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="576dc-820">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="576dc-821">SSOX 'te, **(LocalDB) \MSSQLLocalDB > veritabanları > SchoolContext-{GUID}** ' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="576dc-821">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="576dc-822">**Tables** düğümünü genişletin.</span><span class="sxs-lookup"><span data-stu-id="576dc-822">Expand the **Tables** node.</span></span>

<span data-ttu-id="576dc-823">Oluşturulan sütunları ve tabloya yerleştirilen satırları görmek için **öğrenci** tablosuna sağ tıklayın ve **verileri görüntüle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="576dc-823">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="576dc-824">Zaman uyumsuz kod</span><span class="sxs-lookup"><span data-stu-id="576dc-824">Asynchronous code</span></span>

<span data-ttu-id="576dc-825">Zaman uyumsuz programlama, ASP.NET Core ve EF Core için varsayılan moddur.</span><span class="sxs-lookup"><span data-stu-id="576dc-825">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="576dc-826">Web sunucusunda sınırlı sayıda iş parçacığı bulunur ve yüksek yük durumlarında tüm kullanılabilir iş parçacıkları kullanımda olabilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-826">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="576dc-827">Bu durumda, sunucu, iş parçacıkları boşaltılana kadar yeni istekleri işleyemez.</span><span class="sxs-lookup"><span data-stu-id="576dc-827">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="576dc-828">Zaman uyumlu kodla, çok sayıda iş parçacığı, g/ç 'nin tamamlanmasını beklediği için aslında herhangi bir iş yapmadıklarında bağlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-828">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="576dc-829">Zaman uyumsuz kod ile, bir işlem g/ç 'yi tamamlanmayı beklerken, sunucunun diğer istekleri işlemek için kullanması için iş parçacığı serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="576dc-829">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="576dc-830">Sonuç olarak, zaman uyumsuz kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucu, gecikme olmadan daha fazla trafiği işlemeye etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="576dc-830">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="576dc-831">Zaman uyumsuz kod, çalışma zamanında az miktarda yük getirir.</span><span class="sxs-lookup"><span data-stu-id="576dc-831">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="576dc-832">Düşük trafik durumlarında, performans artışı göz ardı edilebilir, ancak yüksek trafik durumları için olası performans iyileştirmesi oldukça önemlidir.</span><span class="sxs-lookup"><span data-stu-id="576dc-832">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="576dc-833">Aşağıdaki kodda, [Async](/dotnet/csharp/language-reference/keywords/async) anahtar sözcüğü, `Task<T>` return değeri, `await` anahtar sözcüğü ve `ToListAsync` yöntemi kodu zaman uyumsuz olarak yürütür.</span><span class="sxs-lookup"><span data-stu-id="576dc-833">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="576dc-834">`async`Anahtar sözcüğü derleyiciye şunu söyler:</span><span class="sxs-lookup"><span data-stu-id="576dc-834">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="576dc-835">Yöntem gövdesinin parçaları için geri çağrılar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="576dc-835">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="576dc-836">Döndürülen [görev](/dotnet/api/system.threading.tasks.task) nesnesini otomatik olarak oluşturun.</span><span class="sxs-lookup"><span data-stu-id="576dc-836">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="576dc-837">Daha fazla bilgi için bkz. [görev dönüş türü](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="576dc-837">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="576dc-838">Örtük dönüş türü, `Task` devam eden işi temsil eder.</span><span class="sxs-lookup"><span data-stu-id="576dc-838">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="576dc-839">`await`Anahtar sözcüğü, derleyicinin yöntemi iki parçaya böetmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="576dc-839">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="576dc-840">İlk bölüm, zaman uyumsuz olarak başlatılan işlemle biter.</span><span class="sxs-lookup"><span data-stu-id="576dc-840">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="576dc-841">İkinci bölüm, işlem tamamlandığında çağrılan bir geri çağırma yöntemine konur.</span><span class="sxs-lookup"><span data-stu-id="576dc-841">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="576dc-842">`ToListAsync` , Uzantı yönteminin zaman uyumsuz sürümüdür `ToList` .</span><span class="sxs-lookup"><span data-stu-id="576dc-842">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="576dc-843">EF Core kullanan zaman uyumsuz kodu yazarken dikkat edilmesi gereken bazı şeyler:</span><span class="sxs-lookup"><span data-stu-id="576dc-843">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="576dc-844">Yalnızca sorguları veya komutlarının VERITABANıNA gönderilmesine neden olan deyimler zaman uyumsuz olarak yürütülür.</span><span class="sxs-lookup"><span data-stu-id="576dc-844">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="576dc-845">,,, `ToListAsync` `SingleOrDefaultAsync` Ve içerir `FirstOrDefaultAsync` `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="576dc-845">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="576dc-846">Yalnızca, gibi bir değiştiren deyimler içermez `IQueryable` `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="576dc-846">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="576dc-847">EF Core bağlamı iş parçacığı açısından güvenli değildir: paralel olarak birden çok işlem yapmayı denemeyin.</span><span class="sxs-lookup"><span data-stu-id="576dc-847">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="576dc-848">Zaman uyumsuz kodun performans avantajlarından yararlanmak için, VERITABANıNA sorgu gönderen EF Core yöntemlerini çağırıyorsa kitaplık paketlerinin (örneğin, sayfalama için) zaman uyumsuz olarak kullanılacağını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="576dc-848">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="576dc-849">.NET 'te zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. Async [and await ile](/dotnet/csharp/programming-guide/concepts/async/)zaman uyumsuz [genel bakış](/dotnet/standard/async) ve zaman uyumsuz programlama</span><span class="sxs-lookup"><span data-stu-id="576dc-849">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="576dc-850">Sonraki öğreticide, temel CRUD (oluşturma, okuma, güncelleştirme, silme) işlemleri incelenir.</span><span class="sxs-lookup"><span data-stu-id="576dc-850">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="576dc-851">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="576dc-851">Additional resources</span></span>

* [<span data-ttu-id="576dc-852">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="576dc-852">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="576dc-853">Sonraki</span><span class="sxs-lookup"><span data-stu-id="576dc-853">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
