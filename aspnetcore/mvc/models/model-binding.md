---
title: ASP.NET Core 'de model bağlama
author: rick-anderson
description: ASP.NET Core model bağlamasının nasıl çalıştığını ve davranışını nasıl özelleştireceğinizi öğrenin.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
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
uid: mvc/models/model-binding
ms.openlocfilehash: 6ec531a04a220f75f5793cb2c7b5232908dbd883
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019163"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="dc292-103">ASP.NET Core 'de model bağlama</span><span class="sxs-lookup"><span data-stu-id="dc292-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="dc292-104">Bu makalede, model bağlamanın ne olduğu, nasıl çalıştığı ve davranışını nasıl özelleştireceğiniz açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="dc292-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="dc292-105">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="dc292-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="dc292-106">Model bağlama nedir?</span><span class="sxs-lookup"><span data-stu-id="dc292-106">What is Model binding</span></span>

<span data-ttu-id="dc292-107">Denetleyiciler ve Razor Sayfalar, http isteklerinden gelen verilerle çalışır.</span><span class="sxs-lookup"><span data-stu-id="dc292-107">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="dc292-108">Örneğin, rota verileri bir kayıt anahtarı sağlayabilir ve postalanan form alanları, modelin özelliklerine ilişkin değerler sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="dc292-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="dc292-109">Bu değerlerin her birini almak ve bunları dizelerden .NET türlerine dönüştürmek için kod yazma sıkıcı ve hata durumunda olabilir.</span><span class="sxs-lookup"><span data-stu-id="dc292-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="dc292-110">Model bağlama bu işlemi otomatikleştirir.</span><span class="sxs-lookup"><span data-stu-id="dc292-110">Model binding automates this process.</span></span> <span data-ttu-id="dc292-111">Model bağlama sistemi:</span><span class="sxs-lookup"><span data-stu-id="dc292-111">The model binding system:</span></span>

* <span data-ttu-id="dc292-112">Veri yolu, form alanları ve sorgu dizeleri gibi çeşitli kaynaklardan veri alır.</span><span class="sxs-lookup"><span data-stu-id="dc292-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="dc292-113">RazorYöntem parametreleri ve ortak özellikler içindeki denetleyicilere ve sayfalara verileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="dc292-113">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="dc292-114">Dize verilerini .NET türlerine dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="dc292-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="dc292-115">Karmaşık türlerin özelliklerini güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="dc292-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="dc292-116">Örnek</span><span class="sxs-lookup"><span data-stu-id="dc292-116">Example</span></span>

<span data-ttu-id="dc292-117">Aşağıdaki eylem yöntemine sahip olduğunuzu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="dc292-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="dc292-118">Ve uygulama şu URL ile bir istek alıyor:</span><span class="sxs-lookup"><span data-stu-id="dc292-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="dc292-119">Model bağlama, yönlendirme sistemi eylem yöntemini seçtikten sonra aşağıdaki adımlardan geçer:</span><span class="sxs-lookup"><span data-stu-id="dc292-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="dc292-120">Adlı bir tamsayı olan ilk parametresini bulur `GetByID` `id` .</span><span class="sxs-lookup"><span data-stu-id="dc292-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="dc292-121">HTTP isteğindeki kullanılabilir kaynakları arar ve `id` route verilerinde = "2" bulur.</span><span class="sxs-lookup"><span data-stu-id="dc292-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="dc292-122">"2" dizesini tamsayı 2 ' ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="dc292-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="dc292-123">`GetByID`Adlı bir Boole değeri olan bir sonraki parametresini bulur `dogsOnly` .</span><span class="sxs-lookup"><span data-stu-id="dc292-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="dc292-124">Kaynakları arar ve sorgu dizesinde "DogsOnly = true" bulur.</span><span class="sxs-lookup"><span data-stu-id="dc292-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="dc292-125">Ad eşleştirme, büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="dc292-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="dc292-126">"True" dizesini Boole değerine dönüştürür `true` .</span><span class="sxs-lookup"><span data-stu-id="dc292-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="dc292-127">Framework daha sonra yöntemi çağırır `GetById` , parametresi için 2 ' ye geçerek `id` ve `true` `dogsOnly` parametresi için.</span><span class="sxs-lookup"><span data-stu-id="dc292-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="dc292-128">Önceki örnekte, model bağlama hedefleri basit türler olan yöntem parametreleridir.</span><span class="sxs-lookup"><span data-stu-id="dc292-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="dc292-129">Hedefler, karmaşık bir türün özellikleri de olabilir.</span><span class="sxs-lookup"><span data-stu-id="dc292-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="dc292-130">Her bir özellik başarıyla bağlandıktan sonra, bu özellik için [model doğrulaması](xref:mvc/models/validation) oluşur.</span><span class="sxs-lookup"><span data-stu-id="dc292-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="dc292-131">Hangi verilerin modele bağladığına ve tüm bağlama veya doğrulama hatalarıyla ilgili kayıt, [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) veya [Pagemodel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState)içinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="dc292-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="dc292-132">Bu işlemin başarılı olup olmadığını öğrenmek için uygulama [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) bayrağını denetler.</span><span class="sxs-lookup"><span data-stu-id="dc292-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="dc292-133">Hedefler</span><span class="sxs-lookup"><span data-stu-id="dc292-133">Targets</span></span>

<span data-ttu-id="dc292-134">Model bağlama, aşağıdaki tür hedeflerin değerlerini bulmayı dener:</span><span class="sxs-lookup"><span data-stu-id="dc292-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="dc292-135">Bir isteğin yönlendirildiği denetleyici eylemi yönteminin parametreleri.</span><span class="sxs-lookup"><span data-stu-id="dc292-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="dc292-136">RazorBir isteğin yönlendirildiği sayfa işleyicisi yönteminin parametreleri.</span><span class="sxs-lookup"><span data-stu-id="dc292-136">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="dc292-137">Bir denetleyicinin veya `PageModel` sınıfın öznitelikleri tarafından belirtilmişse ortak özellikleri.</span><span class="sxs-lookup"><span data-stu-id="dc292-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="dc292-138">[BindProperty] özniteliği</span><span class="sxs-lookup"><span data-stu-id="dc292-138">[BindProperty] attribute</span></span>

<span data-ttu-id="dc292-139">, `PageModel` Model bağlamasının bu özelliği hedeflemesini sağlamak için bir denetleyicinin veya sınıfın ortak özelliğine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="dc292-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="dc292-140">[BindProperties] özniteliği</span><span class="sxs-lookup"><span data-stu-id="dc292-140">[BindProperties] attribute</span></span>

<span data-ttu-id="dc292-141">ASP.NET Core 2,1 ve üzeri sürümlerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="dc292-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="dc292-142">`PageModel`Model bağlamaya, sınıfın tüm ortak özelliklerini hedeflemesini bildirmek için bir denetleyiciye veya sınıfa uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="dc292-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="dc292-143">HTTP GET istekleri için model bağlama</span><span class="sxs-lookup"><span data-stu-id="dc292-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="dc292-144">Varsayılan olarak, Özellikler HTTP GET istekleri için bağlantılı değildir.</span><span class="sxs-lookup"><span data-stu-id="dc292-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="dc292-145">Genellikle, bir GET isteği için tüm ihtiyacınız olan bir kayıt KIMLIĞI parametresidir.</span><span class="sxs-lookup"><span data-stu-id="dc292-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="dc292-146">Kayıt KIMLIĞI, veritabanındaki öğeyi aramak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="dc292-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="dc292-147">Bu nedenle, modelin bir örneğini tutan bir özelliği bağlamaya gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="dc292-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="dc292-148">GET isteklerinden alınan özelliklerin verilerine bağlanmasını istediğiniz senaryolarda `SupportsGet` özelliği şu şekilde ayarlayın `true` :</span><span class="sxs-lookup"><span data-stu-id="dc292-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="dc292-149">Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="dc292-149">Sources</span></span>

<span data-ttu-id="dc292-150">Varsayılan olarak, model bağlama, bir HTTP isteğindeki aşağıdaki kaynaklardan gelen anahtar-değer çiftleri biçimindeki verileri alır:</span><span class="sxs-lookup"><span data-stu-id="dc292-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="dc292-151">Form alanları</span><span class="sxs-lookup"><span data-stu-id="dc292-151">Form fields</span></span>
1. <span data-ttu-id="dc292-152">İstek gövdesi ( [[ApiController] özniteliğine sahip denetleyiciler](xref:web-api/index#binding-source-parameter-inference)için.)</span><span class="sxs-lookup"><span data-stu-id="dc292-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="dc292-153">Veri yönlendirme</span><span class="sxs-lookup"><span data-stu-id="dc292-153">Route data</span></span>
1. <span data-ttu-id="dc292-154">Sorgu dizesi parametreleri</span><span class="sxs-lookup"><span data-stu-id="dc292-154">Query string parameters</span></span>
1. <span data-ttu-id="dc292-155">Karşıya yüklenen dosyalar</span><span class="sxs-lookup"><span data-stu-id="dc292-155">Uploaded files</span></span>

<span data-ttu-id="dc292-156">Her hedef parametresi veya özelliği için kaynaklar, önceki listede belirtilen sırada taranır.</span><span class="sxs-lookup"><span data-stu-id="dc292-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="dc292-157">Birkaç özel durum vardır:</span><span class="sxs-lookup"><span data-stu-id="dc292-157">There are a few exceptions:</span></span>

* <span data-ttu-id="dc292-158">Rota verileri ve sorgu dizesi değerleri yalnızca basit türler için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="dc292-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="dc292-159">Karşıya yüklenen dosyalar yalnızca veya uygulayan hedef türlere bağlanır `IFormFile` `IEnumerable<IFormFile>` .</span><span class="sxs-lookup"><span data-stu-id="dc292-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="dc292-160">Varsayılan kaynak doğru değilse, kaynağı belirtmek için aşağıdaki özniteliklerden birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="dc292-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="dc292-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)-Sorgu dizesinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="dc292-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="dc292-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)-Rota verilerinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="dc292-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="dc292-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)-Postalanan Form alanlarındaki değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="dc292-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="dc292-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)-İstek gövdesinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="dc292-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="dc292-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)-HTTP başlıklarındaki değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="dc292-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="dc292-166">Bu öznitelikler:</span><span class="sxs-lookup"><span data-stu-id="dc292-166">These attributes:</span></span>

