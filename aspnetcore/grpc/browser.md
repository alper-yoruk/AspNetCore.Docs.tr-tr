---
title: Tarayıcı uygulamalarında gRPC kullanma
author: jamesnk
description: gRPC-Web'i kullanarak tarayıcı uygulamalarından çağrılabilir olması için ASP.NET Core'daki gRPC hizmetlerini nasıl yapılandırabileceğinizi öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 04/15/2020
uid: grpc/browser
ms.openlocfilehash: a20e604488b1fb919f18932599ba690bfa308f0c
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440772"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="36cf1-103">Tarayıcı uygulamalarında gRPC kullanma</span><span class="sxs-lookup"><span data-stu-id="36cf1-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="36cf1-104">Yazar: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="36cf1-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="36cf1-105">**.NET'te gRPC-Web desteği deneyseldir**</span><span class="sxs-lookup"><span data-stu-id="36cf1-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="36cf1-106">.NET için gRPC-Web, taahhüt edilen bir ürün değil, deneysel bir projedir.</span><span class="sxs-lookup"><span data-stu-id="36cf1-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="36cf1-107">Biz istiyorum:</span><span class="sxs-lookup"><span data-stu-id="36cf1-107">We want to:</span></span>
>
> * <span data-ttu-id="36cf1-108">gRPC-Web'i uygulama yaklaşımımızın işe yaradığını test edin.</span><span class="sxs-lookup"><span data-stu-id="36cf1-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="36cf1-109">Bu yaklaşımın .NET geliştiricileri için bir proxy aracılığıyla gRPC-Web'i kurmanın geleneksel yolu ile karşılaştırıldığında yararlı olup olmadığını hakkında geri bildirim alın.</span><span class="sxs-lookup"><span data-stu-id="36cf1-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="36cf1-110">Geliştiricilerin beğendikleri ve üretken oldukları bir şey oluşturduğumuzdan emin olmak [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) için lütfen geri bildirimde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="36cf1-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="36cf1-111">Tarayıcı tabanlı bir uygulamadan HTTP/2 gRPC hizmetini aramak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="36cf1-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="36cf1-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) tarayıcı JavaScript ve Blazor uygulamaları gRPC hizmetleri aramak için izin veren bir protokoldür.</span><span class="sxs-lookup"><span data-stu-id="36cf1-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="36cf1-113">Bu makalede, .NET Core'da gRPC-Web'in nasıl kullanılacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="36cf1-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a><span data-ttu-id="36cf1-114">gRPC-Web ASP.NET Core vs Elçi</span><span class="sxs-lookup"><span data-stu-id="36cf1-114">gRPC-Web in ASP.NET Core vs. Envoy</span></span>

<span data-ttu-id="36cf1-115">ASP.NET Core uygulamasına gRPC-Web eklemenin iki seçeneği vardır:</span><span class="sxs-lookup"><span data-stu-id="36cf1-115">There are two choices for how to add gRPC-Web to an ASP.NET Core app:</span></span>

