---
title: JsonPatch in ASP.NET Core web API
author: rick-anderson
description: ASP.NET Core web API'sinde JSON Yama isteklerini nasıl işleyeceğinizi öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
uid: web-api/jsonpatch
ms.openlocfilehash: be4115e870dac818aeb6b1e65ddfb21e89d9cf25
ms.sourcegitcommit: 9675db7bf4b67ae269f9226b6f6f439b5cce4603
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80625876"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="2967d-103">JsonPatch in ASP.NET Core web API</span><span class="sxs-lookup"><span data-stu-id="2967d-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="2967d-104">Yazar: [Tom Dykstra](https://github.com/tdykstra) ve [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="2967d-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2967d-105">Bu makalede, ASP.NET Core web API'de JSON Yama isteklerinin nasıl işleyeceğini açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="2967d-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="2967d-106">Paket kurulumu</span><span class="sxs-lookup"><span data-stu-id="2967d-106">Package installation</span></span>

<span data-ttu-id="2967d-107">Uygulamanızda JSON Patch desteğini etkinleştirmek için aşağıdaki adımları tamamlayın:</span><span class="sxs-lookup"><span data-stu-id="2967d-107">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="2967d-108">[Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet paketini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="2967d-108">Install the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="2967d-109">Aramak <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>için projenin `Startup.ConfigureServices` yöntemini güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="2967d-109">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="2967d-110">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2967d-110">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="2967d-111">`AddNewtonsoftJson`MVC hizmet kayıt yöntemleri ile uyumludur:</span><span class="sxs-lookup"><span data-stu-id="2967d-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="2967d-112">JSON Yama, AddNewtonsoftJson ve System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="2967d-112">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="2967d-113">`AddNewtonsoftJson``System.Text.Json` **tüm** JSON içeriğini biçimlendirmek için kullanılan -tabanlı giriş ve çıktı formatters değiştirir.</span><span class="sxs-lookup"><span data-stu-id="2967d-113">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="2967d-114">JSON Patch için destek `Newtonsoft.Json`eklemek için, diğer konuları değiştirmeden bırakırken, projenin `Startup.ConfigureServices` yöntemini aşağıdaki gibi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="2967d-114">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="2967d-115">Önceki kod `Microsoft.AspNetCore.Mvc.NewtonsoftJson` paketi ve aşağıdaki `using` ifadeleri gerektirir:</span><span class="sxs-lookup"><span data-stu-id="2967d-115">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="2967d-116">PATCH HTTP istek yöntemi</span><span class="sxs-lookup"><span data-stu-id="2967d-116">PATCH HTTP request method</span></span>

<span data-ttu-id="2967d-117">PUT ve [PATCH](https://tools.ietf.org/html/rfc5789) yöntemleri varolan bir kaynağı güncelleştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2967d-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="2967d-118">Bunlar arasındaki fark, PUT'un tüm kaynağın yerini alması, PATCH ise yalnızca değişiklikleri belirtir.</span><span class="sxs-lookup"><span data-stu-id="2967d-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="2967d-119">JSON Yama</span><span class="sxs-lookup"><span data-stu-id="2967d-119">JSON Patch</span></span>

<span data-ttu-id="2967d-120">[JSON Yama,](https://tools.ietf.org/html/rfc6902) bir kaynağa uygulanacak güncelleştirmeleri belirtmek için bir biçimdir.</span><span class="sxs-lookup"><span data-stu-id="2967d-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="2967d-121">JSON Yama belgesinde bir dizi *işlem*vardır.</span><span class="sxs-lookup"><span data-stu-id="2967d-121">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="2967d-122">Her işlem belirli bir değişiklik türünü tanımlar.</span><span class="sxs-lookup"><span data-stu-id="2967d-122">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="2967d-123">Bu tür değişikliklere örnek olarak bir dizi öğesi eklemek veya bir özellik değerini değiştirmek verilebilir.</span><span class="sxs-lookup"><span data-stu-id="2967d-123">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="2967d-124">Örneğin, aşağıdaki JSON belgeleri bir kaynağı, kaynak için bir JSON Yama belgesini ve Yama işlemlerini uygulama sonucunu temsil emektedir.</span><span class="sxs-lookup"><span data-stu-id="2967d-124">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="2967d-125">Kaynak örneği</span><span class="sxs-lookup"><span data-stu-id="2967d-125">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="2967d-126">JSON yama örneği</span><span class="sxs-lookup"><span data-stu-id="2967d-126">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="2967d-127">Önceki JSON olarak:</span><span class="sxs-lookup"><span data-stu-id="2967d-127">In the preceding JSON:</span></span>

* <span data-ttu-id="2967d-128">Özellik, `op` işlem türünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="2967d-128">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="2967d-129">Özellik, `path` güncelleştirilen öğeyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="2967d-129">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="2967d-130">Özellik `value` yeni değeri sağlar.</span><span class="sxs-lookup"><span data-stu-id="2967d-130">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="2967d-131">Yama sonrası kaynak</span><span class="sxs-lookup"><span data-stu-id="2967d-131">Resource after patch</span></span>

<span data-ttu-id="2967d-132">Yukarıdaki JSON Yama belgesini uyguladıktan sonra kaynak aşağıda vesiyonu aşağıda veda edebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="2967d-132">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="2967d-133">Bir kaynağa JSON Yama belgesi uygulanarak yapılan değişiklikler atomiktir.</span><span class="sxs-lookup"><span data-stu-id="2967d-133">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="2967d-134">Listedeki herhangi bir işlem başarısız olursa, listede hiçbir işlem uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="2967d-134">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="2967d-135">Yol sözdizimi</span><span class="sxs-lookup"><span data-stu-id="2967d-135">Path syntax</span></span>

<span data-ttu-id="2967d-136">İşlem nesnesinin [yol](https://tools.ietf.org/html/rfc6901) özelliği düzeyleri arasında kesikler vardır.</span><span class="sxs-lookup"><span data-stu-id="2967d-136">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="2967d-137">Örneğin, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="2967d-137">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="2967d-138">Dizi öğelerini belirtmek için sıfır tabanlı dizinler kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2967d-138">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="2967d-139">`addresses` Dizinin ilk öğesi `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="2967d-139">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="2967d-140">Dizinin `add` sonuna kadar, dizin numarası yerine`-`tire ( ) `/addresses/-`kullanın: .</span><span class="sxs-lookup"><span data-stu-id="2967d-140">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="2967d-141">İşlemler</span><span class="sxs-lookup"><span data-stu-id="2967d-141">Operations</span></span>

<span data-ttu-id="2967d-142">Aşağıdaki tablo, [JSON Yama belirtiminde](https://tools.ietf.org/html/rfc6902)tanımlandığı şekilde desteklenen işlemleri gösterir:</span><span class="sxs-lookup"><span data-stu-id="2967d-142">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="2967d-143">İşlem</span><span class="sxs-lookup"><span data-stu-id="2967d-143">Operation</span></span>  | <span data-ttu-id="2967d-144">Notlar</span><span class="sxs-lookup"><span data-stu-id="2967d-144">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="2967d-145">Bir özellik veya dizi öğesi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2967d-145">Add a property or array element.</span></span> <span data-ttu-id="2967d-146">Varolan özellik için: değer ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="2967d-146">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="2967d-147">Bir özellik veya dizi öğesini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="2967d-147">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="2967d-148">Aynı `remove` yerde `add` takip edildiği gibi.</span><span class="sxs-lookup"><span data-stu-id="2967d-148">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="2967d-149">Kaynaktan `remove` gelen değeri `add` kullanarak hedef tarafından takip aynı.</span><span class="sxs-lookup"><span data-stu-id="2967d-149">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="2967d-150">Kaynaktan `add` gelen değeri kullanarak hedefe gitmekle aynı.</span><span class="sxs-lookup"><span data-stu-id="2967d-150">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="2967d-151">`path` Değer = sağlanan ise başarı `value`durum kodunu döndürün .</span><span class="sxs-lookup"><span data-stu-id="2967d-151">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="2967d-152">JSON Patch ASP.NET Çekirdek</span><span class="sxs-lookup"><span data-stu-id="2967d-152">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="2967d-153">JSON Patch'in ASP.NET Core uygulaması [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet paketinde sağlanır.</span><span class="sxs-lookup"><span data-stu-id="2967d-153">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="2967d-154">Eylem yöntemi kodu</span><span class="sxs-lookup"><span data-stu-id="2967d-154">Action method code</span></span>

<span data-ttu-id="2967d-155">Bir API denetleyicisinde, JSON Patch için bir eylem yöntemi:</span><span class="sxs-lookup"><span data-stu-id="2967d-155">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="2967d-156">Öznitelik ile `HttpPatch` açıklamalı.</span><span class="sxs-lookup"><span data-stu-id="2967d-156">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="2967d-157">Genellikle `JsonPatchDocument<T>` `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="2967d-157">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="2967d-158">Değişiklikleri `ApplyTo` uygulamak için yama belgesini çağırır.</span><span class="sxs-lookup"><span data-stu-id="2967d-158">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="2967d-159">Bir örneği aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="2967d-159">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="2967d-160">Örnek uygulamadan bu kod aşağıdaki `Customer` modelle çalışır:</span><span class="sxs-lookup"><span data-stu-id="2967d-160">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="2967d-161">Örnek eylem yöntemi:</span><span class="sxs-lookup"><span data-stu-id="2967d-161">The sample action method:</span></span>

* <span data-ttu-id="2967d-162">Bir `Customer`.</span><span class="sxs-lookup"><span data-stu-id="2967d-162">Constructs a `Customer`.</span></span>
* <span data-ttu-id="2967d-163">Yama uygular.</span><span class="sxs-lookup"><span data-stu-id="2967d-163">Applies the patch.</span></span>
* <span data-ttu-id="2967d-164">Yanıtı gövdesinde sonucu döndürür.</span><span class="sxs-lookup"><span data-stu-id="2967d-164">Returns the result in the body of the response.</span></span>

<span data-ttu-id="2967d-165">Gerçek bir uygulamada, kod veritabanı gibi bir mağazadan verileri alır ve yama uyguladıktan sonra veritabanını günceller.</span><span class="sxs-lookup"><span data-stu-id="2967d-165">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="2967d-166">Model durumu</span><span class="sxs-lookup"><span data-stu-id="2967d-166">Model state</span></span>

<span data-ttu-id="2967d-167">Önceki eylem yöntemi örneği, model `ApplyTo` durumunu parametrelerinden biri olarak alan aşırı yüklemeyi çağırır.</span><span class="sxs-lookup"><span data-stu-id="2967d-167">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="2967d-168">Bu seçenekle, yanıtlarda hata iletileri alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2967d-168">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="2967d-169">Aşağıdaki örnek, bir `test` işlem için 400 Kötü İstek yanıtının gövdesini gösterir:</span><span class="sxs-lookup"><span data-stu-id="2967d-169">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="2967d-170">Dinamik nesneler</span><span class="sxs-lookup"><span data-stu-id="2967d-170">Dynamic objects</span></span>

<span data-ttu-id="2967d-171">Aşağıdaki eylem yöntemi örneği, dinamik bir nesneye yamanın nasıl uygulanacağı gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="2967d-171">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="2967d-172">Ekleme işlemi</span><span class="sxs-lookup"><span data-stu-id="2967d-172">The add operation</span></span>

* <span data-ttu-id="2967d-173">Bir `path` dizi öğesine işaret ederse: `path`'' tarafından belirtilenöğeden önce yeni öğe ekler.</span><span class="sxs-lookup"><span data-stu-id="2967d-173">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="2967d-174">Bir `path` özelliği işaret ederse: özellik değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2967d-174">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="2967d-175">Var `path` olmayan bir konuma işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="2967d-175">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="2967d-176">Yama için kaynak dinamik bir nesne ise: bir özellik ekler.</span><span class="sxs-lookup"><span data-stu-id="2967d-176">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="2967d-177">Yama için kaynak statik bir nesne ise: istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="2967d-177">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="2967d-178">Aşağıdaki örnek yama belgesi, `CustomerName` `Orders` dizinin `Order` değerini ayarlar ve dizinin sonuna bir nesne ekler.</span><span class="sxs-lookup"><span data-stu-id="2967d-178">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="2967d-179">Kaldırma işlemi</span><span class="sxs-lookup"><span data-stu-id="2967d-179">The remove operation</span></span>

* <span data-ttu-id="2967d-180">Bir `path` dizi öğesini işaret ederse: öğeyi kaldırır.</span><span class="sxs-lookup"><span data-stu-id="2967d-180">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="2967d-181">Bir `path` özelliği işaret ederse:</span><span class="sxs-lookup"><span data-stu-id="2967d-181">If `path` points to a property:</span></span>
  * <span data-ttu-id="2967d-182">Yama için kaynak dinamik bir nesne ise: özelliği kaldırır.</span><span class="sxs-lookup"><span data-stu-id="2967d-182">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="2967d-183">Yama için kaynak statik bir nesne ise:</span><span class="sxs-lookup"><span data-stu-id="2967d-183">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="2967d-184">Özellik nullable ise: null ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2967d-184">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="2967d-185">Özellik nullable değilse, `default<T>`ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2967d-185">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="2967d-186">Aşağıdaki örnek yama `CustomerName` belge null `Orders[0]`ayarlar ve siler:</span><span class="sxs-lookup"><span data-stu-id="2967d-186">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="2967d-187">Değiştirme işlemi</span><span class="sxs-lookup"><span data-stu-id="2967d-187">The replace operation</span></span>

<span data-ttu-id="2967d-188">Bu işlem işlevsel olarak bir `remove` `add`takip ile aynıdır.</span><span class="sxs-lookup"><span data-stu-id="2967d-188">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="2967d-189">Aşağıdaki örnek yama belgesi `CustomerName` değerini ayarlar `Orders[0]`ve `Order` yerine yeni bir nesne alır:</span><span class="sxs-lookup"><span data-stu-id="2967d-189">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="2967d-190">Taşıma işlemi</span><span class="sxs-lookup"><span data-stu-id="2967d-190">The move operation</span></span>

* <span data-ttu-id="2967d-191">Bir `path` dizi öğesine işaret `from` ediyorsa: `path` öğenin konumuna `remove` öğekopyalar, sonra `from` öğe üzerinde bir işlem çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="2967d-191">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="2967d-192">Bir `path` özelliği işaret ediyorsa: `from` özelliğin `path` değerini mülke `remove` kopyalar, `from` sonra özellik üzerinde bir işlem çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="2967d-192">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="2967d-193">Var `path` olmayan bir özelliğe işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="2967d-193">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="2967d-194">Yama için kaynak statik bir nesne ise: istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="2967d-194">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="2967d-195">Yama için kaynak dinamik bir nesne `from` ise: tarafından `path`belirtilen konuma `remove` özelliği `from` kopyalar, sonra özellik üzerinde bir işlem çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="2967d-195">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="2967d-196">Aşağıdaki örnek yama belgesi:</span><span class="sxs-lookup"><span data-stu-id="2967d-196">The following sample patch document:</span></span>

* <span data-ttu-id="2967d-197">Değerini `Orders[0].OrderName` `CustomerName`kopyalar.</span><span class="sxs-lookup"><span data-stu-id="2967d-197">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="2967d-198">Null `Orders[0].OrderName` ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2967d-198">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="2967d-199">Öncesine `Orders[1]` `Orders[0]`taşınır.</span><span class="sxs-lookup"><span data-stu-id="2967d-199">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="2967d-200">Kopyalama işlemi</span><span class="sxs-lookup"><span data-stu-id="2967d-200">The copy operation</span></span>

<span data-ttu-id="2967d-201">Bu işlem işlevsel olarak son `move` `remove` adım olmadan bir işlem olarak aynıdır.</span><span class="sxs-lookup"><span data-stu-id="2967d-201">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="2967d-202">Aşağıdaki örnek yama belgesi:</span><span class="sxs-lookup"><span data-stu-id="2967d-202">The following sample patch document:</span></span>

* <span data-ttu-id="2967d-203">Değerini `Orders[0].OrderName` `CustomerName`kopyalar.</span><span class="sxs-lookup"><span data-stu-id="2967d-203">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="2967d-204">Önce'nin `Orders[1]` `Orders[0]`bir kopyasını ekler.</span><span class="sxs-lookup"><span data-stu-id="2967d-204">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="2967d-205">Test işlemi</span><span class="sxs-lookup"><span data-stu-id="2967d-205">The test operation</span></span>

<span data-ttu-id="2967d-206">Belirtilen konumdaki değer, `path` sağlanan değerden `value`farklıysa, istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="2967d-206">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="2967d-207">Bu durumda, yama belgesindeki diğer tüm işlemler başarılı olsa bile tüm PATCH isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="2967d-207">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="2967d-208">İşlem `test` genellikle eşzamanlılık çakışması olduğunda bir güncelleştirmeyi önlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2967d-208">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="2967d-209">Test başarısız olduğundan, aşağıdaki örnek yama `CustomerName` belgesinin ilk değeri "John" ise hiçbir etkisi yoktur:</span><span class="sxs-lookup"><span data-stu-id="2967d-209">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="2967d-210">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="2967d-210">Get the code</span></span>

<span data-ttu-id="2967d-211">[Örnek kodu görüntüleyin veya indirin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples)</span><span class="sxs-lookup"><span data-stu-id="2967d-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="2967d-212">([Nasıl indirilir).](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="2967d-212">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="2967d-213">Örneği test etmek için uygulamayı çalıştırın ve aşağıdaki ayarlarla HTTP istekleri gönderin:</span><span class="sxs-lookup"><span data-stu-id="2967d-213">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="2967d-214">Url:`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="2967d-214">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="2967d-215">HTTP yöntemi:`PATCH`</span><span class="sxs-lookup"><span data-stu-id="2967d-215">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="2967d-216">Üstbilgi:`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="2967d-216">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="2967d-217">Gövde: *JSON* proje klasöründen JSON yama belge örneklerinden birini kopyalayın ve yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="2967d-217">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2967d-218">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2967d-218">Additional resources</span></span>

* [<span data-ttu-id="2967d-219">IETF RFC 5789 PATCH yöntemi belirtimi</span><span class="sxs-lookup"><span data-stu-id="2967d-219">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="2967d-220">IETF RFC 6902 JSON Patch belirtimi</span><span class="sxs-lookup"><span data-stu-id="2967d-220">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="2967d-221">IETF RFC 6901 JSON Patch yol formatı spec</span><span class="sxs-lookup"><span data-stu-id="2967d-221">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="2967d-222">[JSON Patch belgeleri](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="2967d-222">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="2967d-223">JSON Patch belgeleri oluşturmak için kaynaklara bağlantılar içerir.</span><span class="sxs-lookup"><span data-stu-id="2967d-223">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="2967d-224">ASP.NET Core JSON Patch kaynak kodu</span><span class="sxs-lookup"><span data-stu-id="2967d-224">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2967d-225">Bu makalede, ASP.NET Core web API'de JSON Yama isteklerinin nasıl işleyeceğini açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="2967d-225">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="2967d-226">PATCH HTTP istek yöntemi</span><span class="sxs-lookup"><span data-stu-id="2967d-226">PATCH HTTP request method</span></span>

<span data-ttu-id="2967d-227">PUT ve [PATCH](https://tools.ietf.org/html/rfc5789) yöntemleri varolan bir kaynağı güncelleştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2967d-227">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="2967d-228">Bunlar arasındaki fark, PUT'un tüm kaynağın yerini alması, PATCH ise yalnızca değişiklikleri belirtir.</span><span class="sxs-lookup"><span data-stu-id="2967d-228">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="2967d-229">JSON Yama</span><span class="sxs-lookup"><span data-stu-id="2967d-229">JSON Patch</span></span>

<span data-ttu-id="2967d-230">[JSON Yama,](https://tools.ietf.org/html/rfc6902) bir kaynağa uygulanacak güncelleştirmeleri belirtmek için bir biçimdir.</span><span class="sxs-lookup"><span data-stu-id="2967d-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="2967d-231">JSON Yama belgesinde bir dizi *işlem*vardır.</span><span class="sxs-lookup"><span data-stu-id="2967d-231">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="2967d-232">Her işlem, bir dizi öğesi ekleme veya bir özellik değerini değiştirme gibi belirli bir değişiklik türünü tanımlar.</span><span class="sxs-lookup"><span data-stu-id="2967d-232">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="2967d-233">Örneğin, aşağıdaki JSON belgeleri bir kaynağı, kaynak için bir JSON yama belgesini ve yama işlemlerini uygulama sonucunu temsil emektedir.</span><span class="sxs-lookup"><span data-stu-id="2967d-233">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="2967d-234">Kaynak örneği</span><span class="sxs-lookup"><span data-stu-id="2967d-234">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="2967d-235">JSON yama örneği</span><span class="sxs-lookup"><span data-stu-id="2967d-235">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="2967d-236">Önceki JSON olarak:</span><span class="sxs-lookup"><span data-stu-id="2967d-236">In the preceding JSON:</span></span>

* <span data-ttu-id="2967d-237">Özellik, `op` işlem türünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="2967d-237">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="2967d-238">Özellik, `path` güncelleştirilen öğeyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="2967d-238">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="2967d-239">Özellik `value` yeni değeri sağlar.</span><span class="sxs-lookup"><span data-stu-id="2967d-239">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="2967d-240">Yama sonrası kaynak</span><span class="sxs-lookup"><span data-stu-id="2967d-240">Resource after patch</span></span>

<span data-ttu-id="2967d-241">Yukarıdaki JSON Yama belgesini uyguladıktan sonra kaynak aşağıda vesiyonu aşağıda veda edebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="2967d-241">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="2967d-242">Bir kaynağa JSON Yama belgesi uygulanarak yapılan değişiklikler atomiktir: listedeki herhangi bir işlem başarısız olursa, listede hiçbir işlem uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="2967d-242">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="2967d-243">Yol sözdizimi</span><span class="sxs-lookup"><span data-stu-id="2967d-243">Path syntax</span></span>

<span data-ttu-id="2967d-244">İşlem nesnesinin [yol](https://tools.ietf.org/html/rfc6901) özelliği düzeyleri arasında kesikler vardır.</span><span class="sxs-lookup"><span data-stu-id="2967d-244">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="2967d-245">Örneğin, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="2967d-245">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="2967d-246">Dizi öğelerini belirtmek için sıfır tabanlı dizinler kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2967d-246">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="2967d-247">`addresses` Dizinin ilk öğesi `/addresses/0`.</span><span class="sxs-lookup"><span data-stu-id="2967d-247">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="2967d-248">Dizinin `add` sonuna kadar dizin numarası yerine tire (-) kullanın: `/addresses/-`.</span><span class="sxs-lookup"><span data-stu-id="2967d-248">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="2967d-249">İşlemler</span><span class="sxs-lookup"><span data-stu-id="2967d-249">Operations</span></span>

<span data-ttu-id="2967d-250">Aşağıdaki tablo, [JSON Yama belirtiminde](https://tools.ietf.org/html/rfc6902)tanımlandığı şekilde desteklenen işlemleri gösterir:</span><span class="sxs-lookup"><span data-stu-id="2967d-250">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="2967d-251">İşlem</span><span class="sxs-lookup"><span data-stu-id="2967d-251">Operation</span></span>  | <span data-ttu-id="2967d-252">Notlar</span><span class="sxs-lookup"><span data-stu-id="2967d-252">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="2967d-253">Bir özellik veya dizi öğesi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="2967d-253">Add a property or array element.</span></span> <span data-ttu-id="2967d-254">Varolan özellik için: değer ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="2967d-254">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="2967d-255">Bir özellik veya dizi öğesini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="2967d-255">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="2967d-256">Aynı `remove` yerde `add` takip edildiği gibi.</span><span class="sxs-lookup"><span data-stu-id="2967d-256">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="2967d-257">Kaynaktan `remove` gelen değeri `add` kullanarak hedef tarafından takip aynı.</span><span class="sxs-lookup"><span data-stu-id="2967d-257">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="2967d-258">Kaynaktan `add` gelen değeri kullanarak hedefe gitmekle aynı.</span><span class="sxs-lookup"><span data-stu-id="2967d-258">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="2967d-259">`path` Değer = sağlanan ise başarı `value`durum kodunu döndürün .</span><span class="sxs-lookup"><span data-stu-id="2967d-259">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="2967d-260">JsonPatch ASP.NET Core içinde</span><span class="sxs-lookup"><span data-stu-id="2967d-260">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="2967d-261">JSON Patch'in ASP.NET Core uygulaması [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet paketinde sağlanır.</span><span class="sxs-lookup"><span data-stu-id="2967d-261">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="2967d-262">Paket [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) meta paketine dahildir.</span><span class="sxs-lookup"><span data-stu-id="2967d-262">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="2967d-263">Eylem yöntemi kodu</span><span class="sxs-lookup"><span data-stu-id="2967d-263">Action method code</span></span>

<span data-ttu-id="2967d-264">Bir API denetleyicisinde, JSON Patch için bir eylem yöntemi:</span><span class="sxs-lookup"><span data-stu-id="2967d-264">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="2967d-265">Öznitelik ile `HttpPatch` açıklamalı.</span><span class="sxs-lookup"><span data-stu-id="2967d-265">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="2967d-266">Genellikle `JsonPatchDocument<T>` `[FromBody]`.</span><span class="sxs-lookup"><span data-stu-id="2967d-266">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="2967d-267">Değişiklikleri `ApplyTo` uygulamak için yama belgesini çağırır.</span><span class="sxs-lookup"><span data-stu-id="2967d-267">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="2967d-268">Bir örneği aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="2967d-268">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="2967d-269">Örnek uygulamadan alınan bu kod `Customer` aşağıdaki modelle çalışır.</span><span class="sxs-lookup"><span data-stu-id="2967d-269">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="2967d-270">Örnek eylem yöntemi:</span><span class="sxs-lookup"><span data-stu-id="2967d-270">The sample action method:</span></span>

* <span data-ttu-id="2967d-271">Bir `Customer`.</span><span class="sxs-lookup"><span data-stu-id="2967d-271">Constructs a `Customer`.</span></span>
* <span data-ttu-id="2967d-272">Yama uygular.</span><span class="sxs-lookup"><span data-stu-id="2967d-272">Applies the patch.</span></span>
* <span data-ttu-id="2967d-273">Yanıtı gövdesinde sonucu döndürür.</span><span class="sxs-lookup"><span data-stu-id="2967d-273">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="2967d-274">Gerçek bir uygulamada, kod veritabanı gibi bir mağazadan verileri alır ve yama uyguladıktan sonra veritabanını günceller.</span><span class="sxs-lookup"><span data-stu-id="2967d-274">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="2967d-275">Model durumu</span><span class="sxs-lookup"><span data-stu-id="2967d-275">Model state</span></span>

<span data-ttu-id="2967d-276">Önceki eylem yöntemi örneği, model `ApplyTo` durumunu parametrelerinden biri olarak alan aşırı yüklemeyi çağırır.</span><span class="sxs-lookup"><span data-stu-id="2967d-276">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="2967d-277">Bu seçenekle, yanıtlarda hata iletileri alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2967d-277">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="2967d-278">Aşağıdaki örnek, bir `test` işlem için 400 Kötü İstek yanıtının gövdesini gösterir:</span><span class="sxs-lookup"><span data-stu-id="2967d-278">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="2967d-279">Dinamik nesneler</span><span class="sxs-lookup"><span data-stu-id="2967d-279">Dynamic objects</span></span>

<span data-ttu-id="2967d-280">Aşağıdaki eylem yöntemi örneği, dinamik bir nesneye yamanın nasıl uygulanacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="2967d-280">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="2967d-281">Ekleme işlemi</span><span class="sxs-lookup"><span data-stu-id="2967d-281">The add operation</span></span>

* <span data-ttu-id="2967d-282">Bir `path` dizi öğesine işaret ederse: `path`'' tarafından belirtilenöğeden önce yeni öğe ekler.</span><span class="sxs-lookup"><span data-stu-id="2967d-282">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="2967d-283">Bir `path` özelliği işaret ederse: özellik değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2967d-283">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="2967d-284">Var `path` olmayan bir konuma işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="2967d-284">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="2967d-285">Yama için kaynak dinamik bir nesne ise: bir özellik ekler.</span><span class="sxs-lookup"><span data-stu-id="2967d-285">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="2967d-286">Yama için kaynak statik bir nesne ise: istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="2967d-286">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="2967d-287">Aşağıdaki örnek yama belgesi, `CustomerName` `Orders` dizinin `Order` değerini ayarlar ve dizinin sonuna bir nesne ekler.</span><span class="sxs-lookup"><span data-stu-id="2967d-287">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="2967d-288">Kaldırma işlemi</span><span class="sxs-lookup"><span data-stu-id="2967d-288">The remove operation</span></span>

* <span data-ttu-id="2967d-289">Bir `path` dizi öğesini işaret ederse: öğeyi kaldırır.</span><span class="sxs-lookup"><span data-stu-id="2967d-289">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="2967d-290">Bir `path` özelliği işaret ederse:</span><span class="sxs-lookup"><span data-stu-id="2967d-290">If `path` points to a property:</span></span>
  * <span data-ttu-id="2967d-291">Yama için kaynak dinamik bir nesne ise: özelliği kaldırır.</span><span class="sxs-lookup"><span data-stu-id="2967d-291">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="2967d-292">Yama için kaynak statik bir nesne ise:</span><span class="sxs-lookup"><span data-stu-id="2967d-292">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="2967d-293">Özellik nullable ise: null ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2967d-293">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="2967d-294">Özellik nullable değilse, `default<T>`ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2967d-294">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="2967d-295">Aşağıdaki örnek yama `CustomerName` belgesi null `Orders[0]`ayarlar ve siler.</span><span class="sxs-lookup"><span data-stu-id="2967d-295">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="2967d-296">Değiştirme işlemi</span><span class="sxs-lookup"><span data-stu-id="2967d-296">The replace operation</span></span>

<span data-ttu-id="2967d-297">Bu işlem işlevsel olarak bir `remove` `add`takip ile aynıdır.</span><span class="sxs-lookup"><span data-stu-id="2967d-297">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="2967d-298">Aşağıdaki örnek yama belgesi `CustomerName` değerini ayarlar `Orders[0]`ve `Order` yerine yeni bir nesne alır.</span><span class="sxs-lookup"><span data-stu-id="2967d-298">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="2967d-299">Taşıma işlemi</span><span class="sxs-lookup"><span data-stu-id="2967d-299">The move operation</span></span>

* <span data-ttu-id="2967d-300">Bir `path` dizi öğesine işaret `from` ediyorsa: `path` öğenin konumuna `remove` öğekopyalar, sonra `from` öğe üzerinde bir işlem çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="2967d-300">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="2967d-301">Bir `path` özelliği işaret ediyorsa: `from` özelliğin `path` değerini mülke `remove` kopyalar, `from` sonra özellik üzerinde bir işlem çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="2967d-301">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="2967d-302">Var `path` olmayan bir özelliğe işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="2967d-302">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="2967d-303">Yama için kaynak statik bir nesne ise: istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="2967d-303">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="2967d-304">Yama için kaynak dinamik bir nesne `from` ise: tarafından `path`belirtilen konuma `remove` özelliği `from` kopyalar, sonra özellik üzerinde bir işlem çalıştırır.</span><span class="sxs-lookup"><span data-stu-id="2967d-304">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="2967d-305">Aşağıdaki örnek yama belgesi:</span><span class="sxs-lookup"><span data-stu-id="2967d-305">The following sample patch document:</span></span>

* <span data-ttu-id="2967d-306">Değerini `Orders[0].OrderName` `CustomerName`kopyalar.</span><span class="sxs-lookup"><span data-stu-id="2967d-306">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="2967d-307">Null `Orders[0].OrderName` ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2967d-307">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="2967d-308">Öncesine `Orders[1]` `Orders[0]`taşınır.</span><span class="sxs-lookup"><span data-stu-id="2967d-308">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="2967d-309">Kopyalama işlemi</span><span class="sxs-lookup"><span data-stu-id="2967d-309">The copy operation</span></span>

<span data-ttu-id="2967d-310">Bu işlem işlevsel olarak son `move` `remove` adım olmadan bir işlem olarak aynıdır.</span><span class="sxs-lookup"><span data-stu-id="2967d-310">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="2967d-311">Aşağıdaki örnek yama belgesi:</span><span class="sxs-lookup"><span data-stu-id="2967d-311">The following sample patch document:</span></span>

* <span data-ttu-id="2967d-312">Değerini `Orders[0].OrderName` `CustomerName`kopyalar.</span><span class="sxs-lookup"><span data-stu-id="2967d-312">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="2967d-313">Önce'nin `Orders[1]` `Orders[0]`bir kopyasını ekler.</span><span class="sxs-lookup"><span data-stu-id="2967d-313">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="2967d-314">Test işlemi</span><span class="sxs-lookup"><span data-stu-id="2967d-314">The test operation</span></span>

<span data-ttu-id="2967d-315">Belirtilen konumdaki değer, `path` sağlanan değerden `value`farklıysa, istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="2967d-315">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="2967d-316">Bu durumda, yama belgesindeki diğer tüm işlemler başarılı olsa bile tüm PATCH isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="2967d-316">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="2967d-317">İşlem `test` genellikle eşzamanlılık çakışması olduğunda bir güncelleştirmeyi önlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2967d-317">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="2967d-318">Test başarısız olduğundan, aşağıdaki örnek yama `CustomerName` belgesinin ilk değeri "John" ise hiçbir etkisi yoktur:</span><span class="sxs-lookup"><span data-stu-id="2967d-318">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="2967d-319">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="2967d-319">Get the code</span></span>

<span data-ttu-id="2967d-320">[Örnek kodu görüntüleyin veya indirin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)</span><span class="sxs-lookup"><span data-stu-id="2967d-320">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="2967d-321">([Nasıl indirilir).](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="2967d-321">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="2967d-322">Örneği test etmek için uygulamayı çalıştırın ve aşağıdaki ayarlarla HTTP istekleri gönderin:</span><span class="sxs-lookup"><span data-stu-id="2967d-322">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="2967d-323">Url:`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="2967d-323">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="2967d-324">HTTP yöntemi:`PATCH`</span><span class="sxs-lookup"><span data-stu-id="2967d-324">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="2967d-325">Üstbilgi:`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="2967d-325">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="2967d-326">Gövde: *JSON* proje klasöründen JSON yama belge örneklerinden birini kopyalayın ve yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="2967d-326">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2967d-327">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2967d-327">Additional resources</span></span>

* [<span data-ttu-id="2967d-328">IETF RFC 5789 PATCH yöntemi belirtimi</span><span class="sxs-lookup"><span data-stu-id="2967d-328">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="2967d-329">IETF RFC 6902 JSON Patch belirtimi</span><span class="sxs-lookup"><span data-stu-id="2967d-329">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="2967d-330">IETF RFC 6901 JSON Patch yol formatı spec</span><span class="sxs-lookup"><span data-stu-id="2967d-330">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="2967d-331">[JSON Patch belgeleri](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="2967d-331">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="2967d-332">JSON Patch belgeleri oluşturmak için kaynaklara bağlantılar içerir.</span><span class="sxs-lookup"><span data-stu-id="2967d-332">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="2967d-333">ASP.NET Core JSON Patch kaynak kodu</span><span class="sxs-lookup"><span data-stu-id="2967d-333">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
