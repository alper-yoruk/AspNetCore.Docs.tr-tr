---
title: Microsoft Hesapları Blazor ile ASP.NET Core WebAssembly bağımsız bir uygulama güvenli
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 8c409651b3338c2baeae497bef43b994823a20f9
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977086"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="e991f-102">Microsoft Hesapları Blazor ile ASP.NET Core WebAssembly bağımsız bir uygulama güvenli</span><span class="sxs-lookup"><span data-stu-id="e991f-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="e991f-103">Yazar: [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="e991f-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="e991f-104">Kimlik doğrulama Blazor için Azure Etkin [Dizinli (AAD) microsoft hesaplarını](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) kullanan bağımsız bir WebAssembly uygulaması oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="e991f-104">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="e991f-105">AAD kiracısı ve web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="e991f-105">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="e991f-106">Azure portalının Azure **Etkin Dizin** > **Uygulaması kayıtları** alanında bir AAD uygulaması kaydolun:</span><span class="sxs-lookup"><span data-stu-id="e991f-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="e991f-107">1\.</span><span class="sxs-lookup"><span data-stu-id="e991f-107">1\.</span></span> <span data-ttu-id="e991f-108">Uygulama için bir **Ad** sağlayın (örneğin, \*\* Blazor İstemci AAD).\*\*</span><span class="sxs-lookup"><span data-stu-id="e991f-108">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="e991f-109">2\.</span><span class="sxs-lookup"><span data-stu-id="e991f-109">2\.</span></span> <span data-ttu-id="e991f-110">Desteklenen hesap **türlerinde, herhangi bir kuruluş dizinindeki Hesapları**seçin. **Supported account types**</span><span class="sxs-lookup"><span data-stu-id="e991f-110">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="e991f-111">3\.</span><span class="sxs-lookup"><span data-stu-id="e991f-111">3\.</span></span> <span data-ttu-id="e991f-112">**Redirect URI** damlasını **Web'e**bırak ve uri'yi yeniden yönlendirmeyi `https://localhost:5001/authentication/login-callback`sağlar.</span><span class="sxs-lookup"><span data-stu-id="e991f-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="e991f-113">4\.</span><span class="sxs-lookup"><span data-stu-id="e991f-113">4\.</span></span> <span data-ttu-id="e991f-114">**İzinleri** > devre dışı**kaldırın, Yönetici yi openid ve offline_access izinleri** onay kutusunu açmak için yoğunlaşın.</span><span class="sxs-lookup"><span data-stu-id="e991f-114">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="e991f-115">5\.</span><span class="sxs-lookup"><span data-stu-id="e991f-115">5\.</span></span> <span data-ttu-id="e991f-116">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="e991f-116">Select **Register**.</span></span>

   <span data-ttu-id="e991f-117">**Kimlik** > Doğrulama**Platformu yapılandırmalarında** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="e991f-117">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="e991f-118">1\.</span><span class="sxs-lookup"><span data-stu-id="e991f-118">1\.</span></span> <span data-ttu-id="e991f-119">**Redirect** URI'nin `https://localhost:5001/authentication/login-callback` mevcut olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="e991f-119">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="e991f-120">2\.</span><span class="sxs-lookup"><span data-stu-id="e991f-120">2\.</span></span> <span data-ttu-id="e991f-121">**Örtülü hibe**için, Erişim **belirteçleri** ve **kimlik belirteçleri**için onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="e991f-121">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="e991f-122">3\.</span><span class="sxs-lookup"><span data-stu-id="e991f-122">3\.</span></span> <span data-ttu-id="e991f-123">Uygulama için kalan varsayılanlar bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="e991f-123">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="e991f-124">4\.</span><span class="sxs-lookup"><span data-stu-id="e991f-124">4\.</span></span> <span data-ttu-id="e991f-125">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="e991f-125">Select the **Save** button.</span></span>

   <span data-ttu-id="e991f-126">Uygulama Kimliğini (İstemci Kimliği) `11111111-1111-1111-1111-111111111111`kaydetme (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="e991f-126">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="e991f-127">Aşağıdaki komuttaki yer tutucuları daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğunda uygulayın:</span><span class="sxs-lookup"><span data-stu-id="e991f-127">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="e991f-128">Yoksa proje klasörü oluşturan çıktı konumunu belirtmek için, çıkış seçeneğini bir yolu olan komuta ekleyin `-o BlazorSample`(örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="e991f-128">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="e991f-129">Klasör adı da projenin adının bir parçası olur.</span><span class="sxs-lookup"><span data-stu-id="e991f-129">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="e991f-130">Uygulamayı oluşturduktan sonra şunları yapabilmeniz gerekir:</span><span class="sxs-lookup"><span data-stu-id="e991f-130">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="e991f-131">Bir Microsoft Hesabı kullanarak uygulamaya giriş yapın.</span><span class="sxs-lookup"><span data-stu-id="e991f-131">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="e991f-132">Uygulamayı doğru şekilde yapılandırmanız koşuluyla, bağımsız Blazor uygulamalarla aynı yaklaşımı kullanarak Microsoft API'leri için erişim belirteçleri isteyin.</span><span class="sxs-lookup"><span data-stu-id="e991f-132">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="e991f-133">Daha fazla bilgi için [Bkz. Quickstart: Web API'lerini ortaya çıkarmak için bir uygulamayı yapılandırın.](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)</span><span class="sxs-lookup"><span data-stu-id="e991f-133">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="e991f-134">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="e991f-134">Authentication package</span></span>

<span data-ttu-id="e991f-135">İş veya Okul Hesaplarını kullanmak için`SingleOrg`bir uygulama oluşturulduğunda ( ), uygulama otomatik olarak`Microsoft.Authentication.WebAssembly.Msal`Microsoft Kimlik Doğrulama [Kitaplığı](/azure/active-directory/develop/msal-overview) için bir paket başvurusu alır ( ).</span><span class="sxs-lookup"><span data-stu-id="e991f-135">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="e991f-136">Paket, uygulamanın kullanıcıları doğrulamasına ve korumalı API'leri aramak için belirteçler almasına yardımcı olan bir dizi ilkel lik sağlar.</span><span class="sxs-lookup"><span data-stu-id="e991f-136">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="e991f-137">Bir uygulamaya kimlik doğrulama ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e991f-137">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="e991f-138">Önceki `{VERSION}` paket başvuruyu makalede gösterilen `Microsoft.AspNetCore.Blazor.Templates` paketin sürümüyle <xref:blazor/get-started> değiştirin.</span><span class="sxs-lookup"><span data-stu-id="e991f-138">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="e991f-139">Paket, `Microsoft.Authentication.WebAssembly.Msal` `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paketi geçici olarak uygulamaya ekler.</span><span class="sxs-lookup"><span data-stu-id="e991f-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="e991f-140">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="e991f-140">Authentication service support</span></span>

<span data-ttu-id="e991f-141">Kullanıcıların kimlik doğrulaması için destek, paket `AddMsalAuthentication` tarafından sağlanan uzantı yöntemiyle servis kapsayıcısında `Microsoft.Authentication.WebAssembly.Msal` kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="e991f-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="e991f-142">Bu yöntem, uygulamanın Kimlik Sağlayıcısı (IP) ile etkileşim kurması için gereken tüm hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="e991f-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="e991f-143">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e991f-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="e991f-144">Yöntem, `AddMsalAuthentication` bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri arama kabul eder.</span><span class="sxs-lookup"><span data-stu-id="e991f-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="e991f-145">Uygulamayı yapılandırmak için gereken değerler, uygulamayı kaydettirdiğinizde Microsoft Hesapları yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="e991f-145">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="e991f-146">Belirteç kapsamlarına erişim</span><span class="sxs-lookup"><span data-stu-id="e991f-146">Access token scopes</span></span>

<span data-ttu-id="e991f-147">Blazor WebAssembly şablonu, güvenli bir API için erişim belirteci istemek üzere uygulamayı otomatik olarak yapılandırmaz.</span><span class="sxs-lookup"><span data-stu-id="e991f-147">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="e991f-148">Oturum açma akışının bir parçası olarak bir belirteç sağlamak için, kapsamı aşağıdakilerin varsayılan erişim belirteç kapsamına `MsalProviderOptions`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e991f-148">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="e991f-149">Azure portalı bir kapsam oluşturuyorsa URI ve uygulama API'den *401 Yetkisiz* yanıt aldığında **işlenmemiş bir özel durum atarsa,** düzeni ve ana bilgisayarı içermeyen bir KAPSAM URI kullanmayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="e991f-149">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="e991f-150">Örneğin, Azure portalı aşağıdaki kapsam URI biçimlerinden birini sağlayabilir:</span><span class="sxs-lookup"><span data-stu-id="e991f-150">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="e991f-151">Şema ve ana bilgisayar olmadan URI kapsamını tedarik edin:</span><span class="sxs-lookup"><span data-stu-id="e991f-151">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="e991f-152">Daha fazla bilgi için bkz. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="e991f-152">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="e991f-153">Dosyayı alma</span><span class="sxs-lookup"><span data-stu-id="e991f-153">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="e991f-154">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="e991f-154">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="e991f-155">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="e991f-155">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="e991f-156">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="e991f-156">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="e991f-157">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="e991f-157">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="e991f-158">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="e991f-158">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="e991f-159">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e991f-159">Additional resources</span></span>

* [<span data-ttu-id="e991f-160">Ek erişim belirteçleri isteme</span><span class="sxs-lookup"><span data-stu-id="e991f-160">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="e991f-161">Quickstart: Microsoft kimlik platformuile bir uygulama kaydetme</span><span class="sxs-lookup"><span data-stu-id="e991f-161">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="e991f-162">Quickstart: Web API'lerini ortaya çıkarmak için bir uygulamayı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e991f-162">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
