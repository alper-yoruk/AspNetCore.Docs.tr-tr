---
<span data-ttu-id="9fcca-101">Başlık: ' Blazor Azure Active Directory ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc ile barındırılan bir uygulamayı ASP.NET Core güvenli hale getirme:</span><span class="sxs-lookup"><span data-stu-id="9fcca-101">title: 'Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="9fcca-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="9fcca-102">'Blazor'</span></span>
- <span data-ttu-id="9fcca-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="9fcca-103">'Identity'</span></span>
- <span data-ttu-id="9fcca-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="9fcca-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="9fcca-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="9fcca-105">'Razor'</span></span>
- <span data-ttu-id="9fcca-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="9fcca-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a><span data-ttu-id="9fcca-107">Azure Active Directory bir ASP.NET Core Blazor weelsembly barındırılan uygulamasının güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="9fcca-107">Secure an ASP.NET Core Blazor WebAssembly hosted app with Azure Active Directory</span></span>

<span data-ttu-id="9fcca-108">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="9fcca-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="9fcca-109">Bu makalede, kimlik doğrulaması için [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) kullanan bir [ Blazor webassembly barındırılan uygulamasının](xref:blazor/hosting-models#blazor-webassembly) nasıl oluşturulacağı açıklanır.</span><span class="sxs-lookup"><span data-stu-id="9fcca-109">This article describes how to create a [Blazor WebAssembly hosted app](xref:blazor/hosting-models#blazor-webassembly) that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication.</span></span>

## <a name="register-apps-in-aad-and-create-solution"></a><span data-ttu-id="9fcca-110">AAD 'de uygulama kaydetme ve çözüm oluşturma</span><span class="sxs-lookup"><span data-stu-id="9fcca-110">Register apps in AAD and create solution</span></span>

### <a name="create-a-tenant"></a><span data-ttu-id="9fcca-111">Kiracı oluşturma</span><span class="sxs-lookup"><span data-stu-id="9fcca-111">Create a tenant</span></span>

<span data-ttu-id="9fcca-112">Hızlı başlangıç: AAD 'de kiracı oluşturmak için [bir kiracı ayarlama](/azure/active-directory/develop/quickstart-create-new-tenant) bölümündeki yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="9fcca-112">Follow the guidance in [Quickstart: Set up a tenant](/azure/active-directory/develop/quickstart-create-new-tenant) to create a tenant in AAD.</span></span>

### <a name="register-a-server-api-app"></a><span data-ttu-id="9fcca-113">Sunucu API 'SI uygulaması kaydetme</span><span class="sxs-lookup"><span data-stu-id="9fcca-113">Register a server API app</span></span>

<span data-ttu-id="9fcca-114">*Sunucu API uygulaması*IÇIN bir AAD uygulaması kaydetmek üzere [hızlı başlangıç: Microsoft Identity platformu ve sonraki Azure AAD Ile bir uygulamayı kaydetme](/azure/active-directory/develop/quickstart-register-app) bölümündeki yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="9fcca-114">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register an AAD app for the *Server API app*:</span></span>

