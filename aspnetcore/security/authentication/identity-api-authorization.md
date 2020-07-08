---
title: ASP.NET Core tek sayfalı uygulamalar için kimlik doğrulamaya giriş
author: javiercn
description: IdentityASP.NET Core uygulamasının içinde barındırılan tek sayfalı bir uygulamayla kullanın.
monikerRange: '>= aspnetcore-3.0'
ms.author: scaddie
ms.custom: mvc
ms.date: 11/08/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity/spa
ms.openlocfilehash: 2b587517268208dcf66cd2895b7aa22bfa381f84
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060364"
---
# <a name="authentication-and-authorization-for-spas"></a><span data-ttu-id="dae21-103">Maça kimlik doğrulaması ve yetkilendirme</span><span class="sxs-lookup"><span data-stu-id="dae21-103">Authentication and authorization for SPAs</span></span>

<span data-ttu-id="dae21-104">ASP.NET Core 3,0 veya üzeri, API yetkilendirmesi desteğini kullanarak tek sayfalı uygulamalarda (Spaon) kimlik doğrulaması sunmaktadır.</span><span class="sxs-lookup"><span data-stu-id="dae21-104">ASP.NET Core 3.0 or later offers authentication in Single Page Apps (SPAs) using the support for API authorization.</span></span> <span data-ttu-id="dae21-105">IdentityKimlik doğrulama ve depolama için ASP.NET Core, açık kimlik Connect uygulama Için [IdentityServer](https://identityserver.io/) ile birleştirilir.</span><span class="sxs-lookup"><span data-stu-id="dae21-105">ASP.NET Core Identity for authenticating and storing users is combined with [IdentityServer](https://identityserver.io/) for implementing Open ID Connect.</span></span>

<span data-ttu-id="dae21-106">Bir kimlik doğrulama parametresi, **angular** 'a eklenmiştir ve **Web uygulamasındaki (model-görünüm-denetleyici)** (MVC) ve **Web uygulaması** (sayfalar) proje şablonlarında kimlik doğrulama parametresine benzer olan proje şablonlarına **tepki** verir Razor .</span><span class="sxs-lookup"><span data-stu-id="dae21-106">An authentication parameter was added to the **Angular** and **React** project templates that is similar to the authentication parameter in the **Web Application (Model-View-Controller)** (MVC) and **Web Application** (Razor Pages) project templates.</span></span> <span data-ttu-id="dae21-107">İzin verilen parametre değerleri **none** ve **bireysel**.</span><span class="sxs-lookup"><span data-stu-id="dae21-107">The allowed parameter values are **None** and **Individual**.</span></span> <span data-ttu-id="dae21-108">**React.js ve Redux** proje şablonu, kimlik doğrulama parametresini Şu anda desteklemiyor.</span><span class="sxs-lookup"><span data-stu-id="dae21-108">The **React.js and Redux** project template doesn't support the authentication parameter at this time.</span></span>

## <a name="create-an-app-with-api-authorization-support"></a><span data-ttu-id="dae21-109">API yetkilendirme desteğiyle uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="dae21-109">Create an app with API authorization support</span></span>

<span data-ttu-id="dae21-110">Kullanıcı kimlik doğrulaması ve yetkilendirme, hem angular ile hem de maça 'Ları ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="dae21-110">User authentication and authorization can be used with both Angular and React SPAs.</span></span> <span data-ttu-id="dae21-111">Bir komut kabuğu açın ve şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="dae21-111">Open a command shell, and run the following command:</span></span>

<span data-ttu-id="dae21-112">**Angular**:</span><span class="sxs-lookup"><span data-stu-id="dae21-112">**Angular**:</span></span>

```dotnetcli
dotnet new angular -o <output_directory_name> -au Individual
```

<span data-ttu-id="dae21-113">**Tepki**verme:</span><span class="sxs-lookup"><span data-stu-id="dae21-113">**React**:</span></span>

```dotnetcli
dotnet new react -o <output_directory_name> -au Individual
```

<span data-ttu-id="dae21-114">Yukarıdaki komut, SPA 'yı içeren *clientapp* dizinine sahip bir ASP.NET Core uygulaması oluşturur.</span><span class="sxs-lookup"><span data-stu-id="dae21-114">The preceding command creates an ASP.NET Core app with a *ClientApp* directory containing the SPA.</span></span>

## <a name="general-description-of-the-aspnet-core-components-of-the-app"></a><span data-ttu-id="dae21-115">Uygulamanın ASP.NET Core bileşenlerinin genel açıklaması</span><span class="sxs-lookup"><span data-stu-id="dae21-115">General description of the ASP.NET Core components of the app</span></span>

<span data-ttu-id="dae21-116">Aşağıdaki bölümlerde, kimlik doğrulama desteği dahil edildiğinde projenin eklemeleri açıklanır:</span><span class="sxs-lookup"><span data-stu-id="dae21-116">The following sections describe additions to the project when authentication support is included:</span></span>

### <a name="startup-class"></a><span data-ttu-id="dae21-117">Başlangıç sınıfı</span><span class="sxs-lookup"><span data-stu-id="dae21-117">Startup class</span></span>

<span data-ttu-id="dae21-118">Aşağıdaki kod örnekleri [Microsoft. AspNetCore. ApiAuthorization. IdentityServer](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet paketini kullanır.</span><span class="sxs-lookup"><span data-stu-id="dae21-118">The following code examples rely on the [Microsoft.AspNetCore.ApiAuthorization.IdentityServer](https://www.nuget.org/packages/Microsoft.AspNetCore.ApiAuthorization.IdentityServer) NuGet package.</span></span> <span data-ttu-id="dae21-119">Örnekler, <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> ve genişletme yöntemlerini kullanarak API kimlik doğrulaması ve yetkilendirme yapılandırır <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="dae21-119">The examples configure API authentication and authorization using the <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> and <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiResourceCollection.AddIdentityServerJwt%2A> extension methods.</span></span> <span data-ttu-id="dae21-120">Kimlik doğrulamasıyla tepki verme veya angular SPA proje şablonlarını kullanan projeler, bu pakete bir başvuru içerir.</span><span class="sxs-lookup"><span data-stu-id="dae21-120">Projects using the React or Angular SPA project templates with authentication include a reference to this package.</span></span>

<span data-ttu-id="dae21-121">`Startup`Sınıfı aşağıdaki eklemelere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="dae21-121">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="dae21-122">Yöntemin içinde `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="dae21-122">Inside the `Startup.ConfigureServices` method:</span></span>
  * Identity<span data-ttu-id="dae21-123">Varsayılan Kullanıcı arabirimi ile:</span><span class="sxs-lookup"><span data-stu-id="dae21-123"> with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="dae21-124">IdentityServer `AddApiAuthorization` 'ın en üstünde bazı varsayılan ASP.NET Core kuralları ayarlayan ek bir yardımcı yöntemi olan IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="dae21-124">IdentityServer with an additional `AddApiAuthorization` helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="dae21-125">Kimliği `AddIdentityServerJwt` , IdentityServer tarafından ÜRETILEN JWT belirteçlerini doğrulamak üzere uygulamayı yapılandıran ek bir yardımcı yöntem ile kimlik doğrulaması:</span><span class="sxs-lookup"><span data-stu-id="dae21-125">Authentication with an additional `AddIdentityServerJwt` helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="dae21-126">Yöntemin içinde `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="dae21-126">Inside the `Startup.Configure` method:</span></span>
  * <span data-ttu-id="dae21-127">İstek kimlik bilgilerini doğrulamadan ve Kullanıcı istek bağlamında ayarlamaktan sorumlu kimlik doğrulama ara yazılımı:</span><span class="sxs-lookup"><span data-stu-id="dae21-127">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="dae21-128">Açık KIMLIK bağlantı noktalarını kullanıma sunan IdentityServer ara yazılımı:</span><span class="sxs-lookup"><span data-stu-id="dae21-128">The IdentityServer middleware that exposes the Open ID Connect endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="dae21-129">Addadpiauthorization</span><span class="sxs-lookup"><span data-stu-id="dae21-129">AddApiAuthorization</span></span>

<span data-ttu-id="dae21-130">Bu yardımcı yöntemi, IdentityServer 'ı desteklenen yapılandırmamızı kullanacak şekilde yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="dae21-130">This helper method configures IdentityServer to use our supported configuration.</span></span> <span data-ttu-id="dae21-131">IdentityServer, uygulama güvenliği sorunlarını işlemeye yönelik güçlü ve genişletilebilir bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="dae21-131">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="dae21-132">Aynı zamanda, en yaygın senaryolar için gereksiz karmaşıklık sunan.</span><span class="sxs-lookup"><span data-stu-id="dae21-132">At the same time, that exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="dae21-133">Sonuç olarak, size iyi bir başlangıç noktası olarak kabul edilen bir dizi kural ve yapılandırma seçeneği sağlanır.</span><span class="sxs-lookup"><span data-stu-id="dae21-133">Consequently, a set of conventions and configuration options is provided to you that are considered a good starting point.</span></span> <span data-ttu-id="dae21-134">Kimlik doğrulama gereksinimleriniz değiştikçe, IdentityServer 'ın tam gücü, kimlik doğrulamasını gereksinimlerinize uyacak şekilde özelleştirmek için hala kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="dae21-134">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit your needs.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="dae21-135">Addentityserverjwt</span><span class="sxs-lookup"><span data-stu-id="dae21-135">AddIdentityServerJwt</span></span>

<span data-ttu-id="dae21-136">Bu yardımcı yöntemi, varsayılan kimlik doğrulama işleyicisi olarak uygulama için bir ilke düzeni yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="dae21-136">This helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="dae21-137">İlke, Identity Identity "/" URL alanındaki herhangi bir alt yolda yönlendirilen tüm isteklerin işlemesini sağlamak üzere yapılandırılmıştır Identity .</span><span class="sxs-lookup"><span data-stu-id="dae21-137">The policy is configured to let Identity handle all requests routed to any subpath in the Identity URL space "/Identity".</span></span> <span data-ttu-id="dae21-138">`JwtBearerHandler`Diğer tüm istekleri işler.</span><span class="sxs-lookup"><span data-stu-id="dae21-138">The `JwtBearerHandler` handles all other requests.</span></span> <span data-ttu-id="dae21-139">Ayrıca, bu yöntem, `<<ApplicationName>>API` IdentityServer ile bir API kaynağını varsayılan kapsamına kaydeder ve bu `<<ApplicationName>>API` uygulama Için IdentityServer tarafından verilen belirteçleri doğrulamak üzere JWT taşıyıcı belirteç ara yazılımını yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="dae21-139">Additionally, this method registers an `<<ApplicationName>>API` API resource with IdentityServer with a default scope of `<<ApplicationName>>API` and configures the JWT Bearer token middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="dae21-140">Dalgalı bir denetleyici</span><span class="sxs-lookup"><span data-stu-id="dae21-140">WeatherForecastController</span></span>

<span data-ttu-id="dae21-141">*Controllers\dalgalı therforebir Controller.cs* dosyasında, `[Authorize]` kullanıcıya kaynağa erişim için varsayılan ilkeye göre yetkilendirilmiş olması gerektiğini belirten sınıfa uygulanan özniteliğe dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="dae21-141">In the *Controllers\WeatherForecastController.cs* file, notice the `[Authorize]` attribute applied to the class that indicates that the user needs to be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="dae21-142">Varsayılan yetkilendirme ilkesi, yukarıda belirtilen ilke şemasına tarafından yapılandırılan varsayılan kimlik doğrulama şemasını kullanacak şekilde yapılandırılmıştır `AddIdentityServerJwt` `JwtBearerHandler` . Bu, bu tür bir yardımcı yöntemi tarafından yapılandırılmış bir uygulama istekleri için varsayılan işleyicidir.</span><span class="sxs-lookup"><span data-stu-id="dae21-142">The default authorization policy happens to be configured to use the default authentication scheme, which is set up by `AddIdentityServerJwt` to the policy scheme that was mentioned above, making the `JwtBearerHandler` configured by such helper method the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="dae21-143">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="dae21-143">ApplicationDbContext</span></span>

<span data-ttu-id="dae21-144">*Data\applicationdbcontext.cs* dosyasında, ' `DbContext` nin Identity `ApiAuthorizationDbContext` (öğesinden daha fazla türetilmiş bir sınıf `IdentityDbContext` ) IdentityServer şemasını dahil etmek için kullandığı özel durum ile birlikte kullanıldığına dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="dae21-144">In the *Data\ApplicationDbContext.cs* file, notice the same `DbContext` is used in Identity with the exception that it extends `ApiAuthorizationDbContext` (a more derived class from `IdentityDbContext`) to include the schema for IdentityServer.</span></span>

<span data-ttu-id="dae21-145">Veritabanı şemasının tam denetimini elde etmek için, kullanılabilir Identity `DbContext` sınıflardan birini ve Identity yöntemi çağırarak şemayı içerecek şekilde yapılandırın `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` .</span><span class="sxs-lookup"><span data-stu-id="dae21-145">To gain full control of the database schema, inherit from one of the available Identity `DbContext` classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` on the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="dae21-146">Oıdcconfigurationcontroller</span><span class="sxs-lookup"><span data-stu-id="dae21-146">OidcConfigurationController</span></span>

<span data-ttu-id="dae21-147">*Controllers\oıdcconfigurationcontroller.cs* dosyasında, istemcinin kullanması gereken OIDC parametrelerine hizmeti sağlaması için sağlanan uç noktaya dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="dae21-147">In the *Controllers\OidcConfigurationController.cs* file, notice the endpoint that's provisioned to serve the OIDC parameters that the client needs to use.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="dae21-148">Üzerinde appsettings.js</span><span class="sxs-lookup"><span data-stu-id="dae21-148">appsettings.json</span></span>

<span data-ttu-id="dae21-149">Proje kökünün dosyasındaki *appsettings.js* , `IdentityServer` yapılandırılmış istemciler listesini açıklayan yeni bir bölüm vardır.</span><span class="sxs-lookup"><span data-stu-id="dae21-149">In the *appsettings.json* file of the project root, there's a new `IdentityServer` section that describes the list of configured clients.</span></span> <span data-ttu-id="dae21-150">Aşağıdaki örnekte, tek bir istemci vardır.</span><span class="sxs-lookup"><span data-stu-id="dae21-150">In the following example, there's a single client.</span></span> <span data-ttu-id="dae21-151">İstemci adı, uygulama adına karşılık gelir ve kural tarafından OAuth `ClientId` parametresine eşlenir.</span><span class="sxs-lookup"><span data-stu-id="dae21-151">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="dae21-152">Profil, yapılandırılan uygulama türünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="dae21-152">The profile indicates the app type being configured.</span></span> <span data-ttu-id="dae21-153">Sunucu için yapılandırma işlemini basitleştiren kuralları yönlendirmek için dahili olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="dae21-153">It's used internally to drive conventions that simplify the configuration process for the server.</span></span> <span data-ttu-id="dae21-154">[Uygulama profilleri](#application-profiles) bölümünde açıklandığı gibi çeşitli profiller mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="dae21-154">There are several profiles available, as explained in the [Application profiles](#application-profiles) section.</span></span>

```json
"IdentityServer": {
  "Clients": {
    "angularindividualpreview3final": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

### <a name="appsettingsdevelopmentjson"></a><span data-ttu-id="dae21-155">Üzerinde appsettings.Development.js</span><span class="sxs-lookup"><span data-stu-id="dae21-155">appsettings.Development.json</span></span>

<span data-ttu-id="dae21-156">Proje kökünün dosyasındaki *appsettings.Development.js* , `IdentityServer` belirteçleri imzalamak için kullanılan anahtarı açıklayan bir bölüm vardır.</span><span class="sxs-lookup"><span data-stu-id="dae21-156">In the *appsettings.Development.json* file of the project root, there's an `IdentityServer` section that describes the key used to sign tokens.</span></span> <span data-ttu-id="dae21-157">Üretime dağıtım yaparken, [üretime dağıtma](#deploy-to-production) bölümünde açıklandığı gibi bir anahtarın uygulamayla birlikte sağlanması ve dağıtılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="dae21-157">When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section.</span></span>

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="general-description-of-the-angular-app"></a><span data-ttu-id="dae21-158">Angular uygulamasının genel açıklaması</span><span class="sxs-lookup"><span data-stu-id="dae21-158">General description of the Angular app</span></span>

<span data-ttu-id="dae21-159">Angular şablonundaki kimlik doğrulama ve API yetkilendirme desteği *Clientapp\src\api-Authorization* dizinindeki kendi angular modülünde yer alır.</span><span class="sxs-lookup"><span data-stu-id="dae21-159">The authentication and API authorization support in the Angular template resides in its own Angular module in the *ClientApp\src\api-authorization* directory.</span></span> <span data-ttu-id="dae21-160">Modül aşağıdaki öğelerden oluşur:</span><span class="sxs-lookup"><span data-stu-id="dae21-160">The module is composed of the following elements:</span></span>

* <span data-ttu-id="dae21-161">3 bileşen:</span><span class="sxs-lookup"><span data-stu-id="dae21-161">3 components:</span></span>
  * <span data-ttu-id="dae21-162">*login. Component. TS*: uygulamanın oturum açma akışını işler.</span><span class="sxs-lookup"><span data-stu-id="dae21-162">*login.component.ts*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="dae21-163">*Logout. Component. TS*: uygulamanın oturum kapatma akışını işler.</span><span class="sxs-lookup"><span data-stu-id="dae21-163">*logout.component.ts*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="dae21-164">*login-Menu. Component. TS*: aşağıdaki bağlantı kümelerinden birini görüntüleyen pencere öğesi:</span><span class="sxs-lookup"><span data-stu-id="dae21-164">*login-menu.component.ts*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="dae21-165">Kullanıcı profili yönetimi ve kullanıcının kimlik doğrulaması yapıldığında oturum kapatma bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="dae21-165">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="dae21-166">Kullanıcının kimlik doğrulaması olmadığında kayıt ve oturum açma bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="dae21-166">Registration and log in links when the user isn't authenticated.</span></span>
* <span data-ttu-id="dae21-167">`AuthorizeGuard`Rotalara eklenebilen ve yolu ziyaret etmeden önce bir kullanıcının kimliğinin doğrulanmasını gerektiren bir Route koruyucusu.</span><span class="sxs-lookup"><span data-stu-id="dae21-167">A route guard `AuthorizeGuard` that can be added to routes and requires a user to be authenticated before visiting the route.</span></span>
* <span data-ttu-id="dae21-168">`AuthorizeInterceptor`Kullanıcının kimlik doğrulaması yapıldığında API 'yi hedefleyen gıden http isteklerine erişim belirtecini bağlayan BIR http yakalayıcısı.</span><span class="sxs-lookup"><span data-stu-id="dae21-168">An HTTP interceptor `AuthorizeInterceptor` that attaches the access token to outgoing HTTP requests targeting the API when the user is authenticated.</span></span>
* <span data-ttu-id="dae21-169">`AuthorizeService`Kimlik doğrulama işleminin alt düzey ayrıntılarını işleyen ve kimliği doğrulanmış kullanıcı hakkındaki bilgileri, tüketim için uygulamanın geri kalanına getiren bir hizmet.</span><span class="sxs-lookup"><span data-stu-id="dae21-169">A service `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>
* <span data-ttu-id="dae21-170">Uygulamanın kimlik doğrulama bölümleriyle ilişkili yolları tanımlayan angular modülü.</span><span class="sxs-lookup"><span data-stu-id="dae21-170">An Angular module that defines routes associated with the authentication parts of the app.</span></span> <span data-ttu-id="dae21-171">Oturum açma menü bileşeni, yakalayıcısı, koruyucu ve uygulamanın geri kalanından tüketim için hizmeti sunar.</span><span class="sxs-lookup"><span data-stu-id="dae21-171">It exposes the login menu component, the interceptor, the guard, and the service for consumption from the rest of the app.</span></span>

## <a name="general-description-of-the-react-app"></a><span data-ttu-id="dae21-172">Tepki verme uygulamasının genel açıklaması</span><span class="sxs-lookup"><span data-stu-id="dae21-172">General description of the React app</span></span>

<span data-ttu-id="dae21-173">Yanıt verme şablonunda kimlik doğrulama ve API yetkilendirmesi desteği *Clientapp\src\\disk api-Authorization* dizininde bulunur.</span><span class="sxs-lookup"><span data-stu-id="dae21-173">The support for authentication and API authorization in the React template resides in the *ClientApp\src\components\api-authorization* directory.</span></span> <span data-ttu-id="dae21-174">Şu öğelerden oluşur:</span><span class="sxs-lookup"><span data-stu-id="dae21-174">It's composed of the following elements:</span></span>

* <span data-ttu-id="dae21-175">4 bileşen:</span><span class="sxs-lookup"><span data-stu-id="dae21-175">4 components:</span></span>
  * <span data-ttu-id="dae21-176">*Login.js*: uygulamanın oturum açma akışını işler.</span><span class="sxs-lookup"><span data-stu-id="dae21-176">*Login.js*: Handles the app's login flow.</span></span>
  * <span data-ttu-id="dae21-177">*Logout.js*: uygulamanın oturum kapatma akışını işler.</span><span class="sxs-lookup"><span data-stu-id="dae21-177">*Logout.js*: Handles the app's logout flow.</span></span>
  * <span data-ttu-id="dae21-178">*LoginMenu.js*: aşağıdaki bağlantı kümelerinden birini görüntüleyen pencere öğesi:</span><span class="sxs-lookup"><span data-stu-id="dae21-178">*LoginMenu.js*: A widget that displays one of the following sets of links:</span></span>
    * <span data-ttu-id="dae21-179">Kullanıcı profili yönetimi ve kullanıcının kimlik doğrulaması yapıldığında oturum kapatma bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="dae21-179">User profile management and log out links when the user is authenticated.</span></span>
    * <span data-ttu-id="dae21-180">Kullanıcının kimlik doğrulaması olmadığında kayıt ve oturum açma bağlantıları.</span><span class="sxs-lookup"><span data-stu-id="dae21-180">Registration and log in links when the user isn't authenticated.</span></span>
  * <span data-ttu-id="dae21-181">*AuthorizeRoute.js*: bir kullanıcının, parametresinde belirtilen bileşeni işlemeden önce kimliğinin doğrulanmasını gerektiren bir rota bileşeni `Component` .</span><span class="sxs-lookup"><span data-stu-id="dae21-181">*AuthorizeRoute.js*: A route component that requires a user to be authenticated before rendering the component indicated in the `Component` parameter.</span></span>
* <span data-ttu-id="dae21-182">`authService` `AuthorizeService` Kimlik doğrulama işleminin alt düzey ayrıntılarını işleyen ve kimliği doğrulanmış kullanıcı hakkındaki bilgileri, tüketim için uygulamanın geri kalanına getiren, dışa aktarılmış bir sınıf örneği.</span><span class="sxs-lookup"><span data-stu-id="dae21-182">An exported `authService` instance of class `AuthorizeService` that handles the lower-level details of the authentication process and exposes information about the authenticated user to the rest of the app for consumption.</span></span>

<span data-ttu-id="dae21-183">Artık çözümün ana bileşenlerini gördüğünüze göre, uygulama için ayrı senaryolara daha ayrıntılı bir şekilde göz atabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="dae21-183">Now that you've seen the main components of the solution, you can take a deeper look at individual scenarios for the app.</span></span>

## <a name="require-authorization-on-a-new-api"></a><span data-ttu-id="dae21-184">Yeni bir API 'de yetkilendirme gerektir</span><span class="sxs-lookup"><span data-stu-id="dae21-184">Require authorization on a new API</span></span>

<span data-ttu-id="dae21-185">Varsayılan olarak, sistem yeni API 'Ler için kolayca yetkilendirme gerektirecek şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="dae21-185">By default, the system is configured to easily require authorization for new APIs.</span></span> <span data-ttu-id="dae21-186">Bunu yapmak için yeni bir denetleyici oluşturun ve `[Authorize]` özniteliği Controller sınıfına veya denetleyici içindeki herhangi bir eyleme ekleyin.</span><span class="sxs-lookup"><span data-stu-id="dae21-186">To do so, create a new controller and add the `[Authorize]` attribute to the controller class or to any action within the controller.</span></span>

## <a name="customize-the-api-authentication-handler"></a><span data-ttu-id="dae21-187">API kimlik doğrulama işleyicisini özelleştirme</span><span class="sxs-lookup"><span data-stu-id="dae21-187">Customize the API authentication handler</span></span>

<span data-ttu-id="dae21-188">API 'nin JWT işleyicisinin yapılandırmasını özelleştirmek için, <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> örneğini yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="dae21-188">To customize the configuration of the API's JWT handler, configure its <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> instance:</span></span>

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

<span data-ttu-id="dae21-189">API 'nin JWT işleyicisi, kullanarak kimlik doğrulama işlemi üzerinde denetimi etkinleştiren olaylar oluşturur `JwtBearerEvents` .</span><span class="sxs-lookup"><span data-stu-id="dae21-189">The API's JWT handler raises events that enable control over the authentication process using `JwtBearerEvents`.</span></span> <span data-ttu-id="dae21-190">API yetkilendirmesi için destek sağlamak üzere `AddIdentityServerJwt` kendi olay işleyicilerini kaydeder.</span><span class="sxs-lookup"><span data-stu-id="dae21-190">To provide support for API authorization, `AddIdentityServerJwt` registers its own event handlers.</span></span>

<span data-ttu-id="dae21-191">Bir olayın işlenmesini özelleştirmek için, var olan olay işleyicisini gereken ek mantığla sarın.</span><span class="sxs-lookup"><span data-stu-id="dae21-191">To customize the handling of an event, wrap the existing event handler with additional logic as required.</span></span> <span data-ttu-id="dae21-192">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="dae21-192">For example:</span></span>

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

<span data-ttu-id="dae21-193">Önceki kodda, `OnTokenValidated` olay işleyicisi özel bir uygulamayla değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="dae21-193">In the preceding code, the `OnTokenValidated` event handler is replaced with a custom implementation.</span></span> <span data-ttu-id="dae21-194">Bu uygulama:</span><span class="sxs-lookup"><span data-stu-id="dae21-194">This implementation:</span></span>

1. <span data-ttu-id="dae21-195">API yetkilendirme desteği tarafından sunulan özgün uygulamayı çağırır.</span><span class="sxs-lookup"><span data-stu-id="dae21-195">Calls the original implementation provided by the API authorization support.</span></span>
1. <span data-ttu-id="dae21-196">Kendi özel mantığını çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="dae21-196">Run its own custom logic.</span></span>

## <a name="protect-a-client-side-route-angular"></a><span data-ttu-id="dae21-197">İstemci tarafı yolunu koruma (angular)</span><span class="sxs-lookup"><span data-stu-id="dae21-197">Protect a client-side route (Angular)</span></span>

<span data-ttu-id="dae21-198">İstemci tarafı bir yolu korumak, yetkilendirme koruyucusu bir rota yapılandırılırken çalıştırılacak korumalara listesine eklenerek yapılır.</span><span class="sxs-lookup"><span data-stu-id="dae21-198">Protecting a client-side route is done by adding the authorize guard to the list of guards to run when configuring a route.</span></span> <span data-ttu-id="dae21-199">Örnek olarak, `fetch-data` yolun ana uygulama angular modülü içinde nasıl yapılandırıldığını görebilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="dae21-199">As an example, you can see how the `fetch-data` route is configured within the main app Angular module:</span></span>

```typescript
RouterModule.forRoot([
  // ...
  { path: 'fetch-data', component: FetchDataComponent, canActivate: [AuthorizeGuard] },
])
```

<span data-ttu-id="dae21-200">Bir yolu korumanın gerçek uç noktayı korumadığını (buna hala bir `[Authorize]` öznitelik uygulanacağını), ancak kullanıcının kimlik doğrulaması olmadığında verilen istemci tarafı rotasında gezinmelerini önlediği bahsetmek önemlidir.</span><span class="sxs-lookup"><span data-stu-id="dae21-200">It's important to mention that protecting a route doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it) but that it only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-angular"></a><span data-ttu-id="dae21-201">API isteklerinin kimliğini doğrulama (angular)</span><span class="sxs-lookup"><span data-stu-id="dae21-201">Authenticate API requests (Angular)</span></span>

<span data-ttu-id="dae21-202">Uygulama ile barındırılan API 'lere yönelik kimlik doğrulama istekleri, uygulama tarafından tanımlanan HTTP istemci yakalayıcısı kullanılarak otomatik olarak yapılır.</span><span class="sxs-lookup"><span data-stu-id="dae21-202">Authenticating requests to APIs hosted alongside the app is done automatically through the use of the HTTP client interceptor defined by the app.</span></span>

## <a name="protect-a-client-side-route-react"></a><span data-ttu-id="dae21-203">İstemci tarafı yolunu koruma (tepki verme)</span><span class="sxs-lookup"><span data-stu-id="dae21-203">Protect a client-side route (React)</span></span>

<span data-ttu-id="dae21-204">`AuthorizeRoute`Düz bileşen yerine bileşeni kullanarak bir istemci tarafı yolunu koruyun `Route` .</span><span class="sxs-lookup"><span data-stu-id="dae21-204">Protect a client-side route by using the `AuthorizeRoute` component instead of the plain `Route` component.</span></span> <span data-ttu-id="dae21-205">Örneğin, `fetch-data` yolun bileşen içinde nasıl yapılandırıldığına dikkat edin `App` :</span><span class="sxs-lookup"><span data-stu-id="dae21-205">For example, notice how the `fetch-data` route is configured within the `App` component:</span></span>

```jsx
<AuthorizeRoute path='/fetch-data' component={FetchData} />
```

<span data-ttu-id="dae21-206">Bir yolu koruma:</span><span class="sxs-lookup"><span data-stu-id="dae21-206">Protecting a route:</span></span>

* <span data-ttu-id="dae21-207">Gerçek uç noktayı korumaz (yine de `[Authorize]` ona uygulanan bir özniteliği gerektirir).</span><span class="sxs-lookup"><span data-stu-id="dae21-207">Doesn't protect the actual endpoint (which still requires an `[Authorize]` attribute applied to it).</span></span>
* <span data-ttu-id="dae21-208">Yalnızca kullanıcının kimlik doğrulaması olmadığında verilen istemci tarafı rotasında gezinmelerini engeller.</span><span class="sxs-lookup"><span data-stu-id="dae21-208">Only prevents the user from navigating to the given client-side route when it isn't authenticated.</span></span>

## <a name="authenticate-api-requests-react"></a><span data-ttu-id="dae21-209">API isteklerinin kimliğini doğrulama (tepki)</span><span class="sxs-lookup"><span data-stu-id="dae21-209">Authenticate API requests (React)</span></span>

<span data-ttu-id="dae21-210">Yanıt vererek istekleri kimlik doğrulama işlemi, önce örneği öğesinden içeri aktarılarak yapılır `authService` `AuthorizeService` .</span><span class="sxs-lookup"><span data-stu-id="dae21-210">Authenticating requests with React is done by first importing the `authService` instance from the `AuthorizeService`.</span></span> <span data-ttu-id="dae21-211">Erişim belirteci konumundan alınır `authService` ve aşağıda gösterildiği gibi isteğe iliştirilir.</span><span class="sxs-lookup"><span data-stu-id="dae21-211">The access token is retrieved from the `authService` and is attached to the request as shown below.</span></span> <span data-ttu-id="dae21-212">Yanıt verme bileşenlerinde, bu çalışma genellikle `componentDidMount` yaşam döngüsü yönteminde veya bazı Kullanıcı etkileşiminden elde edilen sonuç olarak yapılır.</span><span class="sxs-lookup"><span data-stu-id="dae21-212">In React components, this work is typically done in the `componentDidMount` lifecycle method or as the result from some user interaction.</span></span>

### <a name="import-the-authservice-into-your-component"></a><span data-ttu-id="dae21-213">AuthService 'i bileşeninizdeki içeri aktarın</span><span class="sxs-lookup"><span data-stu-id="dae21-213">Import the authService into your component</span></span>

```javascript
import authService from './api-authorization/AuthorizeService'
```

### <a name="retrieve-and-attach-the-access-token-to-the-response"></a><span data-ttu-id="dae21-214">Erişim belirtecini alma ve yanıta iliştirme</span><span class="sxs-lookup"><span data-stu-id="dae21-214">Retrieve and attach the access token to the response</span></span>

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

## <a name="deploy-to-production"></a><span data-ttu-id="dae21-215">Üretime dağıtma</span><span class="sxs-lookup"><span data-stu-id="dae21-215">Deploy to production</span></span>

<span data-ttu-id="dae21-216">Uygulamayı üretime dağıtmak için aşağıdaki kaynakların sağlanması gerekir:</span><span class="sxs-lookup"><span data-stu-id="dae21-216">To deploy the app to production, the following resources need to be provisioned:</span></span>

* <span data-ttu-id="dae21-217">Kullanıcı hesaplarını depolamak için bir veritabanı Identity ve IdentityServer izin verir.</span><span class="sxs-lookup"><span data-stu-id="dae21-217">A database to store the Identity user accounts and the IdentityServer grants.</span></span>
* <span data-ttu-id="dae21-218">Belirteçleri imzalamak için kullanılacak bir üretim sertifikası.</span><span class="sxs-lookup"><span data-stu-id="dae21-218">A production certificate to use for signing tokens.</span></span>
  * <span data-ttu-id="dae21-219">Bu sertifika için belirli bir gereksinim yoktur; otomatik olarak imzalanan bir sertifika veya bir CA yetkilisi tarafından sağlanan bir sertifika olabilir.</span><span class="sxs-lookup"><span data-stu-id="dae21-219">There are no specific requirements for this certificate; it can be a self-signed certificate or a certificate provisioned through a CA authority.</span></span>
  * <span data-ttu-id="dae21-220">PowerShell veya OpenSSL gibi standart araçlarla oluşturulabilir.</span><span class="sxs-lookup"><span data-stu-id="dae21-220">It can be generated through standard tools like PowerShell or OpenSSL.</span></span>
  * <span data-ttu-id="dae21-221">Hedef makinelerdeki sertifika deposuna yüklenebilir veya güçlü bir parolayla bir *. pfx* dosyası olarak dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="dae21-221">It can be installed into the certificate store on the target machines or deployed as a *.pfx* file with a strong password.</span></span>

### <a name="example-deploy-to-azure-app-service"></a><span data-ttu-id="dae21-222">Örnek: Azure App Service dağıt</span><span class="sxs-lookup"><span data-stu-id="dae21-222">Example: Deploy to Azure App Service</span></span>

<span data-ttu-id="dae21-223">Bu bölümde, sertifika deposunda depolanan bir sertifikayı kullanarak Azure App Service uygulamanın dağıtımı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="dae21-223">This section describes deploying the app to Azure App Service using a certificate stored in the certificate store.</span></span> <span data-ttu-id="dae21-224">Uygulamayı sertifika deposundan bir sertifika yükleyecek şekilde değiştirmek için, daha sonraki bir adımda Azure portal uygulamayı yapılandırdığınızda standart bir katman hizmeti planı veya daha iyi bir seçenektir.</span><span class="sxs-lookup"><span data-stu-id="dae21-224">To modify the app to load a certificate from the certificate store, a Standard tier service plan or better is required when you configure the app in the Azure portal in a later step.</span></span>

<span data-ttu-id="dae21-225">Uygulamanın dosyada *appsettings.js* , `IdentityServer` önemli ayrıntıları içerecek şekilde bölümünü değiştirin:</span><span class="sxs-lookup"><span data-stu-id="dae21-225">In the app's *appsettings.json* file, modify the `IdentityServer` section to include the key details:</span></span>

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

* <span data-ttu-id="dae21-226">Mağaza adı, sertifikanın depolandığı sertifika deposunun adını temsil eder.</span><span class="sxs-lookup"><span data-stu-id="dae21-226">The store name represents the name of the certificate store where the certificate is stored.</span></span> <span data-ttu-id="dae21-227">Bu durumda, kişisel Kullanıcı deposuna işaret eder.</span><span class="sxs-lookup"><span data-stu-id="dae21-227">In this case, it points to the personal user store.</span></span>
* <span data-ttu-id="dae21-228">Depolama konumu, sertifikanın nereden yükleneceğini (veya) temsil eder `CurrentUser` `LocalMachine` .</span><span class="sxs-lookup"><span data-stu-id="dae21-228">The store location represents where to load the certificate from (`CurrentUser` or `LocalMachine`).</span></span>
* <span data-ttu-id="dae21-229">Sertifikadaki ad özelliği, sertifikanın ayırt edici konusuna karşılık gelir.</span><span class="sxs-lookup"><span data-stu-id="dae21-229">The name property on certificate corresponds with the distinguished subject for the certificate.</span></span>

<span data-ttu-id="dae21-230">Azure App Service dağıtmak için, [uygulamayı Azure 'A dağıtma](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure)' daki adımları Izleyerek gerekli Azure kaynaklarının nasıl oluşturulduğunu ve uygulamayı üretime dağıtmayı açıklar.</span><span class="sxs-lookup"><span data-stu-id="dae21-230">To deploy to Azure App Service, follow the steps in [Deploy the app to Azure](xref:tutorials/publish-to-azure-webapp-using-vs#deploy-the-app-to-azure), which explains how to create the necessary Azure resources and deploy the app to production.</span></span>

<span data-ttu-id="dae21-231">Yukarıdaki yönergeleri uyguladıktan sonra, uygulama Azure 'a dağıtılır ancak henüz işlevsel değildir.</span><span class="sxs-lookup"><span data-stu-id="dae21-231">After following the preceding instructions, the app is deployed to Azure but isn't yet functional.</span></span> <span data-ttu-id="dae21-232">Uygulama tarafından kullanılan sertifikanın Azure portal yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="dae21-232">The certificate used by the app must be configured in the Azure portal.</span></span> <span data-ttu-id="dae21-233">Sertifika için parmak izini bulun ve [sertifikalarınızı yükleme](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code)bölümünde açıklanan adımları izleyin.</span><span class="sxs-lookup"><span data-stu-id="dae21-233">Locate the thumbprint for the certificate and follow the steps described in [Load your certificates](/azure/app-service/app-service-web-ssl-cert-load#load-the-certificate-in-code).</span></span>

<span data-ttu-id="dae21-234">Bu adımlar SSL 'den bahsetirken, uygulama ile kullanmak üzere sağlanan sertifikayı karşıya yükleyebileceğiniz Azure portal **özel sertifikalar** bölümü vardır.</span><span class="sxs-lookup"><span data-stu-id="dae21-234">While these steps mention SSL, there's a **Private certificates** section in the Azure portal where you can upload the provisioned certificate to use with the app.</span></span>

<span data-ttu-id="dae21-235">Azure portal uygulamayı ve uygulamanın ayarlarını yapılandırdıktan sonra, uygulamayı portalda yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="dae21-235">After configuring the app and the app's settings in the Azure portal, restart the app in the portal.</span></span>

## <a name="other-configuration-options"></a><span data-ttu-id="dae21-236">Diğer yapılandırma seçenekleri</span><span class="sxs-lookup"><span data-stu-id="dae21-236">Other configuration options</span></span>

<span data-ttu-id="dae21-237">API yetkilendirmesi desteği, IdentityServer 'ın en üstünde bir dizi kural, varsayılan değer ve, maça deneyimini basitleştirecek geliştirmeler oluşturur.</span><span class="sxs-lookup"><span data-stu-id="dae21-237">The support for API authorization builds on top of IdentityServer with a set of conventions, default values, and enhancements to simplify the experience for SPAs.</span></span> <span data-ttu-id="dae21-238">Daha az ki, ASP.NET Core tümleştirmeler senaryonuzu kapsamadıysanız, IdentityServer 'ın tam gücü arka planda kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="dae21-238">Needless to say, the full power of IdentityServer is available behind the scenes if the ASP.NET Core integrations don't cover your scenario.</span></span> <span data-ttu-id="dae21-239">ASP.NET Core desteği, tüm uygulamaların kuruluşumuza göre oluşturulduğu ve dağıtıldığı "birinci taraf" uygulamalara odaklanır.</span><span class="sxs-lookup"><span data-stu-id="dae21-239">The ASP.NET Core support is focused on "first-party" apps, where all the apps are created and deployed by our organization.</span></span> <span data-ttu-id="dae21-240">Bu nedenle, izin veya Federasyon gibi şeyler için destek sunulmaz.</span><span class="sxs-lookup"><span data-stu-id="dae21-240">As such, support isn't offered for things like consent or federation.</span></span> <span data-ttu-id="dae21-241">Bu senaryolar için IdentityServer kullanın ve belgelerini izleyin.</span><span class="sxs-lookup"><span data-stu-id="dae21-241">For those scenarios, use IdentityServer and follow their documentation.</span></span>

### <a name="application-profiles"></a><span data-ttu-id="dae21-242">Uygulama profilleri</span><span class="sxs-lookup"><span data-stu-id="dae21-242">Application profiles</span></span>

<span data-ttu-id="dae21-243">Uygulama profilleri, parametrelerini daha fazla tanımlayan uygulamalar için önceden tanımlanmış yapılandırlardır.</span><span class="sxs-lookup"><span data-stu-id="dae21-243">Application profiles are predefined configurations for apps that further define their parameters.</span></span> <span data-ttu-id="dae21-244">Şu anda, aşağıdaki profiller desteklenir:</span><span class="sxs-lookup"><span data-stu-id="dae21-244">At this time, the following profiles are supported:</span></span>

* <span data-ttu-id="dae21-245">`IdentityServerSPA`: IdentityServer 'ın yanı sıra tek bir birim olarak barındırılan bir SPA 'yı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="dae21-245">`IdentityServerSPA`: Represents a SPA hosted alongside IdentityServer as a single unit.</span></span>
  * <span data-ttu-id="dae21-246">`redirect_uri`Varsayılan olarak olur `/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="dae21-246">The `redirect_uri` defaults to `/authentication/login-callback`.</span></span>
  * <span data-ttu-id="dae21-247">`post_logout_redirect_uri`Varsayılan olarak olur `/authentication/logout-callback` .</span><span class="sxs-lookup"><span data-stu-id="dae21-247">The `post_logout_redirect_uri` defaults to `/authentication/logout-callback`.</span></span>
  * <span data-ttu-id="dae21-248">Kapsam kümesi `openid` , `profile` , ve uygulamadaki API 'ler için tanımlanan tüm kapsamları içerir.</span><span class="sxs-lookup"><span data-stu-id="dae21-248">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="dae21-249">İzin verilen OıDC yanıt türleri kümesi, `id_token token` tek tek ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="dae21-249">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="dae21-250">İzin verilen yanıt modu `fragment` .</span><span class="sxs-lookup"><span data-stu-id="dae21-250">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="dae21-251">`SPA`: IdentityServer ile barındırılmayan bir SPA 'yı temsil eder.</span><span class="sxs-lookup"><span data-stu-id="dae21-251">`SPA`: Represents a SPA that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="dae21-252">Kapsam kümesi `openid` , `profile` , ve uygulamadaki API 'ler için tanımlanan tüm kapsamları içerir.</span><span class="sxs-lookup"><span data-stu-id="dae21-252">The set of scopes includes the `openid`, `profile`, and every scope defined for the APIs in the app.</span></span>
  * <span data-ttu-id="dae21-253">İzin verilen OıDC yanıt türleri kümesi, `id_token token` tek tek ( `id_token` , `token` ).</span><span class="sxs-lookup"><span data-stu-id="dae21-253">The set of allowed OIDC response types is `id_token token` or each of them individually (`id_token`, `token`).</span></span>
  * <span data-ttu-id="dae21-254">İzin verilen yanıt modu `fragment` .</span><span class="sxs-lookup"><span data-stu-id="dae21-254">The allowed response mode is `fragment`.</span></span>
* <span data-ttu-id="dae21-255">`IdentityServerJwt`: IdentityServer ile birlikte barındırılan bir API 'YI temsil eder.</span><span class="sxs-lookup"><span data-stu-id="dae21-255">`IdentityServerJwt`: Represents an API that is hosted alongside with IdentityServer.</span></span>
  * <span data-ttu-id="dae21-256">Uygulama, uygulama adı için varsayılan olarak kullanılan tek bir kapsama sahip olacak şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="dae21-256">The app is configured to have a single scope that defaults to the app name.</span></span>
* <span data-ttu-id="dae21-257">`API`: IdentityServer ile barındırılmayan bir API 'YI temsil eder.</span><span class="sxs-lookup"><span data-stu-id="dae21-257">`API`: Represents an API that isn't hosted with IdentityServer.</span></span>
  * <span data-ttu-id="dae21-258">Uygulama, uygulama adı için varsayılan olarak kullanılan tek bir kapsama sahip olacak şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="dae21-258">The app is configured to have a single scope that defaults to the app name.</span></span>

### <a name="configuration-through-appsettings"></a><span data-ttu-id="dae21-259">AppSettings aracılığıyla yapılandırma</span><span class="sxs-lookup"><span data-stu-id="dae21-259">Configuration through AppSettings</span></span>

<span data-ttu-id="dae21-260">Uygulamaları, veya listesine ekleyerek yapılandırma sistemi aracılığıyla yapılandırın `Clients` `Resources` .</span><span class="sxs-lookup"><span data-stu-id="dae21-260">Configure the apps through the configuration system by adding them to the list of `Clients` or `Resources`.</span></span>

<span data-ttu-id="dae21-261">`redirect_uri` `post_logout_redirect_uri` Aşağıdaki örnekte gösterildiği gibi, her bir istemcinin ve özelliğini yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="dae21-261">Configure each client's `redirect_uri` and `post_logout_redirect_uri` property, as shown in the following example:</span></span>

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

<span data-ttu-id="dae21-262">Kaynakları yapılandırırken, kaynak için kapsamları aşağıda gösterildiği gibi yapılandırabilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="dae21-262">When configuring resources, you can configure the scopes for the resource as shown below:</span></span>

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

### <a name="configuration-through-code"></a><span data-ttu-id="dae21-263">Kod aracılığıyla yapılandırma</span><span class="sxs-lookup"><span data-stu-id="dae21-263">Configuration through code</span></span>

<span data-ttu-id="dae21-264">Ayrıca, `AddApiAuthorization` seçeneklerini yapılandırmak için bir eylem alan aşırı yüklemesini kullanarak, istemcileri ve kaynakları kod aracılığıyla da yapılandırabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="dae21-264">You can also configure the clients and resources through code using an overload of `AddApiAuthorization` that takes an action to configure options.</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="dae21-265">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="dae21-265">Additional resources</span></span>

* <xref:spa/angular>
* <xref:spa/react>
* <xref:security/authentication/scaffold-identity>
