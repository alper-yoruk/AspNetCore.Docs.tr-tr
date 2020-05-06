---
title: ASP.NET Core ara yazılıma HTTP işleyicileri ve modülleri geçirme
author: rick-anderson
description: ''
ms.author: riande
ms.date: 12/07/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/http-modules
ms.openlocfilehash: c2b49976d2063679eab2403aae432660e8c8932d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775420"
---
# <a name="migrate-http-handlers-and-modules-to-aspnet-core-middleware"></a><span data-ttu-id="e460b-102">ASP.NET Core ara yazılıma HTTP işleyicileri ve modülleri geçirme</span><span class="sxs-lookup"><span data-stu-id="e460b-102">Migrate HTTP handlers and modules to ASP.NET Core middleware</span></span>

<span data-ttu-id="e460b-103">Bu makalede [, System. webserver 'deki mevcut ASP.net http modüllerinin ve işleyicilerinin](/iis/configuration/system.webserver/) ASP.NET Core [Ara yazılıma](xref:fundamentals/middleware/index)nasıl geçirileceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="e460b-103">This article shows how to migrate existing ASP.NET [HTTP modules and handlers from system.webserver](/iis/configuration/system.webserver/) to ASP.NET Core [middleware](xref:fundamentals/middleware/index).</span></span>

## <a name="modules-and-handlers-revisited"></a><span data-ttu-id="e460b-104">Modüller ve işleyiciler yeniden ziyaret edildi</span><span class="sxs-lookup"><span data-stu-id="e460b-104">Modules and handlers revisited</span></span>

<span data-ttu-id="e460b-105">ASP.NET Core ara yazılıma devam etmeden önce, ilk olarak HTTP modüllerinin ve işleyicilerinin nasıl çalıştığını görelim:</span><span class="sxs-lookup"><span data-stu-id="e460b-105">Before proceeding to ASP.NET Core middleware, let's first recap how HTTP modules and handlers work:</span></span>

![Modüller Işleyicisi](http-modules/_static/moduleshandlers.png)

<span data-ttu-id="e460b-107">**İşleyiciler şunlardır:**</span><span class="sxs-lookup"><span data-stu-id="e460b-107">**Handlers are:**</span></span>

* <span data-ttu-id="e460b-108">[IHttpHandler](/dotnet/api/system.web.ihttphandler) uygulayan sınıflar</span><span class="sxs-lookup"><span data-stu-id="e460b-108">Classes that implement [IHttpHandler](/dotnet/api/system.web.ihttphandler)</span></span>

* <span data-ttu-id="e460b-109">Verilen bir dosya adına veya uzantıya sahip istekleri işlemek için kullanılır *. rapor*</span><span class="sxs-lookup"><span data-stu-id="e460b-109">Used to handle requests with a given file name or extension, such as *.report*</span></span>

* <span data-ttu-id="e460b-110">*Web. config* 'de [yapılandırıldı](/iis/configuration/system.webserver/handlers/)</span><span class="sxs-lookup"><span data-stu-id="e460b-110">[Configured](/iis/configuration/system.webserver/handlers/) in *Web.config*</span></span>

<span data-ttu-id="e460b-111">**Modüller şunlardır:**</span><span class="sxs-lookup"><span data-stu-id="e460b-111">**Modules are:**</span></span>

* <span data-ttu-id="e460b-112">[IHttpModule](/dotnet/api/system.web.ihttpmodule) uygulayan sınıflar</span><span class="sxs-lookup"><span data-stu-id="e460b-112">Classes that implement [IHttpModule](/dotnet/api/system.web.ihttpmodule)</span></span>

* <span data-ttu-id="e460b-113">Her istek için çağrılır</span><span class="sxs-lookup"><span data-stu-id="e460b-113">Invoked for every request</span></span>

* <span data-ttu-id="e460b-114">Kısa devre verebilir (bir isteğin daha fazla işlenmesini durdurabilirsiniz)</span><span class="sxs-lookup"><span data-stu-id="e460b-114">Able to short-circuit (stop further processing of a request)</span></span>

* <span data-ttu-id="e460b-115">HTTP yanıtına ekleme yapabilir veya kendi kendine oluşturma</span><span class="sxs-lookup"><span data-stu-id="e460b-115">Able to add to the HTTP response, or create their own</span></span>

* <span data-ttu-id="e460b-116">*Web. config* 'de [yapılandırıldı](/iis/configuration/system.webserver/modules/)</span><span class="sxs-lookup"><span data-stu-id="e460b-116">[Configured](/iis/configuration/system.webserver/modules/) in *Web.config*</span></span>

<span data-ttu-id="e460b-117">**Modüllerin gelen istekleri işleme sırası şu şekilde belirlenir:**</span><span class="sxs-lookup"><span data-stu-id="e460b-117">**The order in which modules process incoming requests is determined by:**</span></span>

1. <span data-ttu-id="e460b-118">ASP.NET tarafından tetiklenen bir dizi olay olan [uygulama yaşam döngüsü](https://msdn.microsoft.com/library/ms227673.aspx): [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [kimlik doğrulayan terequest](/dotnet/api/system.web.httpapplication.authenticaterequest), vb. Her modül, bir veya daha fazla olay için bir işleyici oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="e460b-118">The [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx), which is a series events fired by ASP.NET: [BeginRequest](/dotnet/api/system.web.httpapplication.beginrequest), [AuthenticateRequest](/dotnet/api/system.web.httpapplication.authenticaterequest), etc. Each module can create a handler for one or more events.</span></span>

2. <span data-ttu-id="e460b-119">Aynı olay için, *Web. config*'de yapılandırıldıkları sıra.</span><span class="sxs-lookup"><span data-stu-id="e460b-119">For the same event, the order in which they're configured in *Web.config*.</span></span>

<span data-ttu-id="e460b-120">Modüllere ek olarak, *Global.asax.cs* dosyanıza yaşam döngüsü olaylarının işleyicilerini ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e460b-120">In addition to modules, you can add handlers for the life cycle events to your *Global.asax.cs* file.</span></span> <span data-ttu-id="e460b-121">Bu işleyiciler, yapılandırılan modüllerdeki işleyicilerden sonra çalışır.</span><span class="sxs-lookup"><span data-stu-id="e460b-121">These handlers run after the handlers in the configured modules.</span></span>

## <a name="from-handlers-and-modules-to-middleware"></a><span data-ttu-id="e460b-122">İşleyiciler ve modüllerden ara yazılıma</span><span class="sxs-lookup"><span data-stu-id="e460b-122">From handlers and modules to middleware</span></span>

<span data-ttu-id="e460b-123">**Ara yazılım HTTP modülleriyle ve işleyicilerinden daha basittir:**</span><span class="sxs-lookup"><span data-stu-id="e460b-123">**Middleware are simpler than HTTP modules and handlers:**</span></span>

* <span data-ttu-id="e460b-124">Modüller, işleyiciler, *Global.asax.cs*, *Web. config* (IIS yapılandırması hariç) ve uygulama yaşam döngüsü kayboldu</span><span class="sxs-lookup"><span data-stu-id="e460b-124">Modules, handlers, *Global.asax.cs*, *Web.config* (except for IIS configuration) and the application life cycle are gone</span></span>

* <span data-ttu-id="e460b-125">Her iki modülün ve işleyicinin rolleri, ara yazılım tarafından ele alınmıştır</span><span class="sxs-lookup"><span data-stu-id="e460b-125">The roles of both modules and handlers have been taken over by middleware</span></span>

* <span data-ttu-id="e460b-126">Ara yazılım, *Web. config* yerine kod kullanılarak yapılandırılır</span><span class="sxs-lookup"><span data-stu-id="e460b-126">Middleware are configured using code rather than in *Web.config*</span></span>

* <span data-ttu-id="e460b-127">[Ardışık düzen dallandırma](xref:fundamentals/middleware/index#use-run-and-map) , yalnızca URL 'ye değil, istek üst bilgileri, sorgu dizeleri vb. gibi belirli bir ara yazılıma istek göndermenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="e460b-127">[Pipeline branching](xref:fundamentals/middleware/index#use-run-and-map) lets you send requests to specific middleware, based on not only the URL but also on request headers, query strings, etc.</span></span>

<span data-ttu-id="e460b-128">**Ara yazılım, modüllere çok benzer:**</span><span class="sxs-lookup"><span data-stu-id="e460b-128">**Middleware are very similar to modules:**</span></span>

* <span data-ttu-id="e460b-129">Her istek için prensibi çağrıldı</span><span class="sxs-lookup"><span data-stu-id="e460b-129">Invoked in principle for every request</span></span>

* <span data-ttu-id="e460b-130">İstek, bir [sonraki ara yazılıma geçirilmeyen](#http-modules-shortcircuiting-middleware) bir istek için kısa devre verebilir</span><span class="sxs-lookup"><span data-stu-id="e460b-130">Able to short-circuit a request, by [not passing the request to the next middleware](#http-modules-shortcircuiting-middleware)</span></span>

* <span data-ttu-id="e460b-131">Kendi HTTP yanıtlarını oluşturabilebiliyor</span><span class="sxs-lookup"><span data-stu-id="e460b-131">Able to create their own HTTP response</span></span>

<span data-ttu-id="e460b-132">**Ara yazılım ve modüller farklı bir sırayla işlenir:**</span><span class="sxs-lookup"><span data-stu-id="e460b-132">**Middleware and modules are processed in a different order:**</span></span>

* <span data-ttu-id="e460b-133">Ara yazılım sırası, istek işlem hattına eklendikleri sıraya göre, modüller sırası genellikle [uygulama yaşam döngüsü](https://msdn.microsoft.com/library/ms227673.aspx) olaylarına dayalıdır</span><span class="sxs-lookup"><span data-stu-id="e460b-133">Order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on [application life cycle](https://msdn.microsoft.com/library/ms227673.aspx) events</span></span>

* <span data-ttu-id="e460b-134">Yanıt için ara yazılım sırası, istekler için bu istekten ters, modüller sırası istekler ve yanıtlar için de aynıdır</span><span class="sxs-lookup"><span data-stu-id="e460b-134">Order of middleware for responses is the reverse from that for requests, while order of modules is the same for requests and responses</span></span>

* <span data-ttu-id="e460b-135">Bkz. [IApplicationBuilder ile ara yazılım işlem hattı oluşturma](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span><span class="sxs-lookup"><span data-stu-id="e460b-135">See [Create a middleware pipeline with IApplicationBuilder](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder)</span></span>

![Ara yazılım](http-modules/_static/middleware.png)

<span data-ttu-id="e460b-137">Yukarıdaki görüntüde, kimlik doğrulama ara, isteği kabul eden bir şekilde kabul edin.</span><span class="sxs-lookup"><span data-stu-id="e460b-137">Note how in the image above, the authentication middleware short-circuited the request.</span></span>

## <a name="migrating-module-code-to-middleware"></a><span data-ttu-id="e460b-138">Modül kodunu ara yazılıma geçirme</span><span class="sxs-lookup"><span data-stu-id="e460b-138">Migrating module code to middleware</span></span>

<span data-ttu-id="e460b-139">Var olan bir HTTP modülü şuna benzer olacaktır:</span><span class="sxs-lookup"><span data-stu-id="e460b-139">An existing HTTP module will look similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyModule.cs?highlight=6,8,24,31)]

<span data-ttu-id="e460b-140">[Ara](xref:fundamentals/middleware/index) yazılım sayfasında gösterildiği gibi, ASP.NET Core bir ara yazılım, bir `Invoke` `HttpContext` ve döndüren bir `Task`yöntemi ortaya çıkaran bir sınıftır.</span><span class="sxs-lookup"><span data-stu-id="e460b-140">As shown in the [Middleware](xref:fundamentals/middleware/index) page, an ASP.NET Core middleware is a class that exposes an `Invoke` method taking an `HttpContext` and returning a `Task`.</span></span> <span data-ttu-id="e460b-141">Yeni ara yazılım şu şekilde görünür:</span><span class="sxs-lookup"><span data-stu-id="e460b-141">Your new middleware will look like this:</span></span>

<a name="http-modules-usemiddleware"></a>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddleware.cs?highlight=9,13,20,24,28,30,32)]

<span data-ttu-id="e460b-142">Yukarıdaki ara yazılım şablonu, [Ara yazılım yazma](xref:fundamentals/middleware/write)bölümündeki bölümden alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="e460b-142">The preceding middleware template was taken from the section on [writing middleware](xref:fundamentals/middleware/write).</span></span>

<span data-ttu-id="e460b-143">*MyMiddlewareExtensions* yardımcı sınıfı, kendi ara ortamınızı sınıfınıza `Startup` yapılandırmayı kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="e460b-143">The *MyMiddlewareExtensions* helper class makes it easier to configure your middleware in your `Startup` class.</span></span> <span data-ttu-id="e460b-144">Yöntemi `UseMyMiddleware` , ara yazılım sınıfınızı istek ardışık düzenine ekler.</span><span class="sxs-lookup"><span data-stu-id="e460b-144">The `UseMyMiddleware` method adds your middleware class to the request pipeline.</span></span> <span data-ttu-id="e460b-145">Ara yazılım tarafından istenen hizmetler, ara yazılım oluşturucusuna eklenir.</span><span class="sxs-lookup"><span data-stu-id="e460b-145">Services required by the middleware get injected in the middleware's constructor.</span></span>

<a name="http-modules-shortcircuiting-middleware"></a>

<span data-ttu-id="e460b-146">Modülünüzün bir isteği sonlandırabilir, örneğin Kullanıcı yetkilendirilmezse:</span><span class="sxs-lookup"><span data-stu-id="e460b-146">Your module might terminate a request, for example if the user isn't authorized:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Modules/MyTerminatingModule.cs?highlight=9,10,11,12,13&name=snippet_Terminate)]

<span data-ttu-id="e460b-147">Bir ara yazılım, işlem hattındaki `Invoke` sonraki ara yazılıma çağrı gerçekleştirerek bunu işler.</span><span class="sxs-lookup"><span data-stu-id="e460b-147">A middleware handles this by not calling `Invoke` on the next middleware in the pipeline.</span></span> <span data-ttu-id="e460b-148">Yanıt işlem hattı üzerinden geri geldiğinde, önceki middlewares hala çağrıldığı için, bu, isteği tamamen sonlandıramadığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="e460b-148">Keep in mind that this doesn't fully terminate the request, because previous middlewares will still be invoked when the response makes its way back through the pipeline.</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyTerminatingMiddleware.cs?highlight=7,8&name=snippet_Terminate)]

<span data-ttu-id="e460b-149">Modülünüzün işlevselliğini yeni ara yazılıma geçirdiğinizde, `HttpContext` sınıf ASP.NET Core içinde önemli ölçüde değiştiğinden kodunuzun derlenmediğini fark edebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e460b-149">When you migrate your module's functionality to your new middleware, you may find that your code doesn't compile because the `HttpContext` class has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="e460b-150">[Daha sonra](#migrating-to-the-new-httpcontext), yeni ASP.NET Core HttpContext 'e nasıl geçirebileceğiniz göreceksiniz.</span><span class="sxs-lookup"><span data-stu-id="e460b-150">[Later on](#migrating-to-the-new-httpcontext), you'll see how to migrate to the new ASP.NET Core HttpContext.</span></span>

## <a name="migrating-module-insertion-into-the-request-pipeline"></a><span data-ttu-id="e460b-151">Modül ekleme isteği ardışık düzenine geçiriliyor</span><span class="sxs-lookup"><span data-stu-id="e460b-151">Migrating module insertion into the request pipeline</span></span>

<span data-ttu-id="e460b-152">HTTP modülleri, genellikle *Web. config*kullanılarak istek ardışık düzenine eklenir:</span><span class="sxs-lookup"><span data-stu-id="e460b-152">HTTP modules are typically added to the request pipeline using *Web.config*:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32-33,36,43,50,101)]

