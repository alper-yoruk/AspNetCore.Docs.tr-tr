---
title: ASP.NET Core'daki web sunucusu uygulamaları
author: rick-anderson
description: ASP.NET Core için kerkenez ve HTTP.sys web sunucularını keşfedin. Sunucunasıl seçeceğinizi ve ters proxy sunucusunun ne zaman kullanılacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: fundamentals/servers/index
ms.openlocfilehash: d46793ef54c99fe609b5983c5a658fb7b20032fa
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666343"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="2299a-104">ASP.NET Core'daki web sunucusu uygulamaları</span><span class="sxs-lookup"><span data-stu-id="2299a-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="2299a-105">Tarafından [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), Stephen [Halter](https://twitter.com/halter73), ve [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="2299a-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="2299a-106">Core ASP.NET uygulaması, işlem içi http sunucu uygulamasıyla çalışır.</span><span class="sxs-lookup"><span data-stu-id="2299a-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="2299a-107">Sunucu uygulaması HTTP isteklerini dinler ve bunları bir . [request features](xref:fundamentals/request-features) <xref:Microsoft.AspNetCore.Http.HttpContext></span><span class="sxs-lookup"><span data-stu-id="2299a-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

## <a name="kestrel"></a><span data-ttu-id="2299a-108">Kestrel</span><span class="sxs-lookup"><span data-stu-id="2299a-108">Kestrel</span></span>

<span data-ttu-id="2299a-109">Kerkenez, ASP.NET Core proje şablonları tarafından belirtilen varsayılan web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="2299a-109">Kestrel is the default web server specified by the ASP.NET Core project templates.</span></span>

<span data-ttu-id="2299a-110">Kerkenez kullanın:</span><span class="sxs-lookup"><span data-stu-id="2299a-110">Use Kestrel:</span></span>

* <span data-ttu-id="2299a-111">Tek başına bir kenar sunucu işleme isteklerini doğrudan internet de dahil olmak üzere bir ağdan alır.</span><span class="sxs-lookup"><span data-stu-id="2299a-111">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Kerkenez ters proxy sunucusu olmadan internet ile doğrudan iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="2299a-113">[Internet Information Services (IIS) ,](https://www.iis.net/) [Nginx](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi *ters proxy sunucusu*ile.</span><span class="sxs-lookup"><span data-stu-id="2299a-113">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="2299a-114">Ters proxy sunucusu Internet'ten HTTP isteklerini alır ve bunları Kestrel'e ileter.</span><span class="sxs-lookup"><span data-stu-id="2299a-114">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Kerkenez, IIS, Nginx veya Apache gibi ters proxy sunucusu aracılığıyla Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="2299a-116">Ters proxy&mdash;sunucusu&mdash;olan veya olmayan yapılandırmayı barındırma desteklenir.</span><span class="sxs-lookup"><span data-stu-id="2299a-116">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported.</span></span>

<span data-ttu-id="2299a-117">Kestrel yapılandırma kılavuzu ve ters proxy yapılandırmasında Kerkenez'in <xref:fundamentals/servers/kestrel>ne zaman kullanılacağı hakkında bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="2299a-117">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="2299a-118">Windows</span><span class="sxs-lookup"><span data-stu-id="2299a-118">Windows</span></span>](#tab/windows)

<span data-ttu-id="2299a-119">ASP.NET Core gemileri aşağıdakilerle birlikte:</span><span class="sxs-lookup"><span data-stu-id="2299a-119">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="2299a-120">[Kerkenez sunucu](xref:fundamentals/servers/kestrel) varsayılan, çapraz platform HTTP sunucu uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="2299a-120">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span>
* <span data-ttu-id="2299a-121">IIS HTTP Server, IIS için işlem aşamasında bir [sunucudur.](#hosting-models)</span><span class="sxs-lookup"><span data-stu-id="2299a-121">IIS HTTP Server is an [in-process server](#hosting-models) for IIS.</span></span>
* <span data-ttu-id="2299a-122">[HTTP.sys sunucusu,](xref:fundamentals/servers/httpsys) [HTTP.sys çekirdek sürücüsü ve HTTP Server API'ye](/windows/desktop/Http/http-api-start-page)dayalı bir Windows'a özel HTTP sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="2299a-122">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="2299a-123">[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)kullanırken uygulama aşağıdakileri çalıştırarak çalışır:</span><span class="sxs-lookup"><span data-stu-id="2299a-123">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="2299a-124">IIS HTTP Server ile IIS alt işlemi [(süreç içi barındırma modeli)](#hosting-models)ile aynı süreçte.</span><span class="sxs-lookup"><span data-stu-id="2299a-124">In the same process as the IIS worker process (the [in-process hosting model](#hosting-models)) with the IIS HTTP Server.</span></span> <span data-ttu-id="2299a-125">*Süreç içi* önerilen yapılandırmadır.</span><span class="sxs-lookup"><span data-stu-id="2299a-125">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="2299a-126">[Kerkenez sunucusu](#kestrel)ile IIS alt işlemden [(işlem dışı barındırma modeli)](#hosting-models)ayrı bir işlemde.</span><span class="sxs-lookup"><span data-stu-id="2299a-126">In a process separate from the IIS worker process (the [out-of-process hosting model](#hosting-models)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="2299a-127">[ASP.NET Çekirdek Modülü,](xref:host-and-deploy/aspnet-core-module) IIS ile işlem deki IIS HTTP Server veya Kestrel arasındaki yerel IIS isteklerini işleyen yerel bir IIS modülüdür.</span><span class="sxs-lookup"><span data-stu-id="2299a-127">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="2299a-128">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="2299a-128">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="2299a-129">Barındırma modelleri</span><span class="sxs-lookup"><span data-stu-id="2299a-129">Hosting models</span></span>

<span data-ttu-id="2299a-130">ASP.NET Core uygulaması, süreç içi barındırmayı kullanarak IIS alt işlemiyle aynı işlemde çalışır.</span><span class="sxs-lookup"><span data-stu-id="2299a-130">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="2299a-131">İstekler giden ağ trafiğini aynı makineye geri döndüren bir ağ arabirimi olan döngü bağdaştırıcısı üzerinden yakınlamadığından, işlem dışı barındırma işlemi barındırma üzerinde gelişmiş performans sağlar.</span><span class="sxs-lookup"><span data-stu-id="2299a-131">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="2299a-132">IIS, Windows Process [Etkinleştirme Hizmeti (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)ile işlem yönetimini işler.</span><span class="sxs-lookup"><span data-stu-id="2299a-132">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="2299a-133">İşlem dışı barındırma yı kullanarak, ASP.NET Core uygulamaları IIS alt işleminden ayrı bir işlemle çalışır ve modül süreç yönetimini işler.</span><span class="sxs-lookup"><span data-stu-id="2299a-133">Using out-of-process hosting, ASP.NET Core apps run in a process separate from the IIS worker process, and the module handles process management.</span></span> <span data-ttu-id="2299a-134">Modül, ilk istek geldiğinde ASP.NET Core uygulaması için süreci başlatır ve kapanDığında veya çökerse uygulamayı yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="2299a-134">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="2299a-135">Bu aslında [Windows Process Etkinleştirme Hizmeti (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen süreç içinde çalışan uygulamalarda görülen aynı davranıştır.</span><span class="sxs-lookup"><span data-stu-id="2299a-135">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="2299a-136">Daha fazla bilgi ve yapılandırma kılavuzu için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="2299a-136">For more information and configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="2299a-137">macOS</span><span class="sxs-lookup"><span data-stu-id="2299a-137">macOS</span></span>](#tab/macos)

<span data-ttu-id="2299a-138">ASP.NET Core, varsayılan olarak, çapraz platform HTTP sunucusu olan [Kestrel sunucusuile](xref:fundamentals/servers/kestrel)birlikte gemilere biner.</span><span class="sxs-lookup"><span data-stu-id="2299a-138">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="2299a-139">Linux</span><span class="sxs-lookup"><span data-stu-id="2299a-139">Linux</span></span>](#tab/linux)

<span data-ttu-id="2299a-140">ASP.NET Core, varsayılan olarak, çapraz platform HTTP sunucusu olan [Kestrel sunucusuile](xref:fundamentals/servers/kestrel)birlikte gemilere biner.</span><span class="sxs-lookup"><span data-stu-id="2299a-140">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="2299a-141">Windows</span><span class="sxs-lookup"><span data-stu-id="2299a-141">Windows</span></span>](#tab/windows)

<span data-ttu-id="2299a-142">ASP.NET Core gemileri aşağıdakilerle birlikte:</span><span class="sxs-lookup"><span data-stu-id="2299a-142">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="2299a-143">[Kerkenez sunucu](xref:fundamentals/servers/kestrel) varsayılan, çapraz platform HTTP sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="2299a-143">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="2299a-144">[HTTP.sys sunucusu,](xref:fundamentals/servers/httpsys) [HTTP.sys çekirdek sürücüsü ve HTTP Server API'ye](/windows/desktop/Http/http-api-start-page)dayalı bir Windows'a özel HTTP sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="2299a-144">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="2299a-145">[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)kullanırken, uygulama [Kestrel sunucusu](#kestrel)ile IIS alt işleminden ayrı bir işlemde *(işlem dışı)* çalışır.</span><span class="sxs-lookup"><span data-stu-id="2299a-145">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="2299a-146">ASP.NET Core uygulamaları IIS alt işleminden ayrı bir işlemde çalıştığı için, modül işlem yönetimini işler.</span><span class="sxs-lookup"><span data-stu-id="2299a-146">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="2299a-147">Modül, ilk istek geldiğinde ASP.NET Core uygulaması için süreci başlatır ve kapanDığında veya çökerse uygulamayı yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="2299a-147">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="2299a-148">Bu aslında [Windows Process Etkinleştirme Hizmeti (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen süreç içinde çalışan uygulamalarda görülen aynı davranıştır.</span><span class="sxs-lookup"><span data-stu-id="2299a-148">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="2299a-149">Aşağıdaki diyagram, IIS, ASP.NET Çekirdek Modülü ve işlem dışı barındırılan bir uygulama arasındaki ilişkiyi göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="2299a-149">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core Modülü](_static/ancm-outofprocess.png)

<span data-ttu-id="2299a-151">İstekler web'den çekirdek modu HTTP.sys sürücüsüne gelir.</span><span class="sxs-lookup"><span data-stu-id="2299a-151">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="2299a-152">Sürücü, istekleri genellikle 80 (HTTP) veya 443 (HTTPS) olarak web sitesinin yapılandırılmış bağlantı noktasında IIS'ye yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="2299a-152">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="2299a-153">Modül, 80 veya 443 bağlantı noktası olmayan uygulama için rastgele bir bağlantı noktası üzerindeki istekleri Kestrel'e iletiyor.</span><span class="sxs-lookup"><span data-stu-id="2299a-153">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="2299a-154">Modül başlangıçta bir ortam değişkeni üzerinden bağlantı noktasını belirtir ve [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) sunucuyu dinleyecek şekilde `http://localhost:{port}`yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="2299a-154">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="2299a-155">Ek denetimler gerçekleştirilir ve modülden kaynaklanmıyor istekleri reddedilir.</span><span class="sxs-lookup"><span data-stu-id="2299a-155">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="2299a-156">Modül HTTPS iletmesini desteklemez, bu nedenle iIS tarafından HTTPS üzerinden alınsa bile istekler HTTP üzerinden iletilir.</span><span class="sxs-lookup"><span data-stu-id="2299a-156">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="2299a-157">Kestrel modülden isteği aldıktan sonra, istek ASP.NET Core ara yazılım boru hattına itilir.</span><span class="sxs-lookup"><span data-stu-id="2299a-157">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="2299a-158">Ara yazılım ardışık alanı isteği işler ve `HttpContext` uygulamanın mantığına örnek olarak aktarın.</span><span class="sxs-lookup"><span data-stu-id="2299a-158">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="2299a-159">IIS Integration tarafından eklenen ara yazılım, isteği Kestrel'e iletmek için hesap vermek üzere şemayı, uzak IP'yi ve yol tabanını güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="2299a-159">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="2299a-160">Uygulamanın yanıtı IIS'ye geri aktarılır ve bu da isteği başlatan HTTP istemcisine geri iter.</span><span class="sxs-lookup"><span data-stu-id="2299a-160">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="2299a-161">IIS ve ASP.NET Core Module yapılandırma kılavuzu için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="2299a-161">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="2299a-162">macOS</span><span class="sxs-lookup"><span data-stu-id="2299a-162">macOS</span></span>](#tab/macos)

<span data-ttu-id="2299a-163">ASP.NET Core, varsayılan olarak, çapraz platform HTTP sunucusu olan [Kestrel sunucusuile](xref:fundamentals/servers/kestrel)birlikte gemilere biner.</span><span class="sxs-lookup"><span data-stu-id="2299a-163">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="2299a-164">Linux</span><span class="sxs-lookup"><span data-stu-id="2299a-164">Linux</span></span>](#tab/linux)

<span data-ttu-id="2299a-165">ASP.NET Core, varsayılan olarak, çapraz platform HTTP sunucusu olan [Kestrel sunucusuile](xref:fundamentals/servers/kestrel)birlikte gemilere biner.</span><span class="sxs-lookup"><span data-stu-id="2299a-165">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="2299a-166">Kerkenez ile Nginx</span><span class="sxs-lookup"><span data-stu-id="2299a-166">Nginx with Kestrel</span></span>

<span data-ttu-id="2299a-167">Kerkenez için ters proxy sunucusu olarak Linux'ta Nginx'in nasıl kullanılacağı hakkında bilgi için bkz. <xref:host-and-deploy/linux-nginx></span><span class="sxs-lookup"><span data-stu-id="2299a-167">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="2299a-168">Kerkenez ile Apaçi</span><span class="sxs-lookup"><span data-stu-id="2299a-168">Apache with Kestrel</span></span>

<span data-ttu-id="2299a-169">Kestrel için ters proxy sunucusu olarak Linux'ta Apache'nin nasıl kullanılacağı hakkında bilgi için bkz. <xref:host-and-deploy/linux-apache></span><span class="sxs-lookup"><span data-stu-id="2299a-169">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="2299a-170">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="2299a-170">HTTP.sys</span></span>

<span data-ttu-id="2299a-171">ASP.NET Core uygulamaları Windows'da çalıştırılıyorsa, HTTP.sys Kestrel'e alternatiftir.</span><span class="sxs-lookup"><span data-stu-id="2299a-171">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="2299a-172">Kerkenez genellikle en iyi performans için tavsiye edilir.</span><span class="sxs-lookup"><span data-stu-id="2299a-172">Kestrel is generally recommended for best performance.</span></span> <span data-ttu-id="2299a-173">HTTP.sys, uygulamanın Internet'e maruz kaldığı ve gerekli özelliklerin KEStrel tarafından desteklendiği senaryolarda kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2299a-173">HTTP.sys can be used in scenarios where the app is exposed to the Internet and required capabilities are supported by HTTP.sys but not Kestrel.</span></span> <span data-ttu-id="2299a-174">Daha fazla bilgi için bkz. <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="2299a-174">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![HTTP.sys internet ile doğrudan iletişim kurar](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="2299a-176">HTTP.sys, yalnızca dahili bir ağa maruz kalan uygulamalar için de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2299a-176">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![HTTP.sys dahili ağ ile doğrudan iletişim kurar](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="2299a-178">HTTP.sys yapılandırma kılavuzu <xref:fundamentals/servers/httpsys>için bkz.</span><span class="sxs-lookup"><span data-stu-id="2299a-178">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="2299a-179">ASP.NET Core sunucu altyapısı</span><span class="sxs-lookup"><span data-stu-id="2299a-179">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="2299a-180">Yöntemde <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> kullanılabilir türü <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>özelliğini ortaya çıkarır. `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="2299a-180">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="2299a-181">Kerkenez ve HTTP.sys yalnızca tek <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>bir özelliği ortaya çıkarır, ancak farklı sunucu uygulamaları ek işlevselliği ortaya çıkarabilir.</span><span class="sxs-lookup"><span data-stu-id="2299a-181">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="2299a-182">`IServerAddressesFeature`sunucu uygulamasının çalışma zamanında hangi bağlantı noktasına bağlı olduğunu bulmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2299a-182">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="2299a-183">Özel sunucular</span><span class="sxs-lookup"><span data-stu-id="2299a-183">Custom servers</span></span>

<span data-ttu-id="2299a-184">Yerleşik sunucular uygulamanın gereksinimlerini karşılamazsa, özel bir sunucu uygulaması oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="2299a-184">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="2299a-185">[.NET (OWIN) için Açık Web Arabirimi kılavuzu,](xref:fundamentals/owin) [Nowin](https://github.com/Bobris/Nowin)tabanlı <xref:Microsoft.AspNetCore.Hosting.Server.IServer> bir uygulamanın nasıl yazılacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="2299a-185">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="2299a-186">Yalnızca uygulamanın kullandığı özellik arabirimleri, en azından <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> uygulanması nı gerektirir ve desteklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="2299a-186">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="2299a-187">Sunucu başlatma</span><span class="sxs-lookup"><span data-stu-id="2299a-187">Server startup</span></span>

<span data-ttu-id="2299a-188">Sunucu, Tümleşik Geliştirme Ortamı (IDE) veya düzenleyici uygulamayı başlattığında başlatılır:</span><span class="sxs-lookup"><span data-stu-id="2299a-188">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="2299a-189">[Visual Studio](https://visualstudio.microsoft.com) &ndash; Launch profilleri, uygulamayı ve sunucuyu [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) veya konsolile başlatmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2299a-189">[Visual Studio](https://visualstudio.microsoft.com) &ndash; Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="2299a-190">[Görsel Stüdyo Kodu](https://code.visualstudio.com/) &ndash; Uygulama ve sunucu [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode)tarafından başlatılır , Hangi CoreCLR hata ayıklayıcı etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="2299a-190">[Visual Studio Code](https://code.visualstudio.com/) &ndash; The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="2299a-191">[Mac](https://visualstudio.microsoft.com/vs/mac/) &ndash; için Visual Studio Uygulama ve sunucu [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/)tarafından başlatılır.</span><span class="sxs-lookup"><span data-stu-id="2299a-191">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) &ndash; The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="2299a-192">Uygulamayı projenin klasöründeki bir komut isteminden başlatırken, [dotnet run](/dotnet/core/tools/dotnet-run) uygulamayı ve sunucuyu başlatır (yalnızca Kestrel ve HTTP.sys).</span><span class="sxs-lookup"><span data-stu-id="2299a-192">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="2299a-193">Yapılandırma, (varsayılan) `-c|--configuration` veya `Debug` `Release`. olarak ayarlanan seçenek tarafından belirtilir.</span><span class="sxs-lookup"><span data-stu-id="2299a-193">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span>

<span data-ttu-id="2299a-194">*LaunchSettings.json* dosyası, Visual Studio gibi `dotnet run` araç yapısına yerleşik bir hata ayıklayıcıile veya hata ayıklayıcıyla bir uygulama başlatırken yapılandırma sağlar.</span><span class="sxs-lookup"><span data-stu-id="2299a-194">A *launchSettings.json* file provides configuration when launching an app with `dotnet run` or with a debugger built into tooling, such as Visual Studio.</span></span> <span data-ttu-id="2299a-195">Başlatma profilleri *launchSettings.json* dosyasında varsa, `--launch-profile {PROFILE NAME}` `dotnet run` komutla birlikte seçeneği kullanın veya Visual Studio'daki profili seçin.</span><span class="sxs-lookup"><span data-stu-id="2299a-195">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile {PROFILE NAME}` option with the `dotnet run` command or select the profile in Visual Studio.</span></span> <span data-ttu-id="2299a-196">Daha fazla bilgi için [dotnet çalıştır](/dotnet/core/tools/dotnet-run) ve [.NET Core dağıtım ambalajına](/dotnet/core/build/distribution-packaging)bakın.</span><span class="sxs-lookup"><span data-stu-id="2299a-196">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="2299a-197">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="2299a-197">HTTP/2 support</span></span>

<span data-ttu-id="2299a-198">[HTTP/2](https://httpwg.org/specs/rfc7540.html) aşağıdaki dağıtım senaryolarında ASP.NET Core ile desteklenir:</span><span class="sxs-lookup"><span data-stu-id="2299a-198">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

* [<span data-ttu-id="2299a-199">Kestrel</span><span class="sxs-lookup"><span data-stu-id="2299a-199">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="2299a-200">İşletim sistemi</span><span class="sxs-lookup"><span data-stu-id="2299a-200">Operating system</span></span>
    * <span data-ttu-id="2299a-201">Windows Server 2016/Windows 10 veya sonrası&dagger;</span><span class="sxs-lookup"><span data-stu-id="2299a-201">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="2299a-202">OpenSSL 1.0.2 veya sonrası (örneğin, Ubuntu 16.04 veya sonrası) ile Linux</span><span class="sxs-lookup"><span data-stu-id="2299a-202">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="2299a-203">HTTP/2 gelecekte yayınlanacak bir sürümde macOS'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="2299a-203">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="2299a-204">Hedef çerçeve: .NET Core 2.2 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="2299a-204">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="2299a-205">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="2299a-205">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="2299a-206">Windows Server 2016/Windows 10 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="2299a-206">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="2299a-207">Hedef çerçeve: HTTP.sys dağıtımları için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="2299a-207">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="2299a-208">IIS (süreç içinde)</span><span class="sxs-lookup"><span data-stu-id="2299a-208">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="2299a-209">Windows Server 2016/Windows 10 veya sonrası; IIS 10 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="2299a-209">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="2299a-210">Hedef çerçeve: .NET Core 2.2 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="2299a-210">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="2299a-211">IIS (işlem dışı)</span><span class="sxs-lookup"><span data-stu-id="2299a-211">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="2299a-212">Windows Server 2016/Windows 10 veya sonrası; IIS 10 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="2299a-212">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="2299a-213">Genel kullanıma dönük kenar sunucusu bağlantıları HTTP/2'yi kullanır, ancak Kestrel'e ters proxy bağlantısı HTTP/1.1 kullanır.</span><span class="sxs-lookup"><span data-stu-id="2299a-213">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="2299a-214">Hedef çerçeve: IIS işlem dışı dağıtımlar için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="2299a-214">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="2299a-215">&dagger;Kerkenez, Windows Server 2012 R2 ve Windows 8.1'de HTTP/2 için sınırlı bir desteğe sahiptir.</span><span class="sxs-lookup"><span data-stu-id="2299a-215">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="2299a-216">Bu işletim sistemlerinde bulunan desteklenen TLS şifreleme paketlerinin listesi sınırlı olduğundan destek sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="2299a-216">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="2299a-217">TLS bağlantılarını güvence altına almak için Eliptik Eğri Dijital İmza Algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="2299a-217">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="2299a-218">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="2299a-218">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="2299a-219">Windows Server 2016/Windows 10 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="2299a-219">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="2299a-220">Hedef çerçeve: HTTP.sys dağıtımları için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="2299a-220">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="2299a-221">IIS (işlem dışı)</span><span class="sxs-lookup"><span data-stu-id="2299a-221">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="2299a-222">Windows Server 2016/Windows 10 veya sonrası; IIS 10 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="2299a-222">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="2299a-223">Genel kullanıma dönük kenar sunucusu bağlantıları HTTP/2'yi kullanır, ancak Kestrel'e ters proxy bağlantısı HTTP/1.1 kullanır.</span><span class="sxs-lookup"><span data-stu-id="2299a-223">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="2299a-224">Hedef çerçeve: IIS işlem dışı dağıtımlar için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="2299a-224">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="2299a-225">BIR HTTP/2 [bağlantısı, Uygulama Katmanı Protokol Anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) ve TLS 1.2 veya daha sonra kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2299a-225">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="2299a-226">Daha fazla bilgi için sunucu dağıtım senaryolarınız ile ilgili konulara bakın.</span><span class="sxs-lookup"><span data-stu-id="2299a-226">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2299a-227">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2299a-227">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
