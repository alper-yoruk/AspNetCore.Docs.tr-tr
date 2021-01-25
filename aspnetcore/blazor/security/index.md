---
title: ASP.NET Core Blazor kimlik doğrulaması ve yetkilendirme
author: guardrex
description: BlazorKimlik doğrulama ve yetkilendirme senaryoları hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/security/index
ms.openlocfilehash: 35620299b1138ca45a6ebf1d789a9f4aa9b29518
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751609"
---
# <a name="aspnet-core-no-locblazor-authentication-and-authorization"></a>ASP.NET Core Blazor kimlik doğrulaması ve yetkilendirme

Ve [Steve Sanderson](https://github.com/SteveSandersonMS) ve [Luke Latham](https://github.com/guardrex)

ASP.NET Core, uygulamalardaki güvenliğin yapılandırmasını ve yönetimini destekler Blazor .

Güvenlik senaryoları ve uygulamalar arasında farklılık gösterir Blazor Server Blazor WebAssembly . Blazor ServerUygulamalar sunucuda çalıştığı için, yetkilendirme denetimleri şunları tespit edebilir:

* Kullanıcıya sunulan kullanıcı ARABIRIMI seçenekleri (örneğin, bir kullanıcı için hangi menü girişlerinin kullanılabildiği).
* Uygulama ve bileşenlerin bölgeleri için erişim kuralları.

Blazor WebAssembly uygulamalar istemcide çalışır. Yetkilendirme *yalnızca* hangi kullanıcı arabirimi seçeneklerinin gösterileceğini belirlemede kullanılır. İstemci tarafı denetimleri bir kullanıcı tarafından değiştirililerek veya atlandığından, bir Blazor WebAssembly uygulama yetkilendirme erişim kurallarını zorunlu kılamaz.

[ Razor Sayfalar yetkilendirme kuralları](xref:security/authorization/razor-pages-authorization) yönlendirilebilir Razor bileşenlere uygulanmaz. Bir sayfada yönlendirilemeyen bir Razor bileşen [gömüliyorsa](xref:blazor/components/prerendering-and-integration), sayfanın yetkilendirme kuralları, Razor sayfanın geri kalanı ile birlikte, bileşeni dolaylı olarak etkiler.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Identity.SignInManager%601> ve <xref:Microsoft.AspNetCore.Identity.UserManager%601> Razor bileşenlerde desteklenmez.

## <a name="authentication"></a>Kimlik Doğrulaması

Blazor , kullanıcının kimliğini kurmak için mevcut ASP.NET Core kimlik doğrulama mekanizmalarını kullanır. Tam mekanizma, Blazor uygulamanın nasıl barındırıldığını Blazor WebAssembly veya Blazor Server .

### <a name="no-locblazor-webassembly-authentication"></a>Blazor WebAssembly yetkilendirmesi

Blazor WebAssemblyUygulamalarda, tüm istemci tarafı kodlar kullanıcılar tarafından değiştirilemediği için, kimlik doğrulama denetimleri atlanabilir. Aynı, JavaScript SPA çerçeveleri veya herhangi bir işletim sistemi için yerel uygulamalar dahil olmak üzere tüm istemci tarafı uygulama teknolojileri için de geçerlidir.

Aşağıdakileri ekleyin:

* Uygulamanın proje dosyasına yönelik bir paket başvurusu [`Microsoft.AspNetCore.Components.Authorization`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization) .
* `Microsoft.AspNetCore.Components.Authorization`Uygulama dosyasının ad alanı `_Imports.razor` .

Kimlik doğrulamasını işlemek için, yerleşik veya özel bir <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> hizmetin kullanımı aşağıdaki bölümlerde ele alınmıştır.

Uygulama ve yapılandırma oluşturma hakkında daha fazla bilgi için bkz <xref:blazor/security/webassembly/index> ..

### <a name="no-locblazor-server-authentication"></a>Blazor Server yetkilendirmesi

Blazor Server uygulamalar kullanılarak oluşturulan gerçek zamanlı bir bağlantı üzerinden çalışır SignalR . Bağlantı kurulurken [kimlik doğrulaması SignalR tabanlı uygulamalar](xref:signalr/authn-and-authz) işlenir. Kimlik doğrulaması bir cookie veya başka bir taşıyıcı belirtecini temel alabilir.

<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>Uygulamalar için yerleşik hizmet, Blazor Server ASP.NET Core 'dan kimlik doğrulama durumu verilerini alır `HttpContext.User` . Kimlik doğrulama durumu, mevcut ASP.NET Core kimlik doğrulama mekanizmalarıyla tümleştirilir.

Uygulama ve yapılandırma oluşturma hakkında daha fazla bilgi için bkz <xref:blazor/security/server/index> ..

## <a name="authenticationstateprovider-service"></a>AuthenticationStateProvider hizmeti

<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> , <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> bileşen ve <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> bileşen tarafından kimlik doğrulama durumunu almak için kullanılan temel hizmettir.

Genellikle doğrudan kullanmazsınız <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> . [ `AuthorizeView` ](#authorizeview-component) [`Task<AuthenticationState>`](#expose-the-authentication-state-as-a-cascading-parameter) Bu makalenin ilerleyen kısımlarında açıklanan bileşeni veya yaklaşımları kullanın. Doğrudan kullanmanın ana dezavantajı, <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> temeldeki kimlik doğrulama durumu verileri değişirse bileşen tarafından otomatik olarak bildirilmemektedir.

<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider>Hizmet, <xref:System.Security.Claims.ClaimsPrincipal> Aşağıdaki örnekte gösterildiği gibi geçerli kullanıcının verilerini sağlayabilir:

```razor
@page "/"
@using System.Security.Claims
@using Microsoft.AspNetCore.Components.Authorization
@inject AuthenticationStateProvider AuthenticationStateProvider

<h3>ClaimsPrincipal Data</h3>

<button @onclick="GetClaimsPrincipalData">Get ClaimsPrincipal Data</button>

<p>@_authMessage</p>

@if (_claims.Count() > 0)
{
    <ul>
        @foreach (var claim in _claims)
        {
            <li>@claim.Type: @claim.Value</li>
        }
    </ul>
}

<p>@_surnameMessage</p>

@code {
    private string _authMessage;
    private string _surnameMessage;
    private IEnumerable<Claim> _claims = Enumerable.Empty<Claim>();

    private async Task GetClaimsPrincipalData()
    {
        var authState = await AuthenticationStateProvider.GetAuthenticationStateAsync();
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
            _claims = user.Claims;
            _surnameMessage = 
                $"Surname: {user.FindFirst(c => c.Type == ClaimTypes.Surname)?.Value}";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

`user.Identity.IsAuthenticated`İse `true` ve Kullanıcı bir ise <xref:System.Security.Claims.ClaimsPrincipal> , talepler numaralandırılabilir ve değerlendirilen rollerde üyelik yapılabilir.

Bağımlılık ekleme (dı) ve hizmetleri hakkında daha fazla bilgi için bkz <xref:blazor/fundamentals/dependency-injection> <xref:fundamentals/dependency-injection> . ve.

## <a name="implement-a-custom-authenticationstateprovider"></a>Özel bir AuthenticationStateProvider uygulama

Uygulama için özel bir sağlayıcı gerekiyorsa, uygulayın <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> ve geçersiz kılın `GetAuthenticationStateAsync` :

```csharp
using System.Security.Claims;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Components.Authorization;

public class CustomAuthStateProvider : AuthenticationStateProvider
{
    public override Task<AuthenticationState> GetAuthenticationStateAsync()
    {
        var identity = new ClaimsIdentity(new[]
        {
            new Claim(ClaimTypes.Name, "mrfibuli"),
        }, "Fake authentication type");

        var user = new ClaimsPrincipal(identity);

        return Task.FromResult(new AuthenticationState(user));
    }
}
```

Bir Blazor WebAssembly uygulamada, `CustomAuthStateProvider` hizmet şu şekilde kaydedilir `Main` `Program.cs` :

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

Bir Blazor Server uygulamada, `CustomAuthStateProvider` hizmet şu şekilde kaydedilir `Startup.ConfigureServices` :

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

`CustomAuthStateProvider`Önceki örnekte kullanarak, tüm kullanıcıların Kullanıcı adı ile kimliği doğrulanır `mrfibuli` .

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Kimlik doğrulama durumunu basamaklı bir parametre olarak kullanıma sunma

Kullanıcı tarafından tetiklenen bir eylem gerçekleştirilirken gibi yordamsal mantık için kimlik doğrulama durumu verileri gerekliyse, türü geçişli bir parametre tanımlayarak kimlik doğrulama durumu verilerini alın `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` :

```razor
@page "/"

<button @onclick="LogUsername">Log username</button>

<p>@_authMessage</p>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private string _authMessage;

    private async Task LogUsername()
    {
        var authState = await authenticationStateTask;
        var user = authState.User;

        if (user.Identity.IsAuthenticated)
        {
            _authMessage = $"{user.Identity.Name} is authenticated.";
        }
        else
        {
            _authMessage = "The user is NOT authenticated.";
        }
    }
}
```

`user.Identity.IsAuthenticated`İse `true` , talepler, değerlendirilen roller içinde numaralandırılabilir ve üyelenebilir.

`Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> Bileşen () içindeki ve bileşenlerini kullanarak basamaklı parametreyi ayarlayın `App` `App.razor` :

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)" />
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

