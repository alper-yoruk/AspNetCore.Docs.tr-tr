---
title: Identity ASP.NET Core projelerinde yapı iskelesi
author: rick-anderson
description: ASP.NET Core projesindeki fkatlama Identity oluşturmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 6f1ff69863e14c73e90496ea61188387f5267b19
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768396"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="4570c-103">Identity ASP.NET Core projelerinde yapı iskelesi</span><span class="sxs-lookup"><span data-stu-id="4570c-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="4570c-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4570c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4570c-105">ASP.NET Core [bir Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core Identity ](xref:security/authentication/identity) sağlar.</span><span class="sxs-lookup"><span data-stu-id="4570c-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="4570c-106">Dahil Identity edilen uygulamalar, Identity Razor sınıf kitaplığı 'nda (RCL) bulunan kaynak kodu seçmeli olarak eklemek için desteği uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="4570c-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="4570c-107">Kodu değiştirebilmeniz ve davranışı değiştirebilmek için kaynak kodu oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4570c-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="4570c-108">Örneğin, kayıt sırasında kullanılan kodu oluşturmak için desteği ' ı söyleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4570c-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="4570c-109">Oluşturulan kod Identity RCL 'deki aynı koda göre önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="4570c-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="4570c-110">Kullanıcı arabirimine tam denetim sağlamak ve varsayılan RCL 'yi kullanmak için, [tam KIMLIK UI kaynağı oluşturma](#full)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="4570c-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="4570c-111">Kimlik **doğrulaması içermeyen uygulamalar** , RCL Identity paketini eklemek için desteği uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="4570c-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="4570c-112">Oluşturulacak kodu seçme Identity seçeneğiniz vardır.</span><span class="sxs-lookup"><span data-stu-id="4570c-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="4570c-113">Desteği gerekli kodların çoğunu üretse de, işlemi gerçekleştirmek için projenizi güncelleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="4570c-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="4570c-114">Bu belgede, bir Identity yapı iskelesi güncelleştirmesini tamamlaması için gereken adımlar açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="4570c-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="4570c-115">Dosya farklılıklarını gösteren ve değişikliklerden geri dönüş yapmanızı sağlayan bir kaynak denetimi sistemi kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="4570c-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="4570c-116">Identity Scaffolder çalıştırıldıktan sonra değişiklikleri inceleyin.</span><span class="sxs-lookup"><span data-stu-id="4570c-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="4570c-117">[Iki öğeli kimlik doğrulaması](xref:security/authentication/identity-enable-qrcodes), [Hesap onaylama ve parola kurtarma](xref:security/authentication/accconfirm)ile diğer güvenlik özellikleri kullanılırken hizmetler gereklidir Identity.</span><span class="sxs-lookup"><span data-stu-id="4570c-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="4570c-118">Hizmetler veya hizmet saplamaları, yapı iskelesi Identitysırasında oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="4570c-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="4570c-119">Bu özelliklerin etkinleştirilmesi için hizmetlerin el ile eklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="4570c-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="4570c-120">Örneğin, bkz. [e-posta onayı gerektir](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="4570c-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="4570c-121">Yeni bir veri Identity bağlamıyla, var olan bireysel hesaplara sahip bir projeye dönüştürme yaparken:</span><span class="sxs-lookup"><span data-stu-id="4570c-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="4570c-122">İçinde `Startup.ConfigureServices`, şu çağrıları kaldırın:</span><span class="sxs-lookup"><span data-stu-id="4570c-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="4570c-123">Örneğin, `AddDbContext` ve `AddDefaultIdentity` aşağıdaki kodda yorum yapılır:</span><span class="sxs-lookup"><span data-stu-id="4570c-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="4570c-124">Önceki kod, alanlarda çoğaltılan kodu Yorumlar *Identity//IdentityHostingStartup.cs*</span><span class="sxs-lookup"><span data-stu-id="4570c-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="4570c-125">Genellikle, bireysel hesaplarla oluşturulan uygulamalar yeni bir veri bağlamı ***oluşturmamalıdır*** .</span><span class="sxs-lookup"><span data-stu-id="4570c-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="4570c-126">Boş bir projede yapı iskelesi kimliği</span><span class="sxs-lookup"><span data-stu-id="4570c-126">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="4570c-127">`Startup` Sınıfını aşağıdakine benzer kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="4570c-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="4570c-128">Var olan yetkilendirme olmadan bir projede Razor kimlik oluşturma kimliği</span><span class="sxs-lookup"><span data-stu-id="4570c-128">Scaffold identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="4570c-129">, */Identity/IdentityHostingStartup.cs alanlarında*yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="4570c-129"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="4570c-130">daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="4570c-130">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="4570c-131">Geçişler, UseAuthentication ve düzen</span><span class="sxs-lookup"><span data-stu-id="4570c-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="4570c-132">Kimlik doğrulamasını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="4570c-132">Enable authentication</span></span>

