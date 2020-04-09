---
title: Tarayıcı uygulamalarında gRPC kullanma
author: jamesnk
description: gRPC-Web'i kullanarak tarayıcı uygulamalarından çağrılabilir olması için ASP.NET Core'daki gRPC hizmetlerini nasıl yapılandırabileceğinizi öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 02/16/2020
uid: grpc/browser
ms.openlocfilehash: 0bb8157525ccd32991d8925816c1b599c3d21a92
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977151"
---
# <a name="use-grpc-in-browser-apps"></a><span data-ttu-id="dbd02-103">Tarayıcı uygulamalarında gRPC kullanma</span><span class="sxs-lookup"><span data-stu-id="dbd02-103">Use gRPC in browser apps</span></span>

<span data-ttu-id="dbd02-104">Yazar: [James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="dbd02-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="dbd02-105">**.NET'te gRPC-Web desteği deneyseldir**</span><span class="sxs-lookup"><span data-stu-id="dbd02-105">**gRPC-Web support in .NET is experimental**</span></span>
>
> <span data-ttu-id="dbd02-106">.NET için gRPC-Web, taahhüt edilen bir ürün değil, deneysel bir projedir.</span><span class="sxs-lookup"><span data-stu-id="dbd02-106">gRPC-Web for .NET is an experimental project, not a committed product.</span></span> <span data-ttu-id="dbd02-107">Biz istiyorum:</span><span class="sxs-lookup"><span data-stu-id="dbd02-107">We want to:</span></span>
>
> * <span data-ttu-id="dbd02-108">gRPC-Web'i uygulama yaklaşımımızın işe yaradığını test edin.</span><span class="sxs-lookup"><span data-stu-id="dbd02-108">Test that our approach to implementing gRPC-Web works.</span></span>
> * <span data-ttu-id="dbd02-109">Bu yaklaşımın .NET geliştiricileri için bir proxy aracılığıyla gRPC-Web'i kurmanın geleneksel yolu ile karşılaştırıldığında yararlı olup olmadığını hakkında geri bildirim alın.</span><span class="sxs-lookup"><span data-stu-id="dbd02-109">Get feedback on if this approach is useful to .NET developers compared to the traditional way of setting up gRPC-Web via a proxy.</span></span>
>
> <span data-ttu-id="dbd02-110">Geliştiricilerin beğendikleri ve üretken oldukları bir şey oluşturduğumuzdan emin olmak [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) için lütfen geri bildirimde bulundurun.</span><span class="sxs-lookup"><span data-stu-id="dbd02-110">Please leave feedback at [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="dbd02-111">Tarayıcı tabanlı bir uygulamadan HTTP/2 gRPC hizmetini aramak mümkün değildir.</span><span class="sxs-lookup"><span data-stu-id="dbd02-111">It is not possible to call a HTTP/2 gRPC service from a browser-based app.</span></span> <span data-ttu-id="dbd02-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) tarayıcı JavaScript ve Blazor uygulamaları gRPC hizmetleri aramak için izin veren bir protokoldür.</span><span class="sxs-lookup"><span data-stu-id="dbd02-112">[gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) is a protocol that allows browser JavaScript and Blazor apps to call gRPC services.</span></span> <span data-ttu-id="dbd02-113">Bu makalede, .NET Core'da gRPC-Web'in nasıl kullanılacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="dbd02-113">This article explains how to use gRPC-Web in .NET Core.</span></span>

## <a name="configure-grpc-web-in-aspnet-core"></a><span data-ttu-id="dbd02-114">gRPC-Web'i ASP.NET Core'da yapılandırın</span><span class="sxs-lookup"><span data-stu-id="dbd02-114">Configure gRPC-Web in ASP.NET Core</span></span>

<span data-ttu-id="dbd02-115">ASP.NET Core'da barındırılan gRPC hizmetleri, HTTP/2 gRPC ile birlikte gRPC-Web'i destekleyecek şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="dbd02-115">gRPC services hosted in ASP.NET Core can be configured to support gRPC-Web alongside HTTP/2 gRPC.</span></span> <span data-ttu-id="dbd02-116">gRPC-Web hizmetlerde herhangi bir değişiklik gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="dbd02-116">gRPC-Web does not require any changes to services.</span></span> <span data-ttu-id="dbd02-117">Tek değişiklik başlangıç yapılandırmasıdır.</span><span class="sxs-lookup"><span data-stu-id="dbd02-117">The only modification is startup configuration.</span></span>

<span data-ttu-id="dbd02-118">ASP.NET Core gRPC hizmeti ile gRPC-Web'i etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="dbd02-118">To enable gRPC-Web with an ASP.NET Core gRPC service:</span></span>

* <span data-ttu-id="dbd02-119">[Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) paketine bir başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="dbd02-119">Add a reference to the [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) package.</span></span>
* <span data-ttu-id="dbd02-120">Uygulamayı gRPC-Web'i ekleyerek `AddGrpcWeb` ve `UseGrpcWeb` *Startup.cs*kullanacak şekilde yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="dbd02-120">Configure the app to use gRPC-Web by adding `AddGrpcWeb` and `UseGrpcWeb` to *Startup.cs*:</span></span>

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

<span data-ttu-id="dbd02-121">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="dbd02-121">The preceding code:</span></span>

* <span data-ttu-id="dbd02-122">gRPC-Web ara yazılımını, `UseGrpcWeb`yönlendirmeden sonra ve uç noktalardan önce ekler.</span><span class="sxs-lookup"><span data-stu-id="dbd02-122">Adds the gRPC-Web middleware, `UseGrpcWeb`, after routing and before endpoints.</span></span>
* <span data-ttu-id="dbd02-123">`endpoints.MapGrpcService<GreeterService>()` Yöntemi gRPC-Web ile `EnableGrpcWeb`destekler belirtir.</span><span class="sxs-lookup"><span data-stu-id="dbd02-123">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports gRPC-Web with `EnableGrpcWeb`.</span></span> 

<span data-ttu-id="dbd02-124">Alternatif olarak, ConfigureServices'e ekleyerek `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` gRPC-Web'i desteklemek için tüm hizmetleri yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="dbd02-124">Alternatively, configure all services to support gRPC-Web by adding `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` to ConfigureServices.</span></span>

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> <span data-ttu-id="dbd02-125">GRPC-Web'in .NET Core [3.x'te Http.sys tarafından barındırıldığında](xref:fundamentals/servers/httpsys) başarısız olmasına neden olan bilinen bir sorun vardır.</span><span class="sxs-lookup"><span data-stu-id="dbd02-125">There is a known issue that causes gRPC-Web to fail when [hosted by Http.sys](xref:fundamentals/servers/httpsys) in .NET Core 3.x.</span></span>
>
> <span data-ttu-id="dbd02-126">Http.sys üzerinde çalışan gRPC-Web almak için bir geçici çözüm [burada](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="dbd02-126">A workaround to get gRPC-Web working on Http.sys is available [here](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202).</span></span>

### <a name="grpc-web-and-cors"></a><span data-ttu-id="dbd02-127">gRPC-Web ve CORS</span><span class="sxs-lookup"><span data-stu-id="dbd02-127">gRPC-Web and CORS</span></span>

<span data-ttu-id="dbd02-128">Tarayıcı güvenliği, bir web sayfasının web sayfasına hizmet eden den farklı bir etki alanına istekte bulunmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="dbd02-128">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="dbd02-129">Bu kısıtlama tarayıcı uygulamaları yla gRPC-Web aramaları yapmak için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="dbd02-129">This restriction applies to making gRPC-Web calls with browser apps.</span></span> <span data-ttu-id="dbd02-130">Örneğin, hizmet verilen bir `https://www.contoso.com` tarayıcı uygulamasının gRPC-Web hizmetlerini `https://services.contoso.com`barındıran ' ı araması engellenir.</span><span class="sxs-lookup"><span data-stu-id="dbd02-130">For example, a browser app served by `https://www.contoso.com` is blocked from calling gRPC-Web services hosted on `https://services.contoso.com`.</span></span> <span data-ttu-id="dbd02-131">Cross Origin Kaynak Paylaşımı (CORS) bu kısıtlamayı gevşetmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="dbd02-131">Cross Origin Resource Sharing (CORS) can be used to relax this restriction.</span></span>

<span data-ttu-id="dbd02-132">Tarayıcı uygulamanızın orjinal gRPC-Web aramaları yapabilmesine izin vermek [için, ASP.NET Core'da CORS'u](xref:security/cors)ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="dbd02-132">To allow your browser app to make cross-origin gRPC-Web calls, set up [CORS in ASP.NET Core](xref:security/cors).</span></span> <span data-ttu-id="dbd02-133">Yerleşik CORS desteğini kullanın ve gRPC'ye özgü <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>üstbilgideki leri açığa çıkar.</span><span class="sxs-lookup"><span data-stu-id="dbd02-133">Use the built-in CORS support, and expose gRPC-specific headers with <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>.</span></span>

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

<span data-ttu-id="dbd02-134">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="dbd02-134">The preceding code:</span></span>

* <span data-ttu-id="dbd02-135">CORS hizmetlerini eklemek için çağrılar `AddCors` ve gRPC'ye özgü üstbilgiler ortaya çıkaran bir CORS ilkesini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="dbd02-135">Calls `AddCors` to add CORS services and configures a CORS policy that exposes gRPC-specific headers.</span></span>
* <span data-ttu-id="dbd02-136">Yönlendirmeden sonra ve bitiş noktalarından önce CORS ara yazılımını eklemek için çağrılar. `UseCors`</span><span class="sxs-lookup"><span data-stu-id="dbd02-136">Calls `UseCors` to add the CORS middleware after routing and before endpoints.</span></span>
* <span data-ttu-id="dbd02-137">`endpoints.MapGrpcService<GreeterService>()` Yöntem ile CORS destekler `RequiresCors`belirtir.</span><span class="sxs-lookup"><span data-stu-id="dbd02-137">Specifies the `endpoints.MapGrpcService<GreeterService>()` method supports CORS with `RequiresCors`.</span></span>

## <a name="call-grpc-web-from-the-browser"></a><span data-ttu-id="dbd02-138">tarayıcıdan gRPC-Web'i arayın</span><span class="sxs-lookup"><span data-stu-id="dbd02-138">Call gRPC-Web from the browser</span></span>

<span data-ttu-id="dbd02-139">Tarayıcı uygulamaları gRPC hizmetlerini aramak için gRPC-Web'i kullanabilir.</span><span class="sxs-lookup"><span data-stu-id="dbd02-139">Browser apps can use gRPC-Web to call gRPC services.</span></span> <span data-ttu-id="dbd02-140">Tarayıcıdan gRPC-Web ile gRPC hizmetlerini ararken bazı gereksinimler ve sınırlamalar vardır:</span><span class="sxs-lookup"><span data-stu-id="dbd02-140">There are some requirements and limitations when calling gRPC services with gRPC-Web from the browser:</span></span>

* <span data-ttu-id="dbd02-141">Sunucu gRPC-Web'i destekleyecek şekilde yapılandırılmış olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="dbd02-141">The server must have been configured to support gRPC-Web.</span></span>
* <span data-ttu-id="dbd02-142">İstemci akışı ve çift yönlü akış çağrıları desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="dbd02-142">Client streaming and bidirectional streaming calls aren't supported.</span></span> <span data-ttu-id="dbd02-143">Sunucu akışı desteklenir.</span><span class="sxs-lookup"><span data-stu-id="dbd02-143">Server streaming is supported.</span></span>
* <span data-ttu-id="dbd02-144">GRPC hizmetlerinin farklı bir etki alanında çağrılması, [CORS'in](xref:security/cors) sunucuda yapılandırılmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="dbd02-144">Calling gRPC services on a different domain requires [CORS](xref:security/cors) to be configured on the server.</span></span>

### <a name="javascript-grpc-web-client"></a><span data-ttu-id="dbd02-145">JavaScript gRPC-Web istemcisi</span><span class="sxs-lookup"><span data-stu-id="dbd02-145">JavaScript gRPC-Web client</span></span>

<span data-ttu-id="dbd02-146">Bir JavaScript gRPC-Web istemcisi vardır.</span><span class="sxs-lookup"><span data-stu-id="dbd02-146">There is a JavaScript gRPC-Web client.</span></span> <span data-ttu-id="dbd02-147">JavaScript'ten gRPC-Web'in nasıl kullanılacağına ilişkin talimatlar için [bkz.](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)</span><span class="sxs-lookup"><span data-stu-id="dbd02-147">For instructions on how to use gRPC-Web from JavaScript, see [write JavaScript client code with gRPC-Web](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).</span></span>

### <a name="configure-grpc-web-with-the-net-grpc-client"></a><span data-ttu-id="dbd02-148">gRPC-Web'i .NET gRPC istemcisi ile yapılandırın</span><span class="sxs-lookup"><span data-stu-id="dbd02-148">Configure gRPC-Web with the .NET gRPC client</span></span>

<span data-ttu-id="dbd02-149">.NET gRPC istemcisi gRPC-Web aramaları yapacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="dbd02-149">The .NET gRPC client can be configured to make gRPC-Web calls.</span></span> <span data-ttu-id="dbd02-150">Bu, tarayıcıda barındırılan ve JavaScript kodunun aynı HTTP sınırlamaları olan [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) uygulamaları için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="dbd02-150">This is useful for [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) apps, which are hosted in the browser and have the same HTTP limitations of JavaScript code.</span></span> <span data-ttu-id="dbd02-151">gRPC-Web'i .NET istemcisi ile aramak [HTTP/2 gRPC ile aynıdır.](xref:grpc/client)</span><span class="sxs-lookup"><span data-stu-id="dbd02-151">Calling gRPC-Web with a .NET client is [the same as HTTP/2 gRPC](xref:grpc/client).</span></span> <span data-ttu-id="dbd02-152">Tek değişiklik kanalın nasıl oluşturulduğudur.</span><span class="sxs-lookup"><span data-stu-id="dbd02-152">The only modification is how the channel is created.</span></span>

<span data-ttu-id="dbd02-153">gRPC-Web'i kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="dbd02-153">To use gRPC-Web:</span></span>

* <span data-ttu-id="dbd02-154">[Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) paketine bir başvuru ekleyin.</span><span class="sxs-lookup"><span data-stu-id="dbd02-154">Add a reference to the [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) package.</span></span>
* <span data-ttu-id="dbd02-155">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) paketine başvuru2.27.0 veya daha büyük olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="dbd02-155">Ensure the reference to [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) package is 2.27.0 or greater.</span></span>
* <span data-ttu-id="dbd02-156">Kanalı kullanmak için `GrpcWebHandler`yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="dbd02-156">Configure the channel to use the `GrpcWebHandler`:</span></span>

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

<span data-ttu-id="dbd02-157">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="dbd02-157">The preceding code:</span></span>

* <span data-ttu-id="dbd02-158">bir kanalı gRPC-Web'i kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="dbd02-158">Configures a channel to use gRPC-Web.</span></span>
* <span data-ttu-id="dbd02-159">İstemci oluşturur ve kanalı kullanarak arama yapar.</span><span class="sxs-lookup"><span data-stu-id="dbd02-159">Creates a client and makes a call using the channel.</span></span>

<span data-ttu-id="dbd02-160">Oluşturulduğunda `GrpcWebHandler` aşağıdaki yapılandırma seçenekleri vardır:</span><span class="sxs-lookup"><span data-stu-id="dbd02-160">The `GrpcWebHandler` has the following configuration options when created:</span></span>

* <span data-ttu-id="dbd02-161">**InnerHandler**: <xref:System.Net.Http.HttpMessageHandler> örneğin `HttpClientHandler`gRPC HTTP isteğini oluşturan temel.</span><span class="sxs-lookup"><span data-stu-id="dbd02-161">**InnerHandler**: The underlying <xref:System.Net.Http.HttpMessageHandler> that makes the gRPC HTTP request, for example, `HttpClientHandler`.</span></span>
* <span data-ttu-id="dbd02-162">**Mod**: gRPC HTTP istek isteğinin `Content-Type` olup olmadığını `application/grpc-web` belirten bir `application/grpc-web-text`numaralandırma türü.</span><span class="sxs-lookup"><span data-stu-id="dbd02-162">**Mode**: An enumeration type that specifies whether the gRPC HTTP request request `Content-Type` is `application/grpc-web` or `application/grpc-web-text`.</span></span>
    * <span data-ttu-id="dbd02-163">`GrpcWebMode.GrpcWeb`kodlar olmadan gönderilecek içeriği yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="dbd02-163">`GrpcWebMode.GrpcWeb` configures content to be sent without encoding.</span></span> <span data-ttu-id="dbd02-164">Varsayılan değer.</span><span class="sxs-lookup"><span data-stu-id="dbd02-164">Default value.</span></span>
    * <span data-ttu-id="dbd02-165">`GrpcWebMode.GrpcWebText`içeriği base64 kodlanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="dbd02-165">`GrpcWebMode.GrpcWebText` configures content to be base64 encoded.</span></span> <span data-ttu-id="dbd02-166">Tarayıcılarda sunucu akışı aramaları için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="dbd02-166">Required for server streaming calls in browsers.</span></span>
* <span data-ttu-id="dbd02-167">**HttpVersion**: `Version` HTTP protokolü altta yatan gRPC HTTP isteği [httpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) ayarlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="dbd02-167">**HttpVersion**: HTTP protocol `Version` used to set [HttpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) on the underlying gRPC HTTP request.</span></span> <span data-ttu-id="dbd02-168">gRPC-Web belirli bir sürümü gerektirmez ve belirtilmedikçe varsayılangeçersiz kılmaz.</span><span class="sxs-lookup"><span data-stu-id="dbd02-168">gRPC-Web doesn't require a specific version and doesn't override the default unless specified.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="dbd02-169">Oluşturulan gRPC istemcileri unary yöntemleri aramak için eşitleme ve async yöntemleri var.</span><span class="sxs-lookup"><span data-stu-id="dbd02-169">Generated gRPC clients have sync and async methods for calling unary methods.</span></span> <span data-ttu-id="dbd02-170">Örneğin, `SayHello` eşitlenir `SayHelloAsync` ve async olduğunu.</span><span class="sxs-lookup"><span data-stu-id="dbd02-170">For example, `SayHello` is sync and `SayHelloAsync` is async.</span></span> <span data-ttu-id="dbd02-171">Blazor WebAssembly uygulamasında eşitleme yöntemini çağırmak, uygulamanın yanıt vermese neden olur.</span><span class="sxs-lookup"><span data-stu-id="dbd02-171">Calling a sync method in a Blazor WebAssembly app will cause the app to become unresponsive.</span></span> <span data-ttu-id="dbd02-172">Blazor WebAssembly'de async yöntemleri her zaman kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="dbd02-172">Async methods must always be used in Blazor WebAssembly.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dbd02-173">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="dbd02-173">Additional resources</span></span>

* [<span data-ttu-id="dbd02-174">Web Istemcileri GitHub projesi için gRPC</span><span class="sxs-lookup"><span data-stu-id="dbd02-174">gRPC for Web Clients GitHub project</span></span>](https://github.com/grpc/grpc-web)
* <xref:security/cors>
