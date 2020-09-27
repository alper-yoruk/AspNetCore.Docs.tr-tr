---
title: ASP.NET Core 'de model bağlama
author: rick-anderson
description: ASP.NET Core model bağlamasının nasıl çalıştığını ve davranışını nasıl özelleştireceğinizi öğrenin.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/models/model-binding
ms.openlocfilehash: ca2f071ccb84fdb2eb06f533fc4d088ad1b1c785
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393892"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="ba649-103">ASP.NET Core 'de model bağlama</span><span class="sxs-lookup"><span data-stu-id="ba649-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ba649-104">Bu makalede, model bağlamanın ne olduğu, nasıl çalıştığı ve davranışını nasıl özelleştireceğiniz açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="ba649-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="ba649-105">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ba649-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="ba649-106">Model bağlama nedir?</span><span class="sxs-lookup"><span data-stu-id="ba649-106">What is Model binding</span></span>

<span data-ttu-id="ba649-107">Denetleyiciler ve Razor Sayfalar, http isteklerinden gelen verilerle çalışır.</span><span class="sxs-lookup"><span data-stu-id="ba649-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="ba649-108">Örneğin, rota verileri bir kayıt anahtarı sağlayabilir ve postalanan form alanları, modelin özelliklerine ilişkin değerler sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="ba649-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="ba649-109">Bu değerlerin her birini almak ve bunları dizelerden .NET türlerine dönüştürmek için kod yazma sıkıcı ve hata durumunda olabilir.</span><span class="sxs-lookup"><span data-stu-id="ba649-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="ba649-110">Model bağlama bu işlemi otomatikleştirir.</span><span class="sxs-lookup"><span data-stu-id="ba649-110">Model binding automates this process.</span></span> <span data-ttu-id="ba649-111">Model bağlama sistemi:</span><span class="sxs-lookup"><span data-stu-id="ba649-111">The model binding system:</span></span>

* <span data-ttu-id="ba649-112">Veri yolu, form alanları ve sorgu dizeleri gibi çeşitli kaynaklardan veri alır.</span><span class="sxs-lookup"><span data-stu-id="ba649-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="ba649-113">RazorYöntem parametreleri ve ortak özellikler içindeki denetleyicilere ve sayfalara verileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="ba649-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="ba649-114">Dize verilerini .NET türlerine dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="ba649-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="ba649-115">Karmaşık türlerin özelliklerini güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="ba649-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="ba649-116">Örnek</span><span class="sxs-lookup"><span data-stu-id="ba649-116">Example</span></span>

<span data-ttu-id="ba649-117">Aşağıdaki eylem yöntemine sahip olduğunuzu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="ba649-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="ba649-118">Ve uygulama şu URL ile bir istek alıyor:</span><span class="sxs-lookup"><span data-stu-id="ba649-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="ba649-119">Model bağlama, yönlendirme sistemi eylem yöntemini seçtikten sonra aşağıdaki adımlardan geçer:</span><span class="sxs-lookup"><span data-stu-id="ba649-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="ba649-120">Adlı bir tamsayı olan ilk parametresini bulur `GetByID` `id` .</span><span class="sxs-lookup"><span data-stu-id="ba649-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="ba649-121">HTTP isteğindeki kullanılabilir kaynakları arar ve `id` route verilerinde = "2" bulur.</span><span class="sxs-lookup"><span data-stu-id="ba649-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="ba649-122">"2" dizesini tamsayı 2 ' ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="ba649-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="ba649-123">`GetByID`Adlı bir Boole değeri olan bir sonraki parametresini bulur `dogsOnly` .</span><span class="sxs-lookup"><span data-stu-id="ba649-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="ba649-124">Kaynakları arar ve sorgu dizesinde "DogsOnly = true" bulur.</span><span class="sxs-lookup"><span data-stu-id="ba649-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="ba649-125">Ad eşleştirme, büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="ba649-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="ba649-126">"True" dizesini Boole değerine dönüştürür `true` .</span><span class="sxs-lookup"><span data-stu-id="ba649-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="ba649-127">Framework daha sonra yöntemi çağırır `GetById` , parametresi için 2 ' ye geçerek `id` ve `true` `dogsOnly` parametresi için.</span><span class="sxs-lookup"><span data-stu-id="ba649-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="ba649-128">Önceki örnekte, model bağlama hedefleri basit türler olan yöntem parametreleridir.</span><span class="sxs-lookup"><span data-stu-id="ba649-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="ba649-129">Hedefler, karmaşık bir türün özellikleri de olabilir.</span><span class="sxs-lookup"><span data-stu-id="ba649-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="ba649-130">Her bir özellik başarıyla bağlandıktan sonra, bu özellik için [model doğrulaması](xref:mvc/models/validation) oluşur.</span><span class="sxs-lookup"><span data-stu-id="ba649-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="ba649-131">Hangi verilerin modele bağladığına ve tüm bağlama veya doğrulama hatalarıyla ilgili kayıt, [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) veya [Pagemodel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState)içinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="ba649-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="ba649-132">Bu işlemin başarılı olup olmadığını öğrenmek için uygulama [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) bayrağını denetler.</span><span class="sxs-lookup"><span data-stu-id="ba649-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="ba649-133">Targets</span><span class="sxs-lookup"><span data-stu-id="ba649-133">Targets</span></span>

<span data-ttu-id="ba649-134">Model bağlama, aşağıdaki tür hedeflerin değerlerini bulmayı dener:</span><span class="sxs-lookup"><span data-stu-id="ba649-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="ba649-135">Bir isteğin yönlendirildiği denetleyici eylemi yönteminin parametreleri.</span><span class="sxs-lookup"><span data-stu-id="ba649-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="ba649-136">RazorBir isteğin yönlendirildiği sayfa işleyicisi yönteminin parametreleri.</span><span class="sxs-lookup"><span data-stu-id="ba649-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="ba649-137">Bir denetleyicinin veya `PageModel` sınıfın öznitelikleri tarafından belirtilmişse ortak özellikleri.</span><span class="sxs-lookup"><span data-stu-id="ba649-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="ba649-138">[BindProperty] özniteliği</span><span class="sxs-lookup"><span data-stu-id="ba649-138">[BindProperty] attribute</span></span>

<span data-ttu-id="ba649-139">, `PageModel` Model bağlamasının bu özelliği hedeflemesini sağlamak için bir denetleyicinin veya sınıfın ortak özelliğine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="ba649-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="ba649-140">[BindProperties] özniteliği</span><span class="sxs-lookup"><span data-stu-id="ba649-140">[BindProperties] attribute</span></span>

<span data-ttu-id="ba649-141">ASP.NET Core 2,1 ve üzeri sürümlerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ba649-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="ba649-142">`PageModel`Model bağlamaya, sınıfın tüm ortak özelliklerini hedeflemesini bildirmek için bir denetleyiciye veya sınıfa uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="ba649-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="ba649-143">HTTP GET istekleri için model bağlama</span><span class="sxs-lookup"><span data-stu-id="ba649-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="ba649-144">Varsayılan olarak, Özellikler HTTP GET istekleri için bağlantılı değildir.</span><span class="sxs-lookup"><span data-stu-id="ba649-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="ba649-145">Genellikle, bir GET isteği için tüm ihtiyacınız olan bir kayıt KIMLIĞI parametresidir.</span><span class="sxs-lookup"><span data-stu-id="ba649-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="ba649-146">Kayıt KIMLIĞI, veritabanındaki öğeyi aramak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ba649-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="ba649-147">Bu nedenle, modelin bir örneğini tutan bir özelliği bağlamaya gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="ba649-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="ba649-148">GET isteklerinden alınan özelliklerin verilerine bağlanmasını istediğiniz senaryolarda `SupportsGet` özelliği şu şekilde ayarlayın `true` :</span><span class="sxs-lookup"><span data-stu-id="ba649-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="ba649-149">Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ba649-149">Sources</span></span>

