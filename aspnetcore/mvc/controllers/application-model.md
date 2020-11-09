---
title: ASP.NET Core 'de uygulama modeliyle çalışma
author: ardalis
description: MVC öğelerinin ASP.NET Core nasıl davranacağını değiştirmek için uygulama modelini okumayı ve işlemeyi öğrenin.
ms.author: riande
ms.date: 12/05/2019
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
uid: mvc/controllers/application-model
ms.openlocfilehash: a7a654eb43c0dbf375af911d8d5353ac4c04a825
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060943"
---
# <a name="work-with-the-application-model-in-aspnet-core"></a><span data-ttu-id="1e67c-103">ASP.NET Core 'de uygulama modeliyle çalışma</span><span class="sxs-lookup"><span data-stu-id="1e67c-103">Work with the application model in ASP.NET Core</span></span>

<span data-ttu-id="1e67c-104">[Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="1e67c-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="1e67c-105">ASP.NET Core MVC, MVC uygulamasının bileşenlerini temsil eden bir *uygulama modeli* tanımlar.</span><span class="sxs-lookup"><span data-stu-id="1e67c-105">ASP.NET Core MVC defines an *application model* representing the components of an MVC app.</span></span> <span data-ttu-id="1e67c-106">MVC öğelerinin nasıl davrandığını değiştirmek için bu modeli okuyabilir ve düzenleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1e67c-106">You can read and manipulate this model to modify how MVC elements behave.</span></span> <span data-ttu-id="1e67c-107">Varsayılan olarak, MVC, hangi sınıfların denetleyici olduğunu belirlemek için bazı kuralları izler, bu sınıfların hangi yöntemlerin eylem olduğunu ve parametrelerin ve yönlendirmenin nasıl davranacağını sağlar.</span><span class="sxs-lookup"><span data-stu-id="1e67c-107">By default, MVC follows certain conventions to determine which classes are considered to be controllers, which methods on those classes are actions, and how parameters and routing behave.</span></span> <span data-ttu-id="1e67c-108">Kendi kurallarınızı oluşturarak ve bunları küresel olarak veya öznitelik olarak uygulayarak, uygulamanızın gereksinimlerine uyacak şekilde bu davranışı özelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1e67c-108">You can customize this behavior to suit your app's needs by creating your own conventions and applying them globally or as attributes.</span></span>

## <a name="models-and-providers"></a><span data-ttu-id="1e67c-109">Modeller ve sağlayıcılar</span><span class="sxs-lookup"><span data-stu-id="1e67c-109">Models and Providers</span></span>

<span data-ttu-id="1e67c-110">ASP.NET Core MVC uygulama modeli, hem soyut arabirimleri hem de bir MVC uygulamasını tanımlayan somut uygulama sınıflarını içerir.</span><span class="sxs-lookup"><span data-stu-id="1e67c-110">The ASP.NET Core MVC application model include both abstract interfaces and concrete implementation classes that describe an MVC application.</span></span> <span data-ttu-id="1e67c-111">Bu model, uygulamanın denetleyicilerini, eylemlerini, eylem parametrelerini, yolları ve filtreleri varsayılan kurallara göre bulma hakkında MVC 'nin sonucudur.</span><span class="sxs-lookup"><span data-stu-id="1e67c-111">This model is the result of MVC discovering the app's controllers, actions, action parameters, routes, and filters according to default conventions.</span></span> <span data-ttu-id="1e67c-112">Uygulama modeliyle çalışarak, uygulamanızı varsayılan MVC davranışından farklı kurallara göre izlemek için değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1e67c-112">By working with the application model, you can modify your app to follow different conventions from the default MVC behavior.</span></span> <span data-ttu-id="1e67c-113">Parametrelerin, adların, yolların ve filtrelerin hepsi, Eylemler ve denetleyiciler için yapılandırma verileri olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1e67c-113">The parameters, names, routes, and filters are all used as configuration data for actions and controllers.</span></span>

<span data-ttu-id="1e67c-114">ASP.NET Core MVC uygulama modeli aşağıdaki yapıya sahiptir:</span><span class="sxs-lookup"><span data-stu-id="1e67c-114">The ASP.NET Core MVC Application Model has the following structure:</span></span>

* <span data-ttu-id="1e67c-115">ApplicationModel</span><span class="sxs-lookup"><span data-stu-id="1e67c-115">ApplicationModel</span></span>
  * <span data-ttu-id="1e67c-116">Denetleyiciler (ControllerModel)</span><span class="sxs-lookup"><span data-stu-id="1e67c-116">Controllers (ControllerModel)</span></span>
    * <span data-ttu-id="1e67c-117">Eylemler (ActionModel)</span><span class="sxs-lookup"><span data-stu-id="1e67c-117">Actions (ActionModel)</span></span>
      * <span data-ttu-id="1e67c-118">Parametreler (ParameterModel)</span><span class="sxs-lookup"><span data-stu-id="1e67c-118">Parameters (ParameterModel)</span></span>

<span data-ttu-id="1e67c-119">Modelin her düzeyinin ortak bir koleksiyona erişimi vardır `Properties` ve alt düzeyler hiyerarşideki daha yüksek düzeyler tarafından ayarlanan özellik değerlerine erişebilir ve üzerine yazabilir.</span><span class="sxs-lookup"><span data-stu-id="1e67c-119">Each level of the model has access to a common `Properties` collection, and lower levels can access and overwrite property values set by higher levels in the hierarchy.</span></span> <span data-ttu-id="1e67c-120">Eylemler oluşturulduğunda Özellikler ' de kalıcı hale getirilir `ActionDescriptor.Properties` .</span><span class="sxs-lookup"><span data-stu-id="1e67c-120">The properties are persisted to the `ActionDescriptor.Properties` when the actions are created.</span></span> <span data-ttu-id="1e67c-121">Bir istek işlenirken, bir kurala eklenen veya değiştirilen tüm özelliklere aracılığıyla erişilebilir `ActionContext.ActionDescriptor.Properties` .</span><span class="sxs-lookup"><span data-stu-id="1e67c-121">Then when a request is being handled, any properties a convention added or modified can be accessed through `ActionContext.ActionDescriptor.Properties`.</span></span> <span data-ttu-id="1e67c-122">Özellikleri kullanmak, filtrelerinizi, model ciltlerinizi, vb. her eylem temelinde yapılandırmanın harika bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="1e67c-122">Using properties is a great way to configure your filters, model binders, etc. on a per-action basis.</span></span>

> [!NOTE]
> <span data-ttu-id="1e67c-123">`ActionDescriptor.Properties`Uygulama başlatma işlemi tamamlandıktan sonra koleksiyon iş parçacığı güvenli (yazma için) değil.</span><span class="sxs-lookup"><span data-stu-id="1e67c-123">The `ActionDescriptor.Properties` collection isn't thread safe (for writes) once app startup has finished.</span></span> <span data-ttu-id="1e67c-124">Kurallar, bu koleksiyona güvenle veri eklemenin en iyi yoludur.</span><span class="sxs-lookup"><span data-stu-id="1e67c-124">Conventions are the best way to safely add data to this collection.</span></span>

### <a name="iapplicationmodelprovider"></a><span data-ttu-id="1e67c-125">Iapplicationmodelprovider</span><span class="sxs-lookup"><span data-stu-id="1e67c-125">IApplicationModelProvider</span></span>

<span data-ttu-id="1e67c-126">ASP.NET Core MVC, [ıapplicationmodelprovider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) arabirimi tarafından tanımlanan bir sağlayıcı modeli kullanarak uygulama modelini yükler.</span><span class="sxs-lookup"><span data-stu-id="1e67c-126">ASP.NET Core MVC loads the application model using a provider pattern, defined by the [IApplicationModelProvider](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelprovider) interface.</span></span> <span data-ttu-id="1e67c-127">Bu bölümde, bu sağlayıcının nasıl çalıştığı hakkında bazı iç uygulama ayrıntıları ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="1e67c-127">This section covers some of the internal implementation details of how this provider functions.</span></span> <span data-ttu-id="1e67c-128">Bu gelişmiş bir konudur. uygulama modelinden yararlanan birçok uygulama, kurallara göre çalışır.</span><span class="sxs-lookup"><span data-stu-id="1e67c-128">This is an advanced topic - most apps that leverage the application model should do so by working with conventions.</span></span>

<span data-ttu-id="1e67c-129">`IApplicationModelProvider`Her uygulamayla, özelliği temel alınarak artan düzende çağırılmadan, "Wrap" arabiriminin uygulamaları birbirini bir diğeri `OnProvidersExecuting` `Order` .</span><span class="sxs-lookup"><span data-stu-id="1e67c-129">Implementations of the `IApplicationModelProvider` interface "wrap" one another, with each implementation calling `OnProvidersExecuting` in ascending order based on its `Order` property.</span></span> <span data-ttu-id="1e67c-130">`OnProvidersExecuted`Yöntemi daha sonra ters sırada çağrılır.</span><span class="sxs-lookup"><span data-stu-id="1e67c-130">The `OnProvidersExecuted` method is then called in reverse order.</span></span> <span data-ttu-id="1e67c-131">Framework çeşitli sağlayıcıları tanımlar:</span><span class="sxs-lookup"><span data-stu-id="1e67c-131">The framework defines several providers:</span></span>

<span data-ttu-id="1e67c-132">First ( `Order=-1000` ):</span><span class="sxs-lookup"><span data-stu-id="1e67c-132">First (`Order=-1000`):</span></span>

* [`DefaultApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.defaultapplicationmodelprovider)

<span data-ttu-id="1e67c-133">Sonra ( `Order=-990` ):</span><span class="sxs-lookup"><span data-stu-id="1e67c-133">Then (`Order=-990`):</span></span>

* [`AuthorizationApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.internal.authorizationapplicationmodelprovider)
* [`CorsApplicationModelProvider`](/dotnet/api/microsoft.aspnetcore.mvc.cors.internal.corsapplicationmodelprovider)

> [!NOTE]
> <span data-ttu-id="1e67c-134">İçin aynı değere sahip iki sağlayıcının `Order` çağrılmasıyla ilgili sıralama tanımsız ve bu nedenle güvenmemelidir.</span><span class="sxs-lookup"><span data-stu-id="1e67c-134">The order in which two providers with the same value for `Order` are called is undefined, and therefore shouldn't be relied upon.</span></span>

> [!NOTE]
> <span data-ttu-id="1e67c-135">`IApplicationModelProvider` , çerçeve yazarlarının genişlemesine yönelik gelişmiş bir kavramdır.</span><span class="sxs-lookup"><span data-stu-id="1e67c-135">`IApplicationModelProvider` is an advanced concept for framework authors to extend.</span></span> <span data-ttu-id="1e67c-136">Genel olarak, uygulamalar, sağlayıcıları kullanması gereken kuralları ve çerçeveleri kullanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="1e67c-136">In general, apps should use conventions and frameworks should use providers.</span></span> <span data-ttu-id="1e67c-137">Anahtar ayrımı, sağlayıcıların kuralların önüne her zaman çalıştırılacağı bir değer.</span><span class="sxs-lookup"><span data-stu-id="1e67c-137">The key distinction is that providers always run before conventions.</span></span>

<span data-ttu-id="1e67c-138">, `DefaultApplicationModelProvider` ASP.NET Core MVC tarafından kullanılan varsayılan davranışların çoğunu belirler.</span><span class="sxs-lookup"><span data-stu-id="1e67c-138">The `DefaultApplicationModelProvider` establishes many of the default behaviors used by ASP.NET Core MVC.</span></span> <span data-ttu-id="1e67c-139">Sorumlulukları şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="1e67c-139">Its responsibilities include:</span></span>

* <span data-ttu-id="1e67c-140">Bağlama genel filtreler ekleme</span><span class="sxs-lookup"><span data-stu-id="1e67c-140">Adding global filters to the context</span></span>
* <span data-ttu-id="1e67c-141">Bağlama denetleyicilere ekleniyor</span><span class="sxs-lookup"><span data-stu-id="1e67c-141">Adding controllers to the context</span></span>
* <span data-ttu-id="1e67c-142">Genel denetleyici yöntemlerini eylem olarak ekleme</span><span class="sxs-lookup"><span data-stu-id="1e67c-142">Adding public controller methods as actions</span></span>
* <span data-ttu-id="1e67c-143">Bağlama eylem yöntemi parametreleri ekleme</span><span class="sxs-lookup"><span data-stu-id="1e67c-143">Adding action method parameters to the context</span></span>
* <span data-ttu-id="1e67c-144">Yol ve diğer öznitelikler uygulanıyor</span><span class="sxs-lookup"><span data-stu-id="1e67c-144">Applying route and other attributes</span></span>

<span data-ttu-id="1e67c-145">Bazı yerleşik davranışlar, tarafından uygulanır `DefaultApplicationModelProvider` .</span><span class="sxs-lookup"><span data-stu-id="1e67c-145">Some built-in behaviors are implemented by the `DefaultApplicationModelProvider`.</span></span> <span data-ttu-id="1e67c-146">Bu sağlayıcı,, [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel) [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel) [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel) , ve örneklerinin başvurduğu, öğesini oluşturmaktan sorumludur [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel) .</span><span class="sxs-lookup"><span data-stu-id="1e67c-146">This provider is responsible for constructing the [`ControllerModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.controllermodel), which in turn references [`ActionModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.actionmodel), [`PropertyModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.propertymodel), and [`ParameterModel`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.parametermodel) instances.</span></span> <span data-ttu-id="1e67c-147">`DefaultApplicationModelProvider`Sınıfı, gelecekte değişme ve değiştirecek bir iç çerçeve uygulama ayrıntısı.</span><span class="sxs-lookup"><span data-stu-id="1e67c-147">The `DefaultApplicationModelProvider` class is an internal framework implementation detail that can and will change in the future.</span></span> 

<span data-ttu-id="1e67c-148">, `AuthorizationApplicationModelProvider` Ve öznitelikleriyle ilişkili davranışı uygulamaktan sorumludur `AuthorizeFilter` `AllowAnonymousFilter` .</span><span class="sxs-lookup"><span data-stu-id="1e67c-148">The `AuthorizationApplicationModelProvider` is responsible for applying the behavior associated with the `AuthorizeFilter` and `AllowAnonymousFilter` attributes.</span></span> <span data-ttu-id="1e67c-149">[Bu öznitelikler hakkında daha fazla bilgi edinin](xref:security/authorization/simple).</span><span class="sxs-lookup"><span data-stu-id="1e67c-149">[Learn more about these attributes](xref:security/authorization/simple).</span></span>

<span data-ttu-id="1e67c-150">, `CorsApplicationModelProvider` Ve ile ilişkili davranışı uygular, ve `IEnableCorsAttribute` `IDisableCorsAttribute` `DisableCorsAuthorizationFilter` .</span><span class="sxs-lookup"><span data-stu-id="1e67c-150">The `CorsApplicationModelProvider` implements behavior associated with the `IEnableCorsAttribute` and `IDisableCorsAttribute`, and the `DisableCorsAuthorizationFilter`.</span></span> <span data-ttu-id="1e67c-151">[CORS hakkında daha fazla bilgi edinin](xref:security/cors).</span><span class="sxs-lookup"><span data-stu-id="1e67c-151">[Learn more about CORS](xref:security/cors).</span></span>

## <a name="conventions"></a><span data-ttu-id="1e67c-152">Kurallar</span><span class="sxs-lookup"><span data-stu-id="1e67c-152">Conventions</span></span>

<span data-ttu-id="1e67c-153">Uygulama modeli, modellerin tüm model veya sağlayıcıyı geçersiz kılmasından daha basit bir yol sağlayan kural soyutlamalarını tanımlar.</span><span class="sxs-lookup"><span data-stu-id="1e67c-153">The application model defines convention abstractions that provide a simpler way to customize the behavior of the models than overriding the entire model or provider.</span></span> <span data-ttu-id="1e67c-154">Bu soyutlamalar, uygulamanızın davranışını değiştirmek için önerilen yoldur.</span><span class="sxs-lookup"><span data-stu-id="1e67c-154">These abstractions are the recommended way to modify your app's behavior.</span></span> <span data-ttu-id="1e67c-155">Kurallar, özelleştirmeleri dinamik olarak uygulayacak kodu yazmanız için bir yol sağlar.</span><span class="sxs-lookup"><span data-stu-id="1e67c-155">Conventions provide a way for you to write code that will dynamically apply customizations.</span></span> <span data-ttu-id="1e67c-156">[Filtreler](xref:mvc/controllers/filters) çerçeve davranışının değiştirilmesini sağlayan bir yol sağlarken, özelleştirmeler uygulamanın tamamının birlikte nasıl çalıştığını denetlemenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="1e67c-156">While [filters](xref:mvc/controllers/filters) provide a means of modifying the framework's behavior, customizations let you control how the whole app works together.</span></span>

<span data-ttu-id="1e67c-157">Aşağıdaki kurallar kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="1e67c-157">The following conventions are available:</span></span>

* [`IApplicationModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iapplicationmodelconvention)
* [`IControllerModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.icontrollermodelconvention)
* [`IActionModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iactionmodelconvention)
* [`IParameterModelConvention`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.iparametermodelconvention)

<span data-ttu-id="1e67c-158">Kurallar, MVC seçeneklerine eklenerek veya `Attribute` s uygulanarak ve bunları denetleyicilere, eylemlere veya eylem parametrelerine (benzer şekilde) uygulanarak uygulanır [`Filters`](xref:mvc/controllers/filters) .</span><span class="sxs-lookup"><span data-stu-id="1e67c-158">Conventions are applied by adding them to MVC options or by implementing `Attribute`s and applying them to controllers, actions, or action parameters (similar to [`Filters`](xref:mvc/controllers/filters)).</span></span> <span data-ttu-id="1e67c-159">Filtrelerin aksine, kurallar yalnızca uygulama başlatıldığında yürütülür, her isteğin bir parçası olarak değildir.</span><span class="sxs-lookup"><span data-stu-id="1e67c-159">Unlike filters, conventions are only executed when the app is starting, not as part of each request.</span></span>

### <a name="sample-modifying-the-applicationmodel"></a><span data-ttu-id="1e67c-160">Örnek: ApplicationModel değiştirme</span><span class="sxs-lookup"><span data-stu-id="1e67c-160">Sample: Modifying the ApplicationModel</span></span>

<span data-ttu-id="1e67c-161">Aşağıdaki kural uygulama modeline bir özellik eklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1e67c-161">The following convention is used to add a property to the application model.</span></span> 

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ApplicationDescription.cs)]

