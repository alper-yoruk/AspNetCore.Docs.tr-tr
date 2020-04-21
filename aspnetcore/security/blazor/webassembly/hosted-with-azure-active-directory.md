---
title: Azure Active Blazor Directory ile ASP.NET Core WebAssembly barındırılan uygulamayı güvenli hale
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: a80be8d145b7c58be35e2c353a448db7e234e20b
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661810"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="1c589-102">Azure Active Blazor Directory ile ASP.NET Core WebAssembly barındırılan uygulamayı güvenli hale</span><span class="sxs-lookup"><span data-stu-id="1c589-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="1c589-103">Yazar: [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="1c589-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="1c589-104">Bu makalede, kimlik doğrulaması için [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) kullanan bir [ Blazor WebAssembly barındırılan uygulama](xref:blazor/hosting-models#blazor-webassembly) nasıl oluşturulacak açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="1c589-104">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="1c589-105">Uygulamaları AAD B2C'ye kaydedin ve çözüm oluşturun</span><span class="sxs-lookup"><span data-stu-id="1c589-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="1c589-106">Kiracı oluşturma</span><span class="sxs-lookup"><span data-stu-id="1c589-106">Create a tenant</span></span>

<span data-ttu-id="1c589-107">[Quickstart'taki](/azure/active-directory/develop/quickstart-create-new-tenant) kılavuzu izleyin: AAD'de kiracı oluşturmak için kiracı ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="1c589-107">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="1c589-108">Sunucu API uygulamasını kaydetme</span><span class="sxs-lookup"><span data-stu-id="1c589-108">Register a server API app</span></span>

<span data-ttu-id="1c589-109">[Quickstart'taki](/azure/active-directory/develop/quickstart-register-app) kılavuzu izleyin: Azure portalının **Azure Active Directory** > **App kayıtları** alanında *Sunucu API uygulaması* için bir AAD uygulaması kaydetmek için bir uygulamayı Microsoft kimlik platformuna ve sonraki Azure AAD konularına kaydedin:</span><span class="sxs-lookup"><span data-stu-id="1c589-109">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="1c589-110">**Yeni kayıt**seçin.</span><span class="sxs-lookup"><span data-stu-id="1c589-110">Select **New registration**.</span></span>
1. <span data-ttu-id="1c589-111">Uygulama için bir **Ad** sağlayın (örneğin, \*\* Blazor Sunucu AAD).\*\*</span><span class="sxs-lookup"><span data-stu-id="1c589-111">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="1c589-112">Desteklenen **hesap türlerini**seçin.</span><span class="sxs-lookup"><span data-stu-id="1c589-112">Choose a **Supported account types**.</span></span> <span data-ttu-id="1c589-113">Bu deneyim için **bu kuruluş dizinindeki Hesapları** yalnızca (tek kiracı) seçebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1c589-113">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="1c589-114">*Sunucu API uygulaması* bu senaryoda bir Yönlendirme **URI** gerektirmez, bu nedenle açılan kümeyi **Web'e** bırakın ve yeniden yönlendirme URI'si girmeyin.</span><span class="sxs-lookup"><span data-stu-id="1c589-114">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="1c589-115">**İzinleri** > devre dışı**kaldırın, Yönetici yi openid ve offline_access izinleri** onay kutusunu açmak için yoğunlaşın.</span><span class="sxs-lookup"><span data-stu-id="1c589-115">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="1c589-116">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="1c589-116">Select **Register**.</span></span>

<span data-ttu-id="1c589-117">**API izinlerinde,** uygulama oturum açma veya kullanıcı profili erişimi gerektirmediği için **Microsoft Graph** > **User.Read** iznini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="1c589-117">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or uer profile access.</span></span>

<span data-ttu-id="1c589-118">Bir **API ortaya çıkarmak:**</span><span class="sxs-lookup"><span data-stu-id="1c589-118">In **Expose an API**:</span></span>

1. <span data-ttu-id="1c589-119">**Kapsam ekle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="1c589-119">Select **Add a scope**.</span></span>
1. <span data-ttu-id="1c589-120">**Kaydet ve devam et**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="1c589-120">Select **Save and continue**.</span></span>
1. <span data-ttu-id="1c589-121">Kapsam **adı** sağlayın (örneğin, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="1c589-121">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="1c589-122">Yönetici **onay görüntüleme adı** sağlayın `Access API`(örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="1c589-122">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="1c589-123">Yönetici **onayı açıklaması** sağlayın (örneğin, `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="1c589-123">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="1c589-124">**Durum'un** **Etkin**olarak ayarladığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="1c589-124">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="1c589-125">**Kapsam Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="1c589-125">Select **Add scope**.</span></span>

<span data-ttu-id="1c589-126">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="1c589-126">Record the following information:</span></span>

* <span data-ttu-id="1c589-127">*Sunucu API uygulaması* Uygulama Kimliği (İstemci Kimliği) (örneğin, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="1c589-127">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="1c589-128">Uygulama Kimliği URI (örneğin, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, veya sağladığınız özel değer)</span><span class="sxs-lookup"><span data-stu-id="1c589-128">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="1c589-129">Dizin Kimliği (Kiracı Kimliği) `222222222-2222-2222-2222-222222222222`(örneğin, )</span><span class="sxs-lookup"><span data-stu-id="1c589-129">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="1c589-130">AAD Kiracı etki alanı `contoso.onmicrosoft.com`(örneğin, )</span><span class="sxs-lookup"><span data-stu-id="1c589-130">AAD Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>
* <span data-ttu-id="1c589-131">Varsayılan kapsam (örneğin, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="1c589-131">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="1c589-132">İstemci uygulamasını kaydetme</span><span class="sxs-lookup"><span data-stu-id="1c589-132">Register a client app</span></span>

<span data-ttu-id="1c589-133">[Quickstart'taki](/azure/active-directory/develop/quickstart-register-app) kılavuzu izleyin: Azure portalının **Azure Active Directory** > **App kayıtları** alanında Müşteri uygulaması için bir AAD *uygulaması* kaydetmek için bir uygulamayı Microsoft kimlik platformuna ve sonraki Azure AAD konularına kaydedin:</span><span class="sxs-lookup"><span data-stu-id="1c589-133">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="1c589-134">**Yeni kayıt**seçin.</span><span class="sxs-lookup"><span data-stu-id="1c589-134">Select **New registration**.</span></span>
1. <span data-ttu-id="1c589-135">Uygulama için bir **Ad** sağlayın (örneğin, \*\* Blazor İstemci AAD).\*\*</span><span class="sxs-lookup"><span data-stu-id="1c589-135">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="1c589-136">Desteklenen **hesap türlerini**seçin.</span><span class="sxs-lookup"><span data-stu-id="1c589-136">Choose a **Supported account types**.</span></span> <span data-ttu-id="1c589-137">Bu deneyim için **bu kuruluş dizinindeki Hesapları** yalnızca (tek kiracı) seçebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1c589-137">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="1c589-138">**Redirect URI** damlasını **Web'e**bırak ve uri'yi yeniden yönlendirmeyi `https://localhost:5001/authentication/login-callback`sağlar.</span><span class="sxs-lookup"><span data-stu-id="1c589-138">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="1c589-139">**İzinleri** > devre dışı**kaldırın, Yönetici yi openid ve offline_access izinleri** onay kutusunu açmak için yoğunlaşın.</span><span class="sxs-lookup"><span data-stu-id="1c589-139">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="1c589-140">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="1c589-140">Select **Register**.</span></span>

<span data-ttu-id="1c589-141">**Kimlik** > Doğrulama**Platformu yapılandırmalarında** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="1c589-141">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="1c589-142">**Redirect** URI'nin `https://localhost:5001/authentication/login-callback` mevcut olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="1c589-142">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="1c589-143">**Örtülü hibe**için, Erişim **belirteçleri** ve **kimlik belirteçleri**için onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="1c589-143">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="1c589-144">Uygulama için kalan varsayılanlar bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="1c589-144">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="1c589-145">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="1c589-145">Select the **Save** button.</span></span>

<span data-ttu-id="1c589-146">**API izinlerinde:**</span><span class="sxs-lookup"><span data-stu-id="1c589-146">In **API permissions**:</span></span>

1. <span data-ttu-id="1c589-147">Uygulamanın Microsoft **Graph** > **User.Read** iznine sahip olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="1c589-147">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="1c589-148">**API'lerim'i**izleyen **bir izin ekle'yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="1c589-148">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="1c589-149">**Ad** sütunundan *Sunucu API uygulamasını* seçin (örneğin, \*\* Blazor Sunucu AAD).\*\*</span><span class="sxs-lookup"><span data-stu-id="1c589-149">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="1c589-150">**API** listesini açın.</span><span class="sxs-lookup"><span data-stu-id="1c589-150">Open the **API** list.</span></span>
1. <span data-ttu-id="1c589-151">API'ye erişimi etkinleştirin `API.Access`(örneğin. ).</span><span class="sxs-lookup"><span data-stu-id="1c589-151">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="1c589-152">**İzin Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="1c589-152">Select **Add permissions**.</span></span>
1. <span data-ttu-id="1c589-153">**{KIRACı ADI}** düğmesi için Hibe yöneticisi içeriğini seçin.</span><span class="sxs-lookup"><span data-stu-id="1c589-153">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="1c589-154">Onaylamak için **Evet**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="1c589-154">Select **Yes** to confirm.</span></span>

<span data-ttu-id="1c589-155">*İstemci uygulaması* Uygulama Kimliğini (İstemci `33333333-3333-3333-3333-333333333333`Kimliği) kaydetme (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="1c589-155">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="1c589-156">Uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="1c589-156">Create the app</span></span>

<span data-ttu-id="1c589-157">Aşağıdaki komuttaki yer tutucuları daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğunda uygulayın:</span><span class="sxs-lookup"><span data-stu-id="1c589-157">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="1c589-158">Yoksa proje klasörü oluşturan çıktı konumunu belirtmek için, çıkış seçeneğini bir yolu olan komuta ekleyin `-o BlazorSample`(örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="1c589-158">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="1c589-159">Klasör adı da projenin adının bir parçası olur.</span><span class="sxs-lookup"><span data-stu-id="1c589-159">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="1c589-160">App ID URI'yi `app-id-uri` seçenegeç, ancak [Erişim belirteç kapsamları](#access-token-scopes) bölümünde açıklanan istemci uygulamasında bir yapılandırma değişikliği gerekebileceğini unutmayın.</span><span class="sxs-lookup"><span data-stu-id="1c589-160">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="1c589-161">Sunucu uygulaması yapılandırması</span><span class="sxs-lookup"><span data-stu-id="1c589-161">Server app configuration</span></span>

<span data-ttu-id="1c589-162">*Bu bölüm çözümün **Server** uygulamasıyla ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="1c589-162">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="1c589-163">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="1c589-163">Authentication package</span></span>

<span data-ttu-id="1c589-164">ASP.NET Çekirdek Web API'lerine yapılan çağrıları doğrulamak ve yetkilendirmek için destek `Microsoft.AspNetCore.Authentication.AzureAD.UI`aşağıdakiler tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="1c589-164">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="1c589-165">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="1c589-165">Authentication service support</span></span>

<span data-ttu-id="1c589-166">Yöntem, `AddAuthentication` uygulama içinde kimlik doğrulama hizmetleri ayarlar ve Varsayılan kimlik doğrulama yöntemi olarak JWT Taşıyıcı işleyicisi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="1c589-166">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="1c589-167">Yöntem, `AddAzureADBearer` Azure Etkin Dizini tarafından yayılan belirteçleri doğrulamak için gereken JWT Taşıyıcı işleyicisindeki belirli parametreleri ayarlar:</span><span class="sxs-lookup"><span data-stu-id="1c589-167">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="1c589-168">`UseAuthentication`ve `UseAuthorization` şunları sağlamak:</span><span class="sxs-lookup"><span data-stu-id="1c589-168">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="1c589-169">Uygulama, gelen istekler üzerindeki belirteçleri ayrışdırmaya ve doğrulamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="1c589-169">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="1c589-170">Uygun kimlik bilgileri olmadan korumalı bir kaynağa erişmeye çalışan tüm istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="1c589-170">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="1c589-171">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="1c589-171">User.Identity.Name</span></span>

<span data-ttu-id="1c589-172">Varsayılan olarak, Sunucu uygulaması `User.Identity.Name` `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` API'si talep türünden (örneğin,) `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`değerle birlikte doldurulur.</span><span class="sxs-lookup"><span data-stu-id="1c589-172">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="1c589-173">Uygulamanın `name` talep türünden değer alacak şekilde yapılandırılması için, [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) in: <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="1c589-173">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="1c589-174">Uygulama ayarları</span><span class="sxs-lookup"><span data-stu-id="1c589-174">App settings</span></span>

<span data-ttu-id="1c589-175">*appsettings.json* dosyası, erişim belirteçlerini doğrulamak için kullanılan JWT taşıyıcı işleyicisini yapılandırma seçeneklerini içerir.</span><span class="sxs-lookup"><span data-stu-id="1c589-175">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="1c589-176">WeatherForecast denetleyicisi</span><span class="sxs-lookup"><span data-stu-id="1c589-176">WeatherForecast controller</span></span>

<span data-ttu-id="1c589-177">WeatherForecast denetleyicisi *(Controllers/WeatherForecastController.cs)* denetleyiciye uygulanan `[Authorize]` öznitelik ile korunan bir API ortaya çıkarır.</span><span class="sxs-lookup"><span data-stu-id="1c589-177">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="1c589-178">Şunu anlamak **önemlidir:**</span><span class="sxs-lookup"><span data-stu-id="1c589-178">It's **important** to understand that:</span></span>

* <span data-ttu-id="1c589-179">`[Authorize]` Bu API denetleyicisindeki öznitelik, bu API'yi yetkisiz erişimden koruyan tek şeydir.</span><span class="sxs-lookup"><span data-stu-id="1c589-179">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="1c589-180">`[Authorize]` WebAssembly uygulamasında kullanılan öznitelik, yalnızca uygulamanın doğru çalışması için kullanıcıya yetki verilmesi gerektiğine dair bir ipucu görevi göremez. Blazor</span><span class="sxs-lookup"><span data-stu-id="1c589-180">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="1c589-181">İstemci uygulaması yapılandırması</span><span class="sxs-lookup"><span data-stu-id="1c589-181">Client app configuration</span></span>

<span data-ttu-id="1c589-182">*Bu bölüm çözümün **İstemci** uygulamasıyla ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="1c589-182">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="1c589-183">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="1c589-183">Authentication package</span></span>

<span data-ttu-id="1c589-184">İş veya Okul Hesaplarını kullanmak için`SingleOrg`bir uygulama oluşturulduğunda ( ), uygulama otomatik olarak`Microsoft.Authentication.WebAssembly.Msal`Microsoft Kimlik Doğrulama [Kitaplığı](/azure/active-directory/develop/msal-overview) için bir paket başvurusu alır ( ).</span><span class="sxs-lookup"><span data-stu-id="1c589-184">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="1c589-185">Paket, uygulamanın kullanıcıları doğrulamasına ve korumalı API'leri aramak için belirteçler almasına yardımcı olan bir dizi ilkel lik sağlar.</span><span class="sxs-lookup"><span data-stu-id="1c589-185">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="1c589-186">Bir uygulamaya kimlik doğrulama ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="1c589-186">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="1c589-187">Önceki `{VERSION}` paket başvuruyu makalede gösterilen `Microsoft.AspNetCore.Blazor.Templates` paketin sürümüyle <xref:blazor/get-started> değiştirin.</span><span class="sxs-lookup"><span data-stu-id="1c589-187">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="1c589-188">Paket, `Microsoft.Authentication.WebAssembly.Msal` `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paketi geçici olarak uygulamaya ekler.</span><span class="sxs-lookup"><span data-stu-id="1c589-188">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="1c589-189">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="1c589-189">Authentication service support</span></span>

<span data-ttu-id="1c589-190">Kullanıcıların kimlik doğrulaması için destek, paket `AddMsalAuthentication` tarafından sağlanan uzantı yöntemiyle servis kapsayıcısında `Microsoft.Authentication.WebAssembly.Msal` kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="1c589-190">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="1c589-191">Bu yöntem, uygulamanın Kimlik Sağlayıcısı (IP) ile etkileşim kurması için gereken tüm hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="1c589-191">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="1c589-192">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="1c589-192">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="1c589-193">Yöntem, `AddMsalAuthentication` bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri arama kabul eder.</span><span class="sxs-lookup"><span data-stu-id="1c589-193">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="1c589-194">Uygulamayı yapılandırmak için gereken değerler, uygulamayı kaydettirdiğinizde Azure Portal AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="1c589-194">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="1c589-195">Belirteç kapsamlarına erişim</span><span class="sxs-lookup"><span data-stu-id="1c589-195">Access token scopes</span></span>

<span data-ttu-id="1c589-196">Varsayılan erişim belirteç kapsamları, aşağıdakiler için erişim belirteç kapsamları listesini temsil eder:</span><span class="sxs-lookup"><span data-stu-id="1c589-196">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="1c589-197">Oturum açma isteğine varsayılan olarak dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="1c589-197">Included by default in the sign in request.</span></span>
* <span data-ttu-id="1c589-198">Kimlik doğrulamadan hemen sonra bir erişim belirteci sağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1c589-198">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="1c589-199">Tüm kapsamlar Azure Etkin Dizin kurallarına göre aynı uygulamaya ait olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="1c589-199">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="1c589-200">Gerektiğinde ek API uygulamaları için ek kapsamlar eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="1c589-200">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="1c589-201">Azure portalı bir kapsam oluşturuyorsa URI ve uygulama API'den *401 Yetkisiz* yanıt aldığında **işlenmemiş bir özel durum atarsa,** düzeni ve ana bilgisayarı içermeyen bir KAPSAM URI kullanmayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="1c589-201">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="1c589-202">Örneğin, Azure portalı aşağıdaki kapsam URI biçimlerinden birini sağlayabilir:</span><span class="sxs-lookup"><span data-stu-id="1c589-202">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="1c589-203">Şema ve ana bilgisayar olmadan URI kapsamını tedarik edin:</span><span class="sxs-lookup"><span data-stu-id="1c589-203">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="1c589-204">Daha fazla bilgi için bkz. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="1c589-204">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

### <a name="imports-file"></a><span data-ttu-id="1c589-205">Dosyayı alma</span><span class="sxs-lookup"><span data-stu-id="1c589-205">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="1c589-206">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="1c589-206">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="1c589-207">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="1c589-207">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="1c589-208">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="1c589-208">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="1c589-209">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="1c589-209">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="1c589-210">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="1c589-210">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="1c589-211">FetchData bileşeni</span><span class="sxs-lookup"><span data-stu-id="1c589-211">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="1c589-212">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="1c589-212">Run the app</span></span>

<span data-ttu-id="1c589-213">Uygulamayı Sunucu projesinden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="1c589-213">Run the app from the Server project.</span></span> <span data-ttu-id="1c589-214">Visual Studio'yu kullanırken, **Solution Explorer'daki** Sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya **Hata Ayıklama** menüsünden uygulamayı başlatın.</span><span class="sxs-lookup"><span data-stu-id="1c589-214">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="1c589-215">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1c589-215">Additional resources</span></span>

* [<span data-ttu-id="1c589-216">Ek erişim belirteçleri isteme</span><span class="sxs-lookup"><span data-stu-id="1c589-216">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="1c589-217">Microsoft kimlik platformu belgeleri</span><span class="sxs-lookup"><span data-stu-id="1c589-217">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