<span data-ttu-id="ba649-150">Varsayılan olarak, model bağlama, bir HTTP isteğindeki aşağıdaki kaynaklardan gelen anahtar-değer çiftleri biçimindeki verileri alır:</span><span class="sxs-lookup"><span data-stu-id="ba649-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="ba649-151">Form alanları</span><span class="sxs-lookup"><span data-stu-id="ba649-151">Form fields</span></span>
1. <span data-ttu-id="ba649-152">İstek gövdesi ( [[ApiController] özniteliğine sahip denetleyiciler](xref:web-api/index#binding-source-parameter-inference)için.)</span><span class="sxs-lookup"><span data-stu-id="ba649-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="ba649-153">Veri yönlendirme</span><span class="sxs-lookup"><span data-stu-id="ba649-153">Route data</span></span>
1. <span data-ttu-id="ba649-154">Sorgu dizesi parametreleri</span><span class="sxs-lookup"><span data-stu-id="ba649-154">Query string parameters</span></span>
1. <span data-ttu-id="ba649-155">Karşıya yüklenen dosyalar</span><span class="sxs-lookup"><span data-stu-id="ba649-155">Uploaded files</span></span>

<span data-ttu-id="ba649-156">Her hedef parametresi veya özelliği için kaynaklar, önceki listede belirtilen sırada taranır.</span><span class="sxs-lookup"><span data-stu-id="ba649-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="ba649-157">Birkaç özel durum vardır:</span><span class="sxs-lookup"><span data-stu-id="ba649-157">There are a few exceptions:</span></span>

* <span data-ttu-id="ba649-158">Rota verileri ve sorgu dizesi değerleri yalnızca basit türler için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ba649-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="ba649-159">Karşıya yüklenen dosyalar yalnızca veya uygulayan hedef türlere bağlanır `IFormFile` `IEnumerable<IFormFile>` .</span><span class="sxs-lookup"><span data-stu-id="ba649-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="ba649-160">Varsayılan kaynak doğru değilse, kaynağı belirtmek için aşağıdaki özniteliklerden birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="ba649-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="ba649-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Sorgu dizesinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="ba649-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="ba649-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Rota verilerinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="ba649-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="ba649-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Postalanan Form alanlarındaki değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="ba649-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="ba649-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -İstek gövdesinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="ba649-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="ba649-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -HTTP başlıklarındaki değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="ba649-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="ba649-166">Bu öznitelikler:</span><span class="sxs-lookup"><span data-stu-id="ba649-166">These attributes:</span></span>

* <span data-ttu-id="ba649-167">Model özelliklerine tek tek eklenir (model sınıfına değil), aşağıdaki örnekte olduğu gibi:</span><span class="sxs-lookup"><span data-stu-id="ba649-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="ba649-168">İsteğe bağlı olarak oluşturucuda bir model adı değeri kabul edin.</span><span class="sxs-lookup"><span data-stu-id="ba649-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="ba649-169">Bu seçenek, özellik adının istekteki değerle eşleşmemesi durumunda sağlanır.</span><span class="sxs-lookup"><span data-stu-id="ba649-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="ba649-170">Örneğin, istekteki değer aşağıdaki örnekte olduğu gibi adında bir tire olan bir üstbilgi olabilir:</span><span class="sxs-lookup"><span data-stu-id="ba649-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="ba649-171">[FromBody] özniteliği</span><span class="sxs-lookup"><span data-stu-id="ba649-171">[FromBody] attribute</span></span>

<span data-ttu-id="ba649-172">`[FromBody]`Özniteliği BIR http isteği gövdesinden doldurmak için bir parametreye özniteliğini uygulayın.</span><span class="sxs-lookup"><span data-stu-id="ba649-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="ba649-173">ASP.NET Core çalışma zamanı, gövdeyi bir giriş biçimlendirici 'ya okumaktan sorumlu olarak temsil eder.</span><span class="sxs-lookup"><span data-stu-id="ba649-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="ba649-174">Giriş biçimleri [Bu makalenin ilerleyen kısımlarında](#input-formatters)açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="ba649-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="ba649-175">`[FromBody]`Karmaşık bir tür parametresine uygulandığında, özelliklerine uygulanan herhangi bir bağlama kaynak özniteliği yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="ba649-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="ba649-176">Örneğin, aşağıdaki eylem, `Create` `pet` parametresinin gövdeden doldurulduğunu belirtir:</span><span class="sxs-lookup"><span data-stu-id="ba649-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="ba649-177">`Pet`Sınıfı, `Breed` özelliğinin bir sorgu dizesi parametresinden doldurulduğunu belirtir:</span><span class="sxs-lookup"><span data-stu-id="ba649-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="ba649-178">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="ba649-178">In the preceding example:</span></span>

* <span data-ttu-id="ba649-179">`[FromQuery]`Öznitelik yoksayıldı.</span><span class="sxs-lookup"><span data-stu-id="ba649-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="ba649-180">`Breed`Özellik bir sorgu dizesi parametresinden doldurulmamış.</span><span class="sxs-lookup"><span data-stu-id="ba649-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="ba649-181">Giriş biçimleri yalnızca gövdeyi okur ve bağlama kaynak özniteliklerini anlamayın.</span><span class="sxs-lookup"><span data-stu-id="ba649-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="ba649-182">Gövdede uygun bir değer bulunursa, bu değer özelliği doldurmak için kullanılır `Breed` .</span><span class="sxs-lookup"><span data-stu-id="ba649-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="ba649-183">`[FromBody]`Action yöntemi başına birden fazla parametreye uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="ba649-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="ba649-184">İstek akışı bir giriş biçimlendirici tarafından okunduktan sonra, diğer parametreleri bağlamak için artık bir daha okunamaz `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="ba649-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="ba649-185">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ba649-185">Additional sources</span></span>

<span data-ttu-id="ba649-186">Kaynak verileri, model bağlama sistemine *değer sağlayıcılara*göre sağlanır.</span><span class="sxs-lookup"><span data-stu-id="ba649-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="ba649-187">Diğer kaynaklardan model bağlamaya yönelik verileri alan özel değer sağlayıcıları yazabilir ve kaydedebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ba649-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="ba649-188">Örneğin, verilerin cookie veya oturum durumunun olmasını isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ba649-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="ba649-189">Yeni bir kaynaktan veri almak için:</span><span class="sxs-lookup"><span data-stu-id="ba649-189">To get data from a new source:</span></span>

* <span data-ttu-id="ba649-190">Uygulayan bir sınıf oluşturun `IValueProvider` .</span><span class="sxs-lookup"><span data-stu-id="ba649-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="ba649-191">Uygulayan bir sınıf oluşturun `IValueProviderFactory` .</span><span class="sxs-lookup"><span data-stu-id="ba649-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="ba649-192">Fabrika sınıfını içine kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ba649-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="ba649-193">Örnek uygulama, ' den değerler alan bir [değer sağlayıcısı](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) ve [Factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) örneği içerir cookie .</span><span class="sxs-lookup"><span data-stu-id="ba649-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="ba649-194">Kayıt kodu aşağıda verilmiştir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ba649-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="ba649-195">Gösterilen kod, tüm yerleşik değer sağlayıcılarından sonra özel değer sağlayıcısını koyar.</span><span class="sxs-lookup"><span data-stu-id="ba649-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="ba649-196">Bunu listede ilk yapmak için `Insert(0, new CookieValueProviderFactory())` yerine çağırın `Add` .</span><span class="sxs-lookup"><span data-stu-id="ba649-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="ba649-197">Model özelliği için kaynak yok</span><span class="sxs-lookup"><span data-stu-id="ba649-197">No source for a model property</span></span>

<span data-ttu-id="ba649-198">Varsayılan olarak, model özelliği için bir değer bulunmazsa model durumu hatası oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="ba649-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="ba649-199">Özelliği null veya varsayılan bir değer olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="ba649-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="ba649-200">Null yapılabilir basit türler olarak ayarlanır `null` .</span><span class="sxs-lookup"><span data-stu-id="ba649-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="ba649-201">Null yapılamayan değer türleri olarak ayarlanır `default(T)` .</span><span class="sxs-lookup"><span data-stu-id="ba649-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="ba649-202">Örneğin, bir parametre `int id` 0 olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="ba649-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="ba649-203">Karmaşık türler için model bağlama, özellikleri ayarlamadan varsayılan oluşturucuyu kullanarak bir örnek oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ba649-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="ba649-204">Diziler olarak ayarlanır `Array.Empty<T>()` , ancak `byte[]` diziler olarak ayarlanır `null` .</span><span class="sxs-lookup"><span data-stu-id="ba649-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="ba649-205">Model özelliği için form alanlarında hiçbir şey bulunamadığında model durumunun geçersiz kılınmalıdır, [`[BindRequired]`](#bindrequired-attribute) özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="ba649-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="ba649-206">Bu `[BindRequired]` davranışın, bir istek GÖVDESINDE JSON veya XML verilerine değil, postalanan form verilerinden model bağlama için geçerli olduğunu unutmayın.</span><span class="sxs-lookup"><span data-stu-id="ba649-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="ba649-207">İstek gövdesi verileri, [giriş formatlayıcıları](#input-formatters)tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="ba649-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="ba649-208">Tür dönüştürme hataları</span><span class="sxs-lookup"><span data-stu-id="ba649-208">Type conversion errors</span></span>

<span data-ttu-id="ba649-209">Bir kaynak bulunursa ancak hedef türe dönüştürülemiyorsa, model durumu geçersiz olarak işaretlenir.</span><span class="sxs-lookup"><span data-stu-id="ba649-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="ba649-210">Hedef parametresi veya özelliği, önceki bölümde belirtildiği gibi null veya varsayılan değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="ba649-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="ba649-211">Özniteliği olan bir API denetleyicisinde `[ApiController]` , geçersiz model durumu OTOMATIK HTTP 400 yanıtına neden olur.</span><span class="sxs-lookup"><span data-stu-id="ba649-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="ba649-212">Bir Razor sayfada, sayfayı bir hata iletisiyle yeniden görüntüleyin:</span><span class="sxs-lookup"><span data-stu-id="ba649-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="ba649-213">İstemci tarafı doğrulama, aksi takdirde bir sayfalar formuna gönderilemeyen hatalı verileri yakalar Razor .</span><span class="sxs-lookup"><span data-stu-id="ba649-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="ba649-214">Bu doğrulama, önceki vurgulanmış kodu tetiklemeyi zorlaştırır.</span><span class="sxs-lookup"><span data-stu-id="ba649-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="ba649-215">Örnek uygulama, teslim **tarihi** alanına hatalı veri yerleştiren ve formu Gönderen **Geçersiz tarih içeren bir Gönder** düğmesi içerir.</span><span class="sxs-lookup"><span data-stu-id="ba649-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="ba649-216">Bu düğme, veri dönüştürme hataları oluştuğunda sayfanın yeniden görüntülenmesine yönelik kodun nasıl çalıştığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="ba649-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="ba649-217">Sayfa önceki kodla yeniden görüntülendiğinde, form alanında geçersiz giriş gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="ba649-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="ba649-218">Bunun nedeni model özelliğinin null ya da varsayılan bir değer olarak ayarlanmış olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="ba649-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="ba649-219">Geçersiz giriş bir hata iletisinde görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ba649-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="ba649-220">Ancak form alanındaki hatalı verileri yeniden görüntülemek istiyorsanız, model özelliğini bir dize haline getirmeyi ve veri dönüştürmeyi el ile gerçekleştirmeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="ba649-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="ba649-221">Tür dönüştürme hatalarının model durumu hatalarına neden olmasını istemiyorsanız aynı strateji önerilir.</span><span class="sxs-lookup"><span data-stu-id="ba649-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="ba649-222">Bu durumda model özelliğini bir dize yapın.</span><span class="sxs-lookup"><span data-stu-id="ba649-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="ba649-223">Basit türler</span><span class="sxs-lookup"><span data-stu-id="ba649-223">Simple types</span></span>

<span data-ttu-id="ba649-224">Model cildin kaynak dizeleri dönüştürebileceğiniz basit türler aşağıdakileri içerir:</span><span class="sxs-lookup"><span data-stu-id="ba649-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="ba649-225">Boolean</span><span class="sxs-lookup"><span data-stu-id="ba649-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="ba649-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="ba649-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="ba649-227">Char</span><span class="sxs-lookup"><span data-stu-id="ba649-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="ba649-228">Tarih Saat</span><span class="sxs-lookup"><span data-stu-id="ba649-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="ba649-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="ba649-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="ba649-230">Kategori</span><span class="sxs-lookup"><span data-stu-id="ba649-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="ba649-231">Çift</span><span class="sxs-lookup"><span data-stu-id="ba649-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="ba649-232">Sabit listesi</span><span class="sxs-lookup"><span data-stu-id="ba649-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="ba649-233">'İni</span><span class="sxs-lookup"><span data-stu-id="ba649-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="ba649-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="ba649-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="ba649-235">Tek</span><span class="sxs-lookup"><span data-stu-id="ba649-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="ba649-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="ba649-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="ba649-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="ba649-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="ba649-238">Kullanılmamışsa</span><span class="sxs-lookup"><span data-stu-id="ba649-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="ba649-239">Sürüm</span><span class="sxs-lookup"><span data-stu-id="ba649-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="ba649-240">Karmaşık türler</span><span class="sxs-lookup"><span data-stu-id="ba649-240">Complex types</span></span>

<span data-ttu-id="ba649-241">Karmaşık bir türün bağlanması için ortak bir varsayılan Oluşturucusu ve ortak yazılabilir özellikleri olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ba649-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="ba649-242">Model bağlama gerçekleştiğinde, sınıf ortak varsayılan Oluşturucu kullanılarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="ba649-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="ba649-243">Karmaşık türün her özelliği için model bağlama, ad modeli ön eki için kaynakları arar *. property_name*.</span><span class="sxs-lookup"><span data-stu-id="ba649-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="ba649-244">Hiçbir şey bulunamazsa, ön ek olmadan yalnızca *property_name* arar.</span><span class="sxs-lookup"><span data-stu-id="ba649-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="ba649-245">Bir parametreye bağlama için, önek parametre adıdır.</span><span class="sxs-lookup"><span data-stu-id="ba649-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="ba649-246">Ortak özelliğe bağlama için `PageModel` , önek ortak özellik adıdır.</span><span class="sxs-lookup"><span data-stu-id="ba649-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="ba649-247">Bazı özniteliklerin `Prefix` , parametre veya özellik adının varsayılan kullanımını geçersiz kılabilmenizi sağlayan bir özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="ba649-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="ba649-248">Örneğin, karmaşık türün aşağıdaki sınıf olduğunu varsayalım `Instructor` :</span><span class="sxs-lookup"><span data-stu-id="ba649-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="ba649-249">Önek = parametre adı</span><span class="sxs-lookup"><span data-stu-id="ba649-249">Prefix = parameter name</span></span>

<span data-ttu-id="ba649-250">Bağlanacak model adlı bir parametre ise `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="ba649-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="ba649-251">Model bağlama, anahtar kaynaklarına bakarak başlar `instructorToUpdate.ID` .</span><span class="sxs-lookup"><span data-stu-id="ba649-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="ba649-252">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="ba649-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="ba649-253">Önek = Özellik adı</span><span class="sxs-lookup"><span data-stu-id="ba649-253">Prefix = property name</span></span>

<span data-ttu-id="ba649-254">Bağlanacak model, `Instructor` denetleyicinin veya sınıfın adında bir özelliktir `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="ba649-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="ba649-255">Model bağlama, anahtar kaynaklarına bakarak başlar `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="ba649-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="ba649-256">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="ba649-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="ba649-257">Özel ön ek</span><span class="sxs-lookup"><span data-stu-id="ba649-257">Custom prefix</span></span>

<span data-ttu-id="ba649-258">Bağlanacak model adlı bir parametre ise `instructorToUpdate` ve bir `Bind` öznitelik `Instructor` önek olarak belirtirse:</span><span class="sxs-lookup"><span data-stu-id="ba649-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="ba649-259">Model bağlama, anahtar kaynaklarına bakarak başlar `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="ba649-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="ba649-260">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="ba649-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="ba649-261">Karmaşık tür hedefleri için öznitelikler</span><span class="sxs-lookup"><span data-stu-id="ba649-261">Attributes for complex type targets</span></span>

<span data-ttu-id="ba649-262">Karmaşık türlerin model bağlamasını denetlemek için birkaç yerleşik öznitelik mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="ba649-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="ba649-263">Bu öznitelikler, gönderilen form verileri değer kaynağı olduğunda model bağlamayı etkiler.</span><span class="sxs-lookup"><span data-stu-id="ba649-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="ba649-264">Bunlar, gönderilen JSON ve XML istek gövdelerini işleyen giriş formatlayıcıları ***'nı etkilemez.***</span><span class="sxs-lookup"><span data-stu-id="ba649-264">They do ***not*** affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="ba649-265">Giriş biçimleri [Bu makalenin ilerleyen kısımlarında](#input-formatters)açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="ba649-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="ba649-266">[Bind] özniteliği</span><span class="sxs-lookup"><span data-stu-id="ba649-266">[Bind] attribute</span></span>

<span data-ttu-id="ba649-267">, Bir sınıfa veya yöntem parametresine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="ba649-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="ba649-268">Model bağlamasındaki bir modelin hangi özelliklerinin dahil edileceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="ba649-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="ba649-269">`[Bind]`Giriş ***formatlarını etkilemez.***</span><span class="sxs-lookup"><span data-stu-id="ba649-269">`[Bind]` does ***not*** affect input formatters.</span></span>

<span data-ttu-id="ba649-270">Aşağıdaki örnekte, `Instructor` herhangi bir işleyici veya eylem yöntemi çağrıldığında yalnızca modelin belirtilen özellikleri bağlanır:</span><span class="sxs-lookup"><span data-stu-id="ba649-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="ba649-271">Aşağıdaki örnekte, `Instructor` yöntemi çağrıldığında yalnızca modelin belirtilen özellikleri bağlanır `OnPost` :</span><span class="sxs-lookup"><span data-stu-id="ba649-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="ba649-272">`[Bind]`Özniteliği, *oluşturma* senaryolarında fazla nakline karşı korumak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ba649-272">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="ba649-273">Dışlanan Özellikler null ya da boş değer olarak ayarlandığı için, düzenleme senaryolarında iyi çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="ba649-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="ba649-274">Fazla naklin savunma için, öznitelik yerine görüntüleme modelleri önerilir `[Bind]` .</span><span class="sxs-lookup"><span data-stu-id="ba649-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="ba649-275">Daha fazla bilgi için bkz. fazla [nakil hakkında güvenlik NOI](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="ba649-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="ba649-276">[BindRequired] özniteliği</span><span class="sxs-lookup"><span data-stu-id="ba649-276">[BindRequired] attribute</span></span>

<span data-ttu-id="ba649-277">, Yöntem parametrelerine değil yalnızca model özelliklerine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="ba649-277">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="ba649-278">Modelin özelliği için bağlama gerçekleşmemişse model bağlamasının model durumu hatası eklemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="ba649-278">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="ba649-279">Aşağıda bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="ba649-279">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="ba649-280">Ayrıca bkz `[Required]` . [model doğrulama](xref:mvc/models/validation#required-attribute)içindeki öznitelik tartışması.</span><span class="sxs-lookup"><span data-stu-id="ba649-280">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="ba649-281">[Bindhiç] özniteliği</span><span class="sxs-lookup"><span data-stu-id="ba649-281">[BindNever] attribute</span></span>

<span data-ttu-id="ba649-282">, Yöntem parametrelerine değil yalnızca model özelliklerine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="ba649-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="ba649-283">Model bağlamasının model özelliğini değiştirmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="ba649-283">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="ba649-284">Aşağıda bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="ba649-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="ba649-285">Koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="ba649-285">Collections</span></span>

<span data-ttu-id="ba649-286">Basit türlerin koleksiyonları olan hedefler için model bağlama, *parameter_name* veya *property_name*ile eşleşmeleri arar.</span><span class="sxs-lookup"><span data-stu-id="ba649-286">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="ba649-287">Eşleşme bulunmazsa, ön ek olmadan desteklenen biçimlerden birini arar.</span><span class="sxs-lookup"><span data-stu-id="ba649-287">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="ba649-288">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ba649-288">For example:</span></span>

* <span data-ttu-id="ba649-289">Bağlanacak parametrenin adlı bir dizi olduğunu varsayalım `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="ba649-289">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="ba649-290">Form veya sorgu dizesi verileri aşağıdaki biçimlerden birinde olabilir:</span><span class="sxs-lookup"><span data-stu-id="ba649-290">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="ba649-291">Aşağıdaki biçim yalnızca form verilerinde desteklenir:</span><span class="sxs-lookup"><span data-stu-id="ba649-291">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="ba649-292">Önceki örnek biçimlerinin hepsi için model bağlama iki öğe dizisini `selectedCourses` parametreye geçirir:</span><span class="sxs-lookup"><span data-stu-id="ba649-292">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="ba649-293">Selectedkurslar [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="ba649-293">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="ba649-294">Selectedkurslar [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="ba649-294">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="ba649-295">Alt simge numaralarını kullanan veri biçimleri (... [0]... [1]...) sıfırdan başlayarak sıralı olarak numaralandırıldıklarından emin olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ba649-295">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="ba649-296">Alt simge numaralandırmasında boşluk varsa, boşluklardan sonraki tüm öğeler yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="ba649-296">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="ba649-297">Örneğin, alt simgeler 0 ve 1 yerine 0 ve 2 ise ikinci öğe yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="ba649-297">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="ba649-298">Sözlükler</span><span class="sxs-lookup"><span data-stu-id="ba649-298">Dictionaries</span></span>

<span data-ttu-id="ba649-299">`Dictionary`Hedefler için, model bağlama *parameter_name* veya *property_name*eşleşme arar.</span><span class="sxs-lookup"><span data-stu-id="ba649-299">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="ba649-300">Eşleşme bulunmazsa, ön ek olmadan desteklenen biçimlerden birini arar.</span><span class="sxs-lookup"><span data-stu-id="ba649-300">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="ba649-301">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ba649-301">For example:</span></span>

* <span data-ttu-id="ba649-302">Hedef parametrenin bir adlandırılmış olduğunu varsayalım `Dictionary<int, string>` `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="ba649-302">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="ba649-303">Postalanan form veya sorgu dizesi verileri aşağıdaki örneklerden birine benzeyebilir:</span><span class="sxs-lookup"><span data-stu-id="ba649-303">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="ba649-304">Önceki örnek biçimlerinin hepsi için model bağlama iki öğenin bir sözlüğünü `selectedCourses` parametreye geçirir:</span><span class="sxs-lookup"><span data-stu-id="ba649-304">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="ba649-305">Selectedkurslar ["1050"] = "Chemistry"</span><span class="sxs-lookup"><span data-stu-id="ba649-305">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="ba649-306">Selectedkurslar ["2000"] = "Ekonomiks"</span><span class="sxs-lookup"><span data-stu-id="ba649-306">selectedCourses["2000"]="Economics"</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a><span data-ttu-id="ba649-307">Oluşturucu bağlama ve kayıt türleri</span><span class="sxs-lookup"><span data-stu-id="ba649-307">Constructor binding and record types</span></span>

<span data-ttu-id="ba649-308">Model bağlama, karmaşık türlerin parametresiz bir oluşturucuya sahip olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ba649-308">Model binding requires that complex types have a parameterless constructor.</span></span> <span data-ttu-id="ba649-309">Hem hem de `System.Text.Json` `Newtonsoft.Json` tabanlı giriş formatlayıcıları parametresiz oluşturucusu olmayan sınıfların serisini kaldırma desteği.</span><span class="sxs-lookup"><span data-stu-id="ba649-309">Both `System.Text.Json` and `Newtonsoft.Json` based input formatters support deserialization of classes that don't have a parameterless constructor.</span></span> 

<span data-ttu-id="ba649-310">C# 9, succinctly ağ üzerinden verileri temsil etmenin harika bir yolu olan kayıt türlerini tanıtır.</span><span class="sxs-lookup"><span data-stu-id="ba649-310">C# 9 introduces record types, which are a great way to succinctly represent data over the network.</span></span> <span data-ttu-id="ba649-311">ASP.NET Core, tek bir Oluşturucu ile model bağlama ve kayıt türlerini doğrulama desteği ekler:</span><span class="sxs-lookup"><span data-stu-id="ba649-311">ASP.NET Core adds support for model binding and validating record types with a single constructor:</span></span>

```csharp
public record Person([Required] string Name, [Range(0, 150)] int Age);

public class PersonController
{
   public IActionResult Index() => View();

   [HttpPost]
   public IActionResult Index(Person person)
   {
       ...
   }
}
```

<span data-ttu-id="ba649-312">`Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="ba649-312">`Person/Index.cshtml`:</span></span>

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

<span data-ttu-id="ba649-313">Kayıt türleri doğrulanırken, çalışma zamanı, doğrulama meta verilerini özellikle özellikler yerine parametreler üzerinde arar.</span><span class="sxs-lookup"><span data-stu-id="ba649-313">When validating record types, the runtime searches for validation metadata specifically on parameters rather than on properties.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="ba649-314">Model bağlama yolu verileri ve sorgu dizeleri için Genelleştirme davranışı</span><span class="sxs-lookup"><span data-stu-id="ba649-314">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="ba649-315">ASP.NET Core yol değeri sağlayıcısı ve sorgu dizesi değer sağlayıcısı:</span><span class="sxs-lookup"><span data-stu-id="ba649-315">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="ba649-316">Değerleri sabit kültür olarak değerlendirin.</span><span class="sxs-lookup"><span data-stu-id="ba649-316">Treat values as invariant culture.</span></span>
* <span data-ttu-id="ba649-317">URL 'Lerin kültür sabiti olmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="ba649-317">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="ba649-318">Buna karşılık, form verilerinden gelen değerler kültüre duyarlı bir dönüştürmeye gider.</span><span class="sxs-lookup"><span data-stu-id="ba649-318">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="ba649-319">Bu, URL 'Lerin yerel ayarlarda paylaşılabilir olması için tasarımdır.</span><span class="sxs-lookup"><span data-stu-id="ba649-319">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="ba649-320">ASP.NET Core yol değeri sağlayıcısını ve sorgu dizesi değeri sağlayıcısını, kültüre duyarlı bir dönüşüme dönüştürmek için:</span><span class="sxs-lookup"><span data-stu-id="ba649-320">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="ba649-321">Şuradan devralma <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="ba649-321">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="ba649-322">[QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) veya [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs) adresinden kodu kopyalayın</span><span class="sxs-lookup"><span data-stu-id="ba649-322">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="ba649-323">Değer sağlayıcısı oluşturucusuna geçirilen [kültür değerini](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture) ile değiştirin</span><span class="sxs-lookup"><span data-stu-id="ba649-323">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="ba649-324">MVC seçeneklerinde varsayılan değer sağlayıcı fabrikasını yeni bir değerle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="ba649-324">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="ba649-325">Özel veri türleri</span><span class="sxs-lookup"><span data-stu-id="ba649-325">Special data types</span></span>

<span data-ttu-id="ba649-326">Model bağlamanın işleyebileceği bazı özel veri türleri vardır.</span><span class="sxs-lookup"><span data-stu-id="ba649-326">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="ba649-327">Iformfile ve ıformfilecollection</span><span class="sxs-lookup"><span data-stu-id="ba649-327">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="ba649-328">HTTP isteğine eklenen karşıya yüklenen dosya.</span><span class="sxs-lookup"><span data-stu-id="ba649-328">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="ba649-329">Ayrıca, `IEnumerable<IFormFile>` birden çok dosya için de desteklenir.</span><span class="sxs-lookup"><span data-stu-id="ba649-329">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="ba649-330">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="ba649-330">CancellationToken</span></span>

<span data-ttu-id="ba649-331">Zaman uyumsuz denetleyicilerde etkinliği iptal etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ba649-331">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="ba649-332">Form koleksiyonu</span><span class="sxs-lookup"><span data-stu-id="ba649-332">FormCollection</span></span>

<span data-ttu-id="ba649-333">Postalanan form verilerinden tüm değerleri almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ba649-333">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="ba649-334">Giriş biçimleri</span><span class="sxs-lookup"><span data-stu-id="ba649-334">Input formatters</span></span>

<span data-ttu-id="ba649-335">İstek gövdesindeki veriler JSON, XML veya başka bir biçimde olabilir.</span><span class="sxs-lookup"><span data-stu-id="ba649-335">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="ba649-336">Model bağlama, bu verileri ayrıştırmak için belirli bir içerik türünü işlemek üzere yapılandırılmış bir *giriş biçimlendiricisi* kullanır.</span><span class="sxs-lookup"><span data-stu-id="ba649-336">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="ba649-337">Varsayılan olarak, ASP.NET Core JSON verilerini işlemek için JSON tabanlı giriş formatlayıcıları içerir.</span><span class="sxs-lookup"><span data-stu-id="ba649-337">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="ba649-338">Diğer içerik türleri için başka biçim ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ba649-338">You can add other formatters for other content types.</span></span>

<span data-ttu-id="ba649-339">ASP.NET Core, [tüketen](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) özniteliğine bağlı olarak giriş formatlayıcıları seçer.</span><span class="sxs-lookup"><span data-stu-id="ba649-339">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="ba649-340">Hiçbir öznitelik yoksa, [Content-Type üst bilgisini](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)kullanır.</span><span class="sxs-lookup"><span data-stu-id="ba649-340">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="ba649-341">Yerleşik XML girişi formatlayıcıları 'nı kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="ba649-341">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="ba649-342">`Microsoft.AspNetCore.Mvc.Formatters.Xml`NuGet paketini yükler.</span><span class="sxs-lookup"><span data-stu-id="ba649-342">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="ba649-343">İçinde `Startup.ConfigureServices` , veya öğesini çağırın <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> .</span><span class="sxs-lookup"><span data-stu-id="ba649-343">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="ba649-344">Özniteliği, `Consumes` istek GÖVDESINDE XML beklemeniz gereken denetleyici sınıflarına veya eylem yöntemlerine uygulayın.</span><span class="sxs-lookup"><span data-stu-id="ba649-344">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="ba649-345">Daha fazla bilgi için bkz. [XML serileştirme tanıtımı](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="ba649-345">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="ba649-346">Giriş formatlayıcıları ile model bağlamayı özelleştirme</span><span class="sxs-lookup"><span data-stu-id="ba649-346">Customize model binding with input formatters</span></span>

<span data-ttu-id="ba649-347">Giriş biçimlendiricisi, istek gövdesinden veri okumak için tam sorumluluğa sahiptir.</span><span class="sxs-lookup"><span data-stu-id="ba649-347">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="ba649-348">Bu işlemi özelleştirmek için, giriş biçimlendirici tarafından kullanılan API 'Leri yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="ba649-348">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="ba649-349">Bu bölümde, `System.Text.Json` adlı özel bir türü anlamak için tabanlı giriş biçimlendiricinin nasıl özelleştirileceği açıklanmaktadır `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="ba649-349">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="ba649-350">Adlı özel bir özellik içeren aşağıdaki modeli göz önünde bulundurun `ObjectId` `Id` :</span><span class="sxs-lookup"><span data-stu-id="ba649-350">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="ba649-351">Kullanırken model bağlama işlemini özelleştirmek için `System.Text.Json` , öğesinden türetilmiş bir sınıf oluşturun <xref:System.Text.Json.Serialization.JsonConverter%601> :</span><span class="sxs-lookup"><span data-stu-id="ba649-351">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="ba649-352">Özel bir dönüştürücü kullanmak için, <xref:System.Text.Json.Serialization.JsonConverterAttribute> türüne özniteliğini uygulayın.</span><span class="sxs-lookup"><span data-stu-id="ba649-352">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="ba649-353">Aşağıdaki örnekte, `ObjectId` türü `ObjectIdConverter` özel dönüştürücü olarak ile yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="ba649-353">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="ba649-354">Daha fazla bilgi için bkz. [özel dönüştürücüler yazma](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="ba649-354">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="ba649-355">Belirtilen türleri model bağlamalarından Dışla</span><span class="sxs-lookup"><span data-stu-id="ba649-355">Exclude specified types from model binding</span></span>

<span data-ttu-id="ba649-356">Model bağlama ve doğrulama sistemlerinin davranışı [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata)tarafından yönlendiriliyor.</span><span class="sxs-lookup"><span data-stu-id="ba649-356">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="ba649-357">`ModelMetadata` [Mvcoptions. modelmetadatadetails sağlayıcılarına](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders)bir ayrıntı sağlayıcısı ekleyerek özelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ba649-357">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="ba649-358">Yerleşik Ayrıntılar sağlayıcıları, belirtilen türler için model bağlamayı veya doğrulamayı devre dışı bırakmak üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ba649-358">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="ba649-359">Belirtilen türdeki tüm modellerdeki model bağlamayı devre dışı bırakmak için içine bir ekleyin <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ba649-359">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ba649-360">Örneğin, türü tüm modeller üzerinde model bağlamayı devre dışı bırakmak için `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="ba649-360">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="ba649-361">Belirtilen türdeki özelliklerde doğrulamayı devre dışı bırakmak için içine bir ekleyin <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ba649-361">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ba649-362">Örneğin, türündeki özelliklerde doğrulamayı devre dışı bırakmak için `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="ba649-362">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="ba649-363">Özel model ciltleri</span><span class="sxs-lookup"><span data-stu-id="ba649-363">Custom model binders</span></span>

<span data-ttu-id="ba649-364">Özel bir model cildi yazarak ve `[ModelBinder]` belirli bir hedef için seçmek üzere özniteliğini kullanarak model bağlamayı genişletebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ba649-364">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="ba649-365">[Özel model bağlama](xref:mvc/advanced/custom-model-binding)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="ba649-365">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="ba649-366">El ile model bağlama</span><span class="sxs-lookup"><span data-stu-id="ba649-366">Manual model binding</span></span> 

<span data-ttu-id="ba649-367">Model bağlama yöntemi kullanılarak el ile çağrılabilir <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> .</span><span class="sxs-lookup"><span data-stu-id="ba649-367">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="ba649-368">Yöntemi hem hem de sınıflarında tanımlanmıştır `ControllerBase` `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="ba649-368">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="ba649-369">Yöntem aşırı yüklemeleri, kullanılacak öneki ve değer sağlayıcısını belirtmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="ba649-369">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="ba649-370">Yöntemi `false` model bağlamanın başarısız olup olmadığını döndürür.</span><span class="sxs-lookup"><span data-stu-id="ba649-370">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="ba649-371">Aşağıda bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="ba649-371">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="ba649-372"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  , form gövdesinden, sorgu dizesinden ve veri yönlendirme verilerinden veri almak için değer sağlayıcılarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="ba649-372"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="ba649-373">`TryUpdateModelAsync` genellikle:</span><span class="sxs-lookup"><span data-stu-id="ba649-373">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="ba649-374">Daha Razor fazla nakletmeyi engellemek için denetleyiciler ve görünümler kullanan sayfalarla ve MVC uygulamalarıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ba649-374">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="ba649-375">Form verilerinden, sorgu dizelerinden ve veri rotalarından tüketilmediği müddetçe bir Web API 'SI ile kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="ba649-375">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="ba649-376">JSON kullanan Web API uç noktaları, istek gövdesini bir nesne olarak seri durumdan çıkarmak için [giriş formatlarını](#input-formatters) kullanır.</span><span class="sxs-lookup"><span data-stu-id="ba649-376">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="ba649-377">Daha fazla bilgi için bkz. [Tryupdatemodelasync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="ba649-377">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="ba649-378">[FromServices] özniteliği</span><span class="sxs-lookup"><span data-stu-id="ba649-378">[FromServices] attribute</span></span>

<span data-ttu-id="ba649-379">Bu özniteliğin adı, bir veri kaynağı belirten model bağlama özniteliklerinin düzeniyle uyar.</span><span class="sxs-lookup"><span data-stu-id="ba649-379">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="ba649-380">Ancak bir değer sağlayıcısından veri bağlama hakkında bilgi yoktur.</span><span class="sxs-lookup"><span data-stu-id="ba649-380">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="ba649-381">[Bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısından bir türün bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="ba649-381">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ba649-382">Amacı, yalnızca belirli bir yöntem çağrılırsa bir hizmete ihtiyacınız olduğunda Oluşturucu ekleme için bir alternatif sağlamaktır.</span><span class="sxs-lookup"><span data-stu-id="ba649-382">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ba649-383">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ba649-383">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ba649-384">Bu makalede, model bağlamanın ne olduğu, nasıl çalıştığı ve davranışını nasıl özelleştireceğiniz açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="ba649-384">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="ba649-385">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="ba649-385">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="ba649-386">Model bağlama nedir?</span><span class="sxs-lookup"><span data-stu-id="ba649-386">What is Model binding</span></span>

<span data-ttu-id="ba649-387">Denetleyiciler ve Razor Sayfalar, http isteklerinden gelen verilerle çalışır.</span><span class="sxs-lookup"><span data-stu-id="ba649-387">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="ba649-388">Örneğin, rota verileri bir kayıt anahtarı sağlayabilir ve postalanan form alanları, modelin özelliklerine ilişkin değerler sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="ba649-388">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="ba649-389">Bu değerlerin her birini almak ve bunları dizelerden .NET türlerine dönüştürmek için kod yazma sıkıcı ve hata durumunda olabilir.</span><span class="sxs-lookup"><span data-stu-id="ba649-389">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="ba649-390">Model bağlama bu işlemi otomatikleştirir.</span><span class="sxs-lookup"><span data-stu-id="ba649-390">Model binding automates this process.</span></span> <span data-ttu-id="ba649-391">Model bağlama sistemi:</span><span class="sxs-lookup"><span data-stu-id="ba649-391">The model binding system:</span></span>

* <span data-ttu-id="ba649-392">Veri yolu, form alanları ve sorgu dizeleri gibi çeşitli kaynaklardan veri alır.</span><span class="sxs-lookup"><span data-stu-id="ba649-392">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="ba649-393">RazorYöntem parametreleri ve ortak özellikler içindeki denetleyicilere ve sayfalara verileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="ba649-393">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="ba649-394">Dize verilerini .NET türlerine dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="ba649-394">Converts string data to .NET types.</span></span>
* <span data-ttu-id="ba649-395">Karmaşık türlerin özelliklerini güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="ba649-395">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="ba649-396">Örnek</span><span class="sxs-lookup"><span data-stu-id="ba649-396">Example</span></span>

<span data-ttu-id="ba649-397">Aşağıdaki eylem yöntemine sahip olduğunuzu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="ba649-397">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="ba649-398">Ve uygulama şu URL ile bir istek alıyor:</span><span class="sxs-lookup"><span data-stu-id="ba649-398">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="ba649-399">Model bağlama, yönlendirme sistemi eylem yöntemini seçtikten sonra aşağıdaki adımlardan geçer:</span><span class="sxs-lookup"><span data-stu-id="ba649-399">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="ba649-400">Adlı bir tamsayı olan ilk parametresini bulur `GetByID` `id` .</span><span class="sxs-lookup"><span data-stu-id="ba649-400">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="ba649-401">HTTP isteğindeki kullanılabilir kaynakları arar ve `id` route verilerinde = "2" bulur.</span><span class="sxs-lookup"><span data-stu-id="ba649-401">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="ba649-402">"2" dizesini tamsayı 2 ' ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="ba649-402">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="ba649-403">`GetByID`Adlı bir Boole değeri olan bir sonraki parametresini bulur `dogsOnly` .</span><span class="sxs-lookup"><span data-stu-id="ba649-403">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="ba649-404">Kaynakları arar ve sorgu dizesinde "DogsOnly = true" bulur.</span><span class="sxs-lookup"><span data-stu-id="ba649-404">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="ba649-405">Ad eşleştirme, büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="ba649-405">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="ba649-406">"True" dizesini Boole değerine dönüştürür `true` .</span><span class="sxs-lookup"><span data-stu-id="ba649-406">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="ba649-407">Framework daha sonra yöntemi çağırır `GetById` , parametresi için 2 ' ye geçerek `id` ve `true` `dogsOnly` parametresi için.</span><span class="sxs-lookup"><span data-stu-id="ba649-407">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="ba649-408">Önceki örnekte, model bağlama hedefleri basit türler olan yöntem parametreleridir.</span><span class="sxs-lookup"><span data-stu-id="ba649-408">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="ba649-409">Hedefler, karmaşık bir türün özellikleri de olabilir.</span><span class="sxs-lookup"><span data-stu-id="ba649-409">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="ba649-410">Her bir özellik başarıyla bağlandıktan sonra, bu özellik için [model doğrulaması](xref:mvc/models/validation) oluşur.</span><span class="sxs-lookup"><span data-stu-id="ba649-410">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="ba649-411">Hangi verilerin modele bağladığına ve tüm bağlama veya doğrulama hatalarıyla ilgili kayıt, [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) veya [Pagemodel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState)içinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="ba649-411">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="ba649-412">Bu işlemin başarılı olup olmadığını öğrenmek için uygulama [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) bayrağını denetler.</span><span class="sxs-lookup"><span data-stu-id="ba649-412">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="ba649-413">Targets</span><span class="sxs-lookup"><span data-stu-id="ba649-413">Targets</span></span>

<span data-ttu-id="ba649-414">Model bağlama, aşağıdaki tür hedeflerin değerlerini bulmayı dener:</span><span class="sxs-lookup"><span data-stu-id="ba649-414">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="ba649-415">Bir isteğin yönlendirildiği denetleyici eylemi yönteminin parametreleri.</span><span class="sxs-lookup"><span data-stu-id="ba649-415">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="ba649-416">RazorBir isteğin yönlendirildiği sayfa işleyicisi yönteminin parametreleri.</span><span class="sxs-lookup"><span data-stu-id="ba649-416">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="ba649-417">Bir denetleyicinin veya `PageModel` sınıfın öznitelikleri tarafından belirtilmişse ortak özellikleri.</span><span class="sxs-lookup"><span data-stu-id="ba649-417">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="ba649-418">[BindProperty] özniteliği</span><span class="sxs-lookup"><span data-stu-id="ba649-418">[BindProperty] attribute</span></span>

<span data-ttu-id="ba649-419">, `PageModel` Model bağlamasının bu özelliği hedeflemesini sağlamak için bir denetleyicinin veya sınıfın ortak özelliğine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="ba649-419">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="ba649-420">[BindProperties] özniteliği</span><span class="sxs-lookup"><span data-stu-id="ba649-420">[BindProperties] attribute</span></span>

<span data-ttu-id="ba649-421">ASP.NET Core 2,1 ve üzeri sürümlerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ba649-421">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="ba649-422">`PageModel`Model bağlamaya, sınıfın tüm ortak özelliklerini hedeflemesini bildirmek için bir denetleyiciye veya sınıfa uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="ba649-422">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="ba649-423">HTTP GET istekleri için model bağlama</span><span class="sxs-lookup"><span data-stu-id="ba649-423">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="ba649-424">Varsayılan olarak, Özellikler HTTP GET istekleri için bağlantılı değildir.</span><span class="sxs-lookup"><span data-stu-id="ba649-424">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="ba649-425">Genellikle, bir GET isteği için tüm ihtiyacınız olan bir kayıt KIMLIĞI parametresidir.</span><span class="sxs-lookup"><span data-stu-id="ba649-425">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="ba649-426">Kayıt KIMLIĞI, veritabanındaki öğeyi aramak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ba649-426">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="ba649-427">Bu nedenle, modelin bir örneğini tutan bir özelliği bağlamaya gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="ba649-427">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="ba649-428">GET isteklerinden alınan özelliklerin verilerine bağlanmasını istediğiniz senaryolarda `SupportsGet` özelliği şu şekilde ayarlayın `true` :</span><span class="sxs-lookup"><span data-stu-id="ba649-428">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="ba649-429">Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ba649-429">Sources</span></span>

<span data-ttu-id="ba649-430">Varsayılan olarak, model bağlama, bir HTTP isteğindeki aşağıdaki kaynaklardan gelen anahtar-değer çiftleri biçimindeki verileri alır:</span><span class="sxs-lookup"><span data-stu-id="ba649-430">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="ba649-431">Form alanları</span><span class="sxs-lookup"><span data-stu-id="ba649-431">Form fields</span></span>
1. <span data-ttu-id="ba649-432">İstek gövdesi ( [[ApiController] özniteliğine sahip denetleyiciler](xref:web-api/index#binding-source-parameter-inference)için.)</span><span class="sxs-lookup"><span data-stu-id="ba649-432">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="ba649-433">Veri yönlendirme</span><span class="sxs-lookup"><span data-stu-id="ba649-433">Route data</span></span>
1. <span data-ttu-id="ba649-434">Sorgu dizesi parametreleri</span><span class="sxs-lookup"><span data-stu-id="ba649-434">Query string parameters</span></span>
1. <span data-ttu-id="ba649-435">Karşıya yüklenen dosyalar</span><span class="sxs-lookup"><span data-stu-id="ba649-435">Uploaded files</span></span>

<span data-ttu-id="ba649-436">Her hedef parametresi veya özelliği için kaynaklar, önceki listede belirtilen sırada taranır.</span><span class="sxs-lookup"><span data-stu-id="ba649-436">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="ba649-437">Birkaç özel durum vardır:</span><span class="sxs-lookup"><span data-stu-id="ba649-437">There are a few exceptions:</span></span>

* <span data-ttu-id="ba649-438">Rota verileri ve sorgu dizesi değerleri yalnızca basit türler için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ba649-438">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="ba649-439">Karşıya yüklenen dosyalar yalnızca veya uygulayan hedef türlere bağlanır `IFormFile` `IEnumerable<IFormFile>` .</span><span class="sxs-lookup"><span data-stu-id="ba649-439">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="ba649-440">Varsayılan kaynak doğru değilse, kaynağı belirtmek için aşağıdaki özniteliklerden birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="ba649-440">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="ba649-441">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Sorgu dizesinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="ba649-441">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="ba649-442">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Rota verilerinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="ba649-442">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="ba649-443">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Postalanan Form alanlarındaki değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="ba649-443">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="ba649-444">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -İstek gövdesinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="ba649-444">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="ba649-445">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -HTTP başlıklarındaki değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="ba649-445">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="ba649-446">Bu öznitelikler:</span><span class="sxs-lookup"><span data-stu-id="ba649-446">These attributes:</span></span>

* <span data-ttu-id="ba649-447">Model özelliklerine tek tek eklenir (model sınıfına değil), aşağıdaki örnekte olduğu gibi:</span><span class="sxs-lookup"><span data-stu-id="ba649-447">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="ba649-448">İsteğe bağlı olarak oluşturucuda bir model adı değeri kabul edin.</span><span class="sxs-lookup"><span data-stu-id="ba649-448">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="ba649-449">Bu seçenek, özellik adının istekteki değerle eşleşmemesi durumunda sağlanır.</span><span class="sxs-lookup"><span data-stu-id="ba649-449">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="ba649-450">Örneğin, istekteki değer aşağıdaki örnekte olduğu gibi adında bir tire olan bir üstbilgi olabilir:</span><span class="sxs-lookup"><span data-stu-id="ba649-450">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="ba649-451">[FromBody] özniteliği</span><span class="sxs-lookup"><span data-stu-id="ba649-451">[FromBody] attribute</span></span>

<span data-ttu-id="ba649-452">`[FromBody]`Özniteliği BIR http isteği gövdesinden doldurmak için bir parametreye özniteliğini uygulayın.</span><span class="sxs-lookup"><span data-stu-id="ba649-452">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="ba649-453">ASP.NET Core çalışma zamanı, gövdeyi bir giriş biçimlendirici 'ya okumaktan sorumlu olarak temsil eder.</span><span class="sxs-lookup"><span data-stu-id="ba649-453">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="ba649-454">Giriş biçimleri [Bu makalenin ilerleyen kısımlarında](#input-formatters)açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="ba649-454">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="ba649-455">`[FromBody]`Karmaşık bir tür parametresine uygulandığında, özelliklerine uygulanan herhangi bir bağlama kaynak özniteliği yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="ba649-455">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="ba649-456">Örneğin, aşağıdaki eylem, `Create` `pet` parametresinin gövdeden doldurulduğunu belirtir:</span><span class="sxs-lookup"><span data-stu-id="ba649-456">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="ba649-457">`Pet`Sınıfı, `Breed` özelliğinin bir sorgu dizesi parametresinden doldurulduğunu belirtir:</span><span class="sxs-lookup"><span data-stu-id="ba649-457">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="ba649-458">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="ba649-458">In the preceding example:</span></span>

* <span data-ttu-id="ba649-459">`[FromQuery]`Öznitelik yoksayıldı.</span><span class="sxs-lookup"><span data-stu-id="ba649-459">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="ba649-460">`Breed`Özellik bir sorgu dizesi parametresinden doldurulmamış.</span><span class="sxs-lookup"><span data-stu-id="ba649-460">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="ba649-461">Giriş biçimleri yalnızca gövdeyi okur ve bağlama kaynak özniteliklerini anlamayın.</span><span class="sxs-lookup"><span data-stu-id="ba649-461">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="ba649-462">Gövdede uygun bir değer bulunursa, bu değer özelliği doldurmak için kullanılır `Breed` .</span><span class="sxs-lookup"><span data-stu-id="ba649-462">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="ba649-463">`[FromBody]`Action yöntemi başına birden fazla parametreye uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="ba649-463">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="ba649-464">İstek akışı bir giriş biçimlendirici tarafından okunduktan sonra, diğer parametreleri bağlamak için artık bir daha okunamaz `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="ba649-464">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="ba649-465">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ba649-465">Additional sources</span></span>

<span data-ttu-id="ba649-466">Kaynak verileri, model bağlama sistemine *değer sağlayıcılara*göre sağlanır.</span><span class="sxs-lookup"><span data-stu-id="ba649-466">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="ba649-467">Diğer kaynaklardan model bağlamaya yönelik verileri alan özel değer sağlayıcıları yazabilir ve kaydedebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ba649-467">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="ba649-468">Örneğin, verilerin cookie veya oturum durumunun olmasını isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ba649-468">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="ba649-469">Yeni bir kaynaktan veri almak için:</span><span class="sxs-lookup"><span data-stu-id="ba649-469">To get data from a new source:</span></span>

* <span data-ttu-id="ba649-470">Uygulayan bir sınıf oluşturun `IValueProvider` .</span><span class="sxs-lookup"><span data-stu-id="ba649-470">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="ba649-471">Uygulayan bir sınıf oluşturun `IValueProviderFactory` .</span><span class="sxs-lookup"><span data-stu-id="ba649-471">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="ba649-472">Fabrika sınıfını içine kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ba649-472">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="ba649-473">Örnek uygulama, ' den değerler alan bir [değer sağlayıcısı](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) ve [Factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) örneği içerir cookie .</span><span class="sxs-lookup"><span data-stu-id="ba649-473">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="ba649-474">Kayıt kodu aşağıda verilmiştir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ba649-474">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="ba649-475">Gösterilen kod, tüm yerleşik değer sağlayıcılarından sonra özel değer sağlayıcısını koyar.</span><span class="sxs-lookup"><span data-stu-id="ba649-475">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="ba649-476">Bunu listede ilk yapmak için `Insert(0, new CookieValueProviderFactory())` yerine çağırın `Add` .</span><span class="sxs-lookup"><span data-stu-id="ba649-476">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="ba649-477">Model özelliği için kaynak yok</span><span class="sxs-lookup"><span data-stu-id="ba649-477">No source for a model property</span></span>

<span data-ttu-id="ba649-478">Varsayılan olarak, model özelliği için bir değer bulunmazsa model durumu hatası oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="ba649-478">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="ba649-479">Özelliği null veya varsayılan bir değer olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="ba649-479">The property is set to null or a default value:</span></span>

* <span data-ttu-id="ba649-480">Null yapılabilir basit türler olarak ayarlanır `null` .</span><span class="sxs-lookup"><span data-stu-id="ba649-480">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="ba649-481">Null yapılamayan değer türleri olarak ayarlanır `default(T)` .</span><span class="sxs-lookup"><span data-stu-id="ba649-481">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="ba649-482">Örneğin, bir parametre `int id` 0 olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="ba649-482">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="ba649-483">Karmaşık türler için model bağlama, özellikleri ayarlamadan varsayılan oluşturucuyu kullanarak bir örnek oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ba649-483">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="ba649-484">Diziler olarak ayarlanır `Array.Empty<T>()` , ancak `byte[]` diziler olarak ayarlanır `null` .</span><span class="sxs-lookup"><span data-stu-id="ba649-484">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="ba649-485">Model özelliği için form alanlarında hiçbir şey bulunamadığında model durumunun geçersiz kılınmalıdır, [`[BindRequired]`](#bindrequired-attribute) özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="ba649-485">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="ba649-486">Bu `[BindRequired]` davranışın, bir istek GÖVDESINDE JSON veya XML verilerine değil, postalanan form verilerinden model bağlama için geçerli olduğunu unutmayın.</span><span class="sxs-lookup"><span data-stu-id="ba649-486">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="ba649-487">İstek gövdesi verileri, [giriş formatlayıcıları](#input-formatters)tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="ba649-487">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="ba649-488">Tür dönüştürme hataları</span><span class="sxs-lookup"><span data-stu-id="ba649-488">Type conversion errors</span></span>

<span data-ttu-id="ba649-489">Bir kaynak bulunursa ancak hedef türe dönüştürülemiyorsa, model durumu geçersiz olarak işaretlenir.</span><span class="sxs-lookup"><span data-stu-id="ba649-489">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="ba649-490">Hedef parametresi veya özelliği, önceki bölümde belirtildiği gibi null veya varsayılan değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="ba649-490">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="ba649-491">Özniteliği olan bir API denetleyicisinde `[ApiController]` , geçersiz model durumu OTOMATIK HTTP 400 yanıtına neden olur.</span><span class="sxs-lookup"><span data-stu-id="ba649-491">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="ba649-492">Bir Razor sayfada, sayfayı bir hata iletisiyle yeniden görüntüleyin:</span><span class="sxs-lookup"><span data-stu-id="ba649-492">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="ba649-493">İstemci tarafı doğrulama, aksi takdirde bir sayfalar formuna gönderilemeyen hatalı verileri yakalar Razor .</span><span class="sxs-lookup"><span data-stu-id="ba649-493">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="ba649-494">Bu doğrulama, önceki vurgulanmış kodu tetiklemeyi zorlaştırır.</span><span class="sxs-lookup"><span data-stu-id="ba649-494">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="ba649-495">Örnek uygulama, teslim **tarihi** alanına hatalı veri yerleştiren ve formu Gönderen **Geçersiz tarih içeren bir Gönder** düğmesi içerir.</span><span class="sxs-lookup"><span data-stu-id="ba649-495">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="ba649-496">Bu düğme, veri dönüştürme hataları oluştuğunda sayfanın yeniden görüntülenmesine yönelik kodun nasıl çalıştığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="ba649-496">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="ba649-497">Sayfa önceki kodla yeniden görüntülendiğinde, form alanında geçersiz giriş gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="ba649-497">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="ba649-498">Bunun nedeni model özelliğinin null ya da varsayılan bir değer olarak ayarlanmış olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="ba649-498">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="ba649-499">Geçersiz giriş bir hata iletisinde görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ba649-499">The invalid input does appear in an error message.</span></span> <span data-ttu-id="ba649-500">Ancak form alanındaki hatalı verileri yeniden görüntülemek istiyorsanız, model özelliğini bir dize haline getirmeyi ve veri dönüştürmeyi el ile gerçekleştirmeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="ba649-500">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="ba649-501">Tür dönüştürme hatalarının model durumu hatalarına neden olmasını istemiyorsanız aynı strateji önerilir.</span><span class="sxs-lookup"><span data-stu-id="ba649-501">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="ba649-502">Bu durumda model özelliğini bir dize yapın.</span><span class="sxs-lookup"><span data-stu-id="ba649-502">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="ba649-503">Basit türler</span><span class="sxs-lookup"><span data-stu-id="ba649-503">Simple types</span></span>

<span data-ttu-id="ba649-504">Model cildin kaynak dizeleri dönüştürebileceğiniz basit türler aşağıdakileri içerir:</span><span class="sxs-lookup"><span data-stu-id="ba649-504">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="ba649-505">Boolean</span><span class="sxs-lookup"><span data-stu-id="ba649-505">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="ba649-506">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="ba649-506">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="ba649-507">Char</span><span class="sxs-lookup"><span data-stu-id="ba649-507">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="ba649-508">Tarih Saat</span><span class="sxs-lookup"><span data-stu-id="ba649-508">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="ba649-509">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="ba649-509">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="ba649-510">Kategori</span><span class="sxs-lookup"><span data-stu-id="ba649-510">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="ba649-511">Çift</span><span class="sxs-lookup"><span data-stu-id="ba649-511">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="ba649-512">Sabit listesi</span><span class="sxs-lookup"><span data-stu-id="ba649-512">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="ba649-513">'İni</span><span class="sxs-lookup"><span data-stu-id="ba649-513">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="ba649-514">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="ba649-514">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="ba649-515">Tek</span><span class="sxs-lookup"><span data-stu-id="ba649-515">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="ba649-516">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="ba649-516">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="ba649-517">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="ba649-517">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="ba649-518">Kullanılmamışsa</span><span class="sxs-lookup"><span data-stu-id="ba649-518">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="ba649-519">Sürüm</span><span class="sxs-lookup"><span data-stu-id="ba649-519">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="ba649-520">Karmaşık türler</span><span class="sxs-lookup"><span data-stu-id="ba649-520">Complex types</span></span>

<span data-ttu-id="ba649-521">Karmaşık bir türün bağlanması için ortak bir varsayılan Oluşturucusu ve ortak yazılabilir özellikleri olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ba649-521">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="ba649-522">Model bağlama gerçekleştiğinde, sınıf ortak varsayılan Oluşturucu kullanılarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="ba649-522">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="ba649-523">Karmaşık türün her özelliği için model bağlama, ad modeli ön eki için kaynakları arar *. property_name*.</span><span class="sxs-lookup"><span data-stu-id="ba649-523">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="ba649-524">Hiçbir şey bulunamazsa, ön ek olmadan yalnızca *property_name* arar.</span><span class="sxs-lookup"><span data-stu-id="ba649-524">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="ba649-525">Bir parametreye bağlama için, önek parametre adıdır.</span><span class="sxs-lookup"><span data-stu-id="ba649-525">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="ba649-526">Ortak özelliğe bağlama için `PageModel` , önek ortak özellik adıdır.</span><span class="sxs-lookup"><span data-stu-id="ba649-526">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="ba649-527">Bazı özniteliklerin `Prefix` , parametre veya özellik adının varsayılan kullanımını geçersiz kılabilmenizi sağlayan bir özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="ba649-527">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="ba649-528">Örneğin, karmaşık türün aşağıdaki sınıf olduğunu varsayalım `Instructor` :</span><span class="sxs-lookup"><span data-stu-id="ba649-528">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="ba649-529">Önek = parametre adı</span><span class="sxs-lookup"><span data-stu-id="ba649-529">Prefix = parameter name</span></span>

<span data-ttu-id="ba649-530">Bağlanacak model adlı bir parametre ise `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="ba649-530">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="ba649-531">Model bağlama, anahtar kaynaklarına bakarak başlar `instructorToUpdate.ID` .</span><span class="sxs-lookup"><span data-stu-id="ba649-531">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="ba649-532">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="ba649-532">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="ba649-533">Önek = Özellik adı</span><span class="sxs-lookup"><span data-stu-id="ba649-533">Prefix = property name</span></span>

<span data-ttu-id="ba649-534">Bağlanacak model, `Instructor` denetleyicinin veya sınıfın adında bir özelliktir `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="ba649-534">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="ba649-535">Model bağlama, anahtar kaynaklarına bakarak başlar `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="ba649-535">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="ba649-536">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="ba649-536">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="ba649-537">Özel ön ek</span><span class="sxs-lookup"><span data-stu-id="ba649-537">Custom prefix</span></span>

<span data-ttu-id="ba649-538">Bağlanacak model adlı bir parametre ise `instructorToUpdate` ve bir `Bind` öznitelik `Instructor` önek olarak belirtirse:</span><span class="sxs-lookup"><span data-stu-id="ba649-538">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="ba649-539">Model bağlama, anahtar kaynaklarına bakarak başlar `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="ba649-539">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="ba649-540">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="ba649-540">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="ba649-541">Karmaşık tür hedefleri için öznitelikler</span><span class="sxs-lookup"><span data-stu-id="ba649-541">Attributes for complex type targets</span></span>

<span data-ttu-id="ba649-542">Karmaşık türlerin model bağlamasını denetlemek için birkaç yerleşik öznitelik mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="ba649-542">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="ba649-543">Bu öznitelikler, gönderilen form verileri değer kaynağı olduğunda model bağlamayı etkiler.</span><span class="sxs-lookup"><span data-stu-id="ba649-543">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="ba649-544">Bunlar, gönderilen JSON ve XML istek gövdelerini işleyen giriş formatlayıcıları 'nı etkilemez.</span><span class="sxs-lookup"><span data-stu-id="ba649-544">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="ba649-545">Giriş biçimleri [Bu makalenin ilerleyen kısımlarında](#input-formatters)açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="ba649-545">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="ba649-546">Ayrıca bkz `[Required]` . [model doğrulama](xref:mvc/models/validation#required-attribute)içindeki öznitelik tartışması.</span><span class="sxs-lookup"><span data-stu-id="ba649-546">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="ba649-547">[BindRequired] özniteliği</span><span class="sxs-lookup"><span data-stu-id="ba649-547">[BindRequired] attribute</span></span>

<span data-ttu-id="ba649-548">, Yöntem parametrelerine değil yalnızca model özelliklerine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="ba649-548">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="ba649-549">Modelin özelliği için bağlama gerçekleşmemişse model bağlamasının model durumu hatası eklemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="ba649-549">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="ba649-550">Aşağıda bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="ba649-550">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="ba649-551">[Bindhiç] özniteliği</span><span class="sxs-lookup"><span data-stu-id="ba649-551">[BindNever] attribute</span></span>

<span data-ttu-id="ba649-552">, Yöntem parametrelerine değil yalnızca model özelliklerine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="ba649-552">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="ba649-553">Model bağlamasının model özelliğini değiştirmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="ba649-553">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="ba649-554">Aşağıda bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="ba649-554">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="ba649-555">[Bind] özniteliği</span><span class="sxs-lookup"><span data-stu-id="ba649-555">[Bind] attribute</span></span>

<span data-ttu-id="ba649-556">, Bir sınıfa veya yöntem parametresine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="ba649-556">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="ba649-557">Model bağlamasındaki bir modelin hangi özelliklerinin dahil edileceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="ba649-557">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="ba649-558">Aşağıdaki örnekte, `Instructor` herhangi bir işleyici veya eylem yöntemi çağrıldığında yalnızca modelin belirtilen özellikleri bağlanır:</span><span class="sxs-lookup"><span data-stu-id="ba649-558">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="ba649-559">Aşağıdaki örnekte, `Instructor` yöntemi çağrıldığında yalnızca modelin belirtilen özellikleri bağlanır `OnPost` :</span><span class="sxs-lookup"><span data-stu-id="ba649-559">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="ba649-560">`[Bind]`Özniteliği, *oluşturma* senaryolarında fazla nakline karşı korumak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ba649-560">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="ba649-561">Dışlanan Özellikler null ya da boş değer olarak ayarlandığı için, düzenleme senaryolarında iyi çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="ba649-561">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="ba649-562">Fazla naklin savunma için, öznitelik yerine görüntüleme modelleri önerilir `[Bind]` .</span><span class="sxs-lookup"><span data-stu-id="ba649-562">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="ba649-563">Daha fazla bilgi için bkz. fazla [nakil hakkında güvenlik NOI](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="ba649-563">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="ba649-564">Koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="ba649-564">Collections</span></span>

<span data-ttu-id="ba649-565">Basit türlerin koleksiyonları olan hedefler için model bağlama, *parameter_name* veya *property_name*ile eşleşmeleri arar.</span><span class="sxs-lookup"><span data-stu-id="ba649-565">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="ba649-566">Eşleşme bulunmazsa, ön ek olmadan desteklenen biçimlerden birini arar.</span><span class="sxs-lookup"><span data-stu-id="ba649-566">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="ba649-567">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ba649-567">For example:</span></span>

* <span data-ttu-id="ba649-568">Bağlanacak parametrenin adlı bir dizi olduğunu varsayalım `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="ba649-568">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="ba649-569">Form veya sorgu dizesi verileri aşağıdaki biçimlerden birinde olabilir:</span><span class="sxs-lookup"><span data-stu-id="ba649-569">Form or query string data can be in one of the following formats:</span></span>
   
  ```
  selectedCourses=1050&selectedCourses=2000 
  ```

  ```
  selectedCourses[0]=1050&selectedCourses[1]=2000
  ```

  ```
  [0]=1050&[1]=2000
  ```

  ```
  selectedCourses[a]=1050&selectedCourses[b]=2000&selectedCourses.index=a&selectedCourses.index=b
  ```

  ```
  [a]=1050&[b]=2000&index=a&index=b
  ```

* <span data-ttu-id="ba649-570">Aşağıdaki biçim yalnızca form verilerinde desteklenir:</span><span class="sxs-lookup"><span data-stu-id="ba649-570">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="ba649-571">Önceki örnek biçimlerinin hepsi için model bağlama iki öğe dizisini `selectedCourses` parametreye geçirir:</span><span class="sxs-lookup"><span data-stu-id="ba649-571">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="ba649-572">Selectedkurslar [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="ba649-572">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="ba649-573">Selectedkurslar [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="ba649-573">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="ba649-574">Alt simge numaralarını kullanan veri biçimleri (... [0]... [1]...) sıfırdan başlayarak sıralı olarak numaralandırıldıklarından emin olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="ba649-574">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="ba649-575">Alt simge numaralandırmasında boşluk varsa, boşluklardan sonraki tüm öğeler yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="ba649-575">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="ba649-576">Örneğin, alt simgeler 0 ve 1 yerine 0 ve 2 ise ikinci öğe yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="ba649-576">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="ba649-577">Sözlükler</span><span class="sxs-lookup"><span data-stu-id="ba649-577">Dictionaries</span></span>

<span data-ttu-id="ba649-578">`Dictionary`Hedefler için, model bağlama *parameter_name* veya *property_name*eşleşme arar.</span><span class="sxs-lookup"><span data-stu-id="ba649-578">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="ba649-579">Eşleşme bulunmazsa, ön ek olmadan desteklenen biçimlerden birini arar.</span><span class="sxs-lookup"><span data-stu-id="ba649-579">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="ba649-580">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ba649-580">For example:</span></span>

* <span data-ttu-id="ba649-581">Hedef parametrenin bir adlandırılmış olduğunu varsayalım `Dictionary<int, string>` `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="ba649-581">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="ba649-582">Postalanan form veya sorgu dizesi verileri aşağıdaki örneklerden birine benzeyebilir:</span><span class="sxs-lookup"><span data-stu-id="ba649-582">The posted form or query string data can look like one of the following examples:</span></span>

  ```
  selectedCourses[1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  [1050]=Chemistry&selectedCourses[2000]=Economics
  ```

  ```
  selectedCourses[0].Key=1050&selectedCourses[0].Value=Chemistry&
  selectedCourses[1].Key=2000&selectedCourses[1].Value=Economics
  ```

  ```
  [0].Key=1050&[0].Value=Chemistry&[1].Key=2000&[1].Value=Economics
  ```

* <span data-ttu-id="ba649-583">Önceki örnek biçimlerinin hepsi için model bağlama iki öğenin bir sözlüğünü `selectedCourses` parametreye geçirir:</span><span class="sxs-lookup"><span data-stu-id="ba649-583">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="ba649-584">Selectedkurslar ["1050"] = "Chemistry"</span><span class="sxs-lookup"><span data-stu-id="ba649-584">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="ba649-585">Selectedkurslar ["2000"] = "Ekonomiks"</span><span class="sxs-lookup"><span data-stu-id="ba649-585">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="ba649-586">Model bağlama yolu verileri ve sorgu dizeleri için Genelleştirme davranışı</span><span class="sxs-lookup"><span data-stu-id="ba649-586">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="ba649-587">ASP.NET Core yol değeri sağlayıcısı ve sorgu dizesi değer sağlayıcısı:</span><span class="sxs-lookup"><span data-stu-id="ba649-587">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="ba649-588">Değerleri sabit kültür olarak değerlendirin.</span><span class="sxs-lookup"><span data-stu-id="ba649-588">Treat values as invariant culture.</span></span>
* <span data-ttu-id="ba649-589">URL 'Lerin kültür sabiti olmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="ba649-589">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="ba649-590">Buna karşılık, form verilerinden gelen değerler kültüre duyarlı bir dönüştürmeye gider.</span><span class="sxs-lookup"><span data-stu-id="ba649-590">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="ba649-591">Bu, URL 'Lerin yerel ayarlarda paylaşılabilir olması için tasarımdır.</span><span class="sxs-lookup"><span data-stu-id="ba649-591">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="ba649-592">ASP.NET Core yol değeri sağlayıcısını ve sorgu dizesi değeri sağlayıcısını, kültüre duyarlı bir dönüşüme dönüştürmek için:</span><span class="sxs-lookup"><span data-stu-id="ba649-592">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="ba649-593">Şuradan devralma <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="ba649-593">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="ba649-594">[QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) veya [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs) adresinden kodu kopyalayın</span><span class="sxs-lookup"><span data-stu-id="ba649-594">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="ba649-595">Değer sağlayıcısı oluşturucusuna geçirilen [kültür değerini](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture) ile değiştirin</span><span class="sxs-lookup"><span data-stu-id="ba649-595">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="ba649-596">MVC seçeneklerinde varsayılan değer sağlayıcı fabrikasını yeni bir değerle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="ba649-596">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="ba649-597">Özel veri türleri</span><span class="sxs-lookup"><span data-stu-id="ba649-597">Special data types</span></span>

<span data-ttu-id="ba649-598">Model bağlamanın işleyebileceği bazı özel veri türleri vardır.</span><span class="sxs-lookup"><span data-stu-id="ba649-598">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="ba649-599">Iformfile ve ıformfilecollection</span><span class="sxs-lookup"><span data-stu-id="ba649-599">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="ba649-600">HTTP isteğine eklenen karşıya yüklenen dosya.</span><span class="sxs-lookup"><span data-stu-id="ba649-600">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="ba649-601">Ayrıca, `IEnumerable<IFormFile>` birden çok dosya için de desteklenir.</span><span class="sxs-lookup"><span data-stu-id="ba649-601">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="ba649-602">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="ba649-602">CancellationToken</span></span>

<span data-ttu-id="ba649-603">Zaman uyumsuz denetleyicilerde etkinliği iptal etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ba649-603">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="ba649-604">Form koleksiyonu</span><span class="sxs-lookup"><span data-stu-id="ba649-604">FormCollection</span></span>

<span data-ttu-id="ba649-605">Postalanan form verilerinden tüm değerleri almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ba649-605">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="ba649-606">Giriş biçimleri</span><span class="sxs-lookup"><span data-stu-id="ba649-606">Input formatters</span></span>

<span data-ttu-id="ba649-607">İstek gövdesindeki veriler JSON, XML veya başka bir biçimde olabilir.</span><span class="sxs-lookup"><span data-stu-id="ba649-607">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="ba649-608">Model bağlama, bu verileri ayrıştırmak için belirli bir içerik türünü işlemek üzere yapılandırılmış bir *giriş biçimlendiricisi* kullanır.</span><span class="sxs-lookup"><span data-stu-id="ba649-608">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="ba649-609">Varsayılan olarak, ASP.NET Core JSON verilerini işlemek için JSON tabanlı giriş formatlayıcıları içerir.</span><span class="sxs-lookup"><span data-stu-id="ba649-609">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="ba649-610">Diğer içerik türleri için başka biçim ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ba649-610">You can add other formatters for other content types.</span></span>

<span data-ttu-id="ba649-611">ASP.NET Core, [tüketen](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) özniteliğine bağlı olarak giriş formatlayıcıları seçer.</span><span class="sxs-lookup"><span data-stu-id="ba649-611">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="ba649-612">Hiçbir öznitelik yoksa, [Content-Type üst bilgisini](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)kullanır.</span><span class="sxs-lookup"><span data-stu-id="ba649-612">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="ba649-613">Yerleşik XML girişi formatlayıcıları 'nı kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="ba649-613">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="ba649-614">`Microsoft.AspNetCore.Mvc.Formatters.Xml`NuGet paketini yükler.</span><span class="sxs-lookup"><span data-stu-id="ba649-614">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="ba649-615">İçinde `Startup.ConfigureServices` , veya öğesini çağırın <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> .</span><span class="sxs-lookup"><span data-stu-id="ba649-615">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="ba649-616">Özniteliği, `Consumes` istek GÖVDESINDE XML beklemeniz gereken denetleyici sınıflarına veya eylem yöntemlerine uygulayın.</span><span class="sxs-lookup"><span data-stu-id="ba649-616">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="ba649-617">Daha fazla bilgi için bkz. [XML serileştirme tanıtımı](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="ba649-617">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="ba649-618">Belirtilen türleri model bağlamalarından Dışla</span><span class="sxs-lookup"><span data-stu-id="ba649-618">Exclude specified types from model binding</span></span>

<span data-ttu-id="ba649-619">Model bağlama ve doğrulama sistemlerinin davranışı [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata)tarafından yönlendiriliyor.</span><span class="sxs-lookup"><span data-stu-id="ba649-619">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="ba649-620">`ModelMetadata` [Mvcoptions. modelmetadatadetails sağlayıcılarına](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders)bir ayrıntı sağlayıcısı ekleyerek özelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ba649-620">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="ba649-621">Yerleşik Ayrıntılar sağlayıcıları, belirtilen türler için model bağlamayı veya doğrulamayı devre dışı bırakmak üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="ba649-621">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="ba649-622">Belirtilen türdeki tüm modellerdeki model bağlamayı devre dışı bırakmak için içine bir ekleyin <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ba649-622">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ba649-623">Örneğin, türü tüm modeller üzerinde model bağlamayı devre dışı bırakmak için `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="ba649-623">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="ba649-624">Belirtilen türdeki özelliklerde doğrulamayı devre dışı bırakmak için içine bir ekleyin <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ba649-624">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ba649-625">Örneğin, türündeki özelliklerde doğrulamayı devre dışı bırakmak için `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="ba649-625">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="ba649-626">Özel model ciltleri</span><span class="sxs-lookup"><span data-stu-id="ba649-626">Custom model binders</span></span>

<span data-ttu-id="ba649-627">Özel bir model cildi yazarak ve `[ModelBinder]` belirli bir hedef için seçmek üzere özniteliğini kullanarak model bağlamayı genişletebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="ba649-627">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="ba649-628">[Özel model bağlama](xref:mvc/advanced/custom-model-binding)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="ba649-628">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="ba649-629">El ile model bağlama</span><span class="sxs-lookup"><span data-stu-id="ba649-629">Manual model binding</span></span>

<span data-ttu-id="ba649-630">Model bağlama yöntemi kullanılarak el ile çağrılabilir <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> .</span><span class="sxs-lookup"><span data-stu-id="ba649-630">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="ba649-631">Yöntemi hem hem de sınıflarında tanımlanmıştır `ControllerBase` `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="ba649-631">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="ba649-632">Yöntem aşırı yüklemeleri, kullanılacak öneki ve değer sağlayıcısını belirtmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="ba649-632">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="ba649-633">Yöntemi `false` model bağlamanın başarısız olup olmadığını döndürür.</span><span class="sxs-lookup"><span data-stu-id="ba649-633">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="ba649-634">Aşağıda bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="ba649-634">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="ba649-635">[FromServices] özniteliği</span><span class="sxs-lookup"><span data-stu-id="ba649-635">[FromServices] attribute</span></span>

<span data-ttu-id="ba649-636">Bu özniteliğin adı, bir veri kaynağı belirten model bağlama özniteliklerinin düzeniyle uyar.</span><span class="sxs-lookup"><span data-stu-id="ba649-636">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="ba649-637">Ancak bir değer sağlayıcısından veri bağlama hakkında bilgi yoktur.</span><span class="sxs-lookup"><span data-stu-id="ba649-637">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="ba649-638">[Bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısından bir türün bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="ba649-638">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="ba649-639">Amacı, yalnızca belirli bir yöntem çağrılırsa bir hizmete ihtiyacınız olduğunda Oluşturucu ekleme için bir alternatif sağlamaktır.</span><span class="sxs-lookup"><span data-stu-id="ba649-639">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="ba649-640">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="ba649-640">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
