---
<span data-ttu-id="271b3-101">Başlık: ASP.NET Core ' Blazor Azure Active Directory B2C ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="271b3-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="271b3-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="271b3-102">'Blazor'</span></span>
- <span data-ttu-id="271b3-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="271b3-103">'Identity'</span></span>
- <span data-ttu-id="271b3-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="271b3-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="271b3-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="271b3-105">'Razor'</span></span>
- <span data-ttu-id="271b3-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="271b3-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a><span data-ttu-id="271b3-107">Azure Active Directory B2C bir ASP.NET Core Blazor webassembly tek başına uygulamasının güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="271b3-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory B2C</span></span>

<span data-ttu-id="271b3-108">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="271b3-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="271b3-109">BlazorKimlik doğrulaması için [Azure ACTIVE DIRECTORY (AAD) B2C](/azure/active-directory-b2c/overview) kullanan bir webassembly tek başına uygulaması oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="271b3-109">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) for authentication:</span></span>

<span data-ttu-id="271b3-110">Bir kiracı oluşturmak ve bir Web uygulamasını Azure portalında kaydetmek için aşağıdaki konulardaki yönergeleri izleyin:</span><span class="sxs-lookup"><span data-stu-id="271b3-110">Follow the guidance in the following topics to create a tenant and register a web app in the Azure Portal:</span></span>

[<span data-ttu-id="271b3-111">AAD B2C kiracı oluşturma</span><span class="sxs-lookup"><span data-stu-id="271b3-111">Create an AAD B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)

<span data-ttu-id="271b3-112">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="271b3-112">Record the following information:</span></span>

* <span data-ttu-id="271b3-113">AAD B2C örneği (örneğin, `https://contoso.b2clogin.com/` sonunda eğik çizgi içeren).</span><span class="sxs-lookup"><span data-stu-id="271b3-113">AAD B2C instance (for example, `https://contoso.b2clogin.com/`, which includes the trailing slash).</span></span>
* <span data-ttu-id="271b3-114">AAD B2C kiracı etki alanı (örneğin, `contoso.onmicrosoft.com` ).</span><span class="sxs-lookup"><span data-stu-id="271b3-114">AAD B2C Tenant domain (for example, `contoso.onmicrosoft.com`).</span></span>

<span data-ttu-id="271b3-115">Eğitim bölümündeki yönergeleri izleyerek *istemci uygulaması*IÇIN bir AAD uygulaması kaydetmek üzere [Azure Active Directory B2C bir uygulamayı yeniden kaydedin](/azure/active-directory-b2c/tutorial-register-applications) :</span><span class="sxs-lookup"><span data-stu-id="271b3-115">Follow the guidance in [Tutorial: Register an application in Azure Active Directory B2C](/azure/active-directory-b2c/tutorial-register-applications) again to register an AAD app for the *Client app*:</span></span>

1. <span data-ttu-id="271b3-116">**Azure Active Directory**  >  **uygulama kayıtları** **Yeni kayıt**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="271b3-116">In **Azure Active Directory** > **App registrations**, select **New registration**.</span></span>
1. <span data-ttu-id="271b3-117">Uygulama için bir **ad** sağlayın (örneğin, \*\* Blazor tek başına AAD B2C\*\*).</span><span class="sxs-lookup"><span data-stu-id="271b3-117">Provide a **Name** for the app (for example, **Blazor Standalone AAD B2C**).</span></span>
1. <span data-ttu-id="271b3-118">**Desteklenen hesap türleri**için, birden çok kiracılı seçeneği seçin: **herhangi bir kuruluş dizinindeki hesaplar veya herhangi bir kimlik sağlayıcısı. Azure AD B2C kullanıcıları kimlik doğrulaması için.**</span><span class="sxs-lookup"><span data-stu-id="271b3-118">For **Supported account types**, select the multi-tenant option: **Accounts in any organizational directory or any identity provider. For authenticating users with Azure AD B2C.**</span></span>
1. <span data-ttu-id="271b3-119">**Yeniden yönlendirme URI 'si** açılan öğesini **Web**olarak ayarlayın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="271b3-119">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="271b3-120">Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir.</span><span class="sxs-lookup"><span data-stu-id="271b3-120">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="271b3-121">IIS Express için, rastgele oluşturulan bağlantı noktası, **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="271b3-121">For IIS Express, the randomly generated port can be found in the app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="271b3-122">**İzinlerin**  >  ,**OpenID 'ye yönetici tarafından verildiğini ve offline_access izinleri** olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="271b3-122">Confirm that **Permissions** > **Grant admin concent to openid and offline_access permissions** is enabled.</span></span>
1. <span data-ttu-id="271b3-123">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="271b3-123">Select **Register**.</span></span>

