---
title: ASP.NET Core SignalR .NET istemcisi
author: bradygaster
description: ASP.NET Core SignalR .net istemcisiyle ilgili bilgiler
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/14/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/dotnet-client
ms.openlocfilehash: 54e86479b9f9f0acc861769f9ab78958f79acfd3
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85400147"
---
# <a name="aspnet-core-signalr-net-client"></a><span data-ttu-id="ba9f9-103">ASP.NET Core SignalR .NET istemcisi</span><span class="sxs-lookup"><span data-stu-id="ba9f9-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="ba9f9-104">ASP.NET Core SignalR .NET istemci kitaplığı, .net uygulamalarından hub 'larla iletişim kurmanızı sağlar SignalR .</span><span class="sxs-lookup"><span data-stu-id="ba9f9-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

<span data-ttu-id="ba9f9-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ba9f9-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ba9f9-106">Bu makaledeki kod örneği, ASP.NET Core .net istemcisini kullanan bir WPF uygulamasıdır SignalR .</span><span class="sxs-lookup"><span data-stu-id="ba9f9-106">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-signalr-net-client-package"></a><span data-ttu-id="ba9f9-107">SignalR.NET istemci paketini yükler</span><span class="sxs-lookup"><span data-stu-id="ba9f9-107">Install the SignalR .NET client package</span></span>

<span data-ttu-id="ba9f9-108">[Microsoft. AspNetCore. SignalR . ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client).Net istemcilerinin hub 'lara bağlanması için istemci paketi gereklidir SignalR .</span><span class="sxs-lookup"><span data-stu-id="ba9f9-108">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package is required for .NET clients to connect to SignalR hubs.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ba9f9-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ba9f9-109">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ba9f9-110">İstemci kitaplığını yüklemek için, **Paket Yöneticisi konsolu** penceresinde aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="ba9f9-110">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-cli"></a>[<span data-ttu-id="ba9f9-111">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ba9f9-111">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ba9f9-112">İstemci kitaplığını yüklemek için komut kabuğu 'nda aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="ba9f9-112">To install the client library, run the following command in a command shell:</span></span>

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a><span data-ttu-id="ba9f9-113">Bir hub 'a bağlanma</span><span class="sxs-lookup"><span data-stu-id="ba9f9-113">Connect to a hub</span></span>

<span data-ttu-id="ba9f9-114">Bir bağlantı kurmak için bir `HubConnectionBuilder` ve çağrısı oluşturun `Build` .</span><span class="sxs-lookup"><span data-stu-id="ba9f9-114">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="ba9f9-115">Hub URL 'SI, protokol, aktarım türü, günlük düzeyi, üst bilgiler ve diğer seçenekler bir bağlantı oluşturulurken yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-115">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="ba9f9-116">Herhangi bir yöntemden herhangi birini ekleyerek gerekli seçenekleri yapılandırın `HubConnectionBuilder` `Build` .</span><span class="sxs-lookup"><span data-stu-id="ba9f9-116">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="ba9f9-117">Bağlantısını ile başlatın `StartAsync` .</span><span class="sxs-lookup"><span data-stu-id="ba9f9-117">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="ba9f9-118">Kayıp bağlantıyı işle</span><span class="sxs-lookup"><span data-stu-id="ba9f9-118">Handle lost connection</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a><span data-ttu-id="ba9f9-119">Otomatik olarak yeniden bağlan</span><span class="sxs-lookup"><span data-stu-id="ba9f9-119">Automatically reconnect</span></span>

<span data-ttu-id="ba9f9-120">, <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> Üzerinde yöntemi kullanılarak otomatik olarak yeniden bağlanacak şekilde yapılandırılabilir `WithAutomaticReconnect` <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> .</span><span class="sxs-lookup"><span data-stu-id="ba9f9-120">The <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> can be configured to automatically reconnect using the `WithAutomaticReconnect` method on the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="ba9f9-121">Varsayılan olarak otomatik olarak yeniden bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-121">It won't automatically reconnect by default.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

<span data-ttu-id="ba9f9-122">Herhangi bir parametre olmadan, `WithAutomaticReconnect()` her yeniden bağlanma denemesini denemeden önce, dört başarısız denemeden sonra durdurulan istemciyi 0, 2, 10 ve 30 saniye bekleyecek şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-122">Without any parameters, `WithAutomaticReconnect()` configures the client to wait 0, 2, 10, and 30 seconds respectively before trying each reconnect attempt, stopping after four failed attempts.</span></span>

