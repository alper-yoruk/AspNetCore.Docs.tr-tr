---
title: ASP.NET Core ilke tabanlı yetkilendirme
author: rick-anderson
description: ASP.NET Core uygulamasında yetkilendirme gereksinimlerini zorlama için yetkilendirme ilkesi işleyicileri oluşturma ve kullanma hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/policies
ms.openlocfilehash: 533bddc9c4499dad99cfdb3089045ea10aed4548
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074158"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="88f37-103">ASP.NET Core ilke tabanlı yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="88f37-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="88f37-104">Kapakların altında [rol tabanlı yetkilendirme](xref:security/authorization/roles) ve [talep tabanlı yetkilendirme](xref:security/authorization/claims) , bir gereksinim, gereksinim işleyicisi ve önceden yapılandırılmış bir ilke kullanır.</span><span class="sxs-lookup"><span data-stu-id="88f37-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="88f37-105">Bu yapı taşları, koddaki yetkilendirme değerlendirmeleri ifadesini destekler.</span><span class="sxs-lookup"><span data-stu-id="88f37-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="88f37-106">Sonuç daha zengin, yeniden kullanılabilir ve test edilebilir bir yetkilendirme yapısıdır.</span><span class="sxs-lookup"><span data-stu-id="88f37-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="88f37-107">Yetkilendirme ilkesi bir veya daha fazla gereksinimden oluşur.</span><span class="sxs-lookup"><span data-stu-id="88f37-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="88f37-108">Bu, yetkilendirme hizmeti yapılandırmasının bir parçası olarak kaydedilir, `Startup.ConfigureServices` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="88f37-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="88f37-109">Yukarıdaki örnekte, "AtLeast21" ilkesi oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="88f37-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="88f37-110">Bu, &mdash; gereksinimle bir parametre olarak sağlanan minimum Age 'in tek bir gereksinimine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="88f37-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="88f37-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="88f37-111">IAuthorizationService</span></span> 

