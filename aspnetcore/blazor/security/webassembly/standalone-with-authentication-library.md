---
title: Blazor WebAssemblyKimlik doğrulama kitaplığıyla ASP.NET Core tek başına uygulamanın güvenliğini sağlama
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: d3611e26414f5dd1103ca9af15b17f9813023e21
ms.sourcegitcommit: 84150702757cf7a7b839485382420e8db8e92b9c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87818865"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="644eb-102">Blazor WebAssemblyKimlik doğrulama kitaplığıyla ASP.NET Core tek başına uygulamanın güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="644eb-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="644eb-103">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="644eb-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="644eb-104">*Azure Active Directory (AAD) ve Azure Active Directory B2C (AAD B2C) için, bu konudaki yönergeleri izleyin. Bu içindekiler tablosu düğümündeki AAD ve AAD B2C konularına bakın.*</span><span class="sxs-lookup"><span data-stu-id="644eb-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="644eb-105">Blazor WebAssemblyKitaplığı kullanan tek başına bir uygulama oluşturmak için [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) araç seçiminiz için yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="644eb-105">To create a Blazor WebAssembly standalone app that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library, follow the guidance for your choice of tooling.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="644eb-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="644eb-106">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="644eb-107">Blazor WebAssemblyKimlik doğrulama mekanizmasına sahip yeni bir proje oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="644eb-107">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="644eb-108">**Yeni bir ASP.NET Core Web uygulaması oluştur** iletişim kutusunda \*\* Blazor WebAssembly uygulama\*\* şablonunu seçtikten sonra, **kimlik doğrulaması**altında **Değiştir** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="644eb-108">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="644eb-109">ASP.NET Core sistemi kullanarak uygulama içinde kullanıcıları depolamak için **Kullanıcı hesaplarını depola ve uygulama Içi** **Kullanıcı hesapları** seçeneğini belirleyin [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="644eb-109">Select **Individual User Accounts** with the **Store user accounts in-app** option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="644eb-110">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="644eb-110">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="644eb-111">Blazor WebAssemblyBoş bir klasörde kimlik doğrulama mekanizmasına sahip yeni bir proje oluşturun.</span><span class="sxs-lookup"><span data-stu-id="644eb-111">Create a new Blazor WebAssembly project with an authentication mechanism in an empty folder.</span></span> <span data-ttu-id="644eb-112">`Individual` `-au|--auth` ASP.NET Core sistemini kullanarak uygulama içinde kullanıcıları depolama seçeneğiyle birlikte kimlik doğrulama mekanizmasını belirtin [Identity](xref:security/authentication/identity) :</span><span class="sxs-lookup"><span data-stu-id="644eb-112">Specify the `Individual` authentication mechanism with the `-au|--auth` option to store users within the app using ASP.NET Core's [Identity](xref:security/authentication/identity) system:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| <span data-ttu-id="644eb-113">Yer tutucu</span><span class="sxs-lookup"><span data-stu-id="644eb-113">Placeholder</span></span>  | <span data-ttu-id="644eb-114">Örnek</span><span class="sxs-lookup"><span data-stu-id="644eb-114">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="644eb-115">Seçeneğiyle belirtilen çıktı konumu, `-o|--output` mevcut değilse bir proje klasörü oluşturur ve uygulamanın adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="644eb-115">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="644eb-116">Daha fazla bilgi için [`dotnet new`](/dotnet/core/tools/dotnet-new) .NET Core kılavuzundaki komutuna bakın.</span><span class="sxs-lookup"><span data-stu-id="644eb-116">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="644eb-117">Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="644eb-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="644eb-118">Blazor WebAssemblyKimlik doğrulama mekanizmasına sahip yeni bir proje oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="644eb-118">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="644eb-119">**Yeni Blazor WebAssembly uygulamanızı yapılandırın** adımındaki **kimlik doğrulaması** açılan listesinden **bireysel kimlik doğrulaması (uygulama içi)** seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="644eb-119">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="644eb-120">Uygulama, ASP.NET Core uygulamada depolanan bireysel kullanıcılar için oluşturulur [Identity](xref:security/authentication/identity) .</span><span class="sxs-lookup"><span data-stu-id="644eb-120">The app is created for individual users stored in the app with ASP.NET Core [Identity](xref:security/authentication/identity).</span></span>

