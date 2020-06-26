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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/logging
ms.openlocfilehash: 1f4b18bdea02016fb76b75dd01a8fcbeab9b2bc9
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402838"
---
# <a name="aspnet-core-blazor-logging"></a><span data-ttu-id="0e26b-103">BlazorGünlüğe kaydetme ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0e26b-103">ASP.NET Core Blazor logging</span></span>

## Blazor WebAssembly

<span data-ttu-id="0e26b-104">Uygulamalarda oturum açma Blazor WebAssembly `WebAssemblyHostBuilder.Logging` özelliğini içindeki özelliği kullanarak yapılandırın `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="0e26b-104">Configure logging in Blazor WebAssembly apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="0e26b-105">`Logging`Özelliği türündedir <xref:Microsoft.Extensions.Logging.ILoggingBuilder> , bu nedenle tüm uzantı yöntemleri <xref:Microsoft.Extensions.Logging.ILoggingBuilder> üzerinde de kullanılabilir `Logging` .</span><span class="sxs-lookup"><span data-stu-id="0e26b-105">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="0e26b-106">Günlüğe kaydetme yapılandırması, uygulama ayarları dosyalarından yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="0e26b-106">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="0e26b-107">Daha fazla bilgi için bkz. <xref:blazor/fundamentals/configuration#logging-configuration>.</span><span class="sxs-lookup"><span data-stu-id="0e26b-107">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## Blazor Server

<span data-ttu-id="0e26b-108">Genel ASP.NET Core günlük Kılavuzu için bkz <xref:fundamentals/logging/index> ..</span><span class="sxs-lookup"><span data-stu-id="0e26b-108">For general ASP.NET Core logging guidance, see <xref:fundamentals/logging/index>.</span></span>

## <a name="blazor-webassembly-signalr-net-client-logging"></a>Blazor WebAssembly<span data-ttu-id="0e26b-109">SignalR.NET istemci günlüğü</span><span class="sxs-lookup"><span data-stu-id="0e26b-109"> SignalR .NET client logging</span></span>

<span data-ttu-id="0e26b-110">Öğesine <xref:Microsoft.Extensions.Logging.ILoggerProvider> `WebAssemblyConsoleLogger` geçirilen günlüğe kaydetme sağlayıcılarına eklemek için bir ekleyin <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> .</span><span class="sxs-lookup"><span data-stu-id="0e26b-110">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="0e26b-111">Geleneksel olarak <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> , `WebAssemblyConsoleLogger` tarayıcıya özgü günlüğe kaydetme API 'leri (örneğin,) etrafında bir sarmalayıcı olur `console.log` .</span><span class="sxs-lookup"><span data-stu-id="0e26b-111">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="0e26b-112">' Nin kullanımı, `WebAssemblyConsoleLogger` bir tarayıcı bağlamı Içinde mono içinde mümkün olan bir kayıt oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0e26b-112">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-razor-components"></a><span data-ttu-id="0e26b-113">RazorBileşenlerdeki oturum açma</span><span class="sxs-lookup"><span data-stu-id="0e26b-113">Log in Razor components</span></span>

<span data-ttu-id="0e26b-114">Uygulama başlangıç yapılandırmasına göre günlüğe kaydetme defterleri.</span><span class="sxs-lookup"><span data-stu-id="0e26b-114">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="0e26b-115">`using`İçin olan yönergesi <xref:Microsoft.Extensions.Logging> , ve gibi API 'Leri için IntelliSense 'i desteklemek için gereklidir <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> .</span><span class="sxs-lookup"><span data-stu-id="0e26b-115">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="0e26b-116">Aşağıdaki örnek, bileşenleri içindeki bir günlüğü <xref:Microsoft.Extensions.Logging.ILogger> gösterir Razor :</span><span class="sxs-lookup"><span data-stu-id="0e26b-116">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in Razor components:</span></span>

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

<span data-ttu-id="0e26b-117">Aşağıdaki örnek, bileşenleri içindeki bir günlüğü <xref:Microsoft.Extensions.Logging.ILoggerFactory> gösterir Razor :</span><span class="sxs-lookup"><span data-stu-id="0e26b-117">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in Razor components:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="0e26b-118">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0e26b-118">Additional resources</span></span>

* <xref:fundamentals/logging/index>
