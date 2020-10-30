---
title: ASP.NET Web API 'sinden ASP.NET Core 'e geçiş
author: ardalis
description: ASP.NET 4. x Web API 'sinden bir Web API uygulamasını ASP.NET Core MVC 'ye geçirmeyi öğrenin.
ms.author: scaddie
ms.custom: mvc
ms.date: 05/26/2020
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
uid: migration/webapi
ms.openlocfilehash: 320805c0d40bf06cee384e6d98caea5c420d45bc
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061476"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a><span data-ttu-id="69ece-103">ASP.NET Web API 'sinden ASP.NET Core 'e geçiş</span><span class="sxs-lookup"><span data-stu-id="69ece-103">Migrate from ASP.NET Web API to ASP.NET Core</span></span>

<span data-ttu-id="69ece-104">[Scott Ade](https://twitter.com/scott_addie) ve [Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="69ece-104">By [Scott Addie](https://twitter.com/scott_addie) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="69ece-105">ASP.NET 4. x Web API 'SI, tarayıcılar ve mobil cihazlar dahil olmak üzere çok çeşitli istemcilere ulaşan bir HTTP hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="69ece-105">An ASP.NET 4.x Web API is an HTTP service that reaches a broad range of clients, including browsers and mobile devices.</span></span> <span data-ttu-id="69ece-106">ASP.NET Core ASP.NET 4. x ' in MVC ve Web API 'SI uygulama modellerini ASP.NET Core MVC olarak bilinen tek bir programlama modelinde birleştirir.</span><span class="sxs-lookup"><span data-stu-id="69ece-106">ASP.NET Core combines ASP.NET 4.x's MVC and Web API app models into a single programming model known as ASP.NET Core MVC.</span></span> <span data-ttu-id="69ece-107">Bu makalede, ASP.NET 4. x Web API 'sinden ASP.NET Core MVC 'ye geçiş yapmak için gereken adımlar gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="69ece-107">This article demonstrates the steps required to migrate from ASP.NET 4.x Web API to ASP.NET Core MVC.</span></span>

<span data-ttu-id="69ece-108">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="69ece-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="69ece-109">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="69ece-109">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="69ece-110">ASP.NET 4. x Web API projesini inceleyin</span><span class="sxs-lookup"><span data-stu-id="69ece-110">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="69ece-111">Bu makale, [ASP.NET Web API 2 Ile çalışmaya](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)başlama bölümünde oluşturulan *productsapp* projesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="69ece-111">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="69ece-112">Bu projede, temel bir ASP.NET 4. x Web API projesi aşağıdaki şekilde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="69ece-112">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="69ece-113">*Global.asax.cs* ' de, şu şekilde bir çağrı yapılır `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="69ece-113">In *Global.asax.cs* , a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="69ece-114">`WebApiConfig`Sınıfı *App_Start* klasöründe bulunur ve statik bir `Register` yönteme sahiptir:</span><span class="sxs-lookup"><span data-stu-id="69ece-114">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="69ece-115">Önceki sınıf:</span><span class="sxs-lookup"><span data-stu-id="69ece-115">The preceding class:</span></span>

* <span data-ttu-id="69ece-116">, Gerçekten kullanılmasa da, [öznitelik yönlendirmeyi](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="69ece-116">Configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used.</span></span>
* <span data-ttu-id="69ece-117">Yönlendirme tablosunu yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="69ece-117">Configures the routing table.</span></span>
<span data-ttu-id="69ece-118">Örnek kod, isteğe bağlı olarak, URL 'Lerin biçimle eşleşmesini bekler `/api/{controller}/{id}` `{id}` .</span><span class="sxs-lookup"><span data-stu-id="69ece-118">The sample code expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="69ece-119">Aşağıdaki bölümlerde, Web API projesinin ASP.NET Core MVC 'ye geçişi gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="69ece-119">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="69ece-120">Hedef projeyi oluşturma</span><span class="sxs-lookup"><span data-stu-id="69ece-120">Create the destination project</span></span>

<span data-ttu-id="69ece-121">Visual Studio 'da yeni bir boş çözüm oluşturun ve geçirilecek ASP.NET 4. x Web API projesini ekleyin:</span><span class="sxs-lookup"><span data-stu-id="69ece-121">Create a new blank solution in Visual Studio and add the ASP.NET 4.x Web API project to migrate:</span></span>

1. <span data-ttu-id="69ece-122">**Dosya** menüsünden **Yeni** > **Proje** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="69ece-122">From the **File** menu, select **New** > **Project** .</span></span>
1. <span data-ttu-id="69ece-123">**Boş çözüm** şablonunu seçin ve Ileri ' **yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="69ece-123">Select the **Blank Solution** template and select **Next** .</span></span>
1. <span data-ttu-id="69ece-124">Çözümü *WebAPIMigration* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="69ece-124">Name the solution *WebAPIMigration* .</span></span> <span data-ttu-id="69ece-125">**Oluştur** ’u seçin.</span><span class="sxs-lookup"><span data-stu-id="69ece-125">Select **Create** .</span></span>
1. <span data-ttu-id="69ece-126">Varolan *Productsapp* projesini çözüme ekleyin.</span><span class="sxs-lookup"><span data-stu-id="69ece-126">Add the existing *ProductsApp* project to the solution.</span></span>

<span data-ttu-id="69ece-127">Geçirilecek yeni bir API projesi ekleyin:</span><span class="sxs-lookup"><span data-stu-id="69ece-127">Add a new API project to migrate to:</span></span>

1. <span data-ttu-id="69ece-128">Çözüme yeni bir **ASP.NET Core Web uygulaması** projesi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="69ece-128">Add a new **ASP.NET Core Web Application** project to the solution.</span></span>
1. <span data-ttu-id="69ece-129">**Yeni projenizi yapılandırın** iletişim kutusunda, proje *Productscore* ' ı adlandırın ve **Oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="69ece-129">In the **Configure your new project** dialog, Name the project *ProductsCore* , and select **Create** .</span></span>
1. <span data-ttu-id="69ece-130">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda, **.net Core** ve **ASP.NET Core 3,1** ' un seçili olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="69ece-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="69ece-131">**API** proje şablonunu seçin ve **Oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="69ece-131">Select the **API** project template, and select **Create** .</span></span>
1. <span data-ttu-id="69ece-132">*WeatherForecast.cs* ve *Controllers/dalgalı sıra. cs* örnek dosyalarını yeni *productscore* projesinden kaldırın.</span><span class="sxs-lookup"><span data-stu-id="69ece-132">Remove the *WeatherForecast.cs* and *Controllers/WeatherForecastController.cs* example files from the new *ProductsCore* project.</span></span>

<span data-ttu-id="69ece-133">Çözüm artık iki proje içerir.</span><span class="sxs-lookup"><span data-stu-id="69ece-133">The solution now contains two projects.</span></span> <span data-ttu-id="69ece-134">Aşağıdaki bölümlerde *Productsapp* projesinin Içeriğinin *productscore* projesine geçirilmesi açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="69ece-134">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="69ece-135">Yapılandırmayı geçir</span><span class="sxs-lookup"><span data-stu-id="69ece-135">Migrate configuration</span></span>

<span data-ttu-id="69ece-136">ASP.NET Core *App_Start* klasörünü veya *Global. asax* dosyasını kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="69ece-136">ASP.NET Core doesn't use the *App_Start* folder or the *Global.asax* file.</span></span> <span data-ttu-id="69ece-137">Ayrıca, *web.config* dosyası yayımlama zamanında eklenir.</span><span class="sxs-lookup"><span data-stu-id="69ece-137">Additionally, the *web.config* file is added at publish time.</span></span>

<span data-ttu-id="69ece-138">`Startup` sınıfı:</span><span class="sxs-lookup"><span data-stu-id="69ece-138">The `Startup` class:</span></span>

* <span data-ttu-id="69ece-139">*Global. asax* öğesini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="69ece-139">Replaces *Global.asax* .</span></span>
* <span data-ttu-id="69ece-140">Tüm uygulama başlangıç görevlerini işler.</span><span class="sxs-lookup"><span data-stu-id="69ece-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="69ece-141">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="69ece-141">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="69ece-142">Modelleri ve denetleyicileri geçirme</span><span class="sxs-lookup"><span data-stu-id="69ece-142">Migrate models and controllers</span></span>

<span data-ttu-id="69ece-143">Aşağıdaki kod, `ProductsController` ASP.NET Core için güncelleştirileceğini gösterir:</span><span class="sxs-lookup"><span data-stu-id="69ece-143">The following code shows the `ProductsController` to be updated for ASP.NET Core:</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsApp/Controllers/ProductsController.cs)]

