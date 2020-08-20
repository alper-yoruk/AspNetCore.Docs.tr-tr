---
title: BlazorGünlüğe kaydetme ASP.NET Core
author: guardrex
description: BlazorGünlük düzeyi yapılandırma ve bileşenlerden günlük iletilerini yazma dahil olmak üzere uygulamalarda oturum açma hakkında bilgi edinin Razor .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
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
ms.openlocfilehash: 79a77a7e7f5c6c5ecb09ffa276ec1d0a3103e6d6
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626512"
---
# <a name="aspnet-core-no-locblazor-logging"></a>BlazorGünlüğe kaydetme ASP.NET Core

## Blazor WebAssembly

Uygulamalarda oturum açma Blazor WebAssembly `WebAssemblyHostBuilder.Logging` özelliğini içindeki özelliği kullanarak yapılandırın `Program.Main` :

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

...

var builder = WebAssemblyHostBuilder.CreateDefault(args);

builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

`Logging`Özelliği türündedir <xref:Microsoft.Extensions.Logging.ILoggingBuilder> , bu nedenle tüm uzantı yöntemleri <xref:Microsoft.Extensions.Logging.ILoggingBuilder> üzerinde de kullanılabilir `Logging` .

Günlüğe kaydetme yapılandırması, uygulama ayarları dosyalarından yüklenebilir. Daha fazla bilgi için bkz. <xref:blazor/fundamentals/configuration#logging-configuration>.

## Blazor Server

Genel ASP.NET Core günlük Kılavuzu için bkz <xref:fundamentals/logging/index> ..

## <a name="no-locblazor-webassembly-no-locsignalr-net-client-logging"></a>Blazor WebAssemblySignalR.NET istemci günlüğü

Öğesine <xref:Microsoft.Extensions.Logging.ILoggerProvider> `WebAssemblyConsoleLogger` geçirilen günlüğe kaydetme sağlayıcılarına eklemek için bir ekleyin <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> . Geleneksel olarak <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> , `WebAssemblyConsoleLogger` tarayıcıya özgü günlüğe kaydetme API 'leri (örneğin,) etrafında bir sarmalayıcı olur `console.log` . ' Nin kullanımı, `WebAssemblyConsoleLogger` bir tarayıcı bağlamı Içinde mono içinde mümkün olan bir kayıt oluşturur.

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider

...

var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

## <a name="log-in-no-locrazor-components"></a>RazorBileşenlerdeki oturum açma

Uygulama başlangıç yapılandırmasına göre günlüğe kaydetme defterleri.

`using`İçin olan yönergesi <xref:Microsoft.Extensions.Logging> , ve gibi API 'Leri için IntelliSense 'i desteklemek için gereklidir <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> .

Aşağıdaki örnek, bileşenleri içindeki bir günlüğü <xref:Microsoft.Extensions.Logging.ILogger> gösterir Razor :

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

Aşağıdaki örnek, bileşenleri içindeki bir günlüğü <xref:Microsoft.Extensions.Logging.ILoggerFactory> gösterir Razor :

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

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/logging/index>
