---
title: 'ASP.NET Core projelerinde yapı iskelesi :::no-loc(Identity):::'
author: rick-anderson
description: 'ASP.NET Core projesindeki fkatlama oluşturmayı öğrenin :::no-loc(Identity)::: .'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 813dd7837c265c78c584d66dd51bc23399d12fbe
ms.sourcegitcommit: 5156eab2118584405eb663e1fcd82f8bd7764504
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2020
ms.locfileid: "93141501"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a><span data-ttu-id="b1eef-103">ASP.NET Core projelerinde yapı iskelesi :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="b1eef-103">Scaffold :::no-loc(Identity)::: in ASP.NET Core projects</span></span>

<span data-ttu-id="b1eef-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b1eef-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b1eef-105">ASP.NET Core [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) bir [ :::no-loc(Razor)::: sınıf kitaplığı](xref:razor-pages/ui-class)olarak sunulur.</span><span class="sxs-lookup"><span data-stu-id="b1eef-105">ASP.NET Core provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="b1eef-106">Dahil edilen uygulamalar, :::no-loc(Identity)::: :::no-loc(Identity)::: :::no-loc(Razor)::: sınıf kitaplığı 'nda (RCL) bulunan kaynak kodu seçmeli olarak eklemek için desteği uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="b1eef-106">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="b1eef-107">Kodu değiştirebilmeniz ve davranışı değiştirebilmek için kaynak kodu oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b1eef-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="b1eef-108">Örneğin, kayıt sırasında kullanılan kodu oluşturmak için desteği ' ı söyleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b1eef-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="b1eef-109">Oluşturulan kod RCL 'deki aynı koda göre önceliklidir :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-109">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="b1eef-110">Kullanıcı arabirimine tam denetim sağlamak ve varsayılan RCL 'yi kullanmak için, [tam :::no-loc(Identity)::: UI kaynağı oluşturma](#full)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b1eef-110">To gain full control of the UI and not use the default RCL, see the section [Create full :::no-loc(Identity)::: UI source](#full).</span></span>

<span data-ttu-id="b1eef-111">Kimlik **doğrulaması içermeyen uygulamalar** , RCL paketini eklemek için desteği uygulayabilir :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="b1eef-112">Oluşturulacak kodu seçme seçeneğiniz vardır :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-112">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="b1eef-113">Desteği gerekli kodların çoğunu üretse de, işlemi gerçekleştirmek için projenizi güncelleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="b1eef-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="b1eef-114">Bu belgede, bir yapı iskelesi güncelleştirmesini tamamlaması için gereken adımlar açıklanmaktadır :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-114">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="b1eef-115">Dosya farklılıklarını gösteren ve değişikliklerden geri dönüş yapmanızı sağlayan bir kaynak denetimi sistemi kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="b1eef-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="b1eef-116">Scaffolder çalıştırıldıktan sonra değişiklikleri inceleyin :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-116">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

<span data-ttu-id="b1eef-117">[Iki öğeli kimlik doğrulaması](xref:security/authentication/identity-enable-qrcodes), [Hesap onaylama ve parola kurtarma](xref:security/authentication/accconfirm)ile diğer güvenlik özellikleri kullanılırken hizmetler gereklidir :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="b1eef-118">Hizmetler veya hizmet saplamaları, yapı iskelesi sırasında oluşturulmaz :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-118">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="b1eef-119">Bu özelliklerin etkinleştirilmesi için hizmetlerin el ile eklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="b1eef-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="b1eef-120">Örneğin, bkz. [e-posta onayı gerektir](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="b1eef-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="b1eef-121">:::no-loc(Identity):::Yeni bir veri bağlamıyla, var olan bireysel hesaplara sahip bir projeye dönüştürme yaparken:</span><span class="sxs-lookup"><span data-stu-id="b1eef-121">When scaffolding :::no-loc(Identity)::: with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="b1eef-122">İçinde `Startup.ConfigureServices` , şu çağrıları kaldırın:</span><span class="sxs-lookup"><span data-stu-id="b1eef-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefault:::no-loc(Identity):::`

<span data-ttu-id="b1eef-123">Örneğin, `AddDbContext` ve `AddDefault:::no-loc(Identity):::` aşağıdaki kodda yorum yapılır:</span><span class="sxs-lookup"><span data-stu-id="b1eef-123">For example, `AddDbContext` and `AddDefault:::no-loc(Identity):::` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="b1eef-124">Önceki kod, *alanlarda/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* yinelenen kodu Yorumlar</span><span class="sxs-lookup"><span data-stu-id="b1eef-124">The preceeding code comments out the code that is duplicated in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs*</span></span>

<span data-ttu-id="b1eef-125">Genellikle, bireysel hesaplarla oluşturulan uygulamalar \* **değil** _ yeni bir veri bağlamı oluşturmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="b1eef-125">Typically, apps that were created with individual accounts should \* **not** _ create a new data context.</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="b1eef-126">:::no-loc(Identity):::Boş bir projeye yapı iskelesi</span><span class="sxs-lookup"><span data-stu-id="b1eef-126">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b1eef-127">`Startup`Sınıfını aşağıdakine benzer kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="b1eef-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="b1eef-128">:::no-loc(Identity)::: :::no-loc(Razor)::: Var olan yetkilendirme olmadan bir projeye yapı iskelesi yapın</span><span class="sxs-lookup"><span data-stu-id="b1eef-128">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add Create:::no-loc(Identity):::Schema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b1eef-129">:::no-loc(Identity):::_Areas/HostingStartup.cs \* ' de yapılandırılır :::no-loc(Identity)::: / :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-129">:::no-loc(Identity)::: is configured in _Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs\*.</span></span> <span data-ttu-id="b1eef-130">Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b1eef-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="b1eef-131">Geçişler, UseAuthentication ve düzen</span><span class="sxs-lookup"><span data-stu-id="b1eef-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="b1eef-132">Kimlik doğrulamayı etkinleştir</span><span class="sxs-lookup"><span data-stu-id="b1eef-132">Enable authentication</span></span>

<span data-ttu-id="b1eef-133">`Startup`Sınıfını aşağıdakine benzer kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="b1eef-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="b1eef-134">Düzen değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="b1eef-134">Layout changes</span></span>

<span data-ttu-id="b1eef-135">İsteğe bağlı: () oturum açma bilgilerini `_LoginPartial` Düzen dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b1eef-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="b1eef-136">:::no-loc(Identity)::: :::no-loc(Razor)::: Yetkilendirmeyle bir projeye yapı</span><span class="sxs-lookup"><span data-stu-id="b1eef-136">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

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

<span data-ttu-id="b1eef-137">Bazı :::no-loc(Identity)::: Seçenekler, *alanlarda/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* içinde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="b1eef-137">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="b1eef-138">Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b1eef-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="b1eef-139">:::no-loc(Identity):::Var olan yetkilendirme olmadan BIR MVC projesinde yapı iskelesi yapın</span><span class="sxs-lookup"><span data-stu-id="b1eef-139">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b1eef-140">İsteğe bağlı: bir Login ( `_LoginPartial` ) öğesini *views/Shared/_Layout. cshtml* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b1eef-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="b1eef-141">*Pages/Shared/_LoginPartial. cshtml* dosyasını *views/Shared/_LoginPartial. cshtml* olarak taşıyın</span><span class="sxs-lookup"><span data-stu-id="b1eef-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="b1eef-142">:::no-loc(Identity):::, *alanlarında/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* ' de yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="b1eef-142">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="b1eef-143">Daha fazla bilgi için bkz. ıhostingstartup.</span><span class="sxs-lookup"><span data-stu-id="b1eef-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="b1eef-144">`Startup`Sınıfını aşağıdakine benzer kodla güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="b1eef-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="b1eef-145">:::no-loc(Identity):::Yetkilendirme ile BIR MVC projesinde yapı iskelesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="b1eef-145">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a><span data-ttu-id="b1eef-146">:::no-loc(Identity)::: :::no-loc(Blazor Server)::: Var olan yetkilendirme olmadan bir projeye yapı iskelesi yapın</span><span class="sxs-lookup"><span data-stu-id="b1eef-146">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b1eef-147">:::no-loc(Identity):::, *alanlarında/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* ' de yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="b1eef-147">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="b1eef-148">Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b1eef-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="b1eef-149">Geçişler</span><span class="sxs-lookup"><span data-stu-id="b1eef-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="b1eef-150">Uygulamaya bir XSRF belirteci geçirin</span><span class="sxs-lookup"><span data-stu-id="b1eef-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="b1eef-151">Belirteçler bileşenlere geçirilebilir:</span><span class="sxs-lookup"><span data-stu-id="b1eef-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="b1eef-152">Kimlik doğrulama belirteçleri sağlandığında ve kimlik doğrulamasına kaydedildiğinde :::no-loc(cookie)::: , bunlar bileşenlere geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="b1eef-152">When authentication tokens are provisioned and saved to the authentication :::no-loc(cookie):::, they can be passed to components.</span></span>
* <span data-ttu-id="b1eef-153">:::no-loc(Razor)::: bileşenler doğrudan kullanamaz `HttpContext` . bu nedenle, tarihinde oturum kapatma uç noktasına gönderi için bir [istek önleme zaman AŞıMı (XSRF) belirteci](xref:security/anti-request-forgery) elde etmenin bir yolu yoktur :::no-loc(Identity)::: `/:::no-loc(Identity):::/Account/Logout` .</span><span class="sxs-lookup"><span data-stu-id="b1eef-153">:::no-loc(Razor)::: components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to :::no-loc(Identity):::'s logout endpoint at `/:::no-loc(Identity):::/Account/Logout`.</span></span> <span data-ttu-id="b1eef-154">Bir XSRF belirteci bileşenlere geçirilebilir.</span><span class="sxs-lookup"><span data-stu-id="b1eef-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="b1eef-155">Daha fazla bilgi için bkz. <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span><span class="sxs-lookup"><span data-stu-id="b1eef-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="b1eef-156">*Pages/_Host. cshtml* dosyasında, ve sınıflarına eklendikten sonra belirteci oluşturun `InitialApplicationState` `TokenProvider` :</span><span class="sxs-lookup"><span data-stu-id="b1eef-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="b1eef-157">Şunu `App` atamak için bileşeni ( *app. Razor* ) güncelleştirin `InitialState.XsrfToken` :</span><span class="sxs-lookup"><span data-stu-id="b1eef-157">Update the `App` component ( *App.razor* ) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="b1eef-158">`TokenProvider`Konusunda gösterilen hizmet, `LoginDisplay` bileşeninde aşağıdaki [Düzen ve kimlik doğrulama akışı değişiklikleri](#layout-and-authentication-flow-changes) bölümünde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b1eef-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="b1eef-159">Kimlik doğrulamayı etkinleştir</span><span class="sxs-lookup"><span data-stu-id="b1eef-159">Enable authentication</span></span>

<span data-ttu-id="b1eef-160">`Startup`Sınıfında:</span><span class="sxs-lookup"><span data-stu-id="b1eef-160">In the `Startup` class:</span></span>

* <span data-ttu-id="b1eef-161">:::no-loc(Razor):::Sayfaların hizmetlerinin eklendiğini onaylayın `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b1eef-161">Confirm that :::no-loc(Razor)::: Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="b1eef-162">[TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)kullanılıyorsa, hizmeti kaydedin.</span><span class="sxs-lookup"><span data-stu-id="b1eef-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="b1eef-163">`UseDatabaseErrorPage`Geliştirme ortamı için içindeki uygulama oluşturucuda çağırın `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b1eef-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="b1eef-164">Çağırın `UseAuthentication` ve `UseAuthorization` sonra `UseRouting` .</span><span class="sxs-lookup"><span data-stu-id="b1eef-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="b1eef-165">Sayfalar için bir uç nokta ekleyin :::no-loc(Razor)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-165">Add an endpoint for :::no-loc(Razor)::: Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/Startup:::no-loc(Blazor):::.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="b1eef-166">Düzen ve kimlik doğrulama akışı değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="b1eef-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="b1eef-167">`RedirectToLogin`Uygulamanın proje kökündeki *paylaşılan* klasörüne bir bileşen ( *redirecttologin. Razor* ) ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b1eef-167">Add a `RedirectToLogin` component ( *RedirectToLogin.razor* ) to the app's *Shared* folder in the project root:</span></span>

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(":::no-loc(Identity):::/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

`LoginDisplay`Uygulamanın *paylaşılan* klasörüne bir bileşen ( *logindisplay. Razor* ) ekleyin. <span data-ttu-id="b1eef-169">[TokenProvider hizmeti](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) , :::no-loc(Identity)::: oturum kapatma uç noktasına gönderilen HTML formu için XSRF belirteci sağlar:</span><span class="sxs-lookup"><span data-stu-id="b1eef-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to :::no-loc(Identity):::'s logout endpoint:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href=":::no-loc(Identity):::/Account/Manage/Index">
            Hello, @context.User.:::no-loc(Identity):::.Name!
        </a>
        <form action="/:::no-loc(Identity):::/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href=":::no-loc(Identity):::/Account/Register">Register</a>
        <a href=":::no-loc(Identity):::/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="b1eef-170">`MainLayout`Bileşende ( *paylaşılan/mainlayout. Razor* ), `LoginDisplay` bileşeni en üst satır `<div>` öğesinin içeriğine ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b1eef-170">In the `MainLayout` component ( *Shared/MainLayout.razor* ), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="b1eef-171">Stil kimlik doğrulama uç noktaları</span><span class="sxs-lookup"><span data-stu-id="b1eef-171">Style authentication endpoints</span></span>

<span data-ttu-id="b1eef-172">:::no-loc(Blazor Server)::: :::no-loc(Razor)::: Sayfa sayfalarını kullandığından :::no-loc(Identity)::: , bir ziyaretçi sayfalar ve bileşenler arasında gezinse Kullanıcı arabiriminin stili değişir :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-172">Because :::no-loc(Blazor Server)::: uses :::no-loc(Razor)::: Pages :::no-loc(Identity)::: pages, the styling of the UI changes when a visitor navigates between :::no-loc(Identity)::: pages and components.</span></span> <span data-ttu-id="b1eef-173">Uyumsuz stillere yönelik iki seçeneğiniz vardır:</span><span class="sxs-lookup"><span data-stu-id="b1eef-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-no-locidentity-components"></a><span data-ttu-id="b1eef-174">Derleme :::no-loc(Identity)::: bileşenleri</span><span class="sxs-lookup"><span data-stu-id="b1eef-174">Build :::no-loc(Identity)::: components</span></span>

<span data-ttu-id="b1eef-175">Bileşenler oluşturmak için bileşenleri kullanmanın bir yaklaşımı :::no-loc(Identity)::: , :::no-loc(Identity)::: bileşenleri oluşturmaktır.</span><span class="sxs-lookup"><span data-stu-id="b1eef-175">An approach to using components for :::no-loc(Identity)::: instead of pages is to build :::no-loc(Identity)::: components.</span></span> <span data-ttu-id="b1eef-176">`SignInManager` `UserManager` :::no-loc(Razor)::: Bileşenlerinde desteklenmediğinden, :::no-loc(Blazor Server)::: Kullanıcı hesabı EYLEMLERINI işlemek için uygulamadaki API uç noktalarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="b1eef-176">Because `SignInManager` and `UserManager` aren't supported in :::no-loc(Razor)::: components, use API endpoints in the :::no-loc(Blazor Server)::: app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a><span data-ttu-id="b1eef-177">Uygulama stilleriyle özel düzen kullanma :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="b1eef-177">Use a custom layout with :::no-loc(Blazor)::: app styles</span></span>

<span data-ttu-id="b1eef-178">:::no-loc(Identity):::Sayfa düzeni ve stilleri, varsayılan temayı kullanan sayfalar oluşturmak için değiştirilebilir :::no-loc(Blazor)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-178">The :::no-loc(Identity)::: pages layout and styles can be modified to produce pages that use the default :::no-loc(Blazor)::: theme.</span></span>

> [!NOTE]
> <span data-ttu-id="b1eef-179">Bu bölümdeki örnek yalnızca özelleştirme için bir başlangıç noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="b1eef-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="b1eef-180">En iyi kullanıcı deneyimi için büyük olasılıkla ek çalışma gerekir.</span><span class="sxs-lookup"><span data-stu-id="b1eef-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="b1eef-181">Yeni bir `NavMenu_:::no-loc(Identity):::Layout` bileşen oluşturun ( *paylaşılan/NavMenu_ :::no-loc(Identity)::: Layout. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="b1eef-181">Create a new `NavMenu_:::no-loc(Identity):::Layout` component ( *Shared/NavMenu_:::no-loc(Identity):::Layout.razor* ).</span></span> <span data-ttu-id="b1eef-182">Bileşenin biçimlendirmesi ve kodu için, uygulamanın bileşeniyle aynı içeriği kullanın `NavMenu` ( *paylaşılan/navmenu. Razor* ).</span><span class="sxs-lookup"><span data-stu-id="b1eef-182">For the markup and code of the component, use the same content of the app's `NavMenu` component ( *Shared/NavMenu.razor* ).</span></span> <span data-ttu-id="b1eef-183">`NavLink`Bileşendeki otomatik yeniden yönlendirmeler `RedirectToLogin` kimlik doğrulama veya yetkilendirme gerektiren bileşenler için başarısız olduğundan anonim olarak erişilemeyen bileşenlere her birini kaldırın.</span><span class="sxs-lookup"><span data-stu-id="b1eef-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="b1eef-184">*Sayfalar/paylaşılan/Layout. cshtml* dosyasında aşağıdaki değişiklikleri yapın:</span><span class="sxs-lookup"><span data-stu-id="b1eef-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="b1eef-185">:::no-loc(Razor):::Etiket yardımcıları ve uygulamanın bileşenlerini *paylaşılan* klasörde kullanmak için dosyanın en üstüne yönergeler ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b1eef-185">Add :::no-loc(Razor)::: directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="b1eef-186">`{APPLICATION ASSEMBLY}`Uygulamanın derleme adıyla değiştirin.</span><span class="sxs-lookup"><span data-stu-id="b1eef-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="b1eef-187">İçeriğe bir `<base>` etiket ve :::no-loc(Blazor)::: stil sayfası ekleyin `<link>` `<head>` :</span><span class="sxs-lookup"><span data-stu-id="b1eef-187">Add a `<base>` tag and :::no-loc(Blazor)::: stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="b1eef-188">`<body>`Etiketin içeriğini aşağıdaki şekilde değiştirin:</span><span class="sxs-lookup"><span data-stu-id="b1eef-188">Change the content of the `<body>` tag to the following:</span></span>

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_:::no-loc(Identity):::Layout)" 
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
              throw new InvalidOperationException("The default :::no-loc(Identity)::: UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/:::no-loc(Identity):::/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/:::no-loc(Identity):::/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/:::no-loc(Identity):::/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a><span data-ttu-id="b1eef-189">:::no-loc(Identity)::: :::no-loc(Blazor Server)::: Yetkilendirmeyle bir projeye yapı</span><span class="sxs-lookup"><span data-stu-id="b1eef-189">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="b1eef-190">Bazı :::no-loc(Identity)::: Seçenekler, *alanlarda/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* içinde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="b1eef-190">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="b1eef-191">Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b1eef-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="standalone-or-hosted-no-locblazor-webassembly-apps"></a><span data-ttu-id="b1eef-192">Tek başına veya barındırılan :::no-loc(Blazor WebAssembly)::: uygulamalar</span><span class="sxs-lookup"><span data-stu-id="b1eef-192">Standalone or hosted :::no-loc(Blazor WebAssembly)::: apps</span></span>

<span data-ttu-id="b1eef-193">İstemci tarafı :::no-loc(Blazor WebAssembly)::: uygulamalar kendi :::no-loc(Identity)::: Kullanıcı arabirimi yaklaşımlarını kullanır ve :::no-loc(ASP.NET Core Identity)::: Yapı iskelesi kullanamaz.</span><span class="sxs-lookup"><span data-stu-id="b1eef-193">Client-side :::no-loc(Blazor WebAssembly)::: apps use their own :::no-loc(Identity)::: UI approaches and can't use :::no-loc(ASP.NET Core Identity)::: scaffolding.</span></span> <span data-ttu-id="b1eef-194">Barındırılan çözümlerin sunucu tarafı ASP.NET Core Uygulamaları :::no-loc(Blazor)::: , :::no-loc(Razor)::: Bu makaledeki sayfalar/MVC kılavuzlarını izleyebilir ve ' nin desteklediği diğer ASP.NET Core uygulama türleri gibi yapılandırılır :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-194">Server-side ASP.NET Core apps of hosted :::no-loc(Blazor)::: solutions can follow the :::no-loc(Razor)::: Pages/MVC guidance in this article and are configured just like any other type of ASP.NET Core app that supports :::no-loc(Identity):::.</span></span>

<span data-ttu-id="b1eef-195">:::no-loc(Blazor):::Çerçeve, :::no-loc(Razor)::: UI sayfalarının bileşen sürümlerini içermez :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-195">The :::no-loc(Blazor)::: framework doesn't include :::no-loc(Razor)::: component versions of :::no-loc(Identity)::: UI pages.</span></span> <span data-ttu-id="b1eef-196">:::no-loc(Identity)::: UI :::no-loc(Razor)::: bileşenleri, desteklenmeyen üçüncü taraf kaynaklarından özel olarak oluşturulmuş veya alınmış olabilir.</span><span class="sxs-lookup"><span data-stu-id="b1eef-196">:::no-loc(Identity)::: UI :::no-loc(Razor)::: components can be custom built or obtained from unsupported third-party sources.</span></span>

<span data-ttu-id="b1eef-197">Daha fazla bilgi için bkz. [ :::no-loc(Blazor)::: güvenlik ve :::no-loc(Identity)::: makaleler](xref:blazor/security/index).</span><span class="sxs-lookup"><span data-stu-id="b1eef-197">For more information, see the [:::no-loc(Blazor)::: Security and :::no-loc(Identity)::: articles](xref:blazor/security/index).</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="b1eef-198">Tam :::no-loc(Identity)::: UI kaynağı oluştur</span><span class="sxs-lookup"><span data-stu-id="b1eef-198">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="b1eef-199">Kullanıcı arabiriminin tam denetimini sürdürmek için :::no-loc(Identity)::: , :::no-loc(Identity)::: desteği ' ı çalıştırın ve **tüm dosyaları geçersiz kıl** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="b1eef-199">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="b1eef-200">Aşağıdaki vurgulanan kod, varsayılan :::no-loc(Identity)::: Kullanıcı arabirimini :::no-loc(Identity)::: ASP.NET Core 2,1 Web uygulamasında değiştirecek değişiklikleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="b1eef-200">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="b1eef-201">Bunu, Kullanıcı arabirimine tam denetim sağlamak için yapmak isteyebilirsiniz :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-201">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="b1eef-202">Varsayılan değer :::no-loc(Identity)::: aşağıdaki kodda değiştirilmiştir:</span><span class="sxs-lookup"><span data-stu-id="b1eef-202">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="b1eef-203">Aşağıdaki kod, [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)ve [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath)öğesini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="b1eef-203">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="b1eef-204">Bir `IEmailSender` uygulamayı kaydedin, örneğin:</span><span class="sxs-lookup"><span data-stu-id="b1eef-204">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="b1eef-205">Parola Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="b1eef-205">Password configuration</span></span>

<span data-ttu-id="b1eef-206">' <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> De yapılandırılmışsa `Startup.ConfigureServices` , [ `[StringLength]` ](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` Yapı iskelesi sayfalarında özelliği için öznitelik yapılandırması gerekli olabilir :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-206">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="b1eef-207">`InputModel``Password`aşağıdaki dosyalarda özellikler bulunur:</span><span class="sxs-lookup"><span data-stu-id="b1eef-207">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a><span data-ttu-id="b1eef-208">Sayfayı devre dışı bırakma</span><span class="sxs-lookup"><span data-stu-id="b1eef-208">Disable a page</span></span>

<span data-ttu-id="b1eef-209">Bu bölümler, kaydetme sayfasının nasıl devre dışı bırakılacağını gösterir, ancak herhangi bir sayfayı devre dışı bırakmak için yaklaşım kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="b1eef-209">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="b1eef-210">Kullanıcı kaydını devre dışı bırakmak için:</span><span class="sxs-lookup"><span data-stu-id="b1eef-210">To disable user registration:</span></span>

* <span data-ttu-id="b1eef-211">Yapı iskelesi :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-211">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="b1eef-212">Account. Register, Account. Login ve account. RegisterConfirmation bilgilerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b1eef-212">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="b1eef-213">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b1eef-213">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="b1eef-214">Kullanıcıları bu uç noktadan kaydedememesi için *alanı/ :::no-loc(Identity)::: /Pages/Account/Register.cshtml.cs* güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="b1eef-214">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="b1eef-215">Önceki değişikliklerle tutarlı olması için *Areas/ :::no-loc(Identity)::: /Pages/Account/Register.exe* öğesini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="b1eef-215">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="b1eef-216">*/ :::no-loc(Identity)::: /Pages/Account/Login.exe* içindeki kayıt bağlantısını açıklama veya Kaldır</span><span class="sxs-lookup"><span data-stu-id="b1eef-216">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="b1eef-217">*Areas/ :::no-loc(Identity)::: /Pages/Account/registerconfirmation* sayfasını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="b1eef-217">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="b1eef-218">Cshtml dosyasındaki kodu ve bağlantıları kaldırın.</span><span class="sxs-lookup"><span data-stu-id="b1eef-218">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="b1eef-219">Onay kodunu şuradan kaldırın `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="b1eef-219">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="b1eef-220">Kullanıcıları eklemek için başka bir uygulama kullanma</span><span class="sxs-lookup"><span data-stu-id="b1eef-220">Use another app to add users</span></span>

<span data-ttu-id="b1eef-221">Web uygulaması dışındaki kullanıcıları eklemek için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="b1eef-221">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="b1eef-222">Kullanıcı ekleme seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="b1eef-222">Options to add users include:</span></span>

* <span data-ttu-id="b1eef-223">Adanmış bir yönetim Web uygulaması.</span><span class="sxs-lookup"><span data-stu-id="b1eef-223">A dedicated admin web app.</span></span>
* <span data-ttu-id="b1eef-224">Bir konsol uygulaması.</span><span class="sxs-lookup"><span data-stu-id="b1eef-224">A console app.</span></span>

<span data-ttu-id="b1eef-225">Aşağıdaki kod, kullanıcı eklemeye yönelik bir yaklaşımı özetler:</span><span class="sxs-lookup"><span data-stu-id="b1eef-225">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="b1eef-226">Kullanıcıların listesi belleği okur.</span><span class="sxs-lookup"><span data-stu-id="b1eef-226">A list of users is read into memory.</span></span>
* <span data-ttu-id="b1eef-227">Her Kullanıcı için güçlü bir benzersiz parola oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="b1eef-227">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="b1eef-228">Kullanıcı :::no-loc(Identity)::: veritabanına eklenir.</span><span class="sxs-lookup"><span data-stu-id="b1eef-228">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="b1eef-229">Kullanıcıya bildirilir ve parolayı değiştirmesi bildirilir.</span><span class="sxs-lookup"><span data-stu-id="b1eef-229">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="b1eef-230">Aşağıdaki kod, bir kullanıcı ekleme ana hatlarıyla verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="b1eef-230">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="b1eef-231">Üretim senaryolarında de benzer bir yaklaşım izlenebilir.</span><span class="sxs-lookup"><span data-stu-id="b1eef-231">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="b1eef-232">Statik varlıkların yayımlanmasını Engelle :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="b1eef-232">Prevent publish of static :::no-loc(Identity)::: assets</span></span>

<span data-ttu-id="b1eef-233">Statik :::no-loc(Identity)::: varlıkların Web köküne yayımlanmasını engellemek için bkz <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> ..</span><span class="sxs-lookup"><span data-stu-id="b1eef-233">To prevent publishing static :::no-loc(Identity)::: assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1eef-234">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b1eef-234">Additional resources</span></span>

* [<span data-ttu-id="b1eef-235">ASP.NET Core 2,1 ve üzeri kimlik doğrulama kodundaki değişiklikler</span><span class="sxs-lookup"><span data-stu-id="b1eef-235">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b1eef-236">ASP.NET Core 2,1 ve üzeri [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) bir [ :::no-loc(Razor)::: sınıf kitaplığı](xref:razor-pages/ui-class)olarak sunulmaktadır.</span><span class="sxs-lookup"><span data-stu-id="b1eef-236">ASP.NET Core 2.1 and later provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="b1eef-237">Dahil edilen uygulamalar, :::no-loc(Identity)::: :::no-loc(Identity)::: :::no-loc(Razor)::: sınıf kitaplığı 'nda (RCL) bulunan kaynak kodu seçmeli olarak eklemek için desteği uygulayabilir.</span><span class="sxs-lookup"><span data-stu-id="b1eef-237">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="b1eef-238">Kodu değiştirebilmeniz ve davranışı değiştirebilmek için kaynak kodu oluşturmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b1eef-238">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="b1eef-239">Örneğin, kayıt sırasında kullanılan kodu oluşturmak için desteği ' ı söyleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b1eef-239">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="b1eef-240">Oluşturulan kod RCL 'deki aynı koda göre önceliklidir :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-240">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="b1eef-241">Kullanıcı arabirimine tam denetim sağlamak ve varsayılan RCL 'yi kullanmak için, [tam KIMLIK UI kaynağı oluşturma](#full)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b1eef-241">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="b1eef-242">Kimlik **doğrulaması içermeyen uygulamalar** , RCL paketini eklemek için desteği uygulayabilir :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-242">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="b1eef-243">Oluşturulacak kodu seçme seçeneğiniz vardır :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-243">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="b1eef-244">Desteği gerekli kodların çoğunu üretse de, işlemi gerçekleştirmek için projenizi güncelleştirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="b1eef-244">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="b1eef-245">Bu belgede, bir yapı iskelesi güncelleştirmesini tamamlaması için gereken adımlar açıklanmaktadır :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-245">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="b1eef-246">:::no-loc(Identity):::Desteği çalıştırıldığında, proje dizininde bir *ScaffoldingReadme.txt* dosyası oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="b1eef-246">When the :::no-loc(Identity)::: scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="b1eef-247">*ScaffoldingReadme.txt* dosyası, yapı iskelesi güncelleştirmesinin tamamlanabilmesi için gerekli olan genel yönergeleri içerir :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-247">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the :::no-loc(Identity)::: scaffolding update.</span></span> <span data-ttu-id="b1eef-248">Bu belge, *ScaffoldingReadme.txt* dosyasından daha ayrıntılı yönergeler içerir.</span><span class="sxs-lookup"><span data-stu-id="b1eef-248">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="b1eef-249">Dosya farklılıklarını gösteren ve değişikliklerden geri dönüş yapmanızı sağlayan bir kaynak denetimi sistemi kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="b1eef-249">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="b1eef-250">Scaffolder çalıştırıldıktan sonra değişiklikleri inceleyin :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-250">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="b1eef-251">[Iki öğeli kimlik doğrulaması](xref:security/authentication/identity-enable-qrcodes), [Hesap onaylama ve parola kurtarma](xref:security/authentication/accconfirm)ile diğer güvenlik özellikleri kullanılırken hizmetler gereklidir :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-251">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="b1eef-252">Hizmetler veya hizmet saplamaları, yapı iskelesi sırasında oluşturulmaz :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-252">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="b1eef-253">Bu özelliklerin etkinleştirilmesi için hizmetlerin el ile eklenmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="b1eef-253">Services to enable these features must be added manually.</span></span> <span data-ttu-id="b1eef-254">Örneğin, bkz. [e-posta onayı gerektir](xref:security/authentication/accconfirm#require-email-confirmation).</span><span class="sxs-lookup"><span data-stu-id="b1eef-254">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="b1eef-255">:::no-loc(Identity):::Boş bir projeye yapı iskelesi</span><span class="sxs-lookup"><span data-stu-id="b1eef-255">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b1eef-256">Aşağıdaki Vurgulanan çağrıları `Startup` sınıfına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b1eef-256">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="b1eef-257">:::no-loc(Identity)::: :::no-loc(Razor)::: Var olan yetkilendirme olmadan bir projeye yapı iskelesi yapın</span><span class="sxs-lookup"><span data-stu-id="b1eef-257">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b1eef-258">:::no-loc(Identity):::, *alanlarında/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* ' de yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="b1eef-258">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="b1eef-259">Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b1eef-259">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="b1eef-260">Geçişler, UseAuthentication ve düzen</span><span class="sxs-lookup"><span data-stu-id="b1eef-260">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="b1eef-261">Kimlik doğrulamayı etkinleştir</span><span class="sxs-lookup"><span data-stu-id="b1eef-261">Enable authentication</span></span>

<span data-ttu-id="b1eef-262">`Configure` `Startup` Sınıfının yönteminde, <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> öğesinden sonra çağırın `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="b1eef-262">In the `Configure` method of the `Startup` class, call <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="b1eef-263">Düzen değişiklikleri</span><span class="sxs-lookup"><span data-stu-id="b1eef-263">Layout changes</span></span>

<span data-ttu-id="b1eef-264">İsteğe bağlı: () oturum açma bilgilerini `_LoginPartial` Düzen dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b1eef-264">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="b1eef-265">:::no-loc(Identity)::: :::no-loc(Razor)::: Yetkilendirmeyle bir projeye yapı</span><span class="sxs-lookup"><span data-stu-id="b1eef-265">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

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

<span data-ttu-id="b1eef-266">Bazı :::no-loc(Identity)::: Seçenekler, *alanlarda/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* içinde yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="b1eef-266">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="b1eef-267">Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).</span><span class="sxs-lookup"><span data-stu-id="b1eef-267">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="b1eef-268">:::no-loc(Identity):::Var olan yetkilendirme olmadan BIR MVC projesinde yapı iskelesi yapın</span><span class="sxs-lookup"><span data-stu-id="b1eef-268">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="b1eef-269">İsteğe bağlı: bir Login ( `_LoginPartial` ) öğesini *views/Shared/_Layout. cshtml* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b1eef-269">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="b1eef-270">*Pages/Shared/_LoginPartial. cshtml* dosyasını *views/Shared/_LoginPartial. cshtml* olarak taşıyın</span><span class="sxs-lookup"><span data-stu-id="b1eef-270">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="b1eef-271">:::no-loc(Identity):::, *alanlarında/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* ' de yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="b1eef-271">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="b1eef-272">Daha fazla bilgi için bkz. ıhostingstartup.</span><span class="sxs-lookup"><span data-stu-id="b1eef-272">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="b1eef-273"><xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>Sonra çağır `UseStaticFiles` :</span><span class="sxs-lookup"><span data-stu-id="b1eef-273">Call <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="b1eef-274">:::no-loc(Identity):::Yetkilendirme ile BIR MVC projesinde yapı iskelesi oluşturma</span><span class="sxs-lookup"><span data-stu-id="b1eef-274">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="b1eef-275">*Sayfaları/paylaşılan* klasörünü ve bu klasördeki dosyaları silin.</span><span class="sxs-lookup"><span data-stu-id="b1eef-275">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="b1eef-276">Tam :::no-loc(Identity)::: UI kaynağı oluştur</span><span class="sxs-lookup"><span data-stu-id="b1eef-276">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="b1eef-277">Kullanıcı arabiriminin tam denetimini sürdürmek için :::no-loc(Identity)::: , :::no-loc(Identity)::: desteği ' ı çalıştırın ve **tüm dosyaları geçersiz kıl** ' ı seçin.</span><span class="sxs-lookup"><span data-stu-id="b1eef-277">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="b1eef-278">Aşağıdaki vurgulanan kod, varsayılan :::no-loc(Identity)::: Kullanıcı arabirimini :::no-loc(Identity)::: ASP.NET Core 2,1 Web uygulamasında değiştirecek değişiklikleri gösterir.</span><span class="sxs-lookup"><span data-stu-id="b1eef-278">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="b1eef-279">Bunu, Kullanıcı arabirimine tam denetim sağlamak için yapmak isteyebilirsiniz :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-279">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="b1eef-280">Varsayılan değer :::no-loc(Identity)::: aşağıdaki kodda değiştirilmiştir:</span><span class="sxs-lookup"><span data-stu-id="b1eef-280">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="b1eef-281">Aşağıdaki kod, [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)ve [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath)öğesini ayarlar:</span><span class="sxs-lookup"><span data-stu-id="b1eef-281">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="b1eef-282">Bir `IEmailSender` uygulamayı kaydedin, örneğin:</span><span class="sxs-lookup"><span data-stu-id="b1eef-282">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="b1eef-283">Parola Yapılandırması</span><span class="sxs-lookup"><span data-stu-id="b1eef-283">Password configuration</span></span>

<span data-ttu-id="b1eef-284">' <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> De yapılandırılmışsa `Startup.ConfigureServices` , [ `[StringLength]` ](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` Yapı iskelesi sayfalarında özelliği için öznitelik yapılandırması gerekli olabilir :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-284">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="b1eef-285">`InputModel``Password`aşağıdaki dosyalarda özellikler bulunur:</span><span class="sxs-lookup"><span data-stu-id="b1eef-285">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a><span data-ttu-id="b1eef-286">Kayıt sayfasını devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="b1eef-286">Disable register page</span></span>

<span data-ttu-id="b1eef-287">Kullanıcı kaydını devre dışı bırakmak için:</span><span class="sxs-lookup"><span data-stu-id="b1eef-287">To disable user registration:</span></span>

* <span data-ttu-id="b1eef-288">Yapı iskelesi :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="b1eef-288">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="b1eef-289">Account. Register, Account. Login ve account. RegisterConfirmation bilgilerini ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b1eef-289">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="b1eef-290">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="b1eef-290">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="b1eef-291">Kullanıcıları bu uç noktadan kaydedememesi için *alanı/ :::no-loc(Identity)::: /Pages/Account/Register.cshtml.cs* güncelleştir:</span><span class="sxs-lookup"><span data-stu-id="b1eef-291">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="b1eef-292">Önceki değişikliklerle tutarlı olması için *Areas/ :::no-loc(Identity)::: /Pages/Account/Register.exe* öğesini güncelleştirin:</span><span class="sxs-lookup"><span data-stu-id="b1eef-292">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="b1eef-293">*/ :::no-loc(Identity)::: /Pages/Account/Login.exe* içindeki kayıt bağlantısını açıklama veya Kaldır</span><span class="sxs-lookup"><span data-stu-id="b1eef-293">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="b1eef-294">*Areas/ :::no-loc(Identity)::: /Pages/Account/registerconfirmation* sayfasını güncelleştirin.</span><span class="sxs-lookup"><span data-stu-id="b1eef-294">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="b1eef-295">Cshtml dosyasındaki kodu ve bağlantıları kaldırın.</span><span class="sxs-lookup"><span data-stu-id="b1eef-295">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="b1eef-296">Onay kodunu şuradan kaldırın `PageModel` :</span><span class="sxs-lookup"><span data-stu-id="b1eef-296">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="b1eef-297">Kullanıcıları eklemek için başka bir uygulama kullanma</span><span class="sxs-lookup"><span data-stu-id="b1eef-297">Use another app to add users</span></span>

<span data-ttu-id="b1eef-298">Web uygulaması dışındaki kullanıcıları eklemek için bir mekanizma sağlar.</span><span class="sxs-lookup"><span data-stu-id="b1eef-298">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="b1eef-299">Kullanıcı ekleme seçenekleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="b1eef-299">Options to add users include:</span></span>

* <span data-ttu-id="b1eef-300">Adanmış bir yönetim Web uygulaması.</span><span class="sxs-lookup"><span data-stu-id="b1eef-300">A dedicated admin web app.</span></span>
* <span data-ttu-id="b1eef-301">Bir konsol uygulaması.</span><span class="sxs-lookup"><span data-stu-id="b1eef-301">A console app.</span></span>

<span data-ttu-id="b1eef-302">Aşağıdaki kod, kullanıcı eklemeye yönelik bir yaklaşımı özetler:</span><span class="sxs-lookup"><span data-stu-id="b1eef-302">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="b1eef-303">Kullanıcıların listesi belleği okur.</span><span class="sxs-lookup"><span data-stu-id="b1eef-303">A list of users is read into memory.</span></span>
* <span data-ttu-id="b1eef-304">Her Kullanıcı için güçlü bir benzersiz parola oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="b1eef-304">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="b1eef-305">Kullanıcı :::no-loc(Identity)::: veritabanına eklenir.</span><span class="sxs-lookup"><span data-stu-id="b1eef-305">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="b1eef-306">Kullanıcıya bildirilir ve parolayı değiştirmesi bildirilir.</span><span class="sxs-lookup"><span data-stu-id="b1eef-306">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="b1eef-307">Aşağıdaki kod, bir kullanıcı ekleme ana hatlarıyla verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="b1eef-307">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="b1eef-308">Üretim senaryolarında de benzer bir yaklaşım izlenebilir.</span><span class="sxs-lookup"><span data-stu-id="b1eef-308">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1eef-309">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b1eef-309">Additional resources</span></span>

* [<span data-ttu-id="b1eef-310">ASP.NET Core 2,1 ve üzeri kimlik doğrulama kodundaki değişiklikler</span><span class="sxs-lookup"><span data-stu-id="b1eef-310">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
