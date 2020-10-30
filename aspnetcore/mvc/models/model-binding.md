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
ms.openlocfilehash: a3be22134246c76b0a809ddb97b33ff97ace9a5b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057511"
---
# <a name="model-binding-in-aspnet-core"></a><span data-ttu-id="3e38a-103">ASP.NET Core 'de model bağlama</span><span class="sxs-lookup"><span data-stu-id="3e38a-103">Model Binding in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3e38a-104">Bu makalede, model bağlamanın ne olduğu, nasıl çalıştığı ve davranışını nasıl özelleştireceğiniz açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-104">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="3e38a-105">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3e38a-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="3e38a-106">Model bağlama nedir?</span><span class="sxs-lookup"><span data-stu-id="3e38a-106">What is Model binding</span></span>

<span data-ttu-id="3e38a-107">Denetleyiciler ve :::no-loc(Razor)::: Sayfalar, http isteklerinden gelen verilerle çalışır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-107">Controllers and :::no-loc(Razor)::: pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="3e38a-108">Örneğin, rota verileri bir kayıt anahtarı sağlayabilir ve postalanan form alanları, modelin özelliklerine ilişkin değerler sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-108">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="3e38a-109">Bu değerlerin her birini almak ve bunları dizelerden .NET türlerine dönüştürmek için kod yazma sıkıcı ve hata durumunda olabilir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-109">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="3e38a-110">Model bağlama bu işlemi otomatikleştirir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-110">Model binding automates this process.</span></span> <span data-ttu-id="3e38a-111">Model bağlama sistemi:</span><span class="sxs-lookup"><span data-stu-id="3e38a-111">The model binding system:</span></span>

* <span data-ttu-id="3e38a-112">Veri yolu, form alanları ve sorgu dizeleri gibi çeşitli kaynaklardan veri alır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-112">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="3e38a-113">:::no-loc(Razor):::Yöntem parametreleri ve ortak özellikler içindeki denetleyicilere ve sayfalara verileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-113">Provides the data to controllers and :::no-loc(Razor)::: pages in method parameters and public properties.</span></span>
* <span data-ttu-id="3e38a-114">Dize verilerini .NET türlerine dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="3e38a-114">Converts string data to .NET types.</span></span>
* <span data-ttu-id="3e38a-115">Karmaşık türlerin özelliklerini güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-115">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="3e38a-116">Örnek</span><span class="sxs-lookup"><span data-stu-id="3e38a-116">Example</span></span>

<span data-ttu-id="3e38a-117">Aşağıdaki eylem yöntemine sahip olduğunuzu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="3e38a-117">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="3e38a-118">Ve uygulama şu URL ile bir istek alıyor:</span><span class="sxs-lookup"><span data-stu-id="3e38a-118">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="3e38a-119">Model bağlama, yönlendirme sistemi eylem yöntemini seçtikten sonra aşağıdaki adımlardan geçer:</span><span class="sxs-lookup"><span data-stu-id="3e38a-119">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="3e38a-120">Adlı bir tamsayı olan ilk parametresini bulur `GetByID` `id` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-120">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="3e38a-121">HTTP isteğindeki kullanılabilir kaynakları arar ve `id` route verilerinde = "2" bulur.</span><span class="sxs-lookup"><span data-stu-id="3e38a-121">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="3e38a-122">"2" dizesini tamsayı 2 ' ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="3e38a-122">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="3e38a-123">`GetByID`Adlı bir Boole değeri olan bir sonraki parametresini bulur `dogsOnly` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-123">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="3e38a-124">Kaynakları arar ve sorgu dizesinde "DogsOnly = true" bulur.</span><span class="sxs-lookup"><span data-stu-id="3e38a-124">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="3e38a-125">Ad eşleştirme, büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-125">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="3e38a-126">"True" dizesini Boole değerine dönüştürür `true` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-126">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="3e38a-127">Framework daha sonra yöntemi çağırır `GetById` , parametresi için 2 ' ye geçerek `id` ve `true` `dogsOnly` parametresi için.</span><span class="sxs-lookup"><span data-stu-id="3e38a-127">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="3e38a-128">Önceki örnekte, model bağlama hedefleri basit türler olan yöntem parametreleridir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-128">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="3e38a-129">Hedefler, karmaşık bir türün özellikleri de olabilir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-129">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="3e38a-130">Her bir özellik başarıyla bağlandıktan sonra, bu özellik için [model doğrulaması](xref:mvc/models/validation) oluşur.</span><span class="sxs-lookup"><span data-stu-id="3e38a-130">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="3e38a-131">Hangi verilerin modele bağladığına ve tüm bağlama veya doğrulama hatalarıyla ilgili kayıt, [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) veya [Pagemodel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState)içinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-131">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="3e38a-132">Bu işlemin başarılı olup olmadığını öğrenmek için uygulama [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) bayrağını denetler.</span><span class="sxs-lookup"><span data-stu-id="3e38a-132">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="3e38a-133">Targets</span><span class="sxs-lookup"><span data-stu-id="3e38a-133">Targets</span></span>

<span data-ttu-id="3e38a-134">Model bağlama, aşağıdaki tür hedeflerin değerlerini bulmayı dener:</span><span class="sxs-lookup"><span data-stu-id="3e38a-134">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="3e38a-135">Bir isteğin yönlendirildiği denetleyici eylemi yönteminin parametreleri.</span><span class="sxs-lookup"><span data-stu-id="3e38a-135">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="3e38a-136">:::no-loc(Razor):::Bir isteğin yönlendirildiği sayfa işleyicisi yönteminin parametreleri.</span><span class="sxs-lookup"><span data-stu-id="3e38a-136">Parameters of the :::no-loc(Razor)::: Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="3e38a-137">Bir denetleyicinin veya `PageModel` sınıfın öznitelikleri tarafından belirtilmişse ortak özellikleri.</span><span class="sxs-lookup"><span data-stu-id="3e38a-137">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="3e38a-138">[BindProperty] özniteliği</span><span class="sxs-lookup"><span data-stu-id="3e38a-138">[BindProperty] attribute</span></span>

<span data-ttu-id="3e38a-139">, `PageModel` Model bağlamasının bu özelliği hedeflemesini sağlamak için bir denetleyicinin veya sınıfın ortak özelliğine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-139">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="3e38a-140">[BindProperties] özniteliği</span><span class="sxs-lookup"><span data-stu-id="3e38a-140">[BindProperties] attribute</span></span>

<span data-ttu-id="3e38a-141">ASP.NET Core 2,1 ve üzeri sürümlerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-141">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="3e38a-142">`PageModel`Model bağlamaya, sınıfın tüm ortak özelliklerini hedeflemesini bildirmek için bir denetleyiciye veya sınıfa uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-142">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="3e38a-143">HTTP GET istekleri için model bağlama</span><span class="sxs-lookup"><span data-stu-id="3e38a-143">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="3e38a-144">Varsayılan olarak, Özellikler HTTP GET istekleri için bağlantılı değildir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-144">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="3e38a-145">Genellikle, bir GET isteği için tüm ihtiyacınız olan bir kayıt KIMLIĞI parametresidir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-145">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="3e38a-146">Kayıt KIMLIĞI, veritabanındaki öğeyi aramak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-146">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="3e38a-147">Bu nedenle, modelin bir örneğini tutan bir özelliği bağlamaya gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="3e38a-147">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="3e38a-148">GET isteklerinden alınan özelliklerin verilerine bağlanmasını istediğiniz senaryolarda `SupportsGet` özelliği şu şekilde ayarlayın `true` :</span><span class="sxs-lookup"><span data-stu-id="3e38a-148">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="3e38a-149">Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3e38a-149">Sources</span></span>

<span data-ttu-id="3e38a-150">Varsayılan olarak, model bağlama, bir HTTP isteğindeki aşağıdaki kaynaklardan gelen anahtar-değer çiftleri biçimindeki verileri alır:</span><span class="sxs-lookup"><span data-stu-id="3e38a-150">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="3e38a-151">Form alanları</span><span class="sxs-lookup"><span data-stu-id="3e38a-151">Form fields</span></span>
1. <span data-ttu-id="3e38a-152">İstek gövdesi ( [[ApiController] özniteliğine sahip denetleyiciler](xref:web-api/index#binding-source-parameter-inference)için.)</span><span class="sxs-lookup"><span data-stu-id="3e38a-152">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="3e38a-153">Veri yönlendirme</span><span class="sxs-lookup"><span data-stu-id="3e38a-153">Route data</span></span>
1. <span data-ttu-id="3e38a-154">Sorgu dizesi parametreleri</span><span class="sxs-lookup"><span data-stu-id="3e38a-154">Query string parameters</span></span>
1. <span data-ttu-id="3e38a-155">Karşıya yüklenen dosyalar</span><span class="sxs-lookup"><span data-stu-id="3e38a-155">Uploaded files</span></span>

<span data-ttu-id="3e38a-156">Her hedef parametresi veya özelliği için kaynaklar, önceki listede belirtilen sırada taranır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-156">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="3e38a-157">Birkaç özel durum vardır:</span><span class="sxs-lookup"><span data-stu-id="3e38a-157">There are a few exceptions:</span></span>

* <span data-ttu-id="3e38a-158">Rota verileri ve sorgu dizesi değerleri yalnızca basit türler için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-158">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="3e38a-159">Karşıya yüklenen dosyalar yalnızca veya uygulayan hedef türlere bağlanır `IFormFile` `IEnumerable<IFormFile>` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-159">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="3e38a-160">Varsayılan kaynak doğru değilse, kaynağı belirtmek için aşağıdaki özniteliklerden birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="3e38a-160">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="3e38a-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Sorgu dizesinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-161">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="3e38a-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Rota verilerinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-162">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="3e38a-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Postalanan Form alanlarındaki değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-163">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="3e38a-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -İstek gövdesinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-164">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="3e38a-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -HTTP başlıklarındaki değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-165">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="3e38a-166">Bu öznitelikler:</span><span class="sxs-lookup"><span data-stu-id="3e38a-166">These attributes:</span></span>

* <span data-ttu-id="3e38a-167">Model özelliklerine tek tek eklenir (model sınıfına değil), aşağıdaki örnekte olduğu gibi:</span><span class="sxs-lookup"><span data-stu-id="3e38a-167">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="3e38a-168">İsteğe bağlı olarak oluşturucuda bir model adı değeri kabul edin.</span><span class="sxs-lookup"><span data-stu-id="3e38a-168">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="3e38a-169">Bu seçenek, özellik adının istekteki değerle eşleşmemesi durumunda sağlanır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-169">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="3e38a-170">Örneğin, istekteki değer aşağıdaki örnekte olduğu gibi adında bir tire olan bir üstbilgi olabilir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-170">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="3e38a-171">[FromBody] özniteliği</span><span class="sxs-lookup"><span data-stu-id="3e38a-171">[FromBody] attribute</span></span>

<span data-ttu-id="3e38a-172">`[FromBody]`Özniteliği BIR http isteği gövdesinden doldurmak için bir parametreye özniteliğini uygulayın.</span><span class="sxs-lookup"><span data-stu-id="3e38a-172">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="3e38a-173">ASP.NET Core çalışma zamanı, gövdeyi bir giriş biçimlendirici 'ya okumaktan sorumlu olarak temsil eder.</span><span class="sxs-lookup"><span data-stu-id="3e38a-173">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="3e38a-174">Giriş biçimleri [Bu makalenin ilerleyen kısımlarında](#input-formatters)açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-174">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="3e38a-175">`[FromBody]`Karmaşık bir tür parametresine uygulandığında, özelliklerine uygulanan herhangi bir bağlama kaynak özniteliği yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-175">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="3e38a-176">Örneğin, aşağıdaki eylem, `Create` `pet` parametresinin gövdeden doldurulduğunu belirtir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-176">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="3e38a-177">`Pet`Sınıfı, `Breed` özelliğinin bir sorgu dizesi parametresinden doldurulduğunu belirtir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-177">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="3e38a-178">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="3e38a-178">In the preceding example:</span></span>

* <span data-ttu-id="3e38a-179">`[FromQuery]`Öznitelik yoksayıldı.</span><span class="sxs-lookup"><span data-stu-id="3e38a-179">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="3e38a-180">`Breed`Özellik bir sorgu dizesi parametresinden doldurulmamış.</span><span class="sxs-lookup"><span data-stu-id="3e38a-180">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="3e38a-181">Giriş biçimleri yalnızca gövdeyi okur ve bağlama kaynak özniteliklerini anlamayın.</span><span class="sxs-lookup"><span data-stu-id="3e38a-181">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="3e38a-182">Gövdede uygun bir değer bulunursa, bu değer özelliği doldurmak için kullanılır `Breed` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-182">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="3e38a-183">`[FromBody]`Action yöntemi başına birden fazla parametreye uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="3e38a-183">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="3e38a-184">İstek akışı bir giriş biçimlendirici tarafından okunduktan sonra, diğer parametreleri bağlamak için artık bir daha okunamaz `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-184">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="3e38a-185">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3e38a-185">Additional sources</span></span>

<span data-ttu-id="3e38a-186">Kaynak verileri, model bağlama sistemine *değer sağlayıcılara* göre sağlanır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-186">Source data is provided to the model binding system by *value providers* .</span></span> <span data-ttu-id="3e38a-187">Diğer kaynaklardan model bağlamaya yönelik verileri alan özel değer sağlayıcıları yazabilir ve kaydedebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3e38a-187">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="3e38a-188">Örneğin, verilerin :::no-loc(cookie)::: veya oturum durumunun olmasını isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3e38a-188">For example, you might want data from :::no-loc(cookie):::s or session state.</span></span> <span data-ttu-id="3e38a-189">Yeni bir kaynaktan veri almak için:</span><span class="sxs-lookup"><span data-stu-id="3e38a-189">To get data from a new source:</span></span>

* <span data-ttu-id="3e38a-190">Uygulayan bir sınıf oluşturun `IValueProvider` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-190">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="3e38a-191">Uygulayan bir sınıf oluşturun `IValueProviderFactory` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-191">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="3e38a-192">Fabrika sınıfını içine kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-192">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="3e38a-193">Örnek uygulama, ' den değerler alan bir [değer sağlayıcısı](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) ve [Factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) örneği içerir :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="3e38a-193">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/3.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) example that gets values from :::no-loc(cookie):::s.</span></span> <span data-ttu-id="3e38a-194">Kayıt kodu aşağıda verilmiştir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3e38a-194">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4)]

