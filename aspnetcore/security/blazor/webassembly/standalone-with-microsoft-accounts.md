---
<span data-ttu-id="e9e35-101">Başlık: ' Blazor Microsoft hesaplarıyla birlikte ASP.NET Core webassembly tek başına uygulamasını güvenli Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="e9e35-101">title: 'Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts' author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="e9e35-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="e9e35-102">'Blazor'</span></span>
- <span data-ttu-id="e9e35-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="e9e35-103">'Identity'</span></span>
- <span data-ttu-id="e9e35-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="e9e35-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="e9e35-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="e9e35-105">'Razor'</span></span>
- <span data-ttu-id="e9e35-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="e9e35-106">'SignalR' uid:</span></span> 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a><span data-ttu-id="e9e35-107">BlazorMicrosoft hesaplarıyla ASP.NET Core webassembly tek başına uygulamasının güvenliğini sağlama</span><span class="sxs-lookup"><span data-stu-id="e9e35-107">Secure an ASP.NET Core Blazor WebAssembly standalone app with Microsoft Accounts</span></span>

<span data-ttu-id="e9e35-108">, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre</span><span class="sxs-lookup"><span data-stu-id="e9e35-108">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="e9e35-109">BlazorKimlik doğrulaması için [Azure ACTIVE DIRECTORY (AAD) Ile Microsoft hesapları](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) kullanan bir webassembly tek başına uygulaması oluşturmak için:</span><span class="sxs-lookup"><span data-stu-id="e9e35-109">To create a Blazor WebAssembly standalone app that uses [Microsoft Accounts with Azure Active Directory (AAD)](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) for authentication:</span></span>

[<span data-ttu-id="e9e35-110">AAD kiracısı ve Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="e9e35-110">Create an AAD tenant and web application</span></span>](/azure/active-directory/develop/v2-overview)

<span data-ttu-id="e9e35-111">Azure Portal **Azure Active Directory**  >  **uygulama kayıtları** alanına bir AAD uygulaması kaydedin:</span><span class="sxs-lookup"><span data-stu-id="e9e35-111">Register a AAD app in the **Azure Active Directory** > **App registrations** area of the Azure portal:</span></span>

1. <span data-ttu-id="e9e35-112">Uygulama için bir **ad** sağlayın (örneğin, \*\* Blazor tek başına AAD Microsoft hesapları\*\*).</span><span class="sxs-lookup"><span data-stu-id="e9e35-112">Provide a **Name** for the app (for example, **Blazor Standalone AAD Microsoft Accounts**).</span></span>
1. <span data-ttu-id="e9e35-113">**Desteklenen hesap türleri**' nde, **herhangi bir kuruluş dizininde hesaplar**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="e9e35-113">In **Supported account types**, select **Accounts in any organizational directory**.</span></span>
1. <span data-ttu-id="e9e35-114">**Yeniden yönlendirme URI 'si** açılan öğesini **Web** 'e ayarlı bırakın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` .</span><span class="sxs-lookup"><span data-stu-id="e9e35-114">Leave the **Redirect URI** drop down set to **Web** and provide the following redirect URI: `https://localhost:{PORT}/authentication/login-callback`.</span></span> <span data-ttu-id="e9e35-115">Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir.</span><span class="sxs-lookup"><span data-stu-id="e9e35-115">The default port for an app running on Kestrel is 5001.</span></span> <span data-ttu-id="e9e35-116">Uygulama farklı bir Kestrel bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktasını kullanın.</span><span class="sxs-lookup"><span data-stu-id="e9e35-116">If the app is run on a different Kestrel port, use the app's port.</span></span> <span data-ttu-id="e9e35-117">IIS Express için, uygulama için rastgele oluşturulan bağlantı noktası, **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="e9e35-117">For IIS Express, the randomly generated port for the app can be found in the app's properties in the **Debug** panel.</span></span> <span data-ttu-id="e9e35-118">Uygulama bu noktada mevcut olmadığından ve IIS Express bağlantı noktası bilinmediğinden, uygulama oluşturulduktan sonra bu adıma geri dönün ve yeniden yönlendirme URI 'sini güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="e9e35-118">Since the app doesn't exist at this point and the IIS Express port isn't known, return to this step after the app is created and update the redirect URI.</span></span> <span data-ttu-id="e9e35-119">Bu konunun ilerleyen kısımlarında bir açıklama görüntülenerek IIS Express kullanıcıların yeniden yönlendirme URI 'sini güncelleştirmesini hatırlatır.</span><span class="sxs-lookup"><span data-stu-id="e9e35-119">A remark appears later in this topic to remind IIS Express users to update the redirect URI.</span></span>
1. <span data-ttu-id="e9e35-120">Yönetici tarafından **Permissions**  >  **OpenID ve offline_access izinleri için izin ver** onay kutusunu devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="e9e35-120">Disable the **Permissions** > **Grant admin concent to openid and offline_access permissions** check box.</span></span>
1. <span data-ttu-id="e9e35-121">**Kaydol**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="e9e35-121">Select **Register**.</span></span>

