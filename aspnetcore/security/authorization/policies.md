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
ms.openlocfilehash: 82ed4cc2ce47d3bd85ca9c2ba2bbeb075eaefcef
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635339"
---
# <a name="policy-based-authorization-in-aspnet-core"></a>ASP.NET Core ilke tabanlı yetkilendirme

::: moniker range=">= aspnetcore-3.0"

Kapakların altında [rol tabanlı yetkilendirme](xref:security/authorization/roles) ve [talep tabanlı yetkilendirme](xref:security/authorization/claims) , bir gereksinim, gereksinim işleyicisi ve önceden yapılandırılmış bir ilke kullanır. Bu yapı taşları, koddaki yetkilendirme değerlendirmeleri ifadesini destekler. Sonuç daha zengin, yeniden kullanılabilir ve test edilebilir bir yetkilendirme yapısıdır.

Yetkilendirme ilkesi bir veya daha fazla gereksinimden oluşur. Bu, yetkilendirme hizmeti yapılandırmasının bir parçası olarak kaydedilir, `Startup.ConfigureServices` yöntemi:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

Yukarıdaki örnekte, "AtLeast21" ilkesi oluşturulur. Bu, &mdash; gereksinimle bir parametre olarak sağlanan minimum Age 'in tek bir gereksinimine sahiptir.

## <a name="iauthorizationservice"></a>IAuthorizationService 

Yetkilendirmenin başarılı olup olmadığını belirleyen birincil hizmet <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

Yukarıdaki kod, [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)'in iki yöntemini vurgular.

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> , yöntemi olmayan bir işaret hizmetidir ve yetkilendirme işleminin başarılı olup olmadığını izlemeye yönelik mekanizmaya yöneliktir.

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>Gereksinimlerin karşılanıp karşılanmadığını denetlenmekten her biri sorumludur:
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

Bu <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> sınıf, işleyicinin gereksinimlerin karşılanıp karşılanmadığını işaretlemek için kullandığı şeydir:

```csharp
 context.Succeed(requirement)
```

Aşağıdaki kod, yetkilendirme hizmetinin Basitleştirilmiş (ve açıklamalar ile açıklamalı) varsayılan uygulamasını gösterir:

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

