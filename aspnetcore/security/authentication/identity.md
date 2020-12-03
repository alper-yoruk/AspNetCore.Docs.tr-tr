---
title: ASP.NET Core giriş Identity
author: rick-anderson
description: IdentityASP.NET Core bir uygulamayla kullanın. Parola gereksinimlerini (RequireDigit, RequiredLength, RequiredUniqueChars ve daha fazlasını) ayarlamayı öğrenin.
ms.author: riande
ms.date: 7/15/2020
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
uid: security/authentication/identity
ms.openlocfilehash: ad4184fce494ba06acf7e583a42a54d04d37ea20
ms.sourcegitcommit: 92439194682dc788b8b5b3a08bd2184dc00e200b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96556651"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a><span data-ttu-id="c14e6-104">ASP.NET Core giriş Identity</span><span class="sxs-lookup"><span data-stu-id="c14e6-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c14e6-105">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c14e6-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c14e6-106">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="c14e6-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="c14e6-107">, Kullanıcı arabirimi (UI) oturum açma işlevselliğini destekleyen bir API 'dir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="c14e6-108">Kullanıcıları, parolaları, profil verilerini, rolleri, talepleri, belirteçleri, e-posta onayını ve daha fazlasını yönetir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="c14e6-109">Kullanıcılar, içinde depolanan oturum açma bilgilerini içeren bir hesap oluşturabilir Identity veya bir dış oturum açma sağlayıcısı kullanabilirler.</span><span class="sxs-lookup"><span data-stu-id="c14e6-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="c14e6-110">Desteklenen dış oturum açma sağlayıcıları [Facebook, Google, Microsoft hesabı ve Twitter](xref:security/authentication/social/index)içerir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="c14e6-111">[ Identity Kaynak kodu](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) GitHub ' da kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="c14e6-112">[Yapı Identity İskelesi](xref:security/authentication/scaffold-identity) ve şablon etkileşimini gözden geçirmek için oluşturulan dosyaları görüntüleyin Identity .</span><span class="sxs-lookup"><span data-stu-id="c14e6-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="c14e6-113">Identity genellikle kullanıcı adlarını, parolaları ve profil verilerini depolamak için bir SQL Server veritabanı kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="c14e6-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="c14e6-114">Alternatif olarak, başka bir kalıcı mağaza da kullanılabilir, örneğin Azure Tablo depolaması.</span><span class="sxs-lookup"><span data-stu-id="c14e6-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="c14e6-115">Bu konu başlığında, Identity bir kullanıcıyı kaydetmek, oturum açmak ve oturumu kapatmak için kullanmayı öğreneceksiniz.</span><span class="sxs-lookup"><span data-stu-id="c14e6-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="c14e6-116">Note: Şablonlar Kullanıcı adı ve e-postayı kullanıcılar için aynı olarak değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="c14e6-117">Kullanan uygulamaları oluşturma hakkında daha ayrıntılı yönergeler için Identity bkz. [sonraki adımlar](#next).</span><span class="sxs-lookup"><span data-stu-id="c14e6-117">For more detailed instructions about creating apps that use Identity, see [Next Steps](#next).</span></span>

<span data-ttu-id="c14e6-118">[Microsoft Identity platform](/azure/active-directory/develop/) :</span><span class="sxs-lookup"><span data-stu-id="c14e6-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="c14e6-119">Azure Active Directory (Azure AD) geliştirici platformunun bir evrimi.</span><span class="sxs-lookup"><span data-stu-id="c14e6-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="c14e6-120">İlgisiz ASP.NET Core Identity .</span><span class="sxs-lookup"><span data-stu-id="c14e6-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="c14e6-121">Örnek kodu ([indirme](xref:index#how-to-download-a-sample)) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) .</span><span class="sxs-lookup"><span data-stu-id="c14e6-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="c14e6-122">Kimlik doğrulamasıyla bir Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="c14e6-122">Create a Web app with authentication</span></span>

