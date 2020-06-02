---
<span data-ttu-id="81591-101">Başlık: ASP.NET Core Web API Author içindeki denetleyici eylemi dönüş türleri: İskoçya Taddie açıklaması: bir ASP.NET Core Web API 'sindeki çeşitli denetleyici eylemi yöntemi döndürme türlerini kullanma hakkında bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="81591-101">title: Controller action return types in ASP.NET Core web API author: scottaddie description: Learn about using the various controller action method return types in an ASP.NET Core web API.</span></span>
<span data-ttu-id="81591-102">MS. Author: scadzar MS. Custom: MVC MS. Date: 02/03/2020 No-loc:</span><span class="sxs-lookup"><span data-stu-id="81591-102">ms.author: scaddie ms.custom: mvc ms.date: 02/03/2020 no-loc:</span></span>
- <span data-ttu-id="81591-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="81591-103">'Blazor'</span></span>
- <span data-ttu-id="81591-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="81591-104">'Identity'</span></span>
- <span data-ttu-id="81591-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="81591-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="81591-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="81591-106">'Razor'</span></span>
- <span data-ttu-id="81591-107">' SignalR ' uid: Web-api/ACTION-Return-Types</span><span class="sxs-lookup"><span data-stu-id="81591-107">'SignalR' uid: web-api/action-return-types</span></span>

---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a><span data-ttu-id="81591-108">ASP.NET Core Web API 'sindeki denetleyici eylemi dönüş türleri</span><span class="sxs-lookup"><span data-stu-id="81591-108">Controller action return types in ASP.NET Core web API</span></span>

