---
title: ASP.NET Core ile Microsoft hesabı dış oturum açma kurulumu
author: rick-anderson
description: Bu örnek, Microsoft hesabı kullanıcı kimlik doğrulamasının mevcut bir ASP.NET Core uygulamasına tümleştirilmesini gösterir.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 3161e4f0f735294d69dd51634b424d1ed573e615
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060306"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="b243e-103">ASP.NET Core ile Microsoft hesabı dış oturum açma kurulumu</span><span class="sxs-lookup"><span data-stu-id="b243e-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="b243e-104">Tarafından [Valeriy Novyıtskyy](https://github.com/01binary) ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b243e-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b243e-105">Bu örnek, [önceki sayfada](xref:security/authentication/social/index)oluşturulan ASP.NET Core 3,0 projesini kullanarak, kullanıcıların iş, okul veya kişisel Microsoft hesabı oturum açmasını nasıl olanaklı hale kullanabileceğinizi gösterir.</span><span class="sxs-lookup"><span data-stu-id="b243e-105">This sample shows you how to enable users to sign in with their work, school, or personal Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="b243e-106">Uygulamayı Microsoft Geliştirici Portalında oluşturma</span><span class="sxs-lookup"><span data-stu-id="b243e-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="b243e-107">Projeye [Microsoft. AspNetCore. Authentication. MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet paketini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b243e-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="b243e-108">[Azure portal uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) sayfasına gidin ve bir Microsoft hesabı oluşturun veya oturum açın:</span><span class="sxs-lookup"><span data-stu-id="b243e-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="b243e-109">Microsoft hesabı yoksa **bir tane oluştur** ' u seçin.</span><span class="sxs-lookup"><span data-stu-id="b243e-109">If you don't have a Microsoft account, select **Create one** .</span></span> <span data-ttu-id="b243e-110">Oturum açtıktan sonra, **uygulama kayıtları** sayfasına yönlendirilirsiniz:</span><span class="sxs-lookup"><span data-stu-id="b243e-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="b243e-111">**Yeni kayıt** Seç</span><span class="sxs-lookup"><span data-stu-id="b243e-111">Select **New registration**</span></span>
* <span data-ttu-id="b243e-112">Bir **ad** girin.</span><span class="sxs-lookup"><span data-stu-id="b243e-112">Enter a **Name** .</span></span>
* <span data-ttu-id="b243e-113">**Desteklenen hesap türleri** için bir seçenek belirleyin.</span><span class="sxs-lookup"><span data-stu-id="b243e-113">Select an option for **Supported account types** .</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
  * <span data-ttu-id="b243e-114">`MicrosoftAccount`Paket, varsayılan olarak "herhangi bir kuruluş dizinindeki hesaplar" veya "herhangi bir kurumsal dizin ve Microsoft hesabı Içindeki hesaplar" seçenekleri kullanılarak oluşturulan uygulama kayıtlarını destekler.</span><span class="sxs-lookup"><span data-stu-id="b243e-114">The `MicrosoftAccount` package supports App Registrations created using "Accounts in any organizational directory" or "Accounts in any organizational directory and Microsoft accounts" options by default.</span></span>
  * <span data-ttu-id="b243e-115">Diğer seçenekleri kullanmak için, `AuthorizationEndpoint` `TokenEndpoint` `MicrosoftAccountOptions` Microsoft hesabı kimlik doğrulamasını, oluşturulduktan sonra uygulama kaydının **uç noktalar** sayfasında görüntülenir ( **genel bakış** sayfasındaki uç noktalar ' a tıklayarak kullanılabilir).</span><span class="sxs-lookup"><span data-stu-id="b243e-115">To use other options, set `AuthorizationEndpoint` and `TokenEndpoint` members of `MicrosoftAccountOptions` used to initialize the Microsoft Account authentication to the URLs displayed on **Endpoints** page of the App Registration after it is created (available by clicking Endpoints on the **Overview** page).</span></span>
* <span data-ttu-id="b243e-116">**Yeniden yönlendirme URI 'si** altında, eklenen geliştirme URL 'nizi girin `/signin-microsoft` .</span><span class="sxs-lookup"><span data-stu-id="b243e-116">Under **Redirect URI** , enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="b243e-117">Örneğin, `https://localhost:5001/signin-microsoft`.</span><span class="sxs-lookup"><span data-stu-id="b243e-117">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="b243e-118">Bu örnekte daha sonra yapılandırılan Microsoft kimlik doğrulama şeması, `/signin-microsoft` OAuth akışını uygulamak için rotadaki istekleri otomatik olarak işleymeyecektir.</span><span class="sxs-lookup"><span data-stu-id="b243e-118">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="b243e-119">**Kaydol** ' u seçin</span><span class="sxs-lookup"><span data-stu-id="b243e-119">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="b243e-120">İstemci parolası oluştur</span><span class="sxs-lookup"><span data-stu-id="b243e-120">Create client secret</span></span>

* <span data-ttu-id="b243e-121">Sol bölmede **sertifikalar & gizlilikler** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="b243e-121">In the left pane, select **Certificates & secrets** .</span></span>
* <span data-ttu-id="b243e-122">**İstemci gizli** dizileri altında **yeni istemci parolası** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="b243e-122">Under **Client secrets** , select **New client secret**</span></span>

  * <span data-ttu-id="b243e-123">İstemci parolası için bir açıklama ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b243e-123">Add a description for the client secret.</span></span>
  * <span data-ttu-id="b243e-124">**Ekle** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="b243e-124">Select the **Add** button.</span></span>

* <span data-ttu-id="b243e-125">**İstemci** gizli dizileri altında, istemci parolasının değerini kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="b243e-125">Under **Client secrets** , copy the value of the client secret.</span></span>

<span data-ttu-id="b243e-126">URI segmenti, `/signin-microsoft` Microsoft kimlik doğrulama sağlayıcısı 'nın varsayılan geri çağırması olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="b243e-126">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="b243e-127">Microsoft kimlik doğrulama ara yazılımını, [Microsoftaccountoptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) sınıfının devralınmış [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) özelliği aracılığıyla YAPıLANDıRıRKEN varsayılan geri çağırma URI 'sini değiştirebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b243e-127">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="b243e-128">Microsoft istemci KIMLIĞINI ve gizli anahtarını depolayın</span><span class="sxs-lookup"><span data-stu-id="b243e-128">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="b243e-129">[Gizli yönetici](xref:security/app-secrets)ile MICROSOFT istemci kimliği ve gizli anahtar değerleri gibi hassas ayarları depolayın.</span><span class="sxs-lookup"><span data-stu-id="b243e-129">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="b243e-130">Bu örnek için aşağıdaki adımları kullanın:</span><span class="sxs-lookup"><span data-stu-id="b243e-130">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="b243e-131">[Gizli depolamayı etkinleştirme](xref:security/app-secrets#enable-secret-storage)konusundaki yönergeler temelinde projeyi gizli depolama için başlatın.</span><span class="sxs-lookup"><span data-stu-id="b243e-131">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="b243e-132">Gizli anahtar ayarlarını gizli anahtarlar ve gizli anahtarlar ile yerel gizli dizi deposunda `Authentication:Microsoft:ClientId` depolayın `Authentication:Microsoft:ClientSecret` :</span><span class="sxs-lookup"><span data-stu-id="b243e-132">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="b243e-133">Microsoft hesabı kimlik doğrulamasını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b243e-133">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="b243e-134">Microsoft hesabı hizmetini öğesine ekleyin `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b243e-134">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="b243e-135">Microsoft hesabı kimlik doğrulaması tarafından desteklenen yapılandırma seçenekleri hakkında daha fazla bilgi için bkz. [Microsoftaccountoptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API başvurusu.</span><span class="sxs-lookup"><span data-stu-id="b243e-135">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="b243e-136">Bu, Kullanıcı hakkında farklı bilgiler istemek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b243e-136">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="b243e-137">Microsoft hesabıyla oturum açın hesabı</span><span class="sxs-lookup"><span data-stu-id="b243e-137">Sign in with Microsoft Account</span></span>

<span data-ttu-id="b243e-138">Uygulamayı çalıştırın ve **oturum aç** ' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="b243e-138">Run the app and click **Log in** .</span></span> <span data-ttu-id="b243e-139">Microsoft ile oturum açma seçeneği görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="b243e-139">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="b243e-140">Microsoft 'a tıkladığınızda, kimlik doğrulaması için Microsoft 'a yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b243e-140">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="b243e-141">Microsoft hesabınızla oturum açtıktan sonra uygulamanın bilgilerinize erişmesine izin vermek isteyip istemediğiniz sorulur:</span><span class="sxs-lookup"><span data-stu-id="b243e-141">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="b243e-142">**Evet** ' e dokunduktan sonra, e-postanızı ayarlayabileceğiniz Web sitesine geri yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b243e-142">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="b243e-143">Şu anda Microsoft kimlik bilgilerinizi kullanarak oturum açtınız:</span><span class="sxs-lookup"><span data-stu-id="b243e-143">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="b243e-144">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="b243e-144">Troubleshooting</span></span>

* <span data-ttu-id="b243e-145">Microsoft hesabı sağlayıcısı sizi bir oturum açma hatası sayfasına yönlendirirse, `#` URI 'deki (hashtag) hata başlığına ve açıklama sorgu dizesi parametrelerine göz önüne alın.</span><span class="sxs-lookup"><span data-stu-id="b243e-145">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="b243e-146">Hata iletisi Microsoft kimlik doğrulamasıyla ilgili bir sorun olduğunu gösteriyorsa, en sık karşılaşılan neden, uygulama URI 'niz **Web** platformu Için belirtilen **yeniden yönlendirme URI** 'lerinden hiçbiriyle eşleşmemedir.</span><span class="sxs-lookup"><span data-stu-id="b243e-146">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="b243e-147">Identity `services.AddIdentity` ' De çağırarak yapılandırılmamışsa `ConfigureServices` , kimlik doğrulamaya çalışmak ArgumentException ile sonuçlanır *: ' signınscheme ' seçeneği sağlanmalıdır* .</span><span class="sxs-lookup"><span data-stu-id="b243e-147">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided* .</span></span> <span data-ttu-id="b243e-148">Bu örnekte kullanılan proje şablonu bunun yapılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="b243e-148">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="b243e-149">Site veritabanı ilk geçiş uygulanarak oluşturulmadıysa, *istek hatasını Işlerken bir veritabanı işlemi başarısız* olur.</span><span class="sxs-lookup"><span data-stu-id="b243e-149">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="b243e-150">Veritabanını oluşturmak için **geçişleri Uygula** ' ya dokunun ve hatanın ötesinde devam etmek için yenileyin.</span><span class="sxs-lookup"><span data-stu-id="b243e-150">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b243e-151">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="b243e-151">Next steps</span></span>

* <span data-ttu-id="b243e-152">Bu makale, Microsoft ile nasıl kimlik doğrulayacağınızı gösterdi.</span><span class="sxs-lookup"><span data-stu-id="b243e-152">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="b243e-153">[Önceki sayfada](xref:security/authentication/social/index)listelenen diğer sağlayıcılarla kimlik doğrulaması yapmak için benzer bir yaklaşımı izleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b243e-153">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="b243e-154">Web sitenizi Azure Web App 'e yayımladığınızda, Microsoft Geliştirici Portalında yeni bir istemci gizli dizileri oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b243e-154">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="b243e-155">`Authentication:Microsoft:ClientId`Ve `Authentication:Microsoft:ClientSecret` Azure Portal uygulama ayarları olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b243e-155">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="b243e-156">Yapılandırma sistemi, ortam değişkenlerinden anahtarları okumak üzere ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="b243e-156">The configuration system is set up to read keys from environment variables.</span></span>
