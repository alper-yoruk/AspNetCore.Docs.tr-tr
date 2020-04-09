---
title: ASP.NET Core'da Facebook, Google ve harici sağlayıcı kimlik doğrulaması
author: rick-anderson
description: Bu öğretici, harici kimlik doğrulama sağlayıcıları yla OAuth 2.0 kullanarak bir ASP.NET Core uygulamasının nasıl oluşturulabildiğini göstermektedir.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/authentication/social/index
ms.openlocfilehash: c698edbd85d665509366287b1dcad08e276e71cc
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78668044"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="aedd9-103">ASP.NET Core'da Facebook, Google ve harici sağlayıcı kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="aedd9-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="aedd9-104">Yazar: [Valeriy Novytskyy](https://github.com/01binary) ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="aedd9-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="aedd9-105">Bu öğretici, kullanıcıların OAuth 2.0 kullanarak harici kimlik doğrulama sağlayıcılarının kimlik bilgileriyle oturum açmalarını sağlayan bir ASP.NET Core 3.0 uygulamasının nasıl oluşturulabildiğini göstermektedir.</span><span class="sxs-lookup"><span data-stu-id="aedd9-105">This tutorial demonstrates how to build an ASP.NET Core 3.0 app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="aedd9-106">[Facebook,](xref:security/authentication/facebook-logins) [Twitter,](xref:security/authentication/twitter-logins) [Google](xref:security/authentication/google-logins)ve [Microsoft](xref:security/authentication/microsoft-logins) sağlayıcıları aşağıdaki bölümlerde ele alınır ve bu makalede oluşturulan başlangıç projesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="aedd9-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections and use the starter project created in this article.</span></span> <span data-ttu-id="aedd9-107">Diğer sağlayıcılar [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) ve [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers)gibi üçüncü taraf paketlerde mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="aedd9-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

<span data-ttu-id="aedd9-108">Kullanıcıların varolan kimlik bilgileriyle oturum açmalarını etkinleştirme:</span><span class="sxs-lookup"><span data-stu-id="aedd9-108">Enabling users to sign in with their existing credentials:</span></span>

* <span data-ttu-id="aedd9-109">Kullanıcılar için uygundur.</span><span class="sxs-lookup"><span data-stu-id="aedd9-109">Is convenient for the users.</span></span>
* <span data-ttu-id="aedd9-110">Oturum açma işlemini yönetme karmaşıklıklarının çoğunu üçüncü bir tarafa kaydırıyor.</span><span class="sxs-lookup"><span data-stu-id="aedd9-110">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span>

<span data-ttu-id="aedd9-111">Sosyal girişlerin trafiği ve müşteri dönüşümlerini nasıl yönlendirebileceğine örnekler için [Facebook](https://www.facebook.com/unsupportedbrowser) ve [Twitter'ın](https://dev.twitter.com/resources/case-studies)örnek araştırmalarına bakın.</span><span class="sxs-lookup"><span data-stu-id="aedd9-111">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="aedd9-112">Yeni bir ASP.NET Çekirdek Projesi Oluşturun</span><span class="sxs-lookup"><span data-stu-id="aedd9-112">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="aedd9-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aedd9-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="aedd9-114">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="aedd9-114">Create a new project.</span></span>
* <span data-ttu-id="aedd9-115">Core Web Application ve **NextASP.NETi** seçin. **Next**</span><span class="sxs-lookup"><span data-stu-id="aedd9-115">Select **ASP.NET Core Web Application** and **Next**.</span></span>
* <span data-ttu-id="aedd9-116">Proje **adı** sağlayın ve **Konumu**onaylayın veya değiştirin.</span><span class="sxs-lookup"><span data-stu-id="aedd9-116">Provide a **Project name** and confirm or change the **Location**.</span></span> <span data-ttu-id="aedd9-117">**Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="aedd9-117">Select **Create**.</span></span>
* <span data-ttu-id="aedd9-118">açılır **(ASP.NET Core {X.Y}**(ASP.NET Core'da ASP.NET Core'un en son sürümünü seçin ve ardından **Web Uygulamasını**seçin.</span><span class="sxs-lookup"><span data-stu-id="aedd9-118">Select the latest version of ASP.NET Core in the drop-down (**ASP.NET Core {X.Y}**), and then select **Web Application**.</span></span>
* <span data-ttu-id="aedd9-119">**Kimlik Doğrulama**altında **Değiştir'i** seçin ve kimlik doğrulamasını Bireysel **Kullanıcı Hesapları**olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="aedd9-119">Under **Authentication**, select **Change** and set the authentication to **Individual User Accounts**.</span></span> <span data-ttu-id="aedd9-120">**Tamam'ı**seçin.</span><span class="sxs-lookup"><span data-stu-id="aedd9-120">Select **OK**.</span></span>
* <span data-ttu-id="aedd9-121">Yeni **bir ASP.NET Çekirdek Web Uygulaması Oluştur** penceresinde **Oluştur'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="aedd9-121">In the **Create a new ASP.NET Core Web Application** window, select **Create**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="aedd9-122">Mac için Visual Studio Code / Visual Studio</span><span class="sxs-lookup"><span data-stu-id="aedd9-122">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="aedd9-123">Terminali aç.</span><span class="sxs-lookup"><span data-stu-id="aedd9-123">Open the terminal.</span></span>  <span data-ttu-id="aedd9-124">Visual Studio Code için [entegre terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="aedd9-124">For Visual Studio Code you can open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="aedd9-125">Dizinleri`cd`( ) projeyi içeren bir klasörle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="aedd9-125">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="aedd9-126">Windows için şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="aedd9-126">For Windows, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  <span data-ttu-id="aedd9-127">macOS ve Linux için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="aedd9-127">For macOS and Linux, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * <span data-ttu-id="aedd9-128">Komut, `dotnet new` *WebApp1* klasöründe yeni bir Razor Pages projesi oluşturur.</span><span class="sxs-lookup"><span data-stu-id="aedd9-128">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="aedd9-129">`-au Individual`Bireysel kimlik doğrulaması için kod oluşturur.</span><span class="sxs-lookup"><span data-stu-id="aedd9-129">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="aedd9-130">`-uld`Windows için SQL Server Express'in hafif bir sürümü olan LocalDB'yi kullanır.</span><span class="sxs-lookup"><span data-stu-id="aedd9-130">`-uld` uses LocalDB, a lightweight version of SQL Server Express for Windows.</span></span> <span data-ttu-id="aedd9-131">SQLite `-uld` kullanmak için atla.</span><span class="sxs-lookup"><span data-stu-id="aedd9-131">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="aedd9-132">Komut, `code` Visual Studio Code'un yeni bir örneğinde *WebApp1* klasörünü açar.</span><span class="sxs-lookup"><span data-stu-id="aedd9-132">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

---

## <a name="apply-migrations"></a><span data-ttu-id="aedd9-133">Geçişleri uygulama</span><span class="sxs-lookup"><span data-stu-id="aedd9-133">Apply migrations</span></span>

* <span data-ttu-id="aedd9-134">Uygulamayı çalıştırın ve **Kayıt bağlantısını** seçin.</span><span class="sxs-lookup"><span data-stu-id="aedd9-134">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="aedd9-135">Yeni hesabın e-postasını ve parolasını girin ve ardından **Kaydol'u**seçin.</span><span class="sxs-lookup"><span data-stu-id="aedd9-135">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="aedd9-136">Geçişleri uygulamak için yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="aedd9-136">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="aedd9-137">Giriş sağlayıcıları tarafından atanan belirteçleri depolamak için SecretManager'ı kullanma</span><span class="sxs-lookup"><span data-stu-id="aedd9-137">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="aedd9-138">Sosyal giriş sağlayıcıları kayıt işlemi sırasında **Uygulama Kimliği** ve **Uygulama Gizli** belirteçleri atar.</span><span class="sxs-lookup"><span data-stu-id="aedd9-138">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="aedd9-139">Tam belirteç adları sağlayıcıya göre değişir.</span><span class="sxs-lookup"><span data-stu-id="aedd9-139">The exact token names vary by provider.</span></span> <span data-ttu-id="aedd9-140">Bu belirteçler, uygulamanızın API'lerine erişmek için kullandığı kimlik bilgilerini temsil eder.</span><span class="sxs-lookup"><span data-stu-id="aedd9-140">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="aedd9-141">Belirteçler, [Secret Manager](xref:security/app-secrets#secret-manager)yardımıyla uygulama yapılandırmanıza bağlanabilecek "sırları" oluşturur.</span><span class="sxs-lookup"><span data-stu-id="aedd9-141">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="aedd9-142">Gizli Yönetici, belirteçleri *appsettings.json*gibi bir yapılandırma dosyasında depolamak için daha güvenli bir alternatiftir.</span><span class="sxs-lookup"><span data-stu-id="aedd9-142">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="aedd9-143">Gizli Müdür sadece geliştirme amaçlıdır.</span><span class="sxs-lookup"><span data-stu-id="aedd9-143">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="aedd9-144">Azure Test ve üretim sırlarını Azure [Anahtar Kasası yapılandırma sağlayıcısıyla](xref:security/key-vault-configuration)saklayabilir ve koruyabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="aedd9-144">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="aedd9-145">Aşağıdaki her giriş sağlayıcısı tarafından atanan belirteçleri depolamak için ASP.NET Temel konu [geliştirme uygulama sırlarını güvenli depolama](xref:security/app-secrets) adımları izleyin.</span><span class="sxs-lookup"><span data-stu-id="aedd9-145">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="aedd9-146">Uygulamanızın gerektirdiği kurulum giriş sağlayıcıları</span><span class="sxs-lookup"><span data-stu-id="aedd9-146">Setup login providers required by your application</span></span>

<span data-ttu-id="aedd9-147">Uygulamanızı ilgili sağlayıcıları kullanacak şekilde yapılandırmak için aşağıdaki konuları kullanın:</span><span class="sxs-lookup"><span data-stu-id="aedd9-147">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="aedd9-148">[Facebook](xref:security/authentication/facebook-logins) talimatları</span><span class="sxs-lookup"><span data-stu-id="aedd9-148">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="aedd9-149">[Twitter](xref:security/authentication/twitter-logins) talimatları</span><span class="sxs-lookup"><span data-stu-id="aedd9-149">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="aedd9-150">[Google](xref:security/authentication/google-logins) yönergeleri</span><span class="sxs-lookup"><span data-stu-id="aedd9-150">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="aedd9-151">[Microsoft](xref:security/authentication/microsoft-logins) yönergeleri</span><span class="sxs-lookup"><span data-stu-id="aedd9-151">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="aedd9-152">[Diğer sağlayıcı](xref:security/authentication/otherlogins) talimatları</span><span class="sxs-lookup"><span data-stu-id="aedd9-152">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="aedd9-153">İsteğe bağlı olarak parolayı ayarlayın</span><span class="sxs-lookup"><span data-stu-id="aedd9-153">Optionally set password</span></span>

<span data-ttu-id="aedd9-154">Harici bir giriş sağlayıcısına kaydolduğunuzda, uygulamaya kayıtlı bir parolanız yoktur.</span><span class="sxs-lookup"><span data-stu-id="aedd9-154">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="aedd9-155">Bu, site için bir parola oluşturmanızı ve hatırlamanızı kolaylaştırır, ancak aynı zamanda sizi harici giriş sağlayıcısına bağımlı hale getirir.</span><span class="sxs-lookup"><span data-stu-id="aedd9-155">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="aedd9-156">Harici oturum açma sağlayıcısı kullanılamıyorsa, web sitesinde oturum açamazsınız.</span><span class="sxs-lookup"><span data-stu-id="aedd9-156">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="aedd9-157">Oturum açma işlemi sırasında harici sağlayıcılarla belirlediğiniz e-postanızı kullanarak parola oluşturmak ve oturum unuzu imzalamak için:</span><span class="sxs-lookup"><span data-stu-id="aedd9-157">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="aedd9-158">**Yönet** görünümüne gitmek için sağ üst köşedeki \*\*Merhaba &lt;e-posta takma adını&gt; \*\* seçin.</span><span class="sxs-lookup"><span data-stu-id="aedd9-158">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![Web uygulaması Görünümü yönet](index/_static/pass1a.png)

* <span data-ttu-id="aedd9-160">**Oluştur**’u seçin</span><span class="sxs-lookup"><span data-stu-id="aedd9-160">Select **Create**</span></span>

![Parola sayfanızı ayarlama](index/_static/pass2a.png)

* <span data-ttu-id="aedd9-162">Geçerli bir parola ayarlayın ve bunu e-postanızla oturum kurmak için kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="aedd9-162">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="aedd9-163">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="aedd9-163">Next steps</span></span>

* <span data-ttu-id="aedd9-164">Giriş düğmelerini özelleştirme hakkında bilgi almak için [bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/10563) bakın.</span><span class="sxs-lookup"><span data-stu-id="aedd9-164">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/10563) for information on how to customize the login buttons.</span></span>
* <span data-ttu-id="aedd9-165">Bu makalede, harici kimlik doğrulaması tanıtıldı ve ASP.NET Core uygulamanıza harici oturum açmak için gereken ön koşullar açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="aedd9-165">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>
* <span data-ttu-id="aedd9-166">Uygulamanızın gerektirdiği sağlayıcılar için oturum açma işlemlerini yapılandırmak için sağlayıcıya özel sayfalara başvurun.</span><span class="sxs-lookup"><span data-stu-id="aedd9-166">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>
* <span data-ttu-id="aedd9-167">Kullanıcı ve bunların erişim ve yenileme belirteçleri hakkında ek veri kalıcı isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="aedd9-167">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="aedd9-168">Daha fazla bilgi için bkz. <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="aedd9-168">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