<span data-ttu-id="69ece-144">`ProductsController`ASP.NET Core için Güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="69ece-144">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="69ece-145">*Denetleyiciyi/ProductsController. cs* ve *modeller* klasörünü özgün projeden yeni bir klasöre kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="69ece-145">Copy *Controllers/ProductsController.cs* and the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="69ece-146">Kopyalanan dosyaların kök ad alanını olarak değiştirin `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="69ece-146">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="69ece-147">`using ProductsApp.Models;`İfadesini olarak güncelleştirin `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="69ece-147">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="69ece-148">Aşağıdaki bileşenler ASP.NET Core yok:</span><span class="sxs-lookup"><span data-stu-id="69ece-148">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="69ece-149">`ApiController` sınıfı</span><span class="sxs-lookup"><span data-stu-id="69ece-149">`ApiController` class</span></span>
* <span data-ttu-id="69ece-150">`System.Web.Http` uzayına</span><span class="sxs-lookup"><span data-stu-id="69ece-150">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="69ece-151">`IHttpActionResult` arayüz</span><span class="sxs-lookup"><span data-stu-id="69ece-151">`IHttpActionResult` interface</span></span>

<span data-ttu-id="69ece-152">Aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="69ece-152">Make the following changes:</span></span>

1. <span data-ttu-id="69ece-153">`ApiController`Olarak değiştirin <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="69ece-153">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="69ece-154">`using Microsoft.AspNetCore.Mvc;`Başvuruyu çözümlemek için ekleyin `ControllerBase` .</span><span class="sxs-lookup"><span data-stu-id="69ece-154">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="69ece-155">`using System.Web.Http;` klasörünü silin.</span><span class="sxs-lookup"><span data-stu-id="69ece-155">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="69ece-156">`GetProduct`Eylemin dönüş türünü `IHttpActionResult` olarak değiştirin `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="69ece-156">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="69ece-157">`GetProduct`Eylemin `return` ifadesini aşağıdaki şekilde kolaylaştırın:</span><span class="sxs-lookup"><span data-stu-id="69ece-157">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="69ece-158">Yönlendirmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="69ece-158">Configure routing</span></span>

<span data-ttu-id="69ece-159">ASP.NET Core *API* proje şablonu, üretilen koddaki Endpoint Routing yapılandırmasını içerir.</span><span class="sxs-lookup"><span data-stu-id="69ece-159">The ASP.NET Core *API* project template includes endpoint routing configuration in the generated code.</span></span>

<span data-ttu-id="69ece-160">Aşağıdakiler <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> ve <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> çağırır:</span><span class="sxs-lookup"><span data-stu-id="69ece-160">The following <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> and <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> calls:</span></span>

* <span data-ttu-id="69ece-161">Yönlendirme eşleştirme ve uç nokta yürütmeyi [Ara yazılım](xref:fundamentals/middleware/index) ardışık düzeninde kaydedin.</span><span class="sxs-lookup"><span data-stu-id="69ece-161">Register route matching and endpoint execution in the [middleware](xref:fundamentals/middleware/index) pipeline.</span></span>
* <span data-ttu-id="69ece-162">*Productsapp* projesinin *App_Start/webapiconfig.cs* dosyasını değiştirin.</span><span class="sxs-lookup"><span data-stu-id="69ece-162">Replace the *ProductsApp* project's *App_Start/WebApiConfig.cs* file.</span></span>

[!code-csharp[](webapi/sample/3.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=10,14)]

<span data-ttu-id="69ece-163">Yönlendirmeyi aşağıdaki şekilde yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="69ece-163">Configure routing as follows:</span></span>

1. <span data-ttu-id="69ece-164">`ProductsController`Sınıfı aşağıdaki özniteliklere işaretle:</span><span class="sxs-lookup"><span data-stu-id="69ece-164">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="69ece-165">Önceki [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) öznitelik, denetleyicinin öznitelik yönlendirme modelini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="69ece-165">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="69ece-166">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Öznitelik, bu denetleyicideki tüm eylemler için öznitelik yönlendirme bir gereksinim yapar.</span><span class="sxs-lookup"><span data-stu-id="69ece-166">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="69ece-167">Öznitelik yönlendirme, ve gibi belirteçleri destekler `[controller]` `[action]` .</span><span class="sxs-lookup"><span data-stu-id="69ece-167">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="69ece-168">Çalışma zamanında, her belirteç, sırasıyla özniteliğin uygulandığı denetleyicinin veya eylemin adı ile değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="69ece-168">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="69ece-169">Belirteçler:</span><span class="sxs-lookup"><span data-stu-id="69ece-169">The tokens:</span></span>
    * <span data-ttu-id="69ece-170">Projedeki sihirli dize sayısını azaltın.</span><span class="sxs-lookup"><span data-stu-id="69ece-170">Reduce the number of magic strings in the project.</span></span>
    * <span data-ttu-id="69ece-171">Otomatik yeniden adlandırma yeniden düzenlemeler uygulandığında, yolların karşılık gelen denetleyiciler ve eylemlerle eşitlenmiş durumda kalmasını sağlayın.</span><span class="sxs-lookup"><span data-stu-id="69ece-171">Ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="69ece-172">Eylemlere HTTP Get isteklerini etkinleştirin `ProductController` :</span><span class="sxs-lookup"><span data-stu-id="69ece-172">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="69ece-173">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Özniteliği `GetAllProducts` eyleme uygulayın.</span><span class="sxs-lookup"><span data-stu-id="69ece-173">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="69ece-174">`[HttpGet("{id}")]`Özniteliği `GetProduct` eyleme uygulayın.</span><span class="sxs-lookup"><span data-stu-id="69ece-174">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="69ece-175">Geçirilen projeyi çalıştırın ve konumuna gidin `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="69ece-175">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="69ece-176">Üç ürünün tam bir listesi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="69ece-176">A full list of three products appears.</span></span> <span data-ttu-id="69ece-177">`/api/products/1` adresine gidin.</span><span class="sxs-lookup"><span data-stu-id="69ece-177">Browse to `/api/products/1`.</span></span> <span data-ttu-id="69ece-178">İlk ürün görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="69ece-178">The first product appears.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="69ece-179">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="69ece-179">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
