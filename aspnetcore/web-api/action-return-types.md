---
title: ASP.NET Core Web API 'sindeki denetleyici eylemi dönüş türleri
author: scottaddie
description: ASP.NET Core Web API 'sindeki çeşitli denetleyici eylemi yöntemi döndürme türlerini kullanma hakkında bilgi edinin.
ms.author: scaddie
ms.custom: mvc
ms.date: 02/03/2020
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
uid: web-api/action-return-types
ms.openlocfilehash: 3058fabb0c08ac62956c18f3c294692d35122e12
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022166"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a>ASP.NET Core Web API 'sindeki denetleyici eylemi dönüş türleri

[Scott Ade](https://github.com/scottaddie) tarafından

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

ASP.NET Core Web API denetleyicisi eylem dönüş türleri için aşağıdaki seçenekleri sunar:

::: moniker range=">= aspnetcore-2.1"

* [Belirli tür](#specific-type)
* [Iactionresult](#iactionresult-type)
* [ActionResult\<T>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [Belirli tür](#specific-type)
* [Iactionresult](#iactionresult-type)

::: moniker-end

Bu belgede, her dönüş türünü kullanmak için en uygun olan bilgiler açıklanmaktadır.

## <a name="specific-type"></a>Belirli tür

En basit eylem, basit veya karmaşık bir veri türü döndürür (örneğin, `string` veya özel nesne türü). Özel nesneler koleksiyonunu döndüren aşağıdaki eylemi göz önünde bulundurun `Product` :

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

Eylem yürütme sırasında korunmak üzere bilinen koşullar olmadan, belirli bir türü döndürmek yeterli olabilir. Önceki eylem hiçbir parametre kabul etmez, bu nedenle parametre kısıtlamaları doğrulaması gerekli değildir.

Birden çok dönüş türü mümkün olduğunda, bir <xref:Microsoft.AspNetCore.Mvc.ActionResult> dönüş türünü basit veya karmaşık dönüş türüyle karıştırmak yaygındır. Bu tür eyleme uyum sağlamak için [ıactionresult](#iactionresult-type) veya [ \<T> ActionResult](#actionresultt-type) gereklidir. Bu belgede birden fazla dönüş türünden birkaç örnek verilmiştir.

### <a name="return-ienumerablet-or-iasyncenumerablet"></a>IEnumerable \<T> veya ıasyncenumerable döndürün\<T>

ASP.NET Core 2,2 ve önceki sürümlerde, <xref:System.Collections.Generic.IEnumerable%601> bir eylemden geri dönmek seri hale getirici tarafından zaman uyumlu koleksiyon yinelemesi ile sonuçlanır. Sonuç olarak, çağrı engelleme ve iş parçacığı havuzu için olası bir olasılık vardır. Göstermek için, Web API 'sinin veri erişimi ihtiyaçları için Entity Framework (EF) Core kullanıldığını düşünün. Aşağıdaki eylemin dönüş türü serileştirme sırasında zaman uyumlu olarak numaralandırılır:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Zaman uyumlu numaralandırmayı önlemek ve engellemeyi ASP.NET Core 2,2 ve önceki sürümlerde veritabanı üzerinde bekleyip engellemek için şunu çağırın `ToListAsync` :

```csharp
public async Task<IEnumerable<Product>> GetOnSaleProducts() =>
    await _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

ASP.NET Core 3,0 ve üzeri sürümlerde, `IAsyncEnumerable<T>` bir eylemden geri dönerek:

* Zaman uyumlu yineleme ile sonuçlanmayacaktır.
* Dönerek etkin hale gelir <xref:System.Collections.Generic.IEnumerable%601> .

ASP.NET Core 3,0 ve üzeri, serileştiriciye sağlamadan önce aşağıdaki eylemin sonucunu arabelleğe alır:

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

Zaman uyumsuz yinelemeyi güvence altına almak için eylem imzasının dönüş türünü bildirmeyi göz önünde bulundurun `IAsyncEnumerable<T>` . Sonuç olarak, yineleme modu döndürülmekte olan temel somut türü temel alır. MVC, uygulayan tüm somut türleri otomatik olarak arabelleğe alır `IAsyncEnumerable<T>` .

Şu şekilde satış fiyatlı ürün kayıtları döndüren aşağıdaki eylemi göz önünde bulundurun `IEnumerable<Product>` :

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

`IAsyncEnumerable<Product>`Önceki eylemin eşdeğeri şunlardır:

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

Yukarıdaki eylemlerin her ikisi de ASP.NET Core 3,0 itibariyle engellenmeyen bir işlem değildir.

## <a name="iactionresult-type"></a>Iactionresult türü

<xref:Microsoft.AspNetCore.Mvc.IActionResult>Bir eylemde birden çok dönüş türü mümkünse, dönüş türü uygun olur `ActionResult` . `ActionResult`Türler ÇEŞITLI http durum kodlarını temsil eder. Herhangi bir soyut olmayan sınıf, `ActionResult` geçerli bir dönüş türü olarak niteleyen ' dan türetiliyor. Bu kategorideki bazı yaygın dönüş türleri şunlardır <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) ve <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200). Alternatif olarak, sınıftaki kullanışlı yöntemler <xref:Microsoft.AspNetCore.Mvc.ControllerBase> bir eylemden tür döndürmek için kullanılabilir `ActionResult` . Örneğin, `return BadRequest();` öğesinin bir toplu biçimidir `return new BadRequestResult();` .

Bu tür bir eylemde birden çok dönüş türü ve yolu olduğundan, özniteliğin serbest kullanımı [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) gereklidir. Bu öznitelik, [Swagger](xref:tutorials/web-api-help-pages-using-swagger)gibi araçlar tarafından oluşturulan Web API Yardım sayfaları için daha açıklayıcı yanıt ayrıntıları üretir. `[ProducesResponseType]`eylem tarafından döndürülecek bilinen türleri ve HTTP durum kodlarını gösterir.

### <a name="synchronous-action"></a>Zaman uyumlu eylem

İki olası dönüş türü olan aşağıdaki zaman uyumlu eylemi göz önünde bulundurun:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

Önceki eylemde:

* Temel alınan veri deposunda tarafından temsil edilen ürün olmadığında 404 durum kodu döndürülür `id` . <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*>Kolaylık yöntemi için toplu olarak çağrılır `return new NotFoundResult();` .
* Ürün mevcut olduğunda nesneyle bir 200 durum kodu döndürülür `Product` . <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*>Kolaylık yöntemi için toplu olarak çağrılır `return new OkObjectResult(product);` .

### <a name="asynchronous-action"></a>Zaman uyumsuz eylem

İki olası dönüş türü olan aşağıdaki zaman uyumsuz eylemi göz önünde bulundurun:

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

Önceki eylemde:

* Ürün açıklaması "XYZ pencere öğesi" içerdiğinde 400 durum kodu döndürülür. <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>Kolaylık yöntemi için toplu olarak çağrılır `return new BadRequestResult();` .
* Bir ürün oluşturulduğunda 201 durum kodu <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> kolaylık yöntemi tarafından oluşturulur. Çağırmak için bir alternatiftir `CreatedAtAction` `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);` . Bu kod yolunda, `Product` nesne yanıt gövdesinde sağlanır. `Location`Yeni oluşturulan ürünün URL 'sini içeren bir yanıt üst bilgisi sağlanır.

Örneğin, aşağıdaki model isteklerin ve özelliklerini içermesi gerektiğini gösterir `Name` `Description` . `Name`İstek içinde sağlama hatası `Description` , model doğrulamasının başarısız olmasına neden olur.

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)ASP.NET Core 2,1 veya sonraki bir sürümde bulunan öznitelik uygulanmışsa, model doğrulama hataları 400 durum koduna neden olur. Daha fazla bilgi için bkz. [OTOMATIK HTTP 400 yanıtları](xref:web-api/index#automatic-http-400-responses).

## <a name="actionresultt-type"></a>ActionResult \<T> türü

ASP.NET Core 2,1, Web API denetleyicisi eylemleri için [ActionResult \<T> ](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) dönüş türünü sunmuştur. <xref:Microsoft.AspNetCore.Mvc.ActionResult> [Belirli bir türden](#specific-type)türetilen veya döndürülen bir tür döndürmenizi sağlar. `ActionResult<T>`[ıactionresult türü](#iactionresult-type)üzerinde aşağıdaki avantajları sunar:

* [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute)Özniteliğin `Type` özelliği dışarıda bırakılabilirler. Örneğin, `[ProducesResponseType(200, Type = typeof(Product))]` basitleştirilmiştir `[ProducesResponseType(200)]` . Eylemin beklenen dönüş türü, yerine öğesinden çıkarsanamıyor `T` `ActionResult<T>` .
* [Örtük atama işleçleri](/dotnet/csharp/language-reference/keywords/implicit) hem hem de için dönüştürmeyi `T` destekler `ActionResult` `ActionResult<T>` . `T`öğesine dönüştürür <xref:Microsoft.AspNetCore.Mvc.ObjectResult> , yani `return new ObjectResult(T);` basitleştirilmiştir `return T;` .

C#, arabirimlerde örtük atama işleçlerini desteklemez. Sonuç olarak, kullanmak için arabirimin somut bir türe dönüştürülmesi gerekir `ActionResult<T>` . Örneğin, `IEnumerable` Aşağıdaki örnekte öğesinin kullanımı işe yaramaz:

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

Önceki kodu gidermeye yönelik bir seçenek, geri dönelim `_repository.GetProducts().ToList();` .

Çoğu eylemin belirli bir dönüş türü vardır. Eylem yürütme sırasında beklenmeyen koşullar gerçekleşebilir, bu durumda belirli tür döndürülmez. Örneğin, bir eylemin giriş parametresi, model doğrulamasının başarısız olmasına neden olabilir. Böyle bir durumda, belirli tür yerine uygun türü döndürmek yaygın bir durumdur `ActionResult` .

### <a name="synchronous-action"></a>Zaman uyumlu eylem

İki olası dönüş türü olan zaman uyumlu bir eylem düşünün:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

Önceki eylemde:

* Ürün veritabanında mevcut olmadığında bir 404 durum kodu döndürülür.
* Ürün mevcut olduğunda ilgili nesneyle bir 200 durum kodu döndürülür `Product` . 2,1 ASP.NET Core önce `return product;` satırın olması gerekiyordu `return Ok(product);` .

### <a name="asynchronous-action"></a>Zaman uyumsuz eylem

İki olası dönüş türü olan zaman uyumsuz bir eylem düşünün:

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

Önceki eylemde:

* ASP.NET Core çalışma zamanı tarafından bir 400 durum kodu ( <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> ) döndürülür:
  * [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)Öznitelik uygulandı ve model doğrulaması başarısız oluyor.
  * Ürün açıklaması "XYZ pencere öğesi" içerir.
* Bir ürün oluşturulduğunda yöntemi tarafından bir 201 durum kodu oluşturulur <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> . Bu kod yolunda, `Product` nesne yanıt gövdesinde sağlanır. `Location`Yeni oluşturulan ürünün URL 'sini içeren bir yanıt üst bilgisi sağlanır.

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
