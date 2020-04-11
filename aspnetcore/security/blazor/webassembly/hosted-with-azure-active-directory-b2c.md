---
title: Azure Active Blazor Directory B2C ile ASP.NET Core WebAssembly barındırılan uygulamayı güvenli hale
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: e2bb0c1bd807d590331b714e3b80d8c4ab434e2f
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123465"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="609ee-102">Azure Active Blazor Directory B2C ile ASP.NET Core WebAssembly barındırılan uygulamayı güvenli hale</span><span class="sxs-lookup"><span data-stu-id="609ee-102">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="609ee-103">Yazar: [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="609ee-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

<span data-ttu-id="609ee-104">Bu makalede, kimlik Blazor doğrulaması için Azure [Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) kullanan bir WebAssembly bağımsız uygulaması nasıl oluşturulacak açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="609ee-104">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="609ee-105">Uygulamaları AAD B2C'ye kaydedin ve çözüm oluşturun</span><span class="sxs-lookup"><span data-stu-id="609ee-105">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="609ee-106">Kiracı oluşturma</span><span class="sxs-lookup"><span data-stu-id="609ee-106">Create a tenant</span></span>

<span data-ttu-id="609ee-107">Öğretici: AAD B2C kiracısı oluşturmak ve aşağıdaki bilgileri kaydetmek için [Bir Azure Etkin Dizin B2C kiracısı oluşturun: Öğretici'deki](/azure/active-directory-b2c/tutorial-create-tenant) kılavuzu izleyin:</span><span class="sxs-lookup"><span data-stu-id="609ee-107">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant and record the following information:</span></span>

* <span data-ttu-id="609ee-108">AAD B2C örneği (örneğin, `https://contoso.b2clogin.com/`sondaki eğik çizgiyi içerir)</span><span class="sxs-lookup"><span data-stu-id="609ee-108">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="609ee-109">AAD B2C Kiracı etki `contoso.onmicrosoft.com`alanı (örneğin, )</span><span class="sxs-lookup"><span data-stu-id="609ee-109">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="609ee-110">Sunucu API uygulamasını kaydetme</span><span class="sxs-lookup"><span data-stu-id="609ee-110">Register a server API app</span></span>

<span data-ttu-id="609ee-111">Öğretici' deki kılavuzu izleyin: Azure portalının **Azure Active Directory** > **App kayıtları** alanında Sunucu *API uygulaması* için bir AAD uygulaması kaydetmek için bir uygulamayı Azure Active [Directory B2C'ye kaydedin:](/azure/active-directory-b2c/tutorial-register-applications)</span><span class="sxs-lookup"><span data-stu-id="609ee-111">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="609ee-112">**Yeni kayıt**seçin.</span><span class="sxs-lookup"><span data-stu-id="609ee-112">Select **New registration**.</span></span>
1. <span data-ttu-id="609ee-113">Uygulama için bir **Ad** sağlayın (örneğin, \*\* Blazor Sunucu AAD B2C).\*\*</span><span class="sxs-lookup"><span data-stu-id="609ee-113">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="609ee-114">**Desteklenen hesap türleri için,** herhangi bir kuruluş **dizinindeki veya herhangi bir kimlik sağlayıcısındaki Hesapları seçin. Azure AD B2C ile kullanıcıların kimliğini doğrulamak için.**</span><span class="sxs-lookup"><span data-stu-id="609ee-114">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="609ee-115">(çok kiracılı) bu deneyim için.</span><span class="sxs-lookup"><span data-stu-id="609ee-115">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="609ee-116">*Sunucu API uygulaması* bu senaryoda bir Yönlendirme **URI** gerektirmez, bu nedenle açılan kümeyi **Web'e** bırakın ve yeniden yönlendirme URI'si girmeyin.</span><span class="sxs-lookup"><span data-stu-id="609ee-116">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="609ee-117">**İzinler** > **Grant yöneticisinin openid ve offline_access izinleri için yoğunlaştığını** onaylayın.</span><span class="sxs-lookup"><span data-stu-id="609ee-117">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="609ee-118">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="609ee-118">Select **Register**.</span></span>

