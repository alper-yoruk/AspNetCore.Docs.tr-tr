---
title: ASP.NET Core'da politika tabanlı yetkilendirme
author: rick-anderson
description: ASP.NET Core uygulamasında yetkilendirme gereksinimlerini uygulamak için yetkilendirme ilkesi işleyicilerini nasıl oluşturup kullanacağınızı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
uid: security/authorization/policies
ms.openlocfilehash: 66412a6020ea8f12427c8c5b466e1b2eedccf0f9
ms.sourcegitcommit: 77c046331f3d633d7cc247ba77e58b89e254f487
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81488767"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="e8334-103">ASP.NET Core'da politika tabanlı yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="e8334-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e8334-104">Kapakların altında, [rol tabanlı yetkilendirme](xref:security/authorization/roles) ve talep tabanlı [yetkilendirme](xref:security/authorization/claims) bir gereksinim, gereksinim işleyicisi ve önceden yapılandırılmış bir ilke kullanır.</span><span class="sxs-lookup"><span data-stu-id="e8334-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="e8334-105">Bu yapı taşları, yetkilendirme değerlendirmelerinin koddaki ifadesini destekler.</span><span class="sxs-lookup"><span data-stu-id="e8334-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="e8334-106">Sonuç daha zengin, yeniden kullanılabilir, test edilebilir bir yetkilendirme yapısıdır.</span><span class="sxs-lookup"><span data-stu-id="e8334-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="e8334-107">Yetkilendirme ilkesi bir veya daha fazla gereksinimden oluşur.</span><span class="sxs-lookup"><span data-stu-id="e8334-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="e8334-108">Yetkilendirme hizmeti yapılandırmasının bir parçası olarak, `Startup.ConfigureServices` yöntemde kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="e8334-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="e8334-109">Önceki örnekte, bir "AtLeast21" ilkesi oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e8334-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="e8334-110">Gereksinime parametre&mdash;olarak verilen minimum yaş için tek bir gereksinimvardır.</span><span class="sxs-lookup"><span data-stu-id="e8334-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="e8334-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="e8334-111">IAuthorizationService</span></span> 

