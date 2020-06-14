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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/dotnet-client
ms.openlocfilehash: 7423624bdddfe6cee696cf87c255415170f46455
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/13/2020
ms.locfileid: "84755761"
---
# <a name="aspnet-core-signalr-net-client"></a>ASP.NET Core SignalR .NET istemcisi

ASP.NET Core SignalR .NET istemci kitaplığı, .net uygulamalarından hub 'larla iletişim kurmanızı sağlar SignalR .

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Bu makaledeki kod örneği, ASP.NET Core .net istemcisini kullanan bir WPF uygulamasıdır SignalR .

## <a name="install-the-signalr-net-client-package"></a>SignalR.NET istemci paketini yükler

[Microsoft. AspNetCore. SignalR . ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client).Net istemcilerinin hub 'lara bağlanması için istemci paketi gereklidir SignalR .

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

İstemci kitaplığını yüklemek için, **Paket Yöneticisi konsolu** penceresinde aşağıdaki komutu çalıştırın:

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

İstemci kitaplığını yüklemek için komut kabuğu 'nda aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet add package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="connect-to-a-hub"></a>Bir hub 'a bağlanma

Bir bağlantı kurmak için bir `HubConnectionBuilder` ve çağrısı oluşturun `Build` . Hub URL 'SI, protokol, aktarım türü, günlük düzeyi, üst bilgiler ve diğer seçenekler bir bağlantı oluşturulurken yapılandırılabilir. Herhangi bir yöntemden herhangi birini ekleyerek gerekli seçenekleri yapılandırın `HubConnectionBuilder` `Build` . Bağlantısını ile başlatın `StartAsync` .

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a>Kayıp bağlantıyı işle

::: moniker range=">= aspnetcore-3.0"

### <a name="automatically-reconnect"></a>Otomatik olarak yeniden bağlan

, <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection> Üzerinde yöntemi kullanılarak otomatik olarak yeniden bağlanacak şekilde yapılandırılabilir `WithAutomaticReconnect` <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> . Varsayılan olarak otomatik olarak yeniden bağlanmaz.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

Herhangi bir parametre olmadan, `WithAutomaticReconnect()` her yeniden bağlanma denemesini denemeden önce, dört başarısız denemeden sonra durdurulan istemciyi 0, 2, 10 ve 30 saniye bekleyecek şekilde yapılandırır.

Yeniden bağlanma girişimlerini başlatmadan önce, `HubConnection` `HubConnectionState.Reconnecting` durumuna geçer ve olayı harekete geçirebilir `Reconnecting` .  Bu, kullanıcıların bağlantının kaybedildiği ve Kullanıcı arabirimi öğelerini devre dışı bırakan kullanıcıları uyarma fırsatı sağlar. Etkileşimli olmayan uygulamalar, iletileri sıraya alabilir veya bırakarak başlatabilir.

```csharp
connection.Reconnecting += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Reconnecting);

    // Notify users the connection was lost and the client is reconnecting.
    // Start queuing or dropping messages.

    return Task.CompletedTask;
};
```

İstemci ilk dört deneme süresi içinde başarıyla yeniden bağlanırsa, `HubConnection` `Connected` duruma geçer ve olayı harekete geçirebilir `Reconnected` . Bu, kullanıcılara bağlantı yeniden kurulduğunda ve sıraya alınan tüm iletileri sıradan bildiren bir fırsat sağlar.

Bağlantı sunucuya tamamen yeni göründüğünden olay işleyicilerine yeni bir verilecek `ConnectionId` `Reconnected` .

