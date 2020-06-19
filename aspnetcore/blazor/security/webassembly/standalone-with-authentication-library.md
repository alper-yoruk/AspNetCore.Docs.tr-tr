---
title: BlazorKimlik doğrulama kitaplığıyla ASP.NET Core webassembly tek başına uygulamasının güvenliğini sağlama
author: guardrex
description: ''
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
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: ba6b3a333a021184ad8a42d6292915e908cc6eb7
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103950"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="479a0-102">BlazorKimlik doğrulama kitaplığıyla ASP.NET Core webassembly tek başına uygulamasının güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="479a0-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="479a0-103">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="479a0-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="479a0-104">*Azure Active Directory (AAD) ve Azure Active Directory B2C (AAD B2C) için, bu konudaki yönergeleri izleyin. Bu içindekiler tablosu düğümündeki AAD ve AAD B2C konularına bakın.*</span><span class="sxs-lookup"><span data-stu-id="479a0-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="479a0-105">Blazor [Microsoft. Aspnetcore. components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) kitaplığını kullanan bir webassembly tek başına uygulaması oluşturmak için, komut kabuğu 'nda aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="479a0-105">To create a Blazor WebAssembly standalone app that uses [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="479a0-106">Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="479a0-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="479a0-107">Klasör adı Ayrıca projenin adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="479a0-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="479a0-108">Visual Studio 'da [bir Blazor webassembly uygulaması oluşturun](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="479a0-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="479a0-109">**Uygulama içi kullanıcı hesaplarını depola** seçeneğiyle **bireysel kullanıcı hesapları** için **kimlik doğrulamasını** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="479a0-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="479a0-110">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="479a0-110">Authentication package</span></span>

<span data-ttu-id="479a0-111">Tek tek kullanıcı hesaplarını kullanmak üzere bir uygulama oluşturulduğunda, uygulama uygulamanın proje dosyasındaki [Microsoft. AspNetCore. components. WebAssembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) paketi için otomatik olarak bir paket başvurusu alır.</span><span class="sxs-lookup"><span data-stu-id="479a0-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="479a0-112">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="479a0-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="479a0-113">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="479a0-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a><span data-ttu-id="479a0-114">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="479a0-114">Authentication service support</span></span>

<span data-ttu-id="479a0-115">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısına <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> [Microsoft. Aspnetcore. components. WebAssembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) paketi tarafından sağlanmış olan uzantı yöntemiyle kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="479a0-115">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="479a0-116">Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="479a0-116">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="479a0-117">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="479a0-117">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="479a0-118">Yapılandırma, dosya *üzerinde Wwwroot/appsettings.js* tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="479a0-118">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="479a0-119">Tek başına uygulamalar için kimlik doğrulama desteği, Open ID Connect (OıDC) kullanılarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="479a0-119">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="479a0-120"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>Yöntemi, OıDC kullanarak bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="479a0-120">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="479a0-121">Uygulamayı yapılandırmak için gereken değerler OıDC ile uyumlu IP 'den elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="479a0-121">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="479a0-122">Uygulamayı kaydettiğinizde, genellikle çevrimiçi portalında gerçekleşen değerleri alın.</span><span class="sxs-lookup"><span data-stu-id="479a0-122">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="479a0-123">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="479a0-123">Access token scopes</span></span>

<span data-ttu-id="479a0-124">BlazorWebassembly şablonu, uygulamayı güvenli BIR API için erişim belirteci isteyecek şekilde otomatik olarak yapılandırmaz.</span><span class="sxs-lookup"><span data-stu-id="479a0-124">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="479a0-125">Oturum açma akışının bir parçası olarak bir erişim belirteci sağlamak için, kapsamı varsayılan belirteç kapsamlarına ekleyin <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="479a0-125">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="479a0-126">Daha fazla bilgi için *ek senaryolar* makalesinin aşağıdaki bölümlerine bakın:</span><span class="sxs-lookup"><span data-stu-id="479a0-126">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="479a0-127">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="479a0-127">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="479a0-128">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="479a0-128">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="479a0-129">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="479a0-129">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="479a0-130">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="479a0-130">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="479a0-131">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="479a0-131">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="479a0-132">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="479a0-132">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="479a0-133">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="479a0-133">LoginDisplay component</span></span>

<span data-ttu-id="479a0-134">`LoginDisplay`Bileşen (*paylaşılan/logindisplay.* Razor), `MainLayout` bileşende (*paylaşılan/mainlayout. Razor*) işlenir ve aşağıdaki davranışları yönetir:</span><span class="sxs-lookup"><span data-stu-id="479a0-134">The `LoginDisplay` component (*Shared/LoginDisplay.razor*) is rendered in the `MainLayout` component (*Shared/MainLayout.razor*) and manages the following behaviors:</span></span>

* <span data-ttu-id="479a0-135">Kimliği doğrulanmış kullanıcılar için:</span><span class="sxs-lookup"><span data-stu-id="479a0-135">For authenticated users:</span></span>
  * <span data-ttu-id="479a0-136">Geçerli Kullanıcı adını görüntüler.</span><span class="sxs-lookup"><span data-stu-id="479a0-136">Displays the current username.</span></span>
  * <span data-ttu-id="479a0-137">Uygulamanın oturumunu kapatmak için bir düğme sunar.</span><span class="sxs-lookup"><span data-stu-id="479a0-137">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="479a0-138">Anonim kullanıcılar için oturum açma seçeneğini sunar.</span><span class="sxs-lookup"><span data-stu-id="479a0-138">For anonymous users, offers the option to log in.</span></span>

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

## <a name="authentication-component"></a><span data-ttu-id="479a0-139">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="479a0-139">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="479a0-140">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="479a0-140">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="479a0-141">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="479a0-141">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
