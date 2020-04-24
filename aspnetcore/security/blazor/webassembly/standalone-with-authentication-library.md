---
title: Kimlik doğrulama kitaplığıyla Blazor ASP.NET Core webassembly tek başına uygulamasının güvenliğini sağlama
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 043e4548ad6f40fdf1e6c27cd51946c7bf59a66e
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110960"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="7e990-102">Kimlik doğrulama kitaplığıyla Blazor ASP.NET Core webassembly tek başına uygulamasının güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="7e990-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="7e990-103">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="7e990-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="7e990-104">Bu makaledeki kılavuz, ASP.NET Core 3,2 Preview 4 için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="7e990-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="7e990-105">Bu konu, 24 Nisan, Cuma günü, Önizleme 5 ' i kapsayacak şekilde güncelleştirilecektir.</span><span class="sxs-lookup"><span data-stu-id="7e990-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="7e990-106">*Azure Active Directory (AAD) ve Azure Active Directory B2C (AAD B2C) için, bu konudaki yönergeleri izleyin. Bu içindekiler tablosu düğümündeki AAD ve AAD B2C konularına bakın.*</span><span class="sxs-lookup"><span data-stu-id="7e990-106">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="7e990-107">Kitaplığı kullanan `Microsoft.AspNetCore.Components.WebAssembly.Authentication` bir Blazor webassembly tek başına uygulaması oluşturmak için, komut kabuğu 'nda aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="7e990-107">To create a Blazor WebAssembly standalone app that uses `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="7e990-108">Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="7e990-108">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="7e990-109">Klasör adı Ayrıca projenin adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="7e990-109">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="7e990-110">Visual Studio 'da [bir Blazor webassembly uygulaması oluşturun](xref:blazor/get-started).</span><span class="sxs-lookup"><span data-stu-id="7e990-110">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="7e990-111">**Uygulama içi kullanıcı hesaplarını depola** seçeneğiyle **bireysel kullanıcı hesapları** için **kimlik doğrulamasını** ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="7e990-111">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="7e990-112">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="7e990-112">Authentication package</span></span>

<span data-ttu-id="7e990-113">Tek tek kullanıcı hesaplarını kullanmak için uygulama oluşturulduğunda, uygulama otomatik olarak uygulamanın proje dosyasındaki `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paket için bir paket başvurusu alır.</span><span class="sxs-lookup"><span data-stu-id="7e990-113">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package in the app's project file.</span></span> <span data-ttu-id="7e990-114">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="7e990-114">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="7e990-115">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="7e990-115">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

<span data-ttu-id="7e990-116">Yukarıdaki `{VERSION}` paket başvurusunda, `Microsoft.AspNetCore.Blazor.Templates` <xref:blazor/get-started> makalede gösterilen paketin sürümüyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="7e990-116">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="7e990-117">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="7e990-117">Authentication service support</span></span>

<span data-ttu-id="7e990-118">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında `AddOidcAuthentication` `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paket tarafından sağlanmış uzantı yöntemiyle kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="7e990-118">Support for authenticating users is registered in the service container with the `AddOidcAuthentication` extension method provided by the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package.</span></span> <span data-ttu-id="7e990-119">Bu yöntem, uygulamanın kimlik sağlayıcısıyla (IP) etkileşim kurması için gereken tüm hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="7e990-119">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="7e990-120">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="7e990-120">*Program.cs*:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="7e990-121">Tek başına uygulamalar için kimlik doğrulama desteği, Open ID Connect (OıDC) kullanılarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="7e990-121">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="7e990-122">Yöntemi `AddOidcAuthentication` , OIDC kullanarak bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="7e990-122">The `AddOidcAuthentication` method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="7e990-123">Uygulamayı yapılandırmak için gereken değerler OıDC ile uyumlu IP 'den elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="7e990-123">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="7e990-124">Uygulamayı kaydettiğinizde, genellikle çevrimiçi portalında gerçekleşen değerleri alın.</span><span class="sxs-lookup"><span data-stu-id="7e990-124">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="7e990-125">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="7e990-125">Access token scopes</span></span>

<span data-ttu-id="7e990-126">Blazor Webassembly şablonu, uygulamayı GÜVENLI bir API için erişim belirteci isteyecek şekilde otomatik olarak yapılandırmaz.</span><span class="sxs-lookup"><span data-stu-id="7e990-126">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="7e990-127">Oturum açma akışının bir parçası olarak bir belirteç sağlamak için, kapsamı varsayılan belirteç kapsamlarına ekleyin `OidcProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="7e990-127">To provision a token as part of the sign-in flow, add the scope to the default token scopes of the `OidcProviderOptions`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="7e990-128">Azure portal bir kapsam URI 'SI sağlıyorsa ve uygulama API 'den *401 Yetkisiz* bir yanıt aldığında **işlenmeyen bir özel durum oluşturursa** , düzeni ve Konağı içermeyen bir kapsam URI 'si kullanmayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="7e990-128">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="7e990-129">Örneğin, Azure portal aşağıdaki kapsam URI biçimlerinden birini verebilir:</span><span class="sxs-lookup"><span data-stu-id="7e990-129">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="7e990-130">Kapsam URI 'sini düzen ve ana bilgisayar olmadan sağlayın:</span><span class="sxs-lookup"><span data-stu-id="7e990-130">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="7e990-131">Daha fazla bilgi için bkz. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="7e990-131">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="7e990-132">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="7e990-132">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="7e990-133">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="7e990-133">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="7e990-134">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="7e990-134">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="7e990-135">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="7e990-135">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="7e990-136">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="7e990-136">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="7e990-137">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="7e990-137">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="7e990-138">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="7e990-138">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
 