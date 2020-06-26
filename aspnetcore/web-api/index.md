---
title: ASP.NET Core ile web API’leri oluşturma
author: scottaddie
description: ASP.NET Core ' de Web API 'SI oluşturmanın temellerini öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/02/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/index
ms.openlocfilehash: 7c9762d23ff612155846357bfadeaaad492c7299
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404736"
---
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="83abe-103">ASP.NET Core ile web API’leri oluşturma</span><span class="sxs-lookup"><span data-stu-id="83abe-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="83abe-104">[Scott Ade](https://github.com/scottaddie) ve [Tom Dykstra](https://github.com/tdykstra) tarafından</span><span class="sxs-lookup"><span data-stu-id="83abe-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="83abe-105">ASP.NET Core, web API'leri olarak da bilinen RESTful hizmetleri oluşturulmasını C# kullanarak desteklemektedir.</span><span class="sxs-lookup"><span data-stu-id="83abe-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="83abe-106">İstekleri işlemek için, bir Web API 'SI denetleyicileri kullanır.</span><span class="sxs-lookup"><span data-stu-id="83abe-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="83abe-107">Bir Web API 'sindeki *denetleyiciler* öğesinden türetilen sınıflardır `ControllerBase` .</span><span class="sxs-lookup"><span data-stu-id="83abe-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="83abe-108">Bu makalede, Web API isteklerini işlemek için denetleyicilerin nasıl kullanılacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="83abe-108">This article shows how to use controllers for handling web API requests.</span></span>

<span data-ttu-id="83abe-109">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span><span class="sxs-lookup"><span data-stu-id="83abe-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="83abe-110">([İndirme](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="83abe-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="83abe-111">ControllerBase sınıfı</span><span class="sxs-lookup"><span data-stu-id="83abe-111">ControllerBase class</span></span>

<span data-ttu-id="83abe-112">Bir Web API 'SI, öğesinden türetilen bir veya daha fazla denetleyici sınıfından oluşur <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="83abe-112">A web API consists of one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="83abe-113">Web API proje şablonu bir başlatıcı denetleyicisi sağlar:</span><span class="sxs-lookup"><span data-stu-id="83abe-113">The web API project template provides a starter controller:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

<span data-ttu-id="83abe-114">Sınıfından türeterek bir Web API denetleyicisi oluşturmayın <xref:Microsoft.AspNetCore.Mvc.Controller> .</span><span class="sxs-lookup"><span data-stu-id="83abe-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> class.</span></span> <span data-ttu-id="83abe-115">`Controller`' dan türetilir `ControllerBase` ve görünümler için destek ekler, bu nedenle Web API istekleri için değil Web sayfalarını işlemeye yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="83abe-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span> <span data-ttu-id="83abe-116">Bu kural için bir özel durum var: aynı denetleyiciyi hem görünümler hem de Web API 'Leri için kullanmayı planlıyorsanız, öğesinden türetirsiniz `Controller` .</span><span class="sxs-lookup"><span data-stu-id="83abe-116">There's an exception to this rule: if you plan to use the same controller for both views and web APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="83abe-117">`ControllerBase`Sınıfı, http isteklerini işlemek için yararlı olan birçok özellik ve yöntem sağlar.</span><span class="sxs-lookup"><span data-stu-id="83abe-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="83abe-118">Örneğin, `ControllerBase.CreatedAtAction` bir 201 durum kodu döndürür:</span><span class="sxs-lookup"><span data-stu-id="83abe-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

<span data-ttu-id="83abe-119">Sağladığı yöntemlere ilişkin bazı örnekler aşağıda verilmiştir `ControllerBase` .</span><span class="sxs-lookup"><span data-stu-id="83abe-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="83abe-120">Yöntem</span><span class="sxs-lookup"><span data-stu-id="83abe-120">Method</span></span>   |<span data-ttu-id="83abe-121">Notlar</span><span class="sxs-lookup"><span data-stu-id="83abe-121">Notes</span></span>    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| <span data-ttu-id="83abe-122">400 durum kodunu döndürür.</span><span class="sxs-lookup"><span data-stu-id="83abe-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|<span data-ttu-id="83abe-123">404 durum kodunu döndürür.</span><span class="sxs-lookup"><span data-stu-id="83abe-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|<span data-ttu-id="83abe-124">Bir dosya döndürür.</span><span class="sxs-lookup"><span data-stu-id="83abe-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|<span data-ttu-id="83abe-125">[Model bağlamasını](xref:mvc/models/model-binding)çağırır.</span><span class="sxs-lookup"><span data-stu-id="83abe-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|<span data-ttu-id="83abe-126">[Model doğrulamasını](xref:mvc/models/validation)çağırır.</span><span class="sxs-lookup"><span data-stu-id="83abe-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="83abe-127">Tüm kullanılabilir yöntemlerin ve özelliklerin listesi için bkz <xref:Microsoft.AspNetCore.Mvc.ControllerBase> ..</span><span class="sxs-lookup"><span data-stu-id="83abe-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="83abe-128">Öznitelikler</span><span class="sxs-lookup"><span data-stu-id="83abe-128">Attributes</span></span>

