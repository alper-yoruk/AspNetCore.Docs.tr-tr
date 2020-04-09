---
title: ASP.NET Core'da Varlık Çerçeve Çekirdekli Jilet Sayfaları - 8'in 1'i Öğretici
author: rick-anderson
description: Entity Framework Core'u kullanarak Jilet Sayfaları uygulamasının nasıl oluşturuluruz olduğunu gösterir
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 09/26/2019
uid: data/ef-rp/intro
ms.openlocfilehash: 94783aa9014aef4c5f775fc8f36a2c3a7715e4b6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78656823"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="931b6-103">ASP.NET Core'da Varlık Çerçeve Çekirdekli Jilet Sayfaları - 8'in 1'i Öğretici</span><span class="sxs-lookup"><span data-stu-id="931b6-103">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="931b6-104">Yazar: [Tom Dykstra](https://github.com/tdykstra) ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="931b6-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="931b6-105">Bu, [ASP.NET Core Razor Pages](xref:razor-pages/index) uygulamasında Entity Framework (EF) Core'un nasıl kullanılacağını gösteren bir dizi öğreticinin ilkidir.</span><span class="sxs-lookup"><span data-stu-id="931b6-105">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="931b6-106">Öğreticiler kurgusal bir Contoso Üniversitesi için bir web sitesi oluşturmak.</span><span class="sxs-lookup"><span data-stu-id="931b6-106">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="931b6-107">Site, öğrenci kabulü, ders oluşturma ve eğitmen atamaları gibi işlevleri içerir.</span><span class="sxs-lookup"><span data-stu-id="931b6-107">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="931b6-108">Öğretici kod ilk yaklaşım kullanır.</span><span class="sxs-lookup"><span data-stu-id="931b6-108">The tutorial uses the code first approach.</span></span> <span data-ttu-id="931b6-109">Veritabanı ilk yaklaşımı kullanarak bu öğretici aşağıdaki hakkında bilgi için, [bu Github sorunu](https://github.com/dotnet/AspNetCore.Docs/issues/16897)bakın.</span><span class="sxs-lookup"><span data-stu-id="931b6-109">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="931b6-110">Tamamlanan uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="931b6-110">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="931b6-111">[Talimatları indirin.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="931b6-111">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="931b6-112">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="931b6-112">Prerequisites</span></span>

* <span data-ttu-id="931b6-113">Razor Pages'de yeniyseniz, bu sayfaya başlamadan önce Razor Pages öğretici [serisiyle başlayın.](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="931b6-113">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="931b6-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="931b6-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="931b6-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="931b6-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="931b6-116">Veritabanı motorları</span><span class="sxs-lookup"><span data-stu-id="931b6-116">Database engines</span></span>

