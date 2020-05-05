---
title: Ve ASP.NET Core SignalR arasındaki farklarSignalR
author: bradygaster
description: Ve ASP.NET Core SignalR arasındaki farklarSignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: 58d134ae971bace178561322f1c8a6351432be03
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82772585"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a>ASP.NET SignalR ile ASP.NET Core SignalR arasındaki farklar

ASP.NET Core SignalR, ASP.NET SignalR için istemcilerle veya sunucularla uyumlu değildir. Bu makalede, ASP.NET Core SignalR 'de kaldırılan veya değiştirilen özellikler ayrıntılı olarak anlatılmaktadır.

## <a name="how-to-identify-the-signalr-version"></a>SignalR sürümünü belirleme

::: moniker range=">= aspnetcore-3.0"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| Sunucu NuGet paketi | [Microsoft. AspNet. SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | Yok. [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) Shared Framework 'e dahildir. |
| İstemci NuGet paketleri | [Microsoft. AspNet. SignalR. Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. AspNet. SignalR. JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft. AspNetCore. SignalR. Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| JavaScript istemcisi NPM paketi | [SignalR](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| Java istemcisi | [GitHub deposu](https://github.com/SignalR/java-client) (kullanım dışı)  | Maven paketi [com. Microsoft. SignalR](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| Sunucu uygulaması türü | ASP.NET (System. Web) veya OWıN Self-Host | ASP.NET Çekirdeği |
| Desteklenen sunucu platformları | .NET Framework 4,5 veya üzeri | .NET Core 3,0 veya üzeri |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | ASP.NET SignalR | ASP.NET Core SignalR |
| -------------------- | --------------- | -------------------- |
| Sunucu NuGet paketi | [Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | [Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)<br>[Microsoft. AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework) |
| İstemci NuGet paketleri | [Microsoft. AspNet. SignalR. İstemcilerinin](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[Microsoft. AspNet. SignalR. JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [Microsoft. AspNetCore. SignalR. İstemcilerinin](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| JavaScript istemcisi NPM paketi | [SignalR](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| Java istemcisi | [GitHub deposu](https://github.com/SignalR/java-client) (kullanım dışı)  | Maven paketi [com. Microsoft. SignalR](https://search.maven.org/artifact/com.microsoft.signalr/signalr) |
| Sunucu uygulaması türü | ASP.NET (System. Web) veya OWıN Self-Host | ASP.NET Çekirdeği |
| Desteklenen sunucu platformları | .NET Framework 4,5 veya üzeri | .NET Framework 4.6.1 veya üzeri<br>.NET Core 2,1 veya üzeri |

::: moniker-end

## <a name="feature-differences"></a>Özellik farklılıkları

### <a name="automatic-reconnects"></a>Otomatik yeniden bağlantılar

::: moniker range=">= aspnetcore-3.0"

ASP.NET SignalRiçinde:

* Varsayılan olarak, SignalR bağlantı kesildiğinde sunucuya yeniden bağlanmaya çalışır. 

ASP.NET Core SignalR:

* Otomatik yeniden bağlantılar hem [.net istemcisiyle](xref:signalr/dotnet-client#automatically-reconnect) hem de [JavaScript istemcisiyle](xref:signalr/javascript-client#automatically-reconnect)birlikte kabul edilir:

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chatHub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 3,0 ' SignalR dan önce otomatik yeniden bağlantılar desteklenmez. İstemcinin bağlantısı kesilirse, kullanıcının yeniden bağlanmak için açık olarak yeni bir bağlantı başlatması gerekir. ASP.net SignalR' de SignalR , bağlantı kesildiğinde sunucuya yeniden bağlanmaya çalışır.

::: moniker-end

### <a name="protocol-support"></a>Protokol desteği

ASP.NET Core SignalR , JSON 'ı ve [MessagePack](xref:signalr/messagepackhubprotocol)tabanlı yeni bir ikili protokolü destekler. Ek olarak özel protokoller de oluşturulabilir.

### <a name="transports"></a>Taşımalar

ASP.NET Core SignalRIçinde süresiz çerçeve taşıması desteklenmez.

## <a name="differences-on-the-server"></a>Sunucu farkları

ASP.NET Core SignalR sunucu tarafı kitaplıkları, hem hem de Razor MVC projeleri için **ASP.NET Core Web uygulaması** şablonunda kullanılan [Microsoft. aspnetcore. app](xref:fundamentals/metapackage-app)'e dahildir.

ASP.NET Core SignalR bir ASP.NET Core ara yazılımı. ' De <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> `Startup.ConfigureServices`çağırarak yapılandırılması gerekir.

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

Yönlendirmeyi yapılandırmak için, yolları <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> `Startup.Configure` yöntemindeki Yöntem çağrısı içindeki hublara eşleyin.

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Yönlendirmeyi yapılandırmak için, yolları <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> `Startup.Configure` yöntemindeki Yöntem çağrısı içindeki hublara eşleyin.

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a>Yapışkan oturumlar

ASP.NET SignalR için genişleme modeli, istemcilerin gruptaki herhangi bir sunucuya yeniden bağlanmasına ve ileti gönderemesine izin verir. ASP.NET Core SignalR, istemcinin bağlantı süresince aynı sunucu ile etkileşimde olması gerekir. Redsıs kullanarak genişleme için, bu, yapışkan oturumların gerekli olduğu anlamına gelir. [Azure SignalR hizmeti](/azure/azure-signalr/)'ni kullanarak genişleme için, hizmet istemcilerle bağlantıları işlediği için yapışkan oturumlar gerekli değildir.

### <a name="single-hub-per-connection"></a>Bağlantı başına tek hub

ASP.NET Core SignalR, bağlantı modeli basitleştirilmiştir. Bağlantılar, birden çok hub 'a erişimi paylaşmak için kullanılan tek bir bağlantı yerine doğrudan tek bir hub 'a yapılır.

### <a name="streaming"></a>Akış

ASP.NET Core SignalR artık hub 'dan istemciye [veri akışını](xref:signalr/streaming) desteklemektedir.

### <a name="state"></a>Durum

İstemciler ve merkez arasında rastgele durum geçirme özelliği (genellikle denir `HubState`) kaldırılmıştır ve ilerleme durumu iletileri için destek de sağlar. Şu anda hub proxy 'lerinin karşılığı yok.

### <a name="persistentconnection-removal"></a>PersistentConnection kaldırma

ASP.NET Core SignalR, [Persistentconnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) sınıfı kaldırılmıştır.

### <a name="globalhost"></a>GlobalHost

ASP.NET Core çerçeveye yerleşik bağımlılık ekleme (dı) vardır. Hizmetler, [Hubcontext](xref:signalr/hubcontext)'e erışmek için dı kullanabilir. ASP.NET `GlobalHost` `HubContext` SignalRiçinde kullanılacak nesne ASP.NET Core yok. SignalR

### <a name="hubpipeline"></a>HubPipeline

ASP.NET Core SignalR modüller için `HubPipeline` desteğe sahip değildir.

## <a name="differences-on-the-client"></a>İstemci farkları

### <a name="typescript"></a>TypeScript

ASP.NET Core SignalR istemcisi [TypeScript](https://www.typescriptlang.org/)dilinde yazılır. [JavaScript istemcisini](xref:signalr/javascript-client)kullanırken JavaScript veya TypeScript ile yazabilirsiniz.

### <a name="the-javascript-client-is-hosted-at-npm"></a>JavaScript istemcisi NPM 'de barındırılıyor

::: moniker range=">= aspnetcore-3.0"

ASP.NET sürümlerinde JavaScript istemcisi, Visual Studio 'da bir NuGet paketi aracılığıyla elde edildi. ASP.NET Core sürümlerinde [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) NPM paketi Javascript kitaplıklarını içerir. Bu paket, **ASP.NET Core Web uygulaması** şablonuna dahil değildir. NPM paketini edinmek ve yüklemek `@microsoft/signalr` için NPM kullanın.

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

ASP.NET sürümlerinde JavaScript istemcisi, Visual Studio 'da bir NuGet paketi aracılığıyla elde edildi. ASP.NET Core sürümlerinde [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) NPM paketi Javascript kitaplıklarını içerir. Bu paket, **ASP.NET Core Web uygulaması** şablonuna dahil değildir. NPM paketini edinmek ve yüklemek `@aspnet/signalr` için NPM kullanın.

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a>jQuery

JQuery üzerindeki bağımlılık kaldırılmıştır, ancak projeler jQuery kullanmaya devam edebilir.

### <a name="internet-explorer-support"></a>Internet Explorer desteği

ASP.NET Core SignalR , Microsoft Internet Explorer 11 veya üstünü gerektirir ( SignalR ASP.net desteklenen Microsoft Internet Explorer 8 ve üzeri).

### <a name="javascript-client-method-syntax"></a>JavaScript istemci yöntemi sözdizimi

::: moniker range=">= aspnetcore-3.0"

JavaScript sözdizimi, ASP.NET sürümünden değiştirilmiştir SignalR. `$connection` Nesnesini kullanmak yerine, [hubconnectionbuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API 'sini kullanarak bir bağlantı oluşturun.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Hub 'ın çağırakullanabileceği istemci yöntemlerini belirtmek için [on](/javascript/api/@microsoft/signalr/HubConnection#on) metodunu kullanın.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

JavaScript sözdizimi, ASP.NET sürümünden değiştirilmiştir SignalR. `$connection` Nesnesini kullanmak yerine, [hubconnectionbuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API 'sini kullanarak bir bağlantı oluşturun.

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

Hub 'ın çağırakullanabileceği istemci yöntemlerini belirtmek için [on](/javascript/api/@aspnet/signalr/HubConnection#on) metodunu kullanın.

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

İstemci yöntemini oluşturduktan sonra hub bağlantısını başlatın. Günlüğe kaydetmek veya hataları işlemek için bir [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) metodunu zincirleyin.

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a>Hub proxy 'leri

::: moniker range=">= aspnetcore-3.0"

Hub proxy 'leri artık otomatik olarak oluşturulmaz. Bunun yerine, yöntem adı [Invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API 'sine bir dize olarak geçirilir.

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Hub proxy 'leri artık otomatik olarak oluşturulmaz. Bunun yerine, yöntem adı [Invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API 'sine bir dize olarak geçirilir.

::: moniker-end

### <a name="net-and-other-clients"></a>.NET ve diğer istemciler

[Microsoft. AspNetCoreSignalR.. İstemci](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) NuGet paketi, ASP.NET Core SignalRiçin .NET istemci kitaplıklarını içerir.

Bir hub <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> bağlantısının örneğini oluşturmak ve derlemek için kullanın.

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a>Ölçek Genişletme farklılıkları

ASP.NET SignalR SQL Server ve redsıs destekler. ASP.NET Core SignalR Azure SignalR hizmeti ve redsıs 'yi destekler.

### <a name="aspnet"></a>ASP.NET

* [SignalRAzure Service Bus ile ölçeği genişletme](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [SignalRRedsıs ile ölçek genişletme](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [SignalRSQL Server ile ölçeği genişletme](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a>ASP.NET Çekirdeği

* [Azure SignalR hizmeti](/azure/azure-signalr/)
* [Redsıs geri düzlemi](xref:signalr/redis-backplane)

## <a name="additional-resources"></a>Ek kaynaklar

* [Merkezler](xref:signalr/hubs)
* [JavaScript istemcisi](xref:signalr/javascript-client)
* [.NET istemcisi](xref:signalr/dotnet-client)
* [Desteklenen platformlar](xref:signalr/supported-platforms)
