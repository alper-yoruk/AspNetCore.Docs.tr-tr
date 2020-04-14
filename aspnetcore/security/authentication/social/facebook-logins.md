---
title: ASP.NET Core'da Facebook harici giriş kurulumu
author: rick-anderson
description: Facebook hesabı kullanıcı kimlik doğrulamasının varolan bir ASP.NET Core uygulamasına entegrasyonunu gösteren kod örnekleri içeren öğretici.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 9b3128addafb41ad6ec44af5cb12e89607e1ae59
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277307"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="894ec-103">ASP.NET Core'da Facebook harici giriş kurulumu</span><span class="sxs-lookup"><span data-stu-id="894ec-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="894ec-104">Yazar: [Valeriy Novytskyy](https://github.com/01binary) ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="894ec-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

<span data-ttu-id="894ec-105">Kod örnekleri içeren bu öğretici, kullanıcılarınızın [önceki sayfada](xref:security/authentication/social/index)oluşturulan Core 3.0 ASP.NET bir örnek kullanarak Facebook hesabıyla oturum açmalarını nasıl sağlayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="894ec-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="894ec-106">[Resmi adımları](https://developers.facebook.com)izleyerek bir Facebook App ID oluşturarak işe başlıyoruz.</span><span class="sxs-lookup"><span data-stu-id="894ec-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="894ec-107">Uygulamayı Facebook'ta oluşturun</span><span class="sxs-lookup"><span data-stu-id="894ec-107">Create the app in Facebook</span></span>

* <span data-ttu-id="894ec-108">Projeye [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="894ec-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="894ec-109">[Facebook Geliştiricileri uygulama](https://developers.facebook.com/apps/) sayfasına gidin ve oturum açın.</span><span class="sxs-lookup"><span data-stu-id="894ec-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="894ec-110">Zaten bir Facebook hesabınız yoksa, giriş sayfasındafacebook için **Kaydol** bağlantısını kullanarak bir hesap oluşturun.</span><span class="sxs-lookup"><span data-stu-id="894ec-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="894ec-111">Bir Facebook hesabınız olduktan sonra, Facebook Geliştiricisi olarak kaydolmak için talimatları izleyin.</span><span class="sxs-lookup"><span data-stu-id="894ec-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="894ec-112">**Uygulamalarım** menüsünden yeni bir Uygulama Kimliği oluşturmak için **Uygulama Oluştur'u** seçin.</span><span class="sxs-lookup"><span data-stu-id="894ec-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Microsoft Edge'de geliştiriciler portalı için Facebook açıldı](index/_static/FBMyApps.png)

* <span data-ttu-id="894ec-114">Formu doldurun ve **Uygulama Kimliği Oluştur** düğmesine dokunun.</span><span class="sxs-lookup"><span data-stu-id="894ec-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![Yeni Uygulama Kimliği formu oluşturma](index/_static/FBNewAppId.png)

* <span data-ttu-id="894ec-116">Yeni Uygulama kartında **Ürün Ekle'yi**seçin.</span><span class="sxs-lookup"><span data-stu-id="894ec-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="894ec-117">Facebook **Giriş** kartında, **Ayarla'yı** tıklatın</span><span class="sxs-lookup"><span data-stu-id="894ec-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![Ürün Kurulumu sayfası](index/_static/FBProductSetup.png)

* <span data-ttu-id="894ec-119">**Quickstart** sihirbazı, ilk sayfa olarak **Bir Platform Seç** ile başlatılır.</span><span class="sxs-lookup"><span data-stu-id="894ec-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="894ec-120">Sol alttaki menüdeki **FaceBook Giriş** **Ayarları** bağlantısını tıklayarak sihirbazı şimdilik atlayın:</span><span class="sxs-lookup"><span data-stu-id="894ec-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![Hızlı Başlat'ı Atla](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="894ec-122">**Size Client OAuth Ayarları** sayfası sunulur:</span><span class="sxs-lookup"><span data-stu-id="894ec-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![İstemci OAuth Ayarları sayfası](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="894ec-124">**Geçerli OAuth Yönlendirme URI** alanına eklenen */signin-facebook* ile geliştirme URI `https://localhost:44320/signin-facebook`girin (örneğin: ).</span><span class="sxs-lookup"><span data-stu-id="894ec-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="894ec-125">Daha sonra bu öğreticide yapılandırılan Facebook kimlik doğrulaması, OAuth akışını uygulamak için */signin-facebook rotasındaki* istekleri otomatik olarak işler.</span><span class="sxs-lookup"><span data-stu-id="894ec-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="894ec-126">URI */signin-facebook,* Facebook kimlik doğrulama sağlayıcısının varsayılan geri araması olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="894ec-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="894ec-127">[FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) sınıfının devralınan [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) özelliği aracılığıyla Facebook kimlik doğrulama aracını yapılandırırken varsayılan geri arama URI'yi değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="894ec-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="894ec-128">**Değişiklikleri Kaydet**’e tıklayın.</span><span class="sxs-lookup"><span data-stu-id="894ec-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="894ec-129">Sol navigasyonda **Ayarlar** > **Temel** bağlantısını tıklatın.</span><span class="sxs-lookup"><span data-stu-id="894ec-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="894ec-130">Bu sayfada, sizin `App ID` ve . `App Secret`</span><span class="sxs-lookup"><span data-stu-id="894ec-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="894ec-131">Bir sonraki bölümde ASP.NET Core uygulamanıza her ikisini de eklersiniz:</span><span class="sxs-lookup"><span data-stu-id="894ec-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="894ec-132">Siteyi dağıtırken **Facebook Giriş** kurulum sayfasını yeniden ziyaret etmeli ve yeni bir genel URI kaydettirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="894ec-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="894ec-133">Facebook uygulama kimliğini ve gizlisini saklama</span><span class="sxs-lookup"><span data-stu-id="894ec-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="894ec-134">[Gizli Yönetici](xref:security/app-secrets)ile Facebook uygulama kimliği ve gizli değerler gibi hassas ayarları saklayın.</span><span class="sxs-lookup"><span data-stu-id="894ec-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="894ec-135">Bu örnek için aşağıdaki adımları kullanın:</span><span class="sxs-lookup"><span data-stu-id="894ec-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="894ec-136">[Gizli depolamayı etkinleştir'deki](xref:security/app-secrets#enable-secret-storage)yönergelere göre gizli depolama için projeyi başlatma.</span><span class="sxs-lookup"><span data-stu-id="894ec-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="894ec-137">Hassas ayarları gizli anahtarlarla `Authentication:Facebook:AppId` yerel gizli mağazada saklayın ve: `Authentication:Facebook:AppSecret`</span><span class="sxs-lookup"><span data-stu-id="894ec-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="894ec-138">Facebook Kimlik Doğrulamasını Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="894ec-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="894ec-139">`ConfigureServices` *Startup.cs* dosyasındaki yöntemde Facebook hizmetini ekleyin:</span><span class="sxs-lookup"><span data-stu-id="894ec-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a><span data-ttu-id="894ec-140">Facebook ile oturum açın</span><span class="sxs-lookup"><span data-stu-id="894ec-140">Sign in with Facebook</span></span>

* <span data-ttu-id="894ec-141">Uygulamayı çalıştırın ve **Giriş Yap'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="894ec-141">Run the app and select **Log in**.</span></span> 
* <span data-ttu-id="894ec-142">Oturum **açmak için başka bir hizmet kullan altında.**, Facebook'u seçin.</span><span class="sxs-lookup"><span data-stu-id="894ec-142">Under **Use another service to log in.**, select Facebook.</span></span>
* <span data-ttu-id="894ec-143">Kimlik doğrulaması için **Facebook'a** yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="894ec-143">You are redirected to **Facebook** for authentication.</span></span>
* <span data-ttu-id="894ec-144">Facebook kimlik bilgilerinizi girin.</span><span class="sxs-lookup"><span data-stu-id="894ec-144">Enter your Facebook credentials.</span></span>
* <span data-ttu-id="894ec-145">E-postanızı ayarlayabildiğiniz sitenize yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="894ec-145">You are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="894ec-146">Artık Facebook kimlik bilgilerinizi kullanarak oturum açtınız:</span><span class="sxs-lookup"><span data-stu-id="894ec-146">You are now logged in using your Facebook credentials:</span></span>

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a><span data-ttu-id="894ec-147">Harici oturum açma yetkisinin iptaline tepki</span><span class="sxs-lookup"><span data-stu-id="894ec-147">React to cancel authorize external sign-in</span></span>

<span data-ttu-id="894ec-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>kullanıcı istenen yetkilendirme talebini onaylamadığında kullanıcı aracısına yönlendirme yolu sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="894ec-148"><xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath> can provide a redirect path to the user agent when the user doesn't approve the requested authorization demand.</span></span>

<span data-ttu-id="894ec-149">Aşağıdaki kod şu `AccessDeniedPath` `"/AccessDeniedPathInfo"`şekilde ayarlar:</span><span class="sxs-lookup"><span data-stu-id="894ec-149">The following code sets the `AccessDeniedPath` to `"/AccessDeniedPathInfo"`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

<span data-ttu-id="894ec-150">Sayfanın `AccessDeniedPath` aşağıdaki bilgileri içermesini öneririz:</span><span class="sxs-lookup"><span data-stu-id="894ec-150">We recommend the `AccessDeniedPath` page contain the following information:</span></span>

*  <span data-ttu-id="894ec-151">Uzaktan kimlik doğrulama iptal edildi.</span><span class="sxs-lookup"><span data-stu-id="894ec-151">Remote authentication was canceled.</span></span>
* <span data-ttu-id="894ec-152">Bu uygulama kimlik doğrulaması gerektirir.</span><span class="sxs-lookup"><span data-stu-id="894ec-152">This app requires authentication.</span></span>
* <span data-ttu-id="894ec-153">Yeniden oturum açmayı denemek için Giriş bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="894ec-153">To try sign-in again, select the Login link.</span></span>

### <a name="test-accessdeniedpath"></a><span data-ttu-id="894ec-154">Test AccessDeniedPath</span><span class="sxs-lookup"><span data-stu-id="894ec-154">Test AccessDeniedPath</span></span>

* <span data-ttu-id="894ec-155">[facebook.com'ye](https://www.facebook.com/) gidin</span><span class="sxs-lookup"><span data-stu-id="894ec-155">Navigate to [facebook.com](https://www.facebook.com/)</span></span>
* <span data-ttu-id="894ec-156">Oturum açmışsanız, oturum açmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="894ec-156">If you are signed in, you must sign out.</span></span>
* <span data-ttu-id="894ec-157">Uygulamayı çalıştırın ve Facebook oturum aç'ını seçin.</span><span class="sxs-lookup"><span data-stu-id="894ec-157">Run the app and select Facebook sign-in.</span></span>
* <span data-ttu-id="894ec-158">**Şimdi Değil'i**seçin.</span><span class="sxs-lookup"><span data-stu-id="894ec-158">Select **Not now**.</span></span> <span data-ttu-id="894ec-159">Belirtilen `AccessDeniedPath` sayfaya yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="894ec-159">You are redirected to the specified `AccessDeniedPath` page.</span></span>

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="894ec-160">Facebook kimlik doğrulaması tarafından desteklenen yapılandırma seçenekleri hakkında daha fazla bilgi için [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API başvurusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="894ec-160">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="894ec-161">Yapılandırma seçenekleri şu şekilde kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="894ec-161">Configuration options can be used to:</span></span>

* <span data-ttu-id="894ec-162">Kullanıcı hakkında farklı bilgiler isteyin.</span><span class="sxs-lookup"><span data-stu-id="894ec-162">Request different information about the user.</span></span>
* <span data-ttu-id="894ec-163">Oturum açma deneyimini özelleştirmek için sorgu dizebağımsız değişkenleri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="894ec-163">Add query string arguments to customize the login experience.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="894ec-164">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="894ec-164">Troubleshooting</span></span>

* <span data-ttu-id="894ec-165">**ASP.NET Core 2.x yalnızca:** Kimlik arayarak `services.AddIdentity` `ConfigureServices`yapılandırılmamışsa, kimlik doğrulamaya çalışmak ArgumentException ile *sonuçlanır: 'SignInScheme' seçeneği sağlanmalıdır.*</span><span class="sxs-lookup"><span data-stu-id="894ec-165">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="894ec-166">Bu öğreticide kullanılan proje şablonu bunun yapılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="894ec-166">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="894ec-167">Site veritabanı ilk geçiş uygulanarak oluşturulmamışsa, *istek hatasını işlerken bir veritabanı işlemi başarısız olur.*</span><span class="sxs-lookup"><span data-stu-id="894ec-167">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="894ec-168">Veritabanını oluşturmak için **Geçişleri Uygula'ya** dokunun ve hatayı geride devam etmek için yenileyin.</span><span class="sxs-lookup"><span data-stu-id="894ec-168">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="894ec-169">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="894ec-169">Next steps</span></span>

* <span data-ttu-id="894ec-170">Bu makalede, Facebook ile kimlik doğrulaması nasıl gösterilebilir.</span><span class="sxs-lookup"><span data-stu-id="894ec-170">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="894ec-171">[Önceki sayfada](xref:security/authentication/social/index)listelenen diğer sağlayıcılarla kimlik doğrulaması yapmak için benzer bir yaklaşım izleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="894ec-171">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="894ec-172">Web sitenizi Azure web uygulamasında yayımladıktan sonra, `AppSecret` Facebook geliştirici portalındaki web sitesini sıfırlamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="894ec-172">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="894ec-173">Azure `Authentication:Facebook:AppId` portalında uygulama ayarlarını ve uygulama `Authentication:Facebook:AppSecret` ayarlarını ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="894ec-173">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="894ec-174">Yapılandırma sistemi, ortam değişkenlerinden anahtarları okumak üzere ayarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="894ec-174">The configuration system is set up to read keys from environment variables.</span></span>
