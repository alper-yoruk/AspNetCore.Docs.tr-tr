---
<span data-ttu-id="63c62-101">Başlık: ' Blazor Azure Active Directory B2C ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc ile barındırılan bir uygulamayı ASP.NET Core güvenli hale getirme:</span><span class="sxs-lookup"><span data-stu-id="63c62-101">title: 'Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="63c62-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="63c62-102">'Blazor'</span></span>
- <span data-ttu-id="63c62-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="63c62-103">'Identity'</span></span>
- <span data-ttu-id="63c62-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="63c62-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="63c62-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="63c62-105">'Razor'</span></span>
- <span data-ttu-id="63c62-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="63c62-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a><span data-ttu-id="63c62-107">Azure Active Directory B2C bir ASP.NET Core Blazor weelsembly barındırılan uygulamasının güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="63c62-107">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory B2C</span></span>

<span data-ttu-id="63c62-108">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="63c62-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="63c62-109">Bu makalede Blazor kimlik doğrulaması için [Azure ACTIVE DIRECTORY (AAD) B2C](/azure/active-directory-b2c/overview) kullanan bir webassembly tek başına uygulamasının nasıl oluşturulacağı açıklanır.</span><span class="sxs-lookup"><span data-stu-id="63c62-109">This article describes how to create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication.</span></span>

## <a name="register-apps-in-aad-b2c-and-create-solution"></a><span data-ttu-id="63c62-110">Uygulamaları AAD B2C kaydetme ve çözüm oluşturma</span><span class="sxs-lookup"><span data-stu-id="63c62-110">Register apps in AAD B2C and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="63c62-111">Kiracı oluşturma</span><span class="sxs-lookup"><span data-stu-id="63c62-111">Create a tenant</span></span>

<span data-ttu-id="63c62-112">Öğretici: AAD B2C kiracı oluşturmak için [Azure Active Directory B2C kiracı oluşturma](/azure/active-directory-b2c/tutorial-create-tenant) ' daki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="63c62-112">Follow the guidance in [Tutorial: Create an Azure Active Directory B2C tenant](/azure/active-directory-b2c/tutorial-create-tenant) to create an AAD B2C tenant.</span></span>

<span data-ttu-id="63c62-113">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="63c62-113">Record the following information:</span></span>

* <span data-ttu-id="63c62-114">AAD B2C örneği (örneğin, `https://contoso.b2clogin.com/` sonunda eğik çizgi içeren)</span><span class="sxs-lookup"><span data-stu-id="63c62-114">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash)</span></span>
* <span data-ttu-id="63c62-115">AAD B2C kiracı etki alanı (örneğin, `contoso.onmicrosoft.com` )</span><span class="sxs-lookup"><span data-stu-id="63c62-115">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`)</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="63c62-116">Sunucu API 'SI uygulaması kaydetme</span><span class="sxs-lookup"><span data-stu-id="63c62-116">Register a server API app</span></span>

<span data-ttu-id="63c62-117">Bir AAD uygulamasını *sunucu API uygulaması*için kaydetmek üzere [öğretici: Azure Active Directory B2C bir uygulamayı kaydetme öğreticisindeki](/azure/active-directory-b2c/tutorial-register-applications) yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="63c62-117">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) to register an AAD app for the *Server API app*:</span></span>

1. <span data-ttu-id="63c62-118">**Azure Active Directory**  >  **uygulama kayıtları** **Yeni kayıt**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="63c62-118">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="63c62-119">Uygulama için bir **ad** sağlayın (örneğin, \*\* Blazor sunucu AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="63c62-119">Provide a **Name** for the app (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="63c62-120">**Desteklenen hesap türleri**için, birden çok kiracılı seçeneği seçin: **herhangi bir kuruluş dizinindeki hesaplar veya herhangi bir kimlik sağlayıcısı. Azure AD B2C kullanıcıları kimlik doğrulaması için.**</span><span class="sxs-lookup"><span data-stu-id="63c62-120">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="63c62-121">*Sunucu API 'si uygulaması* Bu senaryoda **yeniden yönlendirme URI 'si** gerektirmez, bu nedenle açılan kutudan **Web** 'e ve yeniden yönlendirme URI 'si girmeyin.</span><span class="sxs-lookup"><span data-stu-id="63c62-121">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="63c62-122">**İzinlerin**  >  ,**OpenID 'ye yönetici tarafından verildiğini ve offline_access izinleri** olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="63c62-122">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="63c62-123">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="63c62-123">Select **Register**.</span></span>

<span data-ttu-id="63c62-124">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="63c62-124">Record the following information:</span></span>

* <span data-ttu-id="63c62-125">*Sunucu API 'si uygulaması* Uygulama KIMLIĞI (Istemci KIMLIĞI) (örneğin, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="63c62-125">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="63c62-126">Dizin KIMLIĞI (kiracı KIMLIĞI) (örneğin, `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="63c62-126">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="63c62-127">AAD kiracı etki alanı (örneğin, `contoso.onmicrosoft.com` ) &ndash; etki alanı, kayıtlı uygulama Için Azure Portal **marka** dikey penceresinde **Yayımcı etki alanı** olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="63c62-127">AAD Tenant domain (for example, `contoso.onmicrosoft.com`) &ndash; The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="63c62-128">**API 'Yi kullanıma**sunma bölümünde:</span><span class="sxs-lookup"><span data-stu-id="63c62-128">In **Expose an API**:</span></span>

