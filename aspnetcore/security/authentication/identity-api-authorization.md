---
title: ASP.NET Core tek sayfalı uygulamalar için kimlik doğrulamaya giriş
author: javiercn
description: IdentityASP.NET Core uygulamasının içinde barındırılan tek sayfalı bir uygulamayla kullanın.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 10/27/2020
no-loc:
- appsettings.json
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
uid: security/authentication/identity/spa
ms.openlocfilehash: 5a6c160ebdda3ec600980aa839770f4f22a9c2fc
ms.sourcegitcommit: cc405f20537484744423ddaf87bd1e7d82b6bdf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98658670"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="15db9-103">Maça kimlik doğrulaması ve yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="15db9-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="15db9-104">ASP.NET Core 3,1 ve üzeri şablonlar, API yetkilendirmesi desteğini kullanarak tek sayfalı uygulamalarda (Spaon) kimlik doğrulaması sunar.</span><span class="sxs-lookup"><span data-stu-id="15db9-104">The ASP.NET Core 3.1 and later templates offer authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="15db9-105">ASP.NET Core Identitykimlik doğrulamak ve depolamak için, OpenID Connect 'i uygulamak üzere [ Identity sunucu](https://identityserver.io/) ile birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="15db9-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing OpenID Connect.</span></span>

<span data-ttu-id="15db9-106">Bir kimlik doğrulama parametresi, **angular** 'a eklenmiştir ve **Web uygulamasındaki (model-görünüm-denetleyici)** (MVC) ve **Web uygulaması** (sayfalar) proje şablonlarında kimlik doğrulama parametresine benzer olan proje şablonlarına **tepki** verir Razor .</span><span class="sxs-lookup"><span data-stu-id="15db9-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="15db9-107">İzin verilen parametre değerleri **none** ve **bireysel**.</span><span class="sxs-lookup"><span data-stu-id="15db9-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="15db9-108">**React.js ve Redux** proje şablonu, kimlik doğrulama parametresini Şu anda desteklemiyor.</span><span class="sxs-lookup"><span data-stu-id="15db9-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="15db9-109">API yetkilendirme desteğiyle uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="15db9-109">Create an app with API authorization support</span></span>

<span data-ttu-id="15db9-110">Kullanıcı kimlik doğrulaması ve yetkilendirme, hem angular ile hem de maça 'Ları ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="15db9-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="15db9-111">Bir komut kabuğu açın ve şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="15db9-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="15db9-112">**Angular**:</span><span class="sxs-lookup"><span data-stu-id="15db9-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="15db9-113">**Tepki** verme:</span><span class="sxs-lookup"><span data-stu-id="15db9-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="15db9-114">Yukarıdaki komut, SPA 'yı içeren *clientapp* dizinine sahip bir ASP.NET Core uygulaması oluşturur.</span><span class="sxs-lookup"><span data-stu-id="15db9-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="15db9-115">Uygulamanın ASP.NET Core bileşenlerinin genel açıklaması</span><span class="sxs-lookup"><span data-stu-id="15db9-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="15db9-116">Aşağıdaki bölümlerde, kimlik doğrulama desteği dahil edildiğinde projenin eklemeleri açıklanır:</span><span class="sxs-lookup"><span data-stu-id="15db9-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="15db9-117">Başlangıç sınıfı</span><span class="sxs-lookup"><span data-stu-id="15db9-117">Startup class</span></span>

