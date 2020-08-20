---
title: ASP.NET Core 'da Facebook dış oturum açma kurulumu
author: rick-anderson
description: Facebook hesabı kullanıcı kimlik doğrulamasının mevcut bir ASP.NET Core uygulamasına tümleştirilmesini gösteren kod örnekleri ile öğretici.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
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
uid: security/authentication/facebook-logins
ms.openlocfilehash: ce0e7ad30c137562b74dc9fe5c53235e3599e575
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88634364"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="4cddd-103">ASP.NET Core 'da Facebook dış oturum açma kurulumu</span><span class="sxs-lookup"><span data-stu-id="4cddd-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="4cddd-104">Tarafından [Valeriy Novyıtskyy](https://github.com/01binary) ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4cddd-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

<span data-ttu-id="4cddd-105">Kod örnekleri ile bu öğreticide, kullanıcılarınızın [önceki sayfada](xref:security/authentication/social/index)oluşturulmuş bir örnek ASP.NET Core 3,0 projesi kullanarak Facebook hesabıyla oturum açmasını nasıl etkinleştireceğinizi gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="4cddd-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="4cddd-106">[Resmi adımları](https://developers.facebook.com)Izleyerek Facebook uygulama kimliği oluşturarak başladık.</span><span class="sxs-lookup"><span data-stu-id="4cddd-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="4cddd-107">Facebook 'ta uygulama oluşturma</span><span class="sxs-lookup"><span data-stu-id="4cddd-107">Create the app in Facebook</span></span>

* <span data-ttu-id="4cddd-108">Projeye [Microsoft. AspNetCore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4cddd-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="4cddd-109">[Facebook geliştiricileri uygulama](https://developers.facebook.com/apps/) sayfasına gidin ve oturum açın.</span><span class="sxs-lookup"><span data-stu-id="4cddd-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="4cddd-110">Henüz bir Facebook hesabınız yoksa, bir tane oluşturmak için oturum açma sayfasındaki **Facebook 'A kaydolun** bağlantısını kullanın.</span><span class="sxs-lookup"><span data-stu-id="4cddd-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="4cddd-111">Facebook hesabınız olduğunda, Facebook geliştiricisi olarak kaydolmak için yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="4cddd-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="4cddd-112">Yeni bir uygulama KIMLIĞI oluşturmak için **uygulamalarım** menüsünde **uygulama oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="4cddd-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Microsoft Edge 'de açık geliştiriciler portalı için Facebook](index/_static/FBMyApps.png)

* <span data-ttu-id="4cddd-114">Formu doldurun ve **uygulama kimliği oluştur** düğmesine dokunun.</span><span class="sxs-lookup"><span data-stu-id="4cddd-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Yeni bir uygulama KIMLIĞI formu oluşturun](index/_static/FBNewAppId.png)

* <span data-ttu-id="4cddd-116">Yeni uygulama kartında **Ürün Ekle**' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="4cddd-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="4cddd-117">**Facebook oturum açma** kartında **Ayarla** ' ya tıklayın.</span><span class="sxs-lookup"><span data-stu-id="4cddd-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![Ürün kurulum sayfası](index/_static/FBProductSetup.png)

* <span data-ttu-id="4cddd-119">**Hızlı başlangıç** Sihirbazı, ilk sayfa olarak **bir platform Seç** ile başlatılır.</span><span class="sxs-lookup"><span data-stu-id="4cddd-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="4cddd-120">Sol alt taraftaki menüdeki **Facebook oturum açma** **ayarları** bağlantısına tıklayarak Sihirbazı Şimdi atlayın:</span><span class="sxs-lookup"><span data-stu-id="4cddd-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![hızlı başlangıç atla](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="4cddd-122">**Istemci OAuth ayarları** sayfası görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="4cddd-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![İstemci OAuth ayarları sayfası](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="4cddd-124">**Geçerli OAuth yeniden yönlendirme URI 'leri** alanına */SignIn-Facebook* eklenmiş olan geliştirme URI 'nizi girin (örneğin: `https://localhost:44320/signin-facebook` ).</span><span class="sxs-lookup"><span data-stu-id="4cddd-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="4cddd-125">Bu öğreticide daha sonra yapılandırılan Facebook kimlik doğrulaması, OAuth akışını uygulamak için */SignIn-Facebook* rotasındaki istekleri otomatik olarak işleymeyecektir.</span><span class="sxs-lookup"><span data-stu-id="4cddd-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="4cddd-126">URI */SignIn-Facebook* , Facebook kimlik doğrulama sağlayıcısı 'nın varsayılan geri çağırması olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="4cddd-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="4cddd-127">Facebook kimlik doğrulama ara yazılımını, çok [yönlü önyükleme](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) sınıfının devralınan [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) özelliği aracılığıyla YAPıLANDıRıRKEN varsayılan geri çağırma URI 'sini değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4cddd-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="4cddd-128">**Değişiklikleri Kaydet**’e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="4cddd-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="4cddd-129">**Settings**  >  Sol gezinti bölmesinde ayarlar**temel** bağlantısı ' na tıklayın.</span><span class="sxs-lookup"><span data-stu-id="4cddd-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="4cddd-130">Bu sayfada, ve bilgilerinizi bir yere unutmayın `App ID` `App Secret` .</span><span class="sxs-lookup"><span data-stu-id="4cddd-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="4cddd-131">Bir sonraki bölümde ASP.NET Core uygulamanıza her ikisini de ekleyeceksiniz:</span><span class="sxs-lookup"><span data-stu-id="4cddd-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="4cddd-132">Siteyi dağıttığınızda **Facebook oturum açma** kurulumu sayfasını yeniden ziyaret etmeniz ve yeni BIR ortak URI kaydetmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="4cddd-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="4cddd-133">Facebook uygulama KIMLIĞI ve gizli anahtarını depolayın</span><span class="sxs-lookup"><span data-stu-id="4cddd-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="4cddd-134">Facebook uygulama KIMLIĞI ve gizli anahtar değerleri gibi hassas ayarları [gizli bir yöneticiye](xref:security/app-secrets)depolayın.</span><span class="sxs-lookup"><span data-stu-id="4cddd-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="4cddd-135">Bu örnek için aşağıdaki adımları kullanın:</span><span class="sxs-lookup"><span data-stu-id="4cddd-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="4cddd-136">[Gizli depolamayı etkinleştirme](xref:security/app-secrets#enable-secret-storage)konusundaki yönergeler temelinde projeyi gizli depolama için başlatın.</span><span class="sxs-lookup"><span data-stu-id="4cddd-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="4cddd-137">Gizli anahtar ayarlarını gizli anahtarlar ve gizli anahtarlar ile yerel gizli dizi deposunda `Authentication:Facebook:AppId` depolayın `Authentication:Facebook:AppSecret` :</span><span class="sxs-lookup"><span data-stu-id="4cddd-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="4cddd-138">Facebook kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4cddd-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="4cddd-139">`ConfigureServices` *Startup.cs* dosyasındaki yönteme Facebook hizmetini ekleyin:</span><span class="sxs-lookup"><span data-stu-id="4cddd-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a><span data-ttu-id="4cddd-140">Facebook ile oturum açma</span><span class="sxs-lookup"><span data-stu-id="4cddd-140">Sign in with Facebook</span></span>

* <span data-ttu-id="4cddd-141">Uygulamayı çalıştırın ve **oturum aç '** ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4cddd-141">Run the app and select **Log in**.</span></span> 
* <span data-ttu-id="4cddd-142">**Oturum açmak için başka bir hizmet kullan**' ın altında Facebook ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4cddd-142">Under **Use another service to log in.**, select Facebook.</span></span>
* <span data-ttu-id="4cddd-143">Kimlik doğrulaması için **Facebook** 'a yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4cddd-143">You are redirected to **Facebook** for authentication.</span></span>
* <span data-ttu-id="4cddd-144">Facebook kimlik bilgilerinizi girin.</span><span class="sxs-lookup"><span data-stu-id="4cddd-144">Enter your Facebook credentials.</span></span>
* <span data-ttu-id="4cddd-145">Sitenize geri yönlendirilirsiniz ve e-postanızı ayarlayabiliyorsunuz.</span><span class="sxs-lookup"><span data-stu-id="4cddd-145">You are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="4cddd-146">Artık Facebook kimlik bilgilerinizi kullanarak oturumunuz açıldı:</span><span class="sxs-lookup"><span data-stu-id="4cddd-146">You are now logged in using your Facebook credentials:</span></span>

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a><span data-ttu-id="4cddd-147">Yetkilendirmeyi dış oturum açma işlemini iptal etme tepki verme</span><span class="sxs-lookup"><span data-stu-id="4cddd-147">React to cancel authorize external sign-in</span></span>

<span data-ttu-id="4cddd-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> Kullanıcı İstenen Yetkilendirme talebini onaylamazsa kullanıcı aracısına yeniden yönlendirme yolu sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="4cddd-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> can provide a redirect path to the user agent when the user doesn't approve the requested authorization demand.</span></span>

<span data-ttu-id="4cddd-149">Aşağıdaki kod öğesini olarak ayarlar `AccessDeniedPath` `"/AccessDeniedPathInfo"` :</span><span class="sxs-lookup"><span data-stu-id="4cddd-149">The following code sets the `AccessDeniedPath` to `"/AccessDeniedPathInfo"`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

<span data-ttu-id="4cddd-150">`AccessDeniedPath`Sayfada aşağıdaki bilgileri içermesi önerilir:</span><span class="sxs-lookup"><span data-stu-id="4cddd-150">We recommend the `AccessDeniedPath` page contain the following information:</span></span>

*  <span data-ttu-id="4cddd-151">Uzaktan kimlik doğrulama iptal edildi.</span><span class="sxs-lookup"><span data-stu-id="4cddd-151">Remote authentication was canceled.</span></span>
* <span data-ttu-id="4cddd-152">Bu uygulama kimlik doğrulaması gerektiriyor.</span><span class="sxs-lookup"><span data-stu-id="4cddd-152">This app requires authentication.</span></span>
* <span data-ttu-id="4cddd-153">Yeniden oturum açmayı denemek için oturum açma bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="4cddd-153">To try sign-in again, select the Login link.</span></span>

### <a name="test-accessdeniedpath"></a><span data-ttu-id="4cddd-154">Test AccessDeniedPath</span><span class="sxs-lookup"><span data-stu-id="4cddd-154">Test AccessDeniedPath</span></span>

* <span data-ttu-id="4cddd-155">[Facebook.com](https://www.facebook.com/) adresine gidin</span><span class="sxs-lookup"><span data-stu-id="4cddd-155">Navigate to [facebook.com](https://www.facebook.com/)</span></span>
* <span data-ttu-id="4cddd-156">Oturum açtıysanız oturumunuzu kapatmalısınız.</span><span class="sxs-lookup"><span data-stu-id="4cddd-156">If you are signed in, you must sign out.</span></span>
* <span data-ttu-id="4cddd-157">Uygulamayı çalıştırın ve Facebook oturum açma ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="4cddd-157">Run the app and select Facebook sign-in.</span></span>
* <span data-ttu-id="4cddd-158">**Şimdi değil**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="4cddd-158">Select **Not now**.</span></span> <span data-ttu-id="4cddd-159">Belirtilen `AccessDeniedPath` sayfaya yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4cddd-159">You are redirected to the specified `AccessDeniedPath` page.</span></span>

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="4cddd-160">Facebook kimlik doğrulaması tarafından desteklenen yapılandırma seçenekleri hakkında daha fazla bilgi için bkz. çok [yönlü Bookoptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API başvurusu.</span><span class="sxs-lookup"><span data-stu-id="4cddd-160">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="4cddd-161">Yapılandırma seçenekleri şu şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="4cddd-161">Configuration options can be used to:</span></span>

* <span data-ttu-id="4cddd-162">Kullanıcı hakkında farklı bilgiler isteyin.</span><span class="sxs-lookup"><span data-stu-id="4cddd-162">Request different information about the user.</span></span>
* <span data-ttu-id="4cddd-163">Oturum açma deneyimini özelleştirmek için sorgu dizesi bağımsız değişkenleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4cddd-163">Add query string arguments to customize the login experience.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="4cddd-164">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="4cddd-164">Troubleshooting</span></span>

* <span data-ttu-id="4cddd-165">**Yalnızca 2. x ASP.NET Core:** Identity `services.AddIdentity` ' De çağırarak yapılandırılmamışsa `ConfigureServices` , kimlik doğrulamaya çalışmak ArgumentException ile sonuçlanır *: ' signınscheme ' seçeneği sağlanmalıdır*.</span><span class="sxs-lookup"><span data-stu-id="4cddd-165">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="4cddd-166">Bu öğreticide kullanılan proje şablonu bunun yapılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="4cddd-166">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="4cddd-167">Site veritabanı ilk geçiş uygulanarak oluşturulmadıysa, *istek hatasını Işlerken bir veritabanı işlemi başarısız oldu* .</span><span class="sxs-lookup"><span data-stu-id="4cddd-167">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="4cddd-168">Veritabanını oluşturmak için **geçişleri Uygula** ' ya dokunun ve hatanın ötesinde devam etmek için yenileyin.</span><span class="sxs-lookup"><span data-stu-id="4cddd-168">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4cddd-169">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="4cddd-169">Next steps</span></span>

* <span data-ttu-id="4cddd-170">Bu makalede Facebook ile kimlik doğrulaması yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4cddd-170">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="4cddd-171">[Önceki sayfada](xref:security/authentication/social/index)listelenen diğer sağlayıcılarla kimlik doğrulaması yapmak için benzer bir yaklaşımı izleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4cddd-171">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="4cddd-172">Web sitenizi Azure Web App 'e yayımladığınızda, Facebook Geliştirici portalındaki ' ı sıfırlamanız gerekir `AppSecret` .</span><span class="sxs-lookup"><span data-stu-id="4cddd-172">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="4cddd-173">`Authentication:Facebook:AppId`Ve `Authentication:Facebook:AppSecret` Azure Portal uygulama ayarları olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="4cddd-173">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="4cddd-174">Yapılandırma sistemi, ortam değişkenlerinden anahtarları okumak üzere ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="4cddd-174">The configuration system is set up to read keys from environment variables.</span></span>
