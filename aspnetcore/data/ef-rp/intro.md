---
title: Razor ASP.NET Core Entity Framework Core olan sayfalar-öğretici 1/8
author: rick-anderson
description: RazorEntity Framework Core kullanarak bir sayfalar uygulamasının nasıl oluşturulacağını gösterir
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
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
ms.openlocfilehash: 00d5c348b7ab06f9044e6705026e43feb7807ae3
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630320"
---
# <a name="no-locrazor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="e9ab6-103">Razor ASP.NET Core Entity Framework Core olan sayfalar-öğretici 1/8</span><span class="sxs-lookup"><span data-stu-id="e9ab6-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="e9ab6-104">, [Tom Dykstra](https://github.com/tdykstra) ve [Rick Anderson](https://twitter.com/RickAndMSFT) tarafından</span><span class="sxs-lookup"><span data-stu-id="e9ab6-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e9ab6-105">Bu, bir [ASP.NET Core Razor sayfaları](xref:razor-pages/index) UYGULAMASıNDA Entity Framework (EF) çekirdeğini nasıl kullanacağınızı gösteren bir öğretici serisinin ilkisidir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="e9ab6-106">Öğreticiler, kurgusal bir Contoso Üniversitesi için bir Web sitesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="e9ab6-107">Site, öğrenci giriş, kurs oluşturma ve eğitmen atamaları gibi işlevleri içerir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="e9ab6-108">Öğretici ilk kod yaklaşımını kullanır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="e9ab6-109">Bu öğreticiyi veritabanı ilk yaklaşımı kullanarak takip eden bilgiler için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/16897)bakın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="e9ab6-110">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="e9ab6-111">[Yönergeleri indirin](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="e9ab6-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e9ab6-112">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="e9ab6-112">Prerequisites</span></span>

* <span data-ttu-id="e9ab6-113">Sayfalarla yeni başladıysanız Razor , bunu başlatmadan önce [ Razor sayfalarla çalışmaya başlama](xref:tutorials/razor-pages/razor-pages-start) öğreticisini izleyin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9ab6-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9ab6-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e9ab6-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e9ab6-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="e9ab6-116">Veritabanı altyapıları</span><span class="sxs-lookup"><span data-stu-id="e9ab6-116">Database engines</span></span>

<span data-ttu-id="e9ab6-117">Visual Studio yönergeleri, yalnızca Windows üzerinde çalışan bir SQL Server Express sürümü olan [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)'yi kullanır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="e9ab6-118">Visual Studio Code yönergeler, platformlar arası bir veritabanı altyapısı olan [SQLite](https://www.sqlite.org/)kullanır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="e9ab6-119">SQLite kullanmayı seçerseniz, SQLite [Için DB tarayıcısı](https://sqlitebrowser.org/)gibi bir SQLite veritabanını yönetmek ve görüntülemek için üçüncü taraf bir araç indirip yükleyin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="e9ab6-120">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="e9ab6-120">Troubleshooting</span></span>

