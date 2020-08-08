---
title: Barındırılan bir ASP.NET Core Blazor WebAssembly uygulamasının sunucuyla güvenliğini Identity sağlama
author: guardrex
description: BlazorBir [ Identity sunucu](https://identityserver.io/) arka ucu kullanan Visual Studio içinden kimlik doğrulaması ile yeni bir barındırılan çözüm oluşturmak için
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/09/2020
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
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 630425c4efd5fc4dfffd402018b90d45056f5d46
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88013833"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-no-locidentity-server"></a><span data-ttu-id="f44e3-103">Blazor WebAssemblySunucu ile ASP.NET Core barındırılan bir uygulamanın güvenliğini sağlama Identity</span><span class="sxs-lookup"><span data-stu-id="f44e3-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="f44e3-104">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="f44e3-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f44e3-105">Bu makalede Blazor , kullanıcıların ve API çağrılarının kimliğini doğrulamak için [ Identity sunucusunu](https://identityserver.io/) kullanan yeni bir barındırılan çözümün nasıl oluşturulacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f44e3-105">This article explains how to create a new hosted Blazor solution that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

> [!NOTE]
> <span data-ttu-id="f44e3-106">Tek başına veya barındırılan bir Blazor WebAssembly uygulamayı var olan bir dış sunucu örneğini kullanacak şekilde yapılandırmak için Identity içindeki yönergeleri izleyin <xref:blazor/security/webassembly/standalone-with-authentication-library> .</span><span class="sxs-lookup"><span data-stu-id="f44e3-106">To configure a standalone or hosted Blazor WebAssembly app to use an existing, external Identity Server instance, follow the guidance in <xref:blazor/security/webassembly/standalone-with-authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f44e3-107">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f44e3-107">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f44e3-108">Blazor WebAssemblyKimlik doğrulama mekanizmasına sahip yeni bir proje oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="f44e3-108">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="f44e3-109">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda \*\* Blazor WebAssembly uygulama\*\* şablonunu seçtikten sonra, **kimlik doğrulaması**altında **Değiştir** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="f44e3-109">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="f44e3-110">ASP.NET Core sistemi kullanarak uygulama içinde kullanıcıları depolamak için **Kullanıcı hesaplarını depola ve uygulama Içi** **Kullanıcı hesapları** seçeneğini belirleyin [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="f44e3-110">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

1. <span data-ttu-id="f44e3-111">**Gelişmiş** bölümündeki **ASP.NET Core barındırılan** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="f44e3-111">Select the **ASP.NET Core hosted** check box in the **Advanced** section.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="f44e3-112">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f44e3-112">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="f44e3-113">Blazor WebAssemblyBoş bir klasörde kimlik doğrulama mekanizmasına sahip yeni bir proje oluşturmak için, `Individual` `-au|--auth` ASP.NET Core sistemini kullanarak uygulama içinde kullanıcıları depolama seçeneğiyle birlikte kimlik doğrulama mekanizmasını belirtin [Identity](xref:security/authentication/identity) :</span><span class="sxs-lookup"><span data-stu-id="f44e3-113">To create a new Blazor WebAssembly project with an authentication mechanism in an empty folder, specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho -o {APP NAME}
```

| <span data-ttu-id="f44e3-114">Yer tutucu</span><span class="sxs-lookup"><span data-stu-id="f44e3-114">Placeholder</span></span>  | <span data-ttu-id="f44e3-115">Örnek</span><span class="sxs-lookup"><span data-stu-id="f44e3-115">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="f44e3-116">Seçeneğiyle belirtilen çıktı konumu, `-o|--output` mevcut değilse bir proje klasörü oluşturur ve uygulamanın adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="f44e3-116">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="f44e3-117">Daha fazla bilgi için [`dotnet new`](/dotnet/core/tools/dotnet-new) .NET Core kılavuzundaki komutuna bakın.</span><span class="sxs-lookup"><span data-stu-id="f44e3-117">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="f44e3-118">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f44e3-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="f44e3-119">Blazor WebAssemblyKimlik doğrulama mekanizmasına sahip yeni bir proje oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="f44e3-119">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="f44e3-120">**Yeni Blazor WebAssembly uygulamanızı yapılandırın** adımındaki **kimlik doğrulaması** açılan listesinden **bireysel kimlik doğrulaması (uygulama içi)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="f44e3-120">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="f44e3-121">Uygulama, ASP.NET Core uygulamada depolanan bireysel kullanıcılar için oluşturulur [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="f44e3-121">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

1. <span data-ttu-id="f44e3-122">**Barındırılan ASP.NET Core** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="f44e3-122">Select the **ASP.NET Core hosted** check box.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="f44e3-123">Sunucu uygulaması yapılandırması</span><span class="sxs-lookup"><span data-stu-id="f44e3-123">Server app configuration</span></span>

<span data-ttu-id="f44e3-124">Aşağıdaki bölümlerde, kimlik doğrulama desteği dahil edildiğinde projenin eklemeleri açıklanır.</span><span class="sxs-lookup"><span data-stu-id="f44e3-124">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="f44e3-125">Başlangıç sınıfı</span><span class="sxs-lookup"><span data-stu-id="f44e3-125">Startup class</span></span>

<span data-ttu-id="f44e3-126">`Startup`Sınıfı aşağıdaki eklemeleri içerir.</span><span class="sxs-lookup"><span data-stu-id="f44e3-126">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="f44e3-127">`Startup.ConfigureServices` içinde:</span><span class="sxs-lookup"><span data-stu-id="f44e3-127">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="f44e3-128">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="f44e3-128">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="f44e3-129">Identity<xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Sunucu üstünde varsayılan ASP.NET Core kuralları ayarlayan ek bir yardımcı yöntemi olan sunucu Identity :</span><span class="sxs-lookup"><span data-stu-id="f44e3-129">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="f44e3-130"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Sunucu tarafından ÜRETILEN JWT belirteçlerini doğrulamak üzere uygulamayı yapılandıran ek bir yardımcı yöntem ile kimlik doğrulaması Identity :</span><span class="sxs-lookup"><span data-stu-id="f44e3-130">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="f44e3-131">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="f44e3-131">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="f44e3-132">IdentitySunucu ara sunucusu, OpenID Connect (OıDC) uç noktalarını kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="f44e3-132">The IdentityServer middleware exposes the OpenID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="f44e3-133">Kimlik doğrulama ara yazılımı, istek kimlik bilgilerini doğrulamadan ve Kullanıcı istek bağlamında ayarlamaktan sorumludur:</span><span class="sxs-lookup"><span data-stu-id="f44e3-133">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="f44e3-134">Yetki ara yazılımı yetkilendirme özelliklerini sunar:</span><span class="sxs-lookup"><span data-stu-id="f44e3-134">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="f44e3-135">Addadpiauthorization</span><span class="sxs-lookup"><span data-stu-id="f44e3-135">AddApiAuthorization</span></span>

<span data-ttu-id="f44e3-136"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Yardımcı yöntemi, ASP.NET Core senaryolar için [ Identity sunucuyu](https://identityserver.io/) yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f44e3-136">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="f44e3-137">IdentitySunucu, uygulama güvenliği sorunlarını işlemeye yönelik güçlü ve genişletilebilir bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="f44e3-137">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="f44e3-138">IdentitySunucu, en yaygın senaryolar için gereksiz karmaşıklık sunar.</span><span class="sxs-lookup"><span data-stu-id="f44e3-138">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="f44e3-139">Sonuç olarak, iyi bir başlangıç noktası düşüntiğimiz bir dizi kural ve yapılandırma seçeneği sağlanır.</span><span class="sxs-lookup"><span data-stu-id="f44e3-139">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="f44e3-140">Kimlik doğrulama gereksinimleriniz değiştikçe, sunucunun tam gücü, Identity kimlik doğrulamasını uygulamanın gereksinimlerine uyacak şekilde özelleştirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f44e3-140">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addno-locidentityserverjwt"></a><span data-ttu-id="f44e3-141">IdentityServerjwt Ekle</span><span class="sxs-lookup"><span data-stu-id="f44e3-141">AddIdentityServerJwt</span></span>

<span data-ttu-id="f44e3-142"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Yardımcı yöntemi, varsayılan kimlik doğrulama işleyicisi olarak uygulama için bir ilke düzeni yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f44e3-142">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="f44e3-143">İlke, Identity URL alanındaki herhangi bir alt yolda yönlendirilen tüm istekleri işlemeye izin verecek şekilde yapılandırılmıştır Identity `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="f44e3-143">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="f44e3-144"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Diğer tüm istekleri işler.</span><span class="sxs-lookup"><span data-stu-id="f44e3-144">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="f44e3-145">Ayrıca, bu yöntem:</span><span class="sxs-lookup"><span data-stu-id="f44e3-145">Additionally, this method:</span></span>

* <span data-ttu-id="f44e3-146">Bir `{APPLICATION NAME}API` API kaynağını sunucu ile Identity varsayılan kapsamına kaydeder `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="f44e3-146">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="f44e3-147">Uygulama için sunucu tarafından verilen belirteçleri doğrulamak üzere JWT taşıyıcı belirteç ara yazılımını yapılandırır Identity .</span><span class="sxs-lookup"><span data-stu-id="f44e3-147">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="f44e3-148">Dalgalı bir denetleyici</span><span class="sxs-lookup"><span data-stu-id="f44e3-148">WeatherForecastController</span></span>

<span data-ttu-id="f44e3-149">`WeatherForecastController`() İçinde `Controllers/WeatherForecastController.cs` , [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) özniteliği sınıfına uygulanır.</span><span class="sxs-lookup"><span data-stu-id="f44e3-149">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="f44e3-150">Özniteliği, kullanıcıya kaynağa erişim için varsayılan ilkeye göre yetkilendirilmiş olması gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="f44e3-150">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="f44e3-151">Varsayılan yetkilendirme ilkesi, tarafından ayarlanan varsayılan kimlik doğrulama düzenini kullanacak şekilde yapılandırılır <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="f44e3-151">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="f44e3-152">Yardımcı yöntemi, <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> uygulamaya yönelik istekler için varsayılan işleyici olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="f44e3-152">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="f44e3-153">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="f44e3-153">ApplicationDbContext</span></span>

<span data-ttu-id="f44e3-154">`ApplicationDbContext`() İçinde `Data/ApplicationDbContext.cs` , <xref:Microsoft.EntityFrameworkCore.DbContext> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> sunucu için şemayı içerecek şekilde genişletilir Identity .</span><span class="sxs-lookup"><span data-stu-id="f44e3-154">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="f44e3-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>, öğesinden türetilir <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .</span><span class="sxs-lookup"><span data-stu-id="f44e3-155"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="f44e3-156">Veritabanı şemasının tam denetimini elde etmek için, kullanılabilir Identity <xref:Microsoft.EntityFrameworkCore.DbContext> sınıflardan birini ve Identity yöntemi metodunu çağırarak şemayı içerecek şekilde yapılandırın `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .</span><span class="sxs-lookup"><span data-stu-id="f44e3-156">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="f44e3-157">Oıdcconfigurationcontroller</span><span class="sxs-lookup"><span data-stu-id="f44e3-157">OidcConfigurationController</span></span>

<span data-ttu-id="f44e3-158">`OidcConfigurationController`() İçinde `Controllers/OidcConfigurationController.cs` , istemci uç noktası OIDC parametrelerine hizmeti sunacak şekilde sağlanır.</span><span class="sxs-lookup"><span data-stu-id="f44e3-158">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings"></a><span data-ttu-id="f44e3-159">Uygulama ayarları</span><span class="sxs-lookup"><span data-stu-id="f44e3-159">App settings</span></span>

<span data-ttu-id="f44e3-160">Proje kökündeki uygulama ayarları dosyasında ( `appsettings.json` ), `IdentityServer` bölümünde yapılandırılan istemcilerin listesi açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f44e3-160">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="f44e3-161">Aşağıdaki örnekte, tek bir istemci vardır.</span><span class="sxs-lookup"><span data-stu-id="f44e3-161">In the following example, there's a single client.</span></span> <span data-ttu-id="f44e3-162">İstemci adı, uygulama adına karşılık gelir ve kural tarafından OAuth `ClientId` parametresine eşlenir.</span><span class="sxs-lookup"><span data-stu-id="f44e3-162">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="f44e3-163">Profil, yapılandırılan uygulama türünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="f44e3-163">The profile indicates the app type being configured.</span></span> <span data-ttu-id="f44e3-164">Profil, sunucu için yapılandırma işlemini basitleştiren kuralları yönlendirmek için dahili olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f44e3-164">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="f44e3-165">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `BlazorSample.Client` ).</span><span class="sxs-lookup"><span data-stu-id="f44e3-165">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="f44e3-166">İstemci uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="f44e3-166">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="f44e3-167">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="f44e3-167">Authentication package</span></span>

