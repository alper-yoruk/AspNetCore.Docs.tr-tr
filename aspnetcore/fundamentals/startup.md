---
title: ASP.NET Core 'de uygulama başlatma
author: rick-anderson
description: ASP.NET Core ' deki başlangıç sınıfının Hizmetleri ve uygulamanın istek ardışık düzenini nasıl yapılandırdığını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
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
uid: fundamentals/startup
ms.openlocfilehash: b10ddf52ea7d22ea98c295da61c09da8c87fc7a7
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633753"
---
# <a name="app-startup-in-aspnet-core"></a><span data-ttu-id="2e4bd-103">ASP.NET Core 'de uygulama başlatma</span><span class="sxs-lookup"><span data-stu-id="2e4bd-103">App startup in ASP.NET Core</span></span>

<span data-ttu-id="2e4bd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra)ve [Steve Smith](https://ardalis.com)</span><span class="sxs-lookup"><span data-stu-id="2e4bd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra), and [Steve Smith](https://ardalis.com)</span></span>

<span data-ttu-id="2e4bd-105">`Startup`Sınıfı Hizmetleri ve uygulamanın istek ardışık düzenini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-105">The `Startup` class configures services and the app's request pipeline.</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="the-startup-class"></a><span data-ttu-id="2e4bd-106">Başlangıç sınıfı</span><span class="sxs-lookup"><span data-stu-id="2e4bd-106">The Startup class</span></span>

<span data-ttu-id="2e4bd-107">ASP.NET Core uygulamalar `Startup` , kural tarafından adlandırılan bir sınıfı kullanır `Startup` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-107">ASP.NET Core apps use a `Startup` class, which is named `Startup` by convention.</span></span> <span data-ttu-id="2e4bd-108">`Startup` sınıfı:</span><span class="sxs-lookup"><span data-stu-id="2e4bd-108">The `Startup` class:</span></span>

* <span data-ttu-id="2e4bd-109">İsteğe bağlı olarak <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> , uygulamanın *hizmetlerini*yapılandırmak için bir yöntem içerir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-109">Optionally includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method to configure the app's *services*.</span></span> <span data-ttu-id="2e4bd-110">Hizmet, uygulama işlevselliği sağlayan yeniden kullanılabilir bir bileşendir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-110">A service is a reusable component that provides app functionality.</span></span> <span data-ttu-id="2e4bd-111">Hizmetler *registered* `ConfigureServices` , [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) veya ile uygulama genelinde kullanılır <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-111">Services are *registered* in `ConfigureServices` and consumed across the app via [dependency injection (DI)](xref:fundamentals/dependency-injection) or <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>
* <span data-ttu-id="2e4bd-112"><xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>Uygulamanın istek işleme ardışık düzenini oluşturmak için bir yöntem içerir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-112">Includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method to create the app's request processing pipeline.</span></span>

<span data-ttu-id="2e4bd-113">`ConfigureServices` ve `Configure` uygulama başlatıldığında ASP.NET Core çalışma zamanı tarafından çağrılır:</span><span class="sxs-lookup"><span data-stu-id="2e4bd-113">`ConfigureServices` and `Configure` are called by the ASP.NET Core runtime when the app starts:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="2e4bd-114">Yukarıdaki örnek [ Razor Sayfalar](xref:razor-pages/index)içindir; MVC sürümü benzerdir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-114">The preceding sample is for [Razor Pages](xref:razor-pages/index); the MVC version is similar.</span></span>


<span data-ttu-id="2e4bd-115">`Startup`Sınıf, uygulamanın [ana makinesi](xref:fundamentals/index#host) yapılandırıldığında belirtilir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-115">The `Startup` class is specified when the app's [host](xref:fundamentals/index#host) is built.</span></span> <span data-ttu-id="2e4bd-116">`Startup`Sınıf genellikle konak Oluşturucu üzerinde [Webhostbuilderextensions. usestartup \<TStartup> ](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) yöntemi çağırarak belirtilir:</span><span class="sxs-lookup"><span data-stu-id="2e4bd-116">The `Startup` class is typically specified by calling the [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) method on the host builder:</span></span>

[!code-csharp[](startup/3.0_samples/Program3.cs?name=snippet_Program&highlight=12)]

<span data-ttu-id="2e4bd-117">Ana bilgisayar, sınıf oluşturucusunun kullanabildiği hizmetleri sağlar `Startup` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-117">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="2e4bd-118">Uygulama aracılığıyla ek hizmetler ekler `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-118">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="2e4bd-119">Hem konak hem de uygulama Hizmetleri uygulama içinde ve üzerinde kullanılabilir `Configure` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-119">Both the host and app services are available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="2e4bd-120">`Startup` [Genel ana bilgisayar](xref:fundamentals/host/generic-host) () kullanılırken oluşturucuya yalnızca aşağıdaki hizmet türleri eklenebilir <xref:Microsoft.Extensions.Hosting.IHostBuilder> :</span><span class="sxs-lookup"><span data-stu-id="2e4bd-120">Only the following service types can be injected into the `Startup` constructor when using the [Generic Host](xref:fundamentals/host/generic-host) (<xref:Microsoft.Extensions.Hosting.IHostBuilder>):</span></span>

* <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment>
* <xref:Microsoft.Extensions.Hosting.IHostEnvironment>
* <xref:Microsoft.Extensions.Configuration.IConfiguration>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartUp2.cs?name=snippet)]

<span data-ttu-id="2e4bd-121">Çoğu hizmet, `Configure` Yöntem çağrılana kadar kullanılabilir değildir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-121">Most services are not available until the `Configure` method is called.</span></span>

### <a name="multiple-startup"></a><span data-ttu-id="2e4bd-122">Çoklu başlangıç</span><span class="sxs-lookup"><span data-stu-id="2e4bd-122">Multiple Startup</span></span>

