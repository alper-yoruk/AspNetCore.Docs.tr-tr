---
title: ASP.NET Core Web sunucusu uygulamasını HTTP.sys
author: rick-anderson
description: Windows üzerinde ASP.NET Core için bir Web sunucusu olan HTTP.sys hakkında bilgi edinin. HTTP.sys çekirdek modu sürücüsü üzerine inşa edilen HTTP.sys, IIS olmadan Internet 'e doğrudan bağlanmak için kullanılabilen Kestrel için bir alternatiftir.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: fundamentals/servers/httpsys
ms.openlocfilehash: 8ed9ec3447205107194ffa5c329c0e5ae0fc5553
ms.sourcegitcommit: e519d95d17443abafba8f712ac168347b15c8b57
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653977"
---
# <a name="httpsys-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="59c4f-104">ASP.NET Core Web sunucusu uygulamasını HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="59c4f-104">HTTP.sys web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="59c4f-105">[Tom Dykstra](https://github.com/tdykstra) ve [Chris](https://github.com/Tratcher) 'e göre</span><span class="sxs-lookup"><span data-stu-id="59c4f-105">By [Tom Dykstra](https://github.com/tdykstra) and [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="59c4f-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) , yalnızca Windows üzerinde çalışan [ASP.NET Core için bir Web sunucusudur](xref:fundamentals/servers/index) .</span><span class="sxs-lookup"><span data-stu-id="59c4f-106">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="59c4f-107">HTTP.sys, [Kestrel](xref:fundamentals/servers/kestrel) Server için bir alternatiftir ve Kestrel tarafından sağlamayan bazı özellikler sunar.</span><span class="sxs-lookup"><span data-stu-id="59c4f-107">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="59c4f-108">HTTP.sys [ASP.NET Core modülüyle](xref:host-and-deploy/aspnet-core-module) uyumlu DEĞILDIR ve ııs veya IIS Express ile kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="59c4f-108">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="59c4f-109">HTTP.sys aşağıdaki özellikleri destekler:</span><span class="sxs-lookup"><span data-stu-id="59c4f-109">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="59c4f-110">Windows Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="59c4f-110">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="59c4f-111">Bağlantı noktası Paylaşımı</span><span class="sxs-lookup"><span data-stu-id="59c4f-111">Port sharing</span></span>
* <span data-ttu-id="59c4f-112">SNı ile HTTPS</span><span class="sxs-lookup"><span data-stu-id="59c4f-112">HTTPS with SNI</span></span>
* <span data-ttu-id="59c4f-113">TLS üzerinden HTTP/2 (Windows 10 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="59c4f-113">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="59c4f-114">Doğrudan dosya iletimi</span><span class="sxs-lookup"><span data-stu-id="59c4f-114">Direct file transmission</span></span>
* <span data-ttu-id="59c4f-115">Yanıtları Önbelleğe Alma</span><span class="sxs-lookup"><span data-stu-id="59c4f-115">Response caching</span></span>
* <span data-ttu-id="59c4f-116">WebSockets (Windows 8 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="59c4f-116">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="59c4f-117">Desteklenen Windows sürümleri:</span><span class="sxs-lookup"><span data-stu-id="59c4f-117">Supported Windows versions:</span></span>

* <span data-ttu-id="59c4f-118">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="59c4f-118">Windows 7 or later</span></span>
* <span data-ttu-id="59c4f-119">Windows Server 2008 R2 veya sonraki sürümü</span><span class="sxs-lookup"><span data-stu-id="59c4f-119">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="59c4f-120">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="59c4f-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="59c4f-121">Ne zaman kullanılacağı HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="59c4f-121">When to use HTTP.sys</span></span>

<span data-ttu-id="59c4f-122">HTTP.sys, şu yerlerde olan dağıtımlar için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="59c4f-122">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="59c4f-123">Sunucuyu IIS kullanmadan doğrudan Internet 'e sunmaya gerek vardır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-123">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys doğrudan Internet ile iletişim kurar](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="59c4f-125">İç dağıtım, [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)gibi Kestrel 'de kullanılamayan bir özelliği gerektirir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-125">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys doğrudan iç ağla iletişim kurar](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="59c4f-127">HTTP.sys, birçok saldırı türüne karşı koruyan ve tam özellikli bir Web sunucusu için sağlamlık, güvenlik ve ölçeklenebilirlik sağlayan çok sayıda teknolojiden oluşur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-127">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="59c4f-128">IIS, HTTP.sys en üstünde bir HTTP dinleyicisi olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-128">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="59c4f-129">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="59c4f-129">HTTP/2 support</span></span>

<span data-ttu-id="59c4f-130">Aşağıdaki temel gereksinimler karşılanıyorsa ASP.NET Core uygulamalar için [http/2](https://httpwg.org/specs/rfc7540.html) etkinleştirilir:</span><span class="sxs-lookup"><span data-stu-id="59c4f-130">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="59c4f-131">Windows Server 2016/Windows 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="59c4f-131">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="59c4f-132">[Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı</span><span class="sxs-lookup"><span data-stu-id="59c4f-132">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="59c4f-133">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="59c4f-133">TLS 1.2 or later connection</span></span>

<span data-ttu-id="59c4f-134">Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-134">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="59c4f-135">HTTP/2 varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-135">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="59c4f-136">HTTP/2 bağlantısı kurulmadıysa, bağlantı HTTP/1.1 'ye geri döner.</span><span class="sxs-lookup"><span data-stu-id="59c4f-136">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="59c4f-137">Windows 'un gelecek bir sürümünde http/2 yapılandırma bayrakları, HTTP.sys ile HTTP/2 devre dışı bırakma özelliği de dahil olmak üzere kullanılabilir olacaktır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-137">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="59c4f-138">Kerberos ile çekirdek modu kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="59c4f-138">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="59c4f-139">Kerberos kimlik doğrulama protokolü ile çekirdek modu kimlik doğrulamasına temsilciler HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="59c4f-139">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="59c4f-140">Kullanıcı modu kimlik doğrulaması, Kerberos ve HTTP.sys desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="59c4f-140">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="59c4f-141">Makine hesabı, Active Directory alındığı ve kullanıcının kimliğini doğrulamak için istemci tarafından sunucuya iletilen Kerberos belirtecinin/biletinin şifresini çözmek için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-141">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="59c4f-142">Uygulamanın kullanıcısına değil, ana bilgisayar için hizmet asıl adını (SPN) kaydedin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-142">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="59c4f-143">HTTP.sys kullanma</span><span class="sxs-lookup"><span data-stu-id="59c4f-143">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="59c4f-144">ASP.NET Core uygulamasını kullanacak şekilde yapılandırma HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="59c4f-144">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="59c4f-145"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Ana bilgisayarı oluştururken, gerekli herhangi bir yöntemi belirterek uzantı yöntemini çağırın <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> .</span><span class="sxs-lookup"><span data-stu-id="59c4f-145">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="59c4f-146">Aşağıdaki örnek, seçeneklerini varsayılan değerlerine ayarlar:</span><span class="sxs-lookup"><span data-stu-id="59c4f-146">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="59c4f-147">Ek HTTP.sys yapılandırması, [kayıt defteri ayarları](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)aracılığıyla işlenir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-147">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="59c4f-148">**HTTP.sys seçenekleri**</span><span class="sxs-lookup"><span data-stu-id="59c4f-148">**HTTP.sys options**</span></span>

| <span data-ttu-id="59c4f-149">Özellik</span><span class="sxs-lookup"><span data-stu-id="59c4f-149">Property</span></span> | <span data-ttu-id="59c4f-150">Açıklama</span><span class="sxs-lookup"><span data-stu-id="59c4f-150">Description</span></span> | <span data-ttu-id="59c4f-151">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="59c4f-151">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="59c4f-152">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="59c4f-152">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="59c4f-153">Ve için zaman uyumlu giriş/çıkışa izin verilip verilmeyeceğini `HttpContext.Request.Body` denetleyin `HttpContext.Response.Body` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-153">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="59c4f-154">Authentication. AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="59c4f-154">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="59c4f-155">Anonim isteklere izin verin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-155">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="59c4f-156">Authentication. düzenleri</span><span class="sxs-lookup"><span data-stu-id="59c4f-156">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="59c4f-157">İzin verilen kimlik doğrulama düzenlerini belirtin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-157">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="59c4f-158">Dinleyici elden atılırken önce herhangi bir zamanda değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-158">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="59c4f-159">Değerler [authenticationdüzenlerinin numaralandırması](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)tarafından sağlanır: `Basic` ,,, `Kerberos` `Negotiate` `None` , ve `NTLM` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-159">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="59c4f-160">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="59c4f-160">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="59c4f-161">Uygun üst bilgileri içeren yanıtlar için [çekirdek modu](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) önbelleğe almayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-161">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="59c4f-162">Yanıt `Set-Cookie` , `Vary` veya `Pragma` üst bilgileri içermeyebilir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-162">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="59c4f-163">`Cache-Control` `public` Bir ya da değeri ya da bir üst bilgi içermelidir `shared-max-age` `max-age` `Expires` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-163">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="59c4f-164">En fazla eşzamanlı kabul sayısı.</span><span class="sxs-lookup"><span data-stu-id="59c4f-164">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="59c4f-165">5 &times; [ortam. <br> ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="59c4f-165">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="59c4f-166">Kabul edilecek eşzamanlı bağlantı sayısı üst sınırı.</span><span class="sxs-lookup"><span data-stu-id="59c4f-166">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="59c4f-167">`-1`Sonsuz için kullanın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-167">Use `-1` for infinite.</span></span> <span data-ttu-id="59c4f-168">`null`Kayıt defterinin makine genelindeki ayarını kullanmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-168">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="59c4f-169">(makine genelinde</span><span class="sxs-lookup"><span data-stu-id="59c4f-169">(machine-wide</span></span><br><span data-ttu-id="59c4f-170">ayarlanmasını</span><span class="sxs-lookup"><span data-stu-id="59c4f-170">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="59c4f-171"><a href="#maxrequestbodysize">MaxRequestBodySize</a> bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-171">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="59c4f-172">30000000 bayt</span><span class="sxs-lookup"><span data-stu-id="59c4f-172">30000000 bytes</span></span><br><span data-ttu-id="59c4f-173">(~ 28,6 MB)</span><span class="sxs-lookup"><span data-stu-id="59c4f-173">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="59c4f-174">Sıraya alınabilen en fazla istek sayısı.</span><span class="sxs-lookup"><span data-stu-id="59c4f-174">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="59c4f-175">1000</span><span class="sxs-lookup"><span data-stu-id="59c4f-175">1000</span></span> |
| `RequestQueueMode` | <span data-ttu-id="59c4f-176">Bu, sunucunun istek kuyruğunu oluşturma ve yapılandırmadan sorumlu olup olmadığını veya mevcut bir kuyruğa iliştirilmesinin gerekip gerekmediğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-176">This indicates whether the server is responsible for creating and configuring the request queue, or if it should attach to an existing queue.</span></span><br><span data-ttu-id="59c4f-177">Mevcut bir kuyruğa eklenirken, mevcut yapılandırma seçeneklerinin çoğu geçerli değildir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-177">Most existing configuration options do not apply when attaching to an existing queue.</span></span> | `RequestQueueMode.Create` |
| `RequestQueueName` | <span data-ttu-id="59c4f-178">HTTP.sys istek kuyruğunun adı.</span><span class="sxs-lookup"><span data-stu-id="59c4f-178">The name of the HTTP.sys request queue.</span></span> | <span data-ttu-id="59c4f-179">`null` (Anonim kuyruk)</span><span class="sxs-lookup"><span data-stu-id="59c4f-179">`null` (Anonymous queue)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="59c4f-180">İstemci bağlantısının kesilmesinden kaynaklanan yanıt gövdesi yazmasının, özel durumlar oluşturması veya normal şekilde tamamlanması gerektiğini belirtin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-180">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="59c4f-181">(normal olarak)</span><span class="sxs-lookup"><span data-stu-id="59c4f-181">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="59c4f-182"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager>Kayıt defterinde da yapılandırılabilen HTTP.sys yapılandırmasını kullanıma sunun.</span><span class="sxs-lookup"><span data-stu-id="59c4f-182">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="59c4f-183">Varsayılan değerler de dahil olmak üzere her bir ayar hakkında daha fazla bilgi edinmek için API bağlantılarını izleyin:</span><span class="sxs-lookup"><span data-stu-id="59c4f-183">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="59c4f-184">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): http sunucusu API 'sinin varlık gövdesini etkin tut bağlantısı üzerinde boşaltmasına izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="59c4f-184">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="59c4f-185">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): istek varlığı gövdesinin gelmesi için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="59c4f-185">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="59c4f-186">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): http sunucusu API 'sinin istek üst bilgisini ayrıştırması için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="59c4f-186">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="59c4f-187">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): boştaki bir bağlantı için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="59c4f-187">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="59c4f-188">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): yanıt için en düşük gönderme hızı.</span><span class="sxs-lookup"><span data-stu-id="59c4f-188">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="59c4f-189">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): isteğin, uygulamanın onu seçmeden önce istek kuyruğunda kalması için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="59c4f-189">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="59c4f-190"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>HTTP.sys kaydolmak için öğesini belirtin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-190">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="59c4f-191">En yararlı olan [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), koleksiyona bir ön ek eklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-191">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="59c4f-192">Bunlar, dinleyici elden atılıyor öncesinde herhangi bir zamanda değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-192">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="59c4f-193">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="59c4f-193">**MaxRequestBodySize**</span></span>

<span data-ttu-id="59c4f-194">Herhangi bir istek gövdesinin bayt olarak izin verilen en büyük boyutu.</span><span class="sxs-lookup"><span data-stu-id="59c4f-194">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="59c4f-195">Olarak ayarlandığında `null` , en büyük istek gövdesi boyutu sınırsızdır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-195">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="59c4f-196">Bu sınırın, her zaman sınırsız olan yükseltilmiş bağlantıları üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-196">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="59c4f-197">Tek bir ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yöntem, `IActionResult` <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="59c4f-197">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="59c4f-198">Uygulama isteği okumayı başlattıktan sonra bir istek üzerindeki sınırı yapılandırmayı denerse bir özel durum oluşur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-198">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="59c4f-199">Özelliğin `IsReadOnly` salt okuma durumunda olup olmadığını göstermek için bir özellik kullanılabilir `MaxRequestBodySize` , yani sınırı yapılandırmak için çok geç.</span><span class="sxs-lookup"><span data-stu-id="59c4f-199">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="59c4f-200">Uygulamanın istek başına geçersiz kılması gerekiyorsa <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> , şunu kullanın <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :</span><span class="sxs-lookup"><span data-stu-id="59c4f-200">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="59c4f-201">Visual Studio kullanıyorsanız, uygulamanın IIS veya IIS Express çalıştıracak şekilde yapılandırılmadığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="59c4f-201">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="59c4f-202">Visual Studio 'da varsayılan başlatma profili IIS Express içindir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-202">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="59c4f-203">Projeyi konsol uygulaması olarak çalıştırmak için, aşağıdaki ekran görüntüsünde gösterildiği gibi seçili profili el ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="59c4f-203">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![Konsol uygulaması profilini seçin](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="59c4f-205">Windows Server 'ı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="59c4f-205">Configure Windows Server</span></span>

1. <span data-ttu-id="59c4f-206">Uygulama için açılacak bağlantı noktalarını belirleme ve [Windows Güvenlik Duvarı](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 'Nı veya [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 'ini kullanarak trafiğin HTTP.sys ulaşmasını sağlamak üzere güvenlik duvarı bağlantı noktalarını açın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-206">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="59c4f-207">Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-207">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="59c4f-208">Bir Azure sanal makinesine dağıtım yaparken, [ağ güvenlik grubundaki](/azure/virtual-machines/windows/nsg-quickstart-portal)bağlantı noktalarını açın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-208">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="59c4f-209">Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-209">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="59c4f-210">Gerekirse, X. 509.440 sertifikalarını edinin ve yükler.</span><span class="sxs-lookup"><span data-stu-id="59c4f-210">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="59c4f-211">Windows 'da, [New-SelfSignedCertificate PowerShell cmdlet 'ini](/powershell/module/pkiclient/new-selfsignedcertificate)kullanarak otomatik olarak imzalanan sertifikalar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="59c4f-211">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="59c4f-212">Desteklenmeyen bir örnek için bkz. [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span><span class="sxs-lookup"><span data-stu-id="59c4f-212">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="59c4f-213">Sunucunun **yerel makine** > **Kişisel** deposunda otomatik olarak imzalanan veya CA imzalı sertifikalar yükler.</span><span class="sxs-lookup"><span data-stu-id="59c4f-213">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="59c4f-214">Uygulama [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise, .net core, .NET Framework veya her ikisini de (uygulama .NET Framework hedefleyen bir .NET Core uygulaması ise) yükler.</span><span class="sxs-lookup"><span data-stu-id="59c4f-214">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="59c4f-215">**.NET Core**: uygulama .NET Core gerektiriyorsa .NET Core [Indirmelerinde](https://dotnet.microsoft.com/download) **.NET Core çalışma zamanı** yükleyicisini edinip çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-215">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="59c4f-216">Tam SDK 'Yı sunucuya yüklemeyin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-216">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="59c4f-217">**.NET Framework**: uygulama .NET Framework gerektiriyorsa [.NET Framework yükleme kılavuzuna](/dotnet/framework/install/)bakın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-217">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="59c4f-218">Gerekli .NET Framework yüklemesi.</span><span class="sxs-lookup"><span data-stu-id="59c4f-218">Install the required .NET Framework.</span></span> <span data-ttu-id="59c4f-219">En son .NET Framework yükleyicisi [.NET Core İndirmeleri](https://dotnet.microsoft.com/download) sayfasından edinilebilir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-219">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="59c4f-220">Uygulama, [kendi içinde bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise, uygulama çalışma zamanını dağıtımda içerir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-220">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="59c4f-221">Sunucuda çerçeve yüklemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="59c4f-221">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="59c4f-222">Uygulamada URL 'Leri ve bağlantı noktalarını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-222">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="59c4f-223">Varsayılan olarak, ASP.NET Core öğesine bağlanır `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-223">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="59c4f-224">URL öneklerini ve bağlantı noktalarını yapılandırmak için seçenekler şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="59c4f-224">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="59c4f-225">`urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="59c4f-225">`urls` command-line argument</span></span>
   * <span data-ttu-id="59c4f-226">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="59c4f-226">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="59c4f-227">Aşağıdaki kod örneği, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> `10.0.0.4` 443 numaralı bağlantı noktasında sunucunun yerel IP adresi ile nasıl kullanılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="59c4f-227">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="59c4f-228">Avantajı `UrlPrefixes` , hatalı biçimli ön ekler için hemen bir hata iletisi oluşturulmasından oluşur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-228">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="59c4f-229">Ayarları `UrlPrefixes` geçersiz kıl `UseUrls` / `urls` / `ASPNETCORE_URLS` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-229">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="59c4f-230">Bu nedenle, `UseUrls` `urls` ve `ASPNETCORE_URLS` ortam değişkeninin avantajı Kestrel ve HTTP.sys arasında geçiş yapmak daha kolay bir hale gelir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-230">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="59c4f-231">HTTP.sys, [http sunucusu API UrlPrefix dize biçimlerini](/windows/win32/http/urlprefix-strings)kullanır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-231">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="59c4f-232">Üst düzey joker karakter bağlamaları ( `http://*:80/` ve `http://+:80` ) kullanılmamalıdır **not** .</span><span class="sxs-lookup"><span data-stu-id="59c4f-232">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="59c4f-233">Üst düzey joker karakter bağlamaları uygulama güvenliği güvenlik açıklarını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-233">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="59c4f-234">Bu hem güçlü hem de zayıf Joker karakterlere yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-234">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="59c4f-235">Joker karakterler yerine açık konak adlarını veya IP adreslerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-235">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="59c4f-236">Alt etki alanı joker bağlantısı (örneğin, `*.mysub.com` ), tüm üst etki alanını ( `*.com` Bu güvenlik açığı olan aksine) kontrol ediyorsanız bir güvenlik riski değildir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-236">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="59c4f-237">Daha fazla bilgi için bkz. [RFC 7230: bölüm 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="59c4f-237">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="59c4f-238">Ön ek, sunucudaki URL öneklerini ister.</span><span class="sxs-lookup"><span data-stu-id="59c4f-238">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="59c4f-239">HTTP.sys yapılandırmaya yönelik yerleşik araç *netsh.exe*.</span><span class="sxs-lookup"><span data-stu-id="59c4f-239">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="59c4f-240">*netsh.exe* , URL öneklerini ayırmak ve X. 509.440 sertifikaları atamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-240">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="59c4f-241">Araç, yönetici ayrıcalıkları gerektirir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-241">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="59c4f-242">Uygulamanın URL 'Lerini kaydetmek için *netsh.exe* aracını kullanın:</span><span class="sxs-lookup"><span data-stu-id="59c4f-242">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="59c4f-243">`<URL>`: Tam nitelikli Tekdüzen Kaynak Bulucu (URL).</span><span class="sxs-lookup"><span data-stu-id="59c4f-243">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="59c4f-244">Joker karakter bağlama kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-244">Don't use a wildcard binding.</span></span> <span data-ttu-id="59c4f-245">Geçerli bir ana bilgisayar adı veya yerel IP adresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-245">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="59c4f-246">*URL, sondaki eğik çizgi içermelidir.*</span><span class="sxs-lookup"><span data-stu-id="59c4f-246">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="59c4f-247">`<USER>`: Kullanıcı veya Kullanıcı grubu adını belirtir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-247">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="59c4f-248">Aşağıdaki örnekte sunucusunun yerel IP adresi `10.0.0.4` :</span><span class="sxs-lookup"><span data-stu-id="59c4f-248">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="59c4f-249">Bir URL kaydedildiğinde, araç ile yanıt verir `URL reservation successfully added` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-249">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="59c4f-250">Kayıtlı bir URL 'yi silmek için şu `delete urlacl` komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="59c4f-250">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="59c4f-251">X. 509.440 sertifikalarını sunucusuna kaydedin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-251">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="59c4f-252">Uygulamanın sertifikalarını kaydetmek için *netsh.exe* aracını kullanın:</span><span class="sxs-lookup"><span data-stu-id="59c4f-252">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="59c4f-253">`<IP>`: Bağlama için yerel IP adresini belirtir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-253">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="59c4f-254">Joker karakter bağlama kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-254">Don't use a wildcard binding.</span></span> <span data-ttu-id="59c4f-255">Geçerli bir IP adresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-255">Use a valid IP address.</span></span>
   * <span data-ttu-id="59c4f-256">`<PORT>`: Bağlama için bağlantı noktasını belirtir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-256">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="59c4f-257">`<THUMBPRINT>`: X. 509.440 sertifika parmak izi.</span><span class="sxs-lookup"><span data-stu-id="59c4f-257">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="59c4f-258">`<GUID>`: Uygulamayı bilgilendirme amacıyla temsil eden geliştirici tarafından oluşturulan bir GUID.</span><span class="sxs-lookup"><span data-stu-id="59c4f-258">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="59c4f-259">Başvuru amacıyla, GUID 'yi uygulamada bir paket etiketi olarak depolayın:</span><span class="sxs-lookup"><span data-stu-id="59c4f-259">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="59c4f-260">Visual Studio 'da:</span><span class="sxs-lookup"><span data-stu-id="59c4f-260">In Visual Studio:</span></span>
     * <span data-ttu-id="59c4f-261">**Çözüm Gezgini** ' de uygulamaya sağ tıklayıp **Özellikler**' i seçerek uygulamanın proje özelliklerini açın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-261">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="59c4f-262">**Paket** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-262">Select the **Package** tab.</span></span>
     * <span data-ttu-id="59c4f-263">**Etiketler** alanında oluşturduğunuz GUID 'yi girin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-263">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="59c4f-264">Visual Studio kullanmadığınız durumlarda:</span><span class="sxs-lookup"><span data-stu-id="59c4f-264">When not using Visual Studio:</span></span>
     * <span data-ttu-id="59c4f-265">Uygulamanın proje dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-265">Open the app's project file.</span></span>
     * <span data-ttu-id="59c4f-266">`<PackageTags>`Oluşturduğunuz GUID ile yeni veya var olan bir özelliği ekleyin `<PropertyGroup>` :</span><span class="sxs-lookup"><span data-stu-id="59c4f-266">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="59c4f-267">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="59c4f-267">In the following example:</span></span>

   * <span data-ttu-id="59c4f-268">Sunucunun yerel IP adresi `10.0.0.4` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-268">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="59c4f-269">Bir çevrimiçi rastgele GUID Oluşturucu değeri sağlar `appid` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-269">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="59c4f-270">Bir sertifika kaydedildiğinde, araç ile yanıt verir `SSL Certificate successfully added` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-270">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="59c4f-271">Bir sertifika kaydını silmek için şu komutu kullanın `delete sslcert` :</span><span class="sxs-lookup"><span data-stu-id="59c4f-271">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="59c4f-272">*netsh.exe*için başvuru belgeleri:</span><span class="sxs-lookup"><span data-stu-id="59c4f-272">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="59c4f-273">[Köprü Metni Aktarım Protokolü (HTTP) için Netsh komutları](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="59c4f-273">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="59c4f-274">UrlPrefix dizeleri</span><span class="sxs-lookup"><span data-stu-id="59c4f-274">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="59c4f-275">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-275">Run the app.</span></span>

   <span data-ttu-id="59c4f-276">1024 'den büyük bir bağlantı noktası numarası ile HTTP (HTTPS değil) kullanarak localhost 'a bağlanırken uygulamayı çalıştırmak için yönetici ayrıcalıklarına gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-276">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="59c4f-277">Diğer yapılandırmalarda (örneğin, yerel bir IP adresi veya 443 numaralı bağlantı noktasına bağlama), uygulamayı yönetici ayrıcalıklarıyla çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-277">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="59c4f-278">Uygulama, sunucunun genel IP adresinde yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-278">The app responds at the server's public IP address.</span></span> <span data-ttu-id="59c4f-279">Bu örnekte, sunucusuna, genel IP adresinde Internet 'ten ulaşılırsa `104.214.79.47` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-279">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="59c4f-280">Bu örnekte bir geliştirme sertifikası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-280">A development certificate is used in this example.</span></span> <span data-ttu-id="59c4f-281">Tarayıcının güvenilmeyen sertifika uyarısı atlandıktan sonra sayfa güvenli bir şekilde yüklenir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-281">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![Uygulamanın dizin sayfasını yüklü olarak gösteren tarayıcı penceresi](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="59c4f-283">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="59c4f-283">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="59c4f-284">Internet veya şirket ağından gelen isteklerle etkileşime geçen HTTP.sys tarafından barındırılan uygulamalar için, proxy sunucularının ve yük dengeleyiciler arkasında barındırılırken ek yapılandırma gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-284">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="59c4f-285">Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="59c4f-285">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="advanced-http2-features-to-support-grpc"></a><span data-ttu-id="59c4f-286">GRPC 'yi desteklemeye yönelik gelişmiş HTTP/2 özellikleri</span><span class="sxs-lookup"><span data-stu-id="59c4f-286">Advanced HTTP/2 features to support gRPC</span></span>

<span data-ttu-id="59c4f-287">HTTP.sys ' deki ek HTTP/2 özellikleri, yanıt tanıtımları ve sıfırlama çerçeveleri gönderme desteği dahil olmak üzere gRPC 'yi destekler.</span><span class="sxs-lookup"><span data-stu-id="59c4f-287">Additional HTTP/2 features in HTTP.sys support gRPC, including support for response trailers and sending reset frames.</span></span>

<span data-ttu-id="59c4f-288">GRPC 'yi HTTP.SYS çalıştırma gereksinimleri:</span><span class="sxs-lookup"><span data-stu-id="59c4f-288">Requirements to run gRPC with HTTP.SYS:</span></span>

* <span data-ttu-id="59c4f-289">Windows 10, işletim sistemi derlemesi 19041,508 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="59c4f-289">Windows 10, OS Build 19041.508 or later</span></span>
* <span data-ttu-id="59c4f-290">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="59c4f-290">TLS 1.2 or later connection</span></span>

### <a name="trailers"></a><span data-ttu-id="59c4f-291">Larına</span><span class="sxs-lookup"><span data-stu-id="59c4f-291">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="59c4f-292">Sıfırla</span><span class="sxs-lookup"><span data-stu-id="59c4f-292">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

## <a name="additional-resources"></a><span data-ttu-id="59c4f-293">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="59c4f-293">Additional resources</span></span>

* [<span data-ttu-id="59c4f-294">HTTP.sysile Windows kimlik doğrulamasını etkinleştir </span><span class="sxs-lookup"><span data-stu-id="59c4f-294">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="59c4f-295">HTTP Sunucusu API 'SI</span><span class="sxs-lookup"><span data-stu-id="59c4f-295">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="59c4f-296">ASPNET/HttpSysServer GitHub deposu (kaynak kodu)</span><span class="sxs-lookup"><span data-stu-id="59c4f-296">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="59c4f-297">Ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="59c4f-297">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="59c4f-298">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) , yalnızca Windows üzerinde çalışan [ASP.NET Core için bir Web sunucusudur](xref:fundamentals/servers/index) .</span><span class="sxs-lookup"><span data-stu-id="59c4f-298">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="59c4f-299">HTTP.sys, [Kestrel](xref:fundamentals/servers/kestrel) Server için bir alternatiftir ve Kestrel tarafından sağlamayan bazı özellikler sunar.</span><span class="sxs-lookup"><span data-stu-id="59c4f-299">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="59c4f-300">HTTP.sys [ASP.NET Core modülüyle](xref:host-and-deploy/aspnet-core-module) uyumlu DEĞILDIR ve ııs veya IIS Express ile kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="59c4f-300">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="59c4f-301">HTTP.sys aşağıdaki özellikleri destekler:</span><span class="sxs-lookup"><span data-stu-id="59c4f-301">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="59c4f-302">Windows Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="59c4f-302">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="59c4f-303">Bağlantı noktası Paylaşımı</span><span class="sxs-lookup"><span data-stu-id="59c4f-303">Port sharing</span></span>
* <span data-ttu-id="59c4f-304">SNı ile HTTPS</span><span class="sxs-lookup"><span data-stu-id="59c4f-304">HTTPS with SNI</span></span>
* <span data-ttu-id="59c4f-305">TLS üzerinden HTTP/2 (Windows 10 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="59c4f-305">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="59c4f-306">Doğrudan dosya iletimi</span><span class="sxs-lookup"><span data-stu-id="59c4f-306">Direct file transmission</span></span>
* <span data-ttu-id="59c4f-307">Yanıtları Önbelleğe Alma</span><span class="sxs-lookup"><span data-stu-id="59c4f-307">Response caching</span></span>
* <span data-ttu-id="59c4f-308">WebSockets (Windows 8 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="59c4f-308">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="59c4f-309">Desteklenen Windows sürümleri:</span><span class="sxs-lookup"><span data-stu-id="59c4f-309">Supported Windows versions:</span></span>

* <span data-ttu-id="59c4f-310">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="59c4f-310">Windows 7 or later</span></span>
* <span data-ttu-id="59c4f-311">Windows Server 2008 R2 veya sonraki sürümü</span><span class="sxs-lookup"><span data-stu-id="59c4f-311">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="59c4f-312">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="59c4f-312">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="59c4f-313">Ne zaman kullanılacağı HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="59c4f-313">When to use HTTP.sys</span></span>

<span data-ttu-id="59c4f-314">HTTP.sys, şu yerlerde olan dağıtımlar için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="59c4f-314">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="59c4f-315">Sunucuyu IIS kullanmadan doğrudan Internet 'e sunmaya gerek vardır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-315">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys doğrudan Internet ile iletişim kurar](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="59c4f-317">İç dağıtım, [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)gibi Kestrel 'de kullanılamayan bir özelliği gerektirir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-317">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys doğrudan iç ağla iletişim kurar](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="59c4f-319">HTTP.sys, birçok saldırı türüne karşı koruyan ve tam özellikli bir Web sunucusu için sağlamlık, güvenlik ve ölçeklenebilirlik sağlayan çok sayıda teknolojiden oluşur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-319">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="59c4f-320">IIS, HTTP.sys en üstünde bir HTTP dinleyicisi olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-320">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="59c4f-321">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="59c4f-321">HTTP/2 support</span></span>

<span data-ttu-id="59c4f-322">Aşağıdaki temel gereksinimler karşılanıyorsa ASP.NET Core uygulamalar için [http/2](https://httpwg.org/specs/rfc7540.html) etkinleştirilir:</span><span class="sxs-lookup"><span data-stu-id="59c4f-322">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="59c4f-323">Windows Server 2016/Windows 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="59c4f-323">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="59c4f-324">[Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı</span><span class="sxs-lookup"><span data-stu-id="59c4f-324">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="59c4f-325">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="59c4f-325">TLS 1.2 or later connection</span></span>

<span data-ttu-id="59c4f-326">Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-326">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="59c4f-327">HTTP/2 varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-327">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="59c4f-328">HTTP/2 bağlantısı kurulmadıysa, bağlantı HTTP/1.1 'ye geri döner.</span><span class="sxs-lookup"><span data-stu-id="59c4f-328">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="59c4f-329">Windows 'un gelecek bir sürümünde http/2 yapılandırma bayrakları, HTTP.sys ile HTTP/2 devre dışı bırakma özelliği de dahil olmak üzere kullanılabilir olacaktır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-329">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="59c4f-330">Kerberos ile çekirdek modu kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="59c4f-330">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="59c4f-331">Kerberos kimlik doğrulama protokolü ile çekirdek modu kimlik doğrulamasına temsilciler HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="59c4f-331">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="59c4f-332">Kullanıcı modu kimlik doğrulaması, Kerberos ve HTTP.sys desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="59c4f-332">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="59c4f-333">Makine hesabı, Active Directory alındığı ve kullanıcının kimliğini doğrulamak için istemci tarafından sunucuya iletilen Kerberos belirtecinin/biletinin şifresini çözmek için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-333">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="59c4f-334">Uygulamanın kullanıcısına değil, ana bilgisayar için hizmet asıl adını (SPN) kaydedin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-334">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="59c4f-335">HTTP.sys kullanma</span><span class="sxs-lookup"><span data-stu-id="59c4f-335">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="59c4f-336">ASP.NET Core uygulamasını kullanacak şekilde yapılandırma HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="59c4f-336">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="59c4f-337"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Ana bilgisayarı oluştururken, gerekli herhangi bir yöntemi belirterek uzantı yöntemini çağırın <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> .</span><span class="sxs-lookup"><span data-stu-id="59c4f-337">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="59c4f-338">Aşağıdaki örnek, seçeneklerini varsayılan değerlerine ayarlar:</span><span class="sxs-lookup"><span data-stu-id="59c4f-338">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Program.cs?name=snippet1&highlight=5-13)]

<span data-ttu-id="59c4f-339">Ek HTTP.sys yapılandırması, [kayıt defteri ayarları](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)aracılığıyla işlenir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-339">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="59c4f-340">**HTTP.sys seçenekleri**</span><span class="sxs-lookup"><span data-stu-id="59c4f-340">**HTTP.sys options**</span></span>

| <span data-ttu-id="59c4f-341">Özellik</span><span class="sxs-lookup"><span data-stu-id="59c4f-341">Property</span></span> | <span data-ttu-id="59c4f-342">Açıklama</span><span class="sxs-lookup"><span data-stu-id="59c4f-342">Description</span></span> | <span data-ttu-id="59c4f-343">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="59c4f-343">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="59c4f-344">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="59c4f-344">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="59c4f-345">Ve için zaman uyumlu giriş/çıkışa izin verilip verilmeyeceğini `HttpContext.Request.Body` denetleyin `HttpContext.Response.Body` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-345">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `false` |
| [<span data-ttu-id="59c4f-346">Authentication. AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="59c4f-346">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="59c4f-347">Anonim isteklere izin verin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-347">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="59c4f-348">Authentication. düzenleri</span><span class="sxs-lookup"><span data-stu-id="59c4f-348">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="59c4f-349">İzin verilen kimlik doğrulama düzenlerini belirtin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-349">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="59c4f-350">Dinleyici elden atılırken önce herhangi bir zamanda değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-350">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="59c4f-351">Değerler [authenticationdüzenlerinin numaralandırması](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)tarafından sağlanır: `Basic` ,,, `Kerberos` `Negotiate` `None` , ve `NTLM` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-351">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="59c4f-352">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="59c4f-352">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="59c4f-353">Uygun üst bilgileri içeren yanıtlar için [çekirdek modu](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) önbelleğe almayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-353">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="59c4f-354">Yanıt `Set-Cookie` , `Vary` veya `Pragma` üst bilgileri içermeyebilir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-354">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="59c4f-355">`Cache-Control` `public` Bir ya da değeri ya da bir üst bilgi içermelidir `shared-max-age` `max-age` `Expires` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-355">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="59c4f-356">En fazla eşzamanlı kabul sayısı.</span><span class="sxs-lookup"><span data-stu-id="59c4f-356">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="59c4f-357">5 &times; [ortam. <br> ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="59c4f-357">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="59c4f-358">Kabul edilecek eşzamanlı bağlantı sayısı üst sınırı.</span><span class="sxs-lookup"><span data-stu-id="59c4f-358">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="59c4f-359">`-1`Sonsuz için kullanın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-359">Use `-1` for infinite.</span></span> <span data-ttu-id="59c4f-360">`null`Kayıt defterinin makine genelindeki ayarını kullanmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-360">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="59c4f-361">(makine genelinde</span><span class="sxs-lookup"><span data-stu-id="59c4f-361">(machine-wide</span></span><br><span data-ttu-id="59c4f-362">ayarlanmasını</span><span class="sxs-lookup"><span data-stu-id="59c4f-362">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="59c4f-363"><a href="#maxrequestbodysize">MaxRequestBodySize</a> bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-363">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="59c4f-364">30000000 bayt</span><span class="sxs-lookup"><span data-stu-id="59c4f-364">30000000 bytes</span></span><br><span data-ttu-id="59c4f-365">(~ 28,6 MB)</span><span class="sxs-lookup"><span data-stu-id="59c4f-365">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="59c4f-366">Sıraya alınabilen en fazla istek sayısı.</span><span class="sxs-lookup"><span data-stu-id="59c4f-366">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="59c4f-367">1000</span><span class="sxs-lookup"><span data-stu-id="59c4f-367">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="59c4f-368">İstemci bağlantısının kesilmesinden kaynaklanan yanıt gövdesi yazmasının, özel durumlar oluşturması veya normal şekilde tamamlanması gerektiğini belirtin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-368">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="59c4f-369">(normal olarak)</span><span class="sxs-lookup"><span data-stu-id="59c4f-369">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="59c4f-370"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager>Kayıt defterinde da yapılandırılabilen HTTP.sys yapılandırmasını kullanıma sunun.</span><span class="sxs-lookup"><span data-stu-id="59c4f-370">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="59c4f-371">Varsayılan değerler de dahil olmak üzere her bir ayar hakkında daha fazla bilgi edinmek için API bağlantılarını izleyin:</span><span class="sxs-lookup"><span data-stu-id="59c4f-371">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="59c4f-372">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): http sunucusu API 'sinin varlık gövdesini etkin tut bağlantısı üzerinde boşaltmasına izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="59c4f-372">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="59c4f-373">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): istek varlığı gövdesinin gelmesi için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="59c4f-373">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="59c4f-374">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): http sunucusu API 'sinin istek üst bilgisini ayrıştırması için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="59c4f-374">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="59c4f-375">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): boştaki bir bağlantı için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="59c4f-375">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="59c4f-376">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): yanıt için en düşük gönderme hızı.</span><span class="sxs-lookup"><span data-stu-id="59c4f-376">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="59c4f-377">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): isteğin, uygulamanın onu seçmeden önce istek kuyruğunda kalması için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="59c4f-377">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="59c4f-378"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>HTTP.sys kaydolmak için öğesini belirtin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-378">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="59c4f-379">En yararlı olan [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), koleksiyona bir ön ek eklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-379">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="59c4f-380">Bunlar, dinleyici elden atılıyor öncesinde herhangi bir zamanda değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-380">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="59c4f-381">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="59c4f-381">**MaxRequestBodySize**</span></span>

<span data-ttu-id="59c4f-382">Herhangi bir istek gövdesinin bayt olarak izin verilen en büyük boyutu.</span><span class="sxs-lookup"><span data-stu-id="59c4f-382">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="59c4f-383">Olarak ayarlandığında `null` , en büyük istek gövdesi boyutu sınırsızdır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-383">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="59c4f-384">Bu sınırın, her zaman sınırsız olan yükseltilmiş bağlantıları üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-384">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="59c4f-385">Tek bir ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yöntem, `IActionResult` <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="59c4f-385">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="59c4f-386">Uygulama isteği okumayı başlattıktan sonra bir istek üzerindeki sınırı yapılandırmayı denerse bir özel durum oluşur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-386">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="59c4f-387">Özelliğin `IsReadOnly` salt okuma durumunda olup olmadığını göstermek için bir özellik kullanılabilir `MaxRequestBodySize` , yani sınırı yapılandırmak için çok geç.</span><span class="sxs-lookup"><span data-stu-id="59c4f-387">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="59c4f-388">Uygulamanın istek başına geçersiz kılması gerekiyorsa <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> , şunu kullanın <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :</span><span class="sxs-lookup"><span data-stu-id="59c4f-388">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="59c4f-389">Visual Studio kullanıyorsanız, uygulamanın IIS veya IIS Express çalıştıracak şekilde yapılandırılmadığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="59c4f-389">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="59c4f-390">Visual Studio 'da varsayılan başlatma profili IIS Express içindir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-390">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="59c4f-391">Projeyi konsol uygulaması olarak çalıştırmak için, aşağıdaki ekran görüntüsünde gösterildiği gibi seçili profili el ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="59c4f-391">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![Konsol uygulaması profilini seçin](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="59c4f-393">Windows Server 'ı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="59c4f-393">Configure Windows Server</span></span>

1. <span data-ttu-id="59c4f-394">Uygulama için açılacak bağlantı noktalarını belirleme ve [Windows Güvenlik Duvarı](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 'Nı veya [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 'ini kullanarak trafiğin HTTP.sys ulaşmasını sağlamak üzere güvenlik duvarı bağlantı noktalarını açın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-394">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="59c4f-395">Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-395">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="59c4f-396">Bir Azure sanal makinesine dağıtım yaparken, [ağ güvenlik grubundaki](/azure/virtual-machines/windows/nsg-quickstart-portal)bağlantı noktalarını açın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-396">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="59c4f-397">Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-397">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="59c4f-398">Gerekirse, X. 509.440 sertifikalarını edinin ve yükler.</span><span class="sxs-lookup"><span data-stu-id="59c4f-398">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="59c4f-399">Windows 'da, [New-SelfSignedCertificate PowerShell cmdlet 'ini](/powershell/module/pkiclient/new-selfsignedcertificate)kullanarak otomatik olarak imzalanan sertifikalar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="59c4f-399">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="59c4f-400">Desteklenmeyen bir örnek için bkz. [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span><span class="sxs-lookup"><span data-stu-id="59c4f-400">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="59c4f-401">Sunucunun **yerel makine** > **Kişisel** deposunda otomatik olarak imzalanan veya CA imzalı sertifikalar yükler.</span><span class="sxs-lookup"><span data-stu-id="59c4f-401">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="59c4f-402">Uygulama [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise, .net core, .NET Framework veya her ikisini de (uygulama .NET Framework hedefleyen bir .NET Core uygulaması ise) yükler.</span><span class="sxs-lookup"><span data-stu-id="59c4f-402">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="59c4f-403">**.NET Core**: uygulama .NET Core gerektiriyorsa .NET Core [Indirmelerinde](https://dotnet.microsoft.com/download) **.NET Core çalışma zamanı** yükleyicisini edinip çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-403">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="59c4f-404">Tam SDK 'Yı sunucuya yüklemeyin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-404">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="59c4f-405">**.NET Framework**: uygulama .NET Framework gerektiriyorsa [.NET Framework yükleme kılavuzuna](/dotnet/framework/install/)bakın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-405">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="59c4f-406">Gerekli .NET Framework yüklemesi.</span><span class="sxs-lookup"><span data-stu-id="59c4f-406">Install the required .NET Framework.</span></span> <span data-ttu-id="59c4f-407">En son .NET Framework yükleyicisi [.NET Core İndirmeleri](https://dotnet.microsoft.com/download) sayfasından edinilebilir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-407">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="59c4f-408">Uygulama, [kendi içinde bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise, uygulama çalışma zamanını dağıtımda içerir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-408">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="59c4f-409">Sunucuda çerçeve yüklemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="59c4f-409">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="59c4f-410">Uygulamada URL 'Leri ve bağlantı noktalarını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-410">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="59c4f-411">Varsayılan olarak, ASP.NET Core öğesine bağlanır `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-411">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="59c4f-412">URL öneklerini ve bağlantı noktalarını yapılandırmak için seçenekler şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="59c4f-412">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="59c4f-413">`urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="59c4f-413">`urls` command-line argument</span></span>
   * <span data-ttu-id="59c4f-414">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="59c4f-414">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="59c4f-415">Aşağıdaki kod örneği, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> `10.0.0.4` 443 numaralı bağlantı noktasında sunucunun yerel IP adresi ile nasıl kullanılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="59c4f-415">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/3.x/Program.cs?highlight=7)]

   <span data-ttu-id="59c4f-416">Avantajı `UrlPrefixes` , hatalı biçimli ön ekler için hemen bir hata iletisi oluşturulmasından oluşur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-416">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="59c4f-417">Ayarları `UrlPrefixes` geçersiz kıl `UseUrls` / `urls` / `ASPNETCORE_URLS` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-417">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="59c4f-418">Bu nedenle, `UseUrls` `urls` ve `ASPNETCORE_URLS` ortam değişkeninin avantajı Kestrel ve HTTP.sys arasında geçiş yapmak daha kolay bir hale gelir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-418">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="59c4f-419">HTTP.sys, [http sunucusu API UrlPrefix dize biçimlerini](/windows/win32/http/urlprefix-strings)kullanır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-419">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="59c4f-420">Üst düzey joker karakter bağlamaları ( `http://*:80/` ve `http://+:80` ) kullanılmamalıdır **not** .</span><span class="sxs-lookup"><span data-stu-id="59c4f-420">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="59c4f-421">Üst düzey joker karakter bağlamaları uygulama güvenliği güvenlik açıklarını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-421">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="59c4f-422">Bu hem güçlü hem de zayıf Joker karakterlere yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-422">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="59c4f-423">Joker karakterler yerine açık konak adlarını veya IP adreslerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-423">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="59c4f-424">Alt etki alanı joker bağlantısı (örneğin, `*.mysub.com` ), tüm üst etki alanını ( `*.com` Bu güvenlik açığı olan aksine) kontrol ediyorsanız bir güvenlik riski değildir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-424">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="59c4f-425">Daha fazla bilgi için bkz. [RFC 7230: bölüm 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="59c4f-425">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="59c4f-426">Ön ek, sunucudaki URL öneklerini ister.</span><span class="sxs-lookup"><span data-stu-id="59c4f-426">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="59c4f-427">HTTP.sys yapılandırmaya yönelik yerleşik araç *netsh.exe*.</span><span class="sxs-lookup"><span data-stu-id="59c4f-427">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="59c4f-428">*netsh.exe* , URL öneklerini ayırmak ve X. 509.440 sertifikaları atamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-428">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="59c4f-429">Araç, yönetici ayrıcalıkları gerektirir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-429">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="59c4f-430">Uygulamanın URL 'Lerini kaydetmek için *netsh.exe* aracını kullanın:</span><span class="sxs-lookup"><span data-stu-id="59c4f-430">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="59c4f-431">`<URL>`: Tam nitelikli Tekdüzen Kaynak Bulucu (URL).</span><span class="sxs-lookup"><span data-stu-id="59c4f-431">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="59c4f-432">Joker karakter bağlama kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-432">Don't use a wildcard binding.</span></span> <span data-ttu-id="59c4f-433">Geçerli bir ana bilgisayar adı veya yerel IP adresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-433">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="59c4f-434">*URL, sondaki eğik çizgi içermelidir.*</span><span class="sxs-lookup"><span data-stu-id="59c4f-434">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="59c4f-435">`<USER>`: Kullanıcı veya Kullanıcı grubu adını belirtir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-435">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="59c4f-436">Aşağıdaki örnekte sunucusunun yerel IP adresi `10.0.0.4` :</span><span class="sxs-lookup"><span data-stu-id="59c4f-436">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="59c4f-437">Bir URL kaydedildiğinde, araç ile yanıt verir `URL reservation successfully added` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-437">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="59c4f-438">Kayıtlı bir URL 'yi silmek için şu `delete urlacl` komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="59c4f-438">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="59c4f-439">X. 509.440 sertifikalarını sunucusuna kaydedin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-439">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="59c4f-440">Uygulamanın sertifikalarını kaydetmek için *netsh.exe* aracını kullanın:</span><span class="sxs-lookup"><span data-stu-id="59c4f-440">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="59c4f-441">`<IP>`: Bağlama için yerel IP adresini belirtir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-441">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="59c4f-442">Joker karakter bağlama kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-442">Don't use a wildcard binding.</span></span> <span data-ttu-id="59c4f-443">Geçerli bir IP adresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-443">Use a valid IP address.</span></span>
   * <span data-ttu-id="59c4f-444">`<PORT>`: Bağlama için bağlantı noktasını belirtir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-444">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="59c4f-445">`<THUMBPRINT>`: X. 509.440 sertifika parmak izi.</span><span class="sxs-lookup"><span data-stu-id="59c4f-445">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="59c4f-446">`<GUID>`: Uygulamayı bilgilendirme amacıyla temsil eden geliştirici tarafından oluşturulan bir GUID.</span><span class="sxs-lookup"><span data-stu-id="59c4f-446">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="59c4f-447">Başvuru amacıyla, GUID 'yi uygulamada bir paket etiketi olarak depolayın:</span><span class="sxs-lookup"><span data-stu-id="59c4f-447">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="59c4f-448">Visual Studio 'da:</span><span class="sxs-lookup"><span data-stu-id="59c4f-448">In Visual Studio:</span></span>
     * <span data-ttu-id="59c4f-449">**Çözüm Gezgini** ' de uygulamaya sağ tıklayıp **Özellikler**' i seçerek uygulamanın proje özelliklerini açın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-449">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="59c4f-450">**Paket** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-450">Select the **Package** tab.</span></span>
     * <span data-ttu-id="59c4f-451">**Etiketler** alanında oluşturduğunuz GUID 'yi girin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-451">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="59c4f-452">Visual Studio kullanmadığınız durumlarda:</span><span class="sxs-lookup"><span data-stu-id="59c4f-452">When not using Visual Studio:</span></span>
     * <span data-ttu-id="59c4f-453">Uygulamanın proje dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-453">Open the app's project file.</span></span>
     * <span data-ttu-id="59c4f-454">`<PackageTags>`Oluşturduğunuz GUID ile yeni veya var olan bir özelliği ekleyin `<PropertyGroup>` :</span><span class="sxs-lookup"><span data-stu-id="59c4f-454">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="59c4f-455">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="59c4f-455">In the following example:</span></span>

   * <span data-ttu-id="59c4f-456">Sunucunun yerel IP adresi `10.0.0.4` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-456">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="59c4f-457">Bir çevrimiçi rastgele GUID Oluşturucu değeri sağlar `appid` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-457">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="59c4f-458">Bir sertifika kaydedildiğinde, araç ile yanıt verir `SSL Certificate successfully added` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-458">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="59c4f-459">Bir sertifika kaydını silmek için şu komutu kullanın `delete sslcert` :</span><span class="sxs-lookup"><span data-stu-id="59c4f-459">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="59c4f-460">*netsh.exe*için başvuru belgeleri:</span><span class="sxs-lookup"><span data-stu-id="59c4f-460">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="59c4f-461">[Köprü Metni Aktarım Protokolü (HTTP) için Netsh komutları](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="59c4f-461">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="59c4f-462">UrlPrefix dizeleri</span><span class="sxs-lookup"><span data-stu-id="59c4f-462">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="59c4f-463">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-463">Run the app.</span></span>

   <span data-ttu-id="59c4f-464">1024 'den büyük bir bağlantı noktası numarası ile HTTP (HTTPS değil) kullanarak localhost 'a bağlanırken uygulamayı çalıştırmak için yönetici ayrıcalıklarına gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-464">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="59c4f-465">Diğer yapılandırmalarda (örneğin, yerel bir IP adresi veya 443 numaralı bağlantı noktasına bağlama), uygulamayı yönetici ayrıcalıklarıyla çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-465">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="59c4f-466">Uygulama, sunucunun genel IP adresinde yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-466">The app responds at the server's public IP address.</span></span> <span data-ttu-id="59c4f-467">Bu örnekte, sunucusuna, genel IP adresinde Internet 'ten ulaşılırsa `104.214.79.47` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-467">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="59c4f-468">Bu örnekte bir geliştirme sertifikası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-468">A development certificate is used in this example.</span></span> <span data-ttu-id="59c4f-469">Tarayıcının güvenilmeyen sertifika uyarısı atlandıktan sonra sayfa güvenli bir şekilde yüklenir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-469">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![Uygulamanın dizin sayfasını yüklü olarak gösteren tarayıcı penceresi](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="59c4f-471">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="59c4f-471">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="59c4f-472">Internet veya şirket ağından gelen isteklerle etkileşime geçen HTTP.sys tarafından barındırılan uygulamalar için, proxy sunucularının ve yük dengeleyiciler arkasında barındırılırken ek yapılandırma gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-472">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="59c4f-473">Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="59c4f-473">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="59c4f-474">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="59c4f-474">Additional resources</span></span>

* [<span data-ttu-id="59c4f-475">HTTP.sysile Windows kimlik doğrulamasını etkinleştir </span><span class="sxs-lookup"><span data-stu-id="59c4f-475">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="59c4f-476">HTTP Sunucusu API 'SI</span><span class="sxs-lookup"><span data-stu-id="59c4f-476">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="59c4f-477">ASPNET/HttpSysServer GitHub deposu (kaynak kodu)</span><span class="sxs-lookup"><span data-stu-id="59c4f-477">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="59c4f-478">Ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="59c4f-478">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="59c4f-479">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) , yalnızca Windows üzerinde çalışan [ASP.NET Core için bir Web sunucusudur](xref:fundamentals/servers/index) .</span><span class="sxs-lookup"><span data-stu-id="59c4f-479">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="59c4f-480">HTTP.sys, [Kestrel](xref:fundamentals/servers/kestrel) Server için bir alternatiftir ve Kestrel tarafından sağlamayan bazı özellikler sunar.</span><span class="sxs-lookup"><span data-stu-id="59c4f-480">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="59c4f-481">HTTP.sys [ASP.NET Core modülüyle](xref:host-and-deploy/aspnet-core-module) uyumlu DEĞILDIR ve ııs veya IIS Express ile kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="59c4f-481">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="59c4f-482">HTTP.sys aşağıdaki özellikleri destekler:</span><span class="sxs-lookup"><span data-stu-id="59c4f-482">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="59c4f-483">Windows Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="59c4f-483">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="59c4f-484">Bağlantı noktası Paylaşımı</span><span class="sxs-lookup"><span data-stu-id="59c4f-484">Port sharing</span></span>
* <span data-ttu-id="59c4f-485">SNı ile HTTPS</span><span class="sxs-lookup"><span data-stu-id="59c4f-485">HTTPS with SNI</span></span>
* <span data-ttu-id="59c4f-486">TLS üzerinden HTTP/2 (Windows 10 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="59c4f-486">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="59c4f-487">Doğrudan dosya iletimi</span><span class="sxs-lookup"><span data-stu-id="59c4f-487">Direct file transmission</span></span>
* <span data-ttu-id="59c4f-488">Yanıtları Önbelleğe Alma</span><span class="sxs-lookup"><span data-stu-id="59c4f-488">Response caching</span></span>
* <span data-ttu-id="59c4f-489">WebSockets (Windows 8 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="59c4f-489">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="59c4f-490">Desteklenen Windows sürümleri:</span><span class="sxs-lookup"><span data-stu-id="59c4f-490">Supported Windows versions:</span></span>

* <span data-ttu-id="59c4f-491">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="59c4f-491">Windows 7 or later</span></span>
* <span data-ttu-id="59c4f-492">Windows Server 2008 R2 veya sonraki sürümü</span><span class="sxs-lookup"><span data-stu-id="59c4f-492">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="59c4f-493">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="59c4f-493">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="59c4f-494">Ne zaman kullanılacağı HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="59c4f-494">When to use HTTP.sys</span></span>

<span data-ttu-id="59c4f-495">HTTP.sys, şu yerlerde olan dağıtımlar için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="59c4f-495">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="59c4f-496">Sunucuyu IIS kullanmadan doğrudan Internet 'e sunmaya gerek vardır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-496">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys doğrudan Internet ile iletişim kurar](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="59c4f-498">İç dağıtım, [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)gibi Kestrel 'de kullanılamayan bir özelliği gerektirir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-498">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys doğrudan iç ağla iletişim kurar](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="59c4f-500">HTTP.sys, birçok saldırı türüne karşı koruyan ve tam özellikli bir Web sunucusu için sağlamlık, güvenlik ve ölçeklenebilirlik sağlayan çok sayıda teknolojiden oluşur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-500">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="59c4f-501">IIS, HTTP.sys en üstünde bir HTTP dinleyicisi olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-501">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="59c4f-502">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="59c4f-502">HTTP/2 support</span></span>

<span data-ttu-id="59c4f-503">Aşağıdaki temel gereksinimler karşılanıyorsa ASP.NET Core uygulamalar için [http/2](https://httpwg.org/specs/rfc7540.html) etkinleştirilir:</span><span class="sxs-lookup"><span data-stu-id="59c4f-503">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="59c4f-504">Windows Server 2016/Windows 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="59c4f-504">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="59c4f-505">[Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı</span><span class="sxs-lookup"><span data-stu-id="59c4f-505">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="59c4f-506">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="59c4f-506">TLS 1.2 or later connection</span></span>

<span data-ttu-id="59c4f-507">Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-507">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="59c4f-508">HTTP/2 varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-508">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="59c4f-509">HTTP/2 bağlantısı kurulmadıysa, bağlantı HTTP/1.1 'ye geri döner.</span><span class="sxs-lookup"><span data-stu-id="59c4f-509">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="59c4f-510">Windows 'un gelecek bir sürümünde http/2 yapılandırma bayrakları, HTTP.sys ile HTTP/2 devre dışı bırakma özelliği de dahil olmak üzere kullanılabilir olacaktır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-510">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="59c4f-511">Kerberos ile çekirdek modu kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="59c4f-511">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="59c4f-512">Kerberos kimlik doğrulama protokolü ile çekirdek modu kimlik doğrulamasına temsilciler HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="59c4f-512">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="59c4f-513">Kullanıcı modu kimlik doğrulaması, Kerberos ve HTTP.sys desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="59c4f-513">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="59c4f-514">Makine hesabı, Active Directory alındığı ve kullanıcının kimliğini doğrulamak için istemci tarafından sunucuya iletilen Kerberos belirtecinin/biletinin şifresini çözmek için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-514">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="59c4f-515">Uygulamanın kullanıcısına değil, ana bilgisayar için hizmet asıl adını (SPN) kaydedin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-515">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="59c4f-516">HTTP.sys kullanma</span><span class="sxs-lookup"><span data-stu-id="59c4f-516">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="59c4f-517">ASP.NET Core uygulamasını kullanacak şekilde yapılandırma HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="59c4f-517">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="59c4f-518">[Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) ([NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) kullanılırken proje dosyasındaki bir paket başvurusu gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-518">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="59c4f-519">`Microsoft.AspNetCore.App`Metapackage 'i kullanmıyorsanız [Microsoft. Aspnetcore. Server. httpsys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)öğesine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-519">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="59c4f-520"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Ana bilgisayarı oluştururken, gerekli herhangi bir yöntemi belirterek uzantı yöntemini çağırın <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> .</span><span class="sxs-lookup"><span data-stu-id="59c4f-520">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="59c4f-521">Aşağıdaki örnek, seçeneklerini varsayılan değerlerine ayarlar:</span><span class="sxs-lookup"><span data-stu-id="59c4f-521">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="59c4f-522">Ek HTTP.sys yapılandırması, [kayıt defteri ayarları](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)aracılığıyla işlenir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-522">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="59c4f-523">**HTTP.sys seçenekleri**</span><span class="sxs-lookup"><span data-stu-id="59c4f-523">**HTTP.sys options**</span></span>

| <span data-ttu-id="59c4f-524">Özellik</span><span class="sxs-lookup"><span data-stu-id="59c4f-524">Property</span></span> | <span data-ttu-id="59c4f-525">Açıklama</span><span class="sxs-lookup"><span data-stu-id="59c4f-525">Description</span></span> | <span data-ttu-id="59c4f-526">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="59c4f-526">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="59c4f-527">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="59c4f-527">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="59c4f-528">Ve için zaman uyumlu giriş/çıkışa izin verilip verilmeyeceğini `HttpContext.Request.Body` denetleyin `HttpContext.Response.Body` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-528">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="59c4f-529">Authentication. AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="59c4f-529">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="59c4f-530">Anonim isteklere izin verin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-530">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="59c4f-531">Authentication. düzenleri</span><span class="sxs-lookup"><span data-stu-id="59c4f-531">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="59c4f-532">İzin verilen kimlik doğrulama düzenlerini belirtin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-532">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="59c4f-533">Dinleyici elden atılırken önce herhangi bir zamanda değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-533">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="59c4f-534">Değerler [authenticationdüzenlerinin numaralandırması](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)tarafından sağlanır: `Basic` ,,, `Kerberos` `Negotiate` `None` , ve `NTLM` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-534">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="59c4f-535">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="59c4f-535">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="59c4f-536">Uygun üst bilgileri içeren yanıtlar için [çekirdek modu](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) önbelleğe almayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-536">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="59c4f-537">Yanıt `Set-Cookie` , `Vary` veya `Pragma` üst bilgileri içermeyebilir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-537">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="59c4f-538">`Cache-Control` `public` Bir ya da değeri ya da bir üst bilgi içermelidir `shared-max-age` `max-age` `Expires` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-538">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="59c4f-539">En fazla eşzamanlı kabul sayısı.</span><span class="sxs-lookup"><span data-stu-id="59c4f-539">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="59c4f-540">5 &times; [ortam. <br> ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="59c4f-540">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="59c4f-541">Kabul edilecek eşzamanlı bağlantı sayısı üst sınırı.</span><span class="sxs-lookup"><span data-stu-id="59c4f-541">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="59c4f-542">`-1`Sonsuz için kullanın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-542">Use `-1` for infinite.</span></span> <span data-ttu-id="59c4f-543">`null`Kayıt defterinin makine genelindeki ayarını kullanmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-543">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="59c4f-544">(makine genelinde</span><span class="sxs-lookup"><span data-stu-id="59c4f-544">(machine-wide</span></span><br><span data-ttu-id="59c4f-545">ayarlanmasını</span><span class="sxs-lookup"><span data-stu-id="59c4f-545">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="59c4f-546"><a href="#maxrequestbodysize">MaxRequestBodySize</a> bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-546">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="59c4f-547">30000000 bayt</span><span class="sxs-lookup"><span data-stu-id="59c4f-547">30000000 bytes</span></span><br><span data-ttu-id="59c4f-548">(~ 28,6 MB)</span><span class="sxs-lookup"><span data-stu-id="59c4f-548">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="59c4f-549">Sıraya alınabilen en fazla istek sayısı.</span><span class="sxs-lookup"><span data-stu-id="59c4f-549">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="59c4f-550">1000</span><span class="sxs-lookup"><span data-stu-id="59c4f-550">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="59c4f-551">İstemci bağlantısının kesilmesinden kaynaklanan yanıt gövdesi yazmasının, özel durumlar oluşturması veya normal şekilde tamamlanması gerektiğini belirtin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-551">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="59c4f-552">(normal olarak)</span><span class="sxs-lookup"><span data-stu-id="59c4f-552">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="59c4f-553"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager>Kayıt defterinde da yapılandırılabilen HTTP.sys yapılandırmasını kullanıma sunun.</span><span class="sxs-lookup"><span data-stu-id="59c4f-553">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="59c4f-554">Varsayılan değerler de dahil olmak üzere her bir ayar hakkında daha fazla bilgi edinmek için API bağlantılarını izleyin:</span><span class="sxs-lookup"><span data-stu-id="59c4f-554">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="59c4f-555">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): http sunucusu API 'sinin varlık gövdesini etkin tut bağlantısı üzerinde boşaltmasına izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="59c4f-555">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="59c4f-556">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): istek varlığı gövdesinin gelmesi için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="59c4f-556">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="59c4f-557">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): http sunucusu API 'sinin istek üst bilgisini ayrıştırması için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="59c4f-557">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="59c4f-558">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): boştaki bir bağlantı için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="59c4f-558">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="59c4f-559">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): yanıt için en düşük gönderme hızı.</span><span class="sxs-lookup"><span data-stu-id="59c4f-559">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="59c4f-560">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): isteğin, uygulamanın onu seçmeden önce istek kuyruğunda kalması için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="59c4f-560">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="59c4f-561"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>HTTP.sys kaydolmak için öğesini belirtin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-561">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="59c4f-562">En yararlı olan [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), koleksiyona bir ön ek eklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-562">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="59c4f-563">Bunlar, dinleyici elden atılıyor öncesinde herhangi bir zamanda değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-563">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="59c4f-564">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="59c4f-564">**MaxRequestBodySize**</span></span>

<span data-ttu-id="59c4f-565">Herhangi bir istek gövdesinin bayt olarak izin verilen en büyük boyutu.</span><span class="sxs-lookup"><span data-stu-id="59c4f-565">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="59c4f-566">Olarak ayarlandığında `null` , en büyük istek gövdesi boyutu sınırsızdır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-566">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="59c4f-567">Bu sınırın, her zaman sınırsız olan yükseltilmiş bağlantıları üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-567">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="59c4f-568">Tek bir ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yöntem, `IActionResult` <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="59c4f-568">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="59c4f-569">Uygulama isteği okumayı başlattıktan sonra bir istek üzerindeki sınırı yapılandırmayı denerse bir özel durum oluşur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-569">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="59c4f-570">Özelliğin `IsReadOnly` salt okuma durumunda olup olmadığını göstermek için bir özellik kullanılabilir `MaxRequestBodySize` , yani sınırı yapılandırmak için çok geç.</span><span class="sxs-lookup"><span data-stu-id="59c4f-570">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="59c4f-571">Uygulamanın istek başına geçersiz kılması gerekiyorsa <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> , şunu kullanın <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :</span><span class="sxs-lookup"><span data-stu-id="59c4f-571">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="59c4f-572">Visual Studio kullanıyorsanız, uygulamanın IIS veya IIS Express çalıştıracak şekilde yapılandırılmadığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="59c4f-572">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="59c4f-573">Visual Studio 'da varsayılan başlatma profili IIS Express içindir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-573">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="59c4f-574">Projeyi konsol uygulaması olarak çalıştırmak için, aşağıdaki ekran görüntüsünde gösterildiği gibi seçili profili el ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="59c4f-574">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![Konsol uygulaması profilini seçin](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="59c4f-576">Windows Server 'ı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="59c4f-576">Configure Windows Server</span></span>

1. <span data-ttu-id="59c4f-577">Uygulama için açılacak bağlantı noktalarını belirleme ve [Windows Güvenlik Duvarı](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 'Nı veya [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 'ini kullanarak trafiğin HTTP.sys ulaşmasını sağlamak üzere güvenlik duvarı bağlantı noktalarını açın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-577">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="59c4f-578">Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-578">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="59c4f-579">Bir Azure sanal makinesine dağıtım yaparken, [ağ güvenlik grubundaki](/azure/virtual-machines/windows/nsg-quickstart-portal)bağlantı noktalarını açın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-579">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="59c4f-580">Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-580">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="59c4f-581">Gerekirse, X. 509.440 sertifikalarını edinin ve yükler.</span><span class="sxs-lookup"><span data-stu-id="59c4f-581">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="59c4f-582">Windows 'da, [New-SelfSignedCertificate PowerShell cmdlet 'ini](/powershell/module/pkiclient/new-selfsignedcertificate)kullanarak otomatik olarak imzalanan sertifikalar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="59c4f-582">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="59c4f-583">Desteklenmeyen bir örnek için bkz. [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span><span class="sxs-lookup"><span data-stu-id="59c4f-583">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="59c4f-584">Sunucunun **yerel makine** > **Kişisel** deposunda otomatik olarak imzalanan veya CA imzalı sertifikalar yükler.</span><span class="sxs-lookup"><span data-stu-id="59c4f-584">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="59c4f-585">Uygulama [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise, .net core, .NET Framework veya her ikisini de (uygulama .NET Framework hedefleyen bir .NET Core uygulaması ise) yükler.</span><span class="sxs-lookup"><span data-stu-id="59c4f-585">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="59c4f-586">**.NET Core**: uygulama .NET Core gerektiriyorsa .NET Core [Indirmelerinde](https://dotnet.microsoft.com/download) **.NET Core çalışma zamanı** yükleyicisini edinip çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-586">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="59c4f-587">Tam SDK 'Yı sunucuya yüklemeyin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-587">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="59c4f-588">**.NET Framework**: uygulama .NET Framework gerektiriyorsa [.NET Framework yükleme kılavuzuna](/dotnet/framework/install/)bakın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-588">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="59c4f-589">Gerekli .NET Framework yüklemesi.</span><span class="sxs-lookup"><span data-stu-id="59c4f-589">Install the required .NET Framework.</span></span> <span data-ttu-id="59c4f-590">En son .NET Framework yükleyicisi [.NET Core İndirmeleri](https://dotnet.microsoft.com/download) sayfasından edinilebilir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-590">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="59c4f-591">Uygulama, [kendi içinde bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise, uygulama çalışma zamanını dağıtımda içerir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-591">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="59c4f-592">Sunucuda çerçeve yüklemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="59c4f-592">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="59c4f-593">Uygulamada URL 'Leri ve bağlantı noktalarını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-593">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="59c4f-594">Varsayılan olarak, ASP.NET Core öğesine bağlanır `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-594">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="59c4f-595">URL öneklerini ve bağlantı noktalarını yapılandırmak için seçenekler şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="59c4f-595">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="59c4f-596">`urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="59c4f-596">`urls` command-line argument</span></span>
   * <span data-ttu-id="59c4f-597">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="59c4f-597">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="59c4f-598">Aşağıdaki kod örneği, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> `10.0.0.4` 443 numaralı bağlantı noktasında sunucunun yerel IP adresi ile nasıl kullanılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="59c4f-598">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="59c4f-599">Avantajı `UrlPrefixes` , hatalı biçimli ön ekler için hemen bir hata iletisi oluşturulmasından oluşur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-599">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="59c4f-600">Ayarları `UrlPrefixes` geçersiz kıl `UseUrls` / `urls` / `ASPNETCORE_URLS` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-600">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="59c4f-601">Bu nedenle, `UseUrls` `urls` ve `ASPNETCORE_URLS` ortam değişkeninin avantajı Kestrel ve HTTP.sys arasında geçiş yapmak daha kolay bir hale gelir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-601">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="59c4f-602">HTTP.sys, [http sunucusu API UrlPrefix dize biçimlerini](/windows/win32/http/urlprefix-strings)kullanır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-602">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="59c4f-603">Üst düzey joker karakter bağlamaları ( `http://*:80/` ve `http://+:80` ) kullanılmamalıdır **not** .</span><span class="sxs-lookup"><span data-stu-id="59c4f-603">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="59c4f-604">Üst düzey joker karakter bağlamaları uygulama güvenliği güvenlik açıklarını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-604">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="59c4f-605">Bu hem güçlü hem de zayıf Joker karakterlere yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-605">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="59c4f-606">Joker karakterler yerine açık konak adlarını veya IP adreslerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-606">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="59c4f-607">Alt etki alanı joker bağlantısı (örneğin, `*.mysub.com` ), tüm üst etki alanını ( `*.com` Bu güvenlik açığı olan aksine) kontrol ediyorsanız bir güvenlik riski değildir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-607">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="59c4f-608">Daha fazla bilgi için bkz. [RFC 7230: bölüm 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="59c4f-608">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="59c4f-609">Ön ek, sunucudaki URL öneklerini ister.</span><span class="sxs-lookup"><span data-stu-id="59c4f-609">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="59c4f-610">HTTP.sys yapılandırmaya yönelik yerleşik araç *netsh.exe*.</span><span class="sxs-lookup"><span data-stu-id="59c4f-610">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="59c4f-611">*netsh.exe* , URL öneklerini ayırmak ve X. 509.440 sertifikaları atamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-611">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="59c4f-612">Araç, yönetici ayrıcalıkları gerektirir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-612">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="59c4f-613">Uygulamanın URL 'Lerini kaydetmek için *netsh.exe* aracını kullanın:</span><span class="sxs-lookup"><span data-stu-id="59c4f-613">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="59c4f-614">`<URL>`: Tam nitelikli Tekdüzen Kaynak Bulucu (URL).</span><span class="sxs-lookup"><span data-stu-id="59c4f-614">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="59c4f-615">Joker karakter bağlama kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-615">Don't use a wildcard binding.</span></span> <span data-ttu-id="59c4f-616">Geçerli bir ana bilgisayar adı veya yerel IP adresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-616">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="59c4f-617">*URL, sondaki eğik çizgi içermelidir.*</span><span class="sxs-lookup"><span data-stu-id="59c4f-617">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="59c4f-618">`<USER>`: Kullanıcı veya Kullanıcı grubu adını belirtir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-618">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="59c4f-619">Aşağıdaki örnekte sunucusunun yerel IP adresi `10.0.0.4` :</span><span class="sxs-lookup"><span data-stu-id="59c4f-619">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="59c4f-620">Bir URL kaydedildiğinde, araç ile yanıt verir `URL reservation successfully added` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-620">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="59c4f-621">Kayıtlı bir URL 'yi silmek için şu `delete urlacl` komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="59c4f-621">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="59c4f-622">X. 509.440 sertifikalarını sunucusuna kaydedin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-622">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="59c4f-623">Uygulamanın sertifikalarını kaydetmek için *netsh.exe* aracını kullanın:</span><span class="sxs-lookup"><span data-stu-id="59c4f-623">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="59c4f-624">`<IP>`: Bağlama için yerel IP adresini belirtir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-624">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="59c4f-625">Joker karakter bağlama kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-625">Don't use a wildcard binding.</span></span> <span data-ttu-id="59c4f-626">Geçerli bir IP adresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-626">Use a valid IP address.</span></span>
   * <span data-ttu-id="59c4f-627">`<PORT>`: Bağlama için bağlantı noktasını belirtir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-627">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="59c4f-628">`<THUMBPRINT>`: X. 509.440 sertifika parmak izi.</span><span class="sxs-lookup"><span data-stu-id="59c4f-628">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="59c4f-629">`<GUID>`: Uygulamayı bilgilendirme amacıyla temsil eden geliştirici tarafından oluşturulan bir GUID.</span><span class="sxs-lookup"><span data-stu-id="59c4f-629">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="59c4f-630">Başvuru amacıyla, GUID 'yi uygulamada bir paket etiketi olarak depolayın:</span><span class="sxs-lookup"><span data-stu-id="59c4f-630">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="59c4f-631">Visual Studio 'da:</span><span class="sxs-lookup"><span data-stu-id="59c4f-631">In Visual Studio:</span></span>
     * <span data-ttu-id="59c4f-632">**Çözüm Gezgini** ' de uygulamaya sağ tıklayıp **Özellikler**' i seçerek uygulamanın proje özelliklerini açın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-632">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="59c4f-633">**Paket** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-633">Select the **Package** tab.</span></span>
     * <span data-ttu-id="59c4f-634">**Etiketler** alanında oluşturduğunuz GUID 'yi girin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-634">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="59c4f-635">Visual Studio kullanmadığınız durumlarda:</span><span class="sxs-lookup"><span data-stu-id="59c4f-635">When not using Visual Studio:</span></span>
     * <span data-ttu-id="59c4f-636">Uygulamanın proje dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-636">Open the app's project file.</span></span>
     * <span data-ttu-id="59c4f-637">`<PackageTags>`Oluşturduğunuz GUID ile yeni veya var olan bir özelliği ekleyin `<PropertyGroup>` :</span><span class="sxs-lookup"><span data-stu-id="59c4f-637">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="59c4f-638">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="59c4f-638">In the following example:</span></span>

   * <span data-ttu-id="59c4f-639">Sunucunun yerel IP adresi `10.0.0.4` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-639">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="59c4f-640">Bir çevrimiçi rastgele GUID Oluşturucu değeri sağlar `appid` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-640">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="59c4f-641">Bir sertifika kaydedildiğinde, araç ile yanıt verir `SSL Certificate successfully added` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-641">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="59c4f-642">Bir sertifika kaydını silmek için şu komutu kullanın `delete sslcert` :</span><span class="sxs-lookup"><span data-stu-id="59c4f-642">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="59c4f-643">*netsh.exe*için başvuru belgeleri:</span><span class="sxs-lookup"><span data-stu-id="59c4f-643">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="59c4f-644">[Köprü Metni Aktarım Protokolü (HTTP) için Netsh komutları](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="59c4f-644">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="59c4f-645">UrlPrefix dizeleri</span><span class="sxs-lookup"><span data-stu-id="59c4f-645">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="59c4f-646">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-646">Run the app.</span></span>

   <span data-ttu-id="59c4f-647">1024 'den büyük bir bağlantı noktası numarası ile HTTP (HTTPS değil) kullanarak localhost 'a bağlanırken uygulamayı çalıştırmak için yönetici ayrıcalıklarına gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-647">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="59c4f-648">Diğer yapılandırmalarda (örneğin, yerel bir IP adresi veya 443 numaralı bağlantı noktasına bağlama), uygulamayı yönetici ayrıcalıklarıyla çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-648">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="59c4f-649">Uygulama, sunucunun genel IP adresinde yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-649">The app responds at the server's public IP address.</span></span> <span data-ttu-id="59c4f-650">Bu örnekte, sunucusuna, genel IP adresinde Internet 'ten ulaşılırsa `104.214.79.47` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-650">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="59c4f-651">Bu örnekte bir geliştirme sertifikası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-651">A development certificate is used in this example.</span></span> <span data-ttu-id="59c4f-652">Tarayıcının güvenilmeyen sertifika uyarısı atlandıktan sonra sayfa güvenli bir şekilde yüklenir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-652">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![Uygulamanın dizin sayfasını yüklü olarak gösteren tarayıcı penceresi](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="59c4f-654">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="59c4f-654">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="59c4f-655">Internet veya şirket ağından gelen isteklerle etkileşime geçen HTTP.sys tarafından barındırılan uygulamalar için, proxy sunucularının ve yük dengeleyiciler arkasında barındırılırken ek yapılandırma gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-655">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="59c4f-656">Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="59c4f-656">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="59c4f-657">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="59c4f-657">Additional resources</span></span>

* [<span data-ttu-id="59c4f-658">HTTP.sysile Windows kimlik doğrulamasını etkinleştir </span><span class="sxs-lookup"><span data-stu-id="59c4f-658">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="59c4f-659">HTTP Sunucusu API 'SI</span><span class="sxs-lookup"><span data-stu-id="59c4f-659">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="59c4f-660">ASPNET/HttpSysServer GitHub deposu (kaynak kodu)</span><span class="sxs-lookup"><span data-stu-id="59c4f-660">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="59c4f-661">Ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="59c4f-661">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="59c4f-662">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) , yalnızca Windows üzerinde çalışan [ASP.NET Core için bir Web sunucusudur](xref:fundamentals/servers/index) .</span><span class="sxs-lookup"><span data-stu-id="59c4f-662">[HTTP.sys](/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#hypertext-transfer-protocol-stack-httpsys) is a [web server for ASP.NET Core](xref:fundamentals/servers/index) that only runs on Windows.</span></span> <span data-ttu-id="59c4f-663">HTTP.sys, [Kestrel](xref:fundamentals/servers/kestrel) Server için bir alternatiftir ve Kestrel tarafından sağlamayan bazı özellikler sunar.</span><span class="sxs-lookup"><span data-stu-id="59c4f-663">HTTP.sys is an alternative to [Kestrel](xref:fundamentals/servers/kestrel) server and offers some features that Kestrel doesn't provide.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="59c4f-664">HTTP.sys [ASP.NET Core modülüyle](xref:host-and-deploy/aspnet-core-module) uyumlu DEĞILDIR ve ııs veya IIS Express ile kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="59c4f-664">HTTP.sys isn't compatible with the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) and can't be used with IIS or IIS Express.</span></span>

<span data-ttu-id="59c4f-665">HTTP.sys aşağıdaki özellikleri destekler:</span><span class="sxs-lookup"><span data-stu-id="59c4f-665">HTTP.sys supports the following features:</span></span>

* [<span data-ttu-id="59c4f-666">Windows Kimlik Doğrulaması</span><span class="sxs-lookup"><span data-stu-id="59c4f-666">Windows Authentication</span></span>](xref:security/authentication/windowsauth)
* <span data-ttu-id="59c4f-667">Bağlantı noktası Paylaşımı</span><span class="sxs-lookup"><span data-stu-id="59c4f-667">Port sharing</span></span>
* <span data-ttu-id="59c4f-668">SNı ile HTTPS</span><span class="sxs-lookup"><span data-stu-id="59c4f-668">HTTPS with SNI</span></span>
* <span data-ttu-id="59c4f-669">TLS üzerinden HTTP/2 (Windows 10 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="59c4f-669">HTTP/2 over TLS (Windows 10 or later)</span></span>
* <span data-ttu-id="59c4f-670">Doğrudan dosya iletimi</span><span class="sxs-lookup"><span data-stu-id="59c4f-670">Direct file transmission</span></span>
* <span data-ttu-id="59c4f-671">Yanıtları Önbelleğe Alma</span><span class="sxs-lookup"><span data-stu-id="59c4f-671">Response caching</span></span>
* <span data-ttu-id="59c4f-672">WebSockets (Windows 8 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="59c4f-672">WebSockets (Windows 8 or later)</span></span>

<span data-ttu-id="59c4f-673">Desteklenen Windows sürümleri:</span><span class="sxs-lookup"><span data-stu-id="59c4f-673">Supported Windows versions:</span></span>

* <span data-ttu-id="59c4f-674">Windows 7 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="59c4f-674">Windows 7 or later</span></span>
* <span data-ttu-id="59c4f-675">Windows Server 2008 R2 veya sonraki sürümü</span><span class="sxs-lookup"><span data-stu-id="59c4f-675">Windows Server 2008 R2 or later</span></span>

<span data-ttu-id="59c4f-676">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="59c4f-676">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/httpsys/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-httpsys"></a><span data-ttu-id="59c4f-677">Ne zaman kullanılacağı HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="59c4f-677">When to use HTTP.sys</span></span>

<span data-ttu-id="59c4f-678">HTTP.sys, şu yerlerde olan dağıtımlar için yararlıdır:</span><span class="sxs-lookup"><span data-stu-id="59c4f-678">HTTP.sys is useful for deployments where:</span></span>

* <span data-ttu-id="59c4f-679">Sunucuyu IIS kullanmadan doğrudan Internet 'e sunmaya gerek vardır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-679">There's a need to expose the server directly to the Internet without using IIS.</span></span>

  ![HTTP.sys doğrudan Internet ile iletişim kurar](httpsys/_static/httpsys-to-internet.png)

* <span data-ttu-id="59c4f-681">İç dağıtım, [Windows kimlik doğrulaması](xref:security/authentication/windowsauth)gibi Kestrel 'de kullanılamayan bir özelliği gerektirir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-681">An internal deployment requires a feature not available in Kestrel, such as [Windows Authentication](xref:security/authentication/windowsauth).</span></span>

  ![HTTP.sys doğrudan iç ağla iletişim kurar](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="59c4f-683">HTTP.sys, birçok saldırı türüne karşı koruyan ve tam özellikli bir Web sunucusu için sağlamlık, güvenlik ve ölçeklenebilirlik sağlayan çok sayıda teknolojiden oluşur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-683">HTTP.sys is mature technology that protects against many types of attacks and provides the robustness, security, and scalability of a full-featured web server.</span></span> <span data-ttu-id="59c4f-684">IIS, HTTP.sys en üstünde bir HTTP dinleyicisi olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-684">IIS itself runs as an HTTP listener on top of HTTP.sys.</span></span>

## <a name="http2-support"></a><span data-ttu-id="59c4f-685">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="59c4f-685">HTTP/2 support</span></span>

<span data-ttu-id="59c4f-686">Aşağıdaki temel gereksinimler karşılanıyorsa ASP.NET Core uygulamalar için [http/2](https://httpwg.org/specs/rfc7540.html) etkinleştirilir:</span><span class="sxs-lookup"><span data-stu-id="59c4f-686">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is enabled for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="59c4f-687">Windows Server 2016/Windows 10 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="59c4f-687">Windows Server 2016/Windows 10 or later</span></span>
* <span data-ttu-id="59c4f-688">[Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı</span><span class="sxs-lookup"><span data-stu-id="59c4f-688">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="59c4f-689">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="59c4f-689">TLS 1.2 or later connection</span></span>

<span data-ttu-id="59c4f-690">Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/1.1` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-690">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/1.1`.</span></span>

<span data-ttu-id="59c4f-691">HTTP/2 varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-691">HTTP/2 is enabled by default.</span></span> <span data-ttu-id="59c4f-692">HTTP/2 bağlantısı kurulmadıysa, bağlantı HTTP/1.1 'ye geri döner.</span><span class="sxs-lookup"><span data-stu-id="59c4f-692">If an HTTP/2 connection isn't established, the connection falls back to HTTP/1.1.</span></span> <span data-ttu-id="59c4f-693">Windows 'un gelecek bir sürümünde http/2 yapılandırma bayrakları, HTTP.sys ile HTTP/2 devre dışı bırakma özelliği de dahil olmak üzere kullanılabilir olacaktır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-693">In a future release of Windows, HTTP/2 configuration flags will be available, including the ability to disable HTTP/2 with HTTP.sys.</span></span>

## <a name="kernel-mode-authentication-with-kerberos"></a><span data-ttu-id="59c4f-694">Kerberos ile çekirdek modu kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="59c4f-694">Kernel mode authentication with Kerberos</span></span>

<span data-ttu-id="59c4f-695">Kerberos kimlik doğrulama protokolü ile çekirdek modu kimlik doğrulamasına temsilciler HTTP.sys.</span><span class="sxs-lookup"><span data-stu-id="59c4f-695">HTTP.sys delegates to kernel mode authentication with the Kerberos authentication protocol.</span></span> <span data-ttu-id="59c4f-696">Kullanıcı modu kimlik doğrulaması, Kerberos ve HTTP.sys desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="59c4f-696">User mode authentication isn't supported with Kerberos and HTTP.sys.</span></span> <span data-ttu-id="59c4f-697">Makine hesabı, Active Directory alındığı ve kullanıcının kimliğini doğrulamak için istemci tarafından sunucuya iletilen Kerberos belirtecinin/biletinin şifresini çözmek için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-697">The machine account must be used to decrypt the Kerberos token/ticket that's obtained from Active Directory and forwarded by the client to the server to authenticate the user.</span></span> <span data-ttu-id="59c4f-698">Uygulamanın kullanıcısına değil, ana bilgisayar için hizmet asıl adını (SPN) kaydedin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-698">Register the Service Principal Name (SPN) for the host, not the user of the app.</span></span>

## <a name="how-to-use-httpsys"></a><span data-ttu-id="59c4f-699">HTTP.sys kullanma</span><span class="sxs-lookup"><span data-stu-id="59c4f-699">How to use HTTP.sys</span></span>

### <a name="configure-the-aspnet-core-app-to-use-httpsys"></a><span data-ttu-id="59c4f-700">ASP.NET Core uygulamasını kullanacak şekilde yapılandırma HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="59c4f-700">Configure the ASP.NET Core app to use HTTP.sys</span></span>

<span data-ttu-id="59c4f-701">[Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app) ([NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)) kullanılırken proje dosyasındaki bir paket başvurusu gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-701">A package reference in the project file isn't required when using the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) ([nuget.org](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)).</span></span> <span data-ttu-id="59c4f-702">`Microsoft.AspNetCore.App`Metapackage 'i kullanmıyorsanız [Microsoft. Aspnetcore. Server. httpsys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)öğesine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-702">When not using the `Microsoft.AspNetCore.App` metapackage, add a package reference to [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/).</span></span>

<span data-ttu-id="59c4f-703"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*>Ana bilgisayarı oluştururken, gerekli herhangi bir yöntemi belirterek uzantı yöntemini çağırın <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions> .</span><span class="sxs-lookup"><span data-stu-id="59c4f-703">Call the <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderHttpSysExtensions.UseHttpSys*> extension method when building the host, specifying any required <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions>.</span></span> <span data-ttu-id="59c4f-704">Aşağıdaki örnek, seçeneklerini varsayılan değerlerine ayarlar:</span><span class="sxs-lookup"><span data-stu-id="59c4f-704">The following example sets options to their default values:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Program.cs?name=snippet1&highlight=4-12)]

<span data-ttu-id="59c4f-705">Ek HTTP.sys yapılandırması, [kayıt defteri ayarları](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows)aracılığıyla işlenir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-705">Additional HTTP.sys configuration is handled through [registry settings](https://support.microsoft.com/help/820129/http-sys-registry-settings-for-windows).</span></span>

<span data-ttu-id="59c4f-706">**HTTP.sys seçenekleri**</span><span class="sxs-lookup"><span data-stu-id="59c4f-706">**HTTP.sys options**</span></span>

| <span data-ttu-id="59c4f-707">Özellik</span><span class="sxs-lookup"><span data-stu-id="59c4f-707">Property</span></span> | <span data-ttu-id="59c4f-708">Açıklama</span><span class="sxs-lookup"><span data-stu-id="59c4f-708">Description</span></span> | <span data-ttu-id="59c4f-709">Varsayılan</span><span class="sxs-lookup"><span data-stu-id="59c4f-709">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="59c4f-710">AllowSynchronousIO</span><span class="sxs-lookup"><span data-stu-id="59c4f-710">AllowSynchronousIO</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.AllowSynchronousIO) | <span data-ttu-id="59c4f-711">Ve için zaman uyumlu giriş/çıkışa izin verilip verilmeyeceğini `HttpContext.Request.Body` denetleyin `HttpContext.Response.Body` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-711">Control whether synchronous input/output is allowed for the `HttpContext.Request.Body` and `HttpContext.Response.Body`.</span></span> | `true` |
| [<span data-ttu-id="59c4f-712">Authentication. AllowAnonymous</span><span class="sxs-lookup"><span data-stu-id="59c4f-712">Authentication.AllowAnonymous</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.AllowAnonymous) | <span data-ttu-id="59c4f-713">Anonim isteklere izin verin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-713">Allow anonymous requests.</span></span> | `true` |
| [<span data-ttu-id="59c4f-714">Authentication. düzenleri</span><span class="sxs-lookup"><span data-stu-id="59c4f-714">Authentication.Schemes</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationManager.Schemes) | <span data-ttu-id="59c4f-715">İzin verilen kimlik doğrulama düzenlerini belirtin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-715">Specify the allowed authentication schemes.</span></span> <span data-ttu-id="59c4f-716">Dinleyici elden atılırken önce herhangi bir zamanda değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-716">May be modified at any time prior to disposing the listener.</span></span> <span data-ttu-id="59c4f-717">Değerler [authenticationdüzenlerinin numaralandırması](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes)tarafından sağlanır: `Basic` ,,, `Kerberos` `Negotiate` `None` , ve `NTLM` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-717">Values are provided by the [AuthenticationSchemes enum](xref:Microsoft.AspNetCore.Server.HttpSys.AuthenticationSchemes): `Basic`, `Kerberos`, `Negotiate`, `None`, and `NTLM`.</span></span> | `None` |
| [<span data-ttu-id="59c4f-718">EnableResponseCaching</span><span class="sxs-lookup"><span data-stu-id="59c4f-718">EnableResponseCaching</span></span>](xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.EnableResponseCaching) | <span data-ttu-id="59c4f-719">Uygun üst bilgileri içeren yanıtlar için [çekirdek modu](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) önbelleğe almayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-719">Attempt [kernel-mode](/windows-hardware/drivers/gettingstarted/user-mode-and-kernel-mode) caching for responses with eligible headers.</span></span> <span data-ttu-id="59c4f-720">Yanıt `Set-Cookie` , `Vary` veya `Pragma` üst bilgileri içermeyebilir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-720">The response may not include `Set-Cookie`, `Vary`, or `Pragma` headers.</span></span> <span data-ttu-id="59c4f-721">`Cache-Control` `public` Bir ya da değeri ya da bir üst bilgi içermelidir `shared-max-age` `max-age` `Expires` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-721">It must include a `Cache-Control` header that's `public` and either a `shared-max-age` or `max-age` value, or an `Expires` header.</span></span> | `true` |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxAccepts> | <span data-ttu-id="59c4f-722">En fazla eşzamanlı kabul sayısı.</span><span class="sxs-lookup"><span data-stu-id="59c4f-722">The maximum number of concurrent accepts.</span></span> | <span data-ttu-id="59c4f-723">5 &times; [ortam. <br> ProcessorCount](xref:System.Environment.ProcessorCount)</span><span class="sxs-lookup"><span data-stu-id="59c4f-723">5 &times; [Environment.<br>ProcessorCount](xref:System.Environment.ProcessorCount)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxConnections> | <span data-ttu-id="59c4f-724">Kabul edilecek eşzamanlı bağlantı sayısı üst sınırı.</span><span class="sxs-lookup"><span data-stu-id="59c4f-724">The maximum number of concurrent connections to accept.</span></span> <span data-ttu-id="59c4f-725">`-1`Sonsuz için kullanın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-725">Use `-1` for infinite.</span></span> <span data-ttu-id="59c4f-726">`null`Kayıt defterinin makine genelindeki ayarını kullanmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-726">Use `null` to use the registry's machine-wide setting.</span></span> | `null`<br><span data-ttu-id="59c4f-727">(makine genelinde</span><span class="sxs-lookup"><span data-stu-id="59c4f-727">(machine-wide</span></span><br><span data-ttu-id="59c4f-728">ayarlanmasını</span><span class="sxs-lookup"><span data-stu-id="59c4f-728">setting)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> | <span data-ttu-id="59c4f-729"><a href="#maxrequestbodysize">MaxRequestBodySize</a> bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-729">See the <a href="#maxrequestbodysize">MaxRequestBodySize</a> section.</span></span> | <span data-ttu-id="59c4f-730">30000000 bayt</span><span class="sxs-lookup"><span data-stu-id="59c4f-730">30000000 bytes</span></span><br><span data-ttu-id="59c4f-731">(~ 28,6 MB)</span><span class="sxs-lookup"><span data-stu-id="59c4f-731">(~28.6 MB)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.RequestQueueLimit> | <span data-ttu-id="59c4f-732">Sıraya alınabilen en fazla istek sayısı.</span><span class="sxs-lookup"><span data-stu-id="59c4f-732">The maximum number of requests that can be queued.</span></span> | <span data-ttu-id="59c4f-733">1000</span><span class="sxs-lookup"><span data-stu-id="59c4f-733">1000</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.ThrowWriteExceptions> | <span data-ttu-id="59c4f-734">İstemci bağlantısının kesilmesinden kaynaklanan yanıt gövdesi yazmasının, özel durumlar oluşturması veya normal şekilde tamamlanması gerektiğini belirtin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-734">Indicate if response body writes that fail due to client disconnects should throw exceptions or complete normally.</span></span> | `false`<br><span data-ttu-id="59c4f-735">(normal olarak)</span><span class="sxs-lookup"><span data-stu-id="59c4f-735">(complete normally)</span></span> |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.Timeouts> | <span data-ttu-id="59c4f-736"><xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager>Kayıt defterinde da yapılandırılabilen HTTP.sys yapılandırmasını kullanıma sunun.</span><span class="sxs-lookup"><span data-stu-id="59c4f-736">Expose the HTTP.sys <xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager> configuration, which may also be configured in the registry.</span></span> <span data-ttu-id="59c4f-737">Varsayılan değerler de dahil olmak üzere her bir ayar hakkında daha fazla bilgi edinmek için API bağlantılarını izleyin:</span><span class="sxs-lookup"><span data-stu-id="59c4f-737">Follow the API links to learn more about each setting, including default values:</span></span><ul><li><span data-ttu-id="59c4f-738">[TimeoutManager. DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): http sunucusu API 'sinin varlık gövdesini etkin tut bağlantısı üzerinde boşaltmasına izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="59c4f-738">[TimeoutManager.DrainEntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.DrainEntityBody): Time allowed for the HTTP Server API to drain the entity body on a Keep-Alive connection.</span></span></li><li><span data-ttu-id="59c4f-739">[TimeoutManager. EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): istek varlığı gövdesinin gelmesi için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="59c4f-739">[TimeoutManager.EntityBody](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.EntityBody): Time allowed for the request entity body to arrive.</span></span></li><li><span data-ttu-id="59c4f-740">[TimeoutManager. HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): http sunucusu API 'sinin istek üst bilgisini ayrıştırması için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="59c4f-740">[TimeoutManager.HeaderWait](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.HeaderWait): Time allowed for the HTTP Server API to parse the request header.</span></span></li><li><span data-ttu-id="59c4f-741">[TimeoutManager. IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): boştaki bir bağlantı için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="59c4f-741">[TimeoutManager.IdleConnection](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.IdleConnection): Time allowed for an idle connection.</span></span></li><li><span data-ttu-id="59c4f-742">[TimeoutManager. MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): yanıt için en düşük gönderme hızı.</span><span class="sxs-lookup"><span data-stu-id="59c4f-742">[TimeoutManager.MinSendBytesPerSecond](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.MinSendBytesPerSecond): The minimum send rate for the response.</span></span></li><li><span data-ttu-id="59c4f-743">[TimeoutManager. RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): isteğin, uygulamanın onu seçmeden önce istek kuyruğunda kalması için izin verilen süre.</span><span class="sxs-lookup"><span data-stu-id="59c4f-743">[TimeoutManager.RequestQueue](xref:Microsoft.AspNetCore.Server.HttpSys.TimeoutManager.RequestQueue): Time allowed for the request to remain in the request queue before the app picks it up.</span></span></li></ul> |  |
| <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> | <span data-ttu-id="59c4f-744"><xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection>HTTP.sys kaydolmak için öğesini belirtin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-744">Specify the <xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection> to register with HTTP.sys.</span></span> <span data-ttu-id="59c4f-745">En yararlı olan [UrlPrefixCollection. Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), koleksiyona bir ön ek eklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-745">The most useful is [UrlPrefixCollection.Add](xref:Microsoft.AspNetCore.Server.HttpSys.UrlPrefixCollection.Add*), which is used to add a prefix to the collection.</span></span> <span data-ttu-id="59c4f-746">Bunlar, dinleyici elden atılıyor öncesinde herhangi bir zamanda değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-746">These may be modified at any time prior to disposing the listener.</span></span> |  |

<a name="maxrequestbodysize"></a>

<span data-ttu-id="59c4f-747">**MaxRequestBodySize**</span><span class="sxs-lookup"><span data-stu-id="59c4f-747">**MaxRequestBodySize**</span></span>

<span data-ttu-id="59c4f-748">Herhangi bir istek gövdesinin bayt olarak izin verilen en büyük boyutu.</span><span class="sxs-lookup"><span data-stu-id="59c4f-748">The maximum allowed size of any request body in bytes.</span></span> <span data-ttu-id="59c4f-749">Olarak ayarlandığında `null` , en büyük istek gövdesi boyutu sınırsızdır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-749">When set to `null`, the maximum request body size is unlimited.</span></span> <span data-ttu-id="59c4f-750">Bu sınırın, her zaman sınırsız olan yükseltilmiş bağlantıları üzerinde hiçbir etkisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-750">This limit has no effect on upgraded connections, which are always unlimited.</span></span>

<span data-ttu-id="59c4f-751">Tek bir ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yöntem, `IActionResult` <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="59c4f-751">The recommended method to override the limit in an ASP.NET Core MVC app for a single `IActionResult` is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="59c4f-752">Uygulama isteği okumayı başlattıktan sonra bir istek üzerindeki sınırı yapılandırmayı denerse bir özel durum oluşur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-752">An exception is thrown if the app attempts to configure the limit on a request after the app has started reading the request.</span></span> <span data-ttu-id="59c4f-753">Özelliğin `IsReadOnly` salt okuma durumunda olup olmadığını göstermek için bir özellik kullanılabilir `MaxRequestBodySize` , yani sınırı yapılandırmak için çok geç.</span><span class="sxs-lookup"><span data-stu-id="59c4f-753">An `IsReadOnly` property can be used to indicate if the `MaxRequestBodySize` property is in a read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="59c4f-754">Uygulamanın istek başına geçersiz kılması gerekiyorsa <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> , şunu kullanın <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature> :</span><span class="sxs-lookup"><span data-stu-id="59c4f-754">If the app should override <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.MaxRequestBodySize> per-request, use the <xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>:</span></span>

[!code-csharp[](httpsys/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=6-7)]

<span data-ttu-id="59c4f-755">Visual Studio kullanıyorsanız, uygulamanın IIS veya IIS Express çalıştıracak şekilde yapılandırılmadığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="59c4f-755">If using Visual Studio, make sure the app isn't configured to run IIS or IIS Express.</span></span>

<span data-ttu-id="59c4f-756">Visual Studio 'da varsayılan başlatma profili IIS Express içindir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-756">In Visual Studio, the default launch profile is for IIS Express.</span></span> <span data-ttu-id="59c4f-757">Projeyi konsol uygulaması olarak çalıştırmak için, aşağıdaki ekran görüntüsünde gösterildiği gibi seçili profili el ile değiştirin:</span><span class="sxs-lookup"><span data-stu-id="59c4f-757">To run the project as a console app, manually change the selected profile, as shown in the following screen shot:</span></span>

![Konsol uygulaması profilini seçin](httpsys/_static/vs-choose-profile.png)

### <a name="configure-windows-server"></a><span data-ttu-id="59c4f-759">Windows Server 'ı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="59c4f-759">Configure Windows Server</span></span>

1. <span data-ttu-id="59c4f-760">Uygulama için açılacak bağlantı noktalarını belirleme ve [Windows Güvenlik Duvarı](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) 'Nı veya [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet 'ini kullanarak trafiğin HTTP.sys ulaşmasını sağlamak üzere güvenlik duvarı bağlantı noktalarını açın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-760">Determine the ports to open for the app and use [Windows Firewall](/windows/security/threat-protection/windows-firewall/create-an-inbound-port-rule) or the [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule) PowerShell cmdlet to open firewall ports to allow traffic to reach HTTP.sys.</span></span> <span data-ttu-id="59c4f-761">Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-761">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="59c4f-762">Bir Azure sanal makinesine dağıtım yaparken, [ağ güvenlik grubundaki](/azure/virtual-machines/windows/nsg-quickstart-portal)bağlantı noktalarını açın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-762">When deploying to an Azure VM, open the ports in the [Network Security Group](/azure/virtual-machines/windows/nsg-quickstart-portal).</span></span> <span data-ttu-id="59c4f-763">Aşağıdaki komutlarda ve uygulama yapılandırmasında, 443 numaralı bağlantı noktası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-763">In the following commands and app configuration, port 443 is used.</span></span>

1. <span data-ttu-id="59c4f-764">Gerekirse, X. 509.440 sertifikalarını edinin ve yükler.</span><span class="sxs-lookup"><span data-stu-id="59c4f-764">Obtain and install X.509 certificates, if required.</span></span>

   <span data-ttu-id="59c4f-765">Windows 'da, [New-SelfSignedCertificate PowerShell cmdlet 'ini](/powershell/module/pkiclient/new-selfsignedcertificate)kullanarak otomatik olarak imzalanan sertifikalar oluşturun.</span><span class="sxs-lookup"><span data-stu-id="59c4f-765">On Windows, create self-signed certificates using the [New-SelfSignedCertificate PowerShell cmdlet](/powershell/module/pkiclient/new-selfsignedcertificate).</span></span> <span data-ttu-id="59c4f-766">Desteklenmeyen bir örnek için bkz. [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span><span class="sxs-lookup"><span data-stu-id="59c4f-766">For an unsupported example, see [UpdateIISExpressSSLForChrome.ps1](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/includes/make-x509-cert/UpdateIISExpressSSLForChrome.ps1).</span></span>

   <span data-ttu-id="59c4f-767">Sunucunun **yerel makine** > **Kişisel** deposunda otomatik olarak imzalanan veya CA imzalı sertifikalar yükler.</span><span class="sxs-lookup"><span data-stu-id="59c4f-767">Install either self-signed or CA-signed certificates in the server's **Local Machine** > **Personal** store.</span></span>

1. <span data-ttu-id="59c4f-768">Uygulama [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise, .net core, .NET Framework veya her ikisini de (uygulama .NET Framework hedefleyen bir .NET Core uygulaması ise) yükler.</span><span class="sxs-lookup"><span data-stu-id="59c4f-768">If the app is a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd), install .NET Core, .NET Framework, or both (if the app is a .NET Core app targeting the .NET Framework).</span></span>

   * <span data-ttu-id="59c4f-769">**.NET Core**: uygulama .NET Core gerektiriyorsa .NET Core [Indirmelerinde](https://dotnet.microsoft.com/download) **.NET Core çalışma zamanı** yükleyicisini edinip çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-769">**.NET Core**: If the app requires .NET Core, obtain and run the **.NET Core Runtime** installer from [.NET Core Downloads](https://dotnet.microsoft.com/download).</span></span> <span data-ttu-id="59c4f-770">Tam SDK 'Yı sunucuya yüklemeyin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-770">Don't install the full SDK on the server.</span></span>
   * <span data-ttu-id="59c4f-771">**.NET Framework**: uygulama .NET Framework gerektiriyorsa [.NET Framework yükleme kılavuzuna](/dotnet/framework/install/)bakın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-771">**.NET Framework**: If the app requires .NET Framework, see the [.NET Framework installation guide](/dotnet/framework/install/).</span></span> <span data-ttu-id="59c4f-772">Gerekli .NET Framework yüklemesi.</span><span class="sxs-lookup"><span data-stu-id="59c4f-772">Install the required .NET Framework.</span></span> <span data-ttu-id="59c4f-773">En son .NET Framework yükleyicisi [.NET Core İndirmeleri](https://dotnet.microsoft.com/download) sayfasından edinilebilir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-773">The installer for the latest .NET Framework is available from the [.NET Core Downloads](https://dotnet.microsoft.com/download) page.</span></span>

   <span data-ttu-id="59c4f-774">Uygulama, [kendi içinde bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise, uygulama çalışma zamanını dağıtımda içerir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-774">If the app is a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd), the app includes the runtime in its deployment.</span></span> <span data-ttu-id="59c4f-775">Sunucuda çerçeve yüklemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="59c4f-775">No framework installation is required on the server.</span></span>

1. <span data-ttu-id="59c4f-776">Uygulamada URL 'Leri ve bağlantı noktalarını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-776">Configure URLs and ports in the app.</span></span>

   <span data-ttu-id="59c4f-777">Varsayılan olarak, ASP.NET Core öğesine bağlanır `http://localhost:5000` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-777">By default, ASP.NET Core binds to `http://localhost:5000`.</span></span> <span data-ttu-id="59c4f-778">URL öneklerini ve bağlantı noktalarını yapılandırmak için seçenekler şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="59c4f-778">To configure URL prefixes and ports, options include:</span></span>

   * <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
   * <span data-ttu-id="59c4f-779">`urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="59c4f-779">`urls` command-line argument</span></span>
   * <span data-ttu-id="59c4f-780">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="59c4f-780">`ASPNETCORE_URLS` environment variable</span></span>
   * <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes>

   <span data-ttu-id="59c4f-781">Aşağıdaki kod örneği, <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> `10.0.0.4` 443 numaralı bağlantı noktasında sunucunun yerel IP adresi ile nasıl kullanılacağını göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="59c4f-781">The following code example shows how to use <xref:Microsoft.AspNetCore.Server.HttpSys.HttpSysOptions.UrlPrefixes> with the server's local IP address `10.0.0.4` on port 443:</span></span>

   [!code-csharp[](httpsys/samples_snapshot/2.x/Program.cs?highlight=6)]

   <span data-ttu-id="59c4f-782">Avantajı `UrlPrefixes` , hatalı biçimli ön ekler için hemen bir hata iletisi oluşturulmasından oluşur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-782">An advantage of `UrlPrefixes` is that an error message is generated immediately for improperly formatted prefixes.</span></span>

   <span data-ttu-id="59c4f-783">Ayarları `UrlPrefixes` geçersiz kıl `UseUrls` / `urls` / `ASPNETCORE_URLS` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-783">The settings in `UrlPrefixes` override `UseUrls`/`urls`/`ASPNETCORE_URLS` settings.</span></span> <span data-ttu-id="59c4f-784">Bu nedenle, `UseUrls` `urls` ve `ASPNETCORE_URLS` ortam değişkeninin avantajı Kestrel ve HTTP.sys arasında geçiş yapmak daha kolay bir hale gelir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-784">Therefore, an advantage of `UseUrls`, `urls`, and the `ASPNETCORE_URLS` environment variable is that it's easier to switch between Kestrel and HTTP.sys.</span></span>

   <span data-ttu-id="59c4f-785">HTTP.sys, [http sunucusu API UrlPrefix dize biçimlerini](/windows/win32/http/urlprefix-strings)kullanır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-785">HTTP.sys uses the [HTTP Server API UrlPrefix string formats](/windows/win32/http/urlprefix-strings).</span></span>

   > [!WARNING]
   > <span data-ttu-id="59c4f-786">Üst düzey joker karakter bağlamaları ( `http://*:80/` ve `http://+:80` ) kullanılmamalıdır **not** .</span><span class="sxs-lookup"><span data-stu-id="59c4f-786">Top-level wildcard bindings (`http://*:80/` and `http://+:80`) should **not** be used.</span></span> <span data-ttu-id="59c4f-787">Üst düzey joker karakter bağlamaları uygulama güvenliği güvenlik açıklarını oluşturur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-787">Top-level wildcard bindings create app security vulnerabilities.</span></span> <span data-ttu-id="59c4f-788">Bu hem güçlü hem de zayıf Joker karakterlere yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-788">This applies to both strong and weak wildcards.</span></span> <span data-ttu-id="59c4f-789">Joker karakterler yerine açık konak adlarını veya IP adreslerini kullanın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-789">Use explicit host names or IP addresses rather than wildcards.</span></span> <span data-ttu-id="59c4f-790">Alt etki alanı joker bağlantısı (örneğin, `*.mysub.com` ), tüm üst etki alanını ( `*.com` Bu güvenlik açığı olan aksine) kontrol ediyorsanız bir güvenlik riski değildir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-790">Subdomain wildcard binding (for example, `*.mysub.com`) isn't a security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="59c4f-791">Daha fazla bilgi için bkz. [RFC 7230: bölüm 5,4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span><span class="sxs-lookup"><span data-stu-id="59c4f-791">For more information, see [RFC 7230: Section 5.4: Host](https://tools.ietf.org/html/rfc7230#section-5.4).</span></span>

1. <span data-ttu-id="59c4f-792">Ön ek, sunucudaki URL öneklerini ister.</span><span class="sxs-lookup"><span data-stu-id="59c4f-792">Preregister URL prefixes on the server.</span></span>

   <span data-ttu-id="59c4f-793">HTTP.sys yapılandırmaya yönelik yerleşik araç *netsh.exe*.</span><span class="sxs-lookup"><span data-stu-id="59c4f-793">The built-in tool for configuring HTTP.sys is *netsh.exe*.</span></span> <span data-ttu-id="59c4f-794">*netsh.exe* , URL öneklerini ayırmak ve X. 509.440 sertifikaları atamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-794">*netsh.exe* is used to reserve URL prefixes and assign X.509 certificates.</span></span> <span data-ttu-id="59c4f-795">Araç, yönetici ayrıcalıkları gerektirir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-795">The tool requires administrator privileges.</span></span>

   <span data-ttu-id="59c4f-796">Uygulamanın URL 'Lerini kaydetmek için *netsh.exe* aracını kullanın:</span><span class="sxs-lookup"><span data-stu-id="59c4f-796">Use the *netsh.exe* tool to register URLs for the app:</span></span>

   ```console
   netsh http add urlacl url=<URL> user=<USER>
   ```

   * <span data-ttu-id="59c4f-797">`<URL>`: Tam nitelikli Tekdüzen Kaynak Bulucu (URL).</span><span class="sxs-lookup"><span data-stu-id="59c4f-797">`<URL>`: The fully qualified Uniform Resource Locator (URL).</span></span> <span data-ttu-id="59c4f-798">Joker karakter bağlama kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-798">Don't use a wildcard binding.</span></span> <span data-ttu-id="59c4f-799">Geçerli bir ana bilgisayar adı veya yerel IP adresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-799">Use a valid hostname or local IP address.</span></span> <span data-ttu-id="59c4f-800">*URL, sondaki eğik çizgi içermelidir.*</span><span class="sxs-lookup"><span data-stu-id="59c4f-800">*The URL must include a trailing slash.*</span></span>
   * <span data-ttu-id="59c4f-801">`<USER>`: Kullanıcı veya Kullanıcı grubu adını belirtir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-801">`<USER>`: Specifies the user or user-group name.</span></span>

   <span data-ttu-id="59c4f-802">Aşağıdaki örnekte sunucusunun yerel IP adresi `10.0.0.4` :</span><span class="sxs-lookup"><span data-stu-id="59c4f-802">In the following example, the local IP address of the server is `10.0.0.4`:</span></span>

   ```console
   netsh http add urlacl url=https://10.0.0.4:443/ user=Users
   ```

   <span data-ttu-id="59c4f-803">Bir URL kaydedildiğinde, araç ile yanıt verir `URL reservation successfully added` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-803">When a URL is registered, the tool responds with `URL reservation successfully added`.</span></span>

   <span data-ttu-id="59c4f-804">Kayıtlı bir URL 'yi silmek için şu `delete urlacl` komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="59c4f-804">To delete a registered URL, use the `delete urlacl` command:</span></span>

   ```console
   netsh http delete urlacl url=<URL>
   ```

1. <span data-ttu-id="59c4f-805">X. 509.440 sertifikalarını sunucusuna kaydedin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-805">Register X.509 certificates on the server.</span></span>

   <span data-ttu-id="59c4f-806">Uygulamanın sertifikalarını kaydetmek için *netsh.exe* aracını kullanın:</span><span class="sxs-lookup"><span data-stu-id="59c4f-806">Use the *netsh.exe* tool to register certificates for the app:</span></span>

   ```console
   netsh http add sslcert ipport=<IP>:<PORT> certhash=<THUMBPRINT> appid="{<GUID>}"
   ```

   * <span data-ttu-id="59c4f-807">`<IP>`: Bağlama için yerel IP adresini belirtir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-807">`<IP>`: Specifies the local IP address for the binding.</span></span> <span data-ttu-id="59c4f-808">Joker karakter bağlama kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-808">Don't use a wildcard binding.</span></span> <span data-ttu-id="59c4f-809">Geçerli bir IP adresi kullanın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-809">Use a valid IP address.</span></span>
   * <span data-ttu-id="59c4f-810">`<PORT>`: Bağlama için bağlantı noktasını belirtir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-810">`<PORT>`: Specifies the port for the binding.</span></span>
   * <span data-ttu-id="59c4f-811">`<THUMBPRINT>`: X. 509.440 sertifika parmak izi.</span><span class="sxs-lookup"><span data-stu-id="59c4f-811">`<THUMBPRINT>`: The X.509 certificate thumbprint.</span></span>
   * <span data-ttu-id="59c4f-812">`<GUID>`: Uygulamayı bilgilendirme amacıyla temsil eden geliştirici tarafından oluşturulan bir GUID.</span><span class="sxs-lookup"><span data-stu-id="59c4f-812">`<GUID>`: A developer-generated GUID to represent the app for informational purposes.</span></span>

   <span data-ttu-id="59c4f-813">Başvuru amacıyla, GUID 'yi uygulamada bir paket etiketi olarak depolayın:</span><span class="sxs-lookup"><span data-stu-id="59c4f-813">For reference purposes, store the GUID in the app as a package tag:</span></span>

   * <span data-ttu-id="59c4f-814">Visual Studio 'da:</span><span class="sxs-lookup"><span data-stu-id="59c4f-814">In Visual Studio:</span></span>
     * <span data-ttu-id="59c4f-815">**Çözüm Gezgini** ' de uygulamaya sağ tıklayıp **Özellikler**' i seçerek uygulamanın proje özelliklerini açın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-815">Open the app's project properties by right-clicking on the app in **Solution Explorer** and selecting **Properties**.</span></span>
     * <span data-ttu-id="59c4f-816">**Paket** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-816">Select the **Package** tab.</span></span>
     * <span data-ttu-id="59c4f-817">**Etiketler** alanında oluşturduğunuz GUID 'yi girin.</span><span class="sxs-lookup"><span data-stu-id="59c4f-817">Enter the GUID that you created in the **Tags** field.</span></span>
   * <span data-ttu-id="59c4f-818">Visual Studio kullanmadığınız durumlarda:</span><span class="sxs-lookup"><span data-stu-id="59c4f-818">When not using Visual Studio:</span></span>
     * <span data-ttu-id="59c4f-819">Uygulamanın proje dosyasını açın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-819">Open the app's project file.</span></span>
     * <span data-ttu-id="59c4f-820">`<PackageTags>`Oluşturduğunuz GUID ile yeni veya var olan bir özelliği ekleyin `<PropertyGroup>` :</span><span class="sxs-lookup"><span data-stu-id="59c4f-820">Add a `<PackageTags>` property to a new or existing `<PropertyGroup>` with the GUID that you created:</span></span>

       ```xml
       <PropertyGroup>
         <PackageTags>9412ee86-c21b-4eb8-bd89-f650fbf44931</PackageTags>
       </PropertyGroup>
       ```

   <span data-ttu-id="59c4f-821">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="59c4f-821">In the following example:</span></span>

   * <span data-ttu-id="59c4f-822">Sunucunun yerel IP adresi `10.0.0.4` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-822">The local IP address of the server is `10.0.0.4`.</span></span>
   * <span data-ttu-id="59c4f-823">Bir çevrimiçi rastgele GUID Oluşturucu değeri sağlar `appid` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-823">An online random GUID generator provides the `appid` value.</span></span>

   ```console
   netsh http add sslcert 
       ipport=10.0.0.4:443 
       certhash=b66ee04419d4ee37464ab8785ff02449980eae10 
       appid="{9412ee86-c21b-4eb8-bd89-f650fbf44931}"
   ```

   <span data-ttu-id="59c4f-824">Bir sertifika kaydedildiğinde, araç ile yanıt verir `SSL Certificate successfully added` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-824">When a certificate is registered, the tool responds with `SSL Certificate successfully added`.</span></span>

   <span data-ttu-id="59c4f-825">Bir sertifika kaydını silmek için şu komutu kullanın `delete sslcert` :</span><span class="sxs-lookup"><span data-stu-id="59c4f-825">To delete a certificate registration, use the `delete sslcert` command:</span></span>

   ```console
   netsh http delete sslcert ipport=<IP>:<PORT>
   ```

   <span data-ttu-id="59c4f-826">*netsh.exe*için başvuru belgeleri:</span><span class="sxs-lookup"><span data-stu-id="59c4f-826">Reference documentation for *netsh.exe*:</span></span>

   * <span data-ttu-id="59c4f-827">[Köprü Metni Aktarım Protokolü (HTTP) için Netsh komutları](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span><span class="sxs-lookup"><span data-stu-id="59c4f-827">[Netsh Commands for Hypertext Transfer Protocol (HTTP)](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc725882(v=ws.10))</span></span>
   * [<span data-ttu-id="59c4f-828">UrlPrefix dizeleri</span><span class="sxs-lookup"><span data-stu-id="59c4f-828">UrlPrefix Strings</span></span>](/windows/win32/http/urlprefix-strings)

1. <span data-ttu-id="59c4f-829">Uygulamayı çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-829">Run the app.</span></span>

   <span data-ttu-id="59c4f-830">1024 'den büyük bir bağlantı noktası numarası ile HTTP (HTTPS değil) kullanarak localhost 'a bağlanırken uygulamayı çalıştırmak için yönetici ayrıcalıklarına gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="59c4f-830">Administrator privileges aren't required to run the app when binding to localhost using HTTP (not HTTPS) with a port number greater than 1024.</span></span> <span data-ttu-id="59c4f-831">Diğer yapılandırmalarda (örneğin, yerel bir IP adresi veya 443 numaralı bağlantı noktasına bağlama), uygulamayı yönetici ayrıcalıklarıyla çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="59c4f-831">For other configurations (for example, using a local IP address or binding to port 443), run the app with administrator privileges.</span></span>

   <span data-ttu-id="59c4f-832">Uygulama, sunucunun genel IP adresinde yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-832">The app responds at the server's public IP address.</span></span> <span data-ttu-id="59c4f-833">Bu örnekte, sunucusuna, genel IP adresinde Internet 'ten ulaşılırsa `104.214.79.47` .</span><span class="sxs-lookup"><span data-stu-id="59c4f-833">In this example, the server is reached from the Internet at its public IP address of `104.214.79.47`.</span></span>

   <span data-ttu-id="59c4f-834">Bu örnekte bir geliştirme sertifikası kullanılır.</span><span class="sxs-lookup"><span data-stu-id="59c4f-834">A development certificate is used in this example.</span></span> <span data-ttu-id="59c4f-835">Tarayıcının güvenilmeyen sertifika uyarısı atlandıktan sonra sayfa güvenli bir şekilde yüklenir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-835">The page loads securely after bypassing the browser's untrusted certificate warning.</span></span>

   ![Uygulamanın dizin sayfasını yüklü olarak gösteren tarayıcı penceresi](httpsys/_static/browser.png)

## <a name="proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="59c4f-837">Proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="59c4f-837">Proxy server and load balancer scenarios</span></span>

<span data-ttu-id="59c4f-838">Internet veya şirket ağından gelen isteklerle etkileşime geçen HTTP.sys tarafından barındırılan uygulamalar için, proxy sunucularının ve yük dengeleyiciler arkasında barındırılırken ek yapılandırma gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="59c4f-838">For apps hosted by HTTP.sys that interact with requests from the Internet or a corporate network, additional configuration might be required when hosting behind proxy servers and load balancers.</span></span> <span data-ttu-id="59c4f-839">Daha fazla bilgi için bkz. [proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma](xref:host-and-deploy/proxy-load-balancer).</span><span class="sxs-lookup"><span data-stu-id="59c4f-839">For more information, see [Configure ASP.NET Core to work with proxy servers and load balancers](xref:host-and-deploy/proxy-load-balancer).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="59c4f-840">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="59c4f-840">Additional resources</span></span>

* [<span data-ttu-id="59c4f-841">HTTP.sysile Windows kimlik doğrulamasını etkinleştir </span><span class="sxs-lookup"><span data-stu-id="59c4f-841">Enable Windows Authentication with HTTP.sys</span></span>](xref:security/authentication/windowsauth#httpsys)
* [<span data-ttu-id="59c4f-842">HTTP Sunucusu API 'SI</span><span class="sxs-lookup"><span data-stu-id="59c4f-842">HTTP Server API</span></span>](/windows/win32/http/http-api-start-page)
* [<span data-ttu-id="59c4f-843">ASPNET/HttpSysServer GitHub deposu (kaynak kodu)</span><span class="sxs-lookup"><span data-stu-id="59c4f-843">aspnet/HttpSysServer GitHub repository (source code)</span></span>](https://github.com/aspnet/HttpSysServer/)
* [<span data-ttu-id="59c4f-844">Ana bilgisayar</span><span class="sxs-lookup"><span data-stu-id="59c4f-844">The host</span></span>](xref:fundamentals/index#host)
* <xref:test/troubleshoot>

::: moniker-end
