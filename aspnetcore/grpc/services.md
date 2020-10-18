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
ms.openlocfilehash: c4d37556a3345d275f45c537a40908c5966fe015
ms.sourcegitcommit: ecae2aa432628b9181d1fa11037c231c7dd56c9e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92113627"
---
# <a name="create-grpc-services-and-methods"></a>GRPC Hizmetleri ve yöntemleri oluşturma

, [James bAyKiNg](https://twitter.com/jamesnk)

Bu belgede, C# ' de gRPC Hizmetleri ve yöntemlerinin nasıl oluşturulacağı açıklanmaktadır. Konu başlıkları şunlardır:

* *. Proto* dosyalarında Hizmetleri ve yöntemleri tanımlama.
* GRPC C# araçları kullanılarak oluşturulan kod.
* GRPC Hizmetleri ve yöntemleri uygulama.

## <a name="create-new-grpc-services"></a>Yeni gRPC hizmetleri oluşturma

[C# ile GRPC Hizmetleri,](xref:grpc/basics) GRPC 'nin sözleşme-API geliştirmeye ilk yaklaşımı getirmiştir. Hizmetler ve mesajlar *. proto* dosyalarında tanımlanır. C# araçları daha sonra *. proto* dosyalarından kod oluşturur. Sunucu tarafı varlıklar için, her bir hizmet için bir soyut temel tür ve tüm iletiler için sınıflar oluşturulur.

Aşağıdaki *. proto* dosyası:

* Bir `Greeter` hizmeti tanımlar.
* `Greeter`Hizmet bir çağrıyı tanımlar `SayHello` .
* `SayHello` bir `HelloRequest` ileti gönderir ve bir `HelloReply` ileti alır

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

C# araçları, C# `GreeterBase` temel türünü oluşturur:

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

Varsayılan olarak, oluşturulan `GreeterBase` hiçbir şey yapmaz. Sanal `SayHello` yöntemi, `UNIMPLEMENTED` çağıran istemcilere bir hata döndürür. Hizmetin yararlı olması için bir uygulama için somut bir uygulama oluşturmanız gerekir `GreeterBase` :

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloRequest { Message = $"Hello {request.Name}" });
    }
}
```

Hizmet uygulaması, uygulamaya kaydedilir. Hizmet ASP.NET Core gRPC tarafından barındırılıyorsa, yöntemi ile yönlendirme ardışık düzenine eklenmelidir `MapGrpcService` .

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapGrpcService<GreeterService>();
});
```

Daha fazla bilgi edinmek için bkz. <xref:grpc/aspnetcore>.

## <a name="implement-grpc-methods"></a>GRPC yöntemlerini uygulama

GRPC hizmeti farklı türde yöntemlere sahip olabilir. İletilerin bir hizmet tarafından gönderilme ve alınma şekli, tanımlanan yöntemin türüne bağlıdır. GRPC Yöntem türleri şunlardır:

* Birli
* Sunucu akışı
* İstemci akışı
* İki yönlü akış

Akış çağrıları `stream` *. proto* dosyasında anahtar sözcüğüyle belirtilir. `stream` bir çağrının istek iletisine, yanıt iletisine veya her ikisine birden yerleştirilebilecek.

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

Her çağrı türünün farklı bir yöntem imzası vardır. Somut bir uygulamadaki soyut temel hizmet türünden oluşturulan yöntemlerin üzerine yazmak, doğru bağımsız değişkenlerin ve dönüş türünün kullanılmasını sağlar.

### <a name="unary-method"></a>Birli yöntemi

