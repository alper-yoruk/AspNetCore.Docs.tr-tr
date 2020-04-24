---
title: Azure Active Directory B2C bir ASP.NET Core Blazor weelsembly barındırılan uygulamasının güvenliğini sağlama
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 45ef1e6599777a38da7db753a8868028f3134f4b
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82110986"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="e1e68-102">Azure Active Directory B2C bir ASP.NET Core Blazor weelsembly barındırılan uygulamasının güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="e1e68-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="e1e68-103">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="e1e68-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> <span data-ttu-id="e1e68-104">Bu makaledeki kılavuz, ASP.NET Core 3,2 Preview 4 için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="e1e68-104">The guidance in this article applies to ASP.NET Core 3.2 Preview 4.</span></span> <span data-ttu-id="e1e68-105">Bu konu, 24 Nisan, Cuma günü, Önizleme 5 ' i kapsayacak şekilde güncelleştirilecektir.</span><span class="sxs-lookup"><span data-stu-id="e1e68-105">This topic will be updated to cover Preview 5 on Friday, April 24.</span></span>

<span data-ttu-id="e1e68-106">Bu makalede kimlik doğrulaması için Blazor [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) kullanan bir webassembly tek başına uygulamasının nasıl oluşturulacağı açıklanır.</span><span class="sxs-lookup"><span data-stu-id="e1e68-106">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="e1e68-107">Uygulamaları AAD B2C kaydetme ve çözüm oluşturma</span><span class="sxs-lookup"><span data-stu-id="e1e68-107">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="e1e68-108">Kiracı oluşturma</span><span class="sxs-lookup"><span data-stu-id="e1e68-108">Create a tenant</span></span>

<span data-ttu-id="e1e68-109">Öğreticideki yönergeleri izleyin: bir AAD B2C kiracı oluşturmak için [Azure Active Directory B2C kiracı oluşturun](/azure/active-directory-b2c/tutorial-create-tenant) ve aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="e1e68-109">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="e1e68-110">AAD B2C örneği (örneğin, `https://contoso.b2clogin.com/`sonunda eğik çizgi içeren)</span><span class="sxs-lookup"><span data-stu-id="e1e68-110">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="e1e68-111">AAD B2C kiracı etki alanı (örneğin, `contoso.onmicrosoft.com`)</span><span class="sxs-lookup"><span data-stu-id="e1e68-111">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="e1e68-112">Sunucu API 'SI uygulaması kaydetme</span><span class="sxs-lookup"><span data-stu-id="e1e68-112">Register a server API app</span></span>

