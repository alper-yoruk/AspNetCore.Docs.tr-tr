---
title: NSwag ve ASP.NET Core ile başlayın
author: zuckerthoben
description: ASP.NET Core web API için belge ve yardım sayfaları oluşturmak için NSwag'ı nasıl kullanacağınızı öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
uid: tutorials/get-started-with-nswag
ms.openlocfilehash: 3eae5d3c66204a10806a8036c8f114af6c501b2c
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666056"
---
# <a name="get-started-with-nswag-and-aspnet-core"></a><span data-ttu-id="fb4b5-103">NSwag ve ASP.NET Core ile başlayın</span><span class="sxs-lookup"><span data-stu-id="fb4b5-103">Get started with NSwag and ASP.NET Core</span></span>

<span data-ttu-id="fb4b5-104">Christoph [Nienaber](https://twitter.com/zuckerthoben)tarafından , [Rico Suter](https://rsuter.com), ve [Dave Brock](https://twitter.com/daveabrock)</span><span class="sxs-lookup"><span data-stu-id="fb4b5-104">By [Christoph Nienaber](https://twitter.com/zuckerthoben), [Rico Suter](https://rsuter.com), and [Dave Brock](https://twitter.com/daveabrock)</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="fb4b5-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fb4b5-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="fb4b5-106">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fb4b5-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker-end

<span data-ttu-id="fb4b5-107">NSwag aşağıdaki yetenekleri sunar:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-107">NSwag offers the following capabilities:</span></span>

* <span data-ttu-id="fb4b5-108">Yeteneği Swagger UI ve Swagger jeneratör kullanmak için.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-108">The ability to utilize the Swagger UI and Swagger generator.</span></span>
* <span data-ttu-id="fb4b5-109">Esnek kod oluşturma yetenekleri.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-109">Flexible code generation capabilities.</span></span>

<span data-ttu-id="fb4b5-110">NSwag ile, Swagger'ı içeren&mdash;ve istemci uygulaması oluşturan üçüncü taraf API'leri kullanabileceğiniz varolan bir API'ye ihtiyacınız yoktur.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-110">With NSwag, you don't need an existing API&mdash;you can use third-party APIs that incorporate Swagger and generate a client implementation.</span></span> <span data-ttu-id="fb4b5-111">NSwag geliştirme döngüsünü hızlandırmak ve kolayca API değişikliklere uyum sağlar.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-111">NSwag allows you to expedite the development cycle and easily adapt to API changes.</span></span>

## <a name="register-the-nswag-middleware"></a><span data-ttu-id="fb4b5-112">NSwag ara yazılımını kaydedin</span><span class="sxs-lookup"><span data-stu-id="fb4b5-112">Register the NSwag middleware</span></span>

<span data-ttu-id="fb4b5-113">NSwag aracını şu şekilde kaydedin:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-113">Register the NSwag middleware to:</span></span>

* <span data-ttu-id="fb4b5-114">Uygulanan web API'si için Swagger belirtimini oluşturun.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-114">Generate the Swagger specification for the implemented web API.</span></span>
* <span data-ttu-id="fb4b5-115">Web API'sine göz atmak ve test etmek için Swagger UI'ye hizmet edin.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-115">Serve the Swagger UI to browse and test the web API.</span></span>

<span data-ttu-id="fb4b5-116">[NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core ara yazılımını kullanmak için [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet paketini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-116">To use the [NSwag](https://github.com/RicoSuter/NSwag) ASP.NET Core middleware, install the [NSwag.AspNetCore](https://www.nuget.org/packages/NSwag.AspNetCore/) NuGet package.</span></span> <span data-ttu-id="fb4b5-117">Bu paket oluşturmak ve Swagger belirtimi, Swagger UI (v2 ve v3) ve [ReDoc UI](https://github.com/Rebilly/ReDoc)hizmet ara içerir.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-117">This package contains the middleware to generate and serve the Swagger specification, Swagger UI (v2 and v3), and [ReDoc UI](https://github.com/Rebilly/ReDoc).</span></span>

<span data-ttu-id="fb4b5-118">NSwag NuGet paketini yüklemek için aşağıdaki yaklaşımlardan birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-118">Use one of the following approaches to install the NSwag NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fb4b5-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb4b5-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="fb4b5-120">Paket **Yöneticisi Konsolu** penceresinden:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-120">From the **Package Manager Console** window:</span></span>
  * <span data-ttu-id="fb4b5-121">**Diğer Windows** > **Paket Yöneticisi Konsolu'na** **Git** > </span><span class="sxs-lookup"><span data-stu-id="fb4b5-121">Go to **View** > **Other Windows** > **Package Manager Console**</span></span>
  * <span data-ttu-id="fb4b5-122">*TodoApi.csproj* dosyasının bulunduğu dizine gidin</span><span class="sxs-lookup"><span data-stu-id="fb4b5-122">Navigate to the directory in which the *TodoApi.csproj* file exists</span></span>
  * <span data-ttu-id="fb4b5-123">Aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-123">Execute the following command:</span></span>

    ```powershell
    Install-Package NSwag.AspNetCore
    ```

* <span data-ttu-id="fb4b5-124">**NuGet Paketlerini Yönet** iletişim kutusundan:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-124">From the **Manage NuGet Packages** dialog:</span></span>
  * <span data-ttu-id="fb4b5-125">**Solution Explorer** > **Manage NuGet Paketleri'nde** projeye sağ tıklayın</span><span class="sxs-lookup"><span data-stu-id="fb4b5-125">Right-click the project in **Solution Explorer** > **Manage NuGet Packages**</span></span>
  * <span data-ttu-id="fb4b5-126">Paket **kaynağını** "nuget.org" olarak ayarlama</span><span class="sxs-lookup"><span data-stu-id="fb4b5-126">Set the **Package source** to "nuget.org"</span></span>
  * <span data-ttu-id="fb4b5-127">Arama kutusuna "NSwag.AspNetCore" girin</span><span class="sxs-lookup"><span data-stu-id="fb4b5-127">Enter "NSwag.AspNetCore" in the search box</span></span>
  * <span data-ttu-id="fb4b5-128">**Gözat** sekmesinden "NSwag.AspNetCore" paketini seçin ve **Yükle'yi** tıklatın</span><span class="sxs-lookup"><span data-stu-id="fb4b5-128">Select the "NSwag.AspNetCore" package from the **Browse** tab and click **Install**</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fb4b5-129">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb4b5-129">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="fb4b5-130">**Çözüm Pad** > Ekle Paketleri'ndeki *Paketler* klasörüne sağ**tıklayın...**</span><span class="sxs-lookup"><span data-stu-id="fb4b5-130">Right-click the *Packages* folder in **Solution Pad** > **Add Packages...**</span></span>
* <span data-ttu-id="fb4b5-131">Paketleri **Ekle** penceresinin **Kaynak** açılır penceresini "nuget.org" olarak ayarlama</span><span class="sxs-lookup"><span data-stu-id="fb4b5-131">Set the **Add Packages** window's **Source** drop-down to "nuget.org"</span></span>
* <span data-ttu-id="fb4b5-132">Arama kutusuna "NSwag.AspNetCore" girin</span><span class="sxs-lookup"><span data-stu-id="fb4b5-132">Enter "NSwag.AspNetCore" in the search box</span></span>
* <span data-ttu-id="fb4b5-133">Sonuç bölmesinden "NSwag.AspNetCore" paketini seçin ve **Paket Ekle'yi** tıklatın</span><span class="sxs-lookup"><span data-stu-id="fb4b5-133">Select the "NSwag.AspNetCore" package from the results pane and click **Add Package**</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="fb4b5-134">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="fb4b5-134">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="fb4b5-135">Şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-135">Run the following command:</span></span>

```dotnetcli
dotnet add TodoApi.csproj package NSwag.AspNetCore
```

---

## <a name="add-and-configure-swagger-middleware"></a><span data-ttu-id="fb4b5-136">Swagger ara yazılım ekleme ve yapılandırma</span><span class="sxs-lookup"><span data-stu-id="fb4b5-136">Add and configure Swagger middleware</span></span>

<span data-ttu-id="fb4b5-137">Aşağıdaki adımları gerçekleştirerek ASP.NET Core uygulamanızda Swagger ekleyin ve yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-137">Add and configure Swagger in your ASP.NET Core app by performing the following steps:</span></span>

* <span data-ttu-id="fb4b5-138">`Startup.ConfigureServices` Yöntemde, gerekli Swagger hizmetlerini kaydedin:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-138">In the `Startup.ConfigureServices` method, register the required Swagger services:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_ConfigureServices&highlight=8)]

* <span data-ttu-id="fb4b5-139">`Startup.Configure` Yöntemde, oluşturulan Swagger belirtimi ve Swagger UI hizmet için ara yazılım etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-139">In the `Startup.Configure` method, enable the middleware for serving the generated Swagger specification and the Swagger UI:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup.cs?name=snippet_Configure&highlight=6-7)]

* <span data-ttu-id="fb4b5-140">Uygulamayı başlatma.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-140">Launch the app.</span></span> <span data-ttu-id="fb4b5-141">Şuna gidin:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-141">Navigate to:</span></span>
  * <span data-ttu-id="fb4b5-142">`http://localhost:<port>/swagger`Swagger UI görüntülemek için.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-142">`http://localhost:<port>/swagger` to view the Swagger UI.</span></span>
  * <span data-ttu-id="fb4b5-143">`http://localhost:<port>/swagger/v1/swagger.json`Swagger belirtimini görüntülemek için.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-143">`http://localhost:<port>/swagger/v1/swagger.json` to view the Swagger specification.</span></span>

## <a name="code-generation"></a><span data-ttu-id="fb4b5-144">Kod oluşturma</span><span class="sxs-lookup"><span data-stu-id="fb4b5-144">Code generation</span></span>

<span data-ttu-id="fb4b5-145">Aşağıdaki seçeneklerden birini seçerek NSwag'ın kod oluşturma özelliklerinden yararlanabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-145">You can take advantage of NSwag's code generation capabilities by choosing one of the following options:</span></span>

* <span data-ttu-id="fb4b5-146">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio) &ndash; C# veya TypeScript API istemci kodu oluşturmak için bir Windows masaüstü uygulaması.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-146">[NSwagStudio](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio) &ndash; a Windows desktop app for generating API client code in C# or TypeScript.</span></span>
* <span data-ttu-id="fb4b5-147">Projeniz içinde kod oluşturma için [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) veya [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet paketleri.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-147">The [NSwag.CodeGeneration.CSharp](https://www.nuget.org/packages/NSwag.CodeGeneration.CSharp/) or [NSwag.CodeGeneration.TypeScript](https://www.nuget.org/packages/NSwag.CodeGeneration.TypeScript/) NuGet packages for code generation inside your project.</span></span>
* <span data-ttu-id="fb4b5-148">[Komuta satırından](https://github.com/RicoSuter/NSwag/wiki/CommandLine)NSwag.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-148">NSwag from the [command line](https://github.com/RicoSuter/NSwag/wiki/CommandLine).</span></span>
* <span data-ttu-id="fb4b5-149">[NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild) NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-149">The [NSwag.MSBuild](https://github.com/RicoSuter/NSwag/wiki/NSwag.MSBuild) NuGet package.</span></span>
* <span data-ttu-id="fb4b5-150">[Unchase OpenAPI (Swagger) Bağlı Hizmet](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice) &ndash; C# veya TypeScript API istemci kodu oluşturmak için bir Visual Studio Connected Service.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-150">The [Unchase OpenAPI (Swagger) Connected Service](https://marketplace.visualstudio.com/items?itemName=Unchase.unchaseopenapiconnectedservice) &ndash; a Visual Studio Connected Service for generating API client code in C# or TypeScript.</span></span> <span data-ttu-id="fb4b5-151">Ayrıca NSwag ile OpenAPI hizmetleri için C# denetleyicileri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-151">Also generates C# controllers for OpenAPI services with NSwag.</span></span>

### <a name="generate-code-with-nswagstudio"></a><span data-ttu-id="fb4b5-152">NSwagStudio ile kod oluşturma</span><span class="sxs-lookup"><span data-stu-id="fb4b5-152">Generate code with NSwagStudio</span></span>

* <span data-ttu-id="fb4b5-153">[NSwagStudio GitHub deposunda talimatları izleyerek NSwagStudio yükleyin.](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio)</span><span class="sxs-lookup"><span data-stu-id="fb4b5-153">Install NSwagStudio by following the instructions at the [NSwagStudio GitHub repository](https://github.com/RicoSuter/NSwag/wiki/NSwagStudio).</span></span> <span data-ttu-id="fb4b5-154">NSwag yayın sayfasında yükleme ve yönetici ayrıcalıkları olmadan başlatılabilir bir xcopy sürümünü indirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-154">On the NSwag release page you can download an xcopy version which can be started without installation and admin privileges.</span></span>
* <span data-ttu-id="fb4b5-155">NSwagStudio'yu başlatın ve **Swagger Specification URL** metin kutusuna *swagger.json* dosya URL'sini girin.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-155">Launch NSwagStudio and enter the *swagger.json* file URL in the **Swagger Specification URL** text box.</span></span> <span data-ttu-id="fb4b5-156">Örneğin, *http://localhost:44354/swagger/v1/swagger.json*.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-156">For example, *http://localhost:44354/swagger/v1/swagger.json*.</span></span>
* <span data-ttu-id="fb4b5-157">Swagger belirtiminizin JSON temsilini oluşturmak için **yerel Kopya oluştur** düğmesini tıklatın.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-157">Click the **Create local Copy** button to generate a JSON representation of your Swagger specification.</span></span>

  ![Swagger belirtiminin yerel kopyasını oluşturma](web-api-help-pages-using-swagger/_static/CreateLocalCopy-NSwagStudio.PNG)

* <span data-ttu-id="fb4b5-159">**Çıktılar** alanında **CSharp İstemci** onay kutusunu tıklatın.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-159">In the **Outputs** area, click the **CSharp Client** check box.</span></span> <span data-ttu-id="fb4b5-160">Projenize bağlı olarak **TypeScript Client** veya **CSharp Web API Denetleyicisi'ni**de seçebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-160">Depending on your project, you can also choose **TypeScript Client** or **CSharp Web API Controller**.</span></span> <span data-ttu-id="fb4b5-161">**CSharp Web API Denetleyicisi'ni**seçerseniz, bir hizmet belirtimi ters nesil olarak hizmet veren hizmeti yeniden belirler.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-161">If you select **CSharp Web API Controller**, a service specification rebuilds the service, serving as a reverse generation.</span></span>
* <span data-ttu-id="fb4b5-162">*TodoApi.NSwag* projesinin tam bir C# istemci seçimi üretmek için **ürünleri Oluştur'** u tıklatın.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-162">Click **Generate Outputs** to produce a complete C# client implementation of the *TodoApi.NSwag* project.</span></span> <span data-ttu-id="fb4b5-163">Oluşturulan istemci kodunu görmek için **CSharp İstemci** sekmesini tıklatın:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-163">To see the generated client code, click the **CSharp Client** tab:</span></span>

```csharp
//----------------------
// <auto-generated>
//     Generated using the NSwag toolchain v12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0)) (http://NSwag.org)
// </auto-generated>
//----------------------

namespace MyNamespace
{
    #pragma warning disable

    [System.CodeDom.Compiler.GeneratedCode("NSwag", "12.0.9.0 (NJsonSchema v9.13.10.0 (Newtonsoft.Json v11.0.0.0))")]
    public partial class TodoClient
    {
        private string _baseUrl = "https://localhost:44354";
        private System.Net.Http.HttpClient _httpClient;
        private System.Lazy<Newtonsoft.Json.JsonSerializerSettings> _settings;

        public TodoClient(System.Net.Http.HttpClient httpClient)
        {
            _httpClient = httpClient;
            _settings = new System.Lazy<Newtonsoft.Json.JsonSerializerSettings>(() =>
            {
                var settings = new Newtonsoft.Json.JsonSerializerSettings();
                UpdateJsonSerializerSettings(settings);
                return settings;
            });
        }

        public string BaseUrl
        {
            get { return _baseUrl; }
            set { _baseUrl = value; }
        }

        // code omitted for brevity
```

> [!TIP]
> <span data-ttu-id="fb4b5-164">C# istemci **kodu, Ayarlar** sekmesindeki seçimlere göre oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-164">The C# client code is generated based on selections in the **Settings** tab. Modify the settings to perform tasks such as default namespace renaming and synchronous method generation.</span></span>

* <span data-ttu-id="fb4b5-165">Oluşturulan C# kodunu istemci projesinde API'yi tüketecek bir dosyaya kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-165">Copy the generated C# code into a file in the client project that will consume the API.</span></span>
* <span data-ttu-id="fb4b5-166">Web API'sini tüketmeye başlayın:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-166">Start consuming the web API:</span></span>

```csharp
 var todoClient = new TodoClient();

// Gets all to-dos from the API
 var allTodos = await todoClient.GetAllAsync();

 // Create a new TodoItem, and save it via the API.
var createdTodo = await todoClient.CreateAsync(new TodoItem());

// Get a single to-do by ID
var foundTodo = await todoClient.GetByIdAsync(1);
```

## <a name="customize-api-documentation"></a><span data-ttu-id="fb4b5-167">API belgelerini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="fb4b5-167">Customize API documentation</span></span>

<span data-ttu-id="fb4b5-168">Swagger, web API tüketimini kolaylaştırmak için nesne modelini belgelemek için seçenekler sunar.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-168">Swagger provides options for documenting the object model to ease consumption of the web API.</span></span>

### <a name="api-info-and-description"></a><span data-ttu-id="fb4b5-169">API bilgileri ve açıklaması</span><span class="sxs-lookup"><span data-stu-id="fb4b5-169">API info and description</span></span>

<span data-ttu-id="fb4b5-170">`Startup.ConfigureServices` Yöntemde, `AddSwaggerDocument` yönteme geçirilen bir yapılandırma eylemi yazar, lisans ve açıklama gibi bilgiler ekler:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-170">In the `Startup.ConfigureServices` method, a configuration action passed to the `AddSwaggerDocument` method adds information such as the author, license, and description:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Startup2.cs?name=snippet_AddSwaggerDocument)]

<span data-ttu-id="fb4b5-171">Swagger Kullanıcı İyiuŞu sürümün bilgilerini görüntüler:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-171">The Swagger UI displays the version's information:</span></span>

![Sürüm bilgileriyle Swagger Kullanıcı Bira](web-api-help-pages-using-swagger/_static/custom-info-nswag.png)

### <a name="xml-comments"></a><span data-ttu-id="fb4b5-173">XML açıklamaları</span><span class="sxs-lookup"><span data-stu-id="fb4b5-173">XML comments</span></span>

<span data-ttu-id="fb4b5-174">XML açıklamalarını etkinleştirmek için aşağıdaki adımları gerçekleştirin:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-174">To enable XML comments, perform the following steps:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="fb4b5-175">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb4b5-175">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="fb4b5-176">**Solution Explorer'da** projeyi sağ tıklatın ve project_name **<>.csproj'u**<edit'i seçin.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-176">Right-click the project in **Solution Explorer** and select **Edit <project_name>.csproj**.</span></span>
* <span data-ttu-id="fb4b5-177">Vurgulanan satırları *.csproj* dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-177">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="fb4b5-178">**Solution Explorer'da** projeye sağ tıklayın ve **Özellikler'i** seçin</span><span class="sxs-lookup"><span data-stu-id="fb4b5-178">Right-click the project in **Solution Explorer** and select **Properties**</span></span>
* <span data-ttu-id="fb4b5-179">**Yapı** sekmesinin **Çıktı** bölümü altındaki **XML belgedosya** kutusunu denetleme</span><span class="sxs-lookup"><span data-stu-id="fb4b5-179">Check the **XML documentation file** box under the **Output** section of the **Build** tab</span></span>

::: moniker-end

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="fb4b5-180">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="fb4b5-180">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

::: moniker range=">= aspnetcore-2.0"

* <span data-ttu-id="fb4b5-181">Çözüm *Defteri'nden* **denetime** basın ve proje adını tıklatın.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-181">From the *Solution Pad*, press **control** and click the project name.</span></span> <span data-ttu-id="fb4b5-182">**Araçlar** > **Dosyayı Edit'e**gidin.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-182">Navigate to **Tools** > **Edit File**.</span></span>
* <span data-ttu-id="fb4b5-183">Vurgulanan satırları *.csproj* dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-183">Manually add the highlighted lines to the *.csproj* file:</span></span>

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

* <span data-ttu-id="fb4b5-184">> **Yapı** **Derleyicisi** > **Proje Seçenekleri** iletişim kutusunu açın</span><span class="sxs-lookup"><span data-stu-id="fb4b5-184">Open the **Project Options** dialog > **Build** > **Compiler**</span></span>
* <span data-ttu-id="fb4b5-185">**Genel Seçenekler** bölümünün altındaki **XML dokümantasyon** kutusunu oluştur'u işaretleyin</span><span class="sxs-lookup"><span data-stu-id="fb4b5-185">Check the **Generate xml documentation** box under the **General Options** section</span></span>

::: moniker-end

# <a name="net-core-cli"></a>[<span data-ttu-id="fb4b5-186">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="fb4b5-186">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="fb4b5-187">Vurgulanan satırları *.csproj* dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-187">Manually add the highlighted lines to the *.csproj* file:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-xml[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/TodoApi.csproj?name=snippet_DocumentationFileElement&highlight=1-2,4)]

::: moniker-end

---

### <a name="data-annotations"></a><span data-ttu-id="fb4b5-188">Veri açıklamaları</span><span class="sxs-lookup"><span data-stu-id="fb4b5-188">Data annotations</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="fb4b5-189">NSwag [Yansıma](/dotnet/csharp/programming-guide/concepts/reflection)kullanır ve web API eylemleri için önerilen dönüş türü [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult)olduğundan, eyleminizi ne yaptığını ve ne döndürür çıkaramaz.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-189">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [IActionResult](xref:Microsoft.AspNetCore.Mvc.IActionResult), it can't infer what your action is doing and what it returns.</span></span>

<span data-ttu-id="fb4b5-190">Aşağıdaki örneği inceleyin:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-190">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="fb4b5-191">Önceki eylem döndürür, `IActionResult`ancak eylem içinde [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) veya [BadRequest](xref:System.Web.Http.ApiController.BadRequest*)döndürür.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-191">The preceding action returns `IActionResult`, but inside the action it's returning either [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*) or [BadRequest](xref:System.Web.Http.ApiController.BadRequest*).</span></span> <span data-ttu-id="fb4b5-192">İstemcilere bu eylemin hangi HTTP durum kodlarını döndüreceğini söylemek için veri ek açıklamalarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-192">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="fb4b5-193">Eylemi aşağıdaki özniteliklerle işaretleyin:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-193">Mark the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.0/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="fb4b5-194">NSwag [Yansıma](/dotnet/csharp/programming-guide/concepts/reflection)kullanır ve web API eylemleri için önerilen dönüş türü [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601)olduğundan, yalnızca tarafından `T`tanımlanan dönüş türü çıkarabilirsiniz .</span><span class="sxs-lookup"><span data-stu-id="fb4b5-194">Because NSwag uses [Reflection](/dotnet/csharp/programming-guide/concepts/reflection), and the recommended return type for web API actions is [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult%601), it can only infer the return type defined by `T`.</span></span> <span data-ttu-id="fb4b5-195">Diğer olası iade türlerini otomatik olarak çıkaramazsınız.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-195">You can't automatically infer other possible return types.</span></span>

<span data-ttu-id="fb4b5-196">Aşağıdaki örneği inceleyin:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-196">Consider the following example:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateAction)]

<span data-ttu-id="fb4b5-197">Önceki eylem döndürür. `ActionResult<T>`</span><span class="sxs-lookup"><span data-stu-id="fb4b5-197">The preceding action returns `ActionResult<T>`.</span></span> <span data-ttu-id="fb4b5-198">Eylem içinde, [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*)döndürediyor.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-198">Inside the action, it's returning [CreatedAtRoute](xref:System.Web.Http.ApiController.CreatedAtRoute*).</span></span> <span data-ttu-id="fb4b5-199">Denetleyici özniteliğe [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) sahip olduğundan, bir [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) yanıtı da mümkündür.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-199">Since the controller has the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute, a [BadRequest](xref:System.Web.Http.ApiController.BadRequest*) response is possible, too.</span></span> <span data-ttu-id="fb4b5-200">Daha fazla bilgi için [Otomatik HTTP 400 yanıtları'na](xref:web-api/index#automatic-http-400-responses)bakın.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-200">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span> <span data-ttu-id="fb4b5-201">İstemcilere bu eylemin hangi HTTP durum kodlarını döndüreceğini söylemek için veri ek açıklamalarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-201">Use data annotations to tell clients which HTTP status codes this action is known to return.</span></span> <span data-ttu-id="fb4b5-202">Eylemi aşağıdaki özniteliklerle işaretleyin:</span><span class="sxs-lookup"><span data-stu-id="fb4b5-202">Mark the action with the following attributes:</span></span>

[!code-csharp[](../tutorials/web-api-help-pages-using-swagger/samples/2.1/TodoApi.NSwag/Controllers/TodoController.cs?name=snippet_CreateActionAttributes)]

<span data-ttu-id="fb4b5-203">Core 2.2 veya daha sonra ASP.NET, tek eylemleri açıkça .' `[ProducesResponseType]`ile süslemek yerine konvansiyonları kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-203">In ASP.NET Core 2.2 or later, you can use conventions instead of explicitly decorating individual actions with `[ProducesResponseType]`.</span></span> <span data-ttu-id="fb4b5-204">Daha fazla bilgi için bkz. <xref:web-api/advanced/conventions>.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-204">For more information, see <xref:web-api/advanced/conventions>.</span></span>

::: moniker-end

<span data-ttu-id="fb4b5-205">Swagger jeneratörü artık bu eylemi doğru bir şekilde açıklayabilir ve oluşturulan istemciler bitiş noktasını ararken ne aldıklarını bilirler.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-205">The Swagger generator can now accurately describe this action, and generated clients know what they receive when calling the endpoint.</span></span> <span data-ttu-id="fb4b5-206">Bir öneri olarak, tüm eylemleri bu özniteliklerle işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-206">As a recommendation, mark all actions with these attributes.</span></span>

<span data-ttu-id="fb4b5-207">API eylemlerinizin ne kadar HTTP yanıtları döndürmesi gerektiğine ilişkin yönergeler için [RFC 7231 belirtimine](https://tools.ietf.org/html/rfc7231#section-4.3)bakın.</span><span class="sxs-lookup"><span data-stu-id="fb4b5-207">For guidelines on what HTTP responses your API actions should return, see the [RFC 7231 specification](https://tools.ietf.org/html/rfc7231#section-4.3).</span></span>