<span data-ttu-id="3e38a-195">Gösterilen kod, tüm yerleşik değer sağlayıcılarından sonra özel değer sağlayıcısını koyar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-195">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="3e38a-196">Bunu listede ilk yapmak için `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` yerine çağırın `Add` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-196">To make it the first in the list, call `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="3e38a-197">Model özelliği için kaynak yok</span><span class="sxs-lookup"><span data-stu-id="3e38a-197">No source for a model property</span></span>

<span data-ttu-id="3e38a-198">Varsayılan olarak, model özelliği için bir değer bulunmazsa model durumu hatası oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="3e38a-198">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="3e38a-199">Özelliği null veya varsayılan bir değer olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="3e38a-199">The property is set to null or a default value:</span></span>

* <span data-ttu-id="3e38a-200">Null yapılabilir basit türler olarak ayarlanır `null` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-200">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="3e38a-201">Null yapılamayan değer türleri olarak ayarlanır `default(T)` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-201">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="3e38a-202">Örneğin, bir parametre `int id` 0 olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-202">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="3e38a-203">Karmaşık türler için model bağlama, özellikleri ayarlamadan varsayılan oluşturucuyu kullanarak bir örnek oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3e38a-203">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="3e38a-204">Diziler olarak ayarlanır `Array.Empty<T>()` , ancak `byte[]` diziler olarak ayarlanır `null` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-204">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="3e38a-205">Model özelliği için form alanlarında hiçbir şey bulunamadığında model durumunun geçersiz kılınmalıdır, [`[BindRequired]`](#bindrequired-attribute) özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="3e38a-205">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="3e38a-206">Bu `[BindRequired]` davranışın, bir istek GÖVDESINDE JSON veya XML verilerine değil, postalanan form verilerinden model bağlama için geçerli olduğunu unutmayın.</span><span class="sxs-lookup"><span data-stu-id="3e38a-206">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="3e38a-207">İstek gövdesi verileri, [giriş formatlayıcıları](#input-formatters)tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-207">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="3e38a-208">Tür dönüştürme hataları</span><span class="sxs-lookup"><span data-stu-id="3e38a-208">Type conversion errors</span></span>

<span data-ttu-id="3e38a-209">Bir kaynak bulunursa ancak hedef türe dönüştürülemiyorsa, model durumu geçersiz olarak işaretlenir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-209">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="3e38a-210">Hedef parametresi veya özelliği, önceki bölümde belirtildiği gibi null veya varsayılan değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-210">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="3e38a-211">Özniteliği olan bir API denetleyicisinde `[ApiController]` , geçersiz model durumu OTOMATIK HTTP 400 yanıtına neden olur.</span><span class="sxs-lookup"><span data-stu-id="3e38a-211">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="3e38a-212">Bir :::no-loc(Razor)::: sayfada, sayfayı bir hata iletisiyle yeniden görüntüleyin:</span><span class="sxs-lookup"><span data-stu-id="3e38a-212">In a :::no-loc(Razor)::: page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="3e38a-213">İstemci tarafı doğrulama, aksi takdirde bir sayfalar formuna gönderilemeyen hatalı verileri yakalar :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="3e38a-213">Client-side validation catches most bad data that would otherwise be submitted to a :::no-loc(Razor)::: Pages form.</span></span> <span data-ttu-id="3e38a-214">Bu doğrulama, önceki vurgulanmış kodu tetiklemeyi zorlaştırır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-214">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="3e38a-215">Örnek uygulama, teslim **tarihi** alanına hatalı veri yerleştiren ve formu Gönderen **Geçersiz tarih içeren bir Gönder** düğmesi içerir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-215">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="3e38a-216">Bu düğme, veri dönüştürme hataları oluştuğunda sayfanın yeniden görüntülenmesine yönelik kodun nasıl çalıştığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-216">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="3e38a-217">Sayfa önceki kodla yeniden görüntülendiğinde, form alanında geçersiz giriş gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="3e38a-217">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="3e38a-218">Bunun nedeni model özelliğinin null ya da varsayılan bir değer olarak ayarlanmış olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-218">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="3e38a-219">Geçersiz giriş bir hata iletisinde görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-219">The invalid input does appear in an error message.</span></span> <span data-ttu-id="3e38a-220">Ancak form alanındaki hatalı verileri yeniden görüntülemek istiyorsanız, model özelliğini bir dize haline getirmeyi ve veri dönüştürmeyi el ile gerçekleştirmeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="3e38a-220">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="3e38a-221">Tür dönüştürme hatalarının model durumu hatalarına neden olmasını istemiyorsanız aynı strateji önerilir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-221">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="3e38a-222">Bu durumda model özelliğini bir dize yapın.</span><span class="sxs-lookup"><span data-stu-id="3e38a-222">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="3e38a-223">Basit türler</span><span class="sxs-lookup"><span data-stu-id="3e38a-223">Simple types</span></span>

<span data-ttu-id="3e38a-224">Model cildin kaynak dizeleri dönüştürebileceğiniz basit türler aşağıdakileri içerir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-224">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="3e38a-225">Boolean</span><span class="sxs-lookup"><span data-stu-id="3e38a-225">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="3e38a-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="3e38a-226">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="3e38a-227">Char</span><span class="sxs-lookup"><span data-stu-id="3e38a-227">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="3e38a-228">Tarih Saat</span><span class="sxs-lookup"><span data-stu-id="3e38a-228">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="3e38a-229">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="3e38a-229">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="3e38a-230">Kategori</span><span class="sxs-lookup"><span data-stu-id="3e38a-230">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="3e38a-231">Çift</span><span class="sxs-lookup"><span data-stu-id="3e38a-231">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="3e38a-232">Sabit listesi</span><span class="sxs-lookup"><span data-stu-id="3e38a-232">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="3e38a-233">'İni</span><span class="sxs-lookup"><span data-stu-id="3e38a-233">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="3e38a-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="3e38a-234">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="3e38a-235">Tek</span><span class="sxs-lookup"><span data-stu-id="3e38a-235">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="3e38a-236">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="3e38a-236">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="3e38a-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="3e38a-237">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="3e38a-238">Kullanılmamışsa</span><span class="sxs-lookup"><span data-stu-id="3e38a-238">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="3e38a-239">Sürüm</span><span class="sxs-lookup"><span data-stu-id="3e38a-239">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="3e38a-240">Karmaşık türler</span><span class="sxs-lookup"><span data-stu-id="3e38a-240">Complex types</span></span>

<span data-ttu-id="3e38a-241">Karmaşık bir türün bağlanması için ortak bir varsayılan Oluşturucusu ve ortak yazılabilir özellikleri olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-241">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="3e38a-242">Model bağlama gerçekleştiğinde, sınıf ortak varsayılan Oluşturucu kullanılarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3e38a-242">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="3e38a-243">Karmaşık türün her özelliği için model bağlama *prefix.property_name* ad deseninin kaynaklarını arar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-243">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name* .</span></span> <span data-ttu-id="3e38a-244">Hiçbir şey bulunamazsa, ön ek olmadan yalnızca *property_name* arar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-244">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="3e38a-245">Bir parametreye bağlama için, önek parametre adıdır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-245">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="3e38a-246">Ortak özelliğe bağlama için `PageModel` , önek ortak özellik adıdır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-246">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="3e38a-247">Bazı özniteliklerin `Prefix` , parametre veya özellik adının varsayılan kullanımını geçersiz kılabilmenizi sağlayan bir özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-247">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="3e38a-248">Örneğin, karmaşık türün aşağıdaki sınıf olduğunu varsayalım `Instructor` :</span><span class="sxs-lookup"><span data-stu-id="3e38a-248">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="3e38a-249">Önek = parametre adı</span><span class="sxs-lookup"><span data-stu-id="3e38a-249">Prefix = parameter name</span></span>

<span data-ttu-id="3e38a-250">Bağlanacak model adlı bir parametre ise `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="3e38a-250">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="3e38a-251">Model bağlama, anahtar kaynaklarına bakarak başlar `instructorToUpdate.ID` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-251">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="3e38a-252">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-252">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="3e38a-253">Önek = Özellik adı</span><span class="sxs-lookup"><span data-stu-id="3e38a-253">Prefix = property name</span></span>

<span data-ttu-id="3e38a-254">Bağlanacak model, `Instructor` denetleyicinin veya sınıfın adında bir özelliktir `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="3e38a-254">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="3e38a-255">Model bağlama, anahtar kaynaklarına bakarak başlar `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-255">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="3e38a-256">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-256">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="3e38a-257">Özel ön ek</span><span class="sxs-lookup"><span data-stu-id="3e38a-257">Custom prefix</span></span>

<span data-ttu-id="3e38a-258">Bağlanacak model adlı bir parametre ise `instructorToUpdate` ve bir `Bind` öznitelik `Instructor` önek olarak belirtirse:</span><span class="sxs-lookup"><span data-stu-id="3e38a-258">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="3e38a-259">Model bağlama, anahtar kaynaklarına bakarak başlar `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-259">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="3e38a-260">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-260">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="3e38a-261">Karmaşık tür hedefleri için öznitelikler</span><span class="sxs-lookup"><span data-stu-id="3e38a-261">Attributes for complex type targets</span></span>

<span data-ttu-id="3e38a-262">Karmaşık türlerin model bağlamasını denetlemek için birkaç yerleşik öznitelik mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="3e38a-262">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[Bind]`
* `[BindRequired]`
* `[BindNever]`

