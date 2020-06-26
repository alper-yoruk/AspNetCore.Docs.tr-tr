---
title: ASP.NET Core giriş Identity
author: rick-anderson
description: IdentityASP.NET Core bir uygulamayla kullanın. Parola gereksinimlerini (RequireDigit, RequiredLength, RequiredUniqueChars ve daha fazlasını) ayarlamayı öğrenin.
ms.author: riande
ms.date: 01/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: 97cbc5f68d20c06e92a56ead85c8377d4e8ef582
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85404593"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="0bd42-104">ASP.NET Core giriş Identity</span><span class="sxs-lookup"><span data-stu-id="0bd42-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0bd42-105">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0bd42-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0bd42-106">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="0bd42-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="0bd42-107">, Kullanıcı arabirimi (UI) oturum açma işlevselliğini destekleyen bir API 'dir.</span><span class="sxs-lookup"><span data-stu-id="0bd42-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="0bd42-108">Kullanıcıları, parolaları, profil verilerini, rolleri, talepleri, belirteçleri, e-posta onayını ve daha fazlasını yönetir.</span><span class="sxs-lookup"><span data-stu-id="0bd42-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="0bd42-109">Kullanıcılar, içinde depolanan oturum açma bilgilerini içeren bir hesap oluşturabilir Identity veya bir dış oturum açma sağlayıcısı kullanabilirler.</span><span class="sxs-lookup"><span data-stu-id="0bd42-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="0bd42-110">Desteklenen dış oturum açma sağlayıcıları [Facebook, Google, Microsoft hesabı ve Twitter](xref:security/authentication/social/index)içerir.</span><span class="sxs-lookup"><span data-stu-id="0bd42-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="0bd42-111">[ Identity Kaynak kodu](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) GitHub ' da kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="0bd42-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="0bd42-112">[Yapı Identity İskelesi](xref:security/authentication/scaffold-identity) ve şablon etkileşimini gözden geçirmek için oluşturulan dosyaları görüntüleyin Identity .</span><span class="sxs-lookup"><span data-stu-id="0bd42-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

