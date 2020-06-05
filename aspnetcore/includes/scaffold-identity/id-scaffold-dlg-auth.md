::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5006d-101">Identity scaffolder öğesini çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5006d-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5006d-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5006d-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5006d-103">**Çözüm Gezgini**, projeye sağ tıklayıp **Add** > **Yeni iskli öğe**Ekle >.</span><span class="sxs-lookup"><span data-stu-id="5006d-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="5006d-104">**Yapı iskelesi Ekle** iletişim kutusunun sol bölmesinde, **kimlik** > **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="5006d-104">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="5006d-105">**Kimlik Ekle** iletişim kutusunda istediğiniz seçenekleri belirleyin.</span><span class="sxs-lookup"><span data-stu-id="5006d-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="5006d-106">Düzen dosyanızın üzerine yazılmaması için, var olan düzen sayfanızı seçin.</span><span class="sxs-lookup"><span data-stu-id="5006d-106">Select your existing layout page so your layout file isn't overwritten with incorrect markup.</span></span> <span data-ttu-id="5006d-107">Var olan bir \* \_ Layout. cshtml\* dosyası **seçildiğinde, üzerine yazılmaz.**</span><span class="sxs-lookup"><span data-stu-id="5006d-107">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="5006d-108">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="5006d-108">For example:</span></span>
    * <span data-ttu-id="5006d-109">`~/Pages/Shared/_Layout.cshtml`Mevcut Razor Pages altyapısına sahip Razor Pages veya Blazor Server projeleri için</span><span class="sxs-lookup"><span data-stu-id="5006d-109">`~/Pages/Shared/_Layout.cshtml` for Razor Pages or Blazor Server projects with existing Razor Pages infrastructure</span></span>
    * <span data-ttu-id="5006d-110">`~/Views/Shared/_Layout.cshtml`Mevcut MVC altyapısına sahip MVC projeleri veya Blazor Server projeleri için</span><span class="sxs-lookup"><span data-stu-id="5006d-110">`~/Views/Shared/_Layout.cshtml` for MVC projects or Blazor Server projects with existing MVC infrastructure</span></span>
* <span data-ttu-id="5006d-111">Mevcut veri bağlamınızı kullanmak için, geçersiz kılmak üzere en az bir dosya seçin.</span><span class="sxs-lookup"><span data-stu-id="5006d-111">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="5006d-112">Veri bağlamınızı eklemek için en az bir dosya seçmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="5006d-112">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="5006d-113">Veri bağlamı sınıfınızı seçin.</span><span class="sxs-lookup"><span data-stu-id="5006d-113">Select your data context class.</span></span>
  * <span data-ttu-id="5006d-114">**Add (Ekle)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="5006d-114">Select **Add**.</span></span>