<span data-ttu-id="1e67c-162">Uygulama modeli kuralları, ' de MVC eklendiğinde seçenek olarak uygulanır `ConfigureServices` `Startup` .</span><span class="sxs-lookup"><span data-stu-id="1e67c-162">Application model conventions are applied as options when MVC is added in `ConfigureServices` in `Startup`.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=5)]

<span data-ttu-id="1e67c-163">Özellikleri, `ActionDescriptor` Denetleyici eylemleri içindeki Özellikler koleksiyonundan erişilebilir:</span><span class="sxs-lookup"><span data-stu-id="1e67c-163">Properties are accessible from the `ActionDescriptor` properties collection within controller actions:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/AppModelController.cs?name=AppModelController)]

### <a name="sample-modifying-the-controllermodel-description"></a><span data-ttu-id="1e67c-164">Örnek: ControllerModel açıklamasını değiştirme</span><span class="sxs-lookup"><span data-stu-id="1e67c-164">Sample: Modifying the ControllerModel Description</span></span>

<span data-ttu-id="1e67c-165">Önceki örnekte olduğu gibi, denetleyici modeli de özel özellikler içerecek şekilde değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="1e67c-165">As in the previous example, the controller model can also be modified to include custom properties.</span></span> <span data-ttu-id="1e67c-166">Bunlar, varolan özellikleri uygulama modelinde belirtilen adla geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="1e67c-166">These will override existing properties with the same name specified in the application model.</span></span> <span data-ttu-id="1e67c-167">Aşağıdaki kural özniteliği, denetleyici düzeyine bir açıklama ekler:</span><span class="sxs-lookup"><span data-stu-id="1e67c-167">The following convention attribute adds a description at the controller level:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ControllerDescriptionAttribute.cs)]