<span data-ttu-id="88f37-112">Yetkilendirmenin başarılı olup olmadığını belirleyen birincil hizmet <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="88f37-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="88f37-113">Yukarıdaki kod, [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)'in iki yöntemini vurgular.</span><span class="sxs-lookup"><span data-stu-id="88f37-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="88f37-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>, yöntemi olmayan bir işaret hizmetidir ve yetkilendirme işleminin başarılı olup olmadığını izlemeye yönelik mekanizmaya yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="88f37-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="88f37-115"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>Gereksinimlerin karşılanıp karşılanmadığını denetlenmekten her biri sorumludur:</span><span class="sxs-lookup"><span data-stu-id="88f37-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="88f37-116">Bu <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> sınıf, işleyicinin gereksinimlerin karşılanıp karşılanmadığını işaretlemek için kullandığı şeydir:</span><span class="sxs-lookup"><span data-stu-id="88f37-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="88f37-117">Aşağıdaki kod, yetkilendirme hizmetinin Basitleştirilmiş (ve açıklamalar ile açıklamalı) varsayılan uygulamasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="88f37-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="88f37-118">Aşağıdaki kod tipik bir göstermektedir `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="88f37-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="88f37-119"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` Yetkilendirme için veya kullanın.</span><span class="sxs-lookup"><span data-stu-id="88f37-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="88f37-120">MVC denetleyicilerine ilke uygulama</span><span class="sxs-lookup"><span data-stu-id="88f37-120">Apply policies to MVC controllers</span></span>

<span data-ttu-id="88f37-121">RazorSayfalar kullanıyorsanız, bkz. bu belgedeki [ Razor sayfalara ilke uygulama](#apply-policies-to-razor-pages) .</span><span class="sxs-lookup"><span data-stu-id="88f37-121">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="88f37-122">İlkeler, `[Authorize]` ilke adı ile özniteliği kullanılarak denetleyicilere uygulanır.</span><span class="sxs-lookup"><span data-stu-id="88f37-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="88f37-123">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="88f37-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-razor-pages"></a><span data-ttu-id="88f37-124">İlkeleri sayfalara uygula Razor</span><span class="sxs-lookup"><span data-stu-id="88f37-124">Apply policies to Razor Pages</span></span>

<span data-ttu-id="88f37-125">İlkeler, Razor `[Authorize]` ilke adı ile özniteliği kullanılarak sayfalara uygulanır.</span><span class="sxs-lookup"><span data-stu-id="88f37-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="88f37-126">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="88f37-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="88f37-127">İlkeler, Razor bir [Yetkilendirme kuralı](xref:security/authorization/razor-pages-authorization)kullanılarak sayfalara da uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="88f37-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="88f37-128">Gereksinimler</span><span class="sxs-lookup"><span data-stu-id="88f37-128">Requirements</span></span>

<span data-ttu-id="88f37-129">Yetkilendirme gereksinimi, bir ilkenin geçerli kullanıcı sorumlusunu değerlendirmek için kullanabileceği veri parametreleri koleksiyonudur.</span><span class="sxs-lookup"><span data-stu-id="88f37-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="88f37-130">"AtLeast21" ilkenizde, gereksinim en düşük yaş olan tek bir parametredir &mdash; .</span><span class="sxs-lookup"><span data-stu-id="88f37-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="88f37-131">Bir gereksinim, boş bir işaret arabirimi olan [ıauthorizationrequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)'ı uygular.</span><span class="sxs-lookup"><span data-stu-id="88f37-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="88f37-132">Parametreli en düşük yaş gereksinimi aşağıdaki gibi uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="88f37-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="88f37-133">Yetkilendirme ilkesi birden çok yetkilendirme gereksinimi içeriyorsa, ilke değerlendirmesinin başarılı olması için tüm gereksinimlerin geçmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="88f37-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="88f37-134">Diğer bir deyişle, tek bir yetkilendirme ilkesine eklenen birden çok yetkilendirme gereksinimi bir **ve** temelinde değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="88f37-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="88f37-135">Bir gereksinimin veri veya özellikleri olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="88f37-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="88f37-136">Yetkilendirme işleyicileri</span><span class="sxs-lookup"><span data-stu-id="88f37-136">Authorization handlers</span></span>

<span data-ttu-id="88f37-137">Bir yetkilendirme işleyicisi, bir gereksinimin özelliklerinin değerlendirilmesinden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="88f37-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="88f37-138">Yetkilendirme işleyicisi, erişim izni verilip verilmediğini belirlemede, gereksinimleri belirtilen [Authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 'e göre değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="88f37-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="88f37-139">Bir gereksinimin [birden çok işleyicisi](#security-authorization-policies-based-multiple-handlers)olabilir.</span><span class="sxs-lookup"><span data-stu-id="88f37-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="88f37-140">Bir işleyici, [Authorizationhandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)' i devralınabilir, burada `TRequirement` işlenme gereksinimidir.</span><span class="sxs-lookup"><span data-stu-id="88f37-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="88f37-141">Alternatif olarak, bir işleyici birden fazla gereksinim türünü işlemek için [ıauthorizationhandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="88f37-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="88f37-142">Bir gereksinim için bir işleyici kullanın</span><span class="sxs-lookup"><span data-stu-id="88f37-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="88f37-143">Aşağıda, en az bir yaş işleyicisinin tek bir gereksinimin kullanıldığı bire bir ilişkiye örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="88f37-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="88f37-144">Önceki kod, geçerli kullanıcı sorumlusunun bilinen ve güvenilir bir veren tarafından verilen bir Doğum talebi tarihi olup olmadığını belirler.</span><span class="sxs-lookup"><span data-stu-id="88f37-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="88f37-145">Talep eksik olduğunda yetkilendirme gerçekleşemez, bu durumda tamamlanan bir görev döndürülür.</span><span class="sxs-lookup"><span data-stu-id="88f37-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="88f37-146">Bir talep bulunduğunda, kullanıcının yaşı hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="88f37-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="88f37-147">Kullanıcı gereksinim tarafından tanımlanan en düşük yaşı karşılıyorsa, Yetkilendirme başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="88f37-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="88f37-148">Yetkilendirme başarılı olduğunda, `context.Succeed` tek parametresi olarak tatmin eden gereksinimle çağrılır.</span><span class="sxs-lookup"><span data-stu-id="88f37-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="88f37-149">Birden çok gereksinim için bir işleyici kullanın</span><span class="sxs-lookup"><span data-stu-id="88f37-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="88f37-150">Aşağıda, bir izin işleyicisinin üç farklı gereksinim türünü işleyebileceği bir çoktan çoğa ilişkiye örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="88f37-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="88f37-151">Yukarıdaki kod, [Pendingrequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)öğesine geçer ve &mdash; başarılı olarak işaretlenmemiş gereksinimleri içeren bir özelliktir.</span><span class="sxs-lookup"><span data-stu-id="88f37-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="88f37-152">Bir `ReadPermission` gereksinim için, kullanıcı istenen kaynağa erişmek için bir sahip veya sponsor olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="88f37-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="88f37-153">Veya gereksinimi söz konusu olduğunda `EditPermission` `DeletePermission` , istenen kaynağa erişmek için sahip olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="88f37-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="88f37-154">İşleyici kaydı</span><span class="sxs-lookup"><span data-stu-id="88f37-154">Handler registration</span></span>

<span data-ttu-id="88f37-155">İşleyiciler, yapılandırma sırasında hizmetler koleksiyonuna kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="88f37-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="88f37-156">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="88f37-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="88f37-157">Yukarıdaki kod, `MinimumAgeHandler` çağırarak tek bir olarak kaydedilir `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="88f37-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="88f37-158">İşleyiciler, yerleşik [hizmet yaşam sürelerinin](xref:fundamentals/dependency-injection#service-lifetimes)herhangi biri kullanılarak kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="88f37-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="88f37-159">İşleyici ne döndürmelidir?</span><span class="sxs-lookup"><span data-stu-id="88f37-159">What should a handler return?</span></span>

