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
# <a name="host-aspnet-core-signalr-in-background-services"></a><span data-ttu-id="48b70-103">Arka plan SignalR hizmetlerinde ana bilgisayar ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="48b70-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="48b70-104">, [Brady Gaster](https://twitter.com/bradygaster) tarafından</span><span class="sxs-lookup"><span data-stu-id="48b70-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="48b70-105">Bu makalede şu yönergelere kılavuzluk sunulmaktadır:</span><span class="sxs-lookup"><span data-stu-id="48b70-105">This article provides guidance for:</span></span>

* <span data-ttu-id="48b70-106">ASP.NET Core SignalR ile barındırılan bir arka plan çalışan işlemi kullanarak hub 'ları barındırma.</span><span class="sxs-lookup"><span data-stu-id="48b70-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="48b70-107">.NET Core [Backgroundservice](xref:Microsoft.Extensions.Hosting.BackgroundService)içinden bağlı istemcilere ileti gönderme.</span><span class="sxs-lookup"><span data-stu-id="48b70-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

<span data-ttu-id="48b70-108">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(nasıl indirileceği)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="48b70-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/sample/) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

## <a name="enable-signalr-in-startup"></a><span data-ttu-id="48b70-109">Başlangıçta SignalR etkinleştir</span><span class="sxs-lookup"><span data-stu-id="48b70-109">Enable SignalR in startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="48b70-110">Arka plan SignalR çalışan işleminin bağlamında ASP.NET Core hub 'ların barındırılması, bir ASP.NET Core Web uygulamasında hub barındırmakla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="48b70-110">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="48b70-111">`Startup.ConfigureServices` Yöntemi, çağırma `services.AddSignalR` , gerekli hizmetleri desteklemek SignalRiçin ASP.NET Core bağımlılık ekleme (dı) katmanına ekler.</span><span class="sxs-lookup"><span data-stu-id="48b70-111">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="48b70-112">' `Startup.Configure`De, `MapHub` yöntemi, ASP.NET Core isteği ardışık `UseEndpoints` düzeninde hub uç noktalarına bağlanmak için geri çağırmada çağrılır.</span><span class="sxs-lookup"><span data-stu-id="48b70-112">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to connect the Hub endpoints in the ASP.NET Core request pipeline.</span></span>

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

