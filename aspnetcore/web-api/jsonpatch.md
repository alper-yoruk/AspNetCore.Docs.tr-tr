---
<span data-ttu-id="bda5c-101">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-102">'Blazor'</span></span>
- <span data-ttu-id="bda5c-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-103">'Identity'</span></span>
- <span data-ttu-id="bda5c-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-105">'Razor'</span></span>
- <span data-ttu-id="bda5c-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-106">'SignalR' uid:</span></span> 

---

# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="bda5c-107">ASP.NET Core Web API 'sinde JsonPatch</span><span class="sxs-lookup"><span data-stu-id="bda5c-107">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="bda5c-108">, [Tom Dykstra](https://github.com/tdykstra) ve [Kirk larkabağı](https://github.com/serpent5) tarafından</span><span class="sxs-lookup"><span data-stu-id="bda5c-108">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="bda5c-109">Bu makalede, ASP.NET Core Web API 'sinde JSON Patch isteklerinin nasıl işleneceği açıklanır.</span><span class="sxs-lookup"><span data-stu-id="bda5c-109">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="bda5c-110">Paket yüklemesi</span><span class="sxs-lookup"><span data-stu-id="bda5c-110">Package installation</span></span>

<span data-ttu-id="bda5c-111">Uygulamanızda JSON yama desteğini etkinleştirmek için aşağıdaki adımları izleyin:</span><span class="sxs-lookup"><span data-stu-id="bda5c-111">To enable JSON Patch support in your app, complete the following steps:</span></span>