<span data-ttu-id="4570c-133">`Startup` Sınıfını aşağıdakine benzer kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="4570c-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="4570c-134">Düzen değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="4570c-134">Layout changes</span></span>

<span data-ttu-id="4570c-135">İsteğe bağlı: (`_LoginPartial`) oturum açma bilgilerini düzen dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="4570c-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="4570c-136">Yetkilendirmeyle kimliği bir Razor projede yapı iskelesi</span><span class="sxs-lookup"><span data-stu-id="4570c-136">Scaffold identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
<span data-ttu-id="4570c-137">Bazı Identity Seçenekler */Identity/IdentityHostingStartup.cs alanlarında*yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="4570c-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="4570c-138">Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="4570c-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="4570c-139">Var olan yetkilendirme olmadan bir MVC projesinde kimlik oluşturma kimliği</span><span class="sxs-lookup"><span data-stu-id="4570c-139">Scaffold identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="4570c-140">İsteğe bağlı: bir Login (`_LoginPartial`) öğesini *views/Shared/_Layout. cshtml* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="4570c-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="4570c-141">*Pages/Shared/_LoginPartial. cshtml* dosyasını *views/Shared/_LoginPartial. cshtml* olarak taşıyın</span><span class="sxs-lookup"><span data-stu-id="4570c-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="4570c-142">, */Identity/IdentityHostingStartup.cs alanlarında*yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="4570c-142"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="4570c-143">Daha fazla bilgi için bkz. ıhostingstartup.</span><span class="sxs-lookup"><span data-stu-id="4570c-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="4570c-144">`Startup` Sınıfını aşağıdakine benzer kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="4570c-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="4570c-145">Yetkilendirmeyi içeren bir MVC projesinde kimlik oluşturma kimliği</span><span class="sxs-lookup"><span data-stu-id="4570c-145">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="4570c-146">Tam kimlik UI kaynağı oluşturma</span><span class="sxs-lookup"><span data-stu-id="4570c-146">Create full identity UI source</span></span>

<span data-ttu-id="4570c-147">Identity Kullanıcı arabiriminin tam denetimini sürdürmek için, Identity desteği ' ı çalıştırın ve **tüm dosyaları geçersiz kıl**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4570c-147">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="4570c-148">Aşağıdaki vurgulanan kod, varsayılan Identity kullanıcı arabirimini ASP.NET Core 2,1 Web Identity uygulamasında değiştirecek değişiklikleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="4570c-148">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="4570c-149">Bunu, Identity Kullanıcı arabirimine tam denetim sağlamak için yapmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4570c-149">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="4570c-150">Varsayılan değer Identity aşağıdaki kodda değiştirilmiştir:</span><span class="sxs-lookup"><span data-stu-id="4570c-150">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="4570c-151">Aşağıdaki kod, [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)ve [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)öğesini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="4570c-151">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="4570c-152">Bir `IEmailSender` uygulamayı kaydedin, örneğin:</span><span class="sxs-lookup"><span data-stu-id="4570c-152">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="4570c-153">Kayıt sayfasını devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="4570c-153">Disable register page</span></span>

