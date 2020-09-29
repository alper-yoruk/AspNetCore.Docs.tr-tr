---
title: ASP.NET Core ilke tabanlı yetkilendirme
author: rick-anderson
description: ASP.NET Core uygulamasında yetkilendirme gereksinimlerini zorlama için yetkilendirme ilkesi işleyicileri oluşturma ve kullanma hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 04/15/2020
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
uid: security/authorization/policies
ms.openlocfilehash: af282ec1f82b2ac31fd0b46b2406110e24e9211b
ms.sourcegitcommit: 6c82d78662332cd40d614019b9ed17c46e25be28
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91424249"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="3acc3-103">ASP.NET Core ilke tabanlı yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="3acc3-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3acc3-104">Kapakların altında [rol tabanlı yetkilendirme](xref:security/authorization/roles) ve [talep tabanlı yetkilendirme](xref:security/authorization/claims) , bir gereksinim, gereksinim işleyicisi ve önceden yapılandırılmış bir ilke kullanır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="3acc3-105">Bu yapı taşları, koddaki yetkilendirme değerlendirmeleri ifadesini destekler.</span><span class="sxs-lookup"><span data-stu-id="3acc3-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="3acc3-106">Sonuç daha zengin, yeniden kullanılabilir ve test edilebilir bir yetkilendirme yapısıdır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="3acc3-107">Yetkilendirme ilkesi bir veya daha fazla gereksinimden oluşur.</span><span class="sxs-lookup"><span data-stu-id="3acc3-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="3acc3-108">Bu, yetkilendirme hizmeti yapılandırmasının bir parçası olarak kaydedilir, `Startup.ConfigureServices` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="3acc3-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="3acc3-109">Yukarıdaki örnekte, "AtLeast21" ilkesi oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3acc3-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="3acc3-110">Bu, &mdash; gereksinimle bir parametre olarak sağlanan minimum Age 'in tek bir gereksinimine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="3acc3-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="3acc3-111">IAuthorizationService</span></span> 

<span data-ttu-id="3acc3-112">Yetkilendirmenin başarılı olup olmadığını belirleyen birincil hizmet <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="3acc3-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="3acc3-113">Yukarıdaki kod, [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)'in iki yöntemini vurgular.</span><span class="sxs-lookup"><span data-stu-id="3acc3-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="3acc3-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> , yöntemi olmayan bir işaret hizmetidir ve yetkilendirme işleminin başarılı olup olmadığını izlemeye yönelik mekanizmaya yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="3acc3-115"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>Gereksinimlerin karşılanıp karşılanmadığını denetlenmekten her biri sorumludur:</span><span class="sxs-lookup"><span data-stu-id="3acc3-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="3acc3-116">Bu <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> sınıf, işleyicinin gereksinimlerin karşılanıp karşılanmadığını işaretlemek için kullandığı şeydir:</span><span class="sxs-lookup"><span data-stu-id="3acc3-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="3acc3-117">Aşağıdaki kod, yetkilendirme hizmetinin Basitleştirilmiş (ve açıklamalar ile açıklamalı) varsayılan uygulamasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="3acc3-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="3acc3-118">Aşağıdaki kod tipik bir göstermektedir `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3acc3-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="3acc3-119"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` Yetkilendirme için veya kullanın.</span><span class="sxs-lookup"><span data-stu-id="3acc3-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="3acc3-120">MVC denetleyicilerine ilke uygulama</span><span class="sxs-lookup"><span data-stu-id="3acc3-120">Apply policies to MVC controllers</span></span>

