---
title: ASP.NET Core Web sunucusu uygulamasını HTTP.sys
author: rick-anderson
description: Windows üzerinde ASP.NET Core için bir Web sunucusu olan HTTP.sys hakkında bilgi edinin. HTTP.sys çekirdek modu sürücüsü üzerine inşa edilen HTTP.sys, IIS olmadan Internet 'e doğrudan bağlanmak için kullanılabilen Kestrel için bir alternatiftir.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 9c65abd5a055bb677a14921296316e7e03760bc2
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "96855371"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="38a8d-104">ASP.NET Core Web sunucusu uygulamasını HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="38a8d-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="38a8d-105">[Tom Dykstra](https://github.com/tdykstra) ve [Chris](https://github.com/Tratcher) 'e göre</span><span class="sxs-lookup"><span data-stu-id="38a8d-105">By [Tom Dykstra](https://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="38a8d-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) , yalnızca Windows üzerinde çalışan [ASP.NET Core için bir Web sunucusudur](xref:fundamentals/servers/index) .</span><span class="sxs-lookup"><span data-stu-id="38a8d-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="38a8d-107">HTTP.sys, [Kestrel](xref:fundamentals/servers/kestrel) Server için bir alternatiftir ve Kestrel tarafından sağlamayan bazı özellikler sunar.</span><span class="sxs-lookup"><span data-stu-id="38a8d-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="38a8d-108">HTTP.sys [ASP.NET Core modülüyle](xref:host-and-deploy/aspnet-core-module) uyumlu DEĞILDIR ve ııs veya IIS Express ile kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="38a8d-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="38a8d-109">HTTP.sys aşağıdaki özellikleri destekler:</span><span class="sxs-lookup"><span data-stu-id="38a8d-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="38a8d-110">Windows Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="38a8d-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="38a8d-111">Bağlantı noktası Paylaşımı</span><span class="sxs-lookup"><span data-stu-id="38a8d-111">Port sharing</span></span>
* <span data-ttu-id="38a8d-112">SNı ile HTTPS</span><span class="sxs-lookup"><span data-stu-id="38a8d-112">HTTPS with SNI</span></span>
* <span data-ttu-id="38a8d-113">TLS üzerinden HTTP/2 (Windows 10 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="38a8d-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="38a8d-114">Doğrudan dosya iletimi</span><span class="sxs-lookup"><span data-stu-id="38a8d-114">Direct file transmission</span></span>
* <span data-ttu-id="38a8d-115">Yanıtları Önbelleğe Alma</span><span class="sxs-lookup"><span data-stu-id="38a8d-115">Response caching</span></span>
* <span data-ttu-id="38a8d-116">WebSockets (Windows 8 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="38a8d-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="38a8d-117">Desteklenen Windows sürümleri:</span><span class="sxs-lookup"><span data-stu-id="38a8d-117">Supported Windows versions:</span></span>

* <span data-ttu-id="38a8d-118">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="38a8d-118">Windows 7 or later</span></span>
* <span data-ttu-id="38a8d-119">Windows Server 2008 R2 veya sonraki sürümü</span><span class="sxs-lookup"><span data-stu-id="38a8d-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="38a8d-120">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="38a8d-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="38a8d-121">Ne zaman kullanılacağı HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="38a8d-121">When to use HTTP.sys</span></span>

<span data-ttu-id="38a8d-122">HTTP.sys, şu yerlerde olan dağıtımlar için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="38a8d-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="38a8d-123">Sunucuyu IIS kullanmadan doğrudan Internet 'e sunmaya gerek vardır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys doğrudan Internet ile iletişim kurar](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="38a8d-125">İç dağıtım, [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)gibi Kestrel 'de kullanılamayan bir özelliği gerektirir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-125">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys doğrudan iç ağla iletişim kurar](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="38a8d-127">HTTP.sys, birçok saldırı türüne karşı koruyan ve tam özellikli bir Web sunucusu için sağlamlık, güvenlik ve ölçeklenebilirlik sağlayan çok sayıda teknolojiden oluşur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-127">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="38a8d-128">IIS, HTTP.sys en üstünde bir HTTP dinleyicisi olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-128">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="38a8d-129">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="38a8d-129">HTTP/2 support</span></span>

<span data-ttu-id="38a8d-130">Aşağıdaki temel gereksinimler karşılanıyorsa ASP.NET Core uygulamalar için [http/2](https://httpwg.org/specs/rfc7540.html) etkinleştirilir:</span><span class="sxs-lookup"><span data-stu-id="38a8d-130">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="38a8d-131">Windows Server 2016/Windows 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="38a8d-131">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="38a8d-132">[Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı</span><span class="sxs-lookup"><span data-stu-id="38a8d-132">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="38a8d-133">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="38a8d-133">TLS 1.2 or later connection</span></span>

<span data-ttu-id="38a8d-134">Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-134">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="38a8d-135">HTTP/2 varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-135">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="38a8d-136">HTTP/2 bağlantısı kurulmadıysa, bağlantı HTTP/1.1 'ye geri döner.</span><span class="sxs-lookup"><span data-stu-id="38a8d-136">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="38a8d-137">Windows 'un gelecek bir sürümünde http/2 yapılandırma bayrakları, HTTP.sys ile HTTP/2 devre dışı bırakma özelliği de dahil olmak üzere kullanılabilir olacaktır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-137">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="38a8d-138">Kerberos ile çekirdek modu kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="38a8d-138">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="38a8d-139">Kerberos kimlik doğrulama protokolü ile çekirdek modu kimlik doğrulamasına temsilciler HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="38a8d-139">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="38a8d-140">Kullanıcı modu kimlik doğrulaması, Kerberos ve HTTP.sys desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="38a8d-140">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="38a8d-141">Makine hesabı, Active Directory alındığı ve kullanıcının kimliğini doğrulamak için istemci tarafından sunucuya iletilen Kerberos belirtecinin/biletinin şifresini çözmek için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-141">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="38a8d-142">Uygulamanın kullanıcısına değil, ana bilgisayar için hizmet asıl adını (SPN) kaydedin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-142">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="38a8d-143">HTTP.sys kullanma</span><span class="sxs-lookup"><span data-stu-id="38a8d-143">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="38a8d-144">ASP.NET Core uygulamasını kullanacak şekilde yapılandırma HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="38a8d-144">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="38a8d-145"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Ana bilgisayarı oluştururken, gerekli herhangi bir yöntemi belirterek uzantı yöntemini çağırın <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> .</span><span class="sxs-lookup"><span data-stu-id="38a8d-145">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="38a8d-146">Aşağıdaki örnek, seçeneklerini varsayılan değerlerine ayarlar:</span><span class="sxs-lookup"><span data-stu-id="38a8d-146">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="38a8d-147">Ek HTTP.sys yapılandırması, [kayıt defteri ayarları](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)aracılığıyla işlenir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-147">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="38a8d-148">**HTTP.sys seçenekleri**</span><span class="sxs-lookup"><span data-stu-id="38a8d-148">**HTTP.sys options**</span></span>

| <span data-ttu-id="38a8d-149">Özellik</span><span class="sxs-lookup"><span data-stu-id="38a8d-149">Property</span></span> | <span data-ttu-id="38a8d-150">Açıklama</span><span class="sxs-lookup"><span data-stu-id="38a8d-150">Description</span></span> | <span data-ttu-id="38a8d-151">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="38a8d-151">Default</span></span> |
| -------- | ----------- | :-----: |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO> | <span data-ttu-id="38a8d-152">Ve için zaman uyumlu giriş/çıkışa izin verilip verilmeyeceğini `HttpContext.Request.Body` denetleyin `HttpContext.Response.Body` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-152">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="38a8d-153">Authentication. AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="38a8d-153">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="38a8d-154">Anonim isteklere izin verin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-154">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="38a8d-155">Authentication. düzenleri</span><span class="sxs-lookup"><span data-stu-id="38a8d-155">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="38a8d-156">İzin verilen kimlik doğrulama düzenlerini belirtin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-156">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="38a8d-157">Dinleyici elden atılırken önce herhangi bir zamanda değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-157">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="38a8d-158">Değerler [authenticationdüzenlerinin numaralandırması](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)tarafından sağlanır: `Basic` ,,, `Kerberos` `Negotiate` `None` , ve `NTLM` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-158">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching> | <span data-ttu-id="38a8d-159">Uygun üst bilgileri içeren yanıtlar için [çekirdek modu](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) önbelleğe almayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-159">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="38a8d-160">Yanıt `Set-Cookie` , `Vary` veya `Pragma` üst bilgileri içermeyebilir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-160">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="38a8d-161">`Cache-Control` `public` Bir ya da değeri ya da bir üst bilgi içermelidir `shared-max-age` `max-age` `Expires` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-161">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Http503Verbosity> | <span data-ttu-id="38a8d-162">Azaltma koşulları nedeniyle istekleri reddetmeden HTTP.sys davranışı.</span><span class="sxs-lookup"><span data-stu-id="38a8d-162">The HTTP.sys behavior when rejecting requests due to throttling conditions.</span></span> | [<span data-ttu-id="38a8d-163">Http503VerbosityLevel. <br> Basit</span><span class="sxs-lookup"><span data-stu-id="38a8d-163">Http503VerbosityLevel.<br>Basic</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.Http503VerbosityLevel) |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="38a8d-164">En fazla eşzamanlı kabul sayısı.</span><span class="sxs-lookup"><span data-stu-id="38a8d-164">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="38a8d-165">5 &times; [ortam. <br> ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="38a8d-165">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="38a8d-166">Kabul edilecek eşzamanlı bağlantı sayısı üst sınırı.</span><span class="sxs-lookup"><span data-stu-id="38a8d-166">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="38a8d-167">`-1`Sonsuz için kullanın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-167">Use `-1` for infinite.</span></span> <span data-ttu-id="38a8d-168">`null`Kayıt defterinin makine genelindeki ayarını kullanmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-168">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="38a8d-169">(makine genelinde</span><span class="sxs-lookup"><span data-stu-id="38a8d-169">(machine-wide</span></span><br><span data-ttu-id="38a8d-170">ayarlanmasını</span><span class="sxs-lookup"><span data-stu-id="38a8d-170">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="38a8d-171"><a href="#maxrequestbodysize">MaxRequestBodySize</a> bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-171">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="38a8d-172">30000000 bayt</span><span class="sxs-lookup"><span data-stu-id="38a8d-172">30000000 bytes</span></span><br><span data-ttu-id="38a8d-173">(~ 28,6 MB)</span><span class="sxs-lookup"><span data-stu-id="38a8d-173">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="38a8d-174">Sıraya alınabilen en fazla istek sayısı.</span><span class="sxs-lookup"><span data-stu-id="38a8d-174">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="38a8d-175">1000</span><span class="sxs-lookup"><span data-stu-id="38a8d-175">1000</span></span> |
| `RequestQueueMode` | <span data-ttu-id="38a8d-176">Bu, sunucunun istek kuyruğunu oluşturma ve yapılandırmadan sorumlu olup olmadığını veya mevcut bir kuyruğa iliştirilmesinin gerekip gerekmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-176">This indicates whether the server is responsible for creating and configuring the request queue, or if it should attach to an existing queue.</span></span><br><span data-ttu-id="38a8d-177">Mevcut bir kuyruğa eklenirken, mevcut yapılandırma seçeneklerinin çoğu geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-177">Most existing configuration options do not apply when attaching to an existing queue.</span></span> | `RequestQueueMode.Create` |
| `RequestQueueName` | <span data-ttu-id="38a8d-178">HTTP.sys istek kuyruğunun adı.</span><span class="sxs-lookup"><span data-stu-id="38a8d-178">The name of the HTTP.sys request queue.</span></span> | <span data-ttu-id="38a8d-179">`null` (Anonim kuyruk)</span><span class="sxs-lookup"><span data-stu-id="38a8d-179">`null` (Anonymous queue)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="38a8d-180">İstemci bağlantısının kesilmesinden kaynaklanan yanıt gövdesi yazmasının, özel durumlar oluşturması veya normal şekilde tamamlanması gerektiğini belirtin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-180">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="38a8d-181">(normal olarak)</span><span class="sxs-lookup"><span data-stu-id="38a8d-181">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="38a8d-182"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager>Kayıt defterinde da yapılandırılabilen HTTP.sys yapılandırmasını kullanıma sunun.</span><span class="sxs-lookup"><span data-stu-id="38a8d-182">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="38a8d-183">Varsayılan değerler de dahil olmak üzere her bir ayar hakkında daha fazla bilgi edinmek için API bağlantılarını izleyin:</span><span class="sxs-lookup"><span data-stu-id="38a8d-183">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="38a8d-184"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody?displayProperty=nameWithType>: HTTP Sunucusu API 'sinin Keep-Alive bağlantısında varlık gövdesini boşaltışında izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="38a8d-184"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody?displayProperty=nameWithType>: Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="38a8d-185"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody?displayProperty=nameWithType>: İstek varlığı gövdesinin gelmesi için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="38a8d-185"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody?displayProperty=nameWithType>: Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="38a8d-186"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait?displayProperty=nameWithType>: HTTP Sunucusu API 'sinin istek üst bilgisini ayrıştırması için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="38a8d-186"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait?displayProperty=nameWithType>: Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="38a8d-187"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection?displayProperty=nameWithType>: Boştaki bir bağlantı için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="38a8d-187"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection?displayProperty=nameWithType>: Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="38a8d-188"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond?displayProperty=nameWithType>: Yanıt için en düşük gönderme hızı.</span><span class="sxs-lookup"><span data-stu-id="38a8d-188"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond?displayProperty=nameWithType>: The minimum send rate for the response.</span></span></li><li><span data-ttu-id="38a8d-189"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue?displayProperty=nameWithType>: Uygulama onu seçmeden önce istek kuyruğunda kalan süre için izin verilir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-189"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue?displayProperty=nameWithType>: Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="38a8d-190"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>HTTP.sys kaydolmak için öğesini belirtin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-190">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="38a8d-191"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add%2A?displayProperty=nameWithType>Koleksiyona bir ön ek eklemek için kullanılan en yararlı seçenektir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-191">The most useful is <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add%2A?displayProperty=nameWithType>, which is used to add a prefix to the collection.</span></span> <span data-ttu-id="38a8d-192">Bunlar, dinleyici elden atılıyor öncesinde herhangi bir zamanda değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-192">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="38a8d-193">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="38a8d-193">**MaxRequestBodySize**</span></span>

<span data-ttu-id="38a8d-194">Herhangi bir istek gövdesinin bayt olarak izin verilen en büyük boyutu.</span><span class="sxs-lookup"><span data-stu-id="38a8d-194">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="38a8d-195">Olarak ayarlandığında `null` , en büyük istek gövdesi boyutu sınırsızdır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-195">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="38a8d-196">Bu sınırın, her zaman sınırsız olan yükseltilmiş bağlantıları üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-196">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="38a8d-197">Tek bir ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yöntem, `IActionResult` <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="38a8d-197">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="38a8d-198">Uygulama isteği okumayı başlattıktan sonra bir istek üzerindeki sınırı yapılandırmayı denerse bir özel durum oluşur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-198">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="38a8d-199">Özelliğin `IsReadOnly` salt okuma durumunda olup olmadığını göstermek için bir özellik kullanılabilir `MaxRequestBodySize` , yani sınırı yapılandırmak için çok geç.</span><span class="sxs-lookup"><span data-stu-id="38a8d-199">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="38a8d-200">Uygulamanın istek başına geçersiz kılması gerekiyorsa <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> , şunu kullanın <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :</span><span class="sxs-lookup"><span data-stu-id="38a8d-200">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="38a8d-201">Visual Studio kullanıyorsanız, uygulamanın IIS veya IIS Express çalıştıracak şekilde yapılandırılmadığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="38a8d-201">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="38a8d-202">Visual Studio 'da varsayılan başlatma profili IIS Express içindir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-202">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="38a8d-203">Projeyi konsol uygulaması olarak çalıştırmak için, aşağıdaki ekran görüntüsünde gösterildiği gibi seçili profili el ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="38a8d-203">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![Konsol uygulaması profilini seçin](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="38a8d-205">Windows Server 'ı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="38a8d-205">Configure Windows Server</span></span>

1. <span data-ttu-id="38a8d-206">Uygulama için açılacak bağlantı noktalarını belirleme ve [Windows Güvenlik Duvarı](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 'Nı veya [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 'ini kullanarak trafiğin HTTP.sys ulaşmasını sağlamak üzere güvenlik duvarı bağlantı noktalarını açın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-206">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="38a8d-207">Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-207">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="38a8d-208">Bir Azure sanal makinesine dağıtım yaparken, [ağ güvenlik grubundaki](/azure/virtual-machines/windows/nsg-quickstart-portal)bağlantı noktalarını açın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-208">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="38a8d-209">Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-209">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="38a8d-210">Gerekirse, X. 509.440 sertifikalarını edinin ve yükler.</span><span class="sxs-lookup"><span data-stu-id="38a8d-210">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="38a8d-211">Windows 'da, [New-SelfSignedCertificate PowerShell cmdlet 'ini](/powershell/module/pkiclient/new-selfsignedcertificate)kullanarak otomatik olarak imzalanan sertifikalar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="38a8d-211">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="38a8d-212">Desteklenmeyen bir örnek için bkz. [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span><span class="sxs-lookup"><span data-stu-id="38a8d-212">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="38a8d-213">Sunucunun **yerel makine** > **Kişisel** deposunda otomatik olarak imzalanan veya CA imzalı sertifikalar yükler.</span><span class="sxs-lookup"><span data-stu-id="38a8d-213">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="38a8d-214">Uygulama [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise, .net core, .NET Framework veya her ikisini de (uygulama .NET Framework hedefleyen bir .NET Core uygulaması ise) yükler.</span><span class="sxs-lookup"><span data-stu-id="38a8d-214">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="38a8d-215">**.NET Core**: uygulama .NET Core gerektiriyorsa .NET Core [Indirmelerinde](https://dotnet.microsoft.com/download) **.NET Core çalışma zamanı** yükleyicisini edinip çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-215">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="38a8d-216">Tam SDK 'Yı sunucuya yüklemeyin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-216">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="38a8d-217">**.NET Framework**: uygulama .NET Framework gerektiriyorsa [.NET Framework yükleme kılavuzuna](/dotnet/framework/install/)bakın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-217">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="38a8d-218">Gerekli .NET Framework yüklemesi.</span><span class="sxs-lookup"><span data-stu-id="38a8d-218">Install the required .NET Framework.</span></span> <span data-ttu-id="38a8d-219">En son .NET Framework yükleyicisi [.NET Core İndirmeleri](https://dotnet.microsoft.com/download) sayfasından edinilebilir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-219">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="38a8d-220">Uygulama, [kendi içinde bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise, uygulama çalışma zamanını dağıtımda içerir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-220">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="38a8d-221">Sunucuda çerçeve yüklemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="38a8d-221">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="38a8d-222">Uygulamada URL 'Leri ve bağlantı noktalarını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-222">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="38a8d-223">Varsayılan olarak, ASP.NET Core öğesine bağlanır `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-223">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="38a8d-224">URL öneklerini ve bağlantı noktalarını yapılandırmak için seçenekler şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="38a8d-224">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="38a8d-225">`urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="38a8d-225">`urls` command-line argument</span></span>
   * <span data-ttu-id="38a8d-226">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="38a8d-226">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="38a8d-227">Aşağıdaki kod örneği, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> `10.0.0.4` 443 numaralı bağlantı noktasında sunucunun yerel IP adresi ile nasıl kullanılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="38a8d-227">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="38a8d-228">Avantajı `UrlPrefixes` , hatalı biçimli ön ekler için hemen bir hata iletisi oluşturulmasından oluşur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-228">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="38a8d-229">Ayarları `UrlPrefixes` geçersiz kıl `UseUrls` / `urls` / `ASPNETCORE_URLS` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-229">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="38a8d-230">Bu nedenle, `UseUrls` `urls` ve `ASPNETCORE_URLS` ortam değişkeninin avantajı Kestrel ve HTTP.sys arasında geçiş yapmak daha kolay bir hale gelir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-230">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="38a8d-231">HTTP.sys, [http sunucusu API UrlPrefix dize biçimlerini](/windows/win32/http/urlprefix-strings)kullanır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-231">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="38a8d-232">Üst düzey joker karakter bağlamaları ( `http://*:80/` ve `http://+:80` ) kullanılmamalıdır  .</span><span class="sxs-lookup"><span data-stu-id="38a8d-232">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="38a8d-233">Üst düzey joker karakter bağlamaları uygulama güvenliği güvenlik açıklarını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-233">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="38a8d-234">Bu hem güçlü hem de zayıf Joker karakterlere yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-234">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="38a8d-235">Joker karakterler yerine açık konak adlarını veya IP adreslerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-235">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="38a8d-236">Alt etki alanı joker bağlantısı (örneğin, `*.mysub.com` ), tüm üst etki alanını ( `*.com` Bu güvenlik açığı olan aksine) kontrol ediyorsanız bir güvenlik riski değildir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-236">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="38a8d-237">Daha fazla bilgi için bkz. [RFC 7230: bölüm 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="38a8d-237">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="38a8d-238">Ön ek, sunucudaki URL öneklerini ister.</span><span class="sxs-lookup"><span data-stu-id="38a8d-238">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="38a8d-239">HTTP.sys yapılandırmaya yönelik yerleşik araç *netsh.exe*.</span><span class="sxs-lookup"><span data-stu-id="38a8d-239">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="38a8d-240">*netsh.exe* , URL öneklerini ayırmak ve X. 509.440 sertifikaları atamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-240">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="38a8d-241">Araç, yönetici ayrıcalıkları gerektirir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-241">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="38a8d-242">Uygulamanın URL 'Lerini kaydetmek için *netsh.exe* aracını kullanın:</span><span class="sxs-lookup"><span data-stu-id="38a8d-242">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="38a8d-243">`<URL>`: Tam nitelikli Tekdüzen Kaynak Bulucu (URL).</span><span class="sxs-lookup"><span data-stu-id="38a8d-243">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="38a8d-244">Joker karakter bağlama kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-244">Don't use a wildcard binding.</span></span> <span data-ttu-id="38a8d-245">Geçerli bir ana bilgisayar adı veya yerel IP adresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-245">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="38a8d-246">*URL, sondaki eğik çizgi içermelidir.*</span><span class="sxs-lookup"><span data-stu-id="38a8d-246">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="38a8d-247">`<USER>`: Kullanıcı veya Kullanıcı grubu adını belirtir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-247">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="38a8d-248">Aşağıdaki örnekte sunucusunun yerel IP adresi `10.0.0.4` :</span><span class="sxs-lookup"><span data-stu-id="38a8d-248">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="38a8d-249">Bir URL kaydedildiğinde, araç ile yanıt verir `URL reservation successfully added` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-249">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="38a8d-250">Kayıtlı bir URL 'yi silmek için şu `delete urlacl` komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="38a8d-250">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="38a8d-251">X. 509.440 sertifikalarını sunucusuna kaydedin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-251">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="38a8d-252">Uygulamanın sertifikalarını kaydetmek için *netsh.exe* aracını kullanın:</span><span class="sxs-lookup"><span data-stu-id="38a8d-252">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="38a8d-253">`<IP>`: Bağlama için yerel IP adresini belirtir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-253">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="38a8d-254">Joker karakter bağlama kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-254">Don't use a wildcard binding.</span></span> <span data-ttu-id="38a8d-255">Geçerli bir IP adresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-255">Use a valid IP address.</span></span>
   * <span data-ttu-id="38a8d-256">`<PORT>`: Bağlama için bağlantı noktasını belirtir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-256">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="38a8d-257">`<THUMBPRINT>`: X. 509.440 sertifika parmak izi.</span><span class="sxs-lookup"><span data-stu-id="38a8d-257">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="38a8d-258">`<GUID>`: Uygulamayı bilgilendirme amacıyla temsil eden geliştirici tarafından oluşturulan bir GUID.</span><span class="sxs-lookup"><span data-stu-id="38a8d-258">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="38a8d-259">Başvuru amacıyla, GUID 'yi uygulamada bir paket etiketi olarak depolayın:</span><span class="sxs-lookup"><span data-stu-id="38a8d-259">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="38a8d-260">Visual Studio 'da:</span><span class="sxs-lookup"><span data-stu-id="38a8d-260">In Visual Studio:</span></span>
     * <span data-ttu-id="38a8d-261">**Çözüm Gezgini** ' de uygulamaya sağ tıklayıp **Özellikler**' i seçerek uygulamanın proje özelliklerini açın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-261">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="38a8d-262">**Paket** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-262">Select the **Package** tab.</span></span>
     * <span data-ttu-id="38a8d-263">**Etiketler** alanında oluşturduğunuz GUID 'yi girin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-263">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="38a8d-264">Visual Studio kullanmadığınız durumlarda:</span><span class="sxs-lookup"><span data-stu-id="38a8d-264">When not using Visual Studio:</span></span>
     * <span data-ttu-id="38a8d-265">Uygulamanın proje dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-265">Open the app's project file.</span></span>
     * <span data-ttu-id="38a8d-266">`<PackageTags>`Oluşturduğunuz GUID ile yeni veya var olan bir özelliği ekleyin `<PropertyGroup>` :</span><span class="sxs-lookup"><span data-stu-id="38a8d-266">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="38a8d-267">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="38a8d-267">In the following example:</span></span>

   * <span data-ttu-id="38a8d-268">Sunucunun yerel IP adresi `10.0.0.4` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-268">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="38a8d-269">Bir çevrimiçi rastgele GUID Oluşturucu değeri sağlar `appid` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-269">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="38a8d-270">Bir sertifika kaydedildiğinde, araç ile yanıt verir `SSL Certificate successfully added` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-270">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="38a8d-271">Bir sertifika kaydını silmek için şu komutu kullanın `delete sslcert` :</span><span class="sxs-lookup"><span data-stu-id="38a8d-271">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="38a8d-272">*netsh.exe* için başvuru belgeleri:</span><span class="sxs-lookup"><span data-stu-id="38a8d-272">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="38a8d-273">[Köprü Metni Aktarım Protokolü (HTTP) için Netsh komutları](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="38a8d-273">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="38a8d-274">UrlPrefix dizeleri</span><span class="sxs-lookup"><span data-stu-id="38a8d-274">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="38a8d-275">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-275">Run the app.</span></span>

   <span data-ttu-id="38a8d-276">1024 'den büyük bir bağlantı noktası numarası ile HTTP (HTTPS değil) kullanarak localhost 'a bağlanırken uygulamayı çalıştırmak için yönetici ayrıcalıklarına gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-276">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="38a8d-277">Diğer yapılandırmalarda (örneğin, yerel bir IP adresi veya 443 numaralı bağlantı noktasına bağlama), uygulamayı yönetici ayrıcalıklarıyla çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-277">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="38a8d-278">Uygulama, sunucunun genel IP adresinde yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-278">The app responds at the server's public IP address.</span></span> <span data-ttu-id="38a8d-279">Bu örnekte, sunucusuna, genel IP adresinde Internet 'ten ulaşılırsa `104.214.79.47` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-279">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="38a8d-280">Bu örnekte bir geliştirme sertifikası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-280">A development certificate is used in this example.</span></span> <span data-ttu-id="38a8d-281">Tarayıcının güvenilmeyen sertifika uyarısı atlandıktan sonra sayfa güvenli bir şekilde yüklenir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-281">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![Uygulamanın dizin sayfasını yüklü olarak gösteren tarayıcı penceresi](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="38a8d-283">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="38a8d-283">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="38a8d-284">Internet veya şirket ağından gelen isteklerle etkileşime geçen HTTP.sys tarafından barındırılan uygulamalar için, proxy sunucularının ve yük dengeleyiciler arkasında barındırılırken ek yapılandırma gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-284">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="38a8d-285">Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="38a8d-285">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="advanced-http2-features-to-support-grpc"></a><span data-ttu-id="38a8d-286">GRPC 'yi desteklemeye yönelik gelişmiş HTTP/2 özellikleri</span><span class="sxs-lookup"><span data-stu-id="38a8d-286">Advanced HTTP/2 features to support gRPC</span></span>

<span data-ttu-id="38a8d-287">HTTP.sys ' deki ek HTTP/2 özellikleri, yanıt tanıtımları ve sıfırlama çerçeveleri gönderme desteği dahil olmak üzere gRPC 'yi destekler.</span><span class="sxs-lookup"><span data-stu-id="38a8d-287">Additional HTTP/2 features in HTTP.sys support gRPC, including support for response trailers and sending reset frames.</span></span>

<span data-ttu-id="38a8d-288">GRPC 'yi HTTP.SYS çalıştırma gereksinimleri:</span><span class="sxs-lookup"><span data-stu-id="38a8d-288">Requirements to run gRPC with HTTP.SYS:</span></span>

* <span data-ttu-id="38a8d-289">Windows 10, işletim sistemi derlemesi 19041,508 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="38a8d-289">Windows 10, OS Build 19041.508 or later</span></span>
* <span data-ttu-id="38a8d-290">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="38a8d-290">TLS 1.2 or later connection</span></span>

### <a name="trailers"></a><span data-ttu-id="38a8d-291">Larına</span><span class="sxs-lookup"><span data-stu-id="38a8d-291">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="38a8d-292">Sıfırla</span><span class="sxs-lookup"><span data-stu-id="38a8d-292">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

## <a name="additional-resources"></a><span data-ttu-id="38a8d-293">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="38a8d-293">Additional resources</span></span>

* [<span data-ttu-id="38a8d-294">HTTP.sysile Windows kimlik doğrulamasını etkinleştir </span><span class="sxs-lookup"><span data-stu-id="38a8d-294">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="38a8d-295">HTTP Sunucusu API 'SI</span><span class="sxs-lookup"><span data-stu-id="38a8d-295">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="38a8d-296">ASPNET/HttpSysServer GitHub deposu (kaynak kodu)</span><span class="sxs-lookup"><span data-stu-id="38a8d-296">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="38a8d-297">Ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="38a8d-297">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="38a8d-298">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) , yalnızca Windows üzerinde çalışan [ASP.NET Core için bir Web sunucusudur](xref:fundamentals/servers/index) .</span><span class="sxs-lookup"><span data-stu-id="38a8d-298">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="38a8d-299">HTTP.sys, [Kestrel](xref:fundamentals/servers/kestrel) Server için bir alternatiftir ve Kestrel tarafından sağlamayan bazı özellikler sunar.</span><span class="sxs-lookup"><span data-stu-id="38a8d-299">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="38a8d-300">HTTP.sys [ASP.NET Core modülüyle](xref:host-and-deploy/aspnet-core-module) uyumlu DEĞILDIR ve ııs veya IIS Express ile kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="38a8d-300">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="38a8d-301">HTTP.sys aşağıdaki özellikleri destekler:</span><span class="sxs-lookup"><span data-stu-id="38a8d-301">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="38a8d-302">Windows Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="38a8d-302">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="38a8d-303">Bağlantı noktası Paylaşımı</span><span class="sxs-lookup"><span data-stu-id="38a8d-303">Port sharing</span></span>
* <span data-ttu-id="38a8d-304">SNı ile HTTPS</span><span class="sxs-lookup"><span data-stu-id="38a8d-304">HTTPS with SNI</span></span>
* <span data-ttu-id="38a8d-305">TLS üzerinden HTTP/2 (Windows 10 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="38a8d-305">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="38a8d-306">Doğrudan dosya iletimi</span><span class="sxs-lookup"><span data-stu-id="38a8d-306">Direct file transmission</span></span>
* <span data-ttu-id="38a8d-307">Yanıtları Önbelleğe Alma</span><span class="sxs-lookup"><span data-stu-id="38a8d-307">Response caching</span></span>
* <span data-ttu-id="38a8d-308">WebSockets (Windows 8 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="38a8d-308">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="38a8d-309">Desteklenen Windows sürümleri:</span><span class="sxs-lookup"><span data-stu-id="38a8d-309">Supported Windows versions:</span></span>

* <span data-ttu-id="38a8d-310">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="38a8d-310">Windows 7 or later</span></span>
* <span data-ttu-id="38a8d-311">Windows Server 2008 R2 veya sonraki sürümü</span><span class="sxs-lookup"><span data-stu-id="38a8d-311">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="38a8d-312">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="38a8d-312">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="38a8d-313">Ne zaman kullanılacağı HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="38a8d-313">When to use HTTP.sys</span></span>

<span data-ttu-id="38a8d-314">HTTP.sys, şu yerlerde olan dağıtımlar için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="38a8d-314">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="38a8d-315">Sunucuyu IIS kullanmadan doğrudan Internet 'e sunmaya gerek vardır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-315">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys doğrudan Internet ile iletişim kurar](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="38a8d-317">İç dağıtım, [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)gibi Kestrel 'de kullanılamayan bir özelliği gerektirir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-317">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys doğrudan iç ağla iletişim kurar](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="38a8d-319">HTTP.sys, birçok saldırı türüne karşı koruyan ve tam özellikli bir Web sunucusu için sağlamlık, güvenlik ve ölçeklenebilirlik sağlayan çok sayıda teknolojiden oluşur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-319">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="38a8d-320">IIS, HTTP.sys en üstünde bir HTTP dinleyicisi olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-320">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="38a8d-321">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="38a8d-321">HTTP/2 support</span></span>

<span data-ttu-id="38a8d-322">Aşağıdaki temel gereksinimler karşılanıyorsa ASP.NET Core uygulamalar için [http/2](https://httpwg.org/specs/rfc7540.html) etkinleştirilir:</span><span class="sxs-lookup"><span data-stu-id="38a8d-322">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="38a8d-323">Windows Server 2016/Windows 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="38a8d-323">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="38a8d-324">[Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı</span><span class="sxs-lookup"><span data-stu-id="38a8d-324">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="38a8d-325">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="38a8d-325">TLS 1.2 or later connection</span></span>

<span data-ttu-id="38a8d-326">Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-326">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="38a8d-327">HTTP/2 varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-327">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="38a8d-328">HTTP/2 bağlantısı kurulmadıysa, bağlantı HTTP/1.1 'ye geri döner.</span><span class="sxs-lookup"><span data-stu-id="38a8d-328">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="38a8d-329">Windows 'un gelecek bir sürümünde http/2 yapılandırma bayrakları, HTTP.sys ile HTTP/2 devre dışı bırakma özelliği de dahil olmak üzere kullanılabilir olacaktır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-329">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="38a8d-330">Kerberos ile çekirdek modu kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="38a8d-330">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="38a8d-331">Kerberos kimlik doğrulama protokolü ile çekirdek modu kimlik doğrulamasına temsilciler HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="38a8d-331">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="38a8d-332">Kullanıcı modu kimlik doğrulaması, Kerberos ve HTTP.sys desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="38a8d-332">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="38a8d-333">Makine hesabı, Active Directory alındığı ve kullanıcının kimliğini doğrulamak için istemci tarafından sunucuya iletilen Kerberos belirtecinin/biletinin şifresini çözmek için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-333">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="38a8d-334">Uygulamanın kullanıcısına değil, ana bilgisayar için hizmet asıl adını (SPN) kaydedin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-334">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="38a8d-335">HTTP.sys kullanma</span><span class="sxs-lookup"><span data-stu-id="38a8d-335">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="38a8d-336">ASP.NET Core uygulamasını kullanacak şekilde yapılandırma HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="38a8d-336">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="38a8d-337"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Ana bilgisayarı oluştururken, gerekli herhangi bir yöntemi belirterek uzantı yöntemini çağırın <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> .</span><span class="sxs-lookup"><span data-stu-id="38a8d-337">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="38a8d-338">Aşağıdaki örnek, seçeneklerini varsayılan değerlerine ayarlar:</span><span class="sxs-lookup"><span data-stu-id="38a8d-338">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="38a8d-339">Ek HTTP.sys yapılandırması, [kayıt defteri ayarları](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)aracılığıyla işlenir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-339">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="38a8d-340">**HTTP.sys seçenekleri**</span><span class="sxs-lookup"><span data-stu-id="38a8d-340">**HTTP.sys options**</span></span>

| <span data-ttu-id="38a8d-341">Özellik</span><span class="sxs-lookup"><span data-stu-id="38a8d-341">Property</span></span> | <span data-ttu-id="38a8d-342">Açıklama</span><span class="sxs-lookup"><span data-stu-id="38a8d-342">Description</span></span> | <span data-ttu-id="38a8d-343">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="38a8d-343">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="38a8d-344">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="38a8d-344">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="38a8d-345">Ve için zaman uyumlu giriş/çıkışa izin verilip verilmeyeceğini `HttpContext.Request.Body` denetleyin `HttpContext.Response.Body` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-345">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="38a8d-346">Authentication. AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="38a8d-346">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="38a8d-347">Anonim isteklere izin verin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-347">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="38a8d-348">Authentication. düzenleri</span><span class="sxs-lookup"><span data-stu-id="38a8d-348">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="38a8d-349">İzin verilen kimlik doğrulama düzenlerini belirtin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-349">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="38a8d-350">Dinleyici elden atılırken önce herhangi bir zamanda değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-350">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="38a8d-351">Değerler [authenticationdüzenlerinin numaralandırması](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)tarafından sağlanır: `Basic` ,,, `Kerberos` `Negotiate` `None` , ve `NTLM` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-351">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="38a8d-352">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="38a8d-352">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="38a8d-353">Uygun üst bilgileri içeren yanıtlar için [çekirdek modu](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) önbelleğe almayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-353">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="38a8d-354">Yanıt `Set-Cookie` , `Vary` veya `Pragma` üst bilgileri içermeyebilir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-354">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="38a8d-355">`Cache-Control` `public` Bir ya da değeri ya da bir üst bilgi içermelidir `shared-max-age` `max-age` `Expires` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-355">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="38a8d-356">En fazla eşzamanlı kabul sayısı.</span><span class="sxs-lookup"><span data-stu-id="38a8d-356">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="38a8d-357">5 &times; [ortam. <br> ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="38a8d-357">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="38a8d-358">Kabul edilecek eşzamanlı bağlantı sayısı üst sınırı.</span><span class="sxs-lookup"><span data-stu-id="38a8d-358">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="38a8d-359">`-1`Sonsuz için kullanın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-359">Use `-1` for infinite.</span></span> <span data-ttu-id="38a8d-360">`null`Kayıt defterinin makine genelindeki ayarını kullanmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-360">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="38a8d-361">(makine genelinde</span><span class="sxs-lookup"><span data-stu-id="38a8d-361">(machine-wide</span></span><br><span data-ttu-id="38a8d-362">ayarlanmasını</span><span class="sxs-lookup"><span data-stu-id="38a8d-362">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="38a8d-363"><a href="#maxrequestbodysize">MaxRequestBodySize</a> bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-363">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="38a8d-364">30000000 bayt</span><span class="sxs-lookup"><span data-stu-id="38a8d-364">30000000 bytes</span></span><br><span data-ttu-id="38a8d-365">(~ 28,6 MB)</span><span class="sxs-lookup"><span data-stu-id="38a8d-365">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="38a8d-366">Sıraya alınabilen en fazla istek sayısı.</span><span class="sxs-lookup"><span data-stu-id="38a8d-366">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="38a8d-367">1000</span><span class="sxs-lookup"><span data-stu-id="38a8d-367">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="38a8d-368">İstemci bağlantısının kesilmesinden kaynaklanan yanıt gövdesi yazmasının, özel durumlar oluşturması veya normal şekilde tamamlanması gerektiğini belirtin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-368">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="38a8d-369">(normal olarak)</span><span class="sxs-lookup"><span data-stu-id="38a8d-369">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="38a8d-370"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager>Kayıt defterinde da yapılandırılabilen HTTP.sys yapılandırmasını kullanıma sunun.</span><span class="sxs-lookup"><span data-stu-id="38a8d-370">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="38a8d-371">Varsayılan değerler de dahil olmak üzere her bir ayar hakkında daha fazla bilgi edinmek için API bağlantılarını izleyin:</span><span class="sxs-lookup"><span data-stu-id="38a8d-371">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="38a8d-372">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): http sunucu apı 'sinin Keep-Alive bağlantısında varlık gövdesini boşaltışında izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="38a8d-372">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="38a8d-373">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): istek varlığı gövdesinin gelmesi için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="38a8d-373">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="38a8d-374">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): http sunucusu API 'sinin istek üst bilgisini ayrıştırması için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="38a8d-374">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="38a8d-375">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): boştaki bir bağlantı için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="38a8d-375">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="38a8d-376">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): yanıt için en düşük gönderme hızı.</span><span class="sxs-lookup"><span data-stu-id="38a8d-376">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="38a8d-377">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): isteğin, uygulamanın onu seçmeden önce istek kuyruğunda kalması için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="38a8d-377">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="38a8d-378"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>HTTP.sys kaydolmak için öğesini belirtin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-378">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="38a8d-379">En yararlı olan [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), koleksiyona bir ön ek eklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-379">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="38a8d-380">Bunlar, dinleyici elden atılıyor öncesinde herhangi bir zamanda değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-380">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="38a8d-381">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="38a8d-381">**MaxRequestBodySize**</span></span>

<span data-ttu-id="38a8d-382">Herhangi bir istek gövdesinin bayt olarak izin verilen en büyük boyutu.</span><span class="sxs-lookup"><span data-stu-id="38a8d-382">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="38a8d-383">Olarak ayarlandığında `null` , en büyük istek gövdesi boyutu sınırsızdır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-383">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="38a8d-384">Bu sınırın, her zaman sınırsız olan yükseltilmiş bağlantıları üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-384">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="38a8d-385">Tek bir ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yöntem, `IActionResult` <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="38a8d-385">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="38a8d-386">Uygulama isteği okumayı başlattıktan sonra bir istek üzerindeki sınırı yapılandırmayı denerse bir özel durum oluşur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-386">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="38a8d-387">Özelliğin `IsReadOnly` salt okuma durumunda olup olmadığını göstermek için bir özellik kullanılabilir `MaxRequestBodySize` , yani sınırı yapılandırmak için çok geç.</span><span class="sxs-lookup"><span data-stu-id="38a8d-387">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="38a8d-388">Uygulamanın istek başına geçersiz kılması gerekiyorsa <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> , şunu kullanın <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :</span><span class="sxs-lookup"><span data-stu-id="38a8d-388">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="38a8d-389">Visual Studio kullanıyorsanız, uygulamanın IIS veya IIS Express çalıştıracak şekilde yapılandırılmadığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="38a8d-389">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="38a8d-390">Visual Studio 'da varsayılan başlatma profili IIS Express içindir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-390">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="38a8d-391">Projeyi konsol uygulaması olarak çalıştırmak için, aşağıdaki ekran görüntüsünde gösterildiği gibi seçili profili el ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="38a8d-391">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![Konsol uygulaması profilini seçin](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="38a8d-393">Windows Server 'ı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="38a8d-393">Configure Windows Server</span></span>

1. <span data-ttu-id="38a8d-394">Uygulama için açılacak bağlantı noktalarını belirleme ve [Windows Güvenlik Duvarı](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 'Nı veya [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 'ini kullanarak trafiğin HTTP.sys ulaşmasını sağlamak üzere güvenlik duvarı bağlantı noktalarını açın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-394">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="38a8d-395">Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-395">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="38a8d-396">Bir Azure sanal makinesine dağıtım yaparken, [ağ güvenlik grubundaki](/azure/virtual-machines/windows/nsg-quickstart-portal)bağlantı noktalarını açın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-396">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="38a8d-397">Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-397">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="38a8d-398">Gerekirse, X. 509.440 sertifikalarını edinin ve yükler.</span><span class="sxs-lookup"><span data-stu-id="38a8d-398">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="38a8d-399">Windows 'da, [New-SelfSignedCertificate PowerShell cmdlet 'ini](/powershell/module/pkiclient/new-selfsignedcertificate)kullanarak otomatik olarak imzalanan sertifikalar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="38a8d-399">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="38a8d-400">Desteklenmeyen bir örnek için bkz. [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span><span class="sxs-lookup"><span data-stu-id="38a8d-400">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="38a8d-401">Sunucunun **yerel makine** > **Kişisel** deposunda otomatik olarak imzalanan veya CA imzalı sertifikalar yükler.</span><span class="sxs-lookup"><span data-stu-id="38a8d-401">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="38a8d-402">Uygulama [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise, .net core, .NET Framework veya her ikisini de (uygulama .NET Framework hedefleyen bir .NET Core uygulaması ise) yükler.</span><span class="sxs-lookup"><span data-stu-id="38a8d-402">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="38a8d-403">**.NET Core**: uygulama .NET Core gerektiriyorsa .NET Core [Indirmelerinde](https://dotnet.microsoft.com/download) **.NET Core çalışma zamanı** yükleyicisini edinip çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-403">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="38a8d-404">Tam SDK 'Yı sunucuya yüklemeyin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-404">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="38a8d-405">**.NET Framework**: uygulama .NET Framework gerektiriyorsa [.NET Framework yükleme kılavuzuna](/dotnet/framework/install/)bakın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-405">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="38a8d-406">Gerekli .NET Framework yüklemesi.</span><span class="sxs-lookup"><span data-stu-id="38a8d-406">Install the required .NET Framework.</span></span> <span data-ttu-id="38a8d-407">En son .NET Framework yükleyicisi [.NET Core İndirmeleri](https://dotnet.microsoft.com/download) sayfasından edinilebilir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-407">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="38a8d-408">Uygulama, [kendi içinde bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise, uygulama çalışma zamanını dağıtımda içerir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-408">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="38a8d-409">Sunucuda çerçeve yüklemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="38a8d-409">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="38a8d-410">Uygulamada URL 'Leri ve bağlantı noktalarını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-410">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="38a8d-411">Varsayılan olarak, ASP.NET Core öğesine bağlanır `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-411">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="38a8d-412">URL öneklerini ve bağlantı noktalarını yapılandırmak için seçenekler şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="38a8d-412">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="38a8d-413">`urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="38a8d-413">`urls` command-line argument</span></span>
   * <span data-ttu-id="38a8d-414">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="38a8d-414">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="38a8d-415">Aşağıdaki kod örneği, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> `10.0.0.4` 443 numaralı bağlantı noktasında sunucunun yerel IP adresi ile nasıl kullanılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="38a8d-415">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="38a8d-416">Avantajı `UrlPrefixes` , hatalı biçimli ön ekler için hemen bir hata iletisi oluşturulmasından oluşur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-416">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="38a8d-417">Ayarları `UrlPrefixes` geçersiz kıl `UseUrls` / `urls` / `ASPNETCORE_URLS` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-417">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="38a8d-418">Bu nedenle, `UseUrls` `urls` ve `ASPNETCORE_URLS` ortam değişkeninin avantajı Kestrel ve HTTP.sys arasında geçiş yapmak daha kolay bir hale gelir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-418">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="38a8d-419">HTTP.sys, [http sunucusu API UrlPrefix dize biçimlerini](/windows/win32/http/urlprefix-strings)kullanır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-419">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="38a8d-420">Üst düzey joker karakter bağlamaları ( `http://*:80/` ve `http://+:80` ) kullanılmamalıdır  .</span><span class="sxs-lookup"><span data-stu-id="38a8d-420">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="38a8d-421">Üst düzey joker karakter bağlamaları uygulama güvenliği güvenlik açıklarını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-421">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="38a8d-422">Bu hem güçlü hem de zayıf Joker karakterlere yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-422">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="38a8d-423">Joker karakterler yerine açık konak adlarını veya IP adreslerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-423">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="38a8d-424">Alt etki alanı joker bağlantısı (örneğin, `*.mysub.com` ), tüm üst etki alanını ( `*.com` Bu güvenlik açığı olan aksine) kontrol ediyorsanız bir güvenlik riski değildir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-424">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="38a8d-425">Daha fazla bilgi için bkz. [RFC 7230: bölüm 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="38a8d-425">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="38a8d-426">Ön ek, sunucudaki URL öneklerini ister.</span><span class="sxs-lookup"><span data-stu-id="38a8d-426">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="38a8d-427">HTTP.sys yapılandırmaya yönelik yerleşik araç *netsh.exe*.</span><span class="sxs-lookup"><span data-stu-id="38a8d-427">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="38a8d-428">*netsh.exe* , URL öneklerini ayırmak ve X. 509.440 sertifikaları atamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-428">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="38a8d-429">Araç, yönetici ayrıcalıkları gerektirir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-429">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="38a8d-430">Uygulamanın URL 'Lerini kaydetmek için *netsh.exe* aracını kullanın:</span><span class="sxs-lookup"><span data-stu-id="38a8d-430">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="38a8d-431">`<URL>`: Tam nitelikli Tekdüzen Kaynak Bulucu (URL).</span><span class="sxs-lookup"><span data-stu-id="38a8d-431">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="38a8d-432">Joker karakter bağlama kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-432">Don't use a wildcard binding.</span></span> <span data-ttu-id="38a8d-433">Geçerli bir ana bilgisayar adı veya yerel IP adresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-433">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="38a8d-434">*URL, sondaki eğik çizgi içermelidir.*</span><span class="sxs-lookup"><span data-stu-id="38a8d-434">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="38a8d-435">`<USER>`: Kullanıcı veya Kullanıcı grubu adını belirtir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-435">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="38a8d-436">Aşağıdaki örnekte sunucusunun yerel IP adresi `10.0.0.4` :</span><span class="sxs-lookup"><span data-stu-id="38a8d-436">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="38a8d-437">Bir URL kaydedildiğinde, araç ile yanıt verir `URL reservation successfully added` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-437">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="38a8d-438">Kayıtlı bir URL 'yi silmek için şu `delete urlacl` komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="38a8d-438">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="38a8d-439">X. 509.440 sertifikalarını sunucusuna kaydedin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-439">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="38a8d-440">Uygulamanın sertifikalarını kaydetmek için *netsh.exe* aracını kullanın:</span><span class="sxs-lookup"><span data-stu-id="38a8d-440">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="38a8d-441">`<IP>`: Bağlama için yerel IP adresini belirtir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-441">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="38a8d-442">Joker karakter bağlama kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-442">Don't use a wildcard binding.</span></span> <span data-ttu-id="38a8d-443">Geçerli bir IP adresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-443">Use a valid IP address.</span></span>
   * <span data-ttu-id="38a8d-444">`<PORT>`: Bağlama için bağlantı noktasını belirtir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-444">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="38a8d-445">`<THUMBPRINT>`: X. 509.440 sertifika parmak izi.</span><span class="sxs-lookup"><span data-stu-id="38a8d-445">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="38a8d-446">`<GUID>`: Uygulamayı bilgilendirme amacıyla temsil eden geliştirici tarafından oluşturulan bir GUID.</span><span class="sxs-lookup"><span data-stu-id="38a8d-446">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="38a8d-447">Başvuru amacıyla, GUID 'yi uygulamada bir paket etiketi olarak depolayın:</span><span class="sxs-lookup"><span data-stu-id="38a8d-447">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="38a8d-448">Visual Studio 'da:</span><span class="sxs-lookup"><span data-stu-id="38a8d-448">In Visual Studio:</span></span>
     * <span data-ttu-id="38a8d-449">**Çözüm Gezgini** ' de uygulamaya sağ tıklayıp **Özellikler**' i seçerek uygulamanın proje özelliklerini açın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-449">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="38a8d-450">**Paket** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-450">Select the **Package** tab.</span></span>
     * <span data-ttu-id="38a8d-451">**Etiketler** alanında oluşturduğunuz GUID 'yi girin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-451">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="38a8d-452">Visual Studio kullanmadığınız durumlarda:</span><span class="sxs-lookup"><span data-stu-id="38a8d-452">When not using Visual Studio:</span></span>
     * <span data-ttu-id="38a8d-453">Uygulamanın proje dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-453">Open the app's project file.</span></span>
     * <span data-ttu-id="38a8d-454">`<PackageTags>`Oluşturduğunuz GUID ile yeni veya var olan bir özelliği ekleyin `<PropertyGroup>` :</span><span class="sxs-lookup"><span data-stu-id="38a8d-454">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="38a8d-455">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="38a8d-455">In the following example:</span></span>

   * <span data-ttu-id="38a8d-456">Sunucunun yerel IP adresi `10.0.0.4` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-456">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="38a8d-457">Bir çevrimiçi rastgele GUID Oluşturucu değeri sağlar `appid` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-457">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="38a8d-458">Bir sertifika kaydedildiğinde, araç ile yanıt verir `SSL Certificate successfully added` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-458">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="38a8d-459">Bir sertifika kaydını silmek için şu komutu kullanın `delete sslcert` :</span><span class="sxs-lookup"><span data-stu-id="38a8d-459">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="38a8d-460">*netsh.exe* için başvuru belgeleri:</span><span class="sxs-lookup"><span data-stu-id="38a8d-460">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="38a8d-461">[Köprü Metni Aktarım Protokolü (HTTP) için Netsh komutları](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="38a8d-461">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="38a8d-462">UrlPrefix dizeleri</span><span class="sxs-lookup"><span data-stu-id="38a8d-462">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="38a8d-463">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-463">Run the app.</span></span>

   <span data-ttu-id="38a8d-464">1024 'den büyük bir bağlantı noktası numarası ile HTTP (HTTPS değil) kullanarak localhost 'a bağlanırken uygulamayı çalıştırmak için yönetici ayrıcalıklarına gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-464">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="38a8d-465">Diğer yapılandırmalarda (örneğin, yerel bir IP adresi veya 443 numaralı bağlantı noktasına bağlama), uygulamayı yönetici ayrıcalıklarıyla çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-465">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="38a8d-466">Uygulama, sunucunun genel IP adresinde yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-466">The app responds at the server's public IP address.</span></span> <span data-ttu-id="38a8d-467">Bu örnekte, sunucusuna, genel IP adresinde Internet 'ten ulaşılırsa `104.214.79.47` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-467">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="38a8d-468">Bu örnekte bir geliştirme sertifikası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-468">A development certificate is used in this example.</span></span> <span data-ttu-id="38a8d-469">Tarayıcının güvenilmeyen sertifika uyarısı atlandıktan sonra sayfa güvenli bir şekilde yüklenir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-469">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![Uygulamanın dizin sayfasını yüklü olarak gösteren tarayıcı penceresi](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="38a8d-471">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="38a8d-471">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="38a8d-472">Internet veya şirket ağından gelen isteklerle etkileşime geçen HTTP.sys tarafından barındırılan uygulamalar için, proxy sunucularının ve yük dengeleyiciler arkasında barındırılırken ek yapılandırma gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-472">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="38a8d-473">Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="38a8d-473">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="38a8d-474">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="38a8d-474">Additional resources</span></span>

* [<span data-ttu-id="38a8d-475">HTTP.sysile Windows kimlik doğrulamasını etkinleştir </span><span class="sxs-lookup"><span data-stu-id="38a8d-475">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="38a8d-476">HTTP Sunucusu API 'SI</span><span class="sxs-lookup"><span data-stu-id="38a8d-476">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="38a8d-477">ASPNET/HttpSysServer GitHub deposu (kaynak kodu)</span><span class="sxs-lookup"><span data-stu-id="38a8d-477">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="38a8d-478">Ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="38a8d-478">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="38a8d-479">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) , yalnızca Windows üzerinde çalışan [ASP.NET Core için bir Web sunucusudur](xref:fundamentals/servers/index) .</span><span class="sxs-lookup"><span data-stu-id="38a8d-479">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="38a8d-480">HTTP.sys, [Kestrel](xref:fundamentals/servers/kestrel) Server için bir alternatiftir ve Kestrel tarafından sağlamayan bazı özellikler sunar.</span><span class="sxs-lookup"><span data-stu-id="38a8d-480">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="38a8d-481">HTTP.sys [ASP.NET Core modülüyle](xref:host-and-deploy/aspnet-core-module) uyumlu DEĞILDIR ve ııs veya IIS Express ile kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="38a8d-481">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="38a8d-482">HTTP.sys aşağıdaki özellikleri destekler:</span><span class="sxs-lookup"><span data-stu-id="38a8d-482">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="38a8d-483">Windows Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="38a8d-483">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="38a8d-484">Bağlantı noktası Paylaşımı</span><span class="sxs-lookup"><span data-stu-id="38a8d-484">Port sharing</span></span>
* <span data-ttu-id="38a8d-485">SNı ile HTTPS</span><span class="sxs-lookup"><span data-stu-id="38a8d-485">HTTPS with SNI</span></span>
* <span data-ttu-id="38a8d-486">TLS üzerinden HTTP/2 (Windows 10 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="38a8d-486">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="38a8d-487">Doğrudan dosya iletimi</span><span class="sxs-lookup"><span data-stu-id="38a8d-487">Direct file transmission</span></span>
* <span data-ttu-id="38a8d-488">Yanıtları Önbelleğe Alma</span><span class="sxs-lookup"><span data-stu-id="38a8d-488">Response caching</span></span>
* <span data-ttu-id="38a8d-489">WebSockets (Windows 8 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="38a8d-489">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="38a8d-490">Desteklenen Windows sürümleri:</span><span class="sxs-lookup"><span data-stu-id="38a8d-490">Supported Windows versions:</span></span>

* <span data-ttu-id="38a8d-491">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="38a8d-491">Windows 7 or later</span></span>
* <span data-ttu-id="38a8d-492">Windows Server 2008 R2 veya sonraki sürümü</span><span class="sxs-lookup"><span data-stu-id="38a8d-492">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="38a8d-493">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="38a8d-493">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="38a8d-494">Ne zaman kullanılacağı HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="38a8d-494">When to use HTTP.sys</span></span>

<span data-ttu-id="38a8d-495">HTTP.sys, şu yerlerde olan dağıtımlar için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="38a8d-495">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="38a8d-496">Sunucuyu IIS kullanmadan doğrudan Internet 'e sunmaya gerek vardır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-496">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys doğrudan Internet ile iletişim kurar](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="38a8d-498">İç dağıtım, [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)gibi Kestrel 'de kullanılamayan bir özelliği gerektirir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-498">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys doğrudan iç ağla iletişim kurar](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="38a8d-500">HTTP.sys, birçok saldırı türüne karşı koruyan ve tam özellikli bir Web sunucusu için sağlamlık, güvenlik ve ölçeklenebilirlik sağlayan çok sayıda teknolojiden oluşur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-500">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="38a8d-501">IIS, HTTP.sys en üstünde bir HTTP dinleyicisi olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-501">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="38a8d-502">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="38a8d-502">HTTP/2 support</span></span>

<span data-ttu-id="38a8d-503">Aşağıdaki temel gereksinimler karşılanıyorsa ASP.NET Core uygulamalar için [http/2](https://httpwg.org/specs/rfc7540.html) etkinleştirilir:</span><span class="sxs-lookup"><span data-stu-id="38a8d-503">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="38a8d-504">Windows Server 2016/Windows 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="38a8d-504">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="38a8d-505">[Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı</span><span class="sxs-lookup"><span data-stu-id="38a8d-505">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="38a8d-506">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="38a8d-506">TLS 1.2 or later connection</span></span>

<span data-ttu-id="38a8d-507">Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-507">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="38a8d-508">HTTP/2 varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-508">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="38a8d-509">HTTP/2 bağlantısı kurulmadıysa, bağlantı HTTP/1.1 'ye geri döner.</span><span class="sxs-lookup"><span data-stu-id="38a8d-509">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="38a8d-510">Windows 'un gelecek bir sürümünde http/2 yapılandırma bayrakları, HTTP.sys ile HTTP/2 devre dışı bırakma özelliği de dahil olmak üzere kullanılabilir olacaktır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-510">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="38a8d-511">Kerberos ile çekirdek modu kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="38a8d-511">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="38a8d-512">Kerberos kimlik doğrulama protokolü ile çekirdek modu kimlik doğrulamasına temsilciler HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="38a8d-512">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="38a8d-513">Kullanıcı modu kimlik doğrulaması, Kerberos ve HTTP.sys desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="38a8d-513">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="38a8d-514">Makine hesabı, Active Directory alındığı ve kullanıcının kimliğini doğrulamak için istemci tarafından sunucuya iletilen Kerberos belirtecinin/biletinin şifresini çözmek için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-514">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="38a8d-515">Uygulamanın kullanıcısına değil, ana bilgisayar için hizmet asıl adını (SPN) kaydedin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-515">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="38a8d-516">HTTP.sys kullanma</span><span class="sxs-lookup"><span data-stu-id="38a8d-516">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="38a8d-517">ASP.NET Core uygulamasını kullanacak şekilde yapılandırma HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="38a8d-517">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="38a8d-518">[Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) ([NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) kullanılırken proje dosyasındaki bir paket başvurusu gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-518">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="38a8d-519">`Microsoft.AspNetCore.App`Metapackage 'i kullanmıyorsanız [Microsoft. Aspnetcore. Server. httpsys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)öğesine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-519">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="38a8d-520"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Ana bilgisayarı oluştururken, gerekli herhangi bir yöntemi belirterek uzantı yöntemini çağırın <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> .</span><span class="sxs-lookup"><span data-stu-id="38a8d-520">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="38a8d-521">Aşağıdaki örnek, seçeneklerini varsayılan değerlerine ayarlar:</span><span class="sxs-lookup"><span data-stu-id="38a8d-521">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="38a8d-522">Ek HTTP.sys yapılandırması, [kayıt defteri ayarları](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)aracılığıyla işlenir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-522">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="38a8d-523">**HTTP.sys seçenekleri**</span><span class="sxs-lookup"><span data-stu-id="38a8d-523">**HTTP.sys options**</span></span>

| <span data-ttu-id="38a8d-524">Özellik</span><span class="sxs-lookup"><span data-stu-id="38a8d-524">Property</span></span> | <span data-ttu-id="38a8d-525">Açıklama</span><span class="sxs-lookup"><span data-stu-id="38a8d-525">Description</span></span> | <span data-ttu-id="38a8d-526">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="38a8d-526">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="38a8d-527">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="38a8d-527">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="38a8d-528">Ve için zaman uyumlu giriş/çıkışa izin verilip verilmeyeceğini `HttpContext.Request.Body` denetleyin `HttpContext.Response.Body` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-528">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="38a8d-529">Authentication. AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="38a8d-529">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="38a8d-530">Anonim isteklere izin verin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-530">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="38a8d-531">Authentication. düzenleri</span><span class="sxs-lookup"><span data-stu-id="38a8d-531">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="38a8d-532">İzin verilen kimlik doğrulama düzenlerini belirtin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-532">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="38a8d-533">Dinleyici elden atılırken önce herhangi bir zamanda değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-533">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="38a8d-534">Değerler [authenticationdüzenlerinin numaralandırması](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)tarafından sağlanır: `Basic` ,,, `Kerberos` `Negotiate` `None` , ve `NTLM` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-534">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="38a8d-535">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="38a8d-535">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="38a8d-536">Uygun üst bilgileri içeren yanıtlar için [çekirdek modu](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) önbelleğe almayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-536">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="38a8d-537">Yanıt `Set-Cookie` , `Vary` veya `Pragma` üst bilgileri içermeyebilir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-537">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="38a8d-538">`Cache-Control` `public` Bir ya da değeri ya da bir üst bilgi içermelidir `shared-max-age` `max-age` `Expires` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-538">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="38a8d-539">En fazla eşzamanlı kabul sayısı.</span><span class="sxs-lookup"><span data-stu-id="38a8d-539">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="38a8d-540">5 &times; [ortam. <br> ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="38a8d-540">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="38a8d-541">Kabul edilecek eşzamanlı bağlantı sayısı üst sınırı.</span><span class="sxs-lookup"><span data-stu-id="38a8d-541">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="38a8d-542">`-1`Sonsuz için kullanın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-542">Use `-1` for infinite.</span></span> <span data-ttu-id="38a8d-543">`null`Kayıt defterinin makine genelindeki ayarını kullanmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-543">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="38a8d-544">(makine genelinde</span><span class="sxs-lookup"><span data-stu-id="38a8d-544">(machine-wide</span></span><br><span data-ttu-id="38a8d-545">ayarlanmasını</span><span class="sxs-lookup"><span data-stu-id="38a8d-545">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="38a8d-546"><a href="#maxrequestbodysize">MaxRequestBodySize</a> bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-546">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="38a8d-547">30000000 bayt</span><span class="sxs-lookup"><span data-stu-id="38a8d-547">30000000 bytes</span></span><br><span data-ttu-id="38a8d-548">(~ 28,6 MB)</span><span class="sxs-lookup"><span data-stu-id="38a8d-548">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="38a8d-549">Sıraya alınabilen en fazla istek sayısı.</span><span class="sxs-lookup"><span data-stu-id="38a8d-549">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="38a8d-550">1000</span><span class="sxs-lookup"><span data-stu-id="38a8d-550">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="38a8d-551">İstemci bağlantısının kesilmesinden kaynaklanan yanıt gövdesi yazmasının, özel durumlar oluşturması veya normal şekilde tamamlanması gerektiğini belirtin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-551">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="38a8d-552">(normal olarak)</span><span class="sxs-lookup"><span data-stu-id="38a8d-552">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="38a8d-553"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager>Kayıt defterinde da yapılandırılabilen HTTP.sys yapılandırmasını kullanıma sunun.</span><span class="sxs-lookup"><span data-stu-id="38a8d-553">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="38a8d-554">Varsayılan değerler de dahil olmak üzere her bir ayar hakkında daha fazla bilgi edinmek için API bağlantılarını izleyin:</span><span class="sxs-lookup"><span data-stu-id="38a8d-554">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="38a8d-555">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): http sunucu apı 'sinin Keep-Alive bağlantısında varlık gövdesini boşaltışında izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="38a8d-555">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="38a8d-556">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): istek varlığı gövdesinin gelmesi için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="38a8d-556">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="38a8d-557">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): http sunucusu API 'sinin istek üst bilgisini ayrıştırması için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="38a8d-557">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="38a8d-558">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): boştaki bir bağlantı için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="38a8d-558">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="38a8d-559">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): yanıt için en düşük gönderme hızı.</span><span class="sxs-lookup"><span data-stu-id="38a8d-559">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="38a8d-560">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): isteğin, uygulamanın onu seçmeden önce istek kuyruğunda kalması için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="38a8d-560">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="38a8d-561"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>HTTP.sys kaydolmak için öğesini belirtin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-561">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="38a8d-562">En yararlı olan [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), koleksiyona bir ön ek eklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-562">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="38a8d-563">Bunlar, dinleyici elden atılıyor öncesinde herhangi bir zamanda değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-563">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="38a8d-564">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="38a8d-564">**MaxRequestBodySize**</span></span>

<span data-ttu-id="38a8d-565">Herhangi bir istek gövdesinin bayt olarak izin verilen en büyük boyutu.</span><span class="sxs-lookup"><span data-stu-id="38a8d-565">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="38a8d-566">Olarak ayarlandığında `null` , en büyük istek gövdesi boyutu sınırsızdır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-566">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="38a8d-567">Bu sınırın, her zaman sınırsız olan yükseltilmiş bağlantıları üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-567">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="38a8d-568">Tek bir ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yöntem, `IActionResult` <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="38a8d-568">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="38a8d-569">Uygulama isteği okumayı başlattıktan sonra bir istek üzerindeki sınırı yapılandırmayı denerse bir özel durum oluşur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-569">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="38a8d-570">Özelliğin `IsReadOnly` salt okuma durumunda olup olmadığını göstermek için bir özellik kullanılabilir `MaxRequestBodySize` , yani sınırı yapılandırmak için çok geç.</span><span class="sxs-lookup"><span data-stu-id="38a8d-570">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="38a8d-571">Uygulamanın istek başına geçersiz kılması gerekiyorsa <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> , şunu kullanın <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :</span><span class="sxs-lookup"><span data-stu-id="38a8d-571">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="38a8d-572">Visual Studio kullanıyorsanız, uygulamanın IIS veya IIS Express çalıştıracak şekilde yapılandırılmadığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="38a8d-572">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="38a8d-573">Visual Studio 'da varsayılan başlatma profili IIS Express içindir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-573">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="38a8d-574">Projeyi konsol uygulaması olarak çalıştırmak için, aşağıdaki ekran görüntüsünde gösterildiği gibi seçili profili el ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="38a8d-574">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![Konsol uygulaması profilini seçin](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="38a8d-576">Windows Server 'ı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="38a8d-576">Configure Windows Server</span></span>

1. <span data-ttu-id="38a8d-577">Uygulama için açılacak bağlantı noktalarını belirleme ve [Windows Güvenlik Duvarı](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 'Nı veya [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 'ini kullanarak trafiğin HTTP.sys ulaşmasını sağlamak üzere güvenlik duvarı bağlantı noktalarını açın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-577">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="38a8d-578">Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-578">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="38a8d-579">Bir Azure sanal makinesine dağıtım yaparken, [ağ güvenlik grubundaki](/azure/virtual-machines/windows/nsg-quickstart-portal)bağlantı noktalarını açın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-579">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="38a8d-580">Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-580">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="38a8d-581">Gerekirse, X. 509.440 sertifikalarını edinin ve yükler.</span><span class="sxs-lookup"><span data-stu-id="38a8d-581">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="38a8d-582">Windows 'da, [New-SelfSignedCertificate PowerShell cmdlet 'ini](/powershell/module/pkiclient/new-selfsignedcertificate)kullanarak otomatik olarak imzalanan sertifikalar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="38a8d-582">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="38a8d-583">Desteklenmeyen bir örnek için bkz. [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span><span class="sxs-lookup"><span data-stu-id="38a8d-583">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="38a8d-584">Sunucunun **yerel makine** > **Kişisel** deposunda otomatik olarak imzalanan veya CA imzalı sertifikalar yükler.</span><span class="sxs-lookup"><span data-stu-id="38a8d-584">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="38a8d-585">Uygulama [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise, .net core, .NET Framework veya her ikisini de (uygulama .NET Framework hedefleyen bir .NET Core uygulaması ise) yükler.</span><span class="sxs-lookup"><span data-stu-id="38a8d-585">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="38a8d-586">**.NET Core**: uygulama .NET Core gerektiriyorsa .NET Core [Indirmelerinde](https://dotnet.microsoft.com/download) **.NET Core çalışma zamanı** yükleyicisini edinip çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-586">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="38a8d-587">Tam SDK 'Yı sunucuya yüklemeyin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-587">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="38a8d-588">**.NET Framework**: uygulama .NET Framework gerektiriyorsa [.NET Framework yükleme kılavuzuna](/dotnet/framework/install/)bakın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-588">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="38a8d-589">Gerekli .NET Framework yüklemesi.</span><span class="sxs-lookup"><span data-stu-id="38a8d-589">Install the required .NET Framework.</span></span> <span data-ttu-id="38a8d-590">En son .NET Framework yükleyicisi [.NET Core İndirmeleri](https://dotnet.microsoft.com/download) sayfasından edinilebilir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-590">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="38a8d-591">Uygulama, [kendi içinde bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise, uygulama çalışma zamanını dağıtımda içerir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-591">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="38a8d-592">Sunucuda çerçeve yüklemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="38a8d-592">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="38a8d-593">Uygulamada URL 'Leri ve bağlantı noktalarını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-593">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="38a8d-594">Varsayılan olarak, ASP.NET Core öğesine bağlanır `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-594">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="38a8d-595">URL öneklerini ve bağlantı noktalarını yapılandırmak için seçenekler şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="38a8d-595">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="38a8d-596">`urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="38a8d-596">`urls` command-line argument</span></span>
   * <span data-ttu-id="38a8d-597">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="38a8d-597">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="38a8d-598">Aşağıdaki kod örneği, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> `10.0.0.4` 443 numaralı bağlantı noktasında sunucunun yerel IP adresi ile nasıl kullanılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="38a8d-598">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="38a8d-599">Avantajı `UrlPrefixes` , hatalı biçimli ön ekler için hemen bir hata iletisi oluşturulmasından oluşur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-599">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="38a8d-600">Ayarları `UrlPrefixes` geçersiz kıl `UseUrls` / `urls` / `ASPNETCORE_URLS` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-600">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="38a8d-601">Bu nedenle, `UseUrls` `urls` ve `ASPNETCORE_URLS` ortam değişkeninin avantajı Kestrel ve HTTP.sys arasında geçiş yapmak daha kolay bir hale gelir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-601">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="38a8d-602">HTTP.sys, [http sunucusu API UrlPrefix dize biçimlerini](/windows/win32/http/urlprefix-strings)kullanır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-602">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="38a8d-603">Üst düzey joker karakter bağlamaları ( `http://*:80/` ve `http://+:80` ) kullanılmamalıdır  .</span><span class="sxs-lookup"><span data-stu-id="38a8d-603">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="38a8d-604">Üst düzey joker karakter bağlamaları uygulama güvenliği güvenlik açıklarını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-604">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="38a8d-605">Bu hem güçlü hem de zayıf Joker karakterlere yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-605">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="38a8d-606">Joker karakterler yerine açık konak adlarını veya IP adreslerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-606">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="38a8d-607">Alt etki alanı joker bağlantısı (örneğin, `*.mysub.com` ), tüm üst etki alanını ( `*.com` Bu güvenlik açığı olan aksine) kontrol ediyorsanız bir güvenlik riski değildir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-607">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="38a8d-608">Daha fazla bilgi için bkz. [RFC 7230: bölüm 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="38a8d-608">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="38a8d-609">Ön ek, sunucudaki URL öneklerini ister.</span><span class="sxs-lookup"><span data-stu-id="38a8d-609">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="38a8d-610">HTTP.sys yapılandırmaya yönelik yerleşik araç *netsh.exe*.</span><span class="sxs-lookup"><span data-stu-id="38a8d-610">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="38a8d-611">*netsh.exe* , URL öneklerini ayırmak ve X. 509.440 sertifikaları atamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-611">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="38a8d-612">Araç, yönetici ayrıcalıkları gerektirir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-612">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="38a8d-613">Uygulamanın URL 'Lerini kaydetmek için *netsh.exe* aracını kullanın:</span><span class="sxs-lookup"><span data-stu-id="38a8d-613">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="38a8d-614">`<URL>`: Tam nitelikli Tekdüzen Kaynak Bulucu (URL).</span><span class="sxs-lookup"><span data-stu-id="38a8d-614">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="38a8d-615">Joker karakter bağlama kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-615">Don't use a wildcard binding.</span></span> <span data-ttu-id="38a8d-616">Geçerli bir ana bilgisayar adı veya yerel IP adresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-616">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="38a8d-617">*URL, sondaki eğik çizgi içermelidir.*</span><span class="sxs-lookup"><span data-stu-id="38a8d-617">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="38a8d-618">`<USER>`: Kullanıcı veya Kullanıcı grubu adını belirtir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-618">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="38a8d-619">Aşağıdaki örnekte sunucusunun yerel IP adresi `10.0.0.4` :</span><span class="sxs-lookup"><span data-stu-id="38a8d-619">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="38a8d-620">Bir URL kaydedildiğinde, araç ile yanıt verir `URL reservation successfully added` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-620">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="38a8d-621">Kayıtlı bir URL 'yi silmek için şu `delete urlacl` komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="38a8d-621">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="38a8d-622">X. 509.440 sertifikalarını sunucusuna kaydedin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-622">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="38a8d-623">Uygulamanın sertifikalarını kaydetmek için *netsh.exe* aracını kullanın:</span><span class="sxs-lookup"><span data-stu-id="38a8d-623">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="38a8d-624">`<IP>`: Bağlama için yerel IP adresini belirtir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-624">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="38a8d-625">Joker karakter bağlama kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-625">Don't use a wildcard binding.</span></span> <span data-ttu-id="38a8d-626">Geçerli bir IP adresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-626">Use a valid IP address.</span></span>
   * <span data-ttu-id="38a8d-627">`<PORT>`: Bağlama için bağlantı noktasını belirtir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-627">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="38a8d-628">`<THUMBPRINT>`: X. 509.440 sertifika parmak izi.</span><span class="sxs-lookup"><span data-stu-id="38a8d-628">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="38a8d-629">`<GUID>`: Uygulamayı bilgilendirme amacıyla temsil eden geliştirici tarafından oluşturulan bir GUID.</span><span class="sxs-lookup"><span data-stu-id="38a8d-629">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="38a8d-630">Başvuru amacıyla, GUID 'yi uygulamada bir paket etiketi olarak depolayın:</span><span class="sxs-lookup"><span data-stu-id="38a8d-630">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="38a8d-631">Visual Studio 'da:</span><span class="sxs-lookup"><span data-stu-id="38a8d-631">In Visual Studio:</span></span>
     * <span data-ttu-id="38a8d-632">**Çözüm Gezgini** ' de uygulamaya sağ tıklayıp **Özellikler**' i seçerek uygulamanın proje özelliklerini açın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-632">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="38a8d-633">**Paket** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-633">Select the **Package** tab.</span></span>
     * <span data-ttu-id="38a8d-634">**Etiketler** alanında oluşturduğunuz GUID 'yi girin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-634">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="38a8d-635">Visual Studio kullanmadığınız durumlarda:</span><span class="sxs-lookup"><span data-stu-id="38a8d-635">When not using Visual Studio:</span></span>
     * <span data-ttu-id="38a8d-636">Uygulamanın proje dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-636">Open the app's project file.</span></span>
     * <span data-ttu-id="38a8d-637">`<PackageTags>`Oluşturduğunuz GUID ile yeni veya var olan bir özelliği ekleyin `<PropertyGroup>` :</span><span class="sxs-lookup"><span data-stu-id="38a8d-637">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="38a8d-638">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="38a8d-638">In the following example:</span></span>

   * <span data-ttu-id="38a8d-639">Sunucunun yerel IP adresi `10.0.0.4` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-639">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="38a8d-640">Bir çevrimiçi rastgele GUID Oluşturucu değeri sağlar `appid` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-640">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="38a8d-641">Bir sertifika kaydedildiğinde, araç ile yanıt verir `SSL Certificate successfully added` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-641">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="38a8d-642">Bir sertifika kaydını silmek için şu komutu kullanın `delete sslcert` :</span><span class="sxs-lookup"><span data-stu-id="38a8d-642">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="38a8d-643">*netsh.exe* için başvuru belgeleri:</span><span class="sxs-lookup"><span data-stu-id="38a8d-643">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="38a8d-644">[Köprü Metni Aktarım Protokolü (HTTP) için Netsh komutları](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="38a8d-644">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="38a8d-645">UrlPrefix dizeleri</span><span class="sxs-lookup"><span data-stu-id="38a8d-645">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="38a8d-646">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-646">Run the app.</span></span>

   <span data-ttu-id="38a8d-647">1024 'den büyük bir bağlantı noktası numarası ile HTTP (HTTPS değil) kullanarak localhost 'a bağlanırken uygulamayı çalıştırmak için yönetici ayrıcalıklarına gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-647">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="38a8d-648">Diğer yapılandırmalarda (örneğin, yerel bir IP adresi veya 443 numaralı bağlantı noktasına bağlama), uygulamayı yönetici ayrıcalıklarıyla çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-648">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="38a8d-649">Uygulama, sunucunun genel IP adresinde yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-649">The app responds at the server's public IP address.</span></span> <span data-ttu-id="38a8d-650">Bu örnekte, sunucusuna, genel IP adresinde Internet 'ten ulaşılırsa `104.214.79.47` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-650">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="38a8d-651">Bu örnekte bir geliştirme sertifikası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-651">A development certificate is used in this example.</span></span> <span data-ttu-id="38a8d-652">Tarayıcının güvenilmeyen sertifika uyarısı atlandıktan sonra sayfa güvenli bir şekilde yüklenir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-652">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![Uygulamanın dizin sayfasını yüklü olarak gösteren tarayıcı penceresi](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="38a8d-654">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="38a8d-654">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="38a8d-655">Internet veya şirket ağından gelen isteklerle etkileşime geçen HTTP.sys tarafından barındırılan uygulamalar için, proxy sunucularının ve yük dengeleyiciler arkasında barındırılırken ek yapılandırma gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-655">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="38a8d-656">Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="38a8d-656">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="38a8d-657">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="38a8d-657">Additional resources</span></span>

* [<span data-ttu-id="38a8d-658">HTTP.sysile Windows kimlik doğrulamasını etkinleştir </span><span class="sxs-lookup"><span data-stu-id="38a8d-658">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="38a8d-659">HTTP Sunucusu API 'SI</span><span class="sxs-lookup"><span data-stu-id="38a8d-659">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="38a8d-660">ASPNET/HttpSysServer GitHub deposu (kaynak kodu)</span><span class="sxs-lookup"><span data-stu-id="38a8d-660">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="38a8d-661">Ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="38a8d-661">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="38a8d-662">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) , yalnızca Windows üzerinde çalışan [ASP.NET Core için bir Web sunucusudur](xref:fundamentals/servers/index) .</span><span class="sxs-lookup"><span data-stu-id="38a8d-662">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="38a8d-663">HTTP.sys, [Kestrel](xref:fundamentals/servers/kestrel) Server için bir alternatiftir ve Kestrel tarafından sağlamayan bazı özellikler sunar.</span><span class="sxs-lookup"><span data-stu-id="38a8d-663">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="38a8d-664">HTTP.sys [ASP.NET Core modülüyle](xref:host-and-deploy/aspnet-core-module) uyumlu DEĞILDIR ve ııs veya IIS Express ile kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="38a8d-664">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="38a8d-665">HTTP.sys aşağıdaki özellikleri destekler:</span><span class="sxs-lookup"><span data-stu-id="38a8d-665">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="38a8d-666">Windows Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="38a8d-666">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="38a8d-667">Bağlantı noktası Paylaşımı</span><span class="sxs-lookup"><span data-stu-id="38a8d-667">Port sharing</span></span>
* <span data-ttu-id="38a8d-668">SNı ile HTTPS</span><span class="sxs-lookup"><span data-stu-id="38a8d-668">HTTPS with SNI</span></span>
* <span data-ttu-id="38a8d-669">TLS üzerinden HTTP/2 (Windows 10 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="38a8d-669">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="38a8d-670">Doğrudan dosya iletimi</span><span class="sxs-lookup"><span data-stu-id="38a8d-670">Direct file transmission</span></span>
* <span data-ttu-id="38a8d-671">Yanıtları Önbelleğe Alma</span><span class="sxs-lookup"><span data-stu-id="38a8d-671">Response caching</span></span>
* <span data-ttu-id="38a8d-672">WebSockets (Windows 8 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="38a8d-672">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="38a8d-673">Desteklenen Windows sürümleri:</span><span class="sxs-lookup"><span data-stu-id="38a8d-673">Supported Windows versions:</span></span>

* <span data-ttu-id="38a8d-674">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="38a8d-674">Windows 7 or later</span></span>
* <span data-ttu-id="38a8d-675">Windows Server 2008 R2 veya sonraki sürümü</span><span class="sxs-lookup"><span data-stu-id="38a8d-675">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="38a8d-676">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="38a8d-676">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="38a8d-677">Ne zaman kullanılacağı HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="38a8d-677">When to use HTTP.sys</span></span>

<span data-ttu-id="38a8d-678">HTTP.sys, şu yerlerde olan dağıtımlar için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="38a8d-678">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="38a8d-679">Sunucuyu IIS kullanmadan doğrudan Internet 'e sunmaya gerek vardır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-679">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys doğrudan Internet ile iletişim kurar](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="38a8d-681">İç dağıtım, [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)gibi Kestrel 'de kullanılamayan bir özelliği gerektirir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-681">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys doğrudan iç ağla iletişim kurar](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="38a8d-683">HTTP.sys, birçok saldırı türüne karşı koruyan ve tam özellikli bir Web sunucusu için sağlamlık, güvenlik ve ölçeklenebilirlik sağlayan çok sayıda teknolojiden oluşur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-683">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="38a8d-684">IIS, HTTP.sys en üstünde bir HTTP dinleyicisi olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-684">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="38a8d-685">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="38a8d-685">HTTP/2 support</span></span>

<span data-ttu-id="38a8d-686">Aşağıdaki temel gereksinimler karşılanıyorsa ASP.NET Core uygulamalar için [http/2](https://httpwg.org/specs/rfc7540.html) etkinleştirilir:</span><span class="sxs-lookup"><span data-stu-id="38a8d-686">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="38a8d-687">Windows Server 2016/Windows 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="38a8d-687">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="38a8d-688">[Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı</span><span class="sxs-lookup"><span data-stu-id="38a8d-688">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="38a8d-689">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="38a8d-689">TLS 1.2 or later connection</span></span>

<span data-ttu-id="38a8d-690">Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-690">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="38a8d-691">HTTP/2 varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-691">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="38a8d-692">HTTP/2 bağlantısı kurulmadıysa, bağlantı HTTP/1.1 'ye geri döner.</span><span class="sxs-lookup"><span data-stu-id="38a8d-692">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="38a8d-693">Windows 'un gelecek bir sürümünde http/2 yapılandırma bayrakları, HTTP.sys ile HTTP/2 devre dışı bırakma özelliği de dahil olmak üzere kullanılabilir olacaktır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-693">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="38a8d-694">Kerberos ile çekirdek modu kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="38a8d-694">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="38a8d-695">Kerberos kimlik doğrulama protokolü ile çekirdek modu kimlik doğrulamasına temsilciler HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="38a8d-695">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="38a8d-696">Kullanıcı modu kimlik doğrulaması, Kerberos ve HTTP.sys desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="38a8d-696">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="38a8d-697">Makine hesabı, Active Directory alındığı ve kullanıcının kimliğini doğrulamak için istemci tarafından sunucuya iletilen Kerberos belirtecinin/biletinin şifresini çözmek için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-697">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="38a8d-698">Uygulamanın kullanıcısına değil, ana bilgisayar için hizmet asıl adını (SPN) kaydedin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-698">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="38a8d-699">HTTP.sys kullanma</span><span class="sxs-lookup"><span data-stu-id="38a8d-699">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="38a8d-700">ASP.NET Core uygulamasını kullanacak şekilde yapılandırma HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="38a8d-700">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="38a8d-701">[Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) ([NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) kullanılırken proje dosyasındaki bir paket başvurusu gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-701">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="38a8d-702">`Microsoft.AspNetCore.App`Metapackage 'i kullanmıyorsanız [Microsoft. Aspnetcore. Server. httpsys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)öğesine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-702">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="38a8d-703"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Ana bilgisayarı oluştururken, gerekli herhangi bir yöntemi belirterek uzantı yöntemini çağırın <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> .</span><span class="sxs-lookup"><span data-stu-id="38a8d-703">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="38a8d-704">Aşağıdaki örnek, seçeneklerini varsayılan değerlerine ayarlar:</span><span class="sxs-lookup"><span data-stu-id="38a8d-704">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="38a8d-705">Ek HTTP.sys yapılandırması, [kayıt defteri ayarları](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)aracılığıyla işlenir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-705">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="38a8d-706">**HTTP.sys seçenekleri**</span><span class="sxs-lookup"><span data-stu-id="38a8d-706">**HTTP.sys options**</span></span>

| <span data-ttu-id="38a8d-707">Özellik</span><span class="sxs-lookup"><span data-stu-id="38a8d-707">Property</span></span> | <span data-ttu-id="38a8d-708">Açıklama</span><span class="sxs-lookup"><span data-stu-id="38a8d-708">Description</span></span> | <span data-ttu-id="38a8d-709">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="38a8d-709">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="38a8d-710">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="38a8d-710">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="38a8d-711">Ve için zaman uyumlu giriş/çıkışa izin verilip verilmeyeceğini `HttpContext.Request.Body` denetleyin `HttpContext.Response.Body` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-711">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="38a8d-712">Authentication. AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="38a8d-712">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="38a8d-713">Anonim isteklere izin verin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-713">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="38a8d-714">Authentication. düzenleri</span><span class="sxs-lookup"><span data-stu-id="38a8d-714">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="38a8d-715">İzin verilen kimlik doğrulama düzenlerini belirtin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-715">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="38a8d-716">Dinleyici elden atılırken önce herhangi bir zamanda değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-716">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="38a8d-717">Değerler [authenticationdüzenlerinin numaralandırması](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)tarafından sağlanır: `Basic` ,,, `Kerberos` `Negotiate` `None` , ve `NTLM` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-717">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="38a8d-718">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="38a8d-718">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="38a8d-719">Uygun üst bilgileri içeren yanıtlar için [çekirdek modu](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) önbelleğe almayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-719">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="38a8d-720">Yanıt `Set-Cookie` , `Vary` veya `Pragma` üst bilgileri içermeyebilir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-720">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="38a8d-721">`Cache-Control` `public` Bir ya da değeri ya da bir üst bilgi içermelidir `shared-max-age` `max-age` `Expires` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-721">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="38a8d-722">En fazla eşzamanlı kabul sayısı.</span><span class="sxs-lookup"><span data-stu-id="38a8d-722">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="38a8d-723">5 &times; [ortam. <br> ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="38a8d-723">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="38a8d-724">Kabul edilecek eşzamanlı bağlantı sayısı üst sınırı.</span><span class="sxs-lookup"><span data-stu-id="38a8d-724">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="38a8d-725">`-1`Sonsuz için kullanın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-725">Use `-1` for infinite.</span></span> <span data-ttu-id="38a8d-726">`null`Kayıt defterinin makine genelindeki ayarını kullanmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-726">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="38a8d-727">(makine genelinde</span><span class="sxs-lookup"><span data-stu-id="38a8d-727">(machine-wide</span></span><br><span data-ttu-id="38a8d-728">ayarlanmasını</span><span class="sxs-lookup"><span data-stu-id="38a8d-728">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="38a8d-729"><a href="#maxrequestbodysize">MaxRequestBodySize</a> bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-729">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="38a8d-730">30000000 bayt</span><span class="sxs-lookup"><span data-stu-id="38a8d-730">30000000 bytes</span></span><br><span data-ttu-id="38a8d-731">(~ 28,6 MB)</span><span class="sxs-lookup"><span data-stu-id="38a8d-731">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="38a8d-732">Sıraya alınabilen en fazla istek sayısı.</span><span class="sxs-lookup"><span data-stu-id="38a8d-732">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="38a8d-733">1000</span><span class="sxs-lookup"><span data-stu-id="38a8d-733">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="38a8d-734">İstemci bağlantısının kesilmesinden kaynaklanan yanıt gövdesi yazmasının, özel durumlar oluşturması veya normal şekilde tamamlanması gerektiğini belirtin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-734">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="38a8d-735">(normal olarak)</span><span class="sxs-lookup"><span data-stu-id="38a8d-735">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="38a8d-736"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager>Kayıt defterinde da yapılandırılabilen HTTP.sys yapılandırmasını kullanıma sunun.</span><span class="sxs-lookup"><span data-stu-id="38a8d-736">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="38a8d-737">Varsayılan değerler de dahil olmak üzere her bir ayar hakkında daha fazla bilgi edinmek için API bağlantılarını izleyin:</span><span class="sxs-lookup"><span data-stu-id="38a8d-737">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="38a8d-738">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): http sunucu apı 'sinin Keep-Alive bağlantısında varlık gövdesini boşaltışında izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="38a8d-738">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="38a8d-739">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): istek varlığı gövdesinin gelmesi için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="38a8d-739">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="38a8d-740">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): http sunucusu API 'sinin istek üst bilgisini ayrıştırması için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="38a8d-740">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="38a8d-741">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): boştaki bir bağlantı için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="38a8d-741">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="38a8d-742">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): yanıt için en düşük gönderme hızı.</span><span class="sxs-lookup"><span data-stu-id="38a8d-742">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="38a8d-743">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): isteğin, uygulamanın onu seçmeden önce istek kuyruğunda kalması için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="38a8d-743">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="38a8d-744"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>HTTP.sys kaydolmak için öğesini belirtin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-744">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="38a8d-745">En yararlı olan [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), koleksiyona bir ön ek eklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-745">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="38a8d-746">Bunlar, dinleyici elden atılıyor öncesinde herhangi bir zamanda değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-746">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="38a8d-747">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="38a8d-747">**MaxRequestBodySize**</span></span>

<span data-ttu-id="38a8d-748">Herhangi bir istek gövdesinin bayt olarak izin verilen en büyük boyutu.</span><span class="sxs-lookup"><span data-stu-id="38a8d-748">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="38a8d-749">Olarak ayarlandığında `null` , en büyük istek gövdesi boyutu sınırsızdır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-749">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="38a8d-750">Bu sınırın, her zaman sınırsız olan yükseltilmiş bağlantıları üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-750">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="38a8d-751">Tek bir ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yöntem, `IActionResult` <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="38a8d-751">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="38a8d-752">Uygulama isteği okumayı başlattıktan sonra bir istek üzerindeki sınırı yapılandırmayı denerse bir özel durum oluşur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-752">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="38a8d-753">Özelliğin `IsReadOnly` salt okuma durumunda olup olmadığını göstermek için bir özellik kullanılabilir `MaxRequestBodySize` , yani sınırı yapılandırmak için çok geç.</span><span class="sxs-lookup"><span data-stu-id="38a8d-753">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="38a8d-754">Uygulamanın istek başına geçersiz kılması gerekiyorsa <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> , şunu kullanın <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :</span><span class="sxs-lookup"><span data-stu-id="38a8d-754">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="38a8d-755">Visual Studio kullanıyorsanız, uygulamanın IIS veya IIS Express çalıştıracak şekilde yapılandırılmadığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="38a8d-755">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="38a8d-756">Visual Studio 'da varsayılan başlatma profili IIS Express içindir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-756">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="38a8d-757">Projeyi konsol uygulaması olarak çalıştırmak için, aşağıdaki ekran görüntüsünde gösterildiği gibi seçili profili el ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="38a8d-757">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![Konsol uygulaması profilini seçin](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="38a8d-759">Windows Server 'ı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="38a8d-759">Configure Windows Server</span></span>

1. <span data-ttu-id="38a8d-760">Uygulama için açılacak bağlantı noktalarını belirleme ve [Windows Güvenlik Duvarı](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 'Nı veya [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 'ini kullanarak trafiğin HTTP.sys ulaşmasını sağlamak üzere güvenlik duvarı bağlantı noktalarını açın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-760">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="38a8d-761">Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-761">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="38a8d-762">Bir Azure sanal makinesine dağıtım yaparken, [ağ güvenlik grubundaki](/azure/virtual-machines/windows/nsg-quickstart-portal)bağlantı noktalarını açın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-762">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="38a8d-763">Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-763">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="38a8d-764">Gerekirse, X. 509.440 sertifikalarını edinin ve yükler.</span><span class="sxs-lookup"><span data-stu-id="38a8d-764">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="38a8d-765">Windows 'da, [New-SelfSignedCertificate PowerShell cmdlet 'ini](/powershell/module/pkiclient/new-selfsignedcertificate)kullanarak otomatik olarak imzalanan sertifikalar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="38a8d-765">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="38a8d-766">Desteklenmeyen bir örnek için bkz. [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span><span class="sxs-lookup"><span data-stu-id="38a8d-766">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="38a8d-767">Sunucunun **yerel makine** > **Kişisel** deposunda otomatik olarak imzalanan veya CA imzalı sertifikalar yükler.</span><span class="sxs-lookup"><span data-stu-id="38a8d-767">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="38a8d-768">Uygulama [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise, .net core, .NET Framework veya her ikisini de (uygulama .NET Framework hedefleyen bir .NET Core uygulaması ise) yükler.</span><span class="sxs-lookup"><span data-stu-id="38a8d-768">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="38a8d-769">**.NET Core**: uygulama .NET Core gerektiriyorsa .NET Core [Indirmelerinde](https://dotnet.microsoft.com/download) **.NET Core çalışma zamanı** yükleyicisini edinip çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-769">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="38a8d-770">Tam SDK 'Yı sunucuya yüklemeyin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-770">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="38a8d-771">**.NET Framework**: uygulama .NET Framework gerektiriyorsa [.NET Framework yükleme kılavuzuna](/dotnet/framework/install/)bakın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-771">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="38a8d-772">Gerekli .NET Framework yüklemesi.</span><span class="sxs-lookup"><span data-stu-id="38a8d-772">Install the required .NET Framework.</span></span> <span data-ttu-id="38a8d-773">En son .NET Framework yükleyicisi [.NET Core İndirmeleri](https://dotnet.microsoft.com/download) sayfasından edinilebilir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-773">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="38a8d-774">Uygulama, [kendi içinde bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise, uygulama çalışma zamanını dağıtımda içerir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-774">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="38a8d-775">Sunucuda çerçeve yüklemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="38a8d-775">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="38a8d-776">Uygulamada URL 'Leri ve bağlantı noktalarını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-776">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="38a8d-777">Varsayılan olarak, ASP.NET Core öğesine bağlanır `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-777">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="38a8d-778">URL öneklerini ve bağlantı noktalarını yapılandırmak için seçenekler şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="38a8d-778">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="38a8d-779">`urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="38a8d-779">`urls` command-line argument</span></span>
   * <span data-ttu-id="38a8d-780">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="38a8d-780">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="38a8d-781">Aşağıdaki kod örneği, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> `10.0.0.4` 443 numaralı bağlantı noktasında sunucunun yerel IP adresi ile nasıl kullanılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="38a8d-781">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="38a8d-782">Avantajı `UrlPrefixes` , hatalı biçimli ön ekler için hemen bir hata iletisi oluşturulmasından oluşur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-782">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="38a8d-783">Ayarları `UrlPrefixes` geçersiz kıl `UseUrls` / `urls` / `ASPNETCORE_URLS` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-783">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="38a8d-784">Bu nedenle, `UseUrls` `urls` ve `ASPNETCORE_URLS` ortam değişkeninin avantajı Kestrel ve HTTP.sys arasında geçiş yapmak daha kolay bir hale gelir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-784">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="38a8d-785">HTTP.sys, [http sunucusu API UrlPrefix dize biçimlerini](/windows/win32/http/urlprefix-strings)kullanır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-785">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="38a8d-786">Üst düzey joker karakter bağlamaları ( `http://*:80/` ve `http://+:80` ) kullanılmamalıdır  .</span><span class="sxs-lookup"><span data-stu-id="38a8d-786">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="38a8d-787">Üst düzey joker karakter bağlamaları uygulama güvenliği güvenlik açıklarını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-787">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="38a8d-788">Bu hem güçlü hem de zayıf Joker karakterlere yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-788">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="38a8d-789">Joker karakterler yerine açık konak adlarını veya IP adreslerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-789">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="38a8d-790">Alt etki alanı joker bağlantısı (örneğin, `*.mysub.com` ), tüm üst etki alanını ( `*.com` Bu güvenlik açığı olan aksine) kontrol ediyorsanız bir güvenlik riski değildir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-790">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="38a8d-791">Daha fazla bilgi için bkz. [RFC 7230: bölüm 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="38a8d-791">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="38a8d-792">Ön ek, sunucudaki URL öneklerini ister.</span><span class="sxs-lookup"><span data-stu-id="38a8d-792">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="38a8d-793">HTTP.sys yapılandırmaya yönelik yerleşik araç *netsh.exe*.</span><span class="sxs-lookup"><span data-stu-id="38a8d-793">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="38a8d-794">*netsh.exe* , URL öneklerini ayırmak ve X. 509.440 sertifikaları atamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-794">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="38a8d-795">Araç, yönetici ayrıcalıkları gerektirir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-795">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="38a8d-796">Uygulamanın URL 'Lerini kaydetmek için *netsh.exe* aracını kullanın:</span><span class="sxs-lookup"><span data-stu-id="38a8d-796">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="38a8d-797">`<URL>`: Tam nitelikli Tekdüzen Kaynak Bulucu (URL).</span><span class="sxs-lookup"><span data-stu-id="38a8d-797">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="38a8d-798">Joker karakter bağlama kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-798">Don't use a wildcard binding.</span></span> <span data-ttu-id="38a8d-799">Geçerli bir ana bilgisayar adı veya yerel IP adresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-799">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="38a8d-800">*URL, sondaki eğik çizgi içermelidir.*</span><span class="sxs-lookup"><span data-stu-id="38a8d-800">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="38a8d-801">`<USER>`: Kullanıcı veya Kullanıcı grubu adını belirtir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-801">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="38a8d-802">Aşağıdaki örnekte sunucusunun yerel IP adresi `10.0.0.4` :</span><span class="sxs-lookup"><span data-stu-id="38a8d-802">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="38a8d-803">Bir URL kaydedildiğinde, araç ile yanıt verir `URL reservation successfully added` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-803">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="38a8d-804">Kayıtlı bir URL 'yi silmek için şu `delete urlacl` komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="38a8d-804">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="38a8d-805">X. 509.440 sertifikalarını sunucusuna kaydedin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-805">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="38a8d-806">Uygulamanın sertifikalarını kaydetmek için *netsh.exe* aracını kullanın:</span><span class="sxs-lookup"><span data-stu-id="38a8d-806">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="38a8d-807">`<IP>`: Bağlama için yerel IP adresini belirtir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-807">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="38a8d-808">Joker karakter bağlama kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-808">Don't use a wildcard binding.</span></span> <span data-ttu-id="38a8d-809">Geçerli bir IP adresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-809">Use a valid IP address.</span></span>
   * <span data-ttu-id="38a8d-810">`<PORT>`: Bağlama için bağlantı noktasını belirtir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-810">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="38a8d-811">`<THUMBPRINT>`: X. 509.440 sertifika parmak izi.</span><span class="sxs-lookup"><span data-stu-id="38a8d-811">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="38a8d-812">`<GUID>`: Uygulamayı bilgilendirme amacıyla temsil eden geliştirici tarafından oluşturulan bir GUID.</span><span class="sxs-lookup"><span data-stu-id="38a8d-812">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="38a8d-813">Başvuru amacıyla, GUID 'yi uygulamada bir paket etiketi olarak depolayın:</span><span class="sxs-lookup"><span data-stu-id="38a8d-813">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="38a8d-814">Visual Studio 'da:</span><span class="sxs-lookup"><span data-stu-id="38a8d-814">In Visual Studio:</span></span>
     * <span data-ttu-id="38a8d-815">**Çözüm Gezgini** ' de uygulamaya sağ tıklayıp **Özellikler**' i seçerek uygulamanın proje özelliklerini açın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-815">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="38a8d-816">**Paket** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-816">Select the **Package** tab.</span></span>
     * <span data-ttu-id="38a8d-817">**Etiketler** alanında oluşturduğunuz GUID 'yi girin.</span><span class="sxs-lookup"><span data-stu-id="38a8d-817">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="38a8d-818">Visual Studio kullanmadığınız durumlarda:</span><span class="sxs-lookup"><span data-stu-id="38a8d-818">When not using Visual Studio:</span></span>
     * <span data-ttu-id="38a8d-819">Uygulamanın proje dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-819">Open the app's project file.</span></span>
     * <span data-ttu-id="38a8d-820">`<PackageTags>`Oluşturduğunuz GUID ile yeni veya var olan bir özelliği ekleyin `<PropertyGroup>` :</span><span class="sxs-lookup"><span data-stu-id="38a8d-820">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="38a8d-821">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="38a8d-821">In the following example:</span></span>

   * <span data-ttu-id="38a8d-822">Sunucunun yerel IP adresi `10.0.0.4` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-822">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="38a8d-823">Bir çevrimiçi rastgele GUID Oluşturucu değeri sağlar `appid` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-823">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="38a8d-824">Bir sertifika kaydedildiğinde, araç ile yanıt verir `SSL Certificate successfully added` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-824">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="38a8d-825">Bir sertifika kaydını silmek için şu komutu kullanın `delete sslcert` :</span><span class="sxs-lookup"><span data-stu-id="38a8d-825">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="38a8d-826">*netsh.exe* için başvuru belgeleri:</span><span class="sxs-lookup"><span data-stu-id="38a8d-826">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="38a8d-827">[Köprü Metni Aktarım Protokolü (HTTP) için Netsh komutları](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="38a8d-827">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="38a8d-828">UrlPrefix dizeleri</span><span class="sxs-lookup"><span data-stu-id="38a8d-828">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="38a8d-829">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-829">Run the app.</span></span>

   <span data-ttu-id="38a8d-830">1024 'den büyük bir bağlantı noktası numarası ile HTTP (HTTPS değil) kullanarak localhost 'a bağlanırken uygulamayı çalıştırmak için yönetici ayrıcalıklarına gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="38a8d-830">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="38a8d-831">Diğer yapılandırmalarda (örneğin, yerel bir IP adresi veya 443 numaralı bağlantı noktasına bağlama), uygulamayı yönetici ayrıcalıklarıyla çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="38a8d-831">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="38a8d-832">Uygulama, sunucunun genel IP adresinde yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-832">The app responds at the server's public IP address.</span></span> <span data-ttu-id="38a8d-833">Bu örnekte, sunucusuna, genel IP adresinde Internet 'ten ulaşılırsa `104.214.79.47` .</span><span class="sxs-lookup"><span data-stu-id="38a8d-833">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="38a8d-834">Bu örnekte bir geliştirme sertifikası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="38a8d-834">A development certificate is used in this example.</span></span> <span data-ttu-id="38a8d-835">Tarayıcının güvenilmeyen sertifika uyarısı atlandıktan sonra sayfa güvenli bir şekilde yüklenir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-835">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![Uygulamanın dizin sayfasını yüklü olarak gösteren tarayıcı penceresi](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="38a8d-837">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="38a8d-837">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="38a8d-838">Internet veya şirket ağından gelen isteklerle etkileşime geçen HTTP.sys tarafından barındırılan uygulamalar için, proxy sunucularının ve yük dengeleyiciler arkasında barındırılırken ek yapılandırma gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="38a8d-838">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="38a8d-839">Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="38a8d-839">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="38a8d-840">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="38a8d-840">Additional resources</span></span>

* [<span data-ttu-id="38a8d-841">HTTP.sysile Windows kimlik doğrulamasını etkinleştir </span><span class="sxs-lookup"><span data-stu-id="38a8d-841">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="38a8d-842">HTTP Sunucusu API 'SI</span><span class="sxs-lookup"><span data-stu-id="38a8d-842">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="38a8d-843">ASPNET/HttpSysServer GitHub deposu (kaynak kodu)</span><span class="sxs-lookup"><span data-stu-id="38a8d-843">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="38a8d-844">Ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="38a8d-844">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