<span data-ttu-id="e9ab6-121">Giderebileceğiniz bir sorunla karşılaşırsanız, kodunuzu [Tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="e9ab6-122">Yardım almanın iyi bir yolu, [ASP.NET Core etiketi](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF Core etiketi](https://stackoverflow.com/questions/tagged/entity-framework-core)kullanılarak StackOverflow.com 'e bir soru göndererek.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="e9ab6-123">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="e9ab6-123">The sample app</span></span>

<span data-ttu-id="e9ab6-124">Bu öğreticilerde oluşturulan uygulama, temel bir üniversite web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="e9ab6-125">Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="e9ab6-126">Öğreticide oluşturulan ekranlardan bazıları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-126">Here are a few of the screens created in the tutorial.</span></span>

![Öğrenciler Dizin sayfası](intro/_static/students-index30.png)

![Öğrenciler düzenleme sayfası](intro/_static/student-edit30.png)

<span data-ttu-id="e9ab6-129">Bu sitenin kullanıcı arabirimi stili yerleşik proje şablonlarına dayalıdır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="e9ab6-130">Öğreticinin odağı, Kullanıcı arabirimini nasıl özelleştireceğinizi değil EF Core kullanma konusunda yer alır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-130">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="e9ab6-131">Tamamlanan projenin kaynak kodunu almak için sayfanın üst kısmındaki bağlantıyı izleyin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-131">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="e9ab6-132">*Cu30* klasörü, öğreticinin ASP.NET Core 3,0 sürümü için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-132">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="e9ab6-133">1-7 öğreticileri için kodun durumunu yansıtan dosyalar *cu30snapshots* klasöründe bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-133">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9ab6-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9ab6-134">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e9ab6-135">Tamamlanmış projeyi indirdikten sonra uygulamayı çalıştırmak için:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-135">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="e9ab6-136">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-136">Build the project.</span></span>
* <span data-ttu-id="e9ab6-137">Paket Yöneticisi konsolu 'nda (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-137">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="e9ab6-138">Veritabanını temel alarak projeyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-138">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e9ab6-139">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e9ab6-139">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e9ab6-140">Tamamlanmış projeyi indirdikten sonra uygulamayı çalıştırmak için:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-140">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="e9ab6-141">*Contosouniversity. csproj*öğesini silin ve *Contosoüniversıtysqlite. csproj* öğesini *contosouniversity. csproj*olarak yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-141">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="e9ab6-142">*Program.cs*' de, açıklama dışarı bu `#define Startup` şekilde `StartupSQLite` kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-142">In *Program.cs*, comment out `#define Startup` so `StartupSQLite` is used.</span></span>
* <span data-ttu-id="e9ab6-143">*appSettings.js*silin ve üzerinde *appSettings.js* *appSettingsSQLite.js* yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-143">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="e9ab6-144">*Geçişler* klasörünü silin ve *migrationssql* öğesini *geçişlerle*yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-144">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="e9ab6-145">İçin genel arama yapın `#if SQLiteVersion` ve `#if SQLiteVersion` ilgili `#endif` ifadeyi kaldırın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-145">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="e9ab6-146">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-146">Build the project.</span></span>
* <span data-ttu-id="e9ab6-147">Proje klasöründeki bir komut isteminde aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-147">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="e9ab6-148">SQLite aracında şu SQL ifadesini çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-148">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="e9ab6-149">Veritabanını temel alarak projeyi çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-149">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="e9ab6-150">Web uygulaması projesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="e9ab6-150">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9ab6-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9ab6-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e9ab6-152">Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="e9ab6-153">**ASP.NET Core Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="e9ab6-154">Projeyi *Contosouniversity*olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-154">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="e9ab6-155">Büyük harfler de dahil olmak üzere bu tam adı kullanmak önemlidir, bu nedenle kod kopyalanıp yapıştırılırken ad alanları eşleşir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-155">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="e9ab6-156">Açılan menüden **.NET Core** ve **3,0 ASP.NET Core** seçin ve ardından **Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-156">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e9ab6-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e9ab6-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e9ab6-158">Bir terminalde, proje klasörünün oluşturulması gereken klasöre gidin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-158">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="e9ab6-159">Bir Razor sayfa projesi ve yeni proje klasörü oluşturmak için aşağıdaki komutları çalıştırın `cd` :</span><span class="sxs-lookup"><span data-stu-id="e9ab6-159">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="e9ab6-160">Site stilini ayarlayın</span><span class="sxs-lookup"><span data-stu-id="e9ab6-160">Set up the site style</span></span>

<span data-ttu-id="e9ab6-161">*Sayfa/paylaşılan/_Layout. cshtml*'yi güncelleştirerek site üst bilgisini, alt bilgisini ve menüsünü ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-161">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="e9ab6-162">"ContosoUniversity" öğesinin her oluşumunu "Contoso Üniversitesi" olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-162">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="e9ab6-163">Üç oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-163">There are three occurrences.</span></span>

* <span data-ttu-id="e9ab6-164">**Giriş** ve **Gizlilik** menü girişlerini silin ve **hakkında**, **öğrenciler**, **Kurslar**, **eğitmenler**ve **Departmanlar**için girişler ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-164">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="e9ab6-165">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-165">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="e9ab6-166">*Pages/Index. cshtml*dosyasında, ASP.NET Core hakkındaki metni bu uygulamayla ilgili metinle değiştirmek için dosyanın içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-166">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="e9ab6-167">Giriş sayfasının göründüğünü doğrulamak için uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-167">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="e9ab6-168">Veri modeli</span><span class="sxs-lookup"><span data-stu-id="e9ab6-168">The data model</span></span>

<span data-ttu-id="e9ab6-169">Aşağıdaki bölümler bir veri modeli oluşturur:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-169">The following sections create a data model:</span></span>

![Kurs-kayıt-öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

<span data-ttu-id="e9ab6-171">Bir öğrenci herhangi bir sayıda kursa kaydolabilir ve bir kurs, kayıtlı sayıda öğrenciye sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-171">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="e9ab6-172">Öğrenci varlığı</span><span class="sxs-lookup"><span data-stu-id="e9ab6-172">The Student entity</span></span>

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

* <span data-ttu-id="e9ab6-174">Proje klasöründe bir *modeller* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-174">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="e9ab6-175">Aşağıdaki kodla *modeller/öğrenci. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-175">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="e9ab6-176">`ID`Özelliği, bu sınıfa karşılık gelen veritabanı tablosunun birincil anahtar sütunu olur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-176">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="e9ab6-177">Varsayılan olarak, EF Core `ID` birincil anahtar olarak adlandırılan bir özelliği Yorumlar `classnameID` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-177">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="e9ab6-178">Bu nedenle birincil anahtar sınıfı için alternatif otomatik olarak tanınan ad `Student` olur `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-178">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="e9ab6-179">Daha fazla bilgi için bkz. [EF Core-anahtarlar](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="e9ab6-179">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="e9ab6-180">`Enrollments`Özelliği bir [Gezinti özelliğidir](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="e9ab6-180">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="e9ab6-181">Gezinti özellikleri, bu varlıkla ilgili diğer varlıkları tutar.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-181">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="e9ab6-182">Bu durumda, `Enrollments` bir `Student` varlığın özelliği `Enrollment` söz konusu öğrenciye ilişkin tüm varlıkları barındırır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-182">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="e9ab6-183">Örneğin, veritabanındaki bir öğrenci satırında iki ilişkili kayıt satırı varsa, `Enrollments` gezinti özelliği bu Iki kayıt varlığını içerir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-183">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="e9ab6-184">Veritabanında, bir kayıt satırı, Studentitıd sütunu öğrencinin ID değerini içeriyorsa bir öğrenci satırıyla ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-184">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="e9ab6-185">Örneğin, bir öğrenci satırının ID = 1 olduğunu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-185">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="e9ab6-186">İlgili kayıt satırları Studentitıd = 1 olacaktır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-186">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="e9ab6-187">Studentitıd, kayıt tablosundaki bir *yabancı anahtardır* .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-187">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="e9ab6-188">`Enrollments`Özelliği, `ICollection<Enrollment>` birden çok ilgili kayıt varlığı olabileceğinden, olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-188">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="e9ab6-189">Veya gibi diğer koleksiyon türlerini kullanabilirsiniz `List<Enrollment>` `HashSet<Enrollment>` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-189">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="e9ab6-190">Kullanıldığında `ICollection<Enrollment>` , EF Core `HashSet<Enrollment>` Varsayılan olarak bir koleksiyon oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-190">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="e9ab6-191">Kayıt varlığı</span><span class="sxs-lookup"><span data-stu-id="e9ab6-191">The Enrollment entity</span></span>

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

<span data-ttu-id="e9ab6-193">Aşağıdaki kodla *modeller/kayıt. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-193">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="e9ab6-194">`EnrollmentID`Bu özellik birincil anahtardır; bu varlık kendi başına değil, `classnameID` kendi modelini kullanır `ID` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-194">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="e9ab6-195">Bir üretim veri modeli için bir model seçin ve bunu tutarlı bir şekilde kullanın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-195">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="e9ab6-196">Bu öğretici her ikisinin de yalnızca bir iş olduğunu göstermek için kullanır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-196">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="e9ab6-197">`ID`Olmadan kullanmak `classname` , bazı veri modeli değişikliklerinin uygulanmasını kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-197">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="e9ab6-198">`Grade`Özelliği bir `enum` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-198">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="e9ab6-199">Tür bildiriminden sonraki soru işareti, `Grade` `Grade` özelliğin [null yapılabilir](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/)olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-199">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="e9ab6-200">Null olan bir sınıf sıfır bir sınıf null değerinden farklıdır &mdash; veya henüz atanmamış olur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-200">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="e9ab6-201">`StudentID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Student` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-201">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="e9ab6-202">Bir varlık `Enrollment` bir `Student` varlıkla ilişkilendirilir, bu nedenle özellik tek bir varlık içerir `Student` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-202">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="e9ab6-203">`CourseID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Course` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-203">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="e9ab6-204">Bir `Enrollment` varlık bir `Course` varlıkla ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-204">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="e9ab6-205">EF Core bir özelliği, adlandırılmış ise yabancı anahtar olarak yorumlar `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-205">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="e9ab6-206">Örneğin, `StudentID` `Student` `Student` nesnenin birincil anahtarı olduğundan, gezinti özelliği için yabancı anahtardır `ID` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-206">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="e9ab6-207">Yabancı anahtar özellikleri de adlandırılmış olabilir `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-207">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="e9ab6-208">Örneğin, `CourseID` `Course` varlığın birincil anahtarı olduğundan `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-208">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="e9ab6-209">Kurs varlığı</span><span class="sxs-lookup"><span data-stu-id="e9ab6-209">The Course entity</span></span>

![Kurs varlık diyagramı](intro/_static/course-entity.png)

<span data-ttu-id="e9ab6-211">Aşağıdaki kodla *modeller/kurs. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-211">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="e9ab6-212">`Enrollments`Özelliği bir gezinti özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-212">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="e9ab6-213">Bir `Course` varlık herhangi bir sayıda varlıkla ilişkili olabilir `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-213">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="e9ab6-214">`DatabaseGenerated`Özniteliği, uygulamanın veritabanını oluşturmak yerine birincil anahtarı belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-214">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="e9ab6-215">Derleyici hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-215">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="e9ab6-216">Yapı iskelesi öğrenci sayfaları</span><span class="sxs-lookup"><span data-stu-id="e9ab6-216">Scaffold Student pages</span></span>

<span data-ttu-id="e9ab6-217">Bu bölümde, oluşturmak için ASP.NET Core scafkatlama aracını kullanırsınız:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-217">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="e9ab6-218">EF Core *bağlamı* sınıfı.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-218">An EF Core *context* class.</span></span> <span data-ttu-id="e9ab6-219">Bağlam, belirli bir veri modeli için Entity Framework işlevselliği koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-219">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="e9ab6-220">`Microsoft.EntityFrameworkCore.DbContext`Sınıfından türetilir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-220">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="e9ab6-221">Razor varlık için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemlerini işleyen sayfalar `Student` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-221">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9ab6-222">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9ab6-222">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e9ab6-223">*Sayfalar* klasöründe bir *öğrenciler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-223">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="e9ab6-224">**Çözüm Gezgini**, *Sayfalar/öğrenciler* klasörüne sağ tıklayın ve **Add** > **yeni yapı iskelesi Ekle öğesini**seçin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-224">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="e9ab6-225">**Yapı iskelesi Ekle** iletişim kutusunda \*\* Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar\*\* ' ı seçin > **ADD**.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-225">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="e9ab6-226">\*\* Razor Entity Framework (CRUD) kullanarak sayfa ekle\*\* iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-226">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="e9ab6-227">**Model sınıfı** açılır penceresinde **öğrenci (Contosouniversity. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-227">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="e9ab6-228">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-228">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="e9ab6-229">*Contosouniversity. modeller. Contosoüniversıtycontext* olan veri bağlamı adını *Contosouniversity. Data. SchoolContext*olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-229">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="e9ab6-230">**Ekle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-230">Select **Add**.</span></span>

<span data-ttu-id="e9ab6-231">Aşağıdaki paketler otomatik olarak yüklenir:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-231">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="e9ab6-232">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e9ab6-232">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e9ab6-233">Gerekli NuGet paketlerini yüklemek için aşağıdaki .NET Core CLI komutlarını çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-233">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="e9ab6-234">Yapı iskelesi için Microsoft. VisualStudio. Web. CodeGeneration. Design paketi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-234">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="e9ab6-235">Uygulama SQL Server kullanmayabilse de, scafkatlama aracı SQL Server paketine ihtiyaç duyuyor.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-235">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="e9ab6-236">*Sayfalar/öğrenciler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-236">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="e9ab6-237">[ASPNET-CodeGenerator scafkatlama aracı](xref:fundamentals/tools/dotnet-aspnet-codegenerator)'nı yüklemek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-237">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="e9ab6-238">Fkatlama öğrenci sayfalarını iskele almak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-238">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="e9ab6-239">**Windows üzerinde**</span><span class="sxs-lookup"><span data-stu-id="e9ab6-239">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="e9ab6-240">**MacOS veya Linux üzerinde**</span><span class="sxs-lookup"><span data-stu-id="e9ab6-240">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="e9ab6-241">Önceki adımla ilgili bir sorununuz varsa, projeyi derleyin ve yapı iskelesi adımını yeniden deneyin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-241">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="e9ab6-242">Yapı iskelesi işlemi:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-242">The scaffolding process:</span></span>

* <span data-ttu-id="e9ab6-243">Razor *Sayfalar/öğrenciler* klasöründe sayfa oluşturur:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-243">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="e9ab6-244">*. Cshtml* ve *Create.cshtml.cs* oluşturma</span><span class="sxs-lookup"><span data-stu-id="e9ab6-244">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="e9ab6-245">*Delete. cshtml* ve *delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="e9ab6-245">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="e9ab6-246">*Details. cshtml* ve *details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="e9ab6-246">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="e9ab6-247">*. Cshtml* ve *Edit.cshtml.cs* Düzenle</span><span class="sxs-lookup"><span data-stu-id="e9ab6-247">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="e9ab6-248">*Index. cshtml* ve *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="e9ab6-248">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="e9ab6-249">*Data/SchoolContext. cs*oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-249">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="e9ab6-250">*Startup.cs*içinde bağımlılık eklenmesine bağlam ekler.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-250">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="e9ab6-251">*appsettings.js*için bir veritabanı bağlantı dizesi ekler.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-251">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="e9ab6-252">Veritabanı bağlantı dizesi</span><span class="sxs-lookup"><span data-stu-id="e9ab6-252">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9ab6-253">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9ab6-253">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e9ab6-254">Bağlantı dizesi [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)belirtir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-254">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="e9ab6-255">LocalDB, SQL Server Express veritabanı altyapısının hafif bir sürümüdür ve üretim kullanımı için değil uygulama geliştirmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-255">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="e9ab6-256">Varsayılan olarak, LocalDB dizinde *. mdf* dosyaları oluşturur `C:/Users/<user>` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-256">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e9ab6-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e9ab6-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e9ab6-258">Bağlantı dizesini *cu. db*adlı bir SQLite veritabanı dosyasını işaret etmek üzere değiştirin:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-258">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="e9ab6-259">Veritabanı bağlam sınıfını Güncelleştir</span><span class="sxs-lookup"><span data-stu-id="e9ab6-259">Update the database context class</span></span>

<span data-ttu-id="e9ab6-260">Belirli bir veri modeli için EF Core işlevselliğini koordine eden ana sınıf veritabanı bağlamı sınıfıdır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-260">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="e9ab6-261">Bağlam [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-261">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="e9ab6-262">Bağlam, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-262">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="e9ab6-263">Bu projede, sınıfı olarak adlandırılır `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-263">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="e9ab6-264">Aşağıdaki kodla *SchoolContext.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-264">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="e9ab6-265">Vurgulanan kod, her bir varlık kümesi için bir [Dbset \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-265">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="e9ab6-266">EF Core terminoloji:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-266">In EF Core terminology:</span></span>

* <span data-ttu-id="e9ab6-267">Bir varlık kümesi, genellikle bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-267">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="e9ab6-268">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-268">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e9ab6-269">Bir varlık kümesi birden çok varlık içerdiğinden, DBSet özellikleri çoğul adlar olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-269">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="e9ab6-270">Yapı iskelesi aracı bir `Student` dbset oluşturduğundan, bu adım çoğul olarak değişir `Students` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-270">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="e9ab6-271">RazorSayfa kodunun yeni DBSet adıyla eşleşmesini sağlamak için, tüm projesi genelinde küresel bir değişiklik yapın `_context.Student` `_context.Students` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-271">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="e9ab6-272">8 oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-272">There are 8 occurrences.</span></span>

<span data-ttu-id="e9ab6-273">Derleyici hatası olmadığını doğrulamak için projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-273">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="e9ab6-274">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="e9ab6-274">Startup.cs</span></span>

<span data-ttu-id="e9ab6-275">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-275">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e9ab6-276">Hizmetler (EF Core veritabanı bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-276">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e9ab6-277">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-277">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e9ab6-278">Bir veritabanı bağlamı örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-278">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e9ab6-279">Scafkatlama Aracı, bağlam sınıfını bağımlılık ekleme kapsayıcısına otomatik olarak kaydetti.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-279">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9ab6-280">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9ab6-280">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e9ab6-281">`ConfigureServices`' De, vurgulanan satırlar scaffolder tarafından eklenmiştir:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-281">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e9ab6-282">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e9ab6-282">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e9ab6-283">' De `ConfigureServices` , desteği tarafından eklenen kodun çağrılarından emin olun `UseSqlite` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-283">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="e9ab6-284">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-284">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="e9ab6-285">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki *appsettings.js* bağlantı dizesini okur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-285">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="e9ab6-286">Veritabanını oluşturma</span><span class="sxs-lookup"><span data-stu-id="e9ab6-286">Create the database</span></span>

<span data-ttu-id="e9ab6-287">Mevcut değilse veritabanını oluşturmak için *program.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-287">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="e9ab6-288">Bağlam için bir veritabanı varsa, [Ensuyeniden](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) oluşturma yöntemi hiçbir eylemde bulunmaz.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-288">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="e9ab6-289">Veritabanı yoksa, veritabanını ve şemayı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-289">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="e9ab6-290">`EnsureCreated` veri modeli değişikliklerini işlemek için aşağıdaki iş akışını sunar:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-290">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="e9ab6-291">Veritabanını silin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-291">Delete the database.</span></span> <span data-ttu-id="e9ab6-292">Mevcut veriler kaybolur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-292">Any existing data is lost.</span></span>
* <span data-ttu-id="e9ab6-293">Veri modelini değiştirin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-293">Change the data model.</span></span> <span data-ttu-id="e9ab6-294">Örneğin, bir alan ekleyin `EmailAddress` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-294">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="e9ab6-295">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-295">Run the app.</span></span>
* <span data-ttu-id="e9ab6-296">`EnsureCreated` yeni şemaya sahip bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-296">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="e9ab6-297">Bu iş akışı, verileri korumanıza gerek olmadığı sürece, şema hızlı bir şekilde gelişen zaman geliştirme aşamasında iyi bir şekilde gerçekleştirilir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-297">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="e9ab6-298">Veritabanına girilen verilerin korunması gerektiğinde bu durum farklıdır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-298">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="e9ab6-299">Bu durumda, geçişleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-299">When that is the case, use migrations.</span></span>

<span data-ttu-id="e9ab6-300">Öğretici serisinde daha sonra tarafından oluşturulan veritabanını siler `EnsureCreated` ve bunun yerine geçişleri kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-300">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="e9ab6-301">Tarafından oluşturulan bir veritabanı, `EnsureCreated` geçişler kullanılarak güncelleştirilemiyor.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-301">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="e9ab6-302">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="e9ab6-302">Test the app</span></span>

* <span data-ttu-id="e9ab6-303">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-303">Run the app.</span></span>
* <span data-ttu-id="e9ab6-304">**Öğrenciler** bağlantısını seçin ve ardından **Yeni oluştur**.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-304">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="e9ab6-305">Düzenle, Ayrıntılar ve Sil bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-305">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="e9ab6-306">Veritabanını çekirdek</span><span class="sxs-lookup"><span data-stu-id="e9ab6-306">Seed the database</span></span>

<span data-ttu-id="e9ab6-307">`EnsureCreated`Yöntemi boş bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-307">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="e9ab6-308">Bu bölüm, veritabanını test verileriyle dolduran kodu ekler.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-308">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="e9ab6-309">Aşağıdaki kodla *veri/Dbınizer. cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-309">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="e9ab6-310">Kod, veritabanında herhangi bir öğrenci olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-310">The code checks if there are any students in the database.</span></span> <span data-ttu-id="e9ab6-311">Öğrenci yoksa, veritabanına test verileri ekler.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-311">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="e9ab6-312">Performansı iyileştirmek için Koleksiyonlar yerine diziler halinde test verileri oluşturur `List<T>` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-312">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="e9ab6-313">*Program.cs*içinde, `EnsureCreated` çağrıyı bir çağrı ile değiştirin `DbInitializer.Initialize` :</span><span class="sxs-lookup"><span data-stu-id="e9ab6-313">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="e9ab6-314">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9ab6-314">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e9ab6-315">Çalışıyorsa uygulamayı durdurun ve **Paket Yöneticisi konsolunda** (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-315">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="e9ab6-316">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e9ab6-316">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e9ab6-317">Çalışıyorsa uygulamayı durdurun ve *cu. db* dosyasını silin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-317">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="e9ab6-318">Uygulamayı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-318">Restart the app.</span></span>

* <span data-ttu-id="e9ab6-319">Sağlanan verileri görmek için öğrenciler sayfasını seçin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-319">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="e9ab6-320">Veritabanını görüntüleme</span><span class="sxs-lookup"><span data-stu-id="e9ab6-320">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9ab6-321">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9ab6-321">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e9ab6-322">Visual Studio 'daki **Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-322">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="e9ab6-323">SSOX 'te, **(LocalDB) \MSSQLLocalDB > veritabanları > SchoolContext-{GUID}** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-323">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="e9ab6-324">Veritabanı adı, daha önce belirttiğiniz bağlam adından ve bir tire ve bir GUID ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-324">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="e9ab6-325">**Tables** düğümünü genişletin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-325">Expand the **Tables** node.</span></span>
* <span data-ttu-id="e9ab6-326">Oluşturulan sütunları ve tabloya yerleştirilen satırları görmek için **öğrenci** tablosuna sağ tıklayın ve **verileri görüntüle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-326">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="e9ab6-327">Modelin tablo şemasına nasıl eşlendiğini görmek için **öğrenci** tablosuna sağ tıklayın ve **kodu görüntüle** ' ye tıklayın `Student` `Student` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-327">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e9ab6-328">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e9ab6-328">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e9ab6-329">Veritabanı şemasını ve sağlanan verileri görüntülemek için SQLite aracınızı kullanın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-329">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="e9ab6-330">Veritabanı dosyası *cu. db* olarak adlandırılır ve proje klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-330">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="e9ab6-331">Zaman uyumsuz kod</span><span class="sxs-lookup"><span data-stu-id="e9ab6-331">Asynchronous code</span></span>

<span data-ttu-id="e9ab6-332">Zaman uyumsuz programlama, ASP.NET Core ve EF Core için varsayılan moddur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-332">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="e9ab6-333">Web sunucusunda sınırlı sayıda iş parçacığı bulunur ve yüksek yük durumlarında tüm kullanılabilir iş parçacıkları kullanımda olabilir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-333">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="e9ab6-334">Bu durumda, sunucu, iş parçacıkları boşaltılana kadar yeni istekleri işleyemez.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-334">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="e9ab6-335">Zaman uyumlu kodla, çok sayıda iş parçacığı, g/ç 'nin tamamlanmasını beklediği için aslında herhangi bir iş yapmadıklarında bağlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-335">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="e9ab6-336">Zaman uyumsuz kod ile, bir işlem g/ç 'yi tamamlanmayı beklerken, sunucunun diğer istekleri işlemek için kullanması için iş parçacığı serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-336">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="e9ab6-337">Sonuç olarak, zaman uyumsuz kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucu gecikmeksizin daha fazla trafiği işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-337">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="e9ab6-338">Zaman uyumsuz kod, çalışma zamanında az miktarda yük getirir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-338">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="e9ab6-339">Düşük trafik durumlarında, performans artışı göz ardı edilebilir, ancak yüksek trafik durumları için olası performans iyileştirmesi oldukça önemlidir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-339">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="e9ab6-340">Aşağıdaki kodda, [Async](/dotnet/csharp/language-reference/keywords/async) anahtar sözcüğü, `Task<T>` return değeri, `await` anahtar sözcüğü ve `ToListAsync` yöntemi kodu zaman uyumsuz olarak yürütür.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-340">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="e9ab6-341">`async`Anahtar sözcüğü derleyiciye şunu söyler:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-341">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="e9ab6-342">Yöntem gövdesinin parçaları için geri çağrılar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-342">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="e9ab6-343">Döndürülen [görev](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) nesnesini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-343">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="e9ab6-344">`Task<T>`Dönüş türü, devam eden işi temsil eder.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-344">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="e9ab6-345">`await`Anahtar sözcüğü, derleyicinin yöntemi iki parçaya böetmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-345">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="e9ab6-346">İlk bölüm, zaman uyumsuz olarak başlatılan işlemle biter.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-346">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="e9ab6-347">İkinci bölüm, işlem tamamlandığında çağrılan bir geri çağırma yöntemine konur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-347">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="e9ab6-348">`ToListAsync` , Uzantı yönteminin zaman uyumsuz sürümüdür `ToList` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-348">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="e9ab6-349">EF Core kullanan zaman uyumsuz kodu yazarken dikkat edilmesi gereken bazı şeyler:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-349">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="e9ab6-350">Yalnızca sorguları veya komutlarının veritabanına gönderilmesine neden olan deyimler zaman uyumsuz olarak yürütülür.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-350">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="e9ab6-351">,, `ToListAsync` , `SingleOrDefaultAsync` `FirstOrDefaultAsync` Ve içerir `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-351">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="e9ab6-352">Yalnızca, gibi bir değiştiren deyimler içermez `IQueryable` `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-352">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="e9ab6-353">EF Core bağlamı iş parçacığı açısından güvenli değildir: paralel olarak birden çok işlem yapmayı denemeyin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-353">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="e9ab6-354">Zaman uyumsuz kodun performans avantajlarından yararlanmak için, veritabanına sorgu gönderen EF Core yöntemleri çağırıyorsa kitaplık paketlerinin (örneğin, sayfalama için) zaman uyumsuz olarak kullanılacağını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-354">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="e9ab6-355">.NET 'te zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. Async [and await ile](/dotnet/csharp/programming-guide/concepts/async/)zaman uyumsuz [genel bakış](/dotnet/standard/async) ve zaman uyumsuz programlama</span><span class="sxs-lookup"><span data-stu-id="e9ab6-355">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="e9ab6-356">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="e9ab6-356">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="e9ab6-357">Sonraki öğretici</span><span class="sxs-lookup"><span data-stu-id="e9ab6-357">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e9ab6-358">Contoso Üniversitesi örnek Web uygulaması, Razor Entity Framework (EF) Core kullanarak ASP.NET Core sayfalı bir uygulamanın nasıl oluşturulacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-358">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="e9ab6-359">Örnek uygulama, kurgusal bir Contoso Üniversitesi için bir Web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-359">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="e9ab6-360">Öğrenci giriş, kurs oluşturma ve eğitmen atamaları gibi işlevleri içerir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-360">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="e9ab6-361">Bu sayfa, Contoso Üniversitesi örnek uygulamasının nasıl oluşturulacağını açıklayan bir öğretici serisinin ilkisidir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-361">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="e9ab6-362">Tamamlanmış uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-362">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="e9ab6-363">[Yönergeleri indirin](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="e9ab6-363">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e9ab6-364">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="e9ab6-364">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9ab6-365">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9ab6-365">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="e9ab6-366">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e9ab6-366">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="e9ab6-367">[ Razor Sayfalarla](xref:razor-pages/index)benzerlik.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-367">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="e9ab6-368">Yeni programcılar, bu seriyi başlatmadan önce [sayfalarla çalışmaya başlama Razor sayfasını](xref:tutorials/razor-pages/razor-pages-start) tamamlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-368">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="e9ab6-369">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="e9ab6-369">Troubleshooting</span></span>

<span data-ttu-id="e9ab6-370">Çözemiyoruz bir sorunla karşılaşırsanız, kodunuzun [Tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)karşılaştırılmasıyla genellikle çözümü bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-370">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="e9ab6-371">[ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) [için bir](https://stackoverflow.com/questions/tagged/asp.net-core) soru göndererek yardım almanın iyi bir yolu.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-371">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="e9ab6-372">Contoso Üniversitesi web uygulaması</span><span class="sxs-lookup"><span data-stu-id="e9ab6-372">The Contoso University web app</span></span>

<span data-ttu-id="e9ab6-373">Bu öğreticilerde oluşturulan uygulama, temel bir üniversite web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-373">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="e9ab6-374">Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-374">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="e9ab6-375">Öğreticide oluşturulan ekranlardan bazıları aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-375">Here are a few of the screens created in the tutorial.</span></span>

![Öğrenciler Dizin sayfası](intro/_static/students-index.png)

![Öğrenciler düzenleme sayfası](intro/_static/student-edit.png)

<span data-ttu-id="e9ab6-378">Bu sitenin kullanıcı arabirimi stili yerleşik şablonlar tarafından üretilme kadar yakın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-378">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="e9ab6-379">Eğitim odağı, Razor kullanıcı arabiriminden değil, sayfalarla EF Core.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-379">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-no-locrazor-pages-web-app"></a><span data-ttu-id="e9ab6-380">Contosoüniversitesi Razor sayfaları Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="e9ab6-380">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9ab6-381">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9ab6-381">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e9ab6-382">Visual Studio **Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-382">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="e9ab6-383">Yeni bir ASP.NET Core Web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-383">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="e9ab6-384">Projeyi **Contosouniversity**olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-384">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="e9ab6-385">Kod kopyalama/yapıştırma olduğunda, ad alanlarının eşleşmesi için *Contosouniversity* projesini adlandırmak önemlidir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-385">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="e9ab6-386">Açılan listede **ASP.NET Core 2,1** ' i seçin ve ardından **Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-386">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="e9ab6-387">Yukarıdaki adımların görüntüleri için bkz. [ Razor Web uygulaması oluşturma](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="e9ab6-387">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="e9ab6-388">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-388">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e9ab6-389">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e9ab6-389">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="e9ab6-390">Site stilini ayarlayın</span><span class="sxs-lookup"><span data-stu-id="e9ab6-390">Set up the site style</span></span>

<span data-ttu-id="e9ab6-391">Site menüsünü, düzeni ve giriş sayfasını birkaç değişiklik ayarlar.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-391">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="e9ab6-392">*Sayfaları/paylaşılan/_Layout. cshtml* 'yi aşağıdaki değişikliklerle güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-392">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="e9ab6-393">"ContosoUniversity" öğesinin her oluşumunu "Contoso Üniversitesi" olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-393">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="e9ab6-394">Üç oluşum vardır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-394">There are three occurrences.</span></span>

* <span data-ttu-id="e9ab6-395">**Öğrenciler**, **Kurslar**, **eğitmenler**ve **Departmanlar**için menü girişleri ekleyin ve **kişi** menü girişini silin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-395">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="e9ab6-396">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-396">The changes are highlighted.</span></span> <span data-ttu-id="e9ab6-397">(Tüm *biçimlendirme gösterilmez.* )</span><span class="sxs-lookup"><span data-stu-id="e9ab6-397">(All the markup is *not* displayed.)</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="e9ab6-398">*Pages/Index. cshtml*dosyasında, ASP.net ve MVC hakkındaki metni bu uygulamayla ilgili metinle değiştirmek için dosyanın içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-398">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-cshtml[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="e9ab6-399">Veri modelini oluşturma</span><span class="sxs-lookup"><span data-stu-id="e9ab6-399">Create the data model</span></span>

<span data-ttu-id="e9ab6-400">Contoso Üniversitesi uygulaması için varlık sınıfları oluşturma.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-400">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="e9ab6-401">Aşağıdaki üç varlıkla başlayın:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-401">Start with the following three entities:</span></span>

![Kurs-kayıt-öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

<span data-ttu-id="e9ab6-403">Ve varlıkları arasında bire çok ilişki vardır `Student` `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-403">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="e9ab6-404">Ve varlıkları arasında bire çok ilişki vardır `Course` `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-404">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="e9ab6-405">Bir öğrenci herhangi bir sayıda kursa kaydolabilir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-405">A student can enroll in any number of courses.</span></span> <span data-ttu-id="e9ab6-406">Bir kurs, kayıtlı sayıda öğrenciye sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-406">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="e9ab6-407">Aşağıdaki bölümlerde, bu varlıkların her biri için bir sınıf oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-407">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="e9ab6-408">Öğrenci varlığı</span><span class="sxs-lookup"><span data-stu-id="e9ab6-408">The Student entity</span></span>

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

<span data-ttu-id="e9ab6-410">*Modeller* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-410">Create a *Models* folder.</span></span> <span data-ttu-id="e9ab6-411">*Modeller* klasöründe, *Student.cs* adlı bir sınıf dosyasını aşağıdaki kodla oluşturun:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-411">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="e9ab6-412">`ID`Özelliği, bu sınıfa karşılık gelen veritabanı (DB) tablosunun birincil anahtar sütunu olur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-412">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="e9ab6-413">Varsayılan olarak, EF Core `ID` birincil anahtar olarak adlandırılan bir özelliği Yorumlar `classnameID` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-413">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="e9ab6-414">`classnameID`' De, `classname` sınıfının adıdır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-414">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="e9ab6-415">Diğer otomatik olarak tanınan birincil anahtar, `StudentID` Önceki örnekte yer verilir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-415">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="e9ab6-416">`Enrollments`Özelliği bir [Gezinti özelliğidir](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="e9ab6-416">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="e9ab6-417">Gezinti özellikleri bu varlıkla ilgili diğer varlıkların bağlantısını sağlar.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-417">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="e9ab6-418">Bu durumda, `Enrollments` öğesinin özelliği ile `Student entity` `Enrollment` ilgili tüm varlıkları barındırır `Student` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-418">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="e9ab6-419">Örneğin, VERITABANıNDAKI bir öğrenci satırında iki ilişkili kayıt satırı varsa, `Enrollments` gezinti özelliği bu iki `Enrollment` varlığı içerir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-419">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="e9ab6-420">İlgili `Enrollment` satır, söz konusu öğrencinin birincil anahtar değerini sütununda içeren bir satırdır `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-420">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="e9ab6-421">Örneğin, ID = 1 olan öğrencinin tabloda iki satır olduğunu varsayalım `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-421">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="e9ab6-422">`Enrollment`Tabloda = 1 olan iki satır vardır `StudentID` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-422">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="e9ab6-423">`StudentID` , tablodaki `Enrollment` öğrencisi belirten bir yabancı anahtardır `Student` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-423">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="e9ab6-424">Bir gezinti özelliği birden çok varlık tutabileceğinden, gezinti özelliği gibi bir liste türü olmalıdır `ICollection<T>` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-424">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="e9ab6-425">`ICollection<T>` belirtilebilir veya veya gibi bir tür olabilir `List<T>` `HashSet<T>` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-425">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="e9ab6-426">Kullanıldığında `ICollection<T>` , EF Core `HashSet<T>` Varsayılan olarak bir koleksiyon oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-426">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="e9ab6-427">Birden çok varlığı tutan gezinti özellikleri, çoktan çoğa ve bire çok ilişkilerden gelir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-427">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="e9ab6-428">Kayıt varlığı</span><span class="sxs-lookup"><span data-stu-id="e9ab6-428">The Enrollment entity</span></span>

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

<span data-ttu-id="e9ab6-430">*Modeller* klasöründe, aşağıdaki kodla *enrollment.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-430">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="e9ab6-431">`EnrollmentID`Özelliği birincil anahtardır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-431">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="e9ab6-432">Bu varlık `classnameID` `ID` , varlığı beğenmek yerine, modelini kullanır `Student` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-432">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="e9ab6-433">Genellikle geliştiriciler bir model seçer ve bunu veri modeli boyunca kullanır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-433">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="e9ab6-434">Daha sonraki bir öğreticide, veri modelinde devralmayı daha kolay hale getirmek için ClassName olmadan ID kullanımı gösterilmemiştir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-434">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="e9ab6-435">`Grade`Özelliği bir `enum` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-435">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="e9ab6-436">Tür bildiriminden sonraki soru işareti, `Grade` `Grade` özelliğin null yapılabilir olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-436">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="e9ab6-437">Null olan bir sınıf sıfır bir sınıf ile farklıdır--null, henüz bir sınıf bilinmediğini veya henüz atanmadığını belirtir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-437">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="e9ab6-438">`StudentID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Student` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-438">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="e9ab6-439">Bir varlık `Enrollment` bir `Student` varlıkla ilişkilendirilir, bu nedenle özellik tek bir varlık içerir `Student` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-439">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="e9ab6-440">`Student`Varlık, `Student.Enrollments` birden çok varlık içeren gezinti özelliğinden farklıdır `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-440">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="e9ab6-441">`CourseID`Özelliği bir yabancı anahtardır ve karşılık gelen gezinti özelliği ' dir `Course` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-441">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="e9ab6-442">Bir `Enrollment` varlık bir `Course` varlıkla ilişkilendirilir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-442">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="e9ab6-443">EF Core bir özelliği, adlandırılmış ise yabancı anahtar olarak yorumlar `<navigation property name><primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-443">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="e9ab6-444">Örneğin, `StudentID` `Student` gezinti özelliği için `Student` varlığın birincil anahtarı olduğundan `ID` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-444">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="e9ab6-445">Yabancı anahtar özellikleri de adlandırılmış olabilir `<primary key property name>` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-445">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="e9ab6-446">Örneğin, `CourseID` `Course` varlığın birincil anahtarı olduğundan `CourseID` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-446">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="e9ab6-447">Kurs varlığı</span><span class="sxs-lookup"><span data-stu-id="e9ab6-447">The Course entity</span></span>

![Kurs varlık diyagramı](intro/_static/course-entity.png)

<span data-ttu-id="e9ab6-449">*Modeller* klasöründe, aşağıdaki kodla *Course.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-449">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="e9ab6-450">`Enrollments`Özelliği bir gezinti özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-450">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="e9ab6-451">Bir `Course` varlık herhangi bir sayıda varlıkla ilişkili olabilir `Enrollment` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-451">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="e9ab6-452">`DatabaseGenerated`Özniteliği, UYGULAMANıN DB oluşturmak yerine birincil anahtarı belirtmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-452">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="e9ab6-453">Öğrenci modelini dolandırın</span><span class="sxs-lookup"><span data-stu-id="e9ab6-453">Scaffold the student model</span></span>

<span data-ttu-id="e9ab6-454">Bu bölümde öğrenci modeli scafkatdır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-454">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="e9ab6-455">Diğer bir deyişle, scafkatlama aracı öğrenci modeli için oluşturma, okuma, güncelleştirme ve silme (CRUD) işlemleri için sayfalar üretir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-455">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="e9ab6-456">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-456">Build the project.</span></span>
* <span data-ttu-id="e9ab6-457">*Sayfalar/öğrenciler* klasörünü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-457">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="e9ab6-458">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9ab6-458">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="e9ab6-459">**Çözüm Gezgini**, *Sayfalar/öğrenciler* klasörüne sağ tıklayarak **Add** > **yeni yapı iskelesi öğesi**ekleyin >.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-459">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="e9ab6-460">**Yapı iskelesi Ekle** iletişim kutusunda \*\* Razor Entity Framework (CRUD) Ekle öğesini kullanarak sayfalar\*\* ' ı seçin > **ADD**.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-460">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="e9ab6-461">\*\* Razor Entity Framework (CRUD) kullanarak sayfa ekle\*\* iletişim kutusunu doldurun:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-461">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="e9ab6-462">**Model sınıfı** açılır penceresinde **öğrenci (Contosouniversity. modeller)** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-462">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="e9ab6-463">**Veri bağlamı sınıfı** satırında, **+** (artı) işaretini seçin ve üretilen adı **Contosouniversity. modeller. SchoolContext**olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-463">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="e9ab6-464">**Veri bağlamı sınıfı** açılır penceresinde **Contosouniversity. modeller. SchoolContext** öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-464">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="e9ab6-465">**Ekle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-465">Select **Add**.</span></span>

![CRUD iletişim kutusu](intro/_static/s1.png)

<span data-ttu-id="e9ab6-467">Önceki adımla ilgili bir sorununuz varsa [Film modeli](xref:tutorials/razor-pages/model#scaffold-the-movie-model) ' ne bakın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-467">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="e9ab6-468">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e9ab6-468">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="e9ab6-469">Öğrenci modelini iskele almak için aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-469">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="e9ab6-470">Yapı iskelesi işlemi oluşturulur ve aşağıdaki dosyaları değiştirdi:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-470">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="e9ab6-471">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="e9ab6-471">Files created</span></span>

* <span data-ttu-id="e9ab6-472">*Sayfalar/öğrenciler* Oluşturma, silme, ayrıntılar, düzenleme, dizin oluşturma.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-472">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="e9ab6-473">*Data/SchoolContext. cs*</span><span class="sxs-lookup"><span data-stu-id="e9ab6-473">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="e9ab6-474">Dosya güncelleştirmeleri</span><span class="sxs-lookup"><span data-stu-id="e9ab6-474">File updates</span></span>

* <span data-ttu-id="e9ab6-475">*Startup.cs* : Bu dosyadaki değişiklikler sonraki bölümde ayrıntılıdır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-475">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="e9ab6-476">*appsettings.js* : yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesi eklenir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-476">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="e9ab6-477">Bağımlılık ekleme ile kaydedilen bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="e9ab6-477">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="e9ab6-478">ASP.NET Core [bağımlılık ekleme](xref:fundamentals/dependency-injection)ile oluşturulmuştur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-478">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="e9ab6-479">Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık ekleme ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-479">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="e9ab6-480">Bu hizmetleri gerektiren bileşenler (örneğin, Razor Sayfalar) bu hizmetleri Oluşturucu parametreleri aracılığıyla sağlamaktadır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-480">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="e9ab6-481">Bir DB bağlam örneğini alan Oluşturucu kodu öğreticide daha sonra gösterilmiştir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-481">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="e9ab6-482">Scafkatlama aracı otomatik olarak bir DB bağlamı oluşturup bağımlılık ekleme kapsayıcısına kaydettirdi.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-482">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="e9ab6-483">`ConfigureServices` *Startup.cs*içindeki yöntemi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-483">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="e9ab6-484">Vurgulanan satır, scaffolder tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-484">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="e9ab6-485">Bağlantı dizesinin adı, [Dbcontextoptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesinde bir yöntem çağırarak bağlama geçirilir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-485">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="e9ab6-486">Yerel geliştirme için [ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) dosyadaki *appsettings.js* bağlantı dizesini okur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-486">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="e9ab6-487">Ana güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="e9ab6-487">Update main</span></span>

<span data-ttu-id="e9ab6-488">*Program.cs*' de, `Main` aşağıdakileri yapmak için yöntemini değiştirin:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-488">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="e9ab6-489">Bağımlılık ekleme kapsayıcısından bir DB bağlam örneği alın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-489">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="e9ab6-490">Yeniden  [oluşturulmasını](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)çağırın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-490">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="e9ab6-491">`EnsureCreated`Yöntem tamamlandığında bağlamı atın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-491">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="e9ab6-492">Aşağıdaki kod güncelleştirilmiş *program.cs* dosyasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-492">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="e9ab6-493">`EnsureCreated` bağlam veritabanının mevcut olmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-493">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="e9ab6-494">Varsa, hiçbir eylem yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-494">If it exists, no action is taken.</span></span> <span data-ttu-id="e9ab6-495">Yoksa, veritabanı ve tüm şeması oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-495">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="e9ab6-496">`EnsureCreated` veritabanını oluşturmak için geçişleri kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-496">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="e9ab6-497">İle oluşturulan bir veritabanı `EnsureCreated` daha sonra geçişler kullanılarak güncelleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-497">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="e9ab6-498">`EnsureCreated` , aşağıdaki iş akışına izin veren uygulama başlatma sırasında çağrılır:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-498">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="e9ab6-499">VERITABANıNı silin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-499">Delete the DB.</span></span>
* <span data-ttu-id="e9ab6-500">DB şemasını değiştirin (örneğin, bir `EmailAddress` alan ekleyin).</span><span class="sxs-lookup"><span data-stu-id="e9ab6-500">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="e9ab6-501">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-501">Run the app.</span></span>
* <span data-ttu-id="e9ab6-502">`EnsureCreated` sütunu ile bir VERITABANı oluşturur `EmailAddress` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-502">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="e9ab6-503">`EnsureCreated` şema hızlı bir şekilde gelişmede geliştirmede daha erken kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-503">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="e9ab6-504">Öğreticide daha sonra DB silinir ve geçişler kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-504">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="e9ab6-505">Uygulamayı test etme</span><span class="sxs-lookup"><span data-stu-id="e9ab6-505">Test the app</span></span>

<span data-ttu-id="e9ab6-506">Uygulamayı çalıştırın ve ilkeyi kabul edin cookie .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-506">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="e9ab6-507">Bu uygulama, kişisel bilgileri saklar.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-507">This app doesn't keep personal information.</span></span> <span data-ttu-id="e9ab6-508">İlke hakkında, cookie [AB Genel VERI koruma YÖNETMELIĞI (GDPR) desteğiyle](xref:security/gdpr)ilgili bilgi edinebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-508">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="e9ab6-509">**Öğrenciler** bağlantısını seçin ve ardından **Yeni oluştur**.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-509">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="e9ab6-510">Düzenle, Ayrıntılar ve Sil bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-510">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="e9ab6-511">SchoolContext DB bağlamını inceleyin</span><span class="sxs-lookup"><span data-stu-id="e9ab6-511">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="e9ab6-512">Belirli bir veri modeli için EF Core işlevselliğini koordine eden ana sınıf DB bağlam sınıfıdır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-512">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="e9ab6-513">Veri bağlamı [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)öğesinden türetilir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-513">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="e9ab6-514">Veri bağlamı, veri modeline hangi varlıkların ekleneceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-514">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="e9ab6-515">Bu projede, sınıfı olarak adlandırılır `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-515">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="e9ab6-516">Aşağıdaki kodla *SchoolContext.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-516">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="e9ab6-517">Vurgulanan kod, her bir varlık kümesi için bir [Dbset \<TEntity> ](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-517">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="e9ab6-518">EF Core terminoloji:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-518">In EF Core terminology:</span></span>

* <span data-ttu-id="e9ab6-519">Bir varlık kümesi, genellikle bir DB tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-519">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="e9ab6-520">Bir varlık, tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-520">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="e9ab6-521">`DbSet<Enrollment>` ve `DbSet<Course>` atlanamaz.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-521">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="e9ab6-522">EF Core `Student` `Enrollment` , varlık varlığa başvurduğundan ve varlık `Enrollment` varlığa başvurduğundan bunları örtülü olarak içerir `Course` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-522">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="e9ab6-523">Bu öğreticide, `DbSet<Enrollment>` ve `DbSet<Course>` içinde tutun `SchoolContext` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-523">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="e9ab6-524">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="e9ab6-524">SQL Server Express LocalDB</span></span>

<span data-ttu-id="e9ab6-525">Bağlantı dizesi [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)belirtir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-525">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="e9ab6-526">LocalDB, SQL Server Express veritabanı altyapısının hafif bir sürümüdür ve üretim kullanımı için değil uygulama geliştirmeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-526">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="e9ab6-527">LocalDB, istek üzerine başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-527">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="e9ab6-528">Varsayılan olarak, LocalDB dizinde *. mdf* DB dosyaları oluşturur `C:/Users/<user>` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-528">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="e9ab6-529">Test verileriyle VERITABANıNı başlatmak için kod ekleme</span><span class="sxs-lookup"><span data-stu-id="e9ab6-529">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="e9ab6-530">EF Core boş bir VERITABANı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-530">EF Core creates an empty DB.</span></span> <span data-ttu-id="e9ab6-531">Bu bölümde, `Initialize` Test verileriyle doldurmak için bir yöntem yazılır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-531">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="e9ab6-532">*Veri* klasöründe, *DbInitializer.cs* adlı yeni bir sınıf dosyası oluşturun ve aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-532">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="e9ab6-533">Note: önceki kod, `Models` yerine ad alanı için ( `namespace ContosoUniversity.Models` ) kullanır `Data` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-533">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="e9ab6-534">`Models` , scaffolder tarafından oluşturulan kodla tutarlıdır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-534">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="e9ab6-535">Daha fazla bilgi için bkz. [GitHub yapı iskelesi sorunu](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="e9ab6-535">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="e9ab6-536">Kod, VERITABANıNDA herhangi bir öğrenci olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-536">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="e9ab6-537">Veritabanında hiç öğrenci yoksa, DB test verileriyle başlatılır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-537">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="e9ab6-538">Performansı iyileştirmek için test verilerini koleksiyonlar yerine dizilere yükler `List<T>` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-538">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="e9ab6-539">`EnsureCreated`YÖNTEMI DB bağlamı için otomatik olarak DB oluşturur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-539">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="e9ab6-540">VERITABANı varsa, `EnsureCreated` veritabanını değiştirmeden döndürür.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-540">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="e9ab6-541">*Program.cs*içinde, `Main` çağrısı yapılacak yöntemi değiştirin `Initialize` :</span><span class="sxs-lookup"><span data-stu-id="e9ab6-541">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="e9ab6-542">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e9ab6-542">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="e9ab6-543">Çalışıyorsa uygulamayı durdurun ve **Paket Yöneticisi konsolunda** (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-543">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="e9ab6-544">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e9ab6-544">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="e9ab6-545">Çalışıyorsa uygulamayı durdurun ve *cu. db* dosyasını silin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-545">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="e9ab6-546">VERITABANıNı görüntüleme</span><span class="sxs-lookup"><span data-stu-id="e9ab6-546">View the DB</span></span>

<span data-ttu-id="e9ab6-547">Veritabanı adı, daha önce belirttiğiniz bağlam adından ve bir tire ve bir GUID ile oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-547">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="e9ab6-548">Bu nedenle, veritabanı adı "SchoolContext-{GUID}" olacaktır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-548">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="e9ab6-549">GUID her kullanıcı için farklı olacaktır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-549">The GUID will be different for each user.</span></span>
<span data-ttu-id="e9ab6-550">Visual Studio 'daki **Görünüm** menüsünden **SQL Server Nesne Gezgini** (ssox) öğesini açın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-550">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="e9ab6-551">SSOX 'te, **(LocalDB) \MSSQLLocalDB > veritabanları > SchoolContext-{GUID}**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-551">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="e9ab6-552">**Tables** düğümünü genişletin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-552">Expand the **Tables** node.</span></span>

<span data-ttu-id="e9ab6-553">Oluşturulan sütunları ve tabloya yerleştirilen satırları görmek için **öğrenci** tablosuna sağ tıklayın ve **verileri görüntüle** ' ye tıklayın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-553">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="e9ab6-554">Zaman uyumsuz kod</span><span class="sxs-lookup"><span data-stu-id="e9ab6-554">Asynchronous code</span></span>

<span data-ttu-id="e9ab6-555">Zaman uyumsuz programlama, ASP.NET Core ve EF Core için varsayılan moddur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-555">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="e9ab6-556">Web sunucusunda sınırlı sayıda iş parçacığı bulunur ve yüksek yük durumlarında tüm kullanılabilir iş parçacıkları kullanımda olabilir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-556">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="e9ab6-557">Bu durumda, sunucu, iş parçacıkları boşaltılana kadar yeni istekleri işleyemez.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-557">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="e9ab6-558">Zaman uyumlu kodla, çok sayıda iş parçacığı, g/ç 'nin tamamlanmasını beklediği için aslında herhangi bir iş yapmadıklarında bağlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-558">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="e9ab6-559">Zaman uyumsuz kod ile, bir işlem g/ç 'yi tamamlanmayı beklerken, sunucunun diğer istekleri işlemek için kullanması için iş parçacığı serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-559">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="e9ab6-560">Sonuç olarak, zaman uyumsuz kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucu, gecikme olmadan daha fazla trafiği işlemeye etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-560">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="e9ab6-561">Zaman uyumsuz kod, çalışma zamanında az miktarda yük getirir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-561">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="e9ab6-562">Düşük trafik durumlarında, performans artışı göz ardı edilebilir, ancak yüksek trafik durumları için olası performans iyileştirmesi oldukça önemlidir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-562">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="e9ab6-563">Aşağıdaki kodda, [Async](/dotnet/csharp/language-reference/keywords/async) anahtar sözcüğü, `Task<T>` return değeri, `await` anahtar sözcüğü ve `ToListAsync` yöntemi kodu zaman uyumsuz olarak yürütür.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-563">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="e9ab6-564">`async`Anahtar sözcüğü derleyiciye şunu söyler:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-564">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="e9ab6-565">Yöntem gövdesinin parçaları için geri çağrılar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-565">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="e9ab6-566">Döndürülen [görev](/dotnet/api/system.threading.tasks.task) nesnesini otomatik olarak oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-566">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="e9ab6-567">Daha fazla bilgi için bkz. [görev dönüş türü](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="e9ab6-567">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="e9ab6-568">Örtük dönüş türü, `Task` devam eden işi temsil eder.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-568">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="e9ab6-569">`await`Anahtar sözcüğü, derleyicinin yöntemi iki parçaya böetmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-569">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="e9ab6-570">İlk bölüm, zaman uyumsuz olarak başlatılan işlemle biter.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-570">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="e9ab6-571">İkinci bölüm, işlem tamamlandığında çağrılan bir geri çağırma yöntemine konur.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-571">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="e9ab6-572">`ToListAsync` , Uzantı yönteminin zaman uyumsuz sürümüdür `ToList` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-572">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="e9ab6-573">EF Core kullanan zaman uyumsuz kodu yazarken dikkat edilmesi gereken bazı şeyler:</span><span class="sxs-lookup"><span data-stu-id="e9ab6-573">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="e9ab6-574">Yalnızca sorguları veya komutlarının VERITABANıNA gönderilmesine neden olan deyimler zaman uyumsuz olarak yürütülür.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-574">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="e9ab6-575">,,, `ToListAsync` `SingleOrDefaultAsync` Ve içerir `FirstOrDefaultAsync` `SaveChangesAsync` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-575">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="e9ab6-576">Yalnızca, gibi bir değiştiren deyimler içermez `IQueryable` `var students = context.Students.Where(s => s.LastName == "Davolio")` .</span><span class="sxs-lookup"><span data-stu-id="e9ab6-576">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="e9ab6-577">EF Core bağlamı iş parçacığı açısından güvenli değildir: paralel olarak birden çok işlem yapmayı denemeyin.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-577">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="e9ab6-578">Zaman uyumsuz kodun performans avantajlarından yararlanmak için, VERITABANıNA sorgu gönderen EF Core yöntemlerini çağırıyorsa kitaplık paketlerinin (örneğin, sayfalama için) zaman uyumsuz olarak kullanılacağını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-578">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="e9ab6-579">.NET 'te zaman uyumsuz programlama hakkında daha fazla bilgi için bkz. Async [and await ile](/dotnet/csharp/programming-guide/concepts/async/)zaman uyumsuz [genel bakış](/dotnet/standard/async) ve zaman uyumsuz programlama</span><span class="sxs-lookup"><span data-stu-id="e9ab6-579">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="e9ab6-580">Sonraki öğreticide, temel CRUD (oluşturma, okuma, güncelleştirme, silme) işlemleri incelenir.</span><span class="sxs-lookup"><span data-stu-id="e9ab6-580">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="e9ab6-581">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e9ab6-581">Additional resources</span></span>

* [<span data-ttu-id="e9ab6-582">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="e9ab6-582">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="e9ab6-583">Sonraki</span><span class="sxs-lookup"><span data-stu-id="e9ab6-583">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
