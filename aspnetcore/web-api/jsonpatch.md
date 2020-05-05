---
title: ASP.NET Core Web API 'sinde JsonPatch
author: rick-anderson
description: ASP.NET Core Web API 'sindeki JSON Patch isteklerini nasıl işleyeceğinizi öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/jsonpatch
ms.openlocfilehash: 3a78fa268cce8cff10fedf5814d61ce0e5faaf4b
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766673"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="77d12-103">ASP.NET Core Web API 'sinde JsonPatch</span><span class="sxs-lookup"><span data-stu-id="77d12-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="77d12-104">, [Tom Dykstra](https://github.com/tdykstra) ve [Kirk larkabağı](https://github.com/serpent5) tarafından</span><span class="sxs-lookup"><span data-stu-id="77d12-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="77d12-105">Bu makalede, ASP.NET Core Web API 'sinde JSON Patch isteklerinin nasıl işleneceği açıklanır.</span><span class="sxs-lookup"><span data-stu-id="77d12-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="77d12-106">Paket yüklemesi</span><span class="sxs-lookup"><span data-stu-id="77d12-106">Package installation</span></span>

<span data-ttu-id="77d12-107">Uygulamanızda JSON yama desteğini etkinleştirmek için aşağıdaki adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="77d12-107">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="77d12-108">[Microsoft. AspNetCore. Mvc. NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet paketini yükler.</span><span class="sxs-lookup"><span data-stu-id="77d12-108">Install the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="77d12-109">Çağırmak `Startup.ConfigureServices` <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>için projenin metodunu güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="77d12-109">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="77d12-110">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="77d12-110">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="77d12-111">`AddNewtonsoftJson`, MVC hizmeti kayıt yöntemleriyle uyumludur:</span><span class="sxs-lookup"><span data-stu-id="77d12-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="77d12-112">JSON Patch, AddNewtonsoftJson ve System. Text. JSON</span><span class="sxs-lookup"><span data-stu-id="77d12-112">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="77d12-113">`AddNewtonsoftJson``System.Text.Json` **Tüm** JSON içeriğini biçimlendirmek için kullanılan tabanlı giriş ve çıkış biçimlerini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="77d12-113">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="77d12-114">Kullanarak `Newtonsoft.Json`JSON Patch desteği eklemek için, diğer biçimleri değişmeden bırakarak, projenin `Startup.ConfigureServices` yöntemini aşağıdaki gibi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="77d12-114">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="77d12-115">Yukarıdaki kod, `Microsoft.AspNetCore.Mvc.NewtonsoftJson` paketi ve aşağıdaki `using` deyimlerini gerektirir:</span><span class="sxs-lookup"><span data-stu-id="77d12-115">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="77d12-116">PATCH HTTP istek yöntemi</span><span class="sxs-lookup"><span data-stu-id="77d12-116">PATCH HTTP request method</span></span>

<span data-ttu-id="77d12-117">PUT ve [Patch](https://tools.ietf.org/html/rfc5789) yöntemleri, var olan bir kaynağı güncelleştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77d12-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="77d12-118">Bunlar arasındaki fark, PUT 'ın tüm kaynağı değiştirmesi, ancak düzeltme eki yalnızca değişiklikleri belirttiğinde.</span><span class="sxs-lookup"><span data-stu-id="77d12-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="77d12-119">JSON yaması</span><span class="sxs-lookup"><span data-stu-id="77d12-119">JSON Patch</span></span>

<span data-ttu-id="77d12-120">[JSON yaması](https://tools.ietf.org/html/rfc6902) , bir kaynağa uygulanacak güncelleştirmelerin belirtilmesine yönelik bir biçimdir.</span><span class="sxs-lookup"><span data-stu-id="77d12-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="77d12-121">JSON yama belgesinde bir dizi *işlem*vardır.</span><span class="sxs-lookup"><span data-stu-id="77d12-121">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="77d12-122">Her işlem belirli bir değişiklik türünü tanımlar.</span><span class="sxs-lookup"><span data-stu-id="77d12-122">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="77d12-123">Bu tür değişikliklere örnek olarak bir dizi öğesi ekleme veya bir özellik değeri değiştirme sayılabilir.</span><span class="sxs-lookup"><span data-stu-id="77d12-123">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="77d12-124">Örneğin, aşağıdaki JSON belgeleri bir kaynağı, kaynak için bir JSON yama belgesini ve düzeltme eki işlemlerini uygulamanın sonucunu temsil eder.</span><span class="sxs-lookup"><span data-stu-id="77d12-124">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="77d12-125">Kaynak örneği</span><span class="sxs-lookup"><span data-stu-id="77d12-125">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="77d12-126">JSON Patch örneği</span><span class="sxs-lookup"><span data-stu-id="77d12-126">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="77d12-127">Önceki JSON 'da:</span><span class="sxs-lookup"><span data-stu-id="77d12-127">In the preceding JSON:</span></span>

* <span data-ttu-id="77d12-128">`op` Özelliği, işlem türünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="77d12-128">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="77d12-129">`path` Özelliği güncelleştirilecek öğeyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="77d12-129">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="77d12-130">`value` Özelliği yeni değeri sağlar.</span><span class="sxs-lookup"><span data-stu-id="77d12-130">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="77d12-131">Düzeltme ekiyle sonra kaynak</span><span class="sxs-lookup"><span data-stu-id="77d12-131">Resource after patch</span></span>

<span data-ttu-id="77d12-132">Önceki JSON Patch belgesi uygulandıktan sonra kaynak şu şekildedir:</span><span class="sxs-lookup"><span data-stu-id="77d12-132">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="77d12-133">Bir kaynak için bir JSON Patch belgesi uygulanarak yapılan değişiklikler atomik.</span><span class="sxs-lookup"><span data-stu-id="77d12-133">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="77d12-134">Listedeki herhangi bir işlem başarısız olursa, listede hiçbir işlem uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="77d12-134">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="77d12-135">Yol sözdizimi</span><span class="sxs-lookup"><span data-stu-id="77d12-135">Path syntax</span></span>

<span data-ttu-id="77d12-136">Bir işlem nesnesinin [Path](https://tools.ietf.org/html/rfc6901) özelliği düzeyler arasında eğik çizgi içeriyor.</span><span class="sxs-lookup"><span data-stu-id="77d12-136">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="77d12-137">Örneğin, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="77d12-137">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="77d12-138">Sıfır tabanlı dizinler, dizi öğelerini belirtmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77d12-138">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="77d12-139">`addresses` Dizinin ilk öğesi `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="77d12-139">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="77d12-140">`add` Bir dizinin sonuna kadar, dizin numarası yerine bir tire (`-`) kullanın: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="77d12-140">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="77d12-141">İşlemler</span><span class="sxs-lookup"><span data-stu-id="77d12-141">Operations</span></span>

<span data-ttu-id="77d12-142">Aşağıdaki tabloda, [JSON Patch belirtiminde](https://tools.ietf.org/html/rfc6902)tanımlanan desteklenen işlemler gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="77d12-142">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="77d12-143">İşlem</span><span class="sxs-lookup"><span data-stu-id="77d12-143">Operation</span></span>  | <span data-ttu-id="77d12-144">Notlar</span><span class="sxs-lookup"><span data-stu-id="77d12-144">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="77d12-145">Bir özellik veya dizi öğesi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="77d12-145">Add a property or array element.</span></span> <span data-ttu-id="77d12-146">Var olan özellik için: set değeri.</span><span class="sxs-lookup"><span data-stu-id="77d12-146">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="77d12-147">Bir özellik veya dizi öğesi kaldırın.</span><span class="sxs-lookup"><span data-stu-id="77d12-147">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="77d12-148">Aynı konumdaki `remove` ve sonrasında `add` aynı.</span><span class="sxs-lookup"><span data-stu-id="77d12-148">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="77d12-149">`remove` Kaynaktaki değeri kullanarak kaynağından sonra `add` hedefle aynı.</span><span class="sxs-lookup"><span data-stu-id="77d12-149">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="77d12-150">Kaynaktaki değeri `add` kullanarak hedefle aynı olacak şekilde aynı.</span><span class="sxs-lookup"><span data-stu-id="77d12-150">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="77d12-151">Değer: `path` belirtilmişse başarı durum kodu döndürür `value`.</span><span class="sxs-lookup"><span data-stu-id="77d12-151">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="77d12-152">ASP.NET Core JSON yaması</span><span class="sxs-lookup"><span data-stu-id="77d12-152">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="77d12-153">JSON düzeltme ekinin ASP.NET Core uygulanması, [Microsoft. AspNetCore. JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet paketinde sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="77d12-153">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="77d12-154">Eylem yöntemi kodu</span><span class="sxs-lookup"><span data-stu-id="77d12-154">Action method code</span></span>

<span data-ttu-id="77d12-155">Bir API denetleyicisinde, JSON yaması için bir eylem yöntemi:</span><span class="sxs-lookup"><span data-stu-id="77d12-155">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="77d12-156">, `HttpPatch` Özniteliğiyle açıklama eklenir.</span><span class="sxs-lookup"><span data-stu-id="77d12-156">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="77d12-157">`JsonPatchDocument<T>`, Genellikle ile `[FromBody]`kabul eder.</span><span class="sxs-lookup"><span data-stu-id="77d12-157">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="77d12-158">Değişiklikleri `ApplyTo` uygulamak için düzeltme eki belgesindeki çağrılar.</span><span class="sxs-lookup"><span data-stu-id="77d12-158">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="77d12-159">Bir örneği aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="77d12-159">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="77d12-160">Örnek uygulamadaki Bu kod aşağıdaki `Customer` modelle birlikte kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="77d12-160">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="77d12-161">Örnek eylem yöntemi:</span><span class="sxs-lookup"><span data-stu-id="77d12-161">The sample action method:</span></span>

* <span data-ttu-id="77d12-162">Bir `Customer`oluşturur.</span><span class="sxs-lookup"><span data-stu-id="77d12-162">Constructs a `Customer`.</span></span>
* <span data-ttu-id="77d12-163">Düzeltme ekini uygular.</span><span class="sxs-lookup"><span data-stu-id="77d12-163">Applies the patch.</span></span>
* <span data-ttu-id="77d12-164">Yanıtın gövdesinde sonucu döndürür.</span><span class="sxs-lookup"><span data-stu-id="77d12-164">Returns the result in the body of the response.</span></span>

<span data-ttu-id="77d12-165">Gerçek bir uygulamada, kod veritabanı gibi bir mağazadan verileri alır ve düzeltme ekini uyguladıktan sonra veritabanını güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="77d12-165">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="77d12-166">Model durumu</span><span class="sxs-lookup"><span data-stu-id="77d12-166">Model state</span></span>

<span data-ttu-id="77d12-167">Önceki eylem yöntemi örneği, parametrelerinden biri olarak model `ApplyTo` durumunu alan aşırı yüklemesini çağırır.</span><span class="sxs-lookup"><span data-stu-id="77d12-167">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="77d12-168">Bu seçenekle, yanıtlardan hata iletileri alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="77d12-168">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="77d12-169">Aşağıdaki örnekte bir `test` işlem Için 400 hatalı istek yanıtının gövdesi gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="77d12-169">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="77d12-170">Dinamik nesneler</span><span class="sxs-lookup"><span data-stu-id="77d12-170">Dynamic objects</span></span>

<span data-ttu-id="77d12-171">Aşağıdaki eylem yöntemi örneği, dinamik bir nesneye nasıl düzeltme eki uygulanacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="77d12-171">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="77d12-172">Ekleme işlemi</span><span class="sxs-lookup"><span data-stu-id="77d12-172">The add operation</span></span>

* <span data-ttu-id="77d12-173">Bir `path` dizi öğesine işaret ediyorsa: tarafından `path`belirtiden önce yeni bir öğe ekler.</span><span class="sxs-lookup"><span data-stu-id="77d12-173">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="77d12-174">Bir `path` özelliğe işaret ediyorsa: özellik değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="77d12-174">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="77d12-175">Varolmayan `path` bir konuma işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="77d12-175">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="77d12-176">Yama yapılacak kaynak dinamik bir nesnedir: bir özellik ekler.</span><span class="sxs-lookup"><span data-stu-id="77d12-176">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="77d12-177">Yama yapılacak kaynak statik bir nesnese: istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="77d12-177">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="77d12-178">Aşağıdaki örnek düzeltme eki belgesi değerini ayarlar `CustomerName` ve `Order` `Orders` dizinin sonuna bir nesnesi ekler.</span><span class="sxs-lookup"><span data-stu-id="77d12-178">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="77d12-179">Kaldırma işlemi</span><span class="sxs-lookup"><span data-stu-id="77d12-179">The remove operation</span></span>

* <span data-ttu-id="77d12-180">Bir `path` dizi öğesine işaret ediyorsa: öğesini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="77d12-180">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="77d12-181">Bir `path` özelliğe işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="77d12-181">If `path` points to a property:</span></span>
  * <span data-ttu-id="77d12-182">Yayama kaynağı dinamik bir nesne ise: özelliğini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="77d12-182">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="77d12-183">Yama yapılacak kaynak statik bir nesnese:</span><span class="sxs-lookup"><span data-stu-id="77d12-183">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="77d12-184">Özellik null atanabilir ise: null olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="77d12-184">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="77d12-185">Özellik null atanamaz ise, olarak `default<T>`ayarlar.</span><span class="sxs-lookup"><span data-stu-id="77d12-185">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="77d12-186">Aşağıdaki örnek düzeltme eki belgesi null `CustomerName` ve siler `Orders[0]`olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="77d12-186">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="77d12-187">Değiştirme işlemi</span><span class="sxs-lookup"><span data-stu-id="77d12-187">The replace operation</span></span>

<span data-ttu-id="77d12-188">Bu işlem, bir `remove` sonrasında bir `add`ile aynıdır.</span><span class="sxs-lookup"><span data-stu-id="77d12-188">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="77d12-189">Aşağıdaki örnek düzeltme eki belgesi değerini ayarlar `CustomerName` ve yeni `Orders[0]` `Order` bir nesneyle değiştirir:</span><span class="sxs-lookup"><span data-stu-id="77d12-189">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="77d12-190">Taşıma işlemi</span><span class="sxs-lookup"><span data-stu-id="77d12-190">The move operation</span></span>

* <span data-ttu-id="77d12-191">Bir `path` dizi öğesinin işaret ediyorsa: öğesini öğesinin `from` konumuna `path` kopyalar, sonra `remove` `from` öğesi üzerinde bir işlem çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="77d12-191">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="77d12-192">Bir `path` `from` özelliğe işaret ediyorsa: özelliğin değerini `path` özelliğine kopyalar ve sonra `remove` `from` özelliği üzerinde bir işlem çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="77d12-192">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="77d12-193">Varolmayan `path` bir özelliğe işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="77d12-193">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="77d12-194">Yama yapılacak kaynak statik bir nesnese: istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="77d12-194">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="77d12-195">Yaması gereken kaynak dinamik bir nesnedir: özelliği tarafından `from` `path`belirtilen konuma kopyalar, sonra `remove` `from` özellik üzerinde bir işlem çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="77d12-195">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="77d12-196">Aşağıdaki örnek düzeltme eki belgesi:</span><span class="sxs-lookup"><span data-stu-id="77d12-196">The following sample patch document:</span></span>

* <span data-ttu-id="77d12-197">Değerini `Orders[0].OrderName` olarak `CustomerName`kopyalar.</span><span class="sxs-lookup"><span data-stu-id="77d12-197">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="77d12-198">Null `Orders[0].OrderName` olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="77d12-198">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="77d12-199">Öncesine `Orders[1]` `Orders[0]`gider.</span><span class="sxs-lookup"><span data-stu-id="77d12-199">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="77d12-200">Kopyalama işlemi</span><span class="sxs-lookup"><span data-stu-id="77d12-200">The copy operation</span></span>

<span data-ttu-id="77d12-201">Bu işlem, son `move` `remove` adım olmadan işlem ile aynı şekilde aynıdır.</span><span class="sxs-lookup"><span data-stu-id="77d12-201">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="77d12-202">Aşağıdaki örnek düzeltme eki belgesi:</span><span class="sxs-lookup"><span data-stu-id="77d12-202">The following sample patch document:</span></span>

* <span data-ttu-id="77d12-203">Değerini `Orders[0].OrderName` olarak `CustomerName`kopyalar.</span><span class="sxs-lookup"><span data-stu-id="77d12-203">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="77d12-204">Daha `Orders[1]` önce `Orders[0]`bir kopyası ekler.</span><span class="sxs-lookup"><span data-stu-id="77d12-204">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="77d12-205">Test işlemi</span><span class="sxs-lookup"><span data-stu-id="77d12-205">The test operation</span></span>

<span data-ttu-id="77d12-206">Tarafından `path` belirtilen konumdaki değer içinde `value`belirtilen değerden farklıysa, istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="77d12-206">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="77d12-207">Bu durumda, yama belgesindeki diğer tüm işlemler başka bir şekilde başarılı olsa bile, tüm yama isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="77d12-207">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="77d12-208">İşlem `test` , bir eşzamanlılık çakışması olduğunda bir güncelleştirmeyi engellemek için yaygın olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77d12-208">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="77d12-209">Aşağıdaki örnek düzeltme eki belgesinin ilk değeri `CustomerName` "John" ise, test başarısız olursa hiçbir etkisi yoktur:</span><span class="sxs-lookup"><span data-stu-id="77d12-209">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="77d12-210">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="77d12-210">Get the code</span></span>

<span data-ttu-id="77d12-211">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span><span class="sxs-lookup"><span data-stu-id="77d12-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="77d12-212">([İndirme](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="77d12-212">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="77d12-213">Örneği test etmek için, uygulamayı çalıştırın ve aşağıdaki ayarlarla HTTP istekleri gönderin:</span><span class="sxs-lookup"><span data-stu-id="77d12-213">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="77d12-214">'DEKI`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="77d12-214">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="77d12-215">HTTP yöntemi:`PATCH`</span><span class="sxs-lookup"><span data-stu-id="77d12-215">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="77d12-216">Üst bilgi`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="77d12-216">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="77d12-217">Gövde: *JSON proje klasöründen JSON Patch* belgesi örneklerinden birini kopyalayıp yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="77d12-217">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="77d12-218">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="77d12-218">Additional resources</span></span>

* [<span data-ttu-id="77d12-219">IETF RFC 5789 PATCH yöntemi belirtimi</span><span class="sxs-lookup"><span data-stu-id="77d12-219">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="77d12-220">IETF RFC 6902 JSON Patch belirtimi</span><span class="sxs-lookup"><span data-stu-id="77d12-220">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="77d12-221">IETF RFC 6901 JSON Patch yolu biçim belirtimi</span><span class="sxs-lookup"><span data-stu-id="77d12-221">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="77d12-222">[JSON yama belgeleri](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="77d12-222">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="77d12-223">JSON yama belgeleri oluşturmak için kaynakların bağlantılarını içerir.</span><span class="sxs-lookup"><span data-stu-id="77d12-223">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="77d12-224">ASP.NET Core JSON Patch kaynak kodu</span><span class="sxs-lookup"><span data-stu-id="77d12-224">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="77d12-225">Bu makalede, ASP.NET Core Web API 'sinde JSON Patch isteklerinin nasıl işleneceği açıklanır.</span><span class="sxs-lookup"><span data-stu-id="77d12-225">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="77d12-226">PATCH HTTP istek yöntemi</span><span class="sxs-lookup"><span data-stu-id="77d12-226">PATCH HTTP request method</span></span>

<span data-ttu-id="77d12-227">PUT ve [Patch](https://tools.ietf.org/html/rfc5789) yöntemleri, var olan bir kaynağı güncelleştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77d12-227">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="77d12-228">Bunlar arasındaki fark, PUT 'ın tüm kaynağı değiştirmesi, ancak düzeltme eki yalnızca değişiklikleri belirttiğinde.</span><span class="sxs-lookup"><span data-stu-id="77d12-228">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="77d12-229">JSON yaması</span><span class="sxs-lookup"><span data-stu-id="77d12-229">JSON Patch</span></span>

<span data-ttu-id="77d12-230">[JSON yaması](https://tools.ietf.org/html/rfc6902) , bir kaynağa uygulanacak güncelleştirmelerin belirtilmesine yönelik bir biçimdir.</span><span class="sxs-lookup"><span data-stu-id="77d12-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="77d12-231">JSON yama belgesinde bir dizi *işlem*vardır.</span><span class="sxs-lookup"><span data-stu-id="77d12-231">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="77d12-232">Her işlem, bir dizi öğesi ekleme veya bir özellik değerini değiştirme gibi belirli bir değişiklik türünü tanımlar.</span><span class="sxs-lookup"><span data-stu-id="77d12-232">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="77d12-233">Örneğin, aşağıdaki JSON belgeleri bir kaynağı, kaynak için bir JSON yama belgesini ve düzeltme eki işlemlerini uygulamanın sonucunu temsil eder.</span><span class="sxs-lookup"><span data-stu-id="77d12-233">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="77d12-234">Kaynak örneği</span><span class="sxs-lookup"><span data-stu-id="77d12-234">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="77d12-235">JSON Patch örneği</span><span class="sxs-lookup"><span data-stu-id="77d12-235">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="77d12-236">Önceki JSON 'da:</span><span class="sxs-lookup"><span data-stu-id="77d12-236">In the preceding JSON:</span></span>

* <span data-ttu-id="77d12-237">`op` Özelliği, işlem türünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="77d12-237">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="77d12-238">`path` Özelliği güncelleştirilecek öğeyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="77d12-238">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="77d12-239">`value` Özelliği yeni değeri sağlar.</span><span class="sxs-lookup"><span data-stu-id="77d12-239">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="77d12-240">Düzeltme ekiyle sonra kaynak</span><span class="sxs-lookup"><span data-stu-id="77d12-240">Resource after patch</span></span>

<span data-ttu-id="77d12-241">Önceki JSON Patch belgesi uygulandıktan sonra kaynak şu şekildedir:</span><span class="sxs-lookup"><span data-stu-id="77d12-241">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="77d12-242">Bir kaynak için bir JSON Patch belgesi uygulanarak yapılan değişiklikler atomik: listedeki herhangi bir işlem başarısız olursa, listede hiçbir işlem uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="77d12-242">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="77d12-243">Yol sözdizimi</span><span class="sxs-lookup"><span data-stu-id="77d12-243">Path syntax</span></span>

<span data-ttu-id="77d12-244">Bir işlem nesnesinin [Path](https://tools.ietf.org/html/rfc6901) özelliği düzeyler arasında eğik çizgi içeriyor.</span><span class="sxs-lookup"><span data-stu-id="77d12-244">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="77d12-245">Örneğin, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="77d12-245">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="77d12-246">Sıfır tabanlı dizinler, dizi öğelerini belirtmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77d12-246">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="77d12-247">`addresses` Dizinin ilk öğesi `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="77d12-247">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="77d12-248">`add` Bir dizinin sonuna kadar, dizin numarası yerine bir tire (-) kullanın: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="77d12-248">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="77d12-249">İşlemler</span><span class="sxs-lookup"><span data-stu-id="77d12-249">Operations</span></span>

<span data-ttu-id="77d12-250">Aşağıdaki tabloda, [JSON Patch belirtiminde](https://tools.ietf.org/html/rfc6902)tanımlanan desteklenen işlemler gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="77d12-250">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="77d12-251">İşlem</span><span class="sxs-lookup"><span data-stu-id="77d12-251">Operation</span></span>  | <span data-ttu-id="77d12-252">Notlar</span><span class="sxs-lookup"><span data-stu-id="77d12-252">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="77d12-253">Bir özellik veya dizi öğesi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="77d12-253">Add a property or array element.</span></span> <span data-ttu-id="77d12-254">Var olan özellik için: set değeri.</span><span class="sxs-lookup"><span data-stu-id="77d12-254">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="77d12-255">Bir özellik veya dizi öğesi kaldırın.</span><span class="sxs-lookup"><span data-stu-id="77d12-255">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="77d12-256">Aynı konumdaki `remove` ve sonrasında `add` aynı.</span><span class="sxs-lookup"><span data-stu-id="77d12-256">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="77d12-257">`remove` Kaynaktaki değeri kullanarak kaynağından sonra `add` hedefle aynı.</span><span class="sxs-lookup"><span data-stu-id="77d12-257">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="77d12-258">Kaynaktaki değeri `add` kullanarak hedefle aynı olacak şekilde aynı.</span><span class="sxs-lookup"><span data-stu-id="77d12-258">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="77d12-259">Değer: `path` belirtilmişse başarı durum kodu döndürür `value`.</span><span class="sxs-lookup"><span data-stu-id="77d12-259">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="77d12-260">ASP.NET Core 'de JsonPatch</span><span class="sxs-lookup"><span data-stu-id="77d12-260">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="77d12-261">JSON düzeltme ekinin ASP.NET Core uygulanması, [Microsoft. AspNetCore. JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet paketinde sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="77d12-261">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="77d12-262">Paket, [Microsoft. AspnetCore. app](xref:fundamentals/metapackage-app) metapackage 'e dahildir.</span><span class="sxs-lookup"><span data-stu-id="77d12-262">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="77d12-263">Eylem yöntemi kodu</span><span class="sxs-lookup"><span data-stu-id="77d12-263">Action method code</span></span>

<span data-ttu-id="77d12-264">Bir API denetleyicisinde, JSON yaması için bir eylem yöntemi:</span><span class="sxs-lookup"><span data-stu-id="77d12-264">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="77d12-265">, `HttpPatch` Özniteliğiyle açıklama eklenir.</span><span class="sxs-lookup"><span data-stu-id="77d12-265">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="77d12-266">`JsonPatchDocument<T>`, Genellikle ile `[FromBody]`kabul eder.</span><span class="sxs-lookup"><span data-stu-id="77d12-266">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="77d12-267">Değişiklikleri `ApplyTo` uygulamak için düzeltme eki belgesindeki çağrılar.</span><span class="sxs-lookup"><span data-stu-id="77d12-267">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="77d12-268">Bir örneği aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="77d12-268">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="77d12-269">Örnek uygulamadaki Bu kod aşağıdaki `Customer` modelle birlikte kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="77d12-269">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="77d12-270">Örnek eylem yöntemi:</span><span class="sxs-lookup"><span data-stu-id="77d12-270">The sample action method:</span></span>

* <span data-ttu-id="77d12-271">Bir `Customer`oluşturur.</span><span class="sxs-lookup"><span data-stu-id="77d12-271">Constructs a `Customer`.</span></span>
* <span data-ttu-id="77d12-272">Düzeltme ekini uygular.</span><span class="sxs-lookup"><span data-stu-id="77d12-272">Applies the patch.</span></span>
* <span data-ttu-id="77d12-273">Yanıtın gövdesinde sonucu döndürür.</span><span class="sxs-lookup"><span data-stu-id="77d12-273">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="77d12-274">Gerçek bir uygulamada, kod veritabanı gibi bir mağazadan verileri alır ve düzeltme ekini uyguladıktan sonra veritabanını güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="77d12-274">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="77d12-275">Model durumu</span><span class="sxs-lookup"><span data-stu-id="77d12-275">Model state</span></span>

<span data-ttu-id="77d12-276">Önceki eylem yöntemi örneği, parametrelerinden biri olarak model `ApplyTo` durumunu alan aşırı yüklemesini çağırır.</span><span class="sxs-lookup"><span data-stu-id="77d12-276">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="77d12-277">Bu seçenekle, yanıtlardan hata iletileri alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="77d12-277">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="77d12-278">Aşağıdaki örnekte bir `test` işlem Için 400 hatalı istek yanıtının gövdesi gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="77d12-278">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="77d12-279">Dinamik nesneler</span><span class="sxs-lookup"><span data-stu-id="77d12-279">Dynamic objects</span></span>

<span data-ttu-id="77d12-280">Aşağıdaki eylem yöntemi örneği, dinamik bir nesne için bir düzeltme ekinin nasıl uygulanacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="77d12-280">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="77d12-281">Ekleme işlemi</span><span class="sxs-lookup"><span data-stu-id="77d12-281">The add operation</span></span>

* <span data-ttu-id="77d12-282">Bir `path` dizi öğesine işaret ediyorsa: tarafından `path`belirtiden önce yeni bir öğe ekler.</span><span class="sxs-lookup"><span data-stu-id="77d12-282">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="77d12-283">Bir `path` özelliğe işaret ediyorsa: özellik değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="77d12-283">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="77d12-284">Varolmayan `path` bir konuma işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="77d12-284">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="77d12-285">Yama yapılacak kaynak dinamik bir nesnedir: bir özellik ekler.</span><span class="sxs-lookup"><span data-stu-id="77d12-285">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="77d12-286">Yama yapılacak kaynak statik bir nesnese: istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="77d12-286">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="77d12-287">Aşağıdaki örnek düzeltme eki belgesi değerini ayarlar `CustomerName` ve `Order` `Orders` dizinin sonuna bir nesnesi ekler.</span><span class="sxs-lookup"><span data-stu-id="77d12-287">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="77d12-288">Kaldırma işlemi</span><span class="sxs-lookup"><span data-stu-id="77d12-288">The remove operation</span></span>

* <span data-ttu-id="77d12-289">Bir `path` dizi öğesine işaret ediyorsa: öğesini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="77d12-289">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="77d12-290">Bir `path` özelliğe işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="77d12-290">If `path` points to a property:</span></span>
  * <span data-ttu-id="77d12-291">Yayama kaynağı dinamik bir nesne ise: özelliğini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="77d12-291">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="77d12-292">Yama yapılacak kaynak statik bir nesnese:</span><span class="sxs-lookup"><span data-stu-id="77d12-292">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="77d12-293">Özellik null atanabilir ise: null olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="77d12-293">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="77d12-294">Özellik null atanamaz ise, olarak `default<T>`ayarlar.</span><span class="sxs-lookup"><span data-stu-id="77d12-294">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="77d12-295">Aşağıdaki örnek düzeltme eki belgesi null `CustomerName` ve siler `Orders[0]`olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="77d12-295">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="77d12-296">Değiştirme işlemi</span><span class="sxs-lookup"><span data-stu-id="77d12-296">The replace operation</span></span>

<span data-ttu-id="77d12-297">Bu işlem, bir `remove` sonrasında bir `add`ile aynıdır.</span><span class="sxs-lookup"><span data-stu-id="77d12-297">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="77d12-298">Aşağıdaki örnek düzeltme eki belgesi değerini ayarlar `CustomerName` ve yeni `Orders[0]` `Order` bir nesneyle değiştirir.</span><span class="sxs-lookup"><span data-stu-id="77d12-298">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="77d12-299">Taşıma işlemi</span><span class="sxs-lookup"><span data-stu-id="77d12-299">The move operation</span></span>

* <span data-ttu-id="77d12-300">Bir `path` dizi öğesinin işaret ediyorsa: öğesini öğesinin `from` konumuna `path` kopyalar, sonra `remove` `from` öğesi üzerinde bir işlem çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="77d12-300">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="77d12-301">Bir `path` `from` özelliğe işaret ediyorsa: özelliğin değerini `path` özelliğine kopyalar ve sonra `remove` `from` özelliği üzerinde bir işlem çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="77d12-301">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="77d12-302">Varolmayan `path` bir özelliğe işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="77d12-302">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="77d12-303">Yama yapılacak kaynak statik bir nesnese: istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="77d12-303">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="77d12-304">Yaması gereken kaynak dinamik bir nesnedir: özelliği tarafından `from` `path`belirtilen konuma kopyalar, sonra `remove` `from` özellik üzerinde bir işlem çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="77d12-304">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="77d12-305">Aşağıdaki örnek düzeltme eki belgesi:</span><span class="sxs-lookup"><span data-stu-id="77d12-305">The following sample patch document:</span></span>

* <span data-ttu-id="77d12-306">Değerini `Orders[0].OrderName` olarak `CustomerName`kopyalar.</span><span class="sxs-lookup"><span data-stu-id="77d12-306">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="77d12-307">Null `Orders[0].OrderName` olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="77d12-307">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="77d12-308">Öncesine `Orders[1]` `Orders[0]`gider.</span><span class="sxs-lookup"><span data-stu-id="77d12-308">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="77d12-309">Kopyalama işlemi</span><span class="sxs-lookup"><span data-stu-id="77d12-309">The copy operation</span></span>

<span data-ttu-id="77d12-310">Bu işlem, son `move` `remove` adım olmadan işlem ile aynı şekilde aynıdır.</span><span class="sxs-lookup"><span data-stu-id="77d12-310">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="77d12-311">Aşağıdaki örnek düzeltme eki belgesi:</span><span class="sxs-lookup"><span data-stu-id="77d12-311">The following sample patch document:</span></span>

* <span data-ttu-id="77d12-312">Değerini `Orders[0].OrderName` olarak `CustomerName`kopyalar.</span><span class="sxs-lookup"><span data-stu-id="77d12-312">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="77d12-313">Daha `Orders[1]` önce `Orders[0]`bir kopyası ekler.</span><span class="sxs-lookup"><span data-stu-id="77d12-313">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="77d12-314">Test işlemi</span><span class="sxs-lookup"><span data-stu-id="77d12-314">The test operation</span></span>

<span data-ttu-id="77d12-315">Tarafından `path` belirtilen konumdaki değer içinde `value`belirtilen değerden farklıysa, istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="77d12-315">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="77d12-316">Bu durumda, yama belgesindeki diğer tüm işlemler başka bir şekilde başarılı olsa bile, tüm yama isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="77d12-316">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="77d12-317">İşlem `test` , bir eşzamanlılık çakışması olduğunda bir güncelleştirmeyi engellemek için yaygın olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77d12-317">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="77d12-318">Aşağıdaki örnek düzeltme eki belgesinin ilk değeri `CustomerName` "John" ise, test başarısız olursa hiçbir etkisi yoktur:</span><span class="sxs-lookup"><span data-stu-id="77d12-318">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="77d12-319">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="77d12-319">Get the code</span></span>

<span data-ttu-id="77d12-320">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="77d12-320">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="77d12-321">([İndirme](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="77d12-321">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="77d12-322">Örneği test etmek için, uygulamayı çalıştırın ve aşağıdaki ayarlarla HTTP istekleri gönderin:</span><span class="sxs-lookup"><span data-stu-id="77d12-322">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="77d12-323">'DEKI`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="77d12-323">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="77d12-324">HTTP yöntemi:`PATCH`</span><span class="sxs-lookup"><span data-stu-id="77d12-324">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="77d12-325">Üst bilgi`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="77d12-325">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="77d12-326">Gövde: *JSON proje klasöründen JSON Patch* belgesi örneklerinden birini kopyalayıp yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="77d12-326">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="77d12-327">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="77d12-327">Additional resources</span></span>

* [<span data-ttu-id="77d12-328">IETF RFC 5789 PATCH yöntemi belirtimi</span><span class="sxs-lookup"><span data-stu-id="77d12-328">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="77d12-329">IETF RFC 6902 JSON Patch belirtimi</span><span class="sxs-lookup"><span data-stu-id="77d12-329">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="77d12-330">IETF RFC 6901 JSON Patch yolu biçim belirtimi</span><span class="sxs-lookup"><span data-stu-id="77d12-330">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="77d12-331">[JSON yama belgeleri](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="77d12-331">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="77d12-332">JSON yama belgeleri oluşturmak için kaynakların bağlantılarını içerir.</span><span class="sxs-lookup"><span data-stu-id="77d12-332">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="77d12-333">ASP.NET Core JSON Patch kaynak kodu</span><span class="sxs-lookup"><span data-stu-id="77d12-333">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
