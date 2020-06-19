---
title: Azure Active Directory B2C bir ASP.NET Core Blazor weelsembly barındırılan uygulamasının güvenliğini sağlama
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 22e9af6ed0a6215e881ed733aa0ba3ad8c6cc78e
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103982"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="88271-102">Azure Active Directory B2C bir ASP.NET Core Blazor weelsembly barındırılan uygulamasının güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="88271-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="88271-103">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="88271-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="88271-104">Bu makalede Blazor kimlik doğrulaması için [Azure ACTIVE DIRECTORY (AAD) B2C](/azure/active-directory-b2c/overview) kullanan bir webassembly tek başına uygulamasının nasıl oluşturulacağı açıklanır.</span><span class="sxs-lookup"><span data-stu-id="88271-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="88271-105">Uygulamaları AAD B2C kaydetme ve çözüm oluşturma</span><span class="sxs-lookup"><span data-stu-id="88271-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="88271-106">Kiracı oluşturma</span><span class="sxs-lookup"><span data-stu-id="88271-106">Create a tenant</span></span>

<span data-ttu-id="88271-107">Öğretici: AAD B2C kiracı oluşturmak için [Azure Active Directory B2C kiracı oluşturma](/azure/active-directory-b2c/tutorial-create-tenant) ' daki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="88271-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="88271-108">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="88271-108">Record the following information:</span></span>

* <span data-ttu-id="88271-109">AAD B2C örneği (örneğin, `https://contoso.b2clogin.com/` sonunda eğik çizgi içeren)</span><span class="sxs-lookup"><span data-stu-id="88271-109">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="88271-110">AAD B2C kiracı etki alanı (örneğin, `contoso.onmicrosoft.com` )</span><span class="sxs-lookup"><span data-stu-id="88271-110">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="88271-111">Sunucu API 'SI uygulaması kaydetme</span><span class="sxs-lookup"><span data-stu-id="88271-111">Register a server API app</span></span>

