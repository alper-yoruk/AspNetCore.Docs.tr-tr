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
# <a name="policy-based-authorization-in-aspnet-core"></a>ASP.NET Core'da politika tabanlı yetkilendirme

::: moniker range=">= aspnetcore-3.0"

Kapakların altında, [rol tabanlı yetkilendirme](xref:security/authorization/roles) ve talep tabanlı [yetkilendirme](xref:security/authorization/claims) bir gereksinim, gereksinim işleyicisi ve önceden yapılandırılmış bir ilke kullanır. Bu yapı taşları, yetkilendirme değerlendirmelerinin koddaki ifadesini destekler. Sonuç daha zengin, yeniden kullanılabilir, test edilebilir bir yetkilendirme yapısıdır.

Yetkilendirme ilkesi bir veya daha fazla gereksinimden oluşur. Yetkilendirme hizmeti yapılandırmasının bir parçası olarak, `Startup.ConfigureServices` yöntemde kaydedilir:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53, 58)]

Önceki örnekte, bir "AtLeast21" ilkesi oluşturulur. Gereksinime parametre&mdash;olarak verilen minimum yaş için tek bir gereksinimvardır.

## <a name="iauthorizationservice"></a>IAuthorizationService 

Yetkilendirmenin başarılı olup olmadığını belirleyen <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>birincil hizmet:

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

Önceki kod [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)iki yöntem vurgulamaktadır.

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>hiçbir yöntem ve yetkilendirme başarılı olup olmadığını izleme mekanizması ile bir işaretçi hizmetidir.

Gereksinimlerin karşılanıp karşılanmadığını denetlemekle ilgili her <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> şeyi kontrol etmekle yükümlüdür:
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

Sınıf, <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> gereksinimlerin karşılanıp karşılanmadığını işaretlemek için işleyicinin kullandığı şeydir:

```csharp
 context.Succeed(requirement)
```

Aşağıdaki kod, yetkilendirme hizmetinin basitleştirilmiş (ve açıklamalı) varsayılan uygulamasını gösterir:

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

Aşağıdaki kod tipik `ConfigureServices`bir gösterir:

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