## <a name="prerequisites"></a><span data-ttu-id="69ece-180">Önkoşullar</span><span class="sxs-lookup"><span data-stu-id="69ece-180">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a><span data-ttu-id="69ece-181">ASP.NET 4. x Web API projesini inceleyin</span><span class="sxs-lookup"><span data-stu-id="69ece-181">Review ASP.NET 4.x Web API project</span></span>

<span data-ttu-id="69ece-182">Bu makale, [ASP.NET Web API 2 Ile çalışmaya](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)başlama bölümünde oluşturulan *productsapp* projesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="69ece-182">This article uses the *ProductsApp* project created in [Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).</span></span> <span data-ttu-id="69ece-183">Bu projede, temel bir ASP.NET 4. x Web API projesi aşağıdaki şekilde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="69ece-183">In that project, a basic ASP.NET 4.x Web API project is configured as follows.</span></span>

<span data-ttu-id="69ece-184">*Global.asax.cs* ' de, şu şekilde bir çağrı yapılır `WebApiConfig.Register` :</span><span class="sxs-lookup"><span data-stu-id="69ece-184">In *Global.asax.cs* , a call is made to `WebApiConfig.Register`:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/Global.asax.cs?highlight=14)]

<span data-ttu-id="69ece-185">`WebApiConfig`Sınıfı *App_Start* klasöründe bulunur ve statik bir `Register` yönteme sahiptir:</span><span class="sxs-lookup"><span data-stu-id="69ece-185">The `WebApiConfig` class is found in the *App_Start* folder and has a static `Register` method:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsApp/App_Start/WebApiConfig.cs)]

