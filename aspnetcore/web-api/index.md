---
title: ASP.NET Core ile web API'leri oluşturma
author: scottaddie
description: ASP.NET Core'da web API oluşturmanın temellerini öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: scaddie
ms.custom: mvc
ms.date: 02/02/2020
uid: web-api/index
ms.openlocfilehash: be88b8d58f1f660f3a815c395c210c05a7b4917c
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666007"
---
# <a name="create-web-apis-with-aspnet-core"></a><span data-ttu-id="5f2e1-103">ASP.NET Core ile web API'leri oluşturma</span><span class="sxs-lookup"><span data-stu-id="5f2e1-103">Create web APIs with ASP.NET Core</span></span>

<span data-ttu-id="5f2e1-104">Scott [Addie](https://github.com/scottaddie) ve [Tom Dykstra](https://github.com/tdykstra) tarafından</span><span class="sxs-lookup"><span data-stu-id="5f2e1-104">By [Scott Addie](https://github.com/scottaddie) and [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="5f2e1-105">ASP.NET Core, web API'leri olarak da bilinen RESTful hizmetleri oluşturulmasını C# kullanarak desteklemektedir.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-105">ASP.NET Core supports creating RESTful services, also known as web APIs, using C#.</span></span> <span data-ttu-id="5f2e1-106">İstekleri işlemek için web API denetleyicileri kullanır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-106">To handle requests, a web API uses controllers.</span></span> <span data-ttu-id="5f2e1-107">Web API'sindeki *denetleyiciler,* `ControllerBase`'den türeyen sınıflardır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-107">*Controllers* in a web API are classes that derive from `ControllerBase`.</span></span> <span data-ttu-id="5f2e1-108">Bu makalede, web API isteklerini işlemek için denetleyicileri nasıl kullanılacağı nı gösterir.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-108">This article shows how to use controllers for handling web API requests.</span></span>

<span data-ttu-id="5f2e1-109">[Örnek kodu görüntüleyin veya indirin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples)</span><span class="sxs-lookup"><span data-stu-id="5f2e1-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/index/samples).</span></span> <span data-ttu-id="5f2e1-110">([Nasıl indirilir).](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="5f2e1-110">([How to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="controllerbase-class"></a><span data-ttu-id="5f2e1-111">ControllerBase sınıfı</span><span class="sxs-lookup"><span data-stu-id="5f2e1-111">ControllerBase class</span></span>

<span data-ttu-id="5f2e1-112">Web API'si, bir veya daha <xref:Microsoft.AspNetCore.Mvc.ControllerBase>fazla denetleyici sınıftan oluşur.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-112">A web API consists of one or more controller classes that derive from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="5f2e1-113">Web API proje şablonu bir başlatıcı denetleyicisi sağlar:</span><span class="sxs-lookup"><span data-stu-id="5f2e1-113">The web API project template provides a starter controller:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=3)]

::: moniker-end

<span data-ttu-id="5f2e1-114"><xref:Microsoft.AspNetCore.Mvc.Controller> Sınıftan çıkararak bir web API denetleyicisi oluşturmayın.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-114">Don't create a web API controller by deriving from the <xref:Microsoft.AspNetCore.Mvc.Controller> class.</span></span> <span data-ttu-id="5f2e1-115">`Controller`görünümlerden kaynaklanır `ControllerBase` ve görünümler için destek ekler, bu nedenle web API isteklerini değil, web sayfalarını işlemek içindir.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-115">`Controller` derives from `ControllerBase` and adds support for views, so it's for handling web pages, not web API requests.</span></span> <span data-ttu-id="5f2e1-116">Bu kuralın bir istisnası vardır: Hem görünümler hem de web API'leri `Controller`için aynı denetleyiciyi kullanmayı planlıyorsanız, bunu .</span><span class="sxs-lookup"><span data-stu-id="5f2e1-116">There's an exception to this rule: if you plan to use the same controller for both views and web APIs, derive it from `Controller`.</span></span>

<span data-ttu-id="5f2e1-117">Sınıf, `ControllerBase` HTTP isteklerini işlemek için yararlı olan birçok özellik ve yöntem sağlar.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-117">The `ControllerBase` class provides many properties and methods that are useful for handling HTTP requests.</span></span> <span data-ttu-id="5f2e1-118">Örneğin, `ControllerBase.CreatedAtAction` 201 durum kodunu döndürür:</span><span class="sxs-lookup"><span data-stu-id="5f2e1-118">For example, `ControllerBase.CreatedAtAction` returns a 201 status code:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=10)]

<span data-ttu-id="5f2e1-119">Aşağıda, `ControllerBase` sağlayan yöntemlere birkaç örnek daha verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-119">Here are some more examples of methods that `ControllerBase` provides.</span></span>

|<span data-ttu-id="5f2e1-120">Yöntem</span><span class="sxs-lookup"><span data-stu-id="5f2e1-120">Method</span></span>   |<span data-ttu-id="5f2e1-121">Notlar</span><span class="sxs-lookup"><span data-stu-id="5f2e1-121">Notes</span></span>    |
|---------|---------|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest%2A>| <span data-ttu-id="5f2e1-122">400 durum kodunu döndürür.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-122">Returns 400 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound%2A>|<span data-ttu-id="5f2e1-123">404 durum kodunu döndürür.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-123">Returns 404 status code.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.PhysicalFile%2A>|<span data-ttu-id="5f2e1-124">Bir dosyayı döndürür.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-124">Returns a file.</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync%2A>|<span data-ttu-id="5f2e1-125">Model [bağlamayı](xref:mvc/models/model-binding)çağırır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-125">Invokes [model binding](xref:mvc/models/model-binding).</span></span>|
|<xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryValidateModel%2A>|<span data-ttu-id="5f2e1-126">Model [doğrulaması](xref:mvc/models/validation)çağırır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-126">Invokes [model validation](xref:mvc/models/validation).</span></span>|

<span data-ttu-id="5f2e1-127">Kullanılabilir tüm yöntem ve özelliklerin <xref:Microsoft.AspNetCore.Mvc.ControllerBase>listesi için bkz.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-127">For a list of all available methods and properties, see <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span>

## <a name="attributes"></a><span data-ttu-id="5f2e1-128">Öznitelikler</span><span class="sxs-lookup"><span data-stu-id="5f2e1-128">Attributes</span></span>

<span data-ttu-id="5f2e1-129">Ad <xref:Microsoft.AspNetCore.Mvc> alanı, web API denetleyicilerinin ve eylem yöntemlerinin davranışını yapılandırmak için kullanılabilecek öznitelikler sağlar.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-129">The <xref:Microsoft.AspNetCore.Mvc> namespace provides attributes that can be used to configure the behavior of web API controllers and action methods.</span></span> <span data-ttu-id="5f2e1-130">Aşağıdaki örnek, desteklenen HTTP eylem fiilini ve döndürülebilecek bilinen herhangi bir HTTP durum kodlarını belirtmek için öznitelikleri kullanır:</span><span class="sxs-lookup"><span data-stu-id="5f2e1-130">The following example uses attributes to specify the supported HTTP action verb and any known HTTP status codes that could be returned:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_400And201&highlight=1-3)]

<span data-ttu-id="5f2e1-131">Burada kullanılabilir özniteliklere birkaç daha fazla örnek verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-131">Here are some more examples of attributes that are available.</span></span>

|<span data-ttu-id="5f2e1-132">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="5f2e1-132">Attribute</span></span>|<span data-ttu-id="5f2e1-133">Notlar</span><span class="sxs-lookup"><span data-stu-id="5f2e1-133">Notes</span></span>|
|---------|-----|
|[`[Route]`](<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>)      |<span data-ttu-id="5f2e1-134">Denetleyici veya eylem için URL deseni belirtir.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-134">Specifies URL pattern for a controller or action.</span></span>|
|[`[Bind]`](<xref:Microsoft.AspNetCore.Mvc.BindAttribute>)        |<span data-ttu-id="5f2e1-135">Model bağlama için ekecek önek ve özellikler belirtir.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-135">Specifies prefix and properties to include for model binding.</span></span>|
|[`[HttpGet]`](<xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute>)  |<span data-ttu-id="5f2e1-136">HTTP GET eylem fiilini destekleyen bir eylemi tanımlar.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-136">Identifies an action that supports the HTTP GET action verb.</span></span>|
|[`[Consumes]`](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>)|<span data-ttu-id="5f2e1-137">Bir eylemin kabul ettiği veri türlerini belirtir.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-137">Specifies data types that an action accepts.</span></span>|
|[`[Produces]`](<xref:Microsoft.AspNetCore.Mvc.ProducesAttribute>)|<span data-ttu-id="5f2e1-138">Bir eylemin döndürdüğü veri düşüntülerini</span><span class="sxs-lookup"><span data-stu-id="5f2e1-138">Specifies data types that an action returns.</span></span>|

<span data-ttu-id="5f2e1-139">Kullanılabilir öznitelikleri içeren bir liste <xref:Microsoft.AspNetCore.Mvc> için ad alanına bakın.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-139">For a list that includes the available attributes, see the <xref:Microsoft.AspNetCore.Mvc> namespace.</span></span>

## <a name="apicontroller-attribute"></a><span data-ttu-id="5f2e1-140">ApiController özniteliği</span><span class="sxs-lookup"><span data-stu-id="5f2e1-140">ApiController attribute</span></span>

<span data-ttu-id="5f2e1-141">Öznitelik, [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) aşağıdaki göreli, API'ye özgü davranışları etkinleştirmek için bir denetleyici sınıfına uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="5f2e1-141">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute can be applied to a controller class to enable the following opinionated, API-specific behaviors:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="5f2e1-142">Öznitelik yönlendirme gereksinimi</span><span class="sxs-lookup"><span data-stu-id="5f2e1-142">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="5f2e1-143">Otomatik HTTP 400 yanıtları</span><span class="sxs-lookup"><span data-stu-id="5f2e1-143">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="5f2e1-144">Bağlayıcı kaynak parametresi çıkarımı</span><span class="sxs-lookup"><span data-stu-id="5f2e1-144">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="5f2e1-145">Çoklu bölüm/form-veri isteği çıkarımı</span><span class="sxs-lookup"><span data-stu-id="5f2e1-145">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)
* [<span data-ttu-id="5f2e1-146">Hata durum kodları için sorun ayrıntıları</span><span class="sxs-lookup"><span data-stu-id="5f2e1-146">Problem details for error status codes</span></span>](#problem-details-for-error-status-codes)

<span data-ttu-id="5f2e1-147">Hata durumu kodları özelliği *için Sorun ayrıntıları* 2.2 veya daha sonra bir uyumluluk [sürümü](xref:mvc/compatibility-version) gerektirir.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-147">The *Problem details for error status codes* feature requires a [compatibility version](xref:mvc/compatibility-version) of 2.2 or later.</span></span> <span data-ttu-id="5f2e1-148">Diğer özellikler 2.1 veya sonraki bir uyumluluk sürümü gerektirir.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-148">The other features require a compatibility version of 2.1 or later.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

* [<span data-ttu-id="5f2e1-149">Öznitelik yönlendirme gereksinimi</span><span class="sxs-lookup"><span data-stu-id="5f2e1-149">Attribute routing requirement</span></span>](#attribute-routing-requirement)
* [<span data-ttu-id="5f2e1-150">Otomatik HTTP 400 yanıtları</span><span class="sxs-lookup"><span data-stu-id="5f2e1-150">Automatic HTTP 400 responses</span></span>](#automatic-http-400-responses)
* [<span data-ttu-id="5f2e1-151">Bağlayıcı kaynak parametresi çıkarımı</span><span class="sxs-lookup"><span data-stu-id="5f2e1-151">Binding source parameter inference</span></span>](#binding-source-parameter-inference)
* [<span data-ttu-id="5f2e1-152">Çoklu bölüm/form-veri isteği çıkarımı</span><span class="sxs-lookup"><span data-stu-id="5f2e1-152">Multipart/form-data request inference</span></span>](#multipartform-data-request-inference)

<span data-ttu-id="5f2e1-153">Bu özellikler 2.1 veya sonraki bir [uyumluluk sürümü](xref:mvc/compatibility-version) gerektirir.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-153">These features require a [compatibility version](xref:mvc/compatibility-version) of 2.1 or later.</span></span>

::: moniker-end

### <a name="attribute-on-specific-controllers"></a><span data-ttu-id="5f2e1-154">Belirli denetleyicilere öznitelik</span><span class="sxs-lookup"><span data-stu-id="5f2e1-154">Attribute on specific controllers</span></span>

<span data-ttu-id="5f2e1-155">Öznitelik, `[ApiController]` proje şablonundaki aşağıdaki örnekte olduğu gibi belirli denetleyicilere uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="5f2e1-155">The `[ApiController]` attribute can be applied to specific controllers, as in the following example from the project template:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2])]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=2)]

::: moniker-end

### <a name="attribute-on-multiple-controllers"></a><span data-ttu-id="5f2e1-156">Birden çok denetleyiciye öznitelik</span><span class="sxs-lookup"><span data-stu-id="5f2e1-156">Attribute on multiple controllers</span></span>

<span data-ttu-id="5f2e1-157">Özniteliği birden fazla denetleyicide kullanmaya yönelik bir yaklaşım, öznitelik ile `[ApiController]` açıklamalı özel bir taban denetleyici sınıfı oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-157">One approach to using the attribute on more than one controller is to create a custom base controller class annotated with the `[ApiController]` attribute.</span></span> <span data-ttu-id="5f2e1-158">Aşağıdaki örnek, özel bir taban sınıf ve ondan türetilen bir denetleyici gösterir:</span><span class="sxs-lookup"><span data-stu-id="5f2e1-158">The following example shows a custom base class and a controller that derives from it:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/MyControllerBase.cs?name=snippet_MyControllerBase)]

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_Inherit)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="attribute-on-an-assembly"></a><span data-ttu-id="5f2e1-159">Montajda öznitelik</span><span class="sxs-lookup"><span data-stu-id="5f2e1-159">Attribute on an assembly</span></span>