<span data-ttu-id="15db9-118">Aşağıdaki kod örnekleri, [Microsoft. AspNetCore. ApiAuthorization öğesine bağımlıdır. Identity Sunucu](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet paketi.</span><span class="sxs-lookup"><span data-stu-id="15db9-118">The following code examples rely on the [Microsoft.AspNetCore.ApiAuthorization.IdentityServer](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet package.</span></span> <span data-ttu-id="15db9-119">Örnekler, <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> ve genişletme yöntemlerini kullanarak API kimlik doğrulaması ve yetkilendirme yapılandırır <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="15db9-119">The examples configure API authentication and authorization using the <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> and <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> extension methods.</span></span> <span data-ttu-id="15db9-120">Kimlik doğrulamasıyla tepki verme veya angular SPA proje şablonlarını kullanan projeler, bu pakete bir başvuru içerir.</span><span class="sxs-lookup"><span data-stu-id="15db9-120">Projects using the React or Angular SPA project templates with authentication include a reference to this package.</span></span>

<span data-ttu-id="15db9-121">`Startup`Sınıfı aşağıdaki eklemelere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="15db9-121">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="15db9-122">Yöntemin içinde `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="15db9-122">Inside the `Startup.ConfigureServices` method:</span></span>
  * <span data-ttu-id="15db9-123">Identity Varsayılan Kullanıcı arabirimi ile:</span><span class="sxs-lookup"><span data-stu-id="15db9-123">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="15db9-124">Identity`AddApiAuthorization`Sunucu üst kısmında bazı varsayılan ASP.NET Core kuralları ayarlayan ek bir yardımcı yöntemi olan sunucu Identity :</span><span class="sxs-lookup"><span data-stu-id="15db9-124">IdentityServer with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="15db9-125">`AddIdentityServerJwt`Sunucu tarafından ÜRETILEN JWT belirteçlerini doğrulamak üzere uygulamayı yapılandıran ek bir yardımcı yöntem ile kimlik doğrulaması Identity :</span><span class="sxs-lookup"><span data-stu-id="15db9-125">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="15db9-126">Yöntemin içinde `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="15db9-126">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="15db9-127">İstek kimlik bilgilerini doğrulamadan ve Kullanıcı istek bağlamında ayarlamaktan sorumlu kimlik doğrulama ara yazılımı:</span><span class="sxs-lookup"><span data-stu-id="15db9-127">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="15db9-128">IdentityOpenID Connect uç noktalarını kullanıma sunan sunucu ara yazılımı:</span><span class="sxs-lookup"><span data-stu-id="15db9-128">The IdentityServer middleware that exposes the OpenID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="azure-app-service-on-linux"></a><span data-ttu-id="15db9-129">Linux üzerinde Azure App Service</span><span class="sxs-lookup"><span data-stu-id="15db9-129">Azure App Service on Linux</span></span>

<span data-ttu-id="15db9-130">Linux üzerinde Azure App Service dağıtımlar için, sertifikayı vereni açık olarak belirtin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="15db9-130">For Azure App Service deployments on Linux, specify the issuer explicitly in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme, 
    options =>
    {
        options.Authority = "{AUTHORITY}";
    });
```

<span data-ttu-id="15db9-131">Yukarıdaki kodda, `{AUTHORITY}` yer tutucu, <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions.Authority> OpenID Connect çağrısı yaparken kullanılacak olan yer tutucudur.</span><span class="sxs-lookup"><span data-stu-id="15db9-131">In the preceding code, the `{AUTHORITY}` placeholder is the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions.Authority> to use when making OpenID Connect calls.</span></span>

<span data-ttu-id="15db9-132">Örnek:</span><span class="sxs-lookup"><span data-stu-id="15db9-132">Example:</span></span>

```csharp
options.Authority = "https://contoso-service.azurewebsites.net";
```

### <a name="addapiauthorization"></a><span data-ttu-id="15db9-133">Addadpiauthorization</span><span class="sxs-lookup"><span data-stu-id="15db9-133">AddApiAuthorization</span></span>

<span data-ttu-id="15db9-134">Bu yardımcı yöntem, Identity sunucuyu desteklenen yapılandırmamızı kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="15db9-134">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="15db9-135">IdentitySunucu, uygulama güvenliği sorunlarını işlemeye yönelik güçlü ve genişletilebilir bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="15db9-135">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="15db9-136">Aynı zamanda, en yaygın senaryolar için gereksiz karmaşıklık sunan.</span><span class="sxs-lookup"><span data-stu-id="15db9-136">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="15db9-137">Sonuç olarak, size iyi bir başlangıç noktası olarak kabul edilen bir dizi kural ve yapılandırma seçeneği sağlanır.</span><span class="sxs-lookup"><span data-stu-id="15db9-137">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="15db9-138">Kimlik doğrulamanın değişmesi için, sunucunun tam gücü, Identity kimlik doğrulamasını gereksinimlerinize uyacak şekilde özelleştirmek için hala kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="15db9-138">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="15db9-139">IdentityServerjwt Ekle</span><span class="sxs-lookup"><span data-stu-id="15db9-139">AddIdentityServerJwt</span></span>

<span data-ttu-id="15db9-140">Bu yardımcı yöntemi, varsayılan kimlik doğrulama işleyicisi olarak uygulama için bir ilke düzeni yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="15db9-140">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="15db9-141">İlke, Identity Identity "/" URL alanındaki herhangi bir alt yolda yönlendirilen tüm isteklerin işlemesini sağlamak üzere yapılandırılmıştır Identity .</span><span class="sxs-lookup"><span data-stu-id="15db9-141">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="15db9-142">`JwtBearerHandler`Diğer tüm istekleri işler.</span><span class="sxs-lookup"><span data-stu-id="15db9-142">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="15db9-143">Ayrıca, bu yöntem, bir `<<ApplicationName>>API` API kaynağını Identity varsayılan kapsamına sahip sunucuya kaydeder ve bu `<<ApplicationName>>API` Identity uygulama için sunucu tarafından VERILEN belirteçleri doğrulamak üzere JWT taşıyıcı belirteç ara yazılımını yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="15db9-143">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="15db9-144">Dalgalı bir denetleyici</span><span class="sxs-lookup"><span data-stu-id="15db9-144">WeatherForecastController</span></span>

<span data-ttu-id="15db9-145">*Controllers\dalgalı therforebir Controller.cs* dosyasında, `[Authorize]` kullanıcıya kaynağa erişim için varsayılan ilkeye göre yetkilendirilmiş olması gerektiğini belirten sınıfa uygulanan özniteliğe dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="15db9-145">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="15db9-146">Varsayılan yetkilendirme ilkesi, yukarıda belirtilen ilke şemasına tarafından yapılandırılan varsayılan kimlik doğrulama şemasını kullanacak şekilde yapılandırılmıştır `AddIdentityServerJwt` `JwtBearerHandler` . Bu, bu tür bir yardımcı yöntemi tarafından yapılandırılmış bir uygulama istekleri için varsayılan işleyicidir.</span><span class="sxs-lookup"><span data-stu-id="15db9-146">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="15db9-147">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="15db9-147">ApplicationDbContext</span></span>

<span data-ttu-id="15db9-148">*Data\applicationdbcontext.cs* dosyasında, ' `DbContext` ın Identity `ApiAuthorizationDbContext` genişlettiği özel durumla (öğesinden daha fazla türetilmiş bir sınıf `IdentityDbContext` ) sunucu için şemayı dahil etmek için Identity aynı olduğuna dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="15db9-148">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="15db9-149">Veritabanı şemasının tam denetimini elde etmek için, kullanılabilir Identity `DbContext` sınıflardan birini ve Identity yöntemi çağırarak şemayı içerecek şekilde yapılandırın `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="15db9-149">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="15db9-150">Oıdcconfigurationcontroller</span><span class="sxs-lookup"><span data-stu-id="15db9-150">OidcConfigurationController</span></span>

<span data-ttu-id="15db9-151">*Controllers\oıdcconfigurationcontroller.cs* dosyasında, istemcinin kullanması gereken OIDC parametrelerine hizmeti sağlaması için sağlanan uç noktaya dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="15db9-151">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### appsettings.json

<span data-ttu-id="15db9-152">*appsettings.json* Proje kökü dosyasında, `IdentityServer` yapılandırılmış istemciler listesini açıklayan yeni bir bölüm vardır.</span><span class="sxs-lookup"><span data-stu-id="15db9-152">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="15db9-153">Aşağıdaki örnekte, tek bir istemci vardır.</span><span class="sxs-lookup"><span data-stu-id="15db9-153">In the following example, there's a single client.</span></span> <span data-ttu-id="15db9-154">İstemci adı, uygulama adına karşılık gelir ve kural tarafından OAuth `ClientId` parametresine eşlenir.</span><span class="sxs-lookup"><span data-stu-id="15db9-154">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="15db9-155">Profil, yapılandırılan uygulama türünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="15db9-155">The profile indicates the app type being configured.</span></span> <span data-ttu-id="15db9-156">Sunucu için yapılandırma işlemini basitleştiren kuralları yönlendirmek için dahili olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="15db9-156">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="15db9-157">[Uygulama profilleri](#application-profiles) bölümünde açıklandığı gibi çeşitli profiller mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="15db9-157">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="15db9-158">Üzerinde appsettings.Development.js</span><span class="sxs-lookup"><span data-stu-id="15db9-158">appsettings.Development.json</span></span>

<span data-ttu-id="15db9-159">Proje kökünün dosyasındaki *appsettings.Development.js* , `IdentityServer` belirteçleri imzalamak için kullanılan anahtarı açıklayan bir bölüm vardır.</span><span class="sxs-lookup"><span data-stu-id="15db9-159">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="15db9-160">Üretime dağıtım yaparken, [üretime dağıtma](#deploy-to-production) bölümünde açıklandığı gibi bir anahtarın uygulamayla birlikte sağlanması ve dağıtılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="15db9-160">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="15db9-161">Angular uygulamasının genel açıklaması</span><span class="sxs-lookup"><span data-stu-id="15db9-161">General description of the Angular app</span></span>

<span data-ttu-id="15db9-162">Angular şablonundaki kimlik doğrulama ve API yetkilendirme desteği *Clientapp\src\api-Authorization* dizinindeki kendi angular modülünde yer alır.</span><span class="sxs-lookup"><span data-stu-id="15db9-162">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="15db9-163">Modül aşağıdaki öğelerden oluşur:</span><span class="sxs-lookup"><span data-stu-id="15db9-163">The module is composed of the following elements:</span></span>

* <span data-ttu-id="15db9-164">3 bileşen:</span><span class="sxs-lookup"><span data-stu-id="15db9-164">3 components:</span></span>
  * <span data-ttu-id="15db9-165">*login. Component. TS*: uygulamanın oturum açma akışını işler.</span><span class="sxs-lookup"><span data-stu-id="15db9-165">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="15db9-166">*Logout. Component. TS*: uygulamanın oturum kapatma akışını işler.</span><span class="sxs-lookup"><span data-stu-id="15db9-166">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="15db9-167">*login-Menu. Component. TS*: aşağıdaki bağlantı kümelerinden birini görüntüleyen pencere öğesi:</span><span class="sxs-lookup"><span data-stu-id="15db9-167">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="15db9-168">Kullanıcı profili yönetimi ve kullanıcının kimlik doğrulaması yapıldığında oturum kapatma bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="15db9-168">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="15db9-169">Kullanıcının kimlik doğrulaması olmadığında kayıt ve oturum açma bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="15db9-169">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="15db9-170">`AuthorizeGuard`Rotalara eklenebilen ve yolu ziyaret etmeden önce bir kullanıcının kimliğinin doğrulanmasını gerektiren bir Route koruyucusu.</span><span class="sxs-lookup"><span data-stu-id="15db9-170">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="15db9-171">`AuthorizeInterceptor`Kullanıcının kimlik doğrulaması yapıldığında API 'yi hedefleyen gıden http isteklerine erişim belirtecini bağlayan BIR http yakalayıcısı.</span><span class="sxs-lookup"><span data-stu-id="15db9-171">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="15db9-172">`AuthorizeService`Kimlik doğrulama işleminin alt düzey ayrıntılarını işleyen ve kimliği doğrulanmış kullanıcı hakkındaki bilgileri, tüketim için uygulamanın geri kalanına getiren bir hizmet.</span><span class="sxs-lookup"><span data-stu-id="15db9-172">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="15db9-173">Uygulamanın kimlik doğrulama bölümleriyle ilişkili yolları tanımlayan angular modülü.</span><span class="sxs-lookup"><span data-stu-id="15db9-173">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="15db9-174">Oturum açma menü bileşeni, yakalayıcısı, koruyucu ve uygulamanın geri kalanından tüketim için hizmeti sunar.</span><span class="sxs-lookup"><span data-stu-id="15db9-174">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="15db9-175">Tepki verme uygulamasının genel açıklaması</span><span class="sxs-lookup"><span data-stu-id="15db9-175">General description of the React app</span></span>

<span data-ttu-id="15db9-176">Yanıt verme şablonunda kimlik doğrulama ve API yetkilendirmesi desteği *Clientapp\src\\disk api-Authorization* dizininde bulunur.</span><span class="sxs-lookup"><span data-stu-id="15db9-176">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="15db9-177">Şu öğelerden oluşur:</span><span class="sxs-lookup"><span data-stu-id="15db9-177">It's composed of the following elements:</span></span>

* <span data-ttu-id="15db9-178">4 bileşen:</span><span class="sxs-lookup"><span data-stu-id="15db9-178">4 components:</span></span>
  * <span data-ttu-id="15db9-179">*Login.js*: uygulamanın oturum açma akışını işler.</span><span class="sxs-lookup"><span data-stu-id="15db9-179">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="15db9-180">*Logout.js*: uygulamanın oturum kapatma akışını işler.</span><span class="sxs-lookup"><span data-stu-id="15db9-180">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="15db9-181">*LoginMenu.js*: aşağıdaki bağlantı kümelerinden birini görüntüleyen pencere öğesi:</span><span class="sxs-lookup"><span data-stu-id="15db9-181">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="15db9-182">Kullanıcı profili yönetimi ve kullanıcının kimlik doğrulaması yapıldığında oturum kapatma bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="15db9-182">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="15db9-183">Kullanıcının kimlik doğrulaması olmadığında kayıt ve oturum açma bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="15db9-183">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="15db9-184">*AuthorizeRoute.js*: bir kullanıcının, parametresinde belirtilen bileşeni işlemeden önce kimliğinin doğrulanmasını gerektiren bir rota bileşeni `Component` .</span><span class="sxs-lookup"><span data-stu-id="15db9-184">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="15db9-185">`authService` `AuthorizeService` Kimlik doğrulama işleminin alt düzey ayrıntılarını işleyen ve kimliği doğrulanmış kullanıcı hakkındaki bilgileri, tüketim için uygulamanın geri kalanına getiren, dışa aktarılmış bir sınıf örneği.</span><span class="sxs-lookup"><span data-stu-id="15db9-185">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="15db9-186">Artık çözümün ana bileşenlerini gördüğünüze göre, uygulama için ayrı senaryolara daha ayrıntılı bir şekilde göz atabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="15db9-186">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="15db9-187">Yeni bir API 'de yetkilendirme gerektir</span><span class="sxs-lookup"><span data-stu-id="15db9-187">Require authorization on a new API</span></span>

<span data-ttu-id="15db9-188">Varsayılan olarak, sistem yeni API 'Ler için kolayca yetkilendirme gerektirecek şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="15db9-188">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="15db9-189">Bunu yapmak için yeni bir denetleyici oluşturun ve `[Authorize]` özniteliği Controller sınıfına veya denetleyici içindeki herhangi bir eyleme ekleyin.</span><span class="sxs-lookup"><span data-stu-id="15db9-189">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="15db9-190">API kimlik doğrulama işleyicisini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="15db9-190">Customize the API authentication handler</span></span>

<span data-ttu-id="15db9-191">API 'nin JWT işleyicisinin yapılandırmasını özelleştirmek için, <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> örneğini yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="15db9-191">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();

services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        ...
    });
