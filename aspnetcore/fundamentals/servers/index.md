---
title: ASP.NET Core Web sunucusu uygulamaları
author: rick-anderson
description: Kestrel için Web sunucularını bulun ve ASP.NET Core HTTP.sys. Sunucu seçme ve ters proxy sunucusu ne zaman kullanılacağı hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
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
uid: fundamentals/servers/index
ms.openlocfilehash: 2acddd212639ac0a82b3c46f2225ff66d0999dd0
ms.sourcegitcommit: 7e394a8527c9818caebb940f692ae4fcf2f1b277
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217563"
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="8310e-104">ASP.NET Core Web sunucusu uygulamaları</span><span class="sxs-lookup"><span data-stu-id="8310e-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="8310e-105">[Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen halter](https://twitter.com/halter73)ve [Chris](https://github.com/Tratcher) 'e göre</span><span class="sxs-lookup"><span data-stu-id="8310e-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="8310e-106">ASP.NET Core bir uygulama, işlem içi HTTP sunucu uygulamasıyla çalışır.</span><span class="sxs-lookup"><span data-stu-id="8310e-106">An ASP.NET Core app runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="8310e-107">Sunucu uygulaması, HTTP isteklerini dinler ve bunları uygulamaya oluşturulan [istek özellikleri](xref:fundamentals/request-features) kümesi olarak uygulamaya sunar <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="8310e-107">The server implementation listens for HTTP requests and surfaces them to the app as a set of [request features](xref:fundamentals/request-features) composed into an <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

::: moniker range=">= aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="8310e-108">Windows</span><span class="sxs-lookup"><span data-stu-id="8310e-108">Windows</span></span>](#tab/windows)