<span data-ttu-id="1e67c-168">Bu kural, bir denetleyiciye bir öznitelik olarak uygulanır.</span><span class="sxs-lookup"><span data-stu-id="1e67c-168">This convention is applied as an attribute on a controller.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=ControllerDescription&highlight=1)]

<span data-ttu-id="1e67c-169">"Description" özelliğine, önceki örneklerde olduğu gibi erişilir.</span><span class="sxs-lookup"><span data-stu-id="1e67c-169">The "description" property is accessed in the same manner as in previous examples.</span></span>

### <a name="sample-modifying-the-actionmodel-description"></a><span data-ttu-id="1e67c-170">Örnek: ActionModel açıklamasını değiştirme</span><span class="sxs-lookup"><span data-stu-id="1e67c-170">Sample: Modifying the ActionModel Description</span></span>

<span data-ttu-id="1e67c-171">Ayrı bir öznitelik kuralı tek tek eylemlere uygulanabilir, uygulama veya denetleyici düzeyinde zaten uygulanmış davranışı geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="1e67c-171">A separate attribute convention can be applied to individual actions, overriding behavior already applied at the application or controller level.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/ActionDescriptionAttribute.cs)]

<span data-ttu-id="1e67c-172">Bu, önceki örnekte denetleyicinin içindeki bir eyleme uygulandığında, denetleyici düzeyi kuralı nasıl geçersiz kılacağını gösterir:</span><span class="sxs-lookup"><span data-stu-id="1e67c-172">Applying this to an action within the previous example's controller demonstrates how it overrides the controller-level convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/DescriptionAttributesController.cs?name=DescriptionAttributesController&highlight=9)]