<span data-ttu-id="931b6-117">Visual Studio yönergeleri, SQL Server Express'in yalnızca Windows'da çalışan bir sürümü olan [SQL Server LocalDB'ı](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)kullanır.</span><span class="sxs-lookup"><span data-stu-id="931b6-117">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="931b6-118">Visual Studio Code yönergeleri, platformlar arası veritabanı motoru [olan SQLite'ı](https://www.sqlite.org/)kullanır.</span><span class="sxs-lookup"><span data-stu-id="931b6-118">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="931b6-119">SQLite'yi kullanmayı seçerseniz, [SQLite için DB Browser](https://sqlitebrowser.org/)gibi bir SQLite veritabanını yönetmek ve görüntülemek için üçüncü taraf bir araç indirin ve yükleyin.</span><span class="sxs-lookup"><span data-stu-id="931b6-119">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="931b6-120">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="931b6-120">Troubleshooting</span></span>

<span data-ttu-id="931b6-121">Çözemediğiniz bir sorunla karşılaştıysanız, kodunuzu [tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="931b6-121">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="931b6-122">Yardım almak için iyi bir yol StackOverflow.com için bir soru göndererek, [ASP.NET Core etiketi](https://stackoverflow.com/questions/tagged/asp.net-core) veya EF Core [etiketi](https://stackoverflow.com/questions/tagged/entity-framework-core)ni kullanarak.</span><span class="sxs-lookup"><span data-stu-id="931b6-122">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="931b6-123">Örnek uygulama</span><span class="sxs-lookup"><span data-stu-id="931b6-123">The sample app</span></span>

<span data-ttu-id="931b6-124">Bu eğitimlerde yerleşik olan uygulama temel bir üniversite web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="931b6-124">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="931b6-125">Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleyebilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-125">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="931b6-126">Burada öğretici oluşturulan ekranlardan birkaçı vardır.</span><span class="sxs-lookup"><span data-stu-id="931b6-126">Here are a few of the screens created in the tutorial.</span></span>

![Öğrenci Endeksi sayfası](intro/_static/students-index30.png)

![Öğrenciler Sayfayı Edit](intro/_static/student-edit30.png)

<span data-ttu-id="931b6-129">Bu sitenin UI stili yerleşik proje şablonlarını temel almaktadır.</span><span class="sxs-lookup"><span data-stu-id="931b6-129">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="931b6-130">Öğreticinin odak noktası, UI'nin nasıl özelleştirilene değil, EF Core'un nasıl kullanılacağıdır.</span><span class="sxs-lookup"><span data-stu-id="931b6-130">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="931b6-131">Tamamlanan projenin kaynak kodunu almak için sayfanın üst kısmındaki bağlantıyı izleyin.</span><span class="sxs-lookup"><span data-stu-id="931b6-131">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="931b6-132">*Cu30* klasörü öğretici ASP.NET Core 3.0 sürümü için kod vardır.</span><span class="sxs-lookup"><span data-stu-id="931b6-132">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="931b6-133">1-7 öğreticiler için kodun durumunu yansıtan dosyalar *cu30snapshots* klasöründe bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-133">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="931b6-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="931b6-134">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="931b6-135">Tamamlanan projeyi indirdikten sonra uygulamayı çalıştırmak için:</span><span class="sxs-lookup"><span data-stu-id="931b6-135">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="931b6-136">Adında *SQLite* olan üç dosyayı ve bir klasörü silin.</span><span class="sxs-lookup"><span data-stu-id="931b6-136">Delete three files and one folder that have *SQLite* in the name.</span></span>
* <span data-ttu-id="931b6-137">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="931b6-137">Build the project.</span></span>
* <span data-ttu-id="931b6-138">Paket Yöneticisi Konsolunda (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="931b6-138">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="931b6-139">Veritabanıtohum için proje çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="931b6-139">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="931b6-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="931b6-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="931b6-141">Tamamlanan projeyi indirdikten sonra uygulamayı çalıştırmak için:</span><span class="sxs-lookup"><span data-stu-id="931b6-141">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="931b6-142">*ContosoUniversity.csproj*silin ve *ContosoUniversitySQLite.csproj* *ContosoUniversity.csproj*için yeniden adlandırmak .</span><span class="sxs-lookup"><span data-stu-id="931b6-142">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="931b6-143">*Startup.cs*silin ve *StartupSQLite.cs* *Startup.cs*olarak yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="931b6-143">Delete *Startup.cs*, and rename *StartupSQLite.cs* to *Startup.cs*.</span></span>
* <span data-ttu-id="931b6-144">*AppSettings.json'u*silin ve *appSettingsSQLite.json'ı* *appSettings.json*olarak yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="931b6-144">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="931b6-145">*Geçişler* klasörünü silin ve *MigrationsSQL'i* *Geçişler*klasörüne yeniden adlandırın.</span><span class="sxs-lookup"><span data-stu-id="931b6-145">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="931b6-146">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="931b6-146">Build the project.</span></span>
* <span data-ttu-id="931b6-147">Proje klasöründeki bir komut isteminde aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="931b6-147">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="931b6-148">SQLite aracınızda aşağıdaki SQL deyimini çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="931b6-148">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="931b6-149">Veritabanıtohum için proje çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="931b6-149">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="931b6-150">Web uygulaması projesini oluşturma</span><span class="sxs-lookup"><span data-stu-id="931b6-150">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="931b6-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="931b6-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="931b6-152">Visual Studio **File** menüsünden **Yeni** > **Proje'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="931b6-152">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="931b6-153">**Core Web Uygulaması ASP.NET**seçin.</span><span class="sxs-lookup"><span data-stu-id="931b6-153">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="931b6-154">Proje *ContosoUniversity*adı .</span><span class="sxs-lookup"><span data-stu-id="931b6-154">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="931b6-155">Büyük harf de dahil olmak üzere bu tam adı kullanmak önemlidir, böylece kod kopyalanıp yapıştırıldığında ad boşlukları eşleşir.</span><span class="sxs-lookup"><span data-stu-id="931b6-155">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="931b6-156">Açılan lar arasında **.NET Core** ve **ASP.NET Core 3.0'ı** seçin ve ardından **Web Uygulaması'nı**seçin.</span><span class="sxs-lookup"><span data-stu-id="931b6-156">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="931b6-157">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="931b6-157">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="931b6-158">Bir terminalde, proje klasörü oluşturulması gereken klasöre gidin.</span><span class="sxs-lookup"><span data-stu-id="931b6-158">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="931b6-159">Razor Pages projesi oluşturmak için aşağıdaki `cd` komutları çalıştırın ve yeni proje klasörüne:</span><span class="sxs-lookup"><span data-stu-id="931b6-159">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="931b6-160">Site stilini ayarlama</span><span class="sxs-lookup"><span data-stu-id="931b6-160">Set up the site style</span></span>

<span data-ttu-id="931b6-161">*Sayfalar/Paylaşılan/_Layout.cshtml'i*güncelleyerek site üstbilgisini, altbilgisini ve menüsünü ayarlama:</span><span class="sxs-lookup"><span data-stu-id="931b6-161">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="931b6-162">"ContosoUniversity"in her oluşumunu "Contoso Üniversitesi" olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="931b6-162">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="931b6-163">Üç olay var.</span><span class="sxs-lookup"><span data-stu-id="931b6-163">There are three occurrences.</span></span>

* <span data-ttu-id="931b6-164">**Ev** ve **Gizlilik** menü girişlerini silin ve **Hakkında,** **Öğrenciler,** **Kurslar,** Eğitmenler ve **Bölümler**için **girişler**ekleyin.</span><span class="sxs-lookup"><span data-stu-id="931b6-164">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="931b6-165">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="931b6-165">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="931b6-166">*Pages/Index.cshtml'de,* ASP.NET Core hakkındaki metni bu uygulamayla ilgili metinle değiştirmek için dosyanın içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="931b6-166">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="931b6-167">Ana sayfanın görüntülenediğini doğrulamak için uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="931b6-167">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="931b6-168">Veri modeli</span><span class="sxs-lookup"><span data-stu-id="931b6-168">The data model</span></span>

<span data-ttu-id="931b6-169">Aşağıdaki bölümlerde bir veri modeli oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="931b6-169">The following sections create a data model:</span></span>

![Ders-Kayıt-Öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

<span data-ttu-id="931b6-171">Bir öğrenci herhangi bir sayıda kursa kaydolabilir ve bir kursa herhangi bir sayıda öğrenci kaydolabilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-171">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="931b6-172">Öğrenci varlığı</span><span class="sxs-lookup"><span data-stu-id="931b6-172">The Student entity</span></span>

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

* <span data-ttu-id="931b6-174">Proje klasöründe bir *Modeller* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="931b6-174">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="931b6-175">Aşağıdaki kodile *Modeller/Student.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="931b6-175">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="931b6-176">Özellik, `ID` veritabanı tablosunun bu sınıfa karşılık gelen birincil anahtar sütunu olur.</span><span class="sxs-lookup"><span data-stu-id="931b6-176">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="931b6-177">Varsayılan olarak, EF Core adlı veya `ID` `classnameID` birincil anahtar olarak bir özelliği yorumlar.</span><span class="sxs-lookup"><span data-stu-id="931b6-177">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="931b6-178">Yani `Student` sınıf birincil anahtarı `StudentID`için otomatik olarak tanınan ad .</span><span class="sxs-lookup"><span data-stu-id="931b6-178">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span>

<span data-ttu-id="931b6-179">Özellik `Enrollments` bir [navigasyon özelliğidir.](/ef/core/modeling/relationships)</span><span class="sxs-lookup"><span data-stu-id="931b6-179">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="931b6-180">Gezinti özellikleri, bu varlıkla ilişkili diğer varlıkları tutar.</span><span class="sxs-lookup"><span data-stu-id="931b6-180">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="931b6-181">Bu durumda, `Enrollments` bir `Student` varlığın mülkiyeti, `Enrollment` o Öğrenci ile ilgili tüm varlıkları tutar.</span><span class="sxs-lookup"><span data-stu-id="931b6-181">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="931b6-182">Örneğin, veritabanındaki bir Öğrenci satırında ilgili iki Kayıt `Enrollments` satırı varsa, gezinti özelliği bu iki Kayıt varlığını içerir.</span><span class="sxs-lookup"><span data-stu-id="931b6-182">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="931b6-183">Veritabanında, Öğrenci Kimliği sütununda öğrencinin kimlik değeri varsa, Öğrenci satırı öğrenci satırıyla ilişkilidir.</span><span class="sxs-lookup"><span data-stu-id="931b6-183">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="931b6-184">Örneğin, bir Öğrenci satırında ID=1 olduğunu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="931b6-184">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="931b6-185">İlgili Kayıt satırları StudentID = 1 olacaktır.</span><span class="sxs-lookup"><span data-stu-id="931b6-185">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="931b6-186">StudentID, Kayıt tablosundaki yabancı bir *anahtardır.*</span><span class="sxs-lookup"><span data-stu-id="931b6-186">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="931b6-187">Özellik, `Enrollments` birden `ICollection<Enrollment>` çok ilişkili Kayıt varlığı olabileceği için tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="931b6-187">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="931b6-188">Diğer koleksiyon türlerini kullanabilirsiniz, `HashSet<Enrollment>`örneğin. `List<Enrollment>`</span><span class="sxs-lookup"><span data-stu-id="931b6-188">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="931b6-189">Kullanıldığında, `ICollection<Enrollment>` EF Core varsayılan `HashSet<Enrollment>` olarak bir koleksiyon oluşturur.</span><span class="sxs-lookup"><span data-stu-id="931b6-189">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="931b6-190">Kayıt varlığı</span><span class="sxs-lookup"><span data-stu-id="931b6-190">The Enrollment entity</span></span>

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

<span data-ttu-id="931b6-192">Aşağıdaki kodile *Modeller/Enrollment.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="931b6-192">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="931b6-193">Özellik `EnrollmentID` birincil anahtardır; bu varlık `classnameID` kendi başına `ID` yerine deseni kullanır.</span><span class="sxs-lookup"><span data-stu-id="931b6-193">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="931b6-194">Üretim veri modeli için bir desen seçin ve tutarlı bir şekilde kullanın.</span><span class="sxs-lookup"><span data-stu-id="931b6-194">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="931b6-195">Bu öğretici sadece her iki iş göstermek için hem kullanır.</span><span class="sxs-lookup"><span data-stu-id="931b6-195">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="931b6-196">`ID` Kullanmadan `classname` kullanmak, bazı veri modeli değişikliklerinin uygulanmasını kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="931b6-196">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="931b6-197">Özellik `Grade` bir `enum`.</span><span class="sxs-lookup"><span data-stu-id="931b6-197">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="931b6-198">Tür bildiriminden `Grade` sonraki soru işareti `Grade` özelliğin [geçersiz](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/)olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="931b6-198">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="931b6-199">Sıfır notu null'dan farklı olan&mdash;bir not, bir notun bilinmediği veya henüz atanmadığı anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="931b6-199">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="931b6-200">Özellik `StudentID` yabancı bir anahtardır ve ilgili `Student`navigasyon özelliği .</span><span class="sxs-lookup"><span data-stu-id="931b6-200">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="931b6-201">Bir `Enrollment` varlık tek `Student` bir varlıkla ilişkilidir, `Student` bu nedenle özellik tek bir varlık içerir.</span><span class="sxs-lookup"><span data-stu-id="931b6-201">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="931b6-202">Özellik `CourseID` yabancı bir anahtardır ve ilgili `Course`navigasyon özelliği .</span><span class="sxs-lookup"><span data-stu-id="931b6-202">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="931b6-203">Bir `Enrollment` varlık tek `Course` bir varlıkla ilişkilidir.</span><span class="sxs-lookup"><span data-stu-id="931b6-203">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="931b6-204">EF Core, bir özelliği yabancı `<navigation property name><primary key property name>`bir anahtar olarak yorumluyorsa.</span><span class="sxs-lookup"><span data-stu-id="931b6-204">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="931b6-205">Örneğin,`StudentID` `Student` `Student` varlığın birincil anahtarı `ID`.</span><span class="sxs-lookup"><span data-stu-id="931b6-205">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="931b6-206">Yabancı anahtar özellikleri de `<primary key property name>`adlandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-206">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="931b6-207">Örneğin, `CourseID` varlığın `Course` birincil anahtarı `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="931b6-207">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="931b6-208">Kurs varlığı</span><span class="sxs-lookup"><span data-stu-id="931b6-208">The Course entity</span></span>

![Ders varlık diyagramı](intro/_static/course-entity.png)

<span data-ttu-id="931b6-210">Aşağıdaki kodile *Modeller/Course.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="931b6-210">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="931b6-211">Tesis `Enrollments` bir navigasyon özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="931b6-211">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="931b6-212">Bir `Course` varlık herhangi bir sayıda `Enrollment` varlıkla ilişkili olabilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-212">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="931b6-213">Öznitelik, `DatabaseGenerated` uygulamanın veritabanının oluşturması yerine birincil anahtarı belirtmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="931b6-213">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="931b6-214">Derleyici hatası olmadığını doğrulamak için projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="931b6-214">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="931b6-215">İskele Öğrenci sayfaları</span><span class="sxs-lookup"><span data-stu-id="931b6-215">Scaffold Student pages</span></span>

<span data-ttu-id="931b6-216">Bu bölümde, oluşturmak için ASP.NET Core iskele aracını kullanırsınız:</span><span class="sxs-lookup"><span data-stu-id="931b6-216">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="931b6-217">Bir EF Core *bağlam* sınıfı.</span><span class="sxs-lookup"><span data-stu-id="931b6-217">An EF Core *context* class.</span></span> <span data-ttu-id="931b6-218">Bağlam, belirli bir veri modeli için Varlık Çerçevesi işlevselliğini koordine eden ana sınıftır.</span><span class="sxs-lookup"><span data-stu-id="931b6-218">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="931b6-219">Sınıftan `Microsoft.EntityFrameworkCore.DbContext` türetilmiştir.</span><span class="sxs-lookup"><span data-stu-id="931b6-219">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="931b6-220">`Student` Varlık için Oluştur, Oku, Güncelleştir ve Sil (CRUD) işlemlerini işleyen jilet sayfaları.</span><span class="sxs-lookup"><span data-stu-id="931b6-220">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="931b6-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="931b6-221">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="931b6-222">*Sayfalar* klasöründe bir *Öğrenci* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="931b6-222">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="931b6-223">**Çözüm Gezgini'nde,** *Sayfalar/Öğrenciler* klasörüne sağ tıklayın ve **Yeni İskele Öğesi** **Ekle'yi** > seçin.</span><span class="sxs-lookup"><span data-stu-id="931b6-223">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="931b6-224">İskele **Ekle** iletişim kutusunda, **Entity Framework (CRUD)** > **ADD**kullanarak Jilet Sayfaları'nı seçin.</span><span class="sxs-lookup"><span data-stu-id="931b6-224">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="931b6-225">Varlık **Çerçevesi (CRUD)** iletişim kutusunu kullanarak Jilet Sayfaları Ekle iletişim kutusunda:</span><span class="sxs-lookup"><span data-stu-id="931b6-225">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="931b6-226">Model **sınıfı** açılır açılır, **Öğrenci (ContosoUniversity.Models)** seçin.</span><span class="sxs-lookup"><span data-stu-id="931b6-226">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="931b6-227">Veri **bağlamı sınıf** satırında **+** (artı) işaretini seçin.</span><span class="sxs-lookup"><span data-stu-id="931b6-227">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="931b6-228">*ContosoUniversity.Models.ContosoUniversityContext'den* *ContosoUniversity.Data.SchoolContext'a*veri bağlamı adını değiştirin.</span><span class="sxs-lookup"><span data-stu-id="931b6-228">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="931b6-229">**Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="931b6-229">Select **Add**.</span></span>

<span data-ttu-id="931b6-230">Aşağıdaki paketler otomatik olarak yüklenir:</span><span class="sxs-lookup"><span data-stu-id="931b6-230">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="931b6-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="931b6-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="931b6-232">Gerekli NuGet paketlerini yüklemek için aşağıdaki .NET Core CLI komutlarını çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="931b6-232">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="931b6-233">Microsoft.VisualStudio.Web.CodeGeneration.Design paketi iskele için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="931b6-233">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="931b6-234">Uygulama SQL Server kullanmasa da, iskele aracının SQL Server paketine ihtiyacı vardır.</span><span class="sxs-lookup"><span data-stu-id="931b6-234">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="931b6-235">*Sayfalar/Öğrenciler* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="931b6-235">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="931b6-236">[Aspnet-codegenerator iskele aracını](xref:fundamentals/tools/dotnet-aspnet-codegenerator)yüklemek için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="931b6-236">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="931b6-237">Öğrenci sayfalarını iskeleye aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="931b6-237">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="931b6-238">**Windows üzerinde**</span><span class="sxs-lookup"><span data-stu-id="931b6-238">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="931b6-239">**macOS veya Linux'ta**</span><span class="sxs-lookup"><span data-stu-id="931b6-239">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="931b6-240">Önceki adımla ilgili bir sorununuz varsa, projeyi oluşturun ve iskele adımını yeniden deneyin.</span><span class="sxs-lookup"><span data-stu-id="931b6-240">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="931b6-241">İskele işlemi:</span><span class="sxs-lookup"><span data-stu-id="931b6-241">The scaffolding process:</span></span>

* <span data-ttu-id="931b6-242">*Sayfalar/Öğrenciler* klasöründe Jilet sayfaları oluşturur:</span><span class="sxs-lookup"><span data-stu-id="931b6-242">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="931b6-243">*Create.cshtml* ve *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="931b6-243">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="931b6-244">*Delete.cshtml* ve *Delete.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="931b6-244">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="931b6-245">*Ayrıntılar.cshtml* ve *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="931b6-245">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="931b6-246">*Edit.cshtml* ve *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="931b6-246">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="931b6-247">*Index.cshtml* ve *Index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="931b6-247">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="931b6-248">*Veri/SchoolContext.cs*oluşturur.</span><span class="sxs-lookup"><span data-stu-id="931b6-248">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="931b6-249">*Startup.cs*bağımlılık enjeksiyonuna bağlam ekler.</span><span class="sxs-lookup"><span data-stu-id="931b6-249">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="931b6-250">*appsettings.json'a*veritabanı bağlantı dizesi ekler.</span><span class="sxs-lookup"><span data-stu-id="931b6-250">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="931b6-251">Veritabanı bağlantı dizesi</span><span class="sxs-lookup"><span data-stu-id="931b6-251">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="931b6-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="931b6-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="931b6-253">Bağlantı dizesi [SQL Server LocalDB'yi](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)belirtir.</span><span class="sxs-lookup"><span data-stu-id="931b6-253">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="931b6-254">LocalDB, SQL Server Express Veritabanı Altyapısının hafif bir sürümüdür ve üretim kullanımı için değil, uygulama geliştirme için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="931b6-254">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="931b6-255">Varsayılan olarak, LocalDB `C:/Users/<user>` dizinde *.mdf* dosyaları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="931b6-255">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="931b6-256">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="931b6-256">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="931b6-257">Bağlantı dizesini *CU.db*adlı bir SQLite veritabanı dosyasına işaret edecek şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="931b6-257">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="931b6-258">Veritabanı bağlam ı sınıfını güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="931b6-258">Update the database context class</span></span>

<span data-ttu-id="931b6-259">Belirli bir veri modeli için EF Core işlevini koordine eden ana sınıf veritabanı bağlam sınıfıdır.</span><span class="sxs-lookup"><span data-stu-id="931b6-259">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="931b6-260">İçerik [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)türetilmiştir.</span><span class="sxs-lookup"><span data-stu-id="931b6-260">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="931b6-261">Bağlam, veri modeline hangi varlıkların dahil edildiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="931b6-261">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="931b6-262">Bu projede sınıfadlandırılmış. `SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="931b6-262">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="931b6-263">Aşağıdaki kodla *SchoolContext.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="931b6-263">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="931b6-264">Vurgulanan kod, her varlık kümesi için bir [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="931b6-264">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="931b6-265">EF Çekirdek terminolojisinde:</span><span class="sxs-lookup"><span data-stu-id="931b6-265">In EF Core terminology:</span></span>

* <span data-ttu-id="931b6-266">Bir varlık kümesi genellikle bir veritabanı tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="931b6-266">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="931b6-267">Bir varlık tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="931b6-267">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="931b6-268">Bir varlık kümesi birden çok varlık içerdiğinden, DBSet özellikleri çoğul adlar olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="931b6-268">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="931b6-269">İskele aracı bir`Student` DBSet oluşturduğundan, bu adım `Students`onu çoğul olarak değiştirir.</span><span class="sxs-lookup"><span data-stu-id="931b6-269">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="931b6-270">Razor Pages kodunun yeni DBSet adı ile eşleşmesini `_context.Student` sağlamak için, `_context.Students`tüm projede '' için küresel bir değişiklik yapın.</span><span class="sxs-lookup"><span data-stu-id="931b6-270">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="931b6-271">8 olay var.</span><span class="sxs-lookup"><span data-stu-id="931b6-271">There are 8 occurrences.</span></span>

<span data-ttu-id="931b6-272">Derleyici hatası olmadığını doğrulamak için projeyi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="931b6-272">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="931b6-273">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="931b6-273">Startup.cs</span></span>

<span data-ttu-id="931b6-274">ASP.NET Core [bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection)ile inşa edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="931b6-274">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="931b6-275">Hizmetler (EF Core veritabanı bağlamı gibi) uygulama başlatma sırasında bağımlılık enjeksiyonu ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-275">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="931b6-276">Bu hizmetleri gerektiren bileşenlere (Jilet Sayfaları gibi) bu hizmetler yapıcı parametreler aracılığıyla sağlanır.</span><span class="sxs-lookup"><span data-stu-id="931b6-276">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="931b6-277">Veritabanı bağlamı örneğini alan oluşturucu kodu daha sonra öğreticide gösterilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-277">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="931b6-278">İskele aracı, bağlam sınıfını bağımlılık enjeksiyon konteyneri ile otomatik olarak kaydetti.</span><span class="sxs-lookup"><span data-stu-id="931b6-278">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="931b6-279">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="931b6-279">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="931b6-280">, `ConfigureServices`vurgulanan satırlar iskele tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="931b6-280">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="931b6-281">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="931b6-281">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="931b6-282">In `ConfigureServices`, iskele aramaları `UseSqlite`tarafından eklenen kodu emin olun.</span><span class="sxs-lookup"><span data-stu-id="931b6-282">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="931b6-283">Bağlantı dizesinin adı, [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesindeki bir yöntem çağırılarak içeriğe aktarılır.</span><span class="sxs-lookup"><span data-stu-id="931b6-283">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="931b6-284">Yerel geliştirme [için, ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) *appsettings.json* dosyasından bağlantı dizesini okur.</span><span class="sxs-lookup"><span data-stu-id="931b6-284">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="931b6-285">Veritabanını oluşturma</span><span class="sxs-lookup"><span data-stu-id="931b6-285">Create the database</span></span>

<span data-ttu-id="931b6-286">Veritabanı yoksa oluşturmak için *Program.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="931b6-286">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="931b6-287">İçerik için bir veritabanı [varsa, Oluşturulanları Sağlama](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) yöntemi hiçbir eylemde bulunmaz.</span><span class="sxs-lookup"><span data-stu-id="931b6-287">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="931b6-288">Veritabanı yoksa, veritabanı ve şema oluşturur.</span><span class="sxs-lookup"><span data-stu-id="931b6-288">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="931b6-289">`EnsureCreated`veri modeli değişikliklerini işlemek için aşağıdaki iş akışını sağlar:</span><span class="sxs-lookup"><span data-stu-id="931b6-289">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="931b6-290">Veritabanını silin.</span><span class="sxs-lookup"><span data-stu-id="931b6-290">Delete the database.</span></span> <span data-ttu-id="931b6-291">Varolan tüm veriler kaybolur.</span><span class="sxs-lookup"><span data-stu-id="931b6-291">Any existing data is lost.</span></span>
* <span data-ttu-id="931b6-292">Veri modelini değiştirin.</span><span class="sxs-lookup"><span data-stu-id="931b6-292">Change the data model.</span></span> <span data-ttu-id="931b6-293">Örneğin, bir `EmailAddress` alan ekleyin.</span><span class="sxs-lookup"><span data-stu-id="931b6-293">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="931b6-294">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="931b6-294">Run the app.</span></span>
* <span data-ttu-id="931b6-295">`EnsureCreated`yeni şema ile bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="931b6-295">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="931b6-296">Bu iş akışı, verileri korumanız gerekmediğiniz sürece şema hızla geliştiğinde geliştirmede çok erken çalışır.</span><span class="sxs-lookup"><span data-stu-id="931b6-296">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="931b6-297">Veritabanına girilen verilerin korunması gerektiğinde durum farklıdır.</span><span class="sxs-lookup"><span data-stu-id="931b6-297">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="931b6-298">Bu durumda, geçişleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="931b6-298">When that is the case, use migrations.</span></span>

<span data-ttu-id="931b6-299">Öğretici serinin ilerleyen saatlerinde, oluşturulan `EnsureCreated` veritabanını siler ve bunun yerine geçişleri kullanırsınız.</span><span class="sxs-lookup"><span data-stu-id="931b6-299">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="931b6-300">Geçişler kullanılarak `EnsureCreated` oluşturulan bir veritabanı güncelleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="931b6-300">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="931b6-301">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="931b6-301">Test the app</span></span>

* <span data-ttu-id="931b6-302">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="931b6-302">Run the app.</span></span>
* <span data-ttu-id="931b6-303">**Öğrenciler** bağlantısını seçin ve ardından Yeni Oluştur' seçeneğini **belirleyin.**</span><span class="sxs-lookup"><span data-stu-id="931b6-303">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="931b6-304">Düzenle, Ayrıntılar ve Sil bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="931b6-304">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="931b6-305">Veritabanını tohumla</span><span class="sxs-lookup"><span data-stu-id="931b6-305">Seed the database</span></span>

<span data-ttu-id="931b6-306">Yöntem `EnsureCreated` boş bir veritabanı oluşturur.</span><span class="sxs-lookup"><span data-stu-id="931b6-306">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="931b6-307">Bu bölümde, veritabanını test verileriyle dolduran kod ekler.</span><span class="sxs-lookup"><span data-stu-id="931b6-307">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="931b6-308">Aşağıdaki kodile *Veri/DbInitializer.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="931b6-308">Create *Data/DbInitializer.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="931b6-309">Kod, veritabanında öğrenci olup olmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="931b6-309">The code checks if there are any students in the database.</span></span> <span data-ttu-id="931b6-310">Öğrenci yoksa, veritabanına test verileri ekler.</span><span class="sxs-lookup"><span data-stu-id="931b6-310">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="931b6-311">Performansı en iyi duruma getirmek için `List<T>` koleksiyonlar yerine diziler halinde test verilerini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="931b6-311">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="931b6-312">*Program.cs,* aramayı `EnsureCreated` bir `DbInitializer.Initialize` çağrıyla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="931b6-312">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="931b6-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="931b6-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="931b6-314">Çalışıyorsa uygulamayı durdurun ve **Paket Yöneticisi Konsolu'nda** (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="931b6-314">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="931b6-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="931b6-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="931b6-316">Çalışıyorsa uygulamayı durdurun ve *CU.db* dosyasını silin.</span><span class="sxs-lookup"><span data-stu-id="931b6-316">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="931b6-317">Uygulamayı yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="931b6-317">Restart the app.</span></span>

* <span data-ttu-id="931b6-318">Tohumlu verileri görmek için Öğrenciler sayfasını seçin.</span><span class="sxs-lookup"><span data-stu-id="931b6-318">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="931b6-319">Veritabanını görüntüleme</span><span class="sxs-lookup"><span data-stu-id="931b6-319">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="931b6-320">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="931b6-320">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="931b6-321">Visual **Studio'daki** **Görünüm** menüsünden SQL Server Object Explorer'ı (SSOX) açın.</span><span class="sxs-lookup"><span data-stu-id="931b6-321">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="931b6-322">SSOX'ta **SchoolContext-{GUID} > (localdb)\MSSQLLocalDB > Databases'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="931b6-322">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="931b6-323">Veritabanı adı, daha önce sağladığınız bağlam adından artı bir tire ve guid'den oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="931b6-323">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="931b6-324">**Tablolar** düğümlerini genişletin.</span><span class="sxs-lookup"><span data-stu-id="931b6-324">Expand the **Tables** node.</span></span>
* <span data-ttu-id="931b6-325">Oluşturulan sütunları ve tabloya eklenen satırları görmek için **Öğrenci** tablosuna sağ tıklayın ve **Verileri Görüntüle'yi** tıklatın.</span><span class="sxs-lookup"><span data-stu-id="931b6-325">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="931b6-326">**Modelin** tablo şemasını nasıl eşleştebildiğini `Student` görmek `Student` için Öğrenci tablosuna sağ tıklayın ve **Kodu Görüntüle'yi** tıklatın.</span><span class="sxs-lookup"><span data-stu-id="931b6-326">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="931b6-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="931b6-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="931b6-328">Veritabanı şemasını ve tohumlu verileri görüntülemek için SQLite aracınızı kullanın.</span><span class="sxs-lookup"><span data-stu-id="931b6-328">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="931b6-329">Veritabanı dosyası *CU.db* olarak adlandırılır ve proje klasöründe bulunur.</span><span class="sxs-lookup"><span data-stu-id="931b6-329">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="931b6-330">Asynchronous kodu</span><span class="sxs-lookup"><span data-stu-id="931b6-330">Asynchronous code</span></span>

<span data-ttu-id="931b6-331">Asynchronous programlama ASP.NET Core ve EF Core için varsayılan moddur.</span><span class="sxs-lookup"><span data-stu-id="931b6-331">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="931b6-332">Bir web sunucusunda sınırlı sayıda iş parçacığı vardır ve yüksek yük durumlarında kullanılabilir tüm iş parçacıkları kullanılıyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-332">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="931b6-333">Bu durumda, iş parçacıkları serbest bırakılıncaya kadar sunucu yeni istekleri işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-333">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="931b6-334">Senkron kodla, G/Ç'nin tamamlanmasını bekledikleri için birçok iş parçacığı aslında herhangi bir iş yapmasalar da bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-334">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="931b6-335">Bir işlem G/Ç'nin tamamlanmasını beklerken, iş parçacığı sunucunun diğer istekleri işlemek için kullanması için serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="931b6-335">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="931b6-336">Sonuç olarak, eşzamanlı kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucu gecikmeden daha fazla trafiği işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-336">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="931b6-337">Asynchronous kodu çalışma zamanında az miktarda ek yükü tanıtmak yok.</span><span class="sxs-lookup"><span data-stu-id="931b6-337">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="931b6-338">Düşük trafik durumları için, performans isabet ihmal edilebilir, yüksek trafik durumlarda ise, potansiyel performans iyileştirme önemli.</span><span class="sxs-lookup"><span data-stu-id="931b6-338">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="931b6-339">Aşağıdaki kodda, [async](/dotnet/csharp/language-reference/keywords/async) anahtar `Task<T>` kelime, `await` iade değeri, `ToListAsync` anahtar kelime ve yöntem kodu eşzamanlı olarak yürütmek olun.</span><span class="sxs-lookup"><span data-stu-id="931b6-339">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="931b6-340">Anahtar `async` kelime derleyiciye şunları söyler:</span><span class="sxs-lookup"><span data-stu-id="931b6-340">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="931b6-341">Yöntem gövdesinin parçaları için geri arama lar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="931b6-341">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="931b6-342">Döndürülen [Görev](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) nesnesini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="931b6-342">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="931b6-343">İade `Task<T>` türü devam eden çalışmayı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="931b6-343">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="931b6-344">Anahtar `await` kelime derleyicinin yöntemi iki bölüme bölmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="931b6-344">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="931b6-345">İlk bölüm, eş zamanlı olarak başlatılan işlemle sona erer.</span><span class="sxs-lookup"><span data-stu-id="931b6-345">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="931b6-346">İkinci bölüm, işlem tamamlandığında çağrılan bir geri arama yöntemine konur.</span><span class="sxs-lookup"><span data-stu-id="931b6-346">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="931b6-347">`ToListAsync`uzantı yönteminin `ToList` eşzamanlı sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="931b6-347">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="931b6-348">EF Core kullanan eşzamanlı kod yazarken dikkat edilmesi gereken bazı noktalar:</span><span class="sxs-lookup"><span data-stu-id="931b6-348">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="931b6-349">Yalnızca sorguların veya komutların veritabanına gönderilmesine neden olan ifadeler eşsenkronize olarak yürütülür.</span><span class="sxs-lookup"><span data-stu-id="931b6-349">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="931b6-350">Buna `ToListAsync`, `SingleOrDefaultAsync` `FirstOrDefaultAsync`, `SaveChangesAsync`, ve .</span><span class="sxs-lookup"><span data-stu-id="931b6-350">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="931b6-351">Bu sadece bir `IQueryable`değiştirmek ifadeler içermez , `var students = context.Students.Where(s => s.LastName == "Davolio")`gibi .</span><span class="sxs-lookup"><span data-stu-id="931b6-351">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="931b6-352">EF Core bağlamı iş parçacığı güvenli değildir: paralel olarak birden çok işlem yapmaya çalışmayın.</span><span class="sxs-lookup"><span data-stu-id="931b6-352">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="931b6-353">Async kodunun performans avantajlarından yararlanmak için, kitaplık paketlerinin (sayfalama gibi) veritabanına sorgu gönderen EF Core yöntemlerini aramaları durumunda async kullandığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="931b6-353">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="931b6-354">.NET'te asynchronous programlama hakkında daha fazla bilgi için [Async Genel Bakış](/dotnet/standard/async) ve [Asynchronous programlamaya](/dotnet/csharp/programming-guide/concepts/async/)bakın ve sizi bekliyor.</span><span class="sxs-lookup"><span data-stu-id="931b6-354">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="931b6-355">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="931b6-355">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="931b6-356">Sonraki öğretici</span><span class="sxs-lookup"><span data-stu-id="931b6-356">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="931b6-357">Contoso Üniversitesi örnek web uygulaması, Entity Framework (EF) Core kullanarak ASP.NET Core Razor Pages uygulamasının nasıl oluşturulabildiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="931b6-357">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="931b6-358">Örnek uygulama kurgusal bir Contoso Üniversitesi için bir web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="931b6-358">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="931b6-359">Öğrenci kabulü, ders oluşturma ve eğitmen atamaları gibi işlevleri içerir.</span><span class="sxs-lookup"><span data-stu-id="931b6-359">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="931b6-360">Bu sayfa, Contoso Üniversitesi örnek uygulamasının nasıl inşa edilebildiğini açıklayan bir dizi öğreticinin ilkidir.</span><span class="sxs-lookup"><span data-stu-id="931b6-360">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="931b6-361">Tamamlanan uygulamayı indirin veya görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="931b6-361">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="931b6-362">[Talimatları indirin.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="931b6-362">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="931b6-363">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="931b6-363">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="931b6-364">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="931b6-364">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="931b6-365">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="931b6-365">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="931b6-366">[Jilet Sayfaları](xref:razor-pages/index)ile Aşinalık .</span><span class="sxs-lookup"><span data-stu-id="931b6-366">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="931b6-367">Yeni programcılar bu seriye başlamadan önce [Razor Pages ile başlayın](xref:tutorials/razor-pages/razor-pages-start) tamamlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="931b6-367">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="931b6-368">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="931b6-368">Troubleshooting</span></span>

<span data-ttu-id="931b6-369">Çözemediğiniz bir sorunla karşınıza çıkarsa, kodunuzu [tamamlanan projeyle](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)karşılaştırarak genellikle çözümü bulabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="931b6-369">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="931b6-370">Yardım almak için iyi bir yol ASP.NET [Core](https://stackoverflow.com/questions/tagged/asp.net-core) veya [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core)için [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) için bir soru göndererek.</span><span class="sxs-lookup"><span data-stu-id="931b6-370">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="931b6-371">Contoso Üniversitesi web uygulaması</span><span class="sxs-lookup"><span data-stu-id="931b6-371">The Contoso University web app</span></span>

<span data-ttu-id="931b6-372">Bu eğitimlerde yerleşik olan uygulama temel bir üniversite web sitesidir.</span><span class="sxs-lookup"><span data-stu-id="931b6-372">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="931b6-373">Kullanıcılar öğrenci, kurs ve eğitmen bilgilerini görüntüleyebilir ve güncelleyebilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-373">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="931b6-374">Burada öğretici oluşturulan ekranlardan birkaçı vardır.</span><span class="sxs-lookup"><span data-stu-id="931b6-374">Here are a few of the screens created in the tutorial.</span></span>

![Öğrenci Endeksi sayfası](intro/_static/students-index.png)

![Öğrenciler Sayfayı Edit](intro/_static/student-edit.png)

<span data-ttu-id="931b6-377">Bu sitenin UI stili, yerleşik şablonlar tarafından oluşturulana yakındır.</span><span class="sxs-lookup"><span data-stu-id="931b6-377">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="931b6-378">Öğretici odak Razor Pages ile EF Core değil, UI.</span><span class="sxs-lookup"><span data-stu-id="931b6-378">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="931b6-379">ContosoUniversity Razor Pages web uygulamasını oluşturma</span><span class="sxs-lookup"><span data-stu-id="931b6-379">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="931b6-380">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="931b6-380">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="931b6-381">Visual Studio **File** menüsünden **Yeni** > **Proje'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="931b6-381">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="931b6-382">Yeni bir ASP.NET Core Web Uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="931b6-382">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="931b6-383">Proje **ContosoUniversity**adı .</span><span class="sxs-lookup"><span data-stu-id="931b6-383">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="931b6-384">Kod kopyalandığında/yapıştırıldığında ad boşluklarının eşleşmesi için *projecontosoUniversity* adını vermek önemlidir.</span><span class="sxs-lookup"><span data-stu-id="931b6-384">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="931b6-385">Açılır ASP.NET **Core 2.1'i** seçin ve ardından **Web Uygulaması'nı**seçin.</span><span class="sxs-lookup"><span data-stu-id="931b6-385">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="931b6-386">Önceki adımların görüntüleri için [bkz.](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app)</span><span class="sxs-lookup"><span data-stu-id="931b6-386">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="931b6-387">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="931b6-387">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="931b6-388">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="931b6-388">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="931b6-389">Site stilini ayarlama</span><span class="sxs-lookup"><span data-stu-id="931b6-389">Set up the site style</span></span>

<span data-ttu-id="931b6-390">Site menüsünü, düzeni ve ana sayfayı birkaç değişiklik ayarla.</span><span class="sxs-lookup"><span data-stu-id="931b6-390">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="931b6-391">*Sayfaları/Paylaşılan/_Layout.cshtml'i* aşağıdaki değişikliklerle güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="931b6-391">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="931b6-392">"ContosoUniversity"in her oluşumunu "Contoso Üniversitesi" olarak değiştirin.</span><span class="sxs-lookup"><span data-stu-id="931b6-392">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="931b6-393">Üç olay var.</span><span class="sxs-lookup"><span data-stu-id="931b6-393">There are three occurrences.</span></span>

* <span data-ttu-id="931b6-394">**Öğrenciler,** **Kurslar,** Eğitmenler ve **Bölümler**için menü **girişlerini**ekleyin ve **İletişim** menüsü girişini silin.</span><span class="sxs-lookup"><span data-stu-id="931b6-394">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="931b6-395">Değişiklikler vurgulanır.</span><span class="sxs-lookup"><span data-stu-id="931b6-395">The changes are highlighted.</span></span> <span data-ttu-id="931b6-396">(Tüm biçimlendirme *görüntülenmez.)*</span><span class="sxs-lookup"><span data-stu-id="931b6-396">(All the markup is *not* displayed.)</span></span>

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="931b6-397">*Pages/Index.cshtml'de,* ASP.NET ve MVC hakkındaki metni bu uygulamayla ilgili metinle değiştirmek için dosyanın içeriğini aşağıdaki kodla değiştirin:</span><span class="sxs-lookup"><span data-stu-id="931b6-397">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="931b6-398">Veri modelini oluşturma</span><span class="sxs-lookup"><span data-stu-id="931b6-398">Create the data model</span></span>

<span data-ttu-id="931b6-399">Contoso Üniversitesi uygulaması için varlık sınıfları oluşturun.</span><span class="sxs-lookup"><span data-stu-id="931b6-399">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="931b6-400">Aşağıdaki üç varlıkla başlayın:</span><span class="sxs-lookup"><span data-stu-id="931b6-400">Start with the following three entities:</span></span>

![Ders-Kayıt-Öğrenci veri modeli diyagramı](intro/_static/data-model-diagram.png)

<span data-ttu-id="931b6-402">Varlıklarla arasında `Student` `Enrollment` bir-çok ilişki vardır.</span><span class="sxs-lookup"><span data-stu-id="931b6-402">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="931b6-403">Varlıklarla arasında `Course` `Enrollment` bir-çok ilişki vardır.</span><span class="sxs-lookup"><span data-stu-id="931b6-403">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="931b6-404">Bir öğrenci herhangi bir sayıda kursa kaydolabilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-404">A student can enroll in any number of courses.</span></span> <span data-ttu-id="931b6-405">Bir kursa herhangi bir sayıda öğrenci kaydolabilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-405">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="931b6-406">Aşağıdaki bölümlerde, bu varlıkların her biri için bir sınıf oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="931b6-406">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="931b6-407">Öğrenci varlığı</span><span class="sxs-lookup"><span data-stu-id="931b6-407">The Student entity</span></span>

![Öğrenci varlık diyagramı](intro/_static/student-entity.png)

<span data-ttu-id="931b6-409">*Modeller* klasörü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="931b6-409">Create a *Models* folder.</span></span> <span data-ttu-id="931b6-410">*Modeller* klasöründe, aşağıdaki kodla *Student.cs* adlı bir sınıf dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="931b6-410">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="931b6-411">Özellik, `ID` veritabanı (DB) tablosunun bu sınıfa karşılık gelen birincil anahtar sütunu olur.</span><span class="sxs-lookup"><span data-stu-id="931b6-411">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="931b6-412">Varsayılan olarak, EF Core adlı veya `ID` `classnameID` birincil anahtar olarak bir özelliği yorumlar.</span><span class="sxs-lookup"><span data-stu-id="931b6-412">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="931b6-413">In `classnameID` `classname` , sınıfın adıdır.</span><span class="sxs-lookup"><span data-stu-id="931b6-413">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="931b6-414">Otomatik olarak tanınan birincil `StudentID` anahtar seçeneği yukarıdaki örnektedir.</span><span class="sxs-lookup"><span data-stu-id="931b6-414">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="931b6-415">Özellik `Enrollments` bir [navigasyon özelliğidir.](/ef/core/modeling/relationships)</span><span class="sxs-lookup"><span data-stu-id="931b6-415">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="931b6-416">Gezinti özellikleri, bu varlıkla ilişkili diğer varlıklara bağlanır.</span><span class="sxs-lookup"><span data-stu-id="931b6-416">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="931b6-417">Bu durumda, `Enrollments` bir `Student entity` özelliği ile ilgili `Enrollment` tüm varlıkların `Student`tutar.</span><span class="sxs-lookup"><span data-stu-id="931b6-417">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="931b6-418">Örneğin, DB'deki bir Öğrenci satırında ilgili iki `Enrollments` Kayıt satırı varsa, gezinti özelliği bu iki `Enrollment` varlığı içerir.</span><span class="sxs-lookup"><span data-stu-id="931b6-418">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="931b6-419">İlişkili `Enrollment` satır, `StudentID` sütundaki öğrencinin birincil anahtar değerini içeren bir satırdır.</span><span class="sxs-lookup"><span data-stu-id="931b6-419">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="931b6-420">Örneğin, ID=1'e sahip öğrencinin `Enrollment` tabloda iki satırı olduğunu varsayalım.</span><span class="sxs-lookup"><span data-stu-id="931b6-420">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="931b6-421">Tabloda `Enrollment` = 1 `StudentID` ile iki satır vardır.</span><span class="sxs-lookup"><span data-stu-id="931b6-421">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="931b6-422">`StudentID``Student` tablodaki öğrenciyi `Enrollment` belirten yabancı bir anahtardır.</span><span class="sxs-lookup"><span data-stu-id="931b6-422">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="931b6-423">Bir gezinti özelliği birden çok varlık tutabiliyorsa, gezinti `ICollection<T>`özelliği ' nin liste türü olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="931b6-423">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="931b6-424">`ICollection<T>`belirtilebilir, ya da gibi `List<T>` `HashSet<T>`bir tür ya da .</span><span class="sxs-lookup"><span data-stu-id="931b6-424">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="931b6-425">Kullanıldığında, `ICollection<T>` EF Core varsayılan `HashSet<T>` olarak bir koleksiyon oluşturur.</span><span class="sxs-lookup"><span data-stu-id="931b6-425">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="931b6-426">Birden çok varlığı barındıran gezinti özellikleri, çok-çok ve bir-çok ilişkisinden gelir.</span><span class="sxs-lookup"><span data-stu-id="931b6-426">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="931b6-427">Kayıt varlığı</span><span class="sxs-lookup"><span data-stu-id="931b6-427">The Enrollment entity</span></span>

![Kayıt varlık diyagramı](intro/_static/enrollment-entity.png)

<span data-ttu-id="931b6-429">*Modeller* klasöründe, aşağıdaki kodla *Enrollment.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="931b6-429">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="931b6-430">Özellik `EnrollmentID` birincil anahtardır.</span><span class="sxs-lookup"><span data-stu-id="931b6-430">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="931b6-431">Bu `Student` varlık, `classnameID` varlık `ID` gibi değil deseni kullanır.</span><span class="sxs-lookup"><span data-stu-id="931b6-431">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="931b6-432">Genellikle geliştiriciler bir desen seçin ve veri modeli boyunca kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="931b6-432">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="931b6-433">Daha sonraki bir öğreticide, sınıf adı olmadan kimlik kullanılması, veri modelinde devralmayı daha kolay uygulamak için gösterilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-433">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="931b6-434">Özellik `Grade` bir `enum`.</span><span class="sxs-lookup"><span data-stu-id="931b6-434">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="931b6-435">Tür bildiriminden `Grade` sonraki soru işareti `Grade` özelliğin geçersiz olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="931b6-435">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="931b6-436">Sıfır notundan farklı olan bir not - null, bir notun bilinmediği veya henüz atanmadığı anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="931b6-436">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="931b6-437">Özellik `StudentID` yabancı bir anahtardır ve ilgili `Student`navigasyon özelliği .</span><span class="sxs-lookup"><span data-stu-id="931b6-437">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="931b6-438">Bir `Enrollment` varlık tek `Student` bir varlıkla ilişkilidir, `Student` bu nedenle özellik tek bir varlık içerir.</span><span class="sxs-lookup"><span data-stu-id="931b6-438">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="931b6-439">Varlık, `Student` birden çok `Student.Enrollments` `Enrollment` varlık içeren gezinti özelliğinden farklıdır.</span><span class="sxs-lookup"><span data-stu-id="931b6-439">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="931b6-440">Özellik `CourseID` yabancı bir anahtardır ve ilgili `Course`navigasyon özelliği .</span><span class="sxs-lookup"><span data-stu-id="931b6-440">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="931b6-441">Bir `Enrollment` varlık tek `Course` bir varlıkla ilişkilidir.</span><span class="sxs-lookup"><span data-stu-id="931b6-441">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="931b6-442">EF Core, bir özelliği yabancı `<navigation property name><primary key property name>`bir anahtar olarak yorumluyorsa.</span><span class="sxs-lookup"><span data-stu-id="931b6-442">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="931b6-443">Örneğin,`StudentID` `Student` `Student` varlığın birincil anahtarı . `ID`</span><span class="sxs-lookup"><span data-stu-id="931b6-443">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="931b6-444">Yabancı anahtar özellikleri de `<primary key property name>`adlandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-444">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="931b6-445">Örneğin, `CourseID` varlığın `Course` birincil anahtarı `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="931b6-445">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="931b6-446">Kurs varlığı</span><span class="sxs-lookup"><span data-stu-id="931b6-446">The Course entity</span></span>

![Ders varlık diyagramı](intro/_static/course-entity.png)

<span data-ttu-id="931b6-448">*Modeller* klasöründe aşağıdaki kodla *Course.cs* oluşturun:</span><span class="sxs-lookup"><span data-stu-id="931b6-448">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="931b6-449">Tesis `Enrollments` bir navigasyon özelliğidir.</span><span class="sxs-lookup"><span data-stu-id="931b6-449">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="931b6-450">Bir `Course` varlık herhangi bir sayıda `Enrollment` varlıkla ilişkili olabilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-450">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="931b6-451">Öznitelik, `DatabaseGenerated` uygulamanın DB'nin oluşturması yerine birincil anahtarı belirtmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="931b6-451">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="931b6-452">İskele öğrenci modeli</span><span class="sxs-lookup"><span data-stu-id="931b6-452">Scaffold the student model</span></span>

<span data-ttu-id="931b6-453">Bu bölümde, öğrenci modeli iskeleli.</span><span class="sxs-lookup"><span data-stu-id="931b6-453">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="931b6-454">Diğer bir zamanda, iskele aracı öğrenci modeli için Oluştur, Oku, Güncelleştir ve Sil (CRUD) işlemleri için sayfalar oluşturur.</span><span class="sxs-lookup"><span data-stu-id="931b6-454">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="931b6-455">Projeyi derleyin.</span><span class="sxs-lookup"><span data-stu-id="931b6-455">Build the project.</span></span>
* <span data-ttu-id="931b6-456">*Sayfalar/Öğrenciler* klasörünü oluşturun.</span><span class="sxs-lookup"><span data-stu-id="931b6-456">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="931b6-457">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="931b6-457">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="931b6-458">**Solution Explorer'da,** Yeni **İskele**Öğesi **Ekle** > > *Sayfalar/Öğrenciler* klasörüne sağ tıklayın.</span><span class="sxs-lookup"><span data-stu-id="931b6-458">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="931b6-459">İskele **Ekle** iletişim kutusunda, **Entity Framework (CRUD)** > **ADD**kullanarak Jilet Sayfaları'nı seçin.</span><span class="sxs-lookup"><span data-stu-id="931b6-459">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="931b6-460">Varlık **Çerçevesi (CRUD)** iletişim kutusunu kullanarak Jilet Sayfaları Ekle'yi tamamlayın:</span><span class="sxs-lookup"><span data-stu-id="931b6-460">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="931b6-461">Model **sınıfı** açılır açılır, **Öğrenci (ContosoUniversity.Models)** seçin.</span><span class="sxs-lookup"><span data-stu-id="931b6-461">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="931b6-462">Veri **bağlamında sınıf** satırında(artı) işaretini **+** seçin ve oluşturulan adı **ContosoUniversity.Models.SchoolContext olarak değiştirin.**</span><span class="sxs-lookup"><span data-stu-id="931b6-462">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="931b6-463">Veri **bağlamında sınıf** açılır, **ContosoUniversity.Models.SchoolContext'ı** seçin</span><span class="sxs-lookup"><span data-stu-id="931b6-463">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="931b6-464">**Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="931b6-464">Select **Add**.</span></span>

![CRUD iletişim kutusu](intro/_static/s1.png)

<span data-ttu-id="931b6-466">Bir önceki adımla ilgili bir sorununvarsa [film modelini İskele'ye](xref:tutorials/razor-pages/model#scaffold-the-movie-model) bakın.</span><span class="sxs-lookup"><span data-stu-id="931b6-466">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="931b6-467">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="931b6-467">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="931b6-468">Öğrenci modelini iskeleye getirmek için aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="931b6-468">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="931b6-469">İskele işlemi aşağıdaki dosyaları oluşturdu ve değiştirdi:</span><span class="sxs-lookup"><span data-stu-id="931b6-469">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="931b6-470">Oluşturulan dosyalar</span><span class="sxs-lookup"><span data-stu-id="931b6-470">Files created</span></span>

* <span data-ttu-id="931b6-471">*Sayfalar / Öğrenciler* Oluştur, Sil, Ayrıntılar, Düzenle, Dizin.</span><span class="sxs-lookup"><span data-stu-id="931b6-471">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="931b6-472">*Veri/SchoolContext.cs*</span><span class="sxs-lookup"><span data-stu-id="931b6-472">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="931b6-473">Dosya güncellemeleri</span><span class="sxs-lookup"><span data-stu-id="931b6-473">File updates</span></span>

* <span data-ttu-id="931b6-474">*Startup.cs* : Bu dosyadaki değişiklikler sonraki bölümde ayrıntılı olarak açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="931b6-474">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="931b6-475">*appsettings.json* : Yerel bir veritabanına bağlanmak için kullanılan bağlantı dizesi eklenir.</span><span class="sxs-lookup"><span data-stu-id="931b6-475">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="931b6-476">Bağımlılık enjeksiyonu ile kayıtlı bağlamı inceleyin</span><span class="sxs-lookup"><span data-stu-id="931b6-476">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="931b6-477">ASP.NET Core [bağımlılık enjeksiyonu](xref:fundamentals/dependency-injection)ile inşa edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="931b6-477">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="931b6-478">Hizmetler (EF Core DB bağlamı gibi) uygulama başlatma sırasında bağımlılık enjeksiyonu ile kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-478">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="931b6-479">Bu hizmetleri gerektiren bileşenlere (Jilet Sayfaları gibi) bu hizmetler yapıcı parametreler aracılığıyla sağlanır.</span><span class="sxs-lookup"><span data-stu-id="931b6-479">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="931b6-480">DB bağlam örneğini alan yapıcı kod daha sonra öğreticide gösterilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-480">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="931b6-481">İskele aracı otomatik olarak bir DB Bağlamı oluşturdu ve bağımlılık enjeksiyon konteyneri ile kaydetti.</span><span class="sxs-lookup"><span data-stu-id="931b6-481">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="931b6-482">yöntemi Startup.cs inceleyin. *Startup.cs* `ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="931b6-482">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="931b6-483">Vurgulanan satır iskele tarafından eklendi:</span><span class="sxs-lookup"><span data-stu-id="931b6-483">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="931b6-484">Bağlantı dizesinin adı, [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) nesnesindeki bir yöntem çağırılarak içeriğe aktarılır.</span><span class="sxs-lookup"><span data-stu-id="931b6-484">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="931b6-485">Yerel geliştirme [için, ASP.NET Core yapılandırma sistemi](xref:fundamentals/configuration/index) *appsettings.json* dosyasından bağlantı dizesini okur.</span><span class="sxs-lookup"><span data-stu-id="931b6-485">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="931b6-486">Ana güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="931b6-486">Update main</span></span>

<span data-ttu-id="931b6-487">*Program.cs,* `Main` aşağıdakileri yapmak için yöntemi değiştirin:</span><span class="sxs-lookup"><span data-stu-id="931b6-487">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="931b6-488">Bağımlılık enjeksiyon kapsayıcısından bir DB bağlam örneği alın.</span><span class="sxs-lookup"><span data-stu-id="931b6-488">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="931b6-489">[EnsureCreated'ı](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)arayın.</span><span class="sxs-lookup"><span data-stu-id="931b6-489">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="931b6-490">Yöntem tamamlandığında bağlamı `EnsureCreated` atın.</span><span class="sxs-lookup"><span data-stu-id="931b6-490">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="931b6-491">Aşağıdaki kod güncelleştirilmiş *Program.cs* dosyasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="931b6-491">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="931b6-492">`EnsureCreated`bağlam için veritabanının var olmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="931b6-492">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="931b6-493">Varsa, hiçbir işlem yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="931b6-493">If it exists, no action is taken.</span></span> <span data-ttu-id="931b6-494">Yoksa, veritabanı ve tüm şema oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="931b6-494">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="931b6-495">`EnsureCreated`veritabanını oluşturmak için geçişler kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="931b6-495">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="931b6-496">Daha `EnsureCreated` sonra geçişler kullanılarak oluşturulan bir veritabanı güncelleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="931b6-496">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="931b6-497">`EnsureCreated`aşağıdaki iş akışını sağlayan uygulama başlangıcında çağrılır:</span><span class="sxs-lookup"><span data-stu-id="931b6-497">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="931b6-498">DB'yi silin.</span><span class="sxs-lookup"><span data-stu-id="931b6-498">Delete the DB.</span></span>
* <span data-ttu-id="931b6-499">DB şemasını değiştirin (örneğin, `EmailAddress` bir alan ekleyin).</span><span class="sxs-lookup"><span data-stu-id="931b6-499">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="931b6-500">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="931b6-500">Run the app.</span></span>
* <span data-ttu-id="931b6-501">`EnsureCreated``EmailAddress` sütunile bir DB oluşturur.</span><span class="sxs-lookup"><span data-stu-id="931b6-501">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="931b6-502">`EnsureCreated`şema hızla geliştiğinde gelişmenin erken dönemlerinde uygundur.</span><span class="sxs-lookup"><span data-stu-id="931b6-502">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="931b6-503">Daha sonra öğreticide DB silinir ve geçişler kullanılır.</span><span class="sxs-lookup"><span data-stu-id="931b6-503">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="931b6-504">Uygulamayı test edin</span><span class="sxs-lookup"><span data-stu-id="931b6-504">Test the app</span></span>

<span data-ttu-id="931b6-505">Uygulamayı çalıştırın ve çerez ilkesini kabul edin.</span><span class="sxs-lookup"><span data-stu-id="931b6-505">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="931b6-506">Bu uygulama kişisel bilgileri saklamaz.</span><span class="sxs-lookup"><span data-stu-id="931b6-506">This app doesn't keep personal information.</span></span> <span data-ttu-id="931b6-507">Ab Genel Veri Koruma [Yönetmeliği (GDPR) desteğinde](xref:security/gdpr)çerez politikası hakkında bilgi edinebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="931b6-507">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="931b6-508">**Öğrenciler** bağlantısını seçin ve ardından Yeni Oluştur' seçeneğini **belirleyin.**</span><span class="sxs-lookup"><span data-stu-id="931b6-508">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="931b6-509">Düzenle, Ayrıntılar ve Sil bağlantılarını test edin.</span><span class="sxs-lookup"><span data-stu-id="931b6-509">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="931b6-510">SchoolContext DB bağlamını inceleyin</span><span class="sxs-lookup"><span data-stu-id="931b6-510">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="931b6-511">Belirli bir veri modeli için EF Core işlevini koordine eden ana sınıf DB bağlam sınıfıdır.</span><span class="sxs-lookup"><span data-stu-id="931b6-511">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="931b6-512">Veri bağlamı [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)türetilmiştir.</span><span class="sxs-lookup"><span data-stu-id="931b6-512">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="931b6-513">Veri bağlamı, veri modeline hangi varlıkların dahil edildiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="931b6-513">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="931b6-514">Bu projede sınıfadlandırılmış. `SchoolContext`</span><span class="sxs-lookup"><span data-stu-id="931b6-514">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="931b6-515">Aşağıdaki kodla *SchoolContext.cs* güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="931b6-515">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="931b6-516">Vurgulanan kod, her varlık kümesi için bir [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) özelliği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="931b6-516">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="931b6-517">EF Çekirdek terminolojisinde:</span><span class="sxs-lookup"><span data-stu-id="931b6-517">In EF Core terminology:</span></span>

* <span data-ttu-id="931b6-518">Bir varlık kümesi genellikle bir DB tablosuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="931b6-518">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="931b6-519">Bir varlık tablodaki bir satıra karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="931b6-519">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="931b6-520">`DbSet<Enrollment>`ve `DbSet<Course>` atlanabilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-520">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="931b6-521">`Student` VARLıK `Enrollment` varlığa, `Enrollment` varlık da `Course` varlığa atıfta bulunduğundan, EF Core bunları dolaylı olarak içerir.</span><span class="sxs-lookup"><span data-stu-id="931b6-521">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="931b6-522">Bu öğretici için, `DbSet<Course>` tutmak `SchoolContext` `DbSet<Enrollment>` ve .</span><span class="sxs-lookup"><span data-stu-id="931b6-522">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="931b6-523">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="931b6-523">SQL Server Express LocalDB</span></span>

<span data-ttu-id="931b6-524">Bağlantı dizesi [SQL Server LocalDB'yi](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)belirtir.</span><span class="sxs-lookup"><span data-stu-id="931b6-524">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="931b6-525">LocalDB, SQL Server Express Veritabanı Altyapısının hafif bir sürümüdür ve üretim kullanımı için değil, uygulama geliştirme için tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="931b6-525">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="931b6-526">LocalDB isteğe bağlı olarak başlar ve kullanıcı modunda çalışır, bu nedenle karmaşık bir yapılandırma yoktur.</span><span class="sxs-lookup"><span data-stu-id="931b6-526">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="931b6-527">Varsayılan olarak, LocalDB `C:/Users/<user>` dizinde *.mdf* DB dosyaları oluşturur.</span><span class="sxs-lookup"><span data-stu-id="931b6-527">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="931b6-528">Test verileriyle DB'yi başlatmaya kod ekleme</span><span class="sxs-lookup"><span data-stu-id="931b6-528">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="931b6-529">EF Core boş bir DB oluşturur.</span><span class="sxs-lookup"><span data-stu-id="931b6-529">EF Core creates an empty DB.</span></span> <span data-ttu-id="931b6-530">Bu bölümde, `Initialize` test verileri ile doldurmak için bir yöntem yazılır.</span><span class="sxs-lookup"><span data-stu-id="931b6-530">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="931b6-531">*Veri* *klasöründe, DbInitializer.cs* adında yeni bir sınıf dosyası oluşturun ve aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="931b6-531">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="931b6-532">Not: Önceki kod, `Models` `Data`ad alanı`namespace ContosoUniversity.Models`( ) yerine .</span><span class="sxs-lookup"><span data-stu-id="931b6-532">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="931b6-533">`Models`iskele tarafından oluşturulan kodla tutarlıdır.</span><span class="sxs-lookup"><span data-stu-id="931b6-533">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="931b6-534">Daha fazla bilgi için [bu GitHub iskele sorununa](https://github.com/aspnet/Scaffolding/issues/822)bakın.</span><span class="sxs-lookup"><span data-stu-id="931b6-534">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="931b6-535">Kod, DB'de öğrenci olup olmadığını kontrol eder.</span><span class="sxs-lookup"><span data-stu-id="931b6-535">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="931b6-536">DB'de öğrenci yoksa, DB test verileriyle başolarak başolarak başlatEdilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-536">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="931b6-537">Performansı en iyi duruma getirmek `List<T>` için test verilerini koleksiyonlar yerine dizilere yükler.</span><span class="sxs-lookup"><span data-stu-id="931b6-537">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="931b6-538">Yöntem `EnsureCreated` otomatik olarak DB bağlamı için DB oluşturur.</span><span class="sxs-lookup"><span data-stu-id="931b6-538">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="931b6-539">DB varsa, `EnsureCreated` DB'yi değiştirmeden döndürür.</span><span class="sxs-lookup"><span data-stu-id="931b6-539">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="931b6-540">Program.cs *Program.cs*yılında, `Main` aramak `Initialize`için yöntemi değiştirmek:</span><span class="sxs-lookup"><span data-stu-id="931b6-540">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="931b6-541">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="931b6-541">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="931b6-542">Çalışıyorsa uygulamayı durdurun ve **Paket Yöneticisi Konsolu'nda** (PMC) aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="931b6-542">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="931b6-543">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="931b6-543">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="931b6-544">Çalışıyorsa uygulamayı durdurun ve *CU.db* dosyasını silin.</span><span class="sxs-lookup"><span data-stu-id="931b6-544">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="931b6-545">DB'yi görüntüleyin</span><span class="sxs-lookup"><span data-stu-id="931b6-545">View the DB</span></span>

<span data-ttu-id="931b6-546">Veritabanı adı, daha önce sağladığınız bağlam adından artı bir tire ve guid'den oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="931b6-546">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="931b6-547">Böylece, veritabanı adı "SchoolContext-{GUID}" olacaktır.</span><span class="sxs-lookup"><span data-stu-id="931b6-547">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="931b6-548">GUID her kullanıcı için farklı olacaktır.</span><span class="sxs-lookup"><span data-stu-id="931b6-548">The GUID will be different for each user.</span></span>
<span data-ttu-id="931b6-549">Visual **Studio'daki** **Görünüm** menüsünden SQL Server Object Explorer'ı (SSOX) açın.</span><span class="sxs-lookup"><span data-stu-id="931b6-549">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="931b6-550">SSOX'ta **(localdb)\MSSQLLocalDB > Veritabanları > SchoolContext-{GUID}** seçeneğini tıklayın.</span><span class="sxs-lookup"><span data-stu-id="931b6-550">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="931b6-551">**Tablolar** düğümlerini genişletin.</span><span class="sxs-lookup"><span data-stu-id="931b6-551">Expand the **Tables** node.</span></span>

<span data-ttu-id="931b6-552">Oluşturulan sütunları ve tabloya eklenen satırları görmek için **Öğrenci** tablosuna sağ tıklayın ve **Verileri Görüntüle'yi** tıklatın.</span><span class="sxs-lookup"><span data-stu-id="931b6-552">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="931b6-553">Asynchronous kodu</span><span class="sxs-lookup"><span data-stu-id="931b6-553">Asynchronous code</span></span>

<span data-ttu-id="931b6-554">Asynchronous programlama ASP.NET Core ve EF Core için varsayılan moddur.</span><span class="sxs-lookup"><span data-stu-id="931b6-554">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="931b6-555">Bir web sunucusunda sınırlı sayıda iş parçacığı vardır ve yüksek yük durumlarında kullanılabilir tüm iş parçacıkları kullanılıyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-555">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="931b6-556">Bu durumda, iş parçacıkları serbest bırakılıncaya kadar sunucu yeni istekleri işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-556">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="931b6-557">Senkron kodla, G/Ç'nin tamamlanmasını bekledikleri için birçok iş parçacığı aslında herhangi bir iş yapmasalar da bağlanabilir.</span><span class="sxs-lookup"><span data-stu-id="931b6-557">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="931b6-558">Bir işlem G/Ç'nin tamamlanmasını beklerken, iş parçacığı sunucunun diğer istekleri işlemek için kullanması için serbest bırakılır.</span><span class="sxs-lookup"><span data-stu-id="931b6-558">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="931b6-559">Sonuç olarak, eşzamanlı kod sunucu kaynaklarının daha verimli kullanılmasını sağlar ve sunucunun gecikmeden daha fazla trafiği işlemesi sağlanır.</span><span class="sxs-lookup"><span data-stu-id="931b6-559">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="931b6-560">Asynchronous kodu çalışma zamanında az miktarda ek yükü tanıtmak yok.</span><span class="sxs-lookup"><span data-stu-id="931b6-560">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="931b6-561">Düşük trafik durumları için, performans isabet ihmal edilebilir, yüksek trafik durumlarda ise, potansiyel performans iyileştirme önemli.</span><span class="sxs-lookup"><span data-stu-id="931b6-561">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="931b6-562">Aşağıdaki kodda, [async](/dotnet/csharp/language-reference/keywords/async) anahtar `Task<T>` kelime, `await` iade değeri, `ToListAsync` anahtar kelime ve yöntem kodu eşzamanlı olarak yürütmek olun.</span><span class="sxs-lookup"><span data-stu-id="931b6-562">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="931b6-563">Anahtar `async` kelime derleyiciye şunları söyler:</span><span class="sxs-lookup"><span data-stu-id="931b6-563">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="931b6-564">Yöntem gövdesinin parçaları için geri arama lar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="931b6-564">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="931b6-565">Döndürülen [Görev](/dotnet/api/system.threading.tasks.task) nesnesini otomatik olarak oluşturun.</span><span class="sxs-lookup"><span data-stu-id="931b6-565">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="931b6-566">Daha fazla bilgi için [Görev İade Türü'ne](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)bakın.</span><span class="sxs-lookup"><span data-stu-id="931b6-566">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="931b6-567">Örtük iade `Task` türü devam eden çalışmayı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="931b6-567">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="931b6-568">Anahtar `await` kelime derleyicinin yöntemi iki bölüme bölmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="931b6-568">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="931b6-569">İlk bölüm, eş zamanlı olarak başlatılan işlemle sona erer.</span><span class="sxs-lookup"><span data-stu-id="931b6-569">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="931b6-570">İkinci bölüm, işlem tamamlandığında çağrılan bir geri arama yöntemine konur.</span><span class="sxs-lookup"><span data-stu-id="931b6-570">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="931b6-571">`ToListAsync`uzantı yönteminin `ToList` eşzamanlı sürümüdür.</span><span class="sxs-lookup"><span data-stu-id="931b6-571">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="931b6-572">EF Core kullanan eşzamanlı kod yazarken dikkat edilmesi gereken bazı noktalar:</span><span class="sxs-lookup"><span data-stu-id="931b6-572">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="931b6-573">Yalnızca Sorguların veya komutların DB'ye gönderilmesine neden olan ifadeler eşsenkronize olarak yürütülür.</span><span class="sxs-lookup"><span data-stu-id="931b6-573">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="931b6-574">Buna `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, `SaveChangesAsync`ve .</span><span class="sxs-lookup"><span data-stu-id="931b6-574">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="931b6-575">Bu sadece bir `IQueryable`değiştirmek ifadeler içermez , `var students = context.Students.Where(s => s.LastName == "Davolio")`gibi .</span><span class="sxs-lookup"><span data-stu-id="931b6-575">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="931b6-576">EF Core bağlamı iş parçacığı güvenli değildir: paralel olarak birden çok işlem yapmaya çalışmayın.</span><span class="sxs-lookup"><span data-stu-id="931b6-576">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="931b6-577">Async kodunun performans avantajlarından yararlanmak için, kitaplık paketlerinin (sayfalama gibi) DB'ye sorgu gönderen EF Core yöntemlerini aramaları durumunda async kullandığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="931b6-577">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="931b6-578">.NET'te asynchronous programlama hakkında daha fazla bilgi için [Async Genel Bakış](/dotnet/standard/async) ve [Asynchronous programlamaya](/dotnet/csharp/programming-guide/concepts/async/)bakın ve sizi bekliyor.</span><span class="sxs-lookup"><span data-stu-id="931b6-578">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="931b6-579">Bir sonraki öğreticide, temel CRUD (oluşturma, okuma, güncelleme, silme) işlemleri incelenir.</span><span class="sxs-lookup"><span data-stu-id="931b6-579">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="931b6-580">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="931b6-580">Additional resources</span></span>

* [<span data-ttu-id="931b6-581">Bu öğreticinin YouTube sürümü</span><span class="sxs-lookup"><span data-stu-id="931b6-581">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="931b6-582">Sonraki</span><span class="sxs-lookup"><span data-stu-id="931b6-582">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
