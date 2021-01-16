---
title: Kimlik doğrulamasını cookie ASP.NET uygulamalar arasında paylaşma
author: rick-anderson
description: cookieASP.NET 4. x ve ASP.NET Core uygulamaları arasında kimlik doğrulamanın nasıl paylaşılacağını öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
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
uid: security/cookie-sharing
ms.openlocfilehash: 0d43bbbc44015aff040b12dfacb260fe50492e54
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253000"
---
# <a name="share-authentication-no-loccookies-among-aspnet-apps"></a><span data-ttu-id="cc59e-103">Kimlik doğrulamasını cookie ASP.NET uygulamalar arasında paylaşma</span><span class="sxs-lookup"><span data-stu-id="cc59e-103">Share authentication cookies among ASP.NET apps</span></span>

<span data-ttu-id="cc59e-104">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cc59e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cc59e-105">Web siteleri genellikle birlikte çalışan ayrı Web uygulamalarından oluşur.</span><span class="sxs-lookup"><span data-stu-id="cc59e-105">Websites often consist of individual web apps working together.</span></span> <span data-ttu-id="cc59e-106">Çoklu oturum açma (SSO) deneyimi sağlamak için, bir site içindeki Web uygulamaları kimlik doğrulama s 'yi paylaşmalıdır cookie .</span><span class="sxs-lookup"><span data-stu-id="cc59e-106">To provide a single sign-on (SSO) experience, web apps within a site must share authentication cookies.</span></span> <span data-ttu-id="cc59e-107">Bu senaryoyu desteklemek için, veri koruma yığını, Katana cookie kimlik doğrulamanın ve ASP.NET Core cookie kimlik doğrulama anahtarlarının paylaşılmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="cc59e-107">To support this scenario, the data protection stack allows sharing Katana cookie authentication and ASP.NET Core cookie authentication tickets.</span></span>

<span data-ttu-id="cc59e-108">Aşağıdaki örneklerde:</span><span class="sxs-lookup"><span data-stu-id="cc59e-108">In the examples that follow:</span></span>

