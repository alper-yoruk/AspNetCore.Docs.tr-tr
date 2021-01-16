---
title: Blazor WebAssemblyKimlik doğrulama kitaplığıyla ASP.NET Core tek başına uygulamanın güvenliğini sağlama
author: guardrex
description: Blazor WebAssemblyKimlik doğrulama kitaplığıyla ASP.NET Core tek başına uygulamasının güvenliğini nasıl sağlayacağınızı öğrenin.
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
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 3da9ea045de996602ead052f6f13ffc999273a50
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252493"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="8b4f8-103">Blazor WebAssemblyKimlik doğrulama kitaplığıyla ASP.NET Core tek başına uygulamanın güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="8b4f8-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="8b4f8-104">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="8b4f8-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="8b4f8-105">*Azure Active Directory (AAD) ve Azure Active Directory B2C (AAD B2C) için, bu konudaki yönergeleri izleyin. Bu içindekiler tablosu düğümündeki AAD ve AAD B2C konularına bakın.*</span><span class="sxs-lookup"><span data-stu-id="8b4f8-105">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="8b4f8-106">Kitaplığı kullanan [tek başına bir Blazor WebAssembly uygulama](xref:blazor/hosting-models#blazor-webassembly) oluşturmak için [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) araç seçiminiz için yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-106">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library, follow the guidance for your choice of tooling.</span></span>

