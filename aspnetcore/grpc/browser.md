---
title: Tarayıcı uygulamalarında gRPC kullanma
author: jamesnk
description: ASP.NET Core GRPC hizmetlerini, gRPC-Web kullanan tarayıcı uygulamalarından çağrılabilir olacak şekilde nasıl yapılandıracağınızı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/browser
ms.openlocfilehash: a74f7acb54b4601a0c30ff1a39dc30231e2b5a78
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774749"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="2e2db-103">Tarayıcı uygulamalarında gRPC kullanma</span><span class="sxs-lookup"><span data-stu-id="2e2db-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="2e2db-104">, [James bAyKiNg](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="2e2db-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2e2db-105">**gRPC-.NET 'teki Web desteği deneysel**</span><span class="sxs-lookup"><span data-stu-id="2e2db-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="2e2db-106">gRPC-.NET için Web, kaydedilmiş bir ürün değil, deneysel bir projem projem.</span><span class="sxs-lookup"><span data-stu-id="2e2db-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="2e2db-107">Şunları yapmak istiyoruz:</span><span class="sxs-lookup"><span data-stu-id="2e2db-107">We want to:</span></span>
>
> * <span data-ttu-id="2e2db-108">GRPC-Web ' i uygulamaya yönelik yaklaşımımızı test edin.</span><span class="sxs-lookup"><span data-stu-id="2e2db-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="2e2db-109">Bu yaklaşım .NET geliştiricileri için, bir ara sunucu aracılığıyla gRPC-Web ayarlamanın geleneksel bir yolu ile karşılaştırıldığında yararlı olup olmadığını hakkında geri bildirim alın.</span><span class="sxs-lookup"><span data-stu-id="2e2db-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="2e2db-110">Geliştiricilerin, ve ile [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) üretken olduğu bir şey oluşturduğumdan emin olmak için lütfen geri bildirimde bulunun.</span><span class="sxs-lookup"><span data-stu-id="2e2db-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="2e2db-111">Tarayıcı tabanlı bir uygulamadan HTTP/2 gRPC hizmetini çağırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="2e2db-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="2e2db-112">[GRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) , tarayıcı JavaScript ve Blazor uygulamalarının GRPC hizmetlerini çağırmasını sağlayan bir protokoldür.</span><span class="sxs-lookup"><span data-stu-id="2e2db-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="2e2db-113">Bu makalede, .NET Core 'da gRPC-Web ' i kullanma açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="2e2db-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="2e2db-114">gRPC-Web 'de ASP.NET Core ve Envoy 'ye karşı</span><span class="sxs-lookup"><span data-stu-id="2e2db-114">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="2e2db-115">GRPC-Web ' i bir ASP.NET Core uygulamasına eklemenin iki seçeneği vardır:</span><span class="sxs-lookup"><span data-stu-id="2e2db-115">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="2e2db-116">ASP.NET Core 'de GRPC HTTP/2 ile birlikte gRPC-Web desteği.</span><span class="sxs-lookup"><span data-stu-id="2e2db-116">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="2e2db-117">Bu seçenek, `Grpc.AspNetCore.Web` paket tarafından sağlanmış olan ara yazılımı kullanır.</span><span class="sxs-lookup"><span data-stu-id="2e2db-117">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="2e2db-118">GRPC-Web ' i GRPC HTTP/2 ' ye çevirmek için [Envoy proxy 'nin](https://www.envoyproxy.io/) GRPC-Web desteğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="2e2db-118">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="2e2db-119">Çevrilen çağrı daha sonra ASP.NET Core uygulamasına iletilir.</span><span class="sxs-lookup"><span data-stu-id="2e2db-119">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="2e2db-120">Her yaklaşımın olumlu ve olumsuz yönleri vardır.</span><span class="sxs-lookup"><span data-stu-id="2e2db-120">There are pros and cons to each approach.</span></span> <span data-ttu-id="2e2db-121">Zaten uygulamanızın ortamında bir ara sunucu olarak kullanmak istiyorsanız, gRPC-Web desteği sağlamak için de kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2e2db-121">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="2e2db-122">GRPC-Web için yalnızca ASP.NET Core `Grpc.AspNetCore.Web` gerektiren basit bir çözüm istiyorsanız iyi bir seçimdir.</span><span class="sxs-lookup"><span data-stu-id="2e2db-122">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="2e2db-123">ASP.NET Core 'de gRPC-Web yapılandırma</span><span class="sxs-lookup"><span data-stu-id="2e2db-123">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="2e2db-124">ASP.NET Core 'de barındırılan gRPC Hizmetleri, gRPC-Web ' i HTTP/2 gRPC ile birlikte destekleyecek şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="2e2db-124">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="2e2db-125">gRPC-Web, hizmetlerde herhangi bir değişiklik yapılmasını gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="2e2db-125">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="2e2db-126">Tek değişiklik başlangıç yapılandırması ' dır.</span><span class="sxs-lookup"><span data-stu-id="2e2db-126">The only modification is startup configuration.</span></span>

<span data-ttu-id="2e2db-127">GRPC-Web ' i bir ASP.NET Core gRPC hizmeti ile etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="2e2db-127">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="2e2db-128">[GRPC. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) paketine bir başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2e2db-128">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="2e2db-129">Uygulamayı `AddGrpcWeb` `UseGrpcWeb` *Startup.cs*ekleyerek GRPC-Web kullanacak şekilde yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="2e2db-129">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="2e2db-130">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="2e2db-130">The preceding code:</span></span>

* <span data-ttu-id="2e2db-131">Yönlendirme sonrasında ve bitiş noktalarından önce gRPC `UseGrpcWeb`-Web ara yazılımını ekler.</span><span class="sxs-lookup"><span data-stu-id="2e2db-131">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="2e2db-132">Yöntemi, `endpoints.MapGrpcService<GreeterService>()` Ile `EnableGrpcWeb`GRPC-Web ' i destekler.</span><span class="sxs-lookup"><span data-stu-id="2e2db-132">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="2e2db-133">Alternatif olarak, tüm hizmetleri, bir ConfigureServices 'e ekleyerek `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` GRPC-Web ' i destekleyecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="2e2db-133">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> <span data-ttu-id="2e2db-134">.NET Core 3. x içinde [http. sys tarafından barındırılırken](xref:fundamentals/servers/httpsys) GRPC-Web ' i, başarısız olmasına neden olan bilinen bir sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="2e2db-134">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="2e2db-135">GRPC 'yi almaya yönelik bir geçici çözüm olan http. sys üzerinde Web 'de çalışmaya [buradan](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)ulaşabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2e2db-135">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="2e2db-136">gRPC-Web ve CORS</span><span class="sxs-lookup"><span data-stu-id="2e2db-136">gRPC-Web and CORS</span></span>

<span data-ttu-id="2e2db-137">Tarayıcı güvenliği, bir Web sayfasının Web sayfasını sunduğundan farklı bir etki alanına istek yapmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="2e2db-137">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="2e2db-138">Bu kısıtlama, tarayıcı uygulamalarıyla gRPC Web çağrıları yapmak için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="2e2db-138">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="2e2db-139">Örneğin, tarafından `https://www.contoso.com` sunulan bir tarayıcı uygulamasının üzerinde `https://services.contoso.com`barındırılan GRPC-Web hizmetlerinden çağrılması engellenir.</span><span class="sxs-lookup"><span data-stu-id="2e2db-139">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="2e2db-140">Bu kısıtlamayı rahatmak için çapraz kaynak kaynak paylaşımı (CORS) kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2e2db-140">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="2e2db-141">Tarayıcı uygulamanızın çıkış noktaları arası gRPC-Web çağrıları yapmasına izin vermek için [ASP.NET Core 'de CORS](xref:security/cors)'yi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="2e2db-141">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="2e2db-142">Yerleşik CORS desteğini kullanın ve gRPC 'ye özgü üst bilgileri ile <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>kullanıma sunun.</span><span class="sxs-lookup"><span data-stu-id="2e2db-142">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="2e2db-143">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="2e2db-143">The preceding code:</span></span>

* <span data-ttu-id="2e2db-144">CORS `AddCors` Hizmetleri ekleme ve GRPC 'ye özgü üst bilgileri kullanıma sunan bir CORS ilkesini yapılandırma çağrıları.</span><span class="sxs-lookup"><span data-stu-id="2e2db-144">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="2e2db-145">Yönlendirmeden `UseCors` ve bitiş NOKTALARıNDAN önce CORS ara yazılımını ekleme çağrıları.</span><span class="sxs-lookup"><span data-stu-id="2e2db-145">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="2e2db-146">CORS 'yi `endpoints.MapGrpcService<GreeterService>()` ile `RequiresCors`desteklerken yöntemi belirtir.</span><span class="sxs-lookup"><span data-stu-id="2e2db-146">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="2e2db-147">Tarayıcıdan gRPC-Web 'i çağırma</span><span class="sxs-lookup"><span data-stu-id="2e2db-147">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="2e2db-148">Tarayıcı uygulamaları GRPC hizmetlerini çağırmak için gRPC-Web kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="2e2db-148">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="2e2db-149">GRPC hizmetlerini tarayıcıda GRPC-Web ile çağırırken bazı gereksinimler ve sınırlamalar vardır:</span><span class="sxs-lookup"><span data-stu-id="2e2db-149">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="2e2db-150">Sunucu, gRPC-Web ' i destekleyecek şekilde yapılandırılmış olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2e2db-150">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="2e2db-151">İstemci akışı ve çift yönlü akış çağrıları desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="2e2db-151">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="2e2db-152">Sunucu akışı destekleniyor.</span><span class="sxs-lookup"><span data-stu-id="2e2db-152">Server streaming is supported.</span></span>
* <span data-ttu-id="2e2db-153">Farklı bir etki alanında gRPC hizmetlerinin çağrılması için [CORS](xref:security/cors) 'nin sunucuda yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2e2db-153">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="2e2db-154">JavaScript gRPC-Web istemcisi</span><span class="sxs-lookup"><span data-stu-id="2e2db-154">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="2e2db-155">Bir JavaScript gRPC-Web istemcisi vardır.</span><span class="sxs-lookup"><span data-stu-id="2e2db-155">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="2e2db-156">JavaScript 'ten gRPC-Web kullanma hakkında yönergeler için bkz. [gRPC-web Ile JavaScript istemci kodu yazma](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="2e2db-156">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="2e2db-157">.NET gRPC istemcisiyle gRPC-Web yapılandırma</span><span class="sxs-lookup"><span data-stu-id="2e2db-157">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="2e2db-158">.NET gRPC istemcisi, gRPC-Web çağrıları yapmak için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="2e2db-158">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="2e2db-159">Bu, tarayıcıda barındırılan ve JavaScript koduyla aynı http kısıtlamalarına sahip olan [ Blazor webassembly](xref:blazor/index#blazor-webassembly) uygulamaları için kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="2e2db-159">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="2e2db-160">Bir .NET istemcisiyle gRPC-Web ' i çağırmak [http/2 gRPC ile aynıdır](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="2e2db-160">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="2e2db-161">Tek değişiklik, kanalın oluşturulma şekli olur.</span><span class="sxs-lookup"><span data-stu-id="2e2db-161">The only modification is how the channel is created.</span></span>

<span data-ttu-id="2e2db-162">GRPC-Web kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="2e2db-162">To use gRPC-Web:</span></span>

* <span data-ttu-id="2e2db-163">[GRPC .net. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) paketine başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2e2db-163">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="2e2db-164">[GRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) paketine yönelik başvurunun 2.27.0 veya daha büyük olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="2e2db-164">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="2e2db-165">Kanalı şunu kullanacak şekilde yapılandırın `GrpcWebHandler`:</span><span class="sxs-lookup"><span data-stu-id="2e2db-165">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="2e2db-166">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="2e2db-166">The preceding code:</span></span>

* <span data-ttu-id="2e2db-167">GRPC-Web kullanmak için bir kanal yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="2e2db-167">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="2e2db-168">Bir istemci oluşturur ve kanalı kullanarak bir çağrı yapar.</span><span class="sxs-lookup"><span data-stu-id="2e2db-168">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="2e2db-169">, `GrpcWebHandler` Oluşturulduğunda aşağıdaki yapılandırma seçeneklerine sahiptir:</span><span class="sxs-lookup"><span data-stu-id="2e2db-169">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="2e2db-170">**InnerHandler**:, örneğin <xref:System.Net.Http.HttpMessageHandler> , `HttpClientHandler`GRPC http isteğini oluşturan temel.</span><span class="sxs-lookup"><span data-stu-id="2e2db-170">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="2e2db-171">**Mode**: GRPC http isteğinin `Content-Type` `application/grpc-web` veya `application/grpc-web-text`olup olmadığını belirten bir numaralandırma türü.</span><span class="sxs-lookup"><span data-stu-id="2e2db-171">**Mode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="2e2db-172">`GrpcWebMode.GrpcWeb`kodlamadan gönderilecek içeriği yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="2e2db-172">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="2e2db-173">Varsayılan değer.</span><span class="sxs-lookup"><span data-stu-id="2e2db-173">Default value.</span></span>
    * <span data-ttu-id="2e2db-174">`GrpcWebMode.GrpcWebText`içeriği Base64 kodlamalı olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="2e2db-174">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="2e2db-175">Tarayıcılarda sunucu akış çağrıları için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="2e2db-175">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="2e2db-176">**HttpVersion**: temeldeki GRPC http `Version` Isteğindeki [HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) öğesini ayarlamak için kullanılan http protokolü.</span><span class="sxs-lookup"><span data-stu-id="2e2db-176">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="2e2db-177">gRPC-Web belirli bir sürüm gerektirmez ve belirtilmediği takdirde varsayılanı geçersiz kılmaz.</span><span class="sxs-lookup"><span data-stu-id="2e2db-177">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="2e2db-178">Oluşturulan gRPC istemcilerinin birli yöntemleri çağırmak için eşitleme ve zaman uyumsuz yöntemleri vardır.</span><span class="sxs-lookup"><span data-stu-id="2e2db-178">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="2e2db-179">Örneğin, `SayHello` eşitlenir ve `SayHelloAsync` zaman uyumsuz olur.</span><span class="sxs-lookup"><span data-stu-id="2e2db-179">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="2e2db-180">Bir Blazor webassembly uygulamasında bir Sync yönteminin çağrılması uygulamanın yanıt vermemeye başlamasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="2e2db-180">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="2e2db-181">Zaman uyumsuz yöntemlerin her zaman webassembly içinde Blazor kullanılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2e2db-181">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2e2db-182">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2e2db-182">Additional resources</span></span>

* [<span data-ttu-id="2e2db-183">Web Istemcileri için gRPC GitHub projesi</span><span class="sxs-lookup"><span data-stu-id="2e2db-183">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