<span data-ttu-id="69ece-186">Bu sınıf, proje içinde gerçekten kullanılmasa da [öznitelik yönlendirmeyi](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="69ece-186">This class configures [attribute routing](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2), although it's not actually being used in the project.</span></span> <span data-ttu-id="69ece-187">Ayrıca, ASP.NET Web API 'SI tarafından kullanılan yönlendirme tablosunu da yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="69ece-187">It also configures the routing table, which is used by ASP.NET Web API.</span></span> <span data-ttu-id="69ece-188">Bu durumda, ASP.NET 4. x Web API 'SI, isteğe bağlı olarak URL 'Lerin biçimle eşleşmesini bekler `/api/{controller}/{id}` `{id}` .</span><span class="sxs-lookup"><span data-stu-id="69ece-188">In this case, ASP.NET 4.x Web API expects URLs to match the format `/api/{controller}/{id}`, with `{id}` being optional.</span></span>

<span data-ttu-id="69ece-189">Aşağıdaki bölümlerde, Web API projesinin ASP.NET Core MVC 'ye geçişi gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="69ece-189">The following sections demonstrate migration of the Web API project to ASP.NET Core MVC.</span></span>

## <a name="create-the-destination-project"></a><span data-ttu-id="69ece-190">Hedef projeyi oluşturma</span><span class="sxs-lookup"><span data-stu-id="69ece-190">Create the destination project</span></span>

<span data-ttu-id="69ece-191">Visual Studio 'da aşağıdaki adımları uygulayın:</span><span class="sxs-lookup"><span data-stu-id="69ece-191">Complete the following steps in Visual Studio:</span></span>

* <span data-ttu-id="69ece-192">**Dosya**  >  **Yeni**  >  **Proje**  >  **diğer proje türleri**  >  **Visual Studio çözümleri** ' ne gidin.</span><span class="sxs-lookup"><span data-stu-id="69ece-192">Go to **File** > **New** > **Project** > **Other Project Types** > **Visual Studio Solutions** .</span></span> <span data-ttu-id="69ece-193">**Boş çözüm** ' i seçin ve çözümü *WebAPIMigration* olarak adlandırın.</span><span class="sxs-lookup"><span data-stu-id="69ece-193">Select **Blank Solution** , and name the solution *WebAPIMigration* .</span></span> <span data-ttu-id="69ece-194">**Tamam** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="69ece-194">Click the **OK** button.</span></span>
* <span data-ttu-id="69ece-195">Varolan *Productsapp* projesini çözüme ekleyin.</span><span class="sxs-lookup"><span data-stu-id="69ece-195">Add the existing *ProductsApp* project to the solution.</span></span>
* <span data-ttu-id="69ece-196">Çözüme yeni bir **ASP.NET Core Web uygulaması** projesi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="69ece-196">Add a new **ASP.NET Core Web Application** project to the solution.</span></span> <span data-ttu-id="69ece-197">Açılan listeden **.NET Core** hedef çerçevesini seçin ve **API** proje şablonunu seçin.</span><span class="sxs-lookup"><span data-stu-id="69ece-197">Select the **.NET Core** target framework from the drop-down, and select the **API** project template.</span></span> <span data-ttu-id="69ece-198">Projeyi *Productscore* olarak adlandırın ve **Tamam** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="69ece-198">Name the project *ProductsCore* , and click the **OK** button.</span></span>

<span data-ttu-id="69ece-199">Çözüm artık iki proje içerir.</span><span class="sxs-lookup"><span data-stu-id="69ece-199">The solution now contains two projects.</span></span> <span data-ttu-id="69ece-200">Aşağıdaki bölümlerde *Productsapp* projesinin Içeriğinin *productscore* projesine geçirilmesi açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="69ece-200">The following sections explain migrating the *ProductsApp* project's contents to the *ProductsCore* project.</span></span>

## <a name="migrate-configuration"></a><span data-ttu-id="69ece-201">Yapılandırmayı geçir</span><span class="sxs-lookup"><span data-stu-id="69ece-201">Migrate configuration</span></span>

<span data-ttu-id="69ece-202">ASP.NET Core şunu kullanmaz:</span><span class="sxs-lookup"><span data-stu-id="69ece-202">ASP.NET Core doesn't use:</span></span>

* <span data-ttu-id="69ece-203">*App_Start* klasörü veya *Global. asax* dosyası</span><span class="sxs-lookup"><span data-stu-id="69ece-203">*App_Start* folder or the *Global.asax* file</span></span>
* <span data-ttu-id="69ece-204">*web.config* dosya yayımlama zamanında eklenir.</span><span class="sxs-lookup"><span data-stu-id="69ece-204">*web.config* file is added at publish time.</span></span>

<span data-ttu-id="69ece-205">`Startup` sınıfı:</span><span class="sxs-lookup"><span data-stu-id="69ece-205">The `Startup` class:</span></span>

* <span data-ttu-id="69ece-206">*Global. asax* öğesini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="69ece-206">Replaces *Global.asax* .</span></span>
* <span data-ttu-id="69ece-207">Tüm uygulama başlangıç görevlerini işler.</span><span class="sxs-lookup"><span data-stu-id="69ece-207">Handles all app startup tasks.</span></span>

<span data-ttu-id="69ece-208">Daha fazla bilgi için bkz. <xref:fundamentals/startup>.</span><span class="sxs-lookup"><span data-stu-id="69ece-208">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="69ece-209">ASP.NET Core MVC 'de, ' de çağrıldığında öznitelik yönlendirme varsayılan olarak dahil edilir <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="69ece-209">In ASP.NET Core MVC, attribute routing is included by default when <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> is called in `Startup.Configure`.</span></span> <span data-ttu-id="69ece-210">Aşağıdaki `UseMvc` çağrı *Productsapp* projesinin *App_Start/webapiconfig.cs* dosyasının yerini alır:</span><span class="sxs-lookup"><span data-stu-id="69ece-210">The following `UseMvc` call replaces the *ProductsApp* project's *App_Start/WebApiConfig.cs* file:</span></span>

[!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13)]

