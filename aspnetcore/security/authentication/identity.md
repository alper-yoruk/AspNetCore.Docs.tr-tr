---
title: 'ASP.NET Core giriş Identity'
author: rick-anderson
description: IdentityASP.NET Core bir uygulamayla kullanın. Parola gereksinimlerini (RequireDigit, RequiredLength, RequiredUniqueChars ve daha fazlasını) ayarlamayı öğrenin.
ms.author: riande
ms.date: 7/15/2020
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
uid: security/authentication/identity
ms.openlocfilehash: bfcef860beb07ab81dda1a10a1648491ae187bef
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052025"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a><span data-ttu-id="5c1ef-104">ASP.NET Core giriş Identity</span><span class="sxs-lookup"><span data-stu-id="5c1ef-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5c1ef-105">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5c1ef-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5c1ef-106">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="5c1ef-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="5c1ef-107">, Kullanıcı arabirimi (UI) oturum açma işlevselliğini destekleyen bir API 'dir.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="5c1ef-108">Kullanıcıları, parolaları, profil verilerini, rolleri, talepleri, belirteçleri, e-posta onayını ve daha fazlasını yönetir.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="5c1ef-109">Kullanıcılar, içinde depolanan oturum açma bilgilerini içeren bir hesap oluşturabilir Identity veya bir dış oturum açma sağlayıcısı kullanabilirler.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="5c1ef-110">Desteklenen dış oturum açma sağlayıcıları [Facebook, Google, Microsoft hesabı ve Twitter](xref:security/authentication/social/index)içerir.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="5c1ef-111">[ Identity Kaynak kodu](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) GitHub ' da kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="5c1ef-112">[Yapı Identity İskelesi](xref:security/authentication/scaffold-identity) ve şablon etkileşimini gözden geçirmek için oluşturulan dosyaları görüntüleyin Identity .</span><span class="sxs-lookup"><span data-stu-id="5c1ef-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="5c1ef-113">Identity genellikle kullanıcı adlarını, parolaları ve profil verilerini depolamak için bir SQL Server veritabanı kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="5c1ef-114">Alternatif olarak, başka bir kalıcı mağaza da kullanılabilir, örneğin Azure Tablo depolaması.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="5c1ef-115">Bu konu başlığında, Identity bir kullanıcıyı kaydetmek, oturum açmak ve oturumu kapatmak için kullanmayı öğreneceksiniz.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="5c1ef-116">Note: Şablonlar Kullanıcı adı ve e-postayı kullanıcılar için aynı olarak değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="5c1ef-117">Kullanan uygulamaları oluşturma hakkında daha ayrıntılı yönergeler için Identity bkz. [sonraki adımlar](#next).</span><span class="sxs-lookup"><span data-stu-id="5c1ef-117">For more detailed instructions about creating apps that use Identity, see [Next Steps](#next).</span></span>

<span data-ttu-id="5c1ef-118">[Microsoft Identity platform](/azure/active-directory/develop/) :</span><span class="sxs-lookup"><span data-stu-id="5c1ef-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="5c1ef-119">Azure Active Directory (Azure AD) geliştirici platformunun bir evrimi.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="5c1ef-120">İlgisiz ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="5c1ef-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="5c1ef-121">Örnek kodu ([indirme](xref:index#how-to-download-a-sample)) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) .</span><span class="sxs-lookup"><span data-stu-id="5c1ef-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="5c1ef-122">Kimlik doğrulamasıyla bir Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="5c1ef-122">Create a Web app with authentication</span></span>

<span data-ttu-id="5c1ef-123">Bireysel kullanıcı hesaplarıyla bir ASP.NET Core Web uygulaması projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c1ef-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c1ef-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5c1ef-125">**Dosya** > **Yeni** > **Proje** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-125">Select **File** > **New** > **Project** .</span></span>
* <span data-ttu-id="5c1ef-126">**ASP.NET Core Web uygulaması** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-126">Select **ASP.NET Core Web Application** .</span></span> <span data-ttu-id="5c1ef-127">Projeyi Proje **WebApp1** aynı ad alanına sahip olacak şekilde adlandırın.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="5c1ef-128">**Tamam** ’a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-128">Click **OK** .</span></span>
* <span data-ttu-id="5c1ef-129">Bir ASP.NET Core **Web uygulaması** seçip **kimlik doğrulamasını Değiştir** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-129">Select an ASP.NET Core **Web Application** , then select **Change Authentication** .</span></span>
* <span data-ttu-id="5c1ef-130">**Bireysel kullanıcı hesapları** ' nı seçip **Tamam** ' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-130">Select **Individual User Accounts** and click **OK** .</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5c1ef-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5c1ef-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="5c1ef-132">Yukarıdaki komut, Razor SQLite kullanarak bir Web uygulaması oluşturur.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="5c1ef-133">LocalDB ile Web uygulaması oluşturmak için şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5c1ef-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="5c1ef-134">Oluşturulan proje [ASP.NET Core Identity](xref:security/authentication/identity) bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="5c1ef-135">Identity Razor Sınıf kitaplığı, alanı ile uç noktaları kullanıma sunar `Identity` .</span><span class="sxs-lookup"><span data-stu-id="5c1ef-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="5c1ef-136">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="5c1ef-136">For example:</span></span>

* <span data-ttu-id="5c1ef-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="5c1ef-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="5c1ef-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="5c1ef-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="5c1ef-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="5c1ef-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="5c1ef-140">Geçişleri Uygula</span><span class="sxs-lookup"><span data-stu-id="5c1ef-140">Apply migrations</span></span>

<span data-ttu-id="5c1ef-141">Veritabanını başlatmak için geçişleri uygulayın.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c1ef-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c1ef-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5c1ef-143">Paket Yöneticisi konsolunda aşağıdaki komutu çalıştırın (PMC):</span><span class="sxs-lookup"><span data-stu-id="5c1ef-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="5c1ef-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5c1ef-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5c1ef-145">SQLite kullanılırken geçişler Bu adımda gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="5c1ef-146">LocalDB için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="5c1ef-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="5c1ef-147">Test kaydı ve oturum açma</span><span class="sxs-lookup"><span data-stu-id="5c1ef-147">Test Register and Login</span></span>

<span data-ttu-id="5c1ef-148">Uygulamayı çalıştırın ve bir Kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-148">Run the app and register a user.</span></span> <span data-ttu-id="5c1ef-149">Ekran boyutunuza bağlı olarak, **kayıt** ve **oturum açma** bağlantılarını görmek için gezinti geçiş düğmesini seçmeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="5c1ef-150">IdentityHizmetleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5c1ef-150">Configure Identity services</span></span>

<span data-ttu-id="5c1ef-151">Hizmetler ' de eklenir `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5c1ef-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="5c1ef-152">Tipik model, tüm yöntemleri çağırmalıdır `Add{Service}` ve sonra tüm `services.Configure{Service}` yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="5c1ef-153">Önceki vurgulanan kod Identity varsayılan seçenek değerleriyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="5c1ef-154">Hizmetler, [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama için kullanılabilir hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="5c1ef-155">Identity çağırarak etkindir <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="5c1ef-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="5c1ef-156">`UseAuthentication` istek ardışık düzenine kimlik doğrulama [ara yazılımı](xref:fundamentals/middleware/index) ekler.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="5c1ef-157">Şablon tarafından oluşturulan uygulama [Yetkilendirme](xref:security/authorization/secure-data)kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="5c1ef-158">`app.UseAuthorization` , uygulamanın yetkilendirme eklemesi için doğru sırada eklendiğinden emin olmak için dahil edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="5c1ef-159">`UseRouting`, `UseAuthentication` , `UseAuthorization` ve `UseEndpoints` Önceki kodda gösterilen sırada çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="5c1ef-160">Ve hakkında daha fazla bilgi için `IdentityOptions` `Startup` , bkz <xref:Microsoft.AspNetCore.Identity.IdentityOptions> . ve [uygulama başlatma](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="5c1ef-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="5c1ef-161">Scafkatlama kaydı, oturum açma, oturum kapatma ve RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="5c1ef-161">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c1ef-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c1ef-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5c1ef-163">,, `Register` `Login` `LogOut` Ve `RegisterConfirmation` dosyalarını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-163">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="5c1ef-164">Bu bölümde gösterilen kodu oluşturmak için, [Yetkilendirme talimatlarına Razor sahip bir projede Scaffold kimliğini](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) izleyin.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5c1ef-165">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5c1ef-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5c1ef-166">Projeyi **WebApp1** adıyla oluşturduysanız aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-166">If you created the project with name **WebApp1** , run the following commands.</span></span> <span data-ttu-id="5c1ef-167">Aksi takdirde, için doğru ad alanını kullanın `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="5c1ef-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="5c1ef-168">PowerShell, bir komut ayırıcısı olarak noktalı virgül kullanır.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="5c1ef-169">PowerShell kullanırken, dosya listesinde noktalı virgül karakterini kaçış veya dosya listesini, yukarıdaki örnekte gösterildiği gibi çift tırnak içine koyun.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="5c1ef-170">Yapı iskelesi hakkında daha fazla bilgi için Identity bkz. [ Razor kimlik doğrulama ile bir projede yapı iskelesi kimliği](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="5c1ef-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="5c1ef-171">Kaydı İncele</span><span class="sxs-lookup"><span data-stu-id="5c1ef-171">Examine Register</span></span>

<span data-ttu-id="5c1ef-172">Bir kullanıcı sayfadaki **Kaydet** düğmesine tıkladığında `Register` `RegisterModel.OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-172">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="5c1ef-173">Kullanıcı, nesnesi üzerinde [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) tarafından oluşturulmuştur `_userManager` :</span><span class="sxs-lookup"><span data-stu-id="5c1ef-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="5c1ef-174">Oturum açma</span><span class="sxs-lookup"><span data-stu-id="5c1ef-174">Log in</span></span>

<span data-ttu-id="5c1ef-175">Oturum açma formu şu durumlarda görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="5c1ef-175">The Login form is displayed when:</span></span>

* <span data-ttu-id="5c1ef-176">**Oturum aç** bağlantısı seçilidir.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-176">The **Log in** link is selected.</span></span>
* <span data-ttu-id="5c1ef-177">Kullanıcı, erişim yetkisi olmayan **veya** sistem tarafından kimliği doğrulanmamış olan sınırlı bir sayfaya erişmeyi dener.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-177">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="5c1ef-178">Oturum açma sayfasındaki form gönderildiğinde, `OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-178">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="5c1ef-179">`PasswordSignInAsync` , nesne üzerinde çağrılır `_signInManager` .</span><span class="sxs-lookup"><span data-stu-id="5c1ef-179">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="5c1ef-180">Yetkilendirme kararlarının nasıl yapılacağı hakkında bilgi için bkz <xref:security/authorization/introduction> ..</span><span class="sxs-lookup"><span data-stu-id="5c1ef-180">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="5c1ef-181">Oturumu Kapat</span><span class="sxs-lookup"><span data-stu-id="5c1ef-181">Log out</span></span>

<span data-ttu-id="5c1ef-182">**Oturumu kapatma** bağlantısı `LogoutModel.OnPost` eylemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-182">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="5c1ef-183">Önceki kodda, `return RedirectToPage();` tarayıcının yeni bir istek gerçekleştirmesini ve Kullanıcı kimliğinin güncelleştirilmesini sağlamak için kodun yeniden yönlendirme olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-183">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="5c1ef-184">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) , kullanıcının bir içinde depolanan taleplerini temizler cookie .</span><span class="sxs-lookup"><span data-stu-id="5c1ef-184">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="5c1ef-185">*Sayfa/paylaşılan/_LoginPartial. cshtml* 'de gönderi belirtildi:</span><span class="sxs-lookup"><span data-stu-id="5c1ef-185">Post is specified in the *Pages/Shared/_LoginPartial.cshtml* :</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a><span data-ttu-id="5c1ef-186">Sınamanız Identity</span><span class="sxs-lookup"><span data-stu-id="5c1ef-186">Test Identity</span></span>

<span data-ttu-id="5c1ef-187">Varsayılan Web projesi şablonları, giriş sayfalarına anonim erişime izin verir.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-187">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="5c1ef-188">Test etmek için şunları Identity ekleyin [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="5c1ef-188">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="5c1ef-189">Oturumunuz açık ise oturumu kapatın. Uygulamayı çalıştırın ve **Gizlilik** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-189">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="5c1ef-190">Oturum açma sayfasına yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-190">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="5c1ef-191">Explorer Identity</span><span class="sxs-lookup"><span data-stu-id="5c1ef-191">Explore Identity</span></span>

<span data-ttu-id="5c1ef-192">IdentityDaha ayrıntılı incelemek için:</span><span class="sxs-lookup"><span data-stu-id="5c1ef-192">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="5c1ef-193">Tam kimlik UI kaynağı oluşturma</span><span class="sxs-lookup"><span data-stu-id="5c1ef-193">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="5c1ef-194">Her sayfanın kaynağını inceleyin ve hata ayıklayıcıda ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-194">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="5c1ef-195">Identity Bileşenleri</span><span class="sxs-lookup"><span data-stu-id="5c1ef-195">Identity Components</span></span>

<span data-ttu-id="5c1ef-196">Tüm Identity bağımlı NuGet paketleri [ASP.NET Core paylaşılan çerçevesine](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)dahildir.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-196">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="5c1ef-197">İçin birincil paket Identity [Microsoft. aspnetcore Identity .](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)..</span><span class="sxs-lookup"><span data-stu-id="5c1ef-197">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="5c1ef-198">Bu paket, için temel arabirim kümesini içerir ASP.NET Core Identity ve tarafından dahildir `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="5c1ef-198">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-no-locaspnet-core-identity"></a><span data-ttu-id="5c1ef-199">Geçiş yapılıyor ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="5c1ef-199">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="5c1ef-200">Mevcut deponuzu geçirme hakkında daha fazla bilgi ve yönergeler için Identity bkz. [geçiş kimlik Identity doğrulaması ve ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="5c1ef-200">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="5c1ef-201">Parola gücünü ayarlama</span><span class="sxs-lookup"><span data-stu-id="5c1ef-201">Setting password strength</span></span>

<span data-ttu-id="5c1ef-202">Minimum parola gereksinimlerini ayarlayan bir örnek için bkz. [yapılandırma](#pw) .</span><span class="sxs-lookup"><span data-stu-id="5c1ef-202">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="5c1ef-203">AddDefault Identity ve EkleIdentity</span><span class="sxs-lookup"><span data-stu-id="5c1ef-203">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="5c1ef-204"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> ASP.NET Core 2,1 ' de tanıtılmıştı.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-204"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="5c1ef-205">Çağırma `AddDefaultIdentity` , aşağıdakileri çağırmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="5c1ef-205">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="5c1ef-206">Daha fazla bilgi için bkz. [ADDDEFAULT Identity kaynağı](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="5c1ef-206">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="5c1ef-207">Statik varlıkların yayımlanmasını Engelle Identity</span><span class="sxs-lookup"><span data-stu-id="5c1ef-207">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="5c1ef-208">Statik Identity varlıkların (Kullanıcı arabirimi için stil sayfaları ve JavaScript dosyaları Identity ) Web köküne yayımlanmasını engellemek için aşağıdaki `ResolveStaticWebAssetsInputsDependsOn` özelliği ve `RemoveIdentityAssets` hedefi uygulamanın proje dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="5c1ef-208">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a><span data-ttu-id="5c1ef-209">Sonraki Adımlar</span><span class="sxs-lookup"><span data-stu-id="5c1ef-209">Next Steps</span></span>

* <span data-ttu-id="5c1ef-210">[ASP.NET Core Identity kaynak kodu](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="5c1ef-210">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="5c1ef-211">SQLite kullanarak yapılandırma hakkında bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/5131) bakın Identity .</span><span class="sxs-lookup"><span data-stu-id="5c1ef-211">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="5c1ef-212">Yapılandırma Identity</span><span class="sxs-lookup"><span data-stu-id="5c1ef-212">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5c1ef-213">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5c1ef-213">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5c1ef-214">ASP.NET Core Identity , ASP.NET Core uygulamalarına oturum açma işlevselliği ekleyen bir üyelik sistemidir.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-214">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="5c1ef-215">Kullanıcılar, içinde depolanan oturum açma bilgilerini içeren bir hesap oluşturabilir Identity veya bir dış oturum açma sağlayıcısı kullanabilirler.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-215">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="5c1ef-216">Desteklenen dış oturum açma sağlayıcıları [Facebook, Google, Microsoft hesabı ve Twitter](xref:security/authentication/social/index)içerir.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-216">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="5c1ef-217">Identity Kullanıcı adlarını, parolaları ve profil verilerini depolamak için bir SQL Server veritabanı kullanılarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-217">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="5c1ef-218">Alternatif olarak, başka bir kalıcı mağaza da kullanılabilir, örneğin Azure Tablo depolaması.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-218">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="5c1ef-219">Örnek kodu ([indirme](xref:index#how-to-download-a-sample)) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) .</span><span class="sxs-lookup"><span data-stu-id="5c1ef-219">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="5c1ef-220">Bu konu başlığında, Identity bir kullanıcıyı kaydetmek, oturum açmak ve oturumu kapatmak için kullanmayı öğreneceksiniz.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-220">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="5c1ef-221">Kullanan uygulamalar oluşturma hakkında daha ayrıntılı yönergeler için Identity , bu makalenin sonundaki sonraki adımlar bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-221">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="5c1ef-222">AddDefault Identity ve EkleIdentity</span><span class="sxs-lookup"><span data-stu-id="5c1ef-222">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="5c1ef-223"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> ASP.NET Core 2,1 ' de tanıtılmıştı.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-223"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="5c1ef-224">Çağırma `AddDefaultIdentity` , aşağıdakileri çağırmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="5c1ef-224">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="5c1ef-225">Daha fazla bilgi için bkz. [ADDDEFAULT Identity kaynağı](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="5c1ef-225">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="5c1ef-226">Kimlik doğrulamasıyla bir Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="5c1ef-226">Create a Web app with authentication</span></span>

<span data-ttu-id="5c1ef-227">Bireysel kullanıcı hesaplarıyla bir ASP.NET Core Web uygulaması projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-227">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c1ef-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c1ef-228">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5c1ef-229">**Dosya** > **Yeni** > **Proje** ’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-229">Select **File** > **New** > **Project** .</span></span>
* <span data-ttu-id="5c1ef-230">**ASP.NET Core Web uygulaması** ' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-230">Select **ASP.NET Core Web Application** .</span></span> <span data-ttu-id="5c1ef-231">Projeyi Proje **WebApp1** aynı ad alanına sahip olacak şekilde adlandırın.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-231">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="5c1ef-232">**Tamam** ’a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-232">Click **OK** .</span></span>
* <span data-ttu-id="5c1ef-233">Bir ASP.NET Core **Web uygulaması** seçip **kimlik doğrulamasını Değiştir** ' i seçin.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-233">Select an ASP.NET Core **Web Application** , then select **Change Authentication** .</span></span>
* <span data-ttu-id="5c1ef-234">**Bireysel kullanıcı hesapları** ' nı seçip **Tamam** ' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-234">Select **Individual User Accounts** and click **OK** .</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5c1ef-235">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5c1ef-235">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="5c1ef-236">Oluşturulan proje [ASP.NET Core Identity](xref:security/authentication/identity) bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-236">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="5c1ef-237">Identity Razor Sınıf kitaplığı, alanı ile uç noktaları kullanıma sunar `Identity` .</span><span class="sxs-lookup"><span data-stu-id="5c1ef-237">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="5c1ef-238">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="5c1ef-238">For example:</span></span>

* <span data-ttu-id="5c1ef-239">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="5c1ef-239">/Identity/Account/Login</span></span>
* <span data-ttu-id="5c1ef-240">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="5c1ef-240">/Identity/Account/Logout</span></span>
* <span data-ttu-id="5c1ef-241">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="5c1ef-241">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="5c1ef-242">Geçişleri Uygula</span><span class="sxs-lookup"><span data-stu-id="5c1ef-242">Apply migrations</span></span>

<span data-ttu-id="5c1ef-243">Veritabanını başlatmak için geçişleri uygulayın.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-243">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c1ef-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c1ef-244">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5c1ef-245">Paket Yöneticisi konsolunda aşağıdaki komutu çalıştırın (PMC):</span><span class="sxs-lookup"><span data-stu-id="5c1ef-245">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="5c1ef-246">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5c1ef-246">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="5c1ef-247">Test kaydı ve oturum açma</span><span class="sxs-lookup"><span data-stu-id="5c1ef-247">Test Register and Login</span></span>

<span data-ttu-id="5c1ef-248">Uygulamayı çalıştırın ve bir Kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-248">Run the app and register a user.</span></span> <span data-ttu-id="5c1ef-249">Ekran boyutunuza bağlı olarak, **kayıt** ve **oturum açma** bağlantılarını görmek için gezinti geçiş düğmesini seçmeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-249">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="5c1ef-250">IdentityHizmetleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="5c1ef-250">Configure Identity services</span></span>

<span data-ttu-id="5c1ef-251">Hizmetler ' de eklenir `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="5c1ef-251">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="5c1ef-252">Tipik model, tüm yöntemleri çağırmalıdır `Add{Service}` ve sonra tüm `services.Configure{Service}` yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-252">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="5c1ef-253">Önceki kod Identity varsayılan seçenek değerleriyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-253">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="5c1ef-254">Hizmetler, [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama için kullanılabilir hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-254">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="5c1ef-255">Identity[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)çağırarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-255">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="5c1ef-256">`UseAuthentication` istek ardışık düzenine kimlik doğrulama [ara yazılımı](xref:fundamentals/middleware/index) ekler.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-256">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="5c1ef-257">Daha fazla bilgi için bkz. [ Identity Options sınıfı](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) ve [uygulama başlatma](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="5c1ef-257">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="5c1ef-258">Yapı iskelesi kaydı, oturum açma ve oturum kapatma</span><span class="sxs-lookup"><span data-stu-id="5c1ef-258">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="5c1ef-259">Bu bölümde gösterilen kodu oluşturmak için, [Yetkilendirme talimatlarına Razor sahip bir projede Scaffold kimliğini](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) izleyin.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-259">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5c1ef-260">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5c1ef-260">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5c1ef-261">Kayıt, oturum açma ve oturum kapatma dosyalarını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-261">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="5c1ef-262">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="5c1ef-262">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="5c1ef-263">Projeyi **WebApp1** adıyla oluşturduysanız aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-263">If you created the project with name **WebApp1** , run the following commands.</span></span> <span data-ttu-id="5c1ef-264">Aksi takdirde, için doğru ad alanını kullanın `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="5c1ef-264">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="5c1ef-265">PowerShell, bir komut ayırıcısı olarak noktalı virgül kullanır.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-265">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="5c1ef-266">PowerShell kullanırken, dosya listesinde noktalı virgül karakterini kaçış veya dosya listesini, yukarıdaki örnekte gösterildiği gibi çift tırnak içine koyun.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-266">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="5c1ef-267">Kaydı İncele</span><span class="sxs-lookup"><span data-stu-id="5c1ef-267">Examine Register</span></span>

<span data-ttu-id="5c1ef-268">Kullanıcı **Kaydet** bağlantısına tıkladığında `RegisterModel.OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-268">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="5c1ef-269">Kullanıcı, nesnesi üzerinde [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) tarafından oluşturulmuştur `_userManager` :</span><span class="sxs-lookup"><span data-stu-id="5c1ef-269">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="5c1ef-270">Kullanıcı başarıyla oluşturulduysa, Kullanıcı çağrısıyla oturum açar `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="5c1ef-270">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="5c1ef-271">**Note:** Kayıt sırasında anında oturum açmayı önlemeye yönelik adımlar için bkz. [Hesap onayı](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="5c1ef-271">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="5c1ef-272">Oturum açma</span><span class="sxs-lookup"><span data-stu-id="5c1ef-272">Log in</span></span>

<span data-ttu-id="5c1ef-273">Oturum açma formu şu durumlarda görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="5c1ef-273">The Login form is displayed when:</span></span>

* <span data-ttu-id="5c1ef-274">**Oturum aç** bağlantısı seçilidir.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-274">The **Log in** link is selected.</span></span>
* <span data-ttu-id="5c1ef-275">Kullanıcı, erişim yetkisi olmayan **veya** sistem tarafından kimliği doğrulanmamış olan sınırlı bir sayfaya erişmeyi dener.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-275">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="5c1ef-276">Oturum açma sayfasındaki form gönderildiğinde, `OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-276">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="5c1ef-277">`PasswordSignInAsync` , nesne üzerinde çağrılır `_signInManager` .</span><span class="sxs-lookup"><span data-stu-id="5c1ef-277">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="5c1ef-278">Yetkilendirme kararlarının nasıl yapılacağı hakkında bilgi için bkz <xref:security/authorization/introduction> ..</span><span class="sxs-lookup"><span data-stu-id="5c1ef-278">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="5c1ef-279">Oturumu Kapat</span><span class="sxs-lookup"><span data-stu-id="5c1ef-279">Log out</span></span>

<span data-ttu-id="5c1ef-280">**Oturumu kapatma** bağlantısı `LogoutModel.OnPost` eylemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-280">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="5c1ef-281">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) , kullanıcının bir içinde depolanan taleplerini temizler cookie .</span><span class="sxs-lookup"><span data-stu-id="5c1ef-281">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="5c1ef-282">*Sayfa/paylaşılan/_LoginPartial. cshtml* 'de gönderi belirtildi:</span><span class="sxs-lookup"><span data-stu-id="5c1ef-282">Post is specified in the *Pages/Shared/_LoginPartial.cshtml* :</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a><span data-ttu-id="5c1ef-283">Sınamanız Identity</span><span class="sxs-lookup"><span data-stu-id="5c1ef-283">Test Identity</span></span>

<span data-ttu-id="5c1ef-284">Varsayılan Web projesi şablonları, giriş sayfalarına anonim erişime izin verir.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-284">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="5c1ef-285">Test etmek için Identity [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) Gizlilik sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-285">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="5c1ef-286">Oturumunuz açık ise oturumu kapatın. Uygulamayı çalıştırın ve **Gizlilik** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-286">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="5c1ef-287">Oturum açma sayfasına yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-287">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="5c1ef-288">Explorer Identity</span><span class="sxs-lookup"><span data-stu-id="5c1ef-288">Explore Identity</span></span>

<span data-ttu-id="5c1ef-289">IdentityDaha ayrıntılı incelemek için:</span><span class="sxs-lookup"><span data-stu-id="5c1ef-289">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="5c1ef-290">Tam kimlik UI kaynağı oluşturma</span><span class="sxs-lookup"><span data-stu-id="5c1ef-290">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="5c1ef-291">Her sayfanın kaynağını inceleyin ve hata ayıklayıcıda ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-291">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="5c1ef-292">Identity Bileşenleri</span><span class="sxs-lookup"><span data-stu-id="5c1ef-292">Identity Components</span></span>

<span data-ttu-id="5c1ef-293">Tüm Identity bağımlı NuGet paketleri [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app)'e dahildir.</span><span class="sxs-lookup"><span data-stu-id="5c1ef-293">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="5c1ef-294">İçin birincil paket Identity [Microsoft. aspnetcore Identity .](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)..</span><span class="sxs-lookup"><span data-stu-id="5c1ef-294">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="5c1ef-295">Bu paket, için temel arabirim kümesini içerir ASP.NET Core Identity ve tarafından dahildir `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="5c1ef-295">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-no-locaspnet-core-identity"></a><span data-ttu-id="5c1ef-296">Geçiş yapılıyor ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="5c1ef-296">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="5c1ef-297">Mevcut deponuzu geçirme hakkında daha fazla bilgi ve yönergeler için Identity bkz. [geçiş kimlik Identity doğrulaması ve ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="5c1ef-297">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="5c1ef-298">Parola gücünü ayarlama</span><span class="sxs-lookup"><span data-stu-id="5c1ef-298">Setting password strength</span></span>

<span data-ttu-id="5c1ef-299">Minimum parola gereksinimlerini ayarlayan bir örnek için bkz. [yapılandırma](#pw) .</span><span class="sxs-lookup"><span data-stu-id="5c1ef-299">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5c1ef-300">Sonraki Adımlar</span><span class="sxs-lookup"><span data-stu-id="5c1ef-300">Next Steps</span></span>

* <span data-ttu-id="5c1ef-301">SQLite kullanarak yapılandırma hakkında bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/5131) bakın Identity .</span><span class="sxs-lookup"><span data-stu-id="5c1ef-301">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="5c1ef-302">Yapılandırma Identity</span><span class="sxs-lookup"><span data-stu-id="5c1ef-302">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
