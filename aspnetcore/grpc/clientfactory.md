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
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="20e62-103">gRPC istemci fabrika entegrasyonu .NET Core</span><span class="sxs-lookup"><span data-stu-id="20e62-103">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="20e62-104">gRPC entegrasyonu `HttpClientFactory` ile gRPC istemcileri oluşturmak için merkezi bir yol sunar.</span><span class="sxs-lookup"><span data-stu-id="20e62-104">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="20e62-105">Tek [başına gRPC istemci örneklerini yapılandırmaya](xref:grpc/client)alternatif olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="20e62-105">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="20e62-106">Fabrika entegrasyonu [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet paketinde mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="20e62-106">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="20e62-107">Fabrika aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="20e62-107">The factory offers the following benefits:</span></span>

* <span data-ttu-id="20e62-108">Mantıksal gRPC istemci örneklerini yapılandırmak için merkezi bir konum sağlar</span><span class="sxs-lookup"><span data-stu-id="20e62-108">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="20e62-109">Altta yatan ömrü yönetir`HttpClientMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="20e62-109">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="20e62-110">Core gRPC hizmeti ASP.NETnde son tarihin otomatik olarak yayılması ve iptal edilmesi</span><span class="sxs-lookup"><span data-stu-id="20e62-110">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="20e62-111">gRPC istemcilerini kaydedin</span><span class="sxs-lookup"><span data-stu-id="20e62-111">Register gRPC clients</span></span>

<span data-ttu-id="20e62-112">Bir gRPC istemcisi `AddGrpcClient` kaydetmek için, genel `Startup.ConfigureServices`uzatma yöntemi içinde kullanılabilir , gRPC dakti-si istemci sınıfı ve hizmet adresi belirterek:</span><span class="sxs-lookup"><span data-stu-id="20e62-112">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="20e62-113">gRPC istemci türü bağımlılık enjeksiyonu (DI) ile geçici olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="20e62-113">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="20e62-114">İstemci artık doğrudan DI tarafından oluşturulan türlerde enjekte edilebilir ve tüketilebilir.</span><span class="sxs-lookup"><span data-stu-id="20e62-114">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="20e62-115">ASP.NET Core MVC SignalR denetleyicileri, hub'lar ve gRPC hizmetleri gRPC istemcilerinin otomatik olarak enjekte edilebildiği yerlerdir:</span><span class="sxs-lookup"><span data-stu-id="20e62-115">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

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

## <a name="configure-httpclient"></a><span data-ttu-id="20e62-116">Yapılandırma httpClient</span><span class="sxs-lookup"><span data-stu-id="20e62-116">Configure HttpClient</span></span>

<span data-ttu-id="20e62-117">`HttpClientFactory`gRPC `HttpClient` istemcisi tarafından kullanılan oluşturur.</span><span class="sxs-lookup"><span data-stu-id="20e62-117">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="20e62-118">Standart `HttpClientFactory` yöntemler giden istek ara eklemek veya altta yatan `HttpClientHandler` yapılandırmak `HttpClient`için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="20e62-118">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

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

<span data-ttu-id="20e62-119">Daha fazla bilgi için Bkz. [IHttpClientFactory'yi kullanarak HTTP isteklerini gerçekleştirin.](xref:fundamentals/http-requests)</span><span class="sxs-lookup"><span data-stu-id="20e62-119">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="20e62-120">Kanal ve Durdurucuları Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="20e62-120">Configure Channel and Interceptors</span></span>

<span data-ttu-id="20e62-121">gRPC'ye özgü yöntemler şunlardır:</span><span class="sxs-lookup"><span data-stu-id="20e62-121">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="20e62-122">gRPC istemcisinin temel kanalLarını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="20e62-122">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="20e62-123">istemcinin gRPC aramaları yaparken kullanacağı örnekleri ekleyin. `Interceptor`</span><span class="sxs-lookup"><span data-stu-id="20e62-123">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

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

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="20e62-124">Son tarih ve iptal yayılımı</span><span class="sxs-lookup"><span data-stu-id="20e62-124">Deadline and cancellation propagation</span></span>

<span data-ttu-id="20e62-125">bir gRPC hizmetinde fabrika tarafından oluşturulan gRPC `EnableCallContextPropagation()` istemcileri, son tarihi ve iptal jetonunu çocuk çağrılarına otomatik olarak yaymak üzere yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="20e62-125">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="20e62-126">Uzatma `EnableCallContextPropagation()` yöntemi [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet paketinde mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="20e62-126">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="20e62-127">Mevcut gRPC istek bağlamından son tarih ve iptal belirteci okuyarak ve bunları otomatik olarak gRPC istemcisi tarafından yapılan giden aramalara çoğaltarak çağrı bağlamı yayılımı çalışır.</span><span class="sxs-lookup"><span data-stu-id="20e62-127">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="20e62-128">Çağrı bağlamı yayılımı, karmaşık, iç içe açılan gRPC senaryolarının her zaman son tarihi ve iptali yaymasını sağlamanın mükemmel bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="20e62-128">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="20e62-129">Son tarihler ve RPC iptali hakkında daha fazla bilgi için [RPC yaşam döngüsüne](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle)bakın.</span><span class="sxs-lookup"><span data-stu-id="20e62-129">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="20e62-130">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="20e62-130">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
