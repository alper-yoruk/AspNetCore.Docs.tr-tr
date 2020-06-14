---
title: ASP.NET Core 'de özel yetkilendirme Ilkesi sağlayıcıları
author: mjrousos
description: Bir ASP.NET Core uygulamasında özel bir ıauthorizationpolicyprovider kullanarak yetkilendirme ilkelerini dinamik olarak oluşturma hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 11/14/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/iauthorizationpolicyprovider
ms.openlocfilehash: c104de2903867b3257e5bde5b55dfcb256499746
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/13/2020
ms.locfileid: "84755800"
---
# <a name="custom-authorization-policy-providers-using-iauthorizationpolicyprovider-in-aspnet-core"></a><span data-ttu-id="c81b7-103">ASP.NET Core 'de ıauthorizationpolicyprovider kullanan özel yetkilendirme Ilkesi sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="c81b7-103">Custom Authorization Policy Providers using IAuthorizationPolicyProvider in ASP.NET Core</span></span> 

<span data-ttu-id="c81b7-104">, [Mike Rousos](https://github.com/mjrousos) tarafından</span><span class="sxs-lookup"><span data-stu-id="c81b7-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="c81b7-105">Genellikle [ilke tabanlı yetkilendirme](xref:security/authorization/policies)kullanılırken, ilkeler, `AuthorizationOptions.AddPolicy` yetkilendirme hizmeti yapılandırmasının bir parçası olarak çağırarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="c81b7-105">Typically when using [policy-based authorization](xref:security/authorization/policies), policies are registered by calling `AuthorizationOptions.AddPolicy` as part of authorization service configuration.</span></span> <span data-ttu-id="c81b7-106">Bazı senaryolarda, tüm yetkilendirme ilkelerini bu şekilde kaydetmek mümkün olmayabilir (veya istenebilir).</span><span class="sxs-lookup"><span data-stu-id="c81b7-106">In some scenarios, it may not be possible (or desirable) to register all authorization policies in this way.</span></span> <span data-ttu-id="c81b7-107">Bu durumlarda, yetkilendirme ilkelerinin nasıl sağlanmış olduğunu denetlemek için [özel `IAuthorizationPolicyProvider` ](#ci) ' i kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c81b7-107">In those cases, you can [use a custom `IAuthorizationPolicyProvider`](#ci) to control how authorization policies are supplied.</span></span>

<span data-ttu-id="c81b7-108">Özel bir [ıauthorizationpolicyprovider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) yararlı olabilecek senaryolara örnek olarak şunlar verilebilir:</span><span class="sxs-lookup"><span data-stu-id="c81b7-108">Examples of scenarios where a custom [IAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider) may be useful include:</span></span>

* <span data-ttu-id="c81b7-109">İlke değerlendirmesi sağlamak için bir dış hizmet kullanma.</span><span class="sxs-lookup"><span data-stu-id="c81b7-109">Using an external service to provide policy evaluation.</span></span>
* <span data-ttu-id="c81b7-110">Büyük bir ilke yelpazesi (örneğin, farklı oda numaraları veya yaşlar için) kullanarak, her bireysel yetkilendirme ilkesini bir çağrısıyla eklemek mantıklı değildir `AuthorizationOptions.AddPolicy` .</span><span class="sxs-lookup"><span data-stu-id="c81b7-110">Using a large range of policies (for different room numbers or ages, for example), so it doesn't make sense to add each individual authorization policy with an `AuthorizationOptions.AddPolicy` call.</span></span>
* <span data-ttu-id="c81b7-111">Dış veri kaynağındaki bilgilere (bir veritabanı gibi) göre veya başka bir mekanizmaya göre yetkilendirme gereksinimlerini dinamik olarak belirleyerek çalışma zamanında ilkeler oluşturma.</span><span class="sxs-lookup"><span data-stu-id="c81b7-111">Creating policies at runtime based on information in an external data source (like a database) or determining authorization requirements dynamically through another mechanism.</span></span>

<span data-ttu-id="c81b7-112">[Aspnetcore GitHub deposundan](https://github.com/dotnet/AspNetCore) [örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) .</span><span class="sxs-lookup"><span data-stu-id="c81b7-112">[View or download sample code](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider) from the [AspNetCore GitHub repository](https://github.com/dotnet/AspNetCore).</span></span> <span data-ttu-id="c81b7-113">DotNet/AspNetCore depo ZIP dosyasını indirin.</span><span class="sxs-lookup"><span data-stu-id="c81b7-113">Download the dotnet/AspNetCore repository ZIP file.</span></span> <span data-ttu-id="c81b7-114">Dosyayı sıkıştırmayı açın.</span><span class="sxs-lookup"><span data-stu-id="c81b7-114">Unzip the file.</span></span> <span data-ttu-id="c81b7-115">*Src/Security/Samples/CustomPolicyProvider* proje klasörüne gidin.</span><span class="sxs-lookup"><span data-stu-id="c81b7-115">Navigate to the *src/Security/samples/CustomPolicyProvider* project folder.</span></span>

## <a name="customize-policy-retrieval"></a><span data-ttu-id="c81b7-116">İlke almayı özelleştirme</span><span class="sxs-lookup"><span data-stu-id="c81b7-116">Customize policy retrieval</span></span>

<span data-ttu-id="c81b7-117">ASP.NET Core uygulamalar, `IAuthorizationPolicyProvider` yetkilendirme ilkelerini almak için arabirimin bir uygulamasını kullanır.</span><span class="sxs-lookup"><span data-stu-id="c81b7-117">ASP.NET Core apps use an implementation of the `IAuthorizationPolicyProvider` interface to retrieve authorization policies.</span></span> <span data-ttu-id="c81b7-118">Varsayılan olarak, [Defaultauthorizationpolicyprovider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) kaydedilir ve kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c81b7-118">By default, [DefaultAuthorizationPolicyProvider](/dotnet/api/microsoft.aspnetcore.authorization.defaultauthorizationpolicyprovider) is registered and used.</span></span> <span data-ttu-id="c81b7-119">`DefaultAuthorizationPolicyProvider``AuthorizationOptions`bir çağrıda sunulan ilkeleri döndürür `IServiceCollection.AddAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="c81b7-119">`DefaultAuthorizationPolicyProvider` returns policies from the `AuthorizationOptions` provided in an `IServiceCollection.AddAuthorization` call.</span></span>

<span data-ttu-id="c81b7-120">`IAuthorizationPolicyProvider`Uygulamanın [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısına farklı bir uygulama kaydederek bu davranışı özelleştirin.</span><span class="sxs-lookup"><span data-stu-id="c81b7-120">Customize this behavior by registering a different `IAuthorizationPolicyProvider` implementation in the app's [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> 

<span data-ttu-id="c81b7-121">`IAuthorizationPolicyProvider`Arabirim üç API içerir:</span><span class="sxs-lookup"><span data-stu-id="c81b7-121">The `IAuthorizationPolicyProvider` interface contains three APIs:</span></span>

* <span data-ttu-id="c81b7-122">[Getpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) , belirli bir ad için yetkilendirme ilkesi döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="c81b7-122">[GetPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getpolicyasync#Microsoft_AspNetCore_Authorization_IAuthorizationPolicyProvider_GetPolicyAsync_System_String_) returns an authorization policy for a given name.</span></span>
* <span data-ttu-id="c81b7-123">[Getdefaultpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) varsayılan yetkilendirme ilkesini döndürür ( `[Authorize]` bir ilke belirtilmeden öznitelikler için kullanılan ilke).</span><span class="sxs-lookup"><span data-stu-id="c81b7-123">[GetDefaultPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getdefaultpolicyasync) returns the default authorization policy (the policy used for `[Authorize]` attributes without a policy specified).</span></span> 
* <span data-ttu-id="c81b7-124">[Getfallbackpolicyasync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) , geri dönüş yetkilendirme ilkesini döndürür (Ilke belirtilmediğinde yetkilendirme ara yazılımı tarafından kullanılan ilke).</span><span class="sxs-lookup"><span data-stu-id="c81b7-124">[GetFallbackPolicyAsync](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationpolicyprovider.getfallbackpolicyasync) returns the fallback authorization policy (the policy used by the Authorization Middleware when no policy is specified).</span></span> 

<span data-ttu-id="c81b7-125">Bu API 'Leri uygulayarak, yetkilendirme ilkelerinin nasıl sağlandığını özelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c81b7-125">By implementing these APIs, you can customize how authorization policies are provided.</span></span>

## <a name="parameterized-authorize-attribute-example"></a><span data-ttu-id="c81b7-126">Parametreli yetkilendirme özniteliği örneği</span><span class="sxs-lookup"><span data-stu-id="c81b7-126">Parameterized authorize attribute example</span></span>

<span data-ttu-id="c81b7-127">Tek senaryo, `IAuthorizationPolicyProvider` `[Authorize]` gereksinimleri bir parametreye bağlı özel öznitelikleri etkinleştirir.</span><span class="sxs-lookup"><span data-stu-id="c81b7-127">One scenario where `IAuthorizationPolicyProvider` is useful is enabling custom `[Authorize]` attributes whose requirements depend on a parameter.</span></span> <span data-ttu-id="c81b7-128">Örneğin, [ilke tabanlı yetkilendirme](xref:security/authorization/policies) belgelerinde, örnek olarak yaş tabanlı ("AtLeast21") ilkesi kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="c81b7-128">For example, in [policy-based authorization](xref:security/authorization/policies) documentation, an age-based (“AtLeast21”) policy was used as a sample.</span></span> <span data-ttu-id="c81b7-129">Bir uygulamadaki farklı denetleyici eylemleri *farklı* yaştaki kullanıcılar için kullanılabilir hale getirilse, çok sayıda farklı yaş tabanlı ilke olması faydalı olabilir.</span><span class="sxs-lookup"><span data-stu-id="c81b7-129">If different controller actions in an app should be made available to users of *different* ages, it might be useful to have many different age-based policies.</span></span> <span data-ttu-id="c81b7-130">Uygulamanın ihtiyaç duyduğu tüm farklı yaş tabanlı ilkeleri kaydetmek yerine `AuthorizationOptions` , ilkeleri özel olarak dinamik bir şekilde oluşturabilirsiniz `IAuthorizationPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="c81b7-130">Instead of registering all the different age-based policies that the application will need in `AuthorizationOptions`, you can generate the policies dynamically with a custom `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="c81b7-131">İlkeleri kullanmak daha kolay hale getirmek için, gibi özel yetkilendirme özniteliğiyle eylemlere not ekleyebilirsiniz `[MinimumAgeAuthorize(20)]` .</span><span class="sxs-lookup"><span data-stu-id="c81b7-131">To make using the policies easier, you can annotate actions with custom authorization attribute like `[MinimumAgeAuthorize(20)]`.</span></span>

## <a name="custom-authorization-attributes"></a><span data-ttu-id="c81b7-132">Özel yetkilendirme öznitelikleri</span><span class="sxs-lookup"><span data-stu-id="c81b7-132">Custom Authorization attributes</span></span>

<span data-ttu-id="c81b7-133">Yetkilendirme ilkeleri adlarıyla tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="c81b7-133">Authorization policies are identified by their names.</span></span> <span data-ttu-id="c81b7-134">`MinimumAgeAuthorizeAttribute`Daha önce açıklanan özel, bağımsız değişkenleri ilgili yetkilendirme ilkesini almak için kullanılabilecek bir dizeye eşlemenize ihtiyaç duyuyor.</span><span class="sxs-lookup"><span data-stu-id="c81b7-134">The custom `MinimumAgeAuthorizeAttribute` described previously needs to map arguments into a string that can be used to retrieve the corresponding authorization policy.</span></span> <span data-ttu-id="c81b7-135">Bunu öğesinden türeterek `AuthorizeAttribute` ve özelliği sararak yaparak yapabilirsiniz `Age` `AuthorizeAttribute.Policy` .</span><span class="sxs-lookup"><span data-stu-id="c81b7-135">You can do this by deriving from `AuthorizeAttribute` and making the `Age` property wrap the `AuthorizeAttribute.Policy` property.</span></span>

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

<span data-ttu-id="c81b7-136">Bu öznitelik türü, `Policy` sabit kodlanmış ön eki ( `"MinimumAge"` ) ve Oluşturucu aracılığıyla geçirilen bir tamsayıyı temel alan bir dizeye sahiptir.</span><span class="sxs-lookup"><span data-stu-id="c81b7-136">This attribute type has a `Policy` string based on the hard-coded prefix (`"MinimumAge"`) and an integer passed in via the constructor.</span></span>

<span data-ttu-id="c81b7-137">`Authorize`Bir parametre olarak bir tamsayı almak dışında, diğer özniteliklerle aynı şekilde eylemlere uygulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c81b7-137">You can apply it to actions in the same way as other `Authorize` attributes except that it takes an integer as a parameter.</span></span>

```csharp
[MinimumAgeAuthorize(10)]
public IActionResult RequiresMinimumAge10()
```

## <a name="custom-iauthorizationpolicyprovider"></a><span data-ttu-id="c81b7-138">Özel ıauthorizationpolicyprovider</span><span class="sxs-lookup"><span data-stu-id="c81b7-138">Custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="c81b7-139">Özel, `MinimumAgeAuthorizeAttribute` istenen en düşük yaş için yetkilendirme ilkeleri istemeyi kolaylaştırır.</span><span class="sxs-lookup"><span data-stu-id="c81b7-139">The custom `MinimumAgeAuthorizeAttribute` makes it easy to request authorization policies for any minimum age desired.</span></span> <span data-ttu-id="c81b7-140">Çözecek bir sonraki sorun, yetkilendirme ilkelerinin tüm bu farklı yaşlar için kullanılabilir olduğundan emin olmanızı sağlar.</span><span class="sxs-lookup"><span data-stu-id="c81b7-140">The next problem to solve is making sure that authorization policies are available for all of those different ages.</span></span> <span data-ttu-id="c81b7-141">Bunun yararlı olduğu yerdir `IAuthorizationPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="c81b7-141">This is where an `IAuthorizationPolicyProvider` is useful.</span></span>

<span data-ttu-id="c81b7-142">Kullanırken `MinimumAgeAuthorizationAttribute` , yetkilendirme ilkesi adları, bu sayede, özel olarak `"MinimumAge" + Age` `IAuthorizationPolicyProvider` Yetkilendirme İlkeleri oluşturması gerekir.</span><span class="sxs-lookup"><span data-stu-id="c81b7-142">When using `MinimumAgeAuthorizationAttribute`, the authorization policy names will follow the pattern `"MinimumAge" + Age`, so the custom `IAuthorizationPolicyProvider` should generate authorization policies by:</span></span>

* <span data-ttu-id="c81b7-143">İlke adından yaş ayrıştırılıyor.</span><span class="sxs-lookup"><span data-stu-id="c81b7-143">Parsing the age from the policy name.</span></span>
* <span data-ttu-id="c81b7-144">`AuthorizationPolicyBuilder`Yeni bir oluşturmak için kullanarak`AuthorizationPolicy`</span><span class="sxs-lookup"><span data-stu-id="c81b7-144">Using `AuthorizationPolicyBuilder` to create a new `AuthorizationPolicy`</span></span>
* <span data-ttu-id="c81b7-145">Bu ve aşağıdaki örneklerde, kullanıcının kimliğinin bir tanımlama bilgisi aracılığıyla doğrulandığını varsayacaktır.</span><span class="sxs-lookup"><span data-stu-id="c81b7-145">In this and following examples it will be assumed that the user is authenticated via a cookie.</span></span> <span data-ttu-id="c81b7-146">`AuthorizationPolicyBuilder`En az bir yetkilendirme düzeni adıyla oluşturulmalıdır ya da her zaman başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="c81b7-146">The `AuthorizationPolicyBuilder` should either be constructed with at least one authorization scheme name or always succeed.</span></span> <span data-ttu-id="c81b7-147">Aksi takdirde, kullanıcıya nasıl bir sınama sağlayacaksınız ve bir özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="c81b7-147">Otherwise there is no information on how to provide a challenge to the user and an exception will be thrown.</span></span>
* <span data-ttu-id="c81b7-148">Yaş temelinde ilkeye gereksinimler ekleme `AuthorizationPolicyBuilder.AddRequirements` .</span><span class="sxs-lookup"><span data-stu-id="c81b7-148">Adding requirements to the policy based on the age with `AuthorizationPolicyBuilder.AddRequirements`.</span></span> <span data-ttu-id="c81b7-149">Diğer senaryolarda, `RequireClaim` `RequireRole` veya `RequireUserName` yerine kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c81b7-149">In other scenarios, you might use `RequireClaim`, `RequireRole`, or `RequireUserName` instead.</span></span>

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

## <a name="multiple-authorization-policy-providers"></a><span data-ttu-id="c81b7-150">Birden çok yetkilendirme ilkesi sağlayıcısı</span><span class="sxs-lookup"><span data-stu-id="c81b7-150">Multiple authorization policy providers</span></span>

<span data-ttu-id="c81b7-151">Özel uygulamalar kullanırken `IAuthorizationPolicyProvider` ASP.NET Core yalnızca bir örneğini kullandığını aklınızda bulundurun `IAuthorizationPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="c81b7-151">When using custom `IAuthorizationPolicyProvider` implementations, keep in mind that ASP.NET Core only uses one instance of `IAuthorizationPolicyProvider`.</span></span> <span data-ttu-id="c81b7-152">Özel bir sağlayıcı kullanılacak tüm ilke adları için yetkilendirme ilkeleri sağlayamıyorsa, bu, bir yedekleme sağlayıcısına ertelemelidir.</span><span class="sxs-lookup"><span data-stu-id="c81b7-152">If a custom provider isn't able to provide authorization policies for all policy names that will be used, it should defer to a backup provider.</span></span> 

<span data-ttu-id="c81b7-153">Örneğin, özel yaş ilkelerine ve daha geleneksel rol tabanlı ilke almaya ihtiyacı olan bir uygulamayı düşünün.</span><span class="sxs-lookup"><span data-stu-id="c81b7-153">For example, consider an application that needs both custom age policies and more traditional role-based policy retrieval.</span></span> <span data-ttu-id="c81b7-154">Bu tür bir uygulama, özel bir yetkilendirme ilkesi sağlayıcısını şu şekilde kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="c81b7-154">Such an app could use a custom authorization policy provider that:</span></span>

* <span data-ttu-id="c81b7-155">İlke adlarını ayrıştırmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="c81b7-155">Attempts to parse policy names.</span></span> 
* <span data-ttu-id="c81b7-156">İlke adı bir yaş içermiyorsa farklı bir ilke sağlayıcısına (gibi) çağrı yapılır `DefaultAuthorizationPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="c81b7-156">Calls into a different policy provider (like `DefaultAuthorizationPolicyProvider`) if the policy name doesn't contain an age.</span></span>

<span data-ttu-id="c81b7-157">`IAuthorizationPolicyProvider`Yukarıda gösterilen örnek uygulama, `DefaultAuthorizationPolicyProvider` oluşturucusunda bir yedekleme ilkesi sağlayıcısı oluşturularak (ilke adının beklenen ' minimum umage ' ve Age ile eşleşmemesi durumunda kullanılmak üzere) tarafından kullanılmak üzere güncelleştirilemeyebilir.</span><span class="sxs-lookup"><span data-stu-id="c81b7-157">The example `IAuthorizationPolicyProvider` implementation shown above can be updated to use the `DefaultAuthorizationPolicyProvider` by creating a backup policy provider in its constructor (to be used in case the policy name doesn't match its expected pattern of 'MinimumAge' + age).</span></span>

```csharp
private DefaultAuthorizationPolicyProvider BackupPolicyProvider { get; }

public MinimumAgePolicyProvider(IOptions<AuthorizationOptions> options)
{
    // ASP.NET Core only uses one authorization policy provider, so if the custom implementation
    // doesn't handle all policies it should fall back to an alternate provider.
    BackupPolicyProvider = new DefaultAuthorizationPolicyProvider(options);
}
```

<span data-ttu-id="c81b7-158">Daha sonra, `GetPolicyAsync` yöntemi null döndürmek yerine öğesini kullanacak şekilde yapılandırılabilir `BackupPolicyProvider` :</span><span class="sxs-lookup"><span data-stu-id="c81b7-158">Then, the `GetPolicyAsync` method can be updated to use the `BackupPolicyProvider` instead of returning null:</span></span>

```csharp
...
return BackupPolicyProvider.GetPolicyAsync(policyName);
```

## <a name="default-policy"></a><span data-ttu-id="c81b7-159">Varsayılan ilke</span><span class="sxs-lookup"><span data-stu-id="c81b7-159">Default policy</span></span>

<span data-ttu-id="c81b7-160">Adlandırılmış Yetkilendirme İlkeleri sağlamaya ek olarak, bir `IAuthorizationPolicyProvider` `GetDefaultPolicyAsync` `[Authorize]` ilke adı belirtmeden öznitelikler için bir yetkilendirme ilkesi sağlamak üzere özel bir uygulama gerekir.</span><span class="sxs-lookup"><span data-stu-id="c81b7-160">In addition to providing named authorization policies, a custom `IAuthorizationPolicyProvider` needs to implement `GetDefaultPolicyAsync` to provide an authorization policy for `[Authorize]` attributes without a policy name specified.</span></span>

<span data-ttu-id="c81b7-161">Çoğu durumda, bu yetkilendirme özniteliği yalnızca kimliği doğrulanmış bir kullanıcı gerektirir, bu nedenle gerekli ilkeyi öğesine bir çağrı ile yapabilirsiniz `RequireAuthenticatedUser` :</span><span class="sxs-lookup"><span data-stu-id="c81b7-161">In many cases, this authorization attribute only requires an authenticated user, so you can make the necessary policy with a call to `RequireAuthenticatedUser`:</span></span>

```csharp
public Task<AuthorizationPolicy> GetDefaultPolicyAsync() => 
    Task.FromResult(new AuthorizationPolicyBuilder(CookieAuthenticationDefaults.AuthenticationScheme).RequireAuthenticatedUser().Build());
```

<span data-ttu-id="c81b7-162">Özel bir tüm yönlere göre `IAuthorizationPolicyProvider` , bunu gerektiği şekilde özelleştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c81b7-162">As with all aspects of a custom `IAuthorizationPolicyProvider`, you can customize this, as needed.</span></span> <span data-ttu-id="c81b7-163">Bazı durumlarda, varsayılan ilkeyi bir geri yüklemeden almak istenebilir `IAuthorizationPolicyProvider` .</span><span class="sxs-lookup"><span data-stu-id="c81b7-163">In some cases, it may be desirable to retrieve the default policy from a fallback `IAuthorizationPolicyProvider`.</span></span>

## <a name="fallback-policy"></a><span data-ttu-id="c81b7-164">Geri dönüş ilkesi</span><span class="sxs-lookup"><span data-stu-id="c81b7-164">Fallback policy</span></span>

<span data-ttu-id="c81b7-165">Özel `IAuthorizationPolicyProvider` olarak, `GetFallbackPolicyAsync` [ilkeler birleştirilirken](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) ve hiçbir ilke belirtilmediğinde kullanılan bir ilke sağlamak için isteğe bağlı olarak uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="c81b7-165">A custom `IAuthorizationPolicyProvider` can optionally implement `GetFallbackPolicyAsync` to provide a policy that's used when [combining policies](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicy.combine) and when no policies are specified.</span></span> <span data-ttu-id="c81b7-166">`GetFallbackPolicyAsync`Null olmayan bir ilke döndürürse, istek için hiçbir ilke belirtilmediğinde, döndürülen Ilke yetkilendirme ara yazılımı tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="c81b7-166">If `GetFallbackPolicyAsync` returns a non-null policy, the returned policy is used by the Authorization Middleware when no policies are specified for the request.</span></span>

<span data-ttu-id="c81b7-167">Geri dönüş ilkesi gerekmiyorsa, sağlayıcı geri dönüş `null` sağlayıcısına dönebilir veya erteleyebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="c81b7-167">If no fallback policy is required, the provider can return `null` or defer to the fallback provider:</span></span>

```csharp
public Task<AuthorizationPolicy> GetFallbackPolicyAsync() => 
    Task.FromResult<AuthorizationPolicy>(null);
```

<a name="ci"></a>

## <a name="use-a-custom-iauthorizationpolicyprovider"></a><span data-ttu-id="c81b7-168">Özel bir ıauthorizationpolicyprovider kullanın</span><span class="sxs-lookup"><span data-stu-id="c81b7-168">Use a custom IAuthorizationPolicyProvider</span></span>

<span data-ttu-id="c81b7-169">Özel ilkeleri bir ile kullanmak için şunları yapmanız `IAuthorizationPolicyProvider` ***gerekir***:</span><span class="sxs-lookup"><span data-stu-id="c81b7-169">To use custom policies from an `IAuthorizationPolicyProvider`, you ***must***:</span></span>

* <span data-ttu-id="c81b7-170">İlke tabanlı yetkilendirme `AuthorizationHandler` senaryolarında olduğu gibi, bağımlılık ekleme ( [ilke tabanlı yetkilendirme](xref:security/authorization/policies#authorization-handlers)bölümünde açıklanmıştır) ile uygun türleri kaydedin.</span><span class="sxs-lookup"><span data-stu-id="c81b7-170">Register the appropriate `AuthorizationHandler` types with dependency injection (described in [policy-based authorization](xref:security/authorization/policies#authorization-handlers)), as with all policy-based authorization scenarios.</span></span>
* <span data-ttu-id="c81b7-171">`IAuthorizationPolicyProvider` `Startup.ConfigureServices` Varsayılan ilke sağlayıcısını değiştirmek için içindeki uygulamanın bağımlılık ekleme hizmeti koleksiyonuna özel türü kaydedin.</span><span class="sxs-lookup"><span data-stu-id="c81b7-171">Register the custom `IAuthorizationPolicyProvider` type in the app's dependency injection service collection in `Startup.ConfigureServices` to replace the default policy provider.</span></span>

  ```csharp
  services.AddSingleton<IAuthorizationPolicyProvider, MinimumAgePolicyProvider>();
  ```

<span data-ttu-id="c81b7-172">`IAuthorizationPolicyProvider` [DotNet/Aspnetcore GitHub deposunda](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider)tüm özel bir örnek kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="c81b7-172">A complete custom `IAuthorizationPolicyProvider` sample is available in the [dotnet/aspnetcore GitHub repository](https://github.com/dotnet/aspnetcore/tree/v3.1.3/src/Security/samples/CustomPolicyProvider).</span></span>
