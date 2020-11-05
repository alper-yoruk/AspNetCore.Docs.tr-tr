---
title: ASP.NET Core 'de model bağlama
author: rick-anderson
description: ASP.NET Core model bağlamasının nasıl çalıştığını ve davranışını nasıl özelleştireceğinizi öğrenin.
ms.assetid: 0be164aa-1d72-4192-bd6b-192c9c301164
ms.author: riande
ms.date: 12/18/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/models/model-binding
ms.openlocfilehash: 49300d32096e577db9b13a0510cc310b91ddb51d
ms.sourcegitcommit: 33f631a4427b9a422755601ac9119953db0b4a3e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93365359"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="748cd-103">ASP.NET Core 'de model bağlama</span><span class="sxs-lookup"><span data-stu-id="748cd-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="748cd-104">Bu makalede, model bağlamanın ne olduğu, nasıl çalıştığı ve davranışını nasıl özelleştireceğiniz açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="748cd-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="748cd-105">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="748cd-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="748cd-106">Model bağlama nedir?</span><span class="sxs-lookup"><span data-stu-id="748cd-106">What is Model binding</span></span>

<span data-ttu-id="748cd-107">Denetleyiciler ve :::no-loc(Razor)::: Sayfalar, http isteklerinden gelen verilerle çalışır.</span><span class="sxs-lookup"><span data-stu-id="748cd-107">Controllers and :::no-loc(Razor)::: pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="748cd-108">Örneğin, rota verileri bir kayıt anahtarı sağlayabilir ve postalanan form alanları, modelin özelliklerine ilişkin değerler sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="748cd-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="748cd-109">Bu değerlerin her birini almak ve bunları dizelerden .NET türlerine dönüştürmek için kod yazma sıkıcı ve hata durumunda olabilir.</span><span class="sxs-lookup"><span data-stu-id="748cd-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="748cd-110">Model bağlama bu işlemi otomatikleştirir.</span><span class="sxs-lookup"><span data-stu-id="748cd-110">Model binding automates this process.</span></span> <span data-ttu-id="748cd-111">Model bağlama sistemi:</span><span class="sxs-lookup"><span data-stu-id="748cd-111">The model binding system:</span></span>

* <span data-ttu-id="748cd-112">Veri yolu, form alanları ve sorgu dizeleri gibi çeşitli kaynaklardan veri alır.</span><span class="sxs-lookup"><span data-stu-id="748cd-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="748cd-113">:::no-loc(Razor):::Yöntem parametreleri ve ortak özellikler içindeki denetleyicilere ve sayfalara verileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="748cd-113">Provides the data to controllers and :::no-loc(Razor)::: pages in method parameters and public properties.</span></span>
* <span data-ttu-id="748cd-114">Dize verilerini .NET türlerine dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="748cd-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="748cd-115">Karmaşık türlerin özelliklerini güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="748cd-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="748cd-116">Örnek</span><span class="sxs-lookup"><span data-stu-id="748cd-116">Example</span></span>

<span data-ttu-id="748cd-117">Aşağıdaki eylem yöntemine sahip olduğunuzu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="748cd-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="748cd-118">Ve uygulama şu URL ile bir istek alıyor:</span><span class="sxs-lookup"><span data-stu-id="748cd-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="748cd-119">Model bağlama, yönlendirme sistemi eylem yöntemini seçtikten sonra aşağıdaki adımlardan geçer:</span><span class="sxs-lookup"><span data-stu-id="748cd-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="748cd-120">Adlı bir tamsayı olan ilk parametresini bulur `GetByID` `id` .</span><span class="sxs-lookup"><span data-stu-id="748cd-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="748cd-121">HTTP isteğindeki kullanılabilir kaynakları arar ve `id` route verilerinde = "2" bulur.</span><span class="sxs-lookup"><span data-stu-id="748cd-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="748cd-122">"2" dizesini tamsayı 2 ' ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="748cd-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="748cd-123">`GetByID`Adlı bir Boole değeri olan bir sonraki parametresini bulur `dogsOnly` .</span><span class="sxs-lookup"><span data-stu-id="748cd-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="748cd-124">Kaynakları arar ve sorgu dizesinde "DogsOnly = true" bulur.</span><span class="sxs-lookup"><span data-stu-id="748cd-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="748cd-125">Ad eşleştirme, büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="748cd-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="748cd-126">"True" dizesini Boole değerine dönüştürür `true` .</span><span class="sxs-lookup"><span data-stu-id="748cd-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="748cd-127">Framework daha sonra yöntemi çağırır `GetById` , parametresi için 2 ' ye geçerek `id` ve `true` `dogsOnly` parametresi için.</span><span class="sxs-lookup"><span data-stu-id="748cd-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="748cd-128">Önceki örnekte, model bağlama hedefleri basit türler olan yöntem parametreleridir.</span><span class="sxs-lookup"><span data-stu-id="748cd-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="748cd-129">Hedefler, karmaşık bir türün özellikleri de olabilir.</span><span class="sxs-lookup"><span data-stu-id="748cd-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="748cd-130">Her bir özellik başarıyla bağlandıktan sonra, bu özellik için [model doğrulaması](xref:mvc/models/validation) oluşur.</span><span class="sxs-lookup"><span data-stu-id="748cd-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="748cd-131">Hangi verilerin modele bağladığına ve tüm bağlama veya doğrulama hatalarıyla ilgili kayıt, [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) veya [Pagemodel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState)içinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="748cd-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="748cd-132">Bu işlemin başarılı olup olmadığını öğrenmek için uygulama [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) bayrağını denetler.</span><span class="sxs-lookup"><span data-stu-id="748cd-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="748cd-133">Targets</span><span class="sxs-lookup"><span data-stu-id="748cd-133">Targets</span></span>

<span data-ttu-id="748cd-134">Model bağlama, aşağıdaki tür hedeflerin değerlerini bulmayı dener:</span><span class="sxs-lookup"><span data-stu-id="748cd-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="748cd-135">Bir isteğin yönlendirildiği denetleyici eylemi yönteminin parametreleri.</span><span class="sxs-lookup"><span data-stu-id="748cd-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="748cd-136">:::no-loc(Razor):::Bir isteğin yönlendirildiği sayfa işleyicisi yönteminin parametreleri.</span><span class="sxs-lookup"><span data-stu-id="748cd-136">Parameters of the :::no-loc(Razor)::: Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="748cd-137">Bir denetleyicinin veya `PageModel` sınıfın öznitelikleri tarafından belirtilmişse ortak özellikleri.</span><span class="sxs-lookup"><span data-stu-id="748cd-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="748cd-138">[BindProperty] özniteliği</span><span class="sxs-lookup"><span data-stu-id="748cd-138">[BindProperty] attribute</span></span>

<span data-ttu-id="748cd-139">, `PageModel` Model bağlamasının bu özelliği hedeflemesini sağlamak için bir denetleyicinin veya sınıfın ortak özelliğine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="748cd-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="748cd-140">[BindProperties] özniteliği</span><span class="sxs-lookup"><span data-stu-id="748cd-140">[BindProperties] attribute</span></span>

<span data-ttu-id="748cd-141">ASP.NET Core 2,1 ve üzeri sürümlerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="748cd-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="748cd-142">`PageModel`Model bağlamaya, sınıfın tüm ortak özelliklerini hedeflemesini bildirmek için bir denetleyiciye veya sınıfa uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="748cd-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="748cd-143">HTTP GET istekleri için model bağlama</span><span class="sxs-lookup"><span data-stu-id="748cd-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="748cd-144">Varsayılan olarak, Özellikler HTTP GET istekleri için bağlantılı değildir.</span><span class="sxs-lookup"><span data-stu-id="748cd-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="748cd-145">Genellikle, bir GET isteği için tüm ihtiyacınız olan bir kayıt KIMLIĞI parametresidir.</span><span class="sxs-lookup"><span data-stu-id="748cd-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="748cd-146">Kayıt KIMLIĞI, veritabanındaki öğeyi aramak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="748cd-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="748cd-147">Bu nedenle, modelin bir örneğini tutan bir özelliği bağlamaya gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="748cd-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="748cd-148">GET isteklerinden alınan özelliklerin verilerine bağlanmasını istediğiniz senaryolarda `SupportsGet` özelliği şu şekilde ayarlayın `true` :</span><span class="sxs-lookup"><span data-stu-id="748cd-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="748cd-149">Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="748cd-149">Sources</span></span>

