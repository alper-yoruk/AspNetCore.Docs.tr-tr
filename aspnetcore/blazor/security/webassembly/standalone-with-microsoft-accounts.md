---
title: Blazor WebAssemblyMicrosoft hesaplarıyla ASP.NET Core tek başına uygulamayı güvenli hale getirme
author: guardrex
description: Blazor WebAssemblyMicrosoft hesaplarıyla ASP.NET Core tek başına uygulamasının güvenliğini nasıl sağlayacağınızı öğrenin.
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
uid: blazor/security/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 268debd51b6828aad0bcfe917bdf95b691ac7365
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710533"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="807ee-103">Blazor WebAssemblyMicrosoft hesaplarıyla ASP.NET Core tek başına uygulamayı güvenli hale getirme</span><span class="sxs-lookup"><span data-stu-id="807ee-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="807ee-104">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="807ee-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="807ee-105">Bu makalede, kimlik doğrulaması için [Azure Active Directory (AAD) Ile Microsoft hesaplarını](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) kullanan [tek başına bir Blazor WebAssembly uygulamanın](xref:blazor/hosting-models#blazor-webassembly) nasıl oluşturulacağı ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="807ee-105">This article covers how to create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication.</span></span>

<span data-ttu-id="807ee-106">Azure Portal **Azure Active Directory**  >  **uygulama kayıtları** alanına bir AAD uygulaması kaydedin:</span><span class="sxs-lookup"><span data-stu-id="807ee-106">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="807ee-107">Uygulama için bir **ad** sağlayın (örneğin, **Blazor tek başına AAD Microsoft hesapları**).</span><span class="sxs-lookup"><span data-stu-id="807ee-107">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="807ee-108">**Desteklenen hesap türleri**' nde, **herhangi bir kuruluş dizininde hesaplar**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="807ee-108">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="807ee-109">**Yeniden yönlendirme URI 'si** açılan öğesini **tek SAYFALı uygulama (Spa)** olarak ayarlayın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="807ee-109">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="807ee-110">Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir.</span><span class="sxs-lookup"><span data-stu-id="807ee-110">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="807ee-111">Uygulama farklı bir Kestrel bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktasını kullanın.</span><span class="sxs-lookup"><span data-stu-id="807ee-111">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="807ee-112">IIS Express için, uygulama için rastgele oluşturulan bağlantı noktası, **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="807ee-112">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="807ee-113">Uygulama bu noktada mevcut olmadığından ve IIS Express bağlantı noktası bilinmediğinden, uygulama oluşturulduktan sonra bu adıma geri dönün ve yeniden yönlendirme URI 'sini güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="807ee-113">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="807ee-114">Bu konunun ilerleyen kısımlarında bir açıklama görüntülenerek IIS Express kullanıcıların yeniden yönlendirme URI 'sini güncelleştirmesini hatırlatır.</span><span class="sxs-lookup"><span data-stu-id="807ee-114">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="807ee-115"> > **OpenID ve offline_access izinleri için yönetici onayı verme** izinleri onay kutusunu temizleyin.</span><span class="sxs-lookup"><span data-stu-id="807ee-115">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="807ee-116">**Kaydet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="807ee-116">Select **Register**.</span></span>

