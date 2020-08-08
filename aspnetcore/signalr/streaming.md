---
title: ASP.NET Core 'da akış kullanmaSignalR
author: bradygaster
description: İstemci ve sunucu arasında veri akışını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
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
ms.openlocfilehash: e0eabe711fd69e42bd9bfa5e03a92e1df780e4db
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022517"
---
# <a name="use-streaming-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="42332-103">ASP.NET Core 'da akış kullanmaSignalR</span><span class="sxs-lookup"><span data-stu-id="42332-103">Use streaming in ASP.NET Core SignalR</span></span>

<span data-ttu-id="42332-104">[Brennan Conroy](https://github.com/BrennanConroy) tarafından</span><span class="sxs-lookup"><span data-stu-id="42332-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="42332-105">ASP.NET Core SignalR , istemciden sunucuya ve sunucudan istemciye akışı destekler.</span><span class="sxs-lookup"><span data-stu-id="42332-105">ASP.NET Core SignalR supports streaming from client to server and from server to client.</span></span> <span data-ttu-id="42332-106">Bu, veri parçalarının zaman içinde nereden ulaştığını senaryolar için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="42332-106">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="42332-107">Akış sırasında her parça, tüm verilerin kullanılabilir hale gelmesini beklemek yerine istemciye veya sunucuya gönderilir.</span><span class="sxs-lookup"><span data-stu-id="42332-107">When streaming, each fragment is sent to the client or server as soon as it becomes available, rather than waiting for all of the data to become available.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="42332-108">ASP.NET Core SignalR , sunucu yöntemlerinin akış dönüş değerlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="42332-108">ASP.NET Core SignalR supports streaming return values of server methods.</span></span> <span data-ttu-id="42332-109">Bu, veri parçalarının zaman içinde nereden ulaştığını senaryolar için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="42332-109">This is useful for scenarios where fragments of data arrive over time.</span></span> <span data-ttu-id="42332-110">Bir dönüş değeri istemciye akışa eklendiğinde, her parça, tüm verilerin kullanılabilir hale gelmesini beklemek yerine istemciye gönderilir.</span><span class="sxs-lookup"><span data-stu-id="42332-110">When a return value is streamed to the client, each fragment is sent to the client as soon as it becomes available, rather than waiting for all the data to become available.</span></span>

::: moniker-end

<span data-ttu-id="42332-111">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="42332-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/signalr/streaming/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="set-up-a-hub-for-streaming"></a><span data-ttu-id="42332-112">Akış için bir hub ayarlama</span><span class="sxs-lookup"><span data-stu-id="42332-112">Set up a hub for streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="42332-113">Hub yöntemi,,, veya döndürdüğünde otomatik olarak akış hub yöntemi haline gelir <xref:System.Collections.Generic.IAsyncEnumerable`1> <xref:System.Threading.Channels.ChannelReader%601> `Task<IAsyncEnumerable<T>>` `Task<ChannelReader<T>>` .</span><span class="sxs-lookup"><span data-stu-id="42332-113">A hub method automatically becomes a streaming hub method when it returns <xref:System.Collections.Generic.IAsyncEnumerable`1>, <xref:System.Threading.Channels.ChannelReader%601>, `Task<IAsyncEnumerable<T>>`, or `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="42332-114">Bir hub yöntemi, bir veya döndürdüğünde otomatik olarak akış hub yöntemi haline gelir <xref:System.Threading.Channels.ChannelReader%601> `Task<ChannelReader<T>>` .</span><span class="sxs-lookup"><span data-stu-id="42332-114">A hub method automatically becomes a streaming hub method when it returns a <xref:System.Threading.Channels.ChannelReader%601> or a `Task<ChannelReader<T>>`.</span></span>

::: moniker-end

### <a name="server-to-client-streaming"></a><span data-ttu-id="42332-115">Sunucudan istemciye akış</span><span class="sxs-lookup"><span data-stu-id="42332-115">Server-to-client streaming</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="42332-116">Akış hub 'ı yöntemleri, `IAsyncEnumerable<T>` öğesine ek olarak dönebilir `ChannelReader<T>` .</span><span class="sxs-lookup"><span data-stu-id="42332-116">Streaming hub methods can return `IAsyncEnumerable<T>` in addition to `ChannelReader<T>`.</span></span> <span data-ttu-id="42332-117">' In en basit yolu, `IAsyncEnumerable<T>` Aşağıdaki örnekte gösterildiği gibi, hub yöntemini zaman uyumsuz bir yineleyici yöntemi yaparak kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="42332-117">The simplest way to return `IAsyncEnumerable<T>` is by making the hub method an async iterator method as the following sample demonstrates.</span></span> <span data-ttu-id="42332-118">Hub zaman uyumsuz Yineleyici yöntemleri, `CancellationToken` istemci akıştan aboneliği kaldırdığında tetiklenen bir parametreyi kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="42332-118">Hub async iterator methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="42332-119">Zaman uyumsuz Yineleyici yöntemleri, kanallarla ortak olan sorunları önlemek `ChannelReader` için, yeterince erken dönmemek veya yönteminden çıkmak gibi <xref:System.Threading.Channels.ChannelWriter`1> .</span><span class="sxs-lookup"><span data-stu-id="42332-119">Async iterator methods avoid problems common with Channels, such as not returning the `ChannelReader` early enough or exiting the method without completing the <xref:System.Threading.Channels.ChannelWriter`1>.</span></span>

[!INCLUDE[](~/includes/csharp-8-required.md)]

[!code-csharp[Streaming hub async iterator method](streaming/samples/3.0/Hubs/AsyncEnumerableHub.cs?name=snippet_AsyncIterator)]

::: moniker-end

<span data-ttu-id="42332-120">Aşağıdaki örnekte, kanalları kullanarak istemciye veri akışı hakkında temel bilgiler gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="42332-120">The following sample shows the basics of streaming data to the client using Channels.</span></span> <span data-ttu-id="42332-121">Nesnesine bir nesne yazıldığında <xref:System.Threading.Channels.ChannelWriter%601> , nesne hemen istemciye gönderilir.</span><span class="sxs-lookup"><span data-stu-id="42332-121">Whenever an object is written to the <xref:System.Threading.Channels.ChannelWriter%601>, the object is immediately sent to the client.</span></span> <span data-ttu-id="42332-122">Sonunda, `ChannelWriter` istemciye akışın kapalı olduğunu bildirmek için tamamlanır.</span><span class="sxs-lookup"><span data-stu-id="42332-122">At the end, the `ChannelWriter` is completed to tell the client the stream is closed.</span></span>

> [!NOTE]
> <span data-ttu-id="42332-123">`ChannelWriter<T>`Arka plan iş parçacığında öğesine yazın ve mümkün olan en `ChannelReader` kısa sürede geri döndürün.</span><span class="sxs-lookup"><span data-stu-id="42332-123">Write to the `ChannelWriter<T>` on a background thread and return the `ChannelReader` as soon as possible.</span></span> <span data-ttu-id="42332-124">Diğer Merkez çağırmaları `ChannelReader` , döndürülünceye kadar engellenir.</span><span class="sxs-lookup"><span data-stu-id="42332-124">Other hub invocations are blocked until a `ChannelReader` is returned.</span></span>
>
> <span data-ttu-id="42332-125">İçindeki mantığı sarın `try ... catch` .</span><span class="sxs-lookup"><span data-stu-id="42332-125">Wrap logic in a `try ... catch`.</span></span> <span data-ttu-id="42332-126">`Channel` `catch` `catch` Hub yöntemi çağrısının doğru şekilde tamamlandığından emin olmak için içindeki ve dışındaki içindeki öğesini doldurun.</span><span class="sxs-lookup"><span data-stu-id="42332-126">Complete the `Channel` in the `catch` and outside the `catch` to make sure the hub method invocation is completed properly.</span></span>

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

<span data-ttu-id="42332-127">Sunucudan istemciye akış hub 'ı yöntemleri, `CancellationToken` istemci akıştan aboneliği kaldırdığında tetiklenen bir parametreyi kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="42332-127">Server-to-client streaming hub methods can accept a `CancellationToken` parameter that's triggered when the client unsubscribes from the stream.</span></span> <span data-ttu-id="42332-128">Sunucu işlemini durdurmak ve istemci akışın sonundan önce bağlantıyı kestiğinde tüm kaynakları serbest bırakmak için bu belirteci kullanın.</span><span class="sxs-lookup"><span data-stu-id="42332-128">Use this token to stop the server operation and release any resources if the client disconnects before the end of the stream.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="42332-129">İstemciden sunucuya akış</span><span class="sxs-lookup"><span data-stu-id="42332-129">Client-to-server streaming</span></span>

<span data-ttu-id="42332-130">Hub yöntemi, veya türünde bir veya daha fazla nesne kabul ettiğinde, istemci-sunucu akış hub yöntemi otomatik olarak olur <xref:System.Threading.Channels.ChannelReader%601> <xref:System.Collections.Generic.IAsyncEnumerable%601> .</span><span class="sxs-lookup"><span data-stu-id="42332-130">A hub method automatically becomes a client-to-server streaming hub method when it accepts one or more objects of type <xref:System.Threading.Channels.ChannelReader%601> or <xref:System.Collections.Generic.IAsyncEnumerable%601>.</span></span> <span data-ttu-id="42332-131">Aşağıdaki örnek, istemciden gönderilen akış verilerini okumayla ilgili temel bilgileri gösterir.</span><span class="sxs-lookup"><span data-stu-id="42332-131">The following sample shows the basics of reading streaming data sent from the client.</span></span> <span data-ttu-id="42332-132">İstemci öğesine her yazdığında <xref:System.Threading.Channels.ChannelWriter%601> , veriler `ChannelReader` hub yönteminin okuduğu sunucuda üzerine yazılır.</span><span class="sxs-lookup"><span data-stu-id="42332-132">Whenever the client writes to the <xref:System.Threading.Channels.ChannelWriter%601>, the data is written into the `ChannelReader` on the server from which the hub method is reading.</span></span>

[!code-csharp[Streaming upload hub method](streaming/samples/3.0/Hubs/StreamHub.cs?name=snippet2)]

<span data-ttu-id="42332-133"><xref:System.Collections.Generic.IAsyncEnumerable%601>Yönteminin bir sürümü aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="42332-133">An <xref:System.Collections.Generic.IAsyncEnumerable%601> version of the method follows.</span></span>

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

## <a name="net-client"></a><span data-ttu-id="42332-134">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="42332-134">.NET client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="42332-135">Sunucudan istemciye akış</span><span class="sxs-lookup"><span data-stu-id="42332-135">Server-to-client streaming</span></span>


::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="42332-136">`StreamAsync`Ve `StreamAsChannelAsync` üzerindeki yöntemleri, `HubConnection` sunucudan istemciye akış yöntemlerini çağırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="42332-136">The `StreamAsync` and `StreamAsChannelAsync` methods on `HubConnection` are used to invoke server-to-client streaming methods.</span></span> <span data-ttu-id="42332-137">Hub yöntemi veya hub metodunda tanımlanan bağımsız değişkenleri ya da ' a geçirin `StreamAsync` `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="42332-137">Pass the hub method name and arguments defined in the hub method to `StreamAsync` or `StreamAsChannelAsync`.</span></span> <span data-ttu-id="42332-138">Ve üzerindeki genel parametresi `StreamAsync<T>` , `StreamAsChannelAsync<T>` akış yöntemi tarafından döndürülen nesne türünü belirtir.</span><span class="sxs-lookup"><span data-stu-id="42332-138">The generic parameter on `StreamAsync<T>` and `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="42332-139">Veya türündeki bir nesne `IAsyncEnumerable<T>` , `ChannelReader<T>` akış çağrısından döndürülür ve istemcideki akışı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="42332-139">An object of type `IAsyncEnumerable<T>` or `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

<span data-ttu-id="42332-140">Şunu `StreamAsync` döndüren bir örnek `IAsyncEnumerable<int>` :</span><span class="sxs-lookup"><span data-stu-id="42332-140">A `StreamAsync` example that returns `IAsyncEnumerable<int>`:</span></span>

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

<span data-ttu-id="42332-141">Şunu döndüren karşılık gelen bir `StreamAsChannelAsync` örnek `ChannelReader<int>` :</span><span class="sxs-lookup"><span data-stu-id="42332-141">A corresponding `StreamAsChannelAsync` example that returns `ChannelReader<int>`:</span></span>

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

<span data-ttu-id="42332-142">`StreamAsChannelAsync`Üzerinde yöntemi, `HubConnection` sunucudan istemciye akış yöntemini çağırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="42332-142">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="42332-143">Hub yöntemi içinde tanımlanan bağımsız değişkenleri ve hub metodunu geçirin `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="42332-143">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="42332-144">Üzerinde genel parametresi, `StreamAsChannelAsync<T>` akış yöntemi tarafından döndürülen nesne türünü belirtir.</span><span class="sxs-lookup"><span data-stu-id="42332-144">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="42332-145">Bir `ChannelReader<T>` akış çağrısından döndürülür ve istemcideki akışı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="42332-145">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

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

<span data-ttu-id="42332-146">`StreamAsChannelAsync`Üzerinde yöntemi, `HubConnection` sunucudan istemciye akış yöntemini çağırmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="42332-146">The `StreamAsChannelAsync` method on `HubConnection` is used to invoke a server-to-client streaming method.</span></span> <span data-ttu-id="42332-147">Hub yöntemi içinde tanımlanan bağımsız değişkenleri ve hub metodunu geçirin `StreamAsChannelAsync` .</span><span class="sxs-lookup"><span data-stu-id="42332-147">Pass the hub method name and arguments defined in the hub method to `StreamAsChannelAsync`.</span></span> <span data-ttu-id="42332-148">Üzerinde genel parametresi, `StreamAsChannelAsync<T>` akış yöntemi tarafından döndürülen nesne türünü belirtir.</span><span class="sxs-lookup"><span data-stu-id="42332-148">The generic parameter on `StreamAsChannelAsync<T>` specifies the type of objects returned by the streaming method.</span></span> <span data-ttu-id="42332-149">Bir `ChannelReader<T>` akış çağrısından döndürülür ve istemcideki akışı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="42332-149">A `ChannelReader<T>` is returned from the stream invocation and represents the stream on the client.</span></span>

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

### <a name="client-to-server-streaming"></a><span data-ttu-id="42332-150">İstemciden sunucuya akış</span><span class="sxs-lookup"><span data-stu-id="42332-150">Client-to-server streaming</span></span>

<span data-ttu-id="42332-151">.NET istemcisinden istemciden sunucuya akış hub 'ı yöntemini çağırmak için iki yol vardır.</span><span class="sxs-lookup"><span data-stu-id="42332-151">There are two ways to invoke a client-to-server streaming hub method from the .NET client.</span></span> <span data-ttu-id="42332-152">`IAsyncEnumerable<T>` `ChannelReader` `SendAsync` `InvokeAsync` `StreamAsChannelAsync` Çağrılan hub yöntemine bağlı olarak, veya bir bağımsız değişken olarak bir ya da bir bağımsız değişken olarak geçirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="42332-152">You can either pass in an `IAsyncEnumerable<T>` or a `ChannelReader` as an argument to `SendAsync`, `InvokeAsync`, or `StreamAsChannelAsync`, depending on the hub method invoked.</span></span>

<span data-ttu-id="42332-153">Veriler `IAsyncEnumerable` veya nesnesine her yazıldığında `ChannelWriter` , sunucudaki hub yöntemi istemciden alınan verilerle yeni bir öğe alır.</span><span class="sxs-lookup"><span data-stu-id="42332-153">Whenever data is written to the `IAsyncEnumerable` or `ChannelWriter` object, the hub method on the server receives a new item with the data from the client.</span></span>

<span data-ttu-id="42332-154">Bir nesne kullanılıyorsa `IAsyncEnumerable` , akış öğeleri döndüren yöntem çıktıktan sonra akış sonlanır.</span><span class="sxs-lookup"><span data-stu-id="42332-154">If using an `IAsyncEnumerable` object, the stream ends after the method returning stream items exits.</span></span>

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

<span data-ttu-id="42332-155">Ya da kullanıyorsanız `ChannelWriter` , şu ile kanalı tamamlıyoruz `channel.Writer.Complete()` :</span><span class="sxs-lookup"><span data-stu-id="42332-155">Or if you're using a `ChannelWriter`, you complete the channel with `channel.Writer.Complete()`:</span></span>

```csharp
var channel = Channel.CreateBounded<string>(10);
await connection.SendAsync("UploadStream", channel.Reader);
await channel.Writer.WriteAsync("some data");
await channel.Writer.WriteAsync("some more data");
channel.Writer.Complete();
```

::: moniker-end

## <a name="javascript-client"></a><span data-ttu-id="42332-156">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="42332-156">JavaScript client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="42332-157">Sunucudan istemciye akış</span><span class="sxs-lookup"><span data-stu-id="42332-157">Server-to-client streaming</span></span>

<span data-ttu-id="42332-158">JavaScript istemcileri, ile hub 'larda sunucudan istemciye akış yöntemlerini çağırır `connection.stream` .</span><span class="sxs-lookup"><span data-stu-id="42332-158">JavaScript clients call server-to-client streaming methods on hubs with `connection.stream`.</span></span> <span data-ttu-id="42332-159">`stream`Yöntemi iki bağımsız değişkeni kabul eder:</span><span class="sxs-lookup"><span data-stu-id="42332-159">The `stream` method accepts two arguments:</span></span>

* <span data-ttu-id="42332-160">Hub yönteminin adı.</span><span class="sxs-lookup"><span data-stu-id="42332-160">The name of the hub method.</span></span> <span data-ttu-id="42332-161">Aşağıdaki örnekte, hub yöntemi adı `Counter` .</span><span class="sxs-lookup"><span data-stu-id="42332-161">In the following example, the hub method name is `Counter`.</span></span>
* <span data-ttu-id="42332-162">Hub yönteminde tanımlanan bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="42332-162">Arguments defined in the hub method.</span></span> <span data-ttu-id="42332-163">Aşağıdaki örnekte, bağımsız değişkenler alacak akış öğesi sayısı ve akış öğeleri arasındaki gecikme için bir sayıdır.</span><span class="sxs-lookup"><span data-stu-id="42332-163">In the following example, the arguments are a count for the number of stream items to receive and the delay between stream items.</span></span>

<span data-ttu-id="42332-164">`connection.stream``IStreamResult`bir yöntemi içeren bir döndürür `subscribe` .</span><span class="sxs-lookup"><span data-stu-id="42332-164">`connection.stream` returns an `IStreamResult`, which contains a `subscribe` method.</span></span> <span data-ttu-id="42332-165">' A `IStreamSubscriber` geçirin `subscribe` ve `next` `error` `complete` çağrıdan bildirim almak için, ve geri çağırmaları ayarlayın `stream` .</span><span class="sxs-lookup"><span data-stu-id="42332-165">Pass an `IStreamSubscriber` to `subscribe` and set the `next`, `error`, and `complete` callbacks to receive notifications from the `stream` invocation.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-javascript[Streaming javascript](streaming/samples/2.2/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="42332-166">Akışı istemciden sonlandırmak için, `dispose` yönteminden döndürülen üzerinde yöntemi çağırın `ISubscription` `subscribe` .</span><span class="sxs-lookup"><span data-stu-id="42332-166">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span> <span data-ttu-id="42332-167">Bu yöntemi çağırmak `CancellationToken` , bir tane sağladıysanız hub yönteminin parametresinin iptal edilmesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="42332-167">Calling this method causes cancellation of the `CancellationToken` parameter of the Hub method, if you provided one.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-javascript[Streaming javascript](streaming/samples/2.1/wwwroot/js/stream.js?range=19-36)]

<span data-ttu-id="42332-168">Akışı istemciden sonlandırmak için, `dispose` yönteminden döndürülen üzerinde yöntemi çağırın `ISubscription` `subscribe` .</span><span class="sxs-lookup"><span data-stu-id="42332-168">To end the stream from the client, call the `dispose` method on the `ISubscription` that's returned from the `subscribe` method.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="client-to-server-streaming"></a><span data-ttu-id="42332-169">İstemciden sunucuya akış</span><span class="sxs-lookup"><span data-stu-id="42332-169">Client-to-server streaming</span></span>

<span data-ttu-id="42332-170">JavaScript istemcileri `Subject` `send` , `invoke` `stream` çağrılan hub yöntemine bağlı olarak, veya olarak bir bağımsız değişken olarak geçirerek hub 'larda istemciden sunucuya akış yöntemlerini çağırır.</span><span class="sxs-lookup"><span data-stu-id="42332-170">JavaScript clients call client-to-server streaming methods on hubs by passing in a `Subject` as an argument to `send`, `invoke`, or `stream`, depending on the hub method invoked.</span></span> <span data-ttu-id="42332-171">, `Subject` Gibi görünen bir sınıftır `Subject` .</span><span class="sxs-lookup"><span data-stu-id="42332-171">The `Subject` is a class that looks like a `Subject`.</span></span> <span data-ttu-id="42332-172">Örneğin, RxJS ' de, bu kitaplıktaki [Konu](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) sınıfını kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="42332-172">For example in RxJS, you can use the [Subject](https://rxjs-dev.firebaseapp.com/api/index/class/Subject) class from that library.</span></span>

[!code-javascript[Upload javascript](streaming/samples/3.0/wwwroot/js/stream.js?range=41-51)]

<span data-ttu-id="42332-173">`subject.next(item)`Bir öğeyle çağırmak öğeyi akışa yazar ve hub yöntemi sunucudaki öğeyi alır.</span><span class="sxs-lookup"><span data-stu-id="42332-173">Calling `subject.next(item)` with an item writes the item to the stream, and the hub method receives the item on the server.</span></span>

<span data-ttu-id="42332-174">Akışı sonlandırmak için çağırın `subject.complete()` .</span><span class="sxs-lookup"><span data-stu-id="42332-174">To end the stream, call `subject.complete()`.</span></span>

## <a name="java-client"></a><span data-ttu-id="42332-175">Java istemcisi</span><span class="sxs-lookup"><span data-stu-id="42332-175">Java client</span></span>

### <a name="server-to-client-streaming"></a><span data-ttu-id="42332-176">Sunucudan istemciye akış</span><span class="sxs-lookup"><span data-stu-id="42332-176">Server-to-client streaming</span></span>

<span data-ttu-id="42332-177">SignalRJava istemcisi, `stream` akış yöntemlerini çağırmak için yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="42332-177">The SignalR Java client uses the `stream` method to invoke streaming methods.</span></span> <span data-ttu-id="42332-178">`stream`üç veya daha fazla bağımsız değişken kabul eder:</span><span class="sxs-lookup"><span data-stu-id="42332-178">`stream` accepts three or more arguments:</span></span>

* <span data-ttu-id="42332-179">Akış öğelerinin beklenen türü.</span><span class="sxs-lookup"><span data-stu-id="42332-179">The expected type of the stream items.</span></span>
* <span data-ttu-id="42332-180">Hub yönteminin adı.</span><span class="sxs-lookup"><span data-stu-id="42332-180">The name of the hub method.</span></span>
* <span data-ttu-id="42332-181">Hub yönteminde tanımlanan bağımsız değişkenler.</span><span class="sxs-lookup"><span data-stu-id="42332-181">Arguments defined in the hub method.</span></span>

```java
hubConnection.stream(String.class, "ExampleStreamingHubMethod", "Arg1")
    .subscribe(
        (item) -> {/* Define your onNext handler here. */ },
        (error) -> {/* Define your onError handler here. */},
        () -> {/* Define your onCompleted handler here. */});
```

<span data-ttu-id="42332-182">`stream`Üzerindeki yöntemi, `HubConnection` akış öğesi türü Için bir observable döndürür.</span><span class="sxs-lookup"><span data-stu-id="42332-182">The `stream` method on `HubConnection` returns an Observable of the stream item type.</span></span> <span data-ttu-id="42332-183">Observable türünün `subscribe` yöntemi nerede `onNext` , `onError` ve `onCompleted` işleyiciler tanımlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="42332-183">The Observable type's `subscribe` method is where `onNext`, `onError` and `onCompleted` handlers are defined.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="42332-184">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="42332-184">Additional resources</span></span>

* [<span data-ttu-id="42332-185">Hub'lar</span><span class="sxs-lookup"><span data-stu-id="42332-185">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="42332-186">.NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="42332-186">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="42332-187">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="42332-187">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="42332-188">Azure’da Yayımlama</span><span class="sxs-lookup"><span data-stu-id="42332-188">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
