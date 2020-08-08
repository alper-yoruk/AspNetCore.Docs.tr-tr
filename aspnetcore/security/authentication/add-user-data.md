---
title: ASP.NET Core projesindeki Kullanıcı verilerini ekleme, indirme ve silme Identity
author: rick-anderson
description: ASP.NET Core projesindeki özel kullanıcı verilerinin nasıl ekleneceğini öğrenin Identity . GDPR başına verileri silme.
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/add-user-data
ms.openlocfilehash: d65974e9ff8e2f5be52ab79b063ed9d2dca557ea
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020866"
---
# <a name="add-download-and-delete-custom-user-data-to-no-locidentity-in-an-aspnet-core-project"></a>ASP.NET Core projesindeki özel kullanıcı verilerini ekleme, indirme ve silme Identity

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu makalede nasıl yapılacağı gösterilmektedir:

* ASP.NET Core Web uygulamasına özel kullanıcı verileri ekleyin.
* Özel Kullanıcı veri modelini özniteliği ile işaretleyin, <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> Bu nedenle otomatik olarak indirilebilir ve silinemez. Verilerin indirilip silinebilmesini sağlamak, [GDPR](xref:security/gdpr) gereksinimlerini karşılamaya yardımcı olur.

Proje örneği bir sayfalar Web uygulamasından oluşturulur Razor , ancak yönergeler ASP.NET Core MVC web uygulaması için benzerdir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Önkoşullar

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-no-locrazor-web-app"></a>RazorWeb uygulaması oluşturma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* Visual Studio **Dosya** menüsünden **Yeni**  >  **Proje**' yi seçin. [İndirme örnek](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) kodunun ad alanıyla eşleştirmek Istiyorsanız projeyi **WebApp1** olarak adlandırın.
* **ASP.NET Core Web uygulaması** > **Tamam ' ı** seçin
* Açılan listede **ASP.NET Core 3,0** ' i seçin
* **Web uygulaması** > **Tamam 'ı** seçin
* Projeyi derleyin ve çalıştırın.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* Visual Studio **Dosya** menüsünden **Yeni**  >  **Proje**' yi seçin. [İndirme örnek](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data) kodunun ad alanıyla eşleştirmek Istiyorsanız projeyi **WebApp1** olarak adlandırın.
* **ASP.NET Core Web uygulaması** > **Tamam ' ı** seçin
* Açılan listede **ASP.NET Core 2,2** ' i seçin
* **Web uygulaması** > **Tamam 'ı** seçin
* Projeyi derleyin ve çalıştırın.

::: moniker-end


# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-no-locidentity-scaffolder"></a>IdentityDesteği 'ı çalıştırma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Çözüm Gezgini**, projeye sağ tıklayıp **Add**  >  **Yeni iskli öğe**Ekle >.
* **Yapı iskelesi Ekle** iletişim kutusunun sol bölmesinde Ekle ' yi seçin **Identity**  >  **Add**.
* **Ekle Identity ** iletişim kutusunda aşağıdaki seçenekler:
  * Var olan düzen dosyasını seçin *~/Pages/Shared/_Layout. cshtml*
  * Geçersiz kılmak için aşağıdaki dosyaları seçin:
    * **Hesap/kayıt**
    * **Hesap/yönet/Dizin**
  * **+** Yeni bir **veri bağlamı sınıfı**oluşturmak için düğmeyi seçin. Proje **WebApp1**olarak adlandırılmışsa türü (**WebApp1. modeller. WebApp1Context** ) kabul edin.
  * **+** Yeni bir **Kullanıcı sınıfı**oluşturmak için düğmeyi seçin. **Ekle**> ( **projenin adı****WebApp1User** ) öğesini kabul edin.
* **Add (Ekle)** seçeneğini belirleyin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

ASP.NET Core scaffolder ' ı daha önce yüklemediyseniz, şimdi yükleyebilirsiniz:

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

Proje (. csproj) dosyasına [Microsoft. VisualStudio. Web. CodeGeneration. Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/) öğesine bir paket başvurusu ekleyin. Proje dizininde aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

Desteği seçeneklerini listelemek için aşağıdaki komutu çalıştırın Identity :

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

Proje klasöründe, Identity scaffolder ' ı çalıştırın:

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