Bir Blazor WebAssembly uygulamada, Seçenekler ve yetkilendirme için Hizmetleri şu şekilde ekleyin `Program.Main` :

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

Bir Blazor Server uygulamada, Seçenekler ve yetkilendirme hizmetleri zaten mevcuttur, bu nedenle başka bir eylem gerekmez.

## <a name="authorization"></a>Yetkilendirme

Bir kullanıcının kimliği doğrulandıktan sonra, kullanıcının neler yapabileceğini denetlemek için *Yetkilendirme* kuralları uygulanır.

Erişim, genellikle aşağıdakileri yapıp verilmeksizin verilir veya reddedilir:

* Bir kullanıcının kimliği doğrulanır (oturum açıldı).
* Bir Kullanıcı bir *roldür*.
* Bir kullanıcının *talebi* vardır.
* Bir *ilke* karşılandı.

Bu kavramların her biri, ASP.NET Core MVC veya Pages uygulamasındaki ile aynıdır Razor . ASP.NET Core güvenliği hakkında daha fazla bilgi için [ASP.NET Core güvenlik ve Identity ](xref:security/index)altındaki makalelere bakın.

## <a name="authorizeview-component"></a>AuthorizeView bileşeni

<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>Bileşen Kullanıcı yetkinize bağlı olarak Kullanıcı arabirimi içeriğini seçmeli olarak görüntüler. Bu yaklaşım yalnızca Kullanıcı için veri *görüntülemesi* gerektiğinde ve Kullanıcı kimliğini yordamsal mantığda kullanmanıza gerek olmadığında yararlıdır.

