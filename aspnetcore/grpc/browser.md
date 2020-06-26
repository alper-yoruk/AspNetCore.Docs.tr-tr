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
# <a name="use-grpc-in-browser-apps"></a>Tarayıcı uygulamalarında gRPC kullanma

, [James bAyKiNg](https://twitter.com/jamesnk)

Tarayıcı tabanlı bir uygulamadan HTTP/2 gRPC hizmetini çağırmak mümkün değildir. [GRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) , tarayıcı JavaScript ve Blazor uygulamalarının GRPC hizmetlerini çağırmasını sağlayan bir protokoldür. Bu makalede, .NET Core 'da gRPC-Web ' i kullanma açıklanmaktadır.

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a>gRPC-Web 'de ASP.NET Core ve Envoy 'ye karşı

GRPC-Web ' i bir ASP.NET Core uygulamasına eklemenin iki seçeneği vardır:

* ASP.NET Core 'de GRPC HTTP/2 ile birlikte gRPC-Web desteği. Bu seçenek, paket tarafından sağlanmış olan ara yazılımı kullanır `Grpc.AspNetCore.Web` .
* GRPC-Web ' i GRPC HTTP/2 ' ye çevirmek için [Envoy proxy 'nin](https://www.envoyproxy.io/) GRPC-Web desteğini kullanın. Çevrilen çağrı daha sonra ASP.NET Core uygulamasına iletilir.

Her yaklaşımın olumlu ve olumsuz yönleri vardır. Zaten uygulamanızın ortamında bir ara sunucu olarak kullanmak istiyorsanız, gRPC-Web desteği sağlamak için de kullanabilirsiniz. GRPC-Web için yalnızca ASP.NET Core gerektiren basit bir çözüm istiyorsanız `Grpc.AspNetCore.Web` iyi bir seçimdir.

## <a name="configure-grpc-web-in-aspnet-core"></a>ASP.NET Core 'de gRPC-Web yapılandırma

ASP.NET Core 'de barındırılan gRPC Hizmetleri, gRPC-Web ' i HTTP/2 gRPC ile birlikte destekleyecek şekilde yapılandırılabilir. gRPC-Web, hizmetlerde herhangi bir değişiklik yapılmasını gerektirmez. Tek değişiklik başlangıç yapılandırması ' dır.

GRPC-Web ' i bir ASP.NET Core gRPC hizmeti ile etkinleştirmek için:

* [GRPC. AspNetCore. Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) paketine bir başvuru ekleyin.
* Uygulamayı Startup.cs ekleyerek gRPC-Web kullanacak şekilde yapılandırın `UseGrpcWeb` `EnableGrpcWeb` : *Startup.cs*

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

Yukarıdaki kod:

* `UseGrpcWeb`Yönlendirme sonrasında ve bitiş noktalarından önce gRPC-Web ara yazılımını ekler.
* Yöntemi, `endpoints.MapGrpcService<GreeterService>()` Ile gRPC-Web ' i destekler `EnableGrpcWeb` . 

Alternatif olarak, gRPC-Web ara yazılımı, tüm hizmetlerin varsayılan olarak gRPC-Web 'i desteklemesi ve gerekli olmaması için yapılandırılabilir `EnableGrpcWeb` . `new GrpcWebOptions { DefaultEnabled = true }`Ara yazılımın ne zaman ekleneceğini belirtin.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=12)]

> [!NOTE]
> .NET Core 3. x içinde [Http.systarafından barındırılırken](xref:fundamentals/servers/httpsys) GRPC-Web ' i, başarısız olmasına neden olan bilinen bir sorun vardır.
>
> GRPC 'yi (Http.sys Web üzerinde çalışmaya yönelik bir geçici çözüm) [buradan](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)edinebilirsiniz.

### <a name="grpc-web-and-cors"></a>gRPC-Web ve CORS

Tarayıcı güvenliği, bir Web sayfasının Web sayfasını sunduğundan farklı bir etki alanına istek yapmasını engeller. Bu kısıtlama, tarayıcı uygulamalarıyla gRPC Web çağrıları yapmak için geçerlidir. Örneğin, tarafından sunulan bir tarayıcı uygulamasının `https://www.contoso.com` üzerinde barındırılan gRPC-Web hizmetlerinden çağrılması engellenir `https://services.contoso.com` . Bu kısıtlamayı rahatmak için çapraz kaynak kaynak paylaşımı (CORS) kullanılabilir.

Tarayıcı uygulamanızın çıkış noktaları arası gRPC-Web çağrıları yapmasına izin vermek için [ASP.NET Core 'de CORS](xref:security/cors)'yi ayarlayın. Yerleşik CORS desteğini kullanın ve gRPC 'ye özgü üst bilgileri ile kullanıma sunun <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> .

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

Yukarıdaki kod:

* `AddCors`CORS Hizmetleri ekleme ve gRPC 'ye özgü üst bilgileri kullanıma sunan BIR CORS ilkesini yapılandırma çağrıları.
* `UseCors`Yönlendirmeden ve bitiş noktalarından önce CORS ara yazılımını ekleme çağrıları.
* `endpoints.MapGrpcService<GreeterService>()`CORS 'yi ile desteklerken yöntemi belirtir `RequiresCors` .

## <a name="call-grpc-web-from-the-browser"></a>Tarayıcıdan gRPC-Web 'i çağırma

Tarayıcı uygulamaları GRPC hizmetlerini çağırmak için gRPC-Web kullanabilir. GRPC hizmetlerini tarayıcıda GRPC-Web ile çağırırken bazı gereksinimler ve sınırlamalar vardır:

* Sunucu, gRPC-Web ' i destekleyecek şekilde yapılandırılmış olmalıdır.
* İstemci akışı ve çift yönlü akış çağrıları desteklenmez. Sunucu akışı destekleniyor.
* Farklı bir etki alanında gRPC hizmetlerinin çağrılması için [CORS](xref:security/cors) 'nin sunucuda yapılandırılması gerekir.

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC-Web istemcisi

Bir JavaScript gRPC-Web istemcisi vardır. JavaScript 'ten gRPC-Web kullanma hakkında yönergeler için bkz. [gRPC-web Ile JavaScript istemci kodu yazma](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code).

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>.NET gRPC istemcisiyle gRPC-Web yapılandırma

.NET gRPC istemcisi, gRPC-Web çağrıları yapmak için yapılandırılabilir. Bu, [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) tarayıcıda barındırılan ve JavaScript koduyla aynı http kısıtlamalarına sahip olan uygulamalar için yararlıdır. Bir .NET istemcisiyle gRPC-Web ' i çağırmak [http/2 gRPC ile aynıdır](xref:grpc/client). Tek değişiklik, kanalın oluşturulma şekli olur.

GRPC-Web kullanmak için:

* [GRPC .net. Client. Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) paketine başvuru ekleyin.
* [GRPC .net. Client](https://www.nuget.org/packages/Grpc.Net.Client) paketine yönelik başvurunun 2.29.0 veya daha büyük olduğundan emin olun.
* Kanalı şunu kullanacak şekilde yapılandırın `GrpcWebHandler` :

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

Yukarıdaki kod:

* GRPC-Web kullanmak için bir kanal yapılandırır.
* Bir istemci oluşturur ve kanalı kullanarak bir çağrı yapar.

`GrpcWebHandler`Aşağıdaki yapılandırma seçeneklerine sahiptir:

* **InnerHandler**:, <xref:System.Net.Http.HttpMessageHandler> Örneğin, GRPC http isteğini oluşturan temel `HttpClientHandler` .
* **Grpcwebmode**: GRPC http isteğinin veya olup olmadığını belirten bir numaralandırma türü `Content-Type` `application/grpc-web` `application/grpc-web-text` .
    * `GrpcWebMode.GrpcWeb`kodlamadan gönderilecek içeriği yapılandırır. Varsayılan değer.
    * `GrpcWebMode.GrpcWebText`içeriği Base64 kodlamalı olarak yapılandırır. Tarayıcılarda sunucu akış çağrıları için gereklidir.
* **HttpVersion**: `Version` TEMELDEKI GRPC http isteğindeki [HttpRequestMessage. Version](xref:System.Net.Http.HttpRequestMessage.Version) öğesini ayarlamak için kullanılan http protokolü. gRPC-Web belirli bir sürüm gerektirmez ve belirtilmediği takdirde varsayılanı geçersiz kılmaz.

> [!IMPORTANT]
> Oluşturulan gRPC istemcilerinin birli yöntemleri çağırmak için eşitleme ve zaman uyumsuz yöntemleri vardır. Örneğin, `SayHello` eşitlenir ve `SayHelloAsync` zaman uyumsuz olur. Bir uygulamada eşitleme yönteminin çağrılması Blazor WebAssembly uygulamanın yanıt vermemeye başlamasına neden olur. Zaman uyumsuz yöntemlerin içinde her zaman kullanılması gerekir Blazor WebAssembly .

## <a name="additional-resources"></a>Ek kaynaklar

* [Web Istemcileri için gRPC GitHub projesi](https://github.com/grpc/grpc-web)
* <xref:security/cors>