<span data-ttu-id="e460b-153">[Yeni ara ortamınızı](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) sınıfınızdaki `Startup` istek ardışık düzenine ekleyerek bunu dönüştürün:</span><span class="sxs-lookup"><span data-stu-id="e460b-153">Convert this by [adding your new middleware](xref:fundamentals/middleware/index#create-a-middleware-pipeline-with-iapplicationbuilder) to the request pipeline in your `Startup` class:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=16)]

<span data-ttu-id="e460b-154">Yeni ara ortamınızı eklediğiniz tam nokta, bir modül (`BeginRequest`, `EndRequest`, vb.) olarak işlenen olaya ve *Web. config*dosyasındaki modüller listenizde bulunan sıraya bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="e460b-154">The exact spot in the pipeline where you insert your new middleware depends on the event that it handled as a module (`BeginRequest`, `EndRequest`, etc.) and its order in your list of modules in *Web.config*.</span></span>

<span data-ttu-id="e460b-155">Daha önce belirtildiği gibi, ASP.NET Core hiçbir uygulama yaşam döngüsü yoktur ve bu işlem, yaşam döngüsünün işlenme sırası modüller tarafından kullanılan siparişten farklıdır.</span><span class="sxs-lookup"><span data-stu-id="e460b-155">As previously stated, there's no application life cycle in ASP.NET Core and the order in which responses are processed by middleware differs from the order used by modules.</span></span> <span data-ttu-id="e460b-156">Bu, sipariş kararlarınızı daha zor hale getirir.</span><span class="sxs-lookup"><span data-stu-id="e460b-156">This could make your ordering decision more challenging.</span></span>

<span data-ttu-id="e460b-157">Sıralama bir sorun haline gelirse, modülünüzü bağımsız olarak sıralanmış birden fazla ara yazılım bileşenine bölebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e460b-157">If ordering becomes a problem, you could split your module into multiple middleware components that can be ordered independently.</span></span>

## <a name="migrating-handler-code-to-middleware"></a><span data-ttu-id="e460b-158">İşleyici kodunu ara yazılıma geçirme</span><span class="sxs-lookup"><span data-stu-id="e460b-158">Migrating handler code to middleware</span></span>

<span data-ttu-id="e460b-159">HTTP işleyicisi şuna benzer:</span><span class="sxs-lookup"><span data-stu-id="e460b-159">An HTTP handler looks something like this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/HttpHandlers/ReportHandler.cs?highlight=5,7,13,14,15,16)]