<span data-ttu-id="88f37-160">`Handle` [İşleyici örnekteki](#security-authorization-handler-example) yöntemin değer döndürmediğini unutmayın.</span><span class="sxs-lookup"><span data-stu-id="88f37-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="88f37-161">Başarı ya da hatanın durumu nasıl belirtilir?</span><span class="sxs-lookup"><span data-stu-id="88f37-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="88f37-162">Bir işleyici çağırarak başarılı bir `context.Succeed(IAuthorizationRequirement requirement)` şekilde doğrulanan gereksinimi geçirerek başarılı olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="88f37-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="88f37-163">Aynı gereksinim için diğer işleyiciler başarılı olabileceğinden, işleyicinin sorunları genellikle işlemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="88f37-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="88f37-164">Diğer gereksinim işleyicileri başarılı olsa bile hatayı güvence altına almak için çağrısı yapın `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="88f37-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="88f37-165">Bir işleyici veya çağırırsa `context.Succeed` `context.Fail` , diğer tüm işleyiciler hala çağırılır.</span><span class="sxs-lookup"><span data-stu-id="88f37-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="88f37-166">Bu, başka bir işleyicinin bir gereksinimi başarıyla doğrulayan veya başarısız olsa bile, gereksinimlerin günlüğe kaydetme gibi yan etkileri üretmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="88f37-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="88f37-167">Olarak ayarlandığında `false` , [ınvokehandlersafterfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) özelliği (ASP.NET Core 1,1 ve üzeri sürümlerde mevcuttur), çağrıldığında işleyicilerin yürütülmesi için kısa devre dışı `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="88f37-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="88f37-168">`InvokeHandlersAfterFailure`Varsayılan olarak `true` , bu durumda tüm işleyiciler çağrılır.</span><span class="sxs-lookup"><span data-stu-id="88f37-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="88f37-169">Yetkilendirme işleyicileri, kimlik doğrulama başarısız olsa bile çağrılır.</span><span class="sxs-lookup"><span data-stu-id="88f37-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="88f37-170">Bir gereksinim için neden birden çok işleyici istiyorum?</span><span class="sxs-lookup"><span data-stu-id="88f37-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="88f37-171">Değerlendirmenin bir **veya** temelinde olmasını istediğiniz durumlarda, tek bir gereksinim için birden çok işleyici uygulayın.</span><span class="sxs-lookup"><span data-stu-id="88f37-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="88f37-172">Örneğin, Microsoft yalnızca ana kartlarla açık olan kapılara sahiptir.</span><span class="sxs-lookup"><span data-stu-id="88f37-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="88f37-173">Ana kartınızı evden bırakırsanız, alıcı geçici bir etiket yazdırır ve kapıyı sizin için açar.</span><span class="sxs-lookup"><span data-stu-id="88f37-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="88f37-174">Bu senaryoda, tek bir gereksinimi incelerken tek bir gereksinimin, *Buildingentry*, ancak birden çok işleyici vardır.</span><span class="sxs-lookup"><span data-stu-id="88f37-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="88f37-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="88f37-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="88f37-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="88f37-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="88f37-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="88f37-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="88f37-178">Her iki işleyicinin de [kaydedildiğinden](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)emin olun.</span><span class="sxs-lookup"><span data-stu-id="88f37-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="88f37-179">Bir ilke değerlendirirken her iki işleyici de başarılı olursa `BuildingEntryRequirement` , ilke değerlendirmesi başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="88f37-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="88f37-180">Bir ilkeyi yerine getirmek için bir Func kullanın</span><span class="sxs-lookup"><span data-stu-id="88f37-180">Use a func to fulfill a policy</span></span>

<span data-ttu-id="88f37-181">Kodun kodda hızlı bir şekilde kullanılması için bir ilkeyi karşıladığı durumlar olabilir.</span><span class="sxs-lookup"><span data-stu-id="88f37-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="88f37-182">İlke `Func<AuthorizationHandlerContext, bool>` Oluşturucu ile ilkenizi yapılandırırken bir sağlamak mümkündür `RequireAssertion` .</span><span class="sxs-lookup"><span data-stu-id="88f37-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="88f37-183">Örneğin, önceki, `BadgeEntryHandler` aşağıdaki gibi yeniden yazılabilir:</span><span class="sxs-lookup"><span data-stu-id="88f37-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="88f37-184">İşleyicilerde MVC istek bağlamına erişme</span><span class="sxs-lookup"><span data-stu-id="88f37-184">Access MVC request context in handlers</span></span>

<span data-ttu-id="88f37-185">`HandleRequirementAsync`Bir yetkilendirme işleyicisinde uyguladığınız yöntemin iki parametresi vardır: bir `AuthorizationHandlerContext` ve `TRequirement` işleme çalışıyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="88f37-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="88f37-186">Daha SignalR `Resource` `AuthorizationHandlerContext` fazla bilgi GEÇIRMEK için, MVC gibi çerçeveler veya üzerinde özelliğine herhangi bir nesne eklemek ücretsizdir.</span><span class="sxs-lookup"><span data-stu-id="88f37-186">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="88f37-187">Endpoint Routing kullanılırken, yetkilendirme genellikle yetkilendirme ara yazılımı tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="88f37-187">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="88f37-188">Bu durumda, `Resource` özelliği öğesinin bir örneğidir <xref:Microsoft.AspNetCore.Http.Endpoint> .</span><span class="sxs-lookup"><span data-stu-id="88f37-188">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="88f37-189">Uç noktası, yönlendirolduğunuz temel kaynağı yoklayabilmeniz için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="88f37-189">The endpoint can be used to probe the underlying resource to which you're routing.</span></span> <span data-ttu-id="88f37-190">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="88f37-190">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="88f37-191">Uç nokta geçerli bir erişim sağlamaz `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="88f37-191">The endpoint doesn't provide access to the current `HttpContext`.</span></span> <span data-ttu-id="88f37-192">Endpoint Routing kullanılırken, `IHttpContextAcessor` `HttpContext` bir yetkilendirme işleyicisinin içine erişmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="88f37-192">When using endpoint routing, use `IHttpContextAcessor` to access `HttpContext` inside of an authorization handler.</span></span> <span data-ttu-id="88f37-193">Daha fazla bilgi için bkz. [özel bileşenlerden HttpContext kullanma](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span><span class="sxs-lookup"><span data-stu-id="88f37-193">For more information, see [Use HttpContext from custom components](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span></span>

<span data-ttu-id="88f37-194">Geleneksel yönlendirme ile veya MVC 'nin yetkilendirme filtresinin bir parçası olarak yetkilendirme gerçekleştiğinde, değeri `Resource` bir <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> örnek olur.</span><span class="sxs-lookup"><span data-stu-id="88f37-194">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="88f37-195">Bu özellik `HttpContext` ,, ve IÇIN `RouteData` MVC ve sayfalar tarafından sağlanan diğer her şeye erişim sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="88f37-195">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="88f37-196">Özelliğin kullanımı Framework 'e `Resource` özgüdür.</span><span class="sxs-lookup"><span data-stu-id="88f37-196">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="88f37-197">Özelliğindeki bilgilerin kullanılması, `Resource` Yetkilendirme ilkelerinizi belirli çerçeveler ile sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="88f37-197">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="88f37-198">`Resource`Anahtar sözcüğünü kullanarak özelliği atamalısınız `is` ve sonra, kodunuzun `InvalidCastException` diğer çerçeveler üzerinde çalıştırıldığında bir ile çökmemesini sağlamak için dönüştürmenin başarılı olduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="88f37-198">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

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

<span data-ttu-id="88f37-199">Kapakların altında [rol tabanlı yetkilendirme](xref:security/authorization/roles) ve [talep tabanlı yetkilendirme](xref:security/authorization/claims) , bir gereksinim, gereksinim işleyicisi ve önceden yapılandırılmış bir ilke kullanır.</span><span class="sxs-lookup"><span data-stu-id="88f37-199">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="88f37-200">Bu yapı taşları, koddaki yetkilendirme değerlendirmeleri ifadesini destekler.</span><span class="sxs-lookup"><span data-stu-id="88f37-200">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="88f37-201">Sonuç daha zengin, yeniden kullanılabilir ve test edilebilir bir yetkilendirme yapısıdır.</span><span class="sxs-lookup"><span data-stu-id="88f37-201">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="88f37-202">Yetkilendirme ilkesi bir veya daha fazla gereksinimden oluşur.</span><span class="sxs-lookup"><span data-stu-id="88f37-202">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="88f37-203">Bu, yetkilendirme hizmeti yapılandırmasının bir parçası olarak kaydedilir, `Startup.ConfigureServices` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="88f37-203">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="88f37-204">Yukarıdaki örnekte, "AtLeast21" ilkesi oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="88f37-204">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="88f37-205">Bu, &mdash; gereksinimle bir parametre olarak sağlanan minimum Age 'in tek bir gereksinimine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="88f37-205">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="88f37-206">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="88f37-206">IAuthorizationService</span></span> 

<span data-ttu-id="88f37-207">Yetkilendirmenin başarılı olup olmadığını belirleyen birincil hizmet <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="88f37-207">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="88f37-208">Yukarıdaki kod, [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)'in iki yöntemini vurgular.</span><span class="sxs-lookup"><span data-stu-id="88f37-208">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="88f37-209"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>, yöntemi olmayan bir işaret hizmetidir ve yetkilendirme işleminin başarılı olup olmadığını izlemeye yönelik mekanizmaya yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="88f37-209"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="88f37-210"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>Gereksinimlerin karşılanıp karşılanmadığını denetlenmekten her biri sorumludur:</span><span class="sxs-lookup"><span data-stu-id="88f37-210">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="88f37-211">Bu <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> sınıf, işleyicinin gereksinimlerin karşılanıp karşılanmadığını işaretlemek için kullandığı şeydir:</span><span class="sxs-lookup"><span data-stu-id="88f37-211">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="88f37-212">Aşağıdaki kod, yetkilendirme hizmetinin Basitleştirilmiş (ve açıklamalar ile açıklamalı) varsayılan uygulamasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="88f37-212">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="88f37-213">Aşağıdaki kod tipik bir göstermektedir `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="88f37-213">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="88f37-214"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` Yetkilendirme için veya kullanın.</span><span class="sxs-lookup"><span data-stu-id="88f37-214">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="88f37-215">MVC denetleyicilerine ilke uygulama</span><span class="sxs-lookup"><span data-stu-id="88f37-215">Apply policies to MVC controllers</span></span>

<span data-ttu-id="88f37-216">RazorSayfalar kullanıyorsanız, bkz. bu belgedeki [ Razor sayfalara ilke uygulama](#apply-policies-to-razor-pages) .</span><span class="sxs-lookup"><span data-stu-id="88f37-216">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="88f37-217">İlkeler, `[Authorize]` ilke adı ile özniteliği kullanılarak denetleyicilere uygulanır.</span><span class="sxs-lookup"><span data-stu-id="88f37-217">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="88f37-218">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="88f37-218">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-razor-pages"></a><span data-ttu-id="88f37-219">İlkeleri sayfalara uygula Razor</span><span class="sxs-lookup"><span data-stu-id="88f37-219">Apply policies to Razor Pages</span></span>

<span data-ttu-id="88f37-220">İlkeler, Razor `[Authorize]` ilke adı ile özniteliği kullanılarak sayfalara uygulanır.</span><span class="sxs-lookup"><span data-stu-id="88f37-220">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="88f37-221">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="88f37-221">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="88f37-222">İlkeler, Razor bir [Yetkilendirme kuralı](xref:security/authorization/razor-pages-authorization)kullanılarak sayfalara da uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="88f37-222">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="88f37-223">Gereksinimler</span><span class="sxs-lookup"><span data-stu-id="88f37-223">Requirements</span></span>

<span data-ttu-id="88f37-224">Yetkilendirme gereksinimi, bir ilkenin geçerli kullanıcı sorumlusunu değerlendirmek için kullanabileceği veri parametreleri koleksiyonudur.</span><span class="sxs-lookup"><span data-stu-id="88f37-224">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="88f37-225">"AtLeast21" ilkenizde, gereksinim en düşük yaş olan tek bir parametredir &mdash; .</span><span class="sxs-lookup"><span data-stu-id="88f37-225">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="88f37-226">Bir gereksinim, boş bir işaret arabirimi olan [ıauthorizationrequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)'ı uygular.</span><span class="sxs-lookup"><span data-stu-id="88f37-226">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="88f37-227">Parametreli en düşük yaş gereksinimi aşağıdaki gibi uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="88f37-227">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="88f37-228">Yetkilendirme ilkesi birden çok yetkilendirme gereksinimi içeriyorsa, ilke değerlendirmesinin başarılı olması için tüm gereksinimlerin geçmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="88f37-228">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="88f37-229">Diğer bir deyişle, tek bir yetkilendirme ilkesine eklenen birden çok yetkilendirme gereksinimi bir **ve** temelinde değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="88f37-229">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="88f37-230">Bir gereksinimin veri veya özellikleri olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="88f37-230">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="88f37-231">Yetkilendirme işleyicileri</span><span class="sxs-lookup"><span data-stu-id="88f37-231">Authorization handlers</span></span>

<span data-ttu-id="88f37-232">Bir yetkilendirme işleyicisi, bir gereksinimin özelliklerinin değerlendirilmesinden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="88f37-232">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="88f37-233">Yetkilendirme işleyicisi, erişim izni verilip verilmediğini belirlemede, gereksinimleri belirtilen [Authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 'e göre değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="88f37-233">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="88f37-234">Bir gereksinimin [birden çok işleyicisi](#security-authorization-policies-based-multiple-handlers)olabilir.</span><span class="sxs-lookup"><span data-stu-id="88f37-234">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="88f37-235">Bir işleyici, [Authorizationhandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)' i devralınabilir, burada `TRequirement` işlenme gereksinimidir.</span><span class="sxs-lookup"><span data-stu-id="88f37-235">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="88f37-236">Alternatif olarak, bir işleyici birden fazla gereksinim türünü işlemek için [ıauthorizationhandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="88f37-236">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="88f37-237">Bir gereksinim için bir işleyici kullanın</span><span class="sxs-lookup"><span data-stu-id="88f37-237">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="88f37-238">Aşağıda, en az bir yaş işleyicisinin tek bir gereksinimin kullanıldığı bire bir ilişkiye örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="88f37-238">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="88f37-239">Önceki kod, geçerli kullanıcı sorumlusunun bilinen ve güvenilir bir veren tarafından verilen bir Doğum talebi tarihi olup olmadığını belirler.</span><span class="sxs-lookup"><span data-stu-id="88f37-239">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="88f37-240">Talep eksik olduğunda yetkilendirme gerçekleşemez, bu durumda tamamlanan bir görev döndürülür.</span><span class="sxs-lookup"><span data-stu-id="88f37-240">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="88f37-241">Bir talep bulunduğunda, kullanıcının yaşı hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="88f37-241">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="88f37-242">Kullanıcı gereksinim tarafından tanımlanan en düşük yaşı karşılıyorsa, Yetkilendirme başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="88f37-242">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="88f37-243">Yetkilendirme başarılı olduğunda, `context.Succeed` tek parametresi olarak tatmin eden gereksinimle çağrılır.</span><span class="sxs-lookup"><span data-stu-id="88f37-243">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="88f37-244">Birden çok gereksinim için bir işleyici kullanın</span><span class="sxs-lookup"><span data-stu-id="88f37-244">Use a handler for multiple requirements</span></span>

<span data-ttu-id="88f37-245">Aşağıda, bir izin işleyicisinin üç farklı gereksinim türünü işleyebileceği bir çoktan çoğa ilişkiye örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="88f37-245">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="88f37-246">Yukarıdaki kod, [Pendingrequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)öğesine geçer ve &mdash; başarılı olarak işaretlenmemiş gereksinimleri içeren bir özelliktir.</span><span class="sxs-lookup"><span data-stu-id="88f37-246">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="88f37-247">Bir `ReadPermission` gereksinim için, kullanıcı istenen kaynağa erişmek için bir sahip veya sponsor olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="88f37-247">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="88f37-248">Veya gereksinimi söz konusu olduğunda `EditPermission` `DeletePermission` , istenen kaynağa erişmek için sahip olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="88f37-248">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="88f37-249">İşleyici kaydı</span><span class="sxs-lookup"><span data-stu-id="88f37-249">Handler registration</span></span>

<span data-ttu-id="88f37-250">İşleyiciler, yapılandırma sırasında hizmetler koleksiyonuna kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="88f37-250">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="88f37-251">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="88f37-251">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="88f37-252">Yukarıdaki kod, `MinimumAgeHandler` çağırarak tek bir olarak kaydedilir `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="88f37-252">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="88f37-253">İşleyiciler, yerleşik [hizmet yaşam sürelerinin](xref:fundamentals/dependency-injection#service-lifetimes)herhangi biri kullanılarak kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="88f37-253">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="88f37-254">İşleyici ne döndürmelidir?</span><span class="sxs-lookup"><span data-stu-id="88f37-254">What should a handler return?</span></span>

<span data-ttu-id="88f37-255">`Handle` [İşleyici örnekteki](#security-authorization-handler-example) yöntemin değer döndürmediğini unutmayın.</span><span class="sxs-lookup"><span data-stu-id="88f37-255">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="88f37-256">Başarı ya da hatanın durumu nasıl belirtilir?</span><span class="sxs-lookup"><span data-stu-id="88f37-256">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="88f37-257">Bir işleyici çağırarak başarılı bir `context.Succeed(IAuthorizationRequirement requirement)` şekilde doğrulanan gereksinimi geçirerek başarılı olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="88f37-257">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="88f37-258">Aynı gereksinim için diğer işleyiciler başarılı olabileceğinden, işleyicinin sorunları genellikle işlemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="88f37-258">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="88f37-259">Diğer gereksinim işleyicileri başarılı olsa bile hatayı güvence altına almak için çağrısı yapın `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="88f37-259">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="88f37-260">Bir işleyici veya çağırırsa `context.Succeed` `context.Fail` , diğer tüm işleyiciler hala çağırılır.</span><span class="sxs-lookup"><span data-stu-id="88f37-260">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="88f37-261">Bu, başka bir işleyicinin bir gereksinimi başarıyla doğrulayan veya başarısız olsa bile, gereksinimlerin günlüğe kaydetme gibi yan etkileri üretmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="88f37-261">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="88f37-262">Olarak ayarlandığında `false` , [ınvokehandlersafterfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) özelliği (ASP.NET Core 1,1 ve üzeri sürümlerde mevcuttur), çağrıldığında işleyicilerin yürütülmesi için kısa devre dışı `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="88f37-262">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="88f37-263">`InvokeHandlersAfterFailure`Varsayılan olarak `true` , bu durumda tüm işleyiciler çağrılır.</span><span class="sxs-lookup"><span data-stu-id="88f37-263">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="88f37-264">Yetkilendirme işleyicileri, kimlik doğrulama başarısız olsa bile çağrılır.</span><span class="sxs-lookup"><span data-stu-id="88f37-264">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="88f37-265">Bir gereksinim için neden birden çok işleyici istiyorum?</span><span class="sxs-lookup"><span data-stu-id="88f37-265">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="88f37-266">Değerlendirmenin bir **veya** temelinde olmasını istediğiniz durumlarda, tek bir gereksinim için birden çok işleyici uygulayın.</span><span class="sxs-lookup"><span data-stu-id="88f37-266">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="88f37-267">Örneğin, Microsoft yalnızca ana kartlarla açık olan kapılara sahiptir.</span><span class="sxs-lookup"><span data-stu-id="88f37-267">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="88f37-268">Ana kartınızı evden bırakırsanız, alıcı geçici bir etiket yazdırır ve kapıyı sizin için açar.</span><span class="sxs-lookup"><span data-stu-id="88f37-268">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="88f37-269">Bu senaryoda, tek bir gereksinimi incelerken tek bir gereksinimin, *Buildingentry*, ancak birden çok işleyici vardır.</span><span class="sxs-lookup"><span data-stu-id="88f37-269">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="88f37-270">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="88f37-270">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="88f37-271">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="88f37-271">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="88f37-272">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="88f37-272">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="88f37-273">Her iki işleyicinin de [kaydedildiğinden](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)emin olun.</span><span class="sxs-lookup"><span data-stu-id="88f37-273">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="88f37-274">Bir ilke değerlendirirken her iki işleyici de başarılı olursa `BuildingEntryRequirement` , ilke değerlendirmesi başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="88f37-274">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="88f37-275">Bir ilkeyi yerine getirmek için bir Func kullanın</span><span class="sxs-lookup"><span data-stu-id="88f37-275">Use a func to fulfill a policy</span></span>

<span data-ttu-id="88f37-276">Kodun kodda hızlı bir şekilde kullanılması için bir ilkeyi karşıladığı durumlar olabilir.</span><span class="sxs-lookup"><span data-stu-id="88f37-276">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="88f37-277">İlke `Func<AuthorizationHandlerContext, bool>` Oluşturucu ile ilkenizi yapılandırırken bir sağlamak mümkündür `RequireAssertion` .</span><span class="sxs-lookup"><span data-stu-id="88f37-277">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="88f37-278">Örneğin, önceki, `BadgeEntryHandler` aşağıdaki gibi yeniden yazılabilir:</span><span class="sxs-lookup"><span data-stu-id="88f37-278">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="88f37-279">İşleyicilerde MVC istek bağlamına erişme</span><span class="sxs-lookup"><span data-stu-id="88f37-279">Access MVC request context in handlers</span></span>

<span data-ttu-id="88f37-280">`HandleRequirementAsync`Bir yetkilendirme işleyicisinde uyguladığınız yöntemin iki parametresi vardır: bir `AuthorizationHandlerContext` ve `TRequirement` işleme çalışıyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="88f37-280">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="88f37-281">Daha SignalR `Resource` `AuthorizationHandlerContext` fazla bilgi GEÇIRMEK için, MVC gibi çerçeveler veya üzerinde özelliğine herhangi bir nesne eklemek ücretsizdir.</span><span class="sxs-lookup"><span data-stu-id="88f37-281">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="88f37-282">Örneğin, MVC, özelliğinde [Authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext) örneğini geçirir `Resource` .</span><span class="sxs-lookup"><span data-stu-id="88f37-282">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="88f37-283">Bu özellik `HttpContext` ,, ve IÇIN `RouteData` MVC ve sayfalar tarafından sağlanan diğer her şeye erişim sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="88f37-283">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="88f37-284">Özelliğin kullanımı Framework 'e `Resource` özgüdür.</span><span class="sxs-lookup"><span data-stu-id="88f37-284">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="88f37-285">Özelliğindeki bilgilerin kullanılması, `Resource` Yetkilendirme ilkelerinizi belirli çerçeveler ile sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="88f37-285">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="88f37-286">`Resource`Anahtar sözcüğünü kullanarak özelliği atamalısınız `is` ve sonra, kodunuzun `InvalidCastException` diğer çerçeveler üzerinde çalıştırıldığında bir ile çökmemesini sağlamak için dönüştürmenin başarılı olduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="88f37-286">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
