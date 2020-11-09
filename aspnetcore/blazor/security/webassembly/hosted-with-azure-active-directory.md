---
title: Blazor WebAssemblyAzure Active Directory ile ASP.NET Core barındırılan bir uygulamanın güvenliğini sağlama
author: guardrex
description: 'ASP.NET Core barındırılan bir uygulamanın güvenliğini Azure Active Directory ile nasıl sağlayacağınızı öğrenin Blazor WebAssembly .'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 17f96be762ece8c59577445eb2ae630a8ee3b3dd
ms.sourcegitcommit: d64bf0cbe763beda22a7728c7f10d07fc5e19262
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93234484"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="697f6-103">Blazor WebAssemblyAzure Active Directory ile ASP.NET Core barındırılan bir uygulamanın güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="697f6-103">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="697f6-104">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="697f6-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="697f6-105">Bu makalede, kimlik doğrulaması için [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) kullanan bir [barındırılan Blazor WebAssembly uygulamanın](xref:blazor/hosting-models#blazor-webassembly) nasıl oluşturulacağı açıklanır.</span><span class="sxs-lookup"><span data-stu-id="697f6-105">This article describes how to create a [hosted Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="697f6-106">Visual Blazor WebAssembly Studio 'da oluşturulan ve bır AAD kurumsal dizinindeki hesapları destekleyecek şekilde yapılandırılan uygulamalar için, Visual Studio uygulamayı proje nesli üzerinde doğru şekilde yapılandırmaz.</span><span class="sxs-lookup"><span data-stu-id="697f6-106">For Blazor WebAssembly apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't configure the app correctly on project generation.</span></span> <span data-ttu-id="697f6-107">Bu, Visual Studio 'nun gelecek bir sürümünde değinilecek.</span><span class="sxs-lookup"><span data-stu-id="697f6-107">This will be addressed in a future release of Visual Studio.</span></span> <span data-ttu-id="697f6-108">Bu makalede .NET Core CLI komutuyla uygulamanın nasıl oluşturulacağı gösterilmektedir `dotnet new` .</span><span class="sxs-lookup"><span data-stu-id="697f6-108">This article shows how to create the app with the .NET Core CLI's `dotnet new` command.</span></span> <span data-ttu-id="697f6-109">IDE 'yi ASP.NET Core 5,0 ' deki en son şablonlar için güncelleştirmeden önce Visual Studio ile oluşturmayı tercih ediyorsanız Blazor , bu makalenin her bölümüne başvurun ve Visual Studio uygulamayı oluşturduktan sonra uygulamanın yapılandırmasını onaylayın veya güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="697f6-109">If you prefer to create the app with Visual Studio before the IDE is updated for the latest Blazor templates in ASP.NET Core 5.0, refer to each section of this article and confirm or update the app's configuration after Visual Studio creates the app.</span></span>

::: moniker-end

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="697f6-110">AAD 'de uygulama kaydetme ve çözüm oluşturma</span><span class="sxs-lookup"><span data-stu-id="697f6-110">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="697f6-111">Kiracı oluşturma</span><span class="sxs-lookup"><span data-stu-id="697f6-111">Create a tenant</span></span>

<span data-ttu-id="697f6-112">Hızlı başlangıç: AAD 'de kiracı oluşturmak için [bir kiracı ayarlama](/azure/active-directory/develop/quickstart-create-new-tenant) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="697f6-112">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="697f6-113">Sunucu API 'SI uygulaması kaydetme</span><span class="sxs-lookup"><span data-stu-id="697f6-113">Register a server API app</span></span>

<span data-ttu-id="697f6-114">Hızlı Başlangıç bölümündeki yönergeleri izleyin: *sunucu API uygulaması* IÇIN bir AAD uygulaması kaydetmek üzere Microsoft Identity platformu ve sonrakı Azure AAD konularıyla [bir uygulama kaydetme](/azure/active-directory/develop/quickstart-register-app) ve ardından aşağıdakileri yapın:</span><span class="sxs-lookup"><span data-stu-id="697f6-114">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* and then do the following:</span></span>

1. <span data-ttu-id="697f6-115">**Azure Active Directory**  >  **uygulama kayıtları** **Yeni kayıt** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="697f6-115">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="697f6-116">Uygulama için bir **ad** sağlayın (örneğin, **Blazor Server AAD** ).</span><span class="sxs-lookup"><span data-stu-id="697f6-116">Provide a **Name** for the app (for example, **Blazor Server AAD** ).</span></span>
1. <span data-ttu-id="697f6-117">Desteklenen bir **Hesap türü** seçin.</span><span class="sxs-lookup"><span data-stu-id="697f6-117">Choose a **Supported account types** .</span></span> <span data-ttu-id="697f6-118">Bu deneyim için **yalnızca bu kuruluş dizininde** (tek kiracı) hesaplar seçebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="697f6-118">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="697f6-119">*Sunucu API 'si uygulaması* Bu senaryoda **yeniden yönlendirme URI 'si** gerektirmez, bu nedenle açılan kutudan **Web** 'e ve yeniden yönlendirme URI 'si girmeyin.</span><span class="sxs-lookup"><span data-stu-id="697f6-119">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="697f6-120">**Permissions**  >  **OpenID ve offline_access izinleri için yönetici onayı verme** izinleri onay kutusunu temizleyin.</span><span class="sxs-lookup"><span data-stu-id="697f6-120">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="697f6-121">**Kaydet** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="697f6-121">Select **Register** .</span></span>

<span data-ttu-id="697f6-122">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="697f6-122">Record the following information:</span></span>

* <span data-ttu-id="697f6-123">*Sunucu API 'si uygulaması* Uygulama (istemci) KIMLIĞI (örneğin, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="697f6-123">*Server API app* Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="697f6-124">Dizin (kiracı) KIMLIĞI (örneğin, `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="697f6-124">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>
* <span data-ttu-id="697f6-125">AAD birincil/yayımcı/kiracı etki alanı (örneğin, `contoso.onmicrosoft.com` ): etki alanı, kayıtlı uygulama için Azure Portal **marka** dikey penceresinde **Yayımcı etki alanı** olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="697f6-125">AAD Primary/Publisher/Tenant domain (for example, `contoso.onmicrosoft.com`): The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="697f6-126">**API izinlerinde** , **Microsoft Graph**  >  uygulama oturum açma veya Kullanıcı profili erişimi gerektirmediğinden Microsoft Graph **User. Read** iznini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="697f6-126">In **API permissions** , remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="697f6-127">**API 'Yi kullanıma** sunma bölümünde:</span><span class="sxs-lookup"><span data-stu-id="697f6-127">In **Expose an API** :</span></span>

1. <span data-ttu-id="697f6-128">**Kapsam ekle** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="697f6-128">Select **Add a scope** .</span></span>
1. <span data-ttu-id="697f6-129">**Kaydet ve devam et** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="697f6-129">Select **Save and continue** .</span></span>
1. <span data-ttu-id="697f6-130">Bir **kapsam adı** sağlayın (örneğin, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="697f6-130">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="697f6-131">**Yönetici izni görünen adı** sağlayın (örneğin, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="697f6-131">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="697f6-132">**Yönetici onay açıklaması** sağlayın (örneğin, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="697f6-132">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="697f6-133">**Durumun** **etkin** olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="697f6-133">Confirm that the **State** is set to **Enabled** .</span></span>
1. <span data-ttu-id="697f6-134">**Kapsam Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="697f6-134">Select **Add scope** .</span></span>

<span data-ttu-id="697f6-135">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="697f6-135">Record the following information:</span></span>

* <span data-ttu-id="697f6-136">Uygulama KIMLIĞI URI 'SI (örneğin, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` , `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` veya sağladığınız özel değer)</span><span class="sxs-lookup"><span data-stu-id="697f6-136">App ID URI (for example, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd`, `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`, or the custom value that you provide)</span></span>
* <span data-ttu-id="697f6-137">Kapsam adı (örneğin, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="697f6-137">Scope name (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="697f6-138">İstemci uygulamasını kaydetme</span><span class="sxs-lookup"><span data-stu-id="697f6-138">Register a client app</span></span>

<span data-ttu-id="697f6-139">[Hızlı başlangıç: uygulamayı Microsoft Identity platformu Ile kaydetme](/azure/active-directory/develop/quickstart-register-app) ve sonrakı Azure AAD konularındaki yönergeleri izleyerek uygulamaya YÖNELIK bir AAD uygulaması kaydedin *`Client`* ve ardından aşağıdakileri yapın:</span><span class="sxs-lookup"><span data-stu-id="697f6-139">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *`Client`* app and then do the following:</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="697f6-140">**Azure Active Directory** > **uygulama kayıtları** **Yeni kayıt** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="697f6-140">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="697f6-141">Uygulama için bir **ad** sağlayın (örneğin, **Blazor istemci AAD** ).</span><span class="sxs-lookup"><span data-stu-id="697f6-141">Provide a **Name** for the app (for example, **Blazor Client AAD** ).</span></span>
1. <span data-ttu-id="697f6-142">Desteklenen bir **Hesap türü** seçin.</span><span class="sxs-lookup"><span data-stu-id="697f6-142">Choose a **Supported account types** .</span></span> <span data-ttu-id="697f6-143">Bu deneyim için **yalnızca bu kuruluş dizininde** (tek kiracı) hesaplar seçebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="697f6-143">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="697f6-144">**Yeniden yönlendirme URI 'si** açılan öğesini **tek SAYFALı uygulama (Spa)** olarak ayarlayın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="697f6-144">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="697f6-145">Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir.</span><span class="sxs-lookup"><span data-stu-id="697f6-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="697f6-146">Uygulama farklı bir Kestrel bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktasını kullanın.</span><span class="sxs-lookup"><span data-stu-id="697f6-146">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="697f6-147">IIS Express için, uygulama için rastgele oluşturulan bağlantı noktası, *`Server`* **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="697f6-147">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="697f6-148">Uygulama bu noktada mevcut olmadığından ve IIS Express bağlantı noktası bilinmediğinden, uygulama oluşturulduktan sonra bu adıma geri dönün ve yeniden yönlendirme URI 'sini güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="697f6-148">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="697f6-149">[Uygulama oluştur](#create-the-app) bölümünde, kullanıcıların YENIDEN yönlendirme URI 'sini güncelleştirmesi IIS Express hatırlatmak için bir açıklama belirir.</span><span class="sxs-lookup"><span data-stu-id="697f6-149">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="697f6-150">**Permissions** > **OpenID ve offline_access izinleri için yönetici onayı verme** izinleri onay kutusunu temizleyin.</span><span class="sxs-lookup"><span data-stu-id="697f6-150">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="697f6-151">**Kaydet** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="697f6-151">Select **Register** .</span></span>

<span data-ttu-id="697f6-152">*`Client`* Uygulama uygulaması (istemci) kimliğini (örneğin, `4369008b-21fa-427c-abaa-9b53bf58e538` ) kaydedin.</span><span class="sxs-lookup"><span data-stu-id="697f6-152">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="697f6-153">**Kimlik doğrulama** > **platformu yapılandırmalarında** > **tek sayfalı uygulama (Spa)** :</span><span class="sxs-lookup"><span data-stu-id="697f6-153">In **Authentication** > **Platform configurations** > **Single-page application (SPA)** :</span></span>

1. <span data-ttu-id="697f6-154">**Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="697f6-154">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="697f6-155">**Örtük verme** Için, **erişim belirteçleri** ve **Kimlik belirteçleri** onay kutularının seçili **olmadığından** emin olun.</span><span class="sxs-lookup"><span data-stu-id="697f6-155">For **Implicit grant** , ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="697f6-156">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="697f6-156">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="697f6-157">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="697f6-157">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="697f6-158">**Azure Active Directory** > **uygulama kayıtları** **Yeni kayıt** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="697f6-158">In **Azure Active Directory** > **App registrations** , select **New registration** .</span></span>
1. <span data-ttu-id="697f6-159">Uygulama için bir **ad** sağlayın (örneğin, **Blazor istemci AAD** ).</span><span class="sxs-lookup"><span data-stu-id="697f6-159">Provide a **Name** for the app (for example, **Blazor Client AAD** ).</span></span>
1. <span data-ttu-id="697f6-160">Desteklenen bir **Hesap türü** seçin.</span><span class="sxs-lookup"><span data-stu-id="697f6-160">Choose a **Supported account types** .</span></span> <span data-ttu-id="697f6-161">Bu deneyim için **yalnızca bu kuruluş dizininde** (tek kiracı) hesaplar seçebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="697f6-161">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="697f6-162">**Yeniden yönlendirme URI 'si** açılan öğesini **Web** 'e ayarlı bırakın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="697f6-162">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="697f6-163">Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir.</span><span class="sxs-lookup"><span data-stu-id="697f6-163">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="697f6-164">Uygulama farklı bir Kestrel bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktasını kullanın.</span><span class="sxs-lookup"><span data-stu-id="697f6-164">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="697f6-165">IIS Express için, uygulama için rastgele oluşturulan bağlantı noktası, *`Server`* **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="697f6-165">For IIS Express, the randomly generated port for the app can be found in the *`Server`* app's properties in the **Debug** panel.</span></span> <span data-ttu-id="697f6-166">Uygulama bu noktada mevcut olmadığından ve IIS Express bağlantı noktası bilinmediğinden, uygulama oluşturulduktan sonra bu adıma geri dönün ve yeniden yönlendirme URI 'sini güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="697f6-166">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="697f6-167">[Uygulama oluştur](#create-the-app) bölümünde, kullanıcıların YENIDEN yönlendirme URI 'sini güncelleştirmesi IIS Express hatırlatmak için bir açıklama belirir.</span><span class="sxs-lookup"><span data-stu-id="697f6-167">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="697f6-168">**Permissions** > **OpenID ve offline_access izinleri için yönetici onayı verme** izinleri onay kutusunu temizleyin.</span><span class="sxs-lookup"><span data-stu-id="697f6-168">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="697f6-169">**Kaydet** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="697f6-169">Select **Register** .</span></span>

<span data-ttu-id="697f6-170">*`Client`* Uygulama uygulaması (istemci) kimliğini (örneğin, `4369008b-21fa-427c-abaa-9b53bf58e538` ) kaydedin.</span><span class="sxs-lookup"><span data-stu-id="697f6-170">Record the *`Client`* app Application (client) ID (for example, `4369008b-21fa-427c-abaa-9b53bf58e538`).</span></span>

<span data-ttu-id="697f6-171">**Kimlik doğrulama** > **platformu yapılandırması** > **Web** :</span><span class="sxs-lookup"><span data-stu-id="697f6-171">In **Authentication** > **Platform configurations** > **Web** :</span></span>

1. <span data-ttu-id="697f6-172">**Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="697f6-172">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="697f6-173">**Örtük izin** Için, **erişim belirteçleri** ve **Kimlik belirteçleri** onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="697f6-173">For **Implicit grant** , select the check boxes for **Access tokens** and **ID tokens** .</span></span>
1. <span data-ttu-id="697f6-174">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="697f6-174">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="697f6-175">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="697f6-175">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="697f6-176">**API izinleri** :</span><span class="sxs-lookup"><span data-stu-id="697f6-176">In **API permissions** :</span></span>

1. <span data-ttu-id="697f6-177">Uygulamanın **Microsoft Graph**  >  **User. Read** iznine sahip olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="697f6-177">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="697f6-178">**Izin Ekle** ' yi ve ardından **API 'lerim** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="697f6-178">Select **Add a permission** followed by **My APIs** .</span></span>
1. <span data-ttu-id="697f6-179">**Ad** SÜTUNUNDAN *sunucu API uygulamasını* (örneğin, **Blazor Server AAD** ) seçin.</span><span class="sxs-lookup"><span data-stu-id="697f6-179">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD** ).</span></span>
1. <span data-ttu-id="697f6-180">**API** listesini açın.</span><span class="sxs-lookup"><span data-stu-id="697f6-180">Open the **API** list.</span></span>
1. <span data-ttu-id="697f6-181">API 'ye erişimi etkinleştirin (örneğin, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="697f6-181">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="697f6-182">**Izin Ekle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="697f6-182">Select **Add permissions** .</span></span>
1. <span data-ttu-id="697f6-183">**{Tenant Name} için yönetici onayı Izni ver** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="697f6-183">Select the **Grant admin consent for {TENANT NAME}** button.</span></span> <span data-ttu-id="697f6-184">Onaylamak için **Evet** ’i seçin.</span><span class="sxs-lookup"><span data-stu-id="697f6-184">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="697f6-185">Uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="697f6-185">Create the app</span></span>

<span data-ttu-id="697f6-186">Boş bir klasörde, aşağıdaki komutta yer tutucuları daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:</span><span class="sxs-lookup"><span data-stu-id="697f6-186">In an empty folder, replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="697f6-187">Yer tutucu</span><span class="sxs-lookup"><span data-stu-id="697f6-187">Placeholder</span></span>                  | <span data-ttu-id="697f6-188">Azure portal adı</span><span class="sxs-lookup"><span data-stu-id="697f6-188">Azure portal name</span></span>                                     | <span data-ttu-id="697f6-189">Örnek</span><span class="sxs-lookup"><span data-stu-id="697f6-189">Example</span></span>                                        |
| ---------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{APP NAME}`                 | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`     | <span data-ttu-id="697f6-190">Uygulamanın uygulama (istemci) KIMLIĞI *`Client`*</span><span class="sxs-lookup"><span data-stu-id="697f6-190">Application (client) ID for the *`Client`* app</span></span>        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`            | <span data-ttu-id="697f6-191">Kapsam adı</span><span class="sxs-lookup"><span data-stu-id="697f6-191">Scope name</span></span>                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}` | <span data-ttu-id="697f6-192">*Sunucu API uygulaması* için uygulama (ISTEMCI) kimliği</span><span class="sxs-lookup"><span data-stu-id="697f6-192">Application (client) ID for the *Server API app*</span></span>      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`    | <span data-ttu-id="697f6-193">Uygulama KIMLIĞI URI 'SI&dagger;</span><span class="sxs-lookup"><span data-stu-id="697f6-193">Application ID URI&dagger;</span></span>                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{TENANT DOMAIN}`            | <span data-ttu-id="697f6-194">Birincil/yayımcı/kiracı etki alanı</span><span class="sxs-lookup"><span data-stu-id="697f6-194">Primary/Publisher/Tenant domain</span></span>                       | `contoso.onmicrosoft.com`                      |
| `{TENANT ID}`                | <span data-ttu-id="697f6-195">Dizin (kiracı) kimliği</span><span class="sxs-lookup"><span data-stu-id="697f6-195">Directory (tenant) ID</span></span>                                 | `e86c78e2-8bb4-4c41-aefd-918e0565a45e`         |

<span data-ttu-id="697f6-196">&dagger;Blazor WebAssemblyŞablon, `api://` komutta GEÇIRILEN uygulama kimliği URI bağımsız değişkenine otomatik olarak bir şeması ekler `dotnet new` .</span><span class="sxs-lookup"><span data-stu-id="697f6-196">&dagger;The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="697f6-197">Yer tutucu için uygulama KIMLIĞI URI 'SI sağlarken `{SERVER API APP ID URI}` ve düzen ise, `api://` `api://` Yukarıdaki tabloda örnek değer olarak gösterildiği gibi, bağımsız değişkenden düzeni () kaldırın.</span><span class="sxs-lookup"><span data-stu-id="697f6-197">When providing the App ID URI for the `{SERVER API APP ID URI}` placeholder and if the scheme is `api://`, remove the scheme (`api://`) from the argument, as the example value in the preceding table shows.</span></span> <span data-ttu-id="697f6-198">Uygulama KIMLIĞI URI 'SI özel bir değer ise veya başka bir şemaya sahipse (örneğin, `https://` güvenilir olmayan bir yayımcı etki alanı için `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` ), varsayılan kapsam URI 'sini el ile güncelleştirmeniz ve `api://` *`Client`* uygulama şablon tarafından oluşturulduktan sonra düzeni kaldırmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="697f6-198">If the App ID URI is a custom value or has some other scheme (for example, `https://` for an untrusted publisher domain similar to `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd`), you must manually update the default scope URI and remove the `api://` scheme after the *`Client`* app is created by the template.</span></span> <span data-ttu-id="697f6-199">Daha fazla bilgi için, [erişim belirteci kapsamları](#access-token-scopes) bölümündeki nota bakın.</span><span class="sxs-lookup"><span data-stu-id="697f6-199">For more information, see the note in the [Access token scopes](#access-token-scopes) section.</span></span> <span data-ttu-id="697f6-200">Blazor WebAssemblyŞablon bu senaryolara yönelik ASP.NET Core gelecek bir sürümünde değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="697f6-200">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="697f6-201">Daha fazla bilgi için bkz. [IStream şablonuyla uygulama kimliği URI 'si Için çift düzen ( Blazor barındırılan, tek kuruluş) (DotNet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="697f6-201">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="697f6-202">Seçeneğiyle belirtilen çıktı konumu, `-o|--output` mevcut değilse bir proje klasörü oluşturur ve uygulamanın adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="697f6-202">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="697f6-203">[Uygulama ayarları](#app-settings) bölümünde açıklanan, doğrulanmamış bir yayımcı etki alanı Ile bir Azure kiracısı kullanılırken bir yapılandırma değişikliği gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="697f6-203">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [App settings](#app-settings) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="697f6-204">[Erişim belirteci kapsamları](#access-token-scopes) bölümünde açıklanan, doğrulanmamış yayımcı etki alanı Ile bir Azure kiracısı kullanılırken bir yapılandırma değişikliği gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="697f6-204">A configuration change might be required when using an Azure tenant with an unverified publisher domain, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

::: moniker-end

> [!NOTE]
> <span data-ttu-id="697f6-205">Azure portal, *`Client`* uygulamanın platform yapılandırması **yeniden yönlendirme URI 'Si** , Kestrel sunucusunda varsayılan ayarlarla çalışan uygulamalar için bağlantı noktası 5001 için yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="697f6-205">In the Azure portal, the *`Client`* app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="697f6-206">*`Client`* Uygulama rastgele bir IIS Express bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktası **hata ayıklama** panelindeki *sunucu API 'si uygulamasının* özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="697f6-206">If the *`Client`* app is run on a random IIS Express port, the port for the app can be found in the *Server API app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="697f6-207">Bağlantı noktası, *`Client`* uygulamanın bilinen bağlantı noktasıyla daha önce yapılandırılmamışsa, *`Client`* Azure Portal uygulamanın kaydına dönün ve yenıden yönlendirme URI 'sini doğru bağlantı noktasıyla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="697f6-207">If the port wasn't configured earlier with the *`Client`* app's known port, return to the *`Client`* app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="697f6-208">*`Server`* Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="697f6-208">*`Server`* app configuration</span></span>

<span data-ttu-id="697f6-209">*Bu bölüm, çözümün uygulaması ile ilgilidir **`Server`** .*</span><span class="sxs-lookup"><span data-stu-id="697f6-209">*This section pertains to the solution's **`Server`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="697f6-210">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="697f6-210">Authentication package</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="697f6-211">Microsoft Platformu ile ASP.NET Core Web API 'Lerine yönelik kimlik doğrulama ve yetkilendirme desteği Identity Aşağıdaki paketler tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="697f6-211">The support for authenticating and authorizing calls to ASP.NET Core Web APIs with the Microsoft Identity Platform is provided by the following packages:</span></span>

* [`Microsoft.Identity.Web`](https://www.nuget.org/packages/Microsoft.Identity.Web)
* [`Microsoft.Identity.Web.UI`](https://www.nuget.org/packages/Microsoft.Identity.Web.UI)

```xml
<PackageReference Include="Microsoft.Identity.Web" Version="{VERSION}" />
<PackageReference Include="Microsoft.Identity.Web.UI" Version="{VERSION}" />
```

<span data-ttu-id="697f6-212">Yer tutucu için `{VERSION}` , uygulamanın paylaşılan Framework sürümüyle eşleşen en son kararlı sürümü paketin NuGet.org adresindeki **sürüm geçmişinde** bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="697f6-212">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at NuGet.org.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="697f6-213">ASP.NET Core Web API 'Lerine yönelik kimlik doğrulama ve yetkilendirme desteği paket tarafından sağlanır [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) :</span><span class="sxs-lookup"><span data-stu-id="697f6-213">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the [`Microsoft.AspNetCore.Authentication.AzureAD.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI) package:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="{VERSION}" />
```

<span data-ttu-id="697f6-214">Yer tutucu için `{VERSION}` , uygulamanın paylaşılan Framework sürümüyle eşleşen en son kararlı sürümü paketin [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI)adresindeki **sürüm geçmişinde** bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="697f6-214">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI).</span></span>

::: moniker-end

### <a name="authentication-service-support"></a><span data-ttu-id="697f6-215">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="697f6-215">Authentication service support</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="697f6-216">`AddAuthentication`Yöntemi, uygulama içinde kimlik doğrulama hizmetlerini ayarlar ve JWT taşıyıcı işleyicisini varsayılan kimlik doğrulama yöntemi olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="697f6-216">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="697f6-217"><xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A>Yöntemi, Web API 'Sini Microsoft Platformu v 2.0 ile korumak için hizmetleri yapılandırır Identity .</span><span class="sxs-lookup"><span data-stu-id="697f6-217">The <xref:Microsoft.Identity.Web.MicrosoftIdentityWebApiAuthenticationBuilderExtensions.AddMicrosoftIdentityWebApi%2A> method configures services to protect the web API with Microsoft Identity Platform v2.0.</span></span> <span data-ttu-id="697f6-218">Bu yöntem `AzureAd` , kimlik doğrulama seçeneklerini başlatmak için gerekli ayarlarla uygulamanın yapılandırmasında bir bölüm bekler.</span><span class="sxs-lookup"><span data-stu-id="697f6-218">This method expects an `AzureAd` section in the app's configuration with the necessary settings to initialize authentication options.</span></span>

```csharp
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddMicrosoftIdentityWebApi(Configuration.GetSection("AzureAd"));
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="697f6-219">`AddAuthentication`Yöntemi, uygulama içinde kimlik doğrulama hizmetlerini ayarlar ve JWT taşıyıcı işleyicisini varsayılan kimlik doğrulama yöntemi olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="697f6-219">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="697f6-220"><xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A>Yöntemi, Azure Active Directory tarafından yayılan belirteçleri doğrulamak için gereken JWT taşıyıcı işleyicisinde belirli parametreleri ayarlar:</span><span class="sxs-lookup"><span data-stu-id="697f6-220">The <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A> method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

::: moniker-end

<span data-ttu-id="697f6-221"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A><xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>aşağıdakileri doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="697f6-221"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> and <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> ensure that:</span></span>

* <span data-ttu-id="697f6-222">Uygulama, gelen isteklerde belirteçleri ayrıştırmaya ve doğrulamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="697f6-222">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="697f6-223">Uygun kimlik bilgileri olmadan korunan kaynağa erişmeye çalışan istekler başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="697f6-223">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a><span data-ttu-id="697f6-224">Kullanıcı. Identity . Ada</span><span class="sxs-lookup"><span data-stu-id="697f6-224">User.Identity.Name</span></span>

<span data-ttu-id="697f6-225">Varsayılan olarak, *`Server`* uygulama API 'si `User.Identity.Name` `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` talep türündeki değerle (örneğin, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ) doldurulur.</span><span class="sxs-lookup"><span data-stu-id="697f6-225">By default, the *`Server`* app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="697f6-226">Uygulamayı talep türünden değeri alacak şekilde yapılandırmak için, `name` <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> ' ın <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> ' de ' yi yapılandırın `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="697f6-226">To configure the app to receive the value from the `name` claim type, configure the <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="697f6-227">Uygulama ayarları</span><span class="sxs-lookup"><span data-stu-id="697f6-227">App settings</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="697f6-228">`appsettings.json`Dosya, erişim belirteçlerini doğrulamak için kullanılan JWT taşıyıcı işleyicisini yapılandırma seçeneklerini içerir:</span><span class="sxs-lookup"><span data-stu-id="697f6-228">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "CallbackPath": "/signin-oidc"
  }
}
```

<span data-ttu-id="697f6-229">Örnek:</span><span class="sxs-lookup"><span data-stu-id="697f6-229">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "CallbackPath": "/signin-oidc"
  }
}
```

[!INCLUDE[](~/includes/blazor-security/azure-scope-5x.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="697f6-230">`appsettings.json`Dosya, erişim belirteçlerini doğrulamak için kullanılan JWT taşıyıcı işleyicisini yapılandırma seçeneklerini içerir:</span><span class="sxs-lookup"><span data-stu-id="697f6-230">The `appsettings.json` file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

<span data-ttu-id="697f6-231">Örnek:</span><span class="sxs-lookup"><span data-stu-id="697f6-231">Example:</span></span>

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

::: moniker-end

### <a name="weatherforecast-controller"></a><span data-ttu-id="697f6-232">Hava tahmin denetleyicisi</span><span class="sxs-lookup"><span data-stu-id="697f6-232">WeatherForecast controller</span></span>

<span data-ttu-id="697f6-233">Dalgalı tahmin denetleyicisi ( *denetleyiciler/dalgalı Therforetcontroller. cs* ), BIR korumalı API 'yi [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) denetleyiciye uygulanmış şekilde gösterir.</span><span class="sxs-lookup"><span data-stu-id="697f6-233">The WeatherForecast controller ( *Controllers/WeatherForecastController.cs* ) exposes a protected API with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute applied to the controller.</span></span> <span data-ttu-id="697f6-234">Bunun anlaşılması **önemlidir** :</span><span class="sxs-lookup"><span data-stu-id="697f6-234">It's **important** to understand that:</span></span>

* <span data-ttu-id="697f6-235">Bu [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) API denetleyicisindeki özniteliği, bu API 'yi yetkisiz erişime karşı koruyan tek şeydir.</span><span class="sxs-lookup"><span data-stu-id="697f6-235">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="697f6-236">[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Uygulamada kullanılan özniteliği yalnızca uygulamanın, Blazor WebAssembly uygulamanın düzgün şekilde çalışması için yetkilendirilmiş olması gerektiğine yönelik bir ipucu görevi görür.</span><span class="sxs-lookup"><span data-stu-id="697f6-236">The [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="697f6-237">*`Client`* Uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="697f6-237">*`Client`* app configuration</span></span>

<span data-ttu-id="697f6-238">*Bu bölüm, çözümün uygulaması ile ilgilidir **`Client`** .*</span><span class="sxs-lookup"><span data-stu-id="697f6-238">*This section pertains to the solution's **`Client`** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="697f6-239">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="697f6-239">Authentication package</span></span>

<span data-ttu-id="697f6-240">Iş veya okul hesaplarını () kullanmak üzere bir uygulama oluşturulduğunda `SingleOrg` , uygulama [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) () için bir paket başvurusu otomatik olarak alır [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) .</span><span class="sxs-lookup"><span data-stu-id="697f6-240">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="697f6-241">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="697f6-241">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="697f6-242">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="697f6-242">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="697f6-243">Yer tutucu için `{VERSION}` , uygulamanın paylaşılan Framework sürümüyle eşleşen en son kararlı sürümü paketin [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)adresindeki **sürüm geçmişinde** bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="697f6-243">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="697f6-244">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)Paket geçişli [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) olarak uygulamayı uygulamaya ekler.</span><span class="sxs-lookup"><span data-stu-id="697f6-244">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="697f6-245">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="697f6-245">Authentication service support</span></span>

<span data-ttu-id="697f6-246"><xref:System.Net.Http.HttpClient>Sunucu projesine istek yaparken erişim belirteçlerini içeren örnekler için destek eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="697f6-246">Support for <xref:System.Net.Http.HttpClient> instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="697f6-247">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="697f6-247">`Program.cs`:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="697f6-248">Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `BlazorSample.ServerAPI` ).</span><span class="sxs-lookup"><span data-stu-id="697f6-248">The placeholder `{APP ASSEMBLY}` is the app's assembly name (for example, `BlazorSample.ServerAPI`).</span></span>

<span data-ttu-id="697f6-249">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> paket tarafından sağlanmış uzantı yöntemiyle kaydedilir [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) .</span><span class="sxs-lookup"><span data-stu-id="697f6-249">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="697f6-250">Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="697f6-250">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="697f6-251">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="697f6-251">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="697f6-252"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Yöntemi, bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="697f6-252">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="697f6-253">Uygulamanın yapılandırılması için gereken değerler, uygulamayı kaydettiğinizde Azure Portal AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="697f6-253">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="697f6-254">Yapılandırma dosya tarafından sağlanır `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="697f6-254">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="697f6-255">Örnek:</span><span class="sxs-lookup"><span data-stu-id="697f6-255">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="697f6-256">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="697f6-256">Access token scopes</span></span>

<span data-ttu-id="697f6-257">Varsayılan erişim belirteci kapsamları, erişim belirteci kapsamlarının listesini temsil eder:</span><span class="sxs-lookup"><span data-stu-id="697f6-257">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="697f6-258">Oturum açma isteğine varsayılan olarak dahildir.</span><span class="sxs-lookup"><span data-stu-id="697f6-258">Included by default in the sign in request.</span></span>
* <span data-ttu-id="697f6-259">Kimlik doğrulamasından hemen sonra bir erişim belirteci sağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="697f6-259">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="697f6-260">Tüm kapsamlar Azure Active Directory kuralları başına aynı uygulamaya ait olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="697f6-260">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="697f6-261">Gerektiğinde ek API uygulamaları için ek kapsamlar eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="697f6-261">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="697f6-262">Blazor WebAssemblyŞablon, `api://` komutta GEÇIRILEN uygulama kimliği URI bağımsız değişkenine otomatik olarak bir şeması ekler `dotnet new` .</span><span class="sxs-lookup"><span data-stu-id="697f6-262">The Blazor WebAssembly template automatically adds a scheme of `api://` to the App ID URI argument passed in the `dotnet new` command.</span></span> <span data-ttu-id="697f6-263">Proje şablonundan bir uygulama oluştururken Blazor , varsayılan erişim belirteci kapsamı değerinin Azure Portal belirttiğiniz doğru özel uygulama KIMLIĞI URI değerini ya da aşağıdaki biçimlerden **birine** sahip bir değeri kullandığını doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="697f6-263">When generating an app from the Blazor project template, confirm that the value of the default access token scope uses either the correct custom App ID URI value that you provided in the Azure portal or a value with **one** of the following formats:</span></span>
>
> * <span data-ttu-id="697f6-264">Dizinin yayımcı etki alanına **güvenilirse** , varsayılan erişim belirteci kapsamı genellikle aşağıdaki örneğe benzer bir değerdir; burada `API.Access` varsayılan kapsam adıdır:</span><span class="sxs-lookup"><span data-stu-id="697f6-264">When the publisher domain of the directory is **trusted** , the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="697f6-265">Çift düzen () için değeri inceleyin `api://api://...` .</span><span class="sxs-lookup"><span data-stu-id="697f6-265">Inspect the value for a double scheme (`api://api://...`).</span></span> <span data-ttu-id="697f6-266">Bir çift düzen varsa, ilk `api://` düzeni değerden kaldırın.</span><span class="sxs-lookup"><span data-stu-id="697f6-266">If a double scheme is present, remove the first `api://` scheme from the value.</span></span>
>
> * <span data-ttu-id="697f6-267">Dizinin yayımcı etki alanı **güvenilir** olmadığında, varsayılan erişim belirteci kapsamı genellikle aşağıdaki örneğe benzer bir değerdir; burada `API.Access` varsayılan kapsam adıdır:</span><span class="sxs-lookup"><span data-stu-id="697f6-267">When the publisher domain of the directory is **untrusted** , the default access token scope is typically a value similar to the following example, where `API.Access` is the default scope name:</span></span>
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   <span data-ttu-id="697f6-268">Ek bir `api://` düzenin () değerini inceleyin `api://https://contoso.onmicrosoft.com/...` .</span><span class="sxs-lookup"><span data-stu-id="697f6-268">Inspect the value for an extra `api://` scheme (`api://https://contoso.onmicrosoft.com/...`).</span></span> <span data-ttu-id="697f6-269">Ek bir `api://` düzen varsa, `api://` düzeni değerden kaldırın.</span><span class="sxs-lookup"><span data-stu-id="697f6-269">If an extra `api://` scheme is present, remove the `api://` scheme from the value.</span></span>
>
> <span data-ttu-id="697f6-270">Blazor WebAssemblyŞablon bu senaryolara yönelik ASP.NET Core gelecek bir sürümünde değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="697f6-270">The Blazor WebAssembly template might be changed in a future release of ASP.NET Core to address these scenarios.</span></span> <span data-ttu-id="697f6-271">Daha fazla bilgi için bkz. [IStream şablonuyla uygulama kimliği URI 'si Için çift düzen ( Blazor barındırılan, tek kuruluş) (DotNet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span><span class="sxs-lookup"><span data-stu-id="697f6-271">For more information, see [Double scheme for App ID URI with Blazor WASM template (hosted, single org) (dotnet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).</span></span>

<span data-ttu-id="697f6-272">İle ek kapsamlar belirtin `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="697f6-272">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/blazor-security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="697f6-273">Daha fazla bilgi için *ek senaryolar* makalesinin aşağıdaki bölümlerine bakın:</span><span class="sxs-lookup"><span data-stu-id="697f6-273">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="697f6-274">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="697f6-274">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="697f6-275">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="697f6-275">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a><span data-ttu-id="697f6-276">Oturum açma modu</span><span class="sxs-lookup"><span data-stu-id="697f6-276">Login mode</span></span>

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

### <a name="imports-file"></a><span data-ttu-id="697f6-277">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="697f6-277">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="697f6-278">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="697f6-278">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="697f6-279">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="697f6-279">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="697f6-280">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="697f6-280">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="697f6-281">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="697f6-281">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="697f6-282">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="697f6-282">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="697f6-283">FetchData bileşeni</span><span class="sxs-lookup"><span data-stu-id="697f6-283">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="697f6-284">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="697f6-284">Run the app</span></span>

<span data-ttu-id="697f6-285">Uygulamayı sunucu projesinden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="697f6-285">Run the app from the Server project.</span></span> <span data-ttu-id="697f6-286">Visual Studio 'yu kullanırken şunlardan birini yapın:</span><span class="sxs-lookup"><span data-stu-id="697f6-286">When using Visual Studio, either:</span></span>

* <span data-ttu-id="697f6-287">Araç çubuğundaki **başlangıç projeleri** açılan listesini *sunucu API 'si uygulamasına* ayarlayın ve **Çalıştır** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="697f6-287">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="697f6-288">**Çözüm Gezgini** ' de sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya uygulamayı **Hata Ayıkla** menüsünden başlatın.</span><span class="sxs-lookup"><span data-stu-id="697f6-288">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="697f6-289">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="697f6-289">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="697f6-290">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="697f6-290">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="697f6-291">Microsoft kimlik platformu belgeleri</span><span class="sxs-lookup"><span data-stu-id="697f6-291">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
