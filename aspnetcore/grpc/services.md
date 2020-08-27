---
title: GRPC Hizmetleri ve yöntemleri oluşturma
author: jamesnk
description: GRPC Hizmetleri ve yöntemleri oluşturmayı öğrenin.
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/25/2020
no-loc:
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
uid: grpc/services
ms.openlocfilehash: fde589b2de9d908db26a2557c5f57c715625aadc
ms.sourcegitcommit: 47c9a59ff8a359baa6bca2637d3af87ddca1245b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88945759"
---
# <a name="create-grpc-services-and-methods"></a><span data-ttu-id="7ac84-103">GRPC Hizmetleri ve yöntemleri oluşturma</span><span class="sxs-lookup"><span data-stu-id="7ac84-103">Create gRPC services and methods</span></span>

<span data-ttu-id="7ac84-104">Bu belgede, C# ' de gRPC Hizmetleri ve yöntemlerinin nasıl oluşturulacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="7ac84-104">This document explains how to create gRPC services and methods in C#.</span></span> <span data-ttu-id="7ac84-105">Konu başlıkları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="7ac84-105">Topics include:</span></span>

* <span data-ttu-id="7ac84-106">*. Proto* dosyalarında Hizmetleri ve yöntemleri tanımlama.</span><span class="sxs-lookup"><span data-stu-id="7ac84-106">How to define services and methods in *.proto* files.</span></span>
* <span data-ttu-id="7ac84-107">GRPC C# araçları kullanılarak oluşturulan kod.</span><span class="sxs-lookup"><span data-stu-id="7ac84-107">Generated code using gRPC C# tooling.</span></span>
* <span data-ttu-id="7ac84-108">GRPC Hizmetleri ve yöntemleri uygulama.</span><span class="sxs-lookup"><span data-stu-id="7ac84-108">Implementing gRPC services and methods.</span></span>

## <a name="create-new-grpc-services"></a><span data-ttu-id="7ac84-109">Yeni gRPC hizmetleri oluşturma</span><span class="sxs-lookup"><span data-stu-id="7ac84-109">Create new gRPC services</span></span>