* <span data-ttu-id="cc59e-109">Kimlik doğrulama cookie adı, ortak bir değerine ayarlanır `.AspNet.SharedCookie` .</span><span class="sxs-lookup"><span data-stu-id="cc59e-109">The authentication cookie name is set to a common value of `.AspNet.SharedCookie`.</span></span>
* <span data-ttu-id="cc59e-110">, `AuthenticationType` `Identity.Application` Açıkça ya da varsayılan olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="cc59e-110">The `AuthenticationType` is set to `Identity.Application` either explicitly or by default.</span></span>
* <span data-ttu-id="cc59e-111">Veri koruma sisteminin veri koruma anahtarlarını () paylaşmasını sağlamak için ortak bir uygulama adı kullanılır `SharedCookieApp` .</span><span class="sxs-lookup"><span data-stu-id="cc59e-111">A common app name is used to enable the data protection system to share data protection keys (`SharedCookieApp`).</span></span>
* <span data-ttu-id="cc59e-112">`Identity.Application` , kimlik doğrulama düzeni olarak kullanılır.</span><span class="sxs-lookup"><span data-stu-id="cc59e-112">`Identity.Application` is used as the authentication scheme.</span></span> <span data-ttu-id="cc59e-113">Hangi düzenin kullanıldığı, her ne kadar paylaşılan uygulamalar için  cookie varsayılan düzen olarak, ya da açıkça ayarlanarak kullanılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="cc59e-113">Whatever scheme is used, it must be used consistently *within and across* the shared cookie apps either as the default scheme or by explicitly setting it.</span></span> <span data-ttu-id="cc59e-114">Şema, şifreleme ve şifre çözme sırasında kullanılır cookie , bu nedenle uygulamalarda tutarlı bir düzenin kullanılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="cc59e-114">The scheme is used when encrypting and decrypting cookies, so a consistent scheme must be used across apps.</span></span>
* <span data-ttu-id="cc59e-115">Ortak bir [veri koruma anahtarı](xref:security/data-protection/implementation/key-management) depolama konumu kullanılır.</span><span class="sxs-lookup"><span data-stu-id="cc59e-115">A common [data protection key](xref:security/data-protection/implementation/key-management) storage location is used.</span></span>
  * <span data-ttu-id="cc59e-116">ASP.NET Core uygulamalarda, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> anahtar depolama konumunu ayarlamak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="cc59e-116">In ASP.NET Core apps, <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> is used to set the key storage location.</span></span>
  * <span data-ttu-id="cc59e-117">.NET Framework uygulamalarda, Cookie kimlik doğrulama ara yazılımı bir uygulamasını kullanır <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider> .</span><span class="sxs-lookup"><span data-stu-id="cc59e-117">In .NET Framework apps, Cookie Authentication Middleware uses an implementation of <xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider>.</span></span> <span data-ttu-id="cc59e-118">`DataProtectionProvider` , kimlik doğrulaması yük verilerinin şifrelenmesi ve şifresinin çözülmesi için veri koruma hizmetleri sağlar cookie .</span><span class="sxs-lookup"><span data-stu-id="cc59e-118">`DataProtectionProvider` provides data protection services for the encryption and decryption of authentication cookie payload data.</span></span> <span data-ttu-id="cc59e-119">`DataProtectionProvider`Örnek, uygulamanın diğer bölümleri tarafından kullanılan veri koruma sisteminden yalıtılmıştır.</span><span class="sxs-lookup"><span data-stu-id="cc59e-119">The `DataProtectionProvider` instance is isolated from the data protection system used by other parts of the app.</span></span> <span data-ttu-id="cc59e-120">[Dataprotectionprovider. Create (System. IO. DirectoryInfo, Action \<IDataProtectionBuilder> ),](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) <xref:System.IO.DirectoryInfo> veri koruma anahtar depolaması konumunu belirtmek için bir değerini kabul eder.</span><span class="sxs-lookup"><span data-stu-id="cc59e-120">[DataProtectionProvider.Create(System.IO.DirectoryInfo, Action\<IDataProtectionBuilder>)](xref:Microsoft.AspNetCore.DataProtection.DataProtectionProvider.Create*) accepts a <xref:System.IO.DirectoryInfo> to specify the location for data protection key storage.</span></span>
