---
title: ':::no-loc(Razor)::: ASP.NET Core Entity Framework Core olan sayfalar-öğretici 1/8'
author: rick-anderson
description: :::no-loc(Razor):::Entity Framework Core kullanarak bir sayfalar uygulamasının nasıl oluşturulacağını gösterir
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 9/26/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: data/ef-rp/intro
ms.openlocfilehash: 74f65b916c2d5b7de61ec29f4259a51584ee5989
ms.sourcegitcommit: 33f631a4427b9a422755601ac9119953db0b4a3e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365424"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="1bf58-103">:::no-loc(Razor)::: ASP.NET Core Entity Framework Core olan sayfalar-öğretici 1/8</span><span class="sxs-lookup"><span data-stu-id="1bf58-103">:::no-loc(Razor)::: Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="1bf58-104">, [Tom Dykstra](https://github.com/tdykstra) ve [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından</span><span class="sxs-lookup"><span data-stu-id="1bf58-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="1bf58-105">Bu, bir [ASP.NET Core :::no-loc(Razor)::: sayfaları](xref:razor-pages/index) UYGULAMASıNDA Entity Framework (EF) çekirdeğini nasıl kullanacağınızı gösteren bir öğretici serisinin ilkisidir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="1bf58-106">Öğreticiler, kurgusal bir Contoso Üniversitesi için bir Web sitesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="1bf58-107">Site, öğrenci giriş, kurs oluşturma ve eğitmen atamaları gibi işlevleri içerir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="1bf58-108">Öğretici ilk kod yaklaşımını kullanır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="1bf58-109">Bu öğreticiyi veritabanı ilk yaklaşımı kullanarak takip eden bilgiler için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/16897)bakın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="1bf58-110">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="1bf58-111">[Yönergeleri indirin](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="1bf58-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1bf58-112">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="1bf58-112">Prerequisites</span></span>

* <span data-ttu-id="1bf58-113">Sayfalarla yeni başladıysanız :::no-loc(Razor)::: , bunu başlatmadan önce [ :::no-loc(Razor)::: sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) öğreticisini izleyin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-113">If you're new to :::no-loc(Razor)::: Pages, go through the [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bf58-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bf58-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1bf58-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1bf58-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-5.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="1bf58-116">Veritabanı altyapıları</span><span class="sxs-lookup"><span data-stu-id="1bf58-116">Database engines</span></span>

<span data-ttu-id="1bf58-117">Visual Studio yönergeleri, yalnızca Windows üzerinde çalışan bir SQL Server Express sürümü olan [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)'yi kullanır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="1bf58-118">Visual Studio Code yönergeler, platformlar arası bir veritabanı altyapısı olan [SQLite](https://www.sqlite.org/)kullanır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="1bf58-119">SQLite kullanmayı seçerseniz, SQLite [Için DB tarayıcısı](https://sqlitebrowser.org/)gibi bir SQLite veritabanını yönetmek ve görüntülemek için üçüncü taraf bir araç indirip yükleyin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="1bf58-120">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="1bf58-120">Troubleshooting</span></span>

<span data-ttu-id="1bf58-121">Giderebileceğiniz bir sorunla karşılaşırsanız, kodunuzu [Tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="1bf58-122">Yardım almanın iyi bir yolu, [ASP.NET Core etiketi](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF Core etiketi](https://stackoverflow.com/questions/tagged/entity-framework-core)kullanılarak StackOverflow.com 'e bir soru göndererek.</span><span class="sxs-lookup"><span data-stu-id="1bf58-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="1bf58-123">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="1bf58-123">The sample app</span></span>

<span data-ttu-id="1bf58-124">Bu öğreticilerde oluşturulan uygulama, temel bir üniversite web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="1bf58-125">Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="1bf58-126">Öğreticide oluşturulan ekranlardan bazıları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-126">Here are a few of the screens created in the tutorial.</span></span>

![Öğrenciler Dizin sayfası](intro/_static/students-index30.png)

![Öğrenciler düzenleme sayfası](intro/_static/student-edit30.png)

<span data-ttu-id="1bf58-129">Bu sitenin kullanıcı arabirimi stili yerleşik proje şablonlarına dayalıdır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="1bf58-130">Öğreticinin odağı, Kullanıcı arabirimini nasıl özelleştireceğinizi değil, ASP.NET Core EF Core nasıl kullanacağınızı kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-130">The tutorial's focus is on how to use EF Core with ASP.NET Core, not how to customize the UI.</span></span>

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

## <a name="create-the-web-app-project"></a><span data-ttu-id="1bf58-131">Web uygulaması projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="1bf58-131">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bf58-132">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bf58-132">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1bf58-133">Visual Studio 'Yu başlatın ve **Yeni proje oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-133">Start Visual Studio and select **Create a new project**.</span></span>
* <span data-ttu-id="1bf58-134">**ASP.NET Core Web uygulaması** > **İleri ' yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-134">Select **ASP.NET Core Web Application** > **NEXT**.</span></span>
* <span data-ttu-id="1bf58-135">Projeyi *Contosouniversity* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-135">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="1bf58-136">Büyük harfler de dahil olmak üzere bu tam adı kullanmak önemlidir, bu nedenle kod kopyalanıp yapıştırılırken ad alanları eşleşir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-136">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="1bf58-137">**Oluştur** ’u seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-137">Select **Create**.</span></span>
* <span data-ttu-id="1bf58-138">Açılan menüden **.NET Core** ve **5,0 ASP.NET Core** seçin ve ardından **Web uygulaması** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-138">Select **.NET Core** and **ASP.NET Core 5.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1bf58-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1bf58-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1bf58-140">Bir terminalde, proje klasörünün oluşturulması gereken klasöre gidin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-140">In a terminal, navigate to the folder in which the project folder should be created.</span></span>
* <span data-ttu-id="1bf58-141">Bir :::no-loc(Razor)::: sayfa projesi ve yeni proje klasörü oluşturmak için aşağıdaki komutları çalıştırın `cd` :</span><span class="sxs-lookup"><span data-stu-id="1bf58-141">Run the following commands to create a :::no-loc(Razor)::: Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity  
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="1bf58-142">Site stilini ayarlayın</span><span class="sxs-lookup"><span data-stu-id="1bf58-142">Set up the site style</span></span>

<span data-ttu-id="1bf58-143">Aşağıdaki kodu kopyalayıp *Sayfalar/paylaşılan/_Layout. cshtml* dosyasına yapıştırın: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span><span class="sxs-lookup"><span data-stu-id="1bf58-143">Copy and paste the following code into the *Pages/Shared/_Layout.cshtml* file: [!code-cshtml[Main](intro/samples/cu50/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]</span></span>

<span data-ttu-id="1bf58-144">Düzen dosyası site üst bilgisini, alt bilgisini ve menüsünü ayarlar.</span><span class="sxs-lookup"><span data-stu-id="1bf58-144">The layout file sets the site header, footer, and menu.</span></span> <span data-ttu-id="1bf58-145">Yukarıdaki kod aşağıdaki değişiklikleri yapar:</span><span class="sxs-lookup"><span data-stu-id="1bf58-145">The preceding code makes the following changes:</span></span>

* <span data-ttu-id="1bf58-146">Her "ContosoUniversity" öğesinin "Contoso Üniversitesi" olarak her oluşumu.</span><span class="sxs-lookup"><span data-stu-id="1bf58-146">Each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="1bf58-147">Üç oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-147">There are three occurrences.</span></span>
* <span data-ttu-id="1bf58-148">**Giriş** ve **Gizlilik** menü girişleri silinir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-148">The **Home** and **Privacy** menu entries are deleted.</span></span>
* <span data-ttu-id="1bf58-149">, **Öğrenciler** , **Kurslar** , **eğitmenler** ve **Departmanlar** **için girişler** eklenir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-149">Entries are added for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="1bf58-150">*Pages/Index. cshtml* dosyasında, dosyanın içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="1bf58-150">In *Pages/Index.cshtml* , replace the contents of the file with the following code:</span></span>

[!code-cshtml[Main](intro/samples/cu50/Pages/Index.cshtml)]

<span data-ttu-id="1bf58-151">Yukarıdaki kod, ASP.NET Core ile ilgili metnin yerine bu uygulamayla ilgili metni koyar.</span><span class="sxs-lookup"><span data-stu-id="1bf58-151">The preceding code replaces the text about ASP.NET Core with text about this app.</span></span>

<span data-ttu-id="1bf58-152">Giriş sayfasının göründüğünü doğrulamak için uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-152">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="1bf58-153">Veri modeli</span><span class="sxs-lookup"><span data-stu-id="1bf58-153">The data model</span></span>

<span data-ttu-id="1bf58-154">Aşağıdaki bölümler bir veri modeli oluşturur:</span><span class="sxs-lookup"><span data-stu-id="1bf58-154">The following sections create a data model:</span></span>

![Kurs-kayıt-öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

<span data-ttu-id="1bf58-156">Bir öğrenci herhangi bir sayıda kursa kaydolabilir ve bir kurs, kayıtlı sayıda öğrenciye sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-156">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="1bf58-157">Öğrenci varlığı</span><span class="sxs-lookup"><span data-stu-id="1bf58-157">The Student entity</span></span>

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

* <span data-ttu-id="1bf58-159">Proje klasöründe bir *modeller* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1bf58-159">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="1bf58-160">Aşağıdaki kodla *modeller/öğrenci. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="1bf58-160">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="1bf58-161">`ID`Özelliği, bu sınıfa karşılık gelen veritabanı tablosunun birincil anahtar sütunu olur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-161">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="1bf58-162">Varsayılan olarak, EF Core `ID` birincil anahtar olarak adlandırılan bir özelliği Yorumlar `classnameID` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-162">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="1bf58-163">Bu nedenle birincil anahtar sınıfı için alternatif otomatik olarak tanınan ad `Student` olur `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-163">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="1bf58-164">Daha fazla bilgi için bkz. [EF Core-anahtarlar](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="1bf58-164">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="1bf58-165">`Enrollments`Özelliği bir [Gezinti özelliğidir](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="1bf58-165">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="1bf58-166">Gezinti özellikleri, bu varlıkla ilgili diğer varlıkları tutar.</span><span class="sxs-lookup"><span data-stu-id="1bf58-166">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="1bf58-167">Bu durumda, `Enrollments` bir `Student` varlığın özelliği `Enrollment` söz konusu öğrenciye ilişkin tüm varlıkları barındırır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-167">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="1bf58-168">Örneğin, veritabanındaki bir öğrenci satırında iki ilişkili kayıt satırı varsa, `Enrollments` gezinti özelliği bu Iki kayıt varlığını içerir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-168">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="1bf58-169">Veritabanında, bir kayıt satırı, Studentitıd sütunu öğrencinin ID değerini içeriyorsa bir öğrenci satırıyla ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-169">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="1bf58-170">Örneğin, bir öğrenci satırının ID = 1 olduğunu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="1bf58-170">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="1bf58-171">İlgili kayıt satırları Studentitıd = 1 olacaktır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-171">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="1bf58-172">Studentitıd, kayıt tablosundaki bir *yabancı anahtardır* .</span><span class="sxs-lookup"><span data-stu-id="1bf58-172">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="1bf58-173">`Enrollments`Özelliği, `ICollection<Enrollment>` birden çok ilgili kayıt varlığı olabileceğinden, olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-173">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="1bf58-174">Veya gibi diğer koleksiyon türlerini kullanabilirsiniz `List<Enrollment>` `HashSet<Enrollment>` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-174">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="1bf58-175">Kullanıldığında `ICollection<Enrollment>` , EF Core `HashSet<Enrollment>` Varsayılan olarak bir koleksiyon oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-175">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="1bf58-176">Kayıt varlığı</span><span class="sxs-lookup"><span data-stu-id="1bf58-176">The Enrollment entity</span></span>

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

<span data-ttu-id="1bf58-178">Aşağıdaki kodla *modeller/kayıt. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="1bf58-178">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="1bf58-179">`EnrollmentID`Bu özellik birincil anahtardır; bu varlık kendi başına değil, `classnameID` kendi modelini kullanır `ID` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-179">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="1bf58-180">Bir üretim veri modeli için bir model seçin ve bunu tutarlı bir şekilde kullanın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-180">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="1bf58-181">Bu öğretici her ikisinin de yalnızca bir iş olduğunu göstermek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-181">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="1bf58-182">`ID`Olmadan kullanmak `classname` , bazı veri modeli değişikliklerinin uygulanmasını kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-182">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="1bf58-183">`Grade`Özelliği bir `enum` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-183">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="1bf58-184">Tür bildiriminden sonraki soru işareti, `Grade` `Grade` özelliğin [null yapılabilir](/dotnet/csharp/programming-guide/nullable-types/)olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-184">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="1bf58-185">Null olan bir sınıf sıfır bir sınıf null değerinden farklıdır &mdash; veya henüz atanmamış olur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-185">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="1bf58-186">`StudentID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Student` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-186">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="1bf58-187">Bir varlık `Enrollment` bir `Student` varlıkla ilişkilendirilir, bu nedenle özellik tek bir varlık içerir `Student` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-187">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="1bf58-188">`CourseID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Course` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-188">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="1bf58-189">Bir `Enrollment` varlık bir `Course` varlıkla ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-189">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="1bf58-190">EF Core bir özelliği, adlandırılmış ise yabancı anahtar olarak yorumlar `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-190">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="1bf58-191">Örneğin, `StudentID` `Student` `Student` nesnenin birincil anahtarı olduğundan, gezinti özelliği için yabancı anahtardır `ID` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-191">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="1bf58-192">Yabancı anahtar özellikleri de adlandırılmış olabilir `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-192">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="1bf58-193">Örneğin, `CourseID` `Course` varlığın birincil anahtarı olduğundan `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-193">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="1bf58-194">Kurs varlığı</span><span class="sxs-lookup"><span data-stu-id="1bf58-194">The Course entity</span></span>

![Kurs varlık diyagramı](intro/_static/course-entity.png)

<span data-ttu-id="1bf58-196">Aşağıdaki kodla *modeller/kurs. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="1bf58-196">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="1bf58-197">`Enrollments`Özelliği bir gezinti özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-197">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="1bf58-198">Bir `Course` varlık herhangi bir sayıda varlıkla ilişkili olabilir `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-198">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="1bf58-199">`DatabaseGenerated`Özniteliği, uygulamanın veritabanını oluşturmak yerine birincil anahtarı belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="1bf58-199">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="1bf58-200">Derleyici hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-200">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="1bf58-201">Yapı iskelesi öğrenci sayfaları</span><span class="sxs-lookup"><span data-stu-id="1bf58-201">Scaffold Student pages</span></span>

<span data-ttu-id="1bf58-202">Bu bölümde, oluşturmak için ASP.NET Core scafkatlama aracını kullanırsınız:</span><span class="sxs-lookup"><span data-stu-id="1bf58-202">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="1bf58-203">EF Core `DbContext` sınıfı.</span><span class="sxs-lookup"><span data-stu-id="1bf58-203">An EF Core `DbContext` class.</span></span> <span data-ttu-id="1bf58-204">Bağlam, belirli bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-204">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="1bf58-205"><xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName>Sınıfından türetilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-205">It derives from the <xref:Microsoft.EntityFrameworkCore.DbContext?displayProperty=fullName> class.</span></span>
* <span data-ttu-id="1bf58-206">:::no-loc(Razor)::: varlık için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemlerini işleyen sayfalar `Student` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-206">:::no-loc(Razor)::: pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bf58-207">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bf58-207">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1bf58-208">*Sayfalar/öğrenciler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1bf58-208">Create a *Pages/Students* folder.</span></span>
* <span data-ttu-id="1bf58-209">**Çözüm Gezgini** , *Sayfalar/öğrenciler* klasörüne sağ tıklayın ve **Add** > **yeni yapı iskelesi Ekle öğesini** seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-209">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="1bf58-210">**Yeni yapı Iskelesi öğesi Ekle** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="1bf58-210">In the **Add New Scaffold Item** dialog:</span></span>
  * <span data-ttu-id="1bf58-211">Sol sekmede, **yüklü > ortak > :::no-loc(Razor)::: sayfaları** ' nı seçin</span><span class="sxs-lookup"><span data-stu-id="1bf58-211">In the left tab, select **Installed > Common > :::no-loc(Razor)::: Pages**</span></span>
  * <span data-ttu-id="1bf58-212">**:::no-loc(Razor)::: Entity Framework (CRUD) Ekle kullanarak sayfa** seçin > **ADD**.</span><span class="sxs-lookup"><span data-stu-id="1bf58-212">Select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="1bf58-213">**:::no-loc(Razor)::: Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="1bf58-213">In the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="1bf58-214">**Model sınıfı** açılır penceresinde **öğrenci (Contosouniversity. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-214">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="1bf58-215">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-215">In the **Data context class** row, select the **+** (plus) sign.</span></span>
    * <span data-ttu-id="1bf58-216">Veri bağlamı adını yerine End ile değiştirin `SchoolContext` `ContosoUniversityContext` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-216">Change the data context name to end in `SchoolContext` rather than `ContosoUniversityContext`.</span></span> <span data-ttu-id="1bf58-217">Güncelleştirilmiş bağlam adı: `ContosoUniversity.Data.SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="1bf58-217">The updated context name: `ContosoUniversity.Data.SchoolContext`</span></span>
   * <span data-ttu-id="1bf58-218">**Ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-218">Select **Add**.</span></span>

<span data-ttu-id="1bf58-219">Aşağıdaki paketler otomatik olarak yüklenir:</span><span class="sxs-lookup"><span data-stu-id="1bf58-219">The following packages are automatically installed:</span></span>

* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.EntityFrameworkCore.Tools`
* `Microsoft.VisualStudio.Web.CodeGeneration.Design`

# <a name="visual-studio-code"></a>[<span data-ttu-id="1bf58-220">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1bf58-220">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1bf58-221">Gerekli NuGet paketlerini yüklemek için aşağıdaki .NET Core CLI komutlarını çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1bf58-221">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>

  ```dotnetcli
  dotnet add package Microsoft.EntityFrameworkCore.SQLite -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.SqlServer -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Design -v 5.0.0-*
  dotnet add package Microsoft.EntityFrameworkCore.Tools -v 5.0.0-*
  dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v 5.0.0-*
  dotnet add package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -v 5.0.0-*  
  ```

   <span data-ttu-id="1bf58-222">Yapı iskelesi için Microsoft. VisualStudio. Web. CodeGeneration. Design paketi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-222">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="1bf58-223">Uygulama SQL Server kullanmayabilse de, scafkatlama aracı SQL Server paketine ihtiyaç duyuyor.</span><span class="sxs-lookup"><span data-stu-id="1bf58-223">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="1bf58-224">*Sayfalar/öğrenciler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1bf58-224">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="1bf58-225">[ASPNET-CodeGenerator scafkatlama aracı](xref:fundamentals/tools/dotnet-aspnet-codegenerator)'nı yüklemek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-225">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-aspnet-codegenerator
  dotnet tool install --global dotnet-aspnet-codegenerator --version 5.0.0-*  
  ```

* <span data-ttu-id="1bf58-226">Fkatlama öğrenci sayfalarını iskele almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-226">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="1bf58-227">**Windows üzerinde**</span><span class="sxs-lookup"><span data-stu-id="1bf58-227">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries -sqlite  
  ```

  <span data-ttu-id="1bf58-228">**MacOS veya Linux üzerinde**</span><span class="sxs-lookup"><span data-stu-id="1bf58-228">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries -sqlite  
  ```

---

<span data-ttu-id="1bf58-229">Önceki adım başarısız olursa, projeyi derleyin ve iskele adımını yeniden deneyin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-229">If the preceding step fails, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="1bf58-230">Yapı iskelesi işlemi:</span><span class="sxs-lookup"><span data-stu-id="1bf58-230">The scaffolding process:</span></span>

* <span data-ttu-id="1bf58-231">:::no-loc(Razor)::: *Sayfalar/öğrenciler* klasöründe sayfa oluşturur:</span><span class="sxs-lookup"><span data-stu-id="1bf58-231">Creates :::no-loc(Razor)::: pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="1bf58-232">*. Cshtml* ve *Create.cshtml.cs* oluşturma</span><span class="sxs-lookup"><span data-stu-id="1bf58-232">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="1bf58-233">*Delete. cshtml* ve *delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="1bf58-233">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="1bf58-234">*Details. cshtml* ve *details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="1bf58-234">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="1bf58-235">*. Cshtml* ve *Edit.cshtml.cs* Düzenle</span><span class="sxs-lookup"><span data-stu-id="1bf58-235">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="1bf58-236">*Index. cshtml* ve *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="1bf58-236">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="1bf58-237">*Data/SchoolContext. cs* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-237">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="1bf58-238">*Startup.cs* içinde bağımlılık eklenmesine bağlam ekler.</span><span class="sxs-lookup"><span data-stu-id="1bf58-238">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="1bf58-239">Öğesine bir veritabanı bağlantı dizesi ekler *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="1bf58-239">Adds a database connection string to *:::no-loc(appsettings.json):::*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="1bf58-240">Veritabanı bağlantı dizesi</span><span class="sxs-lookup"><span data-stu-id="1bf58-240">Database connection string</span></span>

<span data-ttu-id="1bf58-241">Scafkatlama aracı dosyada bir bağlantı dizesi oluşturur *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="1bf58-241">The scaffolding tool generates a connection string in the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bf58-242">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bf58-242">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1bf58-243">Bağlantı dizesi [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)belirtir:</span><span class="sxs-lookup"><span data-stu-id="1bf58-243">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb):</span></span>

[!code-json[Main](intro/samples/cu50/:::no-loc(appsettings.json):::?highlight=11)]

<span data-ttu-id="1bf58-244">LocalDB, SQL Server Express veritabanı altyapısının hafif bir sürümüdür ve üretim kullanımı için değil uygulama geliştirmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-244">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="1bf58-245">Varsayılan olarak, LocalDB dizinde *. mdf* dosyaları oluşturur `C:/Users/<user>` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-245">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1bf58-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1bf58-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1bf58-247">*. Db* için SQLite bağlantı dizesini kısaltın:</span><span class="sxs-lookup"><span data-stu-id="1bf58-247">Shorten the SQLite connection string to *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu50/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="1bf58-248">Veritabanı bağlam sınıfını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="1bf58-248">Update the database context class</span></span>

<span data-ttu-id="1bf58-249">Belirli bir veri modeli için EF Core işlevselliğini koordine eden ana sınıf veritabanı bağlamı sınıfıdır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-249">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="1bf58-250">Bağlam [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-250">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="1bf58-251">Bağlam, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-251">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="1bf58-252">Bu projede, sınıfı olarak adlandırılır `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-252">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="1bf58-253">*Data/SchoolContext. cs* öğesini şu kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="1bf58-253">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="1bf58-254">Yukarıdaki kod tekil ile çoğul arasında değişir `DbSet<Student> Student` `DbSet<Student> Students` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-254">The preceding code changes from the singular `DbSet<Student> Student` to the  plural `DbSet<Student> Students`.</span></span> <span data-ttu-id="1bf58-255">:::no-loc(Razor):::Sayfa kodunun yeni adla eşleşmesini sağlamak için `DBSet` , ' den küresel bir değişiklik yapın:`_context.Student.`</span><span class="sxs-lookup"><span data-stu-id="1bf58-255">To make the :::no-loc(Razor)::: Pages code match the new `DBSet` name, make a global change from: `_context.Student.`</span></span>
<span data-ttu-id="1bf58-256">Hedef: `_context.Students.`</span><span class="sxs-lookup"><span data-stu-id="1bf58-256">to: `_context.Students.`</span></span>

<span data-ttu-id="1bf58-257">8 oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-257">There are 8 occurrences.</span></span>

<span data-ttu-id="1bf58-258">Bir varlık kümesi birden çok varlık içerdiğinden birçok geliştirici, `DBSet` özellik adlarının çoğul olması gerektiğini tercih eder.</span><span class="sxs-lookup"><span data-stu-id="1bf58-258">Because an entity set contains multiple entities, many developers prefer the `DBSet` property names should be plural.</span></span>

<span data-ttu-id="1bf58-259">Vurgulanan kod:</span><span class="sxs-lookup"><span data-stu-id="1bf58-259">The highlighted code:</span></span>

* <span data-ttu-id="1bf58-260">Her bir varlık kümesi için bir [Dbset \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-260">Creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="1bf58-261">EF Core terminoloji:</span><span class="sxs-lookup"><span data-stu-id="1bf58-261">In EF Core terminology:</span></span>
  * <span data-ttu-id="1bf58-262">Bir varlık kümesi, genellikle bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-262">An entity set typically corresponds to a database table.</span></span>
  * <span data-ttu-id="1bf58-263">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-263">An entity corresponds to a row in the table.</span></span>
* <span data-ttu-id="1bf58-264">Çağırır <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .</span><span class="sxs-lookup"><span data-stu-id="1bf58-264">Calls <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A>.</span></span> <span data-ttu-id="1bf58-265">`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="1bf58-265">`OnModelCreating`:</span></span>
  * <span data-ttu-id="1bf58-266">Başlatıldığı zaman çağrılır `SchoolContext` , ancak model kilitlenmeden önce ve bağlamı başlatmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-266">Is called when `SchoolContext` has been initialized, but before the model has been locked down and used to initialize the context.</span></span>
  * <span data-ttu-id="1bf58-267">Daha sonraki öğreticide, `Student` varlığın diğer varlıklara başvuruları olacağı için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-267">Is required because later in the tutorial The `Student` entity will have references to the other entities.</span></span>
  <!-- Review, OnModelCreating needs review -->

<span data-ttu-id="1bf58-268">Derleyici hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-268">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="1bf58-269">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="1bf58-269">Startup.cs</span></span>

<span data-ttu-id="1bf58-270">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-270">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1bf58-271">Gibi hizmetler, `SchoolContext` uygulamanın başlatılması sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-271">Services such as the `SchoolContext` are registered with dependency injection during app startup.</span></span> <span data-ttu-id="1bf58-272">Sayfalar gibi bu hizmetleri gerektiren bileşenler :::no-loc(Razor)::: , bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-272">Components that require these services, such as :::no-loc(Razor)::: Pages, are provided these services via constructor parameters.</span></span> <span data-ttu-id="1bf58-273">Bir veritabanı bağlamı örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-273">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="1bf58-274">Scafkatlama Aracı, bağlam sınıfını bağımlılık ekleme kapsayıcısına otomatik olarak kaydetti.</span><span class="sxs-lookup"><span data-stu-id="1bf58-274">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bf58-275">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bf58-275">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1bf58-276">Aşağıdaki Vurgulanan satırlar scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="1bf58-276">The following highlighted lines were added by the scaffolder:</span></span>

[!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1bf58-277">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1bf58-277">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1bf58-278">Desteği çağrıları tarafından eklenen kodu doğrulayın `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-278">Verify the code added by the scaffolder calls `UseSqlite`.</span></span>

[!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="1bf58-279">Üretim veritabanını kullanma hakkında bilgi için bkz. [geliştirme Için SQLite kullanma, üretime yönelik SQL Server](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) .</span><span class="sxs-lookup"><span data-stu-id="1bf58-279">See [Use SQLite for development, SQL Server for production](xref:tutorials/razor-pages/model#use-sqlite-for-development-sql-server-for-production) for information on using a production database.</span></span>

---

<span data-ttu-id="1bf58-280">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-280">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="1bf58-281">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="1bf58-281">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

### <a name="add-the-database-exception-filter"></a><span data-ttu-id="1bf58-282">Veritabanı özel durum filtresini ekleme</span><span class="sxs-lookup"><span data-stu-id="1bf58-282">Add the database exception filter</span></span>

<span data-ttu-id="1bf58-283"><xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> `ConfigureServices` Aşağıdaki kodda gösterildiği gibi öğesine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1bf58-283">Add <xref:Microsoft.Extensions.DependencyInjection.DatabaseDeveloperPageExceptionFilterServiceExtensions.AddDatabaseDeveloperPageExceptionFilter%2A> to `ConfigureServices` as shown in the following code:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bf58-284">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bf58-284">Visual Studio</span></span>](#tab/visual-studio)

[!code-csharp[Main](intro/samples/cu50/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

<span data-ttu-id="1bf58-285">[Microsoft. aspnetcore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-285">Add the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore) NuGet package.</span></span>

<span data-ttu-id="1bf58-286">Şu şekilde, NuGet paketini eklemek için aşağıdakileri girin:</span><span class="sxs-lookup"><span data-stu-id="1bf58-286">In the PMC, enter the following to add the NuGet package:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore -Version 5.0.0-rc.2.20475.17
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="1bf58-287">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1bf58-287">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!code-csharp[Main](intro/samples/cu50/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=8)]

---

<span data-ttu-id="1bf58-288">`Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore`NuGet paketi, Entity Framework Core hata sayfaları için ASP.NET Core ara yazılım sağlar.</span><span class="sxs-lookup"><span data-stu-id="1bf58-288">The `Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore` NuGet package provides ASP.NET Core middleware for Entity Framework Core error pages.</span></span> <span data-ttu-id="1bf58-289">Bu ara yazılım Entity Framework Core geçişlerle hataları algılamaya ve tanılamanıza yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-289">This middleware helps to detect and diagnose errors with Entity Framework Core migrations.</span></span>

<span data-ttu-id="1bf58-290">, `AddDatabaseDeveloperPageExceptionFilter` [Geliştirme ortamında](xref:fundamentals/environments)yararlı hata bilgileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="1bf58-290">The `AddDatabaseDeveloperPageExceptionFilter` provides helpful error information in the [development environment](xref:fundamentals/environments).</span></span>

## <a name="create-the-database"></a><span data-ttu-id="1bf58-291">Veritabanını oluşturma</span><span class="sxs-lookup"><span data-stu-id="1bf58-291">Create the database</span></span>

<span data-ttu-id="1bf58-292">Mevcut değilse veritabanını oluşturmak için *program.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="1bf58-292">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="1bf58-293">Bağlam için bir veritabanı varsa, [Ensuyeniden](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) oluşturma yöntemi hiçbir eylemde bulunmaz.</span><span class="sxs-lookup"><span data-stu-id="1bf58-293">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="1bf58-294">Veritabanı yoksa, veritabanını ve şemayı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-294">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="1bf58-295">`EnsureCreated` veri modeli değişikliklerini işlemek için aşağıdaki iş akışını sunar:</span><span class="sxs-lookup"><span data-stu-id="1bf58-295">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="1bf58-296">Veritabanını silin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-296">Delete the database.</span></span> <span data-ttu-id="1bf58-297">Mevcut veriler kaybolur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-297">Any existing data is lost.</span></span>
* <span data-ttu-id="1bf58-298">Veri modelini değiştirin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-298">Change the data model.</span></span> <span data-ttu-id="1bf58-299">Örneğin, bir alan ekleyin `EmailAddress` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-299">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="1bf58-300">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-300">Run the app.</span></span>
* <span data-ttu-id="1bf58-301">`EnsureCreated` yeni şemaya sahip bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-301">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="1bf58-302">Bu iş akışı, verileri korumanıza gerek olmadığı sürece, şema hızlı bir şekilde gelişen zaman geliştirme aşamasında iyi bir şekilde gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-302">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="1bf58-303">Veritabanına girilen verilerin korunması gerektiğinde bu durum farklıdır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-303">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="1bf58-304">Bu durumda, geçişleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-304">When that is the case, use migrations.</span></span>

<span data-ttu-id="1bf58-305">Öğretici serisinde daha sonra tarafından oluşturulan veritabanını siler `EnsureCreated` ve bunun yerine geçişleri kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="1bf58-305">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="1bf58-306">Tarafından oluşturulan bir veritabanı, `EnsureCreated` geçişler kullanılarak güncelleştirilemiyor.</span><span class="sxs-lookup"><span data-stu-id="1bf58-306">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="1bf58-307">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="1bf58-307">Test the app</span></span>

* <span data-ttu-id="1bf58-308">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-308">Run the app.</span></span>
* <span data-ttu-id="1bf58-309">**Öğrenciler** bağlantısını seçin ve ardından **Yeni oluştur**.</span><span class="sxs-lookup"><span data-stu-id="1bf58-309">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="1bf58-310">Düzenle, Ayrıntılar ve Sil bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-310">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="1bf58-311">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="1bf58-311">Seed the database</span></span>

<span data-ttu-id="1bf58-312">`EnsureCreated`Yöntemi boş bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-312">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="1bf58-313">Bu bölüm, veritabanını test verileriyle dolduran kodu ekler.</span><span class="sxs-lookup"><span data-stu-id="1bf58-313">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="1bf58-314">Aşağıdaki kodla *veri/Dbınizer. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="1bf58-314">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="1bf58-315">Kod, veritabanında herhangi bir öğrenci olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="1bf58-315">The code checks if there are any students in the database.</span></span> <span data-ttu-id="1bf58-316">Öğrenci yoksa, veritabanına test verileri ekler.</span><span class="sxs-lookup"><span data-stu-id="1bf58-316">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="1bf58-317">Performansı iyileştirmek için Koleksiyonlar yerine diziler halinde test verileri oluşturur `List<T>` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-317">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="1bf58-318">*Program.cs* içinde, `EnsureCreated` çağrıyı bir çağrı ile değiştirin `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="1bf58-318">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="1bf58-319">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bf58-319">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1bf58-320">Çalışıyorsa uygulamayı durdurun ve **Paket Yöneticisi konsolunda** (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1bf58-320">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database -Confirm
```

<span data-ttu-id="1bf58-321">`Y`Veritabanını silmek için ile yanıtlayın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-321">Respond with `Y` to delete the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1bf58-322">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1bf58-322">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1bf58-323">Çalışıyorsa uygulamayı durdurun ve *cu. db* dosyasını silin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-323">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="1bf58-324">Uygulamayı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-324">Restart the app.</span></span>
* <span data-ttu-id="1bf58-325">Sağlanan verileri görmek için öğrenciler sayfasını seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-325">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="1bf58-326">Veritabanını görüntüleme</span><span class="sxs-lookup"><span data-stu-id="1bf58-326">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bf58-327">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bf58-327">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1bf58-328">Visual Studio 'daki **Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-328">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="1bf58-329">SSOX 'te, **(LocalDB) \MSSQLLocalDB > veritabanları > SchoolContext-{GUID}** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-329">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="1bf58-330">Veritabanı adı, daha önce belirttiğiniz bağlam adından ve bir tire ve bir GUID ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-330">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="1bf58-331">**Tables** düğümünü genişletin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-331">Expand the **Tables** node.</span></span>
* <span data-ttu-id="1bf58-332">Oluşturulan sütunları ve tabloya yerleştirilen satırları görmek için **öğrenci** tablosuna sağ tıklayın ve **verileri görüntüle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-332">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="1bf58-333">Modelin tablo şemasına nasıl eşlendiğini görmek için **öğrenci** tablosuna sağ tıklayın ve **kodu görüntüle** ' ye tıklayın `Student` `Student` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-333">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1bf58-334">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1bf58-334">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1bf58-335">Veritabanı şemasını ve sağlanan verileri görüntülemek için SQLite aracınızı kullanın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-335">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="1bf58-336">Veritabanı dosyası *cu. db* olarak adlandırılır ve proje klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-336">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="1bf58-337">Zaman uyumsuz kod</span><span class="sxs-lookup"><span data-stu-id="1bf58-337">Asynchronous code</span></span>

<span data-ttu-id="1bf58-338">Zaman uyumsuz programlama, ASP.NET Core ve EF Core için varsayılan moddur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-338">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="1bf58-339">Web sunucusunda sınırlı sayıda iş parçacığı bulunur ve yüksek yük durumlarında tüm kullanılabilir iş parçacıkları kullanımda olabilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-339">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="1bf58-340">Bu durumda, sunucu, iş parçacıkları boşaltılana kadar yeni istekleri işleyemez.</span><span class="sxs-lookup"><span data-stu-id="1bf58-340">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="1bf58-341">Zaman uyumlu kod ile, çok sayıda iş parçacığı g/ç 'nin tamamlanmasını beklediği için iş çalışmadıklarında bağlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-341">With synchronous code, many threads may be tied up while they aren't doing work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="1bf58-342">Zaman uyumsuz kod ile, bir işlem g/ç 'yi tamamlanmayı beklerken, sunucunun diğer istekleri işlemek için kullanması için iş parçacığı serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-342">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="1bf58-343">Sonuç olarak, zaman uyumsuz kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucu gecikmeksizin daha fazla trafiği işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-343">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="1bf58-344">Zaman uyumsuz kod, çalışma zamanında az miktarda yük getirir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-344">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="1bf58-345">Düşük trafik durumlarında, performans artışı göz ardı edilebilir, ancak yüksek trafik durumları için olası performans iyileştirmesi oldukça önemlidir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-345">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="1bf58-346">Aşağıdaki kodda, [Async](/dotnet/csharp/language-reference/keywords/async) anahtar sözcüğü, `Task<T>` return değeri, `await` anahtar sözcüğü ve `ToListAsync` yöntemi kodu zaman uyumsuz olarak yürütür.</span><span class="sxs-lookup"><span data-stu-id="1bf58-346">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="1bf58-347">`async`Anahtar sözcüğü derleyiciye şunu söyler:</span><span class="sxs-lookup"><span data-stu-id="1bf58-347">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="1bf58-348">Yöntem gövdesinin parçaları için geri çağrılar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1bf58-348">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="1bf58-349">Döndürülen [görev](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) nesnesini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1bf58-349">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="1bf58-350">`Task<T>`Dönüş türü, devam eden işi temsil eder.</span><span class="sxs-lookup"><span data-stu-id="1bf58-350">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="1bf58-351">`await`Anahtar sözcüğü, derleyicinin yöntemi iki parçaya böetmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-351">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="1bf58-352">İlk bölüm, zaman uyumsuz olarak başlatılan işlemle biter.</span><span class="sxs-lookup"><span data-stu-id="1bf58-352">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="1bf58-353">İkinci bölüm, işlem tamamlandığında çağrılan bir geri çağırma yöntemine konur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-353">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="1bf58-354">`ToListAsync` , Uzantı yönteminin zaman uyumsuz sürümüdür `ToList` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-354">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="1bf58-355">EF Core kullanan zaman uyumsuz kodu yazarken dikkat edilmesi gereken bazı şeyler:</span><span class="sxs-lookup"><span data-stu-id="1bf58-355">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="1bf58-356">Yalnızca sorguları veya komutlarının veritabanına gönderilmesine neden olan deyimler zaman uyumsuz olarak yürütülür.</span><span class="sxs-lookup"><span data-stu-id="1bf58-356">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="1bf58-357">,, `ToListAsync` , `SingleOrDefaultAsync` `FirstOrDefaultAsync` Ve içerir `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-357">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="1bf58-358">Yalnızca, gibi bir değiştiren deyimler içermez `IQueryable` `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-358">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="1bf58-359">EF Core bağlamı iş parçacığı açısından güvenli değildir: paralel olarak birden çok işlem yapmayı denemeyin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-359">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="1bf58-360">Zaman uyumsuz kodun performans avantajlarından yararlanmak için, veritabanına sorgu gönderen EF Core yöntemleri çağırıyorsa kitaplık paketlerinin (örneğin, sayfalama için) zaman uyumsuz olarak kullanılacağını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-360">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="1bf58-361">.NET 'te zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. Async [and await ile](/dotnet/csharp/programming-guide/concepts/async/)zaman uyumsuz [genel bakış](/dotnet/standard/async) ve zaman uyumsuz programlama</span><span class="sxs-lookup"><span data-stu-id="1bf58-361">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<!-- Review: See https://github.com/dotnet/AspNetCore.Docs/issues/14528 -->
## <a name="performance-considerations"></a><span data-ttu-id="1bf58-362">Performansla ilgili önemli noktalar</span><span class="sxs-lookup"><span data-stu-id="1bf58-362">Performance considerations</span></span>

<span data-ttu-id="1bf58-363">Genel olarak, bir Web sayfası rastgele sayıda satır yüklememelidir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-363">In general, a web page shouldn't be loading an arbitrary number of rows.</span></span> <span data-ttu-id="1bf58-364">Bir sorgu, sayfalama veya sınırlandırma yaklaşımını kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-364">A query should use paging or a limiting approach.</span></span> <span data-ttu-id="1bf58-365">Örneğin, önceki sorgu `Take` döndürülen satırları sınırlamak için kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="1bf58-365">For example, the preceding query could use `Take` to limit the rows returned:</span></span>

[!code-csharp[Main](intro/samples/cu50snapshots/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="1bf58-366">Bir görünümde büyük bir tablonun numaralandırılması, bir veritabanı özel durumu numaralandırma aracılığıyla bir bölüm oluşursa kısmen oluşturulmuş bir HTTP 200 yanıtı döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-366">Enumerating a large table in a view could return a partially constructed HTTP 200 response if a database exception occurs part way through the enumeration.</span></span>

<span data-ttu-id="1bf58-367"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> Varsayılan olarak 1024 ' dir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-367"><xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxModelBindingCollectionSize> defaults to 1024.</span></span> <span data-ttu-id="1bf58-368">Aşağıdaki kod kümeleri `MaxModelBindingCollectionSize` :</span><span class="sxs-lookup"><span data-stu-id="1bf58-368">The following code sets `MaxModelBindingCollectionSize`:</span></span>

[!code-csharp[Main](intro/samples/cu50/StartupMaxMBsize.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="1bf58-369">Sayfalama daha sonra öğreticide ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-369">Paging is covered later in the tutorial.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1bf58-370">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="1bf58-370">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1bf58-371">Sonraki öğretici</span><span class="sxs-lookup"><span data-stu-id="1bf58-371">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="1bf58-372">Bu, bir [ASP.NET Core :::no-loc(Razor)::: sayfaları](xref:razor-pages/index) UYGULAMASıNDA Entity Framework (EF) çekirdeğini nasıl kullanacağınızı gösteren bir öğretici serisinin ilkisidir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-372">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core :::no-loc(Razor)::: Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="1bf58-373">Öğreticiler, kurgusal bir Contoso Üniversitesi için bir Web sitesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-373">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="1bf58-374">Site, öğrenci giriş, kurs oluşturma ve eğitmen atamaları gibi işlevleri içerir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-374">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="1bf58-375">Öğretici ilk kod yaklaşımını kullanır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-375">The tutorial uses the code first approach.</span></span> <span data-ttu-id="1bf58-376">Bu öğreticiyi veritabanı ilk yaklaşımı kullanarak takip eden bilgiler için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/16897)bakın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-376">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="1bf58-377">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-377">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="1bf58-378">[Yönergeleri indirin](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="1bf58-378">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1bf58-379">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="1bf58-379">Prerequisites</span></span>

* <span data-ttu-id="1bf58-380">Sayfalarla yeni başladıysanız :::no-loc(Razor)::: , bunu başlatmadan önce [ :::no-loc(Razor)::: sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) öğreticisini izleyin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-380">If you're new to :::no-loc(Razor)::: Pages, go through the [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bf58-381">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bf58-381">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1bf58-382">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1bf58-382">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="1bf58-383">Veritabanı altyapıları</span><span class="sxs-lookup"><span data-stu-id="1bf58-383">Database engines</span></span>

<span data-ttu-id="1bf58-384">Visual Studio yönergeleri, yalnızca Windows üzerinde çalışan bir SQL Server Express sürümü olan [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)'yi kullanır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-384">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="1bf58-385">Visual Studio Code yönergeler, platformlar arası bir veritabanı altyapısı olan [SQLite](https://www.sqlite.org/)kullanır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-385">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="1bf58-386">SQLite kullanmayı seçerseniz, SQLite [Için DB tarayıcısı](https://sqlitebrowser.org/)gibi bir SQLite veritabanını yönetmek ve görüntülemek için üçüncü taraf bir araç indirip yükleyin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-386">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="1bf58-387">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="1bf58-387">Troubleshooting</span></span>

<span data-ttu-id="1bf58-388">Giderebileceğiniz bir sorunla karşılaşırsanız, kodunuzu [Tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-388">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="1bf58-389">Yardım almanın iyi bir yolu, [ASP.NET Core etiketi](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF Core etiketi](https://stackoverflow.com/questions/tagged/entity-framework-core)kullanılarak StackOverflow.com 'e bir soru göndererek.</span><span class="sxs-lookup"><span data-stu-id="1bf58-389">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="1bf58-390">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="1bf58-390">The sample app</span></span>

<span data-ttu-id="1bf58-391">Bu öğreticilerde oluşturulan uygulama, temel bir üniversite web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-391">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="1bf58-392">Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-392">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="1bf58-393">Öğreticide oluşturulan ekranlardan bazıları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-393">Here are a few of the screens created in the tutorial.</span></span>

![Öğrenciler Dizin sayfası](intro/_static/students-index30.png)

![Öğrenciler düzenleme sayfası](intro/_static/student-edit30.png)

<span data-ttu-id="1bf58-396">Bu sitenin kullanıcı arabirimi stili yerleşik proje şablonlarına dayalıdır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-396">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="1bf58-397">Öğreticinin odağı, Kullanıcı arabirimini nasıl özelleştireceğinizi değil EF Core kullanma konusunda yer alır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-397">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="1bf58-398">Tamamlanan projenin kaynak kodunu almak için sayfanın üst kısmındaki bağlantıyı izleyin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-398">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="1bf58-399">*Cu30* klasörü, öğreticinin ASP.NET Core 3,0 sürümü için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-399">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="1bf58-400">1-7 öğreticileri için kodun durumunu yansıtan dosyalar *cu30snapshots* klasöründe bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-400">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bf58-401">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bf58-401">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1bf58-402">Tamamlanmış projeyi indirdikten sonra uygulamayı çalıştırmak için:</span><span class="sxs-lookup"><span data-stu-id="1bf58-402">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="1bf58-403">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-403">Build the project.</span></span>
* <span data-ttu-id="1bf58-404">Paket Yöneticisi konsolu 'nda (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1bf58-404">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="1bf58-405">Veritabanını temel alarak projeyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-405">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1bf58-406">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1bf58-406">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1bf58-407">Tamamlanmış projeyi indirdikten sonra uygulamayı çalıştırmak için:</span><span class="sxs-lookup"><span data-stu-id="1bf58-407">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="1bf58-408">*Contosouniversity. csproj* öğesini silin ve *Contosoüniversıtysqlite. csproj* öğesini *contosouniversity. csproj* olarak yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-408">Delete *ContosoUniversity.csproj* , and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="1bf58-409">*Program.cs* ' de, açıklama dışarı bu `#define Startup` şekilde `StartupSQLite` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-409">In *Program.cs* , comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="1bf58-410">*appSettings.js* silin ve üzerinde *appSettings.js* *appSettingsSQLite.js* yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-410">Delete *appSettings.json* , and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="1bf58-411">*Geçişler* klasörünü silin ve *migrationssql* öğesini *geçişlerle* yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-411">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="1bf58-412">İçin genel arama yapın `#if SQLiteVersion` ve `#if SQLiteVersion` ilgili `#endif` ifadeyi kaldırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-412">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="1bf58-413">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-413">Build the project.</span></span>
* <span data-ttu-id="1bf58-414">Proje klasöründeki bir komut isteminde aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1bf58-414">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool uninstall --global dotnet-ef
  dotnet tool install --global dotnet-ef --version 5.0.0-*
  dotnet ef database update
  ```

* <span data-ttu-id="1bf58-415">SQLite aracında şu SQL ifadesini çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1bf58-415">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```
  
* <span data-ttu-id="1bf58-416">Veritabanını temel alarak projeyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-416">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="1bf58-417">Web uygulaması projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="1bf58-417">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bf58-418">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bf58-418">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1bf58-419">Visual Studio **Dosya** menüsünden **Yeni** > **Proje** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-419">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="1bf58-420">**ASP.NET Core Web uygulaması** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-420">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="1bf58-421">Projeyi *Contosouniversity* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-421">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="1bf58-422">Büyük harfler de dahil olmak üzere bu tam adı kullanmak önemlidir, bu nedenle kod kopyalanıp yapıştırılırken ad alanları eşleşir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-422">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="1bf58-423">Açılan menüden **.NET Core** ve **3,0 ASP.NET Core** seçin ve ardından **Web uygulaması** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-423">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1bf58-424">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1bf58-424">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1bf58-425">Bir terminalde, proje klasörünün oluşturulması gereken klasöre gidin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-425">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="1bf58-426">Bir :::no-loc(Razor)::: sayfa projesi ve yeni proje klasörü oluşturmak için aşağıdaki komutları çalıştırın `cd` :</span><span class="sxs-lookup"><span data-stu-id="1bf58-426">Run the following commands to create a :::no-loc(Razor)::: Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="1bf58-427">Site stilini ayarlayın</span><span class="sxs-lookup"><span data-stu-id="1bf58-427">Set up the site style</span></span>

<span data-ttu-id="1bf58-428">*Sayfa/paylaşılan/_Layout. cshtml* 'yi güncelleştirerek site üst bilgisini, alt bilgisini ve menüsünü ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="1bf58-428">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml* :</span></span>

* <span data-ttu-id="1bf58-429">"ContosoUniversity" öğesinin her oluşumunu "Contoso Üniversitesi" olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-429">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="1bf58-430">Üç oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-430">There are three occurrences.</span></span>

* <span data-ttu-id="1bf58-431">**Giriş** ve **Gizlilik** menü girişlerini silin ve **hakkında** , **öğrenciler** , **Kurslar** , **eğitmenler** ve **Departmanlar** için girişler ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-431">Delete the **Home** and **Privacy** menu entries, and add entries for **About** , **Students** , **Courses** , **Instructors** , and **Departments**.</span></span>

<span data-ttu-id="1bf58-432">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-432">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="1bf58-433">*Pages/Index. cshtml* dosyasında, ASP.NET Core hakkındaki metni bu uygulamayla ilgili metinle değiştirmek için dosyanın içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="1bf58-433">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="1bf58-434">Giriş sayfasının göründüğünü doğrulamak için uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-434">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="1bf58-435">Veri modeli</span><span class="sxs-lookup"><span data-stu-id="1bf58-435">The data model</span></span>

<span data-ttu-id="1bf58-436">Aşağıdaki bölümler bir veri modeli oluşturur:</span><span class="sxs-lookup"><span data-stu-id="1bf58-436">The following sections create a data model:</span></span>

![Kurs-kayıt-öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

<span data-ttu-id="1bf58-438">Bir öğrenci herhangi bir sayıda kursa kaydolabilir ve bir kurs, kayıtlı sayıda öğrenciye sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-438">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="1bf58-439">Öğrenci varlığı</span><span class="sxs-lookup"><span data-stu-id="1bf58-439">The Student entity</span></span>

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

* <span data-ttu-id="1bf58-441">Proje klasöründe bir *modeller* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1bf58-441">Create a *Models* folder in the project folder.</span></span>
* <span data-ttu-id="1bf58-442">Aşağıdaki kodla *modeller/öğrenci. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="1bf58-442">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="1bf58-443">`ID`Özelliği, bu sınıfa karşılık gelen veritabanı tablosunun birincil anahtar sütunu olur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-443">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="1bf58-444">Varsayılan olarak, EF Core `ID` birincil anahtar olarak adlandırılan bir özelliği Yorumlar `classnameID` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-444">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="1bf58-445">Bu nedenle birincil anahtar sınıfı için alternatif otomatik olarak tanınan ad `Student` olur `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-445">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="1bf58-446">Daha fazla bilgi için bkz. [EF Core-anahtarlar](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="1bf58-446">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="1bf58-447">`Enrollments`Özelliği bir [Gezinti özelliğidir](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="1bf58-447">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="1bf58-448">Gezinti özellikleri, bu varlıkla ilgili diğer varlıkları tutar.</span><span class="sxs-lookup"><span data-stu-id="1bf58-448">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="1bf58-449">Bu durumda, `Enrollments` bir `Student` varlığın özelliği `Enrollment` söz konusu öğrenciye ilişkin tüm varlıkları barındırır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-449">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="1bf58-450">Örneğin, veritabanındaki bir öğrenci satırında iki ilişkili kayıt satırı varsa, `Enrollments` gezinti özelliği bu Iki kayıt varlığını içerir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-450">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="1bf58-451">Veritabanında, bir kayıt satırı, Studentitıd sütunu öğrencinin ID değerini içeriyorsa bir öğrenci satırıyla ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-451">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="1bf58-452">Örneğin, bir öğrenci satırının ID = 1 olduğunu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="1bf58-452">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="1bf58-453">İlgili kayıt satırları Studentitıd = 1 olacaktır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-453">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="1bf58-454">Studentitıd, kayıt tablosundaki bir *yabancı anahtardır* .</span><span class="sxs-lookup"><span data-stu-id="1bf58-454">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="1bf58-455">`Enrollments`Özelliği, `ICollection<Enrollment>` birden çok ilgili kayıt varlığı olabileceğinden, olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-455">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="1bf58-456">Veya gibi diğer koleksiyon türlerini kullanabilirsiniz `List<Enrollment>` `HashSet<Enrollment>` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-456">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="1bf58-457">Kullanıldığında `ICollection<Enrollment>` , EF Core `HashSet<Enrollment>` Varsayılan olarak bir koleksiyon oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-457">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="1bf58-458">Kayıt varlığı</span><span class="sxs-lookup"><span data-stu-id="1bf58-458">The Enrollment entity</span></span>

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

<span data-ttu-id="1bf58-460">Aşağıdaki kodla *modeller/kayıt. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="1bf58-460">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="1bf58-461">`EnrollmentID`Bu özellik birincil anahtardır; bu varlık kendi başına değil, `classnameID` kendi modelini kullanır `ID` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-461">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="1bf58-462">Bir üretim veri modeli için bir model seçin ve bunu tutarlı bir şekilde kullanın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-462">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="1bf58-463">Bu öğretici her ikisinin de yalnızca bir iş olduğunu göstermek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-463">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="1bf58-464">`ID`Olmadan kullanmak `classname` , bazı veri modeli değişikliklerinin uygulanmasını kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-464">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="1bf58-465">`Grade`Özelliği bir `enum` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-465">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="1bf58-466">Tür bildiriminden sonraki soru işareti, `Grade` `Grade` özelliğin [null yapılabilir](/dotnet/csharp/programming-guide/nullable-types/)olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-466">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="1bf58-467">Null olan bir sınıf sıfır bir sınıf null değerinden farklıdır &mdash; veya henüz atanmamış olur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-467">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="1bf58-468">`StudentID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Student` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-468">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="1bf58-469">Bir varlık `Enrollment` bir `Student` varlıkla ilişkilendirilir, bu nedenle özellik tek bir varlık içerir `Student` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-469">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="1bf58-470">`CourseID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Course` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-470">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="1bf58-471">Bir `Enrollment` varlık bir `Course` varlıkla ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-471">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="1bf58-472">EF Core bir özelliği, adlandırılmış ise yabancı anahtar olarak yorumlar `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-472">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="1bf58-473">Örneğin, `StudentID` `Student` `Student` nesnenin birincil anahtarı olduğundan, gezinti özelliği için yabancı anahtardır `ID` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-473">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="1bf58-474">Yabancı anahtar özellikleri de adlandırılmış olabilir `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-474">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="1bf58-475">Örneğin, `CourseID` `Course` varlığın birincil anahtarı olduğundan `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-475">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="1bf58-476">Kurs varlığı</span><span class="sxs-lookup"><span data-stu-id="1bf58-476">The Course entity</span></span>

![Kurs varlık diyagramı](intro/_static/course-entity.png)

<span data-ttu-id="1bf58-478">Aşağıdaki kodla *modeller/kurs. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="1bf58-478">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="1bf58-479">`Enrollments`Özelliği bir gezinti özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-479">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="1bf58-480">Bir `Course` varlık herhangi bir sayıda varlıkla ilişkili olabilir `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-480">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="1bf58-481">`DatabaseGenerated`Özniteliği, uygulamanın veritabanını oluşturmak yerine birincil anahtarı belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="1bf58-481">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="1bf58-482">Derleyici hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-482">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="1bf58-483">Yapı iskelesi öğrenci sayfaları</span><span class="sxs-lookup"><span data-stu-id="1bf58-483">Scaffold Student pages</span></span>

<span data-ttu-id="1bf58-484">Bu bölümde, oluşturmak için ASP.NET Core scafkatlama aracını kullanırsınız:</span><span class="sxs-lookup"><span data-stu-id="1bf58-484">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="1bf58-485">EF Core *bağlamı* sınıfı.</span><span class="sxs-lookup"><span data-stu-id="1bf58-485">An EF Core *context* class.</span></span> <span data-ttu-id="1bf58-486">Bağlam, belirli bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-486">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="1bf58-487">`Microsoft.EntityFrameworkCore.DbContext`Sınıfından türetilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-487">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="1bf58-488">:::no-loc(Razor)::: varlık için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemlerini işleyen sayfalar `Student` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-488">:::no-loc(Razor)::: pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bf58-489">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bf58-489">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1bf58-490">*Sayfalar* klasöründe bir *öğrenciler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1bf58-490">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="1bf58-491">**Çözüm Gezgini** , *Sayfalar/öğrenciler* klasörüne sağ tıklayın ve **Add** > **yeni yapı iskelesi Ekle öğesini** seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-491">In **Solution Explorer** , right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="1bf58-492">**Yapı iskelesi Ekle** iletişim kutusunda **:::no-loc(Razor)::: Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > **ADD**.</span><span class="sxs-lookup"><span data-stu-id="1bf58-492">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="1bf58-493">**:::no-loc(Razor)::: Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="1bf58-493">In the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="1bf58-494">**Model sınıfı** açılır penceresinde **öğrenci (Contosouniversity. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-494">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="1bf58-495">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-495">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="1bf58-496">*Contosouniversity. modeller. Contosoüniversıtycontext* olan veri bağlamı adını *Contosouniversity. Data. SchoolContext* olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-496">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="1bf58-497">**Ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-497">Select **Add**.</span></span>

<span data-ttu-id="1bf58-498">Aşağıdaki paketler otomatik olarak yüklenir:</span><span class="sxs-lookup"><span data-stu-id="1bf58-498">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="1bf58-499">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1bf58-499">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1bf58-500">Gerekli NuGet paketlerini yüklemek için aşağıdaki .NET Core CLI komutlarını çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1bf58-500">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="1bf58-501">Yapı iskelesi için Microsoft. VisualStudio. Web. CodeGeneration. Design paketi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-501">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="1bf58-502">Uygulama SQL Server kullanmayabilse de, scafkatlama aracı SQL Server paketine ihtiyaç duyuyor.</span><span class="sxs-lookup"><span data-stu-id="1bf58-502">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="1bf58-503">*Sayfalar/öğrenciler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1bf58-503">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="1bf58-504">[ASPNET-CodeGenerator scafkatlama aracı](xref:fundamentals/tools/dotnet-aspnet-codegenerator)'nı yüklemek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-504">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="1bf58-505">Fkatlama öğrenci sayfalarını iskele almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-505">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="1bf58-506">**Windows üzerinde**</span><span class="sxs-lookup"><span data-stu-id="1bf58-506">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="1bf58-507">**MacOS veya Linux üzerinde**</span><span class="sxs-lookup"><span data-stu-id="1bf58-507">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="1bf58-508">Önceki adımla ilgili bir sorununuz varsa, projeyi derleyin ve yapı iskelesi adımını yeniden deneyin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-508">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="1bf58-509">Yapı iskelesi işlemi:</span><span class="sxs-lookup"><span data-stu-id="1bf58-509">The scaffolding process:</span></span>

* <span data-ttu-id="1bf58-510">:::no-loc(Razor)::: *Sayfalar/öğrenciler* klasöründe sayfa oluşturur:</span><span class="sxs-lookup"><span data-stu-id="1bf58-510">Creates :::no-loc(Razor)::: pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="1bf58-511">*. Cshtml* ve *Create.cshtml.cs* oluşturma</span><span class="sxs-lookup"><span data-stu-id="1bf58-511">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="1bf58-512">*Delete. cshtml* ve *delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="1bf58-512">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="1bf58-513">*Details. cshtml* ve *details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="1bf58-513">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="1bf58-514">*. Cshtml* ve *Edit.cshtml.cs* Düzenle</span><span class="sxs-lookup"><span data-stu-id="1bf58-514">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="1bf58-515">*Index. cshtml* ve *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="1bf58-515">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="1bf58-516">*Data/SchoolContext. cs* oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-516">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="1bf58-517">*Startup.cs* içinde bağımlılık eklenmesine bağlam ekler.</span><span class="sxs-lookup"><span data-stu-id="1bf58-517">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="1bf58-518">Öğesine bir veritabanı bağlantı dizesi ekler *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="1bf58-518">Adds a database connection string to *:::no-loc(appsettings.json):::*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="1bf58-519">Veritabanı bağlantı dizesi</span><span class="sxs-lookup"><span data-stu-id="1bf58-519">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bf58-520">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bf58-520">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1bf58-521">*:::no-loc(appsettings.json):::* Dosya, [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)bağlantı dizesini belirtir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-521">The *:::no-loc(appsettings.json):::* file specifies the connection string [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span>

[!code-json[Main](intro/samples/cu30/:::no-loc(appsettings.json):::?highlight=11)]

<span data-ttu-id="1bf58-522">LocalDB, SQL Server Express veritabanı altyapısının hafif bir sürümüdür ve üretim kullanımı için değil uygulama geliştirmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-522">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="1bf58-523">Varsayılan olarak, LocalDB dizinde *. mdf* dosyaları oluşturur `C:/Users/<user>` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-523">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1bf58-524">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1bf58-524">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1bf58-525">Bağlantı dizesini *cu. db* adlı bir SQLite veritabanı dosyasını işaret etmek üzere değiştirin:</span><span class="sxs-lookup"><span data-stu-id="1bf58-525">Change the connection string to point to a SQLite database file named *CU.db* :</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="1bf58-526">Veritabanı bağlam sınıfını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="1bf58-526">Update the database context class</span></span>

<span data-ttu-id="1bf58-527">Belirli bir veri modeli için EF Core işlevselliğini koordine eden ana sınıf veritabanı bağlamı sınıfıdır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-527">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="1bf58-528">Bağlam [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-528">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="1bf58-529">Bağlam, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-529">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="1bf58-530">Bu projede, sınıfı olarak adlandırılır `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-530">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="1bf58-531">*Data/SchoolContext. cs* öğesini şu kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="1bf58-531">Update *Data/SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="1bf58-532">Vurgulanan kod, her bir varlık kümesi için bir [Dbset \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-532">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="1bf58-533">EF Core terminoloji:</span><span class="sxs-lookup"><span data-stu-id="1bf58-533">In EF Core terminology:</span></span>

* <span data-ttu-id="1bf58-534">Bir varlık kümesi, genellikle bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-534">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="1bf58-535">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-535">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="1bf58-536">Bir varlık kümesi birden çok varlık içerdiğinden, DBSet özellikleri çoğul adlar olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-536">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="1bf58-537">Yapı iskelesi aracı bir `Student` dbset oluşturduğundan, bu adım çoğul olarak değişir `Students` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-537">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="1bf58-538">:::no-loc(Razor):::Sayfa kodunun yeni DBSet adıyla eşleşmesini sağlamak için, tüm projesi genelinde küresel bir değişiklik yapın `_context.Student` `_context.Students` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-538">To make the :::no-loc(Razor)::: Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="1bf58-539">8 oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-539">There are 8 occurrences.</span></span>

<span data-ttu-id="1bf58-540">Derleyici hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-540">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="1bf58-541">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="1bf58-541">Startup.cs</span></span>

<span data-ttu-id="1bf58-542">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-542">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1bf58-543">Hizmetler (EF Core veritabanı bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-543">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="1bf58-544">Bu hizmetleri gerektiren bileşenler (örneğin, :::no-loc(Razor)::: Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-544">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="1bf58-545">Bir veritabanı bağlamı örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-545">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="1bf58-546">Scafkatlama Aracı, bağlam sınıfını bağımlılık ekleme kapsayıcısına otomatik olarak kaydetti.</span><span class="sxs-lookup"><span data-stu-id="1bf58-546">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bf58-547">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bf58-547">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1bf58-548">`ConfigureServices`' De, vurgulanan satırlar scaffolder tarafından eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="1bf58-548">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1bf58-549">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1bf58-549">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1bf58-550">' De `ConfigureServices` , desteği tarafından eklenen kodun çağrılarından emin olun `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-550">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="1bf58-551">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-551">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="1bf58-552">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="1bf58-552">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="1bf58-553">Veritabanını oluşturma</span><span class="sxs-lookup"><span data-stu-id="1bf58-553">Create the database</span></span>

<span data-ttu-id="1bf58-554">Mevcut değilse veritabanını oluşturmak için *program.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="1bf58-554">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="1bf58-555">Bağlam için bir veritabanı varsa, [Ensuyeniden](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) oluşturma yöntemi hiçbir eylemde bulunmaz.</span><span class="sxs-lookup"><span data-stu-id="1bf58-555">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="1bf58-556">Veritabanı yoksa, veritabanını ve şemayı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-556">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="1bf58-557">`EnsureCreated` veri modeli değişikliklerini işlemek için aşağıdaki iş akışını sunar:</span><span class="sxs-lookup"><span data-stu-id="1bf58-557">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="1bf58-558">Veritabanını silin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-558">Delete the database.</span></span> <span data-ttu-id="1bf58-559">Mevcut veriler kaybolur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-559">Any existing data is lost.</span></span>
* <span data-ttu-id="1bf58-560">Veri modelini değiştirin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-560">Change the data model.</span></span> <span data-ttu-id="1bf58-561">Örneğin, bir alan ekleyin `EmailAddress` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-561">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="1bf58-562">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-562">Run the app.</span></span>
* <span data-ttu-id="1bf58-563">`EnsureCreated` yeni şemaya sahip bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-563">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="1bf58-564">Bu iş akışı, verileri korumanıza gerek olmadığı sürece, şema hızlı bir şekilde gelişen zaman geliştirme aşamasında iyi bir şekilde gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-564">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="1bf58-565">Veritabanına girilen verilerin korunması gerektiğinde bu durum farklıdır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-565">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="1bf58-566">Bu durumda, geçişleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-566">When that is the case, use migrations.</span></span>

<span data-ttu-id="1bf58-567">Öğretici serisinde daha sonra tarafından oluşturulan veritabanını siler `EnsureCreated` ve bunun yerine geçişleri kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="1bf58-567">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="1bf58-568">Tarafından oluşturulan bir veritabanı, `EnsureCreated` geçişler kullanılarak güncelleştirilemiyor.</span><span class="sxs-lookup"><span data-stu-id="1bf58-568">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="1bf58-569">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="1bf58-569">Test the app</span></span>

* <span data-ttu-id="1bf58-570">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-570">Run the app.</span></span>
* <span data-ttu-id="1bf58-571">**Öğrenciler** bağlantısını seçin ve ardından **Yeni oluştur**.</span><span class="sxs-lookup"><span data-stu-id="1bf58-571">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="1bf58-572">Düzenle, Ayrıntılar ve Sil bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-572">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="1bf58-573">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="1bf58-573">Seed the database</span></span>

<span data-ttu-id="1bf58-574">`EnsureCreated`Yöntemi boş bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-574">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="1bf58-575">Bu bölüm, veritabanını test verileriyle dolduran kodu ekler.</span><span class="sxs-lookup"><span data-stu-id="1bf58-575">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="1bf58-576">Aşağıdaki kodla *veri/Dbınizer. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="1bf58-576">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="1bf58-577">Kod, veritabanında herhangi bir öğrenci olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="1bf58-577">The code checks if there are any students in the database.</span></span> <span data-ttu-id="1bf58-578">Öğrenci yoksa, veritabanına test verileri ekler.</span><span class="sxs-lookup"><span data-stu-id="1bf58-578">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="1bf58-579">Performansı iyileştirmek için Koleksiyonlar yerine diziler halinde test verileri oluşturur `List<T>` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-579">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="1bf58-580">*Program.cs* içinde, `EnsureCreated` çağrıyı bir çağrı ile değiştirin `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="1bf58-580">In *Program.cs* , replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="1bf58-581">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bf58-581">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1bf58-582">Çalışıyorsa uygulamayı durdurun ve **Paket Yöneticisi konsolunda** (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1bf58-582">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="1bf58-583">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1bf58-583">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1bf58-584">Çalışıyorsa uygulamayı durdurun ve *cu. db* dosyasını silin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-584">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="1bf58-585">Uygulamayı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-585">Restart the app.</span></span>

* <span data-ttu-id="1bf58-586">Sağlanan verileri görmek için öğrenciler sayfasını seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-586">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="1bf58-587">Veritabanını görüntüleme</span><span class="sxs-lookup"><span data-stu-id="1bf58-587">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bf58-588">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bf58-588">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1bf58-589">Visual Studio 'daki **Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-589">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="1bf58-590">SSOX 'te, **(LocalDB) \MSSQLLocalDB > veritabanları > SchoolContext-{GUID}** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-590">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="1bf58-591">Veritabanı adı, daha önce belirttiğiniz bağlam adından ve bir tire ve bir GUID ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-591">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="1bf58-592">**Tables** düğümünü genişletin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-592">Expand the **Tables** node.</span></span>
* <span data-ttu-id="1bf58-593">Oluşturulan sütunları ve tabloya yerleştirilen satırları görmek için **öğrenci** tablosuna sağ tıklayın ve **verileri görüntüle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-593">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="1bf58-594">Modelin tablo şemasına nasıl eşlendiğini görmek için **öğrenci** tablosuna sağ tıklayın ve **kodu görüntüle** ' ye tıklayın `Student` `Student` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-594">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1bf58-595">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1bf58-595">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1bf58-596">Veritabanı şemasını ve sağlanan verileri görüntülemek için SQLite aracınızı kullanın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-596">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="1bf58-597">Veritabanı dosyası *cu. db* olarak adlandırılır ve proje klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-597">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="1bf58-598">Zaman uyumsuz kod</span><span class="sxs-lookup"><span data-stu-id="1bf58-598">Asynchronous code</span></span>

<span data-ttu-id="1bf58-599">Zaman uyumsuz programlama, ASP.NET Core ve EF Core için varsayılan moddur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-599">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="1bf58-600">Web sunucusunda sınırlı sayıda iş parçacığı bulunur ve yüksek yük durumlarında tüm kullanılabilir iş parçacıkları kullanımda olabilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-600">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="1bf58-601">Bu durumda, sunucu, iş parçacıkları boşaltılana kadar yeni istekleri işleyemez.</span><span class="sxs-lookup"><span data-stu-id="1bf58-601">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="1bf58-602">Zaman uyumlu kodla, çok sayıda iş parçacığı, g/ç 'nin tamamlanmasını beklediği için aslında herhangi bir iş yapmadıklarında bağlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-602">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="1bf58-603">Zaman uyumsuz kod ile, bir işlem g/ç 'yi tamamlanmayı beklerken, sunucunun diğer istekleri işlemek için kullanması için iş parçacığı serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-603">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="1bf58-604">Sonuç olarak, zaman uyumsuz kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucu gecikmeksizin daha fazla trafiği işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-604">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="1bf58-605">Zaman uyumsuz kod, çalışma zamanında az miktarda yük getirir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-605">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="1bf58-606">Düşük trafik durumlarında, performans artışı göz ardı edilebilir, ancak yüksek trafik durumları için olası performans iyileştirmesi oldukça önemlidir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-606">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="1bf58-607">Aşağıdaki kodda, [Async](/dotnet/csharp/language-reference/keywords/async) anahtar sözcüğü, `Task<T>` return değeri, `await` anahtar sözcüğü ve `ToListAsync` yöntemi kodu zaman uyumsuz olarak yürütür.</span><span class="sxs-lookup"><span data-stu-id="1bf58-607">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="1bf58-608">`async`Anahtar sözcüğü derleyiciye şunu söyler:</span><span class="sxs-lookup"><span data-stu-id="1bf58-608">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="1bf58-609">Yöntem gövdesinin parçaları için geri çağrılar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1bf58-609">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="1bf58-610">Döndürülen [görev](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) nesnesini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1bf58-610">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="1bf58-611">`Task<T>`Dönüş türü, devam eden işi temsil eder.</span><span class="sxs-lookup"><span data-stu-id="1bf58-611">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="1bf58-612">`await`Anahtar sözcüğü, derleyicinin yöntemi iki parçaya böetmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-612">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="1bf58-613">İlk bölüm, zaman uyumsuz olarak başlatılan işlemle biter.</span><span class="sxs-lookup"><span data-stu-id="1bf58-613">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="1bf58-614">İkinci bölüm, işlem tamamlandığında çağrılan bir geri çağırma yöntemine konur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-614">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="1bf58-615">`ToListAsync` , Uzantı yönteminin zaman uyumsuz sürümüdür `ToList` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-615">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="1bf58-616">EF Core kullanan zaman uyumsuz kodu yazarken dikkat edilmesi gereken bazı şeyler:</span><span class="sxs-lookup"><span data-stu-id="1bf58-616">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="1bf58-617">Yalnızca sorguları veya komutlarının veritabanına gönderilmesine neden olan deyimler zaman uyumsuz olarak yürütülür.</span><span class="sxs-lookup"><span data-stu-id="1bf58-617">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="1bf58-618">,, `ToListAsync` , `SingleOrDefaultAsync` `FirstOrDefaultAsync` Ve içerir `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-618">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="1bf58-619">Yalnızca, gibi bir değiştiren deyimler içermez `IQueryable` `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-619">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="1bf58-620">EF Core bağlamı iş parçacığı açısından güvenli değildir: paralel olarak birden çok işlem yapmayı denemeyin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-620">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="1bf58-621">Zaman uyumsuz kodun performans avantajlarından yararlanmak için, veritabanına sorgu gönderen EF Core yöntemleri çağırıyorsa kitaplık paketlerinin (örneğin, sayfalama için) zaman uyumsuz olarak kullanılacağını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-621">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="1bf58-622">.NET 'te zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. Async [and await ile](/dotnet/csharp/programming-guide/concepts/async/)zaman uyumsuz [genel bakış](/dotnet/standard/async) ve zaman uyumsuz programlama</span><span class="sxs-lookup"><span data-stu-id="1bf58-622">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="1bf58-623">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="1bf58-623">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1bf58-624">Sonraki öğretici</span><span class="sxs-lookup"><span data-stu-id="1bf58-624">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1bf58-625">Contoso Üniversitesi örnek Web uygulaması, :::no-loc(Razor)::: Entity Framework (EF) Core kullanarak ASP.NET Core sayfalı bir uygulamanın nasıl oluşturulacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-625">The Contoso University sample web app demonstrates how to create an ASP.NET Core :::no-loc(Razor)::: Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="1bf58-626">Örnek uygulama, kurgusal bir Contoso Üniversitesi için bir Web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-626">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="1bf58-627">Öğrenci giriş, kurs oluşturma ve eğitmen atamaları gibi işlevleri içerir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-627">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="1bf58-628">Bu sayfa, Contoso Üniversitesi örnek uygulamasının nasıl oluşturulacağını açıklayan bir öğretici serisinin ilkisidir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-628">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="1bf58-629">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-629">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="1bf58-630">[Yönergeleri indirin](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="1bf58-630">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1bf58-631">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="1bf58-631">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bf58-632">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bf58-632">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="1bf58-633">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1bf58-633">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="1bf58-634">[ :::no-loc(Razor)::: Sayfalarla](xref:razor-pages/index)benzerlik.</span><span class="sxs-lookup"><span data-stu-id="1bf58-634">Familiarity with [:::no-loc(Razor)::: Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="1bf58-635">Yeni programcılar, bu seriyi başlatmadan önce [sayfalarla çalışmaya başlama :::no-loc(Razor)::: sayfasını](xref:tutorials/razor-pages/razor-pages-start) tamamlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-635">New programmers should complete [Get started with :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="1bf58-636">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="1bf58-636">Troubleshooting</span></span>

<span data-ttu-id="1bf58-637">Çözemiyoruz bir sorunla karşılaşırsanız, kodunuzun [Tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)karşılaştırılmasıyla genellikle çözümü bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1bf58-637">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="1bf58-638">[ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) [için bir](https://stackoverflow.com/questions/tagged/asp.net-core) soru göndererek yardım almanın iyi bir yolu.</span><span class="sxs-lookup"><span data-stu-id="1bf58-638">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="1bf58-639">Contoso Üniversitesi web uygulaması</span><span class="sxs-lookup"><span data-stu-id="1bf58-639">The Contoso University web app</span></span>

<span data-ttu-id="1bf58-640">Bu öğreticilerde oluşturulan uygulama, temel bir üniversite web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-640">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="1bf58-641">Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-641">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="1bf58-642">Öğreticide oluşturulan ekranlardan bazıları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-642">Here are a few of the screens created in the tutorial.</span></span>

![Öğrenciler Dizin sayfası](intro/_static/students-index.png)

![Öğrenciler düzenleme sayfası](intro/_static/student-edit.png)

<span data-ttu-id="1bf58-645">Bu sitenin kullanıcı arabirimi stili yerleşik şablonlar tarafından üretilme kadar yakın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-645">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="1bf58-646">Eğitim odağı, :::no-loc(Razor)::: kullanıcı arabiriminden değil, sayfalarla EF Core.</span><span class="sxs-lookup"><span data-stu-id="1bf58-646">The tutorial focus is on EF Core with :::no-loc(Razor)::: Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="1bf58-647">Contosoüniversitesi :::no-loc(Razor)::: sayfaları Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="1bf58-647">Create the ContosoUniversity :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bf58-648">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bf58-648">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1bf58-649">Visual Studio **Dosya** menüsünden **Yeni** > **Proje** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-649">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="1bf58-650">Yeni bir ASP.NET Core Web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1bf58-650">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="1bf58-651">Projeyi **Contosouniversity** olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-651">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="1bf58-652">Kod kopyalama/yapıştırma olduğunda, ad alanlarının eşleşmesi için *Contosouniversity* projesini adlandırmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-652">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="1bf58-653">Açılan listede **ASP.NET Core 2,1** ' i seçin ve ardından **Web uygulaması** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-653">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="1bf58-654">Yukarıdaki adımların görüntüleri için bkz. [ :::no-loc(Razor)::: Web uygulaması oluşturma](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="1bf58-654">For images of the preceding steps, see [Create a :::no-loc(Razor)::: web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="1bf58-655">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-655">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1bf58-656">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1bf58-656">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="1bf58-657">Site stilini ayarlayın</span><span class="sxs-lookup"><span data-stu-id="1bf58-657">Set up the site style</span></span>

<span data-ttu-id="1bf58-658">Site menüsünü, düzeni ve giriş sayfasını birkaç değişiklik ayarlar.</span><span class="sxs-lookup"><span data-stu-id="1bf58-658">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="1bf58-659">*Sayfaları/paylaşılan/_Layout. cshtml* 'yi aşağıdaki değişikliklerle güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="1bf58-659">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="1bf58-660">"ContosoUniversity" öğesinin her oluşumunu "Contoso Üniversitesi" olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-660">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="1bf58-661">Üç oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-661">There are three occurrences.</span></span>

* <span data-ttu-id="1bf58-662">**Öğrenciler** , **Kurslar** , **eğitmenler** ve **Departmanlar** için menü girişleri ekleyin ve **kişi** menü girişini silin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-662">Add menu entries for **Students** , **Courses** , **Instructors** , and **Departments** , and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="1bf58-663">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-663">The changes are highlighted.</span></span> <span data-ttu-id="1bf58-664">(Tüm *biçimlendirme gösterilmez.* )</span><span class="sxs-lookup"><span data-stu-id="1bf58-664">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="1bf58-665">*Pages/Index. cshtml* dosyasında, ASP.net ve MVC hakkındaki metni bu uygulamayla ilgili metinle değiştirmek için dosyanın içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="1bf58-665">In *Pages/Index.cshtml* , replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="1bf58-666">Veri modelini oluşturma</span><span class="sxs-lookup"><span data-stu-id="1bf58-666">Create the data model</span></span>

<span data-ttu-id="1bf58-667">Contoso Üniversitesi uygulaması için varlık sınıfları oluşturma.</span><span class="sxs-lookup"><span data-stu-id="1bf58-667">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="1bf58-668">Aşağıdaki üç varlıkla başlayın:</span><span class="sxs-lookup"><span data-stu-id="1bf58-668">Start with the following three entities:</span></span>

![Kurs-kayıt-öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

<span data-ttu-id="1bf58-670">Ve varlıkları arasında bire çok ilişki vardır `Student` `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-670">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="1bf58-671">Ve varlıkları arasında bire çok ilişki vardır `Course` `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-671">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="1bf58-672">Bir öğrenci herhangi bir sayıda kursa kaydolabilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-672">A student can enroll in any number of courses.</span></span> <span data-ttu-id="1bf58-673">Bir kurs, kayıtlı sayıda öğrenciye sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-673">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="1bf58-674">Aşağıdaki bölümlerde, bu varlıkların her biri için bir sınıf oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-674">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="1bf58-675">Öğrenci varlığı</span><span class="sxs-lookup"><span data-stu-id="1bf58-675">The Student entity</span></span>

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

<span data-ttu-id="1bf58-677">*Modeller* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1bf58-677">Create a *Models* folder.</span></span> <span data-ttu-id="1bf58-678">*Modeller* klasöründe, *Student.cs* adlı bir sınıf dosyasını aşağıdaki kodla oluşturun:</span><span class="sxs-lookup"><span data-stu-id="1bf58-678">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="1bf58-679">`ID`Özelliği, bu sınıfa karşılık gelen veritabanı (DB) tablosunun birincil anahtar sütunu olur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-679">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="1bf58-680">Varsayılan olarak, EF Core `ID` birincil anahtar olarak adlandırılan bir özelliği Yorumlar `classnameID` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-680">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="1bf58-681">`classnameID`' De, `classname` sınıfının adıdır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-681">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="1bf58-682">Diğer otomatik olarak tanınan birincil anahtar, `StudentID` Önceki örnekte yer verilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-682">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="1bf58-683">`Enrollments`Özelliği bir [Gezinti özelliğidir](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="1bf58-683">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="1bf58-684">Gezinti özellikleri bu varlıkla ilgili diğer varlıkların bağlantısını sağlar.</span><span class="sxs-lookup"><span data-stu-id="1bf58-684">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="1bf58-685">Bu durumda, `Enrollments` öğesinin özelliği ile `Student entity` `Enrollment` ilgili tüm varlıkları barındırır `Student` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-685">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="1bf58-686">Örneğin, VERITABANıNDAKI bir öğrenci satırında iki ilişkili kayıt satırı varsa, `Enrollments` gezinti özelliği bu iki `Enrollment` varlığı içerir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-686">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="1bf58-687">İlgili `Enrollment` satır, söz konusu öğrencinin birincil anahtar değerini sütununda içeren bir satırdır `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-687">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="1bf58-688">Örneğin, ID = 1 olan öğrencinin tabloda iki satır olduğunu varsayalım `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-688">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="1bf58-689">`Enrollment`Tabloda = 1 olan iki satır vardır `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-689">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="1bf58-690">`StudentID` , tablodaki `Enrollment` öğrencisi belirten bir yabancı anahtardır `Student` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-690">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="1bf58-691">Bir gezinti özelliği birden çok varlık tutabileceğinden, gezinti özelliği gibi bir liste türü olmalıdır `ICollection<T>` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-691">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="1bf58-692">`ICollection<T>` belirtilebilir veya veya gibi bir tür olabilir `List<T>` `HashSet<T>` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-692">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="1bf58-693">Kullanıldığında `ICollection<T>` , EF Core `HashSet<T>` Varsayılan olarak bir koleksiyon oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-693">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="1bf58-694">Birden çok varlığı tutan gezinti özellikleri, çoktan çoğa ve bire çok ilişkilerden gelir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-694">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="1bf58-695">Kayıt varlığı</span><span class="sxs-lookup"><span data-stu-id="1bf58-695">The Enrollment entity</span></span>

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

<span data-ttu-id="1bf58-697">*Modeller* klasöründe, aşağıdaki kodla *enrollment.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="1bf58-697">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="1bf58-698">`EnrollmentID`Özelliği birincil anahtardır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-698">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="1bf58-699">Bu varlık `classnameID` `ID` , varlığı beğenmek yerine, modelini kullanır `Student` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-699">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="1bf58-700">Genellikle geliştiriciler bir model seçer ve bunu veri modeli boyunca kullanır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-700">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="1bf58-701">Daha sonraki bir öğreticide, veri modelinde devralmayı daha kolay hale getirmek için ClassName olmadan ID kullanımı gösterilmemiştir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-701">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="1bf58-702">`Grade`Özelliği bir `enum` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-702">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="1bf58-703">Tür bildiriminden sonraki soru işareti, `Grade` `Grade` özelliğin null yapılabilir olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-703">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="1bf58-704">Null olan bir sınıf sıfır bir sınıf ile farklıdır--null, henüz bir sınıf bilinmediğini veya henüz atanmadığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-704">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="1bf58-705">`StudentID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Student` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-705">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="1bf58-706">Bir varlık `Enrollment` bir `Student` varlıkla ilişkilendirilir, bu nedenle özellik tek bir varlık içerir `Student` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-706">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="1bf58-707">`Student`Varlık, `Student.Enrollments` birden çok varlık içeren gezinti özelliğinden farklıdır `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-707">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="1bf58-708">`CourseID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Course` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-708">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="1bf58-709">Bir `Enrollment` varlık bir `Course` varlıkla ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-709">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="1bf58-710">EF Core bir özelliği, adlandırılmış ise yabancı anahtar olarak yorumlar `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-710">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="1bf58-711">Örneğin, `StudentID` `Student` gezinti özelliği için `Student` varlığın birincil anahtarı olduğundan `ID` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-711">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="1bf58-712">Yabancı anahtar özellikleri de adlandırılmış olabilir `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-712">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="1bf58-713">Örneğin, `CourseID` `Course` varlığın birincil anahtarı olduğundan `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-713">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="1bf58-714">Kurs varlığı</span><span class="sxs-lookup"><span data-stu-id="1bf58-714">The Course entity</span></span>

![Kurs varlık diyagramı](intro/_static/course-entity.png)

<span data-ttu-id="1bf58-716">*Modeller* klasöründe, aşağıdaki kodla *Course.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="1bf58-716">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="1bf58-717">`Enrollments`Özelliği bir gezinti özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-717">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="1bf58-718">Bir `Course` varlık herhangi bir sayıda varlıkla ilişkili olabilir `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-718">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="1bf58-719">`DatabaseGenerated`Özniteliği, UYGULAMANıN DB oluşturmak yerine birincil anahtarı belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="1bf58-719">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="1bf58-720">Öğrenci modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="1bf58-720">Scaffold the student model</span></span>

<span data-ttu-id="1bf58-721">Bu bölümde öğrenci modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-721">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="1bf58-722">Diğer bir deyişle, scafkatlama aracı öğrenci modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-722">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="1bf58-723">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-723">Build the project.</span></span>
* <span data-ttu-id="1bf58-724">*Sayfalar/öğrenciler* klasörünü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1bf58-724">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="1bf58-725">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bf58-725">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1bf58-726">**Çözüm Gezgini** , *Sayfalar/öğrenciler* klasörüne sağ tıklayarak **Add** > **yeni yapı iskelesi öğesi** ekleyin >.</span><span class="sxs-lookup"><span data-stu-id="1bf58-726">In **Solution Explorer** , right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="1bf58-727">**Yapı iskelesi Ekle** iletişim kutusunda **:::no-loc(Razor)::: Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar** ' ı seçin > **ADD**.</span><span class="sxs-lookup"><span data-stu-id="1bf58-727">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="1bf58-728">**:::no-loc(Razor)::: Entity Framework (CRUD) kullanarak sayfa ekle** iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="1bf58-728">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="1bf58-729">**Model sınıfı** açılır penceresinde **öğrenci (Contosouniversity. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-729">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="1bf58-730">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin ve üretilen adı **Contosouniversity. modeller. SchoolContext** olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-730">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="1bf58-731">**Veri bağlamı sınıfı** açılır penceresinde **Contosouniversity. modeller. SchoolContext** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-731">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="1bf58-732">**Ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-732">Select **Add**.</span></span>

![CRUD iletişim kutusu](intro/_static/s1.png)

<span data-ttu-id="1bf58-734">Önceki adımla ilgili bir sorununuz varsa [Film modeli](xref:tutorials/razor-pages/model#scaffold-the-movie-model) ' ne bakın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-734">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="1bf58-735">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1bf58-735">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="1bf58-736">Öğrenci modelini iskele almak için aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-736">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="1bf58-737">Yapı iskelesi işlemi oluşturulur ve aşağıdaki dosyaları değiştirdi:</span><span class="sxs-lookup"><span data-stu-id="1bf58-737">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="1bf58-738">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="1bf58-738">Files created</span></span>

* <span data-ttu-id="1bf58-739">*Sayfalar/öğrenciler* Oluşturma, silme, ayrıntılar, düzenleme, dizin oluşturma.</span><span class="sxs-lookup"><span data-stu-id="1bf58-739">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="1bf58-740">*Data/SchoolContext. cs*</span><span class="sxs-lookup"><span data-stu-id="1bf58-740">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="1bf58-741">Dosya güncelleştirmeleri</span><span class="sxs-lookup"><span data-stu-id="1bf58-741">File updates</span></span>

* <span data-ttu-id="1bf58-742">*Startup.cs* : Bu dosyadaki değişiklikler sonraki bölümde ayrıntılıdır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-742">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="1bf58-743">*:::no-loc(appsettings.json):::* : Yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesi eklenir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-743">*:::no-loc(appsettings.json):::* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="1bf58-744">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="1bf58-744">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="1bf58-745">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-745">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="1bf58-746">Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-746">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="1bf58-747">Bu hizmetleri gerektiren bileşenler (örneğin, :::no-loc(Razor)::: Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-747">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="1bf58-748">Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-748">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="1bf58-749">Scafkatlama aracı otomatik olarak bir DB bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.</span><span class="sxs-lookup"><span data-stu-id="1bf58-749">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="1bf58-750">`ConfigureServices` *Startup.cs* içindeki yöntemi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-750">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="1bf58-751">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="1bf58-751">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="1bf58-752">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-752">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="1bf58-753">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki bağlantı dizesini okur *:::no-loc(appsettings.json):::* .</span><span class="sxs-lookup"><span data-stu-id="1bf58-753">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="1bf58-754">Ana güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="1bf58-754">Update main</span></span>

<span data-ttu-id="1bf58-755">*Program.cs* ' de, `Main` aşağıdakileri yapmak için yöntemini değiştirin:</span><span class="sxs-lookup"><span data-stu-id="1bf58-755">In *Program.cs* , modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="1bf58-756">Bağımlılık ekleme kapsayıcısından bir DB bağlam örneği alın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-756">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="1bf58-757">Yeniden  [oluşturulmasını](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)çağırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-757">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="1bf58-758">`EnsureCreated`Yöntem tamamlandığında bağlamı atın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-758">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="1bf58-759">Aşağıdaki kod güncelleştirilmiş *program.cs* dosyasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-759">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="1bf58-760">`EnsureCreated` bağlam veritabanının mevcut olmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="1bf58-760">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="1bf58-761">Varsa, hiçbir eylem yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="1bf58-761">If it exists, no action is taken.</span></span> <span data-ttu-id="1bf58-762">Yoksa, veritabanı ve tüm şeması oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-762">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="1bf58-763">`EnsureCreated` veritabanını oluşturmak için geçişleri kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="1bf58-763">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="1bf58-764">İle oluşturulan bir veritabanı `EnsureCreated` daha sonra geçişler kullanılarak güncelleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="1bf58-764">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="1bf58-765">`EnsureCreated` , aşağıdaki iş akışına izin veren uygulama başlatma sırasında çağrılır:</span><span class="sxs-lookup"><span data-stu-id="1bf58-765">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="1bf58-766">VERITABANıNı silin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-766">Delete the DB.</span></span>
* <span data-ttu-id="1bf58-767">DB şemasını değiştirin (örneğin, bir `EmailAddress` alan ekleyin).</span><span class="sxs-lookup"><span data-stu-id="1bf58-767">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="1bf58-768">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-768">Run the app.</span></span>
* <span data-ttu-id="1bf58-769">`EnsureCreated` sütunu ile bir VERITABANı oluşturur `EmailAddress` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-769">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="1bf58-770">`EnsureCreated` şema hızlı bir şekilde gelişmede geliştirmede daha erken kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-770">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="1bf58-771">Öğreticide daha sonra DB silinir ve geçişler kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-771">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="1bf58-772">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="1bf58-772">Test the app</span></span>

<span data-ttu-id="1bf58-773">Uygulamayı çalıştırın ve ilkeyi kabul edin :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="1bf58-773">Run the app and accept the :::no-loc(cookie)::: policy.</span></span> <span data-ttu-id="1bf58-774">Bu uygulama, kişisel bilgileri saklar.</span><span class="sxs-lookup"><span data-stu-id="1bf58-774">This app doesn't keep personal information.</span></span> <span data-ttu-id="1bf58-775">İlke hakkında, :::no-loc(cookie)::: [AB Genel VERI koruma YÖNETMELIĞI (GDPR) desteğiyle](xref:security/gdpr)ilgili bilgi edinebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1bf58-775">You can read about the :::no-loc(cookie)::: policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="1bf58-776">**Öğrenciler** bağlantısını seçin ve ardından **Yeni oluştur**.</span><span class="sxs-lookup"><span data-stu-id="1bf58-776">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="1bf58-777">Düzenle, Ayrıntılar ve Sil bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-777">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="1bf58-778">SchoolContext DB bağlamını inceleyin</span><span class="sxs-lookup"><span data-stu-id="1bf58-778">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="1bf58-779">Belirli bir veri modeli için EF Core işlevselliğini koordine eden ana sınıf DB bağlam sınıfıdır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-779">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="1bf58-780">Veri bağlamı [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-780">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="1bf58-781">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-781">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="1bf58-782">Bu projede, sınıfı olarak adlandırılır `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-782">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="1bf58-783">Aşağıdaki kodla *SchoolContext.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="1bf58-783">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="1bf58-784">Vurgulanan kod, her bir varlık kümesi için bir [Dbset \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-784">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="1bf58-785">EF Core terminoloji:</span><span class="sxs-lookup"><span data-stu-id="1bf58-785">In EF Core terminology:</span></span>

* <span data-ttu-id="1bf58-786">Bir varlık kümesi, genellikle bir DB tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-786">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="1bf58-787">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-787">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="1bf58-788">`DbSet<Enrollment>` ve `DbSet<Course>` atlanamaz.</span><span class="sxs-lookup"><span data-stu-id="1bf58-788">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="1bf58-789">EF Core `Student` `Enrollment` , varlık varlığa başvurduğundan ve varlık `Enrollment` varlığa başvurduğundan bunları örtülü olarak içerir `Course` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-789">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="1bf58-790">Bu öğreticide, `DbSet<Enrollment>` ve `DbSet<Course>` içinde tutun `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-790">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="1bf58-791">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="1bf58-791">SQL Server Express LocalDB</span></span>

<span data-ttu-id="1bf58-792">Bağlantı dizesi [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)belirtir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-792">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="1bf58-793">LocalDB, SQL Server Express veritabanı altyapısının hafif bir sürümüdür ve üretim kullanımı için değil uygulama geliştirmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-793">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="1bf58-794">LocalDB, istek üzerine başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-794">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="1bf58-795">Varsayılan olarak, LocalDB dizinde *. mdf* DB dosyaları oluşturur `C:/Users/<user>` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-795">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="1bf58-796">Test verileriyle VERITABANıNı başlatmak için kod ekleme</span><span class="sxs-lookup"><span data-stu-id="1bf58-796">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="1bf58-797">EF Core boş bir VERITABANı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-797">EF Core creates an empty DB.</span></span> <span data-ttu-id="1bf58-798">Bu bölümde, `Initialize` Test verileriyle doldurmak için bir yöntem yazılır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-798">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="1bf58-799">*Veri* klasöründe, *DbInitializer.cs* adlı yeni bir sınıf dosyası oluşturun ve aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1bf58-799">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="1bf58-800">Note: önceki kod, `Models` yerine ad alanı için ( `namespace ContosoUniversity.Models` ) kullanır `Data` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-800">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="1bf58-801">`Models` , scaffolder tarafından oluşturulan kodla tutarlıdır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-801">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="1bf58-802">Daha fazla bilgi için bkz. [GitHub yapı iskelesi sorunu](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="1bf58-802">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="1bf58-803">Kod, VERITABANıNDA herhangi bir öğrenci olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="1bf58-803">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="1bf58-804">Veritabanında hiç öğrenci yoksa, DB test verileriyle başlatılır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-804">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="1bf58-805">Performansı iyileştirmek için test verilerini koleksiyonlar yerine dizilere yükler `List<T>` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-805">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="1bf58-806">`EnsureCreated`YÖNTEMI DB bağlamı için otomatik olarak DB oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-806">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="1bf58-807">VERITABANı varsa, `EnsureCreated` veritabanını değiştirmeden döndürür.</span><span class="sxs-lookup"><span data-stu-id="1bf58-807">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="1bf58-808">*Program.cs* içinde, `Main` çağrısı yapılacak yöntemi değiştirin `Initialize` :</span><span class="sxs-lookup"><span data-stu-id="1bf58-808">In *Program.cs* , modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="1bf58-809">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1bf58-809">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1bf58-810">Çalışıyorsa uygulamayı durdurun ve **Paket Yöneticisi konsolunda** (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="1bf58-810">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="1bf58-811">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1bf58-811">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1bf58-812">Çalışıyorsa uygulamayı durdurun ve *cu. db* dosyasını silin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-812">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="1bf58-813">VERITABANıNı görüntüleme</span><span class="sxs-lookup"><span data-stu-id="1bf58-813">View the DB</span></span>

<span data-ttu-id="1bf58-814">Veritabanı adı, daha önce belirttiğiniz bağlam adından ve bir tire ve bir GUID ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-814">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="1bf58-815">Bu nedenle, veritabanı adı "SchoolContext-{GUID}" olacaktır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-815">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="1bf58-816">GUID her kullanıcı için farklı olacaktır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-816">The GUID will be different for each user.</span></span>
<span data-ttu-id="1bf58-817">Visual Studio 'daki **Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-817">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="1bf58-818">SSOX 'te, **(LocalDB) \MSSQLLocalDB > veritabanları > SchoolContext-{GUID}** ' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-818">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="1bf58-819">**Tables** düğümünü genişletin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-819">Expand the **Tables** node.</span></span>

<span data-ttu-id="1bf58-820">Oluşturulan sütunları ve tabloya yerleştirilen satırları görmek için **öğrenci** tablosuna sağ tıklayın ve **verileri görüntüle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-820">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="1bf58-821">Zaman uyumsuz kod</span><span class="sxs-lookup"><span data-stu-id="1bf58-821">Asynchronous code</span></span>

<span data-ttu-id="1bf58-822">Zaman uyumsuz programlama, ASP.NET Core ve EF Core için varsayılan moddur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-822">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="1bf58-823">Web sunucusunda sınırlı sayıda iş parçacığı bulunur ve yüksek yük durumlarında tüm kullanılabilir iş parçacıkları kullanımda olabilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-823">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="1bf58-824">Bu durumda, sunucu, iş parçacıkları boşaltılana kadar yeni istekleri işleyemez.</span><span class="sxs-lookup"><span data-stu-id="1bf58-824">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="1bf58-825">Zaman uyumlu kodla, çok sayıda iş parçacığı, g/ç 'nin tamamlanmasını beklediği için aslında herhangi bir iş yapmadıklarında bağlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-825">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="1bf58-826">Zaman uyumsuz kod ile, bir işlem g/ç 'yi tamamlanmayı beklerken, sunucunun diğer istekleri işlemek için kullanması için iş parçacığı serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="1bf58-826">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="1bf58-827">Sonuç olarak, zaman uyumsuz kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucu, gecikme olmadan daha fazla trafiği işlemeye etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-827">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="1bf58-828">Zaman uyumsuz kod, çalışma zamanında az miktarda yük getirir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-828">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="1bf58-829">Düşük trafik durumlarında, performans artışı göz ardı edilebilir, ancak yüksek trafik durumları için olası performans iyileştirmesi oldukça önemlidir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-829">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="1bf58-830">Aşağıdaki kodda, [Async](/dotnet/csharp/language-reference/keywords/async) anahtar sözcüğü, `Task<T>` return değeri, `await` anahtar sözcüğü ve `ToListAsync` yöntemi kodu zaman uyumsuz olarak yürütür.</span><span class="sxs-lookup"><span data-stu-id="1bf58-830">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="1bf58-831">`async`Anahtar sözcüğü derleyiciye şunu söyler:</span><span class="sxs-lookup"><span data-stu-id="1bf58-831">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="1bf58-832">Yöntem gövdesinin parçaları için geri çağrılar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1bf58-832">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="1bf58-833">Döndürülen [görev](/dotnet/api/system.threading.tasks.task) nesnesini otomatik olarak oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1bf58-833">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="1bf58-834">Daha fazla bilgi için bkz. [görev dönüş türü](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="1bf58-834">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="1bf58-835">Örtük dönüş türü, `Task` devam eden işi temsil eder.</span><span class="sxs-lookup"><span data-stu-id="1bf58-835">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="1bf58-836">`await`Anahtar sözcüğü, derleyicinin yöntemi iki parçaya böetmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-836">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="1bf58-837">İlk bölüm, zaman uyumsuz olarak başlatılan işlemle biter.</span><span class="sxs-lookup"><span data-stu-id="1bf58-837">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="1bf58-838">İkinci bölüm, işlem tamamlandığında çağrılan bir geri çağırma yöntemine konur.</span><span class="sxs-lookup"><span data-stu-id="1bf58-838">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="1bf58-839">`ToListAsync` , Uzantı yönteminin zaman uyumsuz sürümüdür `ToList` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-839">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="1bf58-840">EF Core kullanan zaman uyumsuz kodu yazarken dikkat edilmesi gereken bazı şeyler:</span><span class="sxs-lookup"><span data-stu-id="1bf58-840">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="1bf58-841">Yalnızca sorguları veya komutlarının VERITABANıNA gönderilmesine neden olan deyimler zaman uyumsuz olarak yürütülür.</span><span class="sxs-lookup"><span data-stu-id="1bf58-841">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="1bf58-842">,,, `ToListAsync` `SingleOrDefaultAsync` Ve içerir `FirstOrDefaultAsync` `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-842">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="1bf58-843">Yalnızca, gibi bir değiştiren deyimler içermez `IQueryable` `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="1bf58-843">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="1bf58-844">EF Core bağlamı iş parçacığı açısından güvenli değildir: paralel olarak birden çok işlem yapmayı denemeyin.</span><span class="sxs-lookup"><span data-stu-id="1bf58-844">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="1bf58-845">Zaman uyumsuz kodun performans avantajlarından yararlanmak için, VERITABANıNA sorgu gönderen EF Core yöntemlerini çağırıyorsa kitaplık paketlerinin (örneğin, sayfalama için) zaman uyumsuz olarak kullanılacağını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="1bf58-845">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="1bf58-846">.NET 'te zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. Async [and await ile](/dotnet/csharp/programming-guide/concepts/async/)zaman uyumsuz [genel bakış](/dotnet/standard/async) ve zaman uyumsuz programlama</span><span class="sxs-lookup"><span data-stu-id="1bf58-846">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="1bf58-847">Sonraki öğreticide, temel CRUD (oluşturma, okuma, güncelleştirme, silme) işlemleri incelenir.</span><span class="sxs-lookup"><span data-stu-id="1bf58-847">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="1bf58-848">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1bf58-848">Additional resources</span></span>

* [<span data-ttu-id="1bf58-849">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="1bf58-849">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="1bf58-850">Sonraki</span><span class="sxs-lookup"><span data-stu-id="1bf58-850">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
