---
title: ASP.NET Core projesinde kullanıcı verilerini Identity'e ekleme, indirme ve silme
author: rick-anderson
description: ASP.NET Core projesinde Kimlik'e özel kullanıcı verilerini nasıl ekleyeceğinizi öğrenin. GDPR başına verileri silin.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 76b83df22381429feab80056c36dbdac1e5f20c7
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501225"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a>ASP.NET Core projesinde özel kullanıcı verilerini Identity'e ekleme, indirme ve silme

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu makalede, nasıl gösterilmektedir:

* ASP.NET Core web uygulamasına özel kullanıcı verileri ekleyin.
* Özel kullanıcı veri modelini <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> özel nitelikle işaretleyin, böylece karşıdan yükleme ve silme için otomatik olarak kullanılabilir. Verilerin indirilebilmeleri ve silinmelerini sağlamak [GDPR](xref:security/gdpr) gereksinimlerini karşılamaya yardımcı olur.

Proje örneği Bir Razor Pages web uygulamasından oluşturulur, ancak talimatlar ASP.NET Core MVC web uygulaması için benzerdir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Ön koşullar

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a>Razor web uygulaması oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* Visual Studio **File** menüsünden **Yeni** > **Proje'yi**seçin. İstersenbir proje **webapp1** adını indir [örnek](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) kodun ad alanıyla eşleştirin.
* **Core Web Application** > **OKASP.NET** seçin
* Açılır ASP.NET **Core 3.0'ı** seçin
* **Web Uygulaması Tamam'ı** > **OK** Seçin
* Projeyi oluşturun ve çalıştırın.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Visual Studio **File** menüsünden **Yeni** > **Proje'yi**seçin. İstersenbir proje **webapp1** adını indir [örnek](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) kodun ad alanıyla eşleştirin.
* **Core Web Application** > **OKASP.NET** seçin
* Açılan yolda **Core 2.2ASP.NET** seçin
* **Web Uygulaması Tamam'ı** > **OK** Seçin
* Projeyi oluşturun ve çalıştırın.

::: moniker-end


# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a>Kimlik iskelesini çalıştırma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Solution Explorer'dan,** Yeni**İskele**Öğesi **Ekle>** > projeye sağ tıklayın.
* **İskele Ekle** iletişim kutusunun sol bölmesinden **Kimlik** > **Ekle'yi**seçin.
* Kimlik **Ekle** iletişim kutusunda aşağıdaki seçenekler:
  * Varolan düzen dosyasını *~/Pages/Shared/_Layout.cshtml'i* seçin
  * Geçersiz kılmak için aşağıdaki dosyaları seçin:
    * **Hesap/Kayıt**
    * **Hesap/Yönet/Dizini**
  * Yeni **+** bir **Veri bağlamı sınıfı**oluşturmak için düğmeyi seçin. Türü kabul edin (**WebApp1.Models.WebApp1Context** eğer proje **WebApp1**adlandırılmışsa).
  * Yeni **+** bir **Kullanıcı sınıfı**oluşturmak için düğmeyi seçin. Türü kabul edin (**WebApp1User** proje **WebApp1**adlı ise ) > **Ekle**.
* **Ekle'yi**seçin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

