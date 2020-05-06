---
title: ASP.NET Core Azure Active Directory B2C ile bulut kimlik doğrulaması
author: camsoper
description: ASP.NET Core ile Azure Active Directory B2C kimlik doğrulamasını ayarlamayı öğrenin.
ms.author: casoper
ms.custom: mvc
ms.date: 01/21/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/azure-ad-b2c
ms.openlocfilehash: d62889ba6dba6748ce3d047f0d37b3a904199496
ms.sourcegitcommit: 4a9321db7ca4e69074fa08a678dcc91e16215b1e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82850441"
---
# <a name="cloud-authentication-with-azure-active-directory-b2c-in-aspnet-core"></a><span data-ttu-id="66ae6-103">ASP.NET Core Azure Active Directory B2C ile bulut kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="66ae6-103">Cloud authentication with Azure Active Directory B2C in ASP.NET Core</span></span>

<span data-ttu-id="66ae6-104">[Cam Soper](https://twitter.com/camsoper) göre</span><span class="sxs-lookup"><span data-stu-id="66ae6-104">By [Cam Soper](https://twitter.com/camsoper)</span></span>

<span data-ttu-id="66ae6-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C), Web ve mobil uygulamalar için bir bulut kimlik yönetimi çözümüdür.</span><span class="sxs-lookup"><span data-stu-id="66ae6-105">[Azure Active Directory B2C](/azure/active-directory-b2c/active-directory-b2c-overview) (Azure AD B2C) is a cloud identity management solution for web and mobile apps.</span></span> <span data-ttu-id="66ae6-106">Hizmet, bulutta ve şirket içinde barındırılan uygulamalar için kimlik doğrulaması sağlar.</span><span class="sxs-lookup"><span data-stu-id="66ae6-106">The service provides authentication for apps hosted in the cloud and on-premises.</span></span> <span data-ttu-id="66ae6-107">Kimlik doğrulama türleri tek tek hesapları, sosyal ağ hesaplarını ve Federasyon kurumsal hesaplarını içerir.</span><span class="sxs-lookup"><span data-stu-id="66ae6-107">Authentication types include individual accounts, social network accounts, and federated enterprise accounts.</span></span> <span data-ttu-id="66ae6-108">Ayrıca, Azure AD B2C, çok faktörlü kimlik doğrulaması için en az yapılandırma sağlar.</span><span class="sxs-lookup"><span data-stu-id="66ae6-108">Additionally, Azure AD B2C can provide multi-factor authentication with minimal configuration.</span></span>

> [!TIP]
> <span data-ttu-id="66ae6-109">Azure Active Directory (Azure AD) ve Azure AD B2C ayrı ürün tekliflerdir.</span><span class="sxs-lookup"><span data-stu-id="66ae6-109">Azure Active Directory (Azure AD) and Azure AD B2C are separate product offerings.</span></span> <span data-ttu-id="66ae6-110">Bir Azure AD kiracısı bir kuruluşu temsil ettiğinde Azure AD B2C kiracı, bağlı olan taraf uygulamalarıyla kullanılacak bir kimlik koleksiyonunu temsil eder.</span><span class="sxs-lookup"><span data-stu-id="66ae6-110">An Azure AD tenant represents an organization, while an Azure AD B2C tenant represents a collection of identities to be used with relying party applications.</span></span> <span data-ttu-id="66ae6-111">Daha fazla bilgi için bkz. [Azure AD B2C: sık sorulan sorular (SSS)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span><span class="sxs-lookup"><span data-stu-id="66ae6-111">To learn more, see [Azure AD B2C: Frequently asked questions (FAQ)](/azure/active-directory-b2c/active-directory-b2c-faqs).</span></span>

<span data-ttu-id="66ae6-112">Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:</span><span class="sxs-lookup"><span data-stu-id="66ae6-112">In this tutorial, learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="66ae6-113">Azure Active Directory B2C kiracı oluşturma</span><span class="sxs-lookup"><span data-stu-id="66ae6-113">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="66ae6-114">Uygulamayı Azure AD B2C kaydetme</span><span class="sxs-lookup"><span data-stu-id="66ae6-114">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="66ae6-115">Kimlik doğrulaması için Azure AD B2C kiracısını kullanacak şekilde yapılandırılmış bir ASP.NET Core Web uygulaması oluşturmak için Visual Studio 'Yu kullanma</span><span class="sxs-lookup"><span data-stu-id="66ae6-115">Use Visual Studio to create an ASP.NET Core web app configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="66ae6-116">Azure AD B2C kiracının davranışını denetleyen ilkeleri yapılandırın</span><span class="sxs-lookup"><span data-stu-id="66ae6-116">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

## <a name="prerequisites"></a><span data-ttu-id="66ae6-117">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="66ae6-117">Prerequisites</span></span>

<span data-ttu-id="66ae6-118">Bu izlenecek yol için aşağıdakiler gereklidir:</span><span class="sxs-lookup"><span data-stu-id="66ae6-118">The following are required for this walkthrough:</span></span>

* [<span data-ttu-id="66ae6-119">Microsoft Azure aboneliği</span><span class="sxs-lookup"><span data-stu-id="66ae6-119">Microsoft Azure subscription</span></span>](https://azure.microsoft.com/free/dotnet/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [<span data-ttu-id="66ae6-120">Visual Studio 2019</span><span class="sxs-lookup"><span data-stu-id="66ae6-120">Visual Studio 2019</span></span>](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)

## <a name="create-the-azure-active-directory-b2c-tenant"></a><span data-ttu-id="66ae6-121">Azure Active Directory B2C kiracı oluşturma</span><span class="sxs-lookup"><span data-stu-id="66ae6-121">Create the Azure Active Directory B2C tenant</span></span>

<span data-ttu-id="66ae6-122">[Belgelerde açıklandığı şekilde](/azure/active-directory-b2c/active-directory-b2c-get-started)bir Azure Active Directory B2C kiracı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="66ae6-122">Create an Azure Active Directory B2C tenant [as described in the documentation](/azure/active-directory-b2c/active-directory-b2c-get-started).</span></span> <span data-ttu-id="66ae6-123">İstendiğinde, kiracının bir Azure aboneliğiyle ilişkilendirilmesi Bu öğretici için isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="66ae6-123">When prompted, associating the tenant with an Azure subscription is optional for this tutorial.</span></span>

## <a name="register-the-app-in-azure-ad-b2c"></a><span data-ttu-id="66ae6-124">Uygulamayı Azure AD B2C kaydetme</span><span class="sxs-lookup"><span data-stu-id="66ae6-124">Register the app in Azure AD B2C</span></span>

<span data-ttu-id="66ae6-125">Yeni oluşturulan Azure AD B2C kiracısında, **Web uygulaması kaydetme** bölümünün altındaki [belgelerde bulunan adımları](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) kullanarak uygulamanızı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="66ae6-125">In the newly created Azure AD B2C tenant, register your app using [the steps in the documentation](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application) under the **Register a web app** section.</span></span> <span data-ttu-id="66ae6-126">**Web uygulaması istemci parolası oluşturma** bölümünde durun.</span><span class="sxs-lookup"><span data-stu-id="66ae6-126">Stop at the **Create a web app client secret** section.</span></span> <span data-ttu-id="66ae6-127">Bu öğretici için bir istemci gizli anahtarı gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="66ae6-127">A client secret isn't required for this tutorial.</span></span> 

<span data-ttu-id="66ae6-128">Aşağıdaki değerleri kullanın:</span><span class="sxs-lookup"><span data-stu-id="66ae6-128">Use the following values:</span></span>

| <span data-ttu-id="66ae6-129">Ayar</span><span class="sxs-lookup"><span data-stu-id="66ae6-129">Setting</span></span>                       | <span data-ttu-id="66ae6-130">Değer</span><span class="sxs-lookup"><span data-stu-id="66ae6-130">Value</span></span>                     | <span data-ttu-id="66ae6-131">Notlar</span><span class="sxs-lookup"><span data-stu-id="66ae6-131">Notes</span></span>                                                                                                                                                                                              |
|-------------------------------|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <span data-ttu-id="66ae6-132">**Adı**</span><span class="sxs-lookup"><span data-stu-id="66ae6-132">**Name**</span></span>                      | <span data-ttu-id="66ae6-133">*&lt;uygulama adı&gt;*</span><span class="sxs-lookup"><span data-stu-id="66ae6-133">*&lt;app name&gt;*</span></span>        | <span data-ttu-id="66ae6-134">Uygulamanızı tüketicilere açıklayan uygulama için bir **ad** girin.</span><span class="sxs-lookup"><span data-stu-id="66ae6-134">Enter a **Name** for the app that describes your app to consumers.</span></span>                                                                                                                                 |
| <span data-ttu-id="66ae6-135">**Web uygulamasını / web API'sini dahil etme**</span><span class="sxs-lookup"><span data-stu-id="66ae6-135">**Include web app / web API**</span></span> | <span data-ttu-id="66ae6-136">Yes</span><span class="sxs-lookup"><span data-stu-id="66ae6-136">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="66ae6-137">**Örtük akışa izin verme**</span><span class="sxs-lookup"><span data-stu-id="66ae6-137">**Allow implicit flow**</span></span>       | <span data-ttu-id="66ae6-138">Yes</span><span class="sxs-lookup"><span data-stu-id="66ae6-138">Yes</span></span>                       |                                                                                                                                                                                                    |
| <span data-ttu-id="66ae6-139">**Yanıt URL'si**</span><span class="sxs-lookup"><span data-stu-id="66ae6-139">**Reply URL**</span></span>                 | `https://localhost:44300/signin-oidc` | <span data-ttu-id="66ae6-140">Yanıt URL'leri, Azure AD B2C'nin, uygulamanız tarafından istenen belirteçleri döndürdüğü uç noktalardır.</span><span class="sxs-lookup"><span data-stu-id="66ae6-140">Reply URLs are endpoints where Azure AD B2C returns any tokens that your app requests.</span></span> <span data-ttu-id="66ae6-141">Visual Studio, kullanılacak yanıt URL 'sini sağlar.</span><span class="sxs-lookup"><span data-stu-id="66ae6-141">Visual Studio provides the Reply URL to use.</span></span> <span data-ttu-id="66ae6-142">Şu an için formunu `https://localhost:44300/signin-oidc` doldurun için yazın.</span><span class="sxs-lookup"><span data-stu-id="66ae6-142">For now, enter `https://localhost:44300/signin-oidc` to complete the form.</span></span> |
| <span data-ttu-id="66ae6-143">**Uygulama Kimliği URI'si**</span><span class="sxs-lookup"><span data-stu-id="66ae6-143">**App ID URI**</span></span>                | <span data-ttu-id="66ae6-144">Boş bırakın</span><span class="sxs-lookup"><span data-stu-id="66ae6-144">Leave blank</span></span>               | <span data-ttu-id="66ae6-145">Bu öğretici için gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="66ae6-145">Not required for this tutorial.</span></span>                                                                                                                                                                    |
| <span data-ttu-id="66ae6-146">**Yerel istemci ekle**</span><span class="sxs-lookup"><span data-stu-id="66ae6-146">**Include native client**</span></span>     | <span data-ttu-id="66ae6-147">No</span><span class="sxs-lookup"><span data-stu-id="66ae6-147">No</span></span>                        |                                                                                                                                                                                                    |

> [!WARNING]
> <span data-ttu-id="66ae6-148">Localhost olmayan bir yanıt URL 'SI ayarlıyorsanız, [yanıt URL 'si listesinde izin verilen kısıtlamalara](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application)dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="66ae6-148">If setting up a non-localhost Reply URL, be aware of the [constraints on what is allowed in the Reply URL list](/azure/active-directory-b2c/tutorial-register-applications#register-a-web-application).</span></span> 

<span data-ttu-id="66ae6-149">Uygulama kaydedildikten sonra, Kiracıdaki uygulamaların listesi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="66ae6-149">After the app is registered, the list of apps in the tenant is displayed.</span></span> <span data-ttu-id="66ae6-150">Yeni kaydedilen uygulamayı seçin.</span><span class="sxs-lookup"><span data-stu-id="66ae6-150">Select the app that was just registered.</span></span> <span data-ttu-id="66ae6-151">Pano 'ya kopyalamak için **uygulama kimliği** alanının sağ tarafındaki **Kopyala** simgesini seçin.</span><span class="sxs-lookup"><span data-stu-id="66ae6-151">Select the **Copy** icon to the right of the **Application ID** field to copy it to the clipboard.</span></span>

<span data-ttu-id="66ae6-152">Şu anda Azure AD B2C kiracısında başka hiçbir şey yapılandırılamaz, ancak tarayıcı penceresini açık bırakın.</span><span class="sxs-lookup"><span data-stu-id="66ae6-152">Nothing more can be configured in the Azure AD B2C tenant at this time, but leave the browser window open.</span></span> <span data-ttu-id="66ae6-153">ASP.NET Core uygulama oluşturulduktan sonra daha fazla yapılandırma vardır.</span><span class="sxs-lookup"><span data-stu-id="66ae6-153">There is more configuration after the ASP.NET Core app is created.</span></span>

## <a name="create-an-aspnet-core-app-in-visual-studio"></a><span data-ttu-id="66ae6-154">Visual Studio 'da ASP.NET Core uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="66ae6-154">Create an ASP.NET Core app in Visual Studio</span></span>

<span data-ttu-id="66ae6-155">Visual Studio Web uygulaması şablonu, kimlik doğrulaması için Azure AD B2C kiracısını kullanacak şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="66ae6-155">The Visual Studio Web Application template can be configured to use the Azure AD B2C tenant for authentication.</span></span>

<span data-ttu-id="66ae6-156">Visual Studio 'da:</span><span class="sxs-lookup"><span data-stu-id="66ae6-156">In Visual Studio:</span></span>

1. <span data-ttu-id="66ae6-157">Yeni bir ASP.NET Core Web uygulaması oluşturun.</span><span class="sxs-lookup"><span data-stu-id="66ae6-157">Create a new ASP.NET Core Web Application.</span></span> 
2. <span data-ttu-id="66ae6-158">Şablonlar listesinden **Web uygulaması** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="66ae6-158">Select **Web Application** from the list of templates.</span></span>
3. <span data-ttu-id="66ae6-159">**Kimlik doğrulamasını Değiştir** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="66ae6-159">Select the **Change Authentication** button.</span></span>
    
    ![Kimlik doğrulama düğmesini Değiştir](./azure-ad-b2c/_static/changeauth.png)

4. <span data-ttu-id="66ae6-161">**Kimlik doğrulamasını Değiştir** iletişim kutusunda, **bireysel kullanıcı hesapları**' nı seçin ve ardından açılan listede **buluttaki mevcut bir Kullanıcı deposuna Bağlan** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="66ae6-161">In the **Change Authentication** dialog, select **Individual User Accounts**, and then select **Connect to an existing user store in the cloud** in the dropdown.</span></span> 
    
    ![Kimlik doğrulamasını Değiştir Iletişim kutusu](./azure-ad-b2c/_static/changeauthdialog.png)

5. <span data-ttu-id="66ae6-163">Formu aşağıdaki değerlerle doldurun:</span><span class="sxs-lookup"><span data-stu-id="66ae6-163">Complete the form with the following values:</span></span>
    
    | <span data-ttu-id="66ae6-164">Ayar</span><span class="sxs-lookup"><span data-stu-id="66ae6-164">Setting</span></span>                       | <span data-ttu-id="66ae6-165">Değer</span><span class="sxs-lookup"><span data-stu-id="66ae6-165">Value</span></span>                                                 |
    |-------------------------------|-------------------------------------------------------|
    | <span data-ttu-id="66ae6-166">**Etki alanı adı**</span><span class="sxs-lookup"><span data-stu-id="66ae6-166">**Domain Name**</span></span>               | <span data-ttu-id="66ae6-167">*&lt;B2C kiracınızın etki alanı adı&gt;*</span><span class="sxs-lookup"><span data-stu-id="66ae6-167">*&lt;the domain name of your B2C tenant&gt;*</span></span>          |
    | <span data-ttu-id="66ae6-168">**Uygulama KIMLIĞI**</span><span class="sxs-lookup"><span data-stu-id="66ae6-168">**Application ID**</span></span>            | <span data-ttu-id="66ae6-169">*&lt;Uygulama KIMLIĞINI Panodan yapıştırma&gt;*</span><span class="sxs-lookup"><span data-stu-id="66ae6-169">*&lt;paste the Application ID from the clipboard&gt;*</span></span> |
    | <span data-ttu-id="66ae6-170">**Geri çağırma yolu**</span><span class="sxs-lookup"><span data-stu-id="66ae6-170">**Callback Path**</span></span>             | <span data-ttu-id="66ae6-171">*&lt;Varsayılan değeri kullan&gt;*</span><span class="sxs-lookup"><span data-stu-id="66ae6-171">*&lt;use the default value&gt;*</span></span>                       |
    | <span data-ttu-id="66ae6-172">**Kaydolma veya oturum açma ilkesi**</span><span class="sxs-lookup"><span data-stu-id="66ae6-172">**Sign-up or sign-in policy**</span></span> | `B2C_1_SiUpIn`                                        |
    | <span data-ttu-id="66ae6-173">**Parola ilkesini Sıfırla**</span><span class="sxs-lookup"><span data-stu-id="66ae6-173">**Reset password policy**</span></span>     | `B2C_1_SSPR`                                          |
    | <span data-ttu-id="66ae6-174">**Profil ilkesini Düzenle**</span><span class="sxs-lookup"><span data-stu-id="66ae6-174">**Edit profile policy**</span></span>       | <span data-ttu-id="66ae6-175">*&lt;boş bırakın&gt;*</span><span class="sxs-lookup"><span data-stu-id="66ae6-175">*&lt;leave blank&gt;*</span></span>                                 |
    
    <span data-ttu-id="66ae6-176">Yanıt URI 'sini panoya kopyalamak için **Yanıt URI 'sinin** yanındaki **Kopyala** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="66ae6-176">Select the **Copy** link next to **Reply URI** to copy the Reply URI to the clipboard.</span></span> <span data-ttu-id="66ae6-177">**Kimlik doğrulaması Değiştir** iletişim kutusunu kapatmak için **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="66ae6-177">Select **OK** to close the **Change Authentication** dialog.</span></span> <span data-ttu-id="66ae6-178">Web uygulamasını oluşturmak için **Tamam ' ı** seçin.</span><span class="sxs-lookup"><span data-stu-id="66ae6-178">Select **OK** to create the web app.</span></span>

## <a name="finish-the-b2c-app-registration"></a><span data-ttu-id="66ae6-179">B2C uygulama kaydını tamamlama</span><span class="sxs-lookup"><span data-stu-id="66ae6-179">Finish the B2C app registration</span></span>

<span data-ttu-id="66ae6-180">B2C uygulama özellikleri hala açık olan tarayıcı penceresine geri dönün.</span><span class="sxs-lookup"><span data-stu-id="66ae6-180">Return to the browser window with the B2C app properties still open.</span></span> <span data-ttu-id="66ae6-181">Daha önce belirtilen geçici **yanıt URL** 'Sini Visual Studio 'dan kopyalanmış değerle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="66ae6-181">Change the temporary **Reply URL** specified earlier to the value copied from Visual Studio.</span></span> <span data-ttu-id="66ae6-182">Pencerenin üst kısmındaki **Kaydet** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="66ae6-182">Select **Save** at the top of the window.</span></span>

> [!TIP]
> <span data-ttu-id="66ae6-183">Yanıt URL 'sini kopyalamadıysanız, Web projesi özelliklerindeki hata ayıklama sekmesinden HTTPS adresini kullanın ve *appSettings. JSON*' dan **callbackpath** değerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="66ae6-183">If you didn't copy the Reply URL, use the HTTPS address from the Debug tab in the web project properties, and append the **CallbackPath** value from *appsettings.json*.</span></span>

## <a name="configure-policies"></a><span data-ttu-id="66ae6-184">İlkeleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="66ae6-184">Configure policies</span></span>

<span data-ttu-id="66ae6-185">[Kaydolma veya oturum açma ilkesi oluşturmak](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions)için Azure AD B2C belgelerindeki adımları kullanın ve ardından [bir parola sıfırlama ilkesi oluşturun](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span><span class="sxs-lookup"><span data-stu-id="66ae6-185">Use the steps in the Azure AD B2C documentation to [create a sign-up or sign-in policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions), and then [create a password reset policy](/azure/active-directory-b2c/active-directory-b2c-reference-policies#user-flow-versions).</span></span> <span data-ttu-id="66ae6-186">\*\* Identity Sağlayıcılar\*\*, **kaydolma öznitelikleri**ve **uygulama talepleri**için belgelerde verilen örnek değerleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="66ae6-186">Use the example values provided in the documentation for **Identity providers**, **Sign-up attributes**, and **Application claims**.</span></span> <span data-ttu-id="66ae6-187">Belgelerde açıklandığı şekilde ilkeleri test etmek için **Şimdi Çalıştır** düğmesini kullanmak isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="66ae6-187">Using the **Run now** button to test the policies as described in the documentation is optional.</span></span>

> [!WARNING]
> <span data-ttu-id="66ae6-188">İlke adlarının, bu ilkeler Visual Studio 'daki **kimlik doğrulaması Değiştir** iletişim kutusunda kullanıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="66ae6-188">Ensure the policy names are exactly as described in the documentation, as those policies were used in the **Change Authentication** dialog in Visual Studio.</span></span> <span data-ttu-id="66ae6-189">İlke adları *appSettings. JSON*içinde doğrulanabilir.</span><span class="sxs-lookup"><span data-stu-id="66ae6-189">The policy names can be verified in *appsettings.json*.</span></span>

## <a name="configure-the-underlying-openidconnectoptionsjwtbearercookie-options"></a><span data-ttu-id="66ae6-190">Temeldeki Openıdconnectoptions/Jwttaşıyıcı/tanımlama bilgisi seçeneklerini yapılandırın</span><span class="sxs-lookup"><span data-stu-id="66ae6-190">Configure the underlying OpenIdConnectOptions/JwtBearer/Cookie options</span></span>

<span data-ttu-id="66ae6-191">Temel alınan seçenekleri doğrudan yapılandırmak için, içinde `Startup.ConfigureServices`uygun düzen sabitini kullanın:</span><span class="sxs-lookup"><span data-stu-id="66ae6-191">To configure the underlying options directly, use the appropriate scheme constant in `Startup.ConfigureServices`:</span></span>

```csharp
services.Configure<OpenIdConnectOptions>(
    AzureAD[B2C]Defaults.OpenIdScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<CookieAuthenticationOptions>(
    AzureAD[B2C]Defaults.CookieScheme, options => 
    {
        // Omitted for brevity
    });

services.Configure<JwtBearerOptions>(
    AzureAD[B2C]Defaults.JwtBearerAuthenticationScheme, options => 
    {
        // Omitted for brevity
    });
```

## <a name="run-the-app"></a><span data-ttu-id="66ae6-192">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="66ae6-192">Run the app</span></span>

<span data-ttu-id="66ae6-193">Visual Studio 'da **F5** ' e basarak uygulamayı derleyin ve çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="66ae6-193">In Visual Studio, press **F5** to build and run the app.</span></span> <span data-ttu-id="66ae6-194">Web uygulaması başlatıldıktan sonra, tanımlama bilgilerinin kullanımını kabul etmek için **kabul et** ' i seçin (istenirse) ve ardından **oturum aç**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="66ae6-194">After the web app launches, select **Accept** to accept the use of cookies (if prompted), and then select **Sign in**.</span></span>

![Uygulamada oturum açın](./azure-ad-b2c/_static/signin.png)

<span data-ttu-id="66ae6-196">Tarayıcı Azure AD B2C kiracıya yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="66ae6-196">The browser redirects to the Azure AD B2C tenant.</span></span> <span data-ttu-id="66ae6-197">Mevcut bir hesapla oturum açın (ilkeleri test etmeyi oluşturulduysa) veya yeni bir hesap oluşturmak için **Şimdi kaydolun** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="66ae6-197">Sign in with an existing account (if one was created testing the policies) or select **Sign up now** to create a new account.</span></span> <span data-ttu-id="66ae6-198">Unutulan bir parolayı sıfırlamak için **parolanızı mı unuttunuz?** bağlantısı kullanılır.</span><span class="sxs-lookup"><span data-stu-id="66ae6-198">The **Forgot your password?** link is used to reset a forgotten password.</span></span>

![Azure AD B2C oturumu açma](./azure-ad-b2c/_static/b2csts.png)

<span data-ttu-id="66ae6-200">Başarıyla oturum açtıktan sonra tarayıcı web uygulamasına yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="66ae6-200">After successfully signing in, the browser redirects to the web app.</span></span>

![Başarılı](./azure-ad-b2c/_static/success.png)

## <a name="next-steps"></a><span data-ttu-id="66ae6-202">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="66ae6-202">Next steps</span></span>

<span data-ttu-id="66ae6-203">Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:</span><span class="sxs-lookup"><span data-stu-id="66ae6-203">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="66ae6-204">Azure Active Directory B2C kiracı oluşturma</span><span class="sxs-lookup"><span data-stu-id="66ae6-204">Create an Azure Active Directory B2C tenant</span></span>
> * <span data-ttu-id="66ae6-205">Uygulamayı Azure AD B2C kaydetme</span><span class="sxs-lookup"><span data-stu-id="66ae6-205">Register an app in Azure AD B2C</span></span>
> * <span data-ttu-id="66ae6-206">Kimlik doğrulaması için Azure AD B2C kiracısını kullanacak şekilde yapılandırılmış bir ASP.NET Core Web uygulaması oluşturmak için Visual Studio 'Yu kullanma</span><span class="sxs-lookup"><span data-stu-id="66ae6-206">Use Visual Studio to create an ASP.NET Core Web Application configured to use the Azure AD B2C tenant for authentication</span></span>
> * <span data-ttu-id="66ae6-207">Azure AD B2C kiracının davranışını denetleyen ilkeleri yapılandırın</span><span class="sxs-lookup"><span data-stu-id="66ae6-207">Configure policies controlling the behavior of the Azure AD B2C tenant</span></span>

<span data-ttu-id="66ae6-208">ASP.NET Core uygulaması kimlik doğrulaması için Azure AD B2C kullanacak şekilde yapılandırıldığına göre, uygulamanızın güvenliğini sağlamak için [Yetkilendir özniteliği](xref:security/authorization/simple) kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="66ae6-208">Now that the ASP.NET Core app is configured to use Azure AD B2C for authentication, the [Authorize attribute](xref:security/authorization/simple) can be used to secure your app.</span></span> <span data-ttu-id="66ae6-209">Şunları öğrenerek uygulamanızı geliştirmeye devam edin:</span><span class="sxs-lookup"><span data-stu-id="66ae6-209">Continue developing your app by learning to:</span></span>

* <span data-ttu-id="66ae6-210">[Azure AD B2C Kullanıcı arabirimini özelleştirin](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span><span class="sxs-lookup"><span data-stu-id="66ae6-210">[Customize the Azure AD B2C user interface](/azure/active-directory-b2c/active-directory-b2c-reference-ui-customization).</span></span>
* <span data-ttu-id="66ae6-211">[Parola karmaşıklığı gereksinimlerini yapılandırın](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span><span class="sxs-lookup"><span data-stu-id="66ae6-211">[Configure password complexity requirements](/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).</span></span>
* <span data-ttu-id="66ae6-212">[Multi-Factor Authentication 'ı etkinleştirin](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span><span class="sxs-lookup"><span data-stu-id="66ae6-212">[Enable multi-factor authentication](/azure/active-directory-b2c/active-directory-b2c-reference-mfa).</span></span>
* <span data-ttu-id="66ae6-213">[Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app)ve diğerleri gibi ek kimlik sağlayıcılarını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="66ae6-213">Configure additional identity providers, such as [Microsoft](/azure/active-directory-b2c/active-directory-b2c-setup-msa-app), [Facebook](/azure/active-directory-b2c/active-directory-b2c-setup-fb-app), [Google](/azure/active-directory-b2c/active-directory-b2c-setup-goog-app), [Amazon](/azure/active-directory-b2c/active-directory-b2c-setup-amzn-app), [Twitter](/azure/active-directory-b2c/active-directory-b2c-setup-twitter-app), and others.</span></span>
* <span data-ttu-id="66ae6-214">Azure AD B2C kiracısından grup üyeliği gibi ek kullanıcı bilgilerini almak için [Azure AD Graph API kullanın](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) .</span><span class="sxs-lookup"><span data-stu-id="66ae6-214">[Use the Azure AD Graph API](/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) to retrieve additional user information, such as group membership, from the Azure AD B2C tenant.</span></span>
* <span data-ttu-id="66ae6-215">[Azure AD B2C kullanarak bir ASP.NET Core Web API 'Sinin güvenliğini sağlayın](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/).</span><span class="sxs-lookup"><span data-stu-id="66ae6-215">[Secure an ASP.NET Core web API using Azure AD B2C](https://azure.microsoft.com/resources/samples/active-directory-b2c-dotnetcore-webapi/).</span></span>
* <span data-ttu-id="66ae6-216">[Öğretici: Azure Active Directory B2C kullanarak bir ASP.NET Web API 'sine erişim Izni verme](/azure/active-directory-b2c/tutorial-web-api-dotnet).</span><span class="sxs-lookup"><span data-stu-id="66ae6-216">[Tutorial: Grant access to an ASP.NET web API using Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-web-api-dotnet).</span></span>
