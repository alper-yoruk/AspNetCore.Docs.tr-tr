---
title: Azure Active Blazor Directory B2C ile ASP.NET Core WebAssembly bağımsız bir uygulamasını güvenli hale
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 0734bad2d4281eb856783a362ef8c608a303c17a
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977060"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="d1984-102">Azure Active Blazor Directory B2C ile ASP.NET Core WebAssembly bağımsız bir uygulamasını güvenli hale</span><span class="sxs-lookup"><span data-stu-id="d1984-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="d1984-103">Yazar: [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d1984-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="d1984-104">Kimlik doğrulaması için [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) kullanan bağımsız bir Blazor WebAssembly uygulaması oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="d1984-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="d1984-105">Bir kiracı oluşturmak ve Azure Portalı'nda bir web uygulaması kaydetmek için aşağıdaki konulardaki kılavuzu izleyin:</span><span class="sxs-lookup"><span data-stu-id="d1984-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="d1984-106">[AAD B2C kiracı](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; oluşturma Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="d1984-106">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="d1984-107">1\.</span><span class="sxs-lookup"><span data-stu-id="d1984-107">1\.</span></span> <span data-ttu-id="d1984-108">AAD B2C örneği (örneğin, `https://contoso.b2clogin.com/`sondaki eğik çizgiyi içerir)</span><span class="sxs-lookup"><span data-stu-id="d1984-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="d1984-109">2\.</span><span class="sxs-lookup"><span data-stu-id="d1984-109">2\.</span></span> <span data-ttu-id="d1984-110">AAD B2C Kiracı etki `contoso.onmicrosoft.com`alanı (örneğin, )</span><span class="sxs-lookup"><span data-stu-id="d1984-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="d1984-111">[Bir web uygulaması](/azure/active-directory-b2c/tutorial-register-applications) &ndash; kaydolun Uygulama kaydı sırasında aşağıdaki seçimleri yapın:</span><span class="sxs-lookup"><span data-stu-id="d1984-111">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="d1984-112">1\.</span><span class="sxs-lookup"><span data-stu-id="d1984-112">1\.</span></span> <span data-ttu-id="d1984-113">**Web App / Web API'yi** **Evet**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="d1984-113">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="d1984-114">2\.</span><span class="sxs-lookup"><span data-stu-id="d1984-114">2\.</span></span> <span data-ttu-id="d1984-115">Küme Örtülü akışı **Evet'e** **bırak.**</span><span class="sxs-lookup"><span data-stu-id="d1984-115">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="d1984-116">3\.</span><span class="sxs-lookup"><span data-stu-id="d1984-116">3\.</span></span> <span data-ttu-id="d1984-117">'nin Yanıt `https://localhost:5001/authentication/login-callback` **URL'si** ekle</span><span class="sxs-lookup"><span data-stu-id="d1984-117">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="d1984-118">Uygulama Kimliğini (İstemci Kimliği) `11111111-1111-1111-1111-111111111111`kaydetme (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="d1984-118">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="d1984-119">[Kullanıcı akışları](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; oluşturun Kaydolma ve kaydolma kullanıcı akışı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d1984-119">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="d1984-120">En azından, `context.User.Identity.Name` bileşendeki *(Shared/LoginDisplay.razor)* doldurmak `LoginDisplay` için Uygulama **talepleri** > Display**Name** kullanıcı özniteliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="d1984-120">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="d1984-121">Uygulama için oluşturulan kaydolma ve kaydolma kullanıcı akış adını `B2C_1_signupsignin`kaydedin (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="d1984-121">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="d1984-122">Aşağıdaki komuttaki yer tutucuları daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğunda uygulayın:</span><span class="sxs-lookup"><span data-stu-id="d1984-122">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="d1984-123">Yoksa proje klasörü oluşturan çıktı konumunu belirtmek için, çıkış seçeneğini bir yolu olan komuta ekleyin `-o BlazorSample`(örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="d1984-123">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="d1984-124">Klasör adı da projenin adının bir parçası olur.</span><span class="sxs-lookup"><span data-stu-id="d1984-124">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="d1984-125">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="d1984-125">Authentication package</span></span>

<span data-ttu-id="d1984-126">Bir uygulama Bireysel B2C Hesabı kullanmak`IndividualB2C`için oluşturulduğunda ( ), uygulama otomatik olarak Microsoft`Microsoft.Authentication.WebAssembly.Msal`Kimlik Doğrulama [Kitaplığı](/azure/active-directory/develop/msal-overview) için bir paket başvurusu alır ( ).</span><span class="sxs-lookup"><span data-stu-id="d1984-126">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="d1984-127">Paket, uygulamanın kullanıcıları doğrulamasına ve korumalı API'leri aramak için belirteçler almasına yardımcı olan bir dizi ilkel lik sağlar.</span><span class="sxs-lookup"><span data-stu-id="d1984-127">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="d1984-128">Bir uygulamaya kimlik doğrulama ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d1984-128">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="d1984-129">Önceki `{VERSION}` paket başvuruyu makalede gösterilen `Microsoft.AspNetCore.Blazor.Templates` paketin sürümüyle <xref:blazor/get-started> değiştirin.</span><span class="sxs-lookup"><span data-stu-id="d1984-129">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="d1984-130">Paket, `Microsoft.Authentication.WebAssembly.Msal` `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paketi geçici olarak uygulamaya ekler.</span><span class="sxs-lookup"><span data-stu-id="d1984-130">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="d1984-131">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="d1984-131">Authentication service support</span></span>

<span data-ttu-id="d1984-132">Kullanıcıların kimlik doğrulaması için destek, paket `AddMsalAuthentication` tarafından sağlanan uzantı yöntemiyle servis kapsayıcısında `Microsoft.Authentication.WebAssembly.Msal` kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="d1984-132">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="d1984-133">Bu yöntem, uygulamanın Kimlik Sağlayıcısı (IP) ile etkileşim kurması için gereken tüm hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d1984-133">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="d1984-134">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d1984-134">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
});
```

<span data-ttu-id="d1984-135">Yöntem, `AddMsalAuthentication` bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri arama kabul eder.</span><span class="sxs-lookup"><span data-stu-id="d1984-135">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="d1984-136">Uygulamayı yapılandırmak için gereken değerler, uygulamayı kaydettirdiğinizde Azure Portal AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="d1984-136">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="d1984-137">Belirteç kapsamlarına erişim</span><span class="sxs-lookup"><span data-stu-id="d1984-137">Access token scopes</span></span>

<span data-ttu-id="d1984-138">Blazor WebAssembly şablonu, güvenli bir API için erişim belirteci istemek üzere uygulamayı otomatik olarak yapılandırmaz.</span><span class="sxs-lookup"><span data-stu-id="d1984-138">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="d1984-139">Oturum açma akışının bir parçası olarak bir belirteç sağlamak için, kapsamı aşağıdakilerin varsayılan erişim belirteç kapsamına `MsalProviderOptions`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d1984-139">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="d1984-140">Azure portalı bir kapsam oluşturuyorsa URI ve uygulama API'den *401 Yetkisiz* yanıt aldığında **işlenmemiş bir özel durum atarsa,** düzeni ve ana bilgisayarı içermeyen bir KAPSAM URI kullanmayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="d1984-140">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="d1984-141">Örneğin, Azure portalı aşağıdaki kapsam URI biçimlerinden birini sağlayabilir:</span><span class="sxs-lookup"><span data-stu-id="d1984-141">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="d1984-142">Şema ve ana bilgisayar olmadan URI kapsamını tedarik edin:</span><span class="sxs-lookup"><span data-stu-id="d1984-142">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="d1984-143">Daha fazla bilgi için bkz. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="d1984-143">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="d1984-144">Dosyayı alma</span><span class="sxs-lookup"><span data-stu-id="d1984-144">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="d1984-145">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="d1984-145">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="d1984-146">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="d1984-146">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="d1984-147">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="d1984-147">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="d1984-148">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="d1984-148">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="d1984-149">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="d1984-149">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="d1984-150">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d1984-150">Additional resources</span></span>

* [<span data-ttu-id="d1984-151">Ek erişim belirteçleri isteme</span><span class="sxs-lookup"><span data-stu-id="d1984-151">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="d1984-152">Öğretici: Azure Active Directory B2C kiracısı oluşturma</span><span class="sxs-lookup"><span data-stu-id="d1984-152">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="d1984-153">Microsoft kimlik platformu belgeleri</span><span class="sxs-lookup"><span data-stu-id="d1984-153">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
