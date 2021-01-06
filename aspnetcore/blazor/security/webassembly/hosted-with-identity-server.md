---
title: Barındırılan bir ASP.NET Core Blazor WebAssembly uygulamasının sunucuyla güvenliğini Identity sağlama
author: guardrex
description: Barındırılan bir ASP.NET Core uygulamasının sunucusuyla güvenliğini nasıl sağlayacağınızı öğrenin Blazor WebAssembly Identity .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 80196945bc6891d5517d7da0e07ca1b0debddd28
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854697"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a><span data-ttu-id="77a76-103">Blazor WebAssemblySunucu ile ASP.NET Core barındırılan bir uygulamanın güvenliğini sağlama Identity</span><span class="sxs-lookup"><span data-stu-id="77a76-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="77a76-104">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="77a76-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="77a76-105">Bu makalede, kullanıcıların ve API çağrılarının kimliğini doğrulamak için [ Identity sunucusunu](https://identityserver.io/) kullanan bir [barındırılan Blazor WebAssembly uygulamanın](xref:blazor/hosting-models#blazor-webassembly) nasıl oluşturulacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="77a76-105">This article explains how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="77a76-106">Tek başına veya barındırılan bir Blazor WebAssembly uygulamayı var olan bir dış sunucu örneğini kullanacak şekilde yapılandırmak için Identity içindeki yönergeleri izleyin <xref:blazor/security/webassembly/standalone-with-authentication-library> .</span><span class="sxs-lookup"><span data-stu-id="77a76-106">To configure a standalone or hosted Blazor WebAssembly app to use an existing, external Identity Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="77a76-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77a76-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="77a76-108">Blazor WebAssemblyKimlik doğrulama mekanizmasına sahip yeni bir proje oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="77a76-108">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="77a76-109">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda **Blazor WebAssembly uygulama** şablonunu seçtikten sonra, **kimlik doğrulaması** altında **Değiştir** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="77a76-109">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="77a76-110">ASP.NET Core sistemi kullanarak uygulama içinde kullanıcıları depolamak için **Kullanıcı hesaplarını depola ve uygulama Içi** **Kullanıcı hesapları** seçeneğini belirleyin [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="77a76-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="77a76-111">**Gelişmiş** bölümündeki **ASP.NET Core barındırılan** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="77a76-111">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="77a76-112">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="77a76-112">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="77a76-113">Blazor WebAssemblyBoş bir klasörde kimlik doğrulama mekanizmasına sahip yeni bir proje oluşturmak için, `Individual` `-au|--auth` ASP.NET Core sistemini kullanarak uygulama içinde kullanıcıları depolama seçeneğiyle birlikte kimlik doğrulama mekanizmasını belirtin [Identity](xref:security/authentication/identity) :</span><span class="sxs-lookup"><span data-stu-id="77a76-113">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="77a76-114">Yer tutucu</span><span class="sxs-lookup"><span data-stu-id="77a76-114">Placeholder</span></span>  | <span data-ttu-id="77a76-115">Örnek</span><span class="sxs-lookup"><span data-stu-id="77a76-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="77a76-116">Seçeneğiyle belirtilen çıktı konumu, `-o|--output` mevcut değilse bir proje klasörü oluşturur ve uygulamanın adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="77a76-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="77a76-117">Daha fazla bilgi için [`dotnet new`](/dotnet/core/tools/dotnet-new) .NET Core kılavuzundaki komutuna bakın.</span><span class="sxs-lookup"><span data-stu-id="77a76-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="77a76-118">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="77a76-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="77a76-119">Blazor WebAssemblyKimlik doğrulama mekanizmasına sahip yeni bir proje oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="77a76-119">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="77a76-120">**Yeni Blazor WebAssembly uygulamanızı yapılandırın** adımındaki **kimlik doğrulaması** açılan listesinden **bireysel kimlik doğrulaması (uygulama içi)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="77a76-120">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="77a76-121">Uygulama, ASP.NET Core uygulamada depolanan bireysel kullanıcılar için oluşturulur [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="77a76-121">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="77a76-122">**Barındırılan ASP.NET Core** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="77a76-122">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="77a76-123">*`Server`* Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="77a76-123">*`Server`* app configuration</span></span>

<span data-ttu-id="77a76-124">Aşağıdaki bölümlerde, kimlik doğrulama desteği dahil edildiğinde projenin eklemeleri açıklanır.</span><span class="sxs-lookup"><span data-stu-id="77a76-124">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="77a76-125">Başlangıç sınıfı</span><span class="sxs-lookup"><span data-stu-id="77a76-125">Startup class</span></span>

<span data-ttu-id="77a76-126">`Startup`Sınıfı aşağıdaki eklemeleri içerir.</span><span class="sxs-lookup"><span data-stu-id="77a76-126">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="77a76-127">`Startup.ConfigureServices` içinde:</span><span class="sxs-lookup"><span data-stu-id="77a76-127">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="77a76-128">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="77a76-128">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="77a76-129">Identity<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Sunucu üstünde varsayılan ASP.NET Core kuralları ayarlayan ek bir yardımcı yöntemi olan sunucu Identity :</span><span class="sxs-lookup"><span data-stu-id="77a76-129">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="77a76-130"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Sunucu tarafından ÜRETILEN JWT belirteçlerini doğrulamak üzere uygulamayı yapılandıran ek bir yardımcı yöntem ile kimlik doğrulaması Identity :</span><span class="sxs-lookup"><span data-stu-id="77a76-130">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="77a76-131">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="77a76-131">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="77a76-132">IdentitySunucu ara sunucusu, OpenID Connect (OıDC) uç noktalarını kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="77a76-132">The IdentityServer middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="77a76-133">Kimlik doğrulama ara yazılımı, istek kimlik bilgilerini doğrulamadan ve Kullanıcı istek bağlamında ayarlamaktan sorumludur:</span><span class="sxs-lookup"><span data-stu-id="77a76-133">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="77a76-134">Yetki ara yazılımı yetkilendirme özelliklerini sunar:</span><span class="sxs-lookup"><span data-stu-id="77a76-134">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="77a76-135">Addadpiauthorization</span><span class="sxs-lookup"><span data-stu-id="77a76-135">AddApiAuthorization</span></span>

<span data-ttu-id="77a76-136"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Yardımcı yöntemi, ASP.NET Core senaryolar için [ Identity sunucuyu](https://identityserver.io/) yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="77a76-136">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="77a76-137">IdentitySunucu, uygulama güvenliği sorunlarını işlemeye yönelik güçlü ve genişletilebilir bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="77a76-137">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="77a76-138">IdentitySunucu, en yaygın senaryolar için gereksiz karmaşıklık sunar.</span><span class="sxs-lookup"><span data-stu-id="77a76-138">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="77a76-139">Sonuç olarak, iyi bir başlangıç noktası düşüntiğimiz bir dizi kural ve yapılandırma seçeneği sağlanır.</span><span class="sxs-lookup"><span data-stu-id="77a76-139">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="77a76-140">Kimlik doğrulama gereksinimleriniz değiştikçe, sunucunun tam gücü, Identity kimlik doğrulamasını uygulamanın gereksinimlerine uyacak şekilde özelleştirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="77a76-140">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="77a76-141">IdentityServerjwt Ekle</span><span class="sxs-lookup"><span data-stu-id="77a76-141">AddIdentityServerJwt</span></span>

<span data-ttu-id="77a76-142"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Yardımcı yöntemi, varsayılan kimlik doğrulama işleyicisi olarak uygulama için bir ilke düzeni yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="77a76-142">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="77a76-143">İlke, Identity URL alanındaki herhangi bir alt yolda yönlendirilen tüm istekleri işlemeye izin verecek şekilde yapılandırılmıştır Identity `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="77a76-143">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="77a76-144"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Diğer tüm istekleri işler.</span><span class="sxs-lookup"><span data-stu-id="77a76-144">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="77a76-145">Ayrıca, bu yöntem:</span><span class="sxs-lookup"><span data-stu-id="77a76-145">Additionally, this method:</span></span>

* <span data-ttu-id="77a76-146">Bir `{APPLICATION NAME}API` API kaynağını sunucu ile Identity varsayılan kapsamına kaydeder `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="77a76-146">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="77a76-147">Uygulama için sunucu tarafından verilen belirteçleri doğrulamak üzere JWT taşıyıcı belirteç ara yazılımını yapılandırır Identity .</span><span class="sxs-lookup"><span data-stu-id="77a76-147">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="77a76-148">Dalgalı bir denetleyici</span><span class="sxs-lookup"><span data-stu-id="77a76-148">WeatherForecastController</span></span>

<span data-ttu-id="77a76-149">`WeatherForecastController`() İçinde `Controllers/WeatherForecastController.cs` , [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) özniteliği sınıfına uygulanır.</span><span class="sxs-lookup"><span data-stu-id="77a76-149">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="77a76-150">Özniteliği, kullanıcıya kaynağa erişim için varsayılan ilkeye göre yetkilendirilmiş olması gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="77a76-150">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="77a76-151">Varsayılan yetkilendirme ilkesi, tarafından ayarlanan varsayılan kimlik doğrulama düzenini kullanacak şekilde yapılandırılır <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="77a76-151">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="77a76-152">Yardımcı yöntemi, <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> uygulamaya yönelik istekler için varsayılan işleyici olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="77a76-152">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="77a76-153">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="77a76-153">ApplicationDbContext</span></span>

<span data-ttu-id="77a76-154">`ApplicationDbContext`() İçinde `Data/ApplicationDbContext.cs` , <xref:Microsoft.EntityFrameworkCore.DbContext> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> sunucu için şemayı içerecek şekilde genişletilir Identity .</span><span class="sxs-lookup"><span data-stu-id="77a76-154">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="77a76-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> , öğesinden türetilir <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .</span><span class="sxs-lookup"><span data-stu-id="77a76-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="77a76-156">Veritabanı şemasının tam denetimini elde etmek için, kullanılabilir Identity <xref:Microsoft.EntityFrameworkCore.DbContext> sınıflardan birini ve Identity yöntemi metodunu çağırarak şemayı içerecek şekilde yapılandırın `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .</span><span class="sxs-lookup"><span data-stu-id="77a76-156">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="77a76-157">Oıdcconfigurationcontroller</span><span class="sxs-lookup"><span data-stu-id="77a76-157">OidcConfigurationController</span></span>

<span data-ttu-id="77a76-158">`OidcConfigurationController`() İçinde `Controllers/OidcConfigurationController.cs` , istemci uç noktası OIDC parametrelerine hizmeti sunacak şekilde sağlanır.</span><span class="sxs-lookup"><span data-stu-id="77a76-158">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="77a76-159">Uygulama ayarları</span><span class="sxs-lookup"><span data-stu-id="77a76-159">App settings</span></span>

<span data-ttu-id="77a76-160">Proje kökündeki uygulama ayarları dosyasında ( `appsettings.json` ), `IdentityServer` bölümünde yapılandırılan istemcilerin listesi açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="77a76-160">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="77a76-161">Aşağıdaki örnekte, tek bir istemci vardır.</span><span class="sxs-lookup"><span data-stu-id="77a76-161">In the following example, there's a single client.</span></span> <span data-ttu-id="77a76-162">İstemci adı, uygulama adına karşılık gelir ve kural tarafından OAuth `ClientId` parametresine eşlenir.</span><span class="sxs-lookup"><span data-stu-id="77a76-162">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="77a76-163">Profil, yapılandırılan uygulama türünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="77a76-163">The profile indicates the app type being configured.</span></span> <span data-ttu-id="77a76-164">Profil, sunucu için yapılandırma işlemini basitleştiren kuralları yönlendirmek için dahili olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="77a76-164">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="77a76-165">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `BlazorSample.Client` ).</span><span class="sxs-lookup"><span data-stu-id="77a76-165">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="77a76-166">*`Client`* Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="77a76-166">*`Client`* app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="77a76-167">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="77a76-167">Authentication package</span></span>

<span data-ttu-id="77a76-168">Bireysel kullanıcı hesapları () kullanmak üzere bir uygulama oluşturulduğunda `Individual` , uygulama otomatik olarak [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) uygulamanın proje dosyasındaki paket için bir paket başvurusu alır.</span><span class="sxs-lookup"><span data-stu-id="77a76-168">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="77a76-169">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="77a76-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="77a76-170">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="77a76-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="77a76-171">Yer tutucu için `{VERSION}` , uygulamanın paylaşılan Framework sürümüyle eşleşen en son kararlı sürümü paketin [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)adresindeki **sürüm geçmişinde** bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="77a76-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

### <a name="httpclient-configuration"></a><span data-ttu-id="77a76-172">`HttpClient` yapılandırmada</span><span class="sxs-lookup"><span data-stu-id="77a76-172">`HttpClient` configuration</span></span>

<span data-ttu-id="77a76-173">`Program.Main`( `Program.cs` ) İçinde, bir adlandırılmış <xref:System.Net.Http.HttpClient> ( `HostIS.ServerAPI` ), <xref:System.Net.Http.HttpClient> sunucu API 'sine istek yaparken erişim belirteçlerini içeren örnekler sağlamak üzere yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="77a76-173">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="77a76-174">Bir Blazor WebAssembly uygulamayı barındırılan bir çözümün parçası olmayan var olan bir Identity sunucu örneğini kullanacak şekilde yapılandırıyorsanız Blazor , <xref:System.Net.Http.HttpClient> temel adres kaydını <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `builder.HostEnvironment.BaseAddress` ) konumundan sunucu uygulamasının API yetkilendirme uç noktası URL 'sine değiştirin.</span><span class="sxs-lookup"><span data-stu-id="77a76-174">If you're configuring a Blazor WebAssembly app to use an existing Identity Server instance that isn't part of a hosted Blazor solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="77a76-175">API yetkilendirme desteği</span><span class="sxs-lookup"><span data-stu-id="77a76-175">API authorization support</span></span>

<span data-ttu-id="77a76-176">Kullanıcıları kimlik doğrulama desteği, paket içinde sunulan genişletme yöntemi tarafından hizmet kapsayıcısına takılır [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) .</span><span class="sxs-lookup"><span data-stu-id="77a76-176">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="77a76-177">Bu yöntem, uygulamanın mevcut yetkilendirme sistemiyle etkileşimde bulunmak için gereken hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="77a76-177">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="77a76-178">Varsayılan olarak, uygulamanın yapılandırması tarafından kural tarafından yüklenir `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="77a76-178">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="77a76-179">Kural gereği, istemci KIMLIĞI uygulamanın derleme adına ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="77a76-179">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="77a76-180">Bu URL, aþýrý yükleme seçeneklerini çağırarak ayrı bir uç noktaya işaret etmek üzere değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="77a76-180">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="77a76-181">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="77a76-181">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="77a76-182">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="77a76-182">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="77a76-183">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="77a76-183">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="77a76-184">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="77a76-184">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="77a76-185">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="77a76-185">LoginDisplay component</span></span>

<span data-ttu-id="77a76-186">`LoginDisplay`Bileşen ( `Shared/LoginDisplay.razor` ) `MainLayout` bileşende () içinde işlenir `Shared/MainLayout.razor` ve aşağıdaki davranışları yönetir:</span><span class="sxs-lookup"><span data-stu-id="77a76-186">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="77a76-187">Kimliği doğrulanmış kullanıcılar için:</span><span class="sxs-lookup"><span data-stu-id="77a76-187">For authenticated users:</span></span>
  * <span data-ttu-id="77a76-188">Geçerli Kullanıcı adını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="77a76-188">Displays the current user name.</span></span>
  * <span data-ttu-id="77a76-189">İçindeki kullanıcı profili sayfasına bir bağlantı sunar ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="77a76-189">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="77a76-190">Uygulamanın oturumunu kapatmak için bir düğme sunar.</span><span class="sxs-lookup"><span data-stu-id="77a76-190">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="77a76-191">Anonim kullanıcılar için:</span><span class="sxs-lookup"><span data-stu-id="77a76-191">For anonymous users:</span></span>
  * <span data-ttu-id="77a76-192">Kayıt için seçeneği sunar.</span><span class="sxs-lookup"><span data-stu-id="77a76-192">Offers the option to register.</span></span>
  * <span data-ttu-id="77a76-193">Oturum açma seçeneği sunar.</span><span class="sxs-lookup"><span data-stu-id="77a76-193">Offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        <a href="authentication/profile">Hello, @context.User.Identity.Name!</a>
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/register">Register</a>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

### <a name="authentication-component"></a><span data-ttu-id="77a76-194">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="77a76-194">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="77a76-195">FetchData bileşeni</span><span class="sxs-lookup"><span data-stu-id="77a76-195">FetchData component</span></span>

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="77a76-196">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="77a76-196">Run the app</span></span>

<span data-ttu-id="77a76-197">Uygulamayı sunucu projesinden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="77a76-197">Run the app from the Server project.</span></span> <span data-ttu-id="77a76-198">Visual Studio 'yu kullanırken şunlardan birini yapın:</span><span class="sxs-lookup"><span data-stu-id="77a76-198">When using Visual Studio, either:</span></span>

* <span data-ttu-id="77a76-199">Araç çubuğundaki **başlangıç projeleri** açılan listesini *sunucu API 'si uygulamasına* ayarlayın ve **Çalıştır** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="77a76-199">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="77a76-200">**Çözüm Gezgini** ' de sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya uygulamayı **Hata Ayıkla** menüsünden başlatın.</span><span class="sxs-lookup"><span data-stu-id="77a76-200">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="77a76-201">API yetkilendirmesi ile ad ve rol talebi</span><span class="sxs-lookup"><span data-stu-id="77a76-201">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="77a76-202">Özel Kullanıcı fabrikası</span><span class="sxs-lookup"><span data-stu-id="77a76-202">Custom user factory</span></span>

<span data-ttu-id="77a76-203">*`Client`* Uygulamada, özel bir Kullanıcı fabrikası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="77a76-203">In the *`Client`* app, create a custom user factory.</span></span> <span data-ttu-id="77a76-204">Identity Sunucu, tek bir talep içinde birden çok rolü JSON dizisi olarak gönderir `role` .</span><span class="sxs-lookup"><span data-stu-id="77a76-204">Identity Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="77a76-205">Tek bir rol, talepte bir dize değeri olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="77a76-205">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="77a76-206">Fabrika, `role` kullanıcının rollerinin her biri için ayrı bir talep oluşturur.</span><span class="sxs-lookup"><span data-stu-id="77a76-206">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="77a76-207">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="77a76-207">`CustomUserFactory.cs`:</span></span>

```csharp
using System.Linq;
using System.Security.Claims;
using System.Text.Json;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication.Internal;

public class CustomUserFactory
    : AccountClaimsPrincipalFactory<RemoteUserAccount>
{
    public CustomUserFactory(IAccessTokenProviderAccessor accessor)
        : base(accessor)
    {
    }

    public async override ValueTask<ClaimsPrincipal> CreateUserAsync(
        RemoteUserAccount account,
        RemoteAuthenticationUserOptions options)
    {
        var user = await base.CreateUserAsync(account, options);

        if (user.Identity.IsAuthenticated)
        {
            var identity = (ClaimsIdentity)user.Identity;
            var roleClaims = identity.FindAll(identity.RoleClaimType).ToArray();

            if (roleClaims != null && roleClaims.Any())
            {
                foreach (var existingClaim in roleClaims)
                {
                    identity.RemoveClaim(existingClaim);
                }

                var rolesElem = account.AdditionalProperties[identity.RoleClaimType];

                if (rolesElem is JsonElement roles)
                {
                    if (roles.ValueKind == JsonValueKind.Array)
                    {
                        foreach (var role in roles.EnumerateArray())
                        {
                            identity.AddClaim(new Claim(options.RoleClaim, role.GetString()));
                        }
                    }
                    else
                    {
                        identity.AddClaim(new Claim(options.RoleClaim, roles.GetString()));
                    }
                }
            }
        }

        return user;
    }
}
```

<span data-ttu-id="77a76-208">*`Client`* Uygulamada, () fabrikasını kaydedin `Program.Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="77a76-208">In the *`Client`* app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="77a76-209">Uygulamada, *`Server`* <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> Identity rol ile ilgili hizmetleri ekleyen Oluşturucu üzerinde çağırın:</span><span class="sxs-lookup"><span data-stu-id="77a76-209">In the *`Server`* app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a><span data-ttu-id="77a76-210">IdentitySunucu yapılandırma</span><span class="sxs-lookup"><span data-stu-id="77a76-210">Configure Identity Server</span></span>

<span data-ttu-id="77a76-211">Aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="77a76-211">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="77a76-212">API yetkilendirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="77a76-212">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="77a76-213">Profil hizmeti</span><span class="sxs-lookup"><span data-stu-id="77a76-213">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="77a76-214">API yetkilendirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="77a76-214">API authorization options</span></span>

<span data-ttu-id="77a76-215">*`Server`* Uygulamada:</span><span class="sxs-lookup"><span data-stu-id="77a76-215">In the *`Server`* app:</span></span>

* <span data-ttu-id="77a76-216">Identity `name` Ve `role` taleplerini kimlik belirtecine ve erişim belirtecine yerleştirmek için sunucuyu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="77a76-216">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="77a76-217">JWT belirteci işleyicisindeki roller için varsayılan eşlemeyi engelleyin.</span><span class="sxs-lookup"><span data-stu-id="77a76-217">Prevent the default mapping for roles in the JWT token handler.</span></span>

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Linq;

...

services.AddIdentityServer()
    .AddApiAuthorization<ApplicationUser, ApplicationDbContext>(options => {
        options.IdentityResources["openid"].UserClaims.Add("name");
        options.ApiResources.Single().UserClaims.Add("name");
        options.IdentityResources["openid"].UserClaims.Add("role");
        options.ApiResources.Single().UserClaims.Add("role");
    });

JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Remove("role");
```

#### <a name="profile-service"></a><span data-ttu-id="77a76-218">Profil hizmeti</span><span class="sxs-lookup"><span data-stu-id="77a76-218">Profile Service</span></span>

<span data-ttu-id="77a76-219">*`Server`* Uygulamada bir `ProfileService` uygulama oluşturun.</span><span class="sxs-lookup"><span data-stu-id="77a76-219">In the *`Server`* app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="77a76-220">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="77a76-220">`ProfileService.cs`:</span></span>

```csharp
using IdentityModel;
using IdentityServer4.Models;
using IdentityServer4.Services;
using System.Threading.Tasks;

public class ProfileService : IProfileService
{
    public ProfileService()
    {
    }

    public async Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        await Task.CompletedTask;
    }

    public async Task IsActiveAsync(IsActiveContext context)
    {
        await Task.CompletedTask;
    }
}
```

<span data-ttu-id="77a76-221">*`Server`* Uygulamada, profil hizmetini şu şekilde kaydedin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="77a76-221">In the *`Server`* app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="77a76-222">Yetkilendirme mekanizmalarını kullanma</span><span class="sxs-lookup"><span data-stu-id="77a76-222">Use authorization mechanisms</span></span>

<span data-ttu-id="77a76-223">*`Client`* Uygulamada, bileşen yetkilendirme yaklaşımları bu noktada işlevseldir.</span><span class="sxs-lookup"><span data-stu-id="77a76-223">In the *`Client`* app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="77a76-224">Bileşenlerdeki yetkilendirme mekanizmalarının herhangi biri, kullanıcıyı yetkilendirmek için bir rol kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="77a76-224">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="77a76-225">[ `AuthorizeView` bileşen](xref:blazor/security/index#authorizeview-component) (örnek: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="77a76-225">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="77a76-226">[ `[Authorize]` Attribute yönergesi](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (örnek: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="77a76-226">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="77a76-227">[Yordamsal Logic](xref:blazor/security/index#procedural-logic) (örnek: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="77a76-227">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="77a76-228">Birden çok rol testi desteklenir:</span><span class="sxs-lookup"><span data-stu-id="77a76-228">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="77a76-229">`User.Identity.Name` , *`Client`* genellikle oturum açma e-posta adresi olan kullanıcının kullanıcı adıyla birlikte doldurulur.</span><span class="sxs-lookup"><span data-stu-id="77a76-229">`User.Identity.Name` is populated in the *`Client`* app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]

## <a name="host-in-azure-app-service-with-a-custom-domain"></a><span data-ttu-id="77a76-230">Özel bir etki alanı ile Azure App Service barındırın</span><span class="sxs-lookup"><span data-stu-id="77a76-230">Host in Azure App Service with a custom domain</span></span>

<span data-ttu-id="77a76-231">Aşağıdaki kılavuzda, Blazor WebAssembly Identity özel bir etki alanı ile [Azure App Service](https://azure.microsoft.com/services/app-service/) üzere barındırılan bir uygulamanın sunucu ile nasıl dağıtılacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="77a76-231">The following guidance explains how to deploy a hosted Blazor WebAssembly app with Identity Server to [Azure App Service](https://azure.microsoft.com/services/app-service/) with a custom domain.</span></span>

<span data-ttu-id="77a76-232">Bu barındırma senaryosunda, [ Identity sunucunun belirteç imzalama anahtarı](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) için aynı SERTIFIKAYı ve sitenin https güvenli iletişimini tarayıcılarla **birlikte kullanmayın:**</span><span class="sxs-lookup"><span data-stu-id="77a76-232">For this hosting scenario, do **not** use the same certificate for [Identity Server's token signing key](https://docs.identityserver.io/en/latest/topics/crypto.html#token-signing-and-validation) and the site's HTTPS secure communication with browsers:</span></span>

* <span data-ttu-id="77a76-233">Bu iki gereksinim için farklı sertifikaların kullanılması, her amaçla özel anahtarları yalıtdığından iyi bir güvenlik uygulamasıdır.</span><span class="sxs-lookup"><span data-stu-id="77a76-233">Using different certificates for these two requirements is a good security practice because it isolates private keys for each purpose.</span></span>
* <span data-ttu-id="77a76-234">Tarayıcılarla iletişim için TLS sertifikaları, sunucunun belirteç imzasını etkilemeden bağımsız olarak yönetilir Identity .</span><span class="sxs-lookup"><span data-stu-id="77a76-234">TLS certificates for communication with browsers is managed independently without affecting Identity Server's token signing.</span></span>
* <span data-ttu-id="77a76-235">[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) , özel etki alanı bağlamaya yönelik bir App Service uygulamasına sertifika sağladığı zaman, Identity sunucu, belirteç imzalama için Azure Key Vault aynı sertifikayı elde edemiyor.</span><span class="sxs-lookup"><span data-stu-id="77a76-235">When [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) supplies a certificate to an App Service app for custom domain binding, Identity Server can't obtain the same certificate from Azure Key Vault for token signing.</span></span> <span data-ttu-id="77a76-236">IdentitySunucuyu fiziksel bir yoldan aynı TLS sertifikasını kullanacak şekilde yapılandırmak mümkün olsa da, güvenlik sertifikalarının kaynak denetimine yerleştirilmesi **kötü bir uygulamadır ve çoğu senaryoda kaçınılmalıdır**.</span><span class="sxs-lookup"><span data-stu-id="77a76-236">Although configuring Identity Server to use the same TLS certificate from a physical path is possible, placing security certificates into source control is a **poor practice and should be avoided in most scenarios**.</span></span>

<span data-ttu-id="77a76-237">Aşağıdaki kılavuzda, yalnızca sunucu belirteci imzalama için Azure Key Vault ' de otomatik olarak imzalanan bir sertifika oluşturulur Identity .</span><span class="sxs-lookup"><span data-stu-id="77a76-237">In the following guidance, a self-signed certificate is created in Azure Key Vault solely for Identity Server token signing.</span></span> <span data-ttu-id="77a76-238">IdentitySunucu yapılandırması, uygulamanın `My`  >  sertifika deposu aracılığıyla Anahtar Kasası sertifikasını kullanır `CurrentUser` .</span><span class="sxs-lookup"><span data-stu-id="77a76-238">The Identity Server configuration uses the key vault certificate via the app's `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="77a76-239">Özel etki alanları olan HTTPS trafiği için kullanılan diğer sertifikalar, sunucu imzalama sertifikasından ayrı olarak oluşturulur ve yapılandırılır Identity .</span><span class="sxs-lookup"><span data-stu-id="77a76-239">Other certificates used for HTTPS traffic with custom domains are created and configured separately from the Identity Server signing certificate.</span></span>

<span data-ttu-id="77a76-240">Bir uygulamayı, Azure App Service ve Azure Key Vault özel bir etki alanı ve HTTPS ile barındıracak şekilde yapılandırmak için:</span><span class="sxs-lookup"><span data-stu-id="77a76-240">To configure an app, Azure App Service, and Azure Key Vault to host with a custom domain and HTTPS:</span></span>

1. <span data-ttu-id="77a76-241">Plan düzeyi veya daha yüksek bir [App Service planı](/azure/app-service/overview-hosting-plans) oluşturun `Basic B1` .</span><span class="sxs-lookup"><span data-stu-id="77a76-241">Create an [App Service plan](/azure/app-service/overview-hosting-plans) with an plan level of `Basic B1` or higher.</span></span> <span data-ttu-id="77a76-242">App Service, bir `Basic B1` veya daha yüksek hizmet katmanının özel etki alanlarını kullanmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="77a76-242">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="77a76-243">Sitenin güvenli tarayıcı iletişimi (HTTPS protokolü) için, kuruluşunuzun denetlediği sitenin tam etki alanı adı (FQDN) olan bir PFX sertifikası oluşturun (örneğin, `www.contoso.com` ).</span><span class="sxs-lookup"><span data-stu-id="77a76-243">Create a PFX certificate for the site's secure browser communication (HTTPS protocol) with a common name of the site's fully qualified domain name (FQDN) that your organization controls (for example, `www.contoso.com`).</span></span> <span data-ttu-id="77a76-244">Sertifikayı şu şekilde oluşturun:</span><span class="sxs-lookup"><span data-stu-id="77a76-244">Create the certificate with:</span></span>
   * <span data-ttu-id="77a76-245">Anahtar kullanımları</span><span class="sxs-lookup"><span data-stu-id="77a76-245">Key uses</span></span>
     * <span data-ttu-id="77a76-246">Dijital imza doğrulaması ( `digitalSignature` )</span><span class="sxs-lookup"><span data-stu-id="77a76-246">Digital signature validation (`digitalSignature`)</span></span>
     * <span data-ttu-id="77a76-247">Anahtar şifreleme ( `keyEncipherment` )</span><span class="sxs-lookup"><span data-stu-id="77a76-247">Key encipherment (`keyEncipherment`)</span></span>
   * <span data-ttu-id="77a76-248">Gelişmiş/genişletilmiş anahtar kullanımları</span><span class="sxs-lookup"><span data-stu-id="77a76-248">Enhanced/extended key uses</span></span>
     * <span data-ttu-id="77a76-249">İstemci kimlik doğrulaması (1.3.6.1.5.5.7.3.2)</span><span class="sxs-lookup"><span data-stu-id="77a76-249">Client Authentication (1.3.6.1.5.5.7.3.2)</span></span>
     * <span data-ttu-id="77a76-250">Sunucu kimlik doğrulaması (1.3.6.1.5.5.7.3.1)</span><span class="sxs-lookup"><span data-stu-id="77a76-250">Server Authentication (1.3.6.1.5.5.7.3.1)</span></span>

   <span data-ttu-id="77a76-251">Sertifikayı oluşturmak için aşağıdaki yaklaşımlardan birini veya başka bir uygun aracı ya da çevrimiçi hizmeti kullanın:</span><span class="sxs-lookup"><span data-stu-id="77a76-251">To create the certificate, use one of the following approaches or any other suitable tool or online service:</span></span>

   * [<span data-ttu-id="77a76-252">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="77a76-252">Azure Key Vault</span></span>](/azure/key-vault/certificates/quick-create-portal#add-a-certificate-to-key-vault)
   * [<span data-ttu-id="77a76-253">Windows üzerinde MakeCert</span><span class="sxs-lookup"><span data-stu-id="77a76-253">MakeCert on Windows</span></span>](/windows/desktop/seccrypto/makecert)
   * [<span data-ttu-id="77a76-254">OpenSSL</span><span class="sxs-lookup"><span data-stu-id="77a76-254">OpenSSL</span></span>](https://www.openssl.org)

   <span data-ttu-id="77a76-255">Daha sonra sertifikayı Azure Key Vault aktarmak için kullanılan parolayı unutmayın.</span><span class="sxs-lookup"><span data-stu-id="77a76-255">Make note of the password, which is used later to import the certificate into Azure Key Vault.</span></span>

   <span data-ttu-id="77a76-256">Azure Key Vault sertifikaları hakkında daha fazla bilgi için bkz. [Azure Key Vault: sertifikalar](/azure/key-vault/certificates/).</span><span class="sxs-lookup"><span data-stu-id="77a76-256">For more information on Azure Key Vault certificates, see [Azure Key Vault: Certificates](/azure/key-vault/certificates/).</span></span>
1. <span data-ttu-id="77a76-257">Yeni bir Azure Key Vault oluşturun veya Azure aboneliğinizde mevcut bir anahtar kasasını kullanın.</span><span class="sxs-lookup"><span data-stu-id="77a76-257">Create a new Azure Key Vault or use an existing key vault in your Azure subscription.</span></span>
1. <span data-ttu-id="77a76-258">Anahtar kasasının **Sertifikalar** alanında PFX site sertifikasını içeri aktarın.</span><span class="sxs-lookup"><span data-stu-id="77a76-258">In the key vault's **Certificates** area, import the PFX site certificate.</span></span> <span data-ttu-id="77a76-259">Uygulamanın yapılandırmasında daha sonra kullanılan sertifikanın parmak izini kaydedin.</span><span class="sxs-lookup"><span data-stu-id="77a76-259">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="77a76-260">Azure Key Vault, sunucu belirteci imzalama için otomatik olarak imzalanan yeni bir sertifika oluşturun Identity .</span><span class="sxs-lookup"><span data-stu-id="77a76-260">In Azure Key Vault, generate a new self-signed certificate for Identity Server token signing.</span></span> <span data-ttu-id="77a76-261">Sertifikaya bir **sertifika adı** ve **Konu** verin.</span><span class="sxs-lookup"><span data-stu-id="77a76-261">Give the certificate a **Certificate Name** and **Subject**.</span></span> <span data-ttu-id="77a76-262">**Konusu** , `CN={COMMON NAME}` `{COMMON NAME}` yer tutucunun sertifikanın ortak adı olduğu gibi belirtilir.</span><span class="sxs-lookup"><span data-stu-id="77a76-262">The **Subject** is specified as `CN={COMMON NAME}`, where the `{COMMON NAME}` placeholder is the certificate's common name.</span></span> <span data-ttu-id="77a76-263">Ortak ad herhangi bir alfasayısal dize olabilir.</span><span class="sxs-lookup"><span data-stu-id="77a76-263">The common name can be any alphanumeric string.</span></span> <span data-ttu-id="77a76-264">Örneğin, `CN=IdentityServerSigning` geçerli bir sertifika **konusudur**.</span><span class="sxs-lookup"><span data-stu-id="77a76-264">For example, `CN=IdentityServerSigning` is a valid certificate **Subject**.</span></span> <span data-ttu-id="77a76-265">Varsayılan **Gelişmiş Ilke yapılandırma** ayarlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="77a76-265">Use the default **Advanced Policy Configuration** settings.</span></span> <span data-ttu-id="77a76-266">Uygulamanın yapılandırmasında daha sonra kullanılan sertifikanın parmak izini kaydedin.</span><span class="sxs-lookup"><span data-stu-id="77a76-266">Record the certificate's thumbprint, which is used in the app's configuration later.</span></span>
1. <span data-ttu-id="77a76-267">Azure portal Azure App Service gidin ve aşağıdaki yapılandırmayla yeni bir App Service oluşturun:</span><span class="sxs-lookup"><span data-stu-id="77a76-267">Navigate to Azure App Service in the Azure portal and create a new App Service with the following configuration:</span></span>
   * <span data-ttu-id="77a76-268"> Kümeyi olarak Yayımla `Code` .</span><span class="sxs-lookup"><span data-stu-id="77a76-268">**Publish** set to `Code`.</span></span>
   * <span data-ttu-id="77a76-269">**Çalışma zamanı yığını** , uygulamanın çalışma zamanına ayarlandı.</span><span class="sxs-lookup"><span data-stu-id="77a76-269">**Runtime stack** set to the app's runtime.</span></span>
   * <span data-ttu-id="77a76-270">**SKU ve boyut** için App Service katmanının `Basic B1` veya daha yüksek olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="77a76-270">For **Sku and size**, confirm that the App Service tier is `Basic B1` or higher.</span></span>  <span data-ttu-id="77a76-271">App Service, bir `Basic B1` veya daha yüksek hizmet katmanının özel etki alanlarını kullanmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="77a76-271">App Service requires a `Basic B1` or higher service tier to use custom domains.</span></span>
1. <span data-ttu-id="77a76-272">Azure App Service oluşturduktan sonra, uygulamanın **yapılandırmasını** açın ve daha önce kaydedilen sertifika parmak izlerini belirten yeni bir uygulama ayarı ekleyin.</span><span class="sxs-lookup"><span data-stu-id="77a76-272">After Azure creates the App Service, open the app's **Configuration** and add a new application setting specifying the certificate thumbprints recorded earlier.</span></span> <span data-ttu-id="77a76-273">Uygulama ayarı anahtarı `WEBSITE_LOAD_CERTIFICATES` .</span><span class="sxs-lookup"><span data-stu-id="77a76-273">The app setting key is `WEBSITE_LOAD_CERTIFICATES`.</span></span> <span data-ttu-id="77a76-274">Aşağıdaki örnekte gösterildiği gibi, uygulama ayarı değerindeki sertifika parmak izlerini bir virgülle ayırın:</span><span class="sxs-lookup"><span data-stu-id="77a76-274">Separate the certificate thumbprints in the app setting value with a comma, as the following example shows:</span></span>
   * <span data-ttu-id="77a76-275">Anahtar: `WEBSITE_LOAD_CERTIFICATES`</span><span class="sxs-lookup"><span data-stu-id="77a76-275">Key: `WEBSITE_LOAD_CERTIFICATES`</span></span>
   * <span data-ttu-id="77a76-276">Değer: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span><span class="sxs-lookup"><span data-stu-id="77a76-276">Value: `57443A552A46DB...D55E28D412B943565,29F43A772CB6AF...1D04F0C67F85FB0B1`</span></span>

   <span data-ttu-id="77a76-277">Azure portal, uygulama ayarlarının kaydedilmesi iki adımlı bir işlemdir: `WEBSITE_LOAD_CERTIFICATES` anahtar-değer ayarını kaydedin, ardından dikey pencerenin en üstündeki **Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="77a76-277">In the Azure portal, saving app settings is a two-step process: Save the `WEBSITE_LOAD_CERTIFICATES` key-value setting, then select the **Save** button at the top of the blade.</span></span>
1. <span data-ttu-id="77a76-278">Uygulamanın **TLS/SSL ayarlarını** seçin.</span><span class="sxs-lookup"><span data-stu-id="77a76-278">Select the app's **TLS/SSL settings**.</span></span> <span data-ttu-id="77a76-279">**Özel anahtar sertifikaları (. pfx)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="77a76-279">Select **Private Key Certificates (.pfx)**.</span></span> <span data-ttu-id="77a76-280">HTTPS iletişimi için sitenin sertifikasını ve sitenin otomatik olarak imzalanan sunucu belirteci imzalama sertifikasını içeri aktarmak için **Key Vault sertifikayı Içeri aktar** işlemini iki kez kullanın Identity .</span><span class="sxs-lookup"><span data-stu-id="77a76-280">Use the **Import Key Vault Certificate** process twice to import both the site's certificate for HTTPS communication and the site's self-signed Identity Server token signing certificate.</span></span>
1. <span data-ttu-id="77a76-281">**Özel etki alanları** dikey penceresine gidin.</span><span class="sxs-lookup"><span data-stu-id="77a76-281">Navigate to the **Custom domains** blade.</span></span> <span data-ttu-id="77a76-282">Etki alanı Kaydedicinizin Web sitesinde, etki alanını yapılandırmak için **IP adresini** ve **özel etkı alanı doğrulama kimliğini** kullanın.</span><span class="sxs-lookup"><span data-stu-id="77a76-282">At your domain registrar's website, use the **IP address** and **Custom Domain Verification ID** to configure the domain.</span></span> <span data-ttu-id="77a76-283">Tipik bir etki alanı yapılandırması şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="77a76-283">A typical domain configuration includes:</span></span>
   * <span data-ttu-id="77a76-284">Azure portal **ana bilgisayarı** ve IP adresi değeri olan **bir kayıt** `@` .</span><span class="sxs-lookup"><span data-stu-id="77a76-284">An **A Record** with a **Host** of `@` and a value of the IP address from the Azure portal.</span></span>
   * <span data-ttu-id="77a76-285">**Ana bilgisayarı**  `asuid` ve Azure tarafından oluşturulan ve Azure Portal tarafından oluşturulan doğrulama kimliği değeri olan bir TXT kaydı.</span><span class="sxs-lookup"><span data-stu-id="77a76-285">A **TXT Record** with a **Host** of `asuid` and the value of the verification ID generated by Azure and provided by the Azure portal.</span></span>

   <span data-ttu-id="77a76-286">Değişiklikleri etki alanı Kaydedicinizin Web sitesinde doğru bir şekilde kaydettiğinizden emin olun.</span><span class="sxs-lookup"><span data-stu-id="77a76-286">Make sure that you save the changes at your domain registrar's website correctly.</span></span> <span data-ttu-id="77a76-287">Bazı kaydedici Web siteleri, etki alanı kayıtlarını kaydetmek için iki adımlı bir işlem gerektirir: bir veya daha fazla kayıt, bir veya daha fazla kaydın ayrı bir düğmeyle güncelleştirilmesi tarafından tek tek kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="77a76-287">Some registrar websites require a two-step process to save domain records: One or more records are saved individually followed by updating the domain's registration with a separate button.</span></span>
1. <span data-ttu-id="77a76-288">Azure portal **özel etki alanları** dikey penceresine geri dönün.</span><span class="sxs-lookup"><span data-stu-id="77a76-288">Return to the **Custom domains** blade in the Azure portal.</span></span> <span data-ttu-id="77a76-289">**Özel etki alanı ekle**'yi seçin.</span><span class="sxs-lookup"><span data-stu-id="77a76-289">Select **Add custom domain**.</span></span> <span data-ttu-id="77a76-290">**Bir kayıt** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="77a76-290">Select the **A Record** option.</span></span> <span data-ttu-id="77a76-291">Etki alanını girip **Doğrula**' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="77a76-291">Provide the domain and select **Validate**.</span></span> <span data-ttu-id="77a76-292">Etki alanı kayıtları doğruysa ve Internet üzerinden yayıldığında, portal **özel etki alanı Ekle** düğmesini seçmenizi sağlar.</span><span class="sxs-lookup"><span data-stu-id="77a76-292">If the domain records are correct and propagated across the Internet, the portal allows you to select the **Add custom domain** button.</span></span>

   <span data-ttu-id="77a76-293">Etki alanı kayıt şirketiniz tarafından işlendikten sonra, etki alanı kaydı değişikliklerinin Internet etki alanı ad sunucuları (DNS) üzerinden yayılması birkaç gün sürebilir.</span><span class="sxs-lookup"><span data-stu-id="77a76-293">It can take a few days for domain registration changes to propagate across Internet domain name servers (DNS) after they're processed by your domain registrar.</span></span> <span data-ttu-id="77a76-294">Etki alanı kayıtları üç iş günü içinde güncellenmemişse, kayıtların etki alanı kaydedicisinde doğru şekilde ayarlandığını onaylayın ve müşteri desteğiyle iletişim kurun.</span><span class="sxs-lookup"><span data-stu-id="77a76-294">If domain records aren't updated within three business days, confirm the records are correctly set with the domain registrar and contact their customer support.</span></span>
1. <span data-ttu-id="77a76-295">**Özel etki alanları** dikey penceresinde, etki alanı IÇIN **SSL durumu** işaretlenir `Not Secure` .</span><span class="sxs-lookup"><span data-stu-id="77a76-295">In the **Custom domains** blade, the **SSL STATE** for the domain is marked `Not Secure`.</span></span> <span data-ttu-id="77a76-296">**Bağlama Ekle** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="77a76-296">Select the **Add binding** link.</span></span> <span data-ttu-id="77a76-297">Özel etki alanı bağlamasının anahtar kasasından site HTTPS sertifikasını seçin.</span><span class="sxs-lookup"><span data-stu-id="77a76-297">Select the site HTTPS certificate from the key vault for the custom domain binding.</span></span>
1. <span data-ttu-id="77a76-298">Visual Studio 'da, *sunucu* projesinin uygulama ayarları dosyasını ( `appsettings.json` veya `appsettings.Production.json` ) açın.</span><span class="sxs-lookup"><span data-stu-id="77a76-298">In Visual Studio, open the *Server* project's app settings file (`appsettings.json` or `appsettings.Production.json`).</span></span> <span data-ttu-id="77a76-299">IdentitySunucu yapılandırması ' nda aşağıdaki `Key` bölümü ekleyin.</span><span class="sxs-lookup"><span data-stu-id="77a76-299">In the Identity Server configuration, add the following `Key` section.</span></span> <span data-ttu-id="77a76-300">Anahtar için otomatik olarak imzalanan sertifika **konusunu** belirtin `Name` .</span><span class="sxs-lookup"><span data-stu-id="77a76-300">Specify the self-signed certificate **Subject** for the `Name` key.</span></span> <span data-ttu-id="77a76-301">Aşağıdaki örnekte, sertifikanın anahtar kasasında atanan ortak adı ' dır; `IdentityServerSigning` Bu da bir **konuyu** veren `CN=IdentityServerSigning` :</span><span class="sxs-lookup"><span data-stu-id="77a76-301">In the following example, the certificate's common name assigned in the key vault is `IdentityServerSigning`, which yields a **Subject** of `CN=IdentityServerSigning`:</span></span>

   ```json
   "IdentityServer": {

     ...

     "Key": {
       "Type": "Store",
       "StoreName": "My",
       "StoreLocation": "CurrentUser",
       "Name": "CN=IdentityServerSigning"
     }
   },
   ```

1. <span data-ttu-id="77a76-302">Visual Studio 'da *sunucu* projesi için bir Azure App Service [Yayımlama profili](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) oluşturun.</span><span class="sxs-lookup"><span data-stu-id="77a76-302">In Visual Studio, create an Azure App Service [publish profile](xref:host-and-deploy/visual-studio-publish-profiles#publish-profiles) for the *Server* project.</span></span> <span data-ttu-id="77a76-303">Menü çubuğundan: **derleme**  >    >  **Yeni**  >  **Azure**  >  **Azure App Service** yayımlama (Windows veya Linux) öğesini seçin.</span><span class="sxs-lookup"><span data-stu-id="77a76-303">From the menu bar, select: **Build** > **Publish** > **New** > **Azure** > **Azure App Service** (Windows or Linux).</span></span> <span data-ttu-id="77a76-304">Visual Studio bir Azure aboneliğine bağlıyken, Azure kaynaklarının **görünümünü** **kaynak türüne** göre ayarlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="77a76-304">When Visual Studio is connected to an Azure subscription, you can set the **View** of Azure resources by **Resource type**.</span></span> <span data-ttu-id="77a76-305">Uygulamanın App Service bulmak ve bunu seçmek için **Web uygulaması** listesinde gezinin.</span><span class="sxs-lookup"><span data-stu-id="77a76-305">Navigate within the **Web App** list to find the App Service for the app and select it.</span></span> <span data-ttu-id="77a76-306">**Son**'u seçin.</span><span class="sxs-lookup"><span data-stu-id="77a76-306">Select **Finish**.</span></span>
1. <span data-ttu-id="77a76-307">Visual Studio **Yayımla** penceresine döndüğünde, anahtar kasası ve SQL Server veritabanı hizmeti bağımlılıkları otomatik olarak algılanır.</span><span class="sxs-lookup"><span data-stu-id="77a76-307">When Visual Studio returns to the **Publish** window, the key vault and SQL Server database service dependencies are automatically detected.</span></span>

   <span data-ttu-id="77a76-308">Anahtar Kasası hizmeti için varsayılan ayarlarda yapılandırma değişikliği yapılması gerekmez.</span><span class="sxs-lookup"><span data-stu-id="77a76-308">No configuration changes to the default settings are required for the key vault service.</span></span>

   <span data-ttu-id="77a76-309">Test amacıyla, bir uygulamanın varsayılan olarak şablon tarafından yapılandırılan yerel [SQLite](https://www.sqlite.org/index.html) veritabanı, Blazor ek yapılandırma olmadan uygulamayla birlikte dağıtılabilir.</span><span class="sxs-lookup"><span data-stu-id="77a76-309">For testing purposes, an app's local [SQLite](https://www.sqlite.org/index.html) database, which is configured by default by the Blazor template, can be deployed with the app without additional configuration.</span></span> <span data-ttu-id="77a76-310">Üretimde sunucu için farklı bir veritabanını yapılandırmak, Identity Bu makalenin kapsamı dışındadır.</span><span class="sxs-lookup"><span data-stu-id="77a76-310">Configuring a different database for Identity Server in production is beyond the scope of this article.</span></span> <span data-ttu-id="77a76-311">Daha fazla bilgi için aşağıdaki belge kümelerinde veritabanı kaynaklarına bakın:</span><span class="sxs-lookup"><span data-stu-id="77a76-311">For more information, see the database resources in the following documentation sets:</span></span>
   * [<span data-ttu-id="77a76-312">App Service</span><span class="sxs-lookup"><span data-stu-id="77a76-312">App Service</span></span>](/azure/app-service/)
   * [<span data-ttu-id="77a76-313">Identity Server</span><span class="sxs-lookup"><span data-stu-id="77a76-313">Identity Server</span></span>](https://identityserver4.readthedocs.io/en/latest/)

1. <span data-ttu-id="77a76-314">Pencerenin üst kısmındaki dağıtım profili adı altındaki **Düzenle** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="77a76-314">Select the **Edit** link under the deployment profile name at the top of the window.</span></span> <span data-ttu-id="77a76-315">Hedef URL 'yi sitenin özel etki alanı URL 'SI olarak değiştirin (örneğin, `https://www.contoso.com` ).</span><span class="sxs-lookup"><span data-stu-id="77a76-315">Change the destination URL to the site's custom domain URL (for example, `https://www.contoso.com`).</span></span> <span data-ttu-id="77a76-316">Ayarları kaydedin.</span><span class="sxs-lookup"><span data-stu-id="77a76-316">Save the settings.</span></span>
1. <span data-ttu-id="77a76-317">Uygulamayı yayımlayın.</span><span class="sxs-lookup"><span data-stu-id="77a76-317">Publish the app.</span></span> <span data-ttu-id="77a76-318">Visual Studio bir tarayıcı penceresi açar ve siteyi kendi özel etki alanında ister.</span><span class="sxs-lookup"><span data-stu-id="77a76-318">Visual Studio opens a browser window and requests the site at its custom domain.</span></span>

<span data-ttu-id="77a76-319">Azure belgeleri, Azure hizmetleri ve özel etki alanlarını, bir kayıt yerine CNAME kayıtlarını kullanma hakkında bilgi dahil olmak üzere App Service 'de TLS bağlama ile kullanmayla ilgili ek bilgiler içerir.</span><span class="sxs-lookup"><span data-stu-id="77a76-319">The Azure documentation contains additional detail on using Azure services and custom domains with TLS binding in App Service, including information on using CNAME records instead of A records.</span></span> <span data-ttu-id="77a76-320">Daha fazla bilgi için aşağıdaki kaynaklara bakın:</span><span class="sxs-lookup"><span data-stu-id="77a76-320">For more information, see the following resources:</span></span>

* [<span data-ttu-id="77a76-321">App Service belgeleri</span><span class="sxs-lookup"><span data-stu-id="77a76-321">App Service documentation</span></span>](/azure/app-service/)
* [<span data-ttu-id="77a76-322">Öğretici: mevcut bir özel DNS adını Azure App Service eşleme</span><span class="sxs-lookup"><span data-stu-id="77a76-322">Tutorial: Map an existing custom DNS name to Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-custom-domain)
* [<span data-ttu-id="77a76-323">Azure App Service 'de TLS/SSL bağlaması ile özel bir DNS adının güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="77a76-323">Secure a custom DNS name with a TLS/SSL binding in Azure App Service</span></span>](/azure/app-service/configure-ssl-bindings)
* [<span data-ttu-id="77a76-324">Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="77a76-324">Azure Key Vault</span></span>](/azure/key-vault/)

<span data-ttu-id="77a76-325">Uygulama, uygulama yapılandırması veya Azure portal Azure hizmetlerinde yapılan bir değişiklikten sonra her uygulama test çalışması için yeni bir özel veya geçersiz tarayıcı penceresi kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="77a76-325">We recommend using a new in-private or incognito browser window for each app test run after a change to the app, app configuration, or Azure services in the Azure portal.</span></span> <span data-ttu-id="77a76-326">cookieÖnceki bir test çalıştırağından kalan öğeleri, site yapılandırması doğru olsa bile siteyi test ederken başarısız kimlik doğrulama veya yetkilendirmeyle sonuçlanabilir.</span><span class="sxs-lookup"><span data-stu-id="77a76-326">Lingering cookies from a previous test run can result in failed authentication or authorization when testing the site even when the site's configuration is correct.</span></span> <span data-ttu-id="77a76-327">Visual Studio 'Yu her test çalışması için yeni bir özel veya bir tarayıcı penceresi açmak üzere yapılandırma hakkında daha fazla bilgi için, [ Cookie s ve site verileri](#cookies-and-site-data) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="77a76-327">For more information on how to configure Visual Studio to open a new in-private or incognito browser window for each test run, see the [Cookies and site data](#cookies-and-site-data) section.</span></span>

<span data-ttu-id="77a76-328">Azure portal App Service yapılandırma değiştirildiğinde, güncelleştirmeler genellikle hızla etkili olur ancak anında gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="77a76-328">When App Service configuration is changed in the Azure portal, the updates generally take effect quickly but aren't instant.</span></span> <span data-ttu-id="77a76-329">Bazen, bir yapılandırma değişikliğinin etkili olabilmesi için bir App Service yeniden başlatılması için kısa bir süre beklemeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="77a76-329">Sometimes, you must wait a short period for an App Service to restart in order for a configuration change to take effect.</span></span>

<span data-ttu-id="77a76-330">Bir sertifika yükleme sorununu giderirken, Azure portal [kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell komut kabuğu 'nda aşağıdaki komutu yürütün.</span><span class="sxs-lookup"><span data-stu-id="77a76-330">If troubleshooting a certificate loading problem, execute the following command in an Azure portal [Kudu](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service) PowerShell command shell.</span></span> <span data-ttu-id="77a76-331">Komut, uygulamanın sertifika deposundan erişebileceği sertifikaların bir listesini sağlar `My`  >  `CurrentUser` .</span><span class="sxs-lookup"><span data-stu-id="77a76-331">The command provides a list of certificates that the app can access from the `My` > `CurrentUser` certificate store.</span></span> <span data-ttu-id="77a76-332">Çıktıda sertifika konuları ve parmak izleri, bir uygulamada hata ayıklanırken yararlı olur:</span><span class="sxs-lookup"><span data-stu-id="77a76-332">The output includes certificate subjects and thumbprints useful when debugging an app:</span></span>

```powershell
Get-ChildItem -path Cert:\CurrentUser\My -Recurse | Format-List DnsNameList, Subject, Thumbprint, EnhancedKeyUsageList
```

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="77a76-333">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="77a76-333">Additional resources</span></span>

* [<span data-ttu-id="77a76-334">Azure App Service dağıtım</span><span class="sxs-lookup"><span data-stu-id="77a76-334">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="77a76-335">Key Vault bir sertifikayı içeri aktarma (Azure belgeleri)</span><span class="sxs-lookup"><span data-stu-id="77a76-335">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="77a76-336">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="77a76-336">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
