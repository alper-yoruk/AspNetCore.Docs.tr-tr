---
title: Blazor WebAssemblyAzure Active Directory B2C ile ASP.NET Core barındırılan bir uygulamanın güvenliğini sağlama
author: guardrex
description: ASP.NET Core barındırılan bir uygulamanın güvenliğini Azure Active Directory B2C ile nasıl sağlayacağınızı öğrenin Blazor WebAssembly .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/26/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 1c87330dec069e05f274206d2d35f50f489f9623
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710626"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="bfab6-103">Blazor WebAssemblyAzure Active Directory B2C ile ASP.NET Core barındırılan bir uygulamanın güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="bfab6-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="bfab6-104">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="bfab6-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="bfab6-105">Bu makalede kimlik doğrulaması için [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) kullanan bir [barındırılan Blazor WebAssembly uygulamanın](xref:blazor/hosting-models#blazor-webassembly) nasıl oluşturulacağı açıklanır.</span><span class="sxs-lookup"><span data-stu-id="bfab6-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="bfab6-106">Uygulamaları AAD B2C kaydetme ve çözüm oluşturma</span><span class="sxs-lookup"><span data-stu-id="bfab6-106">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="bfab6-107">Kiracı oluşturma</span><span class="sxs-lookup"><span data-stu-id="bfab6-107">Create a tenant</span></span>

<span data-ttu-id="bfab6-108">Öğretici: AAD B2C kiracı oluşturmak için [Azure Active Directory B2C kiracı oluşturma](/azure/active-directory-b2c/tutorial-create-tenant) ' daki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-108">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span> <span data-ttu-id="bfab6-109">Kullanmak üzere bir kiracı oluşturduktan veya tanımladıktan hemen sonra bu makaleye geri dönün.</span><span class="sxs-lookup"><span data-stu-id="bfab6-109">Return to this article immediately after creating or identifying a tenant to use.</span></span>

<span data-ttu-id="bfab6-110">AAD B2C örneğini kaydedin (örneğin, `https://contoso.b2clogin.com/` sonunda eğik çizgi içeren).</span><span class="sxs-lookup"><span data-stu-id="bfab6-110">Record the AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span> <span data-ttu-id="bfab6-111">Örnek, Azure portal **uygulama kayıtları** sayfasından **uç noktalar** penceresi AÇıLARAK bulunan bir Azure B2C uygulama kaydının şeması ve barındırmadır.</span><span class="sxs-lookup"><span data-stu-id="bfab6-111">The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="bfab6-112">Sunucu API 'SI uygulaması kaydetme</span><span class="sxs-lookup"><span data-stu-id="bfab6-112">Register a server API app</span></span>

<span data-ttu-id="bfab6-113">*Sunucu API uygulaması* için bir AAD B2C uygulaması kaydetme:</span><span class="sxs-lookup"><span data-stu-id="bfab6-113">Register an AAD B2C app for the *Server API app*:</span></span>

1. <span data-ttu-id="bfab6-114">**Azure Active Directory**  >  **uygulama kayıtları** **Yeni kayıt**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-114">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="bfab6-115">Uygulama için bir **ad** sağlayın (örneğin, **Blazor Server AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="bfab6-115">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="bfab6-116">**Desteklenen hesap türleri** için, çok kiracılı seçeneğini belirleyin: **herhangi bir kimlik sağlayıcısındaki veya kuruluş dizinindeki hesaplar (Kullanıcı akışları olan kullanıcıların kimliğini doğrulamak için)**</span><span class="sxs-lookup"><span data-stu-id="bfab6-116">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="bfab6-117">*Sunucu API 'si uygulaması* Bu senaryoda **yeniden yönlendirme URI 'si** gerektirmez, bu nedenle açılan kutudan **Web** 'e ve yeniden yönlendirme URI 'si girmeyin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-117">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="bfab6-118">**İzinlerin**  >  **OpenID 'ye yönetici onayı verdiğini ve offline_access izinlerinin** seçili olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="bfab6-118">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="bfab6-119">**Kaydet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-119">Select **Register**.</span></span>

<span data-ttu-id="bfab6-120">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="bfab6-120">Record the following information:</span></span>