<span data-ttu-id="83abe-129"><xref:Microsoft.AspNetCore.Mvc>Ad alanı, Web API denetleyicileri ve eylem yöntemlerinin davranışını yapılandırmak için kullanılabilecek öznitelikleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="83abe-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="83abe-130">Aşağıdaki örnek, desteklenen HTTP eylem fiilini ve döndürülebilecek bilinen HTTP durum kodlarını belirtmek için özniteliklerini kullanır:</span><span class="sxs-lookup"><span data-stu-id="83abe-130">The following example uses attributes to specify the supported HTTP action verb and any known HTTP status codes that could be returned:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="83abe-131">Aşağıda, kullanılabilecek özniteliklerin daha fazla örneği verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="83abe-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="83abe-132">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="83abe-132">Attribute</span></span>|<span data-ttu-id="83abe-133">Notlar</span><span class="sxs-lookup"><span data-stu-id="83abe-133">Notes</span></span>|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |<span data-ttu-id="83abe-134">Bir denetleyicinin veya eylemin URL modelini belirtir.</span><span class="sxs-lookup"><span data-stu-id="83abe-134">Specifies URL pattern for a controller or action.</span></span>|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |<span data-ttu-id="83abe-135">Model bağlama için dahil edilecek öneki ve özellikleri belirtir.</span><span class="sxs-lookup"><span data-stu-id="83abe-135">Specifies prefix and properties to include for model binding.</span></span>|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |<span data-ttu-id="83abe-136">HTTP GET ACTION fiilini destekleyen bir eylemi tanımlar.</span><span class="sxs-lookup"><span data-stu-id="83abe-136">Identifies an action that supports the HTTP GET action verb.</span></span>|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|<span data-ttu-id="83abe-137">Bir eylemin kabul ettiği veri türlerini belirtir.</span><span class="sxs-lookup"><span data-stu-id="83abe-137">Specifies data types that an action accepts.</span></span>|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|<span data-ttu-id="83abe-138">Bir eylemin döndürdüğü veri türlerini belirtir.</span><span class="sxs-lookup"><span data-stu-id="83abe-138">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="83abe-139">Kullanılabilir öznitelikleri içeren bir liste için, bkz <xref:Microsoft.AspNetCore.Mvc> . ad alanı.</span><span class="sxs-lookup"><span data-stu-id="83abe-139">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="83abe-140">ApiController özniteliği</span><span class="sxs-lookup"><span data-stu-id="83abe-140">ApiController attribute</span></span>

<span data-ttu-id="83abe-141">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Özniteliği bir denetleyici sınıfına uygulanabilir ve bu, API 'ye özgü aşağıdaki davranışları etkinleştirmek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="83abe-141">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable the following opinionated, API-specific behaviors:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="83abe-142">Öznitelik yönlendirme gereksinimi</span><span class="sxs-lookup"><span data-stu-id="83abe-142">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="83abe-143">Otomatik HTTP 400 yanıtları</span><span class="sxs-lookup"><span data-stu-id="83abe-143">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="83abe-144">Bağlama kaynak parametresi çıkarımı</span><span class="sxs-lookup"><span data-stu-id="83abe-144">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="83abe-145">Multipart/form-veri isteği çıkarımı</span><span class="sxs-lookup"><span data-stu-id="83abe-145">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="83abe-146">Hata durum kodları için sorun ayrıntıları</span><span class="sxs-lookup"><span data-stu-id="83abe-146">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="83abe-147">*Hata durum kodları özelliği Için sorun ayrıntıları* , 2,2 veya üzeri bir [Uyumluluk sürümü](xref:mvc/compatibility-version) gerektirir.</span><span class="sxs-lookup"><span data-stu-id="83abe-147">The *Problem details for error status codes* feature requires a [compatibility version](xref:mvc/compatibility-version) of 2.2 or later.</span></span> <span data-ttu-id="83abe-148">Diğer özellikler, 2,1 veya üzeri bir uyumluluk sürümü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="83abe-148">The other features require a compatibility version of 2.1 or later.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