<span data-ttu-id="5f2e1-160">[Uyumluluk sürümü](xref:mvc/compatibility-version) 2.2 veya daha sonra `[ApiController]` olarak ayarlanırsa, öznitelik bir derlemeye uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-160">If [compatibility version](xref:mvc/compatibility-version) is set to 2.2 or later, the `[ApiController]` attribute can be applied to an assembly.</span></span> <span data-ttu-id="5f2e1-161">Bu şekilde ek açıklama, derlemedeki tüm denetleyicilere web API davranışını uygular.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-161">Annotation in this manner applies web API behavior to all controllers in the assembly.</span></span> <span data-ttu-id="5f2e1-162">Tek tek denetleyicileri devre dışı bırakmanın bir yolu yok.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-162">There's no way to opt out for individual controllers.</span></span> <span data-ttu-id="5f2e1-163">Sınıfı çevreleyen ad alanı bildirimine derleme düzeyinde `Startup` öznitelik uygulayın:</span><span class="sxs-lookup"><span data-stu-id="5f2e1-163">Apply the assembly-level attribute to the namespace declaration surrounding the `Startup` class:</span></span>

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

## <a name="attribute-routing-requirement"></a><span data-ttu-id="5f2e1-164">Öznitelik yönlendirme gereksinimi</span><span class="sxs-lookup"><span data-stu-id="5f2e1-164">Attribute routing requirement</span></span>

