---
title: ASP.NET Core için özel depolama sağlayıcılarıIdentity
author: ardalis
description: ASP.NET Core Identityiçin özel depolama sağlayıcıları yapılandırma hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 07/23/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: c33267350f8f6b47f3ba649e96efd3d29fb116be
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775238"
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a>ASP.NET Core için özel depolama sağlayıcılarıIdentity

[Steve Smith](https://ardalis.com/) tarafından

ASP.NET Core Identity , özel bir depolama sağlayıcısı oluşturup uygulamanıza bağlayabilmenizi sağlayan genişletilebilir bir sistemdir. Bu konuda, ASP.NET Core Identityiçin özelleştirilmiş bir depolama sağlayıcısının nasıl oluşturulacağı açıklanmaktadır. Kendi depolama sağlayıcınızı oluşturmaya yönelik önemli kavramları ele alır, ancak adım adım bir adım adım değildir.

[GitHub 'dan örnek görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample).

## <a name="introduction"></a>Giriş

Varsayılan olarak, ASP.NET Core Identity sistem kullanıcı bilgilerini Entity Framework Core kullanarak SQL Server veritabanında depolar. Birçok uygulama için bu yaklaşım iyi bir sonuç verir. Ancak, farklı bir Kalıcılık mekanizması veya veri şeması kullanmayı tercih edebilirsiniz. Örneğin:

* [Azure Tablo depolama](/azure/storage/) veya başka bir veri deposu kullanıyorsunuz.
* Veritabanı tablolarınız farklı bir yapıya sahip. 
* [Kaber](https://github.com/StackExchange/Dapper)gibi farklı bir veri erişimi yaklaşımı kullanmak isteyebilirsiniz. 

Bu durumların her birinde, depolama mekanizmanız için özelleştirilmiş bir sağlayıcı yazabilir ve bu sağlayıcıyı uygulamanıza takabilirsiniz.

ASP.NET Core Identity , Visual Studio 'daki proje şablonlarına "bireysel kullanıcı hesapları" seçeneği ile dahildir.

.NET Core CLI kullanırken şunu ekleyin `-au Individual`:

```dotnetcli
dotnet new mvc -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a>ASP.NET Core Identity mimarisi

ASP.NET Core Identity , Yöneticiler ve depolar adlı sınıflardan oluşur. *Yöneticiler* , bir uygulama geliştiricisinin Identity Kullanıcı oluşturma gibi işlemleri gerçekleştirmek için kullandığı üst düzey sınıflardır. *Depolar* , kullanıcılar ve roller gibi varlıkların nasıl kalıcı olduğunu belirten alt düzey sınıflardır. Depolar depo modelini izler ve kalıcılık mekanizmasıyla yakından ilişkilidir. Yöneticiler mağazalardan ayrılır, bu da uygulama kodunuzu değiştirmeden (yapılandırma dışında) Kalıcılık mekanizmasını değiştirebilirsiniz.

Aşağıdaki diyagramda, bir Web uygulamasının yöneticileriyle nasıl etkileşim kurduğu, mağazalarda veri erişim katmanıyla etkileşim kurma yöntemi gösterilmektedir.

![ASP.NET Core uygulamalar yöneticileriyle çalışır (örneğin, ' UserManager ', ' RoleManager '). Yöneticiler, Entity Framework Core gibi bir kitaplık kullanarak bir veri kaynağıyla iletişim kuran depolarla (örneğin, ' UserStore ') çalışır.](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

Özel bir depolama sağlayıcısı oluşturmak için veri kaynağını, veri erişim katmanını ve bu veri erişim katmanıyla etkileşime geçen mağaza sınıflarını oluşturun (Yukarıdaki diyagramda yeşil ve gri kutular). Yöneticileri veya bunlarla etkileşim kuran uygulama kodunuzu özelleştirmeniz gerekmez (Yukarıdaki mavi kutular).

Veya `UserManager` `RoleManager` ' nin yeni bir örneğini oluştururken, Kullanıcı sınıfının türünü sağlarsınız ve depo sınıfının bir örneğini bir bağımsız değişken olarak geçitirsiniz. Bu yaklaşım özelleştirilmiş sınıflarınızı ASP.NET Core eklemenize olanak sağlar. 

[Uygulamayı yeni depolama sağlayıcısını kullanacak şekilde yeniden yapılandırmak](#reconfigure-app-to-use-a-new-storage-provider) , özelleştirilmiş bir `UserManager` deponun `RoleManager` örneğini oluşturmayı ve bunu gösterir.

## <a name="aspnet-core-identity-stores-data-types"></a>Veri Identity türlerini ASP.NET Core depolar

[ASP.NET Core Identity ](https://github.com/aspnet/identity) veri türleri aşağıdaki bölümlerde ayrıntılı olarak verilmiştir:

### <a name="users"></a>Kullanıcılar

Web sitenizin kayıtlı kullanıcıları. [Identityuser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser) türü, kendi özel türü için bir örnek olarak genişletilebilir veya kullanılıyor olabilir. Kendi özel kimlik depolama çözümünüzü uygulamak için belirli bir türden devralma gerekmez.

### <a name="user-claims"></a>Kullanıcı talepleri

Kullanıcının kimliğini temsil eden kullanıcı hakkındaki deyimler (veya [talepler](/dotnet/api/system.security.claims.claim)) kümesi. Kullanıcı kimliğinin, roller aracılığıyla elde edilebileceğinden daha fazla ifadesini etkinleştirebilir.

### <a name="user-logins"></a>Kullanıcı oturumu açma

Bir kullanıcıya oturum açarken kullanmak üzere dış kimlik doğrulama sağlayıcısı (Facebook veya bir Microsoft hesabı gibi) hakkında bilgiler. [Örneğinde](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a>Roller

Sitenizin yetkilendirme grupları. Rol kimliği ve rol adı ("admin" veya "Employee" gibi) içerir. [Örneğinde](/dotnet/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a>Veri erişim katmanı

Bu konuda, kullanacağınız Kalıcılık mekanizması hakkında bilgi sahibi olduğunuz ve bu mekanizma için nasıl varlık oluşturacağınız varsayılmaktadır. Bu konu, depoları veya veri erişim sınıflarını oluşturma hakkında ayrıntılı bilgi sağlamaz; ASP.NET Core Identityçalışırken tasarım kararları hakkında bazı öneriler sağlar.

Özelleştirilmiş bir mağaza sağlayıcısı için veri erişim katmanını tasarlarken çok fazla özgürlük vardır. Yalnızca uygulamanızda kullanmayı düşündüğünüz özellikler için kalıcılık mekanizmaları oluşturmanız gerekir. Örneğin, uygulamanızda roller kullanmıyorsanız, roller veya Kullanıcı rolü ilişkilendirmeleri için depolama alanı oluşturmanız gerekmez. Teknolojiniz ve mevcut altyapınız, ASP.NET Core Identityvarsayılan uygulamasından çok farklı bir yapı gerektirebilir. Veri erişim katmanınızdaki depolama uygulamanızın yapısıyla çalışma mantığını sağlarsınız.

Veri erişim katmanı, verileri ASP.NET Core Identity bir veri kaynağına kaydetme mantığını sağlar. Özelleştirilmiş depolama sağlayıcınızla ilgili veri erişim katmanı, Kullanıcı ve rol bilgilerini depolamak için aşağıdaki sınıfları içerebilir.

### <a name="context-class"></a>Bağlam sınıfı

Kalıcılık mekanizmanıza bağlanmak ve sorguları yürütmek için bilgileri kapsüller. Birçok veri sınıfı, genellikle bağımlılık ekleme yoluyla sağlanmış olan bu sınıfın bir örneğini gerektirir. [Örnek](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1).

### <a name="user-storage"></a>Kullanıcı depolaması

Kullanıcı bilgilerini depolar ve alır (Kullanıcı adı ve parola karması gibi). [Örneğinde](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a>Rol depolama

Rol bilgilerini depolar ve alır (rol adı gibi). [Örneğinde](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a>Userclaim depolaması

Kullanıcı talep bilgilerini depolar ve alır (talep türü ve değeri gibi). [Örneğinde](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a>UserLogins depolaması

Kullanıcı oturum açma bilgilerini depolar ve alır (örneğin, bir dış kimlik doğrulama sağlayıcısı). [Örneğinde](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a>UserRole depolaması

Hangi rollerin hangi kullanıcılara atandığını depolar ve alır. [Örneğinde](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

**İpucu:** Yalnızca uygulamanızda kullanmayı düşündüğünüz sınıfları uygulayın.

Veri erişim sınıflarında, kalıcılık mekanizmanız için veri işlemlerini gerçekleştirmek üzere kod sağlayın. Örneğin, özel bir sağlayıcı içinde, *Mağaza* sınıfında yeni bir kullanıcı oluşturmak için aşağıdaki koda sahip olabilirsiniz:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

Kullanıcı oluşturmaya yönelik uygulama mantığı aşağıda gösterildiği `_usersTable.CreateAsync` yöntemdir.

## <a name="customize-the-user-class"></a>Kullanıcı sınıfını özelleştirme

Bir depolama sağlayıcısı uygularken, [ıdentityuser sınıfına](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser)eşdeğer bir Kullanıcı sınıfı oluşturun.

En azından, Kullanıcı sınıfınızın bir `Id` ve bir `UserName` özelliği içermesi gerekir.

`IdentityUser` Sınıfı, istenen işlemleri gerçekleştirirken `UserManager` çağrıların özelliklerini tanımlar. `Id` Özelliğin varsayılan türü bir dizedir, ancak öğesinden `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` kalıtımla alabilir ve farklı bir tür belirtebilirsiniz. Çerçeve, depolama uygulamasının veri türü dönüştürmelerini işlemesini bekler.

## <a name="customize-the-user-store"></a>Kullanıcı deposunu özelleştirme

Kullanıcı üzerindeki `UserStore` tüm veri işlemlerine yönelik yöntemleri sağlayan bir sınıf oluşturun. Bu sınıf, [userStore&lt;Tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) sınıfına eşdeğerdir. Sınıfınıza `UserStore` `IUserStore<TUser>` ve isteğe bağlı arabirimler gereklidir. Uygulamanızda belirtilen işlevlere göre hangi isteğe bağlı arabirimlerin uygulanacağını seçersiniz.

### <a name="optional-interfaces"></a>İsteğe bağlı arabirimler

* [Iuserrolestore](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)
* [Iuserclaimstore](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)
* [Iuserpasswordstore](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)
* [Iusersecuritystampstore](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)
* [Iuseremailstore](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)
* [Iuserphonenumberstore](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)
* [Iqueryableuserstore](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)
* [Iuserloginstore](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)
* [Iusertwofactorstore](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)
* [Iuserlockoutstore](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)

İsteğe bağlı arabirimler öğesinden `IUserStore<TUser>`devralınır. [Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs)kısmen uygulanmış bir örnek kullanıcı deposu görebilirsiniz.

`UserStore` Sınıfı içinde, işlemleri gerçekleştirmek için oluşturduğunuz veri erişim sınıflarını kullanırsınız. Bunlar bağımlılık ekleme kullanılarak geçirilir. Örneğin, Davber uygulamasıyla SQL Server, `UserStore` sınıfı yeni bir kayıt eklemek `CreateAsync` `DapperUsersTable` için örneğini kullanan yöntemine sahiptir:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a>Kullanıcı deposunu özelleştirirken uygulanacak arabirimler

* **Iuserstore**  
 [Iuserstore&lt;&gt; Tuser](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1) arabirimi, kullanıcı deposunda uygulamanız gereken tek arabirimdir. Kullanıcıları oluşturma, güncelleştirme, silme ve alma yöntemlerini tanımlar.
* **Iuserclaimstore**  
 [Iuserclaimstore&lt;Tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1) arabirimi, Kullanıcı taleplerini etkinleştirmek için uyguladığınız yöntemleri tanımlar. Kullanıcı taleplerini ekleme, kaldırma ve alma yöntemlerini içerir.
* **Iuserloginstore**  
 [Iuserloginstore&lt;Tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1) , dış kimlik doğrulama sağlayıcılarını etkinleştirmek için uyguladığınız yöntemleri tanımlar. Kullanıcı oturum açma bilgilerini ekleme, kaldırma ve alma ve oturum açma bilgilerine göre Kullanıcı alma yöntemi gibi yöntemleri içerir.
* **Iuserrolestore**  
 [Iuserrolestore&lt;Tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1) arabirimi, bir kullanıcıyı bir rolle eşlemek için uyguladığınız yöntemleri tanımlar. Bir kullanıcının rollerini ekleme, kaldırma ve alma yöntemlerini ve bir rolün bir rolün atanıp atanmadığını denetlemek için bir yöntem içerir.
* **Iuserpasswordstore**  
 [Iuserpasswordstore&lt;Tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) arabirimi, Karma parolaları kalıcı hale getirmek için uyguladığınız yöntemleri tanımlar. Karma parolanın alınması ve ayarlanması için yöntemler ve kullanıcının bir parola ayarlayıp ayarlamadığını belirten bir yöntem içerir.
* **Iusersecuritystampstore**  
 [&lt;Iusersecuritystampstore Tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) arabirimi, kullanıcının hesap bilgilerinin değişip değişmediğini belirten bir güvenlik damgası kullanmak için uyguladığınız yöntemleri tanımlar. Bu damga, Kullanıcı parolayı değiştirdiğinde veya oturum açma ekler veya kaldırdığında güncelleştirilir. Güvenlik damgasını alma ve ayarlama yöntemlerini içerir.
* **Iusertwofactorstore**  
 [&lt;Iusertwofactorstore Tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) arabirimi iki öğeli kimlik doğrulamayı desteklemek için uyguladığınız yöntemleri tanımlar. Bir kullanıcı için iki öğeli kimlik doğrulamasının etkin olup olmadığını alma ve ayarlama yöntemlerini içerir.
* **Iuserphonenumberstore**  
 [Iuserphonenumberstore&lt;Tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) arabirimi, Kullanıcı telefon numaralarını depolamak için uyguladığınız yöntemleri tanımlar. Telefon numarasını alma ve ayarlama ve telefon numarasının onaylanıp onaylanmayacağı yöntemlerini içerir.
* **Iuseremailstore**  
 [Iuseremailstore&lt;Tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1) arabirimi, kullanıcı e-posta adreslerini depolamak için uyguladığınız yöntemleri tanımlar. E-posta adresini alma ve ayarlama yöntemlerini ve e-postanın onaylanıp onaylanmadığını içerir.
* **Iuserlockoutstore**  
 [Iuserlockoutstore&lt;Tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) arabirimi, bir hesabı kilitleme hakkındaki bilgileri depolamak için uyguladığınız yöntemleri tanımlar. Başarısız erişim girişimlerini ve kilitleme işlemleri izlemek için yöntemler içerir.
* **Iqueryableuserstore**  
 [Iqueryableuserstore&lt;Tuser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) arabirimi, bir sorgulanabilir kullanıcı deposu sağlamak için uyguladığınız üyeleri tanımlar.

Yalnızca uygulamanızda gerekli olan arabirimleri uygulayabilirsiniz. Örneğin:

```csharp
public class UserStore : IUserStore<IdentityUser>,
                         IUserClaimStore<IdentityUser>,
                         IUserLoginStore<IdentityUser>,
                         IUserRoleStore<IdentityUser>,
                         IUserPasswordStore<IdentityUser>,
                         IUserSecurityStampStore<IdentityUser>
{
    // interface implementations not shown
}
```

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a>Identityuserclaim, ıdentityuserlogin ve ıdentityuserrole

`Microsoft.AspNet.Identity.EntityFramework` Ad alanı [ıdentityuserclaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [ıdentityuserlogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)ve [ıdentityuserrole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) sınıflarının uygulamalarını içerir. Bu özellikleri kullanıyorsanız, bu sınıfların kendi sürümlerinizi oluşturmak ve uygulamanızın özelliklerini tanımlamak isteyebilirsiniz. Ancak bazen, temel işlemleri gerçekleştirirken bu varlıkların belleğe yüklenmemesinin (örneğin, bir kullanıcının talebini ekleme veya kaldırma) daha etkilidir. Bunun yerine, arka uç deposu sınıfları bu işlemleri doğrudan veri kaynağında yürütebilir. Örneğin, `UserStore.GetClaimsAsync` yöntemi bu tablo üzerinde doğrudan bir `userClaimTable.FindByUserId(user.Id)` sorgu yürütmek ve talepler listesini döndürmek için yöntemini çağırabilir.

## <a name="customize-the-role-class"></a>Rol sınıfını özelleştirme

Rol depolama sağlayıcısı uygularken özel bir rol türü oluşturabilirsiniz. Belirli bir arabirim gerçekleştirmemelidir, ancak bir özelliği olmalıdır `Id` ve genellikle bir `Name` özelliğine sahip olur.

Aşağıda örnek bir rol sınıfı verilmiştir:

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a>Rol deposunu özelleştirme

Rollerdeki tüm veri `RoleStore` işlemleri için yöntemler sağlayan bir sınıf oluşturabilirsiniz. Bu sınıf [rolestore&lt;trole&gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) sınıfına eşdeğerdir. `RoleStore` Sınıfında, `IRoleStore<TRole>` ve isteğe bağlı olarak `IQueryableRoleStore<TRole>` arabirimini uygulayacağınızı görürsünüz.

* **Irolestore&lt;trole&gt;**  
 [Irolestore&lt;&gt; trole](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1) arabirimi, rol deposu sınıfında uygulanacak yöntemleri tanımlar. Rol oluşturma, güncelleştirme, silme ve alma yöntemlerini içerir.
* **RoleStore&lt;trole&gt;**  
 Özelleştirmek `RoleStore`için, `IRoleStore<TRole>` arabirimini uygulayan bir sınıf oluşturun. 

## <a name="reconfigure-app-to-use-a-new-storage-provider"></a>Yeni bir depolama sağlayıcısını kullanmak için uygulamayı yeniden yapılandırın

Bir depolama sağlayıcısı uygulandıktan sonra uygulamanızı kullanacak şekilde yapılandırırsınız. Uygulamanız varsayılan sağlayıcıyı kullandıysanız, bunu özel sağlayıcınızla değiştirin.

1. `Microsoft.AspNetCore.EntityFramework.Identity` NuGet paketini kaldırın.
1. Depolama sağlayıcısı ayrı bir projede veya pakette bulunuyorsa buna bir başvuru ekleyin.
1. Tüm başvuruları `Microsoft.AspNetCore.EntityFramework.Identity` , depolama sağlayıcınızın ad alanı için bir using ifadesiyle değiştirin.
1. `ConfigureServices` Yönteminde, özel türlerinizi kullanmak için `AddIdentity` yöntemini değiştirin. Bu amaçla kendi genişletme yöntemlerinizi oluşturabilirsiniz. Bir örnek için bkz. [IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) .
1. Roller kullanıyorsanız, `RoleManager` `RoleStore` sınıfınızı kullanmak için ' i güncelleştirin.
1. Bağlantı dizesini ve kimlik bilgilerini uygulamanızın yapılandırmasına güncelleştirin.

Örnek:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add identity types
    services.AddIdentity<ApplicationUser, ApplicationRole>()
        .AddDefaultTokenProviders();

    // Identity Services
    services.AddTransient<IUserStore<ApplicationUser>, CustomUserStore>();
    services.AddTransient<IRoleStore<ApplicationRole>, CustomRoleStore>();
    string connectionString = Configuration.GetConnectionString("DefaultConnection");
    services.AddTransient<SqlConnection>(e => new SqlConnection(connectionString));
    services.AddTransient<DapperUsersTable>();

    // additional configuration
}
```

## <a name="references"></a>Başvurular

* [ASP.NET 4. x için özel depolama sağlayıcılarıIdentity](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
* Bu depo [ASP.NET Core Identity ](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) &ndash; , topluluk tarafından tutulan depo sağlayıcılarının bağlantılarını içerir.
