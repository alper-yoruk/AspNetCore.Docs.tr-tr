---
title: "Kimlik doğrulamasını :::no-loc(Identity)::: ASP.NET Core ve 2,0 'e geçirin"
author: scottaddie
description: "Bu makalede ASP.NET Core 1. x kimlik doğrulamasını ve ASP.NET Core 2,0 ' ye geçirmek için en yaygın adımlar özetlenmektedir :::no-loc(Identity)::: ."
ms.author: scaddie
ms.date: 06/21/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: cad7582670013661f5fcbfbebad923f0f092462e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057186"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core-20"></a><span data-ttu-id="b52aa-103">Kimlik doğrulamasını :::no-loc(Identity)::: ASP.NET Core ve 2,0 'e geçirin</span><span class="sxs-lookup"><span data-stu-id="b52aa-103">Migrate authentication and :::no-loc(Identity)::: to ASP.NET Core 2.0</span></span>

<span data-ttu-id="b52aa-104">[Scott Ade](https://github.com/scottaddie) ve [Hao Kung](https://github.com/HaoK) tarafından</span><span class="sxs-lookup"><span data-stu-id="b52aa-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="b52aa-105">ASP.NET Core 2,0, kimlik doğrulaması için yeni bir modele sahiptir ve [:::no-loc(Identity):::](xref:security/authentication/identity) Hizmetleri kullanarak yapılandırmayı basitleştirir.</span><span class="sxs-lookup"><span data-stu-id="b52aa-105">ASP.NET Core 2.0 has a new model for authentication and [:::no-loc(Identity):::](xref:security/authentication/identity) that simplifies configuration by using services.</span></span> <span data-ttu-id="b52aa-106">Kimlik doğrulaması kullanan veya :::no-loc(Identity)::: yeni modeli aşağıda özetlenen şekilde kullanacak şekilde güncelleştirilebilen 1. x uygulama ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="b52aa-106">ASP.NET Core 1.x applications that use authentication or :::no-loc(Identity)::: can be updated to use the new model as outlined below.</span></span>

## <a name="update-namespaces"></a><span data-ttu-id="b52aa-107">Ad alanlarını güncelleştirme</span><span class="sxs-lookup"><span data-stu-id="b52aa-107">Update namespaces</span></span>

<span data-ttu-id="b52aa-108">1. x içinde `:::no-loc(Identity):::Role` ve `:::no-loc(Identity):::User` ad alanında bulunan sınıflar `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="b52aa-108">In 1.x, classes such `:::no-loc(Identity):::Role` and `:::no-loc(Identity):::User` were found in the `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` namespace.</span></span>

<span data-ttu-id="b52aa-109">2,0 ' de, <xref:Microsoft.AspNetCore.:::no-loc(Identity):::> ad alanı bu tür sınıfların birkaçı için yeni giriş haline geldi.</span><span class="sxs-lookup"><span data-stu-id="b52aa-109">In 2.0, the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::> namespace became the new home for several of such classes.</span></span> <span data-ttu-id="b52aa-110">Varsayılan :::no-loc(Identity)::: kodla, etkilenen sınıflar `ApplicationUser` ve içerir `Startup` .</span><span class="sxs-lookup"><span data-stu-id="b52aa-110">With the default :::no-loc(Identity)::: code, affected classes include `ApplicationUser` and `Startup`.</span></span> <span data-ttu-id="b52aa-111">`using`Etkilenen başvuruları çözümlemek için deyimlerinizi ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b52aa-111">Adjust your `using` statements to resolve the affected references.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="b52aa-112">Kimlik doğrulama ara yazılımı ve Hizmetleri</span><span class="sxs-lookup"><span data-stu-id="b52aa-112">Authentication Middleware and services</span></span>

<span data-ttu-id="b52aa-113">1. x projelerinde kimlik doğrulaması, ara yazılım aracılığıyla yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="b52aa-113">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="b52aa-114">Desteklemek istediğiniz her kimlik doğrulama düzeni için bir ara yazılım yöntemi çağırılır.</span><span class="sxs-lookup"><span data-stu-id="b52aa-114">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="b52aa-115">Aşağıdaki 1. x örneği, Startup.cs içinde Facebook kimlik doğrulamasını yapılandırır :::no-loc(Identity)::: : *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="b52aa-115">The following 1.x example configures Facebook authentication with :::no-loc(Identity)::: in *Startup.cs* :</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
            .AddEntityFrameworkStores<ApplicationDbContext>();
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory)
{
    app.Use:::no-loc(Identity):::();
    app.UseFacebookAuthentication(new FacebookOptions {
        AppId = Configuration["auth:facebook:appid"],
        AppSecret = Configuration["auth:facebook:appsecret"]
    });
}
```

<span data-ttu-id="b52aa-116">2,0 projesinde, kimlik doğrulaması hizmetler aracılığıyla yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="b52aa-116">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="b52aa-117">Her kimlik doğrulama şeması, `ConfigureServices` *Startup.cs* yöntemine kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="b52aa-117">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs* .</span></span> <span data-ttu-id="b52aa-118">`Use:::no-loc(Identity):::`Yöntemi ile değiştirilmiştir `UseAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="b52aa-118">The `Use:::no-loc(Identity):::` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="b52aa-119">Aşağıdaki 2,0 örneği, Startup.cs içinde Facebook kimlik doğrulamasını yapılandırır :::no-loc(Identity)::: : *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="b52aa-119">The following 2.0 example configures Facebook authentication with :::no-loc(Identity)::: in *Startup.cs* :</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
            .AddEntityFrameworkStores<ApplicationDbContext>();

    // If you want to tweak :::no-loc(Identity)::: :::no-loc(cookie):::s, they're no longer part of :::no-loc(Identity):::Options.
    services.ConfigureApplication:::no-loc(Cookie):::(options => options.LoginPath = "/Account/LogIn");
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
}

public void Configure(IApplicationBuilder app, ILoggerFactory loggerfactory) {
    app.UseAuthentication();
}
```

<span data-ttu-id="b52aa-120">`UseAuthentication`Yöntemi otomatik kimlik doğrulamasından ve uzak kimlik doğrulama isteklerinin işlenmesinden sorumlu olan tek bir kimlik doğrulama ara yazılım bileşeni ekler.</span><span class="sxs-lookup"><span data-stu-id="b52aa-120">The `UseAuthentication` method adds a single authentication middleware component, which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="b52aa-121">Tek, ortak bir ara yazılım bileşeniyle tüm bireysel ara yazılım bileşenlerini değiştirir.</span><span class="sxs-lookup"><span data-stu-id="b52aa-121">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="b52aa-122">Her bir büyük kimlik doğrulama şeması için 2,0 geçiş yönergeleri aşağıda verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="b52aa-122">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="no-loccookie-based-authentication"></a><span data-ttu-id="b52aa-123">:::no-loc(Cookie):::tabanlı kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="b52aa-123">:::no-loc(Cookie):::-based authentication</span></span>

<span data-ttu-id="b52aa-124">Aşağıdaki iki seçenekten birini seçin ve *Startup.cs* 'de gerekli değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="b52aa-124">Select one of the two options below, and make the necessary changes in *Startup.cs* :</span></span>

1. <span data-ttu-id="b52aa-125">:::no-loc(cookie):::İle s kullanın:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="b52aa-125">Use :::no-loc(cookie):::s with :::no-loc(Identity):::</span></span>
    - <span data-ttu-id="b52aa-126">`Use:::no-loc(Identity):::`Yönteminde ile değiştirin `UseAuthentication` `Configure` :</span><span class="sxs-lookup"><span data-stu-id="b52aa-126">Replace `Use:::no-loc(Identity):::` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="b52aa-127">`Add:::no-loc(Identity):::` `ConfigureServices` Kimlik doğrulama hizmetlerini eklemek için yöntemindeki yöntemi çağırın :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="b52aa-127">Invoke the `Add:::no-loc(Identity):::` method in the `ConfigureServices` method to add the :::no-loc(cookie)::: authentication services.</span></span>
    - <span data-ttu-id="b52aa-128">İsteğe bağlı olarak, `ConfigureApplication:::no-loc(Cookie):::` `ConfigureExternal:::no-loc(Cookie):::` `ConfigureServices` ayarları ince ayar için yöntemi içindeki or metodunu çağırın :::no-loc(Identity)::: :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="b52aa-128">Optionally, invoke the `ConfigureApplication:::no-loc(Cookie):::` or `ConfigureExternal:::no-loc(Cookie):::` method in the `ConfigureServices` method to tweak the :::no-loc(Identity)::: :::no-loc(cookie)::: settings.</span></span>

        ```csharp
        services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplication:::no-loc(Cookie):::(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="b52aa-129">Şunu :::no-loc(cookie)::: olmadan s kullan :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="b52aa-129">Use :::no-loc(cookie):::s without :::no-loc(Identity):::</span></span>
    - <span data-ttu-id="b52aa-130">`Use:::no-loc(Cookie):::Authentication`Yöntemindeki yöntem çağrısını `Configure` ile değiştirin `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="b52aa-130">Replace the `Use:::no-loc(Cookie):::Authentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="b52aa-131">`AddAuthentication` `Add:::no-loc(Cookie):::` Yöntemi içindeki ve yöntemlerini çağırın `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b52aa-131">Invoke the `AddAuthentication` and `Add:::no-loc(Cookie):::` methods in the `ConfigureServices` method:</span></span>

        ```csharp
        // If you don't want the :::no-loc(cookie)::: to be automatically authenticated and assigned to HttpContext.User,
        // remove the :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme parameter passed to AddAuthentication.
        services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme)
                .Add:::no-loc(Cookie):::(options =>
                {
                    options.LoginPath = "/Account/LogIn";
                    options.LogoutPath = "/Account/LogOff";
                });
        ```

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="b52aa-132">JWT taşıyıcı kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="b52aa-132">JWT Bearer Authentication</span></span>

<span data-ttu-id="b52aa-133">*Startup.cs* içinde aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="b52aa-133">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="b52aa-134">`UseJwtBearerAuthentication`Yöntemindeki yöntem çağrısını `Configure` ile değiştirin `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="b52aa-134">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="b52aa-135">Yönteminde `AddJwtBearer` yöntemi çağırın `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b52aa-135">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="b52aa-136">Bu kod parçacığı kullanmaz :::no-loc(Identity)::: , bu nedenle varsayılan şema yöntemine geçirerek ayarlanmalıdır `JwtBearerDefaults.AuthenticationScheme` `AddAuthentication` .</span><span class="sxs-lookup"><span data-stu-id="b52aa-136">This code snippet doesn't use :::no-loc(Identity):::, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="b52aa-137">OpenID Connect (OıDC) kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="b52aa-137">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="b52aa-138">*Startup.cs* içinde aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="b52aa-138">Make the following changes in *Startup.cs* :</span></span>

- <span data-ttu-id="b52aa-139">`UseOpenIdConnectAuthentication`Yöntemindeki yöntem çağrısını `Configure` ile değiştirin `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="b52aa-139">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="b52aa-140">Yönteminde `AddOpenIdConnect` yöntemi çağırın `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b52aa-140">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(options =>
    {
        options.DefaultScheme = :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
    })
    .Add:::no-loc(Cookie):::()
    .AddOpenIdConnect(options =>
    {
        options.Authority = Configuration["auth:oidc:authority"];
        options.ClientId = Configuration["auth:oidc:clientid"];
    });
    ```

- <span data-ttu-id="b52aa-141">`PostLogoutRedirectUri` `OpenIdConnectOptions` Eylemdeki özelliğini ile değiştirin `SignedOutRedirectUri` :</span><span class="sxs-lookup"><span data-stu-id="b52aa-141">Replace the `PostLogoutRedirectUri` property in the `OpenIdConnectOptions` action with `SignedOutRedirectUri`:</span></span>

    ```csharp
    .AddOpenIdConnect(options =>
    {
        options.SignedOutRedirectUri = "https://contoso.com";
    });
    ```
    
### <a name="facebook-authentication"></a><span data-ttu-id="b52aa-142">Facebook kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="b52aa-142">Facebook authentication</span></span>

<span data-ttu-id="b52aa-143">*Startup.cs* içinde aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="b52aa-143">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="b52aa-144">`UseFacebookAuthentication`Yöntemindeki yöntem çağrısını `Configure` ile değiştirin `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="b52aa-144">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="b52aa-145">Yönteminde `AddFacebook` yöntemi çağırın `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b52aa-145">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="b52aa-146">Google kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="b52aa-146">Google authentication</span></span>

<span data-ttu-id="b52aa-147">*Startup.cs* içinde aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="b52aa-147">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="b52aa-148">`UseGoogleAuthentication`Yöntemindeki yöntem çağrısını `Configure` ile değiştirin `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="b52aa-148">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="b52aa-149">Yönteminde `AddGoogle` yöntemi çağırın `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b52aa-149">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="b52aa-150">Microsoft Hesabı kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="b52aa-150">Microsoft Account authentication</span></span>

<span data-ttu-id="b52aa-151">Microsoft hesabı kimlik doğrulaması hakkında daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/14455)bakın.</span><span class="sxs-lookup"><span data-stu-id="b52aa-151">For more information on Microsoft account authentication, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/14455).</span></span>

<span data-ttu-id="b52aa-152">*Startup.cs* içinde aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="b52aa-152">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="b52aa-153">`UseMicrosoftAccountAuthentication`Yöntemindeki yöntem çağrısını `Configure` ile değiştirin `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="b52aa-153">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="b52aa-154">Yönteminde `AddMicrosoftAccount` yöntemi çağırın `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b52aa-154">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="b52aa-155">Twitter kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="b52aa-155">Twitter authentication</span></span>

<span data-ttu-id="b52aa-156">*Startup.cs* içinde aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="b52aa-156">Make the following changes in *Startup.cs* :</span></span>
- <span data-ttu-id="b52aa-157">`UseTwitterAuthentication`Yöntemindeki yöntem çağrısını `Configure` ile değiştirin `UseAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="b52aa-157">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="b52aa-158">Yönteminde `AddTwitter` yöntemi çağırın `ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b52aa-158">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="b52aa-159">Varsayılan kimlik doğrulama düzenlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="b52aa-159">Setting default authentication schemes</span></span>

<span data-ttu-id="b52aa-160">1. x içinde, `AutomaticAuthenticate` `AutomaticChallenge` [authenticationoptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) temel sınıfının ve özellikleri tek bir kimlik doğrulama düzeninde ayarlamaya yöneliktir.</span><span class="sxs-lookup"><span data-stu-id="b52aa-160">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="b52aa-161">Bunu zorlayacağından iyi bir yol yoktu.</span><span class="sxs-lookup"><span data-stu-id="b52aa-161">There was no good way to enforce this.</span></span>

<span data-ttu-id="b52aa-162">2,0 ' de, bu iki özellik ayrı bir örnekteki özellikler olarak kaldırılmıştır `AuthenticationOptions` .</span><span class="sxs-lookup"><span data-stu-id="b52aa-162">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="b52aa-163">`AddAuthentication`Startup.cs yöntemi içinde yöntem çağrısında yapılandırılabilirler `ConfigureServices` : *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="b52aa-163">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs* :</span></span>

```csharp
services.AddAuthentication(:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="b52aa-164">Yukarıdaki kod parçacığında, varsayılan düzen `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (" :::no-loc(Cookie)::: s") olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="b52aa-164">In the preceding code snippet, the default scheme is set to `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` (":::no-loc(Cookie):::s").</span></span>

<span data-ttu-id="b52aa-165">Alternatif olarak, birden `AddAuthentication` fazla özellik ayarlamak için yönteminin aşırı yüklenmiş bir sürümünü kullanın.</span><span class="sxs-lookup"><span data-stu-id="b52aa-165">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="b52aa-166">Aşağıdaki aşırı yüklenmiş yöntem örneğinde, varsayılan düzen olarak ayarlanır `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme` .</span><span class="sxs-lookup"><span data-stu-id="b52aa-166">In the following overloaded method example, the default scheme is set to `:::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="b52aa-167">Kimlik doğrulama düzeni, farklı `[Authorize]` özniteliklerde veya yetkilendirme ilkeleriniz içinde de belirtilebilir.</span><span class="sxs-lookup"><span data-stu-id="b52aa-167">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = :::no-loc(Cookie):::AuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="b52aa-168">Aşağıdaki koşullardan biri doğru ise, 2,0 'de varsayılan bir düzen tanımlayın:</span><span class="sxs-lookup"><span data-stu-id="b52aa-168">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="b52aa-169">Kullanıcının otomatik olarak oturum açabilmesi istiyorsunuz</span><span class="sxs-lookup"><span data-stu-id="b52aa-169">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="b52aa-170">`[Authorize]`Şemaları belirtmeden özniteliği veya yetkilendirme ilkelerini kullanın</span><span class="sxs-lookup"><span data-stu-id="b52aa-170">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="b52aa-171">Bu kural için bir özel durum `Add:::no-loc(Identity):::` yöntemidir.</span><span class="sxs-lookup"><span data-stu-id="b52aa-171">An exception to this rule is the `Add:::no-loc(Identity):::` method.</span></span> <span data-ttu-id="b52aa-172">Bu yöntem :::no-loc(cookie)::: sizin için ekler ve varsayılan kimlik doğrulama ve zorluk düzenlerini uygulamaya ayarlar :::no-loc(cookie)::: `:::no-loc(Identity):::Constants.ApplicationScheme` .</span><span class="sxs-lookup"><span data-stu-id="b52aa-172">This method adds :::no-loc(cookie):::s for you and sets the default authenticate and challenge schemes to the application :::no-loc(cookie)::: `:::no-loc(Identity):::Constants.ApplicationScheme`.</span></span> <span data-ttu-id="b52aa-173">Ayrıca, varsayılan oturum açma düzenini dış olarak ayarlar :::no-loc(cookie)::: `:::no-loc(Identity):::Constants.ExternalScheme` .</span><span class="sxs-lookup"><span data-stu-id="b52aa-173">Additionally, it sets the default sign-in scheme to the external :::no-loc(cookie)::: `:::no-loc(Identity):::Constants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="b52aa-174">HttpContext kimlik doğrulama uzantılarını kullanma</span><span class="sxs-lookup"><span data-stu-id="b52aa-174">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="b52aa-175">`IAuthenticationManager`Arabirim, 1. x kimlik doğrulama sistemine ana giriş noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="b52aa-175">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="b52aa-176">`HttpContext`Ad alanındaki yeni bir genişletme yöntemleri kümesiyle değiştirilmiştir `Microsoft.AspNetCore.Authentication` .</span><span class="sxs-lookup"><span data-stu-id="b52aa-176">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="b52aa-177">Örneğin, 1. x projeleri bir özelliğe başvurur `Authentication` :</span><span class="sxs-lookup"><span data-stu-id="b52aa-177">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="b52aa-178">2,0 projesinde, `Microsoft.AspNetCore.Authentication` ad alanını içeri aktarın ve `Authentication` özellik başvurularını silin:</span><span class="sxs-lookup"><span data-stu-id="b52aa-178">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="b52aa-179">Windows kimlik doğrulaması (HTTP.sys/Iisıntegration)</span><span class="sxs-lookup"><span data-stu-id="b52aa-179">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="b52aa-180">Windows kimlik doğrulamasının iki farklı çeşidi vardır:</span><span class="sxs-lookup"><span data-stu-id="b52aa-180">There are two variations of Windows authentication:</span></span>

* <span data-ttu-id="b52aa-181">Konak yalnızca kimliği doğrulanmış kullanıcılara izin verir.</span><span class="sxs-lookup"><span data-stu-id="b52aa-181">The host only allows authenticated users.</span></span> <span data-ttu-id="b52aa-182">Bu çeşitleme 2,0 değişikliklerden etkilenmez.</span><span class="sxs-lookup"><span data-stu-id="b52aa-182">This variation isn't affected by the 2.0 changes.</span></span>
* <span data-ttu-id="b52aa-183">Konak hem anonim hem de kimliği doğrulanmış kullanıcılara izin verir.</span><span class="sxs-lookup"><span data-stu-id="b52aa-183">The host allows both anonymous and authenticated users.</span></span> <span data-ttu-id="b52aa-184">Bu çeşitleme 2,0 değişikliklerden etkilenir.</span><span class="sxs-lookup"><span data-stu-id="b52aa-184">This variation is affected by the 2.0 changes.</span></span> <span data-ttu-id="b52aa-185">Örneğin, uygulama [IIS](xref:host-and-deploy/iis/index) veya [HTTP.sys](xref:fundamentals/servers/httpsys) katmanında anonim kullanıcılara izin vermeli, ancak kullanıcıları denetleyici düzeyinde yetkilendirebilmelidir.</span><span class="sxs-lookup"><span data-stu-id="b52aa-185">For example, the app should allow anonymous users at the [IIS](xref:host-and-deploy/iis/index) or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorize users at the controller level.</span></span> <span data-ttu-id="b52aa-186">Bu senaryoda, yönteminde varsayılan düzeni ayarlayın `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b52aa-186">In this scenario, set the default scheme in the `Startup.ConfigureServices` method.</span></span>

  <span data-ttu-id="b52aa-187">[Microsoft. AspNetCore. Server. ıısıntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/)için varsayılan düzeni şu şekilde ayarlayın `IISDefaults.AuthenticationScheme` :</span><span class="sxs-lookup"><span data-stu-id="b52aa-187">For [Microsoft.AspNetCore.Server.IISIntegration](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IISIntegration/), set the default scheme to `IISDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.IISIntegration;

  services.AddAuthentication(IISDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="b52aa-188">[Microsoft. AspNetCore. Server. HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/)için varsayılan düzeni olarak ayarlayın `HttpSysDefaults.AuthenticationScheme` :</span><span class="sxs-lookup"><span data-stu-id="b52aa-188">For [Microsoft.AspNetCore.Server.HttpSys](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.HttpSys/), set the default scheme to `HttpSysDefaults.AuthenticationScheme`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Server.HttpSys;

  services.AddAuthentication(HttpSysDefaults.AuthenticationScheme);
  ```

  <span data-ttu-id="b52aa-189">Varsayılan düzenin ayarlanamaması, yetkilendirme (sınama) isteğinin şu özel durumla çalışmasını engeller:</span><span class="sxs-lookup"><span data-stu-id="b52aa-189">Failure to set the default scheme prevents the authorize (challenge) request from working with the following exception:</span></span>

  > <span data-ttu-id="b52aa-190">`System.InvalidOperationException`: Hiçbir authenticationScheme belirtilmedi ve hiç Defaultchallenbescheme bulunamadı.</span><span class="sxs-lookup"><span data-stu-id="b52aa-190">`System.InvalidOperationException`: No authenticationScheme was specified, and there was no DefaultChallengeScheme found.</span></span>

<span data-ttu-id="b52aa-191">Daha fazla bilgi için bkz. <xref:security/authentication/windowsauth>.</span><span class="sxs-lookup"><span data-stu-id="b52aa-191">For more information, see <xref:security/authentication/windowsauth>.</span></span>

<a name="identity-:::no-loc(cookie):::-options"></a>

## <a name="no-locidentityno-loccookieoptions-instances"></a><span data-ttu-id="b52aa-192">:::no-loc(Identity)::::::no-loc(Cookie):::Seçenek örnekleri</span><span class="sxs-lookup"><span data-stu-id="b52aa-192">:::no-loc(Identity)::::::no-loc(Cookie):::Options instances</span></span>

<span data-ttu-id="b52aa-193">2,0 değişikliğin yan etkisi, seçenek örnekleri yerine adlandırılmış seçenekleri kullanma anahtarıdır :::no-loc(cookie)::: .</span><span class="sxs-lookup"><span data-stu-id="b52aa-193">A side effect of the 2.0 changes is the switch to using named options instead of :::no-loc(cookie)::: options instances.</span></span> <span data-ttu-id="b52aa-194">:::no-loc(Identity)::: :::no-loc(cookie)::: Düzen adlarını özelleştirme özelliği kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="b52aa-194">The ability to customize the :::no-loc(Identity)::: :::no-loc(cookie)::: scheme names is removed.</span></span>

<span data-ttu-id="b52aa-195">Örneğin, 1. x projeleri bir [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) `:::no-loc(Identity)::::::no-loc(Cookie):::Options` parametreyi *accountcontroller.cs* ve *ManageController.cs* öğesine geçirmek için Oluşturucu Ekleme kullanır.</span><span class="sxs-lookup"><span data-stu-id="b52aa-195">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `:::no-loc(Identity)::::::no-loc(Cookie):::Options` parameter into *AccountController.cs* and *ManageController.cs* .</span></span> <span data-ttu-id="b52aa-196">Dış :::no-loc(cookie)::: kimlik doğrulama düzenine, belirtilen örnekten erişilir:</span><span class="sxs-lookup"><span data-stu-id="b52aa-196">The external :::no-loc(cookie)::: authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="b52aa-197">Belirtilen Oluşturucu Ekleme, 2,0 projesinde gereksiz hale gelir ve `_external:::no-loc(Cookie):::Scheme` alan silinebilir:</span><span class="sxs-lookup"><span data-stu-id="b52aa-197">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_external:::no-loc(Cookie):::Scheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="b52aa-198">1. x projeleri `_external:::no-loc(Cookie):::Scheme` alanı şu şekilde kullandı:</span><span class="sxs-lookup"><span data-stu-id="b52aa-198">1.x projects used the `_external:::no-loc(Cookie):::Scheme` field as follows:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="b52aa-199">2,0 projesinde, yukarıdaki kodu aşağıdaki kodla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="b52aa-199">In 2.0 projects, replace the preceding code with the following.</span></span> <span data-ttu-id="b52aa-200">`:::no-loc(Identity):::Constants.ExternalScheme`Sabit doğrudan kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b52aa-200">The `:::no-loc(Identity):::Constants.ExternalScheme` constant can be used directly.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="b52aa-201">`SignOutAsync`Aşağıdaki ad alanını içeri aktararak yeni eklenen çağrıyı çözümleyin:</span><span class="sxs-lookup"><span data-stu-id="b52aa-201">Resolve the newly added `SignOutAsync` call by importing the following namespace:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationImport)]

<a name="navigation-properties"></a>

## <a name="add-no-locidentityuser-poco-navigation-properties"></a><span data-ttu-id="b52aa-202">:::no-loc(Identity):::Kullanıcı POCO gezintisi özellikleri ekle</span><span class="sxs-lookup"><span data-stu-id="b52aa-202">Add :::no-loc(Identity):::User POCO navigation properties</span></span>

<span data-ttu-id="b52aa-203">Taban `:::no-loc(Identity):::User` poco 'nın (düz eskı CLR nesnesi) Entity Framework (EF) temel gezinti özellikleri kaldırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="b52aa-203">The Entity Framework (EF) Core navigation properties of the base `:::no-loc(Identity):::User` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="b52aa-204">1. x projeniz bu özellikleri kullandıysanız, bunları el ile 2,0 projesine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b52aa-204">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

```csharp
/// <summary>
/// Navigation property for the roles this user belongs to.
/// </summary>
public virtual ICollection<:::no-loc(Identity):::UserRole<int>> Roles { get; } = new List<:::no-loc(Identity):::UserRole<int>>();

/// <summary>
/// Navigation property for the claims this user possesses.
/// </summary>
public virtual ICollection<:::no-loc(Identity):::UserClaim<int>> Claims { get; } = new List<:::no-loc(Identity):::UserClaim<int>>();

/// <summary>
/// Navigation property for this users login accounts.
/// </summary>
public virtual ICollection<:::no-loc(Identity):::UserLogin<int>> Logins { get; } = new List<:::no-loc(Identity):::UserLogin<int>>();
```

<span data-ttu-id="b52aa-205">EF Core geçişleri çalıştırırken yinelenen yabancı anahtarların oluşmasını engellemek için, `:::no-loc(Identity):::DbContext` sınıfınızın ' `OnModelCreating` yöntemine (çağrıdan sonra) aşağıdaki ekleyin `base.OnModelCreating();` :</span><span class="sxs-lookup"><span data-stu-id="b52aa-205">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `:::no-loc(Identity):::DbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder builder)
{
    base.OnModelCreating(builder);
    // Customize the :::no-loc(ASP.NET Core Identity)::: model and override the defaults if needed.
    // For example, you can rename the :::no-loc(ASP.NET Core Identity)::: table names and more.
    // Add your customizations after calling base.OnModelCreating(builder);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Claims)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Logins)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);

    builder.Entity<ApplicationUser>()
        .HasMany(e => e.Roles)
        .WithOne()
        .HasForeignKey(e => e.UserId)
        .IsRequired()
        .OnDelete(DeleteBehavior.Cascade);
}
```

<a name="synchronous-method-removal"></a>

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="b52aa-206">Getexternalauthenticationdüzenlerini değiştirme</span><span class="sxs-lookup"><span data-stu-id="b52aa-206">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="b52aa-207">Zaman uyumlu yöntem, `GetExternalAuthenticationSchemes` zaman uyumsuz bir sürüm yararına kaldırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="b52aa-207">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="b52aa-208">1. x projeleri *denetleyicileri/ManageController. cs* dosyasında aşağıdaki koda sahiptir:</span><span class="sxs-lookup"><span data-stu-id="b52aa-208">1.x projects have the following code in *Controllers/ManageController.cs* :</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="b52aa-209">Bu yöntem, *Görünümler/Account/Login. cshtml* içinde de görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="b52aa-209">This method appears in *Views/Account/Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemes&highlight=2)]

<span data-ttu-id="b52aa-210">2,0 projesinde <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager`1.GetExternalAuthenticationSchemesAsync*> yöntemini kullanın.</span><span class="sxs-lookup"><span data-stu-id="b52aa-210">In 2.0 projects, use the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.SignInManager`1.GetExternalAuthenticationSchemesAsync*> method.</span></span> <span data-ttu-id="b52aa-211">*ManageController.cs* içindeki değişiklik aşağıdaki koda benzer:</span><span class="sxs-lookup"><span data-stu-id="b52aa-211">The change in *ManageController.cs* resembles the following code:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="b52aa-212">*Login. cshtml* dosyasında, `AuthenticationScheme` döngüde erişilen özellik şu `foreach` şekilde değişir `Name` :</span><span class="sxs-lookup"><span data-stu-id="b52aa-212">In *Login.cshtml* , the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?name=snippet_GetExtAuthNSchemesAsync&highlight=2,19)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="b52aa-213">ManageLoginsViewModel özelliği değişikliği</span><span class="sxs-lookup"><span data-stu-id="b52aa-213">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="b52aa-214">Bir `ManageLoginsViewModel` nesne, `ManageLogins` *ManageController.cs* eyleminde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b52aa-214">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs* .</span></span> <span data-ttu-id="b52aa-215">1. x projelerinde, nesnenin `OtherLogins` özellik dönüş türü `IList<AuthenticationDescription>` .</span><span class="sxs-lookup"><span data-stu-id="b52aa-215">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="b52aa-216">Bu dönüş türü için içeri aktarma gereklidir `Microsoft.AspNetCore.Http.Authentication` :</span><span class="sxs-lookup"><span data-stu-id="b52aa-216">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="b52aa-217">2,0 projesinde, dönüş türü olarak değişir `IList<AuthenticationScheme>` .</span><span class="sxs-lookup"><span data-stu-id="b52aa-217">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="b52aa-218">Bu yeni dönüş türünün içeri aktarma işlemi `Microsoft.AspNetCore.Http.Authentication` bir içeri aktarma ile değiştirilmesi gerekiyor `Microsoft.AspNetCore.Authentication` .</span><span class="sxs-lookup"><span data-stu-id="b52aa-218">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="b52aa-219">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b52aa-219">Additional resources</span></span>

<span data-ttu-id="b52aa-220">Daha fazla bilgi için bkz. GitHub 'da [kimlik doğrulama 2,0 sorunu tartışması](https://github.com/aspnet/Security/issues/1338) .</span><span class="sxs-lookup"><span data-stu-id="b52aa-220">For more information, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>