Bileşeni, `context` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> oturum açmış kullanıcıyla ilgili bilgilere erişmek için kullanabileceğiniz türünde bir değişken kullanıma sunar:

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

Kullanıcı yetkilendirilmemişse, görüntülenmek üzere farklı içerikler de sağlayabilirsiniz:

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authorized.</p>
        <button @onclick="SecureMethod">Authorized Only Button</button>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>

@code {
    private void SecureMethod() { ... }
}
```

`<Authorized>`Ve `<NotAuthorized>` etiketlerinin içeriği, diğer etkileşimli bileşenler gibi rastgele öğeler içerebilir.

Bir yetkili öğe için, önceki örnekteki öğe yöntemi gibi varsayılan bir olay işleyicisi `SecureMethod` `<button>` yalnızca yetkili bir kullanıcı tarafından çağrılabilir.

Kullanıcı arabirimi seçeneklerini veya erişimini denetleyen roller veya ilkeler gibi yetkilendirme koşulları, [Yetkilendirme](#authorization) bölümünde ele alınmıştır.

Yetkilendirme koşulları belirtilmemişse, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> varsayılan bir ilke kullanır ve şu şekilde davranır:

* Kimliği doğrulanmış (oturum açmış) kullanıcılar yetkili olarak.
* Kimliği doğrulanmamış (oturumu açılmış) kullanıcılar yetkilendirilmemiş.

Bileşen, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> `NavMenu` `Shared/NavMenu.razor` bir bileşen () için bir liste öğesi () göstermek üzere bileşende () kullanılabilir `<li>...</li>` [ `NavLink` ](xref:blazor/fundamentals/routing#navlink-and-navmenu-components) <xref:Microsoft.AspNetCore.Components.Routing.NavLink> , ancak bu yaklaşımın yalnızca liste öğesini işlenen çıktıdan kaldırdığına unutmayın. Kullanıcının bileşene gitmesini engellemez.

### <a name="role-based-and-policy-based-authorization"></a>Rol tabanlı ve ilke tabanlı yetkilendirme

<xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView>Bileşen *rol tabanlı* veya *ilke tabanlı* yetkilendirmeyi destekler.

Rol tabanlı yetkilendirme için <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> parametresini kullanın:

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Daha fazla bilgi için bkz. <xref:security/authorization/roles>.

İlke tabanlı yetkilendirme için <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> parametresini kullanın:

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

Talep tabanlı yetkilendirme, ilke tabanlı yetkilendirme için özel bir durumdur. Örneğin, kullanıcıların belirli bir talebe sahip olmasını gerektiren bir ilke tanımlayabilirsiniz. Daha fazla bilgi için bkz. <xref:security/authorization/policies>.

Bu API 'Ler, ya da Blazor Server uygulamalarında kullanılabilir Blazor WebAssembly .

Ne <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Roles> de <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView.Policy> belirtilmemişse, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> varsayılan ilkeyi kullanır.

### <a name="content-displayed-during-asynchronous-authentication"></a>Zaman uyumsuz kimlik doğrulaması sırasında görünen içerik

Blazor kimlik doğrulaması durumunun *zaman uyumsuz* olarak belirlenmesine izin verir. Bu yaklaşım için birincil senaryo, Blazor WebAssembly kimlik doğrulaması için bir dış uç noktaya istek yapan uygulamalarda yer alan uygulamalardır.

Kimlik doğrulama devam ederken, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> Varsayılan olarak içerik görüntülemez. Kimlik doğrulama sırasında içeriği göstermek için `<Authorizing>` etiketini kullanın:

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <Authorizing>
        <h1>Authentication in progress</h1>
        <p>You can only see this content while authentication is in progress.</p>
    </Authorizing>
</AuthorizeView>
```