## <a name="migrate-models-and-controllers"></a><span data-ttu-id="69ece-211">Modelleri ve denetleyicileri geçirme</span><span class="sxs-lookup"><span data-stu-id="69ece-211">Migrate models and controllers</span></span>

<span data-ttu-id="69ece-212">Aşağıdaki kod `ProductsController` ASP.NET Core güncelleştirmesini gösterir: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span><span class="sxs-lookup"><span data-stu-id="69ece-212">The following code shows the `ProductsController` update for ASP.NET Core: [!code-csharp[](webapi/sample/2.x/ProductsApp/Controllers/ProductsController.cs)]</span></span>

<span data-ttu-id="69ece-213">`ProductsController`ASP.NET Core için Güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="69ece-213">Update the `ProductsController` for ASP.NET Core:</span></span>

1. <span data-ttu-id="69ece-214">*Denetleyiciyi/ProductsController. cs* öğesini özgün projeden yeni bir kopyaya kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="69ece-214">Copy *Controllers/ProductsController.cs* from the original project to the new one.</span></span>
1. <span data-ttu-id="69ece-215">*Modeller* klasörünü özgün projeden yeni bir klasöre kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="69ece-215">Copy the *Models* folder from the original project to the new one.</span></span>
1. <span data-ttu-id="69ece-216">Kopyalanan dosyaların kök ad alanını olarak değiştirin `ProductsCore` .</span><span class="sxs-lookup"><span data-stu-id="69ece-216">Change the copied files' root namespace to `ProductsCore`.</span></span>
1. <span data-ttu-id="69ece-217">`using ProductsApp.Models;`İfadesini olarak güncelleştirin `using ProductsCore.Models;` .</span><span class="sxs-lookup"><span data-stu-id="69ece-217">Update the `using ProductsApp.Models;` statement to `using ProductsCore.Models;`.</span></span>