> [!WARNING]
> <span data-ttu-id="3e38a-263">Bu öznitelikler, gönderilen form verileri değer kaynağı olduğunda model bağlamayı etkiler.</span><span class="sxs-lookup"><span data-stu-id="3e38a-263">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="3e38a-264">Bunlar, gönderilen JSON ve XML istek gövdelerini işleyen giriş formatlarını **etkilemez.**</span><span class="sxs-lookup"><span data-stu-id="3e38a-264">They do \* **not** _ affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="3e38a-265">Giriş biçimleri [Bu makalenin ilerleyen kısımlarında](#input-formatters)açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-265">Input formatters are explained [later in this article](#input-formatters).</span></span>

### <a name="bind-attribute"></a><span data-ttu-id="3e38a-266">[Bind] özniteliği</span><span class="sxs-lookup"><span data-stu-id="3e38a-266">[Bind] attribute</span></span>

<span data-ttu-id="3e38a-267">, Bir sınıfa veya yöntem parametresine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-267">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="3e38a-268">Model bağlamasındaki bir modelin hangi özelliklerinin dahil edileceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-268">Specifies which properties of a model should be included in model binding.</span></span> <span data-ttu-id="3e38a-269">`[Bind]`Giriş _*_formatlarını etkilemez._*_</span><span class="sxs-lookup"><span data-stu-id="3e38a-269">`[Bind]` does _*_not_*_ affect input formatters.</span></span>

<span data-ttu-id="3e38a-270">Aşağıdaki örnekte, `Instructor` herhangi bir işleyici veya eylem yöntemi çağrıldığında yalnızca modelin belirtilen özellikleri bağlanır:</span><span class="sxs-lookup"><span data-stu-id="3e38a-270">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="3e38a-271">Aşağıdaki örnekte, `Instructor` yöntemi çağrıldığında yalnızca modelin belirtilen özellikleri bağlanır `OnPost` :</span><span class="sxs-lookup"><span data-stu-id="3e38a-271">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="3e38a-272">`[Bind]`Özniteliği, _Create \* senaryolarında fazla nakline karşı korumak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-272">The `[Bind]` attribute can be used to protect against overposting in _create\* scenarios.</span></span> <span data-ttu-id="3e38a-273">Dışlanan Özellikler null ya da boş değer olarak ayarlandığı için, düzenleme senaryolarında iyi çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="3e38a-273">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="3e38a-274">Fazla naklin savunma için, öznitelik yerine görüntüleme modelleri önerilir `[Bind]` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-274">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="3e38a-275">Daha fazla bilgi için bkz. fazla [nakil hakkında güvenlik NOI](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="3e38a-275">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="3e38a-276">[BindRequired] özniteliği</span><span class="sxs-lookup"><span data-stu-id="3e38a-276">[BindRequired] attribute</span></span>

<span data-ttu-id="3e38a-277">, Yöntem parametrelerine değil yalnızca model özelliklerine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-277">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="3e38a-278">Modelin özelliği için bağlama gerçekleşmemişse model bağlamasının model durumu hatası eklemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="3e38a-278">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="3e38a-279">Aşağıda bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-279">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

<span data-ttu-id="3e38a-280">Ayrıca bkz `[Required]` . [model doğrulama](xref:mvc/models/validation#required-attribute)içindeki öznitelik tartışması.</span><span class="sxs-lookup"><span data-stu-id="3e38a-280">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindnever-attribute"></a><span data-ttu-id="3e38a-281">[Bindhiç] özniteliği</span><span class="sxs-lookup"><span data-stu-id="3e38a-281">[BindNever] attribute</span></span>

<span data-ttu-id="3e38a-282">, Yöntem parametrelerine değil yalnızca model özelliklerine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-282">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="3e38a-283">Model bağlamasının model özelliğini değiştirmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="3e38a-283">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="3e38a-284">Aşağıda bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-284">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

## <a name="collections"></a><span data-ttu-id="3e38a-285">Koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="3e38a-285">Collections</span></span>

<span data-ttu-id="3e38a-286">Basit türlerin koleksiyonları olan hedefler için model bağlama, *parameter_name* veya *property_name* ile eşleşmeleri arar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-286">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name* .</span></span> <span data-ttu-id="3e38a-287">Eşleşme bulunmazsa, ön ek olmadan desteklenen biçimlerden birini arar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-287">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="3e38a-288">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="3e38a-288">For example:</span></span>

* <span data-ttu-id="3e38a-289">Bağlanacak parametrenin adlı bir dizi olduğunu varsayalım `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="3e38a-289">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="3e38a-290">Form veya sorgu dizesi verileri aşağıdaki biçimlerden birinde olabilir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-290">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="3e38a-291">Aşağıdaki biçim yalnızca form verilerinde desteklenir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-291">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="3e38a-292">Önceki örnek biçimlerinin hepsi için model bağlama iki öğe dizisini `selectedCourses` parametreye geçirir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-292">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="3e38a-293">Selectedkurslar [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="3e38a-293">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="3e38a-294">Selectedkurslar [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="3e38a-294">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="3e38a-295">Alt simge numaralarını kullanan veri biçimleri (... [0]... [1]...) sıfırdan başlayarak sıralı olarak numaralandırıldıklarından emin olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-295">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="3e38a-296">Alt simge numaralandırmasında boşluk varsa, boşluklardan sonraki tüm öğeler yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-296">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="3e38a-297">Örneğin, alt simgeler 0 ve 1 yerine 0 ve 2 ise ikinci öğe yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-297">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="3e38a-298">Sözlükler</span><span class="sxs-lookup"><span data-stu-id="3e38a-298">Dictionaries</span></span>

<span data-ttu-id="3e38a-299">`Dictionary`Hedefler için, model bağlama *parameter_name* veya *property_name* eşleşme arar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-299">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name* .</span></span> <span data-ttu-id="3e38a-300">Eşleşme bulunmazsa, ön ek olmadan desteklenen biçimlerden birini arar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-300">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="3e38a-301">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="3e38a-301">For example:</span></span>

* <span data-ttu-id="3e38a-302">Hedef parametrenin bir adlandırılmış olduğunu varsayalım `Dictionary<int, string>` `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="3e38a-302">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="3e38a-303">Postalanan form veya sorgu dizesi verileri aşağıdaki örneklerden birine benzeyebilir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-303">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="3e38a-304">Önceki örnek biçimlerinin hepsi için model bağlama iki öğenin bir sözlüğünü `selectedCourses` parametreye geçirir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-304">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="3e38a-305">Selectedkurslar ["1050"] = "Chemistry"</span><span class="sxs-lookup"><span data-stu-id="3e38a-305">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="3e38a-306">Selectedkurslar ["2000"] = "Ekonomiks"</span><span class="sxs-lookup"><span data-stu-id="3e38a-306">selectedCourses["2000"]="Economics"</span></span>
  
::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="constructor-binding-and-record-types"></a><span data-ttu-id="3e38a-307">Oluşturucu bağlama ve kayıt türleri</span><span class="sxs-lookup"><span data-stu-id="3e38a-307">Constructor binding and record types</span></span>

<span data-ttu-id="3e38a-308">Model bağlama, karmaşık türlerin parametresiz bir oluşturucuya sahip olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-308">Model binding requires that complex types have a parameterless constructor.</span></span> <span data-ttu-id="3e38a-309">Hem hem de `System.Text.Json` `Newtonsoft.Json` tabanlı giriş formatlayıcıları parametresiz oluşturucusu olmayan sınıfların serisini kaldırma desteği.</span><span class="sxs-lookup"><span data-stu-id="3e38a-309">Both `System.Text.Json` and `Newtonsoft.Json` based input formatters support deserialization of classes that don't have a parameterless constructor.</span></span> 

<span data-ttu-id="3e38a-310">C# 9, succinctly ağ üzerinden verileri temsil etmenin harika bir yolu olan kayıt türlerini tanıtır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-310">C# 9 introduces record types, which are a great way to succinctly represent data over the network.</span></span> <span data-ttu-id="3e38a-311">ASP.NET Core, tek bir Oluşturucu ile model bağlama ve kayıt türlerini doğrulama desteği ekler:</span><span class="sxs-lookup"><span data-stu-id="3e38a-311">ASP.NET Core adds support for model binding and validating record types with a single constructor:</span></span>

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

<span data-ttu-id="3e38a-312">`Person/Index.cshtml`:</span><span class="sxs-lookup"><span data-stu-id="3e38a-312">`Person/Index.cshtml`:</span></span>

```cshtml
@model Person

Name: <input asp-for="Name" />
...
Age: <input asp-for="Age" />
```

<span data-ttu-id="3e38a-313">Kayıt türleri doğrulanırken, çalışma zamanı, doğrulama meta verilerini özellikle özellikler yerine parametreler üzerinde arar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-313">When validating record types, the runtime searches for validation metadata specifically on parameters rather than on properties.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="3e38a-314">Model bağlama yolu verileri ve sorgu dizeleri için Genelleştirme davranışı</span><span class="sxs-lookup"><span data-stu-id="3e38a-314">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="3e38a-315">ASP.NET Core yol değeri sağlayıcısı ve sorgu dizesi değer sağlayıcısı:</span><span class="sxs-lookup"><span data-stu-id="3e38a-315">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="3e38a-316">Değerleri sabit kültür olarak değerlendirin.</span><span class="sxs-lookup"><span data-stu-id="3e38a-316">Treat values as invariant culture.</span></span>
* <span data-ttu-id="3e38a-317">URL 'Lerin kültür sabiti olmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="3e38a-317">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="3e38a-318">Buna karşılık, form verilerinden gelen değerler kültüre duyarlı bir dönüştürmeye gider.</span><span class="sxs-lookup"><span data-stu-id="3e38a-318">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="3e38a-319">Bu, URL 'Lerin yerel ayarlarda paylaşılabilir olması için tasarımdır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-319">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="3e38a-320">ASP.NET Core yol değeri sağlayıcısını ve sorgu dizesi değeri sağlayıcısını, kültüre duyarlı bir dönüşüme dönüştürmek için:</span><span class="sxs-lookup"><span data-stu-id="3e38a-320">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="3e38a-321">Şuradan devralma <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="3e38a-321">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="3e38a-322">[QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) veya [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs) adresinden kodu kopyalayın</span><span class="sxs-lookup"><span data-stu-id="3e38a-322">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="3e38a-323">Değer sağlayıcısı oluşturucusuna geçirilen [kültür değerini](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture) ile değiştirin</span><span class="sxs-lookup"><span data-stu-id="3e38a-323">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="3e38a-324">MVC seçeneklerinde varsayılan değer sağlayıcı fabrikasını yeni bir değerle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="3e38a-324">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/3.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="3e38a-325">Özel veri türleri</span><span class="sxs-lookup"><span data-stu-id="3e38a-325">Special data types</span></span>

<span data-ttu-id="3e38a-326">Model bağlamanın işleyebileceği bazı özel veri türleri vardır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-326">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="3e38a-327">Iformfile ve ıformfilecollection</span><span class="sxs-lookup"><span data-stu-id="3e38a-327">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="3e38a-328">HTTP isteğine eklenen karşıya yüklenen dosya.</span><span class="sxs-lookup"><span data-stu-id="3e38a-328">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="3e38a-329">Ayrıca, `IEnumerable<IFormFile>` birden çok dosya için de desteklenir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-329">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="3e38a-330">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="3e38a-330">CancellationToken</span></span>

<span data-ttu-id="3e38a-331">Zaman uyumsuz denetleyicilerde etkinliği iptal etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-331">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="3e38a-332">Form koleksiyonu</span><span class="sxs-lookup"><span data-stu-id="3e38a-332">FormCollection</span></span>

<span data-ttu-id="3e38a-333">Postalanan form verilerinden tüm değerleri almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-333">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="3e38a-334">Giriş biçimleri</span><span class="sxs-lookup"><span data-stu-id="3e38a-334">Input formatters</span></span>

<span data-ttu-id="3e38a-335">İstek gövdesindeki veriler JSON, XML veya başka bir biçimde olabilir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-335">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="3e38a-336">Model bağlama, bu verileri ayrıştırmak için belirli bir içerik türünü işlemek üzere yapılandırılmış bir *giriş biçimlendiricisi* kullanır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-336">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="3e38a-337">Varsayılan olarak, ASP.NET Core JSON verilerini işlemek için JSON tabanlı giriş formatlayıcıları içerir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-337">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="3e38a-338">Diğer içerik türleri için başka biçim ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3e38a-338">You can add other formatters for other content types.</span></span>

<span data-ttu-id="3e38a-339">ASP.NET Core, [tüketen](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) özniteliğine bağlı olarak giriş formatlayıcıları seçer.</span><span class="sxs-lookup"><span data-stu-id="3e38a-339">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="3e38a-340">Hiçbir öznitelik yoksa, [Content-Type üst bilgisini](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)kullanır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-340">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="3e38a-341">Yerleşik XML girişi formatlayıcıları 'nı kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="3e38a-341">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="3e38a-342">`Microsoft.AspNetCore.Mvc.Formatters.Xml`NuGet paketini yükler.</span><span class="sxs-lookup"><span data-stu-id="3e38a-342">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="3e38a-343">İçinde `Startup.ConfigureServices` , veya öğesini çağırın <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> .</span><span class="sxs-lookup"><span data-stu-id="3e38a-343">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=10)]

* <span data-ttu-id="3e38a-344">Özniteliği, `Consumes` istek GÖVDESINDE XML beklemeniz gereken denetleyici sınıflarına veya eylem yöntemlerine uygulayın.</span><span class="sxs-lookup"><span data-stu-id="3e38a-344">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="3e38a-345">Daha fazla bilgi için bkz. [XML serileştirme tanıtımı](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="3e38a-345">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

### <a name="customize-model-binding-with-input-formatters"></a><span data-ttu-id="3e38a-346">Giriş formatlayıcıları ile model bağlamayı özelleştirme</span><span class="sxs-lookup"><span data-stu-id="3e38a-346">Customize model binding with input formatters</span></span>

<span data-ttu-id="3e38a-347">Giriş biçimlendiricisi, istek gövdesinden veri okumak için tam sorumluluğa sahiptir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-347">An input formatter takes full responsibility for reading data from the request body.</span></span> <span data-ttu-id="3e38a-348">Bu işlemi özelleştirmek için, giriş biçimlendirici tarafından kullanılan API 'Leri yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="3e38a-348">To customize this process, configure the APIs used by the input formatter.</span></span> <span data-ttu-id="3e38a-349">Bu bölümde, `System.Text.Json` adlı özel bir türü anlamak için tabanlı giriş biçimlendiricinin nasıl özelleştirileceği açıklanmaktadır `ObjectId` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-349">This section describes how to customize the `System.Text.Json`-based input formatter to understand a custom type named `ObjectId`.</span></span> 

<span data-ttu-id="3e38a-350">Adlı özel bir özellik içeren aşağıdaki modeli göz önünde bulundurun `ObjectId` `Id` :</span><span class="sxs-lookup"><span data-stu-id="3e38a-350">Consider the following model, which contains a custom `ObjectId` property named `Id`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ModelWithObjectId.cs?name=snippet_Class&highlight=3)]

<span data-ttu-id="3e38a-351">Kullanırken model bağlama işlemini özelleştirmek için `System.Text.Json` , öğesinden türetilmiş bir sınıf oluşturun <xref:System.Text.Json.Serialization.JsonConverter%601> :</span><span class="sxs-lookup"><span data-stu-id="3e38a-351">To customize the model binding process when using `System.Text.Json`, create a class derived from <xref:System.Text.Json.Serialization.JsonConverter%601>:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/JsonConverters/ObjectIdConverter.cs?name=snippet_Class)]

<span data-ttu-id="3e38a-352">Özel bir dönüştürücü kullanmak için, <xref:System.Text.Json.Serialization.JsonConverterAttribute> türüne özniteliğini uygulayın.</span><span class="sxs-lookup"><span data-stu-id="3e38a-352">To use a custom converter, apply the <xref:System.Text.Json.Serialization.JsonConverterAttribute> attribute to the type.</span></span> <span data-ttu-id="3e38a-353">Aşağıdaki örnekte, `ObjectId` türü `ObjectIdConverter` özel dönüştürücü olarak ile yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="3e38a-353">In the following example, the `ObjectId` type is configured with `ObjectIdConverter` as its custom converter:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Models/ObjectId.cs?name=snippet_Class&highlight=1)]

<span data-ttu-id="3e38a-354">Daha fazla bilgi için bkz. [özel dönüştürücüler yazma](/dotnet/standard/serialization/system-text-json-converters-how-to).</span><span class="sxs-lookup"><span data-stu-id="3e38a-354">For more information, see [How to write custom converters](/dotnet/standard/serialization/system-text-json-converters-how-to).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="3e38a-355">Belirtilen türleri model bağlamalarından Dışla</span><span class="sxs-lookup"><span data-stu-id="3e38a-355">Exclude specified types from model binding</span></span>

