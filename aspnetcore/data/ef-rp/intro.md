---
title: Razor ASP.NET Core Entity Framework Core olan sayfalar-öğretici 1/8
author: rick-anderson
description: RazorEntity Framework Core kullanarak bir sayfalar uygulamasının nasıl oluşturulacağını gösterir
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
no-loc:
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
ms.openlocfilehash: 35a5758500ae2bc691c8d08eccb22340f9998c39
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424301"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="7e71a-103">Razor ASP.NET Core Entity Framework Core olan sayfalar-öğretici 1/8</span><span class="sxs-lookup"><span data-stu-id="7e71a-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="7e71a-104">, [Tom Dykstra](https://github.com/tdykstra) ve [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından</span><span class="sxs-lookup"><span data-stu-id="7e71a-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="7e71a-105">Bu, bir [ASP.NET Core Razor sayfaları](xref:razor-pages/index) UYGULAMASıNDA Entity Framework (EF) çekirdeğini nasıl kullanacağınızı gösteren bir öğretici serisinin ilkisidir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="7e71a-106">Öğreticiler, kurgusal bir Contoso Üniversitesi için bir Web sitesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="7e71a-107">Site, öğrenci giriş, kurs oluşturma ve eğitmen atamaları gibi işlevleri içerir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="7e71a-108">Öğretici ilk kod yaklaşımını kullanır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="7e71a-109">Bu öğreticiyi veritabanı ilk yaklaşımı kullanarak takip eden bilgiler için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/16897)bakın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="7e71a-110">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="7e71a-111">[Yönergeleri indirin](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="7e71a-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7e71a-112">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="7e71a-112">Prerequisites</span></span>

* <span data-ttu-id="7e71a-113">Sayfalarla yeni başladıysanız Razor , bunu başlatmadan önce [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) öğreticisini izleyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e71a-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e71a-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e71a-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e71a-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="7e71a-116">Veritabanı altyapıları</span><span class="sxs-lookup"><span data-stu-id="7e71a-116">Database engines</span></span>

<span data-ttu-id="7e71a-117">Visual Studio yönergeleri, yalnızca Windows üzerinde çalışan bir SQL Server Express sürümü olan [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)'yi kullanır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="7e71a-118">Visual Studio Code yönergeler, platformlar arası bir veritabanı altyapısı olan [SQLite](https://www.sqlite.org/)kullanır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="7e71a-119">SQLite kullanmayı seçerseniz, SQLite [Için DB tarayıcısı](https://sqlitebrowser.org/)gibi bir SQLite veritabanını yönetmek ve görüntülemek için üçüncü taraf bir araç indirip yükleyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="7e71a-120">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="7e71a-120">Troubleshooting</span></span>

<span data-ttu-id="7e71a-121">Giderebileceğiniz bir sorunla karşılaşırsanız, kodunuzu [Tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="7e71a-122">Yardım almanın iyi bir yolu, [ASP.NET Core etiketi](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF Core etiketi](https://stackoverflow.com/questions/tagged/entity-framework-core)kullanılarak StackOverflow.com 'e bir soru göndererek.</span><span class="sxs-lookup"><span data-stu-id="7e71a-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="7e71a-123">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="7e71a-123">The sample app</span></span>

<span data-ttu-id="7e71a-124">Bu öğreticilerde oluşturulan uygulama, temel bir üniversite web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="7e71a-125">Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="7e71a-126">Öğreticide oluşturulan ekranlardan bazıları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-126">Here are a few of the screens created in the tutorial.</span></span>

![Öğrenciler Dizin sayfası](intro/_static/students-index30.png)

![Öğrenciler düzenleme sayfası](intro/_static/student-edit30.png)

<span data-ttu-id="7e71a-129">Bu sitenin kullanıcı arabirimi stili yerleşik proje şablonlarına dayalıdır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="7e71a-130">Öğreticinin odağı, Kullanıcı arabirimini nasıl özelleştireceğinizi değil, ASP.NET Core EF Core nasıl kullanacağınızı kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

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

## <a name="create-the-web-app-project"></a><span data-ttu-id="7e71a-131">Web uygulaması projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="7e71a-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e71a-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e71a-132">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e71a-133">Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-133">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="7e71a-134">**ASP.NET Core Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-134">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="7e71a-135">Projeyi *Contosouniversity*olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-135">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="7e71a-136">Büyük harfler de dahil olmak üzere bu tam adı kullanmak önemlidir, bu nedenle kod kopyalanıp yapıştırılırken ad alanları eşleşir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-136">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="7e71a-137">Açılan menüden **.NET Core** ve **5,0 ASP.NET Core** seçin ve ardından **Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-137">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e71a-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e71a-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7e71a-139">Bir terminalde, proje klasörünün oluşturulması gereken klasöre gidin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-139">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="7e71a-140">Bir Razor sayfa projesi ve yeni proje klasörü oluşturmak için aşağıdaki komutları çalıştırın `cd` :</span><span class="sxs-lookup"><span data-stu-id="7e71a-140">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="7e71a-141">Site stilini ayarlayın</span><span class="sxs-lookup"><span data-stu-id="7e71a-141">Set up the site style</span></span>

<span data-ttu-id="7e71a-142">Aşağıdaki kodu kopyalayıp *Sayfalar/paylaşılan/_Layout. cshtml* dosyasına yapıştırın: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span><span class="sxs-lookup"><span data-stu-id="7e71a-142">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="7e71a-143">Düzen dosyası site üst bilgisini, alt bilgisini ve menüsünü ayarlar.</span><span class="sxs-lookup"><span data-stu-id="7e71a-143">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="7e71a-144">Yukarıdaki kod aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="7e71a-144">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="7e71a-145">Her "ContosoUniversity" öğesinin "Contoso Üniversitesi" olarak her oluşumu.</span><span class="sxs-lookup"><span data-stu-id="7e71a-145">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="7e71a-146">Üç oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-146">There are three occurrences.</span></span>
* <span data-ttu-id="7e71a-147">**Giriş** ve **Gizlilik** menü girişleri silinir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-147">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="7e71a-148">, **Öğrenciler**, **Kurslar**, **eğitmenler**ve **Departmanlar** **için girişler**eklenir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-148">Entries are added for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="7e71a-149">*Pages/Index. cshtml*dosyasında, dosyanın içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="7e71a-149">In *Pages/Index.cshtml*, replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="7e71a-150">Yukarıdaki kod, ASP.NET Core ile ilgili metnin yerine bu uygulamayla ilgili metni koyar.</span><span class="sxs-lookup"><span data-stu-id="7e71a-150">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="7e71a-151">Giriş sayfasının göründüğünü doğrulamak için uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-151">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="7e71a-152">Veri modeli</span><span class="sxs-lookup"><span data-stu-id="7e71a-152">The data model</span></span>

<span data-ttu-id="7e71a-153">Aşağıdaki bölümler bir veri modeli oluşturur:</span><span class="sxs-lookup"><span data-stu-id="7e71a-153">The following sections create a data model:</span></span>

![Kurs-kayıt-öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

<span data-ttu-id="7e71a-155">Bir öğrenci herhangi bir sayıda kursa kaydolabilir ve bir kurs, kayıtlı sayıda öğrenciye sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-155">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="7e71a-156">Öğrenci varlığı</span><span class="sxs-lookup"><span data-stu-id="7e71a-156">The Student entity</span></span>

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

* <span data-ttu-id="7e71a-158">Proje klasöründe bir *modeller* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7e71a-158">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="7e71a-159">Aşağıdaki kodla *modeller/öğrenci. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="7e71a-159">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="7e71a-160">`ID`Özelliği, bu sınıfa karşılık gelen veritabanı tablosunun birincil anahtar sütunu olur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-160">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="7e71a-161">Varsayılan olarak, EF Core `ID` birincil anahtar olarak adlandırılan bir özelliği Yorumlar `classnameID` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-161">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="7e71a-162">Bu nedenle birincil anahtar sınıfı için alternatif otomatik olarak tanınan ad `Student` olur `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-162">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="7e71a-163">Daha fazla bilgi için bkz. [EF Core-anahtarlar](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="7e71a-163">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="7e71a-164">`Enrollments`Özelliği bir [Gezinti özelliğidir](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="7e71a-164">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="7e71a-165">Gezinti özellikleri, bu varlıkla ilgili diğer varlıkları tutar.</span><span class="sxs-lookup"><span data-stu-id="7e71a-165">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="7e71a-166">Bu durumda, `Enrollments` bir `Student` varlığın özelliği `Enrollment` söz konusu öğrenciye ilişkin tüm varlıkları barındırır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-166">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="7e71a-167">Örneğin, veritabanındaki bir öğrenci satırında iki ilişkili kayıt satırı varsa, `Enrollments` gezinti özelliği bu Iki kayıt varlığını içerir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-167">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="7e71a-168">Veritabanında, bir kayıt satırı, Studentitıd sütunu öğrencinin ID değerini içeriyorsa bir öğrenci satırıyla ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-168">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="7e71a-169">Örneğin, bir öğrenci satırının ID = 1 olduğunu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="7e71a-169">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="7e71a-170">İlgili kayıt satırları Studentitıd = 1 olacaktır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-170">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="7e71a-171">Studentitıd, kayıt tablosundaki bir *yabancı anahtardır* .</span><span class="sxs-lookup"><span data-stu-id="7e71a-171">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="7e71a-172">`Enrollments`Özelliği, `ICollection<Enrollment>` birden çok ilgili kayıt varlığı olabileceğinden, olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-172">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="7e71a-173">Veya gibi diğer koleksiyon türlerini kullanabilirsiniz `List<Enrollment>` `HashSet<Enrollment>` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-173">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="7e71a-174">Kullanıldığında `ICollection<Enrollment>` , EF Core `HashSet<Enrollment>` Varsayılan olarak bir koleksiyon oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-174">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="7e71a-175">Kayıt varlığı</span><span class="sxs-lookup"><span data-stu-id="7e71a-175">The Enrollment entity</span></span>

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

<span data-ttu-id="7e71a-177">Aşağıdaki kodla *modeller/kayıt. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="7e71a-177">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="7e71a-178">`EnrollmentID`Bu özellik birincil anahtardır; bu varlık kendi başına değil, `classnameID` kendi modelini kullanır `ID` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-178">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="7e71a-179">Bir üretim veri modeli için bir model seçin ve bunu tutarlı bir şekilde kullanın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-179">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="7e71a-180">Bu öğretici her ikisinin de yalnızca bir iş olduğunu göstermek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-180">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="7e71a-181">`ID`Olmadan kullanmak `classname` , bazı veri modeli değişikliklerinin uygulanmasını kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-181">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="7e71a-182">`Grade`Özelliği bir `enum` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-182">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="7e71a-183">Tür bildiriminden sonraki soru işareti, `Grade` `Grade` özelliğin [null yapılabilir](/dotnet/csharp/programming-guide/nullable-types/)olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-183">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="7e71a-184">Null olan bir sınıf sıfır bir sınıf null değerinden farklıdır &mdash; veya henüz atanmamış olur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-184">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="7e71a-185">`StudentID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Student` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-185">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="7e71a-186">Bir varlık `Enrollment` bir `Student` varlıkla ilişkilendirilir, bu nedenle özellik tek bir varlık içerir `Student` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-186">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="7e71a-187">`CourseID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Course` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-187">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="7e71a-188">Bir `Enrollment` varlık bir `Course` varlıkla ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-188">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="7e71a-189">EF Core bir özelliği, adlandırılmış ise yabancı anahtar olarak yorumlar `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-189">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="7e71a-190">Örneğin, `StudentID` `Student` `Student` nesnenin birincil anahtarı olduğundan, gezinti özelliği için yabancı anahtardır `ID` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-190">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="7e71a-191">Yabancı anahtar özellikleri de adlandırılmış olabilir `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-191">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="7e71a-192">Örneğin, `CourseID` `Course` varlığın birincil anahtarı olduğundan `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-192">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="7e71a-193">Kurs varlığı</span><span class="sxs-lookup"><span data-stu-id="7e71a-193">The Course entity</span></span>

![Kurs varlık diyagramı](intro/_static/course-entity.png)

<span data-ttu-id="7e71a-195">Aşağıdaki kodla *modeller/kurs. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="7e71a-195">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="7e71a-196">`Enrollments`Özelliği bir gezinti özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-196">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="7e71a-197">Bir `Course` varlık herhangi bir sayıda varlıkla ilişkili olabilir `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-197">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="7e71a-198">`DatabaseGenerated`Özniteliği, uygulamanın veritabanını oluşturmak yerine birincil anahtarı belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="7e71a-198">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="7e71a-199">Derleyici hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-199">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="7e71a-200">Yapı iskelesi öğrenci sayfaları</span><span class="sxs-lookup"><span data-stu-id="7e71a-200">Scaffold Student pages</span></span>

<span data-ttu-id="7e71a-201">Bu bölümde, oluşturmak için ASP.NET Core scafkatlama aracını kullanırsınız:</span><span class="sxs-lookup"><span data-stu-id="7e71a-201">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="7e71a-202">EF Core `DbContext` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="7e71a-202">An EF Core `DbContext` class.</span></span> <span data-ttu-id="7e71a-203">Bağlam, belirli bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-203">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="7e71a-204"><xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>Sınıfından türetilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-204">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="7e71a-205">Razor varlık için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemlerini işleyen sayfalar `Student` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-205">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e71a-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e71a-206">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e71a-207">*Sayfalar/öğrenciler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7e71a-207">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="7e71a-208">**Çözüm Gezgini**, *Sayfalar/öğrenciler* klasörüne sağ tıklayın ve **Add** > **yeni yapı iskelesi Ekle öğesini**seçin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-208">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="7e71a-209">**Yeni yapı Iskelesi öğesi Ekle** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="7e71a-209">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="7e71a-210">Sol sekmede, **yüklü > ortak > Razor sayfaları** ' nı seçin</span><span class="sxs-lookup"><span data-stu-id="7e71a-210">In the left tab, select **Installed > Common > Razor Pages**</span></span>
  * <span data-ttu-id="7e71a-211">\*\* Razor Entity Framework (CRUD) Ekle kullanarak sayfa\*\* seçin > **ADD**.</span><span class="sxs-lookup"><span data-stu-id="7e71a-211">Select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="7e71a-212">\*\* Razor Entity Framework (CRUD) kullanarak sayfa ekle\*\* iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="7e71a-212">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="7e71a-213">**Model sınıfı** açılır penceresinde **öğrenci (Contosouniversity. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-213">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="7e71a-214">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-214">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="7e71a-215">Veri bağlamı adını yerine End ile değiştirin `SchoolContext` `ContosoUniversityContext` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-215">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="7e71a-216">Güncelleştirilmiş bağlam adı: `ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="7e71a-216">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="7e71a-217">**Add (Ekle)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-217">Select **Add**.</span></span>

<span data-ttu-id="7e71a-218">Aşağıdaki paketler otomatik olarak yüklenir:</span><span class="sxs-lookup"><span data-stu-id="7e71a-218">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e71a-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e71a-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7e71a-220">Gerekli NuGet paketlerini yüklemek için aşağıdaki .NET Core CLI komutlarını çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="7e71a-220">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="7e71a-221">Yapı iskelesi için Microsoft. VisualStudio. Web. CodeGeneration. Design paketi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-221">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="7e71a-222">Uygulama SQL Server kullanmayabilse de, scafkatlama aracı SQL Server paketine ihtiyaç duyuyor.</span><span class="sxs-lookup"><span data-stu-id="7e71a-222">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="7e71a-223">*Sayfalar/öğrenciler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7e71a-223">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="7e71a-224">[ASPNET-CodeGenerator scafkatlama aracı](xref:fundamentals/tools/dotnet-aspnet-codegenerator)'nı yüklemek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-224">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="7e71a-225">Fkatlama öğrenci sayfalarını iskele almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-225">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="7e71a-226">**Windows üzerinde**</span><span class="sxs-lookup"><span data-stu-id="7e71a-226">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="7e71a-227">**MacOS veya Linux üzerinde**</span><span class="sxs-lookup"><span data-stu-id="7e71a-227">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="7e71a-228">Önceki adım başarısız olursa, projeyi derleyin ve iskele adımını yeniden deneyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-228">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="7e71a-229">Yapı iskelesi işlemi:</span><span class="sxs-lookup"><span data-stu-id="7e71a-229">The scaffolding process:</span></span>

* <span data-ttu-id="7e71a-230">Razor *Sayfalar/öğrenciler* klasöründe sayfa oluşturur:</span><span class="sxs-lookup"><span data-stu-id="7e71a-230">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="7e71a-231">*. Cshtml* ve *Create.cshtml.cs* oluşturma</span><span class="sxs-lookup"><span data-stu-id="7e71a-231">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="7e71a-232">*Delete. cshtml* ve *delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7e71a-232">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="7e71a-233">*Details. cshtml* ve *details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7e71a-233">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="7e71a-234">*. Cshtml* ve *Edit.cshtml.cs* Düzenle</span><span class="sxs-lookup"><span data-stu-id="7e71a-234">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="7e71a-235">*Index. cshtml* ve *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7e71a-235">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="7e71a-236">*Data/SchoolContext. cs*oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-236">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="7e71a-237">*Startup.cs*içinde bağımlılık eklenmesine bağlam ekler.</span><span class="sxs-lookup"><span data-stu-id="7e71a-237">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="7e71a-238">*appsettings.js*için bir veritabanı bağlantı dizesi ekler.</span><span class="sxs-lookup"><span data-stu-id="7e71a-238">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="7e71a-239">Veritabanı bağlantı dizesi</span><span class="sxs-lookup"><span data-stu-id="7e71a-239">Database connection string</span></span>

<span data-ttu-id="7e71a-240">Scafkatlama aracı dosyadaki *appsettings.js* bir bağlantı dizesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-240">The scaffolding tool generates a connection string in the *appsettings.json* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e71a-241">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e71a-241">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7e71a-242">Bağlantı dizesi [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)belirtir:</span><span class="sxs-lookup"><span data-stu-id="7e71a-242">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/appsettings.json?highlight=11)]

<span data-ttu-id="7e71a-243">LocalDB, SQL Server Express veritabanı altyapısının hafif bir sürümüdür ve üretim kullanımı için değil uygulama geliştirmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-243">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="7e71a-244">Varsayılan olarak, LocalDB dizinde *. mdf* dosyaları oluşturur `C:/Users/<user>` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-244">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e71a-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e71a-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7e71a-246">*. Db*için SQLite bağlantı dizesini kısaltın:</span><span class="sxs-lookup"><span data-stu-id="7e71a-246">Shorten the SQLite connection string to *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="7e71a-247">Veritabanı bağlam sınıfını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="7e71a-247">Update the database context class</span></span>

<span data-ttu-id="7e71a-248">Belirli bir veri modeli için EF Core işlevselliğini koordine eden ana sınıf veritabanı bağlamı sınıfıdır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-248">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="7e71a-249">Bağlam [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-249">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="7e71a-250">Bağlam, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-250">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="7e71a-251">Bu projede, sınıfı olarak adlandırılır `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-251">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="7e71a-252">*Data/SchoolContext. cs* öğesini şu kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="7e71a-252">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="7e71a-253">Yukarıdaki kod tekil ile çoğul arasında değişir `DbSet<Student> Student` `DbSet<Student> Students` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-253">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="7e71a-254">RazorSayfa kodunun yeni adla eşleşmesini sağlamak için `DBSet` , ' den küresel bir değişiklik yapın:`_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="7e71a-254">To make the Razor Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="7e71a-255">Hedef: `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="7e71a-255">to: `_context.Students.`</span></span>

<span data-ttu-id="7e71a-256">8 oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-256">There are 8 occurrences.</span></span>

<span data-ttu-id="7e71a-257">Bir varlık kümesi birden çok varlık içerdiğinden birçok geliştirici, `DBSet` özellik adlarının çoğul olması gerektiğini tercih eder.</span><span class="sxs-lookup"><span data-stu-id="7e71a-257">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="7e71a-258">Vurgulanan kod:</span><span class="sxs-lookup"><span data-stu-id="7e71a-258">The highlighted code:</span></span>

* <span data-ttu-id="7e71a-259">Her bir varlık kümesi için bir [Dbset \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-259">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="7e71a-260">EF Core terminoloji:</span><span class="sxs-lookup"><span data-stu-id="7e71a-260">In EF Core terminology:</span></span>
  * <span data-ttu-id="7e71a-261">Bir varlık kümesi, genellikle bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-261">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="7e71a-262">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-262">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="7e71a-263">Çağırır <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .</span><span class="sxs-lookup"><span data-stu-id="7e71a-263">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="7e71a-264">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="7e71a-264">`OnModelCreating`:</span></span>
  * <span data-ttu-id="7e71a-265">Başlatıldığı zaman çağrılır `SchoolContext` , ancak model kilitlenmeden önce ve bağlamı başlatmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-265">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="7e71a-266">Daha sonraki öğreticide, `Student` varlığın diğer varlıklara başvuruları olacağı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-266">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="7e71a-267">Derleyici hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-267">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="7e71a-268">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="7e71a-268">Startup.cs</span></span>

<span data-ttu-id="7e71a-269">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-269">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7e71a-270">Gibi hizmetler, `SchoolContext` uygulamanın başlatılması sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-270">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="7e71a-271">Sayfalar gibi bu hizmetleri gerektiren bileşenler Razor , bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-271">Components that require these services, such as Razor Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="7e71a-272">Bir veritabanı bağlamı örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-272">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="7e71a-273">Scafkatlama Aracı, bağlam sınıfını bağımlılık ekleme kapsayıcısına otomatik olarak kaydetti.</span><span class="sxs-lookup"><span data-stu-id="7e71a-273">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e71a-274">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e71a-274">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7e71a-275">Aşağıdaki Vurgulanan satırlar scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="7e71a-275">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e71a-276">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e71a-276">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7e71a-277">Desteği çağrıları tarafından eklenen kodu doğrulayın `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-277">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="7e71a-278">Üretim veritabanını kullanma hakkında bilgi için bkz. [geliştirme Için SQLite kullanma, üretime yönelik SQL Server](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) .</span><span class="sxs-lookup"><span data-stu-id="7e71a-278">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="7e71a-279">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-279">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="7e71a-280">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki *appsettings.js* bağlantı dizesini okur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-280">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="7e71a-281">Veritabanı özel durum filtresini ekleme</span><span class="sxs-lookup"><span data-stu-id="7e71a-281">Add the database exception filter</span></span>

<span data-ttu-id="7e71a-282">`AddDatabaseDeveloperPageExceptionFilter` `ConfigureServices` Aşağıdaki kodda gösterildiği gibi öğesine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="7e71a-282">Add `AddDatabaseDeveloperPageExceptionFilter` to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e71a-283">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e71a-283">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="7e71a-284">[Microsoft. aspnetcore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-284">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="7e71a-285">PMC 'de, NuGet paketini eklemek için aşağıdaki komutu girin:</span><span class="sxs-lookup"><span data-stu-id="7e71a-285">In the PMC, enter the following command to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.1.20451.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e71a-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e71a-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="7e71a-287">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore`NuGet paketi, Entity Framework Core hata sayfaları için ASP.NET Core ara yazılım sağlar.</span><span class="sxs-lookup"><span data-stu-id="7e71a-287">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="7e71a-288">Bu ara yazılım Entity Framework Core geçişlerle hataları algılamaya ve tanılamanıza yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-288">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="7e71a-289">Veritabanını oluşturma</span><span class="sxs-lookup"><span data-stu-id="7e71a-289">Create the database</span></span>

<span data-ttu-id="7e71a-290">Mevcut değilse veritabanını oluşturmak için *program.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="7e71a-290">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="7e71a-291">Bağlam için bir veritabanı varsa, [Ensuyeniden](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) oluşturma yöntemi hiçbir eylemde bulunmaz.</span><span class="sxs-lookup"><span data-stu-id="7e71a-291">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="7e71a-292">Veritabanı yoksa, veritabanını ve şemayı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-292">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="7e71a-293">`EnsureCreated` veri modeli değişikliklerini işlemek için aşağıdaki iş akışını sunar:</span><span class="sxs-lookup"><span data-stu-id="7e71a-293">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="7e71a-294">Veritabanını silin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-294">Delete the database.</span></span> <span data-ttu-id="7e71a-295">Mevcut veriler kaybolur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-295">Any existing data is lost.</span></span>
* <span data-ttu-id="7e71a-296">Veri modelini değiştirin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-296">Change the data model.</span></span> <span data-ttu-id="7e71a-297">Örneğin, bir alan ekleyin `EmailAddress` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-297">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="7e71a-298">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-298">Run the app.</span></span>
* <span data-ttu-id="7e71a-299">`EnsureCreated` yeni şemaya sahip bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-299">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="7e71a-300">Bu iş akışı, verileri korumanıza gerek olmadığı sürece, şema hızlı bir şekilde gelişen zaman geliştirme aşamasında iyi bir şekilde gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-300">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="7e71a-301">Veritabanına girilen verilerin korunması gerektiğinde bu durum farklıdır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-301">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="7e71a-302">Bu durumda, geçişleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-302">When that is the case, use migrations.</span></span>

<span data-ttu-id="7e71a-303">Öğretici serisinde daha sonra tarafından oluşturulan veritabanını siler `EnsureCreated` ve bunun yerine geçişleri kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="7e71a-303">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="7e71a-304">Tarafından oluşturulan bir veritabanı, `EnsureCreated` geçişler kullanılarak güncelleştirilemiyor.</span><span class="sxs-lookup"><span data-stu-id="7e71a-304">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="7e71a-305">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="7e71a-305">Test the app</span></span>

* <span data-ttu-id="7e71a-306">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-306">Run the app.</span></span>
* <span data-ttu-id="7e71a-307">**Öğrenciler** bağlantısını seçin ve ardından **Yeni oluştur**.</span><span class="sxs-lookup"><span data-stu-id="7e71a-307">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="7e71a-308">Düzenle, Ayrıntılar ve Sil bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-308">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="7e71a-309">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="7e71a-309">Seed the database</span></span>

<span data-ttu-id="7e71a-310">`EnsureCreated`Yöntemi boş bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-310">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="7e71a-311">Bu bölüm, veritabanını test verileriyle dolduran kodu ekler.</span><span class="sxs-lookup"><span data-stu-id="7e71a-311">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="7e71a-312">Aşağıdaki kodla *veri/Dbınizer. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="7e71a-312">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="7e71a-313">Kod, veritabanında herhangi bir öğrenci olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="7e71a-313">The code checks if there are any students in the database.</span></span> <span data-ttu-id="7e71a-314">Öğrenci yoksa, veritabanına test verileri ekler.</span><span class="sxs-lookup"><span data-stu-id="7e71a-314">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="7e71a-315">Performansı iyileştirmek için Koleksiyonlar yerine diziler halinde test verileri oluşturur `List<T>` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-315">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="7e71a-316">*Program.cs*içinde, `EnsureCreated` çağrıyı bir çağrı ile değiştirin `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="7e71a-316">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="7e71a-317">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e71a-317">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7e71a-318">Çalışıyorsa uygulamayı durdurun ve **Paket Yöneticisi konsolunda** (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="7e71a-318">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="7e71a-319">`Y`Veritabanını silmek için ile yanıtlayın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-319">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e71a-320">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e71a-320">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7e71a-321">Çalışıyorsa uygulamayı durdurun ve *cu. db* dosyasını silin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-321">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="7e71a-322">Uygulamayı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-322">Restart the app.</span></span>
* <span data-ttu-id="7e71a-323">Sağlanan verileri görmek için öğrenciler sayfasını seçin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-323">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="7e71a-324">Veritabanını görüntüleme</span><span class="sxs-lookup"><span data-stu-id="7e71a-324">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e71a-325">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e71a-325">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e71a-326">Visual Studio 'daki **Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-326">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="7e71a-327">SSOX 'te, **(LocalDB) \MSSQLLocalDB > veritabanları > SchoolContext-{GUID}** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-327">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="7e71a-328">Veritabanı adı, daha önce belirttiğiniz bağlam adından ve bir tire ve bir GUID ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-328">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="7e71a-329">**Tables** düğümünü genişletin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-329">Expand the **Tables** node.</span></span>
* <span data-ttu-id="7e71a-330">Oluşturulan sütunları ve tabloya yerleştirilen satırları görmek için **öğrenci** tablosuna sağ tıklayın ve **verileri görüntüle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-330">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="7e71a-331">Modelin tablo şemasına nasıl eşlendiğini görmek için **öğrenci** tablosuna sağ tıklayın ve **kodu görüntüle** ' ye tıklayın `Student` `Student` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-331">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e71a-332">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e71a-332">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7e71a-333">Veritabanı şemasını ve sağlanan verileri görüntülemek için SQLite aracınızı kullanın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-333">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="7e71a-334">Veritabanı dosyası *cu. db* olarak adlandırılır ve proje klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-334">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="7e71a-335">Zaman uyumsuz kod</span><span class="sxs-lookup"><span data-stu-id="7e71a-335">Asynchronous code</span></span>

<span data-ttu-id="7e71a-336">Zaman uyumsuz programlama, ASP.NET Core ve EF Core için varsayılan moddur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-336">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="7e71a-337">Web sunucusunda sınırlı sayıda iş parçacığı bulunur ve yüksek yük durumlarında tüm kullanılabilir iş parçacıkları kullanımda olabilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-337">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="7e71a-338">Bu durumda, sunucu, iş parçacıkları boşaltılana kadar yeni istekleri işleyemez.</span><span class="sxs-lookup"><span data-stu-id="7e71a-338">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="7e71a-339">Zaman uyumlu kod ile, çok sayıda iş parçacığı g/ç 'nin tamamlanmasını beklediği için iş çalışmadıklarında bağlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-339">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="7e71a-340">Zaman uyumsuz kod ile, bir işlem g/ç 'yi tamamlanmayı beklerken, sunucunun diğer istekleri işlemek için kullanması için iş parçacığı serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-340">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="7e71a-341">Sonuç olarak, zaman uyumsuz kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucu gecikmeksizin daha fazla trafiği işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-341">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="7e71a-342">Zaman uyumsuz kod, çalışma zamanında az miktarda yük getirir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-342">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="7e71a-343">Düşük trafik durumlarında, performans artışı göz ardı edilebilir, ancak yüksek trafik durumları için olası performans iyileştirmesi oldukça önemlidir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-343">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="7e71a-344">Aşağıdaki kodda, [Async](/dotnet/csharp/language-reference/keywords/async) anahtar sözcüğü, `Task<T>` return değeri, `await` anahtar sözcüğü ve `ToListAsync` yöntemi kodu zaman uyumsuz olarak yürütür.</span><span class="sxs-lookup"><span data-stu-id="7e71a-344">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="7e71a-345">`async`Anahtar sözcüğü derleyiciye şunu söyler:</span><span class="sxs-lookup"><span data-stu-id="7e71a-345">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="7e71a-346">Yöntem gövdesinin parçaları için geri çağrılar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7e71a-346">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="7e71a-347">Döndürülen [görev](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) nesnesini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7e71a-347">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="7e71a-348">`Task<T>`Dönüş türü, devam eden işi temsil eder.</span><span class="sxs-lookup"><span data-stu-id="7e71a-348">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="7e71a-349">`await`Anahtar sözcüğü, derleyicinin yöntemi iki parçaya böetmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-349">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="7e71a-350">İlk bölüm, zaman uyumsuz olarak başlatılan işlemle biter.</span><span class="sxs-lookup"><span data-stu-id="7e71a-350">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="7e71a-351">İkinci bölüm, işlem tamamlandığında çağrılan bir geri çağırma yöntemine konur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-351">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="7e71a-352">`ToListAsync` , Uzantı yönteminin zaman uyumsuz sürümüdür `ToList` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-352">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="7e71a-353">EF Core kullanan zaman uyumsuz kodu yazarken dikkat edilmesi gereken bazı şeyler:</span><span class="sxs-lookup"><span data-stu-id="7e71a-353">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="7e71a-354">Yalnızca sorguları veya komutlarının veritabanına gönderilmesine neden olan deyimler zaman uyumsuz olarak yürütülür.</span><span class="sxs-lookup"><span data-stu-id="7e71a-354">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="7e71a-355">,, `ToListAsync` , `SingleOrDefaultAsync` `FirstOrDefaultAsync` Ve içerir `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-355">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="7e71a-356">Yalnızca, gibi bir değiştiren deyimler içermez `IQueryable` `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-356">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="7e71a-357">EF Core bağlamı iş parçacığı açısından güvenli değildir: paralel olarak birden çok işlem yapmayı denemeyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-357">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="7e71a-358">Zaman uyumsuz kodun performans avantajlarından yararlanmak için, veritabanına sorgu gönderen EF Core yöntemleri çağırıyorsa kitaplık paketlerinin (örneğin, sayfalama için) zaman uyumsuz olarak kullanılacağını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-358">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="7e71a-359">.NET 'te zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. Async [and await ile](/dotnet/csharp/programming-guide/concepts/async/)zaman uyumsuz [genel bakış](/dotnet/standard/async) ve zaman uyumsuz programlama</span><span class="sxs-lookup"><span data-stu-id="7e71a-359">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="7e71a-360">Performansla ilgili önemli noktalar</span><span class="sxs-lookup"><span data-stu-id="7e71a-360">Performance considerations</span></span>

<span data-ttu-id="7e71a-361">Genel olarak, bir Web sayfası rastgele sayıda satır yüklememelidir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-361">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="7e71a-362">Bir sorgu, sayfalama veya sınırlandırma yaklaşımını kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-362">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="7e71a-363">Örneğin, önceki sorgu `Take` döndürülen satırları sınırlamak için kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="7e71a-363">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="7e71a-364">Bir görünümde büyük bir tablonun numaralandırılması, bir veritabanı özel durumu numaralandırma aracılığıyla bir bölüm oluşursa kısmen oluşturulmuş bir HTTP 200 yanıtı döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-364">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="7e71a-365"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> Varsayılan olarak 1024 ' dir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-365"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="7e71a-366">Aşağıdaki kod kümeleri `MaxModelBindingCollectionSize` :</span><span class="sxs-lookup"><span data-stu-id="7e71a-366">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="7e71a-367">Sayfalama daha sonra öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-367">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7e71a-368">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="7e71a-368">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="7e71a-369">Sonraki öğretici</span><span class="sxs-lookup"><span data-stu-id="7e71a-369">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="7e71a-370">Bu, bir [ASP.NET Core Razor sayfaları](xref:razor-pages/index) UYGULAMASıNDA Entity Framework (EF) çekirdeğini nasıl kullanacağınızı gösteren bir öğretici serisinin ilkisidir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-370">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="7e71a-371">Öğreticiler, kurgusal bir Contoso Üniversitesi için bir Web sitesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-371">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="7e71a-372">Site, öğrenci giriş, kurs oluşturma ve eğitmen atamaları gibi işlevleri içerir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-372">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="7e71a-373">Öğretici ilk kod yaklaşımını kullanır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-373">The tutorial uses the code first approach.</span></span> <span data-ttu-id="7e71a-374">Bu öğreticiyi veritabanı ilk yaklaşımı kullanarak takip eden bilgiler için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/16897)bakın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-374">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="7e71a-375">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-375">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="7e71a-376">[Yönergeleri indirin](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="7e71a-376">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7e71a-377">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="7e71a-377">Prerequisites</span></span>

* <span data-ttu-id="7e71a-378">Sayfalarla yeni başladıysanız Razor , bunu başlatmadan önce [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) öğreticisini izleyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-378">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e71a-379">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e71a-379">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e71a-380">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e71a-380">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="7e71a-381">Veritabanı altyapıları</span><span class="sxs-lookup"><span data-stu-id="7e71a-381">Database engines</span></span>

<span data-ttu-id="7e71a-382">Visual Studio yönergeleri, yalnızca Windows üzerinde çalışan bir SQL Server Express sürümü olan [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)'yi kullanır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-382">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="7e71a-383">Visual Studio Code yönergeler, platformlar arası bir veritabanı altyapısı olan [SQLite](https://www.sqlite.org/)kullanır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-383">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="7e71a-384">SQLite kullanmayı seçerseniz, SQLite [Için DB tarayıcısı](https://sqlitebrowser.org/)gibi bir SQLite veritabanını yönetmek ve görüntülemek için üçüncü taraf bir araç indirip yükleyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-384">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="7e71a-385">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="7e71a-385">Troubleshooting</span></span>

<span data-ttu-id="7e71a-386">Giderebileceğiniz bir sorunla karşılaşırsanız, kodunuzu [Tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-386">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="7e71a-387">Yardım almanın iyi bir yolu, [ASP.NET Core etiketi](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF Core etiketi](https://stackoverflow.com/questions/tagged/entity-framework-core)kullanılarak StackOverflow.com 'e bir soru göndererek.</span><span class="sxs-lookup"><span data-stu-id="7e71a-387">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="7e71a-388">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="7e71a-388">The sample app</span></span>

<span data-ttu-id="7e71a-389">Bu öğreticilerde oluşturulan uygulama, temel bir üniversite web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-389">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="7e71a-390">Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-390">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="7e71a-391">Öğreticide oluşturulan ekranlardan bazıları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-391">Here are a few of the screens created in the tutorial.</span></span>

![Öğrenciler Dizin sayfası](intro/_static/students-index30.png)

![Öğrenciler düzenleme sayfası](intro/_static/student-edit30.png)

<span data-ttu-id="7e71a-394">Bu sitenin kullanıcı arabirimi stili yerleşik proje şablonlarına dayalıdır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-394">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="7e71a-395">Öğreticinin odağı, Kullanıcı arabirimini nasıl özelleştireceğinizi değil EF Core kullanma konusunda yer alır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-395">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="7e71a-396">Tamamlanan projenin kaynak kodunu almak için sayfanın üst kısmındaki bağlantıyı izleyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-396">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="7e71a-397">*Cu30* klasörü, öğreticinin ASP.NET Core 3,0 sürümü için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-397">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="7e71a-398">1-7 öğreticileri için kodun durumunu yansıtan dosyalar *cu30snapshots* klasöründe bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-398">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e71a-399">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e71a-399">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7e71a-400">Tamamlanmış projeyi indirdikten sonra uygulamayı çalıştırmak için:</span><span class="sxs-lookup"><span data-stu-id="7e71a-400">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="7e71a-401">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-401">Build the project.</span></span>
* <span data-ttu-id="7e71a-402">Paket Yöneticisi konsolu 'nda (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="7e71a-402">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="7e71a-403">Veritabanını temel alarak projeyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-403">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e71a-404">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e71a-404">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7e71a-405">Tamamlanmış projeyi indirdikten sonra uygulamayı çalıştırmak için:</span><span class="sxs-lookup"><span data-stu-id="7e71a-405">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="7e71a-406">*Contosouniversity. csproj*öğesini silin ve *Contosoüniversıtysqlite. csproj* öğesini *contosouniversity. csproj*olarak yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-406">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="7e71a-407">*Program.cs*' de, açıklama dışarı bu `#define Startup` şekilde `StartupSQLite` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-407">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="7e71a-408">*appSettings.js*silin ve üzerinde *appSettings.js* *appSettingsSQLite.js* yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-408">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="7e71a-409">*Geçişler* klasörünü silin ve *migrationssql* öğesini *geçişlerle*yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-409">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="7e71a-410">İçin genel arama yapın `#if SQLiteVersion` ve `#if SQLiteVersion` ilgili `#endif` ifadeyi kaldırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-410">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="7e71a-411">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-411">Build the project.</span></span>
* <span data-ttu-id="7e71a-412">Proje klasöründeki bir komut isteminde aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="7e71a-412">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="7e71a-413">SQLite aracında şu SQL ifadesini çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="7e71a-413">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="7e71a-414">Veritabanını temel alarak projeyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-414">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="7e71a-415">Web uygulaması projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="7e71a-415">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e71a-416">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e71a-416">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e71a-417">Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-417">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="7e71a-418">**ASP.NET Core Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-418">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="7e71a-419">Projeyi *Contosouniversity*olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-419">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="7e71a-420">Büyük harfler de dahil olmak üzere bu tam adı kullanmak önemlidir, bu nedenle kod kopyalanıp yapıştırılırken ad alanları eşleşir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-420">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="7e71a-421">Açılan menüden **.NET Core** ve **3,0 ASP.NET Core** seçin ve ardından **Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-421">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e71a-422">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e71a-422">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7e71a-423">Bir terminalde, proje klasörünün oluşturulması gereken klasöre gidin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-423">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="7e71a-424">Bir Razor sayfa projesi ve yeni proje klasörü oluşturmak için aşağıdaki komutları çalıştırın `cd` :</span><span class="sxs-lookup"><span data-stu-id="7e71a-424">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="7e71a-425">Site stilini ayarlayın</span><span class="sxs-lookup"><span data-stu-id="7e71a-425">Set up the site style</span></span>

<span data-ttu-id="7e71a-426">*Sayfa/paylaşılan/_Layout. cshtml*'yi güncelleştirerek site üst bilgisini, alt bilgisini ve menüsünü ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="7e71a-426">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="7e71a-427">"ContosoUniversity" öğesinin her oluşumunu "Contoso Üniversitesi" olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-427">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="7e71a-428">Üç oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-428">There are three occurrences.</span></span>

* <span data-ttu-id="7e71a-429">**Giriş** ve **Gizlilik** menü girişlerini silin ve **hakkında**, **öğrenciler**, **Kurslar**, **eğitmenler**ve **Departmanlar**için girişler ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-429">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="7e71a-430">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-430">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="7e71a-431">*Pages/Index. cshtml*dosyasında, ASP.NET Core hakkındaki metni bu uygulamayla ilgili metinle değiştirmek için dosyanın içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="7e71a-431">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="7e71a-432">Giriş sayfasının göründüğünü doğrulamak için uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-432">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="7e71a-433">Veri modeli</span><span class="sxs-lookup"><span data-stu-id="7e71a-433">The data model</span></span>

<span data-ttu-id="7e71a-434">Aşağıdaki bölümler bir veri modeli oluşturur:</span><span class="sxs-lookup"><span data-stu-id="7e71a-434">The following sections create a data model:</span></span>

![Kurs-kayıt-öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

<span data-ttu-id="7e71a-436">Bir öğrenci herhangi bir sayıda kursa kaydolabilir ve bir kurs, kayıtlı sayıda öğrenciye sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-436">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="7e71a-437">Öğrenci varlığı</span><span class="sxs-lookup"><span data-stu-id="7e71a-437">The Student entity</span></span>

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

* <span data-ttu-id="7e71a-439">Proje klasöründe bir *modeller* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7e71a-439">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="7e71a-440">Aşağıdaki kodla *modeller/öğrenci. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="7e71a-440">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="7e71a-441">`ID`Özelliği, bu sınıfa karşılık gelen veritabanı tablosunun birincil anahtar sütunu olur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-441">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="7e71a-442">Varsayılan olarak, EF Core `ID` birincil anahtar olarak adlandırılan bir özelliği Yorumlar `classnameID` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-442">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="7e71a-443">Bu nedenle birincil anahtar sınıfı için alternatif otomatik olarak tanınan ad `Student` olur `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-443">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="7e71a-444">Daha fazla bilgi için bkz. [EF Core-anahtarlar](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="7e71a-444">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="7e71a-445">`Enrollments`Özelliği bir [Gezinti özelliğidir](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="7e71a-445">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="7e71a-446">Gezinti özellikleri, bu varlıkla ilgili diğer varlıkları tutar.</span><span class="sxs-lookup"><span data-stu-id="7e71a-446">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="7e71a-447">Bu durumda, `Enrollments` bir `Student` varlığın özelliği `Enrollment` söz konusu öğrenciye ilişkin tüm varlıkları barındırır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-447">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="7e71a-448">Örneğin, veritabanındaki bir öğrenci satırında iki ilişkili kayıt satırı varsa, `Enrollments` gezinti özelliği bu Iki kayıt varlığını içerir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-448">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="7e71a-449">Veritabanında, bir kayıt satırı, Studentitıd sütunu öğrencinin ID değerini içeriyorsa bir öğrenci satırıyla ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-449">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="7e71a-450">Örneğin, bir öğrenci satırının ID = 1 olduğunu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="7e71a-450">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="7e71a-451">İlgili kayıt satırları Studentitıd = 1 olacaktır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-451">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="7e71a-452">Studentitıd, kayıt tablosundaki bir *yabancı anahtardır* .</span><span class="sxs-lookup"><span data-stu-id="7e71a-452">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="7e71a-453">`Enrollments`Özelliği, `ICollection<Enrollment>` birden çok ilgili kayıt varlığı olabileceğinden, olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-453">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="7e71a-454">Veya gibi diğer koleksiyon türlerini kullanabilirsiniz `List<Enrollment>` `HashSet<Enrollment>` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-454">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="7e71a-455">Kullanıldığında `ICollection<Enrollment>` , EF Core `HashSet<Enrollment>` Varsayılan olarak bir koleksiyon oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-455">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="7e71a-456">Kayıt varlığı</span><span class="sxs-lookup"><span data-stu-id="7e71a-456">The Enrollment entity</span></span>

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

<span data-ttu-id="7e71a-458">Aşağıdaki kodla *modeller/kayıt. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="7e71a-458">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="7e71a-459">`EnrollmentID`Bu özellik birincil anahtardır; bu varlık kendi başına değil, `classnameID` kendi modelini kullanır `ID` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-459">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="7e71a-460">Bir üretim veri modeli için bir model seçin ve bunu tutarlı bir şekilde kullanın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-460">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="7e71a-461">Bu öğretici her ikisinin de yalnızca bir iş olduğunu göstermek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-461">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="7e71a-462">`ID`Olmadan kullanmak `classname` , bazı veri modeli değişikliklerinin uygulanmasını kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-462">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="7e71a-463">`Grade`Özelliği bir `enum` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-463">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="7e71a-464">Tür bildiriminden sonraki soru işareti, `Grade` `Grade` özelliğin [null yapılabilir](/dotnet/csharp/programming-guide/nullable-types/)olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-464">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="7e71a-465">Null olan bir sınıf sıfır bir sınıf null değerinden farklıdır &mdash; veya henüz atanmamış olur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-465">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="7e71a-466">`StudentID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Student` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-466">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="7e71a-467">Bir varlık `Enrollment` bir `Student` varlıkla ilişkilendirilir, bu nedenle özellik tek bir varlık içerir `Student` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-467">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="7e71a-468">`CourseID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Course` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-468">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="7e71a-469">Bir `Enrollment` varlık bir `Course` varlıkla ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-469">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="7e71a-470">EF Core bir özelliği, adlandırılmış ise yabancı anahtar olarak yorumlar `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-470">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="7e71a-471">Örneğin, `StudentID` `Student` `Student` nesnenin birincil anahtarı olduğundan, gezinti özelliği için yabancı anahtardır `ID` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-471">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="7e71a-472">Yabancı anahtar özellikleri de adlandırılmış olabilir `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-472">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="7e71a-473">Örneğin, `CourseID` `Course` varlığın birincil anahtarı olduğundan `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-473">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="7e71a-474">Kurs varlığı</span><span class="sxs-lookup"><span data-stu-id="7e71a-474">The Course entity</span></span>

![Kurs varlık diyagramı](intro/_static/course-entity.png)

<span data-ttu-id="7e71a-476">Aşağıdaki kodla *modeller/kurs. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="7e71a-476">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="7e71a-477">`Enrollments`Özelliği bir gezinti özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-477">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="7e71a-478">Bir `Course` varlık herhangi bir sayıda varlıkla ilişkili olabilir `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-478">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="7e71a-479">`DatabaseGenerated`Özniteliği, uygulamanın veritabanını oluşturmak yerine birincil anahtarı belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="7e71a-479">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="7e71a-480">Derleyici hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-480">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="7e71a-481">Yapı iskelesi öğrenci sayfaları</span><span class="sxs-lookup"><span data-stu-id="7e71a-481">Scaffold Student pages</span></span>

<span data-ttu-id="7e71a-482">Bu bölümde, oluşturmak için ASP.NET Core scafkatlama aracını kullanırsınız:</span><span class="sxs-lookup"><span data-stu-id="7e71a-482">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="7e71a-483">EF Core *bağlamı* sınıfı.</span><span class="sxs-lookup"><span data-stu-id="7e71a-483">An EF Core *context* class.</span></span> <span data-ttu-id="7e71a-484">Bağlam, belirli bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-484">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="7e71a-485">`Microsoft.EntityFrameworkCore.DbContext`Sınıfından türetilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-485">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="7e71a-486">Razor varlık için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemlerini işleyen sayfalar `Student` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-486">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e71a-487">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e71a-487">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e71a-488">*Sayfalar* klasöründe bir *öğrenciler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7e71a-488">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="7e71a-489">**Çözüm Gezgini**, *Sayfalar/öğrenciler* klasörüne sağ tıklayın ve **Add** > **yeni yapı iskelesi Ekle öğesini**seçin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-489">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="7e71a-490">**Yapı iskelesi Ekle** iletişim kutusunda \*\* Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar\*\* ' ı seçin > **ADD**.</span><span class="sxs-lookup"><span data-stu-id="7e71a-490">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="7e71a-491">\*\* Razor Entity Framework (CRUD) kullanarak sayfa ekle\*\* iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="7e71a-491">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="7e71a-492">**Model sınıfı** açılır penceresinde **öğrenci (Contosouniversity. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-492">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="7e71a-493">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-493">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="7e71a-494">*Contosouniversity. modeller. Contosoüniversıtycontext* olan veri bağlamı adını *Contosouniversity. Data. SchoolContext*olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-494">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="7e71a-495">**Add (Ekle)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-495">Select **Add**.</span></span>

<span data-ttu-id="7e71a-496">Aşağıdaki paketler otomatik olarak yüklenir:</span><span class="sxs-lookup"><span data-stu-id="7e71a-496">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e71a-497">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e71a-497">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7e71a-498">Gerekli NuGet paketlerini yüklemek için aşağıdaki .NET Core CLI komutlarını çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="7e71a-498">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="7e71a-499">Yapı iskelesi için Microsoft. VisualStudio. Web. CodeGeneration. Design paketi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-499">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="7e71a-500">Uygulama SQL Server kullanmayabilse de, scafkatlama aracı SQL Server paketine ihtiyaç duyuyor.</span><span class="sxs-lookup"><span data-stu-id="7e71a-500">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="7e71a-501">*Sayfalar/öğrenciler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7e71a-501">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="7e71a-502">[ASPNET-CodeGenerator scafkatlama aracı](xref:fundamentals/tools/dotnet-aspnet-codegenerator)'nı yüklemek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-502">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="7e71a-503">Fkatlama öğrenci sayfalarını iskele almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-503">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="7e71a-504">**Windows üzerinde**</span><span class="sxs-lookup"><span data-stu-id="7e71a-504">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="7e71a-505">**MacOS veya Linux üzerinde**</span><span class="sxs-lookup"><span data-stu-id="7e71a-505">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="7e71a-506">Önceki adımla ilgili bir sorununuz varsa, projeyi derleyin ve yapı iskelesi adımını yeniden deneyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-506">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="7e71a-507">Yapı iskelesi işlemi:</span><span class="sxs-lookup"><span data-stu-id="7e71a-507">The scaffolding process:</span></span>

* <span data-ttu-id="7e71a-508">Razor *Sayfalar/öğrenciler* klasöründe sayfa oluşturur:</span><span class="sxs-lookup"><span data-stu-id="7e71a-508">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="7e71a-509">*. Cshtml* ve *Create.cshtml.cs* oluşturma</span><span class="sxs-lookup"><span data-stu-id="7e71a-509">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="7e71a-510">*Delete. cshtml* ve *delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7e71a-510">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="7e71a-511">*Details. cshtml* ve *details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7e71a-511">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="7e71a-512">*. Cshtml* ve *Edit.cshtml.cs* Düzenle</span><span class="sxs-lookup"><span data-stu-id="7e71a-512">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="7e71a-513">*Index. cshtml* ve *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="7e71a-513">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="7e71a-514">*Data/SchoolContext. cs*oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-514">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="7e71a-515">*Startup.cs*içinde bağımlılık eklenmesine bağlam ekler.</span><span class="sxs-lookup"><span data-stu-id="7e71a-515">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="7e71a-516">*appsettings.js*için bir veritabanı bağlantı dizesi ekler.</span><span class="sxs-lookup"><span data-stu-id="7e71a-516">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="7e71a-517">Veritabanı bağlantı dizesi</span><span class="sxs-lookup"><span data-stu-id="7e71a-517">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e71a-518">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e71a-518">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7e71a-519">*appsettings.js* dosya [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)bağlantı dizesini belirtir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-519">The *appsettings.json* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="7e71a-520">LocalDB, SQL Server Express veritabanı altyapısının hafif bir sürümüdür ve üretim kullanımı için değil uygulama geliştirmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-520">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="7e71a-521">Varsayılan olarak, LocalDB dizinde *. mdf* dosyaları oluşturur `C:/Users/<user>` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-521">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e71a-522">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e71a-522">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7e71a-523">Bağlantı dizesini *cu. db*adlı bir SQLite veritabanı dosyasını işaret etmek üzere değiştirin:</span><span class="sxs-lookup"><span data-stu-id="7e71a-523">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="7e71a-524">Veritabanı bağlam sınıfını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="7e71a-524">Update the database context class</span></span>

<span data-ttu-id="7e71a-525">Belirli bir veri modeli için EF Core işlevselliğini koordine eden ana sınıf veritabanı bağlamı sınıfıdır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-525">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="7e71a-526">Bağlam [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-526">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="7e71a-527">Bağlam, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-527">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="7e71a-528">Bu projede, sınıfı olarak adlandırılır `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-528">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="7e71a-529">*Data/SchoolContext. cs* öğesini şu kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="7e71a-529">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="7e71a-530">Vurgulanan kod, her bir varlık kümesi için bir [Dbset \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-530">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="7e71a-531">EF Core terminoloji:</span><span class="sxs-lookup"><span data-stu-id="7e71a-531">In EF Core terminology:</span></span>

* <span data-ttu-id="7e71a-532">Bir varlık kümesi, genellikle bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-532">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="7e71a-533">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-533">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="7e71a-534">Bir varlık kümesi birden çok varlık içerdiğinden, DBSet özellikleri çoğul adlar olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-534">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="7e71a-535">Yapı iskelesi aracı bir `Student` dbset oluşturduğundan, bu adım çoğul olarak değişir `Students` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-535">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="7e71a-536">RazorSayfa kodunun yeni DBSet adıyla eşleşmesini sağlamak için, tüm projesi genelinde küresel bir değişiklik yapın `_context.Student` `_context.Students` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-536">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="7e71a-537">8 oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-537">There are 8 occurrences.</span></span>

<span data-ttu-id="7e71a-538">Derleyici hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-538">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="7e71a-539">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="7e71a-539">Startup.cs</span></span>

<span data-ttu-id="7e71a-540">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-540">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7e71a-541">Hizmetler (EF Core veritabanı bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-541">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="7e71a-542">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-542">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="7e71a-543">Bir veritabanı bağlamı örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-543">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="7e71a-544">Scafkatlama Aracı, bağlam sınıfını bağımlılık ekleme kapsayıcısına otomatik olarak kaydetti.</span><span class="sxs-lookup"><span data-stu-id="7e71a-544">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e71a-545">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e71a-545">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e71a-546">`ConfigureServices`' De, vurgulanan satırlar scaffolder tarafından eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="7e71a-546">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e71a-547">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e71a-547">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7e71a-548">' De `ConfigureServices` , desteği tarafından eklenen kodun çağrılarından emin olun `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-548">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="7e71a-549">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-549">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="7e71a-550">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki *appsettings.js* bağlantı dizesini okur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-550">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="7e71a-551">Veritabanını oluşturma</span><span class="sxs-lookup"><span data-stu-id="7e71a-551">Create the database</span></span>

<span data-ttu-id="7e71a-552">Mevcut değilse veritabanını oluşturmak için *program.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="7e71a-552">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="7e71a-553">Bağlam için bir veritabanı varsa, [Ensuyeniden](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) oluşturma yöntemi hiçbir eylemde bulunmaz.</span><span class="sxs-lookup"><span data-stu-id="7e71a-553">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="7e71a-554">Veritabanı yoksa, veritabanını ve şemayı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-554">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="7e71a-555">`EnsureCreated` veri modeli değişikliklerini işlemek için aşağıdaki iş akışını sunar:</span><span class="sxs-lookup"><span data-stu-id="7e71a-555">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="7e71a-556">Veritabanını silin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-556">Delete the database.</span></span> <span data-ttu-id="7e71a-557">Mevcut veriler kaybolur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-557">Any existing data is lost.</span></span>
* <span data-ttu-id="7e71a-558">Veri modelini değiştirin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-558">Change the data model.</span></span> <span data-ttu-id="7e71a-559">Örneğin, bir alan ekleyin `EmailAddress` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-559">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="7e71a-560">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-560">Run the app.</span></span>
* <span data-ttu-id="7e71a-561">`EnsureCreated` yeni şemaya sahip bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-561">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="7e71a-562">Bu iş akışı, verileri korumanıza gerek olmadığı sürece, şema hızlı bir şekilde gelişen zaman geliştirme aşamasında iyi bir şekilde gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-562">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="7e71a-563">Veritabanına girilen verilerin korunması gerektiğinde bu durum farklıdır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-563">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="7e71a-564">Bu durumda, geçişleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-564">When that is the case, use migrations.</span></span>

<span data-ttu-id="7e71a-565">Öğretici serisinde daha sonra tarafından oluşturulan veritabanını siler `EnsureCreated` ve bunun yerine geçişleri kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="7e71a-565">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="7e71a-566">Tarafından oluşturulan bir veritabanı, `EnsureCreated` geçişler kullanılarak güncelleştirilemiyor.</span><span class="sxs-lookup"><span data-stu-id="7e71a-566">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="7e71a-567">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="7e71a-567">Test the app</span></span>

* <span data-ttu-id="7e71a-568">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-568">Run the app.</span></span>
* <span data-ttu-id="7e71a-569">**Öğrenciler** bağlantısını seçin ve ardından **Yeni oluştur**.</span><span class="sxs-lookup"><span data-stu-id="7e71a-569">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="7e71a-570">Düzenle, Ayrıntılar ve Sil bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-570">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="7e71a-571">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="7e71a-571">Seed the database</span></span>

<span data-ttu-id="7e71a-572">`EnsureCreated`Yöntemi boş bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-572">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="7e71a-573">Bu bölüm, veritabanını test verileriyle dolduran kodu ekler.</span><span class="sxs-lookup"><span data-stu-id="7e71a-573">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="7e71a-574">Aşağıdaki kodla *veri/Dbınizer. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="7e71a-574">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="7e71a-575">Kod, veritabanında herhangi bir öğrenci olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="7e71a-575">The code checks if there are any students in the database.</span></span> <span data-ttu-id="7e71a-576">Öğrenci yoksa, veritabanına test verileri ekler.</span><span class="sxs-lookup"><span data-stu-id="7e71a-576">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="7e71a-577">Performansı iyileştirmek için Koleksiyonlar yerine diziler halinde test verileri oluşturur `List<T>` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-577">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="7e71a-578">*Program.cs*içinde, `EnsureCreated` çağrıyı bir çağrı ile değiştirin `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="7e71a-578">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="7e71a-579">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e71a-579">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7e71a-580">Çalışıyorsa uygulamayı durdurun ve **Paket Yöneticisi konsolunda** (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="7e71a-580">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e71a-581">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e71a-581">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7e71a-582">Çalışıyorsa uygulamayı durdurun ve *cu. db* dosyasını silin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-582">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="7e71a-583">Uygulamayı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-583">Restart the app.</span></span>

* <span data-ttu-id="7e71a-584">Sağlanan verileri görmek için öğrenciler sayfasını seçin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-584">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="7e71a-585">Veritabanını görüntüleme</span><span class="sxs-lookup"><span data-stu-id="7e71a-585">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e71a-586">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e71a-586">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e71a-587">Visual Studio 'daki **Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-587">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="7e71a-588">SSOX 'te, **(LocalDB) \MSSQLLocalDB > veritabanları > SchoolContext-{GUID}** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-588">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="7e71a-589">Veritabanı adı, daha önce belirttiğiniz bağlam adından ve bir tire ve bir GUID ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-589">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="7e71a-590">**Tables** düğümünü genişletin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-590">Expand the **Tables** node.</span></span>
* <span data-ttu-id="7e71a-591">Oluşturulan sütunları ve tabloya yerleştirilen satırları görmek için **öğrenci** tablosuna sağ tıklayın ve **verileri görüntüle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-591">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="7e71a-592">Modelin tablo şemasına nasıl eşlendiğini görmek için **öğrenci** tablosuna sağ tıklayın ve **kodu görüntüle** ' ye tıklayın `Student` `Student` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-592">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e71a-593">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e71a-593">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7e71a-594">Veritabanı şemasını ve sağlanan verileri görüntülemek için SQLite aracınızı kullanın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-594">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="7e71a-595">Veritabanı dosyası *cu. db* olarak adlandırılır ve proje klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-595">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="7e71a-596">Zaman uyumsuz kod</span><span class="sxs-lookup"><span data-stu-id="7e71a-596">Asynchronous code</span></span>

<span data-ttu-id="7e71a-597">Zaman uyumsuz programlama, ASP.NET Core ve EF Core için varsayılan moddur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-597">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="7e71a-598">Web sunucusunda sınırlı sayıda iş parçacığı bulunur ve yüksek yük durumlarında tüm kullanılabilir iş parçacıkları kullanımda olabilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-598">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="7e71a-599">Bu durumda, sunucu, iş parçacıkları boşaltılana kadar yeni istekleri işleyemez.</span><span class="sxs-lookup"><span data-stu-id="7e71a-599">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="7e71a-600">Zaman uyumlu kodla, çok sayıda iş parçacığı, g/ç 'nin tamamlanmasını beklediği için aslında herhangi bir iş yapmadıklarında bağlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-600">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="7e71a-601">Zaman uyumsuz kod ile, bir işlem g/ç 'yi tamamlanmayı beklerken, sunucunun diğer istekleri işlemek için kullanması için iş parçacığı serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-601">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="7e71a-602">Sonuç olarak, zaman uyumsuz kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucu gecikmeksizin daha fazla trafiği işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-602">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="7e71a-603">Zaman uyumsuz kod, çalışma zamanında az miktarda yük getirir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-603">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="7e71a-604">Düşük trafik durumlarında, performans artışı göz ardı edilebilir, ancak yüksek trafik durumları için olası performans iyileştirmesi oldukça önemlidir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-604">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="7e71a-605">Aşağıdaki kodda, [Async](/dotnet/csharp/language-reference/keywords/async) anahtar sözcüğü, `Task<T>` return değeri, `await` anahtar sözcüğü ve `ToListAsync` yöntemi kodu zaman uyumsuz olarak yürütür.</span><span class="sxs-lookup"><span data-stu-id="7e71a-605">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="7e71a-606">`async`Anahtar sözcüğü derleyiciye şunu söyler:</span><span class="sxs-lookup"><span data-stu-id="7e71a-606">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="7e71a-607">Yöntem gövdesinin parçaları için geri çağrılar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7e71a-607">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="7e71a-608">Döndürülen [görev](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) nesnesini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7e71a-608">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="7e71a-609">`Task<T>`Dönüş türü, devam eden işi temsil eder.</span><span class="sxs-lookup"><span data-stu-id="7e71a-609">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="7e71a-610">`await`Anahtar sözcüğü, derleyicinin yöntemi iki parçaya böetmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-610">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="7e71a-611">İlk bölüm, zaman uyumsuz olarak başlatılan işlemle biter.</span><span class="sxs-lookup"><span data-stu-id="7e71a-611">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="7e71a-612">İkinci bölüm, işlem tamamlandığında çağrılan bir geri çağırma yöntemine konur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-612">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="7e71a-613">`ToListAsync` , Uzantı yönteminin zaman uyumsuz sürümüdür `ToList` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-613">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="7e71a-614">EF Core kullanan zaman uyumsuz kodu yazarken dikkat edilmesi gereken bazı şeyler:</span><span class="sxs-lookup"><span data-stu-id="7e71a-614">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="7e71a-615">Yalnızca sorguları veya komutlarının veritabanına gönderilmesine neden olan deyimler zaman uyumsuz olarak yürütülür.</span><span class="sxs-lookup"><span data-stu-id="7e71a-615">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="7e71a-616">,, `ToListAsync` , `SingleOrDefaultAsync` `FirstOrDefaultAsync` Ve içerir `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-616">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="7e71a-617">Yalnızca, gibi bir değiştiren deyimler içermez `IQueryable` `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-617">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="7e71a-618">EF Core bağlamı iş parçacığı açısından güvenli değildir: paralel olarak birden çok işlem yapmayı denemeyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-618">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="7e71a-619">Zaman uyumsuz kodun performans avantajlarından yararlanmak için, veritabanına sorgu gönderen EF Core yöntemleri çağırıyorsa kitaplık paketlerinin (örneğin, sayfalama için) zaman uyumsuz olarak kullanılacağını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-619">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="7e71a-620">.NET 'te zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. Async [and await ile](/dotnet/csharp/programming-guide/concepts/async/)zaman uyumsuz [genel bakış](/dotnet/standard/async) ve zaman uyumsuz programlama</span><span class="sxs-lookup"><span data-stu-id="7e71a-620">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="7e71a-621">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="7e71a-621">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="7e71a-622">Sonraki öğretici</span><span class="sxs-lookup"><span data-stu-id="7e71a-622">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7e71a-623">Contoso Üniversitesi örnek Web uygulaması, Razor Entity Framework (EF) Core kullanarak ASP.NET Core sayfalı bir uygulamanın nasıl oluşturulacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-623">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="7e71a-624">Örnek uygulama, kurgusal bir Contoso Üniversitesi için bir Web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-624">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="7e71a-625">Öğrenci giriş, kurs oluşturma ve eğitmen atamaları gibi işlevleri içerir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-625">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="7e71a-626">Bu sayfa, Contoso Üniversitesi örnek uygulamasının nasıl oluşturulacağını açıklayan bir öğretici serisinin ilkisidir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-626">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="7e71a-627">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-627">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="7e71a-628">[Yönergeleri indirin](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="7e71a-628">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7e71a-629">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="7e71a-629">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e71a-630">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e71a-630">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e71a-631">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e71a-631">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="7e71a-632">[ Razor Sayfalarla](xref:razor-pages/index)benzerlik.</span><span class="sxs-lookup"><span data-stu-id="7e71a-632">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="7e71a-633">Yeni programcılar, bu seriyi başlatmadan önce [sayfalarla çalışmaya başlama Razor sayfasını](xref:tutorials/razor-pages/razor-pages-start) tamamlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-633">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="7e71a-634">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="7e71a-634">Troubleshooting</span></span>

<span data-ttu-id="7e71a-635">Çözemiyoruz bir sorunla karşılaşırsanız, kodunuzun [Tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)karşılaştırılmasıyla genellikle çözümü bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7e71a-635">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="7e71a-636">[ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) [için bir](https://stackoverflow.com/questions/tagged/asp.net-core) soru göndererek yardım almanın iyi bir yolu.</span><span class="sxs-lookup"><span data-stu-id="7e71a-636">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="7e71a-637">Contoso Üniversitesi web uygulaması</span><span class="sxs-lookup"><span data-stu-id="7e71a-637">The Contoso University web app</span></span>

<span data-ttu-id="7e71a-638">Bu öğreticilerde oluşturulan uygulama, temel bir üniversite web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-638">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="7e71a-639">Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-639">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="7e71a-640">Öğreticide oluşturulan ekranlardan bazıları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-640">Here are a few of the screens created in the tutorial.</span></span>

![Öğrenciler Dizin sayfası](intro/_static/students-index.png)

![Öğrenciler düzenleme sayfası](intro/_static/student-edit.png)

<span data-ttu-id="7e71a-643">Bu sitenin kullanıcı arabirimi stili yerleşik şablonlar tarafından üretilme kadar yakın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-643">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="7e71a-644">Eğitim odağı, Razor kullanıcı arabiriminden değil, sayfalarla EF Core.</span><span class="sxs-lookup"><span data-stu-id="7e71a-644">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="7e71a-645">Contosoüniversitesi Razor sayfaları Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="7e71a-645">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e71a-646">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e71a-646">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e71a-647">Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-647">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="7e71a-648">Yeni bir ASP.NET Core Web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7e71a-648">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="7e71a-649">Projeyi **Contosouniversity**olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-649">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="7e71a-650">Kod kopyalama/yapıştırma olduğunda, ad alanlarının eşleşmesi için *Contosouniversity* projesini adlandırmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-650">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="7e71a-651">Açılan listede **ASP.NET Core 2,1** ' i seçin ve ardından **Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-651">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="7e71a-652">Yukarıdaki adımların görüntüleri için bkz. [ Razor Web uygulaması oluşturma](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="7e71a-652">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="7e71a-653">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-653">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e71a-654">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e71a-654">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="7e71a-655">Site stilini ayarlayın</span><span class="sxs-lookup"><span data-stu-id="7e71a-655">Set up the site style</span></span>

<span data-ttu-id="7e71a-656">Site menüsünü, düzeni ve giriş sayfasını birkaç değişiklik ayarlar.</span><span class="sxs-lookup"><span data-stu-id="7e71a-656">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="7e71a-657">*Sayfaları/paylaşılan/_Layout. cshtml* 'yi aşağıdaki değişikliklerle güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="7e71a-657">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="7e71a-658">"ContosoUniversity" öğesinin her oluşumunu "Contoso Üniversitesi" olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-658">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="7e71a-659">Üç oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-659">There are three occurrences.</span></span>

* <span data-ttu-id="7e71a-660">**Öğrenciler**, **Kurslar**, **eğitmenler**ve **Departmanlar**için menü girişleri ekleyin ve **kişi** menü girişini silin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-660">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="7e71a-661">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-661">The changes are highlighted.</span></span> <span data-ttu-id="7e71a-662">(Tüm *biçimlendirme gösterilmez.* )</span><span class="sxs-lookup"><span data-stu-id="7e71a-662">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="7e71a-663">*Pages/Index. cshtml*dosyasında, ASP.net ve MVC hakkındaki metni bu uygulamayla ilgili metinle değiştirmek için dosyanın içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="7e71a-663">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="7e71a-664">Veri modelini oluşturma</span><span class="sxs-lookup"><span data-stu-id="7e71a-664">Create the data model</span></span>

<span data-ttu-id="7e71a-665">Contoso Üniversitesi uygulaması için varlık sınıfları oluşturma.</span><span class="sxs-lookup"><span data-stu-id="7e71a-665">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="7e71a-666">Aşağıdaki üç varlıkla başlayın:</span><span class="sxs-lookup"><span data-stu-id="7e71a-666">Start with the following three entities:</span></span>

![Kurs-kayıt-öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

<span data-ttu-id="7e71a-668">Ve varlıkları arasında bire çok ilişki vardır `Student` `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-668">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="7e71a-669">Ve varlıkları arasında bire çok ilişki vardır `Course` `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-669">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="7e71a-670">Bir öğrenci herhangi bir sayıda kursa kaydolabilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-670">A student can enroll in any number of courses.</span></span> <span data-ttu-id="7e71a-671">Bir kurs, kayıtlı sayıda öğrenciye sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-671">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="7e71a-672">Aşağıdaki bölümlerde, bu varlıkların her biri için bir sınıf oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-672">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="7e71a-673">Öğrenci varlığı</span><span class="sxs-lookup"><span data-stu-id="7e71a-673">The Student entity</span></span>

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

<span data-ttu-id="7e71a-675">*Modeller* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7e71a-675">Create a *Models* folder.</span></span> <span data-ttu-id="7e71a-676">*Modeller* klasöründe, *Student.cs* adlı bir sınıf dosyasını aşağıdaki kodla oluşturun:</span><span class="sxs-lookup"><span data-stu-id="7e71a-676">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="7e71a-677">`ID`Özelliği, bu sınıfa karşılık gelen veritabanı (DB) tablosunun birincil anahtar sütunu olur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-677">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="7e71a-678">Varsayılan olarak, EF Core `ID` birincil anahtar olarak adlandırılan bir özelliği Yorumlar `classnameID` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-678">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="7e71a-679">`classnameID`' De, `classname` sınıfının adıdır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-679">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="7e71a-680">Diğer otomatik olarak tanınan birincil anahtar, `StudentID` Önceki örnekte yer verilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-680">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="7e71a-681">`Enrollments`Özelliği bir [Gezinti özelliğidir](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="7e71a-681">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="7e71a-682">Gezinti özellikleri bu varlıkla ilgili diğer varlıkların bağlantısını sağlar.</span><span class="sxs-lookup"><span data-stu-id="7e71a-682">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="7e71a-683">Bu durumda, `Enrollments` öğesinin özelliği ile `Student entity` `Enrollment` ilgili tüm varlıkları barındırır `Student` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-683">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="7e71a-684">Örneğin, VERITABANıNDAKI bir öğrenci satırında iki ilişkili kayıt satırı varsa, `Enrollments` gezinti özelliği bu iki `Enrollment` varlığı içerir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-684">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="7e71a-685">İlgili `Enrollment` satır, söz konusu öğrencinin birincil anahtar değerini sütununda içeren bir satırdır `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-685">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="7e71a-686">Örneğin, ID = 1 olan öğrencinin tabloda iki satır olduğunu varsayalım `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-686">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="7e71a-687">`Enrollment`Tabloda = 1 olan iki satır vardır `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-687">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="7e71a-688">`StudentID` , tablodaki `Enrollment` öğrencisi belirten bir yabancı anahtardır `Student` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-688">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="7e71a-689">Bir gezinti özelliği birden çok varlık tutabileceğinden, gezinti özelliği gibi bir liste türü olmalıdır `ICollection<T>` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-689">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="7e71a-690">`ICollection<T>` belirtilebilir veya veya gibi bir tür olabilir `List<T>` `HashSet<T>` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-690">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="7e71a-691">Kullanıldığında `ICollection<T>` , EF Core `HashSet<T>` Varsayılan olarak bir koleksiyon oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-691">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="7e71a-692">Birden çok varlığı tutan gezinti özellikleri, çoktan çoğa ve bire çok ilişkilerden gelir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-692">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="7e71a-693">Kayıt varlığı</span><span class="sxs-lookup"><span data-stu-id="7e71a-693">The Enrollment entity</span></span>

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

<span data-ttu-id="7e71a-695">*Modeller* klasöründe, aşağıdaki kodla *enrollment.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="7e71a-695">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="7e71a-696">`EnrollmentID`Özelliği birincil anahtardır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-696">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="7e71a-697">Bu varlık `classnameID` `ID` , varlığı beğenmek yerine, modelini kullanır `Student` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-697">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="7e71a-698">Genellikle geliştiriciler bir model seçer ve bunu veri modeli boyunca kullanır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-698">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="7e71a-699">Daha sonraki bir öğreticide, veri modelinde devralmayı daha kolay hale getirmek için ClassName olmadan ID kullanımı gösterilmemiştir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-699">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="7e71a-700">`Grade`Özelliği bir `enum` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-700">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="7e71a-701">Tür bildiriminden sonraki soru işareti, `Grade` `Grade` özelliğin null yapılabilir olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-701">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="7e71a-702">Null olan bir sınıf sıfır bir sınıf ile farklıdır--null, henüz bir sınıf bilinmediğini veya henüz atanmadığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-702">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="7e71a-703">`StudentID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Student` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-703">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="7e71a-704">Bir varlık `Enrollment` bir `Student` varlıkla ilişkilendirilir, bu nedenle özellik tek bir varlık içerir `Student` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-704">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="7e71a-705">`Student`Varlık, `Student.Enrollments` birden çok varlık içeren gezinti özelliğinden farklıdır `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-705">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="7e71a-706">`CourseID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Course` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-706">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="7e71a-707">Bir `Enrollment` varlık bir `Course` varlıkla ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-707">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="7e71a-708">EF Core bir özelliği, adlandırılmış ise yabancı anahtar olarak yorumlar `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-708">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="7e71a-709">Örneğin, `StudentID` `Student` gezinti özelliği için `Student` varlığın birincil anahtarı olduğundan `ID` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-709">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="7e71a-710">Yabancı anahtar özellikleri de adlandırılmış olabilir `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-710">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="7e71a-711">Örneğin, `CourseID` `Course` varlığın birincil anahtarı olduğundan `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-711">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="7e71a-712">Kurs varlığı</span><span class="sxs-lookup"><span data-stu-id="7e71a-712">The Course entity</span></span>

![Kurs varlık diyagramı](intro/_static/course-entity.png)

<span data-ttu-id="7e71a-714">*Modeller* klasöründe, aşağıdaki kodla *Course.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="7e71a-714">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="7e71a-715">`Enrollments`Özelliği bir gezinti özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-715">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="7e71a-716">Bir `Course` varlık herhangi bir sayıda varlıkla ilişkili olabilir `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-716">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="7e71a-717">`DatabaseGenerated`Özniteliği, UYGULAMANıN DB oluşturmak yerine birincil anahtarı belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="7e71a-717">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="7e71a-718">Öğrenci modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="7e71a-718">Scaffold the student model</span></span>

<span data-ttu-id="7e71a-719">Bu bölümde öğrenci modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-719">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="7e71a-720">Diğer bir deyişle, scafkatlama aracı öğrenci modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-720">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="7e71a-721">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-721">Build the project.</span></span>
* <span data-ttu-id="7e71a-722">*Sayfalar/öğrenciler* klasörünü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7e71a-722">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7e71a-723">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e71a-723">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7e71a-724">**Çözüm Gezgini**, *Sayfalar/öğrenciler* klasörüne sağ tıklayarak **Add** > **yeni yapı iskelesi öğesi**ekleyin >.</span><span class="sxs-lookup"><span data-stu-id="7e71a-724">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="7e71a-725">**Yapı iskelesi Ekle** iletişim kutusunda \*\* Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar\*\* ' ı seçin > **ADD**.</span><span class="sxs-lookup"><span data-stu-id="7e71a-725">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="7e71a-726">\*\* Razor Entity Framework (CRUD) kullanarak sayfa ekle\*\* iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="7e71a-726">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="7e71a-727">**Model sınıfı** açılır penceresinde **öğrenci (Contosouniversity. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-727">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="7e71a-728">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin ve üretilen adı **Contosouniversity. modeller. SchoolContext**olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-728">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="7e71a-729">**Veri bağlamı sınıfı** açılır penceresinde **Contosouniversity. modeller. SchoolContext** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-729">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="7e71a-730">**Add (Ekle)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-730">Select **Add**.</span></span>

![CRUD iletişim kutusu](intro/_static/s1.png)

<span data-ttu-id="7e71a-732">Önceki adımla ilgili bir sorununuz varsa [Film modeli](xref:tutorials/razor-pages/model#scaffold-the-movie-model) ' ne bakın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-732">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e71a-733">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e71a-733">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7e71a-734">Öğrenci modelini iskele almak için aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-734">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="7e71a-735">Yapı iskelesi işlemi oluşturulur ve aşağıdaki dosyaları değiştirdi:</span><span class="sxs-lookup"><span data-stu-id="7e71a-735">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="7e71a-736">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="7e71a-736">Files created</span></span>

* <span data-ttu-id="7e71a-737">*Sayfalar/öğrenciler* Oluşturma, silme, ayrıntılar, düzenleme, dizin oluşturma.</span><span class="sxs-lookup"><span data-stu-id="7e71a-737">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="7e71a-738">*Data/SchoolContext. cs*</span><span class="sxs-lookup"><span data-stu-id="7e71a-738">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="7e71a-739">Dosya güncelleştirmeleri</span><span class="sxs-lookup"><span data-stu-id="7e71a-739">File updates</span></span>

* <span data-ttu-id="7e71a-740">*Startup.cs* : Bu dosyadaki değişiklikler sonraki bölümde ayrıntılıdır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-740">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="7e71a-741">*appsettings.js* : yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesi eklenir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-741">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="7e71a-742">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="7e71a-742">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="7e71a-743">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-743">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7e71a-744">Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-744">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="7e71a-745">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-745">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="7e71a-746">Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-746">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="7e71a-747">Scafkatlama aracı otomatik olarak bir DB bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.</span><span class="sxs-lookup"><span data-stu-id="7e71a-747">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="7e71a-748">`ConfigureServices` *Startup.cs*içindeki yöntemi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-748">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="7e71a-749">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="7e71a-749">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="7e71a-750">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-750">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="7e71a-751">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki *appsettings.js* bağlantı dizesini okur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-751">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="7e71a-752">Ana güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="7e71a-752">Update main</span></span>

<span data-ttu-id="7e71a-753">*Program.cs*' de, `Main` aşağıdakileri yapmak için yöntemini değiştirin:</span><span class="sxs-lookup"><span data-stu-id="7e71a-753">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="7e71a-754">Bağımlılık ekleme kapsayıcısından bir DB bağlam örneği alın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-754">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="7e71a-755">Yeniden  [oluşturulmasını](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)çağırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-755">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="7e71a-756">`EnsureCreated`Yöntem tamamlandığında bağlamı atın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-756">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="7e71a-757">Aşağıdaki kod güncelleştirilmiş *program.cs* dosyasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-757">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="7e71a-758">`EnsureCreated` bağlam veritabanının mevcut olmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="7e71a-758">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="7e71a-759">Varsa, hiçbir eylem yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="7e71a-759">If it exists, no action is taken.</span></span> <span data-ttu-id="7e71a-760">Yoksa, veritabanı ve tüm şeması oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-760">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="7e71a-761">`EnsureCreated` veritabanını oluşturmak için geçişleri kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="7e71a-761">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="7e71a-762">İle oluşturulan bir veritabanı `EnsureCreated` daha sonra geçişler kullanılarak güncelleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="7e71a-762">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="7e71a-763">`EnsureCreated` , aşağıdaki iş akışına izin veren uygulama başlatma sırasında çağrılır:</span><span class="sxs-lookup"><span data-stu-id="7e71a-763">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="7e71a-764">VERITABANıNı silin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-764">Delete the DB.</span></span>
* <span data-ttu-id="7e71a-765">DB şemasını değiştirin (örneğin, bir `EmailAddress` alan ekleyin).</span><span class="sxs-lookup"><span data-stu-id="7e71a-765">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="7e71a-766">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-766">Run the app.</span></span>
* <span data-ttu-id="7e71a-767">`EnsureCreated` sütunu ile bir VERITABANı oluşturur `EmailAddress` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-767">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="7e71a-768">`EnsureCreated` şema hızlı bir şekilde gelişmede geliştirmede daha erken kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-768">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="7e71a-769">Öğreticide daha sonra DB silinir ve geçişler kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-769">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="7e71a-770">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="7e71a-770">Test the app</span></span>

<span data-ttu-id="7e71a-771">Uygulamayı çalıştırın ve ilkeyi kabul edin cookie .</span><span class="sxs-lookup"><span data-stu-id="7e71a-771">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="7e71a-772">Bu uygulama, kişisel bilgileri saklar.</span><span class="sxs-lookup"><span data-stu-id="7e71a-772">This app doesn't keep personal information.</span></span> <span data-ttu-id="7e71a-773">İlke hakkında, cookie [AB Genel VERI koruma YÖNETMELIĞI (GDPR) desteğiyle](xref:security/gdpr)ilgili bilgi edinebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7e71a-773">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="7e71a-774">**Öğrenciler** bağlantısını seçin ve ardından **Yeni oluştur**.</span><span class="sxs-lookup"><span data-stu-id="7e71a-774">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="7e71a-775">Düzenle, Ayrıntılar ve Sil bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-775">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="7e71a-776">SchoolContext DB bağlamını inceleyin</span><span class="sxs-lookup"><span data-stu-id="7e71a-776">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="7e71a-777">Belirli bir veri modeli için EF Core işlevselliğini koordine eden ana sınıf DB bağlam sınıfıdır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-777">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="7e71a-778">Veri bağlamı [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-778">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="7e71a-779">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-779">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="7e71a-780">Bu projede, sınıfı olarak adlandırılır `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-780">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="7e71a-781">Aşağıdaki kodla *SchoolContext.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="7e71a-781">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="7e71a-782">Vurgulanan kod, her bir varlık kümesi için bir [Dbset \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-782">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="7e71a-783">EF Core terminoloji:</span><span class="sxs-lookup"><span data-stu-id="7e71a-783">In EF Core terminology:</span></span>

* <span data-ttu-id="7e71a-784">Bir varlık kümesi, genellikle bir DB tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-784">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="7e71a-785">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-785">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="7e71a-786">`DbSet<Enrollment>` ve `DbSet<Course>` atlanamaz.</span><span class="sxs-lookup"><span data-stu-id="7e71a-786">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="7e71a-787">EF Core `Student` `Enrollment` , varlık varlığa başvurduğundan ve varlık `Enrollment` varlığa başvurduğundan bunları örtülü olarak içerir `Course` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-787">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="7e71a-788">Bu öğreticide, `DbSet<Enrollment>` ve `DbSet<Course>` içinde tutun `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-788">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="7e71a-789">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="7e71a-789">SQL Server Express LocalDB</span></span>

<span data-ttu-id="7e71a-790">Bağlantı dizesi [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)belirtir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-790">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="7e71a-791">LocalDB, SQL Server Express veritabanı altyapısının hafif bir sürümüdür ve üretim kullanımı için değil uygulama geliştirmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-791">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="7e71a-792">LocalDB, istek üzerine başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-792">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="7e71a-793">Varsayılan olarak, LocalDB dizinde *. mdf* DB dosyaları oluşturur `C:/Users/<user>` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-793">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="7e71a-794">Test verileriyle VERITABANıNı başlatmak için kod ekleme</span><span class="sxs-lookup"><span data-stu-id="7e71a-794">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="7e71a-795">EF Core boş bir VERITABANı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-795">EF Core creates an empty DB.</span></span> <span data-ttu-id="7e71a-796">Bu bölümde, `Initialize` Test verileriyle doldurmak için bir yöntem yazılır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-796">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="7e71a-797">*Veri* klasöründe, *DbInitializer.cs* adlı yeni bir sınıf dosyası oluşturun ve aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="7e71a-797">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="7e71a-798">Note: önceki kod, `Models` yerine ad alanı için ( `namespace ContosoUniversity.Models` ) kullanır `Data` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-798">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="7e71a-799">`Models` , scaffolder tarafından oluşturulan kodla tutarlıdır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-799">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="7e71a-800">Daha fazla bilgi için bkz. [GitHub yapı iskelesi sorunu](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="7e71a-800">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="7e71a-801">Kod, VERITABANıNDA herhangi bir öğrenci olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="7e71a-801">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="7e71a-802">Veritabanında hiç öğrenci yoksa, DB test verileriyle başlatılır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-802">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="7e71a-803">Performansı iyileştirmek için test verilerini koleksiyonlar yerine dizilere yükler `List<T>` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-803">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="7e71a-804">`EnsureCreated`YÖNTEMI DB bağlamı için otomatik olarak DB oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-804">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="7e71a-805">VERITABANı varsa, `EnsureCreated` veritabanını değiştirmeden döndürür.</span><span class="sxs-lookup"><span data-stu-id="7e71a-805">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="7e71a-806">*Program.cs*içinde, `Main` çağrısı yapılacak yöntemi değiştirin `Initialize` :</span><span class="sxs-lookup"><span data-stu-id="7e71a-806">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="7e71a-807">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7e71a-807">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7e71a-808">Çalışıyorsa uygulamayı durdurun ve **Paket Yöneticisi konsolunda** (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="7e71a-808">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="7e71a-809">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7e71a-809">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7e71a-810">Çalışıyorsa uygulamayı durdurun ve *cu. db* dosyasını silin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-810">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="7e71a-811">VERITABANıNı görüntüleme</span><span class="sxs-lookup"><span data-stu-id="7e71a-811">View the DB</span></span>

<span data-ttu-id="7e71a-812">Veritabanı adı, daha önce belirttiğiniz bağlam adından ve bir tire ve bir GUID ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-812">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="7e71a-813">Bu nedenle, veritabanı adı "SchoolContext-{GUID}" olacaktır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-813">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="7e71a-814">GUID her kullanıcı için farklı olacaktır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-814">The GUID will be different for each user.</span></span>
<span data-ttu-id="7e71a-815">Visual Studio 'daki **Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-815">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="7e71a-816">SSOX 'te, **(LocalDB) \MSSQLLocalDB > veritabanları > SchoolContext-{GUID}**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-816">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="7e71a-817">**Tables** düğümünü genişletin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-817">Expand the **Tables** node.</span></span>

<span data-ttu-id="7e71a-818">Oluşturulan sütunları ve tabloya yerleştirilen satırları görmek için **öğrenci** tablosuna sağ tıklayın ve **verileri görüntüle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-818">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="7e71a-819">Zaman uyumsuz kod</span><span class="sxs-lookup"><span data-stu-id="7e71a-819">Asynchronous code</span></span>

<span data-ttu-id="7e71a-820">Zaman uyumsuz programlama, ASP.NET Core ve EF Core için varsayılan moddur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-820">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="7e71a-821">Web sunucusunda sınırlı sayıda iş parçacığı bulunur ve yüksek yük durumlarında tüm kullanılabilir iş parçacıkları kullanımda olabilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-821">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="7e71a-822">Bu durumda, sunucu, iş parçacıkları boşaltılana kadar yeni istekleri işleyemez.</span><span class="sxs-lookup"><span data-stu-id="7e71a-822">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="7e71a-823">Zaman uyumlu kodla, çok sayıda iş parçacığı, g/ç 'nin tamamlanmasını beklediği için aslında herhangi bir iş yapmadıklarında bağlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-823">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="7e71a-824">Zaman uyumsuz kod ile, bir işlem g/ç 'yi tamamlanmayı beklerken, sunucunun diğer istekleri işlemek için kullanması için iş parçacığı serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="7e71a-824">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="7e71a-825">Sonuç olarak, zaman uyumsuz kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucu, gecikme olmadan daha fazla trafiği işlemeye etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-825">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="7e71a-826">Zaman uyumsuz kod, çalışma zamanında az miktarda yük getirir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-826">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="7e71a-827">Düşük trafik durumlarında, performans artışı göz ardı edilebilir, ancak yüksek trafik durumları için olası performans iyileştirmesi oldukça önemlidir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-827">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="7e71a-828">Aşağıdaki kodda, [Async](/dotnet/csharp/language-reference/keywords/async) anahtar sözcüğü, `Task<T>` return değeri, `await` anahtar sözcüğü ve `ToListAsync` yöntemi kodu zaman uyumsuz olarak yürütür.</span><span class="sxs-lookup"><span data-stu-id="7e71a-828">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="7e71a-829">`async`Anahtar sözcüğü derleyiciye şunu söyler:</span><span class="sxs-lookup"><span data-stu-id="7e71a-829">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="7e71a-830">Yöntem gövdesinin parçaları için geri çağrılar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7e71a-830">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="7e71a-831">Döndürülen [görev](/dotnet/api/system.threading.tasks.task) nesnesini otomatik olarak oluşturun.</span><span class="sxs-lookup"><span data-stu-id="7e71a-831">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="7e71a-832">Daha fazla bilgi için bkz. [görev dönüş türü](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="7e71a-832">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="7e71a-833">Örtük dönüş türü, `Task` devam eden işi temsil eder.</span><span class="sxs-lookup"><span data-stu-id="7e71a-833">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="7e71a-834">`await`Anahtar sözcüğü, derleyicinin yöntemi iki parçaya böetmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-834">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="7e71a-835">İlk bölüm, zaman uyumsuz olarak başlatılan işlemle biter.</span><span class="sxs-lookup"><span data-stu-id="7e71a-835">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="7e71a-836">İkinci bölüm, işlem tamamlandığında çağrılan bir geri çağırma yöntemine konur.</span><span class="sxs-lookup"><span data-stu-id="7e71a-836">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="7e71a-837">`ToListAsync` , Uzantı yönteminin zaman uyumsuz sürümüdür `ToList` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-837">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="7e71a-838">EF Core kullanan zaman uyumsuz kodu yazarken dikkat edilmesi gereken bazı şeyler:</span><span class="sxs-lookup"><span data-stu-id="7e71a-838">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="7e71a-839">Yalnızca sorguları veya komutlarının VERITABANıNA gönderilmesine neden olan deyimler zaman uyumsuz olarak yürütülür.</span><span class="sxs-lookup"><span data-stu-id="7e71a-839">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="7e71a-840">,,, `ToListAsync` `SingleOrDefaultAsync` Ve içerir `FirstOrDefaultAsync` `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-840">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="7e71a-841">Yalnızca, gibi bir değiştiren deyimler içermez `IQueryable` `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="7e71a-841">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="7e71a-842">EF Core bağlamı iş parçacığı açısından güvenli değildir: paralel olarak birden çok işlem yapmayı denemeyin.</span><span class="sxs-lookup"><span data-stu-id="7e71a-842">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="7e71a-843">Zaman uyumsuz kodun performans avantajlarından yararlanmak için, VERITABANıNA sorgu gönderen EF Core yöntemlerini çağırıyorsa kitaplık paketlerinin (örneğin, sayfalama için) zaman uyumsuz olarak kullanılacağını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="7e71a-843">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="7e71a-844">.NET 'te zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. Async [and await ile](/dotnet/csharp/programming-guide/concepts/async/)zaman uyumsuz [genel bakış](/dotnet/standard/async) ve zaman uyumsuz programlama</span><span class="sxs-lookup"><span data-stu-id="7e71a-844">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="7e71a-845">Sonraki öğreticide, temel CRUD (oluşturma, okuma, güncelleştirme, silme) işlemleri incelenir.</span><span class="sxs-lookup"><span data-stu-id="7e71a-845">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="7e71a-846">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7e71a-846">Additional resources</span></span>

* [<span data-ttu-id="7e71a-847">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="7e71a-847">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="7e71a-848">Sonraki</span><span class="sxs-lookup"><span data-stu-id="7e71a-848">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