<span data-ttu-id="69ece-218">Aşağıdaki bileşenler ASP.NET Core yok:</span><span class="sxs-lookup"><span data-stu-id="69ece-218">The following components don't exist in ASP.NET Core:</span></span>

* <span data-ttu-id="69ece-219">`ApiController` sınıfı</span><span class="sxs-lookup"><span data-stu-id="69ece-219">`ApiController` class</span></span>
* <span data-ttu-id="69ece-220">`System.Web.Http` uzayına</span><span class="sxs-lookup"><span data-stu-id="69ece-220">`System.Web.Http` namespace</span></span>
* <span data-ttu-id="69ece-221">`IHttpActionResult` arayüz</span><span class="sxs-lookup"><span data-stu-id="69ece-221">`IHttpActionResult` interface</span></span>

<span data-ttu-id="69ece-222">Aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="69ece-222">Make the following changes:</span></span>

1. <span data-ttu-id="69ece-223">`ApiController`Olarak değiştirin <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="69ece-223">Change `ApiController` to <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="69ece-224">`using Microsoft.AspNetCore.Mvc;`Başvuruyu çözümlemek için ekleyin `ControllerBase` .</span><span class="sxs-lookup"><span data-stu-id="69ece-224">Add `using Microsoft.AspNetCore.Mvc;` to resolve the `ControllerBase` reference.</span></span>
1. <span data-ttu-id="69ece-225">`using System.Web.Http;` klasörünü silin.</span><span class="sxs-lookup"><span data-stu-id="69ece-225">Delete `using System.Web.Http;`.</span></span>
1. <span data-ttu-id="69ece-226">`GetProduct`Eylemin dönüş türünü `IHttpActionResult` olarak değiştirin `ActionResult<Product>` .</span><span class="sxs-lookup"><span data-stu-id="69ece-226">Change the `GetProduct` action's return type from `IHttpActionResult` to `ActionResult<Product>`.</span></span>
1. <span data-ttu-id="69ece-227">`GetProduct`Eylemin `return` ifadesini aşağıdaki şekilde kolaylaştırın:</span><span class="sxs-lookup"><span data-stu-id="69ece-227">Simplify the `GetProduct` action's `return` statement to the following:</span></span>

    ```csharp
    return product;
    ```

## <a name="configure-routing"></a><span data-ttu-id="69ece-228">Yönlendirmeyi yapılandırma</span><span class="sxs-lookup"><span data-stu-id="69ece-228">Configure routing</span></span>

<span data-ttu-id="69ece-229">Yönlendirmeyi aşağıdaki şekilde yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="69ece-229">Configure routing as follows:</span></span>

1. <span data-ttu-id="69ece-230">`ProductsController`Sınıfı aşağıdaki özniteliklere işaretle:</span><span class="sxs-lookup"><span data-stu-id="69ece-230">Mark the `ProductsController` class with the following attributes:</span></span>

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    <span data-ttu-id="69ece-231">Önceki [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) öznitelik, denetleyicinin öznitelik yönlendirme modelini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="69ece-231">The preceding [`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute) attribute configures the controller's attribute routing pattern.</span></span> <span data-ttu-id="69ece-232">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Öznitelik, bu denetleyicideki tüm eylemler için öznitelik yönlendirme bir gereksinim yapar.</span><span class="sxs-lookup"><span data-stu-id="69ece-232">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute makes attribute routing a requirement for all actions in this controller.</span></span>

    <span data-ttu-id="69ece-233">Öznitelik yönlendirme, ve gibi belirteçleri destekler `[controller]` `[action]` .</span><span class="sxs-lookup"><span data-stu-id="69ece-233">Attribute routing supports tokens, such as `[controller]` and `[action]`.</span></span> <span data-ttu-id="69ece-234">Çalışma zamanında, her belirteç, sırasıyla özniteliğin uygulandığı denetleyicinin veya eylemin adı ile değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="69ece-234">At runtime, each token is replaced with the name of the controller or action, respectively, to which the attribute has been applied.</span></span> <span data-ttu-id="69ece-235">Belirteçler, projedeki sihirli dize sayısını azaltır.</span><span class="sxs-lookup"><span data-stu-id="69ece-235">The tokens reduce the number of magic strings in the project.</span></span> <span data-ttu-id="69ece-236">Belirteçler Ayrıca otomatik yeniden adlandırma yeniden düzenlemeler uygulandığında, yolların karşılık gelen denetleyiciler ve eylemlerle eşitlenmiş durumda kalmasını da güvence altına aldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="69ece-236">The tokens also ensure routes remain synchronized with the corresponding controllers and actions when automatic rename refactorings are applied.</span></span>
1. <span data-ttu-id="69ece-237">Projenin uyumluluk modunu ASP.NET Core 2,2 olarak ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="69ece-237">Set the project's compatibility mode to ASP.NET Core 2.2:</span></span>

    [!code-csharp[](webapi/sample/2.x/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    <span data-ttu-id="69ece-238">Önceki değişiklik:</span><span class="sxs-lookup"><span data-stu-id="69ece-238">The preceding change:</span></span>

    * <span data-ttu-id="69ece-239">`[ApiController]`Özniteliği denetleyici düzeyinde kullanmak için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="69ece-239">Is required to use the `[ApiController]` attribute at the controller level.</span></span>
    * <span data-ttu-id="69ece-240">ASP.NET Core 2,2 ' de ortaya çıkan davranışlardan oluşan davranışa izin verebilir.</span><span class="sxs-lookup"><span data-stu-id="69ece-240">Opts in to potentially breaking behaviors introduced in ASP.NET Core 2.2.</span></span>
1. <span data-ttu-id="69ece-241">Eylemlere HTTP Get isteklerini etkinleştirin `ProductController` :</span><span class="sxs-lookup"><span data-stu-id="69ece-241">Enable HTTP Get requests to the `ProductController` actions:</span></span>
    * <span data-ttu-id="69ece-242">[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)Özniteliği `GetAllProducts` eyleme uygulayın.</span><span class="sxs-lookup"><span data-stu-id="69ece-242">Apply the [`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute) attribute to the `GetAllProducts` action.</span></span>
    * <span data-ttu-id="69ece-243">`[HttpGet("{id}")]`Özniteliği `GetProduct` eyleme uygulayın.</span><span class="sxs-lookup"><span data-stu-id="69ece-243">Apply the `[HttpGet("{id}")]` attribute to the `GetProduct` action.</span></span>