```

<span data-ttu-id="15db9-192">API 'nin JWT işleyicisi, kullanarak kimlik doğrulama işlemi üzerinde denetimi etkinleştiren olaylar oluşturur `JwtBearerEvents` .</span><span class="sxs-lookup"><span data-stu-id="15db9-192">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="15db9-193">API yetkilendirmesi için destek sağlamak üzere `AddIdentityServerJwt` kendi olay işleyicilerini kaydeder.</span><span class="sxs-lookup"><span data-stu-id="15db9-193">To provide support for API authorization, `AddIdentityServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="15db9-194">Bir olayın işlenmesini özelleştirmek için, var olan olay işleyicisini gereken ek mantığla sarın.</span><span class="sxs-lookup"><span data-stu-id="15db9-194">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="15db9-195">Örnek:</span><span class="sxs-lookup"><span data-stu-id="15db9-195">For example:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    IdentityServerJwtConstants.IdentityServerJwtBearerScheme,
    options =>
    {
        var onTokenValidated = options.Events.OnTokenValidated;       
        
        options.Events.OnTokenValidated = async context =>
        {
            await onTokenValidated(context);
            ...
        }
    });
```

<span data-ttu-id="15db9-196">Önceki kodda, `OnTokenValidated` olay işleyicisi özel bir uygulamayla değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="15db9-196">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="15db9-197">Bu uygulama:</span><span class="sxs-lookup"><span data-stu-id="15db9-197">This implementation:</span></span>

1. <span data-ttu-id="15db9-198">API yetkilendirme desteği tarafından sunulan özgün uygulamayı çağırır.</span><span class="sxs-lookup"><span data-stu-id="15db9-198">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="15db9-199">Kendi özel mantığını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="15db9-199">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="15db9-200">İstemci tarafı yolunu koruma (angular)</span><span class="sxs-lookup"><span data-stu-id="15db9-200">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="15db9-201">İstemci tarafı bir yolu korumak, yetkilendirme koruyucusu bir rota yapılandırılırken çalıştırılacak korumalara listesine eklenerek yapılır.</span><span class="sxs-lookup"><span data-stu-id="15db9-201">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="15db9-202">Örnek olarak, `fetch-data` yolun ana uygulama angular modülü içinde nasıl yapılandırıldığını görebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="15db9-202">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="15db9-203">Bir yolu korumanın gerçek uç noktayı korumadığını (buna hala bir `[Authorize]` öznitelik uygulanacağını), ancak kullanıcının kimlik doğrulaması olmadığında verilen istemci tarafı rotasında gezinmelerini önlediği bahsetmek önemlidir.</span><span class="sxs-lookup"><span data-stu-id="15db9-203">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="15db9-204">API isteklerinin kimliğini doğrulama (angular)</span><span class="sxs-lookup"><span data-stu-id="15db9-204">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="15db9-205">Uygulama ile barındırılan API 'lere yönelik kimlik doğrulama istekleri, uygulama tarafından tanımlanan HTTP istemci yakalayıcısı kullanılarak otomatik olarak yapılır.</span><span class="sxs-lookup"><span data-stu-id="15db9-205">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="15db9-206">İstemci tarafı yolunu koruma (tepki verme)</span><span class="sxs-lookup"><span data-stu-id="15db9-206">Protect a client-side route (React)</span></span>

<span data-ttu-id="15db9-207">`AuthorizeRoute`Düz bileşen yerine bileşeni kullanarak bir istemci tarafı yolunu koruyun `Route` .</span><span class="sxs-lookup"><span data-stu-id="15db9-207">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="15db9-208">Örneğin, `fetch-data` yolun bileşen içinde nasıl yapılandırıldığına dikkat edin `App` :</span><span class="sxs-lookup"><span data-stu-id="15db9-208">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="15db9-209">Bir yolu koruma:</span><span class="sxs-lookup"><span data-stu-id="15db9-209">Protecting a route:</span></span>

* <span data-ttu-id="15db9-210">Gerçek uç noktayı korumaz (yine de `[Authorize]` ona uygulanan bir özniteliği gerektirir).</span><span class="sxs-lookup"><span data-stu-id="15db9-210">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="15db9-211">Yalnızca kullanıcının kimlik doğrulaması olmadığında verilen istemci tarafı rotasında gezinmelerini engeller.</span><span class="sxs-lookup"><span data-stu-id="15db9-211">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="15db9-212">API isteklerinin kimliğini doğrulama (tepki)</span><span class="sxs-lookup"><span data-stu-id="15db9-212">Authenticate API requests (React)</span></span>

<span data-ttu-id="15db9-213">Yanıt vererek istekleri kimlik doğrulama işlemi, önce örneği öğesinden içeri aktarılarak yapılır `authService` `AuthorizeService` .</span><span class="sxs-lookup"><span data-stu-id="15db9-213">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="15db9-214">Erişim belirteci konumundan alınır `authService` ve aşağıda gösterildiği gibi isteğe iliştirilir.</span><span class="sxs-lookup"><span data-stu-id="15db9-214">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="15db9-215">Yanıt verme bileşenlerinde, bu çalışma genellikle `componentDidMount` yaşam döngüsü yönteminde veya bazı Kullanıcı etkileşiminden elde edilen sonuç olarak yapılır.</span><span class="sxs-lookup"><span data-stu-id="15db9-215">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="15db9-216">AuthService 'i bileşeninizdeki içeri aktarın</span><span class="sxs-lookup"><span data-stu-id="15db9-216">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="15db9-217">Erişim belirtecini alma ve yanıta iliştirme</span><span class="sxs-lookup"><span data-stu-id="15db9-217">Retrieve and attach the access token to the response</span></span>

```javascript
async populateWeatherData() {
  const token = await authService.getAccessToken();
  const response = await fetch('api/SampleData/WeatherForecasts', {
    headers: !token ? {} : { 'Authorization': `Bearer ${token}` }
  });
  const data = await response.json();
  this.setState({ forecasts: data, loading: false });
}
```

## <a name="deploy-to-production"></a><span data-ttu-id="15db9-218">Üretime dağıtma</span><span class="sxs-lookup"><span data-stu-id="15db9-218">Deploy to production</span></span>

<span data-ttu-id="15db9-219">Uygulamayı üretime dağıtmak için aşağıdaki kaynakların sağlanması gerekir:</span><span class="sxs-lookup"><span data-stu-id="15db9-219">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="15db9-220">Kullanıcı hesaplarını depolamak için bir veritabanı Identity ve Identity sunucu verir.</span><span class="sxs-lookup"><span data-stu-id="15db9-220">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="15db9-221">Belirteçleri imzalamak için kullanılacak bir üretim sertifikası.</span><span class="sxs-lookup"><span data-stu-id="15db9-221">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="15db9-222">Bu sertifika için belirli bir gereksinim yoktur; otomatik olarak imzalanan bir sertifika veya bir CA yetkilisi tarafından sağlanan bir sertifika olabilir.</span><span class="sxs-lookup"><span data-stu-id="15db9-222">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="15db9-223">PowerShell veya OpenSSL gibi standart araçlarla oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="15db9-223">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="15db9-224">Hedef makinelerdeki sertifika deposuna yüklenebilir veya güçlü bir parolayla bir *. pfx* dosyası olarak dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="15db9-224">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-a-non-azure-web-hosting-provider"></a><span data-ttu-id="15db9-225">Örnek: Azure olmayan bir Web barındırma sağlayıcısına dağıtma</span><span class="sxs-lookup"><span data-stu-id="15db9-225">Example: Deploy to a non-Azure web hosting provider</span></span>

<span data-ttu-id="15db9-226">Web barındırma panelinde sertifikanızı oluşturun veya yükleyin.</span><span class="sxs-lookup"><span data-stu-id="15db9-226">In your web hosting panel, create or load your certificate.</span></span> <span data-ttu-id="15db9-227">Ardından, uygulama dosyasında, *appsettings.json* `IdentityServer` önemli ayrıntıları dahil etmek için bölümünü değiştirin.</span><span class="sxs-lookup"><span data-stu-id="15db9-227">Then in the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details.</span></span> <span data-ttu-id="15db9-228">Örnek:</span><span class="sxs-lookup"><span data-stu-id="15db9-228">For example:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "WebHosting",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

<span data-ttu-id="15db9-229">Yukarıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="15db9-229">In the preceding example:</span></span>

* <span data-ttu-id="15db9-230">`StoreName` Sertifikanın depolandığı sertifika deposunun adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="15db9-230">`StoreName` represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="15db9-231">Bu durumda, Web barındırma deposuna işaret eder.</span><span class="sxs-lookup"><span data-stu-id="15db9-231">In this case, it points to the web hosting store.</span></span>
* <span data-ttu-id="15db9-232">`StoreLocation` Sertifikanın nereden yükleneceğini temsil eder ( `CurrentUser` Bu durumda).</span><span class="sxs-lookup"><span data-stu-id="15db9-232">`StoreLocation` represents where to load the certificate from (`CurrentUser` in this case).</span></span>
* <span data-ttu-id="15db9-233">`Name` Sertifikanın ayırt edici konusuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="15db9-233">`Name` corresponds with the distinguished subject for the certificate.</span></span>

### <a name="example-deploy-to-azure-app-service"></a><span data-ttu-id="15db9-234">Örnek: Azure App Service dağıt</span><span class="sxs-lookup"><span data-stu-id="15db9-234">Example: Deploy to Azure App Service</span></span>

<span data-ttu-id="15db9-235">Bu bölümde, sertifika deposunda depolanan bir sertifikayı kullanarak Azure App Service uygulamanın dağıtımı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="15db9-235">This section describes deploying the app to Azure App Service using a certificate stored in the certificate store.</span></span> <span data-ttu-id="15db9-236">Uygulamayı sertifika deposundan bir sertifika yükleyecek şekilde değiştirmek için, daha sonraki bir adımda Azure portal uygulamayı yapılandırdığınızda standart bir katman hizmeti planı veya daha iyi bir seçenektir.</span><span class="sxs-lookup"><span data-stu-id="15db9-236">To modify the app to load a certificate from the certificate store, a Standard tier service plan or better is required when you configure the app in the Azure portal in a later step.</span></span>

<span data-ttu-id="15db9-237">Uygulamanın *appsettings.json* dosyasında, `IdentityServer` önemli ayrıntıları dahil etmek için bölümünü değiştirin:</span><span class="sxs-lookup"><span data-stu-id="15db9-237">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Store",
    "StoreName": "My",
    "StoreLocation": "CurrentUser",
    "Name": "CN=MyApplication"
  }
}
```

