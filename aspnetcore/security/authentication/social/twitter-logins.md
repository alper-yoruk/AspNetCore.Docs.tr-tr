---
title: ASP.NET Core ile Twitter harici oturum açma kurulumu
author: rick-anderson
description: Bu öğretici, Twitter hesabı kullanıcı kimlik doğrulamasının mevcut bir ASP.NET Core uygulamasına entegrasyonunu göstermektedir.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 1f5d667e905e49ae05f5aa31bd5b69ad126f6e28
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277294"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="6084e-103">ASP.NET Core ile Twitter harici oturum açma kurulumu</span><span class="sxs-lookup"><span data-stu-id="6084e-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="6084e-104">Yazar: [Valeriy Novytskyy](https://github.com/01binary) ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="6084e-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="6084e-105">Bu örnek, kullanıcıların [önceki sayfada](xref:security/authentication/social/index)oluşturulan Core 3.0 projesi ASP.NET bir örnek kullanarak [Twitter hesabıyla oturum açmalarını](https://dev.twitter.com/web/sign-in/desktop-browser) nasıl sağlayacaklarını gösterir.</span><span class="sxs-lookup"><span data-stu-id="6084e-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="6084e-106">Uygulamayı Twitter'da oluşturun</span><span class="sxs-lookup"><span data-stu-id="6084e-106">Create the app in Twitter</span></span>

* <span data-ttu-id="6084e-107">Projeye [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6084e-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="6084e-108">Gidin [https://apps.twitter.com/](https://apps.twitter.com/) ve oturum açın.</span><span class="sxs-lookup"><span data-stu-id="6084e-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="6084e-109">Zaten bir Twitter hesabınız yoksa, bir tane oluşturmak için **[şimdi Kaydol](https://twitter.com/signup)** bağlantısını kullanın.</span><span class="sxs-lookup"><span data-stu-id="6084e-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="6084e-110">**Uygulama Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="6084e-110">Select **Create an app**.</span></span> <span data-ttu-id="6084e-111">**Uygulama adını,** **Uygulama açıklamasını** ve genel Web **Sitesi** URI'yi doldurun (alan adını kaydettirene kadar geçici olabilir):</span><span class="sxs-lookup"><span data-stu-id="6084e-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="6084e-112">Twitter ile **Oturum Açma'yı Etkinleştirmek** için yanındaki kutuyu işaretleyin</span><span class="sxs-lookup"><span data-stu-id="6084e-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="6084e-113">Microsoft.AspNetCore.Identity, kullanıcıların varsayılan olarak bir e-posta adresine sahip olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="6084e-113">Microsoft.AspNetCore.Identity requires users to have an email address by default.</span></span> <span data-ttu-id="6084e-114">**İzinler** sekmesine gidin, **Edit** düğmesini tıklatın ve **kullanıcılardan e-posta adresi isteyin**yanındaki kutuyu işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="6084e-114">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users**.</span></span>

* <span data-ttu-id="6084e-115">**Geri Arama URL'leri** alanına `https://webapp128.azurewebsites.net/signin-twitter` `/signin-twitter` eklenen geliştirme URI'nizi girin (örneğin: ).</span><span class="sxs-lookup"><span data-stu-id="6084e-115">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="6084e-116">Daha sonra bu örnekte yapılandırılan Twitter kimlik doğrulama `/signin-twitter` şeması, OAuth akışını uygulamak için rotadaki istekleri otomatik olarak işler.</span><span class="sxs-lookup"><span data-stu-id="6084e-116">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="6084e-117">URI segmenti, `/signin-twitter` Twitter kimlik doğrulama sağlayıcısının varsayılan geri araması olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="6084e-117">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="6084e-118">[TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) sınıfının devralınan [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) özelliği üzerinden Twitter kimlik doğrulama ara yazılımını yapılandırırken varsayılan geri arama URI'yi değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6084e-118">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="6084e-119">Formun geri kalanını doldurun ve **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="6084e-119">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="6084e-120">Yeni uygulama ayrıntıları görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6084e-120">New application details are displayed:</span></span>

## <a name="store-the-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="6084e-121">Twitter tüketici API anahtarı nı ve gizlisini saklayın</span><span class="sxs-lookup"><span data-stu-id="6084e-121">Store the Twitter consumer API key and secret</span></span>

<span data-ttu-id="6084e-122">[Gizli Yöneticisi](xref:security/app-secrets)ile Twitter tüketici API anahtarı ve gizli gibi hassas ayarları saklayın.</span><span class="sxs-lookup"><span data-stu-id="6084e-122">Store sensitive settings such as the Twitter consumer API key and secret with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="6084e-123">Bu örnek için aşağıdaki adımları kullanın:</span><span class="sxs-lookup"><span data-stu-id="6084e-123">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="6084e-124">[Gizli depolamayı etkinleştir'deki](xref:security/app-secrets#enable-secret-storage)yönergelere göre gizli depolama için projeyi başlatma.</span><span class="sxs-lookup"><span data-stu-id="6084e-124">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="6084e-125">Sırlar anahtarları `Authentication:Twitter:ConsumerKey` ile yerel gizli mağazada `Authentication:Twitter:ConsumerSecret`hassas ayarları saklayın ve:</span><span class="sxs-lookup"><span data-stu-id="6084e-125">Store the sensitive settings in the local secret store with the secrets keys `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="6084e-126">Bu belirteçler, yeni bir Twitter uygulaması oluşturduktan sonra **Anahtarlar ve Erişim Belirteçleri** sekmesinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="6084e-126">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="6084e-127">Twitter Kimlik Doğrulama'yı yapılandırma</span><span class="sxs-lookup"><span data-stu-id="6084e-127">Configure Twitter Authentication</span></span>

<span data-ttu-id="6084e-128">`ConfigureServices` *Startup.cs* dosyasında yöntemde Twitter hizmeti ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6084e-128">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="6084e-129">Twitter kimlik doğrulaması tarafından desteklenen yapılandırma seçenekleri hakkında daha fazla bilgi için [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API başvurusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="6084e-129">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="6084e-130">Bu, kullanıcı hakkında farklı bilgiler istemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="6084e-130">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="6084e-131">Twitter ile oturum açın</span><span class="sxs-lookup"><span data-stu-id="6084e-131">Sign in with Twitter</span></span>

<span data-ttu-id="6084e-132">Uygulamayı çalıştırın ve **Giriş Yap'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="6084e-132">Run the app and select **Log in**.</span></span> <span data-ttu-id="6084e-133">Twitter ile oturum açma seçeneği görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="6084e-133">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="6084e-134">**Twitter'a** tıkladığınızda kimlik doğrulaması için Twitter'a yönlendirir:</span><span class="sxs-lookup"><span data-stu-id="6084e-134">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="6084e-135">Twitter kimlik bilgilerinizi girdikten sonra, e-postanızı ayarlayabileceğiniz web sitesine geri yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6084e-135">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="6084e-136">Artık Twitter kimlik bilgilerinizi kullanarak oturum açtınız:</span><span class="sxs-lookup"><span data-stu-id="6084e-136">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a><span data-ttu-id="6084e-137">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="6084e-137">Troubleshooting</span></span>

* <span data-ttu-id="6084e-138">**ASP.NET Core 2.x yalnızca:** Kimlik arayarak `services.AddIdentity` `ConfigureServices`yapılandırılmamışsa, kimlik doğrulamaya çalışmak ArgumentException ile *sonuçlanır: 'SignInScheme' seçeneği sağlanmalıdır.*</span><span class="sxs-lookup"><span data-stu-id="6084e-138">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="6084e-139">Bu örnekte kullanılan proje şablonu bunun yapılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="6084e-139">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="6084e-140">Site veritabanı ilk geçiş uygulanarak oluşturulmamışsa, *istek hatasını işlerken bir veritabanı işlemi başarısız olur.*</span><span class="sxs-lookup"><span data-stu-id="6084e-140">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="6084e-141">Veritabanını oluşturmak için **Geçişleri Uygula'ya** dokunun ve hatayı geride devam etmek için yenileyin.</span><span class="sxs-lookup"><span data-stu-id="6084e-141">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="6084e-142">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="6084e-142">Next steps</span></span>

* <span data-ttu-id="6084e-143">Bu makale, Twitter ile nasıl doğrulayabilirsiniz gösterdi.</span><span class="sxs-lookup"><span data-stu-id="6084e-143">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="6084e-144">[Önceki sayfada](xref:security/authentication/social/index)listelenen diğer sağlayıcılarla kimlik doğrulaması yapmak için benzer bir yaklaşım izleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6084e-144">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="6084e-145">Web sitenizi Azure web uygulamasında yayımladıktan sonra, `ConsumerSecret` Twitter geliştirici portalındaki web sitenizi sıfırlamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="6084e-145">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="6084e-146">Azure `Authentication:Twitter:ConsumerKey` portalında uygulama ayarlarını ve uygulama `Authentication:Twitter:ConsumerSecret` ayarlarını ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="6084e-146">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="6084e-147">Yapılandırma sistemi, ortam değişkenlerinden anahtarları okumak üzere ayarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="6084e-147">The configuration system is set up to read keys from environment variables.</span></span>