<span data-ttu-id="271b3-124">Uygulama KIMLIĞINI (Istemci KIMLIĞI) kaydedin (örneğin, `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="271b3-124">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="271b3-125">**Kimlik doğrulama**  >  **platformu yapılandırması**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="271b3-125">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="271b3-126">**Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="271b3-126">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="271b3-127">**Örtük izin**Için, **erişim belirteçleri** ve **Kimlik belirteçleri**onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="271b3-127">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="271b3-128">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="271b3-128">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="271b3-129">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="271b3-129">Select the **Save** button.</span></span>

<span data-ttu-id="271b3-130">**Giriş**  >  **Azure AD B2C**  >  **Kullanıcı akışları**' nda:</span><span class="sxs-lookup"><span data-stu-id="271b3-130">In **Home** > **Azure AD B2C** > **User flows**:</span></span>

[<span data-ttu-id="271b3-131">Kaydolma ve oturum açma Kullanıcı akışı oluşturma</span><span class="sxs-lookup"><span data-stu-id="271b3-131">Create a sign-up and sign-in user flow</span></span>](/azure/active-directory-b2c/tutorial-create-user-flows)

<span data-ttu-id="271b3-132">En azından, **Application claims**  >  **Display Name** `context.User.Identity.Name` `LoginDisplay` bileşende (*paylaşılan/logindisplay. Razor*) doldurmak için uygulama talepleri görünen adı Kullanıcı özniteliğini seçin.</span><span class="sxs-lookup"><span data-stu-id="271b3-132">At a minimum, select the **Application claims** > **Display Name** user attribute to populate the `context.User.Identity.Name` in the `LoginDisplay` component (*Shared/LoginDisplay.razor*).</span></span>

<span data-ttu-id="271b3-133">Uygulama için oluşturulan kaydolma ve oturum açma Kullanıcı akış adını kaydedin (örneğin, `B2C_1_signupsignin` ).</span><span class="sxs-lookup"><span data-stu-id="271b3-133">Record the sign-up and sign-in user flow name created for the app (for example, `B2C_1_signupsignin`).</span></span>

<span data-ttu-id="271b3-134">Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:</span><span class="sxs-lookup"><span data-stu-id="271b3-134">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
```

<span data-ttu-id="271b3-135">Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="271b3-135">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="271b3-136">Klasör adı Ayrıca projenin adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="271b3-136">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="271b3-137">Uygulamayı oluşturduktan sonra şunları yapmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="271b3-137">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="271b3-138">AAD Kullanıcı hesabını kullanarak uygulamada oturum açın.</span><span class="sxs-lookup"><span data-stu-id="271b3-138">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="271b3-139">Microsoft API 'Leri için erişim belirteçleri isteyin.</span><span class="sxs-lookup"><span data-stu-id="271b3-139">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="271b3-140">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="271b3-140">For more information, see:</span></span>
  * [<span data-ttu-id="271b3-141">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="271b3-141">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="271b3-142">[Hızlı başlangıç: Web API 'lerini kullanıma sunmak için bir uygulama yapılandırma](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="271b3-142">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="271b3-143">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="271b3-143">Authentication package</span></span>

<span data-ttu-id="271b3-144">Tek bir B2C hesabı () kullanmak üzere bir uygulama oluşturulduğunda `IndividualB2C` , uygulama otomatik olarak [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) () için bir paket başvurusu alır `Microsoft.Authentication.WebAssembly.Msal` .</span><span class="sxs-lookup"><span data-stu-id="271b3-144">When an app is created to use an Individual B2C Account (`IndividualB2C`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="271b3-145">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="271b3-145">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="271b3-146">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="271b3-146">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="271b3-147">`Microsoft.Authentication.WebAssembly.Msal`Paket geçişli `Microsoft.AspNetCore.Components.WebAssembly.Authentication` olarak uygulamayı uygulamaya ekler.</span><span class="sxs-lookup"><span data-stu-id="271b3-147">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="271b3-148">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="271b3-148">Authentication service support</span></span>

<span data-ttu-id="271b3-149">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında `AddMsalAuthentication` paket tarafından sağlanmış uzantı yöntemiyle kaydedilir `Microsoft.Authentication.WebAssembly.Msal` .</span><span class="sxs-lookup"><span data-stu-id="271b3-149">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="271b3-150">Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken tüm hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="271b3-150">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="271b3-151">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="271b3-151">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="271b3-152">`AddMsalAuthentication`Yöntemi, bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="271b3-152">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="271b3-153">Uygulamayı yapılandırmak için gereken değerler, uygulamayı kaydettiğinizde AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="271b3-153">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="271b3-154">Yapılandırma *Wwwroot/appSettings. JSON* dosyası tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="271b3-154">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

<span data-ttu-id="271b3-155">Örnek:</span><span class="sxs-lookup"><span data-stu-id="271b3-155">Example:</span></span>

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="271b3-156">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="271b3-156">Access token scopes</span></span>

<span data-ttu-id="271b3-157">BlazorWebassembly şablonu, uygulamayı güvenli BIR API için erişim belirteci isteyecek şekilde otomatik olarak yapılandırmaz.</span><span class="sxs-lookup"><span data-stu-id="271b3-157">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="271b3-158">Oturum açma akışının bir parçası olarak bir erişim belirteci sağlamak için, kapsamı varsayılan erişim belirteci kapsamlarına ekleyin `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="271b3-158">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="271b3-159">Daha fazla bilgi için *ek senaryolar* makalesinin aşağıdaki bölümlerine bakın:</span><span class="sxs-lookup"><span data-stu-id="271b3-159">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="271b3-160">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="271b3-160">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="271b3-161">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="271b3-161">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="271b3-162">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="271b3-162">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="271b3-163">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="271b3-163">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="271b3-164">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="271b3-164">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="271b3-165">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="271b3-165">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="271b3-166">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="271b3-166">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="271b3-167">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="271b3-167">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="271b3-168">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="271b3-168">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="271b3-169">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="271b3-169">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [<span data-ttu-id="271b3-170">Öğretici: Azure Active Directory B2C kiracısı oluşturma</span><span class="sxs-lookup"><span data-stu-id="271b3-170">Tutorial: Create an Azure Active Directory B2C tenant</span></span>](/azure/active-directory-b2c/tutorial-create-tenant)
* [<span data-ttu-id="271b3-171">Microsoft kimlik platformu belgeleri</span><span class="sxs-lookup"><span data-stu-id="271b3-171">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
