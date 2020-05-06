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
# <a name="use-grpc-in-browser-apps"></a>Tarayıcı uygulamalarında gRPC kullanma

, [James bAyKiNg](https://twitter.com/jamesnk)

> [!IMPORTANT]
> **gRPC-.NET 'teki Web desteği deneysel**
>
> gRPC-.NET için Web, kaydedilmiş bir ürün değil, deneysel bir projem projem. Şunları yapmak istiyoruz:
>
> * GRPC-Web ' i uygulamaya yönelik yaklaşımımızı test edin.
> * Bu yaklaşım .NET geliştiricileri için, bir ara sunucu aracılığıyla gRPC-Web ayarlamanın geleneksel bir yolu ile karşılaştırıldığında yararlı olup olmadığını hakkında geri bildirim alın.
>
> Geliştiricilerin, ve ile [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) üretken olduğu bir şey oluşturduğumdan emin olmak için lütfen geri bildirimde bulunun.

Tarayıcı tabanlı bir uygulamadan HTTP/2 gRPC hizmetini çağırmak mümkün değildir. [GRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) , tarayıcı JavaScript ve Blazor uygulamalarının GRPC hizmetlerini çağırmasını sağlayan bir protokoldür. Bu makalede, .NET Core 'da gRPC-Web ' i kullanma açıklanmaktadır.

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a>gRPC-Web 'de ASP.NET Core ve Envoy 'ye karşı

GRPC-Web ' i bir ASP.NET Core uygulamasına eklemenin iki seçeneği vardır:

* ASP.NET Core 'de GRPC HTTP/2 ile birlikte gRPC-Web desteği. Bu seçenek, `Grpc.AspNetCore.Web` paket tarafından sağlanmış olan ara yazılımı kullanır.
* GRPC-Web ' i GRPC HTTP/2 ' ye çevirmek için [Envoy proxy 'nin](https://www.envoyproxy.io/) GRPC-Web desteğini kullanın. Çevrilen çağrı daha sonra ASP.NET Core uygulamasına iletilir.

Her yaklaşımın olumlu ve olumsuz yönleri vardır. Zaten uygulamanızın ortamında bir ara sunucu olarak kullanmak istiyorsanız, gRPC-Web desteği sağlamak için de kullanabilirsiniz. GRPC-Web için yalnızca ASP.NET Core `Grpc.AspNetCore.Web` gerektiren basit bir çözüm istiyorsanız iyi bir seçimdir.

## <a name="configure-grpc-web-in-aspnet-core"></a>ASP.NET Core 'de gRPC-Web yapılandırma

ASP.NET Core 'de barındırılan gRPC Hizmetleri, gRPC-Web ' i HTTP/2 gRPC ile birlikte destekleyecek şekilde yapılandırılabilir. gRPC-Web, hizmetlerde herhangi bir değişiklik yapılmasını gerektirmez. Tek değişiklik başlangıç yapılandırması ' dır.

GRPC-Web ' i bir ASP.NET Core gRPC hizmeti ile etkinleştirmek için:

* [GRPC. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) paketine bir başvuru ekleyin.
* Uygulamayı `AddGrpcWeb` `UseGrpcWeb` *Startup.cs*ekleyerek GRPC-Web kullanacak şekilde yapılandırın:

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

Yukarıdaki kod:

* Yönlendirme sonrasında ve bitiş noktalarından önce gRPC `UseGrpcWeb`-Web ara yazılımını ekler.
* Yöntemi, `endpoints.MapGrpcService<GreeterService>()` Ile `EnableGrpcWeb`GRPC-Web ' i destekler. 

Alternatif olarak, tüm hizmetleri, bir ConfigureServices 'e ekleyerek `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` GRPC-Web ' i destekleyecek şekilde yapılandırın.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> .NET Core 3. x içinde [http. sys tarafından barındırılırken](xref:fundamentals/servers/httpsys) GRPC-Web ' i, başarısız olmasına neden olan bilinen bir sorun vardır.
>
> GRPC 'yi almaya yönelik bir geçici çözüm olan http. sys üzerinde Web 'de çalışmaya [buradan](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)ulaşabilirsiniz.

### <a name="grpc-web-and-cors"></a>gRPC-Web ve CORS

Tarayıcı güvenliği, bir Web sayfasının Web sayfasını sunduğundan farklı bir etki alanına istek yapmasını engeller. Bu kısıtlama, tarayıcı uygulamalarıyla gRPC Web çağrıları yapmak için geçerlidir. Örneğin, tarafından `https://www.contoso.com` sunulan bir tarayıcı uygulamasının üzerinde `https://services.contoso.com`barındırılan GRPC-Web hizmetlerinden çağrılması engellenir. Bu kısıtlamayı rahatmak için çapraz kaynak kaynak paylaşımı (CORS) kullanılabilir.

Tarayıcı uygulamanızın çıkış noktaları arası gRPC-Web çağrıları yapmasına izin vermek için [ASP.NET Core 'de CORS](xref:security/cors)'yi ayarlayın. Yerleşik CORS desteğini kullanın ve gRPC 'ye özgü üst bilgileri ile <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>kullanıma sunun.

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

Yukarıdaki kod:

* CORS `AddCors` Hizmetleri ekleme ve GRPC 'ye özgü üst bilgileri kullanıma sunan bir CORS ilkesini yapılandırma çağrıları.
* Yönlendirmeden `UseCors` ve bitiş NOKTALARıNDAN önce CORS ara yazılımını ekleme çağrıları.
* CORS 'yi `endpoints.MapGrpcService<GreeterService>()` ile `RequiresCors`desteklerken yöntemi belirtir.

## <a name="call-grpc-web-from-the-browser"></a>Tarayıcıdan gRPC-Web 'i çağırma

Tarayıcı uygulamaları GRPC hizmetlerini çağırmak için gRPC-Web kullanabilir. GRPC hizmetlerini tarayıcıda GRPC-Web ile çağırırken bazı gereksinimler ve sınırlamalar vardır:

* Sunucu, gRPC-Web ' i destekleyecek şekilde yapılandırılmış olmalıdır.
* İstemci akışı ve çift yönlü akış çağrıları desteklenmez. Sunucu akışı destekleniyor.
* Farklı bir etki alanında gRPC hizmetlerinin çağrılması için [CORS](xref:security/cors) 'nin sunucuda yapılandırılması gerekir.

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC-Web istemcisi

Bir JavaScript gRPC-Web istemcisi vardır. JavaScript 'ten gRPC-Web kullanma hakkında yönergeler için bkz. [gRPC-web Ile JavaScript istemci kodu yazma](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>.NET gRPC istemcisiyle gRPC-Web yapılandırma

.NET gRPC istemcisi, gRPC-Web çağrıları yapmak için yapılandırılabilir. Bu, tarayıcıda barındırılan ve JavaScript koduyla aynı http kısıtlamalarına sahip olan [ Blazor webassembly](xref:blazor/index#blazor-webassembly) uygulamaları için kullanışlıdır. Bir .NET istemcisiyle gRPC-Web ' i çağırmak [http/2 gRPC ile aynıdır](xref:grpc/client). Tek değişiklik, kanalın oluşturulma şekli olur.

GRPC-Web kullanmak için:

* [GRPC .net. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) paketine başvuru ekleyin.
* [GRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) paketine yönelik başvurunun 2.27.0 veya daha büyük olduğundan emin olun.
* Kanalı şunu kullanacak şekilde yapılandırın `GrpcWebHandler`:

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

Yukarıdaki kod:

* GRPC-Web kullanmak için bir kanal yapılandırır.
* Bir istemci oluşturur ve kanalı kullanarak bir çağrı yapar.

, `GrpcWebHandler` Oluşturulduğunda aşağıdaki yapılandırma seçeneklerine sahiptir:

* **InnerHandler**:, örneğin <xref:System.Net.Http.HttpMessageHandler> , `HttpClientHandler`GRPC http isteğini oluşturan temel.
* **Mode**: GRPC http isteğinin `Content-Type` `application/grpc-web` veya `application/grpc-web-text`olup olmadığını belirten bir numaralandırma türü.
    * `GrpcWebMode.GrpcWeb`kodlamadan gönderilecek içeriği yapılandırır. Varsayılan değer.
    * `GrpcWebMode.GrpcWebText`içeriği Base64 kodlamalı olarak yapılandırır. Tarayıcılarda sunucu akış çağrıları için gereklidir.
* **HttpVersion**: temeldeki GRPC http `Version` Isteğindeki [HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) öğesini ayarlamak için kullanılan http protokolü. gRPC-Web belirli bir sürüm gerektirmez ve belirtilmediği takdirde varsayılanı geçersiz kılmaz.

> [!IMPORTANT]
> Oluşturulan gRPC istemcilerinin birli yöntemleri çağırmak için eşitleme ve zaman uyumsuz yöntemleri vardır. Örneğin, `SayHello` eşitlenir ve `SayHelloAsync` zaman uyumsuz olur. Bir Blazor webassembly uygulamasında bir Sync yönteminin çağrılması uygulamanın yanıt vermemeye başlamasına neden olur. Zaman uyumsuz yöntemlerin her zaman webassembly içinde Blazor kullanılması gerekir.

## <a name="additional-resources"></a>Ek kaynaklar

* [Web Istemcileri için gRPC GitHub projesi](https://github.com/grpc/grpc-web)
* <xref:security/cors>