<span data-ttu-id="609ee-119">Bir **API ortaya çıkarmak:**</span><span class="sxs-lookup"><span data-stu-id="609ee-119">In **Expose an API**:</span></span>

1. <span data-ttu-id="609ee-120">**Kapsam ekle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="609ee-120">Select **Add a scope**.</span></span>
1. <span data-ttu-id="609ee-121">**Kaydet ve devam et**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="609ee-121">Select **Save and continue**.</span></span>
1. <span data-ttu-id="609ee-122">Kapsam **adı** sağlayın (örneğin, `API.Access`).</span><span class="sxs-lookup"><span data-stu-id="609ee-122">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="609ee-123">Yönetici **onay görüntüleme adı** sağlayın `Access API`(örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="609ee-123">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="609ee-124">Yönetici **onayı açıklaması** sağlayın (örneğin, `Allows the app to access server app API endpoints.`).</span><span class="sxs-lookup"><span data-stu-id="609ee-124">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="609ee-125">**Durum'un** **Etkin**olarak ayarladığını doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="609ee-125">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="609ee-126">**Kapsam Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="609ee-126">Select **Add scope**.</span></span>

<span data-ttu-id="609ee-127">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="609ee-127">Record the following information:</span></span>

* <span data-ttu-id="609ee-128">*Sunucu API uygulaması* Uygulama Kimliği (İstemci Kimliği) (örneğin, `11111111-1111-1111-1111-111111111111`)</span><span class="sxs-lookup"><span data-stu-id="609ee-128">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="609ee-129">Uygulama Kimliği URI (örneğin, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, veya sağladığınız özel değer)</span><span class="sxs-lookup"><span data-stu-id="609ee-129">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="609ee-130">Dizin Kimliği (Kiracı Kimliği) `222222222-2222-2222-2222-222222222222`(örneğin, )</span><span class="sxs-lookup"><span data-stu-id="609ee-130">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="609ee-131">*Sunucu API uygulaması* Uygulama Kimliği URI (örneğin, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`Azure portalı Istemci Kimliği'nin değerini varsayılan olarak alabilir)</span><span class="sxs-lookup"><span data-stu-id="609ee-131">*Server API app* App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, the Azure portal might default the value to the Client ID)</span></span>
* <span data-ttu-id="609ee-132">Varsayılan kapsam (örneğin, `API.Access`)</span><span class="sxs-lookup"><span data-stu-id="609ee-132">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="609ee-133">İstemci uygulamasını kaydetme</span><span class="sxs-lookup"><span data-stu-id="609ee-133">Register a client app</span></span>

<span data-ttu-id="609ee-134">Öğretici: Azure portalının **Azure Active Directory** > **App kayıtları** alanında Müşteri uygulaması için bir AAD *uygulaması* kaydetmek için bir uygulamayı Azure [Active Directory B2C'ye yeniden kaydedin:](/azure/active-directory-b2c/tutorial-register-applications)</span><span class="sxs-lookup"><span data-stu-id="609ee-134">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app* in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="609ee-135">**Yeni kayıt**seçin.</span><span class="sxs-lookup"><span data-stu-id="609ee-135">Select **New registration**.</span></span>
1. <span data-ttu-id="609ee-136">Uygulama için bir **Ad** sağlayın (örneğin, \*\* Blazor İstemci AAD B2C).\*\*</span><span class="sxs-lookup"><span data-stu-id="609ee-136">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="609ee-137">**Desteklenen hesap türleri için,** herhangi bir kuruluş **dizinindeki veya herhangi bir kimlik sağlayıcısındaki Hesapları seçin. Azure AD B2C ile kullanıcıların kimliğini doğrulamak için.**</span><span class="sxs-lookup"><span data-stu-id="609ee-137">For **Supported account types**, select **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span> <span data-ttu-id="609ee-138">(çok kiracılı) bu deneyim için.</span><span class="sxs-lookup"><span data-stu-id="609ee-138">(multi-tenant) for this experience.</span></span>
1. <span data-ttu-id="609ee-139">**Redirect URI** damlasını **Web'e**bırak ve uri'yi yeniden yönlendirmeyi `https://localhost:5001/authentication/login-callback`sağlar.</span><span class="sxs-lookup"><span data-stu-id="609ee-139">Leave the **Redirect URI** drop down set to **Web**, and provide a redirect URI of `https://localhost:5001/authentication/login-callback`.</span></span>
1. <span data-ttu-id="609ee-140">**İzinler** > **Grant yöneticisinin openid ve offline_access izinleri için yoğunlaştığını** onaylayın.</span><span class="sxs-lookup"><span data-stu-id="609ee-140">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="609ee-141">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="609ee-141">Select **Register**.</span></span>

<span data-ttu-id="609ee-142">**Kimlik** > Doğrulama**Platformu yapılandırmalarında** > **Web**:</span><span class="sxs-lookup"><span data-stu-id="609ee-142">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="609ee-143">**Redirect** URI'nin `https://localhost:5001/authentication/login-callback` mevcut olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="609ee-143">Confirm the **Redirect URI** of `https://localhost:5001/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="609ee-144">**Örtülü hibe**için, Erişim **belirteçleri** ve **kimlik belirteçleri**için onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="609ee-144">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="609ee-145">Uygulama için kalan varsayılanlar bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="609ee-145">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="609ee-146">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="609ee-146">Select the **Save** button.</span></span>

<span data-ttu-id="609ee-147">**API izinlerinde:**</span><span class="sxs-lookup"><span data-stu-id="609ee-147">In **API permissions**:</span></span>

1. <span data-ttu-id="609ee-148">Uygulamanın Microsoft **Graph** > **User.Read** iznine sahip olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="609ee-148">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="609ee-149">**API'lerim'i**izleyen **bir izin ekle'yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="609ee-149">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="609ee-150">**Ad** sütunundan *Server API uygulamasını* seçin (örneğin, \*\* Blazor Sunucu AAD B2C).\*\*</span><span class="sxs-lookup"><span data-stu-id="609ee-150">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="609ee-151">**API** listesini açın.</span><span class="sxs-lookup"><span data-stu-id="609ee-151">Open the **API** list.</span></span>
1. <span data-ttu-id="609ee-152">API'ye erişimi etkinleştirin `API.Access`(örneğin. ).</span><span class="sxs-lookup"><span data-stu-id="609ee-152">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="609ee-153">**İzin Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="609ee-153">Select **Add permissions**.</span></span>
1. <span data-ttu-id="609ee-154">**{KIRACı ADI}** düğmesi için Hibe yöneticisi içeriğini seçin.</span><span class="sxs-lookup"><span data-stu-id="609ee-154">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="609ee-155">Onaylamak için **Evet**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="609ee-155">Select **Yes** to confirm.</span></span>

<span data-ttu-id="609ee-156">**Evde** > **Azure AD B2C** > Kullanıcı**akışları:**</span><span class="sxs-lookup"><span data-stu-id="609ee-156">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="609ee-157">Kaydolma ve kaydolma kullanıcı akışı oluşturma</span><span class="sxs-lookup"><span data-stu-id="609ee-157">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="609ee-158">En azından, `context.User.Identity.Name` bileşendeki *(Shared/LoginDisplay.razor)* doldurmak `LoginDisplay` için Uygulama **talepleri** > Display**Name** kullanıcı özniteliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="609ee-158">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="609ee-159">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="609ee-159">Record the following information:</span></span>

* <span data-ttu-id="609ee-160">*İstemci uygulaması* Uygulama Kimliğini (İstemci `33333333-3333-3333-3333-333333333333`Kimliği) kaydetme (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="609ee-160">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>
* <span data-ttu-id="609ee-161">Uygulama için oluşturulan kaydolma ve kaydolma kullanıcı akış adını `B2C_1_signupsignin`kaydedin (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="609ee-161">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="609ee-162">Uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="609ee-162">Create the app</span></span>

<span data-ttu-id="609ee-163">Aşağıdaki komuttaki yer tutucuları daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğunda uygulayın:</span><span class="sxs-lookup"><span data-stu-id="609ee-163">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="609ee-164">Yoksa proje klasörü oluşturan çıktı konumunu belirtmek için, çıkış seçeneğini bir yolu olan komuta ekleyin `-o BlazorSample`(örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="609ee-164">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="609ee-165">Klasör adı da projenin adının bir parçası olur.</span><span class="sxs-lookup"><span data-stu-id="609ee-165">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="609ee-166">App ID URI'yi `app-id-uri` seçenegeç, ancak [Erişim belirteç kapsamları](#access-token-scopes) bölümünde açıklanan istemci uygulamasında bir yapılandırma değişikliği gerekebileceğini unutmayın.</span><span class="sxs-lookup"><span data-stu-id="609ee-166">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="609ee-167">Sunucu uygulaması yapılandırması</span><span class="sxs-lookup"><span data-stu-id="609ee-167">Server app configuration</span></span>

<span data-ttu-id="609ee-168">*Bu bölüm çözümün **Server** uygulamasıyla ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="609ee-168">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="609ee-169">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="609ee-169">Authentication package</span></span>

<span data-ttu-id="609ee-170">ASP.NET Çekirdek Web API'lerine yapılan çağrıları doğrulamak ve yetkilendirmek için destek `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`aşağıdakiler tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="609ee-170">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="609ee-171">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="609ee-171">Authentication service support</span></span>

<span data-ttu-id="609ee-172">Yöntem, `AddAuthentication` uygulama içinde kimlik doğrulama hizmetleri ayarlar ve Varsayılan kimlik doğrulama yöntemi olarak JWT Taşıyıcı işleyicisi yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="609ee-172">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="609ee-173">Yöntem, `AddAzureADB2CBearer` Azure Etkin Dizin idenb'i B2C tarafından yayılan belirteçleri doğrulamak için gereken JWT Taşıyıcı işleyicisindeki belirli parametreleri ayarlar:</span><span class="sxs-lookup"><span data-stu-id="609ee-173">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="609ee-174">`UseAuthentication`ve `UseAuthorization` şunları sağlamak:</span><span class="sxs-lookup"><span data-stu-id="609ee-174">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="609ee-175">Uygulama, gelen istekler üzerindeki belirteçleri ayrışdırmaya ve doğrulamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="609ee-175">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="609ee-176">Uygun kimlik bilgileri olmadan korumalı bir kaynağa erişmeye çalışan tüm istek başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="609ee-176">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="609ee-177">User.Identity.Name</span><span class="sxs-lookup"><span data-stu-id="609ee-177">User.Identity.Name</span></span>

<span data-ttu-id="609ee-178">Varsayılan olarak, `User.Identity.Name` doldurulur değil.</span><span class="sxs-lookup"><span data-stu-id="609ee-178">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="609ee-179">Uygulamanın `name` talep türünden değer alacak şekilde yapılandırılması için, [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) in: <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="609ee-179">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="609ee-180">Uygulama ayarları</span><span class="sxs-lookup"><span data-stu-id="609ee-180">App settings</span></span>

<span data-ttu-id="609ee-181">*appsettings.json* dosyası, erişim belirteçlerini doğrulamak için kullanılan JWT taşıyıcı işleyicisini yapılandırma seçeneklerini içerir.</span><span class="sxs-lookup"><span data-stu-id="609ee-181">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="609ee-182">WeatherForecast denetleyicisi</span><span class="sxs-lookup"><span data-stu-id="609ee-182">WeatherForecast controller</span></span>

<span data-ttu-id="609ee-183">WeatherForecast denetleyicisi *(Controllers/WeatherForecastController.cs)* denetleyiciye uygulanan `[Authorize]` öznitelik ile korunan bir API ortaya çıkarır.</span><span class="sxs-lookup"><span data-stu-id="609ee-183">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="609ee-184">Şunu anlamak **önemlidir:**</span><span class="sxs-lookup"><span data-stu-id="609ee-184">It's **important** to understand that:</span></span>

* <span data-ttu-id="609ee-185">`[Authorize]` Bu API denetleyicisindeki öznitelik, bu API'yi yetkisiz erişimden koruyan tek şeydir.</span><span class="sxs-lookup"><span data-stu-id="609ee-185">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="609ee-186">`[Authorize]` WebAssembly uygulamasında kullanılan öznitelik, yalnızca uygulamanın doğru çalışması için kullanıcıya yetki verilmesi gerektiğine dair bir ipucu görevi göremez. Blazor</span><span class="sxs-lookup"><span data-stu-id="609ee-186">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="609ee-187">İstemci uygulaması yapılandırması</span><span class="sxs-lookup"><span data-stu-id="609ee-187">Client app configuration</span></span>

<span data-ttu-id="609ee-188">*Bu bölüm çözümün **İstemci** uygulamasıyla ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="609ee-188">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="609ee-189">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="609ee-189">Authentication package</span></span>

<span data-ttu-id="609ee-190">Bir uygulama Bireysel B2C Hesabı kullanmak`IndividualB2C`için oluşturulduğunda ( ), uygulama otomatik olarak Microsoft`Microsoft.Authentication.WebAssembly.Msal`Kimlik Doğrulama [Kitaplığı](/azure/active-directory/develop/msal-overview) için bir paket başvurusu alır ( ).</span><span class="sxs-lookup"><span data-stu-id="609ee-190">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="609ee-191">Paket, uygulamanın kullanıcıları doğrulamasına ve korumalı API'leri aramak için belirteçler almasına yardımcı olan bir dizi ilkel lik sağlar.</span><span class="sxs-lookup"><span data-stu-id="609ee-191">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="609ee-192">Bir uygulamaya kimlik doğrulama ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="609ee-192">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

<span data-ttu-id="609ee-193">Önceki `{VERSION}` paket başvuruyu makalede gösterilen `Microsoft.AspNetCore.Blazor.Templates` paketin sürümüyle <xref:blazor/get-started> değiştirin.</span><span class="sxs-lookup"><span data-stu-id="609ee-193">Replace `{VERSION}` in the preceding package reference with the version of the `Microsoft.AspNetCore.Blazor.Templates` package shown in the <xref:blazor/get-started> article.</span></span>

<span data-ttu-id="609ee-194">Paket, `Microsoft.Authentication.WebAssembly.Msal` `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paketi geçici olarak uygulamaya ekler.</span><span class="sxs-lookup"><span data-stu-id="609ee-194">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="609ee-195">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="609ee-195">Authentication service support</span></span>

<span data-ttu-id="609ee-196">Kullanıcıların kimlik doğrulaması için destek, paket `AddMsalAuthentication` tarafından sağlanan uzantı yöntemiyle servis kapsayıcısında `Microsoft.Authentication.WebAssembly.Msal` kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="609ee-196">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="609ee-197">Bu yöntem, uygulamanın Kimlik Sağlayıcısı (IP) ile etkileşim kurması için gereken tüm hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="609ee-197">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="609ee-198">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="609ee-198">*Program.cs*:</span></span>

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

<span data-ttu-id="609ee-199">Yöntem, `AddMsalAuthentication` bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri arama kabul eder.</span><span class="sxs-lookup"><span data-stu-id="609ee-199">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="609ee-200">Uygulamayı yapılandırmak için gereken değerler, uygulamayı kaydettirdiğinizde Azure Portal AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="609ee-200">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

### <a name="access-token-scopes"></a><span data-ttu-id="609ee-201">Belirteç kapsamlarına erişim</span><span class="sxs-lookup"><span data-stu-id="609ee-201">Access token scopes</span></span>

<span data-ttu-id="609ee-202">Varsayılan erişim belirteç kapsamları, aşağıdakiler için erişim belirteç kapsamları listesini temsil eder:</span><span class="sxs-lookup"><span data-stu-id="609ee-202">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="609ee-203">Oturum açma isteğine varsayılan olarak dahil edilir.</span><span class="sxs-lookup"><span data-stu-id="609ee-203">Included by default in the sign in request.</span></span>
* <span data-ttu-id="609ee-204">Kimlik doğrulamadan hemen sonra bir erişim belirteci sağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="609ee-204">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="609ee-205">Tüm kapsamlar Azure Etkin Dizin kurallarına göre aynı uygulamaya ait olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="609ee-205">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="609ee-206">Gerektiğinde ek API uygulamaları için ek kapsamlar eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="609ee-206">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> <span data-ttu-id="609ee-207">Azure portalı bir kapsam oluşturuyorsa URI ve uygulama API'den *401 Yetkisiz* yanıt aldığında **işlenmemiş bir özel durum atarsa,** düzeni ve ana bilgisayarı içermeyen bir KAPSAM URI kullanmayı deneyin.</span><span class="sxs-lookup"><span data-stu-id="609ee-207">If the Azure portal provides a scope URI and **the app throws an unhandled exception** when it receives a *401 Unauthorized* response from the API, try using a scope URI that doesn't include the scheme and host.</span></span> <span data-ttu-id="609ee-208">Örneğin, Azure portalı aşağıdaki kapsam URI biçimlerinden birini sağlayabilir:</span><span class="sxs-lookup"><span data-stu-id="609ee-208">For example, the Azure portal may provide one of the following scope URI formats:</span></span>
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> <span data-ttu-id="609ee-209">Şema ve ana bilgisayar olmadan URI kapsamını tedarik edin:</span><span class="sxs-lookup"><span data-stu-id="609ee-209">Supply the scope URI without the scheme and host:</span></span>
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

<span data-ttu-id="609ee-210">Daha fazla bilgi için bkz. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span><span class="sxs-lookup"><span data-stu-id="609ee-210">For more information, see <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.</span></span>

### <a name="imports-file"></a><span data-ttu-id="609ee-211">Dosyayı alma</span><span class="sxs-lookup"><span data-stu-id="609ee-211">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="609ee-212">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="609ee-212">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="609ee-213">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="609ee-213">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="609ee-214">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="609ee-214">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="609ee-215">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="609ee-215">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="609ee-216">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="609ee-216">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="609ee-217">FetchData bileşeni</span><span class="sxs-lookup"><span data-stu-id="609ee-217">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="609ee-218">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="609ee-218">Run the app</span></span>

<span data-ttu-id="609ee-219">Uygulamayı Sunucu projesinden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="609ee-219">Run the app from the Server project.</span></span> <span data-ttu-id="609ee-220">Visual Studio'yu kullanırken, **Solution Explorer'daki** Sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya **Hata Ayıklama** menüsünden uygulamayı başlatın.</span><span class="sxs-lookup"><span data-stu-id="609ee-220">When using Visual Studio, select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="609ee-221">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="609ee-221">Additional resources</span></span>

* [<span data-ttu-id="609ee-222">Ek erişim belirteçleri isteme</span><span class="sxs-lookup"><span data-stu-id="609ee-222">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="609ee-223">Öğretici: Azure Active Directory B2C kiracısı oluşturma</span><span class="sxs-lookup"><span data-stu-id="609ee-223">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="609ee-224">Microsoft kimlik platformu belgeleri</span><span class="sxs-lookup"><span data-stu-id="609ee-224">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