* <span data-ttu-id="cc59e-121">`DataProtectionProvider`[Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet paketini gerektirir:</span><span class="sxs-lookup"><span data-stu-id="cc59e-121">`DataProtectionProvider` requires the [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/) NuGet package:</span></span>
  * <span data-ttu-id="cc59e-122">ASP.NET Core 2. x uygulamalarında [Microsoft. AspNetCore. app metapackage](xref:fundamentals/metapackage-app)öğesine başvurun.</span><span class="sxs-lookup"><span data-stu-id="cc59e-122">In ASP.NET Core 2.x apps, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>
  * <span data-ttu-id="cc59e-123">.NET Framework uygulamalar ' da [Microsoft. AspNetCore. DataProtection. Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/)'a bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="cc59e-123">In .NET Framework apps, add a package reference to [Microsoft.AspNetCore.DataProtection.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.DataProtection.Extensions/).</span></span>
* <span data-ttu-id="cc59e-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> ortak uygulama adını ayarlar.</span><span class="sxs-lookup"><span data-stu-id="cc59e-124"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> sets the common app name.</span></span>

## <a name="share-authentication-no-loccookies-with-no-locaspnet-core-identity"></a><span data-ttu-id="cc59e-125">Kimlik doğrulaması cookie ile paylaşma ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="cc59e-125">Share authentication cookies with ASP.NET Core Identity</span></span>

<span data-ttu-id="cc59e-126">Kullanırken ASP.NET Core Identity :</span><span class="sxs-lookup"><span data-stu-id="cc59e-126">When using ASP.NET Core Identity:</span></span>

* <span data-ttu-id="cc59e-127">Veri koruma anahtarlarının ve uygulama adının uygulamalar arasında paylaşılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="cc59e-127">Data protection keys and the app name must be shared among apps.</span></span> <span data-ttu-id="cc59e-128">Aşağıdaki örneklerde yöntemine ortak bir anahtar depolama konumu verilmiştir <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> .</span><span class="sxs-lookup"><span data-stu-id="cc59e-128">A common key storage location is provided to the <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.PersistKeysToFileSystem*> method in the following examples.</span></span> <span data-ttu-id="cc59e-129"><xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*>Ortak bir paylaşılan uygulama adı yapılandırmak için kullanın ( `SharedCookieApp` Aşağıdaki örneklerde).</span><span class="sxs-lookup"><span data-stu-id="cc59e-129">Use <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.SetApplicationName*> to configure a common shared app name (`SharedCookieApp` in the following examples).</span></span> <span data-ttu-id="cc59e-130">Daha fazla bilgi için bkz. <xref:security/data-protection/configuration/overview>.</span><span class="sxs-lookup"><span data-stu-id="cc59e-130">For more information, see <xref:security/data-protection/configuration/overview>.</span></span>
* <span data-ttu-id="cc59e-131"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*>İçin veri koruma hizmetini ayarlamak üzere genişletme yöntemini kullanın cookie .</span><span class="sxs-lookup"><span data-stu-id="cc59e-131">Use the <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.ConfigureApplicationCookie*> extension method to set up the data protection service for cookies.</span></span>
* <span data-ttu-id="cc59e-132">Varsayılan kimlik doğrulama türü `Identity.Application` .</span><span class="sxs-lookup"><span data-stu-id="cc59e-132">The default authentication type is `Identity.Application`.</span></span>

<span data-ttu-id="cc59e-133">`Startup.ConfigureServices` içinde:</span><span class="sxs-lookup"><span data-stu-id="cc59e-133">In `Startup.ConfigureServices`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
});
```

<span data-ttu-id="cc59e-134">**Note:** Yukarıdaki yönergeler () ile birlikte `ITicketStore` çalışmaz `CookieAuthenticationOptions.SessionStore` .</span><span class="sxs-lookup"><span data-stu-id="cc59e-134">**Note:** The preceding instructions don't work with `ITicketStore` (`CookieAuthenticationOptions.SessionStore`).</span></span>  <span data-ttu-id="cc59e-135">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/21163)bakın.</span><span class="sxs-lookup"><span data-stu-id="cc59e-135">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/21163).</span></span>

## <a name="share-authentication-no-loccookies-without-no-locaspnet-core-identity"></a><span data-ttu-id="cc59e-136">Kimlik doğrulama cookie 'leri paylaşma ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="cc59e-136">Share authentication cookies without ASP.NET Core Identity</span></span>

<span data-ttu-id="cc59e-137">İle cookie doğrudan kullanıldığında ASP.NET Core Identity , ' de veri korumayı ve kimlik doğrulamasını yapılandırın `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="cc59e-137">When using cookies directly without ASP.NET Core Identity, configure data protection and authentication in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cc59e-138">Aşağıdaki örnekte, kimlik doğrulama türü olarak ayarlanır `Identity.Application` :</span><span class="sxs-lookup"><span data-stu-id="cc59e-138">In the following example, the authentication type is set to `Identity.Application`:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.AddAuthentication("Identity.Application")
    .AddCookie("Identity.Application", options =>
    {
        options.Cookie.Name = ".AspNet.SharedCookie";
    });