<span data-ttu-id="3e38a-356">Model bağlama ve doğrulama sistemlerinin davranışı [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata)tarafından yönlendiriliyor.</span><span class="sxs-lookup"><span data-stu-id="3e38a-356">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="3e38a-357">`ModelMetadata` [Mvcoptions. modelmetadatadetails sağlayıcılarına](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders)bir ayrıntı sağlayıcısı ekleyerek özelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3e38a-357">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="3e38a-358">Yerleşik Ayrıntılar sağlayıcıları, belirtilen türler için model bağlamayı veya doğrulamayı devre dışı bırakmak üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-358">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="3e38a-359">Belirtilen türdeki tüm modellerdeki model bağlamayı devre dışı bırakmak için içine bir ekleyin <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-359">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3e38a-360">Örneğin, türü tüm modeller üzerinde model bağlamayı devre dışı bırakmak için `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="3e38a-360">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=5-6)]

<span data-ttu-id="3e38a-361">Belirtilen türdeki özelliklerde doğrulamayı devre dışı bırakmak için içine bir ekleyin <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-361">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3e38a-362">Örneğin, türündeki özelliklerde doğrulamayı devre dışı bırakmak için `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="3e38a-362">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=7-8)]

## <a name="custom-model-binders"></a><span data-ttu-id="3e38a-363">Özel model ciltleri</span><span class="sxs-lookup"><span data-stu-id="3e38a-363">Custom model binders</span></span>

<span data-ttu-id="3e38a-364">Özel bir model cildi yazarak ve `[ModelBinder]` belirli bir hedef için seçmek üzere özniteliğini kullanarak model bağlamayı genişletebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3e38a-364">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="3e38a-365">[Özel model bağlama](xref:mvc/advanced/custom-model-binding)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="3e38a-365">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="3e38a-366">El ile model bağlama</span><span class="sxs-lookup"><span data-stu-id="3e38a-366">Manual model binding</span></span> 

<span data-ttu-id="3e38a-367">Model bağlama yöntemi kullanılarak el ile çağrılabilir <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> .</span><span class="sxs-lookup"><span data-stu-id="3e38a-367">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="3e38a-368">Yöntemi hem hem de sınıflarında tanımlanmıştır `ControllerBase` `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-368">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="3e38a-369">Yöntem aşırı yüklemeleri, kullanılacak öneki ve değer sağlayıcısını belirtmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-369">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="3e38a-370">Yöntemi `false` model bağlamanın başarısız olup olmadığını döndürür.</span><span class="sxs-lookup"><span data-stu-id="3e38a-370">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="3e38a-371">Aşağıda bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-371">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/3.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

<span data-ttu-id="3e38a-372"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  , form gövdesinden, sorgu dizesinden ve veri yönlendirme verilerinden veri almak için değer sağlayıcılarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-372"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*>  uses value providers to get data from the form body, query string, and route data.</span></span> <span data-ttu-id="3e38a-373">`TryUpdateModelAsync` genellikle:</span><span class="sxs-lookup"><span data-stu-id="3e38a-373">`TryUpdateModelAsync` is typically:</span></span> 

* <span data-ttu-id="3e38a-374">Daha :::no-loc(Razor)::: fazla nakletmeyi engellemek için denetleyiciler ve görünümler kullanan sayfalarla ve MVC uygulamalarıyla kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-374">Used with :::no-loc(Razor)::: Pages and MVC apps using controllers and views to prevent over-posting.</span></span>
* <span data-ttu-id="3e38a-375">Form verilerinden, sorgu dizelerinden ve veri rotalarından tüketilmediği müddetçe bir Web API 'SI ile kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="3e38a-375">Not used with a web API unless consumed from form data, query strings, and route data.</span></span> <span data-ttu-id="3e38a-376">JSON kullanan Web API uç noktaları, istek gövdesini bir nesne olarak seri durumdan çıkarmak için [giriş formatlarını](#input-formatters) kullanır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-376">Web API endpoints that consume JSON use [Input formatters](#input-formatters) to deserialize the request body into an object.</span></span>

<span data-ttu-id="3e38a-377">Daha fazla bilgi için bkz. [Tryupdatemodelasync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span><span class="sxs-lookup"><span data-stu-id="3e38a-377">For more information, see [TryUpdateModelAsync](xref:data/ef-rp/crud#TryUpdateModelAsync).</span></span>

## <a name="fromservices-attribute"></a><span data-ttu-id="3e38a-378">[FromServices] özniteliği</span><span class="sxs-lookup"><span data-stu-id="3e38a-378">[FromServices] attribute</span></span>

<span data-ttu-id="3e38a-379">Bu özniteliğin adı, bir veri kaynağı belirten model bağlama özniteliklerinin düzeniyle uyar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-379">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="3e38a-380">Ancak bir değer sağlayıcısından veri bağlama hakkında bilgi yoktur.</span><span class="sxs-lookup"><span data-stu-id="3e38a-380">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="3e38a-381">[Bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısından bir türün bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-381">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="3e38a-382">Amacı, yalnızca belirli bir yöntem çağrılırsa bir hizmete ihtiyacınız olduğunda Oluşturucu ekleme için bir alternatif sağlamaktır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-382">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3e38a-383">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3e38a-383">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3e38a-384">Bu makalede, model bağlamanın ne olduğu, nasıl çalıştığı ve davranışını nasıl özelleştireceğiniz açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-384">This article explains what model binding is, how it works, and how to customize its behavior.</span></span>

<span data-ttu-id="3e38a-385">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([nasıl indirilir](xref:index#how-to-download-a-sample)).</span><span class="sxs-lookup"><span data-stu-id="3e38a-385">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/model-binding/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="what-is-model-binding"></a><span data-ttu-id="3e38a-386">Model bağlama nedir?</span><span class="sxs-lookup"><span data-stu-id="3e38a-386">What is Model binding</span></span>

<span data-ttu-id="3e38a-387">Denetleyiciler ve :::no-loc(Razor)::: Sayfalar, http isteklerinden gelen verilerle çalışır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-387">Controllers and :::no-loc(Razor)::: pages work with data that comes from HTTP requests.</span></span> <span data-ttu-id="3e38a-388">Örneğin, rota verileri bir kayıt anahtarı sağlayabilir ve postalanan form alanları, modelin özelliklerine ilişkin değerler sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-388">For example, route data may provide a record key, and posted form fields may provide values for the properties of the model.</span></span> <span data-ttu-id="3e38a-389">Bu değerlerin her birini almak ve bunları dizelerden .NET türlerine dönüştürmek için kod yazma sıkıcı ve hata durumunda olabilir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-389">Writing code to retrieve each of these values and convert them from strings to .NET types would be tedious and error-prone.</span></span> <span data-ttu-id="3e38a-390">Model bağlama bu işlemi otomatikleştirir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-390">Model binding automates this process.</span></span> <span data-ttu-id="3e38a-391">Model bağlama sistemi:</span><span class="sxs-lookup"><span data-stu-id="3e38a-391">The model binding system:</span></span>

* <span data-ttu-id="3e38a-392">Veri yolu, form alanları ve sorgu dizeleri gibi çeşitli kaynaklardan veri alır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-392">Retrieves data from various sources such as route data, form fields, and query strings.</span></span>
* <span data-ttu-id="3e38a-393">:::no-loc(Razor):::Yöntem parametreleri ve ortak özellikler içindeki denetleyicilere ve sayfalara verileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-393">Provides the data to controllers and :::no-loc(Razor)::: pages in method parameters and public properties.</span></span>
* <span data-ttu-id="3e38a-394">Dize verilerini .NET türlerine dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="3e38a-394">Converts string data to .NET types.</span></span>
* <span data-ttu-id="3e38a-395">Karmaşık türlerin özelliklerini güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-395">Updates properties of complex types.</span></span>

## <a name="example"></a><span data-ttu-id="3e38a-396">Örnek</span><span class="sxs-lookup"><span data-stu-id="3e38a-396">Example</span></span>

<span data-ttu-id="3e38a-397">Aşağıdaki eylem yöntemine sahip olduğunuzu varsayalım:</span><span class="sxs-lookup"><span data-stu-id="3e38a-397">Suppose you have the following action method:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Controllers/PetsController.cs?name=snippet_DogsOnly)]

<span data-ttu-id="3e38a-398">Ve uygulama şu URL ile bir istek alıyor:</span><span class="sxs-lookup"><span data-stu-id="3e38a-398">And the app receives a request with this URL:</span></span>

```
http://contoso.com/api/pets/2?DogsOnly=true
```

<span data-ttu-id="3e38a-399">Model bağlama, yönlendirme sistemi eylem yöntemini seçtikten sonra aşağıdaki adımlardan geçer:</span><span class="sxs-lookup"><span data-stu-id="3e38a-399">Model binding goes through the following steps after the routing system selects the action method:</span></span>

* <span data-ttu-id="3e38a-400">Adlı bir tamsayı olan ilk parametresini bulur `GetByID` `id` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-400">Finds the first parameter of `GetByID`, an integer named `id`.</span></span>
* <span data-ttu-id="3e38a-401">HTTP isteğindeki kullanılabilir kaynakları arar ve `id` route verilerinde = "2" bulur.</span><span class="sxs-lookup"><span data-stu-id="3e38a-401">Looks through the available sources in the HTTP request and finds `id` = "2" in route data.</span></span>
* <span data-ttu-id="3e38a-402">"2" dizesini tamsayı 2 ' ye dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="3e38a-402">Converts the string "2" into integer 2.</span></span>
* <span data-ttu-id="3e38a-403">`GetByID`Adlı bir Boole değeri olan bir sonraki parametresini bulur `dogsOnly` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-403">Finds the next parameter of `GetByID`, a boolean named `dogsOnly`.</span></span>
* <span data-ttu-id="3e38a-404">Kaynakları arar ve sorgu dizesinde "DogsOnly = true" bulur.</span><span class="sxs-lookup"><span data-stu-id="3e38a-404">Looks through the sources and finds "DogsOnly=true" in the query string.</span></span> <span data-ttu-id="3e38a-405">Ad eşleştirme, büyük/küçük harfe duyarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-405">Name matching is not case-sensitive.</span></span>
* <span data-ttu-id="3e38a-406">"True" dizesini Boole değerine dönüştürür `true` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-406">Converts the string "true" into boolean `true`.</span></span>

<span data-ttu-id="3e38a-407">Framework daha sonra yöntemi çağırır `GetById` , parametresi için 2 ' ye geçerek `id` ve `true` `dogsOnly` parametresi için.</span><span class="sxs-lookup"><span data-stu-id="3e38a-407">The framework then calls the `GetById` method, passing in 2 for the `id` parameter, and `true` for the `dogsOnly` parameter.</span></span>

<span data-ttu-id="3e38a-408">Önceki örnekte, model bağlama hedefleri basit türler olan yöntem parametreleridir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-408">In the preceding example, the model binding targets are method parameters that are simple types.</span></span> <span data-ttu-id="3e38a-409">Hedefler, karmaşık bir türün özellikleri de olabilir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-409">Targets may also be the properties of a complex type.</span></span> <span data-ttu-id="3e38a-410">Her bir özellik başarıyla bağlandıktan sonra, bu özellik için [model doğrulaması](xref:mvc/models/validation) oluşur.</span><span class="sxs-lookup"><span data-stu-id="3e38a-410">After each property is successfully bound, [model validation](xref:mvc/models/validation) occurs for that property.</span></span> <span data-ttu-id="3e38a-411">Hangi verilerin modele bağladığına ve tüm bağlama veya doğrulama hatalarıyla ilgili kayıt, [ControllerBase. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) veya [Pagemodel. ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState)içinde depolanır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-411">The record of what data is bound to the model, and any binding or validation errors, is stored in [ControllerBase.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState) or [PageModel.ModelState](xref:Microsoft.AspNetCore.Mvc.ControllerBase.ModelState).</span></span> <span data-ttu-id="3e38a-412">Bu işlemin başarılı olup olmadığını öğrenmek için uygulama [ModelState. IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) bayrağını denetler.</span><span class="sxs-lookup"><span data-stu-id="3e38a-412">To find out if this process was successful, the app checks the [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) flag.</span></span>

## <a name="targets"></a><span data-ttu-id="3e38a-413">Targets</span><span class="sxs-lookup"><span data-stu-id="3e38a-413">Targets</span></span>

<span data-ttu-id="3e38a-414">Model bağlama, aşağıdaki tür hedeflerin değerlerini bulmayı dener:</span><span class="sxs-lookup"><span data-stu-id="3e38a-414">Model binding tries to find values for the following kinds of targets:</span></span>

* <span data-ttu-id="3e38a-415">Bir isteğin yönlendirildiği denetleyici eylemi yönteminin parametreleri.</span><span class="sxs-lookup"><span data-stu-id="3e38a-415">Parameters of the controller action method that a request is routed to.</span></span>
* <span data-ttu-id="3e38a-416">:::no-loc(Razor):::Bir isteğin yönlendirildiği sayfa işleyicisi yönteminin parametreleri.</span><span class="sxs-lookup"><span data-stu-id="3e38a-416">Parameters of the :::no-loc(Razor)::: Pages handler method that a request is routed to.</span></span> 
* <span data-ttu-id="3e38a-417">Bir denetleyicinin veya `PageModel` sınıfın öznitelikleri tarafından belirtilmişse ortak özellikleri.</span><span class="sxs-lookup"><span data-stu-id="3e38a-417">Public properties of a controller or `PageModel` class, if specified by attributes.</span></span>

### <a name="bindproperty-attribute"></a><span data-ttu-id="3e38a-418">[BindProperty] özniteliği</span><span class="sxs-lookup"><span data-stu-id="3e38a-418">[BindProperty] attribute</span></span>

<span data-ttu-id="3e38a-419">, `PageModel` Model bağlamasının bu özelliği hedeflemesini sağlamak için bir denetleyicinin veya sınıfın ortak özelliğine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-419">Can be applied to a public property of a controller or `PageModel` class to cause model binding to target that property:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Edit.cshtml.cs?name=snippet_BindProperty&highlight=3-4)]

### <a name="bindproperties-attribute"></a><span data-ttu-id="3e38a-420">[BindProperties] özniteliği</span><span class="sxs-lookup"><span data-stu-id="3e38a-420">[BindProperties] attribute</span></span>

<span data-ttu-id="3e38a-421">ASP.NET Core 2,1 ve üzeri sürümlerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-421">Available in ASP.NET Core 2.1 and later.</span></span>  <span data-ttu-id="3e38a-422">`PageModel`Model bağlamaya, sınıfın tüm ortak özelliklerini hedeflemesini bildirmek için bir denetleyiciye veya sınıfa uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-422">Can be applied to a controller or `PageModel` class to tell model binding to target all public properties of the class:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_BindProperties&highlight=1-2)]

### <a name="model-binding-for-http-get-requests"></a><span data-ttu-id="3e38a-423">HTTP GET istekleri için model bağlama</span><span class="sxs-lookup"><span data-stu-id="3e38a-423">Model binding for HTTP GET requests</span></span>

<span data-ttu-id="3e38a-424">Varsayılan olarak, Özellikler HTTP GET istekleri için bağlantılı değildir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-424">By default, properties are not bound for HTTP GET requests.</span></span> <span data-ttu-id="3e38a-425">Genellikle, bir GET isteği için tüm ihtiyacınız olan bir kayıt KIMLIĞI parametresidir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-425">Typically, all you need for a GET request is a record ID parameter.</span></span> <span data-ttu-id="3e38a-426">Kayıt KIMLIĞI, veritabanındaki öğeyi aramak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-426">The record ID is used to look up the item in the database.</span></span> <span data-ttu-id="3e38a-427">Bu nedenle, modelin bir örneğini tutan bir özelliği bağlamaya gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="3e38a-427">Therefore, there is no need to bind a property that holds an instance of the model.</span></span> <span data-ttu-id="3e38a-428">GET isteklerinden alınan özelliklerin verilerine bağlanmasını istediğiniz senaryolarda `SupportsGet` özelliği şu şekilde ayarlayın `true` :</span><span class="sxs-lookup"><span data-stu-id="3e38a-428">In scenarios where you do want properties bound to data from GET requests, set the `SupportsGet` property to `true`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_SupportsGet)]

## <a name="sources"></a><span data-ttu-id="3e38a-429">Kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3e38a-429">Sources</span></span>

<span data-ttu-id="3e38a-430">Varsayılan olarak, model bağlama, bir HTTP isteğindeki aşağıdaki kaynaklardan gelen anahtar-değer çiftleri biçimindeki verileri alır:</span><span class="sxs-lookup"><span data-stu-id="3e38a-430">By default, model binding gets data in the form of key-value pairs from the following sources in an HTTP request:</span></span>

1. <span data-ttu-id="3e38a-431">Form alanları</span><span class="sxs-lookup"><span data-stu-id="3e38a-431">Form fields</span></span>
1. <span data-ttu-id="3e38a-432">İstek gövdesi ( [[ApiController] özniteliğine sahip denetleyiciler](xref:web-api/index#binding-source-parameter-inference)için.)</span><span class="sxs-lookup"><span data-stu-id="3e38a-432">The request body (For [controllers that have the [ApiController] attribute](xref:web-api/index#binding-source-parameter-inference).)</span></span>
1. <span data-ttu-id="3e38a-433">Veri yönlendirme</span><span class="sxs-lookup"><span data-stu-id="3e38a-433">Route data</span></span>
1. <span data-ttu-id="3e38a-434">Sorgu dizesi parametreleri</span><span class="sxs-lookup"><span data-stu-id="3e38a-434">Query string parameters</span></span>
1. <span data-ttu-id="3e38a-435">Karşıya yüklenen dosyalar</span><span class="sxs-lookup"><span data-stu-id="3e38a-435">Uploaded files</span></span>

<span data-ttu-id="3e38a-436">Her hedef parametresi veya özelliği için kaynaklar, önceki listede belirtilen sırada taranır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-436">For each target parameter or property, the sources are scanned in the order indicated in the preceding list.</span></span> <span data-ttu-id="3e38a-437">Birkaç özel durum vardır:</span><span class="sxs-lookup"><span data-stu-id="3e38a-437">There are a few exceptions:</span></span>

* <span data-ttu-id="3e38a-438">Rota verileri ve sorgu dizesi değerleri yalnızca basit türler için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-438">Route data and query string values are used only for simple types.</span></span>
* <span data-ttu-id="3e38a-439">Karşıya yüklenen dosyalar yalnızca veya uygulayan hedef türlere bağlanır `IFormFile` `IEnumerable<IFormFile>` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-439">Uploaded files are bound only to target types that implement `IFormFile` or `IEnumerable<IFormFile>`.</span></span>

<span data-ttu-id="3e38a-440">Varsayılan kaynak doğru değilse, kaynağı belirtmek için aşağıdaki özniteliklerden birini kullanın:</span><span class="sxs-lookup"><span data-stu-id="3e38a-440">If the default source is not correct, use one of the following attributes to specify the source:</span></span>

* <span data-ttu-id="3e38a-441">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) -Sorgu dizesinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-441">[`[FromQuery]`](xref:Microsoft.AspNetCore.Mvc.FromQueryAttribute) - Gets values from the query string.</span></span> 
* <span data-ttu-id="3e38a-442">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) -Rota verilerinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-442">[`[FromRoute]`](xref:Microsoft.AspNetCore.Mvc.FromRouteAttribute) - Gets values from route data.</span></span>
* <span data-ttu-id="3e38a-443">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) -Postalanan Form alanlarındaki değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-443">[`[FromForm]`](xref:Microsoft.AspNetCore.Mvc.FromFormAttribute) - Gets values from posted form fields.</span></span>
* <span data-ttu-id="3e38a-444">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) -İstek gövdesinden değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-444">[`[FromBody]`](xref:Microsoft.AspNetCore.Mvc.FromBodyAttribute) - Gets values from the request body.</span></span>
* <span data-ttu-id="3e38a-445">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) -HTTP başlıklarındaki değerleri alır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-445">[`[FromHeader]`](xref:Microsoft.AspNetCore.Mvc.FromHeaderAttribute) - Gets values from HTTP headers.</span></span>

<span data-ttu-id="3e38a-446">Bu öznitelikler:</span><span class="sxs-lookup"><span data-stu-id="3e38a-446">These attributes:</span></span>

* <span data-ttu-id="3e38a-447">Model özelliklerine tek tek eklenir (model sınıfına değil), aşağıdaki örnekte olduğu gibi:</span><span class="sxs-lookup"><span data-stu-id="3e38a-447">Are added to model properties individually (not to the model class), as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/Instructor.cs?name=snippet_FromQuery&highlight=5-6)]

* <span data-ttu-id="3e38a-448">İsteğe bağlı olarak oluşturucuda bir model adı değeri kabul edin.</span><span class="sxs-lookup"><span data-stu-id="3e38a-448">Optionally accept a model name value in the constructor.</span></span> <span data-ttu-id="3e38a-449">Bu seçenek, özellik adının istekteki değerle eşleşmemesi durumunda sağlanır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-449">This option is provided in case the property name doesn't match the value in the request.</span></span> <span data-ttu-id="3e38a-450">Örneğin, istekteki değer aşağıdaki örnekte olduğu gibi adında bir tire olan bir üstbilgi olabilir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-450">For instance, the value in the request might be a header with a hyphen in its name, as in the following example:</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Index.cshtml.cs?name=snippet_FromHeader)]

### <a name="frombody-attribute"></a><span data-ttu-id="3e38a-451">[FromBody] özniteliği</span><span class="sxs-lookup"><span data-stu-id="3e38a-451">[FromBody] attribute</span></span>

<span data-ttu-id="3e38a-452">`[FromBody]`Özniteliği BIR http isteği gövdesinden doldurmak için bir parametreye özniteliğini uygulayın.</span><span class="sxs-lookup"><span data-stu-id="3e38a-452">Apply the `[FromBody]` attribute to a parameter to populate its properties from the body of an HTTP request.</span></span> <span data-ttu-id="3e38a-453">ASP.NET Core çalışma zamanı, gövdeyi bir giriş biçimlendirici 'ya okumaktan sorumlu olarak temsil eder.</span><span class="sxs-lookup"><span data-stu-id="3e38a-453">The ASP.NET Core runtime delegates the responsibility of reading the body to an input formatter.</span></span> <span data-ttu-id="3e38a-454">Giriş biçimleri [Bu makalenin ilerleyen kısımlarında](#input-formatters)açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-454">Input formatters are explained [later in this article](#input-formatters).</span></span>

<span data-ttu-id="3e38a-455">`[FromBody]`Karmaşık bir tür parametresine uygulandığında, özelliklerine uygulanan herhangi bir bağlama kaynak özniteliği yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-455">When `[FromBody]` is applied to a complex type parameter, any binding source attributes applied to its properties are ignored.</span></span> <span data-ttu-id="3e38a-456">Örneğin, aşağıdaki eylem, `Create` `pet` parametresinin gövdeden doldurulduğunu belirtir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-456">For example, the following `Create` action specifies that its `pet` parameter is populated from the body:</span></span>

```csharp
public ActionResult<Pet> Create([FromBody] Pet pet)
```

<span data-ttu-id="3e38a-457">`Pet`Sınıfı, `Breed` özelliğinin bir sorgu dizesi parametresinden doldurulduğunu belirtir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-457">The `Pet` class specifies that its `Breed` property is populated from a query string parameter:</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }

    [FromQuery] // Attribute is ignored.
    public string Breed { get; set; }
}
```

