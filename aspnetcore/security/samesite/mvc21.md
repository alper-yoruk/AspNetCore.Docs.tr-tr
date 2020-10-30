---
title: ASP.NET Core 2,1 MVC SameSite cookie örneği
author: rick-anderson
description: ASP.NET Core 2,1 MVC SameSite cookie örneği
monikerRange: '>= aspnetcore-2.1 < aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
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
uid: security/samesite/mvc21
ms.openlocfilehash: 61878af0f9af72284b43ffd46cca42b0cf043326
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051557"
---
# <a name="aspnet-core-21-mvc-samesite-no-loccookie-sample"></a>ASP.NET Core 2,1 MVC SameSite cookie örneği

ASP.NET Core 2,1, [SameSite](https://www.owasp.org/index.php/SameSite) özniteliği için yerleşik desteğe sahiptir, ancak özgün standart olarak yazılmıştır. [Düzeltme eki uygulanmış davranış](https://github.com/dotnet/aspnetcore/issues/8212) , `SameSite.None` `None` değerini değeri bir değere yaymaması yerine, ' ın bir değeriyle birlikte göstermek için öğesinin anlamını değiştirdi. Değeri göstermek istiyorsanız, `SameSite` özelliğini-1 ' de ayarlayabilirsiniz cookie .

[!INCLUDE[](~/includes/SameSiteIdentity.md)]

## <a name="writing-the-samesite-attribute"></a><a name="sampleCode"></a>SameSite özniteliği yazılıyor

Aşağıdakilerden birine bir SameSite özniteliği yazma örneği verilmiştir cookie :

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

## <a name="setting-no-loccookie-authentication-and-session-state-no-loccookies"></a>CookieKimlik doğrulaması ve oturum durumu cookie s ayarlanıyor

Cookie kimlik doğrulama, oturum durumu ve [diğer diğer bileşenler](../samesite.md?view=aspnetcore-2.1) aynı site seçeneklerini seçenekler aracılığıyla ayarlar Cookie , örneğin

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

Yukarıdaki kodda, hem cookie kimlik doğrulaması hem de oturum durumu, `None` özniteliği bir değerle yayın `None` ve ayrıca güvenli özniteliğini true olarak ayarlayın.

### <a name="run-the-sample"></a>Örneği çalıştırma

[Örnek projeyi](https://github.com/blowdart/AspNetSameSiteSamples/tree/master/AspNetCore21MVC)çalıştırırsanız, tarayıcı hata ayıklayıcıyı ilk sayfaya yükleyin ve site koleksiyonunu görüntülemek için kullanın cookie . Bunu Edge ve Chrome 'da yapmak için `F12` , `Application` sekmesini seçin ve bölümünde seçeneğinin altındaki site URL 'sine tıklayın `Cookies` `Storage` .

![Tarayıcı hata ayıklayıcı::: No-Loc (Cookie)::: List](BrowserDebugger.png)

cookie"SameSite oluştur" düğmesine tıkladığınızda örnek tarafından oluşturulan görüntüden Cookie `Lax` , [örnek kodda](#sampleCode)ayarlanan değerle eşleşen bir SameSite özniteliği değeri olduğunu görebilirsiniz.

## <a name="intercepting-no-loccookies"></a><a name="interception"></a>Kesintiye cookie uğrat

cookieKullanıcıların tarayıcı aracısındaki desteğine göre None değerini ayarlamak için, `CookiePolicy` ara yazılımı kullanmanız gerekir. Bu, içinde yazma yapan ve yapılandırılan bileşenlerden **önce** http istek işlem hattının yerleştirilmesi gerekir cookie `ConfigureServices()` .

`app.UseCookiePolicy()` `Configure(IApplicationBuilder, IHostingEnvironment)` [Startup.cs](https://github.com/blowdart/AspNetSameSiteSamples/blob/master/AspNetCore21MVC/Startup.cs)içindeki yönteminde işlem hattı kullanımına eklemek için. Örneğin:

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

Ardından, `ConfigureServices(IServiceCollection services)` cookie ilke eklendiği veya silindiği zaman bir yardımcı sınıfa çağırmak için ilkeyi yapılandırın cookie . Örneğin:

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

Yardımcı işlevi `CheckSameSite(HttpContext, CookieOptions)` :

* cookieİstekleri isteğin sonuna eklendiğinde veya istekten silindiği zaman çağrılır.
* `SameSite`Özelliğinin olarak ayarlanmış olup olmadığını denetler `None` .
* `SameSite`, Olarak ayarlanmışsa `None` ve geçerli kullanıcı aracısına None öznitelik değerini desteklemediği bilinmektedir. Denetim, [Samesitesupport](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/samesite/sample/snippets/SameSiteSupport.cs) sınıfı kullanılarak yapılır:
  * `SameSite`Özelliği özelliği olarak ayarlayarak değeri yaymayan ayarlar`(SameSiteMode)(-1)`

## <a name="targeting-net-framework"></a>Hedefleme .NET Framework

ASP.NET Core ve System. Web (ASP.NET Classic), SameSite 'nin bağımsız uygulamalarına sahiptir. ASP.NET Core kullanılıyorsa veya System. Web SameSite en düşük çerçeve sürümü gereksinimi (.NET 4.7.2) ASP.NET Core için .NET Framework, için SameSite KB yamaları gerekli değildir.

.NET üzerindeki ASP.NET Core, uygun düzeltmeleri almak için NuGet paket bağımlılıklarının güncelleştirilmesini gerektirir.

.NET Framework ASP.NET Core değişiklikleri almak için, düzeltme eki uygulanmış paketlere ve sürümlere (2.1.14 veya üzeri 2,1 sürümler) doğrudan başvurtığınızdan emin olun.

```xml
<PackageReference Include="Microsoft.Net.Http.Headers" Version="2.1.14" />
<PackageReference Include="Microsoft.AspNetCore.CookiePolicy" Version="2.1.14" />
```

### <a name="more-information"></a>Daha Fazla Bilgi
 
[Chrome güncelleştirmeleri](https://www.chromium.org/updates/same-site) 
 [ASP.NET Core SameSite belgeleri](../samesite.md?view=aspnetcore-2.1) 
 [ASP.NET Core 2,1 SameSite değişiklik duyurusu](https://github.com/dotnet/aspnetcore/issues/8212)