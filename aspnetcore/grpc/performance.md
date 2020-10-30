---
title: GRPC ile en iyi performans uygulamaları
author: jamesnk
description: Yüksek performanslı gRPC hizmetleri oluşturmak için en iyi uygulamaları öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/performance
ms.openlocfilehash: 622c6ba042c5832f99bba379fadd9aba7d7163f2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060410"
---
# <a name="performance-best-practices-with-grpc"></a>GRPC ile en iyi performans uygulamaları

, [James bAyKiNg](https://twitter.com/jamesnk)

gRPC, yüksek performanslı hizmetler için tasarlanmıştır. Bu belgede, gRPC 'den mümkün olan en iyi performansı alma açıklanmaktadır.

## <a name="reuse-grpc-channels"></a>GRPC kanallarını yeniden kullanma

GRPC araması yapıldığında bir gRPC kanalının kullanılması gerekir. Bir kanalı yeniden kullanmak, çağrıların mevcut bir HTTP/2 bağlantısıyla çoğullanmış olmasına olanak sağlar.

Her gRPC çağrısı için yeni bir kanal oluşturulursa, tamamlanma süresi önemli ölçüde artabilir. Her çağrının, istemci ile sunucu arasında yeni bir HTTP/2 bağlantısı oluşturmak için birden çok ağ gidiş dönüşmesini gerekir:

1. Yuva açma
2. TCP bağlantısı kuruluyor
3. TLS anlaşması
4. HTTP/2 bağlantısı başlatılıyor
5. GRPC çağrısını yapma

Kanalların gRPC çağrıları arasında paylaşılması ve yeniden kullanılması güvenlidir:

* gRPC istemcileri kanallarla oluşturulur. gRPC istemcileri hafif nesnelerdir ve önbelleğe alınması veya yeniden kullanılması gerekmez.
* Farklı istemci türleri dahil olmak üzere bir kanaldan birden çok gRPC istemcisi oluşturulabilir.
* Kanaldan oluşturulan bir kanal ve istemciler, birden çok iş parçacığı tarafından güvenli bir şekilde kullanılabilir.
* Kanaldan oluşturulan istemciler birden çok eş zamanlı çağrı yapabilir.

gRPC istemci fabrikası, kanalları yapılandırmak için merkezi bir yöntem sunar. Temel kanalları otomatik olarak yeniden kullanır. Daha fazla bilgi için bkz. <xref:grpc/clientfactory>.

## <a name="connection-concurrency"></a>Bağlantı eşzamanlılık

HTTP/2 bağlantıları genellikle bir bağlantı üzerinde aynı anda [en fazla eşzamanlı akış (ETKIN http isteği)](https://httpwg.github.io/specs/rfc7540.html#rfc.section.5.1.2) sayısı için bir sınıra sahiptir. Varsayılan olarak, çoğu sunucu bu sınırı 100 eşzamanlı akış olarak ayarlar.

GRPC kanalı tek bir HTTP/2 bağlantısı kullanır ve eşzamanlı çağrılar bu bağlantı üzerinde çoğullanmış. Etkin çağrıların sayısı bağlantı akışı sınırına ulaştığında, istemci içinde ek çağrılar sıraya alınır. Kuyruğa alınan çağrılar, gönderilmeden önce etkin çağrıların tamamlanmasını bekler. Yüksek yük veya uzun süren akış gRPC çağrılarına sahip uygulamalar, bu sınır nedeniyle çağrı kuyruğu oluşturma nedeniyle oluşan performans sorunlarını görebilir.

::: moniker range=">= aspnetcore-5.0"

.NET 5, `SocketsHttpHandler.EnableMultipleHttp2Connections` özelliği tanıtır. Olarak ayarlandığında `true` , eş zamanlı akış sınırına ulaşıldığında ek http/2 bağlantıları bir kanal tarafından oluşturulur. Bir `GrpcChannel` oluşturulduğunda, iç, `SocketsHttpHandler` ek http/2 bağlantıları oluşturacak şekilde otomatik olarak yapılandırılır. Bir uygulama kendi işleyicisini yapılandırıyorsa, aşağıdakileri yapmak için aşağıdakileri göz önünde bulundurun `EnableMultipleHttp2Connections` `true` :

```csharp
var channel = GrpcChannel.ForAddress("https://localhost", new GrpcChannelOptions
{
    HttpHandler = new SocketsHttpHandler
    {
        EnableMultipleHttp2Connections = true,

        // ...configure other handler settings
    }
});
```

::: moniker-end

.NET Core 3,1 uygulamaları için birkaç geçici çözüm vardır:

* Yüksek yük ile uygulamanın bölgeleri için ayrı gRPC kanalları oluşturun. Örneğin, `Logger` GRPC hizmeti yüksek bir yüke sahip olabilir. Uygulamada oluşturmak için ayrı bir kanal kullanın `LoggerClient` .
* GRPC kanalları havuzunu kullanın, örneğin, gRPC kanallarının bir listesini oluşturun. `Random` Her bir gRPC kanalı gerektiğinde listeden bir kanal seçmek için kullanılır. `Random`Birden çok bağlantı üzerinde rastgele bir şekilde çağrı dağıtır.

> [!IMPORTANT]
> Sunucuda maksimum eşzamanlı akış sınırının artırılması, bu sorunu çözmenin başka bir yoludur. Kestrel içinde, ile yapılandırılır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> .
>
> En fazla eşzamanlı akış sınırının artırılması önerilmez. Tek bir HTTP/2 bağlantısında çok fazla akış yeni performans sorunları sunuyor:
>
> * Bağlantıya yazmaya çalışan akışlar arasında iş parçacığı çakışması.
> * Bağlantı paketi kaybı, TCP katmanında tüm çağrıların engellenmesine neden olur.

## <a name="load-balancing"></a>Yük dengeleme

Bazı yük dengeleyiciler gRPC ile etkili bir şekilde çalışmaz. L4 (taşıma) yük dengeleyiciler, TCP bağlantılarını uç noktalar arasında dağıtarak bağlantı düzeyinde çalışır. Bu yaklaşım, HTTP/1.1 ile yapılan Yük Dengeleme API çağrılarının yüklenmesi için iyi bir sonuç verir. HTTP/1.1 ile yapılan eşzamanlı çağrılar farklı bağlantılarda gönderilir ve çağrılar, uç noktalar arasında yük dengelemesi yapılmasına izin verir.

L4 yük dengeleyiciler bir bağlantı düzeyinde çalıştığından, gRPC ile iyi çalışmaz. gRPC, tek bir TCP bağlantısında birden çok çağrının çoğullanmış HTTP/2 kullanır. Bu bağlantı üzerindeki tüm gRPC çağrıları bir uç noktaya gider.

GRPC 'nin yükünü etkin şekilde dengelemek için iki seçenek vardır:

* İstemci tarafı Yük Dengeleme
* L7 (uygulama) proxy Yük Dengeleme

> [!NOTE]
> Yalnızca gRPC çağrıları uç noktalar arasında yük dengelenebilir. Akış gRPC çağrısı kurulduktan sonra, akış üzerinden gönderilen tüm iletiler bir uç noktaya gider.

### <a name="client-side-load-balancing"></a>İstemci tarafı Yük Dengeleme

İstemci tarafı yük dengelemesi ile istemci, uç noktalar hakkında bilir. Her gRPC çağrısı için, çağrısını göndermek üzere farklı bir uç nokta seçer. Gecikme süresi önemli olduğunda istemci tarafı yük dengelemesi iyi bir seçenektir. İstemci ile hizmet arasında, çağrının hizmete doğrudan gönderilmesi için bir ara sunucu yok. İstemci tarafı yük dengelemenin dezavantajı, her istemcinin kullanması gereken kullanılabilir uç noktaları izlemesine sahip olması gerekir.

Arabelleği istemci yük dengelemesi, Yük Dengeleme durumunun merkezi bir konumda depolandığı bir tekniktir. İstemciler, Yük Dengeleme kararları verirken kullanılacak bilgiler için merkezi konumu düzenli aralıklarla sorgular.

`Grpc.Net.Client` Şu anda istemci tarafı yük dengelemeyi desteklemez. .NET ' te istemci tarafı yük dengelemesi gerekliyse, [GRPC. Core](https://www.nuget.org/packages/Grpc.Core) iyi bir seçimdir.

### <a name="proxy-load-balancing"></a>Proxy yük dengelemesi

Bir L7 (uygulama) proxy 'si, bir L4 (aktarım) proxy 'si tarafından daha yüksek bir düzeyde çalışmaktadır. L7 proxy 'leri HTTP/2 ' yi anlayın ve birden fazla uç nokta genelinde tek bir HTTP/2 bağlantısı üzerinde gRPC çağrılarını bir tane sunucuya dağıtabiliyor. Proxy kullanmak, istemci tarafı yük dengelemeden daha basittir, ancak gRPC çağrılarına ek gecikme süresi ekleyebilir.

Kullanılabilir çok sayıda L7 proxy vardır. Bazı seçenekler şunlardır:

* [Envoy](https://www.envoyproxy.io/) -popüler bir açık kaynak proxy 'si.
* Kubernetes için [Linkerd](https://linkerd.io/) -hizmet ağı.
* [Yarp: bir ters ara sunucu](https://microsoft.github.io/reverse-proxy/) -.net ' te yazılmış bir önizleme açık kaynak proxy 'si.

::: moniker range=">= aspnetcore-5.0"

## <a name="inter-process-communication"></a>İşlemler arası iletişim

istemci ve hizmet arasındaki gRPC çağrıları genellikle TCP Yuvaları üzerinden gönderilir. TCP bir ağ üzerinden iletişim kurmak için idealdir, ancak istemci ve hizmet aynı makinede olduğunda, [işlemler arası iletişim (IPC)](https://wikipedia.org/wiki/Inter-process_communication) daha etkilidir.

Aynı makinede bulunan süreçler arasında gRPC çağrıları için UNIX etki alanı yuvaları veya adlandırılmış kanallar gibi bir taşıma kullanmayı düşünün. Daha fazla bilgi için bkz. <xref:grpc/interprocess>.

## <a name="keep-alive-pings"></a>Canlı ping pingleri tut

Canlı tut ping işlemleri, etkin olmama süreleri boyunca HTTP/2 bağlantılarının etkin kalmasını sağlamak için kullanılabilir. Mevcut bir HTTP/2 bağlantısının, bir uygulamanın etkinliği sürdürülürse, yeniden yüklenmekte olan bağlantının neden olduğu bir gecikme süresi olmadan, ilk gRPC çağrılarının hızla yapılmasına izin verir.

Etkin tutma pingleri şu şekilde yapılandırılır <xref:System.Net.Http.SocketsHttpHandler> :

```csharp
var handler = new SocketsHttpHandler
{
    PooledConnectionIdleTimeout = Timeout.InfiniteTimeSpan,
    KeepAlivePingDelay = TimeSpan.FromSeconds(60),
    KeepAlivePingTimeout = TimeSpan.FromSeconds(30),
    EnableMultipleHttp2Connections = true
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    HttpHandler = handler
});
```

Yukarıdaki kod, işlem yapılmayan dönemler sırasında her 60 saniyede sunucuya canlı tutmayı canlı tut iletisi gönderen bir kanalı yapılandırır. Ping işlemi, sunucunun ve kullanımda olan tüm proxy 'lerin, etkin olmama nedeniyle bağlantıyı kapatmamasını sağlar.

::: moniker-end

## <a name="streaming"></a>Akış

gRPC çift yönlü akışı, yüksek performanslı senaryolarda birli gRPC çağrılarını değiştirmek için kullanılabilir. Çift yönlü bir akış başlatıldıktan sonra, akış iletileri geri ve ileri, birden çok birli gRPC çağrısı içeren iletiler göndermekten daha hızlıdır. Akışlı iletiler, var olan bir HTTP/2 isteğine veri olarak gönderilir ve her birli çağrı için yeni bir HTTP/2 isteği oluşturma yükünü ortadan kaldırır.

Örnek hizmet:

```csharp
public override async Task SayHello(IAsyncStreamReader<HelloRequest> requestStream,
    IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
{
    await foreach (var request in requestStream.ReadAllAsync())
    {
        var helloReply = new HelloReply { Message = "Hello " + request.Name };

        await responseStream.WriteAsync(helloReply);
    }
}
```

Örnek istemci:

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHello();

Console.WriteLine("Type a name then press enter.");
while (true)
{
    var text = Console.ReadLine();

    // Send and receive messages over the stream
    await call.RequestStream.WriteAsync(new HelloRequest { Name = text });
    await call.ResponseStream.MoveNext();

    Console.WriteLine($"Greeting: {call.ResponseStream.Current.Message}");
}
```

Çift yönlü akışla birli çağrıları performans nedenleriyle değiştirmek, gelişmiş bir tekniktir ve birçok durumda uygun değildir.

Akış çağrılarının kullanılması şu durumlarda iyi bir seçimdir:

1. Yüksek aktarım hızı veya düşük gecikme süresi gereklidir.
2. gRPC ve HTTP/2 performans sorunu olarak tanımlanır.
3. İstemcideki bir çalışan, gRPC hizmeti ile normal iletiler gönderiyor veya alıyor.

Birli yerine akış çağrılarını kullanmanın ek karmaşıklığı ve sınırlamalarından haberdar olun:

1. Bir akış, bir hizmet veya bağlantı hatası tarafından kesintiye uğrar. Bir hata varsa akışın yeniden başlatılması için mantık gereklidir.
2. `RequestStream.WriteAsync` Çoklu iş parçacığı oluşturma için güvenli değildir. Tek seferde bir akışa yalnızca bir ileti yazılabilir. Tek bir akış üzerinden birden çok iş parçacığından ileti göndermek için, Marshall iletileri gibi bir üretici/tüketici kuyruğu gerekir <xref:System.Threading.Channels.Channel%601> .
3. Bir gRPC akış yöntemi bir ileti türü alacak ve bir ileti türü göndermesiyle sınırlandırılmıştır. Örneğin, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` alır `RequestMessage` ve gönderir `ResponseMessage` . Ve kullanılarak bilinmeyen veya koşullu iletiler için protoarabellek desteği `Any` `oneof` Bu sınırlamaya geçici çözüm verebilir.
