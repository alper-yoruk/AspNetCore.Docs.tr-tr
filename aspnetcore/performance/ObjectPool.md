---
title: ASP.NET Core içinde ObjectPool ile nesne yeniden kullanımı
author: rick-anderson
description: ObjectPool kullanan ASP.NET Core uygulamalarında performansı artırmaya yönelik ipuçları.
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
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
uid: performance/ObjectPool
ms.openlocfilehash: 3bbfde6f68a238131149e67ce72f0ddc68a9ea0f
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93056913"
---
# <a name="object-reuse-with-objectpool-in-aspnet-core"></a><span data-ttu-id="c678c-103">ASP.NET Core içinde ObjectPool ile nesne yeniden kullanımı</span><span class="sxs-lookup"><span data-stu-id="c678c-103">Object reuse with ObjectPool in ASP.NET Core</span></span>

<span data-ttu-id="c678c-104">[Steve Gordon](https://twitter.com/stevejgordon), [Ryan şimdi ak](https://github.com/rynowak)ve [Günther fodl](https://github.com/gfoidl)</span><span class="sxs-lookup"><span data-stu-id="c678c-104">By [Steve Gordon](https://twitter.com/stevejgordon), [Ryan Nowak](https://github.com/rynowak), and [Günther Foidl](https://github.com/gfoidl)</span></span>

<span data-ttu-id="c678c-105"><xref:Microsoft.Extensions.ObjectPool> , nesnelerin çöp toplanmasına izin vermek yerine, bir nesne grubunu yeniden kullanım için bellekte tutmayı destekleyen ASP.NET Core altyapısının bir parçasıdır.</span><span class="sxs-lookup"><span data-stu-id="c678c-105"><xref:Microsoft.Extensions.ObjectPool> is part of the ASP.NET Core infrastructure that supports keeping a group of objects in memory for reuse rather than allowing the objects to be garbage collected.</span></span>

<span data-ttu-id="c678c-106">Yönetilebilecek nesneler şunları içeriyorsa, nesne havuzunu kullanmak isteyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="c678c-106">You might want to use the object pool if the objects that are being managed are:</span></span>

- <span data-ttu-id="c678c-107">Ayırma/başlatma pahalıdır.</span><span class="sxs-lookup"><span data-stu-id="c678c-107">Expensive to allocate/initialize.</span></span>
- <span data-ttu-id="c678c-108">Sınırlı bir kaynağı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="c678c-108">Represent some limited resource.</span></span>
- <span data-ttu-id="c678c-109">Tahmin edilebilir ve sık kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c678c-109">Used predictably and frequently.</span></span>

<span data-ttu-id="c678c-110">Örneğin, ASP.NET Core Framework örnekleri yeniden kullanmak için bazı yerlerde nesne havuzunu kullanır <xref:System.Text.StringBuilder> .</span><span class="sxs-lookup"><span data-stu-id="c678c-110">For example, the ASP.NET Core framework uses the object pool in some places to reuse <xref:System.Text.StringBuilder> instances.</span></span> <span data-ttu-id="c678c-111">`StringBuilder` karakter verilerini tutmak için kendi arabelleğini ayırır ve yönetir.</span><span class="sxs-lookup"><span data-stu-id="c678c-111">`StringBuilder` allocates and manages its own buffers to hold character data.</span></span> <span data-ttu-id="c678c-112">ASP.NET Core `StringBuilder` Özellikler uygulamak için düzenli olarak kullanımlar ve bunları yeniden kullanmak bir performans avantajı sağlar.</span><span class="sxs-lookup"><span data-stu-id="c678c-112">ASP.NET Core regularly uses `StringBuilder` to implement features, and reusing them provides a performance benefit.</span></span>

<span data-ttu-id="c678c-113">Nesne havuzu her zaman performansı iyileştirmez:</span><span class="sxs-lookup"><span data-stu-id="c678c-113">Object pooling doesn't always improve performance:</span></span>

- <span data-ttu-id="c678c-114">Bir nesnenin başlatma maliyeti yüksek değilse, havuzdan nesneyi almak genellikle daha yavaştır.</span><span class="sxs-lookup"><span data-stu-id="c678c-114">Unless the initialization cost of an object is high, it's usually slower to get the object from the pool.</span></span>
- <span data-ttu-id="c678c-115">Havuz tarafından yönetilen nesneler, havuz serbest olarak ayrılana kadar ayrılmış değildir.</span><span class="sxs-lookup"><span data-stu-id="c678c-115">Objects managed by the pool aren't de-allocated until the pool is de-allocated.</span></span>

<span data-ttu-id="c678c-116">Yalnızca uygulamanız veya kitaplığınız için gerçekçi senaryolar kullanarak performans verilerini topladıktan sonra nesne havuzunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="c678c-116">Use object pooling only after collecting performance data using realistic scenarios for your app or library.</span></span>

::: moniker range="< aspnetcore-3.0"
<span data-ttu-id="c678c-117">**Uyarı: `ObjectPool` uygulamaz `IDisposable` . Bunu, aktiften çıkarma gerektiren türlerle kullanmanızı önermiyoruz.**</span><span class="sxs-lookup"><span data-stu-id="c678c-117">**WARNING: The `ObjectPool` doesn't implement `IDisposable`. We don't recommend using it with types that need disposal.**</span></span> <span data-ttu-id="c678c-118">`ObjectPool` ASP.NET Core 3,0 ve üzeri sürümlerde desteklenir `IDisposable` .</span><span class="sxs-lookup"><span data-stu-id="c678c-118">`ObjectPool` in ASP.NET Core 3.0 and later supports `IDisposable`.</span></span>
::: moniker-end

<span data-ttu-id="c678c-119">**UNUTMAYıN: ObjectPool, ayırabilecek nesne sayısına bir sınır yerleştirmez, saklanacak nesne sayısına bir sınır koyar.**</span><span class="sxs-lookup"><span data-stu-id="c678c-119">**NOTE: The ObjectPool doesn't place a limit on the number of objects that it will allocate, it places a limit on the number of objects it will retain.**</span></span>

## <a name="concepts"></a><span data-ttu-id="c678c-120">Kavramlar</span><span class="sxs-lookup"><span data-stu-id="c678c-120">Concepts</span></span>

<span data-ttu-id="c678c-121"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> -temel nesne havuzu soyutlama.</span><span class="sxs-lookup"><span data-stu-id="c678c-121"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1> - the basic object pool abstraction.</span></span> <span data-ttu-id="c678c-122">Nesneleri almak ve döndürmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c678c-122">Used to get and return objects.</span></span>

<span data-ttu-id="c678c-123"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> -bir nesnenin nasıl oluşturulduğunu ve havuza döndürüldüğünde nasıl *sıfırlandığını* özelleştirmek için bunu uygulayın.</span><span class="sxs-lookup"><span data-stu-id="c678c-123"><xref:Microsoft.Extensions.ObjectPool.PooledObjectPolicy%601> - implement this to customize how an object is created and how it is *reset* when returned to the pool.</span></span> <span data-ttu-id="c678c-124">Bu, doğrudan oluşturduğunuz bir nesne havuzuna geçirilebilir.... VEYA</span><span class="sxs-lookup"><span data-stu-id="c678c-124">This can be passed into an object pool that you construct directly.... OR</span></span>

<span data-ttu-id="c678c-125"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> nesne havuzları oluşturmak için bir fabrika işlevi görür.</span><span class="sxs-lookup"><span data-stu-id="c678c-125"><xref:Microsoft.Extensions.ObjectPool.ObjectPoolProvider.Create*> acts as a factory for creating object pools.</span></span>
<!-- REview, there is no ObjectPoolProvider<T> -->

<span data-ttu-id="c678c-126">ObjectPool bir uygulamada birden çok şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="c678c-126">The ObjectPool can be used in an app in multiple ways:</span></span>

* <span data-ttu-id="c678c-127">Havuz örneği oluşturuluyor.</span><span class="sxs-lookup"><span data-stu-id="c678c-127">Instantiating a pool.</span></span>
* <span data-ttu-id="c678c-128">Bir havuzu bir örnek olarak [bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) içinde kaydetme.</span><span class="sxs-lookup"><span data-stu-id="c678c-128">Registering a pool in [Dependency injection](xref:fundamentals/dependency-injection) (DI) as an instance.</span></span>
* <span data-ttu-id="c678c-129">, `ObjectPoolProvider<>` Ve ' a kayıt yaptırın ve fabrika olarak kullanılıyor.</span><span class="sxs-lookup"><span data-stu-id="c678c-129">Registering the `ObjectPoolProvider<>` in DI and using it as a factory.</span></span>

## <a name="how-to-use-objectpool"></a><span data-ttu-id="c678c-130">ObjectPool kullanma</span><span class="sxs-lookup"><span data-stu-id="c678c-130">How to use ObjectPool</span></span>

<span data-ttu-id="c678c-131"><xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*>Bir nesne almak ve <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> nesneyi döndürmek için çağırın.</span><span class="sxs-lookup"><span data-stu-id="c678c-131">Call <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Get*> to get an object and <xref:Microsoft.Extensions.ObjectPool.ObjectPool`1.Return*> to return the object.</span></span>  <span data-ttu-id="c678c-132">Her nesneyi döndürmenizde gereksinim yoktur.</span><span class="sxs-lookup"><span data-stu-id="c678c-132">There's no requirement that you return every object.</span></span> <span data-ttu-id="c678c-133">Bir nesne döndürmezseniz atık olarak toplanacaktır.</span><span class="sxs-lookup"><span data-stu-id="c678c-133">If you don't return an object, it will be garbage collected.</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="c678c-134">Kullanıldığında <xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider> ve `T` `IDisposable` şunları uygular:</span><span class="sxs-lookup"><span data-stu-id="c678c-134">When <xref:Microsoft.Extensions.ObjectPool.DefaultObjectPoolProvider> is used and `T` implements `IDisposable`:</span></span>

* <span data-ttu-id="c678c-135">Havuza geri döndürülen \* **olmayan** öğeler silinir.</span><span class="sxs-lookup"><span data-stu-id="c678c-135">Items that are \* **not** _ returned to the pool will be disposed.</span></span>
<span data-ttu-id="c678c-136">_ Havuz DI tarafından atıldığında, havuzdaki tüm öğeler silinir.</span><span class="sxs-lookup"><span data-stu-id="c678c-136">_ When the pool gets disposed by DI, all items in the pool are disposed.</span></span>

<span data-ttu-id="c678c-137">NOTE: havuz atıldıktan sonra:</span><span class="sxs-lookup"><span data-stu-id="c678c-137">NOTE: After the pool is disposed:</span></span>

* <span data-ttu-id="c678c-138">Çağırma `Get` a oluşturur `ObjectDisposedException` .</span><span class="sxs-lookup"><span data-stu-id="c678c-138">Calling `Get` throws a `ObjectDisposedException`.</span></span>
* <span data-ttu-id="c678c-139">`return` verilen öğeyi atar.</span><span class="sxs-lookup"><span data-stu-id="c678c-139">`return` disposes the given item.</span></span>

::: moniker-end

## <a name="objectpool-sample"></a><span data-ttu-id="c678c-140">ObjectPool örneği</span><span class="sxs-lookup"><span data-stu-id="c678c-140">ObjectPool sample</span></span>

<span data-ttu-id="c678c-141">Aşağıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="c678c-141">The following code:</span></span>

* <span data-ttu-id="c678c-142">`ObjectPoolProvider` [Bağımlılık ekleme](xref:fundamentals/dependency-injection) (dı) kapsayıcısına ekler.</span><span class="sxs-lookup"><span data-stu-id="c678c-142">Adds `ObjectPoolProvider` to the [Dependency injection](xref:fundamentals/dependency-injection) (DI) container.</span></span>
* <span data-ttu-id="c678c-143">Dı kapsayıcısına ekler ve yapılandırır `ObjectPool<StringBuilder>` .</span><span class="sxs-lookup"><span data-stu-id="c678c-143">Adds and configures `ObjectPool<StringBuilder>` to the DI container.</span></span>
* <span data-ttu-id="c678c-144">Öğesini ekler `BirthdayMiddleware` .</span><span class="sxs-lookup"><span data-stu-id="c678c-144">Adds the `BirthdayMiddleware`.</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/Startup.cs?name=snippet)]

<span data-ttu-id="c678c-145">Aşağıdaki kod şunu uygular `BirthdayMiddleware`</span><span class="sxs-lookup"><span data-stu-id="c678c-145">The following code implements `BirthdayMiddleware`</span></span>

[!code-csharp[](ObjectPool/ObjectPoolSample/BirthdayMiddleware.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]
