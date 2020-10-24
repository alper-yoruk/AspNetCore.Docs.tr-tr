---
title: ASP.NET Core kimlik doğrulaması ve yetkilendirme SignalR
author: bradygaster
description: ASP.NET Core 'da kimlik doğrulama ve yetkilendirmeyi nasıl kullanacağınızı öğrenin SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/05/2019
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
uid: signalr/authn-and-authz
ms.openlocfilehash: e16efa59a82d0f3cb1a2272ae0c07654ebec6a51
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491567"
---
# <a name="authentication-and-authorization-in-aspnet-core-no-locsignalr"></a>ASP.NET Core kimlik doğrulaması ve yetkilendirme SignalR

, [Andrew Stanton-nurte](https://twitter.com/anurse)

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/signalr/authn-and-authz/sample/) [(nasıl indirileceği)](xref:index#how-to-download-a-sample)

## <a name="authenticate-users-connecting-to-a-no-locsignalr-hub"></a>Bir hub 'a bağlanan kullanıcıların kimliğini doğrulama SignalR

SignalR , bir kullanıcıyı her bağlantıyla ilişkilendirmek için [ASP.NET Core kimlik doğrulamasıyla](xref:security/authentication/identity) birlikte kullanılabilir. Bir hub 'da, [Hubconnectioncontext. User](/dotnet/api/microsoft.aspnetcore.signalr.hubconnectioncontext.user) özelliğinden kimlik doğrulama verilerine erişilebilir. Kimlik doğrulaması, hub 'ın bir kullanıcıyla ilişkili tüm bağlantılar üzerinde Yöntemler çağırmasını sağlar. Daha fazla bilgi için bkz. [kullanıcıları ve grupları ' SignalR de yönetme ](xref:signalr/groups). Birden çok bağlantı tek bir kullanıcıyla ilişkilendirilebilir.

Aşağıda, `Startup.Configure` ASP.NET Core kimlik doğrulaması kullanan bir örnek verilmiştir SignalR :

::: moniker range=">= aspnetcore-3.0"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseRouting();

    app.UseAuthentication();
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chat");
        endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

```csharp
public void Configure(IApplicationBuilder app)
{
    ...

    app.UseStaticFiles();

    app.UseAuthentication();

    app.UseSignalR(hubs =>
    {
        hubs.MapHub<ChatHub>("/chat");
    });

    app.UseMvc(routes =>
    {
        routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
    });
}
```

> [!NOTE]
> SignalRVe ASP.NET Core kimlik doğrulama ara yazılımını kaydetme sırası önemli. `UseAuthentication` `UseSignalR` SignalR Üzerinde bir kullanıcı olması için önce her zaman ' i çağırın `HttpContext` .

::: moniker-end

### <a name="no-loccookie-authentication"></a>Cookie yetkilendirmesi

Tarayıcı tabanlı bir uygulamada, cookie kimlik doğrulama mevcut kullanıcı kimlik bilgilerinizin otomatik olarak bağlantılara akmasını sağlar SignalR . Tarayıcı istemcisi kullanılırken ek yapılandırma gerekmez. Kullanıcı uygulamanızda oturum açtıysa, SignalR bağlantı bu kimlik doğrulamasını otomatik olarak devralır.

Cookie, erişim belirteçleri göndermek için tarayıcıya özgü bir yoldur, ancak tarayıcı olmayan istemciler bu uygulamaları gönderebilir. [.NET istemcisi](xref:signalr/dotnet-client)kullanılırken, `Cookies` özelliği `.WithUrl` sağlamak için çağrısında yapılandırılabilir cookie . Ancak, cookie .net istemcisinden kimlik doğrulaması kullanmak, uygulamanın bir için kimlik doğrulama verilerini Exchange için BIR API sağlamasını gerektirir cookie .

### <a name="bearer-token-authentication"></a>Taşıyıcı belirteç kimlik doğrulaması

İstemci, kullanmak yerine bir erişim belirteci sağlayabilir cookie . Sunucu belirteci doğrular ve kullanıcıyı tanımlamak için kullanır. Bu doğrulama yalnızca bağlantı kurulduunda yapılır. Bağlantının kullanım ömrü boyunca, sunucu otomatik olarak yeniden doğrulamadan belirteç iptalini kontrol etmez.

JavaScript istemcisinde, belirteç [Accesstokenfactory](xref:signalr/configuration#configure-bearer-authentication) seçeneği kullanılarak sağlanabilirler.

[!code-typescript[Configure Access Token](authn-and-authz/sample/wwwroot/js/chat.ts?range=52-55)]

.NET istemcisinde, belirteci yapılandırmak için kullanılabilecek, benzer bir [Accesstokenprovider](xref:signalr/configuration#configure-bearer-authentication) özelliği vardır:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    { 
        options.AccessTokenProvider = () => Task.FromResult(_myAccessToken);
    })
    .Build();
```

> [!NOTE]
> Sağladığınız erişim belirteci işlevi, tarafından yapılan **her** http isteğinin önüne çağırılır SignalR . Bağlantıyı etkin tutmak için belirteci yenilemeniz gerekiyorsa (bağlantı sırasında süresi dolarsa), bunu bu işlevin içinden yapın ve güncelleştirilmiş belirteci döndürün.

Standart Web API 'Lerinde, taşıyıcı belirteçleri bir HTTP üst bilgisinde gönderilir. Ancak, SignalR bazı aktarımlar kullanılırken tarayıcılarda bu üst bilgileri ayarlayamamalıdır. WebSockets ve Server-Sent olaylarını kullanırken, belirteç bir sorgu dizesi parametresi olarak iletilir. 

#### <a name="built-in-jwt-authentication"></a>Yerleşik JWT kimlik doğrulaması

Sunucusunda, taşıyıcı belirteç kimlik doğrulaması [JWT taşıyıcı ara yazılımı](xref:Microsoft.Extensions.DependencyInjection.JwtBearerExtensions.AddJwtBearer%2A)kullanılarak yapılandırılır:

[!code-csharp[Configure Server to accept access token from Query String](authn-and-authz/sample/Startup.cs?name=snippet)]

[!INCLUDE[request localized comments](~/includes/code-comments-loc.md)]

> [!NOTE]
> Sorgu dizesi tarayıcı API 'SI sınırlamaları nedeniyle WebSockets ve Server-Sent olaylarıyla bağlantı kurulurken tarayıcılarda kullanılır. HTTPS kullanılırken sorgu dizesi değerleri TLS bağlantısıyla korunmuş hale getirilir. Ancak, birçok sunucu günlük sorgu dizesi değerlerini günlüğe kaydeder. Daha fazla bilgi için [ SignalR ASP.NET Core güvenlik konuları ](xref:signalr/security)bölümüne bakın. SignalR , bunları destekleyen ortamlarda (.NET ve Java istemcileri gibi) belirteçleri iletmek için üst bilgileri kullanır.

#### <a name="no-locidentity-server-jwt-authentication"></a>Identity Sunucu JWT kimlik doğrulaması

Sunucu kullanırken Identity , <xref:Microsoft.Extensions.Options.PostConfigureOptions%601> projeye bir hizmet ekleyin:

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.Extensions.Options;
public class ConfigureJwtBearerOptions : IPostConfigureOptions<JwtBearerOptions>
{
    public void PostConfigure(string name, JwtBearerOptions options)
    {
        var originalOnMessageReceived = options.Events.OnMessageReceived;
        options.Events.OnMessageReceived = async context =>
        {
            await originalOnMessageReceived(context);
                
            if (string.IsNullOrEmpty(context.Token))
            {
                var accessToken = context.Request.Query["access_token"];
                var path = context.HttpContext.Request.Path;
                
                if (!string.IsNullOrEmpty(accessToken) && 
                    path.StartsWithSegments("/hubs"))
                {
                    context.Token = accessToken;
                }
            }
        };
    }
}
```

`Startup.ConfigureServices`Kimlik doğrulaması () için Hizmetler <xref:Microsoft.Extensions.DependencyInjection.AuthenticationServiceCollectionExtensions.AddAuthentication%2A> ve sunucu için kimlik doğrulama işleyicisi () eklendikten sonra hizmeti kaydetme Identity <xref:Microsoft.AspNetCore.Authentication.AuthenticationBuilderExtensions.AddIdentityServerJwt%2A> :

```csharp
services.AddAuthentication()
    .AddIdentityServerJwt();
services.TryAddEnumerable(
    ServiceDescriptor.Singleton<IPostConfigureOptions<JwtBearerOptions>, 
        ConfigureJwtBearerOptions>());
```

### <a name="no-loccookies-vs-bearer-tokens"></a>Cookies ile taşıyıcı belirteçleri karşılaştırması 

Cookie, tarayıcılara özeldir. Diğer istemci türlerinden gönderilmesi, taşıyıcı belirteçlerinin gönderilmesine kıyasla karmaşıklık ekler. Sonuç olarak, cookie uygulamanın yalnızca tarayıcı istemcisinden kullanıcıların kimliğini doğrulaması gerekmediği takdirde kimlik doğrulaması önerilmez. Taşıyıcı belirteç kimlik doğrulaması, tarayıcı istemcisi dışındaki istemcileri kullanırken önerilen yaklaşımdır.

### <a name="windows-authentication"></a>Windows kimlik doğrulaması

Uygulamanızda [Windows kimlik doğrulaması](xref:security/authentication/windowsauth) yapılandırıldıysa, SignalR hub 'ları güvenli hale getirmek için bu kimliği kullanabilir. Ancak, bireysel kullanıcılara ileti göndermek için özel bir kullanıcı KIMLIĞI sağlayıcısı eklemeniz gerekir. Windows kimlik doğrulama sistemi, "ad tanımlayıcı" talebi sağlamaz. SignalR Kullanıcı adını öğrenmek için talebi kullanır.

`IUserIdProvider`Kullanıcı tarafından tanımlayıcı olarak kullanılacak taleplerden birini uygulayan ve alan yeni bir sınıf ekleyin. Örneğin, "ad" talebini (formdaki Windows Kullanıcı adı) kullanmak için `[Domain]\[Username]` aşağıdaki sınıfı oluşturun:

[!code-csharp[Name based provider](authn-and-authz/sample/nameuseridprovider.cs?name=NameUserIdProvider)]

Yerine, `ClaimTypes.Name` herhangi bir değeri `User` (örneğin, Windows SID tanımlayıcısı, vb.) kullanabilirsiniz.

> [!NOTE]
> Seçtiğiniz değer, sisteminizdeki tüm kullanıcılar arasında benzersiz olmalıdır. Aksi takdirde, bir kullanıcı için tasarlanan bir ileti farklı bir kullanıcıya gidiyor olabilir.

Bu bileşeni yönteminizin içine kaydedin `Startup.ConfigureServices` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services.AddSignalR();
    services.AddSingleton<IUserIdProvider, NameUserIdProvider>();
}
```

.NET Istemcisinde, [UseDefaultCredentials](/dotnet/api/microsoft.aspnetcore.http.connections.client.httpconnectionoptions.usedefaultcredentials) özelliği ayarlanarak Windows kimlik doğrulamasının etkinleştirilmesi gerekir:

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options =>
    {
        options.UseDefaultCredentials = true;
    })
    .Build();
```

Windows kimlik doğrulaması, Internet Explorer ve Microsoft Edge 'de desteklenir, ancak tüm tarayıcılarda desteklenmez. Örneğin, Chrome ve Safari 'de, Windows kimlik doğrulaması ve WebSockets kullanma girişimi başarısız olur. Windows kimlik doğrulaması başarısız olduğunda, istemci, işe gerekebilecek diğer aktarımlara geri dönmesine çalışır.

### <a name="use-claims-to-customize-identity-handling"></a>Kimlik işlemeyi özelleştirmek için talepler kullanma

Kullanıcıların kimliğini doğrulayan bir uygulama, Kullanıcı SignalR taleplerinden Kullanıcı kimliklerini türetebilir. SignalRKullanıcı kimliklerini nasıl oluşturduğunu belirtmek için, `IUserIdProvider` uygulamayı uygulayın ve kaydedin.

Örnek kod, tanımlama özelliği olarak kullanıcının e-posta adresini seçmek için talepleri nasıl kullanacağınızı gösterir. 

> [!NOTE]
> Seçtiğiniz değer, sisteminizdeki tüm kullanıcılar arasında benzersiz olmalıdır. Aksi takdirde, bir kullanıcı için tasarlanan bir ileti farklı bir kullanıcıya gidiyor olabilir.

[!code-csharp[Email provider](authn-and-authz/sample/EmailBasedUserIdProvider.cs?name=EmailBasedUserIdProvider)]

Hesap kaydı, ASP.NET Identity veritabanına türünde bir talep ekler `ClaimsTypes.Email` .

[!code-csharp[Adding the email to the ASP.NET identity claims](authn-and-authz/sample/pages/account/Register.cshtml.cs?name=AddEmailClaim)]

Bu bileşeni ' ye kaydedin `Startup.ConfigureServices` .

```csharp
services.AddSingleton<IUserIdProvider, EmailBasedUserIdProvider>();
```

## <a name="authorize-users-to-access-hubs-and-hub-methods"></a>Kullanıcılara, hub 'lara ve hub yöntemlerine erişim yetkisi verme

Varsayılan olarak, bir hub 'daki tüm yöntemler kimliği doğrulanmamış bir kullanıcı tarafından çağrılabilir. Kimlik doğrulaması gerektirmek için, [Yetkilendir](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) özniteliğini hub 'a uygulayın:

[!code-csharp[Restrict a hub to only authorized users](authn-and-authz/sample/Hubs/ChatHub.cs?range=8-10,32)]

`[Authorize]`Yalnızca belirli [Yetkilendirme ilkeleriyle](xref:security/authorization/policies)eşleşen kullanıcılara erişimi kısıtlamak için özniteliğinin Oluşturucu bağımsız değişkenlerini ve özelliklerini kullanabilirsiniz. Örneğin, adlı bir özel yetkilendirme ilkeniz varsa, `MyAuthorizationPolicy` aşağıdaki kodu kullanarak yalnızca bu ilkeyle eşleşen kullanıcıların hub 'a erişmesini sağlayabilirsiniz:

```csharp
[Authorize("MyAuthorizationPolicy")]
public class ChatHub : Hub
{
}
```

Tek bir hub yöntemi `[Authorize]` özniteliğe de uygulanmış olabilir. Geçerli Kullanıcı, yönteme uygulanan ilkeyle eşleşmezse, çağırana bir hata döndürülür:

```csharp
[Authorize]
public class ChatHub : Hub
{
    public async Task Send(string message)
    {
        // ... send a message to all users ...
    }

    [Authorize("Administrators")]
    public void BanUser(string userName)
    {
        // ... ban a user from the chat room (something only Administrators can do) ...
    }
}
```

::: moniker range=">= aspnetcore-3.0"

### <a name="use-authorization-handlers-to-customize-hub-method-authorization"></a>Hub yöntemi yetkilendirmesini özelleştirmek için yetkilendirme işleyicilerini kullanma

SignalR bir hub yöntemi yetkilendirme gerektirdiğinde, yetkilendirme işleyicilerine özel bir kaynak sağlar. Kaynak bir örneğidir `HubInvocationContext` . , `HubInvocationContext` `HubCallerContext` Çağrılan hub yönteminin adını ve hub yönteminin bağımsız değişkenlerini içerir.

Azure Active Directory aracılığıyla birden çok kuruluşun oturum açmasına izin veren bir sohbet odası örneğini göz önünde bulundurun. Microsoft hesabı olan herkes sohbet için oturum açabilir, ancak yalnızca sahip olunan kuruluşun üyeleri kullanıcıları yasaklatabilecek veya kullanıcıların sohbet geçmişlerini görüntüleyebilmelidir. Ayrıca, belirli kullanıcılardan belirli işlevleri kısıtlamak isteyebilirsiniz. ASP.NET Core 3,0 ' deki güncelleştirilmiş özellikleri kullanarak bu tamamen mümkündür. Nasıl `DomainRestrictedRequirement` özel olarak işlev gördüğüne göz önünde kalın `IAuthorizationRequirement` . `HubInvocationContext`Kaynak parametresi geçirilmeye hazır olduğuna göre, iç mantık hub 'ın çağrıldığı bağlamı inceleyebilir ve kullanıcının bireysel hub yöntemlerini yürütmesine izin verirken kararlar verebilir.

```csharp
[Authorize]
public class ChatHub : Hub
{
    public void SendMessage(string message)
    {
    }

    [Authorize("DomainRestricted")]
    public void BanUser(string username)
    {
    }

    [Authorize("DomainRestricted")]
    public void ViewUserHistory(string username)
    {
    }
}

public class DomainRestrictedRequirement : 
    AuthorizationHandler<DomainRestrictedRequirement, HubInvocationContext>, 
    IAuthorizationRequirement
{
    protected override Task HandleRequirementAsync(AuthorizationHandlerContext context,
        DomainRestrictedRequirement requirement, 
        HubInvocationContext resource)
    {
        if (IsUserAllowedToDoThis(resource.HubMethodName, context.User.Identity.Name) && 
            context.User.Identity.Name.EndsWith("@microsoft.com"))
        {
            context.Succeed(requirement);
        }
        return Task.CompletedTask;
    }

    private bool IsUserAllowedToDoThis(string hubMethodName,
        string currentUsername)
    {
        return !(currentUsername.Equals("asdf42@microsoft.com") && 
            hubMethodName.Equals("banUser", StringComparison.OrdinalIgnoreCase));
    }
}
```

İçinde `Startup.ConfigureServices` , `DomainRestrictedRequirement` ilkeyi oluşturmak için özel gereksinimi bir parametre olarak sağlayarak yeni ilkeyi ekleyin `DomainRestricted` .

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ... other services ...

    services
        .AddAuthorization(options =>
        {
            options.AddPolicy("DomainRestricted", policy =>
            {
                policy.Requirements.Add(new DomainRestrictedRequirement());
            });
        });
}
```

Yukarıdaki örnekte, `DomainRestrictedRequirement` sınıfı, `IAuthorizationRequirement` Bu gereksinim için hem bir ve kendisi olur `AuthorizationHandler` . Bu iki bileşeni birbirinden ayrı ayrı sınıflara bölmek kabul edilebilir. Örneğin yaklaşımın bir avantajı, `AuthorizationHandler` gereksinim ve işleyicinin aynı şey olduğu için başlangıç sırasında ekleme zorunluluktur.

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* [ASP.NET Core 'de taşıyıcı belirteç kimlik doğrulaması](https://blogs.msdn.microsoft.com/webdev/2016/10/27/bearer-token-authentication-in-asp-net-core/)
* [Kaynak tabanlı yetkilendirme](xref:security/authorization/resourcebased)