---

## <a name="authentication-package"></a><span data-ttu-id="644eb-121">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="644eb-121">Authentication package</span></span>

<span data-ttu-id="644eb-122">Tek tek kullanıcı hesaplarını kullanmak için uygulama oluşturulduğunda, uygulama otomatik olarak [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) uygulamanın proje dosyasındaki paket için bir paket başvurusu alır.</span><span class="sxs-lookup"><span data-stu-id="644eb-122">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="644eb-123">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="644eb-123">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="644eb-124">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="644eb-124">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a><span data-ttu-id="644eb-125">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="644eb-125">Authentication service support</span></span>

<span data-ttu-id="644eb-126">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> paket tarafından sağlanmış uzantı yöntemiyle kaydedilir [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="644eb-126">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="644eb-127">Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="644eb-127">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="644eb-128">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="644eb-128">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="644eb-129">Yapılandırma dosya tarafından sağlanır `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="644eb-129">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="644eb-130">Tek başına uygulamalar için kimlik doğrulama desteği, OpenID Connect (OıDC) kullanılarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="644eb-130">Authentication support for standalone apps is offered using OpenID Connect (OIDC).</span></span> <span data-ttu-id="644eb-131"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>Yöntemi, OıDC kullanarak bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="644eb-131">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="644eb-132">Uygulamayı yapılandırmak için gereken değerler OıDC ile uyumlu IP 'den elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="644eb-132">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="644eb-133">Uygulamayı kaydettiğinizde, genellikle çevrimiçi portalında gerçekleşen değerleri alın.</span><span class="sxs-lookup"><span data-stu-id="644eb-133">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="644eb-134">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="644eb-134">Access token scopes</span></span>

<span data-ttu-id="644eb-135">Blazor WebAssemblyŞablon, uygulamayı güvenli BIR API için erişim belirteci isteyecek şekilde otomatik olarak yapılandırmaz.</span><span class="sxs-lookup"><span data-stu-id="644eb-135">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="644eb-136">Oturum açma akışının bir parçası olarak bir erişim belirteci sağlamak için, kapsamı varsayılan belirteç kapsamlarına ekleyin <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="644eb-136">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="644eb-137">Daha fazla bilgi için *ek senaryolar* makalesinin aşağıdaki bölümlerine bakın:</span><span class="sxs-lookup"><span data-stu-id="644eb-137">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="644eb-138">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="644eb-138">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="644eb-139">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="644eb-139">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="644eb-140">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="644eb-140">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="644eb-141">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="644eb-141">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="644eb-142">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="644eb-142">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="644eb-143">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="644eb-143">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="644eb-144">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="644eb-144">LoginDisplay component</span></span>

<span data-ttu-id="644eb-145">`LoginDisplay`Bileşen ( `Shared/LoginDisplay.razor` ) `MainLayout` bileşende () içinde işlenir `Shared/MainLayout.razor` ve aşağıdaki davranışları yönetir:</span><span class="sxs-lookup"><span data-stu-id="644eb-145">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="644eb-146">Kimliği doğrulanmış kullanıcılar için:</span><span class="sxs-lookup"><span data-stu-id="644eb-146">For authenticated users:</span></span>
  * <span data-ttu-id="644eb-147">Geçerli Kullanıcı adını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="644eb-147">Displays the current username.</span></span>
  * <span data-ttu-id="644eb-148">Uygulamanın oturumunu kapatmak için bir düğme sunar.</span><span class="sxs-lookup"><span data-stu-id="644eb-148">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="644eb-149">Anonim kullanıcılar için oturum açma seçeneğini sunar.</span><span class="sxs-lookup"><span data-stu-id="644eb-149">For anonymous users, offers the option to log in.</span></span>

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

## <a name="authentication-component"></a><span data-ttu-id="644eb-150">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="644eb-150">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="644eb-151">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="644eb-151">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="644eb-152">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="644eb-152">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