<span data-ttu-id="5f2e1-165">Öznitelik `[ApiController]` öznitelik yönlendirme bir gereksinim i</span><span class="sxs-lookup"><span data-stu-id="5f2e1-165">The `[ApiController]` attribute makes attribute routing a requirement.</span></span> <span data-ttu-id="5f2e1-166">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="5f2e1-166">For example:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_ControllerSignature&highlight=2)]

<span data-ttu-id="5f2e1-167">[conventional routes](xref:mvc/controllers/routing#conventional-routing) Eylemlere `UseEndpoints`, ' <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>veya <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> . `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="5f2e1-167">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by `UseEndpoints`, <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A>, or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Controllers/ValuesController.cs?name=snippet_ControllerSignature&highlight=1)]

<span data-ttu-id="5f2e1-168">Eylemlere, tarafından <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> tanımlanan geleneksel <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> [yollarla](xref:mvc/controllers/routing#conventional-routing) veya `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="5f2e1-168">Actions are inaccessible via [conventional routes](xref:mvc/controllers/routing#conventional-routing) defined by <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc%2A> or <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute%2A> in `Startup.Configure`.</span></span>

::: moniker-end

## <a name="automatic-http-400-responses"></a><span data-ttu-id="5f2e1-169">Otomatik HTTP 400 yanıtları</span><span class="sxs-lookup"><span data-stu-id="5f2e1-169">Automatic HTTP 400 responses</span></span>

<span data-ttu-id="5f2e1-170">Öznitelik, model doğrulama hatalarının `[ApiController]` otomatik olarak bir HTTP 400 yanıtını tetikletir.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-170">The `[ApiController]` attribute makes model validation errors automatically trigger an HTTP 400 response.</span></span> <span data-ttu-id="5f2e1-171">Sonuç olarak, bir eylem yönteminde aşağıdaki kod gereksizdir:</span><span class="sxs-lookup"><span data-stu-id="5f2e1-171">Consequently, the following code is unnecessary in an action method:</span></span>

```csharp
if (!ModelState.IsValid)
{
    return BadRequest(ModelState);
}
```

<span data-ttu-id="5f2e1-172">ASP.NET Core MVC <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> önceki denetimi yapmak için eylem filtresini kullanır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-172">ASP.NET Core MVC uses the <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ModelStateInvalidFilter> action filter to do the preceding check.</span></span>

### <a name="default-badrequest-response"></a><span data-ttu-id="5f2e1-173">Varsayılan BadRequest yanıtı</span><span class="sxs-lookup"><span data-stu-id="5f2e1-173">Default BadRequest response</span></span>

<span data-ttu-id="5f2e1-174">2.1 uyumluluk sürümüile, bir HTTP 400 yanıtı için <xref:Microsoft.AspNetCore.Mvc.SerializableError>varsayılan yanıt türü .</span><span class="sxs-lookup"><span data-stu-id="5f2e1-174">With a compatibility version of 2.1, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.SerializableError>.</span></span> <span data-ttu-id="5f2e1-175">Aşağıdaki istek gövdesi serileştirilmiş türe örnektir:</span><span class="sxs-lookup"><span data-stu-id="5f2e1-175">The following request body is an example of the serialized type:</span></span>

```json
{
  "": [
    "A non-empty request body is required."
  ]
}
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="5f2e1-176">2.2 veya sonraki uyumluluk sürümünde, HTTP 400 yanıtı için <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>varsayılan yanıt türü .</span><span class="sxs-lookup"><span data-stu-id="5f2e1-176">With a compatibility version of 2.2 or later, the default response type for an HTTP 400 response is <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="5f2e1-177">Aşağıdaki istek gövdesi serileştirilmiş türe örnektir:</span><span class="sxs-lookup"><span data-stu-id="5f2e1-177">The following request body is an example of the serialized type:</span></span>

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

<span data-ttu-id="5f2e1-178">Türü: `ValidationProblemDetails`</span><span class="sxs-lookup"><span data-stu-id="5f2e1-178">The `ValidationProblemDetails` type:</span></span>

* <span data-ttu-id="5f2e1-179">Web API yanıtlarında hataları belirtmek için makine tarafından okunabilir bir biçim sağlar.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-179">Provides a machine-readable format for specifying errors in web API responses.</span></span>
* <span data-ttu-id="5f2e1-180">[RFC 7807 belirtimine](https://tools.ietf.org/html/rfc7807)uygundur.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-180">Complies with the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807).</span></span>

::: moniker-end

### <a name="log-automatic-400-responses"></a><span data-ttu-id="5f2e1-181">Günlük otomatik 400 yanıt</span><span class="sxs-lookup"><span data-stu-id="5f2e1-181">Log automatic 400 responses</span></span>

<span data-ttu-id="5f2e1-182">[Model doğrulama hataları (aspnet/AspNetCore.Docs #12157) üzerinde otomatik 400 yanıtları nasıl günlüğe kaydedeceğimi](https://github.com/dotnet/AspNetCore.Docs/issues/12157)görün.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-182">See [How to log automatic 400 responses on model validation errors (aspnet/AspNetCore.Docs #12157)](https://github.com/dotnet/AspNetCore.Docs/issues/12157).</span></span>

### <a name="disable-automatic-400-response"></a><span data-ttu-id="5f2e1-183">Otomatik 400 yanıtını devre dışı</span><span class="sxs-lookup"><span data-stu-id="5f2e1-183">Disable automatic 400 response</span></span>

<span data-ttu-id="5f2e1-184">Otomatik 400 davranışını devre dışı <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> bırakabilmek için `true`özelliği ' ye göre ayarla</span><span class="sxs-lookup"><span data-stu-id="5f2e1-184">To disable the automatic 400 behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressModelStateInvalidFilter> property to `true`.</span></span> <span data-ttu-id="5f2e1-185">Aşağıdaki vurgulanan kodu `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5f2e1-185">Add the following highlighted code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,5)]

::: moniker-end

## <a name="binding-source-parameter-inference"></a><span data-ttu-id="5f2e1-186">Bağlayıcı kaynak parametresi çıkarımı</span><span class="sxs-lookup"><span data-stu-id="5f2e1-186">Binding source parameter inference</span></span>

<span data-ttu-id="5f2e1-187">Bağlayıcı kaynak özniteliği, eylem parametresi değerinin bulunduğu konumu tanımlar.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-187">A binding source attribute defines the location at which an action parameter's value is found.</span></span> <span data-ttu-id="5f2e1-188">Aşağıdaki bağlayıcı kaynak öznitelikleri vardır:</span><span class="sxs-lookup"><span data-stu-id="5f2e1-188">The following binding source attributes exist:</span></span>

|<span data-ttu-id="5f2e1-189">Öznitelik</span><span class="sxs-lookup"><span data-stu-id="5f2e1-189">Attribute</span></span>|<span data-ttu-id="5f2e1-190">Kaynak bağlama</span><span class="sxs-lookup"><span data-stu-id="5f2e1-190">Binding source</span></span> |
|---------|---------|
|[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)     | <span data-ttu-id="5f2e1-191">İstek gövdesi</span><span class="sxs-lookup"><span data-stu-id="5f2e1-191">Request body</span></span> |
|[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)     | <span data-ttu-id="5f2e1-192">İstek gövdesindeki form verileri</span><span class="sxs-lookup"><span data-stu-id="5f2e1-192">Form data in the request body</span></span> |
|[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) | <span data-ttu-id="5f2e1-193">İstek üst bilgisi</span><span class="sxs-lookup"><span data-stu-id="5f2e1-193">Request header</span></span> |
|[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)   | <span data-ttu-id="5f2e1-194">İstek sorgusu dize parametresi</span><span class="sxs-lookup"><span data-stu-id="5f2e1-194">Request query string parameter</span></span> |
|[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)   | <span data-ttu-id="5f2e1-195">Geçerli istekteki verileri yönlendirme</span><span class="sxs-lookup"><span data-stu-id="5f2e1-195">Route data from the current request</span></span> |
|[`[FromServices]`](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) | <span data-ttu-id="5f2e1-196">Eylem parametresi olarak enjekte edilen istek hizmeti</span><span class="sxs-lookup"><span data-stu-id="5f2e1-196">The request service injected as an action parameter</span></span> |

> [!WARNING]
> <span data-ttu-id="5f2e1-197">Değerler içerebileceğinde `[FromRoute]` `%2f` `/`(yani) kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-197">Don't use `[FromRoute]` when values might contain `%2f` (that is `/`).</span></span> <span data-ttu-id="5f2e1-198">`%2f`kaçmadan `/`olmayacaktır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-198">`%2f` won't be unescaped to `/`.</span></span> <span data-ttu-id="5f2e1-199">Değer `[FromQuery]` içeriyorsa `%2f`kullanın.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-199">Use `[FromQuery]` if the value might contain `%2f`.</span></span>

<span data-ttu-id="5f2e1-200">Gibi `[ApiController]` öznitelik veya bağlayıcı `[FromQuery]`kaynak öznitelikleri olmadan, ASP.NET Core çalışma zamanı karmaşık nesne modeli bağlayıcısı kullanmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-200">Without the `[ApiController]` attribute or binding source attributes like `[FromQuery]`, the ASP.NET Core runtime attempts to use the complex object model binder.</span></span> <span data-ttu-id="5f2e1-201">Karmaşık nesne modeli bağlayıcısı, tanımlı bir sırada değer sağlayıcılardan veri çeker.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-201">The complex object model binder pulls data from value providers in a defined order.</span></span>

<span data-ttu-id="5f2e1-202">Aşağıdaki örnekte, `[FromQuery]` `discontinuedOnly` öznitelik, parametre değerinin istek URL'sinin sorgu dizesinde sağlandığını gösterir:</span><span class="sxs-lookup"><span data-stu-id="5f2e1-202">In the following example, the `[FromQuery]` attribute indicates that the `discontinuedOnly` parameter value is provided in the request URL's query string:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/ProductsController.cs?name=snippet_BindingSourceAttributes&highlight=3)]

