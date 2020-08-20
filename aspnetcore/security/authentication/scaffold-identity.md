---
title: ASP.NET Core projelerinde yapı iskelesi Identity
author: rick-anderson
description: ASP.NET Core projesindeki fkatlama oluşturmayı öğrenin Identity .
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
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
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 09535f41d15b90fa5e50eb1f22f6aecef0530f0c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88629567"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a><span data-ttu-id="e9740-103">ASP.NET Core projelerinde yapı iskelesi Identity</span><span class="sxs-lookup"><span data-stu-id="e9740-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="e9740-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="e9740-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="e9740-105">ASP.NET Core [ASP.NET Core Identity](xref:security/authentication/identity) bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="e9740-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="e9740-106">Dahil edilen uygulamalar, Identity Identity Razor sınıf kitaplığı 'nda (RCL) bulunan kaynak kodu seçmeli olarak eklemek için desteği uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="e9740-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="e9740-107">Kodu değiştirebilmeniz ve davranışı değiştirebilmek için kaynak kodu oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e9740-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="e9740-108">Örneğin, kayıt sırasında kullanılan kodu oluşturmak için desteği ' ı söyleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e9740-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="e9740-109">Oluşturulan kod RCL 'deki aynı koda göre önceliklidir Identity .</span><span class="sxs-lookup"><span data-stu-id="e9740-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="e9740-110">Kullanıcı arabirimine tam denetim sağlamak ve varsayılan RCL 'yi kullanmak için, [tam Identity UI kaynağı oluşturma](#full)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="e9740-110">To gain full control of the UI and not use the default RCL, see the section [Create full Identity UI source](#full).</span></span>

<span data-ttu-id="e9740-111">Kimlik **doğrulaması içermeyen uygulamalar** , RCL paketini eklemek için desteği uygulayabilir Identity .</span><span class="sxs-lookup"><span data-stu-id="e9740-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="e9740-112">Oluşturulacak kodu seçme seçeneğiniz vardır Identity .</span><span class="sxs-lookup"><span data-stu-id="e9740-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="e9740-113">Desteği gerekli kodların çoğunu üretse de, işlemi gerçekleştirmek için projenizi güncelleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="e9740-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="e9740-114">Bu belgede, bir yapı iskelesi güncelleştirmesini tamamlaması için gereken adımlar açıklanmaktadır Identity .</span><span class="sxs-lookup"><span data-stu-id="e9740-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="e9740-115">Dosya farklılıklarını gösteren ve değişikliklerden geri dönüş yapmanızı sağlayan bir kaynak denetimi sistemi kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="e9740-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="e9740-116">Scaffolder çalıştırıldıktan sonra değişiklikleri inceleyin Identity .</span><span class="sxs-lookup"><span data-stu-id="e9740-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="e9740-117">[Iki öğeli kimlik doğrulaması](xref:security/authentication/identity-enable-qrcodes), [Hesap onaylama ve parola kurtarma](xref:security/authentication/accconfirm)ile diğer güvenlik özellikleri kullanılırken hizmetler gereklidir Identity .</span><span class="sxs-lookup"><span data-stu-id="e9740-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="e9740-118">Hizmetler veya hizmet saplamaları, yapı iskelesi sırasında oluşturulmaz Identity .</span><span class="sxs-lookup"><span data-stu-id="e9740-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="e9740-119">Bu özelliklerin etkinleştirilmesi için hizmetlerin el ile eklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="e9740-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="e9740-120">Örneğin, bkz. [e-posta onayı gerektir](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="e9740-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="e9740-121">IdentityYeni bir veri bağlamıyla, var olan bireysel hesaplara sahip bir projeye dönüştürme yaparken:</span><span class="sxs-lookup"><span data-stu-id="e9740-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="e9740-122">İçinde `Startup.ConfigureServices` , şu çağrıları kaldırın:</span><span class="sxs-lookup"><span data-stu-id="e9740-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="e9740-123">Örneğin, `AddDbContext` ve `AddDefaultIdentity` aşağıdaki kodda yorum yapılır:</span><span class="sxs-lookup"><span data-stu-id="e9740-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="e9740-124">Önceki kod, *alanlarda/ Identity / Identity HostingStartup.cs* yinelenen kodu Yorumlar</span><span class="sxs-lookup"><span data-stu-id="e9740-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="e9740-125">Genellikle, bireysel hesaplarla oluşturulan uygulamalar yeni bir veri bağlamı ***oluşturmamalıdır*** .</span><span class="sxs-lookup"><span data-stu-id="e9740-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="e9740-126">IdentityBoş bir projeye yapı iskelesi</span><span class="sxs-lookup"><span data-stu-id="e9740-126">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="e9740-127">`Startup`Sınıfını aşağıdakine benzer kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="e9740-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="e9740-128">Identity Razor Var olan yetkilendirme olmadan bir projeye yapı iskelesi yapın</span><span class="sxs-lookup"><span data-stu-id="e9740-128">Scaffold Identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="e9740-129">Identity, *alanlarında/ Identity / Identity HostingStartup.cs*' de yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="e9740-129">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="e9740-130">Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="e9740-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="e9740-131">Geçişler, UseAuthentication ve düzen</span><span class="sxs-lookup"><span data-stu-id="e9740-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="e9740-132">Kimlik doğrulamayı etkinleştir</span><span class="sxs-lookup"><span data-stu-id="e9740-132">Enable authentication</span></span>