<span data-ttu-id="48b70-113">Arka plan SignalR çalışan işleminin bağlamında ASP.NET Core hub 'ların barındırılması, bir ASP.NET Core Web uygulamasında hub barındırmakla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="48b70-113">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="48b70-114">`Startup.ConfigureServices` Yöntemi, çağırma `services.AddSignalR` , gerekli hizmetleri desteklemek SignalRiçin ASP.NET Core bağımlılık ekleme (dı) katmanına ekler.</span><span class="sxs-lookup"><span data-stu-id="48b70-114">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="48b70-115">' `Startup.Configure`De, `UseSignalR` yöntemi, ASP.NET Core isteği ardışık düzeninde hub uç noktaları bağlamak için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="48b70-115">In `Startup.Configure`, the `UseSignalR` method is called to connect the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="48b70-116">Önceki örnekte, `ClockHub` sınıfı türü kesin belirlenmiş bir hub `Hub<T>` oluşturmak için sınıfı uygular.</span><span class="sxs-lookup"><span data-stu-id="48b70-116">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="48b70-117">, `ClockHub` `Startup` Sınıfında, uç noktasındaki `/hubs/clock`isteklere yanıt vermek için yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="48b70-117">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="48b70-118">Türü kesin belirlenmiş hub 'Lar hakkında daha fazla bilgi için bkz. [ASP.NET Core SignalR Için hub 'ları kullanma](xref:signalr/hubs#strongly-typed-hubs).</span><span class="sxs-lookup"><span data-stu-id="48b70-118">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="48b70-119">Bu işlevsellik, [\<hub t>](xref:Microsoft.AspNetCore.SignalR.Hub`1) sınıfıyla sınırlı değildir.</span><span class="sxs-lookup"><span data-stu-id="48b70-119">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="48b70-120">[Dynamichub](xref:Microsoft.AspNetCore.SignalR.DynamicHub)gibi [hub](xref:Microsoft.AspNetCore.SignalR.Hub)'dan devralan tüm sınıflar da çalışır.</span><span class="sxs-lookup"><span data-stu-id="48b70-120">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), will also work.</span></span>

[!code-csharp[Startup](background-service/sample/Server/ClockHub.cs?name=ClockHub)]

<span data-ttu-id="48b70-121">Kesin olarak yazılan `ClockHub` tarafından kullanılan arabirim `IClock` arabirimidir.</span><span class="sxs-lookup"><span data-stu-id="48b70-121">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

[!code-csharp[Startup](background-service/sample/HubServiceInterfaces/IClock.cs?name=IClock)]

## <a name="call-a-signalr-hub-from-a-background-service"></a><span data-ttu-id="48b70-122">Arka plan SignalR hizmetinden hub çağırma</span><span class="sxs-lookup"><span data-stu-id="48b70-122">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="48b70-123">Başlangıç sırasında, a `Worker` `BackgroundService`sınıfı kullanılarak `AddHostedService`etkindir.</span><span class="sxs-lookup"><span data-stu-id="48b70-123">During startup, the `Worker` class, a `BackgroundService`, is enabled using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="48b70-124">Ayrıca, her hub 'ın ASP.NET Core HTTP isteği ardışık düzeninde tek bir uç noktaya eklendiği için, her hub, sunucu üzerinde bir `IHubContext<T>` ile temsil edilir. `Startup` SignalR</span><span class="sxs-lookup"><span data-stu-id="48b70-124">Since SignalR is also enabled up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="48b70-125">ASP.NET Core dı özelliklerini kullanarak, sınıflar, MVC denetleyici sınıfları veya `BackgroundService` Razor sayfa modelleri gibi barındırma katmanı tarafından oluşturulan diğer sınıflar, oluşturma `IHubContext<ClockHub, IClock>` sırasında örneklerini kabul ederek sunucu tarafı hub 'larına başvurular alabilir.</span><span class="sxs-lookup"><span data-stu-id="48b70-125">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

[!code-csharp[Startup](background-service/sample/Server/Worker.cs?name=Worker)]

<span data-ttu-id="48b70-126">`ExecuteAsync` Yöntemi arka plan hizmetinde yinelemeli olarak çağrıldığı için sunucunun geçerli tarih ve saati kullanılarak bağlı istemcilere gönderilir `ClockHub`.</span><span class="sxs-lookup"><span data-stu-id="48b70-126">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-signalr-events-with-background-services"></a><span data-ttu-id="48b70-127">Arka plan SignalR hizmetleriyle olaylara tepki verme</span><span class="sxs-lookup"><span data-stu-id="48b70-127">React to SignalR events with background services</span></span>

<span data-ttu-id="48b70-128">JavaScript SignalR istemcisi veya bir .net Masaüstü <xref:signalr/dotnet-client>uygulaması kullanan tek sayfalı bir uygulama gibi kullanarak, `BackgroundService` `IHostedService` SignalR hub 'lara bağlanmak ve olaylara yanıt vermek için bir veya uygulaması da kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="48b70-128">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="48b70-129">`ClockHubClient` Sınıfı hem `IClock` arabirimini hem de `IHostedService` arabirimini uygular.</span><span class="sxs-lookup"><span data-stu-id="48b70-129">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="48b70-130">Bu sayede, sürekli olarak çalıştırılmak ve `Startup` sunucudan hub olaylarına yanıt vermek için etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="48b70-130">This way it can be enabled during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="48b70-131">`ClockHubClient` Başlatma sırasında, `HubConnection` bir örneğini oluşturur ve hub 'ın `IClock.ShowTime` `ShowTime` olayının işleyicisi olarak yöntemi sunar.</span><span class="sxs-lookup"><span data-stu-id="48b70-131">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and enables the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

[!code-csharp[The ClockHubClient constructor](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="48b70-132">`IHostedService.StartAsync` Uygulamada, zaman uyumsuz olarak `HubConnection` başlatılır.</span><span class="sxs-lookup"><span data-stu-id="48b70-132">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="48b70-133">`IHostedService.StopAsync` Yöntemi `HubConnection` sırasında, zaman uyumsuz olarak bırakıldı.</span><span class="sxs-lookup"><span data-stu-id="48b70-133">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/sample/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

## <a name="additional-resources"></a><span data-ttu-id="48b70-134">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="48b70-134">Additional resources</span></span>

* [<span data-ttu-id="48b70-135">başlarken</span><span class="sxs-lookup"><span data-stu-id="48b70-135">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="48b70-136">Merkezler</span><span class="sxs-lookup"><span data-stu-id="48b70-136">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="48b70-137">Azure’da Yayımlama</span><span class="sxs-lookup"><span data-stu-id="48b70-137">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="48b70-138">Türü kesin belirlenmiş hub 'Lar</span><span class="sxs-lookup"><span data-stu-id="48b70-138">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