> [!WARNING]
> `Reconnected`Olay işleyicisinin `connectionId` parametresi, `HubConnection` [anlaşmayı atlayacak](xref:signalr/configuration#configure-client-options)şekilde yapılandırıldıysa null olur.

```csharp
connection.Reconnected += connectionId =>
{
    Debug.Assert(connection.State == HubConnectionState.Connected);

    // Notify users the connection was reestablished.
    // Start dequeuing messages queued while reconnecting if any.

    return Task.CompletedTask;
};
```

`WithAutomaticReconnect()`, `HubConnection` ilk başlatma başarısızlıklarını yeniden denemek üzere yapılandırmaz, bu nedenle başlatma hatalarının el ile işlenmesi gerekir:

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

İstemci ilk dört denemeden sonra başarıyla yeniden bağlanmazsa, `HubConnection` `Disconnected` durumuna geçer ve olayı harekete geçirebilir <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> . Bu, bağlantıyı el ile yeniden başlatmayı denemek veya bağlantıyı kalıcı olarak kaybettiğini bildirmek için bir fırsat sağlar.

```csharp
connection.Closed += error =>
{
    Debug.Assert(connection.State == HubConnectionState.Disconnected);

    // Notify users the connection has been closed or manually try to restart the connection.

    return Task.CompletedTask;
};
```

Bağlantıyı kesmeden veya yeniden bağlanma zamanlamasını değiştirmeden önce özel sayıda yeniden bağlantı girişimi yapılandırmak için, her bir `WithAutomaticReconnect` yeniden bağlanma denemesine başlamadan önce beklenecek gecikme süresi temsil eden bir sayı dizisi kabul eder.

```csharp
HubConnection connection= new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.Zero, TimeSpan.FromSeconds(10) })
    .Build();

    // .WithAutomaticReconnect(new[] { TimeSpan.Zero, TimeSpan.FromSeconds(2), TimeSpan.FromSeconds(10), TimeSpan.FromSeconds(30) }) yields the default behavior.
```

Yukarıdaki örnek, `HubConnection` bağlantı kaybolduktan hemen sonra yeniden bağlanmaya başlamak için öğesini yapılandırır. Bu, varsayılan yapılandırma için de geçerlidir.

İlk yeniden bağlantı girişimi başarısız olursa, ikinci yeniden bağlanma denemesi de varsayılan yapılandırmada olduğu gibi 2 saniye beklemek yerine hemen başlatılır.

İkinci yeniden bağlantı girişimi başarısız olursa, üçüncü yeniden bağlanma denemesi varsayılan yapılandırma gibi 10 saniye içinde başlar.

Özel davranış daha sonra, üçüncü yeniden bağlantı girişimi başarısızlığından sonra durarak varsayılan davranıştan daha sonra yeniden ayrılmış. Varsayılan yapılandırmada, 30 saniye içinde bir veya daha fazla yeniden bağlantı denemesi olur.

Otomatik yeniden bağlanma girişimlerinin zamanlaması ve sayısı üzerinde daha fazla denetime sahip olmak isterseniz, `WithAutomaticReconnect` `IRetryPolicy` adlı tek bir yöntemine sahip olan arabirimini uygulayan nesneyi kabul eder `NextRetryDelay` .

`NextRetryDelay`türünde tek bir bağımsız değişken alır `RetryContext` . ,, `RetryContext` Ve sırasıyla bir olan üç özelliğe sahiptir: `PreviousRetryCount` `ElapsedTime` ve `RetryReason` `long` `TimeSpan` `Exception` . İlk yeniden bağlanma denemesinden önce, `PreviousRetryCount` ve `ElapsedTime` sıfır olur ve `RetryReason` bağlantının kaybolmasına neden olan özel durum olacaktır. Her başarısız yeniden deneme denemesinden sonra, `PreviousRetryCount` Bu, `ElapsedTime` şimdiye kadar bir süre sonra yeniden bağlanılan süreyi yansıtacak şekilde güncelleştirilir ve `RetryReason` son yeniden bağlanma denemesinin başarısız olmasına neden olan özel durum olacaktır.

`NextRetryDelay`bir sonraki yeniden bağlanma girişiminden önce beklenecek süreyi temsil eden bir TimeSpan değeri veya bunun yeniden `null` `HubConnection` bağlanması durdurulmalıdır.

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

Alternatif olarak, [el ile yeniden bağlanma](#manually-reconnect)bölümünde gösterildiği gibi istemcinizi el ile yeniden bağlayacaksınız.

::: moniker-end

### <a name="manually-reconnect"></a>El ile yeniden bağlan

::: moniker range="< aspnetcore-3.0"

> [!WARNING]
> 3,0 ' den önce, için .NET istemcisi SignalR otomatik olarak yeniden bağlanmaz. İstemcinizi el ile yeniden bağlayacaksınız kodu yazmanız gerekir.

::: moniker-end

<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed>Kayıp bir bağlantıya yanıt vermek için olayını kullanın. Örneğin, yeniden bağlanmayı otomatik hale getirmek isteyebilirsiniz.

`Closed`Olay, `Task` zaman uyumsuz kodun kullanılmadan çalışmasına izin veren, döndüren bir temsilci gerektirir `async void` . Zaman uyumlu olarak çalışan bir olay işleyicisinde temsilci imzasını karşılamak için `Closed` şunu döndürün `Task.CompletedTask` :

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

Zaman uyumsuz desteğin ana nedeni, bağlantıyı yeniden başlatabilmeniz için kullanılır. Bir bağlantının başlatılması zaman uyumsuz bir işlemdir.

`Closed`Bağlantıyı yeniden başlatan bir İşleyicide, aşağıdaki örnekte gösterildiği gibi, sunucunun aşırı yüklenmesini engellemek için bazı rastgele gecikme yapmayı düşünün:

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a>İstemciden çağrı merkezi yöntemleri

`InvokeAsync`Hub 'daki yöntemleri çağırır. Hub yöntemi adını ve hub metodunda tanımlanan tüm bağımsız değişkenleri öğesine geçirin `InvokeAsync` . SignalRzaman uyumsuzdur, bu nedenle `async` `await` çağrıları yaparken ve kullanın.

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

`InvokeAsync`Yöntemi, `Task` sunucu yöntemi döndürüldüğünde tamamlanmış bir döndürür. Varsa dönüş değeri, sonucu olarak sağlanır `Task` . Sunucu üzerindeki yöntemi tarafından oluşturulan özel durumlar hatalı bir şekilde üretir `Task` . `await`Sunucu yönteminin tamamlanmasını beklemek için sözdizimi kullanın ve `try...catch` hataları işlemek için söz dizimini kullanın.

`SendAsync`Yöntemi, `Task` ileti sunucuya gönderildiğinde tamamlanmış bir döndürür. Bu `Task` , sunucu yöntemi tamamlanana kadar beklemediğinden hiçbir dönüş değeri sağlanmaz. İletiyi gönderirken istemcide oluşturulan özel durumlar hatalı bir şekilde oluşur `Task` . `await` `try...catch` Gönderme hatalarını işlemek için ve sözdizimini kullanın.

> [!NOTE]
> Azure SignalR hizmeti 'Ni *sunucusuz modda*kullanıyorsanız, bir istemciden hub yöntemleri çağrılamaz. Daha fazla bilgi için bkz. [ SignalR hizmet belgeleri](/azure/azure-signalr/signalr-concept-serverless-development-config).

## <a name="call-client-methods-from-hub"></a>Hub 'dan istemci yöntemlerini çağır

Hub `connection.On` 'ı derlemeden sonra, ancak bağlantıyı başlatmadan önce kullanarak çağıran yöntemleri tanımlayın.

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

Yukarıdaki kod, `connection.On` sunucu tarafı kodu yöntemini kullanarak çağırdığında çalıştırılır `SendAsync` .

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a>Hata işleme ve günlüğe kaydetme

Try-catch ifadesiyle hataları işleyin. `Exception`Bir hata oluştuktan sonra gerçekleştirilecek uygun eylemi öğrenmek için nesneyi inceleyin.

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a>Ek kaynaklar

* [Merkezler](xref:signalr/hubs)
* [JavaScript istemcisi](xref:signalr/javascript-client)
* [Azure’da Yayımlama](xref:signalr/publish-to-azure-web-app)
* [Azure SignalR hizmeti sunucusuz belgeler](/azure/azure-signalr/signalr-concept-serverless-development-config)
