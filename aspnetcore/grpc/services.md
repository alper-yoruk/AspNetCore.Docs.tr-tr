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
ms.openlocfilehash: 878792120d69bea9ca6f620a87a7e04da2ec1815
ms.sourcegitcommit: 111b4e451da2e275fb074cde5d8a84b26a81937d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89040846"
---
# <a name="create-grpc-services-and-methods"></a><span data-ttu-id="489bd-103">GRPC Hizmetleri ve yöntemleri oluşturma</span><span class="sxs-lookup"><span data-stu-id="489bd-103">Create gRPC services and methods</span></span>

<span data-ttu-id="489bd-104">, [James bAyKiNg](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="489bd-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="489bd-105">Bu belgede, C# ' de gRPC Hizmetleri ve yöntemlerinin nasıl oluşturulacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="489bd-105">This document explains how to create gRPC services and methods in C#.</span></span> <span data-ttu-id="489bd-106">Konu başlıkları şunlardır:</span><span class="sxs-lookup"><span data-stu-id="489bd-106">Topics include:</span></span>

* <span data-ttu-id="489bd-107">*. Proto* dosyalarında Hizmetleri ve yöntemleri tanımlama.</span><span class="sxs-lookup"><span data-stu-id="489bd-107">How to define services and methods in *.proto* files.</span></span>
* <span data-ttu-id="489bd-108">GRPC C# araçları kullanılarak oluşturulan kod.</span><span class="sxs-lookup"><span data-stu-id="489bd-108">Generated code using gRPC C# tooling.</span></span>
* <span data-ttu-id="489bd-109">GRPC Hizmetleri ve yöntemleri uygulama.</span><span class="sxs-lookup"><span data-stu-id="489bd-109">Implementing gRPC services and methods.</span></span>

## <a name="create-new-grpc-services"></a><span data-ttu-id="489bd-110">Yeni gRPC hizmetleri oluşturma</span><span class="sxs-lookup"><span data-stu-id="489bd-110">Create new gRPC services</span></span>

<span data-ttu-id="489bd-111">[C# ile GRPC Hizmetleri,](xref:grpc/basics) GRPC 'nin sözleşme-API geliştirmeye ilk yaklaşımı getirmiştir.</span><span class="sxs-lookup"><span data-stu-id="489bd-111">[gRPC services with C#](xref:grpc/basics) introduced gRPC's contract-first approach to API development.</span></span> <span data-ttu-id="489bd-112">Hizmetler ve mesajlar *. proto* dosyalarında tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="489bd-112">Services and messages are defined in *.proto* files.</span></span> <span data-ttu-id="489bd-113">C# araçları daha sonra *. proto* dosyalarından kod oluşturur.</span><span class="sxs-lookup"><span data-stu-id="489bd-113">C# tooling then generates code from *.proto* files.</span></span> <span data-ttu-id="489bd-114">Sunucu tarafı varlıklar için, her bir hizmet için bir soyut temel tür ve tüm iletiler için sınıflar oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="489bd-114">For server-side assets, an abstract base type is generated for each service, along with classes for any messages.</span></span>

<span data-ttu-id="489bd-115">Aşağıdaki *. proto* dosyası:</span><span class="sxs-lookup"><span data-stu-id="489bd-115">The following *.proto* file:</span></span>

* <span data-ttu-id="489bd-116">Bir `Greeter` hizmeti tanımlar.</span><span class="sxs-lookup"><span data-stu-id="489bd-116">Defines a `Greeter` service.</span></span>
* <span data-ttu-id="489bd-117">`Greeter`Hizmet bir çağrıyı tanımlar `SayHello` .</span><span class="sxs-lookup"><span data-stu-id="489bd-117">The `Greeter` service defines a `SayHello` call.</span></span>
* <span data-ttu-id="489bd-118">`SayHello` bir `HelloRequest` ileti gönderir ve bir `HelloReply` ileti alır</span><span class="sxs-lookup"><span data-stu-id="489bd-118">`SayHello` sends a `HelloRequest` message and receives a `HelloReply` message</span></span>

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

<span data-ttu-id="489bd-119">C# araçları, C# `GreeterBase` temel türünü oluşturur:</span><span class="sxs-lookup"><span data-stu-id="489bd-119">C# tooling generates the C# `GreeterBase` base type:</span></span>

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

<span data-ttu-id="489bd-120">Varsayılan olarak, oluşturulan `GreeterBase` hiçbir şey yapmaz.</span><span class="sxs-lookup"><span data-stu-id="489bd-120">By default the generated `GreeterBase` doesn't do anything.</span></span> <span data-ttu-id="489bd-121">Sanal `SayHello` yöntemi, `UNIMPLEMENTED` çağıran istemcilere bir hata döndürür.</span><span class="sxs-lookup"><span data-stu-id="489bd-121">Its virtual `SayHello` method will return an `UNIMPLEMENTED` error to any clients that call it.</span></span> <span data-ttu-id="489bd-122">Hizmetin yararlı olması için bir uygulama için somut bir uygulama oluşturmanız gerekir `GreeterBase` :</span><span class="sxs-lookup"><span data-stu-id="489bd-122">For the service to be useful an app must create a concrete implementation of `GreeterBase`:</span></span>

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> UnaryCall(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

<span data-ttu-id="489bd-123">Hizmet uygulaması, uygulamaya kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="489bd-123">The service implementation is registered with the app.</span></span> <span data-ttu-id="489bd-124">Hizmet ASP.NET Core gRPC tarafından barındırılıyorsa, yöntemi ile yönlendirme ardışık düzenine eklenmelidir `MapGrpcService` .</span><span class="sxs-lookup"><span data-stu-id="489bd-124">If the service is hosted by ASP.NET Core gRPC, it should be added to the routing pipeline with the `MapGrpcService` method.</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

<span data-ttu-id="489bd-125">Daha fazla bilgi edinmek için bkz. <xref:grpc/aspnetcore>.</span><span class="sxs-lookup"><span data-stu-id="489bd-125">See <xref:grpc/aspnetcore> for more information.</span></span>

## <a name="implement-grpc-methods"></a><span data-ttu-id="489bd-126">GRPC yöntemlerini uygulama</span><span class="sxs-lookup"><span data-stu-id="489bd-126">Implement gRPC methods</span></span>

<span data-ttu-id="489bd-127">GRPC hizmeti farklı türde yöntemlere sahip olabilir.</span><span class="sxs-lookup"><span data-stu-id="489bd-127">A gRPC service can have different types of methods.</span></span> <span data-ttu-id="489bd-128">İletilerin bir hizmet tarafından gönderilme ve alınma şekli, tanımlanan yöntemin türüne bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="489bd-128">How messages are sent and received by a service depends on the type of method defined.</span></span> <span data-ttu-id="489bd-129">GRPC Yöntem türleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="489bd-129">The gRPC method types are:</span></span>

* <span data-ttu-id="489bd-130">Birli</span><span class="sxs-lookup"><span data-stu-id="489bd-130">Unary</span></span>
* <span data-ttu-id="489bd-131">Sunucu akışı</span><span class="sxs-lookup"><span data-stu-id="489bd-131">Server streaming</span></span>
* <span data-ttu-id="489bd-132">İstemci akışı</span><span class="sxs-lookup"><span data-stu-id="489bd-132">Client streaming</span></span>
* <span data-ttu-id="489bd-133">İki yönlü akış</span><span class="sxs-lookup"><span data-stu-id="489bd-133">Bi-directional streaming</span></span>

<span data-ttu-id="489bd-134">Akış çağrıları `stream` *. proto* dosyasında anahtar sözcüğüyle belirtilir.</span><span class="sxs-lookup"><span data-stu-id="489bd-134">Streaming calls are specified with the `stream` keyword in the *.proto* file.</span></span> <span data-ttu-id="489bd-135">`stream` bir çağrının istek iletisine, yanıt iletisine veya her ikisine birden yerleştirilebilecek.</span><span class="sxs-lookup"><span data-stu-id="489bd-135">`stream` can be placed on a call's request message, response message, or both.</span></span>

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

<span data-ttu-id="489bd-136">Her çağrı türünün farklı bir yöntem imzası vardır.</span><span class="sxs-lookup"><span data-stu-id="489bd-136">Each call type has a different method signature.</span></span> <span data-ttu-id="489bd-137">Somut bir uygulamadaki soyut temel hizmet türünden oluşturulan yöntemlerin üzerine yazmak, doğru bağımsız değişkenlerin ve dönüş türünün kullanılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="489bd-137">Overriding generated methods from the abstract base service type in a concrete implementation ensures the correct arguments and return type are used.</span></span>

### <a name="unary-method"></a><span data-ttu-id="489bd-138">Birli yöntemi</span><span class="sxs-lookup"><span data-stu-id="489bd-138">Unary method</span></span>

<span data-ttu-id="489bd-139">Birli Yöntem, istek iletisini parametre olarak alır ve yanıtı döndürür.</span><span class="sxs-lookup"><span data-stu-id="489bd-139">A unary method gets the request message as a parameter, and returns the response.</span></span> <span data-ttu-id="489bd-140">Yanıt döndürüldüğünde birli çağrı tamamlanır.</span><span class="sxs-lookup"><span data-stu-id="489bd-140">A unary call is complete when the response is returned.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

<span data-ttu-id="489bd-141">Birli çağrılar, [Web API denetleyicilerindeki eylemlere](xref:web-api/index)en çok benzer.</span><span class="sxs-lookup"><span data-stu-id="489bd-141">Unary calls are the most similar to [actions on web API controllers](xref:web-api/index).</span></span> <span data-ttu-id="489bd-142">Bir önemli fark gRPC yöntemi eylemlerden oluşur gRPC metotları bir isteğin parçalarını farklı yöntem bağımsız değişkenlerine bağlanamaz.</span><span class="sxs-lookup"><span data-stu-id="489bd-142">One important difference gRPC methods have from actions is gRPC methods are not able to bind parts of a request to different method arguments.</span></span> <span data-ttu-id="489bd-143">gRPC yöntemlerinin her zaman gelen istek verileri için bir ileti bağımsız değişkeni vardır.</span><span class="sxs-lookup"><span data-stu-id="489bd-143">gRPC methods always have one message argument for the incoming request data.</span></span> <span data-ttu-id="489bd-144">Bir gRPC hizmetine, istek iletisinde alanlar yapılarak birden çok değer gönderilebilir:</span><span class="sxs-lookup"><span data-stu-id="489bd-144">Multiple values can still be sent to a gRPC service by making them fields on the request message:</span></span>

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a><span data-ttu-id="489bd-145">Sunucu akış yöntemi</span><span class="sxs-lookup"><span data-stu-id="489bd-145">Server streaming method</span></span>

<span data-ttu-id="489bd-146">Sunucu akış yöntemi istek iletisini bir parametre olarak alır.</span><span class="sxs-lookup"><span data-stu-id="489bd-146">A server streaming method gets the request message as a parameter.</span></span> <span data-ttu-id="489bd-147">Birden çok ileti çağırana geri akışı olabileceğinden, `responseStream.WriteAsync` yanıt iletilerini göndermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="489bd-147">Because multiple messages can be streamed back to the caller, `responseStream.WriteAsync` is used to send response messages.</span></span> <span data-ttu-id="489bd-148">Yöntem döndürüldüğünde bir sunucu akış çağrısı tamamlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="489bd-148">A server streaming call is complete when the method returns.</span></span>

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

<span data-ttu-id="489bd-149">Sunucu akış yöntemi başlatıldıktan sonra istemcinin ek ileti veya veri gönderme yolu yoktur.</span><span class="sxs-lookup"><span data-stu-id="489bd-149">The client has no way to send additional messages or data once the server streaming method has started.</span></span> <span data-ttu-id="489bd-150">Bazı akış yöntemleri, süresiz olarak çalışacak şekilde tasarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="489bd-150">Some streaming methods are designed to run forever.</span></span> <span data-ttu-id="489bd-151">Sürekli akış yöntemleri için, istemci artık gerekli olmadığında çağrıyı iptal edebilir.</span><span class="sxs-lookup"><span data-stu-id="489bd-151">For continuous streaming methods, a client can cancel the call when it's no longer needed.</span></span> <span data-ttu-id="489bd-152">İptal gerçekleştiğinde, istemci sunucuya bir sinyal gönderir ve [Servercallcontext. CancellationToken](xref:System.Threading.CancellationToken) oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="489bd-152">When cancellation happens the client sends a signal to the server and the [ServerCallContext.CancellationToken](xref:System.Threading.CancellationToken) is raised.</span></span> <span data-ttu-id="489bd-153">`CancellationToken`Belirtecin zaman uyumsuz yöntemlerle sunucuda kullanılması gerekir, bu nedenle:</span><span class="sxs-lookup"><span data-stu-id="489bd-153">The `CancellationToken` token should be used on the server with async methods so that:</span></span>

* <span data-ttu-id="489bd-154">Tüm zaman uyumsuz işler, akış çağrısıyla birlikte iptal edilir.</span><span class="sxs-lookup"><span data-stu-id="489bd-154">Any asynchronous work is canceled together with the streaming call.</span></span>
* <span data-ttu-id="489bd-155">Yöntemi hızla çıkar.</span><span class="sxs-lookup"><span data-stu-id="489bd-155">The method exits quickly.</span></span>

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

### <a name="client-streaming-method"></a><span data-ttu-id="489bd-156">İstemci akış yöntemi</span><span class="sxs-lookup"><span data-stu-id="489bd-156">Client streaming method</span></span>

<span data-ttu-id="489bd-157">Bir istemci akış yöntemi bir ileti *almadan başlar.*</span><span class="sxs-lookup"><span data-stu-id="489bd-157">A client streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="489bd-158">`requestStream`Parametresi istemcisinden iletileri okumak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="489bd-158">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="489bd-159">Yanıt iletisi döndürüldüğünde bir istemci akış çağrısı tamamlanır:</span><span class="sxs-lookup"><span data-stu-id="489bd-159">A client streaming call is complete when a response message is returned:</span></span>

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

<span data-ttu-id="489bd-160">C# 8 veya sonraki bir sürümünü kullanırken, `await foreach` söz dizimi iletileri okumak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="489bd-160">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="489bd-161">`IAsyncStreamReader<T>.ReadAllAsync()`Uzantı yöntemi istek akışındaki tüm iletileri okur:</span><span class="sxs-lookup"><span data-stu-id="489bd-161">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the request stream:</span></span>

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

### <a name="bi-directional-streaming-method"></a><span data-ttu-id="489bd-162">İki yönlü akış yöntemi</span><span class="sxs-lookup"><span data-stu-id="489bd-162">Bi-directional streaming method</span></span>

<span data-ttu-id="489bd-163">Çift yönlü bir akış yöntemi bir ileti *almadan başlar.*</span><span class="sxs-lookup"><span data-stu-id="489bd-163">A bi-directional streaming method starts *without* the method receiving a message.</span></span> <span data-ttu-id="489bd-164">`requestStream`Parametresi istemcisinden iletileri okumak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="489bd-164">The `requestStream` parameter is used to read messages from the client.</span></span> <span data-ttu-id="489bd-165">Yöntemi, ile ileti gönderilmesini tercih edebilir `responseStream.WriteAsync` .</span><span class="sxs-lookup"><span data-stu-id="489bd-165">The method can choose to send messages with `responseStream.WriteAsync`.</span></span> <span data-ttu-id="489bd-166">Yöntemin döndürdüğü iki yönlü akış çağrısı tamamlanır:</span><span class="sxs-lookup"><span data-stu-id="489bd-166">A bi-directional streaming call is complete when the the method returns:</span></span>

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

<span data-ttu-id="489bd-167">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="489bd-167">The preceding code:</span></span>

* <span data-ttu-id="489bd-168">Her istek için bir yanıt gönderir.</span><span class="sxs-lookup"><span data-stu-id="489bd-168">Sends a response for each request.</span></span>
* <span data-ttu-id="489bd-169">, İki yönlü akış için temel bir kullanımdır.</span><span class="sxs-lookup"><span data-stu-id="489bd-169">Is a basic usage of bi-directional streaming.</span></span>

<span data-ttu-id="489bd-170">İstekleri okuma ve yanıtları eşzamanlı gönderme gibi daha karmaşık senaryoları desteklemek mümkündür:</span><span class="sxs-lookup"><span data-stu-id="489bd-170">It is possible to support more complex scenarios, such as reading requests and sending responses simultaneously:</span></span>

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

<span data-ttu-id="489bd-171">Çift yönlü bir akış yönteminde, istemci ve hizmet herhangi bir zamanda iletileri birbirlerine gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="489bd-171">In a bi-directional streaming method, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="489bd-172">İki yönlü yöntemin en iyi uygulanması gereksinimlere göre farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="489bd-172">The best implementation of a bi-directional method varies depending upon requirements.</span></span>

## <a name="access-grpc-request-headers"></a><span data-ttu-id="489bd-173">GRPC isteği üst bilgilerine erişin</span><span class="sxs-lookup"><span data-stu-id="489bd-173">Access gRPC request headers</span></span>

<span data-ttu-id="489bd-174">İstek iletisi, bir istemcinin bir gRPC hizmetine veri gönderebilmesi için tek yol değildir.</span><span class="sxs-lookup"><span data-stu-id="489bd-174">A request message is not the only way for a client to send data to a gRPC service.</span></span> <span data-ttu-id="489bd-175">Üst bilgi değerleri kullanılarak bir hizmette kullanılabilir `ServerCallContext.RequestHeaders` .</span><span class="sxs-lookup"><span data-stu-id="489bd-175">Header values are available in a service using `ServerCallContext.RequestHeaders`.</span></span>

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a><span data-ttu-id="489bd-176">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="489bd-176">Additional resources</span></span>

* <xref:grpc/basics>
* <xref:grpc/client>