1. <span data-ttu-id="9fcca-115">**Azure Active Directory**  >  **uygulama kayıtları** **Yeni kayıt**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="9fcca-115">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="9fcca-116">Uygulama için bir **ad** sağlayın (örneğin, \*\* Blazor sunucu AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="9fcca-116">Provide a **Name** for the app (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="9fcca-117">Desteklenen bir **Hesap türü**seçin.</span><span class="sxs-lookup"><span data-stu-id="9fcca-117">Choose a **Supported account types**.</span></span> <span data-ttu-id="9fcca-118">Bu deneyim için **yalnızca bu kuruluş dizininde** (tek kiracı) hesaplar seçebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9fcca-118">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="9fcca-119">*Sunucu API 'si uygulaması* Bu senaryoda **yeniden yönlendirme URI 'si** gerektirmez, bu nedenle açılan kutudan **Web** 'e ve yeniden yönlendirme URI 'si girmeyin.</span><span class="sxs-lookup"><span data-stu-id="9fcca-119">The *Server API app* doesn't require a **Redirect URI** in this scenario, so leave the drop down set to **Web** and don't enter a redirect URI.</span></span>
1. <span data-ttu-id="9fcca-120">Yönetici tarafından **Permissions**  >  **OpenID ve offline_access izinleri için izin ver** onay kutusunu devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="9fcca-120">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="9fcca-121">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="9fcca-121">Select **Register**.</span></span>

<span data-ttu-id="9fcca-122">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="9fcca-122">Record the following information:</span></span>

* <span data-ttu-id="9fcca-123">*Sunucu API 'si uygulaması* Uygulama KIMLIĞI (Istemci KIMLIĞI) (örneğin, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="9fcca-123">*Server API app* Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="9fcca-124">Dizin KIMLIĞI (kiracı KIMLIĞI) (örneğin, `222222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="9fcca-124">Directory ID (Tenant ID) (for example, `222222222-2222-2222-2222-222222222222`)</span></span>
* <span data-ttu-id="9fcca-125">AAD kiracı etki alanı (örneğin, `contoso.onmicrosoft.com` ) &ndash; etki alanı, kayıtlı uygulama Için Azure Portal **marka** dikey penceresinde **Yayımcı etki alanı** olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="9fcca-125">AAD Tenant domain (for example, `contoso.onmicrosoft.com`) &ndash; The domain is available as the **Publisher domain** in the **Branding** blade of the Azure portal for the registered app.</span></span>

<span data-ttu-id="9fcca-126">**API izinlerinde**, **Microsoft Graph**  >  uygulama oturum açma veya Kullanıcı profili erişimi gerektirmediğinden Microsoft Graph**User. Read** iznini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="9fcca-126">In **API permissions**, remove the **Microsoft Graph** > **User.Read** permission, as the app doesn't require sign in or user profile access.</span></span>

<span data-ttu-id="9fcca-127">**API 'Yi kullanıma**sunma bölümünde:</span><span class="sxs-lookup"><span data-stu-id="9fcca-127">In **Expose an API**:</span></span>

1. <span data-ttu-id="9fcca-128">**Kapsam ekle**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="9fcca-128">Select **Add a scope**.</span></span>
1. <span data-ttu-id="9fcca-129">**Kaydet ve devam et**’i seçin.</span><span class="sxs-lookup"><span data-stu-id="9fcca-129">Select **Save and continue**.</span></span>
1. <span data-ttu-id="9fcca-130">Bir **kapsam adı** sağlayın (örneğin, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="9fcca-130">Provide a **Scope name** (for example, `API.Access`).</span></span>
1. <span data-ttu-id="9fcca-131">**Yönetici izni görünen adı** sağlayın (örneğin, `Access API` ).</span><span class="sxs-lookup"><span data-stu-id="9fcca-131">Provide an **Admin consent display name** (for example, `Access API`).</span></span>
1. <span data-ttu-id="9fcca-132">**Yönetici onay açıklaması** sağlayın (örneğin, `Allows the app to access server app API endpoints.` ).</span><span class="sxs-lookup"><span data-stu-id="9fcca-132">Provide an **Admin consent description** (for example, `Allows the app to access server app API endpoints.`).</span></span>
1. <span data-ttu-id="9fcca-133">**Durumun** **etkin**olarak ayarlandığını onaylayın.</span><span class="sxs-lookup"><span data-stu-id="9fcca-133">Confirm that the **State** is set to **Enabled**.</span></span>
1. <span data-ttu-id="9fcca-134">**Kapsam Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="9fcca-134">Select **Add scope**.</span></span>

<span data-ttu-id="9fcca-135">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="9fcca-135">Record the following information:</span></span>

* <span data-ttu-id="9fcca-136">Uygulama KIMLIĞI URI 'SI (örneğin, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` , `api://11111111-1111-1111-1111-111111111111` veya belirttiğiniz özel değer)</span><span class="sxs-lookup"><span data-stu-id="9fcca-136">App ID URI (for example, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, or the custom value that you provided)</span></span>
* <span data-ttu-id="9fcca-137">Varsayılan kapsam (örneğin, `API.Access` )</span><span class="sxs-lookup"><span data-stu-id="9fcca-137">Default scope (for example, `API.Access`)</span></span>

### <a name="register-a-client-app"></a><span data-ttu-id="9fcca-138">İstemci uygulamasını kaydetme</span><span class="sxs-lookup"><span data-stu-id="9fcca-138">Register a client app</span></span>

<span data-ttu-id="9fcca-139">*İstemci uygulaması*IÇIN bir AAD uygulaması kaydetmek üzere [hızlı başlangıç: Microsoft Identity platformu ve sonraki Azure AAD Ile bir uygulamayı kaydetme](/azure/active-directory/develop/quickstart-register-app) bölümündeki yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="9fcca-139">Follow the guidance in [Quickstart: Register an application with the Microsoft identity platform](/azure/active-directory/develop/quickstart-register-app) and subsequent Azure AAD topics to register a AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="9fcca-140">**Azure Active Directory**  >  **uygulama kayıtları** **Yeni kayıt**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="9fcca-140">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="9fcca-141">Uygulama için bir **ad** sağlayın (örneğin, \*\* Blazor istemci AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="9fcca-141">Provide a **Name** for the app (for example, **Blazor Client AAD**).</span></span>
1. <span data-ttu-id="9fcca-142">Desteklenen bir **Hesap türü**seçin.</span><span class="sxs-lookup"><span data-stu-id="9fcca-142">Choose a **Supported account types**.</span></span> <span data-ttu-id="9fcca-143">Bu deneyim için **yalnızca bu kuruluş dizininde** (tek kiracı) hesaplar seçebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="9fcca-143">You may select **Accounts in this organizational directory only** (single tenant) for this experience.</span></span>
1. <span data-ttu-id="9fcca-144">**Yeniden yönlendirme URI 'si** açılan öğesini **Web**olarak ayarlayın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="9fcca-144">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="9fcca-145">Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir.</span><span class="sxs-lookup"><span data-stu-id="9fcca-145">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="9fcca-146">IIS Express için, rastgele oluşturulan bağlantı noktası, **hata ayıklama** panelinde sunucu uygulamasının özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="9fcca-146">For IIS Express, the randomly generated port can be found in the Server app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="9fcca-147">Yönetici tarafından **Permissions**  >  **OpenID ve offline_access izinleri için izin ver** onay kutusunu devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="9fcca-147">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="9fcca-148">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="9fcca-148">Select **Register**.</span></span>

<span data-ttu-id="9fcca-149">*İstemci* UYGULAMASı uygulama kimliğini (istemci kimliği) kaydedin (örneğin, `33333333-3333-3333-3333-333333333333` ).</span><span class="sxs-lookup"><span data-stu-id="9fcca-149">Record the *Client app* Application ID (Client ID) (for example, `33333333-3333-3333-3333-333333333333`).</span></span>

<span data-ttu-id="9fcca-150">**Kimlik doğrulama**  >  **platformu yapılandırması**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="9fcca-150">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="9fcca-151">**Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="9fcca-151">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="9fcca-152">**Örtük izin**Için, **erişim belirteçleri** ve **Kimlik belirteçleri**onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="9fcca-152">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="9fcca-153">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="9fcca-153">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="9fcca-154">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="9fcca-154">Select the **Save** button.</span></span>

<span data-ttu-id="9fcca-155">**API izinleri**:</span><span class="sxs-lookup"><span data-stu-id="9fcca-155">In **API permissions**:</span></span>

1. <span data-ttu-id="9fcca-156">Uygulamanın **Microsoft Graph**  >  **User. Read** iznine sahip olduğunu doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="9fcca-156">Confirm that the app has **Microsoft Graph** > **User.Read** permission.</span></span>
1. <span data-ttu-id="9fcca-157">**Izin Ekle** ' yi ve ardından **API 'lerim**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="9fcca-157">Select **Add a permission** followed by **My APIs**.</span></span>
1. <span data-ttu-id="9fcca-158">**Ad** SÜTUNUNDAN *sunucu API uygulamasını* (örneğin, \*\* Blazor sunucu AAD\*\*) seçin.</span><span class="sxs-lookup"><span data-stu-id="9fcca-158">Select the *Server API app* from the **Name** column (for example, **Blazor Server AAD**).</span></span>
1. <span data-ttu-id="9fcca-159">**API** listesini açın.</span><span class="sxs-lookup"><span data-stu-id="9fcca-159">Open the **API** list.</span></span>
1. <span data-ttu-id="9fcca-160">API 'ye erişimi etkinleştirin (örneğin, `API.Access` ).</span><span class="sxs-lookup"><span data-stu-id="9fcca-160">Enable access to the API (for example, `API.Access`).</span></span>
1. <span data-ttu-id="9fcca-161">**Izin Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="9fcca-161">Select **Add permissions**.</span></span>
1. <span data-ttu-id="9fcca-162">**{Tenant Name} için yönetici Içeriği ver** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="9fcca-162">Select the **Grant admin content for {TENANT NAME}** button.</span></span> <span data-ttu-id="9fcca-163">Onaylamak için **Evet**'i seçin.</span><span class="sxs-lookup"><span data-stu-id="9fcca-163">Select **Yes** to confirm.</span></span>

### <a name="create-the-app"></a><span data-ttu-id="9fcca-164">Uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="9fcca-164">Create the app</span></span>

<span data-ttu-id="9fcca-165">Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:</span><span class="sxs-lookup"><span data-stu-id="9fcca-165">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

<span data-ttu-id="9fcca-166">Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="9fcca-166">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="9fcca-167">Klasör adı Ayrıca projenin adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="9fcca-167">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="9fcca-168">Uygulama KIMLIĞI URI 'sini `app-id-uri` seçeneğe geçirin, ancak [erişim belirteci kapsamları](#access-token-scopes) bölümünde açıklanan istemci uygulamasında bir yapılandırma değişikliği gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="9fcca-168">Pass the App ID URI to the `app-id-uri` option, but note a configuration change might be required in the client app, which is described in the [Access token scopes](#access-token-scopes) section.</span></span>

## <a name="server-app-configuration"></a><span data-ttu-id="9fcca-169">Sunucu uygulaması yapılandırması</span><span class="sxs-lookup"><span data-stu-id="9fcca-169">Server app configuration</span></span>

<span data-ttu-id="9fcca-170">*Bu bölüm, çözümün **sunucu** uygulamasıyla ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="9fcca-170">*This section pertains to the solution's **Server** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="9fcca-171">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="9fcca-171">Authentication package</span></span>

<span data-ttu-id="9fcca-172">ASP.NET Core Web API 'Lerine yönelik kimlik doğrulama ve yetkilendirme desteği şu şekilde sağlanır `Microsoft.AspNetCore.Authentication.AzureAD.UI` :</span><span class="sxs-lookup"><span data-stu-id="9fcca-172">The support for authenticating and authorizing calls to ASP.NET Core Web APIs is provided by the `Microsoft.AspNetCore.Authentication.AzureAD.UI`:</span></span>

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.2.0" />
```

### <a name="authentication-service-support"></a><span data-ttu-id="9fcca-173">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="9fcca-173">Authentication service support</span></span>

<span data-ttu-id="9fcca-174">`AddAuthentication`Yöntemi, uygulama içinde kimlik doğrulama hizmetlerini ayarlar ve JWT taşıyıcı işleyicisini varsayılan kimlik doğrulama yöntemi olarak yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="9fcca-174">The `AddAuthentication` method sets up authentication services within the app and configures the JWT Bearer handler as the default authentication method.</span></span> <span data-ttu-id="9fcca-175">`AddAzureADBearer`Yöntemi, Azure Active Directory tarafından yayılan belirteçleri doğrulamak için gereken JWT taşıyıcı işleyicisinde belirli parametreleri ayarlar:</span><span class="sxs-lookup"><span data-stu-id="9fcca-175">The `AddAzureADBearer` method sets up the specific parameters in the JWT Bearer handler required to validate tokens emitted by the Azure Active Directory:</span></span>

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<span data-ttu-id="9fcca-176">`UseAuthentication``UseAuthorization`aşağıdakileri doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="9fcca-176">`UseAuthentication` and `UseAuthorization` ensure that:</span></span>

* <span data-ttu-id="9fcca-177">Uygulama, gelen isteklerde belirteçleri ayrıştırmaya ve doğrulamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="9fcca-177">The app attempts to parse and validate tokens on incoming requests.</span></span>
* <span data-ttu-id="9fcca-178">Uygun kimlik bilgileri olmadan korunan kaynağa erişmeye çalışan istekler başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="9fcca-178">Any request attempting to access a protected resource without proper credentials fails.</span></span>

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a><span data-ttu-id="9fcca-179">Kullanıcı. Identity . Ada</span><span class="sxs-lookup"><span data-stu-id="9fcca-179">User.Identity.Name</span></span>

<span data-ttu-id="9fcca-180">Varsayılan olarak, sunucu uygulaması API 'SI `User.Identity.Name` talep türünden değer ile doldurulur `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` (örneğin, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="9fcca-180">By default, the Server app API populates `User.Identity.Name` with the value from the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim type (for example, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="9fcca-181">Uygulamayı talep türünden değeri alacak şekilde yapılandırmak için, `name` Içindeki [Tokenvalidationparameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) ' ı yapılandırın <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="9fcca-181">To configure the app to receive the value from the `name` claim type, configure the [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) of the <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> in `Startup.ConfigureServices`:</span></span>

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a><span data-ttu-id="9fcca-182">Uygulama ayarları</span><span class="sxs-lookup"><span data-stu-id="9fcca-182">App settings</span></span>

<span data-ttu-id="9fcca-183">*AppSettings. JSON* dosyası, erişim belirteçlerini doğrulamak IÇIN kullanılan JWT taşıyıcı işleyicisini yapılandırma seçeneklerini içerir:</span><span class="sxs-lookup"><span data-stu-id="9fcca-183">The *appsettings.json* file contains the options to configure the JWT bearer handler used to validate access tokens:</span></span>

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

<span data-ttu-id="9fcca-184">Örnek:</span><span class="sxs-lookup"><span data-stu-id="9fcca-184">Example:</span></span>

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

### <a name="weatherforecast-controller"></a><span data-ttu-id="9fcca-185">Hava tahmin denetleyicisi</span><span class="sxs-lookup"><span data-stu-id="9fcca-185">WeatherForecast controller</span></span>

<span data-ttu-id="9fcca-186">Dalgalı tahmin denetleyicisi (*denetleyiciler/dalgalı Therforetcontroller. cs*), BIR korumalı API 'yi `[Authorize]` denetleyiciye uygulanmış şekilde gösterir.</span><span class="sxs-lookup"><span data-stu-id="9fcca-186">The WeatherForecast controller (*Controllers/WeatherForecastController.cs*) exposes a protected API with the `[Authorize]` attribute applied to the controller.</span></span> <span data-ttu-id="9fcca-187">Bunun anlaşılması **önemlidir** :</span><span class="sxs-lookup"><span data-stu-id="9fcca-187">It's **important** to understand that:</span></span>

* <span data-ttu-id="9fcca-188">Bu `[Authorize]` API denetleyicisindeki özniteliği, bu API 'yi yetkisiz erişime karşı koruyan tek şeydir.</span><span class="sxs-lookup"><span data-stu-id="9fcca-188">The `[Authorize]` attribute in this API controller is the only thing that protect this API from unauthorized access.</span></span>
* <span data-ttu-id="9fcca-189">`[Authorize]` Blazor Webassembly uygulamasında kullanılan özniteliği yalnızca uygulamanın, uygulamanın düzgün şekilde çalışması için yetkilendirilmiş olması gerektiğine yönelik bir ipucu görevi görür.</span><span class="sxs-lookup"><span data-stu-id="9fcca-189">The `[Authorize]` attribute used in the Blazor WebAssembly app only serves as a hint to the app that the user should be authorized for the app to work correctly.</span></span>

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

## <a name="client-app-configuration"></a><span data-ttu-id="9fcca-190">İstemci uygulama yapılandırması</span><span class="sxs-lookup"><span data-stu-id="9fcca-190">Client app configuration</span></span>

<span data-ttu-id="9fcca-191">*Bu bölüm, çözümün **istemci** uygulaması ile ilgilidir.*</span><span class="sxs-lookup"><span data-stu-id="9fcca-191">*This section pertains to the solution's **Client** app.*</span></span>

### <a name="authentication-package"></a><span data-ttu-id="9fcca-192">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="9fcca-192">Authentication package</span></span>

<span data-ttu-id="9fcca-193">Iş veya okul hesaplarını () kullanmak üzere bir uygulama oluşturulduğunda `SingleOrg` , uygulama [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) () için bir paket başvurusu otomatik olarak alır `Microsoft.Authentication.WebAssembly.Msal` .</span><span class="sxs-lookup"><span data-stu-id="9fcca-193">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="9fcca-194">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="9fcca-194">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="9fcca-195">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="9fcca-195">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="9fcca-196">`Microsoft.Authentication.WebAssembly.Msal`Paket geçişli `Microsoft.AspNetCore.Components.WebAssembly.Authentication` olarak uygulamayı uygulamaya ekler.</span><span class="sxs-lookup"><span data-stu-id="9fcca-196">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

### <a name="authentication-service-support"></a><span data-ttu-id="9fcca-197">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="9fcca-197">Authentication service support</span></span>

<span data-ttu-id="9fcca-198">`HttpClient`Sunucu projesine istek yaparken erişim belirteçlerini içeren örnekler için destek eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="9fcca-198">Support for `HttpClient` instances is added that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="9fcca-199">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9fcca-199">*Program.cs*:</span></span>

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

<span data-ttu-id="9fcca-200">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında `AddMsalAuthentication` paket tarafından sağlanmış uzantı yöntemiyle kaydedilir `Microsoft.Authentication.WebAssembly.Msal` .</span><span class="sxs-lookup"><span data-stu-id="9fcca-200">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="9fcca-201">Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="9fcca-201">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="9fcca-202">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="9fcca-202">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="9fcca-203">`AddMsalAuthentication`Yöntemi, bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="9fcca-203">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="9fcca-204">Uygulamanın yapılandırılması için gereken değerler, uygulamayı kaydettiğinizde Azure Portal AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="9fcca-204">The values required for configuring the app can be obtained from the Azure Portal AAD configuration when you register the app.</span></span>

<span data-ttu-id="9fcca-205">Yapılandırma *Wwwroot/appSettings. JSON* dosyası tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="9fcca-205">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="9fcca-206">Örnek:</span><span class="sxs-lookup"><span data-stu-id="9fcca-206">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a><span data-ttu-id="9fcca-207">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="9fcca-207">Access token scopes</span></span>

<span data-ttu-id="9fcca-208">Varsayılan erişim belirteci kapsamları, erişim belirteci kapsamlarının listesini temsil eder:</span><span class="sxs-lookup"><span data-stu-id="9fcca-208">The default access token scopes represent the list of access token scopes that are:</span></span>

* <span data-ttu-id="9fcca-209">Oturum açma isteğine varsayılan olarak dahildir.</span><span class="sxs-lookup"><span data-stu-id="9fcca-209">Included by default in the sign in request.</span></span>
* <span data-ttu-id="9fcca-210">Kimlik doğrulamasından hemen sonra bir erişim belirteci sağlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9fcca-210">Used to provision an access token immediately after authentication.</span></span>

<span data-ttu-id="9fcca-211">Tüm kapsamlar Azure Active Directory kuralları başına aynı uygulamaya ait olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="9fcca-211">All scopes must belong to the same app per Azure Active Directory rules.</span></span> <span data-ttu-id="9fcca-212">Gerektiğinde ek API uygulamaları için ek kapsamlar eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="9fcca-212">Additional scopes can be added for additional API apps as needed:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="9fcca-213">Daha fazla bilgi için *ek senaryolar* makalesinin aşağıdaki bölümlerine bakın:</span><span class="sxs-lookup"><span data-stu-id="9fcca-213">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="9fcca-214">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="9fcca-214">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="9fcca-215">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="9fcca-215">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a><span data-ttu-id="9fcca-216">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="9fcca-216">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a><span data-ttu-id="9fcca-217">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="9fcca-217">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a><span data-ttu-id="9fcca-218">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="9fcca-218">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a><span data-ttu-id="9fcca-219">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="9fcca-219">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a><span data-ttu-id="9fcca-220">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="9fcca-220">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a><span data-ttu-id="9fcca-221">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="9fcca-221">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a><span data-ttu-id="9fcca-222">FetchData bileşeni</span><span class="sxs-lookup"><span data-stu-id="9fcca-222">FetchData component</span></span>

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a><span data-ttu-id="9fcca-223">Uygulamayı çalıştırma</span><span class="sxs-lookup"><span data-stu-id="9fcca-223">Run the app</span></span>

<span data-ttu-id="9fcca-224">Uygulamayı sunucu projesinden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="9fcca-224">Run the app from the Server project.</span></span> <span data-ttu-id="9fcca-225">Visual Studio 'yu kullanırken şunlardan birini yapın:</span><span class="sxs-lookup"><span data-stu-id="9fcca-225">When using Visual Studio, either:</span></span>

* <span data-ttu-id="9fcca-226">Araç çubuğundaki **başlangıç projeleri** açılan listesini *sunucu API 'si uygulamasına* ayarlayın ve **Çalıştır** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="9fcca-226">Set the **Startup Projects** drop down list in the toolbar to the *Server API app* and select the **Run** button.</span></span>
* <span data-ttu-id="9fcca-227">**Çözüm Gezgini** ' de sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya uygulamayı **Hata Ayıkla** menüsünden başlatın.</span><span class="sxs-lookup"><span data-stu-id="9fcca-227">Select the Server project in **Solution Explorer** and select the **Run** button in the toolbar or start the app from the **Debug** menu.</span></span>

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="9fcca-228">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9fcca-228">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="9fcca-229">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="9fcca-229">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="9fcca-230">Microsoft kimlik platformu belgeleri</span><span class="sxs-lookup"><span data-stu-id="9fcca-230">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