* [<span data-ttu-id="83abe-149">Öznitelik yönlendirme gereksinimi</span><span class="sxs-lookup"><span data-stu-id="83abe-149">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="83abe-150">Otomatik HTTP 400 yanıtları</span><span class="sxs-lookup"><span data-stu-id="83abe-150">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="83abe-151">Bağlama kaynak parametresi çıkarımı</span><span class="sxs-lookup"><span data-stu-id="83abe-151">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="83abe-152">Multipart/form-veri isteği çıkarımı</span><span class="sxs-lookup"><span data-stu-id="83abe-152">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)

<span data-ttu-id="83abe-153">Bu özellikler, 2,1 veya üzeri bir [Uyumluluk sürümü](xref:mvc/compatibility-version) gerektirir.</span><span class="sxs-lookup"><span data-stu-id="83abe-153">These features require a [compatibility version](xref:mvc/compatibility-version) of 2.1 or later.</span></span>

::: moniker-end

### <a name="attribute-on-specific-controllers"></a><span data-ttu-id="83abe-154">Belirli denetleyicilerde öznitelik</span><span class="sxs-lookup"><span data-stu-id="83abe-154">Attribute on specific controllers</span></span>

<span data-ttu-id="83abe-155">`[ApiController]`Özniteliği, proje şablonundan aşağıdaki örnekte olduğu gibi belirli denetleyicilere uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="83abe-155">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a><span data-ttu-id="83abe-156">Birden çok denetleyicilerde öznitelik</span><span class="sxs-lookup"><span data-stu-id="83abe-156">Attribute on multiple controllers</span></span>

<span data-ttu-id="83abe-157">Özniteliği birden fazla denetleyicide kullanmanın bir yaklaşımı, özniteliğiyle birlikte açıklanan özel bir temel denetleyici sınıfı oluşturmaktır `[ApiController]` .</span><span class="sxs-lookup"><span data-stu-id="83abe-157">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="83abe-158">Aşağıdaki örnekte, özel bir temel sınıf ve ondan türetilen bir denetleyici gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="83abe-158">The following example shows a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a><span data-ttu-id="83abe-159">Bir derlemedeki öznitelik</span><span class="sxs-lookup"><span data-stu-id="83abe-159">Attribute on an assembly</span></span>

<span data-ttu-id="83abe-160">[Uyumluluk sürümü](xref:mvc/compatibility-version) 2,2 veya üzeri bir sürüme ayarlandıysa, `[ApiController]` öznitelik bir derlemeye uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="83abe-160">If [compatibility version](xref:mvc/compatibility-version) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="83abe-161">Bu şekilde ek açıklama, derlemedeki tüm denetleyicilere Web API davranışını uygular.</span><span class="sxs-lookup"><span data-stu-id="83abe-161">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="83abe-162">Tek tek denetleyiciler için geri alma yöntemi yoktur.</span><span class="sxs-lookup"><span data-stu-id="83abe-162">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="83abe-163">Derleme düzeyi özniteliğini sınıfı çevreleyen ad alanı bildirimine uygulayın `Startup` :</span><span class="sxs-lookup"><span data-stu-id="83abe-163">Apply the assembly-level attribute to the namespace declaration surrounding the `Startup` class:</span></span>

```csharp
[assembly: ApiController]
namespace WebApiSample
{
    public class Startup
    {
        ...
    }
}
```

::: moniker-end

## <a name="attribute-routing-requirement"></a><span data-ttu-id="83abe-164">Öznitelik yönlendirme gereksinimi</span><span class="sxs-lookup"><span data-stu-id="83abe-164">Attribute routing requirement</span></span>

<span data-ttu-id="83abe-165">`[ApiController]`Öznitelik, öznitelik yönlendirme bir gereksinim yapar.</span><span class="sxs-lookup"><span data-stu-id="83abe-165">The `[ApiController]` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="83abe-166">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="83abe-166">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