```

## <a name="share-no-loccookies-across-different-base-paths"></a><span data-ttu-id="cc59e-139">cookieFarklı temel yollar genelinde paylaşma</span><span class="sxs-lookup"><span data-stu-id="cc59e-139">Share cookies across different base paths</span></span>

<span data-ttu-id="cc59e-140">Bir kimlik doğrulaması cookie , varsayılan olarak [HttpRequest. pathbase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) 'i kullanır [ Cookie . Yol](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span><span class="sxs-lookup"><span data-stu-id="cc59e-140">An authentication cookie uses the [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) as its default [Cookie.Path](xref:Microsoft.AspNetCore.Http.CookieBuilder.Path).</span></span> <span data-ttu-id="cc59e-141">Uygulamanın cookie farklı temel yollar arasında paylaşılması gerekiyorsa, `Path` geçersiz kılınmalıdır:</span><span class="sxs-lookup"><span data-stu-id="cc59e-141">If the app's cookie must be shared across different base paths, `Path` must be overridden:</span></span>

```csharp
services.AddDataProtection()
    .PersistKeysToFileSystem("{PATH TO COMMON KEY RING FOLDER}")
    .SetApplicationName("SharedCookieApp");

services.ConfigureApplicationCookie(options => {
    options.Cookie.Name = ".AspNet.SharedCookie";
    options.Cookie.Path = "/";
});
```

## <a name="share-no-loccookies-across-subdomains"></a><span data-ttu-id="cc59e-142">Alt cookie etki alanları genelinde paylaşma</span><span class="sxs-lookup"><span data-stu-id="cc59e-142">Share cookies across subdomains</span></span>

<span data-ttu-id="cc59e-143">Alt etki alanları arasında paylaşan uygulamalar barındırırken, cookie içinde ortak bir etki alanı belirtin [ Cookie . Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) özelliği.</span><span class="sxs-lookup"><span data-stu-id="cc59e-143">When hosting apps that share cookies across subdomains, specify a common domain in the [Cookie.Domain](xref:Microsoft.AspNetCore.Http.CookieBuilder.Domain) property.</span></span> <span data-ttu-id="cc59e-144">Ve gibi cookie uygulamalarında uygulamalar arasında paylaşmak için şunu `contoso.com` `first_subdomain.contoso.com` `second_subdomain.contoso.com` belirtin `Cookie.Domain` `.contoso.com` :</span><span class="sxs-lookup"><span data-stu-id="cc59e-144">To share cookies across apps at `contoso.com`, such as `first_subdomain.contoso.com` and `second_subdomain.contoso.com`, specify the `Cookie.Domain` as `.contoso.com`:</span></span>

```csharp
options.Cookie.Domain = ".contoso.com";
```

## <a name="encrypt-data-protection-keys-at-rest"></a><span data-ttu-id="cc59e-145">Bekleyen veri koruma anahtarlarını şifreleyin</span><span class="sxs-lookup"><span data-stu-id="cc59e-145">Encrypt data protection keys at rest</span></span>

<span data-ttu-id="cc59e-146">Üretim dağıtımları için, ' yi, `DataProtectionProvider` geri kalan anahtarları DPAPI veya X509Certificate ile şifrelemek üzere yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="cc59e-146">For production deployments, configure the `DataProtectionProvider` to encrypt keys at rest with DPAPI or an X509Certificate.</span></span> <span data-ttu-id="cc59e-147">Daha fazla bilgi için bkz. <xref:security/data-protection/implementation/key-encryption-at-rest>.</span><span class="sxs-lookup"><span data-stu-id="cc59e-147">For more information, see <xref:security/data-protection/implementation/key-encryption-at-rest>.</span></span> <span data-ttu-id="cc59e-148">Aşağıdaki örnekte, için bir sertifika parmak izi sağlanır <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*> :</span><span class="sxs-lookup"><span data-stu-id="cc59e-148">In the following example, a certificate thumbprint is provided to <xref:Microsoft.AspNetCore.DataProtection.DataProtectionBuilderExtensions.ProtectKeysWithCertificate*>:</span></span>

```csharp
services.AddDataProtection()
    .ProtectKeysWithCertificate("{CERTIFICATE THUMBPRINT}");
