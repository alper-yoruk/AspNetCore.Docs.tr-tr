---
title: Microsoft. Extensions. Logging 2,2 2,1 veya 3,0 ' den geçiş yapın
author: pakrym
description: 2,1 ' den 2,2 ' e veya 3,0 ' de Microsoft. Extensions. Logging kullanarak bir non-ASP.NET Core uygulamasını geçirmeyi öğrenin.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 2fd738ed0e0a06d0793e3c624d40a13725b53cd8
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84274238"
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