<span data-ttu-id="e9e35-122">Uygulama KIMLIĞINI (Istemci KIMLIĞI) kaydedin (örneğin, `11111111-1111-1111-1111-111111111111` ).</span><span class="sxs-lookup"><span data-stu-id="e9e35-122">Record the Application ID (Client ID) (for example, `11111111-1111-1111-1111-111111111111`).</span></span>

<span data-ttu-id="e9e35-123">**Kimlik doğrulama**  >  **platformu yapılandırması**  >  **Web**:</span><span class="sxs-lookup"><span data-stu-id="e9e35-123">In **Authentication** > **Platform configurations** > **Web**:</span></span>

1. <span data-ttu-id="e9e35-124">**Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.</span><span class="sxs-lookup"><span data-stu-id="e9e35-124">Confirm the **Redirect URI** of `https://localhost:{PORT}/authentication/login-callback` is present.</span></span>
1. <span data-ttu-id="e9e35-125">**Örtük izin**Için, **erişim belirteçleri** ve **Kimlik belirteçleri**onay kutularını seçin.</span><span class="sxs-lookup"><span data-stu-id="e9e35-125">For **Implicit grant**, select the check boxes for **Access tokens** and **ID tokens**.</span></span>
1. <span data-ttu-id="e9e35-126">Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.</span><span class="sxs-lookup"><span data-stu-id="e9e35-126">The remaining defaults for the app are acceptable for this experience.</span></span>
1. <span data-ttu-id="e9e35-127">**Kaydet** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="e9e35-127">Select the **Save** button.</span></span>

<span data-ttu-id="e9e35-128">Uygulamayı oluşturun.</span><span class="sxs-lookup"><span data-stu-id="e9e35-128">Create the app.</span></span> <span data-ttu-id="e9e35-129">Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komut kabuğu 'nda aşağıdaki komutu yürütün:</span><span class="sxs-lookup"><span data-stu-id="e9e35-129">Replace the placeholders in the following command with the information recorded earlier and execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
```

<span data-ttu-id="e9e35-130">Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample` ).</span><span class="sxs-lookup"><span data-stu-id="e9e35-130">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="e9e35-131">Klasör adı Ayrıca projenin adının bir parçası haline gelir.</span><span class="sxs-lookup"><span data-stu-id="e9e35-131">The folder name also becomes part of the project's name.</span></span>

> [!NOTE]
> <span data-ttu-id="e9e35-132">Azure Portal, uygulamanın **kimlik doğrulama**  >  **platformu yapılandırması**  >  **Web**  >  **yeniden yönlendirme URI 'si** , Kestrel sunucusunda varsayılan ayarlarla çalışan uygulamalar için bağlantı noktası 5001 için yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="e9e35-132">In the Azure portal, the app's **Authentication** > **Platform configurations** > **Web** > **Redirect URI** is configured for port 5001 for apps that run on the Kestrel server with default settings.</span></span>
>
> <span data-ttu-id="e9e35-133">Uygulama rastgele bir IIS Express bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktası **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="e9e35-133">If the app is run on a random IIS Express port, the port for the app can be found in the app's properties in the **Debug** panel.</span></span>
>
> <span data-ttu-id="e9e35-134">Bağlantı noktası, uygulamanın bilinen bağlantı noktasıyla daha önce yapılandırılmamışsa, Azure portal uygulamanın kaydına dönün ve yeniden yönlendirme URI 'sini doğru bağlantı noktasıyla güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="e9e35-134">If the port wasn't configured earlier with the app's known port, return to the app's registration in the Azure portal and update the redirect URI with the correct port.</span></span>

