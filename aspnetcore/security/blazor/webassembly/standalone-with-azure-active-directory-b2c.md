---
title: Azure Active Directory B2C bir ASP.NET Core Blazor webassembly tek başına uygulamasının güvenliğini sağlama
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 7d1031d3eac0e1d6790ca946809038127eb59a73
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111168"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="4664f-102">Azure Active Directory B2C bir ASP.NET Core Blazor webassembly tek başına uygulamasının güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="4664f-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="4664f-103">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="4664f-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="4664f-104">Bu makaledeki kılavuz, ASP.NET Core 3,2 Preview 4 için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="4664f-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="4664f-105">Bu konu, 24 Nisan, Cuma günü, Önizleme 5 ' i kapsayacak şekilde güncelleştirilecektir.</span><span class="sxs-lookup"><span data-stu-id="4664f-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="4664f-106">Kimlik doğrulaması için Blazor [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) kullanan bir webassembly tek başına uygulaması oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="4664f-106">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

1. <span data-ttu-id="4664f-107">Bir kiracı oluşturmak ve bir Web uygulamasını Azure portalında kaydetmek için aşağıdaki konulardaki yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="4664f-107">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

   * <span data-ttu-id="4664f-108">[AAD B2C kiracı](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; oluşturun aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="4664f-108">[Create an AAD B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; Record the following information:</span></span>

     <span data-ttu-id="4664f-109">1 \.</span><span class="sxs-lookup"><span data-stu-id="4664f-109">1\.</span></span> <span data-ttu-id="4664f-110">AAD B2C örneği (örneğin, `https://contoso.b2clogin.com/`sonunda eğik çizgi içeren)</span><span class="sxs-lookup"><span data-stu-id="4664f-110">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span><br>
     <span data-ttu-id="4664f-111">2 \.</span><span class="sxs-lookup"><span data-stu-id="4664f-111">2\.</span></span> <span data-ttu-id="4664f-112">AAD B2C kiracı etki alanı (örneğin, `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="4664f-112">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

   * <span data-ttu-id="4664f-113">[Bir Web uygulamasını](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Kaydet uygulama kaydı sırasında aşağıdaki seçimleri yapın:</span><span class="sxs-lookup"><span data-stu-id="4664f-113">[Register a web application](/azure/active-directory-b2c/tutorial-register-applications) &ndash; Make the following selections during app registration:</span></span>

     <span data-ttu-id="4664f-114">1 \.</span><span class="sxs-lookup"><span data-stu-id="4664f-114">1\.</span></span> <span data-ttu-id="4664f-115">**Web uygulaması/Web API 'Sini** **Evet**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4664f-115">Set **Web App / Web API** to **Yes**.</span></span><br>
     <span data-ttu-id="4664f-116">2 \.</span><span class="sxs-lookup"><span data-stu-id="4664f-116">2\.</span></span> <span data-ttu-id="4664f-117">**Örtük akışa Izin ver** ' i **Evet**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4664f-117">Set **Allow implicit flow** to **Yes**.</span></span><br>
     <span data-ttu-id="4664f-118">3 \.</span><span class="sxs-lookup"><span data-stu-id="4664f-118">3\.</span></span> <span data-ttu-id="4664f-119">**Yanıt URL 'si** ekleyin `https://localhost:5001/authentication/login-callback`.</span><span class="sxs-lookup"><span data-stu-id="4664f-119">Add a **Reply URL** of `https://localhost:5001/authentication/login-callback`.</span></span>

     <span data-ttu-id="4664f-120">Uygulama KIMLIĞINI (Istemci KIMLIĞI) kaydedin (örneğin, `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="4664f-120">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

   * <span data-ttu-id="4664f-121">[Kullanıcı akışları](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; oluşturma kaydolma ve oturum açma Kullanıcı akışı oluşturma.</span><span class="sxs-lookup"><span data-stu-id="4664f-121">[Create user flows](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; Create a sign-up and sign-in user flow.</span></span>

     <span data-ttu-id="4664f-122">En azından `context.User.Identity.Name` , `LoginDisplay` bileşende (*paylaşılan/logindisplay. Razor*) doldurmak için **uygulama talepleri** > **görünen adı** Kullanıcı özniteliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="4664f-122">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

     <span data-ttu-id="4664f-123">Uygulama için oluşturulan kaydolma ve oturum açma Kullanıcı akış adını kaydedin (örneğin, `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="4664f-123">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

1. <span data-ttu-id="4664f-124">Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:</span><span class="sxs-lookup"><span data-stu-id="4664f-124">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   <span data-ttu-id="4664f-125">Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="4664f-125">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="4664f-126">Klasör adı Ayrıca projenin adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="4664f-126">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="4664f-127">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="4664f-127">Authentication package</span></span>

<span data-ttu-id="4664f-128">Tek bir B2C hesabı (`IndividualB2C`) kullanmak üzere bir uygulama oluşturulduğunda, uygulama otomatik olarak [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`) için bir paket başvurusu alır.</span><span class="sxs-lookup"><span data-stu-id="4664f-128">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="4664f-129">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="4664f-129">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="4664f-130">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="4664f-130">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="4664f-131">Yukarıdaki `{VERSION}` paket başvurusunda, `Microsoft.AspNetCore.Blazor.Templates` <xref:blazor/get-started> makalede gösterilen paketin sürümüyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="4664f-131">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="4664f-132">`Microsoft.Authentication.WebAssembly.Msal` Paket geçişli olarak uygulamayı uygulamaya ekler `Microsoft.AspNetCore.Components.WebAssembly.Authentication` .</span><span class="sxs-lookup"><span data-stu-id="4664f-132">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="4664f-133">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="4664f-133">Authentication service support</span></span>

<span data-ttu-id="4664f-134">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal` paket tarafından sağlanmış uzantı yöntemiyle kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="4664f-134">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="4664f-135">Bu yöntem, uygulamanın kimlik sağlayıcısıyla (IP) etkileşim kurması için gereken tüm hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="4664f-135">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="4664f-136">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="4664f-136">*Program.cs*:</span></span>

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

<span data-ttu-id="4664f-137">Yöntemi `AddMsalAuthentication` , bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="4664f-137">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="4664f-138">Uygulamanın yapılandırılması için gereken değerler, uygulamayı kaydettiğinizde Azure Portal AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="4664f-138">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="4664f-139">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="4664f-139">Access token scopes</span></span>

<span data-ttu-id="4664f-140">Blazor Webassembly şablonu, uygulamayı GÜVENLI bir API için erişim belirteci isteyecek şekilde otomatik olarak yapılandırmaz.</span><span class="sxs-lookup"><span data-stu-id="4664f-140">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="4664f-141">Oturum açma akışının bir parçası olarak bir belirteç sağlamak için, kapsamı varsayılan erişim belirteci kapsamlarına ekleyin `MsalProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="4664f-141">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="4664f-142">Azure portal bir kapsam URI 'SI sağlıyorsa ve uygulama API 'den *401 Yetkisiz* bir yanıt aldığında **işlenmeyen bir özel durum oluşturursa** , düzeni ve Konağı içermeyen bir kapsam URI 'si kullanmayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="4664f-142">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="4664f-143">Örneğin, Azure portal aşağıdaki kapsam URI biçimlerinden birini verebilir:</span><span class="sxs-lookup"><span data-stu-id="4664f-143">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="4664f-144">Kapsam URI 'sini düzen ve ana bilgisayar olmadan sağlayın:</span><span class="sxs-lookup"><span data-stu-id="4664f-144">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="4664f-145">Daha fazla bilgi için bkz. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="4664f-145">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="4664f-146">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="4664f-146">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="4664f-147">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="4664f-147">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="4664f-148">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="4664f-148">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="4664f-149">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="4664f-149">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="4664f-150">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="4664f-150">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="4664f-151">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="4664f-151">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="4664f-152">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="4664f-152">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="4664f-153">Öğretici: Azure Active Directory B2C kiracısı oluşturma</span><span class="sxs-lookup"><span data-stu-id="4664f-153">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="4664f-154">Microsoft kimlik platformu belgeleri</span><span class="sxs-lookup"><span data-stu-id="4664f-154">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