<span data-ttu-id="c14e6-123">Bireysel kullanıcı hesaplarıyla bir ASP.NET Core Web uygulaması projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c14e6-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c14e6-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c14e6-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c14e6-125">**Dosya** > **Yeni** > **Proje**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c14e6-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="c14e6-126">**ASP.NET Core Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="c14e6-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="c14e6-127">Projeyi Proje **WebApp1** aynı ad alanına sahip olacak şekilde adlandırın.</span><span class="sxs-lookup"><span data-stu-id="c14e6-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="c14e6-128">**Tamam**'a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c14e6-128">Click **OK**.</span></span>
* <span data-ttu-id="c14e6-129">Bir ASP.NET Core **Web uygulaması** seçip **kimlik doğrulamasını Değiştir**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="c14e6-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="c14e6-130">**Bireysel kullanıcı hesapları** ' nı seçip **Tamam**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c14e6-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c14e6-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c14e6-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="c14e6-132">Yukarıdaki komut, Razor SQLite kullanarak bir Web uygulaması oluşturur.</span><span class="sxs-lookup"><span data-stu-id="c14e6-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="c14e6-133">LocalDB ile Web uygulaması oluşturmak için şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c14e6-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="c14e6-134">Oluşturulan proje [ASP.NET Core Identity](xref:security/authentication/identity) bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="c14e6-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="c14e6-135">Identity Razor Sınıf kitaplığı, alanı ile uç noktaları kullanıma sunar `Identity` .</span><span class="sxs-lookup"><span data-stu-id="c14e6-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="c14e6-136">Örnek:</span><span class="sxs-lookup"><span data-stu-id="c14e6-136">For example:</span></span>

* <span data-ttu-id="c14e6-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="c14e6-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="c14e6-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="c14e6-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="c14e6-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="c14e6-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="c14e6-140">Geçişleri Uygula</span><span class="sxs-lookup"><span data-stu-id="c14e6-140">Apply migrations</span></span>

<span data-ttu-id="c14e6-141">Veritabanını başlatmak için geçişleri uygulayın.</span><span class="sxs-lookup"><span data-stu-id="c14e6-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c14e6-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c14e6-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c14e6-143">Paket Yöneticisi konsolunda aşağıdaki komutu çalıştırın (PMC):</span><span class="sxs-lookup"><span data-stu-id="c14e6-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="c14e6-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c14e6-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c14e6-145">SQLite kullanılırken geçişler Bu adımda gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="c14e6-146">LocalDB için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="c14e6-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="c14e6-147">Test kaydı ve oturum açma</span><span class="sxs-lookup"><span data-stu-id="c14e6-147">Test Register and Login</span></span>

<span data-ttu-id="c14e6-148">Uygulamayı çalıştırın ve bir Kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="c14e6-148">Run the app and register a user.</span></span> <span data-ttu-id="c14e6-149">Ekran boyutunuza bağlı olarak, **kayıt** ve **oturum açma** bağlantılarını görmek için gezinti geçiş düğmesini seçmeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="c14e6-150">IdentityHizmetleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="c14e6-150">Configure Identity services</span></span>

<span data-ttu-id="c14e6-151">Hizmetler ' de eklenir `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c14e6-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="c14e6-152">Tipik model, tüm yöntemleri çağırmalıdır `Add{Service}` ve sonra tüm `services.Configure{Service}` yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="c14e6-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="c14e6-153">Önceki vurgulanan kod Identity varsayılan seçenek değerleriyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="c14e6-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="c14e6-154">Hizmetler, [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama için kullanılabilir hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="c14e6-155">Identity çağırarak etkindir <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="c14e6-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="c14e6-156">`UseAuthentication` istek ardışık düzenine kimlik doğrulama [ara yazılımı](xref:fundamentals/middleware/index) ekler.</span><span class="sxs-lookup"><span data-stu-id="c14e6-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](identity/sample/WebApp5x/Startup.cs?name=snippet_configureservices&highlight=12-99)]