<span data-ttu-id="e460b-160">ASP.NET Core projenizde bunu şuna benzer bir ara yazılıma çevirmeniz gerekir:</span><span class="sxs-lookup"><span data-stu-id="e460b-160">In your ASP.NET Core project, you would translate this to a middleware similar to this:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/ReportHandlerMiddleware.cs?highlight=7,9,13,20,21,22,23,40,42,44)]

<span data-ttu-id="e460b-161">Bu ara yazılım, modüllerle ilgili olan ara yazılıma çok benzer.</span><span class="sxs-lookup"><span data-stu-id="e460b-161">This middleware is very similar to the middleware corresponding to modules.</span></span> <span data-ttu-id="e460b-162">Gerçek fark, burada hiçbir çağrı olmadığı için `_next.Invoke(context)`geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="e460b-162">The only real difference is that here there's no call to `_next.Invoke(context)`.</span></span> <span data-ttu-id="e460b-163">Bu anlamlı hale gelir, çünkü işleyici istek ardışık düzeninin sonunda olduğundan, çağırmak için bir sonraki ara yazılım olmayacaktır.</span><span class="sxs-lookup"><span data-stu-id="e460b-163">That makes sense, because the handler is at the end of the request pipeline, so there will be no next middleware to invoke.</span></span>

