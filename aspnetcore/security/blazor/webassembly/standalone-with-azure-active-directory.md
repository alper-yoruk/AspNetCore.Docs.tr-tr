---
title: Azure Active Blazor Directory ile ASP.NET Core WebAssembly bağımsız bir uygulamasını güvenli hale
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 7e132723657b7e12803b67ec12c3a33f1945baa3
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977008"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="64537-102">Azure Active Blazor Directory ile ASP.NET Core WebAssembly bağımsız bir uygulamasını güvenli hale</span><span class="sxs-lookup"><span data-stu-id="64537-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="64537-103">Yazar: [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="64537-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="64537-104">Kimlik doğrulaması için [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) kullanan bağımsız bir Blazor WebAssembly uygulaması oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="64537-104">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="64537-105">[Bir AAD kiracı ve web uygulaması oluşturun:](/azure/active-directory/develop/v2-overview)</span><span class="sxs-lookup"><span data-stu-id="64537-105">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="64537-106">Azure portalının Azure **Etkin Dizin** > **Uygulaması kayıtları** alanında bir AAD uygulaması kaydolun:</span><span class="sxs-lookup"><span data-stu-id="64537-106">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="64537-107">Uygulama için bir **Ad** sağlayın (örneğin, \*\* Blazor İstemci AAD).\*\*</span><span class="sxs-lookup"><span data-stu-id="64537-107">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="64537-108">Desteklenen **hesap türlerini**seçin.</span><span class="sxs-lookup"><span data-stu-id="64537-108">Choose a **Supported account types**.</span></span> <span data-ttu-id="64537-109">**Bu kuruluş dizinindeki Hesapları yalnızca** bu deneyim için seçebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="64537-109">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="64537-110">**Redirect URI** damlasını **Web'e**bırak ve uri'yi yeniden yönlendirmeyi `https://localhost:5001/authentication/login-callback`sağlar.</span><span class="sxs-lookup"><span data-stu-id="64537-110">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="64537-111">**İzinleri** > devre dışı**kaldırın, Yönetici yi openid ve offline_access izinleri** onay kutusunu açmak için yoğunlaşın.</span><span class="sxs-lookup"><span data-stu-id="64537-111">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="64537-112">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="64537-112">Select **Register**.</span></span>

<span data-ttu-id="64537-113">**Kimlik** > Doğrulama**Platformu yapılandırmalarında** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="64537-113">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="64537-114">**Redirect** URI'nin `https://localhost:5001/authentication/login-callback` mevcut olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="64537-114">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="64537-115">**Örtülü hibe**için, Erişim **belirteçleri** ve **kimlik belirteçleri**için onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="64537-115">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="64537-116">Uygulama için kalan varsayılanlar bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="64537-116">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="64537-117">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="64537-117">Select the **Save** button.</span></span>

<span data-ttu-id="64537-118">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="64537-118">Record the following information:</span></span>

* <span data-ttu-id="64537-119">Uygulama Kimliği (İstemci Kimliği) (örneğin, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="64537-119">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="64537-120">Dizin Kimliği (Kiracı Kimliği) `22222222-2222-2222-2222-222222222222`(örneğin, )</span><span class="sxs-lookup"><span data-stu-id="64537-120">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="64537-121">Aşağıdaki komuttaki yer tutucuları daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğunda uygulayın:</span><span class="sxs-lookup"><span data-stu-id="64537-121">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="64537-122">Yoksa proje klasörü oluşturan çıktı konumunu belirtmek için, çıkış seçeneğini bir yolu olan komuta ekleyin `-o BlazorSample`(örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="64537-122">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="64537-123">Klasör adı da projenin adının bir parçası olur.</span><span class="sxs-lookup"><span data-stu-id="64537-123">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="64537-124">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="64537-124">Authentication package</span></span>

<span data-ttu-id="64537-125">İş veya Okul Hesaplarını kullanmak için`SingleOrg`bir uygulama oluşturulduğunda ( ), uygulama otomatik olarak`Microsoft.Authentication.WebAssembly.Msal`Microsoft Kimlik Doğrulama [Kitaplığı](/azure/active-directory/develop/msal-overview) için bir paket başvurusu alır ( ).</span><span class="sxs-lookup"><span data-stu-id="64537-125">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="64537-126">Paket, uygulamanın kullanıcıları doğrulamasına ve korumalı API'leri aramak için belirteçler almasına yardımcı olan bir dizi ilkel lik sağlar.</span><span class="sxs-lookup"><span data-stu-id="64537-126">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="64537-127">Bir uygulamaya kimlik doğrulama ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="64537-127">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="64537-128">Önceki `{VERSION}` paket başvuruyu makalede gösterilen `Microsoft.AspNetCore.Blazor.Templates` paketin sürümüyle <xref:blazor/get-started> değiştirin.</span><span class="sxs-lookup"><span data-stu-id="64537-128">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="64537-129">Paket, `Microsoft.Authentication.WebAssembly.Msal` `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paketi geçici olarak uygulamaya ekler.</span><span class="sxs-lookup"><span data-stu-id="64537-129">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="64537-130">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="64537-130">Authentication service support</span></span>

<span data-ttu-id="64537-131">Kullanıcıların kimlik doğrulaması için destek, paket `AddMsalAuthentication` tarafından sağlanan uzantı yöntemiyle servis kapsayıcısında `Microsoft.Authentication.WebAssembly.Msal` kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="64537-131">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="64537-132">Bu yöntem, uygulamanın Kimlik Sağlayıcısı (IP) ile etkileşim kurması için gereken tüm hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="64537-132">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="64537-133">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="64537-133">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="64537-134">Yöntem, `AddMsalAuthentication` bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri arama kabul eder.</span><span class="sxs-lookup"><span data-stu-id="64537-134">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="64537-135">Uygulamayı yapılandırmak için gereken değerler, uygulamayı kaydettirdiğinizde Azure Portal AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="64537-135">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="64537-136">Belirteç kapsamlarına erişim</span><span class="sxs-lookup"><span data-stu-id="64537-136">Access token scopes</span></span>

<span data-ttu-id="64537-137">Blazor WebAssembly şablonu, güvenli bir API için erişim belirteci istemek üzere uygulamayı otomatik olarak yapılandırmaz.</span><span class="sxs-lookup"><span data-stu-id="64537-137">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="64537-138">Oturum açma akışının bir parçası olarak bir belirteç sağlamak için, kapsamı aşağıdakilerin varsayılan erişim belirteç kapsamına `MsalProviderOptions`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="64537-138">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="64537-139">Azure portalı bir kapsam oluşturuyorsa URI ve uygulama API'den *401 Yetkisiz* yanıt aldığında **işlenmemiş bir özel durum atarsa,** düzeni ve ana bilgisayarı içermeyen bir KAPSAM URI kullanmayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="64537-139">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="64537-140">Örneğin, Azure portalı aşağıdaki kapsam URI biçimlerinden birini sağlayabilir:</span><span class="sxs-lookup"><span data-stu-id="64537-140">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="64537-141">Şema ve ana bilgisayar olmadan URI kapsamını tedarik edin:</span><span class="sxs-lookup"><span data-stu-id="64537-141">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="64537-142">Daha fazla bilgi için bkz. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="64537-142">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

## <a name="imports-file"></a><span data-ttu-id="64537-143">Dosyayı alma</span><span class="sxs-lookup"><span data-stu-id="64537-143">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="64537-144">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="64537-144">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="64537-145">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="64537-145">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="64537-146">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="64537-146">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="64537-147">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="64537-147">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="64537-148">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="64537-148">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="64537-149">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="64537-149">Additional resources</span></span>

* [<span data-ttu-id="64537-150">Ek erişim belirteçleri isteme</span><span class="sxs-lookup"><span data-stu-id="64537-150">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="64537-151">Microsoft kimlik platformu belgeleri</span><span class="sxs-lookup"><span data-stu-id="64537-151">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
