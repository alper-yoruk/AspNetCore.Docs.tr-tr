---
title: BlazorGünlüğe kaydetme ASP.NET Core
author: guardrex
description: BlazorGünlük düzeyi yapılandırma ve bileşenlerden günlük iletilerini yazma dahil olmak üzere uygulamalarda oturum açma hakkında bilgi edinin Razor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/11/2020
no-loc:
- appsettings.json
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
uid: blazor/fundamentals/logging
zone_pivot_groups: blazor-hosting-models
ms.openlocfilehash: 78117fa6e9c7d5aed3fb31bbd3afee55b3b5b875
ms.sourcegitcommit: 6b87f2e064cea02e65dacd206394b44f5c604282
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97506714"
---
# <a name="aspnet-core-no-locblazor-logging"></a><span data-ttu-id="8a88f-103">BlazorGünlüğe kaydetme ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8a88f-103">ASP.NET Core Blazor logging</span></span>

::: zone pivot="webassembly"

<span data-ttu-id="8a88f-104">Blazor WebAssemblyÖzelliği ile uygulamalarda özel günlük kaydını yapılandırın <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="8a88f-104">Configure custom logging in Blazor WebAssembly apps with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> property.</span></span>

<span data-ttu-id="8a88f-105">Ad alanını şu <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> şekilde ekleyin `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="8a88f-105">Add the namespace for <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> to `Program.cs`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
```

<span data-ttu-id="8a88f-106">' `Program.Main` De `Program.cs` , en düşük günlük kaydı düzeyini ile ayarlayın <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> ve özel günlüğe kaydetme sağlayıcısını ekleyin:</span><span class="sxs-lookup"><span data-stu-id="8a88f-106">In `Program.Main` of `Program.cs`, set the minimum logging level with <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> and add the custom logging provider:</span></span>

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
...
builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="8a88f-107">`Logging`Özelliği türündedir <xref:Microsoft.Extensions.Logging.ILoggingBuilder> , bu nedenle tüm uzantı yöntemleri <xref:Microsoft.Extensions.Logging.ILoggingBuilder> üzerinde de kullanılabilir `Logging` .</span><span class="sxs-lookup"><span data-stu-id="8a88f-107">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="8a88f-108">Günlüğe kaydetme yapılandırması, uygulama ayarları dosyalarından yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="8a88f-108">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="8a88f-109">Daha fazla bilgi için bkz. <xref:blazor/fundamentals/configuration#logging-configuration>.</span><span class="sxs-lookup"><span data-stu-id="8a88f-109">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## <a name="no-locsignalr-net-client-logging"></a><span data-ttu-id="8a88f-110">SignalR .NET istemci günlüğü</span><span class="sxs-lookup"><span data-stu-id="8a88f-110">SignalR .NET client logging</span></span>

<span data-ttu-id="8a88f-111">Öğesine <xref:Microsoft.Extensions.Logging.ILoggerProvider> `WebAssemblyConsoleLogger` geçirilen günlüğe kaydetme sağlayıcılarına eklemek için bir ekleyin <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> .</span><span class="sxs-lookup"><span data-stu-id="8a88f-111">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="8a88f-112">Geleneksel olarak <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> , `WebAssemblyConsoleLogger` tarayıcıya özgü günlüğe kaydetme API 'leri (örneğin,) etrafında bir sarmalayıcı olur `console.log` .</span><span class="sxs-lookup"><span data-stu-id="8a88f-112">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="8a88f-113">' Nin kullanımı, `WebAssemblyConsoleLogger` bir tarayıcı bağlamı Içinde mono içinde mümkün olan bir kayıt oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8a88f-113">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

<span data-ttu-id="8a88f-114">İçin ad alanını ekleyin <xref:Microsoft.Extensions.Logging?displayProperty=fullName> ve bileşene ekleyin <xref:Microsoft.Extensions.Logging.ILoggerProvider> :</span><span class="sxs-lookup"><span data-stu-id="8a88f-114">Add the namespace for <xref:Microsoft.Extensions.Logging?displayProperty=fullName> and inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> into the component:</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider
```

<span data-ttu-id="8a88f-115">Bileşenin [ `OnInitializedAsync` yönteminde](xref:blazor/components/lifecycle#component-initialization-methods), şunu kullanın <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType> :</span><span class="sxs-lookup"><span data-stu-id="8a88f-115">In the component's [`OnInitializedAsync` method](xref:blazor/components/lifecycle#component-initialization-methods), use <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType>:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

::: zone-end

::: zone pivot="server"

<span data-ttu-id="8a88f-116">İle ilgili genel ASP.NET Core günlük Kılavuzu için Blazor Server bkz <xref:fundamentals/logging/index> ..</span><span class="sxs-lookup"><span data-stu-id="8a88f-116">For general ASP.NET Core logging guidance that pertains to Blazor Server, see <xref:fundamentals/logging/index>.</span></span>

::: zone-end

## <a name="log-in-no-locrazor-components"></a><span data-ttu-id="8a88f-117">RazorBileşenlerdeki oturum açma</span><span class="sxs-lookup"><span data-stu-id="8a88f-117">Log in Razor components</span></span>

<span data-ttu-id="8a88f-118">Uygulama başlangıç yapılandırmasına göre günlüğe kaydetme defterleri.</span><span class="sxs-lookup"><span data-stu-id="8a88f-118">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="8a88f-119">`using`İçin olan yönergesi <xref:Microsoft.Extensions.Logging> , ve gibi API 'Leri için [IntelliSense](/visualstudio/ide/using-intellisense) 'i desteklemek için gereklidir <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> .</span><span class="sxs-lookup"><span data-stu-id="8a88f-119">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support [IntelliSense](/visualstudio/ide/using-intellisense) completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="8a88f-120">Aşağıdaki örnekte, bileşenleri içindeki bir günlük kaydı gösterilmektedir <xref:Microsoft.Extensions.Logging.ILogger> .</span><span class="sxs-lookup"><span data-stu-id="8a88f-120">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in components.</span></span>

<span data-ttu-id="8a88f-121">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="8a88f-121">`Pages/Counter.razor`:</span></span>

[!code-razor[](logging/samples_snapshot/Counter1.razor?highlight=3,16)]

<span data-ttu-id="8a88f-122">Aşağıdaki örnekte, bileşenleri içindeki bir günlük kaydı gösterilmektedir <xref:Microsoft.Extensions.Logging.ILoggerFactory> .</span><span class="sxs-lookup"><span data-stu-id="8a88f-122">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in components.</span></span>

<span data-ttu-id="8a88f-123">`Pages/Counter.razor`:</span><span class="sxs-lookup"><span data-stu-id="8a88f-123">`Pages/Counter.razor`:</span></span>

[!code-razor[](logging/samples_snapshot/Counter2.razor?highlight=3,16-17)]

## <a name="additional-resources"></a><span data-ttu-id="8a88f-124">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="8a88f-124">Additional resources</span></span>

* <xref:fundamentals/logging/index>