<span data-ttu-id="5f2e1-203">Öznitelik, `[ApiController]` eylem parametrelerinin varsayılan veri kaynakları için çıkarım kuralları uygular.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-203">The `[ApiController]` attribute applies inference rules for the default data sources of action parameters.</span></span> <span data-ttu-id="5f2e1-204">Bu kurallar, eylem parametrelerine öznitelikleri uygulayarak bağlayıcı kaynakları el ile tanımlamak zorunda kalmanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-204">These rules save you from having to identify binding sources manually by applying attributes to the action parameters.</span></span> <span data-ttu-id="5f2e1-205">Bağlayıcı kaynak çıkarım kuralları aşağıdaki gibi hareket eder:</span><span class="sxs-lookup"><span data-stu-id="5f2e1-205">The binding source inference rules behave as follows:</span></span>

* <span data-ttu-id="5f2e1-206">`[FromBody]`karmaşık tür parametreleri için çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-206">`[FromBody]` is inferred for complex type parameters.</span></span> <span data-ttu-id="5f2e1-207">Çıkarım kuralının `[FromBody]` bir istisnası, özel bir anlamı olan karmaşık, <xref:Microsoft.AspNetCore.Http.IFormCollection> yerleşik <xref:System.Threading.CancellationToken>bir türdür.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-207">An exception to the `[FromBody]` inference rule is any complex, built-in type with a special meaning, such as <xref:Microsoft.AspNetCore.Http.IFormCollection> and <xref:System.Threading.CancellationToken>.</span></span> <span data-ttu-id="5f2e1-208">Bağlayıcı kaynak çıkarım kodu bu özel türleri yoksayılsın.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-208">The binding source inference code ignores those special types.</span></span>
* <span data-ttu-id="5f2e1-209">`[FromForm]`türü <xref:Microsoft.AspNetCore.Http.IFormFile> ve <xref:Microsoft.AspNetCore.Http.IFormFileCollection>eylem parametreleri için çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-209">`[FromForm]` is inferred for action parameters of type <xref:Microsoft.AspNetCore.Http.IFormFile> and <xref:Microsoft.AspNetCore.Http.IFormFileCollection>.</span></span> <span data-ttu-id="5f2e1-210">Basit veya kullanıcı tarafından tanımlanan türler için çıkarılmaz.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-210">It's not inferred for any simple or user-defined types.</span></span>
* <span data-ttu-id="5f2e1-211">`[FromRoute]`rota şablonundaki bir parametreyle eşleşen herhangi bir eylem parametresi adı için çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-211">`[FromRoute]` is inferred for any action parameter name matching a parameter in the route template.</span></span> <span data-ttu-id="5f2e1-212">Birden fazla rota bir eylem parametresi ile `[FromRoute]`eşleştiğinde, herhangi bir rota değeri dikkate alınr.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-212">When more than one route matches an action parameter, any route value is considered `[FromRoute]`.</span></span>
* <span data-ttu-id="5f2e1-213">`[FromQuery]`diğer eylem parametreleri için çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-213">`[FromQuery]` is inferred for any other action parameters.</span></span>

