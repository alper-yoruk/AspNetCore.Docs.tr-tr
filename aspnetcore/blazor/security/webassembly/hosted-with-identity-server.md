---
title: BlazorSunucu ile ASP.NET Core weelsembly barındırılan uygulamasının güvenliğini Identity sağlama
author: guardrex
description: BlazorBir [IdentityServer](https://identityserver.io/) arka ucu kullanan Visual Studio içinden kimlik doğrulaması ile yeni bir barındırılan uygulama oluşturmak için
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/hosted-with-identity-server
ms.openlocfilehash: 8251658a2bb99ec17424ec26b8a44171082fab05
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243492"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="6d69f-103">BlazorSunucu ile ASP.NET Core weelsembly barındırılan uygulamasının güvenliğini Identity sağlama</span><span class="sxs-lookup"><span data-stu-id="6d69f-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="6d69f-104">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="6d69f-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="6d69f-105">Bu makalede Blazor , kullanıcıların ve API çağrılarının kimliğini doğrulamak Için [IdentityServer](https://identityserver.io/) kullanan yeni bir barındırılan uygulamanın nasıl oluşturulacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="6d69f-105">This article explains how to create a new Blazor hosted app that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="6d69f-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="6d69f-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="6d69f-107">Visual Studio 'da:</span><span class="sxs-lookup"><span data-stu-id="6d69f-107">In Visual Studio:</span></span>

1. <span data-ttu-id="6d69f-108">Yeni bir \*\* Blazor webassembly\*\* uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6d69f-108">Create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="6d69f-109">Daha fazla bilgi için bkz. <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="6d69f-109">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="6d69f-110">**Yeni Blazor uygulama oluştur** Iletişim kutusunda **kimlik doğrulama** bölümünde **Değiştir** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="6d69f-110">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="6d69f-111">**Her kullanıcı hesabını** ve ardından **Tamam ' ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="6d69f-111">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="6d69f-112">**Gelişmiş** bölümünde **ASP.NET Core barındırılan** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="6d69f-112">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="6d69f-113">**Oluştur** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="6d69f-113">Select the **Create** button.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="6d69f-114">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="6d69f-114">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="6d69f-115">Uygulamayı bir komut kabuğunda oluşturmak için aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="6d69f-115">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="6d69f-116">Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="6d69f-116">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="6d69f-117">Klasör adı Ayrıca projenin adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="6d69f-117">The folder name also becomes part of the project's name.</span></span>

---

## <a name="server-app-configuration"></a><span data-ttu-id="6d69f-118">Sunucu uygulaması yapılandırması</span><span class="sxs-lookup"><span data-stu-id="6d69f-118">Server app configuration</span></span>

<span data-ttu-id="6d69f-119">Aşağıdaki bölümlerde, kimlik doğrulama desteği dahil edildiğinde projenin eklemeleri açıklanır.</span><span class="sxs-lookup"><span data-stu-id="6d69f-119">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="6d69f-120">Başlangıç sınıfı</span><span class="sxs-lookup"><span data-stu-id="6d69f-120">Startup class</span></span>

<span data-ttu-id="6d69f-121">`Startup`Sınıfı aşağıdaki eklemeleri içerir.</span><span class="sxs-lookup"><span data-stu-id="6d69f-121">The `Startup` class has the following additions.</span></span>

* <span data-ttu-id="6d69f-122">`Startup.ConfigureServices` içinde:</span><span class="sxs-lookup"><span data-stu-id="6d69f-122">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="6d69f-123">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="6d69f-123">ASP.NET Core Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="6d69f-124">İdentityserver <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> en üstünde varsayılan ASP.NET Core kuralları ayarlayan ek bir yardımcı yöntemi olan IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="6d69f-124">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="6d69f-125">Kimliği <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> , IdentityServer tarafından ÜRETILEN JWT belirteçlerini doğrulamak üzere uygulamayı yapılandıran ek bir yardımcı yöntem ile kimlik doğrulaması:</span><span class="sxs-lookup"><span data-stu-id="6d69f-125">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="6d69f-126">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="6d69f-126">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="6d69f-127">IdentityServer ara yazılımı açık KIMLIK Connect (OıDC) uç noktalarını kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="6d69f-127">The IdentityServer middleware exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="6d69f-128">Kimlik doğrulama ara yazılımı, istek kimlik bilgilerini doğrulamadan ve Kullanıcı istek bağlamında ayarlamaktan sorumludur:</span><span class="sxs-lookup"><span data-stu-id="6d69f-128">The Authentication middleware is responsible for validating request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="6d69f-129">Yetki ara yazılımı yetkilendirme özelliklerini sunar:</span><span class="sxs-lookup"><span data-stu-id="6d69f-129">Authorization Middleware enables authorization capabilities:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="6d69f-130">Addadpiauthorization</span><span class="sxs-lookup"><span data-stu-id="6d69f-130">AddApiAuthorization</span></span>

<span data-ttu-id="6d69f-131"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A>Yardımcı yöntemi, [ıdentityserver](https://identityserver.io/) 'ı ASP.NET Core senaryolar için yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="6d69f-131">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="6d69f-132">IdentityServer, uygulama güvenliği sorunlarını işlemeye yönelik güçlü ve genişletilebilir bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="6d69f-132">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="6d69f-133">IdentityServer, en yaygın senaryolar için gereksiz karmaşıklık sunar.</span><span class="sxs-lookup"><span data-stu-id="6d69f-133">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="6d69f-134">Sonuç olarak, iyi bir başlangıç noktası düşüntiğimiz bir dizi kural ve yapılandırma seçeneği sağlanır.</span><span class="sxs-lookup"><span data-stu-id="6d69f-134">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="6d69f-135">Kimlik doğrulama gereksinimleriniz değiştikçe, IdentityServer 'ın tam gücü bir uygulamanın gereksinimlerine uyacak şekilde kimlik doğrulamasını özelleştirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6d69f-135">Once your authentication needs change, the full power of IdentityServer is available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="6d69f-136">Addentityserverjwt</span><span class="sxs-lookup"><span data-stu-id="6d69f-136">AddIdentityServerJwt</span></span>

<span data-ttu-id="6d69f-137"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>Yardımcı yöntemi, varsayılan kimlik doğrulama işleyicisi olarak uygulama için bir ilke düzeni yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="6d69f-137">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="6d69f-138">İlke, Identity URL alanındaki herhangi bir alt yolda yönlendirilen tüm istekleri işlemeye izin verecek şekilde yapılandırılmıştır Identity `/Identity` .</span><span class="sxs-lookup"><span data-stu-id="6d69f-138">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="6d69f-139"><xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler>Diğer tüm istekleri işler.</span><span class="sxs-lookup"><span data-stu-id="6d69f-139">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="6d69f-140">Ayrıca, bu yöntem:</span><span class="sxs-lookup"><span data-stu-id="6d69f-140">Additionally, this method:</span></span>

* <span data-ttu-id="6d69f-141">`{APPLICATION NAME}API`IdentityServer ile bir API kaynağını varsayılan kapsamına kaydeder `{APPLICATION NAME}API` .</span><span class="sxs-lookup"><span data-stu-id="6d69f-141">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="6d69f-142">Uygulama için IdentityServer tarafından verilen belirteçleri doğrulamak üzere JWT taşıyıcı belirteç ara yazılımını yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="6d69f-142">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="6d69f-143">Dalgalı bir denetleyici</span><span class="sxs-lookup"><span data-stu-id="6d69f-143">WeatherForecastController</span></span>

<span data-ttu-id="6d69f-144">`WeatherForecastController`() İçinde `Controllers/WeatherForecastController.cs` , [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) özniteliği sınıfına uygulanır.</span><span class="sxs-lookup"><span data-stu-id="6d69f-144">In the `WeatherForecastController` (`Controllers/WeatherForecastController.cs`), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="6d69f-145">Özniteliği, kullanıcıya kaynağa erişim için varsayılan ilkeye göre yetkilendirilmiş olması gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="6d69f-145">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="6d69f-146">Varsayılan yetkilendirme ilkesi, tarafından ayarlanan varsayılan kimlik doğrulama düzenini kullanacak şekilde yapılandırılır <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> .</span><span class="sxs-lookup"><span data-stu-id="6d69f-146">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A>.</span></span> <span data-ttu-id="6d69f-147">Yardımcı yöntemi, <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> uygulamaya yönelik istekler için varsayılan işleyici olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="6d69f-147">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="6d69f-148">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="6d69f-148">ApplicationDbContext</span></span>

<span data-ttu-id="6d69f-149">`ApplicationDbContext`() Öğesinde `Data/ApplicationDbContext.cs` , <xref:Microsoft.EntityFrameworkCore.DbContext> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> IdentityServer için şemayı içerecek şekilde genişletilir.</span><span class="sxs-lookup"><span data-stu-id="6d69f-149">In the `ApplicationDbContext` (`Data/ApplicationDbContext.cs`), <xref:Microsoft.EntityFrameworkCore.DbContext> extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="6d69f-150"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>, öğesinden türetilir <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> .</span><span class="sxs-lookup"><span data-stu-id="6d69f-150"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="6d69f-151">Veritabanı şemasının tam denetimini elde etmek için, kullanılabilir Identity <xref:Microsoft.EntityFrameworkCore.DbContext> sınıflardan birini ve Identity yöntemi metodunu çağırarak şemayı içerecek şekilde yapılandırın `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> .</span><span class="sxs-lookup"><span data-stu-id="6d69f-151">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A> method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="6d69f-152">Oıdcconfigurationcontroller</span><span class="sxs-lookup"><span data-stu-id="6d69f-152">OidcConfigurationController</span></span>

<span data-ttu-id="6d69f-153">`OidcConfigurationController`() İçinde `Controllers/OidcConfigurationController.cs` , istemci uç noktası OIDC parametrelerine hizmeti sunacak şekilde sağlanır.</span><span class="sxs-lookup"><span data-stu-id="6d69f-153">In the `OidcConfigurationController` (`Controllers/OidcConfigurationController.cs`), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="6d69f-154">Uygulama ayarları dosyaları</span><span class="sxs-lookup"><span data-stu-id="6d69f-154">App settings files</span></span>

<span data-ttu-id="6d69f-155">Proje kökündeki uygulama ayarları dosyasında ( `appsettings.json` ), `IdentityServer` bölümünde yapılandırılan istemcilerin listesi açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="6d69f-155">In the app settings file (`appsettings.json`) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="6d69f-156">Aşağıdaki örnekte, tek bir istemci vardır.</span><span class="sxs-lookup"><span data-stu-id="6d69f-156">In the following example, there's a single client.</span></span> <span data-ttu-id="6d69f-157">İstemci adı, uygulama adına karşılık gelir ve kural tarafından OAuth `ClientId` parametresine eşlenir.</span><span class="sxs-lookup"><span data-stu-id="6d69f-157">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="6d69f-158">Profil, yapılandırılan uygulama türünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="6d69f-158">The profile indicates the app type being configured.</span></span> <span data-ttu-id="6d69f-159">Profil, sunucu için yapılandırma işlemini basitleştiren kuralları yönlendirmek için dahili olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6d69f-159">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="6d69f-160">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `BlazorSample.Client` ).</span><span class="sxs-lookup"><span data-stu-id="6d69f-160">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.Client`).</span></span>

## <a name="client-app-configuration"></a><span data-ttu-id="6d69f-161">İstemci uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="6d69f-161">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="6d69f-162">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="6d69f-162">Authentication package</span></span>

<span data-ttu-id="6d69f-163">Bireysel kullanıcı hesapları () kullanmak üzere bir uygulama oluşturulduğunda `Individual` , uygulama otomatik olarak [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) uygulamanın proje dosyasındaki paket için bir paket başvurusu alır.</span><span class="sxs-lookup"><span data-stu-id="6d69f-163">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="6d69f-164">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="6d69f-164">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="6d69f-165">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6d69f-165">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

### <a name="api-authorization-support"></a><span data-ttu-id="6d69f-166">API yetkilendirme desteği</span><span class="sxs-lookup"><span data-stu-id="6d69f-166">API authorization support</span></span>

<span data-ttu-id="6d69f-167">Kullanıcıları kimlik doğrulama desteği, paket içinde sunulan genişletme yöntemi tarafından hizmet kapsayıcısına takılır [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="6d69f-167">The support for authenticating users is plugged into the service container by the extension method provided inside the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="6d69f-168">Bu yöntem, uygulamanın mevcut yetkilendirme sistemiyle etkileşimde bulunmak için gereken hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="6d69f-168">This method sets up the services required by the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="6d69f-169">Varsayılan olarak, uygulamanın yapılandırması tarafından kural tarafından yüklenir `_configuration/{client-id}` .</span><span class="sxs-lookup"><span data-stu-id="6d69f-169">By default, configuration for the app is loaded by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="6d69f-170">Kural gereği, istemci KIMLIĞI uygulamanın derleme adına ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="6d69f-170">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="6d69f-171">Bu URL, aþýrý yükleme seçeneklerini çağırarak ayrı bir uç noktaya işaret etmek üzere değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="6d69f-171">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="6d69f-172">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="6d69f-172">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="6d69f-173">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="6d69f-173">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="6d69f-174">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="6d69f-174">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="6d69f-175">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="6d69f-175">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="6d69f-176">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="6d69f-176">LoginDisplay component</span></span>

<span data-ttu-id="6d69f-177">`LoginDisplay`Bileşen ( `Shared/LoginDisplay.razor` ) `MainLayout` bileşende () içinde işlenir `Shared/MainLayout.razor` ve aşağıdaki davranışları yönetir:</span><span class="sxs-lookup"><span data-stu-id="6d69f-177">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="6d69f-178">Kimliği doğrulanmış kullanıcılar için:</span><span class="sxs-lookup"><span data-stu-id="6d69f-178">For authenticated users:</span></span>
  * <span data-ttu-id="6d69f-179">Geçerli Kullanıcı adını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="6d69f-179">Displays the current user name.</span></span>
  * <span data-ttu-id="6d69f-180">ASP.NET Core 'daki Kullanıcı profili sayfasına bir bağlantı sunar Identity .</span><span class="sxs-lookup"><span data-stu-id="6d69f-180">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="6d69f-181">Uygulamanın oturumunu kapatmak için bir düğme sunar.</span><span class="sxs-lookup"><span data-stu-id="6d69f-181">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="6d69f-182">Anonim kullanıcılar için:</span><span class="sxs-lookup"><span data-stu-id="6d69f-182">For anonymous users:</span></span>
  * <span data-ttu-id="6d69f-183">Kayıt için seçeneği sunar.</span><span class="sxs-lookup"><span data-stu-id="6d69f-183">Offers the option to register.</span></span>
  * <span data-ttu-id="6d69f-184">Oturum açma seçeneği sunar.</span><span class="sxs-lookup"><span data-stu-id="6d69f-184">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="6d69f-185">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="6d69f-185">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="6d69f-186">FetchData bileşeni</span><span class="sxs-lookup"><span data-stu-id="6d69f-186">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="6d69f-187">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="6d69f-187">Run the app</span></span>

<span data-ttu-id="6d69f-188">Uygulamayı sunucu projesinden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="6d69f-188">Run the app from the Server project.</span></span> <span data-ttu-id="6d69f-189">Visual Studio 'yu kullanırken şunlardan birini yapın:</span><span class="sxs-lookup"><span data-stu-id="6d69f-189">When using Visual Studio, either:</span></span>

* <span data-ttu-id="6d69f-190">Araç çubuğundaki **başlangıç projeleri** açılan listesini *sunucu API 'si uygulamasına* ayarlayın ve **Çalıştır** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="6d69f-190">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="6d69f-191">**Çözüm Gezgini** ' de sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya uygulamayı **Hata Ayıkla** menüsünden başlatın.</span><span class="sxs-lookup"><span data-stu-id="6d69f-191">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

## <a name="name-and-role-claim-with-api-authorization"></a><span data-ttu-id="6d69f-192">API yetkilendirmesi ile ad ve rol talebi</span><span class="sxs-lookup"><span data-stu-id="6d69f-192">Name and role claim with API authorization</span></span>

### <a name="custom-user-factory"></a><span data-ttu-id="6d69f-193">Özel Kullanıcı fabrikası</span><span class="sxs-lookup"><span data-stu-id="6d69f-193">Custom user factory</span></span>

<span data-ttu-id="6d69f-194">Istemci uygulamasında özel bir Kullanıcı fabrikası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6d69f-194">In the Client app, create a custom user factory.</span></span> Identity<span data-ttu-id="6d69f-195">Sunucu, tek bir talep içinde birden çok rolü JSON dizisi olarak gönderir `role` .</span><span class="sxs-lookup"><span data-stu-id="6d69f-195"> Server sends multiple roles as a JSON array in a single `role` claim.</span></span> <span data-ttu-id="6d69f-196">Tek bir rol, talepte bir dize değeri olarak gönderilir.</span><span class="sxs-lookup"><span data-stu-id="6d69f-196">A single role is sent as a string value in the claim.</span></span> <span data-ttu-id="6d69f-197">Fabrika, `role` kullanıcının rollerinin her biri için ayrı bir talep oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6d69f-197">The factory creates an individual `role` claim for each of the user's roles.</span></span>

<span data-ttu-id="6d69f-198">`CustomUserFactory.cs`:</span><span class="sxs-lookup"><span data-stu-id="6d69f-198">`CustomUserFactory.cs`:</span></span>

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

<span data-ttu-id="6d69f-199">Istemci uygulamasında, () fabrikasını kaydedin `Program.Main` `Program.cs` :</span><span class="sxs-lookup"><span data-stu-id="6d69f-199">In the Client app, register the factory in `Program.Main` (`Program.cs`):</span></span>

```csharp
builder.Services.AddApiAuthorization()
    .AddAccountClaimsPrincipalFactory<CustomUserFactory>();
```

<span data-ttu-id="6d69f-200">Sunucu uygulamasında, <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> Identity rol ile ilgili hizmetleri ekleyen Oluşturucu üzerinde çağırın:</span><span class="sxs-lookup"><span data-stu-id="6d69f-200">In the Server app, call <xref:Microsoft.AspNetCore.Identity.IdentityBuilder.AddRoles*> on the Identity builder, which adds role-related services:</span></span>

```csharp
using Microsoft.AspNetCore.Identity;

...

services.AddDefaultIdentity<ApplicationUser>(options => 
    options.SignIn.RequireConfirmedAccount = true)
    .AddRoles<IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

### <a name="configure-identity-server"></a><span data-ttu-id="6d69f-201">IdentitySunucu yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6d69f-201">Configure Identity Server</span></span>

<span data-ttu-id="6d69f-202">Aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="6d69f-202">Use **one** of the following approaches:</span></span>

* [<span data-ttu-id="6d69f-203">API yetkilendirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="6d69f-203">API authorization options</span></span>](#api-authorization-options)
* [<span data-ttu-id="6d69f-204">Profil hizmeti</span><span class="sxs-lookup"><span data-stu-id="6d69f-204">Profile Service</span></span>](#profile-service)

#### <a name="api-authorization-options"></a><span data-ttu-id="6d69f-205">API yetkilendirme seçenekleri</span><span class="sxs-lookup"><span data-stu-id="6d69f-205">API authorization options</span></span>

<span data-ttu-id="6d69f-206">Sunucu uygulamasında:</span><span class="sxs-lookup"><span data-stu-id="6d69f-206">In the Server app:</span></span>

* <span data-ttu-id="6d69f-207">Identity `name` Ve `role` taleplerini kimlik belirtecine ve erişim belirtecine yerleştirmek için sunucuyu yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="6d69f-207">Configure Identity Server to put the `name` and `role` claims into the ID token and access token.</span></span>
* <span data-ttu-id="6d69f-208">JWT belirteci işleyicisindeki roller için varsayılan eşlemeyi engelleyin.</span><span class="sxs-lookup"><span data-stu-id="6d69f-208">Prevent the default mapping for roles in the JWT token handler.</span></span>

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

#### <a name="profile-service"></a><span data-ttu-id="6d69f-209">Profil hizmeti</span><span class="sxs-lookup"><span data-stu-id="6d69f-209">Profile Service</span></span>

<span data-ttu-id="6d69f-210">Sunucu uygulamasında bir `ProfileService` uygulama oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6d69f-210">In the Server app, create a `ProfileService` implementation.</span></span>

<span data-ttu-id="6d69f-211">`ProfileService.cs`:</span><span class="sxs-lookup"><span data-stu-id="6d69f-211">`ProfileService.cs`:</span></span>

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

<span data-ttu-id="6d69f-212">Sunucu uygulamasında profil hizmetini şu şekilde kaydedin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6d69f-212">In the Server app, register the Profile Service in `Startup.ConfigureServices`:</span></span>

```csharp
using IdentityServer4.Services;

...

services.AddTransient<IProfileService, ProfileService>();
```

### <a name="use-authorization-mechanisms"></a><span data-ttu-id="6d69f-213">Yetkilendirme mekanizmalarını kullanma</span><span class="sxs-lookup"><span data-stu-id="6d69f-213">Use authorization mechanisms</span></span>

<span data-ttu-id="6d69f-214">Istemci uygulamasında, bileşen yetkilendirme yaklaşımları bu noktada işlevseldir.</span><span class="sxs-lookup"><span data-stu-id="6d69f-214">In the Client app, component authorization approaches are functional at this point.</span></span> <span data-ttu-id="6d69f-215">Bileşenlerdeki yetkilendirme mekanizmalarının herhangi biri, kullanıcıyı yetkilendirmek için bir rol kullanabilir:</span><span class="sxs-lookup"><span data-stu-id="6d69f-215">Any of the authorization mechanisms in components can use a role to authorize the user:</span></span>

* <span data-ttu-id="6d69f-216">[ `AuthorizeView` bileşen](xref:blazor/security/index#authorizeview-component) (örnek: `<AuthorizeView Roles="admin">` )</span><span class="sxs-lookup"><span data-stu-id="6d69f-216">[`AuthorizeView` component](xref:blazor/security/index#authorizeview-component) (Example: `<AuthorizeView Roles="admin">`)</span></span>
* <span data-ttu-id="6d69f-217">[ `[Authorize]` Attribute yönergesi](xref:blazor/security/index#authorize-attribute) ( <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute> ) (örnek: `@attribute [Authorize(Roles = "admin")]` )</span><span class="sxs-lookup"><span data-stu-id="6d69f-217">[`[Authorize]` attribute directive](xref:blazor/security/index#authorize-attribute) (<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute>) (Example: `@attribute [Authorize(Roles = "admin")]`)</span></span>
* <span data-ttu-id="6d69f-218">[Yordamsal Logic](xref:blazor/security/index#procedural-logic) (örnek: `if (user.IsInRole("admin")) { ... }` )</span><span class="sxs-lookup"><span data-stu-id="6d69f-218">[Procedural logic](xref:blazor/security/index#procedural-logic) (Example: `if (user.IsInRole("admin")) { ... }`)</span></span>

  <span data-ttu-id="6d69f-219">Birden çok rol testi desteklenir:</span><span class="sxs-lookup"><span data-stu-id="6d69f-219">Multiple role tests are supported:</span></span>

  ```csharp
  if (user.IsInRole("admin") && user.IsInRole("developer"))
  {
      ...
  }
  ```

<span data-ttu-id="6d69f-220">`User.Identity.Name`, genellikle oturum açma e-posta adresi olan kullanıcının kullanıcı adıyla Istemci uygulamasına doldurulur.</span><span class="sxs-lookup"><span data-stu-id="6d69f-220">`User.Identity.Name` is populated in the Client app with the user's user name, which is usually their sign-in email address.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="6d69f-221">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="6d69f-221">Additional resources</span></span>

* [<span data-ttu-id="6d69f-222">Azure App Service dağıtım</span><span class="sxs-lookup"><span data-stu-id="6d69f-222">Deployment to Azure App Service</span></span>](xref:security/authentication/identity/spa#deploy-to-production)
* [<span data-ttu-id="6d69f-223">Key Vault bir sertifikayı içeri aktarma (Azure belgeleri)</span><span class="sxs-lookup"><span data-stu-id="6d69f-223">Import a certificate from Key Vault (Azure documentation)</span></span>](/azure/app-service/configure-ssl-certificate#import-a-certificate-from-key-vault)
* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="6d69f-224">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="6d69f-224">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