Kullanım <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` veya yetkilendirme için.

## <a name="applying-policies-to-mvc-controllers"></a>İlkeleri MVC denetleyicilerine uygulama

Razor Pages kullanıyorsanız, bu belgede [Razor Pages'e ilkeler uygulama'ya](#applying-policies-to-razor-pages) bakın.

İlkeler, öznitelik ilkesi adı `[Authorize]` ile kullanılarak denetleyicilere uygulanır. Örneğin:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>İlkeleri Jilet Sayfalarına Uygulama

İlkeler, ilke adı ile `[Authorize]` öznitelik kullanılarak Razor Pages uygulanır. Örneğin:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

İlkeler, [bir yetkilendirme kuralı](xref:security/authorization/razor-pages-authorization)kullanılarak Razor Pages'e de uygulanabilir.

## <a name="requirements"></a>Gereksinimler

Yetkilendirme gereksinimi, bir ilkenin geçerli kullanıcı ilkesini değerlendirmek için kullanabileceği veri parametreleri topluluğudur. "En Az21" politikamızda, gereksinim minimum yaş&mdash;tek bir parametredir. Bir gereksinim, boş bir işaretçi arabirimi olan [IAuthorizationRequirement'ı](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)uygular. Parametrelendirilmiş bir minimum yaş gereksinimi aşağıdaki gibi uygulanabilir:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Yetkilendirme ilkesi birden çok yetkilendirme gereksinimi içeriyorsa, ilke değerlendirmesinin başarılı olabilmesi için tüm gereksinimlerin geçmesi gerekir. Başka bir deyişle, tek bir yetkilendirme ilkesine eklenen birden çok yetkilendirme gereksinimi **VE** temelinde ele alınır.

> [!NOTE]
> Bir gereksinimin veri veya özelliklere sahip olması gerekmez.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Yetkilendirme işleyicileri

Bir gereksinimin özelliklerinin değerlendirilmesini bir yetkilendirme işleyicisi sorumludur. Yetkilendirme işleyicisi, erişime izin verilip verilmediğini belirlemek için gereksinimleri sağlanan [bir YetkilendirmeHandlerContext'a](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) karşı değerlendirir.

Bir [gereksinimin birden çok işleyicisi](#security-authorization-policies-based-multiple-handlers)olabilir. İşleyici, işlenecek gereksinimin bulunduğu `TRequirement` [yetkilendirmeHandler\<TRequirement>'ı ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)devralabilir. Alternatif olarak, bir işleyici birden fazla gereksinim türünü işlemek için [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) uygulayabilir.

### <a name="use-a-handler-for-one-requirement"></a>Tek bir gereksinim için işleyici kullanma

<a name="security-authorization-handler-example"></a>

Aşağıda, minimum yaş işleyicisinin tek bir gereksinimkullandığı bire bir ilişki örneği verilmiştir:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Önceki kod, geçerli kullanıcı ilkesinin bilinen ve güvenilen bir Veren tarafından verilmiş bir doğum talebi tarihi olup olmadığını belirler. Talep eksik olduğunda yetkilendirme gerçekleşemez ve bu durumda tamamlanmış bir görev iade edilir. Bir talep olduğunda, kullanıcının yaşı hesaplanır. Kullanıcı gereksinimle tanımlanan minimum yaşı karşılıyorsa, yetkilendirme başarılı sayılır. Yetkilendirme başarılı olduğunda, `context.Succeed` tek parametresi olarak memnun gereksinimi ile çağrılır.

### <a name="use-a-handler-for-multiple-requirements"></a>Birden çok gereksinim için işleyici kullanma

Aşağıda, bir izin işleyicisinin üç farklı gereksinim türünü işleyebileceği bir-çok ilişkisine bir örnek verilmiştir:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Önceki [kod, bekleyen](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;olarak işaretlenmemiş gereksinimleri içeren bir özellik olan Bekleme Gereksinimleri'nden geçer. Bir `ReadPermission` gereksinim için, kullanıcının istenen kaynağa erişmek için bir sahip veya sponsor olması gerekir. Bir `EditPermission` veya `DeletePermission` gereksinim söz konusu olduğunda, istenen kaynağa erişmek için bir sahip olması gerekir.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Işleyici kaydı

Işleyiciler yapılandırma sırasında hizmet koleksiyonuna kaydedilir. Örneğin:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=31-32,39-40,42-45, 53-55, 58)]

Önceki kod çağırarak `MinimumAgeHandler` `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`singleton olarak kaydeder. İşleyiciler, yerleşik [hizmet kullanım ömürlerinden](xref:fundamentals/dependency-injection#service-lifetimes)herhangi biri kullanılarak kaydedilebilir.

## <a name="what-should-a-handler-return"></a>Bir işleyici ne iade etmelidir?

`Handle` [Işleyici örneğindeki](#security-authorization-handler-example) yöntemin hiçbir değer döndürmediğini unutmayın. Başarı veya başarısızlık durumu nasıl gösterilir?

* İşleyici, başarıyla `context.Succeed(IAuthorizationRequirement requirement)`doğrulanmış gereksinimi geçerek arayarak başarıyı gösterir.

* Aynı gereksinime sahip diğer işleyiciler başarılı olabileceğinden, işleyicinin hataları genellikle işlemesi gerekmez.

* Diğer gereksinim işleyicileri başarılı olsa bile, `context.Fail`başarısızlığı garanti etmek için.

Bir işleyici `context.Succeed` ararsa veya `context.Fail`diğer tüm işleyiciler hala çağrılır. Bu, gereksinimlerin, başka bir işleyici bir gereksinimi başarıyla doğrulamış veya başarısız olsa bile gerçekleşen günlük oluşturma gibi yan etkiler üretmesine olanak tanır. Çağrıldığında `false` [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) özelliği (ASP.NET Core 1.1 ve sonrası olarak kullanılabilir) kısa devreler çağrıldığında `context.Fail` işleyicilerin yürütülmesi ayarlanır. `InvokeHandlersAfterFailure`varsayılan olarak `true`, bu durumda tüm işleyiciler çağrılır.

> [!NOTE]
> Kimlik doğrulaması başarısız olsa bile yetkilendirme işleyicileri çağrılır.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Neden bir gereksinim için birden fazla işleyici isteyeyim?

Değerlendirmenin **OR** bazında olmasını istediğiniz durumlarda, tek bir gereksinim için birden çok işleyici uygulayın. Örneğin, Microsoft'un yalnızca anahtar kartlarıyla açılan kapıları vardır. Anahtar kartınızı evde bırakırsanız, resepsiyonist geçici bir etiket yazdırır ve kapıyı sizin için açar. Bu senaryoda, tek bir gereksinim, *BuildingEntry,* ancak birden çok işleyicileri, her biri tek bir gereksinimi inceleyen olurdu.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Her iki işleyicinin de [kayıtlı](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)olduğundan emin olun. Bir ilke , `BuildingEntryRequirement`ilke değerlendirmesi başarılı olduğunda ya işleyici başarılı olur.

## <a name="using-a-func-to-fulfill-a-policy"></a>Bir ilkeyi yerine getirmek için func kullanma

Bir ilkeyi yerine getirmenin kodla ifade edilmesinin kolay olduğu durumlar olabilir. İlke oluşturucu ile `Func<AuthorizationHandlerContext, bool>` politikanızı yapılandırırken bir şey sağlamak mümkündür. `RequireAssertion`

Örneğin, önceki `BadgeEntryHandler` aşağıdaki gibi yeniden yazılabilir:

[!code-csharp[](policies/samples/3.0PoliciesAuthApp1/Startup.cs?range=42-43,47-53)]

## <a name="accessing-mvc-request-context-in-handlers"></a>İşleyicilerde MVC istek bağlamına erişim

Yetkilendirme `HandleRequirementAsync` işleyicisinde uyguladığınız yöntemin iki `AuthorizationHandlerContext` parametresi vardır: a ve `TRequirement` siz işlediğiniz. MVC veya Jabbr gibi çerçeveler, ek bilgi `Resource` aktarmak `AuthorizationHandlerContext` için tesise herhangi bir nesne eklemekte serbesttir.

Örneğin, MVC özellikte [YetkilendirmeFiltresi](/dotnet/api/?term=AuthorizationFilterContext) Bağlamı'nın bir örneğini `Resource` geçirir. Bu `HttpContext`özellik, `RouteData`MVC ve Razor Pages tarafından sağlanan her şeye ve her şeye erişim sağlar.

Özelliğin `Resource` kullanımı çerçeveye özgüdür. Tesisteki `Resource` bilgileri kullanmak, yetkilendirme ilkelerinizi belirli çerçevelerle sınırlar. `Resource` Özelliği anahtar kelimeyi `is` kullanarak kullanmalı ve ardından kodunuzun diğer çerçevelerde `InvalidCastException` çalıştırıldığında çökmediğinden emin olmak için dökümün başarılı olduğunu onaylamanız gerekir:

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

Kapakların altında, [rol tabanlı yetkilendirme](xref:security/authorization/roles) ve talep tabanlı [yetkilendirme](xref:security/authorization/claims) bir gereksinim, gereksinim işleyicisi ve önceden yapılandırılmış bir ilke kullanır. Bu yapı taşları, yetkilendirme değerlendirmelerinin koddaki ifadesini destekler. Sonuç daha zengin, yeniden kullanılabilir, test edilebilir bir yetkilendirme yapısıdır.

Yetkilendirme ilkesi bir veya daha fazla gereksinimden oluşur. Yetkilendirme hizmeti yapılandırmasının bir parçası olarak, `Startup.ConfigureServices` yöntemde kaydedilir:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,66)]

Önceki örnekte, bir "AtLeast21" ilkesi oluşturulur. Gereksinime parametre&mdash;olarak verilen minimum yaş için tek bir gereksinimvardır.

## <a name="iauthorizationservice"></a>IAuthorizationService 

Yetkilendirmenin başarılı olup olmadığını belirleyen <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService>birincil hizmet:

[!code-csharp[](policies/samples/stubs/copy_of_IAuthorizationService.cs?highlight=24-25,48-49&name=snippet)]

Önceki kod [IAuthorizationService](https://github.com/dotnet/AspNetCore/blob/v2.2.4/src/Security/Authorization/Core/src/IAuthorizationService.cs)iki yöntem vurgulamaktadır.

<xref:Microsoft.AspNetCore.Authorization.IAuthorizationRequirement>hiçbir yöntem ve yetkilendirme başarılı olup olmadığını izleme mekanizması ile bir işaretçi hizmetidir.

Gereksinimlerin karşılanıp karşılanmadığını denetlemekle ilgili her <xref:Microsoft.AspNetCore.Authorization.IAuthorizationHandler> şeyi kontrol etmekle yükümlüdür:
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

Sınıf, <xref:Microsoft.AspNetCore.Authorization.AuthorizationHandlerContext> gereksinimlerin karşılanıp karşılanmadığını işaretlemek için işleyicinin kullandığı şeydir:

```csharp
 context.Succeed(requirement)
