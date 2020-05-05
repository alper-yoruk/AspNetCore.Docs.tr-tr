---
title: Identity ASP.NET Core giriş
author: rick-anderson
description: ASP.NET Core Identity bir uygulamayla kullanın. Parola gereksinimlerini (RequireDigit, RequiredLength, RequiredUniqueChars ve daha fazlasını) ayarlamayı öğrenin.
ms.author: riande
ms.date: 01/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: d596a8357c5c812b94950809eedf35718328747c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777013"
---
# <a name="introduction-to-identity-on-aspnet-core"></a>ASP.NET Core kimliğe giriş

::: moniker range=">= aspnetcore-3.0"

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core kimliği:

* , Kullanıcı arabirimi (UI) oturum açma işlevselliğini destekleyen bir API 'dir.
* Kullanıcıları, parolaları, profil verilerini, rolleri, talepleri, belirteçleri, e-posta onayını ve daha fazlasını yönetir.

Kullanıcılar, kimlik içinde depolanan oturum açma bilgilerini içeren bir hesap oluşturabilir veya bir dış oturum açma sağlayıcısı kullanabilirler. Desteklenen dış oturum açma sağlayıcıları [Facebook, Google, Microsoft hesabı ve Twitter](xref:security/authentication/social/index)içerir.

[Kimlik kaynak kodu](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) GitHub ' da kullanılabilir. Kimlik ile şablon etkileşimini gözden geçirmek için, [kimliği iskele](xref:security/authentication/scaffold-identity) geçirin ve oluşturulan dosyaları görüntüleyin.

Kimlik genellikle kullanıcı adlarını, parolaları ve profil verilerini depolamak için bir SQL Server veritabanı kullanılarak yapılandırılır. Alternatif olarak, başka bir kalıcı mağaza da kullanılabilir, örneğin Azure Tablo depolaması.

Bu konu başlığında, bir kullanıcıyı kaydetmek, oturum açmak ve oturumu kapatmak için kimlik kullanmayı öğrenirsiniz. Note: Şablonlar Kullanıcı adı ve e-postayı kullanıcılar için aynı olarak değerlendirir. Kimlik kullanan uygulamalar oluşturma hakkında daha ayrıntılı yönergeler için, bu makalenin sonundaki sonraki adımlar bölümüne bakın.

[Microsoft Identity platform](/azure/active-directory/develop/) :

* Azure Active Directory (Azure AD) geliştirici platformunun bir evrimi.
* ASP.NET Core kimlikle ilgisi yoktur.

[!INCLUDE[](~/includes/IdentityServer4.md)]