### <a name="frombody-inference-notes"></a><span data-ttu-id="5f2e1-214">FromBody çıkarım notları</span><span class="sxs-lookup"><span data-stu-id="5f2e1-214">FromBody inference notes</span></span>

<span data-ttu-id="5f2e1-215">`[FromBody]`gibi basit türleri için `string` çıkarılmaz. `int`</span><span class="sxs-lookup"><span data-stu-id="5f2e1-215">`[FromBody]` isn't inferred for simple types such as `string` or `int`.</span></span> <span data-ttu-id="5f2e1-216">Bu nedenle, `[FromBody]` öznitelik bu işlevsellik gerektiğinde basit türleri için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-216">Therefore, the `[FromBody]` attribute should be used for simple types when that functionality is needed.</span></span>

<span data-ttu-id="5f2e1-217">Bir eylemin istek gövdesinden birden fazla parametre bağlı olması halinde, bir özel durum atılır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-217">When an action has more than one parameter bound from the request body, an exception is thrown.</span></span> <span data-ttu-id="5f2e1-218">Örneğin, aşağıdaki eylem yöntemi imzalarının tümü bir özel durum neden olur:</span><span class="sxs-lookup"><span data-stu-id="5f2e1-218">For example, all of the following action method signatures cause an exception:</span></span>

