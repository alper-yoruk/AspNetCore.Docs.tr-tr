---
title: Azure Active Directory B2C bir ASP.NET Core Blazor weelsembly barındırılan uygulamasının güvenliğini sağlama
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: b451df023bdea3e76725d5d1301f3c3f44ea5d99
ms.sourcegitcommit: 30fcf69556b6b6ec54a3879e280d5f61f018b48f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82876211"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="a7924-102">Azure Active Directory B2C bir ASP.NET Core Blazor weelsembly barındırılan uygulamasının güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="a7924-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="a7924-103">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="a7924-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="a7924-104">Bu makalede kimlik doğrulaması için Blazor [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) kullanan bir webassembly tek başına uygulamasının nasıl oluşturulacağı açıklanır.</span><span class="sxs-lookup"><span data-stu-id="a7924-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="a7924-105">Uygulamaları AAD B2C kaydetme ve çözüm oluşturma</span><span class="sxs-lookup"><span data-stu-id="a7924-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="a7924-106">Kiracı oluşturma</span><span class="sxs-lookup"><span data-stu-id="a7924-106">Create a tenant</span></span>

<span data-ttu-id="a7924-107">Öğreticideki yönergeleri izleyin: bir AAD B2C kiracı oluşturmak için [Azure Active Directory B2C kiracı oluşturun](/azure/active-directory-b2c/tutorial-create-tenant) ve aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="a7924-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="a7924-108">AAD B2C örneği (örneğin, `https://contoso.b2clogin.com/`sonunda eğik çizgi içeren)</span><span class="sxs-lookup"><span data-stu-id="a7924-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="a7924-109">AAD B2C kiracı etki alanı (örneğin, `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="a7924-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="a7924-110">Sunucu API 'SI uygulaması kaydetme</span><span class="sxs-lookup"><span data-stu-id="a7924-110">Register a server API app</span></span>

<span data-ttu-id="a7924-111">Eğitim bölümünde yer alan yönergeleri izleyin: *sunucu API uygulaması* için AAD uygulamasını Azure Portal **Azure Active Directory** > **uygulama kayıtları** alanına kaydetmek için [Azure Active Directory B2C bir uygulamayı kaydedin](/azure/active-directory-b2c/tutorial-register-applications) :</span><span class="sxs-lookup"><span data-stu-id="a7924-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="a7924-112">**Yeni kayıt**seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="a7924-112">Select **New registration**.</span></span>
1. <span data-ttu-id="a7924-113">Uygulama için bir **ad** sağlayın (örneğin, \*\* Blazor sunucu AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="a7924-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="a7924-114">**Desteklenen hesap türleri**için **herhangi bir kuruluş dizininde veya herhangi bir kimlik sağlayıcısında hesaplar ' ı seçin. Azure AD B2C kullanıcıları kimlik doğrulaması için.**</span><span class="sxs-lookup"><span data-stu-id="a7924-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="a7924-115">Bu deneyim için (çok kiracılı).</span><span class="sxs-lookup"><span data-stu-id="a7924-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="a7924-116">*Sunucu API 'si uygulaması* Bu senaryoda **yeniden yönlendirme URI 'si** gerektirmez, bu nedenle açılan kutudan **Web** 'e ve yeniden yönlendirme URI 'si girmeyin.</span><span class="sxs-lookup"><span data-stu-id="a7924-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="a7924-117">**İzinlerin** > ,**OpenID 'ye yönetici tarafından verildiğini ve offline_access izinleri** olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="a7924-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="a7924-118">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="a7924-118">Select **Register**.</span></span>

<span data-ttu-id="a7924-119">**API 'Yi kullanıma**sunma bölümünde:</span><span class="sxs-lookup"><span data-stu-id="a7924-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="a7924-120">**Kapsam ekle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a7924-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="a7924-121">**Kaydet ve devam et**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="a7924-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="a7924-122">Bir **kapsam adı** sağlayın (örneğin, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="a7924-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="a7924-123">**Yönetici izni görünen adı** sağlayın (örneğin, `Access API`).</span><span class="sxs-lookup"><span data-stu-id="a7924-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="a7924-124">**Yönetici onay açıklaması** sağlayın (örneğin, `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="a7924-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="a7924-125">**Durumun** **etkin**olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="a7924-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="a7924-126">**Kapsam Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a7924-126">Select **Add scope**.</span></span>

<span data-ttu-id="a7924-127">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="a7924-127">Record the following information:</span></span>

* <span data-ttu-id="a7924-128">*Sunucu API 'si uygulaması* Uygulama KIMLIĞI (Istemci KIMLIĞI) (örneğin, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="a7924-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="a7924-129">Uygulama kimliği URI 'si (örneğin `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`,, veya belirttiğiniz özel değer)</span><span class="sxs-lookup"><span data-stu-id="a7924-129">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="a7924-130">Dizin KIMLIĞI (kiracı KIMLIĞI) (örneğin, `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="a7924-130">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="a7924-131">*Sunucu API 'si uygulaması* Uygulama KIMLIĞI URI 'SI (örneğin `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, Azure Portal istemci kimliği için varsayılan değer olarak değişebilir)</span><span class="sxs-lookup"><span data-stu-id="a7924-131">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="a7924-132">Varsayılan kapsam (örneğin, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="a7924-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="a7924-133">İstemci uygulamasını kaydetme</span><span class="sxs-lookup"><span data-stu-id="a7924-133">Register a client app</span></span>

<span data-ttu-id="a7924-134">Eğitim bölümünde yer alan yönergeleri izleyin: Azure Portal **Azure Active Directory** > **uygulama kayıtları** alanına *istemci uygulaması* için AAD uygulaması kaydetmek üzere [bir uygulamayı yeniden Azure Active Directory B2C kaydedin](/azure/active-directory-b2c/tutorial-register-applications) :</span><span class="sxs-lookup"><span data-stu-id="a7924-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="a7924-135">**Yeni kayıt**seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="a7924-135">Select **New registration**.</span></span>
1. <span data-ttu-id="a7924-136">Uygulama için bir **ad** sağlayın (örneğin, \*\* Blazor istemci AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="a7924-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="a7924-137">**Desteklenen hesap türleri**için **herhangi bir kuruluş dizininde veya herhangi bir kimlik sağlayıcısında hesaplar ' ı seçin. Azure AD B2C kullanıcıları kimlik doğrulaması için.**</span><span class="sxs-lookup"><span data-stu-id="a7924-137">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="a7924-138">Bu deneyim için (çok kiracılı).</span><span class="sxs-lookup"><span data-stu-id="a7924-138">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="a7924-139">**Yeniden yönlendirme URI 'si** açılan listesini `https://localhost:5001/authentication/login-callback` **Web**olarak ayarlayın ve bir yeniden yönlendirme URI 'si sağlayın.</span><span class="sxs-lookup"><span data-stu-id="a7924-139">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="a7924-140">**İzinlerin** > ,**OpenID 'ye yönetici tarafından verildiğini ve offline_access izinleri** olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="a7924-140">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="a7924-141">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="a7924-141">Select **Register**.</span></span>

<span data-ttu-id="a7924-142">**Kimlik doğrulama** > **platformu yapılandırması** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="a7924-142">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="a7924-143">**Yeniden YÖNLENDIRME URI** 'sinin mevcut `https://localhost:5001/authentication/login-callback` olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="a7924-143">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="a7924-144">**Örtük izin**Için, **erişim belirteçleri** ve **Kimlik belirteçleri**onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="a7924-144">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="a7924-145">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="a7924-145">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="a7924-146">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="a7924-146">Select the **Save** button.</span></span>

<span data-ttu-id="a7924-147">**API izinleri**:</span><span class="sxs-lookup"><span data-stu-id="a7924-147">In **API permissions**:</span></span>

1. <span data-ttu-id="a7924-148">Uygulamanın **Microsoft Graph** > **User. Read** iznine sahip olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="a7924-148">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="a7924-149">**Izin Ekle** ' yi ve ardından **API 'lerim**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="a7924-149">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="a7924-150">**Ad** sütunundan *sunucu API uygulamasını* seçin (örneğin, \*\* Blazor sunucu AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="a7924-150">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="a7924-151">**API** listesini açın.</span><span class="sxs-lookup"><span data-stu-id="a7924-151">Open the **API** list.</span></span>
1. <span data-ttu-id="a7924-152">API 'ye erişimi etkinleştirin (örneğin, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="a7924-152">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="a7924-153">**Izin Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a7924-153">Select **Add permissions**.</span></span>
1. <span data-ttu-id="a7924-154">**{Tenant Name} için yönetici Içeriği ver** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="a7924-154">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="a7924-155">Onaylamak için **Evet**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="a7924-155">Select **Yes** to confirm.</span></span>

<span data-ttu-id="a7924-156">**Giriş** > **Azure AD B2C**Azure AD B2C > **Kullanıcı akışları**' nda:</span><span class="sxs-lookup"><span data-stu-id="a7924-156">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="a7924-157">Kaydolma ve oturum açma Kullanıcı akışı oluşturma</span><span class="sxs-lookup"><span data-stu-id="a7924-157">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="a7924-158">En azından `context.User.Identity.Name` , `LoginDisplay` bileşende (*paylaşılan/logindisplay. Razor*) doldurmak için **uygulama talepleri** > **görünen adı** Kullanıcı özniteliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="a7924-158">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="a7924-159">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="a7924-159">Record the following information:</span></span>

* <span data-ttu-id="a7924-160">*İstemci* UYGULAMASı uygulama kimliğini (istemci kimliği) kaydedin (örneğin, `33333333-3333-3333-3333-333333333333`).</span><span class="sxs-lookup"><span data-stu-id="a7924-160">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="a7924-161">Uygulama için oluşturulan kaydolma ve oturum açma Kullanıcı akış adını kaydedin (örneğin, `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="a7924-161">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="a7924-162">Uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="a7924-162">Create the app</span></span>

<span data-ttu-id="a7924-163">Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:</span><span class="sxs-lookup"><span data-stu-id="a7924-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="a7924-164">Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="a7924-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="a7924-165">Klasör adı Ayrıca projenin adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="a7924-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="a7924-166">Uygulama KIMLIĞI URI 'sini `app-id-uri` seçeneğe geçirin, ancak [erişim belirteci kapsamları](#access-token-scopes) bölümünde açıklanan istemci uygulamasında bir yapılandırma değişikliği gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="a7924-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="a7924-167">Sunucu uygulaması yapılandırması</span><span class="sxs-lookup"><span data-stu-id="a7924-167">Server app configuration</span></span>

<span data-ttu-id="a7924-168">*Bu bölüm, çözümün **sunucu** uygulamasıyla ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="a7924-168">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a7924-169">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="a7924-169">Authentication package</span></span>

<span data-ttu-id="a7924-170">ASP.NET Core Web API 'Lerine yönelik kimlik doğrulama ve yetkilendirme desteği şu şekilde sağlanır `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span><span class="sxs-lookup"><span data-stu-id="a7924-170">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="a7924-171">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="a7924-171">Authentication service support</span></span>

<span data-ttu-id="a7924-172">`AddAuthentication` Yöntemi, uygulama içinde kimlik doğrulama hizmetlerini ayarlar ve JWT taşıyıcı işleyicisini varsayılan kimlik doğrulama yöntemi olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="a7924-172">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="a7924-173">`AddAzureADB2CBearer` Yöntemi, Azure Active Directory B2C tarafından yayılan belirteçleri doğrulamak IÇIN gereken JWT taşıyıcı işleyicisinde belirli parametreleri ayarlar:</span><span class="sxs-lookup"><span data-stu-id="a7924-173">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="a7924-174">`UseAuthentication`aşağıdakileri `UseAuthorization` doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="a7924-174">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="a7924-175">Uygulama, gelen isteklerde belirteçleri ayrıştırmaya ve doğrulamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="a7924-175">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="a7924-176">Uygun kimlik bilgileri olmadan korunan kaynağa erişmeye çalışan istekler başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="a7924-176">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="a7924-177">Kullanıcısını. Identity. Ada</span><span class="sxs-lookup"><span data-stu-id="a7924-177">User.Identity.Name</span></span>

<span data-ttu-id="a7924-178">Varsayılan `User.Identity.Name` olarak, doldurulmaz.</span><span class="sxs-lookup"><span data-stu-id="a7924-178">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="a7924-179">Uygulamayı `name` talep türünden değeri alacak şekilde yapılandırmak için, <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> içindeki `Startup.ConfigureServices` [tokenvalidationparameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) ' ı yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="a7924-179">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="a7924-180">Uygulama ayarları</span><span class="sxs-lookup"><span data-stu-id="a7924-180">App settings</span></span>

<span data-ttu-id="a7924-181">*AppSettings. JSON* dosyası, erişim belirteçlerini doğrulamak IÇIN kullanılan JWT taşıyıcı işleyicisini yapılandırma seçeneklerini içerir.</span><span class="sxs-lookup"><span data-stu-id="a7924-181">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="a7924-182">Örnek:</span><span class="sxs-lookup"><span data-stu-id="a7924-182">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="a7924-183">Hava tahmin denetleyicisi</span><span class="sxs-lookup"><span data-stu-id="a7924-183">WeatherForecast controller</span></span>

<span data-ttu-id="a7924-184">Dalgalı tahmin denetleyicisi (*denetleyiciler/dalgalı Therforetcontroller. cs*), BIR korumalı API `[Authorize]` 'yi denetleyiciye uygulanmış şekilde gösterir.</span><span class="sxs-lookup"><span data-stu-id="a7924-184">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="a7924-185">Bunun anlaşılması **önemlidir** :</span><span class="sxs-lookup"><span data-stu-id="a7924-185">It's **important** to understand that:</span></span>

* <span data-ttu-id="a7924-186">Bu `[Authorize]` API denetleyicisindeki özniteliği, bu API 'yi yetkisiz erişime karşı koruyan tek şeydir.</span><span class="sxs-lookup"><span data-stu-id="a7924-186">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="a7924-187">Blazor Webassembly uygulamasında kullanılan `[Authorize]` özniteliği yalnızca uygulamanın, uygulamanın düzgün şekilde çalışması için yetkilendirilmiş olması gerektiğine yönelik bir ipucu görevi görür.</span><span class="sxs-lookup"><span data-stu-id="a7924-187">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="a7924-188">İstemci uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="a7924-188">Client app configuration</span></span>

<span data-ttu-id="a7924-189">*Bu bölüm, çözümün **istemci** uygulaması ile ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="a7924-189">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="a7924-190">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="a7924-190">Authentication package</span></span>

<span data-ttu-id="a7924-191">Tek bir B2C hesabı (`IndividualB2C`) kullanmak üzere bir uygulama oluşturulduğunda, uygulama otomatik olarak [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`) için bir paket başvurusu alır.</span><span class="sxs-lookup"><span data-stu-id="a7924-191">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="a7924-192">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="a7924-192">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="a7924-193">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="a7924-193">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="a7924-194">Yukarıdaki `{VERSION}` paket başvurusunda, `Microsoft.AspNetCore.Blazor.Templates` <xref:blazor/get-started> makalede gösterilen paketin sürümüyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="a7924-194">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="a7924-195">`Microsoft.Authentication.WebAssembly.Msal` Paket geçişli olarak uygulamayı uygulamaya ekler `Microsoft.AspNetCore.Components.WebAssembly.Authentication` .</span><span class="sxs-lookup"><span data-stu-id="a7924-195">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="a7924-196">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="a7924-196">Authentication service support</span></span>

<span data-ttu-id="a7924-197">Sunucu projesine `HttpClient` istek yaparken erişim belirteçlerini içeren örnekler için destek eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="a7924-197">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="a7924-198">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a7924-198">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="a7924-199">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal` paket tarafından sağlanmış uzantı yöntemiyle kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="a7924-199">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="a7924-200">Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken tüm hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="a7924-200">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="a7924-201">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="a7924-201">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="a7924-202">Yöntemi `AddMsalAuthentication` , bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="a7924-202">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="a7924-203">Uygulamanın yapılandırılması için gereken değerler, uygulamayı kaydettiğinizde Azure Portal AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="a7924-203">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="a7924-204">Yapılandırma *Wwwroot/appSettings. JSON* dosyası tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="a7924-204">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="a7924-205">Örnek:</span><span class="sxs-lookup"><span data-stu-id="a7924-205">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="a7924-206">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="a7924-206">Access token scopes</span></span>

<span data-ttu-id="a7924-207">Varsayılan erişim belirteci kapsamları, erişim belirteci kapsamlarının listesini temsil eder:</span><span class="sxs-lookup"><span data-stu-id="a7924-207">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="a7924-208">Oturum açma isteğine varsayılan olarak dahildir.</span><span class="sxs-lookup"><span data-stu-id="a7924-208">Included by default in the sign in request.</span></span>
* <span data-ttu-id="a7924-209">Kimlik doğrulamasından hemen sonra bir erişim belirteci sağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="a7924-209">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="a7924-210">Tüm kapsamlar Azure Active Directory kuralları başına aynı uygulamaya ait olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a7924-210">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="a7924-211">Gerektiğinde ek API uygulamaları için ek kapsamlar eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="a7924-211">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="a7924-212">Azure portal bir kapsam URI 'SI sağlıyorsa ve uygulama API 'den *401 Yetkisiz* bir yanıt aldığında **işlenmeyen bir özel durum oluşturursa** , düzeni ve Konağı içermeyen bir kapsam URI 'si kullanmayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="a7924-212">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="a7924-213">Örneğin, Azure portal aşağıdaki kapsam URI biçimlerinden birini verebilir:</span><span class="sxs-lookup"><span data-stu-id="a7924-213">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="a7924-214">Kapsam URI 'sini düzen ve ana bilgisayar olmadan sağlayın:</span><span class="sxs-lookup"><span data-stu-id="a7924-214">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="a7924-215">Daha fazla bilgi için *ek senaryolar* makalesinin aşağıdaki bölümlerine bakın:</span><span class="sxs-lookup"><span data-stu-id="a7924-215">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="a7924-216">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="a7924-216">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="a7924-217">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="a7924-217">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="a7924-218">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="a7924-218">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="a7924-219">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="a7924-219">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="a7924-220">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="a7924-220">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="a7924-221">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="a7924-221">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="a7924-222">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="a7924-222">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="a7924-223">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="a7924-223">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="a7924-224">FetchData bileşeni</span><span class="sxs-lookup"><span data-stu-id="a7924-224">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="a7924-225">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="a7924-225">Run the app</span></span>

<span data-ttu-id="a7924-226">Uygulamayı sunucu projesinden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a7924-226">Run the app from the Server project.</span></span> <span data-ttu-id="a7924-227">Visual Studio 'Yu kullanırken **Çözüm Gezgini** ' de sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya uygulamayı **Hata Ayıkla** menüsünden başlatın.</span><span class="sxs-lookup"><span data-stu-id="a7924-227">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="a7924-228">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="a7924-228">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="a7924-229">Öğretici: Azure Active Directory B2C kiracısı oluşturma</span><span class="sxs-lookup"><span data-stu-id="a7924-229">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="a7924-230">Microsoft kimlik platformu belgeleri</span><span class="sxs-lookup"><span data-stu-id="a7924-230">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