Örnek kodu ([indirme)](xref:index#how-to-download-a-sample) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) .

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a>Kimlik doğrulamasıyla bir Web uygulaması oluşturma

Bireysel kullanıcı hesaplarıyla bir ASP.NET Core Web uygulaması projesi oluşturun.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Dosya** > **Yeni** > **Proje**’yi seçin.
* **ASP.NET Core Web uygulaması**' nı seçin. Projeyi Proje **WebApp1** aynı ad alanına sahip olacak şekilde adlandırın. **Tamam**'a tıklayın.
* Bir ASP.NET Core **Web uygulaması**seçip **kimlik doğrulamasını Değiştir**' i seçin.
* **Bireysel kullanıcı hesapları** ' nı seçip **Tamam**' a tıklayın.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

Yukarıdaki komut, SQLite kullanarak bir Razor Web uygulaması oluşturur. LocalDB ile Web uygulaması oluşturmak için şu komutu çalıştırın:

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

Oluşturulan proje, [Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core kimliği](xref:security/authentication/identity) sağlar. Identity Razor sınıfı kitaplığı, `Identity` alanı ile uç noktaları kullanıma sunar. Örneğin:

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>Geçişleri Uygula

Veritabanını başlatmak için geçişleri uygulayın.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Paket Yöneticisi konsolunda aşağıdaki komutu çalıştırın (PMC):

`PM> Update-Database`

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

SQLite kullanılırken geçişler Bu adımda gerekli değildir. LocalDB için aşağıdaki komutu çalıştırın:

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Test kaydı ve oturum açma

Uygulamayı çalıştırın ve bir Kullanıcı kaydedin. Ekran boyutunuza bağlı olarak, **kayıt** ve **oturum açma** bağlantılarını görmek için gezinti geçiş düğmesini seçmeniz gerekebilir.

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Kimlik hizmetlerini yapılandırma

Hizmetler ' de `ConfigureServices`eklenir. Tipik model, tüm `Add{Service}` yöntemleri çağırmalıdır ve sonra tüm `services.Configure{Service}` yöntemleri çağırır.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

Önceki vurgulanan kod, varsayılan seçenek değerleriyle kimliği yapılandırır. Hizmetler, [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama için kullanılabilir hale getirilir.

Kimlik çağırarak <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>etkindir. `UseAuthentication`istek ardışık düzenine kimlik doğrulama [ara yazılımı](xref:fundamentals/middleware/index) ekler.

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

Şablon tarafından oluşturulan uygulama [Yetkilendirme](xref:security/authorization/secure-data)kullanmaz. `app.UseAuthorization`, uygulamanın yetkilendirme eklemesi için doğru sırada eklendiğinden emin olmak için dahil edilmiştir. `UseRouting``UseAuthorization` `UseEndpoints` , `UseAuthentication`, ve önceki kodda gösterilen sırada çağrılmalıdır.

Ve `IdentityOptions` `Startup`hakkında daha fazla bilgi için, <xref:Microsoft.AspNetCore.Identity.IdentityOptions> bkz. ve [uygulama başlatma](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Yapı iskelesi kaydı, oturum açma ve oturum kapatma

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Kayıt, oturum açma ve oturum kapatma dosyalarını ekleyin. Bu bölümde gösterilen kodu oluşturmak için, yetkilendirme yönergeleriyle [birlikte bir Razor projesinde yapı iskelesi kimliğini](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) izleyin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Projeyi **WebApp1**adıyla oluşturduysanız aşağıdaki komutları çalıştırın. Aksi takdirde, için doğru ad alanını kullanın `ApplicationDbContext`:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell, bir komut ayırıcısı olarak noktalı virgül kullanır. PowerShell kullanırken, dosya listesinde noktalı virgül karakterini kaçış veya dosya listesini, yukarıdaki örnekte gösterildiği gibi çift tırnak içine koyun.

Yapı iskelesi kimliği hakkında daha fazla bilgi için bkz. kimlik [doğrulama ile bir Razor projesinde yapı iskelesi kimliği](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).

---

### <a name="examine-register"></a>Kaydı İncele

Kullanıcı **Kaydet** bağlantısına tıkladığında `RegisterModel.OnPostAsync` eylem çağrılır. Kullanıcı, `_userManager` nesnesi üzerinde [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) tarafından oluşturulur. `_userManager`bağımlılık ekleme tarafından sağlanır):

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

Kullanıcı başarıyla oluşturulduysa, Kullanıcı çağrısıyla oturum açar `_signInManager.SignInAsync`.

Kayıt sırasında anında oturum açmayı önlemeye yönelik adımlar için bkz. [Hesap onayı](xref:security/authentication/accconfirm#prevent-login-at-registration) .

### <a name="log-in"></a>Oturum açma

Oturum açma formu şu durumlarda görüntülenir:

* **Oturum aç** bağlantısı seçilidir.
* Kullanıcı, erişim yetkisi olmayan **veya** sistem tarafından kimliği doğrulanmamış olan sınırlı bir sayfaya erişmeyi dener.

Oturum açma sayfasındaki form gönderildiğinde, `OnPostAsync` eylem çağrılır. `PasswordSignInAsync``_signInManager` nesnesi üzerinde çağrılır (bağımlılık ekleme tarafından sağlanır).

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Temel `Controller` sınıf, denetleyici yöntemlerinden `User` erişilebilen bir özellik sunar. Örneğin, yetkilendirme kararlarını numaralandırabilirsiniz `User.Claims` ve yapabilirsiniz. Daha fazla bilgi için bkz. <xref:security/authorization/introduction>.

### <a name="log-out"></a>Oturumu Kapat

**Oturumu kapatma** bağlantısı `LogoutModel.OnPost` eylemi çağırır. 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

Önceki kodda, tarayıcının yeni bir istek `return RedirectToPage();` gerçekleştirmesini ve Kullanıcı kimliğinin güncelleştirilmesini sağlamak için kodun yeniden yönlendirme olması gerekir.

[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) , kullanıcının tanımlama bilgisinde depolanan taleplerini temizler.

*Sayfa/paylaşılan/_LoginPartial. cshtml*'de gönderi belirtildi:

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a>Test kimliği

Varsayılan Web projesi şablonları, giriş sayfalarına anonim erişime izin verir. Kimliği test etmek için şunu [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)ekleyin:

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

Oturumunuz açık ise oturumu kapatın. Uygulamayı çalıştırın ve **Gizlilik** bağlantısını seçin. Oturum açma sayfasına yönlendirilirsiniz.

### <a name="explore-identity"></a>Kimliği keşfet

Kimliği daha ayrıntılı incelemek için:

* [Tam kimlik UI kaynağı oluşturma](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Her sayfanın kaynağını inceleyin ve hata ayıklayıcıda ilerleyin.

## <a name="identity-components"></a>Kimlik bileşenleri

Tüm kimlik bağımlı NuGet paketleri [ASP.NET Core paylaşılan çerçevesine](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)dahildir.

Kimliğin birincil paketi [Microsoft. AspNetCore. Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)' dır. Bu paket, ASP.NET Core kimliği için çekirdek arabirim kümesini içerir ve tarafından `Microsoft.AspNetCore.Identity.EntityFrameworkCore`dahildir.

## <a name="migrating-to-aspnet-core-identity"></a>ASP.NET Core kimliğe geçiriliyor

Mevcut kimlik deponuzu geçirme hakkında daha fazla bilgi ve yönergeler için bkz. [kimlik doğrulama ve kimlik geçişi](xref:migration/identity).

## <a name="setting-password-strength"></a>Parola gücünü ayarlama

Minimum parola gereksinimlerini ayarlayan bir örnek için bkz. [yapılandırma](#pw) .

## <a name="adddefaultidentity-and-addidentity"></a>Adddefaultıdentity ve AddEntity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>ASP.NET Core 2,1 ' de tanıtılmıştı. Çağırma `AddDefaultIdentity` , aşağıdakileri çağırmaya benzer:

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

Daha fazla bilgi için bkz. [Adddefaultıdentity kaynağı](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .

## <a name="prevent-publish-of-static-identity-assets"></a>Statik kimlik varlıklarının yayımlanmasını engelle

Statik kimlik varlıklarının (kimlik Kullanıcı arabirimi için stil sayfaları ve JavaScript dosyaları) Web köküne yayımlanmasını engellemek için aşağıdaki `ResolveStaticWebAssetsInputsDependsOn` özelliği ve `RemoveIdentityAssets` hedefi uygulamanın proje dosyasına ekleyin:

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

## <a name="next-steps"></a>Sonraki Adımlar

* SQLite kullanarak kimlik yapılandırma hakkında bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/5131) bakın.
* [Kimliği Yapılandırma](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core kimlik, ASP.NET Core uygulamalara oturum açma işlevselliği ekleyen bir üyelik sistemidir. Kullanıcılar, kimlik içinde depolanan oturum açma bilgilerini içeren bir hesap oluşturabilir veya bir dış oturum açma sağlayıcısı kullanabilirler. Desteklenen dış oturum açma sağlayıcıları [Facebook, Google, Microsoft hesabı ve Twitter](xref:security/authentication/social/index)içerir.

Kimlik, Kullanıcı adlarını, parolaları ve profil verilerini depolamak için bir SQL Server veritabanı kullanılarak yapılandırılabilir. Alternatif olarak, başka bir kalıcı mağaza da kullanılabilir, örneğin Azure Tablo depolaması.

Örnek kodu ([indirme)](xref:index#how-to-download-a-sample) [görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) .

Bu konu başlığında, bir kullanıcıyı kaydetmek, oturum açmak ve oturumu kapatmak için kimlik kullanmayı öğrenirsiniz. Kimlik kullanan uygulamalar oluşturma hakkında daha ayrıntılı yönergeler için, bu makalenin sonundaki sonraki adımlar bölümüne bakın.

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a>Adddefaultıdentity ve AddEntity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>ASP.NET Core 2,1 ' de tanıtılmıştı. Çağırma `AddDefaultIdentity` , aşağıdakileri çağırmaya benzer:

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

Daha fazla bilgi için bkz. [Adddefaultıdentity kaynağı](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) .

## <a name="create-a-web-app-with-authentication"></a>Kimlik doğrulamasıyla bir Web uygulaması oluşturma

Bireysel kullanıcı hesaplarıyla bir ASP.NET Core Web uygulaması projesi oluşturun.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* **Dosya** > **Yeni** > **Proje**’yi seçin.
* **ASP.NET Core Web uygulaması**' nı seçin. Projeyi Proje **WebApp1** aynı ad alanına sahip olacak şekilde adlandırın. **Tamam**'a tıklayın.
* Bir ASP.NET Core **Web uygulaması**seçip **kimlik doğrulamasını Değiştir**' i seçin.
* **Bireysel kullanıcı hesapları** ' nı seçip **Tamam**' a tıklayın.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

Oluşturulan proje, [Razor sınıf kitaplığı](xref:razor-pages/ui-class)olarak [ASP.NET Core kimliği](xref:security/authentication/identity) sağlar. Identity Razor sınıfı kitaplığı, `Identity` alanı ile uç noktaları kullanıma sunar. Örneğin:

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>Geçişleri Uygula

Veritabanını başlatmak için geçişleri uygulayın.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Paket Yöneticisi konsolunda aşağıdaki komutu çalıştırın (PMC):

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>Test kaydı ve oturum açma

Uygulamayı çalıştırın ve bir Kullanıcı kaydedin. Ekran boyutunuza bağlı olarak, **kayıt** ve **oturum açma** bağlantılarını görmek için gezinti geçiş düğmesini seçmeniz gerekebilir.

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>Kimlik hizmetlerini yapılandırma

Hizmetler ' de `ConfigureServices`eklenir. Tipik model, tüm `Add{Service}` yöntemleri çağırmalıdır ve sonra tüm `services.Configure{Service}` yöntemleri çağırır.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

Yukarıdaki kod, varsayılan seçenek değerleriyle kimliği yapılandırır. Hizmetler, [bağımlılık ekleme](xref:fundamentals/dependency-injection)yoluyla uygulama için kullanılabilir hale getirilir.

Kimlik, [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)çağırarak etkinleştirilir. `UseAuthentication`istek ardışık düzenine kimlik doğrulama [ara yazılımı](xref:fundamentals/middleware/index) ekler.

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

Daha fazla bilgi için bkz. [ıdentityoptions sınıfı](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) ve [uygulama başlatma](xref:fundamentals/startup).

## <a name="scaffold-register-login-and-logout"></a>Yapı iskelesi kaydı, oturum açma ve oturum kapatma

Bu bölümde gösterilen kodu oluşturmak için, yetkilendirme yönergeleriyle [birlikte bir Razor projesinde yapı iskelesi kimliğini](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) izleyin.

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

Kayıt, oturum açma ve oturum kapatma dosyalarını ekleyin.

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

Projeyi **WebApp1**adıyla oluşturduysanız aşağıdaki komutları çalıştırın. Aksi takdirde, için doğru ad alanını kullanın `ApplicationDbContext`:

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell, bir komut ayırıcısı olarak noktalı virgül kullanır. PowerShell kullanırken, dosya listesinde noktalı virgül karakterini kaçış veya dosya listesini, yukarıdaki örnekte gösterildiği gibi çift tırnak içine koyun.

---

### <a name="examine-register"></a>Kaydı İncele

Kullanıcı **Kaydet** bağlantısına tıkladığında `RegisterModel.OnPostAsync` eylem çağrılır. Kullanıcı, `_userManager` nesnesi üzerinde [createasync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) tarafından oluşturulur. `_userManager`bağımlılık ekleme tarafından sağlanır):

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

Kullanıcı başarıyla oluşturulduysa, Kullanıcı çağrısıyla oturum açar `_signInManager.SignInAsync`.

**Note:** Kayıt sırasında anında oturum açmayı önlemeye yönelik adımlar için bkz. [Hesap onayı](xref:security/authentication/accconfirm#prevent-login-at-registration) .

### <a name="log-in"></a>Oturum açma

Oturum açma formu şu durumlarda görüntülenir:

* **Oturum aç** bağlantısı seçilidir.
* Kullanıcı, erişim yetkisi olmayan **veya** sistem tarafından kimliği doğrulanmamış olan sınırlı bir sayfaya erişmeyi dener.

Oturum açma sayfasındaki form gönderildiğinde, `OnPostAsync` eylem çağrılır. `PasswordSignInAsync``_signInManager` nesnesi üzerinde çağrılır (bağımlılık ekleme tarafından sağlanır).

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Temel `Controller` sınıf, denetleyici yöntemlerinden `User` erişebileceğiniz bir özellik sunar. Örneğin, yetkilendirme kararlarını numaralandırabilirsiniz `User.Claims` ve yapabilirsiniz. Daha fazla bilgi için bkz. <xref:security/authorization/introduction>.

### <a name="log-out"></a>Oturumu Kapat

**Oturumu kapatma** bağlantısı `LogoutModel.OnPost` eylemi çağırır. 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[Signoutasync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) , kullanıcının tanımlama bilgisinde depolanan taleplerini temizler.

*Sayfa/paylaşılan/_LoginPartial. cshtml*'de gönderi belirtildi:

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a>SınamanızIdentity

Varsayılan Web projesi şablonları, giriş sayfalarına anonim erişime izin verir. Test Identityetmek için Gizlilik [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) sayfasına ekleyin.

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

Oturumunuz açık ise oturumu kapatın. Uygulamayı çalıştırın ve **Gizlilik** bağlantısını seçin. Oturum açma sayfasına yönlendirilirsiniz.

### <a name="explore-identity"></a>ExplorerIdentity

Daha ayrıntılı Identity incelemek için:

* [Tam kimlik UI kaynağı oluşturma](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* Her sayfanın kaynağını inceleyin ve hata ayıklayıcıda ilerleyin.

## <a name="identity-components"></a>IdentityBileşenleri

Tüm Identity bağımlı NuGet paketleri [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)'e dahildir.

İçin Identity birincil paket [Microsoft. aspnetcore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).. Bu paket, ASP.NET Core Identityiçin temel arabirim kümesini içerir ve tarafından `Microsoft.AspNetCore.Identity.EntityFrameworkCore`dahildir.

## <a name="migrating-to-aspnet-core-identity"></a>ASP.NET Core geçiriliyorIdentity

Mevcut Identity deponuzu geçirme hakkında daha fazla bilgi ve yönergeler için bkz. [geçiş kimlik Identitydoğrulaması ve ](xref:migration/identity).

## <a name="setting-password-strength"></a>Parola gücünü ayarlama

Minimum parola gereksinimlerini ayarlayan bir örnek için bkz. [yapılandırma](#pw) .

## <a name="next-steps"></a>Sonraki Adımlar

* SQLite kullanarak yapılandırma Identity hakkında bilgi Için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/5131) bakın.
* [YapılandırmaIdentity](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
