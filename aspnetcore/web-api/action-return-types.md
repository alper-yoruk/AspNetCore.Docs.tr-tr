---
title: ASP.NET Core Web API 'sindeki denetleyici eylemi dönüş türleri
author: scottaddie
description: ASP.NET Core Web API 'sindeki çeşitli denetleyici eylemi yöntemi döndürme türlerini kullanma hakkında bilgi edinin.
ms.author: scaddie
ms.custom: mvc
ms.date: 02/03/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/action-return-types
ms.openlocfilehash: 7227a86db9b94cc68851cb1670ce9668148639ef
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404450"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a><span data-ttu-id="3a945-103">ASP.NET Core Web API 'sindeki denetleyici eylemi dönüş türleri</span><span class="sxs-lookup"><span data-stu-id="3a945-103">Controller action return types in ASP.NET Core web API</span></span>

<span data-ttu-id="3a945-104">[Scott Ade](https://github.com/scottaddie) tarafından</span><span class="sxs-lookup"><span data-stu-id="3a945-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="3a945-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="3a945-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="3a945-106">ASP.NET Core Web API denetleyicisi eylem dönüş türleri için aşağıdaki seçenekleri sunar:</span><span class="sxs-lookup"><span data-stu-id="3a945-106">ASP.NET Core offers the following options for web API controller action return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

* [<span data-ttu-id="3a945-107">Belirli tür</span><span class="sxs-lookup"><span data-stu-id="3a945-107">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="3a945-108">Iactionresult</span><span class="sxs-lookup"><span data-stu-id="3a945-108">IActionResult</span></span>](#iactionresult-type)
* [<span data-ttu-id="3a945-109">ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="3a945-109">ActionResult\<T></span></span>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [<span data-ttu-id="3a945-110">Belirli tür</span><span class="sxs-lookup"><span data-stu-id="3a945-110">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="3a945-111">Iactionresult</span><span class="sxs-lookup"><span data-stu-id="3a945-111">IActionResult</span></span>](#iactionresult-type)

::: moniker-end

<span data-ttu-id="3a945-112">Bu belgede, her dönüş türünü kullanmak için en uygun olan bilgiler açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="3a945-112">This document explains when it's most appropriate to use each return type.</span></span>

## <a name="specific-type"></a><span data-ttu-id="3a945-113">Belirli tür</span><span class="sxs-lookup"><span data-stu-id="3a945-113">Specific type</span></span>

<span data-ttu-id="3a945-114">En basit eylem, basit veya karmaşık bir veri türü döndürür (örneğin, `string` veya özel nesne türü).</span><span class="sxs-lookup"><span data-stu-id="3a945-114">The simplest action returns a primitive or complex data type (for example, `string` or a custom object type).</span></span> <span data-ttu-id="3a945-115">Özel nesneler koleksiyonunu döndüren aşağıdaki eylemi göz önünde bulundurun `Product` :</span><span class="sxs-lookup"><span data-stu-id="3a945-115">Consider the following action, which returns a collection of custom `Product` objects:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

<span data-ttu-id="3a945-116">Eylem yürütme sırasında korunmak üzere bilinen koşullar olmadan, belirli bir türü döndürmek yeterli olabilir.</span><span class="sxs-lookup"><span data-stu-id="3a945-116">Without known conditions to safeguard against during action execution, returning a specific type could suffice.</span></span> <span data-ttu-id="3a945-117">Önceki eylem hiçbir parametre kabul etmez, bu nedenle parametre kısıtlamaları doğrulaması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="3a945-117">The preceding action accepts no parameters, so parameter constraints validation isn't needed.</span></span>

<span data-ttu-id="3a945-118">Birden çok dönüş türü mümkün olduğunda, bir <xref:Microsoft.AspNetCore.Mvc.ActionResult> dönüş türünü basit veya karmaşık dönüş türüyle karıştırmak yaygındır.</span><span class="sxs-lookup"><span data-stu-id="3a945-118">When multiple return types are possible, it's common to mix an <xref:Microsoft.AspNetCore.Mvc.ActionResult> return type with the primitive or complex return type.</span></span> <span data-ttu-id="3a945-119">Bu tür eyleme uyum sağlamak için [ıactionresult](#iactionresult-type) veya [ \<T> ActionResult](#actionresultt-type) gereklidir.</span><span class="sxs-lookup"><span data-stu-id="3a945-119">Either [IActionResult](#iactionresult-type) or [ActionResult\<T>](#actionresultt-type) are necessary to accommodate this type of action.</span></span> <span data-ttu-id="3a945-120">Bu belgede birden fazla dönüş türünden birkaç örnek verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="3a945-120">Several samples of multiple return types are provided in this document.</span></span>

### <a name="return-ienumerablet-or-iasyncenumerablet"></a><span data-ttu-id="3a945-121">IEnumerable \<T> veya ıasyncenumerable döndürün\<T></span><span class="sxs-lookup"><span data-stu-id="3a945-121">Return IEnumerable\<T> or IAsyncEnumerable\<T></span></span>

<span data-ttu-id="3a945-122">ASP.NET Core 2,2 ve önceki sürümlerde, <xref:System.Collections.Generic.IEnumerable%601> bir eylemden geri dönmek seri hale getirici tarafından zaman uyumlu koleksiyon yinelemesi ile sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="3a945-122">In ASP.NET Core 2.2 and earlier, returning <xref:System.Collections.Generic.IEnumerable%601> from an action results in synchronous collection iteration by the serializer.</span></span> <span data-ttu-id="3a945-123">Sonuç olarak, çağrı engelleme ve iş parçacığı havuzu için olası bir olasılık vardır.</span><span class="sxs-lookup"><span data-stu-id="3a945-123">The result is the blocking of calls and a potential for thread pool starvation.</span></span> <span data-ttu-id="3a945-124">Göstermek için, Web API 'sinin veri erişimi ihtiyaçları için Entity Framework (EF) Core kullanıldığını düşünün.</span><span class="sxs-lookup"><span data-stu-id="3a945-124">To illustrate, imagine that Entity Framework (EF) Core is being used for the web API's data access needs.</span></span> <span data-ttu-id="3a945-125">Aşağıdaki eylemin dönüş türü serileştirme sırasında zaman uyumlu olarak numaralandırılır:</span><span class="sxs-lookup"><span data-stu-id="3a945-125">The following action's return type is synchronously enumerated during serialization:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="3a945-126">Zaman uyumlu numaralandırmayı önlemek ve engellemeyi ASP.NET Core 2,2 ve önceki sürümlerde veritabanı üzerinde bekleyip engellemek için şunu çağırın `ToListAsync` :</span><span class="sxs-lookup"><span data-stu-id="3a945-126">To avoid synchronous enumeration and blocking waits on the database in ASP.NET Core 2.2 and earlier, invoke `ToListAsync`:</span></span>

```csharp
public async Task<IEnumerable<Product>> GetOnSaleProducts() =>
    await _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

<span data-ttu-id="3a945-127">ASP.NET Core 3,0 ve üzeri sürümlerde, `IAsyncEnumerable<T>` bir eylemden geri dönerek:</span><span class="sxs-lookup"><span data-stu-id="3a945-127">In ASP.NET Core 3.0 and later, returning `IAsyncEnumerable<T>` from an action:</span></span>

* <span data-ttu-id="3a945-128">Zaman uyumlu yineleme ile sonuçlanmayacaktır.</span><span class="sxs-lookup"><span data-stu-id="3a945-128">No longer results in synchronous iteration.</span></span>
* <span data-ttu-id="3a945-129">Dönerek etkin hale gelir <xref:System.Collections.Generic.IEnumerable%601> .</span><span class="sxs-lookup"><span data-stu-id="3a945-129">Becomes as efficient as returning <xref:System.Collections.Generic.IEnumerable%601>.</span></span>

<span data-ttu-id="3a945-130">ASP.NET Core 3,0 ve üzeri, serileştiriciye sağlamadan önce aşağıdaki eylemin sonucunu arabelleğe alır:</span><span class="sxs-lookup"><span data-stu-id="3a945-130">ASP.NET Core 3.0 and later buffers the result of the following action before providing it to the serializer:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="3a945-131">Zaman uyumsuz yinelemeyi güvence altına almak için eylem imzasının dönüş türünü bildirmeyi göz önünde bulundurun `IAsyncEnumerable<T>` .</span><span class="sxs-lookup"><span data-stu-id="3a945-131">Consider declaring the action signature's return type as `IAsyncEnumerable<T>` to guarantee the asynchronous iteration.</span></span> <span data-ttu-id="3a945-132">Sonuç olarak, yineleme modu döndürülmekte olan temel somut türü temel alır.</span><span class="sxs-lookup"><span data-stu-id="3a945-132">Ultimately, the iteration mode is based on the underlying concrete type being returned.</span></span> <span data-ttu-id="3a945-133">MVC, uygulayan tüm somut türleri otomatik olarak arabelleğe alır `IAsyncEnumerable<T>` .</span><span class="sxs-lookup"><span data-stu-id="3a945-133">MVC automatically buffers any concrete type that implements `IAsyncEnumerable<T>`.</span></span>

<span data-ttu-id="3a945-134">Şu şekilde satış fiyatlı ürün kayıtları döndüren aşağıdaki eylemi göz önünde bulundurun `IEnumerable<Product>` :</span><span class="sxs-lookup"><span data-stu-id="3a945-134">Consider the following action, which returns sale-priced product records as `IEnumerable<Product>`:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

<span data-ttu-id="3a945-135">`IAsyncEnumerable<Product>`Önceki eylemin eşdeğeri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="3a945-135">The `IAsyncEnumerable<Product>` equivalent of the preceding action is:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

<span data-ttu-id="3a945-136">Yukarıdaki eylemlerin her ikisi de ASP.NET Core 3,0 itibariyle engellenmeyen bir işlem değildir.</span><span class="sxs-lookup"><span data-stu-id="3a945-136">Both of the preceding actions are non-blocking as of ASP.NET Core 3.0.</span></span>

## <a name="iactionresult-type"></a><span data-ttu-id="3a945-137">Iactionresult türü</span><span class="sxs-lookup"><span data-stu-id="3a945-137">IActionResult type</span></span>

<span data-ttu-id="3a945-138"><xref:Microsoft.AspNetCore.Mvc.IActionResult>Bir eylemde birden çok dönüş türü mümkünse, dönüş türü uygun olur `ActionResult` .</span><span class="sxs-lookup"><span data-stu-id="3a945-138">The <xref:Microsoft.AspNetCore.Mvc.IActionResult> return type is appropriate when multiple `ActionResult` return types are possible in an action.</span></span> <span data-ttu-id="3a945-139">`ActionResult`Türler ÇEŞITLI http durum kodlarını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="3a945-139">The `ActionResult` types represent various HTTP status codes.</span></span> <span data-ttu-id="3a945-140">Herhangi bir soyut olmayan sınıf, `ActionResult` geçerli bir dönüş türü olarak niteleyen ' dan türetiliyor.</span><span class="sxs-lookup"><span data-stu-id="3a945-140">Any non-abstract class deriving from `ActionResult` qualifies as a valid return type.</span></span> <span data-ttu-id="3a945-141">Bu kategorideki bazı yaygın dönüş türleri şunlardır <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) ve <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span><span class="sxs-lookup"><span data-stu-id="3a945-141">Some common return types in this category are <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404), and <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span></span> <span data-ttu-id="3a945-142">Alternatif olarak, sınıftaki kullanışlı yöntemler <xref:Microsoft.AspNetCore.Mvc.ControllerBase> bir eylemden tür döndürmek için kullanılabilir `ActionResult` .</span><span class="sxs-lookup"><span data-stu-id="3a945-142">Alternatively, convenience methods in the <xref:Microsoft.AspNetCore.Mvc.ControllerBase> class can be used to return `ActionResult` types from an action.</span></span> <span data-ttu-id="3a945-143">Örneğin, `return BadRequest();` öğesinin bir toplu biçimidir `return new BadRequestResult();` .</span><span class="sxs-lookup"><span data-stu-id="3a945-143">For example, `return BadRequest();` is a shorthand form of `return new BadRequestResult();`.</span></span>

<span data-ttu-id="3a945-144">Bu tür bir eylemde birden çok dönüş türü ve yolu olduğundan, özniteliğin serbest kullanımı [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) gereklidir.</span><span class="sxs-lookup"><span data-stu-id="3a945-144">Because there are multiple return types and paths in this type of action, liberal use of the [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute is necessary.</span></span> <span data-ttu-id="3a945-145">Bu öznitelik, [Swagger](xref:tutorials/web-api-help-pages-using-swagger)gibi araçlar tarafından oluşturulan Web API Yardım sayfaları için daha açıklayıcı yanıt ayrıntıları üretir.</span><span class="sxs-lookup"><span data-stu-id="3a945-145">This attribute produces more descriptive response details for web API help pages generated by tools like [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="3a945-146">`[ProducesResponseType]`eylem tarafından döndürülecek bilinen türleri ve HTTP durum kodlarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="3a945-146">`[ProducesResponseType]` indicates the known types and HTTP status codes to be returned by the action.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="3a945-147">Zaman uyumlu eylem</span><span class="sxs-lookup"><span data-stu-id="3a945-147">Synchronous action</span></span>

<span data-ttu-id="3a945-148">İki olası dönüş türü olan aşağıdaki zaman uyumlu eylemi göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="3a945-148">Consider the following synchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

<span data-ttu-id="3a945-149">Önceki eylemde:</span><span class="sxs-lookup"><span data-stu-id="3a945-149">In the preceding action:</span></span>

* <span data-ttu-id="3a945-150">Temel alınan veri deposunda tarafından temsil edilen ürün olmadığında 404 durum kodu döndürülür `id` .</span><span class="sxs-lookup"><span data-stu-id="3a945-150">A 404 status code is returned when the product represented by `id` doesn't exist in the underlying data store.</span></span> <span data-ttu-id="3a945-151"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>Kolaylık yöntemi için toplu olarak çağrılır `return new NotFoundResult();` .</span><span class="sxs-lookup"><span data-stu-id="3a945-151">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> convenience method is invoked as shorthand for `return new NotFoundResult();`.</span></span>
* <span data-ttu-id="3a945-152">Ürün mevcut olduğunda nesneyle bir 200 durum kodu döndürülür `Product` .</span><span class="sxs-lookup"><span data-stu-id="3a945-152">A 200 status code is returned with the `Product` object when the product does exist.</span></span> <span data-ttu-id="3a945-153"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*>Kolaylık yöntemi için toplu olarak çağrılır `return new OkObjectResult(product);` .</span><span class="sxs-lookup"><span data-stu-id="3a945-153">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> convenience method is invoked as shorthand for `return new OkObjectResult(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="3a945-154">Zaman uyumsuz eylem</span><span class="sxs-lookup"><span data-stu-id="3a945-154">Asynchronous action</span></span>

<span data-ttu-id="3a945-155">İki olası dönüş türü olan aşağıdaki zaman uyumsuz eylemi göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="3a945-155">Consider the following asynchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

<span data-ttu-id="3a945-156">Önceki eylemde:</span><span class="sxs-lookup"><span data-stu-id="3a945-156">In the preceding action:</span></span>

* <span data-ttu-id="3a945-157">Ürün açıklaması "XYZ pencere öğesi" içerdiğinde 400 durum kodu döndürülür.</span><span class="sxs-lookup"><span data-stu-id="3a945-157">A 400 status code is returned when the product description contains "XYZ Widget".</span></span> <span data-ttu-id="3a945-158"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>Kolaylık yöntemi için toplu olarak çağrılır `return new BadRequestResult();` .</span><span class="sxs-lookup"><span data-stu-id="3a945-158">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> convenience method is invoked as shorthand for `return new BadRequestResult();`.</span></span>
* <span data-ttu-id="3a945-159">Bir ürün oluşturulduğunda 201 durum kodu <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> kolaylık yöntemi tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3a945-159">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> convenience method when a product is created.</span></span> <span data-ttu-id="3a945-160">Çağırmak için bir alternatiftir `CreatedAtAction` `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);` .</span><span class="sxs-lookup"><span data-stu-id="3a945-160">An alternative to calling `CreatedAtAction` is `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`.</span></span> <span data-ttu-id="3a945-161">Bu kod yolunda, `Product` nesne yanıt gövdesinde sağlanır.</span><span class="sxs-lookup"><span data-stu-id="3a945-161">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="3a945-162">`Location`Yeni oluşturulan ürünün URL 'sini içeren bir yanıt üst bilgisi sağlanır.</span><span class="sxs-lookup"><span data-stu-id="3a945-162">A `Location` response header containing the newly created product's URL is provided.</span></span>

<span data-ttu-id="3a945-163">Örneğin, aşağıdaki model isteklerin ve özelliklerini içermesi gerektiğini gösterir `Name` `Description` .</span><span class="sxs-lookup"><span data-stu-id="3a945-163">For example, the following model indicates that requests must include the `Name` and `Description` properties.</span></span> <span data-ttu-id="3a945-164">`Name`İstek içinde sağlama hatası `Description` , model doğrulamasının başarısız olmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="3a945-164">Failure to provide `Name` and `Description` in the request causes model validation to fail.</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="3a945-165">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)ASP.NET Core 2,1 veya sonraki bir sürümde bulunan öznitelik uygulanmışsa, model doğrulama hataları 400 durum koduna neden olur.</span><span class="sxs-lookup"><span data-stu-id="3a945-165">If the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute in ASP.NET Core 2.1 or later is applied, model validation errors result in a 400 status code.</span></span> <span data-ttu-id="3a945-166">Daha fazla bilgi için bkz. [OTOMATIK HTTP 400 yanıtları](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="3a945-166">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="actionresultt-type"></a><span data-ttu-id="3a945-167">ActionResult \<T> türü</span><span class="sxs-lookup"><span data-stu-id="3a945-167">ActionResult\<T> type</span></span>

<span data-ttu-id="3a945-168">ASP.NET Core 2,1, Web API denetleyicisi eylemleri için [ActionResult \<T> ](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) dönüş türünü sunmuştur.</span><span class="sxs-lookup"><span data-stu-id="3a945-168">ASP.NET Core 2.1 introduced the [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) return type for web API controller actions.</span></span> <span data-ttu-id="3a945-169"><xref:Microsoft.AspNetCore.Mvc.ActionResult> [Belirli bir türden](#specific-type)türetilen veya döndürülen bir tür döndürmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="3a945-169">It enables you to return a type deriving from <xref:Microsoft.AspNetCore.Mvc.ActionResult> or return a [specific type](#specific-type).</span></span> <span data-ttu-id="3a945-170">`ActionResult<T>`[ıactionresult türü](#iactionresult-type)üzerinde aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="3a945-170">`ActionResult<T>` offers the following benefits over the [IActionResult type](#iactionresult-type):</span></span>

* <span data-ttu-id="3a945-171">[`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute)Özniteliğin `Type` özelliği dışarıda bırakılabilirler.</span><span class="sxs-lookup"><span data-stu-id="3a945-171">The [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute's `Type` property can be excluded.</span></span> <span data-ttu-id="3a945-172">Örneğin, `[ProducesResponseType(200, Type = typeof(Product))]` basitleştirilmiştir `[ProducesResponseType(200)]` .</span><span class="sxs-lookup"><span data-stu-id="3a945-172">For example, `[ProducesResponseType(200, Type = typeof(Product))]` is simplified to `[ProducesResponseType(200)]`.</span></span> <span data-ttu-id="3a945-173">Eylemin beklenen dönüş türü, yerine öğesinden çıkarsanamıyor `T` `ActionResult<T>` .</span><span class="sxs-lookup"><span data-stu-id="3a945-173">The action's expected return type is instead inferred from the `T` in `ActionResult<T>`.</span></span>
* <span data-ttu-id="3a945-174">[Örtük atama işleçleri](/dotnet/csharp/language-reference/keywords/implicit) hem hem de için dönüştürmeyi `T` destekler `ActionResult` `ActionResult<T>` .</span><span class="sxs-lookup"><span data-stu-id="3a945-174">[Implicit cast operators](/dotnet/csharp/language-reference/keywords/implicit) support the conversion of both `T` and `ActionResult` to `ActionResult<T>`.</span></span> <span data-ttu-id="3a945-175">`T`öğesine dönüştürür <xref:Microsoft.AspNetCore.Mvc.ObjectResult> , yani `return new ObjectResult(T);` basitleştirilmiştir `return T;` .</span><span class="sxs-lookup"><span data-stu-id="3a945-175">`T` converts to <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, which means `return new ObjectResult(T);` is simplified to `return T;`.</span></span>

<span data-ttu-id="3a945-176">C#, arabirimlerde örtük atama işleçlerini desteklemez.</span><span class="sxs-lookup"><span data-stu-id="3a945-176">C# doesn't support implicit cast operators on interfaces.</span></span> <span data-ttu-id="3a945-177">Sonuç olarak, kullanmak için arabirimin somut bir türe dönüştürülmesi gerekir `ActionResult<T>` .</span><span class="sxs-lookup"><span data-stu-id="3a945-177">Consequently, conversion of the interface to a concrete type is necessary to use `ActionResult<T>`.</span></span> <span data-ttu-id="3a945-178">Örneğin, `IEnumerable` Aşağıdaki örnekte öğesinin kullanımı işe yaramaz:</span><span class="sxs-lookup"><span data-stu-id="3a945-178">For example, use of `IEnumerable` in the following example doesn't work:</span></span>

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

<span data-ttu-id="3a945-179">Önceki kodu gidermeye yönelik bir seçenek, geri dönelim `_repository.GetProducts().ToList();` .</span><span class="sxs-lookup"><span data-stu-id="3a945-179">One option to fix the preceding code is to return `_repository.GetProducts().ToList();`.</span></span>

<span data-ttu-id="3a945-180">Çoğu eylemin belirli bir dönüş türü vardır.</span><span class="sxs-lookup"><span data-stu-id="3a945-180">Most actions have a specific return type.</span></span> <span data-ttu-id="3a945-181">Eylem yürütme sırasında beklenmeyen koşullar gerçekleşebilir, bu durumda belirli tür döndürülmez.</span><span class="sxs-lookup"><span data-stu-id="3a945-181">Unexpected conditions can occur during action execution, in which case the specific type isn't returned.</span></span> <span data-ttu-id="3a945-182">Örneğin, bir eylemin giriş parametresi, model doğrulamasının başarısız olmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="3a945-182">For example, an action's input parameter may fail model validation.</span></span> <span data-ttu-id="3a945-183">Böyle bir durumda, belirli tür yerine uygun türü döndürmek yaygın bir durumdur `ActionResult` .</span><span class="sxs-lookup"><span data-stu-id="3a945-183">In such a case, it's common to return the appropriate `ActionResult` type instead of the specific type.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="3a945-184">Zaman uyumlu eylem</span><span class="sxs-lookup"><span data-stu-id="3a945-184">Synchronous action</span></span>

<span data-ttu-id="3a945-185">İki olası dönüş türü olan zaman uyumlu bir eylem düşünün:</span><span class="sxs-lookup"><span data-stu-id="3a945-185">Consider a synchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

<span data-ttu-id="3a945-186">Önceki eylemde:</span><span class="sxs-lookup"><span data-stu-id="3a945-186">In the preceding action:</span></span>

* <span data-ttu-id="3a945-187">Ürün veritabanında mevcut olmadığında bir 404 durum kodu döndürülür.</span><span class="sxs-lookup"><span data-stu-id="3a945-187">A 404 status code is returned when the product doesn't exist in the database.</span></span>
* <span data-ttu-id="3a945-188">Ürün mevcut olduğunda ilgili nesneyle bir 200 durum kodu döndürülür `Product` .</span><span class="sxs-lookup"><span data-stu-id="3a945-188">A 200 status code is returned with the corresponding `Product` object when the product does exist.</span></span> <span data-ttu-id="3a945-189">2,1 ASP.NET Core önce `return product;` satırın olması gerekiyordu `return Ok(product);` .</span><span class="sxs-lookup"><span data-stu-id="3a945-189">Before ASP.NET Core 2.1, the `return product;` line had to be `return Ok(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="3a945-190">Zaman uyumsuz eylem</span><span class="sxs-lookup"><span data-stu-id="3a945-190">Asynchronous action</span></span>

<span data-ttu-id="3a945-191">İki olası dönüş türü olan zaman uyumsuz bir eylem düşünün:</span><span class="sxs-lookup"><span data-stu-id="3a945-191">Consider an asynchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

<span data-ttu-id="3a945-192">Önceki eylemde:</span><span class="sxs-lookup"><span data-stu-id="3a945-192">In the preceding action:</span></span>

* <span data-ttu-id="3a945-193">ASP.NET Core çalışma zamanı tarafından bir 400 durum kodu ( <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> ) döndürülür:</span><span class="sxs-lookup"><span data-stu-id="3a945-193">A 400 status code (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) is returned by the ASP.NET Core runtime when:</span></span>
  * <span data-ttu-id="3a945-194">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Öznitelik uygulandı ve model doğrulaması başarısız oluyor.</span><span class="sxs-lookup"><span data-stu-id="3a945-194">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute has been applied and model validation fails.</span></span>
  * <span data-ttu-id="3a945-195">Ürün açıklaması "XYZ pencere öğesi" içerir.</span><span class="sxs-lookup"><span data-stu-id="3a945-195">The product description contains "XYZ Widget".</span></span>
* <span data-ttu-id="3a945-196">Bir ürün oluşturulduğunda yöntemi tarafından bir 201 durum kodu oluşturulur <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> .</span><span class="sxs-lookup"><span data-stu-id="3a945-196">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method when a product is created.</span></span> <span data-ttu-id="3a945-197">Bu kod yolunda, `Product` nesne yanıt gövdesinde sağlanır.</span><span class="sxs-lookup"><span data-stu-id="3a945-197">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="3a945-198">`Location`Yeni oluşturulan ürünün URL 'sini içeren bir yanıt üst bilgisi sağlanır.</span><span class="sxs-lookup"><span data-stu-id="3a945-198">A `Location` response header containing the newly created product's URL is provided.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="3a945-199">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3a945-199">Additional resources</span></span>

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