```

## <a name="share-authentication-no-loccookies-between-aspnet-4x-and-aspnet-core-apps"></a><span data-ttu-id="cc59e-149">cookieASP.NET 4. x ve ASP.NET Core uygulamaları arasında kimlik doğrulamasını paylaşma</span><span class="sxs-lookup"><span data-stu-id="cc59e-149">Share authentication cookies between ASP.NET 4.x and ASP.NET Core apps</span></span>

<span data-ttu-id="cc59e-150">Katana kimlik doğrulama ara yazılımı kullanan ASP.NET 4. x uygulamaları Cookie cookie , ASP.NET Core Cookie kimlik doğrulama ara yazılımı ile uyumlu olan kimlik doğrulama 'ları oluşturmak için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="cc59e-150">ASP.NET 4.x apps that use Katana Cookie Authentication Middleware can be configured to generate authentication cookies that are compatible with the ASP.NET Core Cookie Authentication Middleware.</span></span> <span data-ttu-id="cc59e-151">Bu, site genelinde sorunsuz bir SSO deneyimi sağlarken, büyük bir sitenin ayrı uygulamalarının çeşitli adımlarda yükseltilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="cc59e-151">This allows upgrading a large site's individual apps in several steps while providing a smooth SSO experience across the site.</span></span>

<span data-ttu-id="cc59e-152">Bir uygulama Katana Cookie kimlik doğrulama ara yazılımı kullandığında, `UseCookieAuthentication` projenin *Startup.auth.cs* dosyasında çağırır.</span><span class="sxs-lookup"><span data-stu-id="cc59e-152">When an app uses Katana Cookie Authentication Middleware, it calls `UseCookieAuthentication` in the project's *Startup.Auth.cs* file.</span></span> <span data-ttu-id="cc59e-153">Visual Studio 2013 ve sonraki sürümlerde oluşturulan ASP.NET 4. x Web uygulaması projeleri varsayılan olarak Katana Cookie kimlik doğrulama ara yazılımını kullanır.</span><span class="sxs-lookup"><span data-stu-id="cc59e-153">ASP.NET 4.x web app projects created with Visual Studio 2013 and later use the Katana Cookie Authentication Middleware by default.</span></span> <span data-ttu-id="cc59e-154">`UseCookieAuthentication`ASP.NET Core uygulamalar için artık kullanılmıyor ve desteklenmese de, `UseCookieAuthentication` Katana kimlik doğrulama ara yazılımı kullanan bir ASP.NET 4. x uygulamasında çağırma Cookie geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="cc59e-154">Although `UseCookieAuthentication` is obsolete and unsupported for ASP.NET Core apps, calling `UseCookieAuthentication` in an ASP.NET 4.x app that uses Katana Cookie Authentication Middleware is valid.</span></span>

<span data-ttu-id="cc59e-155">ASP.NET 4. x uygulaması .NET Framework 4.5.1 veya üstünü hedeflemelidir.</span><span class="sxs-lookup"><span data-stu-id="cc59e-155">An ASP.NET 4.x app must target .NET Framework 4.5.1 or later.</span></span> <span data-ttu-id="cc59e-156">Aksi takdirde, gerekli NuGet paketleri yüklenemeyebilir.</span><span class="sxs-lookup"><span data-stu-id="cc59e-156">Otherwise, the necessary NuGet packages fail to install.</span></span>

<span data-ttu-id="cc59e-157">cookieBir ASP.NET 4. x uygulaması ve bir ASP.NET Core uygulaması arasında kimlik doğrulamasını paylaşmak için, ASP.NET Core uygulamayı [ cookie ASP.NET Core uygulamalar arasında kimlik doğrulaması](#share-authentication-cookies-with-aspnet-core-identity) oluşturma bölümünde belirtilen şekilde yapılandırın ve ardından ASP.NET 4. x uygulamasını aşağıdaki şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="cc59e-157">To share authentication cookies between an ASP.NET 4.x app and an ASP.NET Core app, configure the ASP.NET Core app as stated in the [Share authentication cookies among ASP.NET Core apps](#share-authentication-cookies-with-aspnet-core-identity) section, then configure the ASP.NET 4.x app as follows.</span></span>

<span data-ttu-id="cc59e-158">Uygulamanın paketlerinin en son sürümlere güncelleştirildiğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="cc59e-158">Confirm that the app's packages are updated to the latest releases.</span></span> <span data-ttu-id="cc59e-159">[Microsoft. Owin. Security. Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) paketini her bir ASP.NET 4. x uygulamasına yükler.</span><span class="sxs-lookup"><span data-stu-id="cc59e-159">Install the [Microsoft.Owin.Security.Interop](https://www.nuget.org/packages/Microsoft.Owin.Security.Interop/) package into each ASP.NET 4.x app.</span></span>

<span data-ttu-id="cc59e-160">Çağrısını bulun ve değiştirin `UseCookieAuthentication` :</span><span class="sxs-lookup"><span data-stu-id="cc59e-160">Locate and modify the call to `UseCookieAuthentication`:</span></span>

* <span data-ttu-id="cc59e-161">cookieAdı ASP.NET Core Cookie kimlik doğrulama ara yazılımı (örnekteki) tarafından kullanılan adla eşleşecek şekilde değiştirin `.AspNet.SharedCookie` .</span><span class="sxs-lookup"><span data-stu-id="cc59e-161">Change the cookie name to match the name used by the ASP.NET Core Cookie Authentication Middleware (`.AspNet.SharedCookie` in the example).</span></span>
* <span data-ttu-id="cc59e-162">Aşağıdaki örnekte, kimlik doğrulama türü olarak ayarlanır `Identity.Application` .</span><span class="sxs-lookup"><span data-stu-id="cc59e-162">In the following example, the authentication type is set to `Identity.Application`.</span></span>
* <span data-ttu-id="cc59e-163">Başlatılmış bir örneğini `DataProtectionProvider` ortak veri koruma anahtarı depolama konumu olarak belirtin.</span><span class="sxs-lookup"><span data-stu-id="cc59e-163">Provide an instance of a `DataProtectionProvider` initialized to the common data protection key storage location.</span></span>
* <span data-ttu-id="cc59e-164">Uygulama adının, kimlik doğrulama s 'yi paylaşan tüm uygulamalar tarafından kullanılan ortak uygulama adına ayarlandığını onaylayın cookie ( `SharedCookieApp` örnekte).</span><span class="sxs-lookup"><span data-stu-id="cc59e-164">Confirm that the app name is set to the common app name used by all apps that share authentication cookies (`SharedCookieApp` in the example).</span></span>

<span data-ttu-id="cc59e-165">Ve ayarlamadıysanız `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider` , <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> benzersiz kullanıcıları ayırt eden bir talep olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="cc59e-165">If not setting `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` and `http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`, set <xref:System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier> to a claim that distinguishes unique users.</span></span>

<span data-ttu-id="cc59e-166">*App_Start/Startup.auth.cs*:</span><span class="sxs-lookup"><span data-stu-id="cc59e-166">*App_Start/Startup.Auth.cs*:</span></span>

```csharp
app.UseCookieAuthentication(new CookieAuthenticationOptions
{
    AuthenticationType = "Identity.Application",
    CookieName = ".AspNet.SharedCookie",
    LoginPath = new PathString("/Account/Login"),
    Provider = new CookieAuthenticationProvider
    {
        OnValidateIdentity =
            SecurityStampValidator
                .OnValidateIdentity<ApplicationUserManager, ApplicationUser>(
                    validateInterval: TimeSpan.FromMinutes(30),
                    regenerateIdentity: (manager, user) =>
                        user.GenerateUserIdentityAsync(manager))
    },
    TicketDataFormat = new AspNetTicketDataFormat(
        new DataProtectorShim(
            DataProtectionProvider.Create("{PATH TO COMMON KEY RING FOLDER}",
                (builder) => { builder.SetApplicationName("SharedCookieApp"); })
            .CreateProtector(
                "Microsoft.AspNetCore.Authentication.Cookies." +
                    "CookieAuthenticationMiddleware",
                "Identity.Application",
                "v2"))),
    CookieManager = new ChunkingCookieManager()
});

