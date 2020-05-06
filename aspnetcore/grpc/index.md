---
title: .NET Core 'da gRPC 'ye giriş
author: juntaoluo
description: Kestrel Server ve ASP.NET Core Stack ile gRPC hizmetleri hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/index
ms.openlocfilehash: 2d7d683051fd1eb97f3f57d75bd582109166a6cd
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768854"
---
# <a name="introduction-to-grpc-on-net-core"></a>.NET Core 'da gRPC 'ye giriş

[John Luo](https://github.com/juntaoluo) ve [James bAyKiNg](https://twitter.com/jamesnk)

[GRPC](https://grpc.io/docs/guides/) , dilden bağımsız, yüksek performanslı bir uzak yordam ÇAĞRıSı (RPC) çerçevesidir.

GRPC 'nin başlıca avantajları şunlardır:
* Modern, yüksek performanslı, hafif RPC çerçevesi.
* Sözleşme-ilk API geliştirmesi, varsayılan olarak protokol arabellekleri kullanarak, dilden bağımsız uygulamalar için izin verir.
* Birçok dilde araç, kesin türü belirtilmiş sunucu ve istemciler oluşturmak için kullanılabilir.
* İstemci, sunucu ve iki yönlü akış çağrılarını destekler.
* Protoarabelleğe ikili serileştirme ile azaltılmış ağ kullanımı.

Bu avantajlar, gRPC 'yi ideal hale getirir:
* Verimlilik açısından kritik olan hafif mikro hizmetler.
* Geliştirme için birden fazla dilin gerekli olduğu çok yönlü sistemleri.
* Akış isteklerini veya yanıtlarını işlemek için gereken noktadan noktaya gerçek zamanlı hizmetler.

## <a name="c-tooling-support-for-proto-files"></a>. Proto dosyaları için C# araçları desteği

gRPC, API geliştirmesi için bir sözleşmenin ilk yaklaşımını kullanır. Hizmetler ve mesajlar * \*. proto* dosyalarında tanımlanmıştır:

```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

Hizmetler, istemciler ve iletiler için .net türleri bir projedeki * \*. proto* dosyaları eklenerek otomatik olarak oluşturulur:

* [GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/) paketine bir paket başvurusu ekleyin.
* * \*. Proto* dosyalarını `<Protobuf>` öğe grubuna ekleyin.

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

GRPC araç desteği hakkında daha fazla bilgi için bkz <xref:grpc/basics>..

## <a name="grpc-services-on-aspnet-core"></a>ASP.NET Core gRPC Hizmetleri

gRPC Hizmetleri, ASP.NET Core üzerinde barındırılabilir. Hizmetler, günlüğe kaydetme, bağımlılık ekleme (dı), kimlik doğrulama ve yetkilendirme gibi popüler ASP.NET Core özelliklerle tam tümleştirmeye sahiptir.

GRPC hizmeti proje şablonu bir başlatıcı hizmeti sağlar:

```csharp
public class GreeterService : Greeter.GreeterBase
{
    private readonly ILogger<GreeterService> _logger;

    public GreeterService(ILogger<GreeterService> logger)
    {
        _logger = logger;
    }

    public override Task<HelloReply> SayHello(HelloRequest request,
        ServerCallContext context)
    {
        _logger.LogInformation("Saying hello to {Name}", request.Name);
        return Task.FromResult(new HelloReply 
        {
            Message = "Hello " + request.Name
        });
    }
}
```

`GreeterService``GreeterBase` `Greeter` *, \*. proto* dosyasındaki hizmetten oluşturulan türden devralır. Hizmet, *Startup.cs*içindeki istemciler için erişilebilir hale getirilir:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

ASP.NET Core 'de gRPC hizmetleri hakkında daha fazla bilgi edinmek için <xref:grpc/aspnetcore>bkz..

## <a name="call-grpc-services-with-a-net-client"></a>Bir .NET istemcisiyle gRPC hizmetlerini çağırma

GRPC istemcileri [ * \*. proto* dosyalarından oluşturulan](xref:grpc/basics#generated-c-assets)somut istemci türleridir. Somut GRPC istemcisinde * \*. proto* dosyasındaki GRPC hizmetine çeviren yöntemler vardır.

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

GRPC istemcisi, bir gRPC hizmeti ile uzun süreli bağlantıyı temsil eden bir kanal kullanılarak oluşturulur. Kullanılarak `GrpcChannel.ForAddress`bir kanal oluşturulabilir.

İstemci oluşturma ve farklı hizmet yöntemlerini çağırma hakkında daha fazla bilgi için bkz <xref:grpc/client>..

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
