---
title: Azure Active Directory bir ASP.NET Core Blazor webassembly tek başına uygulamasının güvenliğini sağlama
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 4b03468d04c96ce90b84ab3c86709f266df187a7
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103942"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="a6cc4-102">Azure Active Directory bir ASP.NET Core Blazor webassembly tek başına uygulamasının güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="a6cc4-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="a6cc4-103">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="a6cc4-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="a6cc4-104">BlazorKimlik doğrulaması için [Azure ACTIVE DIRECTORY (AAD)](https://azure.microsoft.com/services/active-directory/) kullanan bir webassembly tek başına uygulaması oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="a6cc4-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="a6cc4-105">[AAD kiracısı ve Web uygulaması oluşturma](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="a6cc4-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="a6cc4-106">Azure Portal **Azure Active Directory**  >  **uygulama kayıtları** alanına bir AAD uygulaması kaydedin:</span><span class="sxs-lookup"><span data-stu-id="a6cc4-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="a6cc4-107">Uygulama için bir **ad** sağlayın (örneğin, \*\* Blazor tek başına AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="a6cc4-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="a6cc4-108">Desteklenen bir **Hesap türü**seçin.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="a6cc4-109">Bu **kuruluş dizininde yalnızca** bu deneyim için hesaplar seçebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="a6cc4-110">**Yeniden yönlendirme URI 'si** açılan öğesini **Web** 'e ayarlı bırakın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="a6cc4-110">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="a6cc4-111">Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-111">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="a6cc4-112">Uygulama farklı bir Kestrel bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktasını kullanın.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-112">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="a6cc4-113">IIS Express için, uygulama için rastgele oluşturulan bağlantı noktası, **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-113">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="a6cc4-114">Uygulama bu noktada mevcut olmadığından ve IIS Express bağlantı noktası bilinmediğinden, uygulama oluşturulduktan sonra bu adıma geri dönün ve yeniden yönlendirme URI 'sini güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-114">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="a6cc4-115">Bu konunun ilerleyen kısımlarında bir açıklama görüntülenerek IIS Express kullanıcıların yeniden yönlendirme URI 'sini güncelleştirmesini hatırlatır.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-115">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="a6cc4-116">**Permissions**  >  **OpenID ve offline_access izinleri için yönetici onayı izni ver** onay kutusunu devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-116">Disable the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="a6cc4-117">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-117">Select **Register**.</span></span>

<span data-ttu-id="a6cc4-118">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="a6cc4-118">Record the following information:</span></span>

* <span data-ttu-id="a6cc4-119">Uygulama KIMLIĞI (Istemci KIMLIĞI) (örneğin, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="a6cc4-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="a6cc4-120">Dizin KIMLIĞI (kiracı KIMLIĞI) (örneğin, `22222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="a6cc4-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="a6cc4-121">**Kimlik doğrulama**  >  **platformu yapılandırması**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="a6cc4-121">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="a6cc4-122">**Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-122">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a6cc4-123">**Örtük izin**Için, **erişim belirteçleri** ve **Kimlik belirteçleri**onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-123">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="a6cc4-124">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-124">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a6cc4-125">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-125">Select the **Save** button.</span></span>

<span data-ttu-id="a6cc4-126">Uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-126">Create the app.</span></span> <span data-ttu-id="a6cc4-127">Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:</span><span class="sxs-lookup"><span data-stu-id="a6cc4-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="a6cc4-128">Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="a6cc4-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="a6cc4-129">Klasör adı Ayrıca projenin adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-129">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="a6cc4-130">Azure Portal, uygulamanın **kimlik doğrulama**  >  **platformu yapılandırması**  >  **Web**  >  **yeniden yönlendirme URI 'si** , Kestrel sunucusunda varsayılan ayarlarla çalışan uygulamalar için bağlantı noktası 5001 için yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-130">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="a6cc4-131">Uygulama rastgele bir IIS Express bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktası **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-131">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="a6cc4-132">Bağlantı noktası, uygulamanın bilinen bağlantı noktasıyla daha önce yapılandırılmamışsa, Azure portal uygulamanın kaydına dönün ve yeniden yönlendirme URI 'sini doğru bağlantı noktasıyla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-132">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="a6cc4-133">Uygulamayı oluşturduktan sonra şunları yapmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="a6cc4-133">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="a6cc4-134">AAD Kullanıcı hesabını kullanarak uygulamada oturum açın.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-134">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="a6cc4-135">Microsoft API 'Leri için erişim belirteçleri isteyin.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-135">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="a6cc4-136">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-136">For more information, see:</span></span>
  * [<span data-ttu-id="a6cc4-137">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="a6cc4-137">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="a6cc4-138">[Hızlı başlangıç: Web API 'lerini kullanıma sunmak için bir uygulama yapılandırma](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="a6cc4-138">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="a6cc4-139">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="a6cc4-139">Authentication package</span></span>

<span data-ttu-id="a6cc4-140">Iş veya okul hesaplarını () kullanmak üzere bir uygulama oluşturulduğunda `SingleOrg` , uygulama [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) için otomatik olarak bir paket başvurusu alır ([Microsoft. Authentication. Webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="a6cc4-140">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="a6cc4-141">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-141">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a6cc4-142">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="a6cc4-142">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="a6cc4-143">[Microsoft. Authentication. WebAssembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) paketi, [Microsoft. Aspnetcore. components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) paketini uygulamaya göre geçişli olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-143">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="a6cc4-144">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="a6cc4-144">Authentication service support</span></span>

<span data-ttu-id="a6cc4-145">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısına <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> [Microsoft. Authentication. WebAssembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) paketi tarafından sağlanmış uzantı yöntemiyle kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-145">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="a6cc4-146">Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-146">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="a6cc4-147">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a6cc4-147">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="a6cc4-148"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Yöntemi, bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-148">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="a6cc4-149">Uygulamayı yapılandırmak için gereken değerler, uygulamayı kaydettiğinizde AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-149">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="a6cc4-150">Yapılandırma, dosya *üzerinde Wwwroot/appsettings.js* tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="a6cc4-150">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="a6cc4-151">Örnek:</span><span class="sxs-lookup"><span data-stu-id="a6cc4-151">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="a6cc4-152">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="a6cc4-152">Access token scopes</span></span>

<span data-ttu-id="a6cc4-153">BlazorWebassembly şablonu, uygulamayı güvenli BIR API için erişim belirteci isteyecek şekilde otomatik olarak yapılandırmaz.</span><span class="sxs-lookup"><span data-stu-id="a6cc4-153">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="a6cc4-154">Oturum açma akışının bir parçası olarak bir erişim belirteci sağlamak için, kapsamı varsayılan erişim belirteci kapsamlarına ekleyin <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="a6cc4-154">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="a6cc4-155">Daha fazla bilgi için *ek senaryolar* makalesinin aşağıdaki bölümlerine bakın:</span><span class="sxs-lookup"><span data-stu-id="a6cc4-155">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="a6cc4-156">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="a6cc4-156">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="a6cc4-157">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="a6cc4-157">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="a6cc4-158">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="a6cc4-158">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="a6cc4-159">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="a6cc4-159">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="a6cc4-160">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="a6cc4-160">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="a6cc4-161">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="a6cc4-161">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="a6cc4-162">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="a6cc4-162">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="a6cc4-163">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="a6cc4-163">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a6cc4-164">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="a6cc4-164">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="a6cc4-165">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="a6cc4-165">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="a6cc4-166">Microsoft kimlik platformu belgeleri</span><span class="sxs-lookup"><span data-stu-id="a6cc4-166">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
