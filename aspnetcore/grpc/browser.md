---
title: Tarayıcı uygulamalarında gRPC kullanma
author: jamesnk
description: ASP.NET Core GRPC hizmetlerini, gRPC-Web kullanan tarayıcı uygulamalarından çağrılabilir olacak şekilde nasıl yapılandıracağınızı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/browser
ms.openlocfilehash: 37932e755a0ef2149fb2336d2dcef87d3347d1a4
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404762"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="4d3d6-103">Tarayıcı uygulamalarında gRPC kullanma</span><span class="sxs-lookup"><span data-stu-id="4d3d6-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="4d3d6-104">, [James bAyKiNg](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="4d3d6-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="4d3d6-105">Tarayıcı tabanlı bir uygulamadan HTTP/2 gRPC hizmetini çağırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-105">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="4d3d6-106">[GRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) , tarayıcı JavaScript ve Blazor uygulamalarının GRPC hizmetlerini çağırmasını sağlayan bir protokoldür.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-106">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="4d3d6-107">Bu makalede, .NET Core 'da gRPC-Web ' i kullanma açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-107">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="4d3d6-108">gRPC-Web 'de ASP.NET Core ve Envoy 'ye karşı</span><span class="sxs-lookup"><span data-stu-id="4d3d6-108">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="4d3d6-109">GRPC-Web ' i bir ASP.NET Core uygulamasına eklemenin iki seçeneği vardır:</span><span class="sxs-lookup"><span data-stu-id="4d3d6-109">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="4d3d6-110">ASP.NET Core 'de GRPC HTTP/2 ile birlikte gRPC-Web desteği.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-110">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="4d3d6-111">Bu seçenek, paket tarafından sağlanmış olan ara yazılımı kullanır `Grpc.AspNetCore.Web` .</span><span class="sxs-lookup"><span data-stu-id="4d3d6-111">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="4d3d6-112">GRPC-Web ' i GRPC HTTP/2 ' ye çevirmek için [Envoy proxy 'nin](https://www.envoyproxy.io/) GRPC-Web desteğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-112">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="4d3d6-113">Çevrilen çağrı daha sonra ASP.NET Core uygulamasına iletilir.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-113">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="4d3d6-114">Her yaklaşımın olumlu ve olumsuz yönleri vardır.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-114">There are pros and cons to each approach.</span></span> <span data-ttu-id="4d3d6-115">Zaten uygulamanızın ortamında bir ara sunucu olarak kullanmak istiyorsanız, gRPC-Web desteği sağlamak için de kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-115">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="4d3d6-116">GRPC-Web için yalnızca ASP.NET Core gerektiren basit bir çözüm istiyorsanız `Grpc.AspNetCore.Web` iyi bir seçimdir.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-116">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="4d3d6-117">ASP.NET Core 'de gRPC-Web yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4d3d6-117">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="4d3d6-118">ASP.NET Core 'de barındırılan gRPC Hizmetleri, gRPC-Web ' i HTTP/2 gRPC ile birlikte destekleyecek şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-118">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="4d3d6-119">gRPC-Web, hizmetlerde herhangi bir değişiklik yapılmasını gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-119">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="4d3d6-120">Tek değişiklik başlangıç yapılandırması ' dır.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-120">The only modification is startup configuration.</span></span>

<span data-ttu-id="4d3d6-121">GRPC-Web ' i bir ASP.NET Core gRPC hizmeti ile etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="4d3d6-121">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="4d3d6-122">[GRPC. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) paketine bir başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-122">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="4d3d6-123">Uygulamayı Startup.cs ekleyerek gRPC-Web kullanacak şekilde yapılandırın `UseGrpcWeb` `EnableGrpcWeb` : *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="4d3d6-123">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="4d3d6-124">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="4d3d6-124">The preceding code:</span></span>

* <span data-ttu-id="4d3d6-125">`UseGrpcWeb`Yönlendirme sonrasında ve bitiş noktalarından önce gRPC-Web ara yazılımını ekler.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-125">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="4d3d6-126">Yöntemi, `endpoints.MapGrpcService<GreeterService>()` Ile gRPC-Web ' i destekler `EnableGrpcWeb` .</span><span class="sxs-lookup"><span data-stu-id="4d3d6-126">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="4d3d6-127">Alternatif olarak, gRPC-Web ara yazılımı, tüm hizmetlerin varsayılan olarak gRPC-Web 'i desteklemesi ve gerekli olmaması için yapılandırılabilir `EnableGrpcWeb` .</span><span class="sxs-lookup"><span data-stu-id="4d3d6-127">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="4d3d6-128">`new GrpcWebOptions { DefaultEnabled = true }`Ara yazılımın ne zaman ekleneceğini belirtin.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-128">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="4d3d6-129">.NET Core 3. x içinde [Http.systarafından barındırılırken](xref:fundamentals/servers/httpsys) GRPC-Web ' i, başarısız olmasına neden olan bilinen bir sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-129">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="4d3d6-130">GRPC 'yi (Http.sys Web üzerinde çalışmaya yönelik bir geçici çözüm) [buradan](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)edinebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-130">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="4d3d6-131">gRPC-Web ve CORS</span><span class="sxs-lookup"><span data-stu-id="4d3d6-131">gRPC-Web and CORS</span></span>

<span data-ttu-id="4d3d6-132">Tarayıcı güvenliği, bir Web sayfasının Web sayfasını sunduğundan farklı bir etki alanına istek yapmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-132">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="4d3d6-133">Bu kısıtlama, tarayıcı uygulamalarıyla gRPC Web çağrıları yapmak için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-133">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="4d3d6-134">Örneğin, tarafından sunulan bir tarayıcı uygulamasının `https://www.contoso.com` üzerinde barındırılan gRPC-Web hizmetlerinden çağrılması engellenir `https://services.contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="4d3d6-134">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="4d3d6-135">Bu kısıtlamayı rahatmak için çapraz kaynak kaynak paylaşımı (CORS) kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-135">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="4d3d6-136">Tarayıcı uygulamanızın çıkış noktaları arası gRPC-Web çağrıları yapmasına izin vermek için [ASP.NET Core 'de CORS](xref:security/cors)'yi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-136">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="4d3d6-137">Yerleşik CORS desteğini kullanın ve gRPC 'ye özgü üst bilgileri ile kullanıma sunun <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="4d3d6-137">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="4d3d6-138">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="4d3d6-138">The preceding code:</span></span>

* <span data-ttu-id="4d3d6-139">`AddCors`CORS Hizmetleri ekleme ve gRPC 'ye özgü üst bilgileri kullanıma sunan BIR CORS ilkesini yapılandırma çağrıları.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-139">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="4d3d6-140">`UseCors`Yönlendirmeden ve bitiş noktalarından önce CORS ara yazılımını ekleme çağrıları.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-140">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="4d3d6-141">`endpoints.MapGrpcService<GreeterService>()`CORS 'yi ile desteklerken yöntemi belirtir `RequiresCors` .</span><span class="sxs-lookup"><span data-stu-id="4d3d6-141">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="4d3d6-142">Tarayıcıdan gRPC-Web 'i çağırma</span><span class="sxs-lookup"><span data-stu-id="4d3d6-142">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="4d3d6-143">Tarayıcı uygulamaları GRPC hizmetlerini çağırmak için gRPC-Web kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-143">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="4d3d6-144">GRPC hizmetlerini tarayıcıda GRPC-Web ile çağırırken bazı gereksinimler ve sınırlamalar vardır:</span><span class="sxs-lookup"><span data-stu-id="4d3d6-144">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="4d3d6-145">Sunucu, gRPC-Web ' i destekleyecek şekilde yapılandırılmış olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-145">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="4d3d6-146">İstemci akışı ve çift yönlü akış çağrıları desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-146">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="4d3d6-147">Sunucu akışı destekleniyor.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-147">Server streaming is supported.</span></span>
* <span data-ttu-id="4d3d6-148">Farklı bir etki alanında gRPC hizmetlerinin çağrılması için [CORS](xref:security/cors) 'nin sunucuda yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-148">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="4d3d6-149">JavaScript gRPC-Web istemcisi</span><span class="sxs-lookup"><span data-stu-id="4d3d6-149">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="4d3d6-150">Bir JavaScript gRPC-Web istemcisi vardır.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-150">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="4d3d6-151">JavaScript 'ten gRPC-Web kullanma hakkında yönergeler için bkz. [gRPC-web Ile JavaScript istemci kodu yazma](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="4d3d6-151">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="4d3d6-152">.NET gRPC istemcisiyle gRPC-Web yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4d3d6-152">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="4d3d6-153">.NET gRPC istemcisi, gRPC-Web çağrıları yapmak için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-153">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="4d3d6-154">Bu, [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) tarayıcıda barındırılan ve JavaScript koduyla aynı http kısıtlamalarına sahip olan uygulamalar için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-154">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="4d3d6-155">Bir .NET istemcisiyle gRPC-Web ' i çağırmak [http/2 gRPC ile aynıdır](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="4d3d6-155">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="4d3d6-156">Tek değişiklik, kanalın oluşturulma şekli olur.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-156">The only modification is how the channel is created.</span></span>

<span data-ttu-id="4d3d6-157">GRPC-Web kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="4d3d6-157">To use gRPC-Web:</span></span>

* <span data-ttu-id="4d3d6-158">[GRPC .net. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) paketine başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-158">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="4d3d6-159">[GRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) paketine yönelik başvurunun 2.29.0 veya daha büyük olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-159">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="4d3d6-160">Kanalı şunu kullanacak şekilde yapılandırın `GrpcWebHandler` :</span><span class="sxs-lookup"><span data-stu-id="4d3d6-160">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="4d3d6-161">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="4d3d6-161">The preceding code:</span></span>

* <span data-ttu-id="4d3d6-162">GRPC-Web kullanmak için bir kanal yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-162">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="4d3d6-163">Bir istemci oluşturur ve kanalı kullanarak bir çağrı yapar.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-163">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="4d3d6-164">`GrpcWebHandler`Aşağıdaki yapılandırma seçeneklerine sahiptir:</span><span class="sxs-lookup"><span data-stu-id="4d3d6-164">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="4d3d6-165">**InnerHandler**:, <xref:System.Net.Http.HttpMessageHandler> Örneğin, GRPC http isteğini oluşturan temel `HttpClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="4d3d6-165">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="4d3d6-166">**Grpcwebmode**: GRPC http isteğinin veya olup olmadığını belirten bir numaralandırma türü `Content-Type` `application/grpc-web` `application/grpc-web-text` .</span><span class="sxs-lookup"><span data-stu-id="4d3d6-166">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="4d3d6-167">`GrpcWebMode.GrpcWeb`kodlamadan gönderilecek içeriği yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-167">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="4d3d6-168">Varsayılan değer.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-168">Default value.</span></span>
    * <span data-ttu-id="4d3d6-169">`GrpcWebMode.GrpcWebText`içeriği Base64 kodlamalı olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-169">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="4d3d6-170">Tarayıcılarda sunucu akış çağrıları için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-170">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="4d3d6-171">**HttpVersion**: `Version` TEMELDEKI GRPC http isteğindeki [HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) öğesini ayarlamak için kullanılan http protokolü.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-171">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="4d3d6-172">gRPC-Web belirli bir sürüm gerektirmez ve belirtilmediği takdirde varsayılanı geçersiz kılmaz.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-172">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="4d3d6-173">Oluşturulan gRPC istemcilerinin birli yöntemleri çağırmak için eşitleme ve zaman uyumsuz yöntemleri vardır.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-173">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="4d3d6-174">Örneğin, `SayHello` eşitlenir ve `SayHelloAsync` zaman uyumsuz olur.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-174">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="4d3d6-175">Bir uygulamada eşitleme yönteminin çağrılması Blazor WebAssembly uygulamanın yanıt vermemeye başlamasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="4d3d6-175">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="4d3d6-176">Zaman uyumsuz yöntemlerin içinde her zaman kullanılması gerekir Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="4d3d6-176">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4d3d6-177">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="4d3d6-177">Additional resources</span></span>

* [<span data-ttu-id="4d3d6-178">Web Istemcileri için gRPC GitHub projesi</span><span class="sxs-lookup"><span data-stu-id="4d3d6-178">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
