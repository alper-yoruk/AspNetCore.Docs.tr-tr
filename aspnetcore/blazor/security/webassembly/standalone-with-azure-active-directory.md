---
title: Blazor WebAssemblyAzure Active Directory ile ASP.NET Core tek başına uygulamanın güvenliğini sağlama
author: guardrex
description: Blazor WebAssemblyAzure Active Directory ile ASP.NET Core tek başına uygulamanın güvenliğini nasıl sağlayacağınızı öğrenin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: devx-track-csharp, mvc
ms.date: 10/27/2020
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
uid: blazor/security/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: eb5bdb046c4360d1cd33a81c569c86b991e7729d
ms.sourcegitcommit: da5a5bed5718a9f8db59356ef8890b4b60ced6e9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98710548"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="9fe7e-103">Blazor WebAssemblyAzure Active Directory ile ASP.NET Core tek başına uygulamanın güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="9fe7e-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="9fe7e-104">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="9fe7e-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9fe7e-105">Bu makalede, kimlik doğrulaması için [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) kullanan [tek başına bir Blazor WebAssembly uygulamanın](xref:blazor/hosting-models#blazor-webassembly) nasıl oluşturulacağı ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-105">This article covers how to create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="9fe7e-106">Visual Blazor WebAssembly Studio 'da oluşturulan ve bır AAD kurumsal dizinindeki hesapları destekleyecek şekilde yapılandırılan uygulamalar için, Visual Studio şu anda proje oluşturma sırasında Azure Portal uygulama kayıtlarını doğru şekilde yapılandırmaz.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-106">For Blazor WebAssembly apps created in Visual Studio that are configured to support accounts in an AAD organizational directory, Visual Studio doesn't currently configure Azure portal app registrations correctly on project generation.</span></span> <span data-ttu-id="9fe7e-107">Bu, Visual Studio 'nun gelecek bir sürümünde değinilecek.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-107">This will be addressed in a future release of Visual Studio.</span></span>
>
> <span data-ttu-id="9fe7e-108">Bu makalede, .NET CLı komutuyla çözüm ve Azure App Portal kaydı oluşturma `dotnet new` ve Azure Portal uygulama kaydını el ile oluşturma işlemlerinin nasıl yapılacağı gösterilir.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-108">This article shows how to create the solution and Azure app portal registration with the .NET CLI `dotnet new` command and by manually creating the app registration in the Azure portal.</span></span>
>
> <span data-ttu-id="9fe7e-109">IDE güncelleştirildikten önce çözümü ve Visual Studio ile Azure Uygulama kaydını oluşturmayı tercih ediyorsanız, **_Bu makalenin her bölümüne_** başvurun ve Visual Studio çözümü oluşturduktan sonra uygulamanın yapılandırmasını ve uygulamanın kaydını onaylayın veya güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-109">If you prefer to create the solution and Azure app registration with Visual Studio before the IDE is updated, refer to **_each section of this article_** and confirm or update the app's configuration and the app's registration after Visual Studio creates the solution.</span></span>

<span data-ttu-id="9fe7e-110">Azure Portal **Azure Active Directory** > **uygulama kayıtları** alanına bir AAD uygulaması kaydedin:</span><span class="sxs-lookup"><span data-stu-id="9fe7e-110">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="9fe7e-111">Uygulama için bir **ad** sağlayın (örneğin, **Blazor tek başına AAD**).</span><span class="sxs-lookup"><span data-stu-id="9fe7e-111">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="9fe7e-112">Desteklenen bir **Hesap türü** seçin.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="9fe7e-113">Bu **kuruluş dizininde yalnızca** bu deneyim için hesaplar seçebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-113">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="9fe7e-114">**Yeniden yönlendirme URI 'si** açılan öğesini **tek SAYFALı uygulama (Spa)** olarak ayarlayın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="9fe7e-114">Set the **Redirect URI** drop down to **Single-page application (SPA)** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="9fe7e-115">Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="9fe7e-116">Uygulama farklı bir Kestrel bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktasını kullanın.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-116">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="9fe7e-117">IIS Express için, uygulama için rastgele oluşturulan bağlantı noktası, **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-117">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="9fe7e-118">Uygulama bu noktada mevcut olmadığından ve IIS Express bağlantı noktası bilinmediğinden, uygulama oluşturulduktan sonra bu adıma geri dönün ve yeniden yönlendirme URI 'sini güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-118">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="9fe7e-119">Bu konunun ilerleyen kısımlarında bir açıklama görüntülenerek IIS Express kullanıcıların yeniden yönlendirme URI 'sini güncelleştirmesini hatırlatır.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-119">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="9fe7e-120"> > **OpenID ve offline_access izinleri için yönetici onayı verme** izinleri onay kutusunu temizleyin.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-120">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="9fe7e-121">**Kaydet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-121">Select **Register**.</span></span>

<span data-ttu-id="9fe7e-122">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="9fe7e-122">Record the following information:</span></span>

* <span data-ttu-id="9fe7e-123">Uygulama (istemci) KIMLIĞI (örneğin, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="9fe7e-123">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="9fe7e-124">Dizin (kiracı) KIMLIĞI (örneğin, `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="9fe7e-124">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="9fe7e-125">**Kimlik doğrulama** > **platformu yapılandırmalarında** > **tek sayfalı uygulama (Spa)**:</span><span class="sxs-lookup"><span data-stu-id="9fe7e-125">In **Authentication** > **Platform configurations** > **Single-page application (SPA)**:</span></span>

1. <span data-ttu-id="9fe7e-126">**Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-126">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="9fe7e-127">**Örtük verme** Için, **erişim belirteçleri** ve **Kimlik belirteçleri** onay kutularının seçili **olmadığından** emin olun.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-127">For **Implicit grant**, ensure that the check boxes for **Access tokens** and **ID tokens** are **not** selected.</span></span>
1. <span data-ttu-id="9fe7e-128">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-128">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="9fe7e-129">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-129">Select the **Save** button.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="9fe7e-130">Azure Portal **Azure Active Directory** > **uygulama kayıtları** alanına bir AAD uygulaması kaydedin:</span><span class="sxs-lookup"><span data-stu-id="9fe7e-130">Register an AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="9fe7e-131">Uygulama için bir **ad** sağlayın (örneğin, **Blazor tek başına AAD**).</span><span class="sxs-lookup"><span data-stu-id="9fe7e-131">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="9fe7e-132">Desteklenen bir **Hesap türü** seçin.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-132">Choose a **Supported account types**.</span></span> <span data-ttu-id="9fe7e-133">Bu **kuruluş dizininde yalnızca** bu deneyim için hesaplar seçebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-133">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="9fe7e-134">**Yeniden yönlendirme URI 'si** açılan öğesini **Web** 'e ayarlı bırakın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="9fe7e-134">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="9fe7e-135">Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-135">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="9fe7e-136">Uygulama farklı bir Kestrel bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktasını kullanın.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-136">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="9fe7e-137">IIS Express için, uygulama için rastgele oluşturulan bağlantı noktası, **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-137">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="9fe7e-138">Uygulama bu noktada mevcut olmadığından ve IIS Express bağlantı noktası bilinmediğinden, uygulama oluşturulduktan sonra bu adıma geri dönün ve yeniden yönlendirme URI 'sini güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-138">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="9fe7e-139">Bu konunun ilerleyen kısımlarında bir açıklama görüntülenerek IIS Express kullanıcıların yeniden yönlendirme URI 'sini güncelleştirmesini hatırlatır.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-139">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="9fe7e-140"> > **OpenID ve offline_access izinleri için yönetici onayı verme** izinleri onay kutusunu temizleyin.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-140">Clear the **Permissions** > **Grant admin consent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="9fe7e-141">**Kaydet**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-141">Select **Register**.</span></span>

<span data-ttu-id="9fe7e-142">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="9fe7e-142">Record the following information:</span></span>

* <span data-ttu-id="9fe7e-143">Uygulama (istemci) KIMLIĞI (örneğin, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )</span><span class="sxs-lookup"><span data-stu-id="9fe7e-143">Application (client) ID (for example, `41451fa7-82d9-4673-8fa5-69eff5a761fd`)</span></span>
* <span data-ttu-id="9fe7e-144">Dizin (kiracı) KIMLIĞI (örneğin, `e86c78e2-8bb4-4c41-aefd-918e0565a45e` )</span><span class="sxs-lookup"><span data-stu-id="9fe7e-144">Directory (tenant) ID (for example, `e86c78e2-8bb4-4c41-aefd-918e0565a45e`)</span></span>

<span data-ttu-id="9fe7e-145">**Kimlik doğrulama** > **platformu yapılandırması** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="9fe7e-145">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="9fe7e-146">**Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-146">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="9fe7e-147">**Örtük izin** Için, **erişim belirteçleri** ve **Kimlik belirteçleri** onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-147">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="9fe7e-148">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-148">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="9fe7e-149">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-149">Select the **Save** button.</span></span>

::: moniker-end

<span data-ttu-id="9fe7e-150">Uygulamayı boş bir klasörde oluşturun.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-150">Create the app in an empty folder.</span></span> <span data-ttu-id="9fe7e-151">Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:</span><span class="sxs-lookup"><span data-stu-id="9fe7e-151">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" -o {APP NAME} --tenant-id "{TENANT ID}"
```

| <span data-ttu-id="9fe7e-152">Yer tutucu</span><span class="sxs-lookup"><span data-stu-id="9fe7e-152">Placeholder</span></span>   | <span data-ttu-id="9fe7e-153">Azure portal adı</span><span class="sxs-lookup"><span data-stu-id="9fe7e-153">Azure portal name</span></span>       | <span data-ttu-id="9fe7e-154">Örnek</span><span class="sxs-lookup"><span data-stu-id="9fe7e-154">Example</span></span>                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | <span data-ttu-id="9fe7e-155">Uygulama (istemci) kimliği</span><span class="sxs-lookup"><span data-stu-id="9fe7e-155">Application (client) ID</span></span> | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{TENANT ID}` | <span data-ttu-id="9fe7e-156">Dizin (kiracı) kimliği</span><span class="sxs-lookup"><span data-stu-id="9fe7e-156">Directory (tenant) ID</span></span>   | `e86c78e2-8bb4-4c41-aefd-918e0565a45e` |

<span data-ttu-id="9fe7e-157">Seçeneğiyle belirtilen çıktı konumu, `-o|--output` mevcut değilse bir proje klasörü oluşturur ve uygulamanın adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-157">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

> [!NOTE]
> <span data-ttu-id="9fe7e-158">Azure portal, uygulamanın platform yapılandırması **yeniden yönlendirme URI 'si** , Kestrel sunucusunda varsayılan ayarlarla çalışan uygulamalar için bağlantı noktası 5001 için yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-158">In the Azure portal, the app's platform configuration **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="9fe7e-159">Uygulama rastgele bir IIS Express bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktası **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-159">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="9fe7e-160">Bağlantı noktası, uygulamanın bilinen bağlantı noktasıyla daha önce yapılandırılmamışsa, Azure portal uygulamanın kaydına dönün ve yeniden yönlendirme URI 'sini doğru bağlantı noktasıyla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-160">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/additional-scopes-standalone-AAD.md)]

::: moniker-end

<span data-ttu-id="9fe7e-161">Uygulamayı oluşturduktan sonra şunları yapmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="9fe7e-161">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="9fe7e-162">AAD Kullanıcı hesabını kullanarak uygulamada oturum açın.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-162">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="9fe7e-163">Microsoft API 'Leri için erişim belirteçleri isteyin.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-163">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="9fe7e-164">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-164">For more information, see:</span></span>
  * [<span data-ttu-id="9fe7e-165">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="9fe7e-165">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="9fe7e-166">[Hızlı başlangıç: Web API 'lerini kullanıma sunmak için bir uygulama yapılandırma](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="9fe7e-166">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="9fe7e-167">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="9fe7e-167">Authentication package</span></span>

<span data-ttu-id="9fe7e-168">Iş veya okul hesaplarını () kullanmak üzere bir uygulama oluşturulduğunda `SingleOrg` , uygulama [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) () için bir paket başvurusu otomatik olarak alır [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) .</span><span class="sxs-lookup"><span data-stu-id="9fe7e-168">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) ([`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)).</span></span> <span data-ttu-id="9fe7e-169">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-169">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="9fe7e-170">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9fe7e-170">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

<span data-ttu-id="9fe7e-171">Yer tutucu için `{VERSION}` , uygulamanın paylaşılan Framework sürümüyle eşleşen en son kararlı sürümü paketin [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)adresindeki **sürüm geçmişinde** bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-171">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal).</span></span>

<span data-ttu-id="9fe7e-172">[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)Paket geçişli [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) olarak uygulamayı uygulamaya ekler.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-172">The [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package transitively adds the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="9fe7e-173">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="9fe7e-173">Authentication service support</span></span>

<span data-ttu-id="9fe7e-174">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> paket tarafından sağlanmış uzantı yöntemiyle kaydedilir [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) .</span><span class="sxs-lookup"><span data-stu-id="9fe7e-174">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> extension method provided by the [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) package.</span></span> <span data-ttu-id="9fe7e-175">Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-175">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="9fe7e-176">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="9fe7e-176">`Program.cs`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="9fe7e-177"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Yöntemi, bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-177">The <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="9fe7e-178">Uygulamayı yapılandırmak için gereken değerler, uygulamayı kaydettiğinizde AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-178">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="9fe7e-179">Yapılandırma dosya tarafından sağlanır `wwwroot/appsettings.json` :</span><span class="sxs-lookup"><span data-stu-id="9fe7e-179">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="9fe7e-180">Örnek:</span><span class="sxs-lookup"><span data-stu-id="9fe7e-180">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="9fe7e-181">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="9fe7e-181">Access token scopes</span></span>

<span data-ttu-id="9fe7e-182">Blazor WebAssemblyŞablon, uygulamayı güvenli BIR API için erişim belirteci isteyecek şekilde otomatik olarak yapılandırmaz.</span><span class="sxs-lookup"><span data-stu-id="9fe7e-182">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="9fe7e-183">Oturum açma akışının bir parçası olarak bir erişim belirteci sağlamak için, kapsamı varsayılan erişim belirteci kapsamlarına ekleyin <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :</span><span class="sxs-lookup"><span data-stu-id="9fe7e-183">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions>:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="9fe7e-184">İle ek kapsamlar belirtin `AdditionalScopesToConsent` :</span><span class="sxs-lookup"><span data-stu-id="9fe7e-184">Specify additional scopes with `AdditionalScopesToConsent`:</span></span>

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/blazor/includes/security/azure-scope-3x.md)]