<span data-ttu-id="8310e-109">ASP.NET Core aşağıdakiler ile birlikte gelir:</span><span class="sxs-lookup"><span data-stu-id="8310e-109">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="8310e-110">[Kestrel sunucusu](xref:fundamentals/servers/kestrel) varsayılan, platformlar arası http sunucu uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="8310e-110">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span> <span data-ttu-id="8310e-111">Kestrel, en iyi performans ve bellek kullanımını sağlar, ancak HTTP.sys gelişmiş özelliklerden bazılarına sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="8310e-111">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys.</span></span> <span data-ttu-id="8310e-112">Daha fazla bilgi için bu belgedeki [Kestrel vs. HTTP.sys](#korh) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="8310e-112">For more information, see [Kestrel vs. HTTP.sys](#korh) in this document.</span></span>
* <span data-ttu-id="8310e-113">IIS HTTP sunucusu, IIS için bir [işlem içi sunucusudur](#hosting-models) .</span><span class="sxs-lookup"><span data-stu-id="8310e-113">IIS HTTP Server is an [in-process server](#hosting-models) for IIS.</span></span>
* <span data-ttu-id="8310e-114">[HTTP.sys sunucusu](xref:fundamentals/servers/httpsys) , [HTTP.sys çekırdek sürücüsünü ve http sunucusu API](/windows/desktop/Http/http-api-start-page)'sini temel alan bir yalnızca Windows HTTP sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="8310e-114">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="8310e-115">[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)kullanırken, uygulama şu şekilde çalışır:</span><span class="sxs-lookup"><span data-stu-id="8310e-115">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app either runs:</span></span>

* <span data-ttu-id="8310e-116">IIS çalışan işlemiyle aynı işlemde ( [işlem içi barındırma modeli](#hosting-models)) IIS HTTP sunucusu ile.</span><span class="sxs-lookup"><span data-stu-id="8310e-116">In the same process as the IIS worker process (the [in-process hosting model](#hosting-models)) with the IIS HTTP Server.</span></span> <span data-ttu-id="8310e-117">*Işlem içi* önerilen yapılandırmadır.</span><span class="sxs-lookup"><span data-stu-id="8310e-117">*In-process* is the recommended configuration.</span></span>
* <span data-ttu-id="8310e-118">Bir işlemde, IIS çalışan işleminden ( [işlem dışı barındırma modeli](#hosting-models)) [Kestrel sunucusuyla](#kestrel)ayırın.</span><span class="sxs-lookup"><span data-stu-id="8310e-118">In a process separate from the IIS worker process (the [out-of-process hosting model](#hosting-models)) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="8310e-119">[ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) , IIS ile Işlem ıçı IIS HTTP sunucusu ya da Kestrel arasında yerel IIS isteklerini işleyen yerel bir IIS modülüdür.</span><span class="sxs-lookup"><span data-stu-id="8310e-119">The [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) is a native IIS module that handles native IIS requests between IIS and the in-process IIS HTTP Server or Kestrel.</span></span> <span data-ttu-id="8310e-120">Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module>.</span><span class="sxs-lookup"><span data-stu-id="8310e-120">For more information, see <xref:host-and-deploy/aspnet-core-module>.</span></span>

<a name="korh"></a>

## <a name="kestrel-vs-httpsys"></a><span data-ttu-id="8310e-121">Kestrel vs. HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="8310e-121">Kestrel vs. HTTP.sys</span></span>

<span data-ttu-id="8310e-122">Kestrel HTTP.sys aşağıdaki avantajları içerir:</span><span class="sxs-lookup"><span data-stu-id="8310e-122">Kestrel has the following advantages over HTTP.sys:</span></span>

  * <span data-ttu-id="8310e-123">Daha iyi performans ve bellek kullanımı.</span><span class="sxs-lookup"><span data-stu-id="8310e-123">Better performance and memory utilization.</span></span>
  * <span data-ttu-id="8310e-124">Platformlar arası</span><span class="sxs-lookup"><span data-stu-id="8310e-124">Cross platform</span></span>
  * <span data-ttu-id="8310e-125">Çeviklik, işletim sisteminden bağımsız olarak geliştirilmiştir ve düzeltme eki yüklenmiş.</span><span class="sxs-lookup"><span data-stu-id="8310e-125">Agility, it's developed and patched independent of the OS.</span></span>
  * <span data-ttu-id="8310e-126">Programlı bağlantı noktası ve TLS yapılandırması</span><span class="sxs-lookup"><span data-stu-id="8310e-126">Programmatic port and TLS configuration</span></span>
  * <span data-ttu-id="8310e-127">[PPv2](https://github.com/aspnet/AspLabs/blob/master/src/ProxyProtocol/ProxyProtocol.Sample/ProxyProtocol.cs) ve alternatif aktarımlar gibi protokollere izin veren genişletilebilirlik.</span><span class="sxs-lookup"><span data-stu-id="8310e-127">Extensibility that allows for protocols like [PPv2](https://github.com/aspnet/AspLabs/blob/master/src/ProxyProtocol/ProxyProtocol.Sample/ProxyProtocol.cs) and alternate transports.</span></span>

<span data-ttu-id="8310e-128">Http.Sys, Kestrel sahip olmadığı aşağıdaki özelliklerle paylaşılan bir çekirdek modu bileşeni olarak çalışır:</span><span class="sxs-lookup"><span data-stu-id="8310e-128">Http.Sys operates as a shared kernel mode component with the following features that kestrel does not have:</span></span>

  * <span data-ttu-id="8310e-129">Bağlantı noktası Paylaşımı</span><span class="sxs-lookup"><span data-stu-id="8310e-129">Port sharing</span></span>
  * <span data-ttu-id="8310e-130">Çekirdek modu Windows kimlik doğrulaması.</span><span class="sxs-lookup"><span data-stu-id="8310e-130">Kernel mode windows authentication.</span></span> <span data-ttu-id="8310e-131">[Kestrel yalnızca Kullanıcı modu kimlik doğrulamasını destekler](xref:security/authentication/windowsauth#kestrel).</span><span class="sxs-lookup"><span data-stu-id="8310e-131">[Kestrel supports only user-mode authentication](xref:security/authentication/windowsauth#kestrel).</span></span>
  * <span data-ttu-id="8310e-132">Kuyruk aktarımları aracılığıyla hızlı proxy</span><span class="sxs-lookup"><span data-stu-id="8310e-132">Fast proxying via queue transfers</span></span>
  * <span data-ttu-id="8310e-133">Doğrudan dosya iletimi</span><span class="sxs-lookup"><span data-stu-id="8310e-133">Direct file transmission</span></span>
  * <span data-ttu-id="8310e-134">Yanıtları Önbelleğe Alma</span><span class="sxs-lookup"><span data-stu-id="8310e-134">Response caching</span></span>

## <a name="hosting-models"></a><span data-ttu-id="8310e-135">Barındırma modelleri</span><span class="sxs-lookup"><span data-stu-id="8310e-135">Hosting models</span></span>

<span data-ttu-id="8310e-136">ASP.NET Core bir uygulama, işlem içi barındırma kullanarak IIS çalışan işlemiyle aynı işlemde çalışır.</span><span class="sxs-lookup"><span data-stu-id="8310e-136">Using in-process hosting, an ASP.NET Core app runs in the same process as its IIS worker process.</span></span> <span data-ttu-id="8310e-137">İşlem içi barındırma, istek dışı barındırmak için gelişmiş performans sağlar çünkü istekler, giden ağ trafiği ile aynı makineye geri döndürülen bir ağ arabirimidir.</span><span class="sxs-lookup"><span data-stu-id="8310e-137">In-process hosting provides improved performance over out-of-process hosting because requests aren't proxied over the loopback adapter, a network interface that returns outgoing network traffic back to the same machine.</span></span> <span data-ttu-id="8310e-138">IIS, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)ile işlem yönetimini işler.</span><span class="sxs-lookup"><span data-stu-id="8310e-138">IIS handles process management with the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="8310e-139">İşlem dışı barındırma kullanma, ASP.NET Core uygulamalar IIS çalışan işleminden ayrı bir işlemde çalışır ve modül işlem yönetimini işler.</span><span class="sxs-lookup"><span data-stu-id="8310e-139">Using out-of-process hosting, ASP.NET Core apps run in a process separate from the IIS worker process, and the module handles process management.</span></span> <span data-ttu-id="8310e-140">Modül, ilk istek ulaştığında ASP.NET Core uygulama için işlemi başlatır ve kapanırsa veya kilitlenirse uygulamayı yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="8310e-140">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="8310e-141">Bu aslında, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen işlem içi uygulamalarla birlikte görülen davranışdır.</span><span class="sxs-lookup"><span data-stu-id="8310e-141">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="8310e-142">Daha fazla bilgi ve yapılandırma kılavuzu için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="8310e-142">For more information and configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="8310e-143">macOS</span><span class="sxs-lookup"><span data-stu-id="8310e-143">macOS</span></span>](#tab/macos)

<span data-ttu-id="8310e-144">ASP.NET Core, varsayılan, platformlar arası HTTP sunucusu olan [Kestrel Server](xref:fundamentals/servers/kestrel)ile birlikte gönderilir.</span><span class="sxs-lookup"><span data-stu-id="8310e-144">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="8310e-145">Linux</span><span class="sxs-lookup"><span data-stu-id="8310e-145">Linux</span></span>](#tab/linux)

<span data-ttu-id="8310e-146">ASP.NET Core, varsayılan, platformlar arası HTTP sunucusu olan [Kestrel Server](xref:fundamentals/servers/kestrel)ile birlikte gönderilir.</span><span class="sxs-lookup"><span data-stu-id="8310e-146">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

## <a name="kestrel"></a><span data-ttu-id="8310e-147">Kestrel</span><span class="sxs-lookup"><span data-stu-id="8310e-147">Kestrel</span></span>

 <span data-ttu-id="8310e-148">[Kestrel sunucusu](xref:fundamentals/servers/kestrel) varsayılan, platformlar arası http sunucu uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="8310e-148">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server implementation.</span></span> <span data-ttu-id="8310e-149">Kestrel, en iyi performans ve bellek kullanımını sağlar, ancak HTTP.sys gelişmiş özelliklerden bazılarına sahip değildir.</span><span class="sxs-lookup"><span data-stu-id="8310e-149">Kestrel provides the best performance and memory utilization, but it doesn't have some of the advanced features in HTTP.sys.</span></span> <span data-ttu-id="8310e-150">Daha fazla bilgi için bu belgedeki [Kestrel vs. HTTP.sys](#korh) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="8310e-150">For more information, see [Kestrel vs. HTTP.sys](#korh) in this document.</span></span>

<span data-ttu-id="8310e-151">Kestrel kullanın:</span><span class="sxs-lookup"><span data-stu-id="8310e-151">Use Kestrel:</span></span>

* <span data-ttu-id="8310e-152">Kendi başına bir uç sunucu olarak, Internet dahil olmak üzere istekleri doğrudan bir ağdan işleme.</span><span class="sxs-lookup"><span data-stu-id="8310e-152">By itself as an edge server processing requests directly from a network, including the Internet.</span></span>

  ![Kestrel, ters proxy sunucusu olmadan doğrudan Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

* <span data-ttu-id="8310e-154">[Internet Information Services (IIS)](https://www.iis.net/), [NGINX](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi bir *ters ara sunucu* ile.</span><span class="sxs-lookup"><span data-stu-id="8310e-154">With a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="8310e-155">Ters proxy sunucusu, Internet 'ten gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="8310e-155">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel.</span></span>

  ![Kestrel, IIS, NGINX veya Apache gibi bir ters ara sunucu üzerinden Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="8310e-157">&mdash;Ters ara sunucu sunucusuyla ya da olmadan barındırma yapılandırması &mdash; desteklenir.</span><span class="sxs-lookup"><span data-stu-id="8310e-157">Either hosting configuration&mdash;with or without a reverse proxy server&mdash;is supported.</span></span>

<span data-ttu-id="8310e-158">Kestrel yapılandırma kılavuzu ve bir ters proxy yapılandırmasında Kestrel 'in ne zaman kullanılacağı hakkında bilgi için, bkz <xref:fundamentals/servers/kestrel> ..</span><span class="sxs-lookup"><span data-stu-id="8310e-158">For Kestrel configuration guidance and information on when to use Kestrel in a reverse proxy configuration, see <xref:fundamentals/servers/kestrel>.</span></span>

::: moniker range="< aspnetcore-2.2"

# <a name="windows"></a>[<span data-ttu-id="8310e-159">Windows</span><span class="sxs-lookup"><span data-stu-id="8310e-159">Windows</span></span>](#tab/windows)

<span data-ttu-id="8310e-160">ASP.NET Core aşağıdakiler ile birlikte gelir:</span><span class="sxs-lookup"><span data-stu-id="8310e-160">ASP.NET Core ships with the following:</span></span>

* <span data-ttu-id="8310e-161">[Kestrel sunucusu](xref:fundamentals/servers/kestrel) , platformlar arası varsayılan HTTP sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="8310e-161">[Kestrel server](xref:fundamentals/servers/kestrel) is the default, cross-platform HTTP server.</span></span>
* <span data-ttu-id="8310e-162">[HTTP.sys sunucusu](xref:fundamentals/servers/httpsys) , [HTTP.sys çekırdek sürücüsünü ve http sunucusu API](/windows/desktop/Http/http-api-start-page)'sini temel alan bir yalnızca Windows HTTP sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="8310e-162">[HTTP.sys server](xref:fundamentals/servers/httpsys) is a Windows-only HTTP server based on the [HTTP.sys kernel driver and HTTP Server API](/windows/desktop/Http/http-api-start-page).</span></span>

<span data-ttu-id="8310e-163">[IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)kullanırken, uygulama IIS çalışan işleminden (*işlem dışı*) [Kestrel sunucusu](#kestrel)ile ayrı bir işlemde çalışır.</span><span class="sxs-lookup"><span data-stu-id="8310e-163">When using [IIS](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), the app runs in a process separate from the IIS worker process (*out-of-process*) with the [Kestrel server](#kestrel).</span></span>

<span data-ttu-id="8310e-164">ASP.NET Core uygulamalar IIS çalışan işleminden ayrı bir işlemde çalıştığından, modül işlem yönetimini işler.</span><span class="sxs-lookup"><span data-stu-id="8310e-164">Because ASP.NET Core apps run in a process separate from the IIS worker process, the module handles process management.</span></span> <span data-ttu-id="8310e-165">Modül, ilk istek ulaştığında ASP.NET Core uygulama için işlemi başlatır ve kapanırsa veya kilitlenirse uygulamayı yeniden başlatır.</span><span class="sxs-lookup"><span data-stu-id="8310e-165">The module starts the process for the ASP.NET Core app when the first request arrives and restarts the app if it shuts down or crashes.</span></span> <span data-ttu-id="8310e-166">Bu aslında, [Windows Işlem etkinleştirme hizmeti (was)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was)tarafından yönetilen işlem içi uygulamalarla birlikte görülen davranışdır.</span><span class="sxs-lookup"><span data-stu-id="8310e-166">This is essentially the same behavior as seen with apps that run in-process that are managed by the [Windows Process Activation Service (WAS)](/iis/manage/provisioning-and-managing-iis/features-of-the-windows-process-activation-service-was).</span></span>

<span data-ttu-id="8310e-167">Aşağıdaki diyagramda IIS, ASP.NET Core modülü ve işlem dışı barındırılan bir uygulama arasındaki ilişki gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="8310e-167">The following diagram illustrates the relationship between IIS, the ASP.NET Core Module, and an app hosted out-of-process:</span></span>

![ASP.NET Core Modülü](_static/ancm-outofprocess.png)

<span data-ttu-id="8310e-169">İstekler Web 'den çekirdek modu HTTP.sys sürücüsüne ulaşır.</span><span class="sxs-lookup"><span data-stu-id="8310e-169">Requests arrive from the web to the kernel-mode HTTP.sys driver.</span></span> <span data-ttu-id="8310e-170">Sürücü, istekleri Web sitesinin yapılandırılmış bağlantı noktasında IIS 'ye yönlendirir, genellikle 80 (HTTP) veya 443 (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="8310e-170">The driver routes the requests to IIS on the website's configured port, usually 80 (HTTP) or 443 (HTTPS).</span></span> <span data-ttu-id="8310e-171">Modül, 80 veya 443 numaralı bağlantı noktası olmayan uygulama için rastgele bir bağlantı noktasında istekleri Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="8310e-171">The module forwards the requests to Kestrel on a random port for the app, which isn't port 80 or 443.</span></span>

<span data-ttu-id="8310e-172">Modül, başlangıç sırasında bir ortam değişkeni aracılığıyla bağlantı noktasını belirtir ve [IIS tümleştirme ara yazılımı](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) sunucuyu dinleyecek şekilde yapılandırır `http://localhost:{port}` .</span><span class="sxs-lookup"><span data-stu-id="8310e-172">The module specifies the port via an environment variable at startup, and the [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) configures the server to listen on `http://localhost:{port}`.</span></span> <span data-ttu-id="8310e-173">Ek denetimler gerçekleştirilir ve modülünden kaynaklanmayan istekler reddedilir.</span><span class="sxs-lookup"><span data-stu-id="8310e-173">Additional checks are performed, and requests that don't originate from the module are rejected.</span></span> <span data-ttu-id="8310e-174">Modül HTTPS iletmeyi desteklemez, bu nedenle istekler HTTPS üzerinden IIS tarafından alınsa bile HTTP üzerinden iletilir.</span><span class="sxs-lookup"><span data-stu-id="8310e-174">The module doesn't support HTTPS forwarding, so requests are forwarded over HTTP even if received by IIS over HTTPS.</span></span>

<span data-ttu-id="8310e-175">Kestrel, isteği modülden başlattıktan sonra, istek ASP.NET Core ara yazılım ardışık düzenine gönderilir.</span><span class="sxs-lookup"><span data-stu-id="8310e-175">After Kestrel picks up the request from the module, the request is pushed into the ASP.NET Core middleware pipeline.</span></span> <span data-ttu-id="8310e-176">Ara yazılım ardışık düzeni isteği işler ve `HttpContext` uygulamanın mantığına bir örnek olarak geçirir.</span><span class="sxs-lookup"><span data-stu-id="8310e-176">The middleware pipeline handles the request and passes it on as an `HttpContext` instance to the app's logic.</span></span> <span data-ttu-id="8310e-177">IIS tümleştirmesi tarafından eklenen ara yazılım, isteği Kestrel iletmek için düzen, uzak IP ve pathbase 'i hesaba göre güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="8310e-177">Middleware added by IIS Integration updates the scheme, remote IP, and pathbase to account for forwarding the request to Kestrel.</span></span> <span data-ttu-id="8310e-178">Uygulamanın yanıtı IIS 'e geri geçirilir ve bu, isteği başlatan HTTP istemcisine geri gönderilir.</span><span class="sxs-lookup"><span data-stu-id="8310e-178">The app's response is passed back to IIS, which pushes it back out to the HTTP client that initiated the request.</span></span>

<span data-ttu-id="8310e-179">IIS ve ASP.NET Core modülü yapılandırma kılavuzu için aşağıdaki konulara bakın:</span><span class="sxs-lookup"><span data-stu-id="8310e-179">For IIS and ASP.NET Core Module configuration guidance, see the following topics:</span></span>

* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/aspnet-core-module>

# <a name="macos"></a>[<span data-ttu-id="8310e-180">macOS</span><span class="sxs-lookup"><span data-stu-id="8310e-180">macOS</span></span>](#tab/macos)

<span data-ttu-id="8310e-181">ASP.NET Core, varsayılan, platformlar arası HTTP sunucusu olan [Kestrel Server](xref:fundamentals/servers/kestrel)ile birlikte gönderilir.</span><span class="sxs-lookup"><span data-stu-id="8310e-181">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

# <a name="linux"></a>[<span data-ttu-id="8310e-182">Linux</span><span class="sxs-lookup"><span data-stu-id="8310e-182">Linux</span></span>](#tab/linux)

<span data-ttu-id="8310e-183">ASP.NET Core, varsayılan, platformlar arası HTTP sunucusu olan [Kestrel Server](xref:fundamentals/servers/kestrel)ile birlikte gönderilir.</span><span class="sxs-lookup"><span data-stu-id="8310e-183">ASP.NET Core ships with [Kestrel server](xref:fundamentals/servers/kestrel), which is the default, cross-platform HTTP server.</span></span>

---

::: moniker-end

### <a name="nginx-with-kestrel"></a><span data-ttu-id="8310e-184">Kestrel ile NGINX</span><span class="sxs-lookup"><span data-stu-id="8310e-184">Nginx with Kestrel</span></span>

<span data-ttu-id="8310e-185">Linux üzerinde NGINX 'i Kestrel için ters proxy sunucusu olarak kullanma hakkında daha fazla bilgi için bkz <xref:host-and-deploy/linux-nginx> ..</span><span class="sxs-lookup"><span data-stu-id="8310e-185">For information on how to use Nginx on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-nginx>.</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="8310e-186">Kestrel ile Apache</span><span class="sxs-lookup"><span data-stu-id="8310e-186">Apache with Kestrel</span></span>

<span data-ttu-id="8310e-187">Linux üzerinde Apache 'yi Kestrel için ters proxy sunucusu olarak kullanma hakkında daha fazla bilgi için bkz <xref:host-and-deploy/linux-apache> ..</span><span class="sxs-lookup"><span data-stu-id="8310e-187">For information on how to use Apache on Linux as a reverse proxy server for Kestrel, see <xref:host-and-deploy/linux-apache>.</span></span>

## <a name="httpsys"></a><span data-ttu-id="8310e-188">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="8310e-188">HTTP.sys</span></span>

<span data-ttu-id="8310e-189">ASP.NET Core uygulamalar Windows üzerinde çalışıyorsa HTTP.sys, Kestrel için bir alternatiftir.</span><span class="sxs-lookup"><span data-stu-id="8310e-189">If ASP.NET Core apps are run on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="8310e-190">Uygulama, Kestrel içinde kullanılamayan özellikler gerektirmediği takdirde, Kestrel üzerinde HTTP.sys önerilir.</span><span class="sxs-lookup"><span data-stu-id="8310e-190">Kestrel is recommended over HTTP.sys unless the app requires features not available in Kestrel.</span></span> <span data-ttu-id="8310e-191">Daha fazla bilgi için bkz. <xref:fundamentals/servers/httpsys>.</span><span class="sxs-lookup"><span data-stu-id="8310e-191">For more information, see <xref:fundamentals/servers/httpsys>.</span></span>

![HTTP.sys doğrudan Internet ile iletişim kurar](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="8310e-193">HTTP.sys, yalnızca bir iç ağa açık olan uygulamalar için de kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="8310e-193">HTTP.sys can also be used for apps that are only exposed to an internal network.</span></span>

![HTTP.sys doğrudan iç ağla iletişim kurar](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="8310e-195">HTTP.sys yapılandırma kılavuzu için bkz <xref:fundamentals/servers/httpsys> ..</span><span class="sxs-lookup"><span data-stu-id="8310e-195">For HTTP.sys configuration guidance, see <xref:fundamentals/servers/httpsys>.</span></span>

## <a name="aspnet-core-server-infrastructure"></a><span data-ttu-id="8310e-196">ASP.NET Core Server altyapısı</span><span class="sxs-lookup"><span data-stu-id="8310e-196">ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="8310e-197"><xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> `Startup.Configure` Yönteminde bulunan <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> özelliği türünün özelliğini kullanıma sunar <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection> .</span><span class="sxs-lookup"><span data-stu-id="8310e-197">The <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> available in the `Startup.Configure` method exposes the <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder.ServerFeatures> property of type <xref:Microsoft.AspNetCore.Http.Features.IFeatureCollection>.</span></span> <span data-ttu-id="8310e-198">Kestrel ve HTTP.sys her biri yalnızca tek bir özellik sunar, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> ancak farklı sunucu uygulamaları ek işlevsellik sergilede gösterebilir.</span><span class="sxs-lookup"><span data-stu-id="8310e-198">Kestrel and HTTP.sys only expose a single feature each, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>, but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="8310e-199">`IServerAddressesFeature` sunucu uygulamasının çalışma zamanında hangi bağlantı noktasını bağladığına ilişkin bilgi edinmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="8310e-199">`IServerAddressesFeature` can be used to find out which port the server implementation has bound at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="8310e-200">Özel sunucular</span><span class="sxs-lookup"><span data-stu-id="8310e-200">Custom servers</span></span>

<span data-ttu-id="8310e-201">Yerleşik sunucular uygulamanın gereksinimlerini karşılamıyorsa, özel bir sunucu uygulaması oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="8310e-201">If the built-in servers don't meet the app's requirements, a custom server implementation can be created.</span></span> <span data-ttu-id="8310e-202">[.Net Için açık Web arabirimi (OWıN) Kılavuzu](xref:fundamentals/owin) , [nowin](https://github.com/Bobris/Nowin)tabanlı bir uygulamanın nasıl yazılacağını gösterir <xref:Microsoft.AspNetCore.Hosting.Server.IServer> .</span><span class="sxs-lookup"><span data-stu-id="8310e-202">The [Open Web Interface for .NET (OWIN) guide](xref:fundamentals/owin) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based <xref:Microsoft.AspNetCore.Hosting.Server.IServer> implementation.</span></span> <span data-ttu-id="8310e-203">Yalnızca uygulamanın kullandığı Özellik arabirimleri, en azından <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> ve <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> desteklenmesi gereken uygulama gerektirir.</span><span class="sxs-lookup"><span data-stu-id="8310e-203">Only the feature interfaces that the app uses require implementation, though at a minimum <xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature> and <xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature> must be supported.</span></span>

## <a name="server-startup"></a><span data-ttu-id="8310e-204">Sunucu başlatma</span><span class="sxs-lookup"><span data-stu-id="8310e-204">Server startup</span></span>

<span data-ttu-id="8310e-205">Tümleşik geliştirme ortamı (IDE) veya düzenleyici uygulamayı başlattığında sunucu başlatılır:</span><span class="sxs-lookup"><span data-stu-id="8310e-205">The server is launched when the Integrated Development Environment (IDE) or editor starts the app:</span></span>

* <span data-ttu-id="8310e-206">[Visual Studio](https://visualstudio.microsoft.com): başlatma profilleri, uygulamayı ve sunucuyu [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) / [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) ya da konsolundan başlatmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="8310e-206">[Visual Studio](https://visualstudio.microsoft.com): Launch profiles can be used to start the app and server with either [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)/[ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) or the console.</span></span>
* <span data-ttu-id="8310e-207">[Visual Studio Code](https://code.visualstudio.com/): uygulama ve sunucu [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode)tarafından başlatılır ve bu, CoreCLR hata ayıklayıcısını etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="8310e-207">[Visual Studio Code](https://code.visualstudio.com/): The app and server are started by [Omnisharp](https://github.com/OmniSharp/omnisharp-vscode), which activates the CoreCLR debugger.</span></span>
* <span data-ttu-id="8310e-208">[Mac için Visual Studio](https://visualstudio.microsoft.com/vs/mac/): uygulama ve sunucu [mono Soft-Mode hata ayıklayıcısı](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/)tarafından başlatılır.</span><span class="sxs-lookup"><span data-stu-id="8310e-208">[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/): The app and server are started by the [Mono Soft-Mode Debugger](https://www.mono-project.com/docs/advanced/runtime/docs/soft-debugger/).</span></span>

<span data-ttu-id="8310e-209">Uygulamanın, projenin klasöründeki bir komut isteminden başlatılması sırasında [DotNet Run](/dotnet/core/tools/dotnet-run) uygulamayı ve sunucuyu başlatır (yalnızca Kestrel ve HTTP.sys).</span><span class="sxs-lookup"><span data-stu-id="8310e-209">When launching the app from a command prompt in the project's folder, [dotnet run](/dotnet/core/tools/dotnet-run) launches the app and server (Kestrel and HTTP.sys only).</span></span> <span data-ttu-id="8310e-210">Yapılandırma `-c|--configuration` seçeneğiyle belirtilir, `Debug` (varsayılan) veya olarak ayarlanır `Release` .</span><span class="sxs-lookup"><span data-stu-id="8310e-210">The configuration is specified by the `-c|--configuration` option, which is set to either `Debug` (default) or `Release`.</span></span>

<span data-ttu-id="8310e-211">Dosyadaki bir *launchSettings.js* `dotnet run` , Visual Studio gibi araç ile yerleşik bir hata ayıklayıcı ile veya bir uygulama başlatırken yapılandırma sağlar.</span><span class="sxs-lookup"><span data-stu-id="8310e-211">A *launchSettings.json* file provides configuration when launching an app with `dotnet run` or with a debugger built into tooling, such as Visual Studio.</span></span> <span data-ttu-id="8310e-212">Bir *launchSettings.jsdosya üzerinde* başlatma profilleri varsa, `--launch-profile {PROFILE NAME}` komutuyla seçeneğini kullanın `dotnet run` veya Visual Studio 'da profili seçin.</span><span class="sxs-lookup"><span data-stu-id="8310e-212">If launch profiles are present in a *launchSettings.json* file, use the `--launch-profile {PROFILE NAME}` option with the `dotnet run` command or select the profile in Visual Studio.</span></span> <span data-ttu-id="8310e-213">Daha fazla bilgi için bkz. [DotNet Run](/dotnet/core/tools/dotnet-run) ve [.NET Core Distribution paketleme](/dotnet/core/build/distribution-packaging).</span><span class="sxs-lookup"><span data-stu-id="8310e-213">For more information, see [dotnet run](/dotnet/core/tools/dotnet-run) and [.NET Core distribution packaging](/dotnet/core/build/distribution-packaging).</span></span>

## <a name="http2-support"></a><span data-ttu-id="8310e-214">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="8310e-214">HTTP/2 support</span></span>

<span data-ttu-id="8310e-215">[Http/2](https://httpwg.org/specs/rfc7540.html) aşağıdaki dağıtım senaryolarında ASP.NET Core desteklenir:</span><span class="sxs-lookup"><span data-stu-id="8310e-215">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is supported with ASP.NET Core in the following deployment scenarios:</span></span>

::: moniker range=">= aspnetcore-5.0"

* [<span data-ttu-id="8310e-216">Kestrel</span><span class="sxs-lookup"><span data-stu-id="8310e-216">Kestrel</span></span>](xref:fundamentals/servers/kestrel/http2)
  * <span data-ttu-id="8310e-217">İşletim sistemi</span><span class="sxs-lookup"><span data-stu-id="8310e-217">Operating system</span></span>
    * <span data-ttu-id="8310e-218">Windows Server 2016/Windows 10 veya üzeri&dagger;</span><span class="sxs-lookup"><span data-stu-id="8310e-218">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="8310e-219">OpenSSL 1.0.2 veya üzerini içeren Linux (örneğin, Ubuntu 16,04 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="8310e-219">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="8310e-220">HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="8310e-220">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="8310e-221">Hedef Framework: .NET Core 2,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="8310e-221">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="8310e-222">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="8310e-222">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="8310e-223">Windows Server 2016/Windows 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="8310e-223">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="8310e-224">Hedef Framework: HTTP.sys dağıtımlar için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="8310e-224">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="8310e-225">IIS (işlem içi)</span><span class="sxs-lookup"><span data-stu-id="8310e-225">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="8310e-226">Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="8310e-226">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="8310e-227">Hedef Framework: .NET Core 2,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="8310e-227">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="8310e-228">IIS (işlem dışı)</span><span class="sxs-lookup"><span data-stu-id="8310e-228">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="8310e-229">Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="8310e-229">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="8310e-230">Herkese açık uç sunucu bağlantıları HTTP/2 kullanır, ancak Kestrel ile ters proxy bağlantısı HTTP/1.1 kullanır.</span><span class="sxs-lookup"><span data-stu-id="8310e-230">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="8310e-231">Hedef Framework: IIS işlem dışı dağıtımlar için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="8310e-231">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="8310e-232">&dagger;Kestrel, Windows Server 2012 R2 ve Windows 8.1 'de HTTP/2 için sınırlı destek içerir.</span><span class="sxs-lookup"><span data-stu-id="8310e-232">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="8310e-233">Bu işletim sistemlerinde kullanılabilir olan desteklenen TLS şifre paketlerinin listesi sınırlı olduğundan destek sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="8310e-233">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="8310e-234">TLS bağlantılarının güvenliğini sağlamak için Eliptik Eğri dijital Imza algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="8310e-234">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2 < aspnetcore-5.0"

* [<span data-ttu-id="8310e-235">Kestrel</span><span class="sxs-lookup"><span data-stu-id="8310e-235">Kestrel</span></span>](xref:fundamentals/servers/kestrel#http2-support)
  * <span data-ttu-id="8310e-236">İşletim sistemi</span><span class="sxs-lookup"><span data-stu-id="8310e-236">Operating system</span></span>
    * <span data-ttu-id="8310e-237">Windows Server 2016/Windows 10 veya üzeri&dagger;</span><span class="sxs-lookup"><span data-stu-id="8310e-237">Windows Server 2016/Windows 10 or later&dagger;</span></span>
    * <span data-ttu-id="8310e-238">OpenSSL 1.0.2 veya üzerini içeren Linux (örneğin, Ubuntu 16,04 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="8310e-238">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
    * <span data-ttu-id="8310e-239">HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="8310e-239">HTTP/2 will be supported on macOS in a future release.</span></span>
  * <span data-ttu-id="8310e-240">Hedef Framework: .NET Core 2,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="8310e-240">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="8310e-241">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="8310e-241">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="8310e-242">Windows Server 2016/Windows 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="8310e-242">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="8310e-243">Hedef Framework: HTTP.sys dağıtımlar için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="8310e-243">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="8310e-244">IIS (işlem içi)</span><span class="sxs-lookup"><span data-stu-id="8310e-244">IIS (in-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="8310e-245">Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="8310e-245">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="8310e-246">Hedef Framework: .NET Core 2,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="8310e-246">Target framework: .NET Core 2.2 or later</span></span>
* [<span data-ttu-id="8310e-247">IIS (işlem dışı)</span><span class="sxs-lookup"><span data-stu-id="8310e-247">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="8310e-248">Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="8310e-248">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="8310e-249">Herkese açık uç sunucu bağlantıları HTTP/2 kullanır, ancak Kestrel ile ters proxy bağlantısı HTTP/1.1 kullanır.</span><span class="sxs-lookup"><span data-stu-id="8310e-249">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="8310e-250">Hedef Framework: IIS işlem dışı dağıtımlar için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="8310e-250">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

<span data-ttu-id="8310e-251">&dagger;Kestrel, Windows Server 2012 R2 ve Windows 8.1 'de HTTP/2 için sınırlı destek içerir.</span><span class="sxs-lookup"><span data-stu-id="8310e-251">&dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="8310e-252">Bu işletim sistemlerinde kullanılabilir olan desteklenen TLS şifre paketlerinin listesi sınırlı olduğundan destek sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="8310e-252">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="8310e-253">TLS bağlantılarının güvenliğini sağlamak için Eliptik Eğri dijital Imza algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="8310e-253">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* [<span data-ttu-id="8310e-254">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="8310e-254">HTTP.sys</span></span>](xref:fundamentals/servers/httpsys#http2-support)
  * <span data-ttu-id="8310e-255">Windows Server 2016/Windows 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="8310e-255">Windows Server 2016/Windows 10 or later</span></span>
  * <span data-ttu-id="8310e-256">Hedef Framework: HTTP.sys dağıtımlar için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="8310e-256">Target framework: Not applicable to HTTP.sys deployments.</span></span>
* [<span data-ttu-id="8310e-257">IIS (işlem dışı)</span><span class="sxs-lookup"><span data-stu-id="8310e-257">IIS (out-of-process)</span></span>](xref:host-and-deploy/iis/index#http2-support)
  * <span data-ttu-id="8310e-258">Windows Server 2016/Windows 10 veya üzeri; IIS 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="8310e-258">Windows Server 2016/Windows 10 or later; IIS 10 or later</span></span>
  * <span data-ttu-id="8310e-259">Herkese açık uç sunucu bağlantıları HTTP/2 kullanır, ancak Kestrel ile ters proxy bağlantısı HTTP/1.1 kullanır.</span><span class="sxs-lookup"><span data-stu-id="8310e-259">Public-facing edge server connections use HTTP/2, but the reverse proxy connection to Kestrel uses HTTP/1.1.</span></span>
  * <span data-ttu-id="8310e-260">Hedef Framework: IIS işlem dışı dağıtımlar için geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="8310e-260">Target framework: Not applicable to IIS out-of-process deployments.</span></span>

::: moniker-end

<span data-ttu-id="8310e-261">Bir HTTP/2 bağlantısı, [uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) ve TLS 1,2 veya üstünü kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8310e-261">An HTTP/2 connection must use [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) and TLS 1.2 or later.</span></span> <span data-ttu-id="8310e-262">Daha fazla bilgi için, sunucu dağıtım senaryolarınıza ait konulara bakın.</span><span class="sxs-lookup"><span data-stu-id="8310e-262">For more information, see the topics that pertain to your server deployment scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8310e-263">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="8310e-263">Additional resources</span></span>

* <xref:fundamentals/servers/kestrel>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/iis/index>
* <xref:host-and-deploy/azure-apps/index>
* <xref:host-and-deploy/linux-nginx>
* <xref:host-and-deploy/linux-apache>
* <xref:fundamentals/servers/httpsys>
