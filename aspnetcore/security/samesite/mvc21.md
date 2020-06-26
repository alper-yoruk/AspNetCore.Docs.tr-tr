---
title: ASP.NET Core 2,1 MVC SameSite tanımlama bilgisi örneği
author: rick-anderson
description: ASP.NET Core 2,1 MVC SameSite tanımlama bilgisi örneği
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/samesite/mvc21
ms.openlocfilehash: 4239321531f3a7696a15b1dea164450ea0860c2b
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85409065"
---
# <a name="aspnet-core-21-mvc-samesite-cookie-sample"></a><span data-ttu-id="fd338-103">ASP.NET Core 2,1 MVC SameSite tanımlama bilgisi örneği</span><span class="sxs-lookup"><span data-stu-id="fd338-103">ASP.NET Core 2.1 MVC SameSite cookie sample</span></span>

<span data-ttu-id="fd338-104">ASP.NET Core 2,1, [SameSite](https://www.owasp.org/index.php/SameSite) özniteliği için yerleşik desteğe sahiptir, ancak özgün standart olarak yazılmıştır.</span><span class="sxs-lookup"><span data-stu-id="fd338-104">ASP.NET Core 2.1 has built-in support for the [SameSite](https://www.owasp.org/index.php/SameSite) attribute, but it was written to the original standard.</span></span> <span data-ttu-id="fd338-105">[Düzeltme eki uygulanmış davranış](https://github.com/dotnet/aspnetcore/issues/8212) , `SameSite.None` `None` değerini değeri bir değere yaymaması yerine, ' ın bir değeriyle birlikte göstermek için öğesinin anlamını değiştirdi.</span><span class="sxs-lookup"><span data-stu-id="fd338-105">The [patched behavior](https://github.com/dotnet/aspnetcore/issues/8212) changed the meaning of `SameSite.None` to emit the sameSite attribute with a value of `None`, rather than not emit the value at all.</span></span> <span data-ttu-id="fd338-106">Değeri göstermek istiyorsanız, `SameSite` bir tanımlama bilgisinde özelliğini-1 olarak ayarlayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fd338-106">If you want to not emit the value you can set the `SameSite` property on a cookie to -1.</span></span>

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a><span data-ttu-id="fd338-107">SameSite özniteliği yazılıyor</span><span class="sxs-lookup"><span data-stu-id="fd338-107">Writing the SameSite attribute</span></span>

<span data-ttu-id="fd338-108">Bir tanımlama bilgisine bir SameSite özniteliği yazma örneği aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="fd338-108">Following is an example of how to write a SameSite attribute on a cookie:</span></span>

```c#
var cookieOptions = new CookieOptions
{
    // Set the secure flag, which Chrome's changes will require for SameSite none.
    // Note this will also require you to be running on HTTPS
    Secure = true,

    // Set the cookie to HTTP only which is good practice unless you really do need
    // to access it client side in scripts.
    HttpOnly = true,

    // Add the SameSite attribute, this will emit the attribute with a value of none.
    // To not emit the attribute at all set the SameSite property to (SameSiteMode)(-1).
    SameSite = SameSiteMode.None
};

// Add the cookie to the response cookie collection
Response.Cookies.Append(CookieName, "cookieValue", cookieOptions);
```

## <a name="setting-cookie-authentication-and-session-state-cookies"></a><span data-ttu-id="fd338-109">Tanımlama bilgisi kimlik doğrulaması ve oturum durumu tanımlama bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="fd338-109">Setting Cookie Authentication and Session State cookies</span></span>

<span data-ttu-id="fd338-110">Tanımlama bilgisi kimlik doğrulaması, oturum durumu ve [diğer diğer bileşenler, farklı](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) site seçeneklerini tanımlama bilgisi seçenekleri aracılığıyla ayarlar, örneğin</span><span class="sxs-lookup"><span data-stu-id="fd338-110">Cookie authentication, session state and [various other components](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) set their sameSite options via Cookie options, for example</span></span>

```c#
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
    .AddCookie(options =>
    {
        options.Cookie.SameSite = SameSiteMode.None;
        options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
        options.Cookie.IsEssential = true;
    });

services.AddSession(options =>
{
    options.Cookie.SameSite = SameSiteMode.None;
    options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
    options.Cookie.IsEssential = true;
});
```

<span data-ttu-id="fd338-111">Yukarıdaki kodda, tanımlama bilgisi kimlik doğrulaması ve oturum durumu ' ı ' olarak ayarlayın `None` , özniteliği bir değerle yayın `None` ve ayrıca güvenli özniteliği true olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="fd338-111">In the preceding code, both cookie authentication and session state set their sameSite attribute to `None`, emitting the attribute with a `None` value, and also set the Secure attribute to true.</span></span>

### <a name="run-the-sample"></a><span data-ttu-id="fd338-112">Örneği çalıştırma</span><span class="sxs-lookup"><span data-stu-id="fd338-112">Run the sample</span></span>

<span data-ttu-id="fd338-113">[Örnek projeyi](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)çalıştırırsanız, tarayıcı hata ayıklayıcıyı ilk sayfaya yükleyin ve site için tanımlama bilgisi toplamasını görüntülemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="fd338-113">If you run the [sample project](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC), load your browser debugger on the initial page and use it to view the cookie collection for the site.</span></span> <span data-ttu-id="fd338-114">Bunu Edge ve Chrome 'da yapmak için `F12` , `Application` sekmesini seçin ve bölümünde seçeneğinin altındaki site URL 'sine tıklayın `Cookies` `Storage` .</span><span class="sxs-lookup"><span data-stu-id="fd338-114">To do so in Edge and Chrome press `F12` then select the `Application` tab and click the site URL under the `Cookies` option in the `Storage` section.</span></span>

![Tarayıcı hata ayıklayıcısı tanımlama bilgisi listesi](BrowserDebugger.png)

<span data-ttu-id="fd338-116">"SameSite tanımlama bilgisi oluştur" düğmesine tıkladığınızda örnek tarafından oluşturulan tanımlama bilgisinin, `Lax` [örnek kodda](#sampleCode)ayarlanan değer Ile eşleşen bir SameSite özniteliği değeri olduğundan, yukarıdaki görüntüden bakabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fd338-116">You can see from the image above that the cookie created by the sample when you click the "Create SameSite Cookie" button has a SameSite attribute value of `Lax`, matching the value set in the [sample code](#sampleCode).</span></span>

## <a name="intercepting-cookies"></a><a name="interception"></a><span data-ttu-id="fd338-117">Tanımlama bilgilerini kesintiye</span><span class="sxs-lookup"><span data-stu-id="fd338-117">Intercepting cookies</span></span>

<span data-ttu-id="fd338-118">Tanımlama bilgilerini ele almak için, yok değerini kullanıcının tarayıcı aracısındaki desteğine göre ayarlamak için, `CookiePolicy` ara yazılımı kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="fd338-118">In order to intercept cookies, to adjust the none value according to its support in the user's browser agent you must use the `CookiePolicy` middleware.</span></span> <span data-ttu-id="fd338-119">Bu, tanımlama bilgilerini yazan ve içinde yapılandırılan bileşenlerden **önce** http istek ardışık düzenine yerleştirilmesi gerekir `ConfigureServices()` .</span><span class="sxs-lookup"><span data-stu-id="fd338-119">This must be placed into the http request pipeline **before** any components that write cookies and configured within `ConfigureServices()`.</span></span>

<span data-ttu-id="fd338-120">`app.UseCookiePolicy()` `Configure(IApplicationBuilder, IHostingEnvironment)` [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs)içindeki yönteminde işlem hattı kullanımına eklemek için.</span><span class="sxs-lookup"><span data-stu-id="fd338-120">To insert it into the pipeline use `app.UseCookiePolicy()` in the `Configure(IApplicationBuilder, IHostingEnvironment)` method in [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs).</span></span> <span data-ttu-id="fd338-121">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="fd338-121">For example:</span></span>

```c#
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
       app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();

    app.UseMvc(routes =>
    {
        routes.MapRoute(
            name: "default",
            template: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

<span data-ttu-id="fd338-122">Ardından tanımlama `ConfigureServices(IServiceCollection services)` bilgilerini eklenen ya da silinen bir yardımcı sınıfa çağırmak için tanımlama bilgisi ilkesini yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="fd338-122">Then in the `ConfigureServices(IServiceCollection services)` configure the cookie policy to call out to a helper class when cookies are appended or deleted.</span></span> <span data-ttu-id="fd338-123">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="fd338-123">For example:</span></span>

```c#
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<CookiePolicyOptions>(options =>
    {
        options.CheckConsentNeeded = context => true;
        options.MinimumSameSitePolicy = SameSiteMode.None;
        options.OnAppendCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
        options.OnDeleteCookie = cookieContext =>
            CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
    });
}

private void CheckSameSite(HttpContext httpContext, CookieOptions options)
{
    if (options.SameSite == SameSiteMode.None)
    {
        var userAgent = httpContext.Request.Headers["User-Agent"].ToString();
        if (SameSite.BrowserDetection.DisallowsSameSiteNone(userAgent))
        {
            options.SameSite = (SameSiteMode)(-1);
        }
    }
}
```

<span data-ttu-id="fd338-124">Yardımcı işlevi `CheckSameSite(HttpContext, CookieOptions)` :</span><span class="sxs-lookup"><span data-stu-id="fd338-124">The helper function `CheckSameSite(HttpContext, CookieOptions)`:</span></span>

* <span data-ttu-id="fd338-125">, İstek için tanımlama bilgilerinin eklendiği veya istekten silindiği zaman çağrılır.</span><span class="sxs-lookup"><span data-stu-id="fd338-125">Is called when cookies are appended to the request or deleted from the request.</span></span>
* <span data-ttu-id="fd338-126">`SameSite`Özelliğinin olarak ayarlanmış olup olmadığını denetler `None` .</span><span class="sxs-lookup"><span data-stu-id="fd338-126">Checks to see if the `SameSite` property is set to `None`.</span></span>
* <span data-ttu-id="fd338-127">`SameSite`, Olarak ayarlanmışsa `None` ve geçerli kullanıcı aracısına None öznitelik değerini desteklemediği bilinmektedir.</span><span class="sxs-lookup"><span data-stu-id="fd338-127">If `SameSite` is set to `None` and the current user agent is known to not support the none attribute value.</span></span> <span data-ttu-id="fd338-128">Denetim, [Samesitesupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) sınıfı kullanılarak yapılır:</span><span class="sxs-lookup"><span data-stu-id="fd338-128">The check is done using the [SameSiteSupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) class:</span></span>
  * <span data-ttu-id="fd338-129">`SameSite`Özelliği özelliği olarak ayarlayarak değeri yaymayan ayarlar`(SameSiteMode)(-1)`</span><span class="sxs-lookup"><span data-stu-id="fd338-129">Sets `SameSite` to not emit the value by setting the property to `(SameSiteMode)(-1)`</span></span>

## <a name="targeting-net-framework"></a><span data-ttu-id="fd338-130">Hedefleme .NET Framework</span><span class="sxs-lookup"><span data-stu-id="fd338-130">Targeting .NET Framework</span></span>

<span data-ttu-id="fd338-131">ASP.NET Core ve System. Web (ASP.NET Classic), SameSite 'nin bağımsız uygulamalarına sahiptir.</span><span class="sxs-lookup"><span data-stu-id="fd338-131">ASP.NET Core and System.Web (ASP.NET Classic) have independent implementations of SameSite.</span></span> <span data-ttu-id="fd338-132">ASP.NET Core kullanılıyorsa veya System. Web SameSite en düşük çerçeve sürümü gereksinimi (.NET 4.7.2) ASP.NET Core için .NET Framework, için SameSite KB yamaları gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="fd338-132">The SameSite KB patches for .NET Framework are not required if using ASP.NET Core nor does the System.Web SameSite minimum framework version requirement (.NET 4.7.2) apply to ASP.NET Core.</span></span>

<span data-ttu-id="fd338-133">.NET üzerindeki ASP.NET Core, uygun düzeltmeleri almak için NuGet paket bağımlılıklarının güncelleştirilmesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="fd338-133">ASP.NET Core on .NET requires updating nuget package dependencies to get the appropriate fixes.</span></span>

<span data-ttu-id="fd338-134">.NET Framework ASP.NET Core değişiklikleri almak için, düzeltme eki uygulanmış paketlere ve sürümlere (2.1.14 veya üzeri 2,1 sürümler) doğrudan başvurtığınızdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="fd338-134">To get the ASP.NET Core changes for .NET Framework ensure that you have a direct reference to the patched packages and versions (2.1.14 or later 2.1 versions).</span></span>

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a><span data-ttu-id="fd338-135">Daha Fazla Bilgi</span><span class="sxs-lookup"><span data-stu-id="fd338-135">More Information</span></span>
 
<span data-ttu-id="fd338-136">[Chrome güncelleştirmeleri](https://www.chromium.org/updates/same-site) 
 [ASP.NET Core SameSite belgeleri](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1) 
 [ASP.NET Core 2,1 SameSite değişiklik duyurusu](https://github.com/dotnet/aspnetcore/issues/8212)</span><span class="sxs-lookup"><span data-stu-id="fd338-136">[Chrome Updates](https://www.chromium.org/updates/same-site)
[ASP.NET Core SameSite Documentation](https://docs.microsoft.com/aspnet/core/security/samesite?view=aspnetcore-2.1)
[ASP.NET Core 2.1 SameSite Change Announcement](https://github.com/dotnet/aspnetcore/issues/8212)</span></span>