Birli Yöntem, istek iletisini parametre olarak alır ve yanıtı döndürür. Yanıt döndürüldüğünde birli çağrı tamamlanır.

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request,
    ServerCallContext context)
{
    var response = new ExampleResponse();
    return Task.FromResult(response);
}
```

Birli çağrılar, [Web API denetleyicilerindeki eylemlere](xref:web-api/index)en çok benzer. Bir önemli fark gRPC yöntemi eylemlerden oluşur gRPC metotları bir isteğin parçalarını farklı yöntem bağımsız değişkenlerine bağlanamaz. gRPC yöntemlerinin her zaman gelen istek verileri için bir ileti bağımsız değişkeni vardır. Bir gRPC hizmetine, istek iletisinde alanlar yapılarak birden çok değer gönderilebilir:

```protobuf
message ExampleRequest {
    int pageIndex = 1;
    int pageSize = 2;
    bool isDescending = 3;
}
```

### <a name="server-streaming-method"></a>Sunucu akış yöntemi

Sunucu akış yöntemi istek iletisini bir parametre olarak alır. Birden çok ileti çağırana geri akışı olabileceğinden, `responseStream.WriteAsync` yanıt iletilerini göndermek için kullanılır. Yöntem döndürüldüğünde bir sunucu akış çağrısı tamamlanmıştır.

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

Sunucu akış yöntemi başlatıldıktan sonra istemcinin ek ileti veya veri gönderme yolu yoktur. Bazı akış yöntemleri, süresiz olarak çalışacak şekilde tasarlanmıştır. Sürekli akış yöntemleri için, istemci artık gerekli olmadığında çağrıyı iptal edebilir. İptal gerçekleştiğinde, istemci sunucuya bir sinyal gönderir ve [Servercallcontext. CancellationToken](xref:System.Threading.CancellationToken) oluşturulur. `CancellationToken`Belirtecin zaman uyumsuz yöntemlerle sunucuda kullanılması gerekir, bu nedenle:

* Tüm zaman uyumsuz işler, akış çağrısıyla birlikte iptal edilir.
* Yöntemi hızla çıkar.

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

### <a name="client-streaming-method"></a>İstemci akış yöntemi

Bir istemci akış yöntemi bir ileti *almadan başlar.* `requestStream`Parametresi istemcisinden iletileri okumak için kullanılır. Yanıt iletisi döndürüldüğünde bir istemci akış çağrısı tamamlanır:

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

C# 8 veya sonraki bir sürümünü kullanırken, `await foreach` söz dizimi iletileri okumak için kullanılabilir. `IAsyncStreamReader<T>.ReadAllAsync()`Uzantı yöntemi istek akışındaki tüm iletileri okur:

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

### <a name="bi-directional-streaming-method"></a>İki yönlü akış yöntemi

Çift yönlü bir akış yöntemi bir ileti *almadan başlar.* `requestStream`Parametresi istemcisinden iletileri okumak için kullanılır. Yöntemi, ile ileti gönderilmesini tercih edebilir `responseStream.WriteAsync` . Yöntemin döndürdüğü iki yönlü akış çağrısı tamamlanır:

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

Yukarıdaki kod:

* Her istek için bir yanıt gönderir.
* , İki yönlü akış için temel bir kullanımdır.

İstekleri okuma ve yanıtları eşzamanlı gönderme gibi daha karmaşık senaryoları desteklemek mümkündür:

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

Çift yönlü bir akış yönteminde, istemci ve hizmet herhangi bir zamanda iletileri birbirlerine gönderebilir. İki yönlü yöntemin en iyi uygulanması gereksinimlere göre farklılık gösterir.

## <a name="access-grpc-request-headers"></a>GRPC isteği üst bilgilerine erişin

İstek iletisi, bir istemcinin bir gRPC hizmetine veri gönderebilmesi için tek yol değildir. Üst bilgi değerleri kullanılarak bir hizmette kullanılabilir `ServerCallContext.RequestHeaders` .

```csharp
public override Task<ExampleResponse> UnaryCall(ExampleRequest request, ServerCallContext context)
{
    var userAgent = context.RequestHeaders.GetValue("user-agent");
    // ...

    return Task.FromResult(new ExampleResponse());
}
```

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:grpc/basics>
* <xref:grpc/client>