## <a name="migrating-handler-insertion-into-the-request-pipeline"></a><span data-ttu-id="e460b-164">İşleyici ekleme isteği ardışık düzenine geçiriliyor</span><span class="sxs-lookup"><span data-stu-id="e460b-164">Migrating handler insertion into the request pipeline</span></span>

<span data-ttu-id="e460b-165">HTTP işleyicisini yapılandırma *Web. config* dosyasında yapılır ve şuna benzer:</span><span class="sxs-lookup"><span data-stu-id="e460b-165">Configuring an HTTP handler is done in *Web.config* and looks something like this:</span></span>

[!code-xml[](../migration/http-modules/sample/Asp.Net4/Asp.Net4/Web.config?highlight=6&range=1-3,32,46-48,50,101)]

<span data-ttu-id="e460b-166">Bu, yeni işleyici ara ortamınızı sınıfınızdaki `Startup` istek ardışık düzenine ekleyerek, modüllerden dönüştürülen ara yazılıma benzer şekilde dönüştürebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e460b-166">You could convert this by adding your new handler middleware to the request pipeline in your `Startup` class, similar to middleware converted from modules.</span></span> <span data-ttu-id="e460b-167">Bu yaklaşımla ilgili sorun, tüm istekleri yeni işleyici ara yazılıma göndermektir.</span><span class="sxs-lookup"><span data-stu-id="e460b-167">The problem with that approach is that it would send all requests to your new handler middleware.</span></span> <span data-ttu-id="e460b-168">Ancak, yalnızca belirli bir uzantıya sahip isteklerin ara yazılıma ulaşmasını istiyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="e460b-168">However, you only want requests with a given extension to reach your middleware.</span></span> <span data-ttu-id="e460b-169">Bu, size HTTP işleyiciniz ile aynı işlevselliği sağlar.</span><span class="sxs-lookup"><span data-stu-id="e460b-169">That would give you the same functionality you had with your HTTP handler.</span></span>

<span data-ttu-id="e460b-170">Bir çözüm, `MapWhen` genişletme yöntemi kullanılarak belirli bir uzantıya sahip istekler için ardışık düzeni dallandırmayı kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="e460b-170">One solution is to branch the pipeline for requests with a given extension, using the `MapWhen` extension method.</span></span> <span data-ttu-id="e460b-171">Bunu, diğer ara yazılımı eklediğiniz `Configure` yöntemde yapabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="e460b-171">You do this in the same `Configure` method where you add the other middleware:</span></span>

[!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=27-34)]

<span data-ttu-id="e460b-172">`MapWhen`Şu parametreleri alır:</span><span class="sxs-lookup"><span data-stu-id="e460b-172">`MapWhen` takes these parameters:</span></span>

1. <span data-ttu-id="e460b-173">' İ alan ve isteğin `HttpContext` dala gitmesi `true` gerekiyorsa, döndüren bir lambda.</span><span class="sxs-lookup"><span data-stu-id="e460b-173">A lambda that takes the `HttpContext` and returns `true` if the request should go down the branch.</span></span> <span data-ttu-id="e460b-174">Bu, yalnızca uzantısına göre değil, istek üst bilgileri, sorgu dizesi parametreleri vb. için istekleri dallayabileceğiniz anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="e460b-174">This means you can branch requests not just based on their extension, but also on request headers, query string parameters, etc.</span></span>

2. <span data-ttu-id="e460b-175">`IApplicationBuilder` Ve dalı için tüm ara yazılımı ekleyen bir lambda.</span><span class="sxs-lookup"><span data-stu-id="e460b-175">A lambda that takes an `IApplicationBuilder` and adds all the middleware for the branch.</span></span> <span data-ttu-id="e460b-176">Bu, işleyici ara uçınızın önünde dala ek ara yazılım ekleyebileceğiniz anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="e460b-176">This means you can add additional middleware to the branch in front of your handler middleware.</span></span>

<span data-ttu-id="e460b-177">Dal tüm isteklerde çağrılmadan önce işlem hattına eklenen ara yazılım; dalın üzerinde hiçbir etkisi olmaz.</span><span class="sxs-lookup"><span data-stu-id="e460b-177">Middleware added to the pipeline before the branch will be invoked on all requests; the branch will have no impact on them.</span></span>

## <a name="loading-middleware-options-using-the-options-pattern"></a><span data-ttu-id="e460b-178">Seçenekler modelini kullanarak ara yazılım seçeneklerini yükleme</span><span class="sxs-lookup"><span data-stu-id="e460b-178">Loading middleware options using the options pattern</span></span>

<span data-ttu-id="e460b-179">Bazı modüller ve işleyiciler, *Web. config*dosyasında depolanan yapılandırma seçeneklerine sahiptir. Ancak ASP.NET Core ' de, *Web. config*yerine yeni bir yapılandırma modeli kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e460b-179">Some modules and handlers have configuration options that are stored in *Web.config*. However, in ASP.NET Core a new configuration model is used in place of *Web.config*.</span></span>

