---
title: ASP.NET Core giriş Identity
author: rick-anderson
description: IdentityASP.NET Core bir uygulamayla kullanın. Parola gereksinimlerini (RequireDigit, RequiredLength, RequiredUniqueChars ve daha fazlasını) ayarlamayı öğrenin.
ms.author: riande
ms.date: 7/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: dd3296db568700a363c427398f02239846a46ada
ms.sourcegitcommit: 384833762c614851db653b841cc09fbc944da463
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/17/2020
ms.locfileid: "86445443"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="d3c3c-104">ASP.NET Core giriş Identity</span><span class="sxs-lookup"><span data-stu-id="d3c3c-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d3c3c-105">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d3c3c-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d3c3c-106">ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="d3c3c-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="d3c3c-107">, Kullanıcı arabirimi (UI) oturum açma işlevselliğini destekleyen bir API 'dir.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="d3c3c-108">Kullanıcıları, parolaları, profil verilerini, rolleri, talepleri, belirteçleri, e-posta onayını ve daha fazlasını yönetir.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="d3c3c-109">Kullanıcılar, içinde depolanan oturum açma bilgilerini içeren bir hesap oluşturabilir Identity veya bir dış oturum açma sağlayıcısı kullanabilirler.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="d3c3c-110">Desteklenen dış oturum açma sağlayıcıları [Facebook, Google, Microsoft hesabı ve Twitter](xref:security/authentication/social/index)içerir.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="d3c3c-111">[ Identity Kaynak kodu](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) GitHub ' da kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="d3c3c-112">[Yapı Identity İskelesi](xref:security/authentication/scaffold-identity) ve şablon etkileşimini gözden geçirmek için oluşturulan dosyaları görüntüleyin Identity .</span><span class="sxs-lookup"><span data-stu-id="d3c3c-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

Identity<span data-ttu-id="d3c3c-113">genellikle kullanıcı adlarını, parolaları ve profil verilerini depolamak için bir SQL Server veritabanı kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-113"> is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="d3c3c-114">Alternatif olarak, başka bir kalıcı mağaza da kullanılabilir, örneğin Azure Tablo depolaması.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="d3c3c-115">Bu konu başlığında, Identity bir kullanıcıyı kaydetmek, oturum açmak ve oturumu kapatmak için kullanmayı öğreneceksiniz.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="d3c3c-116">Note: Şablonlar Kullanıcı adı ve e-postayı kullanıcılar için aynı olarak değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="d3c3c-117">Kullanan uygulamaları oluşturma hakkında daha ayrıntılı yönergeler için Identity bkz. [sonraki adımlar](#next).</span><span class="sxs-lookup"><span data-stu-id="d3c3c-117">For more detailed instructions about creating apps that use Identity, see [Next Steps](#next).</span></span>

<span data-ttu-id="d3c3c-118">[Microsoft Identity platform](/azure/active-directory/develop/) :</span><span class="sxs-lookup"><span data-stu-id="d3c3c-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="d3c3c-119">Azure Active Directory (Azure AD) geliştirici platformunun bir evrimi.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="d3c3c-120">ASP.NET Core ilgisi yoktur Identity .</span><span class="sxs-lookup"><span data-stu-id="d3c3c-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/Identity<span data-ttu-id="d3c3c-121">Server4.md)]</span><span class="sxs-lookup"><span data-stu-id="d3c3c-121">Server4.md)]</span></span>

