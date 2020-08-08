---
title: Microsoft. Extensions. Logging 2,2 2,1 veya 3,0 ' den geçiş yapın
author: pakrym
description: 2,1 ' den 2,2 ' e veya 3,0 ' de Microsoft. Extensions. Logging kullanarak bir non-ASP.NET Core uygulamasını geçirmeyi öğrenin.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: edb1c6456d0cbbac57d739f61b4c159f146e4f7e
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015120"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a>Microsoft. Extensions. Logging 2,2 2,1 veya 3,0 ' den geçiş yapın

Bu makalede, `Microsoft.Extensions.Logging` 2,1 ' den 2,2 ' e veya 3,0 ' ye kadar kullanan bir non-ASP.NET Core uygulamasını geçirmeye yönelik yaygın adımlar özetlenmektedir.

## <a name="21-to-22"></a>2.1’den 2.2’ye

El ile oluşturun `ServiceCollection` ve çağırın `AddLogging` .

2,1 örnek:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

2,2 örnek:

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a>2,1 3,0

3,0 ' de kullanın `LoggingFactory.Create` .

2,1 örnek:

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

3,0 örnek:

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a>Ek kaynaklar

* [Microsoft. Extensions. Logging. Console NuGet paketi](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).
* <xref:fundamentals/logging/index>
