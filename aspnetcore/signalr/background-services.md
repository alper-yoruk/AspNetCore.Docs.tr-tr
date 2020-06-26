---
title: SignalRArka plan hizmetlerinde ana bilgisayar ASP.NET Core
author: bradygaster
description: SignalR.NET Core BackgroundService sınıflarından istemcilere ileti gönderme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/background-services
ms.openlocfilehash: bf5fff213b2cd7db0b3227922a8c5babba2fc904
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85409091"
---
# <a name="host-aspnet-core-signalr-in-background-services"></a><span data-ttu-id="cdbd2-103">SignalRArka plan hizmetlerinde ana bilgisayar ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cdbd2-103">Host ASP.NET Core SignalR in background services</span></span>

<span data-ttu-id="cdbd2-104">, [Brady Gaster](https://twitter.com/bradygaster) tarafından</span><span class="sxs-lookup"><span data-stu-id="cdbd2-104">By [Brady Gaster](https://twitter.com/bradygaster)</span></span>

<span data-ttu-id="cdbd2-105">Bu makalede şu yönergelere kılavuzluk sunulmaktadır:</span><span class="sxs-lookup"><span data-stu-id="cdbd2-105">This article provides guidance for:</span></span>

* <span data-ttu-id="cdbd2-106">SignalRASP.NET Core ile barındırılan bir arka plan çalışan işlemi kullanarak hub 'ları barındırma.</span><span class="sxs-lookup"><span data-stu-id="cdbd2-106">Hosting SignalR Hubs using a background worker process hosted with ASP.NET Core.</span></span>
* <span data-ttu-id="cdbd2-107">.NET Core [Backgroundservice](xref:Microsoft.Extensions.Hosting.BackgroundService)içinden bağlı istemcilere ileti gönderme.</span><span class="sxs-lookup"><span data-stu-id="cdbd2-107">Sending messages to connected clients from within a .NET Core [BackgroundService](xref:Microsoft.Extensions.Hosting.BackgroundService).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cdbd2-108">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(nasıl indirileceği)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="cdbd2-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/3.x) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="cdbd2-109">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(nasıl indirileceği)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="cdbd2-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/background-service/samples/2.2) [(how to download)](xref:index#how-to-download-a-sample)</span></span>

::: moniker-end

## <a name="enable-signalr-in-startup"></a><span data-ttu-id="cdbd2-110">SignalRBaşlangıçta etkinleştir</span><span class="sxs-lookup"><span data-stu-id="cdbd2-110">Enable SignalR in startup</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cdbd2-111">SignalRArka plan çalışan işleminin bağlamında ASP.NET Core hub 'ların barındırılması, bir ASP.NET Core Web uygulamasında hub barındırmakla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="cdbd2-111">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="cdbd2-112">`Startup.ConfigureServices`Yöntemi, çağırma, `services.AddSignalR` gerekli hizmetleri desteklemek Için ASP.NET Core bağımlılık ekleme (dı) katmanına ekler SignalR .</span><span class="sxs-lookup"><span data-stu-id="cdbd2-112">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="cdbd2-113">`Startup.Configure`' De, `MapHub` yöntemi, `UseEndpoints` ASP.NET Core Isteği ardışık düzeninde hub uç noktalarına bağlanmak için geri çağırmada çağrılır.</span><span class="sxs-lookup"><span data-stu-id="cdbd2-113">In `Startup.Configure`, the `MapHub` method is called in the `UseEndpoints` callback to connect the Hub endpoints in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/samples/3.x/Server/Startup.cs?name=Startup)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="cdbd2-114">SignalRArka plan çalışan işleminin bağlamında ASP.NET Core hub 'ların barındırılması, bir ASP.NET Core Web uygulamasında hub barındırmakla aynıdır.</span><span class="sxs-lookup"><span data-stu-id="cdbd2-114">Hosting ASP.NET Core SignalR Hubs in the context of a background worker process is identical to hosting a Hub in an ASP.NET Core web app.</span></span> <span data-ttu-id="cdbd2-115">`Startup.ConfigureServices`Yöntemi, çağırma, `services.AddSignalR` gerekli hizmetleri desteklemek Için ASP.NET Core bağımlılık ekleme (dı) katmanına ekler SignalR .</span><span class="sxs-lookup"><span data-stu-id="cdbd2-115">In the `Startup.ConfigureServices` method, calling `services.AddSignalR` adds the required services to the ASP.NET Core Dependency Injection (DI) layer to support SignalR.</span></span> <span data-ttu-id="cdbd2-116">`Startup.Configure`' De, `UseSignalR` yöntemi, ASP.NET Core isteği ardışık düzeninde hub uç noktaları bağlamak için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="cdbd2-116">In `Startup.Configure`, the `UseSignalR` method is called to connect the Hub endpoint(s) in the ASP.NET Core request pipeline.</span></span>

[!code-csharp[Startup](background-service/samples/2.2/Server/Startup.cs?name=Startup)]