```

Aşağıdaki kod, yetkilendirme hizmetinin basitleştirilmiş (ve açıklamalı) varsayılan uygulamasını gösterir:

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

Aşağıdaki kod tipik `ConfigureServices`bir gösterir:

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

Kullanım <xref:Microsoft.AspNetCore.Authorization.IAuthorizationService> `[Authorize(Policy = "Something")]` veya yetkilendirme için.

## <a name="applying-policies-to-mvc-controllers"></a>İlkeleri MVC denetleyicilerine uygulama

Razor Pages kullanıyorsanız, bu belgede [Razor Pages'e ilkeler uygulama'ya](#applying-policies-to-razor-pages) bakın.

İlkeler, öznitelik ilkesi adı `[Authorize]` ile kullanılarak denetleyicilere uygulanır. Örneğin:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Controllers/AlcoholPurchaseController.cs?name=snippet_AlcoholPurchaseControllerClass&highlight=4)]

## <a name="applying-policies-to-razor-pages"></a>İlkeleri Jilet Sayfalarına Uygulama

İlkeler, ilke adı ile `[Authorize]` öznitelik kullanılarak Razor Pages uygulanır. Örneğin:

[!code-csharp[](policies/samples/PoliciesAuthApp2/Pages/AlcoholPurchase.cshtml.cs?name=snippet_AlcoholPurchaseModelClass&highlight=4)]

İlkeler, [bir yetkilendirme kuralı](xref:security/authorization/razor-pages-authorization)kullanılarak Razor Pages'e de uygulanabilir.

## <a name="requirements"></a>Gereksinimler

Yetkilendirme gereksinimi, bir ilkenin geçerli kullanıcı ilkesini değerlendirmek için kullanabileceği veri parametreleri topluluğudur. "En Az21" politikamızda, gereksinim minimum yaş&mdash;tek bir parametredir. Bir gereksinim, boş bir işaretçi arabirimi olan [IAuthorizationRequirement'ı](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationrequirement)uygular. Parametrelendirilmiş bir minimum yaş gereksinimi aşağıdaki gibi uygulanabilir:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/MinimumAgeRequirement.cs?name=snippet_MinimumAgeRequirementClass)]

Yetkilendirme ilkesi birden çok yetkilendirme gereksinimi içeriyorsa, ilke değerlendirmesinin başarılı olabilmesi için tüm gereksinimlerin geçmesi gerekir. Başka bir deyişle, tek bir yetkilendirme ilkesine eklenen birden çok yetkilendirme gereksinimi **VE** temelinde ele alınır.

> [!NOTE]
> Bir gereksinimin veri veya özelliklere sahip olması gerekmez.

<a name="security-authorization-policies-based-authorization-handler"></a>

## <a name="authorization-handlers"></a>Yetkilendirme işleyicileri

Bir gereksinimin özelliklerinin değerlendirilmesini bir yetkilendirme işleyicisi sorumludur. Yetkilendirme işleyicisi, erişime izin verilip verilmediğini belirlemek için gereksinimleri sağlanan [bir YetkilendirmeHandlerContext'a](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext) karşı değerlendirir.

Bir [gereksinimin birden çok işleyicisi](#security-authorization-policies-based-multiple-handlers)olabilir. İşleyici, işlenecek gereksinimin bulunduğu `TRequirement` [yetkilendirmeHandler\<TRequirement>'ı ](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandler-1)devralabilir. Alternatif olarak, bir işleyici birden fazla gereksinim türünü işlemek için [IAuthorizationHandler](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationhandler) uygulayabilir.

### <a name="use-a-handler-for-one-requirement"></a>Tek bir gereksinim için işleyici kullanma

<a name="security-authorization-handler-example"></a>

Aşağıda, minimum yaş işleyicisinin tek bir gereksinimkullandığı bire bir ilişki örneği verilmiştir:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/MinimumAgeHandler.cs?name=snippet_MinimumAgeHandlerClass)]

Önceki kod, geçerli kullanıcı ilkesinin bilinen ve güvenilen bir Veren tarafından verilmiş bir doğum talebi tarihi olup olmadığını belirler. Talep eksik olduğunda yetkilendirme gerçekleşemez ve bu durumda tamamlanmış bir görev iade edilir. Bir talep olduğunda, kullanıcının yaşı hesaplanır. Kullanıcı gereksinimle tanımlanan minimum yaşı karşılıyorsa, yetkilendirme başarılı sayılır. Yetkilendirme başarılı olduğunda, `context.Succeed` tek parametresi olarak memnun gereksinimi ile çağrılır.

### <a name="use-a-handler-for-multiple-requirements"></a>Birden çok gereksinim için işleyici kullanma

Aşağıda, bir izin işleyicisinin üç farklı gereksinim türünü işleyebileceği bir-çok ilişkisine bir örnek verilmiştir:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/PermissionHandler.cs?name=snippet_PermissionHandlerClass)]

Önceki [kod, bekleyen](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.pendingrequirements#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_PendingRequirements)&mdash;olarak işaretlenmemiş gereksinimleri içeren bir özellik olan Bekleme Gereksinimleri'nden geçer. Bir `ReadPermission` gereksinim için, kullanıcının istenen kaynağa erişmek için bir sahip veya sponsor olması gerekir. Bir `EditPermission` veya `DeletePermission` gereksinim söz konusu olduğunda, istenen kaynağa erişmek için bir sahip olması gerekir.

<a name="security-authorization-policies-based-handler-registration"></a>

### <a name="handler-registration"></a>Işleyici kaydı

Işleyiciler yapılandırma sırasında hizmet koleksiyonuna kaydedilir. Örneğin:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=32-33,48-53,61,62-63,66)]

Önceki kod çağırarak `MinimumAgeHandler` `services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();`singleton olarak kaydeder. İşleyiciler, yerleşik [hizmet kullanım ömürlerinden](xref:fundamentals/dependency-injection#service-lifetimes)herhangi biri kullanılarak kaydedilebilir.

## <a name="what-should-a-handler-return"></a>Bir işleyici ne iade etmelidir?

`Handle` [Işleyici örneğindeki](#security-authorization-handler-example) yöntemin hiçbir değer döndürmediğini unutmayın. Başarı veya başarısızlık durumu nasıl gösterilir?

* İşleyici, başarıyla `context.Succeed(IAuthorizationRequirement requirement)`doğrulanmış gereksinimi geçerek arayarak başarıyı gösterir.

* Aynı gereksinime sahip diğer işleyiciler başarılı olabileceğinden, işleyicinin hataları genellikle işlemesi gerekmez.

* Diğer gereksinim işleyicileri başarılı olsa bile, `context.Fail`başarısızlığı garanti etmek için.

Bir işleyici `context.Succeed` ararsa veya `context.Fail`diğer tüm işleyiciler hala çağrılır. Bu, gereksinimlerin, başka bir işleyici bir gereksinimi başarıyla doğrulamış veya başarısız olsa bile gerçekleşen günlük oluşturma gibi yan etkiler üretmesine olanak tanır. Çağrıldığında `false` [InvokeHandlersAfterFailure](/dotnet/api/microsoft.aspnetcore.authorization.authorizationoptions.invokehandlersafterfailure#Microsoft_AspNetCore_Authorization_AuthorizationOptions_InvokeHandlersAfterFailure) özelliği (ASP.NET Core 1.1 ve sonrası olarak kullanılabilir) kısa devreler çağrıldığında `context.Fail` işleyicilerin yürütülmesi ayarlanır. `InvokeHandlersAfterFailure`varsayılan olarak `true`, bu durumda tüm işleyiciler çağrılır.

> [!NOTE]
> Kimlik doğrulaması başarısız olsa bile yetkilendirme işleyicileri çağrılır.

<a name="security-authorization-policies-based-multiple-handlers"></a>

## <a name="why-would-i-want-multiple-handlers-for-a-requirement"></a>Neden bir gereksinim için birden fazla işleyici isteyeyim?

Değerlendirmenin **OR** bazında olmasını istediğiniz durumlarda, tek bir gereksinim için birden çok işleyici uygulayın. Örneğin, Microsoft'un yalnızca anahtar kartlarıyla açılan kapıları vardır. Anahtar kartınızı evde bırakırsanız, resepsiyonist geçici bir etiket yazdırır ve kapıyı sizin için açar. Bu senaryoda, tek bir gereksinim, *BuildingEntry,* ancak birden çok işleyicileri, her biri tek bir gereksinimi inceleyen olurdu.

*BuildingEntryRequirement.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Requirements/BuildingEntryRequirement.cs?name=snippet_BuildingEntryRequirementClass)]

*BadgeEntryHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/BadgeEntryHandler.cs?name=snippet_BadgeEntryHandlerClass)]

*TemporaryStickerHandler.cs*

[!code-csharp[](policies/samples/PoliciesAuthApp1/Services/Handlers/TemporaryStickerHandler.cs?name=snippet_TemporaryStickerHandlerClass)]

Her iki işleyicinin de [kayıtlı](xref:security/authorization/policies#security-authorization-policies-based-handler-registration)olduğundan emin olun. Bir ilke , `BuildingEntryRequirement`ilke değerlendirmesi başarılı olduğunda ya işleyici başarılı olur.

## <a name="using-a-func-to-fulfill-a-policy"></a>Bir ilkeyi yerine getirmek için func kullanma

Bir ilkeyi yerine getirmenin kodla ifade edilmesinin kolay olduğu durumlar olabilir. İlke oluşturucu ile `Func<AuthorizationHandlerContext, bool>` politikanızı yapılandırırken bir şey sağlamak mümkündür. `RequireAssertion`

Örneğin, önceki `BadgeEntryHandler` aşağıdaki gibi yeniden yazılabilir:

[!code-csharp[](policies/samples/PoliciesAuthApp1/Startup.cs?range=50-51,55-61)]

## <a name="accessing-mvc-request-context-in-handlers"></a>İşleyicilerde MVC istek bağlamına erişim

Yetkilendirme `HandleRequirementAsync` işleyicisinde uyguladığınız yöntemin iki `AuthorizationHandlerContext` parametresi vardır: a ve `TRequirement` siz işlediğiniz. MVC veya SignalR gibi çerçeveler, ek bilgi `Resource` aktarmak `AuthorizationHandlerContext` için tesise herhangi bir nesne eklemekte serbesttir.

Uç nokta yönlendirmesi kullanırken, yetkilendirme genellikle Yetkilendirme Middleware tarafından işlenir. Bu durumda, `Resource` özellik bir <xref:Microsoft.AspNetCore.Http.Endpoint>örneğidir. Bitiş noktası, yönlendirdiğiniz kaynağın altında yatan kaynağı araştırmak için kullanılabilir. Örneğin:

```csharp
if (context.Resource is Endpoint endpoint)
{
   var actionDescriptor = endpoint.Metadata.GetMetadata<ControllerActionDescriptor>();
   ...
}
```

Geleneksel yönlendirme yle veya yetkilendirme MVC'nin yetkilendirme filtresinin bir parçası `Resource` olarak <xref:Microsoft.AspNetCore.Mvc.Filters.AuthorizationFilterContext> gerçekleştiğinde, değeri bir örnektir. Bu `HttpContext`özellik, `RouteData`MVC ve Razor Pages tarafından sağlanan her şeye ve her şeye erişim sağlar.

Özelliğin `Resource` kullanımı çerçeveye özgüdür. Tesisteki `Resource` bilgileri kullanmak, yetkilendirme ilkelerinizi belirli çerçevelerle sınırlar. `Resource` Özelliği anahtar kelimeyi `is` kullanarak kullanmalı ve ardından kodunuzun diğer çerçevelerde `InvalidCastException` çalıştırıldığında çökmediğinden emin olmak için dökümün başarılı olduğunu onaylamanız gerekir:

```csharp
// Requires the following import:
//     using Microsoft.AspNetCore.Mvc.Filters;
if (context.Resource is AuthorizationFilterContext mvcContext)
{
    // Examine MVC-specific things like routing data.
}
```

::: moniker-end