<span data-ttu-id="e9e35-135">Uygulamayı oluşturduktan sonra şunları yapmanız gerekir:</span><span class="sxs-lookup"><span data-stu-id="e9e35-135">After creating the app, you should be able to:</span></span>

* <span data-ttu-id="e9e35-136">Microsoft hesabı kullanarak uygulamada oturum açın.</span><span class="sxs-lookup"><span data-stu-id="e9e35-136">Log into the app using a Microsoft account.</span></span>
* <span data-ttu-id="e9e35-137">Microsoft API 'Leri için erişim belirteçleri isteyin.</span><span class="sxs-lookup"><span data-stu-id="e9e35-137">Request access tokens for Microsoft APIs.</span></span> <span data-ttu-id="e9e35-138">Daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="e9e35-138">For more information, see:</span></span>
  * [<span data-ttu-id="e9e35-139">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="e9e35-139">Access token scopes</span></span>](#access-token-scopes)
  * <span data-ttu-id="e9e35-140">[Hızlı başlangıç: Web API 'lerini kullanıma sunmak için bir uygulama yapılandırma](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span><span class="sxs-lookup"><span data-stu-id="e9e35-140">[Quickstart: Configure an application to expose web APIs](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).</span></span>

## <a name="authentication-package"></a><span data-ttu-id="e9e35-141">Kimlik doğrulama paketi</span><span class="sxs-lookup"><span data-stu-id="e9e35-141">Authentication package</span></span>

<span data-ttu-id="e9e35-142">Iş veya okul hesaplarını () kullanmak üzere bir uygulama oluşturulduğunda `SingleOrg` , uygulama [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) () için bir paket başvurusu otomatik olarak alır `Microsoft.Authentication.WebAssembly.Msal` .</span><span class="sxs-lookup"><span data-stu-id="e9e35-142">When an app is created to use Work or School Accounts (`SingleOrg`), the app automatically receives a package reference for the [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`).</span></span> <span data-ttu-id="e9e35-143">Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.</span><span class="sxs-lookup"><span data-stu-id="e9e35-143">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="e9e35-144">Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e9e35-144">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

<span data-ttu-id="e9e35-145">`Microsoft.Authentication.WebAssembly.Msal`Paket geçişli `Microsoft.AspNetCore.Components.WebAssembly.Authentication` olarak uygulamayı uygulamaya ekler.</span><span class="sxs-lookup"><span data-stu-id="e9e35-145">The `Microsoft.Authentication.WebAssembly.Msal` package transitively adds the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` package to the app.</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="e9e35-146">Kimlik doğrulama hizmeti desteği</span><span class="sxs-lookup"><span data-stu-id="e9e35-146">Authentication service support</span></span>

<span data-ttu-id="e9e35-147">Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında `AddMsalAuthentication` paket tarafından sağlanmış uzantı yöntemiyle kaydedilir `Microsoft.Authentication.WebAssembly.Msal` .</span><span class="sxs-lookup"><span data-stu-id="e9e35-147">Support for authenticating users is registered in the service container with the `AddMsalAuthentication` extension method provided by the `Microsoft.Authentication.WebAssembly.Msal` package.</span></span> <span data-ttu-id="e9e35-148">Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken tüm hizmetleri ayarlar.</span><span class="sxs-lookup"><span data-stu-id="e9e35-148">This method sets up all of the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="e9e35-149">*Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="e9e35-149">*Program.cs*:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<span data-ttu-id="e9e35-150">`AddMsalAuthentication`Yöntemi, bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="e9e35-150">The `AddMsalAuthentication` method accepts a callback to configure the parameters required to authenticate an app.</span></span> <span data-ttu-id="e9e35-151">Uygulamayı yapılandırmak için gereken değerler, uygulamayı kaydettiğinizde AAD yapılandırmasından elde edilebilir.</span><span class="sxs-lookup"><span data-stu-id="e9e35-151">The values required for configuring the app can be obtained from the AAD configuration when you register the app.</span></span>

<span data-ttu-id="e9e35-152">Yapılandırma *Wwwroot/appSettings. JSON* dosyası tarafından sağlanır:</span><span class="sxs-lookup"><span data-stu-id="e9e35-152">Configuration is supplied by the *wwwroot/appsettings.json* file:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

<span data-ttu-id="e9e35-153">Örnek:</span><span class="sxs-lookup"><span data-stu-id="e9e35-153">Example:</span></span>

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a><span data-ttu-id="e9e35-154">Erişim belirteci kapsamları</span><span class="sxs-lookup"><span data-stu-id="e9e35-154">Access token scopes</span></span>

<span data-ttu-id="e9e35-155">BlazorWebassembly şablonu, uygulamayı güvenli BIR API için erişim belirteci isteyecek şekilde otomatik olarak yapılandırmaz.</span><span class="sxs-lookup"><span data-stu-id="e9e35-155">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="e9e35-156">Oturum açma akışının bir parçası olarak bir erişim belirteci sağlamak için, kapsamı varsayılan erişim belirteci kapsamlarına ekleyin `MsalProviderOptions` :</span><span class="sxs-lookup"><span data-stu-id="e9e35-156">To provision an access token as part of the sign-in flow, add the scope to the default access token scopes of the `MsalProviderOptions`:</span></span>

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="e9e35-157">Daha fazla bilgi için *ek senaryolar* makalesinin aşağıdaki bölümlerine bakın:</span><span class="sxs-lookup"><span data-stu-id="e9e35-157">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="e9e35-158">Ek erişim belirteçleri isteyin</span><span class="sxs-lookup"><span data-stu-id="e9e35-158">Request additional access tokens</span></span>](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="e9e35-159">Giden isteklere belirteç iliştirme</span><span class="sxs-lookup"><span data-stu-id="e9e35-159">Attach tokens to outgoing requests</span></span>](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="e9e35-160">Dosya içeri aktarmalar</span><span class="sxs-lookup"><span data-stu-id="e9e35-160">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="e9e35-161">Dizin sayfası</span><span class="sxs-lookup"><span data-stu-id="e9e35-161">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a><span data-ttu-id="e9e35-162">Uygulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="e9e35-162">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="e9e35-163">RedirectToLogin bileşeni</span><span class="sxs-lookup"><span data-stu-id="e9e35-163">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="e9e35-164">LoginDisplay bileşeni</span><span class="sxs-lookup"><span data-stu-id="e9e35-164">LoginDisplay component</span></span>

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a><span data-ttu-id="e9e35-165">Kimlik doğrulama bileşeni</span><span class="sxs-lookup"><span data-stu-id="e9e35-165">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="e9e35-166">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e9e35-166">Additional resources</span></span>

* <xref:security/blazor/webassembly/additional-scenarios>
* [<span data-ttu-id="e9e35-167">Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri</span><span class="sxs-lookup"><span data-stu-id="e9e35-167">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* [<span data-ttu-id="e9e35-168">Hızlı başlangıç: Microsoft Identity platformu ile uygulama kaydetme</span><span class="sxs-lookup"><span data-stu-id="e9e35-168">Quickstart: Register an application with the Microsoft identity platform</span></span>](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [<span data-ttu-id="e9e35-169">Hızlı başlangıç: Web API 'Lerini kullanıma sunmak için uygulama yapılandırma</span><span class="sxs-lookup"><span data-stu-id="e9e35-169">Quickstart: Configure an application to expose web APIs</span></span>](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
