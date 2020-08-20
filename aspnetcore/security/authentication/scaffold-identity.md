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
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a>ASP.NET Core projelerinde yapı iskelesi Identity

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core [ASP.NET Core Identity](xref:security/authentication/identity) bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak sunulur. Dahil edilen uygulamalar, Identity Identity Razor sınıf kitaplığı 'nda (RCL) bulunan kaynak kodu seçmeli olarak eklemek için desteği uygulayabilir. Kodu değiştirebilmeniz ve davranışı değiştirebilmek için kaynak kodu oluşturmak isteyebilirsiniz. Örneğin, kayıt sırasında kullanılan kodu oluşturmak için desteği ' ı söyleyebilirsiniz. Oluşturulan kod RCL 'deki aynı koda göre önceliklidir Identity . Kullanıcı arabirimine tam denetim sağlamak ve varsayılan RCL 'yi kullanmak için, [tam Identity UI kaynağı oluşturma](#full)bölümüne bakın.

Kimlik **doğrulaması içermeyen uygulamalar** , RCL paketini eklemek için desteği uygulayabilir Identity . Oluşturulacak kodu seçme seçeneğiniz vardır Identity .

Desteği gerekli kodların çoğunu üretse de, işlemi gerçekleştirmek için projenizi güncelleştirmeniz gerekir. Bu belgede, bir yapı iskelesi güncelleştirmesini tamamlaması için gereken adımlar açıklanmaktadır Identity .

Dosya farklılıklarını gösteren ve değişikliklerden geri dönüş yapmanızı sağlayan bir kaynak denetimi sistemi kullanmanızı öneririz. Scaffolder çalıştırıldıktan sonra değişiklikleri inceleyin Identity .

[Iki öğeli kimlik doğrulaması](xref:security/authentication/identity-enable-qrcodes), [Hesap onaylama ve parola kurtarma](xref:security/authentication/accconfirm)ile diğer güvenlik özellikleri kullanılırken hizmetler gereklidir Identity . Hizmetler veya hizmet saplamaları, yapı iskelesi sırasında oluşturulmaz Identity . Bu özelliklerin etkinleştirilmesi için hizmetlerin el ile eklenmesi gerekir. Örneğin, bkz. [e-posta onayı gerektir](xref:security/authentication/accconfirm#require-email-confirmation).

IdentityYeni bir veri bağlamıyla, var olan bireysel hesaplara sahip bir projeye dönüştürme yaparken:

* İçinde `Startup.ConfigureServices` , şu çağrıları kaldırın:
  * `AddDbContext`
  * `AddDefaultIdentity`

Örneğin, `AddDbContext` ve `AddDefaultIdentity` aşağıdaki kodda yorum yapılır:

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

Önceki kod, *alanlarda/ Identity / Identity HostingStartup.cs* yinelenen kodu Yorumlar

Genellikle, bireysel hesaplarla oluşturulan uygulamalar yeni bir veri bağlamı ***oluşturmamalıdır*** .

## <a name="scaffold-no-locidentity-into-an-empty-project"></a>IdentityBoş bir projeye yapı iskelesi

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

`Startup`Sınıfını aşağıdakine benzer kodla güncelleştirin:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a>Identity Razor Var olan yetkilendirme olmadan bir projeye yapı iskelesi yapın

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

Identity, *alanlarında/ Identity / Identity HostingStartup.cs*' de yapılandırılır. Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Geçişler, UseAuthentication ve düzen

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Kimlik doğrulamayı etkinleştir

`Startup`Sınıfını aşağıdakine benzer kodla güncelleştirin:

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Düzen değişiklikleri

İsteğe bağlı: () oturum açma bilgilerini `_LoginPartial` Düzen dosyasına ekleyin:

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a>Identity Razor Yetkilendirmeyle bir projeye yapı

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

Bazı Identity Seçenekler, *alanlarda/ Identity / Identity HostingStartup.cs*içinde yapılandırılır. Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a>IdentityVar olan yetkilendirme olmadan BIR MVC projesinde yapı iskelesi yapın

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

İsteğe bağlı: bir Login ( `_LoginPartial` ) öğesini *views/Shared/_Layout. cshtml* dosyasına ekleyin:

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* *Pages/Shared/_LoginPartial. cshtml* dosyasını *views/Shared/_LoginPartial. cshtml* olarak taşıyın

Identity, *alanlarında/ Identity / Identity HostingStartup.cs*' de yapılandırılır. Daha fazla bilgi için bkz. ıhostingstartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

`Startup`Sınıfını aşağıdakine benzer kodla güncelleştirin:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a>IdentityYetkilendirme ile BIR MVC projesinde yapı iskelesi oluşturma

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a>Identity Blazor Server Var olan yetkilendirme olmadan bir projeye yapı iskelesi yapın

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity, *alanlarında/ Identity / Identity HostingStartup.cs*' de yapılandırılır. Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).

### <a name="migrations"></a>Geçişler

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a>Uygulamaya bir XSRF belirteci geçirin

Belirteçler bileşenlere geçirilebilir:

* Kimlik doğrulama belirteçleri sağlandığında ve kimlik doğrulamasına kaydedildiğinde cookie , bunlar bileşenlere geçirilebilir.
* Razor bileşenler doğrudan kullanamaz `HttpContext` . bu nedenle, tarihinde oturum kapatma uç noktasına gönderi için bir [istek önleme zaman AŞıMı (XSRF) belirteci](xref:security/anti-request-forgery) elde etmenin bir yolu yoktur Identity `/Identity/Account/Logout` . Bir XSRF belirteci bileşenlere geçirilebilir.

Daha fazla bilgi için bkz. <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.

*Pages/_Host. cshtml* dosyasında, ve sınıflarına eklendikten sonra belirteci oluşturun `InitialApplicationState` `TokenProvider` :

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

Şunu `App` atamak için bileşeni (*app. Razor*) güncelleştirin `InitialState.XsrfToken` :

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

`TokenProvider`Konusunda gösterilen hizmet, `LoginDisplay` bileşeninde aşağıdaki [Düzen ve kimlik doğrulama akışı değişiklikleri](#layout-and-authentication-flow-changes) bölümünde kullanılır.

### <a name="enable-authentication"></a>Kimlik doğrulamayı etkinleştir

`Startup`Sınıfında:

* RazorSayfaların hizmetlerinin eklendiğini onaylayın `Startup.ConfigureServices` .
* [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)kullanılıyorsa, hizmeti kaydedin.
* `UseDatabaseErrorPage`Geliştirme ortamı için içindeki uygulama oluşturucuda çağırın `Startup.Configure` .
* Çağırın `UseAuthentication` ve `UseAuthorization` sonra `UseRouting` .
* Sayfalar için bir uç nokta ekleyin Razor .

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a>Düzen ve kimlik doğrulama akışı değişiklikleri

`RedirectToLogin`Uygulamanın proje kökündeki *paylaşılan* klasörüne bir bileşen (*redirecttologin. Razor*) ekleyin:

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

`LoginDisplay`Uygulamanın *paylaşılan* klasörüne bir bileşen (*logindisplay. Razor*) ekleyin. [TokenProvider hizmeti](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) , Identity oturum kapatma uç noktasına gönderilen HTML formu için XSRF belirteci sağlar:

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

`MainLayout`Bileşende (*paylaşılan/mainlayout. Razor*), `LoginDisplay` bileşeni en üst satır `<div>` öğesinin içeriğine ekleyin:

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a>Stil kimlik doğrulama uç noktaları

Blazor Server Razor Sayfa sayfalarını kullandığından Identity , bir ziyaretçi sayfalar ve bileşenler arasında gezinse Kullanıcı arabiriminin stili değişir Identity . Uyumsuz stillere yönelik iki seçeneğiniz vardır:

#### <a name="build-no-locidentity-components"></a>Derleme Identity bileşenleri

Bileşenler oluşturmak için bileşenleri kullanmanın bir yaklaşımı Identity , Identity bileşenleri oluşturmaktır. `SignInManager` `UserManager` Razor Bileşenlerinde desteklenmediğinden, Blazor Server Kullanıcı hesabı EYLEMLERINI işlemek için uygulamadaki API uç noktalarını kullanın.

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a>Uygulama stilleriyle özel düzen kullanma Blazor

IdentitySayfa düzeni ve stilleri, varsayılan temayı kullanan sayfalar oluşturmak için değiştirilebilir Blazor .

> [!NOTE]
> Bu bölümdeki örnek yalnızca özelleştirme için bir başlangıç noktasıdır. En iyi kullanıcı deneyimi için büyük olasılıkla ek çalışma gerekir.

Yeni bir `NavMenu_IdentityLayout` bileşen oluşturun (*paylaşılan/NavMenu_ Identity Layout. Razor*). Bileşenin biçimlendirmesi ve kodu için, uygulamanın bileşeniyle aynı içeriği kullanın `NavMenu` (*paylaşılan/navmenu. Razor*). `NavLink`Bileşendeki otomatik yeniden yönlendirmeler `RedirectToLogin` kimlik doğrulama veya yetkilendirme gerektiren bileşenler için başarısız olduğundan anonim olarak erişilemeyen bileşenlere her birini kaldırın.

*Sayfalar/paylaşılan/Layout. cshtml* dosyasında aşağıdaki değişiklikleri yapın:

* RazorEtiket yardımcıları ve uygulamanın bileşenlerini *paylaşılan* klasörde kullanmak için dosyanın en üstüne yönergeler ekleyin:

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  `{APPLICATION ASSEMBLY}`Uygulamanın derleme adıyla değiştirin.

* İçeriğe bir `<base>` etiket ve Blazor stil sayfası ekleyin `<link>` `<head>` :

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* `<body>`Etiketin içeriğini aşağıdaki şekilde değiştirin:

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

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a>Identity Blazor Server Yetkilendirmeyle bir projeye yapı

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

Bazı Identity Seçenekler, *alanlarda/ Identity / Identity HostingStartup.cs*içinde yapılandırılır. Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a>Tam Identity UI kaynağı oluştur

Kullanıcı arabiriminin tam denetimini sürdürmek için Identity , Identity desteği ' ı çalıştırın ve **tüm dosyaları geçersiz kıl**' ı seçin.

Aşağıdaki vurgulanan kod, varsayılan Identity Kullanıcı arabirimini Identity ASP.NET Core 2,1 Web uygulamasında değiştirecek değişiklikleri gösterir. Bunu, Kullanıcı arabirimine tam denetim sağlamak için yapmak isteyebilirsiniz Identity .

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

Varsayılan değer Identity aşağıdaki kodda değiştirilmiştir:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Aşağıdaki kod, [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)ve [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)öğesini ayarlar:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Bir `IEmailSender` uygulamayı kaydedin, örneğin:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a>Parola Yapılandırması

' <xref:Microsoft.AspNetCore.Identity.PasswordOptions> De yapılandırılmışsa `Startup.ConfigureServices` , [ `[StringLength]` ](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` Yapı iskelesi sayfalarında özelliği için öznitelik yapılandırması gerekli olabilir Identity . `InputModel``Password`aşağıdaki dosyalarda özellikler bulunur:

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a>Sayfayı devre dışı bırakma

Bu bölümler, kaydetme sayfasının nasıl devre dışı bırakılacağını gösterir, ancak herhangi bir sayfayı devre dışı bırakmak için yaklaşım kullanılabilir.

Kullanıcı kaydını devre dışı bırakmak için:

* Yapı iskelesi Identity . Account. Register, Account. Login ve account. RegisterConfirmation bilgilerini ekleyin. Örnek:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Kullanıcıları bu uç noktadan kaydedememesi için *alanı/ Identity /Pages/Account/Register.cshtml.cs* güncelleştir:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Önceki değişikliklerle tutarlı olması için *Areas/ Identity /Pages/Account/Register.exe* öğesini güncelleştirin:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* */ Identity /Pages/Account/Login.exe* içindeki kayıt bağlantısını açıklama veya Kaldır

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* *Areas/ Identity /Pages/Account/registerconfirmation* sayfasını güncelleştirin.

  * Cshtml dosyasındaki kodu ve bağlantıları kaldırın.
  * Onay kodunu şuradan kaldırın `PageModel` :

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
  
### <a name="use-another-app-to-add-users"></a>Kullanıcıları eklemek için başka bir uygulama kullanma

Web uygulaması dışındaki kullanıcıları eklemek için bir mekanizma sağlar. Kullanıcı ekleme seçenekleri şunlardır:

* Adanmış bir yönetim Web uygulaması.
* Bir konsol uygulaması.

Aşağıdaki kod, kullanıcı eklemeye yönelik bir yaklaşımı özetler:

* Kullanıcıların listesi belleği okur.
* Her Kullanıcı için güçlü bir benzersiz parola oluşturulur.
* Kullanıcı Identity veritabanına eklenir.
* Kullanıcıya bildirilir ve parolayı değiştirmesi bildirilir.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

Aşağıdaki kod, bir kullanıcı ekleme ana hatlarıyla verilmiştir:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Üretim senaryolarında de benzer bir yaklaşım izlenebilir.

## <a name="prevent-publish-of-static-no-locidentity-assets"></a>Statik varlıkların yayımlanmasını Engelle Identity

Statik Identity varlıkların Web köküne yayımlanmasını engellemek için bkz <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> ..

## <a name="additional-resources"></a>Ek kaynaklar

* [ASP.NET Core 2,1 ve üzeri kimlik doğrulama kodundaki değişiklikler](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2,1 ve üzeri [ASP.NET Core Identity](xref:security/authentication/identity) bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak sunulmaktadır. Dahil edilen uygulamalar, Identity Identity Razor sınıf kitaplığı 'nda (RCL) bulunan kaynak kodu seçmeli olarak eklemek için desteği uygulayabilir. Kodu değiştirebilmeniz ve davranışı değiştirebilmek için kaynak kodu oluşturmak isteyebilirsiniz. Örneğin, kayıt sırasında kullanılan kodu oluşturmak için desteği ' ı söyleyebilirsiniz. Oluşturulan kod RCL 'deki aynı koda göre önceliklidir Identity . Kullanıcı arabirimine tam denetim sağlamak ve varsayılan RCL 'yi kullanmak için, [tam KIMLIK UI kaynağı oluşturma](#full)bölümüne bakın.

Kimlik **doğrulaması içermeyen uygulamalar** , RCL paketini eklemek için desteği uygulayabilir Identity . Oluşturulacak kodu seçme seçeneğiniz vardır Identity .

Desteği gerekli kodların çoğunu üretse de, işlemi gerçekleştirmek için projenizi güncelleştirmeniz gerekir. Bu belgede, bir yapı iskelesi güncelleştirmesini tamamlaması için gereken adımlar açıklanmaktadır Identity .

IdentityDesteği çalıştırıldığında, proje dizininde bir *ScaffoldingReadme.txt* dosyası oluşturulur. *ScaffoldingReadme.txt* dosyası, yapı iskelesi güncelleştirmesinin tamamlanabilmesi için gerekli olan genel yönergeleri içerir Identity . Bu belge, *ScaffoldingReadme.txt* dosyasından daha ayrıntılı yönergeler içerir.

Dosya farklılıklarını gösteren ve değişikliklerden geri dönüş yapmanızı sağlayan bir kaynak denetimi sistemi kullanmanızı öneririz. Scaffolder çalıştırıldıktan sonra değişiklikleri inceleyin Identity .

> [!NOTE]
> [Iki öğeli kimlik doğrulaması](xref:security/authentication/identity-enable-qrcodes), [Hesap onaylama ve parola kurtarma](xref:security/authentication/accconfirm)ile diğer güvenlik özellikleri kullanılırken hizmetler gereklidir Identity . Hizmetler veya hizmet saplamaları, yapı iskelesi sırasında oluşturulmaz Identity . Bu özelliklerin etkinleştirilmesi için hizmetlerin el ile eklenmesi gerekir. Örneğin, bkz. [e-posta onayı gerektir](xref:security/authentication/accconfirm#require-email-confirmation).

## <a name="scaffold-no-locidentity-into-an-empty-project"></a>IdentityBoş bir projeye yapı iskelesi

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Aşağıdaki Vurgulanan çağrıları `Startup` sınıfına ekleyin:

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a>Identity Razor Var olan yetkilendirme olmadan bir projeye yapı iskelesi yapın

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

Identity, *alanlarında/ Identity / Identity HostingStartup.cs*' de yapılandırılır. Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Geçişler, UseAuthentication ve düzen

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Kimlik doğrulamayı etkinleştir

`Configure` `Startup` Sınıfının yönteminde, sonrasında [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ' ı çağırın `UseStaticFiles` :

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Düzen değişiklikleri

İsteğe bağlı: () oturum açma bilgilerini `_LoginPartial` Düzen dosyasına ekleyin:

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a>Identity Razor Yetkilendirmeyle bir projeye yapı

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

Bazı Identity Seçenekler, *alanlarda/ Identity / Identity HostingStartup.cs*içinde yapılandırılır. Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a>IdentityVar olan yetkilendirme olmadan BIR MVC projesinde yapı iskelesi yapın

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

İsteğe bağlı: bir Login ( `_LoginPartial` ) öğesini *views/Shared/_Layout. cshtml* dosyasına ekleyin:

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* *Pages/Shared/_LoginPartial. cshtml* dosyasını *views/Shared/_LoginPartial. cshtml* olarak taşıyın

Identity, *alanlarında/ Identity / Identity HostingStartup.cs*' de yapılandırılır. Daha fazla bilgi için bkz. ıhostingstartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Şu tarihten sonra [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) çağrısı `UseStaticFiles` :

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a>IdentityYetkilendirme ile BIR MVC projesinde yapı iskelesi oluşturma

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

*Sayfaları/paylaşılan* klasörünü ve bu klasördeki dosyaları silin.

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a>Tam Identity UI kaynağı oluştur

Kullanıcı arabiriminin tam denetimini sürdürmek için Identity , Identity desteği ' ı çalıştırın ve **tüm dosyaları geçersiz kıl**' ı seçin.

Aşağıdaki vurgulanan kod, varsayılan Identity Kullanıcı arabirimini Identity ASP.NET Core 2,1 Web uygulamasında değiştirecek değişiklikleri gösterir. Bunu, Kullanıcı arabirimine tam denetim sağlamak için yapmak isteyebilirsiniz Identity .

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

Varsayılan değer Identity aşağıdaki kodda değiştirilmiştir:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

Aşağıdaki kod, [Loginpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [Logoutpath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)ve [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)öğesini ayarlar:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

Bir `IEmailSender` uygulamayı kaydedin, örneğin:

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a>Parola Yapılandırması

' <xref:Microsoft.AspNetCore.Identity.PasswordOptions> De yapılandırılmışsa `Startup.ConfigureServices` , [ `[StringLength]` ](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` Yapı iskelesi sayfalarında özelliği için öznitelik yapılandırması gerekli olabilir Identity . `InputModel``Password`aşağıdaki dosyalarda özellikler bulunur:

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a>Kayıt sayfasını devre dışı bırak

Kullanıcı kaydını devre dışı bırakmak için:

* Yapı iskelesi Identity . Account. Register, Account. Login ve account. RegisterConfirmation bilgilerini ekleyin. Örnek:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Kullanıcıları bu uç noktadan kaydedememesi için *alanı/ Identity /Pages/Account/Register.cshtml.cs* güncelleştir:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Önceki değişikliklerle tutarlı olması için *Areas/ Identity /Pages/Account/Register.exe* öğesini güncelleştirin:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* */ Identity /Pages/Account/Login.exe* içindeki kayıt bağlantısını açıklama veya Kaldır

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* *Areas/ Identity /Pages/Account/registerconfirmation* sayfasını güncelleştirin.

  * Cshtml dosyasındaki kodu ve bağlantıları kaldırın.
  * Onay kodunu şuradan kaldırın `PageModel` :

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
  
### <a name="use-another-app-to-add-users"></a>Kullanıcıları eklemek için başka bir uygulama kullanma

Web uygulaması dışındaki kullanıcıları eklemek için bir mekanizma sağlar. Kullanıcı ekleme seçenekleri şunlardır:

* Adanmış bir yönetim Web uygulaması.
* Bir konsol uygulaması.

Aşağıdaki kod, kullanıcı eklemeye yönelik bir yaklaşımı özetler:

* Kullanıcıların listesi belleği okur.
* Her Kullanıcı için güçlü bir benzersiz parola oluşturulur.
* Kullanıcı Identity veritabanına eklenir.
* Kullanıcıya bildirilir ve parolayı değiştirmesi bildirilir.

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

Aşağıdaki kod, bir kullanıcı ekleme ana hatlarıyla verilmiştir:

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

Üretim senaryolarında de benzer bir yaklaşım izlenebilir.

## <a name="additional-resources"></a>Ek kaynaklar

* [ASP.NET Core 2,1 ve üzeri kimlik doğrulama kodundaki değişiklikler](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