Aşağıdaki kod tipik bir göstermektedir `ConfigureServices` :

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

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` Yetkilendirme için veya kullanın.

## <a name="apply-policies-to-mvc-controllers"></a>MVC denetleyicilerine ilke uygulama

RazorSayfalar kullanıyorsanız, bkz. bu belgedeki [ Razor sayfalara ilke uygulama](#apply-policies-to-razor-pages) .

İlkeler, `[Authorize]` ilke adı ile özniteliği kullanılarak denetleyicilere uygulanır. Örnek:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a>İlkeleri sayfalara uygula Razor

İlkeler, Razor `[Authorize]` ilke adı ile özniteliği kullanılarak sayfalara uygulanır. Örnek:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

İlkeler ***not*** Razor sayfa işleyici düzeyinde uygulanamazlar, sayfaya uygulanmaları gerekir.

İlkeler, Razor bir [Yetkilendirme kuralı](xref:security/authorization/razor-pages-authorization)kullanılarak sayfalara uygulanabilir.

## <a name="requirements"></a>Gereksinimler

Yetkilendirme gereksinimi, bir ilkenin geçerli kullanıcı sorumlusunu değerlendirmek için kullanabileceği veri parametreleri koleksiyonudur. "AtLeast21" ilkenizde, gereksinim en düşük yaş olan tek bir parametredir &mdash; . Bir gereksinim, boş bir işaret arabirimi olan [ıauthorizationrequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)'ı uygular. Parametreli en düşük yaş gereksinimi aşağıdaki gibi uygulanabilir:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Yetkilendirme ilkesi birden çok yetkilendirme gereksinimi içeriyorsa, ilke değerlendirmesinin başarılı olması için tüm gereksinimlerin geçmesi gerekir. Diğer bir deyişle, tek bir yetkilendirme ilkesine eklenen birden çok yetkilendirme gereksinimi bir **ve** temelinde değerlendirilir.

> [!NOTE]
> Bir gereksinimin veri veya özellikleri olması gerekmez.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Yetkilendirme işleyicileri

Bir yetkilendirme işleyicisi, bir gereksinimin özelliklerinin değerlendirilmesinden sorumludur. Yetkilendirme işleyicisi, erişim izni verilip verilmediğini belirlemede, gereksinimleri belirtilen [Authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 'e göre değerlendirir.

Bir gereksinimin [birden çok işleyicisi](#security-authorization-policies-based-multiple-handlers)olabilir. Bir işleyici, [Authorizationhandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)' i devralınabilir, burada `TRequirement` işlenme gereksinimidir. Alternatif olarak, bir işleyici birden fazla gereksinim türünü işlemek için [ıauthorizationhandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) uygulayabilir.

### <a name="use-a-handler-for-one-requirement"></a>Bir gereksinim için bir işleyici kullanın

<a name="security-authorization-handler-example"></a>

Aşağıda, en az bir yaş işleyicisinin tek bir gereksinimin kullanıldığı bire bir ilişkiye örnek verilmiştir:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Önceki kod, geçerli kullanıcı sorumlusunun bilinen ve güvenilir bir veren tarafından verilen bir Doğum talebi tarihi olup olmadığını belirler. Talep eksik olduğunda yetkilendirme gerçekleşemez, bu durumda tamamlanan bir görev döndürülür. Bir talep bulunduğunda, kullanıcının yaşı hesaplanır. Kullanıcı gereksinim tarafından tanımlanan en düşük yaşı karşılıyorsa, Yetkilendirme başarılı olur. Yetkilendirme başarılı olduğunda, `context.Succeed` tek parametresi olarak tatmin eden gereksinimle çağrılır.

### <a name="use-a-handler-for-multiple-requirements"></a>Birden çok gereksinim için bir işleyici kullanın

Aşağıda, bir izin işleyicisinin üç farklı gereksinim türünü işleyebileceği bir çoktan çoğa ilişkiye örnek verilmiştir:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Yukarıdaki kod, [Pendingrequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)öğesine geçer ve &mdash; başarılı olarak işaretlenmemiş gereksinimleri içeren bir özelliktir. Bir `ReadPermission` gereksinim için, kullanıcı istenen kaynağa erişmek için bir sahip veya sponsor olmalıdır. Veya gereksinimi söz konusu olduğunda `EditPermission` `DeletePermission` , istenen kaynağa erişmek için sahip olması gerekir.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>İşleyici kaydı

İşleyiciler, yapılandırma sırasında hizmetler koleksiyonuna kaydedilir. Örnek:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

Yukarıdaki kod, `MinimumAgeHandler` çağırarak tek bir olarak kaydedilir `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` . İşleyiciler, yerleşik [hizmet yaşam sürelerinin](xref:fundamentals/dependency-injection#service-lifetimes)herhangi biri kullanılarak kaydedilebilir.

## <a name="what-should-a-handler-return"></a>İşleyici ne döndürmelidir?

`Handle` [İşleyici örnekteki](#security-authorization-handler-example) yöntemin değer döndürmediğini unutmayın. Başarı ya da hatanın durumu nasıl belirtilir?

* Bir işleyici çağırarak başarılı bir `context.Succeed(IAuthorizationRequirement requirement)` şekilde doğrulanan gereksinimi geçirerek başarılı olduğunu gösterir.

* Aynı gereksinim için diğer işleyiciler başarılı olabileceğinden, işleyicinin sorunları genellikle işlemesi gerekmez.

* Diğer gereksinim işleyicileri başarılı olsa bile hatayı güvence altına almak için çağrısı yapın `context.Fail` .

Bir işleyici veya çağırırsa `context.Succeed` `context.Fail` , diğer tüm işleyiciler hala çağırılır. Bu, başka bir işleyicinin bir gereksinimi başarıyla doğrulayan veya başarısız olsa bile, gereksinimlerin günlüğe kaydetme gibi yan etkileri üretmesine olanak tanır. Olarak ayarlandığında `false` , [ınvokehandlersafterfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) özelliği (ASP.NET Core 1,1 ve üzeri sürümlerde mevcuttur), çağrıldığında işleyicilerin yürütülmesi için kısa devre dışı `context.Fail` . `InvokeHandlersAfterFailure` Varsayılan olarak `true` , bu durumda tüm işleyiciler çağrılır.

> [!NOTE]
> Yetkilendirme işleyicileri, kimlik doğrulama başarısız olsa bile çağrılır.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Bir gereksinim için neden birden çok işleyici istiyorum?

Değerlendirmenin bir **veya** temelinde olmasını istediğiniz durumlarda, tek bir gereksinim için birden çok işleyici uygulayın. Örneğin, Microsoft yalnızca ana kartlarla açık olan kapılara sahiptir. Ana kartınızı evden bırakırsanız, alıcı geçici bir etiket yazdırır ve kapıyı sizin için açar. Bu senaryoda, tek bir gereksinimi incelerken tek bir gereksinimin, *Buildingentry*, ancak birden çok işleyici vardır.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Her iki işleyicinin de [kaydedildiğinden](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)emin olun. Bir ilke değerlendirirken her iki işleyici de başarılı olursa `BuildingEntryRequirement` , ilke değerlendirmesi başarılı olur.

## <a name="use-a-func-to-fulfill-a-policy"></a>Bir ilkeyi yerine getirmek için bir Func kullanın

Kodun kodda hızlı bir şekilde kullanılması için bir ilkeyi karşıladığı durumlar olabilir. İlke `Func<AuthorizationHandlerContext, bool>` Oluşturucu ile ilkenizi yapılandırırken bir sağlamak mümkündür `RequireAssertion` .

Örneğin, önceki, `BadgeEntryHandler` aşağıdaki gibi yeniden yazılabilir:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="access-mvc-request-context-in-handlers"></a>İşleyicilerde MVC istek bağlamına erişme

`HandleRequirementAsync`Bir yetkilendirme işleyicisinde uyguladığınız yöntemin iki parametresi vardır: bir `AuthorizationHandlerContext` ve `TRequirement` işleme çalışıyorsunuz. Daha SignalR `Resource` `AuthorizationHandlerContext` fazla bilgi GEÇIRMEK için, MVC gibi çerçeveler veya üzerinde özelliğine herhangi bir nesne eklemek ücretsizdir.

Endpoint Routing kullanılırken, yetkilendirme genellikle yetkilendirme ara yazılımı tarafından işlenir. Bu durumda, `Resource` özelliği öğesinin bir örneğidir <xref:Microsoft.AspNetCore.Http.Endpoint> . Uç noktası, yönlendirolduğunuz temel kaynağı yoklayabilmeniz için kullanılabilir. Örnek:

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

Uç nokta geçerli bir erişim sağlamaz `HttpContext` . Endpoint Routing kullanılırken, `IHttpContextAcessor` `HttpContext` bir yetkilendirme işleyicisinin içine erişmek için kullanın. Daha fazla bilgi için bkz. [özel bileşenlerden HttpContext kullanma](xref:fundamentals/httpcontext#use-httpcontext-from-custom-components).

Geleneksel yönlendirme ile veya MVC 'nin yetkilendirme filtresinin bir parçası olarak yetkilendirme gerçekleştiğinde, değeri `Resource` bir <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> örnek olur. Bu özellik `HttpContext` ,, ve IÇIN `RouteData` MVC ve sayfalar tarafından sağlanan diğer her şeye erişim sağlar Razor .

Özelliğin kullanımı Framework 'e `Resource` özgüdür. Özelliğindeki bilgilerin kullanılması, `Resource` Yetkilendirme ilkelerinizi belirli çerçeveler ile sınırlandırır. `Resource`Anahtar sözcüğünü kullanarak özelliği atamalısınız `is` ve sonra, kodunuzun `InvalidCastException` diğer çerçeveler üzerinde çalıştırıldığında bir ile çökmemesini sağlamak için dönüştürmenin başarılı olduğunu doğrulayın:

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

## <a name="globally-require-all-users-to-be-authenticated"></a>Genel olarak tüm kullanıcıların kimliğinin doğrulanmasını gerektir

[!INCLUDE[](~/includes/requireAuth.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Kapakların altında [rol tabanlı yetkilendirme](xref:security/authorization/roles) ve [talep tabanlı yetkilendirme](xref:security/authorization/claims) , bir gereksinim, gereksinim işleyicisi ve önceden yapılandırılmış bir ilke kullanır. Bu yapı taşları, koddaki yetkilendirme değerlendirmeleri ifadesini destekler. Sonuç daha zengin, yeniden kullanılabilir ve test edilebilir bir yetkilendirme yapısıdır.

Yetkilendirme ilkesi bir veya daha fazla gereksinimden oluşur. Bu, yetkilendirme hizmeti yapılandırmasının bir parçası olarak kaydedilir, `Startup.ConfigureServices` yöntemi:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

Yukarıdaki örnekte, "AtLeast21" ilkesi oluşturulur. Bu, &mdash; gereksinimle bir parametre olarak sağlanan minimum Age 'in tek bir gereksinimine sahiptir.

## <a name="iauthorizationservice"></a>IAuthorizationService 

Yetkilendirmenin başarılı olup olmadığını belirleyen birincil hizmet <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> :

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

Yukarıdaki kod, [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)'in iki yöntemini vurgular.

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement> , yöntemi olmayan bir işaret hizmetidir ve yetkilendirme işleminin başarılı olup olmadığını izlemeye yönelik mekanizmaya yöneliktir.

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler>Gereksinimlerin karşılanıp karşılanmadığını denetlenmekten her biri sorumludur:
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

Bu <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> sınıf, işleyicinin gereksinimlerin karşılanıp karşılanmadığını işaretlemek için kullandığı şeydir:

```csharp
 context.Succeed(requirement)