* <span data-ttu-id="5f2e1-219">`[FromBody]`karmaşık türleri olduğu için her ikisi de çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-219">`[FromBody]` inferred on both because they're complex types.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action1(Product product, Order order)
  ```

* <span data-ttu-id="5f2e1-220">`[FromBody]`karmaşık bir tür olduğu için diğerinde çıkarılan öznitelik.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-220">`[FromBody]` attribute on one, inferred on the other because it's a complex type.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action2(Product product, [FromBody] Order order)
  ```

* <span data-ttu-id="5f2e1-221">`[FromBody]`her ikide de öznitelik.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-221">`[FromBody]` attribute on both.</span></span>

  ```csharp
  [HttpPost]
  public IActionResult Action3([FromBody] Product product, [FromBody] Order order)
  ```

::: moniker range="= aspnetcore-2.1"

> [!NOTE]
> <span data-ttu-id="5f2e1-222">ASP.NET Core 2.1'de, listeler ve diziler gibi toplama türü `[FromQuery]`parametreleri yanlış olarak çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-222">In ASP.NET Core 2.1, collection type parameters such as lists and arrays are incorrectly inferred as `[FromQuery]`.</span></span> <span data-ttu-id="5f2e1-223">İstek `[FromBody]` gövdesinden bağlı olacaksa, bu parametreler için öznitelik kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-223">The `[FromBody]` attribute should be used for these parameters if they are to be bound from the request body.</span></span> <span data-ttu-id="5f2e1-224">Bu davranış, toplama türü parametrelerinin varsayılan olarak gövdeden bağlı olarak çıkarıldığı ASP.NET Core 2.2 veya daha sonra düzeltilir.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-224">This behavior is corrected in ASP.NET Core 2.2 or later, where collection type parameters are inferred to be bound from the body by default.</span></span>