<span data-ttu-id="3e38a-458">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="3e38a-458">In the preceding example:</span></span>

* <span data-ttu-id="3e38a-459">`[FromQuery]`Öznitelik yoksayıldı.</span><span class="sxs-lookup"><span data-stu-id="3e38a-459">The `[FromQuery]` attribute is ignored.</span></span>
* <span data-ttu-id="3e38a-460">`Breed`Özellik bir sorgu dizesi parametresinden doldurulmamış.</span><span class="sxs-lookup"><span data-stu-id="3e38a-460">The `Breed` property is not populated from a query string parameter.</span></span> 

<span data-ttu-id="3e38a-461">Giriş biçimleri yalnızca gövdeyi okur ve bağlama kaynak özniteliklerini anlamayın.</span><span class="sxs-lookup"><span data-stu-id="3e38a-461">Input formatters read only the body and don't understand binding source attributes.</span></span> <span data-ttu-id="3e38a-462">Gövdede uygun bir değer bulunursa, bu değer özelliği doldurmak için kullanılır `Breed` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-462">If a suitable value is found in the body, that value is used to populate the `Breed` property.</span></span>

<span data-ttu-id="3e38a-463">`[FromBody]`Action yöntemi başına birden fazla parametreye uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="3e38a-463">Don't apply `[FromBody]` to more than one parameter per action method.</span></span> <span data-ttu-id="3e38a-464">İstek akışı bir giriş biçimlendirici tarafından okunduktan sonra, diğer parametreleri bağlamak için artık bir daha okunamaz `[FromBody]` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-464">Once the request stream is read by an input formatter, it's no longer available to be read again for binding other `[FromBody]` parameters.</span></span>

### <a name="additional-sources"></a><span data-ttu-id="3e38a-465">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3e38a-465">Additional sources</span></span>

<span data-ttu-id="3e38a-466">Kaynak verileri, model bağlama sistemine *değer sağlayıcılara* göre sağlanır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-466">Source data is provided to the model binding system by *value providers* .</span></span> <span data-ttu-id="3e38a-467">Diğer kaynaklardan model bağlamaya yönelik verileri alan özel değer sağlayıcıları yazabilir ve kaydedebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3e38a-467">You can write and register custom value providers that get data for model binding from other sources.</span></span> <span data-ttu-id="3e38a-468">Örneğin, verilerin :::no-loc(cookie)::: veya oturum durumunun olmasını isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3e38a-468">For example, you might want data from :::no-loc(cookie):::s or session state.</span></span> <span data-ttu-id="3e38a-469">Yeni bir kaynaktan veri almak için:</span><span class="sxs-lookup"><span data-stu-id="3e38a-469">To get data from a new source:</span></span>

