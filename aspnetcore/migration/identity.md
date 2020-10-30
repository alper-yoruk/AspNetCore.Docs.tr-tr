---
title: Kimlik doğrulamasını ve Identity ASP.NET Core geçir
author: ardalis
description: Bir ASP.NET MVC projesinden kimlik doğrulaması ve kimliği ASP.NET Core MVC projesine geçirmeyi öğrenin.
ms.author: riande
ms.date: 3/22/2020
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
uid: migration/identity
ms.openlocfilehash: 8ceff0596c069d815c38b9bb526477a9d1430951
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060657"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core"></a>Kimlik doğrulamasını ve Identity ASP.NET Core geçir

[Steve Smith](https://ardalis.com/) tarafından

Önceki makalede, [yapılandırmayı ASP.NET Core MVC 'ye bir ASP.NET MVC projesinden geçirdik](xref:migration/configuration). Bu makalede, kayıt, oturum açma ve Kullanıcı yönetimi özelliklerini geçiririz.

## <a name="configure-no-locidentity-and-membership"></a>Yapılandırma Identity ve üyelik

ASP.NET MVC 'de, kimlik doğrulama ve kimlik özellikleri Identity *App_Start* klasöründe bulunan *Startup.Auth.cs* ve *Identity config.cs* ile ASP.NET kullanılarak yapılandırılır. ASP.NET Core MVC 'de, bu özellikler *Startup.cs* ' de yapılandırılır.

Aşağıdaki NuGet paketlerini yükleyin:

* `Microsoft.AspNetCore.Identity.EntityFrameworkCore`
* `Microsoft.AspNetCore.Authentication.Cookies`
* `Microsoft.EntityFrameworkCore.SqlServer`

*Startup.cs* ' de, `Startup.ConfigureServices` Entity Framework ve hizmetlerini kullanmak için yöntemi güncelleştirin Identity :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add EF services to the services container.
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

     services.AddMvc();
}
```

Bu noktada, yukarıdaki kodda henüz ASP.NET MVC projesinden geçirdiğimiz iki tür vardır: `ApplicationDbContext` ve `ApplicationUser` . ASP.NET Core projesinde yeni *modeller* klasörü oluşturun ve bu türlere karşılık gelen kendisine iki sınıf ekleyin. Bu sınıfların ASP.NET MVC sürümlerini */models/ Identity models.cs* içinde bulacaksınız, ancak daha açık olan bu yana geçirilmiş projede sınıf başına bir dosya kullanacağız.

*ApplicationUser.cs* :

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : IdentityUser
  {
  }
}
```

*ApplicationDbContext.cs* :

```csharp
using Microsoft.AspNetCore.Identity.EntityFrameworkCore;
using Microsoft.Data.Entity;

namespace NewMvcProject.Models
{
    public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        protected override void OnModelCreating(ModelBuilder builder)
        {
            base.OnModelCreating(builder);
            // Customize the ASP.NET Core Identity model and override the defaults if needed.
            // For example, you can rename the ASP.NET Core Identity table names and more.
            // Add your customizations after calling base.OnModelCreating(builder);
        }
    }
}
```

ASP.NET Core MVC Başlatıcı Web projesi, kullanıcıların çok fazla özelleştirmesini veya ' i içermez `ApplicationDbContext` . Gerçek bir uygulamayı geçirirken uygulamanızın kullanıcı ve sınıflarının tüm özel özelliklerini ve yöntemlerini `DbContext` , uygulamanızın de tüm diğer model sınıflarını geçirmeniz gerekir. Örneğin, varsa `DbContext` `DbSet<Album>` , sınıfı geçirmeniz gerekir `Album` .

Bu dosyalar söz konusu olduğunda, *Startup.cs* dosyası deyimlerini güncelleştirerek derleme yapmak için kullanılabilir `using` :

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

Uygulamamız artık kimlik doğrulama ve Hizmetleri desteklemeye hazırdır Identity . Yalnızca bu özelliklerin kullanıcılara açık olması gerekir.

## <a name="migrate-registration-and-login-logic"></a>Kayıt ve oturum açma mantığını geçirme

IdentityUygulama için yapılandırılmış hizmetler ve Entity Framework ve SQL Server kullanılarak yapılandırılmış veri erişimi sayesinde, kayıt ve uygulamaya oturum açma desteği eklemeye hazırız. [Daha önce geçiş sürecinde,](xref:migration/mvc#migrate-the-layout-file) *_Layout. cshtml* içindeki *_LoginPartial* bir başvuruyu yorumlayacağız. Artık bu koda geri dönmeli, bu kodun açıklamasını kaldırın ve oturum açma işlevlerini desteklemek için gerekli denetleyiciler ve görünümlerde ekleme zamanı vardır.

`@Html.Partial` *_Layout. cshtml* içindeki satırın açıklamasını kaldırın:

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

Şimdi, Razor *Görünümler/paylaşılan* klasöre *_LoginPartial* adlı yeni bir görünüm ekleyin:

*_LoginPartial. cshtml* 'yi aşağıdaki kodla güncelleştirin (tüm içeriğini değiştirin):

```cshtml
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager

@if (SignInManager.IsSignedIn(User))
{
    <form asp-area="" asp-controller="Account" asp-action="Logout" method="post" id="logoutForm" class="navbar-right">
        <ul class="nav navbar-nav navbar-right">
            <li>
                <a asp-area="" asp-controller="Manage" asp-action="Index" title="Manage">Hello @UserManager.GetUserName(User)!</a>
            </li>
            <li>
                <button type="submit" class="btn btn-link navbar-btn navbar-link">Log out</button>
            </li>
        </ul>
    </form>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li><a asp-area="" asp-controller="Account" asp-action="Register">Register</a></li>
        <li><a asp-area="" asp-controller="Account" asp-action="Login">Log in</a></li>
    </ul>
}
```

Bu noktada, tarayıcıda siteyi yenileyebilmelisiniz.

## <a name="summary"></a>Özet

ASP.NET Core, ASP.NET özelliklerinde yapılan değişiklikleri tanıtır Identity . Bu makalede, ASP.NET 'in kimlik doğrulama ve Kullanıcı Yönetimi özelliklerinin ASP.NET Core 'e nasıl geçirileceğiyle karşılaşmış olursunuz Identity .