<span data-ttu-id="3acc3-121">RazorSayfalar kullanıyorsanız, bkz. bu belgedeki [ Razor sayfalara ilke uygulama](#apply-policies-to-razor-pages) .</span><span class="sxs-lookup"><span data-stu-id="3acc3-121">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="3acc3-122">İlkeler, `[Authorize]` ilke adı ile özniteliği kullanılarak denetleyicilere uygulanır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="3acc3-123">Örnek:</span><span class="sxs-lookup"><span data-stu-id="3acc3-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="3acc3-124">İlkeleri sayfalara uygula Razor</span><span class="sxs-lookup"><span data-stu-id="3acc3-124">Apply policies to Razor Pages</span></span>

<span data-ttu-id="3acc3-125">İlkeler, Razor `[Authorize]` ilke adı ile özniteliği kullanılarak sayfalara uygulanır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="3acc3-126">Örnek:</span><span class="sxs-lookup"><span data-stu-id="3acc3-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="3acc3-127">İlkeler ***not*** Razor sayfa işleyici düzeyinde uygulanamazlar, sayfaya uygulanmaları gerekir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-127">Policies can ***not*** be applied at the Razor Page handler level, they must be applied to the Page.</span></span>

<span data-ttu-id="3acc3-128">İlkeler, Razor bir [Yetkilendirme kuralı](xref:security/authorization/razor-pages-authorization)kullanılarak sayfalara uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-128">Policies can be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="3acc3-129">Gereksinimler</span><span class="sxs-lookup"><span data-stu-id="3acc3-129">Requirements</span></span>

<span data-ttu-id="3acc3-130">Yetkilendirme gereksinimi, bir ilkenin geçerli kullanıcı sorumlusunu değerlendirmek için kullanabileceği veri parametreleri koleksiyonudur.</span><span class="sxs-lookup"><span data-stu-id="3acc3-130">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="3acc3-131">"AtLeast21" ilkenizde, gereksinim en düşük yaş olan tek bir parametredir &mdash; .</span><span class="sxs-lookup"><span data-stu-id="3acc3-131">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="3acc3-132">Bir gereksinim, boş bir işaret arabirimi olan [ıauthorizationrequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)'ı uygular.</span><span class="sxs-lookup"><span data-stu-id="3acc3-132">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="3acc3-133">Parametreli en düşük yaş gereksinimi aşağıdaki gibi uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="3acc3-133">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="3acc3-134">Yetkilendirme ilkesi birden çok yetkilendirme gereksinimi içeriyorsa, ilke değerlendirmesinin başarılı olması için tüm gereksinimlerin geçmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-134">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="3acc3-135">Diğer bir deyişle, tek bir yetkilendirme ilkesine eklenen birden çok yetkilendirme gereksinimi bir **ve** temelinde değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-135">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="3acc3-136">Bir gereksinimin veri veya özellikleri olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="3acc3-136">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="3acc3-137">Yetkilendirme işleyicileri</span><span class="sxs-lookup"><span data-stu-id="3acc3-137">Authorization handlers</span></span>

<span data-ttu-id="3acc3-138">Bir yetkilendirme işleyicisi, bir gereksinimin özelliklerinin değerlendirilmesinden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="3acc3-138">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="3acc3-139">Yetkilendirme işleyicisi, erişim izni verilip verilmediğini belirlemede, gereksinimleri belirtilen [Authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 'e göre değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-139">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="3acc3-140">Bir gereksinimin [birden çok işleyicisi](#security-authorization-policies-based-multiple-handlers)olabilir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-140">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="3acc3-141">Bir işleyici, [Authorizationhandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)' i devralınabilir, burada `TRequirement` işlenme gereksinimidir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-141">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="3acc3-142">Alternatif olarak, bir işleyici birden fazla gereksinim türünü işlemek için [ıauthorizationhandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-142">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="3acc3-143">Bir gereksinim için bir işleyici kullanın</span><span class="sxs-lookup"><span data-stu-id="3acc3-143">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="3acc3-144">Aşağıda, en az bir yaş işleyicisinin tek bir gereksinimin kullanıldığı bire bir ilişkiye örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="3acc3-144">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="3acc3-145">Önceki kod, geçerli kullanıcı sorumlusunun bilinen ve güvenilir bir veren tarafından verilen bir Doğum talebi tarihi olup olmadığını belirler.</span><span class="sxs-lookup"><span data-stu-id="3acc3-145">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="3acc3-146">Talep eksik olduğunda yetkilendirme gerçekleşemez, bu durumda tamamlanan bir görev döndürülür.</span><span class="sxs-lookup"><span data-stu-id="3acc3-146">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="3acc3-147">Bir talep bulunduğunda, kullanıcının yaşı hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-147">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="3acc3-148">Kullanıcı gereksinim tarafından tanımlanan en düşük yaşı karşılıyorsa, Yetkilendirme başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="3acc3-148">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="3acc3-149">Yetkilendirme başarılı olduğunda, `context.Succeed` tek parametresi olarak tatmin eden gereksinimle çağrılır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-149">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="3acc3-150">Birden çok gereksinim için bir işleyici kullanın</span><span class="sxs-lookup"><span data-stu-id="3acc3-150">Use a handler for multiple requirements</span></span>

<span data-ttu-id="3acc3-151">Aşağıda, bir izin işleyicisinin üç farklı gereksinim türünü işleyebileceği bir çoktan çoğa ilişkiye örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="3acc3-151">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="3acc3-152">Yukarıdaki kod, [Pendingrequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)öğesine geçer ve &mdash; başarılı olarak işaretlenmemiş gereksinimleri içeren bir özelliktir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-152">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="3acc3-153">Bir `ReadPermission` gereksinim için, kullanıcı istenen kaynağa erişmek için bir sahip veya sponsor olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-153">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="3acc3-154">Veya gereksinimi söz konusu olduğunda `EditPermission` `DeletePermission` , istenen kaynağa erişmek için sahip olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-154">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="3acc3-155">İşleyici kaydı</span><span class="sxs-lookup"><span data-stu-id="3acc3-155">Handler registration</span></span>

<span data-ttu-id="3acc3-156">İşleyiciler, yapılandırma sırasında hizmetler koleksiyonuna kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-156">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="3acc3-157">Örnek:</span><span class="sxs-lookup"><span data-stu-id="3acc3-157">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="3acc3-158">Yukarıdaki kod, `MinimumAgeHandler` çağırarak tek bir olarak kaydedilir `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="3acc3-158">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="3acc3-159">İşleyiciler, yerleşik [hizmet yaşam sürelerinin](xref:fundamentals/dependency-injection#service-lifetimes)herhangi biri kullanılarak kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-159">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="3acc3-160">İşleyici ne döndürmelidir?</span><span class="sxs-lookup"><span data-stu-id="3acc3-160">What should a handler return?</span></span>

<span data-ttu-id="3acc3-161">`Handle` [İşleyici örnekteki](#security-authorization-handler-example) yöntemin değer döndürmediğini unutmayın.</span><span class="sxs-lookup"><span data-stu-id="3acc3-161">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="3acc3-162">Başarı ya da hatanın durumu nasıl belirtilir?</span><span class="sxs-lookup"><span data-stu-id="3acc3-162">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="3acc3-163">Bir işleyici çağırarak başarılı bir `context.Succeed(IAuthorizationRequirement requirement)` şekilde doğrulanan gereksinimi geçirerek başarılı olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-163">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="3acc3-164">Aynı gereksinim için diğer işleyiciler başarılı olabileceğinden, işleyicinin sorunları genellikle işlemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="3acc3-164">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="3acc3-165">Diğer gereksinim işleyicileri başarılı olsa bile hatayı güvence altına almak için çağrısı yapın `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="3acc3-165">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="3acc3-166">Bir işleyici veya çağırırsa `context.Succeed` `context.Fail` , diğer tüm işleyiciler hala çağırılır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-166">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="3acc3-167">Bu, başka bir işleyicinin bir gereksinimi başarıyla doğrulayan veya başarısız olsa bile, gereksinimlerin günlüğe kaydetme gibi yan etkileri üretmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-167">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="3acc3-168">Olarak ayarlandığında `false` , [ınvokehandlersafterfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) özelliği, çağrıldığında işleyicilerin yürütülmesi kısa devre dışı `context.Fail` olur.</span><span class="sxs-lookup"><span data-stu-id="3acc3-168">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="3acc3-169">`InvokeHandlersAfterFailure` Varsayılan olarak `true` , bu durumda tüm işleyiciler çağrılır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-169">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="3acc3-170">Yetkilendirme işleyicileri, kimlik doğrulama başarısız olsa bile çağrılır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-170">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="3acc3-171">Bir gereksinim için neden birden çok işleyici istiyorum?</span><span class="sxs-lookup"><span data-stu-id="3acc3-171">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="3acc3-172">Değerlendirmenin bir **veya** temelinde olmasını istediğiniz durumlarda, tek bir gereksinim için birden çok işleyici uygulayın.</span><span class="sxs-lookup"><span data-stu-id="3acc3-172">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="3acc3-173">Örneğin, Microsoft yalnızca ana kartlarla açık olan kapılara sahiptir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-173">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="3acc3-174">Ana kartınızı evden bırakırsanız, alıcı geçici bir etiket yazdırır ve kapıyı sizin için açar.</span><span class="sxs-lookup"><span data-stu-id="3acc3-174">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="3acc3-175">Bu senaryoda, tek bir gereksinimi incelerken tek bir gereksinimin, *Buildingentry*, ancak birden çok işleyici vardır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-175">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="3acc3-176">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="3acc3-176">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="3acc3-177">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="3acc3-177">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="3acc3-178">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="3acc3-178">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="3acc3-179">Her iki işleyicinin de [kaydedildiğinden](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)emin olun.</span><span class="sxs-lookup"><span data-stu-id="3acc3-179">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="3acc3-180">Bir ilke değerlendirirken her iki işleyici de başarılı olursa `BuildingEntryRequirement` , ilke değerlendirmesi başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="3acc3-180">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="3acc3-181">Bir ilkeyi yerine getirmek için bir Func kullanın</span><span class="sxs-lookup"><span data-stu-id="3acc3-181">Use a func to fulfill a policy</span></span>

<span data-ttu-id="3acc3-182">Kodun kodda hızlı bir şekilde kullanılması için bir ilkeyi karşıladığı durumlar olabilir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-182">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="3acc3-183">İlke `Func<AuthorizationHandlerContext, bool>` Oluşturucu ile ilkenizi yapılandırırken bir sağlamak mümkündür `RequireAssertion` .</span><span class="sxs-lookup"><span data-stu-id="3acc3-183">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="3acc3-184">Örneğin, önceki, `BadgeEntryHandler` aşağıdaki gibi yeniden yazılabilir:</span><span class="sxs-lookup"><span data-stu-id="3acc3-184">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="3acc3-185">İşleyicilerde MVC istek bağlamına erişme</span><span class="sxs-lookup"><span data-stu-id="3acc3-185">Access MVC request context in handlers</span></span>

<span data-ttu-id="3acc3-186">`HandleRequirementAsync`Bir yetkilendirme işleyicisinde uyguladığınız yöntemin iki parametresi vardır: bir `AuthorizationHandlerContext` ve `TRequirement` işleme çalışıyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="3acc3-186">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="3acc3-187">Daha SignalR `Resource` `AuthorizationHandlerContext` fazla bilgi GEÇIRMEK için, MVC gibi çerçeveler veya üzerinde özelliğine herhangi bir nesne eklemek ücretsizdir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-187">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="3acc3-188">Endpoint Routing kullanılırken, yetkilendirme genellikle yetkilendirme ara yazılımı tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-188">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="3acc3-189">Bu durumda, `Resource` özelliği öğesinin bir örneğidir <xref:Microsoft.AspNetCore.Http.Endpoint> .</span><span class="sxs-lookup"><span data-stu-id="3acc3-189">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="3acc3-190">Uç noktası, yönlendirolduğunuz temel kaynağı yoklayabilmeniz için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-190">The endpoint can be used to probe the underlying resource to which you're routing.</span></span> <span data-ttu-id="3acc3-191">Örnek:</span><span class="sxs-lookup"><span data-stu-id="3acc3-191">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="3acc3-192">Uç nokta geçerli bir erişim sağlamaz `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="3acc3-192">The endpoint doesn't provide access to the current `HttpContext`.</span></span> <span data-ttu-id="3acc3-193">Endpoint Routing kullanılırken, `IHttpContextAcessor` `HttpContext` bir yetkilendirme işleyicisinin içine erişmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="3acc3-193">When using endpoint routing, use `IHttpContextAcessor` to access `HttpContext` inside of an authorization handler.</span></span> <span data-ttu-id="3acc3-194">Daha fazla bilgi için bkz. [özel bileşenlerden HttpContext kullanma](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span><span class="sxs-lookup"><span data-stu-id="3acc3-194">For more information, see [Use HttpContext from custom components](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).</span></span>

<span data-ttu-id="3acc3-195">Geleneksel yönlendirme ile veya MVC 'nin yetkilendirme filtresinin bir parçası olarak yetkilendirme gerçekleştiğinde, değeri `Resource` bir <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> örnek olur.</span><span class="sxs-lookup"><span data-stu-id="3acc3-195">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="3acc3-196">Bu özellik `HttpContext` ,, ve IÇIN `RouteData` MVC ve sayfalar tarafından sağlanan diğer her şeye erişim sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="3acc3-196">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="3acc3-197">Özelliğin kullanımı Framework 'e `Resource` özgüdür.</span><span class="sxs-lookup"><span data-stu-id="3acc3-197">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="3acc3-198">Özelliğindeki bilgilerin kullanılması, `Resource` Yetkilendirme ilkelerinizi belirli çerçeveler ile sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-198">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="3acc3-199">`Resource`Anahtar sözcüğünü kullanarak özelliği atamalısınız `is` ve sonra, kodunuzun `InvalidCastException` diğer çerçeveler üzerinde çalıştırıldığında bir ile çökmemesini sağlamak için dönüştürmenin başarılı olduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="3acc3-199">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

## <a name="globally-require-all-users-to-be-authenticated"></a><span data-ttu-id="3acc3-200">Genel olarak tüm kullanıcıların kimliğinin doğrulanmasını gerektir</span><span class="sxs-lookup"><span data-stu-id="3acc3-200">Globally require all users to be authenticated</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3acc3-201">Kapakların altında [rol tabanlı yetkilendirme](xref:security/authorization/roles) ve [talep tabanlı yetkilendirme](xref:security/authorization/claims) , bir gereksinim, gereksinim işleyicisi ve önceden yapılandırılmış bir ilke kullanır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-201">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="3acc3-202">Bu yapı taşları, koddaki yetkilendirme değerlendirmeleri ifadesini destekler.</span><span class="sxs-lookup"><span data-stu-id="3acc3-202">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="3acc3-203">Sonuç daha zengin, yeniden kullanılabilir ve test edilebilir bir yetkilendirme yapısıdır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-203">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="3acc3-204">Yetkilendirme ilkesi bir veya daha fazla gereksinimden oluşur.</span><span class="sxs-lookup"><span data-stu-id="3acc3-204">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="3acc3-205">Bu, yetkilendirme hizmeti yapılandırmasının bir parçası olarak kaydedilir, `Startup.ConfigureServices` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="3acc3-205">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="3acc3-206">Yukarıdaki örnekte, "AtLeast21" ilkesi oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3acc3-206">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="3acc3-207">Bu, &mdash; gereksinimle bir parametre olarak sağlanan minimum Age 'in tek bir gereksinimine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-207">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="3acc3-208">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="3acc3-208">IAuthorizationService</span></span> 

<span data-ttu-id="3acc3-209">Yetkilendirmenin başarılı olup olmadığını belirleyen birincil hizmet <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :</span><span class="sxs-lookup"><span data-stu-id="3acc3-209">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

<span data-ttu-id="3acc3-210">Yukarıdaki kod, [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)'in iki yöntemini vurgular.</span><span class="sxs-lookup"><span data-stu-id="3acc3-210">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="3acc3-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> , yöntemi olmayan bir işaret hizmetidir ve yetkilendirme işleminin başarılı olup olmadığını izlemeye yönelik mekanizmaya yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-211"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="3acc3-212"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>Gereksinimlerin karşılanıp karşılanmadığını denetlenmekten her biri sorumludur:</span><span class="sxs-lookup"><span data-stu-id="3acc3-212">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="3acc3-213">Bu <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> sınıf, işleyicinin gereksinimlerin karşılanıp karşılanmadığını işaretlemek için kullandığı şeydir:</span><span class="sxs-lookup"><span data-stu-id="3acc3-213">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="3acc3-214">Aşağıdaki kod, yetkilendirme hizmetinin Basitleştirilmiş (ve açıklamalar ile açıklamalı) varsayılan uygulamasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="3acc3-214">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="3acc3-215">Aşağıdaki kod tipik bir göstermektedir `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="3acc3-215">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="3acc3-216"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` Yetkilendirme için veya kullanın.</span><span class="sxs-lookup"><span data-stu-id="3acc3-216">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="apply-policies-to-mvc-controllers"></a><span data-ttu-id="3acc3-217">MVC denetleyicilerine ilke uygulama</span><span class="sxs-lookup"><span data-stu-id="3acc3-217">Apply policies to MVC controllers</span></span>

<span data-ttu-id="3acc3-218">RazorSayfalar kullanıyorsanız, bkz. bu belgedeki [ Razor sayfalara ilke uygulama](#apply-policies-to-razor-pages) .</span><span class="sxs-lookup"><span data-stu-id="3acc3-218">If you're using Razor Pages, see [Apply policies to Razor Pages](#apply-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="3acc3-219">İlkeler, `[Authorize]` ilke adı ile özniteliği kullanılarak denetleyicilere uygulanır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-219">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="3acc3-220">Örnek:</span><span class="sxs-lookup"><span data-stu-id="3acc3-220">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a><span data-ttu-id="3acc3-221">İlkeleri sayfalara uygula Razor</span><span class="sxs-lookup"><span data-stu-id="3acc3-221">Apply policies to Razor Pages</span></span>

<span data-ttu-id="3acc3-222">İlkeler, Razor `[Authorize]` ilke adı ile özniteliği kullanılarak sayfalara uygulanır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-222">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="3acc3-223">Örnek:</span><span class="sxs-lookup"><span data-stu-id="3acc3-223">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="3acc3-224">İlkeler, Razor bir [Yetkilendirme kuralı](xref:security/authorization/razor-pages-authorization)kullanılarak sayfalara da uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-224">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="3acc3-225">Gereksinimler</span><span class="sxs-lookup"><span data-stu-id="3acc3-225">Requirements</span></span>

<span data-ttu-id="3acc3-226">Yetkilendirme gereksinimi, bir ilkenin geçerli kullanıcı sorumlusunu değerlendirmek için kullanabileceği veri parametreleri koleksiyonudur.</span><span class="sxs-lookup"><span data-stu-id="3acc3-226">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="3acc3-227">"AtLeast21" ilkenizde, gereksinim en düşük yaş olan tek bir parametredir &mdash; .</span><span class="sxs-lookup"><span data-stu-id="3acc3-227">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="3acc3-228">Bir gereksinim, boş bir işaret arabirimi olan [ıauthorizationrequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)'ı uygular.</span><span class="sxs-lookup"><span data-stu-id="3acc3-228">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="3acc3-229">Parametreli en düşük yaş gereksinimi aşağıdaki gibi uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="3acc3-229">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="3acc3-230">Yetkilendirme ilkesi birden çok yetkilendirme gereksinimi içeriyorsa, ilke değerlendirmesinin başarılı olması için tüm gereksinimlerin geçmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-230">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="3acc3-231">Diğer bir deyişle, tek bir yetkilendirme ilkesine eklenen birden çok yetkilendirme gereksinimi bir **ve** temelinde değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-231">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="3acc3-232">Bir gereksinimin veri veya özellikleri olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="3acc3-232">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="3acc3-233">Yetkilendirme işleyicileri</span><span class="sxs-lookup"><span data-stu-id="3acc3-233">Authorization handlers</span></span>

<span data-ttu-id="3acc3-234">Bir yetkilendirme işleyicisi, bir gereksinimin özelliklerinin değerlendirilmesinden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="3acc3-234">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="3acc3-235">Yetkilendirme işleyicisi, erişim izni verilip verilmediğini belirlemede, gereksinimleri belirtilen [Authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 'e göre değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-235">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="3acc3-236">Bir gereksinimin [birden çok işleyicisi](#security-authorization-policies-based-multiple-handlers)olabilir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-236">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="3acc3-237">Bir işleyici, [Authorizationhandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)' i devralınabilir, burada `TRequirement` işlenme gereksinimidir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-237">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="3acc3-238">Alternatif olarak, bir işleyici birden fazla gereksinim türünü işlemek için [ıauthorizationhandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-238">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="3acc3-239">Bir gereksinim için bir işleyici kullanın</span><span class="sxs-lookup"><span data-stu-id="3acc3-239">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="3acc3-240">Aşağıda, en az bir yaş işleyicisinin tek bir gereksinimin kullanıldığı bire bir ilişkiye örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="3acc3-240">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="3acc3-241">Önceki kod, geçerli kullanıcı sorumlusunun bilinen ve güvenilir bir veren tarafından verilen bir Doğum talebi tarihi olup olmadığını belirler.</span><span class="sxs-lookup"><span data-stu-id="3acc3-241">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="3acc3-242">Talep eksik olduğunda yetkilendirme gerçekleşemez, bu durumda tamamlanan bir görev döndürülür.</span><span class="sxs-lookup"><span data-stu-id="3acc3-242">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="3acc3-243">Bir talep bulunduğunda, kullanıcının yaşı hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-243">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="3acc3-244">Kullanıcı gereksinim tarafından tanımlanan en düşük yaşı karşılıyorsa, Yetkilendirme başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="3acc3-244">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="3acc3-245">Yetkilendirme başarılı olduğunda, `context.Succeed` tek parametresi olarak tatmin eden gereksinimle çağrılır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-245">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="3acc3-246">Birden çok gereksinim için bir işleyici kullanın</span><span class="sxs-lookup"><span data-stu-id="3acc3-246">Use a handler for multiple requirements</span></span>

<span data-ttu-id="3acc3-247">Aşağıda, bir izin işleyicisinin üç farklı gereksinim türünü işleyebileceği bir çoktan çoğa ilişkiye örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="3acc3-247">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="3acc3-248">Yukarıdaki kod, [Pendingrequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)öğesine geçer ve &mdash; başarılı olarak işaretlenmemiş gereksinimleri içeren bir özelliktir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-248">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="3acc3-249">Bir `ReadPermission` gereksinim için, kullanıcı istenen kaynağa erişmek için bir sahip veya sponsor olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-249">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="3acc3-250">Veya gereksinimi söz konusu olduğunda `EditPermission` `DeletePermission` , istenen kaynağa erişmek için sahip olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-250">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="3acc3-251">İşleyici kaydı</span><span class="sxs-lookup"><span data-stu-id="3acc3-251">Handler registration</span></span>

<span data-ttu-id="3acc3-252">İşleyiciler, yapılandırma sırasında hizmetler koleksiyonuna kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-252">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="3acc3-253">Örnek:</span><span class="sxs-lookup"><span data-stu-id="3acc3-253">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="3acc3-254">Yukarıdaki kod, `MinimumAgeHandler` çağırarak tek bir olarak kaydedilir `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` .</span><span class="sxs-lookup"><span data-stu-id="3acc3-254">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="3acc3-255">İşleyiciler, yerleşik [hizmet yaşam sürelerinin](xref:fundamentals/dependency-injection#service-lifetimes)herhangi biri kullanılarak kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-255">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="3acc3-256">İşleyici ne döndürmelidir?</span><span class="sxs-lookup"><span data-stu-id="3acc3-256">What should a handler return?</span></span>

<span data-ttu-id="3acc3-257">`Handle` [İşleyici örnekteki](#security-authorization-handler-example) yöntemin değer döndürmediğini unutmayın.</span><span class="sxs-lookup"><span data-stu-id="3acc3-257">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="3acc3-258">Başarı ya da hatanın durumu nasıl belirtilir?</span><span class="sxs-lookup"><span data-stu-id="3acc3-258">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="3acc3-259">Bir işleyici çağırarak başarılı bir `context.Succeed(IAuthorizationRequirement requirement)` şekilde doğrulanan gereksinimi geçirerek başarılı olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-259">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="3acc3-260">Aynı gereksinim için diğer işleyiciler başarılı olabileceğinden, işleyicinin sorunları genellikle işlemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="3acc3-260">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="3acc3-261">Diğer gereksinim işleyicileri başarılı olsa bile hatayı güvence altına almak için çağrısı yapın `context.Fail` .</span><span class="sxs-lookup"><span data-stu-id="3acc3-261">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="3acc3-262">Bir işleyici veya çağırırsa `context.Succeed` `context.Fail` , diğer tüm işleyiciler hala çağırılır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-262">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="3acc3-263">Bu, başka bir işleyicinin bir gereksinimi başarıyla doğrulayan veya başarısız olsa bile, gereksinimlerin günlüğe kaydetme gibi yan etkileri üretmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-263">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="3acc3-264">Olarak ayarlandığında `false` , [ınvokehandlersafterfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) özelliği, çağrıldığında işleyicilerin yürütülmesi kısa devre dışı `context.Fail` olur.</span><span class="sxs-lookup"><span data-stu-id="3acc3-264">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="3acc3-265">`InvokeHandlersAfterFailure` Varsayılan olarak `true` , bu durumda tüm işleyiciler çağrılır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-265">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="3acc3-266">Yetkilendirme işleyicileri, kimlik doğrulama başarısız olsa bile çağrılır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-266">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="3acc3-267">Bir gereksinim için neden birden çok işleyici istiyorum?</span><span class="sxs-lookup"><span data-stu-id="3acc3-267">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="3acc3-268">Değerlendirmenin bir **veya** temelinde olmasını istediğiniz durumlarda, tek bir gereksinim için birden çok işleyici uygulayın.</span><span class="sxs-lookup"><span data-stu-id="3acc3-268">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="3acc3-269">Örneğin, Microsoft yalnızca ana kartlarla açık olan kapılara sahiptir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-269">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="3acc3-270">Ana kartınızı evden bırakırsanız, alıcı geçici bir etiket yazdırır ve kapıyı sizin için açar.</span><span class="sxs-lookup"><span data-stu-id="3acc3-270">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="3acc3-271">Bu senaryoda, tek bir gereksinimi incelerken tek bir gereksinimin, *Buildingentry*, ancak birden çok işleyici vardır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-271">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="3acc3-272">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="3acc3-272">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="3acc3-273">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="3acc3-273">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="3acc3-274">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="3acc3-274">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="3acc3-275">Her iki işleyicinin de [kaydedildiğinden](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)emin olun.</span><span class="sxs-lookup"><span data-stu-id="3acc3-275">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="3acc3-276">Bir ilke değerlendirirken her iki işleyici de başarılı olursa `BuildingEntryRequirement` , ilke değerlendirmesi başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="3acc3-276">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="use-a-func-to-fulfill-a-policy"></a><span data-ttu-id="3acc3-277">Bir ilkeyi yerine getirmek için bir Func kullanın</span><span class="sxs-lookup"><span data-stu-id="3acc3-277">Use a func to fulfill a policy</span></span>

<span data-ttu-id="3acc3-278">Kodun kodda hızlı bir şekilde kullanılması için bir ilkeyi karşıladığı durumlar olabilir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-278">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="3acc3-279">İlke `Func<AuthorizationHandlerContext, bool>` Oluşturucu ile ilkenizi yapılandırırken bir sağlamak mümkündür `RequireAssertion` .</span><span class="sxs-lookup"><span data-stu-id="3acc3-279">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="3acc3-280">Örneğin, önceki, `BadgeEntryHandler` aşağıdaki gibi yeniden yazılabilir:</span><span class="sxs-lookup"><span data-stu-id="3acc3-280">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a><span data-ttu-id="3acc3-281">İşleyicilerde MVC istek bağlamına erişme</span><span class="sxs-lookup"><span data-stu-id="3acc3-281">Access MVC request context in handlers</span></span>

<span data-ttu-id="3acc3-282">`HandleRequirementAsync`Bir yetkilendirme işleyicisinde uyguladığınız yöntemin iki parametresi vardır: bir `AuthorizationHandlerContext` ve `TRequirement` işleme çalışıyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="3acc3-282">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="3acc3-283">Daha SignalR `Resource` `AuthorizationHandlerContext` fazla bilgi GEÇIRMEK için, MVC gibi çerçeveler veya üzerinde özelliğine herhangi bir nesne eklemek ücretsizdir.</span><span class="sxs-lookup"><span data-stu-id="3acc3-283">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="3acc3-284">Örneğin, MVC, özelliğinde [Authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext) örneğini geçirir `Resource` .</span><span class="sxs-lookup"><span data-stu-id="3acc3-284">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="3acc3-285">Bu özellik `HttpContext` ,, ve IÇIN `RouteData` MVC ve sayfalar tarafından sağlanan diğer her şeye erişim sağlar Razor .</span><span class="sxs-lookup"><span data-stu-id="3acc3-285">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="3acc3-286">Özelliğin kullanımı Framework 'e `Resource` özgüdür.</span><span class="sxs-lookup"><span data-stu-id="3acc3-286">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="3acc3-287">Özelliğindeki bilgilerin kullanılması, `Resource` Yetkilendirme ilkelerinizi belirli çerçeveler ile sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="3acc3-287">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="3acc3-288">`Resource`Anahtar sözcüğünü kullanarak özelliği atamalısınız `is` ve sonra, kodunuzun `InvalidCastException` diğer çerçeveler üzerinde çalıştırıldığında bir ile çökmemesini sağlamak için dönüştürmenin başarılı olduğunu doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="3acc3-288">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
