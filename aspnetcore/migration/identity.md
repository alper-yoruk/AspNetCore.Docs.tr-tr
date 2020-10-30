---
title: 'Kimlik doğrulamasını ve :::no-loc(Identity)::: ASP.NET Core geçir'
author: ardalis
description: Bir ASP.NET MVC projesinden kimlik doğrulaması ve kimliği ASP.NET Core MVC projesine geçirmeyi öğrenin.
ms.author: riande
ms.date: 3/22/2020
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
uid: migration/identity
ms.openlocfilehash: 8ceff0596c069d815c38b9bb526477a9d1430951
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060657"
---
# <a name="migrate-authentication-and-no-locidentity-to-aspnet-core"></a><span data-ttu-id="6791b-103">Kimlik doğrulamasını ve :::no-loc(Identity)::: ASP.NET Core geçir</span><span class="sxs-lookup"><span data-stu-id="6791b-103">Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core</span></span>

<span data-ttu-id="6791b-104">[Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="6791b-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="6791b-105">Önceki makalede, [yapılandırmayı ASP.NET Core MVC 'ye bir ASP.NET MVC projesinden geçirdik](xref:migration/configuration).</span><span class="sxs-lookup"><span data-stu-id="6791b-105">In the previous article, we [migrated configuration from an ASP.NET MVC project to ASP.NET Core MVC](xref:migration/configuration).</span></span> <span data-ttu-id="6791b-106">Bu makalede, kayıt, oturum açma ve Kullanıcı yönetimi özelliklerini geçiririz.</span><span class="sxs-lookup"><span data-stu-id="6791b-106">In this article, we migrate the registration, login, and user management features.</span></span>

## <a name="configure-no-locidentity-and-membership"></a><span data-ttu-id="6791b-107">Yapılandırma :::no-loc(Identity)::: ve üyelik</span><span class="sxs-lookup"><span data-stu-id="6791b-107">Configure :::no-loc(Identity)::: and Membership</span></span>

<span data-ttu-id="6791b-108">ASP.NET MVC 'de, kimlik doğrulama ve kimlik özellikleri :::no-loc(Identity)::: *App_Start* klasöründe bulunan *Startup.Auth.cs* ve *:::no-loc(Identity)::: config.cs* ile ASP.NET kullanılarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="6791b-108">In ASP.NET MVC, authentication and identity features are configured using ASP.NET :::no-loc(Identity)::: in *Startup.Auth.cs* and *:::no-loc(Identity):::Config.cs* , located in the *App_Start* folder.</span></span> <span data-ttu-id="6791b-109">ASP.NET Core MVC 'de, bu özellikler *Startup.cs* ' de yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="6791b-109">In ASP.NET Core MVC, these features are configured in *Startup.cs* .</span></span>

<span data-ttu-id="6791b-110">Aşağıdaki NuGet paketlerini yükleyin:</span><span class="sxs-lookup"><span data-stu-id="6791b-110">Install the following NuGet packages:</span></span>

* `Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore`
* `Microsoft.AspNetCore.Authentication.:::no-loc(Cookie):::s`
* `Microsoft.EntityFrameworkCore.SqlServer`

<span data-ttu-id="6791b-111">*Startup.cs* ' de, `Startup.ConfigureServices` Entity Framework ve hizmetlerini kullanmak için yöntemi güncelleştirin :::no-loc(Identity)::: :</span><span class="sxs-lookup"><span data-stu-id="6791b-111">In *Startup.cs* , update the `Startup.ConfigureServices` method to use Entity Framework and :::no-loc(Identity)::: services:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add EF services to the services container.
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));

    services.Add:::no-loc(Identity):::<ApplicationUser, :::no-loc(Identity):::Role>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

     services.AddMvc();
}
```

<span data-ttu-id="6791b-112">Bu noktada, yukarıdaki kodda henüz ASP.NET MVC projesinden geçirdiğimiz iki tür vardır: `ApplicationDbContext` ve `ApplicationUser` .</span><span class="sxs-lookup"><span data-stu-id="6791b-112">At this point, there are two types referenced in the above code that we haven't yet migrated from the ASP.NET MVC project: `ApplicationDbContext` and `ApplicationUser`.</span></span> <span data-ttu-id="6791b-113">ASP.NET Core projesinde yeni *modeller* klasörü oluşturun ve bu türlere karşılık gelen kendisine iki sınıf ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6791b-113">Create a new *Models* folder in the ASP.NET Core project, and add two classes to it corresponding to these types.</span></span> <span data-ttu-id="6791b-114">Bu sınıfların ASP.NET MVC sürümlerini */models/ :::no-loc(Identity)::: models.cs* içinde bulacaksınız, ancak daha açık olan bu yana geçirilmiş projede sınıf başına bir dosya kullanacağız.</span><span class="sxs-lookup"><span data-stu-id="6791b-114">You will find the ASP.NET MVC versions of these classes in */Models/:::no-loc(Identity):::Models.cs* , but we will use one file per class in the migrated project since that's more clear.</span></span>

<span data-ttu-id="6791b-115">*ApplicationUser.cs* :</span><span class="sxs-lookup"><span data-stu-id="6791b-115">*ApplicationUser.cs* :</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore;

namespace NewMvcProject.Models
{
  public class ApplicationUser : :::no-loc(Identity):::User
  {
  }
}
```