<span data-ttu-id="e460b-180">Yeni [yapılandırma sistemi](xref:fundamentals/configuration/index) , bunu çözümlemek için size bu seçenekleri sunar:</span><span class="sxs-lookup"><span data-stu-id="e460b-180">The new [configuration system](xref:fundamentals/configuration/index) gives you these options to solve this:</span></span>

* <span data-ttu-id="e460b-181">[Sonraki bölümde](#loading-middleware-options-through-direct-injection)gösterildiği gibi, seçenekleri doğrudan ara yazılıma ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e460b-181">Directly inject the options into the middleware, as shown in the [next section](#loading-middleware-options-through-direct-injection).</span></span>

* <span data-ttu-id="e460b-182">[Seçenekler modelini](xref:fundamentals/configuration/options)kullanın:</span><span class="sxs-lookup"><span data-stu-id="e460b-182">Use the [options pattern](xref:fundamentals/configuration/options):</span></span>

1. <span data-ttu-id="e460b-183">Ara yazılım seçeneklerinizi barındıracak bir sınıf oluşturun, örneğin:</span><span class="sxs-lookup"><span data-stu-id="e460b-183">Create a class to hold your middleware options, for example:</span></span>

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Options)]

2. <span data-ttu-id="e460b-184">Seçenek değerlerini depolama</span><span class="sxs-lookup"><span data-stu-id="e460b-184">Store the option values</span></span>

   <span data-ttu-id="e460b-185">Yapılandırma sistemi istediğiniz her yerde seçenek değerlerini depolamanıza olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="e460b-185">The configuration system allows you to store option values anywhere you want.</span></span> <span data-ttu-id="e460b-186">Ancak, çoğu site *appSettings. JSON*kullanır, bu nedenle bu yaklaşımı ele alacağız:</span><span class="sxs-lookup"><span data-stu-id="e460b-186">However, most sites use *appsettings.json*, so we'll take that approach:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,14-18)]

   <span data-ttu-id="e460b-187">Burada *MyMiddlewareOptionsSection* bir bölüm adıdır.</span><span class="sxs-lookup"><span data-stu-id="e460b-187">*MyMiddlewareOptionsSection* here is a section name.</span></span> <span data-ttu-id="e460b-188">Seçenek sınıfınızın adıyla aynı olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="e460b-188">It doesn't have to be the same as the name of your options class.</span></span>

3. <span data-ttu-id="e460b-189">Seçenek değerlerini Options sınıfıyla ilişkilendirin</span><span class="sxs-lookup"><span data-stu-id="e460b-189">Associate the option values with the options class</span></span>

    <span data-ttu-id="e460b-190">Seçenekler stili, seçenek türünü (gibi `MyMiddlewareOptions`) gerçek seçeneklere sahip bir `MyMiddlewareOptions` nesneyle ilişkilendirmek için ASP.NET Core bağımlılık ekleme çerçevesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="e460b-190">The options pattern uses ASP.NET Core's dependency injection framework to associate the options type (such as `MyMiddlewareOptions`) with a `MyMiddlewareOptions` object that has the actual options.</span></span>

    <span data-ttu-id="e460b-191">`Startup` Sınıfınızı güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="e460b-191">Update your `Startup` class:</span></span>

   1. <span data-ttu-id="e460b-192">*AppSettings. JSON*kullanıyorsanız, `Startup` oluşturucunun yapılandırma oluşturucusuna ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e460b-192">If you're using *appsettings.json*, add it to the configuration builder in the `Startup` constructor:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Ctor&highlight=5-6)]

   2. <span data-ttu-id="e460b-193">Seçenekler hizmetini yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="e460b-193">Configure the options service:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

   3. <span data-ttu-id="e460b-194">Seçeneklerinizi seçenekler sınıfınız ile ilişkilendirin:</span><span class="sxs-lookup"><span data-stu-id="e460b-194">Associate your options with your options class:</span></span>

      [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_ConfigureServices&highlight=6-8)]

4. <span data-ttu-id="e460b-195">Seçenekleri, ara yazılım yapıcısına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e460b-195">Inject the options into your middleware constructor.</span></span> <span data-ttu-id="e460b-196">Bu, ekleme seçeneklerine bir denetleyiciye benzer.</span><span class="sxs-lookup"><span data-stu-id="e460b-196">This is similar to injecting options into a controller.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_MiddlewareWithParams&highlight=4,7,10,15-16)]

   <span data-ttu-id="e460b-197">Ara ortamınızı bağımlılık ekleme ' ye `IApplicationBuilder` ekleyen [useara yazılım](#http-modules-usemiddleware) genişletme yöntemi.</span><span class="sxs-lookup"><span data-stu-id="e460b-197">The [UseMiddleware](#http-modules-usemiddleware) extension method that adds your middleware to the `IApplicationBuilder` takes care of dependency injection.</span></span>

   <span data-ttu-id="e460b-198">Bu `IOptions` nesnelerle sınırlı değildir.</span><span class="sxs-lookup"><span data-stu-id="e460b-198">This isn't limited to `IOptions` objects.</span></span> <span data-ttu-id="e460b-199">Bu şekilde, ara yazılım için gereken diğer tüm nesneler bu şekilde eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="e460b-199">Any other object that your middleware requires can be injected this way.</span></span>

## <a name="loading-middleware-options-through-direct-injection"></a><span data-ttu-id="e460b-200">Doğrudan ekleme yoluyla ara yazılım seçeneklerini yükleme</span><span class="sxs-lookup"><span data-stu-id="e460b-200">Loading middleware options through direct injection</span></span>

<span data-ttu-id="e460b-201">Seçenekler deseninin, seçenek değerleri ve tüketicileri arasında gevşek bir bağlantısını oluşturduğunu avantajı vardır.</span><span class="sxs-lookup"><span data-stu-id="e460b-201">The options pattern has the advantage that it creates loose coupling between options values and their consumers.</span></span> <span data-ttu-id="e460b-202">Bir seçenek sınıfını gerçek seçenek değerleriyle ilişkilendirdikten sonra, diğer herhangi bir sınıf, bağımlılık ekleme çerçevesi aracılığıyla seçeneklere erişim alabilir.</span><span class="sxs-lookup"><span data-stu-id="e460b-202">Once you've associated an options class with the actual options values, any other class can get access to the options through the dependency injection framework.</span></span> <span data-ttu-id="e460b-203">Seçenek değerlerini geçirmeye gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="e460b-203">There's no need to pass around options values.</span></span>