System.Web.Helpers.AntiForgeryConfig.UniqueClaimTypeIdentifier =
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name";
```

<span data-ttu-id="cc59e-167">Bir kullanıcı kimliği oluştururken, kimlik doğrulama türü (), içinde `Identity.Application` tanımlı `AuthenticationType` `UseCookieAuthentication` *App_Start/Startup.auth.cs* içinde tanımlanan türle eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="cc59e-167">When generating a user identity, the authentication type (`Identity.Application`) must match the type defined in `AuthenticationType` set with `UseCookieAuthentication` in *App_Start/Startup.Auth.cs*.</span></span>

<span data-ttu-id="cc59e-168">*Modeller/ Identity Models.cs*:</span><span class="sxs-lookup"><span data-stu-id="cc59e-168">*Models/IdentityModels.cs*:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public async Task<ClaimsIdentity> GenerateUserIdentityAsync(
        UserManager<ApplicationUser> manager)
    {
        // The authenticationType must match the one defined in 
        // CookieAuthenticationOptions.AuthenticationType
        var userIdentity = 
            await manager.CreateIdentityAsync(this, "Identity.Application");

        // Add custom user claims here

        return userIdentity;
    }
}
```

## <a name="use-a-common-user-database"></a><span data-ttu-id="cc59e-169">Ortak bir kullanıcı veritabanı kullan</span><span class="sxs-lookup"><span data-stu-id="cc59e-169">Use a common user database</span></span>