::: moniker-end

<span data-ttu-id="cdbd2-117">Önceki örnekte, `ClockHub` sınıfı `Hub<T>` türü kesin belirlenmiş bir hub oluşturmak için sınıfı uygular.</span><span class="sxs-lookup"><span data-stu-id="cdbd2-117">In the preceding example, the `ClockHub` class implements the `Hub<T>` class to create a strongly typed Hub.</span></span> <span data-ttu-id="cdbd2-118">, `ClockHub` `Startup` Sınıfında, uç noktasındaki isteklere yanıt vermek için yapılandırılmıştır `/hubs/clock` .</span><span class="sxs-lookup"><span data-stu-id="cdbd2-118">The `ClockHub` has been configured in the `Startup` class to respond to requests at the endpoint `/hubs/clock`.</span></span>

<span data-ttu-id="cdbd2-119">Türü kesin belirlenmiş hub 'Lar hakkında daha fazla bilgi için bkz. [ SignalR ASP.NET Core için hub 'ları kullanma](xref:signalr/hubs#strongly-typed-hubs).</span><span class="sxs-lookup"><span data-stu-id="cdbd2-119">For more information on strongly typed Hubs, see [Use hubs in SignalR for ASP.NET Core](xref:signalr/hubs#strongly-typed-hubs).</span></span>

> [!NOTE]
> <span data-ttu-id="cdbd2-120">Bu işlev, [hub \<T> ](xref:Microsoft.AspNetCore.SignalR.Hub`1) sınıfıyla sınırlı değildir.</span><span class="sxs-lookup"><span data-stu-id="cdbd2-120">This functionality isn't limited to the [Hub\<T>](xref:Microsoft.AspNetCore.SignalR.Hub`1) class.</span></span> <span data-ttu-id="cdbd2-121">[Dynamichub](xref:Microsoft.AspNetCore.SignalR.DynamicHub)gibi [hub](xref:Microsoft.AspNetCore.SignalR.Hub)'dan devralan herhangi bir sınıf, işe yarar.</span><span class="sxs-lookup"><span data-stu-id="cdbd2-121">Any class that inherits from [Hub](xref:Microsoft.AspNetCore.SignalR.Hub), such as [DynamicHub](xref:Microsoft.AspNetCore.SignalR.DynamicHub), works.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/ClockHub.cs?name=ClockHub)]

::: moniker-end

<span data-ttu-id="cdbd2-122">Kesin olarak yazılan tarafından kullanılan arabirim `ClockHub` `IClock` arabirimidir.</span><span class="sxs-lookup"><span data-stu-id="cdbd2-122">The interface used by the strongly typed `ClockHub` is the `IClock` interface.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/HubServiceInterfaces/IClock.cs?name=IClock)]

::: moniker-end

## <a name="call-a-signalr-hub-from-a-background-service"></a><span data-ttu-id="cdbd2-123">SignalRArka plan hizmetinden hub çağırma</span><span class="sxs-lookup"><span data-stu-id="cdbd2-123">Call a SignalR Hub from a background service</span></span>

<span data-ttu-id="cdbd2-124">Başlangıç sırasında, `Worker` a sınıfı `BackgroundService` kullanılarak etkindir `AddHostedService` .</span><span class="sxs-lookup"><span data-stu-id="cdbd2-124">During startup, the `Worker` class, a `BackgroundService`, is enabled using `AddHostedService`.</span></span>

```csharp
services.AddHostedService<Worker>();
```

<span data-ttu-id="cdbd2-125">SignalRAyrıca `Startup` , her hub 'ıN ASP.NET Core http isteği ardışık düzeninde tek bir uç noktaya eklendiği için, her hub, sunucu üzerinde bir ile temsil edilir `IHubContext<T>` .</span><span class="sxs-lookup"><span data-stu-id="cdbd2-125">Since SignalR is also enabled up during the `Startup` phase, in which each Hub is attached to an individual endpoint in ASP.NET Core's HTTP request pipeline, each Hub is represented by an `IHubContext<T>` on the server.</span></span> <span data-ttu-id="cdbd2-126">ASP.NET Core dı özelliklerini kullanarak, `BackgroundService` sınıflar, MVC denetleyici sınıfları veya sayfa modelleri gibi barındırma katmanı tarafından oluşturulan diğer sınıflar, Razor oluşturma sırasında örneklerini kabul ederek sunucu tarafı hub 'larına başvurular alabilir `IHubContext<ClockHub, IClock>` .</span><span class="sxs-lookup"><span data-stu-id="cdbd2-126">Using ASP.NET Core's DI features, other classes instantiated by the hosting layer, like `BackgroundService` classes, MVC Controller classes, or Razor page models, can get references to server-side Hubs by accepting instances of `IHubContext<ClockHub, IClock>` during construction.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[Startup](background-service/samples/3.x/Server/Worker.cs?name=Worker)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[Startup](background-service/samples/2.2/Server/Worker.cs?name=Worker)]

