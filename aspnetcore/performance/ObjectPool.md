---
title: ASP.NET Core içinde ObjectPool ile nesne yeniden kullanımı
author: rick-anderson
description: ObjectPool kullanan ASP.NET Core uygulamalarında performansı artırmaya yönelik ipuçları.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/ObjectPool
ms.openlocfilehash: f29d15fc1e2d2ad84526598be14638110f08614e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774788"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="35b7c-103">ASP.NET Core içinde ObjectPool ile nesne yeniden kullanımı</span><span class="sxs-lookup"><span data-stu-id="35b7c-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="35b7c-104">, [Steve Gordon](https://twitter.com/stevejgordon), [Ryan şimdi ak](https://github.com/rynowak)ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="35b7c-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="35b7c-105"><xref:Microsoft.Extensions.ObjectPool>, nesnelerin çöp toplanmasına izin vermek yerine, bir nesne grubunu yeniden kullanım için bellekte tutmayı destekleyen ASP.NET Core altyapısının bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="35b7c-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="35b7c-106">Yönetilebilecek nesneler şunları içeriyorsa, nesne havuzunu kullanmak isteyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="35b7c-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="35b7c-107">Ayırma/başlatma pahalıdır.</span><span class="sxs-lookup"><span data-stu-id="35b7c-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="35b7c-108">Sınırlı bir kaynağı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="35b7c-108">Represent some limited resource.</span></span>
- <span data-ttu-id="35b7c-109">Tahmin edilebilir ve sık kullanılır.</span><span class="sxs-lookup"><span data-stu-id="35b7c-109">Used predictably and frequently.</span></span>

<span data-ttu-id="35b7c-110">Örneğin, ASP.NET Core Framework örnekleri yeniden kullanmak <xref:System.Text.StringBuilder> için bazı yerlerde nesne havuzunu kullanır.</span><span class="sxs-lookup"><span data-stu-id="35b7c-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="35b7c-111">`StringBuilder`karakter verilerini tutmak için kendi arabelleğini ayırır ve yönetir.</span><span class="sxs-lookup"><span data-stu-id="35b7c-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="35b7c-112">ASP.NET Core özellikler uygulamak `StringBuilder` için düzenli olarak kullanımlar ve bunları yeniden kullanmak bir performans avantajı sağlar.</span><span class="sxs-lookup"><span data-stu-id="35b7c-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="35b7c-113">Nesne havuzu her zaman performansı iyileştirmez:</span><span class="sxs-lookup"><span data-stu-id="35b7c-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="35b7c-114">Bir nesnenin başlatma maliyeti yüksek değilse, havuzdan nesneyi almak genellikle daha yavaştır.</span><span class="sxs-lookup"><span data-stu-id="35b7c-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="35b7c-115">Havuz tarafından yönetilen nesneler, havuz serbest olarak ayrılana kadar ayrılmış değildir.</span><span class="sxs-lookup"><span data-stu-id="35b7c-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="35b7c-116">Yalnızca uygulamanız veya kitaplığınız için gerçekçi senaryolar kullanarak performans verilerini topladıktan sonra nesne havuzunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="35b7c-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

<span data-ttu-id="35b7c-117">**Uyarı: `ObjectPool` uygulamaz `IDisposable`. Bunu, aktiften çıkarma gerektiren türlerle kullanmanızı önermiyoruz.**</span><span class="sxs-lookup"><span data-stu-id="35b7c-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span>

<span data-ttu-id="35b7c-118">**UNUTMAYıN: ObjectPool, ayırabilecek nesne sayısına bir sınır yerleştirmez, saklanacak nesne sayısına bir sınır koyar.**</span><span class="sxs-lookup"><span data-stu-id="35b7c-118">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="35b7c-119">Kavramlar</span><span class="sxs-lookup"><span data-stu-id="35b7c-119">Concepts</span></span>

<span data-ttu-id="35b7c-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1>-temel nesne havuzu soyutlama.</span><span class="sxs-lookup"><span data-stu-id="35b7c-120"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="35b7c-121">Nesneleri almak ve döndürmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="35b7c-121">Used to get and return objects.</span></span>

<span data-ttu-id="35b7c-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601>-bir nesnenin nasıl oluşturulduğunu ve havuza döndürüldüğünde nasıl *sıfırlandığını* özelleştirmek için bunu uygulayın.</span><span class="sxs-lookup"><span data-stu-id="35b7c-122"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="35b7c-123">Bu, doğrudan oluşturduğunuz bir nesne havuzuna geçirilebilir.... VEYA</span><span class="sxs-lookup"><span data-stu-id="35b7c-123">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="35b7c-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*>nesne havuzları oluşturmak için bir fabrika işlevi görür.</span><span class="sxs-lookup"><span data-stu-id="35b7c-124"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="35b7c-125">ObjectPool bir uygulamada birden çok şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="35b7c-125">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="35b7c-126">Havuz örneği oluşturuluyor.</span><span class="sxs-lookup"><span data-stu-id="35b7c-126">Instantiating a pool.</span></span>
* <span data-ttu-id="35b7c-127">Bir havuzu bir örnek olarak [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) içinde kaydetme.</span><span class="sxs-lookup"><span data-stu-id="35b7c-127">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="35b7c-128">, `ObjectPoolProvider<>` Ve ' a kayıt yaptırın ve fabrika olarak kullanılıyor.</span><span class="sxs-lookup"><span data-stu-id="35b7c-128">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="35b7c-129">ObjectPool kullanma</span><span class="sxs-lookup"><span data-stu-id="35b7c-129">How to use ObjectPool</span></span>

<span data-ttu-id="35b7c-130">Bir <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> nesne almak ve <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> nesneyi döndürmek için çağırın.</span><span class="sxs-lookup"><span data-stu-id="35b7c-130">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="35b7c-131">Her nesneyi döndürmenizde gereksinim yoktur.</span><span class="sxs-lookup"><span data-stu-id="35b7c-131">There's no requirement that you return every object.</span></span> <span data-ttu-id="35b7c-132">Bir nesne döndürmezseniz atık olarak toplanacaktır.</span><span class="sxs-lookup"><span data-stu-id="35b7c-132">If you don't return an object, it will be garbage collected.</span></span>

## <a name="objectpool-sample"></a><span data-ttu-id="35b7c-133">ObjectPool örneği</span><span class="sxs-lookup"><span data-stu-id="35b7c-133">ObjectPool sample</span></span>

<span data-ttu-id="35b7c-134">Aşağıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="35b7c-134">The following code:</span></span>

* <span data-ttu-id="35b7c-135">`ObjectPoolProvider` [Bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="35b7c-135">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="35b7c-136">Dı kapsayıcısına ekler `ObjectPool<StringBuilder>` ve yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="35b7c-136">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="35b7c-137">Öğesini ekler `BirthdayMiddleware`.</span><span class="sxs-lookup"><span data-stu-id="35b7c-137">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="35b7c-138">Aşağıdaki kod şunu uygular`BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="35b7c-138">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]
