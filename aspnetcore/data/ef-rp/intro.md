---
<span data-ttu-id="ad742-101">title: Razor stránky s Entity Framework Core v ASP.NET Core – kurz 1 z 8 autor: Rick-Anderson Description: ukazuje, jak vytvořit aplikaci Razor Pages pomocí Entity Framework Core MS. Author: Riande MS. Custom: MVC, seodec18 "ms. Date: 09/26/2019 No-Loc: [Blazor," identity "," Pojďme šifrovat ", Razor, signaler] UID: data/EF-RP/Úvod</span><span class="sxs-lookup"><span data-stu-id="ad742-101">title: Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8 author: rick-anderson description: Shows how to create a Razor Pages app using Entity Framework Core ms.author: riande ms.custom: "mvc, seodec18" ms.date: 09/26/2019 no-loc: [Blazor, "Identity", "Let's Encrypt", Razor, SignalR] uid: data/ef-rp/intro</span></span>
---

# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a><span data-ttu-id="ad742-102">Razor Pages s Entity Framework Core v ASP.NET Core – kurz 1 z 8</span><span class="sxs-lookup"><span data-stu-id="ad742-102">Razor Pages with Entity Framework Core in ASP.NET Core - Tutorial 1 of 8</span></span>

<span data-ttu-id="ad742-103">[Dykstra](https://github.com/tdykstra) a [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ad742-103">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ad742-104">Toto je první v sérii kurzů, které ukazují, jak používat Entity Framework (EF) jádro v aplikaci [ASP.NET Core Razor Pages](xref:razor-pages/index) .</span><span class="sxs-lookup"><span data-stu-id="ad742-104">This is the first in a series of tutorials that show how to use Entity Framework (EF) Core in an [ASP.NET Core Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="ad742-105">Kurzy vytvářejí web pro fiktivní univerzitě společnosti Contoso.</span><span class="sxs-lookup"><span data-stu-id="ad742-105">The tutorials build a web site for a fictional Contoso University.</span></span> <span data-ttu-id="ad742-106">Tato lokalita obsahuje funkce, jako je například využití studenta, vytváření kurzů a přiřazení instruktorů.</span><span class="sxs-lookup"><span data-stu-id="ad742-106">The site includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="ad742-107">Kurz používá první přístup ke kódu.</span><span class="sxs-lookup"><span data-stu-id="ad742-107">The tutorial uses the code first approach.</span></span> <span data-ttu-id="ad742-108">Informace o tomto kurzu s použitím databáze prvního přístupu najdete v [tomto problému GitHubu](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span><span class="sxs-lookup"><span data-stu-id="ad742-108">For information on following this tutorial using the database first approach, see [this Github issue](https://github.com/dotnet/AspNetCore.Docs/issues/16897).</span></span>

[<span data-ttu-id="ad742-109">Stažení nebo zobrazení dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="ad742-109">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="ad742-110">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="ad742-110">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ad742-111">Požadavky</span><span class="sxs-lookup"><span data-stu-id="ad742-111">Prerequisites</span></span>

* <span data-ttu-id="ad742-112">Pokud s Razor Pages teprve začínáte, Projděte si kurz [Začínáme s Razor Pagesm](xref:tutorials/razor-pages/razor-pages-start) , než začnete s tímto.</span><span class="sxs-lookup"><span data-stu-id="ad742-112">If you're new to Razor Pages, go through the [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) tutorial series before starting this one.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad742-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad742-113">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[VS prereqs](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad742-114">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad742-114">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[VS Code prereqs](~/includes/net-core-prereqs-vsc-3.0.md)]

---

## <a name="database-engines"></a><span data-ttu-id="ad742-115">Databázové moduly</span><span class="sxs-lookup"><span data-stu-id="ad742-115">Database engines</span></span>

<span data-ttu-id="ad742-116">Pokyny pro Visual Studio používají [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), verzi SQL Server Express, která se spouští jenom v systému Windows.</span><span class="sxs-lookup"><span data-stu-id="ad742-116">The Visual Studio instructions use [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), a version of SQL Server Express that runs only on Windows.</span></span>

<span data-ttu-id="ad742-117">Pokyny pro [Visual Studio Code používají nástroj](https://www.sqlite.org/)pro databázový stroj pro různé platformy.</span><span class="sxs-lookup"><span data-stu-id="ad742-117">The Visual Studio Code instructions use [SQLite](https://www.sqlite.org/), a cross-platform database engine.</span></span>

<span data-ttu-id="ad742-118">Pokud se rozhodnete použít SQLite, Stáhněte a nainstalujte nástroj třetí strany pro správu a zobrazení databáze SQLite, jako je například [prohlížeč databáze pro SQLite](https://sqlitebrowser.org/).</span><span class="sxs-lookup"><span data-stu-id="ad742-118">If you choose to use SQLite, download and install a third-party tool for managing and viewing a SQLite database, such as [DB Browser for SQLite](https://sqlitebrowser.org/).</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="ad742-119">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="ad742-119">Troubleshooting</span></span>

<span data-ttu-id="ad742-120">Pokud narazíte na problém, který nemůžete vyřešit, porovnejte kód s [dokončeným projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="ad742-120">If you run into a problem you can't resolve, compare your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="ad742-121">Dobrý způsob, jak získat pomoc, je odeslání otázky do StackOverflow.com s použitím [značky ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [značky EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="ad742-121">A good way to get help is by posting a question to StackOverflow.com, using the [ASP.NET Core tag](https://stackoverflow.com/questions/tagged/asp.net-core) or the [EF Core tag](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-sample-app"></a><span data-ttu-id="ad742-122">Ukázková aplikace</span><span class="sxs-lookup"><span data-stu-id="ad742-122">The sample app</span></span>

<span data-ttu-id="ad742-123">Aplikace sestavená v těchto kurzech je základním webem na univerzitě.</span><span class="sxs-lookup"><span data-stu-id="ad742-123">The app built in these tutorials is a basic university web site.</span></span> <span data-ttu-id="ad742-124">Uživatelé můžou zobrazit a aktualizovat informace o studentech, kurzech a instruktorech.</span><span class="sxs-lookup"><span data-stu-id="ad742-124">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="ad742-125">Tady je několik obrazovek vytvořených v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="ad742-125">Here are a few of the screens created in the tutorial.</span></span>

![Stránka indexu studentů](intro/_static/students-index30.png)

![Stránka pro úpravy studentů](intro/_static/student-edit30.png)

<span data-ttu-id="ad742-128">Styl uživatelského rozhraní tohoto webu je založen na předdefinovaných šablonách projektů.</span><span class="sxs-lookup"><span data-stu-id="ad742-128">The UI style of this site is based on the built-in project templates.</span></span> <span data-ttu-id="ad742-129">V tomto kurzu se naučíte, jak používat EF Core, nikoli způsob přizpůsobení uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ad742-129">The tutorial's focus is on how to use EF Core, not how to customize the UI.</span></span>

<span data-ttu-id="ad742-130">Pomocí odkazu v horní části stránky Získejte zdrojový kód dokončeného projektu.</span><span class="sxs-lookup"><span data-stu-id="ad742-130">Follow the link at the top of the page to get the source code for the completed project.</span></span> <span data-ttu-id="ad742-131">Složka *cu30* obsahuje kód pro verzi kurzu ASP.NET Core 3,0.</span><span class="sxs-lookup"><span data-stu-id="ad742-131">The *cu30* folder has the code for the ASP.NET Core 3.0 version of the tutorial.</span></span> <span data-ttu-id="ad742-132">Soubory, které reflektují stav kódu pro kurzy 1-7, najdete ve složce *cu30snapshots* .</span><span class="sxs-lookup"><span data-stu-id="ad742-132">Files that reflect the state of the code for tutorials 1-7 can be found in the *cu30snapshots* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad742-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad742-133">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ad742-134">Spuštění aplikace po stažení dokončeného projektu:</span><span class="sxs-lookup"><span data-stu-id="ad742-134">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="ad742-135">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="ad742-135">Build the project.</span></span>
* <span data-ttu-id="ad742-136">V konzole správce balíčků (PMC) spusťte následující příkaz:</span><span class="sxs-lookup"><span data-stu-id="ad742-136">In Package Manager Console (PMC) run the following command:</span></span>

  ```powershell
  Update-Database
  ```

* <span data-ttu-id="ad742-137">Spusťte projekt k osazení databáze.</span><span class="sxs-lookup"><span data-stu-id="ad742-137">Run the project to seed the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad742-138">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad742-138">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ad742-139">Spuštění aplikace po stažení dokončeného projektu:</span><span class="sxs-lookup"><span data-stu-id="ad742-139">To run the app after downloading the completed project:</span></span>

* <span data-ttu-id="ad742-140">Odstraňte *ContosoUniversity. csproj*a přejmenujte *ContosoUniversitySQLite. csproj* na *ContosoUniversity. csproj*.</span><span class="sxs-lookup"><span data-stu-id="ad742-140">Delete *ContosoUniversity.csproj*, and rename *ContosoUniversitySQLite.csproj* to *ContosoUniversity.csproj*.</span></span>
* <span data-ttu-id="ad742-141">Odstraňte *Startup.cs*a přejmenujte *StartupSQLite.cs* na *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="ad742-141">Delete *Startup.cs*, and rename *StartupSQLite.cs* to *Startup.cs*.</span></span>
* <span data-ttu-id="ad742-142">Odstraňte *appSettings. JSON*a přejmenujte *appSettingsSQLite.* JSON na *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="ad742-142">Delete *appSettings.json*, and rename *appSettingsSQLite.json* to *appSettings.json*.</span></span>
* <span data-ttu-id="ad742-143">Odstraňte složku *migrace* a přejmenujte *MigrationsSQL* na *migrace*.</span><span class="sxs-lookup"><span data-stu-id="ad742-143">Delete the *Migrations* folder, and rename *MigrationsSQL* to *Migrations*.</span></span>
* <span data-ttu-id="ad742-144">Proveďte globální hledání `#if SQLiteVersion` a odeberte `#if SQLiteVersion` příkaz a přidružený `#endif` příkaz.</span><span class="sxs-lookup"><span data-stu-id="ad742-144">Do a global search for `#if SQLiteVersion` and remove `#if SQLiteVersion` and the associated `#endif` statement.</span></span>
* <span data-ttu-id="ad742-145">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="ad742-145">Build the project.</span></span>
* <span data-ttu-id="ad742-146">Na příkazovém řádku ve složce projektu spusťte následující příkazy:</span><span class="sxs-lookup"><span data-stu-id="ad742-146">At a command prompt in the project folder, run the following commands:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  dotnet ef database update
  ```

* <span data-ttu-id="ad742-147">V nástroji SQLite spusťte následující příkaz SQL:</span><span class="sxs-lookup"><span data-stu-id="ad742-147">In your SQLite tool, run the following SQL statement:</span></span>

  ```sql
  UPDATE Department SET RowVersion = randomblob(8)
  ```

* <span data-ttu-id="ad742-148">Spusťte projekt k osazení databáze.</span><span class="sxs-lookup"><span data-stu-id="ad742-148">Run the project to seed the database.</span></span>

---

## <a name="create-the-web-app-project"></a><span data-ttu-id="ad742-149">Vytvoření projektu webové aplikace</span><span class="sxs-lookup"><span data-stu-id="ad742-149">Create the web app project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad742-150">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad742-150">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ad742-151">V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="ad742-151">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ad742-152">Vyberte **ASP.NET Core webové aplikace**.</span><span class="sxs-lookup"><span data-stu-id="ad742-152">Select **ASP.NET Core Web Application**.</span></span>
* <span data-ttu-id="ad742-153">Pojmenujte projekt *ContosoUniversity*.</span><span class="sxs-lookup"><span data-stu-id="ad742-153">Name the project *ContosoUniversity*.</span></span> <span data-ttu-id="ad742-154">Je důležité použít tento přesný název, včetně velkých a malých písmen, aby se obory názvů shodovaly při zkopírování a vložení kódu.</span><span class="sxs-lookup"><span data-stu-id="ad742-154">It's important to use this exact name including capitalization, so the namespaces match when code is copied and pasted.</span></span>
* <span data-ttu-id="ad742-155">V rozevíracích seznamech vyberte **.NET Core** a **ASP.NET Core 3,0** a potom vyberte **Webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="ad742-155">Select **.NET Core** and **ASP.NET Core 3.0** in the dropdowns, and then select **Web Application**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad742-156">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad742-156">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ad742-157">V terminálu přejděte do složky, ve které by měla být vytvořena složka projektu.</span><span class="sxs-lookup"><span data-stu-id="ad742-157">In a terminal, navigate to the folder in which the project folder should be created.</span></span>

* <span data-ttu-id="ad742-158">Spuštěním následujících příkazů vytvořte projekt Razor Pages a `cd` do nové složky projektu:</span><span class="sxs-lookup"><span data-stu-id="ad742-158">Run the following commands to create a Razor Pages project and `cd` into the new project folder:</span></span>

  ```dotnetcli
  dotnet new webapp -o ContosoUniversity
  cd ContosoUniversity
  ```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="ad742-159">Nastavení stylu webu</span><span class="sxs-lookup"><span data-stu-id="ad742-159">Set up the site style</span></span>

<span data-ttu-id="ad742-160">Pomocí aktualizace *stránek/Shared/_Layout. cshtml*nastavte záhlaví webu, zápatí a nabídku. cshtml:</span><span class="sxs-lookup"><span data-stu-id="ad742-160">Set up the site header, footer, and menu by updating *Pages/Shared/_Layout.cshtml*:</span></span>

* <span data-ttu-id="ad742-161">Změňte všechny výskyty "ContosoUniversity" na "contoso University".</span><span class="sxs-lookup"><span data-stu-id="ad742-161">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="ad742-162">Existují tři výskyty.</span><span class="sxs-lookup"><span data-stu-id="ad742-162">There are three occurrences.</span></span>

* <span data-ttu-id="ad742-163">Odstraňte položky nabídky **Domů** a **Ochrana osobních údajů** a přidejte záznamy **o o** **studentech**, **kurzech**, **instruktorech**a **odděleních**.</span><span class="sxs-lookup"><span data-stu-id="ad742-163">Delete the **Home** and **Privacy** menu entries, and add entries for **About**, **Students**, **Courses**, **Instructors**, and **Departments**.</span></span>

<span data-ttu-id="ad742-164">Změny jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="ad742-164">The changes are highlighted.</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Shared/_Layout.cshtml?highlight=6,14,21-35,49)]

<span data-ttu-id="ad742-165">Na *stránce pages/index. cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET Core textem této aplikace:</span><span class="sxs-lookup"><span data-stu-id="ad742-165">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET Core with text about this app:</span></span>

[!code-cshtml[Main](intro/samples/cu30/Pages/Index.cshtml)]

<span data-ttu-id="ad742-166">Spusťte aplikaci, abyste ověřili, že se zobrazí domovská stránka.</span><span class="sxs-lookup"><span data-stu-id="ad742-166">Run the app to verify that the home page appears.</span></span>

## <a name="the-data-model"></a><span data-ttu-id="ad742-167">Datový model</span><span class="sxs-lookup"><span data-stu-id="ad742-167">The data model</span></span>

<span data-ttu-id="ad742-168">V následujících částech se vytvoří datový model:</span><span class="sxs-lookup"><span data-stu-id="ad742-168">The following sections create a data model:</span></span>

![Kurz – registrace – diagram datového modelu studenta](intro/_static/data-model-diagram.png)

<span data-ttu-id="ad742-170">Student se může zaregistrovat v jakémkoli počtu kurzů a kurz může mít zaregistrovaný libovolný počet studentů.</span><span class="sxs-lookup"><span data-stu-id="ad742-170">A student can enroll in any number of courses, and a course can have any number of students enrolled in it.</span></span>

## <a name="the-student-entity"></a><span data-ttu-id="ad742-171">Entita studenta</span><span class="sxs-lookup"><span data-stu-id="ad742-171">The Student entity</span></span>

![Diagram entity studenta](intro/_static/student-entity.png)

* <span data-ttu-id="ad742-173">Vytvořte složku *modely* ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="ad742-173">Create a *Models* folder in the project folder.</span></span> 

* <span data-ttu-id="ad742-174">Vytvořte *modely/studenta. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="ad742-174">Create *Models/Student.cs* with the following code:</span></span>

  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Student.cs)]

<span data-ttu-id="ad742-175">`ID` Vlastnost se zobrazí jako sloupec primárního klíče tabulky databáze, která odpovídá této třídě.</span><span class="sxs-lookup"><span data-stu-id="ad742-175">The `ID` property becomes the primary key column of the database table that corresponds to this class.</span></span> <span data-ttu-id="ad742-176">Ve výchozím nastavení EF Core interpretuje vlastnost s názvem `ID` nebo `classnameID` jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="ad742-176">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="ad742-177">Proto je `StudentID`alternativní automaticky rozpoznaný název pro primární `Student` klíč třídy.</span><span class="sxs-lookup"><span data-stu-id="ad742-177">So the alternative automatically recognized name for the `Student` class primary key is `StudentID`.</span></span> <span data-ttu-id="ad742-178">Další informace najdete v tématu [EF Core-Keys](/ef/core/modeling/keys?tabs=data-annotations).</span><span class="sxs-lookup"><span data-stu-id="ad742-178">For more information, see [EF Core - Keys](/ef/core/modeling/keys?tabs=data-annotations).</span></span>

<span data-ttu-id="ad742-179">`Enrollments` Vlastnost je [navigační vlastnost](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="ad742-179">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="ad742-180">Navigační vlastnosti obsahují další entity, které se vztahují k této entitě.</span><span class="sxs-lookup"><span data-stu-id="ad742-180">Navigation properties hold other entities that are related to this entity.</span></span> <span data-ttu-id="ad742-181">V takovém případě `Enrollments` vlastnost `Student` entity obsahuje všechny `Enrollment` entity, které se vztahují k danému studentovi.</span><span class="sxs-lookup"><span data-stu-id="ad742-181">In this case, the `Enrollments` property of a `Student` entity holds all of the `Enrollment` entities that are related to that Student.</span></span> <span data-ttu-id="ad742-182">Například pokud má řádek studenta v databázi dva související řádky registrace, `Enrollments` navigační vlastnost obsahuje tyto dvě entity registrace.</span><span class="sxs-lookup"><span data-stu-id="ad742-182">For example, if a Student row in the database has two related Enrollment rows, the `Enrollments` navigation property contains those two Enrollment entities.</span></span> 

<span data-ttu-id="ad742-183">V databázi se řádek registrace vztahuje k řádku studenta, pokud jeho sloupec StudentID obsahuje hodnotu ID studenta.</span><span class="sxs-lookup"><span data-stu-id="ad742-183">In the database, an Enrollment row is related to a Student row if its StudentID column contains the student's ID value.</span></span> <span data-ttu-id="ad742-184">Předpokládejme například, že řádek studenta má ID = 1.</span><span class="sxs-lookup"><span data-stu-id="ad742-184">For example, suppose a Student row has ID=1.</span></span> <span data-ttu-id="ad742-185">Související řádky registrace budou mít StudentID = 1.</span><span class="sxs-lookup"><span data-stu-id="ad742-185">Related Enrollment rows will have StudentID = 1.</span></span> <span data-ttu-id="ad742-186">StudentID je *cizí klíč* v tabulce zápisu.</span><span class="sxs-lookup"><span data-stu-id="ad742-186">StudentID is a *foreign key* in the Enrollment table.</span></span> 

<span data-ttu-id="ad742-187">`Enrollments` Vlastnost je definována tak, `ICollection<Enrollment>` že může existovat více souvisejících entit zápisu.</span><span class="sxs-lookup"><span data-stu-id="ad742-187">The `Enrollments` property is defined as `ICollection<Enrollment>` because there may be multiple related Enrollment entities.</span></span> <span data-ttu-id="ad742-188">Můžete použít jiné typy kolekce, například `List<Enrollment>` nebo. `HashSet<Enrollment>`</span><span class="sxs-lookup"><span data-stu-id="ad742-188">You can use other collection types, such as `List<Enrollment>` or `HashSet<Enrollment>`.</span></span> <span data-ttu-id="ad742-189">Při `ICollection<Enrollment>` použití EF Core vytvoří ve výchozím nastavení `HashSet<Enrollment>` kolekci.</span><span class="sxs-lookup"><span data-stu-id="ad742-189">When `ICollection<Enrollment>` is used, EF Core creates a `HashSet<Enrollment>` collection by default.</span></span>

## <a name="the-enrollment-entity"></a><span data-ttu-id="ad742-190">Entita registrace</span><span class="sxs-lookup"><span data-stu-id="ad742-190">The Enrollment entity</span></span>

![Diagram entity registrace](intro/_static/enrollment-entity.png)

<span data-ttu-id="ad742-192">Vytvořte *modely/registrace. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ad742-192">Create *Models/Enrollment.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Enrollment.cs)]

<span data-ttu-id="ad742-193">`EnrollmentID` Vlastnost je primární klíč; Tato entita používá `classnameID` vzor namísto `ID` samotného.</span><span class="sxs-lookup"><span data-stu-id="ad742-193">The `EnrollmentID` property is the primary key; this entity uses the `classnameID` pattern instead of `ID` by itself.</span></span> <span data-ttu-id="ad742-194">Pro model produkčních dat vyberte jeden vzor a používejte ho konzistentně.</span><span class="sxs-lookup"><span data-stu-id="ad742-194">For a production data model, choose one pattern and use it consistently.</span></span> <span data-ttu-id="ad742-195">Tento kurz používá pouze k ilustraci toho, jak funguje.</span><span class="sxs-lookup"><span data-stu-id="ad742-195">This tutorial uses both just to illustrate that both work.</span></span> <span data-ttu-id="ad742-196">Použití `ID` bez `classname` toho usnadňuje implementaci některých druhů změn datového modelu.</span><span class="sxs-lookup"><span data-stu-id="ad742-196">Using `ID` without `classname` makes it easier to implement some kinds of data model changes.</span></span>

<span data-ttu-id="ad742-197">`Grade` Vlastnost je `enum`.</span><span class="sxs-lookup"><span data-stu-id="ad742-197">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="ad742-198">Otazník po deklaraci `Grade` typu označuje, že vlastnost může `Grade` [mít hodnotu null](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span><span class="sxs-lookup"><span data-stu-id="ad742-198">The question mark after the `Grade` type declaration indicates that the `Grade` property is [nullable](https://docs.microsoft.com/dotnet/csharp/programming-guide/nullable-types/).</span></span> <span data-ttu-id="ad742-199">Třídu, která má hodnotu null, se liší od nulové&mdash;třídy null znamená, že známka není známa nebo ještě nebyla přiřazena.</span><span class="sxs-lookup"><span data-stu-id="ad742-199">A grade that's null is different from a zero grade&mdash;null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="ad742-200">`StudentID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Student`.</span><span class="sxs-lookup"><span data-stu-id="ad742-200">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="ad742-201">`Enrollment` Entita je přidružená k jedné `Student` entitě, takže vlastnost obsahuje jednu `Student` entitu.</span><span class="sxs-lookup"><span data-stu-id="ad742-201">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span>

<span data-ttu-id="ad742-202">`CourseID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Course`.</span><span class="sxs-lookup"><span data-stu-id="ad742-202">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="ad742-203">`Enrollment` Entita je přidružená k jedné `Course` entitě.</span><span class="sxs-lookup"><span data-stu-id="ad742-203">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="ad742-204">EF Core interpretuje vlastnost jako cizí klíč, pokud má název `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="ad742-204">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="ad742-205">Například`StudentID` je cizí klíč pro `Student` navigační vlastnost, protože primární klíč `Student` entity je. `ID`</span><span class="sxs-lookup"><span data-stu-id="ad742-205">For example,`StudentID` is the foreign key for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="ad742-206">Lze také pojmenovat `<primary key property name>`vlastnosti cizího klíče.</span><span class="sxs-lookup"><span data-stu-id="ad742-206">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="ad742-207">Například vzhledem k `CourseID` tomu, `Course` že primární klíč entity je `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="ad742-207">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

## <a name="the-course-entity"></a><span data-ttu-id="ad742-208">Entita kurzu</span><span class="sxs-lookup"><span data-stu-id="ad742-208">The Course entity</span></span>

![Diagram entity kurzu](intro/_static/course-entity.png)

<span data-ttu-id="ad742-210">Vytvořte *modely/Course. cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ad742-210">Create *Models/Course.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Models/Course.cs)]

<span data-ttu-id="ad742-211">`Enrollments` Vlastnost je navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="ad742-211">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="ad742-212">`Course` Entita může souviset s libovolným počtem `Enrollment` entit.</span><span class="sxs-lookup"><span data-stu-id="ad742-212">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="ad742-213">`DatabaseGenerated` Atribut umožňuje aplikaci určit primární klíč místo toho, aby ho databáze vygenerovala.</span><span class="sxs-lookup"><span data-stu-id="ad742-213">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the database generate it.</span></span>

<span data-ttu-id="ad742-214">Sestavte projekt, aby se ověřilo, že nejsou k dispozici žádné chyby kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="ad742-214">Build the project to validate that there are no compiler errors.</span></span>

## <a name="scaffold-student-pages"></a><span data-ttu-id="ad742-215">Stránky studenta pro generování uživatelského rozhraní</span><span class="sxs-lookup"><span data-stu-id="ad742-215">Scaffold Student pages</span></span>

<span data-ttu-id="ad742-216">V této části použijete nástroj ASP.NET Core pro generování uživatelského rozhraní k vygenerování:</span><span class="sxs-lookup"><span data-stu-id="ad742-216">In this section, you use the ASP.NET Core scaffolding tool to generate:</span></span>

* <span data-ttu-id="ad742-217">Třída *kontextu* EF Core.</span><span class="sxs-lookup"><span data-stu-id="ad742-217">An EF Core *context* class.</span></span> <span data-ttu-id="ad742-218">Kontext je hlavní třída, která koordinuje funkce Entity Framework pro daný datový model.</span><span class="sxs-lookup"><span data-stu-id="ad742-218">The context is the main class that coordinates Entity Framework functionality for a given data model.</span></span> <span data-ttu-id="ad742-219">Je odvozen z `Microsoft.EntityFrameworkCore.DbContext` třídy.</span><span class="sxs-lookup"><span data-stu-id="ad742-219">It derives from the `Microsoft.EntityFrameworkCore.DbContext` class.</span></span>
* <span data-ttu-id="ad742-220">Stránky Razor, které zpracovávají operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro `Student` entitu.</span><span class="sxs-lookup"><span data-stu-id="ad742-220">Razor pages that handle Create, Read, Update, and Delete (CRUD) operations for the `Student` entity.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad742-221">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad742-221">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ad742-222">Vytvořte složku *Students* ve složce *stránky* .</span><span class="sxs-lookup"><span data-stu-id="ad742-222">Create a *Students* folder in the *Pages* folder.</span></span>
* <span data-ttu-id="ad742-223">V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Pages/Students* a vyberte **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="ad742-223">In **Solution Explorer**, right-click the *Pages/Students* folder and select **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="ad742-224">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte možnost **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="ad742-224">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>
* <span data-ttu-id="ad742-225">V dialogovém okně **přidat Razor Pages pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="ad742-225">In the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
  * <span data-ttu-id="ad742-226">V rozevíracím seznamu **třída modelu** vyberte **student (ContosoUniversity. Models)**.</span><span class="sxs-lookup"><span data-stu-id="ad742-226">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
  * <span data-ttu-id="ad742-227">V řádku **třídy kontextu dat** vyberte znaménko **+** (plus).</span><span class="sxs-lookup"><span data-stu-id="ad742-227">In the **Data context class** row, select the **+** (plus) sign.</span></span>
  * <span data-ttu-id="ad742-228">Změňte název kontextu dat z *ContosoUniversity. Models. ContosoUniversityContext* na *ContosoUniversity. data. SchoolContext*.</span><span class="sxs-lookup"><span data-stu-id="ad742-228">Change the data context name from *ContosoUniversity.Models.ContosoUniversityContext* to *ContosoUniversity.Data.SchoolContext*.</span></span>
  * <span data-ttu-id="ad742-229">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="ad742-229">Select **Add**.</span></span>

<span data-ttu-id="ad742-230">Automaticky se nainstalují tyto balíčky:</span><span class="sxs-lookup"><span data-stu-id="ad742-230">The following packages are automatically installed:</span></span>

* `Microsoft.VisualStudio.Web.CodeGeneration.Design`
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Microsoft.Extensions.Logging.Debug`
* `Microsoft.EntityFrameworkCore.Tools`

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad742-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad742-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ad742-232">Spuštěním následujících příkazů .NET Core CLI nainstalujte požadované balíčky NuGet:</span><span class="sxs-lookup"><span data-stu-id="ad742-232">Run the following .NET Core CLI commands to install required NuGet packages:</span></span>
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

  <span data-ttu-id="ad742-233">Pro generování uživatelského rozhraní je vyžadován balíček Microsoft. VisualStudio. Web. strategii. Design.</span><span class="sxs-lookup"><span data-stu-id="ad742-233">The Microsoft.VisualStudio.Web.CodeGeneration.Design package is required for scaffolding.</span></span> <span data-ttu-id="ad742-234">I když aplikace nebude používat SQL Server, nástroj pro generování uživatelského rozhraní potřebuje balíček SQL Server.</span><span class="sxs-lookup"><span data-stu-id="ad742-234">Although the app won't use SQL Server, the scaffolding tool needs the SQL Server package.</span></span>

* <span data-ttu-id="ad742-235">Vytvořte složku *stránky/studenty* .</span><span class="sxs-lookup"><span data-stu-id="ad742-235">Create a *Pages/Students* folder.</span></span>

* <span data-ttu-id="ad742-236">Spusťte následující příkaz pro instalaci nástroje pro [generování uživatelského rozhraní ASPNET-CodeGenerator](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span><span class="sxs-lookup"><span data-stu-id="ad742-236">Run the following command to install the [aspnet-codegenerator scaffolding tool](xref:fundamentals/tools/dotnet-aspnet-codegenerator).</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

* <span data-ttu-id="ad742-237">Spusťte následující příkaz pro generování uživatelského rozhraní stránek studenta.</span><span class="sxs-lookup"><span data-stu-id="ad742-237">Run the following command to scaffold Student pages.</span></span>

  <span data-ttu-id="ad742-238">**V systému Windows**</span><span class="sxs-lookup"><span data-stu-id="ad742-238">**On Windows**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages\Students --referenceScriptLibraries
  ```

  <span data-ttu-id="ad742-239">**V systému macOS nebo Linux**</span><span class="sxs-lookup"><span data-stu-id="ad742-239">**On macOS or Linux**</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Data.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
  ```

---

<span data-ttu-id="ad742-240">Pokud máte problém s předchozím krokem, sestavte projekt a opakujte krok generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ad742-240">If you have a problem with the preceding step, build the project and retry the scaffold step.</span></span>

<span data-ttu-id="ad742-241">Proces generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="ad742-241">The scaffolding process:</span></span>

* <span data-ttu-id="ad742-242">Vytvoří stránky Razor ve složce *Pages/Students* :</span><span class="sxs-lookup"><span data-stu-id="ad742-242">Creates Razor pages in the *Pages/Students* folder:</span></span>
  * <span data-ttu-id="ad742-243">*Vytvoření. cshtml* a *Create.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ad742-243">*Create.cshtml* and *Create.cshtml.cs*</span></span>
  * <span data-ttu-id="ad742-244">*Odstranění. cshtml* a *DELETE.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ad742-244">*Delete.cshtml* and *Delete.cshtml.cs*</span></span>
  * <span data-ttu-id="ad742-245">*Podrobnosti. cshtml* a *Details.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ad742-245">*Details.cshtml* and *Details.cshtml.cs*</span></span>
  * <span data-ttu-id="ad742-246">*Upravit. cshtml* a *Edit.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ad742-246">*Edit.cshtml* and *Edit.cshtml.cs*</span></span>
  * <span data-ttu-id="ad742-247">*Index. cshtml* a *index.cshtml.cs*</span><span class="sxs-lookup"><span data-stu-id="ad742-247">*Index.cshtml* and *Index.cshtml.cs*</span></span>
* <span data-ttu-id="ad742-248">Vytvoří *data/SchoolContext. cs*.</span><span class="sxs-lookup"><span data-stu-id="ad742-248">Creates *Data/SchoolContext.cs*.</span></span>
* <span data-ttu-id="ad742-249">Přidá kontext do injektáže závislosti v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="ad742-249">Adds the context to dependency injection in *Startup.cs*.</span></span>
* <span data-ttu-id="ad742-250">Přidá připojovací řetězec databáze do souboru *appSettings. JSON*.</span><span class="sxs-lookup"><span data-stu-id="ad742-250">Adds a database connection string to *appsettings.json*.</span></span>

## <a name="database-connection-string"></a><span data-ttu-id="ad742-251">Připojovací řetězec databáze</span><span class="sxs-lookup"><span data-stu-id="ad742-251">Database connection string</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad742-252">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad742-252">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ad742-253">Připojovací řetězec Určuje [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="ad742-253">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> 

[!code-json[Main](intro/samples/cu30/appsettings.json?highlight=11)]

<span data-ttu-id="ad742-254">LocalDB je zjednodušená verze databázového stroje SQL Server Express a je určena pro vývoj aplikací, nikoli za použití v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="ad742-254">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="ad742-255">Ve výchozím nastavení LocalDB vytvoří soubory *. mdf* v `C:/Users/<user>` adresáři.</span><span class="sxs-lookup"><span data-stu-id="ad742-255">By default, LocalDB creates *.mdf* files in the `C:/Users/<user>` directory.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad742-256">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad742-256">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ad742-257">Změňte připojovací řetězec tak, aby odkazoval na soubor databáze SQLite s názvem *cu. DB*:</span><span class="sxs-lookup"><span data-stu-id="ad742-257">Change the connection string to point to a SQLite database file named *CU.db*:</span></span>

[!code-json[Main](intro/samples/cu30/appsettingsSQLite.json?highlight=11)]

---

## <a name="update-the-database-context-class"></a><span data-ttu-id="ad742-258">Aktualizuje třídu kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="ad742-258">Update the database context class</span></span>

<span data-ttu-id="ad742-259">Hlavní třída, která koordinuje funkce EF Core pro daný datový model, je třída kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="ad742-259">The main class that coordinates EF Core functionality for a given data model is the database context class.</span></span> <span data-ttu-id="ad742-260">Kontext je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="ad742-260">The context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="ad742-261">Kontext určuje, které entity jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="ad742-261">The context specifies which entities are included in the data model.</span></span> <span data-ttu-id="ad742-262">V tomto projektu je třída pojmenována `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="ad742-262">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="ad742-263">*SchoolContext.cs* aktualizujte pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="ad742-263">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/SchoolContext.cs?highlight=13-22)]

<span data-ttu-id="ad742-264">Zvýrazněný kód vytvoří vlastnost [negenerickými\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro každou sadu entit.</span><span class="sxs-lookup"><span data-stu-id="ad742-264">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="ad742-265">V terminologii EF Core:</span><span class="sxs-lookup"><span data-stu-id="ad742-265">In EF Core terminology:</span></span>

* <span data-ttu-id="ad742-266">Sada entit obvykle odpovídá databázové tabulce.</span><span class="sxs-lookup"><span data-stu-id="ad742-266">An entity set typically corresponds to a database table.</span></span>
* <span data-ttu-id="ad742-267">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="ad742-267">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="ad742-268">Vzhledem k tomu, že sada entit obsahuje více entit, musí mít vlastnosti Negenerickými plurální názvy.</span><span class="sxs-lookup"><span data-stu-id="ad742-268">Since an entity set contains multiple entities, the DBSet properties should be plural names.</span></span> <span data-ttu-id="ad742-269">Vzhledem k tomu, že nástroj pro`Student` generování uživatelského rozhraní vytvořil negenerickými, tento krok `Students`změny změní na plural.</span><span class="sxs-lookup"><span data-stu-id="ad742-269">Since the scaffolding tool created a`Student` DBSet, this step changes it to plural `Students`.</span></span> 

<span data-ttu-id="ad742-270">Chcete-li, aby kód Razor Pages odpovídal novému názvu Negenerickými, proveďte globální změnu v celém projektu `_context.Student` na. `_context.Students`</span><span class="sxs-lookup"><span data-stu-id="ad742-270">To make the Razor Pages code match the new DBSet name, make a global change across the whole project of `_context.Student` to `_context.Students`.</span></span>  <span data-ttu-id="ad742-271">K dispozici je 8 výskytů.</span><span class="sxs-lookup"><span data-stu-id="ad742-271">There are 8 occurrences.</span></span>

<span data-ttu-id="ad742-272">Sestavte projekt, aby se ověřilo, že nedošlo k chybám kompilátoru.</span><span class="sxs-lookup"><span data-stu-id="ad742-272">Build the project to verify there are no compiler errors.</span></span>

## <a name="startupcs"></a><span data-ttu-id="ad742-273">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="ad742-273">Startup.cs</span></span>

<span data-ttu-id="ad742-274">ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ad742-274">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ad742-275">Služby (například kontext databáze EF Core) jsou registrovány pomocí injektáže závislosti při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad742-275">Services (such as the EF Core database context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="ad742-276">Komponenty, které vyžadují tyto služby (například Razor Pages), poskytují tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="ad742-276">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="ad742-277">Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="ad742-277">The constructor code that gets a database context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="ad742-278">Nástroj pro generování uživatelského rozhraní automaticky zaregistroval třídu kontextu pomocí kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="ad742-278">The scaffolding tool automatically registered the context class with the dependency injection container.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad742-279">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad742-279">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ad742-280">V `ConfigureServices`byly zvýrazněné řádky přidány pomocí generátoru:</span><span class="sxs-lookup"><span data-stu-id="ad742-280">In `ConfigureServices`, the highlighted lines were added by the scaffolder:</span></span>

  [!code-csharp[Main](intro/samples/cu30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad742-281">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad742-281">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ad742-282">V `ConfigureServices`nástroji se ujistěte, že kód přidaný voláním `UseSqlite`uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ad742-282">In `ConfigureServices`, make sure the code added by the scaffolder calls `UseSqlite`.</span></span>

  [!code-csharp[Main](intro/samples/cu30/StartupSQLite.cs?name=snippet_ConfigureServices&highlight=5-6)]

---

<span data-ttu-id="ad742-283">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="ad742-283">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="ad742-284">Pro místní vývoj načítá [konfigurační systém ASP.NET Core](xref:fundamentals/configuration/index) připojovací řetězec ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="ad742-284">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="create-the-database"></a><span data-ttu-id="ad742-285">Vytvoření databáze</span><span class="sxs-lookup"><span data-stu-id="ad742-285">Create the database</span></span>

<span data-ttu-id="ad742-286">Aktualizujte *program.cs* , aby se vytvořila databáze, pokud neexistuje:</span><span class="sxs-lookup"><span data-stu-id="ad742-286">Update *Program.cs* to create the database if it doesn't exist:</span></span>

[!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Program.cs?highlight=1-2,14-18,21-38)]

<span data-ttu-id="ad742-287">Metoda [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) neprovede žádnou akci, pokud existuje databáze pro kontext.</span><span class="sxs-lookup"><span data-stu-id="ad742-287">The [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated) method takes no action if a database for the context exists.</span></span> <span data-ttu-id="ad742-288">Pokud žádná databáze neexistuje, vytvoří databázi a schéma.</span><span class="sxs-lookup"><span data-stu-id="ad742-288">If no database exists, it creates the database and schema.</span></span> <span data-ttu-id="ad742-289">`EnsureCreated`povolí následující pracovní postup pro zpracování změn datového modelu:</span><span class="sxs-lookup"><span data-stu-id="ad742-289">`EnsureCreated` enables the following workflow for handling data model changes:</span></span>

* <span data-ttu-id="ad742-290">Odstraňte databázi.</span><span class="sxs-lookup"><span data-stu-id="ad742-290">Delete the database.</span></span> <span data-ttu-id="ad742-291">Všechna existující data budou ztracena.</span><span class="sxs-lookup"><span data-stu-id="ad742-291">Any existing data is lost.</span></span>
* <span data-ttu-id="ad742-292">Změňte datový model.</span><span class="sxs-lookup"><span data-stu-id="ad742-292">Change the data model.</span></span> <span data-ttu-id="ad742-293">Například přidejte `EmailAddress` pole.</span><span class="sxs-lookup"><span data-stu-id="ad742-293">For example, add an `EmailAddress` field.</span></span>
* <span data-ttu-id="ad742-294">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ad742-294">Run the app.</span></span>
* <span data-ttu-id="ad742-295">`EnsureCreated`vytvoří databázi s novým schématem.</span><span class="sxs-lookup"><span data-stu-id="ad742-295">`EnsureCreated` creates a database with the new schema.</span></span>

<span data-ttu-id="ad742-296">Tento pracovní postup funguje dobře v rané fázi vývoje, když se schéma rychle vyvíjí, pokud nepotřebujete zachovat data.</span><span class="sxs-lookup"><span data-stu-id="ad742-296">This workflow works well early in development when the schema is rapidly evolving, as long as you don't need to preserve data.</span></span> <span data-ttu-id="ad742-297">Tato situace se liší v případě, že data, která byla zadána do databáze, musí být zachována.</span><span class="sxs-lookup"><span data-stu-id="ad742-297">The situation is different when data that has been entered into the database needs to be preserved.</span></span> <span data-ttu-id="ad742-298">V takovém případě použijte migrace.</span><span class="sxs-lookup"><span data-stu-id="ad742-298">When that is the case, use migrations.</span></span>

<span data-ttu-id="ad742-299">Později v rámci série kurzů odstraníte databázi, kterou vytvořil, `EnsureCreated` a místo toho použijete migrace.</span><span class="sxs-lookup"><span data-stu-id="ad742-299">Later in the tutorial series, you delete the database that was created by `EnsureCreated` and use migrations instead.</span></span> <span data-ttu-id="ad742-300">Databázi vytvořenou nástrojem `EnsureCreated` nelze aktualizovat pomocí migrací.</span><span class="sxs-lookup"><span data-stu-id="ad742-300">A database that is created by `EnsureCreated` can't be updated by using migrations.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="ad742-301">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="ad742-301">Test the app</span></span>

* <span data-ttu-id="ad742-302">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ad742-302">Run the app.</span></span>
* <span data-ttu-id="ad742-303">Vyberte odkaz **Students** a pak **vytvořte nový**.</span><span class="sxs-lookup"><span data-stu-id="ad742-303">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="ad742-304">Otestujte odkazy Upravit, Podrobnosti a Odstranit.</span><span class="sxs-lookup"><span data-stu-id="ad742-304">Test the Edit, Details, and Delete links.</span></span>

## <a name="seed-the-database"></a><span data-ttu-id="ad742-305">Dosazení databáze</span><span class="sxs-lookup"><span data-stu-id="ad742-305">Seed the database</span></span>

<span data-ttu-id="ad742-306">`EnsureCreated` Metoda vytvoří prázdnou databázi.</span><span class="sxs-lookup"><span data-stu-id="ad742-306">The `EnsureCreated` method creates an empty database.</span></span> <span data-ttu-id="ad742-307">V této části se přidá kód, který naplní databázi testovacími daty.</span><span class="sxs-lookup"><span data-stu-id="ad742-307">This section adds code that populates the database with test data.</span></span>

<span data-ttu-id="ad742-308">Vytvořte *data/DbInitializer. cs* pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="ad742-308">Create *Data/DbInitializer.cs* with the following code:</span></span>
<!-- next update, keep this file in the project and surround with #if -->
  [!code-csharp[Main](intro/samples/cu30snapshots/1-intro/Data/DbInitializer.cs)]

  <span data-ttu-id="ad742-309">Kód kontroluje, zda v databázi existují studenti.</span><span class="sxs-lookup"><span data-stu-id="ad742-309">The code checks if there are any students in the database.</span></span> <span data-ttu-id="ad742-310">Pokud neexistují studenti, přidá testovací data do databáze.</span><span class="sxs-lookup"><span data-stu-id="ad742-310">If there are no students, it adds test data to the database.</span></span> <span data-ttu-id="ad742-311">Vytvoří testovací data v polích, nikoli `List<T>` kolekce pro optimalizaci výkonu.</span><span class="sxs-lookup"><span data-stu-id="ad742-311">It creates the test data in arrays rather than `List<T>` collections to optimize performance.</span></span>

* <span data-ttu-id="ad742-312">V *program.cs*nahraďte `EnsureCreated` volání `DbInitializer.Initialize` voláním:</span><span class="sxs-lookup"><span data-stu-id="ad742-312">In *Program.cs*, replace the `EnsureCreated` call with a `DbInitializer.Initialize` call:</span></span>

  ```csharp
  // context.Database.EnsureCreated();
  DbInitializer.Initialize(context);
  ```

# <a name="visual-studio"></a>[<span data-ttu-id="ad742-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad742-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ad742-314">Zastavte aplikaci, pokud je spuštěná, a spusťte následující příkaz v **konzole správce balíčků** (PMC):</span><span class="sxs-lookup"><span data-stu-id="ad742-314">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad742-315">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad742-315">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ad742-316">Zastavte aplikaci, pokud je spuštěná, a odstraňte soubor *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="ad742-316">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

* <span data-ttu-id="ad742-317">Restartujte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ad742-317">Restart the app.</span></span>

* <span data-ttu-id="ad742-318">Vyberte stránku Students a zobrazte si dosazený údaj.</span><span class="sxs-lookup"><span data-stu-id="ad742-318">Select the Students page to see the seeded data.</span></span>

## <a name="view-the-database"></a><span data-ttu-id="ad742-319">Zobrazení databáze</span><span class="sxs-lookup"><span data-stu-id="ad742-319">View the database</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad742-320">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad742-320">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ad742-321">Otevřete **Průzkumník objektů systému SQL Server** (SSOX) z nabídky **Zobrazit** v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ad742-321">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
* <span data-ttu-id="ad742-322">V SSOX vyberte **(LocalDB) \MSSQLLocalDB > databáze > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="ad742-322">In SSOX, select **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span> <span data-ttu-id="ad742-323">Název databáze je vygenerován z dříve zadaného názvu kontextu plus pomlčka a identifikátor GUID.</span><span class="sxs-lookup"><span data-stu-id="ad742-323">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span>
* <span data-ttu-id="ad742-324">Rozbalte uzel **tabulky** .</span><span class="sxs-lookup"><span data-stu-id="ad742-324">Expand the **Tables** node.</span></span>
* <span data-ttu-id="ad742-325">Klikněte pravým tlačítkem myši na tabulku **student** a kliknutím na **Zobrazit data** Zobrazte vytvořené sloupce a řádky vložené do tabulky.</span><span class="sxs-lookup"><span data-stu-id="ad742-325">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>
* <span data-ttu-id="ad742-326">Kliknutím pravým tlačítkem na tabulku **student** a kliknutím na **Zobrazit kód** zjistíte `Student` , jak se model `Student` mapuje na schéma tabulky.</span><span class="sxs-lookup"><span data-stu-id="ad742-326">Right-click the **Student** table and click **View Code** to see how the `Student` model maps to the `Student` table schema.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad742-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad742-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ad742-328">Použijte nástroj SQLite k zobrazení schématu databáze a dat osazených daty.</span><span class="sxs-lookup"><span data-stu-id="ad742-328">Use your SQLite tool to view the database schema and seeded data.</span></span> <span data-ttu-id="ad742-329">Databázový soubor má název *cu. DB* a je umístěn ve složce projektu.</span><span class="sxs-lookup"><span data-stu-id="ad742-329">The database file is named *CU.db* and is located in the project folder.</span></span>

---

## <a name="asynchronous-code"></a><span data-ttu-id="ad742-330">Asynchronní kód</span><span class="sxs-lookup"><span data-stu-id="ad742-330">Asynchronous code</span></span>

<span data-ttu-id="ad742-331">Asynchronní programování je výchozí režim pro ASP.NET Core a EF Core.</span><span class="sxs-lookup"><span data-stu-id="ad742-331">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="ad742-332">Na webovém serveru je k dispozici omezený počet vláken a v situacích vysokého zatížení se mohou používat všechna dostupná vlákna.</span><span class="sxs-lookup"><span data-stu-id="ad742-332">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="ad742-333">Pokud k tomu dojde, server nemůže zpracovat nové požadavky, dokud nebudou vlákna uvolněna.</span><span class="sxs-lookup"><span data-stu-id="ad742-333">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="ad742-334">Pomocí synchronního kódu může být mnoho vláken svázáno s tím, že ve skutečnosti neprovádí žádnou práci, protože čeká na dokončení vstupně-výstupních operací.</span><span class="sxs-lookup"><span data-stu-id="ad742-334">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="ad742-335">V případě asynchronního kódu, když proces čeká na dokončení vstupně-výstupních operací, je jeho vlákno uvolněno na server, který bude použit pro zpracování jiných požadavků.</span><span class="sxs-lookup"><span data-stu-id="ad742-335">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="ad742-336">Výsledkem je, že asynchronní kód umožňuje efektivnější použití prostředků serveru a server může zpracovávat více provozu bez prodlev.</span><span class="sxs-lookup"><span data-stu-id="ad742-336">As a result, asynchronous code enables server resources to be used more efficiently, and the server can handle more traffic without delays.</span></span>

<span data-ttu-id="ad742-337">Asynchronní kód zavádí v době běhu malé množství režie.</span><span class="sxs-lookup"><span data-stu-id="ad742-337">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="ad742-338">V situacích s nízkým provozem je dosaženo zanedbatelného výkonu, zatímco v situacích vysokého provozu je potenciální zlepšení výkonu značné.</span><span class="sxs-lookup"><span data-stu-id="ad742-338">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="ad742-339">V následujícím kódu, klíčové slovo [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` návratová hodnota, `await` klíčové slovo a `ToListAsync` Metoda provede asynchronní spouštění kódu.</span><span class="sxs-lookup"><span data-stu-id="ad742-339">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

```csharp
public async Task OnGetAsync()
{
    Students = await _context.Students.ToListAsync();
}
```

* <span data-ttu-id="ad742-340">`async` Klíčové slovo dává kompilátoru následující informace:</span><span class="sxs-lookup"><span data-stu-id="ad742-340">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="ad742-341">Vygenerujte zpětná volání pro části těla metody.</span><span class="sxs-lookup"><span data-stu-id="ad742-341">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="ad742-342">Vytvořte vrácený objekt [úkolu](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) .</span><span class="sxs-lookup"><span data-stu-id="ad742-342">Create the [Task](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType) object that's returned.</span></span>
* <span data-ttu-id="ad742-343">`Task<T>` Návratový typ představuje probíhající práci.</span><span class="sxs-lookup"><span data-stu-id="ad742-343">The `Task<T>` return type represents ongoing work.</span></span>
* <span data-ttu-id="ad742-344">`await` Klíčové slovo způsobí, že kompilátor rozdělí metodu do dvou částí.</span><span class="sxs-lookup"><span data-stu-id="ad742-344">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="ad742-345">První část končí asynchronně spuštěnou operací.</span><span class="sxs-lookup"><span data-stu-id="ad742-345">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="ad742-346">Druhá část je vložena do metody zpětného volání, která je volána po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="ad742-346">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="ad742-347">`ToListAsync`je asynchronní verze metody `ToList` rozšíření.</span><span class="sxs-lookup"><span data-stu-id="ad742-347">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="ad742-348">Některé věci, které je potřeba znát při psaní asynchronního kódu, který používá EF Core:</span><span class="sxs-lookup"><span data-stu-id="ad742-348">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="ad742-349">Asynchronně jsou spouštěny pouze příkazy, které způsobují odeslání dotazů nebo příkazů do databáze.</span><span class="sxs-lookup"><span data-stu-id="ad742-349">Only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="ad742-350">To zahrnuje `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`a `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="ad742-350">That includes `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="ad742-351">Neobsahuje příkazy, které mění pouze `IQueryable`, například. `var students = context.Students.Where(s => s.LastName == "Davolio")`</span><span class="sxs-lookup"><span data-stu-id="ad742-351">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="ad742-352">Kontext EF Core není bezpečný pro přístup z více vláken: Nepokoušejte se současně provést více operací.</span><span class="sxs-lookup"><span data-stu-id="ad742-352">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="ad742-353">Chcete-li využít výhody z hlediska výkonu asynchronního kódu, ověřte, že balíčky knihoven (například pro stránkování) používají async, pokud volají EF Core metody, které odesílají dotazy do databáze.</span><span class="sxs-lookup"><span data-stu-id="ad742-353">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the database.</span></span>

<span data-ttu-id="ad742-354">Další informace o asynchronním programování v rozhraní .NET naleznete v tématu [Async Overview](/dotnet/standard/async) a [Asynchronous Programming with Async a await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="ad742-354">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

## <a name="next-steps"></a><span data-ttu-id="ad742-355">Další kroky</span><span class="sxs-lookup"><span data-stu-id="ad742-355">Next steps</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="ad742-356">Další kurz</span><span class="sxs-lookup"><span data-stu-id="ad742-356">Next tutorial</span></span>](xref:data/ef-rp/crud)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ad742-357">Ukázková webová aplikace společnosti Contoso University ukazuje, jak vytvořit aplikaci ASP.NET Core Razor Pages pomocí jádra Entity Framework (EF).</span><span class="sxs-lookup"><span data-stu-id="ad742-357">The Contoso University sample web app demonstrates how to create an ASP.NET Core Razor Pages app using Entity Framework (EF) Core.</span></span>

<span data-ttu-id="ad742-358">Ukázková aplikace je web pro fiktivní univerzitě společnosti Contoso.</span><span class="sxs-lookup"><span data-stu-id="ad742-358">The sample app is a web site for a fictional Contoso University.</span></span> <span data-ttu-id="ad742-359">Zahrnuje funkce, jako je například využití studenta, vytváření kurzu a přiřazení instruktora.</span><span class="sxs-lookup"><span data-stu-id="ad742-359">It includes functionality such as student admission, course creation, and instructor assignments.</span></span> <span data-ttu-id="ad742-360">Tato stránka je první v sérii kurzů, které vysvětlují, jak vytvořit ukázkovou aplikaci Contoso University.</span><span class="sxs-lookup"><span data-stu-id="ad742-360">This page is the first in a series of tutorials that explain how to build the Contoso University sample app.</span></span>

[<span data-ttu-id="ad742-361">Stažení nebo zobrazení dokončené aplikace</span><span class="sxs-lookup"><span data-stu-id="ad742-361">Download or view the completed app.</span></span>](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) <span data-ttu-id="ad742-362">[Pokyny ke stažení](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="ad742-362">[Download instructions](xref:index#how-to-download-a-sample).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="ad742-363">Požadavky</span><span class="sxs-lookup"><span data-stu-id="ad742-363">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad742-364">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad742-364">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad742-365">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad742-365">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

<span data-ttu-id="ad742-366">Znalost [Razor Pages](xref:razor-pages/index).</span><span class="sxs-lookup"><span data-stu-id="ad742-366">Familiarity with [Razor Pages](xref:razor-pages/index).</span></span> <span data-ttu-id="ad742-367">Noví Programátori by měli před spuštěním této série dokončit [Začínáme s Razor Pages](xref:tutorials/razor-pages/razor-pages-start) .</span><span class="sxs-lookup"><span data-stu-id="ad742-367">New programmers should complete [Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start) before starting this series.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="ad742-368">Řešení potíží</span><span class="sxs-lookup"><span data-stu-id="ad742-368">Troubleshooting</span></span>

<span data-ttu-id="ad742-369">Pokud narazíte na problém, který nelze vyřešit, můžete řešení obecně najít porovnáním kódu s [dokončeným projektem](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span><span class="sxs-lookup"><span data-stu-id="ad742-369">If you run into a problem you can't resolve, you can generally find the solution by comparing your code to the [completed project](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span> <span data-ttu-id="ad742-370">Dobrým způsobem, jak získat pomoc, je odeslání otázky do [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) pro [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) nebo [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span><span class="sxs-lookup"><span data-stu-id="ad742-370">A good way to get help is by posting a question to [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core) for [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) or [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core).</span></span>

## <a name="the-contoso-university-web-app"></a><span data-ttu-id="ad742-371">Webová aplikace společnosti Contoso University</span><span class="sxs-lookup"><span data-stu-id="ad742-371">The Contoso University web app</span></span>

<span data-ttu-id="ad742-372">Aplikace sestavená v těchto kurzech je základním webem na univerzitě.</span><span class="sxs-lookup"><span data-stu-id="ad742-372">The app built in these tutorials is a basic university web site.</span></span>

<span data-ttu-id="ad742-373">Uživatelé můžou zobrazit a aktualizovat informace o studentech, kurzech a instruktorech.</span><span class="sxs-lookup"><span data-stu-id="ad742-373">Users can view and update student, course, and instructor information.</span></span> <span data-ttu-id="ad742-374">Tady je několik obrazovek vytvořených v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="ad742-374">Here are a few of the screens created in the tutorial.</span></span>

![Stránka indexu studentů](intro/_static/students-index.png)

![Stránka pro úpravy studentů](intro/_static/student-edit.png)

<span data-ttu-id="ad742-377">Styl uživatelského rozhraní tohoto webu je blízko toho, co vygenerovaly předdefinované šablony.</span><span class="sxs-lookup"><span data-stu-id="ad742-377">The UI style of this site is close to what's generated by the built-in templates.</span></span> <span data-ttu-id="ad742-378">Tento kurz se zaměřuje na EF Core s Razor Pages, ne s uživatelským rozhraním.</span><span class="sxs-lookup"><span data-stu-id="ad742-378">The tutorial focus is on EF Core with Razor Pages, not the UI.</span></span>

## <a name="create-the-contosouniversity-razor-pages-web-app"></a><span data-ttu-id="ad742-379">Vytvoření webové aplikace ContosoUniversity Razor Pages</span><span class="sxs-lookup"><span data-stu-id="ad742-379">Create the ContosoUniversity Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad742-380">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad742-380">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ad742-381">V nabídce **soubor** sady Visual Studio vyberte **Nový** > **projekt**.</span><span class="sxs-lookup"><span data-stu-id="ad742-381">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="ad742-382">Vytvořte novou ASP.NET Core webovou aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ad742-382">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="ad742-383">Pojmenujte projekt **ContosoUniversity**.</span><span class="sxs-lookup"><span data-stu-id="ad742-383">Name the project **ContosoUniversity**.</span></span> <span data-ttu-id="ad742-384">Je důležité pojmenovat projekt *ContosoUniversity* , aby se obory názvů shodovaly, když je kód zkopírován/vložen.</span><span class="sxs-lookup"><span data-stu-id="ad742-384">It's important to name the project *ContosoUniversity* so the namespaces match when code is copy/pasted.</span></span>
* <span data-ttu-id="ad742-385">V rozevíracím seznamu vyberte **ASP.NET Core 2,1** a potom vyberte možnost **Webová aplikace**.</span><span class="sxs-lookup"><span data-stu-id="ad742-385">Select **ASP.NET Core 2.1** in the dropdown, and then select **Web Application**.</span></span>

<span data-ttu-id="ad742-386">Obrázky předchozích kroků najdete v tématu [Vytvoření webové aplikace Razor](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span><span class="sxs-lookup"><span data-stu-id="ad742-386">For images of the preceding steps, see [Create a Razor web app](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app).</span></span>
<span data-ttu-id="ad742-387">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ad742-387">Run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad742-388">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad742-388">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a><span data-ttu-id="ad742-389">Nastavení stylu webu</span><span class="sxs-lookup"><span data-stu-id="ad742-389">Set up the site style</span></span>

<span data-ttu-id="ad742-390">Několik změn nastaví nabídku webu, rozložení a domovskou stránku.</span><span class="sxs-lookup"><span data-stu-id="ad742-390">A few changes set up the site menu, layout, and home page.</span></span> <span data-ttu-id="ad742-391">Update *Pages/Shared/_Layout. cshtml* s následujícími změnami:</span><span class="sxs-lookup"><span data-stu-id="ad742-391">Update *Pages/Shared/_Layout.cshtml* with the following changes:</span></span>

* <span data-ttu-id="ad742-392">Změňte všechny výskyty "ContosoUniversity" na "contoso University".</span><span class="sxs-lookup"><span data-stu-id="ad742-392">Change each occurrence of "ContosoUniversity" to "Contoso University".</span></span> <span data-ttu-id="ad742-393">Existují tři výskyty.</span><span class="sxs-lookup"><span data-stu-id="ad742-393">There are three occurrences.</span></span>

* <span data-ttu-id="ad742-394">Přidejte položky nabídky pro **studenty**, **kurzy**, **instruktory**a **oddělení**a odstraňte položku nabídky **kontakt** .</span><span class="sxs-lookup"><span data-stu-id="ad742-394">Add menu entries for **Students**, **Courses**, **Instructors**, and **Departments**, and delete the **Contact** menu entry.</span></span>

<span data-ttu-id="ad742-395">Změny jsou zvýrazněny.</span><span class="sxs-lookup"><span data-stu-id="ad742-395">The changes are highlighted.</span></span> <span data-ttu-id="ad742-396">(Všechny *značky se nezobrazí* .)</span><span class="sxs-lookup"><span data-stu-id="ad742-396">(All the markup is *not* displayed.)</span></span>

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

<span data-ttu-id="ad742-397">Na *stránce pages/index. cshtml*nahraďte obsah souboru následujícím kódem, který nahradí text o ASP.NET a MVC textem o této aplikaci:</span><span class="sxs-lookup"><span data-stu-id="ad742-397">In *Pages/Index.cshtml*, replace the contents of the file with the following code to replace the text about ASP.NET and MVC with text about this app:</span></span>

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a><span data-ttu-id="ad742-398">Vytvoření datového modelu</span><span class="sxs-lookup"><span data-stu-id="ad742-398">Create the data model</span></span>

<span data-ttu-id="ad742-399">Vytvořte třídy entit pro aplikaci Contoso University.</span><span class="sxs-lookup"><span data-stu-id="ad742-399">Create entity classes for the Contoso University app.</span></span> <span data-ttu-id="ad742-400">Začněte následujícími třemi entitami:</span><span class="sxs-lookup"><span data-stu-id="ad742-400">Start with the following three entities:</span></span>

![Kurz – registrace – diagram datového modelu studenta](intro/_static/data-model-diagram.png)

<span data-ttu-id="ad742-402">Mezi `Student` entitami a `Enrollment` entitami je vztah 1: n.</span><span class="sxs-lookup"><span data-stu-id="ad742-402">There's a one-to-many relationship between `Student` and `Enrollment` entities.</span></span> <span data-ttu-id="ad742-403">Mezi `Course` entitami a `Enrollment` entitami je vztah 1: n.</span><span class="sxs-lookup"><span data-stu-id="ad742-403">There's a one-to-many relationship between `Course` and `Enrollment` entities.</span></span> <span data-ttu-id="ad742-404">Student se může zaregistrovat v jakémkoli počtu kurzů.</span><span class="sxs-lookup"><span data-stu-id="ad742-404">A student can enroll in any number of courses.</span></span> <span data-ttu-id="ad742-405">Kurz může mít zaregistrovaný libovolný počet studentů.</span><span class="sxs-lookup"><span data-stu-id="ad742-405">A course can have any number of students enrolled in it.</span></span>

<span data-ttu-id="ad742-406">V následujících oddílech je vytvořena třída pro každou z těchto entit.</span><span class="sxs-lookup"><span data-stu-id="ad742-406">In the following sections, a class for each one of these entities is created.</span></span>

### <a name="the-student-entity"></a><span data-ttu-id="ad742-407">Entita studenta</span><span class="sxs-lookup"><span data-stu-id="ad742-407">The Student entity</span></span>

![Diagram entity studenta](intro/_static/student-entity.png)

<span data-ttu-id="ad742-409">Vytvořte složku *modelů* .</span><span class="sxs-lookup"><span data-stu-id="ad742-409">Create a *Models* folder.</span></span> <span data-ttu-id="ad742-410">Ve složce *modely* vytvořte soubor třídy s názvem *student.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ad742-410">In the *Models* folder, create a class file named *Student.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

<span data-ttu-id="ad742-411">`ID` Vlastnost se zobrazí jako sloupec primárního klíče tabulky databáze (DB), která odpovídá této třídě.</span><span class="sxs-lookup"><span data-stu-id="ad742-411">The `ID` property becomes the primary key column of the database (DB) table that corresponds to this class.</span></span> <span data-ttu-id="ad742-412">Ve výchozím nastavení EF Core interpretuje vlastnost s názvem `ID` nebo `classnameID` jako primární klíč.</span><span class="sxs-lookup"><span data-stu-id="ad742-412">By default, EF Core interprets a property that's named `ID` or `classnameID` as the primary key.</span></span> <span data-ttu-id="ad742-413">V `classnameID` `classname` je název třídy.</span><span class="sxs-lookup"><span data-stu-id="ad742-413">In `classnameID`, `classname` is the name of the class.</span></span> <span data-ttu-id="ad742-414">Alternativní automaticky rozpoznaný primární klíč je `StudentID` v předchozím příkladu.</span><span class="sxs-lookup"><span data-stu-id="ad742-414">The alternative automatically recognized primary key is `StudentID` in the preceding example.</span></span>

<span data-ttu-id="ad742-415">`Enrollments` Vlastnost je [navigační vlastnost](/ef/core/modeling/relationships).</span><span class="sxs-lookup"><span data-stu-id="ad742-415">The `Enrollments` property is a [navigation property](/ef/core/modeling/relationships).</span></span> <span data-ttu-id="ad742-416">Vlastnosti navigace odkazují na další entity, které se vztahují k této entitě.</span><span class="sxs-lookup"><span data-stu-id="ad742-416">Navigation properties link to other entities that are related to this entity.</span></span> <span data-ttu-id="ad742-417">`Enrollments` V tomto případě `Student entity` vlastnost obsahuje všechny `Enrollment` entity, které jsou v relaci. `Student`</span><span class="sxs-lookup"><span data-stu-id="ad742-417">In this case, the `Enrollments` property of a `Student entity` holds all of the `Enrollment` entities that are related to that `Student`.</span></span> <span data-ttu-id="ad742-418">Například pokud má řádek studenta v databázi dva související řádky registrace, `Enrollments` navigační vlastnost obsahuje tyto dvě `Enrollment` entity.</span><span class="sxs-lookup"><span data-stu-id="ad742-418">For example, if a Student row in the DB has two related Enrollment rows, the `Enrollments` navigation property contains those two `Enrollment` entities.</span></span> <span data-ttu-id="ad742-419">Související `Enrollment` řádek je řádek, který obsahuje hodnotu primárního klíče tohoto studenta ve `StudentID` sloupci.</span><span class="sxs-lookup"><span data-stu-id="ad742-419">A related `Enrollment` row is a row that contains that student's primary key value in the `StudentID` column.</span></span> <span data-ttu-id="ad742-420">Předpokládejme například, že student s ID = 1 má dva řádky v `Enrollment` tabulce.</span><span class="sxs-lookup"><span data-stu-id="ad742-420">For example, suppose the student with ID=1 has two rows in the `Enrollment` table.</span></span> <span data-ttu-id="ad742-421">`Enrollment` Tabulka má dva řádky s `StudentID` = 1.</span><span class="sxs-lookup"><span data-stu-id="ad742-421">The `Enrollment` table has two rows with `StudentID` = 1.</span></span> <span data-ttu-id="ad742-422">`StudentID`je cizí klíč v `Enrollment` tabulce, který určuje studenta v `Student` tabulce.</span><span class="sxs-lookup"><span data-stu-id="ad742-422">`StudentID` is a foreign key in the `Enrollment` table that specifies the student in the `Student` table.</span></span>

<span data-ttu-id="ad742-423">Pokud navigační vlastnost může obsahovat více entit, musí být vlastnost navigace typu seznam, například `ICollection<T>`.</span><span class="sxs-lookup"><span data-stu-id="ad742-423">If a navigation property can hold multiple entities, the navigation property must be a list type, such as `ICollection<T>`.</span></span> <span data-ttu-id="ad742-424">`ICollection<T>`lze zadat nebo typ jako `List<T>` nebo. `HashSet<T>`</span><span class="sxs-lookup"><span data-stu-id="ad742-424">`ICollection<T>` can be specified, or a type such as `List<T>` or `HashSet<T>`.</span></span> <span data-ttu-id="ad742-425">Při `ICollection<T>` použití EF Core vytvoří ve výchozím nastavení `HashSet<T>` kolekci.</span><span class="sxs-lookup"><span data-stu-id="ad742-425">When `ICollection<T>` is used, EF Core creates a `HashSet<T>` collection by default.</span></span> <span data-ttu-id="ad742-426">Navigační vlastnosti, které obsahují více entit, přicházejí ze vztahů m:n a 1: n.</span><span class="sxs-lookup"><span data-stu-id="ad742-426">Navigation properties that hold multiple entities come from many-to-many and one-to-many relationships.</span></span>

### <a name="the-enrollment-entity"></a><span data-ttu-id="ad742-427">Entita registrace</span><span class="sxs-lookup"><span data-stu-id="ad742-427">The Enrollment entity</span></span>

![Diagram entity registrace](intro/_static/enrollment-entity.png)

<span data-ttu-id="ad742-429">Ve složce *modely* vytvořte *Enrollment.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ad742-429">In the *Models* folder, create *Enrollment.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

<span data-ttu-id="ad742-430">`EnrollmentID` Vlastnost je primární klíč.</span><span class="sxs-lookup"><span data-stu-id="ad742-430">The `EnrollmentID` property is the primary key.</span></span> <span data-ttu-id="ad742-431">Tato entita používá `classnameID` vzor místo `ID` podobně jako `Student` entita.</span><span class="sxs-lookup"><span data-stu-id="ad742-431">This entity uses the `classnameID` pattern instead of `ID` like the `Student` entity.</span></span> <span data-ttu-id="ad742-432">Vývojáři obvykle volí jeden model a používají ho v rámci datového modelu.</span><span class="sxs-lookup"><span data-stu-id="ad742-432">Typically developers choose one pattern and use it throughout the data model.</span></span> <span data-ttu-id="ad742-433">V pozdějším kurzu se zobrazuje použití ID bez ClassName, které usnadňuje implementaci dědičnosti v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="ad742-433">In a later tutorial, using ID without classname is shown to make it easier to implement inheritance in the data model.</span></span>

<span data-ttu-id="ad742-434">`Grade` Vlastnost je `enum`.</span><span class="sxs-lookup"><span data-stu-id="ad742-434">The `Grade` property is an `enum`.</span></span> <span data-ttu-id="ad742-435">Otazník po deklaraci `Grade` typu označuje, že vlastnost může `Grade` mít hodnotu null.</span><span class="sxs-lookup"><span data-stu-id="ad742-435">The question mark after the `Grade` type declaration indicates that the `Grade` property is nullable.</span></span> <span data-ttu-id="ad742-436">Hodnota, která je null, se liší od nulové třídy – hodnota null znamená, že se nejedná o známku nebo ještě není přiřazená.</span><span class="sxs-lookup"><span data-stu-id="ad742-436">A grade that's null is different from a zero grade -- null means a grade isn't known or hasn't been assigned yet.</span></span>

<span data-ttu-id="ad742-437">`StudentID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Student`.</span><span class="sxs-lookup"><span data-stu-id="ad742-437">The `StudentID` property is a foreign key, and the corresponding navigation property is `Student`.</span></span> <span data-ttu-id="ad742-438">`Enrollment` Entita je přidružená k jedné `Student` entitě, takže vlastnost obsahuje jednu `Student` entitu.</span><span class="sxs-lookup"><span data-stu-id="ad742-438">An `Enrollment` entity is associated with one `Student` entity, so the property contains a single `Student` entity.</span></span> <span data-ttu-id="ad742-439">`Student` Entita se liší od `Student.Enrollments` navigační vlastnosti, která obsahuje více `Enrollment` entit.</span><span class="sxs-lookup"><span data-stu-id="ad742-439">The `Student` entity differs from the `Student.Enrollments` navigation property, which contains multiple `Enrollment` entities.</span></span>

<span data-ttu-id="ad742-440">`CourseID` Vlastnost je cizí klíč a odpovídající navigační vlastnost je `Course`.</span><span class="sxs-lookup"><span data-stu-id="ad742-440">The `CourseID` property is a foreign key, and the corresponding navigation property is `Course`.</span></span> <span data-ttu-id="ad742-441">`Enrollment` Entita je přidružená k jedné `Course` entitě.</span><span class="sxs-lookup"><span data-stu-id="ad742-441">An `Enrollment` entity is associated with one `Course` entity.</span></span>

<span data-ttu-id="ad742-442">EF Core interpretuje vlastnost jako cizí klíč, pokud má název `<navigation property name><primary key property name>`.</span><span class="sxs-lookup"><span data-stu-id="ad742-442">EF Core interprets a property as a foreign key if it's named `<navigation property name><primary key property name>`.</span></span> <span data-ttu-id="ad742-443">Například`StudentID` pro vlastnost `Student` navigace, protože primární klíč `Student` entity je. `ID`</span><span class="sxs-lookup"><span data-stu-id="ad742-443">For example,`StudentID` for the `Student` navigation property, since the `Student` entity's primary key is `ID`.</span></span> <span data-ttu-id="ad742-444">Lze také pojmenovat `<primary key property name>`vlastnosti cizího klíče.</span><span class="sxs-lookup"><span data-stu-id="ad742-444">Foreign key properties can also be named `<primary key property name>`.</span></span> <span data-ttu-id="ad742-445">Například vzhledem k `CourseID` tomu, `Course` že primární klíč entity je `CourseID`.</span><span class="sxs-lookup"><span data-stu-id="ad742-445">For example, `CourseID` since the `Course` entity's primary key is `CourseID`.</span></span>

### <a name="the-course-entity"></a><span data-ttu-id="ad742-446">Entita kurzu</span><span class="sxs-lookup"><span data-stu-id="ad742-446">The Course entity</span></span>

![Diagram entity kurzu](intro/_static/course-entity.png)

<span data-ttu-id="ad742-448">Ve složce *modely* vytvořte *Course.cs* s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="ad742-448">In the *Models* folder, create *Course.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

<span data-ttu-id="ad742-449">`Enrollments` Vlastnost je navigační vlastnost.</span><span class="sxs-lookup"><span data-stu-id="ad742-449">The `Enrollments` property is a navigation property.</span></span> <span data-ttu-id="ad742-450">`Course` Entita může souviset s libovolným počtem `Enrollment` entit.</span><span class="sxs-lookup"><span data-stu-id="ad742-450">A `Course` entity can be related to any number of `Enrollment` entities.</span></span>

<span data-ttu-id="ad742-451">`DatabaseGenerated` Atribut umožňuje aplikaci zadat primární klíč, a ne vytvořit databázi.</span><span class="sxs-lookup"><span data-stu-id="ad742-451">The `DatabaseGenerated` attribute allows the app to specify the primary key rather than having the DB generate it.</span></span>

## <a name="scaffold-the-student-model"></a><span data-ttu-id="ad742-452">Generování uživatelského rozhraní modelu studenta</span><span class="sxs-lookup"><span data-stu-id="ad742-452">Scaffold the student model</span></span>

<span data-ttu-id="ad742-453">V této části je model studenta vygenerovaný jako generátor.</span><span class="sxs-lookup"><span data-stu-id="ad742-453">In this section, the student model is scaffolded.</span></span> <span data-ttu-id="ad742-454">To znamená, že nástroj pro generování uživatelského rozhraní vytváří stránky pro operace vytvoření, čtení, aktualizace a odstranění (CRUD) pro model studenta.</span><span class="sxs-lookup"><span data-stu-id="ad742-454">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the student model.</span></span>

* <span data-ttu-id="ad742-455">Sestavte projekt.</span><span class="sxs-lookup"><span data-stu-id="ad742-455">Build the project.</span></span>
* <span data-ttu-id="ad742-456">Vytvořte složku *stránky/studenty* .</span><span class="sxs-lookup"><span data-stu-id="ad742-456">Create the *Pages/Students* folder.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad742-457">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad742-457">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="ad742-458">V **Průzkumník řešení**klikněte pravým tlačítkem myši na složku *Pages/Students* > **Přidat** > **novou vygenerované položky**.</span><span class="sxs-lookup"><span data-stu-id="ad742-458">In **Solution Explorer**, right click on the *Pages/Students* folder > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="ad742-459">V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte možnost **Razor Pages pomocí Entity Framework (CRUD)** > **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="ad742-459">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **ADD**.</span></span>

<span data-ttu-id="ad742-460">Dokončete dialog **přidat Razor Pages pomocí Entity Framework (CRUD)** :</span><span class="sxs-lookup"><span data-stu-id="ad742-460">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="ad742-461">V rozevíracím seznamu **třída modelu** vyberte **student (ContosoUniversity. Models)**.</span><span class="sxs-lookup"><span data-stu-id="ad742-461">In the **Model class** drop-down, select **Student (ContosoUniversity.Models)**.</span></span>
* <span data-ttu-id="ad742-462">V řádku **třídy kontextu dat** vyberte znaménko **+** (plus) a změňte vygenerovaný název na **ContosoUniversity. Models. SchoolContext**.</span><span class="sxs-lookup"><span data-stu-id="ad742-462">In the **Data context class** row, select the **+** (plus) sign and change the generated name to **ContosoUniversity.Models.SchoolContext**.</span></span>
* <span data-ttu-id="ad742-463">V rozevíracím seznamu **Třída kontextu dat** vyberte **ContosoUniversity. Models. SchoolContext.**</span><span class="sxs-lookup"><span data-stu-id="ad742-463">In the **Data context class** drop-down,  select **ContosoUniversity.Models.SchoolContext**</span></span>
* <span data-ttu-id="ad742-464">Vyberte **Přidat**.</span><span class="sxs-lookup"><span data-stu-id="ad742-464">Select **Add**.</span></span>

![Dialogové okno CRUD](intro/_static/s1.png)

<span data-ttu-id="ad742-466">Pokud máte problém s předchozím krokem, podívejte [se do části vygenerované modely filmů](xref:tutorials/razor-pages/model#scaffold-the-movie-model) .</span><span class="sxs-lookup"><span data-stu-id="ad742-466">See [Scaffold the movie model](xref:tutorials/razor-pages/model#scaffold-the-movie-model) if you have a problem with the preceding step.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad742-467">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad742-467">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="ad742-468">Spusťte následující příkazy pro generování uživatelského rozhraní modelu studenta.</span><span class="sxs-lookup"><span data-stu-id="ad742-468">Run the following commands to scaffold the student model.</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

<span data-ttu-id="ad742-469">Proces generování uživatelského rozhraní byl vytvořen a změnil následující soubory:</span><span class="sxs-lookup"><span data-stu-id="ad742-469">The scaffold process created and changed the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="ad742-470">Vytvořené soubory</span><span class="sxs-lookup"><span data-stu-id="ad742-470">Files created</span></span>

* <span data-ttu-id="ad742-471">*Stránky/studenty* Vytvořit, odstranit, podrobnosti, upravit, index.</span><span class="sxs-lookup"><span data-stu-id="ad742-471">*Pages/Students* Create, Delete, Details, Edit, Index.</span></span>
* <span data-ttu-id="ad742-472">*Data/SchoolContext. cs*</span><span class="sxs-lookup"><span data-stu-id="ad742-472">*Data/SchoolContext.cs*</span></span>

### <a name="file-updates"></a><span data-ttu-id="ad742-473">Aktualizace souborů</span><span class="sxs-lookup"><span data-stu-id="ad742-473">File updates</span></span>

* <span data-ttu-id="ad742-474">*Startup.cs* : změny v tomto souboru jsou podrobně popsané v další části.</span><span class="sxs-lookup"><span data-stu-id="ad742-474">*Startup.cs* : Changes to this file are detailed in the next section.</span></span>
* <span data-ttu-id="ad742-475">*appSettings. JSON* : Přidal se připojovací řetězec použitý k připojení k místní databázi.</span><span class="sxs-lookup"><span data-stu-id="ad742-475">*appsettings.json* : The connection string used to connect to a local database is added.</span></span>

## <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="ad742-476">Kontrola kontextu zaregistrovaného vkládáním závislostí</span><span class="sxs-lookup"><span data-stu-id="ad742-476">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="ad742-477">ASP.NET Core je sestaven s [vkládáním závislostí](xref:fundamentals/dependency-injection).</span><span class="sxs-lookup"><span data-stu-id="ad742-477">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ad742-478">Služby (například kontext EF Core DB) jsou registrovány pomocí injektáže závislosti při spuštění aplikace.</span><span class="sxs-lookup"><span data-stu-id="ad742-478">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="ad742-479">Komponenty, které vyžadují tyto služby (například Razor Pages), poskytují tyto služby prostřednictvím parametrů konstruktoru.</span><span class="sxs-lookup"><span data-stu-id="ad742-479">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="ad742-480">Kód konstruktoru, který získá instanci kontextu databáze, je uveden dále v tomto kurzu.</span><span class="sxs-lookup"><span data-stu-id="ad742-480">The constructor code that gets a db context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="ad742-481">Nástroj pro generování uživatelského rozhraní automaticky vytvořil kontext databáze a zaregistroval ho pomocí kontejneru pro vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="ad742-481">The scaffolding tool automatically created a DB Context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="ad742-482">Projděte `ConfigureServices` si metodu v *Startup.cs*.</span><span class="sxs-lookup"><span data-stu-id="ad742-482">Examine the `ConfigureServices` method in *Startup.cs*.</span></span> <span data-ttu-id="ad742-483">Zvýrazněný řádek byl přidán do modulu generování uživatelského rozhraní:</span><span class="sxs-lookup"><span data-stu-id="ad742-483">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

<span data-ttu-id="ad742-484">Název připojovacího řetězce je předán do kontextu voláním metody v objektu [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) .</span><span class="sxs-lookup"><span data-stu-id="ad742-484">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="ad742-485">Pro místní vývoj načítá [konfigurační systém ASP.NET Core](xref:fundamentals/configuration/index) připojovací řetězec ze souboru *appSettings. JSON* .</span><span class="sxs-lookup"><span data-stu-id="ad742-485">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

## <a name="update-main"></a><span data-ttu-id="ad742-486">Aktualizace Main</span><span class="sxs-lookup"><span data-stu-id="ad742-486">Update main</span></span>

<span data-ttu-id="ad742-487">V *program.cs*upravte `Main` metodu a proveďte následující:</span><span class="sxs-lookup"><span data-stu-id="ad742-487">In *Program.cs*, modify the `Main` method to do the following:</span></span>

* <span data-ttu-id="ad742-488">Získá instanci kontextu databáze z kontejneru vkládání závislostí.</span><span class="sxs-lookup"><span data-stu-id="ad742-488">Get a DB context instance from the dependency injection container.</span></span>
* <span data-ttu-id="ad742-489">Zavolejte [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span><span class="sxs-lookup"><span data-stu-id="ad742-489">Call the  [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated).</span></span>
* <span data-ttu-id="ad742-490">Vyřazení kontextu po dokončení `EnsureCreated` metody.</span><span class="sxs-lookup"><span data-stu-id="ad742-490">Dispose the context when the `EnsureCreated` method completes.</span></span>

<span data-ttu-id="ad742-491">Následující kód ukazuje aktualizovaný soubor *program.cs* .</span><span class="sxs-lookup"><span data-stu-id="ad742-491">The following code shows the updated *Program.cs* file.</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

<span data-ttu-id="ad742-492">`EnsureCreated`zajišťuje, aby databáze pro kontext existovala.</span><span class="sxs-lookup"><span data-stu-id="ad742-492">`EnsureCreated` ensures that the database for the context exists.</span></span> <span data-ttu-id="ad742-493">Pokud existuje, není provedena žádná akce.</span><span class="sxs-lookup"><span data-stu-id="ad742-493">If it exists, no action is taken.</span></span> <span data-ttu-id="ad742-494">Pokud neexistuje, vytvoří se databáze a všechny její schéma.</span><span class="sxs-lookup"><span data-stu-id="ad742-494">If it does not exist, then the database and all its schema are created.</span></span> <span data-ttu-id="ad742-495">`EnsureCreated`nepoužívá k vytvoření databáze migrace.</span><span class="sxs-lookup"><span data-stu-id="ad742-495">`EnsureCreated` does not use migrations to create the database.</span></span> <span data-ttu-id="ad742-496">Databázi, která je vytvořená `EnsureCreated` pomocí, se nedá později aktualizovat pomocí migrace.</span><span class="sxs-lookup"><span data-stu-id="ad742-496">A database that is created with `EnsureCreated` cannot be later updated using migrations.</span></span>

<span data-ttu-id="ad742-497">`EnsureCreated`se volá při spuštění aplikace, což umožňuje následující pracovní postup:</span><span class="sxs-lookup"><span data-stu-id="ad742-497">`EnsureCreated` is called on app start, which allows the following work flow:</span></span>

* <span data-ttu-id="ad742-498">Odstraňte databázi.</span><span class="sxs-lookup"><span data-stu-id="ad742-498">Delete the DB.</span></span>
* <span data-ttu-id="ad742-499">Změňte schéma databáze (například přidat `EmailAddress` pole).</span><span class="sxs-lookup"><span data-stu-id="ad742-499">Change the DB schema (for example, add an `EmailAddress` field).</span></span>
* <span data-ttu-id="ad742-500">Spusťte aplikaci.</span><span class="sxs-lookup"><span data-stu-id="ad742-500">Run the app.</span></span>
* <span data-ttu-id="ad742-501">`EnsureCreated`vytvoří databázi se`EmailAddress` sloupcem.</span><span class="sxs-lookup"><span data-stu-id="ad742-501">`EnsureCreated` creates a DB with the`EmailAddress` column.</span></span>

<span data-ttu-id="ad742-502">`EnsureCreated`je pohodlná v brzké fázi vývoje, když se schéma rychle vyvíjí.</span><span class="sxs-lookup"><span data-stu-id="ad742-502">`EnsureCreated` is convenient early in development when the schema is rapidly evolving.</span></span> <span data-ttu-id="ad742-503">Později v kurzu se databáze odstraní a použijí se migrace.</span><span class="sxs-lookup"><span data-stu-id="ad742-503">Later in the tutorial the DB is deleted and migrations are used.</span></span>

### <a name="test-the-app"></a><span data-ttu-id="ad742-504">Otestování aplikace</span><span class="sxs-lookup"><span data-stu-id="ad742-504">Test the app</span></span>

<span data-ttu-id="ad742-505">Spusťte aplikaci a přijměte zásady souborů cookie.</span><span class="sxs-lookup"><span data-stu-id="ad742-505">Run the app and accept the cookie policy.</span></span> <span data-ttu-id="ad742-506">Tato aplikace neuchovává osobní údaje.</span><span class="sxs-lookup"><span data-stu-id="ad742-506">This app doesn't keep personal information.</span></span> <span data-ttu-id="ad742-507">Informace o zásadách souborů cookie najdete v tématu [Podpora pro EU obecné nařízení o ochraně osobních údajů (GDPR)](xref:security/gdpr).</span><span class="sxs-lookup"><span data-stu-id="ad742-507">You can read about the cookie policy at [EU General Data Protection Regulation (GDPR) support](xref:security/gdpr).</span></span>

* <span data-ttu-id="ad742-508">Vyberte odkaz **Students** a pak **vytvořte nový**.</span><span class="sxs-lookup"><span data-stu-id="ad742-508">Select the **Students** link and then **Create New**.</span></span>
* <span data-ttu-id="ad742-509">Otestujte odkazy Upravit, Podrobnosti a Odstranit.</span><span class="sxs-lookup"><span data-stu-id="ad742-509">Test the Edit, Details, and Delete links.</span></span>

## <a name="examine-the-schoolcontext-db-context"></a><span data-ttu-id="ad742-510">Projděte si kontext SchoolContext DB.</span><span class="sxs-lookup"><span data-stu-id="ad742-510">Examine the SchoolContext DB context</span></span>

<span data-ttu-id="ad742-511">Hlavní třída, která koordinuje funkce EF Core pro daný datový model, je třída kontextu databáze.</span><span class="sxs-lookup"><span data-stu-id="ad742-511">The main class that coordinates EF Core functionality for a given data model is the DB context class.</span></span> <span data-ttu-id="ad742-512">Kontext dat je odvozen od třídy [Microsoft. EntityFrameworkCore. DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span><span class="sxs-lookup"><span data-stu-id="ad742-512">The data context is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="ad742-513">Kontext dat určuje, které entity jsou zahrnuty v datovém modelu.</span><span class="sxs-lookup"><span data-stu-id="ad742-513">The data context specifies which entities are included in the data model.</span></span> <span data-ttu-id="ad742-514">V tomto projektu je třída pojmenována `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="ad742-514">In this project, the class is named `SchoolContext`.</span></span>

<span data-ttu-id="ad742-515">*SchoolContext.cs* aktualizujte pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="ad742-515">Update *SchoolContext.cs* with the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

<span data-ttu-id="ad742-516">Zvýrazněný kód vytvoří vlastnost [negenerickými\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) pro každou sadu entit.</span><span class="sxs-lookup"><span data-stu-id="ad742-516">The highlighted code creates a [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for each entity set.</span></span> <span data-ttu-id="ad742-517">V terminologii EF Core:</span><span class="sxs-lookup"><span data-stu-id="ad742-517">In EF Core terminology:</span></span>

* <span data-ttu-id="ad742-518">Sada entit obvykle odpovídá tabulce databáze.</span><span class="sxs-lookup"><span data-stu-id="ad742-518">An entity set typically corresponds to a DB table.</span></span>
* <span data-ttu-id="ad742-519">Entita odpovídá řádku v tabulce.</span><span class="sxs-lookup"><span data-stu-id="ad742-519">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="ad742-520">`DbSet<Enrollment>`a `DbSet<Course>` může být vynecháno.</span><span class="sxs-lookup"><span data-stu-id="ad742-520">`DbSet<Enrollment>` and `DbSet<Course>` could be omitted.</span></span> <span data-ttu-id="ad742-521">EF Core je implicitně obsahuje, protože `Student` entita odkazuje na `Enrollment` entitu a `Enrollment` entita odkazuje na `Course` entitu.</span><span class="sxs-lookup"><span data-stu-id="ad742-521">EF Core includes them implicitly because the `Student` entity references the `Enrollment` entity, and the `Enrollment` entity references the `Course` entity.</span></span> <span data-ttu-id="ad742-522">V tomto kurzu ponechejte `DbSet<Enrollment>` a `DbSet<Course>` v `SchoolContext`.</span><span class="sxs-lookup"><span data-stu-id="ad742-522">For this tutorial, keep `DbSet<Enrollment>` and `DbSet<Course>` in the `SchoolContext`.</span></span>

### <a name="sql-server-express-localdb"></a><span data-ttu-id="ad742-523">SQL Server Express LocalDB</span><span class="sxs-lookup"><span data-stu-id="ad742-523">SQL Server Express LocalDB</span></span>

<span data-ttu-id="ad742-524">Připojovací řetězec Určuje [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span><span class="sxs-lookup"><span data-stu-id="ad742-524">The connection string specifies [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb).</span></span> <span data-ttu-id="ad742-525">LocalDB je zjednodušená verze databázového stroje SQL Server Express a je určena pro vývoj aplikací, nikoli za použití v produkčním prostředí.</span><span class="sxs-lookup"><span data-stu-id="ad742-525">LocalDB is a lightweight version of the SQL Server Express Database Engine and is intended for app development, not production use.</span></span> <span data-ttu-id="ad742-526">LocalDB se spustí na vyžádání a spustí se v uživatelském režimu, takže neexistuje složitá konfigurace.</span><span class="sxs-lookup"><span data-stu-id="ad742-526">LocalDB starts on demand and runs in user mode, so there's no complex configuration.</span></span> <span data-ttu-id="ad742-527">Ve výchozím nastavení LocalDB vytvoří soubory *. mdf* DB v `C:/Users/<user>` adresáři.</span><span class="sxs-lookup"><span data-stu-id="ad742-527">By default, LocalDB creates *.mdf* DB files in the `C:/Users/<user>` directory.</span></span>

## <a name="add-code-to-initialize-the-db-with-test-data"></a><span data-ttu-id="ad742-528">Přidejte kód pro inicializaci databáze s testovacími daty</span><span class="sxs-lookup"><span data-stu-id="ad742-528">Add code to initialize the DB with test data</span></span>

<span data-ttu-id="ad742-529">EF Core vytvoří prázdnou databázi.</span><span class="sxs-lookup"><span data-stu-id="ad742-529">EF Core creates an empty DB.</span></span> <span data-ttu-id="ad742-530">V této části je zapsána `Initialize` metoda pro naplnění testovacích dat.</span><span class="sxs-lookup"><span data-stu-id="ad742-530">In this section, an `Initialize` method is written to populate it with test data.</span></span>

<span data-ttu-id="ad742-531">Ve složce *data* vytvořte nový soubor třídy s názvem *DbInitializer.cs* a přidejte následující kód:</span><span class="sxs-lookup"><span data-stu-id="ad742-531">In the *Data* folder, create a new class file named *DbInitializer.cs* and add the following code:</span></span>

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

<span data-ttu-id="ad742-532">Poznámka: předchozí kód používá `Models` pro obor názvů (`namespace ContosoUniversity.Models`) spíše než. `Data`</span><span class="sxs-lookup"><span data-stu-id="ad742-532">Note: The preceding code uses `Models` for the namespace (`namespace ContosoUniversity.Models`) rather than `Data`.</span></span> <span data-ttu-id="ad742-533">`Models`je konzistentní s kódem generovaným pomocí generování uživatelského rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ad742-533">`Models` is consistent with the scaffolder-generated code.</span></span> <span data-ttu-id="ad742-534">Další informace najdete v [tomto problému s vygenerováním uživatelského rozhraní GitHub](https://github.com/aspnet/Scaffolding/issues/822).</span><span class="sxs-lookup"><span data-stu-id="ad742-534">For more information, see [this GitHub scaffolding issue](https://github.com/aspnet/Scaffolding/issues/822).</span></span>

<span data-ttu-id="ad742-535">Kód zkontroluje, jestli v databázi existují studenti.</span><span class="sxs-lookup"><span data-stu-id="ad742-535">The code checks if there are any students in the DB.</span></span> <span data-ttu-id="ad742-536">Pokud databáze neobsahuje žádné studenty, databáze se inicializuje s testovacími daty.</span><span class="sxs-lookup"><span data-stu-id="ad742-536">If there are no students in the DB, the DB is initialized with test data.</span></span> <span data-ttu-id="ad742-537">Načte testovací data do polí, nikoli `List<T>` kolekce pro optimalizaci výkonu.</span><span class="sxs-lookup"><span data-stu-id="ad742-537">It loads test data into arrays rather than `List<T>` collections to optimize performance.</span></span>

<span data-ttu-id="ad742-538">`EnsureCreated` Metoda automaticky vytvoří databázi pro kontext databáze.</span><span class="sxs-lookup"><span data-stu-id="ad742-538">The `EnsureCreated` method automatically creates the DB for the DB context.</span></span> <span data-ttu-id="ad742-539">Pokud databáze existuje, `EnsureCreated` vrátí se beze změny databáze.</span><span class="sxs-lookup"><span data-stu-id="ad742-539">If the DB exists, `EnsureCreated` returns without modifying the DB.</span></span>

<span data-ttu-id="ad742-540">V *program.cs*upravte `Main` metodu pro volání `Initialize`:</span><span class="sxs-lookup"><span data-stu-id="ad742-540">In *Program.cs*, modify the `Main` method to call `Initialize`:</span></span>

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

# <a name="visual-studio"></a>[<span data-ttu-id="ad742-541">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad742-541">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ad742-542">Zastavte aplikaci, pokud je spuštěná, a spusťte následující příkaz v **konzole správce balíčků** (PMC):</span><span class="sxs-lookup"><span data-stu-id="ad742-542">Stop the app if it's running, and run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="ad742-543">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="ad742-543">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="ad742-544">Zastavte aplikaci, pokud je spuštěná, a odstraňte soubor *cu. DB* .</span><span class="sxs-lookup"><span data-stu-id="ad742-544">Stop the app if it's running, and delete the *CU.db* file.</span></span>

---

## <a name="view-the-db"></a><span data-ttu-id="ad742-545">Zobrazení databáze</span><span class="sxs-lookup"><span data-stu-id="ad742-545">View the DB</span></span>

<span data-ttu-id="ad742-546">Název databáze je vygenerován z dříve zadaného názvu kontextu plus pomlčka a identifikátor GUID.</span><span class="sxs-lookup"><span data-stu-id="ad742-546">The database name is generated from the context name you provided earlier plus a dash and a GUID.</span></span> <span data-ttu-id="ad742-547">Proto bude název databáze "SchoolContext-{GUID}".</span><span class="sxs-lookup"><span data-stu-id="ad742-547">Thus, the database name will be "SchoolContext-{GUID}".</span></span> <span data-ttu-id="ad742-548">Identifikátor GUID bude pro každého uživatele odlišný.</span><span class="sxs-lookup"><span data-stu-id="ad742-548">The GUID will be different for each user.</span></span>
<span data-ttu-id="ad742-549">Otevřete **Průzkumník objektů systému SQL Server** (SSOX) z nabídky **Zobrazit** v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ad742-549">Open **SQL Server Object Explorer** (SSOX) from the **View** menu in Visual Studio.</span></span>
<span data-ttu-id="ad742-550">V SSOX klikněte na **(LocalDB) \MSSQLLocalDB > databáze > SchoolContext-{GUID}**.</span><span class="sxs-lookup"><span data-stu-id="ad742-550">In SSOX, click **(localdb)\MSSQLLocalDB > Databases > SchoolContext-{GUID}**.</span></span>

<span data-ttu-id="ad742-551">Rozbalte uzel **tabulky** .</span><span class="sxs-lookup"><span data-stu-id="ad742-551">Expand the **Tables** node.</span></span>

<span data-ttu-id="ad742-552">Klikněte pravým tlačítkem myši na tabulku **student** a kliknutím na **Zobrazit data** Zobrazte vytvořené sloupce a řádky vložené do tabulky.</span><span class="sxs-lookup"><span data-stu-id="ad742-552">Right-click the **Student** table and click **View Data** to see the columns created and the rows inserted into the table.</span></span>

## <a name="asynchronous-code"></a><span data-ttu-id="ad742-553">Asynchronní kód</span><span class="sxs-lookup"><span data-stu-id="ad742-553">Asynchronous code</span></span>

<span data-ttu-id="ad742-554">Asynchronní programování je výchozí režim pro ASP.NET Core a EF Core.</span><span class="sxs-lookup"><span data-stu-id="ad742-554">Asynchronous programming is the default mode for ASP.NET Core and EF Core.</span></span>

<span data-ttu-id="ad742-555">Na webovém serveru je k dispozici omezený počet vláken a v situacích vysokého zatížení se mohou používat všechna dostupná vlákna.</span><span class="sxs-lookup"><span data-stu-id="ad742-555">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="ad742-556">Pokud k tomu dojde, server nemůže zpracovat nové požadavky, dokud nebudou vlákna uvolněna.</span><span class="sxs-lookup"><span data-stu-id="ad742-556">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="ad742-557">Pomocí synchronního kódu může být mnoho vláken svázáno s tím, že ve skutečnosti neprovádí žádnou práci, protože čeká na dokončení vstupně-výstupních operací.</span><span class="sxs-lookup"><span data-stu-id="ad742-557">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="ad742-558">V případě asynchronního kódu, když proces čeká na dokončení vstupně-výstupních operací, je jeho vlákno uvolněno na server, který bude použit pro zpracování jiných požadavků.</span><span class="sxs-lookup"><span data-stu-id="ad742-558">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="ad742-559">Výsledkem je, že asynchronní kód umožňuje efektivnější použití prostředků serveru a server je povolen pro zpracování většího objemu dat bez prodlev.</span><span class="sxs-lookup"><span data-stu-id="ad742-559">As a result, asynchronous code enables server resources to be used more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="ad742-560">Asynchronní kód zavádí v době běhu malé množství režie.</span><span class="sxs-lookup"><span data-stu-id="ad742-560">Asynchronous code does introduce a small amount of overhead at run time.</span></span> <span data-ttu-id="ad742-561">V situacích s nízkým provozem je dosaženo zanedbatelného výkonu, zatímco v situacích vysokého provozu je potenciální zlepšení výkonu značné.</span><span class="sxs-lookup"><span data-stu-id="ad742-561">For low traffic situations, the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="ad742-562">V následujícím kódu, klíčové slovo [Async](/dotnet/csharp/language-reference/keywords/async) , `Task<T>` návratová hodnota, `await` klíčové slovo a `ToListAsync` Metoda provede asynchronní spouštění kódu.</span><span class="sxs-lookup"><span data-stu-id="ad742-562">In the following code, the [async](/dotnet/csharp/language-reference/keywords/async) keyword, `Task<T>` return value, `await` keyword, and `ToListAsync` method make the code execute asynchronously.</span></span>

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* <span data-ttu-id="ad742-563">`async` Klíčové slovo dává kompilátoru následující informace:</span><span class="sxs-lookup"><span data-stu-id="ad742-563">The `async` keyword tells the compiler to:</span></span>
  * <span data-ttu-id="ad742-564">Vygenerujte zpětná volání pro části těla metody.</span><span class="sxs-lookup"><span data-stu-id="ad742-564">Generate callbacks for parts of the method body.</span></span>
  * <span data-ttu-id="ad742-565">Automaticky vytvořte vrácený objekt [úkolu](/dotnet/api/system.threading.tasks.task) .</span><span class="sxs-lookup"><span data-stu-id="ad742-565">Automatically create the [Task](/dotnet/api/system.threading.tasks.task) object that's returned.</span></span> <span data-ttu-id="ad742-566">Další informace najdete v tématu [návratový typ úlohy](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span><span class="sxs-lookup"><span data-stu-id="ad742-566">For more information, see [Task Return Type](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType).</span></span>

* <span data-ttu-id="ad742-567">Implicitní návratový typ `Task` představuje probíhající práci.</span><span class="sxs-lookup"><span data-stu-id="ad742-567">The implicit return type `Task` represents ongoing work.</span></span>
* <span data-ttu-id="ad742-568">`await` Klíčové slovo způsobí, že kompilátor rozdělí metodu do dvou částí.</span><span class="sxs-lookup"><span data-stu-id="ad742-568">The `await` keyword causes the compiler to split the method into two parts.</span></span> <span data-ttu-id="ad742-569">První část končí asynchronně spuštěnou operací.</span><span class="sxs-lookup"><span data-stu-id="ad742-569">The first part ends with the operation that's started asynchronously.</span></span> <span data-ttu-id="ad742-570">Druhá část je vložena do metody zpětného volání, která je volána po dokončení operace.</span><span class="sxs-lookup"><span data-stu-id="ad742-570">The second part is put into a callback method that's called when the operation completes.</span></span>
* <span data-ttu-id="ad742-571">`ToListAsync`je asynchronní verze metody `ToList` rozšíření.</span><span class="sxs-lookup"><span data-stu-id="ad742-571">`ToListAsync` is the asynchronous version of the `ToList` extension method.</span></span>

<span data-ttu-id="ad742-572">Některé věci, které je potřeba znát při psaní asynchronního kódu, který používá EF Core:</span><span class="sxs-lookup"><span data-stu-id="ad742-572">Some things to be aware of when writing asynchronous code that uses EF Core:</span></span>

* <span data-ttu-id="ad742-573">Asynchronně jsou spouštěny pouze příkazy, které způsobují odeslání dotazů nebo příkazů do databáze.</span><span class="sxs-lookup"><span data-stu-id="ad742-573">Only statements that cause queries or commands to be sent to the DB are executed asynchronously.</span></span> <span data-ttu-id="ad742-574">To zahrnuje, `ToListAsync`, `SingleOrDefaultAsync` `FirstOrDefaultAsync`, a `SaveChangesAsync`.</span><span class="sxs-lookup"><span data-stu-id="ad742-574">That includes, `ToListAsync`, `SingleOrDefaultAsync`, `FirstOrDefaultAsync`, and `SaveChangesAsync`.</span></span> <span data-ttu-id="ad742-575">Neobsahuje příkazy, které mění pouze `IQueryable`, například. `var students = context.Students.Where(s => s.LastName == "Davolio")`</span><span class="sxs-lookup"><span data-stu-id="ad742-575">It doesn't include statements that just change an `IQueryable`, such as `var students = context.Students.Where(s => s.LastName == "Davolio")`.</span></span>
* <span data-ttu-id="ad742-576">Kontext EF Core není bezpečný pro přístup z více vláken: Nepokoušejte se současně provést více operací.</span><span class="sxs-lookup"><span data-stu-id="ad742-576">An EF Core context isn't thread safe: don't try to do multiple operations in parallel.</span></span>
* <span data-ttu-id="ad742-577">Chcete-li využít výhody z hlediska výkonu asynchronního kódu, ověřte, že balíčky knihoven (například pro stránkování) používají async, pokud volají EF Core metody, které odesílají dotazy do databáze.</span><span class="sxs-lookup"><span data-stu-id="ad742-577">To take advantage of the performance benefits of async code, verify that library packages (such as for paging) use async if they call EF Core methods that send queries to the DB.</span></span>

<span data-ttu-id="ad742-578">Další informace o asynchronním programování v rozhraní .NET naleznete v tématu [Async Overview](/dotnet/standard/async) a [Asynchronous Programming with Async a await](/dotnet/csharp/programming-guide/concepts/async/).</span><span class="sxs-lookup"><span data-stu-id="ad742-578">For more information about asynchronous programming in .NET, see [Async Overview](/dotnet/standard/async) and [Asynchronous programming with async and await](/dotnet/csharp/programming-guide/concepts/async/).</span></span>

<span data-ttu-id="ad742-579">V dalším kurzu se přezkoumávají základní operace CRUD (vytváření, čtení, aktualizace, odstranění).</span><span class="sxs-lookup"><span data-stu-id="ad742-579">In the next tutorial, basic CRUD (create, read, update, delete) operations are examined.</span></span>



## <a name="additional-resources"></a><span data-ttu-id="ad742-580">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="ad742-580">Additional resources</span></span>

* [<span data-ttu-id="ad742-581">Verze YouTube tohoto kurzu</span><span class="sxs-lookup"><span data-stu-id="ad742-581">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [<span data-ttu-id="ad742-582">Další</span><span class="sxs-lookup"><span data-stu-id="ad742-582">Next</span></span>](xref:data/ef-rp/crud)

::: moniker-end