<span data-ttu-id="807ee-117">Uygulama (istemci) KIMLIĞINI (örneğin, `41451fa7-82d9-4673-8fa5-69eff5a761fd` ) kaydedin.</span><span class="sxs-lookup"><span data-stu-id="807ee-117">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="807ee-118">**Kimlik doğrulama** > **platformu yapılandırmalarında** > **tek sayfalı uygulama (Spa)**:</span><span class="sxs-lookup"><span data-stu-id="807ee-118">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="807ee-119">**Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="807ee-119">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="807ee-120">**Örtük verme** Için, **erişim belirteçleri** ve **Kimlik belirteçleri** onay kutularının seçili **olmadığından** emin olun.</span><span class="sxs-lookup"><span data-stu-id="807ee-120">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="807ee-121">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="807ee-121">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="807ee-122">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="807ee-122">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="807ee-123">Uygulama için bir **ad** sağlayın (örneğin, **Blazor tek başına AAD Microsoft hesapları**).</span><span class="sxs-lookup"><span data-stu-id="807ee-123">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="807ee-124">**Desteklenen hesap türleri**' nde, **herhangi bir kuruluş dizininde hesaplar**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="807ee-124">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="807ee-125">**Yeniden yönlendirme URI 'si** açılan öğesini **Web** 'e ayarlı bırakın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="807ee-125">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="807ee-126">Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir.</span><span class="sxs-lookup"><span data-stu-id="807ee-126">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="807ee-127">Uygulama farklı bir Kestrel bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktasını kullanın.</span><span class="sxs-lookup"><span data-stu-id="807ee-127">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="807ee-128">IIS Express için, uygulama için rastgele oluşturulan bağlantı noktası, **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="807ee-128">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="807ee-129">Uygulama bu noktada mevcut olmadığından ve IIS Express bağlantı noktası bilinmediğinden, uygulama oluşturulduktan sonra bu adıma geri dönün ve yeniden yönlendirme URI 'sini güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="807ee-129">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="807ee-130">Bu konunun ilerleyen kısımlarında bir açıklama görüntülenerek IIS Express kullanıcıların yeniden yönlendirme URI 'sini güncelleştirmesini hatırlatır.</span><span class="sxs-lookup"><span data-stu-id="807ee-130">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="807ee-131"> > **OpenID ve offline_access izinleri için yönetici onayı verme** izinleri onay kutusunu temizleyin.</span><span class="sxs-lookup"><span data-stu-id="807ee-131">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="807ee-132">**Kaydet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="807ee-132">Select **Register**.</span></span>

<span data-ttu-id="807ee-133">Uygulama (istemci) KIMLIĞINI (örneğin, `41451fa7-82d9-4673-8fa5-69eff5a761fd` ) kaydedin.</span><span class="sxs-lookup"><span data-stu-id="807ee-133">Record the Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`).</span></span>

<span data-ttu-id="807ee-134">**Kimlik doğrulama** > **platformu yapılandırması** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="807ee-134">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="807ee-135">**Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="807ee-135">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="807ee-136">**Örtük izin** Için, **erişim belirteçleri** ve **Kimlik belirteçleri** onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="807ee-136">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="807ee-137">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="807ee-137">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="807ee-138">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="807ee-138">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="807ee-139">Uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="807ee-139">Create the app.</span></span> <span data-ttu-id="807ee-140">Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komut kabuğu 'nda aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="807ee-140">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common" -o {APP NAME}
```

| <span data-ttu-id="807ee-141">Yer tutucu</span><span class="sxs-lookup"><span data-stu-id="807ee-141">Placeholder</span></span>   | <span data-ttu-id="807ee-142">Azure portal adı</span><span class="sxs-lookup"><span data-stu-id="807ee-142">Azure portal name</span></span>       | <span data-ttu-id="807ee-143">Örnek</span><span class="sxs-lookup"><span data-stu-id="807ee-143">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="807ee-144">Uygulama (istemci) kimliği</span><span class="sxs-lookup"><span data-stu-id="807ee-144">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |

<span data-ttu-id="807ee-145">Seçeneğiyle belirtilen çıktı konumu, `-o|--output` mevcut değilse bir proje klasörü oluşturur ve uygulamanın adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="807ee-145">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="807ee-146">Azure portal, uygulamanın platform yapılandırması **yeniden yönlendirme URI 'si** , Kestrel sunucusunda varsayılan ayarlarla çalışan uygulamalar için bağlantı noktası 5001 için yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="807ee-146">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="807ee-147">Uygulama rastgele bir IIS Express bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktası **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="807ee-147">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="807ee-148">Bağlantı noktası, uygulamanın bilinen bağlantı noktasıyla daha önce yapılandırılmamışsa, Azure portal uygulamanın kaydına dönün ve yeniden yönlendirme URI 'sini doğru bağlantı noktasıyla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="807ee-148">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-nonAAD.md)]

::: moniker-end

