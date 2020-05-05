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
ms.openlocfilehash: 3b6fcef91355bf22e5aa185652d9489a44998db0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777507"
---
# <a name="policy-based-authorization-in-aspnet-core"></a><span data-ttu-id="2be94-103">ASP.NET Core ilke tabanlı yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="2be94-103">Policy-based authorization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2be94-104">Kapakların altında [rol tabanlı yetkilendirme](xref:security/authorization/roles) ve [talep tabanlı yetkilendirme](xref:security/authorization/claims) , bir gereksinim, gereksinim işleyicisi ve önceden yapılandırılmış bir ilke kullanır.</span><span class="sxs-lookup"><span data-stu-id="2be94-104">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="2be94-105">Bu yapı taşları, koddaki yetkilendirme değerlendirmeleri ifadesini destekler.</span><span class="sxs-lookup"><span data-stu-id="2be94-105">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="2be94-106">Sonuç daha zengin, yeniden kullanılabilir ve test edilebilir bir yetkilendirme yapısıdır.</span><span class="sxs-lookup"><span data-stu-id="2be94-106">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="2be94-107">Yetkilendirme ilkesi bir veya daha fazla gereksinimden oluşur.</span><span class="sxs-lookup"><span data-stu-id="2be94-107">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="2be94-108">Bu, yetkilendirme hizmeti yapılandırmasının bir parçası olarak kaydedilir, `Startup.ConfigureServices` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="2be94-108">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

<span data-ttu-id="2be94-109">Yukarıdaki örnekte, "AtLeast21" ilkesi oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="2be94-109">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="2be94-110">Bu, gereksinimle bir&mdash;parametre olarak sağlanan minimum Age 'in tek bir gereksinimine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="2be94-110">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="2be94-111">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="2be94-111">IAuthorizationService</span></span> 

<span data-ttu-id="2be94-112">Yetkilendirmenin başarılı olup olmadığını belirleyen birincil hizmet <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span><span class="sxs-lookup"><span data-stu-id="2be94-112">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="2be94-113">Yukarıdaki kod, [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)'in iki yöntemini vurgular.</span><span class="sxs-lookup"><span data-stu-id="2be94-113">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="2be94-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>, yöntemi olmayan bir işaret hizmetidir ve yetkilendirme işleminin başarılı olup olmadığını izlemeye yönelik mekanizmaya yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="2be94-114"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="2be94-115">Gereksinimlerin <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> karşılanıp karşılanmadığını denetlenmekten her biri sorumludur:</span><span class="sxs-lookup"><span data-stu-id="2be94-115">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="2be94-116">Bu <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> sınıf, işleyicinin gereksinimlerin karşılanıp karşılanmadığını işaretlemek için kullandığı şeydir:</span><span class="sxs-lookup"><span data-stu-id="2be94-116">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="2be94-117">Aşağıdaki kod, yetkilendirme hizmetinin Basitleştirilmiş (ve açıklamalar ile açıklamalı) varsayılan uygulamasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="2be94-117">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="2be94-118">Aşağıdaki kod tipik `ConfigureServices`bir göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="2be94-118">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="2be94-119">Yetkilendirme <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> için `[Authorize(Policy = "Something")]` veya kullanın.</span><span class="sxs-lookup"><span data-stu-id="2be94-119">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="2be94-120">MVC denetleyicilerine ilke uygulama</span><span class="sxs-lookup"><span data-stu-id="2be94-120">Applying policies to MVC controllers</span></span>