* <span data-ttu-id="15db9-238">Mağaza adı, sertifikanın depolandığı sertifika deposunun adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="15db9-238">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="15db9-239">Bu durumda, kişisel Kullanıcı deposuna işaret eder.</span><span class="sxs-lookup"><span data-stu-id="15db9-239">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="15db9-240">Depolama konumu, sertifikanın nereden yükleneceğini (veya) temsil eder `CurrentUser` `LocalMachine` .</span><span class="sxs-lookup"><span data-stu-id="15db9-240">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="15db9-241">Sertifikadaki ad özelliği, sertifikanın ayırt edici konusuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="15db9-241">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="15db9-242">Azure App Service dağıtmak için, [uygulamayı Azure 'A dağıtma](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)' daki adımları Izleyerek gerekli Azure kaynaklarının nasıl oluşturulduğunu ve uygulamayı üretime dağıtmayı açıklar.</span><span class="sxs-lookup"><span data-stu-id="15db9-242">To deploy to Azure App Service, follow the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), which explains how to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="15db9-243">Yukarıdaki yönergeleri uyguladıktan sonra, uygulama Azure 'a dağıtılır ancak henüz işlevsel değildir.</span><span class="sxs-lookup"><span data-stu-id="15db9-243">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="15db9-244">Uygulama tarafından kullanılan sertifikanın Azure portal yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="15db9-244">The certificate used by the app must be configured in the Azure portal.</span></span> <span data-ttu-id="15db9-245">Sertifika için parmak izini bulun ve [sertifikalarınızı yükleme](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)bölümünde açıklanan adımları izleyin.</span><span class="sxs-lookup"><span data-stu-id="15db9-245">Locate the thumbprint for the certificate and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="15db9-246">Bu adımlar SSL 'den bahsetirken, uygulama ile kullanmak üzere sağlanan sertifikayı karşıya yükleyebileceğiniz Azure portal **özel sertifikalar** bölümü vardır.</span><span class="sxs-lookup"><span data-stu-id="15db9-246">While these steps mention SSL, there's a **Private certificates** section in the Azure portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="15db9-247">Azure portal uygulamayı ve uygulamanın ayarlarını yapılandırdıktan sonra, uygulamayı portalda yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="15db9-247">After configuring the app and the app's settings in the Azure portal, restart the app in the portal.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="15db9-248">Diğer yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="15db9-248">Other configuration options</span></span>