Identity<span data-ttu-id="0bd42-113">genellikle kullanıcı adlarını, parolaları ve profil verilerini depolamak için bir SQL Server veritabanı kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="0bd42-113"> is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="0bd42-114">Alternatif olarak, başka bir kalıcı mağaza da kullanılabilir, örneğin Azure Tablo depolaması.</span><span class="sxs-lookup"><span data-stu-id="0bd42-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="0bd42-115">Bu konu başlığında, Identity bir kullanıcıyı kaydetmek, oturum açmak ve oturumu kapatmak için kullanmayı öğreneceksiniz.</span><span class="sxs-lookup"><span data-stu-id="0bd42-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="0bd42-116">Note: Şablonlar Kullanıcı adı ve e-postayı kullanıcılar için aynı olarak değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="0bd42-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="0bd42-117">Kullanan uygulamalar oluşturma hakkında daha ayrıntılı yönergeler için Identity , bu makalenin sonundaki sonraki adımlar bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0bd42-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="0bd42-118">[Microsoft Identity platform](/azure/active-directory/develop/) :</span><span class="sxs-lookup"><span data-stu-id="0bd42-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="0bd42-119">Azure Active Directory (Azure AD) geliştirici platformunun bir evrimi.</span><span class="sxs-lookup"><span data-stu-id="0bd42-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="0bd42-120">ASP.NET Core ilgisi yoktur Identity .</span><span class="sxs-lookup"><span data-stu-id="0bd42-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="0bd42-121">Örnek kodu ([indirme)](xref:index#how-to-download-a-sample) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) .</span><span class="sxs-lookup"><span data-stu-id="0bd42-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="0bd42-122">Kimlik doğrulamasıyla bir Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="0bd42-122">Create a Web app with authentication</span></span>

<span data-ttu-id="0bd42-123">Bireysel kullanıcı hesaplarıyla bir ASP.NET Core Web uygulaması projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0bd42-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0bd42-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0bd42-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0bd42-125">**Dosya** > **Yeni** > **Proje**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0bd42-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="0bd42-126">**ASP.NET Core Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="0bd42-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="0bd42-127">Projeyi Proje **WebApp1** aynı ad alanına sahip olacak şekilde adlandırın.</span><span class="sxs-lookup"><span data-stu-id="0bd42-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="0bd42-128">**Tamam**'a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="0bd42-128">Click **OK**.</span></span>
* <span data-ttu-id="0bd42-129">Bir ASP.NET Core **Web uygulaması**seçip **kimlik doğrulamasını Değiştir**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="0bd42-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="0bd42-130">**Bireysel kullanıcı hesapları** ' nı seçip **Tamam**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="0bd42-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0bd42-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0bd42-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="0bd42-132">Yukarıdaki komut, Razor SQLite kullanarak bir Web uygulaması oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0bd42-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="0bd42-133">LocalDB ile Web uygulaması oluşturmak için şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="0bd42-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="0bd42-134">Oluşturulan proje bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core Identity ](xref:security/authentication/identity) sağlar.</span><span class="sxs-lookup"><span data-stu-id="0bd42-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="0bd42-135">Identity Razor Sınıf kitaplığı, alanı ile uç noktaları kullanıma sunar `Identity` .</span><span class="sxs-lookup"><span data-stu-id="0bd42-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="0bd42-136">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="0bd42-136">For example:</span></span>

* <span data-ttu-id="0bd42-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="0bd42-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="0bd42-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="0bd42-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="0bd42-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="0bd42-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="0bd42-140">Geçişleri Uygula</span><span class="sxs-lookup"><span data-stu-id="0bd42-140">Apply migrations</span></span>

<span data-ttu-id="0bd42-141">Veritabanını başlatmak için geçişleri uygulayın.</span><span class="sxs-lookup"><span data-stu-id="0bd42-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0bd42-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0bd42-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0bd42-143">Paket Yöneticisi konsolunda aşağıdaki komutu çalıştırın (PMC):</span><span class="sxs-lookup"><span data-stu-id="0bd42-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="0bd42-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0bd42-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0bd42-145">SQLite kullanılırken geçişler Bu adımda gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="0bd42-145">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="0bd42-146">LocalDB için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="0bd42-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="0bd42-147">Test kaydı ve oturum açma</span><span class="sxs-lookup"><span data-stu-id="0bd42-147">Test Register and Login</span></span>

<span data-ttu-id="0bd42-148">Uygulamayı çalıştırın ve bir Kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="0bd42-148">Run the app and register a user.</span></span> <span data-ttu-id="0bd42-149">Ekran boyutunuza bağlı olarak, **kayıt** ve **oturum açma** bağlantılarını görmek için gezinti geçiş düğmesini seçmeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="0bd42-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="0bd42-150">IdentityHizmetleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="0bd42-150">Configure Identity services</span></span>

<span data-ttu-id="0bd42-151">Hizmetler ' de eklenir `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0bd42-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="0bd42-152">Tipik model, tüm yöntemleri çağırmalıdır `Add{Service}` ve sonra tüm `services.Configure{Service}` yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="0bd42-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="0bd42-153">Önceki vurgulanan kod Identity varsayılan seçenek değerleriyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="0bd42-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="0bd42-154">Hizmetler, [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama için kullanılabilir hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="0bd42-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="0bd42-155">çağırarak etkindir <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="0bd42-155"> is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="0bd42-156">`UseAuthentication`istek ardışık düzenine kimlik doğrulama [ara yazılımı](xref:fundamentals/middleware/index) ekler.</span><span class="sxs-lookup"><span data-stu-id="0bd42-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="0bd42-157">Şablon tarafından oluşturulan uygulama [Yetkilendirme](xref:security/authorization/secure-data)kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="0bd42-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="0bd42-158">`app.UseAuthorization`, uygulamanın yetkilendirme eklemesi için doğru sırada eklendiğinden emin olmak için dahil edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="0bd42-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="0bd42-159">`UseRouting`, `UseAuthentication` , `UseAuthorization` ve `UseEndpoints` Önceki kodda gösterilen sırada çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0bd42-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="0bd42-160">Ve hakkında daha fazla bilgi için `IdentityOptions` `Startup` , bkz <xref:Microsoft.AspNetCore.Identity.IdentityOptions> . ve [uygulama başlatma](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="0bd42-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="0bd42-161">Yapı iskelesi kaydı, oturum açma ve oturum kapatma</span><span class="sxs-lookup"><span data-stu-id="0bd42-161">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0bd42-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0bd42-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0bd42-163">Kayıt, oturum açma ve oturum kapatma dosyalarını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0bd42-163">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="0bd42-164">Bu bölümde gösterilen kodu oluşturmak için, [Yetkilendirme talimatlarına Razor sahip bir projede Scaffold kimliğini](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) izleyin.</span><span class="sxs-lookup"><span data-stu-id="0bd42-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0bd42-165">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0bd42-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0bd42-166">Projeyi **WebApp1**adıyla oluşturduysanız aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="0bd42-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="0bd42-167">Aksi takdirde, için doğru ad alanını kullanın `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="0bd42-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="0bd42-168">PowerShell, bir komut ayırıcısı olarak noktalı virgül kullanır.</span><span class="sxs-lookup"><span data-stu-id="0bd42-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="0bd42-169">PowerShell kullanırken, dosya listesinde noktalı virgül karakterini kaçış veya dosya listesini, yukarıdaki örnekte gösterildiği gibi çift tırnak içine koyun.</span><span class="sxs-lookup"><span data-stu-id="0bd42-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="0bd42-170">Yapı iskelesi hakkında daha fazla bilgi için Identity bkz. [ Razor kimlik doğrulama ile bir projede yapı iskelesi kimliği](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="0bd42-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="0bd42-171">Kaydı İncele</span><span class="sxs-lookup"><span data-stu-id="0bd42-171">Examine Register</span></span>

<span data-ttu-id="0bd42-172">Kullanıcı **Kaydet** bağlantısına tıkladığında `RegisterModel.OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="0bd42-172">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="0bd42-173">Kullanıcı, nesnesi üzerinde [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) tarafından oluşturulmuştur `_userManager` :</span><span class="sxs-lookup"><span data-stu-id="0bd42-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="0bd42-174">Kullanıcı başarıyla oluşturulduysa, Kullanıcı çağrısıyla oturum açar `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="0bd42-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="0bd42-175">Kayıt sırasında anında oturum açmayı önlemeye yönelik adımlar için bkz. [Hesap onayı](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="0bd42-175">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="0bd42-176">Oturum açma</span><span class="sxs-lookup"><span data-stu-id="0bd42-176">Log in</span></span>

<span data-ttu-id="0bd42-177">Oturum açma formu şu durumlarda görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="0bd42-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="0bd42-178">**Oturum aç** bağlantısı seçilidir.</span><span class="sxs-lookup"><span data-stu-id="0bd42-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="0bd42-179">Kullanıcı, erişim yetkisi olmayan **veya** sistem tarafından kimliği doğrulanmamış olan sınırlı bir sayfaya erişmeyi dener.</span><span class="sxs-lookup"><span data-stu-id="0bd42-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="0bd42-180">Oturum açma sayfasındaki form gönderildiğinde, `OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="0bd42-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="0bd42-181">`PasswordSignInAsync`, nesne üzerinde çağrılır `_signInManager` .</span><span class="sxs-lookup"><span data-stu-id="0bd42-181">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="0bd42-182">Yetkilendirme kararlarının nasıl yapılacağı hakkında bilgi için bkz <xref:security/authorization/introduction> ..</span><span class="sxs-lookup"><span data-stu-id="0bd42-182">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="0bd42-183">Oturumu Kapat</span><span class="sxs-lookup"><span data-stu-id="0bd42-183">Log out</span></span>

<span data-ttu-id="0bd42-184">**Oturumu kapatma** bağlantısı `LogoutModel.OnPost` eylemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="0bd42-184">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="0bd42-185">Önceki kodda, `return RedirectToPage();` tarayıcının yeni bir istek gerçekleştirmesini ve Kullanıcı kimliğinin güncelleştirilmesini sağlamak için kodun yeniden yönlendirme olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0bd42-185">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="0bd42-186">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) , kullanıcının tanımlama bilgisinde depolanan taleplerini temizler.</span><span class="sxs-lookup"><span data-stu-id="0bd42-186">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="0bd42-187">*Sayfa/paylaşılan/_LoginPartial. cshtml*'de gönderi belirtildi:</span><span class="sxs-lookup"><span data-stu-id="0bd42-187">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="0bd42-188">SınamanızIdentity</span><span class="sxs-lookup"><span data-stu-id="0bd42-188">Test Identity</span></span>

<span data-ttu-id="0bd42-189">Varsayılan Web projesi şablonları, giriş sayfalarına anonim erişime izin verir.</span><span class="sxs-lookup"><span data-stu-id="0bd42-189">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="0bd42-190">Test etmek için şunları Identity ekleyin [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="0bd42-190">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="0bd42-191">Oturumunuz açık ise oturumu kapatın. Uygulamayı çalıştırın ve **Gizlilik** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="0bd42-191">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="0bd42-192">Oturum açma sayfasına yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0bd42-192">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="0bd42-193">ExplorerIdentity</span><span class="sxs-lookup"><span data-stu-id="0bd42-193">Explore Identity</span></span>

<span data-ttu-id="0bd42-194">IdentityDaha ayrıntılı incelemek için:</span><span class="sxs-lookup"><span data-stu-id="0bd42-194">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="0bd42-195">Tam kimlik UI kaynağı oluşturma</span><span class="sxs-lookup"><span data-stu-id="0bd42-195">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="0bd42-196">Her sayfanın kaynağını inceleyin ve hata ayıklayıcıda ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="0bd42-196">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="0bd42-197">Bileşenleri</span><span class="sxs-lookup"><span data-stu-id="0bd42-197"> Components</span></span>

<span data-ttu-id="0bd42-198">Tüm Identity bağımlı NuGet paketleri [ASP.NET Core paylaşılan çerçevesine](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)dahildir.</span><span class="sxs-lookup"><span data-stu-id="0bd42-198">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="0bd42-199">İçin birincil paket Identity [Microsoft. aspnetcore Identity .](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)..</span><span class="sxs-lookup"><span data-stu-id="0bd42-199">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="0bd42-200">Bu paket, ASP.NET Core için temel arabirim kümesini içerir Identity ve tarafından dahildir `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="0bd42-200">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="0bd42-201">ASP.NET Core geçiriliyorIdentity</span><span class="sxs-lookup"><span data-stu-id="0bd42-201">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="0bd42-202">Mevcut deponuzu geçirme hakkında daha fazla bilgi ve yönergeler için Identity bkz. [geçiş kimlik Identity doğrulaması ve ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="0bd42-202">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="0bd42-203">Parola gücünü ayarlama</span><span class="sxs-lookup"><span data-stu-id="0bd42-203">Setting password strength</span></span>

<span data-ttu-id="0bd42-204">Minimum parola gereksinimlerini ayarlayan bir örnek için bkz. [yapılandırma](#pw) .</span><span class="sxs-lookup"><span data-stu-id="0bd42-204">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="0bd42-205">Adddefaultıdentity ve AddEntity</span><span class="sxs-lookup"><span data-stu-id="0bd42-205">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="0bd42-206"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>ASP.NET Core 2,1 ' de tanıtılmıştı.</span><span class="sxs-lookup"><span data-stu-id="0bd42-206"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="0bd42-207">Çağırma `AddDefaultIdentity` , aşağıdakileri çağırmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="0bd42-207">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="0bd42-208">Daha fazla bilgi için bkz. [Adddefaultıdentity kaynağı](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="0bd42-208">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="0bd42-209">Statik varlıkların yayımlanmasını Engelle Identity</span><span class="sxs-lookup"><span data-stu-id="0bd42-209">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="0bd42-210">Statik Identity varlıkların (Kullanıcı arabirimi için stil sayfaları ve JavaScript dosyaları Identity ) Web köküne yayımlanmasını engellemek için aşağıdaki `ResolveStaticWebAssetsInputsDependsOn` özelliği ve `RemoveIdentityAssets` hedefi uygulamanın proje dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0bd42-210">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="0bd42-211">Sonraki Adımlar</span><span class="sxs-lookup"><span data-stu-id="0bd42-211">Next Steps</span></span>

* <span data-ttu-id="0bd42-212">[ASP.NET Core Identity kaynak kodu](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="0bd42-212">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="0bd42-213">SQLite kullanarak yapılandırma hakkında bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/5131) bakın Identity .</span><span class="sxs-lookup"><span data-stu-id="0bd42-213">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="0bd42-214">[YapılandırmaIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="0bd42-214">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0bd42-215">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0bd42-215">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0bd42-216">ASP.NET Core Identity , ASP.NET Core uygulamalara oturum açma işlevselliği ekleyen bir üyelik sistemidir.</span><span class="sxs-lookup"><span data-stu-id="0bd42-216">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="0bd42-217">Kullanıcılar, içinde depolanan oturum açma bilgilerini içeren bir hesap oluşturabilir Identity veya bir dış oturum açma sağlayıcısı kullanabilirler.</span><span class="sxs-lookup"><span data-stu-id="0bd42-217">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="0bd42-218">Desteklenen dış oturum açma sağlayıcıları [Facebook, Google, Microsoft hesabı ve Twitter](xref:security/authentication/social/index)içerir.</span><span class="sxs-lookup"><span data-stu-id="0bd42-218">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

Identity<span data-ttu-id="0bd42-219">Kullanıcı adlarını, parolaları ve profil verilerini depolamak için bir SQL Server veritabanı kullanılarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="0bd42-219"> can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="0bd42-220">Alternatif olarak, başka bir kalıcı mağaza da kullanılabilir, örneğin Azure Tablo depolaması.</span><span class="sxs-lookup"><span data-stu-id="0bd42-220">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="0bd42-221">Örnek kodu ([indirme)](xref:index#how-to-download-a-sample) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) .</span><span class="sxs-lookup"><span data-stu-id="0bd42-221">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="0bd42-222">Bu konu başlığında, Identity bir kullanıcıyı kaydetmek, oturum açmak ve oturumu kapatmak için kullanmayı öğreneceksiniz.</span><span class="sxs-lookup"><span data-stu-id="0bd42-222">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="0bd42-223">Kullanan uygulamalar oluşturma hakkında daha ayrıntılı yönergeler için Identity , bu makalenin sonundaki sonraki adımlar bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0bd42-223">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="0bd42-224">Adddefaultıdentity ve AddEntity</span><span class="sxs-lookup"><span data-stu-id="0bd42-224">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="0bd42-225"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>ASP.NET Core 2,1 ' de tanıtılmıştı.</span><span class="sxs-lookup"><span data-stu-id="0bd42-225"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="0bd42-226">Çağırma `AddDefaultIdentity` , aşağıdakileri çağırmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="0bd42-226">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="0bd42-227">Daha fazla bilgi için bkz. [Adddefaultıdentity kaynağı](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="0bd42-227">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="0bd42-228">Kimlik doğrulamasıyla bir Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="0bd42-228">Create a Web app with authentication</span></span>

<span data-ttu-id="0bd42-229">Bireysel kullanıcı hesaplarıyla bir ASP.NET Core Web uygulaması projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0bd42-229">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0bd42-230">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0bd42-230">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0bd42-231">**Dosya** > **Yeni** > **Proje**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0bd42-231">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="0bd42-232">**ASP.NET Core Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="0bd42-232">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="0bd42-233">Projeyi Proje **WebApp1** aynı ad alanına sahip olacak şekilde adlandırın.</span><span class="sxs-lookup"><span data-stu-id="0bd42-233">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="0bd42-234">**Tamam**'a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="0bd42-234">Click **OK**.</span></span>
* <span data-ttu-id="0bd42-235">Bir ASP.NET Core **Web uygulaması**seçip **kimlik doğrulamasını Değiştir**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="0bd42-235">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="0bd42-236">**Bireysel kullanıcı hesapları** ' nı seçip **Tamam**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="0bd42-236">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0bd42-237">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0bd42-237">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="0bd42-238">Oluşturulan proje bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core Identity ](xref:security/authentication/identity) sağlar.</span><span class="sxs-lookup"><span data-stu-id="0bd42-238">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="0bd42-239">Identity Razor Sınıf kitaplığı, alanı ile uç noktaları kullanıma sunar `Identity` .</span><span class="sxs-lookup"><span data-stu-id="0bd42-239">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="0bd42-240">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="0bd42-240">For example:</span></span>

* <span data-ttu-id="0bd42-241">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="0bd42-241">/Identity/Account/Login</span></span>
* <span data-ttu-id="0bd42-242">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="0bd42-242">/Identity/Account/Logout</span></span>
* <span data-ttu-id="0bd42-243">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="0bd42-243">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="0bd42-244">Geçişleri Uygula</span><span class="sxs-lookup"><span data-stu-id="0bd42-244">Apply migrations</span></span>

<span data-ttu-id="0bd42-245">Veritabanını başlatmak için geçişleri uygulayın.</span><span class="sxs-lookup"><span data-stu-id="0bd42-245">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0bd42-246">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0bd42-246">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0bd42-247">Paket Yöneticisi konsolunda aşağıdaki komutu çalıştırın (PMC):</span><span class="sxs-lookup"><span data-stu-id="0bd42-247">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="0bd42-248">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0bd42-248">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="0bd42-249">Test kaydı ve oturum açma</span><span class="sxs-lookup"><span data-stu-id="0bd42-249">Test Register and Login</span></span>

<span data-ttu-id="0bd42-250">Uygulamayı çalıştırın ve bir Kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="0bd42-250">Run the app and register a user.</span></span> <span data-ttu-id="0bd42-251">Ekran boyutunuza bağlı olarak, **kayıt** ve **oturum açma** bağlantılarını görmek için gezinti geçiş düğmesini seçmeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="0bd42-251">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="0bd42-252">IdentityHizmetleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="0bd42-252">Configure Identity services</span></span>

<span data-ttu-id="0bd42-253">Hizmetler ' de eklenir `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="0bd42-253">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="0bd42-254">Tipik model, tüm yöntemleri çağırmalıdır `Add{Service}` ve sonra tüm `services.Configure{Service}` yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="0bd42-254">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="0bd42-255">Önceki kod Identity varsayılan seçenek değerleriyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="0bd42-255">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="0bd42-256">Hizmetler, [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama için kullanılabilir hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="0bd42-256">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="0bd42-257">[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)çağırarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="0bd42-257"> is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="0bd42-258">`UseAuthentication`istek ardışık düzenine kimlik doğrulama [ara yazılımı](xref:fundamentals/middleware/index) ekler.</span><span class="sxs-lookup"><span data-stu-id="0bd42-258">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="0bd42-259">Daha fazla bilgi için bkz. [ıdentityoptions sınıfı](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) ve [uygulama başlatma](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="0bd42-259">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="0bd42-260">Yapı iskelesi kaydı, oturum açma ve oturum kapatma</span><span class="sxs-lookup"><span data-stu-id="0bd42-260">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="0bd42-261">Bu bölümde gösterilen kodu oluşturmak için, [Yetkilendirme talimatlarına Razor sahip bir projede Scaffold kimliğini](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) izleyin.</span><span class="sxs-lookup"><span data-stu-id="0bd42-261">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="0bd42-262">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0bd42-262">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0bd42-263">Kayıt, oturum açma ve oturum kapatma dosyalarını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0bd42-263">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="0bd42-264">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0bd42-264">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="0bd42-265">Projeyi **WebApp1**adıyla oluşturduysanız aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="0bd42-265">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="0bd42-266">Aksi takdirde, için doğru ad alanını kullanın `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="0bd42-266">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="0bd42-267">PowerShell, bir komut ayırıcısı olarak noktalı virgül kullanır.</span><span class="sxs-lookup"><span data-stu-id="0bd42-267">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="0bd42-268">PowerShell kullanırken, dosya listesinde noktalı virgül karakterini kaçış veya dosya listesini, yukarıdaki örnekte gösterildiği gibi çift tırnak içine koyun.</span><span class="sxs-lookup"><span data-stu-id="0bd42-268">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="0bd42-269">Kaydı İncele</span><span class="sxs-lookup"><span data-stu-id="0bd42-269">Examine Register</span></span>

<span data-ttu-id="0bd42-270">Kullanıcı **Kaydet** bağlantısına tıkladığında `RegisterModel.OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="0bd42-270">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="0bd42-271">Kullanıcı, nesnesi üzerinde [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) tarafından oluşturulmuştur `_userManager` :</span><span class="sxs-lookup"><span data-stu-id="0bd42-271">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="0bd42-272">Kullanıcı başarıyla oluşturulduysa, Kullanıcı çağrısıyla oturum açar `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="0bd42-272">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="0bd42-273">**Note:** Kayıt sırasında anında oturum açmayı önlemeye yönelik adımlar için bkz. [Hesap onayı](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="0bd42-273">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="0bd42-274">Oturum açma</span><span class="sxs-lookup"><span data-stu-id="0bd42-274">Log in</span></span>

<span data-ttu-id="0bd42-275">Oturum açma formu şu durumlarda görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="0bd42-275">The Login form is displayed when:</span></span>

* <span data-ttu-id="0bd42-276">**Oturum aç** bağlantısı seçilidir.</span><span class="sxs-lookup"><span data-stu-id="0bd42-276">The **Log in** link is selected.</span></span>
* <span data-ttu-id="0bd42-277">Kullanıcı, erişim yetkisi olmayan **veya** sistem tarafından kimliği doğrulanmamış olan sınırlı bir sayfaya erişmeyi dener.</span><span class="sxs-lookup"><span data-stu-id="0bd42-277">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="0bd42-278">Oturum açma sayfasındaki form gönderildiğinde, `OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="0bd42-278">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="0bd42-279">`PasswordSignInAsync`, nesne üzerinde çağrılır `_signInManager` .</span><span class="sxs-lookup"><span data-stu-id="0bd42-279">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="0bd42-280">Yetkilendirme kararlarının nasıl yapılacağı hakkında bilgi için bkz <xref:security/authorization/introduction> ..</span><span class="sxs-lookup"><span data-stu-id="0bd42-280">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="0bd42-281">Oturumu Kapat</span><span class="sxs-lookup"><span data-stu-id="0bd42-281">Log out</span></span>

<span data-ttu-id="0bd42-282">**Oturumu kapatma** bağlantısı `LogoutModel.OnPost` eylemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="0bd42-282">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="0bd42-283">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) , kullanıcının tanımlama bilgisinde depolanan taleplerini temizler.</span><span class="sxs-lookup"><span data-stu-id="0bd42-283">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="0bd42-284">*Sayfa/paylaşılan/_LoginPartial. cshtml*'de gönderi belirtildi:</span><span class="sxs-lookup"><span data-stu-id="0bd42-284">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="0bd42-285">SınamanızIdentity</span><span class="sxs-lookup"><span data-stu-id="0bd42-285">Test Identity</span></span>

<span data-ttu-id="0bd42-286">Varsayılan Web projesi şablonları, giriş sayfalarına anonim erişime izin verir.</span><span class="sxs-lookup"><span data-stu-id="0bd42-286">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="0bd42-287">Test etmek için Identity [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) Gizlilik sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0bd42-287">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="0bd42-288">Oturumunuz açık ise oturumu kapatın. Uygulamayı çalıştırın ve **Gizlilik** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="0bd42-288">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="0bd42-289">Oturum açma sayfasına yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0bd42-289">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="0bd42-290">ExplorerIdentity</span><span class="sxs-lookup"><span data-stu-id="0bd42-290">Explore Identity</span></span>

<span data-ttu-id="0bd42-291">IdentityDaha ayrıntılı incelemek için:</span><span class="sxs-lookup"><span data-stu-id="0bd42-291">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="0bd42-292">Tam kimlik UI kaynağı oluşturma</span><span class="sxs-lookup"><span data-stu-id="0bd42-292">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="0bd42-293">Her sayfanın kaynağını inceleyin ve hata ayıklayıcıda ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="0bd42-293">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="0bd42-294">Bileşenleri</span><span class="sxs-lookup"><span data-stu-id="0bd42-294"> Components</span></span>

<span data-ttu-id="0bd42-295">Tüm Identity bağımlı NuGet paketleri [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app)'e dahildir.</span><span class="sxs-lookup"><span data-stu-id="0bd42-295">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="0bd42-296">İçin birincil paket Identity [Microsoft. aspnetcore Identity .](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)..</span><span class="sxs-lookup"><span data-stu-id="0bd42-296">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="0bd42-297">Bu paket, ASP.NET Core için temel arabirim kümesini içerir Identity ve tarafından dahildir `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="0bd42-297">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="0bd42-298">ASP.NET Core geçiriliyorIdentity</span><span class="sxs-lookup"><span data-stu-id="0bd42-298">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="0bd42-299">Mevcut deponuzu geçirme hakkında daha fazla bilgi ve yönergeler için Identity bkz. [geçiş kimlik Identity doğrulaması ve ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="0bd42-299">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="0bd42-300">Parola gücünü ayarlama</span><span class="sxs-lookup"><span data-stu-id="0bd42-300">Setting password strength</span></span>

<span data-ttu-id="0bd42-301">Minimum parola gereksinimlerini ayarlayan bir örnek için bkz. [yapılandırma](#pw) .</span><span class="sxs-lookup"><span data-stu-id="0bd42-301">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="0bd42-302">Sonraki Adımlar</span><span class="sxs-lookup"><span data-stu-id="0bd42-302">Next Steps</span></span>

* <span data-ttu-id="0bd42-303">SQLite kullanarak yapılandırma hakkında bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/5131) bakın Identity .</span><span class="sxs-lookup"><span data-stu-id="0bd42-303">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="0bd42-304">[YapılandırmaIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="0bd42-304">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