<span data-ttu-id="f44e3-168">Bireysel kullanıcı hesapları () kullanmak üzere bir uygulama oluşturulduğunda `Individual` , uygulama otomatik olarak [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) uygulamanın proje dosyasındaki paket için bir paket başvurusu alır.</span><span class="sxs-lookup"><span data-stu-id="f44e3-168">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="f44e3-169">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="f44e3-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="f44e3-170">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f44e3-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="httpclient-configuration"></a><span data-ttu-id="f44e3-171">`HttpClient`yapılandırmada</span><span class="sxs-lookup"><span data-stu-id="f44e3-171">`HttpClient` configuration</span></span>

<span data-ttu-id="f44e3-172">`Program.Main`( `Program.cs` ) İçinde, bir adlandırılmış <xref:System.Net.Http.HttpClient> ( `HostIS.ServerAPI` ), <xref:System.Net.Http.HttpClient> sunucu API 'sine istek yaparken erişim belirteçlerini içeren örnekler sağlamak üzere yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="f44e3-172">In `Program.Main` (`Program.cs`), a named <xref:System.Net.Http.HttpClient> (`HostIS.ServerAPI`) is configured to supply <xref:System.Net.Http.HttpClient> instances that include access tokens when making requests to the server API:</span></span>

```csharp
builder.Services.AddHttpClient("HostIS.ServerAPI", 
        client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("HostIS.ServerAPI"));
```

