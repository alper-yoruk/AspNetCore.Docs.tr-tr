---
title: Microsoft. Extensions. Logging 2,2 2,1 veya 3,0 ' den geçiş yapın
author: pakrym
description: 2,1 ' den 2,2 ' e veya 3,0 ' de Microsoft. Extensions. Logging kullanarak bir non-ASP.NET Core uygulamasını geçirmeyi öğrenin.
ms.author: pakrym
ms.custom: mvc
ms.date: 01/04/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: migration/logging-nonaspnetcore
ms.openlocfilehash: 1082ae494aae7a31b195b32dfedae81c60fe3eed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060384"
---
# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="e9374-103">Microsoft. Extensions. Logging 2,2 2,1 veya 3,0 ' den geçiş yapın</span><span class="sxs-lookup"><span data-stu-id="e9374-103">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="e9374-104">Bu makalede, `Microsoft.Extensions.Logging` 2,1 ' den 2,2 ' e veya 3,0 ' ye kadar kullanan bir non-ASP.NET Core uygulamasını geçirmeye yönelik yaygın adımlar özetlenmektedir.</span><span class="sxs-lookup"><span data-stu-id="e9374-104">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="e9374-105">2.1’den 2.2’ye</span><span class="sxs-lookup"><span data-stu-id="e9374-105">2.1 to 2.2</span></span>

<span data-ttu-id="e9374-106">El ile oluşturun `ServiceCollection` ve çağırın `AddLogging` .</span><span class="sxs-lookup"><span data-stu-id="e9374-106">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="e9374-107">2,1 örnek:</span><span class="sxs-lookup"><span data-stu-id="e9374-107">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="e9374-108">2,2 örnek:</span><span class="sxs-lookup"><span data-stu-id="e9374-108">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="e9374-109">2,1 3,0</span><span class="sxs-lookup"><span data-stu-id="e9374-109">2.1 to 3.0</span></span>

<span data-ttu-id="e9374-110">3,0 ' de kullanın `LoggingFactory.Create` .</span><span class="sxs-lookup"><span data-stu-id="e9374-110">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="e9374-111">2,1 örnek:</span><span class="sxs-lookup"><span data-stu-id="e9374-111">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="e9374-112">3,0 örnek:</span><span class="sxs-lookup"><span data-stu-id="e9374-112">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="e9374-113">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e9374-113">Additional resources</span></span>

* <span data-ttu-id="e9374-114">[Microsoft. Extensions. Logging. Console NuGet paketi](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span><span class="sxs-lookup"><span data-stu-id="e9374-114">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