<span data-ttu-id="ba9f9-123">Yeniden bağlanma girişimlerini başlatmadan önce, `HubConnection` `HubConnectionState.Reconnecting` durumuna geçer ve olayı harekete geçirebilir `Reconnecting` .</span><span class="sxs-lookup"><span data-stu-id="ba9f9-123">Before starting any reconnect attempts, the `HubConnection` will transition to the `HubConnectionState.Reconnecting` state and fire the `Reconnecting` event.</span></span>  <span data-ttu-id="ba9f9-124">Bu, kullanıcıların bağlantının kaybedildiği ve Kullanıcı arabirimi öğelerini devre dışı bırakan kullanıcıları uyarma fırsatı sağlar.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-124">This provides an opportunity to warn users that the connection has been lost and to disable UI elements.</span></span> <span data-ttu-id="ba9f9-125">Etkileşimli olmayan uygulamalar, iletileri sıraya alabilir veya bırakarak başlatabilir.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-125">Non-interactive apps can start queuing or dropping messages.</span></span>

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

<span data-ttu-id="ba9f9-126">İstemci ilk dört deneme süresi içinde başarıyla yeniden bağlanırsa, `HubConnection` `Connected` duruma geçer ve olayı harekete geçirebilir `Reconnected` .</span><span class="sxs-lookup"><span data-stu-id="ba9f9-126">If the client successfully reconnects within its first four attempts, the `HubConnection` will transition back to the `Connected` state and fire the `Reconnected` event.</span></span> <span data-ttu-id="ba9f9-127">Bu, kullanıcılara bağlantı yeniden kurulduğunda ve sıraya alınan tüm iletileri sıradan bildiren bir fırsat sağlar.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-127">This provides an opportunity to inform users the connection has been reestablished and dequeue any queued messages.</span></span>

<span data-ttu-id="ba9f9-128">Bağlantı sunucuya tamamen yeni göründüğünden olay işleyicilerine yeni bir verilecek `ConnectionId` `Reconnected` .</span><span class="sxs-lookup"><span data-stu-id="ba9f9-128">Since the connection looks entirely new to the server, a new `ConnectionId` will be provided to the `Reconnected` event handlers.</span></span>

