---
title: ASP.NET Core ile Twitter dışarıdan oturum açma kurulumu
author: rick-anderson
description: Bu öğreticide, Twitter hesabı kullanıcı kimlik doğrulamasının mevcut bir ASP.NET Core uygulamasına tümleştirilmesi gösterilmektedir.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/twitter-logins
ms.openlocfilehash: c6498704214de5e805c9bf57033529d4acc5fd3e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775797"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="cd031-103">ASP.NET Core ile Twitter dışarıdan oturum açma kurulumu</span><span class="sxs-lookup"><span data-stu-id="cd031-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="cd031-104">Tarafından [Valeriy Novyıtskyy](https://github.com/01binary) ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cd031-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cd031-105">Bu örnek, [önceki sayfada](xref:security/authentication/social/index)oluşturulmuş bir örnek ASP.NET Core 3,0 projesi kullanarak kullanıcıların [Twitter hesabıyla oturum açmasını](https://dev.twitter.com/web/sign-in/desktop-browser) nasıl olanaklı hale kullanabileceğinizi gösterir.</span><span class="sxs-lookup"><span data-stu-id="cd031-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="cd031-106">Uygulamayı Twitter 'da oluşturma</span><span class="sxs-lookup"><span data-stu-id="cd031-106">Create the app in Twitter</span></span>

* <span data-ttu-id="cd031-107">Projeye [Microsoft. AspNetCore. Authentication. Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cd031-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="cd031-108">Gidin [https://apps.twitter.com/](https://apps.twitter.com/) ve oturum açın.</span><span class="sxs-lookup"><span data-stu-id="cd031-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="cd031-109">Zaten bir Twitter hesabınız yoksa, oluşturmak için **[Şimdi kaydolun](https://twitter.com/signup)** bağlantısını kullanın.</span><span class="sxs-lookup"><span data-stu-id="cd031-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="cd031-110">**Uygulama oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="cd031-110">Select **Create an app**.</span></span> <span data-ttu-id="cd031-111">**Uygulama adı**, **uygulama açıklaması** ve genel **Web sitesi** URI 'sini doldurun (etki alanı adı kaydoluncaya kadar geçici olabilir):</span><span class="sxs-lookup"><span data-stu-id="cd031-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="cd031-112">**Twitter Ile oturum açmayı etkinleştir** seçeneğinin yanındaki kutuyu işaretleyin</span><span class="sxs-lookup"><span data-stu-id="cd031-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="cd031-113">Microsoft. AspNetCore.Identity</span><span class="sxs-lookup"><span data-stu-id="cd031-113">Microsoft.AspNetCore.Identity</span></span> <span data-ttu-id="cd031-114">Kullanıcıların varsayılan olarak bir e-posta adresi olmasını gerektirir.</span><span class="sxs-lookup"><span data-stu-id="cd031-114">requires users to have an email address by default.</span></span> <span data-ttu-id="cd031-115">**İzinler** sekmesine gidin, **Düzenle** düğmesine tıklayın ve **kullanıcılardan e-posta adresi iste**' nin yanındaki kutuyu işaretleyin.</span><span class="sxs-lookup"><span data-stu-id="cd031-115">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users**.</span></span>

* <span data-ttu-id="cd031-116">**Geri arama URL 'leri** `https://webapp128.azurewebsites.net/signin-twitter`alanına `/signin-twitter` eklenen geliştirme URI 'nizi girin (örneğin:).</span><span class="sxs-lookup"><span data-stu-id="cd031-116">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="cd031-117">Bu örnekte daha sonra yapılandırılan Twitter kimlik doğrulama şeması, OAuth akışını uygulamak için `/signin-twitter` rotadaki istekleri otomatik olarak işler.</span><span class="sxs-lookup"><span data-stu-id="cd031-117">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="cd031-118">URI segmenti `/signin-twitter` Twitter kimlik doğrulama sağlayıcısının varsayılan geri çağırması olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="cd031-118">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="cd031-119">Twitter kimlik doğrulama ara [yazılımını, '](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) ın devralınan [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) özelliğini kullanarak YAPıLANDıRıRKEN varsayılan geri çağırma URI 'sini değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cd031-119">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="cd031-120">Formun geri kalanını doldurun ve **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="cd031-120">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="cd031-121">Yeni uygulama ayrıntıları görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="cd031-121">New application details are displayed:</span></span>

## <a name="store-the-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="cd031-122">Twitter tüketici API anahtarını ve gizli dizisini depolayın</span><span class="sxs-lookup"><span data-stu-id="cd031-122">Store the Twitter consumer API key and secret</span></span>

<span data-ttu-id="cd031-123">Twitter tüketicisi API anahtarı ve gizli dizi [Yöneticisi](xref:security/app-secrets)ile gizli dizi gibi hassas ayarları depolayın.</span><span class="sxs-lookup"><span data-stu-id="cd031-123">Store sensitive settings such as the Twitter consumer API key and secret with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="cd031-124">Bu örnek için aşağıdaki adımları kullanın:</span><span class="sxs-lookup"><span data-stu-id="cd031-124">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="cd031-125">[Gizli depolamayı etkinleştirme](xref:security/app-secrets#enable-secret-storage)konusundaki yönergeler temelinde projeyi gizli depolama için başlatın.</span><span class="sxs-lookup"><span data-stu-id="cd031-125">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="cd031-126">Gizli ayarları gizli anahtar deposunda gizli anahtarları `Authentication:Twitter:ConsumerKey` ve `Authentication:Twitter:ConsumerSecret`bunlarla depolayın:</span><span class="sxs-lookup"><span data-stu-id="cd031-126">Store the sensitive settings in the local secret store with the secrets keys `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="cd031-127">Bu belirteçler, yeni bir Twitter uygulaması oluşturduktan sonra **anahtarlar ve erişim belirteçleri** sekmesinde bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="cd031-127">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="cd031-128">Twitter kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="cd031-128">Configure Twitter Authentication</span></span>

<span data-ttu-id="cd031-129">Twitter hizmetini `ConfigureServices` *Startup.cs* dosyasındaki yöntemine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="cd031-129">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="cd031-130">Twitter kimlik doğrulaması tarafından desteklenen yapılandırma seçenekleri hakkında daha fazla bilgi için, bkz. [dallı ve seçenekleri](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API başvurusu.</span><span class="sxs-lookup"><span data-stu-id="cd031-130">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="cd031-131">Bu, Kullanıcı hakkında farklı bilgiler istemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="cd031-131">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="cd031-132">Twitter ile oturum açma</span><span class="sxs-lookup"><span data-stu-id="cd031-132">Sign in with Twitter</span></span>

<span data-ttu-id="cd031-133">Uygulamayı çalıştırın ve **oturum aç '** ı seçin.</span><span class="sxs-lookup"><span data-stu-id="cd031-133">Run the app and select **Log in**.</span></span> <span data-ttu-id="cd031-134">Twitter ile oturum açma seçeneği görünür:</span><span class="sxs-lookup"><span data-stu-id="cd031-134">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="cd031-135">**Twitter** 'ya tıkladığınızda, kimlik doğrulaması için Twitter 'a yeniden yönlendirme yapılır:</span><span class="sxs-lookup"><span data-stu-id="cd031-135">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="cd031-136">Twitter kimlik bilgilerinizi girdikten sonra, e-postanızı ayarlayabileceğiniz Web sitesine geri yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cd031-136">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="cd031-137">Twitter kimlik bilgilerinizi kullanarak oturumunuz açıldı:</span><span class="sxs-lookup"><span data-stu-id="cd031-137">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a><span data-ttu-id="cd031-138">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="cd031-138">Troubleshooting</span></span>

* <span data-ttu-id="cd031-139">**Yalnızca 2. x ASP.NET Core:** Identity ' De `services.AddIdentity` `ConfigureServices`çağırarak yapılandırılmamışsa, kimlik doğrulamaya çalışmak ArgumentException Ile sonuçlanır *: ' signınscheme ' seçeneği sağlanmalıdır*.</span><span class="sxs-lookup"><span data-stu-id="cd031-139">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="cd031-140">Bu örnekte kullanılan proje şablonu bunun yapılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="cd031-140">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="cd031-141">Site veritabanı ilk geçiş uygulanarak oluşturulmadıysa, *istek hatasını Işlerken bir veritabanı işlemi başarısız* olur.</span><span class="sxs-lookup"><span data-stu-id="cd031-141">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="cd031-142">Veritabanını oluşturmak için **geçişleri Uygula** ' ya dokunun ve hatanın ötesinde devam etmek için yenileyin.</span><span class="sxs-lookup"><span data-stu-id="cd031-142">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="cd031-143">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="cd031-143">Next steps</span></span>

* <span data-ttu-id="cd031-144">Bu makalede Twitter ile kimlik doğrulaması yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cd031-144">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="cd031-145">[Önceki sayfada](xref:security/authentication/social/index)listelenen diğer sağlayıcılarla kimlik doğrulaması yapmak için benzer bir yaklaşımı izleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="cd031-145">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="cd031-146">Web sitenizi Azure Web App 'e yayımladığınızda, `ConsumerSecret` Twitter geliştirici portalındaki ' ı sıfırlamanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="cd031-146">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="cd031-147">`Authentication:Twitter:ConsumerKey` Ve `Authentication:Twitter:ConsumerSecret` Azure Portal uygulama ayarları olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="cd031-147">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="cd031-148">Yapılandırma sistemi, ortam değişkenlerinden anahtarları okumak üzere ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="cd031-148">The configuration system is set up to read keys from environment variables.</span></span>