<span data-ttu-id="e1e68-113">Eğitim bölümünde yer alan yönergeleri izleyin: *sunucu API uygulaması* için AAD uygulamasını Azure Portal **Azure Active Directory** > **uygulama kayıtları** alanına kaydetmek için [Azure Active Directory B2C bir uygulamayı kaydedin](/azure/active-directory-b2c/tutorial-register-applications) :</span><span class="sxs-lookup"><span data-stu-id="e1e68-113">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="e1e68-114">**Yeni kayıt**seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="e1e68-114">Select **New registration**.</span></span>
1. <span data-ttu-id="e1e68-115">Uygulama için bir **ad** sağlayın (örneğin, \*\* Blazor sunucu AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="e1e68-115">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="e1e68-116">**Desteklenen hesap türleri**için **herhangi bir kuruluş dizininde veya herhangi bir kimlik sağlayıcısında hesaplar ' ı seçin. Azure AD B2C kullanıcıları kimlik doğrulaması için.**</span><span class="sxs-lookup"><span data-stu-id="e1e68-116">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="e1e68-117">Bu deneyim için (çok kiracılı).</span><span class="sxs-lookup"><span data-stu-id="e1e68-117">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="e1e68-118">*Sunucu API 'si uygulaması* Bu senaryoda **yeniden yönlendirme URI 'si** gerektirmez, bu nedenle açılan kutudan **Web** 'e ve yeniden yönlendirme URI 'si girmeyin.</span><span class="sxs-lookup"><span data-stu-id="e1e68-118">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="e1e68-119">**İzinlerin** > ,**OpenID 'ye yönetici tarafından verildiğini ve offline_access izinleri** olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="e1e68-119">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="e1e68-120">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="e1e68-120">Select **Register**.</span></span>

<span data-ttu-id="e1e68-121">**API 'Yi kullanıma**sunma bölümünde:</span><span class="sxs-lookup"><span data-stu-id="e1e68-121">In **Expose an API**:</span></span>

1. <span data-ttu-id="e1e68-122">**Kapsam ekle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="e1e68-122">Select **Add a scope**.</span></span>
1. <span data-ttu-id="e1e68-123">**Kaydet ve devam et**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="e1e68-123">Select **Save and continue**.</span></span>
1. <span data-ttu-id="e1e68-124">Bir **kapsam adı** sağlayın (örneğin, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="e1e68-124">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="e1e68-125">**Yönetici izni görünen adı** sağlayın (örneğin, `Access API`).</span><span class="sxs-lookup"><span data-stu-id="e1e68-125">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="e1e68-126">**Yönetici onay açıklaması** sağlayın (örneğin, `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="e1e68-126">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="e1e68-127">**Durumun** **etkin**olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="e1e68-127">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="e1e68-128">**Kapsam Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="e1e68-128">Select **Add scope**.</span></span>

<span data-ttu-id="e1e68-129">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="e1e68-129">Record the following information:</span></span>

* <span data-ttu-id="e1e68-130">*Sunucu API 'si uygulaması* Uygulama KIMLIĞI (Istemci KIMLIĞI) (örneğin, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="e1e68-130">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="e1e68-131">Uygulama kimliği URI 'si (örneğin `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`,, veya belirttiğiniz özel değer)</span><span class="sxs-lookup"><span data-stu-id="e1e68-131">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="e1e68-132">Dizin KIMLIĞI (kiracı KIMLIĞI) (örneğin, `222222222-2222-2222-2222-222222222222`)</span><span class="sxs-lookup"><span data-stu-id="e1e68-132">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="e1e68-133">*Sunucu API 'si uygulaması* Uygulama KIMLIĞI URI 'SI (örneğin `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, Azure Portal istemci kimliği için varsayılan değer olarak değişebilir)</span><span class="sxs-lookup"><span data-stu-id="e1e68-133">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="e1e68-134">Varsayılan kapsam (örneğin, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="e1e68-134">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="e1e68-135">İstemci uygulamasını kaydetme</span><span class="sxs-lookup"><span data-stu-id="e1e68-135">Register a client app</span></span>

<span data-ttu-id="e1e68-136">Eğitim bölümünde yer alan yönergeleri izleyin: Azure Portal **Azure Active Directory** > **uygulama kayıtları** alanına *istemci uygulaması* için AAD uygulaması kaydetmek üzere [bir uygulamayı yeniden Azure Active Directory B2C kaydedin](/azure/active-directory-b2c/tutorial-register-applications) :</span><span class="sxs-lookup"><span data-stu-id="e1e68-136">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="e1e68-137">**Yeni kayıt**seçeneğini belirleyin.</span><span class="sxs-lookup"><span data-stu-id="e1e68-137">Select **New registration**.</span></span>
1. <span data-ttu-id="e1e68-138">Uygulama için bir **ad** sağlayın (örneğin, \*\* Blazor istemci AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="e1e68-138">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="e1e68-139">**Desteklenen hesap türleri**için **herhangi bir kuruluş dizininde veya herhangi bir kimlik sağlayıcısında hesaplar ' ı seçin. Azure AD B2C kullanıcıları kimlik doğrulaması için.**</span><span class="sxs-lookup"><span data-stu-id="e1e68-139">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="e1e68-140">Bu deneyim için (çok kiracılı).</span><span class="sxs-lookup"><span data-stu-id="e1e68-140">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="e1e68-141">**Yeniden yönlendirme URI 'si** açılan listesini `https://localhost:5001/authentication/login-callback` **Web**olarak ayarlayın ve bir yeniden yönlendirme URI 'si sağlayın.</span><span class="sxs-lookup"><span data-stu-id="e1e68-141">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="e1e68-142">**İzinlerin** > ,**OpenID 'ye yönetici tarafından verildiğini ve offline_access izinleri** olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="e1e68-142">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="e1e68-143">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="e1e68-143">Select **Register**.</span></span>

<span data-ttu-id="e1e68-144">**Kimlik doğrulama** > **platformu yapılandırması** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="e1e68-144">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="e1e68-145">**Yeniden YÖNLENDIRME URI** 'sinin mevcut `https://localhost:5001/authentication/login-callback` olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="e1e68-145">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="e1e68-146">**Örtük izin**Için, **erişim belirteçleri** ve **Kimlik belirteçleri**onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="e1e68-146">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="e1e68-147">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="e1e68-147">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="e1e68-148">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="e1e68-148">Select the **Save** button.</span></span>

<span data-ttu-id="e1e68-149">**API izinleri**:</span><span class="sxs-lookup"><span data-stu-id="e1e68-149">In **API permissions**:</span></span>

1. <span data-ttu-id="e1e68-150">Uygulamanın **Microsoft Graph** > **User. Read** iznine sahip olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="e1e68-150">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="e1e68-151">**Izin Ekle** ' yi ve ardından **API 'lerim**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="e1e68-151">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="e1e68-152">**Ad** sütunundan *sunucu API uygulamasını* seçin (örneğin, \*\* Blazor sunucu AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="e1e68-152">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="e1e68-153">**API** listesini açın.</span><span class="sxs-lookup"><span data-stu-id="e1e68-153">Open the **API** list.</span></span>
1. <span data-ttu-id="e1e68-154">API 'ye erişimi etkinleştirin (örneğin, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="e1e68-154">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="e1e68-155">**Izin Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="e1e68-155">Select **Add permissions**.</span></span>
1. <span data-ttu-id="e1e68-156">**{Tenant Name} için yönetici Içeriği ver** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="e1e68-156">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="e1e68-157">Onaylamak için **Evet**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="e1e68-157">Select **Yes** to confirm.</span></span>

<span data-ttu-id="e1e68-158">**Giriş** > **Azure AD B2C**Azure AD B2C > **Kullanıcı akışları**' nda:</span><span class="sxs-lookup"><span data-stu-id="e1e68-158">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="e1e68-159">Kaydolma ve oturum açma Kullanıcı akışı oluşturma</span><span class="sxs-lookup"><span data-stu-id="e1e68-159">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="e1e68-160">En azından `context.User.Identity.Name` , `LoginDisplay` bileşende (*paylaşılan/logindisplay. Razor*) doldurmak için **uygulama talepleri** > **görünen adı** Kullanıcı özniteliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="e1e68-160">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="e1e68-161">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="e1e68-161">Record the following information:</span></span>

* <span data-ttu-id="e1e68-162">*İstemci* UYGULAMASı uygulama kimliğini (istemci kimliği) kaydedin (örneğin, `33333333-3333-3333-3333-333333333333`).</span><span class="sxs-lookup"><span data-stu-id="e1e68-162">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="e1e68-163">Uygulama için oluşturulan kaydolma ve oturum açma Kullanıcı akış adını kaydedin (örneğin, `B2C_1_signupsignin`).</span><span class="sxs-lookup"><span data-stu-id="e1e68-163">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="e1e68-164">Uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="e1e68-164">Create the app</span></span>

<span data-ttu-id="e1e68-165">Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:</span><span class="sxs-lookup"><span data-stu-id="e1e68-165">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="e1e68-166">Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample`).</span><span class="sxs-lookup"><span data-stu-id="e1e68-166">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="e1e68-167">Klasör adı Ayrıca projenin adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="e1e68-167">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="e1e68-168">Uygulama KIMLIĞI URI 'sini `app-id-uri` seçeneğe geçirin, ancak [erişim belirteci kapsamları](#access-token-scopes) bölümünde açıklanan istemci uygulamasında bir yapılandırma değişikliği gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="e1e68-168">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="e1e68-169">Sunucu uygulaması yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e1e68-169">Server app configuration</span></span>

<span data-ttu-id="e1e68-170">*Bu bölüm, çözümün **sunucu** uygulamasıyla ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="e1e68-170">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="e1e68-171">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="e1e68-171">Authentication package</span></span>

<span data-ttu-id="e1e68-172">ASP.NET Core Web API 'Lerine yönelik kimlik doğrulama ve yetkilendirme desteği şu şekilde sağlanır `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span><span class="sxs-lookup"><span data-stu-id="e1e68-172">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="e1e68-173">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="e1e68-173">Authentication service support</span></span>

<span data-ttu-id="e1e68-174">`AddAuthentication` Yöntemi, uygulama içinde kimlik doğrulama hizmetlerini ayarlar ve JWT taşıyıcı işleyicisini varsayılan kimlik doğrulama yöntemi olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="e1e68-174">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="e1e68-175">`AddAzureADB2CBearer` Yöntemi, Azure Active Directory B2C tarafından yayılan belirteçleri doğrulamak IÇIN gereken JWT taşıyıcı işleyicisinde belirli parametreleri ayarlar:</span><span class="sxs-lookup"><span data-stu-id="e1e68-175">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="e1e68-176">`UseAuthentication`aşağıdakileri `UseAuthorization` doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="e1e68-176">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="e1e68-177">Uygulama, gelen isteklerde belirteçleri ayrıştırmaya ve doğrulamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="e1e68-177">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="e1e68-178">Uygun kimlik bilgileri olmadan korunan kaynağa erişmeye çalışan istekler başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="e1e68-178">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="e1e68-179">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="e1e68-179">User.Identity.Name</span></span>

<span data-ttu-id="e1e68-180">Varsayılan `User.Identity.Name` olarak, doldurulmaz.</span><span class="sxs-lookup"><span data-stu-id="e1e68-180">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="e1e68-181">Uygulamayı `name` talep türünden değeri alacak şekilde yapılandırmak için, <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> içindeki `Startup.ConfigureServices` [tokenvalidationparameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) ' ı yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="e1e68-181">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="e1e68-182">Uygulama ayarları</span><span class="sxs-lookup"><span data-stu-id="e1e68-182">App settings</span></span>

<span data-ttu-id="e1e68-183">*AppSettings. JSON* dosyası, erişim belirteçlerini doğrulamak IÇIN kullanılan JWT taşıyıcı işleyicisini yapılandırma seçeneklerini içerir.</span><span class="sxs-lookup"><span data-stu-id="e1e68-183">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a><span data-ttu-id="e1e68-184">Hava tahmin denetleyicisi</span><span class="sxs-lookup"><span data-stu-id="e1e68-184">WeatherForecast controller</span></span>

<span data-ttu-id="e1e68-185">Dalgalı tahmin denetleyicisi (*denetleyiciler/dalgalı Therforetcontroller. cs*), BIR korumalı API `[Authorize]` 'yi denetleyiciye uygulanmış şekilde gösterir.</span><span class="sxs-lookup"><span data-stu-id="e1e68-185">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="e1e68-186">Bunun anlaşılması **önemlidir** :</span><span class="sxs-lookup"><span data-stu-id="e1e68-186">It's **important** to understand that:</span></span>

* <span data-ttu-id="e1e68-187">Bu `[Authorize]` API denetleyicisindeki özniteliği, bu API 'yi yetkisiz erişime karşı koruyan tek şeydir.</span><span class="sxs-lookup"><span data-stu-id="e1e68-187">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="e1e68-188">Blazor Webassembly uygulamasında kullanılan `[Authorize]` özniteliği yalnızca uygulamanın, uygulamanın düzgün şekilde çalışması için yetkilendirilmiş olması gerektiğine yönelik bir ipucu görevi görür.</span><span class="sxs-lookup"><span data-stu-id="e1e68-188">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="e1e68-189">İstemci uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e1e68-189">Client app configuration</span></span>

<span data-ttu-id="e1e68-190">*Bu bölüm, çözümün **istemci** uygulaması ile ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="e1e68-190">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="e1e68-191">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="e1e68-191">Authentication package</span></span>

<span data-ttu-id="e1e68-192">Tek bir B2C hesabı (`IndividualB2C`) kullanmak üzere bir uygulama oluşturulduğunda, uygulama otomatik olarak [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`) için bir paket başvurusu alır.</span><span class="sxs-lookup"><span data-stu-id="e1e68-192">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="e1e68-193">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="e1e68-193">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="e1e68-194">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e1e68-194">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="e1e68-195">Yukarıdaki `{VERSION}` paket başvurusunda, `Microsoft.AspNetCore.Blazor.Templates` <xref:blazor/get-started> makalede gösterilen paketin sürümüyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="e1e68-195">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="e1e68-196">`Microsoft.Authentication.WebAssembly.Msal` Paket geçişli olarak uygulamayı uygulamaya ekler `Microsoft.AspNetCore.Components.WebAssembly.Authentication` .</span><span class="sxs-lookup"><span data-stu-id="e1e68-196">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="e1e68-197">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="e1e68-197">Authentication service support</span></span>

<span data-ttu-id="e1e68-198">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal` paket tarafından sağlanmış uzantı yöntemiyle kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="e1e68-198">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="e1e68-199">Bu yöntem, uygulamanın kimlik sağlayıcısıyla (IP) etkileşim kurması için gereken tüm hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="e1e68-199">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="e1e68-200">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e1e68-200">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="e1e68-201">Yöntemi `AddMsalAuthentication` , bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="e1e68-201">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="e1e68-202">Uygulamanın yapılandırılması için gereken değerler, uygulamayı kaydettiğinizde Azure Portal AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="e1e68-202">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="e1e68-203">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="e1e68-203">Access token scopes</span></span>

<span data-ttu-id="e1e68-204">Varsayılan erişim belirteci kapsamları, erişim belirteci kapsamlarının listesini temsil eder:</span><span class="sxs-lookup"><span data-stu-id="e1e68-204">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="e1e68-205">Oturum açma isteğine varsayılan olarak dahildir.</span><span class="sxs-lookup"><span data-stu-id="e1e68-205">Included by default in the sign in request.</span></span>
* <span data-ttu-id="e1e68-206">Kimlik doğrulamasından hemen sonra bir erişim belirteci sağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e1e68-206">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="e1e68-207">Tüm kapsamlar Azure Active Directory kuralları başına aynı uygulamaya ait olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="e1e68-207">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="e1e68-208">Gerektiğinde ek API uygulamaları için ek kapsamlar eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="e1e68-208">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="e1e68-209">Azure portal bir kapsam URI 'SI sağlıyorsa ve uygulama API 'den *401 Yetkisiz* bir yanıt aldığında **işlenmeyen bir özel durum oluşturursa** , düzeni ve Konağı içermeyen bir kapsam URI 'si kullanmayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="e1e68-209">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="e1e68-210">Örneğin, Azure portal aşağıdaki kapsam URI biçimlerinden birini verebilir:</span><span class="sxs-lookup"><span data-stu-id="e1e68-210">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="e1e68-211">Kapsam URI 'sini düzen ve ana bilgisayar olmadan sağlayın:</span><span class="sxs-lookup"><span data-stu-id="e1e68-211">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="e1e68-212">Daha fazla bilgi için bkz. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="e1e68-212">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

### <a name="imports-file"></a><span data-ttu-id="e1e68-213">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="e1e68-213">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="e1e68-214">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="e1e68-214">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="e1e68-215">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="e1e68-215">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="e1e68-216">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="e1e68-216">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="e1e68-217">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="e1e68-217">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="e1e68-218">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="e1e68-218">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="e1e68-219">FetchData bileşeni</span><span class="sxs-lookup"><span data-stu-id="e1e68-219">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="e1e68-220">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="e1e68-220">Run the app</span></span>

<span data-ttu-id="e1e68-221">Uygulamayı sunucu projesinden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="e1e68-221">Run the app from the Server project.</span></span> <span data-ttu-id="e1e68-222">Visual Studio 'Yu kullanırken **Çözüm Gezgini** ' de sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya uygulamayı **Hata Ayıkla** menüsünden başlatın.</span><span class="sxs-lookup"><span data-stu-id="e1e68-222">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="e1e68-223">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e1e68-223">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="e1e68-224">Öğretici: Azure Active Directory B2C kiracısı oluşturma</span><span class="sxs-lookup"><span data-stu-id="e1e68-224">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="e1e68-225">Microsoft kimlik platformu belgeleri</span><span class="sxs-lookup"><span data-stu-id="e1e68-225">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
