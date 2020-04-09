---
title: .NET Core'da gRPC'ye Giriş
author: juntaoluo
description: Kestrel sunucusu ve ASP.NET Core yığını ile gRPC hizmetleri hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/20/2019
uid: grpc/index
ms.openlocfilehash: d97eea1da28424680a3cfa38102637b1e20ff661
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667316"
---
# <a name="introduction-to-grpc-on-net-core"></a>.NET Core'da gRPC'ye Giriş

Yazar: [John Luo](https://github.com/juntaoluo) ve [James Newton-King](https://twitter.com/jamesnk)

[gRPC](https://grpc.io/docs/guides/) bir dil agnostik, yüksek performanslı Uzaktan Yordam Çağrısı (RPC) çerçevesidir.

gRPC'nin başlıca yararları şunlardır:
* Modern, yüksek performanslı, hafif RPC çerçevesi.
* Varsayılan olarak Protokol Arabellekleri kullanarak, dil agnostik uygulamalarına izin veren sözleşme ilk API geliştirme.
* Güçlü bir şekilde yazılan sunucular ve istemciler oluşturmak için birçok dil için kullanılabilir araç.
* İstemci, sunucu ve çift yönlü akış çağrılarını destekler.
* Protobuf ikili serileştirme ile azaltılmış ağ kullanımı.

Bu avantajlar gRPC'yi şunlar için ideal hale getirir:
* Verimliliğin kritik olduğu hafif mikro hizmetler.
* Geliştirme için birden fazla dilin gerekli olduğu polyglot sistemleri.
* Akış isteklerini veya yanıtlarını işlemesi gereken noktadan noktaya gerçek zamanlı hizmetler.

## <a name="c-tooling-support-for-proto-files"></a>C# .proto dosyaları için araçlama desteği

gRPC, API geliştirme için sözleşmeye ilk yaklaşım kullanır. Hizmetler ve iletiler * \*.proto* dosyalarında tanımlanır:

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

.Net hizmetleri, istemcileri ve iletileri için türleri otomatik olarak bir projeye * \*.proto* dosyaları dahil edilerek oluşturulur:

* [Grpc.Tools](https://www.nuget.org/packages/Grpc.Tools/) paketine bir paket başvurusu ekleyin.
* Öğe * \*grubuna .proto* dosyaları ekleyin. `<Protobuf>`

```xml
<ItemGroup>
  <Protobuf Include="Protos\greet.proto" />
</ItemGroup>
```

gRPC araç lama desteği hakkında <xref:grpc/basics>daha fazla bilgi için bkz.

## <a name="grpc-services-on-aspnet-core"></a>ASP.NET Core'da gRPC hizmetleri

gRPC hizmetleri ASP.NET Core'da barındırılabilir. Hizmetler, günlük, bağımlılık enjeksiyonu (DI), kimlik doğrulama ve yetkilendirme gibi popüler ASP.NET Core özellikleriyle tam entegrasyona sahiptir.

gRPC hizmet proje şablonu bir başlangıç hizmeti sağlar:

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

`GreeterService``GreeterBase` `Greeter` *.proto dosyasındaki hizmetten oluşturulan türden \** devralır. Hizmet *Startup.cs*istemciler için erişilebilir hale getirilir:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

ASP.NET Core'daki gRPC hizmetleri hakkında <xref:grpc/aspnetcore>daha fazla bilgi edinmek için bkz.

## <a name="call-grpc-services-with-a-net-client"></a>.NET istemcisi ile gRPC hizmetlerini arayın

gRPC istemcileri [ * \*.proto* dosyalarından oluşturulan](xref:grpc/basics#generated-c-assets)somut istemci türleridir. Somut gRPC istemcisi * \*.proto* dosyasındaki gRPC hizmetine çevrilen yöntemlere sahiptir.

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greeter.GreeterClient(channel);

var response = await client.SayHelloAsync(
    new HelloRequest { Name = "World" });

Console.WriteLine(response.Message);
```

Bir gRPC istemcisi, gRPC hizmetine uzun süreli bir bağlantıyı temsil eden bir kanal kullanılarak oluşturulur. Bir kanal kullanılarak `GrpcChannel.ForAddress`oluşturulabilir.

İstemci oluşturma ve farklı hizmet yöntemlerini <xref:grpc/client>arama hakkında daha fazla bilgi için bkz.

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:grpc/basics>
* <xref:grpc/aspnetcore>
* <xref:grpc/client>
* <xref:grpc/clientfactory>
* <xref:tutorials/grpc/grpc-start>