<span data-ttu-id="e460b-204">Bu, farklı seçeneklerle aynı ara yazılımı iki kez kullanmak istiyorsanız bu seçeneği keser.</span><span class="sxs-lookup"><span data-stu-id="e460b-204">This breaks down though if you want to use the same middleware twice, with different options.</span></span> <span data-ttu-id="e460b-205">Örneğin farklı dallarda farklı rollere izin veren bir yetkilendirme ara yazılımı.</span><span class="sxs-lookup"><span data-stu-id="e460b-205">For example an authorization middleware used in different branches allowing different roles.</span></span> <span data-ttu-id="e460b-206">İki farklı seçenek nesnesini tek bir seçenek sınıfıyla ilişkilendiremezsiniz.</span><span class="sxs-lookup"><span data-stu-id="e460b-206">You can't associate two different options objects with the one options class.</span></span>

<span data-ttu-id="e460b-207">Çözüm, seçenek nesnelerini sınıfınıza `Startup` gerçek seçenek değerleriyle almak ve bunları doğrudan ara yazılım örneğine iletmektir.</span><span class="sxs-lookup"><span data-stu-id="e460b-207">The solution is to get the options objects with the actual options values in your `Startup` class and pass those directly to each instance of your middleware.</span></span>

1. <span data-ttu-id="e460b-208">*AppSettings. JSON* öğesine ikinci bir anahtar ekleyin</span><span class="sxs-lookup"><span data-stu-id="e460b-208">Add a second key to *appsettings.json*</span></span>

   <span data-ttu-id="e460b-209">*AppSettings. JSON* dosyasına ikinci bir seçenek kümesi eklemek için, benzersiz bir şekilde tanımlamak için yeni bir anahtar kullanın:</span><span class="sxs-lookup"><span data-stu-id="e460b-209">To add a second set of options to the *appsettings.json* file, use a new key to uniquely identify it:</span></span>

   [!code-json[](http-modules/sample/Asp.Net.Core/appsettings.json?range=1,10-18&highlight=2-5)]

2. <span data-ttu-id="e460b-210">Seçenek değerlerini alın ve bunları ara yazılıma geçirin.</span><span class="sxs-lookup"><span data-stu-id="e460b-210">Retrieve options values and pass them to middleware.</span></span> <span data-ttu-id="e460b-211">`Use...` Genişletme yöntemi (ara yazılımını işlem hattına ekleyen), seçenek değerlerinde geçirilecek mantıksal bir yerdir:</span><span class="sxs-lookup"><span data-stu-id="e460b-211">The `Use...` extension method (which adds your middleware to the pipeline) is a logical place to pass in the option values:</span></span> 

   [!code-csharp[](http-modules/sample/Asp.Net.Core/Startup.cs?name=snippet_Configure&highlight=20-23)]

3. <span data-ttu-id="e460b-212">Bir seçenek parametresi almak için ara yazılımı etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="e460b-212">Enable middleware to take an options parameter.</span></span> <span data-ttu-id="e460b-213">`Use...` Uzantı yönteminin bir aşırı yüklemesini sağlayın (Options parametresini alır ve ' ye `UseMiddleware`geçirir).</span><span class="sxs-lookup"><span data-stu-id="e460b-213">Provide an overload of the `Use...` extension method (that takes the options parameter and passes it to `UseMiddleware`).</span></span> <span data-ttu-id="e460b-214">`UseMiddleware` Parametreler ile çağrıldığında, ara yazılım nesnesini örnekleyen zaman, parametreleri ara yazılım yapıcısına geçirir.</span><span class="sxs-lookup"><span data-stu-id="e460b-214">When `UseMiddleware` is called with parameters, it passes the parameters to your middleware constructor when it instantiates the middleware object.</span></span>

   [!code-csharp[](../migration/http-modules/sample/Asp.Net.Core/Middleware/MyMiddlewareWithParams.cs?name=snippet_Extensions&highlight=9-14)]

   <span data-ttu-id="e460b-215">Bunun `OptionsWrapper` nesne içindeki Options nesnesini nasıl saryacağını aklınızda edin.</span><span class="sxs-lookup"><span data-stu-id="e460b-215">Note how this wraps the options object in an `OptionsWrapper` object.</span></span> <span data-ttu-id="e460b-216">Bu, `IOptions`ara yazılım oluşturucusu tarafından beklendiği şekilde uygulanır.</span><span class="sxs-lookup"><span data-stu-id="e460b-216">This implements `IOptions`, as expected by the middleware constructor.</span></span>

## <a name="migrating-to-the-new-httpcontext"></a><span data-ttu-id="e460b-217">Yeni HttpContext 'e geçiş</span><span class="sxs-lookup"><span data-stu-id="e460b-217">Migrating to the new HttpContext</span></span>

<span data-ttu-id="e460b-218">Daha önce, `Invoke` ara ortamınızdaki yöntemin türünde `HttpContext`bir parametre alıp aldıklarını gördünüz:</span><span class="sxs-lookup"><span data-stu-id="e460b-218">You saw earlier that the `Invoke` method in your middleware takes a parameter of type `HttpContext`:</span></span>

```csharp
public async Task Invoke(HttpContext context)
```

<span data-ttu-id="e460b-219">`HttpContext`ASP.NET Core içinde önemli ölçüde değişmiştir.</span><span class="sxs-lookup"><span data-stu-id="e460b-219">`HttpContext` has significantly changed in ASP.NET Core.</span></span> <span data-ttu-id="e460b-220">Bu bölümde, en yaygın olarak kullanılan [System. Web. HttpContext](/dotnet/api/system.web.httpcontext) özelliklerinin yeni `Microsoft.AspNetCore.Http.HttpContext`sürümüne nasıl çevrilebileceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="e460b-220">This section shows how to translate the most commonly used properties of [System.Web.HttpContext](/dotnet/api/system.web.httpcontext) to the new `Microsoft.AspNetCore.Http.HttpContext`.</span></span>

### <a name="httpcontext"></a><span data-ttu-id="e460b-221">HttpContext</span><span class="sxs-lookup"><span data-stu-id="e460b-221">HttpContext</span></span>

<span data-ttu-id="e460b-222">**HttpContext. Items** şu şekilde çevirir:</span><span class="sxs-lookup"><span data-stu-id="e460b-222">**HttpContext.Items** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Items)]