<span data-ttu-id="7ac84-110">[C# ile GRPC Hizmetleri,](xref:grpc/basics) GRPC 'nin sözleşme-API geliştirmeye ilk yaklaşımı getirmiştir.</span><span class="sxs-lookup"><span data-stu-id="7ac84-110">[gRPC services with C#](xref:grpc/basics) introduced gRPC's contract-first approach to API development.</span></span> <span data-ttu-id="7ac84-111">Hizmetler ve mesajlar *. proto* dosyalarında tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="7ac84-111">Services and messages are defined in *.proto* files.</span></span> <span data-ttu-id="7ac84-112">C# araçları daha sonra *. proto* dosyalarından kod oluşturur.</span><span class="sxs-lookup"><span data-stu-id="7ac84-112">C# tooling then generates code from *.proto* files.</span></span> <span data-ttu-id="7ac84-113">Sunucu tarafı varlıklar için, her bir hizmet için bir soyut temel tür ve tüm iletiler için sınıflar oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7ac84-113">For server-side assets, an abstract base type is generated for each service, along with classes for any messages.</span></span>

<span data-ttu-id="7ac84-114">Aşağıdaki *. proto* dosyası:</span><span class="sxs-lookup"><span data-stu-id="7ac84-114">The following *.proto* file:</span></span>

* <span data-ttu-id="7ac84-115">Bir `Greeter` hizmeti tanımlar.</span><span class="sxs-lookup"><span data-stu-id="7ac84-115">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="7ac84-116">`Greeter`Hizmet bir çağrıyı tanımlar `SayHello` .</span><span class="sxs-lookup"><span data-stu-id="7ac84-116">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="7ac84-117">`SayHello` bir `HelloRequest` ileti gönderir ve bir `HelloReply` ileti alır</span><span class="sxs-lookup"><span data-stu-id="7ac84-117">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message</span></span>

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

<span data-ttu-id="7ac84-118">C# araçları, C# `GreeterBase` temel türünü oluşturur:</span><span class="sxs-lookup"><span data-stu-id="7ac84-118">C# tooling generates the C# `GreeterBase` base type:</span></span>

```csharp
public abstract partial class GreeterBase
{
    public virtual Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        throw new RpcException(new Status(StatusCode.Unimplemented, ""));
    }
}

public class HelloRequest
{
    public string Name { get; set; }
}

public class HelloReply
{
    public string Message { get; set; }
}
```

<span data-ttu-id="7ac84-119">Varsayılan olarak, oluşturulan `GreeterBase` hiçbir şey yapmaz.</span><span class="sxs-lookup"><span data-stu-id="7ac84-119">By default the generated `GreeterBase` doesn't do anything.</span></span> <span data-ttu-id="7ac84-120">Sanal `SayHello` yöntemi, `UNIMPLEMENTED` çağıran istemcilere bir hata döndürür.</span><span class="sxs-lookup"><span data-stu-id="7ac84-120">Its virtual `SayHello` method will return an `UNIMPLEMENTED` error to any clients that call it.</span></span> <span data-ttu-id="7ac84-121">Hizmetin yararlı olması için bir uygulama için somut bir uygulama oluşturmanız gerekir `GreeterBase` :</span><span class="sxs-lookup"><span data-stu-id="7ac84-121">For the service to be useful an app must create a concrete implementation of `GreeterBase`:</span></span>

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> UnaryCall(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

<span data-ttu-id="7ac84-122">Hizmet uygulaması, uygulamaya kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="7ac84-122">The service implementation is registered with the app.</span></span> <span data-ttu-id="7ac84-123">Hizmet ASP.NET Core gRPC tarafından barındırılıyorsa, yöntemi ile yönlendirme ardışık düzenine eklenmelidir `MapGrpcService` .</span><span class="sxs-lookup"><span data-stu-id="7ac84-123">If the service is hosted by ASP.NET Core gRPC, it should be added to the routing pipeline with the `MapGrpcService` method.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="7ac84-124">Daha fazla bilgi edinmek için bkz. <xref:grpc/aspnetcore>.</span><span class="sxs-lookup"><span data-stu-id="7ac84-124">See <xref:grpc/aspnetcore> for more information.</span></span>

## <a name="implement-grpc-methods"></a><span data-ttu-id="7ac84-125">GRPC yöntemlerini uygulama</span><span class="sxs-lookup"><span data-stu-id="7ac84-125">Implement gRPC methods</span></span>

<span data-ttu-id="7ac84-126">GRPC hizmeti farklı türde yöntemlere sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="7ac84-126">A gRPC service can have different types of methods.</span></span> <span data-ttu-id="7ac84-127">İletilerin bir hizmet tarafından gönderilme ve alınma şekli, tanımlanan yöntemin türüne bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="7ac84-127">How messages are sent and received by a service depends on the type of method defined.</span></span> <span data-ttu-id="7ac84-128">GRPC Yöntem türleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="7ac84-128">The gRPC method types are:</span></span>

* <span data-ttu-id="7ac84-129">Birli</span><span class="sxs-lookup"><span data-stu-id="7ac84-129">Unary</span></span>
* <span data-ttu-id="7ac84-130">Sunucu akışı</span><span class="sxs-lookup"><span data-stu-id="7ac84-130">Server streaming</span></span>
* <span data-ttu-id="7ac84-131">İstemci akışı</span><span class="sxs-lookup"><span data-stu-id="7ac84-131">Client streaming</span></span>
* <span data-ttu-id="7ac84-132">İki yönlü akış</span><span class="sxs-lookup"><span data-stu-id="7ac84-132">Bi-directional streaming</span></span>

<span data-ttu-id="7ac84-133">Akış çağrıları `stream` *. proto* dosyasında anahtar sözcüğüyle belirtilir.</span><span class="sxs-lookup"><span data-stu-id="7ac84-133">Streaming calls are specified with the `stream` keyword in the *.proto* file.</span></span> <span data-ttu-id="7ac84-134">`stream` bir çağrının istek iletisine, yanıt iletisine veya her ikisine birden yerleştirilebilecek.</span><span class="sxs-lookup"><span data-stu-id="7ac84-134">`stream` can be placed on a call's request message, response message, or both.</span></span>

```protobuf
syntax = "proto3";

service ExampleService {
  // Unary
  rpc UnaryCall (ExampleRequest) returns (ExampleResponse);

  // Server streaming
  rpc StreamingFromServer (ExampleRequest) returns (stream ExampleResponse);

  // Client streaming
  rpc StreamingFromClient (stream ExampleRequest) returns (ExampleResponse);

  // Bi-directional streaming
  rpc StreamingBothWays (stream ExampleRequest) returns (stream ExampleResponse);
}
```

<span data-ttu-id="7ac84-135">Her çağrı türünün farklı bir yöntem imzası vardır.</span><span class="sxs-lookup"><span data-stu-id="7ac84-135">Each call type has a different method signature.</span></span> <span data-ttu-id="7ac84-136">Somut bir uygulamadaki soyut temel hizmet türünden oluşturulan yöntemlerin üzerine yazmak, doğru bağımsız değişkenlerin ve dönüş türünün kullanılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="7ac84-136">Overriding generated methods from the abstract base service type in a concrete implementation ensures the correct arguments and return type are used.</span></span>

### <a name="unary-method"></a><span data-ttu-id="7ac84-137">Birli yöntemi</span><span class="sxs-lookup"><span data-stu-id="7ac84-137">Unary method</span></span>

<span data-ttu-id="7ac84-138">Birli Yöntem, istek iletisini parametre olarak alır ve yanıtı döndürür.</span><span class="sxs-lookup"><span data-stu-id="7ac84-138">A unary method gets the request message as a parameter, and returns the response.</span></span> <span data-ttu-id="7ac84-139">Yanıt döndürüldüğünde birli çağrı tamamlanır.</span><span class="sxs-lookup"><span data-stu-id="7ac84-139">A unary call is complete when the response is returned.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

<span data-ttu-id="7ac84-140">Birli çağrılar, [Web API denetleyicilerindeki eylemlere](xref:web-api/index)en çok benzer.</span><span class="sxs-lookup"><span data-stu-id="7ac84-140">Unary calls are the most similar to [actions on web API controllers](xref:web-api/index).</span></span> <span data-ttu-id="7ac84-141">Bir önemli fark gRPC yöntemi eylemlerden oluşur gRPC metotları bir isteğin parçalarını farklı yöntem bağımsız değişkenlerine bağlanamaz.</span><span class="sxs-lookup"><span data-stu-id="7ac84-141">One important difference gRPC methods have from actions is gRPC methods are not able to bind parts of a request to different method arguments.</span></span> <span data-ttu-id="7ac84-142">gRPC yöntemlerinin her zaman gelen istek verileri için bir ileti bağımsız değişkeni vardır.</span><span class="sxs-lookup"><span data-stu-id="7ac84-142">gRPC methods always have one message argument for the incoming request data.</span></span> <span data-ttu-id="7ac84-143">Bir gRPC hizmetine, istek iletisinde alanlar yapılarak birden çok değer gönderilebilir:</span><span class="sxs-lookup"><span data-stu-id="7ac84-143">Multiple values can still be sent to a gRPC service by making them fields on the request message:</span></span>

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a><span data-ttu-id="7ac84-144">Sunucu akış yöntemi</span><span class="sxs-lookup"><span data-stu-id="7ac84-144">Server streaming method</span></span>

<span data-ttu-id="7ac84-145">Sunucu akış yöntemi istek iletisini bir parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="7ac84-145">A server streaming method gets the request message as a parameter.</span></span> <span data-ttu-id="7ac84-146">Birden çok ileti çağırana geri akışı olabileceğinden, `responseStream.WriteAsync` yanıt iletilerini göndermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7ac84-146">Because multiple messages can be streamed back to the caller, `responseStream.WriteAsync` is used to send response messages.</span></span> <span data-ttu-id="7ac84-147">Yöntem döndürüldüğünde bir sunucu akış çağrısı tamamlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="7ac84-147">A server streaming call is complete when the method returns.</span></span>

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    for (var i = 0; i < 5; i++)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1));
    }
}
```

<span data-ttu-id="7ac84-148">Sunucu akış yöntemi başlatıldıktan sonra istemcinin ek ileti veya veri gönderme yolu yoktur.</span><span class="sxs-lookup"><span data-stu-id="7ac84-148">The client has no way to send additional messages or data once the server streaming method has started.</span></span> <span data-ttu-id="7ac84-149">Bazı akış yöntemleri, süresiz olarak çalışacak şekilde tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="7ac84-149">Some streaming methods are designed to run forever.</span></span> <span data-ttu-id="7ac84-150">Sürekli akış yöntemleri için, istemci artık gerekli olmadığında çağrıyı iptal edebilir.</span><span class="sxs-lookup"><span data-stu-id="7ac84-150">For continuous streaming methods, a client can cancel the call when it's no longer needed.</span></span> <span data-ttu-id="7ac84-151">İptal gerçekleştiğinde, istemci sunucuya bir sinyal gönderir ve [Servercallcontext. CancellationToken](xref:System.Threading.CancellationToken) oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="7ac84-151">When cancellation happens the client sends a signal to the server and the [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="7ac84-152">`CancellationToken`Belirtecin zaman uyumsuz yöntemlerle sunucuda kullanılması gerekir, bu nedenle:</span><span class="sxs-lookup"><span data-stu-id="7ac84-152">The `CancellationToken` token should be used on the server with async methods so that:</span></span>

* <span data-ttu-id="7ac84-153">Tüm zaman uyumsuz işler, akış çağrısıyla birlikte iptal edilir.</span><span class="sxs-lookup"><span data-stu-id="7ac84-153">Any asynchronous work is canceled together with the streaming call.</span></span>
* <span data-ttu-id="7ac84-154">Yöntemi hızla çıkar.</span><span class="sxs-lookup"><span data-stu-id="7ac84-154">The method exits quickly.</span></span>

```csharp
public override async Task StreamingFromServer(ExampleRequest request,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    while (!context.CancellationToken.IsCancellationRequested)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

### <a name="client-streaming-method"></a><span data-ttu-id="7ac84-155">İstemci akış yöntemi</span><span class="sxs-lookup"><span data-stu-id="7ac84-155">Client streaming method</span></span>

<span data-ttu-id="7ac84-156">Bir istemci akış yöntemi bir ileti *almadan başlar.*</span><span class="sxs-lookup"><span data-stu-id="7ac84-156">A client streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="7ac84-157">`requestStream`Parametresi istemcisinden iletileri okumak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7ac84-157">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="7ac84-158">Yanıt iletisi döndürüldüğünde bir istemci akış çağrısı tamamlanır:</span><span class="sxs-lookup"><span data-stu-id="7ac84-158">A client streaming call is complete when a response message is returned:</span></span>

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    while (await requestStream.MoveNext())
    {
        var message = requestStream.Current;
        // ...
    }
    return new ExampleResponse();
}
```

<span data-ttu-id="7ac84-159">C# 8 veya sonraki bir sürümünü kullanırken, `await foreach` söz dizimi iletileri okumak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="7ac84-159">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="7ac84-160">`IAsyncStreamReader<T>.ReadAllAsync()`Uzantı yöntemi istek akışındaki tüm iletileri okur:</span><span class="sxs-lookup"><span data-stu-id="7ac84-160">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the request stream:</span></span>

```csharp
public override async Task<ExampleResponse> StreamingFromClient(
    IAsyncStreamReader<ExampleRequest> requestStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        // ...
    }
    return new ExampleResponse();
}
```

### <a name="bi-directional-streaming-method"></a><span data-ttu-id="7ac84-161">İki yönlü akış yöntemi</span><span class="sxs-lookup"><span data-stu-id="7ac84-161">Bi-directional streaming method</span></span>

<span data-ttu-id="7ac84-162">Çift yönlü bir akış yöntemi bir ileti *almadan başlar.*</span><span class="sxs-lookup"><span data-stu-id="7ac84-162">A bi-directional streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="7ac84-163">`requestStream`Parametresi istemcisinden iletileri okumak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="7ac84-163">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="7ac84-164">Yöntemi, ile ileti gönderilmesini tercih edebilir `responseStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="7ac84-164">The method can choose to send messages with `responseStream.WriteAsync`.</span></span> <span data-ttu-id="7ac84-165">Yöntemin döndürdüğü iki yönlü akış çağrısı tamamlanır:</span><span class="sxs-lookup"><span data-stu-id="7ac84-165">A bi-directional streaming call is complete when the the method returns:</span></span>

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    await foreach (var message in requestStream.ReadAllAsync())
    {
        await responseStream.WriteAsync(new ExampleResponse());
    }
}
```

<span data-ttu-id="7ac84-166">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="7ac84-166">The preceding code:</span></span>

* <span data-ttu-id="7ac84-167">Her istek için bir yanıt gönderir.</span><span class="sxs-lookup"><span data-stu-id="7ac84-167">Sends a response for each request.</span></span>
* <span data-ttu-id="7ac84-168">, İki yönlü akış için temel bir kullanımdır.</span><span class="sxs-lookup"><span data-stu-id="7ac84-168">Is a basic usage of bi-directional streaming.</span></span>

<span data-ttu-id="7ac84-169">İstekleri okuma ve yanıtları eşzamanlı gönderme gibi daha karmaşık senaryoları desteklemek mümkündür:</span><span class="sxs-lookup"><span data-stu-id="7ac84-169">It is possible to support more complex scenarios, such as reading requests and sending responses simultaneously:</span></span>

```csharp
public override async Task StreamingBothWays(IAsyncStreamReader<ExampleRequest> requestStream,
    IServerStreamWriter<ExampleResponse> responseStream, ServerCallContext context)
{
    // Read requests in a background task.
    var readTask = Task.Run(async () =>
    {
        await foreach (var message in requestStream.ReadAllAsync())
        {
            // Process request.
        }
    });
    
    // Send responses until the client signals that it is complete.
    while (!readTask.IsCompleted)
    {
        await responseStream.WriteAsync(new ExampleResponse());
        await Task.Delay(TimeSpan.FromSeconds(1), context.CancellationToken);
    }
}
```

<span data-ttu-id="7ac84-170">Çift yönlü bir akış yönteminde, istemci ve hizmet herhangi bir zamanda iletileri birbirlerine gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="7ac84-170">In a bi-directional streaming method, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="7ac84-171">İki yönlü yöntemin en iyi uygulanması gereksinimlere göre farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="7ac84-171">The best implementation of a bi-directional method varies depending upon requirements.</span></span>

## <a name="access-grpc-request-headers"></a><span data-ttu-id="7ac84-172">GRPC isteği üst bilgilerine erişin</span><span class="sxs-lookup"><span data-stu-id="7ac84-172">Access gRPC request headers</span></span>

<span data-ttu-id="7ac84-173">İstek iletisi, bir istemcinin bir gRPC hizmetine veri gönderebilmesi için tek yol değildir.</span><span class="sxs-lookup"><span data-stu-id="7ac84-173">A request message is not the only way for a client to send data to a gRPC service.</span></span> <span data-ttu-id="7ac84-174">Üst bilgi değerleri kullanılarak bir hizmette kullanılabilir `ServerCallContext.RequestHeaders` .</span><span class="sxs-lookup"><span data-stu-id="7ac84-174">Header values are available in a service using `ServerCallContext.RequestHeaders`.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a><span data-ttu-id="7ac84-175">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7ac84-175">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/client>
