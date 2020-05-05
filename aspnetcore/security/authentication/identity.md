---
title: Identity ASP.NET Core giriş
author: rick-anderson
description: ASP.NET Core Identity bir uygulamayla kullanın. Parola gereksinimlerini (RequireDigit, RequiredLength, RequiredUniqueChars ve daha fazlasını) ayarlamayı öğrenin.
ms.author: riande
ms.date: 01/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: d596a8357c5c812b94950809eedf35718328747c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777013"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="a254a-104">ASP.NET Core kimliğe giriş</span><span class="sxs-lookup"><span data-stu-id="a254a-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a254a-105">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a254a-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a254a-106">ASP.NET Core kimliği:</span><span class="sxs-lookup"><span data-stu-id="a254a-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="a254a-107">, Kullanıcı arabirimi (UI) oturum açma işlevselliğini destekleyen bir API 'dir.</span><span class="sxs-lookup"><span data-stu-id="a254a-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="a254a-108">Kullanıcıları, parolaları, profil verilerini, rolleri, talepleri, belirteçleri, e-posta onayını ve daha fazlasını yönetir.</span><span class="sxs-lookup"><span data-stu-id="a254a-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="a254a-109">Kullanıcılar, kimlik içinde depolanan oturum açma bilgilerini içeren bir hesap oluşturabilir veya bir dış oturum açma sağlayıcısı kullanabilirler.</span><span class="sxs-lookup"><span data-stu-id="a254a-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="a254a-110">Desteklenen dış oturum açma sağlayıcıları [Facebook, Google, Microsoft hesabı ve Twitter](xref:security/authentication/social/index)içerir.</span><span class="sxs-lookup"><span data-stu-id="a254a-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="a254a-111">[Kimlik kaynak kodu](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) GitHub ' da kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="a254a-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="a254a-112">Kimlik ile şablon etkileşimini gözden geçirmek için, [kimliği iskele](xref:security/authentication/scaffold-identity) geçirin ve oluşturulan dosyaları görüntüleyin.</span><span class="sxs-lookup"><span data-stu-id="a254a-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="a254a-113">Kimlik genellikle kullanıcı adlarını, parolaları ve profil verilerini depolamak için bir SQL Server veritabanı kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="a254a-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="a254a-114">Alternatif olarak, başka bir kalıcı mağaza da kullanılabilir, örneğin Azure Tablo depolaması.</span><span class="sxs-lookup"><span data-stu-id="a254a-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="a254a-115">Bu konu başlığında, bir kullanıcıyı kaydetmek, oturum açmak ve oturumu kapatmak için kimlik kullanmayı öğrenirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a254a-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="a254a-116">Note: Şablonlar Kullanıcı adı ve e-postayı kullanıcılar için aynı olarak değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="a254a-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="a254a-117">Kimlik kullanan uygulamalar oluşturma hakkında daha ayrıntılı yönergeler için, bu makalenin sonundaki sonraki adımlar bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="a254a-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="a254a-118">[Microsoft Identity platform](/azure/active-directory/develop/) :</span><span class="sxs-lookup"><span data-stu-id="a254a-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="a254a-119">Azure Active Directory (Azure AD) geliştirici platformunun bir evrimi.</span><span class="sxs-lookup"><span data-stu-id="a254a-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="a254a-120">ASP.NET Core kimlikle ilgisi yoktur.</span><span class="sxs-lookup"><span data-stu-id="a254a-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="a254a-121">Örnek kodu ([indirme)](xref:index#how-to-download-a-sample) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) .</span><span class="sxs-lookup"><span data-stu-id="a254a-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="a254a-122">Kimlik doğrulamasıyla bir Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="a254a-122">Create a Web app with authentication</span></span>

<span data-ttu-id="a254a-123">Bireysel kullanıcı hesaplarıyla bir ASP.NET Core Web uygulaması projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a254a-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a254a-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a254a-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a254a-125">**Dosya** > **Yeni** > **Proje**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a254a-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="a254a-126">**ASP.NET Core Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="a254a-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="a254a-127">Projeyi Proje **WebApp1** aynı ad alanına sahip olacak şekilde adlandırın.</span><span class="sxs-lookup"><span data-stu-id="a254a-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="a254a-128">**Tamam**'a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="a254a-128">Click **OK**.</span></span>
* <span data-ttu-id="a254a-129">Bir ASP.NET Core **Web uygulaması**seçip **kimlik doğrulamasını Değiştir**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="a254a-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="a254a-130">**Bireysel kullanıcı hesapları** ' nı seçip **Tamam**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="a254a-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a254a-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a254a-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="a254a-132">Yukarıdaki komut, SQLite kullanarak bir Razor Web uygulaması oluşturur.</span><span class="sxs-lookup"><span data-stu-id="a254a-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="a254a-133">LocalDB ile Web uygulaması oluşturmak için şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="a254a-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="a254a-134">Oluşturulan proje, [Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core kimliği](xref:security/authentication/identity) sağlar.</span><span class="sxs-lookup"><span data-stu-id="a254a-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="a254a-135">Identity Razor sınıfı kitaplığı, `Identity` alanı ile uç noktaları kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="a254a-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="a254a-136">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="a254a-136">For example:</span></span>

* <span data-ttu-id="a254a-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="a254a-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="a254a-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="a254a-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="a254a-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="a254a-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="a254a-140">Geçişleri Uygula</span><span class="sxs-lookup"><span data-stu-id="a254a-140">Apply migrations</span></span>

<span data-ttu-id="a254a-141">Veritabanını başlatmak için geçişleri uygulayın.</span><span class="sxs-lookup"><span data-stu-id="a254a-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a254a-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a254a-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a254a-143">Paket Yöneticisi konsolunda aşağıdaki komutu çalıştırın (PMC):</span><span class="sxs-lookup"><span data-stu-id="a254a-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="a254a-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a254a-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a254a-145">SQLite kullanılırken geçişler Bu adımda gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="a254a-145">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="a254a-146">LocalDB için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="a254a-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="a254a-147">Test kaydı ve oturum açma</span><span class="sxs-lookup"><span data-stu-id="a254a-147">Test Register and Login</span></span>

<span data-ttu-id="a254a-148">Uygulamayı çalıştırın ve bir Kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="a254a-148">Run the app and register a user.</span></span> <span data-ttu-id="a254a-149">Ekran boyutunuza bağlı olarak, **kayıt** ve **oturum açma** bağlantılarını görmek için gezinti geçiş düğmesini seçmeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="a254a-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="a254a-150">Kimlik hizmetlerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="a254a-150">Configure Identity services</span></span>

<span data-ttu-id="a254a-151">Hizmetler ' de `ConfigureServices`eklenir.</span><span class="sxs-lookup"><span data-stu-id="a254a-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="a254a-152">Tipik model, tüm `Add{Service}` yöntemleri çağırmalıdır ve sonra tüm `services.Configure{Service}` yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="a254a-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="a254a-153">Önceki vurgulanan kod, varsayılan seçenek değerleriyle kimliği yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="a254a-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="a254a-154">Hizmetler, [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama için kullanılabilir hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="a254a-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="a254a-155">Kimlik çağırarak <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>etkindir.</span><span class="sxs-lookup"><span data-stu-id="a254a-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="a254a-156">`UseAuthentication`istek ardışık düzenine kimlik doğrulama [ara yazılımı](xref:fundamentals/middleware/index) ekler.</span><span class="sxs-lookup"><span data-stu-id="a254a-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="a254a-157">Şablon tarafından oluşturulan uygulama [Yetkilendirme](xref:security/authorization/secure-data)kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="a254a-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="a254a-158">`app.UseAuthorization`, uygulamanın yetkilendirme eklemesi için doğru sırada eklendiğinden emin olmak için dahil edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="a254a-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="a254a-159">`UseRouting``UseAuthorization` `UseEndpoints` , `UseAuthentication`, ve önceki kodda gösterilen sırada çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="a254a-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="a254a-160">Ve `IdentityOptions` `Startup`hakkında daha fazla bilgi için, <xref:Microsoft.AspNetCore.Identity.IdentityOptions> bkz. ve [uygulama başlatma](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="a254a-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="a254a-161">Yapı iskelesi kaydı, oturum açma ve oturum kapatma</span><span class="sxs-lookup"><span data-stu-id="a254a-161">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a254a-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a254a-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a254a-163">Kayıt, oturum açma ve oturum kapatma dosyalarını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a254a-163">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="a254a-164">Bu bölümde gösterilen kodu oluşturmak için, yetkilendirme yönergeleriyle [birlikte bir Razor projesinde yapı iskelesi kimliğini](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) izleyin.</span><span class="sxs-lookup"><span data-stu-id="a254a-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a254a-165">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a254a-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a254a-166">Projeyi **WebApp1**adıyla oluşturduysanız aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a254a-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="a254a-167">Aksi takdirde, için doğru ad alanını kullanın `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="a254a-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="a254a-168">PowerShell, bir komut ayırıcısı olarak noktalı virgül kullanır.</span><span class="sxs-lookup"><span data-stu-id="a254a-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="a254a-169">PowerShell kullanırken, dosya listesinde noktalı virgül karakterini kaçış veya dosya listesini, yukarıdaki örnekte gösterildiği gibi çift tırnak içine koyun.</span><span class="sxs-lookup"><span data-stu-id="a254a-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="a254a-170">Yapı iskelesi kimliği hakkında daha fazla bilgi için bkz. kimlik [doğrulama ile bir Razor projesinde yapı iskelesi kimliği](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="a254a-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="a254a-171">Kaydı İncele</span><span class="sxs-lookup"><span data-stu-id="a254a-171">Examine Register</span></span>

<span data-ttu-id="a254a-172">Kullanıcı **Kaydet** bağlantısına tıkladığında `RegisterModel.OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="a254a-172">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="a254a-173">Kullanıcı, `_userManager` nesnesi üzerinde [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="a254a-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="a254a-174">`_userManager`bağımlılık ekleme tarafından sağlanır):</span><span class="sxs-lookup"><span data-stu-id="a254a-174">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="a254a-175">Kullanıcı başarıyla oluşturulduysa, Kullanıcı çağrısıyla oturum açar `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="a254a-175">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="a254a-176">Kayıt sırasında anında oturum açmayı önlemeye yönelik adımlar için bkz. [Hesap onayı](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="a254a-176">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="a254a-177">Oturum açma</span><span class="sxs-lookup"><span data-stu-id="a254a-177">Log in</span></span>

<span data-ttu-id="a254a-178">Oturum açma formu şu durumlarda görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="a254a-178">The Login form is displayed when:</span></span>

* <span data-ttu-id="a254a-179">**Oturum aç** bağlantısı seçilidir.</span><span class="sxs-lookup"><span data-stu-id="a254a-179">The **Log in** link is selected.</span></span>
* <span data-ttu-id="a254a-180">Kullanıcı, erişim yetkisi olmayan **veya** sistem tarafından kimliği doğrulanmamış olan sınırlı bir sayfaya erişmeyi dener.</span><span class="sxs-lookup"><span data-stu-id="a254a-180">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="a254a-181">Oturum açma sayfasındaki form gönderildiğinde, `OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="a254a-181">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="a254a-182">`PasswordSignInAsync``_signInManager` nesnesi üzerinde çağrılır (bağımlılık ekleme tarafından sağlanır).</span><span class="sxs-lookup"><span data-stu-id="a254a-182">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="a254a-183">Temel `Controller` sınıf, denetleyici yöntemlerinden `User` erişilebilen bir özellik sunar.</span><span class="sxs-lookup"><span data-stu-id="a254a-183">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="a254a-184">Örneğin, yetkilendirme kararlarını numaralandırabilirsiniz `User.Claims` ve yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a254a-184">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="a254a-185">Daha fazla bilgi için bkz. <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="a254a-185">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="a254a-186">Oturumu Kapat</span><span class="sxs-lookup"><span data-stu-id="a254a-186">Log out</span></span>

<span data-ttu-id="a254a-187">**Oturumu kapatma** bağlantısı `LogoutModel.OnPost` eylemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="a254a-187">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="a254a-188">Önceki kodda, tarayıcının yeni bir istek `return RedirectToPage();` gerçekleştirmesini ve Kullanıcı kimliğinin güncelleştirilmesini sağlamak için kodun yeniden yönlendirme olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="a254a-188">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="a254a-189">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) , kullanıcının tanımlama bilgisinde depolanan taleplerini temizler.</span><span class="sxs-lookup"><span data-stu-id="a254a-189">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="a254a-190">*Sayfa/paylaşılan/_LoginPartial. cshtml*'de gönderi belirtildi:</span><span class="sxs-lookup"><span data-stu-id="a254a-190">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="a254a-191">Test kimliği</span><span class="sxs-lookup"><span data-stu-id="a254a-191">Test Identity</span></span>

<span data-ttu-id="a254a-192">Varsayılan Web projesi şablonları, giriş sayfalarına anonim erişime izin verir.</span><span class="sxs-lookup"><span data-stu-id="a254a-192">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="a254a-193">Kimliği test etmek için şunu [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)ekleyin:</span><span class="sxs-lookup"><span data-stu-id="a254a-193">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="a254a-194">Oturumunuz açık ise oturumu kapatın. Uygulamayı çalıştırın ve **Gizlilik** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="a254a-194">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="a254a-195">Oturum açma sayfasına yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a254a-195">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="a254a-196">Kimliği keşfet</span><span class="sxs-lookup"><span data-stu-id="a254a-196">Explore Identity</span></span>

<span data-ttu-id="a254a-197">Kimliği daha ayrıntılı incelemek için:</span><span class="sxs-lookup"><span data-stu-id="a254a-197">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="a254a-198">Tam kimlik UI kaynağı oluşturma</span><span class="sxs-lookup"><span data-stu-id="a254a-198">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="a254a-199">Her sayfanın kaynağını inceleyin ve hata ayıklayıcıda ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="a254a-199">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="a254a-200">Kimlik bileşenleri</span><span class="sxs-lookup"><span data-stu-id="a254a-200">Identity Components</span></span>

<span data-ttu-id="a254a-201">Tüm kimlik bağımlı NuGet paketleri [ASP.NET Core paylaşılan çerçevesine](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)dahildir.</span><span class="sxs-lookup"><span data-stu-id="a254a-201">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="a254a-202">Kimliğin birincil paketi [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)' dır.</span><span class="sxs-lookup"><span data-stu-id="a254a-202">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="a254a-203">Bu paket, ASP.NET Core kimliği için çekirdek arabirim kümesini içerir ve tarafından `Microsoft.AspNetCore.Identity.EntityFrameworkCore`dahildir.</span><span class="sxs-lookup"><span data-stu-id="a254a-203">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="a254a-204">ASP.NET Core kimliğe geçiriliyor</span><span class="sxs-lookup"><span data-stu-id="a254a-204">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="a254a-205">Mevcut kimlik deponuzu geçirme hakkında daha fazla bilgi ve yönergeler için bkz. [kimlik doğrulama ve kimlik geçişi](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="a254a-205">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="a254a-206">Parola gücünü ayarlama</span><span class="sxs-lookup"><span data-stu-id="a254a-206">Setting password strength</span></span>

<span data-ttu-id="a254a-207">Minimum parola gereksinimlerini ayarlayan bir örnek için bkz. [yapılandırma](#pw) .</span><span class="sxs-lookup"><span data-stu-id="a254a-207">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="a254a-208">Adddefaultıdentity ve AddEntity</span><span class="sxs-lookup"><span data-stu-id="a254a-208">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="a254a-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>ASP.NET Core 2,1 ' de tanıtılmıştı.</span><span class="sxs-lookup"><span data-stu-id="a254a-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="a254a-210">Çağırma `AddDefaultIdentity` , aşağıdakileri çağırmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="a254a-210">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="a254a-211">Daha fazla bilgi için bkz. [Adddefaultıdentity kaynağı](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="a254a-211">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="a254a-212">Statik kimlik varlıklarının yayımlanmasını engelle</span><span class="sxs-lookup"><span data-stu-id="a254a-212">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="a254a-213">Statik kimlik varlıklarının (kimlik Kullanıcı arabirimi için stil sayfaları ve JavaScript dosyaları) Web köküne yayımlanmasını engellemek için aşağıdaki `ResolveStaticWebAssetsInputsDependsOn` özelliği ve `RemoveIdentityAssets` hedefi uygulamanın proje dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="a254a-213">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="a254a-214">Sonraki Adımlar</span><span class="sxs-lookup"><span data-stu-id="a254a-214">Next Steps</span></span>

* <span data-ttu-id="a254a-215">SQLite kullanarak kimlik yapılandırma hakkında bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/5131) bakın.</span><span class="sxs-lookup"><span data-stu-id="a254a-215">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="a254a-216">Kimliği Yapılandırma</span><span class="sxs-lookup"><span data-stu-id="a254a-216">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a254a-217">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a254a-217">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a254a-218">ASP.NET Core kimlik, ASP.NET Core uygulamalara oturum açma işlevselliği ekleyen bir üyelik sistemidir.</span><span class="sxs-lookup"><span data-stu-id="a254a-218">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="a254a-219">Kullanıcılar, kimlik içinde depolanan oturum açma bilgilerini içeren bir hesap oluşturabilir veya bir dış oturum açma sağlayıcısı kullanabilirler.</span><span class="sxs-lookup"><span data-stu-id="a254a-219">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="a254a-220">Desteklenen dış oturum açma sağlayıcıları [Facebook, Google, Microsoft hesabı ve Twitter](xref:security/authentication/social/index)içerir.</span><span class="sxs-lookup"><span data-stu-id="a254a-220">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="a254a-221">Kimlik, Kullanıcı adlarını, parolaları ve profil verilerini depolamak için bir SQL Server veritabanı kullanılarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="a254a-221">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="a254a-222">Alternatif olarak, başka bir kalıcı mağaza da kullanılabilir, örneğin Azure Tablo depolaması.</span><span class="sxs-lookup"><span data-stu-id="a254a-222">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="a254a-223">Örnek kodu ([indirme)](xref:index#how-to-download-a-sample) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) .</span><span class="sxs-lookup"><span data-stu-id="a254a-223">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="a254a-224">Bu konu başlığında, bir kullanıcıyı kaydetmek, oturum açmak ve oturumu kapatmak için kimlik kullanmayı öğrenirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a254a-224">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="a254a-225">Kimlik kullanan uygulamalar oluşturma hakkında daha ayrıntılı yönergeler için, bu makalenin sonundaki sonraki adımlar bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="a254a-225">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="a254a-226">Adddefaultıdentity ve AddEntity</span><span class="sxs-lookup"><span data-stu-id="a254a-226">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="a254a-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>ASP.NET Core 2,1 ' de tanıtılmıştı.</span><span class="sxs-lookup"><span data-stu-id="a254a-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="a254a-228">Çağırma `AddDefaultIdentity` , aşağıdakileri çağırmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="a254a-228">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="a254a-229">Daha fazla bilgi için bkz. [Adddefaultıdentity kaynağı](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="a254a-229">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="a254a-230">Kimlik doğrulamasıyla bir Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="a254a-230">Create a Web app with authentication</span></span>

<span data-ttu-id="a254a-231">Bireysel kullanıcı hesaplarıyla bir ASP.NET Core Web uygulaması projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="a254a-231">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a254a-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a254a-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a254a-233">**Dosya** > **Yeni** > **Proje**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="a254a-233">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="a254a-234">**ASP.NET Core Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="a254a-234">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="a254a-235">Projeyi Proje **WebApp1** aynı ad alanına sahip olacak şekilde adlandırın.</span><span class="sxs-lookup"><span data-stu-id="a254a-235">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="a254a-236">**Tamam**'a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="a254a-236">Click **OK**.</span></span>
* <span data-ttu-id="a254a-237">Bir ASP.NET Core **Web uygulaması**seçip **kimlik doğrulamasını Değiştir**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="a254a-237">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="a254a-238">**Bireysel kullanıcı hesapları** ' nı seçip **Tamam**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="a254a-238">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a254a-239">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a254a-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="a254a-240">Oluşturulan proje, [Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core kimliği](xref:security/authentication/identity) sağlar.</span><span class="sxs-lookup"><span data-stu-id="a254a-240">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="a254a-241">Identity Razor sınıfı kitaplığı, `Identity` alanı ile uç noktaları kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="a254a-241">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="a254a-242">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="a254a-242">For example:</span></span>

* <span data-ttu-id="a254a-243">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="a254a-243">/Identity/Account/Login</span></span>
* <span data-ttu-id="a254a-244">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="a254a-244">/Identity/Account/Logout</span></span>
* <span data-ttu-id="a254a-245">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="a254a-245">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="a254a-246">Geçişleri Uygula</span><span class="sxs-lookup"><span data-stu-id="a254a-246">Apply migrations</span></span>

<span data-ttu-id="a254a-247">Veritabanını başlatmak için geçişleri uygulayın.</span><span class="sxs-lookup"><span data-stu-id="a254a-247">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a254a-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a254a-248">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a254a-249">Paket Yöneticisi konsolunda aşağıdaki komutu çalıştırın (PMC):</span><span class="sxs-lookup"><span data-stu-id="a254a-249">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="a254a-250">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a254a-250">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="a254a-251">Test kaydı ve oturum açma</span><span class="sxs-lookup"><span data-stu-id="a254a-251">Test Register and Login</span></span>

<span data-ttu-id="a254a-252">Uygulamayı çalıştırın ve bir Kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="a254a-252">Run the app and register a user.</span></span> <span data-ttu-id="a254a-253">Ekran boyutunuza bağlı olarak, **kayıt** ve **oturum açma** bağlantılarını görmek için gezinti geçiş düğmesini seçmeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="a254a-253">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="a254a-254">Kimlik hizmetlerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="a254a-254">Configure Identity services</span></span>

<span data-ttu-id="a254a-255">Hizmetler ' de `ConfigureServices`eklenir.</span><span class="sxs-lookup"><span data-stu-id="a254a-255">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="a254a-256">Tipik model, tüm `Add{Service}` yöntemleri çağırmalıdır ve sonra tüm `services.Configure{Service}` yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="a254a-256">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="a254a-257">Yukarıdaki kod, varsayılan seçenek değerleriyle kimliği yapılandırır.</span><span class="sxs-lookup"><span data-stu-id="a254a-257">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="a254a-258">Hizmetler, [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama için kullanılabilir hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="a254a-258">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="a254a-259">Kimlik, [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)çağırarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="a254a-259">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="a254a-260">`UseAuthentication`istek ardışık düzenine kimlik doğrulama [ara yazılımı](xref:fundamentals/middleware/index) ekler.</span><span class="sxs-lookup"><span data-stu-id="a254a-260">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="a254a-261">Daha fazla bilgi için bkz. [ıdentityoptions sınıfı](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) ve [uygulama başlatma](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="a254a-261">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="a254a-262">Yapı iskelesi kaydı, oturum açma ve oturum kapatma</span><span class="sxs-lookup"><span data-stu-id="a254a-262">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="a254a-263">Bu bölümde gösterilen kodu oluşturmak için, yetkilendirme yönergeleriyle [birlikte bir Razor projesinde yapı iskelesi kimliğini](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) izleyin.</span><span class="sxs-lookup"><span data-stu-id="a254a-263">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a254a-264">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a254a-264">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a254a-265">Kayıt, oturum açma ve oturum kapatma dosyalarını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a254a-265">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a254a-266">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a254a-266">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a254a-267">Projeyi **WebApp1**adıyla oluşturduysanız aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="a254a-267">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="a254a-268">Aksi takdirde, için doğru ad alanını kullanın `ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="a254a-268">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="a254a-269">PowerShell, bir komut ayırıcısı olarak noktalı virgül kullanır.</span><span class="sxs-lookup"><span data-stu-id="a254a-269">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="a254a-270">PowerShell kullanırken, dosya listesinde noktalı virgül karakterini kaçış veya dosya listesini, yukarıdaki örnekte gösterildiği gibi çift tırnak içine koyun.</span><span class="sxs-lookup"><span data-stu-id="a254a-270">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="a254a-271">Kaydı İncele</span><span class="sxs-lookup"><span data-stu-id="a254a-271">Examine Register</span></span>

<span data-ttu-id="a254a-272">Kullanıcı **Kaydet** bağlantısına tıkladığında `RegisterModel.OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="a254a-272">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="a254a-273">Kullanıcı, `_userManager` nesnesi üzerinde [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) tarafından oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="a254a-273">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="a254a-274">`_userManager`bağımlılık ekleme tarafından sağlanır):</span><span class="sxs-lookup"><span data-stu-id="a254a-274">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="a254a-275">Kullanıcı başarıyla oluşturulduysa, Kullanıcı çağrısıyla oturum açar `_signInManager.SignInAsync`.</span><span class="sxs-lookup"><span data-stu-id="a254a-275">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="a254a-276">**Note:** Kayıt sırasında anında oturum açmayı önlemeye yönelik adımlar için bkz. [Hesap onayı](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="a254a-276">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="a254a-277">Oturum açma</span><span class="sxs-lookup"><span data-stu-id="a254a-277">Log in</span></span>

<span data-ttu-id="a254a-278">Oturum açma formu şu durumlarda görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="a254a-278">The Login form is displayed when:</span></span>

* <span data-ttu-id="a254a-279">**Oturum aç** bağlantısı seçilidir.</span><span class="sxs-lookup"><span data-stu-id="a254a-279">The **Log in** link is selected.</span></span>
* <span data-ttu-id="a254a-280">Kullanıcı, erişim yetkisi olmayan **veya** sistem tarafından kimliği doğrulanmamış olan sınırlı bir sayfaya erişmeyi dener.</span><span class="sxs-lookup"><span data-stu-id="a254a-280">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="a254a-281">Oturum açma sayfasındaki form gönderildiğinde, `OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="a254a-281">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="a254a-282">`PasswordSignInAsync``_signInManager` nesnesi üzerinde çağrılır (bağımlılık ekleme tarafından sağlanır).</span><span class="sxs-lookup"><span data-stu-id="a254a-282">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="a254a-283">Temel `Controller` sınıf, denetleyici yöntemlerinden `User` erişebileceğiniz bir özellik sunar.</span><span class="sxs-lookup"><span data-stu-id="a254a-283">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="a254a-284">Örneğin, yetkilendirme kararlarını numaralandırabilirsiniz `User.Claims` ve yapabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a254a-284">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="a254a-285">Daha fazla bilgi için bkz. <xref:security/authorization/introduction>.</span><span class="sxs-lookup"><span data-stu-id="a254a-285">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="a254a-286">Oturumu Kapat</span><span class="sxs-lookup"><span data-stu-id="a254a-286">Log out</span></span>

<span data-ttu-id="a254a-287">**Oturumu kapatma** bağlantısı `LogoutModel.OnPost` eylemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="a254a-287">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="a254a-288">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) , kullanıcının tanımlama bilgisinde depolanan taleplerini temizler.</span><span class="sxs-lookup"><span data-stu-id="a254a-288">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="a254a-289">*Sayfa/paylaşılan/_LoginPartial. cshtml*'de gönderi belirtildi:</span><span class="sxs-lookup"><span data-stu-id="a254a-289">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="a254a-290">SınamanızIdentity</span><span class="sxs-lookup"><span data-stu-id="a254a-290">Test Identity</span></span>

<span data-ttu-id="a254a-291">Varsayılan Web projesi şablonları, giriş sayfalarına anonim erişime izin verir.</span><span class="sxs-lookup"><span data-stu-id="a254a-291">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="a254a-292">Test Identityetmek için Gizlilik [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="a254a-292">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="a254a-293">Oturumunuz açık ise oturumu kapatın. Uygulamayı çalıştırın ve **Gizlilik** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="a254a-293">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="a254a-294">Oturum açma sayfasına yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="a254a-294">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="a254a-295">ExplorerIdentity</span><span class="sxs-lookup"><span data-stu-id="a254a-295">Explore Identity</span></span>

<span data-ttu-id="a254a-296">Daha ayrıntılı Identity incelemek için:</span><span class="sxs-lookup"><span data-stu-id="a254a-296">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="a254a-297">Tam kimlik UI kaynağı oluşturma</span><span class="sxs-lookup"><span data-stu-id="a254a-297">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="a254a-298">Her sayfanın kaynağını inceleyin ve hata ayıklayıcıda ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="a254a-298">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="a254a-299">Bileşenleri</span><span class="sxs-lookup"><span data-stu-id="a254a-299"> Components</span></span>

<span data-ttu-id="a254a-300">Tüm Identity bağımlı NuGet paketleri [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)'e dahildir.</span><span class="sxs-lookup"><span data-stu-id="a254a-300">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="a254a-301">İçin Identity birincil paket [Microsoft. aspnetcore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)..</span><span class="sxs-lookup"><span data-stu-id="a254a-301">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="a254a-302">Bu paket, ASP.NET Core Identityiçin temel arabirim kümesini içerir ve tarafından `Microsoft.AspNetCore.Identity.EntityFrameworkCore`dahildir.</span><span class="sxs-lookup"><span data-stu-id="a254a-302">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="a254a-303">ASP.NET Core geçiriliyorIdentity</span><span class="sxs-lookup"><span data-stu-id="a254a-303">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="a254a-304">Mevcut Identity deponuzu geçirme hakkında daha fazla bilgi ve yönergeler için bkz. [geçiş kimlik Identitydoğrulaması ve ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="a254a-304">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="a254a-305">Parola gücünü ayarlama</span><span class="sxs-lookup"><span data-stu-id="a254a-305">Setting password strength</span></span>

<span data-ttu-id="a254a-306">Minimum parola gereksinimlerini ayarlayan bir örnek için bkz. [yapılandırma](#pw) .</span><span class="sxs-lookup"><span data-stu-id="a254a-306">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a254a-307">Sonraki Adımlar</span><span class="sxs-lookup"><span data-stu-id="a254a-307">Next Steps</span></span>

* <span data-ttu-id="a254a-308">SQLite kullanarak yapılandırma Identity hakkında bilgi Için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/5131) bakın.</span><span class="sxs-lookup"><span data-stu-id="a254a-308">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="a254a-309">[YapılandırmaIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="a254a-309">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
