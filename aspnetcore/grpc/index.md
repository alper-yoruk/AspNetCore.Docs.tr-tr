---
title: .NET üzerinde gRPC 'ye giriş
author: juntaoluo
description: Kestrel Server ve ASP.NET Core Stack ile gRPC hizmetleri hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
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
uid: grpc/index
ms.openlocfilehash: 5820049aba90a2fbd06a23756b12ac9656c3b2c4
ms.sourcegitcommit: 83524f739dd25fbfa95ee34e95342afb383b49fe
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99057518"
---
# <a name="introduction-to-grpc-on-net"></a>.NET üzerinde gRPC 'ye giriş

[John Luo](https://github.com/juntaoluo) ve [James bAyKiNg](https://twitter.com/jamesnk)

[GRPC](https://grpc.io/docs/guides/) , dilden bağımsız, yüksek performanslı bir uzak yordam ÇAĞRıSı (RPC) çerçevesidir.

gRPC’nin başlıca avantajları şunlardır:
* Modern, yüksek performanslı, hafif RPC çerçevesi.
* Varsayılan olarak Protokol Arabellekleri kullanan, dilden bağımsız uygulamalara olanak veren Sözleşme öncelikli API geliştirme.
* Kesin tür belirtilmiş sunucular ve istemciler oluşturmak için çok sayıda dilde kullanılabilen araçlar.
* İstemci, sunucu ve iki yönlü akış çağrılarına yönelik destek.
* Protobuf ikili serileştirme ile azaltılmış ağ kullanımı.

Bu avantajlar, gRPC 'yi ideal hale getirir:
* Verimlilik açısından kritik olan hafif mikro hizmetler.
* Geliştirme için birden fazla dilin gerekli olduğu çok yönlü sistemleri.
* Akış isteklerini veya yanıtlarını işlemek için gereken noktadan noktaya gerçek zamanlı hizmetler.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="c-tooling-support-for-proto-files"></a>. Proto dosyaları için C# araçları desteği

gRPC, API geliştirmesi için bir sözleşmenin ilk yaklaşımını kullanır. Hizmetler ve mesajlar *\* . proto* dosyalarında tanımlanmıştır:

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

Hizmetler, istemciler ve iletiler için .NET türleri bir projedeki *\* . proto* dosyaları eklenerek otomatik olarak oluşturulur:

* [GRPC. Tools](https://www.nuget.org/packages/Grpc.Tools/) paketine bir paket başvurusu ekleyin.
* *\* . Proto* dosyalarını `<Protobuf>` öğe grubuna ekleyin.

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

GRPC araç desteği hakkında daha fazla bilgi için bkz <xref:grpc/basics> ..

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

`GreeterService``GreeterBase`, `Greeter` *\* . proto* dosyasındaki hizmetten oluşturulan türden devralır. Hizmet, *Startup.cs* içindeki istemciler için erişilebilir hale getirilir:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

ASP.NET Core 'de gRPC hizmetleri hakkında daha fazla bilgi edinmek için bkz <xref:grpc/aspnetcore> ..

## <a name="call-grpc-services-with-a-net-client"></a>Bir .NET istemcisiyle gRPC hizmetlerini çağırma

gRPC istemcileri [ *\* . proto* dosyalarından oluşturulan](xref:grpc/basics#generated-c-assets)somut istemci türleridir. Somut gRPC istemcisinde *\* . proto* dosyasındaki GRPC hizmetine çeviren yöntemler vardır.

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

GRPC istemcisi, bir gRPC hizmeti ile uzun süreli bağlantıyı temsil eden bir kanal kullanılarak oluşturulur. Kullanılarak bir kanal oluşturulabilir `GrpcChannel.ForAddress` .

İstemci oluşturma ve farklı hizmet yöntemlerini çağırma hakkında daha fazla bilgi için bkz <xref:grpc/client> ..

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