<span data-ttu-id="748cd-150">Varsayılan olarak, model bağlama, bir HTTP isteğindeki aşağıdaki kaynaklardan gelen anahtar-değer çiftleri biçimindeki verileri alır:</span><span class="sxs-lookup"><span data-stu-id="748cd-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="748cd-151">Form alanları</span><span class="sxs-lookup"><span data-stu-id="748cd-151">Form fields</span></span>
1. <span data-ttu-id="748cd-152">İstek gövdesi ( [[ApiController] özniteliğine sahip denetleyiciler](xref:web-api/index#binding-source-parameter-inference)için.)</span><span class="sxs-lookup"><span data-stu-id="748cd-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="748cd-153">Veri yönlendirme</span><span class="sxs-lookup"><span data-stu-id="748cd-153">Route data</span></span>
1. <span data-ttu-id="748cd-154">Sorgu dizesi parametreleri</span><span class="sxs-lookup"><span data-stu-id="748cd-154">Query string parameters</span></span>
1. <span data-ttu-id="748cd-155">Karşıya yüklenen dosyalar</span><span class="sxs-lookup"><span data-stu-id="748cd-155">Uploaded files</span></span>

<span data-ttu-id="748cd-156">Her hedef parametresi veya özelliği için kaynaklar, önceki listede belirtilen sırada taranır.</span><span class="sxs-lookup"><span data-stu-id="748cd-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="748cd-157">Birkaç özel durum vardır:</span><span class="sxs-lookup"><span data-stu-id="748cd-157">There are a few exceptions:</span></span>

* <span data-ttu-id="748cd-158">Rota verileri ve sorgu dizesi değerleri yalnızca basit türler için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="748cd-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="748cd-159">Karşıya yüklenen dosyalar yalnızca veya uygulayan hedef türlere bağlanır `IFormFile` `IEnumerable<IFormFile>` .</span><span class="sxs-lookup"><span data-stu-id="748cd-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="748cd-160">Varsayılan kaynak doğru değilse, kaynağı belirtmek için aşağıdaki özniteliklerden birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="748cd-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="748cd-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Sorgu dizesinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="748cd-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="748cd-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Rota verilerinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="748cd-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="748cd-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Postalanan Form alanlarındaki değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="748cd-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="748cd-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -İstek gövdesinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="748cd-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="748cd-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -HTTP başlıklarındaki değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="748cd-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="748cd-166">Bu öznitelikler:</span><span class="sxs-lookup"><span data-stu-id="748cd-166">These attributes:</span></span>

* <span data-ttu-id="748cd-167">Model özelliklerine tek tek eklenir (model sınıfına değil), aşağıdaki örnekte olduğu gibi:</span><span class="sxs-lookup"><span data-stu-id="748cd-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="748cd-168">İsteğe bağlı olarak oluşturucuda bir model adı değeri kabul edin.</span><span class="sxs-lookup"><span data-stu-id="748cd-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="748cd-169">Bu seçenek, özellik adının istekteki değerle eşleşmemesi durumunda sağlanır.</span><span class="sxs-lookup"><span data-stu-id="748cd-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="748cd-170">Örneğin, istekteki değer aşağıdaki örnekte olduğu gibi adında bir tire olan bir üstbilgi olabilir:</span><span class="sxs-lookup"><span data-stu-id="748cd-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="748cd-171">[FromBody] özniteliği</span><span class="sxs-lookup"><span data-stu-id="748cd-171">[FromBody] attribute</span></span>

<span data-ttu-id="748cd-172">`[FromBody]`Özniteliği BIR http isteği gövdesinden doldurmak için bir parametreye özniteliğini uygulayın.</span><span class="sxs-lookup"><span data-stu-id="748cd-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="748cd-173">ASP.NET Core çalışma zamanı, gövdeyi bir giriş biçimlendirici 'ya okumaktan sorumlu olarak temsil eder.</span><span class="sxs-lookup"><span data-stu-id="748cd-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="748cd-174">Giriş biçimleri [Bu makalenin ilerleyen kısımlarında](#input-formatters)açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="748cd-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="748cd-175">`[FromBody]`Karmaşık bir tür parametresine uygulandığında, özelliklerine uygulanan herhangi bir bağlama kaynak özniteliği yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="748cd-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="748cd-176">Örneğin, aşağıdaki eylem, `Create` `pet` parametresinin gövdeden doldurulduğunu belirtir:</span><span class="sxs-lookup"><span data-stu-id="748cd-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="748cd-177">`Pet`Sınıfı, `Breed` özelliğinin bir sorgu dizesi parametresinden doldurulduğunu belirtir:</span><span class="sxs-lookup"><span data-stu-id="748cd-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="748cd-178">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="748cd-178">In the preceding example:</span></span>

* <span data-ttu-id="748cd-179">`[FromQuery]`Öznitelik yoksayıldı.</span><span class="sxs-lookup"><span data-stu-id="748cd-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="748cd-180">`Breed`Özellik bir sorgu dizesi parametresinden doldurulmamış.</span><span class="sxs-lookup"><span data-stu-id="748cd-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="748cd-181">Giriş biçimleri yalnızca gövdeyi okur ve bağlama kaynak özniteliklerini anlamayın.</span><span class="sxs-lookup"><span data-stu-id="748cd-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="748cd-182">Gövdede uygun bir değer bulunursa, bu değer özelliği doldurmak için kullanılır `Breed` .</span><span class="sxs-lookup"><span data-stu-id="748cd-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="748cd-183">`[FromBody]`Action yöntemi başına birden fazla parametreye uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="748cd-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="748cd-184">İstek akışı bir giriş biçimlendirici tarafından okunduktan sonra, diğer parametreleri bağlamak için artık bir daha okunamaz `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="748cd-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="748cd-185">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="748cd-185">Additional sources</span></span>

<span data-ttu-id="748cd-186">Kaynak verileri, model bağlama sistemine *değer sağlayıcılara* göre sağlanır.</span><span class="sxs-lookup"><span data-stu-id="748cd-186">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="748cd-187">Diğer kaynaklardan model bağlamaya yönelik verileri alan özel değer sağlayıcıları yazabilir ve kaydedebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="748cd-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="748cd-188">Örneğin, verilerin :::no-loc(cookie)::: veya oturum durumunun olmasını isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="748cd-188">For example, you might want data from :::no-loc(cookie):::s or session state.</span></span> <span data-ttu-id="748cd-189">Yeni bir kaynaktan veri almak için:</span><span class="sxs-lookup"><span data-stu-id="748cd-189">To get data from a new source:</span></span>

* <span data-ttu-id="748cd-190">Uygulayan bir sınıf oluşturun `IValueProvider` .</span><span class="sxs-lookup"><span data-stu-id="748cd-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="748cd-191">Uygulayan bir sınıf oluşturun `IValueProviderFactory` .</span><span class="sxs-lookup"><span data-stu-id="748cd-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="748cd-192">Fabrika sınıfını içine kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="748cd-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="748cd-193">Örnek uygulama, ' den değerler alan bir [değer sağlayıcısı](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) ve [Factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) örneği içerir :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="748cd-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) example that gets values from :::no-loc(cookie):::s.</span></span> <span data-ttu-id="748cd-194">Kayıt kodu aşağıda verilmiştir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="748cd-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="748cd-195">Gösterilen kod, tüm yerleşik değer sağlayıcılarından sonra özel değer sağlayıcısını koyar.</span><span class="sxs-lookup"><span data-stu-id="748cd-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="748cd-196">Bunu listede ilk yapmak için `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` yerine çağırın `Add` .</span><span class="sxs-lookup"><span data-stu-id="748cd-196">To make it the first in the list, call `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="748cd-197">Model özelliği için kaynak yok</span><span class="sxs-lookup"><span data-stu-id="748cd-197">No source for a model property</span></span>

<span data-ttu-id="748cd-198">Varsayılan olarak, model özelliği için bir değer bulunmazsa model durumu hatası oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="748cd-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="748cd-199">Özelliği null veya varsayılan bir değer olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="748cd-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="748cd-200">Null yapılabilir basit türler olarak ayarlanır `null` .</span><span class="sxs-lookup"><span data-stu-id="748cd-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="748cd-201">Null yapılamayan değer türleri olarak ayarlanır `default(T)` .</span><span class="sxs-lookup"><span data-stu-id="748cd-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="748cd-202">Örneğin, bir parametre `int id` 0 olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="748cd-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="748cd-203">Karmaşık türler için model bağlama, özellikleri ayarlamadan varsayılan oluşturucuyu kullanarak bir örnek oluşturur.</span><span class="sxs-lookup"><span data-stu-id="748cd-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="748cd-204">Diziler olarak ayarlanır `Array.Empty<T>()` , ancak `byte[]` diziler olarak ayarlanır `null` .</span><span class="sxs-lookup"><span data-stu-id="748cd-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="748cd-205">Model özelliği için form alanlarında hiçbir şey bulunamadığında model durumunun geçersiz kılınmalıdır, [`[BindRequired]`](#bindrequired-attribute) özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="748cd-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="748cd-206">Bu `[BindRequired]` davranışın, bir istek GÖVDESINDE JSON veya XML verilerine değil, postalanan form verilerinden model bağlama için geçerli olduğunu unutmayın.</span><span class="sxs-lookup"><span data-stu-id="748cd-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="748cd-207">İstek gövdesi verileri, [giriş formatlayıcıları](#input-formatters)tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="748cd-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="748cd-208">Tür dönüştürme hataları</span><span class="sxs-lookup"><span data-stu-id="748cd-208">Type conversion errors</span></span>

<span data-ttu-id="748cd-209">Bir kaynak bulunursa ancak hedef türe dönüştürülemiyorsa, model durumu geçersiz olarak işaretlenir.</span><span class="sxs-lookup"><span data-stu-id="748cd-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="748cd-210">Hedef parametresi veya özelliği, önceki bölümde belirtildiği gibi null veya varsayılan değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="748cd-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="748cd-211">Özniteliği olan bir API denetleyicisinde `[ApiController]` , geçersiz model durumu OTOMATIK HTTP 400 yanıtına neden olur.</span><span class="sxs-lookup"><span data-stu-id="748cd-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="748cd-212">Bir :::no-loc(Razor)::: sayfada, sayfayı bir hata iletisiyle yeniden görüntüleyin:</span><span class="sxs-lookup"><span data-stu-id="748cd-212">In a :::no-loc(Razor)::: page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="748cd-213">İstemci tarafı doğrulama, aksi takdirde bir sayfalar formuna gönderilemeyen hatalı verileri yakalar :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="748cd-213">Client-side validation catches most bad data that would otherwise be submitted to a :::no-loc(Razor)::: Pages form.</span></span> <span data-ttu-id="748cd-214">Bu doğrulama, önceki vurgulanmış kodu tetiklemeyi zorlaştırır.</span><span class="sxs-lookup"><span data-stu-id="748cd-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="748cd-215">Örnek uygulama, teslim **tarihi** alanına hatalı veri yerleştiren ve formu Gönderen **Geçersiz tarih içeren bir Gönder** düğmesi içerir.</span><span class="sxs-lookup"><span data-stu-id="748cd-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="748cd-216">Bu düğme, veri dönüştürme hataları oluştuğunda sayfanın yeniden görüntülenmesine yönelik kodun nasıl çalıştığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="748cd-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="748cd-217">Sayfa önceki kodla yeniden görüntülendiğinde, form alanında geçersiz giriş gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="748cd-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="748cd-218">Bunun nedeni model özelliğinin null ya da varsayılan bir değer olarak ayarlanmış olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="748cd-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="748cd-219">Geçersiz giriş bir hata iletisinde görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="748cd-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="748cd-220">Ancak form alanındaki hatalı verileri yeniden görüntülemek istiyorsanız, model özelliğini bir dize haline getirmeyi ve veri dönüştürmeyi el ile gerçekleştirmeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="748cd-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="748cd-221">Tür dönüştürme hatalarının model durumu hatalarına neden olmasını istemiyorsanız aynı strateji önerilir.</span><span class="sxs-lookup"><span data-stu-id="748cd-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="748cd-222">Bu durumda model özelliğini bir dize yapın.</span><span class="sxs-lookup"><span data-stu-id="748cd-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="748cd-223">Basit türler</span><span class="sxs-lookup"><span data-stu-id="748cd-223">Simple types</span></span>

<span data-ttu-id="748cd-224">Model cildin kaynak dizeleri dönüştürebileceğiniz basit türler aşağıdakileri içerir:</span><span class="sxs-lookup"><span data-stu-id="748cd-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="748cd-225">Boolean</span><span class="sxs-lookup"><span data-stu-id="748cd-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="748cd-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="748cd-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="748cd-227">Char</span><span class="sxs-lookup"><span data-stu-id="748cd-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="748cd-228">Tarih Saat</span><span class="sxs-lookup"><span data-stu-id="748cd-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="748cd-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="748cd-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="748cd-230">Kategori</span><span class="sxs-lookup"><span data-stu-id="748cd-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="748cd-231">Çift</span><span class="sxs-lookup"><span data-stu-id="748cd-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="748cd-232">Sabit listesi</span><span class="sxs-lookup"><span data-stu-id="748cd-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="748cd-233">'İni</span><span class="sxs-lookup"><span data-stu-id="748cd-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="748cd-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="748cd-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="748cd-235">Tek</span><span class="sxs-lookup"><span data-stu-id="748cd-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="748cd-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="748cd-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="748cd-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="748cd-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="748cd-238">Kullanılmamışsa</span><span class="sxs-lookup"><span data-stu-id="748cd-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="748cd-239">Sürüm</span><span class="sxs-lookup"><span data-stu-id="748cd-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="748cd-240">Karmaşık türler</span><span class="sxs-lookup"><span data-stu-id="748cd-240">Complex types</span></span>

<span data-ttu-id="748cd-241">Karmaşık bir türün bağlanması için ortak bir varsayılan Oluşturucusu ve ortak yazılabilir özellikleri olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="748cd-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="748cd-242">Model bağlama gerçekleştiğinde, sınıf ortak varsayılan Oluşturucu kullanılarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="748cd-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="748cd-243">Karmaşık türün her özelliği için model bağlama *prefix.property_name* ad deseninin kaynaklarını arar.</span><span class="sxs-lookup"><span data-stu-id="748cd-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="748cd-244">Hiçbir şey bulunamazsa, ön ek olmadan yalnızca *property_name* arar.</span><span class="sxs-lookup"><span data-stu-id="748cd-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="748cd-245">Bir parametreye bağlama için, önek parametre adıdır.</span><span class="sxs-lookup"><span data-stu-id="748cd-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="748cd-246">Ortak özelliğe bağlama için `PageModel` , önek ortak özellik adıdır.</span><span class="sxs-lookup"><span data-stu-id="748cd-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="748cd-247">Bazı özniteliklerin `Prefix` , parametre veya özellik adının varsayılan kullanımını geçersiz kılabilmenizi sağlayan bir özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="748cd-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="748cd-248">Örneğin, karmaşık türün aşağıdaki sınıf olduğunu varsayalım `Instructor` :</span><span class="sxs-lookup"><span data-stu-id="748cd-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="748cd-249">Önek = parametre adı</span><span class="sxs-lookup"><span data-stu-id="748cd-249">Prefix = parameter name</span></span>

<span data-ttu-id="748cd-250">Bağlanacak model adlı bir parametre ise `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="748cd-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="748cd-251">Model bağlama, anahtar kaynaklarına bakarak başlar `instructorToUpdate.ID` .</span><span class="sxs-lookup"><span data-stu-id="748cd-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="748cd-252">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="748cd-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="748cd-253">Önek = Özellik adı</span><span class="sxs-lookup"><span data-stu-id="748cd-253">Prefix = property name</span></span>

<span data-ttu-id="748cd-254">Bağlanacak model, `Instructor` denetleyicinin veya sınıfın adında bir özelliktir `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="748cd-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="748cd-255">Model bağlama, anahtar kaynaklarına bakarak başlar `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="748cd-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="748cd-256">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="748cd-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="748cd-257">Özel ön ek</span><span class="sxs-lookup"><span data-stu-id="748cd-257">Custom prefix</span></span>

<span data-ttu-id="748cd-258">Bağlanacak model adlı bir parametre ise `instructorToUpdate` ve bir `Bind` öznitelik `Instructor` önek olarak belirtirse:</span><span class="sxs-lookup"><span data-stu-id="748cd-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="748cd-259">Model bağlama, anahtar kaynaklarına bakarak başlar `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="748cd-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="748cd-260">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="748cd-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="748cd-261">Karmaşık tür hedefleri için öznitelikler</span><span class="sxs-lookup"><span data-stu-id="748cd-261">Attributes for complex type targets</span></span>

<span data-ttu-id="748cd-262">Karmaşık türlerin model bağlamasını denetlemek için birkaç yerleşik öznitelik mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="748cd-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="748cd-263">Bu öznitelikler, gönderilen form verileri değer kaynağı olduğunda model bağlamayı etkiler.</span><span class="sxs-lookup"><span data-stu-id="748cd-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="748cd-264">Bunlar, gönderilen JSON ve XML istek gövdelerini işleyen giriş formatlarını **etkilemez.**</span><span class="sxs-lookup"><span data-stu-id="748cd-264">They do \* **not** _ affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="748cd-265">Giriş biçimleri [Bu makalenin ilerleyen kısımlarında](#input-formatters)açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="748cd-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="748cd-266">[Bind] özniteliği</span><span class="sxs-lookup"><span data-stu-id="748cd-266">[Bind] attribute</span></span>

<span data-ttu-id="748cd-267">, Bir sınıfa veya yöntem parametresine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="748cd-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="748cd-268">Model bağlamasındaki bir modelin hangi özelliklerinin dahil edileceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="748cd-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="748cd-269">`[Bind]`Giriş _*_formatlarını etkilemez._*_</span><span class="sxs-lookup"><span data-stu-id="748cd-269">`[Bind]` does _*_not_*_ affect input formatters.</span></span>

<span data-ttu-id="748cd-270">Aşağıdaki örnekte, `Instructor` herhangi bir işleyici veya eylem yöntemi çağrıldığında yalnızca modelin belirtilen özellikleri bağlanır:</span><span class="sxs-lookup"><span data-stu-id="748cd-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="748cd-271">Aşağıdaki örnekte, `Instructor` yöntemi çağrıldığında yalnızca modelin belirtilen özellikleri bağlanır `OnPost` :</span><span class="sxs-lookup"><span data-stu-id="748cd-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="748cd-272">`[Bind]`Özniteliği, _Create \* senaryolarında fazla nakline karşı korumak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="748cd-272">The `[Bind]` attribute can be used to protect against overposting in _create\* scenarios.</span></span> <span data-ttu-id="748cd-273">Dışlanan Özellikler null ya da boş değer olarak ayarlandığı için, düzenleme senaryolarında iyi çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="748cd-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="748cd-274">Fazla naklin savunma için, öznitelik yerine görüntüleme modelleri önerilir `[Bind]` .</span><span class="sxs-lookup"><span data-stu-id="748cd-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="748cd-275">Daha fazla bilgi için bkz. fazla [nakil hakkında güvenlik NOI](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="748cd-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="modelbinder-attribute"></a><span data-ttu-id="748cd-276">[Modelciltçi] özniteliği</span><span class="sxs-lookup"><span data-stu-id="748cd-276">[ModelBinder] attribute</span></span>

<span data-ttu-id="748cd-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> türlere, özelliklere veya parametrelere uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="748cd-277"><xref:Microsoft.AspNetCore.Mvc.ModelBinderAttribute> can be applied to types, properties, or parameters.</span></span> <span data-ttu-id="748cd-278">Belirli örneği veya türü bağlamak için kullanılan model Bağlayıcısı türünü belirtmeyi sağlar.</span><span class="sxs-lookup"><span data-stu-id="748cd-278">It allows specifying the type of model binder used to bind the specific instance or type.</span></span> <span data-ttu-id="748cd-279">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="748cd-279">For example:</span></span>

```C#
[HttpPost]
public IActionResult OnPost([ModelBinder(typeof(MyInstructorModelBinder))] Instructor instructor)
```

<span data-ttu-id="748cd-280">`[ModelBinder]`Öznitelik, model bağlandığında bir özelliğin veya parametrenin adını değiştirmek için de kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="748cd-280">The `[ModelBinder]` attribute can also be used to change the name of a property or parameter when it's being model bound:</span></span>

```C#
public class Instructor
{
    [ModelBinder(Name = "instructor_id")]
    public string Id { get; set; }
    
    public string Name { get; set; }
}
```

### <a name="bindrequired-attribute"></a><span data-ttu-id="748cd-281">[BindRequired] özniteliği</span><span class="sxs-lookup"><span data-stu-id="748cd-281">[BindRequired] attribute</span></span>

<span data-ttu-id="748cd-282">, Yöntem parametrelerine değil yalnızca model özelliklerine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="748cd-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="748cd-283">Modelin özelliği için bağlama gerçekleşmemişse model bağlamasının model durumu hatası eklemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="748cd-283">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="748cd-284">İşte bir örnek:</span><span class="sxs-lookup"><span data-stu-id="748cd-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="748cd-285">Ayrıca bkz `[Required]` . [model doğrulama](xref:mvc/models/validation#required-attribute)içindeki öznitelik tartışması.</span><span class="sxs-lookup"><span data-stu-id="748cd-285">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="748cd-286">[Bindhiç] özniteliği</span><span class="sxs-lookup"><span data-stu-id="748cd-286">[BindNever] attribute</span></span>

<span data-ttu-id="748cd-287">, Yöntem parametrelerine değil yalnızca model özelliklerine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="748cd-287">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="748cd-288">Model bağlamasının model özelliğini değiştirmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="748cd-288">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="748cd-289">İşte bir örnek:</span><span class="sxs-lookup"><span data-stu-id="748cd-289">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="748cd-290">Koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="748cd-290">Collections</span></span>

<span data-ttu-id="748cd-291">Basit türlerin koleksiyonları olan hedefler için model bağlama, *parameter_name* veya *property_name* ile eşleşmeleri arar.</span><span class="sxs-lookup"><span data-stu-id="748cd-291">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="748cd-292">Eşleşme bulunmazsa, ön ek olmadan desteklenen biçimlerden birini arar.</span><span class="sxs-lookup"><span data-stu-id="748cd-292">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="748cd-293">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="748cd-293">For example:</span></span>

* <span data-ttu-id="748cd-294">Bağlanacak parametrenin adlı bir dizi olduğunu varsayalım `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="748cd-294">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="748cd-295">Form veya sorgu dizesi verileri aşağıdaki biçimlerden birinde olabilir:</span><span class="sxs-lookup"><span data-stu-id="748cd-295">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="748cd-296">Aşağıdaki biçim yalnızca form verilerinde desteklenir:</span><span class="sxs-lookup"><span data-stu-id="748cd-296">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="748cd-297">Önceki örnek biçimlerinin hepsi için model bağlama iki öğe dizisini `selectedCourses` parametreye geçirir:</span><span class="sxs-lookup"><span data-stu-id="748cd-297">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="748cd-298">Selectedkurslar [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="748cd-298">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="748cd-299">Selectedkurslar [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="748cd-299">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="748cd-300">Alt simge numaralarını kullanan veri biçimleri (... [0]... [1]...) sıfırdan başlayarak sıralı olarak numaralandırıldıklarından emin olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="748cd-300">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="748cd-301">Alt simge numaralandırmasında boşluk varsa, boşluklardan sonraki tüm öğeler yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="748cd-301">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="748cd-302">Örneğin, alt simgeler 0 ve 1 yerine 0 ve 2 ise ikinci öğe yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="748cd-302">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="748cd-303">Sözlükler</span><span class="sxs-lookup"><span data-stu-id="748cd-303">Dictionaries</span></span>

<span data-ttu-id="748cd-304">`Dictionary`Hedefler için, model bağlama *parameter_name* veya *property_name* eşleşme arar.</span><span class="sxs-lookup"><span data-stu-id="748cd-304">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="748cd-305">Eşleşme bulunmazsa, ön ek olmadan desteklenen biçimlerden birini arar.</span><span class="sxs-lookup"><span data-stu-id="748cd-305">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="748cd-306">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="748cd-306">For example:</span></span>

* <span data-ttu-id="748cd-307">Hedef parametrenin bir adlandırılmış olduğunu varsayalım `Dictionary<int, string>` `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="748cd-307">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="748cd-308">Postalanan form veya sorgu dizesi verileri aşağıdaki örneklerden birine benzeyebilir:</span><span class="sxs-lookup"><span data-stu-id="748cd-308">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="748cd-309">Önceki örnek biçimlerinin hepsi için model bağlama iki öğenin bir sözlüğünü `selectedCourses` parametreye geçirir:</span><span class="sxs-lookup"><span data-stu-id="748cd-309">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="748cd-310">Selectedkurslar ["1050"] = "Chemistry"</span><span class="sxs-lookup"><span data-stu-id="748cd-310">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="748cd-311">Selectedkurslar ["2000"] = "Ekonomiks"</span><span class="sxs-lookup"><span data-stu-id="748cd-311">selectedCourses["2000"]="Economics"</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a><span data-ttu-id="748cd-312">Oluşturucu bağlama ve kayıt türleri</span><span class="sxs-lookup"><span data-stu-id="748cd-312">Constructor binding and record types</span></span>

<span data-ttu-id="748cd-313">Model bağlama, karmaşık türlerin parametresiz bir oluşturucuya sahip olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="748cd-313">Model binding requires that complex types have a parameterless constructor.</span></span> <span data-ttu-id="748cd-314">Hem hem de `System.Text.Json` `Newtonsoft.Json` tabanlı giriş formatlayıcıları parametresiz oluşturucusu olmayan sınıfların serisini kaldırma desteği.</span><span class="sxs-lookup"><span data-stu-id="748cd-314">Both `System.Text.Json` and `Newtonsoft.Json` based input formatters support deserialization of classes that don't have a parameterless constructor.</span></span> 

<span data-ttu-id="748cd-315">C# 9, succinctly ağ üzerinden verileri temsil etmenin harika bir yolu olan kayıt türlerini tanıtır.</span><span class="sxs-lookup"><span data-stu-id="748cd-315">C# 9 introduces record types, which are a great way to succinctly represent data over the network.</span></span> <span data-ttu-id="748cd-316">ASP.NET Core, tek bir Oluşturucu ile model bağlama ve kayıt türlerini doğrulama desteği ekler:</span><span class="sxs-lookup"><span data-stu-id="748cd-316">ASP.NET Core adds support for model binding and validating record types with a single constructor:</span></span>

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

<span data-ttu-id="748cd-317">`Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="748cd-317">`Person/Index.cshtml`:</span></span>

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

<span data-ttu-id="748cd-318">Kayıt türleri doğrulanırken, çalışma zamanı, doğrulama meta verilerini özellikle özellikler yerine parametreler üzerinde arar.</span><span class="sxs-lookup"><span data-stu-id="748cd-318">When validating record types, the runtime searches for validation metadata specifically on parameters rather than on properties.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="748cd-319">Model bağlama yolu verileri ve sorgu dizeleri için Genelleştirme davranışı</span><span class="sxs-lookup"><span data-stu-id="748cd-319">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="748cd-320">ASP.NET Core yol değeri sağlayıcısı ve sorgu dizesi değer sağlayıcısı:</span><span class="sxs-lookup"><span data-stu-id="748cd-320">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="748cd-321">Değerleri sabit kültür olarak değerlendirin.</span><span class="sxs-lookup"><span data-stu-id="748cd-321">Treat values as invariant culture.</span></span>
* <span data-ttu-id="748cd-322">URL 'Lerin kültür sabiti olmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="748cd-322">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="748cd-323">Buna karşılık, form verilerinden gelen değerler kültüre duyarlı bir dönüştürmeye gider.</span><span class="sxs-lookup"><span data-stu-id="748cd-323">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="748cd-324">Bu, URL 'Lerin yerel ayarlarda paylaşılabilir olması için tasarımdır.</span><span class="sxs-lookup"><span data-stu-id="748cd-324">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="748cd-325">ASP.NET Core yol değeri sağlayıcısını ve sorgu dizesi değeri sağlayıcısını, kültüre duyarlı bir dönüşüme dönüştürmek için:</span><span class="sxs-lookup"><span data-stu-id="748cd-325">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="748cd-326">Şuradan devralma <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="748cd-326">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="748cd-327">[QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) veya [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs) adresinden kodu kopyalayın</span><span class="sxs-lookup"><span data-stu-id="748cd-327">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="748cd-328">Değer sağlayıcısı oluşturucusuna geçirilen [kültür değerini](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture) ile değiştirin</span><span class="sxs-lookup"><span data-stu-id="748cd-328">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="748cd-329">MVC seçeneklerinde varsayılan değer sağlayıcı fabrikasını yeni bir değerle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="748cd-329">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="748cd-330">Özel veri türleri</span><span class="sxs-lookup"><span data-stu-id="748cd-330">Special data types</span></span>

<span data-ttu-id="748cd-331">Model bağlamanın işleyebileceği bazı özel veri türleri vardır.</span><span class="sxs-lookup"><span data-stu-id="748cd-331">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="748cd-332">Iformfile ve ıformfilecollection</span><span class="sxs-lookup"><span data-stu-id="748cd-332">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="748cd-333">HTTP isteğine eklenen karşıya yüklenen dosya.</span><span class="sxs-lookup"><span data-stu-id="748cd-333">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="748cd-334">Ayrıca, `IEnumerable<IFormFile>` birden çok dosya için de desteklenir.</span><span class="sxs-lookup"><span data-stu-id="748cd-334">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="748cd-335">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="748cd-335">CancellationToken</span></span>

<span data-ttu-id="748cd-336">Zaman uyumsuz denetleyicilerde etkinliği iptal etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="748cd-336">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="748cd-337">Form koleksiyonu</span><span class="sxs-lookup"><span data-stu-id="748cd-337">FormCollection</span></span>

<span data-ttu-id="748cd-338">Postalanan form verilerinden tüm değerleri almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="748cd-338">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="748cd-339">Giriş biçimleri</span><span class="sxs-lookup"><span data-stu-id="748cd-339">Input formatters</span></span>

<span data-ttu-id="748cd-340">İstek gövdesindeki veriler JSON, XML veya başka bir biçimde olabilir.</span><span class="sxs-lookup"><span data-stu-id="748cd-340">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="748cd-341">Model bağlama, bu verileri ayrıştırmak için belirli bir içerik türünü işlemek üzere yapılandırılmış bir *giriş biçimlendiricisi* kullanır.</span><span class="sxs-lookup"><span data-stu-id="748cd-341">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="748cd-342">Varsayılan olarak, ASP.NET Core JSON verilerini işlemek için JSON tabanlı giriş formatlayıcıları içerir.</span><span class="sxs-lookup"><span data-stu-id="748cd-342">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="748cd-343">Diğer içerik türleri için başka biçim ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="748cd-343">You can add other formatters for other content types.</span></span>

<span data-ttu-id="748cd-344">ASP.NET Core, [tüketen](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) özniteliğine bağlı olarak giriş formatlayıcıları seçer.</span><span class="sxs-lookup"><span data-stu-id="748cd-344">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="748cd-345">Hiçbir öznitelik yoksa, [Content-Type üst bilgisini](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)kullanır.</span><span class="sxs-lookup"><span data-stu-id="748cd-345">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="748cd-346">Yerleşik XML girişi formatlayıcıları 'nı kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="748cd-346">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="748cd-347">`Microsoft.AspNetCore.Mvc.Formatters.Xml`NuGet paketini yükler.</span><span class="sxs-lookup"><span data-stu-id="748cd-347">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="748cd-348">İçinde `Startup.ConfigureServices` , veya öğesini çağırın <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> .</span><span class="sxs-lookup"><span data-stu-id="748cd-348">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="748cd-349">Özniteliği, `Consumes` istek GÖVDESINDE XML beklemeniz gereken denetleyici sınıflarına veya eylem yöntemlerine uygulayın.</span><span class="sxs-lookup"><span data-stu-id="748cd-349">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="748cd-350">Daha fazla bilgi için bkz. [XML serileştirme tanıtımı](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="748cd-350">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="748cd-351">Giriş formatlayıcıları ile model bağlamayı özelleştirme</span><span class="sxs-lookup"><span data-stu-id="748cd-351">Customize model binding with input formatters</span></span>

<span data-ttu-id="748cd-352">Giriş biçimlendiricisi, istek gövdesinden veri okumak için tam sorumluluğa sahiptir.</span><span class="sxs-lookup"><span data-stu-id="748cd-352">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="748cd-353">Bu işlemi özelleştirmek için, giriş biçimlendirici tarafından kullanılan API 'Leri yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="748cd-353">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="748cd-354">Bu bölümde, `System.Text.Json` adlı özel bir türü anlamak için tabanlı giriş biçimlendiricinin nasıl özelleştirileceği açıklanmaktadır `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="748cd-354">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="748cd-355">Adlı özel bir özellik içeren aşağıdaki modeli göz önünde bulundurun `ObjectId` `Id` :</span><span class="sxs-lookup"><span data-stu-id="748cd-355">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="748cd-356">Kullanırken model bağlama işlemini özelleştirmek için `System.Text.Json` , öğesinden türetilmiş bir sınıf oluşturun <xref:System.Text.Json.Serialization.JsonConverter%601> :</span><span class="sxs-lookup"><span data-stu-id="748cd-356">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="748cd-357">Özel bir dönüştürücü kullanmak için, <xref:System.Text.Json.Serialization.JsonConverterAttribute> türüne özniteliğini uygulayın.</span><span class="sxs-lookup"><span data-stu-id="748cd-357">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="748cd-358">Aşağıdaki örnekte, `ObjectId` türü `ObjectIdConverter` özel dönüştürücü olarak ile yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="748cd-358">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="748cd-359">Daha fazla bilgi için bkz. [özel dönüştürücüler yazma](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="748cd-359">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="748cd-360">Belirtilen türleri model bağlamalarından Dışla</span><span class="sxs-lookup"><span data-stu-id="748cd-360">Exclude specified types from model binding</span></span>

<span data-ttu-id="748cd-361">Model bağlama ve doğrulama sistemlerinin davranışı [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata)tarafından yönlendiriliyor.</span><span class="sxs-lookup"><span data-stu-id="748cd-361">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="748cd-362">`ModelMetadata` [Mvcoptions. modelmetadatadetails sağlayıcılarına](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders)bir ayrıntı sağlayıcısı ekleyerek özelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="748cd-362">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="748cd-363">Yerleşik Ayrıntılar sağlayıcıları, belirtilen türler için model bağlamayı veya doğrulamayı devre dışı bırakmak üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="748cd-363">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="748cd-364">Belirtilen türdeki tüm modellerdeki model bağlamayı devre dışı bırakmak için içine bir ekleyin <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="748cd-364">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="748cd-365">Örneğin, türü tüm modeller üzerinde model bağlamayı devre dışı bırakmak için `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="748cd-365">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="748cd-366">Belirtilen türdeki özelliklerde doğrulamayı devre dışı bırakmak için içine bir ekleyin <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="748cd-366">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="748cd-367">Örneğin, türündeki özelliklerde doğrulamayı devre dışı bırakmak için `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="748cd-367">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="748cd-368">Özel model ciltleri</span><span class="sxs-lookup"><span data-stu-id="748cd-368">Custom model binders</span></span>

<span data-ttu-id="748cd-369">Özel bir model cildi yazarak ve `[ModelBinder]` belirli bir hedef için seçmek üzere özniteliğini kullanarak model bağlamayı genişletebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="748cd-369">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="748cd-370">[Özel model bağlama](xref:mvc/advanced/custom-model-binding)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="748cd-370">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="748cd-371">El ile model bağlama</span><span class="sxs-lookup"><span data-stu-id="748cd-371">Manual model binding</span></span> 

<span data-ttu-id="748cd-372">Model bağlama yöntemi kullanılarak el ile çağrılabilir <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> .</span><span class="sxs-lookup"><span data-stu-id="748cd-372">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="748cd-373">Yöntemi hem hem de sınıflarında tanımlanmıştır `ControllerBase` `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="748cd-373">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="748cd-374">Yöntem aşırı yüklemeleri, kullanılacak öneki ve değer sağlayıcısını belirtmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="748cd-374">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="748cd-375">Yöntemi `false` model bağlamanın başarısız olup olmadığını döndürür.</span><span class="sxs-lookup"><span data-stu-id="748cd-375">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="748cd-376">İşte bir örnek:</span><span class="sxs-lookup"><span data-stu-id="748cd-376">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="748cd-377"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  , form gövdesinden, sorgu dizesinden ve veri yönlendirme verilerinden veri almak için değer sağlayıcılarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="748cd-377"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="748cd-378">`TryUpdateModelAsync` genellikle:</span><span class="sxs-lookup"><span data-stu-id="748cd-378">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="748cd-379">Daha :::no-loc(Razor)::: fazla nakletmeyi engellemek için denetleyiciler ve görünümler kullanan sayfalarla ve MVC uygulamalarıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="748cd-379">Used with :::no-loc(Razor)::: Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="748cd-380">Form verilerinden, sorgu dizelerinden ve veri rotalarından tüketilmediği müddetçe bir Web API 'SI ile kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="748cd-380">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="748cd-381">JSON kullanan Web API uç noktaları, istek gövdesini bir nesne olarak seri durumdan çıkarmak için [giriş formatlarını](#input-formatters) kullanır.</span><span class="sxs-lookup"><span data-stu-id="748cd-381">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="748cd-382">Daha fazla bilgi için bkz. [Tryupdatemodelasync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="748cd-382">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="748cd-383">[FromServices] özniteliği</span><span class="sxs-lookup"><span data-stu-id="748cd-383">[FromServices] attribute</span></span>

<span data-ttu-id="748cd-384">Bu özniteliğin adı, bir veri kaynağı belirten model bağlama özniteliklerinin düzeniyle uyar.</span><span class="sxs-lookup"><span data-stu-id="748cd-384">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="748cd-385">Ancak bir değer sağlayıcısından veri bağlama hakkında bilgi yoktur.</span><span class="sxs-lookup"><span data-stu-id="748cd-385">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="748cd-386">[Bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısından bir türün bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="748cd-386">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="748cd-387">Amacı, yalnızca belirli bir yöntem çağrılırsa bir hizmete ihtiyacınız olduğunda Oluşturucu ekleme için bir alternatif sağlamaktır.</span><span class="sxs-lookup"><span data-stu-id="748cd-387">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="748cd-388">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="748cd-388">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="748cd-389">Bu makalede, model bağlamanın ne olduğu, nasıl çalıştığı ve davranışını nasıl özelleştireceğiniz açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="748cd-389">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="748cd-390">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="748cd-390">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="748cd-391">Model bağlama nedir?</span><span class="sxs-lookup"><span data-stu-id="748cd-391">What is Model binding</span></span>

<span data-ttu-id="748cd-392">Denetleyiciler ve :::no-loc(Razor)::: Sayfalar, http isteklerinden gelen verilerle çalışır.</span><span class="sxs-lookup"><span data-stu-id="748cd-392">Controllers and :::no-loc(Razor)::: pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="748cd-393">Örneğin, rota verileri bir kayıt anahtarı sağlayabilir ve postalanan form alanları, modelin özelliklerine ilişkin değerler sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="748cd-393">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="748cd-394">Bu değerlerin her birini almak ve bunları dizelerden .NET türlerine dönüştürmek için kod yazma sıkıcı ve hata durumunda olabilir.</span><span class="sxs-lookup"><span data-stu-id="748cd-394">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="748cd-395">Model bağlama bu işlemi otomatikleştirir.</span><span class="sxs-lookup"><span data-stu-id="748cd-395">Model binding automates this process.</span></span> <span data-ttu-id="748cd-396">Model bağlama sistemi:</span><span class="sxs-lookup"><span data-stu-id="748cd-396">The model binding system:</span></span>

* <span data-ttu-id="748cd-397">Veri yolu, form alanları ve sorgu dizeleri gibi çeşitli kaynaklardan veri alır.</span><span class="sxs-lookup"><span data-stu-id="748cd-397">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="748cd-398">:::no-loc(Razor):::Yöntem parametreleri ve ortak özellikler içindeki denetleyicilere ve sayfalara verileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="748cd-398">Provides the data to controllers and :::no-loc(Razor)::: pages in method parameters and public properties.</span></span>
* <span data-ttu-id="748cd-399">Dize verilerini .NET türlerine dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="748cd-399">Converts string data to .NET types.</span></span>
* <span data-ttu-id="748cd-400">Karmaşık türlerin özelliklerini güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="748cd-400">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="748cd-401">Örnek</span><span class="sxs-lookup"><span data-stu-id="748cd-401">Example</span></span>

<span data-ttu-id="748cd-402">Aşağıdaki eylem yöntemine sahip olduğunuzu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="748cd-402">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="748cd-403">Ve uygulama şu URL ile bir istek alıyor:</span><span class="sxs-lookup"><span data-stu-id="748cd-403">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="748cd-404">Model bağlama, yönlendirme sistemi eylem yöntemini seçtikten sonra aşağıdaki adımlardan geçer:</span><span class="sxs-lookup"><span data-stu-id="748cd-404">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="748cd-405">Adlı bir tamsayı olan ilk parametresini bulur `GetByID` `id` .</span><span class="sxs-lookup"><span data-stu-id="748cd-405">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="748cd-406">HTTP isteğindeki kullanılabilir kaynakları arar ve `id` route verilerinde = "2" bulur.</span><span class="sxs-lookup"><span data-stu-id="748cd-406">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="748cd-407">"2" dizesini tamsayı 2 ' ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="748cd-407">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="748cd-408">`GetByID`Adlı bir Boole değeri olan bir sonraki parametresini bulur `dogsOnly` .</span><span class="sxs-lookup"><span data-stu-id="748cd-408">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="748cd-409">Kaynakları arar ve sorgu dizesinde "DogsOnly = true" bulur.</span><span class="sxs-lookup"><span data-stu-id="748cd-409">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="748cd-410">Ad eşleştirme, büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="748cd-410">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="748cd-411">"True" dizesini Boole değerine dönüştürür `true` .</span><span class="sxs-lookup"><span data-stu-id="748cd-411">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="748cd-412">Framework daha sonra yöntemi çağırır `GetById` , parametresi için 2 ' ye geçerek `id` ve `true` `dogsOnly` parametresi için.</span><span class="sxs-lookup"><span data-stu-id="748cd-412">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="748cd-413">Önceki örnekte, model bağlama hedefleri basit türler olan yöntem parametreleridir.</span><span class="sxs-lookup"><span data-stu-id="748cd-413">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="748cd-414">Hedefler, karmaşık bir türün özellikleri de olabilir.</span><span class="sxs-lookup"><span data-stu-id="748cd-414">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="748cd-415">Her bir özellik başarıyla bağlandıktan sonra, bu özellik için [model doğrulaması](xref:mvc/models/validation) oluşur.</span><span class="sxs-lookup"><span data-stu-id="748cd-415">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="748cd-416">Hangi verilerin modele bağladığına ve tüm bağlama veya doğrulama hatalarıyla ilgili kayıt, [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) veya [Pagemodel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState)içinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="748cd-416">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="748cd-417">Bu işlemin başarılı olup olmadığını öğrenmek için uygulama [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) bayrağını denetler.</span><span class="sxs-lookup"><span data-stu-id="748cd-417">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="748cd-418">Targets</span><span class="sxs-lookup"><span data-stu-id="748cd-418">Targets</span></span>

<span data-ttu-id="748cd-419">Model bağlama, aşağıdaki tür hedeflerin değerlerini bulmayı dener:</span><span class="sxs-lookup"><span data-stu-id="748cd-419">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="748cd-420">Bir isteğin yönlendirildiği denetleyici eylemi yönteminin parametreleri.</span><span class="sxs-lookup"><span data-stu-id="748cd-420">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="748cd-421">:::no-loc(Razor):::Bir isteğin yönlendirildiği sayfa işleyicisi yönteminin parametreleri.</span><span class="sxs-lookup"><span data-stu-id="748cd-421">Parameters of the :::no-loc(Razor)::: Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="748cd-422">Bir denetleyicinin veya `PageModel` sınıfın öznitelikleri tarafından belirtilmişse ortak özellikleri.</span><span class="sxs-lookup"><span data-stu-id="748cd-422">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="748cd-423">[BindProperty] özniteliği</span><span class="sxs-lookup"><span data-stu-id="748cd-423">[BindProperty] attribute</span></span>

<span data-ttu-id="748cd-424">, `PageModel` Model bağlamasının bu özelliği hedeflemesini sağlamak için bir denetleyicinin veya sınıfın ortak özelliğine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="748cd-424">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="748cd-425">[BindProperties] özniteliği</span><span class="sxs-lookup"><span data-stu-id="748cd-425">[BindProperties] attribute</span></span>

<span data-ttu-id="748cd-426">ASP.NET Core 2,1 ve üzeri sürümlerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="748cd-426">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="748cd-427">`PageModel`Model bağlamaya, sınıfın tüm ortak özelliklerini hedeflemesini bildirmek için bir denetleyiciye veya sınıfa uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="748cd-427">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="748cd-428">HTTP GET istekleri için model bağlama</span><span class="sxs-lookup"><span data-stu-id="748cd-428">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="748cd-429">Varsayılan olarak, Özellikler HTTP GET istekleri için bağlantılı değildir.</span><span class="sxs-lookup"><span data-stu-id="748cd-429">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="748cd-430">Genellikle, bir GET isteği için tüm ihtiyacınız olan bir kayıt KIMLIĞI parametresidir.</span><span class="sxs-lookup"><span data-stu-id="748cd-430">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="748cd-431">Kayıt KIMLIĞI, veritabanındaki öğeyi aramak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="748cd-431">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="748cd-432">Bu nedenle, modelin bir örneğini tutan bir özelliği bağlamaya gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="748cd-432">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="748cd-433">GET isteklerinden alınan özelliklerin verilerine bağlanmasını istediğiniz senaryolarda `SupportsGet` özelliği şu şekilde ayarlayın `true` :</span><span class="sxs-lookup"><span data-stu-id="748cd-433">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="748cd-434">Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="748cd-434">Sources</span></span>

<span data-ttu-id="748cd-435">Varsayılan olarak, model bağlama, bir HTTP isteğindeki aşağıdaki kaynaklardan gelen anahtar-değer çiftleri biçimindeki verileri alır:</span><span class="sxs-lookup"><span data-stu-id="748cd-435">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="748cd-436">Form alanları</span><span class="sxs-lookup"><span data-stu-id="748cd-436">Form fields</span></span>
1. <span data-ttu-id="748cd-437">İstek gövdesi ( [[ApiController] özniteliğine sahip denetleyiciler](xref:web-api/index#binding-source-parameter-inference)için.)</span><span class="sxs-lookup"><span data-stu-id="748cd-437">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="748cd-438">Veri yönlendirme</span><span class="sxs-lookup"><span data-stu-id="748cd-438">Route data</span></span>
1. <span data-ttu-id="748cd-439">Sorgu dizesi parametreleri</span><span class="sxs-lookup"><span data-stu-id="748cd-439">Query string parameters</span></span>
1. <span data-ttu-id="748cd-440">Karşıya yüklenen dosyalar</span><span class="sxs-lookup"><span data-stu-id="748cd-440">Uploaded files</span></span>

<span data-ttu-id="748cd-441">Her hedef parametresi veya özelliği için kaynaklar, önceki listede belirtilen sırada taranır.</span><span class="sxs-lookup"><span data-stu-id="748cd-441">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="748cd-442">Birkaç özel durum vardır:</span><span class="sxs-lookup"><span data-stu-id="748cd-442">There are a few exceptions:</span></span>

* <span data-ttu-id="748cd-443">Rota verileri ve sorgu dizesi değerleri yalnızca basit türler için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="748cd-443">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="748cd-444">Karşıya yüklenen dosyalar yalnızca veya uygulayan hedef türlere bağlanır `IFormFile` `IEnumerable<IFormFile>` .</span><span class="sxs-lookup"><span data-stu-id="748cd-444">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="748cd-445">Varsayılan kaynak doğru değilse, kaynağı belirtmek için aşağıdaki özniteliklerden birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="748cd-445">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="748cd-446">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Sorgu dizesinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="748cd-446">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="748cd-447">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Rota verilerinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="748cd-447">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="748cd-448">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Postalanan Form alanlarındaki değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="748cd-448">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="748cd-449">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -İstek gövdesinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="748cd-449">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="748cd-450">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -HTTP başlıklarındaki değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="748cd-450">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="748cd-451">Bu öznitelikler:</span><span class="sxs-lookup"><span data-stu-id="748cd-451">These attributes:</span></span>

* <span data-ttu-id="748cd-452">Model özelliklerine tek tek eklenir (model sınıfına değil), aşağıdaki örnekte olduğu gibi:</span><span class="sxs-lookup"><span data-stu-id="748cd-452">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="748cd-453">İsteğe bağlı olarak oluşturucuda bir model adı değeri kabul edin.</span><span class="sxs-lookup"><span data-stu-id="748cd-453">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="748cd-454">Bu seçenek, özellik adının istekteki değerle eşleşmemesi durumunda sağlanır.</span><span class="sxs-lookup"><span data-stu-id="748cd-454">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="748cd-455">Örneğin, istekteki değer aşağıdaki örnekte olduğu gibi adında bir tire olan bir üstbilgi olabilir:</span><span class="sxs-lookup"><span data-stu-id="748cd-455">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="748cd-456">[FromBody] özniteliği</span><span class="sxs-lookup"><span data-stu-id="748cd-456">[FromBody] attribute</span></span>

<span data-ttu-id="748cd-457">`[FromBody]`Özniteliği BIR http isteği gövdesinden doldurmak için bir parametreye özniteliğini uygulayın.</span><span class="sxs-lookup"><span data-stu-id="748cd-457">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="748cd-458">ASP.NET Core çalışma zamanı, gövdeyi bir giriş biçimlendirici 'ya okumaktan sorumlu olarak temsil eder.</span><span class="sxs-lookup"><span data-stu-id="748cd-458">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="748cd-459">Giriş biçimleri [Bu makalenin ilerleyen kısımlarında](#input-formatters)açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="748cd-459">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="748cd-460">`[FromBody]`Karmaşık bir tür parametresine uygulandığında, özelliklerine uygulanan herhangi bir bağlama kaynak özniteliği yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="748cd-460">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="748cd-461">Örneğin, aşağıdaki eylem, `Create` `pet` parametresinin gövdeden doldurulduğunu belirtir:</span><span class="sxs-lookup"><span data-stu-id="748cd-461">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="748cd-462">`Pet`Sınıfı, `Breed` özelliğinin bir sorgu dizesi parametresinden doldurulduğunu belirtir:</span><span class="sxs-lookup"><span data-stu-id="748cd-462">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="748cd-463">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="748cd-463">In the preceding example:</span></span>

* <span data-ttu-id="748cd-464">`[FromQuery]`Öznitelik yoksayıldı.</span><span class="sxs-lookup"><span data-stu-id="748cd-464">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="748cd-465">`Breed`Özellik bir sorgu dizesi parametresinden doldurulmamış.</span><span class="sxs-lookup"><span data-stu-id="748cd-465">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="748cd-466">Giriş biçimleri yalnızca gövdeyi okur ve bağlama kaynak özniteliklerini anlamayın.</span><span class="sxs-lookup"><span data-stu-id="748cd-466">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="748cd-467">Gövdede uygun bir değer bulunursa, bu değer özelliği doldurmak için kullanılır `Breed` .</span><span class="sxs-lookup"><span data-stu-id="748cd-467">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="748cd-468">`[FromBody]`Action yöntemi başına birden fazla parametreye uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="748cd-468">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="748cd-469">İstek akışı bir giriş biçimlendirici tarafından okunduktan sonra, diğer parametreleri bağlamak için artık bir daha okunamaz `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="748cd-469">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="748cd-470">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="748cd-470">Additional sources</span></span>

<span data-ttu-id="748cd-471">Kaynak verileri, model bağlama sistemine *değer sağlayıcılara* göre sağlanır.</span><span class="sxs-lookup"><span data-stu-id="748cd-471">Source data is provided to the model binding system by *value providers*.</span></span> <span data-ttu-id="748cd-472">Diğer kaynaklardan model bağlamaya yönelik verileri alan özel değer sağlayıcıları yazabilir ve kaydedebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="748cd-472">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="748cd-473">Örneğin, verilerin :::no-loc(cookie)::: veya oturum durumunun olmasını isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="748cd-473">For example, you might want data from :::no-loc(cookie):::s or session state.</span></span> <span data-ttu-id="748cd-474">Yeni bir kaynaktan veri almak için:</span><span class="sxs-lookup"><span data-stu-id="748cd-474">To get data from a new source:</span></span>

* <span data-ttu-id="748cd-475">Uygulayan bir sınıf oluşturun `IValueProvider` .</span><span class="sxs-lookup"><span data-stu-id="748cd-475">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="748cd-476">Uygulayan bir sınıf oluşturun `IValueProviderFactory` .</span><span class="sxs-lookup"><span data-stu-id="748cd-476">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="748cd-477">Fabrika sınıfını içine kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="748cd-477">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="748cd-478">Örnek uygulama, ' den değerler alan bir [değer sağlayıcısı](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) ve [Factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) örneği içerir :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="748cd-478">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) example that gets values from :::no-loc(cookie):::s.</span></span> <span data-ttu-id="748cd-479">Kayıt kodu aşağıda verilmiştir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="748cd-479">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="748cd-480">Gösterilen kod, tüm yerleşik değer sağlayıcılarından sonra özel değer sağlayıcısını koyar.</span><span class="sxs-lookup"><span data-stu-id="748cd-480">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="748cd-481">Bunu listede ilk yapmak için `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` yerine çağırın `Add` .</span><span class="sxs-lookup"><span data-stu-id="748cd-481">To make it the first in the list, call `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="748cd-482">Model özelliği için kaynak yok</span><span class="sxs-lookup"><span data-stu-id="748cd-482">No source for a model property</span></span>

<span data-ttu-id="748cd-483">Varsayılan olarak, model özelliği için bir değer bulunmazsa model durumu hatası oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="748cd-483">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="748cd-484">Özelliği null veya varsayılan bir değer olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="748cd-484">The property is set to null or a default value:</span></span>

* <span data-ttu-id="748cd-485">Null yapılabilir basit türler olarak ayarlanır `null` .</span><span class="sxs-lookup"><span data-stu-id="748cd-485">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="748cd-486">Null yapılamayan değer türleri olarak ayarlanır `default(T)` .</span><span class="sxs-lookup"><span data-stu-id="748cd-486">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="748cd-487">Örneğin, bir parametre `int id` 0 olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="748cd-487">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="748cd-488">Karmaşık türler için model bağlama, özellikleri ayarlamadan varsayılan oluşturucuyu kullanarak bir örnek oluşturur.</span><span class="sxs-lookup"><span data-stu-id="748cd-488">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="748cd-489">Diziler olarak ayarlanır `Array.Empty<T>()` , ancak `byte[]` diziler olarak ayarlanır `null` .</span><span class="sxs-lookup"><span data-stu-id="748cd-489">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="748cd-490">Model özelliği için form alanlarında hiçbir şey bulunamadığında model durumunun geçersiz kılınmalıdır, [`[BindRequired]`](#bindrequired-attribute) özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="748cd-490">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="748cd-491">Bu `[BindRequired]` davranışın, bir istek GÖVDESINDE JSON veya XML verilerine değil, postalanan form verilerinden model bağlama için geçerli olduğunu unutmayın.</span><span class="sxs-lookup"><span data-stu-id="748cd-491">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="748cd-492">İstek gövdesi verileri, [giriş formatlayıcıları](#input-formatters)tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="748cd-492">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="748cd-493">Tür dönüştürme hataları</span><span class="sxs-lookup"><span data-stu-id="748cd-493">Type conversion errors</span></span>

<span data-ttu-id="748cd-494">Bir kaynak bulunursa ancak hedef türe dönüştürülemiyorsa, model durumu geçersiz olarak işaretlenir.</span><span class="sxs-lookup"><span data-stu-id="748cd-494">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="748cd-495">Hedef parametresi veya özelliği, önceki bölümde belirtildiği gibi null veya varsayılan değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="748cd-495">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="748cd-496">Özniteliği olan bir API denetleyicisinde `[ApiController]` , geçersiz model durumu OTOMATIK HTTP 400 yanıtına neden olur.</span><span class="sxs-lookup"><span data-stu-id="748cd-496">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="748cd-497">Bir :::no-loc(Razor)::: sayfada, sayfayı bir hata iletisiyle yeniden görüntüleyin:</span><span class="sxs-lookup"><span data-stu-id="748cd-497">In a :::no-loc(Razor)::: page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="748cd-498">İstemci tarafı doğrulama, aksi takdirde bir sayfalar formuna gönderilemeyen hatalı verileri yakalar :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="748cd-498">Client-side validation catches most bad data that would otherwise be submitted to a :::no-loc(Razor)::: Pages form.</span></span> <span data-ttu-id="748cd-499">Bu doğrulama, önceki vurgulanmış kodu tetiklemeyi zorlaştırır.</span><span class="sxs-lookup"><span data-stu-id="748cd-499">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="748cd-500">Örnek uygulama, teslim **tarihi** alanına hatalı veri yerleştiren ve formu Gönderen **Geçersiz tarih içeren bir Gönder** düğmesi içerir.</span><span class="sxs-lookup"><span data-stu-id="748cd-500">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="748cd-501">Bu düğme, veri dönüştürme hataları oluştuğunda sayfanın yeniden görüntülenmesine yönelik kodun nasıl çalıştığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="748cd-501">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="748cd-502">Sayfa önceki kodla yeniden görüntülendiğinde, form alanında geçersiz giriş gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="748cd-502">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="748cd-503">Bunun nedeni model özelliğinin null ya da varsayılan bir değer olarak ayarlanmış olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="748cd-503">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="748cd-504">Geçersiz giriş bir hata iletisinde görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="748cd-504">The invalid input does appear in an error message.</span></span> <span data-ttu-id="748cd-505">Ancak form alanındaki hatalı verileri yeniden görüntülemek istiyorsanız, model özelliğini bir dize haline getirmeyi ve veri dönüştürmeyi el ile gerçekleştirmeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="748cd-505">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="748cd-506">Tür dönüştürme hatalarının model durumu hatalarına neden olmasını istemiyorsanız aynı strateji önerilir.</span><span class="sxs-lookup"><span data-stu-id="748cd-506">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="748cd-507">Bu durumda model özelliğini bir dize yapın.</span><span class="sxs-lookup"><span data-stu-id="748cd-507">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="748cd-508">Basit türler</span><span class="sxs-lookup"><span data-stu-id="748cd-508">Simple types</span></span>

<span data-ttu-id="748cd-509">Model cildin kaynak dizeleri dönüştürebileceğiniz basit türler aşağıdakileri içerir:</span><span class="sxs-lookup"><span data-stu-id="748cd-509">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="748cd-510">Boolean</span><span class="sxs-lookup"><span data-stu-id="748cd-510">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="748cd-511">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="748cd-511">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="748cd-512">Char</span><span class="sxs-lookup"><span data-stu-id="748cd-512">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="748cd-513">Tarih Saat</span><span class="sxs-lookup"><span data-stu-id="748cd-513">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="748cd-514">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="748cd-514">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="748cd-515">Kategori</span><span class="sxs-lookup"><span data-stu-id="748cd-515">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="748cd-516">Çift</span><span class="sxs-lookup"><span data-stu-id="748cd-516">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="748cd-517">Sabit listesi</span><span class="sxs-lookup"><span data-stu-id="748cd-517">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="748cd-518">'İni</span><span class="sxs-lookup"><span data-stu-id="748cd-518">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="748cd-519">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="748cd-519">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="748cd-520">Tek</span><span class="sxs-lookup"><span data-stu-id="748cd-520">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="748cd-521">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="748cd-521">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="748cd-522">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="748cd-522">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="748cd-523">Kullanılmamışsa</span><span class="sxs-lookup"><span data-stu-id="748cd-523">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="748cd-524">Sürüm</span><span class="sxs-lookup"><span data-stu-id="748cd-524">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="748cd-525">Karmaşık türler</span><span class="sxs-lookup"><span data-stu-id="748cd-525">Complex types</span></span>

<span data-ttu-id="748cd-526">Karmaşık bir türün bağlanması için ortak bir varsayılan Oluşturucusu ve ortak yazılabilir özellikleri olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="748cd-526">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="748cd-527">Model bağlama gerçekleştiğinde, sınıf ortak varsayılan Oluşturucu kullanılarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="748cd-527">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="748cd-528">Karmaşık türün her özelliği için model bağlama *prefix.property_name* ad deseninin kaynaklarını arar.</span><span class="sxs-lookup"><span data-stu-id="748cd-528">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name*.</span></span> <span data-ttu-id="748cd-529">Hiçbir şey bulunamazsa, ön ek olmadan yalnızca *property_name* arar.</span><span class="sxs-lookup"><span data-stu-id="748cd-529">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="748cd-530">Bir parametreye bağlama için, önek parametre adıdır.</span><span class="sxs-lookup"><span data-stu-id="748cd-530">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="748cd-531">Ortak özelliğe bağlama için `PageModel` , önek ortak özellik adıdır.</span><span class="sxs-lookup"><span data-stu-id="748cd-531">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="748cd-532">Bazı özniteliklerin `Prefix` , parametre veya özellik adının varsayılan kullanımını geçersiz kılabilmenizi sağlayan bir özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="748cd-532">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="748cd-533">Örneğin, karmaşık türün aşağıdaki sınıf olduğunu varsayalım `Instructor` :</span><span class="sxs-lookup"><span data-stu-id="748cd-533">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="748cd-534">Önek = parametre adı</span><span class="sxs-lookup"><span data-stu-id="748cd-534">Prefix = parameter name</span></span>

<span data-ttu-id="748cd-535">Bağlanacak model adlı bir parametre ise `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="748cd-535">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="748cd-536">Model bağlama, anahtar kaynaklarına bakarak başlar `instructorToUpdate.ID` .</span><span class="sxs-lookup"><span data-stu-id="748cd-536">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="748cd-537">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="748cd-537">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="748cd-538">Önek = Özellik adı</span><span class="sxs-lookup"><span data-stu-id="748cd-538">Prefix = property name</span></span>

<span data-ttu-id="748cd-539">Bağlanacak model, `Instructor` denetleyicinin veya sınıfın adında bir özelliktir `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="748cd-539">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="748cd-540">Model bağlama, anahtar kaynaklarına bakarak başlar `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="748cd-540">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="748cd-541">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="748cd-541">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="748cd-542">Özel ön ek</span><span class="sxs-lookup"><span data-stu-id="748cd-542">Custom prefix</span></span>

<span data-ttu-id="748cd-543">Bağlanacak model adlı bir parametre ise `instructorToUpdate` ve bir `Bind` öznitelik `Instructor` önek olarak belirtirse:</span><span class="sxs-lookup"><span data-stu-id="748cd-543">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="748cd-544">Model bağlama, anahtar kaynaklarına bakarak başlar `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="748cd-544">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="748cd-545">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="748cd-545">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="748cd-546">Karmaşık tür hedefleri için öznitelikler</span><span class="sxs-lookup"><span data-stu-id="748cd-546">Attributes for complex type targets</span></span>

<span data-ttu-id="748cd-547">Karmaşık türlerin model bağlamasını denetlemek için birkaç yerleşik öznitelik mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="748cd-547">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="748cd-548">Bu öznitelikler, gönderilen form verileri değer kaynağı olduğunda model bağlamayı etkiler.</span><span class="sxs-lookup"><span data-stu-id="748cd-548">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="748cd-549">Bunlar, gönderilen JSON ve XML istek gövdelerini işleyen giriş formatlayıcıları 'nı etkilemez.</span><span class="sxs-lookup"><span data-stu-id="748cd-549">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="748cd-550">Giriş biçimleri [Bu makalenin ilerleyen kısımlarında](#input-formatters)açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="748cd-550">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="748cd-551">Ayrıca bkz `[Required]` . [model doğrulama](xref:mvc/models/validation#required-attribute)içindeki öznitelik tartışması.</span><span class="sxs-lookup"><span data-stu-id="748cd-551">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="748cd-552">[BindRequired] özniteliği</span><span class="sxs-lookup"><span data-stu-id="748cd-552">[BindRequired] attribute</span></span>

<span data-ttu-id="748cd-553">, Yöntem parametrelerine değil yalnızca model özelliklerine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="748cd-553">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="748cd-554">Modelin özelliği için bağlama gerçekleşmemişse model bağlamasının model durumu hatası eklemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="748cd-554">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="748cd-555">İşte bir örnek:</span><span class="sxs-lookup"><span data-stu-id="748cd-555">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="748cd-556">[Bindhiç] özniteliği</span><span class="sxs-lookup"><span data-stu-id="748cd-556">[BindNever] attribute</span></span>

<span data-ttu-id="748cd-557">, Yöntem parametrelerine değil yalnızca model özelliklerine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="748cd-557">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="748cd-558">Model bağlamasının model özelliğini değiştirmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="748cd-558">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="748cd-559">İşte bir örnek:</span><span class="sxs-lookup"><span data-stu-id="748cd-559">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="748cd-560">[Bind] özniteliği</span><span class="sxs-lookup"><span data-stu-id="748cd-560">[Bind] attribute</span></span>

<span data-ttu-id="748cd-561">, Bir sınıfa veya yöntem parametresine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="748cd-561">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="748cd-562">Model bağlamasındaki bir modelin hangi özelliklerinin dahil edileceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="748cd-562">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="748cd-563">Aşağıdaki örnekte, `Instructor` herhangi bir işleyici veya eylem yöntemi çağrıldığında yalnızca modelin belirtilen özellikleri bağlanır:</span><span class="sxs-lookup"><span data-stu-id="748cd-563">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="748cd-564">Aşağıdaki örnekte, `Instructor` yöntemi çağrıldığında yalnızca modelin belirtilen özellikleri bağlanır `OnPost` :</span><span class="sxs-lookup"><span data-stu-id="748cd-564">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="748cd-565">`[Bind]`Özniteliği, *oluşturma* senaryolarında fazla nakline karşı korumak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="748cd-565">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="748cd-566">Dışlanan Özellikler null ya da boş değer olarak ayarlandığı için, düzenleme senaryolarında iyi çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="748cd-566">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="748cd-567">Fazla naklin savunma için, öznitelik yerine görüntüleme modelleri önerilir `[Bind]` .</span><span class="sxs-lookup"><span data-stu-id="748cd-567">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="748cd-568">Daha fazla bilgi için bkz. fazla [nakil hakkında güvenlik NOI](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="748cd-568">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="748cd-569">Koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="748cd-569">Collections</span></span>

<span data-ttu-id="748cd-570">Basit türlerin koleksiyonları olan hedefler için model bağlama, *parameter_name* veya *property_name* ile eşleşmeleri arar.</span><span class="sxs-lookup"><span data-stu-id="748cd-570">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="748cd-571">Eşleşme bulunmazsa, ön ek olmadan desteklenen biçimlerden birini arar.</span><span class="sxs-lookup"><span data-stu-id="748cd-571">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="748cd-572">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="748cd-572">For example:</span></span>

* <span data-ttu-id="748cd-573">Bağlanacak parametrenin adlı bir dizi olduğunu varsayalım `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="748cd-573">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="748cd-574">Form veya sorgu dizesi verileri aşağıdaki biçimlerden birinde olabilir:</span><span class="sxs-lookup"><span data-stu-id="748cd-574">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="748cd-575">Aşağıdaki biçim yalnızca form verilerinde desteklenir:</span><span class="sxs-lookup"><span data-stu-id="748cd-575">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="748cd-576">Önceki örnek biçimlerinin hepsi için model bağlama iki öğe dizisini `selectedCourses` parametreye geçirir:</span><span class="sxs-lookup"><span data-stu-id="748cd-576">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="748cd-577">Selectedkurslar [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="748cd-577">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="748cd-578">Selectedkurslar [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="748cd-578">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="748cd-579">Alt simge numaralarını kullanan veri biçimleri (... [0]... [1]...) sıfırdan başlayarak sıralı olarak numaralandırıldıklarından emin olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="748cd-579">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="748cd-580">Alt simge numaralandırmasında boşluk varsa, boşluklardan sonraki tüm öğeler yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="748cd-580">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="748cd-581">Örneğin, alt simgeler 0 ve 1 yerine 0 ve 2 ise ikinci öğe yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="748cd-581">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="748cd-582">Sözlükler</span><span class="sxs-lookup"><span data-stu-id="748cd-582">Dictionaries</span></span>

<span data-ttu-id="748cd-583">`Dictionary`Hedefler için, model bağlama *parameter_name* veya *property_name* eşleşme arar.</span><span class="sxs-lookup"><span data-stu-id="748cd-583">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name*.</span></span> <span data-ttu-id="748cd-584">Eşleşme bulunmazsa, ön ek olmadan desteklenen biçimlerden birini arar.</span><span class="sxs-lookup"><span data-stu-id="748cd-584">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="748cd-585">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="748cd-585">For example:</span></span>

* <span data-ttu-id="748cd-586">Hedef parametrenin bir adlandırılmış olduğunu varsayalım `Dictionary<int, string>` `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="748cd-586">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="748cd-587">Postalanan form veya sorgu dizesi verileri aşağıdaki örneklerden birine benzeyebilir:</span><span class="sxs-lookup"><span data-stu-id="748cd-587">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="748cd-588">Önceki örnek biçimlerinin hepsi için model bağlama iki öğenin bir sözlüğünü `selectedCourses` parametreye geçirir:</span><span class="sxs-lookup"><span data-stu-id="748cd-588">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="748cd-589">Selectedkurslar ["1050"] = "Chemistry"</span><span class="sxs-lookup"><span data-stu-id="748cd-589">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="748cd-590">Selectedkurslar ["2000"] = "Ekonomiks"</span><span class="sxs-lookup"><span data-stu-id="748cd-590">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="748cd-591">Model bağlama yolu verileri ve sorgu dizeleri için Genelleştirme davranışı</span><span class="sxs-lookup"><span data-stu-id="748cd-591">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="748cd-592">ASP.NET Core yol değeri sağlayıcısı ve sorgu dizesi değer sağlayıcısı:</span><span class="sxs-lookup"><span data-stu-id="748cd-592">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="748cd-593">Değerleri sabit kültür olarak değerlendirin.</span><span class="sxs-lookup"><span data-stu-id="748cd-593">Treat values as invariant culture.</span></span>
* <span data-ttu-id="748cd-594">URL 'Lerin kültür sabiti olmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="748cd-594">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="748cd-595">Buna karşılık, form verilerinden gelen değerler kültüre duyarlı bir dönüştürmeye gider.</span><span class="sxs-lookup"><span data-stu-id="748cd-595">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="748cd-596">Bu, URL 'Lerin yerel ayarlarda paylaşılabilir olması için tasarımdır.</span><span class="sxs-lookup"><span data-stu-id="748cd-596">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="748cd-597">ASP.NET Core yol değeri sağlayıcısını ve sorgu dizesi değeri sağlayıcısını, kültüre duyarlı bir dönüşüme dönüştürmek için:</span><span class="sxs-lookup"><span data-stu-id="748cd-597">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="748cd-598">Şuradan devralma <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="748cd-598">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="748cd-599">[QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) veya [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs) adresinden kodu kopyalayın</span><span class="sxs-lookup"><span data-stu-id="748cd-599">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="748cd-600">Değer sağlayıcısı oluşturucusuna geçirilen [kültür değerini](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture) ile değiştirin</span><span class="sxs-lookup"><span data-stu-id="748cd-600">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="748cd-601">MVC seçeneklerinde varsayılan değer sağlayıcı fabrikasını yeni bir değerle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="748cd-601">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="748cd-602">Özel veri türleri</span><span class="sxs-lookup"><span data-stu-id="748cd-602">Special data types</span></span>

<span data-ttu-id="748cd-603">Model bağlamanın işleyebileceği bazı özel veri türleri vardır.</span><span class="sxs-lookup"><span data-stu-id="748cd-603">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="748cd-604">Iformfile ve ıformfilecollection</span><span class="sxs-lookup"><span data-stu-id="748cd-604">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="748cd-605">HTTP isteğine eklenen karşıya yüklenen dosya.</span><span class="sxs-lookup"><span data-stu-id="748cd-605">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="748cd-606">Ayrıca, `IEnumerable<IFormFile>` birden çok dosya için de desteklenir.</span><span class="sxs-lookup"><span data-stu-id="748cd-606">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="748cd-607">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="748cd-607">CancellationToken</span></span>

<span data-ttu-id="748cd-608">Zaman uyumsuz denetleyicilerde etkinliği iptal etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="748cd-608">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="748cd-609">Form koleksiyonu</span><span class="sxs-lookup"><span data-stu-id="748cd-609">FormCollection</span></span>

<span data-ttu-id="748cd-610">Postalanan form verilerinden tüm değerleri almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="748cd-610">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="748cd-611">Giriş biçimleri</span><span class="sxs-lookup"><span data-stu-id="748cd-611">Input formatters</span></span>

<span data-ttu-id="748cd-612">İstek gövdesindeki veriler JSON, XML veya başka bir biçimde olabilir.</span><span class="sxs-lookup"><span data-stu-id="748cd-612">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="748cd-613">Model bağlama, bu verileri ayrıştırmak için belirli bir içerik türünü işlemek üzere yapılandırılmış bir *giriş biçimlendiricisi* kullanır.</span><span class="sxs-lookup"><span data-stu-id="748cd-613">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="748cd-614">Varsayılan olarak, ASP.NET Core JSON verilerini işlemek için JSON tabanlı giriş formatlayıcıları içerir.</span><span class="sxs-lookup"><span data-stu-id="748cd-614">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="748cd-615">Diğer içerik türleri için başka biçim ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="748cd-615">You can add other formatters for other content types.</span></span>

<span data-ttu-id="748cd-616">ASP.NET Core, [tüketen](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) özniteliğine bağlı olarak giriş formatlayıcıları seçer.</span><span class="sxs-lookup"><span data-stu-id="748cd-616">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="748cd-617">Hiçbir öznitelik yoksa, [Content-Type üst bilgisini](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)kullanır.</span><span class="sxs-lookup"><span data-stu-id="748cd-617">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="748cd-618">Yerleşik XML girişi formatlayıcıları 'nı kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="748cd-618">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="748cd-619">`Microsoft.AspNetCore.Mvc.Formatters.Xml`NuGet paketini yükler.</span><span class="sxs-lookup"><span data-stu-id="748cd-619">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="748cd-620">İçinde `Startup.ConfigureServices` , veya öğesini çağırın <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> .</span><span class="sxs-lookup"><span data-stu-id="748cd-620">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="748cd-621">Özniteliği, `Consumes` istek GÖVDESINDE XML beklemeniz gereken denetleyici sınıflarına veya eylem yöntemlerine uygulayın.</span><span class="sxs-lookup"><span data-stu-id="748cd-621">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="748cd-622">Daha fazla bilgi için bkz. [XML serileştirme tanıtımı](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="748cd-622">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="748cd-623">Belirtilen türleri model bağlamalarından Dışla</span><span class="sxs-lookup"><span data-stu-id="748cd-623">Exclude specified types from model binding</span></span>

<span data-ttu-id="748cd-624">Model bağlama ve doğrulama sistemlerinin davranışı [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata)tarafından yönlendiriliyor.</span><span class="sxs-lookup"><span data-stu-id="748cd-624">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="748cd-625">`ModelMetadata` [Mvcoptions. modelmetadatadetails sağlayıcılarına](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders)bir ayrıntı sağlayıcısı ekleyerek özelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="748cd-625">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="748cd-626">Yerleşik Ayrıntılar sağlayıcıları, belirtilen türler için model bağlamayı veya doğrulamayı devre dışı bırakmak üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="748cd-626">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="748cd-627">Belirtilen türdeki tüm modellerdeki model bağlamayı devre dışı bırakmak için içine bir ekleyin <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="748cd-627">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="748cd-628">Örneğin, türü tüm modeller üzerinde model bağlamayı devre dışı bırakmak için `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="748cd-628">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="748cd-629">Belirtilen türdeki özelliklerde doğrulamayı devre dışı bırakmak için içine bir ekleyin <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="748cd-629">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="748cd-630">Örneğin, türündeki özelliklerde doğrulamayı devre dışı bırakmak için `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="748cd-630">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="748cd-631">Özel model ciltleri</span><span class="sxs-lookup"><span data-stu-id="748cd-631">Custom model binders</span></span>

<span data-ttu-id="748cd-632">Özel bir model cildi yazarak ve `[ModelBinder]` belirli bir hedef için seçmek üzere özniteliğini kullanarak model bağlamayı genişletebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="748cd-632">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="748cd-633">[Özel model bağlama](xref:mvc/advanced/custom-model-binding)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="748cd-633">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="748cd-634">El ile model bağlama</span><span class="sxs-lookup"><span data-stu-id="748cd-634">Manual model binding</span></span>

<span data-ttu-id="748cd-635">Model bağlama yöntemi kullanılarak el ile çağrılabilir <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> .</span><span class="sxs-lookup"><span data-stu-id="748cd-635">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="748cd-636">Yöntemi hem hem de sınıflarında tanımlanmıştır `ControllerBase` `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="748cd-636">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="748cd-637">Yöntem aşırı yüklemeleri, kullanılacak öneki ve değer sağlayıcısını belirtmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="748cd-637">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="748cd-638">Yöntemi `false` model bağlamanın başarısız olup olmadığını döndürür.</span><span class="sxs-lookup"><span data-stu-id="748cd-638">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="748cd-639">İşte bir örnek:</span><span class="sxs-lookup"><span data-stu-id="748cd-639">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="748cd-640">[FromServices] özniteliği</span><span class="sxs-lookup"><span data-stu-id="748cd-640">[FromServices] attribute</span></span>

<span data-ttu-id="748cd-641">Bu özniteliğin adı, bir veri kaynağı belirten model bağlama özniteliklerinin düzeniyle uyar.</span><span class="sxs-lookup"><span data-stu-id="748cd-641">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="748cd-642">Ancak bir değer sağlayıcısından veri bağlama hakkında bilgi yoktur.</span><span class="sxs-lookup"><span data-stu-id="748cd-642">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="748cd-643">[Bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısından bir türün bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="748cd-643">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="748cd-644">Amacı, yalnızca belirli bir yöntem çağrılırsa bir hizmete ihtiyacınız olduğunda Oluşturucu ekleme için bir alternatif sağlamaktır.</span><span class="sxs-lookup"><span data-stu-id="748cd-644">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="748cd-645">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="748cd-645">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
