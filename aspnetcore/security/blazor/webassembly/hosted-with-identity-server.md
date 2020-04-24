---
title: Kimlik sunucusuyla ASP.NET Core Blazor weelsembly barındırılan uygulamasının güvenliğini sağlama
author: guardrex
description: Bir [IdentityServer](https://identityserver.io/) arka Blazor ucu kullanan Visual Studio içinden kimlik doğrulaması ile yeni bir barındırılan uygulama oluşturmak için
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-identity-server
ms.openlocfilehash: ffdcd30ae9ce5350113569a500e99cf8db82ad65
ms.sourcegitcommit: 4f91da9ce4543b39dba5e8920a9500d3ce959746
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138618"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-identity-server"></a><span data-ttu-id="983c6-103">Kimlik sunucusuyla ASP.NET Core Blazor weelsembly barındırılan uygulamasının güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="983c6-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Identity Server</span></span>

<span data-ttu-id="983c6-104">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="983c6-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="983c6-105">Visual Studio 'da, Blazor KULLANıCıLARıN ve API çağrılarının kimliğini doğrulamak Için [IdentityServer](https://identityserver.io/) kullanan yeni bir barındırılan uygulama oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="983c6-105">To create a new Blazor hosted app in Visual Studio that uses [IdentityServer](https://identityserver.io/) to authenticate users and API calls:</span></span>

1. <span data-ttu-id="983c6-106">Yeni \*\* Blazor bir webassembly\*\* uygulaması oluşturmak için Visual Studio 'yu kullanın.</span><span class="sxs-lookup"><span data-stu-id="983c6-106">Use Visual Studio to create a new **Blazor WebAssembly** app.</span></span> <span data-ttu-id="983c6-107">Daha fazla bilgi için bkz. <xref:blazor/get-started>.</span><span class="sxs-lookup"><span data-stu-id="983c6-107">For more information, see <xref:blazor/get-started>.</span></span>
1. <span data-ttu-id="983c6-108">\*\* Blazor Yeni uygulama oluştur\*\* iletişim kutusunda **kimlik doğrulama** bölümünde **Değiştir** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="983c6-108">In the **Create a new Blazor app** dialog, select **Change** in the **Authentication** section.</span></span>
1. <span data-ttu-id="983c6-109">**Her kullanıcı hesabını** ve ardından **Tamam ' ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="983c6-109">Select **Individual User Accounts** followed by **OK**.</span></span>
1. <span data-ttu-id="983c6-110">**Gelişmiş** bölümünde **ASP.NET Core barındırılan** onay kutusunu seçin.</span><span class="sxs-lookup"><span data-stu-id="983c6-110">Select the **ASP.NET Core hosted** checkbox in the **Advanced** section.</span></span>
1. <span data-ttu-id="983c6-111">**Oluştur** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="983c6-111">Select the **Create** button.</span></span>

<span data-ttu-id="983c6-112">Uygulamayı bir komut kabuğunda oluşturmak için aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="983c6-112">To create the app in a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -ho
```

<span data-ttu-id="983c6-113">Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="983c6-113">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="983c6-114">Klasör adı Ayrıca projenin adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="983c6-114">The folder name also becomes part of the project's name.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="983c6-115">Sunucu uygulaması yapılandırması</span><span class="sxs-lookup"><span data-stu-id="983c6-115">Server app configuration</span></span>

<span data-ttu-id="983c6-116">Aşağıdaki bölümlerde, kimlik doğrulama desteği dahil edildiğinde projenin eklemeleri açıklanır.</span><span class="sxs-lookup"><span data-stu-id="983c6-116">The following sections describe additions to the project when authentication support is included.</span></span>

### <a name="startup-class"></a><span data-ttu-id="983c6-117">Başlangıç sınıfı</span><span class="sxs-lookup"><span data-stu-id="983c6-117">Startup class</span></span>

<span data-ttu-id="983c6-118">`Startup` Sınıfı aşağıdaki eklemelere sahiptir:</span><span class="sxs-lookup"><span data-stu-id="983c6-118">The `Startup` class has the following additions:</span></span>

* <span data-ttu-id="983c6-119">`Startup.ConfigureServices` içinde:</span><span class="sxs-lookup"><span data-stu-id="983c6-119">In `Startup.ConfigureServices`:</span></span>

  * <span data-ttu-id="983c6-120">IDENTITY</span><span class="sxs-lookup"><span data-stu-id="983c6-120">Identity:</span></span>

    ```csharp
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));

    services.AddDefaultIdentity<ApplicationUser>(options => 
            options.SignIn.RequireConfirmedAccount = true)
        .AddEntityFrameworkStores<ApplicationDbContext>();
    ```

  * <span data-ttu-id="983c6-121">IdentityServer 'ın en üstünde <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> bazı varsayılan ASP.NET Core kuralları ayarlayan ek bir yardımcı yöntemi olan IdentityServer:</span><span class="sxs-lookup"><span data-stu-id="983c6-121">IdentityServer with an additional <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method that sets up some default ASP.NET Core conventions on top of IdentityServer:</span></span>

    ```csharp
    services.AddIdentityServer()
        .AddApiAuthorization<ApplicationUser, ApplicationDbContext>();
    ```

  * <span data-ttu-id="983c6-122">Kimliği, IdentityServer <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> tarafından üretilen JWT belirteçlerini doğrulamak üzere uygulamayı yapılandıran ek bir yardımcı yöntem ile kimlik doğrulaması:</span><span class="sxs-lookup"><span data-stu-id="983c6-122">Authentication with an additional <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method that configures the app to validate JWT tokens produced by IdentityServer:</span></span>

    ```csharp
    services.AddAuthentication()
        .AddIdentityServerJwt();
    ```

* <span data-ttu-id="983c6-123">`Startup.Configure` içinde:</span><span class="sxs-lookup"><span data-stu-id="983c6-123">In `Startup.Configure`:</span></span>

  * <span data-ttu-id="983c6-124">İstek kimlik bilgilerini doğrulamadan ve Kullanıcı istek bağlamında ayarlamaktan sorumlu kimlik doğrulama ara yazılımı:</span><span class="sxs-lookup"><span data-stu-id="983c6-124">The authentication middleware that is responsible for validating the request credentials and setting the user on the request context:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

  * <span data-ttu-id="983c6-125">Açık KIMLIK Connect (OıDC) uç noktalarını kullanıma sunan IdentityServer ara yazılımı:</span><span class="sxs-lookup"><span data-stu-id="983c6-125">The IdentityServer middleware that exposes the Open ID Connect (OIDC) endpoints:</span></span>

    ```csharp
    app.UseIdentityServer();
    ```

  * <span data-ttu-id="983c6-126">Kimlik doğrulama ve yetkilendirme ara yazılımı:</span><span class="sxs-lookup"><span data-stu-id="983c6-126">Authentication and Authorization Middleware:</span></span>

    ```csharp
    app.UseAuthentication();
    app.UseAuthorization();
    ```

### <a name="addapiauthorization"></a><span data-ttu-id="983c6-127">Addadpiauthorization</span><span class="sxs-lookup"><span data-stu-id="983c6-127">AddApiAuthorization</span></span>

<span data-ttu-id="983c6-128">Yardımcı <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> yöntemi, [ıdentityserver](https://identityserver.io/) 'ı ASP.NET Core senaryolar için yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="983c6-128">The <xref:Microsoft.Extensions.DependencyInjection.IdentityServerBuilderConfigurationExtensions.AddApiAuthorization%2A> helper method configures [IdentityServer](https://identityserver.io/) for ASP.NET Core scenarios.</span></span> <span data-ttu-id="983c6-129">IdentityServer, uygulama güvenliği sorunlarını işlemeye yönelik güçlü ve genişletilebilir bir çerçevedir.</span><span class="sxs-lookup"><span data-stu-id="983c6-129">IdentityServer is a powerful and extensible framework for handling app security concerns.</span></span> <span data-ttu-id="983c6-130">IdentityServer, en yaygın senaryolar için gereksiz karmaşıklık sunar.</span><span class="sxs-lookup"><span data-stu-id="983c6-130">IdentityServer exposes unnecessary complexity for the most common scenarios.</span></span> <span data-ttu-id="983c6-131">Sonuç olarak, iyi bir başlangıç noktası düşüntiğimiz bir dizi kural ve yapılandırma seçeneği sağlanır.</span><span class="sxs-lookup"><span data-stu-id="983c6-131">Consequently, a set of conventions and configuration options is provided that we consider a good starting point.</span></span> <span data-ttu-id="983c6-132">Kimlik doğrulama gereksinimleriniz değiştikçe, IdentityServer 'ın tam gücü, kimlik doğrulamasını uygulamanın gereksinimlerine uyacak şekilde özelleştirmek için hala kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="983c6-132">Once your authentication needs change, the full power of IdentityServer is still available to customize authentication to suit an app's requirements.</span></span>

### <a name="addidentityserverjwt"></a><span data-ttu-id="983c6-133">Addentityserverjwt</span><span class="sxs-lookup"><span data-stu-id="983c6-133">AddIdentityServerJwt</span></span>

<span data-ttu-id="983c6-134"><xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> Yardımcı yöntemi, varsayılan kimlik doğrulama işleyicisi olarak uygulama için bir ilke düzeni yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="983c6-134">The <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> helper method configures a policy scheme for the app as the default authentication handler.</span></span> <span data-ttu-id="983c6-135">İlke, kimliğin kimlik URL 'sindeki herhangi bir alt yolda yönlendirilen tüm istekleri işlemesine izin verecek şekilde yapılandırılmıştır `/Identity`.</span><span class="sxs-lookup"><span data-stu-id="983c6-135">The policy is configured to allow Identity to handle all requests routed to any subpath in the Identity URL space `/Identity`.</span></span> <span data-ttu-id="983c6-136">Diğer <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> tüm istekleri işler.</span><span class="sxs-lookup"><span data-stu-id="983c6-136">The <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> handles all other requests.</span></span> <span data-ttu-id="983c6-137">Ayrıca, bu yöntem:</span><span class="sxs-lookup"><span data-stu-id="983c6-137">Additionally, this method:</span></span>

* <span data-ttu-id="983c6-138">IdentityServer `{APPLICATION NAME}API` ile bir API kaynağını varsayılan kapsamına kaydeder `{APPLICATION NAME}API`.</span><span class="sxs-lookup"><span data-stu-id="983c6-138">Registers an `{APPLICATION NAME}API` API resource with IdentityServer with a default scope of `{APPLICATION NAME}API`.</span></span>
* <span data-ttu-id="983c6-139">Uygulama için IdentityServer tarafından verilen belirteçleri doğrulamak üzere JWT taşıyıcı belirteç ara yazılımını yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="983c6-139">Configures the JWT Bearer Token Middleware to validate tokens issued by IdentityServer for the app.</span></span>

### <a name="weatherforecastcontroller"></a><span data-ttu-id="983c6-140">Dalgalı bir denetleyici</span><span class="sxs-lookup"><span data-stu-id="983c6-140">WeatherForecastController</span></span>

<span data-ttu-id="983c6-141">`WeatherForecastController` (*Controllers/dalgalı therforeroı Controller. cs*) içinde, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) özniteliği sınıfına uygulanır.</span><span class="sxs-lookup"><span data-stu-id="983c6-141">In the `WeatherForecastController` (*Controllers/WeatherForecastController.cs*), the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute is applied to the class.</span></span> <span data-ttu-id="983c6-142">Özniteliği, kullanıcıya kaynağa erişim için varsayılan ilkeye göre yetkilendirilmiş olması gerektiğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="983c6-142">The attribute indicates that the user must be authorized based on the default policy to access the resource.</span></span> <span data-ttu-id="983c6-143">Varsayılan yetkilendirme ilkesi, tarafından <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> daha önce bahsedilen ilke şemasına ayarlanan varsayılan kimlik doğrulama düzenini kullanacak şekilde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="983c6-143">The default authorization policy is configured to use the default authentication scheme, which is set up by <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> to the policy scheme that was mentioned earlier.</span></span> <span data-ttu-id="983c6-144">Yardımcı yöntemi, uygulamaya <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> yönelik istekler için varsayılan işleyici olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="983c6-144">The helper method configures <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerHandler> as the default handler for requests to the app.</span></span>

### <a name="applicationdbcontext"></a><span data-ttu-id="983c6-145">ApplicationDbContext</span><span class="sxs-lookup"><span data-stu-id="983c6-145">ApplicationDbContext</span></span>

<span data-ttu-id="983c6-146">`ApplicationDbContext` (*Data/applicationdbcontext. cs*) öğesinde aynı <xref:Microsoft.EntityFrameworkCore.DbContext> şekilde, kimlik içinde, IdentityServer şemasını dahil etmek için genişlettiği <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> özel durumla birlikte kullanılır.</span><span class="sxs-lookup"><span data-stu-id="983c6-146">In the `ApplicationDbContext` (*Data/ApplicationDbContext.cs*), the same <xref:Microsoft.EntityFrameworkCore.DbContext> is used in Identity with the exception that it extends <xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> to include the schema for IdentityServer.</span></span> <span data-ttu-id="983c6-147"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601>, öğesinden <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>türetilir.</span><span class="sxs-lookup"><span data-stu-id="983c6-147"><xref:Microsoft.AspNetCore.ApiAuthorization.IdentityServer.ApiAuthorizationDbContext%601> is derived from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>.</span></span>

<span data-ttu-id="983c6-148">Veritabanı şeması üzerinde tam denetim elde etmek için, kullanılabilir kimlik <xref:Microsoft.EntityFrameworkCore.DbContext> sınıflarından birini ve `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` `OnModelCreating` yöntemi metodunu çağırarak kimlik şemasını içerecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="983c6-148">To gain full control of the database schema, inherit from one of the available Identity <xref:Microsoft.EntityFrameworkCore.DbContext> classes and configure the context to include the Identity schema by calling `builder.ConfigurePersistedGrantContext(_operationalStoreOptions.Value)` in the `OnModelCreating` method.</span></span>

### <a name="oidcconfigurationcontroller"></a><span data-ttu-id="983c6-149">Oıdcconfigurationcontroller</span><span class="sxs-lookup"><span data-stu-id="983c6-149">OidcConfigurationController</span></span>

<span data-ttu-id="983c6-150">`OidcConfigurationController` (*Controllers/oıdcconfigurationcontroller. cs*) öğesinde, istemci uç noktası OIDC parametrelerine hizmeti sunacak şekilde sağlanır.</span><span class="sxs-lookup"><span data-stu-id="983c6-150">In the `OidcConfigurationController` (*Controllers/OidcConfigurationController.cs*), the client endpoint is provisioned to serve OIDC parameters.</span></span>

### <a name="app-settings-files"></a><span data-ttu-id="983c6-151">Uygulama ayarları dosyaları</span><span class="sxs-lookup"><span data-stu-id="983c6-151">App settings files</span></span>

<span data-ttu-id="983c6-152">Proje kökündeki App settings dosyasında (*appSettings. JSON*), `IdentityServer` bölümünde yapılandırılan istemcilerin listesi açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="983c6-152">In the app settings file (*appsettings.json*) at the project root, the `IdentityServer` section describes the list of configured clients.</span></span> <span data-ttu-id="983c6-153">Aşağıdaki örnekte, tek bir istemci vardır.</span><span class="sxs-lookup"><span data-stu-id="983c6-153">In the following example, there's a single client.</span></span> <span data-ttu-id="983c6-154">İstemci adı, uygulama adına karşılık gelir ve kural tarafından OAuth `ClientId` parametresine eşlenir.</span><span class="sxs-lookup"><span data-stu-id="983c6-154">The client name corresponds to the app name and is mapped by convention to the OAuth `ClientId` parameter.</span></span> <span data-ttu-id="983c6-155">Profil, yapılandırılan uygulama türünü gösterir.</span><span class="sxs-lookup"><span data-stu-id="983c6-155">The profile indicates the app type being configured.</span></span> <span data-ttu-id="983c6-156">Profil, sunucu için yapılandırma işlemini basitleştiren kuralları yönlendirmek için dahili olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="983c6-156">The profile is used internally to drive conventions that simplify the configuration process for the server.</span></span> <!-- There are several profiles available, as explained in the [Application profiles](#application-profiles) section. -->

```json
"IdentityServer": {
  "Clients": {
    "{APP ASSEMBLY}.Client": {
      "Profile": "IdentityServerSPA"
    }
  }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="983c6-157">İstemci uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="983c6-157">Client app configuration</span></span>

### <a name="authentication-package"></a><span data-ttu-id="983c6-158">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="983c6-158">Authentication package</span></span>

<span data-ttu-id="983c6-159">Bireysel kullanıcı hesapları (`Individual`) kullanmak üzere bir uygulama oluşturulduğunda, uygulama otomatik olarak uygulamanın proje dosyasındaki `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paket için bir paket başvurusu alır.</span><span class="sxs-lookup"><span data-stu-id="983c6-159">When an app is created to use Individual User Accounts (`Individual`), the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="983c6-160">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="983c6-160">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="983c6-161">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="983c6-161">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="983c6-162">Yukarıdaki `{VERSION}` paket başvurusunda, `Microsoft.AspNetCore.Blazor.Templates` <xref:blazor/get-started> makalede gösterilen paketin sürümüyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="983c6-162">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

### <a name="api-authorization-support"></a><span data-ttu-id="983c6-163">API yetkilendirme desteği</span><span class="sxs-lookup"><span data-stu-id="983c6-163">API authorization support</span></span>

<span data-ttu-id="983c6-164">Kullanıcıları kimlik doğrulama desteği, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paket içinde sunulan genişletme yöntemi tarafından hizmet kapsayıcısına takılır.</span><span class="sxs-lookup"><span data-stu-id="983c6-164">The support for authenticating users is plugged into the service container by the extension method provided inside the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="983c6-165">Bu yöntem, uygulamanın var olan yetkilendirme sistemiyle etkileşime geçmesini sağlamak için gereken tüm hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="983c6-165">This method sets up all the services needed for the app to interact with the existing authorization system.</span></span>

```csharp
builder.Services.AddApiAuthorization();
```

<span data-ttu-id="983c6-166">Varsayılan olarak, uygulamayı kuralına göre uygulamanın yapılandırmasını yükler `_configuration/{client-id}`.</span><span class="sxs-lookup"><span data-stu-id="983c6-166">By default, it loads the configuration for the app by convention from `_configuration/{client-id}`.</span></span> <span data-ttu-id="983c6-167">Kural gereği, istemci KIMLIĞI uygulamanın derleme adına ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="983c6-167">By convention, the client ID is set to the app's assembly name.</span></span> <span data-ttu-id="983c6-168">Bu URL, aþýrý yükleme seçeneklerini çağırarak ayrı bir uç noktaya işaret etmek üzere değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="983c6-168">This URL can be changed to point to a separate endpoint by calling the overload with options.</span></span>

### <a name="imports-file"></a><span data-ttu-id="983c6-169">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="983c6-169">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="983c6-170">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="983c6-170">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

### <a name="app-component"></a><span data-ttu-id="983c6-171">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="983c6-171">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="983c6-172">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="983c6-172">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="983c6-173">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="983c6-173">LoginDisplay component</span></span>

<span data-ttu-id="983c6-174">Bileşen (*paylaşılan/LoginDisplay.* Razor) `MainLayout` , bileşende (*paylaşılan/mainlayout. Razor*) işlenir ve aşağıdaki davranışları yönetir: `LoginDisplay`</span><span class="sxs-lookup"><span data-stu-id="983c6-174">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="983c6-175">Kimliği doğrulanmış kullanıcılar için:</span><span class="sxs-lookup"><span data-stu-id="983c6-175">For authenticated users:</span></span>
  * <span data-ttu-id="983c6-176">Geçerli Kullanıcı adını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="983c6-176">Displays the current user name.</span></span>
  * <span data-ttu-id="983c6-177">ASP.NET Core kimliği içindeki kullanıcı profili sayfasına bir bağlantı sunar.</span><span class="sxs-lookup"><span data-stu-id="983c6-177">Offers a link to the user profile page in ASP.NET Core Identity.</span></span>
  * <span data-ttu-id="983c6-178">Uygulamanın oturumunu kapatmak için bir düğme sunar.</span><span class="sxs-lookup"><span data-stu-id="983c6-178">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="983c6-179">Anonim kullanıcılar için:</span><span class="sxs-lookup"><span data-stu-id="983c6-179">For anonymous users:</span></span>
  * <span data-ttu-id="983c6-180">Kayıt için seçeneği sunar.</span><span class="sxs-lookup"><span data-stu-id="983c6-180">Offers the option to register.</span></span>
  * <span data-ttu-id="983c6-181">Oturum açma seçeneği sunar.</span><span class="sxs-lookup"><span data-stu-id="983c6-181">Offers the option to log in.</span></span>

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

### <a name="authentication-component"></a><span data-ttu-id="983c6-182">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="983c6-182">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="983c6-183">FetchData bileşeni</span><span class="sxs-lookup"><span data-stu-id="983c6-183">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="983c6-184">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="983c6-184">Run the app</span></span>

<span data-ttu-id="983c6-185">Uygulamayı sunucu projesinden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="983c6-185">Run the app from the Server project.</span></span> <span data-ttu-id="983c6-186">Visual Studio 'Yu kullanırken **Çözüm Gezgini** ' de sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya uygulamayı **Hata Ayıkla** menüsünden başlatın.</span><span class="sxs-lookup"><span data-stu-id="983c6-186">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="983c6-187">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="983c6-187">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
