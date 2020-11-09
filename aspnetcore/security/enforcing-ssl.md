---
title: ASP.NET Core 'de HTTPS 'yi zorla
author: rick-anderson
description: ASP.NET Core Web uygulamasında HTTPS/TLS isteme hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 12/06/2019
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
uid: security/enforcing-ssl
ms.openlocfilehash: e473da9a7cbd91a601ad4af0c7c02c7f576f348c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051128"
---
# <a name="enforce-https-in-aspnet-core"></a><span data-ttu-id="7ae70-103">ASP.NET Core 'de HTTPS 'yi zorla</span><span class="sxs-lookup"><span data-stu-id="7ae70-103">Enforce HTTPS in ASP.NET Core</span></span>

<span data-ttu-id="7ae70-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7ae70-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7ae70-105">Bu belgede nasıl yapılacağı gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="7ae70-105">This document shows how to:</span></span>

* <span data-ttu-id="7ae70-106">Tüm istekler için HTTPS gerektir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-106">Require HTTPS for all requests.</span></span>
* <span data-ttu-id="7ae70-107">Tüm HTTP isteklerini HTTPS 'ye yeniden yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="7ae70-107">Redirect all HTTP requests to HTTPS.</span></span>

<span data-ttu-id="7ae70-108">Hiçbir API, istemcinin ilk istekte hassas veri göndermesini engelleyebilir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-108">No API can prevent a client from sending sensitive data on the first request.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="7ae70-109">API projeleri</span><span class="sxs-lookup"><span data-stu-id="7ae70-109">API projects</span></span>
>
> <span data-ttu-id="7ae70-110">Hassas bilgileri alan Web API 'Lerinde [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) **kullanmayın.**</span><span class="sxs-lookup"><span data-stu-id="7ae70-110">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="7ae70-111">`RequireHttpsAttribute` tarayıcıları HTTP 'den HTTPS 'ye yönlendirmek için HTTP durum kodlarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="7ae70-111">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="7ae70-112">API istemcileri HTTP 'den HTTPS 'ye yeniden yönlendirmeyi anlamayabilir veya buna uymayabilir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-112">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="7ae70-113">Bu tür istemciler, HTTP üzerinden bilgi gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-113">Such clients may send information over HTTP.</span></span> <span data-ttu-id="7ae70-114">Web API 'Leri şunlardan biri olmalıdır:</span><span class="sxs-lookup"><span data-stu-id="7ae70-114">Web APIs should either:</span></span>
>
> * <span data-ttu-id="7ae70-115">HTTP üzerinde dinleme yok.</span><span class="sxs-lookup"><span data-stu-id="7ae70-115">Not listen on HTTP.</span></span>
> * <span data-ttu-id="7ae70-116">Durum kodu 400 olan bağlantıyı kapatın (Hatalı Istek) ve isteğe bağlı değildir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-116">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>
>
> ## <a name="hsts-and-api-projects"></a><span data-ttu-id="7ae70-117">HSTS ve API projeleri</span><span class="sxs-lookup"><span data-stu-id="7ae70-117">HSTS and API projects</span></span>
>
> <span data-ttu-id="7ae70-118">Varsayılan API projeleri, genellikle yalnızca bir tarayıcı yönergesi olduğu için [HSTS](#hsts) içermez.</span><span class="sxs-lookup"><span data-stu-id="7ae70-118">The default API projects don't include [HSTS](#hsts) because HSTS is generally a browser only instruction.</span></span> <span data-ttu-id="7ae70-119">Telefon veya masaüstü uygulamaları gibi diğer çağıranlar, yönergeye **uymayın.**</span><span class="sxs-lookup"><span data-stu-id="7ae70-119">Other callers, such as phone or desktop apps, do **not** obey the instruction.</span></span> <span data-ttu-id="7ae70-120">Tarayıcılar içinde bile, HTTP üzerinden bir API 'ye yapılan tek bir kimlik doğrulamalı çağrı, güvenli olmayan ağlarda risk içerir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-120">Even within browsers, a single authenticated call to an API over HTTP has risks on insecure networks.</span></span> <span data-ttu-id="7ae70-121">Güvenli yaklaşım, API projelerini yalnızca HTTPS dinlemesi ve HTTPS üzerinden yanıt verecek şekilde yapılandırmaktır.</span><span class="sxs-lookup"><span data-stu-id="7ae70-121">The secure approach is to configure API projects to only listen to and respond over HTTPS.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

> [!WARNING]
> ## <a name="api-projects"></a><span data-ttu-id="7ae70-122">API projeleri</span><span class="sxs-lookup"><span data-stu-id="7ae70-122">API projects</span></span>
>
> <span data-ttu-id="7ae70-123">Hassas bilgileri alan Web API 'Lerinde [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) **kullanmayın.**</span><span class="sxs-lookup"><span data-stu-id="7ae70-123">Do **not** use [RequireHttpsAttribute](/dotnet/api/microsoft.aspnetcore.mvc.requirehttpsattribute) on Web APIs that receive sensitive information.</span></span> <span data-ttu-id="7ae70-124">`RequireHttpsAttribute` tarayıcıları HTTP 'den HTTPS 'ye yönlendirmek için HTTP durum kodlarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="7ae70-124">`RequireHttpsAttribute` uses HTTP status codes to redirect browsers from HTTP to HTTPS.</span></span> <span data-ttu-id="7ae70-125">API istemcileri HTTP 'den HTTPS 'ye yeniden yönlendirmeyi anlamayabilir veya buna uymayabilir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-125">API clients may not understand or obey redirects from HTTP to HTTPS.</span></span> <span data-ttu-id="7ae70-126">Bu tür istemciler, HTTP üzerinden bilgi gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-126">Such clients may send information over HTTP.</span></span> <span data-ttu-id="7ae70-127">Web API 'Leri şunlardan biri olmalıdır:</span><span class="sxs-lookup"><span data-stu-id="7ae70-127">Web APIs should either:</span></span>
>
> * <span data-ttu-id="7ae70-128">HTTP üzerinde dinleme yok.</span><span class="sxs-lookup"><span data-stu-id="7ae70-128">Not listen on HTTP.</span></span>
> * <span data-ttu-id="7ae70-129">Durum kodu 400 olan bağlantıyı kapatın (Hatalı Istek) ve isteğe bağlı değildir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-129">Close the connection with status code 400 (Bad Request) and not serve the request.</span></span>

::: moniker-end

## <a name="require-https"></a><span data-ttu-id="7ae70-130">HTTPS gerektir</span><span class="sxs-lookup"><span data-stu-id="7ae70-130">Require HTTPS</span></span>

<span data-ttu-id="7ae70-131">Üretim ASP.NET Core Web uygulamalarının kullanmasını öneririz:</span><span class="sxs-lookup"><span data-stu-id="7ae70-131">We recommend that production ASP.NET Core web apps use:</span></span>

* <span data-ttu-id="7ae70-132"><xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>Http ISTEKLERINI https 'ye yeniden yönlendirmek IÇIN https yeniden yönlendirme ara yazılımı ().</span><span class="sxs-lookup"><span data-stu-id="7ae70-132">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) to redirect HTTP requests to HTTPS.</span></span>
* <span data-ttu-id="7ae70-133">İstemcilere HTTP katı aktarım güvenliği Protokolü (HSTS) üst bilgileri göndermek için HSTS ara yazılımı ([Usehsts](#http-strict-transport-security-protocol-hsts)).</span><span class="sxs-lookup"><span data-stu-id="7ae70-133">HSTS Middleware ([UseHsts](#http-strict-transport-security-protocol-hsts)) to send HTTP Strict Transport Security Protocol (HSTS) headers to clients.</span></span>

> [!NOTE]
> <span data-ttu-id="7ae70-134">Ters Proxy yapılandırmasında dağıtılan uygulamalar, proxy 'nin bağlantı güvenliğini (HTTPS) işlemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="7ae70-134">Apps deployed in a reverse proxy configuration allow the proxy to handle connection security (HTTPS).</span></span> <span data-ttu-id="7ae70-135">Ara sunucu HTTPS yeniden yönlendirmeyi de işleiyorsa, HTTPS yeniden yönlendirme ara yazılımı kullanmanız gerekmez.</span><span class="sxs-lookup"><span data-stu-id="7ae70-135">If the proxy also handles HTTPS redirection, there's no need to use HTTPS Redirection Middleware.</span></span> <span data-ttu-id="7ae70-136">Proxy sunucusu Ayrıca, HSTS üst bilgilerini (örneğin, [ııs 10,0 (1709) veya sonraki sürümlerde yerel HSTS desteği](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)) yazmayı de işişişişsa, uygulama Için HSTS ara yazılımı gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-136">If the proxy server also handles writing HSTS headers (for example, [native HSTS support in IIS 10.0 (1709) or later](/iis/get-started/whats-new-in-iis-10-version-1709/iis-10-version-1709-hsts#iis-100-version-1709-native-hsts-support)), HSTS Middleware isn't required by the app.</span></span> <span data-ttu-id="7ae70-137">Daha fazla bilgi için bkz. [Proje oluşturma SıRASıNDA https/HSTS 'nin geri çevirme](#opt-out-of-httpshsts-on-project-creation).</span><span class="sxs-lookup"><span data-stu-id="7ae70-137">For more information, see [Opt-out of HTTPS/HSTS on project creation](#opt-out-of-httpshsts-on-project-creation).</span></span>

### <a name="usehttpsredirection"></a><span data-ttu-id="7ae70-138">UseHttpsRedirection</span><span class="sxs-lookup"><span data-stu-id="7ae70-138">UseHttpsRedirection</span></span>

<span data-ttu-id="7ae70-139">Aşağıdaki kod, `UseHttpsRedirection` `Startup` sınıfında çağırır:</span><span class="sxs-lookup"><span data-stu-id="7ae70-139">The following code calls `UseHttpsRedirection` in the `Startup` class:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=13)]

::: moniker-end

<span data-ttu-id="7ae70-140">Vurgulanan önceki kod:</span><span class="sxs-lookup"><span data-stu-id="7ae70-140">The preceding highlighted code:</span></span>

* <span data-ttu-id="7ae70-141">Varsayılan [Httpsredirectionoptions. RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)) kullanır.</span><span class="sxs-lookup"><span data-stu-id="7ae70-141">Uses the default [HttpsRedirectionOptions.RedirectStatusCode](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.redirectstatuscode) ([Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect)).</span></span>
* <span data-ttu-id="7ae70-142">, [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) `ASPNETCORE_HTTPS_PORT` Ortam değişkeni veya [ıveraddressesözelliği](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature)tarafından geçersiz kılınmadıkça varsayılan Httpsredirectionoptions. HttpsPort (null) kullanır.</span><span class="sxs-lookup"><span data-stu-id="7ae70-142">Uses the default [HttpsRedirectionOptions.HttpsPort](/dotnet/api/microsoft.aspnetcore.httpspolicy.httpsredirectionoptions.httpsport) (null) unless overridden by the `ASPNETCORE_HTTPS_PORT` environment variable or [IServerAddressesFeature](/dotnet/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature).</span></span>