* <span data-ttu-id="5006d-115">Yeni bir kullanıcı bağlamı oluşturmak ve muhtemelen kimlik için özel bir Kullanıcı sınıfı oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="5006d-115">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="5006d-116">**+** Yeni bir **veri bağlamı sınıfı**oluşturmak için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="5006d-116">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="5006d-117">Varsayılan değeri kabul edin veya bir sınıf belirtin (örneğin, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="5006d-117">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="5006d-118">**Add (Ekle)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="5006d-118">Select **Add**.</span></span>

<span data-ttu-id="5006d-119">Note: yeni bir kullanıcı bağlamı oluşturuyorsanız, geçersiz kılmak için bir dosya seçmeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="5006d-119">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5006d-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5006d-120">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5006d-121">ASP.NET Core scaffolder ' ı daha önce yüklemediyseniz, şimdi yükleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="5006d-121">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="5006d-122">Proje dosyasına (*. csproj*) gerekli NuGet paket başvurularını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5006d-122">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="5006d-123">Proje dizininde aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5006d-123">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="5006d-124">Identity desteği seçeneklerini listelemek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5006d-124">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="5006d-125">Proje klasöründe, kimlik desteği ' ı istediğiniz seçeneklerle çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5006d-125">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="5006d-126">Örneğin, kimliği varsayılan UI ve minimum dosya sayısı ile ayarlamak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5006d-126">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="5006d-127">DB içeriğiniz için doğru tam adı kullanın:</span><span class="sxs-lookup"><span data-stu-id="5006d-127">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="5006d-128">PowerShell, bir komut ayırıcısı olarak noktalı virgül kullanır.</span><span class="sxs-lookup"><span data-stu-id="5006d-128">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="5006d-129">PowerShell kullanırken, dosya listesinde noktalı virgül karakterini kaçış veya dosya listesini çift tırnak içine koyun.</span><span class="sxs-lookup"><span data-stu-id="5006d-129">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="5006d-130">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="5006d-130">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="5006d-131">Identity desteği bayrağını veya bayrağını belirtmeden çalıştırırsanız `--files` `--useDefaultUI` , tüm kullanılabilir kimlik Kullanıcı arabirimi sayfaları projenizde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5006d-131">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5006d-132">Identity scaffolder öğesini çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5006d-132">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5006d-133">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5006d-133">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5006d-134">**Çözüm Gezgini**, projeye sağ tıklayıp **Add** > **Yeni iskli öğe**Ekle >.</span><span class="sxs-lookup"><span data-stu-id="5006d-134">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="5006d-135">**Yapı iskelesi Ekle** iletişim kutusunun sol bölmesinde, **kimlik** > **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="5006d-135">From the left pane of the **Add Scaffold** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="5006d-136">**Kimlik Ekle** iletişim kutusunda istediğiniz seçenekleri belirleyin.</span><span class="sxs-lookup"><span data-stu-id="5006d-136">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="5006d-137">Var olan düzen sayfanızı seçin veya Düzen dosyanızın üzerine yanlış biçimlendirme uygulanır.</span><span class="sxs-lookup"><span data-stu-id="5006d-137">Select your existing layout page, or your layout file will be overwritten with incorrect markup.</span></span> <span data-ttu-id="5006d-138">Var olan bir \* \_ Layout. cshtml\* dosyası **seçildiğinde, üzerine yazılmaz.**</span><span class="sxs-lookup"><span data-stu-id="5006d-138">When an existing *\_Layout.cshtml* file is selected, it is **not** overwritten.</span></span> <span data-ttu-id="5006d-139">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="5006d-139">For example:</span></span>
    * <span data-ttu-id="5006d-140">`~/Pages/Shared/_Layout.cshtml`Razor Pages için</span><span class="sxs-lookup"><span data-stu-id="5006d-140">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="5006d-141">`~/Views/Shared/_Layout.cshtml`MVC projeleri için</span><span class="sxs-lookup"><span data-stu-id="5006d-141">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
* <span data-ttu-id="5006d-142">Mevcut veri bağlamınızı kullanmak için, geçersiz kılmak üzere en az bir dosya seçin.</span><span class="sxs-lookup"><span data-stu-id="5006d-142">To use your existing data context, select at least one file to override.</span></span> <span data-ttu-id="5006d-143">Veri bağlamınızı eklemek için en az bir dosya seçmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="5006d-143">You must select at least one file to add your data context.</span></span>
  * <span data-ttu-id="5006d-144">Veri bağlamı sınıfınızı seçin.</span><span class="sxs-lookup"><span data-stu-id="5006d-144">Select your data context class.</span></span>
  * <span data-ttu-id="5006d-145">**Add (Ekle)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="5006d-145">Select **Add**.</span></span>
* <span data-ttu-id="5006d-146">Yeni bir kullanıcı bağlamı oluşturmak ve muhtemelen kimlik için özel bir Kullanıcı sınıfı oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="5006d-146">To create a new user context and possibly create a custom user class for Identity:</span></span>
  * <span data-ttu-id="5006d-147">**+** Yeni bir **veri bağlamı sınıfı**oluşturmak için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="5006d-147">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="5006d-148">Varsayılan değeri kabul edin veya bir sınıf belirtin (örneğin, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="5006d-148">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
  * <span data-ttu-id="5006d-149">**Add (Ekle)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="5006d-149">Select **Add**.</span></span>

<span data-ttu-id="5006d-150">Note: yeni bir kullanıcı bağlamı oluşturuyorsanız, geçersiz kılmak için bir dosya seçmeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="5006d-150">Note: If you're creating a new user context, you don't have to select a file to override.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5006d-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5006d-151">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5006d-152">ASP.NET Core scaffolder ' ı daha önce yüklemediyseniz, şimdi yükleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="5006d-152">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="5006d-153">Proje dosyasına (*. csproj*) [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) öğesine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5006d-153">Add a package reference to [Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) to the project file (*.csproj*).</span></span> <span data-ttu-id="5006d-154">Proje dizininde aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5006d-154">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

<span data-ttu-id="5006d-155">Identity desteği seçeneklerini listelemek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5006d-155">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

<span data-ttu-id="5006d-156">Proje klasöründe, kimlik desteği ' ı istediğiniz seçeneklerle çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5006d-156">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="5006d-157">Örneğin, kimliği varsayılan UI ve minimum dosya sayısı ile ayarlamak için aşağıdaki komutu çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5006d-157">For example, to setup identity with the default UI and the minimum number of files, run the following command.</span></span> <span data-ttu-id="5006d-158">DB içeriğiniz için doğru tam adı kullanın:</span><span class="sxs-lookup"><span data-stu-id="5006d-158">Use the correct fully qualified name for your DB context:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login"
```

<span data-ttu-id="5006d-159">PowerShell, bir komut ayırıcısı olarak noktalı virgül kullanır.</span><span class="sxs-lookup"><span data-stu-id="5006d-159">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="5006d-160">PowerShell kullanırken, dosya listesinde noktalı virgül karakterini kaçış veya dosya listesini çift tırnak içine koyun.</span><span class="sxs-lookup"><span data-stu-id="5006d-160">When using PowerShell, escape the semi-colons in the file list or put the file list in double quotes.</span></span> <span data-ttu-id="5006d-161">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="5006d-161">For example:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -dc MyApplication.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="5006d-162">Identity desteği bayrağını veya bayrağını belirtmeden çalıştırırsanız `--files` `--useDefaultUI` , tüm kullanılabilir kimlik Kullanıcı arabirimi sayfaları projenizde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="5006d-162">If you run the Identity scaffolder without specifying the `--files` flag or the `--useDefaultUI` flag, all the available Identity UI pages will be created in your project.</span></span>

---

::: moniker-end
