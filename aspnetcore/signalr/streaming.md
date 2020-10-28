---
title: ASP.NET Core 'da akış kullanma SignalR
author: bradygaster
description: İstemci ve sunucu arasında veri akışını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 11/12/2019
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
uid: signalr/streaming
ms.openlocfilehash: 732198cf07392bda008c9cc1c9768df2500852c0
ms.sourcegitcommit: 2e3a967331b2c69f585dd61e9ad5c09763615b44
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92690634"
---
# <a name="use-streaming-in-aspnet-core-no-locsignalr"></a>ASP.NET Core 'da akış kullanma SignalR

[Brennan Conroy](https://github.com/BrennanConroy) tarafından

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core SignalR , istemciden sunucuya ve sunucudan istemciye akışı destekler. Bu, veri parçalarının zaman içinde nereden ulaştığını senaryolar için yararlıdır. Akış sırasında her parça, tüm verilerin kullanılabilir hale gelmesini beklemek yerine istemciye veya sunucuya gönderilir.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core SignalR , sunucu yöntemlerinin akış dönüş değerlerini destekler. Bu, veri parçalarının zaman içinde nereden ulaştığını senaryolar için yararlıdır. Bir dönüş değeri istemciye akışa eklendiğinde, her parça, tüm verilerin kullanılabilir hale gelmesini beklemek yerine istemciye gönderilir.

::: moniker-end

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="set-up-a-hub-for-streaming"></a>Akış için bir hub ayarlama

::: moniker range=">= aspnetcore-3.0"

Hub yöntemi,,, veya döndürdüğünde otomatik olarak akış hub yöntemi haline gelir <xref:System.Collections.Generic.IAsyncEnumerable`1> <xref:System.Threading.Channels.ChannelReader%601> `Task<IAsyncEnumerable<T>>` `Task<ChannelReader<T>>` .

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bir hub yöntemi, bir veya döndürdüğünde otomatik olarak akış hub yöntemi haline gelir <xref:System.Threading.Channels.ChannelReader%601> `Task<ChannelReader<T>>` .

::: moniker-end

### <a name="server-to-client-streaming"></a>Sunucudan istemciye akış

::: moniker range=">= aspnetcore-3.0"

Akış hub 'ı yöntemleri, `IAsyncEnumerable<T>` öğesine ek olarak dönebilir `ChannelReader<T>` . ' In en basit yolu, `IAsyncEnumerable<T>` Aşağıdaki örnekte gösterildiği gibi, hub yöntemini zaman uyumsuz bir yineleyici yöntemi yaparak kullanmaktır. Hub zaman uyumsuz Yineleyici yöntemleri, `CancellationToken` istemci akıştan aboneliği kaldırdığında tetiklenen bir parametreyi kabul edebilir. Zaman uyumsuz Yineleyici yöntemleri, kanallarla ortak olan sorunları önlemek `ChannelReader` için, yeterince erken dönmemek veya yönteminden çıkmak gibi <xref:System.Threading.Channels.ChannelWriter`1> .

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

Aşağıdaki örnekte, kanalları kullanarak istemciye veri akışı hakkında temel bilgiler gösterilmektedir. Nesnesine bir nesne yazıldığında <xref:System.Threading.Channels.ChannelWriter%601> , nesne hemen istemciye gönderilir. Sonunda, `ChannelWriter` istemciye akışın kapalı olduğunu bildirmek için tamamlanır.

> [!NOTE]
> `ChannelWriter<T>`Arka plan iş parçacığında öğesine yazın ve mümkün olan en `ChannelReader` kısa sürede geri döndürün. Diğer Merkez çağırmaları `ChannelReader` , döndürülünceye kadar engellenir.
>
> [ `try ... catch` Deyimdeki](/dotnet/csharp/language-reference/keywords/try-catch)mantığı sarın. ' İ `Channel` bir [ `finally` blokta](/dotnet/csharp/language-reference/keywords/try-catch-finally)doldurun. Bir hata akışı yapmak istiyorsanız, blok içinde yakalayın `catch` ve `finally` bloğa yazın.

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Streaming hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[Streaming hub method](streaming/samples/2.2/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[Streaming hub method](streaming/samples/2.1/Hubs/StreamHub.cs?name=snippet1)]

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

Sunucudan istemciye akış hub 'ı yöntemleri, `CancellationToken` istemci akıştan aboneliği kaldırdığında tetiklenen bir parametreyi kabul edebilir. Sunucu işlemini durdurmak ve istemci akışın sonundan önce bağlantıyı kestiğinde tüm kaynakları serbest bırakmak için bu belirteci kullanın.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>İstemciden sunucuya akış

Hub yöntemi, veya türünde bir veya daha fazla nesne kabul ettiğinde, istemci-sunucu akış hub yöntemi otomatik olarak olur <xref:System.Threading.Channels.ChannelReader%601> <xref:System.Collections.Generic.IAsyncEnumerable%601> . Aşağıdaki örnek, istemciden gönderilen akış verilerini okumayla ilgili temel bilgileri gösterir. İstemci öğesine her yazdığında <xref:System.Threading.Channels.ChannelWriter%601> , veriler `ChannelReader` hub yönteminin okuduğu sunucuda üzerine yazılır.

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<xref:System.Collections.Generic.IAsyncEnumerable%601>Yönteminin bir sürümü aşağıda verilmiştir.

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
public async Task UploadStream(IAsyncEnumerable<string> stream)
{
    await foreach (var item in stream)
    {
        Console.WriteLine(item);
    }
}
```

::: moniker-end

## <a name="net-client"></a>.NET istemcisi

### <a name="server-to-client-streaming"></a>Sunucudan istemciye akış


::: moniker range=">= aspnetcore-3.0"

`StreamAsync`Ve `StreamAsChannelAsync` üzerindeki yöntemleri, `HubConnection` sunucudan istemciye akış yöntemlerini çağırmak için kullanılır. Hub yöntemi veya hub metodunda tanımlanan bağımsız değişkenleri ya da ' a geçirin `StreamAsync` `StreamAsChannelAsync` . Ve üzerindeki genel parametresi `StreamAsync<T>` , `StreamAsChannelAsync<T>` akış yöntemi tarafından döndürülen nesne türünü belirtir. Veya türündeki bir nesne `IAsyncEnumerable<T>` , `ChannelReader<T>` akış çağrısından döndürülür ve istemcideki akışı temsil eder.

Şunu `StreamAsync` döndüren bir örnek `IAsyncEnumerable<int>` :

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var stream = await hubConnection.StreamAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

await foreach (var count in stream)
{
    Console.WriteLine($"{count}");
}

Console.WriteLine("Streaming completed");
```

Şunu döndüren karşılık gelen bir `StreamAsChannelAsync` örnek `ChannelReader<int>` :

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

`StreamAsChannelAsync`Üzerinde yöntemi, `HubConnection` sunucudan istemciye akış yöntemini çağırmak için kullanılır. Hub yöntemi içinde tanımlanan bağımsız değişkenleri ve hub metodunu geçirin `StreamAsChannelAsync` . Üzerinde genel parametresi, `StreamAsChannelAsync<T>` akış yöntemi tarafından döndürülen nesne türünü belirtir. Bir `ChannelReader<T>` akış çağrısından döndürülür ve istemcideki akışı temsil eder.

```csharp
// Call "Cancel" on this CancellationTokenSource to send a cancellation message to
// the server, which will trigger the corresponding token in the hub method.
var cancellationTokenSource = new CancellationTokenSource();
var channel = await hubConnection.StreamAsChannelAsync<int>(
    "Counter", 10, 500, cancellationTokenSource.Token);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range="= aspnetcore-2.1"

`StreamAsChannelAsync`Üzerinde yöntemi, `HubConnection` sunucudan istemciye akış yöntemini çağırmak için kullanılır. Hub yöntemi içinde tanımlanan bağımsız değişkenleri ve hub metodunu geçirin `StreamAsChannelAsync` . Üzerinde genel parametresi, `StreamAsChannelAsync<T>` akış yöntemi tarafından döndürülen nesne türünü belirtir. Bir `ChannelReader<T>` akış çağrısından döndürülür ve istemcideki akışı temsil eder.

```csharp
var channel = await hubConnection
    .StreamAsChannelAsync<int>("Counter", 10, 500, CancellationToken.None);

// Wait asynchronously for data to become available
while (await channel.WaitToReadAsync())
{
    // Read all currently available data synchronously, before waiting for more data
    while (channel.TryRead(out var count))
    {
        Console.WriteLine($"{count}");
    }
}

Console.WriteLine("Streaming completed");
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>İstemciden sunucuya akış

.NET istemcisinden istemciden sunucuya akış hub 'ı yöntemini çağırmak için iki yol vardır. `IAsyncEnumerable<T>` `ChannelReader` `SendAsync` `InvokeAsync` `StreamAsChannelAsync` Çağrılan hub yöntemine bağlı olarak, veya bir bağımsız değişken olarak bir ya da bir bağımsız değişken olarak geçirebilirsiniz.

Veriler `IAsyncEnumerable` veya nesnesine her yazıldığında `ChannelWriter` , sunucudaki hub yöntemi istemciden alınan verilerle yeni bir öğe alır.

Bir nesne kullanılıyorsa `IAsyncEnumerable` , akış öğeleri döndüren yöntem çıktıktan sonra akış sonlanır.

[!INCLUDE[](~/includes/csharp-8-required.md)]

```csharp
async IAsyncEnumerable<string> clientStreamData()
{
    for (var i = 0; i < 5; i++)
    {
        var data = await FetchSomeData();
        yield return data;
    }
    //After the for loop has completed and the local function exits the stream completion will be sent.
}

await connection.SendAsync("UploadStream", clientStreamData());
```

Ya da kullanıyorsanız `ChannelWriter` , şu ile kanalı tamamlıyoruz `channel.Writer.Complete()` :

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a>JavaScript istemcisi

### <a name="server-to-client-streaming"></a>Sunucudan istemciye akış

JavaScript istemcileri, ile hub 'larda sunucudan istemciye akış yöntemlerini çağırır `connection.stream` . `stream`Yöntemi iki bağımsız değişkeni kabul eder:

* Hub yönteminin adı. Aşağıdaki örnekte, hub yöntemi adı `Counter` .
* Hub yönteminde tanımlanan bağımsız değişkenler. Aşağıdaki örnekte, bağımsız değişkenler alacak akış öğesi sayısı ve akış öğeleri arasındaki gecikme için bir sayıdır.

`connection.stream``IStreamResult`bir yöntemi içeren bir döndürür `subscribe` . ' A `IStreamSubscriber` geçirin `subscribe` ve `next` `error` `complete` çağrıdan bildirim almak için, ve geri çağırmaları ayarlayın `stream` .

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

Akışı istemciden sonlandırmak için, `dispose` yönteminden döndürülen üzerinde yöntemi çağırın `ISubscription` `subscribe` . Bu yöntemi çağırmak `CancellationToken` , bir tane sağladıysanız hub yönteminin parametresinin iptal edilmesine neden olur.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

Akışı istemciden sonlandırmak için, `dispose` yönteminden döndürülen üzerinde yöntemi çağırın `ISubscription` `subscribe` .

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a>İstemciden sunucuya akış

JavaScript istemcileri `Subject` `send` , `invoke` `stream` çağrılan hub yöntemine bağlı olarak, veya olarak bir bağımsız değişken olarak geçirerek hub 'larda istemciden sunucuya akış yöntemlerini çağırır. , `Subject` Gibi görünen bir sınıftır `Subject` . Örneğin, RxJS ' de, bu kitaplıktaki [Konu](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) sınıfını kullanabilirsiniz.

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

`subject.next(item)`Bir öğeyle çağırmak öğeyi akışa yazar ve hub yöntemi sunucudaki öğeyi alır.

Akışı sonlandırmak için çağırın `subject.complete()` .

## <a name="java-client"></a>Java istemcisi

### <a name="server-to-client-streaming"></a>Sunucudan istemciye akış

SignalRJava istemcisi, `stream` akış yöntemlerini çağırmak için yöntemini kullanır. `stream` üç veya daha fazla bağımsız değişken kabul eder:

* Akış öğelerinin beklenen türü.
* Hub yönteminin adı.
* Hub yönteminde tanımlanan bağımsız değişkenler.

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

`stream`Üzerindeki yöntemi, `HubConnection` akış öğesi türü Için bir observable döndürür. Observable türünün `subscribe` yöntemi nerede `onNext` , `onError` ve `onCompleted` işleyiciler tanımlanmıştır.

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* [Merkezler](xref:signalr/hubs)
* [.NET istemcisi](xref:signalr/dotnet-client)
* [JavaScript istemcisi](xref:signalr/javascript-client)
* [Azure 'da yayımlama](xref:signalr/publish-to-azure-web-app)