<span data-ttu-id="83abe-167">Eylemlere,, veya içinde tanımlanan [geleneksel yollar](xref:mvc/controllers/routing#conventional-routing) aracılığıyla erişilemez `UseEndpoints` <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="83abe-167">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>, or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

<span data-ttu-id="83abe-168">Eylemlere, veya içinde tanımlanan [geleneksel yollar](xref:mvc/controllers/routing#conventional-routing) aracılığıyla <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> erişilemez <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="83abe-168">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

## <a name="automatic-http-400-responses"></a><span data-ttu-id="83abe-169">Otomatik HTTP 400 yanıtları</span><span class="sxs-lookup"><span data-stu-id="83abe-169">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="83abe-170">`[ApiController]`Öznitelik, model doğrulama hatalarının otomatik olarak BIR HTTP 400 yanıtı tetiklenmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="83abe-170">The `[ApiController]` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="83abe-171">Sonuç olarak, aşağıdaki kod bir eylem yönteminde gereksizdir:</span><span class="sxs-lookup"><span data-stu-id="83abe-171">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

<span data-ttu-id="83abe-172">ASP.NET Core MVC, <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> önceki denetimi yapmak için eylem filtresini kullanır.</span><span class="sxs-lookup"><span data-stu-id="83abe-172">ASP.NET Core MVC uses the <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> action filter to do the preceding check.</span></span>

### <a name="default-badrequest-response"></a><span data-ttu-id="83abe-173">Varsayılan BadRequest yanıtı</span><span class="sxs-lookup"><span data-stu-id="83abe-173">Default BadRequest response</span></span>

<span data-ttu-id="83abe-174">Uyumluluk sürümü 2,1 ile, bir HTTP 400 yanıtı için varsayılan yanıt türü ' dir <xref:Microsoft.AspNetCore.Mvc.SerializableError> .</span><span class="sxs-lookup"><span data-stu-id="83abe-174">With a compatibility version of 2.1, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span></span> <span data-ttu-id="83abe-175">Aşağıdaki istek gövdesi, serileştirilmiş türün bir örneğidir:</span><span class="sxs-lookup"><span data-stu-id="83abe-175">The following request body is an example of the serialized type:</span></span>

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="83abe-176">2,2 veya üzeri bir uyumluluk sürümü ile, bir HTTP 400 yanıtı için varsayılan yanıt türü ' dir <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> .</span><span class="sxs-lookup"><span data-stu-id="83abe-176">With a compatibility version of 2.2 or later, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="83abe-177">Aşağıdaki istek gövdesi, serileştirilmiş türün bir örneğidir:</span><span class="sxs-lookup"><span data-stu-id="83abe-177">The following request body is an example of the serialized type:</span></span>

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.1",
  "title": "One or more validation errors occurred.",
  "status": 400,
  "traceId": "|7fb5e16a-4c8f23bbfc974667.",
  "errors": {
    "": [
      "A non-empty request body is required."
    ]
  }
}
```

<span data-ttu-id="83abe-178">`ValidationProblemDetails`Tür:</span><span class="sxs-lookup"><span data-stu-id="83abe-178">The `ValidationProblemDetails` type:</span></span>

* <span data-ttu-id="83abe-179">Web API yanıtlarında hata belirtmek için makine tarafından okunabilen bir biçim sağlar.</span><span class="sxs-lookup"><span data-stu-id="83abe-179">Provides a machine-readable format for specifying errors in web API responses.</span></span>
* <span data-ttu-id="83abe-180">[RFC 7807 belirtimine](https://tools.ietf.org/html/rfc7807)uyar.</span><span class="sxs-lookup"><span data-stu-id="83abe-180">Complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

::: moniker-end

### <a name="log-automatic-400-responses"></a><span data-ttu-id="83abe-181">Otomatik 400 yanıtlarını günlüğe kaydet</span><span class="sxs-lookup"><span data-stu-id="83abe-181">Log automatic 400 responses</span></span>

<span data-ttu-id="83abe-182">Bkz. [otomatik 400 yanıtlarını model doğrulama hatalarında günlüğe kaydetme (ASPNET/AspNetCore.Docs #12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).</span><span class="sxs-lookup"><span data-stu-id="83abe-182">See [How to log automatic 400 responses on model validation errors (aspnet/AspNetCore.Docs #12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).</span></span>

### <a name="disable-automatic-400-response"></a><span data-ttu-id="83abe-183">Otomatik 400 yanıtını devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="83abe-183">Disable automatic 400 response</span></span>

<span data-ttu-id="83abe-184">Otomatik 400 davranışını devre dışı bırakmak için <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> özelliğini olarak ayarlayın `true` .</span><span class="sxs-lookup"><span data-stu-id="83abe-184">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="83abe-185">Aşağıdaki Vurgulanan kodu içine ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="83abe-185">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="83abe-186">Bağlama kaynak parametresi çıkarımı</span><span class="sxs-lookup"><span data-stu-id="83abe-186">Binding source parameter inference</span></span>

<span data-ttu-id="83abe-187">Bağlama kaynak özniteliği, bir eylem parametresi değerinin bulunduğu konumu tanımlar.</span><span class="sxs-lookup"><span data-stu-id="83abe-187">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="83abe-188">Aşağıdaki bağlama kaynak öznitelikleri var:</span><span class="sxs-lookup"><span data-stu-id="83abe-188">The following binding source attributes exist:</span></span>

|<span data-ttu-id="83abe-189">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="83abe-189">Attribute</span></span>|<span data-ttu-id="83abe-190">Bağlama kaynağı</span><span class="sxs-lookup"><span data-stu-id="83abe-190">Binding source</span></span> |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | <span data-ttu-id="83abe-191">İstek gövdesi</span><span class="sxs-lookup"><span data-stu-id="83abe-191">Request body</span></span> |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | <span data-ttu-id="83abe-192">İstek gövdesinde form verileri</span><span class="sxs-lookup"><span data-stu-id="83abe-192">Form data in the request body</span></span> |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | <span data-ttu-id="83abe-193">İstek üst bilgisi</span><span class="sxs-lookup"><span data-stu-id="83abe-193">Request header</span></span> |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | <span data-ttu-id="83abe-194">İstek sorgusu dize parametresi</span><span class="sxs-lookup"><span data-stu-id="83abe-194">Request query string parameter</span></span> |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | <span data-ttu-id="83abe-195">Geçerli istekten veri yönlendir</span><span class="sxs-lookup"><span data-stu-id="83abe-195">Route data from the current request</span></span> |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | <span data-ttu-id="83abe-196">Eylem parametresi olarak eklenen istek hizmeti</span><span class="sxs-lookup"><span data-stu-id="83abe-196">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="83abe-197">`[FromRoute]`Değerler `%2f` (yani) içerdiğinde kullanmayın `/` .</span><span class="sxs-lookup"><span data-stu-id="83abe-197">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="83abe-198">`%2f`atlanmaz `/` .</span><span class="sxs-lookup"><span data-stu-id="83abe-198">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="83abe-199">`[FromQuery]`Değer içermesi gerekiyorsa kullanın `%2f` .</span><span class="sxs-lookup"><span data-stu-id="83abe-199">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="83abe-200">`[ApiController]`Özniteliği veya gibi bağlama kaynak öznitelikleri olmadan `[FromQuery]` , ASP.NET Core çalışma zamanı karmaşık nesne modeli cildi kullanmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="83abe-200">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="83abe-201">Karmaşık nesne modeli Ciltçi, verileri değer sağlayıcılarından tanımlı bir düzende çeker.</span><span class="sxs-lookup"><span data-stu-id="83abe-201">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="83abe-202">Aşağıdaki örnekte `[FromQuery]` öznitelik, `discontinuedOnly` istek URL 'sinin sorgu dizesinde parametre değerinin sağlandığını gösterir:</span><span class="sxs-lookup"><span data-stu-id="83abe-202">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="83abe-203">`[ApiController]`Öznitelik, eylem parametrelerinin varsayılan veri kaynakları için çıkarım kurallarını uygular.</span><span class="sxs-lookup"><span data-stu-id="83abe-203">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="83abe-204">Bu kurallar, eylem parametrelerine öznitelikleri uygulayarak bağlama kaynaklarını el ile tanımlamak zorunda kalmadan sizi kaydeder.</span><span class="sxs-lookup"><span data-stu-id="83abe-204">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="83abe-205">Bağlama kaynak çıkarımı kuralları aşağıdaki gibi davranır:</span><span class="sxs-lookup"><span data-stu-id="83abe-205">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="83abe-206">`[FromBody]`karmaşık tür parametreleri için algılanır.</span><span class="sxs-lookup"><span data-stu-id="83abe-206">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="83abe-207">Çıkarım kuralı için bir özel durum, `[FromBody]` ve gibi özel bir anlamı olan karmaşık, yerleşik bir türdür <xref:Microsoft.AspNetCore.Http.IFormCollection> <xref:System.Threading.CancellationToken> .</span><span class="sxs-lookup"><span data-stu-id="83abe-207">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="83abe-208">Bağlama kaynak çıkarımı kodu bu özel türleri yoksayar.</span><span class="sxs-lookup"><span data-stu-id="83abe-208">The binding source inference code ignores those special types.</span></span>
* <span data-ttu-id="83abe-209">`[FromForm]`, ve türündeki eylem parametreleri için algılanır <xref:Microsoft.AspNetCore.Http.IFormFile> <xref:Microsoft.AspNetCore.Http.IFormFileCollection> .</span><span class="sxs-lookup"><span data-stu-id="83abe-209">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="83abe-210">Bu, herhangi bir basit veya Kullanıcı tanımlı tür için çıkarsanamıyor.</span><span class="sxs-lookup"><span data-stu-id="83abe-210">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="83abe-211">`[FromRoute]`yol şablonundaki bir parametreyle eşleşen herhangi bir eylem parametresi adı için algılanır.</span><span class="sxs-lookup"><span data-stu-id="83abe-211">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="83abe-212">Birden fazla yol bir eylem parametresiyle eşleştiğinde, herhangi bir rota değeri kabul edilir `[FromRoute]` .</span><span class="sxs-lookup"><span data-stu-id="83abe-212">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="83abe-213">`[FromQuery]`diğer eylem parametreleri için algılanır.</span><span class="sxs-lookup"><span data-stu-id="83abe-213">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="83abe-214">FromBody çıkarım notları</span><span class="sxs-lookup"><span data-stu-id="83abe-214">FromBody inference notes</span></span>

<span data-ttu-id="83abe-215">`[FromBody]`, veya gibi basit türler için çıkarsanamıyor `string` `int` .</span><span class="sxs-lookup"><span data-stu-id="83abe-215">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="83abe-216">Bu nedenle, bu `[FromBody]` işlev gerektiğinde basit türler için özniteliği kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="83abe-216">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="83abe-217">Bir eylem, istek gövdesinden birden fazla parametre bağlamışsa, bir özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="83abe-217">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="83abe-218">Örneğin, aşağıdaki eylem yöntemi imzalarının tümü bir özel duruma neden olur:</span><span class="sxs-lookup"><span data-stu-id="83abe-218">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="83abe-219">`[FromBody]`karmaşık türler olduklarından her ikisi de üzerinde algılanır.</span><span class="sxs-lookup"><span data-stu-id="83abe-219">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="83abe-220">`[FromBody]`karmaşık bir tür olduğundan, diğeri üzerinde olan özniteliği.</span><span class="sxs-lookup"><span data-stu-id="83abe-220">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="83abe-221">`[FromBody]`her ikisinde de özniteliği.</span><span class="sxs-lookup"><span data-stu-id="83abe-221">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> <span data-ttu-id="83abe-222">ASP.NET Core 2,1 ' de, listeler ve diziler gibi koleksiyon türü parametreleri yanlış olarak algılanır `[FromQuery]` .</span><span class="sxs-lookup"><span data-stu-id="83abe-222">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="83abe-223">`[FromBody]`Öznitelik, istek gövdesinden bağlanmaları durumunda bu parametreler için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="83abe-223">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="83abe-224">Bu davranış ASP.NET Core 2,2 veya sonraki bir sürümde düzeltilir. burada, koleksiyon türü parametrelerinin varsayılan olarak gövdeden bağlanacak şekilde çıkarsandır.</span><span class="sxs-lookup"><span data-stu-id="83abe-224">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

::: moniker-end

### <a name="disable-inference-rules"></a><span data-ttu-id="83abe-225">Çıkarım kurallarını devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="83abe-225">Disable inference rules</span></span>

<span data-ttu-id="83abe-226">Bağlama kaynak çıkarımını devre dışı bırakmak için, <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> olarak ayarlayın `true` .</span><span class="sxs-lookup"><span data-stu-id="83abe-226">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="83abe-227">Aşağıdaki kodu içine ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="83abe-227">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="83abe-228">Multipart/form-veri isteği çıkarımı</span><span class="sxs-lookup"><span data-stu-id="83abe-228">Multipart/form-data request inference</span></span>

<span data-ttu-id="83abe-229">Özniteliği `[ApiController]` ile bir eylem parametresine ek açıklama eklendiğinde öznitelik bir çıkarım kuralı uygular [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) .</span><span class="sxs-lookup"><span data-stu-id="83abe-229">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute.</span></span> <span data-ttu-id="83abe-230">`multipart/form-data`İstek içerik türü çıkarsandı.</span><span class="sxs-lookup"><span data-stu-id="83abe-230">The `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="83abe-231">Varsayılan davranışı devre dışı bırakmak için <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> özelliğini `true` olarak ayarlayın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="83abe-231">To disable the default behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property to `true` in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,4)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,3)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="83abe-232">Hata durum kodları için sorun ayrıntıları</span><span class="sxs-lookup"><span data-stu-id="83abe-232">Problem details for error status codes</span></span>

