<span data-ttu-id="df164-101">Identity scaffolder öğesini çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="df164-101">Run the Identity scaffolder:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="df164-102">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="df164-102">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="df164-103">**Çözüm Gezgini**, projeye sağ tıklayıp **Add**  >  **Yeni iskli öğe**Ekle >.</span><span class="sxs-lookup"><span data-stu-id="df164-103">From **Solution Explorer**, right-click on the project > **Add** > **New Scaffolded Item**.</span></span>
* <span data-ttu-id="df164-104">**Yeni yapı iskelesi öğesi Ekle** iletişim kutusunun sol bölmesinde, **kimlik**  >  **Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="df164-104">From the left pane of the **Add New Scaffolded Item** dialog, select **Identity** > **Add**.</span></span>
* <span data-ttu-id="df164-105">**Kimlik Ekle** iletişim kutusunda istediğiniz seçenekleri belirleyin.</span><span class="sxs-lookup"><span data-stu-id="df164-105">In the **Add Identity** dialog, select the options you want.</span></span>
  * <span data-ttu-id="df164-106">Var olan düzen sayfanızı seçin veya Düzen dosyanızın üzerine yanlış biçimlendirme uygulanır:</span><span class="sxs-lookup"><span data-stu-id="df164-106">Select your existing layout page, or your layout file will be overwritten with incorrect markup:</span></span>
    * <span data-ttu-id="df164-107">`~/Pages/Shared/_Layout.cshtml`Razor Pages için</span><span class="sxs-lookup"><span data-stu-id="df164-107">`~/Pages/Shared/_Layout.cshtml` for Razor Pages</span></span>
    * <span data-ttu-id="df164-108">`~/Views/Shared/_Layout.cshtml`MVC projeleri için</span><span class="sxs-lookup"><span data-stu-id="df164-108">`~/Views/Shared/_Layout.cshtml` for MVC projects</span></span>
    * <span data-ttu-id="df164-109">Blazor Server şablonundan oluşturulan Blazor Server uygulamaları, `blazorserver` Varsayılan olarak Razor Pages veya MVC için yapılandırılmamıştır.</span><span class="sxs-lookup"><span data-stu-id="df164-109">Blazor Server apps created from the Blazor Server template (`blazorserver`) aren't configured for Razor Pages or MVC by default.</span></span> <span data-ttu-id="df164-110">Düzen sayfası girişini boş bırakın.</span><span class="sxs-lookup"><span data-stu-id="df164-110">Leave the layout page entry blank.</span></span>
  * <span data-ttu-id="df164-111">**+** Yeni bir **veri bağlamı sınıfı**oluşturmak için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="df164-111">Select the **+** button to create a new **Data context class**.</span></span> <span data-ttu-id="df164-112">Varsayılan değeri kabul edin veya bir sınıf belirtin (örneğin, `MyApplication.Data.ApplicationDbContext` ).</span><span class="sxs-lookup"><span data-stu-id="df164-112">Accept the default value or specify a class (for example, `MyApplication.Data.ApplicationDbContext`).</span></span>
* <span data-ttu-id="df164-113">**Add (Ekle)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="df164-113">Select **Add**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="df164-114">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="df164-114">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="df164-115">ASP.NET Core scaffolder ' ı daha önce yüklemediyseniz, şimdi yükleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="df164-115">If you have not previously installed the ASP.NET Core scaffolder, install it now:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="df164-116">Proje dosyasına (*. csproj*) gerekli NuGet paket başvurularını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="df164-116">Add required NuGet package references to the project file (*.csproj*).</span></span> <span data-ttu-id="df164-117">Proje dizininde aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="df164-117">Run the following commands in the project directory:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

<span data-ttu-id="df164-118">Identity desteği seçeneklerini listelemek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="df164-118">Run the following command to list the Identity scaffolder options:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

[!INCLUDE[](~/includes/scaffoldTFM.md)]

<span data-ttu-id="df164-119">Proje klasöründe, kimlik desteği ' ı istediğiniz seçeneklerle çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="df164-119">In the project folder, run the Identity scaffolder with the options you want.</span></span> <span data-ttu-id="df164-120">Örneğin, kimliği varsayılan UI ve minimum dosya sayısı ile ayarlamak için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="df164-120">For example, to setup identity with the default UI and the minimum number of files, run the following command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator identity --useDefaultUI
```

---
