# <a name="visual-studio"></a>[<span data-ttu-id="6f9ab-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6f9ab-101">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="6f9ab-102">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="6f9ab-102">Create a new project.</span></span>
1. <span data-ttu-id="6f9ab-103">**Çalışan hizmeti** seçin.</span><span class="sxs-lookup"><span data-stu-id="6f9ab-103">Select **Worker Service**.</span></span> <span data-ttu-id="6f9ab-104">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6f9ab-104">Select **Next**.</span></span>
1. <span data-ttu-id="6f9ab-105">**Proje adı** alanında bir proje adı girin veya varsayılan proje adını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="6f9ab-105">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="6f9ab-106">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="6f9ab-106">Select **Create**.</span></span>
1. <span data-ttu-id="6f9ab-107">**Yeni çalışan hizmeti oluştur** Iletişim kutusunda **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="6f9ab-107">In the **Create a new Worker service** dialog, select **Create**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="6f9ab-108">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6f9ab-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="6f9ab-109">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="6f9ab-109">Create a new project.</span></span>
1. <span data-ttu-id="6f9ab-110">Kenar çubuğunda **.NET Core** altında **uygulama** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="6f9ab-110">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="6f9ab-111">**ASP.NET Core** altında **çalışan** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="6f9ab-111">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="6f9ab-112">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6f9ab-112">Select **Next**.</span></span>
1. <span data-ttu-id="6f9ab-113">**Hedef çerçeve** Için **.NET Core 3,0** veya üzerini seçin.</span><span class="sxs-lookup"><span data-stu-id="6f9ab-113">Select **.NET Core 3.0** or later for the **Target Framework**.</span></span> <span data-ttu-id="6f9ab-114">**İleri**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="6f9ab-114">Select **Next**.</span></span>
1. <span data-ttu-id="6f9ab-115">**Proje adı** alanına bir ad girin.</span><span class="sxs-lookup"><span data-stu-id="6f9ab-115">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="6f9ab-116">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="6f9ab-116">Select **Create**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6f9ab-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6f9ab-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="6f9ab-118">Çalışan hizmeti ( `worker` ) şablonunu bir komut kabuğundan [DotNet New](/dotnet/core/tools/dotnet-new) komutuyla birlikte kullanın.</span><span class="sxs-lookup"><span data-stu-id="6f9ab-118">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="6f9ab-119">Aşağıdaki örnekte, adlı bir çalışan hizmeti uygulaması oluşturulur `ContosoWorker` .</span><span class="sxs-lookup"><span data-stu-id="6f9ab-119">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="6f9ab-120">`ContosoWorker`Komut yürütüldüğünde, uygulamanın bir klasörü otomatik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="6f9ab-120">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