> [!WARNING]
> <span data-ttu-id="ba9f9-129">`Reconnected`Olay işleyicisinin `connectionId` parametresi, `HubConnection` [anlaşmayı atlayacak](xref:signalr/configuration#configure-client-options)şekilde yapılandırıldıysa null olur.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-129">The `Reconnected` event handler's `connectionId` parameter will be null if the `HubConnection` was configured to [skip negotiation](xref:signalr/configuration#configure-client-options).</span></span>

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

<span data-ttu-id="ba9f9-130">`WithAutomaticReconnect()`, `HubConnection` ilk başlatma başarısızlıklarını yeniden denemek üzere yapılandırmaz, bu nedenle başlatma hatalarının el ile işlenmesi gerekir:</span><span class="sxs-lookup"><span data-stu-id="ba9f9-130">`WithAutomaticReconnect()` won't configure the `HubConnection` to retry initial start failures, so start failures need to be handled manually:</span></span>

```csharp
public static async Task<bool> ConnectWithRetryAsync(HubConnection connection, CancellationToken token)
{
    // Keep trying to until we can start or the token is canceled.
    while (true)
    {
        try
        {
            await connection.StartAsync(token);
            Debug.Assert(connection.State == HubConnectionState.Connected);
            return true;
        }
        catch when (token.IsCancellationRequested)
        {
            return false;
        }
        catch
        {
            // Failed to connect, trying again in 5000 ms.
            Debug.Assert(connection.State == HubConnectionState.Disconnected);
            await Task.Delay(5000);
        }
    }
}
```

<span data-ttu-id="ba9f9-131">İstemci ilk dört denemeden sonra başarıyla yeniden bağlanmazsa, `HubConnection` `Disconnected` durumuna geçer ve olayı harekete geçirebilir <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> .</span><span class="sxs-lookup"><span data-stu-id="ba9f9-131">If the client doesn't successfully reconnect within its first four attempts, the `HubConnection` will transition to the `Disconnected` state and fire the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event.</span></span> <span data-ttu-id="ba9f9-132">Bu, bağlantıyı el ile yeniden başlatmayı denemek veya bağlantıyı kalıcı olarak kaybettiğini bildirmek için bir fırsat sağlar.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-132">This provides an opportunity to attempt to restart the connection manually or inform users the connection has been permanently lost.</span></span>

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

<span data-ttu-id="ba9f9-133">Bağlantıyı kesmeden veya yeniden bağlanma zamanlamasını değiştirmeden önce özel sayıda yeniden bağlantı girişimi yapılandırmak için, her bir `WithAutomaticReconnect` yeniden bağlanma denemesine başlamadan önce beklenecek gecikme süresi temsil eden bir sayı dizisi kabul eder.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-133">In order to configure a custom number of reconnect attempts before disconnecting or change the reconnect timing, `WithAutomaticReconnect` accepts an array of numbers representing the delay in milliseconds to wait before starting each reconnect attempt.</span></span>

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

<span data-ttu-id="ba9f9-134">Yukarıdaki örnek, `HubConnection` bağlantı kaybolduktan hemen sonra yeniden bağlanmaya başlamak için öğesini yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-134">The preceding example configures the `HubConnection` to start attempting reconnects immediately after the connection is lost.</span></span> <span data-ttu-id="ba9f9-135">Bu, varsayılan yapılandırma için de geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-135">This is also true for the default configuration.</span></span>

<span data-ttu-id="ba9f9-136">İlk yeniden bağlantı girişimi başarısız olursa, ikinci yeniden bağlanma denemesi de varsayılan yapılandırmada olduğu gibi 2 saniye beklemek yerine hemen başlatılır.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-136">If the first reconnect attempt fails, the second reconnect attempt will also start immediately instead of waiting 2 seconds like it would in the default configuration.</span></span>

<span data-ttu-id="ba9f9-137">İkinci yeniden bağlantı girişimi başarısız olursa, üçüncü yeniden bağlanma denemesi varsayılan yapılandırma gibi 10 saniye içinde başlar.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-137">If the second reconnect attempt fails, the third reconnect attempt will start in 10 seconds which is again like the default configuration.</span></span>

<span data-ttu-id="ba9f9-138">Özel davranış daha sonra, üçüncü yeniden bağlantı girişimi başarısızlığından sonra durarak varsayılan davranıştan daha sonra yeniden ayrılmış.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-138">The custom behavior then diverges again from the default behavior by stopping after the third reconnect attempt failure.</span></span> <span data-ttu-id="ba9f9-139">Varsayılan yapılandırmada, 30 saniye içinde bir veya daha fazla yeniden bağlantı denemesi olur.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-139">In the default configuration there would be one more reconnect attempt in another 30 seconds.</span></span>

<span data-ttu-id="ba9f9-140">Otomatik yeniden bağlanma girişimlerinin zamanlaması ve sayısı üzerinde daha fazla denetime sahip olmak isterseniz, `WithAutomaticReconnect` `IRetryPolicy` adlı tek bir yöntemine sahip olan arabirimini uygulayan nesneyi kabul eder `NextRetryDelay` .</span><span class="sxs-lookup"><span data-stu-id="ba9f9-140">If you want even more control over the timing and number of automatic reconnect attempts, `WithAutomaticReconnect` accepts an object implementing the `IRetryPolicy` interface, which has a single method named `NextRetryDelay`.</span></span>

<span data-ttu-id="ba9f9-141">`NextRetryDelay`türünde tek bir bağımsız değişken alır `RetryContext` .</span><span class="sxs-lookup"><span data-stu-id="ba9f9-141">`NextRetryDelay` takes a single argument with the type `RetryContext`.</span></span> <span data-ttu-id="ba9f9-142">,, `RetryContext` Ve sırasıyla bir olan üç özelliğe sahiptir: `PreviousRetryCount` `ElapsedTime` ve `RetryReason` `long` `TimeSpan` `Exception` .</span><span class="sxs-lookup"><span data-stu-id="ba9f9-142">The `RetryContext` has three properties: `PreviousRetryCount`, `ElapsedTime` and `RetryReason`, which are a `long`, a `TimeSpan` and an `Exception` respectively.</span></span> <span data-ttu-id="ba9f9-143">İlk yeniden bağlanma denemesinden önce, `PreviousRetryCount` ve `ElapsedTime` sıfır olur ve `RetryReason` bağlantının kaybolmasına neden olan özel durum olacaktır.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-143">Before the first reconnect attempt, both `PreviousRetryCount` and `ElapsedTime` will be zero, and the `RetryReason` will be the Exception that caused the connection to be lost.</span></span> <span data-ttu-id="ba9f9-144">Her başarısız yeniden deneme denemesinden sonra, `PreviousRetryCount` Bu, `ElapsedTime` şimdiye kadar bir süre sonra yeniden bağlanılan süreyi yansıtacak şekilde güncelleştirilir ve `RetryReason` son yeniden bağlanma denemesinin başarısız olmasına neden olan özel durum olacaktır.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-144">After each failed retry attempt, `PreviousRetryCount` will be incremented by one, `ElapsedTime` will be updated to reflect the amount of time spent reconnecting so far, and the `RetryReason` will be the Exception that caused the last reconnect attempt to fail.</span></span>

<span data-ttu-id="ba9f9-145">`NextRetryDelay`bir sonraki yeniden bağlanma girişiminden önce beklenecek süreyi temsil eden bir TimeSpan değeri veya bunun yeniden `null` `HubConnection` bağlanması durdurulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-145">`NextRetryDelay` must return either a TimeSpan representing the time to wait before the next reconnect attempt or `null` if the `HubConnection` should stop reconnecting.</span></span>

```csharp
public class RandomRetryPolicy : IRetryPolicy
{
    private readonly Random _random = new Random();

    public TimeSpan? NextRetryDelay(RetryContext retryContext)
    {
        // If we've been reconnecting for less than 60 seconds so far,
        // wait between 0 and 10 seconds before the next reconnect attempt.
        if (retryContext.ElapsedTime < TimeSpan.FromSeconds(60))
        {
            return TimeSpan.FromSeconds(_random.NextDouble() * 10);
        }
        else
        {
            // If we've been reconnecting for more than 60 seconds so far, stop reconnecting.
            return null;
        }
    }
}
```

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new RandomRetryPolicy())
    .Build();