::: moniker-end

### <a name="disable-inference-rules"></a><span data-ttu-id="5f2e1-225">Çıkarım kurallarını devre dışı</span><span class="sxs-lookup"><span data-stu-id="5f2e1-225">Disable inference rules</span></span>

<span data-ttu-id="5f2e1-226">Bağlayıcı kaynak çıkarımLarını devre dışı <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> `true`kılabilir, '' olarak ayarlayın</span><span class="sxs-lookup"><span data-stu-id="5f2e1-226">To disable binding source inference, set <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressInferBindingSourcesForParameters> to `true`.</span></span> <span data-ttu-id="5f2e1-227">Aşağıdaki kodu `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5f2e1-227">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,5)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,6)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](index/samples/2.x/2.1/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=1,4)]

::: moniker-end

## <a name="multipartform-data-request-inference"></a><span data-ttu-id="5f2e1-228">Çoklu bölüm/form-veri isteği çıkarımı</span><span class="sxs-lookup"><span data-stu-id="5f2e1-228">Multipart/form-data request inference</span></span>

<span data-ttu-id="5f2e1-229">Bir `[ApiController]` eylem parametresi öznitelik ile [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) açıklamalı olduğunda öznitelik bir çıkarım kuralı uygular.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-229">The `[ApiController]` attribute applies an inference rule when an action parameter is annotated with the [`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) attribute.</span></span> <span data-ttu-id="5f2e1-230">İstek `multipart/form-data` içeriği türü çıkarılır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-230">The `multipart/form-data` request content type is inferred.</span></span>

<span data-ttu-id="5f2e1-231">Varsayılan davranışı devre dışı kılabilir, <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> `true` özelliği `Startup.ConfigureServices`şu şekilde ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="5f2e1-231">To disable the default behavior, set the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressConsumesConstraintForFormFileParameters> property to `true` in `Startup.ConfigureServices`:</span></span>

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

## <a name="problem-details-for-error-status-codes"></a><span data-ttu-id="5f2e1-232">Hata durum kodları için sorun ayrıntıları</span><span class="sxs-lookup"><span data-stu-id="5f2e1-232">Problem details for error status codes</span></span>

<span data-ttu-id="5f2e1-233">Uyumluluk sürümü 2,2 veya daha sonra olduğunda, MVC bir hata sonucunu (durum kodu 400 <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>veya daha yüksek bir sonuç) .</span><span class="sxs-lookup"><span data-stu-id="5f2e1-233">When the compatibility version is 2.2 or later, MVC transforms an error result (a result with status code 400 or higher) to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span> <span data-ttu-id="5f2e1-234">Tür, `ProblemDetails` http yanıtında makine tarafından okunabilir hata ayrıntıları sağlamak için [RFC 7807 belirtimine](https://tools.ietf.org/html/rfc7807) dayanır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-234">The `ProblemDetails` type is based on the [RFC 7807 specification](https://tools.ietf.org/html/rfc7807) for providing machine-readable error details in an HTTP response.</span></span>

<span data-ttu-id="5f2e1-235">Denetleyici eyleminde aşağıdaki kodu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="5f2e1-235">Consider the following code in a controller action:</span></span>

[!code-csharp[](index/samples/2.x/2.2/Controllers/PetsController.cs?name=snippet_ProblemDetailsStatusCode)]

<span data-ttu-id="5f2e1-236">Yöntem, `NotFound` gövdeli bir `ProblemDetails` HTTP 404 durum kodu üretir.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-236">The `NotFound` method produces an HTTP 404 status code with a `ProblemDetails` body.</span></span> <span data-ttu-id="5f2e1-237">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="5f2e1-237">For example:</span></span>

```json
{
  type: "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  title: "Not Found",
  status: 404,
  traceId: "0HLHLV31KRN83:00000001"
}
```

### <a name="disable-problemdetails-response"></a><span data-ttu-id="5f2e1-238">SorunAyrıntıları devre dışı</span><span class="sxs-lookup"><span data-stu-id="5f2e1-238">Disable ProblemDetails response</span></span>

<span data-ttu-id="5f2e1-239">Hata durum kodları `ProblemDetails` için otomatik oluşturma <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> özelliği ayarlandığında `true`devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-239">The automatic creation of a `ProblemDetails` for error status codes is disabled when the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.SuppressMapClientErrors%2A> property is set to `true`.</span></span> <span data-ttu-id="5f2e1-240">Aşağıdaki kodu `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5f2e1-240">Add the following code in `Startup.ConfigureServices`:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=2,7)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=3,8)]

::: moniker-end

<a name="consumes"></a>

## <a name="define-supported-request-content-types-with-the-consumes-attribute"></a><span data-ttu-id="5f2e1-241">Desteklenen istek içerik türlerini [Tüketenler] özelliğiyle tanımlama</span><span class="sxs-lookup"><span data-stu-id="5f2e1-241">Define supported request content types with the [Consumes] attribute</span></span>