::: moniker-end

<span data-ttu-id="9fe7e-185">Daha fazla bilgi için *ek senaryolar* makalesinin aşağıdaki bölümlerine bakın:</span><span class="sxs-lookup"><span data-stu-id="9fe7e-185">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="9fe7e-186">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="9fe7e-186">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="9fe7e-187">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="9fe7e-187">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a><span data-ttu-id="9fe7e-188">Oturum açma modu</span><span class="sxs-lookup"><span data-stu-id="9fe7e-188">Login mode</span></span>

[!INCLUDE[](~/blazor/includes/security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a><span data-ttu-id="9fe7e-189">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="9fe7e-189">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="9fe7e-190">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="9fe7e-190">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="9fe7e-191">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="9fe7e-191">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="9fe7e-192">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="9fe7e-192">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="9fe7e-193">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="9fe7e-193">LoginDisplay component</span></span>

[!INCLUDE[](~/blazor/includes/security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="9fe7e-194">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="9fe7e-194">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="9fe7e-195">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9fe7e-195">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="9fe7e-196">Authentication. MSAL JavaScript kitaplığının özel bir sürümünü oluşturun</span><span class="sxs-lookup"><span data-stu-id="9fe7e-196">Build a custom version of the Authentication.MSAL JavaScript library</span></span>](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [<span data-ttu-id="9fe7e-197">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="9fe7e-197">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="9fe7e-198">Microsoft kimlik platformu belgeleri</span><span class="sxs-lookup"><span data-stu-id="9fe7e-198">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
