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
# <a name="aspnet-core-no-locblazor-logging"></a>BlazorGünlüğe kaydetme ASP.NET Core

::: zone pivot="webassembly"

Blazor WebAssemblyÖzelliği ile uygulamalarda özel günlük kaydını yapılandırın <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.WebAssemblyHostBuilder.Logging?displayProperty=nameWithType> .

Ad alanını şu <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting?displayProperty=fullName> şekilde ekleyin `Program.cs` :

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;
```

' `Program.Main` De `Program.cs` , en düşük günlük kaydı düzeyini ile ayarlayın <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.SetMinimumLevel%2A?displayProperty=nameWithType> ve özel günlüğe kaydetme sağlayıcısını ekleyin:

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
...
builder.Logging.SetMinimumLevel(LogLevel.Debug);
builder.Logging.AddProvider(new CustomLoggingProvider());
```

`Logging`Özelliği türündedir <xref:Microsoft.Extensions.Logging.ILoggingBuilder> , bu nedenle tüm uzantı yöntemleri <xref:Microsoft.Extensions.Logging.ILoggingBuilder> üzerinde de kullanılabilir `Logging` .

Günlüğe kaydetme yapılandırması, uygulama ayarları dosyalarından yüklenebilir. Daha fazla bilgi için bkz. <xref:blazor/fundamentals/configuration#logging-configuration>.

## <a name="no-locsignalr-net-client-logging"></a>SignalR .NET istemci günlüğü

Öğesine <xref:Microsoft.Extensions.Logging.ILoggerProvider> `WebAssemblyConsoleLogger` geçirilen günlüğe kaydetme sağlayıcılarına eklemek için bir ekleyin <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> . Geleneksel olarak <xref:Microsoft.Extensions.Logging.Console.ConsoleLogger> , `WebAssemblyConsoleLogger` tarayıcıya özgü günlüğe kaydetme API 'leri (örneğin,) etrafında bir sarmalayıcı olur `console.log` . ' Nin kullanımı, `WebAssemblyConsoleLogger` bir tarayıcı bağlamı Içinde mono içinde mümkün olan bir kayıt oluşturur.

İçin ad alanını ekleyin <xref:Microsoft.Extensions.Logging?displayProperty=fullName> ve bileşene ekleyin <xref:Microsoft.Extensions.Logging.ILoggerProvider> :

```csharp
@using Microsoft.Extensions.Logging
@inject ILoggerProvider LoggerProvider
```

Bileşenin [ `OnInitializedAsync` yönteminde](xref:blazor/components/lifecycle#component-initialization-methods), şunu kullanın <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilderExtensions.ConfigureLogging%2A?displayProperty=nameWithType> :

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl(NavigationManager.ToAbsoluteUri("/chathub"))
    .ConfigureLogging(logging => logging.AddProvider(LoggerProvider))
    .Build();
```

::: zone-end

::: zone pivot="server"

İle ilgili genel ASP.NET Core günlük Kılavuzu için Blazor Server bkz <xref:fundamentals/logging/index> ..

::: zone-end

## <a name="log-in-no-locrazor-components"></a>RazorBileşenlerdeki oturum açma

Uygulama başlangıç yapılandırmasına göre günlüğe kaydetme defterleri.

`using`İçin olan yönergesi <xref:Microsoft.Extensions.Logging> , ve gibi API 'Leri için [IntelliSense](/visualstudio/ide/using-intellisense) 'i desteklemek için gereklidir <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> .

Aşağıdaki örnekte, bileşenleri içindeki bir günlük kaydı gösterilmektedir <xref:Microsoft.Extensions.Logging.ILogger> .

`Pages/Counter.razor`:

[!code-razor[](logging/samples_snapshot/Counter1.razor?highlight=3,16)]

Aşağıdaki örnekte, bileşenleri içindeki bir günlük kaydı gösterilmektedir <xref:Microsoft.Extensions.Logging.ILoggerFactory> .

`Pages/Counter.razor`:

[!code-razor[](logging/samples_snapshot/Counter2.razor?highlight=3,16-17)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/logging/index>