### <a name="sample-modifying-the-parametermodel"></a><span data-ttu-id="1e67c-173">Örnek: ParameterModel değiştirme</span><span class="sxs-lookup"><span data-stu-id="1e67c-173">Sample: Modifying the ParameterModel</span></span>

<span data-ttu-id="1e67c-174">Aşağıdaki kural, değerlerini değiştirmek için eylem parametrelerine uygulanabilir `BindingInfo` .</span><span class="sxs-lookup"><span data-stu-id="1e67c-174">The following convention can be applied to action parameters to modify their `BindingInfo`.</span></span> <span data-ttu-id="1e67c-175">Aşağıdaki kural parametrenin bir yol parametresi olmasını gerektirir; diğer olası bağlama kaynakları (sorgu dizesi değerleri gibi) yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="1e67c-175">The following convention requires that the parameter be a route parameter; other potential binding sources (such as query string values) are ignored.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/MustBeInRouteParameterModelConvention.cs)]

<span data-ttu-id="1e67c-176">Öznitelik herhangi bir eylem parametresine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="1e67c-176">The attribute may be applied to any action parameter:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/ParameterModelController.cs?name=ParameterModelController&highlight=5)]

### <a name="sample-modifying-the-actionmodel-name"></a><span data-ttu-id="1e67c-177">Örnek: ActionModel adını değiştirme</span><span class="sxs-lookup"><span data-stu-id="1e67c-177">Sample: Modifying the ActionModel Name</span></span>