Aşağıdaki adımları gerçekleştirmek için [geçişler, UseAuthentication ve düzen](xref:security/authentication/scaffold-identity#efm) bölümündeki yönergeleri izleyin:

* Bir geçiş oluşturun ve veritabanını güncelleştirin.
* Add `UseAuthentication` to `Startup.Configure`.
* `<partial name="_LoginPartial" />`Düzen dosyasına ekleyin.
* Uygulamayı test etme:
  * Kullanıcı kaydetme
  * Yeni Kullanıcı adını ( **oturum kapatma** bağlantısının yanında) seçin. Kullanıcı adını ve diğer bağlantıları göstermek için pencereyi genişletmeniz veya gezinti çubuğu simgesini seçmeniz gerekebilir.
  * **Kişisel veri** sekmesini seçin.
  * **İndir** düğmesini seçin ve dosyadaki *PersonalData.js* incelendi.
  * Oturum açan kullanıcıyı silen **Sil** düğmesini test edin.

## <a name="add-custom-user-data-to-the-no-locidentity-db"></a>Veritabanına özel kullanıcı verileri ekleme Identity

`IdentityUser`Türetilmiş sınıfı özel özelliklerle güncelleştirin. Projeyi WebApp1 olarak adlandırdıysanız, dosya */ Identity /Data/webapp1user.cs*olarak adlandırılır. Dosyayı aşağıdaki kodla güncelleştirin:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

[Personal Data](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute) özniteliğiyle birlikte bulunan özellikler şunlardır:

* *Areas/ Identity /Pages/Account/Manage/deletepersondata.exe* Razor sayfası çağırdığında silinir `UserManager.Delete` .
* İndirilen verilere */ Identity /Pages/Account/Manage/downloadpersonal Data.exe* Razor sayfasına dahildir.

### <a name="update-the-accountmanageindexcshtml-page"></a>Hesap/yönet/Index. cshtml sayfasını Güncelleştir

`InputModel` *Areas/ Identity /Pages/Account/Manage/Index.cshtml.cs* içinde aşağıdaki vurgulanmış kodla güncelleştirin:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

*Areas/ Identity /Pages/Account/Manage/Index.cshtml* öğesini aşağıdaki vurgulanmış işaretlerle güncelleştirin:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

*Areas/ Identity /Pages/Account/Manage/Index.cshtml* öğesini aşağıdaki vurgulanmış işaretlerle güncelleştirin:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>Account/Register. cshtml sayfasını Güncelleştir

`InputModel` *Areas/ Identity /Pages/Account/Register.cshtml.cs* içinde aşağıdaki vurgulanmış kodla güncelleştirin:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

*Areas/ Identity /Pages/Account/Register.exe* ' i aşağıdaki vurgulanmış işaretlerle güncelleştirin:

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

*Areas/ Identity /Pages/Account/Register.exe* ' i aşağıdaki vurgulanmış işaretlerle güncelleştirin:

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


Projeyi derleyin.

### <a name="add-a-migration-for-the-custom-user-data"></a>Özel Kullanıcı verileri için bir geçiş ekleyin

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Visual Studio **Paket Yöneticisi konsolunda**:

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

## <a name="test-create-view-download-delete-custom-user-data"></a>Test oluşturma, görüntüleme, indirme, özel kullanıcı verilerini silme

Uygulamayı test etme:

* Yeni bir Kullanıcı kaydedin.
* Sayfadaki özel kullanıcı verilerini görüntüleyin `/Identity/Account/Manage` .
* Kullanıcıların kişisel verilerini sayfadan indirip görüntüleyin `/Identity/Account/Manage/PersonalData` .

## <a name="add-claims-to-no-locidentity-using-iuserclaimsprincipalfactoryapplicationuser"></a>IdentityIUserClaimsPrincipalFactory kullanarak talepler ekleme<ApplicationUser>

> [!NOTE]
> Bu bölüm, önceki öğreticinin bir uzantısı değildir. Aşağıdaki adımları öğretici kullanılarak oluşturulan uygulamaya uygulamak için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/18797)bakın.

Arabirimi kullanılarak ASP.NET Core ek talepler eklenebilir Identity `IUserClaimsPrincipalFactory<T>` . Bu sınıf, yöntemi içinde uygulamaya eklenebilir `Startup.ConfigureServices` . Sınıfının özel uygulamasını şu şekilde ekleyin:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

Demo kodu `ApplicationUser` sınıfını kullanır. Bu sınıf `IsAdmin` , ek talep eklemek için kullanılan bir özellik ekler.

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

, `AdditionalUserClaimsPrincipalFactory` Arabirimini uygular `UserClaimsPrincipalFactory` . Yeni bir rol talebi öğesine eklenir `ClaimsPrincipal` .

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

Ek talep daha sonra uygulamada kullanılabilir. Bir Razor sayfada, örnek, `IAuthorizationService` talep değerine erişmek için kullanılabilir.

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
