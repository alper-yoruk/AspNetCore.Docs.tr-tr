---
title: Azure Active Directory bir ASP.NET Core Blazor weelsembly barındırılan uygulamasının güvenliğini sağlama
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 8c24546da50607d692a9cdc9f9c007d6ac8645ad
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110934"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="fe984-102">Azure Active Directory bir ASP.NET Core Blazor weelsembly barındırılan uygulamasının güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="fe984-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="fe984-103">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="fe984-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="fe984-104">Bu makaledeki kılavuz, ASP.NET Core 3,2 Preview 4 için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="fe984-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="fe984-105">Bu konu, 24 Nisan, Cuma günü, Önizleme 5 ' i kapsayacak şekilde güncelleştirilecektir.</span><span class="sxs-lookup"><span data-stu-id="fe984-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="fe984-106">Bu makalede, kimlik doğrulaması için [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) kullanan bir [ Blazor webassembly barındırılan uygulamasının](xref:blazor/hosting-models#blazor-webassembly) nasıl oluşturulacağı açıklanır.</span><span class="sxs-lookup"><span data-stu-id="fe984-106">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="fe984-107">Uygulamaları AAD B2C kaydetme ve çözüm oluşturma</span><span class="sxs-lookup"><span data-stu-id="fe984-107">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="fe984-108">Kiracı oluşturma</span><span class="sxs-lookup"><span data-stu-id="fe984-108">Create a tenant</span></span>

<span data-ttu-id="fe984-109">Hızlı başlangıç: AAD 'de kiracı oluşturmak için [bir kiracı ayarlama](/azure/active-directory/develop/quickstart-create-new-tenant) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="fe984-109">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="fe984-110">Sunucu API 'SI uygulaması kaydetme</span><span class="sxs-lookup"><span data-stu-id="fe984-110">Register a server API app</span></span>