<span data-ttu-id="1e67c-178">Aşağıdaki kural, `ActionModel` uygulandığı eylemin *adını* güncelleştirmek için ' i değiştirir.</span><span class="sxs-lookup"><span data-stu-id="1e67c-178">The following convention modifies the `ActionModel` to update the *name* of the action to which it's applied.</span></span> <span data-ttu-id="1e67c-179">Yeni ad, özniteliğe bir parametre olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="1e67c-179">The new name is provided as a parameter to the attribute.</span></span> <span data-ttu-id="1e67c-180">Bu yeni ad yönlendirme tarafından kullanılır, bu nedenle bu eylem yöntemine ulaşmak için kullanılan yolu etkileyecektir.</span><span class="sxs-lookup"><span data-stu-id="1e67c-180">This new name is used by routing, so it will affect the route used to reach this action method.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/CustomActionNameAttribute.cs)]

<span data-ttu-id="1e67c-181">Bu öznitelik, içindeki bir eylem yöntemine uygulanır `HomeController` :</span><span class="sxs-lookup"><span data-stu-id="1e67c-181">This attribute is applied to an action method in the `HomeController`:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/HomeController.cs?name=ActionModelConvention&highlight=2)]

<span data-ttu-id="1e67c-182">Yöntem adı olsa da öznitelik, `SomeName` Yöntem adını KULLANMANıN MVC kuralını geçersiz kılar ve eylem adını ile değiştirir `MyCoolAction` .</span><span class="sxs-lookup"><span data-stu-id="1e67c-182">Even though the method name is `SomeName`, the attribute overrides the MVC convention of using the method name and replaces the action name with `MyCoolAction`.</span></span> <span data-ttu-id="1e67c-183">Bu nedenle, bu eyleme ulaşmak için kullanılan yol `/Home/MyCoolAction` .</span><span class="sxs-lookup"><span data-stu-id="1e67c-183">Thus, the route used to reach this action is `/Home/MyCoolAction`.</span></span>