<span data-ttu-id="2e4bd-123">Uygulama `Startup` farklı ortamlar için ayrı sınıflar tanımladığında (örneğin, `StartupDevelopment` ), çalışma zamanında uygun `Startup` sınıf seçilir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-123">When the app defines separate `Startup` classes for different environments (for example, `StartupDevelopment`), the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="2e4bd-124">Geçerli ortamla eşleşen ad sonekine sahip olan sınıf önceliklendirilir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-124">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="2e4bd-125">Uygulama geliştirme ortamında çalıştırıldıysanız ve hem `Startup` sınıf hem de `StartupDevelopment` sınıf içeriyorsa, `StartupDevelopment` sınıfı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-125">If the app is run in the Development environment and includes both a `Startup` class and a `StartupDevelopment` class, the `StartupDevelopment` class is used.</span></span> <span data-ttu-id="2e4bd-126">Daha fazla bilgi için bkz. [birden çok ortam kullanma](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span><span class="sxs-lookup"><span data-stu-id="2e4bd-126">For more information, see [Use multiple environments](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span></span>

<span data-ttu-id="2e4bd-127">Konak hakkında daha fazla bilgi için [konağa](xref:fundamentals/index#host) bakın.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-127">See [The host](xref:fundamentals/index#host) for more information on the host.</span></span> <span data-ttu-id="2e4bd-128">Başlatma sırasında hataları işleme hakkında daha fazla bilgi için bkz. [Başlangıç özel durum işleme](xref:fundamentals/error-handling#startup-exception-handling).</span><span class="sxs-lookup"><span data-stu-id="2e4bd-128">For information on handling errors during startup, see [Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling).</span></span>

## <a name="the-configureservices-method"></a><span data-ttu-id="2e4bd-129">ConfigureServices yöntemi</span><span class="sxs-lookup"><span data-stu-id="2e4bd-129">The ConfigureServices method</span></span>

<span data-ttu-id="2e4bd-130"><xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="2e4bd-130">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method is:</span></span>

* <span data-ttu-id="2e4bd-131">İsteğe bağlı.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-131">Optional.</span></span>
* <span data-ttu-id="2e4bd-132">Uygulamanın hizmetlerini yapılandırma yönteminden önce ana bilgisayar tarafından çağırılır `Configure` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-132">Called by the host before the `Configure` method to configure the app's services.</span></span>
* <span data-ttu-id="2e4bd-133">[Yapılandırma seçeneklerinin](xref:fundamentals/configuration/index) kurala göre ayarlandığı yer.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-133">Where [configuration options](xref:fundamentals/configuration/index) are set by convention.</span></span>

<span data-ttu-id="2e4bd-134">Konak, Yöntemler çağrılmadan önce bazı hizmetleri yapılandırabilir `Startup` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-134">The host may configure some services before `Startup` methods are called.</span></span> <span data-ttu-id="2e4bd-135">Daha fazla bilgi için bkz. [ana bilgisayar](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="2e4bd-135">For more information, see [The host](xref:fundamentals/index#host).</span></span>

<span data-ttu-id="2e4bd-136">Önemli kurulum gerektiren özellikler için `Add{Service}` üzerinde uzantı yöntemleri vardır <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-136">For features that require substantial setup, there are `Add{Service}` extension methods on <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>.</span></span> <span data-ttu-id="2e4bd-137">Örneğin, DbContext **ekleyin**, varsayılan **ekleyin** Identity , entityframeworkmağazalarını **ekleyin**ve sayfa **ekleyin** Razor :</span><span class="sxs-lookup"><span data-stu-id="2e4bd-137">For example, **Add**DbContext, **Add**DefaultIdentity, **Add**EntityFrameworkStores, and **Add**RazorPages:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/StartupIdentity.cs?name=snippet)]

<span data-ttu-id="2e4bd-138">Hizmet kapsayıcısına hizmet eklemek, bunları uygulama içinde ve yönteminde kullanılabilir hale getirir `Configure` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-138">Adding services to the service container makes them available within the app and in the `Configure` method.</span></span> <span data-ttu-id="2e4bd-139">Hizmetler, [bağımlılık ekleme](xref:fundamentals/dependency-injection) veya konumundan çözümlenir <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-139">The services are resolved via [dependency injection](xref:fundamentals/dependency-injection) or from <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>

## <a name="the-configure-method"></a><span data-ttu-id="2e4bd-140">Configure yöntemi</span><span class="sxs-lookup"><span data-stu-id="2e4bd-140">The Configure method</span></span>

<span data-ttu-id="2e4bd-141"><xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>Yöntemi, UYGULAMANıN http isteklerine nasıl yanıt verdiğini belirtmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-141">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method is used to specify how the app responds to HTTP requests.</span></span> <span data-ttu-id="2e4bd-142">İstek ardışık düzeni, bir örneğe [Ara yazılım](xref:fundamentals/middleware/index) bileşenleri eklenerek yapılandırılır <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-142">The request pipeline is configured by adding [middleware](xref:fundamentals/middleware/index) components to an <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance.</span></span> <span data-ttu-id="2e4bd-143">`IApplicationBuilder` , yöntemi için kullanılabilir `Configure` , ancak hizmet kapsayıcısına kayıtlı değildir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-143">`IApplicationBuilder` is available to the `Configure` method, but it isn't registered in the service container.</span></span> <span data-ttu-id="2e4bd-144">Barındırma bir oluşturur `IApplicationBuilder` ve doğrudan öğesine geçirir `Configure` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-144">Hosting creates an `IApplicationBuilder` and passes it directly to `Configure`.</span></span>

<span data-ttu-id="2e4bd-145">[ASP.NET Core şablonlar](/dotnet/core/tools/dotnet-new) işlem hattını desteğiyle birlikte yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="2e4bd-145">The [ASP.NET Core templates](/dotnet/core/tools/dotnet-new) configure the pipeline with support for:</span></span>

* [<span data-ttu-id="2e4bd-146">Geliştirici özel durum sayfası</span><span class="sxs-lookup"><span data-stu-id="2e4bd-146">Developer Exception Page</span></span>](xref:fundamentals/error-handling#developer-exception-page)
* [<span data-ttu-id="2e4bd-147">Özel durum işleyicisi</span><span class="sxs-lookup"><span data-stu-id="2e4bd-147">Exception handler</span></span>](xref:fundamentals/error-handling#exception-handler-page)
* [<span data-ttu-id="2e4bd-148">HTTP katı taşıma güvenliği (HSTS)</span><span class="sxs-lookup"><span data-stu-id="2e4bd-148">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [<span data-ttu-id="2e4bd-149">HTTPS yönlendirmesi</span><span class="sxs-lookup"><span data-stu-id="2e4bd-149">HTTPS redirection</span></span>](xref:security/enforcing-ssl)
* [<span data-ttu-id="2e4bd-150">Statik dosyalar</span><span class="sxs-lookup"><span data-stu-id="2e4bd-150">Static files</span></span>](xref:fundamentals/static-files)
* <span data-ttu-id="2e4bd-151">[MVC](xref:mvc/overview) ve [ Razor sayfaları](xref:razor-pages/index) ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2e4bd-151">ASP.NET Core [MVC](xref:mvc/overview) and [Razor Pages](xref:razor-pages/index)</span></span>


[!code-csharp[](startup/3.0_samples/StartupFilterSample/Startup.cs?name=snippet)]

<span data-ttu-id="2e4bd-152">Yukarıdaki örnek [ Razor Sayfalar](xref:razor-pages/index)içindir; MVC sürümü benzerdir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-152">The preceding sample is for [Razor Pages](xref:razor-pages/index); the MVC version is similar.</span></span>

<span data-ttu-id="2e4bd-153">Her `Use` genişletme yöntemi, istek ardışık düzenine bir veya daha fazla ara yazılım bileşeni ekler.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-153">Each `Use` extension method adds one or more middleware components to the request pipeline.</span></span> <span data-ttu-id="2e4bd-154">Örneğin, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> [ara yazılımı](xref:fundamentals/middleware/index) [statik dosyaları](xref:fundamentals/static-files)sunacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-154">For instance, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configures [middleware](xref:fundamentals/middleware/index) to serve [static files](xref:fundamentals/static-files).</span></span>

<span data-ttu-id="2e4bd-155">İstek ardışık düzeninde bulunan her bir ara yazılım bileşeni, uygun olduğunda, zincirdeki bir sonraki bileşeni çağırmaktan veya zincirde kısa bir süre sonra sağlanmasından sorumludur.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-155">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the chain, if appropriate.</span></span>

<span data-ttu-id="2e4bd-156">, Veya gibi ek hizmetler `IWebHostEnvironment` , `ILoggerFactory` `ConfigureServices` `Configure` Yöntem imzasında belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-156">Additional services, such as `IWebHostEnvironment`, `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="2e4bd-157">Bu hizmetler varsa eklenir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-157">These services are injected if they're available.</span></span>

<span data-ttu-id="2e4bd-158">Kullanımı `IApplicationBuilder` ve ara yazılım işleme sırası hakkında daha fazla bilgi için bkz <xref:fundamentals/middleware/index> ..</span><span class="sxs-lookup"><span data-stu-id="2e4bd-158">For more information on how to use `IApplicationBuilder` and the order of middleware processing, see <xref:fundamentals/middleware/index>.</span></span>

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a><span data-ttu-id="2e4bd-159">Hizmetleri başlatmadan yapılandırma</span><span class="sxs-lookup"><span data-stu-id="2e4bd-159">Configure services without Startup</span></span>

<span data-ttu-id="2e4bd-160">Hizmetleri ve istek işleme işlem hattını, `Startup` `ConfigureServices` ana bilgisayar Oluşturucu üzerinde bir sınıf, çağrı ve kullanışlı yöntemler kullanmadan yapılandırmak için `Configure` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-160">To configure services and the request processing pipeline without using a `Startup` class, call `ConfigureServices` and `Configure` convenience methods on the host builder.</span></span> <span data-ttu-id="2e4bd-161">Birbirine eklenecek birden çok çağrı `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-161">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="2e4bd-162">Birden çok `Configure` Yöntem varsa, son `Configure` çağrı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-162">If multiple `Configure` method calls exist, the last `Configure` call is used.</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program1.cs?name=snippet)]

## <a name="extend-startup-with-startup-filters"></a><span data-ttu-id="2e4bd-163">Başlangıç filtreleriyle başlatmayı Genişlet</span><span class="sxs-lookup"><span data-stu-id="2e4bd-163">Extend Startup with startup filters</span></span>

<span data-ttu-id="2e4bd-164">Şunu kullanın <xref:Microsoft.AspNetCore.Hosting.IStartupFilter> :</span><span class="sxs-lookup"><span data-stu-id="2e4bd-164">Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:</span></span>

* <span data-ttu-id="2e4bd-165">Bir uygulamanın başlangıcında veya sonunda, açık bir çağrı olmadan ara yazılım [yapılandırma](#the-configure-method) ardışık düzenini yapılandırmak için `Use{Middleware}` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-165">To configure middleware at the beginning or end of an app's [Configure](#the-configure-method) middleware pipeline without an explicit call to `Use{Middleware}`.</span></span> <span data-ttu-id="2e4bd-166">`IStartupFilter` ASP.NET Core tarafından, uygulama yazarının varsayılan ara yazılımı açıkça kaydetmesi gerekmeden, işlem hattının başlangıcına varsayılanlar eklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-166">`IStartupFilter` is used by ASP.NET Core to add defaults to the beginning of the pipeline without having to make the app author explicitly register the default middleware.</span></span> <span data-ttu-id="2e4bd-167">`IStartupFilter` Uygulama yazarı adına farklı bir bileşen çağrısının yapılmasına izin verir `Use{Middleware}` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-167">`IStartupFilter` allows a different component call `Use{Middleware}` on behalf of the app author.</span></span>
* <span data-ttu-id="2e4bd-168">Yöntem işlem hattı oluşturmak için `Configure` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-168">To create a pipeline of `Configure` methods.</span></span> <span data-ttu-id="2e4bd-169">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) , bir ara yazılımı kitaplıklar tarafından eklenen ve sonrasında çalışan bir ara yazılım olarak ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-169">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) can set a middleware to run before or after middleware added by libraries.</span></span>

<span data-ttu-id="2e4bd-170">`IStartupFilter`<xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>öğesini alır ve döndürür `Action<IApplicationBuilder>` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-170">`IStartupFilter` implements <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, which receives and returns an `Action<IApplicationBuilder>`.</span></span> <span data-ttu-id="2e4bd-171">Bir <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> uygulamanın istek ardışık düzenini yapılandırmak için bir sınıf tanımlar.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-171">An <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> defines a class to configure an app's request pipeline.</span></span> <span data-ttu-id="2e4bd-172">Daha fazla bilgi için bkz. [IApplicationBuilder ile bir ara yazılım işlem hattı oluşturma](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span><span class="sxs-lookup"><span data-stu-id="2e4bd-172">For more information, see [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span></span>

<span data-ttu-id="2e4bd-173">Her biri `IStartupFilter` , istek ardışık düzeninde bir veya daha fazla middlewares ekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-173">Each `IStartupFilter` can add one or more middlewares in the request pipeline.</span></span> <span data-ttu-id="2e4bd-174">Filtreler, hizmet kapsayıcısına eklendikleri sırada çağrılır.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-174">The filters are invoked in the order they were added to the service container.</span></span> <span data-ttu-id="2e4bd-175">Filtreler bir sonraki filtreye denetimi geçirmeden önce veya sonra bir ara yazılım ekleyebilir, böylece uygulama işlem hattının başına veya sonuna eklenir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-175">Filters may add middleware before or after passing control to the next filter, thus they append to the beginning or end of the app pipeline.</span></span>

<span data-ttu-id="2e4bd-176">Aşağıdaki örnek, ile bir ara yazılımın nasıl kaydettirildiğini gösterir `IStartupFilter` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-176">The following example demonstrates how to register a middleware with `IStartupFilter`.</span></span> <span data-ttu-id="2e4bd-177">`RequestSetOptionsMiddleware`Ara yazılım bir sorgu dizesi parametresinden bir seçenek değeri ayarlar:</span><span class="sxs-lookup"><span data-stu-id="2e4bd-177">The `RequestSetOptionsMiddleware` middleware sets an options value from a query string parameter:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsMiddleware.cs?name=snippet1)]

<span data-ttu-id="2e4bd-178">, `RequestSetOptionsMiddleware` `RequestSetOptionsStartupFilter` Sınıfında yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="2e4bd-178">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

<span data-ttu-id="2e4bd-179">, `IStartupFilter` İçindeki hizmet kapsayıcısına kaydedilir <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-179">The `IStartupFilter` is registered in the service container in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.</span></span>

[!code-csharp[](startup/3.0_samples/StartupFilterSample/Program.cs?name=snippet&highlight=19-20)]

<span data-ttu-id="2e4bd-180">İçin bir sorgu dizesi parametresi `option` sağlandığında, ara yazılım ASP.NET Core ara yazılım tarafından yanıt oluşturmadan önce değer atamasını işler.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-180">When a query string parameter for `option` is provided, the middleware processes the value assignment before the ASP.NET Core middleware renders the response.</span></span>

<span data-ttu-id="2e4bd-181">Ara yazılım yürütme sırası, kayıt sırasıyla ayarlanır `IStartupFilter` :</span><span class="sxs-lookup"><span data-stu-id="2e4bd-181">Middleware execution order is set by the order of `IStartupFilter` registrations:</span></span>

* <span data-ttu-id="2e4bd-182">Birden çok `IStartupFilter` uygulama aynı nesnelerle etkileşime geçebilir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-182">Multiple `IStartupFilter` implementations may interact with the same objects.</span></span> <span data-ttu-id="2e4bd-183">Sıralama önemliyse, `IStartupFilter` hizmet kayıtlarını middlewares 'in çalıştırması gereken sırayla eşleşecek şekilde sıralayın.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-183">If ordering is important, order their `IStartupFilter` service registrations to match the order that their middlewares should run.</span></span>
* <span data-ttu-id="2e4bd-184">Kitaplıklar, `IStartupFilter` ile kaydolmadan önce veya sonra çalışan bir veya daha fazla uygulamayla bir ara yazılım ekleyebilir `IStartupFilter` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-184">Libraries may add middleware with one or more `IStartupFilter` implementations that run before or after other app middleware registered with `IStartupFilter`.</span></span> <span data-ttu-id="2e4bd-185">Bir ara yazılımı, bir kitaplık tarafından eklenen bir ara yazılımı çağırmak için `IStartupFilter` `IStartupFilter` :</span><span class="sxs-lookup"><span data-stu-id="2e4bd-185">To invoke an `IStartupFilter` middleware before a middleware added by a library's `IStartupFilter`:</span></span>

  * <span data-ttu-id="2e4bd-186">Kitaplık hizmet kapsayıcısına eklenmeden önce hizmet kaydını konumlandırın.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-186">Position the service registration before the library is added to the service container.</span></span>
  * <span data-ttu-id="2e4bd-187">Daha sonra çağırmak için, kitaplık eklendikten sonra hizmet kaydını konumlandırın.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-187">To invoke afterward, position the service registration after the library is added.</span></span>

## <a name="add-configuration-at-startup-from-an-external-assembly"></a><span data-ttu-id="2e4bd-188">Başlangıçta bir dış derlemeden yapılandırma Ekle</span><span class="sxs-lookup"><span data-stu-id="2e4bd-188">Add configuration at startup from an external assembly</span></span>

<span data-ttu-id="2e4bd-189">Uygulama <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> , uygulamanın sınıfı dışında bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler eklenmesine olanak sağlar `Startup` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-189">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="2e4bd-190">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-190">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2e4bd-191">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2e4bd-191">Additional resources</span></span>

* [<span data-ttu-id="2e4bd-192">Ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="2e4bd-192">The host</span></span>](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="the-startup-class"></a><span data-ttu-id="2e4bd-193">Başlangıç sınıfı</span><span class="sxs-lookup"><span data-stu-id="2e4bd-193">The Startup class</span></span>

<span data-ttu-id="2e4bd-194">ASP.NET Core uygulamalar `Startup` , kural tarafından adlandırılan bir sınıfı kullanır `Startup` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-194">ASP.NET Core apps use a `Startup` class, which is named `Startup` by convention.</span></span> <span data-ttu-id="2e4bd-195">`Startup` sınıfı:</span><span class="sxs-lookup"><span data-stu-id="2e4bd-195">The `Startup` class:</span></span>

* <span data-ttu-id="2e4bd-196">İsteğe bağlı olarak <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> , uygulamanın *hizmetlerini*yapılandırmak için bir yöntem içerir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-196">Optionally includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method to configure the app's *services*.</span></span> <span data-ttu-id="2e4bd-197">Hizmet, uygulama işlevselliği sağlayan yeniden kullanılabilir bir bileşendir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-197">A service is a reusable component that provides app functionality.</span></span> <span data-ttu-id="2e4bd-198">Hizmetler *registered* `ConfigureServices` , [bağımlılık ekleme (dı)](xref:fundamentals/dependency-injection) veya ile uygulama genelinde kullanılır <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-198">Services are *registered* in `ConfigureServices` and consumed across the app via [dependency injection (DI)](xref:fundamentals/dependency-injection) or <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>
* <span data-ttu-id="2e4bd-199"><xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>Uygulamanın istek işleme ardışık düzenini oluşturmak için bir yöntem içerir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-199">Includes a <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method to create the app's request processing pipeline.</span></span>

<span data-ttu-id="2e4bd-200">`ConfigureServices` ve `Configure` uygulama başlatıldığında ASP.NET Core çalışma zamanı tarafından çağrılır:</span><span class="sxs-lookup"><span data-stu-id="2e4bd-200">`ConfigureServices` and `Configure` are called by the ASP.NET Core runtime when the app starts:</span></span>

[!code-csharp[](startup/sample_snapshot/Startup1.cs)]

<span data-ttu-id="2e4bd-201">`Startup`Sınıf, uygulamanın [ana makinesi](xref:fundamentals/index#host) yapılandırıldığında belirtilir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-201">The `Startup` class is specified when the app's [host](xref:fundamentals/index#host) is built.</span></span> <span data-ttu-id="2e4bd-202">`Startup`Sınıf genellikle konak Oluşturucu üzerinde [Webhostbuilderextensions. usestartup \<TStartup> ](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) yöntemi çağırarak belirtilir:</span><span class="sxs-lookup"><span data-stu-id="2e4bd-202">The `Startup` class is typically specified by calling the [WebHostBuilderExtensions.UseStartup\<TStartup>](xref:Microsoft.AspNetCore.Hosting.WebHostBuilderExtensions.UseStartup*) method on the host builder:</span></span>

[!code-csharp[](startup/sample_snapshot/Program3.cs?name=snippet_Program&highlight=12)]

<span data-ttu-id="2e4bd-203">Ana bilgisayar, sınıf oluşturucusunun kullanabildiği hizmetleri sağlar `Startup` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-203">The host provides services that are available to the `Startup` class constructor.</span></span> <span data-ttu-id="2e4bd-204">Uygulama aracılığıyla ek hizmetler ekler `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-204">The app adds additional services via `ConfigureServices`.</span></span> <span data-ttu-id="2e4bd-205">Hem konak hem de uygulama Hizmetleri uygulama içinde ve üzerinde kullanılabilir `Configure` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-205">Both the host and app services are then available in `Configure` and throughout the app.</span></span>

<span data-ttu-id="2e4bd-206">Sınıfa [bağımlılık ekleme](xref:fundamentals/dependency-injection) 'nin yaygın bir kullanımı `Startup` , şu ekleme yapmak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="2e4bd-206">A common use of [dependency injection](xref:fundamentals/dependency-injection) into the `Startup` class is to inject:</span></span>

* <span data-ttu-id="2e4bd-207"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> Hizmetleri ortama göre yapılandırmak için.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-207"><xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> to configure services by environment.</span></span>
* <span data-ttu-id="2e4bd-208"><xref:Microsoft.Extensions.Configuration.IConfiguration> yapılandırmasını okuyun.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-208"><xref:Microsoft.Extensions.Configuration.IConfiguration> to read configuration.</span></span>
* <span data-ttu-id="2e4bd-209"><xref:Microsoft.Extensions.Logging.ILoggerFactory> içinde bir günlükçü oluşturmak için `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-209"><xref:Microsoft.Extensions.Logging.ILoggerFactory> to create a logger in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](startup/sample_snapshot/Startup2.cs?highlight=7-8)]

<span data-ttu-id="2e4bd-210">Çoğu hizmet, `Configure` Yöntem çağrılana kadar kullanılabilir değildir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-210">Most services are not available until the `Configure` method is called.</span></span>

### <a name="multiple-startup"></a><span data-ttu-id="2e4bd-211">Çoklu başlangıç</span><span class="sxs-lookup"><span data-stu-id="2e4bd-211">Multiple Startup</span></span>

<span data-ttu-id="2e4bd-212">Uygulama `Startup` farklı ortamlar için ayrı sınıflar tanımladığında (örneğin, `StartupDevelopment` ), çalışma zamanında uygun `Startup` sınıf seçilir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-212">When the app defines separate `Startup` classes for different environments (for example, `StartupDevelopment`), the appropriate `Startup` class is selected at runtime.</span></span> <span data-ttu-id="2e4bd-213">Geçerli ortamla eşleşen ad sonekine sahip olan sınıf önceliklendirilir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-213">The class whose name suffix matches the current environment is prioritized.</span></span> <span data-ttu-id="2e4bd-214">Uygulama geliştirme ortamında çalıştırıldıysanız ve hem `Startup` sınıf hem de `StartupDevelopment` sınıf içeriyorsa, `StartupDevelopment` sınıfı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-214">If the app is run in the Development environment and includes both a `Startup` class and a `StartupDevelopment` class, the `StartupDevelopment` class is used.</span></span> <span data-ttu-id="2e4bd-215">Daha fazla bilgi için bkz. [birden çok ortam kullanma](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span><span class="sxs-lookup"><span data-stu-id="2e4bd-215">For more information, see [Use multiple environments](xref:fundamentals/environments#environment-based-startup-class-and-methods).</span></span>

<span data-ttu-id="2e4bd-216">Konak hakkında daha fazla bilgi için [konağa](xref:fundamentals/index#host) bakın.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-216">See [The host](xref:fundamentals/index#host) for more information on the host.</span></span> <span data-ttu-id="2e4bd-217">Başlatma sırasında hataları işleme hakkında daha fazla bilgi için bkz. [Başlangıç özel durum işleme](xref:fundamentals/error-handling#startup-exception-handling).</span><span class="sxs-lookup"><span data-stu-id="2e4bd-217">For information on handling errors during startup, see [Startup exception handling](xref:fundamentals/error-handling#startup-exception-handling).</span></span>

## <a name="the-configureservices-method"></a><span data-ttu-id="2e4bd-218">ConfigureServices yöntemi</span><span class="sxs-lookup"><span data-stu-id="2e4bd-218">The ConfigureServices method</span></span>

<span data-ttu-id="2e4bd-219"><xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>Yöntemi:</span><span class="sxs-lookup"><span data-stu-id="2e4bd-219">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> method is:</span></span>

* <span data-ttu-id="2e4bd-220">İsteğe bağlı.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-220">Optional.</span></span>
* <span data-ttu-id="2e4bd-221">Uygulamanın hizmetlerini yapılandırma yönteminden önce ana bilgisayar tarafından çağırılır `Configure` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-221">Called by the host before the `Configure` method to configure the app's services.</span></span>
* <span data-ttu-id="2e4bd-222">[Yapılandırma seçeneklerinin](xref:fundamentals/configuration/index) kurala göre ayarlandığı yer.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-222">Where [configuration options](xref:fundamentals/configuration/index) are set by convention.</span></span>

<span data-ttu-id="2e4bd-223">Konak, Yöntemler çağrılmadan önce bazı hizmetleri yapılandırabilir `Startup` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-223">The host may configure some services before `Startup` methods are called.</span></span> <span data-ttu-id="2e4bd-224">Daha fazla bilgi için bkz. [ana bilgisayar](xref:fundamentals/index#host).</span><span class="sxs-lookup"><span data-stu-id="2e4bd-224">For more information, see [The host](xref:fundamentals/index#host).</span></span>

<span data-ttu-id="2e4bd-225">Önemli kurulum gerektiren özellikler için `Add{Service}` üzerinde uzantı yöntemleri vardır <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection> .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-225">For features that require substantial setup, there are `Add{Service}` extension methods on <xref:Microsoft.Extensions.DependencyInjection.IServiceCollection>.</span></span> <span data-ttu-id="2e4bd-226">Örneğin, DbContext **ekleyin**, varsayılan **ekleyin** Identity , entityframeworkmağazalarını **ekleyin**ve sayfa **ekleyin** Razor :</span><span class="sxs-lookup"><span data-stu-id="2e4bd-226">For example, **Add**DbContext, **Add**DefaultIdentity, **Add**EntityFrameworkStores, and **Add**RazorPages:</span></span>

[!code-csharp[](startup/sample_snapshot/Startup3.cs)]

<span data-ttu-id="2e4bd-227">Hizmet kapsayıcısına hizmet eklemek, bunları uygulama içinde ve yönteminde kullanılabilir hale getirir `Configure` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-227">Adding services to the service container makes them available within the app and in the `Configure` method.</span></span> <span data-ttu-id="2e4bd-228">Hizmetler, [bağımlılık ekleme](xref:fundamentals/dependency-injection) veya konumundan çözümlenir <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*> .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-228">The services are resolved via [dependency injection](xref:fundamentals/dependency-injection) or from <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ApplicationServices*>.</span></span>

<span data-ttu-id="2e4bd-229">Hakkında daha fazla bilgi için bkz. [Setcompatibilityversion](xref:mvc/compatibility-version) `SetCompatibilityVersion` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-229">See [SetCompatibilityVersion](xref:mvc/compatibility-version) for more information on `SetCompatibilityVersion`.</span></span>

## <a name="the-configure-method"></a><span data-ttu-id="2e4bd-230">Configure yöntemi</span><span class="sxs-lookup"><span data-stu-id="2e4bd-230">The Configure method</span></span>

<span data-ttu-id="2e4bd-231"><xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>Yöntemi, UYGULAMANıN http isteklerine nasıl yanıt verdiğini belirtmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-231">The <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*> method is used to specify how the app responds to HTTP requests.</span></span> <span data-ttu-id="2e4bd-232">İstek ardışık düzeni, bir örneğe [Ara yazılım](xref:fundamentals/middleware/index) bileşenleri eklenerek yapılandırılır <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-232">The request pipeline is configured by adding [middleware](xref:fundamentals/middleware/index) components to an <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> instance.</span></span> <span data-ttu-id="2e4bd-233">`IApplicationBuilder` , yöntemi için kullanılabilir `Configure` , ancak hizmet kapsayıcısına kayıtlı değildir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-233">`IApplicationBuilder` is available to the `Configure` method, but it isn't registered in the service container.</span></span> <span data-ttu-id="2e4bd-234">Barındırma bir oluşturur `IApplicationBuilder` ve doğrudan öğesine geçirir `Configure` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-234">Hosting creates an `IApplicationBuilder` and passes it directly to `Configure`.</span></span>

<span data-ttu-id="2e4bd-235">[ASP.NET Core şablonlar](/dotnet/core/tools/dotnet-new) işlem hattını desteğiyle birlikte yapılandırır:</span><span class="sxs-lookup"><span data-stu-id="2e4bd-235">The [ASP.NET Core templates](/dotnet/core/tools/dotnet-new) configure the pipeline with support for:</span></span>

* [<span data-ttu-id="2e4bd-236">Geliştirici özel durum sayfası</span><span class="sxs-lookup"><span data-stu-id="2e4bd-236">Developer Exception Page</span></span>](xref:fundamentals/error-handling#developer-exception-page)
* [<span data-ttu-id="2e4bd-237">Özel durum işleyicisi</span><span class="sxs-lookup"><span data-stu-id="2e4bd-237">Exception handler</span></span>](xref:fundamentals/error-handling#exception-handler-page)
* [<span data-ttu-id="2e4bd-238">HTTP katı taşıma güvenliği (HSTS)</span><span class="sxs-lookup"><span data-stu-id="2e4bd-238">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts)
* [<span data-ttu-id="2e4bd-239">HTTPS yönlendirmesi</span><span class="sxs-lookup"><span data-stu-id="2e4bd-239">HTTPS redirection</span></span>](xref:security/enforcing-ssl)
* [<span data-ttu-id="2e4bd-240">Statik dosyalar</span><span class="sxs-lookup"><span data-stu-id="2e4bd-240">Static files</span></span>](xref:fundamentals/static-files)
* <span data-ttu-id="2e4bd-241">[MVC](xref:mvc/overview) ve [ Razor sayfaları](xref:razor-pages/index) ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2e4bd-241">ASP.NET Core [MVC](xref:mvc/overview) and [Razor Pages](xref:razor-pages/index)</span></span>
* [<span data-ttu-id="2e4bd-242">Genel Veri Koruma Yönetmeliği (GDPR)</span><span class="sxs-lookup"><span data-stu-id="2e4bd-242">General Data Protection Regulation (GDPR)</span></span>](xref:security/gdpr)

[!code-csharp[](startup/sample_snapshot/Startup4.cs)]

<span data-ttu-id="2e4bd-243">Her `Use` genişletme yöntemi, istek ardışık düzenine bir veya daha fazla ara yazılım bileşeni ekler.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-243">Each `Use` extension method adds one or more middleware components to the request pipeline.</span></span> <span data-ttu-id="2e4bd-244">Örneğin, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> [ara yazılımı](xref:fundamentals/middleware/index) [statik dosyaları](xref:fundamentals/static-files)sunacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-244">For instance, <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> configures [middleware](xref:fundamentals/middleware/index) to serve [static files](xref:fundamentals/static-files).</span></span>

<span data-ttu-id="2e4bd-245">İstek ardışık düzeninde bulunan her bir ara yazılım bileşeni, uygun olduğunda, zincirdeki bir sonraki bileşeni çağırmaktan veya zincirde kısa bir süre sonra sağlanmasından sorumludur.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-245">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the chain, if appropriate.</span></span>

<span data-ttu-id="2e4bd-246">Ve gibi ek hizmetler `IHostingEnvironment` `ILoggerFactory` veya ' de tanımlı herhangi bir şey `ConfigureServices` , `Configure` Yöntem imzasında belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-246">Additional services, such as `IHostingEnvironment` and `ILoggerFactory`, or anything defined in `ConfigureServices`, can be specified in the `Configure` method signature.</span></span> <span data-ttu-id="2e4bd-247">Bu hizmetler varsa eklenir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-247">These services are injected if they're available.</span></span>

<span data-ttu-id="2e4bd-248">Kullanımı `IApplicationBuilder` ve ara yazılım işleme sırası hakkında daha fazla bilgi için bkz <xref:fundamentals/middleware/index> ..</span><span class="sxs-lookup"><span data-stu-id="2e4bd-248">For more information on how to use `IApplicationBuilder` and the order of middleware processing, see <xref:fundamentals/middleware/index>.</span></span>

<a name="convenience-methods"></a>

## <a name="configure-services-without-startup"></a><span data-ttu-id="2e4bd-249">Hizmetleri başlatmadan yapılandırma</span><span class="sxs-lookup"><span data-stu-id="2e4bd-249">Configure services without Startup</span></span>

<span data-ttu-id="2e4bd-250">Hizmetleri ve istek işleme işlem hattını, `Startup` `ConfigureServices` ana bilgisayar Oluşturucu üzerinde bir sınıf, çağrı ve kullanışlı yöntemler kullanmadan yapılandırmak için `Configure` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-250">To configure services and the request processing pipeline without using a `Startup` class, call `ConfigureServices` and `Configure` convenience methods on the host builder.</span></span> <span data-ttu-id="2e4bd-251">Birbirine eklenecek birden çok çağrı `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-251">Multiple calls to `ConfigureServices` append to one another.</span></span> <span data-ttu-id="2e4bd-252">Birden çok `Configure` Yöntem varsa, son `Configure` çağrı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-252">If multiple `Configure` method calls exist, the last `Configure` call is used.</span></span>

[!code-csharp[](startup/sample_snapshot/Program1.cs?highlight=16,20)]

## <a name="extend-startup-with-startup-filters"></a><span data-ttu-id="2e4bd-253">Başlangıç filtreleriyle başlatmayı Genişlet</span><span class="sxs-lookup"><span data-stu-id="2e4bd-253">Extend Startup with startup filters</span></span>

<span data-ttu-id="2e4bd-254">Şunu kullanın <xref:Microsoft.AspNetCore.Hosting.IStartupFilter> :</span><span class="sxs-lookup"><span data-stu-id="2e4bd-254">Use <xref:Microsoft.AspNetCore.Hosting.IStartupFilter>:</span></span>

* <span data-ttu-id="2e4bd-255">Bir uygulamanın başlangıcında veya sonunda, açık bir çağrı olmadan ara yazılım [yapılandırma](#the-configure-method) ardışık düzenini yapılandırmak için `Use{Middleware}` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-255">To configure middleware at the beginning or end of an app's [Configure](#the-configure-method) middleware pipeline without an explicit call to `Use{Middleware}`.</span></span> <span data-ttu-id="2e4bd-256">`IStartupFilter` ASP.NET Core tarafından, uygulama yazarının varsayılan ara yazılımı açıkça kaydetmesi gerekmeden, işlem hattının başlangıcına varsayılanlar eklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-256">`IStartupFilter` is used by ASP.NET Core to add defaults to the beginning of the pipeline without having to make the app author explicitly register the default middleware.</span></span> <span data-ttu-id="2e4bd-257">`IStartupFilter` Uygulama yazarı adına farklı bir bileşen çağrısının yapılmasına izin verir `Use{Middleware}` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-257">`IStartupFilter` allows a different component call `Use{Middleware}` on behalf of the app author.</span></span>
* <span data-ttu-id="2e4bd-258">Yöntem işlem hattı oluşturmak için `Configure` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-258">To create a pipeline of `Configure` methods.</span></span> <span data-ttu-id="2e4bd-259">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) , bir ara yazılımı kitaplıklar tarafından eklenen ve sonrasında çalışan bir ara yazılım olarak ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-259">[IStartupFilter.Configure](xref:Microsoft.AspNetCore.Hosting.IStartupFilter.Configure*) can set a middleware to run before or after middleware added by libraries.</span></span>

<span data-ttu-id="2e4bd-260">`IStartupFilter`<xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>öğesini alır ve döndürür `Action<IApplicationBuilder>` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-260">`IStartupFilter` implements <xref:Microsoft.AspNetCore.Hosting.StartupBase.Configure*>, which receives and returns an `Action<IApplicationBuilder>`.</span></span> <span data-ttu-id="2e4bd-261">Bir <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> uygulamanın istek ardışık düzenini yapılandırmak için bir sınıf tanımlar.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-261">An <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> defines a class to configure an app's request pipeline.</span></span> <span data-ttu-id="2e4bd-262">Daha fazla bilgi için bkz. [IApplicationBuilder ile bir ara yazılım işlem hattı oluşturma](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span><span class="sxs-lookup"><span data-stu-id="2e4bd-262">For more information, see [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder).</span></span>

<span data-ttu-id="2e4bd-263">Her biri `IStartupFilter` , istek ardışık düzeninde bir veya daha fazla middlewares ekleyebilir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-263">Each `IStartupFilter` can add one or more middlewares in the request pipeline.</span></span> <span data-ttu-id="2e4bd-264">Filtreler, hizmet kapsayıcısına eklendikleri sırada çağrılır.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-264">The filters are invoked in the order they were added to the service container.</span></span> <span data-ttu-id="2e4bd-265">Filtreler bir sonraki filtreye denetimi geçirmeden önce veya sonra bir ara yazılım ekleyebilir, böylece uygulama işlem hattının başına veya sonuna eklenir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-265">Filters may add middleware before or after passing control to the next filter, thus they append to the beginning or end of the app pipeline.</span></span>

<span data-ttu-id="2e4bd-266">Aşağıdaki örnek, ile bir ara yazılımın nasıl kaydettirildiğini gösterir `IStartupFilter` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-266">The following example demonstrates how to register a middleware with `IStartupFilter`.</span></span> <span data-ttu-id="2e4bd-267">`RequestSetOptionsMiddleware`Ara yazılım bir sorgu dizesi parametresinden bir seçenek değeri ayarlar:</span><span class="sxs-lookup"><span data-stu-id="2e4bd-267">The `RequestSetOptionsMiddleware` middleware sets an options value from a query string parameter:</span></span>

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsMiddleware.cs?name=snippet1&highlight=21)]

<span data-ttu-id="2e4bd-268">, `RequestSetOptionsMiddleware` `RequestSetOptionsStartupFilter` Sınıfında yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="2e4bd-268">The `RequestSetOptionsMiddleware` is configured in the `RequestSetOptionsStartupFilter` class:</span></span>

[!code-csharp[](startup/sample_snapshot/RequestSetOptionsStartupFilter.cs?name=snippet1&highlight=7)]

<span data-ttu-id="2e4bd-269">, `IStartupFilter` İçindeki hizmet kapsayıcısına kaydedilir <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*> .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-269">The `IStartupFilter` is registered in the service container in <xref:Microsoft.AspNetCore.Hosting.StartupBase.ConfigureServices*>.</span></span>

[!code-csharp[](startup/sample_snapshot/Program2.cs?name=snippet1&highlight=4-5)]

<span data-ttu-id="2e4bd-270">İçin bir sorgu dizesi parametresi `option` sağlandığında, ara yazılım ASP.NET Core ara yazılım tarafından yanıt oluşturmadan önce değer atamasını işler.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-270">When a query string parameter for `option` is provided, the middleware processes the value assignment before the ASP.NET Core middleware renders the response.</span></span>

<span data-ttu-id="2e4bd-271">Ara yazılım yürütme sırası, kayıt sırasıyla ayarlanır `IStartupFilter` :</span><span class="sxs-lookup"><span data-stu-id="2e4bd-271">Middleware execution order is set by the order of `IStartupFilter` registrations:</span></span>

* <span data-ttu-id="2e4bd-272">Birden çok `IStartupFilter` uygulama aynı nesnelerle etkileşime geçebilir.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-272">Multiple `IStartupFilter` implementations may interact with the same objects.</span></span> <span data-ttu-id="2e4bd-273">Sıralama önemliyse, `IStartupFilter` hizmet kayıtlarını middlewares 'in çalıştırması gereken sırayla eşleşecek şekilde sıralayın.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-273">If ordering is important, order their `IStartupFilter` service registrations to match the order that their middlewares should run.</span></span>
* <span data-ttu-id="2e4bd-274">Kitaplıklar, `IStartupFilter` ile kaydolmadan önce veya sonra çalışan bir veya daha fazla uygulamayla bir ara yazılım ekleyebilir `IStartupFilter` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-274">Libraries may add middleware with one or more `IStartupFilter` implementations that run before or after other app middleware registered with `IStartupFilter`.</span></span> <span data-ttu-id="2e4bd-275">Bir ara yazılımı, bir kitaplık tarafından eklenen bir ara yazılımı çağırmak için `IStartupFilter` `IStartupFilter` :</span><span class="sxs-lookup"><span data-stu-id="2e4bd-275">To invoke an `IStartupFilter` middleware before a middleware added by a library's `IStartupFilter`:</span></span>

  * <span data-ttu-id="2e4bd-276">Kitaplık hizmet kapsayıcısına eklenmeden önce hizmet kaydını konumlandırın.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-276">Position the service registration before the library is added to the service container.</span></span>
  * <span data-ttu-id="2e4bd-277">Daha sonra çağırmak için, kitaplık eklendikten sonra hizmet kaydını konumlandırın.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-277">To invoke afterward, position the service registration after the library is added.</span></span>

## <a name="add-configuration-at-startup-from-an-external-assembly"></a><span data-ttu-id="2e4bd-278">Başlangıçta bir dış derlemeden yapılandırma Ekle</span><span class="sxs-lookup"><span data-stu-id="2e4bd-278">Add configuration at startup from an external assembly</span></span>

<span data-ttu-id="2e4bd-279">Uygulama <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> , uygulamanın sınıfı dışında bir dış derlemeden başlatma sırasında bir uygulamaya iyileştirmeler eklenmesine olanak sağlar `Startup` .</span><span class="sxs-lookup"><span data-stu-id="2e4bd-279">An <xref:Microsoft.AspNetCore.Hosting.IHostingStartup> implementation allows adding enhancements to an app at startup from an external assembly outside of the app's `Startup` class.</span></span> <span data-ttu-id="2e4bd-280">Daha fazla bilgi için bkz. <xref:fundamentals/configuration/platform-specific-configuration>.</span><span class="sxs-lookup"><span data-stu-id="2e4bd-280">For more information, see <xref:fundamentals/configuration/platform-specific-configuration>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2e4bd-281">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2e4bd-281">Additional resources</span></span>

* [<span data-ttu-id="2e4bd-282">Ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="2e4bd-282">The host</span></span>](xref:fundamentals/index#host)
* <xref:fundamentals/environments>
* <xref:fundamentals/middleware/index>
* <xref:fundamentals/logging/index>
* <xref:fundamentals/configuration/index>

::: moniker-end