<span data-ttu-id="e9740-133">`Startup`Sınıfını aşağıdakine benzer kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="e9740-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="e9740-134">Düzen değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="e9740-134">Layout changes</span></span>

<span data-ttu-id="e9740-135">İsteğe bağlı: () oturum açma bilgilerini `_LoginPartial` Düzen dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e9740-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="e9740-136">Identity Razor Yetkilendirmeyle bir projeye yapı</span><span class="sxs-lookup"><span data-stu-id="e9740-136">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="e9740-137">Bazı Identity Seçenekler, *alanlarda/ Identity / Identity HostingStartup.cs*içinde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="e9740-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="e9740-138">Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="e9740-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="e9740-139">IdentityVar olan yetkilendirme olmadan BIR MVC projesinde yapı iskelesi yapın</span><span class="sxs-lookup"><span data-stu-id="e9740-139">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="e9740-140">İsteğe bağlı: bir Login ( `_LoginPartial` ) öğesini *views/Shared/_Layout. cshtml* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e9740-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="e9740-141">*Pages/Shared/_LoginPartial. cshtml* dosyasını *views/Shared/_LoginPartial. cshtml* olarak taşıyın</span><span class="sxs-lookup"><span data-stu-id="e9740-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="e9740-142">Identity, *alanlarında/ Identity / Identity HostingStartup.cs*' de yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="e9740-142">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="e9740-143">Daha fazla bilgi için bkz. ıhostingstartup.</span><span class="sxs-lookup"><span data-stu-id="e9740-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="e9740-144">`Startup`Sınıfını aşağıdakine benzer kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="e9740-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="e9740-145">IdentityYetkilendirme ile BIR MVC projesinde yapı iskelesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="e9740-145">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a><span data-ttu-id="e9740-146">Identity Blazor Server Var olan yetkilendirme olmadan bir projeye yapı iskelesi yapın</span><span class="sxs-lookup"><span data-stu-id="e9740-146">Scaffold Identity into a Blazor Server project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="e9740-147">Identity, *alanlarında/ Identity / Identity HostingStartup.cs*' de yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="e9740-147">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="e9740-148">Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="e9740-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="e9740-149">Geçişler</span><span class="sxs-lookup"><span data-stu-id="e9740-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="e9740-150">Uygulamaya bir XSRF belirteci geçirin</span><span class="sxs-lookup"><span data-stu-id="e9740-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="e9740-151">Belirteçler bileşenlere geçirilebilir:</span><span class="sxs-lookup"><span data-stu-id="e9740-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="e9740-152">Kimlik doğrulama belirteçleri sağlandığında ve kimlik doğrulamasına kaydedildiğinde cookie , bunlar bileşenlere geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="e9740-152">When authentication tokens are provisioned and saved to the authentication cookie, they can be passed to components.</span></span>
* <span data-ttu-id="e9740-153">Razor bileşenler doğrudan kullanamaz `HttpContext` . bu nedenle, tarihinde oturum kapatma uç noktasına gönderi için bir [istek önleme zaman AŞıMı (XSRF) belirteci](xref:security/anti-request-forgery) elde etmenin bir yolu yoktur Identity `/Identity/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="e9740-153">Razor components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to Identity's logout endpoint at `/Identity/Account/Logout`.</span></span> <span data-ttu-id="e9740-154">Bir XSRF belirteci bileşenlere geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="e9740-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="e9740-155">Daha fazla bilgi için bkz. <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="e9740-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="e9740-156">*Pages/_Host. cshtml* dosyasında, ve sınıflarına eklendikten sonra belirteci oluşturun `InitialApplicationState` `TokenProvider` :</span><span class="sxs-lookup"><span data-stu-id="e9740-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="e9740-157">Şunu `App` atamak için bileşeni (*app. Razor*) güncelleştirin `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="e9740-157">Update the `App` component (*App.razor*) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="e9740-158">`TokenProvider`Konusunda gösterilen hizmet, `LoginDisplay` bileşeninde aşağıdaki [Düzen ve kimlik doğrulama akışı değişiklikleri](#layout-and-authentication-flow-changes) bölümünde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="e9740-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="e9740-159">Kimlik doğrulamayı etkinleştir</span><span class="sxs-lookup"><span data-stu-id="e9740-159">Enable authentication</span></span>

<span data-ttu-id="e9740-160">`Startup`Sınıfında:</span><span class="sxs-lookup"><span data-stu-id="e9740-160">In the `Startup` class:</span></span>

* <span data-ttu-id="e9740-161">RazorSayfaların hizmetlerinin eklendiğini onaylayın `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="e9740-161">Confirm that Razor Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="e9740-162">[TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)kullanılıyorsa, hizmeti kaydedin.</span><span class="sxs-lookup"><span data-stu-id="e9740-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="e9740-163">`UseDatabaseErrorPage`Geliştirme ortamı için içindeki uygulama oluşturucuda çağırın `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="e9740-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="e9740-164">Çağırın `UseAuthentication` ve `UseAuthorization` sonra `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="e9740-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="e9740-165">Sayfalar için bir uç nokta ekleyin Razor .</span><span class="sxs-lookup"><span data-stu-id="e9740-165">Add an endpoint for Razor Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="e9740-166">Düzen ve kimlik doğrulama akışı değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="e9740-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="e9740-167">`RedirectToLogin`Uygulamanın proje kökündeki *paylaşılan* klasörüne bir bileşen (*redirecttologin. Razor*) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e9740-167">Add a `RedirectToLogin` component (*RedirectToLogin.razor*) to the app's *Shared* folder in the project root:</span></span>

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

`LoginDisplay`Uygulamanın *paylaşılan* klasörüne bir bileşen (*logindisplay. Razor*) ekleyin. <span data-ttu-id="e9740-169">[TokenProvider hizmeti](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) , Identity oturum kapatma uç noktasına gönderilen HTML formu için XSRF belirteci sağlar:</span><span class="sxs-lookup"><span data-stu-id="e9740-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to Identity's logout endpoint:</span></span>

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

<span data-ttu-id="e9740-170">`MainLayout`Bileşende (*paylaşılan/mainlayout. Razor*), `LoginDisplay` bileşeni en üst satır `<div>` öğesinin içeriğine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e9740-170">In the `MainLayout` component (*Shared/MainLayout.razor*), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="e9740-171">Stil kimlik doğrulama uç noktaları</span><span class="sxs-lookup"><span data-stu-id="e9740-171">Style authentication endpoints</span></span>

<span data-ttu-id="e9740-172">Blazor Server Razor Sayfa sayfalarını kullandığından Identity , bir ziyaretçi sayfalar ve bileşenler arasında gezinse Kullanıcı arabiriminin stili değişir Identity .</span><span class="sxs-lookup"><span data-stu-id="e9740-172">Because Blazor Server uses Razor Pages Identity pages, the styling of the UI changes when a visitor navigates between Identity pages and components.</span></span> <span data-ttu-id="e9740-173">Uyumsuz stillere yönelik iki seçeneğiniz vardır:</span><span class="sxs-lookup"><span data-stu-id="e9740-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-no-locidentity-components"></a><span data-ttu-id="e9740-174">Derleme Identity bileşenleri</span><span class="sxs-lookup"><span data-stu-id="e9740-174">Build Identity components</span></span>

<span data-ttu-id="e9740-175">Bileşenler oluşturmak için bileşenleri kullanmanın bir yaklaşımı Identity , Identity bileşenleri oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="e9740-175">An approach to using components for Identity instead of pages is to build Identity components.</span></span> <span data-ttu-id="e9740-176">`SignInManager` `UserManager` Razor Bileşenlerinde desteklenmediğinden, Blazor Server Kullanıcı hesabı EYLEMLERINI işlemek için uygulamadaki API uç noktalarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="e9740-176">Because `SignInManager` and `UserManager` aren't supported in Razor components, use API endpoints in the Blazor Server app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a><span data-ttu-id="e9740-177">Uygulama stilleriyle özel düzen kullanma Blazor</span><span class="sxs-lookup"><span data-stu-id="e9740-177">Use a custom layout with Blazor app styles</span></span>

<span data-ttu-id="e9740-178">IdentitySayfa düzeni ve stilleri, varsayılan temayı kullanan sayfalar oluşturmak için değiştirilebilir Blazor .</span><span class="sxs-lookup"><span data-stu-id="e9740-178">The Identity pages layout and styles can be modified to produce pages that use the default Blazor theme.</span></span>

> [!NOTE]
> <span data-ttu-id="e9740-179">Bu bölümdeki örnek yalnızca özelleştirme için bir başlangıç noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="e9740-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="e9740-180">En iyi kullanıcı deneyimi için büyük olasılıkla ek çalışma gerekir.</span><span class="sxs-lookup"><span data-stu-id="e9740-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="e9740-181">Yeni bir `NavMenu_IdentityLayout` bileşen oluşturun (*paylaşılan/NavMenu_ Identity Layout. Razor*).</span><span class="sxs-lookup"><span data-stu-id="e9740-181">Create a new `NavMenu_IdentityLayout` component (*Shared/NavMenu_IdentityLayout.razor*).</span></span> <span data-ttu-id="e9740-182">Bileşenin biçimlendirmesi ve kodu için, uygulamanın bileşeniyle aynı içeriği kullanın `NavMenu` (*paylaşılan/navmenu. Razor*).</span><span class="sxs-lookup"><span data-stu-id="e9740-182">For the markup and code of the component, use the same content of the app's `NavMenu` component (*Shared/NavMenu.razor*).</span></span> <span data-ttu-id="e9740-183">`NavLink`Bileşendeki otomatik yeniden yönlendirmeler `RedirectToLogin` kimlik doğrulama veya yetkilendirme gerektiren bileşenler için başarısız olduğundan anonim olarak erişilemeyen bileşenlere her birini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="e9740-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="e9740-184">*Sayfalar/paylaşılan/Layout. cshtml* dosyasında aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="e9740-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="e9740-185">RazorEtiket yardımcıları ve uygulamanın bileşenlerini *paylaşılan* klasörde kullanmak için dosyanın en üstüne yönergeler ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e9740-185">Add Razor directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="e9740-186">`{APPLICATION ASSEMBLY}`Uygulamanın derleme adıyla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="e9740-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="e9740-187">İçeriğe bir `<base>` etiket ve Blazor stil sayfası ekleyin `<link>` `<head>` :</span><span class="sxs-lookup"><span data-stu-id="e9740-187">Add a `<base>` tag and Blazor stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="e9740-188">`<body>`Etiketin içeriğini aşağıdaki şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="e9740-188">Change the content of the `<body>` tag to the following:</span></span>

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

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a><span data-ttu-id="e9740-189">Identity Blazor Server Yetkilendirmeyle bir projeye yapı</span><span class="sxs-lookup"><span data-stu-id="e9740-189">Scaffold Identity into a Blazor Server project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="e9740-190">Bazı Identity Seçenekler, *alanlarda/ Identity / Identity HostingStartup.cs*içinde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="e9740-190">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="e9740-191">Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="e9740-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="e9740-192">Tam Identity UI kaynağı oluştur</span><span class="sxs-lookup"><span data-stu-id="e9740-192">Create full Identity UI source</span></span>

<span data-ttu-id="e9740-193">Kullanıcı arabiriminin tam denetimini sürdürmek için Identity , Identity desteği ' ı çalıştırın ve **tüm dosyaları geçersiz kıl**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="e9740-193">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="e9740-194">Aşağıdaki vurgulanan kod, varsayılan Identity Kullanıcı arabirimini Identity ASP.NET Core 2,1 Web uygulamasında değiştirecek değişiklikleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="e9740-194">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="e9740-195">Bunu, Kullanıcı arabirimine tam denetim sağlamak için yapmak isteyebilirsiniz Identity .</span><span class="sxs-lookup"><span data-stu-id="e9740-195">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="e9740-196">Varsayılan değer Identity aşağıdaki kodda değiştirilmiştir:</span><span class="sxs-lookup"><span data-stu-id="e9740-196">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="e9740-197">Aşağıdaki kod, [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)ve [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)öğesini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="e9740-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="e9740-198">Bir `IEmailSender` uygulamayı kaydedin, örneğin:</span><span class="sxs-lookup"><span data-stu-id="e9740-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="e9740-199">Parola Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e9740-199">Password configuration</span></span>

<span data-ttu-id="e9740-200">' <xref:Microsoft.AspNetCore.Identity.PasswordOptions> De yapılandırılmışsa `Startup.ConfigureServices` , [ `[StringLength]` ](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` Yapı iskelesi sayfalarında özelliği için öznitelik yapılandırması gerekli olabilir Identity .</span><span class="sxs-lookup"><span data-stu-id="e9740-200">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="e9740-201">`InputModel``Password`aşağıdaki dosyalarda özellikler bulunur:</span><span class="sxs-lookup"><span data-stu-id="e9740-201">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a><span data-ttu-id="e9740-202">Sayfayı devre dışı bırakma</span><span class="sxs-lookup"><span data-stu-id="e9740-202">Disable a page</span></span>

<span data-ttu-id="e9740-203">Bu bölümler, kaydetme sayfasının nasıl devre dışı bırakılacağını gösterir, ancak herhangi bir sayfayı devre dışı bırakmak için yaklaşım kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="e9740-203">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="e9740-204">Kullanıcı kaydını devre dışı bırakmak için:</span><span class="sxs-lookup"><span data-stu-id="e9740-204">To disable user registration:</span></span>

* <span data-ttu-id="e9740-205">Yapı iskelesi Identity .</span><span class="sxs-lookup"><span data-stu-id="e9740-205">Scaffold Identity.</span></span> <span data-ttu-id="e9740-206">Account. Register, Account. Login ve account. RegisterConfirmation bilgilerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e9740-206">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="e9740-207">Örnek:</span><span class="sxs-lookup"><span data-stu-id="e9740-207">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="e9740-208">Kullanıcıları bu uç noktadan kaydedememesi için *alanı/ Identity /Pages/Account/Register.cshtml.cs* güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="e9740-208">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="e9740-209">Önceki değişikliklerle tutarlı olması için *Areas/ Identity /Pages/Account/Register.exe* öğesini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="e9740-209">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="e9740-210">*/ Identity /Pages/Account/Login.exe* içindeki kayıt bağlantısını açıklama veya Kaldır</span><span class="sxs-lookup"><span data-stu-id="e9740-210">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="e9740-211">*Areas/ Identity /Pages/Account/registerconfirmation* sayfasını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="e9740-211">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="e9740-212">Cshtml dosyasındaki kodu ve bağlantıları kaldırın.</span><span class="sxs-lookup"><span data-stu-id="e9740-212">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="e9740-213">Onay kodunu şuradan kaldırın `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="e9740-213">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="e9740-214">Kullanıcıları eklemek için başka bir uygulama kullanma</span><span class="sxs-lookup"><span data-stu-id="e9740-214">Use another app to add users</span></span>

<span data-ttu-id="e9740-215">Web uygulaması dışındaki kullanıcıları eklemek için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="e9740-215">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="e9740-216">Kullanıcı ekleme seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="e9740-216">Options to add users include:</span></span>

* <span data-ttu-id="e9740-217">Adanmış bir yönetim Web uygulaması.</span><span class="sxs-lookup"><span data-stu-id="e9740-217">A dedicated admin web app.</span></span>
* <span data-ttu-id="e9740-218">Bir konsol uygulaması.</span><span class="sxs-lookup"><span data-stu-id="e9740-218">A console app.</span></span>

<span data-ttu-id="e9740-219">Aşağıdaki kod, kullanıcı eklemeye yönelik bir yaklaşımı özetler:</span><span class="sxs-lookup"><span data-stu-id="e9740-219">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="e9740-220">Kullanıcıların listesi belleği okur.</span><span class="sxs-lookup"><span data-stu-id="e9740-220">A list of users is read into memory.</span></span>
* <span data-ttu-id="e9740-221">Her Kullanıcı için güçlü bir benzersiz parola oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e9740-221">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="e9740-222">Kullanıcı Identity veritabanına eklenir.</span><span class="sxs-lookup"><span data-stu-id="e9740-222">The user is added to the Identity database.</span></span>
* <span data-ttu-id="e9740-223">Kullanıcıya bildirilir ve parolayı değiştirmesi bildirilir.</span><span class="sxs-lookup"><span data-stu-id="e9740-223">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="e9740-224">Aşağıdaki kod, bir kullanıcı ekleme ana hatlarıyla verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="e9740-224">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="e9740-225">Üretim senaryolarında de benzer bir yaklaşım izlenebilir.</span><span class="sxs-lookup"><span data-stu-id="e9740-225">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="e9740-226">Statik varlıkların yayımlanmasını Engelle Identity</span><span class="sxs-lookup"><span data-stu-id="e9740-226">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="e9740-227">Statik Identity varlıkların Web köküne yayımlanmasını engellemek için bkz <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> ..</span><span class="sxs-lookup"><span data-stu-id="e9740-227">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e9740-228">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e9740-228">Additional resources</span></span>

* [<span data-ttu-id="e9740-229">ASP.NET Core 2,1 ve üzeri kimlik doğrulama kodundaki değişiklikler</span><span class="sxs-lookup"><span data-stu-id="e9740-229">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="e9740-230">ASP.NET Core 2,1 ve üzeri [ASP.NET Core Identity](xref:security/authentication/identity) bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="e9740-230">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="e9740-231">Dahil edilen uygulamalar, Identity Identity Razor sınıf kitaplığı 'nda (RCL) bulunan kaynak kodu seçmeli olarak eklemek için desteği uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="e9740-231">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="e9740-232">Kodu değiştirebilmeniz ve davranışı değiştirebilmek için kaynak kodu oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e9740-232">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="e9740-233">Örneğin, kayıt sırasında kullanılan kodu oluşturmak için desteği ' ı söyleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="e9740-233">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="e9740-234">Oluşturulan kod RCL 'deki aynı koda göre önceliklidir Identity .</span><span class="sxs-lookup"><span data-stu-id="e9740-234">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="e9740-235">Kullanıcı arabirimine tam denetim sağlamak ve varsayılan RCL 'yi kullanmak için, [tam KIMLIK UI kaynağı oluşturma](#full)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="e9740-235">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="e9740-236">Kimlik **doğrulaması içermeyen uygulamalar** , RCL paketini eklemek için desteği uygulayabilir Identity .</span><span class="sxs-lookup"><span data-stu-id="e9740-236">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="e9740-237">Oluşturulacak kodu seçme seçeneğiniz vardır Identity .</span><span class="sxs-lookup"><span data-stu-id="e9740-237">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="e9740-238">Desteği gerekli kodların çoğunu üretse de, işlemi gerçekleştirmek için projenizi güncelleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="e9740-238">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="e9740-239">Bu belgede, bir yapı iskelesi güncelleştirmesini tamamlaması için gereken adımlar açıklanmaktadır Identity .</span><span class="sxs-lookup"><span data-stu-id="e9740-239">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="e9740-240">IdentityDesteği çalıştırıldığında, proje dizininde bir *ScaffoldingReadme.txt* dosyası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e9740-240">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="e9740-241">*ScaffoldingReadme.txt* dosyası, yapı iskelesi güncelleştirmesinin tamamlanabilmesi için gerekli olan genel yönergeleri içerir Identity .</span><span class="sxs-lookup"><span data-stu-id="e9740-241">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="e9740-242">Bu belge, *ScaffoldingReadme.txt* dosyasından daha ayrıntılı yönergeler içerir.</span><span class="sxs-lookup"><span data-stu-id="e9740-242">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="e9740-243">Dosya farklılıklarını gösteren ve değişikliklerden geri dönüş yapmanızı sağlayan bir kaynak denetimi sistemi kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="e9740-243">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="e9740-244">Scaffolder çalıştırıldıktan sonra değişiklikleri inceleyin Identity .</span><span class="sxs-lookup"><span data-stu-id="e9740-244">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="e9740-245">[Iki öğeli kimlik doğrulaması](xref:security/authentication/identity-enable-qrcodes), [Hesap onaylama ve parola kurtarma](xref:security/authentication/accconfirm)ile diğer güvenlik özellikleri kullanılırken hizmetler gereklidir Identity .</span><span class="sxs-lookup"><span data-stu-id="e9740-245">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="e9740-246">Hizmetler veya hizmet saplamaları, yapı iskelesi sırasında oluşturulmaz Identity .</span><span class="sxs-lookup"><span data-stu-id="e9740-246">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="e9740-247">Bu özelliklerin etkinleştirilmesi için hizmetlerin el ile eklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="e9740-247">Services to enable these features must be added manually.</span></span> <span data-ttu-id="e9740-248">Örneğin, bkz. [e-posta onayı gerektir](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="e9740-248">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="e9740-249">IdentityBoş bir projeye yapı iskelesi</span><span class="sxs-lookup"><span data-stu-id="e9740-249">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="e9740-250">Aşağıdaki Vurgulanan çağrıları `Startup` sınıfına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e9740-250">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="e9740-251">Identity Razor Var olan yetkilendirme olmadan bir projeye yapı iskelesi yapın</span><span class="sxs-lookup"><span data-stu-id="e9740-251">Scaffold Identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="e9740-252">Identity, *alanlarında/ Identity / Identity HostingStartup.cs*' de yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="e9740-252">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="e9740-253">Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="e9740-253">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="e9740-254">Geçişler, UseAuthentication ve düzen</span><span class="sxs-lookup"><span data-stu-id="e9740-254">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="e9740-255">Kimlik doğrulamayı etkinleştir</span><span class="sxs-lookup"><span data-stu-id="e9740-255">Enable authentication</span></span>

<span data-ttu-id="e9740-256">`Configure` `Startup` Sınıfının yönteminde, sonrasında [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ' ı çağırın `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="e9740-256">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="e9740-257">Düzen değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="e9740-257">Layout changes</span></span>

<span data-ttu-id="e9740-258">İsteğe bağlı: () oturum açma bilgilerini `_LoginPartial` Düzen dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e9740-258">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="e9740-259">Identity Razor Yetkilendirmeyle bir projeye yapı</span><span class="sxs-lookup"><span data-stu-id="e9740-259">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="e9740-260">Bazı Identity Seçenekler, *alanlarda/ Identity / Identity HostingStartup.cs*içinde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="e9740-260">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="e9740-261">Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="e9740-261">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="e9740-262">IdentityVar olan yetkilendirme olmadan BIR MVC projesinde yapı iskelesi yapın</span><span class="sxs-lookup"><span data-stu-id="e9740-262">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="e9740-263">İsteğe bağlı: bir Login ( `_LoginPartial` ) öğesini *views/Shared/_Layout. cshtml* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="e9740-263">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="e9740-264">*Pages/Shared/_LoginPartial. cshtml* dosyasını *views/Shared/_LoginPartial. cshtml* olarak taşıyın</span><span class="sxs-lookup"><span data-stu-id="e9740-264">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="e9740-265">Identity, *alanlarında/ Identity / Identity HostingStartup.cs*' de yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="e9740-265">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="e9740-266">Daha fazla bilgi için bkz. ıhostingstartup.</span><span class="sxs-lookup"><span data-stu-id="e9740-266">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="e9740-267">Şu tarihten sonra [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) çağrısı `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="e9740-267">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="e9740-268">IdentityYetkilendirme ile BIR MVC projesinde yapı iskelesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="e9740-268">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="e9740-269">*Sayfaları/paylaşılan* klasörünü ve bu klasördeki dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="e9740-269">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="e9740-270">Tam Identity UI kaynağı oluştur</span><span class="sxs-lookup"><span data-stu-id="e9740-270">Create full Identity UI source</span></span>

<span data-ttu-id="e9740-271">Kullanıcı arabiriminin tam denetimini sürdürmek için Identity , Identity desteği ' ı çalıştırın ve **tüm dosyaları geçersiz kıl**' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="e9740-271">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="e9740-272">Aşağıdaki vurgulanan kod, varsayılan Identity Kullanıcı arabirimini Identity ASP.NET Core 2,1 Web uygulamasında değiştirecek değişiklikleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="e9740-272">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="e9740-273">Bunu, Kullanıcı arabirimine tam denetim sağlamak için yapmak isteyebilirsiniz Identity .</span><span class="sxs-lookup"><span data-stu-id="e9740-273">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="e9740-274">Varsayılan değer Identity aşağıdaki kodda değiştirilmiştir:</span><span class="sxs-lookup"><span data-stu-id="e9740-274">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="e9740-275">Aşağıdaki kod, [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)ve [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)öğesini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="e9740-275">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="e9740-276">Bir `IEmailSender` uygulamayı kaydedin, örneğin:</span><span class="sxs-lookup"><span data-stu-id="e9740-276">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="e9740-277">Parola Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="e9740-277">Password configuration</span></span>

<span data-ttu-id="e9740-278">' <xref:Microsoft.AspNetCore.Identity.PasswordOptions> De yapılandırılmışsa `Startup.ConfigureServices` , [ `[StringLength]` ](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` Yapı iskelesi sayfalarında özelliği için öznitelik yapılandırması gerekli olabilir Identity .</span><span class="sxs-lookup"><span data-stu-id="e9740-278">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="e9740-279">`InputModel``Password`aşağıdaki dosyalarda özellikler bulunur:</span><span class="sxs-lookup"><span data-stu-id="e9740-279">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a><span data-ttu-id="e9740-280">Kayıt sayfasını devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="e9740-280">Disable register page</span></span>

<span data-ttu-id="e9740-281">Kullanıcı kaydını devre dışı bırakmak için:</span><span class="sxs-lookup"><span data-stu-id="e9740-281">To disable user registration:</span></span>

* <span data-ttu-id="e9740-282">Yapı iskelesi Identity .</span><span class="sxs-lookup"><span data-stu-id="e9740-282">Scaffold Identity.</span></span> <span data-ttu-id="e9740-283">Account. Register, Account. Login ve account. RegisterConfirmation bilgilerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="e9740-283">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="e9740-284">Örnek:</span><span class="sxs-lookup"><span data-stu-id="e9740-284">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="e9740-285">Kullanıcıları bu uç noktadan kaydedememesi için *alanı/ Identity /Pages/Account/Register.cshtml.cs* güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="e9740-285">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="e9740-286">Önceki değişikliklerle tutarlı olması için *Areas/ Identity /Pages/Account/Register.exe* öğesini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="e9740-286">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="e9740-287">*/ Identity /Pages/Account/Login.exe* içindeki kayıt bağlantısını açıklama veya Kaldır</span><span class="sxs-lookup"><span data-stu-id="e9740-287">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="e9740-288">*Areas/ Identity /Pages/Account/registerconfirmation* sayfasını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="e9740-288">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="e9740-289">Cshtml dosyasındaki kodu ve bağlantıları kaldırın.</span><span class="sxs-lookup"><span data-stu-id="e9740-289">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="e9740-290">Onay kodunu şuradan kaldırın `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="e9740-290">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="e9740-291">Kullanıcıları eklemek için başka bir uygulama kullanma</span><span class="sxs-lookup"><span data-stu-id="e9740-291">Use another app to add users</span></span>

<span data-ttu-id="e9740-292">Web uygulaması dışındaki kullanıcıları eklemek için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="e9740-292">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="e9740-293">Kullanıcı ekleme seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="e9740-293">Options to add users include:</span></span>

* <span data-ttu-id="e9740-294">Adanmış bir yönetim Web uygulaması.</span><span class="sxs-lookup"><span data-stu-id="e9740-294">A dedicated admin web app.</span></span>
* <span data-ttu-id="e9740-295">Bir konsol uygulaması.</span><span class="sxs-lookup"><span data-stu-id="e9740-295">A console app.</span></span>

<span data-ttu-id="e9740-296">Aşağıdaki kod, kullanıcı eklemeye yönelik bir yaklaşımı özetler:</span><span class="sxs-lookup"><span data-stu-id="e9740-296">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="e9740-297">Kullanıcıların listesi belleği okur.</span><span class="sxs-lookup"><span data-stu-id="e9740-297">A list of users is read into memory.</span></span>
* <span data-ttu-id="e9740-298">Her Kullanıcı için güçlü bir benzersiz parola oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="e9740-298">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="e9740-299">Kullanıcı Identity veritabanına eklenir.</span><span class="sxs-lookup"><span data-stu-id="e9740-299">The user is added to the Identity database.</span></span>
* <span data-ttu-id="e9740-300">Kullanıcıya bildirilir ve parolayı değiştirmesi bildirilir.</span><span class="sxs-lookup"><span data-stu-id="e9740-300">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="e9740-301">Aşağıdaki kod, bir kullanıcı ekleme ana hatlarıyla verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="e9740-301">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="e9740-302">Üretim senaryolarında de benzer bir yaklaşım izlenebilir.</span><span class="sxs-lookup"><span data-stu-id="e9740-302">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e9740-303">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="e9740-303">Additional resources</span></span>

* [<span data-ttu-id="e9740-304">ASP.NET Core 2,1 ve üzeri kimlik doğrulama kodundaki değişiklikler</span><span class="sxs-lookup"><span data-stu-id="e9740-304">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