ASP.NET Core iskelesini daha önce yüklemediyseniz, şimdi yükleyin:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Proje (.csproj) dosyasına [Microsoft.VisualStudio.Web.CodeGeneration.Design'a](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) bir paket başvurusu ekleyin. Proje dizininde aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Kimlik iskelesi seçeneklerini listelemek için aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Proje klasöründe Kimlik iskelesini çalıştırın:

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

Geçişler, [Kimlik Doğrulaması Kullanımı ve düzendeki](xref:security/authentication/scaffold-identity#efm) yönergeyi aşağıdaki adımları gerçekleştirmek için izleyin:

* Geçiş oluşturun ve veritabanını güncelleştirin.
* Add `UseAuthentication` to `Startup.Configure`.
* Düzen `<partial name="_LoginPartial" />` dosyasına ekleyin.
* Uygulamayı test edin:
  * Bir kullanıcıyı kaydetme
  * Yeni kullanıcı adını seçin **(Oturum Açma** bağlantısının yanında). Kullanıcı adını ve diğer bağlantıları göstermek için pencereyi genişletmeniz veya gezinti çubuğu simgesini seçmeniz gerekebilir.
  * Kişisel **Veriler** sekmesini seçin.
  * **İndir** düğmesini seçin ve *PersonalData.json* dosyasını inceledi.
  * Kullanıcıda oturum açan ları silen **Sil** düğmesini test edin.

## <a name="add-custom-user-data-to-the-identity-db"></a>Kimlik DB'sine özel kullanıcı verileri ekleme

`IdentityUser` Türemiş sınıfı özel özelliklere sahip güncelleştirin. Projeye WebApp1 adını verdiyseniz, dosyanın adı *Alanlar/Kimlik/Veri/WebApp1User.cs.* Dosyayı aşağıdaki kodla güncelleştirin:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

[PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) özniteliğine sahip özellikler şunlardır:

* *Silinen Alanlar/Kimlik/Sayfalar/Hesap/Yönet/SilPersonalData.cshtml* Razor `UserManager.Delete`Page çağırır.
* *Alanlar/Kimlik/Sayfalar/Hesap/Yönet/DownloadPersonalData.cshtml* Razor Page tarafından indirilen verilere dahildir.

### <a name="update-the-accountmanageindexcshtml-page"></a>Hesabı/Yönet/Index.cshtml sayfasını güncelleştir

`InputModel` Aşağıdaki vurgulanan kodile *Alanları/Kimlik/Sayfalar/Hesap/Yönet/Index.cshtml.cs'yi* güncelleyin:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

*Alanları/Kimlik/Sayfalar/Hesap/Yönet/Index.cshtml'i* aşağıdaki vurgulanan biçimlendirmeyle güncelleştirin:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

*Alanları/Kimlik/Sayfalar/Hesap/Yönet/Index.cshtml'i* aşağıdaki vurgulanan biçimlendirmeyle güncelleştirin:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>Hesap/Register.cshtml sayfasını güncelleştir

`InputModel` Aşağıdaki vurgulanan kodile *Alanları/Kimlik/Sayfalar/Hesap/Register.cshtml.cs'yi* güncelleyin:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

*Alanları/Kimlik/Sayfalar/Hesap/Register.cshtml'i* aşağıdaki vurgulanan biçimlendirmeyle güncelleştirin:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

*Alanları/Kimlik/Sayfalar/Hesap/Register.cshtml'i* aşağıdaki vurgulanan biçimlendirmeyle güncelleştirin:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


Projeyi derleyin.

### <a name="add-a-migration-for-the-custom-user-data"></a>Özel kullanıcı verileri için geçiş ekleme

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio **Paket Yöneticisi Konsolunda:**

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a>Özel kullanıcı verilerini oluşturma, görüntüleme, indirme, silme

Uygulamayı test edin:

* Yeni bir kullanıcı kaydettirin.
* Sayfadaki özel kullanıcı `/Identity/Account/Manage` verilerini görüntüleyin.
* Kullanıcıların kişisel verilerini sayfadan `/Identity/Account/Manage/PersonalData` indirin ve görüntüleyin.

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a>IUserClaimsPrincipalFactory kullanarak kimlik için hak ekleme<ApplicationUser>

`IUserClaimsPrincipalFactory<T>` Arayüz kullanılarak ASP.NET Çekirdek Kimlik'e ek talepler eklenebilir. Bu sınıf `Startup.ConfigureServices` yöntemde uygulamaya eklenebilir. Sınıfın özel uygulamasını aşağıdaki gibi ekleyin:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

Demo kodu `ApplicationUser` sınıfı kullanır. Bu sınıf, `IsAdmin` ek talep eklemek için kullanılan bir özellik ekler.

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

`UserClaimsPrincipalFactory` Arabirimi `AdditionalUserClaimsPrincipalFactory` uygular. Yeni bir rol iddiası `ClaimsPrincipal`eklenir.

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

Ek talep daha sonra uygulamada kullanılabilir. Bir Razor `IAuthorizationService` Page'de, örnek talep değerine erişmek için kullanılabilir.

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