<span data-ttu-id="fe984-111">Hızlı Başlangıç bölümündeki yönergeleri izleyin: *sunucu API uygulaması* için bir AAD uygulamasını [Microsoft Identity platformu ile kaydetme](/azure/active-directory/develop/quickstart-register-app) ve sonraki Azure AAD konuları Azure Portal **Azure Active Directory** > **uygulama kayıtları** alanı:</span><span class="sxs-lookup"><span data-stu-id="fe984-111">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="fe984-112">**Yeni kayıt**seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="fe984-112">Select **New registration**.</span></span>
1. <span data-ttu-id="fe984-113">Uygulama için bir **ad** sağlayın (örneğin, \*\* Blazor sunucu AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="fe984-113">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="fe984-114">Desteklenen bir **Hesap türü**seçin.</span><span class="sxs-lookup"><span data-stu-id="fe984-114">Choose a **Supported account types**.</span></span> <span data-ttu-id="fe984-115">Bu deneyim için **yalnızca bu kuruluş dizininde** (tek kiracı) hesaplar seçebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fe984-115">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="fe984-116">*Sunucu API 'si uygulaması* Bu senaryoda **yeniden yönlendirme URI 'si** gerektirmez, bu nedenle açılan kutudan **Web** 'e ve yeniden yönlendirme URI 'si girmeyin.</span><span class="sxs-lookup"><span data-stu-id="fe984-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="fe984-117"> > **Yönetici tarafından OpenID ve offline_access izinleri için** izin ver onay kutusunu devre dışı bırakın. **Permissions**</span><span class="sxs-lookup"><span data-stu-id="fe984-117">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="fe984-118">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="fe984-118">Select **Register**.</span></span>

<span data-ttu-id="fe984-119">**API izinlerinde**, uygulama oturum açma veya uer profil erişimi gerektirmediğinden **Microsoft Graph** > **User. Read** iznini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="fe984-119">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="fe984-120">**API 'Yi kullanıma**sunma bölümünde:</span><span class="sxs-lookup"><span data-stu-id="fe984-120">In **Expose an API**:</span></span>

1. <span data-ttu-id="fe984-121">**Kapsam ekle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="fe984-121">Select **Add a scope**.</span></span>
1. <span data-ttu-id="fe984-122">**Kaydet ve devam et**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="fe984-122">Select **Save and continue**.</span></span>
1. <span data-ttu-id="fe984-123">Bir **kapsam adı** sağlayın (örneğin, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="fe984-123">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="fe984-124">**Yönetici izni görünen adı** sağlayın (örneğin, `Access API`).</span><span class="sxs-lookup"><span data-stu-id="fe984-124">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="fe984-125">**Yönetici onay açıklaması** sağlayın (örneğin, `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="fe984-125">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="fe984-126">**Durumun** **etkin**olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="fe984-126">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="fe984-127">**Kapsam Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="fe984-127">Select **Add scope**.</span></span>

<span data-ttu-id="fe984-128">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="fe984-128">Record the following information:</span></span>

* <span data-ttu-id="fe984-129">*Sunucu API 'si uygulaması* Uygulama KIMLIĞI (Istemci KIMLIĞI) (örneğin, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="fe984-129">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="fe984-130">Uygulama kimliği URI 'si (örneğin `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`,, veya belirttiğiniz özel değer)</span><span class="sxs-lookup"><span data-stu-id="fe984-130">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="fe984-131">Dizin KIMLIĞI (kiracı KIMLIĞI) (örneğin, `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="fe984-131">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="fe984-132">AAD kiracı etki alanı (örneğin, `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="fe984-132">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="fe984-133">Varsayılan kapsam (örneğin, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="fe984-133">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="fe984-134">İstemci uygulamasını kaydetme</span><span class="sxs-lookup"><span data-stu-id="fe984-134">Register a client app</span></span>

<span data-ttu-id="fe984-135">Hızlı Başlangıç bölümündeki yönergeleri izleyin: *istemci uygulaması* için bir AAD uygulamasını [Microsoft Identity platformu ile kaydetme](/azure/active-directory/develop/quickstart-register-app) ve sonraki Azure AAD konuları Azure Portal **Azure Active Directory** > **uygulama kayıtları** alanında.</span><span class="sxs-lookup"><span data-stu-id="fe984-135">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="fe984-136">**Yeni kayıt**seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="fe984-136">Select **New registration**.</span></span>
1. <span data-ttu-id="fe984-137">Uygulama için bir **ad** sağlayın (örneğin, \*\* Blazor istemci AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="fe984-137">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="fe984-138">Desteklenen bir **Hesap türü**seçin.</span><span class="sxs-lookup"><span data-stu-id="fe984-138">Choose a **Supported account types**.</span></span> <span data-ttu-id="fe984-139">Bu deneyim için **yalnızca bu kuruluş dizininde** (tek kiracı) hesaplar seçebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fe984-139">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="fe984-140">**Yeniden yönlendirme URI 'si** açılan listesini `https://localhost:5001/authentication/login-callback` **Web**olarak ayarlayın ve bir yeniden yönlendirme URI 'si sağlayın.</span><span class="sxs-lookup"><span data-stu-id="fe984-140">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="fe984-141"> > **Yönetici tarafından OpenID ve offline_access izinleri için** izin ver onay kutusunu devre dışı bırakın. **Permissions**</span><span class="sxs-lookup"><span data-stu-id="fe984-141">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="fe984-142">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="fe984-142">Select **Register**.</span></span>

<span data-ttu-id="fe984-143">**Kimlik doğrulama** > **platformu yapılandırması** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="fe984-143">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="fe984-144">**Yeniden YÖNLENDIRME URI** 'sinin mevcut `https://localhost:5001/authentication/login-callback` olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="fe984-144">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="fe984-145">**Örtük izin**Için, **erişim belirteçleri** ve **Kimlik belirteçleri**onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="fe984-145">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="fe984-146">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="fe984-146">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="fe984-147">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="fe984-147">Select the **Save** button.</span></span>

<span data-ttu-id="fe984-148">**API izinleri**:</span><span class="sxs-lookup"><span data-stu-id="fe984-148">In **API permissions**:</span></span>

1. <span data-ttu-id="fe984-149">Uygulamanın **Microsoft Graph** > **User. Read** iznine sahip olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="fe984-149">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="fe984-150">**Izin Ekle** ' yi ve ardından **API 'lerim**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="fe984-150">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="fe984-151">**Ad** sütunundan *sunucu API uygulamasını* (örneğin, \*\* Blazor sunucu AAD\*\*) seçin.</span><span class="sxs-lookup"><span data-stu-id="fe984-151">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="fe984-152">**API** listesini açın.</span><span class="sxs-lookup"><span data-stu-id="fe984-152">Open the **API** list.</span></span>
1. <span data-ttu-id="fe984-153">API 'ye erişimi etkinleştirin (örneğin, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="fe984-153">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="fe984-154">**Izin Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="fe984-154">Select **Add permissions**.</span></span>
1. <span data-ttu-id="fe984-155">**{Tenant Name} için yönetici Içeriği ver** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="fe984-155">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="fe984-156">Onaylamak için **Evet**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="fe984-156">Select **Yes** to confirm.</span></span>

<span data-ttu-id="fe984-157">*İstemci* UYGULAMASı uygulama kimliğini (istemci kimliği) kaydedin (örneğin, `33333333-3333-3333-3333-333333333333`).</span><span class="sxs-lookup"><span data-stu-id="fe984-157">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="fe984-158">Uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="fe984-158">Create the app</span></span>

<span data-ttu-id="fe984-159">Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:</span><span class="sxs-lookup"><span data-stu-id="fe984-159">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="fe984-160">Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="fe984-160">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="fe984-161">Klasör adı Ayrıca projenin adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="fe984-161">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="fe984-162">Uygulama KIMLIĞI URI 'sini `app-id-uri` seçeneğe geçirin, ancak [erişim belirteci kapsamları](#access-token-scopes) bölümünde açıklanan istemci uygulamasında bir yapılandırma değişikliği gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="fe984-162">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="fe984-163">Sunucu uygulaması yapılandırması</span><span class="sxs-lookup"><span data-stu-id="fe984-163">Server app configuration</span></span>

<span data-ttu-id="fe984-164">*Bu bölüm, çözümün **sunucu** uygulamasıyla ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="fe984-164">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="fe984-165">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="fe984-165">Authentication package</span></span>

<span data-ttu-id="fe984-166">ASP.NET Core Web API 'Lerine yönelik kimlik doğrulama ve yetkilendirme desteği şu şekilde sağlanır `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span><span class="sxs-lookup"><span data-stu-id="fe984-166">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="fe984-167">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="fe984-167">Authentication service support</span></span>

<span data-ttu-id="fe984-168">`AddAuthentication` Yöntemi, uygulama içinde kimlik doğrulama hizmetlerini ayarlar ve JWT taşıyıcı işleyicisini varsayılan kimlik doğrulama yöntemi olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="fe984-168">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="fe984-169">`AddAzureADBearer` Yöntemi, Azure Active Directory tarafından yayılan belirteçleri doğrulamak IÇIN gereken JWT taşıyıcı işleyicisinde belirli parametreleri ayarlar:</span><span class="sxs-lookup"><span data-stu-id="fe984-169">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="fe984-170">`UseAuthentication`aşağıdakileri `UseAuthorization` doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="fe984-170">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="fe984-171">Uygulama, gelen isteklerde belirteçleri ayrıştırmaya ve doğrulamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="fe984-171">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="fe984-172">Uygun kimlik bilgileri olmadan korunan kaynağa erişmeye çalışan istekler başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="fe984-172">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="fe984-173">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="fe984-173">User.Identity.Name</span></span>

<span data-ttu-id="fe984-174">Varsayılan olarak, sunucu uygulaması API 'SI `User.Identity.Name` `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` talep türünden değer ile doldurulur (örneğin, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span><span class="sxs-lookup"><span data-stu-id="fe984-174">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="fe984-175">Uygulamayı `name` talep türünden değeri alacak şekilde yapılandırmak için, <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> içindeki `Startup.ConfigureServices` [tokenvalidationparameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) ' ı yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="fe984-175">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="fe984-176">Uygulama ayarları</span><span class="sxs-lookup"><span data-stu-id="fe984-176">App settings</span></span>

<span data-ttu-id="fe984-177">*AppSettings. JSON* dosyası, erişim belirteçlerini doğrulamak IÇIN kullanılan JWT taşıyıcı işleyicisini yapılandırma seçeneklerini içerir.</span><span class="sxs-lookup"><span data-stu-id="fe984-177">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="fe984-178">Hava tahmin denetleyicisi</span><span class="sxs-lookup"><span data-stu-id="fe984-178">WeatherForecast controller</span></span>

<span data-ttu-id="fe984-179">Dalgalı tahmin denetleyicisi (*denetleyiciler/dalgalı Therforetcontroller. cs*), BIR korumalı API `[Authorize]` 'yi denetleyiciye uygulanmış şekilde gösterir.</span><span class="sxs-lookup"><span data-stu-id="fe984-179">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="fe984-180">Bunun anlaşılması **önemlidir** :</span><span class="sxs-lookup"><span data-stu-id="fe984-180">It's **important** to understand that:</span></span>

* <span data-ttu-id="fe984-181">Bu `[Authorize]` API denetleyicisindeki özniteliği, bu API 'yi yetkisiz erişime karşı koruyan tek şeydir.</span><span class="sxs-lookup"><span data-stu-id="fe984-181">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="fe984-182">Blazor Webassembly uygulamasında kullanılan `[Authorize]` özniteliği yalnızca uygulamanın, uygulamanın düzgün şekilde çalışması için yetkilendirilmiş olması gerektiğine yönelik bir ipucu görevi görür.</span><span class="sxs-lookup"><span data-stu-id="fe984-182">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a><span data-ttu-id="fe984-183">İstemci uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="fe984-183">Client app configuration</span></span>

<span data-ttu-id="fe984-184">*Bu bölüm, çözümün **istemci** uygulaması ile ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="fe984-184">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="fe984-185">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="fe984-185">Authentication package</span></span>

<span data-ttu-id="fe984-186">Iş veya okul hesaplarını (`SingleOrg`) kullanmak üzere bir uygulama oluşturulduğunda, uygulama [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`) için bir paket başvurusu otomatik olarak alır.</span><span class="sxs-lookup"><span data-stu-id="fe984-186">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="fe984-187">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="fe984-187">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="fe984-188">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="fe984-188">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="fe984-189">Yukarıdaki `{VERSION}` paket başvurusunda, `Microsoft.AspNetCore.Blazor.Templates` <xref:blazor/get-started> makalede gösterilen paketin sürümüyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="fe984-189">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="fe984-190">`Microsoft.Authentication.WebAssembly.Msal` Paket geçişli olarak uygulamayı uygulamaya ekler `Microsoft.AspNetCore.Components.WebAssembly.Authentication` .</span><span class="sxs-lookup"><span data-stu-id="fe984-190">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="fe984-191">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="fe984-191">Authentication service support</span></span>

<span data-ttu-id="fe984-192">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal` paket tarafından sağlanmış uzantı yöntemiyle kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="fe984-192">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="fe984-193">Bu yöntem, uygulamanın kimlik sağlayıcısıyla (IP) etkileşim kurması için gereken tüm hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="fe984-193">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="fe984-194">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="fe984-194">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="fe984-195">Yöntemi `AddMsalAuthentication` , bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="fe984-195">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="fe984-196">Uygulamanın yapılandırılması için gereken değerler, uygulamayı kaydettiğinizde Azure Portal AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="fe984-196">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="fe984-197">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="fe984-197">Access token scopes</span></span>

<span data-ttu-id="fe984-198">Varsayılan erişim belirteci kapsamları, erişim belirteci kapsamlarının listesini temsil eder:</span><span class="sxs-lookup"><span data-stu-id="fe984-198">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="fe984-199">Oturum açma isteğine varsayılan olarak dahildir.</span><span class="sxs-lookup"><span data-stu-id="fe984-199">Included by default in the sign in request.</span></span>
* <span data-ttu-id="fe984-200">Kimlik doğrulamasından hemen sonra bir erişim belirteci sağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="fe984-200">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="fe984-201">Tüm kapsamlar Azure Active Directory kuralları başına aynı uygulamaya ait olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="fe984-201">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="fe984-202">Gerektiğinde ek API uygulamaları için ek kapsamlar eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="fe984-202">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="fe984-203">Azure portal bir kapsam URI 'SI sağlıyorsa ve uygulama API 'den *401 Yetkisiz* bir yanıt aldığında **işlenmeyen bir özel durum oluşturursa** , düzeni ve Konağı içermeyen bir kapsam URI 'si kullanmayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="fe984-203">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="fe984-204">Örneğin, Azure portal aşağıdaki kapsam URI biçimlerinden birini verebilir:</span><span class="sxs-lookup"><span data-stu-id="fe984-204">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="fe984-205">Kapsam URI 'sini düzen ve ana bilgisayar olmadan sağlayın:</span><span class="sxs-lookup"><span data-stu-id="fe984-205">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="fe984-206">Daha fazla bilgi için bkz. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="fe984-206">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

### <a name="imports-file"></a><span data-ttu-id="fe984-207">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="fe984-207">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="fe984-208">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="fe984-208">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="fe984-209">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="fe984-209">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="fe984-210">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="fe984-210">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="fe984-211">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="fe984-211">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="fe984-212">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="fe984-212">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="fe984-213">FetchData bileşeni</span><span class="sxs-lookup"><span data-stu-id="fe984-213">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="fe984-214">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="fe984-214">Run the app</span></span>

<span data-ttu-id="fe984-215">Uygulamayı sunucu projesinden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="fe984-215">Run the app from the Server project.</span></span> <span data-ttu-id="fe984-216">Visual Studio 'Yu kullanırken **Çözüm Gezgini** ' de sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya uygulamayı **Hata Ayıkla** menüsünden başlatın.</span><span class="sxs-lookup"><span data-stu-id="fe984-216">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="fe984-217">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="fe984-217">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="fe984-218">Microsoft kimlik platformu belgeleri</span><span class="sxs-lookup"><span data-stu-id="fe984-218">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