Bu yaklaşım normalde uygulamalar için geçerli değildir Blazor Server . Blazor Server uygulamalar, durum belirlenir oluşturmaz kimlik doğrulama durumunu bilir. <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeViewCore.Authorizing> içerik bir uygulamanın bileşeni içinde bulunabilir Blazor Server <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> , ancak içerik hiçbir şekilde gösterilmez.

## <a name="authorize-attribute"></a>[Yetkilendir] özniteliği

[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Özniteliği, Razor bileşenlerinde kullanılabilir:

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Yalnızca [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) `@page` yönlendirici üzerinden ulaşılan bileşenlerde kullanın Blazor . Yetkilendirme yalnızca, bir sayfada işlenen alt bileşenler için *değil* , yönlendirmenin bir yönü olarak gerçekleştirilir. Bir sayfa içindeki belirli parçaların görüntülenmesini yetkilendirmek için, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> bunun yerine kullanın.

[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Özniteliği rol tabanlı veya ilke tabanlı yetkilendirmeyi de destekler. Rol tabanlı yetkilendirme için <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles> parametresini kullanın:

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

İlke tabanlı yetkilendirme için <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> parametresini kullanın:

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

<xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Roles>Ya da <xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute.Policy> belirtilmemişse [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) varsayılan ilkeyi kullanır, varsayılan olarak şu şekilde değerlendirilir:

* Kimliği doğrulanmış (oturum açmış) kullanıcılar yetkili olarak.
* Kimliği doğrulanmamış (oturumu açılmış) kullanıcılar yetkilendirilmemiş.

## <a name="customize-unauthorized-content-with-the-router-component"></a>Yönlendirici bileşeniyle yetkisiz içeriği özelleştirme

Bileşen <xref:Microsoft.AspNetCore.Components.Routing.Router> ile birlikte bileşeni, uygulamanın şu <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> durumlarda özel içerik belirtmesini sağlar:

* İçerik bulunamadı.
* Kullanıcı, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) bileşene uygulanan bir koşulla başarısız olur. Özniteliği, [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) [ `[Authorize]` öznitelik](#authorize-attribute) bölümünde ele alınmıştır.
* Zaman uyumsuz kimlik doğrulama devam ediyor.

Varsayılan Blazor Server Proje şablonunda, `App` bileşen ( `App.razor` ) özel içeriği nasıl ayarlayabileceğinizi gösterir:

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        <Found Context="routeData">
            <AuthorizeRouteView RouteData="@routeData" 
                DefaultLayout="@typeof(MainLayout)">
                <NotAuthorized>
                    <h1>Sorry</h1>
                    <p>You're not authorized to reach this page.</p>
                    <p>You may need to log in as a different user.</p>
                </NotAuthorized>
                <Authorizing>
                    <h1>Authentication in progress</h1>
                    <p>Only visible while authentication is in progress.</p>
                </Authorizing>
            </AuthorizeRouteView>
        </Found>
        <NotFound>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </NotFound>
    </Router>
</CascadingAuthenticationState>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

`<NotFound>`, `<NotAuthorized>` Ve etiketlerinin içeriği, `<Authorizing>` diğer etkileşimli bileşenler gibi rastgele öğeler içerebilir.

`<NotAuthorized>`Etiket belirtilmemişse, <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeRouteView> aşağıdaki geri dönüş iletisini kullanır:

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Kimlik doğrulama durumu değişiklikleri hakkında bildirim

Uygulama, temeldeki kimlik doğrulama durumu verilerinin değiştiğini belirlerse (örneğin, Kullanıcı oturumu kapattığından veya başka bir kullanıcı rollerini değiştirse), [özel `AuthenticationStateProvider` ](#implement-a-custom-authenticationstateprovider) olarak <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider.NotifyAuthenticationStateChanged%2A> <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> temel sınıfta yöntemi çağırabilir. Bu, yeni verileri kullanarak yeniden kimlik doğrulama durumu verilerini (örneğin,) tüketicilere bildirir <xref:Microsoft.AspNetCore.Components.Authorization.AuthorizeView> .

## <a name="procedural-logic"></a>Yordamsal mantık

Uygulama, yordamsal mantığın bir parçası olarak yetkilendirme kurallarını denetmek için gerekliyse, `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` Kullanıcı tarafından elde etmek için türünde basamaklı bir parametre kullanın <xref:System.Security.Claims.ClaimsPrincipal> . `Task<`<xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState>`>``IAuthorizationService`, ilkeleri değerlendirmek için gibi diğer hizmetlerle birleştirilebilir.

```razor
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

<button @onclick="@DoSomething">Do something important</button>

@code {
    [CascadingParameter]
    private Task<AuthenticationState> authenticationStateTask { get; set; }

    private async Task DoSomething()
    {
        var user = (await authenticationStateTask).User;

        if (user.Identity.IsAuthenticated)
        {
            // Perform an action only available to authenticated (signed-in) users.
        }

        if (user.IsInRole("admin"))
        {
            // Perform an action only available to users in the 'admin' role.
        }

        if ((await AuthorizationService.AuthorizeAsync(user, "content-editor"))
            .Succeeded)
        {
            // Perform an action only available to users satisfying the 
            // 'content-editor' policy.
        }
    }
}
```

> [!NOTE]
> Bir Blazor WebAssembly uygulama bileşeninde, <xref:Microsoft.AspNetCore.Authorization> ve <xref:Microsoft.AspNetCore.Components.Authorization> ad alanlarını ekleyin:
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> Bu ad alanları, uygulamanın dosyasına eklenerek Global olarak temin edilebilir `_Imports.razor` .

## <a name="troubleshoot-errors"></a>Sorun giderme hataları

Sık karşılaşılan hatalar:

* **Yetkilendirme, türünde basamaklı bir parametre gerektirir `Task\<AuthenticationState>` . `CascadingAuthenticationState` Bunu sağlamak için kullanmayı düşünün.**

* **`null` için değer alındı `authenticationStateTask`**

Projenin kimlik doğrulaması etkin bir şablon kullanılarak oluşturulmamış olması olasıdır Blazor Server . `<CascadingAuthenticationState>`Kullanıcı arabirimi ağacının bir bölümünü (örneğin, `App` bileşen () içinde aşağıdaki gibi sarmalayın `App.razor` :

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="@typeof(Program).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

[!INCLUDE[](~/blazor/includes/prefer-exact-matches.md)]

, <xref:Microsoft.AspNetCore.Components.Authorization.CascadingAuthenticationState> `Task<` <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationState> `>` Temel alınan dı hizmetinden aldığı geçişli parametreyi sağlar <xref:Microsoft.AspNetCore.Components.Authorization.AuthenticationStateProvider> .

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/index>
* <xref:security/authentication/windowsauth>
* [Authentication. MSAL JavaScript kitaplığının özel bir sürümünü oluşturun](xref:blazor/security/webassembly/additional-scenarios#build-a-custom-version-of-the-authenticationmsal-javascript-library)
* [Başar Blazor : kimlik doğrulama](https://github.com/AdrienTorris/awesome-blazor#authentication) topluluğu örnek bağlantıları