* <span data-ttu-id="3e38a-470">Uygulayan bir sınıf oluşturun `IValueProvider` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-470">Create a class that implements `IValueProvider`.</span></span>
* <span data-ttu-id="3e38a-471">Uygulayan bir sınıf oluşturun `IValueProviderFactory` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-471">Create a class that implements `IValueProviderFactory`.</span></span>
* <span data-ttu-id="3e38a-472">Fabrika sınıfını içine kaydedin `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-472">Register the factory class in `Startup.ConfigureServices`.</span></span>

<span data-ttu-id="3e38a-473">Örnek uygulama, ' den değerler alan bir [değer sağlayıcısı](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) ve [Factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) örneği içerir :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="3e38a-473">The sample app includes a [value provider](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProvider.cs) and [factory](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/models/model-binding/samples/2.x/ModelBindingSample/:::no-loc(Cookie):::ValueProviderFactory.cs) example that gets values from :::no-loc(cookie):::s.</span></span> <span data-ttu-id="3e38a-474">Kayıt kodu aşağıda verilmiştir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3e38a-474">Here's the registration code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=3)]

<span data-ttu-id="3e38a-475">Gösterilen kod, tüm yerleşik değer sağlayıcılarından sonra özel değer sağlayıcısını koyar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-475">The code shown puts the custom value provider after all the built-in value providers.</span></span>  <span data-ttu-id="3e38a-476">Bunu listede ilk yapmak için `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` yerine çağırın `Add` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-476">To make it the first in the list, call `Insert(0, new :::no-loc(Cookie):::ValueProviderFactory())` instead of `Add`.</span></span>

## <a name="no-source-for-a-model-property"></a><span data-ttu-id="3e38a-477">Model özelliği için kaynak yok</span><span class="sxs-lookup"><span data-stu-id="3e38a-477">No source for a model property</span></span>

<span data-ttu-id="3e38a-478">Varsayılan olarak, model özelliği için bir değer bulunmazsa model durumu hatası oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="3e38a-478">By default, a model state error isn't created if no value is found for a model property.</span></span> <span data-ttu-id="3e38a-479">Özelliği null veya varsayılan bir değer olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="3e38a-479">The property is set to null or a default value:</span></span>

* <span data-ttu-id="3e38a-480">Null yapılabilir basit türler olarak ayarlanır `null` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-480">Nullable simple types are set to `null`.</span></span>
* <span data-ttu-id="3e38a-481">Null yapılamayan değer türleri olarak ayarlanır `default(T)` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-481">Non-nullable value types are set to `default(T)`.</span></span> <span data-ttu-id="3e38a-482">Örneğin, bir parametre `int id` 0 olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-482">For example, a parameter `int id` is set to 0.</span></span>
* <span data-ttu-id="3e38a-483">Karmaşık türler için model bağlama, özellikleri ayarlamadan varsayılan oluşturucuyu kullanarak bir örnek oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3e38a-483">For complex Types, model binding creates an instance by using the default constructor, without setting properties.</span></span>
* <span data-ttu-id="3e38a-484">Diziler olarak ayarlanır `Array.Empty<T>()` , ancak `byte[]` diziler olarak ayarlanır `null` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-484">Arrays are set to `Array.Empty<T>()`, except that `byte[]` arrays are set to `null`.</span></span>

<span data-ttu-id="3e38a-485">Model özelliği için form alanlarında hiçbir şey bulunamadığında model durumunun geçersiz kılınmalıdır, [`[BindRequired]`](#bindrequired-attribute) özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="3e38a-485">If model state should be invalidated when nothing is found in form fields for a model property, use the [`[BindRequired]`](#bindrequired-attribute) attribute.</span></span>

<span data-ttu-id="3e38a-486">Bu `[BindRequired]` davranışın, bir istek GÖVDESINDE JSON veya XML verilerine değil, postalanan form verilerinden model bağlama için geçerli olduğunu unutmayın.</span><span class="sxs-lookup"><span data-stu-id="3e38a-486">Note that this `[BindRequired]` behavior applies to model binding from posted form data, not to JSON or XML data in a request body.</span></span> <span data-ttu-id="3e38a-487">İstek gövdesi verileri, [giriş formatlayıcıları](#input-formatters)tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-487">Request body data is handled by [input formatters](#input-formatters).</span></span>

## <a name="type-conversion-errors"></a><span data-ttu-id="3e38a-488">Tür dönüştürme hataları</span><span class="sxs-lookup"><span data-stu-id="3e38a-488">Type conversion errors</span></span>

<span data-ttu-id="3e38a-489">Bir kaynak bulunursa ancak hedef türe dönüştürülemiyorsa, model durumu geçersiz olarak işaretlenir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-489">If a source is found but can't be converted into the target type, model state is flagged as invalid.</span></span> <span data-ttu-id="3e38a-490">Hedef parametresi veya özelliği, önceki bölümde belirtildiği gibi null veya varsayılan değer olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-490">The target parameter or property is set to null or a default value, as noted in the previous section.</span></span>

<span data-ttu-id="3e38a-491">Özniteliği olan bir API denetleyicisinde `[ApiController]` , geçersiz model durumu OTOMATIK HTTP 400 yanıtına neden olur.</span><span class="sxs-lookup"><span data-stu-id="3e38a-491">In an API controller that has the `[ApiController]` attribute, invalid model state results in an automatic HTTP 400 response.</span></span>

<span data-ttu-id="3e38a-492">Bir :::no-loc(Razor)::: sayfada, sayfayı bir hata iletisiyle yeniden görüntüleyin:</span><span class="sxs-lookup"><span data-stu-id="3e38a-492">In a :::no-loc(Razor)::: page, redisplay the page with an error message:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/Instructors/Create.cshtml.cs?name=snippet_HandleMBError&highlight=3-6)]

<span data-ttu-id="3e38a-493">İstemci tarafı doğrulama, aksi takdirde bir sayfalar formuna gönderilemeyen hatalı verileri yakalar :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="3e38a-493">Client-side validation catches most bad data that would otherwise be submitted to a :::no-loc(Razor)::: Pages form.</span></span> <span data-ttu-id="3e38a-494">Bu doğrulama, önceki vurgulanmış kodu tetiklemeyi zorlaştırır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-494">This validation makes it hard to trigger the preceding highlighted code.</span></span> <span data-ttu-id="3e38a-495">Örnek uygulama, teslim **tarihi** alanına hatalı veri yerleştiren ve formu Gönderen **Geçersiz tarih içeren bir Gönder** düğmesi içerir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-495">The sample app includes a **Submit with Invalid Date** button that puts bad data in the **Hire Date** field and submits the form.</span></span> <span data-ttu-id="3e38a-496">Bu düğme, veri dönüştürme hataları oluştuğunda sayfanın yeniden görüntülenmesine yönelik kodun nasıl çalıştığını gösterir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-496">This button shows how the code for redisplaying the page works when data conversion errors occur.</span></span>

<span data-ttu-id="3e38a-497">Sayfa önceki kodla yeniden görüntülendiğinde, form alanında geçersiz giriş gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="3e38a-497">When the page is redisplayed by the preceding code, the invalid input is not shown in the form field.</span></span> <span data-ttu-id="3e38a-498">Bunun nedeni model özelliğinin null ya da varsayılan bir değer olarak ayarlanmış olmasından kaynaklanır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-498">This is because the model property has been set to null or a default value.</span></span> <span data-ttu-id="3e38a-499">Geçersiz giriş bir hata iletisinde görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-499">The invalid input does appear in an error message.</span></span> <span data-ttu-id="3e38a-500">Ancak form alanındaki hatalı verileri yeniden görüntülemek istiyorsanız, model özelliğini bir dize haline getirmeyi ve veri dönüştürmeyi el ile gerçekleştirmeyi düşünün.</span><span class="sxs-lookup"><span data-stu-id="3e38a-500">But if you want to redisplay the bad data in the form field, consider making the model property a string and doing the data conversion manually.</span></span>

<span data-ttu-id="3e38a-501">Tür dönüştürme hatalarının model durumu hatalarına neden olmasını istemiyorsanız aynı strateji önerilir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-501">The same strategy is recommended if you don't want type conversion errors to result in model state errors.</span></span> <span data-ttu-id="3e38a-502">Bu durumda model özelliğini bir dize yapın.</span><span class="sxs-lookup"><span data-stu-id="3e38a-502">In that case, make the model property a string.</span></span>

## <a name="simple-types"></a><span data-ttu-id="3e38a-503">Basit türler</span><span class="sxs-lookup"><span data-stu-id="3e38a-503">Simple types</span></span>

<span data-ttu-id="3e38a-504">Model cildin kaynak dizeleri dönüştürebileceğiniz basit türler aşağıdakileri içerir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-504">The simple types that the model binder can convert source strings into include the following:</span></span>

* [<span data-ttu-id="3e38a-505">Boolean</span><span class="sxs-lookup"><span data-stu-id="3e38a-505">Boolean</span></span>](xref:System.ComponentModel.BooleanConverter)
* <span data-ttu-id="3e38a-506">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span><span class="sxs-lookup"><span data-stu-id="3e38a-506">[Byte](xref:System.ComponentModel.ByteConverter), [SByte](xref:System.ComponentModel.SByteConverter)</span></span>
* [<span data-ttu-id="3e38a-507">Char</span><span class="sxs-lookup"><span data-stu-id="3e38a-507">Char</span></span>](xref:System.ComponentModel.CharConverter)
* [<span data-ttu-id="3e38a-508">Tarih Saat</span><span class="sxs-lookup"><span data-stu-id="3e38a-508">DateTime</span></span>](xref:System.ComponentModel.DateTimeConverter)
* [<span data-ttu-id="3e38a-509">DateTimeOffset</span><span class="sxs-lookup"><span data-stu-id="3e38a-509">DateTimeOffset</span></span>](xref:System.ComponentModel.DateTimeOffsetConverter)
* [<span data-ttu-id="3e38a-510">Kategori</span><span class="sxs-lookup"><span data-stu-id="3e38a-510">Decimal</span></span>](xref:System.ComponentModel.DecimalConverter)
* [<span data-ttu-id="3e38a-511">Çift</span><span class="sxs-lookup"><span data-stu-id="3e38a-511">Double</span></span>](xref:System.ComponentModel.DoubleConverter)
* [<span data-ttu-id="3e38a-512">Sabit listesi</span><span class="sxs-lookup"><span data-stu-id="3e38a-512">Enum</span></span>](xref:System.ComponentModel.EnumConverter)
* [<span data-ttu-id="3e38a-513">'İni</span><span class="sxs-lookup"><span data-stu-id="3e38a-513">Guid</span></span>](xref:System.ComponentModel.GuidConverter)
* <span data-ttu-id="3e38a-514">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span><span class="sxs-lookup"><span data-stu-id="3e38a-514">[Int16](xref:System.ComponentModel.Int16Converter), [Int32](xref:System.ComponentModel.Int32Converter), [Int64](xref:System.ComponentModel.Int64Converter)</span></span>
* [<span data-ttu-id="3e38a-515">Tek</span><span class="sxs-lookup"><span data-stu-id="3e38a-515">Single</span></span>](xref:System.ComponentModel.SingleConverter)
* [<span data-ttu-id="3e38a-516">TimeSpan</span><span class="sxs-lookup"><span data-stu-id="3e38a-516">TimeSpan</span></span>](xref:System.ComponentModel.TimeSpanConverter)
* <span data-ttu-id="3e38a-517">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span><span class="sxs-lookup"><span data-stu-id="3e38a-517">[UInt16](xref:System.ComponentModel.UInt16Converter), [UInt32](xref:System.ComponentModel.UInt32Converter), [UInt64](xref:System.ComponentModel.UInt64Converter)</span></span>
* [<span data-ttu-id="3e38a-518">Kullanılmamışsa</span><span class="sxs-lookup"><span data-stu-id="3e38a-518">Uri</span></span>](xref:System.UriTypeConverter)
* [<span data-ttu-id="3e38a-519">Sürüm</span><span class="sxs-lookup"><span data-stu-id="3e38a-519">Version</span></span>](xref:System.ComponentModel.VersionConverter)

## <a name="complex-types"></a><span data-ttu-id="3e38a-520">Karmaşık türler</span><span class="sxs-lookup"><span data-stu-id="3e38a-520">Complex types</span></span>

<span data-ttu-id="3e38a-521">Karmaşık bir türün bağlanması için ortak bir varsayılan Oluşturucusu ve ortak yazılabilir özellikleri olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-521">A complex type must have a public default constructor and public writable properties to bind.</span></span> <span data-ttu-id="3e38a-522">Model bağlama gerçekleştiğinde, sınıf ortak varsayılan Oluşturucu kullanılarak oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3e38a-522">When model binding occurs, the class is instantiated using the public default constructor.</span></span> 

<span data-ttu-id="3e38a-523">Karmaşık türün her özelliği için model bağlama *prefix.property_name* ad deseninin kaynaklarını arar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-523">For each property of the complex type, model binding looks through the sources for the name pattern *prefix.property_name* .</span></span> <span data-ttu-id="3e38a-524">Hiçbir şey bulunamazsa, ön ek olmadan yalnızca *property_name* arar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-524">If nothing is found, it looks for just *property_name* without the prefix.</span></span>

<span data-ttu-id="3e38a-525">Bir parametreye bağlama için, önek parametre adıdır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-525">For binding to a parameter, the prefix is the parameter name.</span></span> <span data-ttu-id="3e38a-526">Ortak özelliğe bağlama için `PageModel` , önek ortak özellik adıdır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-526">For binding to a `PageModel` public property, the prefix is the public property name.</span></span> <span data-ttu-id="3e38a-527">Bazı özniteliklerin `Prefix` , parametre veya özellik adının varsayılan kullanımını geçersiz kılabilmenizi sağlayan bir özelliği vardır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-527">Some attributes have a `Prefix` property that lets you override the default usage of parameter or property name.</span></span>

<span data-ttu-id="3e38a-528">Örneğin, karmaşık türün aşağıdaki sınıf olduğunu varsayalım `Instructor` :</span><span class="sxs-lookup"><span data-stu-id="3e38a-528">For example, suppose the complex type is the following `Instructor` class:</span></span>

  ```csharp
  public class Instructor
  {
      public int ID { get; set; }
      public string LastName { get; set; }
      public string FirstName { get; set; }
  }
  ```

### <a name="prefix--parameter-name"></a><span data-ttu-id="3e38a-529">Önek = parametre adı</span><span class="sxs-lookup"><span data-stu-id="3e38a-529">Prefix = parameter name</span></span>

<span data-ttu-id="3e38a-530">Bağlanacak model adlı bir parametre ise `instructorToUpdate` :</span><span class="sxs-lookup"><span data-stu-id="3e38a-530">If the model to be bound is a parameter named `instructorToUpdate`:</span></span>

```csharp
public IActionResult OnPost(int? id, Instructor instructorToUpdate)
```

<span data-ttu-id="3e38a-531">Model bağlama, anahtar kaynaklarına bakarak başlar `instructorToUpdate.ID` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-531">Model binding starts by looking through the sources for the key `instructorToUpdate.ID`.</span></span> <span data-ttu-id="3e38a-532">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-532">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="prefix--property-name"></a><span data-ttu-id="3e38a-533">Önek = Özellik adı</span><span class="sxs-lookup"><span data-stu-id="3e38a-533">Prefix = property name</span></span>

<span data-ttu-id="3e38a-534">Bağlanacak model, `Instructor` denetleyicinin veya sınıfın adında bir özelliktir `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="3e38a-534">If the model to be bound is a property named `Instructor` of the controller or `PageModel` class:</span></span>