<span data-ttu-id="d3c3c-122">Örnek kodu ([indirme)](xref:index#how-to-download-a-sample) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) .</span><span class="sxs-lookup"><span data-stu-id="d3c3c-122">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="d3c3c-123">Kimlik doğrulamasıyla bir Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="d3c3c-123">Create a Web app with authentication</span></span>

<span data-ttu-id="d3c3c-124">Bireysel kullanıcı hesaplarıyla bir ASP.NET Core Web uygulaması projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-124">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d3c3c-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d3c3c-125">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d3c3c-126">**Dosya** > **Yeni** > **Proje**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-126">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="d3c3c-127">**ASP.NET Core Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-127">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="d3c3c-128">Projeyi Proje **WebApp1** aynı ad alanına sahip olacak şekilde adlandırın.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-128">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="d3c3c-129">**Tamam** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-129">Click **OK**.</span></span>
* <span data-ttu-id="d3c3c-130">Bir ASP.NET Core **Web uygulaması**seçip **kimlik doğrulamasını Değiştir**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-130">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="d3c3c-131">**Bireysel kullanıcı hesapları** ' nı seçip **Tamam**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-131">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d3c3c-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d3c3c-132">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="d3c3c-133">Yukarıdaki komut, Razor SQLite kullanarak bir Web uygulaması oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-133">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="d3c3c-134">LocalDB ile Web uygulaması oluşturmak için şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d3c3c-134">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="d3c3c-135">Oluşturulan proje bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core Identity ](xref:security/authentication/identity) sağlar.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-135">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="d3c3c-136">Identity Razor Sınıf kitaplığı, alanı ile uç noktaları kullanıma sunar `Identity` .</span><span class="sxs-lookup"><span data-stu-id="d3c3c-136">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="d3c3c-137">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="d3c3c-137">For example:</span></span>

* <span data-ttu-id="d3c3c-138">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="d3c3c-138">/Identity/Account/Login</span></span>
* <span data-ttu-id="d3c3c-139">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="d3c3c-139">/Identity/Account/Logout</span></span>
* <span data-ttu-id="d3c3c-140">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="d3c3c-140">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="d3c3c-141">Geçişleri Uygula</span><span class="sxs-lookup"><span data-stu-id="d3c3c-141">Apply migrations</span></span>

<span data-ttu-id="d3c3c-142">Veritabanını başlatmak için geçişleri uygulayın.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-142">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d3c3c-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d3c3c-143">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d3c3c-144">Paket Yöneticisi konsolunda aşağıdaki komutu çalıştırın (PMC):</span><span class="sxs-lookup"><span data-stu-id="d3c3c-144">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="d3c3c-145">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d3c3c-145">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d3c3c-146">SQLite kullanılırken geçişler Bu adımda gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-146">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="d3c3c-147">LocalDB için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="d3c3c-147">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="d3c3c-148">Test kaydı ve oturum açma</span><span class="sxs-lookup"><span data-stu-id="d3c3c-148">Test Register and Login</span></span>

<span data-ttu-id="d3c3c-149">Uygulamayı çalıştırın ve bir Kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-149">Run the app and register a user.</span></span> <span data-ttu-id="d3c3c-150">Ekran boyutunuza bağlı olarak, **kayıt** ve **oturum açma** bağlantılarını görmek için gezinti geçiş düğmesini seçmeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-150">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="d3c3c-151">IdentityHizmetleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d3c3c-151">Configure Identity services</span></span>

<span data-ttu-id="d3c3c-152">Hizmetler ' de eklenir `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d3c3c-152">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="d3c3c-153">Tipik model, tüm yöntemleri çağırmalıdır `Add{Service}` ve sonra tüm `services.Configure{Service}` yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-153">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="d3c3c-154">Önceki vurgulanan kod Identity varsayılan seçenek değerleriyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-154">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="d3c3c-155">Hizmetler, [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama için kullanılabilir hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-155">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="d3c3c-156">çağırarak etkindir <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="d3c3c-156"> is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="d3c3c-157">`UseAuthentication`istek ardışık düzenine kimlik doğrulama [ara yazılımı](xref:fundamentals/middleware/index) ekler.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-157">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="d3c3c-158">Şablon tarafından oluşturulan uygulama [Yetkilendirme](xref:security/authorization/secure-data)kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-158">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="d3c3c-159">`app.UseAuthorization`, uygulamanın yetkilendirme eklemesi için doğru sırada eklendiğinden emin olmak için dahil edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-159">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="d3c3c-160">`UseRouting`, `UseAuthentication` , `UseAuthorization` ve `UseEndpoints` Önceki kodda gösterilen sırada çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-160">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="d3c3c-161">Ve hakkında daha fazla bilgi için `IdentityOptions` `Startup` , bkz <xref:Microsoft.AspNetCore.Identity.IdentityOptions> . ve [uygulama başlatma](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="d3c3c-161">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="d3c3c-162">Scafkatlama kaydı, oturum açma, oturum kapatma ve RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="d3c3c-162">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d3c3c-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d3c3c-163">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d3c3c-164">,, `Register` `Login` `LogOut` Ve `RegisterConfirmation` dosyalarını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-164">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="d3c3c-165">Bu bölümde gösterilen kodu oluşturmak için, [Yetkilendirme talimatlarına Razor sahip bir projede Scaffold kimliğini](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) izleyin.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-165">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d3c3c-166">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d3c3c-166">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d3c3c-167">Projeyi **WebApp1**adıyla oluşturduysanız aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-167">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="d3c3c-168">Aksi takdirde, için doğru ad alanını kullanın `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="d3c3c-168">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="d3c3c-169">PowerShell, bir komut ayırıcısı olarak noktalı virgül kullanır.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-169">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="d3c3c-170">PowerShell kullanırken, dosya listesinde noktalı virgül karakterini kaçış veya dosya listesini, yukarıdaki örnekte gösterildiği gibi çift tırnak içine koyun.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-170">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="d3c3c-171">Yapı iskelesi hakkında daha fazla bilgi için Identity bkz. [ Razor kimlik doğrulama ile bir projede yapı iskelesi kimliği](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="d3c3c-171">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="d3c3c-172">Kaydı İncele</span><span class="sxs-lookup"><span data-stu-id="d3c3c-172">Examine Register</span></span>

<span data-ttu-id="d3c3c-173">Bir kullanıcı sayfadaki **Kaydet** düğmesine tıkladığında `Register` `RegisterModel.OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-173">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="d3c3c-174">Kullanıcı, nesnesi üzerinde [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) tarafından oluşturulmuştur `_userManager` :</span><span class="sxs-lookup"><span data-stu-id="d3c3c-174">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="d3c3c-175">Oturum açma</span><span class="sxs-lookup"><span data-stu-id="d3c3c-175">Log in</span></span>

<span data-ttu-id="d3c3c-176">Oturum açma formu şu durumlarda görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="d3c3c-176">The Login form is displayed when:</span></span>

* <span data-ttu-id="d3c3c-177">**Oturum aç** bağlantısı seçilidir.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-177">The **Log in** link is selected.</span></span>
* <span data-ttu-id="d3c3c-178">Kullanıcı, erişim yetkisi olmayan **veya** sistem tarafından kimliği doğrulanmamış olan sınırlı bir sayfaya erişmeyi dener.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-178">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="d3c3c-179">Oturum açma sayfasındaki form gönderildiğinde, `OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-179">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="d3c3c-180">`PasswordSignInAsync`, nesne üzerinde çağrılır `_signInManager` .</span><span class="sxs-lookup"><span data-stu-id="d3c3c-180">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="d3c3c-181">Yetkilendirme kararlarının nasıl yapılacağı hakkında bilgi için bkz <xref:security/authorization/introduction> ..</span><span class="sxs-lookup"><span data-stu-id="d3c3c-181">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="d3c3c-182">Oturumu Kapat</span><span class="sxs-lookup"><span data-stu-id="d3c3c-182">Log out</span></span>

<span data-ttu-id="d3c3c-183">**Oturumu kapatma** bağlantısı `LogoutModel.OnPost` eylemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-183">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="d3c3c-184">Önceki kodda, `return RedirectToPage();` tarayıcının yeni bir istek gerçekleştirmesini ve Kullanıcı kimliğinin güncelleştirilmesini sağlamak için kodun yeniden yönlendirme olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-184">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="d3c3c-185">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) , kullanıcının tanımlama bilgisinde depolanan taleplerini temizler.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-185">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="d3c3c-186">*Sayfa/paylaşılan/_LoginPartial. cshtml*'de gönderi belirtildi:</span><span class="sxs-lookup"><span data-stu-id="d3c3c-186">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="d3c3c-187">SınamanızIdentity</span><span class="sxs-lookup"><span data-stu-id="d3c3c-187">Test Identity</span></span>

