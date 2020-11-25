---
title: ASP.NET Core Facebook, Google ve dış sağlayıcı kimlik doğrulaması
author: rick-anderson
description: Bu öğreticide, dış kimlik doğrulama sağlayıcılarıyla OAuth 2,0 kullanarak bir ASP.NET Core uygulamasının nasıl oluşturulacağı gösterilmektedir.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
no-loc:
- appsettings.json
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
uid: security/authentication/social/index
ms.openlocfilehash: f7bc7c6a14d014f8c3c283fb09718b9191b7b478
ms.sourcegitcommit: 3f0ad1e513296ede1bff39a05be6c278e879afed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96035833"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="186e0-103">ASP.NET Core Facebook, Google ve dış sağlayıcı kimlik doğrulaması</span><span class="sxs-lookup"><span data-stu-id="186e0-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="186e0-104">Tarafından [Valeriy Novyıtskyy](https://github.com/01binary) ve [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="186e0-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="186e0-105">Bu öğreticide, kullanıcıların dış kimlik doğrulama sağlayıcılarından kimlik bilgileriyle OAuth 2,0 kullanarak oturum açmasını sağlayan bir ASP.NET Core uygulamasının nasıl oluşturulacağı gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="186e0-105">This tutorial demonstrates how to build an ASP.NET Core app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="186e0-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins)ve [Microsoft](xref:security/authentication/microsoft-logins) sağlayıcıları aşağıdaki bölümlerde ele alınmıştır ve bu makalede oluşturulan Başlatıcı projesini kullanır.</span><span class="sxs-lookup"><span data-stu-id="186e0-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections and use the starter project created in this article.</span></span> <span data-ttu-id="186e0-107">Diğer sağlayıcılar, [Aspnet. Security. OAuth. Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) ve [Aspnet. Security. OpenID. Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers)gibi üçüncü taraf paketlerinde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="186e0-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

<span data-ttu-id="186e0-108">Kullanıcıların mevcut kimlik bilgileriyle oturum açmasını sağlama:</span><span class="sxs-lookup"><span data-stu-id="186e0-108">Enabling users to sign in with their existing credentials:</span></span>

* <span data-ttu-id="186e0-109">Kullanıcılar için kullanışlıdır.</span><span class="sxs-lookup"><span data-stu-id="186e0-109">Is convenient for the users.</span></span>
* <span data-ttu-id="186e0-110">Oturum açma işlemini üçüncü bir tarafa yönetmenin karmaşıklıklarını birçok kaydırır.</span><span class="sxs-lookup"><span data-stu-id="186e0-110">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span>

<span data-ttu-id="186e0-111">Sosyal oturumların trafik ve müşteri dönüştürmelerini nasıl ve ne şekilde, [Facebook](https://www.facebook.com/unsupportedbrowser) ve [Twitter](https://dev.twitter.com/resources/case-studies)tarafından örnek olay incelemeleri hakkında örnekler için bkz..</span><span class="sxs-lookup"><span data-stu-id="186e0-111">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="186e0-112">Yeni bir ASP.NET Core projesi oluştur</span><span class="sxs-lookup"><span data-stu-id="186e0-112">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="186e0-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="186e0-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="186e0-114">Yeni bir proje oluşturma.</span><span class="sxs-lookup"><span data-stu-id="186e0-114">Create a new project.</span></span>
* <span data-ttu-id="186e0-115">**ASP.NET Core Web uygulaması** ' nı ve **İleri ' yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="186e0-115">Select **ASP.NET Core Web Application** and **Next**.</span></span>
* <span data-ttu-id="186e0-116">Bir **Proje adı** girin ve **konumu** onaylayın veya değiştirin.</span><span class="sxs-lookup"><span data-stu-id="186e0-116">Provide a **Project name** and confirm or change the **Location**.</span></span> <span data-ttu-id="186e0-117">**Oluştur**’u seçin.</span><span class="sxs-lookup"><span data-stu-id="186e0-117">Select **Create**.</span></span>
* <span data-ttu-id="186e0-118">Açılan kutuda ASP.NET Core en son sürümünü (**ASP.NET Core {X. Y}**) seçin ve ardından **Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="186e0-118">Select the latest version of ASP.NET Core in the drop-down (**ASP.NET Core {X.Y}**), and then select **Web Application**.</span></span>
* <span data-ttu-id="186e0-119">**Kimlik doğrulaması** altında **Değiştir** ' i seçin ve kimlik doğrulamasını **bireysel kullanıcı hesapları** olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="186e0-119">Under **Authentication**, select **Change** and set the authentication to **Individual User Accounts**.</span></span> <span data-ttu-id="186e0-120">**Tamam**’ı seçin.</span><span class="sxs-lookup"><span data-stu-id="186e0-120">Select **OK**.</span></span>
* <span data-ttu-id="186e0-121">**Yeni ASP.NET Core Web uygulaması oluştur** penceresinde **Oluştur**' u seçin.</span><span class="sxs-lookup"><span data-stu-id="186e0-121">In the **Create a new ASP.NET Core Web Application** window, select **Create**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="186e0-122">Visual Studio Code/Mac için Visual Studio</span><span class="sxs-lookup"><span data-stu-id="186e0-122">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="186e0-123">Terminali açın.</span><span class="sxs-lookup"><span data-stu-id="186e0-123">Open the terminal.</span></span>  <span data-ttu-id="186e0-124">Visual Studio Code için [Tümleşik Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal)' i açabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="186e0-124">For Visual Studio Code you can open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="186e0-125">Dizinleri ( `cd` ), projeyi içerecek bir klasöre değiştirin.</span><span class="sxs-lookup"><span data-stu-id="186e0-125">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="186e0-126">Windows için şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="186e0-126">For Windows, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  <span data-ttu-id="186e0-127">MacOS ve Linux için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="186e0-127">For macOS and Linux, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * <span data-ttu-id="186e0-128">`dotnet new`Komut WebApp1 klasöründe yeni bir Razor Sayfalar projesi oluşturur. *WebApp1*</span><span class="sxs-lookup"><span data-stu-id="186e0-128">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="186e0-129">`-au Individual` Tek kimlik doğrulaması için kodu oluşturur.</span><span class="sxs-lookup"><span data-stu-id="186e0-129">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="186e0-130">`-uld` Windows için SQL Server Express basit bir sürümü olan LocalDB 'yi kullanır.</span><span class="sxs-lookup"><span data-stu-id="186e0-130">`-uld` uses LocalDB, a lightweight version of SQL Server Express for Windows.</span></span> <span data-ttu-id="186e0-131">`-uld`SQLite kullanmak için atlayın.</span><span class="sxs-lookup"><span data-stu-id="186e0-131">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="186e0-132">`code`Komutu, Visual Studio Code yeni bir örneğinde *WebApp1* klasörünü açar.</span><span class="sxs-lookup"><span data-stu-id="186e0-132">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

---

## <a name="apply-migrations"></a><span data-ttu-id="186e0-133">Geçişleri Uygula</span><span class="sxs-lookup"><span data-stu-id="186e0-133">Apply migrations</span></span>

* <span data-ttu-id="186e0-134">Uygulamayı çalıştırın ve **Kaydet** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="186e0-134">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="186e0-135">Yeni hesap için e-posta ve parolayı girip **Kaydet**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="186e0-135">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="186e0-136">Geçişleri uygulamak için yönergeleri izleyin.</span><span class="sxs-lookup"><span data-stu-id="186e0-136">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="186e0-137">Oturum açma sağlayıcıları tarafından atanan belirteçleri depolamak için SecretManager kullanın</span><span class="sxs-lookup"><span data-stu-id="186e0-137">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="186e0-138">Sosyal oturum açma sağlayıcıları, kayıt işlemi sırasında **uygulama kimliği** ve **uygulama gizli** belirteçleri atar.</span><span class="sxs-lookup"><span data-stu-id="186e0-138">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="186e0-139">Tam belirteç adları sağlayıcıya göre farklılık gösterir.</span><span class="sxs-lookup"><span data-stu-id="186e0-139">The exact token names vary by provider.</span></span> <span data-ttu-id="186e0-140">Bu belirteçler, uygulamanızın API 'lerine erişmek için kullandığı kimlik bilgilerini temsil eder.</span><span class="sxs-lookup"><span data-stu-id="186e0-140">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="186e0-141">Belirteçler, [gizli yöneticinin](xref:security/app-secrets#secret-manager)yardımıyla uygulama yapılandırmanıza bağlanabilen "Kullanıcı gizli dizileri" oluşturur.</span><span class="sxs-lookup"><span data-stu-id="186e0-141">The tokens constitute the "user secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="186e0-142">Kullanıcı gizli dizileri, belirteçlerin gibi bir yapılandırma dosyasında depolanması için daha güvenli bir alternatiftir *appsettings.json* .</span><span class="sxs-lookup"><span data-stu-id="186e0-142">User secrets are a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="186e0-143">Gizli yönetici yalnızca geliştirme amaçlıdır.</span><span class="sxs-lookup"><span data-stu-id="186e0-143">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="186e0-144">[Azure Key Vault yapılandırma sağlayıcısıyla](xref:security/key-vault-configuration)Azure test ve üretim gizli dizilerini saklayabilir ve koruyabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="186e0-144">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="186e0-145">Aşağıdaki her bir oturum açma sağlayıcısı tarafından atanan belirteçleri depolamak için [ASP.NET Core konusundaki geliştirme sırasında uygulama gizli dizileri Için güvenli depolama](xref:security/app-secrets) bölümündeki adımları izleyin.</span><span class="sxs-lookup"><span data-stu-id="186e0-145">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="186e0-146">Uygulamanız için gereken oturum açma sağlayıcılarını ayarlama</span><span class="sxs-lookup"><span data-stu-id="186e0-146">Setup login providers required by your application</span></span>

<span data-ttu-id="186e0-147">Uygulamanızı ilgili sağlayıcıları kullanacak şekilde yapılandırmak için aşağıdaki konuları kullanın:</span><span class="sxs-lookup"><span data-stu-id="186e0-147">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="186e0-148">[Facebook](xref:security/authentication/facebook-logins) yönergeleri</span><span class="sxs-lookup"><span data-stu-id="186e0-148">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="186e0-149">[Twitter](xref:security/authentication/twitter-logins) yönergeleri</span><span class="sxs-lookup"><span data-stu-id="186e0-149">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="186e0-150">[Google](xref:security/authentication/google-logins) yönergeleri</span><span class="sxs-lookup"><span data-stu-id="186e0-150">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="186e0-151">[Microsoft](xref:security/authentication/microsoft-logins) yönergeleri</span><span class="sxs-lookup"><span data-stu-id="186e0-151">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="186e0-152">[Diğer sağlayıcı](xref:security/authentication/otherlogins) yönergeleri</span><span class="sxs-lookup"><span data-stu-id="186e0-152">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="186e0-153">İsteğe bağlı olarak parola ayarla</span><span class="sxs-lookup"><span data-stu-id="186e0-153">Optionally set password</span></span>

<span data-ttu-id="186e0-154">Bir dış oturum açma sağlayıcısına kaydolduysanız, uygulamaya kayıtlı bir parolanız yok demektir.</span><span class="sxs-lookup"><span data-stu-id="186e0-154">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="186e0-155">Bu, site için bir parola oluşturup hatırlamanızı konuma almayı azaltır, ancak aynı zamanda dış oturum açma sağlayıcısına bağımlı hale getirir.</span><span class="sxs-lookup"><span data-stu-id="186e0-155">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="186e0-156">Dış oturum açma sağlayıcısı kullanılamıyorsa, Web sitesinde oturum açamazsınız.</span><span class="sxs-lookup"><span data-stu-id="186e0-156">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="186e0-157">Bir parola oluşturmak ve dış sağlayıcılar ile oturum açma işlemi sırasında ayarladığınız e-postanızı kullanarak oturum açmak için:</span><span class="sxs-lookup"><span data-stu-id="186e0-157">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="186e0-158">**Yönet** görünümüne gitmek için sağ üst köşedeki **Merhaba &lt; e-posta diğer adı &gt;** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="186e0-158">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![Web uygulaması yönetme görünümü](index/_static/pass1a.png)

* <span data-ttu-id="186e0-160">**Oluştur**’u seçin</span><span class="sxs-lookup"><span data-stu-id="186e0-160">Select **Create**</span></span>

![Parola sayfanızı ayarlama](index/_static/pass2a.png)

* <span data-ttu-id="186e0-162">Geçerli bir parola ayarlayın ve bunu kullanarak e-postalarınız ile oturum açabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="186e0-162">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="186e0-163">Sonraki adımlar</span><span class="sxs-lookup"><span data-stu-id="186e0-163">Next steps</span></span>

* <span data-ttu-id="186e0-164">Oturum açma düğmelerinin nasıl özelleştirileceği hakkında bilgi edinmek için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/10563) bakın.</span><span class="sxs-lookup"><span data-stu-id="186e0-164">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/10563) for information on how to customize the login buttons.</span></span>
* <span data-ttu-id="186e0-165">Bu makalede dış kimlik doğrulaması tanıtılmıştır ve ASP.NET Core uygulamanıza dış oturum açma bilgileri eklemek için gereken önkoşullar açıklanacaktır.</span><span class="sxs-lookup"><span data-stu-id="186e0-165">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>
* <span data-ttu-id="186e0-166">Uygulamanız için gereken sağlayıcıları için oturum açma işlemlerini yapılandırmak üzere sağlayıcıya özgü sayfalara başvurun.</span><span class="sxs-lookup"><span data-stu-id="186e0-166">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>
* <span data-ttu-id="186e0-167">Kullanıcı ve bunların erişim ve yenileme belirteçleriyle ilgili ek verileri kalıcı hale getirmek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="186e0-167">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="186e0-168">Daha fazla bilgi için bkz. <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="186e0-168">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
