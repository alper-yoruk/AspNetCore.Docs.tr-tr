---
title: 'ASP.NET Core giriş :::no-loc(Identity):::'
author: rick-anderson
description: :::no-loc(Identity):::ASP.NET Core bir uygulamayla kullanın. Parola gereksinimlerini (RequireDigit, RequiredLength, RequiredUniqueChars ve daha fazlasını) ayarlamayı öğrenin.
ms.author: riande
ms.date: 7/15/2020
no-loc:
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/identity
ms.openlocfilehash: dd3296db568700a363c427398f02239846a46ada
ms.sourcegitcommit: d9ae1f352d372a20534b57e23646c1a1d9171af1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/21/2020
ms.locfileid: "86445443"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a><span data-ttu-id="820c3-104">ASP.NET Core giriş :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="820c3-104">Introduction to :::no-loc(Identity)::: on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="820c3-105">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="820c3-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="820c3-106">ASP.NET Core :::no-loc(Identity)::: :</span><span class="sxs-lookup"><span data-stu-id="820c3-106">ASP.NET Core :::no-loc(Identity)::::</span></span>

* <span data-ttu-id="820c3-107">, Kullanıcı arabirimi (UI) oturum açma işlevselliğini destekleyen bir API 'dir.</span><span class="sxs-lookup"><span data-stu-id="820c3-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="820c3-108">Kullanıcıları, parolaları, profil verilerini, rolleri, talepleri, belirteçleri, e-posta onayını ve daha fazlasını yönetir.</span><span class="sxs-lookup"><span data-stu-id="820c3-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="820c3-109">Kullanıcılar, içinde depolanan oturum açma bilgilerini içeren bir hesap oluşturabilir :::no-loc(Identity)::: veya bir dış oturum açma sağlayıcısı kullanabilirler.</span><span class="sxs-lookup"><span data-stu-id="820c3-109">Users can create an account with the login information stored in :::no-loc(Identity)::: or they can use an external login provider.</span></span> <span data-ttu-id="820c3-110">Desteklenen dış oturum açma sağlayıcıları [Facebook, Google, Microsoft hesabı ve Twitter](xref:security/authentication/social/index)içerir.</span><span class="sxs-lookup"><span data-stu-id="820c3-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="820c3-111">[ :::no-loc(Identity)::: Kaynak kodu](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(Identity):::) GitHub ' da kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="820c3-111">The [:::no-loc(Identity)::: source code](https://github.com/dotnet/AspNetCore/tree/master/src/:::no-loc(Identity):::) is available on GitHub.</span></span> <span data-ttu-id="820c3-112">[Yapı :::no-loc(Identity)::: İskelesi](xref:security/authentication/scaffold-identity) ve şablon etkileşimini gözden geçirmek için oluşturulan dosyaları görüntüleyin :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="820c3-112">[Scaffold :::no-loc(Identity):::](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with :::no-loc(Identity):::.</span></span>

<span data-ttu-id="820c3-113">:::no-loc(Identity):::genellikle kullanıcı adlarını, parolaları ve profil verilerini depolamak için bir SQL Server veritabanı kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="820c3-113">:::no-loc(Identity)::: is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="820c3-114">Alternatif olarak, başka bir kalıcı mağaza da kullanılabilir, örneğin Azure Tablo depolaması.</span><span class="sxs-lookup"><span data-stu-id="820c3-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="820c3-115">Bu konu başlığında, :::no-loc(Identity)::: bir kullanıcıyı kaydetmek, oturum açmak ve oturumu kapatmak için kullanmayı öğreneceksiniz.</span><span class="sxs-lookup"><span data-stu-id="820c3-115">In this topic, you learn how to use :::no-loc(Identity)::: to register, log in, and log out a user.</span></span> <span data-ttu-id="820c3-116">Note: Şablonlar Kullanıcı adı ve e-postayı kullanıcılar için aynı olarak değerlendirir.</span><span class="sxs-lookup"><span data-stu-id="820c3-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="820c3-117">Kullanan uygulamaları oluşturma hakkında daha ayrıntılı yönergeler için :::no-loc(Identity)::: bkz. [sonraki adımlar](#next).</span><span class="sxs-lookup"><span data-stu-id="820c3-117">For more detailed instructions about creating apps that use :::no-loc(Identity):::, see [Next Steps](#next).</span></span>

<span data-ttu-id="820c3-118">[Microsoft Identity platform](/azure/active-directory/develop/) :</span><span class="sxs-lookup"><span data-stu-id="820c3-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="820c3-119">Azure Active Directory (Azure AD) geliştirici platformunun bir evrimi.</span><span class="sxs-lookup"><span data-stu-id="820c3-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="820c3-120">ASP.NET Core ilgisi yoktur :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="820c3-120">Unrelated to ASP.NET Core :::no-loc(Identity):::.</span></span>

[!INCLUDE[](~/includes/:::no-loc(Identity):::Server4.md)]

<span data-ttu-id="820c3-121">Örnek kodu ([indirme)](xref:index#how-to-download-a-sample) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) .</span><span class="sxs-lookup"><span data-stu-id="820c3-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="820c3-122">Kimlik doğrulamasıyla bir Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="820c3-122">Create a Web app with authentication</span></span>

<span data-ttu-id="820c3-123">Bireysel kullanıcı hesaplarıyla bir ASP.NET Core Web uygulaması projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="820c3-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="820c3-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="820c3-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="820c3-125">**Dosya** > **Yeni** > **Proje**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="820c3-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="820c3-126">**ASP.NET Core Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="820c3-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="820c3-127">Projeyi Proje **WebApp1** aynı ad alanına sahip olacak şekilde adlandırın.</span><span class="sxs-lookup"><span data-stu-id="820c3-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="820c3-128">**Tamam** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="820c3-128">Click **OK**.</span></span>
* <span data-ttu-id="820c3-129">Bir ASP.NET Core **Web uygulaması**seçip **kimlik doğrulamasını Değiştir**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="820c3-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="820c3-130">**Bireysel kullanıcı hesapları** ' nı seçip **Tamam**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="820c3-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="820c3-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="820c3-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="820c3-132">Yukarıdaki komut, :::no-loc(Razor)::: SQLite kullanarak bir Web uygulaması oluşturur.</span><span class="sxs-lookup"><span data-stu-id="820c3-132">The preceding command creates a :::no-loc(Razor)::: web app using SQLite.</span></span> <span data-ttu-id="820c3-133">LocalDB ile Web uygulaması oluşturmak için şu komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="820c3-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="820c3-134">Oluşturulan proje bir [ :::no-loc(Razor)::: sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core :::no-loc(Identity)::: ](xref:security/authentication/identity) sağlar.</span><span class="sxs-lookup"><span data-stu-id="820c3-134">The generated project provides [ASP.NET Core :::no-loc(Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="820c3-135">:::no-loc(Identity)::: :::no-loc(Razor)::: Sınıf kitaplığı, alanı ile uç noktaları kullanıma sunar `:::no-loc(Identity):::` .</span><span class="sxs-lookup"><span data-stu-id="820c3-135">The :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library exposes endpoints with the `:::no-loc(Identity):::` area.</span></span> <span data-ttu-id="820c3-136">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="820c3-136">For example:</span></span>

* <span data-ttu-id="820c3-137">/:::no-loc(Identity):::/Account/Login</span><span class="sxs-lookup"><span data-stu-id="820c3-137">/:::no-loc(Identity):::/Account/Login</span></span>
* <span data-ttu-id="820c3-138">/:::no-loc(Identity):::/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="820c3-138">/:::no-loc(Identity):::/Account/Logout</span></span>
* <span data-ttu-id="820c3-139">/:::no-loc(Identity):::/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="820c3-139">/:::no-loc(Identity):::/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="820c3-140">Geçişleri Uygula</span><span class="sxs-lookup"><span data-stu-id="820c3-140">Apply migrations</span></span>

<span data-ttu-id="820c3-141">Veritabanını başlatmak için geçişleri uygulayın.</span><span class="sxs-lookup"><span data-stu-id="820c3-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="820c3-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="820c3-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="820c3-143">Paket Yöneticisi konsolunda aşağıdaki komutu çalıştırın (PMC):</span><span class="sxs-lookup"><span data-stu-id="820c3-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="820c3-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="820c3-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="820c3-145">SQLite kullanılırken geçişler Bu adımda gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="820c3-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="820c3-146">LocalDB için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="820c3-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="820c3-147">Test kaydı ve oturum açma</span><span class="sxs-lookup"><span data-stu-id="820c3-147">Test Register and Login</span></span>

<span data-ttu-id="820c3-148">Uygulamayı çalıştırın ve bir Kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="820c3-148">Run the app and register a user.</span></span> <span data-ttu-id="820c3-149">Ekran boyutunuza bağlı olarak, **kayıt** ve **oturum açma** bağlantılarını görmek için gezinti geçiş düğmesini seçmeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="820c3-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="820c3-150">:::no-loc(Identity):::Hizmetleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="820c3-150">Configure :::no-loc(Identity)::: services</span></span>

<span data-ttu-id="820c3-151">Hizmetler ' de eklenir `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="820c3-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="820c3-152">Tipik model, tüm yöntemleri çağırmalıdır `Add{Service}` ve sonra tüm `services.Configure{Service}` yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="820c3-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="820c3-153">Önceki vurgulanan kod :::no-loc(Identity)::: varsayılan seçenek değerleriyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="820c3-153">The preceding highlighted code configures :::no-loc(Identity)::: with default option values.</span></span> <span data-ttu-id="820c3-154">Hizmetler, [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama için kullanılabilir hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="820c3-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="820c3-155">:::no-loc(Identity):::çağırarak etkindir <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> .</span><span class="sxs-lookup"><span data-stu-id="820c3-155">:::no-loc(Identity)::: is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="820c3-156">`UseAuthentication`istek ardışık düzenine kimlik doğrulama [ara yazılımı](xref:fundamentals/middleware/index) ekler.</span><span class="sxs-lookup"><span data-stu-id="820c3-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="820c3-157">Şablon tarafından oluşturulan uygulama [Yetkilendirme](xref:security/authorization/secure-data)kullanmaz.</span><span class="sxs-lookup"><span data-stu-id="820c3-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="820c3-158">`app.UseAuthorization`, uygulamanın yetkilendirme eklemesi için doğru sırada eklendiğinden emin olmak için dahil edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="820c3-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="820c3-159">`UseRouting`, `UseAuthentication` , `UseAuthorization` ve `UseEndpoints` Önceki kodda gösterilen sırada çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="820c3-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="820c3-160">Ve hakkında daha fazla bilgi için `:::no-loc(Identity):::Options` `Startup` , bkz <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Options> . ve [uygulama başlatma](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="820c3-160">For more information on `:::no-loc(Identity):::Options` and `Startup`, see <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::Options> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="820c3-161">Scafkatlama kaydı, oturum açma, oturum kapatma ve RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="820c3-161">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="820c3-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="820c3-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="820c3-163">,, `Register` `Login` `LogOut` Ve `RegisterConfirmation` dosyalarını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="820c3-163">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="820c3-164">Bu bölümde gösterilen kodu oluşturmak için, [Yetkilendirme talimatlarına :::no-loc(Razor)::: sahip bir projede Scaffold kimliğini](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) izleyin.</span><span class="sxs-lookup"><span data-stu-id="820c3-164">Follow the [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="820c3-165">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="820c3-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="820c3-166">Projeyi **WebApp1**adıyla oluşturduysanız aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="820c3-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="820c3-167">Aksi takdirde, için doğru ad alanını kullanın `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="820c3-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="820c3-168">PowerShell, bir komut ayırıcısı olarak noktalı virgül kullanır.</span><span class="sxs-lookup"><span data-stu-id="820c3-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="820c3-169">PowerShell kullanırken, dosya listesinde noktalı virgül karakterini kaçış veya dosya listesini, yukarıdaki örnekte gösterildiği gibi çift tırnak içine koyun.</span><span class="sxs-lookup"><span data-stu-id="820c3-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="820c3-170">Yapı iskelesi hakkında daha fazla bilgi için :::no-loc(Identity)::: bkz. [ :::no-loc(Razor)::: kimlik doğrulama ile bir projede yapı iskelesi kimliği](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span><span class="sxs-lookup"><span data-stu-id="820c3-170">For more information on scaffolding :::no-loc(Identity):::, see [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="820c3-171">Kaydı İncele</span><span class="sxs-lookup"><span data-stu-id="820c3-171">Examine Register</span></span>

<span data-ttu-id="820c3-172">Bir kullanıcı sayfadaki **Kaydet** düğmesine tıkladığında `Register` `RegisterModel.OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="820c3-172">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="820c3-173">Kullanıcı, nesnesi üzerinde [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) tarafından oluşturulmuştur `_userManager` :</span><span class="sxs-lookup"><span data-stu-id="820c3-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/:::no-loc(Identity):::/UI/src/Areas/:::no-loc(Identity):::/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="820c3-174">Oturum açma</span><span class="sxs-lookup"><span data-stu-id="820c3-174">Log in</span></span>

<span data-ttu-id="820c3-175">Oturum açma formu şu durumlarda görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="820c3-175">The Login form is displayed when:</span></span>

* <span data-ttu-id="820c3-176">**Oturum aç** bağlantısı seçilidir.</span><span class="sxs-lookup"><span data-stu-id="820c3-176">The **Log in** link is selected.</span></span>
* <span data-ttu-id="820c3-177">Kullanıcı, erişim yetkisi olmayan **veya** sistem tarafından kimliği doğrulanmamış olan sınırlı bir sayfaya erişmeyi dener.</span><span class="sxs-lookup"><span data-stu-id="820c3-177">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="820c3-178">Oturum açma sayfasındaki form gönderildiğinde, `OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="820c3-178">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="820c3-179">`PasswordSignInAsync`, nesne üzerinde çağrılır `_signInManager` .</span><span class="sxs-lookup"><span data-stu-id="820c3-179">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="820c3-180">Yetkilendirme kararlarının nasıl yapılacağı hakkında bilgi için bkz <xref:security/authorization/introduction> ..</span><span class="sxs-lookup"><span data-stu-id="820c3-180">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="820c3-181">Oturumu Kapat</span><span class="sxs-lookup"><span data-stu-id="820c3-181">Log out</span></span>

<span data-ttu-id="820c3-182">**Oturumu kapatma** bağlantısı `LogoutModel.OnPost` eylemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="820c3-182">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="820c3-183">Önceki kodda, `return RedirectToPage();` tarayıcının yeni bir istek gerçekleştirmesini ve Kullanıcı kimliğinin güncelleştirilmesini sağlamak için kodun yeniden yönlendirme olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="820c3-183">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="820c3-184">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) , kullanıcının tanımlama bilgisinde depolanan taleplerini temizler.</span><span class="sxs-lookup"><span data-stu-id="820c3-184">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="820c3-185">*Sayfa/paylaşılan/_LoginPartial. cshtml*'de gönderi belirtildi:</span><span class="sxs-lookup"><span data-stu-id="820c3-185">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a><span data-ttu-id="820c3-186">Sınamanız:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="820c3-186">Test :::no-loc(Identity):::</span></span>

<span data-ttu-id="820c3-187">Varsayılan Web projesi şablonları, giriş sayfalarına anonim erişime izin verir.</span><span class="sxs-lookup"><span data-stu-id="820c3-187">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="820c3-188">Test etmek için şunları :::no-loc(Identity)::: ekleyin [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) :</span><span class="sxs-lookup"><span data-stu-id="820c3-188">To test :::no-loc(Identity):::, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="820c3-189">Oturumunuz açık ise oturumu kapatın. Uygulamayı çalıştırın ve **Gizlilik** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="820c3-189">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="820c3-190">Oturum açma sayfasına yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="820c3-190">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="820c3-191">Explorer:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="820c3-191">Explore :::no-loc(Identity):::</span></span>

<span data-ttu-id="820c3-192">:::no-loc(Identity):::Daha ayrıntılı incelemek için:</span><span class="sxs-lookup"><span data-stu-id="820c3-192">To explore :::no-loc(Identity)::: in more detail:</span></span>

* [<span data-ttu-id="820c3-193">Tam kimlik UI kaynağı oluşturma</span><span class="sxs-lookup"><span data-stu-id="820c3-193">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="820c3-194">Her sayfanın kaynağını inceleyin ve hata ayıklayıcıda ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="820c3-194">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="820c3-195">:::no-loc(Identity):::Bileşenleri</span><span class="sxs-lookup"><span data-stu-id="820c3-195">:::no-loc(Identity)::: Components</span></span>

<span data-ttu-id="820c3-196">Tüm :::no-loc(Identity)::: bağımlı NuGet paketleri [ASP.NET Core paylaşılan çerçevesine](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)dahildir.</span><span class="sxs-lookup"><span data-stu-id="820c3-196">All the :::no-loc(Identity):::-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="820c3-197">İçin birincil paket :::no-loc(Identity)::: [Microsoft. aspnetcore :::no-loc(Identity)::: .](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/)..</span><span class="sxs-lookup"><span data-stu-id="820c3-197">The primary package for :::no-loc(Identity)::: is [Microsoft.AspNetCore.:::no-loc(Identity):::](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).</span></span> <span data-ttu-id="820c3-198">Bu paket, ASP.NET Core için temel arabirim kümesini içerir :::no-loc(Identity)::: ve tarafından dahildir `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="820c3-198">This package contains the core set of interfaces for ASP.NET Core :::no-loc(Identity):::, and is included by `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-no-locidentity"></a><span data-ttu-id="820c3-199">ASP.NET Core geçiriliyor:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="820c3-199">Migrating to ASP.NET Core :::no-loc(Identity):::</span></span>

<span data-ttu-id="820c3-200">Mevcut deponuzu geçirme hakkında daha fazla bilgi ve yönergeler için :::no-loc(Identity)::: bkz. [geçiş kimlik :::no-loc(Identity)::: doğrulaması ve ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="820c3-200">For more information and guidance on migrating your existing :::no-loc(Identity)::: store, see [Migrate Authentication and :::no-loc(Identity):::](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="820c3-201">Parola gücünü ayarlama</span><span class="sxs-lookup"><span data-stu-id="820c3-201">Setting password strength</span></span>

<span data-ttu-id="820c3-202">Minimum parola gereksinimlerini ayarlayan bir örnek için bkz. [yapılandırma](#pw) .</span><span class="sxs-lookup"><span data-stu-id="820c3-202">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="820c3-203">AddDefault :::no-loc(Identity)::: ve Ekle:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="820c3-203">AddDefault:::no-loc(Identity)::: and Add:::no-loc(Identity):::</span></span>

<span data-ttu-id="820c3-204"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*>ASP.NET Core 2,1 ' de tanıtılmıştı.</span><span class="sxs-lookup"><span data-stu-id="820c3-204"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="820c3-205">Çağırma `AddDefault:::no-loc(Identity):::` , aşağıdakileri çağırmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="820c3-205">Calling `AddDefault:::no-loc(Identity):::` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="820c3-206">Daha fazla bilgi için bkz. [ADDDEFAULT :::no-loc(Identity)::: kaynağı](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="820c3-206">See [AddDefault:::no-loc(Identity)::: source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="820c3-207">Statik varlıkların yayımlanmasını Engelle :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="820c3-207">Prevent publish of static :::no-loc(Identity)::: assets</span></span>

<span data-ttu-id="820c3-208">Statik :::no-loc(Identity)::: varlıkların (Kullanıcı arabirimi için stil sayfaları ve JavaScript dosyaları :::no-loc(Identity)::: ) Web köküne yayımlanmasını engellemek için aşağıdaki `ResolveStaticWebAssetsInputsDependsOn` özelliği ve `Remove:::no-loc(Identity):::Assets` hedefi uygulamanın proje dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="820c3-208">To prevent publishing static :::no-loc(Identity)::: assets (stylesheets and JavaScript files for :::no-loc(Identity)::: UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `Remove:::no-loc(Identity):::Assets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>Remove:::no-loc(Identity):::Assets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="Remove:::no-loc(Identity):::Assets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.:::no-loc(Identity):::.UI'" />
  </ItemGroup>
</Target>
```

<a name="next"></a>

## <a name="next-steps"></a><span data-ttu-id="820c3-209">Sonraki Adımlar</span><span class="sxs-lookup"><span data-stu-id="820c3-209">Next Steps</span></span>

* <span data-ttu-id="820c3-210">[ASP.NET Core :::no-loc(Identity)::: kaynak kodu](https://github.com/dotnet/aspnetcore/tree/master/src/:::no-loc(Identity):::)</span><span class="sxs-lookup"><span data-stu-id="820c3-210">[ASP.NET Core :::no-loc(Identity)::: source code](https://github.com/dotnet/aspnetcore/tree/master/src/:::no-loc(Identity):::)</span></span>
* <span data-ttu-id="820c3-211">SQLite kullanarak yapılandırma hakkında bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/5131) bakın :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="820c3-211">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring :::no-loc(Identity)::: using SQLite.</span></span>
* [<span data-ttu-id="820c3-212">Yapılandırma:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="820c3-212">Configure :::no-loc(Identity):::</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="820c3-213">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="820c3-213">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="820c3-214">ASP.NET Core :::no-loc(Identity)::: , ASP.NET Core uygulamalara oturum açma işlevselliği ekleyen bir üyelik sistemidir.</span><span class="sxs-lookup"><span data-stu-id="820c3-214">ASP.NET Core :::no-loc(Identity)::: is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="820c3-215">Kullanıcılar, içinde depolanan oturum açma bilgilerini içeren bir hesap oluşturabilir :::no-loc(Identity)::: veya bir dış oturum açma sağlayıcısı kullanabilirler.</span><span class="sxs-lookup"><span data-stu-id="820c3-215">Users can create an account with the login information stored in :::no-loc(Identity)::: or they can use an external login provider.</span></span> <span data-ttu-id="820c3-216">Desteklenen dış oturum açma sağlayıcıları [Facebook, Google, Microsoft hesabı ve Twitter](xref:security/authentication/social/index)içerir.</span><span class="sxs-lookup"><span data-stu-id="820c3-216">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="820c3-217">:::no-loc(Identity):::Kullanıcı adlarını, parolaları ve profil verilerini depolamak için bir SQL Server veritabanı kullanılarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="820c3-217">:::no-loc(Identity)::: can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="820c3-218">Alternatif olarak, başka bir kalıcı mağaza da kullanılabilir, örneğin Azure Tablo depolaması.</span><span class="sxs-lookup"><span data-stu-id="820c3-218">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="820c3-219">Örnek kodu ([indirme)](xref:index#how-to-download-a-sample) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-:::no-loc(Identity):::DemoComplete/) .</span><span class="sxs-lookup"><span data-stu-id="820c3-219">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-:::no-loc(Identity):::DemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="820c3-220">Bu konu başlığında, :::no-loc(Identity)::: bir kullanıcıyı kaydetmek, oturum açmak ve oturumu kapatmak için kullanmayı öğreneceksiniz.</span><span class="sxs-lookup"><span data-stu-id="820c3-220">In this topic, you learn how to use :::no-loc(Identity)::: to register, log in, and log out a user.</span></span> <span data-ttu-id="820c3-221">Kullanan uygulamalar oluşturma hakkında daha ayrıntılı yönergeler için :::no-loc(Identity)::: , bu makalenin sonundaki sonraki adımlar bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="820c3-221">For more detailed instructions about creating apps that use :::no-loc(Identity):::, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="820c3-222">AddDefault :::no-loc(Identity)::: ve Ekle:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="820c3-222">AddDefault:::no-loc(Identity)::: and Add:::no-loc(Identity):::</span></span>

<span data-ttu-id="820c3-223"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*>ASP.NET Core 2,1 ' de tanıtılmıştı.</span><span class="sxs-lookup"><span data-stu-id="820c3-223"><xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionUIExtensions.AddDefault:::no-loc(Identity):::*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="820c3-224">Çağırma `AddDefault:::no-loc(Identity):::` , aşağıdakileri çağırmaya benzer:</span><span class="sxs-lookup"><span data-stu-id="820c3-224">Calling `AddDefault:::no-loc(Identity):::` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.:::no-loc(Identity):::ServiceCollectionExtensions.Add:::no-loc(Identity):::*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.:::no-loc(Identity):::BuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="820c3-225">Daha fazla bilgi için bkz. [ADDDEFAULT :::no-loc(Identity)::: kaynağı](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) .</span><span class="sxs-lookup"><span data-stu-id="820c3-225">See [AddDefault:::no-loc(Identity)::: source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/:::no-loc(Identity):::/UI/src/:::no-loc(Identity):::ServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="820c3-226">Kimlik doğrulamasıyla bir Web uygulaması oluşturma</span><span class="sxs-lookup"><span data-stu-id="820c3-226">Create a Web app with authentication</span></span>

<span data-ttu-id="820c3-227">Bireysel kullanıcı hesaplarıyla bir ASP.NET Core Web uygulaması projesi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="820c3-227">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="820c3-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="820c3-228">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="820c3-229">**Dosya** > **Yeni** > **Proje**’yi seçin.</span><span class="sxs-lookup"><span data-stu-id="820c3-229">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="820c3-230">**ASP.NET Core Web uygulaması**' nı seçin.</span><span class="sxs-lookup"><span data-stu-id="820c3-230">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="820c3-231">Projeyi Proje **WebApp1** aynı ad alanına sahip olacak şekilde adlandırın.</span><span class="sxs-lookup"><span data-stu-id="820c3-231">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="820c3-232">**Tamam** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="820c3-232">Click **OK**.</span></span>
* <span data-ttu-id="820c3-233">Bir ASP.NET Core **Web uygulaması**seçip **kimlik doğrulamasını Değiştir**' i seçin.</span><span class="sxs-lookup"><span data-stu-id="820c3-233">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="820c3-234">**Bireysel kullanıcı hesapları** ' nı seçip **Tamam**' a tıklayın.</span><span class="sxs-lookup"><span data-stu-id="820c3-234">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="820c3-235">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="820c3-235">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="820c3-236">Oluşturulan proje bir [ :::no-loc(Razor)::: sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core :::no-loc(Identity)::: ](xref:security/authentication/identity) sağlar.</span><span class="sxs-lookup"><span data-stu-id="820c3-236">The generated project provides [ASP.NET Core :::no-loc(Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="820c3-237">:::no-loc(Identity)::: :::no-loc(Razor)::: Sınıf kitaplığı, alanı ile uç noktaları kullanıma sunar `:::no-loc(Identity):::` .</span><span class="sxs-lookup"><span data-stu-id="820c3-237">The :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library exposes endpoints with the `:::no-loc(Identity):::` area.</span></span> <span data-ttu-id="820c3-238">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="820c3-238">For example:</span></span>

* <span data-ttu-id="820c3-239">/:::no-loc(Identity):::/Account/Login</span><span class="sxs-lookup"><span data-stu-id="820c3-239">/:::no-loc(Identity):::/Account/Login</span></span>
* <span data-ttu-id="820c3-240">/:::no-loc(Identity):::/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="820c3-240">/:::no-loc(Identity):::/Account/Logout</span></span>
* <span data-ttu-id="820c3-241">/:::no-loc(Identity):::/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="820c3-241">/:::no-loc(Identity):::/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="820c3-242">Geçişleri Uygula</span><span class="sxs-lookup"><span data-stu-id="820c3-242">Apply migrations</span></span>

<span data-ttu-id="820c3-243">Veritabanını başlatmak için geçişleri uygulayın.</span><span class="sxs-lookup"><span data-stu-id="820c3-243">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="820c3-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="820c3-244">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="820c3-245">Paket Yöneticisi konsolunda aşağıdaki komutu çalıştırın (PMC):</span><span class="sxs-lookup"><span data-stu-id="820c3-245">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="820c3-246">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="820c3-246">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="820c3-247">Test kaydı ve oturum açma</span><span class="sxs-lookup"><span data-stu-id="820c3-247">Test Register and Login</span></span>

<span data-ttu-id="820c3-248">Uygulamayı çalıştırın ve bir Kullanıcı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="820c3-248">Run the app and register a user.</span></span> <span data-ttu-id="820c3-249">Ekran boyutunuza bağlı olarak, **kayıt** ve **oturum açma** bağlantılarını görmek için gezinti geçiş düğmesini seçmeniz gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="820c3-249">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="820c3-250">:::no-loc(Identity):::Hizmetleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="820c3-250">Configure :::no-loc(Identity)::: services</span></span>

<span data-ttu-id="820c3-251">Hizmetler ' de eklenir `ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="820c3-251">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="820c3-252">Tipik model, tüm yöntemleri çağırmalıdır `Add{Service}` ve sonra tüm `services.Configure{Service}` yöntemleri çağırır.</span><span class="sxs-lookup"><span data-stu-id="820c3-252">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="820c3-253">Önceki kod :::no-loc(Identity)::: varsayılan seçenek değerleriyle yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="820c3-253">The preceding code configures :::no-loc(Identity)::: with default option values.</span></span> <span data-ttu-id="820c3-254">Hizmetler, [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama için kullanılabilir hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="820c3-254">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="820c3-255">:::no-loc(Identity):::[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)çağırarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="820c3-255">:::no-loc(Identity)::: is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="820c3-256">`UseAuthentication`istek ardışık düzenine kimlik doğrulama [ara yazılımı](xref:fundamentals/middleware/index) ekler.</span><span class="sxs-lookup"><span data-stu-id="820c3-256">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="820c3-257">Daha fazla bilgi için bkz. [ :::no-loc(Identity)::: Options sınıfı](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) ve [uygulama başlatma](xref:fundamentals/startup).</span><span class="sxs-lookup"><span data-stu-id="820c3-257">For more information, see the [:::no-loc(Identity):::Options Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="820c3-258">Yapı iskelesi kaydı, oturum açma ve oturum kapatma</span><span class="sxs-lookup"><span data-stu-id="820c3-258">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="820c3-259">Bu bölümde gösterilen kodu oluşturmak için, [Yetkilendirme talimatlarına :::no-loc(Razor)::: sahip bir projede Scaffold kimliğini](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) izleyin.</span><span class="sxs-lookup"><span data-stu-id="820c3-259">Follow the [Scaffold identity into a :::no-loc(Razor)::: project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="820c3-260">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="820c3-260">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="820c3-261">Kayıt, oturum açma ve oturum kapatma dosyalarını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="820c3-261">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="820c3-262">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="820c3-262">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="820c3-263">Projeyi **WebApp1**adıyla oluşturduysanız aşağıdaki komutları çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="820c3-263">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="820c3-264">Aksi takdirde, için doğru ad alanını kullanın `ApplicationDbContext` :</span><span class="sxs-lookup"><span data-stu-id="820c3-264">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="820c3-265">PowerShell, bir komut ayırıcısı olarak noktalı virgül kullanır.</span><span class="sxs-lookup"><span data-stu-id="820c3-265">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="820c3-266">PowerShell kullanırken, dosya listesinde noktalı virgül karakterini kaçış veya dosya listesini, yukarıdaki örnekte gösterildiği gibi çift tırnak içine koyun.</span><span class="sxs-lookup"><span data-stu-id="820c3-266">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="820c3-267">Kaydı İncele</span><span class="sxs-lookup"><span data-stu-id="820c3-267">Examine Register</span></span>

<span data-ttu-id="820c3-268">Kullanıcı **Kaydet** bağlantısına tıkladığında `RegisterModel.OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="820c3-268">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="820c3-269">Kullanıcı, nesnesi üzerinde [Createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) tarafından oluşturulmuştur `_userManager` :</span><span class="sxs-lookup"><span data-stu-id="820c3-269">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_:::no-loc(Identity):::_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="820c3-270">Kullanıcı başarıyla oluşturulduysa, Kullanıcı çağrısıyla oturum açar `_signInManager.SignInAsync` .</span><span class="sxs-lookup"><span data-stu-id="820c3-270">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="820c3-271">**Note:** Kayıt sırasında anında oturum açmayı önlemeye yönelik adımlar için bkz. [Hesap onayı](xref:security/authentication/accconfirm#prevent-login-at-registration) .</span><span class="sxs-lookup"><span data-stu-id="820c3-271">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="820c3-272">Oturum açma</span><span class="sxs-lookup"><span data-stu-id="820c3-272">Log in</span></span>

<span data-ttu-id="820c3-273">Oturum açma formu şu durumlarda görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="820c3-273">The Login form is displayed when:</span></span>

* <span data-ttu-id="820c3-274">**Oturum aç** bağlantısı seçilidir.</span><span class="sxs-lookup"><span data-stu-id="820c3-274">The **Log in** link is selected.</span></span>
* <span data-ttu-id="820c3-275">Kullanıcı, erişim yetkisi olmayan **veya** sistem tarafından kimliği doğrulanmamış olan sınırlı bir sayfaya erişmeyi dener.</span><span class="sxs-lookup"><span data-stu-id="820c3-275">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="820c3-276">Oturum açma sayfasındaki form gönderildiğinde, `OnPostAsync` eylem çağrılır.</span><span class="sxs-lookup"><span data-stu-id="820c3-276">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="820c3-277">`PasswordSignInAsync`, nesne üzerinde çağrılır `_signInManager` .</span><span class="sxs-lookup"><span data-stu-id="820c3-277">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="820c3-278">Yetkilendirme kararlarının nasıl yapılacağı hakkında bilgi için bkz <xref:security/authorization/introduction> ..</span><span class="sxs-lookup"><span data-stu-id="820c3-278">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="820c3-279">Oturumu Kapat</span><span class="sxs-lookup"><span data-stu-id="820c3-279">Log out</span></span>

<span data-ttu-id="820c3-280">**Oturumu kapatma** bağlantısı `LogoutModel.OnPost` eylemi çağırır.</span><span class="sxs-lookup"><span data-stu-id="820c3-280">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/:::no-loc(Identity):::/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="820c3-281">[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) , kullanıcının tanımlama bilgisinde depolanan taleplerini temizler.</span><span class="sxs-lookup"><span data-stu-id="820c3-281">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="820c3-282">*Sayfa/paylaşılan/_LoginPartial. cshtml*'de gönderi belirtildi:</span><span class="sxs-lookup"><span data-stu-id="820c3-282">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a><span data-ttu-id="820c3-283">Sınamanız:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="820c3-283">Test :::no-loc(Identity):::</span></span>

<span data-ttu-id="820c3-284">Varsayılan Web projesi şablonları, giriş sayfalarına anonim erişime izin verir.</span><span class="sxs-lookup"><span data-stu-id="820c3-284">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="820c3-285">Test etmek için :::no-loc(Identity)::: [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) Gizlilik sayfasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="820c3-285">To test :::no-loc(Identity):::, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="820c3-286">Oturumunuz açık ise oturumu kapatın. Uygulamayı çalıştırın ve **Gizlilik** bağlantısını seçin.</span><span class="sxs-lookup"><span data-stu-id="820c3-286">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="820c3-287">Oturum açma sayfasına yönlendirilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="820c3-287">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="820c3-288">Explorer:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="820c3-288">Explore :::no-loc(Identity):::</span></span>

<span data-ttu-id="820c3-289">:::no-loc(Identity):::Daha ayrıntılı incelemek için:</span><span class="sxs-lookup"><span data-stu-id="820c3-289">To explore :::no-loc(Identity)::: in more detail:</span></span>

* [<span data-ttu-id="820c3-290">Tam kimlik UI kaynağı oluşturma</span><span class="sxs-lookup"><span data-stu-id="820c3-290">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="820c3-291">Her sayfanın kaynağını inceleyin ve hata ayıklayıcıda ilerleyin.</span><span class="sxs-lookup"><span data-stu-id="820c3-291">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="820c3-292">:::no-loc(Identity):::Bileşenleri</span><span class="sxs-lookup"><span data-stu-id="820c3-292">:::no-loc(Identity)::: Components</span></span>

<span data-ttu-id="820c3-293">Tüm :::no-loc(Identity)::: bağımlı NuGet paketleri [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app)'e dahildir.</span><span class="sxs-lookup"><span data-stu-id="820c3-293">All the :::no-loc(Identity)::: dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="820c3-294">İçin birincil paket :::no-loc(Identity)::: [Microsoft. aspnetcore :::no-loc(Identity)::: .](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/)..</span><span class="sxs-lookup"><span data-stu-id="820c3-294">The primary package for :::no-loc(Identity)::: is [Microsoft.AspNetCore.:::no-loc(Identity):::](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(Identity):::/).</span></span> <span data-ttu-id="820c3-295">Bu paket, ASP.NET Core için temel arabirim kümesini içerir :::no-loc(Identity)::: ve tarafından dahildir `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore` .</span><span class="sxs-lookup"><span data-stu-id="820c3-295">This package contains the core set of interfaces for ASP.NET Core :::no-loc(Identity):::, and is included by `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-no-locidentity"></a><span data-ttu-id="820c3-296">ASP.NET Core geçiriliyor:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="820c3-296">Migrating to ASP.NET Core :::no-loc(Identity):::</span></span>

<span data-ttu-id="820c3-297">Mevcut deponuzu geçirme hakkında daha fazla bilgi ve yönergeler için :::no-loc(Identity)::: bkz. [geçiş kimlik :::no-loc(Identity)::: doğrulaması ve ](xref:migration/identity).</span><span class="sxs-lookup"><span data-stu-id="820c3-297">For more information and guidance on migrating your existing :::no-loc(Identity)::: store, see [Migrate Authentication and :::no-loc(Identity):::](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="820c3-298">Parola gücünü ayarlama</span><span class="sxs-lookup"><span data-stu-id="820c3-298">Setting password strength</span></span>

<span data-ttu-id="820c3-299">Minimum parola gereksinimlerini ayarlayan bir örnek için bkz. [yapılandırma](#pw) .</span><span class="sxs-lookup"><span data-stu-id="820c3-299">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="820c3-300">Sonraki Adımlar</span><span class="sxs-lookup"><span data-stu-id="820c3-300">Next Steps</span></span>

* <span data-ttu-id="820c3-301">SQLite kullanarak yapılandırma hakkında bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/5131) bakın :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="820c3-301">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring :::no-loc(Identity)::: using SQLite.</span></span>
* [<span data-ttu-id="820c3-302">Yapılandırma:::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="820c3-302">Configure :::no-loc(Identity):::</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