> [!NOTE]
> <span data-ttu-id="1e67c-184">Bu örnek temelde, yerleşik [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute) özniteliği kullanılarak aynıdır.</span><span class="sxs-lookup"><span data-stu-id="1e67c-184">This example is essentially the same as using the built-in [ActionName](/dotnet/api/microsoft.aspnetcore.mvc.actionnameattribute) attribute.</span></span>

### <a name="sample-custom-routing-convention"></a><span data-ttu-id="1e67c-185">Örnek: özel yönlendirme kuralı</span><span class="sxs-lookup"><span data-stu-id="1e67c-185">Sample: Custom Routing Convention</span></span>

<span data-ttu-id="1e67c-186">`IApplicationModelConvention`Yönlendirmeyi, yönlendirmenin nasıl çalıştığını özelleştirmek için kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1e67c-186">You can use an `IApplicationModelConvention` to customize how routing works.</span></span> <span data-ttu-id="1e67c-187">Örneğin, aşağıdaki kural, denetleyiciler ' ad alanlarını yönlendirmelerin içinde birleştirir, bu `.` ad alanı, rotada ile değiştiriliyor `/` :</span><span class="sxs-lookup"><span data-stu-id="1e67c-187">For example, the following convention will incorporate Controllers' namespaces into their routes, replacing `.` in the namespace with `/` in the route:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/NamespaceRoutingConvention.cs)]

<span data-ttu-id="1e67c-188">Kural başlangıçta bir seçenek olarak eklenir.</span><span class="sxs-lookup"><span data-stu-id="1e67c-188">The convention is added as an option in Startup.</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Startup.cs?name=ConfigureServices&highlight=6)]

> [!TIP]
> <span data-ttu-id="1e67c-189">Kullanarak, [Ara yazılıma](xref:fundamentals/middleware/index) bir kural ekleyebilirsiniz `MvcOptions``services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`</span><span class="sxs-lookup"><span data-stu-id="1e67c-189">You can add conventions to your [middleware](xref:fundamentals/middleware/index) by accessing `MvcOptions` using `services.Configure<MvcOptions>(c => c.Conventions.Add(YOURCONVENTION));`</span></span>

<span data-ttu-id="1e67c-190">Bu örnek, denetleyicinin adında "namespace" özelliği bulunan öznitelik yönlendirme kullanmayan yollara bu kuralı uygular.</span><span class="sxs-lookup"><span data-stu-id="1e67c-190">This sample applies this convention to routes that are not using attribute routing where the controller has  "Namespace" in its name.</span></span> <span data-ttu-id="1e67c-191">Aşağıdaki denetleyicide bu kural gösterilmektedir:</span><span class="sxs-lookup"><span data-stu-id="1e67c-191">The following controller demonstrates this convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Controllers/NamespaceRoutingController.cs?highlight=7-8)]

## <a name="application-model-usage-in-webapicompatshim"></a><span data-ttu-id="1e67c-192">WebApiCompatShim 'de uygulama modeli kullanımı</span><span class="sxs-lookup"><span data-stu-id="1e67c-192">Application Model Usage in WebApiCompatShim</span></span>

<span data-ttu-id="1e67c-193">ASP.NET Core MVC, ASP.NET Web API 2 ' den farklı bir kural kümesi kullanır.</span><span class="sxs-lookup"><span data-stu-id="1e67c-193">ASP.NET Core MVC uses a different set of conventions from ASP.NET Web API 2.</span></span> <span data-ttu-id="1e67c-194">Özel kuralları kullanarak, bir ASP.NET Core MVC uygulamasının davranışını bir Web API uygulaması ile tutarlı olacak şekilde değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1e67c-194">Using custom conventions, you can modify an ASP.NET Core MVC app's behavior to be consistent with that of a Web API app.</span></span> <span data-ttu-id="1e67c-195">Microsoft bu amaçla [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) 'i özel olarak sevk eder.</span><span class="sxs-lookup"><span data-stu-id="1e67c-195">Microsoft ships the [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim/) specifically for this purpose.</span></span>

