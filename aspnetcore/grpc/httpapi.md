---
title: GRPC 'den JSON Web API 'Leri oluşturma
author: jamesnk
description: GRPC Hizmetleri için JSON HTTP API 'Leri oluşturmayı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/28/2020
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
uid: grpc/httpapi
ms.openlocfilehash: 21f47b889014ad4ff66d4cb710aed0159298f0cc
ms.sourcegitcommit: 7258e94cf60c16e5b6883138e5e68516751ead0f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2020
ms.locfileid: "89102893"
---
# <a name="create-json-web-apis-from-grpc"></a><span data-ttu-id="73c71-103">GRPC 'den JSON Web API 'Leri oluşturma</span><span class="sxs-lookup"><span data-stu-id="73c71-103">Create JSON Web APIs from gRPC</span></span>

<span data-ttu-id="73c71-104">, [James bAyKiNg](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="73c71-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="73c71-105">gRPC HTTP API 'SI, kaydedilmiş bir ürün değil, deneysel bir projem projem.</span><span class="sxs-lookup"><span data-stu-id="73c71-105">gRPC HTTP API is an experimental project, not a committed product.</span></span> <span data-ttu-id="73c71-106">Şunları yapmak istiyoruz:</span><span class="sxs-lookup"><span data-stu-id="73c71-106">We want to:</span></span>
>
> * <span data-ttu-id="73c71-107">GRPC Hizmetleri için JSON Web API 'Leri oluşturma yaklaşımımızı test edin.</span><span class="sxs-lookup"><span data-stu-id="73c71-107">Test that our approach to creating JSON Web APIs for gRPC services works.</span></span>
> * <span data-ttu-id="73c71-108">Bu yaklaşım .NET geliştiricileri için yararlı olursa hakkında geri bildirim alın.</span><span class="sxs-lookup"><span data-stu-id="73c71-108">Get feedback on if this approach is useful to .NET developers.</span></span>
>
> <span data-ttu-id="73c71-109">Geliştiricilerin, ve ile üretken olduğu bir şey oluşturduğumdan emin olmak için lütfen [geri bildirimde bulunun](https://github.com/grpc/grpc-dotnet/issues/167) .</span><span class="sxs-lookup"><span data-stu-id="73c71-109">Please [leave feedback](https://github.com/grpc/grpc-dotnet/issues/167) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="73c71-110">gRPC, uygulamalar arasında iletişim kurmanın modern bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="73c71-110">gRPC is a modern way to communicate between apps.</span></span> <span data-ttu-id="73c71-111">gRPC, yüksek performanslı ve gerçek zamanlı hizmetler oluşturmak için HTTP/2, akış, prototip ve ileti sözleşmeleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="73c71-111">gRPC uses HTTP/2, streaming, Protobuf and message contracts to create high-performance, real-time services.</span></span>

<span data-ttu-id="73c71-112">GRPC ile ilgili bir sınırlama, her platformun tarafından kullanılabilir değildir.</span><span class="sxs-lookup"><span data-stu-id="73c71-112">One limitation with gRPC is not every platform can use it.</span></span> <span data-ttu-id="73c71-113">Tarayıcılar, HTTP/2 ' yi tam olarak desteklemez, REST ve JSON, tarayıcı uygulamalarına veri almanın birincil yoludur.</span><span class="sxs-lookup"><span data-stu-id="73c71-113">Browsers don't fully support HTTP/2, making REST and JSON the primary way to get data into browser apps.</span></span> <span data-ttu-id="73c71-114">GRPC 'nin sağladığı avantajlardan de, REST ve JSON Modern uygulamalarda önemli bir yerdir.</span><span class="sxs-lookup"><span data-stu-id="73c71-114">Even with the benefits that gRPC brings, REST and JSON have an important place in modern apps.</span></span> <span data-ttu-id="73c71-115">GRPC ve JSON Web API 'Leri oluşturmak ***,*** uygulama geliştirmeye istenmeyen ek yük getirir.</span><span class="sxs-lookup"><span data-stu-id="73c71-115">Building gRPC ***and*** JSON Web APIs adds unwanted overhead to app development.</span></span>

<span data-ttu-id="73c71-116">Bu belgede, gRPC Hizmetleri kullanılarak JSON Web API 'Lerinin nasıl oluşturulacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="73c71-116">This document discusses how to create JSON Web APIs using gRPC services.</span></span>

## <a name="grpc-http-api"></a><span data-ttu-id="73c71-117">gRPC HTTP API 'SI</span><span class="sxs-lookup"><span data-stu-id="73c71-117">gRPC HTTP API</span></span>

<span data-ttu-id="73c71-118">gRPC HTTP API 'SI, gRPC Hizmetleri için RESTAN JSON API 'Leri oluşturan ASP.NET Core için deneysel bir uzantıdır.</span><span class="sxs-lookup"><span data-stu-id="73c71-118">gRPC HTTP API is an experimental extension for ASP.NET Core that creates RESTful JSON APIs for gRPC services.</span></span> <span data-ttu-id="73c71-119">Bir kez yapılandırıldıktan sonra, gRPC HTTP API 'SI, uygulamaların, tanıdık HTTP kavramlarıyla gRPC hizmetlerini çağırmasını sağlar:</span><span class="sxs-lookup"><span data-stu-id="73c71-119">Once configured, gRPC HTTP API allows apps to call gRPC services with familiar HTTP concepts:</span></span>

* <span data-ttu-id="73c71-120">HTTP fiilleri</span><span class="sxs-lookup"><span data-stu-id="73c71-120">HTTP verbs</span></span>
* <span data-ttu-id="73c71-121">URL parametre bağlama</span><span class="sxs-lookup"><span data-stu-id="73c71-121">URL parameter binding</span></span>
* <span data-ttu-id="73c71-122">JSON istekleri/yanıtları</span><span class="sxs-lookup"><span data-stu-id="73c71-122">JSON requests/responses</span></span>

<span data-ttu-id="73c71-123">gRPC, Hizmetleri çağırmak için hala kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="73c71-123">gRPC can still be used to call services.</span></span>

### <a name="usage"></a><span data-ttu-id="73c71-124">Kullanım</span><span class="sxs-lookup"><span data-stu-id="73c71-124">Usage</span></span>

1. <span data-ttu-id="73c71-125">[Microsoft. AspNetCore. GRPC. HTTPAPI](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi)öğesine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="73c71-125">Add a package reference to [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span></span>
1. <span data-ttu-id="73c71-126">Hizmetleri ile *Startup.cs* 'ye kaydedin `AddGrpcHttpApi` .</span><span class="sxs-lookup"><span data-stu-id="73c71-126">Register services in *Startup.cs* with `AddGrpcHttpApi`.</span></span>
1. <span data-ttu-id="73c71-127">Projenize [Google/API/http. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) ve [Google/API/ek açıklamaları. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) dosyaları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="73c71-127">Add [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) and [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) files to your project.</span></span>
1. <span data-ttu-id="73c71-128">HTTP bağlamaları ve rotalar ile *. proto* dosyalarınıza GRPC yöntemlerine not ekleyin:</span><span class="sxs-lookup"><span data-stu-id="73c71-128">Annotate gRPC methods in your *.proto* files with HTTP bindings and routes:</span></span>

```protobuf
syntax = "proto3";

import "google/api/annotations.proto";

package greet;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {
    option (google.api.http) = {
      get: "v1/greeter/{name}"
    };
  }
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

<span data-ttu-id="73c71-129">`SayHello`GRPC yöntemi artık GRPC + Protoarabellek olarak ve bır HTTP API 'si olarak çağrılabilir:</span><span class="sxs-lookup"><span data-stu-id="73c71-129">The `SayHello` gRPC method can now be invoked as gRPC+Protobuf and as an HTTP API:</span></span>

* <span data-ttu-id="73c71-130">İsteyen `HTTP/1.1 GET /v1/greeter/world`</span><span class="sxs-lookup"><span data-stu-id="73c71-130">Request: `HTTP/1.1 GET /v1/greeter/world`</span></span>
* <span data-ttu-id="73c71-131">Yanıtıyla `{ "message": "Hello world" }`</span><span class="sxs-lookup"><span data-stu-id="73c71-131">Response: `{ "message": "Hello world" }`</span></span>

<span data-ttu-id="73c71-132">Sunucu günlükleri, HTTP çağrısının bir gRPC hizmeti tarafından yürütüldüğünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="73c71-132">Server logs show that the HTTP call is executed by a gRPC service.</span></span> <span data-ttu-id="73c71-133">gRPC HTTP API 'SI, gelen HTTP isteğini bir gRPC iletisine eşler ve yanıt iletisini JSON 'a dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="73c71-133">gRPC HTTP API maps the incoming HTTP request to a gRPC message, and then converts the response message to JSON.</span></span>

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET https://localhost:5001/v1/greeter/world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - v1/greeter/{name}'
info: Server.GreeterService[0]
      Sending hello to world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - v1/greeter/{name}'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.996ms 200 application/json
```

<span data-ttu-id="73c71-134">Bu, temel bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="73c71-134">This is a basic example.</span></span> <span data-ttu-id="73c71-135">Daha fazla özelleştirme seçeneği için bkz. [Httprule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) .</span><span class="sxs-lookup"><span data-stu-id="73c71-135">See [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) for more customization options.</span></span>

### <a name="grpc-http-api-vs-grpc-web"></a><span data-ttu-id="73c71-136">gRPC HTTP API vs gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="73c71-136">gRPC HTTP API vs gRPC-Web</span></span>

<span data-ttu-id="73c71-137">Hem gRPC HTTP API 'SI hem de gRPC-Web, gRPC hizmetlerinin bir tarayıcıdan çağrılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="73c71-137">Both gRPC HTTP API and gRPC-Web allow gRPC services to be called from a browser.</span></span> <span data-ttu-id="73c71-138">Bununla birlikte, her biri farklı şekilde olur:</span><span class="sxs-lookup"><span data-stu-id="73c71-138">However, the way each does this is different:</span></span>

* <span data-ttu-id="73c71-139">gRPC-Web tarayıcı uygulamalarının, GRPC-Web istemcisi ve prototiple tarayıcıda gRPC hizmetlerini çağırmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="73c71-139">gRPC-Web lets browser apps call gRPC services from the browser with the gRPC-Web client and Protobuf.</span></span> <span data-ttu-id="73c71-140">gRPC-Web tarayıcı uygulamasının bir gRPC istemcisi oluşturmasını gerektirir ve küçük ve hızlı prototip iletiler gönderme avantajına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="73c71-140">gRPC-Web requires the browser app generate a gRPC client, and has the advantage of sending small, fast Protobuf messages.</span></span>
* <span data-ttu-id="73c71-141">gRPC HTTP API 'SI, tarayıcı uygulamalarının gRPC hizmetlerini JSON ile yeniden bir API 'Ler olarak çağırmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="73c71-141">gRPC HTTP API allows browser apps to call gRPC services as if they were RESTful APIs with JSON.</span></span> <span data-ttu-id="73c71-142">Tarayıcı uygulamasının bir gRPC istemcisi oluşturması veya gRPC ile ilgili herhangi bir şeyi bilmeleri gerekmez.</span><span class="sxs-lookup"><span data-stu-id="73c71-142">The browser app doesn't need to generate a gRPC client or know anything about gRPC.</span></span>

<span data-ttu-id="73c71-143">GRPC HTTP API 'SI için oluşturulan istemci oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="73c71-143">No generated client is created for gRPC HTTP API.</span></span> <span data-ttu-id="73c71-144">Önceki `Greeter` hizmet tarayıcı JavaScript API 'leri kullanılarak çağrılabilir:</span><span class="sxs-lookup"><span data-stu-id="73c71-144">The previous `Greeter` service can be called using browser JavaScript APIs:</span></span>

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a><span data-ttu-id="73c71-145">Deneysel durum</span><span class="sxs-lookup"><span data-stu-id="73c71-145">Experimental status</span></span>

<span data-ttu-id="73c71-146">gRPC HTTP API 'SI bir deney.</span><span class="sxs-lookup"><span data-stu-id="73c71-146">gRPC HTTP API is an experiment.</span></span> <span data-ttu-id="73c71-147">Bu tamamlanmadı ve desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="73c71-147">It is not complete and it is not supported.</span></span> <span data-ttu-id="73c71-148">Bu teknolojiyle ilgileniyoruz ve App Developers 'ın aynı anda gRPC ve JSON hizmetlerini hızlı bir şekilde oluşturmalarına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="73c71-148">We're interested in this technology, and the ability it gives app developers to quickly create gRPC and JSON services at the same time.</span></span> <span data-ttu-id="73c71-149">GRPC HTTP API 'sini tamamlamaya yönelik bir taahhüt yoktur.</span><span class="sxs-lookup"><span data-stu-id="73c71-149">There is no commitment to completing the gRPC HTTP API.</span></span>

<span data-ttu-id="73c71-150">GRPC HTTP API 'sindeki geliştirici ilgisini ölçmek istiyoruz.</span><span class="sxs-lookup"><span data-stu-id="73c71-150">We want to gauge developer interest in gRPC HTTP API.</span></span> <span data-ttu-id="73c71-151">GRPC HTTP API 'Niz sizin için ilginç ise lütfen [geri bildirimde](https://github.com/grpc/grpc-dotnet/issues/167)bulunun.</span><span class="sxs-lookup"><span data-stu-id="73c71-151">If gRPC HTTP API is interesting to you then please [give feedback](https://github.com/grpc/grpc-dotnet/issues/167).</span></span>

## <a name="grpc-gateway"></a><span data-ttu-id="73c71-152">GRPC-ağ geçidi</span><span class="sxs-lookup"><span data-stu-id="73c71-152">grpc-gateway</span></span>

<span data-ttu-id="73c71-153">[GRPC-Gateway](https://grpc-ecosystem.github.io/grpc-gateway/) , GRPC hizmetlerinden YENIDEN oluşturulan JSON API 'leri oluşturmaya yönelik başka bir teknolojidir.</span><span class="sxs-lookup"><span data-stu-id="73c71-153">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) is another technology for creating RESTful JSON APIs from gRPC services.</span></span> <span data-ttu-id="73c71-154">HTTP kavramlarını gRPC hizmetleriyle eşlemek için aynı *. proto* ek açıklamalarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="73c71-154">It uses the same *.proto* annotations to map HTTP concepts to gRPC services.</span></span>

<span data-ttu-id="73c71-155">GRPC-Gateway ve gRPC HTTP API 'SI arasındaki en büyük fark GRPC-Gateway, bir ters proxy sunucu oluşturmak için kod oluşturmayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="73c71-155">The biggest difference between grpc-gateway and gRPC HTTP API is grpc-gateway uses code generation to create a reverse-proxy server.</span></span> <span data-ttu-id="73c71-156">Ters proxy, gRPC 'ye geri çağrı yapar ve ardından bunları gRPC hizmetine gönderir.</span><span class="sxs-lookup"><span data-stu-id="73c71-156">The reverse-proxy translates RESTful calls into gRPC and then sends them on to the gRPC service.</span></span>

<span data-ttu-id="73c71-157">GRPC-Gateway yükleme ve kullanımı için bkz. [GRPC-Gateway belgeleri](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html).</span><span class="sxs-lookup"><span data-stu-id="73c71-157">For installation and usage of grpc-gateway, see the [grpc-gateway documentation](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="73c71-158">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="73c71-158">Additional resources</span></span>

* [<span data-ttu-id="73c71-159">Google. api. HttpRule belgeleri</span><span class="sxs-lookup"><span data-stu-id="73c71-159">google.api.HttpRule documentation</span></span>](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