<span data-ttu-id="7ae70-143">Kalıcı yeniden yönlendirmeler yerine geçici yeniden yönlendirmeler kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="7ae70-143">We recommend using temporary redirects rather than permanent redirects.</span></span> <span data-ttu-id="7ae70-144">Bağlantıyı önbelleğe alma, geliştirme ortamlarında kararsız davranışa neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-144">Link caching can cause unstable behavior in development environments.</span></span> <span data-ttu-id="7ae70-145">Uygulama geliştirme dışı bir ortamda olduğunda kalıcı yeniden yönlendirme durum kodu göndermek isterseniz, [üretim içindeki kalıcı yeniden yönlendirmeleri yapılandırma](#configure-permanent-redirects-in-production) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="7ae70-145">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, see the [Configure permanent redirects in production](#configure-permanent-redirects-in-production) section.</span></span> <span data-ttu-id="7ae70-146">İstemcilere yalnızca güvenli kaynak isteği gönderilmesi gerektiğini bildirmek için [HSTS](#http-strict-transport-security-protocol-hsts) kullanılması önerilir (yalnızca üretimde).</span><span class="sxs-lookup"><span data-stu-id="7ae70-146">We recommend using [HSTS](#http-strict-transport-security-protocol-hsts) to signal to clients that only secure resource requests should be sent to the app (only in production).</span></span>

### <a name="port-configuration"></a><span data-ttu-id="7ae70-147">Bağlantı noktası yapılandırması</span><span class="sxs-lookup"><span data-stu-id="7ae70-147">Port configuration</span></span>

<span data-ttu-id="7ae70-148">Ara yazılımın güvenli olmayan bir isteği HTTPS 'ye yönlendirmesi için bir bağlantı noktası kullanılabilir olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7ae70-148">A port must be available for the middleware to redirect an insecure request to HTTPS.</span></span> <span data-ttu-id="7ae70-149">Kullanılabilir bağlantı noktası yoksa:</span><span class="sxs-lookup"><span data-stu-id="7ae70-149">If no port is available:</span></span>

* <span data-ttu-id="7ae70-150">HTTPS olarak yeniden yönlendirme gerçekleşmez.</span><span class="sxs-lookup"><span data-stu-id="7ae70-150">Redirection to HTTPS doesn't occur.</span></span>
* <span data-ttu-id="7ae70-151">Ara yazılım "yeniden yönlendirme için HTTPS bağlantı noktası saptanamadı" uyarısını günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="7ae70-151">The middleware logs the warning "Failed to determine the https port for redirect."</span></span>

<span data-ttu-id="7ae70-152">Aşağıdaki yaklaşımlardan herhangi birini kullanarak HTTPS bağlantı noktasını belirtin:</span><span class="sxs-lookup"><span data-stu-id="7ae70-152">Specify the HTTPS port using any of the following approaches:</span></span>