<span data-ttu-id="cc59e-170">Uygulamalar aynı Identity şemayı (sürümünü Identity ) kullandıklarında, Identity her uygulama sisteminin aynı kullanıcı veritabanına işaret ettiği konusunda emin olun.</span><span class="sxs-lookup"><span data-stu-id="cc59e-170">When apps use the same Identity schema (same version of Identity), confirm that the Identity system for each app is pointed at the same user database.</span></span> <span data-ttu-id="cc59e-171">Aksi takdirde, kimlik sistemi, kimlik doğrulamasında bulunan bilgileri veritabanındaki bilgilere göre eşleştirmeye çalıştığında çalışma zamanında hatalara neden olur cookie .</span><span class="sxs-lookup"><span data-stu-id="cc59e-171">Otherwise, the identity system produces failures at runtime when it attempts to match the information in the authentication cookie against the information in its database.</span></span>

<span data-ttu-id="cc59e-172">IdentityŞema uygulamalar arasında farklı olduğunda, genellikle uygulamalar farklı Identity sürümler kullandığından, en son sürümünü temel alan ortak bir veritabanının paylaşılması, Identity yeniden eşleştirmeden ve diğer uygulamanın şemalarında sütun eklemeden mümkün değildir Identity .</span><span class="sxs-lookup"><span data-stu-id="cc59e-172">When the Identity schema is different among apps, usually because apps are using different Identity versions, sharing a common database based on the latest version of Identity isn't possible without remapping and adding columns in other app's Identity schemas.</span></span> <span data-ttu-id="cc59e-173">IdentityOrtak bir veritabanının uygulamalar tarafından paylaşılabilmesi için diğer uygulamaları en son sürümü kullanmak üzere yükseltmek genellikle daha etkilidir.</span><span class="sxs-lookup"><span data-stu-id="cc59e-173">It's often more efficient to upgrade the other apps to use the latest Identity version so that a common database can be shared by the apps.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cc59e-174">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="cc59e-174">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