> [!NOTE]
> <span data-ttu-id="1e67c-196">[ASP.NET Web API 'sinden geçiş](xref:migration/webapi)hakkında daha fazla bilgi edinin.</span><span class="sxs-lookup"><span data-stu-id="1e67c-196">Learn more about [migration from ASP.NET Web API](xref:migration/webapi).</span></span>

<span data-ttu-id="1e67c-197">Web API 'SI uyumluluk dolgusu 'nı kullanmak için, paketi projenize eklemeniz ve sonra ' de çağırarak MVC 'ye kuralları eklemeniz gerekir `AddWebApiConventions` `Startup` :</span><span class="sxs-lookup"><span data-stu-id="1e67c-197">To use the Web API Compatibility Shim, you need to add the package to your project and then add the conventions to MVC by calling `AddWebApiConventions` in `Startup`:</span></span>

```csharp
services.AddMvc().AddWebApiConventions();
```

<span data-ttu-id="1e67c-198">Dolgu tarafından belirtilen kurallar yalnızca belirli özniteliklerin uygulanmış olduğu uygulamanın bölümlerine uygulanır.</span><span class="sxs-lookup"><span data-stu-id="1e67c-198">The conventions provided by the shim are only applied to parts of the app that have had certain attributes applied to them.</span></span> <span data-ttu-id="1e67c-199">Aşağıdaki dört öznitelik, hangi denetleyicilerin kendi kurallarını Shim 'in kuralları tarafından değiştirildiğini denetlemek için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="1e67c-199">The following four attributes are used to control which controllers should have their conventions modified by the shim's conventions:</span></span>

* [<span data-ttu-id="1e67c-200">UseWebApiActionConventionsAttribute</span><span class="sxs-lookup"><span data-stu-id="1e67c-200">UseWebApiActionConventionsAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiactionconventionsattribute)
* [<span data-ttu-id="1e67c-201">UseWebApiOverloadingAttribute</span><span class="sxs-lookup"><span data-stu-id="1e67c-201">UseWebApiOverloadingAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapioverloadingattribute)
* [<span data-ttu-id="1e67c-202">UseWebApiParameterConventionsAttribute</span><span class="sxs-lookup"><span data-stu-id="1e67c-202">UseWebApiParameterConventionsAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiparameterconventionsattribute)
* [<span data-ttu-id="1e67c-203">UseWebApiRoutesAttribute</span><span class="sxs-lookup"><span data-stu-id="1e67c-203">UseWebApiRoutesAttribute</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.webapicompatshim.usewebapiroutesattribute)

### <a name="action-conventions"></a><span data-ttu-id="1e67c-204">Eylem kuralları</span><span class="sxs-lookup"><span data-stu-id="1e67c-204">Action Conventions</span></span>

<span data-ttu-id="1e67c-205">, `UseWebApiActionConventionsAttribute` Http yöntemini adına göre eylemlerle eşlemek için kullanılır (örneğin, `Get` eşlenecek `HttpGet` ).</span><span class="sxs-lookup"><span data-stu-id="1e67c-205">The `UseWebApiActionConventionsAttribute` is used to map the HTTP method to actions based on their name (for instance, `Get` would map to `HttpGet`).</span></span> <span data-ttu-id="1e67c-206">Yalnızca öznitelik yönlendirme kullanmayan eylemler için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="1e67c-206">It only applies to actions that don't use attribute routing.</span></span>

### <a name="overloading"></a><span data-ttu-id="1e67c-207">Aşırı Yükleme</span><span class="sxs-lookup"><span data-stu-id="1e67c-207">Overloading</span></span>

<span data-ttu-id="1e67c-208">, `UseWebApiOverloadingAttribute` Kuralını uygulamak için kullanılır `WebApiOverloadingApplicationModelConvention` .</span><span class="sxs-lookup"><span data-stu-id="1e67c-208">The `UseWebApiOverloadingAttribute` is used to apply the `WebApiOverloadingApplicationModelConvention` convention.</span></span> <span data-ttu-id="1e67c-209">Bu kural, `OverloadActionConstraint` aday eylemlerini isteğin tüm isteğe bağlı olmayan parametreleri karşılayan olanlarla sınırlayan eylem seçimi işlemine ekler.</span><span class="sxs-lookup"><span data-stu-id="1e67c-209">This convention adds an `OverloadActionConstraint` to the action selection process, which limits candidate actions to those for which the request satisfies all non-optional parameters.</span></span>

### <a name="parameter-conventions"></a><span data-ttu-id="1e67c-210">Parametre kuralları</span><span class="sxs-lookup"><span data-stu-id="1e67c-210">Parameter Conventions</span></span>

<span data-ttu-id="1e67c-211">`UseWebApiParameterConventionsAttribute`Eylem kuralını uygulamak için kullanılır `WebApiParameterConventionsApplicationModelConvention` .</span><span class="sxs-lookup"><span data-stu-id="1e67c-211">The `UseWebApiParameterConventionsAttribute` is used to apply the `WebApiParameterConventionsApplicationModelConvention` action convention.</span></span> <span data-ttu-id="1e67c-212">Bu kural, eylem parametreleri olarak kullanılan basit türlerin varsayılan olarak URI 'den bağlandığı, karmaşık türlerin istek gövdesinden bağlandığı bir şekilde belirtir.</span><span class="sxs-lookup"><span data-stu-id="1e67c-212">This convention specifies that simple types used as action parameters are bound from the URI by default, while complex types are bound from the request body.</span></span>

### <a name="routes"></a><span data-ttu-id="1e67c-213">Yollar</span><span class="sxs-lookup"><span data-stu-id="1e67c-213">Routes</span></span>

<span data-ttu-id="1e67c-214">, `UseWebApiRoutesAttribute` `WebApiApplicationModelConvention` Denetleyici kuralının uygulanıp uygulanmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="1e67c-214">The `UseWebApiRoutesAttribute` controls whether the `WebApiApplicationModelConvention` controller convention is applied.</span></span> <span data-ttu-id="1e67c-215">Bu kural etkinleştirildiğinde, rotadaki [alanlara](xref:mvc/controllers/areas) yönelik destek eklemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1e67c-215">When enabled, this convention is used to add support for [areas](xref:mvc/controllers/areas) to the route.</span></span>

<span data-ttu-id="1e67c-216">Bir kural kümesine ek olarak, uyumluluk paketi `System.Web.Http.ApiController` Web API 'si tarafından sağlanarak yerine geçen bir temel sınıf içerir.</span><span class="sxs-lookup"><span data-stu-id="1e67c-216">In addition to a set of conventions, the compatibility package includes a `System.Web.Http.ApiController` base class that replaces the one provided by Web API.</span></span> <span data-ttu-id="1e67c-217">Bu, denetleyicilerinizin Web API 'SI için yazılmasını ve `ApiController` ASP.NET Core MVC üzerinde çalışırken tasarlandığı gibi çalışmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="1e67c-217">This allows your controllers written for Web API and inheriting from its `ApiController` to work as they were designed, while running on ASP.NET Core MVC.</span></span> <span data-ttu-id="1e67c-218">`UseWebApi*`Daha önce listelenen özniteliklerin tümü, temel denetleyici sınıfına uygulanır.</span><span class="sxs-lookup"><span data-stu-id="1e67c-218">All of the `UseWebApi*` attributes listed earlier are applied to the base controller class.</span></span> <span data-ttu-id="1e67c-219">, `ApiController` Web API 'sinde bulunanlarla uyumlu özellikler, Yöntemler ve sonuç türleri sunar.</span><span class="sxs-lookup"><span data-stu-id="1e67c-219">The `ApiController` exposes properties, methods, and result types that are compatible with those found in Web API.</span></span>

## <a name="using-apiexplorer-to-document-your-app"></a><span data-ttu-id="1e67c-220">Uygulamanızı belgelemek için ApiExplorer kullanma</span><span class="sxs-lookup"><span data-stu-id="1e67c-220">Using ApiExplorer to Document Your App</span></span>

<span data-ttu-id="1e67c-221">Uygulama modeli, [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) uygulamanın yapısına çapraz geçiş için kullanılabilecek her düzeyde bir özellik sunar.</span><span class="sxs-lookup"><span data-stu-id="1e67c-221">The application model exposes an [`ApiExplorer`](/dotnet/api/microsoft.aspnetcore.mvc.applicationmodels.apiexplorermodel) property at each level that can be used to traverse the app's structure.</span></span> <span data-ttu-id="1e67c-222">Bu, [Swagger gibi araçları kullanarak Web API 'leriniz için yardım sayfaları oluşturmak](xref:tutorials/web-api-help-pages-using-swagger)üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="1e67c-222">This can be used to [generate help pages for your Web APIs using tools like Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="1e67c-223">`ApiExplorer`Özelliği, `IsVisible` uygulamanızın modelinin hangi bölümlerinin sunulduğunu belirtmek üzere ayarlanabilir bir özellik sunar.</span><span class="sxs-lookup"><span data-stu-id="1e67c-223">The `ApiExplorer` property exposes an `IsVisible` property that can be set to specify which parts of your app's model should be exposed.</span></span> <span data-ttu-id="1e67c-224">Bu ayarı, bir kuralı kullanarak yapılandırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="1e67c-224">You can configure this setting using a convention:</span></span>

[!code-csharp[](./application-model/sample/src/AppModelSample/Conventions/EnableApiExplorerApplicationConvention.cs)]

<span data-ttu-id="1e67c-225">Bu yaklaşımı (ve gerekirse ek kuralları) kullanarak uygulamanızın içindeki herhangi bir düzeyde API görünürlüğünü etkinleştirebilir veya devre dışı bırakabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1e67c-225">Using this approach (and additional conventions if required), you can enable or disable API visibility at any level within your app.</span></span> 