<span data-ttu-id="807ee-149">Uygulamayı oluşturduktan sonra şunları yapmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="807ee-149">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="807ee-150">Microsoft hesabı kullanarak uygulamada oturum açın.</span><span class="sxs-lookup"><span data-stu-id="807ee-150">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="807ee-151">Microsoft API 'Leri için erişim belirteçleri isteyin.</span><span class="sxs-lookup"><span data-stu-id="807ee-151">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="807ee-152">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="807ee-152">For more information, see:</span></span>
  * [<span data-ttu-id="807ee-153">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="807ee-153">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="807ee-154">[Hızlı başlangıç: Web API 'lerini kullanıma sunmak için bir uygulama yapılandırma](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="807ee-154">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="807ee-155">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="807ee-155">Authentication package</span></span>

<span data-ttu-id="807ee-156">Iş veya okul hesaplarını () kullanmak üzere bir uygulama oluşturulduğunda `SingleOrg` , uygulama [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) () için bir paket başvurusu otomatik olarak alır [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) .</span><span class="sxs-lookup"><span data-stu-id="807ee-156">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="807ee-157">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="807ee-157">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="807ee-158">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="807ee-158">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="807ee-159">Yer tutucu için `{VERSION}` , uygulamanın paylaşılan Framework sürümüyle eşleşen en son kararlı sürümü paketin [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)adresindeki **sürüm geçmişinde** bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="807ee-159">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="807ee-160">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)Paket geçişli [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) olarak uygulamayı uygulamaya ekler.</span><span class="sxs-lookup"><span data-stu-id="807ee-160">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="807ee-161">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="807ee-161">Authentication service support</span></span>

<span data-ttu-id="807ee-162">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> paket tarafından sağlanmış uzantı yöntemiyle kaydedilir [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) .</span><span class="sxs-lookup"><span data-stu-id="807ee-162">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="807ee-163">Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken tüm hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="807ee-163">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="807ee-164">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="807ee-164">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="807ee-165"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Yöntemi, bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="807ee-165">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="807ee-166">Uygulamayı yapılandırmak için gereken değerler, uygulamayı kaydettiğinizde AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="807ee-166">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="807ee-167">Yapılandırma dosya tarafından sağlanır `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="807ee-167">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="807ee-168">Örnek:</span><span class="sxs-lookup"><span data-stu-id="807ee-168">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="807ee-169">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="807ee-169">Access token scopes</span></span>

<span data-ttu-id="807ee-170">Blazor WebAssemblyŞablon, uygulamayı güvenli BIR API için erişim belirteci isteyecek şekilde otomatik olarak yapılandırmaz.</span><span class="sxs-lookup"><span data-stu-id="807ee-170">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="807ee-171">Oturum açma akışının bir parçası olarak bir erişim belirteci sağlamak için, kapsamı varsayılan erişim belirteci kapsamlarına ekleyin <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="807ee-171">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="807ee-172">İle ek kapsamlar belirtin `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="807ee-172">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="807ee-173">Daha fazla bilgi için *ek senaryolar* makalesinin aşağıdaki bölümlerine bakın:</span><span class="sxs-lookup"><span data-stu-id="807ee-173">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="807ee-174">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="807ee-174">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="807ee-175">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="807ee-175">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="807ee-176">Oturum açma modu</span><span class="sxs-lookup"><span data-stu-id="807ee-176">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="807ee-177">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="807ee-177">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="807ee-178">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="807ee-178">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="807ee-179">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="807ee-179">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="807ee-180">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="807ee-180">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="807ee-181">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="807ee-181">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="807ee-182">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="807ee-182">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="807ee-183">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="807ee-183">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="807ee-184">Authentication. MSAL JavaScript kitaplığının özel bir sürümünü oluşturun</span><span class="sxs-lookup"><span data-stu-id="807ee-184">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [<span data-ttu-id="807ee-185">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="807ee-185">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* [<span data-ttu-id="807ee-186">Hızlı başlangıç: Microsoft Identity platformu ile uygulama kaydetme</span><span class="sxs-lookup"><span data-stu-id="807ee-186">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="807ee-187">Hızlı başlangıç: Web API 'Lerini kullanıma sunmak için uygulama yapılandırma</span><span class="sxs-lookup"><span data-stu-id="807ee-187">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
