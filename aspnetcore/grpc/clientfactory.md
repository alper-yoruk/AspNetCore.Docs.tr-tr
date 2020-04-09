---
title: gRPC istemci fabrika entegrasyonu .NET Core
author: jamesnk
description: İstemci fabrikasını kullanarak gRPC istemcilerini nasıl oluşturabilirsiniz öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 11/12/2019
no-loc:
- SignalR
uid: grpc/clientfactory
ms.openlocfilehash: 3042bb61367f8b9a9f3142217ad329270ab2cca5
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78667169"
---
# <a name="grpc-client-factory-integration-in-net-core"></a>gRPC istemci fabrika entegrasyonu .NET Core

gRPC entegrasyonu `HttpClientFactory` ile gRPC istemcileri oluşturmak için merkezi bir yol sunar. Tek [başına gRPC istemci örneklerini yapılandırmaya](xref:grpc/client)alternatif olarak kullanılabilir. Fabrika entegrasyonu [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet paketinde mevcuttur.

Fabrika aşağıdaki avantajları sunar:

* Mantıksal gRPC istemci örneklerini yapılandırmak için merkezi bir konum sağlar
* Altta yatan ömrü yönetir`HttpClientMessageHandler`
* Core gRPC hizmeti ASP.NETnde son tarihin otomatik olarak yayılması ve iptal edilmesi

## <a name="register-grpc-clients"></a>gRPC istemcilerini kaydedin

Bir gRPC istemcisi `AddGrpcClient` kaydetmek için, genel `Startup.ConfigureServices`uzatma yöntemi içinde kullanılabilir , gRPC dakti-si istemci sınıfı ve hizmet adresi belirterek:

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

gRPC istemci türü bağımlılık enjeksiyonu (DI) ile geçici olarak kaydedilir. İstemci artık doğrudan DI tarafından oluşturulan türlerde enjekte edilebilir ve tüketilebilir. ASP.NET Core MVC SignalR denetleyicileri, hub'lar ve gRPC hizmetleri gRPC istemcilerinin otomatik olarak enjekte edilebildiği yerlerdir:

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a>Yapılandırma httpClient

`HttpClientFactory`gRPC `HttpClient` istemcisi tarafından kullanılan oluşturur. Standart `HttpClientFactory` yöntemler giden istek ara eklemek veya altta yatan `HttpClientHandler` yapılandırmak `HttpClient`için kullanılabilir:

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

Daha fazla bilgi için Bkz. [IHttpClientFactory'yi kullanarak HTTP isteklerini gerçekleştirin.](xref:fundamentals/http-requests)

## <a name="configure-channel-and-interceptors"></a>Kanal ve Durdurucuları Yapılandırma

gRPC'ye özgü yöntemler şunlardır:

* gRPC istemcisinin temel kanalLarını yapılandırın.
* istemcinin gRPC aramaları yaparken kullanacağı örnekleri ekleyin. `Interceptor`

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a>Son tarih ve iptal yayılımı

bir gRPC hizmetinde fabrika tarafından oluşturulan gRPC `EnableCallContextPropagation()` istemcileri, son tarihi ve iptal jetonunu çocuk çağrılarına otomatik olarak yaymak üzere yapılandırılabilir. Uzatma `EnableCallContextPropagation()` yöntemi [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet paketinde mevcuttur.

Mevcut gRPC istek bağlamından son tarih ve iptal belirteci okuyarak ve bunları otomatik olarak gRPC istemcisi tarafından yapılan giden aramalara çoğaltarak çağrı bağlamı yayılımı çalışır. Çağrı bağlamı yayılımı, karmaşık, iç içe açılan gRPC senaryolarının her zaman son tarihi ve iptali yaymasını sağlamanın mükemmel bir yoludur.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

Son tarihler ve RPC iptali hakkında daha fazla bilgi için [RPC yaşam döngüsüne](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle)bakın.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