* <span data-ttu-id="7ae70-153">[Httpsredirectionoptions. HttpsPort](#options)öğesini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="7ae70-153">Set [HttpsRedirectionOptions.HttpsPort](#options).</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="7ae70-154">`https_port` [Konak ayarını](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#https_port)ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="7ae70-154">Set the `https_port` [host setting](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#https_port):</span></span>

  * <span data-ttu-id="7ae70-155">Konak yapılandırmasında.</span><span class="sxs-lookup"><span data-stu-id="7ae70-155">In host configuration.</span></span>
  * <span data-ttu-id="7ae70-156">`ASPNETCORE_HTTPS_PORT`Ortam değişkenini ayarlayarak.</span><span class="sxs-lookup"><span data-stu-id="7ae70-156">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="7ae70-157">' De bir üst düzey giriş ekleyerek *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="7ae70-157">By adding a top-level entry in *appsettings.json* :</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/3.x/appsettings.json?highlight=2)]

* <span data-ttu-id="7ae70-158">[ASPNETCORE_URLS ortam değişkenini](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#urls)kullanarak güvenli düzene sahip bir bağlantı noktası belirtin.</span><span class="sxs-lookup"><span data-stu-id="7ae70-158">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](../fundamentals/host/generic-host.md?view=aspnetcore-3.0#urls).</span></span> <span data-ttu-id="7ae70-159">Ortam değişkeni sunucusunu yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="7ae70-159">The environment variable configures the server.</span></span> <span data-ttu-id="7ae70-160">Ara yazılım, üzerinden HTTPS bağlantı noktasını dolaylı olarak bulur <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> .</span><span class="sxs-lookup"><span data-stu-id="7ae70-160">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="7ae70-161">Bu yaklaşım, ters proxy dağıtımlarında çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="7ae70-161">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

* <span data-ttu-id="7ae70-162">`https_port` [Konak ayarını](xref:fundamentals/host/web-host#https-port)ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="7ae70-162">Set the `https_port` [host setting](xref:fundamentals/host/web-host#https-port):</span></span>

  * <span data-ttu-id="7ae70-163">Konak yapılandırmasında.</span><span class="sxs-lookup"><span data-stu-id="7ae70-163">In host configuration.</span></span>
  * <span data-ttu-id="7ae70-164">`ASPNETCORE_HTTPS_PORT`Ortam değişkenini ayarlayarak.</span><span class="sxs-lookup"><span data-stu-id="7ae70-164">By setting the `ASPNETCORE_HTTPS_PORT` environment variable.</span></span>
  * <span data-ttu-id="7ae70-165">' De bir üst düzey giriş ekleyerek *appsettings.json* :</span><span class="sxs-lookup"><span data-stu-id="7ae70-165">By adding a top-level entry in *appsettings.json* :</span></span>

    [!code-json[](enforcing-ssl/sample-snapshot/2.x/appsettings.json?highlight=2)]

* <span data-ttu-id="7ae70-166">[ASPNETCORE_URLS ortam değişkenini](xref:fundamentals/host/web-host#server-urls)kullanarak güvenli düzene sahip bir bağlantı noktası belirtin.</span><span class="sxs-lookup"><span data-stu-id="7ae70-166">Indicate a port with the secure scheme using the [ASPNETCORE_URLS environment variable](xref:fundamentals/host/web-host#server-urls).</span></span> <span data-ttu-id="7ae70-167">Ortam değişkeni sunucusunu yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="7ae70-167">The environment variable configures the server.</span></span> <span data-ttu-id="7ae70-168">Ara yazılım, üzerinden HTTPS bağlantı noktasını dolaylı olarak bulur <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> .</span><span class="sxs-lookup"><span data-stu-id="7ae70-168">The middleware indirectly discovers the HTTPS port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span> <span data-ttu-id="7ae70-169">Bu yaklaşım, ters proxy dağıtımlarında çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="7ae70-169">This approach doesn't work in reverse proxy deployments.</span></span>

::: moniker-end

* <span data-ttu-id="7ae70-170">Geliştirme aşamasında, *üzerindelaunchsettings.js* bir https URL 'si ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="7ae70-170">In development, set an HTTPS URL in *launchsettings.json* .</span></span> <span data-ttu-id="7ae70-171">IIS Express kullanıldığında HTTPS 'yi etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="7ae70-171">Enable HTTPS when IIS Express is used.</span></span>

* <span data-ttu-id="7ae70-172">[Kestrel](xref:fundamentals/servers/kestrel) server veya [HTTP.sys](xref:fundamentals/servers/httpsys) Server 'ın herkese açık BIR uç dağıtımı için https URL uç noktası yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="7ae70-172">Configure an HTTPS URL endpoint for a public-facing edge deployment of [Kestrel](xref:fundamentals/servers/kestrel) server or [HTTP.sys](xref:fundamentals/servers/httpsys) server.</span></span> <span data-ttu-id="7ae70-173">Uygulama tarafından yalnızca **BIR HTTPS bağlantı noktası** kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7ae70-173">Only **one HTTPS port** is used by the app.</span></span> <span data-ttu-id="7ae70-174">Ara yazılım, ile bağlantı noktasını bulur <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> .</span><span class="sxs-lookup"><span data-stu-id="7ae70-174">The middleware discovers the port via <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature>.</span></span>

> [!NOTE]
> <span data-ttu-id="7ae70-175">Bir uygulama ters proxy yapılandırmasında çalıştırıldığında, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> kullanılabilir değildir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-175">When an app is run in a reverse proxy configuration, <xref:Microsoft.AspNetCore.Hosting.Server.Features.IServerAddressesFeature> isn't available.</span></span> <span data-ttu-id="7ae70-176">Bu bölümde açıklanan diğer yaklaşımlardan birini kullanarak bağlantı noktasını ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="7ae70-176">Set the port using one of the other approaches described in this section.</span></span>

### <a name="edge-deployments"></a><span data-ttu-id="7ae70-177">Edge dağıtımları</span><span class="sxs-lookup"><span data-stu-id="7ae70-177">Edge deployments</span></span> 

<span data-ttu-id="7ae70-178">Kestrel veya HTTP.sys, herkese açık bir uç sunucu olarak kullanıldığında, Kestrel veya HTTP.sys her ikisini de dinlemek için yapılandırılmış olmalıdır:</span><span class="sxs-lookup"><span data-stu-id="7ae70-178">When Kestrel or HTTP.sys is used as a public-facing edge server, Kestrel or HTTP.sys must be configured to listen on both:</span></span>

* <span data-ttu-id="7ae70-179">İstemcinin yeniden yönlendirildiği güvenli bağlantı noktası (genellikle üretim ortamında 443 ve geliştirme sırasında 5001).</span><span class="sxs-lookup"><span data-stu-id="7ae70-179">The secure port where the client is redirected (typically, 443 in production and 5001 in development).</span></span>
* <span data-ttu-id="7ae70-180">Güvenli olmayan bağlantı noktası (genellikle üretim ortamında 80 ve geliştirme sırasında 5000).</span><span class="sxs-lookup"><span data-stu-id="7ae70-180">The insecure port (typically, 80 in production and 5000 in development).</span></span>

<span data-ttu-id="7ae70-181">Güvenli olmayan bir istek alabilmesi ve istemciyi güvenli bağlantı noktasına yeniden yönlendirebilmesi için güvensiz bağlantı noktasına istemci tarafından erişilebilir olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-181">The insecure port must be accessible by the client in order for the app to receive an insecure request and redirect the client to the secure port.</span></span>

<span data-ttu-id="7ae70-182">Daha fazla bilgi için bkz. [Kestrel Endpoint Configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) veya <xref:fundamentals/servers/httpsys> .</span><span class="sxs-lookup"><span data-stu-id="7ae70-182">For more information, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration) or <xref:fundamentals/servers/httpsys>.</span></span>

### <a name="deployment-scenarios"></a><span data-ttu-id="7ae70-183">Dağıtım senaryoları</span><span class="sxs-lookup"><span data-stu-id="7ae70-183">Deployment scenarios</span></span>

<span data-ttu-id="7ae70-184">İstemci ve sunucu arasındaki herhangi bir güvenlik duvarının trafik için iletişim bağlantı noktaları açık olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-184">Any firewall between the client and server must also have communication ports open for traffic.</span></span>

<span data-ttu-id="7ae70-185">İstekler ters bir ara sunucu yapılandırmasında iletilirse, HTTPS yeniden yönlendirme ara yazılımı çağrılmadan önce [Iletilen üstbilgiler ara yazılımını](xref:host-and-deploy/proxy-load-balancer) kullanın.</span><span class="sxs-lookup"><span data-stu-id="7ae70-185">If requests are forwarded in a reverse proxy configuration, use [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) before calling HTTPS Redirection Middleware.</span></span> <span data-ttu-id="7ae70-186">İletilen üstbilgiler ara yazılımı, `Request.Scheme` üst bilgisini kullanarak öğesini güncelleştirir `X-Forwarded-Proto` .</span><span class="sxs-lookup"><span data-stu-id="7ae70-186">Forwarded Headers Middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header.</span></span> <span data-ttu-id="7ae70-187">Ara yazılım, yeniden yönlendirme URI 'Leri ve diğer güvenlik ilkelerinin doğru çalışmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-187">The middleware permits redirect URIs and other security policies to work correctly.</span></span> <span data-ttu-id="7ae70-188">Iletilen üstbilgiler ara yazılımı kullanılmazsa, arka uç uygulaması doğru düzeni alamayabilir ve yeniden yönlendirme döngüsünde sona ermeyebilir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-188">When Forwarded Headers Middleware isn't used, the backend app might not receive the correct scheme and end up in a redirect loop.</span></span> <span data-ttu-id="7ae70-189">Yaygın bir son kullanıcı hata iletisi, çok fazla yeniden yönlendirme meydana geldi.</span><span class="sxs-lookup"><span data-stu-id="7ae70-189">A common end user error message is that too many redirects have occurred.</span></span>

<span data-ttu-id="7ae70-190">Azure App Service dağıtım sırasında, [öğreticideki yönergeleri izleyin: mevcut bir özel SSL sertifikasını Azure 'A bağlama Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span><span class="sxs-lookup"><span data-stu-id="7ae70-190">When deploying to Azure App Service, follow the guidance in [Tutorial: Bind an existing custom SSL certificate to Azure Web Apps](/azure/app-service/app-service-web-tutorial-custom-ssl).</span></span>

### <a name="options"></a><span data-ttu-id="7ae70-191">Seçenekler</span><span class="sxs-lookup"><span data-stu-id="7ae70-191">Options</span></span>

<span data-ttu-id="7ae70-192">Aşağıdaki vurgulanan kod, ara yazılım seçeneklerini yapılandırmak için [Addhttpsredirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) öğesini çağırır:</span><span class="sxs-lookup"><span data-stu-id="7ae70-192">The following highlighted code calls [AddHttpsRedirection](/dotnet/api/microsoft.aspnetcore.builder.httpsredirectionservicesextensions.addhttpsredirection) to configure middleware options:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=14-18)]

::: moniker-end


<span data-ttu-id="7ae70-193">Çağırma `AddHttpsRedirection` yalnızca veya değerlerini değiştirmek için gereklidir `HttpsPort` `RedirectStatusCode` .</span><span class="sxs-lookup"><span data-stu-id="7ae70-193">Calling `AddHttpsRedirection` is only necessary to change the values of `HttpsPort` or `RedirectStatusCode`.</span></span>

<span data-ttu-id="7ae70-194">Vurgulanan önceki kod:</span><span class="sxs-lookup"><span data-stu-id="7ae70-194">The preceding highlighted code:</span></span>

* <span data-ttu-id="7ae70-195">[Httpsredirectionoptions. RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect> değerini, varsayılan değer olan olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="7ae70-195">Sets [HttpsRedirectionOptions.RedirectStatusCode](xref:Microsoft.AspNetCore.HttpsPolicy.HttpsRedirectionOptions.RedirectStatusCode*) to <xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect>, which is the default value.</span></span> <span data-ttu-id="7ae70-196">Atamaları için sınıfının alanlarını kullanın <xref:Microsoft.AspNetCore.Http.StatusCodes> `RedirectStatusCode` .</span><span class="sxs-lookup"><span data-stu-id="7ae70-196">Use the fields of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for assignments to `RedirectStatusCode`.</span></span>
* <span data-ttu-id="7ae70-197">HTTPS bağlantı noktasını 5001 olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="7ae70-197">Sets the HTTPS port to 5001.</span></span>

#### <a name="configure-permanent-redirects-in-production"></a><span data-ttu-id="7ae70-198">Üretimde kalıcı yeniden yönlendirmeleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="7ae70-198">Configure permanent redirects in production</span></span>

<span data-ttu-id="7ae70-199">Ara yazılım varsayılan olarak tüm yeniden yönlendirmelere bir [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) gönderir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-199">The middleware defaults to sending a [Status307TemporaryRedirect](/dotnet/api/microsoft.aspnetcore.http.statuscodes.status307temporaryredirect) with all redirects.</span></span> <span data-ttu-id="7ae70-200">Uygulama geliştirme dışı bir ortamda olduğunda kalıcı yeniden yönlendirme durum kodu göndermek isterseniz, geliştirme dışı bir ortam için bir koşullu denetim içindeki ara yazılım seçenekleri yapılandırmasını sarın.</span><span class="sxs-lookup"><span data-stu-id="7ae70-200">If you prefer to send a permanent redirect status code when the app is in a non-Development environment, wrap the middleware options configuration in a conditional check for a non-Development environment.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7ae70-201">*Startup.cs* 'de Hizmetleri yapılandırırken:</span><span class="sxs-lookup"><span data-stu-id="7ae70-201">When configuring services in *Startup.cs* :</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IWebHostEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="7ae70-202">*Startup.cs* 'de Hizmetleri yapılandırırken:</span><span class="sxs-lookup"><span data-stu-id="7ae70-202">When configuring services in *Startup.cs* :</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // IHostingEnvironment (stored in _env) is injected into the Startup class.
    if (!_env.IsDevelopment())
    {
        services.AddHttpsRedirection(options =>
        {
            options.RedirectStatusCode = StatusCodes.Status308PermanentRedirect;
            options.HttpsPort = 443;
        });
    }
}
```

::: moniker-end


## <a name="https-redirection-middleware-alternative-approach"></a><span data-ttu-id="7ae70-203">HTTPS yeniden yönlendirme ara yazılımı alternatif yaklaşımı</span><span class="sxs-lookup"><span data-stu-id="7ae70-203">HTTPS Redirection Middleware alternative approach</span></span>

<span data-ttu-id="7ae70-204">HTTPS yeniden yönlendirme ara yazılımı () kullanmanın bir alternatifi `UseHttpsRedirection` , URL yeniden yazma ara yazılımı ( `AddRedirectToHttps` ) kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="7ae70-204">An alternative to using HTTPS Redirection Middleware (`UseHttpsRedirection`) is to use URL Rewriting Middleware (`AddRedirectToHttps`).</span></span> <span data-ttu-id="7ae70-205">`AddRedirectToHttps` , yeniden yönlendirme yürütüldüğünde durum kodunu ve bağlantı noktasını da ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-205">`AddRedirectToHttps` can also set the status code and port when the redirect is executed.</span></span> <span data-ttu-id="7ae70-206">Daha fazla bilgi için bkz. [URL yeniden yazma ara yazılımı](xref:fundamentals/url-rewriting).</span><span class="sxs-lookup"><span data-stu-id="7ae70-206">For more information, see [URL Rewriting Middleware](xref:fundamentals/url-rewriting).</span></span>

<span data-ttu-id="7ae70-207">Ek yeniden yönlendirme kuralları gereksinimi olmadan HTTPS 'ye yönlendirilirken, bu konuda açıklanan HTTPS yeniden yönlendirme ara yazılımı () kullanmanızı öneririz `UseHttpsRedirection` .</span><span class="sxs-lookup"><span data-stu-id="7ae70-207">When redirecting to HTTPS without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware (`UseHttpsRedirection`) described in this topic.</span></span>

<a name="hsts"></a>

## <a name="http-strict-transport-security-protocol-hsts"></a><span data-ttu-id="7ae70-208">HTTP katı aktarım güvenliği Protokolü (HSTS)</span><span class="sxs-lookup"><span data-stu-id="7ae70-208">HTTP Strict Transport Security Protocol (HSTS)</span></span>

<span data-ttu-id="7ae70-209">Her [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project)için, [http katı aktarım güvenliği (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) , yanıt üst bilgisi kullanılarak bir Web uygulaması tarafından belirtilen bir katılım güvenlik geliştirmedir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-209">Per [OWASP](https://www.owasp.org/index.php/About_The_Open_Web_Application_Security_Project), [HTTP Strict Transport Security (HSTS)](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) is an opt-in security enhancement that's specified by a web app through the use of a response header.</span></span> <span data-ttu-id="7ae70-210">[HSTS 'yi destekleyen bir tarayıcı](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) bu üstbilgiyi alırsa:</span><span class="sxs-lookup"><span data-stu-id="7ae70-210">When a [browser that supports HSTS](https://cheatsheetseries.owasp.org/cheatsheets/Transport_Layer_Protection_Cheat_Sheet.html#browser-support) receives this header:</span></span>

* <span data-ttu-id="7ae70-211">Tarayıcı, HTTP üzerinden iletişimin gönderilmesini önleyen etki alanı için yapılandırmayı depolar.</span><span class="sxs-lookup"><span data-stu-id="7ae70-211">The browser stores configuration for the domain that prevents sending any communication over HTTP.</span></span> <span data-ttu-id="7ae70-212">Tarayıcı, HTTPS üzerinden tüm iletişimi zorlar.</span><span class="sxs-lookup"><span data-stu-id="7ae70-212">The browser forces all communication over HTTPS.</span></span>
* <span data-ttu-id="7ae70-213">Tarayıcı, kullanıcının güvenilmeyen veya geçersiz sertifikalar kullanmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="7ae70-213">The browser prevents the user from using untrusted or invalid certificates.</span></span> <span data-ttu-id="7ae70-214">Tarayıcı, kullanıcının bu sertifikaya geçici olarak güvenmesine izin veren istemleri devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="7ae70-214">The browser disables prompts that allow a user to temporarily trust such a certificate.</span></span>

<span data-ttu-id="7ae70-215">HSTS istemci tarafından zorlandığından bazı sınırlamalar vardır:</span><span class="sxs-lookup"><span data-stu-id="7ae70-215">Because HSTS is enforced by the client, it has some limitations:</span></span>

* <span data-ttu-id="7ae70-216">İstemcinin HSTS 'yi desteklemesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-216">The client must support HSTS.</span></span>
* <span data-ttu-id="7ae70-217">HSTS, HSTS ilkesini oluşturmak için en az bir başarılı HTTPS isteği gerektirir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-217">HSTS requires at least one successful HTTPS request to establish the HSTS policy.</span></span>
* <span data-ttu-id="7ae70-218">Uygulamanın her HTTP isteğini denetlemesi ve HTTP isteğini yeniden yönlendirmesi veya reddetmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-218">The application must check every HTTP request and redirect or reject the HTTP request.</span></span>

<span data-ttu-id="7ae70-219">ASP.NET Core 2,1 ve üzeri, genişletme yöntemiyle HSTS uygular `UseHsts` .</span><span class="sxs-lookup"><span data-stu-id="7ae70-219">ASP.NET Core 2.1 and later implements HSTS with the `UseHsts` extension method.</span></span> <span data-ttu-id="7ae70-220">Aşağıdaki kod, `UseHsts` uygulama [geliştirme modunda](xref:fundamentals/environments)olmadığında çağrı yapılır:</span><span class="sxs-lookup"><span data-stu-id="7ae70-220">The following code calls `UseHsts` when the app isn't in [development mode](xref:fundamentals/environments):</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet1&highlight=11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet1&highlight=10)]

::: moniker-end

<span data-ttu-id="7ae70-221">`UseHsts` geliştirme aşamasında önerilmez çünkü HSTS ayarları tarayıcılar tarafından yüksek oranda önbelleklenebilir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-221">`UseHsts` isn't recommended in development because the HSTS settings are highly cacheable by browsers.</span></span> <span data-ttu-id="7ae70-222">Varsayılan olarak, `UseHsts` yerel geri döngü adresini dışlar.</span><span class="sxs-lookup"><span data-stu-id="7ae70-222">By default, `UseHsts` excludes the local loopback address.</span></span>

<span data-ttu-id="7ae70-223">İlk kez HTTPS 'yi uygulayan üretim ortamları için, yöntemlerden birini kullanarak ilk [HstsOptions. maxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) değerini küçük bir değere ayarlayın <xref:System.TimeSpan> .</span><span class="sxs-lookup"><span data-stu-id="7ae70-223">For production environments that are implementing HTTPS for the first time, set the initial [HstsOptions.MaxAge](xref:Microsoft.AspNetCore.HttpsPolicy.HstsOptions.MaxAge*) to a small value using one of the <xref:System.TimeSpan> methods.</span></span> <span data-ttu-id="7ae70-224">HTTPS altyapısını HTTP 'ye döndürmeniz gerekirse, değeri saat olarak bir tek güne kadar ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="7ae70-224">Set the value from hours to no more than a single day in case you need to revert the HTTPS infrastructure to HTTP.</span></span> <span data-ttu-id="7ae70-225">HTTPS yapılandırmasının sürdürülebilirliği konusunda emin olduktan sonra, HSTS `max-age` değerini artırın; yaygın olarak kullanılan bir değer bir yıldır.</span><span class="sxs-lookup"><span data-stu-id="7ae70-225">After you're confident in the sustainability of the HTTPS configuration, increase the HSTS `max-age` value; a commonly used value is one year.</span></span>

<span data-ttu-id="7ae70-226">Aşağıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="7ae70-226">The following code:</span></span>


::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](enforcing-ssl/sample-snapshot/3.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

[!code-csharp[](enforcing-ssl/sample-snapshot/2.x/Startup.cs?name=snippet2&highlight=5-12)]

::: moniker-end


* <span data-ttu-id="7ae70-227">Üstbilginin preload parametresini ayarlar `Strict-Transport-Security` .</span><span class="sxs-lookup"><span data-stu-id="7ae70-227">Sets the preload parameter of the `Strict-Transport-Security` header.</span></span> <span data-ttu-id="7ae70-228">Önyükleme, [RFC HSTS belirtiminin](https://tools.ietf.org/html/rfc6797)bir parçası değildir, ancak Web tarayıcıları tarafından Yeni yüklemede HSTS sitelerini önceden yüklemek için desteklenir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-228">Preload isn't part of the [RFC HSTS specification](https://tools.ietf.org/html/rfc6797), but is supported by web browsers to preload HSTS sites on fresh install.</span></span> <span data-ttu-id="7ae70-229">Daha fazla bilgi için bkz. [https://hstspreload.org/](https://hstspreload.org/).</span><span class="sxs-lookup"><span data-stu-id="7ae70-229">For more information, see [https://hstspreload.org/](https://hstspreload.org/).</span></span>
* <span data-ttu-id="7ae70-230">SSTS ilkesini konak alt etki alanlarını barındıracak şekilde uygulayan [ıncludealt etki alanını](https://tools.ietf.org/html/rfc6797#section-6.1.2)sağlar.</span><span class="sxs-lookup"><span data-stu-id="7ae70-230">Enables [includeSubDomain](https://tools.ietf.org/html/rfc6797#section-6.1.2), which applies the HSTS policy to Host subdomains.</span></span>
* <span data-ttu-id="7ae70-231">`max-age`Üstbilginin parametresini 60 güne açık olarak ayarlar `Strict-Transport-Security` .</span><span class="sxs-lookup"><span data-stu-id="7ae70-231">Explicitly sets the `max-age` parameter of the `Strict-Transport-Security` header to 60 days.</span></span> <span data-ttu-id="7ae70-232">Ayarlanmazsa, varsayılan olarak 30 gün olur.</span><span class="sxs-lookup"><span data-stu-id="7ae70-232">If not set, defaults to 30 days.</span></span> <span data-ttu-id="7ae70-233">Daha fazla bilgi için bkz. [Maksimum yaş yönergesi](https://tools.ietf.org/html/rfc6797#section-6.1.1).</span><span class="sxs-lookup"><span data-stu-id="7ae70-233">For more information, see the [max-age directive](https://tools.ietf.org/html/rfc6797#section-6.1.1).</span></span>
* <span data-ttu-id="7ae70-234">`example.com`Dışlanacak konaklar listesine ekler.</span><span class="sxs-lookup"><span data-stu-id="7ae70-234">Adds `example.com` to the list of hosts to exclude.</span></span>

<span data-ttu-id="7ae70-235">`UseHsts` Aşağıdaki geri döngü Konakları dışlar:</span><span class="sxs-lookup"><span data-stu-id="7ae70-235">`UseHsts` excludes the following loopback hosts:</span></span>

* <span data-ttu-id="7ae70-236">`localhost` : IPv4 geri döngü adresi.</span><span class="sxs-lookup"><span data-stu-id="7ae70-236">`localhost` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="7ae70-237">`127.0.0.1` : IPv4 geri döngü adresi.</span><span class="sxs-lookup"><span data-stu-id="7ae70-237">`127.0.0.1` : The IPv4 loopback address.</span></span>
* <span data-ttu-id="7ae70-238">`[::1]` : IPv6 geri döngü adresi.</span><span class="sxs-lookup"><span data-stu-id="7ae70-238">`[::1]` : The IPv6 loopback address.</span></span>

## <a name="opt-out-of-httpshsts-on-project-creation"></a><span data-ttu-id="7ae70-239">Proje oluşturulurken HTTPS/HSTS 'nin katılımı</span><span class="sxs-lookup"><span data-stu-id="7ae70-239">Opt-out of HTTPS/HSTS on project creation</span></span>

<span data-ttu-id="7ae70-240">Bağlantı güvenliğinin ağın herkese açık kenarından işlendiği bazı arka uç hizmet senaryolarında, her düğümde bağlantı güvenliğini yapılandırmak gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-240">In some backend service scenarios where connection security is handled at the public-facing edge of the network, configuring connection security at each node isn't required.</span></span> <span data-ttu-id="7ae70-241">Visual Studio 'da veya [DotNet yeni](/dotnet/core/tools/dotnet-new) komutundan oluşturulan Web Apps, [https yeniden yönlendirmeyi](#require-https) ve [HSTS](#http-strict-transport-security-protocol-hsts)'yi etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-241">Web apps that are generated from the templates in Visual Studio or from the [dotnet new](/dotnet/core/tools/dotnet-new) command enable [HTTPS redirection](#require-https) and [HSTS](#http-strict-transport-security-protocol-hsts).</span></span> <span data-ttu-id="7ae70-242">Bu senaryoları gerektirmeyen dağıtımlar için, uygulama şablondan oluşturulduğunda HTTPS/HSTS 'nin devre dışı bırakabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7ae70-242">For deployments that don't require these scenarios, you can opt-out of HTTPS/HSTS when the app is created from the template.</span></span>

<span data-ttu-id="7ae70-243">HTTPS/HSTS 'yi devre dışı bırakmak için:</span><span class="sxs-lookup"><span data-stu-id="7ae70-243">To opt-out of HTTPS/HSTS:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7ae70-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7ae70-244">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="7ae70-245">**Https Için Yapılandır** onay kutusunun işaretini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="7ae70-245">Uncheck the **Configure for HTTPS** check box.</span></span>

::: moniker range=">= aspnetcore-3.0"

![HTTPS için Yapılandır onay kutusunun seçili olduğu yeni ASP.NET Core Web uygulaması iletişim kutusu.](enforcing-ssl/_static/out-vs2019.png)

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

![HTTPS için Yapılandır onay kutusunun seçili olduğu yeni ASP.NET Core Web uygulaması iletişim kutusu.](enforcing-ssl/_static/out.png)

::: moniker-end


# <a name="net-core-cli"></a>[<span data-ttu-id="7ae70-248">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="7ae70-248">.NET Core CLI</span></span>](#tab/netcore-cli) 

<span data-ttu-id="7ae70-249">Seçeneğini kullanın `--no-https` .</span><span class="sxs-lookup"><span data-stu-id="7ae70-249">Use the `--no-https` option.</span></span> <span data-ttu-id="7ae70-250">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="7ae70-250">For example</span></span>

```dotnetcli
dotnet new webapp --no-https
```

---

<a name="trust"></a>

## <a name="trust-the-aspnet-core-https-development-certificate-on-windows-and-macos"></a><span data-ttu-id="7ae70-251">Windows ve macOS 'ta ASP.NET Core HTTPS geliştirme sertifikasına güvenin</span><span class="sxs-lookup"><span data-stu-id="7ae70-251">Trust the ASP.NET Core HTTPS development certificate on Windows and macOS</span></span>

<span data-ttu-id="7ae70-252">.NET Core SDK bir HTTPS geliştirme sertifikası içerir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-252">The .NET Core SDK includes an HTTPS development certificate.</span></span> <span data-ttu-id="7ae70-253">Sertifika, ilk çalıştırma deneyiminin bir parçası olarak yüklenir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-253">The certificate is installed as part of the first-run experience.</span></span> <span data-ttu-id="7ae70-254">Örneğin, `dotnet --info` aşağıdaki çıkışın bir varyasyonunu üretir:</span><span class="sxs-lookup"><span data-stu-id="7ae70-254">For example, `dotnet --info` produces a variation of the following output:</span></span>

```
ASP.NET Core
------------
Successfully installed the ASP.NET Core HTTPS Development Certificate.
To trust the certificate run 'dotnet dev-certs https --trust' (Windows and macOS only).
For establishing trust on other platforms refer to the platform specific documentation.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

<span data-ttu-id="7ae70-255">.NET Core SDK yükleme ASP.NET Core HTTPS geliştirme sertifikasını Yerel Kullanıcı sertifika deposuna yüklenir.</span><span class="sxs-lookup"><span data-stu-id="7ae70-255">Installing the .NET Core SDK installs the ASP.NET Core HTTPS development certificate to the local user certificate store.</span></span> <span data-ttu-id="7ae70-256">Sertifika yüklendi, ancak güvenilir değil.</span><span class="sxs-lookup"><span data-stu-id="7ae70-256">The certificate has been installed, but it's not trusted.</span></span> <span data-ttu-id="7ae70-257">Sertifikaya güvenmek için, DotNet aracını çalıştırmak için tek seferlik bir adım gerçekleştirin `dev-certs` :</span><span class="sxs-lookup"><span data-stu-id="7ae70-257">To trust the certificate, perform the one-time step to run the dotnet `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --trust
```

<span data-ttu-id="7ae70-258">Aşağıdaki komut araç üzerinde yardım sağlar `dev-certs` :</span><span class="sxs-lookup"><span data-stu-id="7ae70-258">The following command provides help on the `dev-certs` tool:</span></span>

```dotnetcli
dotnet dev-certs https --help
```

## <a name="how-to-set-up-a-developer-certificate-for-docker"></a><span data-ttu-id="7ae70-259">Docker için Geliştirici Sertifikası ayarlama</span><span class="sxs-lookup"><span data-stu-id="7ae70-259">How to set up a developer certificate for Docker</span></span>

<span data-ttu-id="7ae70-260">[Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/6199)bakın.</span><span class="sxs-lookup"><span data-stu-id="7ae70-260">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/6199).</span></span>

<a name="ssl-linux"></a>

## <a name="trust-https-certificate-on-linux"></a><span data-ttu-id="7ae70-261">Linux üzerinde HTTPS sertifikasına güven</span><span class="sxs-lookup"><span data-stu-id="7ae70-261">Trust HTTPS certificate on Linux</span></span>

<!-- Instructions to be updated by engineering team after 5.0 RTM. -->

<span data-ttu-id="7ae70-262">Linux hakkındaki yönergeler için dağıtım belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="7ae70-262">For instructions on Linux, refer to the distribution documentation.</span></span>

<a name="wsl"></a>

## <a name="trust-https-certificate-from-windows-subsystem-for-linux"></a><span data-ttu-id="7ae70-263">Linux için Windows alt sistemi 'nden HTTPS sertifikasına güven</span><span class="sxs-lookup"><span data-stu-id="7ae70-263">Trust HTTPS certificate from Windows Subsystem for Linux</span></span>

<span data-ttu-id="7ae70-264">Linux için Windows alt sistemi (WSL), HTTPS otomatik olarak imzalanan bir sertifika oluşturur. Windows sertifika deposunu, WSL sertifikasına güvenmek üzere yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="7ae70-264">The Windows Subsystem for Linux (WSL) generates an HTTPS self-signed cert. To configure the Windows certificate store to trust the WSL certificate:</span></span>

* <span data-ttu-id="7ae70-265">WSL tarafından oluşturulan sertifikayı dışarı aktarmak için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="7ae70-265">Run the following command to export the WSL-generated certificate:</span></span>

  ```
  dotnet dev-certs https -ep %USERPROFILE%\.aspnet\https\aspnetapp.pfx -p <cryptic-password>
  ```
* <span data-ttu-id="7ae70-266">Bir WSL penceresinde, aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="7ae70-266">In a WSL window, run the following command:</span></span>

  ```
    ASPNETCORE_Kestrel__Certificates__Default__Password="<cryptic-password>" 
    ASPNETCORE_Kestrel__Certificates__Default__Path=/mnt/c/Users/user-name/.aspnet/https/aspnetapp.pfx
    dotnet watch run
  ```

  <span data-ttu-id="7ae70-267">Yukarıdaki komut, ortam değişkenlerini, Linux 'un Windows güvenilen sertifikasını kullanmasını sağlayacak şekilde ayarlar.</span><span class="sxs-lookup"><span data-stu-id="7ae70-267">The preceding command sets the environment variables so Linux uses the Windows trusted certificate.</span></span>

## <a name="troubleshoot-certificate-problems"></a><span data-ttu-id="7ae70-268">Sertifika sorunlarını giderme</span><span class="sxs-lookup"><span data-stu-id="7ae70-268">Troubleshoot certificate problems</span></span>

<span data-ttu-id="7ae70-269">Bu bölümde, ASP.NET Core HTTPS geliştirme sertifikası [yüklendiğinde ve güveniliyorsa](#trust), ancak hala sertifikaya güvenilmediğini belirten tarayıcı uyarıları varsa yardım sağlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="7ae70-269">This section provides help when the ASP.NET Core HTTPS development certificate has been [installed and trusted](#trust), but you still have browser warnings that the certificate is not trusted.</span></span> <span data-ttu-id="7ae70-270">ASP.NET Core HTTPS geliştirme sertifikası, [Kestrel](xref:fundamentals/servers/kestrel)tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7ae70-270">The ASP.NET Core HTTPS development certificate is used by [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

### <a name="all-platforms---certificate-not-trusted"></a><span data-ttu-id="7ae70-271">Tüm platformlar-Sertifikaya güvenilmiyor</span><span class="sxs-lookup"><span data-stu-id="7ae70-271">All platforms - certificate not trusted</span></span>

<span data-ttu-id="7ae70-272">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="7ae70-272">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="7ae70-273">Açık olan tüm tarayıcı örneklerini kapatın.</span><span class="sxs-lookup"><span data-stu-id="7ae70-273">Close any browser instances open.</span></span> <span data-ttu-id="7ae70-274">Uygulamaya yeni bir tarayıcı penceresi açın.</span><span class="sxs-lookup"><span data-stu-id="7ae70-274">Open a new browser window to app.</span></span> <span data-ttu-id="7ae70-275">Sertifika güveni tarayıcılar tarafından önbelleğe alınır.</span><span class="sxs-lookup"><span data-stu-id="7ae70-275">Certificate trust is cached by browsers.</span></span>

<span data-ttu-id="7ae70-276">Yukarıdaki komutlar çoğu tarayıcı güveni sorununu çözüyor.</span><span class="sxs-lookup"><span data-stu-id="7ae70-276">The preceding commands solve most browser trust issues.</span></span> <span data-ttu-id="7ae70-277">Tarayıcı yine de sertifikaya güvenmiyor ise, aşağıdaki platforma özgü önerileri izleyin.</span><span class="sxs-lookup"><span data-stu-id="7ae70-277">If the browser is still not trusting the certificate, follow the platform-specific suggestions that follow.</span></span>

### <a name="docker---certificate-not-trusted"></a><span data-ttu-id="7ae70-278">Docker-Sertifikaya güvenilmiyor</span><span class="sxs-lookup"><span data-stu-id="7ae70-278">Docker - certificate not trusted</span></span>

* <span data-ttu-id="7ae70-279">*C:\Users \{ Kullanıcı} \AppData\Roaming\ASP.NET\Https* klasörünü silin.</span><span class="sxs-lookup"><span data-stu-id="7ae70-279">Delete the *C:\Users\{USER}\AppData\Roaming\ASP.NET\Https* folder.</span></span>
* <span data-ttu-id="7ae70-280">Çözümü temizleyin.</span><span class="sxs-lookup"><span data-stu-id="7ae70-280">Clean the solution.</span></span> <span data-ttu-id="7ae70-281">*Bin* ve *obj* klasörlerini silin.</span><span class="sxs-lookup"><span data-stu-id="7ae70-281">Delete the *bin* and *obj* folders.</span></span>
* <span data-ttu-id="7ae70-282">Geliştirme aracını yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="7ae70-282">Restart the development tool.</span></span> <span data-ttu-id="7ae70-283">Örneğin, Visual Studio, Visual Studio Code veya Mac için Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="7ae70-283">For example, Visual Studio, Visual Studio Code, or Visual Studio for Mac.</span></span>

### <a name="windows---certificate-not-trusted"></a><span data-ttu-id="7ae70-284">Windows-Sertifikaya güvenilmiyor</span><span class="sxs-lookup"><span data-stu-id="7ae70-284">Windows - certificate not trusted</span></span>

* <span data-ttu-id="7ae70-285">Sertifika deposundaki sertifikaları kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="7ae70-285">Check the certificates in the certificate store.</span></span> <span data-ttu-id="7ae70-286">Hem hem de içinde `localhost` `ASP.NET Core HTTPS development certificate` kolay ada sahip bir sertifika olmalıdır `Current User > Personal > Certificates``Current User > Trusted root certification authorities > Certificates`</span><span class="sxs-lookup"><span data-stu-id="7ae70-286">There should be a `localhost` certificate with the `ASP.NET Core HTTPS development certificate` friendly name both under `Current User > Personal > Certificates` and `Current User > Trusted root certification authorities > Certificates`</span></span>
* <span data-ttu-id="7ae70-287">Yalnızca kişisel ve güvenilen kök sertifika yetkililerinden bulunan tüm sertifikaları kaldırın.</span><span class="sxs-lookup"><span data-stu-id="7ae70-287">Remove all the found certificates from both Personal and Trusted root certification authorities.</span></span> <span data-ttu-id="7ae70-288">IIS Express localhost **sertifikasını kaldırmayın.**</span><span class="sxs-lookup"><span data-stu-id="7ae70-288">Do **not** remove the IIS Express localhost certificate.</span></span>
* <span data-ttu-id="7ae70-289">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="7ae70-289">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="7ae70-290">Açık olan tüm tarayıcı örneklerini kapatın.</span><span class="sxs-lookup"><span data-stu-id="7ae70-290">Close any browser instances open.</span></span> <span data-ttu-id="7ae70-291">Uygulamaya yeni bir tarayıcı penceresi açın.</span><span class="sxs-lookup"><span data-stu-id="7ae70-291">Open a new browser window to app.</span></span>

### <a name="os-x---certificate-not-trusted"></a><span data-ttu-id="7ae70-292">OS X-Sertifikaya güvenilmiyor</span><span class="sxs-lookup"><span data-stu-id="7ae70-292">OS X - certificate not trusted</span></span>

* <span data-ttu-id="7ae70-293">Anahtarlık erişimini açın.</span><span class="sxs-lookup"><span data-stu-id="7ae70-293">Open KeyChain Access.</span></span>
* <span data-ttu-id="7ae70-294">Sistem anahtarlığı ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="7ae70-294">Select the System keychain.</span></span>
* <span data-ttu-id="7ae70-295">Localhost sertifikası olup olmadığını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="7ae70-295">Check for the presence of a localhost certificate.</span></span>
* <span data-ttu-id="7ae70-296">`+`Tüm kullanıcılar için güvenilir olduğunu göstermek için simgenin üzerinde bir simge içerip içermediğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="7ae70-296">Check that it contains a `+` symbol on the icon to indicate it's trusted for all users.</span></span>
* <span data-ttu-id="7ae70-297">Sertifikayı sistem anahtarlığınızdan kaldırın.</span><span class="sxs-lookup"><span data-stu-id="7ae70-297">Remove the certificate from the system keychain.</span></span>
* <span data-ttu-id="7ae70-298">Aşağıdaki komutları çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="7ae70-298">Run the following commands:</span></span>

```dotnetcli
dotnet dev-certs https --clean
dotnet dev-certs https --trust
```

<span data-ttu-id="7ae70-299">Açık olan tüm tarayıcı örneklerini kapatın.</span><span class="sxs-lookup"><span data-stu-id="7ae70-299">Close any browser instances open.</span></span> <span data-ttu-id="7ae70-300">Uygulamaya yeni bir tarayıcı penceresi açın.</span><span class="sxs-lookup"><span data-stu-id="7ae70-300">Open a new browser window to app.</span></span>

<span data-ttu-id="7ae70-301">Visual Studio 'da sertifika sorunlarını gidermek için [IIS Express (DotNet/AspNetCore #16892) kullanarak https hatası](https://github.com/dotnet/AspNetCore/issues/16892) makalesine bakın.</span><span class="sxs-lookup"><span data-stu-id="7ae70-301">See [HTTPS Error using IIS Express (dotnet/AspNetCore #16892)](https://github.com/dotnet/AspNetCore/issues/16892) for troubleshooting certificate issues with Visual Studio.</span></span>

### <a name="iis-express-ssl-certificate-used-with-visual-studio"></a><span data-ttu-id="7ae70-302">Visual Studio ile kullanılan SSL sertifikası IIS Express</span><span class="sxs-lookup"><span data-stu-id="7ae70-302">IIS Express SSL certificate used with Visual Studio</span></span>

<span data-ttu-id="7ae70-303">IIS Express sertifikayla ilgili sorunları gidermek için Visual Studio yükleyicisinden **Onar** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="7ae70-303">To fix problems with the IIS Express certificate, select **Repair** from the Visual Studio installer.</span></span> <span data-ttu-id="7ae70-304">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/aspnetcore/issues/16892)bakın.</span><span class="sxs-lookup"><span data-stu-id="7ae70-304">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/16892).</span></span>

## <a name="additional-information"></a><span data-ttu-id="7ae70-305">Ek bilgiler</span><span class="sxs-lookup"><span data-stu-id="7ae70-305">Additional information</span></span>

* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="7ae70-306">Linux üzerinde Apache: HTTPS yapılandırmasıyla konak ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="7ae70-306">Host ASP.NET Core on Linux with Apache: HTTPS configuration</span></span>](xref:host-and-deploy/linux-apache#https-configuration)
* [<span data-ttu-id="7ae70-307">NGINX ile Linux üzerinde ana bilgisayar ASP.NET Core: HTTPS yapılandırması</span><span class="sxs-lookup"><span data-stu-id="7ae70-307">Host ASP.NET Core on Linux with Nginx: HTTPS configuration</span></span>](xref:host-and-deploy/linux-nginx#https-configuration)
* [<span data-ttu-id="7ae70-308">IIS 'de SSL ayarlama</span><span class="sxs-lookup"><span data-stu-id="7ae70-308">How to Set Up SSL on IIS</span></span>](/iis/manage/configuring-security/how-to-set-up-ssl-on-iis)
* [<span data-ttu-id="7ae70-309">OWASP HSTS tarayıcı desteği</span><span class="sxs-lookup"><span data-stu-id="7ae70-309">OWASP HSTS browser support</span></span>](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet#Browser_Support)