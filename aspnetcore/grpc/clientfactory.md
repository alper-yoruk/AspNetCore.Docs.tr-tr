---
title: .NET Core 'da gRPC istemci fabrikası tümleştirmesi
author: jamesnk
description: İstemci fabrikasını kullanarak gRPC istemcilerini oluşturmayı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: grpc/clientfactory
ms.openlocfilehash: c63bf495f558237ed801881d378953119791b8ce
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060956"
---
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="7bd8f-103">.NET Core 'da gRPC istemci fabrikası tümleştirmesi</span><span class="sxs-lookup"><span data-stu-id="7bd8f-103">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="7bd8f-104">ile gRPC tümleştirmesi, `HttpClientFactory` GRPC istemcileri oluşturmaya yönelik merkezi bir yöntem sunar.</span><span class="sxs-lookup"><span data-stu-id="7bd8f-104">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="7bd8f-105">[Tek başına gRPC istemci örneklerini yapılandırmaya](xref:grpc/client)alternatif olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7bd8f-105">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="7bd8f-106">Fabrika tümleştirmesi, [GRPC .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet paketinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="7bd8f-106">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="7bd8f-107">Fabrika aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="7bd8f-107">The factory offers the following benefits:</span></span>

* <span data-ttu-id="7bd8f-108">Mantıksal gRPC istemci örneklerini yapılandırmak için merkezi bir konum sağlar</span><span class="sxs-lookup"><span data-stu-id="7bd8f-108">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="7bd8f-109">Temeldeki bir yaşam süresini yönetir `HttpClientMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="7bd8f-109">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="7bd8f-110">ASP.NET Core gRPC hizmetinde son tarih ve iptali otomatik olarak yayma</span><span class="sxs-lookup"><span data-stu-id="7bd8f-110">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="7bd8f-111">GRPC istemcilerini kaydetme</span><span class="sxs-lookup"><span data-stu-id="7bd8f-111">Register gRPC clients</span></span>

<span data-ttu-id="7bd8f-112">Bir gRPC istemcisini kaydetmek için, genel `AddGrpcClient` genişletme yöntemi içinde `Startup.ConfigureServices` , GRPC türü belirtilmiş istemci sınıfı ve hizmet adresi belirtilerek kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="7bd8f-112">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="7bd8f-113">GRPC istemci türü, bağımlılık ekleme (dı) ile geçici olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="7bd8f-113">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="7bd8f-114">İstemci artık, DI tarafından oluşturulan türlere eklenebilir ve doğrudan tüketilebilir.</span><span class="sxs-lookup"><span data-stu-id="7bd8f-114">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="7bd8f-115">ASP.NET Core MVC denetleyicileri, :::no-loc(SignalR)::: hub 'lar ve GRPC Hizmetleri, GRPC istemcilerinin otomatik olarak eklenebilir yer lardır:</span><span class="sxs-lookup"><span data-stu-id="7bd8f-115">ASP.NET Core MVC controllers, :::no-loc(SignalR)::: hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

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

## <a name="configure-httpclient"></a><span data-ttu-id="7bd8f-116">HttpClient 'ı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="7bd8f-116">Configure HttpClient</span></span>

<span data-ttu-id="7bd8f-117">`HttpClientFactory``HttpClient`gRPC istemcisi tarafından kullanılan öğesini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7bd8f-117">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="7bd8f-118">Standart `HttpClientFactory` Yöntemler, giden istek ara yazılımı eklemek veya öğesinin temelini yapılandırmak için kullanılabilir `HttpClientHandler` `HttpClient` :</span><span class="sxs-lookup"><span data-stu-id="7bd8f-118">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

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

<span data-ttu-id="7bd8f-119">Daha fazla bilgi için bkz. [ıhttpclientfactory kullanarak http Istekleri oluşturma](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="7bd8f-119">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="7bd8f-120">Kanalı ve Yakacıları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="7bd8f-120">Configure Channel and Interceptors</span></span>

<span data-ttu-id="7bd8f-121">gRPC 'ye özgü Yöntemler şu şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="7bd8f-121">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="7bd8f-122">Bir gRPC istemcisinin temel kanalını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="7bd8f-122">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="7bd8f-123">`Interceptor`Istemcisinin gRPC çağrıları yaparken kullanacağı örnekleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7bd8f-123">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

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

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="7bd8f-124">Son Tarih ve iptal yayma</span><span class="sxs-lookup"><span data-stu-id="7bd8f-124">Deadline and cancellation propagation</span></span>

<span data-ttu-id="7bd8f-125">bir gRPC hizmetinde fabrika tarafından oluşturulan gRPC istemcileri `EnableCallContextPropagation()` , son tarih ve iptal belirtecini alt çağrılara otomatik olarak yaymak üzere ile yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="7bd8f-125">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="7bd8f-126">`EnableCallContextPropagation()`Genişletme yöntemi [GRPC. aspnetcore. Server. clientfactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet paketinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="7bd8f-126">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="7bd8f-127">Çağrı bağlamı yayma, geçerli gRPC isteği bağlamından son tarih ve iptal belirtecini okuyarak ve bunları otomatik olarak gRPC istemcisi tarafından yapılan giden çağrılara yayarak işe yarar.</span><span class="sxs-lookup"><span data-stu-id="7bd8f-127">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="7bd8f-128">Çağrı bağlamı yayma, karmaşık, iç içe gRPC senaryolarının her zaman son tarihi ve iptali yaymasını sağlamaya yönelik mükemmel bir yoldur.</span><span class="sxs-lookup"><span data-stu-id="7bd8f-128">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="7bd8f-129">Varsayılan olarak, `EnableCallContextPropagation` istemci bir gRPC çağrısı bağlamı dışında kullanılıyorsa bir hata oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7bd8f-129">By default, `EnableCallContextPropagation` raises an error if the client is used outside the context of a gRPC call.</span></span> <span data-ttu-id="7bd8f-130">Hata, yaymaya yönelik bir çağrı bağlamı olmadığını belirten bir uyarı verecek şekilde tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="7bd8f-130">The error is designed to alert you that there isn't a call context to propagate.</span></span> <span data-ttu-id="7bd8f-131">İstemciyi bir çağrı bağlamı dışında kullanmak istiyorsanız, istemci ile yapılandırıldığında hatayı gizleyin `SuppressContextNotFoundErrors` :</span><span class="sxs-lookup"><span data-stu-id="7bd8f-131">If you want to use the client outside of a call context, suppress the error when the client is configured with `SuppressContextNotFoundErrors`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

<span data-ttu-id="7bd8f-132">Son tarihler ve RPC iptali hakkında daha fazla bilgi için bkz. [RPC yaşam döngüsü](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span><span class="sxs-lookup"><span data-stu-id="7bd8f-132">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7bd8f-133">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7bd8f-133">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
