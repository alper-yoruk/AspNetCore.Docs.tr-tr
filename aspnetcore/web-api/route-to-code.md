---
title: ASP.NET Core ile temel JSON API 'Leri Route-to-code
author: jamesnk
description: Route-to-codeBasıt JSON Web API 'leri oluşturmak için ve JSON uzantısı yöntemlerini nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 11/30/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
- Route-to-code
uid: web-api/route-to-code
ms.openlocfilehash: 49eaa3ceb47c41226b7a50782436ec270e6e1b7b
ms.sourcegitcommit: 619200f2981656ede6d89adb6a22ad1a0e16da22
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96335613"
---
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a><span data-ttu-id="1f773-103">ASP.NET Core ile temel JSON API 'Leri Route-to-code</span><span class="sxs-lookup"><span data-stu-id="1f773-103">Basic JSON APIs with Route-to-code in ASP.NET Core</span></span>

<span data-ttu-id="1f773-104">, [James bAyKiNg](https://github.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="1f773-104">By [James Newton-King](https://github.com/jamesnk)</span></span>

<span data-ttu-id="1f773-105">ASP.NET Core, JSON Web API 'Leri oluşturmanın çeşitli yollarını destekler:</span><span class="sxs-lookup"><span data-stu-id="1f773-105">ASP.NET Core supports a number of ways of creating JSON web APIs:</span></span>

* <span data-ttu-id="1f773-106">[ASP.NET Core Web API 'si](xref:web-api/index) , API 'ler oluşturmak için kapsamlı bir çerçeve sağlar.</span><span class="sxs-lookup"><span data-stu-id="1f773-106">[ASP.NET Core web API](xref:web-api/index) provides a complete framework for creating APIs.</span></span> <span data-ttu-id="1f773-107">Bir hizmet öğesinden devralarak oluşturulur <xref:Microsoft.AspNetCore.Mvc.ControllerBase> .</span><span class="sxs-lookup"><span data-stu-id="1f773-107">A service is created by inheriting from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="1f773-108">Framework tarafından sunulan bazı özellikler arasında model bağlama, doğrulama, içerik anlaşması, giriş ve çıkış biçimlendirmesi ve Openapı dahildir.</span><span class="sxs-lookup"><span data-stu-id="1f773-108">Some features provided by the framework include model binding, validation, content negotiation, input and output formatting, and OpenAPI.</span></span>
* <span data-ttu-id="1f773-109">Route-to-code , Web API ASP.NET Core için çerçeve dışı bir alternatiftir.</span><span class="sxs-lookup"><span data-stu-id="1f773-109">Route-to-code is a non-framework alternative to ASP.NET Core web API.</span></span> <span data-ttu-id="1f773-110">Route-to-code[ASP.NET Core yönlendirmeyi](xref:fundamentals/routing) doğrudan kodunuza bağlar.</span><span class="sxs-lookup"><span data-stu-id="1f773-110">Route-to-code connects [ASP.NET Core routing](xref:fundamentals/routing) directly to your code.</span></span> <span data-ttu-id="1f773-111">Kodunuz istekten okur ve yanıtı yazar.</span><span class="sxs-lookup"><span data-stu-id="1f773-111">Your code reads from the request and writes the response.</span></span> <span data-ttu-id="1f773-112">Route-to-code Web API 'sinin gelişmiş özelliklerine sahip değildir, ancak bunu kullanmak için bir yapılandırma gerekmez.</span><span class="sxs-lookup"><span data-stu-id="1f773-112">Route-to-code doesn't have web API's advanced features, but there's also no configuration required to use it.</span></span>

<span data-ttu-id="1f773-113">Route-to-code , küçük ve temel JSON Web API 'Leri oluştururken iyi bir yaklaşımdır.</span><span class="sxs-lookup"><span data-stu-id="1f773-113">Route-to-code is a good approach when building small and basic JSON web APIs.</span></span>

## <a name="create-json-web-apis"></a><span data-ttu-id="1f773-114">JSON Web API 'Leri oluşturma</span><span class="sxs-lookup"><span data-stu-id="1f773-114">Create JSON web APIs</span></span>

<span data-ttu-id="1f773-115">ASP.NET Core, JSON Web API 'lerinin oluşturulmasını kolaylaştıran yardımcı yöntemler sağlar:</span><span class="sxs-lookup"><span data-stu-id="1f773-115">ASP.NET Core provides helper methods that ease the creation of JSON web APIs:</span></span>

* <span data-ttu-id="1f773-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A>`Content-Type`JSON içerik türünün üst bilgisini denetler.</span><span class="sxs-lookup"><span data-stu-id="1f773-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> checks the `Content-Type` header for a JSON content type.</span></span>
* <span data-ttu-id="1f773-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> istekten JSON okur ve belirtilen türe göre serileştirir.</span><span class="sxs-lookup"><span data-stu-id="1f773-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> reads JSON from the request and deserializes it to the specified type.</span></span>
* <span data-ttu-id="1f773-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> belirtilen değeri, yanıt gövdesine JSON olarak yazar ve yanıt içerik türünü olarak ayarlar `application/json` .</span><span class="sxs-lookup"><span data-stu-id="1f773-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> writes the specified value as JSON to the response body and sets the response content type to `application/json`.</span></span>

<span data-ttu-id="1f773-119">Hafif, rota tabanlı JSON API 'Leri *Startup.cs* içinde belirtilmiştir.</span><span class="sxs-lookup"><span data-stu-id="1f773-119">Lightweight, route-based JSON APIs are specified in *Startup.cs*.</span></span> <span data-ttu-id="1f773-120">Yol ve API mantığı, <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> bir uygulamanın istek ardışık düzeninin bir parçası olarak ' de yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="1f773-120">The route and the API logic are configured in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> as part of an app's request pipeline.</span></span>

### <a name="write-json-response"></a><span data-ttu-id="1f773-121">JSON yanıtı yaz</span><span class="sxs-lookup"><span data-stu-id="1f773-121">Write JSON response</span></span>

<span data-ttu-id="1f773-122">Bir uygulama için JSON API 'YI yapılandıran aşağıdaki kodu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="1f773-122">Consider the following code that configures a JSON API for an app:</span></span>

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

<span data-ttu-id="1f773-123">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="1f773-123">The preceding code:</span></span>

* <span data-ttu-id="1f773-124">Yönlendirme şablonu olarak içeren bir HTTP GET API uç noktası ekler `/hello/{name:alpha}` .</span><span class="sxs-lookup"><span data-stu-id="1f773-124">Adds an HTTP GET API endpoint with `/hello/{name:alpha}` as the route template.</span></span>
* <span data-ttu-id="1f773-125">Yol eşleştiğinde API, `name` istekten yol değerini okur.</span><span class="sxs-lookup"><span data-stu-id="1f773-125">When the route is matched, the API reads the `name` route value from the request.</span></span>
* <span data-ttu-id="1f773-126">Anonim bir türü, ile JSON yanıtı olarak yazar `WriteAsJsonAsync` .</span><span class="sxs-lookup"><span data-stu-id="1f773-126">Writes an anonymous type as a JSON response with `WriteAsJsonAsync`.</span></span>

### <a name="read-json-request"></a><span data-ttu-id="1f773-127">JSON isteğini oku</span><span class="sxs-lookup"><span data-stu-id="1f773-127">Read JSON request</span></span>

<span data-ttu-id="1f773-128">`HasJsonContentType` ve `ReadFromJsonAsync` , rota tabanlı BIR JSON API 'SINDE JSON yanıtının serisini kaldırmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="1f773-128">`HasJsonContentType` and `ReadFromJsonAsync` can be used to deserialize a JSON response in a route-based JSON API:</span></span>

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

<span data-ttu-id="1f773-129">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="1f773-129">The preceding code:</span></span>

* <span data-ttu-id="1f773-130">Yönlendirme şablonu olarak içeren bir HTTP POST API uç noktası ekler `/weather` .</span><span class="sxs-lookup"><span data-stu-id="1f773-130">Adds an HTTP POST API endpoint with `/weather` as the route template.</span></span>
* <span data-ttu-id="1f773-131">Yol eşleştirildiği zaman, `HasJsonContentType` istek içerik türünü doğrular.</span><span class="sxs-lookup"><span data-stu-id="1f773-131">When the route is matched, `HasJsonContentType` validates the request content type.</span></span> <span data-ttu-id="1f773-132">JSON olmayan bir içerik türü 415 durum kodu döndürür.</span><span class="sxs-lookup"><span data-stu-id="1f773-132">A non-JSON content type returns a 415 status code.</span></span>
* <span data-ttu-id="1f773-133">İçerik türü JSON ise, istek içeriği tarafından seri durumdan çıkarılmış olur `ReadFromJsonAsync` .</span><span class="sxs-lookup"><span data-stu-id="1f773-133">If the content type is JSON, the request content is deserialized by `ReadFromJsonAsync`.</span></span>

### <a name="configure-json-serialization"></a><span data-ttu-id="1f773-134">JSON serileştirmesini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="1f773-134">Configure JSON serialization</span></span>

<span data-ttu-id="1f773-135">JSON serileştirmesini özelleştirmenin iki yolu vardır:</span><span class="sxs-lookup"><span data-stu-id="1f773-135">There are two ways to customize JSON serialization:</span></span>

* <span data-ttu-id="1f773-136">Varsayılan serileştirme seçenekleri yöntemiyle ile yapılandırılabilir `JsonOptions` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="1f773-136">Default serialization options can be configured with `JsonOptions` in the `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="1f773-137">`WriteAsJsonAsync` ve `ReadFromJsonAsync` bir nesneyi kabul eden aşırı yüklemeleri vardır `JsonSerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="1f773-137">`WriteAsJsonAsync` and `ReadFromJsonAsync` have overloads that accept a `JsonSerializerOptions` object.</span></span> <span data-ttu-id="1f773-138">Bu `JsonSerializerOptions` nesne varsayılan seçenekleri geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="1f773-138">This `JsonSerializerOptions` object overrides the default options.</span></span>

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a><span data-ttu-id="1f773-139">Kimlik doğrulaması ve yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="1f773-139">Authentication and authorization</span></span>

<span data-ttu-id="1f773-140">Route-to-code kimlik doğrulama ve yetkilendirmeyi destekler.</span><span class="sxs-lookup"><span data-stu-id="1f773-140">Route-to-code supports authentication and authorization.</span></span> <span data-ttu-id="1f773-141">Ve gibi öznitelikler `[Authorize]` `[AllowAnonymous]` bir istek temsilcisiyle eşlenen uç noktalara yerleştirilemez.</span><span class="sxs-lookup"><span data-stu-id="1f773-141">Attributes, such as `[Authorize]` and `[AllowAnonymous]`, can't be placed on endpoints that map to a request delegate.</span></span> <span data-ttu-id="1f773-142">Bunun yerine, yetkilendirme meta verileri `RequireAuthorization` ve `AllowAnonymous` genişletme yöntemleri kullanılarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="1f773-142">Instead, authorization metadata is added using the `RequireAuthorization` and `AllowAnonymous` extension methods.</span></span>

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a><span data-ttu-id="1f773-143">Bağımlılık ekleme</span><span class="sxs-lookup"><span data-stu-id="1f773-143">Dependency injection</span></span>

<span data-ttu-id="1f773-144">Bir Oluşturucu kullanılarak [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) ile mümkün değildir Route-to-code .</span><span class="sxs-lookup"><span data-stu-id="1f773-144">[Dependency injection (DI)](xref:fundamentals/dependency-injection) using a constructor isn't possible with Route-to-code.</span></span> <span data-ttu-id="1f773-145">Web API 'SI, oluşturucuya eklenen hizmetler ile sizin için bir denetleyici oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1f773-145">Web API creates a controller for you with services injected into the constructor.</span></span> <span data-ttu-id="1f773-146">Bir uç nokta yürütüldüğünde bir tür oluşturulmaz, bu nedenle hizmetler el ile çözümlenmelidir.</span><span class="sxs-lookup"><span data-stu-id="1f773-146">A type isn't created when an endpoint is executed, so services must be resolved manually.</span></span>

<span data-ttu-id="1f773-147">Rota tabanlı API 'Ler, <xref:System.IServiceProvider> Hizmetleri çözümlemek için kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="1f773-147">Route-based APIs can use <xref:System.IServiceProvider> to resolve services:</span></span>

* <span data-ttu-id="1f773-148">Gibi geçici ve kapsamlı ömür Hizmetleri, `DbContext` bir uç noktanın istek temsilcisi Içindeki [HttpContext. requestservices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) 'ten çözümlenmelidir.</span><span class="sxs-lookup"><span data-stu-id="1f773-148">Transient and scoped lifetime services, such as `DbContext`, must be resolved from [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) inside an endpoint's request delegate.</span></span>
* <span data-ttu-id="1f773-149">Gibi tek yaşam süresi Hizmetleri `ILogger` [IEndpointRouteBuilder. ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider)öğesinden çözülebilir.</span><span class="sxs-lookup"><span data-stu-id="1f773-149">Singleton lifetime services, such as `ILogger`, can be resolved from [IEndpointRouteBuilder.ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider).</span></span> <span data-ttu-id="1f773-150">Hizmetler, istek temsilcileri dışında çözülebilir ve uç noktalar arasında paylaşılabilir.</span><span class="sxs-lookup"><span data-stu-id="1f773-150">Services can be resolved outside of request delegates and shared between endpoints.</span></span>

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

<span data-ttu-id="1f773-151">Dı kullanımı yoğun kullanan API 'Ler, DI 'yi destekleyen ASP.NET Core bir uygulama türü kullanmayı göz önünde bulundurmalıdır.</span><span class="sxs-lookup"><span data-stu-id="1f773-151">APIs that use DI extensively should consider using an ASP.NET Core app type that supports DI.</span></span> <span data-ttu-id="1f773-152">Örneğin, ASP.NET Core Web API 'SI.</span><span class="sxs-lookup"><span data-stu-id="1f773-152">For example, ASP.NET Core web API.</span></span> <span data-ttu-id="1f773-153">Bir denetleyicinin oluşturucusunu kullanan hizmet ekleme hizmetleri el ile çözümlenmeden daha kolay.</span><span class="sxs-lookup"><span data-stu-id="1f773-153">Service injection using a controller's constructor is easier than manually resolving services.</span></span>

## <a name="api-project-structure"></a><span data-ttu-id="1f773-154">API proje yapısı</span><span class="sxs-lookup"><span data-stu-id="1f773-154">API project structure</span></span>

<span data-ttu-id="1f773-155">Rota tabanlı API 'Lerin *Startup.cs* içinde bulunması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="1f773-155">Route-based APIs don't have to be located in *Startup.cs*.</span></span> <span data-ttu-id="1f773-156">API 'Ler diğer dosyalara yerleştirilebilecek ve ile başlangıç sırasında eşlenebilir `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="1f773-156">APIs can be placed in other files and mapped at startup with `UseEndpoints`.</span></span> <span data-ttu-id="1f773-157">Bu yaklaşım, başlangıç yapılandırması dosya boyutunu azaltır.</span><span class="sxs-lookup"><span data-stu-id="1f773-157">This approach reduces the startup configuration file size.</span></span>

<span data-ttu-id="1f773-158">Bir yöntemi tanımlayan aşağıdaki statik sınıfı göz önünde bulundurun `UserApi` `Map` .</span><span class="sxs-lookup"><span data-stu-id="1f773-158">Consider the following static `UserApi` class that defines a `Map` method.</span></span> <span data-ttu-id="1f773-159">Yöntemi rota tabanlı API 'Leri eşleştirir.</span><span class="sxs-lookup"><span data-stu-id="1f773-159">The method maps route-based APIs.</span></span>

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

<span data-ttu-id="1f773-160">`Startup.Configure`Yönteminde, `Map` yöntemi ve diğer sınıfın statik yöntemleri içinde çağrılır `UseEndpoints` :</span><span class="sxs-lookup"><span data-stu-id="1f773-160">In the `Startup.Configure` method, the `Map` method and other class's static methods are called in `UseEndpoints`:</span></span>

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="1f773-161">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1f773-161">Additional resources</span></span>

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
