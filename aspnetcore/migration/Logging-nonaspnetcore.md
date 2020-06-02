---
<span data-ttu-id="04385-101">Başlık: Microsoft. Extensions 'dan geçiş yapın. Logging 2,1, 2,2 veya 3,0 Author: pakronym Description: Microsoft. Extensions. 2,1 ' ten 2,2 veya 3,0 ' a günlük kaydı kullanan bir non-ASP.NET Core uygulamasını geçirmeyi öğrenin.</span><span class="sxs-lookup"><span data-stu-id="04385-101">title: Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0 author: pakrym description: Learn how to migrate a non-ASP.NET Core application that uses Microsoft.Extensions.Logging from 2.1 to 2.2 or 3.0.</span></span>
<span data-ttu-id="04385-102">MS. Author: pakronym MS. Custom: MVC MS. Date: 01/04/2019 No-loc:</span><span class="sxs-lookup"><span data-stu-id="04385-102">ms.author: pakrym ms.custom: mvc ms.date: 01/04/2019 no-loc:</span></span>
- <span data-ttu-id="04385-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="04385-103">'Blazor'</span></span>
- <span data-ttu-id="04385-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="04385-104">'Identity'</span></span>
- <span data-ttu-id="04385-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="04385-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="04385-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="04385-106">'Razor'</span></span>
- <span data-ttu-id="04385-107">' SignalR ' uid: geçiş/günlüğe kaydetme-aspnetcore</span><span class="sxs-lookup"><span data-stu-id="04385-107">'SignalR' uid: migration/logging-nonaspnetcore</span></span>

---

# <a name="migrate-from-microsoftextensionslogging-21-to-22-or-30"></a><span data-ttu-id="04385-108">Microsoft. Extensions. Logging 2,2 2,1 veya 3,0 ' den geçiş yapın</span><span class="sxs-lookup"><span data-stu-id="04385-108">Migrate from Microsoft.Extensions.Logging 2.1 to 2.2 or 3.0</span></span>

<span data-ttu-id="04385-109">Bu makalede, `Microsoft.Extensions.Logging` 2,1 ' den 2,2 ' e veya 3,0 ' ye kadar kullanan bir non-ASP.NET Core uygulamasını geçirmeye yönelik yaygın adımlar özetlenmektedir.</span><span class="sxs-lookup"><span data-stu-id="04385-109">This article outlines the common steps for migrating a non-ASP.NET Core application that uses `Microsoft.Extensions.Logging` from 2.1 to 2.2 or 3.0.</span></span>

## <a name="21-to-22"></a><span data-ttu-id="04385-110">2.1’den 2.2’ye</span><span class="sxs-lookup"><span data-stu-id="04385-110">2.1 to 2.2</span></span>

<span data-ttu-id="04385-111">El ile oluşturun `ServiceCollection` ve çağırın `AddLogging` .</span><span class="sxs-lookup"><span data-stu-id="04385-111">Manually create `ServiceCollection` and call `AddLogging`.</span></span>

<span data-ttu-id="04385-112">2,1 örnek:</span><span class="sxs-lookup"><span data-stu-id="04385-112">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="04385-113">2,2 örnek:</span><span class="sxs-lookup"><span data-stu-id="04385-113">2.2 example:</span></span>

```csharp
var serviceCollection = new ServiceCollection();
serviceCollection.AddLogging(builder => builder.AddConsole());

using (var serviceProvider = serviceCollection.BuildServiceProvider())
using (var loggerFactory = serviceProvider.GetService<ILoggerFactory>())
{
    // use loggerFactory
}
```

## <a name="21-to-30"></a><span data-ttu-id="04385-114">2,1 3,0</span><span class="sxs-lookup"><span data-stu-id="04385-114">2.1 to 3.0</span></span>

<span data-ttu-id="04385-115">3,0 ' de kullanın `LoggingFactory.Create` .</span><span class="sxs-lookup"><span data-stu-id="04385-115">In 3.0, use `LoggingFactory.Create`.</span></span>

<span data-ttu-id="04385-116">2,1 örnek:</span><span class="sxs-lookup"><span data-stu-id="04385-116">2.1 example:</span></span>

```csharp
using (var loggerFactory = new LoggerFactory())
{
    loggerFactory.AddConsole();

    // use loggerFactory
}
```

<span data-ttu-id="04385-117">3,0 örnek:</span><span class="sxs-lookup"><span data-stu-id="04385-117">3.0 example:</span></span>

```csharp
using (var loggerFactory = LoggerFactory.Create(builder => builder.AddConsole()))
{
    // use loggerFactory
}
```

## <a name="additional-resources"></a><span data-ttu-id="04385-118">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="04385-118">Additional resources</span></span>

* <span data-ttu-id="04385-119">[Microsoft. Extensions. Logging. Console NuGet paketi](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span><span class="sxs-lookup"><span data-stu-id="04385-119">[Microsoft.Extensions.Logging.Console NuGet package](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/).</span></span>
* <xref:fundamentals/logging/index>