<span data-ttu-id="e460b-223">**Benzersiz istek KIMLIĞI (System. Web. HttpContext karşılığı)**</span><span class="sxs-lookup"><span data-stu-id="e460b-223">**Unique request ID (no System.Web.HttpContext counterpart)**</span></span>

<span data-ttu-id="e460b-224">Size her istek için benzersiz bir kimlik verir.</span><span class="sxs-lookup"><span data-stu-id="e460b-224">Gives you a unique id for each request.</span></span> <span data-ttu-id="e460b-225">Günlüklerinizi eklemek çok yararlı.</span><span class="sxs-lookup"><span data-stu-id="e460b-225">Very useful to include in your logs.</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Trace)]

### <a name="httpcontextrequest"></a><span data-ttu-id="e460b-226">HttpContext. Request</span><span class="sxs-lookup"><span data-stu-id="e460b-226">HttpContext.Request</span></span>

<span data-ttu-id="e460b-227">**HttpContext. Request. HttpMethod** şu şekilde çevirir:</span><span class="sxs-lookup"><span data-stu-id="e460b-227">**HttpContext.Request.HttpMethod** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Method)]

<span data-ttu-id="e460b-228">**HttpContext. Request. QueryString** şu şekilde çevirir:</span><span class="sxs-lookup"><span data-stu-id="e460b-228">**HttpContext.Request.QueryString** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Query)]

<span data-ttu-id="e460b-229">**HttpContext. Request. URL** ve **HttpContext. Request. RawUrl** şu şekilde çeviri yapar:</span><span class="sxs-lookup"><span data-stu-id="e460b-229">**HttpContext.Request.Url** and **HttpContext.Request.RawUrl** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Url)]

<span data-ttu-id="e460b-230">**HttpContext. Request. IsSecureConnection** şu şekilde çevirir:</span><span class="sxs-lookup"><span data-stu-id="e460b-230">**HttpContext.Request.IsSecureConnection** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Secure)]

<span data-ttu-id="e460b-231">**HttpContext. Request. UserHostAddress** şu şekilde çevirir:</span><span class="sxs-lookup"><span data-stu-id="e460b-231">**HttpContext.Request.UserHostAddress** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Host)]

<span data-ttu-id="e460b-232">**HttpContext. Request. Cookies** şu şekilde çevirir:</span><span class="sxs-lookup"><span data-stu-id="e460b-232">**HttpContext.Request.Cookies** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Cookies)]

<span data-ttu-id="e460b-233">**HttpContext. Request. RequestContext. RouteData** şu şekilde çevirir:</span><span class="sxs-lookup"><span data-stu-id="e460b-233">**HttpContext.Request.RequestContext.RouteData** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Route)]

<span data-ttu-id="e460b-234">**HttpContext. Request. Headers** şu şekilde çevirir:</span><span class="sxs-lookup"><span data-stu-id="e460b-234">**HttpContext.Request.Headers** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Headers)]

<span data-ttu-id="e460b-235">**HttpContext. Request. UserAgent** şu şekilde çevirir:</span><span class="sxs-lookup"><span data-stu-id="e460b-235">**HttpContext.Request.UserAgent** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Agent)]

<span data-ttu-id="e460b-236">**HttpContext. Request. Urlbaşvuran** şu şekilde çevirir:</span><span class="sxs-lookup"><span data-stu-id="e460b-236">**HttpContext.Request.UrlReferrer** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Referrer)]

<span data-ttu-id="e460b-237">**HttpContext. Request. ContentType şuna** çevirir:</span><span class="sxs-lookup"><span data-stu-id="e460b-237">**HttpContext.Request.ContentType** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Type)]

<span data-ttu-id="e460b-238">**HttpContext. Request. form** şu şekilde çevirir:</span><span class="sxs-lookup"><span data-stu-id="e460b-238">**HttpContext.Request.Form** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Form)]

> [!WARNING]
> <span data-ttu-id="e460b-239">Form değerlerini yalnızca içerik alt türü *x-www-form-urlencoded* veya *form-Data*ise okuyun.</span><span class="sxs-lookup"><span data-stu-id="e460b-239">Read form values only if the content sub type is *x-www-form-urlencoded* or *form-data*.</span></span>

<span data-ttu-id="e460b-240">**HttpContext. Request. InputStream** şu şekilde çevirir:</span><span class="sxs-lookup"><span data-stu-id="e460b-240">**HttpContext.Request.InputStream** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Input)]

> [!WARNING]
> <span data-ttu-id="e460b-241">Bu kodu yalnızca bir işlem hattının sonundaki bir işleyici türü ara yazılım içinde kullanın.</span><span class="sxs-lookup"><span data-stu-id="e460b-241">Use this code only in a handler type middleware, at the end of a pipeline.</span></span>
>
><span data-ttu-id="e460b-242">Ham gövdeyi, her istek için yukarıda yalnızca bir kez gösterildiği gibi okuyabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e460b-242">You can read the raw body as shown above only once per request.</span></span> <span data-ttu-id="e460b-243">İlk okuduktan sonra gövde okumayı deneyen ara yazılım boş bir gövde okur.</span><span class="sxs-lookup"><span data-stu-id="e460b-243">Middleware trying to read the body after the first read will read an empty body.</span></span>
>
><span data-ttu-id="e460b-244">Bu, bir arabelleğin yapıldığı için daha önce gösterildiği gibi bir formu okumak için uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="e460b-244">This doesn't apply to reading a form as shown earlier, because that's done from a buffer.</span></span>

### <a name="httpcontextresponse"></a><span data-ttu-id="e460b-245">HttpContext. Response</span><span class="sxs-lookup"><span data-stu-id="e460b-245">HttpContext.Response</span></span>

