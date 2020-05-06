---
title: Arka plan SignalR hizmetlerinde ana bilgisayar ASP.NET Core
author: bradygaster
description: .NET Core BackgroundService sınıflarından SignalR istemcilere ileti gönderme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/background-services
ms.openlocfilehash: d5f1668d601f520939956985e46c62f3a5bdfcfa
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777299"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a>Arka plan SignalR hizmetlerinde ana bilgisayar ASP.NET Core

, [Brady Gaster](https://twitter.com/bradygaster) tarafından

Bu makalede şu yönergelere kılavuzluk sunulmaktadır:

* ASP.NET Core SignalR ile barındırılan bir arka plan çalışan işlemi kullanarak hub 'ları barındırma.
* .NET Core [Backgroundservice](xref:Microsoft.Extensions.Hosting.BackgroundService)içinden bağlı istemcilere ileti gönderme.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(nasıl indirileceği)](xref:index#how-to-download-a-sample)

## <a name="enable-signalr-in-startup"></a>Başlangıçta SignalR etkinleştir

::: moniker range=">= aspnetcore-3.0"

Arka plan SignalR çalışan işleminin bağlamında ASP.NET Core hub 'ların barındırılması, bir ASP.NET Core Web uygulamasında hub barındırmakla aynıdır. `Startup.ConfigureServices` Yöntemi, çağırma `services.AddSignalR` , gerekli hizmetleri desteklemek SignalRiçin ASP.NET Core bağımlılık ekleme (dı) katmanına ekler. ' `Startup.Configure`De, `MapHub` yöntemi, ASP.NET Core isteği ardışık `UseEndpoints` düzeninde hub uç noktalarına bağlanmak için geri çağırmada çağrılır.

```csharp
public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR();
        services.AddHostedService<Worker>();
    }

    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }

        app.UseRouting();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ClockHub>("/hubs/clock");
        });
    }
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

Arka plan SignalR çalışan işleminin bağlamında ASP.NET Core hub 'ların barındırılması, bir ASP.NET Core Web uygulamasında hub barındırmakla aynıdır. `Startup.ConfigureServices` Yöntemi, çağırma `services.AddSignalR` , gerekli hizmetleri desteklemek SignalRiçin ASP.NET Core bağımlılık ekleme (dı) katmanına ekler. ' `Startup.Configure`De, `UseSignalR` yöntemi, ASP.NET Core isteği ardışık düzeninde hub uç noktaları bağlamak için çağrılır.

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

Önceki örnekte, `ClockHub` sınıfı türü kesin belirlenmiş bir hub `Hub<T>` oluşturmak için sınıfı uygular. , `ClockHub` `Startup` Sınıfında, uç noktasındaki `/hubs/clock`isteklere yanıt vermek için yapılandırılmıştır.

Türü kesin belirlenmiş hub 'Lar hakkında daha fazla bilgi için bkz. [ASP.NET Core SignalR Için hub 'ları kullanma](xref:signalr/hubs#strongly-typed-hubs).

> [!NOTE]
> Bu işlevsellik, [\<hub t>](xref:Microsoft.AspNetCore.SignalR.Hub`1) sınıfıyla sınırlı değildir. [Dynamichub](xref:Microsoft.AspNetCore.SignalR.DynamicHub)gibi [hub](xref:Microsoft.AspNetCore.SignalR.Hub)'dan devralan tüm sınıflar da çalışır.

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

Kesin olarak yazılan `ClockHub` tarafından kullanılan arabirim `IClock` arabirimidir.

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a>Arka plan SignalR hizmetinden hub çağırma

Başlangıç sırasında, a `Worker` `BackgroundService`sınıfı kullanılarak `AddHostedService`etkindir.

```csharp
services.AddHostedService<Worker>();
```

Ayrıca, her hub 'ın ASP.NET Core HTTP isteği ardışık düzeninde tek bir uç noktaya eklendiği için, her hub, sunucu üzerinde bir `IHubContext<T>` ile temsil edilir. `Startup` SignalR ASP.NET Core dı özelliklerini kullanarak, sınıflar, MVC denetleyici sınıfları veya `BackgroundService` Razor sayfa modelleri gibi barındırma katmanı tarafından oluşturulan diğer sınıflar, oluşturma `IHubContext<ClockHub, IClock>` sırasında örneklerini kabul ederek sunucu tarafı hub 'larına başvurular alabilir.

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

`ExecuteAsync` Yöntemi arka plan hizmetinde yinelemeli olarak çağrıldığı için sunucunun geçerli tarih ve saati kullanılarak bağlı istemcilere gönderilir `ClockHub`.

## <a name="react-to-signalr-events-with-background-services"></a>Arka plan SignalR hizmetleriyle olaylara tepki verme

JavaScript SignalR istemcisi veya bir .net Masaüstü <xref:signalr/dotnet-client>uygulaması kullanan tek sayfalı bir uygulama gibi kullanarak, `BackgroundService` `IHostedService` SignalR hub 'lara bağlanmak ve olaylara yanıt vermek için bir veya uygulaması da kullanılabilir.

`ClockHubClient` Sınıfı hem `IClock` arabirimini hem de `IHostedService` arabirimini uygular. Bu sayede, sürekli olarak çalıştırılmak ve `Startup` sunucudan hub olaylarına yanıt vermek için etkinleştirilebilir.

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

`ClockHubClient` Başlatma sırasında, `HubConnection` bir örneğini oluşturur ve hub 'ın `IClock.ShowTime` `ShowTime` olayının işleyicisi olarak yöntemi sunar.

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

`IHostedService.StartAsync` Uygulamada, zaman uyumsuz olarak `HubConnection` başlatılır.

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

`IHostedService.StopAsync` Yöntemi `HubConnection` sırasında, zaman uyumsuz olarak bırakıldı.

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a>Ek kaynaklar

* [başlarken](xref:tutorials/signalr)
* [Merkezler](xref:signalr/hubs)
* [Azure’da Yayımlama](xref:signalr/publish-to-azure-web-app)
* [Türü kesin belirlenmiş hub 'Lar](xref:signalr/hubs#strongly-typed-hubs)