<span data-ttu-id="4570c-154">Kullanıcı kaydını devre dışı bırakmak için:</span><span class="sxs-lookup"><span data-stu-id="4570c-154">To disable user registration:</span></span>

* <span data-ttu-id="4570c-155">Yapı iskelesi Identity.</span><span class="sxs-lookup"><span data-stu-id="4570c-155">Scaffold Identity.</span></span> <span data-ttu-id="4570c-156">Account. Register, Account. Login ve account. RegisterConfirmation bilgilerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4570c-156">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="4570c-157">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4570c-157">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="4570c-158">Kullanıcıları bu uç noktadan kaydedememesi için *alanı/Identity/Pages/Account/Register.cshtml.cs* güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="4570c-158">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="4570c-159">Önceki değişikliklerle tutarlı olması için *IdentityAreas//Pages/Account/Register.exe* öğesini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="4570c-159">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="4570c-160">*Identity//Pages/Account/Login.exe* içindeki kayıt bağlantısını açıklama veya Kaldır</span><span class="sxs-lookup"><span data-stu-id="4570c-160">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="4570c-161">*IdentityAreas//Pages/Account/registerconfirmation* sayfasını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="4570c-161">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="4570c-162">Cshtml dosyasındaki kodu ve bağlantıları kaldırın.</span><span class="sxs-lookup"><span data-stu-id="4570c-162">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="4570c-163">Onay kodunu şuradan kaldırın `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="4570c-163">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="4570c-164">Kullanıcıları eklemek için başka bir uygulama kullanma</span><span class="sxs-lookup"><span data-stu-id="4570c-164">Use another app to add users</span></span>

<span data-ttu-id="4570c-165">Web uygulaması dışındaki kullanıcıları eklemek için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="4570c-165">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="4570c-166">Kullanıcı ekleme seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="4570c-166">Options to add users include:</span></span>

* <span data-ttu-id="4570c-167">Adanmış bir yönetim Web uygulaması.</span><span class="sxs-lookup"><span data-stu-id="4570c-167">A dedicated admin web app.</span></span>
* <span data-ttu-id="4570c-168">Bir konsol uygulaması.</span><span class="sxs-lookup"><span data-stu-id="4570c-168">A console app.</span></span>

<span data-ttu-id="4570c-169">Aşağıdaki kod, kullanıcı eklemeye yönelik bir yaklaşımı özetler:</span><span class="sxs-lookup"><span data-stu-id="4570c-169">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="4570c-170">Kullanıcıların listesi belleği okur.</span><span class="sxs-lookup"><span data-stu-id="4570c-170">A list of users is read into memory.</span></span>
* <span data-ttu-id="4570c-171">Her Kullanıcı için güçlü bir benzersiz parola oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="4570c-171">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="4570c-172">Kullanıcı Identity veritabanına eklenir.</span><span class="sxs-lookup"><span data-stu-id="4570c-172">The user is added to the Identity database.</span></span>
* <span data-ttu-id="4570c-173">Kullanıcıya bildirilir ve parolayı değiştirmesi bildirilir.</span><span class="sxs-lookup"><span data-stu-id="4570c-173">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="4570c-174">Aşağıdaki kod, bir kullanıcı ekleme ana hatlarıyla verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="4570c-174">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="4570c-175">Üretim senaryolarında de benzer bir yaklaşım izlenebilir.</span><span class="sxs-lookup"><span data-stu-id="4570c-175">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="4570c-176">Statik Identity varlıkların yayımlanmasını engelle</span><span class="sxs-lookup"><span data-stu-id="4570c-176">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="4570c-177">Statik Identity varlıkların Web köküne yayımlanmasını engellemek için bkz <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>..</span><span class="sxs-lookup"><span data-stu-id="4570c-177">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4570c-178">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="4570c-178">Additional resources</span></span>

