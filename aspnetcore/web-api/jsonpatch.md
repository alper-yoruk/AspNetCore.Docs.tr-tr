---
title: ASP.NET Core Web API 'sinde JsonPatch
author: rick-anderson
description: ASP.NET Core Web API 'sindeki JSON Patch isteklerini nasıl işleyeceğinizi öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 04/02/2020
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
uid: web-api/jsonpatch
ms.openlocfilehash: da507974b88c21de22e2c7a56950943207565138
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060553"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="9b17d-103">ASP.NET Core Web API 'sinde JsonPatch</span><span class="sxs-lookup"><span data-stu-id="9b17d-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="9b17d-104">, [Tom Dykstra](https://github.com/tdykstra) ve [Kirk larkabağı](https://github.com/serpent5) tarafından</span><span class="sxs-lookup"><span data-stu-id="9b17d-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9b17d-105">Bu makalede, ASP.NET Core Web API 'sinde JSON Patch isteklerinin nasıl işleneceği açıklanır.</span><span class="sxs-lookup"><span data-stu-id="9b17d-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="9b17d-106">Paket yüklemesi</span><span class="sxs-lookup"><span data-stu-id="9b17d-106">Package installation</span></span>

<span data-ttu-id="9b17d-107">Uygulamanızda JSON yama desteğini etkinleştirmek için aşağıdaki adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="9b17d-107">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="9b17d-108">[`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/)NuGet paketini yükler.</span><span class="sxs-lookup"><span data-stu-id="9b17d-108">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="9b17d-109">`Startup.ConfigureServices`Çağırmak için projenin metodunu güncelleştirin <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> .</span><span class="sxs-lookup"><span data-stu-id="9b17d-109">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="9b17d-110">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="9b17d-110">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="9b17d-111">`AddNewtonsoftJson` , MVC hizmeti kayıt yöntemleriyle uyumludur:</span><span class="sxs-lookup"><span data-stu-id="9b17d-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="9b17d-112">JSON Patch, AddNewtonsoftJson ve System.Text.Js</span><span class="sxs-lookup"><span data-stu-id="9b17d-112">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="9b17d-113">`AddNewtonsoftJson``System.Text.Json` **Tüm** JSON içeriğini biçimlendirmek için kullanılan tabanlı giriş ve çıkış biçimlerini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="9b17d-113">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="9b17d-114">Kullanarak JSON Patch desteği eklemek için `Newtonsoft.Json` , diğer biçimleri değişmeden bırakarak, projenin `Startup.ConfigureServices` yöntemini aşağıdaki gibi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="9b17d-114">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="9b17d-115">Yukarıdaki kod, `Microsoft.AspNetCore.Mvc.NewtonsoftJson` paketi ve aşağıdaki `using` deyimlerini gerektirir:</span><span class="sxs-lookup"><span data-stu-id="9b17d-115">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="9b17d-116">PATCH HTTP istek yöntemi</span><span class="sxs-lookup"><span data-stu-id="9b17d-116">PATCH HTTP request method</span></span>

<span data-ttu-id="9b17d-117">PUT ve [Patch](https://tools.ietf.org/html/rfc5789) yöntemleri, var olan bir kaynağı güncelleştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9b17d-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="9b17d-118">Bunlar arasındaki fark, PUT 'ın tüm kaynağı değiştirmesi, ancak düzeltme eki yalnızca değişiklikleri belirttiğinde.</span><span class="sxs-lookup"><span data-stu-id="9b17d-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="9b17d-119">JSON yaması</span><span class="sxs-lookup"><span data-stu-id="9b17d-119">JSON Patch</span></span>

<span data-ttu-id="9b17d-120">[JSON yaması](https://tools.ietf.org/html/rfc6902) , bir kaynağa uygulanacak güncelleştirmelerin belirtilmesine yönelik bir biçimdir.</span><span class="sxs-lookup"><span data-stu-id="9b17d-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="9b17d-121">JSON yama belgesinde bir dizi *işlem* vardır.</span><span class="sxs-lookup"><span data-stu-id="9b17d-121">A JSON Patch document has an array of *operations* .</span></span> <span data-ttu-id="9b17d-122">Her işlem belirli bir değişiklik türünü tanımlar.</span><span class="sxs-lookup"><span data-stu-id="9b17d-122">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="9b17d-123">Bu tür değişikliklere örnek olarak bir dizi öğesi ekleme veya bir özellik değeri değiştirme sayılabilir.</span><span class="sxs-lookup"><span data-stu-id="9b17d-123">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="9b17d-124">Örneğin, aşağıdaki JSON belgeleri bir kaynağı, kaynak için bir JSON yama belgesini ve düzeltme eki işlemlerini uygulamanın sonucunu temsil eder.</span><span class="sxs-lookup"><span data-stu-id="9b17d-124">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="9b17d-125">Kaynak örneği</span><span class="sxs-lookup"><span data-stu-id="9b17d-125">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="9b17d-126">JSON Patch örneği</span><span class="sxs-lookup"><span data-stu-id="9b17d-126">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="9b17d-127">Yukarıdaki JSON kodunda:</span><span class="sxs-lookup"><span data-stu-id="9b17d-127">In the preceding JSON:</span></span>

* <span data-ttu-id="9b17d-128">`op`Özelliği, işlem türünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="9b17d-128">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="9b17d-129">`path`Özelliği güncelleştirilecek öğeyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="9b17d-129">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="9b17d-130">`value`Özelliği yeni değeri sağlar.</span><span class="sxs-lookup"><span data-stu-id="9b17d-130">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="9b17d-131">Düzeltme ekiyle sonra kaynak</span><span class="sxs-lookup"><span data-stu-id="9b17d-131">Resource after patch</span></span>

<span data-ttu-id="9b17d-132">Önceki JSON Patch belgesi uygulandıktan sonra kaynak şu şekildedir:</span><span class="sxs-lookup"><span data-stu-id="9b17d-132">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="9b17d-133">Bir kaynak için bir JSON Patch belgesi uygulanarak yapılan değişiklikler atomik.</span><span class="sxs-lookup"><span data-stu-id="9b17d-133">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="9b17d-134">Listedeki herhangi bir işlem başarısız olursa, listede hiçbir işlem uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="9b17d-134">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="9b17d-135">Yol sözdizimi</span><span class="sxs-lookup"><span data-stu-id="9b17d-135">Path syntax</span></span>

<span data-ttu-id="9b17d-136">Bir işlem nesnesinin [Path](https://tools.ietf.org/html/rfc6901) özelliği düzeyler arasında eğik çizgi içeriyor.</span><span class="sxs-lookup"><span data-stu-id="9b17d-136">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="9b17d-137">Örneğin, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="9b17d-137">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="9b17d-138">Sıfır tabanlı dizinler, dizi öğelerini belirtmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9b17d-138">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="9b17d-139">Dizinin ilk öğesi `addresses` `/addresses/0` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-139">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="9b17d-140">`add`Bir dizinin sonuna kadar, `-` Dizin numarası yerine bir tire () kullanın: `/addresses/-` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-140">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="9b17d-141">İşlemler</span><span class="sxs-lookup"><span data-stu-id="9b17d-141">Operations</span></span>

<span data-ttu-id="9b17d-142">Aşağıdaki tabloda, [JSON Patch belirtiminde](https://tools.ietf.org/html/rfc6902)tanımlanan desteklenen işlemler gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="9b17d-142">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="9b17d-143">İşlem</span><span class="sxs-lookup"><span data-stu-id="9b17d-143">Operation</span></span>  | <span data-ttu-id="9b17d-144">Notlar</span><span class="sxs-lookup"><span data-stu-id="9b17d-144">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="9b17d-145">Bir özellik veya dizi öğesi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9b17d-145">Add a property or array element.</span></span> <span data-ttu-id="9b17d-146">Var olan özellik için: set değeri.</span><span class="sxs-lookup"><span data-stu-id="9b17d-146">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="9b17d-147">Bir özellik veya dizi öğesi kaldırın.</span><span class="sxs-lookup"><span data-stu-id="9b17d-147">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="9b17d-148">Aynı `remove` konumdaki ve sonrasında aynı `add` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-148">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="9b17d-149">Kaynaktaki `remove` `add` değeri kullanarak kaynağından sonra hedefle aynı.</span><span class="sxs-lookup"><span data-stu-id="9b17d-149">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="9b17d-150">`add`Kaynaktaki değeri kullanarak hedefle aynı olacak şekilde aynı.</span><span class="sxs-lookup"><span data-stu-id="9b17d-150">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="9b17d-151">Değer: belirtilmişse başarı durum kodu döndürür `path` `value` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-151">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="9b17d-152">ASP.NET Core JSON yaması</span><span class="sxs-lookup"><span data-stu-id="9b17d-152">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="9b17d-153">JSON düzeltme ekinin ASP.NET Core uygulanması [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet paketinde sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="9b17d-153">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="9b17d-154">Eylem yöntemi kodu</span><span class="sxs-lookup"><span data-stu-id="9b17d-154">Action method code</span></span>

<span data-ttu-id="9b17d-155">Bir API denetleyicisinde, JSON yaması için bir eylem yöntemi:</span><span class="sxs-lookup"><span data-stu-id="9b17d-155">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="9b17d-156">, Özniteliğiyle açıklama eklenir `HttpPatch` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-156">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="9b17d-157">`JsonPatchDocument<T>`, Genellikle ile kabul eder `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-157">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="9b17d-158">`ApplyTo`Değişiklikleri uygulamak için düzeltme eki belgesindeki çağrılar.</span><span class="sxs-lookup"><span data-stu-id="9b17d-158">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="9b17d-159">Aşağıda bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="9b17d-159">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="9b17d-160">Örnek uygulamadaki Bu kod aşağıdaki modelle birlikte kullanılabilir `Customer` :</span><span class="sxs-lookup"><span data-stu-id="9b17d-160">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="9b17d-161">Örnek eylem yöntemi:</span><span class="sxs-lookup"><span data-stu-id="9b17d-161">The sample action method:</span></span>

* <span data-ttu-id="9b17d-162">Bir oluşturur `Customer` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-162">Constructs a `Customer`.</span></span>
* <span data-ttu-id="9b17d-163">Düzeltme ekini uygular.</span><span class="sxs-lookup"><span data-stu-id="9b17d-163">Applies the patch.</span></span>
* <span data-ttu-id="9b17d-164">Yanıtın gövdesinde sonucu döndürür.</span><span class="sxs-lookup"><span data-stu-id="9b17d-164">Returns the result in the body of the response.</span></span>

<span data-ttu-id="9b17d-165">Gerçek bir uygulamada, kod veritabanı gibi bir mağazadan verileri alır ve düzeltme ekini uyguladıktan sonra veritabanını güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="9b17d-165">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="9b17d-166">Model durumu</span><span class="sxs-lookup"><span data-stu-id="9b17d-166">Model state</span></span>

<span data-ttu-id="9b17d-167">Önceki eylem yöntemi örneği, `ApplyTo` parametrelerinden biri olarak model durumunu alan aşırı yüklemesini çağırır.</span><span class="sxs-lookup"><span data-stu-id="9b17d-167">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="9b17d-168">Bu seçenekle, yanıtlardan hata iletileri alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9b17d-168">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="9b17d-169">Aşağıdaki örnekte bir işlem için 400 Hatalı Istek yanıtının gövdesi gösterilmektedir `test` :</span><span class="sxs-lookup"><span data-stu-id="9b17d-169">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="9b17d-170">Dinamik nesneler</span><span class="sxs-lookup"><span data-stu-id="9b17d-170">Dynamic objects</span></span>

<span data-ttu-id="9b17d-171">Aşağıdaki eylem yöntemi örneği, dinamik bir nesneye nasıl düzeltme eki uygulanacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="9b17d-171">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="9b17d-172">Ekleme işlemi</span><span class="sxs-lookup"><span data-stu-id="9b17d-172">The add operation</span></span>

* <span data-ttu-id="9b17d-173">`path`Bir dizi öğesine işaret ediyorsa: tarafından belirtiden önce yeni bir öğe ekler `path` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-173">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="9b17d-174">`path`Bir özelliğe işaret ediyorsa: özellik değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="9b17d-174">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="9b17d-175">`path`Varolmayan bir konuma işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="9b17d-175">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="9b17d-176">Yama yapılacak kaynak dinamik bir nesnedir: bir özellik ekler.</span><span class="sxs-lookup"><span data-stu-id="9b17d-176">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="9b17d-177">Yama yapılacak kaynak statik bir nesnese: istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="9b17d-177">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="9b17d-178">Aşağıdaki örnek düzeltme eki belgesi değerini ayarlar `CustomerName` ve `Order` dizinin sonuna bir nesnesi ekler `Orders` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-178">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="9b17d-179">Kaldırma işlemi</span><span class="sxs-lookup"><span data-stu-id="9b17d-179">The remove operation</span></span>

* <span data-ttu-id="9b17d-180">`path`Bir dizi öğesine işaret ediyorsa: öğesini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="9b17d-180">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="9b17d-181">`path`Bir özelliğe işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="9b17d-181">If `path` points to a property:</span></span>
  * <span data-ttu-id="9b17d-182">Yayama kaynağı dinamik bir nesne ise: özelliğini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="9b17d-182">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="9b17d-183">Yama yapılacak kaynak statik bir nesnese:</span><span class="sxs-lookup"><span data-stu-id="9b17d-183">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="9b17d-184">Özellik null atanabilir ise: null olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="9b17d-184">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="9b17d-185">Özellik null atanamaz ise, olarak ayarlar `default<T>` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-185">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="9b17d-186">Aşağıdaki örnek düzeltme eki belgesi `CustomerName` null ve siler olarak ayarlanır `Orders[0]` :</span><span class="sxs-lookup"><span data-stu-id="9b17d-186">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="9b17d-187">Değiştirme işlemi</span><span class="sxs-lookup"><span data-stu-id="9b17d-187">The replace operation</span></span>

<span data-ttu-id="9b17d-188">Bu işlem, bir `remove` sonrasında bir ile aynıdır `add` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-188">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="9b17d-189">Aşağıdaki örnek düzeltme eki belgesi değerini ayarlar `CustomerName` ve `Orders[0]` Yeni bir `Order` nesneyle değiştirir:</span><span class="sxs-lookup"><span data-stu-id="9b17d-189">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="9b17d-190">Taşıma işlemi</span><span class="sxs-lookup"><span data-stu-id="9b17d-190">The move operation</span></span>

* <span data-ttu-id="9b17d-191">`path`Bir dizi öğesinin işaret ediyorsa: öğesini öğesinin `from` konumuna kopyalar `path` , sonra `remove` öğesi üzerinde bir işlem çalıştırır `from` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-191">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="9b17d-192">`path`Bir özelliğe işaret ediyorsa: özelliğin değerini özelliğine kopyalar `from` `path` ve sonra `remove` özelliği üzerinde bir işlem çalıştırır `from` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-192">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="9b17d-193">`path`Varolmayan bir özelliğe işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="9b17d-193">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="9b17d-194">Yama yapılacak kaynak statik bir nesnese: istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="9b17d-194">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="9b17d-195">Yaması gereken kaynak dinamik bir nesnedir: `from` özelliği tarafından belirtilen konuma kopyalar `path` , sonra `remove` özellik üzerinde bir işlem çalıştırır `from` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-195">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="9b17d-196">Aşağıdaki örnek düzeltme eki belgesi:</span><span class="sxs-lookup"><span data-stu-id="9b17d-196">The following sample patch document:</span></span>

* <span data-ttu-id="9b17d-197">Değerini `Orders[0].OrderName` olarak kopyalar `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-197">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="9b17d-198">`Orders[0].OrderName`Null olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="9b17d-198">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="9b17d-199">`Orders[1]`Öncesine gider `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-199">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="9b17d-200">Kopyalama işlemi</span><span class="sxs-lookup"><span data-stu-id="9b17d-200">The copy operation</span></span>

<span data-ttu-id="9b17d-201">Bu işlem, `move` son adım olmadan işlem ile aynı şekilde aynıdır `remove` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-201">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="9b17d-202">Aşağıdaki örnek düzeltme eki belgesi:</span><span class="sxs-lookup"><span data-stu-id="9b17d-202">The following sample patch document:</span></span>

* <span data-ttu-id="9b17d-203">Değerini `Orders[0].OrderName` olarak kopyalar `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-203">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="9b17d-204">Daha önce bir kopyası `Orders[1]` ekler `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-204">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="9b17d-205">Test işlemi</span><span class="sxs-lookup"><span data-stu-id="9b17d-205">The test operation</span></span>

<span data-ttu-id="9b17d-206">Tarafından belirtilen konumdaki değer `path` içinde belirtilen değerden farklıysa `value` , istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="9b17d-206">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="9b17d-207">Bu durumda, yama belgesindeki diğer tüm işlemler başka bir şekilde başarılı olsa bile, tüm yama isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="9b17d-207">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="9b17d-208">İşlem, bir `test` eşzamanlılık çakışması olduğunda bir güncelleştirmeyi engellemek için yaygın olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9b17d-208">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="9b17d-209">Aşağıdaki örnek düzeltme eki belgesinin ilk değeri `CustomerName` "John" ise, test başarısız olursa hiçbir etkisi yoktur:</span><span class="sxs-lookup"><span data-stu-id="9b17d-209">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="9b17d-210">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="9b17d-210">Get the code</span></span>

<span data-ttu-id="9b17d-211">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span><span class="sxs-lookup"><span data-stu-id="9b17d-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="9b17d-212">([İndirme](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9b17d-212">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="9b17d-213">Örneği test etmek için, uygulamayı çalıştırın ve aşağıdaki ayarlarla HTTP istekleri gönderin:</span><span class="sxs-lookup"><span data-stu-id="9b17d-213">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="9b17d-214">'DEKI `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="9b17d-214">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="9b17d-215">HTTP yöntemi: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="9b17d-215">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="9b17d-216">Üst bilgi `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="9b17d-216">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="9b17d-217">Gövde: *JSON proje klasöründen JSON Patch* belgesi örneklerinden birini kopyalayıp yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="9b17d-217">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9b17d-218">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9b17d-218">Additional resources</span></span>

* [<span data-ttu-id="9b17d-219">IETF RFC 5789 PATCH yöntemi belirtimi</span><span class="sxs-lookup"><span data-stu-id="9b17d-219">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="9b17d-220">IETF RFC 6902 JSON Patch belirtimi</span><span class="sxs-lookup"><span data-stu-id="9b17d-220">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="9b17d-221">IETF RFC 6901 JSON Patch yolu biçim belirtimi</span><span class="sxs-lookup"><span data-stu-id="9b17d-221">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="9b17d-222">[JSON yama belgeleri](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="9b17d-222">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="9b17d-223">JSON yama belgeleri oluşturmak için kaynakların bağlantılarını içerir.</span><span class="sxs-lookup"><span data-stu-id="9b17d-223">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="9b17d-224">ASP.NET Core JSON Patch kaynak kodu</span><span class="sxs-lookup"><span data-stu-id="9b17d-224">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9b17d-225">Bu makalede, ASP.NET Core Web API 'sinde JSON Patch isteklerinin nasıl işleneceği açıklanır.</span><span class="sxs-lookup"><span data-stu-id="9b17d-225">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="9b17d-226">PATCH HTTP istek yöntemi</span><span class="sxs-lookup"><span data-stu-id="9b17d-226">PATCH HTTP request method</span></span>

<span data-ttu-id="9b17d-227">PUT ve [Patch](https://tools.ietf.org/html/rfc5789) yöntemleri, var olan bir kaynağı güncelleştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9b17d-227">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="9b17d-228">Bunlar arasındaki fark, PUT 'ın tüm kaynağı değiştirmesi, ancak düzeltme eki yalnızca değişiklikleri belirttiğinde.</span><span class="sxs-lookup"><span data-stu-id="9b17d-228">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="9b17d-229">JSON yaması</span><span class="sxs-lookup"><span data-stu-id="9b17d-229">JSON Patch</span></span>

<span data-ttu-id="9b17d-230">[JSON yaması](https://tools.ietf.org/html/rfc6902) , bir kaynağa uygulanacak güncelleştirmelerin belirtilmesine yönelik bir biçimdir.</span><span class="sxs-lookup"><span data-stu-id="9b17d-230">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="9b17d-231">JSON yama belgesinde bir dizi *işlem* vardır.</span><span class="sxs-lookup"><span data-stu-id="9b17d-231">A JSON Patch document has an array of *operations* .</span></span> <span data-ttu-id="9b17d-232">Her işlem, bir dizi öğesi ekleme veya bir özellik değerini değiştirme gibi belirli bir değişiklik türünü tanımlar.</span><span class="sxs-lookup"><span data-stu-id="9b17d-232">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="9b17d-233">Örneğin, aşağıdaki JSON belgeleri bir kaynağı, kaynak için bir JSON yama belgesini ve düzeltme eki işlemlerini uygulamanın sonucunu temsil eder.</span><span class="sxs-lookup"><span data-stu-id="9b17d-233">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="9b17d-234">Kaynak örneği</span><span class="sxs-lookup"><span data-stu-id="9b17d-234">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="9b17d-235">JSON Patch örneği</span><span class="sxs-lookup"><span data-stu-id="9b17d-235">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="9b17d-236">Yukarıdaki JSON kodunda:</span><span class="sxs-lookup"><span data-stu-id="9b17d-236">In the preceding JSON:</span></span>

* <span data-ttu-id="9b17d-237">`op`Özelliği, işlem türünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="9b17d-237">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="9b17d-238">`path`Özelliği güncelleştirilecek öğeyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="9b17d-238">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="9b17d-239">`value`Özelliği yeni değeri sağlar.</span><span class="sxs-lookup"><span data-stu-id="9b17d-239">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="9b17d-240">Düzeltme ekiyle sonra kaynak</span><span class="sxs-lookup"><span data-stu-id="9b17d-240">Resource after patch</span></span>

<span data-ttu-id="9b17d-241">Önceki JSON Patch belgesi uygulandıktan sonra kaynak şu şekildedir:</span><span class="sxs-lookup"><span data-stu-id="9b17d-241">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="9b17d-242">Bir kaynak için bir JSON Patch belgesi uygulanarak yapılan değişiklikler atomik: listedeki herhangi bir işlem başarısız olursa, listede hiçbir işlem uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="9b17d-242">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="9b17d-243">Yol sözdizimi</span><span class="sxs-lookup"><span data-stu-id="9b17d-243">Path syntax</span></span>

<span data-ttu-id="9b17d-244">Bir işlem nesnesinin [Path](https://tools.ietf.org/html/rfc6901) özelliği düzeyler arasında eğik çizgi içeriyor.</span><span class="sxs-lookup"><span data-stu-id="9b17d-244">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="9b17d-245">Örneğin, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="9b17d-245">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="9b17d-246">Sıfır tabanlı dizinler, dizi öğelerini belirtmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9b17d-246">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="9b17d-247">Dizinin ilk öğesi `addresses` `/addresses/0` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-247">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="9b17d-248">`add`Bir dizinin sonuna kadar, dizin numarası yerine bir tire (-) kullanın: `/addresses/-` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-248">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="9b17d-249">İşlemler</span><span class="sxs-lookup"><span data-stu-id="9b17d-249">Operations</span></span>

<span data-ttu-id="9b17d-250">Aşağıdaki tabloda, [JSON Patch belirtiminde](https://tools.ietf.org/html/rfc6902)tanımlanan desteklenen işlemler gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="9b17d-250">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="9b17d-251">İşlem</span><span class="sxs-lookup"><span data-stu-id="9b17d-251">Operation</span></span>  | <span data-ttu-id="9b17d-252">Notlar</span><span class="sxs-lookup"><span data-stu-id="9b17d-252">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="9b17d-253">Bir özellik veya dizi öğesi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="9b17d-253">Add a property or array element.</span></span> <span data-ttu-id="9b17d-254">Var olan özellik için: set değeri.</span><span class="sxs-lookup"><span data-stu-id="9b17d-254">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="9b17d-255">Bir özellik veya dizi öğesi kaldırın.</span><span class="sxs-lookup"><span data-stu-id="9b17d-255">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="9b17d-256">Aynı `remove` konumdaki ve sonrasında aynı `add` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-256">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="9b17d-257">Kaynaktaki `remove` `add` değeri kullanarak kaynağından sonra hedefle aynı.</span><span class="sxs-lookup"><span data-stu-id="9b17d-257">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="9b17d-258">`add`Kaynaktaki değeri kullanarak hedefle aynı olacak şekilde aynı.</span><span class="sxs-lookup"><span data-stu-id="9b17d-258">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="9b17d-259">Değer: belirtilmişse başarı durum kodu döndürür `path` `value` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-259">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="9b17d-260">ASP.NET Core 'de JsonPatch</span><span class="sxs-lookup"><span data-stu-id="9b17d-260">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="9b17d-261">JSON düzeltme ekinin ASP.NET Core uygulanması [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet paketinde sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="9b17d-261">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="9b17d-262">Paket, [Microsoft. AspnetCore. app](xref:fundamentals/metapackage-app) metapackage 'e dahildir.</span><span class="sxs-lookup"><span data-stu-id="9b17d-262">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="9b17d-263">Eylem yöntemi kodu</span><span class="sxs-lookup"><span data-stu-id="9b17d-263">Action method code</span></span>

<span data-ttu-id="9b17d-264">Bir API denetleyicisinde, JSON yaması için bir eylem yöntemi:</span><span class="sxs-lookup"><span data-stu-id="9b17d-264">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="9b17d-265">, Özniteliğiyle açıklama eklenir `HttpPatch` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-265">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="9b17d-266">`JsonPatchDocument<T>`, Genellikle ile kabul eder `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-266">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="9b17d-267">`ApplyTo`Değişiklikleri uygulamak için düzeltme eki belgesindeki çağrılar.</span><span class="sxs-lookup"><span data-stu-id="9b17d-267">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="9b17d-268">Aşağıda bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="9b17d-268">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="9b17d-269">Örnek uygulamadaki Bu kod aşağıdaki modelle birlikte kullanılabilir `Customer` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-269">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="9b17d-270">Örnek eylem yöntemi:</span><span class="sxs-lookup"><span data-stu-id="9b17d-270">The sample action method:</span></span>

* <span data-ttu-id="9b17d-271">Bir oluşturur `Customer` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-271">Constructs a `Customer`.</span></span>
* <span data-ttu-id="9b17d-272">Düzeltme ekini uygular.</span><span class="sxs-lookup"><span data-stu-id="9b17d-272">Applies the patch.</span></span>
* <span data-ttu-id="9b17d-273">Yanıtın gövdesinde sonucu döndürür.</span><span class="sxs-lookup"><span data-stu-id="9b17d-273">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="9b17d-274">Gerçek bir uygulamada, kod veritabanı gibi bir mağazadan verileri alır ve düzeltme ekini uyguladıktan sonra veritabanını güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="9b17d-274">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="9b17d-275">Model durumu</span><span class="sxs-lookup"><span data-stu-id="9b17d-275">Model state</span></span>

<span data-ttu-id="9b17d-276">Önceki eylem yöntemi örneği, `ApplyTo` parametrelerinden biri olarak model durumunu alan aşırı yüklemesini çağırır.</span><span class="sxs-lookup"><span data-stu-id="9b17d-276">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="9b17d-277">Bu seçenekle, yanıtlardan hata iletileri alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9b17d-277">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="9b17d-278">Aşağıdaki örnekte bir işlem için 400 Hatalı Istek yanıtının gövdesi gösterilmektedir `test` :</span><span class="sxs-lookup"><span data-stu-id="9b17d-278">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="9b17d-279">Dinamik nesneler</span><span class="sxs-lookup"><span data-stu-id="9b17d-279">Dynamic objects</span></span>

<span data-ttu-id="9b17d-280">Aşağıdaki eylem yöntemi örneği, dinamik bir nesne için bir düzeltme ekinin nasıl uygulanacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="9b17d-280">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="9b17d-281">Ekleme işlemi</span><span class="sxs-lookup"><span data-stu-id="9b17d-281">The add operation</span></span>

* <span data-ttu-id="9b17d-282">`path`Bir dizi öğesine işaret ediyorsa: tarafından belirtiden önce yeni bir öğe ekler `path` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-282">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="9b17d-283">`path`Bir özelliğe işaret ediyorsa: özellik değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="9b17d-283">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="9b17d-284">`path`Varolmayan bir konuma işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="9b17d-284">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="9b17d-285">Yama yapılacak kaynak dinamik bir nesnedir: bir özellik ekler.</span><span class="sxs-lookup"><span data-stu-id="9b17d-285">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="9b17d-286">Yama yapılacak kaynak statik bir nesnese: istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="9b17d-286">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="9b17d-287">Aşağıdaki örnek düzeltme eki belgesi değerini ayarlar `CustomerName` ve `Order` dizinin sonuna bir nesnesi ekler `Orders` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-287">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="9b17d-288">Kaldırma işlemi</span><span class="sxs-lookup"><span data-stu-id="9b17d-288">The remove operation</span></span>

* <span data-ttu-id="9b17d-289">`path`Bir dizi öğesine işaret ediyorsa: öğesini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="9b17d-289">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="9b17d-290">`path`Bir özelliğe işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="9b17d-290">If `path` points to a property:</span></span>
  * <span data-ttu-id="9b17d-291">Yayama kaynağı dinamik bir nesne ise: özelliğini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="9b17d-291">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="9b17d-292">Yama yapılacak kaynak statik bir nesnese:</span><span class="sxs-lookup"><span data-stu-id="9b17d-292">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="9b17d-293">Özellik null atanabilir ise: null olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="9b17d-293">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="9b17d-294">Özellik null atanamaz ise, olarak ayarlar `default<T>` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-294">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="9b17d-295">Aşağıdaki örnek düzeltme eki belgesi `CustomerName` null ve siler olarak ayarlanır `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-295">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="9b17d-296">Değiştirme işlemi</span><span class="sxs-lookup"><span data-stu-id="9b17d-296">The replace operation</span></span>

<span data-ttu-id="9b17d-297">Bu işlem, bir `remove` sonrasında bir ile aynıdır `add` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-297">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="9b17d-298">Aşağıdaki örnek düzeltme eki belgesi değerini ayarlar `CustomerName` ve `Orders[0]` Yeni bir `Order` nesneyle değiştirir.</span><span class="sxs-lookup"><span data-stu-id="9b17d-298">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="9b17d-299">Taşıma işlemi</span><span class="sxs-lookup"><span data-stu-id="9b17d-299">The move operation</span></span>

* <span data-ttu-id="9b17d-300">`path`Bir dizi öğesinin işaret ediyorsa: öğesini öğesinin `from` konumuna kopyalar `path` , sonra `remove` öğesi üzerinde bir işlem çalıştırır `from` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-300">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="9b17d-301">`path`Bir özelliğe işaret ediyorsa: özelliğin değerini özelliğine kopyalar `from` `path` ve sonra `remove` özelliği üzerinde bir işlem çalıştırır `from` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-301">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="9b17d-302">`path`Varolmayan bir özelliğe işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="9b17d-302">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="9b17d-303">Yama yapılacak kaynak statik bir nesnese: istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="9b17d-303">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="9b17d-304">Yaması gereken kaynak dinamik bir nesnedir: `from` özelliği tarafından belirtilen konuma kopyalar `path` , sonra `remove` özellik üzerinde bir işlem çalıştırır `from` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-304">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="9b17d-305">Aşağıdaki örnek düzeltme eki belgesi:</span><span class="sxs-lookup"><span data-stu-id="9b17d-305">The following sample patch document:</span></span>

* <span data-ttu-id="9b17d-306">Değerini `Orders[0].OrderName` olarak kopyalar `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-306">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="9b17d-307">`Orders[0].OrderName`Null olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="9b17d-307">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="9b17d-308">`Orders[1]`Öncesine gider `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-308">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="9b17d-309">Kopyalama işlemi</span><span class="sxs-lookup"><span data-stu-id="9b17d-309">The copy operation</span></span>

<span data-ttu-id="9b17d-310">Bu işlem, `move` son adım olmadan işlem ile aynı şekilde aynıdır `remove` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-310">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="9b17d-311">Aşağıdaki örnek düzeltme eki belgesi:</span><span class="sxs-lookup"><span data-stu-id="9b17d-311">The following sample patch document:</span></span>

* <span data-ttu-id="9b17d-312">Değerini `Orders[0].OrderName` olarak kopyalar `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-312">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="9b17d-313">Daha önce bir kopyası `Orders[1]` ekler `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="9b17d-313">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="9b17d-314">Test işlemi</span><span class="sxs-lookup"><span data-stu-id="9b17d-314">The test operation</span></span>

<span data-ttu-id="9b17d-315">Tarafından belirtilen konumdaki değer `path` içinde belirtilen değerden farklıysa `value` , istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="9b17d-315">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="9b17d-316">Bu durumda, yama belgesindeki diğer tüm işlemler başka bir şekilde başarılı olsa bile, tüm yama isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="9b17d-316">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="9b17d-317">İşlem, bir `test` eşzamanlılık çakışması olduğunda bir güncelleştirmeyi engellemek için yaygın olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9b17d-317">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="9b17d-318">Aşağıdaki örnek düzeltme eki belgesinin ilk değeri `CustomerName` "John" ise, test başarısız olursa hiçbir etkisi yoktur:</span><span class="sxs-lookup"><span data-stu-id="9b17d-318">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="9b17d-319">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="9b17d-319">Get the code</span></span>

<span data-ttu-id="9b17d-320">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="9b17d-320">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="9b17d-321">([İndirme](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="9b17d-321">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="9b17d-322">Örneği test etmek için, uygulamayı çalıştırın ve aşağıdaki ayarlarla HTTP istekleri gönderin:</span><span class="sxs-lookup"><span data-stu-id="9b17d-322">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="9b17d-323">'DEKI `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="9b17d-323">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="9b17d-324">HTTP yöntemi: `PATCH`</span><span class="sxs-lookup"><span data-stu-id="9b17d-324">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="9b17d-325">Üst bilgi `Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="9b17d-325">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="9b17d-326">Gövde: *JSON proje klasöründen JSON Patch* belgesi örneklerinden birini kopyalayıp yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="9b17d-326">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9b17d-327">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9b17d-327">Additional resources</span></span>

* [<span data-ttu-id="9b17d-328">IETF RFC 5789 PATCH yöntemi belirtimi</span><span class="sxs-lookup"><span data-stu-id="9b17d-328">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="9b17d-329">IETF RFC 6902 JSON Patch belirtimi</span><span class="sxs-lookup"><span data-stu-id="9b17d-329">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="9b17d-330">IETF RFC 6901 JSON Patch yolu biçim belirtimi</span><span class="sxs-lookup"><span data-stu-id="9b17d-330">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="9b17d-331">[JSON yama belgeleri](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="9b17d-331">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="9b17d-332">JSON yama belgeleri oluşturmak için kaynakların bağlantılarını içerir.</span><span class="sxs-lookup"><span data-stu-id="9b17d-332">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="9b17d-333">ASP.NET Core JSON Patch kaynak kodu</span><span class="sxs-lookup"><span data-stu-id="9b17d-333">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