<span data-ttu-id="e460b-246">**HttpContext. Response. Status** ve **HttpContext. Response. StatusDescription** şu şekilde çeviri yapar:</span><span class="sxs-lookup"><span data-stu-id="e460b-246">**HttpContext.Response.Status** and **HttpContext.Response.StatusDescription** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Status)]

<span data-ttu-id="e460b-247">**HttpContext. Response. Contentenkodlamaya** ve **HttpContext. Response. ContentType şuna** çevir:</span><span class="sxs-lookup"><span data-stu-id="e460b-247">**HttpContext.Response.ContentEncoding** and **HttpContext.Response.ContentType** translate to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespType)]

<span data-ttu-id="e460b-248">Yalnızca **HttpContext. Response. ContentType şuna** çevrilir:</span><span class="sxs-lookup"><span data-stu-id="e460b-248">**HttpContext.Response.ContentType** on its own also translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_RespTypeOnly)]

<span data-ttu-id="e460b-249">**HttpContext. Response. Output** şu şekilde çevirir:</span><span class="sxs-lookup"><span data-stu-id="e460b-249">**HttpContext.Response.Output** translates to:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_Output)]

<span data-ttu-id="e460b-250">**HttpContext. Response. TransmitFile**</span><span class="sxs-lookup"><span data-stu-id="e460b-250">**HttpContext.Response.TransmitFile**</span></span>

<span data-ttu-id="e460b-251">Bir dosya sunma [burada](../fundamentals/request-features.md#middleware-and-request-features)ele alınmıştır.</span><span class="sxs-lookup"><span data-stu-id="e460b-251">Serving up a file is discussed [here](../fundamentals/request-features.md#middleware-and-request-features).</span></span>

<span data-ttu-id="e460b-252">**HttpContext. Response. Headers**</span><span class="sxs-lookup"><span data-stu-id="e460b-252">**HttpContext.Response.Headers**</span></span>

<span data-ttu-id="e460b-253">Yanıt üst bilgilerinin gönderilmesi, yanıt gövdesine herhangi bir şey yazıldıktan sonra ayarlandıklarında, gönderilmeyeceği için karmaşıktır.</span><span class="sxs-lookup"><span data-stu-id="e460b-253">Sending response headers is complicated by the fact that if you set them after anything has been written to the response body, they will not be sent.</span></span>

<span data-ttu-id="e460b-254">Çözüm, yanıt başladıktan sonra hemen çağrılacak bir geri çağırma yöntemi ayarlamak olacaktır.</span><span class="sxs-lookup"><span data-stu-id="e460b-254">The solution is to set a callback method that will be called right before writing to the response starts.</span></span> <span data-ttu-id="e460b-255">Bu en iyi işlem, ara ortamınızdaki `Invoke` yöntemin başlangıcında yapılır.</span><span class="sxs-lookup"><span data-stu-id="e460b-255">This is best done at the start of the `Invoke` method in your middleware.</span></span> <span data-ttu-id="e460b-256">Yanıt başlıklarınızı ayarlayan bu geri çağırma yöntemidir.</span><span class="sxs-lookup"><span data-stu-id="e460b-256">It's this callback method that sets your response headers.</span></span>

<span data-ttu-id="e460b-257">Aşağıdaki kod, adlı `SetHeaders`bir geri çağırma yöntemi ayarlar:</span><span class="sxs-lookup"><span data-stu-id="e460b-257">The following code sets a callback method called `SetHeaders`:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="e460b-258">`SetHeaders` Geri çağırma yöntemi şöyle görünür:</span><span class="sxs-lookup"><span data-stu-id="e460b-258">The `SetHeaders` callback method would look like this:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetHeaders)]

<span data-ttu-id="e460b-259">**HttpContext. Response. Cookies**</span><span class="sxs-lookup"><span data-stu-id="e460b-259">**HttpContext.Response.Cookies**</span></span>

<span data-ttu-id="e460b-260">Tanımlama bilgileri bir *set-Cookie* yanıt üst bilgisinde tarayıcıya seyahat ediyor.</span><span class="sxs-lookup"><span data-stu-id="e460b-260">Cookies travel to the browser in a *Set-Cookie* response header.</span></span> <span data-ttu-id="e460b-261">Sonuç olarak, tanımlama bilgilerinin gönderilmesi yanıt üst bilgilerini göndermek için kullanılan geri çağırma işlemini gerektirir:</span><span class="sxs-lookup"><span data-stu-id="e460b-261">As a result, sending cookies requires the same callback as used for sending response headers:</span></span>

```csharp
public async Task Invoke(HttpContext httpContext)
{
    // ...
    httpContext.Response.OnStarting(SetCookies, state: httpContext);
    httpContext.Response.OnStarting(SetHeaders, state: httpContext);
```

<span data-ttu-id="e460b-262">`SetCookies` Geri çağırma yöntemi aşağıdaki gibi görünür:</span><span class="sxs-lookup"><span data-stu-id="e460b-262">The `SetCookies` callback method would look like the following:</span></span>

[!code-csharp[](http-modules/sample/Asp.Net.Core/Middleware/HttpContextDemoMiddleware.cs?name=snippet_SetCookies)]

## <a name="additional-resources"></a><span data-ttu-id="e460b-263">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e460b-263">Additional resources</span></span>

* [<span data-ttu-id="e460b-264">HTTP Işleyicileri ve HTTP modüllerine genel bakış</span><span class="sxs-lookup"><span data-stu-id="e460b-264">HTTP Handlers and HTTP Modules Overview</span></span>](/iis/configuration/system.webserver/)
* [<span data-ttu-id="e460b-265">Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e460b-265">Configuration</span></span>](xref:fundamentals/configuration/index)
* [<span data-ttu-id="e460b-266">Uygulama başlatma</span><span class="sxs-lookup"><span data-stu-id="e460b-266">Application Startup</span></span>](xref:fundamentals/startup)
* [<span data-ttu-id="e460b-267">Ara yazılım</span><span class="sxs-lookup"><span data-stu-id="e460b-267">Middleware</span></span>](xref:fundamentals/middleware/index)