* [<span data-ttu-id="4570c-179">ASP.NET Core 2,1 ve üzeri kimlik doğrulama kodundaki değişiklikler</span><span class="sxs-lookup"><span data-stu-id="4570c-179">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4570c-180">ASP.NET Core 2,1 ve üzeri bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core Identity ](xref:security/authentication/identity) sağlar.</span><span class="sxs-lookup"><span data-stu-id="4570c-180">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="4570c-181">Dahil Identity edilen uygulamalar, Identity Razor sınıf kitaplığı 'nda (RCL) bulunan kaynak kodu seçmeli olarak eklemek için desteği uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="4570c-181">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="4570c-182">Kodu değiştirebilmeniz ve davranışı değiştirebilmek için kaynak kodu oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4570c-182">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="4570c-183">Örneğin, kayıt sırasında kullanılan kodu oluşturmak için desteği ' ı söyleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4570c-183">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="4570c-184">Oluşturulan kod Identity RCL 'deki aynı koda göre önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="4570c-184">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="4570c-185">Kullanıcı arabirimine tam denetim sağlamak ve varsayılan RCL 'yi kullanmak için, [tam KIMLIK UI kaynağı oluşturma](#full)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="4570c-185">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="4570c-186">Kimlik **doğrulaması içermeyen uygulamalar** , RCL Identity paketini eklemek için desteği uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="4570c-186">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="4570c-187">Oluşturulacak kodu seçme Identity seçeneğiniz vardır.</span><span class="sxs-lookup"><span data-stu-id="4570c-187">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="4570c-188">Desteği gerekli kodların çoğunu üretse de, işlemi gerçekleştirmek için projenizi güncelleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="4570c-188">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="4570c-189">Bu belgede, bir Identity yapı iskelesi güncelleştirmesini tamamlaması için gereken adımlar açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="4570c-189">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="4570c-190">Identity Desteği çalıştırıldığında, proje dizininde bir *scaffoldingreadme. txt* dosyası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="4570c-190">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="4570c-191">*Scaffoldingreadme. txt* dosyası, Identity yapı iskelesi güncelleştirmesini tamamlamaya yönelik nelerin gerekli olduğuna ilişkin genel yönergeleri içerir.</span><span class="sxs-lookup"><span data-stu-id="4570c-191">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="4570c-192">Bu belge, *Scaffoldingreadme. txt* dosyasından daha eksiksiz yönergeler içerir.</span><span class="sxs-lookup"><span data-stu-id="4570c-192">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="4570c-193">Dosya farklılıklarını gösteren ve değişikliklerden geri dönüş yapmanızı sağlayan bir kaynak denetimi sistemi kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="4570c-193">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="4570c-194">Identity Scaffolder çalıştırıldıktan sonra değişiklikleri inceleyin.</span><span class="sxs-lookup"><span data-stu-id="4570c-194">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="4570c-195">[Iki öğeli kimlik doğrulaması](xref:security/authentication/identity-enable-qrcodes), [Hesap onaylama ve parola kurtarma](xref:security/authentication/accconfirm)ile diğer güvenlik özellikleri kullanılırken hizmetler gereklidir Identity.</span><span class="sxs-lookup"><span data-stu-id="4570c-195">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="4570c-196">Hizmetler veya hizmet saplamaları, yapı iskelesi Identitysırasında oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="4570c-196">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="4570c-197">Bu özelliklerin etkinleştirilmesi için hizmetlerin el ile eklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="4570c-197">Services to enable these features must be added manually.</span></span> <span data-ttu-id="4570c-198">Örneğin, bkz. [e-posta onayı gerektir](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="4570c-198">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="4570c-199">Boş bir projede yapı iskelesi kimliği</span><span class="sxs-lookup"><span data-stu-id="4570c-199">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="4570c-200">Aşağıdaki Vurgulanan çağrıları `Startup` sınıfına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="4570c-200">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="4570c-201">Var olan yetkilendirme olmadan bir projede Razor kimlik oluşturma kimliği</span><span class="sxs-lookup"><span data-stu-id="4570c-201">Scaffold identity into a Razor project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="4570c-202">, */Identity/IdentityHostingStartup.cs alanlarında*yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="4570c-202"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="4570c-203">daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="4570c-203">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="4570c-204">Geçişler, UseAuthentication ve düzen</span><span class="sxs-lookup"><span data-stu-id="4570c-204">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="4570c-205">Kimlik doğrulamasını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="4570c-205">Enable authentication</span></span>

<span data-ttu-id="4570c-206">`Startup` Sınıfının `Configure` yönteminde, sonrasında `UseStaticFiles` [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ' ı çağırın:</span><span class="sxs-lookup"><span data-stu-id="4570c-206">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="4570c-207">Düzen değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="4570c-207">Layout changes</span></span>

<span data-ttu-id="4570c-208">İsteğe bağlı: (`_LoginPartial`) oturum açma bilgilerini düzen dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="4570c-208">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="4570c-209">Yetkilendirmeyle kimliği bir Razor projede yapı iskelesi</span><span class="sxs-lookup"><span data-stu-id="4570c-209">Scaffold identity into a Razor project with authorization</span></span>

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
<span data-ttu-id="4570c-210">Bazı Identity Seçenekler */Identity/IdentityHostingStartup.cs alanlarında*yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="4570c-210">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="4570c-211">Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="4570c-211">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="4570c-212">Var olan yetkilendirme olmadan bir MVC projesinde kimlik oluşturma kimliği</span><span class="sxs-lookup"><span data-stu-id="4570c-212">Scaffold identity into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="4570c-213">İsteğe bağlı: bir Login (`_LoginPartial`) öğesini *views/Shared/_Layout. cshtml* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="4570c-213">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="4570c-214">*Pages/Shared/_LoginPartial. cshtml* dosyasını *views/Shared/_LoginPartial. cshtml* olarak taşıyın</span><span class="sxs-lookup"><span data-stu-id="4570c-214">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="4570c-215">, */Identity/IdentityHostingStartup.cs alanlarında*yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="4570c-215"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="4570c-216">Daha fazla bilgi için bkz. ıhostingstartup.</span><span class="sxs-lookup"><span data-stu-id="4570c-216">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="4570c-217">Şu tarihten sonra `UseStaticFiles` [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) çağrısı:</span><span class="sxs-lookup"><span data-stu-id="4570c-217">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="4570c-218">Yetkilendirmeyi içeren bir MVC projesinde kimlik oluşturma kimliği</span><span class="sxs-lookup"><span data-stu-id="4570c-218">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="4570c-219">*Sayfaları/paylaşılan* klasörünü ve bu klasördeki dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="4570c-219">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="4570c-220">Tam kimlik UI kaynağı oluşturma</span><span class="sxs-lookup"><span data-stu-id="4570c-220">Create full identity UI source</span></span>

<span data-ttu-id="4570c-221">Identity Kullanıcı arabiriminin tam denetimini sürdürmek için, Identity desteği ' ı çalıştırın ve **tüm dosyaları geçersiz kıl**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="4570c-221">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="4570c-222">Aşağıdaki vurgulanan kod, varsayılan Identity kullanıcı arabirimini ASP.NET Core 2,1 Web Identity uygulamasında değiştirecek değişiklikleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="4570c-222">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="4570c-223">Bunu, Identity Kullanıcı arabirimine tam denetim sağlamak için yapmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="4570c-223">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="4570c-224">Varsayılan değer Identity aşağıdaki kodda değiştirilmiştir:</span><span class="sxs-lookup"><span data-stu-id="4570c-224">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="4570c-225">Aşağıdaki kod, [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)ve [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)öğesini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="4570c-225">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="4570c-226">Bir `IEmailSender` uygulamayı kaydedin, örneğin:</span><span class="sxs-lookup"><span data-stu-id="4570c-226">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="4570c-227">Kayıt sayfasını devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="4570c-227">Disable register page</span></span>

<span data-ttu-id="4570c-228">Kullanıcı kaydını devre dışı bırakmak için:</span><span class="sxs-lookup"><span data-stu-id="4570c-228">To disable user registration:</span></span>

* <span data-ttu-id="4570c-229">Yapı iskelesi Identity.</span><span class="sxs-lookup"><span data-stu-id="4570c-229">Scaffold Identity.</span></span> <span data-ttu-id="4570c-230">Account. Register, Account. Login ve account. RegisterConfirmation bilgilerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4570c-230">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="4570c-231">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="4570c-231">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="4570c-232">Kullanıcıları bu uç noktadan kaydedememesi için *alanı/Identity/Pages/Account/Register.cshtml.cs* güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="4570c-232">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="4570c-233">Önceki değişikliklerle tutarlı olması için *IdentityAreas//Pages/Account/Register.exe* öğesini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="4570c-233">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="4570c-234">*Identity//Pages/Account/Login.exe* içindeki kayıt bağlantısını açıklama veya Kaldır</span><span class="sxs-lookup"><span data-stu-id="4570c-234">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="4570c-235">*IdentityAreas//Pages/Account/registerconfirmation* sayfasını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="4570c-235">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="4570c-236">Cshtml dosyasındaki kodu ve bağlantıları kaldırın.</span><span class="sxs-lookup"><span data-stu-id="4570c-236">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="4570c-237">Onay kodunu şuradan kaldırın `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="4570c-237">Remove the confirmation code from the `PageModel`:</span></span>

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="4570c-238">Kullanıcıları eklemek için başka bir uygulama kullanma</span><span class="sxs-lookup"><span data-stu-id="4570c-238">Use another app to add users</span></span>

<span data-ttu-id="4570c-239">Web uygulaması dışındaki kullanıcıları eklemek için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="4570c-239">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="4570c-240">Kullanıcı ekleme seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="4570c-240">Options to add users include:</span></span>

* <span data-ttu-id="4570c-241">Adanmış bir yönetim Web uygulaması.</span><span class="sxs-lookup"><span data-stu-id="4570c-241">A dedicated admin web app.</span></span>
* <span data-ttu-id="4570c-242">Bir konsol uygulaması.</span><span class="sxs-lookup"><span data-stu-id="4570c-242">A console app.</span></span>

<span data-ttu-id="4570c-243">Aşağıdaki kod, kullanıcı eklemeye yönelik bir yaklaşımı özetler:</span><span class="sxs-lookup"><span data-stu-id="4570c-243">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="4570c-244">Kullanıcıların listesi belleği okur.</span><span class="sxs-lookup"><span data-stu-id="4570c-244">A list of users is read into memory.</span></span>
* <span data-ttu-id="4570c-245">Her Kullanıcı için güçlü bir benzersiz parola oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="4570c-245">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="4570c-246">Kullanıcı Identity veritabanına eklenir.</span><span class="sxs-lookup"><span data-stu-id="4570c-246">The user is added to the Identity database.</span></span>
* <span data-ttu-id="4570c-247">Kullanıcıya bildirilir ve parolayı değiştirmesi bildirilir.</span><span class="sxs-lookup"><span data-stu-id="4570c-247">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="4570c-248">Aşağıdaki kod, bir kullanıcı ekleme ana hatlarıyla verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="4570c-248">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="4570c-249">Üretim senaryolarında de benzer bir yaklaşım izlenebilir.</span><span class="sxs-lookup"><span data-stu-id="4570c-249">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4570c-250">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="4570c-250">Additional resources</span></span>

* [<span data-ttu-id="4570c-251">ASP.NET Core 2,1 ve üzeri kimlik doğrulama kodundaki değişiklikler</span><span class="sxs-lookup"><span data-stu-id="4570c-251">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end