> [!NOTE]
> <span data-ttu-id="f44e3-173">Bir Blazor WebAssembly uygulamayı barındırılan bir çözümün parçası olmayan var olan bir Identity sunucu örneğini kullanacak şekilde yapılandırıyorsanız Blazor , <xref:System.Net.Http.HttpClient> temel adres kaydını <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> ( `builder.HostEnvironment.BaseAddress` ) konumundan sunucu uygulamasının API yetkilendirme uç noktası URL 'sine değiştirin.</span><span class="sxs-lookup"><span data-stu-id="f44e3-173">If you're configuring a Blazor WebAssembly app to use an existing Identity Server instance that isn't part of a hosted Blazor solution, change the <xref:System.Net.Http.HttpClient> base address registration from <xref:Microsoft.AspNetCore.Components.WebAssembly.Hosting.IWebAssemblyHostEnvironment.BaseAddress?displayProperty=nameWithType> (`builder.HostEnvironment.BaseAddress`) to the server app's API authorization endpoint URL.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="f44e3-174">API yetkilendirme desteği</span><span class="sxs-lookup"><span data-stu-id="f44e3-174">API authorization support</span></span>

<span data-ttu-id="f44e3-175">Kullanıcıları kimlik doğrulama desteği, paket içinde sunulan genişletme yöntemi tarafından hizmet kapsayıcısına takılır [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="f44e3-175">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="f44e3-176">Bu yöntem, uygulamanın mevcut yetkilendirme sistemiyle etkileşimde bulunmak için gereken hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f44e3-176">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="f44e3-177">Varsayılan olarak, uygulamanın yapılandırması tarafından kural tarafından yüklenir `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="f44e3-177">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="f44e3-178">Kural gereği, istemci KIMLIĞI uygulamanın derleme adına ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="f44e3-178">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="f44e3-179">Bu URL, aþýrý yükleme seçeneklerini çağırarak ayrı bir uç noktaya işaret etmek üzere değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="f44e3-179">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="f44e3-180">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="f44e3-180">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="f44e3-181">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="f44e3-181">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="f44e3-182">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="f44e3-182">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="f44e3-183">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="f44e3-183">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="f44e3-184">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="f44e3-184">LoginDisplay component</span></span>

<span data-ttu-id="f44e3-185">`LoginDisplay`Bileşen ( `Shared/LoginDisplay.razor` ) `MainLayout` bileşende () içinde işlenir `Shared/MainLayout.razor` ve aşağıdaki davranışları yönetir:</span><span class="sxs-lookup"><span data-stu-id="f44e3-185">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="f44e3-186">Kimliği doğrulanmış kullanıcılar için:</span><span class="sxs-lookup"><span data-stu-id="f44e3-186">For authenticated users:</span></span>
  * <span data-ttu-id="f44e3-187">Geçerli Kullanıcı adını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="f44e3-187">Displays the current user name.</span></span>
  * <span data-ttu-id="f44e3-188">ASP.NET Core 'daki Kullanıcı profili sayfasına bir bağlantı sunar Identity .</span><span class="sxs-lookup"><span data-stu-id="f44e3-188">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="f44e3-189">Uygulamanın oturumunu kapatmak için bir düğme sunar.</span><span class="sxs-lookup"><span data-stu-id="f44e3-189">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="f44e3-190">Anonim kullanıcılar için:</span><span class="sxs-lookup"><span data-stu-id="f44e3-190">For anonymous users:</span></span>
  * <span data-ttu-id="f44e3-191">Kayıt için seçeneği sunar.</span><span class="sxs-lookup"><span data-stu-id="f44e3-191">Offers the option to register.</span></span>
  * <span data-ttu-id="f44e3-192">Oturum açma seçeneği sunar.</span><span class="sxs-lookup"><span data-stu-id="f44e3-192">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="f44e3-193">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="f44e3-193">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="f44e3-194">FetchData bileşeni</span><span class="sxs-lookup"><span data-stu-id="f44e3-194">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="f44e3-195">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="f44e3-195">Run the app</span></span>

<span data-ttu-id="f44e3-196">Uygulamayı sunucu projesinden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="f44e3-196">Run the app from the Server project.</span></span> <span data-ttu-id="f44e3-197">Visual Studio 'yu kullanırken şunlardan birini yapın:</span><span class="sxs-lookup"><span data-stu-id="f44e3-197">When using Visual Studio, either:</span></span>

* <span data-ttu-id="f44e3-198">Araç çubuğundaki **başlangıç projeleri** açılan listesini *sunucu API 'si uygulamasına* ayarlayın ve **Çalıştır** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f44e3-198">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="f44e3-199">**Çözüm Gezgini** ' de sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya uygulamayı **Hata Ayıkla** menüsünden başlatın.</span><span class="sxs-lookup"><span data-stu-id="f44e3-199">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="f44e3-200">API yetkilendirmesi ile ad ve rol talebi</span><span class="sxs-lookup"><span data-stu-id="f44e3-200">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="f44e3-201">Özel Kullanıcı fabrikası</span><span class="sxs-lookup"><span data-stu-id="f44e3-201">Custom user factory</span></span>

<span data-ttu-id="f44e3-202">Istemci uygulamasında özel bir Kullanıcı fabrikası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f44e3-202">In the Client app, create a custom user factory.</span></span> <span data-ttu-id="f44e3-203">IdentitySunucu, tek bir talep içinde birden çok rolü JSON dizisi olarak gönderir `role` .</span><span class="sxs-lookup"><span data-stu-id="f44e3-203">Identity Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="f44e3-204">Tek bir rol, talepte bir dize değeri olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="f44e3-204">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="f44e3-205">Fabrika, `role` kullanıcının rollerinin her biri için ayrı bir talep oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f44e3-205">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="f44e3-206">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="f44e3-206">`CustomUserFactory.cs`:</span></span>

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
            var roleClaims = identity.FindAll(identity.RoleClaimType);

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

<span data-ttu-id="f44e3-207">Istemci uygulamasında, () fabrikasını kaydedin `Program.Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="f44e3-207">In the Client app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="f44e3-208">Sunucu uygulamasında, <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> Identity rol ile ilgili hizmetleri ekleyen Oluşturucu üzerinde çağırın:</span><span class="sxs-lookup"><span data-stu-id="f44e3-208">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-no-locidentity-server"></a><span data-ttu-id="f44e3-209">IdentitySunucu yapılandırma</span><span class="sxs-lookup"><span data-stu-id="f44e3-209">Configure Identity Server</span></span>

<span data-ttu-id="f44e3-210">Aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="f44e3-210">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="f44e3-211">API yetkilendirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="f44e3-211">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="f44e3-212">Profil hizmeti</span><span class="sxs-lookup"><span data-stu-id="f44e3-212">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="f44e3-213">API yetkilendirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="f44e3-213">API authorization options</span></span>

<span data-ttu-id="f44e3-214">Sunucu uygulamasında:</span><span class="sxs-lookup"><span data-stu-id="f44e3-214">In the Server app:</span></span>

* <span data-ttu-id="f44e3-215">Identity `name` Ve `role` taleplerini kimlik belirtecine ve erişim belirtecine yerleştirmek için sunucuyu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f44e3-215">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="f44e3-216">JWT belirteci işleyicisindeki roller için varsayılan eşlemeyi engelleyin.</span><span class="sxs-lookup"><span data-stu-id="f44e3-216">Prevent the default mapping for roles in the JWT token handler.</span></span>

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

#### <a name="profile-service"></a><span data-ttu-id="f44e3-217">Profil hizmeti</span><span class="sxs-lookup"><span data-stu-id="f44e3-217">Profile Service</span></span>

<span data-ttu-id="f44e3-218">Sunucu uygulamasında bir `ProfileService` uygulama oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f44e3-218">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="f44e3-219">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="f44e3-219">`ProfileService.cs`:</span></span>

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

    public Task GetProfileDataAsync(ProfileDataRequestContext context)
    {
        var nameClaim = context.Subject.FindAll(JwtClaimTypes.Name);
        context.IssuedClaims.AddRange(nameClaim);

        var roleClaims = context.Subject.FindAll(JwtClaimTypes.Role);
        context.IssuedClaims.AddRange(roleClaims);

        return Task.CompletedTask;
    }

    public Task IsActiveAsync(IsActiveContext context)
    {
        return Task.CompletedTask;
    }
}
```

<span data-ttu-id="f44e3-220">Sunucu uygulamasında profil hizmetini şu şekilde kaydedin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f44e3-220">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="f44e3-221">Yetkilendirme mekanizmalarını kullanma</span><span class="sxs-lookup"><span data-stu-id="f44e3-221">Use authorization mechanisms</span></span>

<span data-ttu-id="f44e3-222">Istemci uygulamasında, bileşen yetkilendirme yaklaşımları bu noktada işlevseldir.</span><span class="sxs-lookup"><span data-stu-id="f44e3-222">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="f44e3-223">Bileşenlerdeki yetkilendirme mekanizmalarının herhangi biri, kullanıcıyı yetkilendirmek için bir rol kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="f44e3-223">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="f44e3-224">[ `AuthorizeView` bileşen](xref:blazor/security/index#authorizeview-component) (örnek: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="f44e3-224">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="f44e3-225">[ `[Authorize]` Attribute yönergesi](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (örnek: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="f44e3-225">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="f44e3-226">[Yordamsal Logic](xref:blazor/security/index#procedural-logic) (örnek: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="f44e3-226">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="f44e3-227">Birden çok rol testi desteklenir:</span><span class="sxs-lookup"><span data-stu-id="f44e3-227">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="f44e3-228">`User.Identity.Name`, genellikle oturum açma e-posta adresi olan kullanıcının kullanıcı adıyla Istemci uygulamasına doldurulur.</span><span class="sxs-lookup"><span data-stu-id="f44e3-228">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="f44e3-229">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f44e3-229">Additional resources</span></span>

* [<span data-ttu-id="f44e3-230">Azure App Service dağıtım</span><span class="sxs-lookup"><span data-stu-id="f44e3-230">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="f44e3-231">Key Vault bir sertifikayı içeri aktarma (Azure belgeleri)</span><span class="sxs-lookup"><span data-stu-id="f44e3-231">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="f44e3-232">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="f44e3-232">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