```

<span data-ttu-id="ba9f9-146">Alternatif olarak, [el ile yeniden bağlanma](#manually-reconnect)bölümünde gösterildiği gibi istemcinizi el ile yeniden bağlayacaksınız.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-146">Alternatively, you can write code that will reconnect your client manually as demonstrated in [Manually reconnect](#manually-reconnect).</span></span>

::: moniker-end

### <a name="manually-reconnect"></a><span data-ttu-id="ba9f9-147">El ile yeniden bağlan</span><span class="sxs-lookup"><span data-stu-id="ba9f9-147">Manually reconnect</span></span>

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="ba9f9-148">3,0 ' den önce, için .NET istemcisi SignalR otomatik olarak yeniden bağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-148">Prior to 3.0, the .NET client for SignalR doesn't automatically reconnect.</span></span> <span data-ttu-id="ba9f9-149">İstemcinizi el ile yeniden bağlayacaksınız kodu yazmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-149">You must write code that will reconnect your client manually.</span></span>

::: moniker-end

<span data-ttu-id="ba9f9-150"><xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed>Kayıp bir bağlantıya yanıt vermek için olayını kullanın.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-150">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="ba9f9-151">Örneğin, yeniden bağlanmayı otomatik hale getirmek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-151">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="ba9f9-152">`Closed`Olay, `Task` zaman uyumsuz kodun kullanılmadan çalışmasına izin veren, döndüren bir temsilci gerektirir `async void` .</span><span class="sxs-lookup"><span data-stu-id="ba9f9-152">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="ba9f9-153">Zaman uyumlu olarak çalışan bir olay işleyicisinde temsilci imzasını karşılamak için `Closed` şunu döndürün `Task.CompletedTask` :</span><span class="sxs-lookup"><span data-stu-id="ba9f9-153">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="ba9f9-154">Zaman uyumsuz desteğin ana nedeni, bağlantıyı yeniden başlatabilmeniz için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-154">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="ba9f9-155">Bir bağlantının başlatılması zaman uyumsuz bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-155">Starting a connection is an async action.</span></span>

<span data-ttu-id="ba9f9-156">`Closed`Bağlantıyı yeniden başlatan bir İşleyicide, aşağıdaki örnekte gösterildiği gibi, sunucunun aşırı yüklenmesini engellemek için bazı rastgele gecikme yapmayı düşünün:</span><span class="sxs-lookup"><span data-stu-id="ba9f9-156">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="ba9f9-157">İstemciden çağrı merkezi yöntemleri</span><span class="sxs-lookup"><span data-stu-id="ba9f9-157">Call hub methods from client</span></span>

<span data-ttu-id="ba9f9-158">`InvokeAsync`Hub 'daki yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-158">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="ba9f9-159">Hub yöntemi adını ve hub metodunda tanımlanan tüm bağımsız değişkenleri öğesine geçirin `InvokeAsync` .</span><span class="sxs-lookup"><span data-stu-id="ba9f9-159">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> SignalR<span data-ttu-id="ba9f9-160">zaman uyumsuzdur, bu nedenle `async` `await` çağrıları yaparken ve kullanın.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-160"> is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="ba9f9-161">`InvokeAsync`Yöntemi, `Task` sunucu yöntemi döndürüldüğünde tamamlanmış bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-161">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="ba9f9-162">Varsa dönüş değeri, sonucu olarak sağlanır `Task` .</span><span class="sxs-lookup"><span data-stu-id="ba9f9-162">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="ba9f9-163">Sunucu üzerindeki yöntemi tarafından oluşturulan özel durumlar hatalı bir şekilde üretir `Task` .</span><span class="sxs-lookup"><span data-stu-id="ba9f9-163">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="ba9f9-164">`await`Sunucu yönteminin tamamlanmasını beklemek için sözdizimi kullanın ve `try...catch` hataları işlemek için söz dizimini kullanın.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-164">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="ba9f9-165">`SendAsync`Yöntemi, `Task` ileti sunucuya gönderildiğinde tamamlanmış bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-165">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="ba9f9-166">Bu `Task` , sunucu yöntemi tamamlanana kadar beklemediğinden hiçbir dönüş değeri sağlanmaz.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-166">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="ba9f9-167">İletiyi gönderirken istemcide oluşturulan özel durumlar hatalı bir şekilde oluşur `Task` .</span><span class="sxs-lookup"><span data-stu-id="ba9f9-167">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="ba9f9-168">`await` `try...catch` Gönderme hatalarını işlemek için ve sözdizimini kullanın.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-168">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="ba9f9-169">Azure SignalR hizmeti 'Ni *sunucusuz modda*kullanıyorsanız, bir istemciden hub yöntemleri çağrılamaz.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-169">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="ba9f9-170">Daha fazla bilgi için bkz. [ SignalR hizmet belgeleri](/azure/azure-signalr/signalr-concept-serverless-development-config).</span><span class="sxs-lookup"><span data-stu-id="ba9f9-170">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="ba9f9-171">Hub 'dan istemci yöntemlerini çağır</span><span class="sxs-lookup"><span data-stu-id="ba9f9-171">Call client methods from hub</span></span>

<span data-ttu-id="ba9f9-172">Hub `connection.On` 'ı derlemeden sonra, ancak bağlantıyı başlatmadan önce kullanarak çağıran yöntemleri tanımlayın.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-172">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="ba9f9-173">Yukarıdaki kod, `connection.On` sunucu tarafı kodu yöntemini kullanarak çağırdığında çalıştırılır `SendAsync` .</span><span class="sxs-lookup"><span data-stu-id="ba9f9-173">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="ba9f9-174">Hata işleme ve günlüğe kaydetme</span><span class="sxs-lookup"><span data-stu-id="ba9f9-174">Error handling and logging</span></span>

<span data-ttu-id="ba9f9-175">Try-catch ifadesiyle hataları işleyin.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-175">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="ba9f9-176">`Exception`Bir hata oluştuktan sonra gerçekleştirilecek uygun eylemi öğrenmek için nesneyi inceleyin.</span><span class="sxs-lookup"><span data-stu-id="ba9f9-176">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="ba9f9-177">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ba9f9-177">Additional resources</span></span>

* [<span data-ttu-id="ba9f9-178">Merkezler</span><span class="sxs-lookup"><span data-stu-id="ba9f9-178">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="ba9f9-179">JavaScript istemcisi</span><span class="sxs-lookup"><span data-stu-id="ba9f9-179">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="ba9f9-180">Azure’da Yayımlama</span><span class="sxs-lookup"><span data-stu-id="ba9f9-180">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* <span data-ttu-id="ba9f9-181">[Azure SignalR hizmeti sunucusuz belgeler](/azure/azure-signalr/signalr-concept-serverless-development-config)</span><span class="sxs-lookup"><span data-stu-id="ba9f9-181">[Azure SignalR Service serverless documentation](/azure/azure-signalr/signalr-concept-serverless-development-config)</span></span>