```csharp
[BindProperty]
public Instructor Instructor { get; set; }
```

<span data-ttu-id="3e38a-535">Model bağlama, anahtar kaynaklarına bakarak başlar `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-535">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="3e38a-536">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-536">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="custom-prefix"></a><span data-ttu-id="3e38a-537">Özel ön ek</span><span class="sxs-lookup"><span data-stu-id="3e38a-537">Custom prefix</span></span>

<span data-ttu-id="3e38a-538">Bağlanacak model adlı bir parametre ise `instructorToUpdate` ve bir `Bind` öznitelik `Instructor` önek olarak belirtirse:</span><span class="sxs-lookup"><span data-stu-id="3e38a-538">If the model to be bound is a parameter named `instructorToUpdate` and a `Bind` attribute specifies `Instructor` as the prefix:</span></span>

```csharp
public IActionResult OnPost(
    int? id, [Bind(Prefix = "Instructor")] Instructor instructorToUpdate)
```

<span data-ttu-id="3e38a-539">Model bağlama, anahtar kaynaklarına bakarak başlar `Instructor.ID` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-539">Model binding starts by looking through the sources for the key `Instructor.ID`.</span></span> <span data-ttu-id="3e38a-540">Bu bulunamazsa, `ID` öneki olmadan arar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-540">If that isn't found, it looks for `ID` without a prefix.</span></span>

### <a name="attributes-for-complex-type-targets"></a><span data-ttu-id="3e38a-541">Karmaşık tür hedefleri için öznitelikler</span><span class="sxs-lookup"><span data-stu-id="3e38a-541">Attributes for complex type targets</span></span>

<span data-ttu-id="3e38a-542">Karmaşık türlerin model bağlamasını denetlemek için birkaç yerleşik öznitelik mevcuttur:</span><span class="sxs-lookup"><span data-stu-id="3e38a-542">Several built-in attributes are available for controlling model binding of complex types:</span></span>

* `[BindRequired]`
* `[BindNever]`
* `[Bind]`

> [!NOTE]
> <span data-ttu-id="3e38a-543">Bu öznitelikler, gönderilen form verileri değer kaynağı olduğunda model bağlamayı etkiler.</span><span class="sxs-lookup"><span data-stu-id="3e38a-543">These attributes affect model binding when posted form data is the source of values.</span></span> <span data-ttu-id="3e38a-544">Bunlar, gönderilen JSON ve XML istek gövdelerini işleyen giriş formatlayıcıları 'nı etkilemez.</span><span class="sxs-lookup"><span data-stu-id="3e38a-544">They do not affect input formatters, which process posted JSON and XML request bodies.</span></span> <span data-ttu-id="3e38a-545">Giriş biçimleri [Bu makalenin ilerleyen kısımlarında](#input-formatters)açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-545">Input formatters are explained [later in this article](#input-formatters).</span></span>
>
> <span data-ttu-id="3e38a-546">Ayrıca bkz `[Required]` . [model doğrulama](xref:mvc/models/validation#required-attribute)içindeki öznitelik tartışması.</span><span class="sxs-lookup"><span data-stu-id="3e38a-546">See also the discussion of the `[Required]` attribute in [Model validation](xref:mvc/models/validation#required-attribute).</span></span>

### <a name="bindrequired-attribute"></a><span data-ttu-id="3e38a-547">[BindRequired] özniteliği</span><span class="sxs-lookup"><span data-stu-id="3e38a-547">[BindRequired] attribute</span></span>

<span data-ttu-id="3e38a-548">, Yöntem parametrelerine değil yalnızca model özelliklerine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-548">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="3e38a-549">Modelin özelliği için bağlama gerçekleşmemişse model bağlamasının model durumu hatası eklemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="3e38a-549">Causes model binding to add a model state error if binding cannot occur for a model's property.</span></span> <span data-ttu-id="3e38a-550">Aşağıda bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-550">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithCollection.cs?name=snippet_BindRequired&highlight=8-9)]

### <a name="bindnever-attribute"></a><span data-ttu-id="3e38a-551">[Bindhiç] özniteliği</span><span class="sxs-lookup"><span data-stu-id="3e38a-551">[BindNever] attribute</span></span>

<span data-ttu-id="3e38a-552">, Yöntem parametrelerine değil yalnızca model özelliklerine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-552">Can only be applied to model properties, not to method parameters.</span></span> <span data-ttu-id="3e38a-553">Model bağlamasının model özelliğini değiştirmesini engeller.</span><span class="sxs-lookup"><span data-stu-id="3e38a-553">Prevents model binding from setting a model's property.</span></span> <span data-ttu-id="3e38a-554">Aşağıda bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-554">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Models/InstructorWithDictionary.cs?name=snippet_BindNever&highlight=3-4)]

### <a name="bind-attribute"></a><span data-ttu-id="3e38a-555">[Bind] özniteliği</span><span class="sxs-lookup"><span data-stu-id="3e38a-555">[Bind] attribute</span></span>

<span data-ttu-id="3e38a-556">, Bir sınıfa veya yöntem parametresine uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-556">Can be applied to a class or a method parameter.</span></span> <span data-ttu-id="3e38a-557">Model bağlamasındaki bir modelin hangi özelliklerinin dahil edileceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-557">Specifies which properties of a model should be included in model binding.</span></span>

<span data-ttu-id="3e38a-558">Aşağıdaki örnekte, `Instructor` herhangi bir işleyici veya eylem yöntemi çağrıldığında yalnızca modelin belirtilen özellikleri bağlanır:</span><span class="sxs-lookup"><span data-stu-id="3e38a-558">In the following example, only the specified properties of the `Instructor` model are bound when any handler or action method is called:</span></span>

```csharp
[Bind("LastName,FirstMidName,HireDate")]
public class Instructor
```

<span data-ttu-id="3e38a-559">Aşağıdaki örnekte, `Instructor` yöntemi çağrıldığında yalnızca modelin belirtilen özellikleri bağlanır `OnPost` :</span><span class="sxs-lookup"><span data-stu-id="3e38a-559">In the following example, only the specified properties of the `Instructor` model are bound when the `OnPost` method is called:</span></span>

```csharp
[HttpPost]
public IActionResult OnPost([Bind("LastName,FirstMidName,HireDate")] Instructor instructor)
```

<span data-ttu-id="3e38a-560">`[Bind]`Özniteliği, *oluşturma* senaryolarında fazla nakline karşı korumak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-560">The `[Bind]` attribute can be used to protect against overposting in *create* scenarios.</span></span> <span data-ttu-id="3e38a-561">Dışlanan Özellikler null ya da boş değer olarak ayarlandığı için, düzenleme senaryolarında iyi çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="3e38a-561">It doesn't work well in edit scenarios because excluded properties are set to null or a default value instead of being left unchanged.</span></span> <span data-ttu-id="3e38a-562">Fazla naklin savunma için, öznitelik yerine görüntüleme modelleri önerilir `[Bind]` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-562">For defense against overposting, view models are recommended rather than the `[Bind]` attribute.</span></span> <span data-ttu-id="3e38a-563">Daha fazla bilgi için bkz. fazla [nakil hakkında güvenlik NOI](xref:data/ef-mvc/crud#security-note-about-overposting).</span><span class="sxs-lookup"><span data-stu-id="3e38a-563">For more information, see [Security note about overposting](xref:data/ef-mvc/crud#security-note-about-overposting).</span></span>

## <a name="collections"></a><span data-ttu-id="3e38a-564">Koleksiyonlar</span><span class="sxs-lookup"><span data-stu-id="3e38a-564">Collections</span></span>

<span data-ttu-id="3e38a-565">Basit türlerin koleksiyonları olan hedefler için model bağlama, *parameter_name* veya *property_name* ile eşleşmeleri arar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-565">For targets that are collections of simple types, model binding looks for matches to *parameter_name* or *property_name* .</span></span> <span data-ttu-id="3e38a-566">Eşleşme bulunmazsa, ön ek olmadan desteklenen biçimlerden birini arar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-566">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="3e38a-567">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="3e38a-567">For example:</span></span>

* <span data-ttu-id="3e38a-568">Bağlanacak parametrenin adlı bir dizi olduğunu varsayalım `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="3e38a-568">Suppose the parameter to be bound is an array named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, int[] selectedCourses)
  ```

* <span data-ttu-id="3e38a-569">Form veya sorgu dizesi verileri aşağıdaki biçimlerden birinde olabilir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-569">Form or query string data can be in one of the following formats:</span></span>
   
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

* <span data-ttu-id="3e38a-570">Aşağıdaki biçim yalnızca form verilerinde desteklenir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-570">The following format is supported only in form data:</span></span>

  ```
  selectedCourses[]=1050&selectedCourses[]=2000
  ```

* <span data-ttu-id="3e38a-571">Önceki örnek biçimlerinin hepsi için model bağlama iki öğe dizisini `selectedCourses` parametreye geçirir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-571">For all of the preceding example formats, model binding passes an array of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="3e38a-572">Selectedkurslar [0] = 1050</span><span class="sxs-lookup"><span data-stu-id="3e38a-572">selectedCourses[0]=1050</span></span>
  * <span data-ttu-id="3e38a-573">Selectedkurslar [1] = 2000</span><span class="sxs-lookup"><span data-stu-id="3e38a-573">selectedCourses[1]=2000</span></span>

  <span data-ttu-id="3e38a-574">Alt simge numaralarını kullanan veri biçimleri (... [0]... [1]...) sıfırdan başlayarak sıralı olarak numaralandırıldıklarından emin olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-574">Data formats that use subscript numbers (... [0] ... [1] ...) must ensure that they are numbered sequentially starting at zero.</span></span> <span data-ttu-id="3e38a-575">Alt simge numaralandırmasında boşluk varsa, boşluklardan sonraki tüm öğeler yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-575">If there are any gaps in subscript numbering, all items after the gap are ignored.</span></span> <span data-ttu-id="3e38a-576">Örneğin, alt simgeler 0 ve 1 yerine 0 ve 2 ise ikinci öğe yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-576">For example, if the subscripts are 0 and 2 instead of 0 and 1, the second item is ignored.</span></span>

## <a name="dictionaries"></a><span data-ttu-id="3e38a-577">Sözlükler</span><span class="sxs-lookup"><span data-stu-id="3e38a-577">Dictionaries</span></span>

<span data-ttu-id="3e38a-578">`Dictionary`Hedefler için, model bağlama *parameter_name* veya *property_name* eşleşme arar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-578">For `Dictionary` targets, model binding looks for matches to *parameter_name* or *property_name* .</span></span> <span data-ttu-id="3e38a-579">Eşleşme bulunmazsa, ön ek olmadan desteklenen biçimlerden birini arar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-579">If no match is found, it looks for one of the supported formats without the prefix.</span></span> <span data-ttu-id="3e38a-580">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="3e38a-580">For example:</span></span>

* <span data-ttu-id="3e38a-581">Hedef parametrenin bir adlandırılmış olduğunu varsayalım `Dictionary<int, string>` `selectedCourses` :</span><span class="sxs-lookup"><span data-stu-id="3e38a-581">Suppose the target parameter is a `Dictionary<int, string>` named `selectedCourses`:</span></span>

  ```csharp
  public IActionResult OnPost(int? id, Dictionary<int, string> selectedCourses)
  ```

* <span data-ttu-id="3e38a-582">Postalanan form veya sorgu dizesi verileri aşağıdaki örneklerden birine benzeyebilir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-582">The posted form or query string data can look like one of the following examples:</span></span>

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

* <span data-ttu-id="3e38a-583">Önceki örnek biçimlerinin hepsi için model bağlama iki öğenin bir sözlüğünü `selectedCourses` parametreye geçirir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-583">For all of the preceding example formats, model binding passes a dictionary of two items to the `selectedCourses` parameter:</span></span>

  * <span data-ttu-id="3e38a-584">Selectedkurslar ["1050"] = "Chemistry"</span><span class="sxs-lookup"><span data-stu-id="3e38a-584">selectedCourses["1050"]="Chemistry"</span></span>
  * <span data-ttu-id="3e38a-585">Selectedkurslar ["2000"] = "Ekonomiks"</span><span class="sxs-lookup"><span data-stu-id="3e38a-585">selectedCourses["2000"]="Economics"</span></span>

<a name="glob"></a>

## <a name="globalization-behavior-of-model-binding-route-data-and-query-strings"></a><span data-ttu-id="3e38a-586">Model bağlama yolu verileri ve sorgu dizeleri için Genelleştirme davranışı</span><span class="sxs-lookup"><span data-stu-id="3e38a-586">Globalization behavior of model binding route data and query strings</span></span>

<span data-ttu-id="3e38a-587">ASP.NET Core yol değeri sağlayıcısı ve sorgu dizesi değer sağlayıcısı:</span><span class="sxs-lookup"><span data-stu-id="3e38a-587">The ASP.NET Core route value provider and query string value provider:</span></span>

* <span data-ttu-id="3e38a-588">Değerleri sabit kültür olarak değerlendirin.</span><span class="sxs-lookup"><span data-stu-id="3e38a-588">Treat values as invariant culture.</span></span>
* <span data-ttu-id="3e38a-589">URL 'Lerin kültür sabiti olmasını bekler.</span><span class="sxs-lookup"><span data-stu-id="3e38a-589">Expect that URLs are culture-invariant.</span></span>

<span data-ttu-id="3e38a-590">Buna karşılık, form verilerinden gelen değerler kültüre duyarlı bir dönüştürmeye gider.</span><span class="sxs-lookup"><span data-stu-id="3e38a-590">In contrast, values coming from form data undergo a culture-sensitive conversion.</span></span> <span data-ttu-id="3e38a-591">Bu, URL 'Lerin yerel ayarlarda paylaşılabilir olması için tasarımdır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-591">This is by design so that URLs are shareable across locales.</span></span>

<span data-ttu-id="3e38a-592">ASP.NET Core yol değeri sağlayıcısını ve sorgu dizesi değeri sağlayıcısını, kültüre duyarlı bir dönüşüme dönüştürmek için:</span><span class="sxs-lookup"><span data-stu-id="3e38a-592">To make the ASP.NET Core route value provider and query string value provider undergo a culture-sensitive conversion:</span></span>

* <span data-ttu-id="3e38a-593">Şuradan devralma <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span><span class="sxs-lookup"><span data-stu-id="3e38a-593">Inherit from <xref:Microsoft.AspNetCore.Mvc.ModelBinding.IValueProviderFactory></span></span>
* <span data-ttu-id="3e38a-594">[QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) veya [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs) adresinden kodu kopyalayın</span><span class="sxs-lookup"><span data-stu-id="3e38a-594">Copy the code from [QueryStringValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs) or [RouteValueValueProviderFactory](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/ModelBinding/RouteValueProviderFactory.cs)</span></span>
* <span data-ttu-id="3e38a-595">Değer sağlayıcısı oluşturucusuna geçirilen [kültür değerini](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) [CultureInfo. CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture) ile değiştirin</span><span class="sxs-lookup"><span data-stu-id="3e38a-595">Replace the [culture value](https://github.com/dotnet/AspNetCore/blob/e625fe29b049c60242e8048b4ea743cca65aa7b5/src/Mvc/Mvc.Core/src/ModelBinding/QueryStringValueProviderFactory.cs#L30) passed to the value provider constructor with [CultureInfo.CurrentCulture](xref:System.Globalization.CultureInfo.CurrentCulture)</span></span>
* <span data-ttu-id="3e38a-596">MVC seçeneklerinde varsayılan değer sağlayıcı fabrikasını yeni bir değerle değiştirin:</span><span class="sxs-lookup"><span data-stu-id="3e38a-596">Replace the default value provider factory in MVC options with your new one:</span></span>

[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet)]
[!code-csharp[](model-binding/samples_snapshot/2.x/Startup.cs?name=snippet1)]

## <a name="special-data-types"></a><span data-ttu-id="3e38a-597">Özel veri türleri</span><span class="sxs-lookup"><span data-stu-id="3e38a-597">Special data types</span></span>

<span data-ttu-id="3e38a-598">Model bağlamanın işleyebileceği bazı özel veri türleri vardır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-598">There are some special data types that model binding can handle.</span></span>

### <a name="iformfile-and-iformfilecollection"></a><span data-ttu-id="3e38a-599">Iformfile ve ıformfilecollection</span><span class="sxs-lookup"><span data-stu-id="3e38a-599">IFormFile and IFormFileCollection</span></span>

<span data-ttu-id="3e38a-600">HTTP isteğine eklenen karşıya yüklenen dosya.</span><span class="sxs-lookup"><span data-stu-id="3e38a-600">An uploaded file included in the HTTP request.</span></span>  <span data-ttu-id="3e38a-601">Ayrıca, `IEnumerable<IFormFile>` birden çok dosya için de desteklenir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-601">Also supported is `IEnumerable<IFormFile>` for multiple files.</span></span>

### <a name="cancellationtoken"></a><span data-ttu-id="3e38a-602">CancellationToken</span><span class="sxs-lookup"><span data-stu-id="3e38a-602">CancellationToken</span></span>

<span data-ttu-id="3e38a-603">Zaman uyumsuz denetleyicilerde etkinliği iptal etmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-603">Used to cancel activity in asynchronous controllers.</span></span>

### <a name="formcollection"></a><span data-ttu-id="3e38a-604">Form koleksiyonu</span><span class="sxs-lookup"><span data-stu-id="3e38a-604">FormCollection</span></span>

<span data-ttu-id="3e38a-605">Postalanan form verilerinden tüm değerleri almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-605">Used to retrieve all the values from posted form data.</span></span>

## <a name="input-formatters"></a><span data-ttu-id="3e38a-606">Giriş biçimleri</span><span class="sxs-lookup"><span data-stu-id="3e38a-606">Input formatters</span></span>

<span data-ttu-id="3e38a-607">İstek gövdesindeki veriler JSON, XML veya başka bir biçimde olabilir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-607">Data in the request body can be in JSON, XML, or some other format.</span></span> <span data-ttu-id="3e38a-608">Model bağlama, bu verileri ayrıştırmak için belirli bir içerik türünü işlemek üzere yapılandırılmış bir *giriş biçimlendiricisi* kullanır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-608">To parse this data, model binding uses an *input formatter* that is configured to handle a particular content type.</span></span> <span data-ttu-id="3e38a-609">Varsayılan olarak, ASP.NET Core JSON verilerini işlemek için JSON tabanlı giriş formatlayıcıları içerir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-609">By default, ASP.NET Core includes JSON based input formatters for handling JSON data.</span></span> <span data-ttu-id="3e38a-610">Diğer içerik türleri için başka biçim ekleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3e38a-610">You can add other formatters for other content types.</span></span>

<span data-ttu-id="3e38a-611">ASP.NET Core, [tüketen](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) özniteliğine bağlı olarak giriş formatlayıcıları seçer.</span><span class="sxs-lookup"><span data-stu-id="3e38a-611">ASP.NET Core selects input formatters based on the [Consumes](xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute) attribute.</span></span> <span data-ttu-id="3e38a-612">Hiçbir öznitelik yoksa, [Content-Type üst bilgisini](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html)kullanır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-612">If no attribute is present, it uses the [Content-Type header](https://www.w3.org/Protocols/rfc1341/4_Content-Type.html).</span></span>

<span data-ttu-id="3e38a-613">Yerleşik XML girişi formatlayıcıları 'nı kullanmak için:</span><span class="sxs-lookup"><span data-stu-id="3e38a-613">To use the built-in XML input formatters:</span></span>

* <span data-ttu-id="3e38a-614">`Microsoft.AspNetCore.Mvc.Formatters.Xml`NuGet paketini yükler.</span><span class="sxs-lookup"><span data-stu-id="3e38a-614">Install the `Microsoft.AspNetCore.Mvc.Formatters.Xml` NuGet package.</span></span>

* <span data-ttu-id="3e38a-615">İçinde `Startup.ConfigureServices` , veya öğesini çağırın <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*> .</span><span class="sxs-lookup"><span data-stu-id="3e38a-615">In `Startup.ConfigureServices`, call <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlSerializerFormatters*> or <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcCoreBuilderExtensions.AddXmlDataContractSerializerFormatters*>.</span></span>

  [!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=9)]

* <span data-ttu-id="3e38a-616">Özniteliği, `Consumes` istek GÖVDESINDE XML beklemeniz gereken denetleyici sınıflarına veya eylem yöntemlerine uygulayın.</span><span class="sxs-lookup"><span data-stu-id="3e38a-616">Apply the `Consumes` attribute to controller classes or action methods that should expect XML in the request body.</span></span>

  ```csharp
  [HttpPost]
  [Consumes("application/xml")]
  public ActionResult<Pet> Create(Pet pet)
  ```

  <span data-ttu-id="3e38a-617">Daha fazla bilgi için bkz. [XML serileştirme tanıtımı](/dotnet/standard/serialization/introducing-xml-serialization).</span><span class="sxs-lookup"><span data-stu-id="3e38a-617">For more information, see [Introducing XML Serialization](/dotnet/standard/serialization/introducing-xml-serialization).</span></span>

## <a name="exclude-specified-types-from-model-binding"></a><span data-ttu-id="3e38a-618">Belirtilen türleri model bağlamalarından Dışla</span><span class="sxs-lookup"><span data-stu-id="3e38a-618">Exclude specified types from model binding</span></span>

<span data-ttu-id="3e38a-619">Model bağlama ve doğrulama sistemlerinin davranışı [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata)tarafından yönlendiriliyor.</span><span class="sxs-lookup"><span data-stu-id="3e38a-619">The model binding and validation systems' behavior is driven by [ModelMetadata](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.modelmetadata).</span></span> <span data-ttu-id="3e38a-620">`ModelMetadata` [Mvcoptions. modelmetadatadetails sağlayıcılarına](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders)bir ayrıntı sağlayıcısı ekleyerek özelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3e38a-620">You can customize `ModelMetadata` by adding a details provider to [MvcOptions.ModelMetadataDetailsProviders](xref:Microsoft.AspNetCore.Mvc.MvcOptions.ModelMetadataDetailsProviders).</span></span> <span data-ttu-id="3e38a-621">Yerleşik Ayrıntılar sağlayıcıları, belirtilen türler için model bağlamayı veya doğrulamayı devre dışı bırakmak üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3e38a-621">Built-in details providers are available for disabling model binding or validation for specified types.</span></span>

<span data-ttu-id="3e38a-622">Belirtilen türdeki tüm modellerdeki model bağlamayı devre dışı bırakmak için içine bir ekleyin <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-622">To disable model binding on all models of a specified type, add an <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.ExcludeBindingMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3e38a-623">Örneğin, türü tüm modeller üzerinde model bağlamayı devre dışı bırakmak için `System.Version` :</span><span class="sxs-lookup"><span data-stu-id="3e38a-623">For example, to disable model binding on all models of type `System.Version`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=4-5)]

<span data-ttu-id="3e38a-624">Belirtilen türdeki özelliklerde doğrulamayı devre dışı bırakmak için içine bir ekleyin <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-624">To disable validation on properties of a specified type, add a <xref:Microsoft.AspNetCore.Mvc.ModelBinding.SuppressChildValidationMetadataProvider> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3e38a-625">Örneğin, türündeki özelliklerde doğrulamayı devre dışı bırakmak için `System.Guid` :</span><span class="sxs-lookup"><span data-stu-id="3e38a-625">For example, to disable validation on properties of type `System.Guid`:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Startup.cs?name=snippet_ValueProvider&highlight=6-7)]

## <a name="custom-model-binders"></a><span data-ttu-id="3e38a-626">Özel model ciltleri</span><span class="sxs-lookup"><span data-stu-id="3e38a-626">Custom model binders</span></span>

<span data-ttu-id="3e38a-627">Özel bir model cildi yazarak ve `[ModelBinder]` belirli bir hedef için seçmek üzere özniteliğini kullanarak model bağlamayı genişletebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="3e38a-627">You can extend model binding by writing a custom model binder and using the `[ModelBinder]` attribute to select it for a given target.</span></span> <span data-ttu-id="3e38a-628">[Özel model bağlama](xref:mvc/advanced/custom-model-binding)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="3e38a-628">Learn more about [custom model binding](xref:mvc/advanced/custom-model-binding).</span></span>

## <a name="manual-model-binding"></a><span data-ttu-id="3e38a-629">El ile model bağlama</span><span class="sxs-lookup"><span data-stu-id="3e38a-629">Manual model binding</span></span>

<span data-ttu-id="3e38a-630">Model bağlama yöntemi kullanılarak el ile çağrılabilir <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> .</span><span class="sxs-lookup"><span data-stu-id="3e38a-630">Model binding can be invoked manually by using the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.TryUpdateModelAsync*> method.</span></span> <span data-ttu-id="3e38a-631">Yöntemi hem hem de sınıflarında tanımlanmıştır `ControllerBase` `PageModel` .</span><span class="sxs-lookup"><span data-stu-id="3e38a-631">The method is defined on both `ControllerBase` and `PageModel` classes.</span></span> <span data-ttu-id="3e38a-632">Yöntem aşırı yüklemeleri, kullanılacak öneki ve değer sağlayıcısını belirtmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-632">Method overloads let you specify the prefix and value provider to use.</span></span> <span data-ttu-id="3e38a-633">Yöntemi `false` model bağlamanın başarısız olup olmadığını döndürür.</span><span class="sxs-lookup"><span data-stu-id="3e38a-633">The method returns `false` if model binding fails.</span></span> <span data-ttu-id="3e38a-634">Aşağıda bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="3e38a-634">Here's an example:</span></span>

[!code-csharp[](model-binding/samples/2.x/ModelBindingSample/Pages/InstructorsWithCollection/Create.cshtml.cs?name=snippet_TryUpdate&highlight=1-4)]

## <a name="fromservices-attribute"></a><span data-ttu-id="3e38a-635">[FromServices] özniteliği</span><span class="sxs-lookup"><span data-stu-id="3e38a-635">[FromServices] attribute</span></span>

<span data-ttu-id="3e38a-636">Bu özniteliğin adı, bir veri kaynağı belirten model bağlama özniteliklerinin düzeniyle uyar.</span><span class="sxs-lookup"><span data-stu-id="3e38a-636">This attribute's name follows the pattern of model binding attributes that specify a data source.</span></span> <span data-ttu-id="3e38a-637">Ancak bir değer sağlayıcısından veri bağlama hakkında bilgi yoktur.</span><span class="sxs-lookup"><span data-stu-id="3e38a-637">But it's not about binding data from a value provider.</span></span> <span data-ttu-id="3e38a-638">[Bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısından bir türün bir örneğini alır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-638">It gets an instance of a type from the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="3e38a-639">Amacı, yalnızca belirli bir yöntem çağrılırsa bir hizmete ihtiyacınız olduğunda Oluşturucu ekleme için bir alternatif sağlamaktır.</span><span class="sxs-lookup"><span data-stu-id="3e38a-639">Its purpose is to provide an alternative to constructor injection for when you need a service only if a particular method is called.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3e38a-640">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3e38a-640">Additional resources</span></span>

* <xref:mvc/models/validation>
* <xref:mvc/advanced/custom-model-binding>

::: moniker-end
