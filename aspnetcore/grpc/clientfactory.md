---
title: .NET Core 'da gRPC istemci fabrikası tümleştirmesi
author: jamesnk
description: İstemci fabrikasını kullanarak gRPC istemcilerini oluşturmayı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
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
uid: grpc/clientfactory
ms.openlocfilehash: c63bf495f558237ed801881d378953119791b8ce
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "93060956"
---
# <a name="grpc-client-factory-integration-in-net-core"></a>.NET Core 'da gRPC istemci fabrikası tümleştirmesi

ile gRPC tümleştirmesi, `HttpClientFactory` GRPC istemcileri oluşturmaya yönelik merkezi bir yöntem sunar. [Tek başına gRPC istemci örneklerini yapılandırmaya](xref:grpc/client)alternatif olarak kullanılabilir. Fabrika tümleştirmesi, [GRPC .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet paketinde bulunabilir.

Fabrika aşağıdaki avantajları sunar:

* Mantıksal gRPC istemci örneklerini yapılandırmak için merkezi bir konum sağlar
* Temeldeki bir yaşam süresini yönetir `HttpClientMessageHandler`
* ASP.NET Core gRPC hizmetinde son tarih ve iptali otomatik olarak yayma

## <a name="register-grpc-clients"></a>GRPC istemcilerini kaydetme

Bir gRPC istemcisini kaydetmek için, genel `AddGrpcClient` genişletme yöntemi içinde `Startup.ConfigureServices` , GRPC türü belirtilmiş istemci sınıfı ve hizmet adresi belirtilerek kullanılabilir:

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

GRPC istemci türü, bağımlılık ekleme (dı) ile geçici olarak kaydedilir. İstemci artık, DI tarafından oluşturulan türlere eklenebilir ve doğrudan tüketilebilir. ASP.NET Core MVC denetleyicileri, SignalR hub 'lar ve GRPC Hizmetleri, GRPC istemcilerinin otomatik olarak eklenebilir yer lardır:

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

## <a name="configure-httpclient"></a>HttpClient 'ı yapılandırma

`HttpClientFactory``HttpClient`gRPC istemcisi tarafından kullanılan öğesini oluşturur. Standart `HttpClientFactory` Yöntemler, giden istek ara yazılımı eklemek veya öğesinin temelini yapılandırmak için kullanılabilir `HttpClientHandler` `HttpClient` :

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

Daha fazla bilgi için bkz. [ıhttpclientfactory kullanarak http Istekleri oluşturma](xref:fundamentals/http-requests).

## <a name="configure-channel-and-interceptors"></a>Kanalı ve Yakacıları yapılandırma

gRPC 'ye özgü Yöntemler şu şekilde kullanılabilir:

* Bir gRPC istemcisinin temel kanalını yapılandırın.
* `Interceptor`Istemcisinin gRPC çağrıları yaparken kullanacağı örnekleri ekleyin.

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

## <a name="deadline-and-cancellation-propagation"></a>Son Tarih ve iptal yayma

bir gRPC hizmetinde fabrika tarafından oluşturulan gRPC istemcileri `EnableCallContextPropagation()` , son tarih ve iptal belirtecini alt çağrılara otomatik olarak yaymak üzere ile yapılandırılabilir. `EnableCallContextPropagation()`Genişletme yöntemi [GRPC. aspnetcore. Server. clientfactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet paketinde bulunur.

Çağrı bağlamı yayma, geçerli gRPC isteği bağlamından son tarih ve iptal belirtecini okuyarak ve bunları otomatik olarak gRPC istemcisi tarafından yapılan giden çağrılara yayarak işe yarar. Çağrı bağlamı yayma, karmaşık, iç içe gRPC senaryolarının her zaman son tarihi ve iptali yaymasını sağlamaya yönelik mükemmel bir yoldur.

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

Varsayılan olarak, `EnableCallContextPropagation` istemci bir gRPC çağrısı bağlamı dışında kullanılıyorsa bir hata oluşturur. Hata, yaymaya yönelik bir çağrı bağlamı olmadığını belirten bir uyarı verecek şekilde tasarlanmıştır. İstemciyi bir çağrı bağlamı dışında kullanmak istiyorsanız, istemci ile yapılandırıldığında hatayı gizleyin `SuppressContextNotFoundErrors` :

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

Son tarihler ve RPC iptali hakkında daha fazla bilgi için bkz. [RPC yaşam döngüsü](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
