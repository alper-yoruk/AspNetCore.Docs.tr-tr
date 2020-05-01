---
title: ASP.NET Core projelerinde yapı iskelesi kimliği
author: rick-anderson
description: ASP.NET Core projesindeki kimliği nasıl yapılandıracağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
uid: security/authentication/scaffold-identity
ms.openlocfilehash: ac95035b114274ddaa6ccb0b5b6e3da98885e39e
ms.sourcegitcommit: 6318d2bdd63116e178c34492a904be85ec9ac108
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82604733"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="0cbdd-103">ASP.NET Core projelerinde yapı iskelesi kimliği</span><span class="sxs-lookup"><span data-stu-id="0cbdd-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="0cbdd-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0cbdd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0cbdd-105">ASP.NET Core [Razor sınıfı kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core kimliği](xref:security/authentication/identity) sağlar.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="0cbdd-106">Kimlik içeren uygulamalar, Identity Razor sınıf kitaplığı 'nda (RCL) bulunan kaynak kodu seçmeli olarak eklemek için desteği 'ı uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="0cbdd-107">Kodu değiştirebilmeniz ve davranışı değiştirebilmek için kaynak kodu oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="0cbdd-108">Örneğin, kayıt sırasında kullanılan kodu oluşturmak için desteği ' ı söyleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="0cbdd-109">Oluşturulan kod, RCL kimliği içindeki aynı koddan önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="0cbdd-110">Kullanıcı arabirimine tam denetim sağlamak ve varsayılan RCL 'yi kullanmak için, [tam KIMLIK UI kaynağı oluşturma](#full)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="0cbdd-111">Kimlik **doğrulaması içermeyen uygulamalar** , RCL kimlik paketini eklemek için desteği uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="0cbdd-112">Oluşturulacak kimlik kodunu seçme seçeneğiniz vardır.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="0cbdd-113">Desteği gerekli kodların çoğunu üretse de, işlemi gerçekleştirmek için projenizi güncelleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="0cbdd-114">Bu belgede, bir kimlik yapı iskelesi güncelleştirmesini tamamlaması için gereken adımlar açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="0cbdd-115">Dosya farklılıklarını gösteren ve değişikliklerden geri dönüş yapmanızı sağlayan bir kaynak denetimi sistemi kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="0cbdd-116">Kimlik scaffolder 'ı çalıştırdıktan sonra değişiklikleri inceleyin.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="0cbdd-117">[Iki öğeli kimlik doğrulaması](xref:security/authentication/identity-enable-qrcodes), [Hesap onaylama ve parola kurtarma](xref:security/authentication/accconfirm)ve kimlik ile diğer güvenlik özellikleri kullanılırken hizmetler gereklidir.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="0cbdd-118">Hizmet veya hizmet saplamaları, kimliğe iskele oluştururken oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="0cbdd-119">Bu özelliklerin etkinleştirilmesi için hizmetlerin el ile eklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="0cbdd-120">Örneğin, bkz. [e-posta onayı gerektir](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="0cbdd-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="0cbdd-121">Yeni bir veri bağlamı olan kimliği, mevcut bireysel hesaplara sahip bir projeye dönüştürmek için:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="0cbdd-122">İçinde `Startup.ConfigureServices`, şu çağrıları kaldırın:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="0cbdd-123">Örneğin, `AddDbContext` ve `AddDefaultIdentity` aşağıdaki kodda yorum yapılır:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="0cbdd-124">Önceki kod, *alanlarda/Identity/ıdentityhostingstartup. cs* dosyasında çoğaltılan kodu Yorumlar</span><span class="sxs-lookup"><span data-stu-id="0cbdd-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="0cbdd-125">Genellikle, bireysel hesaplarla oluşturulan uygulamalar yeni bir veri bağlamı ***oluşturmamalıdır*** .</span><span class="sxs-lookup"><span data-stu-id="0cbdd-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="0cbdd-126">Boş bir projede yapı iskelesi kimliği</span><span class="sxs-lookup"><span data-stu-id="0cbdd-126">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="0cbdd-127">`Startup` Sınıfını aşağıdakine benzer kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="0cbdd-128">Mevcut yetkilendirme olmadan bir Razor projesinde kimlik oluşturma kimliği</span><span class="sxs-lookup"><span data-stu-id="0cbdd-128">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="0cbdd-129">Kimlik, */kimlik/ıdentityhostingstartup. cs alanlarında*yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-129">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="0cbdd-130">daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="0cbdd-130">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="0cbdd-131">Geçişler, UseAuthentication ve düzen</span><span class="sxs-lookup"><span data-stu-id="0cbdd-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="0cbdd-132">Kimlik doğrulamasını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="0cbdd-132">Enable authentication</span></span>

<span data-ttu-id="0cbdd-133">`Startup` Sınıfını aşağıdakine benzer kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="0cbdd-134">Düzen değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="0cbdd-134">Layout changes</span></span>

<span data-ttu-id="0cbdd-135">İsteğe bağlı: (`_LoginPartial`) oturum açma bilgilerini düzen dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="0cbdd-136">Yetkilendirmeyi içeren bir Razor projesinde kimlik oluşturma kimliği</span><span class="sxs-lookup"><span data-stu-id="0cbdd-136">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="0cbdd-137">Bazı kimlik seçenekleri */Identity/ıdentityhostingstartup. cs alanlarında*yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="0cbdd-138">Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="0cbdd-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="0cbdd-139">Var olan yetkilendirme olmadan bir MVC projesinde kimlik oluşturma kimliği</span><span class="sxs-lookup"><span data-stu-id="0cbdd-139">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="0cbdd-140">İsteğe bağlı: bir Login (`_LoginPartial`) öğesini *views/Shared/_Layout. cshtml* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="0cbdd-141">*Pages/Shared/_LoginPartial. cshtml* dosyasını *views/Shared/_LoginPartial. cshtml* olarak taşıyın</span><span class="sxs-lookup"><span data-stu-id="0cbdd-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="0cbdd-142">Kimlik, */kimlik/ıdentityhostingstartup. cs alanlarında*yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-142">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="0cbdd-143">Daha fazla bilgi için bkz. ıhostingstartup.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="0cbdd-144">`Startup` Sınıfını aşağıdakine benzer kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="0cbdd-145">Yetkilendirmeyi içeren bir MVC projesinde kimlik oluşturma kimliği</span><span class="sxs-lookup"><span data-stu-id="0cbdd-145">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="0cbdd-146">Tam kimlik UI kaynağı oluşturma</span><span class="sxs-lookup"><span data-stu-id="0cbdd-146">Create full identity UI source</span></span>

<span data-ttu-id="0cbdd-147">Kimlik Kullanıcı arabirimine tam denetim sağlamak için, Identity desteği ' ı çalıştırın ve **tüm dosyaları geçersiz kıl**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-147">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="0cbdd-148">Aşağıdaki Vurgulanan kodda, varsayılan kimlik Kullanıcı arabirimini ASP.NET Core 2,1 Web uygulamasındaki kimlikle değiştirme değişiklikleri gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-148">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="0cbdd-149">Bunu, kimlik Kullanıcı arabirimine tam denetim sağlamak için yapmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-149">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="0cbdd-150">Varsayılan kimlik aşağıdaki kodda değiştirilmiştir:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-150">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="0cbdd-151">Aşağıdaki kod, [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)ve [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)öğesini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-151">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="0cbdd-152">Bir `IEmailSender` uygulamayı kaydedin, örneğin:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-152">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="0cbdd-153">Kayıt sayfasını devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="0cbdd-153">Disable register page</span></span>

<span data-ttu-id="0cbdd-154">Kullanıcı kaydını devre dışı bırakmak için:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-154">To disable user registration:</span></span>

* <span data-ttu-id="0cbdd-155">Yapı iskelesi kimliği.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-155">Scaffold Identity.</span></span> <span data-ttu-id="0cbdd-156">Account. Register, Account. Login ve account. RegisterConfirmation bilgilerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-156">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="0cbdd-157">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-157">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="0cbdd-158">Kullanıcıları bu uç noktadan kaydedememesi için *alan/kimlik/sayfa/hesap/kayıt. cshtml. cs* 'yi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-158">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="0cbdd-159">Önceki değişikliklerle tutarlı olması için *alan/kimlik/sayfa/hesap/kayıt. cshtml* 'yi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-159">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="0cbdd-160">*Alan/kimlik/sayfa/hesap/Login. cshtml* 'den kayıt bağlantısını açıklama veya kaldırma</span><span class="sxs-lookup"><span data-stu-id="0cbdd-160">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="0cbdd-161">*Bölgeler/kimlik/sayfalar/hesap/RegisterConfirmation* sayfasını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-161">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="0cbdd-162">Cshtml dosyasındaki kodu ve bağlantıları kaldırın.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-162">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="0cbdd-163">Onay kodunu şuradan kaldırın `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-163">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="0cbdd-164">Kullanıcıları eklemek için başka bir uygulama kullanma</span><span class="sxs-lookup"><span data-stu-id="0cbdd-164">Use another app to add users</span></span>

<span data-ttu-id="0cbdd-165">Web uygulaması dışındaki kullanıcıları eklemek için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-165">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="0cbdd-166">Kullanıcı ekleme seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-166">Options to add users include:</span></span>

* <span data-ttu-id="0cbdd-167">Adanmış bir yönetim Web uygulaması.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-167">A dedicated admin web app.</span></span>
* <span data-ttu-id="0cbdd-168">Bir konsol uygulaması.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-168">A console app.</span></span>

<span data-ttu-id="0cbdd-169">Aşağıdaki kod, kullanıcı eklemeye yönelik bir yaklaşımı özetler:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-169">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="0cbdd-170">Kullanıcıların listesi belleği okur.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-170">A list of users is read into memory.</span></span>
* <span data-ttu-id="0cbdd-171">Her Kullanıcı için güçlü bir benzersiz parola oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-171">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="0cbdd-172">Kullanıcı, kimlik veritabanına eklenir.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-172">The user is added to the Identity database.</span></span>
* <span data-ttu-id="0cbdd-173">Kullanıcıya bildirilir ve parolayı değiştirmesi bildirilir.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-173">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="0cbdd-174">Aşağıdaki kod, bir kullanıcı ekleme ana hatlarıyla verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-174">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="0cbdd-175">Üretim senaryolarında de benzer bir yaklaşım izlenebilir.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-175">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="0cbdd-176">Statik kimlik varlıklarının yayımlanmasını engelle</span><span class="sxs-lookup"><span data-stu-id="0cbdd-176">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="0cbdd-177">Statik kimlik varlıklarının Web köküne yayımlanmasını engellemek için bkz <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>..</span><span class="sxs-lookup"><span data-stu-id="0cbdd-177">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0cbdd-178">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0cbdd-178">Additional resources</span></span>

* [<span data-ttu-id="0cbdd-179">ASP.NET Core 2,1 ve üzeri kimlik doğrulama kodundaki değişiklikler</span><span class="sxs-lookup"><span data-stu-id="0cbdd-179">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0cbdd-180">ASP.NET Core 2,1 ve üzeri, [Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core kimliği](xref:security/authentication/identity) sağlar.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-180">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="0cbdd-181">Kimlik içeren uygulamalar, Identity Razor sınıf kitaplığı 'nda (RCL) bulunan kaynak kodu seçmeli olarak eklemek için desteği 'ı uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-181">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="0cbdd-182">Kodu değiştirebilmeniz ve davranışı değiştirebilmek için kaynak kodu oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-182">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="0cbdd-183">Örneğin, kayıt sırasında kullanılan kodu oluşturmak için desteği ' ı söyleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-183">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="0cbdd-184">Oluşturulan kod, RCL kimliği içindeki aynı koddan önceliklidir.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-184">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="0cbdd-185">Kullanıcı arabirimine tam denetim sağlamak ve varsayılan RCL 'yi kullanmak için, [tam KIMLIK UI kaynağı oluşturma](#full)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-185">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="0cbdd-186">Kimlik **doğrulaması içermeyen uygulamalar** , RCL kimlik paketini eklemek için desteği uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-186">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="0cbdd-187">Oluşturulacak kimlik kodunu seçme seçeneğiniz vardır.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-187">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="0cbdd-188">Desteği gerekli kodların çoğunu üretse de, işlemi gerçekleştirmek için projenizi güncelleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-188">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="0cbdd-189">Bu belgede, bir kimlik yapı iskelesi güncelleştirmesini tamamlaması için gereken adımlar açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-189">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="0cbdd-190">Identity desteği çalıştırıldığında, proje dizininde bir *scaffoldingreadme. txt* dosyası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-190">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="0cbdd-191">*Scaffoldingreadme. txt* dosyası, kimlik yapı iskelesi güncelleştirmesinin tamamlanabilmesi için gerekli olan genel yönergeleri içerir.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-191">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="0cbdd-192">Bu belge, *Scaffoldingreadme. txt* dosyasından daha eksiksiz yönergeler içerir.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-192">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="0cbdd-193">Dosya farklılıklarını gösteren ve değişikliklerden geri dönüş yapmanızı sağlayan bir kaynak denetimi sistemi kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-193">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="0cbdd-194">Kimlik scaffolder 'ı çalıştırdıktan sonra değişiklikleri inceleyin.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-194">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="0cbdd-195">[Iki öğeli kimlik doğrulaması](xref:security/authentication/identity-enable-qrcodes), [Hesap onaylama ve parola kurtarma](xref:security/authentication/accconfirm)ve kimlik ile diğer güvenlik özellikleri kullanılırken hizmetler gereklidir.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-195">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="0cbdd-196">Hizmet veya hizmet saplamaları, kimliğe iskele oluştururken oluşturulmaz.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-196">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="0cbdd-197">Bu özelliklerin etkinleştirilmesi için hizmetlerin el ile eklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-197">Services to enable these features must be added manually.</span></span> <span data-ttu-id="0cbdd-198">Örneğin, bkz. [e-posta onayı gerektir](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="0cbdd-198">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="0cbdd-199">Boş bir projede yapı iskelesi kimliği</span><span class="sxs-lookup"><span data-stu-id="0cbdd-199">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="0cbdd-200">Aşağıdaki Vurgulanan çağrıları `Startup` sınıfına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-200">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="0cbdd-201">Mevcut yetkilendirme olmadan bir Razor projesinde kimlik oluşturma kimliği</span><span class="sxs-lookup"><span data-stu-id="0cbdd-201">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="0cbdd-202">Kimlik, */kimlik/ıdentityhostingstartup. cs alanlarında*yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-202">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="0cbdd-203">daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="0cbdd-203">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="0cbdd-204">Geçişler, UseAuthentication ve düzen</span><span class="sxs-lookup"><span data-stu-id="0cbdd-204">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="0cbdd-205">Kimlik doğrulamasını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="0cbdd-205">Enable authentication</span></span>

<span data-ttu-id="0cbdd-206">`Startup` Sınıfının `Configure` yönteminde, sonrasında `UseStaticFiles` [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ' ı çağırın:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-206">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="0cbdd-207">Düzen değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="0cbdd-207">Layout changes</span></span>

<span data-ttu-id="0cbdd-208">İsteğe bağlı: (`_LoginPartial`) oturum açma bilgilerini düzen dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-208">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="0cbdd-209">Yetkilendirmeyi içeren bir Razor projesinde kimlik oluşturma kimliği</span><span class="sxs-lookup"><span data-stu-id="0cbdd-209">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="0cbdd-210">Bazı kimlik seçenekleri */Identity/ıdentityhostingstartup. cs alanlarında*yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-210">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="0cbdd-211">Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="0cbdd-211">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="0cbdd-212">Var olan yetkilendirme olmadan bir MVC projesinde kimlik oluşturma kimliği</span><span class="sxs-lookup"><span data-stu-id="0cbdd-212">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="0cbdd-213">İsteğe bağlı: bir Login (`_LoginPartial`) öğesini *views/Shared/_Layout. cshtml* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-213">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="0cbdd-214">*Pages/Shared/_LoginPartial. cshtml* dosyasını *views/Shared/_LoginPartial. cshtml* olarak taşıyın</span><span class="sxs-lookup"><span data-stu-id="0cbdd-214">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="0cbdd-215">Kimlik, */kimlik/ıdentityhostingstartup. cs alanlarında*yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-215">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="0cbdd-216">Daha fazla bilgi için bkz. ıhostingstartup.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-216">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="0cbdd-217">Şu tarihten sonra `UseStaticFiles` [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) çağrısı:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-217">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="0cbdd-218">Yetkilendirmeyi içeren bir MVC projesinde kimlik oluşturma kimliği</span><span class="sxs-lookup"><span data-stu-id="0cbdd-218">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="0cbdd-219">*Sayfaları/paylaşılan* klasörünü ve bu klasördeki dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-219">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="0cbdd-220">Tam kimlik UI kaynağı oluşturma</span><span class="sxs-lookup"><span data-stu-id="0cbdd-220">Create full identity UI source</span></span>

<span data-ttu-id="0cbdd-221">Kimlik Kullanıcı arabirimine tam denetim sağlamak için, Identity desteği ' ı çalıştırın ve **tüm dosyaları geçersiz kıl**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-221">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="0cbdd-222">Aşağıdaki Vurgulanan kodda, varsayılan kimlik Kullanıcı arabirimini ASP.NET Core 2,1 Web uygulamasındaki kimlikle değiştirme değişiklikleri gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-222">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="0cbdd-223">Bunu, kimlik Kullanıcı arabirimine tam denetim sağlamak için yapmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-223">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="0cbdd-224">Varsayılan kimlik aşağıdaki kodda değiştirilmiştir:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-224">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="0cbdd-225">Aşağıdaki kod, [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)ve [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)öğesini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-225">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="0cbdd-226">Bir `IEmailSender` uygulamayı kaydedin, örneğin:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-226">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="0cbdd-227">Kayıt sayfasını devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="0cbdd-227">Disable register page</span></span>

<span data-ttu-id="0cbdd-228">Kullanıcı kaydını devre dışı bırakmak için:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-228">To disable user registration:</span></span>

* <span data-ttu-id="0cbdd-229">Yapı iskelesi kimliği.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-229">Scaffold Identity.</span></span> <span data-ttu-id="0cbdd-230">Account. Register, Account. Login ve account. RegisterConfirmation bilgilerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-230">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="0cbdd-231">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-231">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="0cbdd-232">Kullanıcıları bu uç noktadan kaydedememesi için *alan/kimlik/sayfa/hesap/kayıt. cshtml. cs* 'yi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-232">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="0cbdd-233">Önceki değişikliklerle tutarlı olması için *alan/kimlik/sayfa/hesap/kayıt. cshtml* 'yi güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-233">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="0cbdd-234">*Alan/kimlik/sayfa/hesap/Login. cshtml* 'den kayıt bağlantısını açıklama veya kaldırma</span><span class="sxs-lookup"><span data-stu-id="0cbdd-234">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="0cbdd-235">*Bölgeler/kimlik/sayfalar/hesap/RegisterConfirmation* sayfasını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-235">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="0cbdd-236">Cshtml dosyasındaki kodu ve bağlantıları kaldırın.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-236">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="0cbdd-237">Onay kodunu şuradan kaldırın `PageModel`:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-237">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="0cbdd-238">Kullanıcıları eklemek için başka bir uygulama kullanma</span><span class="sxs-lookup"><span data-stu-id="0cbdd-238">Use another app to add users</span></span>

<span data-ttu-id="0cbdd-239">Web uygulaması dışındaki kullanıcıları eklemek için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-239">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="0cbdd-240">Kullanıcı ekleme seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-240">Options to add users include:</span></span>

* <span data-ttu-id="0cbdd-241">Adanmış bir yönetim Web uygulaması.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-241">A dedicated admin web app.</span></span>
* <span data-ttu-id="0cbdd-242">Bir konsol uygulaması.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-242">A console app.</span></span>

<span data-ttu-id="0cbdd-243">Aşağıdaki kod, kullanıcı eklemeye yönelik bir yaklaşımı özetler:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-243">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="0cbdd-244">Kullanıcıların listesi belleği okur.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-244">A list of users is read into memory.</span></span>
* <span data-ttu-id="0cbdd-245">Her Kullanıcı için güçlü bir benzersiz parola oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-245">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="0cbdd-246">Kullanıcı, kimlik veritabanına eklenir.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-246">The user is added to the Identity database.</span></span>
* <span data-ttu-id="0cbdd-247">Kullanıcıya bildirilir ve parolayı değiştirmesi bildirilir.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-247">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="0cbdd-248">Aşağıdaki kod, bir kullanıcı ekleme ana hatlarıyla verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="0cbdd-248">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="0cbdd-249">Üretim senaryolarında de benzer bir yaklaşım izlenebilir.</span><span class="sxs-lookup"><span data-stu-id="0cbdd-249">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0cbdd-250">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0cbdd-250">Additional resources</span></span>

* [<span data-ttu-id="0cbdd-251">ASP.NET Core 2,1 ve üzeri kimlik doğrulama kodundaki değişiklikler</span><span class="sxs-lookup"><span data-stu-id="0cbdd-251">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end