1. <span data-ttu-id="63c62-129">**Kapsam ekle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="63c62-129">Select **Add a scope**.</span></span>
1. <span data-ttu-id="63c62-130">**Kaydet ve devam et**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="63c62-130">Select **Save and continue**.</span></span>
1. <span data-ttu-id="63c62-131">Bir **kapsam adı** sağlayın (örneğin, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="63c62-131">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="63c62-132">**Yönetici izni görünen adı** sağlayın (örneğin, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="63c62-132">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="63c62-133">**Yönetici onay açıklaması** sağlayın (örneğin, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="63c62-133">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="63c62-134">**Durumun** **etkin**olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="63c62-134">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="63c62-135">**Kapsam Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="63c62-135">Select **Add scope**.</span></span>

<span data-ttu-id="63c62-136">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="63c62-136">Record the following information:</span></span>

* <span data-ttu-id="63c62-137">Uygulama KIMLIĞI URI 'SI (örneğin, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` , `api://11111111-1111-1111-1111-111111111111` veya belirttiğiniz özel değer)</span><span class="sxs-lookup"><span data-stu-id="63c62-137">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="63c62-138">Varsayılan kapsam (örneğin, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="63c62-138">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="63c62-139">İstemci uygulamasını kaydetme</span><span class="sxs-lookup"><span data-stu-id="63c62-139">Register a client app</span></span>

<span data-ttu-id="63c62-140">Eğitim bölümündeki yönergeleri izleyerek *istemci uygulaması*IÇIN bir AAD uygulaması kaydetmek üzere [Azure Active Directory B2C bir uygulamayı yeniden kaydedin](/azure/active-directory-b2c/tutorial-register-applications) :</span><span class="sxs-lookup"><span data-stu-id="63c62-140">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="63c62-141">**Azure Active Directory**  >  **uygulama kayıtları** **Yeni kayıt**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="63c62-141">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="63c62-142">Uygulama için bir **ad** sağlayın (örneğin, \*\* Blazor istemci AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="63c62-142">Provide a **Name** for the app (for example, **Blazor Client AAD B2C**).</span></span>
1. <span data-ttu-id="63c62-143">**Desteklenen hesap türleri**için, birden çok kiracılı seçeneği seçin: **herhangi bir kuruluş dizinindeki hesaplar veya herhangi bir kimlik sağlayıcısı. Azure AD B2C kullanıcıları kimlik doğrulaması için.**</span><span class="sxs-lookup"><span data-stu-id="63c62-143">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="63c62-144">**Yeniden yönlendirme URI 'si** açılan öğesini **Web** 'e ayarlı bırakın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="63c62-144">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="63c62-145">Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir.</span><span class="sxs-lookup"><span data-stu-id="63c62-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="63c62-146">Uygulama farklı bir Kestrel bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktasını kullanın.</span><span class="sxs-lookup"><span data-stu-id="63c62-146">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="63c62-147">IIS Express için, uygulama için rastgele oluşturulan bağlantı noktası, **hata ayıklama** panelinde sunucu uygulamasının özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="63c62-147">For IIS Express, the randomly generated port for the app can be found in the Server app's properties in the **Debug** panel.</span></span> <span data-ttu-id="63c62-148">Uygulama bu noktada mevcut olmadığından ve IIS Express bağlantı noktası bilinmediğinden, uygulama oluşturulduktan sonra bu adıma geri dönün ve yeniden yönlendirme URI 'sini güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="63c62-148">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="63c62-149">[Uygulama oluştur](#create-the-app) bölümünde, kullanıcıların YENIDEN yönlendirme URI 'sini güncelleştirmesi IIS Express hatırlatmak için bir açıklama belirir.</span><span class="sxs-lookup"><span data-stu-id="63c62-149">A remark appears in the [Create the app](#create-the-app) section to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="63c62-150">**İzinlerin**  >  ,**OpenID 'ye yönetici tarafından verildiğini ve offline_access izinleri** olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="63c62-150">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="63c62-151">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="63c62-151">Select **Register**.</span></span>

<span data-ttu-id="63c62-152">Uygulama KIMLIĞINI (Istemci KIMLIĞI) kaydedin (örneğin, `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="63c62-152">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="63c62-153">**Kimlik doğrulama**  >  **platformu yapılandırması**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="63c62-153">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="63c62-154">**Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="63c62-154">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="63c62-155">**Örtük izin**Için, **erişim belirteçleri** ve **Kimlik belirteçleri**onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="63c62-155">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="63c62-156">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="63c62-156">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="63c62-157">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="63c62-157">Select the **Save** button.</span></span>

<span data-ttu-id="63c62-158">**API izinleri**:</span><span class="sxs-lookup"><span data-stu-id="63c62-158">In **API permissions**:</span></span>

1. <span data-ttu-id="63c62-159">**Izin Ekle** ' yi ve ardından **API 'lerim**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="63c62-159">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="63c62-160">**Ad** SÜTUNUNDAN *sunucu API uygulamasını* seçin (örneğin, \*\* Blazor sunucu AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="63c62-160">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD B2C**).</span></span>
1. <span data-ttu-id="63c62-161">**API** listesini açın.</span><span class="sxs-lookup"><span data-stu-id="63c62-161">Open the **API** list.</span></span>
1. <span data-ttu-id="63c62-162">API 'ye erişimi etkinleştirin (örneğin, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="63c62-162">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="63c62-163">**Izin Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="63c62-163">Select **Add permissions**.</span></span>
1. <span data-ttu-id="63c62-164">**{Tenant Name} için yönetici Içeriği ver** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="63c62-164">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="63c62-165">Onaylamak için **Evet**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="63c62-165">Select **Yes** to confirm.</span></span>

<span data-ttu-id="63c62-166">**Giriş**  >  **Azure AD B2C**  >  **Kullanıcı akışları**' nda:</span><span class="sxs-lookup"><span data-stu-id="63c62-166">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="63c62-167">Kaydolma ve oturum açma Kullanıcı akışı oluşturma</span><span class="sxs-lookup"><span data-stu-id="63c62-167">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="63c62-168">En azından, **Application claims**  >  **Display Name** `context.User.Identity.Name` `LoginDisplay` bileşende (*paylaşılan/logindisplay. Razor*) doldurmak için uygulama talepleri görünen adı Kullanıcı özniteliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="63c62-168">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="63c62-169">Uygulama için oluşturulan kaydolma ve oturum açma Kullanıcı akış adını kaydedin (örneğin, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="63c62-169">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

### <a name="create-the-app"></a><span data-ttu-id="63c62-170">Uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="63c62-170">Create the app</span></span>

<span data-ttu-id="63c62-171">Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:</span><span class="sxs-lookup"><span data-stu-id="63c62-171">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="63c62-172">Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="63c62-172">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="63c62-173">Klasör adı Ayrıca projenin adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="63c62-173">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="63c62-174">Uygulama KIMLIĞI URI 'sini `app-id-uri` seçeneğe geçirin, ancak [erişim belirteci kapsamları](#access-token-scopes) bölümünde açıklanan istemci uygulamasında bir yapılandırma değişikliği gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="63c62-174">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>
>
> <span data-ttu-id="63c62-175">Ayrıca, barındırılan şablon tarafından ayarlanan kapsamda Blazor uygulama KIMLIĞI URI ana bilgisayarı yineleniyor olabilir.</span><span class="sxs-lookup"><span data-stu-id="63c62-175">Additionally, the scope set up by the Hosted Blazor template might have the App ID URI host repeated.</span></span> <span data-ttu-id="63c62-176">Koleksiyon için yapılandırılan kapsamın `DefaultAccessTokenScopes` `Program.Main` *istemci uygulamasının*(*program.cs*) içinde doğru olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="63c62-176">Confirm that the scope configured for the `DefaultAccessTokenScopes` collection is correct in `Program.Main` (*Program.cs*) of the *Client app*.</span></span>

> [!NOTE]
> <span data-ttu-id="63c62-177">Azure Portal, *istemci uygulamasının* **kimlik doğrulama**  >  **platformu yapılandırması**  >  **Web**  >  **yeniden yönlendirme URI 'si** , Kestrel sunucusunda varsayılan ayarlarla çalışan uygulamalar için bağlantı noktası 5001 için yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="63c62-177">In the Azure portal, the *Client app's* **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="63c62-178">*İstemci uygulaması* rastgele bir IIS Express bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktası **hata ayıklama** panelinde *sunucu uygulamasının* özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="63c62-178">If the *Client app* is run on a random IIS Express port, the port for the app can be found in the *Server app's* properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="63c62-179">Bağlantı noktası, *istemci uygulamasının* bilinen bağlantı noktasıyla daha önce yapılandırılmamışsa, Azure Portal *istemci uygulamanın* kaydına dönün ve yeniden yönlendirme URI 'sini doğru bağlantı noktasıyla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="63c62-179">If the port wasn't configured earlier with the *Client app's* known port, return to the *Client app's* registration in the Azure portal and update the redirect URI with the correct port.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="63c62-180">Sunucu uygulaması yapılandırması</span><span class="sxs-lookup"><span data-stu-id="63c62-180">Server app configuration</span></span>

<span data-ttu-id="63c62-181">*Bu bölüm, çözümün **sunucu** uygulamasıyla ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="63c62-181">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="63c62-182">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="63c62-182">Authentication package</span></span>

<span data-ttu-id="63c62-183">ASP.NET Core Web API 'Lerine yönelik kimlik doğrulama ve yetkilendirme desteği şu şekilde sağlanır `Microsoft.AspNetCore.Authentication.AzureADB2C.UI` :</span><span class="sxs-lookup"><span data-stu-id="63c62-183">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="63c62-184">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="63c62-184">Authentication service support</span></span>

<span data-ttu-id="63c62-185">`AddAuthentication`Yöntemi, uygulama içinde kimlik doğrulama hizmetlerini ayarlar ve JWT taşıyıcı işleyicisini varsayılan kimlik doğrulama yöntemi olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="63c62-185">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="63c62-186">`AddAzureADB2CBearer`Yöntemi, Azure Active Directory B2C tarafından yayılan belirteçleri doğrulamak için gereken JWT taşıyıcı işleyicisinde belirli parametreleri ayarlar:</span><span class="sxs-lookup"><span data-stu-id="63c62-186">The `AddAzureADB2CBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory B2C:</span></span>

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<span data-ttu-id="63c62-187">`UseAuthentication``UseAuthorization`aşağıdakileri doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="63c62-187">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="63c62-188">Uygulama, gelen isteklerde belirteçleri ayrıştırmaya ve doğrulamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="63c62-188">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="63c62-189">Uygun kimlik bilgileri olmadan korunan kaynağa erişmeye çalışan istekler başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="63c62-189">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="63c62-190">Kullanıcı. Identity . Ada</span><span class="sxs-lookup"><span data-stu-id="63c62-190">User.Identity.Name</span></span>

<span data-ttu-id="63c62-191">Varsayılan olarak, `User.Identity.Name` doldurulmaz.</span><span class="sxs-lookup"><span data-stu-id="63c62-191">By default, the `User.Identity.Name` isn't populated.</span></span>

<span data-ttu-id="63c62-192">Uygulamayı talep türünden değeri alacak şekilde yapılandırmak için, `name` Içindeki [Tokenvalidationparameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) ' ı yapılandırın <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="63c62-192">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="63c62-193">Uygulama ayarları</span><span class="sxs-lookup"><span data-stu-id="63c62-193">App settings</span></span>

<span data-ttu-id="63c62-194">*AppSettings. JSON* dosyası, erişim belirteçlerini doğrulamak IÇIN kullanılan JWT taşıyıcı işleyicisini yapılandırma seçeneklerini içerir.</span><span class="sxs-lookup"><span data-stu-id="63c62-194">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens.</span></span>

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

<span data-ttu-id="63c62-195">Örnek:</span><span class="sxs-lookup"><span data-stu-id="63c62-195">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="63c62-196">Hava tahmin denetleyicisi</span><span class="sxs-lookup"><span data-stu-id="63c62-196">WeatherForecast controller</span></span>

<span data-ttu-id="63c62-197">Dalgalı tahmin denetleyicisi (*denetleyiciler/dalgalı Therforetcontroller. cs*), BIR korumalı API 'yi `[Authorize]` denetleyiciye uygulanmış şekilde gösterir.</span><span class="sxs-lookup"><span data-stu-id="63c62-197">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="63c62-198">Bunun anlaşılması **önemlidir** :</span><span class="sxs-lookup"><span data-stu-id="63c62-198">It's **important** to understand that:</span></span>

* <span data-ttu-id="63c62-199">Bu `[Authorize]` API denetleyicisindeki özniteliği, bu API 'yi yetkisiz erişime karşı koruyan tek şeydir.</span><span class="sxs-lookup"><span data-stu-id="63c62-199">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="63c62-200">`[Authorize]` Blazor Webassembly uygulamasında kullanılan özniteliği yalnızca uygulamanın, uygulamanın düzgün şekilde çalışması için yetkilendirilmiş olması gerektiğine yönelik bir ipucu görevi görür.</span><span class="sxs-lookup"><span data-stu-id="63c62-200">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="63c62-201">İstemci uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="63c62-201">Client app configuration</span></span>

<span data-ttu-id="63c62-202">*Bu bölüm, çözümün **istemci** uygulaması ile ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="63c62-202">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="63c62-203">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="63c62-203">Authentication package</span></span>

<span data-ttu-id="63c62-204">Tek bir B2C hesabı () kullanmak üzere bir uygulama oluşturulduğunda `IndividualB2C` , uygulama otomatik olarak [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) () için bir paket başvurusu alır `Microsoft.Authentication.WebAssembly.Msal` .</span><span class="sxs-lookup"><span data-stu-id="63c62-204">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="63c62-205">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="63c62-205">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="63c62-206">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="63c62-206">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="63c62-207">`Microsoft.Authentication.WebAssembly.Msal`Paket geçişli `Microsoft.AspNetCore.Components.WebAssembly.Authentication` olarak uygulamayı uygulamaya ekler.</span><span class="sxs-lookup"><span data-stu-id="63c62-207">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="63c62-208">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="63c62-208">Authentication service support</span></span>

<span data-ttu-id="63c62-209">`HttpClient`Sunucu projesine istek yaparken erişim belirteçlerini içeren örnekler için destek eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="63c62-209">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="63c62-210">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="63c62-210">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="63c62-211">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında `AddMsalAuthentication` paket tarafından sağlanmış uzantı yöntemiyle kaydedilir `Microsoft.Authentication.WebAssembly.Msal` .</span><span class="sxs-lookup"><span data-stu-id="63c62-211">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="63c62-212">Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="63c62-212">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="63c62-213">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="63c62-213">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="63c62-214">`AddMsalAuthentication`Yöntemi, bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="63c62-214">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="63c62-215">Uygulamanın yapılandırılması için gereken değerler, uygulamayı kaydettiğinizde Azure Portal AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="63c62-215">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="63c62-216">Yapılandırma *Wwwroot/appSettings. JSON* dosyası tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="63c62-216">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="63c62-217">Örnek:</span><span class="sxs-lookup"><span data-stu-id="63c62-217">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="63c62-218">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="63c62-218">Access token scopes</span></span>

<span data-ttu-id="63c62-219">Varsayılan erişim belirteci kapsamları, erişim belirteci kapsamlarının listesini temsil eder:</span><span class="sxs-lookup"><span data-stu-id="63c62-219">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="63c62-220">Oturum açma isteğine varsayılan olarak dahildir.</span><span class="sxs-lookup"><span data-stu-id="63c62-220">Included by default in the sign in request.</span></span>
* <span data-ttu-id="63c62-221">Kimlik doğrulamasından hemen sonra bir erişim belirteci sağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="63c62-221">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="63c62-222">Tüm kapsamlar Azure Active Directory kuralları başına aynı uygulamaya ait olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="63c62-222">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="63c62-223">Gerektiğinde ek API uygulamaları için ek kapsamlar eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="63c62-223">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="63c62-224">Daha fazla bilgi için *ek senaryolar* makalesinin aşağıdaki bölümlerine bakın:</span><span class="sxs-lookup"><span data-stu-id="63c62-224">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="63c62-225">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="63c62-225">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="63c62-226">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="63c62-226">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="63c62-227">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="63c62-227">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="63c62-228">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="63c62-228">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="63c62-229">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="63c62-229">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="63c62-230">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="63c62-230">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="63c62-231">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="63c62-231">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="63c62-232">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="63c62-232">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="63c62-233">FetchData bileşeni</span><span class="sxs-lookup"><span data-stu-id="63c62-233">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="63c62-234">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="63c62-234">Run the app</span></span>

<span data-ttu-id="63c62-235">Uygulamayı sunucu projesinden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="63c62-235">Run the app from the Server project.</span></span> <span data-ttu-id="63c62-236">Visual Studio 'yu kullanırken şunlardan birini yapın:</span><span class="sxs-lookup"><span data-stu-id="63c62-236">When using Visual Studio, either:</span></span>

* <span data-ttu-id="63c62-237">Araç çubuğundaki **başlangıç projeleri** açılan listesini *sunucu API 'si uygulamasına* ayarlayın ve **Çalıştır** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="63c62-237">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="63c62-238">**Çözüm Gezgini** ' de sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya uygulamayı **Hata Ayıkla** menüsünden başlatın.</span><span class="sxs-lookup"><span data-stu-id="63c62-238">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="63c62-239">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="63c62-239">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="63c62-240">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="63c62-240">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="63c62-241">Öğretici: Azure Active Directory B2C kiracısı oluşturma</span><span class="sxs-lookup"><span data-stu-id="63c62-241">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="63c62-242">Microsoft kimlik platformu belgeleri</span><span class="sxs-lookup"><span data-stu-id="63c62-242">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