<span data-ttu-id="15db9-249">API yetkilendirmesi desteği, Identity bir dizi kural, varsayılan değer ve, maça deneyimini basitleştirmek için geliştirmeler kümesi ile sunucu üzerinde oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="15db9-249">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="15db9-250">Daha az ki, Identity ASP.NET Core tümleştirmeler senaryonuzu kapsamadıysanız sunucunun tam gücü arka planda kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="15db9-250">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="15db9-251">ASP.NET Core desteği, tüm uygulamaların kuruluşumuza göre oluşturulduğu ve dağıtıldığı "birinci taraf" uygulamalara odaklanır.</span><span class="sxs-lookup"><span data-stu-id="15db9-251">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="15db9-252">Bu nedenle, izin veya Federasyon gibi şeyler için destek sunulmaz.</span><span class="sxs-lookup"><span data-stu-id="15db9-252">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="15db9-253">Bu senaryolar için sunucu ' yı kullanın Identity ve belgelerini izleyin.</span><span class="sxs-lookup"><span data-stu-id="15db9-253">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="15db9-254">Uygulama profilleri</span><span class="sxs-lookup"><span data-stu-id="15db9-254">Application profiles</span></span>

<span data-ttu-id="15db9-255">Uygulama profilleri, parametrelerini daha fazla tanımlayan uygulamalar için önceden tanımlanmış yapılandırlardır.</span><span class="sxs-lookup"><span data-stu-id="15db9-255">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="15db9-256">Şu anda, aşağıdaki profiller desteklenir:</span><span class="sxs-lookup"><span data-stu-id="15db9-256">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="15db9-257">`IdentityServerSPA`: Identity Tek bir birim olarak sunucu ile barındırılan BIR Spa 'yı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="15db9-257">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="15db9-258">`redirect_uri`Varsayılan olarak olur `/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="15db9-258">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="15db9-259">`post_logout_redirect_uri`Varsayılan olarak olur `/authentication/logout-callback` .</span><span class="sxs-lookup"><span data-stu-id="15db9-259">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="15db9-260">Kapsam kümesi `openid` , `profile` , ve uygulamadaki API 'ler için tanımlanan tüm kapsamları içerir.</span><span class="sxs-lookup"><span data-stu-id="15db9-260">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="15db9-261">İzin verilen OıDC yanıt türleri kümesi, `id_token token` tek tek ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="15db9-261">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="15db9-262">İzin verilen yanıt modu `fragment` .</span><span class="sxs-lookup"><span data-stu-id="15db9-262">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="15db9-263">`SPA`: Sunucusu ile barındırılmayan bir SPA 'yı temsil eder Identity .</span><span class="sxs-lookup"><span data-stu-id="15db9-263">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="15db9-264">Kapsam kümesi `openid` , `profile` , ve uygulamadaki API 'ler için tanımlanan tüm kapsamları içerir.</span><span class="sxs-lookup"><span data-stu-id="15db9-264">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="15db9-265">İzin verilen OıDC yanıt türleri kümesi, `id_token token` tek tek ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="15db9-265">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="15db9-266">İzin verilen yanıt modu `fragment` .</span><span class="sxs-lookup"><span data-stu-id="15db9-266">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="15db9-267">`IdentityServerJwt`: Sunucusuyla birlikte barındırılan bir API 'YI temsil eder Identity .</span><span class="sxs-lookup"><span data-stu-id="15db9-267">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="15db9-268">Uygulama, uygulama adı için varsayılan olarak kullanılan tek bir kapsama sahip olacak şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="15db9-268">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="15db9-269">`API`: Sunucusu ile barındırılmayan bir API 'YI temsil eder Identity .</span><span class="sxs-lookup"><span data-stu-id="15db9-269">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="15db9-270">Uygulama, uygulama adı için varsayılan olarak kullanılan tek bir kapsama sahip olacak şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="15db9-270">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="15db9-271">AppSettings aracılığıyla yapılandırma</span><span class="sxs-lookup"><span data-stu-id="15db9-271">Configuration through AppSettings</span></span>