<span data-ttu-id="c14e6-157">Önceki kod Identity varsayılan seçenek değerleriyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="c14e6-157">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="c14e6-158">Hizmetler, [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama için kullanılabilir hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-158">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="c14e6-159">Identity[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)çağırarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-159">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="c14e6-160">`UseAuthentication` istek ardışık düzenine kimlik doğrulama [ara yazılımı](xref:fundamentals/middleware/index) ekler.</span><span class="sxs-lookup"><span data-stu-id="c14e6-160">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp5x/Startup.cs?name=snippet_configure&highlight=19)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c14e6-161">Şablon tarafından oluşturulan uygulama [Yetkilendirme](xref:security/authorization/secure-data)kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="c14e6-161">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="c14e6-162">`app.UseAuthorization` , uygulamanın yetkilendirme eklemesi için doğru sırada eklendiğinden emin olmak için dahil edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-162">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="c14e6-163">`UseRouting`, `UseAuthentication` , `UseAuthorization` ve `UseEndpoints` Önceki kodda gösterilen sırada çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="c14e6-163">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="c14e6-164">Ve hakkında daha fazla bilgi için `IdentityOptions` `Startup` , bkz <xref:Microsoft.AspNetCore.Identity.IdentityOptions> . ve [uygulama başlatma](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="c14e6-164">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="c14e6-165">Scafkatlama kaydı, oturum açma, oturum kapatma ve RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="c14e6-165">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c14e6-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c14e6-166">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c14e6-167">,, `Register` `Login` `LogOut` Ve `RegisterConfirmation` dosyalarını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c14e6-167">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="c14e6-168">Bu bölümde gösterilen kodu oluşturmak için, [Yetkilendirme talimatlarına Razor sahip bir projede Scaffold kimliğini](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) izleyin.</span><span class="sxs-lookup"><span data-stu-id="c14e6-168">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c14e6-169">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c14e6-169">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c14e6-170">Projeyi **WebApp1** adıyla oluşturduysanız aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c14e6-170">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="c14e6-171">Aksi takdirde, için doğru ad alanını kullanın `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="c14e6-171">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="c14e6-172">PowerShell, bir komut ayırıcısı olarak noktalı virgül kullanır.</span><span class="sxs-lookup"><span data-stu-id="c14e6-172">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="c14e6-173">PowerShell kullanırken, dosya listesinde noktalı virgül karakterini kaçış veya dosya listesini, yukarıdaki örnekte gösterildiği gibi çift tırnak içine koyun.</span><span class="sxs-lookup"><span data-stu-id="c14e6-173">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="c14e6-174">Yapı iskelesi hakkında daha fazla bilgi için Identity bkz. [ Razor kimlik doğrulama ile bir projede yapı iskelesi kimliği](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="c14e6-174">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="c14e6-175">Kaydı İncele</span><span class="sxs-lookup"><span data-stu-id="c14e6-175">Examine Register</span></span>

<span data-ttu-id="c14e6-176">Bir kullanıcı sayfadaki **Kaydet** düğmesine tıkladığında `Register` `RegisterModel.OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="c14e6-176">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="c14e6-177">Kullanıcı, nesnesi üzerinde [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) tarafından oluşturulmuştur `_userManager` :</span><span class="sxs-lookup"><span data-stu-id="c14e6-177">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="c14e6-178">Oturum açma</span><span class="sxs-lookup"><span data-stu-id="c14e6-178">Log in</span></span>

<span data-ttu-id="c14e6-179">Oturum açma formu şu durumlarda görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="c14e6-179">The Login form is displayed when:</span></span>

* <span data-ttu-id="c14e6-180">**Oturum aç** bağlantısı seçilidir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-180">The **Log in** link is selected.</span></span>
* <span data-ttu-id="c14e6-181">Kullanıcı, erişim yetkisi olmayan **veya** sistem tarafından kimliği doğrulanmamış olan sınırlı bir sayfaya erişmeyi dener.</span><span class="sxs-lookup"><span data-stu-id="c14e6-181">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="c14e6-182">Oturum açma sayfasındaki form gönderildiğinde, `OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="c14e6-182">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="c14e6-183">`PasswordSignInAsync` , nesne üzerinde çağrılır `_signInManager` .</span><span class="sxs-lookup"><span data-stu-id="c14e6-183">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="c14e6-184">Yetkilendirme kararlarının nasıl yapılacağı hakkında bilgi için bkz <xref:security/authorization/introduction> ..</span><span class="sxs-lookup"><span data-stu-id="c14e6-184">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="c14e6-185">Oturumu Kapat</span><span class="sxs-lookup"><span data-stu-id="c14e6-185">Log out</span></span>

<span data-ttu-id="c14e6-186">**Oturumu kapatma** bağlantısı `LogoutModel.OnPost` eylemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="c14e6-186">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="c14e6-187">Önceki kodda, `return RedirectToPage();` tarayıcının yeni bir istek gerçekleştirmesini ve Kullanıcı kimliğinin güncelleştirilmesini sağlamak için kodun yeniden yönlendirme olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-187">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="c14e6-188">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) , kullanıcının bir içinde depolanan taleplerini temizler cookie .</span><span class="sxs-lookup"><span data-stu-id="c14e6-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="c14e6-189">*Sayfa/paylaşılan/_LoginPartial. cshtml*'de gönderi belirtildi:</span><span class="sxs-lookup"><span data-stu-id="c14e6-189">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a><span data-ttu-id="c14e6-190">Sınamanız Identity</span><span class="sxs-lookup"><span data-stu-id="c14e6-190">Test Identity</span></span>

<span data-ttu-id="c14e6-191">Varsayılan Web projesi şablonları, giriş sayfalarına anonim erişime izin verir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-191">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="c14e6-192">Test etmek için şunları Identity ekleyin [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="c14e6-192">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="c14e6-193">Oturumunuz açık ise oturumu kapatın. Uygulamayı çalıştırın ve **Gizlilik** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="c14e6-193">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="c14e6-194">Oturum açma sayfasına yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c14e6-194">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="c14e6-195">Explorer Identity</span><span class="sxs-lookup"><span data-stu-id="c14e6-195">Explore Identity</span></span>

<span data-ttu-id="c14e6-196">IdentityDaha ayrıntılı incelemek için:</span><span class="sxs-lookup"><span data-stu-id="c14e6-196">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="c14e6-197">Tam kimlik UI kaynağı oluşturma</span><span class="sxs-lookup"><span data-stu-id="c14e6-197">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="c14e6-198">Her sayfanın kaynağını inceleyin ve hata ayıklayıcıda ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="c14e6-198">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="c14e6-199">Identity Bileşenleri</span><span class="sxs-lookup"><span data-stu-id="c14e6-199">Identity Components</span></span>

<span data-ttu-id="c14e6-200">Tüm Identity bağımlı NuGet paketleri [ASP.NET Core paylaşılan çerçevesine](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)dahildir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-200">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="c14e6-201">İçin birincil paket Identity [Microsoft. aspnetcore Identity .](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)..</span><span class="sxs-lookup"><span data-stu-id="c14e6-201">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="c14e6-202">Bu paket, için temel arabirim kümesini içerir ASP.NET Core Identity ve tarafından dahildir `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="c14e6-202">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-no-locaspnet-core-identity"></a><span data-ttu-id="c14e6-203">Geçiş yapılıyor ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="c14e6-203">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="c14e6-204">Mevcut deponuzu geçirme hakkında daha fazla bilgi ve yönergeler için Identity bkz. [geçiş kimlik Identity doğrulaması ve ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="c14e6-204">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="c14e6-205">Parola gücünü ayarlama</span><span class="sxs-lookup"><span data-stu-id="c14e6-205">Setting password strength</span></span>

<span data-ttu-id="c14e6-206">Minimum parola gereksinimlerini ayarlayan bir örnek için bkz. [yapılandırma](#pw) .</span><span class="sxs-lookup"><span data-stu-id="c14e6-206">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="c14e6-207">AddDefault Identity ve EkleIdentity</span><span class="sxs-lookup"><span data-stu-id="c14e6-207">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="c14e6-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> ASP.NET Core 2,1 ' de tanıtılmıştı.</span><span class="sxs-lookup"><span data-stu-id="c14e6-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="c14e6-209">Çağırma `AddDefaultIdentity` , aşağıdakileri çağırmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="c14e6-209">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="c14e6-210">Daha fazla bilgi için bkz. [ADDDEFAULT Identity kaynağı](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="c14e6-210">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="c14e6-211">Statik varlıkların yayımlanmasını Engelle Identity</span><span class="sxs-lookup"><span data-stu-id="c14e6-211">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="c14e6-212">Statik Identity varlıkların (Kullanıcı arabirimi için stil sayfaları ve JavaScript dosyaları Identity ) Web köküne yayımlanmasını engellemek için aşağıdaki `ResolveStaticWebAssetsInputsDependsOn` özelliği ve `RemoveIdentityAssets` hedefi uygulamanın proje dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="c14e6-212">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="c14e6-213">Sonraki Adımlar</span><span class="sxs-lookup"><span data-stu-id="c14e6-213">Next Steps</span></span>

* <span data-ttu-id="c14e6-214">[ASP.NET Core Identity kaynak kodu](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="c14e6-214">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="c14e6-215">SQLite kullanarak yapılandırma hakkında bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/5131) bakın Identity .</span><span class="sxs-lookup"><span data-stu-id="c14e6-215">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="c14e6-216">Yapılandırma Identity</span><span class="sxs-lookup"><span data-stu-id="c14e6-216">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c14e6-217">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c14e6-217">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c14e6-218">ASP.NET Core Identity , ASP.NET Core uygulamalarına oturum açma işlevselliği ekleyen bir üyelik sistemidir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-218">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="c14e6-219">Kullanıcılar, içinde depolanan oturum açma bilgilerini içeren bir hesap oluşturabilir Identity veya bir dış oturum açma sağlayıcısı kullanabilirler.</span><span class="sxs-lookup"><span data-stu-id="c14e6-219">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="c14e6-220">Desteklenen dış oturum açma sağlayıcıları [Facebook, Google, Microsoft hesabı ve Twitter](xref:security/authentication/social/index)içerir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-220">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="c14e6-221">Identity Kullanıcı adlarını, parolaları ve profil verilerini depolamak için bir SQL Server veritabanı kullanılarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-221">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="c14e6-222">Alternatif olarak, başka bir kalıcı mağaza da kullanılabilir, örneğin Azure Tablo depolaması.</span><span class="sxs-lookup"><span data-stu-id="c14e6-222">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="c14e6-223">Örnek kodu ([indirme](xref:index#how-to-download-a-sample)) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) .</span><span class="sxs-lookup"><span data-stu-id="c14e6-223">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="c14e6-224">Bu konu başlığında, Identity bir kullanıcıyı kaydetmek, oturum açmak ve oturumu kapatmak için kullanmayı öğreneceksiniz.</span><span class="sxs-lookup"><span data-stu-id="c14e6-224">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="c14e6-225">Kullanan uygulamalar oluşturma hakkında daha ayrıntılı yönergeler için Identity , bu makalenin sonundaki sonraki adımlar bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="c14e6-225">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="c14e6-226">AddDefault Identity ve EkleIdentity</span><span class="sxs-lookup"><span data-stu-id="c14e6-226">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="c14e6-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> ASP.NET Core 2,1 ' de tanıtılmıştı.</span><span class="sxs-lookup"><span data-stu-id="c14e6-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="c14e6-228">Çağırma `AddDefaultIdentity` , aşağıdakileri çağırmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="c14e6-228">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="c14e6-229">Daha fazla bilgi için bkz. [ADDDEFAULT Identity kaynağı](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="c14e6-229">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="c14e6-230">Kimlik doğrulamasıyla bir Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="c14e6-230">Create a Web app with authentication</span></span>

<span data-ttu-id="c14e6-231">Bireysel kullanıcı hesaplarıyla bir ASP.NET Core Web uygulaması projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="c14e6-231">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c14e6-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c14e6-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="c14e6-233">**Dosya** > **Yeni** > **Proje**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="c14e6-233">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="c14e6-234">**ASP.NET Core Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="c14e6-234">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="c14e6-235">Projeyi Proje **WebApp1** aynı ad alanına sahip olacak şekilde adlandırın.</span><span class="sxs-lookup"><span data-stu-id="c14e6-235">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="c14e6-236">**Tamam**'a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c14e6-236">Click **OK**.</span></span>
* <span data-ttu-id="c14e6-237">Bir ASP.NET Core **Web uygulaması** seçip **kimlik doğrulamasını Değiştir**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="c14e6-237">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="c14e6-238">**Bireysel kullanıcı hesapları** ' nı seçip **Tamam**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="c14e6-238">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c14e6-239">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c14e6-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="c14e6-240">Oluşturulan proje [ASP.NET Core Identity](xref:security/authentication/identity) bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak sağlanır.</span><span class="sxs-lookup"><span data-stu-id="c14e6-240">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="c14e6-241">Identity Razor Sınıf kitaplığı, alanı ile uç noktaları kullanıma sunar `Identity` .</span><span class="sxs-lookup"><span data-stu-id="c14e6-241">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="c14e6-242">Örnek:</span><span class="sxs-lookup"><span data-stu-id="c14e6-242">For example:</span></span>

* <span data-ttu-id="c14e6-243">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="c14e6-243">/Identity/Account/Login</span></span>
* <span data-ttu-id="c14e6-244">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="c14e6-244">/Identity/Account/Logout</span></span>
* <span data-ttu-id="c14e6-245">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="c14e6-245">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="c14e6-246">Geçişleri Uygula</span><span class="sxs-lookup"><span data-stu-id="c14e6-246">Apply migrations</span></span>

<span data-ttu-id="c14e6-247">Veritabanını başlatmak için geçişleri uygulayın.</span><span class="sxs-lookup"><span data-stu-id="c14e6-247">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c14e6-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c14e6-248">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c14e6-249">Paket Yöneticisi konsolunda aşağıdaki komutu çalıştırın (PMC):</span><span class="sxs-lookup"><span data-stu-id="c14e6-249">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="c14e6-250">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c14e6-250">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="c14e6-251">Test kaydı ve oturum açma</span><span class="sxs-lookup"><span data-stu-id="c14e6-251">Test Register and Login</span></span>

<span data-ttu-id="c14e6-252">Uygulamayı çalıştırın ve bir Kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="c14e6-252">Run the app and register a user.</span></span> <span data-ttu-id="c14e6-253">Ekran boyutunuza bağlı olarak, **kayıt** ve **oturum açma** bağlantılarını görmek için gezinti geçiş düğmesini seçmeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-253">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="c14e6-254">IdentityHizmetleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="c14e6-254">Configure Identity services</span></span>

<span data-ttu-id="c14e6-255">Hizmetler ' de eklenir `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="c14e6-255">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="c14e6-256">Tipik model, tüm yöntemleri çağırmalıdır `Add{Service}` ve sonra tüm `services.Configure{Service}` yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="c14e6-256">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

<span data-ttu-id="c14e6-257">Önceki kod Identity varsayılan seçenek değerleriyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="c14e6-257">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="c14e6-258">Hizmetler, [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama için kullanılabilir hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-258">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="c14e6-259">Identity[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)çağırarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-259">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="c14e6-260">`UseAuthentication` istek ardışık düzenine kimlik doğrulama [ara yazılımı](xref:fundamentals/middleware/index) ekler.</span><span class="sxs-lookup"><span data-stu-id="c14e6-260">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="c14e6-261">Daha fazla bilgi için bkz. [ Identity Options sınıfı](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) ve [uygulama başlatma](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="c14e6-261">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="c14e6-262">Yapı iskelesi kaydı, oturum açma ve oturum kapatma</span><span class="sxs-lookup"><span data-stu-id="c14e6-262">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="c14e6-263">Bu bölümde gösterilen kodu oluşturmak için, [Yetkilendirme talimatlarına Razor sahip bir projede Scaffold kimliğini](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) izleyin.</span><span class="sxs-lookup"><span data-stu-id="c14e6-263">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="c14e6-264">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="c14e6-264">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="c14e6-265">Kayıt, oturum açma ve oturum kapatma dosyalarını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c14e6-265">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="c14e6-266">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="c14e6-266">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="c14e6-267">Projeyi **WebApp1** adıyla oluşturduysanız aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="c14e6-267">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="c14e6-268">Aksi takdirde, için doğru ad alanını kullanın `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="c14e6-268">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="c14e6-269">PowerShell, bir komut ayırıcısı olarak noktalı virgül kullanır.</span><span class="sxs-lookup"><span data-stu-id="c14e6-269">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="c14e6-270">PowerShell kullanırken, dosya listesinde noktalı virgül karakterini kaçış veya dosya listesini, yukarıdaki örnekte gösterildiği gibi çift tırnak içine koyun.</span><span class="sxs-lookup"><span data-stu-id="c14e6-270">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="c14e6-271">Kaydı İncele</span><span class="sxs-lookup"><span data-stu-id="c14e6-271">Examine Register</span></span>

<span data-ttu-id="c14e6-272">Kullanıcı **Kaydet** bağlantısına tıkladığında `RegisterModel.OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="c14e6-272">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="c14e6-273">Kullanıcı, nesnesi üzerinde [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) tarafından oluşturulmuştur `_userManager` :</span><span class="sxs-lookup"><span data-stu-id="c14e6-273">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="c14e6-274">Kullanıcı başarıyla oluşturulduysa, Kullanıcı çağrısıyla oturum açar `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="c14e6-274">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="c14e6-275">**Note:** Kayıt sırasında anında oturum açmayı önlemeye yönelik adımlar için bkz. [Hesap onayı](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="c14e6-275">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="c14e6-276">Oturum açma</span><span class="sxs-lookup"><span data-stu-id="c14e6-276">Log in</span></span>

<span data-ttu-id="c14e6-277">Oturum açma formu şu durumlarda görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="c14e6-277">The Login form is displayed when:</span></span>

* <span data-ttu-id="c14e6-278">**Oturum aç** bağlantısı seçilidir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-278">The **Log in** link is selected.</span></span>
* <span data-ttu-id="c14e6-279">Kullanıcı, erişim yetkisi olmayan **veya** sistem tarafından kimliği doğrulanmamış olan sınırlı bir sayfaya erişmeyi dener.</span><span class="sxs-lookup"><span data-stu-id="c14e6-279">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="c14e6-280">Oturum açma sayfasındaki form gönderildiğinde, `OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="c14e6-280">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="c14e6-281">`PasswordSignInAsync` , nesne üzerinde çağrılır `_signInManager` .</span><span class="sxs-lookup"><span data-stu-id="c14e6-281">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="c14e6-282">Yetkilendirme kararlarının nasıl yapılacağı hakkında bilgi için bkz <xref:security/authorization/introduction> ..</span><span class="sxs-lookup"><span data-stu-id="c14e6-282">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="c14e6-283">Oturumu Kapat</span><span class="sxs-lookup"><span data-stu-id="c14e6-283">Log out</span></span>

<span data-ttu-id="c14e6-284">**Oturumu kapatma** bağlantısı `LogoutModel.OnPost` eylemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="c14e6-284">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="c14e6-285">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) , kullanıcının bir içinde depolanan taleplerini temizler cookie .</span><span class="sxs-lookup"><span data-stu-id="c14e6-285">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="c14e6-286">*Sayfa/paylaşılan/_LoginPartial. cshtml*'de gönderi belirtildi:</span><span class="sxs-lookup"><span data-stu-id="c14e6-286">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a><span data-ttu-id="c14e6-287">Sınamanız Identity</span><span class="sxs-lookup"><span data-stu-id="c14e6-287">Test Identity</span></span>

<span data-ttu-id="c14e6-288">Varsayılan Web projesi şablonları, giriş sayfalarına anonim erişime izin verir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-288">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="c14e6-289">Test etmek için Identity [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) Gizlilik sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="c14e6-289">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="c14e6-290">Oturumunuz açık ise oturumu kapatın. Uygulamayı çalıştırın ve **Gizlilik** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="c14e6-290">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="c14e6-291">Oturum açma sayfasına yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="c14e6-291">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="c14e6-292">Explorer Identity</span><span class="sxs-lookup"><span data-stu-id="c14e6-292">Explore Identity</span></span>

<span data-ttu-id="c14e6-293">IdentityDaha ayrıntılı incelemek için:</span><span class="sxs-lookup"><span data-stu-id="c14e6-293">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="c14e6-294">Tam kimlik UI kaynağı oluşturma</span><span class="sxs-lookup"><span data-stu-id="c14e6-294">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="c14e6-295">Her sayfanın kaynağını inceleyin ve hata ayıklayıcıda ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="c14e6-295">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="c14e6-296">Identity Bileşenleri</span><span class="sxs-lookup"><span data-stu-id="c14e6-296">Identity Components</span></span>

<span data-ttu-id="c14e6-297">Tüm Identity bağımlı NuGet paketleri [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app)'e dahildir.</span><span class="sxs-lookup"><span data-stu-id="c14e6-297">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c14e6-298">İçin birincil paket Identity [Microsoft. aspnetcore Identity .](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)..</span><span class="sxs-lookup"><span data-stu-id="c14e6-298">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="c14e6-299">Bu paket, için temel arabirim kümesini içerir ASP.NET Core Identity ve tarafından dahildir `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="c14e6-299">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-no-locaspnet-core-identity"></a><span data-ttu-id="c14e6-300">Geçiş yapılıyor ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="c14e6-300">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="c14e6-301">Mevcut deponuzu geçirme hakkında daha fazla bilgi ve yönergeler için Identity bkz. [geçiş kimlik Identity doğrulaması ve ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="c14e6-301">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="c14e6-302">Parola gücünü ayarlama</span><span class="sxs-lookup"><span data-stu-id="c14e6-302">Setting password strength</span></span>

<span data-ttu-id="c14e6-303">Minimum parola gereksinimlerini ayarlayan bir örnek için bkz. [yapılandırma](#pw) .</span><span class="sxs-lookup"><span data-stu-id="c14e6-303">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="c14e6-304">Sonraki Adımlar</span><span class="sxs-lookup"><span data-stu-id="c14e6-304">Next Steps</span></span>

* <span data-ttu-id="c14e6-305">SQLite kullanarak yapılandırma hakkında bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/5131) bakın Identity .</span><span class="sxs-lookup"><span data-stu-id="c14e6-305">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="c14e6-306">Yapılandırma Identity</span><span class="sxs-lookup"><span data-stu-id="c14e6-306">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