1. <span data-ttu-id="bda5c-112">[`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/)NuGet paketini yükler.</span><span class="sxs-lookup"><span data-stu-id="bda5c-112">Install the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
1. <span data-ttu-id="bda5c-113">`Startup.ConfigureServices`Çağırmak için projenin metodunu güncelleştirin <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*> .</span><span class="sxs-lookup"><span data-stu-id="bda5c-113">Update the project's `Startup.ConfigureServices` method to call <xref:Microsoft.Extensions.DependencyInjection.NewtonsoftJsonMvcBuilderExtensions.AddNewtonsoftJson*>.</span></span> <span data-ttu-id="bda5c-114">Örnek:</span><span class="sxs-lookup"><span data-stu-id="bda5c-114">For example:</span></span>

    ```csharp
    services
        .AddControllersWithViews()
        .AddNewtonsoftJson();
    ```

<span data-ttu-id="bda5c-115">`AddNewtonsoftJson`, MVC hizmeti kayıt yöntemleriyle uyumludur:</span><span class="sxs-lookup"><span data-stu-id="bda5c-115">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddRazorPages*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews*>
* <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllers*>

## <a name="json-patch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="bda5c-116">JSON Patch, AddNewtonsoftJson ve System. Text. JSON</span><span class="sxs-lookup"><span data-stu-id="bda5c-116">JSON Patch, AddNewtonsoftJson, and System.Text.Json</span></span>

<span data-ttu-id="bda5c-117">`AddNewtonsoftJson``System.Text.Json` **Tüm** JSON içeriğini biçimlendirmek için kullanılan tabanlı giriş ve çıkış biçimlerini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="bda5c-117">`AddNewtonsoftJson` replaces the `System.Text.Json`-based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="bda5c-118">Kullanarak JSON Patch desteği eklemek için `Newtonsoft.Json` , diğer biçimleri değişmeden bırakarak, projenin `Startup.ConfigureServices` yöntemini aşağıdaki gibi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="bda5c-118">To add support for JSON Patch using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` method as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="bda5c-119">Yukarıdaki kod, `Microsoft.AspNetCore.Mvc.NewtonsoftJson` paketi ve aşağıdaki `using` deyimlerini gerektirir:</span><span class="sxs-lookup"><span data-stu-id="bda5c-119">The preceding code requires the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package and the following `using` statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="bda5c-120">PATCH HTTP istek yöntemi</span><span class="sxs-lookup"><span data-stu-id="bda5c-120">PATCH HTTP request method</span></span>

<span data-ttu-id="bda5c-121">PUT ve [Patch](https://tools.ietf.org/html/rfc5789) yöntemleri, var olan bir kaynağı güncelleştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="bda5c-121">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="bda5c-122">Bunlar arasındaki fark, PUT 'ın tüm kaynağı değiştirmesi, ancak düzeltme eki yalnızca değişiklikleri belirttiğinde.</span><span class="sxs-lookup"><span data-stu-id="bda5c-122">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="bda5c-123">JSON yaması</span><span class="sxs-lookup"><span data-stu-id="bda5c-123">JSON Patch</span></span>

<span data-ttu-id="bda5c-124">[JSON yaması](https://tools.ietf.org/html/rfc6902) , bir kaynağa uygulanacak güncelleştirmelerin belirtilmesine yönelik bir biçimdir.</span><span class="sxs-lookup"><span data-stu-id="bda5c-124">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="bda5c-125">JSON yama belgesinde bir dizi *işlem*vardır.</span><span class="sxs-lookup"><span data-stu-id="bda5c-125">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="bda5c-126">Her işlem belirli bir değişiklik türünü tanımlar.</span><span class="sxs-lookup"><span data-stu-id="bda5c-126">Each operation identifies a particular type of change.</span></span> <span data-ttu-id="bda5c-127">Bu tür değişikliklere örnek olarak bir dizi öğesi ekleme veya bir özellik değeri değiştirme sayılabilir.</span><span class="sxs-lookup"><span data-stu-id="bda5c-127">Examples of such changes include adding an array element or replacing a property value.</span></span>

<span data-ttu-id="bda5c-128">Örneğin, aşağıdaki JSON belgeleri bir kaynağı, kaynak için bir JSON yama belgesini ve düzeltme eki işlemlerini uygulamanın sonucunu temsil eder.</span><span class="sxs-lookup"><span data-stu-id="bda5c-128">For example, the following JSON documents represent a resource, a JSON Patch document for the resource, and the result of applying the Patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="bda5c-129">Kaynak örneği</span><span class="sxs-lookup"><span data-stu-id="bda5c-129">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="bda5c-130">JSON Patch örneği</span><span class="sxs-lookup"><span data-stu-id="bda5c-130">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="bda5c-131">Önceki JSON 'da:</span><span class="sxs-lookup"><span data-stu-id="bda5c-131">In the preceding JSON:</span></span>

* <span data-ttu-id="bda5c-132">`op`Özelliği, işlem türünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="bda5c-132">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="bda5c-133">`path`Özelliği güncelleştirilecek öğeyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="bda5c-133">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="bda5c-134">`value`Özelliği yeni değeri sağlar.</span><span class="sxs-lookup"><span data-stu-id="bda5c-134">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="bda5c-135">Düzeltme ekiyle sonra kaynak</span><span class="sxs-lookup"><span data-stu-id="bda5c-135">Resource after patch</span></span>

<span data-ttu-id="bda5c-136">Önceki JSON Patch belgesi uygulandıktan sonra kaynak şu şekildedir:</span><span class="sxs-lookup"><span data-stu-id="bda5c-136">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="bda5c-137">Bir kaynak için bir JSON Patch belgesi uygulanarak yapılan değişiklikler atomik.</span><span class="sxs-lookup"><span data-stu-id="bda5c-137">The changes made by applying a JSON Patch document to a resource are atomic.</span></span> <span data-ttu-id="bda5c-138">Listedeki herhangi bir işlem başarısız olursa, listede hiçbir işlem uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="bda5c-138">If any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="bda5c-139">Yol sözdizimi</span><span class="sxs-lookup"><span data-stu-id="bda5c-139">Path syntax</span></span>

<span data-ttu-id="bda5c-140">Bir işlem nesnesinin [Path](https://tools.ietf.org/html/rfc6901) özelliği düzeyler arasında eğik çizgi içeriyor.</span><span class="sxs-lookup"><span data-stu-id="bda5c-140">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="bda5c-141">Örneğin, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="bda5c-141">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="bda5c-142">Sıfır tabanlı dizinler, dizi öğelerini belirtmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="bda5c-142">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="bda5c-143">Dizinin ilk öğesi `addresses` `/addresses/0` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-143">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="bda5c-144">`add`Bir dizinin sonuna kadar, `-` Dizin numarası yerine bir tire () kullanın: `/addresses/-` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-144">To `add` to the end of an array, use a hyphen (`-`) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="bda5c-145">İşlemler</span><span class="sxs-lookup"><span data-stu-id="bda5c-145">Operations</span></span>

<span data-ttu-id="bda5c-146">Aşağıdaki tabloda, [JSON Patch belirtiminde](https://tools.ietf.org/html/rfc6902)tanımlanan desteklenen işlemler gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="bda5c-146">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="bda5c-147">İşlem</span><span class="sxs-lookup"><span data-stu-id="bda5c-147">Operation</span></span>  | <span data-ttu-id="bda5c-148">Notlar</span><span class="sxs-lookup"><span data-stu-id="bda5c-148">Notes</span></span> |
|---
<span data-ttu-id="bda5c-149">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-149">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-150">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-150">'Blazor'</span></span>
- <span data-ttu-id="bda5c-151">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-151">'Identity'</span></span>
- <span data-ttu-id="bda5c-152">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-152">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-153">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-153">'Razor'</span></span>
- <span data-ttu-id="bda5c-154">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-154">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-155">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-155">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-156">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-156">'Blazor'</span></span>
- <span data-ttu-id="bda5c-157">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-157">'Identity'</span></span>
- <span data-ttu-id="bda5c-158">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-158">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-159">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-159">'Razor'</span></span>
- <span data-ttu-id="bda5c-160">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-160">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-161">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-161">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-162">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-162">'Blazor'</span></span>
- <span data-ttu-id="bda5c-163">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-163">'Identity'</span></span>
- <span data-ttu-id="bda5c-164">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-164">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-165">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-165">'Razor'</span></span>
- <span data-ttu-id="bda5c-166">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-166">'SignalR' uid:</span></span> 

<span data-ttu-id="bda5c-167">------|---
Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-167">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-168">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-168">'Blazor'</span></span>
- <span data-ttu-id="bda5c-169">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-169">'Identity'</span></span>
- <span data-ttu-id="bda5c-170">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-170">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-171">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-171">'Razor'</span></span>
- <span data-ttu-id="bda5c-172">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-172">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-173">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-173">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-174">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-174">'Blazor'</span></span>
- <span data-ttu-id="bda5c-175">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-175">'Identity'</span></span>
- <span data-ttu-id="bda5c-176">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-176">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-177">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-177">'Razor'</span></span>
- <span data-ttu-id="bda5c-178">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-178">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-179">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-179">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-180">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-180">'Blazor'</span></span>
- <span data-ttu-id="bda5c-181">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-181">'Identity'</span></span>
- <span data-ttu-id="bda5c-182">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-182">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-183">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-183">'Razor'</span></span>
- <span data-ttu-id="bda5c-184">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-184">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-185">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-185">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-186">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-186">'Blazor'</span></span>
- <span data-ttu-id="bda5c-187">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-187">'Identity'</span></span>
- <span data-ttu-id="bda5c-188">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-188">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-189">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-189">'Razor'</span></span>
- <span data-ttu-id="bda5c-190">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-190">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-191">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-191">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-192">'Blazor'</span></span>
- <span data-ttu-id="bda5c-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-193">'Identity'</span></span>
- <span data-ttu-id="bda5c-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-195">'Razor'</span></span>
- <span data-ttu-id="bda5c-196">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-197">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-197">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-198">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-198">'Blazor'</span></span>
- <span data-ttu-id="bda5c-199">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-199">'Identity'</span></span>
- <span data-ttu-id="bda5c-200">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-200">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-201">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-201">'Razor'</span></span>
- <span data-ttu-id="bda5c-202">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-202">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-203">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-203">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-204">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-204">'Blazor'</span></span>
- <span data-ttu-id="bda5c-205">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-205">'Identity'</span></span>
- <span data-ttu-id="bda5c-206">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-206">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-207">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-207">'Razor'</span></span>
- <span data-ttu-id="bda5c-208">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-208">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-209">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-209">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-210">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-210">'Blazor'</span></span>
- <span data-ttu-id="bda5c-211">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-211">'Identity'</span></span>
- <span data-ttu-id="bda5c-212">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-212">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-213">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-213">'Razor'</span></span>
- <span data-ttu-id="bda5c-214">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-214">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-215">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-215">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-216">'Blazor'</span></span>
- <span data-ttu-id="bda5c-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-217">'Identity'</span></span>
- <span data-ttu-id="bda5c-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-219">'Razor'</span></span>
- <span data-ttu-id="bda5c-220">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-221">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-221">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-222">'Blazor'</span></span>
- <span data-ttu-id="bda5c-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-223">'Identity'</span></span>
- <span data-ttu-id="bda5c-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-225">'Razor'</span></span>
- <span data-ttu-id="bda5c-226">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-227">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-227">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-228">'Blazor'</span></span>
- <span data-ttu-id="bda5c-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-229">'Identity'</span></span>
- <span data-ttu-id="bda5c-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-231">'Razor'</span></span>
- <span data-ttu-id="bda5c-232">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-233">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-233">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-234">'Blazor'</span></span>
- <span data-ttu-id="bda5c-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-235">'Identity'</span></span>
- <span data-ttu-id="bda5c-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-237">'Razor'</span></span>
- <span data-ttu-id="bda5c-238">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-239">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-239">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-240">'Blazor'</span></span>
- <span data-ttu-id="bda5c-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-241">'Identity'</span></span>
- <span data-ttu-id="bda5c-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-243">'Razor'</span></span>
- <span data-ttu-id="bda5c-244">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-245">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-245">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-246">'Blazor'</span></span>
- <span data-ttu-id="bda5c-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-247">'Identity'</span></span>
- <span data-ttu-id="bda5c-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-249">'Razor'</span></span>
- <span data-ttu-id="bda5c-250">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-250">'SignalR' uid:</span></span> 

<span data-ttu-id="bda5c-251">----------------| | `add`     | Bir özellik veya dizi öğesi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="bda5c-251">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="bda5c-252">Mevcut özellik için: Set Value. | | `remove`  | Bir özellik veya dizi öğesi kaldırın.</span><span class="sxs-lookup"><span data-stu-id="bda5c-252">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="bda5c-253">| | `replace` | Aynı `remove` konumdaki ve sonrasında aynı `add` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-253">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="bda5c-254">| | `move`    | Kaynaktaki `remove` `add` değeri kullanarak kaynağından sonra hedefle aynı.</span><span class="sxs-lookup"><span data-stu-id="bda5c-254">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="bda5c-255">| | `copy`    | `add`Kaynaktaki değeri kullanarak hedefle aynı olacak şekilde aynı.</span><span class="sxs-lookup"><span data-stu-id="bda5c-255">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="bda5c-256">| | `test`    | Değer = sağlanmışsa, başarı durum kodu döndürür `path` `value` . |</span><span class="sxs-lookup"><span data-stu-id="bda5c-256">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="json-patch-in-aspnet-core"></a><span data-ttu-id="bda5c-257">ASP.NET Core JSON yaması</span><span class="sxs-lookup"><span data-stu-id="bda5c-257">JSON Patch in ASP.NET Core</span></span>

<span data-ttu-id="bda5c-258">JSON düzeltme ekinin ASP.NET Core uygulanması, [Microsoft. AspNetCore. JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet paketinde sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="bda5c-258">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="bda5c-259">Eylem yöntemi kodu</span><span class="sxs-lookup"><span data-stu-id="bda5c-259">Action method code</span></span>

<span data-ttu-id="bda5c-260">Bir API denetleyicisinde, JSON yaması için bir eylem yöntemi:</span><span class="sxs-lookup"><span data-stu-id="bda5c-260">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="bda5c-261">, Özniteliğiyle açıklama eklenir `HttpPatch` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-261">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="bda5c-262">`JsonPatchDocument<T>`, Genellikle ile kabul eder `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-262">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="bda5c-263">`ApplyTo`Değişiklikleri uygulamak için düzeltme eki belgesindeki çağrılar.</span><span class="sxs-lookup"><span data-stu-id="bda5c-263">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="bda5c-264">İşte bir örnek:</span><span class="sxs-lookup"><span data-stu-id="bda5c-264">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="bda5c-265">Örnek uygulamadaki Bu kod aşağıdaki modelle birlikte kullanılabilir `Customer` :</span><span class="sxs-lookup"><span data-stu-id="bda5c-265">This code from the sample app works with the following `Customer` model:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="bda5c-266">Örnek eylem yöntemi:</span><span class="sxs-lookup"><span data-stu-id="bda5c-266">The sample action method:</span></span>

* <span data-ttu-id="bda5c-267">Bir oluşturur `Customer` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-267">Constructs a `Customer`.</span></span>
* <span data-ttu-id="bda5c-268">Düzeltme ekini uygular.</span><span class="sxs-lookup"><span data-stu-id="bda5c-268">Applies the patch.</span></span>
* <span data-ttu-id="bda5c-269">Yanıtın gövdesinde sonucu döndürür.</span><span class="sxs-lookup"><span data-stu-id="bda5c-269">Returns the result in the body of the response.</span></span>

<span data-ttu-id="bda5c-270">Gerçek bir uygulamada, kod veritabanı gibi bir mağazadan verileri alır ve düzeltme ekini uyguladıktan sonra veritabanını güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="bda5c-270">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="bda5c-271">Model durumu</span><span class="sxs-lookup"><span data-stu-id="bda5c-271">Model state</span></span>

<span data-ttu-id="bda5c-272">Önceki eylem yöntemi örneği, `ApplyTo` parametrelerinden biri olarak model durumunu alan aşırı yüklemesini çağırır.</span><span class="sxs-lookup"><span data-stu-id="bda5c-272">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="bda5c-273">Bu seçenekle, yanıtlardan hata iletileri alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="bda5c-273">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="bda5c-274">Aşağıdaki örnekte bir işlem için 400 Hatalı Istek yanıtının gövdesi gösterilmektedir `test` :</span><span class="sxs-lookup"><span data-stu-id="bda5c-274">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="bda5c-275">Dinamik nesneler</span><span class="sxs-lookup"><span data-stu-id="bda5c-275">Dynamic objects</span></span>

<span data-ttu-id="bda5c-276">Aşağıdaki eylem yöntemi örneği, dinamik bir nesneye nasıl düzeltme eki uygulanacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="bda5c-276">The following action method example shows how to apply a patch to a dynamic object:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="bda5c-277">Ekleme işlemi</span><span class="sxs-lookup"><span data-stu-id="bda5c-277">The add operation</span></span>

* <span data-ttu-id="bda5c-278">`path`Bir dizi öğesine işaret ediyorsa: tarafından belirtiden önce yeni bir öğe ekler `path` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-278">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="bda5c-279">`path`Bir özelliğe işaret ediyorsa: özellik değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="bda5c-279">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="bda5c-280">`path`Varolmayan bir konuma işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="bda5c-280">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="bda5c-281">Yama yapılacak kaynak dinamik bir nesnedir: bir özellik ekler.</span><span class="sxs-lookup"><span data-stu-id="bda5c-281">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="bda5c-282">Yama yapılacak kaynak statik bir nesnese: istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="bda5c-282">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="bda5c-283">Aşağıdaki örnek düzeltme eki belgesi değerini ayarlar `CustomerName` ve `Order` dizinin sonuna bir nesnesi ekler `Orders` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-283">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="bda5c-284">Kaldırma işlemi</span><span class="sxs-lookup"><span data-stu-id="bda5c-284">The remove operation</span></span>

* <span data-ttu-id="bda5c-285">`path`Bir dizi öğesine işaret ediyorsa: öğesini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="bda5c-285">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="bda5c-286">`path`Bir özelliğe işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="bda5c-286">If `path` points to a property:</span></span>
  * <span data-ttu-id="bda5c-287">Yayama kaynağı dinamik bir nesne ise: özelliğini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="bda5c-287">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="bda5c-288">Yama yapılacak kaynak statik bir nesnese:</span><span class="sxs-lookup"><span data-stu-id="bda5c-288">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="bda5c-289">Özellik null atanabilir ise: null olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="bda5c-289">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="bda5c-290">Özellik null atanamaz ise, olarak ayarlar `default<T>` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-290">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="bda5c-291">Aşağıdaki örnek düzeltme eki belgesi `CustomerName` null ve siler olarak ayarlanır `Orders[0]` :</span><span class="sxs-lookup"><span data-stu-id="bda5c-291">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="bda5c-292">Değiştirme işlemi</span><span class="sxs-lookup"><span data-stu-id="bda5c-292">The replace operation</span></span>

<span data-ttu-id="bda5c-293">Bu işlem, bir `remove` sonrasında bir ile aynıdır `add` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-293">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="bda5c-294">Aşağıdaki örnek düzeltme eki belgesi değerini ayarlar `CustomerName` ve `Orders[0]` Yeni bir `Order` nesneyle değiştirir:</span><span class="sxs-lookup"><span data-stu-id="bda5c-294">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="bda5c-295">Taşıma işlemi</span><span class="sxs-lookup"><span data-stu-id="bda5c-295">The move operation</span></span>

* <span data-ttu-id="bda5c-296">`path`Bir dizi öğesinin işaret ediyorsa: öğesini öğesinin `from` konumuna kopyalar `path` , sonra `remove` öğesi üzerinde bir işlem çalıştırır `from` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-296">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="bda5c-297">`path`Bir özelliğe işaret ediyorsa: özelliğin değerini özelliğine kopyalar `from` `path` ve sonra `remove` özelliği üzerinde bir işlem çalıştırır `from` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-297">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="bda5c-298">`path`Varolmayan bir özelliğe işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="bda5c-298">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="bda5c-299">Yama yapılacak kaynak statik bir nesnese: istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="bda5c-299">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="bda5c-300">Yaması gereken kaynak dinamik bir nesnedir: `from` özelliği tarafından belirtilen konuma kopyalar `path` , sonra `remove` özellik üzerinde bir işlem çalıştırır `from` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-300">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="bda5c-301">Aşağıdaki örnek düzeltme eki belgesi:</span><span class="sxs-lookup"><span data-stu-id="bda5c-301">The following sample patch document:</span></span>

* <span data-ttu-id="bda5c-302">Değerini `Orders[0].OrderName` olarak kopyalar `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-302">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="bda5c-303">`Orders[0].OrderName`Null olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="bda5c-303">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="bda5c-304">`Orders[1]`Öncesine gider `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-304">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="bda5c-305">Kopyalama işlemi</span><span class="sxs-lookup"><span data-stu-id="bda5c-305">The copy operation</span></span>

<span data-ttu-id="bda5c-306">Bu işlem, `move` son adım olmadan işlem ile aynı şekilde aynıdır `remove` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-306">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="bda5c-307">Aşağıdaki örnek düzeltme eki belgesi:</span><span class="sxs-lookup"><span data-stu-id="bda5c-307">The following sample patch document:</span></span>

* <span data-ttu-id="bda5c-308">Değerini `Orders[0].OrderName` olarak kopyalar `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-308">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="bda5c-309">Daha önce bir kopyası `Orders[1]` ekler `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-309">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="bda5c-310">Test işlemi</span><span class="sxs-lookup"><span data-stu-id="bda5c-310">The test operation</span></span>

<span data-ttu-id="bda5c-311">Tarafından belirtilen konumdaki değer `path` içinde belirtilen değerden farklıysa `value` , istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="bda5c-311">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="bda5c-312">Bu durumda, yama belgesindeki diğer tüm işlemler başka bir şekilde başarılı olsa bile, tüm yama isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="bda5c-312">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="bda5c-313">İşlem, bir `test` eşzamanlılık çakışması olduğunda bir güncelleştirmeyi engellemek için yaygın olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="bda5c-313">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="bda5c-314">Aşağıdaki örnek düzeltme eki belgesinin ilk değeri `CustomerName` "John" ise, test başarısız olursa hiçbir etkisi yoktur:</span><span class="sxs-lookup"><span data-stu-id="bda5c-314">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="bda5c-315">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="bda5c-315">Get the code</span></span>

<span data-ttu-id="bda5c-316">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span><span class="sxs-lookup"><span data-stu-id="bda5c-316">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples).</span></span> <span data-ttu-id="bda5c-317">([İndirme](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="bda5c-317">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="bda5c-318">Örneği test etmek için, uygulamayı çalıştırın ve aşağıdaki ayarlarla HTTP istekleri gönderin:</span><span class="sxs-lookup"><span data-stu-id="bda5c-318">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="bda5c-319">'DEKI`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="bda5c-319">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="bda5c-320">HTTP yöntemi:`PATCH`</span><span class="sxs-lookup"><span data-stu-id="bda5c-320">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="bda5c-321">Üst bilgi`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="bda5c-321">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="bda5c-322">Gövde: *JSON proje klasöründen JSON Patch* belgesi örneklerinden birini kopyalayıp yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="bda5c-322">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bda5c-323">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="bda5c-323">Additional resources</span></span>

* [<span data-ttu-id="bda5c-324">IETF RFC 5789 PATCH yöntemi belirtimi</span><span class="sxs-lookup"><span data-stu-id="bda5c-324">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="bda5c-325">IETF RFC 6902 JSON Patch belirtimi</span><span class="sxs-lookup"><span data-stu-id="bda5c-325">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="bda5c-326">IETF RFC 6901 JSON Patch yolu biçim belirtimi</span><span class="sxs-lookup"><span data-stu-id="bda5c-326">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="bda5c-327">[JSON yama belgeleri](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="bda5c-327">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="bda5c-328">JSON yama belgeleri oluşturmak için kaynakların bağlantılarını içerir.</span><span class="sxs-lookup"><span data-stu-id="bda5c-328">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="bda5c-329">ASP.NET Core JSON Patch kaynak kodu</span><span class="sxs-lookup"><span data-stu-id="bda5c-329">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="bda5c-330">Bu makalede, ASP.NET Core Web API 'sinde JSON Patch isteklerinin nasıl işleneceği açıklanır.</span><span class="sxs-lookup"><span data-stu-id="bda5c-330">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="bda5c-331">PATCH HTTP istek yöntemi</span><span class="sxs-lookup"><span data-stu-id="bda5c-331">PATCH HTTP request method</span></span>

<span data-ttu-id="bda5c-332">PUT ve [Patch](https://tools.ietf.org/html/rfc5789) yöntemleri, var olan bir kaynağı güncelleştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="bda5c-332">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="bda5c-333">Bunlar arasındaki fark, PUT 'ın tüm kaynağı değiştirmesi, ancak düzeltme eki yalnızca değişiklikleri belirttiğinde.</span><span class="sxs-lookup"><span data-stu-id="bda5c-333">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="bda5c-334">JSON yaması</span><span class="sxs-lookup"><span data-stu-id="bda5c-334">JSON Patch</span></span>

<span data-ttu-id="bda5c-335">[JSON yaması](https://tools.ietf.org/html/rfc6902) , bir kaynağa uygulanacak güncelleştirmelerin belirtilmesine yönelik bir biçimdir.</span><span class="sxs-lookup"><span data-stu-id="bda5c-335">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="bda5c-336">JSON yama belgesinde bir dizi *işlem*vardır.</span><span class="sxs-lookup"><span data-stu-id="bda5c-336">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="bda5c-337">Her işlem, bir dizi öğesi ekleme veya bir özellik değerini değiştirme gibi belirli bir değişiklik türünü tanımlar.</span><span class="sxs-lookup"><span data-stu-id="bda5c-337">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="bda5c-338">Örneğin, aşağıdaki JSON belgeleri bir kaynağı, kaynak için bir JSON yama belgesini ve düzeltme eki işlemlerini uygulamanın sonucunu temsil eder.</span><span class="sxs-lookup"><span data-stu-id="bda5c-338">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="bda5c-339">Kaynak örneği</span><span class="sxs-lookup"><span data-stu-id="bda5c-339">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="bda5c-340">JSON Patch örneği</span><span class="sxs-lookup"><span data-stu-id="bda5c-340">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="bda5c-341">Önceki JSON 'da:</span><span class="sxs-lookup"><span data-stu-id="bda5c-341">In the preceding JSON:</span></span>

* <span data-ttu-id="bda5c-342">`op`Özelliği, işlem türünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="bda5c-342">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="bda5c-343">`path`Özelliği güncelleştirilecek öğeyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="bda5c-343">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="bda5c-344">`value`Özelliği yeni değeri sağlar.</span><span class="sxs-lookup"><span data-stu-id="bda5c-344">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="bda5c-345">Düzeltme ekiyle sonra kaynak</span><span class="sxs-lookup"><span data-stu-id="bda5c-345">Resource after patch</span></span>

<span data-ttu-id="bda5c-346">Önceki JSON Patch belgesi uygulandıktan sonra kaynak şu şekildedir:</span><span class="sxs-lookup"><span data-stu-id="bda5c-346">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="bda5c-347">Bir kaynak için bir JSON Patch belgesi uygulanarak yapılan değişiklikler atomik: listedeki herhangi bir işlem başarısız olursa, listede hiçbir işlem uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="bda5c-347">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="bda5c-348">Yol sözdizimi</span><span class="sxs-lookup"><span data-stu-id="bda5c-348">Path syntax</span></span>

<span data-ttu-id="bda5c-349">Bir işlem nesnesinin [Path](https://tools.ietf.org/html/rfc6901) özelliği düzeyler arasında eğik çizgi içeriyor.</span><span class="sxs-lookup"><span data-stu-id="bda5c-349">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="bda5c-350">Örneğin, `"/address/zipCode"`.</span><span class="sxs-lookup"><span data-stu-id="bda5c-350">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="bda5c-351">Sıfır tabanlı dizinler, dizi öğelerini belirtmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="bda5c-351">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="bda5c-352">Dizinin ilk öğesi `addresses` `/addresses/0` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-352">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="bda5c-353">`add`Bir dizinin sonuna kadar, dizin numarası yerine bir tire (-) kullanın: `/addresses/-` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-353">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="bda5c-354">İşlemler</span><span class="sxs-lookup"><span data-stu-id="bda5c-354">Operations</span></span>

<span data-ttu-id="bda5c-355">Aşağıdaki tabloda, [JSON Patch belirtiminde](https://tools.ietf.org/html/rfc6902)tanımlanan desteklenen işlemler gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="bda5c-355">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="bda5c-356">İşlem</span><span class="sxs-lookup"><span data-stu-id="bda5c-356">Operation</span></span>  | <span data-ttu-id="bda5c-357">Notlar</span><span class="sxs-lookup"><span data-stu-id="bda5c-357">Notes</span></span> |
|---
<span data-ttu-id="bda5c-358">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-358">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-359">'Blazor'</span></span>
- <span data-ttu-id="bda5c-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-360">'Identity'</span></span>
- <span data-ttu-id="bda5c-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-362">'Razor'</span></span>
- <span data-ttu-id="bda5c-363">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-364">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-364">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-365">'Blazor'</span></span>
- <span data-ttu-id="bda5c-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-366">'Identity'</span></span>
- <span data-ttu-id="bda5c-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-368">'Razor'</span></span>
- <span data-ttu-id="bda5c-369">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-370">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-370">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-371">'Blazor'</span></span>
- <span data-ttu-id="bda5c-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-372">'Identity'</span></span>
- <span data-ttu-id="bda5c-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-374">'Razor'</span></span>
- <span data-ttu-id="bda5c-375">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-375">'SignalR' uid:</span></span> 

<span data-ttu-id="bda5c-376">------|---
Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-376">------|---
title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-377">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-377">'Blazor'</span></span>
- <span data-ttu-id="bda5c-378">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-378">'Identity'</span></span>
- <span data-ttu-id="bda5c-379">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-379">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-380">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-380">'Razor'</span></span>
- <span data-ttu-id="bda5c-381">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-381">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-382">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-382">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-383">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-383">'Blazor'</span></span>
- <span data-ttu-id="bda5c-384">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-384">'Identity'</span></span>
- <span data-ttu-id="bda5c-385">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-385">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-386">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-386">'Razor'</span></span>
- <span data-ttu-id="bda5c-387">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-387">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-388">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-388">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-389">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-389">'Blazor'</span></span>
- <span data-ttu-id="bda5c-390">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-390">'Identity'</span></span>
- <span data-ttu-id="bda5c-391">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-391">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-392">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-392">'Razor'</span></span>
- <span data-ttu-id="bda5c-393">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-393">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-394">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-394">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-395">'Blazor'</span></span>
- <span data-ttu-id="bda5c-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-396">'Identity'</span></span>
- <span data-ttu-id="bda5c-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-398">'Razor'</span></span>
- <span data-ttu-id="bda5c-399">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-400">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-400">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-401">'Blazor'</span></span>
- <span data-ttu-id="bda5c-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-402">'Identity'</span></span>
- <span data-ttu-id="bda5c-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-404">'Razor'</span></span>
- <span data-ttu-id="bda5c-405">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-406">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-406">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-407">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-407">'Blazor'</span></span>
- <span data-ttu-id="bda5c-408">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-408">'Identity'</span></span>
- <span data-ttu-id="bda5c-409">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-409">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-410">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-410">'Razor'</span></span>
- <span data-ttu-id="bda5c-411">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-411">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-412">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-412">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-413">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-413">'Blazor'</span></span>
- <span data-ttu-id="bda5c-414">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-414">'Identity'</span></span>
- <span data-ttu-id="bda5c-415">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-415">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-416">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-416">'Razor'</span></span>
- <span data-ttu-id="bda5c-417">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-417">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-418">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-418">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-419">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-419">'Blazor'</span></span>
- <span data-ttu-id="bda5c-420">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-420">'Identity'</span></span>
- <span data-ttu-id="bda5c-421">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-421">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-422">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-422">'Razor'</span></span>
- <span data-ttu-id="bda5c-423">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-423">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-424">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-424">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-425">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-425">'Blazor'</span></span>
- <span data-ttu-id="bda5c-426">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-426">'Identity'</span></span>
- <span data-ttu-id="bda5c-427">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-427">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-428">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-428">'Razor'</span></span>
- <span data-ttu-id="bda5c-429">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-429">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-430">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-430">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-431">'Blazor'</span></span>
- <span data-ttu-id="bda5c-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-432">'Identity'</span></span>
- <span data-ttu-id="bda5c-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-434">'Razor'</span></span>
- <span data-ttu-id="bda5c-435">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-435">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-436">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-436">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-437">'Blazor'</span></span>
- <span data-ttu-id="bda5c-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-438">'Identity'</span></span>
- <span data-ttu-id="bda5c-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-440">'Razor'</span></span>
- <span data-ttu-id="bda5c-441">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-442">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-442">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-443">'Blazor'</span></span>
- <span data-ttu-id="bda5c-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-444">'Identity'</span></span>
- <span data-ttu-id="bda5c-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-446">'Razor'</span></span>
- <span data-ttu-id="bda5c-447">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-448">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-448">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-449">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-449">'Blazor'</span></span>
- <span data-ttu-id="bda5c-450">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-450">'Identity'</span></span>
- <span data-ttu-id="bda5c-451">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-451">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-452">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-452">'Razor'</span></span>
- <span data-ttu-id="bda5c-453">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-453">'SignalR' uid:</span></span> 

-
<span data-ttu-id="bda5c-454">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="bda5c-454">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="bda5c-455">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-455">'Blazor'</span></span>
- <span data-ttu-id="bda5c-456">'Identity'</span><span class="sxs-lookup"><span data-stu-id="bda5c-456">'Identity'</span></span>
- <span data-ttu-id="bda5c-457">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="bda5c-457">'Let's Encrypt'</span></span>
- <span data-ttu-id="bda5c-458">'Razor'</span><span class="sxs-lookup"><span data-stu-id="bda5c-458">'Razor'</span></span>
- <span data-ttu-id="bda5c-459">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="bda5c-459">'SignalR' uid:</span></span> 

<span data-ttu-id="bda5c-460">----------------| | `add`     | Bir özellik veya dizi öğesi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="bda5c-460">----------------| | `add`     | Add a property or array element.</span></span> <span data-ttu-id="bda5c-461">Mevcut özellik için: Set Value. | | `remove`  | Bir özellik veya dizi öğesi kaldırın.</span><span class="sxs-lookup"><span data-stu-id="bda5c-461">For existing property: set value.| | `remove`  | Remove a property or array element.</span></span> <span data-ttu-id="bda5c-462">| | `replace` | Aynı `remove` konumdaki ve sonrasında aynı `add` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-462">| | `replace` | Same as `remove` followed by `add` at same location.</span></span> <span data-ttu-id="bda5c-463">| | `move`    | Kaynaktaki `remove` `add` değeri kullanarak kaynağından sonra hedefle aynı.</span><span class="sxs-lookup"><span data-stu-id="bda5c-463">| | `move`    | Same as `remove` from source followed by `add` to destination using value from source.</span></span> <span data-ttu-id="bda5c-464">| | `copy`    | `add`Kaynaktaki değeri kullanarak hedefle aynı olacak şekilde aynı.</span><span class="sxs-lookup"><span data-stu-id="bda5c-464">| | `copy`    | Same as `add` to destination using value from source.</span></span> <span data-ttu-id="bda5c-465">| | `test`    | Değer = sağlanmışsa, başarı durum kodu döndürür `path` `value` . |</span><span class="sxs-lookup"><span data-stu-id="bda5c-465">| | `test`    | Return success status code if value at `path` = provided `value`.|</span></span>

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="bda5c-466">ASP.NET Core 'de JsonPatch</span><span class="sxs-lookup"><span data-stu-id="bda5c-466">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="bda5c-467">JSON düzeltme ekinin ASP.NET Core uygulanması, [Microsoft. AspNetCore. JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet paketinde sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="bda5c-467">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="bda5c-468">Paket, [Microsoft. AspnetCore. app](xref:fundamentals/metapackage-app) metapackage 'e dahildir.</span><span class="sxs-lookup"><span data-stu-id="bda5c-468">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="bda5c-469">Eylem yöntemi kodu</span><span class="sxs-lookup"><span data-stu-id="bda5c-469">Action method code</span></span>

<span data-ttu-id="bda5c-470">Bir API denetleyicisinde, JSON yaması için bir eylem yöntemi:</span><span class="sxs-lookup"><span data-stu-id="bda5c-470">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="bda5c-471">, Özniteliğiyle açıklama eklenir `HttpPatch` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-471">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="bda5c-472">`JsonPatchDocument<T>`, Genellikle ile kabul eder `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-472">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="bda5c-473">`ApplyTo`Değişiklikleri uygulamak için düzeltme eki belgesindeki çağrılar.</span><span class="sxs-lookup"><span data-stu-id="bda5c-473">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="bda5c-474">İşte bir örnek:</span><span class="sxs-lookup"><span data-stu-id="bda5c-474">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="bda5c-475">Örnek uygulamadaki Bu kod aşağıdaki modelle birlikte kullanılabilir `Customer` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-475">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="bda5c-476">Örnek eylem yöntemi:</span><span class="sxs-lookup"><span data-stu-id="bda5c-476">The sample action method:</span></span>

* <span data-ttu-id="bda5c-477">Bir oluşturur `Customer` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-477">Constructs a `Customer`.</span></span>
* <span data-ttu-id="bda5c-478">Düzeltme ekini uygular.</span><span class="sxs-lookup"><span data-stu-id="bda5c-478">Applies the patch.</span></span>
* <span data-ttu-id="bda5c-479">Yanıtın gövdesinde sonucu döndürür.</span><span class="sxs-lookup"><span data-stu-id="bda5c-479">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="bda5c-480">Gerçek bir uygulamada, kod veritabanı gibi bir mağazadan verileri alır ve düzeltme ekini uyguladıktan sonra veritabanını güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="bda5c-480">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="bda5c-481">Model durumu</span><span class="sxs-lookup"><span data-stu-id="bda5c-481">Model state</span></span>

<span data-ttu-id="bda5c-482">Önceki eylem yöntemi örneği, `ApplyTo` parametrelerinden biri olarak model durumunu alan aşırı yüklemesini çağırır.</span><span class="sxs-lookup"><span data-stu-id="bda5c-482">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="bda5c-483">Bu seçenekle, yanıtlardan hata iletileri alabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="bda5c-483">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="bda5c-484">Aşağıdaki örnekte bir işlem için 400 Hatalı Istek yanıtının gövdesi gösterilmektedir `test` :</span><span class="sxs-lookup"><span data-stu-id="bda5c-484">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="bda5c-485">Dinamik nesneler</span><span class="sxs-lookup"><span data-stu-id="bda5c-485">Dynamic objects</span></span>

<span data-ttu-id="bda5c-486">Aşağıdaki eylem yöntemi örneği, dinamik bir nesne için bir düzeltme ekinin nasıl uygulanacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="bda5c-486">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="bda5c-487">Ekleme işlemi</span><span class="sxs-lookup"><span data-stu-id="bda5c-487">The add operation</span></span>

* <span data-ttu-id="bda5c-488">`path`Bir dizi öğesine işaret ediyorsa: tarafından belirtiden önce yeni bir öğe ekler `path` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-488">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="bda5c-489">`path`Bir özelliğe işaret ediyorsa: özellik değerini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="bda5c-489">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="bda5c-490">`path`Varolmayan bir konuma işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="bda5c-490">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="bda5c-491">Yama yapılacak kaynak dinamik bir nesnedir: bir özellik ekler.</span><span class="sxs-lookup"><span data-stu-id="bda5c-491">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="bda5c-492">Yama yapılacak kaynak statik bir nesnese: istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="bda5c-492">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="bda5c-493">Aşağıdaki örnek düzeltme eki belgesi değerini ayarlar `CustomerName` ve `Order` dizinin sonuna bir nesnesi ekler `Orders` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-493">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="bda5c-494">Kaldırma işlemi</span><span class="sxs-lookup"><span data-stu-id="bda5c-494">The remove operation</span></span>

* <span data-ttu-id="bda5c-495">`path`Bir dizi öğesine işaret ediyorsa: öğesini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="bda5c-495">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="bda5c-496">`path`Bir özelliğe işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="bda5c-496">If `path` points to a property:</span></span>
  * <span data-ttu-id="bda5c-497">Yayama kaynağı dinamik bir nesne ise: özelliğini kaldırır.</span><span class="sxs-lookup"><span data-stu-id="bda5c-497">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="bda5c-498">Yama yapılacak kaynak statik bir nesnese:</span><span class="sxs-lookup"><span data-stu-id="bda5c-498">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="bda5c-499">Özellik null atanabilir ise: null olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="bda5c-499">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="bda5c-500">Özellik null atanamaz ise, olarak ayarlar `default<T>` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-500">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="bda5c-501">Aşağıdaki örnek düzeltme eki belgesi `CustomerName` null ve siler olarak ayarlanır `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-501">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="bda5c-502">Değiştirme işlemi</span><span class="sxs-lookup"><span data-stu-id="bda5c-502">The replace operation</span></span>

<span data-ttu-id="bda5c-503">Bu işlem, bir `remove` sonrasında bir ile aynıdır `add` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-503">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="bda5c-504">Aşağıdaki örnek düzeltme eki belgesi değerini ayarlar `CustomerName` ve `Orders[0]` Yeni bir `Order` nesneyle değiştirir.</span><span class="sxs-lookup"><span data-stu-id="bda5c-504">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="bda5c-505">Taşıma işlemi</span><span class="sxs-lookup"><span data-stu-id="bda5c-505">The move operation</span></span>

* <span data-ttu-id="bda5c-506">`path`Bir dizi öğesinin işaret ediyorsa: öğesini öğesinin `from` konumuna kopyalar `path` , sonra `remove` öğesi üzerinde bir işlem çalıştırır `from` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-506">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="bda5c-507">`path`Bir özelliğe işaret ediyorsa: özelliğin değerini özelliğine kopyalar `from` `path` ve sonra `remove` özelliği üzerinde bir işlem çalıştırır `from` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-507">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="bda5c-508">`path`Varolmayan bir özelliğe işaret ediyorsa:</span><span class="sxs-lookup"><span data-stu-id="bda5c-508">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="bda5c-509">Yama yapılacak kaynak statik bir nesnese: istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="bda5c-509">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="bda5c-510">Yaması gereken kaynak dinamik bir nesnedir: `from` özelliği tarafından belirtilen konuma kopyalar `path` , sonra `remove` özellik üzerinde bir işlem çalıştırır `from` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-510">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="bda5c-511">Aşağıdaki örnek düzeltme eki belgesi:</span><span class="sxs-lookup"><span data-stu-id="bda5c-511">The following sample patch document:</span></span>

* <span data-ttu-id="bda5c-512">Değerini `Orders[0].OrderName` olarak kopyalar `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-512">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="bda5c-513">`Orders[0].OrderName`Null olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="bda5c-513">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="bda5c-514">`Orders[1]`Öncesine gider `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-514">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="bda5c-515">Kopyalama işlemi</span><span class="sxs-lookup"><span data-stu-id="bda5c-515">The copy operation</span></span>

<span data-ttu-id="bda5c-516">Bu işlem, `move` son adım olmadan işlem ile aynı şekilde aynıdır `remove` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-516">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="bda5c-517">Aşağıdaki örnek düzeltme eki belgesi:</span><span class="sxs-lookup"><span data-stu-id="bda5c-517">The following sample patch document:</span></span>

* <span data-ttu-id="bda5c-518">Değerini `Orders[0].OrderName` olarak kopyalar `CustomerName` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-518">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="bda5c-519">Daha önce bir kopyası `Orders[1]` ekler `Orders[0]` .</span><span class="sxs-lookup"><span data-stu-id="bda5c-519">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="bda5c-520">Test işlemi</span><span class="sxs-lookup"><span data-stu-id="bda5c-520">The test operation</span></span>

<span data-ttu-id="bda5c-521">Tarafından belirtilen konumdaki değer `path` içinde belirtilen değerden farklıysa `value` , istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="bda5c-521">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="bda5c-522">Bu durumda, yama belgesindeki diğer tüm işlemler başka bir şekilde başarılı olsa bile, tüm yama isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="bda5c-522">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="bda5c-523">İşlem, bir `test` eşzamanlılık çakışması olduğunda bir güncelleştirmeyi engellemek için yaygın olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="bda5c-523">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="bda5c-524">Aşağıdaki örnek düzeltme eki belgesinin ilk değeri `CustomerName` "John" ise, test başarısız olursa hiçbir etkisi yoktur:</span><span class="sxs-lookup"><span data-stu-id="bda5c-524">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="bda5c-525">Kodu alma</span><span class="sxs-lookup"><span data-stu-id="bda5c-525">Get the code</span></span>

<span data-ttu-id="bda5c-526">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span><span class="sxs-lookup"><span data-stu-id="bda5c-526">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="bda5c-527">([İndirme](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="bda5c-527">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="bda5c-528">Örneği test etmek için, uygulamayı çalıştırın ve aşağıdaki ayarlarla HTTP istekleri gönderin:</span><span class="sxs-lookup"><span data-stu-id="bda5c-528">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="bda5c-529">'DEKI`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="bda5c-529">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="bda5c-530">HTTP yöntemi:`PATCH`</span><span class="sxs-lookup"><span data-stu-id="bda5c-530">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="bda5c-531">Üst bilgi`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="bda5c-531">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="bda5c-532">Gövde: *JSON proje klasöründen JSON Patch* belgesi örneklerinden birini kopyalayıp yapıştırın.</span><span class="sxs-lookup"><span data-stu-id="bda5c-532">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bda5c-533">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="bda5c-533">Additional resources</span></span>

* [<span data-ttu-id="bda5c-534">IETF RFC 5789 PATCH yöntemi belirtimi</span><span class="sxs-lookup"><span data-stu-id="bda5c-534">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="bda5c-535">IETF RFC 6902 JSON Patch belirtimi</span><span class="sxs-lookup"><span data-stu-id="bda5c-535">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="bda5c-536">IETF RFC 6901 JSON Patch yolu biçim belirtimi</span><span class="sxs-lookup"><span data-stu-id="bda5c-536">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="bda5c-537">[JSON yama belgeleri](https://jsonpatch.com/).</span><span class="sxs-lookup"><span data-stu-id="bda5c-537">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="bda5c-538">JSON yama belgeleri oluşturmak için kaynakların bağlantılarını içerir.</span><span class="sxs-lookup"><span data-stu-id="bda5c-538">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="bda5c-539">ASP.NET Core JSON Patch kaynak kodu</span><span class="sxs-lookup"><span data-stu-id="bda5c-539">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
