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
ms.openlocfilehash: 45e2a1a5e6a9f00294147db769454b78c5b866e5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059942"
---
# <a name="create-json-web-apis-from-grpc"></a>gRPC'den JSON Web API'leri oluşturma

, [James bAyKiNg](https://twitter.com/jamesnk)

> [!IMPORTANT]
> gRPC HTTP API 'SI, kaydedilmiş bir ürün değil, deneysel bir projem projem. Şunları yapmak istiyoruz:
>
> * GRPC Hizmetleri için JSON Web API 'Leri oluşturma yaklaşımımızı test edin.
> * Bu yaklaşım .NET geliştiricileri için yararlı olursa hakkında geri bildirim alın.
>
> Geliştiricilerin, ve ile üretken olduğu bir şey oluşturduğumdan emin olmak için lütfen [geri bildirimde bulunun](https://github.com/grpc/grpc-dotnet/issues/167) .

gRPC, uygulamalar arasında iletişim kurmanın modern bir yoludur. gRPC, yüksek performanslı ve gerçek zamanlı hizmetler oluşturmak için HTTP/2, akış, prototip ve ileti sözleşmeleri kullanır.

GRPC ile ilgili bir sınırlama, her platformun tarafından kullanılabilir değildir. Tarayıcılar, HTTP/2 ' yi tam olarak desteklemez, REST ve JSON, tarayıcı uygulamalarına veri almanın birincil yoludur. GRPC 'nin sağladığı avantajlardan de, REST ve JSON Modern uygulamalarda önemli bir yerdir. GRPC * ve _ JSON Web API 'Leri oluşturmak **,** uygulama geliştirmeye istenmeyen ek yük ekler.

Bu belgede, gRPC Hizmetleri kullanılarak JSON Web API 'Lerinin nasıl oluşturulacağı açıklanmaktadır.

## <a name="grpc-http-api"></a>gRPC HTTP API 'SI

gRPC HTTP API 'SI, gRPC Hizmetleri için RESTAN JSON API 'Leri oluşturan ASP.NET Core için deneysel bir uzantıdır. Bir kez yapılandırıldıktan sonra, gRPC HTTP API 'SI, uygulamaların, tanıdık HTTP kavramlarıyla gRPC hizmetlerini çağırmasını sağlar:

_ HTTP fiilleri
* URL parametre bağlama
* JSON istekleri/yanıtları

gRPC, Hizmetleri çağırmak için hala kullanılabilir.

### <a name="usage"></a>Kullanım

1. [Microsoft. AspNetCore. GRPC. HTTPAPI](https://www.nuget.org/packages/Microsoft.AspNetCore.Grpc.HttpApi)öğesine bir paket başvurusu ekleyin.
1. Hizmetleri ile *Startup.cs* 'ye kaydedin `AddGrpcHttpApi` .
1. Projenize [Google/API/http. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/http.proto) ve [Google/API/ek açıklamaları. proto](https://github.com/aspnet/AspLabs/blob/c1e59cacf7b9606650d6ec38e54fa3a82377f360/src/GrpcHttpApi/sample/Proto/google/api/annotations.proto) dosyaları ekleyin.
1. HTTP bağlamaları ve rotalar ile *. proto* dosyalarınıza GRPC yöntemlerine not ekleyin:

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

`SayHello`GRPC yöntemi artık GRPC + Protoarabellek olarak ve bır HTTP API 'si olarak çağrılabilir:

* İsteyen `HTTP/1.1 GET /v1/greeter/world`
* Yanıtıyla `{ "message": "Hello world" }`

Sunucu günlükleri, HTTP çağrısının bir gRPC hizmeti tarafından yürütüldüğünü gösterir. gRPC HTTP API 'SI, gelen HTTP isteğini bir gRPC iletisine eşler ve yanıt iletisini JSON 'a dönüştürür.

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

Bu, temel bir örnektir. Daha fazla özelleştirme seçeneği için bkz. [Httprule](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule) .

### <a name="grpc-http-api-vs-grpc-web"></a>gRPC HTTP API vs gRPC-Web

Hem gRPC HTTP API 'SI hem de gRPC-Web, gRPC hizmetlerinin bir tarayıcıdan çağrılmasına izin verir. Bununla birlikte, her biri farklı şekilde olur:

* gRPC-Web tarayıcı uygulamalarının, GRPC-Web istemcisi ve prototiple tarayıcıda gRPC hizmetlerini çağırmasını sağlar. gRPC-Web tarayıcı uygulamasının bir gRPC istemcisi oluşturmasını gerektirir ve küçük ve hızlı prototip iletiler gönderme avantajına sahiptir.
* gRPC HTTP API 'SI, tarayıcı uygulamalarının gRPC hizmetlerini JSON ile yeniden bir API 'Ler olarak çağırmasını sağlar. Tarayıcı uygulamasının bir gRPC istemcisi oluşturması veya gRPC ile ilgili herhangi bir şeyi bilmeleri gerekmez.

GRPC HTTP API 'SI için oluşturulan istemci oluşturulmaz. Önceki `Greeter` hizmet tarayıcı JavaScript API 'leri kullanılarak çağrılabilir:

```javascript
var name = nameInput.value;

fetch("/v1/greeter/" + name).then(function (response) {
  response.json().then(function (data) {
    console.log("Result: " + data.message);
  });
});
```

### <a name="experimental-status"></a>Deneysel durum

gRPC HTTP API 'SI bir deney. Bu tamamlanmadı ve desteklenmez. Bu teknolojiyle ilgileniyoruz ve App Developers 'ın aynı anda gRPC ve JSON hizmetlerini hızlı bir şekilde oluşturmalarına olanak tanır. GRPC HTTP API 'sini tamamlamaya yönelik bir taahhüt yoktur.

GRPC HTTP API 'sindeki geliştirici ilgisini ölçmek istiyoruz. GRPC HTTP API 'Niz sizin için ilginç ise lütfen [geri bildirimde](https://github.com/grpc/grpc-dotnet/issues/167)bulunun.

## <a name="grpc-gateway"></a>GRPC-ağ geçidi

[GRPC-Gateway](https://grpc-ecosystem.github.io/grpc-gateway/) , GRPC hizmetlerinden YENIDEN oluşturulan JSON API 'leri oluşturmaya yönelik başka bir teknolojidir. HTTP kavramlarını gRPC hizmetleriyle eşlemek için aynı *. proto* ek açıklamalarını kullanır.

GRPC-Gateway ve gRPC HTTP API 'SI arasındaki en büyük fark GRPC-Gateway, bir ters proxy sunucu oluşturmak için kod oluşturmayı kullanır. Ters proxy, gRPC 'ye geri çağrı yapar ve ardından bunları gRPC hizmetine gönderir.

GRPC-Gateway yükleme ve kullanımı için bkz. [GRPC-Gateway belgeleri](https://grpc-ecosystem.github.io/grpc-gateway/docs/usage.html).

## <a name="additional-resources"></a>Ek kaynaklar

* [Google. api. HttpRule belgeleri](https://cloud.google.com/service-infrastructure/docs/service-management/reference/rpc/google.api#google.api.HttpRule)
* <xref:grpc/browser>