<span data-ttu-id="d3c3c-188">Varsayılan Web projesi şablonları, giriş sayfalarına anonim erişime izin verir.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-188">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="d3c3c-189">Test etmek için şunları Identity ekleyin [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="d3c3c-189">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="d3c3c-190">Oturumunuz açık ise oturumu kapatın. Uygulamayı çalıştırın ve **Gizlilik** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-190">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="d3c3c-191">Oturum açma sayfasına yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-191">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="d3c3c-192">ExplorerIdentity</span><span class="sxs-lookup"><span data-stu-id="d3c3c-192">Explore Identity</span></span>

<span data-ttu-id="d3c3c-193">IdentityDaha ayrıntılı incelemek için:</span><span class="sxs-lookup"><span data-stu-id="d3c3c-193">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="d3c3c-194">Tam kimlik UI kaynağı oluşturma</span><span class="sxs-lookup"><span data-stu-id="d3c3c-194">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="d3c3c-195">Her sayfanın kaynağını inceleyin ve hata ayıklayıcıda ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-195">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="d3c3c-196">Bileşenleri</span><span class="sxs-lookup"><span data-stu-id="d3c3c-196"> Components</span></span>

<span data-ttu-id="d3c3c-197">Tüm Identity bağımlı NuGet paketleri [ASP.NET Core paylaşılan çerçevesine](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)dahildir.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-197">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="d3c3c-198">İçin birincil paket Identity [Microsoft. aspnetcore Identity .](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)..</span><span class="sxs-lookup"><span data-stu-id="d3c3c-198">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="d3c3c-199">Bu paket, ASP.NET Core için temel arabirim kümesini içerir Identity ve tarafından dahildir `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="d3c3c-199">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="d3c3c-200">ASP.NET Core geçiriliyorIdentity</span><span class="sxs-lookup"><span data-stu-id="d3c3c-200">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="d3c3c-201">Mevcut deponuzu geçirme hakkında daha fazla bilgi ve yönergeler için Identity bkz. [geçiş kimlik Identity doğrulaması ve ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="d3c3c-201">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="d3c3c-202">Parola gücünü ayarlama</span><span class="sxs-lookup"><span data-stu-id="d3c3c-202">Setting password strength</span></span>

