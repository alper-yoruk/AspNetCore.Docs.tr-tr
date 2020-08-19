---
title: Tarayıcı uygulamalarında gRPC kullanma
author: jamesnk
description: ASP.NET Core GRPC hizmetlerini, gRPC-Web kullanan tarayıcı uygulamalarından çağrılabilir olacak şekilde nasıl yapılandıracağınızı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 06/30/2020
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
uid: grpc/browser
ms.openlocfilehash: 8d1f761731ab3840d009eba1ff5316808bafec40
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634416"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="7fb3f-103">Tarayıcı uygulamalarında gRPC kullanma</span><span class="sxs-lookup"><span data-stu-id="7fb3f-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="7fb3f-104">, [James bAyKiNg](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="7fb3f-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

 <span data-ttu-id="7fb3f-105">[GRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) protokolünü kullanarak var olan bir ASP.NET Core GRPC hizmetini tarayıcı uygulamalarından çağrılabilir olacak şekilde yapılandırmayı öğrenin.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-105">Learn how to configure an existing ASP.NET Core gRPC service to be callable from browser apps, using the [gRPC-Web](https://github.com/grpc/grpc/blob/2a388793792cc80944334535b7c729494d209a7e/doc/PROTOCOL-WEB.md) protocol.</span></span> <span data-ttu-id="7fb3f-106">gRPC-Web tarayıcı JavaScript ve Blazor uygulamalarının GRPC hizmetlerini çağırmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-106">gRPC-Web allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="7fb3f-107">Tarayıcı tabanlı bir uygulamadan HTTP/2 gRPC hizmetini çağırmak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-107">It's not possible to call an HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="7fb3f-108">ASP.NET Core 'de barındırılan gRPC Hizmetleri, gRPC-Web ' i HTTP/2 gRPC ile birlikte destekleyecek şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-108">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span>


<span data-ttu-id="7fb3f-109">Mevcut bir ASP.NET Core uygulamasına gRPC hizmeti ekleme hakkında yönergeler için bkz. [bir ASP.NET Core uygulamasına GRPC Hizmetleri ekleme](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).</span><span class="sxs-lookup"><span data-stu-id="7fb3f-109">For instructions on adding a gRPC service to an existing ASP.NET Core app, see [Add gRPC services to an ASP.NET Core app](xref:grpc/aspnetcore#add-grpc-services-to-an-aspnet-core-app).</span></span>

<span data-ttu-id="7fb3f-110">GRPC projesi oluşturma hakkında yönergeler için bkz <xref:tutorials/grpc/grpc-start> ..</span><span class="sxs-lookup"><span data-stu-id="7fb3f-110">For instructions on creating a gRPC project, see <xref:tutorials/grpc/grpc-start>.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="7fb3f-111">gRPC-Web 'de ASP.NET Core ve Envoy 'ye karşı</span><span class="sxs-lookup"><span data-stu-id="7fb3f-111">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="7fb3f-112">GRPC-Web ' i bir ASP.NET Core uygulamasına eklemenin iki seçeneği vardır:</span><span class="sxs-lookup"><span data-stu-id="7fb3f-112">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="7fb3f-113">ASP.NET Core 'de GRPC HTTP/2 ile birlikte gRPC-Web desteği.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-113">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="7fb3f-114">Bu seçenek, paket tarafından sağlanmış olan ara yazılımı kullanır `Grpc.AspNetCore.Web` .</span><span class="sxs-lookup"><span data-stu-id="7fb3f-114">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="7fb3f-115">GRPC-Web ' i GRPC HTTP/2 ' ye çevirmek için [Envoy proxy 'nin](https://www.envoyproxy.io/) GRPC-Web desteğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-115">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="7fb3f-116">Çevrilen çağrı daha sonra ASP.NET Core uygulamasına iletilir.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-116">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="7fb3f-117">Her yaklaşımın olumlu ve olumsuz yönleri vardır.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-117">There are pros and cons to each approach.</span></span> <span data-ttu-id="7fb3f-118">Bir uygulamanın ortamı zaten bir ara sunucu olarak kullanılıyorsa, gRPC-Web desteği sağlamak için de Envoy kullanılması mantıklı olabilir.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-118">If an app's environment is already using Envoy as a proxy, it might make sense to also use Envoy to provide gRPC-Web support.</span></span> <span data-ttu-id="7fb3f-119">Yalnızca ASP.NET Core gerektiren gRPC-Web için temel bir çözüm için `Grpc.AspNetCore.Web` iyi bir seçimdir.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-119">For a basic solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="7fb3f-120">ASP.NET Core 'de gRPC-Web yapılandırma</span><span class="sxs-lookup"><span data-stu-id="7fb3f-120">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="7fb3f-121">ASP.NET Core 'de barındırılan gRPC Hizmetleri, gRPC-Web ' i HTTP/2 gRPC ile birlikte destekleyecek şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-121">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="7fb3f-122">gRPC-Web, hizmetlerde herhangi bir değişiklik yapılmasını gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-122">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="7fb3f-123">Tek değişiklik başlangıç yapılandırması ' dır.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-123">The only modification is startup configuration.</span></span>

<span data-ttu-id="7fb3f-124">GRPC-Web ' i bir ASP.NET Core gRPC hizmeti ile etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="7fb3f-124">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="7fb3f-125">[GRPC. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) paketine bir başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-125">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="7fb3f-126">Uygulamayı Startup.cs ekleyerek gRPC-Web kullanacak şekilde yapılandırın `UseGrpcWeb` `EnableGrpcWeb` : *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="7fb3f-126">Configure the app to use gRPC-Web by adding `UseGrpcWeb` and `EnableGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="7fb3f-127">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="7fb3f-127">The preceding code:</span></span>

* <span data-ttu-id="7fb3f-128">`UseGrpcWeb`Yönlendirme sonrasında ve bitiş noktalarından önce gRPC-Web ara yazılımını ekler.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-128">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="7fb3f-129">Yöntemi, `endpoints.MapGrpcService<GreeterService>()` Ile gRPC-Web ' i destekler `EnableGrpcWeb` .</span><span class="sxs-lookup"><span data-stu-id="7fb3f-129">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="7fb3f-130">Alternatif olarak, gRPC-Web ara yazılımı, tüm hizmetlerin varsayılan olarak gRPC-Web 'i desteklemesi ve gerekli olmaması için yapılandırılabilir `EnableGrpcWeb` .</span><span class="sxs-lookup"><span data-stu-id="7fb3f-130">Alternatively, the gRPC-Web middleware can be configured so all services support gRPC-Web by default and `EnableGrpcWeb` isn't required.</span></span> <span data-ttu-id="7fb3f-131">`new GrpcWebOptions { DefaultEnabled = true }`Ara yazılımın ne zaman ekleneceğini belirtin.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-131">Specify `new GrpcWebOptions { DefaultEnabled = true }` when the middleware is added.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> <span data-ttu-id="7fb3f-132">.NET Core 3. x içinde [Http.systarafından barındırılırken ](xref:fundamentals/servers/httpsys) GRPC-Web ' i, başarısız olmasına neden olan bilinen bir sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-132">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="7fb3f-133">GRPC 'yi (Http.sys Web üzerinde çalışmaya yönelik bir geçici çözüm) [buradan](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)edinebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-133">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="7fb3f-134">gRPC-Web ve CORS</span><span class="sxs-lookup"><span data-stu-id="7fb3f-134">gRPC-Web and CORS</span></span>

<span data-ttu-id="7fb3f-135">Tarayıcı güvenliği, bir Web sayfasının Web sayfasını sunduğundan farklı bir etki alanına istek yapmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-135">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="7fb3f-136">Bu kısıtlama, tarayıcı uygulamalarıyla gRPC Web çağrıları yapmak için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-136">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="7fb3f-137">Örneğin, tarafından sunulan bir tarayıcı uygulamasının `https://www.contoso.com` üzerinde barındırılan gRPC-Web hizmetlerinden çağrılması engellenir `https://services.contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="7fb3f-137">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="7fb3f-138">Bu kısıtlamayı rahatmak için çapraz kaynak kaynak paylaşımı (CORS) kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-138">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="7fb3f-139">Bir tarayıcı uygulamasının, çıkış noktaları arası gRPC-Web çağrıları yapmasına izin vermek için [ASP.NET Core 'de CORS](xref:security/cors)'yi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-139">To allow a browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="7fb3f-140">Yerleşik CORS desteğini kullanın ve gRPC 'ye özgü üst bilgileri ile kullanıma sunun <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A> .</span><span class="sxs-lookup"><span data-stu-id="7fb3f-140">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders%2A>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="7fb3f-141">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="7fb3f-141">The preceding code:</span></span>

* <span data-ttu-id="7fb3f-142">`AddCors`CORS Hizmetleri ekleme ve gRPC 'ye özgü üst bilgileri kullanıma sunan BIR CORS ilkesini yapılandırma çağrıları.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-142">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="7fb3f-143">`UseCors`Yönlendirmeden ve bitiş noktalarından önce CORS ara yazılımını ekleme çağrıları.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-143">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="7fb3f-144">`endpoints.MapGrpcService<GreeterService>()`CORS 'yi ile desteklerken yöntemi belirtir `RequiresCors` .</span><span class="sxs-lookup"><span data-stu-id="7fb3f-144">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

### <a name="grpc-web-and-streaming"></a><span data-ttu-id="7fb3f-145">gRPC-Web ve akış</span><span class="sxs-lookup"><span data-stu-id="7fb3f-145">gRPC-Web and streaming</span></span>

<span data-ttu-id="7fb3f-146">HTTP/2 üzerinde geleneksel gRPC, akışı tüm yönlere göre destekler.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-146">Traditional gRPC over HTTP/2 supports streaming in all directions.</span></span> <span data-ttu-id="7fb3f-147">gRPC-Web, akış için sınırlı destek sunar:</span><span class="sxs-lookup"><span data-stu-id="7fb3f-147">gRPC-Web offers limited support for streaming:</span></span>

* <span data-ttu-id="7fb3f-148">gRPC-Web tarayıcısı istemcileri, istemci akışı ve çift yönlü akış yöntemlerinin çağrılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-148">gRPC-Web browser clients don't support calling client streaming and bidirectional streaming methods.</span></span>
* <span data-ttu-id="7fb3f-149">Azure App Service ve IIS 'de barındırılan ASP.NET Core gRPC Hizmetleri çift yönlü akışı desteklemez.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-149">ASP.NET Core gRPC services hosted on Azure App Service and IIS don't support bidirectional streaming.</span></span>

<span data-ttu-id="7fb3f-150">GRPC-Web kullanırken, yalnızca birli yöntemlerin ve sunucu akış yöntemlerinin kullanılması önerilir.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-150">When using gRPC-Web, we only recommend the use of unary methods and server streaming methods.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="7fb3f-151">Tarayıcıdan gRPC-Web 'i çağırma</span><span class="sxs-lookup"><span data-stu-id="7fb3f-151">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="7fb3f-152">Tarayıcı uygulamaları GRPC hizmetlerini çağırmak için gRPC-Web kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-152">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="7fb3f-153">GRPC hizmetlerini tarayıcıda GRPC-Web ile çağırırken bazı gereksinimler ve sınırlamalar vardır:</span><span class="sxs-lookup"><span data-stu-id="7fb3f-153">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="7fb3f-154">Sunucu, gRPC-Web ' i destekleyecek şekilde yapılandırılmış olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-154">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="7fb3f-155">İstemci akışı ve çift yönlü akış çağrıları desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-155">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="7fb3f-156">Sunucu akışı destekleniyor.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-156">Server streaming is supported.</span></span>
* <span data-ttu-id="7fb3f-157">Farklı bir etki alanında gRPC hizmetlerinin çağrılması için [CORS](xref:security/cors) 'nin sunucuda yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-157">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="7fb3f-158">JavaScript gRPC-Web istemcisi</span><span class="sxs-lookup"><span data-stu-id="7fb3f-158">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="7fb3f-159">Bir JavaScript gRPC-Web istemcisi vardır.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-159">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="7fb3f-160">JavaScript 'ten gRPC-Web kullanma hakkında yönergeler için bkz. [gRPC-web Ile JavaScript istemci kodu yazma](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span><span class="sxs-lookup"><span data-stu-id="7fb3f-160">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="7fb3f-161">.NET gRPC istemcisiyle gRPC-Web yapılandırma</span><span class="sxs-lookup"><span data-stu-id="7fb3f-161">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="7fb3f-162">.NET gRPC istemcisi, gRPC-Web çağrıları yapmak için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-162">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="7fb3f-163">Bu, [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) tarayıcıda barındırılan ve JavaScript koduyla aynı http kısıtlamalarına sahip olan uygulamalar için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-163">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="7fb3f-164">Bir .NET istemcisiyle gRPC-Web ' i çağırmak [http/2 gRPC ile aynıdır](xref:grpc/client).</span><span class="sxs-lookup"><span data-stu-id="7fb3f-164">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="7fb3f-165">Tek değişiklik, kanalın oluşturulma şekli olur.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-165">The only modification is how the channel is created.</span></span>

<span data-ttu-id="7fb3f-166">GRPC-Web kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="7fb3f-166">To use gRPC-Web:</span></span>

* <span data-ttu-id="7fb3f-167">[GRPC .net. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) paketine başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-167">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="7fb3f-168">[GRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) paketine yönelik başvurunun 2.29.0 veya daha büyük olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-168">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.29.0 or greater.</span></span>
* <span data-ttu-id="7fb3f-169">Kanalı şunu kullanacak şekilde yapılandırın `GrpcWebHandler` :</span><span class="sxs-lookup"><span data-stu-id="7fb3f-169">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="7fb3f-170">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="7fb3f-170">The preceding code:</span></span>

* <span data-ttu-id="7fb3f-171">GRPC-Web kullanmak için bir kanal yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-171">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="7fb3f-172">Bir istemci oluşturur ve kanalı kullanarak bir çağrı yapar.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-172">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="7fb3f-173">`GrpcWebHandler` Aşağıdaki yapılandırma seçeneklerine sahiptir:</span><span class="sxs-lookup"><span data-stu-id="7fb3f-173">`GrpcWebHandler` has the following configuration options:</span></span>

* <span data-ttu-id="7fb3f-174">**InnerHandler**:, <xref:System.Net.Http.HttpMessageHandler> Örneğin, GRPC http isteğini oluşturan temel `HttpClientHandler` .</span><span class="sxs-lookup"><span data-stu-id="7fb3f-174">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="7fb3f-175">**Grpcwebmode**: GRPC http isteğinin veya olup olmadığını belirten bir numaralandırma türü `Content-Type` `application/grpc-web` `application/grpc-web-text` .</span><span class="sxs-lookup"><span data-stu-id="7fb3f-175">**GrpcWebMode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="7fb3f-176">`GrpcWebMode.GrpcWeb` kodlamadan gönderilecek içeriği yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-176">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="7fb3f-177">Varsayılan değer.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-177">Default value.</span></span>
    * <span data-ttu-id="7fb3f-178">`GrpcWebMode.GrpcWebText` içeriği Base64 kodlamalı olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-178">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="7fb3f-179">Tarayıcılarda sunucu akış çağrıları için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-179">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="7fb3f-180">**HttpVersion**: `Version` TEMELDEKI GRPC http isteğindeki [HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) öğesini ayarlamak için kullanılan http protokolü.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-180">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="7fb3f-181">gRPC-Web belirli bir sürüm gerektirmez ve belirtilmediği takdirde varsayılanı geçersiz kılmaz.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-181">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="7fb3f-182">Oluşturulan gRPC istemcilerinin birli yöntemleri çağırmak için eşitleme ve zaman uyumsuz yöntemleri vardır.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-182">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="7fb3f-183">Örneğin, `SayHello` eşitlenir ve `SayHelloAsync` zaman uyumsuz olur.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-183">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="7fb3f-184">Bir uygulamada eşitleme yönteminin çağrılması Blazor WebAssembly uygulamanın yanıt vermemeye başlamasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="7fb3f-184">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="7fb3f-185">Zaman uyumsuz yöntemlerin içinde her zaman kullanılması gerekir Blazor WebAssembly .</span><span class="sxs-lookup"><span data-stu-id="7fb3f-185">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7fb3f-186">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7fb3f-186">Additional resources</span></span>

* [<span data-ttu-id="7fb3f-187">Web Istemcileri için gRPC GitHub projesi</span><span class="sxs-lookup"><span data-stu-id="7fb3f-187">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
