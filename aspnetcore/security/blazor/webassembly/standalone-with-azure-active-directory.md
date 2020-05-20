---
<span data-ttu-id="f997e-101">Başlık: ASP.NET Core ' Blazor Azure Active Directory ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="f997e-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="f997e-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="f997e-102">'Blazor'</span></span>
- <span data-ttu-id="f997e-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="f997e-103">'Identity'</span></span>
- <span data-ttu-id="f997e-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="f997e-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="f997e-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="f997e-105">'Razor'</span></span>
- <span data-ttu-id="f997e-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="f997e-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory"></a><span data-ttu-id="f997e-107">Azure Active Directory bir ASP.NET Core Blazor webassembly tek başına uygulamasının güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="f997e-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Azure Active Directory</span></span>

<span data-ttu-id="f997e-108">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="f997e-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f997e-109">BlazorKimlik doğrulaması için [Azure ACTIVE DIRECTORY (AAD)](https://azure.microsoft.com/services/active-directory/) kullanan bir webassembly tek başına uygulaması oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="f997e-109">To create a Blazor WebAssembly standalone app that uses [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) for authentication:</span></span>

<span data-ttu-id="f997e-110">[AAD kiracısı ve Web uygulaması oluşturma](/azure/active-directory/develop/v2-overview):</span><span class="sxs-lookup"><span data-stu-id="f997e-110">[Create an AAD tenant and web application](/azure/active-directory/develop/v2-overview):</span></span>

<span data-ttu-id="f997e-111">Azure Portal **Azure Active Directory**  >  **uygulama kayıtları** alanına bir AAD uygulaması kaydedin:</span><span class="sxs-lookup"><span data-stu-id="f997e-111">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="f997e-112">Uygulama için bir **ad** sağlayın (örneğin, \*\* Blazor tek başına AAD\*\*).</span><span class="sxs-lookup"><span data-stu-id="f997e-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD**).</span></span>
1. <span data-ttu-id="f997e-113">Desteklenen bir **Hesap türü**seçin.</span><span class="sxs-lookup"><span data-stu-id="f997e-113">Choose a **Supported account types**.</span></span> <span data-ttu-id="f997e-114">Bu **kuruluş dizininde yalnızca** bu deneyim için hesaplar seçebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="f997e-114">You may select **Accounts in this organizational directory only** for this experience.</span></span>
1. <span data-ttu-id="f997e-115">**Yeniden yönlendirme URI 'si** açılan öğesini **Web**olarak ayarlayın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="f997e-115">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="f997e-116">Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir.</span><span class="sxs-lookup"><span data-stu-id="f997e-116">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="f997e-117">IIS Express için, rastgele oluşturulan bağlantı noktası, **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="f997e-117">For IIS Express, the randomly generated port can be found in the app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="f997e-118">Yönetici tarafından **Permissions**  >  **OpenID ve offline_access izinleri için izin ver** onay kutusunu devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="f997e-118">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="f997e-119">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="f997e-119">Select **Register**.</span></span>

<span data-ttu-id="f997e-120">Aşağıdaki bilgileri kaydedin:</span><span class="sxs-lookup"><span data-stu-id="f997e-120">Record the following information:</span></span>

* <span data-ttu-id="f997e-121">Uygulama KIMLIĞI (Istemci KIMLIĞI) (örneğin, `11111111-1111-1111-1111-111111111111` )</span><span class="sxs-lookup"><span data-stu-id="f997e-121">Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`)</span></span>
* <span data-ttu-id="f997e-122">Dizin KIMLIĞI (kiracı KIMLIĞI) (örneğin, `22222222-2222-2222-2222-222222222222` )</span><span class="sxs-lookup"><span data-stu-id="f997e-122">Directory ID (Tenant ID) (for example, `22222222-2222-2222-2222-222222222222`)</span></span>

<span data-ttu-id="f997e-123">**Kimlik doğrulama**  >  **platformu yapılandırması**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="f997e-123">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="f997e-124">**Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="f997e-124">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="f997e-125">**Örtük izin**Için, **erişim belirteçleri** ve **Kimlik belirteçleri**onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="f997e-125">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="f997e-126">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="f997e-126">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="f997e-127">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="f997e-127">Select the **Save** button.</span></span>

<span data-ttu-id="f997e-128">Uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="f997e-128">Create the app.</span></span> <span data-ttu-id="f997e-129">Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:</span><span class="sxs-lookup"><span data-stu-id="f997e-129">Replace the placeholders in the following command with the information recorded earlier and execute the command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

<span data-ttu-id="f997e-130">Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="f997e-130">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="f997e-131">Klasör adı Ayrıca projenin adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="f997e-131">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="f997e-132">Uygulamayı oluşturduktan sonra şunları yapmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="f997e-132">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="f997e-133">AAD Kullanıcı hesabını kullanarak uygulamada oturum açın.</span><span class="sxs-lookup"><span data-stu-id="f997e-133">Log into the app using an AAD user account.</span></span>
* <span data-ttu-id="f997e-134">Microsoft API 'Leri için erişim belirteçleri isteyin.</span><span class="sxs-lookup"><span data-stu-id="f997e-134">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="f997e-135">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="f997e-135">For more information, see:</span></span>
  * [<span data-ttu-id="f997e-136">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="f997e-136">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="f997e-137">[Hızlı başlangıç: Web API 'lerini kullanıma sunmak için bir uygulama yapılandırma](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="f997e-137">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="f997e-138">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="f997e-138">Authentication package</span></span>

<span data-ttu-id="f997e-139">Iş veya okul hesaplarını () kullanmak üzere bir uygulama oluşturulduğunda `SingleOrg` , uygulama [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) () için bir paket başvurusu otomatik olarak alır `Microsoft.Authentication.WebAssembly.Msal` .</span><span class="sxs-lookup"><span data-stu-id="f997e-139">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="f997e-140">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="f997e-140">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="f997e-141">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f997e-141">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="f997e-142">`Microsoft.Authentication.WebAssembly.Msal`Paket geçişli `Microsoft.AspNetCore.Components.WebAssembly.Authentication` olarak uygulamayı uygulamaya ekler.</span><span class="sxs-lookup"><span data-stu-id="f997e-142">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="f997e-143">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="f997e-143">Authentication service support</span></span>

<span data-ttu-id="f997e-144">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında `AddMsalAuthentication` paket tarafından sağlanmış uzantı yöntemiyle kaydedilir `Microsoft.Authentication.WebAssembly.Msal` .</span><span class="sxs-lookup"><span data-stu-id="f997e-144">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="f997e-145">Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f997e-145">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="f997e-146">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="f997e-146">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="f997e-147">`AddMsalAuthentication`Yöntemi, bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="f997e-147">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="f997e-148">Uygulamayı yapılandırmak için gereken değerler, uygulamayı kaydettiğinizde AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="f997e-148">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="f997e-149">Yapılandırma *Wwwroot/appSettings. JSON* dosyası tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="f997e-149">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="f997e-150">Örnek:</span><span class="sxs-lookup"><span data-stu-id="f997e-150">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="f997e-151">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="f997e-151">Access token scopes</span></span>

<span data-ttu-id="f997e-152">BlazorWebassembly şablonu, uygulamayı güvenli BIR API için erişim belirteci isteyecek şekilde otomatik olarak yapılandırmaz.</span><span class="sxs-lookup"><span data-stu-id="f997e-152">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="f997e-153">Oturum açma akışının bir parçası olarak bir erişim belirteci sağlamak için, kapsamı varsayılan erişim belirteci kapsamlarına ekleyin `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="f997e-153">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="f997e-154">Daha fazla bilgi için *ek senaryolar* makalesinin aşağıdaki bölümlerine bakın:</span><span class="sxs-lookup"><span data-stu-id="f997e-154">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="f997e-155">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="f997e-155">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="f997e-156">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="f997e-156">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="f997e-157">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="f997e-157">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="f997e-158">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="f997e-158">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="f997e-159">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="f997e-159">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="f997e-160">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="f997e-160">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="f997e-161">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="f997e-161">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="f997e-162">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="f997e-162">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="f997e-163">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f997e-163">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="f997e-164">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="f997e-164">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [<span data-ttu-id="f997e-165">Microsoft kimlik platformu belgeleri</span><span class="sxs-lookup"><span data-stu-id="f997e-165">Microsoft identity platform documentation</span></span>](/azure/active-directory/develop/)
