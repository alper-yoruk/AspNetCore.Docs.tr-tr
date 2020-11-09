---
title: Blazor WebAssemblyAzure Active Directory B2C ile ASP.NET Core barındırılan bir uygulamanın güvenliğini sağlama
author: guardrex
description: 'ASP.NET Core barındırılan bir uygulamanın güvenliğini Azure Active Directory B2C ile nasıl sağlayacağınızı öğrenin Blazor WebAssembly .'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/02/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 8727fa52acbcf59549c326bd5106e5dfe23c36be
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234497"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="2b5d0-103">Blazor WebAssemblyAzure Active Directory B2C ile ASP.NET Core barındırılan bir uygulamanın güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="2b5d0-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="2b5d0-104">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="2b5d0-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="2b5d0-105">Bu makalede kimlik doğrulaması için [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) kullanan bir [barındırılan Blazor WebAssembly uygulamanın](xref:blazor/hosting-models#blazor-webassembly) nasıl oluşturulacağı açıklanır.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="2b5d0-106">Uygulamaları AAD B2C kaydetme ve çözüm oluşturma</span><span class="sxs-lookup"><span data-stu-id="2b5d0-106">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="2b5d0-107">Kiracı oluşturma</span><span class="sxs-lookup"><span data-stu-id="2b5d0-107">Create a tenant</span></span>

<span data-ttu-id="2b5d0-108">Öğretici: AAD B2C kiracı oluşturmak için [Azure Active Directory B2C kiracı oluşturma](/azure/active-directory-b2c/tutorial-create-tenant) ' daki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-108">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="2b5d0-109">AAD B2C örneğini kaydedin (örneğin, `https://contoso.b2clogin.com/` sonunda eğik çizgi içeren).</span><span class="sxs-lookup"><span data-stu-id="2b5d0-109">Record the AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span> <span data-ttu-id="2b5d0-110">Örnek, Azure portal **uygulama kayıtları** sayfasından **uç noktalar** penceresi AÇıLARAK bulunan bir Azure B2C uygulama kaydının şeması ve barındırmadır.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-110">The instance is the scheme and host of an Azure B2C app registration, which can be found by opening the **Endpoints** window from the **App registrations** page in the Azure portal.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="2b5d0-111">Sunucu API 'SI uygulaması kaydetme</span><span class="sxs-lookup"><span data-stu-id="2b5d0-111">Register a server API app</span></span>

<span data-ttu-id="2b5d0-112">Eğitim bölümündeki yönergeleri izleyin: *sunucu API uygulaması* IÇIN bir AAD uygulaması kaydetmek üzere [Azure Active Directory B2C bir uygulamayı kaydetme](/azure/active-directory-b2c/tutorial-register-applications) ve ardından aşağıdakileri yapın:</span><span class="sxs-lookup"><span data-stu-id="2b5d0-112">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="2b5d0-113">**Azure Active Directory**  >  **uygulama kayıtları** **Yeni kayıt** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-113">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="2b5d0-114">Uygulama için bir **ad** sağlayın (örneğin, **Blazor Server AAD B2C** ).</span><span class="sxs-lookup"><span data-stu-id="2b5d0-114">Provide a **Name** for the app (for example, **Blazor Server AAD B2C** ).</span></span>
1. <span data-ttu-id="2b5d0-115">**Desteklenen hesap türleri** için, çok kiracılı seçeneğini belirleyin: **herhangi bir kimlik sağlayıcısındaki veya kuruluş dizinindeki hesaplar (Kullanıcı akışları olan kullanıcıların kimliğini doğrulamak için)**</span><span class="sxs-lookup"><span data-stu-id="2b5d0-115">For **Supported account types** , select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="2b5d0-116">*Sunucu API 'si uygulaması* Bu senaryoda **yeniden yönlendirme URI 'si** gerektirmez, bu nedenle açılan kutudan **Web** 'e ve yeniden yönlendirme URI 'si girmeyin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="2b5d0-117">**İzinlerin**  >  **OpenID 'ye yönetici onayı verdiğini ve offline_access izinlerinin** seçili olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-117">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="2b5d0-118">**Kaydet** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-118">Select **Register** .</span></span>

