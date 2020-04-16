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
# <a name="use-grpc-in-browser-apps"></a>Tarayıcı uygulamalarında gRPC kullanma

Yazar: [James Newton-King](https://twitter.com/jamesnk)

> [!IMPORTANT]
> **.NET'te gRPC-Web desteği deneyseldir**
>
> .NET için gRPC-Web, taahhüt edilen bir ürün değil, deneysel bir projedir. Biz istiyorum:
>
> * gRPC-Web'i uygulama yaklaşımımızın işe yaradığını test edin.
> * Bu yaklaşımın .NET geliştiricileri için bir proxy aracılığıyla gRPC-Web'i kurmanın geleneksel yolu ile karşılaştırıldığında yararlı olup olmadığını hakkında geri bildirim alın.
>
> Geliştiricilerin beğendikleri ve üretken oldukları bir şey oluşturduğumuzdan emin olmak [https://github.com/grpc/grpc-dotnet](https://github.com/grpc/grpc-dotnet) için lütfen geri bildirimde bulundurun.

Tarayıcı tabanlı bir uygulamadan HTTP/2 gRPC hizmetini aramak mümkün değildir. [gRPC-Web](https://github.com/grpc/grpc/blob/master/doc/PROTOCOL-WEB.md) tarayıcı JavaScript ve Blazor uygulamaları gRPC hizmetleri aramak için izin veren bir protokoldür. Bu makalede, .NET Core'da gRPC-Web'in nasıl kullanılacağı açıklanmaktadır.

## <a name="grpc-web-in-aspnet-core-vs-envoy"></a>gRPC-Web ASP.NET Core vs Elçi

ASP.NET Core uygulamasına gRPC-Web eklemenin iki seçeneği vardır:

* ASP.NET Core'da gRPC HTTP/2 ile birlikte gRPC-Web'i destekleyin. Bu seçenek, paket tarafından `Grpc.AspNetCore.Web` sağlanan ara yazılım kullanır.
* GRPC-Web'i gRPC HTTP/2'ye çevirmek için [Elçi proxy'sinin](https://www.envoyproxy.io/) gRPC-Web desteğini kullanın. Çevrilen arama daha sonra ASP.NET Core uygulamasına iletilir.

Her yaklaşımın artıları ve eksileri vardır. Elçisi uygulamanızın ortamında zaten proxy olarak kullanıyorsanız, bunu gRPC-Web desteği sağlamak için de kullanmanız anlamlı olabilir. Eğer sadece ASP.NET Core gerektiren gRPC-Web için `Grpc.AspNetCore.Web` basit bir çözüm istiyorsanız, iyi bir seçimdir.

## <a name="configure-grpc-web-in-aspnet-core"></a>gRPC-Web'i ASP.NET Core'da yapılandırın

ASP.NET Core'da barındırılan gRPC hizmetleri, HTTP/2 gRPC ile birlikte gRPC-Web'i destekleyecek şekilde yapılandırılabilir. gRPC-Web hizmetlerde herhangi bir değişiklik gerektirmez. Tek değişiklik başlangıç yapılandırmasıdır.

ASP.NET Core gRPC hizmeti ile gRPC-Web'i etkinleştirmek için:

* [Grpc.AspNetCore.Web](https://www.nuget.org/packages/Grpc.AspNetCore.Web) paketine bir başvuru ekleyin.
* Uygulamayı gRPC-Web'i ekleyerek `AddGrpcWeb` ve `UseGrpcWeb` *Startup.cs*kullanacak şekilde yapılandırın:

[!code-csharp[](~/grpc/browser/sample/Startup.cs?name=snippet_1&highlight=10,14)]

Yukarıdaki kod:

* gRPC-Web ara yazılımını, `UseGrpcWeb`yönlendirmeden sonra ve uç noktalardan önce ekler.
* `endpoints.MapGrpcService<GreeterService>()` Yöntemi gRPC-Web ile `EnableGrpcWeb`destekler belirtir. 

Alternatif olarak, ConfigureServices'e ekleyerek `services.AddGrpcWeb(o => o.GrpcWebEnabled = true);` gRPC-Web'i desteklemek için tüm hizmetleri yapılandırın.

[!code-csharp[](~/grpc/browser/sample/AllServicesSupportExample_Startup.cs?name=snippet_1&highlight=6,13)]

> [!NOTE]
> GRPC-Web'in .NET Core [3.x'te Http.sys tarafından barındırıldığında](xref:fundamentals/servers/httpsys) başarısız olmasına neden olan bilinen bir sorun vardır.
>
> Http.sys üzerinde çalışan gRPC-Web almak için bir geçici çözüm [burada](https://github.com/grpc/grpc-dotnet/issues/853#issuecomment-610078202)mevcuttur.

### <a name="grpc-web-and-cors"></a>gRPC-Web ve CORS

Tarayıcı güvenliği, bir web sayfasının web sayfasına hizmet eden den farklı bir etki alanına istekte bulunmasını engeller. Bu kısıtlama tarayıcı uygulamaları yla gRPC-Web aramaları yapmak için geçerlidir. Örneğin, hizmet verilen bir `https://www.contoso.com` tarayıcı uygulamasının gRPC-Web hizmetlerini `https://services.contoso.com`barındıran ' ı araması engellenir. Cross Origin Kaynak Paylaşımı (CORS) bu kısıtlamayı gevşetmek için kullanılabilir.

Tarayıcı uygulamanızın orjinal gRPC-Web aramaları yapabilmesine izin vermek [için, ASP.NET Core'da CORS'u](xref:security/cors)ayarlayın. Yerleşik CORS desteğini kullanın ve gRPC'ye özgü <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>üstbilgideki leri açığa çıkar.

[!code-csharp[](~/grpc/browser/sample/CORS_Startup.cs?name=snippet_1&highlight=5-11,19,24)]

Yukarıdaki kod:

* CORS hizmetlerini eklemek için çağrılar `AddCors` ve gRPC'ye özgü üstbilgiler ortaya çıkaran bir CORS ilkesini yapılandırır.
* Yönlendirmeden sonra ve bitiş noktalarından önce CORS ara yazılımını eklemek için çağrılar. `UseCors`
* `endpoints.MapGrpcService<GreeterService>()` Yöntem ile CORS destekler `RequiresCors`belirtir.

## <a name="call-grpc-web-from-the-browser"></a>tarayıcıdan gRPC-Web'i arayın

Tarayıcı uygulamaları gRPC hizmetlerini aramak için gRPC-Web'i kullanabilir. Tarayıcıdan gRPC-Web ile gRPC hizmetlerini ararken bazı gereksinimler ve sınırlamalar vardır:

* Sunucu gRPC-Web'i destekleyecek şekilde yapılandırılmış olmalıdır.
* İstemci akışı ve çift yönlü akış çağrıları desteklenmez. Sunucu akışı desteklenir.
* GRPC hizmetlerinin farklı bir etki alanında çağrılması, [CORS'in](xref:security/cors) sunucuda yapılandırılmasını gerektirir.

### <a name="javascript-grpc-web-client"></a>JavaScript gRPC-Web istemcisi

Bir JavaScript gRPC-Web istemcisi vardır. JavaScript'ten gRPC-Web'in nasıl kullanılacağına ilişkin talimatlar için [bkz.](https://github.com/grpc/grpc-web/tree/master/net/grpc/gateway/examples/helloworld#write-client-code)

### <a name="configure-grpc-web-with-the-net-grpc-client"></a>gRPC-Web'i .NET gRPC istemcisi ile yapılandırın

.NET gRPC istemcisi gRPC-Web aramaları yapacak şekilde yapılandırılabilir. Bu, tarayıcıda barındırılan ve JavaScript kodunun aynı HTTP sınırlamaları olan [Blazor WebAssembly](xref:blazor/index#blazor-webassembly) uygulamaları için yararlıdır. gRPC-Web'i .NET istemcisi ile aramak [HTTP/2 gRPC ile aynıdır.](xref:grpc/client) Tek değişiklik kanalın nasıl oluşturulduğudur.

gRPC-Web'i kullanmak için:

* [Grpc.Net.Client.Web](https://www.nuget.org/packages/Grpc.Net.Client.Web) paketine bir başvuru ekleyin.
* [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) paketine başvuru2.27.0 veya daha büyük olduğundan emin olun.
* Kanalı kullanmak için `GrpcWebHandler`yapılandırın:

[!code-csharp[](~/grpc/browser/sample/Handler.cs?name=snippet_1)]

Yukarıdaki kod:

* bir kanalı gRPC-Web'i kullanacak şekilde yapılandırır.
* İstemci oluşturur ve kanalı kullanarak arama yapar.

Oluşturulduğunda `GrpcWebHandler` aşağıdaki yapılandırma seçenekleri vardır:

* **InnerHandler**: <xref:System.Net.Http.HttpMessageHandler> örneğin `HttpClientHandler`gRPC HTTP isteğini oluşturan temel.
* **Mod**: gRPC HTTP isteğinin `Content-Type` olup olmadığını `application/grpc-web` belirten bir `application/grpc-web-text`numaralandırma türü.
    * `GrpcWebMode.GrpcWeb`kodlar olmadan gönderilecek içeriği yapılandırır. Varsayılan değer.
    * `GrpcWebMode.GrpcWebText`içeriği base64 kodlanacak şekilde yapılandırır. Tarayıcılarda sunucu akışı aramaları için gereklidir.
* **HttpVersion**: `Version` HTTP protokolü altta yatan gRPC HTTP isteği [httpRequestMessage.Version](xref:System.Net.Http.HttpRequestMessage.Version) ayarlamak için kullanılır. gRPC-Web belirli bir sürümü gerektirmez ve belirtilmedikçe varsayılangeçersiz kılmaz.

> [!IMPORTANT]
> Oluşturulan gRPC istemcileri unary yöntemleri aramak için eşitleme ve async yöntemleri var. Örneğin, `SayHello` eşitlenir `SayHelloAsync` ve async olduğunu. Blazor WebAssembly uygulamasında eşitleme yöntemini çağırmak, uygulamanın yanıt vermese neden olur. Blazor WebAssembly'de async yöntemleri her zaman kullanılmalıdır.

## <a name="additional-resources"></a>Ek kaynaklar

* [Web Istemcileri GitHub projesi için gRPC](https://github.com/grpc/grpc-web)
* <xref:security/cors>
