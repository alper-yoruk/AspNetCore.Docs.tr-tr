---
title: Azure Active Directory B2C bir ASP.NET Core Blazor webassembly tek başına uygulamasının güvenliğini sağlama
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
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: ec35614e3bc4b5b6422b254dfe579c1cb7ca8310
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724399"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="02d0a-102">Azure Active Directory B2C bir ASP.NET Core Blazor webassembly tek başına uygulamasının güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="02d0a-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="02d0a-103">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="02d0a-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="02d0a-104">BlazorKimlik doğrulaması için [Azure ACTIVE DIRECTORY (AAD) B2C](/azure/active-directory-b2c/overview) kullanan bir webassembly tek başına uygulaması oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="02d0a-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

<span data-ttu-id="02d0a-105">Bir kiracı oluşturmak ve bir Web uygulamasını Azure portalında kaydetmek için aşağıdaki konulardaki yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="02d0a-105">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

[<span data-ttu-id="02d0a-106">AAD B2C kiracı oluşturma</span><span class="sxs-lookup"><span data-stu-id="02d0a-106">Create an AAD B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)

<span data-ttu-id="02d0a-107">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="02d0a-107">Record the following information:</span></span>

* <span data-ttu-id="02d0a-108">AAD B2C örneği (örneğin, `https://contoso.b2clogin.com/` sonunda eğik çizgi içeren).</span><span class="sxs-lookup"><span data-stu-id="02d0a-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span>
* <span data-ttu-id="02d0a-109">AAD B2C kiracı etki alanı (örneğin, `contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="02d0a-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="02d0a-110">Eğitim bölümündeki yönergeleri izleyin: *istemci uygulaması* için AAD uygulamasını kaydetmek üzere [Azure Active Directory B2C bir uygulamayı yeniden kaydedin](/azure/active-directory-b2c/tutorial-register-applications) ve ardından aşağıdakileri yapın:</span><span class="sxs-lookup"><span data-stu-id="02d0a-110">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="02d0a-111">**Azure Active Directory**  >  **uygulama kayıtları** **Yeni kayıt**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="02d0a-111">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="02d0a-112">Uygulama için bir **ad** sağlayın (örneğin, \*\* Blazor tek başına AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="02d0a-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="02d0a-113">**Desteklenen hesap türleri**için, birden çok kiracılı seçeneği seçin: **herhangi bir kuruluş dizinindeki hesaplar veya herhangi bir kimlik sağlayıcısı. Azure AD B2C kullanıcıları kimlik doğrulaması için.**</span><span class="sxs-lookup"><span data-stu-id="02d0a-113">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="02d0a-114">**Yeniden yönlendirme URI 'si** açılan öğesini **Web** 'e ayarlı bırakın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="02d0a-114">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="02d0a-115">Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir.</span><span class="sxs-lookup"><span data-stu-id="02d0a-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="02d0a-116">Uygulama farklı bir Kestrel bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktasını kullanın.</span><span class="sxs-lookup"><span data-stu-id="02d0a-116">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="02d0a-117">IIS Express için, uygulama için rastgele oluşturulan bağlantı noktası, **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="02d0a-117">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="02d0a-118">Uygulama bu noktada mevcut olmadığından ve IIS Express bağlantı noktası bilinmediğinden, uygulama oluşturulduktan sonra bu adıma geri dönün ve yeniden yönlendirme URI 'sini güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="02d0a-118">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="02d0a-119">Bu konunun ilerleyen kısımlarında bir açıklama görüntülenerek IIS Express kullanıcıların yeniden yönlendirme URI 'sini güncelleştirmesini hatırlatır.</span><span class="sxs-lookup"><span data-stu-id="02d0a-119">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="02d0a-120">**İzinlerin**  >  **OpenID 'ye yönetici onayı verdiğini ve offline_access izinlerinin** etkin olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="02d0a-120">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="02d0a-121">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="02d0a-121">Select **Register**.</span></span>

<span data-ttu-id="02d0a-122">Uygulama KIMLIĞINI (Istemci KIMLIĞI) kaydedin (örneğin, `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="02d0a-122">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="02d0a-123">**Kimlik doğrulama**  >  **platformu yapılandırması**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="02d0a-123">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="02d0a-124">**Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="02d0a-124">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="02d0a-125">**Örtük izin**Için, **erişim belirteçleri** ve **Kimlik belirteçleri**onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="02d0a-125">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="02d0a-126">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="02d0a-126">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="02d0a-127">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="02d0a-127">Select the **Save** button.</span></span>

<span data-ttu-id="02d0a-128">**Giriş**  >  **Azure AD B2C**  >  **Kullanıcı akışları**' nda:</span><span class="sxs-lookup"><span data-stu-id="02d0a-128">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="02d0a-129">Kaydolma ve oturum açma Kullanıcı akışı oluşturma</span><span class="sxs-lookup"><span data-stu-id="02d0a-129">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="02d0a-130">En azından, **Application claims**  >  **Display Name** `context.User.Identity.Name` `LoginDisplay` bileşende (*paylaşılan/logindisplay. Razor*) doldurmak için uygulama talepleri görünen adı Kullanıcı özniteliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="02d0a-130">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="02d0a-131">Uygulama için oluşturulan kaydolma ve oturum açma Kullanıcı akış adını kaydedin (örneğin, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="02d0a-131">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="02d0a-132">Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:</span><span class="sxs-lookup"><span data-stu-id="02d0a-132">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
```

<span data-ttu-id="02d0a-133">Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="02d0a-133">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="02d0a-134">Klasör adı Ayrıca projenin adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="02d0a-134">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="02d0a-135">Azure Portal, uygulamanın **kimlik doğrulama**  >  **platformu yapılandırması**  >  **Web**  >  **yeniden yönlendirme URI 'si** , Kestrel sunucusunda varsayılan ayarlarla çalışan uygulamalar için bağlantı noktası 5001 için yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="02d0a-135">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="02d0a-136">Uygulama rastgele bir IIS Express bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktası **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="02d0a-136">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="02d0a-137">Bağlantı noktası, uygulamanın bilinen bağlantı noktasıyla daha önce yapılandırılmamışsa, Azure portal uygulamanın kaydına dönün ve yeniden yönlendirme URI 'sini doğru bağlantı noktasıyla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="02d0a-137">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="02d0a-138">Uygulamayı oluşturduktan sonra şunları yapmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="02d0a-138">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="02d0a-139">AAD Kullanıcı hesabını kullanarak uygulamada oturum açın.</span><span class="sxs-lookup"><span data-stu-id="02d0a-139">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="02d0a-140">Microsoft API 'Leri için erişim belirteçleri isteyin.</span><span class="sxs-lookup"><span data-stu-id="02d0a-140">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="02d0a-141">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="02d0a-141">For more information, see:</span></span>
  * [<span data-ttu-id="02d0a-142">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="02d0a-142">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="02d0a-143">[Hızlı başlangıç: Web API 'lerini kullanıma sunmak için bir uygulama yapılandırma](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="02d0a-143">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="02d0a-144">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="02d0a-144">Authentication package</span></span>

<span data-ttu-id="02d0a-145">Tek bir B2C hesabı () kullanmak üzere bir uygulama oluşturulduğunda `IndividualB2C` , uygulama [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) için otomatik olarak bir paket başvurusu alır ([Microsoft. Authentication. Webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="02d0a-145">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="02d0a-146">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="02d0a-146">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="02d0a-147">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="02d0a-147">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="02d0a-148">[Microsoft. Authentication. WebAssembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) paketi, [Microsoft. Aspnetcore. components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) paketini uygulamaya göre geçişli olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="02d0a-148">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="02d0a-149">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="02d0a-149">Authentication service support</span></span>

<span data-ttu-id="02d0a-150">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısına <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> [Microsoft. Authentication. WebAssembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) paketi tarafından sağlanmış uzantı yöntemiyle kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="02d0a-150">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="02d0a-151">Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken tüm hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="02d0a-151">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="02d0a-152">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="02d0a-152">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="02d0a-153"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Yöntemi, bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="02d0a-153">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="02d0a-154">Uygulamayı yapılandırmak için gereken değerler, uygulamayı kaydettiğinizde AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="02d0a-154">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="02d0a-155">Yapılandırma, dosya *üzerinde Wwwroot/appsettings.js* tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="02d0a-155">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="02d0a-156">Örnek:</span><span class="sxs-lookup"><span data-stu-id="02d0a-156">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="02d0a-157">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="02d0a-157">Access token scopes</span></span>

<span data-ttu-id="02d0a-158">BlazorWebassembly şablonu, uygulamayı güvenli BIR API için erişim belirteci isteyecek şekilde otomatik olarak yapılandırmaz.</span><span class="sxs-lookup"><span data-stu-id="02d0a-158">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="02d0a-159">Oturum açma akışının bir parçası olarak bir erişim belirteci sağlamak için, kapsamı varsayılan erişim belirteci kapsamlarına ekleyin <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="02d0a-159">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="02d0a-160">Daha fazla bilgi için *ek senaryolar* makalesinin aşağıdaki bölümlerine bakın:</span><span class="sxs-lookup"><span data-stu-id="02d0a-160">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="02d0a-161">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="02d0a-161">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="02d0a-162">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="02d0a-162">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="02d0a-163">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="02d0a-163">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="02d0a-164">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="02d0a-164">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="02d0a-165">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="02d0a-165">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="02d0a-166">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="02d0a-166">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="02d0a-167">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="02d0a-167">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="02d0a-168">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="02d0a-168">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="02d0a-169">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="02d0a-169">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="02d0a-170">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="02d0a-170">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="02d0a-171">Öğretici: Azure Active Directory B2C kiracısı oluşturma</span><span class="sxs-lookup"><span data-stu-id="02d0a-171">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="02d0a-172">Microsoft kimlik platformu belgeleri</span><span class="sxs-lookup"><span data-stu-id="02d0a-172">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