<span data-ttu-id="2b5d0-119">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="2b5d0-119">Record the following information:</span></span>

* <span data-ttu-id="2b5d0-120">*Sunucu API 'si uygulaması* Uygulama (istemci) KIMLIĞI (örneğin, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="2b5d0-120">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="2b5d0-121">AAD birincil/yayımcı/kiracı etki alanı (örneğin, `contoso.onmicrosoft.com` ): etki alanı, kayıtlı uygulama için Azure Portal **marka** dikey penceresinde **Yayımcı etki alanı** olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-121">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="2b5d0-122">**API 'Yi kullanıma** sunma bölümünde:</span><span class="sxs-lookup"><span data-stu-id="2b5d0-122">In **Expose an API** :</span></span>

1. <span data-ttu-id="2b5d0-123">**Kapsam ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-123">Select **Add a scope** .</span></span>
1. <span data-ttu-id="2b5d0-124">**Kaydet ve devam et** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-124">Select **Save and continue** .</span></span>
1. <span data-ttu-id="2b5d0-125">Bir **kapsam adı** sağlayın (örneğin, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="2b5d0-125">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="2b5d0-126">**Yönetici izni görünen adı** sağlayın (örneğin, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="2b5d0-126">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="2b5d0-127">**Yönetici onay açıklaması** sağlayın (örneğin, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="2b5d0-127">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="2b5d0-128">**Durumun** **etkin** olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-128">Confirm that the **State** is set to **Enabled** .</span></span>
1. <span data-ttu-id="2b5d0-129">**Kapsam Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-129">Select **Add scope** .</span></span>

<span data-ttu-id="2b5d0-130">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="2b5d0-130">Record the following information:</span></span>

* <span data-ttu-id="2b5d0-131">Uygulama KIMLIĞI URI 'SI (örneğin, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` , `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` veya belirttiğiniz özel değer)</span><span class="sxs-lookup"><span data-stu-id="2b5d0-131">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provided)</span></span>
* <span data-ttu-id="2b5d0-132">Kapsam adı (örneğin, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="2b5d0-132">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="2b5d0-133">İstemci uygulamasını kaydetme</span><span class="sxs-lookup"><span data-stu-id="2b5d0-133">Register a client app</span></span>

<span data-ttu-id="2b5d0-134">Eğitim bölümündeki yönergeleri izleyin [:](/azure/active-directory-b2c/tutorial-register-applications) uygulamayı bir AAD uygulaması kaydetmek için Azure Active Directory B2C yeniden kaydedin *`Client`* ve ardından aşağıdakileri yapın:</span><span class="sxs-lookup"><span data-stu-id="2b5d0-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *`Client`* app and then do the following:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="2b5d0-135">**Azure Active Directory** > **uygulama kayıtları** **Yeni kayıt** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-135">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="2b5d0-136">Uygulama için bir **ad** sağlayın (örneğin, **Blazor istemci AAD B2C** ).</span><span class="sxs-lookup"><span data-stu-id="2b5d0-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C** ).</span></span>
1. <span data-ttu-id="2b5d0-137">**Desteklenen hesap türleri** için, çok kiracılı seçeneğini belirleyin: **herhangi bir kimlik sağlayıcısındaki veya kuruluş dizinindeki hesaplar (Kullanıcı akışları olan kullanıcıların kimliğini doğrulamak için)**</span><span class="sxs-lookup"><span data-stu-id="2b5d0-137">For **Supported account types** , select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="2b5d0-138">**Yeniden yönlendirme URI 'si** açılan öğesini **tek SAYFALı uygulama (Spa)** olarak ayarlayın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="2b5d0-138">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="2b5d0-139">Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-139">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="2b5d0-140">Uygulama farklı bir Kestrel bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktasını kullanın.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-140">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="2b5d0-141">IIS Express için, uygulama için rastgele oluşturulan bağlantı noktası, *`Server`* **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-141">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="2b5d0-142">Uygulama bu noktada mevcut olmadığından ve IIS Express bağlantı noktası bilinmediğinden, uygulama oluşturulduktan sonra bu adıma geri dönün ve yeniden yönlendirme URI 'sini güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-142">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="2b5d0-143">[Uygulama oluştur](#create-the-app) bölümünde, kullanıcıların YENIDEN yönlendirme URI 'sini güncelleştirmesi IIS Express hatırlatmak için bir açıklama belirir.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-143">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="2b5d0-144">**İzinlerin** > **OpenID 'ye yönetici onayı verdiğini ve offline_access izinlerinin** seçili olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-144">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="2b5d0-145">**Kaydet** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-145">Select **Register** .</span></span>

1. <span data-ttu-id="2b5d0-146">Uygulama (istemci) KIMLIĞINI (örneğin, `4369008b-21fa-427c-abaa-9b53bf58e538` ) kaydedin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-146">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="2b5d0-147">**Kimlik doğrulama** > **platformu yapılandırmalarında** > **tek sayfalı uygulama (Spa)** :</span><span class="sxs-lookup"><span data-stu-id="2b5d0-147">In **Authentication** > **Platform configurations** > **Single-page application (SPA)** :</span></span>

1. <span data-ttu-id="2b5d0-148">**Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-148">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="2b5d0-149">**Örtük verme** Için, **erişim belirteçleri** ve **Kimlik belirteçleri** onay kutularının seçili **olmadığından** emin olun.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-149">For **Implicit grant** , ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="2b5d0-150">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-150">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="2b5d0-151">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-151">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="2b5d0-152">**Azure Active Directory** > **uygulama kayıtları** **Yeni kayıt** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-152">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="2b5d0-153">Uygulama için bir **ad** sağlayın (örneğin, **Blazor istemci AAD B2C** ).</span><span class="sxs-lookup"><span data-stu-id="2b5d0-153">Provide a **Name** for the app (for example, **Blazor Client AAD B2C** ).</span></span>
1. <span data-ttu-id="2b5d0-154">**Desteklenen hesap türleri** için, çok kiracılı seçeneğini belirleyin: **herhangi bir kimlik sağlayıcısındaki veya kuruluş dizinindeki hesaplar (Kullanıcı akışları olan kullanıcıların kimliğini doğrulamak için)**</span><span class="sxs-lookup"><span data-stu-id="2b5d0-154">For **Supported account types** , select the multi-tenant option: **Accounts in any identity provider or organizational directory (for authenticating users with user flows)**</span></span>
1. <span data-ttu-id="2b5d0-155">**Yeniden yönlendirme URI 'si** açılan öğesini **Web** 'e ayarlı bırakın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="2b5d0-155">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="2b5d0-156">Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-156">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="2b5d0-157">Uygulama farklı bir Kestrel bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktasını kullanın.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-157">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="2b5d0-158">IIS Express için, uygulama için rastgele oluşturulan bağlantı noktası, *`Server`* **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-158">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="2b5d0-159">Uygulama bu noktada mevcut olmadığından ve IIS Express bağlantı noktası bilinmediğinden, uygulama oluşturulduktan sonra bu adıma geri dönün ve yeniden yönlendirme URI 'sini güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-159">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="2b5d0-160">[Uygulama oluştur](#create-the-app) bölümünde, kullanıcıların YENIDEN yönlendirme URI 'sini güncelleştirmesi IIS Express hatırlatmak için bir açıklama belirir.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-160">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="2b5d0-161">**İzinlerin** > **OpenID 'ye yönetici onayı verdiğini ve offline_access izinlerinin** seçili olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-161">Confirm that **Permissions** > **Grant admin consent to openid and offline_access permissions** is selected.</span></span>
1. <span data-ttu-id="2b5d0-162">**Kaydet** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-162">Select **Register** .</span></span>

<span data-ttu-id="2b5d0-163">Uygulama (istemci) KIMLIĞINI (örneğin, `4369008b-21fa-427c-abaa-9b53bf58e538` ) kaydedin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-163">Record the Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="2b5d0-164">**Kimlik doğrulama** > **platformu yapılandırması** > **Web** :</span><span class="sxs-lookup"><span data-stu-id="2b5d0-164">In **Authentication** > **Platform configurations** > **Web** :</span></span>

1. <span data-ttu-id="2b5d0-165">**Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-165">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="2b5d0-166">**Örtük izin** Için, **erişim belirteçleri** ve **Kimlik belirteçleri** onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-166">For **Implicit grant** , select the check boxes for **Access tokens** and **ID tokens** .</span></span>
1. <span data-ttu-id="2b5d0-167">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-167">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="2b5d0-168">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-168">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="2b5d0-169">**API izinleri** :</span><span class="sxs-lookup"><span data-stu-id="2b5d0-169">In **API permissions** :</span></span>

1. <span data-ttu-id="2b5d0-170">**Izin Ekle** ' yi ve ardından **API 'lerim** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-170">Select **Add a permission** followed by **My APIs** .</span></span>
1. <span data-ttu-id="2b5d0-171">**Ad** SÜTUNUNDAN *sunucu API uygulamasını* seçin (örneğin, **Blazor Server AAD B2C** ).</span><span class="sxs-lookup"><span data-stu-id="2b5d0-171">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C** ).</span></span>
1. <span data-ttu-id="2b5d0-172">**API** listesini açın.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-172">Open the **API** list.</span></span>
1. <span data-ttu-id="2b5d0-173">API 'ye erişimi etkinleştirin (örneğin, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="2b5d0-173">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="2b5d0-174">**Izin Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-174">Select **Add permissions** .</span></span>
1. <span data-ttu-id="2b5d0-175">**{Tenant Name} için yönetici onayı Izni ver** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-175">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="2b5d0-176">Onaylamak için **Evet** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-176">Select **Yes** to confirm.</span></span>

<span data-ttu-id="2b5d0-177">**Giriş**  >  **Azure AD B2C**  >  **Kullanıcı akışları** ' nda:</span><span class="sxs-lookup"><span data-stu-id="2b5d0-177">In **Home** > **Azure AD B2C** > **User flows** :</span></span>

[<span data-ttu-id="2b5d0-178">Kaydolma ve oturum açma Kullanıcı akışı oluşturma</span><span class="sxs-lookup"><span data-stu-id="2b5d0-178">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="2b5d0-179">En azından, **Application claims**  >  **Display Name** `context.User.Identity.Name` `LoginDisplay` bileşen () içinde doldurmak için uygulama talepleri görünen adı Kullanıcı özniteliğini seçin `Shared/LoginDisplay.razor` .</span><span class="sxs-lookup"><span data-stu-id="2b5d0-179">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (`Shared/LoginDisplay.razor`).</span></span>

<span data-ttu-id="2b5d0-180">Uygulama için oluşturulan kaydolma ve oturum açma Kullanıcı akış adını kaydedin (örneğin, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="2b5d0-180">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="2b5d0-181">Uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="2b5d0-181">Create the app</span></span>

<span data-ttu-id="2b5d0-182">Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:</span><span class="sxs-lookup"><span data-stu-id="2b5d0-182">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| <span data-ttu-id="2b5d0-183">Yer tutucu</span><span class="sxs-lookup"><span data-stu-id="2b5d0-183">Placeholder</span></span>                   | <span data-ttu-id="2b5d0-184">Azure portal adı</span><span class="sxs-lookup"><span data-stu-id="2b5d0-184">Azure portal name</span></span>                                     | <span data-ttu-id="2b5d0-185">Örnek</span><span class="sxs-lookup"><span data-stu-id="2b5d0-185">Example</span></span>                                        |
| ----------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{AAD B2C INSTANCE}`          | <span data-ttu-id="2b5d0-186">Örnek</span><span class="sxs-lookup"><span data-stu-id="2b5d0-186">Instance</span></span>                                              | `https://contoso.b2clogin.com/`                |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`      | <span data-ttu-id="2b5d0-187">Uygulamanın uygulama (istemci) KIMLIĞI *`Client`*</span><span class="sxs-lookup"><span data-stu-id="2b5d0-187">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`             | <span data-ttu-id="2b5d0-188">Kapsam adı</span><span class="sxs-lookup"><span data-stu-id="2b5d0-188">Scope name</span></span>                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}`  | <span data-ttu-id="2b5d0-189">*Sunucu API uygulaması* için uygulama (ISTEMCI) kimliği</span><span class="sxs-lookup"><span data-stu-id="2b5d0-189">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`     | <span data-ttu-id="2b5d0-190">Uygulama KIMLIĞI URI 'SI&dagger;</span><span class="sxs-lookup"><span data-stu-id="2b5d0-190">Application ID URI&dagger;</span></span>                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{SIGN UP OR SIGN IN POLICY}` | <span data-ttu-id="2b5d0-191">Kaydolma/oturum açma Kullanıcı akışı</span><span class="sxs-lookup"><span data-stu-id="2b5d0-191">Sign-up/sign-in user flow</span></span>                             | `B2C_1_signupsignin1`                          |
| `{TENANT DOMAIN}`             | <span data-ttu-id="2b5d0-192">Birincil/yayımcı/kiracı etki alanı</span><span class="sxs-lookup"><span data-stu-id="2b5d0-192">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                      |

<span data-ttu-id="2b5d0-193">&dagger;Blazor WebAssemblyŞablon, `api://` komutta GEÇIRILEN uygulama kimliği URI bağımsız değişkenine otomatik olarak bir şeması ekler `dotnet new` .</span><span class="sxs-lookup"><span data-stu-id="2b5d0-193">&dagger;The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="2b5d0-194">Yer tutucu için uygulama KIMLIĞI URI 'SI sağlarken `{SERVER API APP ID URI}` ve düzen ise, `api://` `api://` Yukarıdaki tabloda örnek değer olarak gösterildiği gibi, bağımsız değişkenden düzeni () kaldırın.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-194">When providing the App ID URI for the `{SERVER API APP ID URI}` placeholder and if the scheme is `api://`, remove the scheme (`api://`) from the argument, as the example value in the preceding table shows.</span></span> <span data-ttu-id="2b5d0-195">Uygulama KIMLIĞI URI 'SI özel bir değer ise veya başka bir şemaya sahipse (örneğin, `https://` güvenilir olmayan bir yayımcı etki alanı için `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` ), varsayılan kapsam URI 'sini el ile güncelleştirmeniz ve `api://` *`Client`* uygulama şablon tarafından oluşturulduktan sonra düzeni kaldırmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-195">If the App ID URI is a custom value or has some other scheme (for example, `https://` for an untrusted publisher domain similar to `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), you must manually update the default scope URI and remove the `api://` scheme after the *`Client`* app is created by the template.</span></span> <span data-ttu-id="2b5d0-196">Daha fazla bilgi için, [erişim belirteci kapsamları](#access-token-scopes) bölümündeki nota bakın.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-196">For more information, see the note in the [Access token scopes](#access-token-scopes) section.</span></span> <span data-ttu-id="2b5d0-197">Blazor WebAssemblyŞablon bu senaryolara yönelik ASP.NET Core gelecek bir sürümünde değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-197">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="2b5d0-198">Daha fazla bilgi için bkz. [IStream şablonuyla uygulama kimliği URI 'si Için çift düzen ( Blazor barındırılan, tek kuruluş) (DotNet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="2b5d0-198">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="2b5d0-199">Seçeneğiyle belirtilen çıktı konumu, `-o|--output` mevcut değilse bir proje klasörü oluşturur ve uygulamanın adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-199">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="2b5d0-200">Barındırılan şablon tarafından ayarlanan kapsamda Blazor uygulama KIMLIĞI URI ana bilgisayarı yineleniyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-200">The scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="2b5d0-201">Koleksiyon için yapılandırılan kapsamın `DefaultAccessTokenScopes` `Program.Main` uygulamanın () içinde doğru olduğundan emin olun `Program.cs` *`Client`* .</span><span class="sxs-lookup"><span data-stu-id="2b5d0-201">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (`Program.cs`) of the *`Client`* app.</span></span>

> [!NOTE]
> <span data-ttu-id="2b5d0-202">Azure portal, *`Client`* uygulamanın platform yapılandırması **yeniden yönlendirme URI 'Si** , Kestrel sunucusunda varsayılan ayarlarla çalışan uygulamalar için bağlantı noktası 5001 için yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-202">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="2b5d0-203">*`Client`* Uygulama rastgele bir IIS Express bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktası **hata ayıklama** panelindeki *sunucu API 'si uygulamasının* özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-203">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="2b5d0-204">Bağlantı noktası, *`Client`* uygulamanın bilinen bağlantı noktasıyla daha önce yapılandırılmamışsa, *`Client`* Azure Portal uygulamanın kaydına dönün ve yenıden yönlendirme URI 'sini doğru bağlantı noktasıyla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-204">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="2b5d0-205">*`Server`* Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="2b5d0-205">*`Server`* app configuration</span></span>

<span data-ttu-id="2b5d0-206">*Bu bölüm, çözümün uygulaması ile ilgilidir **`Server`** .*</span><span class="sxs-lookup"><span data-stu-id="2b5d0-206">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="2b5d0-207">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="2b5d0-207">Authentication package</span></span>

<span data-ttu-id="2b5d0-208">ASP.NET Core Web API 'Lerine yönelik kimlik doğrulama ve yetkilendirme desteği paket tarafından sağlanır [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) :</span><span class="sxs-lookup"><span data-stu-id="2b5d0-208">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="2b5d0-209">Yer tutucu için `{VERSION}` , uygulamanın paylaşılan Framework sürümüyle eşleşen en son kararlı sürümü paketin [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI)adresindeki **sürüm geçmişinde** bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-209">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="2b5d0-210">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="2b5d0-210">Authentication service support</span></span>

<span data-ttu-id="2b5d0-211">`AddAuthentication`Yöntemi, uygulama içinde kimlik doğrulama hizmetlerini ayarlar ve JWT taşıyıcı işleyicisini varsayılan kimlik doğrulama yöntemi olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-211">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="2b5d0-212"><xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A>Yöntemi, Azure Active Directory B2C tarafından yayılan belirteçleri doğrulamak için gereken JWT taşıyıcı işleyicisinde belirli parametreleri ayarlar:</span><span class="sxs-lookup"><span data-stu-id="2b5d0-212">The <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="2b5d0-213"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A><xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>aşağıdakileri doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="2b5d0-213"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="2b5d0-214">Uygulama, gelen isteklerde belirteçleri ayrıştırmaya ve doğrulamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-214">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="2b5d0-215">Uygun kimlik bilgileri olmadan korunan kaynağa erişmeye çalışan istekler başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-215">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="2b5d0-216">Kullanıcı. Identity . Ada</span><span class="sxs-lookup"><span data-stu-id="2b5d0-216">User.Identity.Name</span></span>

<span data-ttu-id="2b5d0-217">Varsayılan olarak, `User.Identity.Name` doldurulmaz.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-217">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="2b5d0-218">Uygulamayı talep türünden değeri alacak şekilde yapılandırmak için, `name` <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> ' ın <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> ' de ' yi yapılandırın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2b5d0-218">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="2b5d0-219">Uygulama ayarları</span><span class="sxs-lookup"><span data-stu-id="2b5d0-219">App settings</span></span>

<span data-ttu-id="2b5d0-220">`appsettings.json`Dosya, erişim belirteçlerini doğrulamak için kullanılan JWT taşıyıcı işleyicisini yapılandırma seçeneklerini içerir.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-220">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

<span data-ttu-id="2b5d0-221">Örnek:</span><span class="sxs-lookup"><span data-stu-id="2b5d0-221">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="2b5d0-222">Hava tahmin denetleyicisi</span><span class="sxs-lookup"><span data-stu-id="2b5d0-222">WeatherForecast controller</span></span>

<span data-ttu-id="2b5d0-223">Dalgalı tahmin denetleyicisi ( *denetleyiciler/dalgalı Therforetcontroller. cs* ), BIR korumalı API 'yi [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) denetleyiciye uygulanmış şekilde gösterir.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-223">The WeatherForecast controller ( *Controllers/WeatherForecastController.cs* ) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="2b5d0-224">Bunun anlaşılması **önemlidir** :</span><span class="sxs-lookup"><span data-stu-id="2b5d0-224">It's **important** to understand that:</span></span>

* <span data-ttu-id="2b5d0-225">Bu [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) API denetleyicisindeki özniteliği, bu API 'yi yetkisiz erişime karşı koruyan tek şeydir.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-225">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="2b5d0-226">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Uygulamada kullanılan özniteliği yalnızca uygulamanın, Blazor WebAssembly uygulamanın düzgün şekilde çalışması için yetkilendirilmiş olması gerektiğine yönelik bir ipucu görevi görür.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-226">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="2b5d0-227">*`Client`* Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="2b5d0-227">*`Client`* app configuration</span></span>

<span data-ttu-id="2b5d0-228">*Bu bölüm, çözümün uygulaması ile ilgilidir **`Client`** .*</span><span class="sxs-lookup"><span data-stu-id="2b5d0-228">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="2b5d0-229">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="2b5d0-229">Authentication package</span></span>

<span data-ttu-id="2b5d0-230">Tek bir B2C hesabı () kullanmak üzere bir uygulama oluşturulduğunda `IndividualB2C` , uygulama otomatik olarak [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) () için bir paket başvurusu alır [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) .</span><span class="sxs-lookup"><span data-stu-id="2b5d0-230">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="2b5d0-231">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-231">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="2b5d0-232">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2b5d0-232">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="2b5d0-233">Yer tutucu için `{VERSION}` , uygulamanın paylaşılan Framework sürümüyle eşleşen en son kararlı sürümü paketin [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)adresindeki **sürüm geçmişinde** bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-233">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="2b5d0-234">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)Paket geçişli [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) olarak uygulamayı uygulamaya ekler.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-234">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="2b5d0-235">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="2b5d0-235">Authentication service support</span></span>

<span data-ttu-id="2b5d0-236"><xref:System.Net.Http.HttpClient>Sunucu projesine istek yaparken erişim belirteçlerini içeren örnekler için destek eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-236">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="2b5d0-237">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="2b5d0-237">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="2b5d0-238">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="2b5d0-238">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="2b5d0-239">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> paket tarafından sağlanmış uzantı yöntemiyle kaydedilir [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) .</span><span class="sxs-lookup"><span data-stu-id="2b5d0-239">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="2b5d0-240">Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-240">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="2b5d0-241">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="2b5d0-241">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="2b5d0-242"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Yöntemi, bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-242">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="2b5d0-243">Uygulamanın yapılandırılması için gereken değerler, uygulamayı kaydettiğinizde Azure Portal AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-243">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="2b5d0-244">Yapılandırma dosya tarafından sağlanır `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="2b5d0-244">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="2b5d0-245">Örnek:</span><span class="sxs-lookup"><span data-stu-id="2b5d0-245">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="2b5d0-246">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="2b5d0-246">Access token scopes</span></span>

<span data-ttu-id="2b5d0-247">Varsayılan erişim belirteci kapsamları, erişim belirteci kapsamlarının listesini temsil eder:</span><span class="sxs-lookup"><span data-stu-id="2b5d0-247">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="2b5d0-248">Oturum açma isteğine varsayılan olarak dahildir.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-248">Included by default in the sign in request.</span></span>
* <span data-ttu-id="2b5d0-249">Kimlik doğrulamasından hemen sonra bir erişim belirteci sağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-249">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="2b5d0-250">Tüm kapsamlar Azure Active Directory kuralları başına aynı uygulamaya ait olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-250">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="2b5d0-251">Gerektiğinde ek API uygulamaları için ek kapsamlar eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="2b5d0-251">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="2b5d0-252">Blazor WebAssemblyŞablon, `api://` komutta GEÇIRILEN uygulama kimliği URI bağımsız değişkenine otomatik olarak bir şeması ekler `dotnet new` .</span><span class="sxs-lookup"><span data-stu-id="2b5d0-252">The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="2b5d0-253">Proje şablonundan bir uygulama oluştururken Blazor , varsayılan erişim belirteci kapsamı değerinin Azure Portal belirttiğiniz doğru özel uygulama KIMLIĞI URI değerini ya da aşağıdaki biçimlerden **birine** sahip bir değeri kullandığını doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="2b5d0-253">When generating an app from the Blazor project template, confirm that the value of the default access token scope uses either the correct custom App ID URI value that you provided in the Azure portal or a value with **one** of the following formats:</span></span>
>
> * <span data-ttu-id="2b5d0-254">Dizinin yayımcı etki alanına **güvenilirse** , varsayılan erişim belirteci kapsamı genellikle aşağıdaki örneğe benzer bir değerdir; burada `API.Access` varsayılan kapsam adıdır:</span><span class="sxs-lookup"><span data-stu-id="2b5d0-254">When the publisher domain of the directory is **trusted** , the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="2b5d0-255">Çift düzen () için değeri inceleyin `api://api://...` .</span><span class="sxs-lookup"><span data-stu-id="2b5d0-255">Inspect the value for a double scheme (`api://api://...`).</span></span> <span data-ttu-id="2b5d0-256">Bir çift düzen varsa, ilk `api://` düzeni değerden kaldırın.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-256">If a double scheme is present, remove the first `api://` scheme from the value.</span></span>
>
> * <span data-ttu-id="2b5d0-257">Dizinin yayımcı etki alanı **güvenilir** olmadığında, varsayılan erişim belirteci kapsamı genellikle aşağıdaki örneğe benzer bir değerdir; burada `API.Access` varsayılan kapsam adıdır:</span><span class="sxs-lookup"><span data-stu-id="2b5d0-257">When the publisher domain of the directory is **untrusted** , the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="2b5d0-258">Ek bir `api://` düzenin () değerini inceleyin `api://https://contoso.onmicrosoft.com/...` .</span><span class="sxs-lookup"><span data-stu-id="2b5d0-258">Inspect the value for an extra `api://` scheme (`api://https://contoso.onmicrosoft.com/...`).</span></span> <span data-ttu-id="2b5d0-259">Ek bir `api://` düzen varsa, `api://` düzeni değerden kaldırın.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-259">If an extra `api://` scheme is present, remove the `api://` scheme from the value.</span></span>
>
> <span data-ttu-id="2b5d0-260">Blazor WebAssemblyŞablon bu senaryolara yönelik ASP.NET Core gelecek bir sürümünde değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-260">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="2b5d0-261">Daha fazla bilgi için bkz. [IStream şablonuyla uygulama kimliği URI 'si Için çift düzen ( Blazor barındırılan, tek kuruluş) (DotNet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="2b5d0-261">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="2b5d0-262">İle ek kapsamlar belirtin `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="2b5d0-262">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

<span data-ttu-id="2b5d0-263">Daha fazla bilgi için *ek senaryolar* makalesinin aşağıdaki bölümlerine bakın:</span><span class="sxs-lookup"><span data-stu-id="2b5d0-263">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="2b5d0-264">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="2b5d0-264">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="2b5d0-265">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="2b5d0-265">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="2b5d0-266">Oturum açma modu</span><span class="sxs-lookup"><span data-stu-id="2b5d0-266">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="2b5d0-267">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="2b5d0-267">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="2b5d0-268">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="2b5d0-268">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="2b5d0-269">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="2b5d0-269">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="2b5d0-270">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="2b5d0-270">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="2b5d0-271">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="2b5d0-271">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="2b5d0-272">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="2b5d0-272">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="2b5d0-273">FetchData bileşeni</span><span class="sxs-lookup"><span data-stu-id="2b5d0-273">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="2b5d0-274">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="2b5d0-274">Run the app</span></span>

<span data-ttu-id="2b5d0-275">Uygulamayı sunucu projesinden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-275">Run the app from the Server project.</span></span> <span data-ttu-id="2b5d0-276">Visual Studio 'yu kullanırken şunlardan birini yapın:</span><span class="sxs-lookup"><span data-stu-id="2b5d0-276">When using Visual Studio, either:</span></span>

* <span data-ttu-id="2b5d0-277">Araç çubuğundaki **başlangıç projeleri** açılan listesini *sunucu API 'si uygulamasına* ayarlayın ve **Çalıştır** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-277">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="2b5d0-278">**Çözüm Gezgini** ' de sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya uygulamayı **Hata Ayıkla** menüsünden başlatın.</span><span class="sxs-lookup"><span data-stu-id="2b5d0-278">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="2b5d0-279">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2b5d0-279">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="2b5d0-280">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="2b5d0-280">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="2b5d0-281">Öğretici: Azure Active Directory B2C kiracısı oluşturma</span><span class="sxs-lookup"><span data-stu-id="2b5d0-281">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="2b5d0-282">Microsoft kimlik platformu belgeleri</span><span class="sxs-lookup"><span data-stu-id="2b5d0-282">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
