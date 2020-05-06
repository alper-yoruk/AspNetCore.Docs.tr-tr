---
title: Identity ASP.NET Core projelerinde yapı iskelesi
author: rick-anderson
description: ASP.NET Core projesindeki fkatlama Identity oluşturmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 6f1ff69863e14c73e90496ea61188387f5267b19
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768396"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a>Identity ASP.NET Core projelerinde yapı iskelesi

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core [bir Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core Identity ](xref:security/authentication/identity) sağlar. Dahil Identity edilen uygulamalar, Identity Razor sınıf kitaplığı 'nda (RCL) bulunan kaynak kodu seçmeli olarak eklemek için desteği uygulayabilir. Kodu değiştirebilmeniz ve davranışı değiştirebilmek için kaynak kodu oluşturmak isteyebilirsiniz. Örneğin, kayıt sırasında kullanılan kodu oluşturmak için desteği ' ı söyleyebilirsiniz. Oluşturulan kod Identity RCL 'deki aynı koda göre önceliklidir. Kullanıcı arabirimine tam denetim sağlamak ve varsayılan RCL 'yi kullanmak için, [tam KIMLIK UI kaynağı oluşturma](#full)bölümüne bakın.

Kimlik **doğrulaması içermeyen uygulamalar** , RCL Identity paketini eklemek için desteği uygulayabilir. Oluşturulacak kodu seçme Identity seçeneğiniz vardır.

Desteği gerekli kodların çoğunu üretse de, işlemi gerçekleştirmek için projenizi güncelleştirmeniz gerekir. Bu belgede, bir Identity yapı iskelesi güncelleştirmesini tamamlaması için gereken adımlar açıklanmaktadır.

Dosya farklılıklarını gösteren ve değişikliklerden geri dönüş yapmanızı sağlayan bir kaynak denetimi sistemi kullanmanızı öneririz. Identity Scaffolder çalıştırıldıktan sonra değişiklikleri inceleyin.

[Iki öğeli kimlik doğrulaması](xref:security/authentication/identity-enable-qrcodes), [Hesap onaylama ve parola kurtarma](xref:security/authentication/accconfirm)ile diğer güvenlik özellikleri kullanılırken hizmetler gereklidir Identity. Hizmetler veya hizmet saplamaları, yapı iskelesi Identitysırasında oluşturulmaz. Bu özelliklerin etkinleştirilmesi için hizmetlerin el ile eklenmesi gerekir. Örneğin, bkz. [e-posta onayı gerektir](xref:security/authentication/accconfirm#require-email-confirmation).

Yeni bir veri Identity bağlamıyla, var olan bireysel hesaplara sahip bir projeye dönüştürme yaparken:

* İçinde `Startup.ConfigureServices`, şu çağrıları kaldırın:
  * `AddDbContext`
  * `AddDefaultIdentity`

Örneğin, `AddDbContext` ve `AddDefaultIdentity` aşağıdaki kodda yorum yapılır:

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

Önceki kod, alanlarda çoğaltılan kodu Yorumlar *Identity//IdentityHostingStartup.cs*

Genellikle, bireysel hesaplarla oluşturulan uygulamalar yeni bir veri bağlamı ***oluşturmamalıdır*** .

## <a name="scaffold-identity-into-an-empty-project"></a>Boş bir projede yapı iskelesi kimliği

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

`Startup` Sınıfını aşağıdakine benzer kodla güncelleştirin:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Var olan yetkilendirme olmadan bir projede Razor kimlik oluşturma kimliği

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

Identity, */Identity/IdentityHostingStartup.cs alanlarında*yapılandırılır. daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Geçişler, UseAuthentication ve düzen

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Kimlik doğrulamasını etkinleştir

`Startup` Sınıfını aşağıdakine benzer kodla güncelleştirin:

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Düzen değişiklikleri

İsteğe bağlı: (`_LoginPartial`) oturum açma bilgilerini düzen dosyasına ekleyin:

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Yetkilendirmeyle kimliği bir Razor projede yapı iskelesi

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
Bazı Identity Seçenekler */Identity/IdentityHostingStartup.cs alanlarında*yapılandırılır. Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Var olan yetkilendirme olmadan bir MVC projesinde kimlik oluşturma kimliği

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

İsteğe bağlı: bir Login (`_LoginPartial`) öğesini *views/Shared/_Layout. cshtml* dosyasına ekleyin:

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* *Pages/Shared/_LoginPartial. cshtml* dosyasını *views/Shared/_LoginPartial. cshtml* olarak taşıyın

Identity, */Identity/IdentityHostingStartup.cs alanlarında*yapılandırılır. Daha fazla bilgi için bkz. ıhostingstartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

`Startup` Sınıfını aşağıdakine benzer kodla güncelleştirin:

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Yetkilendirmeyi içeren bir MVC projesinde kimlik oluşturma kimliği

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>Tam kimlik UI kaynağı oluşturma

Identity Kullanıcı arabiriminin tam denetimini sürdürmek için, Identity desteği ' ı çalıştırın ve **tüm dosyaları geçersiz kıl**' ı seçin.

Aşağıdaki vurgulanan kod, varsayılan Identity kullanıcı arabirimini ASP.NET Core 2,1 Web Identity uygulamasında değiştirecek değişiklikleri gösterir. Bunu, Identity Kullanıcı arabirimine tam denetim sağlamak için yapmak isteyebilirsiniz.

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
## <a name="disable-register-page"></a>Kayıt sayfasını devre dışı bırak

Kullanıcı kaydını devre dışı bırakmak için:

* Yapı iskelesi Identity. Account. Register, Account. Login ve account. RegisterConfirmation bilgilerini ekleyin. Örneğin:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Kullanıcıları bu uç noktadan kaydedememesi için *alanı/Identity/Pages/Account/Register.cshtml.cs* güncelleştir:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Önceki değişikliklerle tutarlı olması için *IdentityAreas//Pages/Account/Register.exe* öğesini güncelleştirin:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* *Identity//Pages/Account/Login.exe* içindeki kayıt bağlantısını açıklama veya Kaldır

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* *IdentityAreas//Pages/Account/registerconfirmation* sayfasını güncelleştirin.

  * Cshtml dosyasındaki kodu ve bağlantıları kaldırın.
  * Onay kodunu şuradan kaldırın `PageModel`:

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

## <a name="prevent-publish-of-static-identity-assets"></a>Statik Identity varlıkların yayımlanmasını engelle

Statik Identity varlıkların Web köküne yayımlanmasını engellemek için bkz <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>..

## <a name="additional-resources"></a>Ek kaynaklar

* [ASP.NET Core 2,1 ve üzeri kimlik doğrulama kodundaki değişiklikler](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2,1 ve üzeri bir [ Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core Identity ](xref:security/authentication/identity) sağlar. Dahil Identity edilen uygulamalar, Identity Razor sınıf kitaplığı 'nda (RCL) bulunan kaynak kodu seçmeli olarak eklemek için desteği uygulayabilir. Kodu değiştirebilmeniz ve davranışı değiştirebilmek için kaynak kodu oluşturmak isteyebilirsiniz. Örneğin, kayıt sırasında kullanılan kodu oluşturmak için desteği ' ı söyleyebilirsiniz. Oluşturulan kod Identity RCL 'deki aynı koda göre önceliklidir. Kullanıcı arabirimine tam denetim sağlamak ve varsayılan RCL 'yi kullanmak için, [tam KIMLIK UI kaynağı oluşturma](#full)bölümüne bakın.

Kimlik **doğrulaması içermeyen uygulamalar** , RCL Identity paketini eklemek için desteği uygulayabilir. Oluşturulacak kodu seçme Identity seçeneğiniz vardır.

Desteği gerekli kodların çoğunu üretse de, işlemi gerçekleştirmek için projenizi güncelleştirmeniz gerekir. Bu belgede, bir Identity yapı iskelesi güncelleştirmesini tamamlaması için gereken adımlar açıklanmaktadır.

Identity Desteği çalıştırıldığında, proje dizininde bir *scaffoldingreadme. txt* dosyası oluşturulur. *Scaffoldingreadme. txt* dosyası, Identity yapı iskelesi güncelleştirmesini tamamlamaya yönelik nelerin gerekli olduğuna ilişkin genel yönergeleri içerir. Bu belge, *Scaffoldingreadme. txt* dosyasından daha eksiksiz yönergeler içerir.

Dosya farklılıklarını gösteren ve değişikliklerden geri dönüş yapmanızı sağlayan bir kaynak denetimi sistemi kullanmanızı öneririz. Identity Scaffolder çalıştırıldıktan sonra değişiklikleri inceleyin.

> [!NOTE]
> [Iki öğeli kimlik doğrulaması](xref:security/authentication/identity-enable-qrcodes), [Hesap onaylama ve parola kurtarma](xref:security/authentication/accconfirm)ile diğer güvenlik özellikleri kullanılırken hizmetler gereklidir Identity. Hizmetler veya hizmet saplamaları, yapı iskelesi Identitysırasında oluşturulmaz. Bu özelliklerin etkinleştirilmesi için hizmetlerin el ile eklenmesi gerekir. Örneğin, bkz. [e-posta onayı gerektir](xref:security/authentication/accconfirm#require-email-confirmation).

## <a name="scaffold-identity-into-an-empty-project"></a>Boş bir projede yapı iskelesi kimliği

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Aşağıdaki Vurgulanan çağrıları `Startup` sınıfına ekleyin:

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>Var olan yetkilendirme olmadan bir projede Razor kimlik oluşturma kimliği

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

Identity, */Identity/IdentityHostingStartup.cs alanlarında*yapılandırılır. daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>Geçişler, UseAuthentication ve düzen

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>Kimlik doğrulamasını etkinleştir

`Startup` Sınıfının `Configure` yönteminde, sonrasında `UseStaticFiles` [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ' ı çağırın:

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>Düzen değişiklikleri

İsteğe bağlı: (`_LoginPartial`) oturum açma bilgilerini düzen dosyasına ekleyin:

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>Yetkilendirmeyle kimliği bir Razor projede yapı iskelesi

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
Bazı Identity Seçenekler */Identity/IdentityHostingStartup.cs alanlarında*yapılandırılır. Daha fazla bilgi için bkz. [ıhostingstartup](xref:fundamentals/configuration/platform-specific-configuration).

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>Var olan yetkilendirme olmadan bir MVC projesinde kimlik oluşturma kimliği

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

İsteğe bağlı: bir Login (`_LoginPartial`) öğesini *views/Shared/_Layout. cshtml* dosyasına ekleyin:

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* *Pages/Shared/_LoginPartial. cshtml* dosyasını *views/Shared/_LoginPartial. cshtml* olarak taşıyın

Identity, */Identity/IdentityHostingStartup.cs alanlarında*yapılandırılır. Daha fazla bilgi için bkz. ıhostingstartup.

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

Şu tarihten sonra `UseStaticFiles` [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) çağrısı:

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>Yetkilendirmeyi içeren bir MVC projesinde kimlik oluşturma kimliği

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

## <a name="create-full-identity-ui-source"></a>Tam kimlik UI kaynağı oluşturma

Identity Kullanıcı arabiriminin tam denetimini sürdürmek için, Identity desteği ' ı çalıştırın ve **tüm dosyaları geçersiz kıl**' ı seçin.

Aşağıdaki vurgulanan kod, varsayılan Identity kullanıcı arabirimini ASP.NET Core 2,1 Web Identity uygulamasında değiştirecek değişiklikleri gösterir. Bunu, Identity Kullanıcı arabirimine tam denetim sağlamak için yapmak isteyebilirsiniz.

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
## <a name="disable-register-page"></a>Kayıt sayfasını devre dışı bırak

Kullanıcı kaydını devre dışı bırakmak için:

* Yapı iskelesi Identity. Account. Register, Account. Login ve account. RegisterConfirmation bilgilerini ekleyin. Örneğin:

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* Kullanıcıları bu uç noktadan kaydedememesi için *alanı/Identity/Pages/Account/Register.cshtml.cs* güncelleştir:

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* Önceki değişikliklerle tutarlı olması için *IdentityAreas//Pages/Account/Register.exe* öğesini güncelleştirin:

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* *Identity//Pages/Account/Login.exe* içindeki kayıt bağlantısını açıklama veya Kaldır

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* *IdentityAreas//Pages/Account/registerconfirmation* sayfasını güncelleştirin.

  * Cshtml dosyasındaki kodu ve bağlantıları kaldırın.
  * Onay kodunu şuradan kaldırın `PageModel`:

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