* <span data-ttu-id="36cf1-116">ASP.NET Core'da gRPC HTTP/2 ile birlikte gRPC-Web'i destekleyin.</span><span class="sxs-lookup"><span data-stu-id="36cf1-116">Support gRPC-Web alongside gRPC HTTP/2 in ASP.NET Core.</span></span> <span data-ttu-id="36cf1-117">Bu seçenek, paket tarafından `Grpc.AspNetCore.Web` sağlanan ara yazılım kullanır.</span><span class="sxs-lookup"><span data-stu-id="36cf1-117">This option uses middleware provided by the `Grpc.AspNetCore.Web` package.</span></span>
* <span data-ttu-id="36cf1-118">GRPC-Web'i gRPC HTTP/2'ye çevirmek için [Elçi proxy'sinin](https://www.envoyproxy.io/) gRPC-Web desteğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="36cf1-118">Use the [Envoy proxy's](https://www.envoyproxy.io/) gRPC-Web support to translate gRPC-Web to gRPC HTTP/2.</span></span> <span data-ttu-id="36cf1-119">Çevrilen arama daha sonra ASP.NET Core uygulamasına iletilir.</span><span class="sxs-lookup"><span data-stu-id="36cf1-119">The translated call is then forwarded onto the ASP.NET Core app.</span></span>

<span data-ttu-id="36cf1-120">Her yaklaşımın artıları ve eksileri vardır.</span><span class="sxs-lookup"><span data-stu-id="36cf1-120">There are pros and cons to each approach.</span></span> <span data-ttu-id="36cf1-121">Elçisi uygulamanızın ortamında zaten proxy olarak kullanıyorsanız, bunu gRPC-Web desteği sağlamak için de kullanmanız anlamlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="36cf1-121">If you're already using Envoy as a proxy in your app's environment, it might make sense to also use it to provide gRPC-Web support.</span></span> <span data-ttu-id="36cf1-122">Eğer sadece ASP.NET Core gerektiren gRPC-Web için `Grpc.AspNetCore.Web` basit bir çözüm istiyorsanız, iyi bir seçimdir.</span><span class="sxs-lookup"><span data-stu-id="36cf1-122">If you want a simple solution for gRPC-Web that only requires ASP.NET Core, `Grpc.AspNetCore.Web` is a good choice.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="36cf1-123">gRPC-Web'i ASP.NET Core'da yapılandırın</span><span class="sxs-lookup"><span data-stu-id="36cf1-123">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="36cf1-124">ASP.NET Core'da barındırılan gRPC hizmetleri, HTTP/2 gRPC ile birlikte gRPC-Web'i destekleyecek şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="36cf1-124">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="36cf1-125">gRPC-Web hizmetlerde herhangi bir değişiklik gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="36cf1-125">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="36cf1-126">Tek değişiklik başlangıç yapılandırmasıdır.</span><span class="sxs-lookup"><span data-stu-id="36cf1-126">The only modification is startup configuration.</span></span>

<span data-ttu-id="36cf1-127">ASP.NET Core gRPC hizmeti ile gRPC-Web'i etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="36cf1-127">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="36cf1-128">[Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) paketine bir başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="36cf1-128">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="36cf1-129">Uygulamayı gRPC-Web'i ekleyerek `AddGrpcWeb` ve `UseGrpcWeb` *Startup.cs*kullanacak şekilde yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="36cf1-129">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="36cf1-130">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="36cf1-130">The preceding code:</span></span>

* <span data-ttu-id="36cf1-131">gRPC-Web ara yazılımını, `UseGrpcWeb`yönlendirmeden sonra ve uç noktalardan önce ekler.</span><span class="sxs-lookup"><span data-stu-id="36cf1-131">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="36cf1-132">`endpoints.MapGrpcService<GreeterService>()` Yöntemi gRPC-Web ile `EnableGrpcWeb`destekler belirtir.</span><span class="sxs-lookup"><span data-stu-id="36cf1-132">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="36cf1-133">Alternatif olarak, ConfigureServices'e ekleyerek `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` gRPC-Web'i desteklemek için tüm hizmetleri yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="36cf1-133">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> <span data-ttu-id="36cf1-134">GRPC-Web'in .NET Core [3.x'te Http.sys tarafından barındırıldığında](xref:fundamentals/servers/httpsys) başarısız olmasına neden olan bilinen bir sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="36cf1-134">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="36cf1-135">Http.sys üzerinde çalışan gRPC-Web almak için bir geçici çözüm [burada](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="36cf1-135">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="36cf1-136">gRPC-Web ve CORS</span><span class="sxs-lookup"><span data-stu-id="36cf1-136">gRPC-Web and CORS</span></span>

<span data-ttu-id="36cf1-137">Tarayıcı güvenliği, bir web sayfasının web sayfasına hizmet eden den farklı bir etki alanına istekte bulunmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="36cf1-137">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="36cf1-138">Bu kısıtlama tarayıcı uygulamaları yla gRPC-Web aramaları yapmak için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="36cf1-138">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="36cf1-139">Örneğin, hizmet verilen bir `https://www.contoso.com` tarayıcı uygulamasının gRPC-Web hizmetlerini `https://services.contoso.com`barındıran ' ı araması engellenir.</span><span class="sxs-lookup"><span data-stu-id="36cf1-139">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="36cf1-140">Cross Origin Kaynak Paylaşımı (CORS) bu kısıtlamayı gevşetmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="36cf1-140">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="36cf1-141">Tarayıcı uygulamanızın orjinal gRPC-Web aramaları yapabilmesine izin vermek [için, ASP.NET Core'da CORS'u](xref:security/cors)ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="36cf1-141">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="36cf1-142">Yerleşik CORS desteğini kullanın ve gRPC'ye özgü <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>üstbilgideki leri açığa çıkar.</span><span class="sxs-lookup"><span data-stu-id="36cf1-142">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="36cf1-143">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="36cf1-143">The preceding code:</span></span>

* <span data-ttu-id="36cf1-144">CORS hizmetlerini eklemek için çağrılar `AddCors` ve gRPC'ye özgü üstbilgiler ortaya çıkaran bir CORS ilkesini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="36cf1-144">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="36cf1-145">Yönlendirmeden sonra ve bitiş noktalarından önce CORS ara yazılımını eklemek için çağrılar. `UseCors`</span><span class="sxs-lookup"><span data-stu-id="36cf1-145">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="36cf1-146">`endpoints.MapGrpcService<GreeterService>()` Yöntem ile CORS destekler `RequiresCors`belirtir.</span><span class="sxs-lookup"><span data-stu-id="36cf1-146">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="36cf1-147">tarayıcıdan gRPC-Web'i arayın</span><span class="sxs-lookup"><span data-stu-id="36cf1-147">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="36cf1-148">Tarayıcı uygulamaları gRPC hizmetlerini aramak için gRPC-Web'i kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="36cf1-148">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="36cf1-149">Tarayıcıdan gRPC-Web ile gRPC hizmetlerini ararken bazı gereksinimler ve sınırlamalar vardır:</span><span class="sxs-lookup"><span data-stu-id="36cf1-149">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="36cf1-150">Sunucu gRPC-Web'i destekleyecek şekilde yapılandırılmış olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="36cf1-150">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="36cf1-151">İstemci akışı ve çift yönlü akış çağrıları desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="36cf1-151">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="36cf1-152">Sunucu akışı desteklenir.</span><span class="sxs-lookup"><span data-stu-id="36cf1-152">Server streaming is supported.</span></span>
* <span data-ttu-id="36cf1-153">GRPC hizmetlerinin farklı bir etki alanında çağrılması, [CORS'in](xref:security/cors) sunucuda yapılandırılmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="36cf1-153">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="36cf1-154">JavaScript gRPC-Web istemcisi</span><span class="sxs-lookup"><span data-stu-id="36cf1-154">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="36cf1-155">Bir JavaScript gRPC-Web istemcisi vardır.</span><span class="sxs-lookup"><span data-stu-id="36cf1-155">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="36cf1-156">JavaScript'ten gRPC-Web'in nasıl kullanılacağına ilişkin talimatlar için [bkz.](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)</span><span class="sxs-lookup"><span data-stu-id="36cf1-156">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="36cf1-157">gRPC-Web'i .NET gRPC istemcisi ile yapılandırın</span><span class="sxs-lookup"><span data-stu-id="36cf1-157">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="36cf1-158">.NET gRPC istemcisi gRPC-Web aramaları yapacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="36cf1-158">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="36cf1-159">Bu, tarayıcıda barındırılan ve JavaScript kodunun aynı HTTP sınırlamaları olan [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) uygulamaları için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="36cf1-159">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="36cf1-160">gRPC-Web'i .NET istemcisi ile aramak [HTTP/2 gRPC ile aynıdır.](xref:grpc/client)</span><span class="sxs-lookup"><span data-stu-id="36cf1-160">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="36cf1-161">Tek değişiklik kanalın nasıl oluşturulduğudur.</span><span class="sxs-lookup"><span data-stu-id="36cf1-161">The only modification is how the channel is created.</span></span>

<span data-ttu-id="36cf1-162">gRPC-Web'i kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="36cf1-162">To use gRPC-Web:</span></span>

* <span data-ttu-id="36cf1-163">[Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) paketine bir başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="36cf1-163">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="36cf1-164">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) paketine başvuru2.27.0 veya daha büyük olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="36cf1-164">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="36cf1-165">Kanalı kullanmak için `GrpcWebHandler`yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="36cf1-165">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="36cf1-166">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="36cf1-166">The preceding code:</span></span>

* <span data-ttu-id="36cf1-167">bir kanalı gRPC-Web'i kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="36cf1-167">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="36cf1-168">İstemci oluşturur ve kanalı kullanarak arama yapar.</span><span class="sxs-lookup"><span data-stu-id="36cf1-168">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="36cf1-169">Oluşturulduğunda `GrpcWebHandler` aşağıdaki yapılandırma seçenekleri vardır:</span><span class="sxs-lookup"><span data-stu-id="36cf1-169">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="36cf1-170">**InnerHandler**: <xref:System.Net.Http.HttpMessageHandler> örneğin `HttpClientHandler`gRPC HTTP isteğini oluşturan temel.</span><span class="sxs-lookup"><span data-stu-id="36cf1-170">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="36cf1-171">**Mod**: gRPC HTTP isteğinin `Content-Type` olup olmadığını `application/grpc-web` belirten bir `application/grpc-web-text`numaralandırma türü.</span><span class="sxs-lookup"><span data-stu-id="36cf1-171">**Mode**: An enumeration type that specifies whether the gRPC HTTP request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="36cf1-172">`GrpcWebMode.GrpcWeb`kodlar olmadan gönderilecek içeriği yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="36cf1-172">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="36cf1-173">Varsayılan değer.</span><span class="sxs-lookup"><span data-stu-id="36cf1-173">Default value.</span></span>
    * <span data-ttu-id="36cf1-174">`GrpcWebMode.GrpcWebText`içeriği base64 kodlanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="36cf1-174">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="36cf1-175">Tarayıcılarda sunucu akışı aramaları için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="36cf1-175">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="36cf1-176">**HttpVersion**: `Version` HTTP protokolü altta yatan gRPC HTTP isteği [httpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) ayarlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="36cf1-176">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="36cf1-177">gRPC-Web belirli bir sürümü gerektirmez ve belirtilmedikçe varsayılangeçersiz kılmaz.</span><span class="sxs-lookup"><span data-stu-id="36cf1-177">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="36cf1-178">Oluşturulan gRPC istemcileri unary yöntemleri aramak için eşitleme ve async yöntemleri var.</span><span class="sxs-lookup"><span data-stu-id="36cf1-178">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="36cf1-179">Örneğin, `SayHello` eşitlenir `SayHelloAsync` ve async olduğunu.</span><span class="sxs-lookup"><span data-stu-id="36cf1-179">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="36cf1-180">Blazor WebAssembly uygulamasında eşitleme yöntemini çağırmak, uygulamanın yanıt vermese neden olur.</span><span class="sxs-lookup"><span data-stu-id="36cf1-180">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="36cf1-181">Blazor WebAssembly'de async yöntemleri her zaman kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="36cf1-181">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="36cf1-182">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="36cf1-182">Additional resources</span></span>

* [<span data-ttu-id="36cf1-183">Web Istemcileri GitHub projesi için gRPC</span><span class="sxs-lookup"><span data-stu-id="36cf1-183">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