<span data-ttu-id="69ece-244">Geçirilen projeyi çalıştırın ve konumuna gidin `/api/products` .</span><span class="sxs-lookup"><span data-stu-id="69ece-244">Run the migrated project, and browse to `/api/products`.</span></span> <span data-ttu-id="69ece-245">Üç ürünün tam bir listesi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="69ece-245">A full list of three products appears.</span></span> <span data-ttu-id="69ece-246">`/api/products/1` adresine gidin.</span><span class="sxs-lookup"><span data-stu-id="69ece-246">Browse to `/api/products/1`.</span></span> <span data-ttu-id="69ece-247">İlk ürün görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="69ece-247">The first product appears.</span></span>

## <a name="compatibility-shim"></a><span data-ttu-id="69ece-248">Uyumluluk dolgusu</span><span class="sxs-lookup"><span data-stu-id="69ece-248">Compatibility shim</span></span>

<span data-ttu-id="69ece-249">[Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) kitaplığı, ASP.NET 4. x Web apı projelerini ASP.NET Core 'ye taşımak için bir uyumluluk dolgusu sağlar.</span><span class="sxs-lookup"><span data-stu-id="69ece-249">The [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) library provides a compatibility shim to move ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="69ece-250">Uyumluluk dolgusu, ASP.NET 4. x Web API 2 ' den çok sayıda kuralı desteklemek için ASP.NET Core genişletir.</span><span class="sxs-lookup"><span data-stu-id="69ece-250">The compatibility shim extends ASP.NET Core to support a number of conventions from ASP.NET 4.x Web API 2.</span></span> <span data-ttu-id="69ece-251">Bu belgede daha önce yer alan örnek, uyumluluk dolgunun gereksiz olduğu kadar temel bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="69ece-251">The sample ported previously in this document is basic enough that the compatibility shim was unnecessary.</span></span> <span data-ttu-id="69ece-252">Daha büyük projeler için, uyumluluk dolgunun kullanılması, ASP.NET Core ile ASP.NET 4. x Web API 2 arasındaki API boşluğunu geçici olarak köprülemesi için yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="69ece-252">For larger projects, using the compatibility shim can be useful for temporarily bridging the API gap between ASP.NET Core and ASP.NET 4.x Web API 2.</span></span>

<span data-ttu-id="69ece-253">Web API 'SI uyumluluk dolgusu, büyük ASP.NET 4. x Web API projelerini ASP.NET Core geçirmeyi desteklemek için geçici bir ölçü olarak kullanılmak üzere tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="69ece-253">The Web API compatibility shim is meant to be used as a temporary measure to support migrating large ASP.NET 4.x Web API projects to ASP.NET Core.</span></span> <span data-ttu-id="69ece-254">Zaman içinde, projelerin uyumluluk dolgusunda güvenmek yerine ASP.NET Core desenler kullanması için güncelleştirilmeleri gerekir.</span><span class="sxs-lookup"><span data-stu-id="69ece-254">Over time, projects should be updated to use ASP.NET Core patterns instead of relying on the compatibility shim.</span></span>

<span data-ttu-id="69ece-255">İçinde bulunan uyumluluk özellikleri `Microsoft.AspNetCore.Mvc.WebApiCompatShim` şunlardır:</span><span class="sxs-lookup"><span data-stu-id="69ece-255">Compatibility features included in `Microsoft.AspNetCore.Mvc.WebApiCompatShim` include:</span></span>