<span data-ttu-id="2be94-121">Sayfalar kullanıyorsanız Razor , bkz. bu belgedeki [ Razor sayfalara ilke uygulama](#applying-policies-to-razor-pages) .</span><span class="sxs-lookup"><span data-stu-id="2be94-121">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="2be94-122">İlkeler, ilke adı ile `[Authorize]` özniteliği kullanılarak denetleyicilere uygulanır.</span><span class="sxs-lookup"><span data-stu-id="2be94-122">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="2be94-123">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2be94-123">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="2be94-124">İlkeleri Razor sayfalara uygulama</span><span class="sxs-lookup"><span data-stu-id="2be94-124">Applying policies to Razor Pages</span></span>

<span data-ttu-id="2be94-125">İlkeler, ilke adı Razor ile `[Authorize]` özniteliği kullanılarak sayfalara uygulanır.</span><span class="sxs-lookup"><span data-stu-id="2be94-125">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="2be94-126">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2be94-126">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="2be94-127">İlkeler, bir [Yetkilendirme kuralı](xref:security/authorization/razor-pages-authorization)kullanılarak Razor sayfalara da uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="2be94-127">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="2be94-128">Gereksinimler</span><span class="sxs-lookup"><span data-stu-id="2be94-128">Requirements</span></span>

<span data-ttu-id="2be94-129">Yetkilendirme gereksinimi, bir ilkenin geçerli kullanıcı sorumlusunu değerlendirmek için kullanabileceği veri parametreleri koleksiyonudur.</span><span class="sxs-lookup"><span data-stu-id="2be94-129">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="2be94-130">"AtLeast21" ilkenizde, gereksinim en düşük yaş olan tek bir&mdash;parametredir.</span><span class="sxs-lookup"><span data-stu-id="2be94-130">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="2be94-131">Bir gereksinim, boş bir işaret arabirimi olan [ıauthorizationrequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)'ı uygular.</span><span class="sxs-lookup"><span data-stu-id="2be94-131">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="2be94-132">Parametreli en düşük yaş gereksinimi aşağıdaki gibi uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="2be94-132">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="2be94-133">Yetkilendirme ilkesi birden çok yetkilendirme gereksinimi içeriyorsa, ilke değerlendirmesinin başarılı olması için tüm gereksinimlerin geçmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="2be94-133">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="2be94-134">Diğer bir deyişle, tek bir yetkilendirme ilkesine eklenen birden çok yetkilendirme gereksinimi bir **ve** temelinde değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="2be94-134">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="2be94-135">Bir gereksinimin veri veya özellikleri olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="2be94-135">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="2be94-136">Yetkilendirme işleyicileri</span><span class="sxs-lookup"><span data-stu-id="2be94-136">Authorization handlers</span></span>

<span data-ttu-id="2be94-137">Bir yetkilendirme işleyicisi, bir gereksinimin özelliklerinin değerlendirilmesinden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="2be94-137">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="2be94-138">Yetkilendirme işleyicisi, erişim izni verilip verilmediğini belirlemede, gereksinimleri belirtilen [Authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 'e göre değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="2be94-138">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="2be94-139">Bir gereksinimin [birden çok işleyicisi](#security-authorization-policies-based-multiple-handlers)olabilir.</span><span class="sxs-lookup"><span data-stu-id="2be94-139">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="2be94-140">Bir işleyici, [authorizationhandler\<trequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)devralınabilir, burada `TRequirement` işlenme gereksinimidir.</span><span class="sxs-lookup"><span data-stu-id="2be94-140">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="2be94-141">Alternatif olarak, bir işleyici birden fazla gereksinim türünü işlemek için [ıauthorizationhandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="2be94-141">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="2be94-142">Bir gereksinim için bir işleyici kullanın</span><span class="sxs-lookup"><span data-stu-id="2be94-142">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="2be94-143">Aşağıda, en az bir yaş işleyicisinin tek bir gereksinimin kullanıldığı bire bir ilişkiye örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="2be94-143">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="2be94-144">Önceki kod, geçerli kullanıcı sorumlusunun bilinen ve güvenilir bir veren tarafından verilen bir Doğum talebi tarihi olup olmadığını belirler.</span><span class="sxs-lookup"><span data-stu-id="2be94-144">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="2be94-145">Talep eksik olduğunda yetkilendirme gerçekleşemez, bu durumda tamamlanan bir görev döndürülür.</span><span class="sxs-lookup"><span data-stu-id="2be94-145">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="2be94-146">Bir talep bulunduğunda, kullanıcının yaşı hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="2be94-146">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="2be94-147">Kullanıcı gereksinim tarafından tanımlanan en düşük yaşı karşılıyorsa, Yetkilendirme başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="2be94-147">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="2be94-148">Yetkilendirme başarılı olduğunda, `context.Succeed` tek parametresi olarak tatmin eden gereksinimle çağrılır.</span><span class="sxs-lookup"><span data-stu-id="2be94-148">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="2be94-149">Birden çok gereksinim için bir işleyici kullanın</span><span class="sxs-lookup"><span data-stu-id="2be94-149">Use a handler for multiple requirements</span></span>

<span data-ttu-id="2be94-150">Aşağıda, bir izin işleyicisinin üç farklı gereksinim türünü işleyebileceği bir çoktan çoğa ilişkiye örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="2be94-150">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="2be94-151">Yukarıdaki kod, [pendingrequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;öğesine geçer ve başarılı olarak işaretlenmemiş gereksinimleri içeren bir özelliktir.</span><span class="sxs-lookup"><span data-stu-id="2be94-151">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="2be94-152">Bir `ReadPermission` gereksinim için, kullanıcı istenen kaynağa erişmek için bir sahip veya sponsor olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2be94-152">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="2be94-153">`EditPermission` Veya `DeletePermission` gereksinimi söz konusu olduğunda, istenen kaynağa erişmek için sahip olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2be94-153">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="2be94-154">İşleyici kaydı</span><span class="sxs-lookup"><span data-stu-id="2be94-154">Handler registration</span></span>

<span data-ttu-id="2be94-155">İşleyiciler, yapılandırma sırasında hizmetler koleksiyonuna kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="2be94-155">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="2be94-156">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2be94-156">For example:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

<span data-ttu-id="2be94-157">Yukarıdaki kod, çağırarak `MinimumAgeHandler` `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`tek bir olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="2be94-157">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="2be94-158">İşleyiciler, yerleşik [hizmet yaşam sürelerinin](xref:fundamentals/dependency-injection#service-lifetimes)herhangi biri kullanılarak kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="2be94-158">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="2be94-159">İşleyici ne döndürmelidir?</span><span class="sxs-lookup"><span data-stu-id="2be94-159">What should a handler return?</span></span>

<span data-ttu-id="2be94-160">`Handle` [İşleyici örnekteki](#security-authorization-handler-example) yöntemin değer döndürmediğini unutmayın.</span><span class="sxs-lookup"><span data-stu-id="2be94-160">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="2be94-161">Başarı ya da hatanın durumu nasıl belirtilir?</span><span class="sxs-lookup"><span data-stu-id="2be94-161">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="2be94-162">Bir işleyici çağırarak `context.Succeed(IAuthorizationRequirement requirement)`başarılı bir şekilde doğrulanan gereksinimi geçirerek başarılı olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="2be94-162">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="2be94-163">Aynı gereksinim için diğer işleyiciler başarılı olabileceğinden, işleyicinin sorunları genellikle işlemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="2be94-163">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="2be94-164">Diğer gereksinim işleyicileri başarılı olsa bile hatayı güvence altına almak için çağrısı `context.Fail`yapın.</span><span class="sxs-lookup"><span data-stu-id="2be94-164">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="2be94-165">Bir işleyici veya `context.Fail`çağırırsa `context.Succeed` , diğer tüm işleyiciler hala çağırılır.</span><span class="sxs-lookup"><span data-stu-id="2be94-165">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="2be94-166">Bu, başka bir işleyicinin bir gereksinimi başarıyla doğrulayan veya başarısız olsa bile, gereksinimlerin günlüğe kaydetme gibi yan etkileri üretmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="2be94-166">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="2be94-167">Olarak ayarlandığında, `false` [ınvokehandlersafterfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) özelliği (ASP.NET Core 1,1 ve üzeri sürümlerde mevcuttur), çağrıldığında işleyicilerin `context.Fail` yürütülmesi için kısa devre dışı.</span><span class="sxs-lookup"><span data-stu-id="2be94-167">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="2be94-168">`InvokeHandlersAfterFailure`Varsayılan olarak `true`, bu durumda tüm işleyiciler çağrılır.</span><span class="sxs-lookup"><span data-stu-id="2be94-168">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="2be94-169">Yetkilendirme işleyicileri, kimlik doğrulama başarısız olsa bile çağrılır.</span><span class="sxs-lookup"><span data-stu-id="2be94-169">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="2be94-170">Bir gereksinim için neden birden çok işleyici istiyorum?</span><span class="sxs-lookup"><span data-stu-id="2be94-170">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="2be94-171">Değerlendirmenin bir **veya** temelinde olmasını istediğiniz durumlarda, tek bir gereksinim için birden çok işleyici uygulayın.</span><span class="sxs-lookup"><span data-stu-id="2be94-171">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="2be94-172">Örneğin, Microsoft yalnızca ana kartlarla açık olan kapılara sahiptir.</span><span class="sxs-lookup"><span data-stu-id="2be94-172">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="2be94-173">Ana kartınızı evden bırakırsanız, alıcı geçici bir etiket yazdırır ve kapıyı sizin için açar.</span><span class="sxs-lookup"><span data-stu-id="2be94-173">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="2be94-174">Bu senaryoda, tek bir gereksinimi incelerken tek bir gereksinimin, *Buildingentry*, ancak birden çok işleyici vardır.</span><span class="sxs-lookup"><span data-stu-id="2be94-174">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="2be94-175">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="2be94-175">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="2be94-176">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="2be94-176">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="2be94-177">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="2be94-177">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="2be94-178">Her iki işleyicinin de [kaydedildiğinden](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)emin olun.</span><span class="sxs-lookup"><span data-stu-id="2be94-178">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="2be94-179">Bir ilke değerlendirirken her iki işleyici `BuildingEntryRequirement`de başarılı olursa, ilke değerlendirmesi başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="2be94-179">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="2be94-180">İlkeyi yerine getirmek için bir Func kullanma</span><span class="sxs-lookup"><span data-stu-id="2be94-180">Using a func to fulfill a policy</span></span>

<span data-ttu-id="2be94-181">Kodun kodda hızlı bir şekilde kullanılması için bir ilkeyi karşıladığı durumlar olabilir.</span><span class="sxs-lookup"><span data-stu-id="2be94-181">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="2be94-182">İlke Oluşturucu ile `RequireAssertion` ilkenizi yapılandırırken bir `Func<AuthorizationHandlerContext, bool>` sağlamak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="2be94-182">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="2be94-183">Örneğin, önceki `BadgeEntryHandler` , aşağıdaki gibi yeniden yazılabilir:</span><span class="sxs-lookup"><span data-stu-id="2be94-183">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="2be94-184">İşleyicilerde MVC istek bağlamına erişme</span><span class="sxs-lookup"><span data-stu-id="2be94-184">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="2be94-185">Bir `HandleRequirementAsync` yetkilendirme işleyicisinde uyguladığınız yöntemin iki parametresi vardır: bir `AuthorizationHandlerContext` ve `TRequirement` işleme çalışıyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="2be94-185">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="2be94-186">MVC veya Jabbr gibi çerçeveler, `Resource` `AuthorizationHandlerContext` daha fazla bilgi geçirmek için üzerinde özelliğine herhangi bir nesne eklemek ücretsizdir.</span><span class="sxs-lookup"><span data-stu-id="2be94-186">Frameworks such as MVC or Jabbr are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="2be94-187">Örneğin, MVC, `Resource` özelliğinde [authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext) örneğini geçirir.</span><span class="sxs-lookup"><span data-stu-id="2be94-187">For example, MVC passes an instance of [AuthorizationFilterContext](/dotnet/api/?term=AuthorizationFilterContext) in the `Resource` property.</span></span> <span data-ttu-id="2be94-188">Bu özellik, `HttpContext` `RouteData`, ve için MVC ve Razor sayfalar tarafından sağlanan diğer her şeye erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="2be94-188">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="2be94-189">`Resource` Özelliğin kullanımı Framework 'e özgüdür.</span><span class="sxs-lookup"><span data-stu-id="2be94-189">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="2be94-190">Özelliğindeki bilgilerin kullanılması, `Resource` yetkilendirme ilkelerinizi belirli çerçeveler ile sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="2be94-190">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="2be94-191">Anahtar sözcüğünü kullanarak `Resource` özelliği atamalısınız ve sonra, kodunuzun diğer çerçeveler üzerinde çalıştırıldığında bir `InvalidCastException` ile çökmemesini sağlamak için dönüştürmenin başarılı olduğunu doğrulayın: `is`</span><span class="sxs-lookup"><span data-stu-id="2be94-191">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

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

<span data-ttu-id="2be94-192">Kapakların altında [rol tabanlı yetkilendirme](xref:security/authorization/roles) ve [talep tabanlı yetkilendirme](xref:security/authorization/claims) , bir gereksinim, gereksinim işleyicisi ve önceden yapılandırılmış bir ilke kullanır.</span><span class="sxs-lookup"><span data-stu-id="2be94-192">Underneath the covers, [role-based authorization](xref:security/authorization/roles) and [claims-based authorization](xref:security/authorization/claims) use a requirement, a requirement handler, and a pre-configured policy.</span></span> <span data-ttu-id="2be94-193">Bu yapı taşları, koddaki yetkilendirme değerlendirmeleri ifadesini destekler.</span><span class="sxs-lookup"><span data-stu-id="2be94-193">These building blocks support the expression of authorization evaluations in code.</span></span> <span data-ttu-id="2be94-194">Sonuç daha zengin, yeniden kullanılabilir ve test edilebilir bir yetkilendirme yapısıdır.</span><span class="sxs-lookup"><span data-stu-id="2be94-194">The result is a richer, reusable, testable authorization structure.</span></span>

<span data-ttu-id="2be94-195">Yetkilendirme ilkesi bir veya daha fazla gereksinimden oluşur.</span><span class="sxs-lookup"><span data-stu-id="2be94-195">An authorization policy consists of one or more requirements.</span></span> <span data-ttu-id="2be94-196">Bu, yetkilendirme hizmeti yapılandırmasının bir parçası olarak kaydedilir, `Startup.ConfigureServices` yöntemi:</span><span class="sxs-lookup"><span data-stu-id="2be94-196">It's registered as part of the authorization service configuration, in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

<span data-ttu-id="2be94-197">Yukarıdaki örnekte, "AtLeast21" ilkesi oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="2be94-197">In the preceding example, an "AtLeast21" policy is created.</span></span> <span data-ttu-id="2be94-198">Bu, gereksinimle bir&mdash;parametre olarak sağlanan minimum Age 'in tek bir gereksinimine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="2be94-198">It has a single requirement&mdash;that of a minimum age, which is supplied as a parameter to the requirement.</span></span>

## <a name="iauthorizationservice"></a><span data-ttu-id="2be94-199">IAuthorizationService</span><span class="sxs-lookup"><span data-stu-id="2be94-199">IAuthorizationService</span></span> 

<span data-ttu-id="2be94-200">Yetkilendirmenin başarılı olup olmadığını belirleyen birincil hizmet <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span><span class="sxs-lookup"><span data-stu-id="2be94-200">The primary service that determines if authorization is successful is <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>:</span></span>

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

<span data-ttu-id="2be94-201">Yukarıdaki kod, [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)'in iki yöntemini vurgular.</span><span class="sxs-lookup"><span data-stu-id="2be94-201">The preceding code highlights the two methods of the [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs).</span></span>

<span data-ttu-id="2be94-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>, yöntemi olmayan bir işaret hizmetidir ve yetkilendirme işleminin başarılı olup olmadığını izlemeye yönelik mekanizmaya yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="2be94-202"><xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> is a marker service with no methods, and the mechanism for tracking whether authorization is successful.</span></span>

<span data-ttu-id="2be94-203">Gereksinimlerin <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> karşılanıp karşılanmadığını denetlenmekten her biri sorumludur:</span><span class="sxs-lookup"><span data-stu-id="2be94-203">Each <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> is responsible for checking if requirements are met:</span></span>
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

<span data-ttu-id="2be94-204">Bu <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> sınıf, işleyicinin gereksinimlerin karşılanıp karşılanmadığını işaretlemek için kullandığı şeydir:</span><span class="sxs-lookup"><span data-stu-id="2be94-204">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> class is what the handler uses to mark whether requirements have been met:</span></span>

```csharp
 context.Succeed(requirement)
```

<span data-ttu-id="2be94-205">Aşağıdaki kod, yetkilendirme hizmetinin Basitleştirilmiş (ve açıklamalar ile açıklamalı) varsayılan uygulamasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="2be94-205">The following code shows the simplified (and annotated with comments) default implementation of the authorization service:</span></span>

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

<span data-ttu-id="2be94-206">Aşağıdaki kod tipik `ConfigureServices`bir göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="2be94-206">The following code shows a typical `ConfigureServices`:</span></span>

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

<span data-ttu-id="2be94-207">Yetkilendirme <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> için `[Authorize(Policy = "Something")]` veya kullanın.</span><span class="sxs-lookup"><span data-stu-id="2be94-207">Use <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> or `[Authorize(Policy = "Something")]` for authorization.</span></span>

## <a name="applying-policies-to-mvc-controllers"></a><span data-ttu-id="2be94-208">MVC denetleyicilerine ilke uygulama</span><span class="sxs-lookup"><span data-stu-id="2be94-208">Applying policies to MVC controllers</span></span>

<span data-ttu-id="2be94-209">Sayfalar kullanıyorsanız Razor , bkz. bu belgedeki [ Razor sayfalara ilke uygulama](#applying-policies-to-razor-pages) .</span><span class="sxs-lookup"><span data-stu-id="2be94-209">If you're using Razor Pages, see [Applying policies to Razor Pages](#applying-policies-to-razor-pages) in this document.</span></span>

<span data-ttu-id="2be94-210">İlkeler, ilke adı ile `[Authorize]` özniteliği kullanılarak denetleyicilere uygulanır.</span><span class="sxs-lookup"><span data-stu-id="2be94-210">Policies are applied to controllers by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="2be94-211">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2be94-211">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a><span data-ttu-id="2be94-212">İlkeleri Razor sayfalara uygulama</span><span class="sxs-lookup"><span data-stu-id="2be94-212">Applying policies to Razor Pages</span></span>

<span data-ttu-id="2be94-213">İlkeler, ilke adı Razor ile `[Authorize]` özniteliği kullanılarak sayfalara uygulanır.</span><span class="sxs-lookup"><span data-stu-id="2be94-213">Policies are applied to Razor Pages by using the `[Authorize]` attribute with the policy name.</span></span> <span data-ttu-id="2be94-214">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2be94-214">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

<span data-ttu-id="2be94-215">İlkeler, bir [Yetkilendirme kuralı](xref:security/authorization/razor-pages-authorization)kullanılarak Razor sayfalara da uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="2be94-215">Policies can also be applied to Razor Pages by using an [authorization convention](xref:security/authorization/razor-pages-authorization).</span></span>

## <a name="requirements"></a><span data-ttu-id="2be94-216">Gereksinimler</span><span class="sxs-lookup"><span data-stu-id="2be94-216">Requirements</span></span>

<span data-ttu-id="2be94-217">Yetkilendirme gereksinimi, bir ilkenin geçerli kullanıcı sorumlusunu değerlendirmek için kullanabileceği veri parametreleri koleksiyonudur.</span><span class="sxs-lookup"><span data-stu-id="2be94-217">An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal.</span></span> <span data-ttu-id="2be94-218">"AtLeast21" ilkenizde, gereksinim en düşük yaş olan tek bir&mdash;parametredir.</span><span class="sxs-lookup"><span data-stu-id="2be94-218">In our "AtLeast21" policy, the requirement is a single parameter&mdash;the minimum age.</span></span> <span data-ttu-id="2be94-219">Bir gereksinim, boş bir işaret arabirimi olan [ıauthorizationrequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)'ı uygular.</span><span class="sxs-lookup"><span data-stu-id="2be94-219">A requirement implements [IAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement), which is an empty marker interface.</span></span> <span data-ttu-id="2be94-220">Parametreli en düşük yaş gereksinimi aşağıdaki gibi uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="2be94-220">A parameterized minimum age requirement could be implemented as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

<span data-ttu-id="2be94-221">Yetkilendirme ilkesi birden çok yetkilendirme gereksinimi içeriyorsa, ilke değerlendirmesinin başarılı olması için tüm gereksinimlerin geçmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="2be94-221">If an authorization policy contains multiple authorization requirements, all requirements must pass in order for the policy evaluation to succeed.</span></span> <span data-ttu-id="2be94-222">Diğer bir deyişle, tek bir yetkilendirme ilkesine eklenen birden çok yetkilendirme gereksinimi bir **ve** temelinde değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="2be94-222">In other words, multiple authorization requirements added to a single authorization policy are treated on an **AND** basis.</span></span>

> [!NOTE]
> <span data-ttu-id="2be94-223">Bir gereksinimin veri veya özellikleri olması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="2be94-223">A requirement doesn't need to have data or properties.</span></span>

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a><span data-ttu-id="2be94-224">Yetkilendirme işleyicileri</span><span class="sxs-lookup"><span data-stu-id="2be94-224">Authorization handlers</span></span>

<span data-ttu-id="2be94-225">Bir yetkilendirme işleyicisi, bir gereksinimin özelliklerinin değerlendirilmesinden sorumludur.</span><span class="sxs-lookup"><span data-stu-id="2be94-225">An authorization handler is responsible for the evaluation of a requirement's properties.</span></span> <span data-ttu-id="2be94-226">Yetkilendirme işleyicisi, erişim izni verilip verilmediğini belirlemede, gereksinimleri belirtilen [Authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 'e göre değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="2be94-226">The authorization handler evaluates the requirements against a provided [AuthorizationHandlerContext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) to determine if access is allowed.</span></span>

<span data-ttu-id="2be94-227">Bir gereksinimin [birden çok işleyicisi](#security-authorization-policies-based-multiple-handlers)olabilir.</span><span class="sxs-lookup"><span data-stu-id="2be94-227">A requirement can have [multiple handlers](#security-authorization-policies-based-multiple-handlers).</span></span> <span data-ttu-id="2be94-228">Bir işleyici, [authorizationhandler\<trequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)devralınabilir, burada `TRequirement` işlenme gereksinimidir.</span><span class="sxs-lookup"><span data-stu-id="2be94-228">A handler may inherit [AuthorizationHandler\<TRequirement>](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1), where `TRequirement` is the requirement to be handled.</span></span> <span data-ttu-id="2be94-229">Alternatif olarak, bir işleyici birden fazla gereksinim türünü işlemek için [ıauthorizationhandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="2be94-229">Alternatively, a handler may implement [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) to handle more than one type of requirement.</span></span>

### <a name="use-a-handler-for-one-requirement"></a><span data-ttu-id="2be94-230">Bir gereksinim için bir işleyici kullanın</span><span class="sxs-lookup"><span data-stu-id="2be94-230">Use a handler for one requirement</span></span>

<a name="security-authorization-handler-example"></a>

<span data-ttu-id="2be94-231">Aşağıda, en az bir yaş işleyicisinin tek bir gereksinimin kullanıldığı bire bir ilişkiye örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="2be94-231">The following is an example of a one-to-one relationship in which a minimum age handler utilizes a single requirement:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

<span data-ttu-id="2be94-232">Önceki kod, geçerli kullanıcı sorumlusunun bilinen ve güvenilir bir veren tarafından verilen bir Doğum talebi tarihi olup olmadığını belirler.</span><span class="sxs-lookup"><span data-stu-id="2be94-232">The preceding code determines if the current user principal has a date of birth claim which has been issued by a known and trusted Issuer.</span></span> <span data-ttu-id="2be94-233">Talep eksik olduğunda yetkilendirme gerçekleşemez, bu durumda tamamlanan bir görev döndürülür.</span><span class="sxs-lookup"><span data-stu-id="2be94-233">Authorization can't occur when the claim is missing, in which case a completed task is returned.</span></span> <span data-ttu-id="2be94-234">Bir talep bulunduğunda, kullanıcının yaşı hesaplanır.</span><span class="sxs-lookup"><span data-stu-id="2be94-234">When a claim is present, the user's age is calculated.</span></span> <span data-ttu-id="2be94-235">Kullanıcı gereksinim tarafından tanımlanan en düşük yaşı karşılıyorsa, Yetkilendirme başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="2be94-235">If the user meets the minimum age defined by the requirement, authorization is deemed successful.</span></span> <span data-ttu-id="2be94-236">Yetkilendirme başarılı olduğunda, `context.Succeed` tek parametresi olarak tatmin eden gereksinimle çağrılır.</span><span class="sxs-lookup"><span data-stu-id="2be94-236">When authorization is successful, `context.Succeed` is invoked with the satisfied requirement as its sole parameter.</span></span>

### <a name="use-a-handler-for-multiple-requirements"></a><span data-ttu-id="2be94-237">Birden çok gereksinim için bir işleyici kullanın</span><span class="sxs-lookup"><span data-stu-id="2be94-237">Use a handler for multiple requirements</span></span>

<span data-ttu-id="2be94-238">Aşağıda, bir izin işleyicisinin üç farklı gereksinim türünü işleyebileceği bir çoktan çoğa ilişkiye örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="2be94-238">The following is an example of a one-to-many relationship in which a permission handler can handle three different types of requirements:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

<span data-ttu-id="2be94-239">Yukarıdaki kod, [pendingrequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;öğesine geçer ve başarılı olarak işaretlenmemiş gereksinimleri içeren bir özelliktir.</span><span class="sxs-lookup"><span data-stu-id="2be94-239">The preceding code traverses [PendingRequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;a property containing requirements not marked as successful.</span></span> <span data-ttu-id="2be94-240">Bir `ReadPermission` gereksinim için, kullanıcı istenen kaynağa erişmek için bir sahip veya sponsor olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2be94-240">For a `ReadPermission` requirement, the user must be either an owner or a sponsor to access the requested resource.</span></span> <span data-ttu-id="2be94-241">`EditPermission` Veya `DeletePermission` gereksinimi söz konusu olduğunda, istenen kaynağa erişmek için sahip olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2be94-241">In the case of an `EditPermission` or `DeletePermission` requirement, he or she must be an owner to access the requested resource.</span></span>

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a><span data-ttu-id="2be94-242">İşleyici kaydı</span><span class="sxs-lookup"><span data-stu-id="2be94-242">Handler registration</span></span>

<span data-ttu-id="2be94-243">İşleyiciler, yapılandırma sırasında hizmetler koleksiyonuna kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="2be94-243">Handlers are registered in the services collection during configuration.</span></span> <span data-ttu-id="2be94-244">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2be94-244">For example:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

<span data-ttu-id="2be94-245">Yukarıdaki kod, çağırarak `MinimumAgeHandler` `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`tek bir olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="2be94-245">The preceding code registers `MinimumAgeHandler` as a singleton by invoking `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`.</span></span> <span data-ttu-id="2be94-246">İşleyiciler, yerleşik [hizmet yaşam sürelerinin](xref:fundamentals/dependency-injection#service-lifetimes)herhangi biri kullanılarak kaydedilebilir.</span><span class="sxs-lookup"><span data-stu-id="2be94-246">Handlers can be registered using any of the built-in [service lifetimes](xref:fundamentals/dependency-injection#service-lifetimes).</span></span>

## <a name="what-should-a-handler-return"></a><span data-ttu-id="2be94-247">İşleyici ne döndürmelidir?</span><span class="sxs-lookup"><span data-stu-id="2be94-247">What should a handler return?</span></span>

<span data-ttu-id="2be94-248">`Handle` [İşleyici örnekteki](#security-authorization-handler-example) yöntemin değer döndürmediğini unutmayın.</span><span class="sxs-lookup"><span data-stu-id="2be94-248">Note that the `Handle` method in the [handler example](#security-authorization-handler-example) returns no value.</span></span> <span data-ttu-id="2be94-249">Başarı ya da hatanın durumu nasıl belirtilir?</span><span class="sxs-lookup"><span data-stu-id="2be94-249">How is a status of either success or failure indicated?</span></span>

* <span data-ttu-id="2be94-250">Bir işleyici çağırarak `context.Succeed(IAuthorizationRequirement requirement)`başarılı bir şekilde doğrulanan gereksinimi geçirerek başarılı olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="2be94-250">A handler indicates success by calling `context.Succeed(IAuthorizationRequirement requirement)`, passing the requirement that has been successfully validated.</span></span>

* <span data-ttu-id="2be94-251">Aynı gereksinim için diğer işleyiciler başarılı olabileceğinden, işleyicinin sorunları genellikle işlemesi gerekmez.</span><span class="sxs-lookup"><span data-stu-id="2be94-251">A handler doesn't need to handle failures generally, as other handlers for the same requirement may succeed.</span></span>

* <span data-ttu-id="2be94-252">Diğer gereksinim işleyicileri başarılı olsa bile hatayı güvence altına almak için çağrısı `context.Fail`yapın.</span><span class="sxs-lookup"><span data-stu-id="2be94-252">To guarantee failure, even if other requirement handlers succeed, call `context.Fail`.</span></span>

<span data-ttu-id="2be94-253">Bir işleyici veya `context.Fail`çağırırsa `context.Succeed` , diğer tüm işleyiciler hala çağırılır.</span><span class="sxs-lookup"><span data-stu-id="2be94-253">If a handler calls `context.Succeed` or `context.Fail`, all other handlers are still called.</span></span> <span data-ttu-id="2be94-254">Bu, başka bir işleyicinin bir gereksinimi başarıyla doğrulayan veya başarısız olsa bile, gereksinimlerin günlüğe kaydetme gibi yan etkileri üretmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="2be94-254">This allows requirements to produce side effects, such as logging, which takes place even if another handler has successfully validated or failed a requirement.</span></span> <span data-ttu-id="2be94-255">Olarak ayarlandığında, `false` [ınvokehandlersafterfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) özelliği (ASP.NET Core 1,1 ve üzeri sürümlerde mevcuttur), çağrıldığında işleyicilerin `context.Fail` yürütülmesi için kısa devre dışı.</span><span class="sxs-lookup"><span data-stu-id="2be94-255">When set to `false`, the [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) property (available in ASP.NET Core 1.1 and later) short-circuits the execution of handlers when `context.Fail` is called.</span></span> <span data-ttu-id="2be94-256">`InvokeHandlersAfterFailure`Varsayılan olarak `true`, bu durumda tüm işleyiciler çağrılır.</span><span class="sxs-lookup"><span data-stu-id="2be94-256">`InvokeHandlersAfterFailure` defaults to `true`, in which case all handlers are called.</span></span>

> [!NOTE]
> <span data-ttu-id="2be94-257">Yetkilendirme işleyicileri, kimlik doğrulama başarısız olsa bile çağrılır.</span><span class="sxs-lookup"><span data-stu-id="2be94-257">Authorization handlers are called even if authentication fails.</span></span>

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a><span data-ttu-id="2be94-258">Bir gereksinim için neden birden çok işleyici istiyorum?</span><span class="sxs-lookup"><span data-stu-id="2be94-258">Why would I want multiple handlers for a requirement?</span></span>

<span data-ttu-id="2be94-259">Değerlendirmenin bir **veya** temelinde olmasını istediğiniz durumlarda, tek bir gereksinim için birden çok işleyici uygulayın.</span><span class="sxs-lookup"><span data-stu-id="2be94-259">In cases where you want evaluation to be on an **OR** basis, implement multiple handlers for a single requirement.</span></span> <span data-ttu-id="2be94-260">Örneğin, Microsoft yalnızca ana kartlarla açık olan kapılara sahiptir.</span><span class="sxs-lookup"><span data-stu-id="2be94-260">For example, Microsoft has doors which only open with key cards.</span></span> <span data-ttu-id="2be94-261">Ana kartınızı evden bırakırsanız, alıcı geçici bir etiket yazdırır ve kapıyı sizin için açar.</span><span class="sxs-lookup"><span data-stu-id="2be94-261">If you leave your key card at home, the receptionist prints a temporary sticker and opens the door for you.</span></span> <span data-ttu-id="2be94-262">Bu senaryoda, tek bir gereksinimi incelerken tek bir gereksinimin, *Buildingentry*, ancak birden çok işleyici vardır.</span><span class="sxs-lookup"><span data-stu-id="2be94-262">In this scenario, you'd have a single requirement, *BuildingEntry*, but multiple handlers, each one examining a single requirement.</span></span>

<span data-ttu-id="2be94-263">*BuildingEntryRequirement.cs*</span><span class="sxs-lookup"><span data-stu-id="2be94-263">*BuildingEntryRequirement.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

<span data-ttu-id="2be94-264">*BadgeEntryHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="2be94-264">*BadgeEntryHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

<span data-ttu-id="2be94-265">*TemporaryStickerHandler.cs*</span><span class="sxs-lookup"><span data-stu-id="2be94-265">*TemporaryStickerHandler.cs*</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

<span data-ttu-id="2be94-266">Her iki işleyicinin de [kaydedildiğinden](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)emin olun.</span><span class="sxs-lookup"><span data-stu-id="2be94-266">Ensure that both handlers are [registered](xref:security/authorization/policies#security-authorization-policies-based-handler-registration).</span></span> <span data-ttu-id="2be94-267">Bir ilke değerlendirirken her iki işleyici `BuildingEntryRequirement`de başarılı olursa, ilke değerlendirmesi başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="2be94-267">If either handler succeeds when a policy evaluates the `BuildingEntryRequirement`, the policy evaluation succeeds.</span></span>

## <a name="using-a-func-to-fulfill-a-policy"></a><span data-ttu-id="2be94-268">İlkeyi yerine getirmek için bir Func kullanma</span><span class="sxs-lookup"><span data-stu-id="2be94-268">Using a func to fulfill a policy</span></span>

<span data-ttu-id="2be94-269">Kodun kodda hızlı bir şekilde kullanılması için bir ilkeyi karşıladığı durumlar olabilir.</span><span class="sxs-lookup"><span data-stu-id="2be94-269">There may be situations in which fulfilling a policy is simple to express in code.</span></span> <span data-ttu-id="2be94-270">İlke Oluşturucu ile `RequireAssertion` ilkenizi yapılandırırken bir `Func<AuthorizationHandlerContext, bool>` sağlamak mümkündür.</span><span class="sxs-lookup"><span data-stu-id="2be94-270">It's possible to supply a `Func<AuthorizationHandlerContext, bool>` when configuring your policy with the `RequireAssertion` policy builder.</span></span>

<span data-ttu-id="2be94-271">Örneğin, önceki `BadgeEntryHandler` , aşağıdaki gibi yeniden yazılabilir:</span><span class="sxs-lookup"><span data-stu-id="2be94-271">For example, the previous `BadgeEntryHandler` could be rewritten as follows:</span></span>

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a><span data-ttu-id="2be94-272">İşleyicilerde MVC istek bağlamına erişme</span><span class="sxs-lookup"><span data-stu-id="2be94-272">Accessing MVC request context in handlers</span></span>

<span data-ttu-id="2be94-273">Bir `HandleRequirementAsync` yetkilendirme işleyicisinde uyguladığınız yöntemin iki parametresi vardır: bir `AuthorizationHandlerContext` ve `TRequirement` işleme çalışıyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="2be94-273">The `HandleRequirementAsync` method you implement in an authorization handler has two parameters: an `AuthorizationHandlerContext` and the `TRequirement` you are handling.</span></span> <span data-ttu-id="2be94-274">Daha fazla bilgi geçirmek için SignalR , `Resource` `AuthorizationHandlerContext` MVC gibi çerçeveler veya üzerinde özelliğine herhangi bir nesne eklemek ücretsizdir.</span><span class="sxs-lookup"><span data-stu-id="2be94-274">Frameworks such as MVC or SignalR are free to add any object to the `Resource` property on the `AuthorizationHandlerContext` to pass extra information.</span></span>

<span data-ttu-id="2be94-275">Endpoint Routing kullanılırken, yetkilendirme genellikle yetkilendirme ara yazılımı tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="2be94-275">When using endpoint routing, authorization is typically handled by the Authorization Middleware.</span></span> <span data-ttu-id="2be94-276">Bu durumda, `Resource` özelliği öğesinin <xref:Microsoft.AspNetCore.Http.Endpoint>bir örneğidir.</span><span class="sxs-lookup"><span data-stu-id="2be94-276">In this case, the `Resource` property is an instance of <xref:Microsoft.AspNetCore.Http.Endpoint>.</span></span> <span data-ttu-id="2be94-277">Uç noktası, yönlendirolduğunuz kaynak temelinde araştırma yapmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2be94-277">The endpoint can be used to probe the underlying the resource to which you're routing.</span></span> <span data-ttu-id="2be94-278">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="2be94-278">For example:</span></span>

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

<span data-ttu-id="2be94-279">Geleneksel yönlendirme ile veya MVC 'nin yetkilendirme filtresinin bir parçası olarak yetkilendirme gerçekleştiğinde, değeri bir `Resource` <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> örnek olur.</span><span class="sxs-lookup"><span data-stu-id="2be94-279">With traditional routing, or when authorization happens as part of MVC's authorization filter, the value of `Resource` is an <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> instance.</span></span> <span data-ttu-id="2be94-280">Bu özellik, `HttpContext` `RouteData`, ve için MVC ve Razor sayfalar tarafından sağlanan diğer her şeye erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="2be94-280">This property provides access to `HttpContext`, `RouteData`, and everything else provided by MVC and Razor Pages.</span></span>

<span data-ttu-id="2be94-281">`Resource` Özelliğin kullanımı Framework 'e özgüdür.</span><span class="sxs-lookup"><span data-stu-id="2be94-281">The use of the `Resource` property is framework specific.</span></span> <span data-ttu-id="2be94-282">Özelliğindeki bilgilerin kullanılması, `Resource` yetkilendirme ilkelerinizi belirli çerçeveler ile sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="2be94-282">Using information in the `Resource` property limits your authorization policies to particular frameworks.</span></span> <span data-ttu-id="2be94-283">Anahtar sözcüğünü kullanarak `Resource` özelliği atamalısınız ve sonra, kodunuzun diğer çerçeveler üzerinde çalıştırıldığında bir `InvalidCastException` ile çökmemesini sağlamak için dönüştürmenin başarılı olduğunu doğrulayın: `is`</span><span class="sxs-lookup"><span data-stu-id="2be94-283">You should cast the `Resource` property using the `is` keyword, and then confirm the cast has succeeded to ensure your code doesn't crash with an `InvalidCastException` when run on other frameworks:</span></span>

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
