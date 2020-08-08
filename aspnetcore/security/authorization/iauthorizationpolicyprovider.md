---
title: ASP.NET Core 'de özel yetkilendirme Ilkesi sağlayıcıları
author: mjrousos
description: Bir ASP.NET Core uygulamasında özel bir ıauthorizationpolicyprovider kullanarak yetkilendirme ilkelerini dinamik olarak oluşturma hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: 724b1f065e83302137d920fe4e0e2b381be505b7
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022140"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a>ASP.NET Core 'de ıauthorizationpolicyprovider kullanan özel yetkilendirme Ilkesi sağlayıcıları 

, [Mike Rousos](https://github.com/mjrousos) tarafından

Genellikle [ilke tabanlı yetkilendirme](xref:security/authorization/policies)kullanılırken, ilkeler, `AuthorizationOptions.AddPolicy` yetkilendirme hizmeti yapılandırmasının bir parçası olarak çağırarak kaydedilir. Bazı senaryolarda, tüm yetkilendirme ilkelerini bu şekilde kaydetmek mümkün olmayabilir (veya istenebilir). Bu durumlarda, yetkilendirme ilkelerinin nasıl sağlanmış olduğunu denetlemek için [özel `IAuthorizationPolicyProvider` ](#ci) ' i kullanabilirsiniz.

Özel bir [ıauthorizationpolicyprovider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) yararlı olabilecek senaryolara örnek olarak şunlar verilebilir:

* İlke değerlendirmesi sağlamak için bir dış hizmet kullanma.
* Büyük bir ilke yelpazesi (örneğin, farklı oda numaraları veya yaşlar için) kullanarak, her bireysel yetkilendirme ilkesini bir çağrısıyla eklemek mantıklı değildir `AuthorizationOptions.AddPolicy` .
* Dış veri kaynağındaki bilgilere (bir veritabanı gibi) göre veya başka bir mekanizmaya göre yetkilendirme gereksinimlerini dinamik olarak belirleyerek çalışma zamanında ilkeler oluşturma.

[Aspnetcore GitHub deposundan](https://github.com/dotnet/AspNetCore) [örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) . DotNet/AspNetCore depo ZIP dosyasını indirin. Dosyayı sıkıştırmayı açın. *Src/Security/Samples/CustomPolicyProvider* proje klasörüne gidin.

## <a name="customize-policy-retrieval"></a>İlke almayı özelleştirme

ASP.NET Core uygulamalar, `IAuthorizationPolicyProvider` yetkilendirme ilkelerini almak için arabirimin bir uygulamasını kullanır. Varsayılan olarak, [Defaultauthorizationpolicyprovider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) kaydedilir ve kullanılır. `DefaultAuthorizationPolicyProvider``AuthorizationOptions`bir çağrıda sunulan ilkeleri döndürür `IServiceCollection.AddAuthorization` .

`IAuthorizationPolicyProvider`Uygulamanın [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına farklı bir uygulama kaydederek bu davranışı özelleştirin. 

`IAuthorizationPolicyProvider`Arabirim üç API içerir:

* [Getpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) , belirli bir ad için yetkilendirme ilkesi döndürüyor.
* [Getdefaultpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) varsayılan yetkilendirme ilkesini döndürür ( `[Authorize]` bir ilke belirtilmeden öznitelikler için kullanılan ilke). 
* [Getfallbackpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) , geri dönüş yetkilendirme ilkesini döndürür (Ilke belirtilmediğinde yetkilendirme ara yazılımı tarafından kullanılan ilke). 

Bu API 'Leri uygulayarak, yetkilendirme ilkelerinin nasıl sağlandığını özelleştirebilirsiniz.

## <a name="parameterized-authorize-attribute-example"></a>Parametreli yetkilendirme özniteliği örneği

Tek senaryo, `IAuthorizationPolicyProvider` `[Authorize]` gereksinimleri bir parametreye bağlı özel öznitelikleri etkinleştirir. Örneğin, [ilke tabanlı yetkilendirme](xref:security/authorization/policies) belgelerinde, örnek olarak yaş tabanlı ("AtLeast21") ilkesi kullanılmıştır. Bir uygulamadaki farklı denetleyici eylemleri *farklı* yaştaki kullanıcılar için kullanılabilir hale getirilse, çok sayıda farklı yaş tabanlı ilke olması faydalı olabilir. Uygulamanın ihtiyaç duyduğu tüm farklı yaş tabanlı ilkeleri kaydetmek yerine `AuthorizationOptions` , ilkeleri özel olarak dinamik bir şekilde oluşturabilirsiniz `IAuthorizationPolicyProvider` . İlkeleri kullanmak daha kolay hale getirmek için, gibi özel yetkilendirme özniteliğiyle eylemlere not ekleyebilirsiniz `[MinimumAgeAuthorize(20)]` .

## <a name="custom-authorization-attributes"></a>Özel yetkilendirme öznitelikleri

Yetkilendirme ilkeleri adlarıyla tanımlanır. `MinimumAgeAuthorizeAttribute`Daha önce açıklanan özel, bağımsız değişkenleri ilgili yetkilendirme ilkesini almak için kullanılabilecek bir dizeye eşlemenize ihtiyaç duyuyor. Bunu öğesinden türeterek `AuthorizeAttribute` ve özelliği sararak yaparak yapabilirsiniz `Age` `AuthorizeAttribute.Policy` .

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

Bu öznitelik türü, `Policy` sabit kodlanmış ön eki ( `"MinimumAge"` ) ve Oluşturucu aracılığıyla geçirilen bir tamsayıyı temel alan bir dizeye sahiptir.

`Authorize`Bir parametre olarak bir tamsayı almak dışında, diğer özniteliklerle aynı şekilde eylemlere uygulayabilirsiniz.

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a>Özel ıauthorizationpolicyprovider

Özel, `MinimumAgeAuthorizeAttribute` istenen en düşük yaş için yetkilendirme ilkeleri istemeyi kolaylaştırır. Çözecek bir sonraki sorun, yetkilendirme ilkelerinin tüm bu farklı yaşlar için kullanılabilir olduğundan emin olmanızı sağlar. Bunun yararlı olduğu yerdir `IAuthorizationPolicyProvider` .

Kullanırken `MinimumAgeAuthorizationAttribute` , yetkilendirme ilkesi adları, bu sayede, özel olarak `"MinimumAge" + Age` `IAuthorizationPolicyProvider` Yetkilendirme İlkeleri oluşturması gerekir.

* İlke adından yaş ayrıştırılıyor.
* `AuthorizationPolicyBuilder`Yeni bir oluşturmak için kullanarak`AuthorizationPolicy`
* Bu ve aşağıdaki örneklerde, kullanıcının kimlik doğrulamasının bir ile olduğu varsayılır cookie . `AuthorizationPolicyBuilder`En az bir yetkilendirme düzeni adıyla oluşturulmalıdır ya da her zaman başarılı olur. Aksi takdirde, kullanıcıya nasıl bir sınama sağlayacaksınız ve bir özel durum oluşturulur.
* Yaş temelinde ilkeye gereksinimler ekleme `AuthorizationPolicyBuilder.AddRequirements` . Diğer senaryolarda, `RequireClaim` `RequireRole` veya `RequireUserName` yerine kullanabilirsiniz.

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

Özel uygulamalar kullanırken `IAuthorizationPolicyProvider` ASP.NET Core yalnızca bir örneğini kullandığını aklınızda bulundurun `IAuthorizationPolicyProvider` . Özel bir sağlayıcı kullanılacak tüm ilke adları için yetkilendirme ilkeleri sağlayamıyorsa, bu, bir yedekleme sağlayıcısına ertelemelidir. 

Örneğin, özel yaş ilkelerine ve daha geleneksel rol tabanlı ilke almaya ihtiyacı olan bir uygulamayı düşünün. Bu tür bir uygulama, özel bir yetkilendirme ilkesi sağlayıcısını şu şekilde kullanabilir:

* İlke adlarını ayrıştırmaya çalışır. 
* İlke adı bir yaş içermiyorsa farklı bir ilke sağlayıcısına (gibi) çağrı yapılır `DefaultAuthorizationPolicyProvider` .

`IAuthorizationPolicyProvider`Yukarıda gösterilen örnek uygulama, `DefaultAuthorizationPolicyProvider` oluşturucusunda bir yedekleme ilkesi sağlayıcısı oluşturularak (ilke adının beklenen ' minimum umage ' ve Age ile eşleşmemesi durumunda kullanılmak üzere) tarafından kullanılmak üzere güncelleştirilemeyebilir.

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

Daha sonra, `GetPolicyAsync` yöntemi null döndürmek yerine öğesini kullanacak şekilde yapılandırılabilir `BackupPolicyProvider` :

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a>Varsayılan ilke

Adlandırılmış Yetkilendirme İlkeleri sağlamaya ek olarak, bir `IAuthorizationPolicyProvider` `GetDefaultPolicyAsync` `[Authorize]` ilke adı belirtmeden öznitelikler için bir yetkilendirme ilkesi sağlamak üzere özel bir uygulama gerekir.

Çoğu durumda, bu yetkilendirme özniteliği yalnızca kimliği doğrulanmış bir kullanıcı gerektirir, bu nedenle gerekli ilkeyi öğesine bir çağrı ile yapabilirsiniz `RequireAuthenticatedUser` :

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

Özel bir tüm yönlere göre `IAuthorizationPolicyProvider` , bunu gerektiği şekilde özelleştirebilirsiniz. Bazı durumlarda, varsayılan ilkeyi bir geri yüklemeden almak istenebilir `IAuthorizationPolicyProvider` .

## <a name="fallback-policy"></a>Geri dönüş ilkesi

Özel `IAuthorizationPolicyProvider` olarak, `GetFallbackPolicyAsync` [ilkeler birleştirilirken](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) ve hiçbir ilke belirtilmediğinde kullanılan bir ilke sağlamak için isteğe bağlı olarak uygulanabilir. `GetFallbackPolicyAsync`Null olmayan bir ilke döndürürse, istek için hiçbir ilke belirtilmediğinde, döndürülen Ilke yetkilendirme ara yazılımı tarafından kullanılır.

Geri dönüş ilkesi gerekmiyorsa, sağlayıcı geri dönüş `null` sağlayıcısına dönebilir veya erteleyebilirsiniz:

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

<a name="ci"></a>

## <a name="use-a-custom-iauthorizationpolicyprovider"></a>Özel bir ıauthorizationpolicyprovider kullanın

Özel ilkeleri bir ile kullanmak için şunları yapmanız `IAuthorizationPolicyProvider` ***gerekir***:

* İlke tabanlı yetkilendirme `AuthorizationHandler` senaryolarında olduğu gibi, bağımlılık ekleme ( [ilke tabanlı yetkilendirme](xref:security/authorization/policies#authorization-handlers)bölümünde açıklanmıştır) ile uygun türleri kaydedin.
* `IAuthorizationPolicyProvider` `Startup.ConfigureServices` Varsayılan ilke sağlayıcısını değiştirmek için içindeki uygulamanın bağımlılık ekleme hizmeti koleksiyonuna özel türü kaydedin.

  ```csharp
  services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
  ```

`IAuthorizationPolicyProvider` [DotNet/Aspnetcore GitHub deposunda](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider)tüm özel bir örnek kullanılabilir.
