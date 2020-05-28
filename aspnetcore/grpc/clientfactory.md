---
<span data-ttu-id="7795f-101">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="7795f-101">title: author: description: monikerRange: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="7795f-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7795f-102">'Blazor'</span></span>
- <span data-ttu-id="7795f-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7795f-103">'Identity'</span></span>
- <span data-ttu-id="7795f-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7795f-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="7795f-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7795f-105">'Razor'</span></span>
- <span data-ttu-id="7795f-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="7795f-106">'SignalR' uid:</span></span> 

---
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="7795f-107">.NET Core 'da gRPC istemci fabrikası tümleştirmesi</span><span class="sxs-lookup"><span data-stu-id="7795f-107">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="7795f-108">ile gRPC tümleştirmesi, `HttpClientFactory` GRPC istemcileri oluşturmaya yönelik merkezi bir yöntem sunar.</span><span class="sxs-lookup"><span data-stu-id="7795f-108">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="7795f-109">[Tek başına gRPC istemci örneklerini yapılandırmaya](xref:grpc/client)alternatif olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7795f-109">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="7795f-110">Fabrika tümleştirmesi, [GRPC .net. ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet paketinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="7795f-110">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="7795f-111">Fabrika aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="7795f-111">The factory offers the following benefits:</span></span>

* <span data-ttu-id="7795f-112">Mantıksal gRPC istemci örneklerini yapılandırmak için merkezi bir konum sağlar</span><span class="sxs-lookup"><span data-stu-id="7795f-112">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="7795f-113">Temeldeki bir yaşam süresini yönetir`HttpClientMessageHandler`</span><span class="sxs-lookup"><span data-stu-id="7795f-113">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="7795f-114">ASP.NET Core gRPC hizmetinde son tarih ve iptali otomatik olarak yayma</span><span class="sxs-lookup"><span data-stu-id="7795f-114">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="7795f-115">GRPC istemcilerini kaydetme</span><span class="sxs-lookup"><span data-stu-id="7795f-115">Register gRPC clients</span></span>

<span data-ttu-id="7795f-116">Bir gRPC istemcisini kaydetmek için, genel `AddGrpcClient` genişletme yöntemi içinde `Startup.ConfigureServices` , GRPC türü belirtilmiş istemci sınıfı ve hizmet adresi belirtilerek kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="7795f-116">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="7795f-117">GRPC istemci türü, bağımlılık ekleme (dı) ile geçici olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="7795f-117">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="7795f-118">İstemci artık, DI tarafından oluşturulan türlere eklenebilir ve doğrudan tüketilebilir.</span><span class="sxs-lookup"><span data-stu-id="7795f-118">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="7795f-119">ASP.NET Core MVC denetleyicileri, SignalR hub 'lar ve GRPC Hizmetleri, GRPC istemcilerinin otomatik olarak eklenebilir yer lardır:</span><span class="sxs-lookup"><span data-stu-id="7795f-119">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

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

## <a name="configure-httpclient"></a><span data-ttu-id="7795f-120">HttpClient 'ı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="7795f-120">Configure HttpClient</span></span>

<span data-ttu-id="7795f-121">`HttpClientFactory``HttpClient`gRPC istemcisi tarafından kullanılan öğesini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7795f-121">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="7795f-122">Standart `HttpClientFactory` Yöntemler, giden istek ara yazılımı eklemek veya öğesinin temelini yapılandırmak için kullanılabilir `HttpClientHandler` `HttpClient` :</span><span class="sxs-lookup"><span data-stu-id="7795f-122">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

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

<span data-ttu-id="7795f-123">Daha fazla bilgi için bkz. [ıhttpclientfactory kullanarak http Istekleri oluşturma](xref:fundamentals/http-requests).</span><span class="sxs-lookup"><span data-stu-id="7795f-123">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="7795f-124">Kanalı ve Yakacıları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="7795f-124">Configure Channel and Interceptors</span></span>

<span data-ttu-id="7795f-125">gRPC 'ye özgü Yöntemler şu şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="7795f-125">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="7795f-126">Bir gRPC istemcisinin temel kanalını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="7795f-126">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="7795f-127">`Interceptor`Istemcisinin gRPC çağrıları yaparken kullanacağı örnekleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="7795f-127">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

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

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="7795f-128">Son Tarih ve iptal yayma</span><span class="sxs-lookup"><span data-stu-id="7795f-128">Deadline and cancellation propagation</span></span>

<span data-ttu-id="7795f-129">bir gRPC hizmetinde fabrika tarafından oluşturulan gRPC istemcileri `EnableCallContextPropagation()` , son tarih ve iptal belirtecini alt çağrılara otomatik olarak yaymak üzere ile yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="7795f-129">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="7795f-130">`EnableCallContextPropagation()`Genişletme yöntemi [GRPC. aspnetcore. Server. clientfactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet paketinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="7795f-130">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="7795f-131">Çağrı bağlamı yayma, geçerli gRPC isteği bağlamından son tarih ve iptal belirtecini okuyarak ve bunları otomatik olarak gRPC istemcisi tarafından yapılan giden çağrılara yayarak işe yarar.</span><span class="sxs-lookup"><span data-stu-id="7795f-131">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="7795f-132">Çağrı bağlamı yayma, karmaşık, iç içe gRPC senaryolarının her zaman son tarihi ve iptali yaymasını sağlamaya yönelik mükemmel bir yoldur.</span><span class="sxs-lookup"><span data-stu-id="7795f-132">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="7795f-133">Varsayılan olarak, `EnableCallContextPropagation` istemci bir gRPC çağrısı bağlamı dışında kullanılıyorsa bir hata oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7795f-133">By default, `EnableCallContextPropagation` raises an error if the client is used outside the context of a gRPC call.</span></span> <span data-ttu-id="7795f-134">Hata, yaymaya yönelik bir çağrı bağlamı olmadığını belirten bir uyarı verecek şekilde tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="7795f-134">The error is designed to alert you that there isn't a call context to propagate.</span></span> <span data-ttu-id="7795f-135">İstemciyi bir çağrı bağlamı dışında kullanmak istiyorsanız, istemci ile yapılandırıldığında hatayı gizleyin `SuppressContextNotFoundErrors` :</span><span class="sxs-lookup"><span data-stu-id="7795f-135">If you want to use the client outside of a call context, suppress the error when the client is configured with `SuppressContextNotFoundErrors`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

<span data-ttu-id="7795f-136">Son tarihler ve RPC iptali hakkında daha fazla bilgi için bkz. [RPC yaşam döngüsü](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span><span class="sxs-lookup"><span data-stu-id="7795f-136">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7795f-137">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7795f-137">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
