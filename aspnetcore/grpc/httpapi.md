---
title: gRPC'den JSON Web API'leri oluşturma
author: jamesnk
description: GRPC Hizmetleri için JSON HTTP API 'Leri oluşturmayı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/28/2020
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
uid: grpc/httpapi
ms.openlocfilehash: cb2855f0293a6bc800bb5758cd1a8400d4434a24
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "96855474"
---
# <a name="create-json-web-apis-from-grpc"></a><span data-ttu-id="17253-103">gRPC'den JSON Web API'leri oluşturma</span><span class="sxs-lookup"><span data-stu-id="17253-103">Create JSON Web APIs from gRPC</span></span>

<span data-ttu-id="17253-104">, [James bAyKiNg](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="17253-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

> [!IMPORTANT]
> <span data-ttu-id="17253-105">gRPC HTTP API 'SI, kaydedilmiş bir ürün değil, deneysel bir projem projem.</span><span class="sxs-lookup"><span data-stu-id="17253-105">gRPC HTTP API is an experimental project, not a committed product.</span></span> <span data-ttu-id="17253-106">Şunları yapmak istiyoruz:</span><span class="sxs-lookup"><span data-stu-id="17253-106">We want to:</span></span>
>
> * <span data-ttu-id="17253-107">GRPC Hizmetleri için JSON Web API 'Leri oluşturma yaklaşımımızı test edin.</span><span class="sxs-lookup"><span data-stu-id="17253-107">Test that our approach to creating JSON Web APIs for gRPC services works.</span></span>
> * <span data-ttu-id="17253-108">Bu yaklaşım .NET geliştiricileri için yararlı olursa hakkında geri bildirim alın.</span><span class="sxs-lookup"><span data-stu-id="17253-108">Get feedback on if this approach is useful to .NET developers.</span></span>
>
> <span data-ttu-id="17253-109">Geliştiricilerin, ve ile üretken olduğu bir şey oluşturduğumdan emin olmak için lütfen [geri bildirimde bulunun](https://github.com/grpc/grpc-dotnet/issues/167) .</span><span class="sxs-lookup"><span data-stu-id="17253-109">Please [leave feedback](https://github.com/grpc/grpc-dotnet/issues/167) to ensure we build something that developers like and are productive with.</span></span>

<span data-ttu-id="17253-110">gRPC, uygulamalar arasında iletişim kurmanın modern bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="17253-110">gRPC is a modern way to communicate between apps.</span></span> <span data-ttu-id="17253-111">gRPC, yüksek performanslı ve gerçek zamanlı hizmetler oluşturmak için HTTP/2, akış, prototip ve ileti sözleşmeleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="17253-111">gRPC uses HTTP/2, streaming, Protobuf and message contracts to create high-performance, real-time services.</span></span>

<span data-ttu-id="17253-112">GRPC ile ilgili bir sınırlama, her platformun tarafından kullanılabilir değildir.</span><span class="sxs-lookup"><span data-stu-id="17253-112">One limitation with gRPC is not every platform can use it.</span></span> <span data-ttu-id="17253-113">Tarayıcılar, HTTP/2 ' yi tam olarak desteklemez, REST ve JSON, tarayıcı uygulamalarına veri almanın birincil yoludur.</span><span class="sxs-lookup"><span data-stu-id="17253-113">Browsers don't fully support HTTP/2, making REST and JSON the primary way to get data into browser apps.</span></span> <span data-ttu-id="17253-114">GRPC 'nin sağladığı avantajlardan de, REST ve JSON Modern uygulamalarda önemli bir yerdir.</span><span class="sxs-lookup"><span data-stu-id="17253-114">Even with the benefits that gRPC brings, REST and JSON have an important place in modern apps.</span></span> <span data-ttu-id="17253-115">GRPC \* ve _ JSON Web API 'Leri oluşturmak **,** uygulama geliştirmeye istenmeyen ek yük ekler.</span><span class="sxs-lookup"><span data-stu-id="17253-115">Building gRPC \***and** _ JSON Web APIs adds unwanted overhead to app development.</span></span>

<span data-ttu-id="17253-116">Bu belgede, gRPC Hizmetleri kullanılarak JSON Web API 'Lerinin nasıl oluşturulacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="17253-116">This document discusses how to create JSON Web APIs using gRPC services.</span></span>

## <a name="grpc-http-api"></a><span data-ttu-id="17253-117">gRPC HTTP API 'SI</span><span class="sxs-lookup"><span data-stu-id="17253-117">gRPC HTTP API</span></span>

<span data-ttu-id="17253-118">gRPC HTTP API 'SI, gRPC Hizmetleri için RESTAN JSON API 'Leri oluşturan ASP.NET Core için deneysel bir uzantıdır.</span><span class="sxs-lookup"><span data-stu-id="17253-118">gRPC HTTP API is an experimental extension for ASP.NET Core that creates RESTful JSON APIs for gRPC services.</span></span> <span data-ttu-id="17253-119">Bir kez yapılandırıldıktan sonra, gRPC HTTP API 'SI, uygulamaların, tanıdık HTTP kavramlarıyla gRPC hizmetlerini çağırmasını sağlar:</span><span class="sxs-lookup"><span data-stu-id="17253-119">Once configured, gRPC HTTP API allows apps to call gRPC services with familiar HTTP concepts:</span></span>

<span data-ttu-id="17253-120">_ HTTP fiilleri</span><span class="sxs-lookup"><span data-stu-id="17253-120">_ HTTP verbs</span></span>
* <span data-ttu-id="17253-121">URL parametre bağlama</span><span class="sxs-lookup"><span data-stu-id="17253-121">URL parameter binding</span></span>
* <span data-ttu-id="17253-122">JSON istekleri/yanıtları</span><span class="sxs-lookup"><span data-stu-id="17253-122">JSON requests/responses</span></span>

<span data-ttu-id="17253-123">gRPC, Hizmetleri çağırmak için hala kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="17253-123">gRPC can still be used to call services.</span></span>

### <a name="usage"></a><span data-ttu-id="17253-124">Kullanım</span><span class="sxs-lookup"><span data-stu-id="17253-124">Usage</span></span>

1. <span data-ttu-id="17253-125">[Microsoft. AspNetCore. GRPC. HTTPAPI](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi)öğesine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="17253-125">Add a package reference to [Microsoft.AspNetCore.Grpc.HttpApi](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi).</span></span>
1. <span data-ttu-id="17253-126">Hizmetleri ile *Startup.cs* 'ye kaydedin `AddGrpcHttpApi` .</span><span class="sxs-lookup"><span data-stu-id="17253-126">Register services in *Startup.cs* with `AddGrpcHttpApi`.</span></span>
1. <span data-ttu-id="17253-127">Projenize [Google/API/http. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) ve [Google/API/ek açıklamaları. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) dosyaları ekleyin.</span><span class="sxs-lookup"><span data-stu-id="17253-127">Add [google/api/http.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) and [google/api/annotations.proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) files to your project.</span></span>
1. <span data-ttu-id="17253-128">HTTP bağlamaları ve rotalar ile *. proto* dosyalarınıza GRPC yöntemlerine not ekleyin:</span><span class="sxs-lookup"><span data-stu-id="17253-128">Annotate gRPC methods in your *.proto* files with HTTP bindings and routes:</span></span>

```protobuf
syntax = "proto3";

import "google/api/annotations.proto";

package greet;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {
    option (google.api.http) = {
      get: "/v1/greeter/{name}"
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

<span data-ttu-id="17253-129">`SayHello`GRPC yöntemi artık GRPC + Protoarabellek olarak ve bır HTTP API 'si olarak çağrılabilir:</span><span class="sxs-lookup"><span data-stu-id="17253-129">The `SayHello` gRPC method can now be invoked as gRPC+Protobuf and as an HTTP API:</span></span>

* <span data-ttu-id="17253-130">İsteyen `HTTP/1.1 GET /v1/greeter/world`</span><span class="sxs-lookup"><span data-stu-id="17253-130">Request: `HTTP/1.1 GET /v1/greeter/world`</span></span>
* <span data-ttu-id="17253-131">Yanıtıyla `{ "message": "Hello world" }`</span><span class="sxs-lookup"><span data-stu-id="17253-131">Response: `{ "message": "Hello world" }`</span></span>

<span data-ttu-id="17253-132">Sunucu günlükleri, HTTP çağrısının bir gRPC hizmeti tarafından yürütüldüğünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="17253-132">Server logs show that the HTTP call is executed by a gRPC service.</span></span> <span data-ttu-id="17253-133">gRPC HTTP API 'SI, gelen HTTP isteğini bir gRPC iletisine eşler ve yanıt iletisini JSON 'a dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="17253-133">gRPC HTTP API maps the incoming HTTP request to a gRPC message, and then converts the response message to JSON.</span></span>

```
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET https://localhost:5001/v1/greeter/world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /v1/greeter/{name}'
info: Server.GreeterService[0]
      Sending hello to world
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /v1/greeter/{name}'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.996ms 200 application/json
```

<span data-ttu-id="17253-134">Bu, temel bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="17253-134">This is a basic example.</span></span> <span data-ttu-id="17253-135">Daha fazla özelleştirme seçeneği için bkz. [Httprule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) .</span><span class="sxs-lookup"><span data-stu-id="17253-135">See [HttpRule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) for more customization options.</span></span>

### <a name="enable-swaggeropenapi-support"></a><span data-ttu-id="17253-136">Swagger/Openapı desteğini etkinleştir</span><span class="sxs-lookup"><span data-stu-id="17253-136">Enable Swagger/OpenAPI support</span></span>

<span data-ttu-id="17253-137">Swagger (Openapı), REST API 'Leri açıklamak için dilden bağımsız bir belirtimdir.</span><span class="sxs-lookup"><span data-stu-id="17253-137">Swagger (OpenAPI) is a language-agnostic specification for describing REST APIs.</span></span> <span data-ttu-id="17253-138">gRPC HTTP API 'SI, Re, gRPC Hizmetleri için bir Swagger uç noktası oluşturmak üzere [swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) ile tümleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="17253-138">gRPC HTTP API can integrate with [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle.AspNetCore) to generate a Swagger endpoint for RESTful gRPC services.</span></span> <span data-ttu-id="17253-139">Swagger uç noktası daha sonra [Swagger Kullanıcı arabirimi](https://swagger.io/swagger-ui/) ve diğer araç ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="17253-139">The Swagger endpoint can then be used with [Swagger UI](https://swagger.io/swagger-ui/) and other tooling.</span></span>

<span data-ttu-id="17253-140">Swagger 'yi gRPC HTTP API 'SI ile etkinleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="17253-140">To enable Swagger with gRPC HTTP API:</span></span>

1. <span data-ttu-id="17253-141">[Microsoft. AspNetCore. GRPC. Swagger](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.Swagger)öğesine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="17253-141">Add a package reference to [Microsoft.AspNetCore.Grpc.Swagger](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.Swagger).</span></span>
2. <span data-ttu-id="17253-142">*Startup.cs* Içinde swashbuckle 'ı yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="17253-142">Configure Swashbuckle in *Startup.cs*.</span></span> <span data-ttu-id="17253-143">`AddGrpcSwagger`Yöntemi, gRPC HTTP API uç noktaları eklemek Için swashbuckle 'yi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="17253-143">The `AddGrpcSwagger` method configures Swashbuckle to include gRPC HTTP API endpoints.</span></span>

[!code-csharp[](~/grpc/httpapi/Startup.cs?name=snippet_1&highlight=6-10,15-19)]

<span data-ttu-id="17253-144">Swashbuckle 'nın, Restvıgrpc Hizmetleri için Swagger oluşturduğunu doğrulamak için, uygulamayı başlatın ve Swagger Kullanıcı arabirimi sayfasına gidin:</span><span class="sxs-lookup"><span data-stu-id="17253-144">To confirm that Swashbuckle is generating Swagger for the RESTful gRPC services, start the app and navigate to the Swagger UI page:</span></span>

![Swagger Kullanıcı arabirimi](~/grpc/httpapi/static/swaggerui.png)

### <a name="grpc-http-api-vs-grpc-web"></a><span data-ttu-id="17253-146">gRPC HTTP API vs gRPC-Web</span><span class="sxs-lookup"><span data-stu-id="17253-146">gRPC HTTP API vs gRPC-Web</span></span>

<span data-ttu-id="17253-147">Hem gRPC HTTP API 'SI hem de gRPC-Web, gRPC hizmetlerinin bir tarayıcıdan çağrılmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="17253-147">Both gRPC HTTP API and gRPC-Web allow gRPC services to be called from a browser.</span></span> <span data-ttu-id="17253-148">Bununla birlikte, her biri farklı şekilde olur:</span><span class="sxs-lookup"><span data-stu-id="17253-148">However, the way each does this is different:</span></span>

* <span data-ttu-id="17253-149">gRPC-Web tarayıcı uygulamalarının, GRPC-Web istemcisi ve prototiple tarayıcıda gRPC hizmetlerini çağırmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="17253-149">gRPC-Web lets browser apps call gRPC services from the browser with the gRPC-Web client and Protobuf.</span></span> <span data-ttu-id="17253-150">gRPC-Web tarayıcı uygulamasının bir gRPC istemcisi oluşturmasını gerektirir ve küçük ve hızlı prototip iletiler gönderme avantajına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="17253-150">gRPC-Web requires the browser app generate a gRPC client, and has the advantage of sending small, fast Protobuf messages.</span></span>
* <span data-ttu-id="17253-151">gRPC HTTP API 'SI, tarayıcı uygulamalarının gRPC hizmetlerini JSON ile yeniden bir API 'Ler olarak çağırmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="17253-151">gRPC HTTP API allows browser apps to call gRPC services as if they were RESTful APIs with JSON.</span></span> <span data-ttu-id="17253-152">Tarayıcı uygulamasının bir gRPC istemcisi oluşturması veya gRPC ile ilgili herhangi bir şeyi bilmeleri gerekmez.</span><span class="sxs-lookup"><span data-stu-id="17253-152">The browser app doesn't need to generate a gRPC client or know anything about gRPC.</span></span>

<span data-ttu-id="17253-153">GRPC HTTP API 'SI için oluşturulan istemci oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="17253-153">No generated client is created for gRPC HTTP API.</span></span> <span data-ttu-id="17253-154">Önceki `Greeter` hizmet tarayıcı JavaScript API 'leri kullanılarak çağrılabilir:</span><span class="sxs-lookup"><span data-stu-id="17253-154">The previous `Greeter` service can be called using browser JavaScript APIs:</span></span>

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a><span data-ttu-id="17253-155">Deneysel durum</span><span class="sxs-lookup"><span data-stu-id="17253-155">Experimental status</span></span>

<span data-ttu-id="17253-156">gRPC HTTP API 'SI bir deney.</span><span class="sxs-lookup"><span data-stu-id="17253-156">gRPC HTTP API is an experiment.</span></span> <span data-ttu-id="17253-157">Bu tamamlanmadı ve desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="17253-157">It is not complete and it is not supported.</span></span> <span data-ttu-id="17253-158">Bu teknolojiyle ilgileniyoruz ve App Developers 'ın aynı anda gRPC ve JSON hizmetlerini hızlı bir şekilde oluşturmalarına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="17253-158">We're interested in this technology, and the ability it gives app developers to quickly create gRPC and JSON services at the same time.</span></span> <span data-ttu-id="17253-159">GRPC HTTP API 'sini tamamlamaya yönelik bir taahhüt yoktur.</span><span class="sxs-lookup"><span data-stu-id="17253-159">There is no commitment to completing the gRPC HTTP API.</span></span>

<span data-ttu-id="17253-160">GRPC HTTP API 'sindeki geliştirici ilgisini ölçmek istiyoruz.</span><span class="sxs-lookup"><span data-stu-id="17253-160">We want to gauge developer interest in gRPC HTTP API.</span></span> <span data-ttu-id="17253-161">GRPC HTTP API 'Niz sizin için ilginç ise lütfen [geri bildirimde](https://github.com/grpc/grpc-dotnet/issues/167)bulunun.</span><span class="sxs-lookup"><span data-stu-id="17253-161">If gRPC HTTP API is interesting to you then please [give feedback](https://github.com/grpc/grpc-dotnet/issues/167).</span></span>

## <a name="grpc-gateway"></a><span data-ttu-id="17253-162">GRPC-ağ geçidi</span><span class="sxs-lookup"><span data-stu-id="17253-162">grpc-gateway</span></span>

<span data-ttu-id="17253-163">[GRPC-Gateway](https://grpc-ecosystem.github.io/grpc-gateway/) , GRPC hizmetlerinden YENIDEN oluşturulan JSON API 'leri oluşturmaya yönelik başka bir teknolojidir.</span><span class="sxs-lookup"><span data-stu-id="17253-163">[grpc-gateway](https://grpc-ecosystem.github.io/grpc-gateway/) is another technology for creating RESTful JSON APIs from gRPC services.</span></span> <span data-ttu-id="17253-164">HTTP kavramlarını gRPC hizmetleriyle eşlemek için aynı *. proto* ek açıklamalarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="17253-164">It uses the same *.proto* annotations to map HTTP concepts to gRPC services.</span></span>

<span data-ttu-id="17253-165">GRPC-Gateway ve gRPC HTTP API 'SI arasındaki en büyük fark GRPC-Gateway, bir ters proxy sunucu oluşturmak için kod oluşturmayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="17253-165">The biggest difference between grpc-gateway and gRPC HTTP API is grpc-gateway uses code generation to create a reverse-proxy server.</span></span> <span data-ttu-id="17253-166">Ters proxy, gRPC 'ye geri çağrı yapar ve ardından bunları gRPC hizmetine gönderir.</span><span class="sxs-lookup"><span data-stu-id="17253-166">The reverse-proxy translates RESTful calls into gRPC and then sends them on to the gRPC service.</span></span>

<span data-ttu-id="17253-167">GRPC-Gateway yükleme ve kullanımı için bkz. [GRPC-Gateway Benioku](https://github.com/grpc-ecosystem/grpc-gateway/#grpc-gateway).</span><span class="sxs-lookup"><span data-stu-id="17253-167">For installation and usage of grpc-gateway, see the [grpc-gateway README](https://github.com/grpc-ecosystem/grpc-gateway/#grpc-gateway).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="17253-168">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="17253-168">Additional resources</span></span>

* [<span data-ttu-id="17253-169">Google. api. HttpRule belgeleri</span><span class="sxs-lookup"><span data-stu-id="17253-169">google.api.HttpRule documentation</span></span>](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