::: moniker-end

<span data-ttu-id="cdbd2-127">`ExecuteAsync`Yöntemi arka plan hizmetinde yinelemeli olarak çağrıldığı için sunucunun geçerli tarih ve saati kullanılarak bağlı istemcilere gönderilir `ClockHub` .</span><span class="sxs-lookup"><span data-stu-id="cdbd2-127">As the `ExecuteAsync` method is called iteratively in the background service, the server's current date and time are sent to the connected clients using the `ClockHub`.</span></span>

## <a name="react-to-signalr-events-with-background-services"></a><span data-ttu-id="cdbd2-128">SignalRArka plan hizmetleriyle olaylara tepki verme</span><span class="sxs-lookup"><span data-stu-id="cdbd2-128">React to SignalR events with background services</span></span>

<span data-ttu-id="cdbd2-129">JavaScript istemcisi veya bir .NET masaüstü uygulaması kullanan tek sayfalı bir uygulama gibi kullanarak, SignalR <xref:signalr/dotnet-client> `BackgroundService` `IHostedService` SignalR hub 'lara bağlanmak ve olaylara yanıt vermek için bir veya uygulaması da kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="cdbd2-129">Like a Single Page App using the JavaScript client for SignalR or a .NET desktop app can do using the using the <xref:signalr/dotnet-client>, a `BackgroundService` or `IHostedService` implementation can also be used to connect to SignalR Hubs and respond to events.</span></span>

<span data-ttu-id="cdbd2-130">`ClockHubClient`Sınıfı hem arabirimini hem de `IClock` `IHostedService` arabirimini uygular.</span><span class="sxs-lookup"><span data-stu-id="cdbd2-130">The `ClockHubClient` class implements both the `IClock` interface and the `IHostedService` interface.</span></span> <span data-ttu-id="cdbd2-131">Bu sayede `Startup` , sürekli olarak çalıştırılmak ve sunucudan hub olaylarına yanıt vermek için etkinleştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="cdbd2-131">This way it can be enabled during `Startup` to run continuously and respond to Hub events from the server.</span></span>

```csharp
public partial class ClockHubClient : IClock, IHostedService
{
}
```

<span data-ttu-id="cdbd2-132">Başlatma sırasında, `ClockHubClient` bir örneğini oluşturur `HubConnection` ve `IClock.ShowTime` hub 'ın olayının işleyicisi olarak yöntemi sunar `ShowTime` .</span><span class="sxs-lookup"><span data-stu-id="cdbd2-132">During initialization, the `ClockHubClient` creates an instance of a `HubConnection` and enables the `IClock.ShowTime` method as the handler for the Hub's `ShowTime` event.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[The ClockHubClient constructor](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="cdbd2-133">Uygulamada, `IHostedService.StartAsync` `HubConnection` zaman uyumsuz olarak başlatılır.</span><span class="sxs-lookup"><span data-stu-id="cdbd2-133">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="cdbd2-134">Yöntemi sırasında, `IHostedService.StopAsync` `HubConnection` zaman uyumsuz olarak bırakıldı.</span><span class="sxs-lookup"><span data-stu-id="cdbd2-134">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/samples/3.x/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end
::: moniker range="<= aspnetcore-2.2"

[!code-csharp[The ClockHubClient constructor](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=ClockHubClientCtor)]

<span data-ttu-id="cdbd2-135">Uygulamada, `IHostedService.StartAsync` `HubConnection` zaman uyumsuz olarak başlatılır.</span><span class="sxs-lookup"><span data-stu-id="cdbd2-135">In the `IHostedService.StartAsync` implementation, the `HubConnection` is started asynchronously.</span></span>

[!code-csharp[StartAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StartAsync)]

<span data-ttu-id="cdbd2-136">Yöntemi sırasında, `IHostedService.StopAsync` `HubConnection` zaman uyumsuz olarak bırakıldı.</span><span class="sxs-lookup"><span data-stu-id="cdbd2-136">During the `IHostedService.StopAsync` method, the `HubConnection` is disposed of asynchronously.</span></span>

[!code-csharp[StopAsync method](background-service/samples/2.2/Clients.ConsoleTwo/ClockHubClient.cs?name=StopAsync)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="cdbd2-137">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="cdbd2-137">Additional resources</span></span>

* [<span data-ttu-id="cdbd2-138">Kullanmaya başlayın</span><span class="sxs-lookup"><span data-stu-id="cdbd2-138">Get started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="cdbd2-139">Merkezler</span><span class="sxs-lookup"><span data-stu-id="cdbd2-139">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="cdbd2-140">Azure’da Yayımlama</span><span class="sxs-lookup"><span data-stu-id="cdbd2-140">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="cdbd2-141">Türü kesin belirlenmiş hub 'Lar</span><span class="sxs-lookup"><span data-stu-id="cdbd2-141">Strongly typed Hubs</span></span>](xref:signalr/hubs#strongly-typed-hubs)