<span data-ttu-id="15db9-272">Uygulamaları, veya listesine ekleyerek yapılandırma sistemi aracılığıyla yapılandırın `Clients` `Resources` .</span><span class="sxs-lookup"><span data-stu-id="15db9-272">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="15db9-273">`redirect_uri` `post_logout_redirect_uri` Aşağıdaki örnekte gösterildiği gibi, her bir istemcinin ve özelliğini yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="15db9-273">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

```json
"IdentityServer": {
  "Clients": {
    "MySPA": {
      "Profile": "SPA",
      "RedirectUri": "https://www.example.com/authentication/login-callback",
      "LogoutUri": "https://www.example.com/authentication/logout-callback"
    }
  }
}
```

<span data-ttu-id="15db9-274">Kaynakları yapılandırırken, kaynak için kapsamları aşağıda gösterildiği gibi yapılandırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="15db9-274">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

```json
"IdentityServer": {
  "Resources": {
    "MyExternalApi": {
      "Profile": "API",
      "Scopes": "a b c"
    }
  }
}
```

### <a name="configuration-through-code"></a><span data-ttu-id="15db9-275">Kod aracılığıyla yapılandırma</span><span class="sxs-lookup"><span data-stu-id="15db9-275">Configuration through code</span></span>

<span data-ttu-id="15db9-276">Ayrıca, `AddApiAuthorization` seçeneklerini yapılandırmak için bir eylem alan aşırı yüklemesini kullanarak, istemcileri ve kaynakları kod aracılığıyla da yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="15db9-276">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

```csharp
AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options =>
{
    options.Clients.AddSPA(
        "My SPA", spa =>
        spa.WithRedirectUri("http://www.example.com/authentication/login-callback")
           .WithLogoutRedirectUri(
               "http://www.example.com/authentication/logout-callback"));

    options.ApiResources.AddApiResource("MyExternalApi", resource =>
        resource.WithScopes("a", "b", "c"));
});
```

## <a name="additional-resources"></a><span data-ttu-id="15db9-277">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="15db9-277">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
