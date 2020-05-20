---
<span data-ttu-id="857aa-101">Başlık: ' Blazor Microsoft hesaplarıyla birlikte ASP.NET Core webassembly tek başına uygulamasını güvenli Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="857aa-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="857aa-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="857aa-102">'Blazor'</span></span>
- <span data-ttu-id="857aa-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="857aa-103">'Identity'</span></span>
- <span data-ttu-id="857aa-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="857aa-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="857aa-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="857aa-105">'Razor'</span></span>
- <span data-ttu-id="857aa-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="857aa-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="857aa-107">BlazorMicrosoft hesaplarıyla ASP.NET Core webassembly tek başına uygulamasının güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="857aa-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="857aa-108">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="857aa-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="857aa-109">BlazorKimlik doğrulaması için [Azure ACTIVE DIRECTORY (AAD) Ile Microsoft hesapları](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) kullanan bir webassembly tek başına uygulaması oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="857aa-109">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

[<span data-ttu-id="857aa-110">AAD kiracısı ve Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="857aa-110">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

<span data-ttu-id="857aa-111">Azure Portal **Azure Active Directory**  >  **uygulama kayıtları** alanına bir AAD uygulaması kaydedin:</span><span class="sxs-lookup"><span data-stu-id="857aa-111">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="857aa-112">Uygulama için bir **ad** sağlayın (örneğin, \*\* Blazor tek başına AAD Microsoft hesapları\*\*).</span><span class="sxs-lookup"><span data-stu-id="857aa-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="857aa-113">**Desteklenen hesap türleri**' nde, **herhangi bir kuruluş dizininde hesaplar**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="857aa-113">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="857aa-114">**Yeniden yönlendirme URI 'si** açılan öğesini **Web**olarak ayarlayın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="857aa-114">Leave the **Redirect URI** drop down set to **Web**, and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="857aa-115">Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir.</span><span class="sxs-lookup"><span data-stu-id="857aa-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="857aa-116">IIS Express için, rastgele oluşturulan bağlantı noktası, **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="857aa-116">For IIS Express, the randomly generated port can be found in the app's properties in the **Debug** panel.</span></span>
1. <span data-ttu-id="857aa-117">Yönetici tarafından **Permissions**  >  **OpenID ve offline_access izinleri için izin ver** onay kutusunu devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="857aa-117">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="857aa-118">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="857aa-118">Select **Register**.</span></span>

<span data-ttu-id="857aa-119">Uygulama KIMLIĞINI (Istemci KIMLIĞI) kaydedin (örneğin, `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="857aa-119">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="857aa-120">**Kimlik doğrulama**  >  **platformu yapılandırması**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="857aa-120">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="857aa-121">**Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="857aa-121">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="857aa-122">**Örtük izin**Için, **erişim belirteçleri** ve **Kimlik belirteçleri**onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="857aa-122">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="857aa-123">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="857aa-123">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="857aa-124">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="857aa-124">Select the **Save** button.</span></span>

<span data-ttu-id="857aa-125">Uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="857aa-125">Create the app.</span></span> <span data-ttu-id="857aa-126">Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komut kabuğu 'nda aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="857aa-126">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
```

<span data-ttu-id="857aa-127">Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="857aa-127">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="857aa-128">Klasör adı Ayrıca projenin adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="857aa-128">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="857aa-129">Uygulamayı oluşturduktan sonra şunları yapmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="857aa-129">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="857aa-130">Microsoft hesabı kullanarak uygulamada oturum açın.</span><span class="sxs-lookup"><span data-stu-id="857aa-130">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="857aa-131">Microsoft API 'Leri için erişim belirteçleri isteyin.</span><span class="sxs-lookup"><span data-stu-id="857aa-131">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="857aa-132">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="857aa-132">For more information, see:</span></span>
  * [<span data-ttu-id="857aa-133">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="857aa-133">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="857aa-134">[Hızlı başlangıç: Web API 'lerini kullanıma sunmak için bir uygulama yapılandırma](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="857aa-134">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="857aa-135">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="857aa-135">Authentication package</span></span>

<span data-ttu-id="857aa-136">Iş veya okul hesaplarını () kullanmak üzere bir uygulama oluşturulduğunda `SingleOrg` , uygulama [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) () için bir paket başvurusu otomatik olarak alır `Microsoft.Authentication.WebAssembly.Msal` .</span><span class="sxs-lookup"><span data-stu-id="857aa-136">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="857aa-137">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="857aa-137">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="857aa-138">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="857aa-138">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="857aa-139">`Microsoft.Authentication.WebAssembly.Msal`Paket geçişli `Microsoft.AspNetCore.Components.WebAssembly.Authentication` olarak uygulamayı uygulamaya ekler.</span><span class="sxs-lookup"><span data-stu-id="857aa-139">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="857aa-140">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="857aa-140">Authentication service support</span></span>

<span data-ttu-id="857aa-141">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında `AddMsalAuthentication` paket tarafından sağlanmış uzantı yöntemiyle kaydedilir `Microsoft.Authentication.WebAssembly.Msal` .</span><span class="sxs-lookup"><span data-stu-id="857aa-141">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="857aa-142">Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken tüm hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="857aa-142">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="857aa-143">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="857aa-143">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="857aa-144">`AddMsalAuthentication`Yöntemi, bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="857aa-144">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="857aa-145">Uygulamayı yapılandırmak için gereken değerler, uygulamayı kaydettiğinizde AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="857aa-145">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="857aa-146">Yapılandırma *Wwwroot/appSettings. JSON* dosyası tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="857aa-146">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="857aa-147">Örnek:</span><span class="sxs-lookup"><span data-stu-id="857aa-147">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="857aa-148">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="857aa-148">Access token scopes</span></span>

<span data-ttu-id="857aa-149">BlazorWebassembly şablonu, uygulamayı güvenli BIR API için erişim belirteci isteyecek şekilde otomatik olarak yapılandırmaz.</span><span class="sxs-lookup"><span data-stu-id="857aa-149">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="857aa-150">Oturum açma akışının bir parçası olarak bir erişim belirteci sağlamak için, kapsamı varsayılan erişim belirteci kapsamlarına ekleyin `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="857aa-150">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="857aa-151">Daha fazla bilgi için *ek senaryolar* makalesinin aşağıdaki bölümlerine bakın:</span><span class="sxs-lookup"><span data-stu-id="857aa-151">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="857aa-152">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="857aa-152">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="857aa-153">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="857aa-153">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="857aa-154">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="857aa-154">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="857aa-155">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="857aa-155">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="857aa-156">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="857aa-156">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="857aa-157">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="857aa-157">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="857aa-158">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="857aa-158">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="857aa-159">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="857aa-159">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="857aa-160">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="857aa-160">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="857aa-161">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="857aa-161">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* [<span data-ttu-id="857aa-162">Hızlı başlangıç: Microsoft Identity platformu ile uygulama kaydetme</span><span class="sxs-lookup"><span data-stu-id="857aa-162">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="857aa-163">Hızlı başlangıç: Web API 'Lerini kullanıma sunmak için uygulama yapılandırma</span><span class="sxs-lookup"><span data-stu-id="857aa-163">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