<span data-ttu-id="83abe-233">Uyumluluk sürümü 2,2 veya üzeri olduğunda, MVC bir hata sonucunu (durum kodu 400 veya üzeri olan bir sonuç) ile bir sonuçla dönüştürür <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> .</span><span class="sxs-lookup"><span data-stu-id="83abe-233">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="83abe-234">`ProblemDetails`Türü, BIR http yanıtında makine tarafından okunabilen hata ayrıntılarını sağlamak Için [RFC 7807 belirtimine](https://tools.ietf.org/html/rfc7807) dayalıdır.</span><span class="sxs-lookup"><span data-stu-id="83abe-234">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="83abe-235">Bir denetleyici eyleminde aşağıdaki kodu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="83abe-235">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="83abe-236">`NotFound`Yöntemi, gövdesi olan BIR HTTP 404 durum kodu üretir `ProblemDetails` .</span><span class="sxs-lookup"><span data-stu-id="83abe-236">The `NotFound` method produces an HTTP 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="83abe-237">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="83abe-237">For example:</span></span>

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a><span data-ttu-id="83abe-238">ProblemDetails yanıtını devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="83abe-238">Disable ProblemDetails response</span></span>

<span data-ttu-id="83abe-239">`ProblemDetails`Özelliği olarak ayarlandığında hata durumu kodlarının otomatik olarak oluşturulması devre dışı bırakılır <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> `true` .</span><span class="sxs-lookup"><span data-stu-id="83abe-239">The automatic creation of a `ProblemDetails` for error status codes is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> property is set to `true`.</span></span> <span data-ttu-id="83abe-240">Aşağıdaki kodu içine ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="83abe-240">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

<a name="consumes"></a>

## <a name="define-supported-request-content-types-with-the-consumes-attribute"></a><span data-ttu-id="83abe-241">Desteklenen istek içerik türlerini [tüketir] özniteliğiyle tanımlayın</span><span class="sxs-lookup"><span data-stu-id="83abe-241">Define supported request content types with the [Consumes] attribute</span></span>

<span data-ttu-id="83abe-242">Varsayılan olarak, bir eylem tüm kullanılabilir istek içerik türlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="83abe-242">By default, an action supports all available request content types.</span></span> <span data-ttu-id="83abe-243">Örneğin, bir uygulama hem JSON hem de XML [giriş formatlarını](xref:mvc/models/model-binding#input-formatters)destekleyecek şekilde yapılandırıldıysa, bir eylem, ve dahil olmak üzere birden çok içerik türünü destekler `application/json` `application/xml` .</span><span class="sxs-lookup"><span data-stu-id="83abe-243">For example, if an app is configured to support both JSON and XML [input formatters](xref:mvc/models/model-binding#input-formatters), an action supports multiple content types, including `application/json` and `application/xml`.</span></span>

<span data-ttu-id="83abe-244">[[Tüketir]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) özniteliği, desteklenen istek içerik türlerini sınırlama eylemi sağlar.</span><span class="sxs-lookup"><span data-stu-id="83abe-244">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="83abe-245">Özniteliği bir `[Consumes]` eylem veya denetleyiciye uygulayarak bir veya daha fazla içerik türü belirtin:</span><span class="sxs-lookup"><span data-stu-id="83abe-245">Apply the `[Consumes]` attribute to an action or controller, specifying one or more content types:</span></span>

```csharp
[HttpPost]
[Consumes("application/xml")]
public IActionResult CreateProduct(Product product)
```

<span data-ttu-id="83abe-246">Yukarıdaki kodda, `CreateProduct` eylem içerik türünü belirtir `application/xml` .</span><span class="sxs-lookup"><span data-stu-id="83abe-246">In the preceding code, the `CreateProduct` action specifies the content type `application/xml`.</span></span> <span data-ttu-id="83abe-247">Bu eyleme yönlendirilen isteklerin bir `Content-Type` üst bilgisi belirtmesi gerekir `application/xml` .</span><span class="sxs-lookup"><span data-stu-id="83abe-247">Requests routed to this action must specify a `Content-Type` header of `application/xml`.</span></span> <span data-ttu-id="83abe-248">`Content-Type`Üst bilgisi belirtmeyen istekler, `application/xml` [415 desteklenmeyen medya türü](https://developer.mozilla.org/docs/Web/HTTP/Status/415) yanıtı ile sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="83abe-248">Requests that don't specify a `Content-Type` header of `application/xml` result in a [415 Unsupported Media Type](https://developer.mozilla.org/docs/Web/HTTP/Status/415) response.</span></span>

<span data-ttu-id="83abe-249">`[Consumes]`Öznitelik Ayrıca bir eylemin bir tür kısıtlaması uygulayarak bir gelen isteğin içerik türüne göre seçimini etkilemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="83abe-249">The `[Consumes]` attribute also allows an action to influence its selection based on an incoming request's content type by applying a type constraint.</span></span> <span data-ttu-id="83abe-250">Aşağıdaki örneği inceleyin:</span><span class="sxs-lookup"><span data-stu-id="83abe-250">Consider the following example:</span></span>

[!code-csharp[](index/samples/3.x/Controllers/ConsumesController.cs?name=snippet_Class)]

<span data-ttu-id="83abe-251">Önceki kodda, `ConsumesController` URL 'ye gönderilen istekleri işleyecek şekilde yapılandırılır `https://localhost:5001/api/Consumes` .</span><span class="sxs-lookup"><span data-stu-id="83abe-251">In the preceding code, `ConsumesController` is configured to handle requests sent to the `https://localhost:5001/api/Consumes` URL.</span></span> <span data-ttu-id="83abe-252">Denetleyicinin eylemlerinin her ikisi de `PostJson` `PostForm` aynı URL 'ye sahip post isteklerini işler.</span><span class="sxs-lookup"><span data-stu-id="83abe-252">Both of the controller's actions, `PostJson` and `PostForm`, handle POST requests with the same URL.</span></span> <span data-ttu-id="83abe-253">`[Consumes]`Öznitelik bir tür kısıtlaması uygulamadan, belirsiz eşleşme özel durumu oluşur.</span><span class="sxs-lookup"><span data-stu-id="83abe-253">Without the `[Consumes]` attribute applying a type constraint, an ambiguous match exception is thrown.</span></span>

<span data-ttu-id="83abe-254">`[Consumes]`Özniteliği her iki eyleme de uygulanır.</span><span class="sxs-lookup"><span data-stu-id="83abe-254">The `[Consumes]` attribute is applied to both actions.</span></span> <span data-ttu-id="83abe-255">`PostJson`Eylem, üst bilgisi ile gönderilen istekleri işler `Content-Type` `application/json` .</span><span class="sxs-lookup"><span data-stu-id="83abe-255">The `PostJson` action handles requests sent with a `Content-Type` header of `application/json`.</span></span> <span data-ttu-id="83abe-256">`PostForm`Eylem, üst bilgisi ile gönderilen istekleri işler `Content-Type` `application/x-www-form-urlencoded` .</span><span class="sxs-lookup"><span data-stu-id="83abe-256">The `PostForm` action handles requests sent with a `Content-Type` header of `application/x-www-form-urlencoded`.</span></span> 

## <a name="additional-resources"></a><span data-ttu-id="83abe-257">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="83abe-257">Additional resources</span></span>

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
