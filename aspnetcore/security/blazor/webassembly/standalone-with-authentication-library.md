---
title: Kimlik Doğrulama Blazor kitaplığı yla ASP.NET Core WebAssembly bağımsız bir uygulama güvenli
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 893fff10df37e1c2be549604f4cb83cd20049108
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977047"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="77889-102">Kimlik Doğrulama Blazor kitaplığı yla ASP.NET Core WebAssembly bağımsız bir uygulama güvenli</span><span class="sxs-lookup"><span data-stu-id="77889-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="77889-103">Yazar: [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="77889-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="77889-104">*Azure Etkin Dizin (AAD) ve Azure Etkin Dizini B2C (AAD B2C) için bu konudaki yönergeye uymayın. Bu içerik düğümü tablosundaki AAD ve AAD B2C konularına bakın.*</span><span class="sxs-lookup"><span data-stu-id="77889-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="77889-105">Kitaplık Blazor kullanan `Microsoft.AspNetCore.Components.WebAssembly.Authentication` bir WebAssembly bağımsız uygulaması oluşturmak için, komut kabuğunda aşağıdaki komutu uygulayın:</span><span class="sxs-lookup"><span data-stu-id="77889-105">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="77889-106">Yoksa proje klasörü oluşturan çıktı konumunu belirtmek için, çıkış seçeneğini bir yolu olan komuta ekleyin `-o BlazorSample`(örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="77889-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="77889-107">Klasör adı da projenin adının bir parçası olur.</span><span class="sxs-lookup"><span data-stu-id="77889-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="77889-108">Visual Studio'da [bir Blazor WebAssembly uygulaması oluşturun.](xref:blazor/get-started)</span><span class="sxs-lookup"><span data-stu-id="77889-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="77889-109">Mağaza kullanıcı hesapları uygulama içi seçeneğiyle **Kimlik Doğrulamayı** **Bireysel Kullanıcı Hesaplarına** ayarlayın. **Store user accounts in-app**</span><span class="sxs-lookup"><span data-stu-id="77889-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="77889-110">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="77889-110">Authentication package</span></span>

<span data-ttu-id="77889-111">Bireysel Kullanıcı Hesapları'nı kullanmak için bir uygulama oluşturulduğunda, uygulama `Microsoft.AspNetCore.Components.WebAssembly.Authentication` otomatik olarak uygulamanın proje dosyasındaki paket için bir paket başvurusu alır.</span><span class="sxs-lookup"><span data-stu-id="77889-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="77889-112">Paket, uygulamanın kullanıcıları doğrulamasına ve korumalı API'leri aramak için belirteçler almasına yardımcı olan bir dizi ilkel lik sağlar.</span><span class="sxs-lookup"><span data-stu-id="77889-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="77889-113">Bir uygulamaya kimlik doğrulama ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="77889-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="77889-114">Önceki `{VERSION}` paket başvuruyu makalede gösterilen `Microsoft.AspNetCore.Blazor.Templates` paketin sürümüyle <xref:blazor/get-started> değiştirin.</span><span class="sxs-lookup"><span data-stu-id="77889-114">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="77889-115">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="77889-115">Authentication service support</span></span>

<span data-ttu-id="77889-116">Kullanıcıların kimlik doğrulaması için destek, paket `AddOidcAuthentication` tarafından sağlanan uzantı yöntemiyle servis kapsayıcısında `Microsoft.AspNetCore.Components.WebAssembly.Authentication` kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="77889-116">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="77889-117">Bu yöntem, uygulamanın Kimlik Sağlayıcısı (IP) ile etkileşim kurması için gereken tüm hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="77889-117">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="77889-118">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="77889-118">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="77889-119">Bağımsız uygulamalar için kimlik doğrulama desteği Open ID Connect (OIDC) kullanılarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="77889-119">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="77889-120">Yöntem, `AddOidcAuthentication` OIDC kullanarak bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri arama kabul eder.</span><span class="sxs-lookup"><span data-stu-id="77889-120">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="77889-121">Uygulamanın yapılandırılması için gereken değerler OIDC uyumlu IP'den elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="77889-121">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="77889-122">Genellikle çevrimiçi portallarında bulunan uygulamayı kaydederken değerleri edinin.</span><span class="sxs-lookup"><span data-stu-id="77889-122">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="77889-123">Belirteç kapsamlarına erişim</span><span class="sxs-lookup"><span data-stu-id="77889-123">Access token scopes</span></span>

<span data-ttu-id="77889-124">Blazor WebAssembly şablonu, güvenli bir API için erişim belirteci istemek üzere uygulamayı otomatik olarak yapılandırmaz.</span><span class="sxs-lookup"><span data-stu-id="77889-124">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="77889-125">Oturum açma akışının bir parçası olarak bir belirteç sağlamak için, kapsamı `OidcProviderOptions`aşağıdakilerin varsayılan belirteç kapsamına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="77889-125">To provision a token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="77889-126">Azure portalı bir kapsam oluşturuyorsa URI ve uygulama API'den *401 Yetkisiz* yanıt aldığında **işlenmemiş bir özel durum atarsa,** düzeni ve ana bilgisayarı içermeyen bir KAPSAM URI kullanmayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="77889-126">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="77889-127">Örneğin, Azure portalı aşağıdaki kapsam URI biçimlerinden birini sağlayabilir:</span><span class="sxs-lookup"><span data-stu-id="77889-127">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="77889-128">Şema ve ana bilgisayar olmadan URI kapsamını tedarik edin:</span><span class="sxs-lookup"><span data-stu-id="77889-128">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="77889-129">Daha fazla bilgi için bkz. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="77889-129">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="77889-130">Dosyayı alma</span><span class="sxs-lookup"><span data-stu-id="77889-130">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="77889-131">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="77889-131">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="77889-132">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="77889-132">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="77889-133">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="77889-133">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="77889-134">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="77889-134">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="77889-135">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="77889-135">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="77889-136">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="77889-136">Additional resources</span></span>

* [<span data-ttu-id="77889-137">Ek erişim belirteçleri isteme</span><span class="sxs-lookup"><span data-stu-id="77889-137">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