* <span data-ttu-id="69ece-256">`ApiController`Denetleyicilerin temel türlerinin güncelleştirilmesini gerektirmeyen bir tür ekler.</span><span class="sxs-lookup"><span data-stu-id="69ece-256">Adds an `ApiController` type so that controllers' base types don't need to be updated.</span></span>
* <span data-ttu-id="69ece-257">Web API stili model bağlamayı etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="69ece-257">Enables Web API-style model binding.</span></span> <span data-ttu-id="69ece-258">ASP.NET Core MVC modeli bağlama işlevleri, varsayılan olarak ASP.NET 4. x MVC 5 ' in benzer şekilde.</span><span class="sxs-lookup"><span data-stu-id="69ece-258">ASP.NET Core MVC model binding functions similarly to that of ASP.NET 4.x MVC 5, by default.</span></span> <span data-ttu-id="69ece-259">Uyumluluk dolgusu model bağlamasını ASP.NET 4. x Web API 2 model bağlama kurallarına benzer olacak şekilde değiştirir.</span><span class="sxs-lookup"><span data-stu-id="69ece-259">The compatibility shim changes model binding to be more similar to ASP.NET 4.x Web API 2 model binding conventions.</span></span> <span data-ttu-id="69ece-260">Örneğin, karmaşık türler, istek gövdesinden otomatik olarak bağlanır.</span><span class="sxs-lookup"><span data-stu-id="69ece-260">For example, complex types are automatically bound from the request body.</span></span>
* <span data-ttu-id="69ece-261">Denetleyici eylemlerinin tür parametreleri alması için model bağlamayı genişletir `HttpRequestMessage` .</span><span class="sxs-lookup"><span data-stu-id="69ece-261">Extends model binding so that controller actions can take parameters of type `HttpRequestMessage`.</span></span>
* <span data-ttu-id="69ece-262">Eylemlerin tür sonuçları döndürmesini sağlayan ileti biçimleri ekler `HttpResponseMessage` .</span><span class="sxs-lookup"><span data-stu-id="69ece-262">Adds message formatters allowing actions to return results of type `HttpResponseMessage`.</span></span>
* <span data-ttu-id="69ece-263">Web API 2 eylemlerinin yanıtları karşılamak için kullanmış olabileceği ek yanıt yöntemleri ekler:</span><span class="sxs-lookup"><span data-stu-id="69ece-263">Adds additional response methods that Web API 2 actions may have used to serve responses:</span></span>
  * <span data-ttu-id="69ece-264">`HttpResponseMessage` oluşturucuları</span><span class="sxs-lookup"><span data-stu-id="69ece-264">`HttpResponseMessage` generators:</span></span>
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * <span data-ttu-id="69ece-265">Eylem sonucu yöntemleri:</span><span class="sxs-lookup"><span data-stu-id="69ece-265">Action result methods:</span></span>
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* <span data-ttu-id="69ece-266">`IContentNegotiator`Uygulamanın bağımlılık ekleme (dı) kapsayıcısına bir örneğini ekler ve [Microsoft. Aspnet. WebApi. Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)öğesinden içerik anlaşmasına ilişkin türleri kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="69ece-266">Adds an instance of `IContentNegotiator` to the app's dependency injection (DI) container and makes available the content negotiation-related types from [Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/).</span></span> <span data-ttu-id="69ece-267">Bu tür türlere örnekler `DefaultContentNegotiator` ve içerir `MediaTypeFormatter` .</span><span class="sxs-lookup"><span data-stu-id="69ece-267">Examples of such types include `DefaultContentNegotiator` and `MediaTypeFormatter`.</span></span>

<span data-ttu-id="69ece-268">Uyumluluk Shim 'yi kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="69ece-268">To use the compatibility shim:</span></span>

1. <span data-ttu-id="69ece-269">[Microsoft. AspNetCore. Mvc. WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet paketini yükler.</span><span class="sxs-lookup"><span data-stu-id="69ece-269">Install the [Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet package.</span></span>
1. <span data-ttu-id="69ece-270">İçinde arayarak uyumluluk dolgunun hizmetlerini uygulamanın dı kapsayıcısına kaydedin `services.AddMvc().AddWebApiConventions()` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="69ece-270">Register the compatibility shim's services with the app's DI container by calling `services.AddMvc().AddWebApiConventions()` in `Startup.ConfigureServices`.</span></span>
1. <span data-ttu-id="69ece-271">`MapWebApiRoute`Uygulamanın çağrısında üzerinde kullanarak Web API 'sine özgü yollar tanımlayın `IRouteBuilder` `IApplicationBuilder.UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="69ece-271">Define web API-specific routes using `MapWebApiRoute` on the `IRouteBuilder` in the app's `IApplicationBuilder.UseMvc` call.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="69ece-272">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="69ece-272">Additional resources</span></span>

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
::: moniker-end
