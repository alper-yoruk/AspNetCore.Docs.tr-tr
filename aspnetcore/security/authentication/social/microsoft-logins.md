---
title: ASP.NET Core ile Microsoft Hesabı harici giriş kurulumu
author: rick-anderson
description: Bu örnek, Microsoft hesabı kullanıcı kimlik doğrulamasının varolan bir ASP.NET Core uygulamasına entegrasyonunu gösterir.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 12c86456dad86731b86487a3a4dd725f36677002
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384041"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="bf8dd-103">ASP.NET Core ile Microsoft Hesabı harici giriş kurulumu</span><span class="sxs-lookup"><span data-stu-id="bf8dd-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="bf8dd-104">Yazar: [Valeriy Novytskyy](https://github.com/01binary) ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bf8dd-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bf8dd-105">Bu örnek, önceki [sayfada](xref:security/authentication/social/index)oluşturulan ASP.NET Core 3.0 projesini kullanarak kullanıcıların çalışmaları, okulları veya kişisel Microsoft hesapları yla oturum açmalarını nasıl sağlayacağınızı gösterir.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-105">This sample shows you how to enable users to sign in with their work, school, or personal Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="bf8dd-106">Uygulamayı Microsoft Geliştirici Portalı'nda oluşturun</span><span class="sxs-lookup"><span data-stu-id="bf8dd-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="bf8dd-107">Projeye [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="bf8dd-108">[Azure portalına](https://go.microsoft.com/fwlink/?linkid=2083908) gidin - Uygulama kayıtları sayfasına gidin ve bir Microsoft hesabı oluşturun veya oturum açın:</span><span class="sxs-lookup"><span data-stu-id="bf8dd-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="bf8dd-109">Microsoft hesabınız yoksa, bir tane **oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="bf8dd-110">Oturum açmadan **sonra, Uygulama kayıtları** sayfasına yönlendirilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="bf8dd-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="bf8dd-111">**Yeni kayıt** seçin</span><span class="sxs-lookup"><span data-stu-id="bf8dd-111">Select **New registration**</span></span>
* <span data-ttu-id="bf8dd-112">Bir **Ad**girin.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-112">Enter a **Name**.</span></span>
* <span data-ttu-id="bf8dd-113">Desteklenen hesap **türleri**için bir seçenek seçin.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* <span data-ttu-id="bf8dd-114">**Redirect URI**altında, `/signin-microsoft` eklenen geliştirme URL'nizi girin.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-114">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="bf8dd-115">Örneğin, `https://localhost:5001/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-115">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="bf8dd-116">Bu örnekte daha sonra yapılandırılan Microsoft kimlik doğrulama `/signin-microsoft` düzeni, OAuth akışını uygulamak için rotadaki istekleri otomatik olarak işler.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-116">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="bf8dd-117">**Kaydol'u** Seçin</span><span class="sxs-lookup"><span data-stu-id="bf8dd-117">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="bf8dd-118">İstemci sırrı oluşturma</span><span class="sxs-lookup"><span data-stu-id="bf8dd-118">Create client secret</span></span>

* <span data-ttu-id="bf8dd-119">Sol bölmede, **Sertifikalar & sırları**seçin.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-119">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="bf8dd-120">**İstemci sırları**altında, **Yeni istemci sırrını** seçin</span><span class="sxs-lookup"><span data-stu-id="bf8dd-120">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="bf8dd-121">İstemci sırrı için bir açıklama ekleyin.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-121">Add a description for the client secret.</span></span>
  * <span data-ttu-id="bf8dd-122">**Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-122">Select the **Add** button.</span></span>

* <span data-ttu-id="bf8dd-123">**İstemci sırları**altında, istemci sırrının değerini kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-123">Under **Client secrets**, copy the value of the client secret.</span></span>

<span data-ttu-id="bf8dd-124">URI kesimi, `/signin-microsoft` Microsoft kimlik doğrulama sağlayıcısının varsayılan geri araması olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-124">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="bf8dd-125">[MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) sınıfının devralınan [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) özelliği aracılığıyla Microsoft kimlik doğrulama aracını yapılandırırken varsayılan geri arama URI'sini değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-125">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="bf8dd-126">Microsoft istemci kimliğini ve gizlisini depolama</span><span class="sxs-lookup"><span data-stu-id="bf8dd-126">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="bf8dd-127">Microsoft istemci kimliği ve gizli değerler gibi hassas ayarları [Gizli Yönetici](xref:security/app-secrets)ile saklayın.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-127">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="bf8dd-128">Bu örnek için aşağıdaki adımları kullanın:</span><span class="sxs-lookup"><span data-stu-id="bf8dd-128">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="bf8dd-129">[Gizli depolamayı etkinleştir'deki](xref:security/app-secrets#enable-secret-storage)yönergelere göre gizli depolama için projeyi başlatma.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-129">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="bf8dd-130">Hassas ayarları gizli anahtarlarla `Authentication:Microsoft:ClientId` yerel gizli mağazada saklayın ve: `Authentication:Microsoft:ClientSecret`</span><span class="sxs-lookup"><span data-stu-id="bf8dd-130">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="bf8dd-131">Microsoft Hesap Kimlik Doğrulamasını Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="bf8dd-131">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="bf8dd-132">Microsoft Hesabı hizmetini aşağıdakilere `Startup.ConfigureServices`ekleyin:</span><span class="sxs-lookup"><span data-stu-id="bf8dd-132">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="bf8dd-133">Microsoft Hesabı kimlik doğrulaması tarafından desteklenen yapılandırma seçenekleri hakkında daha fazla bilgi için [Microsoft AccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API başvurusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-133">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="bf8dd-134">Bu, kullanıcı hakkında farklı bilgiler istemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-134">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="bf8dd-135">Microsoft Hesabı ile oturum açın</span><span class="sxs-lookup"><span data-stu-id="bf8dd-135">Sign in with Microsoft Account</span></span>

<span data-ttu-id="bf8dd-136">Uygulamayı çalıştırın ve **Giriş Yap'a**tıklayın.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-136">Run the app and click **Log in**.</span></span> <span data-ttu-id="bf8dd-137">Microsoft ile oturum açma seçeneği görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-137">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="bf8dd-138">Microsoft'u tıklattığınızda, kimlik doğrulaması için Microsoft'a yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-138">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="bf8dd-139">Microsoft Hesabınızla oturum açtıktan sonra, uygulamanın bilgilerinize erişmesine izin vermeniz istenir:</span><span class="sxs-lookup"><span data-stu-id="bf8dd-139">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="bf8dd-140">**Evet'e** dokunduğunuzda e-postanızı ayarlayabileceğiniz web sitesine geri yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-140">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="bf8dd-141">Artık Microsoft kimlik bilgilerinizi kullanarak oturum açtınız:</span><span class="sxs-lookup"><span data-stu-id="bf8dd-141">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="bf8dd-142">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="bf8dd-142">Troubleshooting</span></span>

* <span data-ttu-id="bf8dd-143">Microsoft Hesabı sağlayıcısı sizi bir oturum açma hatası sayfasına yönlendirirse, Uri'deki `#` (hashtag) doğrudan aşağıdaki hata başlığı ve açıklama sorgusu dize parametrelerine dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-143">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="bf8dd-144">Hata iletisi Microsoft kimlik doğrulamasıyla ilgili bir sorun olduğunu gösterse de, en yaygın neden uygulamanız Uri'nin **Web** platformu için belirtilen **Yönlendirme URL'lerinden** herhangi biriyle eşleşmemesidir.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-144">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="bf8dd-145">Kimlik arayarak `services.AddIdentity` `ConfigureServices`yapılandırılmamışsa, kimlik doğrulamaya çalışmak ArgumentException ile *sonuçlanır: 'SignInScheme' seçeneği sağlanmalıdır.*</span><span class="sxs-lookup"><span data-stu-id="bf8dd-145">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="bf8dd-146">Bu örnekte kullanılan proje şablonu bunun yapılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-146">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="bf8dd-147">Site veritabanı ilk geçiş uygulanarak oluşturulmamışsa, *istek hatasını işlerken bir veritabanı işlemi başarısız olur.*</span><span class="sxs-lookup"><span data-stu-id="bf8dd-147">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="bf8dd-148">Veritabanını oluşturmak için **Geçişleri Uygula'ya** dokunun ve hatayı geride devam etmek için yenileyin.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-148">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="bf8dd-149">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="bf8dd-149">Next steps</span></span>

* <span data-ttu-id="bf8dd-150">Bu makalede, Microsoft ile kimlik doğrulaması nasıl gösterilebilir.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-150">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="bf8dd-151">[Önceki sayfada](xref:security/authentication/social/index)listelenen diğer sağlayıcılarla kimlik doğrulaması yapmak için benzer bir yaklaşım izleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-151">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="bf8dd-152">Web sitenizi Azure web uygulamasında yayımladıktan sonra Microsoft Geliştirici Portalı'nda yeni bir istemci sırları oluşturun.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-152">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="bf8dd-153">Azure `Authentication:Microsoft:ClientId` portalında uygulama ayarlarını ve uygulama `Authentication:Microsoft:ClientSecret` ayarlarını ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-153">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="bf8dd-154">Yapılandırma sistemi, ortam değişkenlerinden anahtarları okumak üzere ayarlanmıştır.</span><span class="sxs-lookup"><span data-stu-id="bf8dd-154">The configuration system is set up to read keys from environment variables.</span></span>