<span data-ttu-id="e8334-112">Yetkilendirmenin başarılı olup olmadığını belirleyen <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>birincil hizmet:</span><span class="sxs-lookup"><span data-stu-id="e8334-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="e8334-113">Önceki kod [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)iki yöntem vurgulamaktadır.</span><span class="sxs-lookup"><span data-stu-id="e8334-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="e8334-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>hiçbir yöntem ve yetkilendirme başarılı olup olmadığını izleme mekanizması ile bir işaretçi hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="e8334-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="e8334-115">Gereksinimlerin karşılanıp karşılanmadığını denetlemekle ilgili her <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> şeyi kontrol etmekle yükümlüdür:</span><span class="sxs-lookup"><span data-stu-id="e8334-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="e8334-116">Sınıf, <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> gereksinimlerin karşılanıp karşılanmadığını işaretlemek için işleyicinin kullandığı şeydir:</span><span class="sxs-lookup"><span data-stu-id="e8334-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="e8334-117">Aşağıdaki kod, yetkilendirme hizmetinin basitleştirilmiş (ve açıklamalı) varsayılan uygulamasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="e8334-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="e8334-118">Aşağıdaki kod tipik `ConfigureServices`bir gösterir:</span><span class="sxs-lookup"><span data-stu-id="e8334-118">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddControllersWithViews();
    services.AddRazorPages();
}
```

<span data-ttu-id="e8334-119">Kullanım <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` veya yetkilendirme için.</span><span class="sxs-lookup"><span data-stu-id="e8334-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="e8334-120">İlkeleri MVC denetleyicilerine uygulama</span><span class="sxs-lookup"><span data-stu-id="e8334-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="e8334-121">Razor Pages kullanıyorsanız, bu belgede [Razor Pages'e ilkeler uygulama'ya](#applying-policies-to-razor-pages) bakın.</span><span class="sxs-lookup"><span data-stu-id="e8334-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="e8334-122">İlkeler, öznitelik ilkesi adı `[Authorize]` ile kullanılarak denetleyicilere uygulanır.</span><span class="sxs-lookup"><span data-stu-id="e8334-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="e8334-123">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="e8334-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="e8334-124">İlkeleri Jilet Sayfalarına Uygulama</span><span class="sxs-lookup"><span data-stu-id="e8334-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="e8334-125">İlkeler, ilke adı ile `[Authorize]` öznitelik kullanılarak Razor Pages uygulanır.</span><span class="sxs-lookup"><span data-stu-id="e8334-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="e8334-126">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="e8334-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="e8334-127">İlkeler, [bir yetkilendirme kuralı](xref:security/authorization/razor-pages-authorization)kullanılarak Razor Pages'e de uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="e8334-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="e8334-128">Gereksinimler</span><span class="sxs-lookup"><span data-stu-id="e8334-128">Requirements</span></span>

<span data-ttu-id="e8334-129">Yetkilendirme gereksinimi, bir ilkenin geçerli kullanıcı ilkesini değerlendirmek için kullanabileceği veri parametreleri topluluğudur.</span><span class="sxs-lookup"><span data-stu-id="e8334-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="e8334-130">"En Az21" politikamızda, gereksinim minimum yaş&mdash;tek bir parametredir.</span><span class="sxs-lookup"><span data-stu-id="e8334-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="e8334-131">Bir gereksinim, boş bir işaretçi arabirimi olan [IAuthorizationRequirement'ı](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)uygular.</span><span class="sxs-lookup"><span data-stu-id="e8334-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="e8334-132">Parametrelendirilmiş bir minimum yaş gereksinimi aşağıdaki gibi uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="e8334-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="e8334-133">Yetkilendirme ilkesi birden çok yetkilendirme gereksinimi içeriyorsa, ilke değerlendirmesinin başarılı olabilmesi için tüm gereksinimlerin geçmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="e8334-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="e8334-134">Başka bir deyişle, tek bir yetkilendirme ilkesine eklenen birden çok yetkilendirme gereksinimi **VE** temelinde ele alınır.</span><span class="sxs-lookup"><span data-stu-id="e8334-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="e8334-135">Bir gereksinimin veri veya özelliklere sahip olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="e8334-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="e8334-136">Yetkilendirme işleyicileri</span><span class="sxs-lookup"><span data-stu-id="e8334-136">Authorization handlers</span></span>

<span data-ttu-id="e8334-137">Bir gereksinimin özelliklerinin değerlendirilmesini bir yetkilendirme işleyicisi sorumludur.</span><span class="sxs-lookup"><span data-stu-id="e8334-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="e8334-138">Yetkilendirme işleyicisi, erişime izin verilip verilmediğini belirlemek için gereksinimleri sağlanan [bir YetkilendirmeHandlerContext'a](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) karşı değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="e8334-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="e8334-139">Bir [gereksinimin birden çok işleyicisi](#security-authorization-policies-based-multiple-handlers)olabilir.</span><span class="sxs-lookup"><span data-stu-id="e8334-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="e8334-140">İşleyici, işlenecek gereksinimin bulunduğu `TRequirement` [yetkilendirmeHandler\<TRequirement>'ı ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)devralabilir.</span><span class="sxs-lookup"><span data-stu-id="e8334-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="e8334-141">Alternatif olarak, bir işleyici birden fazla gereksinim türünü işlemek için [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="e8334-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="e8334-142">Tek bir gereksinim için işleyici kullanma</span><span class="sxs-lookup"><span data-stu-id="e8334-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="e8334-143">Aşağıda, minimum yaş işleyicisinin tek bir gereksinimkullandığı bire bir ilişki örneği verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="e8334-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="e8334-144">Önceki kod, geçerli kullanıcı ilkesinin bilinen ve güvenilen bir Veren tarafından verilmiş bir doğum talebi tarihi olup olmadığını belirler.</span><span class="sxs-lookup"><span data-stu-id="e8334-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="e8334-145">Talep eksik olduğunda yetkilendirme gerçekleşemez ve bu durumda tamamlanmış bir görev iade edilir.</span><span class="sxs-lookup"><span data-stu-id="e8334-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="e8334-146">Bir talep olduğunda, kullanıcının yaşı hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="e8334-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="e8334-147">Kullanıcı gereksinimle tanımlanan minimum yaşı karşılıyorsa, yetkilendirme başarılı sayılır.</span><span class="sxs-lookup"><span data-stu-id="e8334-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="e8334-148">Yetkilendirme başarılı olduğunda, `context.Succeed` tek parametresi olarak memnun gereksinimi ile çağrılır.</span><span class="sxs-lookup"><span data-stu-id="e8334-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="e8334-149">Birden çok gereksinim için işleyici kullanma</span><span class="sxs-lookup"><span data-stu-id="e8334-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="e8334-150">Aşağıda, bir izin işleyicisinin üç farklı gereksinim türünü işleyebileceği bir-çok ilişkisine bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="e8334-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="e8334-151">Önceki [kod, bekleyen](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;olarak işaretlenmemiş gereksinimleri içeren bir özellik olan Bekleme Gereksinimleri'nden geçer.</span><span class="sxs-lookup"><span data-stu-id="e8334-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="e8334-152">Bir `ReadPermission` gereksinim için, kullanıcının istenen kaynağa erişmek için bir sahip veya sponsor olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e8334-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="e8334-153">Bir `EditPermission` veya `DeletePermission` gereksinim söz konusu olduğunda, istenen kaynağa erişmek için bir sahip olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e8334-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="e8334-154">Işleyici kaydı</span><span class="sxs-lookup"><span data-stu-id="e8334-154">Handler registration</span></span>

<span data-ttu-id="e8334-155">Işleyiciler yapılandırma sırasında hizmet koleksiyonuna kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="e8334-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="e8334-156">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="e8334-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="e8334-157">Önceki kod çağırarak `MinimumAgeHandler` `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`singleton olarak kaydeder.</span><span class="sxs-lookup"><span data-stu-id="e8334-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="e8334-158">İşleyiciler, yerleşik [hizmet kullanım ömürlerinden](xref:fundamentals/dependency-injection#service-lifetimes)herhangi biri kullanılarak kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="e8334-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="e8334-159">Bir işleyici ne iade etmelidir?</span><span class="sxs-lookup"><span data-stu-id="e8334-159">What should a handler return?</span></span>

<span data-ttu-id="e8334-160">`Handle` [Işleyici örneğindeki](#security-authorization-handler-example) yöntemin hiçbir değer döndürmediğini unutmayın.</span><span class="sxs-lookup"><span data-stu-id="e8334-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="e8334-161">Başarı veya başarısızlık durumu nasıl gösterilir?</span><span class="sxs-lookup"><span data-stu-id="e8334-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="e8334-162">İşleyici, başarıyla `context.Succeed(IAuthorizationRequirement requirement)`doğrulanmış gereksinimi geçerek arayarak başarıyı gösterir.</span><span class="sxs-lookup"><span data-stu-id="e8334-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="e8334-163">Aynı gereksinime sahip diğer işleyiciler başarılı olabileceğinden, işleyicinin hataları genellikle işlemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="e8334-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="e8334-164">Diğer gereksinim işleyicileri başarılı olsa bile, `context.Fail`başarısızlığı garanti etmek için.</span><span class="sxs-lookup"><span data-stu-id="e8334-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="e8334-165">Bir işleyici `context.Succeed` ararsa veya `context.Fail`diğer tüm işleyiciler hala çağrılır.</span><span class="sxs-lookup"><span data-stu-id="e8334-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="e8334-166">Bu, gereksinimlerin, başka bir işleyici bir gereksinimi başarıyla doğrulamış veya başarısız olsa bile gerçekleşen günlük oluşturma gibi yan etkiler üretmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="e8334-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="e8334-167">Çağrıldığında `false` [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) özelliği (ASP.NET Core 1.1 ve sonrası olarak kullanılabilir) kısa devreler çağrıldığında `context.Fail` işleyicilerin yürütülmesi ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="e8334-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="e8334-168">`InvokeHandlersAfterFailure`varsayılan olarak `true`, bu durumda tüm işleyiciler çağrılır.</span><span class="sxs-lookup"><span data-stu-id="e8334-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="e8334-169">Kimlik doğrulaması başarısız olsa bile yetkilendirme işleyicileri çağrılır.</span><span class="sxs-lookup"><span data-stu-id="e8334-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="e8334-170">Neden bir gereksinim için birden fazla işleyici isteyeyim?</span><span class="sxs-lookup"><span data-stu-id="e8334-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="e8334-171">Değerlendirmenin **OR** bazında olmasını istediğiniz durumlarda, tek bir gereksinim için birden çok işleyici uygulayın.</span><span class="sxs-lookup"><span data-stu-id="e8334-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="e8334-172">Örneğin, Microsoft'un yalnızca anahtar kartlarıyla açılan kapıları vardır.</span><span class="sxs-lookup"><span data-stu-id="e8334-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="e8334-173">Anahtar kartınızı evde bırakırsanız, resepsiyonist geçici bir etiket yazdırır ve kapıyı sizin için açar.</span><span class="sxs-lookup"><span data-stu-id="e8334-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="e8334-174">Bu senaryoda, tek bir gereksinim, *BuildingEntry,* ancak birden çok işleyicileri, her biri tek bir gereksinimi inceleyen olurdu.</span><span class="sxs-lookup"><span data-stu-id="e8334-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="e8334-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="e8334-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="e8334-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="e8334-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="e8334-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="e8334-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="e8334-178">Her iki işleyicinin de [kayıtlı](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="e8334-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="e8334-179">Bir ilke , `BuildingEntryRequirement`ilke değerlendirmesi başarılı olduğunda ya işleyici başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="e8334-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="e8334-180">Bir ilkeyi yerine getirmek için func kullanma</span><span class="sxs-lookup"><span data-stu-id="e8334-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="e8334-181">Bir ilkeyi yerine getirmenin kodla ifade edilmesinin kolay olduğu durumlar olabilir.</span><span class="sxs-lookup"><span data-stu-id="e8334-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="e8334-182">İlke oluşturucu ile `Func<AuthorizationHandlerContext, bool>` politikanızı yapılandırırken bir şey sağlamak mümkündür. `RequireAssertion`</span><span class="sxs-lookup"><span data-stu-id="e8334-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="e8334-183">Örneğin, önceki `BadgeEntryHandler` aşağıdaki gibi yeniden yazılabilir:</span><span class="sxs-lookup"><span data-stu-id="e8334-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="e8334-184">İşleyicilerde MVC istek bağlamına erişim</span><span class="sxs-lookup"><span data-stu-id="e8334-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="e8334-185">Yetkilendirme `HandleRequirementAsync` işleyicisinde uyguladığınız yöntemin iki `AuthorizationHandlerContext` parametresi vardır: a ve `TRequirement` siz işlediğiniz.</span><span class="sxs-lookup"><span data-stu-id="e8334-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="e8334-186">MVC veya Jabbr gibi çerçeveler, ek bilgi `Resource` aktarmak `AuthorizationHandlerContext` için tesise herhangi bir nesne eklemekte serbesttir.</span><span class="sxs-lookup"><span data-stu-id="e8334-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="e8334-187">Örneğin, MVC özellikte [YetkilendirmeFiltresi](/dotnet/api/?term=AuthorizationFilterContext) Bağlamı'nın bir örneğini `Resource` geçirir.</span><span class="sxs-lookup"><span data-stu-id="e8334-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="e8334-188">Bu `HttpContext`özellik, `RouteData`MVC ve Razor Pages tarafından sağlanan her şeye ve her şeye erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="e8334-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="e8334-189">Özelliğin `Resource` kullanımı çerçeveye özgüdür.</span><span class="sxs-lookup"><span data-stu-id="e8334-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="e8334-190">Tesisteki `Resource` bilgileri kullanmak, yetkilendirme ilkelerinizi belirli çerçevelerle sınırlar.</span><span class="sxs-lookup"><span data-stu-id="e8334-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="e8334-191">`Resource` Özelliği anahtar kelimeyi `is` kullanarak kullanmalı ve ardından kodunuzun diğer çerçevelerde `InvalidCastException` çalıştırıldığında çökmediğinden emin olmak için dökümün başarılı olduğunu onaylamanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="e8334-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e8334-192">Kapakların altında, [rol tabanlı yetkilendirme](xref:security/authorization/roles) ve talep tabanlı [yetkilendirme](xref:security/authorization/claims) bir gereksinim, gereksinim işleyicisi ve önceden yapılandırılmış bir ilke kullanır.</span><span class="sxs-lookup"><span data-stu-id="e8334-192">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="e8334-193">Bu yapı taşları, yetkilendirme değerlendirmelerinin koddaki ifadesini destekler.</span><span class="sxs-lookup"><span data-stu-id="e8334-193">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="e8334-194">Sonuç daha zengin, yeniden kullanılabilir, test edilebilir bir yetkilendirme yapısıdır.</span><span class="sxs-lookup"><span data-stu-id="e8334-194">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="e8334-195">Yetkilendirme ilkesi bir veya daha fazla gereksinimden oluşur.</span><span class="sxs-lookup"><span data-stu-id="e8334-195">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="e8334-196">Yetkilendirme hizmeti yapılandırmasının bir parçası olarak, `Startup.ConfigureServices` yöntemde kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="e8334-196">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="e8334-197">Önceki örnekte, bir "AtLeast21" ilkesi oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e8334-197">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="e8334-198">Gereksinime parametre&mdash;olarak verilen minimum yaş için tek bir gereksinimvardır.</span><span class="sxs-lookup"><span data-stu-id="e8334-198">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="e8334-199">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="e8334-199">IAuthorizationService</span></span> 

<span data-ttu-id="e8334-200">Yetkilendirmenin başarılı olup olmadığını belirleyen <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>birincil hizmet:</span><span class="sxs-lookup"><span data-stu-id="e8334-200">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="e8334-201">Önceki kod [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)iki yöntem vurgulamaktadır.</span><span class="sxs-lookup"><span data-stu-id="e8334-201">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="e8334-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>hiçbir yöntem ve yetkilendirme başarılı olup olmadığını izleme mekanizması ile bir işaretçi hizmetidir.</span><span class="sxs-lookup"><span data-stu-id="e8334-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="e8334-203">Gereksinimlerin karşılanıp karşılanmadığını denetlemekle ilgili her <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> şeyi kontrol etmekle yükümlüdür:</span><span class="sxs-lookup"><span data-stu-id="e8334-203">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
<!--The following code is a copy/paste from 
https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationHandler.cs -->

```csharp
/// <summary>
/// Classes implementing this interface are able to make a decision if authorization
/// is allowed.
/// </summary>
public interface IAuthorizationHandler
{
    /// <summary>
    /// Makes a decision if authorization is allowed.
    /// </summary>
    /// <param name="context">The authorization information.</param>
    Task HandleAsync(AuthorizationHandlerContext context);
}
```

<span data-ttu-id="e8334-204">Sınıf, <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> gereksinimlerin karşılanıp karşılanmadığını işaretlemek için işleyicinin kullandığı şeydir:</span><span class="sxs-lookup"><span data-stu-id="e8334-204">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="e8334-205">Aşağıdaki kod, yetkilendirme hizmetinin basitleştirilmiş (ve açıklamalı) varsayılan uygulamasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="e8334-205">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

```csharp
public async Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user, 
             object resource, IEnumerable<IAuthorizationRequirement> requirements)
{
    // Create a tracking context from the authorization inputs.
    var authContext = _contextFactory.CreateContext(requirements, user, resource);

    // By default this returns an IEnumerable<IAuthorizationHandlers> from DI.
    var handlers = await _handlers.GetHandlersAsync(authContext);

    // Invoke all handlers.
    foreach (var handler in handlers)
    {
        await handler.HandleAsync(authContext);
    }

    // Check the context, by default success is when all requirements have been met.
    return _evaluator.Evaluate(authContext);
}
```

<span data-ttu-id="e8334-206">Aşağıdaki kod tipik `ConfigureServices`bir gösterir:</span><span class="sxs-lookup"><span data-stu-id="e8334-206">The following code shows a typical `ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add all of your handlers to DI.
    services.AddSingleton<IAuthorizationHandler, MyHandler1>();
    // MyHandler2, ...

    services.AddSingleton<IAuthorizationHandler, MyHandlerN>();

    // Configure your policies
    services.AddAuthorization(options =>
          options.AddPolicy("Something",
          policy => policy.RequireClaim("Permission", "CanViewPage", "CanViewAnything")));


    services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
}
```

<span data-ttu-id="e8334-207">Kullanım <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` veya yetkilendirme için.</span><span class="sxs-lookup"><span data-stu-id="e8334-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="e8334-208">İlkeleri MVC denetleyicilerine uygulama</span><span class="sxs-lookup"><span data-stu-id="e8334-208">Applying policies to MVC controllers</span></span>

<span data-ttu-id="e8334-209">Razor Pages kullanıyorsanız, bu belgede [Razor Pages'e ilkeler uygulama'ya](#applying-policies-to-razor-pages) bakın.</span><span class="sxs-lookup"><span data-stu-id="e8334-209">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="e8334-210">İlkeler, öznitelik ilkesi adı `[Authorize]` ile kullanılarak denetleyicilere uygulanır.</span><span class="sxs-lookup"><span data-stu-id="e8334-210">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="e8334-211">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="e8334-211">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="e8334-212">İlkeleri Jilet Sayfalarına Uygulama</span><span class="sxs-lookup"><span data-stu-id="e8334-212">Applying policies to Razor Pages</span></span>

<span data-ttu-id="e8334-213">İlkeler, ilke adı ile `[Authorize]` öznitelik kullanılarak Razor Pages uygulanır.</span><span class="sxs-lookup"><span data-stu-id="e8334-213">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="e8334-214">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="e8334-214">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="e8334-215">İlkeler, [bir yetkilendirme kuralı](xref:security/authorization/razor-pages-authorization)kullanılarak Razor Pages'e de uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="e8334-215">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="e8334-216">Gereksinimler</span><span class="sxs-lookup"><span data-stu-id="e8334-216">Requirements</span></span>

<span data-ttu-id="e8334-217">Yetkilendirme gereksinimi, bir ilkenin geçerli kullanıcı ilkesini değerlendirmek için kullanabileceği veri parametreleri topluluğudur.</span><span class="sxs-lookup"><span data-stu-id="e8334-217">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="e8334-218">"En Az21" politikamızda, gereksinim minimum yaş&mdash;tek bir parametredir.</span><span class="sxs-lookup"><span data-stu-id="e8334-218">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="e8334-219">Bir gereksinim, boş bir işaretçi arabirimi olan [IAuthorizationRequirement'ı](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)uygular.</span><span class="sxs-lookup"><span data-stu-id="e8334-219">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="e8334-220">Parametrelendirilmiş bir minimum yaş gereksinimi aşağıdaki gibi uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="e8334-220">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="e8334-221">Yetkilendirme ilkesi birden çok yetkilendirme gereksinimi içeriyorsa, ilke değerlendirmesinin başarılı olabilmesi için tüm gereksinimlerin geçmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="e8334-221">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="e8334-222">Başka bir deyişle, tek bir yetkilendirme ilkesine eklenen birden çok yetkilendirme gereksinimi **VE** temelinde ele alınır.</span><span class="sxs-lookup"><span data-stu-id="e8334-222">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="e8334-223">Bir gereksinimin veri veya özelliklere sahip olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="e8334-223">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="e8334-224">Yetkilendirme işleyicileri</span><span class="sxs-lookup"><span data-stu-id="e8334-224">Authorization handlers</span></span>

<span data-ttu-id="e8334-225">Bir gereksinimin özelliklerinin değerlendirilmesini bir yetkilendirme işleyicisi sorumludur.</span><span class="sxs-lookup"><span data-stu-id="e8334-225">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="e8334-226">Yetkilendirme işleyicisi, erişime izin verilip verilmediğini belirlemek için gereksinimleri sağlanan [bir YetkilendirmeHandlerContext'a](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) karşı değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="e8334-226">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="e8334-227">Bir [gereksinimin birden çok işleyicisi](#security-authorization-policies-based-multiple-handlers)olabilir.</span><span class="sxs-lookup"><span data-stu-id="e8334-227">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="e8334-228">İşleyici, işlenecek gereksinimin bulunduğu `TRequirement` [yetkilendirmeHandler\<TRequirement>'ı ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)devralabilir.</span><span class="sxs-lookup"><span data-stu-id="e8334-228">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="e8334-229">Alternatif olarak, bir işleyici birden fazla gereksinim türünü işlemek için [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="e8334-229">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="e8334-230">Tek bir gereksinim için işleyici kullanma</span><span class="sxs-lookup"><span data-stu-id="e8334-230">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="e8334-231">Aşağıda, minimum yaş işleyicisinin tek bir gereksinimkullandığı bire bir ilişki örneği verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="e8334-231">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="e8334-232">Önceki kod, geçerli kullanıcı ilkesinin bilinen ve güvenilen bir Veren tarafından verilmiş bir doğum talebi tarihi olup olmadığını belirler.</span><span class="sxs-lookup"><span data-stu-id="e8334-232">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="e8334-233">Talep eksik olduğunda yetkilendirme gerçekleşemez ve bu durumda tamamlanmış bir görev iade edilir.</span><span class="sxs-lookup"><span data-stu-id="e8334-233">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="e8334-234">Bir talep olduğunda, kullanıcının yaşı hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="e8334-234">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="e8334-235">Kullanıcı gereksinimle tanımlanan minimum yaşı karşılıyorsa, yetkilendirme başarılı sayılır.</span><span class="sxs-lookup"><span data-stu-id="e8334-235">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="e8334-236">Yetkilendirme başarılı olduğunda, `context.Succeed` tek parametresi olarak memnun gereksinimi ile çağrılır.</span><span class="sxs-lookup"><span data-stu-id="e8334-236">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="e8334-237">Birden çok gereksinim için işleyici kullanma</span><span class="sxs-lookup"><span data-stu-id="e8334-237">Use a handler for multiple requirements</span></span>

<span data-ttu-id="e8334-238">Aşağıda, bir izin işleyicisinin üç farklı gereksinim türünü işleyebileceği bir-çok ilişkisine bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="e8334-238">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="e8334-239">Önceki [kod, bekleyen](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;olarak işaretlenmemiş gereksinimleri içeren bir özellik olan Bekleme Gereksinimleri'nden geçer.</span><span class="sxs-lookup"><span data-stu-id="e8334-239">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="e8334-240">Bir `ReadPermission` gereksinim için, kullanıcının istenen kaynağa erişmek için bir sahip veya sponsor olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e8334-240">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="e8334-241">Bir `EditPermission` veya `DeletePermission` gereksinim söz konusu olduğunda, istenen kaynağa erişmek için bir sahip olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="e8334-241">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="e8334-242">Işleyici kaydı</span><span class="sxs-lookup"><span data-stu-id="e8334-242">Handler registration</span></span>

<span data-ttu-id="e8334-243">Işleyiciler yapılandırma sırasında hizmet koleksiyonuna kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="e8334-243">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="e8334-244">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="e8334-244">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="e8334-245">Önceki kod çağırarak `MinimumAgeHandler` `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`singleton olarak kaydeder.</span><span class="sxs-lookup"><span data-stu-id="e8334-245">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="e8334-246">İşleyiciler, yerleşik [hizmet kullanım ömürlerinden](xref:fundamentals/dependency-injection#service-lifetimes)herhangi biri kullanılarak kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="e8334-246">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="e8334-247">Bir işleyici ne iade etmelidir?</span><span class="sxs-lookup"><span data-stu-id="e8334-247">What should a handler return?</span></span>

<span data-ttu-id="e8334-248">`Handle` [Işleyici örneğindeki](#security-authorization-handler-example) yöntemin hiçbir değer döndürmediğini unutmayın.</span><span class="sxs-lookup"><span data-stu-id="e8334-248">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="e8334-249">Başarı veya başarısızlık durumu nasıl gösterilir?</span><span class="sxs-lookup"><span data-stu-id="e8334-249">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="e8334-250">İşleyici, başarıyla `context.Succeed(IAuthorizationRequirement requirement)`doğrulanmış gereksinimi geçerek arayarak başarıyı gösterir.</span><span class="sxs-lookup"><span data-stu-id="e8334-250">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="e8334-251">Aynı gereksinime sahip diğer işleyiciler başarılı olabileceğinden, işleyicinin hataları genellikle işlemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="e8334-251">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="e8334-252">Diğer gereksinim işleyicileri başarılı olsa bile, `context.Fail`başarısızlığı garanti etmek için.</span><span class="sxs-lookup"><span data-stu-id="e8334-252">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="e8334-253">Bir işleyici `context.Succeed` ararsa veya `context.Fail`diğer tüm işleyiciler hala çağrılır.</span><span class="sxs-lookup"><span data-stu-id="e8334-253">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="e8334-254">Bu, gereksinimlerin, başka bir işleyici bir gereksinimi başarıyla doğrulamış veya başarısız olsa bile gerçekleşen günlük oluşturma gibi yan etkiler üretmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="e8334-254">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="e8334-255">Çağrıldığında `false` [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) özelliği (ASP.NET Core 1.1 ve sonrası olarak kullanılabilir) kısa devreler çağrıldığında `context.Fail` işleyicilerin yürütülmesi ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="e8334-255">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="e8334-256">`InvokeHandlersAfterFailure`varsayılan olarak `true`, bu durumda tüm işleyiciler çağrılır.</span><span class="sxs-lookup"><span data-stu-id="e8334-256">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="e8334-257">Kimlik doğrulaması başarısız olsa bile yetkilendirme işleyicileri çağrılır.</span><span class="sxs-lookup"><span data-stu-id="e8334-257">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="e8334-258">Neden bir gereksinim için birden fazla işleyici isteyeyim?</span><span class="sxs-lookup"><span data-stu-id="e8334-258">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="e8334-259">Değerlendirmenin **OR** bazında olmasını istediğiniz durumlarda, tek bir gereksinim için birden çok işleyici uygulayın.</span><span class="sxs-lookup"><span data-stu-id="e8334-259">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="e8334-260">Örneğin, Microsoft'un yalnızca anahtar kartlarıyla açılan kapıları vardır.</span><span class="sxs-lookup"><span data-stu-id="e8334-260">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="e8334-261">Anahtar kartınızı evde bırakırsanız, resepsiyonist geçici bir etiket yazdırır ve kapıyı sizin için açar.</span><span class="sxs-lookup"><span data-stu-id="e8334-261">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="e8334-262">Bu senaryoda, tek bir gereksinim, *BuildingEntry,* ancak birden çok işleyicileri, her biri tek bir gereksinimi inceleyen olurdu.</span><span class="sxs-lookup"><span data-stu-id="e8334-262">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="e8334-263">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="e8334-263">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="e8334-264">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="e8334-264">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="e8334-265">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="e8334-265">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="e8334-266">Her iki işleyicinin de [kayıtlı](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)olduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="e8334-266">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="e8334-267">Bir ilke , `BuildingEntryRequirement`ilke değerlendirmesi başarılı olduğunda ya işleyici başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="e8334-267">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="e8334-268">Bir ilkeyi yerine getirmek için func kullanma</span><span class="sxs-lookup"><span data-stu-id="e8334-268">Using a func to fulfill a policy</span></span>

<span data-ttu-id="e8334-269">Bir ilkeyi yerine getirmenin kodla ifade edilmesinin kolay olduğu durumlar olabilir.</span><span class="sxs-lookup"><span data-stu-id="e8334-269">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="e8334-270">İlke oluşturucu ile `Func<AuthorizationHandlerContext, bool>` politikanızı yapılandırırken bir şey sağlamak mümkündür. `RequireAssertion`</span><span class="sxs-lookup"><span data-stu-id="e8334-270">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="e8334-271">Örneğin, önceki `BadgeEntryHandler` aşağıdaki gibi yeniden yazılabilir:</span><span class="sxs-lookup"><span data-stu-id="e8334-271">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="e8334-272">İşleyicilerde MVC istek bağlamına erişim</span><span class="sxs-lookup"><span data-stu-id="e8334-272">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="e8334-273">Yetkilendirme `HandleRequirementAsync` işleyicisinde uyguladığınız yöntemin iki `AuthorizationHandlerContext` parametresi vardır: a ve `TRequirement` siz işlediğiniz.</span><span class="sxs-lookup"><span data-stu-id="e8334-273">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="e8334-274">MVC veya SignalR gibi çerçeveler, ek bilgi `Resource` aktarmak `AuthorizationHandlerContext` için tesise herhangi bir nesne eklemekte serbesttir.</span><span class="sxs-lookup"><span data-stu-id="e8334-274">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="e8334-275">Uç nokta yönlendirmesi kullanırken, yetkilendirme genellikle Yetkilendirme Middleware tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="e8334-275">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="e8334-276">Bu durumda, `Resource` özellik bir <xref:Microsoft.AspNetCore.Http.Endpoint>örneğidir.</span><span class="sxs-lookup"><span data-stu-id="e8334-276">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="e8334-277">Bitiş noktası, yönlendirdiğiniz kaynağın altında yatan kaynağı araştırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e8334-277">The endpoint can be used to probe the underlying the resource to which you're routing.</span></span> <span data-ttu-id="e8334-278">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="e8334-278">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="e8334-279">Geleneksel yönlendirme yle veya yetkilendirme MVC'nin yetkilendirme filtresinin bir parçası `Resource` olarak <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> gerçekleştiğinde, değeri bir örnektir.</span><span class="sxs-lookup"><span data-stu-id="e8334-279">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="e8334-280">Bu `HttpContext`özellik, `RouteData`MVC ve Razor Pages tarafından sağlanan her şeye ve her şeye erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="e8334-280">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="e8334-281">Özelliğin `Resource` kullanımı çerçeveye özgüdür.</span><span class="sxs-lookup"><span data-stu-id="e8334-281">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="e8334-282">Tesisteki `Resource` bilgileri kullanmak, yetkilendirme ilkelerinizi belirli çerçevelerle sınırlar.</span><span class="sxs-lookup"><span data-stu-id="e8334-282">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="e8334-283">`Resource` Özelliği anahtar kelimeyi `is` kullanarak kullanmalı ve ardından kodunuzun diğer çerçevelerde `InvalidCastException` çalıştırıldığında çökmediğinden emin olmak için dökümün başarılı olduğunu onaylamanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="e8334-283">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