<span data-ttu-id="d3c3c-203">Minimum parola gereksinimlerini ayarlayan bir örnek için bkz. [yapılandırma](#pw) .</span><span class="sxs-lookup"><span data-stu-id="d3c3c-203">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="d3c3c-204">AddDefault Identity ve EkleIdentity</span><span class="sxs-lookup"><span data-stu-id="d3c3c-204">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="d3c3c-205"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>ASP.NET Core 2,1 ' de tanıtılmıştı.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-205"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="d3c3c-206">Çağırma `AddDefaultIdentity` , aşağıdakileri çağırmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="d3c3c-206">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="d3c3c-207">Daha fazla bilgi için bkz. [ADDDEFAULT Identity kaynağı](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="d3c3c-207">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="d3c3c-208">Statik varlıkların yayımlanmasını Engelle Identity</span><span class="sxs-lookup"><span data-stu-id="d3c3c-208">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="d3c3c-209">Statik Identity varlıkların (Kullanıcı arabirimi için stil sayfaları ve JavaScript dosyaları Identity ) Web köküne yayımlanmasını engellemek için aşağıdaki `ResolveStaticWebAssetsInputsDependsOn` özelliği ve `RemoveIdentityAssets` hedefi uygulamanın proje dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d3c3c-209">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="d3c3c-210">Sonraki Adımlar</span><span class="sxs-lookup"><span data-stu-id="d3c3c-210">Next Steps</span></span>

* <span data-ttu-id="d3c3c-211">[ASP.NET Core Identity kaynak kodu](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="d3c3c-211">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="d3c3c-212">SQLite kullanarak yapılandırma hakkında bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/5131) bakın Identity .</span><span class="sxs-lookup"><span data-stu-id="d3c3c-212">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="d3c3c-213">[YapılandırmaIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="d3c3c-213">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d3c3c-214">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d3c3c-214">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d3c3c-215">ASP.NET Core Identity , ASP.NET Core uygulamalara oturum açma işlevselliği ekleyen bir üyelik sistemidir.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-215">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="d3c3c-216">Kullanıcılar, içinde depolanan oturum açma bilgilerini içeren bir hesap oluşturabilir Identity veya bir dış oturum açma sağlayıcısı kullanabilirler.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-216">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="d3c3c-217">Desteklenen dış oturum açma sağlayıcıları [Facebook, Google, Microsoft hesabı ve Twitter](xref:security/authentication/social/index)içerir.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-217">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

Identity<span data-ttu-id="d3c3c-218">Kullanıcı adlarını, parolaları ve profil verilerini depolamak için bir SQL Server veritabanı kullanılarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-218"> can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="d3c3c-219">Alternatif olarak, başka bir kalıcı mağaza da kullanılabilir, örneğin Azure Tablo depolaması.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-219">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="d3c3c-220">Örnek kodu ([indirme)](xref:index#how-to-download-a-sample) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) .</span><span class="sxs-lookup"><span data-stu-id="d3c3c-220">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="d3c3c-221">Bu konu başlığında, Identity bir kullanıcıyı kaydetmek, oturum açmak ve oturumu kapatmak için kullanmayı öğreneceksiniz.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-221">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="d3c3c-222">Kullanan uygulamalar oluşturma hakkında daha ayrıntılı yönergeler için Identity , bu makalenin sonundaki sonraki adımlar bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-222">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="d3c3c-223">AddDefault Identity ve EkleIdentity</span><span class="sxs-lookup"><span data-stu-id="d3c3c-223">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="d3c3c-224"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>ASP.NET Core 2,1 ' de tanıtılmıştı.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-224"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="d3c3c-225">Çağırma `AddDefaultIdentity` , aşağıdakileri çağırmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="d3c3c-225">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="d3c3c-226">Daha fazla bilgi için bkz. [ADDDEFAULT Identity kaynağı](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="d3c3c-226">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="d3c3c-227">Kimlik doğrulamasıyla bir Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="d3c3c-227">Create a Web app with authentication</span></span>

<span data-ttu-id="d3c3c-228">Bireysel kullanıcı hesaplarıyla bir ASP.NET Core Web uygulaması projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-228">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d3c3c-229">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d3c3c-229">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d3c3c-230">**Dosya** > **Yeni** > **Proje**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-230">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="d3c3c-231">**ASP.NET Core Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-231">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="d3c3c-232">Projeyi Proje **WebApp1** aynı ad alanına sahip olacak şekilde adlandırın.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-232">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="d3c3c-233">**Tamam** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-233">Click **OK**.</span></span>
* <span data-ttu-id="d3c3c-234">Bir ASP.NET Core **Web uygulaması**seçip **kimlik doğrulamasını Değiştir**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-234">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="d3c3c-235">**Bireysel kullanıcı hesapları** ' nı seçip **Tamam**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-235">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d3c3c-236">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d3c3c-236">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="d3c3c-237">Oluşturulan proje bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core Identity ](xref:security/authentication/identity) sağlar.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-237">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="d3c3c-238">Identity Razor Sınıf kitaplığı, alanı ile uç noktaları kullanıma sunar `Identity` .</span><span class="sxs-lookup"><span data-stu-id="d3c3c-238">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="d3c3c-239">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="d3c3c-239">For example:</span></span>

* <span data-ttu-id="d3c3c-240">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="d3c3c-240">/Identity/Account/Login</span></span>
* <span data-ttu-id="d3c3c-241">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="d3c3c-241">/Identity/Account/Logout</span></span>
* <span data-ttu-id="d3c3c-242">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="d3c3c-242">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="d3c3c-243">Geçişleri Uygula</span><span class="sxs-lookup"><span data-stu-id="d3c3c-243">Apply migrations</span></span>

<span data-ttu-id="d3c3c-244">Veritabanını başlatmak için geçişleri uygulayın.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-244">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d3c3c-245">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d3c3c-245">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d3c3c-246">Paket Yöneticisi konsolunda aşağıdaki komutu çalıştırın (PMC):</span><span class="sxs-lookup"><span data-stu-id="d3c3c-246">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="d3c3c-247">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d3c3c-247">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="d3c3c-248">Test kaydı ve oturum açma</span><span class="sxs-lookup"><span data-stu-id="d3c3c-248">Test Register and Login</span></span>

<span data-ttu-id="d3c3c-249">Uygulamayı çalıştırın ve bir Kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-249">Run the app and register a user.</span></span> <span data-ttu-id="d3c3c-250">Ekran boyutunuza bağlı olarak, **kayıt** ve **oturum açma** bağlantılarını görmek için gezinti geçiş düğmesini seçmeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-250">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="d3c3c-251">IdentityHizmetleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="d3c3c-251">Configure Identity services</span></span>

<span data-ttu-id="d3c3c-252">Hizmetler ' de eklenir `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d3c3c-252">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="d3c3c-253">Tipik model, tüm yöntemleri çağırmalıdır `Add{Service}` ve sonra tüm `services.Configure{Service}` yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-253">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="d3c3c-254">Önceki kod Identity varsayılan seçenek değerleriyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-254">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="d3c3c-255">Hizmetler, [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama için kullanılabilir hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-255">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="d3c3c-256">[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)çağırarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-256"> is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="d3c3c-257">`UseAuthentication`istek ardışık düzenine kimlik doğrulama [ara yazılımı](xref:fundamentals/middleware/index) ekler.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-257">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="d3c3c-258">Daha fazla bilgi için bkz. [ Identity Options sınıfı](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) ve [uygulama başlatma](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="d3c3c-258">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="d3c3c-259">Yapı iskelesi kaydı, oturum açma ve oturum kapatma</span><span class="sxs-lookup"><span data-stu-id="d3c3c-259">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="d3c3c-260">Bu bölümde gösterilen kodu oluşturmak için, [Yetkilendirme talimatlarına Razor sahip bir projede Scaffold kimliğini](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) izleyin.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-260">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d3c3c-261">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d3c3c-261">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d3c3c-262">Kayıt, oturum açma ve oturum kapatma dosyalarını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-262">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="d3c3c-263">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="d3c3c-263">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="d3c3c-264">Projeyi **WebApp1**adıyla oluşturduysanız aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-264">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="d3c3c-265">Aksi takdirde, için doğru ad alanını kullanın `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="d3c3c-265">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="d3c3c-266">PowerShell, bir komut ayırıcısı olarak noktalı virgül kullanır.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-266">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="d3c3c-267">PowerShell kullanırken, dosya listesinde noktalı virgül karakterini kaçış veya dosya listesini, yukarıdaki örnekte gösterildiği gibi çift tırnak içine koyun.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-267">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="d3c3c-268">Kaydı İncele</span><span class="sxs-lookup"><span data-stu-id="d3c3c-268">Examine Register</span></span>

<span data-ttu-id="d3c3c-269">Kullanıcı **Kaydet** bağlantısına tıkladığında `RegisterModel.OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-269">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="d3c3c-270">Kullanıcı, nesnesi üzerinde [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) tarafından oluşturulmuştur `_userManager` :</span><span class="sxs-lookup"><span data-stu-id="d3c3c-270">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="d3c3c-271">Kullanıcı başarıyla oluşturulduysa, Kullanıcı çağrısıyla oturum açar `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="d3c3c-271">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="d3c3c-272">**Note:** Kayıt sırasında anında oturum açmayı önlemeye yönelik adımlar için bkz. [Hesap onayı](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="d3c3c-272">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="d3c3c-273">Oturum açma</span><span class="sxs-lookup"><span data-stu-id="d3c3c-273">Log in</span></span>

<span data-ttu-id="d3c3c-274">Oturum açma formu şu durumlarda görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="d3c3c-274">The Login form is displayed when:</span></span>

* <span data-ttu-id="d3c3c-275">**Oturum aç** bağlantısı seçilidir.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-275">The **Log in** link is selected.</span></span>
* <span data-ttu-id="d3c3c-276">Kullanıcı, erişim yetkisi olmayan **veya** sistem tarafından kimliği doğrulanmamış olan sınırlı bir sayfaya erişmeyi dener.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-276">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="d3c3c-277">Oturum açma sayfasındaki form gönderildiğinde, `OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-277">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="d3c3c-278">`PasswordSignInAsync`, nesne üzerinde çağrılır `_signInManager` .</span><span class="sxs-lookup"><span data-stu-id="d3c3c-278">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="d3c3c-279">Yetkilendirme kararlarının nasıl yapılacağı hakkında bilgi için bkz <xref:security/authorization/introduction> ..</span><span class="sxs-lookup"><span data-stu-id="d3c3c-279">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="d3c3c-280">Oturumu Kapat</span><span class="sxs-lookup"><span data-stu-id="d3c3c-280">Log out</span></span>

<span data-ttu-id="d3c3c-281">**Oturumu kapatma** bağlantısı `LogoutModel.OnPost` eylemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-281">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="d3c3c-282">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) , kullanıcının tanımlama bilgisinde depolanan taleplerini temizler.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-282">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="d3c3c-283">*Sayfa/paylaşılan/_LoginPartial. cshtml*'de gönderi belirtildi:</span><span class="sxs-lookup"><span data-stu-id="d3c3c-283">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="d3c3c-284">SınamanızIdentity</span><span class="sxs-lookup"><span data-stu-id="d3c3c-284">Test Identity</span></span>

<span data-ttu-id="d3c3c-285">Varsayılan Web projesi şablonları, giriş sayfalarına anonim erişime izin verir.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-285">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="d3c3c-286">Test etmek için Identity [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) Gizlilik sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-286">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="d3c3c-287">Oturumunuz açık ise oturumu kapatın. Uygulamayı çalıştırın ve **Gizlilik** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-287">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="d3c3c-288">Oturum açma sayfasına yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-288">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="d3c3c-289">ExplorerIdentity</span><span class="sxs-lookup"><span data-stu-id="d3c3c-289">Explore Identity</span></span>

<span data-ttu-id="d3c3c-290">IdentityDaha ayrıntılı incelemek için:</span><span class="sxs-lookup"><span data-stu-id="d3c3c-290">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="d3c3c-291">Tam kimlik UI kaynağı oluşturma</span><span class="sxs-lookup"><span data-stu-id="d3c3c-291">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="d3c3c-292">Her sayfanın kaynağını inceleyin ve hata ayıklayıcıda ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-292">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="d3c3c-293">Bileşenleri</span><span class="sxs-lookup"><span data-stu-id="d3c3c-293"> Components</span></span>

<span data-ttu-id="d3c3c-294">Tüm Identity bağımlı NuGet paketleri [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app)'e dahildir.</span><span class="sxs-lookup"><span data-stu-id="d3c3c-294">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="d3c3c-295">İçin birincil paket Identity [Microsoft. aspnetcore Identity .](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)..</span><span class="sxs-lookup"><span data-stu-id="d3c3c-295">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="d3c3c-296">Bu paket, ASP.NET Core için temel arabirim kümesini içerir Identity ve tarafından dahildir `Microsoft.AspNetCore.Identity.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="d3c3c-296">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="d3c3c-297">ASP.NET Core geçiriliyorIdentity</span><span class="sxs-lookup"><span data-stu-id="d3c3c-297">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="d3c3c-298">Mevcut deponuzu geçirme hakkında daha fazla bilgi ve yönergeler için Identity bkz. [geçiş kimlik Identity doğrulaması ve ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="d3c3c-298">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="d3c3c-299">Parola gücünü ayarlama</span><span class="sxs-lookup"><span data-stu-id="d3c3c-299">Setting password strength</span></span>

<span data-ttu-id="d3c3c-300">Minimum parola gereksinimlerini ayarlayan bir örnek için bkz. [yapılandırma](#pw) .</span><span class="sxs-lookup"><span data-stu-id="d3c3c-300">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d3c3c-301">Sonraki Adımlar</span><span class="sxs-lookup"><span data-stu-id="d3c3c-301">Next Steps</span></span>

* <span data-ttu-id="d3c3c-302">SQLite kullanarak yapılandırma hakkında bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/5131) bakın Identity .</span><span class="sxs-lookup"><span data-stu-id="d3c3c-302">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="d3c3c-303">[YapılandırmaIdentity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="d3c3c-303">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
