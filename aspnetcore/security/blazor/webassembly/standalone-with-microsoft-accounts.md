---
title: Microsoft hesaplarıyla ASP.NET Core Blazor webassembly tek başına uygulamasının güvenliğini sağlama
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: a12cc8f94a97882e4a0ac3a6553628df4da2e82c
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111194"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="6152d-102">Microsoft hesaplarıyla ASP.NET Core Blazor webassembly tek başına uygulamasının güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="6152d-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="6152d-103">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="6152d-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="6152d-104">Bu makaledeki kılavuz, ASP.NET Core 3,2 Preview 4 için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="6152d-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="6152d-105">Bu konu, 24 Nisan, Cuma günü, Önizleme 5 ' i kapsayacak şekilde güncelleştirilecektir.</span><span class="sxs-lookup"><span data-stu-id="6152d-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="6152d-106">Kimlik doğrulaması için Blazor [Azure Active Directory (AAD) ile Microsoft hesapları](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) kullanan bir webassembly tek başına uygulaması oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="6152d-106">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

1. [<span data-ttu-id="6152d-107">AAD kiracısı ve Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="6152d-107">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

   <span data-ttu-id="6152d-108">Azure Portal **Azure Active Directory** > **uygulama kayıtları** alanına bir AAD uygulaması kaydedin:</span><span class="sxs-lookup"><span data-stu-id="6152d-108">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

   <span data-ttu-id="6152d-109">1 \.</span><span class="sxs-lookup"><span data-stu-id="6152d-109">1\.</span></span> <span data-ttu-id="6152d-110">Uygulama için bir **ad** sağlayın (örneğin, \*\* Blazor istemci AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="6152d-110">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span><br>
   <span data-ttu-id="6152d-111">2 \.</span><span class="sxs-lookup"><span data-stu-id="6152d-111">2\.</span></span> <span data-ttu-id="6152d-112">**Desteklenen hesap türleri**' nde, **herhangi bir kuruluş dizininde hesaplar**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="6152d-112">In **Supported account types**, select **Accounts in any organizational directory**.</span></span><br>
   <span data-ttu-id="6152d-113">3 \.</span><span class="sxs-lookup"><span data-stu-id="6152d-113">3\.</span></span> <span data-ttu-id="6152d-114">**Yeniden yönlendirme URI 'si** açılan listesini `https://localhost:5001/authentication/login-callback` **Web**olarak ayarlayın ve bir yeniden yönlendirme URI 'si sağlayın.</span><span class="sxs-lookup"><span data-stu-id="6152d-114">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span><br>
   <span data-ttu-id="6152d-115">4 \.</span><span class="sxs-lookup"><span data-stu-id="6152d-115">4\.</span></span> <span data-ttu-id="6152d-116"> > **Yönetici tarafından OpenID ve offline_access izinleri için** izin ver onay kutusunu devre dışı bırakın. **Permissions**</span><span class="sxs-lookup"><span data-stu-id="6152d-116">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span><br>
   <span data-ttu-id="6152d-117">5 \.</span><span class="sxs-lookup"><span data-stu-id="6152d-117">5\.</span></span> <span data-ttu-id="6152d-118">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="6152d-118">Select **Register**.</span></span>

   <span data-ttu-id="6152d-119">**Kimlik doğrulama** > **platformu yapılandırması** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="6152d-119">In **Authentication** > **Platform configurations** > **Web**:</span></span>

   <span data-ttu-id="6152d-120">1 \.</span><span class="sxs-lookup"><span data-stu-id="6152d-120">1\.</span></span> <span data-ttu-id="6152d-121">**Yeniden YÖNLENDIRME URI** 'sinin mevcut `https://localhost:5001/authentication/login-callback` olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="6152d-121">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span><br>
   <span data-ttu-id="6152d-122">2 \.</span><span class="sxs-lookup"><span data-stu-id="6152d-122">2\.</span></span> <span data-ttu-id="6152d-123">**Örtük izin**Için, **erişim belirteçleri** ve **Kimlik belirteçleri**onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="6152d-123">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span><br>
   <span data-ttu-id="6152d-124">3 \.</span><span class="sxs-lookup"><span data-stu-id="6152d-124">3\.</span></span> <span data-ttu-id="6152d-125">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="6152d-125">The remaining defaults for the app are acceptable for this experience.</span></span><br>
   <span data-ttu-id="6152d-126">4 \.</span><span class="sxs-lookup"><span data-stu-id="6152d-126">4\.</span></span> <span data-ttu-id="6152d-127">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="6152d-127">Select the **Save** button.</span></span>

   <span data-ttu-id="6152d-128">Uygulama KIMLIĞINI (Istemci KIMLIĞI) kaydedin (örneğin, `11111111-1111-1111-1111-111111111111`).</span><span class="sxs-lookup"><span data-stu-id="6152d-128">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

1. <span data-ttu-id="6152d-129">Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:</span><span class="sxs-lookup"><span data-stu-id="6152d-129">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   <span data-ttu-id="6152d-130">Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="6152d-130">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="6152d-131">Klasör adı Ayrıca projenin adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="6152d-131">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="6152d-132">Uygulamayı oluşturduktan sonra şunları yapmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="6152d-132">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="6152d-133">Bir Microsoft hesabı kullanarak uygulamada oturum açın.</span><span class="sxs-lookup"><span data-stu-id="6152d-133">Log into the app using a Microsoft Account.</span></span>
* <span data-ttu-id="6152d-134">Uygulamayı doğru şekilde yapılandırdığınıza göre, tek başına Blazor uygulamalarla aynı yaklaşımı kullanarak Microsoft API 'leri için erişim belirteçleri isteyin.</span><span class="sxs-lookup"><span data-stu-id="6152d-134">Request access tokens for Microsoft APIs using the same approach as for standalone Blazor apps provided that you have configured the app correctly.</span></span> <span data-ttu-id="6152d-135">Daha fazla bilgi için bkz. [hızlı başlangıç: Web API 'leri göstermek için uygulama yapılandırma](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="6152d-135">For more information, see [Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="6152d-136">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="6152d-136">Authentication package</span></span>

<span data-ttu-id="6152d-137">Iş veya okul hesaplarını (`SingleOrg`) kullanmak üzere bir uygulama oluşturulduğunda, uygulama [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`) için bir paket başvurusu otomatik olarak alır.</span><span class="sxs-lookup"><span data-stu-id="6152d-137">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="6152d-138">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="6152d-138">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="6152d-139">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6152d-139">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="6152d-140">Yukarıdaki `{VERSION}` paket başvurusunda, `Microsoft.AspNetCore.Blazor.Templates` <xref:blazor/get-started> makalede gösterilen paketin sürümüyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="6152d-140">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="6152d-141">`Microsoft.Authentication.WebAssembly.Msal` Paket geçişli olarak uygulamayı uygulamaya ekler `Microsoft.AspNetCore.Components.WebAssembly.Authentication` .</span><span class="sxs-lookup"><span data-stu-id="6152d-141">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="6152d-142">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="6152d-142">Authentication service support</span></span>

<span data-ttu-id="6152d-143">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal` paket tarafından sağlanmış uzantı yöntemiyle kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="6152d-143">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="6152d-144">Bu yöntem, uygulamanın kimlik sağlayıcısıyla (IP) etkileşim kurması için gereken tüm hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="6152d-144">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="6152d-145">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="6152d-145">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

<span data-ttu-id="6152d-146">Yöntemi `AddMsalAuthentication` , bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="6152d-146">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="6152d-147">Uygulamanın yapılandırılması için gereken değerler, uygulamayı kaydettiğinizde Microsoft hesapları yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="6152d-147">The values required for configuring the app can be obtained from the Microsoft Accounts configuration when you register the app.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="6152d-148">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="6152d-148">Access token scopes</span></span>

<span data-ttu-id="6152d-149">Blazor Webassembly şablonu, uygulamayı GÜVENLI bir API için erişim belirteci isteyecek şekilde otomatik olarak yapılandırmaz.</span><span class="sxs-lookup"><span data-stu-id="6152d-149">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="6152d-150">Oturum açma akışının bir parçası olarak bir belirteç sağlamak için, kapsamı varsayılan erişim belirteci kapsamlarına ekleyin `MsalProviderOptions`:</span><span class="sxs-lookup"><span data-stu-id="6152d-150">To provision a token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="6152d-151">Azure portal bir kapsam URI 'SI sağlıyorsa ve uygulama API 'den *401 Yetkisiz* bir yanıt aldığında **işlenmeyen bir özel durum oluşturursa** , düzeni ve Konağı içermeyen bir kapsam URI 'si kullanmayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="6152d-151">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="6152d-152">Örneğin, Azure portal aşağıdaki kapsam URI biçimlerinden birini verebilir:</span><span class="sxs-lookup"><span data-stu-id="6152d-152">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="6152d-153">Kapsam URI 'sini düzen ve ana bilgisayar olmadan sağlayın:</span><span class="sxs-lookup"><span data-stu-id="6152d-153">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="6152d-154">Daha fazla bilgi için bkz. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="6152d-154">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

## <a name="imports-file"></a><span data-ttu-id="6152d-155">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="6152d-155">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="6152d-156">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="6152d-156">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="6152d-157">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="6152d-157">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="6152d-158">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="6152d-158">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="6152d-159">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="6152d-159">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="6152d-160">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="6152d-160">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="6152d-161">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="6152d-161">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="6152d-162">Hızlı başlangıç: Microsoft Identity platformu ile uygulama kaydetme</span><span class="sxs-lookup"><span data-stu-id="6152d-162">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="6152d-163">Hızlı başlangıç: Web API 'Lerini kullanıma sunmak için uygulama yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6152d-163">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
