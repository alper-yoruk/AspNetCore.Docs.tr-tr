---
title: Azure Active Directory bir ASP.NET Core Blazor webassembly tek başına uygulamasının güvenliğini sağlama
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 71229f41f3f1021aa9ad02402af21de51d7eeee4
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111207"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="571b8-102">Azure Active Directory bir ASP.NET Core Blazor webassembly tek başına uygulamasının güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="571b8-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="571b8-103">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="571b8-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="571b8-104">Bu makaledeki kılavuz, ASP.NET Core 3,2 Preview 4 için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="571b8-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="571b8-105">Bu konu, 24 Nisan, Cuma günü, Önizleme 5 ' i kapsayacak şekilde güncelleştirilecektir.</span><span class="sxs-lookup"><span data-stu-id="571b8-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="571b8-106">Kimlik doğrulaması için Blazor [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) kullanan bir webassembly tek başına uygulaması oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="571b8-106">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="571b8-107">[AAD kiracısı ve Web uygulaması oluşturma](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="571b8-107">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="571b8-108">Azure Portal **Azure Active Directory** > **uygulama kayıtları** alanına bir AAD uygulaması kaydedin:</span><span class="sxs-lookup"><span data-stu-id="571b8-108">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="571b8-109">Uygulama için bir **ad** sağlayın (örneğin, \*\* Blazor istemci AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="571b8-109">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="571b8-110">Desteklenen bir **Hesap türü**seçin.</span><span class="sxs-lookup"><span data-stu-id="571b8-110">Choose a **Supported account types**.</span></span> <span data-ttu-id="571b8-111">Bu **kuruluş dizininde yalnızca** bu deneyim için hesaplar seçebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="571b8-111">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="571b8-112">**Yeniden yönlendirme URI 'si** açılan listesini `https://localhost:5001/authentication/login-callback` **Web**olarak ayarlayın ve bir yeniden yönlendirme URI 'si sağlayın.</span><span class="sxs-lookup"><span data-stu-id="571b8-112">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="571b8-113"> > **Yönetici tarafından OpenID ve offline_access izinleri için** izin ver onay kutusunu devre dışı bırakın. **Permissions**</span><span class="sxs-lookup"><span data-stu-id="571b8-113">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="571b8-114">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="571b8-114">Select **Register**.</span></span>

<span data-ttu-id="571b8-115">**Kimlik doğrulama** > **platformu yapılandırması** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="571b8-115">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="571b8-116">**Yeniden YÖNLENDIRME URI** 'sinin mevcut `https://localhost:5001/authentication/login-callback` olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="571b8-116">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="571b8-117">**Örtük izin**Için, **erişim belirteçleri** ve **Kimlik belirteçleri**onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="571b8-117">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="571b8-118">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="571b8-118">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="571b8-119">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="571b8-119">Select the **Save** button.</span></span>

<span data-ttu-id="571b8-120">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="571b8-120">Record the following information:</span></span>

* <span data-ttu-id="571b8-121">Uygulama KIMLIĞI (Istemci KIMLIĞI) (örneğin, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="571b8-121">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="571b8-122">Dizin KIMLIĞI (kiracı KIMLIĞI) (örneğin, `22222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="571b8-122">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="571b8-123">Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:</span><span class="sxs-lookup"><span data-stu-id="571b8-123">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="571b8-124">Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="571b8-124">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="571b8-125">Klasör adı Ayrıca projenin adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="571b8-125">The folder name also becomes part of the project's name.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="571b8-126">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="571b8-126">Authentication package</span></span>

<span data-ttu-id="571b8-127">Iş veya okul hesaplarını (`SingleOrg`) kullanmak üzere bir uygulama oluşturulduğunda, uygulama [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`) için bir paket başvurusu otomatik olarak alır.</span><span class="sxs-lookup"><span data-stu-id="571b8-127">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="571b8-128">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="571b8-128">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="571b8-129">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="571b8-129">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="571b8-130">Yukarıdaki `{VERSION}` paket başvurusunda, `Microsoft.AspNetCore.Blazor.Templates` <xref:blazor/get-started> makalede gösterilen paketin sürümüyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="571b8-130">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="571b8-131">`Microsoft.Authentication.WebAssembly.Msal` Paket geçişli olarak uygulamayı uygulamaya ekler `Microsoft.AspNetCore.Components.WebAssembly.Authentication` .</span><span class="sxs-lookup"><span data-stu-id="571b8-131">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="571b8-132">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="571b8-132">Authentication service support</span></span>

<span data-ttu-id="571b8-133">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal` paket tarafından sağlanmış uzantı yöntemiyle kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="571b8-133">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="571b8-134">Bu yöntem, uygulamanın kimlik sağlayıcısıyla (IP) etkileşim kurması için gereken tüm hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="571b8-134">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="571b8-135">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="571b8-135">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="571b8-136">Yöntemi `AddMsalAuthentication` , bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="571b8-136">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="571b8-137">Uygulamanın yapılandırılması için gereken değerler, uygulamayı kaydettiğinizde Azure Portal AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="571b8-137">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="571b8-138">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="571b8-138">Access token scopes</span></span>

<span data-ttu-id="571b8-139">Blazor Webassembly şablonu, uygulamayı GÜVENLI bir API için erişim belirteci isteyecek şekilde otomatik olarak yapılandırmaz.</span><span class="sxs-lookup"><span data-stu-id="571b8-139">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="571b8-140">Oturum açma akışının bir parçası olarak bir belirteç sağlamak için, kapsamı varsayılan erişim belirteci kapsamlarına ekleyin `MsalProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="571b8-140">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="571b8-141">Azure portal bir kapsam URI 'SI sağlıyorsa ve uygulama API 'den *401 Yetkisiz* bir yanıt aldığında **işlenmeyen bir özel durum oluşturursa** , düzeni ve Konağı içermeyen bir kapsam URI 'si kullanmayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="571b8-141">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="571b8-142">Örneğin, Azure portal aşağıdaki kapsam URI biçimlerinden birini verebilir:</span><span class="sxs-lookup"><span data-stu-id="571b8-142">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="571b8-143">Kapsam URI 'sini düzen ve ana bilgisayar olmadan sağlayın:</span><span class="sxs-lookup"><span data-stu-id="571b8-143">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="571b8-144">Daha fazla bilgi için bkz. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="571b8-144">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="571b8-145">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="571b8-145">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="571b8-146">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="571b8-146">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="571b8-147">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="571b8-147">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="571b8-148">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="571b8-148">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="571b8-149">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="571b8-149">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="571b8-150">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="571b8-150">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="571b8-151">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="571b8-151">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="571b8-152">Microsoft kimlik platformu belgeleri</span><span class="sxs-lookup"><span data-stu-id="571b8-152">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
