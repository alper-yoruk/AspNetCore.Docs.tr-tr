---
title: :::no-loc(Blazor):::Günlüğe kaydetme ASP.NET Core
author: guardrex
description: ':::no-loc(Blazor):::Günlük düzeyi yapılandırma ve bileşenlerden günlük iletilerini yazma dahil olmak üzere uygulamalarda oturum açma hakkında bilgi edinin :::no-loc(Razor)::: .'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/fundamentals/logging
ms.openlocfilehash: 72d339a4768b734ff33e7642b0af14f3f5725c7b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055990"
---
# <a name="aspnet-core-no-locblazor-logging"></a><span data-ttu-id="22548-103">:::no-loc(Blazor):::Günlüğe kaydetme ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="22548-103">ASP.NET Core :::no-loc(Blazor)::: logging</span></span>

## :::no-loc(Blazor WebAssembly):::

<span data-ttu-id="22548-104">Uygulamalarda oturum açma :::no-loc(Blazor WebAssembly)::: `WebAssemblyHostBuilder.Logging` özelliğini içindeki özelliği kullanarak yapılandırın `Program.Main` :</span><span class="sxs-lookup"><span data-stu-id="22548-104">Configure logging in :::no-loc(Blazor WebAssembly)::: apps with the `WebAssemblyHostBuilder.Logging` property in `Program.Main`:</span></span>

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

<span data-ttu-id="22548-105">`Logging`Özelliği türündedir <xref:Microsoft.Extensions.Logging.ILoggingBuilder> , bu nedenle tüm uzantı yöntemleri <xref:Microsoft.Extensions.Logging.ILoggingBuilder> üzerinde de kullanılabilir `Logging` .</span><span class="sxs-lookup"><span data-stu-id="22548-105">The `Logging` property is of type <xref:Microsoft.Extensions.Logging.ILoggingBuilder>, so all of the extension methods available on <xref:Microsoft.Extensions.Logging.ILoggingBuilder> are also available on `Logging`.</span></span>

<span data-ttu-id="22548-106">Günlüğe kaydetme yapılandırması, uygulama ayarları dosyalarından yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="22548-106">Logging configuration can be loaded from app settings files.</span></span> <span data-ttu-id="22548-107">Daha fazla bilgi için bkz. <xref:blazor/fundamentals/configuration#logging-configuration>.</span><span class="sxs-lookup"><span data-stu-id="22548-107">For more information, see <xref:blazor/fundamentals/configuration#logging-configuration>.</span></span>

## :::no-loc(Blazor Server):::

<span data-ttu-id="22548-108">Genel ASP.NET Core günlük Kılavuzu için bkz <xref:fundamentals/logging/index> ..</span><span class="sxs-lookup"><span data-stu-id="22548-108">For general ASP.NET Core logging guidance, see <xref:fundamentals/logging/index>.</span></span>

## <a name="no-locblazor-webassembly-no-locsignalr-net-client-logging"></a><span data-ttu-id="22548-109">:::no-loc(Blazor WebAssembly)::::::no-loc(SignalR):::.NET istemci günlüğü</span><span class="sxs-lookup"><span data-stu-id="22548-109">:::no-loc(Blazor WebAssembly)::: :::no-loc(SignalR)::: .NET client logging</span></span>

<span data-ttu-id="22548-110">Öğesine <xref:Microsoft.Extensions.Logging.ILoggerProvider> `WebAssemblyConsoleLogger` geçirilen günlüğe kaydetme sağlayıcılarına eklemek için bir ekleyin <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder> .</span><span class="sxs-lookup"><span data-stu-id="22548-110">Inject an <xref:Microsoft.Extensions.Logging.ILoggerProvider> to add a `WebAssemblyConsoleLogger` to the logging providers passed to <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.Client.HubConnectionBuilder>.</span></span> <span data-ttu-id="22548-111">Geleneksel olarak <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> , `WebAssemblyConsoleLogger` tarayıcıya özgü günlüğe kaydetme API 'leri (örneğin,) etrafında bir sarmalayıcı olur `console.log` .</span><span class="sxs-lookup"><span data-stu-id="22548-111">Unlike a traditional <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger>, `WebAssemblyConsoleLogger` is a wrapper around browser-specific logging APIs (for example, `console.log`).</span></span> <span data-ttu-id="22548-112">' Nin kullanımı, `WebAssemblyConsoleLogger` bir tarayıcı bağlamı Içinde mono içinde mümkün olan bir kayıt oluşturur.</span><span class="sxs-lookup"><span data-stu-id="22548-112">Use of `WebAssemblyConsoleLogger` makes logging possible within Mono inside a browser context.</span></span>

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-no-locrazor-components"></a><span data-ttu-id="22548-113">:::no-loc(Razor):::Bileşenlerdeki oturum açma</span><span class="sxs-lookup"><span data-stu-id="22548-113">Log in :::no-loc(Razor)::: components</span></span>

<span data-ttu-id="22548-114">Uygulama başlangıç yapılandırmasına göre günlüğe kaydetme defterleri.</span><span class="sxs-lookup"><span data-stu-id="22548-114">Loggers respect app startup configuration.</span></span>

<span data-ttu-id="22548-115">`using`İçin olan yönergesi <xref:Microsoft.Extensions.Logging> , ve gibi API 'Leri için IntelliSense 'i desteklemek için gereklidir <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> .</span><span class="sxs-lookup"><span data-stu-id="22548-115">The `using` directive for <xref:Microsoft.Extensions.Logging> is required to support Intellisense completions for APIs, such as <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> and <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A>.</span></span>

<span data-ttu-id="22548-116">Aşağıdaki örnek, bileşenleri içindeki bir günlüğü <xref:Microsoft.Extensions.Logging.ILogger> gösterir :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="22548-116">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILogger> in :::no-loc(Razor)::: components:</span></span>

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

<span data-ttu-id="22548-117">Aşağıdaki örnek, bileşenleri içindeki bir günlüğü <xref:Microsoft.Extensions.Logging.ILoggerFactory> gösterir :::no-loc(Razor)::: :</span><span class="sxs-lookup"><span data-stu-id="22548-117">The following example demonstrates logging with an <xref:Microsoft.Extensions.Logging.ILoggerFactory> in :::no-loc(Razor)::: components:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="22548-118">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="22548-118">Additional resources</span></span>

* <xref:fundamentals/logging/index>