> [!NOTE]
> <span data-ttu-id="8b4f8-107">IdentitySağlayıcının (IP) [OpenID Connect (OIDC)](https://openid.net/connect/)kullanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-107">The Identity Provider (IP) must use [OpenID Connect (OIDC)](https://openid.net/connect/).</span></span> <span data-ttu-id="8b4f8-108">Örneğin, Facebook 'un IP 'si OıDC uyumlu bir sağlayıcı olmadığından, bu konudaki kılavuz Facebook IP 'si ile çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-108">For example, Facebook's IP isn't an OIDC-compliant provider, so the guidance in this topic doesn't work with the Facebook IP.</span></span> <span data-ttu-id="8b4f8-109">Daha fazla bilgi için bkz. <xref:blazor/security/webassembly/index#authentication-library>.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-109">For more information, see <xref:blazor/security/webassembly/index#authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8b4f8-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b4f8-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="8b4f8-111">Blazor WebAssemblyKimlik doğrulama mekanizmasına sahip yeni bir proje oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="8b4f8-111">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="8b4f8-112">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda **Blazor WebAssembly uygulama** şablonunu seçtikten sonra, **kimlik doğrulaması** altında **Değiştir** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-112">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="8b4f8-113">ASP.NET Core sistemi kullanarak uygulama içinde kullanıcıları depolamak için **Kullanıcı hesaplarını depola ve uygulama Içi** **Kullanıcı hesapları** seçeneğini belirleyin [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="8b4f8-113">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="8b4f8-114">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8b4f8-114">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="8b4f8-115">Blazor WebAssemblyBoş bir klasörde kimlik doğrulama mekanizmasına sahip yeni bir proje oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-115">Create a new Blazor WebAssembly project with an authentication mechanism in an empty folder.</span></span> <span data-ttu-id="8b4f8-116">`Individual` `-au|--auth` ASP.NET Core sistemini kullanarak uygulama içinde kullanıcıları depolama seçeneğiyle birlikte kimlik doğrulama mekanizmasını belirtin [Identity](xref:security/authentication/identity) :</span><span class="sxs-lookup"><span data-stu-id="8b4f8-116">Specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| <span data-ttu-id="8b4f8-117">Yer tutucu</span><span class="sxs-lookup"><span data-stu-id="8b4f8-117">Placeholder</span></span>  | <span data-ttu-id="8b4f8-118">Örnek</span><span class="sxs-lookup"><span data-stu-id="8b4f8-118">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="8b4f8-119">Seçeneğiyle belirtilen çıktı konumu, `-o|--output` mevcut değilse bir proje klasörü oluşturur ve uygulamanın adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-119">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="8b4f8-120">Daha fazla bilgi için [`dotnet new`](/dotnet/core/tools/dotnet-new) .NET Core kılavuzundaki komutuna bakın.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-120">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8b4f8-121">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8b4f8-121">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="8b4f8-122">Blazor WebAssemblyKimlik doğrulama mekanizmasına sahip yeni bir proje oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="8b4f8-122">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="8b4f8-123">**Yeni Blazor WebAssembly uygulamanızı yapılandırın** adımındaki **kimlik doğrulaması** açılan listesinden **bireysel kimlik doğrulaması (uygulama içi)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-123">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="8b4f8-124">Uygulama, ASP.NET Core uygulamada depolanan bireysel kullanıcılar için oluşturulur [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="8b4f8-124">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

---

## <a name="authentication-package"></a><span data-ttu-id="8b4f8-125">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="8b4f8-125">Authentication package</span></span>

<span data-ttu-id="8b4f8-126">Tek tek kullanıcı hesaplarını kullanmak için uygulama oluşturulduğunda, uygulama otomatik olarak [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) uygulamanın proje dosyasındaki paket için bir paket başvurusu alır.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-126">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="8b4f8-127">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="8b4f8-128">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="8b4f8-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="8b4f8-129">Yer tutucu için `{VERSION}` , uygulamanın paylaşılan Framework sürümüyle eşleşen en son kararlı sürümü paketin [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)adresindeki **sürüm geçmişinde** bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-129">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="8b4f8-130">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="8b4f8-130">Authentication service support</span></span>

<span data-ttu-id="8b4f8-131">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> paket tarafından sağlanmış uzantı yöntemiyle kaydedilir [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) .</span><span class="sxs-lookup"><span data-stu-id="8b4f8-131">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="8b4f8-132">Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-132">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="8b4f8-133">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="8b4f8-133">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="8b4f8-134">Yapılandırma dosya tarafından sağlanır `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="8b4f8-134">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="8b4f8-135">Google OAuth 2,0 OıDC örneği:</span><span class="sxs-lookup"><span data-stu-id="8b4f8-135">Google OAuth 2.0 OIDC example:</span></span>

```json
{
  "Local": {
    "Authority": "https://accounts.google.com/",
    "ClientId": "2.......7-e.....................q.apps.googleusercontent.com",
    "PostLogoutRedirectUri": "https://localhost:5001/authentication/logout-callback",
    "RedirectUri": "https://localhost:5001/authentication/login-callback",
    "ResponseType": "id_token"
  }
}
```

<span data-ttu-id="8b4f8-136">Yeniden yönlendirme URI 'si ( `https://localhost:5001/authentication/login-callback` ), [](https://console.developers.google.com/apis/dashboard) **kimlik bilgileri**  >  **`{NAME}`**  >  **yetkili yeniden yönlendirme URI 'lerinde**, burada `{NAME}` uygulamanın istemci adı, Google API konsolunun **OAuth 2,0 istemci kimlikleri** uygulama listesinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-136">The redirect URI (`https://localhost:5001/authentication/login-callback`) is registered in the [Google APIs console](https://console.developers.google.com/apis/dashboard) in **Credentials** > **`{NAME}`** > **Authorized redirect URIs**, where `{NAME}` is the app's client name in the **OAuth 2.0 Client IDs** app list of the Google APIs console.</span></span>

<span data-ttu-id="8b4f8-137">Tek başına uygulamalar için kimlik doğrulama desteği, OpenID Connect (OıDC) kullanılarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-137">Authentication support for standalone apps is offered using OpenID Connect (OIDC).</span></span> <span data-ttu-id="8b4f8-138"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>Yöntemi, OıDC kullanarak bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-138">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="8b4f8-139">Uygulamayı yapılandırmak için gereken değerler OıDC ile uyumlu IP 'den elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-139">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="8b4f8-140">Uygulamayı kaydettiğinizde, genellikle çevrimiçi portalında gerçekleşen değerleri alın.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-140">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="8b4f8-141">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="8b4f8-141">Access token scopes</span></span>

<span data-ttu-id="8b4f8-142">Blazor WebAssemblyŞablon, ve için varsayılan kapsamları otomatik olarak yapılandırır `openid` `profile` .</span><span class="sxs-lookup"><span data-stu-id="8b4f8-142">The Blazor WebAssembly template automatically configures default scopes for `openid` and `profile`.</span></span>

<span data-ttu-id="8b4f8-143">Blazor WebAssemblyŞablon, uygulamayı güvenli BIR API için erişim belirteci isteyecek şekilde otomatik olarak yapılandırmaz.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-143">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="8b4f8-144">Oturum açma akışının bir parçası olarak bir erişim belirteci sağlamak için, kapsamı varsayılan belirteç kapsamlarına ekleyin <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="8b4f8-144">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="8b4f8-145">Daha fazla bilgi için *ek senaryolar* makalesinin aşağıdaki bölümlerine bakın:</span><span class="sxs-lookup"><span data-stu-id="8b4f8-145">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="8b4f8-146">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="8b4f8-146">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="8b4f8-147">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="8b4f8-147">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="8b4f8-148">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="8b4f8-148">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="8b4f8-149">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="8b4f8-149">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="8b4f8-150">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="8b4f8-150">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="8b4f8-151">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="8b4f8-151">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="8b4f8-152">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="8b4f8-152">LoginDisplay component</span></span>

<span data-ttu-id="8b4f8-153">`LoginDisplay`Bileşen ( `Shared/LoginDisplay.razor` ) `MainLayout` bileşende () içinde işlenir `Shared/MainLayout.razor` ve aşağıdaki davranışları yönetir:</span><span class="sxs-lookup"><span data-stu-id="8b4f8-153">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="8b4f8-154">Kimliği doğrulanmış kullanıcılar için:</span><span class="sxs-lookup"><span data-stu-id="8b4f8-154">For authenticated users:</span></span>
  * <span data-ttu-id="8b4f8-155">Geçerli Kullanıcı adını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-155">Displays the current username.</span></span>
  * <span data-ttu-id="8b4f8-156">Uygulamanın oturumunu kapatmak için bir düğme sunar.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-156">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="8b4f8-157">Anonim kullanıcılar için oturum açma seçeneğini sunar.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-157">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
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

## <a name="authentication-component"></a><span data-ttu-id="8b4f8-158">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="8b4f8-158">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="8b4f8-159">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="8b4f8-159">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="8b4f8-160">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="8b4f8-160">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <span data-ttu-id="8b4f8-161"><xref:host-and-deploy/proxy-load-balancer>: Üzerine yönergeler içerir:</span><span class="sxs-lookup"><span data-stu-id="8b4f8-161"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="8b4f8-162">WAN sunucularında ve iç ağlarda HTTPS şema bilgilerini korumak için Iletilen üstbilgiler ara yazılımı kullanma.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-162">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="8b4f8-163">El ile düzen yapılandırması, doğru istek yönlendirme için istek yolu değişiklikleri ve Linux ve IIS olmayan ters proxy 'ler için istek düzenini iletme dahil ek senaryolar ve kullanım örnekleri.</span><span class="sxs-lookup"><span data-stu-id="8b4f8-163">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
