---
title: ASP.NET Core projelerinde yapı iskelesi Identity
author: rick-anderson
description: ASP.NET Core projesindeki fkatlama oluşturmayı öğrenin Identity .
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
ms.openlocfilehash: 36afa8ece58843b434ebfba6305bffdb9eb9bca0
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724295"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="b4186-103">ASP.NET Core projelerinde yapı iskelesi Identity</span><span class="sxs-lookup"><span data-stu-id="b4186-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="b4186-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b4186-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b4186-105">ASP.NET Core bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core Identity ](xref:security/authentication/identity) sağlar.</span><span class="sxs-lookup"><span data-stu-id="b4186-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="b4186-106">Dahil edilen uygulamalar, Identity Identity Razor sınıf kitaplığı 'nda (RCL) bulunan kaynak kodu seçmeli olarak eklemek için desteği uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="b4186-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="b4186-107">Kodu değiştirebilmeniz ve davranışı değiştirebilmek için kaynak kodu oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b4186-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="b4186-108">Örneğin, kayıt sırasında kullanılan kodu oluşturmak için desteği ' ı söyleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b4186-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="b4186-109">Oluşturulan kod RCL 'deki aynı koda göre önceliklidir Identity .</span><span class="sxs-lookup"><span data-stu-id="b4186-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="b4186-110">Kullanıcı arabirimine tam denetim sağlamak ve varsayılan RCL 'yi kullanmak için, [tam Identity UI kaynağı oluşturma](#full)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b4186-110">To gain full control of the UI and not use the default RCL, see the section [Create full Identity UI source](#full).</span></span>

<span data-ttu-id="b4186-111">Kimlik **doğrulaması içermeyen uygulamalar** , RCL paketini eklemek için desteği uygulayabilir Identity .</span><span class="sxs-lookup"><span data-stu-id="b4186-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="b4186-112">Oluşturulacak kodu seçme seçeneğiniz vardır Identity .</span><span class="sxs-lookup"><span data-stu-id="b4186-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="b4186-113">Desteği gerekli kodların çoğunu üretse de, işlemi gerçekleştirmek için projenizi güncelleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="b4186-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="b4186-114">Bu belgede, bir yapı iskelesi güncelleştirmesini tamamlaması için gereken adımlar açıklanmaktadır Identity .</span><span class="sxs-lookup"><span data-stu-id="b4186-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="b4186-115">Dosya farklılıklarını gösteren ve değişikliklerden geri dönüş yapmanızı sağlayan bir kaynak denetimi sistemi kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="b4186-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="b4186-116">Scaffolder çalıştırıldıktan sonra değişiklikleri inceleyin Identity .</span><span class="sxs-lookup"><span data-stu-id="b4186-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="b4186-117">[Iki öğeli kimlik doğrulaması](xref:security/authentication/identity-enable-qrcodes), [Hesap onaylama ve parola kurtarma](xref:security/authentication/accconfirm)ile diğer güvenlik özellikleri kullanılırken hizmetler gereklidir Identity .</span><span class="sxs-lookup"><span data-stu-id="b4186-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="b4186-118">Hizmetler veya hizmet saplamaları, yapı iskelesi sırasında oluşturulmaz Identity .</span><span class="sxs-lookup"><span data-stu-id="b4186-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="b4186-119">Bu özelliklerin etkinleştirilmesi için hizmetlerin el ile eklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="b4186-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="b4186-120">Örneğin, bkz. [e-posta onayı gerektir](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="b4186-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="b4186-121">IdentityYeni bir veri bağlamıyla, var olan bireysel hesaplara sahip bir projeye dönüştürme yaparken:</span><span class="sxs-lookup"><span data-stu-id="b4186-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="b4186-122">İçinde `Startup.ConfigureServices` , şu çağrıları kaldırın:</span><span class="sxs-lookup"><span data-stu-id="b4186-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="b4186-123">Örneğin, `AddDbContext` ve `AddDefaultIdentity` aşağıdaki kodda yorum yapılır:</span><span class="sxs-lookup"><span data-stu-id="b4186-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="b4186-124">Önceki kod, alanlarda çoğaltılan kodu Yorumlar */ Identity /IdentityHostingStartup.cs*</span><span class="sxs-lookup"><span data-stu-id="b4186-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="b4186-125">Genellikle, bireysel hesaplarla oluşturulan uygulamalar yeni bir veri bağlamı ***oluşturmamalıdır*** .</span><span class="sxs-lookup"><span data-stu-id="b4186-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="b4186-126">IdentityBoş bir projeye yapı iskelesi</span><span class="sxs-lookup"><span data-stu-id="b4186-126">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b4186-127">`Startup`Sınıfını aşağıdakine benzer kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="b4186-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="b4186-128">Identity Razor Var olan yetkilendirme olmadan bir projeye yapı iskelesi yapın</span><span class="sxs-lookup"><span data-stu-id="b4186-128">Scaffold Identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="b4186-129">, */ Identity /IdentityHostingStartup.cs alanlarında*yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="b4186-129"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="b4186-130">Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b4186-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="b4186-131">Geçişler, UseAuthentication ve düzen</span><span class="sxs-lookup"><span data-stu-id="b4186-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="b4186-132">Kimlik doğrulamasını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="b4186-132">Enable authentication</span></span>

<span data-ttu-id="b4186-133">`Startup`Sınıfını aşağıdakine benzer kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="b4186-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="b4186-134">Düzen değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="b4186-134">Layout changes</span></span>

<span data-ttu-id="b4186-135">İsteğe bağlı: () oturum açma bilgilerini `_LoginPartial` Düzen dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b4186-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="b4186-136">Identity Razor Yetkilendirmeyle bir projeye yapı</span><span class="sxs-lookup"><span data-stu-id="b4186-136">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="b4186-137">Bazı Identity Seçenekler */ Identity /IdentityHostingStartup.cs alanlarında*yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="b4186-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="b4186-138">Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b4186-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="b4186-139">IdentityVar olan yetkilendirme olmadan BIR MVC projesinde yapı iskelesi yapın</span><span class="sxs-lookup"><span data-stu-id="b4186-139">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="b4186-140">İsteğe bağlı: bir Login ( `_LoginPartial` ) öğesini *views/Shared/_Layout. cshtml* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b4186-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="b4186-141">*Pages/Shared/_LoginPartial. cshtml* dosyasını *views/Shared/_LoginPartial. cshtml* olarak taşıyın</span><span class="sxs-lookup"><span data-stu-id="b4186-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="b4186-142">, */ Identity /IdentityHostingStartup.cs alanlarında*yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="b4186-142"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="b4186-143">Daha fazla bilgi için bkz. ıhostingstartup.</span><span class="sxs-lookup"><span data-stu-id="b4186-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="b4186-144">`Startup`Sınıfını aşağıdakine benzer kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="b4186-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="b4186-145">IdentityYetkilendirme ile BIR MVC projesinde yapı iskelesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="b4186-145">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-identity-into-a-blazor-server-project-without-existing-authorization"></a><span data-ttu-id="b4186-146">Identity Blazor Var olan yetkilendirme olmadan bir sunucu projesinde yapı iskelesi yapın</span><span class="sxs-lookup"><span data-stu-id="b4186-146">Scaffold Identity into a Blazor Server project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="b4186-147">, */ Identity /IdentityHostingStartup.cs alanlarında*yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="b4186-147"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="b4186-148">Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b4186-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="b4186-149">Geçişler</span><span class="sxs-lookup"><span data-stu-id="b4186-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="b4186-150">Uygulamaya bir XSRF belirteci geçirin</span><span class="sxs-lookup"><span data-stu-id="b4186-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="b4186-151">Belirteçler bileşenlere geçirilebilir:</span><span class="sxs-lookup"><span data-stu-id="b4186-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="b4186-152">Kimlik doğrulama belirteçleri sağlandığında ve kimlik doğrulama tanımlama bilgisine kaydedildiğinde, bunlar bileşenlere geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="b4186-152">When authentication tokens are provisioned and saved to the authentication cookie, they can be passed to components.</span></span>
* Razor<span data-ttu-id="b4186-153">bileşenler doğrudan kullanamaz `HttpContext` . bu nedenle, tarihinde oturum kapatma uç noktasına gönderi için bir [istek önleme zaman AŞıMı (XSRF) belirteci](xref:security/anti-request-forgery) elde etmenin bir yolu yoktur Identity `/Identity/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="b4186-153"> components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to Identity's logout endpoint at `/Identity/Account/Logout`.</span></span> <span data-ttu-id="b4186-154">Bir XSRF belirteci bileşenlere geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="b4186-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="b4186-155">Daha fazla bilgi için bkz. <xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="b4186-155">For more information, see <xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="b4186-156">*Pages/_Host. cshtml* dosyasında, ve sınıflarına eklendikten sonra belirteci oluşturun `InitialApplicationState` `TokenProvider` :</span><span class="sxs-lookup"><span data-stu-id="b4186-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="b4186-157">Şunu `App` atamak için bileşeni (*app. Razor*) güncelleştirin `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="b4186-157">Update the `App` component (*App.razor*) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="b4186-158">`TokenProvider`Konusunda gösterilen hizmet, `LoginDisplay` bileşeninde aşağıdaki [Düzen ve kimlik doğrulama akışı değişiklikleri](#layout-and-authentication-flow-changes) bölümünde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b4186-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="b4186-159">Kimlik doğrulamasını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="b4186-159">Enable authentication</span></span>

<span data-ttu-id="b4186-160">`Startup`Sınıfında:</span><span class="sxs-lookup"><span data-stu-id="b4186-160">In the `Startup` class:</span></span>

* <span data-ttu-id="b4186-161">RazorSayfaların hizmetlerinin eklendiğini onaylayın `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b4186-161">Confirm that Razor Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="b4186-162">[TokenProvider](xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)kullanılıyorsa, hizmeti kaydedin.</span><span class="sxs-lookup"><span data-stu-id="b4186-162">If using the [TokenProvider](xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="b4186-163">`UseDatabaseErrorPage`Geliştirme ortamı için içindeki uygulama oluşturucuda çağırın `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b4186-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="b4186-164">Çağırın `UseAuthentication` ve `UseAuthorization` sonra `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="b4186-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="b4186-165">Sayfalar için bir uç nokta ekleyin Razor .</span><span class="sxs-lookup"><span data-stu-id="b4186-165">Add an endpoint for Razor Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="b4186-166">Düzen ve kimlik doğrulama akışı değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="b4186-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="b4186-167">`RedirectToLogin`Uygulamanın proje kökündeki *paylaşılan* klasörüne bir bileşen (*redirecttologin. Razor*) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b4186-167">Add a `RedirectToLogin` component (*RedirectToLogin.razor*) to the app's *Shared* folder in the project root:</span></span>

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo("Identity/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

`LoginDisplay`Uygulamanın *paylaşılan* klasörüne bir bileşen (*logindisplay. Razor*) ekleyin. <span data-ttu-id="b4186-169">[TokenProvider hizmeti](xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) , Identity oturum kapatma uç noktasına gönderilen HTML formu için XSRF belirteci sağlar:</span><span class="sxs-lookup"><span data-stu-id="b4186-169">The [TokenProvider service](xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to Identity's logout endpoint:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href="Identity/Account/Manage/Index">
            Hello, @context.User.Identity.Name!
        </a>
        <form action="/Identity/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href="Identity/Account/Register">Register</a>
        <a href="Identity/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="b4186-170">`MainLayout`Bileşende (*paylaşılan/mainlayout. Razor*), `LoginDisplay` bileşeni en üst satır `<div>` öğesinin içeriğine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b4186-170">In the `MainLayout` component (*Shared/MainLayout.razor*), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="b4186-171">Stil kimlik doğrulama uç noktaları</span><span class="sxs-lookup"><span data-stu-id="b4186-171">Style authentication endpoints</span></span>

<span data-ttu-id="b4186-172">BlazorSunucu Razor sayfa sayfalarını kullandığından Identity , bir ziyaretçi Identity Sayfalar ve bileşenler arasında gezinse Kullanıcı arabiriminin stili değişir.</span><span class="sxs-lookup"><span data-stu-id="b4186-172">Because Blazor Server uses Razor Pages Identity pages, the styling of the UI changes when a visitor navigates between Identity pages and components.</span></span> <span data-ttu-id="b4186-173">Uyumsuz stillere yönelik iki seçeneğiniz vardır:</span><span class="sxs-lookup"><span data-stu-id="b4186-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-identity-components"></a><span data-ttu-id="b4186-174">Derleme Identity bileşenleri</span><span class="sxs-lookup"><span data-stu-id="b4186-174">Build Identity components</span></span>

<span data-ttu-id="b4186-175">Bileşenler oluşturmak için bileşenleri kullanmanın bir yaklaşımı Identity , Identity bileşenleri oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="b4186-175">An approach to using components for Identity instead of pages is to build Identity components.</span></span> <span data-ttu-id="b4186-176">`SignInManager` `UserManager` Razor Bileşenlerinde desteklenmediğinden, Blazor Kullanıcı hesabı eylemlerini IŞLEMEK için sunucu uygulamasındaki API uç noktalarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="b4186-176">Because `SignInManager` and `UserManager` aren't supported in Razor components, use API endpoints in the Blazor Server app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-blazor-app-styles"></a><span data-ttu-id="b4186-177">Uygulama stilleriyle özel düzen kullanma Blazor</span><span class="sxs-lookup"><span data-stu-id="b4186-177">Use a custom layout with Blazor app styles</span></span>

<span data-ttu-id="b4186-178">IdentitySayfa düzeni ve stilleri, varsayılan temayı kullanan sayfalar oluşturmak için değiştirilebilir Blazor .</span><span class="sxs-lookup"><span data-stu-id="b4186-178">The Identity pages layout and styles can be modified to produce pages that use the default Blazor theme.</span></span>

> [!NOTE]
> <span data-ttu-id="b4186-179">Bu bölümdeki örnek yalnızca özelleştirme için bir başlangıç noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="b4186-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="b4186-180">En iyi kullanıcı deneyimi için büyük olasılıkla ek çalışma gerekir.</span><span class="sxs-lookup"><span data-stu-id="b4186-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="b4186-181">Yeni bir `NavMenu_IdentityLayout` bileşen oluşturun (*paylaşılan/NavMenu_IdentityLayout. Razor*).</span><span class="sxs-lookup"><span data-stu-id="b4186-181">Create a new `NavMenu_IdentityLayout` component (*Shared/NavMenu_IdentityLayout.razor*).</span></span> <span data-ttu-id="b4186-182">Bileşenin biçimlendirmesi ve kodu için, uygulamanın bileşeniyle aynı içeriği kullanın `NavMenu` (*paylaşılan/navmenu. Razor*).</span><span class="sxs-lookup"><span data-stu-id="b4186-182">For the markup and code of the component, use the same content of the app's `NavMenu` component (*Shared/NavMenu.razor*).</span></span> <span data-ttu-id="b4186-183">`NavLink`Bileşendeki otomatik yeniden yönlendirmeler `RedirectToLogin` kimlik doğrulama veya yetkilendirme gerektiren bileşenler için başarısız olduğundan anonim olarak erişilemeyen bileşenlere her birini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="b4186-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="b4186-184">*Sayfalar/paylaşılan/Layout. cshtml* dosyasında aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="b4186-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="b4186-185">RazorEtiket yardımcıları ve uygulamanın bileşenlerini *paylaşılan* klasörde kullanmak için dosyanın en üstüne yönergeler ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b4186-185">Add Razor directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="b4186-186">`{APPLICATION ASSEMBLY}`Uygulamanın derleme adıyla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="b4186-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="b4186-187">İçeriğe bir `<base>` etiket ve Blazor stil sayfası ekleyin `<link>` `<head>` :</span><span class="sxs-lookup"><span data-stu-id="b4186-187">Add a `<base>` tag and Blazor stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="b4186-188">`<body>`Etiketin içeriğini aşağıdaki şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="b4186-188">Change the content of the `<body>` tag to the following:</span></span>

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_IdentityLayout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default Identity UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/Identity/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/Identity/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/Identity/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-identity-into-a-blazor-server-project-with-authorization"></a><span data-ttu-id="b4186-189">Identity Blazor Yetkilendirmeyle bir sunucu projesine dolandırın</span><span class="sxs-lookup"><span data-stu-id="b4186-189">Scaffold Identity into a Blazor Server project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="b4186-190">Bazı Identity Seçenekler */ Identity /IdentityHostingStartup.cs alanlarında*yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="b4186-190">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="b4186-191">Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b4186-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="b4186-192">Tam Identity UI kaynağı oluştur</span><span class="sxs-lookup"><span data-stu-id="b4186-192">Create full Identity UI source</span></span>

<span data-ttu-id="b4186-193">Kullanıcı arabiriminin tam denetimini sürdürmek için Identity , Identity desteği ' ı çalıştırın ve **tüm dosyaları geçersiz kıl**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="b4186-193">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="b4186-194">Aşağıdaki vurgulanan kod, varsayılan Identity Kullanıcı arabirimini Identity ASP.NET Core 2,1 Web uygulamasında değiştirecek değişiklikleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="b4186-194">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="b4186-195">Bunu, Kullanıcı arabirimine tam denetim sağlamak için yapmak isteyebilirsiniz Identity .</span><span class="sxs-lookup"><span data-stu-id="b4186-195">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="b4186-196">Varsayılan değer Identity aşağıdaki kodda değiştirilmiştir:</span><span class="sxs-lookup"><span data-stu-id="b4186-196">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="b4186-197">Aşağıdaki kod, [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)ve [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)öğesini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="b4186-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="b4186-198">Bir `IEmailSender` uygulamayı kaydedin, örneğin:</span><span class="sxs-lookup"><span data-stu-id="b4186-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-a-page"></a><span data-ttu-id="b4186-199">Sayfayı devre dışı bırakma</span><span class="sxs-lookup"><span data-stu-id="b4186-199">Disable a page</span></span>

<span data-ttu-id="b4186-200">Bu bölümler, kaydetme sayfasının nasıl devre dışı bırakılacağını gösterir, ancak herhangi bir sayfayı devre dışı bırakmak için yaklaşım kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b4186-200">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="b4186-201">Kullanıcı kaydını devre dışı bırakmak için:</span><span class="sxs-lookup"><span data-stu-id="b4186-201">To disable user registration:</span></span>

* <span data-ttu-id="b4186-202">Yapı iskelesi Identity .</span><span class="sxs-lookup"><span data-stu-id="b4186-202">Scaffold Identity.</span></span> <span data-ttu-id="b4186-203">Account. Register, Account. Login ve account. RegisterConfirmation bilgilerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b4186-203">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="b4186-204">Örnek:</span><span class="sxs-lookup"><span data-stu-id="b4186-204">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="b4186-205">Kullanıcıları bu uç noktadan kaydedememesi için *alanı/ Identity /Pages/Account/Register.cshtml.cs* güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="b4186-205">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="b4186-206">Önceki değişikliklerle tutarlı olması için *Areas/ Identity /Pages/Account/Register.exe* öğesini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="b4186-206">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="b4186-207">*/ Identity /Pages/Account/Login.exe* içindeki kayıt bağlantısını açıklama veya Kaldır</span><span class="sxs-lookup"><span data-stu-id="b4186-207">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="b4186-208">*Areas/ Identity /Pages/Account/registerconfirmation* sayfasını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="b4186-208">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="b4186-209">Cshtml dosyasındaki kodu ve bağlantıları kaldırın.</span><span class="sxs-lookup"><span data-stu-id="b4186-209">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="b4186-210">Onay kodunu şuradan kaldırın `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="b4186-210">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="b4186-211">Kullanıcıları eklemek için başka bir uygulama kullanma</span><span class="sxs-lookup"><span data-stu-id="b4186-211">Use another app to add users</span></span>

<span data-ttu-id="b4186-212">Web uygulaması dışındaki kullanıcıları eklemek için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="b4186-212">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="b4186-213">Kullanıcı ekleme seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="b4186-213">Options to add users include:</span></span>

* <span data-ttu-id="b4186-214">Adanmış bir yönetim Web uygulaması.</span><span class="sxs-lookup"><span data-stu-id="b4186-214">A dedicated admin web app.</span></span>
* <span data-ttu-id="b4186-215">Bir konsol uygulaması.</span><span class="sxs-lookup"><span data-stu-id="b4186-215">A console app.</span></span>

<span data-ttu-id="b4186-216">Aşağıdaki kod, kullanıcı eklemeye yönelik bir yaklaşımı özetler:</span><span class="sxs-lookup"><span data-stu-id="b4186-216">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="b4186-217">Kullanıcıların listesi belleği okur.</span><span class="sxs-lookup"><span data-stu-id="b4186-217">A list of users is read into memory.</span></span>
* <span data-ttu-id="b4186-218">Her Kullanıcı için güçlü bir benzersiz parola oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="b4186-218">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="b4186-219">Kullanıcı Identity veritabanına eklenir.</span><span class="sxs-lookup"><span data-stu-id="b4186-219">The user is added to the Identity database.</span></span>
* <span data-ttu-id="b4186-220">Kullanıcıya bildirilir ve parolayı değiştirmesi bildirilir.</span><span class="sxs-lookup"><span data-stu-id="b4186-220">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="b4186-221">Aşağıdaki kod, bir kullanıcı ekleme ana hatlarıyla verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="b4186-221">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="b4186-222">Üretim senaryolarında de benzer bir yaklaşım izlenebilir.</span><span class="sxs-lookup"><span data-stu-id="b4186-222">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="b4186-223">Statik varlıkların yayımlanmasını Engelle Identity</span><span class="sxs-lookup"><span data-stu-id="b4186-223">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="b4186-224">Statik Identity varlıkların Web köküne yayımlanmasını engellemek için bkz <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> ..</span><span class="sxs-lookup"><span data-stu-id="b4186-224">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b4186-225">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b4186-225">Additional resources</span></span>

* [<span data-ttu-id="b4186-226">ASP.NET Core 2,1 ve üzeri kimlik doğrulama kodundaki değişiklikler</span><span class="sxs-lookup"><span data-stu-id="b4186-226">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b4186-227">ASP.NET Core 2,1 ve üzeri bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core Identity ](xref:security/authentication/identity) sağlar.</span><span class="sxs-lookup"><span data-stu-id="b4186-227">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="b4186-228">Dahil edilen uygulamalar, Identity Identity Razor sınıf kitaplığı 'nda (RCL) bulunan kaynak kodu seçmeli olarak eklemek için desteği uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="b4186-228">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="b4186-229">Kodu değiştirebilmeniz ve davranışı değiştirebilmek için kaynak kodu oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b4186-229">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="b4186-230">Örneğin, kayıt sırasında kullanılan kodu oluşturmak için desteği ' ı söyleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b4186-230">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="b4186-231">Oluşturulan kod RCL 'deki aynı koda göre önceliklidir Identity .</span><span class="sxs-lookup"><span data-stu-id="b4186-231">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="b4186-232">Kullanıcı arabirimine tam denetim sağlamak ve varsayılan RCL 'yi kullanmak için, [tam KIMLIK UI kaynağı oluşturma](#full)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b4186-232">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="b4186-233">Kimlik **doğrulaması içermeyen uygulamalar** , RCL paketini eklemek için desteği uygulayabilir Identity .</span><span class="sxs-lookup"><span data-stu-id="b4186-233">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="b4186-234">Oluşturulacak kodu seçme seçeneğiniz vardır Identity .</span><span class="sxs-lookup"><span data-stu-id="b4186-234">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="b4186-235">Desteği gerekli kodların çoğunu üretse de, işlemi gerçekleştirmek için projenizi güncelleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="b4186-235">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="b4186-236">Bu belgede, bir yapı iskelesi güncelleştirmesini tamamlaması için gereken adımlar açıklanmaktadır Identity .</span><span class="sxs-lookup"><span data-stu-id="b4186-236">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="b4186-237">IdentityDesteği çalıştırıldığında, proje dizininde bir *ScaffoldingReadme.txt* dosyası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="b4186-237">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="b4186-238">*ScaffoldingReadme.txt* dosyası, yapı iskelesi güncelleştirmesinin tamamlanabilmesi için gerekli olan genel yönergeleri içerir Identity .</span><span class="sxs-lookup"><span data-stu-id="b4186-238">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="b4186-239">Bu belge, *ScaffoldingReadme.txt* dosyasından daha ayrıntılı yönergeler içerir.</span><span class="sxs-lookup"><span data-stu-id="b4186-239">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="b4186-240">Dosya farklılıklarını gösteren ve değişikliklerden geri dönüş yapmanızı sağlayan bir kaynak denetimi sistemi kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="b4186-240">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="b4186-241">Scaffolder çalıştırıldıktan sonra değişiklikleri inceleyin Identity .</span><span class="sxs-lookup"><span data-stu-id="b4186-241">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="b4186-242">[Iki öğeli kimlik doğrulaması](xref:security/authentication/identity-enable-qrcodes), [Hesap onaylama ve parola kurtarma](xref:security/authentication/accconfirm)ile diğer güvenlik özellikleri kullanılırken hizmetler gereklidir Identity .</span><span class="sxs-lookup"><span data-stu-id="b4186-242">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="b4186-243">Hizmetler veya hizmet saplamaları, yapı iskelesi sırasında oluşturulmaz Identity .</span><span class="sxs-lookup"><span data-stu-id="b4186-243">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="b4186-244">Bu özelliklerin etkinleştirilmesi için hizmetlerin el ile eklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="b4186-244">Services to enable these features must be added manually.</span></span> <span data-ttu-id="b4186-245">Örneğin, bkz. [e-posta onayı gerektir](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="b4186-245">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="b4186-246">IdentityBoş bir projeye yapı iskelesi</span><span class="sxs-lookup"><span data-stu-id="b4186-246">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b4186-247">Aşağıdaki Vurgulanan çağrıları `Startup` sınıfına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b4186-247">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="b4186-248">Identity Razor Var olan yetkilendirme olmadan bir projeye yapı iskelesi yapın</span><span class="sxs-lookup"><span data-stu-id="b4186-248">Scaffold Identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="b4186-249">, */ Identity /IdentityHostingStartup.cs alanlarında*yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="b4186-249"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="b4186-250">Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b4186-250">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="b4186-251">Geçişler, UseAuthentication ve düzen</span><span class="sxs-lookup"><span data-stu-id="b4186-251">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="b4186-252">Kimlik doğrulamasını etkinleştir</span><span class="sxs-lookup"><span data-stu-id="b4186-252">Enable authentication</span></span>

<span data-ttu-id="b4186-253">`Configure` `Startup` Sınıfının yönteminde, sonrasında [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ' ı çağırın `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="b4186-253">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="b4186-254">Düzen değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="b4186-254">Layout changes</span></span>

<span data-ttu-id="b4186-255">İsteğe bağlı: () oturum açma bilgilerini `_LoginPartial` Düzen dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b4186-255">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="b4186-256">Identity Razor Yetkilendirmeyle bir projeye yapı</span><span class="sxs-lookup"><span data-stu-id="b4186-256">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="b4186-257">Bazı Identity Seçenekler */ Identity /IdentityHostingStartup.cs alanlarında*yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="b4186-257">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="b4186-258">Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b4186-258">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="b4186-259">IdentityVar olan yetkilendirme olmadan BIR MVC projesinde yapı iskelesi yapın</span><span class="sxs-lookup"><span data-stu-id="b4186-259">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="b4186-260">İsteğe bağlı: bir Login ( `_LoginPartial` ) öğesini *views/Shared/_Layout. cshtml* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b4186-260">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="b4186-261">*Pages/Shared/_LoginPartial. cshtml* dosyasını *views/Shared/_LoginPartial. cshtml* olarak taşıyın</span><span class="sxs-lookup"><span data-stu-id="b4186-261">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="b4186-262">, */ Identity /IdentityHostingStartup.cs alanlarında*yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="b4186-262"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="b4186-263">Daha fazla bilgi için bkz. ıhostingstartup.</span><span class="sxs-lookup"><span data-stu-id="b4186-263">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="b4186-264">Şu tarihten sonra [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) çağrısı `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="b4186-264">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="b4186-265">IdentityYetkilendirme ile BIR MVC projesinde yapı iskelesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="b4186-265">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="b4186-266">*Sayfaları/paylaşılan* klasörünü ve bu klasördeki dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="b4186-266">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="b4186-267">Tam Identity UI kaynağı oluştur</span><span class="sxs-lookup"><span data-stu-id="b4186-267">Create full Identity UI source</span></span>

<span data-ttu-id="b4186-268">Kullanıcı arabiriminin tam denetimini sürdürmek için Identity , Identity desteği ' ı çalıştırın ve **tüm dosyaları geçersiz kıl**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="b4186-268">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="b4186-269">Aşağıdaki vurgulanan kod, varsayılan Identity Kullanıcı arabirimini Identity ASP.NET Core 2,1 Web uygulamasında değiştirecek değişiklikleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="b4186-269">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="b4186-270">Bunu, Kullanıcı arabirimine tam denetim sağlamak için yapmak isteyebilirsiniz Identity .</span><span class="sxs-lookup"><span data-stu-id="b4186-270">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="b4186-271">Varsayılan değer Identity aşağıdaki kodda değiştirilmiştir:</span><span class="sxs-lookup"><span data-stu-id="b4186-271">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="b4186-272">Aşağıdaki kod, [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)ve [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)öğesini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="b4186-272">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="b4186-273">Bir `IEmailSender` uygulamayı kaydedin, örneğin:</span><span class="sxs-lookup"><span data-stu-id="b4186-273">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="b4186-274">Kayıt sayfasını devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="b4186-274">Disable register page</span></span>

<span data-ttu-id="b4186-275">Kullanıcı kaydını devre dışı bırakmak için:</span><span class="sxs-lookup"><span data-stu-id="b4186-275">To disable user registration:</span></span>

* <span data-ttu-id="b4186-276">Yapı iskelesi Identity .</span><span class="sxs-lookup"><span data-stu-id="b4186-276">Scaffold Identity.</span></span> <span data-ttu-id="b4186-277">Account. Register, Account. Login ve account. RegisterConfirmation bilgilerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b4186-277">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="b4186-278">Örnek:</span><span class="sxs-lookup"><span data-stu-id="b4186-278">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="b4186-279">Kullanıcıları bu uç noktadan kaydedememesi için *alanı/ Identity /Pages/Account/Register.cshtml.cs* güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="b4186-279">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="b4186-280">Önceki değişikliklerle tutarlı olması için *Areas/ Identity /Pages/Account/Register.exe* öğesini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="b4186-280">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="b4186-281">*/ Identity /Pages/Account/Login.exe* içindeki kayıt bağlantısını açıklama veya Kaldır</span><span class="sxs-lookup"><span data-stu-id="b4186-281">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="b4186-282">*Areas/ Identity /Pages/Account/registerconfirmation* sayfasını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="b4186-282">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="b4186-283">Cshtml dosyasındaki kodu ve bağlantıları kaldırın.</span><span class="sxs-lookup"><span data-stu-id="b4186-283">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="b4186-284">Onay kodunu şuradan kaldırın `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="b4186-284">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="b4186-285">Kullanıcıları eklemek için başka bir uygulama kullanma</span><span class="sxs-lookup"><span data-stu-id="b4186-285">Use another app to add users</span></span>

<span data-ttu-id="b4186-286">Web uygulaması dışındaki kullanıcıları eklemek için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="b4186-286">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="b4186-287">Kullanıcı ekleme seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="b4186-287">Options to add users include:</span></span>

* <span data-ttu-id="b4186-288">Adanmış bir yönetim Web uygulaması.</span><span class="sxs-lookup"><span data-stu-id="b4186-288">A dedicated admin web app.</span></span>
* <span data-ttu-id="b4186-289">Bir konsol uygulaması.</span><span class="sxs-lookup"><span data-stu-id="b4186-289">A console app.</span></span>

<span data-ttu-id="b4186-290">Aşağıdaki kod, kullanıcı eklemeye yönelik bir yaklaşımı özetler:</span><span class="sxs-lookup"><span data-stu-id="b4186-290">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="b4186-291">Kullanıcıların listesi belleği okur.</span><span class="sxs-lookup"><span data-stu-id="b4186-291">A list of users is read into memory.</span></span>
* <span data-ttu-id="b4186-292">Her Kullanıcı için güçlü bir benzersiz parola oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="b4186-292">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="b4186-293">Kullanıcı Identity veritabanına eklenir.</span><span class="sxs-lookup"><span data-stu-id="b4186-293">The user is added to the Identity database.</span></span>
* <span data-ttu-id="b4186-294">Kullanıcıya bildirilir ve parolayı değiştirmesi bildirilir.</span><span class="sxs-lookup"><span data-stu-id="b4186-294">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="b4186-295">Aşağıdaki kod, bir kullanıcı ekleme ana hatlarıyla verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="b4186-295">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="b4186-296">Üretim senaryolarında de benzer bir yaklaşım izlenebilir.</span><span class="sxs-lookup"><span data-stu-id="b4186-296">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b4186-297">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b4186-297">Additional resources</span></span>

* [<span data-ttu-id="b4186-298">ASP.NET Core 2,1 ve üzeri kimlik doğrulama kodundaki değişiklikler</span><span class="sxs-lookup"><span data-stu-id="b4186-298">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