<span data-ttu-id="88271-112">Eğitim bölümündeki yönergeleri izleyin: *sunucu API uygulaması* IÇIN bir AAD uygulaması kaydetmek üzere [Azure Active Directory B2C bir uygulamayı kaydetme](/azure/active-directory-b2c/tutorial-register-applications) ve ardından aşağıdakileri yapın:</span><span class="sxs-lookup"><span data-stu-id="88271-112">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="88271-113">**Azure Active Directory**  >  **uygulama kayıtları** **Yeni kayıt**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="88271-113">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="88271-114">Uygulama için bir **ad** sağlayın (örneğin, \*\* Blazor sunucu AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="88271-114">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="88271-115">**Desteklenen hesap türleri**için, birden çok kiracılı seçeneği seçin: **herhangi bir kuruluş dizinindeki hesaplar veya herhangi bir kimlik sağlayıcısı. Azure AD B2C kullanıcıları kimlik doğrulaması için.**</span><span class="sxs-lookup"><span data-stu-id="88271-115">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="88271-116">*Sunucu API 'si uygulaması* Bu senaryoda **yeniden yönlendirme URI 'si** gerektirmez, bu nedenle açılan kutudan **Web** 'e ve yeniden yönlendirme URI 'si girmeyin.</span><span class="sxs-lookup"><span data-stu-id="88271-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="88271-117">**İzinlerin**  >  **OpenID 'ye yönetici onayı verdiğini ve offline_access izinlerinin** etkin olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="88271-117">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="88271-118">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="88271-118">Select **Register**.</span></span>

<span data-ttu-id="88271-119">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="88271-119">Record the following information:</span></span>

* <span data-ttu-id="88271-120">*Sunucu API 'si uygulaması* Uygulama KIMLIĞI (Istemci KIMLIĞI) (örneğin, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="88271-120">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="88271-121">Dizin KIMLIĞI (kiracı KIMLIĞI) (örneğin, `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="88271-121">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="88271-122">AAD kiracı etki alanı (örneğin, `contoso.onmicrosoft.com` ): etki alanı, kayıtlı uygulama için Azure Portal **marka** dikey penceresinde **Yayımcı etki alanı** olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="88271-122">AAD Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="88271-123">**API 'Yi kullanıma**sunma bölümünde:</span><span class="sxs-lookup"><span data-stu-id="88271-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="88271-124">**Kapsam ekle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="88271-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="88271-125">**Kaydet ve devam et**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="88271-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="88271-126">Bir **kapsam adı** sağlayın (örneğin, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="88271-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="88271-127">**Yönetici izni görünen adı** sağlayın (örneğin, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="88271-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="88271-128">**Yönetici onay açıklaması** sağlayın (örneğin, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="88271-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="88271-129">**Durumun** **etkin**olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="88271-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="88271-130">**Kapsam Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="88271-130">Select **Add scope**.</span></span>

<span data-ttu-id="88271-131">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="88271-131">Record the following information:</span></span>

* <span data-ttu-id="88271-132">Uygulama KIMLIĞI URI 'SI (örneğin, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` , `api://11111111-1111-1111-1111-111111111111` veya belirttiğiniz özel değer)</span><span class="sxs-lookup"><span data-stu-id="88271-132">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="88271-133">Varsayılan kapsam (örneğin, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="88271-133">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="88271-134">İstemci uygulamasını kaydetme</span><span class="sxs-lookup"><span data-stu-id="88271-134">Register a client app</span></span>

<span data-ttu-id="88271-135">Eğitim bölümündeki yönergeleri izleyin: *istemci uygulaması* için AAD uygulamasını kaydetmek üzere [Azure Active Directory B2C bir uygulamayı yeniden kaydedin](/azure/active-directory-b2c/tutorial-register-applications) ve ardından aşağıdakileri yapın:</span><span class="sxs-lookup"><span data-stu-id="88271-135">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* and then do the following:</span></span>

1. <span data-ttu-id="88271-136">**Azure Active Directory**  >  **uygulama kayıtları** **Yeni kayıt**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="88271-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="88271-137">Uygulama için bir **ad** sağlayın (örneğin, \*\* Blazor istemci AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="88271-137">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="88271-138">**Desteklenen hesap türleri**için, birden çok kiracılı seçeneği seçin: **herhangi bir kuruluş dizinindeki hesaplar veya herhangi bir kimlik sağlayıcısı. Azure AD B2C kullanıcıları kimlik doğrulaması için.**</span><span class="sxs-lookup"><span data-stu-id="88271-138">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="88271-139">**Yeniden yönlendirme URI 'si** açılan öğesini **Web** 'e ayarlı bırakın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="88271-139">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="88271-140">Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir.</span><span class="sxs-lookup"><span data-stu-id="88271-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="88271-141">Uygulama farklı bir Kestrel bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktasını kullanın.</span><span class="sxs-lookup"><span data-stu-id="88271-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="88271-142">IIS Express için, uygulama için rastgele oluşturulan bağlantı noktası, **hata ayıklama** panelinde sunucu uygulamasının özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="88271-142">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="88271-143">Uygulama bu noktada mevcut olmadığından ve IIS Express bağlantı noktası bilinmediğinden, uygulama oluşturulduktan sonra bu adıma geri dönün ve yeniden yönlendirme URI 'sini güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="88271-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="88271-144">[Uygulama oluştur](#create-the-app) bölümünde, kullanıcıların YENIDEN yönlendirme URI 'sini güncelleştirmesi IIS Express hatırlatmak için bir açıklama belirir.</span><span class="sxs-lookup"><span data-stu-id="88271-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="88271-145">**İzinlerin**  >  **OpenID 'ye yönetici onayı verdiğini ve offline_access izinlerinin** etkin olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="88271-145">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="88271-146">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="88271-146">Select **Register**.</span></span>

<span data-ttu-id="88271-147">Uygulama KIMLIĞINI (Istemci KIMLIĞI) kaydedin (örneğin, `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="88271-147">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="88271-148">**Kimlik doğrulama**  >  **platformu yapılandırması**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="88271-148">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="88271-149">**Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="88271-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="88271-150">**Örtük izin**Için, **erişim belirteçleri** ve **Kimlik belirteçleri**onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="88271-150">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="88271-151">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="88271-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="88271-152">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="88271-152">Select the **Save** button.</span></span>

<span data-ttu-id="88271-153">**API izinleri**:</span><span class="sxs-lookup"><span data-stu-id="88271-153">In **API permissions**:</span></span>

1. <span data-ttu-id="88271-154">**Izin Ekle** ' yi ve ardından **API 'lerim**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="88271-154">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="88271-155">**Ad** SÜTUNUNDAN *sunucu API uygulamasını* seçin (örneğin, \*\* Blazor sunucu AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="88271-155">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="88271-156">**API** listesini açın.</span><span class="sxs-lookup"><span data-stu-id="88271-156">Open the **API** list.</span></span>
1. <span data-ttu-id="88271-157">API 'ye erişimi etkinleştirin (örneğin, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="88271-157">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="88271-158">**Izin Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="88271-158">Select **Add permissions**.</span></span>
1. <span data-ttu-id="88271-159">**{Tenant Name} için yönetici Içeriği ver** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="88271-159">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="88271-160">Onaylamak için **Evet**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="88271-160">Select **Yes** to confirm.</span></span>

<span data-ttu-id="88271-161">**Giriş**  >  **Azure AD B2C**  >  **Kullanıcı akışları**' nda:</span><span class="sxs-lookup"><span data-stu-id="88271-161">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="88271-162">Kaydolma ve oturum açma Kullanıcı akışı oluşturma</span><span class="sxs-lookup"><span data-stu-id="88271-162">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="88271-163">En azından, **Application claims**  >  **Display Name** `context.User.Identity.Name` `LoginDisplay` bileşende (*paylaşılan/logindisplay. Razor*) doldurmak için uygulama talepleri görünen adı Kullanıcı özniteliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="88271-163">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="88271-164">Uygulama için oluşturulan kaydolma ve oturum açma Kullanıcı akış adını kaydedin (örneğin, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="88271-164">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="88271-165">Uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="88271-165">Create the app</span></span>

<span data-ttu-id="88271-166">Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:</span><span class="sxs-lookup"><span data-stu-id="88271-166">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="88271-167">Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="88271-167">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="88271-168">Klasör adı Ayrıca projenin adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="88271-168">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="88271-169">Uygulama KIMLIĞI URI 'sini `app-id-uri` seçeneğe geçirin, ancak [erişim belirteci kapsamları](#access-token-scopes) bölümünde açıklanan istemci uygulamasında bir yapılandırma değişikliği gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="88271-169">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="88271-170">Ayrıca, barındırılan şablon tarafından ayarlanan kapsamda Blazor uygulama KIMLIĞI URI ana bilgisayarı yineleniyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="88271-170">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="88271-171">Koleksiyon için yapılandırılan kapsamın `DefaultAccessTokenScopes` `Program.Main` *istemci uygulamasının*(*program.cs*) içinde doğru olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="88271-171">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (*Program.cs*) of the *Client app*.</span></span>

> [!NOTE]
> <span data-ttu-id="88271-172">Azure Portal, *istemci uygulamasının* **kimlik doğrulama**  >  **platformu yapılandırması**  >  **Web**  >  **yeniden yönlendirme URI 'si** , Kestrel sunucusunda varsayılan ayarlarla çalışan uygulamalar için bağlantı noktası 5001 için yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="88271-172">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="88271-173">*İstemci uygulaması* rastgele bir IIS Express bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktası **hata ayıklama** panelinde *sunucu uygulamasının* özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="88271-173">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="88271-174">Bağlantı noktası, *istemci uygulamasının* bilinen bağlantı noktasıyla daha önce yapılandırılmamışsa, Azure Portal *istemci uygulamanın* kaydına dönün ve yeniden yönlendirme URI 'sini doğru bağlantı noktasıyla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="88271-174">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="88271-175">Sunucu uygulaması yapılandırması</span><span class="sxs-lookup"><span data-stu-id="88271-175">Server app configuration</span></span>

<span data-ttu-id="88271-176">*Bu bölüm, çözümün **sunucu** uygulamasıyla ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="88271-176">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="88271-177">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="88271-177">Authentication package</span></span>

<span data-ttu-id="88271-178">ASP.NET Core Web API 'Lerine yönelik kimlik doğrulama ve yetkilendirme desteği [Microsoft. AspNetCore. Authentication. AzureADB2C. UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) paketi tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="88271-178">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [Microsoft.AspNetCore.Authentication.AzureADB2C.UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI/) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="88271-179">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="88271-179">Authentication service support</span></span>

<span data-ttu-id="88271-180">`AddAuthentication`Yöntemi, uygulama içinde kimlik doğrulama hizmetlerini ayarlar ve JWT taşıyıcı işleyicisini varsayılan kimlik doğrulama yöntemi olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="88271-180">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="88271-181"><xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A>Yöntemi, Azure Active Directory B2C tarafından yayılan belirteçleri doğrulamak için gereken JWT taşıyıcı işleyicisinde belirli parametreleri ayarlar:</span><span class="sxs-lookup"><span data-stu-id="88271-181">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="88271-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A><xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>aşağıdakileri doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="88271-182"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="88271-183">Uygulama, gelen isteklerde belirteçleri ayrıştırmaya ve doğrulamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="88271-183">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="88271-184">Uygun kimlik bilgileri olmadan korunan kaynağa erişmeye çalışan istekler başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="88271-184">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="88271-185">Kullanıcı. Identity . Ada</span><span class="sxs-lookup"><span data-stu-id="88271-185">User.Identity.Name</span></span>

<span data-ttu-id="88271-186">Varsayılan olarak, `User.Identity.Name` doldurulmaz.</span><span class="sxs-lookup"><span data-stu-id="88271-186">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="88271-187">Uygulamayı talep türünden değeri alacak şekilde yapılandırmak için, `name` Içindeki [Tokenvalidationparameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) ' ı yapılandırın <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="88271-187">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="88271-188">Uygulama ayarları</span><span class="sxs-lookup"><span data-stu-id="88271-188">App settings</span></span>

<span data-ttu-id="88271-189">Dosyadaki *appsettings.js* , erişim belirteçlerini doğrulamak IÇIN kullanılan JWT taşıyıcı işleyicisini yapılandırma seçeneklerini içerir.</span><span class="sxs-lookup"><span data-stu-id="88271-189">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

<span data-ttu-id="88271-190">Örnek:</span><span class="sxs-lookup"><span data-stu-id="88271-190">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="88271-191">Hava tahmin denetleyicisi</span><span class="sxs-lookup"><span data-stu-id="88271-191">WeatherForecast controller</span></span>

<span data-ttu-id="88271-192">Dalgalı tahmin denetleyicisi (*denetleyiciler/dalgalı Therforetcontroller. cs*), BIR korumalı API 'yi [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) denetleyiciye uygulanmış şekilde gösterir.</span><span class="sxs-lookup"><span data-stu-id="88271-192">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="88271-193">Bunun anlaşılması **önemlidir** :</span><span class="sxs-lookup"><span data-stu-id="88271-193">It's **important** to understand that:</span></span>

* <span data-ttu-id="88271-194">Bu [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) API denetleyicisindeki özniteliği, bu API 'yi yetkisiz erişime karşı koruyan tek şeydir.</span><span class="sxs-lookup"><span data-stu-id="88271-194">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="88271-195">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) Blazor Webassembly uygulamasında kullanılan özniteliği yalnızca uygulamanın, uygulamanın düzgün şekilde çalışması için yetkilendirilmiş olması gerektiğine yönelik bir ipucu görevi görür.</span><span class="sxs-lookup"><span data-stu-id="88271-195">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="88271-196">İstemci uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="88271-196">Client app configuration</span></span>

<span data-ttu-id="88271-197">*Bu bölüm, çözümün **istemci** uygulaması ile ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="88271-197">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="88271-198">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="88271-198">Authentication package</span></span>

<span data-ttu-id="88271-199">Tek bir B2C hesabı () kullanmak üzere bir uygulama oluşturulduğunda `IndividualB2C` , uygulama [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) için otomatik olarak bir paket başvurusu alır ([Microsoft. Authentication. Webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span><span class="sxs-lookup"><span data-stu-id="88271-199">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)).</span></span> <span data-ttu-id="88271-200">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="88271-200">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="88271-201">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="88271-201">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="88271-202">[Microsoft. Authentication. WebAssembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) paketi, [Microsoft. Aspnetcore. components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) paketini uygulamaya göre geçişli olarak ekler.</span><span class="sxs-lookup"><span data-stu-id="88271-202">The [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package transitively adds the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="88271-203">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="88271-203">Authentication service support</span></span>

<span data-ttu-id="88271-204"><xref:System.Net.Http.HttpClient>Sunucu projesine istek yaparken erişim belirteçlerini içeren örnekler için destek eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="88271-204">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="88271-205">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="88271-205">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="88271-206">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="88271-206">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="88271-207">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısına <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> [Microsoft. Authentication. WebAssembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) paketi tarafından sağlanmış uzantı yöntemiyle kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="88271-207">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [Microsoft.Authentication.WebAssembly.Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) package.</span></span> <span data-ttu-id="88271-208">Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="88271-208">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="88271-209">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="88271-209">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="88271-210"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Yöntemi, bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="88271-210">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="88271-211">Uygulamanın yapılandırılması için gereken değerler, uygulamayı kaydettiğinizde Azure Portal AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="88271-211">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="88271-212">Yapılandırma, dosya *üzerinde Wwwroot/appsettings.js* tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="88271-212">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="88271-213">Örnek:</span><span class="sxs-lookup"><span data-stu-id="88271-213">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="88271-214">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="88271-214">Access token scopes</span></span>

<span data-ttu-id="88271-215">Varsayılan erişim belirteci kapsamları, erişim belirteci kapsamlarının listesini temsil eder:</span><span class="sxs-lookup"><span data-stu-id="88271-215">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="88271-216">Oturum açma isteğine varsayılan olarak dahildir.</span><span class="sxs-lookup"><span data-stu-id="88271-216">Included by default in the sign in request.</span></span>
* <span data-ttu-id="88271-217">Kimlik doğrulamasından hemen sonra bir erişim belirteci sağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="88271-217">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="88271-218">Tüm kapsamlar Azure Active Directory kuralları başına aynı uygulamaya ait olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="88271-218">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="88271-219">Gerektiğinde ek API uygulamaları için ek kapsamlar eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="88271-219">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="88271-220">Daha fazla bilgi için *ek senaryolar* makalesinin aşağıdaki bölümlerine bakın:</span><span class="sxs-lookup"><span data-stu-id="88271-220">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="88271-221">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="88271-221">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="88271-222">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="88271-222">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="88271-223">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="88271-223">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="88271-224">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="88271-224">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="88271-225">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="88271-225">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="88271-226">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="88271-226">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="88271-227">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="88271-227">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="88271-228">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="88271-228">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="88271-229">FetchData bileşeni</span><span class="sxs-lookup"><span data-stu-id="88271-229">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="88271-230">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="88271-230">Run the app</span></span>

<span data-ttu-id="88271-231">Uygulamayı sunucu projesinden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="88271-231">Run the app from the Server project.</span></span> <span data-ttu-id="88271-232">Visual Studio 'yu kullanırken şunlardan birini yapın:</span><span class="sxs-lookup"><span data-stu-id="88271-232">When using Visual Studio, either:</span></span>

* <span data-ttu-id="88271-233">Araç çubuğundaki **başlangıç projeleri** açılan listesini *sunucu API 'si uygulamasına* ayarlayın ve **Çalıştır** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="88271-233">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="88271-234">**Çözüm Gezgini** ' de sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya uygulamayı **Hata Ayıkla** menüsünden başlatın.</span><span class="sxs-lookup"><span data-stu-id="88271-234">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="88271-235">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="88271-235">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="88271-236">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="88271-236">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="88271-237">Öğretici: Azure Active Directory B2C kiracısı oluşturma</span><span class="sxs-lookup"><span data-stu-id="88271-237">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="88271-238">Microsoft kimlik platformu belgeleri</span><span class="sxs-lookup"><span data-stu-id="88271-238">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