* <span data-ttu-id="dc292-167">Model özelliklerine tek tek eklenir (model sınıfına değil), aşağıdaki örnekte olduğu gibi:</span><span class="sxs-lookup"><span data-stu-id="dc292-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="dc292-168">İsteğe bağlı olarak oluşturucuda bir model adı değeri kabul edin.</span><span class="sxs-lookup"><span data-stu-id="dc292-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="dc292-169">Bu seçenek, özellik adının istekteki değerle eşleşmemesi durumunda sağlanır.</span><span class="sxs-lookup"><span data-stu-id="dc292-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="dc292-170">Örneğin, istekteki değer aşağıdaki örnekte olduğu gibi adında bir tire olan bir üstbilgi olabilir:</span><span class="sxs-lookup"><span data-stu-id="dc292-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="dc292-171">[FromBody] özniteliği</span><span class="sxs-lookup"><span data-stu-id="dc292-171">[FromBody] attribute</span></span>

<span data-ttu-id="dc292-172">`[FromBody]`Özniteliği BIR http isteği gövdesinden doldurmak için bir parametreye özniteliğini uygulayın.</span><span class="sxs-lookup"><span data-stu-id="dc292-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="dc292-173">ASP.NET Core çalışma zamanı, gövdeyi bir giriş biçimlendirici 'ya okumaktan sorumlu olarak temsil eder.</span><span class="sxs-lookup"><span data-stu-id="dc292-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="dc292-174">Giriş biçimleri [Bu makalenin ilerleyen kısımlarında](#input-formatters)açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="dc292-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="dc292-175">`[FromBody]`Karmaşık bir tür parametresine uygulandığında, özelliklerine uygulanan herhangi bir bağlama kaynak özniteliği yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="dc292-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="dc292-176">Örneğin, aşağıdaki eylem, `Create` `pet` parametresinin gövdeden doldurulduğunu belirtir:</span><span class="sxs-lookup"><span data-stu-id="dc292-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="dc292-177">`Pet`Sınıfı, `Breed` özelliğinin bir sorgu dizesi parametresinden doldurulduğunu belirtir:</span><span class="sxs-lookup"><span data-stu-id="dc292-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="dc292-178">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="dc292-178">In the preceding example:</span></span>

* <span data-ttu-id="dc292-179">`[FromQuery]`Öznitelik yoksayıldı.</span><span class="sxs-lookup"><span data-stu-id="dc292-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="dc292-180">`Breed`Özellik bir sorgu dizesi parametresinden doldurulmamış.</span><span class="sxs-lookup"><span data-stu-id="dc292-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="dc292-181">Giriş biçimleri yalnızca gövdeyi okur ve bağlama kaynak özniteliklerini anlamayın.</span><span class="sxs-lookup"><span data-stu-id="dc292-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="dc292-182">Gövdede uygun bir değer bulunursa, bu değer özelliği doldurmak için kullanılır `Breed` .</span><span class="sxs-lookup"><span data-stu-id="dc292-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="dc292-183">`[FromBody]`Action yöntemi başına birden fazla parametreye uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="dc292-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="dc292-184">İstek akışı bir giriş biçimlendirici tarafından okunduktan sonra, diğer parametreleri bağlamak için artık bir daha okunamaz `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="dc292-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="dc292-185">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="dc292-185">Additional sources</span></span>

<span data-ttu-id="dc292-186">Kaynak verileri, model bağlama sistemine *değer sağlayıcılara*göre sağlanır.</span><span class="sxs-lookup"><span data-stu-id="dc292-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="dc292-187">Diğer kaynaklardan model bağlamaya yönelik verileri alan özel değer sağlayıcıları yazabilir ve kaydedebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="dc292-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="dc292-188">Örneğin, verilerin cookie veya oturum durumunun olmasını isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="dc292-188">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="dc292-189">Yeni bir kaynaktan veri almak için:</span><span class="sxs-lookup"><span data-stu-id="dc292-189">To get data from a new source:</span></span>

* <span data-ttu-id="dc292-190">Uygulayan bir sınıf oluşturun `IValueProvider` .</span><span class="sxs-lookup"><span data-stu-id="dc292-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="dc292-191">Uygulayan bir sınıf oluşturun `IValueProviderFactory` .</span><span class="sxs-lookup"><span data-stu-id="dc292-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="dc292-192">Fabrika sınıfını içine kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="dc292-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="dc292-193">Örnek uygulama, ' den değerler alan bir [değer sağlayıcısı](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) ve [Factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) örneği içerir cookie .</span><span class="sxs-lookup"><span data-stu-id="dc292-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="dc292-194">Kayıt kodu aşağıda verilmiştir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="dc292-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="dc292-195">Gösterilen kod, tüm yerleşik değer sağlayıcılarından sonra özel değer sağlayıcısını koyar.</span><span class="sxs-lookup"><span data-stu-id="dc292-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="dc292-196">Bunu listede ilk yapmak için `Insert(0, new CookieValueProviderFactory())` yerine çağırın `Add` .</span><span class="sxs-lookup"><span data-stu-id="dc292-196">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="dc292-197">Model özelliği için kaynak yok</span><span class="sxs-lookup"><span data-stu-id="dc292-197">No source for a model property</span></span>

<span data-ttu-id="dc292-198">Varsayılan olarak, model özelliği için bir değer bulunmazsa model durumu hatası oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="dc292-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="dc292-199">Özelliği null veya varsayılan bir değer olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="dc292-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="dc292-200">Null yapılabilir basit türler olarak ayarlanır `null` .</span><span class="sxs-lookup"><span data-stu-id="dc292-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="dc292-201">Null yapılamayan değer türleri olarak ayarlanır `default(T)` .</span><span class="sxs-lookup"><span data-stu-id="dc292-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="dc292-202">Örneğin, bir parametre `int id` 0 olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="dc292-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="dc292-203">Karmaşık türler için model bağlama, özellikleri ayarlamadan varsayılan oluşturucuyu kullanarak bir örnek oluşturur.</span><span class="sxs-lookup"><span data-stu-id="dc292-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="dc292-204">Diziler olarak ayarlanır `Array.Empty<T>()` , ancak `byte[]` diziler olarak ayarlanır `null` .</span><span class="sxs-lookup"><span data-stu-id="dc292-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="dc292-205">Model özelliği için form alanlarında hiçbir şey bulunamadığında model durumunun geçersiz kılınmalıdır, [`[BindRequired]`](#bindrequired-attribute) özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="dc292-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="dc292-206">Bu `[BindRequired]` davranışın, bir istek GÖVDESINDE JSON veya XML verilerine değil, postalanan form verilerinden model bağlama için geçerli olduğunu unutmayın.</span><span class="sxs-lookup"><span data-stu-id="dc292-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="dc292-207">İstek gövdesi verileri, [giriş formatlayıcıları](#input-formatters)tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="dc292-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="dc292-208">Tür dönüştürme hataları</span><span class="sxs-lookup"><span data-stu-id="dc292-208">Type conversion errors</span></span>

<span data-ttu-id="dc292-209">Bir kaynak bulunursa ancak hedef türe dönüştürülemiyorsa, model durumu geçersiz olarak işaretlenir.</span><span class="sxs-lookup"><span data-stu-id="dc292-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="dc292-210">Hedef parametresi veya özelliği, önceki bölümde belirtildiği gibi null veya varsayılan değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="dc292-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="dc292-211">Özniteliği olan bir API denetleyicisinde `[ApiController]` , geçersiz model durumu OTOMATIK HTTP 400 yanıtına neden olur.</span><span class="sxs-lookup"><span data-stu-id="dc292-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="dc292-212">Bir Razor sayfada, sayfayı bir hata iletisiyle yeniden görüntüleyin:</span><span class="sxs-lookup"><span data-stu-id="dc292-212">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="dc292-213">İstemci tarafı doğrulama, aksi takdirde bir sayfalar formuna gönderilemeyen hatalı verileri yakalar Razor .</span><span class="sxs-lookup"><span data-stu-id="dc292-213">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="dc292-214">Bu doğrulama, önceki vurgulanmış kodu tetiklemeyi zorlaştırır.</span><span class="sxs-lookup"><span data-stu-id="dc292-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="dc292-215">Örnek uygulama, teslim **tarihi** alanına hatalı veri yerleştiren ve formu Gönderen **Geçersiz tarih içeren bir Gönder** düğmesi içerir.</span><span class="sxs-lookup"><span data-stu-id="dc292-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="dc292-216">Bu düğme, veri dönüştürme hataları oluştuğunda sayfanın yeniden görüntülenmesine yönelik kodun nasıl çalıştığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="dc292-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="dc292-217">Sayfa önceki kodla yeniden görüntülendiğinde, form alanında geçersiz giriş gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="dc292-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="dc292-218">Bunun nedeni model özelliğinin null ya da varsayılan bir değer olarak ayarlanmış olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="dc292-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="dc292-219">Geçersiz giriş bir hata iletisinde görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="dc292-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="dc292-220">Ancak form alanındaki hatalı verileri yeniden görüntülemek istiyorsanız, model özelliğini bir dize haline getirmeyi ve veri dönüştürmeyi el ile gerçekleştirmeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="dc292-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="dc292-221">Tür dönüştürme hatalarının model durumu hatalarına neden olmasını istemiyorsanız aynı strateji önerilir.</span><span class="sxs-lookup"><span data-stu-id="dc292-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="dc292-222">Bu durumda model özelliğini bir dize yapın.</span><span class="sxs-lookup"><span data-stu-id="dc292-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="dc292-223">Basit türler</span><span class="sxs-lookup"><span data-stu-id="dc292-223">Simple types</span></span>

<span data-ttu-id="dc292-224">Model cildin kaynak dizeleri dönüştürebileceğiniz basit türler aşağıdakileri içerir:</span><span class="sxs-lookup"><span data-stu-id="dc292-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="dc292-225">Boole</span><span class="sxs-lookup"><span data-stu-id="dc292-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="dc292-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="dc292-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="dc292-227">Char</span><span class="sxs-lookup"><span data-stu-id="dc292-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="dc292-228">Tarih Saat</span><span class="sxs-lookup"><span data-stu-id="dc292-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="dc292-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="dc292-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="dc292-230">Kategori</span><span class="sxs-lookup"><span data-stu-id="dc292-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="dc292-231">Çift</span><span class="sxs-lookup"><span data-stu-id="dc292-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="dc292-232">Sabit listesi</span><span class="sxs-lookup"><span data-stu-id="dc292-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="dc292-233">'İni</span><span class="sxs-lookup"><span data-stu-id="dc292-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="dc292-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="dc292-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="dc292-235">Tek</span><span class="sxs-lookup"><span data-stu-id="dc292-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="dc292-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="dc292-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="dc292-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="dc292-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="dc292-238">Kullanılmamışsa</span><span class="sxs-lookup"><span data-stu-id="dc292-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="dc292-239">Sürüm</span><span class="sxs-lookup"><span data-stu-id="dc292-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="dc292-240">Karmaşık türler</span><span class="sxs-lookup"><span data-stu-id="dc292-240">Complex types</span></span>

<span data-ttu-id="dc292-241">Karmaşık bir türün bağlanması için ortak bir varsayılan Oluşturucusu ve ortak yazılabilir özellikleri olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="dc292-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="dc292-242">Model bağlama gerçekleştiğinde, sınıf ortak varsayılan Oluşturucu kullanılarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="dc292-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="dc292-243">Karmaşık türün her özelliği için model bağlama, ad modeli ön eki için kaynakları arar *. property_name*.</span><span class="sxs-lookup"><span data-stu-id="dc292-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="dc292-244">Hiçbir şey bulunamazsa, ön ek olmadan yalnızca *property_name* arar.</span><span class="sxs-lookup"><span data-stu-id="dc292-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="dc292-245">Bir parametreye bağlama için, önek parametre adıdır.</span><span class="sxs-lookup"><span data-stu-id="dc292-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="dc292-246">Ortak özelliğe bağlama için `PageModel` , önek ortak özellik adıdır.</span><span class="sxs-lookup"><span data-stu-id="dc292-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="dc292-247">Bazı özniteliklerin `Prefix` , parametre veya özellik adının varsayılan kullanımını geçersiz kılabilmenizi sağlayan bir özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="dc292-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="dc292-248">Örneğin, karmaşık türün aşağıdaki sınıf olduğunu varsayalım `Instructor` :</span><span class="sxs-lookup"><span data-stu-id="dc292-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="dc292-249">Önek = parametre adı</span><span class="sxs-lookup"><span data-stu-id="dc292-249">Prefix = parameter name</span></span>

<span data-ttu-id="dc292-250">Bağlanacak model adlı bir parametre ise `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="dc292-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="dc292-251">Model bağlama, anahtar kaynaklarına bakarak başlar `instructorToUpdate.ID` .</span><span class="sxs-lookup"><span data-stu-id="dc292-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="dc292-252">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="dc292-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="dc292-253">Önek = Özellik adı</span><span class="sxs-lookup"><span data-stu-id="dc292-253">Prefix = property name</span></span>

<span data-ttu-id="dc292-254">Bağlanacak model, `Instructor` denetleyicinin veya sınıfın adında bir özelliktir `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="dc292-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="dc292-255">Model bağlama, anahtar kaynaklarına bakarak başlar `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="dc292-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="dc292-256">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="dc292-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="dc292-257">Özel ön ek</span><span class="sxs-lookup"><span data-stu-id="dc292-257">Custom prefix</span></span>

<span data-ttu-id="dc292-258">Bağlanacak model adlı bir parametre ise `instructorToUpdate` ve bir `Bind` öznitelik `Instructor` önek olarak belirtirse:</span><span class="sxs-lookup"><span data-stu-id="dc292-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="dc292-259">Model bağlama, anahtar kaynaklarına bakarak başlar `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="dc292-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="dc292-260">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="dc292-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="dc292-261">Karmaşık tür hedefleri için öznitelikler</span><span class="sxs-lookup"><span data-stu-id="dc292-261">Attributes for complex type targets</span></span>

<span data-ttu-id="dc292-262">Karmaşık türlerin model bağlamasını denetlemek için birkaç yerleşik öznitelik mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="dc292-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="dc292-263">Bu öznitelikler, gönderilen form verileri değer kaynağı olduğunda model bağlamayı etkiler.</span><span class="sxs-lookup"><span data-stu-id="dc292-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="dc292-264">Bunlar, gönderilen JSON ve XML istek gövdelerini işleyen giriş formatlayıcıları 'nı etkilemez.</span><span class="sxs-lookup"><span data-stu-id="dc292-264">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="dc292-265">Giriş biçimleri [Bu makalenin ilerleyen kısımlarında](#input-formatters)açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="dc292-265">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="dc292-266">Ayrıca bkz `[Required]` . [model doğrulama](xref:mvc/models/validation#required-attribute)içindeki öznitelik tartışması.</span><span class="sxs-lookup"><span data-stu-id="dc292-266">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="dc292-267">[BindRequired] özniteliği</span><span class="sxs-lookup"><span data-stu-id="dc292-267">[BindRequired] attribute</span></span>

<span data-ttu-id="dc292-268">, Yöntem parametrelerine değil yalnızca model özelliklerine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="dc292-268">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="dc292-269">Modelin özelliği için bağlama gerçekleşmemişse model bağlamasının model durumu hatası eklemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="dc292-269">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="dc292-270">Aşağıda bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="dc292-270">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="dc292-271">[Bindhiç] özniteliği</span><span class="sxs-lookup"><span data-stu-id="dc292-271">[BindNever] attribute</span></span>

<span data-ttu-id="dc292-272">, Yöntem parametrelerine değil yalnızca model özelliklerine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="dc292-272">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="dc292-273">Model bağlamasının model özelliğini değiştirmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="dc292-273">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="dc292-274">Aşağıda bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="dc292-274">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="dc292-275">[Bind] özniteliği</span><span class="sxs-lookup"><span data-stu-id="dc292-275">[Bind] attribute</span></span>

<span data-ttu-id="dc292-276">, Bir sınıfa veya yöntem parametresine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="dc292-276">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="dc292-277">Model bağlamasındaki bir modelin hangi özelliklerinin dahil edileceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="dc292-277">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="dc292-278">Aşağıdaki örnekte, `Instructor` herhangi bir işleyici veya eylem yöntemi çağrıldığında yalnızca modelin belirtilen özellikleri bağlanır:</span><span class="sxs-lookup"><span data-stu-id="dc292-278">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="dc292-279">Aşağıdaki örnekte, `Instructor` yöntemi çağrıldığında yalnızca modelin belirtilen özellikleri bağlanır `OnPost` :</span><span class="sxs-lookup"><span data-stu-id="dc292-279">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="dc292-280">`[Bind]`Özniteliği, *oluşturma* senaryolarında fazla nakline karşı korumak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="dc292-280">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="dc292-281">Dışlanan Özellikler null ya da boş değer olarak ayarlandığı için, düzenleme senaryolarında iyi çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="dc292-281">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="dc292-282">Fazla naklin savunma için, öznitelik yerine görüntüleme modelleri önerilir `[Bind]` .</span><span class="sxs-lookup"><span data-stu-id="dc292-282">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="dc292-283">Daha fazla bilgi için bkz. fazla [nakil hakkında güvenlik NOI](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="dc292-283">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="dc292-284">Koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="dc292-284">Collections</span></span>

<span data-ttu-id="dc292-285">Basit türlerin koleksiyonları olan hedefler için model bağlama, *parameter_name* veya *property_name*ile eşleşmeleri arar.</span><span class="sxs-lookup"><span data-stu-id="dc292-285">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="dc292-286">Eşleşme bulunmazsa, ön ek olmadan desteklenen biçimlerden birini arar.</span><span class="sxs-lookup"><span data-stu-id="dc292-286">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="dc292-287">Örnek:</span><span class="sxs-lookup"><span data-stu-id="dc292-287">For example:</span></span>

* <span data-ttu-id="dc292-288">Bağlanacak parametrenin adlı bir dizi olduğunu varsayalım `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="dc292-288">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="dc292-289">Form veya sorgu dizesi verileri aşağıdaki biçimlerden birinde olabilir:</span><span class="sxs-lookup"><span data-stu-id="dc292-289">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="dc292-290">Aşağıdaki biçim yalnızca form verilerinde desteklenir:</span><span class="sxs-lookup"><span data-stu-id="dc292-290">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="dc292-291">Önceki örnek biçimlerinin hepsi için model bağlama iki öğe dizisini `selectedCourses` parametreye geçirir:</span><span class="sxs-lookup"><span data-stu-id="dc292-291">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="dc292-292">Selectedkurslar [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="dc292-292">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="dc292-293">Selectedkurslar [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="dc292-293">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="dc292-294">Alt simge numaralarını kullanan veri biçimleri (... [0]... [1]...) sıfırdan başlayarak sıralı olarak numaralandırıldıklarından emin olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="dc292-294">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="dc292-295">Alt simge numaralandırmasında boşluk varsa, boşluklardan sonraki tüm öğeler yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="dc292-295">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="dc292-296">Örneğin, alt simgeler 0 ve 1 yerine 0 ve 2 ise ikinci öğe yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="dc292-296">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="dc292-297">Sözlükler</span><span class="sxs-lookup"><span data-stu-id="dc292-297">Dictionaries</span></span>

<span data-ttu-id="dc292-298">`Dictionary`Hedefler için, model bağlama *parameter_name* veya *property_name*eşleşme arar.</span><span class="sxs-lookup"><span data-stu-id="dc292-298">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="dc292-299">Eşleşme bulunmazsa, ön ek olmadan desteklenen biçimlerden birini arar.</span><span class="sxs-lookup"><span data-stu-id="dc292-299">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="dc292-300">Örnek:</span><span class="sxs-lookup"><span data-stu-id="dc292-300">For example:</span></span>

* <span data-ttu-id="dc292-301">Hedef parametrenin bir adlandırılmış olduğunu varsayalım `Dictionary<int, string>` `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="dc292-301">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="dc292-302">Postalanan form veya sorgu dizesi verileri aşağıdaki örneklerden birine benzeyebilir:</span><span class="sxs-lookup"><span data-stu-id="dc292-302">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="dc292-303">Önceki örnek biçimlerinin hepsi için model bağlama iki öğenin bir sözlüğünü `selectedCourses` parametreye geçirir:</span><span class="sxs-lookup"><span data-stu-id="dc292-303">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="dc292-304">Selectedkurslar ["1050"] = "Chemistry"</span><span class="sxs-lookup"><span data-stu-id="dc292-304">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="dc292-305">Selectedkurslar ["2000"] = "Ekonomiks"</span><span class="sxs-lookup"><span data-stu-id="dc292-305">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="dc292-306">Model bağlama yolu verileri ve sorgu dizeleri için Genelleştirme davranışı</span><span class="sxs-lookup"><span data-stu-id="dc292-306">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="dc292-307">ASP.NET Core yol değeri sağlayıcısı ve sorgu dizesi değer sağlayıcısı:</span><span class="sxs-lookup"><span data-stu-id="dc292-307">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="dc292-308">Değerleri sabit kültür olarak değerlendirin.</span><span class="sxs-lookup"><span data-stu-id="dc292-308">Treat values as invariant culture.</span></span>
* <span data-ttu-id="dc292-309">URL 'Lerin kültür sabiti olmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="dc292-309">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="dc292-310">Buna karşılık, form verilerinden gelen değerler kültüre duyarlı bir dönüştürmeye gider.</span><span class="sxs-lookup"><span data-stu-id="dc292-310">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="dc292-311">Bu, URL 'Lerin yerel ayarlarda paylaşılabilir olması için tasarımdır.</span><span class="sxs-lookup"><span data-stu-id="dc292-311">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="dc292-312">ASP.NET Core yol değeri sağlayıcısını ve sorgu dizesi değeri sağlayıcısını, kültüre duyarlı bir dönüşüme dönüştürmek için:</span><span class="sxs-lookup"><span data-stu-id="dc292-312">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="dc292-313">Şuradan devralma<xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="dc292-313">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="dc292-314">[QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) veya [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs) adresinden kodu kopyalayın</span><span class="sxs-lookup"><span data-stu-id="dc292-314">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="dc292-315">Değer sağlayıcısı oluşturucusuna geçirilen [kültür değerini](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture) ile değiştirin</span><span class="sxs-lookup"><span data-stu-id="dc292-315">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="dc292-316">MVC seçeneklerinde varsayılan değer sağlayıcı fabrikasını yeni bir değerle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="dc292-316">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="dc292-317">Özel veri türleri</span><span class="sxs-lookup"><span data-stu-id="dc292-317">Special data types</span></span>

<span data-ttu-id="dc292-318">Model bağlamanın işleyebileceği bazı özel veri türleri vardır.</span><span class="sxs-lookup"><span data-stu-id="dc292-318">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="dc292-319">Iformfile ve ıformfilecollection</span><span class="sxs-lookup"><span data-stu-id="dc292-319">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="dc292-320">HTTP isteğine eklenen karşıya yüklenen dosya.</span><span class="sxs-lookup"><span data-stu-id="dc292-320">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="dc292-321">Ayrıca, `IEnumerable<IFormFile>` birden çok dosya için de desteklenir.</span><span class="sxs-lookup"><span data-stu-id="dc292-321">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="dc292-322">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="dc292-322">CancellationToken</span></span>

<span data-ttu-id="dc292-323">Zaman uyumsuz denetleyicilerde etkinliği iptal etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="dc292-323">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="dc292-324">Form koleksiyonu</span><span class="sxs-lookup"><span data-stu-id="dc292-324">FormCollection</span></span>

<span data-ttu-id="dc292-325">Postalanan form verilerinden tüm değerleri almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="dc292-325">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="dc292-326">Giriş biçimleri</span><span class="sxs-lookup"><span data-stu-id="dc292-326">Input formatters</span></span>

<span data-ttu-id="dc292-327">İstek gövdesindeki veriler JSON, XML veya başka bir biçimde olabilir.</span><span class="sxs-lookup"><span data-stu-id="dc292-327">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="dc292-328">Model bağlama, bu verileri ayrıştırmak için belirli bir içerik türünü işlemek üzere yapılandırılmış bir *giriş biçimlendiricisi* kullanır.</span><span class="sxs-lookup"><span data-stu-id="dc292-328">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="dc292-329">Varsayılan olarak, ASP.NET Core JSON verilerini işlemek için JSON tabanlı giriş formatlayıcıları içerir.</span><span class="sxs-lookup"><span data-stu-id="dc292-329">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="dc292-330">Diğer içerik türleri için başka biçim ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="dc292-330">You can add other formatters for other content types.</span></span>

<span data-ttu-id="dc292-331">ASP.NET Core, [tüketen](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) özniteliğine bağlı olarak giriş formatlayıcıları seçer.</span><span class="sxs-lookup"><span data-stu-id="dc292-331">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="dc292-332">Hiçbir öznitelik yoksa, [Content-Type üst bilgisini](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)kullanır.</span><span class="sxs-lookup"><span data-stu-id="dc292-332">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="dc292-333">Yerleşik XML girişi formatlayıcıları 'nı kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="dc292-333">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="dc292-334">`Microsoft.AspNetCore.Mvc.Formatters.Xml`NuGet paketini yükler.</span><span class="sxs-lookup"><span data-stu-id="dc292-334">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="dc292-335">İçinde `Startup.ConfigureServices` , veya öğesini çağırın <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> .</span><span class="sxs-lookup"><span data-stu-id="dc292-335">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="dc292-336">Özniteliği, `Consumes` istek GÖVDESINDE XML beklemeniz gereken denetleyici sınıflarına veya eylem yöntemlerine uygulayın.</span><span class="sxs-lookup"><span data-stu-id="dc292-336">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="dc292-337">Daha fazla bilgi için bkz. [XML serileştirme tanıtımı](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="dc292-337">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="dc292-338">Giriş formatlayıcıları ile model bağlamayı özelleştirme</span><span class="sxs-lookup"><span data-stu-id="dc292-338">Customize model binding with input formatters</span></span>

<span data-ttu-id="dc292-339">Giriş biçimlendiricisi, istek gövdesinden veri okumak için tam sorumluluğa sahiptir.</span><span class="sxs-lookup"><span data-stu-id="dc292-339">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="dc292-340">Bu işlemi özelleştirmek için, giriş biçimlendirici tarafından kullanılan API 'Leri yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="dc292-340">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="dc292-341">Bu bölümde, `System.Text.Json` adlı özel bir türü anlamak için tabanlı giriş biçimlendiricinin nasıl özelleştirileceği açıklanmaktadır `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="dc292-341">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="dc292-342">Adlı özel bir özellik içeren aşağıdaki modeli göz önünde bulundurun `ObjectId` `Id` :</span><span class="sxs-lookup"><span data-stu-id="dc292-342">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="dc292-343">Kullanırken model bağlama işlemini özelleştirmek için `System.Text.Json` , öğesinden türetilmiş bir sınıf oluşturun <xref:System.Text.Json.Serialization.JsonConverter%601> :</span><span class="sxs-lookup"><span data-stu-id="dc292-343">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="dc292-344">Özel bir dönüştürücü kullanmak için, <xref:System.Text.Json.Serialization.JsonConverterAttribute> türüne özniteliğini uygulayın.</span><span class="sxs-lookup"><span data-stu-id="dc292-344">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="dc292-345">Aşağıdaki örnekte, `ObjectId` türü `ObjectIdConverter` özel dönüştürücü olarak ile yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="dc292-345">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="dc292-346">Daha fazla bilgi için bkz. [özel dönüştürücüler yazma](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="dc292-346">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="dc292-347">Belirtilen türleri model bağlamalarından Dışla</span><span class="sxs-lookup"><span data-stu-id="dc292-347">Exclude specified types from model binding</span></span>

<span data-ttu-id="dc292-348">Model bağlama ve doğrulama sistemlerinin davranışı [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata)tarafından yönlendiriliyor.</span><span class="sxs-lookup"><span data-stu-id="dc292-348">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="dc292-349">`ModelMetadata` [Mvcoptions. modelmetadatadetails sağlayıcılarına](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders)bir ayrıntı sağlayıcısı ekleyerek özelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="dc292-349">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="dc292-350">Yerleşik Ayrıntılar sağlayıcıları, belirtilen türler için model bağlamayı veya doğrulamayı devre dışı bırakmak üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="dc292-350">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="dc292-351">Belirtilen türdeki tüm modellerdeki model bağlamayı devre dışı bırakmak için içine bir ekleyin <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="dc292-351">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="dc292-352">Örneğin, türü tüm modeller üzerinde model bağlamayı devre dışı bırakmak için `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="dc292-352">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="dc292-353">Belirtilen türdeki özelliklerde doğrulamayı devre dışı bırakmak için içine bir ekleyin <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="dc292-353">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="dc292-354">Örneğin, türündeki özelliklerde doğrulamayı devre dışı bırakmak için `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="dc292-354">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="dc292-355">Özel model ciltleri</span><span class="sxs-lookup"><span data-stu-id="dc292-355">Custom model binders</span></span>

<span data-ttu-id="dc292-356">Özel bir model cildi yazarak ve `[ModelBinder]` belirli bir hedef için seçmek üzere özniteliğini kullanarak model bağlamayı genişletebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="dc292-356">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="dc292-357">[Özel model bağlama](xref:mvc/advanced/custom-model-binding)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="dc292-357">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="dc292-358">El ile model bağlama</span><span class="sxs-lookup"><span data-stu-id="dc292-358">Manual model binding</span></span> 

<span data-ttu-id="dc292-359">Model bağlama yöntemi kullanılarak el ile çağrılabilir <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> .</span><span class="sxs-lookup"><span data-stu-id="dc292-359">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="dc292-360">Yöntemi hem hem de sınıflarında tanımlanmıştır `ControllerBase` `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="dc292-360">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="dc292-361">Yöntem aşırı yüklemeleri, kullanılacak öneki ve değer sağlayıcısını belirtmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="dc292-361">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="dc292-362">Yöntemi `false` model bağlamanın başarısız olup olmadığını döndürür.</span><span class="sxs-lookup"><span data-stu-id="dc292-362">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="dc292-363">Aşağıda bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="dc292-363">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="dc292-364"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>, form gövdesinden, sorgu dizesinden ve veri yönlendirme verilerinden veri almak için değer sağlayıcılarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="dc292-364"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="dc292-365">`TryUpdateModelAsync`genellikle:</span><span class="sxs-lookup"><span data-stu-id="dc292-365">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="dc292-366">Daha Razor fazla nakletmeyi engellemek için denetleyiciler ve görünümler kullanan sayfalarla ve MVC uygulamalarıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="dc292-366">Used with Razor Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="dc292-367">Form verilerinden, sorgu dizelerinden ve veri rotalarından tüketilmediği müddetçe bir Web API 'SI ile kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="dc292-367">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="dc292-368">JSON kullanan Web API uç noktaları, istek gövdesini bir nesne olarak seri durumdan çıkarmak için [giriş formatlarını](#input-formatters) kullanır.</span><span class="sxs-lookup"><span data-stu-id="dc292-368">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="dc292-369">Daha fazla bilgi için bkz. [Tryupdatemodelasync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="dc292-369">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="dc292-370">[FromServices] özniteliği</span><span class="sxs-lookup"><span data-stu-id="dc292-370">[FromServices] attribute</span></span>

<span data-ttu-id="dc292-371">Bu özniteliğin adı, bir veri kaynağı belirten model bağlama özniteliklerinin düzeniyle uyar.</span><span class="sxs-lookup"><span data-stu-id="dc292-371">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="dc292-372">Ancak bir değer sağlayıcısından veri bağlama hakkında bilgi yoktur.</span><span class="sxs-lookup"><span data-stu-id="dc292-372">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="dc292-373">[Bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısından bir türün bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="dc292-373">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="dc292-374">Amacı, yalnızca belirli bir yöntem çağrılırsa bir hizmete ihtiyacınız olduğunda Oluşturucu ekleme için bir alternatif sağlamaktır.</span><span class="sxs-lookup"><span data-stu-id="dc292-374">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dc292-375">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="dc292-375">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="dc292-376">Bu makalede, model bağlamanın ne olduğu, nasıl çalıştığı ve davranışını nasıl özelleştireceğiniz açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="dc292-376">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="dc292-377">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="dc292-377">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="dc292-378">Model bağlama nedir?</span><span class="sxs-lookup"><span data-stu-id="dc292-378">What is Model binding</span></span>

<span data-ttu-id="dc292-379">Denetleyiciler ve Razor Sayfalar, http isteklerinden gelen verilerle çalışır.</span><span class="sxs-lookup"><span data-stu-id="dc292-379">Controllers and Razor pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="dc292-380">Örneğin, rota verileri bir kayıt anahtarı sağlayabilir ve postalanan form alanları, modelin özelliklerine ilişkin değerler sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="dc292-380">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="dc292-381">Bu değerlerin her birini almak ve bunları dizelerden .NET türlerine dönüştürmek için kod yazma sıkıcı ve hata durumunda olabilir.</span><span class="sxs-lookup"><span data-stu-id="dc292-381">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="dc292-382">Model bağlama bu işlemi otomatikleştirir.</span><span class="sxs-lookup"><span data-stu-id="dc292-382">Model binding automates this process.</span></span> <span data-ttu-id="dc292-383">Model bağlama sistemi:</span><span class="sxs-lookup"><span data-stu-id="dc292-383">The model binding system:</span></span>

* <span data-ttu-id="dc292-384">Veri yolu, form alanları ve sorgu dizeleri gibi çeşitli kaynaklardan veri alır.</span><span class="sxs-lookup"><span data-stu-id="dc292-384">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="dc292-385">RazorYöntem parametreleri ve ortak özellikler içindeki denetleyicilere ve sayfalara verileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="dc292-385">Provides the data to controllers and Razor pages in method parameters and public properties.</span></span>
* <span data-ttu-id="dc292-386">Dize verilerini .NET türlerine dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="dc292-386">Converts string data to .NET types.</span></span>
* <span data-ttu-id="dc292-387">Karmaşık türlerin özelliklerini güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="dc292-387">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="dc292-388">Örnek</span><span class="sxs-lookup"><span data-stu-id="dc292-388">Example</span></span>

<span data-ttu-id="dc292-389">Aşağıdaki eylem yöntemine sahip olduğunuzu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="dc292-389">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="dc292-390">Ve uygulama şu URL ile bir istek alıyor:</span><span class="sxs-lookup"><span data-stu-id="dc292-390">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="dc292-391">Model bağlama, yönlendirme sistemi eylem yöntemini seçtikten sonra aşağıdaki adımlardan geçer:</span><span class="sxs-lookup"><span data-stu-id="dc292-391">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="dc292-392">Adlı bir tamsayı olan ilk parametresini bulur `GetByID` `id` .</span><span class="sxs-lookup"><span data-stu-id="dc292-392">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="dc292-393">HTTP isteğindeki kullanılabilir kaynakları arar ve `id` route verilerinde = "2" bulur.</span><span class="sxs-lookup"><span data-stu-id="dc292-393">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="dc292-394">"2" dizesini tamsayı 2 ' ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="dc292-394">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="dc292-395">`GetByID`Adlı bir Boole değeri olan bir sonraki parametresini bulur `dogsOnly` .</span><span class="sxs-lookup"><span data-stu-id="dc292-395">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="dc292-396">Kaynakları arar ve sorgu dizesinde "DogsOnly = true" bulur.</span><span class="sxs-lookup"><span data-stu-id="dc292-396">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="dc292-397">Ad eşleştirme, büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="dc292-397">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="dc292-398">"True" dizesini Boole değerine dönüştürür `true` .</span><span class="sxs-lookup"><span data-stu-id="dc292-398">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="dc292-399">Framework daha sonra yöntemi çağırır `GetById` , parametresi için 2 ' ye geçerek `id` ve `true` `dogsOnly` parametresi için.</span><span class="sxs-lookup"><span data-stu-id="dc292-399">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="dc292-400">Önceki örnekte, model bağlama hedefleri basit türler olan yöntem parametreleridir.</span><span class="sxs-lookup"><span data-stu-id="dc292-400">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="dc292-401">Hedefler, karmaşık bir türün özellikleri de olabilir.</span><span class="sxs-lookup"><span data-stu-id="dc292-401">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="dc292-402">Her bir özellik başarıyla bağlandıktan sonra, bu özellik için [model doğrulaması](xref:mvc/models/validation) oluşur.</span><span class="sxs-lookup"><span data-stu-id="dc292-402">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="dc292-403">Hangi verilerin modele bağladığına ve tüm bağlama veya doğrulama hatalarıyla ilgili kayıt, [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) veya [Pagemodel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState)içinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="dc292-403">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="dc292-404">Bu işlemin başarılı olup olmadığını öğrenmek için uygulama [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) bayrağını denetler.</span><span class="sxs-lookup"><span data-stu-id="dc292-404">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="dc292-405">Hedefler</span><span class="sxs-lookup"><span data-stu-id="dc292-405">Targets</span></span>

<span data-ttu-id="dc292-406">Model bağlama, aşağıdaki tür hedeflerin değerlerini bulmayı dener:</span><span class="sxs-lookup"><span data-stu-id="dc292-406">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="dc292-407">Bir isteğin yönlendirildiği denetleyici eylemi yönteminin parametreleri.</span><span class="sxs-lookup"><span data-stu-id="dc292-407">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="dc292-408">RazorBir isteğin yönlendirildiği sayfa işleyicisi yönteminin parametreleri.</span><span class="sxs-lookup"><span data-stu-id="dc292-408">Parameters of the Razor Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="dc292-409">Bir denetleyicinin veya `PageModel` sınıfın öznitelikleri tarafından belirtilmişse ortak özellikleri.</span><span class="sxs-lookup"><span data-stu-id="dc292-409">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="dc292-410">[BindProperty] özniteliği</span><span class="sxs-lookup"><span data-stu-id="dc292-410">[BindProperty] attribute</span></span>

<span data-ttu-id="dc292-411">, `PageModel` Model bağlamasının bu özelliği hedeflemesini sağlamak için bir denetleyicinin veya sınıfın ortak özelliğine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="dc292-411">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindpropertiesattribute"></a><span data-ttu-id="dc292-412">[BindProperties] özniteliği</span><span class="sxs-lookup"><span data-stu-id="dc292-412">[BindProperties] attribute</span></span>

<span data-ttu-id="dc292-413">ASP.NET Core 2,1 ve üzeri sürümlerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="dc292-413">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="dc292-414">`PageModel`Model bağlamaya, sınıfın tüm ortak özelliklerini hedeflemesini bildirmek için bir denetleyiciye veya sınıfa uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="dc292-414">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="dc292-415">HTTP GET istekleri için model bağlama</span><span class="sxs-lookup"><span data-stu-id="dc292-415">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="dc292-416">Varsayılan olarak, Özellikler HTTP GET istekleri için bağlantılı değildir.</span><span class="sxs-lookup"><span data-stu-id="dc292-416">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="dc292-417">Genellikle, bir GET isteği için tüm ihtiyacınız olan bir kayıt KIMLIĞI parametresidir.</span><span class="sxs-lookup"><span data-stu-id="dc292-417">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="dc292-418">Kayıt KIMLIĞI, veritabanındaki öğeyi aramak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="dc292-418">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="dc292-419">Bu nedenle, modelin bir örneğini tutan bir özelliği bağlamaya gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="dc292-419">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="dc292-420">GET isteklerinden alınan özelliklerin verilerine bağlanmasını istediğiniz senaryolarda `SupportsGet` özelliği şu şekilde ayarlayın `true` :</span><span class="sxs-lookup"><span data-stu-id="dc292-420">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="dc292-421">Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="dc292-421">Sources</span></span>

<span data-ttu-id="dc292-422">Varsayılan olarak, model bağlama, bir HTTP isteğindeki aşağıdaki kaynaklardan gelen anahtar-değer çiftleri biçimindeki verileri alır:</span><span class="sxs-lookup"><span data-stu-id="dc292-422">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="dc292-423">Form alanları</span><span class="sxs-lookup"><span data-stu-id="dc292-423">Form fields</span></span>
1. <span data-ttu-id="dc292-424">İstek gövdesi ( [[ApiController] özniteliğine sahip denetleyiciler](xref:web-api/index#binding-source-parameter-inference)için.)</span><span class="sxs-lookup"><span data-stu-id="dc292-424">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="dc292-425">Veri yönlendirme</span><span class="sxs-lookup"><span data-stu-id="dc292-425">Route data</span></span>
1. <span data-ttu-id="dc292-426">Sorgu dizesi parametreleri</span><span class="sxs-lookup"><span data-stu-id="dc292-426">Query string parameters</span></span>
1. <span data-ttu-id="dc292-427">Karşıya yüklenen dosyalar</span><span class="sxs-lookup"><span data-stu-id="dc292-427">Uploaded files</span></span>

<span data-ttu-id="dc292-428">Her hedef parametresi veya özelliği için kaynaklar, önceki listede belirtilen sırada taranır.</span><span class="sxs-lookup"><span data-stu-id="dc292-428">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="dc292-429">Birkaç özel durum vardır:</span><span class="sxs-lookup"><span data-stu-id="dc292-429">There are a few exceptions:</span></span>

* <span data-ttu-id="dc292-430">Rota verileri ve sorgu dizesi değerleri yalnızca basit türler için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="dc292-430">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="dc292-431">Karşıya yüklenen dosyalar yalnızca veya uygulayan hedef türlere bağlanır `IFormFile` `IEnumerable<IFormFile>` .</span><span class="sxs-lookup"><span data-stu-id="dc292-431">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="dc292-432">Varsayılan kaynak doğru değilse, kaynağı belirtmek için aşağıdaki özniteliklerden birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="dc292-432">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="dc292-433">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute)-Sorgu dizesinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="dc292-433">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="dc292-434">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute)-Rota verilerinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="dc292-434">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="dc292-435">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute)-Postalanan Form alanlarındaki değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="dc292-435">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="dc292-436">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute)-İstek gövdesinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="dc292-436">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="dc292-437">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute)-HTTP başlıklarındaki değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="dc292-437">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="dc292-438">Bu öznitelikler:</span><span class="sxs-lookup"><span data-stu-id="dc292-438">These attributes:</span></span>

* <span data-ttu-id="dc292-439">Model özelliklerine tek tek eklenir (model sınıfına değil), aşağıdaki örnekte olduğu gibi:</span><span class="sxs-lookup"><span data-stu-id="dc292-439">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="dc292-440">İsteğe bağlı olarak oluşturucuda bir model adı değeri kabul edin.</span><span class="sxs-lookup"><span data-stu-id="dc292-440">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="dc292-441">Bu seçenek, özellik adının istekteki değerle eşleşmemesi durumunda sağlanır.</span><span class="sxs-lookup"><span data-stu-id="dc292-441">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="dc292-442">Örneğin, istekteki değer aşağıdaki örnekte olduğu gibi adında bir tire olan bir üstbilgi olabilir:</span><span class="sxs-lookup"><span data-stu-id="dc292-442">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="dc292-443">[FromBody] özniteliği</span><span class="sxs-lookup"><span data-stu-id="dc292-443">[FromBody] attribute</span></span>

<span data-ttu-id="dc292-444">`[FromBody]`Özniteliği BIR http isteği gövdesinden doldurmak için bir parametreye özniteliğini uygulayın.</span><span class="sxs-lookup"><span data-stu-id="dc292-444">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="dc292-445">ASP.NET Core çalışma zamanı, gövdeyi bir giriş biçimlendirici 'ya okumaktan sorumlu olarak temsil eder.</span><span class="sxs-lookup"><span data-stu-id="dc292-445">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="dc292-446">Giriş biçimleri [Bu makalenin ilerleyen kısımlarında](#input-formatters)açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="dc292-446">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="dc292-447">`[FromBody]`Karmaşık bir tür parametresine uygulandığında, özelliklerine uygulanan herhangi bir bağlama kaynak özniteliği yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="dc292-447">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="dc292-448">Örneğin, aşağıdaki eylem, `Create` `pet` parametresinin gövdeden doldurulduğunu belirtir:</span><span class="sxs-lookup"><span data-stu-id="dc292-448">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="dc292-449">`Pet`Sınıfı, `Breed` özelliğinin bir sorgu dizesi parametresinden doldurulduğunu belirtir:</span><span class="sxs-lookup"><span data-stu-id="dc292-449">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="dc292-450">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="dc292-450">In the preceding example:</span></span>

* <span data-ttu-id="dc292-451">`[FromQuery]`Öznitelik yoksayıldı.</span><span class="sxs-lookup"><span data-stu-id="dc292-451">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="dc292-452">`Breed`Özellik bir sorgu dizesi parametresinden doldurulmamış.</span><span class="sxs-lookup"><span data-stu-id="dc292-452">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="dc292-453">Giriş biçimleri yalnızca gövdeyi okur ve bağlama kaynak özniteliklerini anlamayın.</span><span class="sxs-lookup"><span data-stu-id="dc292-453">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="dc292-454">Gövdede uygun bir değer bulunursa, bu değer özelliği doldurmak için kullanılır `Breed` .</span><span class="sxs-lookup"><span data-stu-id="dc292-454">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="dc292-455">`[FromBody]`Action yöntemi başına birden fazla parametreye uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="dc292-455">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="dc292-456">İstek akışı bir giriş biçimlendirici tarafından okunduktan sonra, diğer parametreleri bağlamak için artık bir daha okunamaz `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="dc292-456">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="dc292-457">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="dc292-457">Additional sources</span></span>

<span data-ttu-id="dc292-458">Kaynak verileri, model bağlama sistemine *değer sağlayıcılara*göre sağlanır.</span><span class="sxs-lookup"><span data-stu-id="dc292-458">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="dc292-459">Diğer kaynaklardan model bağlamaya yönelik verileri alan özel değer sağlayıcıları yazabilir ve kaydedebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="dc292-459">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="dc292-460">Örneğin, verilerin cookie veya oturum durumunun olmasını isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="dc292-460">For example, you might want data from cookies or session state.</span></span> <span data-ttu-id="dc292-461">Yeni bir kaynaktan veri almak için:</span><span class="sxs-lookup"><span data-stu-id="dc292-461">To get data from a new source:</span></span>

* <span data-ttu-id="dc292-462">Uygulayan bir sınıf oluşturun `IValueProvider` .</span><span class="sxs-lookup"><span data-stu-id="dc292-462">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="dc292-463">Uygulayan bir sınıf oluşturun `IValueProviderFactory` .</span><span class="sxs-lookup"><span data-stu-id="dc292-463">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="dc292-464">Fabrika sınıfını içine kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="dc292-464">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="dc292-465">Örnek uygulama, ' den değerler alan bir [değer sağlayıcısı](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) ve [Factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) örneği içerir cookie .</span><span class="sxs-lookup"><span data-stu-id="dc292-465">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/CookieValueProviderFactory.cs) example that gets values from cookies.</span></span> <span data-ttu-id="dc292-466">Kayıt kodu aşağıda verilmiştir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="dc292-466">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="dc292-467">Gösterilen kod, tüm yerleşik değer sağlayıcılarından sonra özel değer sağlayıcısını koyar.</span><span class="sxs-lookup"><span data-stu-id="dc292-467">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="dc292-468">Bunu listede ilk yapmak için `Insert(0, new CookieValueProviderFactory())` yerine çağırın `Add` .</span><span class="sxs-lookup"><span data-stu-id="dc292-468">To make it the first in the list, call `Insert(0, new CookieValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="dc292-469">Model özelliği için kaynak yok</span><span class="sxs-lookup"><span data-stu-id="dc292-469">No source for a model property</span></span>

<span data-ttu-id="dc292-470">Varsayılan olarak, model özelliği için bir değer bulunmazsa model durumu hatası oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="dc292-470">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="dc292-471">Özelliği null veya varsayılan bir değer olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="dc292-471">The property is set to null or a default value:</span></span>

* <span data-ttu-id="dc292-472">Null yapılabilir basit türler olarak ayarlanır `null` .</span><span class="sxs-lookup"><span data-stu-id="dc292-472">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="dc292-473">Null yapılamayan değer türleri olarak ayarlanır `default(T)` .</span><span class="sxs-lookup"><span data-stu-id="dc292-473">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="dc292-474">Örneğin, bir parametre `int id` 0 olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="dc292-474">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="dc292-475">Karmaşık türler için model bağlama, özellikleri ayarlamadan varsayılan oluşturucuyu kullanarak bir örnek oluşturur.</span><span class="sxs-lookup"><span data-stu-id="dc292-475">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="dc292-476">Diziler olarak ayarlanır `Array.Empty<T>()` , ancak `byte[]` diziler olarak ayarlanır `null` .</span><span class="sxs-lookup"><span data-stu-id="dc292-476">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="dc292-477">Model özelliği için form alanlarında hiçbir şey bulunamadığında model durumunun geçersiz kılınmalıdır, [`[BindRequired]`](#bindrequired-attribute) özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="dc292-477">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="dc292-478">Bu `[BindRequired]` davranışın, bir istek GÖVDESINDE JSON veya XML verilerine değil, postalanan form verilerinden model bağlama için geçerli olduğunu unutmayın.</span><span class="sxs-lookup"><span data-stu-id="dc292-478">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="dc292-479">İstek gövdesi verileri, [giriş formatlayıcıları](#input-formatters)tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="dc292-479">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="dc292-480">Tür dönüştürme hataları</span><span class="sxs-lookup"><span data-stu-id="dc292-480">Type conversion errors</span></span>

<span data-ttu-id="dc292-481">Bir kaynak bulunursa ancak hedef türe dönüştürülemiyorsa, model durumu geçersiz olarak işaretlenir.</span><span class="sxs-lookup"><span data-stu-id="dc292-481">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="dc292-482">Hedef parametresi veya özelliği, önceki bölümde belirtildiği gibi null veya varsayılan değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="dc292-482">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="dc292-483">Özniteliği olan bir API denetleyicisinde `[ApiController]` , geçersiz model durumu OTOMATIK HTTP 400 yanıtına neden olur.</span><span class="sxs-lookup"><span data-stu-id="dc292-483">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="dc292-484">Bir Razor sayfada, sayfayı bir hata iletisiyle yeniden görüntüleyin:</span><span class="sxs-lookup"><span data-stu-id="dc292-484">In a Razor page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="dc292-485">İstemci tarafı doğrulama, aksi takdirde bir sayfalar formuna gönderilemeyen hatalı verileri yakalar Razor .</span><span class="sxs-lookup"><span data-stu-id="dc292-485">Client-side validation catches most bad data that would otherwise be submitted to a Razor Pages form.</span></span> <span data-ttu-id="dc292-486">Bu doğrulama, önceki vurgulanmış kodu tetiklemeyi zorlaştırır.</span><span class="sxs-lookup"><span data-stu-id="dc292-486">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="dc292-487">Örnek uygulama, teslim **tarihi** alanına hatalı veri yerleştiren ve formu Gönderen **Geçersiz tarih içeren bir Gönder** düğmesi içerir.</span><span class="sxs-lookup"><span data-stu-id="dc292-487">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="dc292-488">Bu düğme, veri dönüştürme hataları oluştuğunda sayfanın yeniden görüntülenmesine yönelik kodun nasıl çalıştığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="dc292-488">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="dc292-489">Sayfa önceki kodla yeniden görüntülendiğinde, form alanında geçersiz giriş gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="dc292-489">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="dc292-490">Bunun nedeni model özelliğinin null ya da varsayılan bir değer olarak ayarlanmış olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="dc292-490">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="dc292-491">Geçersiz giriş bir hata iletisinde görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="dc292-491">The invalid input does appear in an error message.</span></span> <span data-ttu-id="dc292-492">Ancak form alanındaki hatalı verileri yeniden görüntülemek istiyorsanız, model özelliğini bir dize haline getirmeyi ve veri dönüştürmeyi el ile gerçekleştirmeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="dc292-492">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="dc292-493">Tür dönüştürme hatalarının model durumu hatalarına neden olmasını istemiyorsanız aynı strateji önerilir.</span><span class="sxs-lookup"><span data-stu-id="dc292-493">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="dc292-494">Bu durumda model özelliğini bir dize yapın.</span><span class="sxs-lookup"><span data-stu-id="dc292-494">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="dc292-495">Basit türler</span><span class="sxs-lookup"><span data-stu-id="dc292-495">Simple types</span></span>

<span data-ttu-id="dc292-496">Model cildin kaynak dizeleri dönüştürebileceğiniz basit türler aşağıdakileri içerir:</span><span class="sxs-lookup"><span data-stu-id="dc292-496">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="dc292-497">Boole</span><span class="sxs-lookup"><span data-stu-id="dc292-497">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="dc292-498">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="dc292-498">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="dc292-499">Char</span><span class="sxs-lookup"><span data-stu-id="dc292-499">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="dc292-500">Tarih Saat</span><span class="sxs-lookup"><span data-stu-id="dc292-500">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="dc292-501">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="dc292-501">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="dc292-502">Kategori</span><span class="sxs-lookup"><span data-stu-id="dc292-502">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="dc292-503">Çift</span><span class="sxs-lookup"><span data-stu-id="dc292-503">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="dc292-504">Sabit listesi</span><span class="sxs-lookup"><span data-stu-id="dc292-504">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="dc292-505">'İni</span><span class="sxs-lookup"><span data-stu-id="dc292-505">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="dc292-506">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="dc292-506">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="dc292-507">Tek</span><span class="sxs-lookup"><span data-stu-id="dc292-507">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="dc292-508">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="dc292-508">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="dc292-509">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="dc292-509">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="dc292-510">Kullanılmamışsa</span><span class="sxs-lookup"><span data-stu-id="dc292-510">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="dc292-511">Sürüm</span><span class="sxs-lookup"><span data-stu-id="dc292-511">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="dc292-512">Karmaşık türler</span><span class="sxs-lookup"><span data-stu-id="dc292-512">Complex types</span></span>

<span data-ttu-id="dc292-513">Karmaşık bir türün bağlanması için ortak bir varsayılan Oluşturucusu ve ortak yazılabilir özellikleri olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="dc292-513">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="dc292-514">Model bağlama gerçekleştiğinde, sınıf ortak varsayılan Oluşturucu kullanılarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="dc292-514">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="dc292-515">Karmaşık türün her özelliği için model bağlama, ad modeli ön eki için kaynakları arar *. property_name*.</span><span class="sxs-lookup"><span data-stu-id="dc292-515">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="dc292-516">Hiçbir şey bulunamazsa, ön ek olmadan yalnızca *property_name* arar.</span><span class="sxs-lookup"><span data-stu-id="dc292-516">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="dc292-517">Bir parametreye bağlama için, önek parametre adıdır.</span><span class="sxs-lookup"><span data-stu-id="dc292-517">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="dc292-518">Ortak özelliğe bağlama için `PageModel` , önek ortak özellik adıdır.</span><span class="sxs-lookup"><span data-stu-id="dc292-518">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="dc292-519">Bazı özniteliklerin `Prefix` , parametre veya özellik adının varsayılan kullanımını geçersiz kılabilmenizi sağlayan bir özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="dc292-519">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="dc292-520">Örneğin, karmaşık türün aşağıdaki sınıf olduğunu varsayalım `Instructor` :</span><span class="sxs-lookup"><span data-stu-id="dc292-520">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="dc292-521">Önek = parametre adı</span><span class="sxs-lookup"><span data-stu-id="dc292-521">Prefix = parameter name</span></span>

<span data-ttu-id="dc292-522">Bağlanacak model adlı bir parametre ise `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="dc292-522">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="dc292-523">Model bağlama, anahtar kaynaklarına bakarak başlar `instructorToUpdate.ID` .</span><span class="sxs-lookup"><span data-stu-id="dc292-523">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="dc292-524">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="dc292-524">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="dc292-525">Önek = Özellik adı</span><span class="sxs-lookup"><span data-stu-id="dc292-525">Prefix = property name</span></span>

<span data-ttu-id="dc292-526">Bağlanacak model, `Instructor` denetleyicinin veya sınıfın adında bir özelliktir `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="dc292-526">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="dc292-527">Model bağlama, anahtar kaynaklarına bakarak başlar `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="dc292-527">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="dc292-528">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="dc292-528">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="dc292-529">Özel ön ek</span><span class="sxs-lookup"><span data-stu-id="dc292-529">Custom prefix</span></span>

<span data-ttu-id="dc292-530">Bağlanacak model adlı bir parametre ise `instructorToUpdate` ve bir `Bind` öznitelik `Instructor` önek olarak belirtirse:</span><span class="sxs-lookup"><span data-stu-id="dc292-530">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="dc292-531">Model bağlama, anahtar kaynaklarına bakarak başlar `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="dc292-531">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="dc292-532">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="dc292-532">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="dc292-533">Karmaşık tür hedefleri için öznitelikler</span><span class="sxs-lookup"><span data-stu-id="dc292-533">Attributes for complex type targets</span></span>

<span data-ttu-id="dc292-534">Karmaşık türlerin model bağlamasını denetlemek için birkaç yerleşik öznitelik mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="dc292-534">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="dc292-535">Bu öznitelikler, gönderilen form verileri değer kaynağı olduğunda model bağlamayı etkiler.</span><span class="sxs-lookup"><span data-stu-id="dc292-535">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="dc292-536">Bunlar, gönderilen JSON ve XML istek gövdelerini işleyen giriş formatlayıcıları 'nı etkilemez.</span><span class="sxs-lookup"><span data-stu-id="dc292-536">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="dc292-537">Giriş biçimleri [Bu makalenin ilerleyen kısımlarında](#input-formatters)açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="dc292-537">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="dc292-538">Ayrıca bkz `[Required]` . [model doğrulama](xref:mvc/models/validation#required-attribute)içindeki öznitelik tartışması.</span><span class="sxs-lookup"><span data-stu-id="dc292-538">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="dc292-539">[BindRequired] özniteliği</span><span class="sxs-lookup"><span data-stu-id="dc292-539">[BindRequired] attribute</span></span>

<span data-ttu-id="dc292-540">, Yöntem parametrelerine değil yalnızca model özelliklerine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="dc292-540">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="dc292-541">Modelin özelliği için bağlama gerçekleşmemişse model bağlamasının model durumu hatası eklemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="dc292-541">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="dc292-542">Aşağıda bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="dc292-542">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="dc292-543">[Bindhiç] özniteliği</span><span class="sxs-lookup"><span data-stu-id="dc292-543">[BindNever] attribute</span></span>

<span data-ttu-id="dc292-544">, Yöntem parametrelerine değil yalnızca model özelliklerine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="dc292-544">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="dc292-545">Model bağlamasının model özelliğini değiştirmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="dc292-545">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="dc292-546">Aşağıda bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="dc292-546">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="dc292-547">[Bind] özniteliği</span><span class="sxs-lookup"><span data-stu-id="dc292-547">[Bind] attribute</span></span>

<span data-ttu-id="dc292-548">, Bir sınıfa veya yöntem parametresine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="dc292-548">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="dc292-549">Model bağlamasındaki bir modelin hangi özelliklerinin dahil edileceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="dc292-549">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="dc292-550">Aşağıdaki örnekte, `Instructor` herhangi bir işleyici veya eylem yöntemi çağrıldığında yalnızca modelin belirtilen özellikleri bağlanır:</span><span class="sxs-lookup"><span data-stu-id="dc292-550">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="dc292-551">Aşağıdaki örnekte, `Instructor` yöntemi çağrıldığında yalnızca modelin belirtilen özellikleri bağlanır `OnPost` :</span><span class="sxs-lookup"><span data-stu-id="dc292-551">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="dc292-552">`[Bind]`Özniteliği, *oluşturma* senaryolarında fazla nakline karşı korumak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="dc292-552">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="dc292-553">Dışlanan Özellikler null ya da boş değer olarak ayarlandığı için, düzenleme senaryolarında iyi çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="dc292-553">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="dc292-554">Fazla naklin savunma için, öznitelik yerine görüntüleme modelleri önerilir `[Bind]` .</span><span class="sxs-lookup"><span data-stu-id="dc292-554">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="dc292-555">Daha fazla bilgi için bkz. fazla [nakil hakkında güvenlik NOI](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="dc292-555">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="dc292-556">Koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="dc292-556">Collections</span></span>

<span data-ttu-id="dc292-557">Basit türlerin koleksiyonları olan hedefler için model bağlama, *parameter_name* veya *property_name*ile eşleşmeleri arar.</span><span class="sxs-lookup"><span data-stu-id="dc292-557">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="dc292-558">Eşleşme bulunmazsa, ön ek olmadan desteklenen biçimlerden birini arar.</span><span class="sxs-lookup"><span data-stu-id="dc292-558">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="dc292-559">Örnek:</span><span class="sxs-lookup"><span data-stu-id="dc292-559">For example:</span></span>

* <span data-ttu-id="dc292-560">Bağlanacak parametrenin adlı bir dizi olduğunu varsayalım `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="dc292-560">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="dc292-561">Form veya sorgu dizesi verileri aşağıdaki biçimlerden birinde olabilir:</span><span class="sxs-lookup"><span data-stu-id="dc292-561">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="dc292-562">Aşağıdaki biçim yalnızca form verilerinde desteklenir:</span><span class="sxs-lookup"><span data-stu-id="dc292-562">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="dc292-563">Önceki örnek biçimlerinin hepsi için model bağlama iki öğe dizisini `selectedCourses` parametreye geçirir:</span><span class="sxs-lookup"><span data-stu-id="dc292-563">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="dc292-564">Selectedkurslar [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="dc292-564">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="dc292-565">Selectedkurslar [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="dc292-565">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="dc292-566">Alt simge numaralarını kullanan veri biçimleri (... [0]... [1]...) sıfırdan başlayarak sıralı olarak numaralandırıldıklarından emin olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="dc292-566">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="dc292-567">Alt simge numaralandırmasında boşluk varsa, boşluklardan sonraki tüm öğeler yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="dc292-567">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="dc292-568">Örneğin, alt simgeler 0 ve 1 yerine 0 ve 2 ise ikinci öğe yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="dc292-568">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="dc292-569">Sözlükler</span><span class="sxs-lookup"><span data-stu-id="dc292-569">Dictionaries</span></span>

<span data-ttu-id="dc292-570">`Dictionary`Hedefler için, model bağlama *parameter_name* veya *property_name*eşleşme arar.</span><span class="sxs-lookup"><span data-stu-id="dc292-570">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="dc292-571">Eşleşme bulunmazsa, ön ek olmadan desteklenen biçimlerden birini arar.</span><span class="sxs-lookup"><span data-stu-id="dc292-571">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="dc292-572">Örnek:</span><span class="sxs-lookup"><span data-stu-id="dc292-572">For example:</span></span>

* <span data-ttu-id="dc292-573">Hedef parametrenin bir adlandırılmış olduğunu varsayalım `Dictionary<int, string>` `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="dc292-573">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="dc292-574">Postalanan form veya sorgu dizesi verileri aşağıdaki örneklerden birine benzeyebilir:</span><span class="sxs-lookup"><span data-stu-id="dc292-574">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="dc292-575">Önceki örnek biçimlerinin hepsi için model bağlama iki öğenin bir sözlüğünü `selectedCourses` parametreye geçirir:</span><span class="sxs-lookup"><span data-stu-id="dc292-575">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="dc292-576">Selectedkurslar ["1050"] = "Chemistry"</span><span class="sxs-lookup"><span data-stu-id="dc292-576">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="dc292-577">Selectedkurslar ["2000"] = "Ekonomiks"</span><span class="sxs-lookup"><span data-stu-id="dc292-577">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="dc292-578">Model bağlama yolu verileri ve sorgu dizeleri için Genelleştirme davranışı</span><span class="sxs-lookup"><span data-stu-id="dc292-578">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="dc292-579">ASP.NET Core yol değeri sağlayıcısı ve sorgu dizesi değer sağlayıcısı:</span><span class="sxs-lookup"><span data-stu-id="dc292-579">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="dc292-580">Değerleri sabit kültür olarak değerlendirin.</span><span class="sxs-lookup"><span data-stu-id="dc292-580">Treat values as invariant culture.</span></span>
* <span data-ttu-id="dc292-581">URL 'Lerin kültür sabiti olmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="dc292-581">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="dc292-582">Buna karşılık, form verilerinden gelen değerler kültüre duyarlı bir dönüştürmeye gider.</span><span class="sxs-lookup"><span data-stu-id="dc292-582">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="dc292-583">Bu, URL 'Lerin yerel ayarlarda paylaşılabilir olması için tasarımdır.</span><span class="sxs-lookup"><span data-stu-id="dc292-583">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="dc292-584">ASP.NET Core yol değeri sağlayıcısını ve sorgu dizesi değeri sağlayıcısını, kültüre duyarlı bir dönüşüme dönüştürmek için:</span><span class="sxs-lookup"><span data-stu-id="dc292-584">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="dc292-585">Şuradan devralma<xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="dc292-585">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="dc292-586">[QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) veya [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs) adresinden kodu kopyalayın</span><span class="sxs-lookup"><span data-stu-id="dc292-586">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="dc292-587">Değer sağlayıcısı oluşturucusuna geçirilen [kültür değerini](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture) ile değiştirin</span><span class="sxs-lookup"><span data-stu-id="dc292-587">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="dc292-588">MVC seçeneklerinde varsayılan değer sağlayıcı fabrikasını yeni bir değerle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="dc292-588">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="dc292-589">Özel veri türleri</span><span class="sxs-lookup"><span data-stu-id="dc292-589">Special data types</span></span>

<span data-ttu-id="dc292-590">Model bağlamanın işleyebileceği bazı özel veri türleri vardır.</span><span class="sxs-lookup"><span data-stu-id="dc292-590">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="dc292-591">Iformfile ve ıformfilecollection</span><span class="sxs-lookup"><span data-stu-id="dc292-591">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="dc292-592">HTTP isteğine eklenen karşıya yüklenen dosya.</span><span class="sxs-lookup"><span data-stu-id="dc292-592">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="dc292-593">Ayrıca, `IEnumerable<IFormFile>` birden çok dosya için de desteklenir.</span><span class="sxs-lookup"><span data-stu-id="dc292-593">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="dc292-594">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="dc292-594">CancellationToken</span></span>

<span data-ttu-id="dc292-595">Zaman uyumsuz denetleyicilerde etkinliği iptal etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="dc292-595">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="dc292-596">Form koleksiyonu</span><span class="sxs-lookup"><span data-stu-id="dc292-596">FormCollection</span></span>

<span data-ttu-id="dc292-597">Postalanan form verilerinden tüm değerleri almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="dc292-597">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="dc292-598">Giriş biçimleri</span><span class="sxs-lookup"><span data-stu-id="dc292-598">Input formatters</span></span>

<span data-ttu-id="dc292-599">İstek gövdesindeki veriler JSON, XML veya başka bir biçimde olabilir.</span><span class="sxs-lookup"><span data-stu-id="dc292-599">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="dc292-600">Model bağlama, bu verileri ayrıştırmak için belirli bir içerik türünü işlemek üzere yapılandırılmış bir *giriş biçimlendiricisi* kullanır.</span><span class="sxs-lookup"><span data-stu-id="dc292-600">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="dc292-601">Varsayılan olarak, ASP.NET Core JSON verilerini işlemek için JSON tabanlı giriş formatlayıcıları içerir.</span><span class="sxs-lookup"><span data-stu-id="dc292-601">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="dc292-602">Diğer içerik türleri için başka biçim ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="dc292-602">You can add other formatters for other content types.</span></span>

<span data-ttu-id="dc292-603">ASP.NET Core, [tüketen](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) özniteliğine bağlı olarak giriş formatlayıcıları seçer.</span><span class="sxs-lookup"><span data-stu-id="dc292-603">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="dc292-604">Hiçbir öznitelik yoksa, [Content-Type üst bilgisini](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)kullanır.</span><span class="sxs-lookup"><span data-stu-id="dc292-604">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="dc292-605">Yerleşik XML girişi formatlayıcıları 'nı kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="dc292-605">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="dc292-606">`Microsoft.AspNetCore.Mvc.Formatters.Xml`NuGet paketini yükler.</span><span class="sxs-lookup"><span data-stu-id="dc292-606">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="dc292-607">İçinde `Startup.ConfigureServices` , veya öğesini çağırın <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> .</span><span class="sxs-lookup"><span data-stu-id="dc292-607">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="dc292-608">Özniteliği, `Consumes` istek GÖVDESINDE XML beklemeniz gereken denetleyici sınıflarına veya eylem yöntemlerine uygulayın.</span><span class="sxs-lookup"><span data-stu-id="dc292-608">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="dc292-609">Daha fazla bilgi için bkz. [XML serileştirme tanıtımı](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="dc292-609">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="dc292-610">Belirtilen türleri model bağlamalarından Dışla</span><span class="sxs-lookup"><span data-stu-id="dc292-610">Exclude specified types from model binding</span></span>

<span data-ttu-id="dc292-611">Model bağlama ve doğrulama sistemlerinin davranışı [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata)tarafından yönlendiriliyor.</span><span class="sxs-lookup"><span data-stu-id="dc292-611">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="dc292-612">`ModelMetadata` [Mvcoptions. modelmetadatadetails sağlayıcılarına](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders)bir ayrıntı sağlayıcısı ekleyerek özelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="dc292-612">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="dc292-613">Yerleşik Ayrıntılar sağlayıcıları, belirtilen türler için model bağlamayı veya doğrulamayı devre dışı bırakmak üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="dc292-613">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="dc292-614">Belirtilen türdeki tüm modellerdeki model bağlamayı devre dışı bırakmak için içine bir ekleyin <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="dc292-614">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="dc292-615">Örneğin, türü tüm modeller üzerinde model bağlamayı devre dışı bırakmak için `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="dc292-615">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="dc292-616">Belirtilen türdeki özelliklerde doğrulamayı devre dışı bırakmak için içine bir ekleyin <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="dc292-616">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="dc292-617">Örneğin, türündeki özelliklerde doğrulamayı devre dışı bırakmak için `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="dc292-617">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="dc292-618">Özel model ciltleri</span><span class="sxs-lookup"><span data-stu-id="dc292-618">Custom model binders</span></span>

<span data-ttu-id="dc292-619">Özel bir model cildi yazarak ve `[ModelBinder]` belirli bir hedef için seçmek üzere özniteliğini kullanarak model bağlamayı genişletebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="dc292-619">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="dc292-620">[Özel model bağlama](xref:mvc/advanced/custom-model-binding)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="dc292-620">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="dc292-621">El ile model bağlama</span><span class="sxs-lookup"><span data-stu-id="dc292-621">Manual model binding</span></span>

<span data-ttu-id="dc292-622">Model bağlama yöntemi kullanılarak el ile çağrılabilir <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> .</span><span class="sxs-lookup"><span data-stu-id="dc292-622">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="dc292-623">Yöntemi hem hem de sınıflarında tanımlanmıştır `ControllerBase` `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="dc292-623">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="dc292-624">Yöntem aşırı yüklemeleri, kullanılacak öneki ve değer sağlayıcısını belirtmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="dc292-624">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="dc292-625">Yöntemi `false` model bağlamanın başarısız olup olmadığını döndürür.</span><span class="sxs-lookup"><span data-stu-id="dc292-625">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="dc292-626">Aşağıda bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="dc292-626">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="dc292-627">[FromServices] özniteliği</span><span class="sxs-lookup"><span data-stu-id="dc292-627">[FromServices] attribute</span></span>

<span data-ttu-id="dc292-628">Bu özniteliğin adı, bir veri kaynağı belirten model bağlama özniteliklerinin düzeniyle uyar.</span><span class="sxs-lookup"><span data-stu-id="dc292-628">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="dc292-629">Ancak bir değer sağlayıcısından veri bağlama hakkında bilgi yoktur.</span><span class="sxs-lookup"><span data-stu-id="dc292-629">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="dc292-630">[Bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısından bir türün bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="dc292-630">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="dc292-631">Amacı, yalnızca belirli bir yöntem çağrılırsa bir hizmete ihtiyacınız olduğunda Oluşturucu ekleme için bir alternatif sağlamaktır.</span><span class="sxs-lookup"><span data-stu-id="dc292-631">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dc292-632">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="dc292-632">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