<span data-ttu-id="6791b-116">*ApplicationDbContext.cs* :</span><span class="sxs-lookup"><span data-stu-id="6791b-116">*ApplicationDbContext.cs* :</span></span>

```csharp
using Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore;
using Microsoft.Data.Entity;

namespace NewMvcProject.Models
{
    public class ApplicationDbContext : :::no-loc(Identity):::DbContext<ApplicationUser>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }

        protected override void OnModelCreating(ModelBuilder builder)
        {
            base.OnModelCreating(builder);
            // Customize the :::no-loc(ASP.NET Core Identity)::: model and override the defaults if needed.
            // For example, you can rename the :::no-loc(ASP.NET Core Identity)::: table names and more.
            // Add your customizations after calling base.OnModelCreating(builder);
        }
    }
}
```

<span data-ttu-id="6791b-117">ASP.NET Core MVC Başlatıcı Web projesi, kullanıcıların çok fazla özelleştirmesini veya ' i içermez `ApplicationDbContext` .</span><span class="sxs-lookup"><span data-stu-id="6791b-117">The ASP.NET Core MVC Starter Web project doesn't include much customization of users, or the `ApplicationDbContext`.</span></span> <span data-ttu-id="6791b-118">Gerçek bir uygulamayı geçirirken uygulamanızın kullanıcı ve sınıflarının tüm özel özelliklerini ve yöntemlerini `DbContext` , uygulamanızın de tüm diğer model sınıflarını geçirmeniz gerekir.</span><span class="sxs-lookup"><span data-stu-id="6791b-118">When migrating a real app, you also need to migrate all of the custom properties and methods of your app's user and `DbContext` classes, as well as any other Model classes your app utilizes.</span></span> <span data-ttu-id="6791b-119">Örneğin, varsa `DbContext` `DbSet<Album>` , sınıfı geçirmeniz gerekir `Album` .</span><span class="sxs-lookup"><span data-stu-id="6791b-119">For example, if your `DbContext` has a `DbSet<Album>`, you need to migrate the `Album` class.</span></span>

