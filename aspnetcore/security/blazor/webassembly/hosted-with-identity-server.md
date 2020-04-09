---
title: Identity Server Blazor ile ASP.NET Core WebAssembly barındırılan uygulamayı güvenli hale getirin
author: guardrex
description: Visual Studio Blazor içinden kimlik doğrulaması ile [identityServer](https://identityserver.io/) arka uç kullanan yeni bir barındırılan uygulama oluşturmak için
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: 832109530c4aac372fd75aa1a1d2edbe3768f55f
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501278"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="5ce00-103">Identity Server Blazor ile ASP.NET Core WebAssembly barındırılan uygulamayı güvenli hale getirin</span><span class="sxs-lookup"><span data-stu-id="5ce00-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="5ce00-104">Yazar: [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="5ce00-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="5ce00-105">Visual Studio'da kullanıcıların ve API aramalarını doğrulamak için Blazor [IdentityServer'ı](https://identityserver.io/) kullanan yeni bir barındırılan uygulama oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="5ce00-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="5ce00-106">Yeni bir \*\* Blazor WebAssembly\*\* uygulaması oluşturmak için Visual Studio'yı kullanın.</span><span class="sxs-lookup"><span data-stu-id="5ce00-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="5ce00-107">Daha fazla bilgi için bkz. <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="5ce00-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="5ce00-108">\*\* Blazor Yeni bir uygulama oluştur\*\* iletişim kutusunda Kimlik **Doğrulama** bölümünde **Değiştir'i** seçin.</span><span class="sxs-lookup"><span data-stu-id="5ce00-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="5ce00-109">Ok'un ardından **Bireysel Kullanıcı Hesapları'nı** **seçin.**</span><span class="sxs-lookup"><span data-stu-id="5ce00-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="5ce00-110">**Gelişmiş** bölümünde **ASP.NET Core barındırılan** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="5ce00-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="5ce00-111">**Oluştur** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="5ce00-111">Select the **Create** button.</span></span>

<span data-ttu-id="5ce00-112">Uygulamayı komut kabuğunda oluşturmak için aşağıdaki komutu uygulayın:</span><span class="sxs-lookup"><span data-stu-id="5ce00-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="5ce00-113">Yoksa proje klasörü oluşturan çıktı konumunu belirtmek için, çıkış seçeneğini bir yolu olan komuta ekleyin `-o BlazorSample`(örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="5ce00-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="5ce00-114">Klasör adı da projenin adının bir parçası olur.</span><span class="sxs-lookup"><span data-stu-id="5ce00-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="5ce00-115">Sunucu uygulaması yapılandırması</span><span class="sxs-lookup"><span data-stu-id="5ce00-115">Server app configuration</span></span>

<span data-ttu-id="5ce00-116">Aşağıdaki bölümlerde kimlik doğrulama desteği eklendiğinde projeye yapılan eklemeler açıklanır.</span><span class="sxs-lookup"><span data-stu-id="5ce00-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="5ce00-117">Başlangıç sınıfı</span><span class="sxs-lookup"><span data-stu-id="5ce00-117">Startup class</span></span>

<span data-ttu-id="5ce00-118">Sınıfın `Startup` aşağıdaki eklemeleri vardır:</span><span class="sxs-lookup"><span data-stu-id="5ce00-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="5ce00-119">`Startup.ConfigureServices` içinde:</span><span class="sxs-lookup"><span data-stu-id="5ce00-119">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="5ce00-120">Varsayılan UI ile kimlik:</span><span class="sxs-lookup"><span data-stu-id="5ce00-120">Identity with the default UI:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="5ce00-121">IdentityServer'ın <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> üstüne bazı varsayılan ASP.NET Core kuralları nı ayarlayan ek bir yardımcı yöntemiyle IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="5ce00-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="5ce00-122">IdentityServer tarafından <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> üretilen JWT belirteçlerini doğrulamak için uygulamayı yapılandıran ek bir yardımcı yöntemiyle kimlik doğrulama:</span><span class="sxs-lookup"><span data-stu-id="5ce00-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="5ce00-123">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="5ce00-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="5ce00-124">İstek kimlik bilgilerini doğrulamave kullanıcıyı istek bağlamına ayarlamadan sorumlu kimlik doğrulama aracı:</span><span class="sxs-lookup"><span data-stu-id="5ce00-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="5ce00-125">Open ID Connect (OIDC) uç noktalarını ortaya çıkaran IdentityServer ara yazılımı:</span><span class="sxs-lookup"><span data-stu-id="5ce00-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="5ce00-126">AddApiAuthorization</span><span class="sxs-lookup"><span data-stu-id="5ce00-126">AddApiAuthorization</span></span>

<span data-ttu-id="5ce00-127"><xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> Yardımcı [yöntemi, IdentityServer'ı](https://identityserver.io/) ASP.NET Core senaryoları için yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="5ce00-127">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="5ce00-128">IdentityServer, uygulama güvenliği yle ilgili endişeleri işlemek için güçlü ve genişletilebilir bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="5ce00-128">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="5ce00-129">IdentityServer en yaygın senaryolar için gereksiz karmaşıklığı ortaya çıkarır.</span><span class="sxs-lookup"><span data-stu-id="5ce00-129">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="5ce00-130">Sonuç olarak, iyi bir başlangıç noktası olarak düşünmemiz koşuluyla bir dizi konvansiyon ve yapılandırma seçeneği sağlanır.</span><span class="sxs-lookup"><span data-stu-id="5ce00-130">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="5ce00-131">Kimlik doğrulama gereksinimleriniz değiştiğinde, IdentityServer'ın tüm gücü, bir uygulamanın gereksinimlerine uyacak şekilde kimlik doğrulamayı özelleştirmeye devam eder.</span><span class="sxs-lookup"><span data-stu-id="5ce00-131">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="5ce00-132">AddIdentityServerJwt</span><span class="sxs-lookup"><span data-stu-id="5ce00-132">AddIdentityServerJwt</span></span>

<span data-ttu-id="5ce00-133"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> Yardımcı yöntemi varsayılan kimlik doğrulama işleyicisi olarak uygulama için bir ilke düzeni yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="5ce00-133">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="5ce00-134">İlke, Identity'in Kimlik URL alanındaki `/Identity`herhangi bir alt yola yönlendirilen tüm istekleri işlemesine izin verecek şekilde yapılandırılmıştır.</span><span class="sxs-lookup"><span data-stu-id="5ce00-134">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="5ce00-135">Diğer <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> tüm istekleri işler.</span><span class="sxs-lookup"><span data-stu-id="5ce00-135">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="5ce00-136">Ayrıca, bu yöntem:</span><span class="sxs-lookup"><span data-stu-id="5ce00-136">Additionally, this method:</span></span>

* <span data-ttu-id="5ce00-137">Varsayılan kapsamı `{APPLICATION NAME}API` .'ye sahip bir API `{APPLICATION NAME}API`kaynağını IdentityServer'a kaydeder.</span><span class="sxs-lookup"><span data-stu-id="5ce00-137">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="5ce00-138">Uygulama için IdentityServer tarafından verilen belirteçleri doğrulamak için JWT Taşıyıcı Belirteç Orta ware'i yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="5ce00-138">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="5ce00-139">WeatherForecastController</span><span class="sxs-lookup"><span data-stu-id="5ce00-139">WeatherForecastController</span></span>

<span data-ttu-id="5ce00-140">*(Controllers/WeatherForecastController.cs)* olarak [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) sınıfa öznitelik uygulanır. `WeatherForecastController`</span><span class="sxs-lookup"><span data-stu-id="5ce00-140">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="5ce00-141">Öznitelik, kaynağa erişmek için varsayılan ilkeyi temel alan kullanıcıya yetki verilmesi gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="5ce00-141">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="5ce00-142">Varsayılan yetkilendirme ilkesi, daha önce bahsedilen ilke düzeni <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> tarafından ayarlanan varsayılan kimlik doğrulama düzenini kullanacak şekilde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="5ce00-142">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="5ce00-143">Yardımcı yöntemi, uygulamaya <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> gelen istekler için varsayılan işleyici olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="5ce00-143">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="5ce00-144">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="5ce00-144">ApplicationDbContext</span></span>

<span data-ttu-id="5ce00-145">`ApplicationDbContext` (Data/ApplicationDbContext.cs)*(Data/ApplicationDbContext.cs) (Data/ApplicationDbContext.cs)* olarak, IdentityServer'ın şemasını içerecek şekilde genişletmek <xref:Microsoft.EntityFrameworkCore.DbContext> <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> dışında kimlikte de kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5ce00-145">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="5ce00-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601><xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>türetilmiştir.</span><span class="sxs-lookup"><span data-stu-id="5ce00-146"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="5ce00-147">Veritabanı şemasının tam denetimini elde etmek için, <xref:Microsoft.EntityFrameworkCore.DbContext> kullanılabilir Kimlik sınıflarından birinden devralır ve `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` yönteme çağırarak bağlamı Kimlik şemasını içerecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="5ce00-147">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="5ce00-148">OidcConfigurationController</span><span class="sxs-lookup"><span data-stu-id="5ce00-148">OidcConfigurationController</span></span>

<span data-ttu-id="5ce00-149">`OidcConfigurationController` *(Controllers/OidcConfigurationController.cs)* olarak, istemci uç noktası OIDC parametrelerini sunmak üzere sağlanır.</span><span class="sxs-lookup"><span data-stu-id="5ce00-149">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="5ce00-150">Uygulama ayarları dosyaları</span><span class="sxs-lookup"><span data-stu-id="5ce00-150">App settings files</span></span>

<span data-ttu-id="5ce00-151">Proje kökündeki uygulama ayarları dosyasında *(appsettings.json)* `IdentityServer` bölümünde, yapılandırılan istemcilerin listesi açıklanır.</span><span class="sxs-lookup"><span data-stu-id="5ce00-151">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="5ce00-152">Aşağıdaki örnekte, tek bir istemci vardır.</span><span class="sxs-lookup"><span data-stu-id="5ce00-152">In the following example, there's a single client.</span></span> <span data-ttu-id="5ce00-153">İstemci adı uygulama adına karşılık gelir ve OAuth `ClientId` parametresi için sözleşme ile eşlenir.</span><span class="sxs-lookup"><span data-stu-id="5ce00-153">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="5ce00-154">Profil, yapılandırılan uygulama türünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="5ce00-154">The profile indicates the app type being configured.</span></span> <span data-ttu-id="5ce00-155">Profil, sunucunun yapılandırma işlemini basitleştiren kuralları yönlendirmek için dahili olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5ce00-155">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "BlazorApplicationWithAuthentication.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

<span data-ttu-id="5ce00-156">Geliştirme ortamı uygulama ayarları dosyasında (*uygulama ayarları. Development.json*) proje kökünde, `IdentityServer` belirteçleri imzalamak için kullanılan anahtarı açıklar.</span><span class="sxs-lookup"><span data-stu-id="5ce00-156">In the Development environment app settings file (*appsettings.Development.json*) at the project root, the `IdentityServer` section describes the key used to sign tokens.</span></span> <!-- When deploying to production, a key needs to be provisioned and deployed alongside the app, as explained in the [Deploy to production](#deploy-to-production) section. -->

```json
"IdentityServer": {
  "Key": {
    "Type": "Development"
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="5ce00-157">İstemci uygulaması yapılandırması</span><span class="sxs-lookup"><span data-stu-id="5ce00-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="5ce00-158">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="5ce00-158">Authentication package</span></span>

<span data-ttu-id="5ce00-159">Bireysel Kullanıcı Hesapları'nı kullanmak için`Individual`bir uygulama oluşturulduğunda , uygulama `Microsoft.AspNetCore.Components.WebAssembly.Authentication` otomatik olarak uygulamanın proje dosyasındaki paket için bir paket referansı alır.</span><span class="sxs-lookup"><span data-stu-id="5ce00-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="5ce00-160">Paket, uygulamanın kullanıcıları doğrulamasına ve korumalı API'leri aramak için belirteçler almasına yardımcı olan bir dizi ilkel lik sağlar.</span><span class="sxs-lookup"><span data-stu-id="5ce00-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="5ce00-161">Bir uygulamaya kimlik doğrulama ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5ce00-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="5ce00-162">Önceki `{VERSION}` paket başvuruyu makalede gösterilen `Microsoft.AspNetCore.Blazor.Templates` paketin sürümüyle <xref:blazor/get-started> değiştirin.</span><span class="sxs-lookup"><span data-stu-id="5ce00-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="5ce00-163">API yetkilendirme desteği</span><span class="sxs-lookup"><span data-stu-id="5ce00-163">API authorization support</span></span>

<span data-ttu-id="5ce00-164">Kullanıcıların kimlik doğrulaması desteği, paket içinde sağlanan uzantı yöntemi `Microsoft.AspNetCore.Components.WebAssembly.Authentication` yle servis kabına takılır.</span><span class="sxs-lookup"><span data-stu-id="5ce00-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="5ce00-165">Bu yöntem, uygulamanın varolan yetkilendirme sistemiyle etkileşim kurabilmesi için gereken tüm hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="5ce00-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="5ce00-166">Varsayılan olarak, uygulama nın yapılandırmasını kurala göre `_configuration/{client-id}`yükler.</span><span class="sxs-lookup"><span data-stu-id="5ce00-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="5ce00-167">Sözleşmeye göre, istemci kimliği uygulamanın montaj adına ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="5ce00-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="5ce00-168">Bu URL, seçeneklerle aşırı yüklemeyi çağırarak ayrı bir bitiş noktasına işaret etmek üzere değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="5ce00-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="5ce00-169">Dosyayı alma</span><span class="sxs-lookup"><span data-stu-id="5ce00-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="5ce00-170">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="5ce00-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="5ce00-171">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="5ce00-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="5ce00-172">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="5ce00-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="5ce00-173">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="5ce00-173">LoginDisplay component</span></span>

<span data-ttu-id="5ce00-174">Bileşen `LoginDisplay` *(Paylaşılan/LoginDisplay.razor)* `MainLayout` bileşeni *(Shared/MainLayout.razor)* işlenir ve aşağıdaki davranışları yönetir:</span><span class="sxs-lookup"><span data-stu-id="5ce00-174">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="5ce00-175">Kimliği doğrulanmış kullanıcılar için:</span><span class="sxs-lookup"><span data-stu-id="5ce00-175">For authenticated users:</span></span>
  * <span data-ttu-id="5ce00-176">Geçerli kullanıcı adını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="5ce00-176">Displays the current user name.</span></span>
  * <span data-ttu-id="5ce00-177">ASP.NET Çekirdek Kimlik'teki kullanıcı profili sayfasına bir bağlantı sunar.</span><span class="sxs-lookup"><span data-stu-id="5ce00-177">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="5ce00-178">Uygulamadan çıkmak için bir düğme sunar.</span><span class="sxs-lookup"><span data-stu-id="5ce00-178">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="5ce00-179">Anonim kullanıcılar için:</span><span class="sxs-lookup"><span data-stu-id="5ce00-179">For anonymous users:</span></span>
  * <span data-ttu-id="5ce00-180">Kayıt seçeneği sunar.</span><span class="sxs-lookup"><span data-stu-id="5ce00-180">Offers the option to register.</span></span>
  * <span data-ttu-id="5ce00-181">Oturum açma seçeneği sunar.</span><span class="sxs-lookup"><span data-stu-id="5ce00-181">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="5ce00-182">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="5ce00-182">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="5ce00-183">FetchData bileşeni</span><span class="sxs-lookup"><span data-stu-id="5ce00-183">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="5ce00-184">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="5ce00-184">Run the app</span></span>

<span data-ttu-id="5ce00-185">Uygulamayı Sunucu projesinden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5ce00-185">Run the app from the Server project.</span></span> <span data-ttu-id="5ce00-186">Visual Studio'yu kullanırken, **Solution Explorer'daki** Sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya **Hata Ayıklama** menüsünden uygulamayı başlatın.</span><span class="sxs-lookup"><span data-stu-id="5ce00-186">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="5ce00-187">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5ce00-187">Additional resources</span></span>

* [<span data-ttu-id="5ce00-188">Ek erişim belirteçleri isteme</span><span class="sxs-lookup"><span data-stu-id="5ce00-188">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