```

Aşağıdaki kod, yetkilendirme hizmetinin Basitleştirilmiş (ve açıklamalar ile açıklamalı) varsayılan uygulamasını gösterir:

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

Aşağıdaki kod tipik bir göstermektedir `ConfigureServices` :

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

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` Yetkilendirme için veya kullanın.

## <a name="apply-policies-to-mvc-controllers"></a>MVC denetleyicilerine ilke uygulama

RazorSayfalar kullanıyorsanız, bkz. bu belgedeki [ Razor sayfalara ilke uygulama](#apply-policies-to-razor-pages) .

İlkeler, `[Authorize]` ilke adı ile özniteliği kullanılarak denetleyicilere uygulanır. Örnek:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="apply-policies-to-no-locrazor-pages"></a>İlkeleri sayfalara uygula Razor

İlkeler, Razor `[Authorize]` ilke adı ile özniteliği kullanılarak sayfalara uygulanır. Örnek:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

İlkeler, Razor bir [Yetkilendirme kuralı](xref:security/authorization/razor-pages-authorization)kullanılarak sayfalara da uygulanabilir.

## <a name="requirements"></a>Gereksinimler

Yetkilendirme gereksinimi, bir ilkenin geçerli kullanıcı sorumlusunu değerlendirmek için kullanabileceği veri parametreleri koleksiyonudur. "AtLeast21" ilkenizde, gereksinim en düşük yaş olan tek bir parametredir &mdash; . Bir gereksinim, boş bir işaret arabirimi olan [ıauthorizationrequirement](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)'ı uygular. Parametreli en düşük yaş gereksinimi aşağıdaki gibi uygulanabilir:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Yetkilendirme ilkesi birden çok yetkilendirme gereksinimi içeriyorsa, ilke değerlendirmesinin başarılı olması için tüm gereksinimlerin geçmesi gerekir. Diğer bir deyişle, tek bir yetkilendirme ilkesine eklenen birden çok yetkilendirme gereksinimi bir **ve** temelinde değerlendirilir.

> [!NOTE]
> Bir gereksinimin veri veya özellikleri olması gerekmez.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Yetkilendirme işleyicileri

Bir yetkilendirme işleyicisi, bir gereksinimin özelliklerinin değerlendirilmesinden sorumludur. Yetkilendirme işleyicisi, erişim izni verilip verilmediğini belirlemede, gereksinimleri belirtilen [Authorizationhandlercontext](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) 'e göre değerlendirir.

Bir gereksinimin [birden çok işleyicisi](#security-authorization-policies-based-multiple-handlers)olabilir. Bir işleyici, [Authorizationhandler \<TRequirement> ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)' i devralınabilir, burada `TRequirement` işlenme gereksinimidir. Alternatif olarak, bir işleyici birden fazla gereksinim türünü işlemek için [ıauthorizationhandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) uygulayabilir.

### <a name="use-a-handler-for-one-requirement"></a>Bir gereksinim için bir işleyici kullanın

<a name="security-authorization-handler-example"></a>

Aşağıda, en az bir yaş işleyicisinin tek bir gereksinimin kullanıldığı bire bir ilişkiye örnek verilmiştir:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Önceki kod, geçerli kullanıcı sorumlusunun bilinen ve güvenilir bir veren tarafından verilen bir Doğum talebi tarihi olup olmadığını belirler. Talep eksik olduğunda yetkilendirme gerçekleşemez, bu durumda tamamlanan bir görev döndürülür. Bir talep bulunduğunda, kullanıcının yaşı hesaplanır. Kullanıcı gereksinim tarafından tanımlanan en düşük yaşı karşılıyorsa, Yetkilendirme başarılı olur. Yetkilendirme başarılı olduğunda, `context.Succeed` tek parametresi olarak tatmin eden gereksinimle çağrılır.

### <a name="use-a-handler-for-multiple-requirements"></a>Birden çok gereksinim için bir işleyici kullanın

Aşağıda, bir izin işleyicisinin üç farklı gereksinim türünü işleyebileceği bir çoktan çoğa ilişkiye örnek verilmiştir:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Yukarıdaki kod, [Pendingrequirements](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)öğesine geçer ve &mdash; başarılı olarak işaretlenmemiş gereksinimleri içeren bir özelliktir. Bir `ReadPermission` gereksinim için, kullanıcı istenen kaynağa erişmek için bir sahip veya sponsor olmalıdır. Veya gereksinimi söz konusu olduğunda `EditPermission` `DeletePermission` , istenen kaynağa erişmek için sahip olması gerekir.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>İşleyici kaydı

İşleyiciler, yapılandırma sırasında hizmetler koleksiyonuna kaydedilir. Örnek:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

Yukarıdaki kod, `MinimumAgeHandler` çağırarak tek bir olarak kaydedilir `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();` . İşleyiciler, yerleşik [hizmet yaşam sürelerinin](xref:fundamentals/dependency-injection#service-lifetimes)herhangi biri kullanılarak kaydedilebilir.

## <a name="what-should-a-handler-return"></a>İşleyici ne döndürmelidir?

`Handle` [İşleyici örnekteki](#security-authorization-handler-example) yöntemin değer döndürmediğini unutmayın. Başarı ya da hatanın durumu nasıl belirtilir?

* Bir işleyici çağırarak başarılı bir `context.Succeed(IAuthorizationRequirement requirement)` şekilde doğrulanan gereksinimi geçirerek başarılı olduğunu gösterir.

* Aynı gereksinim için diğer işleyiciler başarılı olabileceğinden, işleyicinin sorunları genellikle işlemesi gerekmez.

* Diğer gereksinim işleyicileri başarılı olsa bile hatayı güvence altına almak için çağrısı yapın `context.Fail` .

Bir işleyici veya çağırırsa `context.Succeed` `context.Fail` , diğer tüm işleyiciler hala çağırılır. Bu, başka bir işleyicinin bir gereksinimi başarıyla doğrulayan veya başarısız olsa bile, gereksinimlerin günlüğe kaydetme gibi yan etkileri üretmesine olanak tanır. Olarak ayarlandığında `false` , [ınvokehandlersafterfailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) özelliği (ASP.NET Core 1,1 ve üzeri sürümlerde mevcuttur), çağrıldığında işleyicilerin yürütülmesi için kısa devre dışı `context.Fail` . `InvokeHandlersAfterFailure` Varsayılan olarak `true` , bu durumda tüm işleyiciler çağrılır.

> [!NOTE]
> Yetkilendirme işleyicileri, kimlik doğrulama başarısız olsa bile çağrılır.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Bir gereksinim için neden birden çok işleyici istiyorum?

Değerlendirmenin bir **veya** temelinde olmasını istediğiniz durumlarda, tek bir gereksinim için birden çok işleyici uygulayın. Örneğin, Microsoft yalnızca ana kartlarla açık olan kapılara sahiptir. Ana kartınızı evden bırakırsanız, alıcı geçici bir etiket yazdırır ve kapıyı sizin için açar. Bu senaryoda, tek bir gereksinimi incelerken tek bir gereksinimin, *Buildingentry*, ancak birden çok işleyici vardır.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Her iki işleyicinin de [kaydedildiğinden](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)emin olun. Bir ilke değerlendirirken her iki işleyici de başarılı olursa `BuildingEntryRequirement` , ilke değerlendirmesi başarılı olur.

## <a name="use-a-func-to-fulfill-a-policy"></a>Bir ilkeyi yerine getirmek için bir Func kullanın

Kodun kodda hızlı bir şekilde kullanılması için bir ilkeyi karşıladığı durumlar olabilir. İlke `Func<AuthorizationHandlerContext, bool>` Oluşturucu ile ilkenizi yapılandırırken bir sağlamak mümkündür `RequireAssertion` .

Örneğin, önceki, `BadgeEntryHandler` aşağıdaki gibi yeniden yazılabilir:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="access-mvc-request-context-in-handlers"></a>İşleyicilerde MVC istek bağlamına erişme

`HandleRequirementAsync`Bir yetkilendirme işleyicisinde uyguladığınız yöntemin iki parametresi vardır: bir `AuthorizationHandlerContext` ve `TRequirement` işleme çalışıyorsunuz. Daha SignalR `Resource` `AuthorizationHandlerContext` fazla bilgi GEÇIRMEK için, MVC gibi çerçeveler veya üzerinde özelliğine herhangi bir nesne eklemek ücretsizdir.

Örneğin, MVC, özelliğinde [Authorizationfiltercontext](/dotnet/api/?term=AuthorizationFilterContext) örneğini geçirir `Resource` . Bu özellik `HttpContext` ,, ve IÇIN `RouteData` MVC ve sayfalar tarafından sağlanan diğer her şeye erişim sağlar Razor .

Özelliğin kullanımı Framework 'e `Resource` özgüdür. Özelliğindeki bilgilerin kullanılması, `Resource` Yetkilendirme ilkelerinizi belirli çerçeveler ile sınırlandırır. `Resource`Anahtar sözcüğünü kullanarak özelliği atamalısınız `is` ve sonra, kodunuzun `InvalidCastException` diğer çerçeveler üzerinde çalıştırıldığında bir ile çökmemesini sağlamak için dönüştürmenin başarılı olduğunu doğrulayın:

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
