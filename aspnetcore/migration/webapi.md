---
<span data-ttu-id="0e4a9-101">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="0e4a9-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="0e4a9-102">'Blazor'</span></span>
- <span data-ttu-id="0e4a9-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="0e4a9-103">'Identity'</span></span>
- <span data-ttu-id="0e4a9-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="0e4a9-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="0e4a9-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="0e4a9-105">'Razor'</span></span>
- <span data-ttu-id="0e4a9-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-106">'SignalR' uid:</span></span> 

---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="0e4a9-107">ASP.NET Web API 'sinden ASP.NET Core 'e geçiş</span><span class="sxs-lookup"><span data-stu-id="0e4a9-107">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="0e4a9-108">[Scott Ade](https://twitter.com/scott_addie) ve [Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="0e4a9-108">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="0e4a9-109">ASP.NET 4. x Web API 'SI, tarayıcılar ve mobil cihazlar dahil olmak üzere çok çeşitli istemcilere ulaşan bir HTTP hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-109">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="0e4a9-110">ASP.NET Core ASP.NET 4. x ' in MVC ve Web API 'SI uygulama modellerini ASP.NET Core MVC olarak bilinen tek bir programlama modelinde birleştirir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-110">ASP.NET Core combines ASP.NET 4.x's MVC and Web API app models into a single programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="0e4a9-111">Bu makalede, ASP.NET 4. x Web API 'sinden ASP.NET Core MVC 'ye geçiş yapmak için gereken adımlar gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-111">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="0e4a9-112">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0e4a9-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="0e4a9-113">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="0e4a9-113">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="0e4a9-114">ASP.NET 4. x Web API projesini inceleyin</span><span class="sxs-lookup"><span data-stu-id="0e4a9-114">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="0e4a9-115">Bu makale, [ASP.NET Web API 2 Ile çalışmaya](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)başlama bölümünde oluşturulan *productsapp* projesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-115">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="0e4a9-116">Bu projede, temel bir ASP.NET 4. x Web API projesi aşağıdaki şekilde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-116">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="0e4a9-117">*Global.asax.cs*' de, şu şekilde bir çağrı yapılır `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="0e4a9-117">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="0e4a9-118">`WebApiConfig`Sınıfı *App_Start* klasöründe bulunur ve statik bir `Register` yönteme sahiptir:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-118">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="0e4a9-119">Önceki sınıf:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-119">The preceding class:</span></span>

* <span data-ttu-id="0e4a9-120">, Gerçekten kullanılmasa da, [öznitelik yönlendirmeyi](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-120">Configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used.</span></span>
* <span data-ttu-id="0e4a9-121">Yönlendirme tablosunu yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-121">Configures the routing table.</span></span>
<span data-ttu-id="0e4a9-122">Örnek kod, isteğe bağlı olarak, URL 'Lerin biçimle eşleşmesini bekler `/api/{controller}/{id}` `{id}` .</span><span class="sxs-lookup"><span data-stu-id="0e4a9-122">The sample code expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="0e4a9-123">Aşağıdaki bölümlerde, Web API projesinin ASP.NET Core MVC 'ye geçişi gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-123">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="0e4a9-124">Hedef projeyi oluşturma</span><span class="sxs-lookup"><span data-stu-id="0e4a9-124">Create the destination project</span></span>

<span data-ttu-id="0e4a9-125">Visual Studio 'da yeni bir boş çözüm oluşturun ve geçirilecek ASP.NET 4. x Web API projesini ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-125">Create a new blank solution in Visual Studio and add the ASP.NET 4.x Web API project to migrate:</span></span>

1. <span data-ttu-id="0e4a9-126">**Dosya** menüsünden **Yeni** > **Proje**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-126">From the **File** menu, select **New** > **Project**.</span></span>
1. <span data-ttu-id="0e4a9-127">**Boş çözüm** şablonunu seçin ve Ileri ' **yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-127">Select the **Blank Solution** template and select **Next**.</span></span>
1. <span data-ttu-id="0e4a9-128">Çözümü *WebAPIMigration*olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-128">Name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="0e4a9-129">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-129">Select **Create**.</span></span>
1. <span data-ttu-id="0e4a9-130">Varolan *Productsapp* projesini çözüme ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-130">Add the existing *ProductsApp* project to the solution.</span></span>

<span data-ttu-id="0e4a9-131">Geçirilecek yeni bir API projesi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-131">Add a new API project to migrate to:</span></span>

1. <span data-ttu-id="0e4a9-132">Çözüme yeni bir **ASP.NET Core Web uygulaması** projesi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-132">Add a new **ASP.NET Core Web Application** project to the solution.</span></span>
1. <span data-ttu-id="0e4a9-133">**Yeni projenizi yapılandırın** iletişim kutusunda, proje *Productscore*' ı adlandırın ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-133">In the **Configure your new project** dialog, Name the project *ProductsCore*, and select **Create**.</span></span>
1. <span data-ttu-id="0e4a9-134">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 3,1** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-134">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="0e4a9-135">**API** proje şablonunu seçin ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-135">Select the **API** project template, and select **Create**.</span></span>
1. <span data-ttu-id="0e4a9-136">*WeatherForecast.cs* ve *Controllers/dalgalı sıra. cs* örnek dosyalarını yeni *productscore* projesinden kaldırın.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-136">Remove the *WeatherForecast.cs* and *Controllers/WeatherForecastController.cs* example files from the new *ProductsCore* project.</span></span>

<span data-ttu-id="0e4a9-137">Çözüm artık iki proje içerir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-137">The solution now contains two projects.</span></span> <span data-ttu-id="0e4a9-138">Aşağıdaki bölümlerde *Productsapp* projesinin Içeriğinin *productscore* projesine geçirilmesi açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-138">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="0e4a9-139">Yapılandırmayı geçir</span><span class="sxs-lookup"><span data-stu-id="0e4a9-139">Migrate configuration</span></span>

<span data-ttu-id="0e4a9-140">ASP.NET Core *App_Start* klasörünü veya *Global. asax* dosyasını kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-140">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file.</span></span> <span data-ttu-id="0e4a9-141">Ayrıca, *Web. config* dosyası yayımlama zamanında eklenir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-141">Additionally, the *web.config* file is added at publish time.</span></span>

<span data-ttu-id="0e4a9-142">`Startup` sınıfı:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-142">The `Startup` class:</span></span>

* <span data-ttu-id="0e4a9-143">*Global. asax*öğesini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-143">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="0e4a9-144">Tüm uygulama başlangıç görevlerini işler.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-144">Handles all app startup tasks.</span></span>

<span data-ttu-id="0e4a9-145">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-145">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="0e4a9-146">Modelleri ve denetleyicileri geçirme</span><span class="sxs-lookup"><span data-stu-id="0e4a9-146">Migrate models and controllers</span></span>

<span data-ttu-id="0e4a9-147">Aşağıdaki kod, `ProductsController` ASP.NET Core için güncelleştirileceğini gösterir:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-147">The following code shows the `ProductsController` to be updated for ASP.NET Core:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

<span data-ttu-id="0e4a9-148">`ProductsController`ASP.NET Core için Güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-148">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="0e4a9-149">*Denetleyiciyi/ProductsController. cs* ve *modeller* klasörünü özgün projeden yeni bir klasöre kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-149">Copy *Controllers/ProductsController.cs* and the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="0e4a9-150">Kopyalanan dosyaların kök ad alanını olarak değiştirin `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="0e4a9-150">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="0e4a9-151">`using ProductsApp.Models;`İfadesini olarak güncelleştirin `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="0e4a9-151">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="0e4a9-152">Aşağıdaki bileşenler ASP.NET Core yok:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-152">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="0e4a9-153">`ApiController` sınıfı</span><span class="sxs-lookup"><span data-stu-id="0e4a9-153">`ApiController` class</span></span>
* <span data-ttu-id="0e4a9-154">`System.Web.Http`uzayına</span><span class="sxs-lookup"><span data-stu-id="0e4a9-154">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="0e4a9-155">`IHttpActionResult`arayüz</span><span class="sxs-lookup"><span data-stu-id="0e4a9-155">`IHttpActionResult` interface</span></span>

<span data-ttu-id="0e4a9-156">Aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-156">Make the following changes:</span></span>

1. <span data-ttu-id="0e4a9-157">`ApiController`Olarak değiştirin <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="0e4a9-157">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="0e4a9-158">`using Microsoft.AspNetCore.Mvc;`Başvuruyu çözümlemek için ekleyin `ControllerBase` .</span><span class="sxs-lookup"><span data-stu-id="0e4a9-158">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="0e4a9-159">`using System.Web.Http;` klasörünü silin.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-159">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="0e4a9-160">`GetProduct`Eylemin dönüş türünü `IHttpActionResult` olarak değiştirin `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="0e4a9-160">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="0e4a9-161">`GetProduct`Eylemin `return` ifadesini aşağıdaki şekilde kolaylaştırın:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-161">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="0e4a9-162">Yönlendirmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="0e4a9-162">Configure routing</span></span>

<span data-ttu-id="0e4a9-163">ASP.NET Core *API* proje şablonu, üretilen koddaki Endpoint Routing yapılandırmasını içerir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-163">The ASP.NET Core *API* project template includes endpoint routing configuration in the generated code.</span></span>

<span data-ttu-id="0e4a9-164">Aşağıdakiler <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> ve <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> çağırır:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-164">The following <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> calls:</span></span>

* <span data-ttu-id="0e4a9-165">Yönlendirme eşleştirme ve uç nokta yürütmeyi [Ara yazılım](xref:fundamentals/middleware/index) ardışık düzeninde kaydedin.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-165">Register route matching and endpoint execution in the [middleware](xref:fundamentals/middleware/index) pipeline.</span></span>
* <span data-ttu-id="0e4a9-166">*Productsapp* projesinin *App_Start/webapiconfig.cs* dosyasını değiştirin.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-166">Replace the *ProductsApp* project's *App_Start/WebApiConfig.cs* file.</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

<span data-ttu-id="0e4a9-167">Yönlendirmeyi aşağıdaki şekilde yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-167">Configure routing as follows:</span></span>

1. <span data-ttu-id="0e4a9-168">`ProductsController`Sınıfı aşağıdaki özniteliklere işaretle:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-168">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="0e4a9-169">Önceki [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) öznitelik, denetleyicinin öznitelik yönlendirme modelini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-169">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="0e4a9-170">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Öznitelik, bu denetleyicideki tüm eylemler için öznitelik yönlendirme bir gereksinim yapar.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-170">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="0e4a9-171">Öznitelik yönlendirme, ve gibi belirteçleri destekler `[controller]` `[action]` .</span><span class="sxs-lookup"><span data-stu-id="0e4a9-171">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="0e4a9-172">Çalışma zamanında, her belirteç, sırasıyla özniteliğin uygulandığı denetleyicinin veya eylemin adı ile değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-172">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="0e4a9-173">Belirteçler:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-173">The tokens:</span></span>
    * <span data-ttu-id="0e4a9-174">Projedeki sihirli dize sayısını azaltın.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-174">Reduce the number of magic strings in the project.</span></span>
    * <span data-ttu-id="0e4a9-175">Otomatik yeniden adlandırma yeniden düzenlemeler uygulandığında, yolların karşılık gelen denetleyiciler ve eylemlerle eşitlenmiş durumda kalmasını sağlayın.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-175">Ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="0e4a9-176">Eylemlere HTTP Get isteklerini etkinleştirin `ProductController` :</span><span class="sxs-lookup"><span data-stu-id="0e4a9-176">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="0e4a9-177">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Özniteliği `GetAllProducts` eyleme uygulayın.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-177">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="0e4a9-178">`[HttpGet("{id}")]`Özniteliği `GetProduct` eyleme uygulayın.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-178">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="0e4a9-179">Geçirilen projeyi çalıştırın ve konumuna gidin `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="0e4a9-179">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="0e4a9-180">Üç ürünün tam bir listesi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-180">A full list of three products appears.</span></span> <span data-ttu-id="0e4a9-181">`/api/products/1` adresine gidin.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-181">Browse to `/api/products/1`.</span></span> <span data-ttu-id="0e4a9-182">İlk ürün görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-182">The first product appears.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0e4a9-183">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0e4a9-183">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a><span data-ttu-id="0e4a9-184">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="0e4a9-184">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="0e4a9-185">ASP.NET 4. x Web API projesini inceleyin</span><span class="sxs-lookup"><span data-stu-id="0e4a9-185">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="0e4a9-186">Bu makale, [ASP.NET Web API 2 Ile çalışmaya](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)başlama bölümünde oluşturulan *productsapp* projesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-186">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="0e4a9-187">Bu projede, temel bir ASP.NET 4. x Web API projesi aşağıdaki şekilde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-187">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="0e4a9-188">*Global.asax.cs*' de, şu şekilde bir çağrı yapılır `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="0e4a9-188">In *Global.asax.cs*, a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="0e4a9-189">`WebApiConfig`Sınıfı *App_Start* klasöründe bulunur ve statik bir `Register` yönteme sahiptir:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-189">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="0e4a9-190">Bu sınıf, proje içinde gerçekten kullanılmasa da [öznitelik yönlendirmeyi](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-190">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="0e4a9-191">Ayrıca, ASP.NET Web API 'SI tarafından kullanılan yönlendirme tablosunu da yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-191">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="0e4a9-192">Bu durumda, ASP.NET 4. x Web API 'SI, isteğe bağlı olarak URL 'Lerin biçimle eşleşmesini bekler `/api/{controller}/{id}` `{id}` .</span><span class="sxs-lookup"><span data-stu-id="0e4a9-192">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="0e4a9-193">Aşağıdaki bölümlerde, Web API projesinin ASP.NET Core MVC 'ye geçişi gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-193">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="0e4a9-194">Hedef projeyi oluşturma</span><span class="sxs-lookup"><span data-stu-id="0e4a9-194">Create the destination project</span></span>

<span data-ttu-id="0e4a9-195">Visual Studio 'da aşağıdaki adımları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-195">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="0e4a9-196">**Dosya**  >  **Yeni**  >  **Proje**  >  **diğer proje türleri**  >  **Visual Studio çözümleri**' ne gidin.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-196">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions**.</span></span> <span data-ttu-id="0e4a9-197">**Boş çözüm**' i seçin ve çözümü *WebAPIMigration*olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-197">Select **Blank Solution**, and name the solution *WebAPIMigration*.</span></span> <span data-ttu-id="0e4a9-198">**Tamam** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-198">Click the **OK** button.</span></span>
* <span data-ttu-id="0e4a9-199">Varolan *Productsapp* projesini çözüme ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-199">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="0e4a9-200">Çözüme yeni bir **ASP.NET Core Web uygulaması** projesi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-200">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="0e4a9-201">Açılan listeden **.NET Core** hedef çerçevesini seçin ve **API** proje şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-201">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="0e4a9-202">Projeyi *Productscore*olarak adlandırın ve **Tamam** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-202">Name the project *ProductsCore*, and click the **OK** button.</span></span>

<span data-ttu-id="0e4a9-203">Çözüm artık iki proje içerir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-203">The solution now contains two projects.</span></span> <span data-ttu-id="0e4a9-204">Aşağıdaki bölümlerde *Productsapp* projesinin Içeriğinin *productscore* projesine geçirilmesi açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-204">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="0e4a9-205">Yapılandırmayı geçir</span><span class="sxs-lookup"><span data-stu-id="0e4a9-205">Migrate configuration</span></span>

<span data-ttu-id="0e4a9-206">ASP.NET Core şunu kullanmaz:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-206">ASP.NET Core doesn't use:</span></span>

* <span data-ttu-id="0e4a9-207">*App_Start* klasörü veya *Global. asax* dosyası</span><span class="sxs-lookup"><span data-stu-id="0e4a9-207">*App_Start* folder or the *Global.asax* file</span></span>
* <span data-ttu-id="0e4a9-208">*Web. config* dosyası yayımlama zamanında eklenir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-208">*web.config* file is added at publish time.</span></span>

<span data-ttu-id="0e4a9-209">`Startup` sınıfı:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-209">The `Startup` class:</span></span>

* <span data-ttu-id="0e4a9-210">*Global. asax*öğesini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-210">Replaces *Global.asax*.</span></span>
* <span data-ttu-id="0e4a9-211">Tüm uygulama başlangıç görevlerini işler.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-211">Handles all app startup tasks.</span></span>

<span data-ttu-id="0e4a9-212">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-212">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="0e4a9-213">ASP.NET Core MVC 'de, ' de çağrıldığında öznitelik yönlendirme varsayılan olarak dahil edilir <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="0e4a9-213">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="0e4a9-214">Aşağıdaki `UseMvc` çağrı *Productsapp* projesinin *App_Start/webapiconfig.cs* dosyasının yerini alır:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-214">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="0e4a9-215">Modelleri ve denetleyicileri geçirme</span><span class="sxs-lookup"><span data-stu-id="0e4a9-215">Migrate models and controllers</span></span>

<span data-ttu-id="0e4a9-216">Aşağıdaki kod `ProductsController` ASP.NET Core güncelleştirmesini gösterir:[!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span><span class="sxs-lookup"><span data-stu-id="0e4a9-216">The following code shows the `ProductsController` update for ASP.NET Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span></span>

<span data-ttu-id="0e4a9-217">`ProductsController`ASP.NET Core için Güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-217">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="0e4a9-218">*Denetleyiciyi/ProductsController. cs* öğesini özgün projeden yeni bir kopyaya kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-218">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="0e4a9-219">*Modeller* klasörünü özgün projeden yeni bir klasöre kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-219">Copy the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="0e4a9-220">Kopyalanan dosyaların kök ad alanını olarak değiştirin `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="0e4a9-220">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="0e4a9-221">`using ProductsApp.Models;`İfadesini olarak güncelleştirin `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="0e4a9-221">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="0e4a9-222">Aşağıdaki bileşenler ASP.NET Core yok:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-222">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="0e4a9-223">`ApiController` sınıfı</span><span class="sxs-lookup"><span data-stu-id="0e4a9-223">`ApiController` class</span></span>
* <span data-ttu-id="0e4a9-224">`System.Web.Http`uzayına</span><span class="sxs-lookup"><span data-stu-id="0e4a9-224">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="0e4a9-225">`IHttpActionResult`arayüz</span><span class="sxs-lookup"><span data-stu-id="0e4a9-225">`IHttpActionResult` interface</span></span>

<span data-ttu-id="0e4a9-226">Aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-226">Make the following changes:</span></span>

1. <span data-ttu-id="0e4a9-227">`ApiController`Olarak değiştirin <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="0e4a9-227">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="0e4a9-228">`using Microsoft.AspNetCore.Mvc;`Başvuruyu çözümlemek için ekleyin `ControllerBase` .</span><span class="sxs-lookup"><span data-stu-id="0e4a9-228">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="0e4a9-229">`using System.Web.Http;` klasörünü silin.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-229">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="0e4a9-230">`GetProduct`Eylemin dönüş türünü `IHttpActionResult` olarak değiştirin `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="0e4a9-230">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="0e4a9-231">`GetProduct`Eylemin `return` ifadesini aşağıdaki şekilde kolaylaştırın:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-231">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="0e4a9-232">Yönlendirmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="0e4a9-232">Configure routing</span></span>

<span data-ttu-id="0e4a9-233">Yönlendirmeyi aşağıdaki şekilde yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-233">Configure routing as follows:</span></span>

1. <span data-ttu-id="0e4a9-234">`ProductsController`Sınıfı aşağıdaki özniteliklere işaretle:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-234">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="0e4a9-235">Önceki [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) öznitelik, denetleyicinin öznitelik yönlendirme modelini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-235">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="0e4a9-236">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Öznitelik, bu denetleyicideki tüm eylemler için öznitelik yönlendirme bir gereksinim yapar.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-236">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="0e4a9-237">Öznitelik yönlendirme, ve gibi belirteçleri destekler `[controller]` `[action]` .</span><span class="sxs-lookup"><span data-stu-id="0e4a9-237">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="0e4a9-238">Çalışma zamanında, her belirteç, sırasıyla özniteliğin uygulandığı denetleyicinin veya eylemin adı ile değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-238">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="0e4a9-239">Belirteçler, projedeki sihirli dize sayısını azaltır.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-239">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="0e4a9-240">Belirteçler Ayrıca otomatik yeniden adlandırma yeniden düzenlemeler uygulandığında, yolların karşılık gelen denetleyiciler ve eylemlerle eşitlenmiş durumda kalmasını da güvence altına aldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-240">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="0e4a9-241">Projenin uyumluluk modunu ASP.NET Core 2,2 olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-241">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="0e4a9-242">Önceki değişiklik:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-242">The preceding change:</span></span>

    * <span data-ttu-id="0e4a9-243">`[ApiController]`Özniteliği denetleyici düzeyinde kullanmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-243">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="0e4a9-244">ASP.NET Core 2,2 ' de ortaya çıkan davranışlardan oluşan davranışa izin verebilir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-244">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="0e4a9-245">Eylemlere HTTP Get isteklerini etkinleştirin `ProductController` :</span><span class="sxs-lookup"><span data-stu-id="0e4a9-245">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="0e4a9-246">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Özniteliği `GetAllProducts` eyleme uygulayın.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-246">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="0e4a9-247">`[HttpGet("{id}")]`Özniteliği `GetProduct` eyleme uygulayın.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-247">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="0e4a9-248">Geçirilen projeyi çalıştırın ve konumuna gidin `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="0e4a9-248">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="0e4a9-249">Üç ürünün tam bir listesi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-249">A full list of three products appears.</span></span> <span data-ttu-id="0e4a9-250">`/api/products/1` adresine gidin.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-250">Browse to `/api/products/1`.</span></span> <span data-ttu-id="0e4a9-251">İlk ürün görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-251">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="0e4a9-252">Uyumluluk dolgusu</span><span class="sxs-lookup"><span data-stu-id="0e4a9-252">Compatibility shim</span></span>

<span data-ttu-id="0e4a9-253">[Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) kitaplığı, ASP.NET 4. x Web apı projelerini ASP.NET Core 'ye taşımak için bir uyumluluk dolgusu sağlar.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-253">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="0e4a9-254">Uyumluluk dolgusu, ASP.NET 4. x Web API 2 ' den çok sayıda kuralı desteklemek için ASP.NET Core genişletir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-254">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="0e4a9-255">Bu belgede daha önce yer alan örnek, uyumluluk dolgunun gereksiz olduğu kadar temel bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-255">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="0e4a9-256">Daha büyük projeler için, uyumluluk dolgunun kullanılması, ASP.NET Core ile ASP.NET 4. x Web API 2 arasındaki API boşluğunu geçici olarak köprülemesi için yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-256">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="0e4a9-257">Web API 'SI uyumluluk dolgusu, büyük ASP.NET 4. x Web API projelerini ASP.NET Core geçirmeyi desteklemek için geçici bir ölçü olarak kullanılmak üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-257">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="0e4a9-258">Zaman içinde, projelerin uyumluluk dolgusunda güvenmek yerine ASP.NET Core desenler kullanması için güncelleştirilmeleri gerekir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-258">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="0e4a9-259">İçinde bulunan uyumluluk özellikleri `Microsoft.AspNetCore.Mvc.WebApiCompatShim` şunlardır:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-259">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="0e4a9-260">`ApiController`Denetleyicilerin temel türlerinin güncelleştirilmesini gerektirmeyen bir tür ekler.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-260">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="0e4a9-261">Web API stili model bağlamayı etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-261">Enables Web API-style model binding.</span></span> <span data-ttu-id="0e4a9-262">ASP.NET Core MVC modeli bağlama işlevleri, varsayılan olarak ASP.NET 4. x MVC 5 ' in benzer şekilde.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-262">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="0e4a9-263">Uyumluluk dolgusu model bağlamasını ASP.NET 4. x Web API 2 model bağlama kurallarına benzer olacak şekilde değiştirir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-263">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="0e4a9-264">Örneğin, karmaşık türler, istek gövdesinden otomatik olarak bağlanır.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-264">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="0e4a9-265">Denetleyici eylemlerinin tür parametreleri alması için model bağlamayı genişletir `HttpRequestMessage` .</span><span class="sxs-lookup"><span data-stu-id="0e4a9-265">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="0e4a9-266">Eylemlerin tür sonuçları döndürmesini sağlayan ileti biçimleri ekler `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="0e4a9-266">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="0e4a9-267">Web API 2 eylemlerinin yanıtları karşılamak için kullanmış olabileceği ek yanıt yöntemleri ekler:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-267">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="0e4a9-268">`HttpResponseMessage`oluşturucuları</span><span class="sxs-lookup"><span data-stu-id="0e4a9-268">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="0e4a9-269">Eylem sonucu yöntemleri:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-269">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="0e4a9-270">`IContentNegotiator`Uygulamanın bağımlılık ekleme (dı) kapsayıcısına bir örneğini ekler ve [Microsoft. Aspnet. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)öğesinden içerik anlaşmasına ilişkin türleri kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-270">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="0e4a9-271">Bu tür türlere örnekler `DefaultContentNegotiator` ve içerir `MediaTypeFormatter` .</span><span class="sxs-lookup"><span data-stu-id="0e4a9-271">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="0e4a9-272">Uyumluluk Shim 'yi kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="0e4a9-272">To use the compatibility shim:</span></span>

1. <span data-ttu-id="0e4a9-273">[Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet paketini yükler.</span><span class="sxs-lookup"><span data-stu-id="0e4a9-273">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="0e4a9-274">İçinde arayarak uyumluluk dolgunun hizmetlerini uygulamanın dı kapsayıcısına kaydedin `services.AddMvc().AddWebApiConventions()` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0e4a9-274">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="0e4a9-275">`MapWebApiRoute`Uygulamanın çağrısında üzerinde kullanarak Web API 'sine özgü yollar tanımlayın `IRouteBuilder` `IApplicationBuilder.UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="0e4a9-275">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0e4a9-276">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0e4a9-276">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end