<span data-ttu-id="6791b-120">Bu dosyalar söz konusu olduğunda, *Startup.cs* dosyası deyimlerini güncelleştirerek derleme yapmak için kullanılabilir `using` :</span><span class="sxs-lookup"><span data-stu-id="6791b-120">With these files in place, the *Startup.cs* file can be made to compile by updating its `using` statements:</span></span>

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.:::no-loc(Identity):::;
using Microsoft.AspNetCore.Hosting;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
```

<span data-ttu-id="6791b-121">Uygulamamız artık kimlik doğrulama ve Hizmetleri desteklemeye hazırdır :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="6791b-121">Our app is now ready to support authentication and :::no-loc(Identity)::: services.</span></span> <span data-ttu-id="6791b-122">Yalnızca bu özelliklerin kullanıcılara açık olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="6791b-122">It just needs to have these features exposed to users.</span></span>

## <a name="migrate-registration-and-login-logic"></a><span data-ttu-id="6791b-123">Kayıt ve oturum açma mantığını geçirme</span><span class="sxs-lookup"><span data-stu-id="6791b-123">Migrate registration and login logic</span></span>

<span data-ttu-id="6791b-124">:::no-loc(Identity):::Uygulama için yapılandırılmış hizmetler ve Entity Framework ve SQL Server kullanılarak yapılandırılmış veri erişimi sayesinde, kayıt ve uygulamaya oturum açma desteği eklemeye hazırız.</span><span class="sxs-lookup"><span data-stu-id="6791b-124">With :::no-loc(Identity)::: services configured for the app and data access configured using Entity Framework and SQL Server, we're ready to add support for registration and login to the app.</span></span> <span data-ttu-id="6791b-125">[Daha önce geçiş sürecinde,](xref:migration/mvc#migrate-the-layout-file) *_Layout. cshtml* içindeki *_LoginPartial* bir başvuruyu yorumlayacağız.</span><span class="sxs-lookup"><span data-stu-id="6791b-125">Recall that [earlier in the migration process](xref:migration/mvc#migrate-the-layout-file) we commented out a reference to *_LoginPartial* in *_Layout.cshtml* .</span></span> <span data-ttu-id="6791b-126">Artık bu koda geri dönmeli, bu kodun açıklamasını kaldırın ve oturum açma işlevlerini desteklemek için gerekli denetleyiciler ve görünümlerde ekleme zamanı vardır.</span><span class="sxs-lookup"><span data-stu-id="6791b-126">Now it's time to return to that code, uncomment it, and add in the necessary controllers and views to support login functionality.</span></span>

<span data-ttu-id="6791b-127">`@Html.Partial` *_Layout. cshtml* içindeki satırın açıklamasını kaldırın:</span><span class="sxs-lookup"><span data-stu-id="6791b-127">Uncomment the `@Html.Partial` line in *_Layout.cshtml* :</span></span>

```cshtml
      <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
    </ul>
    @*@Html.Partial("_LoginPartial")*@
  </div>
</div>
```

<span data-ttu-id="6791b-128">Şimdi, :::no-loc(Razor)::: *Görünümler/paylaşılan* klasöre *_LoginPartial* adlı yeni bir görünüm ekleyin:</span><span class="sxs-lookup"><span data-stu-id="6791b-128">Now, add a new :::no-loc(Razor)::: view called *_LoginPartial* to the *Views/Shared* folder:</span></span>

<span data-ttu-id="6791b-129">*_LoginPartial. cshtml* 'yi aşağıdaki kodla güncelleştirin (tüm içeriğini değiştirin):</span><span class="sxs-lookup"><span data-stu-id="6791b-129">Update *_LoginPartial.cshtml* with the following code (replace all of its contents):</span></span>

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

<span data-ttu-id="6791b-130">Bu noktada, tarayıcıda siteyi yenileyebilmelisiniz.</span><span class="sxs-lookup"><span data-stu-id="6791b-130">At this point, you should be able to refresh the site in your browser.</span></span>

## <a name="summary"></a><span data-ttu-id="6791b-131">Özet</span><span class="sxs-lookup"><span data-stu-id="6791b-131">Summary</span></span>

<span data-ttu-id="6791b-132">ASP.NET Core, ASP.NET özelliklerinde yapılan değişiklikleri tanıtır :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="6791b-132">ASP.NET Core introduces changes to the ASP.NET :::no-loc(Identity)::: features.</span></span> <span data-ttu-id="6791b-133">Bu makalede, ASP.NET 'in kimlik doğrulama ve Kullanıcı Yönetimi özelliklerinin ASP.NET Core 'e nasıl geçirileceğiyle karşılaşmış olursunuz :::no-loc(Identity)::: .</span><span class="sxs-lookup"><span data-stu-id="6791b-133">In this article, you have seen how to migrate the authentication and user management features of ASP.NET :::no-loc(Identity)::: to ASP.NET Core.</span></span>
