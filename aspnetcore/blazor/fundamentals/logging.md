---
title: BlazorGünlüğe kaydetme ASP.NET Core
author: guardrex
description: BlazorGünlük düzeyi yapılandırma ve bileşenlerden günlük iletilerini yazma dahil olmak üzere uygulamalarda oturum açma hakkında bilgi edinin Razor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/logging
ms.openlocfilehash: b0448d5f6e5e16a726eb2274dcacb4dfa8314b5d
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103840"
---
# <a name="aspnet-core-blazor-logging"></a><span data-ttu-id="3d49d-103">BlazorGünlüğe kaydetme ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3d49d-103">ASP.NET Core Blazor logging</span></span>

## <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="3d49d-104">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="3d49d-104"> WebAssembly</span></span>

<span data-ttu-id="3d49d-105">BlazorWebassembly uygulamalarında `WebAssemblyHostBuilder.Logging` ' de bulunan özelliği kullanarak günlüğü yapılandırın `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="3d49d-105">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="3d49d-106">`Logging`Özelliği türündedir <xref:Microsoft.Extensions.Logging.ILoggingBuilder> , bu nedenle tüm uzantı yöntemleri <xref:Microsoft.Extensions.Logging.ILoggingBuilder> üzerinde de kullanılabilir `Logging` .</span><span class="sxs-lookup"><span data-stu-id="3d49d-106">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="3d49d-107">Günlüğe kaydetme yapılandırması, uygulama ayarları dosyalarından yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="3d49d-107">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="3d49d-108">Daha fazla bilgi için bkz. <xref:blazor/fundamentals/logging>.</span><span class="sxs-lookup"><span data-stu-id="3d49d-108">For more information, see <xref:blazor/fundamentals/logging>.</span></span>

## <a name="blazor-server"></a>Blazor<span data-ttu-id="3d49d-109">Server</span><span class="sxs-lookup"><span data-stu-id="3d49d-109"> Server</span></span>

<span data-ttu-id="3d49d-110">Genel ASP.NET Core günlük Kılavuzu için bkz <xref:fundamentals/logging/index> ..</span><span class="sxs-lookup"><span data-stu-id="3d49d-110">For general ASP.NET Core logging guidance, see <xref:fundamentals/logging/index>.</span></span>

## <a name="blazor-webassembly-signalr-net-client-logging"></a>Blazor<span data-ttu-id="3d49d-111">WebAssembly SignalR .NET istemci günlüğü</span><span class="sxs-lookup"><span data-stu-id="3d49d-111"> WebAssembly SignalR .NET client logging</span></span>

<span data-ttu-id="3d49d-112">Öğesine <xref:Microsoft.Extensions.Logging.ILoggerProvider> `WebAssemblyConsoleLogger` geçirilen günlüğe kaydetme sağlayıcılarına eklemek için bir ekleyin <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> .</span><span class="sxs-lookup"><span data-stu-id="3d49d-112">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="3d49d-113">Geleneksel olarak <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> , `WebAssemblyConsoleLogger` tarayıcıya özgü günlüğe kaydetme API 'leri (örneğin,) etrafında bir sarmalayıcı olur `console.log` .</span><span class="sxs-lookup"><span data-stu-id="3d49d-113">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="3d49d-114">' Nin kullanımı, `WebAssemblyConsoleLogger` bir tarayıcı bağlamı Içinde mono içinde mümkün olan bir kayıt oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3d49d-114">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-razor-components"></a><span data-ttu-id="3d49d-115">RazorBileşenlerdeki oturum açma</span><span class="sxs-lookup"><span data-stu-id="3d49d-115">Log in Razor components</span></span>

<span data-ttu-id="3d49d-116">Uygulama başlangıç yapılandırmasına göre günlüğe kaydetme defterleri.</span><span class="sxs-lookup"><span data-stu-id="3d49d-116">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="3d49d-117">`using`İçin olan yönergesi <xref:Microsoft.Extensions.Logging> , ve gibi API 'Leri için IntelliSense 'i desteklemek için gereklidir <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> .</span><span class="sxs-lookup"><span data-stu-id="3d49d-117">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="3d49d-118">Aşağıdaki örnek, bileşenleri içindeki bir günlüğü <xref:Microsoft.Extensions.Logging.ILogger> gösterir Razor :</span><span class="sxs-lookup"><span data-stu-id="3d49d-118">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILogger<Counter> logger;

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

<span data-ttu-id="3d49d-119">Aşağıdaki örnek, bileşenleri içindeki bir günlüğü <xref:Microsoft.Extensions.Logging.ILoggerFactory> gösterir Razor :</span><span class="sxs-lookup"><span data-stu-id="3d49d-119">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

```razor
@page "/counter"
@using Microsoft.Extensions.Logging;
@inject ILoggerFactory LoggerFactory

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        var logger = LoggerFactory.CreateLogger<Counter>();
        logger.LogWarning("Someone has clicked me!");

        currentCount++;
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="3d49d-120">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3d49d-120">Additional resources</span></span>

* <xref:fundamentals/logging/index>
