---
title: ASP.NET Çekirdek özel yetkilendirme politikası sağlayıcıları
author: mjrousos
description: Yetkilendirme ilkelerini dinamik olarak oluşturmak için ASP.NET Core uygulamasında özel bir IAuthorizationPolicyProvider'ı nasıl kullanacağınızı öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 2a8b189cc9f17529a962a1f9642c7bb199d5781b
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440928"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>ASP.NET Core'da IAuthorizationPolicyProvider kullanan Özel Yetkilendirme İlke Sağlayıcıları 

Yazar: [Mike Rousos](https://github.com/mjrousos)

Genellikle [ilke tabanlı yetkilendirme](xref:security/authorization/policies)kullanırken, ilkeler `AuthorizationOptions.AddPolicy` yetkilendirme hizmeti yapılandırmasının bir parçası olarak arayarak kaydedilir. Bazı senaryolarda, tüm yetkilendirme ilkelerini bu şekilde kaydetmek (veya arzu edilen) olmayabilir. Bu gibi durumlarda, yetkilendirme `IAuthorizationPolicyProvider` ilkelerinin nasıl sağlandığını denetlemek için özel bir özel kullanabilirsiniz.

Özel bir [IAuthorizationPolicyProvider'ın](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) yararlı olabileceği senaryolara örnek olarak şunlar verilebilir:

* İlke değerlendirmesi sağlamak için harici bir hizmet kullanma.
* Çok çeşitli ilkeler kullanmak (örneğin, farklı oda numaraları veya yaşlar için), böylece her bir yetkilendirme `AuthorizationOptions.AddPolicy` ilkesini bir çağrıyla eklemek mantıklı değildir.
* Harici bir veri kaynağındaki (veritabanı gibi) bilgilere dayalı olarak çalışma zamanında ilkeler oluşturmak veya başka bir mekanizma aracılığıyla yetkilendirme gereksinimlerini dinamik olarak belirlemek.

Örnek kodu [AspNetCore GitHub deposundan görüntüleyin](https://github.com/dotnet/AspNetCore) [veya indirin.](https://github.com/dotnet/AspNetCore/tree/release/2.2/src/Security/samples/CustomPolicyProvider) dotnet/AspNetCore depo ZIP dosyasını indirin. Dosyanın zip'ini açın. *src/Security/samples/CustomPolicyProvider* proje klasörüne gidin.

## <a name="customize-policy-retrieval"></a>İlke alma özelleştir

ASP.NET Core uygulamaları yetkilendirme `IAuthorizationPolicyProvider` ilkeleri almak için arabirimin bir uygulama kullanın. Varsayılan olarak, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) kaydedilir ve kullanılır. `DefaultAuthorizationPolicyProvider`bir `AuthorizationOptions` `IServiceCollection.AddAuthorization` çağrıda sağlanan ilkeler döndürür.

Uygulamanın bağımlılık `IAuthorizationPolicyProvider` [enjeksiyon](xref:fundamentals/dependency-injection) konteynerine farklı bir uygulama kaydederek bu davranışı özelleştirin. 

Arabirim `IAuthorizationPolicyProvider` üç API içerir:

* [GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) belirli bir ad için bir yetkilendirme ilkesi döndürür.
* [GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) varsayılan yetkilendirme ilkesini (ilke belirtilmeden öznitelikler için `[Authorize]` kullanılan ilke) döndürür. 
* [GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) geri dönüş yetkilendirme ilkesini döndürür (hiçbir ilke belirtilmediğinde Yetkilendirme Middleware tarafından kullanılan ilke). 

Bu API'leri uygulayarak, yetkilendirme ilkelerinin nasıl sağlandığını özelleştirebilirsiniz.

## <a name="parameterized-authorize-attribute-example"></a>Parametreli yetkilendirme öznitelik örneği

Yararlı olan `IAuthorizationPolicyProvider` bir senaryo, `[Authorize]` gereksinimleri bir parametreye bağlı olan özel öznitelikleri etkinleştirmektir. Örneğin, [ilke tabanlı yetkilendirme](xref:security/authorization/policies) belgelerinde, örnek olarak yaşa dayalı ("En Az21") bir ilke kullanılmıştır. Bir uygulamadaki farklı denetleyici eylemleri *farklı* yaşlardaki kullanıcıların kullanımına sunulmalıdır, çok farklı yaş tabanlı ilkelere sahip olmak yararlı olabilir. Uygulamanın gereksinim edeceği tüm farklı yaş tabanlı ilkeleri kaydetmek `AuthorizationOptions`yerine, ilkeleri bir özelle `IAuthorizationPolicyProvider`dinamik olarak oluşturabilirsiniz. İlkeleri kullanmayı kolaylaştırmak için, eylemleri '' nin gibi `[MinimumAgeAuthorize(20)]`özel yetkilendirme özniteliğiyle açıklama ekleyebilirsiniz.

## <a name="custom-authorization-attributes"></a>Özel Yetkilendirme öznitelikleri

Yetkilendirme ilkeleri adlarıyla tanımlanır. Daha `MinimumAgeAuthorizeAttribute` önce açıklanan özel ilgili yetkilendirme ilkesi almak için kullanılabilecek bir dize bağımsız değişkenleri eşlemek gerekir. Bunu, `AuthorizeAttribute` `Age` mülkün mülkiyetini saran `AuthorizeAttribute.Policy` ve türeterek yapabilirsiniz.

```csharp
internal class MinimumAgeAuthorizeAttribute : AuthorizeAttribute
{
    const string POLICY_PREFIX = "MinimumAge";

    public MinimumAgeAuthorizeAttribute(int age) => Age = age;

    // Get or set the Age property by manipulating the underlying Policy property
    public int Age
    {
        get
        {
            if (int.TryParse(Policy.Substring(POLICY_PREFIX.Length), out var age))
            {
                return age;
            }
            return default(int);
        }
        set
        {
            Policy = $"{POLICY_PREFIX}{value.ToString()}";
        }
    }
}
```

Bu öznitelik türü, sabit kodlu önek () `Policy` `"MinimumAge"`dayalı bir dize ve bir tamsayı oluşturucu üzerinden geçti.

Bunu, parametre olarak bir tamsayı `Authorize` alması dışında, diğer özniteliklerle aynı şekilde eylemlere uygulayabilirsiniz.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>Özel IAuthorizationPolicyProvider

Özel, `MinimumAgeAuthorizeAttribute` istenilen herhangi bir minimum yaş için yetkilendirme ilkeleri talep etmeyi kolaylaştırır. Çözülmesi gereken bir sonraki sorun, yetkilendirme ilkelerinin tüm bu farklı yaşlar için kullanılabilir olduğundan emin olmaktır. Burada bir `IAuthorizationPolicyProvider` yararlıdır.

Kullanırken, `MinimumAgeAuthorizationAttribute`yetkilendirme ilkesi adları deseni `"MinimumAge" + Age`izler, `IAuthorizationPolicyProvider` bu nedenle özel yetkilendirme ilkeleri oluşturmak gerekir:

* İlke adından yaşı ayrışdırma.
* Yeni `AuthorizationPolicyBuilder` bir oluşturmak için kullanma`AuthorizationPolicy`
* Bu ve aşağıdaki örneklerde, kullanıcının bir çerez aracılığıyla kimlik doğrulaması olduğu varsayılır. En `AuthorizationPolicyBuilder` az bir yetkilendirme düzeni adı ile oluşturulmalı veya her zaman başarılı olmalıdır. Aksi takdirde, kullanıcıya nasıl meydan okuyacağı hakkında bilgi yoktur ve bir özel durum atılır.
* `AuthorizationPolicyBuilder.AddRequirements`İlkeye gereksinimler ekleme. Diğer senaryolarda, ", `RequireClaim` `RequireRole`" `RequireUserName` veya bunun yerine kullanabilirsiniz.

```csharp
internal class MinimumAgePolicyProvider : IAuthorizationPolicyProvider
{
    const string POLICY_PREFIX = "MinimumAge";

    // Policies are looked up by string name, so expect 'parameters' (like age)
    // to be embedded in the policy names. This is abstracted away from developers
    // by the more strongly-typed attributes derived from AuthorizeAttribute
    // (like [MinimumAgeAuthorize()] in this sample)
    public Task<AuthorizationPolicy> GetPolicyAsync(string policyName)
    {
        if (policyName.StartsWith(POLICY_PREFIX, StringComparison.OrdinalIgnoreCase) &&
            int.TryParse(policyName.Substring(POLICY_PREFIX.Length), out var age))
        {
            var policy = new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme);
            policy.AddRequirements(new MinimumAgeRequirement(age));
            return Task.FromResult(policy.Build());
        }

        return Task.FromResult<AuthorizationPolicy>(null);
    }
}
```

## <a name="multiple-authorization-policy-providers"></a>Birden çok yetkilendirme ilkesi sağlayıcısı

Özel `IAuthorizationPolicyProvider` uygulamaları kullanırken, ASP.NET Core'un yalnızca bir `IAuthorizationPolicyProvider`tane .' Özel bir sağlayıcı kullanılacak tüm ilke adları için yetkilendirme ilkeleri sağlayamazsa, bir yedekleme sağlayıcısına ertelemelidir. 

Örneğin, hem özel yaş ilkeleri hem de daha geleneksel rol tabanlı ilke alma gerektiren bir uygulama düşünün. Böyle bir uygulama, özel bir yetkilendirme ilkesi sağlayıcısı kullanabilir:

* İlke adlarını ayrışdırmaya çalışır. 
* İlke adı bir yaş `DefaultAuthorizationPolicyProvider`içermiyorsa, farklı bir ilke sağlayıcısına (örneğin) çağrılar.

Yukarıda `IAuthorizationPolicyProvider` gösterilen örnek uygulama, oluşturucusunda bir yedekleme ilkesi sağlayıcısı `DefaultAuthorizationPolicyProvider` oluşturarak (ilke adının beklenen 'MinimumYaş' + yaş deseniyle eşleşmemesi durumunda kullanılacak) kullanmak üzere güncellenebilir.

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

Daha sonra, `GetPolicyAsync` yöntem yerine null `BackupPolicyProvider` dönen kullanmak için güncelleştirilebilir:

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>Varsayılan ilke

Adlandırılmış yetkilendirme ilkeleri sağlamanın yanı `IAuthorizationPolicyProvider` sıra, `GetDefaultPolicyAsync` bir ilke adı `[Authorize]` belirtilmeden öznitelikler için bir yetkilendirme ilkesi sağlamak için bir özel uygulama gerekir.

Çoğu durumda, bu yetkilendirme özniteliği yalnızca kimlik doğrulaması yapılan bir kullanıcıyı gerektirir, böylece aşağıdakileri arayarak gerekli ilkeyi `RequireAuthenticatedUser`yapabilirsiniz:

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

Bir özel `IAuthorizationPolicyProvider`tüm yönleriyle olduğu gibi, gerektiğinde, bu özelleştirebilirsiniz. Bazı durumlarda, varsayılan ilkeyi bir geri dönüşten `IAuthorizationPolicyProvider`almak istenebilir.

## <a name="fallback-policy"></a>Geri dönüş ilkesi

Bir `IAuthorizationPolicyProvider` özel, ilkeleri `GetFallbackPolicyAsync` [birleştirirken](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) ve hiçbir ilke belirtilmediğinde kullanılan bir ilke sağlamak için isteğe bağlı olarak uygulayabilirsiniz. Null `GetFallbackPolicyAsync` olmayan bir ilke döndürürse, iade edilen ilke, istek için hiçbir ilke belirtilmediğinde Yetkilendirme Middleware tarafından kullanılır.

Geri dönüş ilkesi gerekmiyorsa, sağlayıcı `null` geri dönüş sağlayıcısına dönebilir veya erteleyebilir:

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>Özel bir IAuthorizationPolicyProvider kullanın

Bir özel ilkeler kullanmak `IAuthorizationPolicyProvider`için, şunları gerekir:

* Tüm ilke tabanlı yetkilendirme senaryolarında olduğu gibi, bağımlılık enjeksiyonu `AuthorizationHandler` [(ilke tabanlı yetkilendirmede](xref:security/authorization/policies#authorization-handlers)açıklanan) uygun türleri kaydedin.
* Varsayılan ilke sağlayıcısının yerini almak için uygulamanın `Startup.ConfigureServices`bağımlılık enjeksiyon hizmeti koleksiyonuna (içinde) özel `IAuthorizationPolicyProvider` türü kaydedin.

```csharp
services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
```

Tam bir `IAuthorizationPolicyProvider` özel örnek [dotnet / aspnetcore GitHub deposunda](https://github.com/dotnet/aspnetcore/tree/ea555458dc61e04314598c25b3ab8c56362a5123/src/Security/samples/CustomPolicyProvider)mevcuttur.