<span data-ttu-id="81591-109">[Scott Ade](https://github.com/scottaddie) tarafından</span><span class="sxs-lookup"><span data-stu-id="81591-109">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="81591-110">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="81591-110">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="81591-111">ASP.NET Core Web API denetleyicisi eylem dönüş türleri için aşağıdaki seçenekleri sunar:</span><span class="sxs-lookup"><span data-stu-id="81591-111">ASP.NET Core offers the following options for web API controller action return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

* [<span data-ttu-id="81591-112">Belirli tür</span><span class="sxs-lookup"><span data-stu-id="81591-112">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="81591-113">Iactionresult</span><span class="sxs-lookup"><span data-stu-id="81591-113">IActionResult</span></span>](#iactionresult-type)
* [<span data-ttu-id="81591-114">ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="81591-114">ActionResult\<T></span></span>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [<span data-ttu-id="81591-115">Belirli tür</span><span class="sxs-lookup"><span data-stu-id="81591-115">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="81591-116">Iactionresult</span><span class="sxs-lookup"><span data-stu-id="81591-116">IActionResult</span></span>](#iactionresult-type)

::: moniker-end

<span data-ttu-id="81591-117">Bu belgede, her dönüş türünü kullanmak için en uygun olan bilgiler açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="81591-117">This document explains when it's most appropriate to use each return type.</span></span>

## <a name="specific-type"></a><span data-ttu-id="81591-118">Belirli tür</span><span class="sxs-lookup"><span data-stu-id="81591-118">Specific type</span></span>

<span data-ttu-id="81591-119">En basit eylem, basit veya karmaşık bir veri türü döndürür (örneğin, `string` veya özel nesne türü).</span><span class="sxs-lookup"><span data-stu-id="81591-119">The simplest action returns a primitive or complex data type (for example, `string` or a custom object type).</span></span> <span data-ttu-id="81591-120">Özel nesneler koleksiyonunu döndüren aşağıdaki eylemi göz önünde bulundurun `Product` :</span><span class="sxs-lookup"><span data-stu-id="81591-120">Consider the following action, which returns a collection of custom `Product` objects:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

<span data-ttu-id="81591-121">Eylem yürütme sırasında korunmak üzere bilinen koşullar olmadan, belirli bir türü döndürmek yeterli olabilir.</span><span class="sxs-lookup"><span data-stu-id="81591-121">Without known conditions to safeguard against during action execution, returning a specific type could suffice.</span></span> <span data-ttu-id="81591-122">Önceki eylem hiçbir parametre kabul etmez, bu nedenle parametre kısıtlamaları doğrulaması gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="81591-122">The preceding action accepts no parameters, so parameter constraints validation isn't needed.</span></span>

<span data-ttu-id="81591-123">Birden çok dönüş türü mümkün olduğunda, bir <xref:Microsoft.AspNetCore.Mvc.ActionResult> dönüş türünü basit veya karmaşık dönüş türüyle karıştırmak yaygındır.</span><span class="sxs-lookup"><span data-stu-id="81591-123">When multiple return types are possible, it's common to mix an <xref:Microsoft.AspNetCore.Mvc.ActionResult> return type with the primitive or complex return type.</span></span> <span data-ttu-id="81591-124">Bu tür eyleme uyum sağlamak için [ıactionresult](#iactionresult-type) veya [ \<T> ActionResult](#actionresultt-type) gereklidir.</span><span class="sxs-lookup"><span data-stu-id="81591-124">Either [IActionResult](#iactionresult-type) or [ActionResult\<T>](#actionresultt-type) are necessary to accommodate this type of action.</span></span> <span data-ttu-id="81591-125">Bu belgede birden fazla dönüş türünden birkaç örnek verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="81591-125">Several samples of multiple return types are provided in this document.</span></span>

### <a name="return-ienumerablet-or-iasyncenumerablet"></a><span data-ttu-id="81591-126">IEnumerable \<T> veya ıasyncenumerable döndürün\<T></span><span class="sxs-lookup"><span data-stu-id="81591-126">Return IEnumerable\<T> or IAsyncEnumerable\<T></span></span>

<span data-ttu-id="81591-127">ASP.NET Core 2,2 ve önceki sürümlerde, <xref:System.Collections.Generic.IEnumerable%601> bir eylemden geri dönmek seri hale getirici tarafından zaman uyumlu koleksiyon yinelemesi ile sonuçlanır.</span><span class="sxs-lookup"><span data-stu-id="81591-127">In ASP.NET Core 2.2 and earlier, returning <xref:System.Collections.Generic.IEnumerable%601> from an action results in synchronous collection iteration by the serializer.</span></span> <span data-ttu-id="81591-128">Sonuç olarak, çağrı engelleme ve iş parçacığı havuzu için olası bir olasılık vardır.</span><span class="sxs-lookup"><span data-stu-id="81591-128">The result is the blocking of calls and a potential for thread pool starvation.</span></span> <span data-ttu-id="81591-129">Göstermek için, Web API 'sinin veri erişimi ihtiyaçları için Entity Framework (EF) Core kullanıldığını düşünün.</span><span class="sxs-lookup"><span data-stu-id="81591-129">To illustrate, imagine that Entity Framework (EF) Core is being used for the web API's data access needs.</span></span> <span data-ttu-id="81591-130">Aşağıdaki eylemin dönüş türü serileştirme sırasında zaman uyumlu olarak numaralandırılır:</span><span class="sxs-lookup"><span data-stu-id="81591-130">The following action's return type is synchronously enumerated during serialization:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="81591-131">Zaman uyumlu numaralandırmayı önlemek ve engellemeyi ASP.NET Core 2,2 ve önceki sürümlerde veritabanı üzerinde bekleyip engellemek için şunu çağırın `ToListAsync` :</span><span class="sxs-lookup"><span data-stu-id="81591-131">To avoid synchronous enumeration and blocking waits on the database in ASP.NET Core 2.2 and earlier, invoke `ToListAsync`:</span></span>

```csharp
public async Task<IEnumerable<Product>> GetOnSaleProducts() =>
    await _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

<span data-ttu-id="81591-132">ASP.NET Core 3,0 ve üzeri sürümlerde, `IAsyncEnumerable<T>` bir eylemden geri dönerek:</span><span class="sxs-lookup"><span data-stu-id="81591-132">In ASP.NET Core 3.0 and later, returning `IAsyncEnumerable<T>` from an action:</span></span>

* <span data-ttu-id="81591-133">Zaman uyumlu yineleme ile sonuçlanmayacaktır.</span><span class="sxs-lookup"><span data-stu-id="81591-133">No longer results in synchronous iteration.</span></span>
* <span data-ttu-id="81591-134">Dönerek etkin hale gelir <xref:System.Collections.Generic.IEnumerable%601> .</span><span class="sxs-lookup"><span data-stu-id="81591-134">Becomes as efficient as returning <xref:System.Collections.Generic.IEnumerable%601>.</span></span>

<span data-ttu-id="81591-135">ASP.NET Core 3,0 ve üzeri, serileştiriciye sağlamadan önce aşağıdaki eylemin sonucunu arabelleğe alır:</span><span class="sxs-lookup"><span data-stu-id="81591-135">ASP.NET Core 3.0 and later buffers the result of the following action before providing it to the serializer:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="81591-136">Zaman uyumsuz yinelemeyi güvence altına almak için eylem imzasının dönüş türünü bildirmeyi göz önünde bulundurun `IAsyncEnumerable<T>` .</span><span class="sxs-lookup"><span data-stu-id="81591-136">Consider declaring the action signature's return type as `IAsyncEnumerable<T>` to guarantee the asynchronous iteration.</span></span> <span data-ttu-id="81591-137">Sonuç olarak, yineleme modu döndürülmekte olan temel somut türü temel alır.</span><span class="sxs-lookup"><span data-stu-id="81591-137">Ultimately, the iteration mode is based on the underlying concrete type being returned.</span></span> <span data-ttu-id="81591-138">MVC, uygulayan tüm somut türleri otomatik olarak arabelleğe alır `IAsyncEnumerable<T>` .</span><span class="sxs-lookup"><span data-stu-id="81591-138">MVC automatically buffers any concrete type that implements `IAsyncEnumerable<T>`.</span></span>

<span data-ttu-id="81591-139">Şu şekilde satış fiyatlı ürün kayıtları döndüren aşağıdaki eylemi göz önünde bulundurun `IEnumerable<Product>` :</span><span class="sxs-lookup"><span data-stu-id="81591-139">Consider the following action, which returns sale-priced product records as `IEnumerable<Product>`:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

<span data-ttu-id="81591-140">`IAsyncEnumerable<Product>`Önceki eylemin eşdeğeri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="81591-140">The `IAsyncEnumerable<Product>` equivalent of the preceding action is:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

<span data-ttu-id="81591-141">Yukarıdaki eylemlerin her ikisi de ASP.NET Core 3,0 itibariyle engellenmeyen bir işlem değildir.</span><span class="sxs-lookup"><span data-stu-id="81591-141">Both of the preceding actions are non-blocking as of ASP.NET Core 3.0.</span></span>

## <a name="iactionresult-type"></a><span data-ttu-id="81591-142">Iactionresult türü</span><span class="sxs-lookup"><span data-stu-id="81591-142">IActionResult type</span></span>

<span data-ttu-id="81591-143"><xref:Microsoft.AspNetCore.Mvc.IActionResult>Bir eylemde birden çok dönüş türü mümkünse, dönüş türü uygun olur `ActionResult` .</span><span class="sxs-lookup"><span data-stu-id="81591-143">The <xref:Microsoft.AspNetCore.Mvc.IActionResult> return type is appropriate when multiple `ActionResult` return types are possible in an action.</span></span> <span data-ttu-id="81591-144">`ActionResult`Türler ÇEŞITLI http durum kodlarını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="81591-144">The `ActionResult` types represent various HTTP status codes.</span></span> <span data-ttu-id="81591-145">Herhangi bir soyut olmayan sınıf, `ActionResult` geçerli bir dönüş türü olarak niteleyen ' dan türetiliyor.</span><span class="sxs-lookup"><span data-stu-id="81591-145">Any non-abstract class deriving from `ActionResult` qualifies as a valid return type.</span></span> <span data-ttu-id="81591-146">Bu kategorideki bazı yaygın dönüş türleri şunlardır <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) ve <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span><span class="sxs-lookup"><span data-stu-id="81591-146">Some common return types in this category are <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404), and <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span></span> <span data-ttu-id="81591-147">Alternatif olarak, sınıftaki kullanışlı yöntemler <xref:Microsoft.AspNetCore.Mvc.ControllerBase> bir eylemden tür döndürmek için kullanılabilir `ActionResult` .</span><span class="sxs-lookup"><span data-stu-id="81591-147">Alternatively, convenience methods in the <xref:Microsoft.AspNetCore.Mvc.ControllerBase> class can be used to return `ActionResult` types from an action.</span></span> <span data-ttu-id="81591-148">Örneğin, `return BadRequest();` öğesinin bir toplu biçimidir `return new BadRequestResult();` .</span><span class="sxs-lookup"><span data-stu-id="81591-148">For example, `return BadRequest();` is a shorthand form of `return new BadRequestResult();`.</span></span>

<span data-ttu-id="81591-149">Bu tür bir eylemde birden çok dönüş türü ve yolu olduğundan, özniteliğin serbest kullanımı [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) gereklidir.</span><span class="sxs-lookup"><span data-stu-id="81591-149">Because there are multiple return types and paths in this type of action, liberal use of the [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute is necessary.</span></span> <span data-ttu-id="81591-150">Bu öznitelik, [Swagger](xref:tutorials/web-api-help-pages-using-swagger)gibi araçlar tarafından oluşturulan Web API Yardım sayfaları için daha açıklayıcı yanıt ayrıntıları üretir.</span><span class="sxs-lookup"><span data-stu-id="81591-150">This attribute produces more descriptive response details for web API help pages generated by tools like [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="81591-151">`[ProducesResponseType]`eylem tarafından döndürülecek bilinen türleri ve HTTP durum kodlarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="81591-151">`[ProducesResponseType]` indicates the known types and HTTP status codes to be returned by the action.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="81591-152">Zaman uyumlu eylem</span><span class="sxs-lookup"><span data-stu-id="81591-152">Synchronous action</span></span>

<span data-ttu-id="81591-153">İki olası dönüş türü olan aşağıdaki zaman uyumlu eylemi göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="81591-153">Consider the following synchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

<span data-ttu-id="81591-154">Önceki eylemde:</span><span class="sxs-lookup"><span data-stu-id="81591-154">In the preceding action:</span></span>

* <span data-ttu-id="81591-155">Temel alınan veri deposunda tarafından temsil edilen ürün olmadığında 404 durum kodu döndürülür `id` .</span><span class="sxs-lookup"><span data-stu-id="81591-155">A 404 status code is returned when the product represented by `id` doesn't exist in the underlying data store.</span></span> <span data-ttu-id="81591-156"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>Kolaylık yöntemi için toplu olarak çağrılır `return new NotFoundResult();` .</span><span class="sxs-lookup"><span data-stu-id="81591-156">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> convenience method is invoked as shorthand for `return new NotFoundResult();`.</span></span>
* <span data-ttu-id="81591-157">Ürün mevcut olduğunda nesneyle bir 200 durum kodu döndürülür `Product` .</span><span class="sxs-lookup"><span data-stu-id="81591-157">A 200 status code is returned with the `Product` object when the product does exist.</span></span> <span data-ttu-id="81591-158"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*>Kolaylık yöntemi için toplu olarak çağrılır `return new OkObjectResult(product);` .</span><span class="sxs-lookup"><span data-stu-id="81591-158">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> convenience method is invoked as shorthand for `return new OkObjectResult(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="81591-159">Zaman uyumsuz eylem</span><span class="sxs-lookup"><span data-stu-id="81591-159">Asynchronous action</span></span>

<span data-ttu-id="81591-160">İki olası dönüş türü olan aşağıdaki zaman uyumsuz eylemi göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="81591-160">Consider the following asynchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

<span data-ttu-id="81591-161">Önceki eylemde:</span><span class="sxs-lookup"><span data-stu-id="81591-161">In the preceding action:</span></span>

* <span data-ttu-id="81591-162">Ürün açıklaması "XYZ pencere öğesi" içerdiğinde 400 durum kodu döndürülür.</span><span class="sxs-lookup"><span data-stu-id="81591-162">A 400 status code is returned when the product description contains "XYZ Widget".</span></span> <span data-ttu-id="81591-163"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>Kolaylık yöntemi için toplu olarak çağrılır `return new BadRequestResult();` .</span><span class="sxs-lookup"><span data-stu-id="81591-163">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> convenience method is invoked as shorthand for `return new BadRequestResult();`.</span></span>
* <span data-ttu-id="81591-164">Bir ürün oluşturulduğunda 201 durum kodu <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> kolaylık yöntemi tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="81591-164">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> convenience method when a product is created.</span></span> <span data-ttu-id="81591-165">Çağırmak için bir alternatiftir `CreatedAtAction` `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);` .</span><span class="sxs-lookup"><span data-stu-id="81591-165">An alternative to calling `CreatedAtAction` is `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`.</span></span> <span data-ttu-id="81591-166">Bu kod yolunda, `Product` nesne yanıt gövdesinde sağlanır.</span><span class="sxs-lookup"><span data-stu-id="81591-166">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="81591-167">`Location`Yeni oluşturulan ürünün URL 'sini içeren bir yanıt üst bilgisi sağlanır.</span><span class="sxs-lookup"><span data-stu-id="81591-167">A `Location` response header containing the newly created product's URL is provided.</span></span>

<span data-ttu-id="81591-168">Örneğin, aşağıdaki model isteklerin ve özelliklerini içermesi gerektiğini gösterir `Name` `Description` .</span><span class="sxs-lookup"><span data-stu-id="81591-168">For example, the following model indicates that requests must include the `Name` and `Description` properties.</span></span> <span data-ttu-id="81591-169">`Name`İstek içinde sağlama hatası `Description` , model doğrulamasının başarısız olmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="81591-169">Failure to provide `Name` and `Description` in the request causes model validation to fail.</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="81591-170">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)ASP.NET Core 2,1 veya sonraki bir sürümde bulunan öznitelik uygulanmışsa, model doğrulama hataları 400 durum koduna neden olur.</span><span class="sxs-lookup"><span data-stu-id="81591-170">If the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute in ASP.NET Core 2.1 or later is applied, model validation errors result in a 400 status code.</span></span> <span data-ttu-id="81591-171">Daha fazla bilgi için bkz. [OTOMATIK HTTP 400 yanıtları](xref:web-api/index#automatic-http-400-responses).</span><span class="sxs-lookup"><span data-stu-id="81591-171">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="actionresultt-type"></a><span data-ttu-id="81591-172">ActionResult \<T> türü</span><span class="sxs-lookup"><span data-stu-id="81591-172">ActionResult\<T> type</span></span>

<span data-ttu-id="81591-173">ASP.NET Core 2,1, Web API denetleyicisi eylemleri için [ActionResult \<T> ](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) dönüş türünü sunmuştur.</span><span class="sxs-lookup"><span data-stu-id="81591-173">ASP.NET Core 2.1 introduced the [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) return type for web API controller actions.</span></span> <span data-ttu-id="81591-174"><xref:Microsoft.AspNetCore.Mvc.ActionResult> [Belirli bir türden](#specific-type)türetilen veya döndürülen bir tür döndürmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="81591-174">It enables you to return a type deriving from <xref:Microsoft.AspNetCore.Mvc.ActionResult> or return a [specific type](#specific-type).</span></span> <span data-ttu-id="81591-175">`ActionResult<T>`[ıactionresult türü](#iactionresult-type)üzerinde aşağıdaki avantajları sunar:</span><span class="sxs-lookup"><span data-stu-id="81591-175">`ActionResult<T>` offers the following benefits over the [IActionResult type](#iactionresult-type):</span></span>

* <span data-ttu-id="81591-176">[`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute)Özniteliğin `Type` özelliği dışarıda bırakılabilirler.</span><span class="sxs-lookup"><span data-stu-id="81591-176">The [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute's `Type` property can be excluded.</span></span> <span data-ttu-id="81591-177">Örneğin, `[ProducesResponseType(200, Type = typeof(Product))]` basitleştirilmiştir `[ProducesResponseType(200)]` .</span><span class="sxs-lookup"><span data-stu-id="81591-177">For example, `[ProducesResponseType(200, Type = typeof(Product))]` is simplified to `[ProducesResponseType(200)]`.</span></span> <span data-ttu-id="81591-178">Eylemin beklenen dönüş türü, yerine öğesinden çıkarsanamıyor `T` `ActionResult<T>` .</span><span class="sxs-lookup"><span data-stu-id="81591-178">The action's expected return type is instead inferred from the `T` in `ActionResult<T>`.</span></span>
* <span data-ttu-id="81591-179">[Örtük atama işleçleri](/dotnet/csharp/language-reference/keywords/implicit) hem hem de için dönüştürmeyi `T` destekler `ActionResult` `ActionResult<T>` .</span><span class="sxs-lookup"><span data-stu-id="81591-179">[Implicit cast operators](/dotnet/csharp/language-reference/keywords/implicit) support the conversion of both `T` and `ActionResult` to `ActionResult<T>`.</span></span> <span data-ttu-id="81591-180">`T`öğesine dönüştürür <xref:Microsoft.AspNetCore.Mvc.ObjectResult> , yani `return new ObjectResult(T);` basitleştirilmiştir `return T;` .</span><span class="sxs-lookup"><span data-stu-id="81591-180">`T` converts to <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, which means `return new ObjectResult(T);` is simplified to `return T;`.</span></span>

<span data-ttu-id="81591-181">C#, arabirimlerde örtük atama işleçlerini desteklemez.</span><span class="sxs-lookup"><span data-stu-id="81591-181">C# doesn't support implicit cast operators on interfaces.</span></span> <span data-ttu-id="81591-182">Sonuç olarak, kullanmak için arabirimin somut bir türe dönüştürülmesi gerekir `ActionResult<T>` .</span><span class="sxs-lookup"><span data-stu-id="81591-182">Consequently, conversion of the interface to a concrete type is necessary to use `ActionResult<T>`.</span></span> <span data-ttu-id="81591-183">Örneğin, `IEnumerable` Aşağıdaki örnekte öğesinin kullanımı işe yaramaz:</span><span class="sxs-lookup"><span data-stu-id="81591-183">For example, use of `IEnumerable` in the following example doesn't work:</span></span>

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

<span data-ttu-id="81591-184">Önceki kodu gidermeye yönelik bir seçenek, geri dönelim `_repository.GetProducts().ToList();` .</span><span class="sxs-lookup"><span data-stu-id="81591-184">One option to fix the preceding code is to return `_repository.GetProducts().ToList();`.</span></span>

<span data-ttu-id="81591-185">Çoğu eylemin belirli bir dönüş türü vardır.</span><span class="sxs-lookup"><span data-stu-id="81591-185">Most actions have a specific return type.</span></span> <span data-ttu-id="81591-186">Eylem yürütme sırasında beklenmeyen koşullar gerçekleşebilir, bu durumda belirli tür döndürülmez.</span><span class="sxs-lookup"><span data-stu-id="81591-186">Unexpected conditions can occur during action execution, in which case the specific type isn't returned.</span></span> <span data-ttu-id="81591-187">Örneğin, bir eylemin giriş parametresi, model doğrulamasının başarısız olmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="81591-187">For example, an action's input parameter may fail model validation.</span></span> <span data-ttu-id="81591-188">Böyle bir durumda, belirli tür yerine uygun türü döndürmek yaygın bir durumdur `ActionResult` .</span><span class="sxs-lookup"><span data-stu-id="81591-188">In such a case, it's common to return the appropriate `ActionResult` type instead of the specific type.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="81591-189">Zaman uyumlu eylem</span><span class="sxs-lookup"><span data-stu-id="81591-189">Synchronous action</span></span>

<span data-ttu-id="81591-190">İki olası dönüş türü olan zaman uyumlu bir eylem düşünün:</span><span class="sxs-lookup"><span data-stu-id="81591-190">Consider a synchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

<span data-ttu-id="81591-191">Önceki eylemde:</span><span class="sxs-lookup"><span data-stu-id="81591-191">In the preceding action:</span></span>

* <span data-ttu-id="81591-192">Ürün veritabanında mevcut olmadığında bir 404 durum kodu döndürülür.</span><span class="sxs-lookup"><span data-stu-id="81591-192">A 404 status code is returned when the product doesn't exist in the database.</span></span>
* <span data-ttu-id="81591-193">Ürün mevcut olduğunda ilgili nesneyle bir 200 durum kodu döndürülür `Product` .</span><span class="sxs-lookup"><span data-stu-id="81591-193">A 200 status code is returned with the corresponding `Product` object when the product does exist.</span></span> <span data-ttu-id="81591-194">2,1 ASP.NET Core önce `return product;` satırın olması gerekiyordu `return Ok(product);` .</span><span class="sxs-lookup"><span data-stu-id="81591-194">Before ASP.NET Core 2.1, the `return product;` line had to be `return Ok(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="81591-195">Zaman uyumsuz eylem</span><span class="sxs-lookup"><span data-stu-id="81591-195">Asynchronous action</span></span>

<span data-ttu-id="81591-196">İki olası dönüş türü olan zaman uyumsuz bir eylem düşünün:</span><span class="sxs-lookup"><span data-stu-id="81591-196">Consider an asynchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

<span data-ttu-id="81591-197">Önceki eylemde:</span><span class="sxs-lookup"><span data-stu-id="81591-197">In the preceding action:</span></span>

* <span data-ttu-id="81591-198">ASP.NET Core çalışma zamanı tarafından bir 400 durum kodu ( <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> ) döndürülür:</span><span class="sxs-lookup"><span data-stu-id="81591-198">A 400 status code (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) is returned by the ASP.NET Core runtime when:</span></span>
  * <span data-ttu-id="81591-199">[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Öznitelik uygulandı ve model doğrulaması başarısız oluyor.</span><span class="sxs-lookup"><span data-stu-id="81591-199">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute has been applied and model validation fails.</span></span>
  * <span data-ttu-id="81591-200">Ürün açıklaması "XYZ pencere öğesi" içerir.</span><span class="sxs-lookup"><span data-stu-id="81591-200">The product description contains "XYZ Widget".</span></span>
* <span data-ttu-id="81591-201">Bir ürün oluşturulduğunda yöntemi tarafından bir 201 durum kodu oluşturulur <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> .</span><span class="sxs-lookup"><span data-stu-id="81591-201">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method when a product is created.</span></span> <span data-ttu-id="81591-202">Bu kod yolunda, `Product` nesne yanıt gövdesinde sağlanır.</span><span class="sxs-lookup"><span data-stu-id="81591-202">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="81591-203">`Location`Yeni oluşturulan ürünün URL 'sini içeren bir yanıt üst bilgisi sağlanır.</span><span class="sxs-lookup"><span data-stu-id="81591-203">A `Location` response header containing the newly created product's URL is provided.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="81591-204">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="81591-204">Additional resources</span></span>

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
