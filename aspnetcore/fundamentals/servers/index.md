---
title: ASP.NET Core Web sunucusu uygulamaları
author: rick-anderson
description: Kestrel için Web sunucularını bulun ve ASP.NET Core HTTP.sys. Sunucu seçme ve ters proxy sunucusu ne zaman kullanılacağı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/servers/index
ms.openlocfilehash: a27fdd70963830d22b3501972d6150dde5e1ea54
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059513"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="7076f-104">ASP.NET Core Web sunucusu uygulamaları</span><span class="sxs-lookup"><span data-stu-id="7076f-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="7076f-105">[Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen halter](https://twitter.com/halter73)ve [Chris](https://github.com/Tratcher) 'e göre</span><span class="sxs-lookup"><span data-stu-id="7076f-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="7076f-106">ASP.NET Core bir uygulama, işlem içi HTTP sunucu uygulamasıyla çalışır.</span><span class="sxs-lookup"><span data-stu-id="7076f-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="7076f-107">Sunucu uygulaması, HTTP isteklerini dinler ve bunları uygulamaya oluşturulan [istek özellikleri](xref:fundamentals/request-features) kümesi olarak uygulamaya sunar <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="7076f-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

## <a name="kestrel"></a><span data-ttu-id="7076f-108">Kestrel</span><span class="sxs-lookup"><span data-stu-id="7076f-108">Kestrel</span></span>

<span data-ttu-id="7076f-109">Kestrel, ASP.NET Core projesi şablonları tarafından belirtilen varsayılan Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="7076f-109">Kestrel is the default web server specified by the ASP.NET Core project templates.</span></span>

<span data-ttu-id="7076f-110">Kestrel kullanın:</span><span class="sxs-lookup"><span data-stu-id="7076f-110">Use Kestrel:</span></span>

* <span data-ttu-id="7076f-111">Kendi başına bir uç sunucu olarak, Internet dahil olmak üzere istekleri doğrudan bir ağdan işleme.</span><span class="sxs-lookup"><span data-stu-id="7076f-111">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Kestrel, ters proxy sunucusu olmadan doğrudan Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="7076f-113">[Internet Information Services (IIS)](https://www.iis.net/), [NGINX](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi bir *ters ara sunucu* ile.</span><span class="sxs-lookup"><span data-stu-id="7076f-113">With a *reverse proxy server* , such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="7076f-114">Ters proxy sunucusu, Internet 'ten gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="7076f-114">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Kestrel, IIS, NGINX veya Apache gibi bir ters ara sunucu üzerinden Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="7076f-116">&mdash;Ters ara sunucu sunucusuyla ya da olmadan barındırma yapılandırması &mdash; desteklenir.</span><span class="sxs-lookup"><span data-stu-id="7076f-116">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported.</span></span>

<span data-ttu-id="7076f-117">Kestrel yapılandırma kılavuzu ve bir ters proxy yapılandırmasında Kestrel 'in ne zaman kullanılacağı hakkında bilgi için, bkz <xref:fundamentals/servers/kestrel> ..</span><span class="sxs-lookup"><span data-stu-id="7076f-117">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="7076f-118">Windows</span><span class="sxs-lookup"><span data-stu-id="7076f-118">Windows</span></span>](#tab/windows)

<span data-ttu-id="7076f-119">ASP.NET Core aşağıdakiler ile birlikte gelir:</span><span class="sxs-lookup"><span data-stu-id="7076f-119">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="7076f-120">[Kestrel sunucusu](xref:fundamentals/servers/kestrel) varsayılan, platformlar arası http sunucu uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="7076f-120">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span>
* <span data-ttu-id="7076f-121">IIS HTTP sunucusu, IIS için bir [işlem içi sunucusudur](#hosting-models) .</span><span class="sxs-lookup"><span data-stu-id="7076f-121">IIS HTTP Server is an [in-process server](#hosting-models) for IIS.</span></span>
* <span data-ttu-id="7076f-122">[HTTP.sys sunucusu](xref:fundamentals/servers/httpsys) , [HTTP.sys çekırdek sürücüsünü ve http sunucusu API](/windows/desktop/Http/http-api-start-page)'sini temel alan bir yalnızca Windows HTTP sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="7076f-122">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="7076f-123">[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)kullanırken, uygulama şu şekilde çalışır:</span><span class="sxs-lookup"><span data-stu-id="7076f-123">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="7076f-124">IIS çalışan işlemiyle aynı işlemde ( [işlem içi barındırma modeli](#hosting-models)) IIS HTTP sunucusu ile.</span><span class="sxs-lookup"><span data-stu-id="7076f-124">In the same process as the IIS worker process (the [in-process hosting model](#hosting-models)) with the IIS HTTP Server.</span></span> <span data-ttu-id="7076f-125">*Işlem içi* önerilen yapılandırmadır.</span><span class="sxs-lookup"><span data-stu-id="7076f-125">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="7076f-126">Bir işlemde, IIS çalışan işleminden ( [işlem dışı barındırma modeli](#hosting-models)) [Kestrel sunucusuyla](#kestrel)ayırın.</span><span class="sxs-lookup"><span data-stu-id="7076f-126">In a process separate from the IIS worker process (the [out-of-process hosting model](#hosting-models)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="7076f-127">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) , IIS ile Işlem ıçı IIS HTTP sunucusu ya da Kestrel arasında yerel IIS isteklerini işleyen yerel bir IIS modülüdür.</span><span class="sxs-lookup"><span data-stu-id="7076f-127">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="7076f-128">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="7076f-128">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="7076f-129">Barındırma modelleri</span><span class="sxs-lookup"><span data-stu-id="7076f-129">Hosting models</span></span>

<span data-ttu-id="7076f-130">ASP.NET Core bir uygulama, işlem içi barındırma kullanarak IIS çalışan işlemiyle aynı işlemde çalışır.</span><span class="sxs-lookup"><span data-stu-id="7076f-130">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="7076f-131">İşlem içi barındırma, istek dışı barındırmak için gelişmiş performans sağlar çünkü istekler, giden ağ trafiği ile aynı makineye geri döndürülen bir ağ arabirimidir.</span><span class="sxs-lookup"><span data-stu-id="7076f-131">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="7076f-132">IIS, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)ile işlem yönetimini işler.</span><span class="sxs-lookup"><span data-stu-id="7076f-132">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="7076f-133">İşlem dışı barındırma kullanma, ASP.NET Core uygulamalar IIS çalışan işleminden ayrı bir işlemde çalışır ve modül işlem yönetimini işler.</span><span class="sxs-lookup"><span data-stu-id="7076f-133">Using out-of-process hosting, ASP.NET Core apps run in a process separate from the IIS worker process, and the module handles process management.</span></span> <span data-ttu-id="7076f-134">Modül, ilk istek ulaştığında ASP.NET Core uygulama için işlemi başlatır ve kapanırsa veya kilitlenirse uygulamayı yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="7076f-134">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="7076f-135">Bu aslında, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen işlem içi uygulamalarla birlikte görülen davranışdır.</span><span class="sxs-lookup"><span data-stu-id="7076f-135">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="7076f-136">Daha fazla bilgi ve yapılandırma kılavuzu için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="7076f-136">For more information and configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="7076f-137">macOS</span><span class="sxs-lookup"><span data-stu-id="7076f-137">macOS</span></span>](#tab/macos)

<span data-ttu-id="7076f-138">ASP.NET Core, varsayılan, platformlar arası HTTP sunucusu olan [Kestrel Server](xref:fundamentals/servers/kestrel)ile birlikte gönderilir.</span><span class="sxs-lookup"><span data-stu-id="7076f-138">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="7076f-139">Linux</span><span class="sxs-lookup"><span data-stu-id="7076f-139">Linux</span></span>](#tab/linux)

<span data-ttu-id="7076f-140">ASP.NET Core, varsayılan, platformlar arası HTTP sunucusu olan [Kestrel Server](xref:fundamentals/servers/kestrel)ile birlikte gönderilir.</span><span class="sxs-lookup"><span data-stu-id="7076f-140">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="7076f-141">Windows</span><span class="sxs-lookup"><span data-stu-id="7076f-141">Windows</span></span>](#tab/windows)

<span data-ttu-id="7076f-142">ASP.NET Core aşağıdakiler ile birlikte gelir:</span><span class="sxs-lookup"><span data-stu-id="7076f-142">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="7076f-143">[Kestrel sunucusu](xref:fundamentals/servers/kestrel) , platformlar arası varsayılan HTTP sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="7076f-143">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="7076f-144">[HTTP.sys sunucusu](xref:fundamentals/servers/httpsys) , [HTTP.sys çekırdek sürücüsünü ve http sunucusu API](/windows/desktop/Http/http-api-start-page)'sini temel alan bir yalnızca Windows HTTP sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="7076f-144">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="7076f-145">[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)kullanırken, uygulama IIS çalışan işleminden ( *işlem dışı* ) [Kestrel sunucusu](#kestrel)ile ayrı bir işlemde çalışır.</span><span class="sxs-lookup"><span data-stu-id="7076f-145">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process ( *out-of-process* ) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="7076f-146">ASP.NET Core uygulamalar IIS çalışan işleminden ayrı bir işlemde çalıştığından, modül işlem yönetimini işler.</span><span class="sxs-lookup"><span data-stu-id="7076f-146">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="7076f-147">Modül, ilk istek ulaştığında ASP.NET Core uygulama için işlemi başlatır ve kapanırsa veya kilitlenirse uygulamayı yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="7076f-147">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="7076f-148">Bu aslında, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen işlem içi uygulamalarla birlikte görülen davranışdır.</span><span class="sxs-lookup"><span data-stu-id="7076f-148">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="7076f-149">Aşağıdaki diyagramda IIS, ASP.NET Core modülü ve işlem dışı barındırılan bir uygulama arasındaki ilişki gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="7076f-149">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core Modülü](_static/ancm-outofprocess.png)

<span data-ttu-id="7076f-151">İstekler Web 'den çekirdek modu HTTP.sys sürücüsüne ulaşır.</span><span class="sxs-lookup"><span data-stu-id="7076f-151">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="7076f-152">Sürücü, istekleri Web sitesinin yapılandırılmış bağlantı noktasında IIS 'ye yönlendirir, genellikle 80 (HTTP) veya 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="7076f-152">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="7076f-153">Modül, 80 veya 443 numaralı bağlantı noktası olmayan uygulama için rastgele bir bağlantı noktasında istekleri Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="7076f-153">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="7076f-154">Modül, başlangıç sırasında bir ortam değişkeni aracılığıyla bağlantı noktasını belirtir ve [IIS tümleştirme ara yazılımı](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) sunucuyu dinleyecek şekilde yapılandırır `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="7076f-154">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="7076f-155">Ek denetimler gerçekleştirilir ve modülünden kaynaklanmayan istekler reddedilir.</span><span class="sxs-lookup"><span data-stu-id="7076f-155">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="7076f-156">Modül HTTPS iletmeyi desteklemez, bu nedenle istekler HTTPS üzerinden IIS tarafından alınsa bile HTTP üzerinden iletilir.</span><span class="sxs-lookup"><span data-stu-id="7076f-156">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="7076f-157">Kestrel, isteği modülden başlattıktan sonra, istek ASP.NET Core ara yazılım ardışık düzenine gönderilir.</span><span class="sxs-lookup"><span data-stu-id="7076f-157">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="7076f-158">Ara yazılım ardışık düzeni isteği işler ve `HttpContext` uygulamanın mantığına bir örnek olarak geçirir.</span><span class="sxs-lookup"><span data-stu-id="7076f-158">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="7076f-159">IIS tümleştirmesi tarafından eklenen ara yazılım, isteği Kestrel iletmek için düzen, uzak IP ve pathbase 'i hesaba göre güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="7076f-159">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="7076f-160">Uygulamanın yanıtı IIS 'e geri geçirilir ve bu, isteği başlatan HTTP istemcisine geri gönderilir.</span><span class="sxs-lookup"><span data-stu-id="7076f-160">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="7076f-161">IIS ve ASP.NET Core modülü yapılandırma kılavuzu için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="7076f-161">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="7076f-162">macOS</span><span class="sxs-lookup"><span data-stu-id="7076f-162">macOS</span></span>](#tab/macos)

<span data-ttu-id="7076f-163">ASP.NET Core, varsayılan, platformlar arası HTTP sunucusu olan [Kestrel Server](xref:fundamentals/servers/kestrel)ile birlikte gönderilir.</span><span class="sxs-lookup"><span data-stu-id="7076f-163">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="7076f-164">Linux</span><span class="sxs-lookup"><span data-stu-id="7076f-164">Linux</span></span>](#tab/linux)

<span data-ttu-id="7076f-165">ASP.NET Core, varsayılan, platformlar arası HTTP sunucusu olan [Kestrel Server](xref:fundamentals/servers/kestrel)ile birlikte gönderilir.</span><span class="sxs-lookup"><span data-stu-id="7076f-165">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="7076f-166">Kestrel ile NGINX</span><span class="sxs-lookup"><span data-stu-id="7076f-166">Nginx with Kestrel</span></span>

<span data-ttu-id="7076f-167">Linux üzerinde NGINX 'i Kestrel için ters proxy sunucusu olarak kullanma hakkında daha fazla bilgi için bkz <xref:host-and-deploy/linux-nginx> ..</span><span class="sxs-lookup"><span data-stu-id="7076f-167">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="7076f-168">Kestrel ile Apache</span><span class="sxs-lookup"><span data-stu-id="7076f-168">Apache with Kestrel</span></span>

<span data-ttu-id="7076f-169">Linux üzerinde Apache 'yi Kestrel için ters proxy sunucusu olarak kullanma hakkında daha fazla bilgi için bkz <xref:host-and-deploy/linux-apache> ..</span><span class="sxs-lookup"><span data-stu-id="7076f-169">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="7076f-170">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="7076f-170">HTTP.sys</span></span>

<span data-ttu-id="7076f-171">ASP.NET Core uygulamalar Windows üzerinde çalışıyorsa HTTP.sys, Kestrel için bir alternatiftir.</span><span class="sxs-lookup"><span data-stu-id="7076f-171">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="7076f-172">Kestrel genellikle en iyi performans için önerilir.</span><span class="sxs-lookup"><span data-stu-id="7076f-172">Kestrel is generally recommended for best performance.</span></span> <span data-ttu-id="7076f-173">HTTP.sys, uygulamanın Internet 'e sunulabileceği senaryolarda ve gerekli yetenekler, HTTP.sys tarafından desteklenmediği durumlarda kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7076f-173">HTTP.sys can be used in scenarios where the app is exposed to the Internet and required capabilities are supported by HTTP.sys but not Kestrel.</span></span> <span data-ttu-id="7076f-174">Daha fazla bilgi için bkz. <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="7076f-174">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![HTTP.sys doğrudan Internet ile iletişim kurar](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="7076f-176">HTTP.sys, yalnızca bir iç ağa açık olan uygulamalar için de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7076f-176">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![HTTP.sys doğrudan iç ağla iletişim kurar](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="7076f-178">HTTP.sys yapılandırma kılavuzu için bkz <xref:fundamentals/servers/httpsys> ..</span><span class="sxs-lookup"><span data-stu-id="7076f-178">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="7076f-179">ASP.NET Core Server altyapısı</span><span class="sxs-lookup"><span data-stu-id="7076f-179">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="7076f-180"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> `Startup.Configure` Yönteminde bulunan <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> özelliği türünün özelliğini kullanıma sunar <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection> .</span><span class="sxs-lookup"><span data-stu-id="7076f-180">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="7076f-181">Kestrel ve HTTP.sys her biri yalnızca tek bir özellik sunar, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> ancak farklı sunucu uygulamaları ek işlevsellik sergilede gösterebilir.</span><span class="sxs-lookup"><span data-stu-id="7076f-181">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="7076f-182">`IServerAddressesFeature` sunucu uygulamasının çalışma zamanında hangi bağlantı noktasını bağladığına ilişkin bilgi edinmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7076f-182">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="7076f-183">Özel sunucular</span><span class="sxs-lookup"><span data-stu-id="7076f-183">Custom servers</span></span>

<span data-ttu-id="7076f-184">Yerleşik sunucular uygulamanın gereksinimlerini karşılamıyorsa, özel bir sunucu uygulaması oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="7076f-184">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="7076f-185">[.Net Için açık Web arabirimi (OWıN) Kılavuzu](xref:fundamentals/owin) , [nowin](https://github.com/Bobris/Nowin)tabanlı bir uygulamanın nasıl yazılacağını gösterir <xref:Microsoft.AspNetCore.Hosting.Server.IServer> .</span><span class="sxs-lookup"><span data-stu-id="7076f-185">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="7076f-186">Yalnızca uygulamanın kullandığı Özellik arabirimleri, en azından <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> ve <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> desteklenmesi gereken uygulama gerektirir.</span><span class="sxs-lookup"><span data-stu-id="7076f-186">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="7076f-187">Sunucu başlatma</span><span class="sxs-lookup"><span data-stu-id="7076f-187">Server startup</span></span>

<span data-ttu-id="7076f-188">Tümleşik geliştirme ortamı (IDE) veya düzenleyici uygulamayı başlattığında sunucu başlatılır:</span><span class="sxs-lookup"><span data-stu-id="7076f-188">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="7076f-189">[Visual Studio](https://visualstudio.microsoft.com): başlatma profilleri, uygulamayı ve sunucuyu [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) / [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) ya da konsolundan başlatmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7076f-189">[Visual Studio](https://visualstudio.microsoft.com): Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="7076f-190">[Visual Studio Code](https://code.visualstudio.com/): uygulama ve sunucu [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode)tarafından başlatılır ve bu, CoreCLR hata ayıklayıcısını etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="7076f-190">[Visual Studio Code](https://code.visualstudio.com/): The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="7076f-191">[Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/): uygulama ve sunucu [mono Soft-Mode hata ayıklayıcısı](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/)tarafından başlatılır.</span><span class="sxs-lookup"><span data-stu-id="7076f-191">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/): The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="7076f-192">Uygulamanın, projenin klasöründeki bir komut isteminden başlatılması sırasında [DotNet Run](/dotnet/core/tools/dotnet-run) uygulamayı ve sunucuyu başlatır (yalnızca Kestrel ve HTTP.sys).</span><span class="sxs-lookup"><span data-stu-id="7076f-192">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="7076f-193">Yapılandırma `-c|--configuration` seçeneğiyle belirtilir, `Debug` (varsayılan) veya olarak ayarlanır `Release` .</span><span class="sxs-lookup"><span data-stu-id="7076f-193">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span>

<span data-ttu-id="7076f-194">Dosyadaki bir *launchSettings.js* `dotnet run` , Visual Studio gibi araç ile yerleşik bir hata ayıklayıcı ile veya bir uygulama başlatırken yapılandırma sağlar.</span><span class="sxs-lookup"><span data-stu-id="7076f-194">A *launchSettings.json* file provides configuration when launching an app with `dotnet run` or with a debugger built into tooling, such as Visual Studio.</span></span> <span data-ttu-id="7076f-195">Bir *launchSettings.jsdosya üzerinde* başlatma profilleri varsa, `--launch-profile {PROFILE NAME}` komutuyla seçeneğini kullanın `dotnet run` veya Visual Studio 'da profili seçin.</span><span class="sxs-lookup"><span data-stu-id="7076f-195">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile {PROFILE NAME}` option with the `dotnet run` command or select the profile in Visual Studio.</span></span> <span data-ttu-id="7076f-196">Daha fazla bilgi için bkz. [DotNet Run](/dotnet/core/tools/dotnet-run) ve [.NET Core Distribution paketleme](/dotnet/core/build/distribution-packaging).</span><span class="sxs-lookup"><span data-stu-id="7076f-196">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="7076f-197">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="7076f-197">HTTP/2 support</span></span>

<span data-ttu-id="7076f-198">[Http/2](https://httpwg.org/specs/rfc7540.html) aşağıdaki dağıtım senaryolarında ASP.NET Core desteklenir:</span><span class="sxs-lookup"><span data-stu-id="7076f-198">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="7076f-199">Kestrel</span><span class="sxs-lookup"><span data-stu-id="7076f-199">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="7076f-200">İşletim sistemi</span><span class="sxs-lookup"><span data-stu-id="7076f-200">Operating system</span></span>
    * <span data-ttu-id="7076f-201">Windows Server 2016/Windows 10 veya üzeri&dagger;</span><span class="sxs-lookup"><span data-stu-id="7076f-201">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="7076f-202">OpenSSL 1.0.2 veya üzerini içeren Linux (örneğin, Ubuntu 16,04 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="7076f-202">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="7076f-203">HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="7076f-203">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="7076f-204">Hedef Framework: .NET Core 2,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="7076f-204">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="7076f-205">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="7076f-205">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="7076f-206">Windows Server 2016/Windows 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="7076f-206">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="7076f-207">Hedef Framework: HTTP.sys dağıtımlar için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="7076f-207">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="7076f-208">IIS (işlem içi)</span><span class="sxs-lookup"><span data-stu-id="7076f-208">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="7076f-209">Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="7076f-209">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="7076f-210">Hedef Framework: .NET Core 2,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="7076f-210">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="7076f-211">IIS (işlem dışı)</span><span class="sxs-lookup"><span data-stu-id="7076f-211">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="7076f-212">Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="7076f-212">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="7076f-213">Herkese açık uç sunucu bağlantıları HTTP/2 kullanır, ancak Kestrel ile ters proxy bağlantısı HTTP/1.1 kullanır.</span><span class="sxs-lookup"><span data-stu-id="7076f-213">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="7076f-214">Hedef Framework: IIS işlem dışı dağıtımlar için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="7076f-214">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="7076f-215">&dagger;Kestrel, Windows Server 2012 R2 ve Windows 8.1 'de HTTP/2 için sınırlı destek içerir.</span><span class="sxs-lookup"><span data-stu-id="7076f-215">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="7076f-216">Bu işletim sistemlerinde kullanılabilir olan desteklenen TLS şifre paketlerinin listesi sınırlı olduğundan destek sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="7076f-216">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="7076f-217">TLS bağlantılarının güvenliğini sağlamak için Eliptik Eğri dijital Imza algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="7076f-217">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="7076f-218">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="7076f-218">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="7076f-219">Windows Server 2016/Windows 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="7076f-219">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="7076f-220">Hedef Framework: HTTP.sys dağıtımlar için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="7076f-220">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="7076f-221">IIS (işlem dışı)</span><span class="sxs-lookup"><span data-stu-id="7076f-221">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="7076f-222">Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="7076f-222">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="7076f-223">Herkese açık uç sunucu bağlantıları HTTP/2 kullanır, ancak Kestrel ile ters proxy bağlantısı HTTP/1.1 kullanır.</span><span class="sxs-lookup"><span data-stu-id="7076f-223">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="7076f-224">Hedef Framework: IIS işlem dışı dağıtımlar için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="7076f-224">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="7076f-225">Bir HTTP/2 bağlantısı, [uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) ve TLS 1,2 veya üstünü kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7076f-225">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="7076f-226">Daha fazla bilgi için, sunucu dağıtım senaryolarınıza ait konulara bakın.</span><span class="sxs-lookup"><span data-stu-id="7076f-226">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7076f-227">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7076f-227">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