* <span data-ttu-id="bfab6-121">*Sunucu API 'si uygulaması* Uygulama (istemci) KIMLIĞI (örneğin, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="bfab6-121">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="bfab6-122">AAD birincil/yayımcı/kiracı etki alanı (örneğin, `contoso.onmicrosoft.com` ): etki alanı, kayıtlı uygulama için Azure Portal **marka** dikey penceresinde **Yayımcı etki alanı** olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-122">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="bfab6-123">**API 'Yi kullanıma** sunma bölümünde:</span><span class="sxs-lookup"><span data-stu-id="bfab6-123">In **Expose an API**:</span></span>

1. <span data-ttu-id="bfab6-124">**Kapsam ekle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-124">Select **Add a scope**.</span></span>
1. <span data-ttu-id="bfab6-125">**Kaydet ve devam et**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-125">Select **Save and continue**.</span></span>
1. <span data-ttu-id="bfab6-126">Bir **kapsam adı** sağlayın (örneğin, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="bfab6-126">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="bfab6-127">**Yönetici izni görünen adı** sağlayın (örneğin, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="bfab6-127">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="bfab6-128">**Yönetici onay açıklaması** sağlayın (örneğin, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="bfab6-128">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="bfab6-129">**Durumun** **etkin** olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="bfab6-129">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="bfab6-130">**Kapsam Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-130">Select **Add scope**.</span></span>

<span data-ttu-id="bfab6-131">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="bfab6-131">Record the following information:</span></span>

* <span data-ttu-id="bfab6-132">Uygulama KIMLIĞI URI 'SI (örneğin, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` , `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` veya belirttiğiniz özel değer)</span><span class="sxs-lookup"><span data-stu-id="bfab6-132">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="bfab6-133">Kapsam adı (örneğin, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="bfab6-133">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="bfab6-134">İstemci uygulamasını kaydetme</span><span class="sxs-lookup"><span data-stu-id="bfab6-134">Register a client app</span></span>

<span data-ttu-id="bfab6-135">*İstemci uygulaması* için bir AAD B2C uygulaması kaydetme:</span><span class="sxs-lookup"><span data-stu-id="bfab6-135">Register an AAD B2C app for the *Client app*:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="bfab6-136">**Azure Active Directory** > **uygulama kayıtları** **Yeni kayıt**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-136">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="bfab6-137">Uygulama için bir **ad** sağlayın (örneğin, **Blazor istemci AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="bfab6-137">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="bfab6-138">**Desteklenen hesap türleri** için, çok kiracılı seçeneğini belirleyin: **herhangi bir kimlik sağlayıcısındaki veya kuruluş dizinindeki hesaplar (Kullanıcı akışları olan kullanıcıların kimliğini doğrulamak için)**</span><span class="sxs-lookup"><span data-stu-id="bfab6-138">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="bfab6-139">**Yeniden yönlendirme URI 'si** açılan öğesini **tek SAYFALı uygulama (Spa)** olarak ayarlayın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="bfab6-139">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="bfab6-140">Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-140">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="bfab6-141">Uygulama farklı bir Kestrel bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktasını kullanın.</span><span class="sxs-lookup"><span data-stu-id="bfab6-141">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="bfab6-142">IIS Express için, uygulama için rastgele oluşturulan bağlantı noktası, *`Server`* **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-142">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="bfab6-143">Uygulama bu noktada mevcut olmadığından ve IIS Express bağlantı noktası bilinmediğinden, uygulama oluşturulduktan sonra bu adıma geri dönün ve yeniden yönlendirme URI 'sini güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-143">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="bfab6-144">[Uygulama oluştur](#create-the-app) bölümünde, kullanıcıların YENIDEN yönlendirme URI 'sini güncelleştirmesi IIS Express hatırlatmak için bir açıklama belirir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-144">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="bfab6-145">**İzinlerin** > **OpenID 'ye yönetici onayı verdiğini ve offline_access izinlerinin** seçili olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="bfab6-145">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="bfab6-146">**Kaydet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-146">Select **Register**.</span></span>

1. <span data-ttu-id="bfab6-147">Uygulama (istemci) KIMLIĞINI (örneğin, `4369008b-21fa-427c-abaa-9b53bf58e538` ) kaydedin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-147">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="bfab6-148">**Kimlik doğrulama** > **platformu yapılandırmalarında** > **tek sayfalı uygulama (Spa)**:</span><span class="sxs-lookup"><span data-stu-id="bfab6-148">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="bfab6-149">**Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="bfab6-149">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="bfab6-150">**Örtük verme** Için, **erişim belirteçleri** ve **Kimlik belirteçleri** onay kutularının seçili **olmadığından** emin olun.</span><span class="sxs-lookup"><span data-stu-id="bfab6-150">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="bfab6-151">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-151">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="bfab6-152">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-152">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="bfab6-153">**Azure Active Directory** > **uygulama kayıtları** **Yeni kayıt**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-153">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="bfab6-154">Uygulama için bir **ad** sağlayın (örneğin, **Blazor istemci AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="bfab6-154">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="bfab6-155">**Desteklenen hesap türleri** için, çok kiracılı seçeneğini belirleyin: **herhangi bir kimlik sağlayıcısındaki veya kuruluş dizinindeki hesaplar (Kullanıcı akışları olan kullanıcıların kimliğini doğrulamak için)**</span><span class="sxs-lookup"><span data-stu-id="bfab6-155">For **Supported account types**, select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="bfab6-156">**Yeniden yönlendirme URI 'si** açılan öğesini **Web** 'e ayarlı bırakın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="bfab6-156">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="bfab6-157">Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-157">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="bfab6-158">Uygulama farklı bir Kestrel bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktasını kullanın.</span><span class="sxs-lookup"><span data-stu-id="bfab6-158">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="bfab6-159">IIS Express için, uygulama için rastgele oluşturulan bağlantı noktası, *`Server`* **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-159">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="bfab6-160">Uygulama bu noktada mevcut olmadığından ve IIS Express bağlantı noktası bilinmediğinden, uygulama oluşturulduktan sonra bu adıma geri dönün ve yeniden yönlendirme URI 'sini güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-160">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="bfab6-161">[Uygulama oluştur](#create-the-app) bölümünde, kullanıcıların YENIDEN yönlendirme URI 'sini güncelleştirmesi IIS Express hatırlatmak için bir açıklama belirir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-161">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="bfab6-162">**İzinlerin** > **OpenID 'ye yönetici onayı verdiğini ve offline_access izinlerinin** seçili olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="bfab6-162">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="bfab6-163">**Kaydet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-163">Select **Register**.</span></span>

<span data-ttu-id="bfab6-164">Uygulama (istemci) KIMLIĞINI (örneğin, `4369008b-21fa-427c-abaa-9b53bf58e538` ) kaydedin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-164">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="bfab6-165">**Kimlik doğrulama** > **platformu yapılandırması** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="bfab6-165">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="bfab6-166">**Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="bfab6-166">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="bfab6-167">**Örtük izin** Için, **erişim belirteçleri** ve **Kimlik belirteçleri** onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-167">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="bfab6-168">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-168">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="bfab6-169">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-169">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="bfab6-170">**API izinleri**:</span><span class="sxs-lookup"><span data-stu-id="bfab6-170">In **API permissions**:</span></span>

1. <span data-ttu-id="bfab6-171">**Izin Ekle** ' yi ve ardından **API 'lerim**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-171">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="bfab6-172">**Ad** SÜTUNUNDAN *sunucu API uygulamasını* seçin (örneğin, **Blazor Server AAD B2C**).</span><span class="sxs-lookup"><span data-stu-id="bfab6-172">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="bfab6-173">**API** listesini açın.</span><span class="sxs-lookup"><span data-stu-id="bfab6-173">Open the **API** list.</span></span>
1. <span data-ttu-id="bfab6-174">API 'ye erişimi etkinleştirin (örneğin, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="bfab6-174">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="bfab6-175">**Izin Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-175">Select **Add permissions**.</span></span>
1. <span data-ttu-id="bfab6-176">**{Tenant Name} için yönetici onayı Izni ver** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-176">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="bfab6-177">Onaylamak için **Evet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-177">Select **Yes** to confirm.</span></span>

<span data-ttu-id="bfab6-178">**Giriş**  >  **Azure AD B2C**  >  **Kullanıcı akışları**' nda:</span><span class="sxs-lookup"><span data-stu-id="bfab6-178">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="bfab6-179">Kaydolma ve oturum açma Kullanıcı akışı oluşturma</span><span class="sxs-lookup"><span data-stu-id="bfab6-179">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="bfab6-180">En azından,   >   `context.User.Identity.Name` `LoginDisplay` bileşen () içinde doldurmak için uygulama talepleri görünen adı Kullanıcı özniteliğini seçin `Shared/LoginDisplay.razor` .</span><span class="sxs-lookup"><span data-stu-id="bfab6-180">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="bfab6-181">Uygulama için oluşturulan kaydolma ve oturum açma Kullanıcı akış adını kaydedin (örneğin, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="bfab6-181">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="bfab6-182">Uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="bfab6-182">Create the app</span></span>

<span data-ttu-id="bfab6-183">Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:</span><span class="sxs-lookup"><span data-stu-id="bfab6-183">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="bfab6-184">Yer tutucu</span><span class="sxs-lookup"><span data-stu-id="bfab6-184">Placeholder</span></span>                   | <span data-ttu-id="bfab6-185">Azure portal adı</span><span class="sxs-lookup"><span data-stu-id="bfab6-185">Azure portal name</span></span>                                     | <span data-ttu-id="bfab6-186">Örnek</span><span class="sxs-lookup"><span data-stu-id="bfab6-186">Example</span></span>                                        |
| ----------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="bfab6-187">Örnek</span><span class="sxs-lookup"><span data-stu-id="bfab6-187">Instance</span></span>                                              | `https://contoso.b2clogin.com/`                |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`      | <span data-ttu-id="bfab6-188">Uygulamanın uygulama (istemci) KIMLIĞI *`Client`*</span><span class="sxs-lookup"><span data-stu-id="bfab6-188">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`             | <span data-ttu-id="bfab6-189">Kapsam adı</span><span class="sxs-lookup"><span data-stu-id="bfab6-189">Scope name</span></span>                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}`  | <span data-ttu-id="bfab6-190">*Sunucu API uygulaması* için uygulama (ISTEMCI) kimliği</span><span class="sxs-lookup"><span data-stu-id="bfab6-190">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`     | <span data-ttu-id="bfab6-191">Uygulama KIMLIĞI URI 'SI&dagger;</span><span class="sxs-lookup"><span data-stu-id="bfab6-191">Application ID URI&dagger;</span></span>                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="bfab6-192">Kaydolma/oturum açma Kullanıcı akışı</span><span class="sxs-lookup"><span data-stu-id="bfab6-192">Sign-up/sign-in user flow</span></span>                             | `B2C_1_signupsignin1`                          |
| `{TENANT DOMAIN}`             | <span data-ttu-id="bfab6-193">Birincil/yayımcı/kiracı etki alanı</span><span class="sxs-lookup"><span data-stu-id="bfab6-193">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                      |

<span data-ttu-id="bfab6-194">&dagger;Blazor WebAssemblyŞablon, `api://` komutta GEÇIRILEN uygulama kimliği URI bağımsız değişkenine otomatik olarak bir şeması ekler `dotnet new` .</span><span class="sxs-lookup"><span data-stu-id="bfab6-194">&dagger;The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="bfab6-195">Yer tutucu için uygulama KIMLIĞI URI 'SI sağlarken `{SERVER API APP ID URI}` ve düzen ise, `api://` `api://` Yukarıdaki tabloda örnek değer olarak gösterildiği gibi, bağımsız değişkenden düzeni () kaldırın.</span><span class="sxs-lookup"><span data-stu-id="bfab6-195">When providing the App ID URI for the `{SERVER API APP ID URI}` placeholder and if the scheme is `api://`, remove the scheme (`api://`) from the argument, as the example value in the preceding table shows.</span></span> <span data-ttu-id="bfab6-196">Uygulama KIMLIĞI URI 'SI özel bir değer ise veya başka bir şemaya sahipse (örneğin, `https://` güvenilir olmayan bir yayımcı etki alanı için `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` ), varsayılan kapsam URI 'sini el ile güncelleştirmeniz ve `api://` *`Client`* uygulama şablon tarafından oluşturulduktan sonra düzeni kaldırmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-196">If the App ID URI is a custom value or has some other scheme (for example, `https://` for an untrusted publisher domain similar to `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), you must manually update the default scope URI and remove the `api://` scheme after the *`Client`* app is created by the template.</span></span> <span data-ttu-id="bfab6-197">Daha fazla bilgi için, [erişim belirteci kapsamları](#access-token-scopes) bölümündeki nota bakın.</span><span class="sxs-lookup"><span data-stu-id="bfab6-197">For more information, see the note in the [Access token scopes](#access-token-scopes) section.</span></span> <span data-ttu-id="bfab6-198">Blazor WebAssemblyŞablon bu senaryolara yönelik ASP.NET Core gelecek bir sürümünde değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-198">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="bfab6-199">Daha fazla bilgi için bkz. [IStream şablonuyla uygulama kimliği URI 'si Için çift düzen ( Blazor barındırılan, tek kuruluş) (DotNet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="bfab6-199">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="bfab6-200">Seçeneğiyle belirtilen çıktı konumu, `-o|--output` mevcut değilse bir proje klasörü oluşturur ve uygulamanın adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-200">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="bfab6-201">Barındırılan şablon tarafından ayarlanan kapsamda Blazor uygulama KIMLIĞI URI ana bilgisayarı yineleniyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-201">The scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="bfab6-202">Koleksiyon için yapılandırılan kapsamın `DefaultAccessTokenScopes` `Program.Main` uygulamanın () içinde doğru olduğundan emin olun `Program.cs` *`Client`* .</span><span class="sxs-lookup"><span data-stu-id="bfab6-202">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *`Client`* app.</span></span>

> [!NOTE]
> <span data-ttu-id="bfab6-203">Azure portal, *`Client`* uygulamanın platform yapılandırması **yeniden yönlendirme URI 'Si** , Kestrel sunucusunda varsayılan ayarlarla çalışan uygulamalar için bağlantı noktası 5001 için yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="bfab6-203">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="bfab6-204">*`Client`* Uygulama rastgele bir IIS Express bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktası **hata ayıklama** panelindeki *sunucu API 'si uygulamasının* özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-204">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="bfab6-205">Bağlantı noktası, *`Client`* uygulamanın bilinen bağlantı noktasıyla daha önce yapılandırılmamışsa, *`Client`* Azure Portal uygulamanın kaydına dönün ve yenıden yönlendirme URI 'sini doğru bağlantı noktasıyla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-205">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="bfab6-206">*`Server`* Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="bfab6-206">*`Server`* app configuration</span></span>

<span data-ttu-id="bfab6-207">*Bu bölüm, çözümün uygulaması ile ilgilidir **`Server`** .*</span><span class="sxs-lookup"><span data-stu-id="bfab6-207">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="bfab6-208">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="bfab6-208">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="bfab6-209">Microsoft Platformu ile ASP.NET Core Web API 'Lerine yönelik kimlik doğrulama ve yetkilendirme desteği Identity Aşağıdaki paketler tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="bfab6-209">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft Identity Platform is provided by the following packages:</span></span>

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="bfab6-210">Yer tutucu için `{VERSION}` , uygulamanın paylaşılan Framework sürümüyle eşleşen en son kararlı sürümü paketin NuGet.org adresindeki **sürüm geçmişinde** bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-210">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="bfab6-211">ASP.NET Core Web API 'Lerine yönelik kimlik doğrulama ve yetkilendirme desteği paket tarafından sağlanır [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) :</span><span class="sxs-lookup"><span data-stu-id="bfab6-211">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="bfab6-212">Yer tutucu için `{VERSION}` , uygulamanın paylaşılan Framework sürümüyle eşleşen en son kararlı sürümü paketin [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI)adresindeki **sürüm geçmişinde** bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-212">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="bfab6-213">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="bfab6-213">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="bfab6-214">`AddAuthentication`Yöntemi, uygulama içinde kimlik doğrulama hizmetlerini ayarlar ve JWT taşıyıcı işleyicisini varsayılan kimlik doğrulama yöntemi olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="bfab6-214">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="bfab6-215"><xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A>Yöntemi, Web API 'Sini Microsoft Platformu v 2.0 ile korumak için hizmetleri yapılandırır Identity .</span><span class="sxs-lookup"><span data-stu-id="bfab6-215">The <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> method configures services to protect the web API with Microsoft Identity Platform v2.0.</span></span> <span data-ttu-id="bfab6-216">Bu yöntem `AzureAdB2C` , kimlik doğrulama seçeneklerini başlatmak için gerekli ayarlarla uygulamanın yapılandırmasında bir bölüm bekler.</span><span class="sxs-lookup"><span data-stu-id="bfab6-216">This method expects an `AzureAdB2C` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAdB2C"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="bfab6-217">`AddAuthentication`Yöntemi, uygulama içinde kimlik doğrulama hizmetlerini ayarlar ve JWT taşıyıcı işleyicisini varsayılan kimlik doğrulama yöntemi olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="bfab6-217">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="bfab6-218"><xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A>Yöntemi, Azure Active Directory B2C tarafından yayılan belirteçleri doğrulamak için gereken JWT taşıyıcı işleyicisinde belirli parametreleri ayarlar:</span><span class="sxs-lookup"><span data-stu-id="bfab6-218">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

::: moniker-end

<span data-ttu-id="bfab6-219"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A><xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>aşağıdakileri doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="bfab6-219"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="bfab6-220">Uygulama, gelen isteklerde belirteçleri ayrıştırmaya ve doğrulamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="bfab6-220">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="bfab6-221">Uygun kimlik bilgileri olmadan korunan kaynağa erişmeye çalışan istekler başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="bfab6-221">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="bfab6-222">Kullanıcı. Identity . Ada</span><span class="sxs-lookup"><span data-stu-id="bfab6-222">User.Identity.Name</span></span>

<span data-ttu-id="bfab6-223">Varsayılan olarak, `User.Identity.Name` doldurulmaz.</span><span class="sxs-lookup"><span data-stu-id="bfab6-223">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="bfab6-224">Uygulamayı talep türünden değeri alacak şekilde yapılandırmak için `name` :</span><span class="sxs-lookup"><span data-stu-id="bfab6-224">To configure the app to receive the value from the `name` claim type:</span></span>

* <span data-ttu-id="bfab6-225">İçin bir ad alanı <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> ekleyin `Startup.cs` :</span><span class="sxs-lookup"><span data-stu-id="bfab6-225">Add a namespace for <xref:Microsoft.AspNetCore.Authentication.JwtBearer?displayProperty=fullName> to `Startup.cs`:</span></span>

  ```csharp
  using Microsoft.AspNetCore.Authentication.JwtBearer;
  ```

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="bfab6-226"><xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType>İçindeki öğesinin öğesini yapılandırın <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="bfab6-226">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      JwtBearerDefaults.AuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="bfab6-227"><xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType>İçindeki öğesinin öğesini yapılandırın <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="bfab6-227">Configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

  ```csharp
  services.Configure<JwtBearerOptions>(
      AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
      {
          options.TokenValidationParameters.NameClaimType = "name";
      });
  ```

::: moniker-end

### <a name="app-settings"></a><span data-ttu-id="bfab6-228">Uygulama ayarları</span><span class="sxs-lookup"><span data-stu-id="bfab6-228">App settings</span></span>

<span data-ttu-id="bfab6-229">`appsettings.json`Dosya, erişim belirteçlerini doğrulamak için kullanılan JWT taşıyıcı işleyicisini yapılandırma seçeneklerini içerir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-229">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

<span data-ttu-id="bfab6-230">Örnek:</span><span class="sxs-lookup"><span data-stu-id="bfab6-230">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="bfab6-231">Hava tahmin denetleyicisi</span><span class="sxs-lookup"><span data-stu-id="bfab6-231">WeatherForecast controller</span></span>

<span data-ttu-id="bfab6-232">Dalgalı tahmin denetleyicisi (*denetleyiciler/dalgalı Therforetcontroller. cs*), BIR korumalı API 'yi [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) denetleyiciye uygulanmış şekilde gösterir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-232">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="bfab6-233">Bunun anlaşılması **önemlidir** :</span><span class="sxs-lookup"><span data-stu-id="bfab6-233">It's **important** to understand that:</span></span>

* <span data-ttu-id="bfab6-234">Bu [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) API denetleyicisindeki özniteliği, bu API 'yi yetkisiz erişime karşı koruyan tek şeydir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-234">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="bfab6-235">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Uygulamada kullanılan özniteliği yalnızca uygulamanın, Blazor WebAssembly uygulamanın düzgün şekilde çalışması için yetkilendirilmiş olması gerektiğine yönelik bir ipucu görevi görür.</span><span class="sxs-lookup"><span data-stu-id="bfab6-235">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="bfab6-236">*`Client`* Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="bfab6-236">*`Client`* app configuration</span></span>

<span data-ttu-id="bfab6-237">*Bu bölüm, çözümün uygulaması ile ilgilidir **`Client`** .*</span><span class="sxs-lookup"><span data-stu-id="bfab6-237">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="bfab6-238">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="bfab6-238">Authentication package</span></span>

<span data-ttu-id="bfab6-239">Tek bir B2C hesabı () kullanmak üzere bir uygulama oluşturulduğunda `IndividualB2C` , uygulama otomatik olarak [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) () için bir paket başvurusu alır [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) .</span><span class="sxs-lookup"><span data-stu-id="bfab6-239">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="bfab6-240">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="bfab6-240">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="bfab6-241">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="bfab6-241">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="bfab6-242">Yer tutucu için `{VERSION}` , uygulamanın paylaşılan Framework sürümüyle eşleşen en son kararlı sürümü paketin [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)adresindeki **sürüm geçmişinde** bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-242">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="bfab6-243">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)Paket geçişli [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) olarak uygulamayı uygulamaya ekler.</span><span class="sxs-lookup"><span data-stu-id="bfab6-243">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="bfab6-244">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="bfab6-244">Authentication service support</span></span>

<span data-ttu-id="bfab6-245"><xref:System.Net.Http.HttpClient>Sunucu projesine istek yaparken erişim belirteçlerini içeren örnekler için destek eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-245">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="bfab6-246">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="bfab6-246">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="bfab6-247">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="bfab6-247">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="bfab6-248">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> paket tarafından sağlanmış uzantı yöntemiyle kaydedilir [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) .</span><span class="sxs-lookup"><span data-stu-id="bfab6-248">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="bfab6-249">Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="bfab6-249">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="bfab6-250">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="bfab6-250">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="bfab6-251"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Yöntemi, bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="bfab6-251">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="bfab6-252">Uygulamanın yapılandırılması için gereken değerler, uygulamayı kaydettiğinizde Azure Portal AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-252">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="bfab6-253">Yapılandırma dosya tarafından sağlanır `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="bfab6-253">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="bfab6-254">Örnek:</span><span class="sxs-lookup"><span data-stu-id="bfab6-254">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="bfab6-255">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="bfab6-255">Access token scopes</span></span>

<span data-ttu-id="bfab6-256">Varsayılan erişim belirteci kapsamları, erişim belirteci kapsamlarının listesini temsil eder:</span><span class="sxs-lookup"><span data-stu-id="bfab6-256">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="bfab6-257">Oturum açma isteğine varsayılan olarak dahildir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-257">Included by default in the sign in request.</span></span>
* <span data-ttu-id="bfab6-258">Kimlik doğrulamasından hemen sonra bir erişim belirteci sağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="bfab6-258">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="bfab6-259">Tüm kapsamlar Azure Active Directory kuralları başına aynı uygulamaya ait olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="bfab6-259">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="bfab6-260">Gerektiğinde ek API uygulamaları için ek kapsamlar eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="bfab6-260">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="bfab6-261">Blazor WebAssemblyŞablon, `api://` komutta GEÇIRILEN uygulama kimliği URI bağımsız değişkenine otomatik olarak bir şeması ekler `dotnet new` .</span><span class="sxs-lookup"><span data-stu-id="bfab6-261">The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="bfab6-262">Proje şablonundan bir uygulama oluştururken Blazor , varsayılan erişim belirteci kapsamı değerinin Azure Portal belirttiğiniz doğru özel uygulama KIMLIĞI URI değerini ya da aşağıdaki biçimlerden **birine** sahip bir değeri kullandığını doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="bfab6-262">When generating an app from the Blazor project template, confirm that the value of the default access token scope uses either the correct custom App ID URI value that you provided in the Azure portal or a value with **one** of the following formats:</span></span>
>
> * <span data-ttu-id="bfab6-263">Dizinin yayımcı etki alanına **güvenilirse**, varsayılan erişim belirteci kapsamı genellikle aşağıdaki örneğe benzer bir değerdir; burada `API.Access` varsayılan kapsam adıdır:</span><span class="sxs-lookup"><span data-stu-id="bfab6-263">When the publisher domain of the directory is **trusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="bfab6-264">Çift düzen () için değeri inceleyin `api://api://...` .</span><span class="sxs-lookup"><span data-stu-id="bfab6-264">Inspect the value for a double scheme (`api://api://...`).</span></span> <span data-ttu-id="bfab6-265">Bir çift düzen varsa, ilk `api://` düzeni değerden kaldırın.</span><span class="sxs-lookup"><span data-stu-id="bfab6-265">If a double scheme is present, remove the first `api://` scheme from the value.</span></span>
>
> * <span data-ttu-id="bfab6-266">Dizinin yayımcı etki alanı **güvenilir** olmadığında, varsayılan erişim belirteci kapsamı genellikle aşağıdaki örneğe benzer bir değerdir; burada `API.Access` varsayılan kapsam adıdır:</span><span class="sxs-lookup"><span data-stu-id="bfab6-266">When the publisher domain of the directory is **untrusted**, the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="bfab6-267">Ek bir `api://` düzenin () değerini inceleyin `api://https://contoso.onmicrosoft.com/...` .</span><span class="sxs-lookup"><span data-stu-id="bfab6-267">Inspect the value for an extra `api://` scheme (`api://https://contoso.onmicrosoft.com/...`).</span></span> <span data-ttu-id="bfab6-268">Ek bir `api://` düzen varsa, `api://` düzeni değerden kaldırın.</span><span class="sxs-lookup"><span data-stu-id="bfab6-268">If an extra `api://` scheme is present, remove the `api://` scheme from the value.</span></span>
>
> <span data-ttu-id="bfab6-269">Blazor WebAssemblyŞablon bu senaryolara yönelik ASP.NET Core gelecek bir sürümünde değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="bfab6-269">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="bfab6-270">Daha fazla bilgi için bkz. [IStream şablonuyla uygulama kimliği URI 'si Için çift düzen ( Blazor barındırılan, tek kuruluş) (DotNet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="bfab6-270">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="bfab6-271">İle ek kapsamlar belirtin `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="bfab6-271">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="bfab6-272">Daha fazla bilgi için *ek senaryolar* makalesinin aşağıdaki bölümlerine bakın:</span><span class="sxs-lookup"><span data-stu-id="bfab6-272">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="bfab6-273">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="bfab6-273">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="bfab6-274">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="bfab6-274">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="bfab6-275">Oturum açma modu</span><span class="sxs-lookup"><span data-stu-id="bfab6-275">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="bfab6-276">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="bfab6-276">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="bfab6-277">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="bfab6-277">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="bfab6-278">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="bfab6-278">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="bfab6-279">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="bfab6-279">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="bfab6-280">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="bfab6-280">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="bfab6-281">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="bfab6-281">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="bfab6-282">FetchData bileşeni</span><span class="sxs-lookup"><span data-stu-id="bfab6-282">FetchData component</span></span>

[!INCLUDE[](~/blazor/includes/security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="bfab6-283">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="bfab6-283">Run the app</span></span>

<span data-ttu-id="bfab6-284">Uygulamayı sunucu projesinden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="bfab6-284">Run the app from the Server project.</span></span> <span data-ttu-id="bfab6-285">Visual Studio 'yu kullanırken şunlardan birini yapın:</span><span class="sxs-lookup"><span data-stu-id="bfab6-285">When using Visual Studio, either:</span></span>

* <span data-ttu-id="bfab6-286">Araç çubuğundaki **başlangıç projeleri** açılan listesini *sunucu API 'si uygulamasına* ayarlayın ve **Çalıştır** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="bfab6-286">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="bfab6-287">**Çözüm Gezgini** ' de sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya uygulamayı **Hata Ayıkla** menüsünden başlatın.</span><span class="sxs-lookup"><span data-stu-id="bfab6-287">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/blazor/includes/security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/blazor/includes/security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="bfab6-288">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="bfab6-288">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="bfab6-289">Authentication. MSAL JavaScript kitaplığının özel bir sürümünü oluşturun</span><span class="sxs-lookup"><span data-stu-id="bfab6-289">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [<span data-ttu-id="bfab6-290">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="bfab6-290">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="bfab6-291">Öğretici: Azure Active Directory B2C kiracısı oluşturma</span><span class="sxs-lookup"><span data-stu-id="bfab6-291">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="bfab6-292">Öğretici: uygulamayı Azure Active Directory B2C kaydetme</span><span class="sxs-lookup"><span data-stu-id="bfab6-292">Tutorial: Register an application in Azure Active Directory B2C</span></span>](/azure/active-directory-b2c/tutorial-register-applications)
* [<span data-ttu-id="bfab6-293">Microsoft kimlik platformu belgeleri</span><span class="sxs-lookup"><span data-stu-id="bfab6-293">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
