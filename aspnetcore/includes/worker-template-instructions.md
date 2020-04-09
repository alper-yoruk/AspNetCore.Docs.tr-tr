# <a name="visual-studio"></a>[<span data-ttu-id="0d8b6-101">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d8b6-101">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="0d8b6-102">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="0d8b6-102">Create a new project.</span></span>
1. <span data-ttu-id="0d8b6-103">**İşçi Hizmeti'ni**seçin.</span><span class="sxs-lookup"><span data-stu-id="0d8b6-103">Select **Worker Service**.</span></span> <span data-ttu-id="0d8b6-104">**Sonraki'ni**seçin.</span><span class="sxs-lookup"><span data-stu-id="0d8b6-104">Select **Next**.</span></span>
1. <span data-ttu-id="0d8b6-105">**Proje adı** alanında bir proje adı sağlayın veya varsayılan proje adını kabul edin.</span><span class="sxs-lookup"><span data-stu-id="0d8b6-105">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="0d8b6-106">**Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="0d8b6-106">Select **Create**.</span></span>
1. <span data-ttu-id="0d8b6-107">Yeni **Bir İşçi Hizmeti Oluştur** iletişim kutusunda **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="0d8b6-107">In the **Create a new Worker service** dialog, select **Create**.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0d8b6-108">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0d8b6-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="0d8b6-109">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="0d8b6-109">Create a new project.</span></span>
1. <span data-ttu-id="0d8b6-110">Kenar çubuğunda **.NET Core** altında **Uygulama'yı** seçin.</span><span class="sxs-lookup"><span data-stu-id="0d8b6-110">Select **App** under **.NET Core** in the sidebar.</span></span>
1. <span data-ttu-id="0d8b6-111">**ASP.NET Çekirdek**altında **İşçi'yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="0d8b6-111">Select **Worker** under **ASP.NET Core**.</span></span> <span data-ttu-id="0d8b6-112">**Sonraki'ni**seçin.</span><span class="sxs-lookup"><span data-stu-id="0d8b6-112">Select **Next**.</span></span>
1. <span data-ttu-id="0d8b6-113">**Hedef Çerçeve**için **.NET Core 3.0** veya sonraki leri seçin.</span><span class="sxs-lookup"><span data-stu-id="0d8b6-113">Select **.NET Core 3.0** or later for the **Target Framework**.</span></span> <span data-ttu-id="0d8b6-114">**Sonraki'ni**seçin.</span><span class="sxs-lookup"><span data-stu-id="0d8b6-114">Select **Next**.</span></span>
1. <span data-ttu-id="0d8b6-115">**Proje Adı** alanında bir ad sağlayın.</span><span class="sxs-lookup"><span data-stu-id="0d8b6-115">Provide a name in the **Project Name** field.</span></span> <span data-ttu-id="0d8b6-116">**Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="0d8b6-116">Select **Create**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0d8b6-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0d8b6-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0d8b6-118">Bir komut kabuğundan`worker` [dotnet yeni](/dotnet/core/tools/dotnet-new) komutu ile İşçi Hizmeti ( ) şablonunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="0d8b6-118">Use the Worker Service (`worker`) template with the [dotnet new](/dotnet/core/tools/dotnet-new) command from a command shell.</span></span> <span data-ttu-id="0d8b6-119">Aşağıdaki örnekte, Bir İşçi Hizmeti `ContosoWorker`uygulaması adlı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="0d8b6-119">In the following example, a Worker Service app is created named `ContosoWorker`.</span></span> <span data-ttu-id="0d8b6-120">Komut yürütüldüğünde `ContosoWorker` uygulama için bir klasör otomatik olarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="0d8b6-120">A folder for the `ContosoWorker` app is created automatically when the command is executed.</span></span>

```dotnetcli
dotnet new worker -o ContosoWorker
```

---