<span data-ttu-id="5f2e1-242">Varsayılan olarak, bir eylem tüm kullanılabilir istek içerik türlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-242">By default, an action supports all available request content types.</span></span> <span data-ttu-id="5f2e1-243">Örneğin, bir uygulama hem JSON hem de XML [giriş formatters](xref:mvc/models/model-binding#input-formatters)destekleyecek şekilde yapılandırılırsa, bir eylem dahil olmak üzere birden çok içerik türünü `application/json` destekler. `application/xml`</span><span class="sxs-lookup"><span data-stu-id="5f2e1-243">For example, if an app is configured to support both JSON and XML [input formatters](xref:mvc/models/model-binding#input-formatters), an action supports multiple content types, including `application/json` and `application/xml`.</span></span>

<span data-ttu-id="5f2e1-244">[[Tüketir]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) özniteliği, desteklenen istek içerik türlerini sınırlamak için bir eyleme izin verir.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-244">The [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) attribute allows an action to limit the supported request content types.</span></span> <span data-ttu-id="5f2e1-245">`[Consumes]` Bir veya daha fazla içerik türünü belirterek bir eyleme veya denetleyiciye özniteliği uygulayın:</span><span class="sxs-lookup"><span data-stu-id="5f2e1-245">Apply the `[Consumes]` attribute to an action or controller, specifying one or more content types:</span></span>

```csharp
[HttpPost]
[Consumes("application/xml")]
public IActionResult CreateProduct(Product product)
```

<span data-ttu-id="5f2e1-246">Önceki kodda, `CreateProduct` eylem içerik türünü `application/xml`belirtir.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-246">In the preceding code, the `CreateProduct` action specifies the content type `application/xml`.</span></span> <span data-ttu-id="5f2e1-247">Bu eyleme yönlendirilen `Content-Type` istekler `application/xml`bir üstbilgi belirtmelidir.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-247">Requests routed to this action must specify a `Content-Type` header of `application/xml`.</span></span> <span data-ttu-id="5f2e1-248">[415 Desteklenmeyen Medya](https://developer.mozilla.org/docs/Web/HTTP/Status/415) `application/xml` Türü yanıtında bir `Content-Type` üstbilgi belirtmeyan istekler.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-248">Requests that don't specify a `Content-Type` header of `application/xml` result in a [415 Unsupported Media Type](https://developer.mozilla.org/docs/Web/HTTP/Status/415) response.</span></span>

<span data-ttu-id="5f2e1-249">Öznitelik `[Consumes]` ayrıca, bir tür kısıtlaması uygulayarak gelen bir isteğin içerik türüne dayalı olarak bir eylemin seçimini etkilemesine de olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-249">The `[Consumes]` attribute also allows an action to influence its selection based on an incoming request's content type by applying a type constraint.</span></span> <span data-ttu-id="5f2e1-250">Aşağıdaki örneği inceleyin:</span><span class="sxs-lookup"><span data-stu-id="5f2e1-250">Consider the following example:</span></span>

[!code-csharp[](index/samples/3.x/Controllers/ConsumesController.cs?name=snippet_Class)]

<span data-ttu-id="5f2e1-251">Önceki kodda, `ConsumesController` `https://localhost:5001/api/Consumes` URL'ye gönderilen istekleri işlemek için yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-251">In the preceding code, `ConsumesController` is configured to handle requests sent to the `https://localhost:5001/api/Consumes` URL.</span></span> <span data-ttu-id="5f2e1-252">Denetleyicinin eylemlerinin her `PostJson` ikisi `PostForm`de ve POST isteklerini aynı URL ile işler.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-252">Both of the controller's actions, `PostJson` and `PostForm`, handle POST requests with the same URL.</span></span> <span data-ttu-id="5f2e1-253">`[Consumes]` Öznitelik bir tür kısıtlaması uygulamadan, belirsiz bir eşleme özel durumu atılır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-253">Without the `[Consumes]` attribute applying a type constraint, an ambiguous match exception is thrown.</span></span>

<span data-ttu-id="5f2e1-254">Öznitelik `[Consumes]` her iki eyleme de uygulanır.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-254">The `[Consumes]` attribute is applied to both actions.</span></span> <span data-ttu-id="5f2e1-255">Eylem, `PostJson` üstbilgi `Content-Type` ile `application/json`gönderilen istekleri işler.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-255">The `PostJson` action handles requests sent with a `Content-Type` header of `application/json`.</span></span> <span data-ttu-id="5f2e1-256">Eylem, `PostForm` üstbilgi `Content-Type` ile `application/x-www-form-urlencoded`gönderilen istekleri işler.</span><span class="sxs-lookup"><span data-stu-id="5f2e1-256">The `PostForm` action handles requests sent with a `Content-Type` header of `application/x-www-form-urlencoded`.</span></span> 

## <a name="additional-resources"></a><span data-ttu-id="5f2e1-257">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5f2e1-257">Additional resources</span></span>

* <xref:web-api/action-return-types>
* <xref:web-api/handle-errors>
* <xref:web-api/advanced/custom-formatters>
* <xref:web-api/advanced/formatting>
* <xref:tutorials/web-api-help-pages-using-swagger>
* <xref:mvc/controllers/routing>
