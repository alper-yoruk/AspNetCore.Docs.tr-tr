---
title: ASP.NET Blazor Çekirdek kimlik doğrulaması ve yetkilendirme
author: guardrex
description: Kimlik Blazor doğrulama ve yetkilendirme senaryoları hakkında bilgi edinin.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/index
ms.openlocfilehash: 04bbf20d1d848edfa98e719f316b790c812bfd95
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80501321"
---
# <a name="aspnet-core-opno-locblazor-authentication-and-authorization"></a>ASP.NET Blazor Çekirdek kimlik doğrulaması ve yetkilendirme

Yazar: [Steve Sanderson](https://github.com/SteveSandersonMS) ve [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

> [!NOTE]
> WebAssembly için geçerli olan bu Blazor makaledeki kılavuz Blazor için, ASP.NET Core WebAssembly şablonu sürüm 3.2 veya sonraki gereklidir. Visual Studio sürüm 16.6 Preview 2 veya sonraki sürümlerini Blazor kullanmıyorsanız, 'deki <xref:blazor/get-started>kılavuzu izleyerek en son WebAssembly şablonuna kavuşun.

ASP.NET Core, uygulamalardaki Blazor güvenliğin yapılandırmasını ve yönetimini destekler.

Güvenlik senaryoları Blazor Sunucu Blazor ve WebAssembly uygulamaları arasında farklılık gösterir. Blazor Sunucu uygulamaları sunucuda çalıştığı için, yetkilendirme denetimleri aşağıdakileri belirleyebilir:

* Kullanıcıya sunulan Kullanıcı Arabirimi seçenekleri (örneğin, hangi menü girişlerinin bir kullanıcı tarafından kullanılabilir).
* Uygulama nın ve bileşenlerin alanları için erişim kuralları.

BlazorWebAssembly uygulamaları istemcide çalışır. Yetkilendirme *yalnızca* hangi Kullanıcı Arama Birimi seçeneklerinin gösterilen belirlemek için kullanılır. İstemci tarafı denetimleri bir kullanıcı tarafından değiştirilebildiği veya atlanabildiği için, Bir Blazor WebAssembly uygulaması yetkilendirme erişim kurallarını uygulayamaz.

[Razor Pages yetkilendirme kuralları](xref:security/authorization/razor-pages-authorization) routable Razor bileşenleri için geçerli değildir. Bir [sayfaya routable](xref:blazor/integrate-components#render-components-from-a-page-or-view)olmayan bir Razor bileşeni gömülüyse, sayfanın yetkilendirme kuralları sayfanın içeriğinin geri kalanıyla birlikte Razor bileşenini dolaylı olarak etkiler.

> [!NOTE]
> <xref:Microsoft.AspNetCore.Identity.SignInManager%601>ve <xref:Microsoft.AspNetCore.Identity.UserManager%601> Razor bileşenleri desteklenmez.

## <a name="authentication"></a>Kimlik Doğrulaması

Blazorkullanıcının kimliğini oluşturmak için varolan ASP.NET Core kimlik doğrulama mekanizmalarını kullanır. Tam mekanizma, uygulamanın Blazor nasıl barındırılan, Blazor Blazor WebAssembly veya Sunucu'ya bağlıdır.

### <a name="opno-locblazor-webassembly-authentication"></a>BlazorWebAssembly kimlik doğrulaması

WebAssembly uygulamalarında Blazor kimlik doğrulama denetimleri atlanabilir, çünkü tüm istemci tarafı kodu kullanıcılar tarafından değiştirilebilir. Aynı durum, javascript SPA çerçeveleri veya herhangi bir işletim sistemi için yerel uygulamalar da dahil olmak üzere tüm istemci tarafındaki uygulama teknolojileri için de geçerlidir.

Aşağıdakileri ekleyin:

* [Microsoft.AspNetCore.Components.Authorization](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Authorization/) için uygulamanın proje dosyasına bir paket başvurusu.
* Uygulamanın `Microsoft.AspNetCore.Components.Authorization` *_Imports.jilet* dosyasının ad alanı.

Kimlik doğrulamayı işlemek için, yerleşik veya `AuthenticationStateProvider` özel bir hizmetin uygulanması aşağıdaki bölümlerde ele alınmıştır.

Uygulama ve yapılandırma oluşturma hakkında <xref:security/blazor/webassembly/index>daha fazla bilgi için bkz.

### <a name="opno-locblazor-server-authentication"></a>BlazorSunucu kimlik doğrulaması

BlazorSunucu uygulamaları, SignalR'' kullanılarak oluşturulan gerçek zamanlı bir bağlantı üzerinden çalışır. Bağlantı kurulduğunda [tabanlı uygulamalarda SignalRkimlik doğrulama](xref:signalr/authn-and-authz) işlenir. Kimlik doğrulama, bir çerez veya başka bir taşıyıcı belirteci temel olabilir.

Uygulama ve yapılandırma oluşturma hakkında <xref:security/blazor/server>daha fazla bilgi için bkz.

## <a name="authenticationstateprovider-service"></a>Kimlik DoğrulamaStateProvider hizmeti

Yerleşik `AuthenticationStateProvider` hizmet, ASP.NET Core'un `HttpContext.User`kimlik doğrulama durumu verilerini elde eder. Kimlik doğrulama durumu, varolan ASP.NET Kimlik Doğrulama mekanizmalarıyla nasıl bütünleşir.

`AuthenticationStateProvider`kimlik doğrulama durumunu almak `AuthorizeView` için `CascadingAuthenticationState` bileşen ve bileşen tarafından kullanılan temel hizmettir.

Genellikle doğrudan kullanmayın. `AuthenticationStateProvider` Bu makalede daha sonra açıklanan [>Yetkilendirme Görünümü bileşenini](#authorizeview-component) veya [Görev\<Kimlik Doğrulama durumunu](#expose-the-authentication-state-as-a-cascading-parameter) kullanın. Doğrudan kullanmanın `AuthenticationStateProvider` ana dezavantajı, temel kimlik doğrulama durumu verileri değişirse bileşenin otomatik olarak bilgilendirilmemesidir.

Hizmet, `AuthenticationStateProvider` aşağıdaki örnekte gösterildiği <xref:System.Security.Claims.ClaimsPrincipal> gibi geçerli kullanıcıverilerini sağlayabilir:

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
            <li>@claim.Type &ndash; @claim.Value</li>
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

Eğer `user.Identity.IsAuthenticated` `true` ve kullanıcı bir, <xref:System.Security.Claims.ClaimsPrincipal>iddiaları numaralandırılmış ve rollerde üyelik değerlendirilebilir.

Bağımlılık enjeksiyonu (DI) ve hizmetleri hakkında <xref:blazor/dependency-injection> <xref:fundamentals/dependency-injection>daha fazla bilgi için bkz.

## <a name="implement-a-custom-authenticationstateprovider"></a>Özel bir AuthenticationStateProvider uygulama

Uygulama özel bir sağlayıcı gerektiriyorsa, uygulayın `AuthenticationStateProvider` ve geçersiz kılın: `GetAuthenticationStateAsync`

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

Bir Blazor WebAssembly uygulamasında, `CustomAuthStateProvider` hizmet `Main` *Program.cs*kayıtlıdır:

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

builder.Services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

Bir Blazor Server `CustomAuthStateProvider` uygulamasında, hizmet şu `Startup.ConfigureServices`şekilde kaydedilir:

```csharp
using Microsoft.AspNetCore.Components.Authorization;

...

services.AddScoped<AuthenticationStateProvider, CustomAuthStateProvider>();
```

Önceki `CustomAuthStateProvider` örnekte kullanarak, tüm kullanıcılar kullanıcı adı `mrfibuli`ile kimlik doğrulaması.

## <a name="expose-the-authentication-state-as-a-cascading-parameter"></a>Kimlik doğrulama durumunu basamaklı bir parametre olarak ortaya çıkarma

Kullanıcı tarafından tetiklenen bir eylemi gerçekleştirirken olduğu gibi yordamsal mantık için kimlik doğrulama durumu verileri gerekiyorsa, basamaklı bir `Task<AuthenticationState>`tür parametresi tanımlayarak kimlik doğrulama durumu verilerini elde edin:

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

`user.Identity.IsAuthenticated` Ise, `true`iddiaları numaralandırılabilir ve rollere üyelik değerlendirilebilir.

Bileşendeki `Task<AuthenticationState>` `AuthorizeRouteView` ve `CascadingAuthenticationState` bileşenlerdeki bileşenleri kullanarak basamaklı `App` parametreyi ayarlama *(App.razor):*

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)" />
    </Found>
    <NotFound>
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

Bir Blazor WebAssembly Uygulamasında, seçenekler ve yetkilendirme `Program.Main`için hizmetler ekleyin:

```csharp
builder.Services.AddOptions();
builder.Services.AddAuthorizationCore();
```

Sunucu Blazor uygulamasında seçenekler ve yetkilendirme hizmetleri zaten mevcut olduğundan başka bir işlem gerekmez.

## <a name="authorization"></a>Yetkilendirme

Kullanıcının kimliği doğrulandıktan sonra, kullanıcının neler yapabileceğini denetlemek için *yetkilendirme* kuralları uygulanır.

Erişim genellikle şu nedenlere bağlı olarak verilir veya reddedilir:

* Bir kullanıcının kimliği doğrulandı (oturum açmış).
* Bir kullanıcı bir *role*roldedir.
* Bir kullanıcının *bir talebi*vardır.
* Bir *ilke* memnun.

Bu kavramların her biri ASP.NET Core MVC veya Razor Pages uygulamasındakiyle aynıdır. ASP.NET Core güvenliği hakkında daha fazla bilgi için, ASP.NET Core Security ve Identity altındaki [makalelere](xref:security/index)bakın.

## <a name="authorizeview-component"></a>AuthorizeView bileşeni

Bileşen, `AuthorizeView` kullanıcının görmeye yetkili olup olmadığına bağlı olarak seçici olarak Kullanıcı Arası Bilgilerini görüntüler. Bu yaklaşım, yalnızca kullanıcı için veri *görüntülemeniz* gerektiğinde ve kullanıcının kimliğini yordam mantığıyla kullanmanız gerektiğinde yararlıdır.

Bileşen, oturum `context` açmış kullanıcı `AuthenticationState`hakkındaki bilgilere erişmek için kullanabileceğiniz bir tür değişkenini ortaya çıkarır:

```razor
<AuthorizeView>
    <h1>Hello, @context.User.Identity.Name!</h1>
    <p>You can only see this content if you're authenticated.</p>
</AuthorizeView>
```

Kullanıcının kimliği doğrulanmıyorsa görüntülenmek için farklı içerik de sağlayabilirsiniz:

```razor
<AuthorizeView>
    <Authorized>
        <h1>Hello, @context.User.Identity.Name!</h1>
        <p>You can only see this content if you're authenticated.</p>
    </Authorized>
    <NotAuthorized>
        <h1>Authentication Failure!</h1>
        <p>You're not signed in.</p>
    </NotAuthorized>
</AuthorizeView>
```

`AuthorizeView` Bileşen, bir liste `NavMenu` öğesini görüntülemek için `NavLink`bileşende *(Paylaşılan/NavMenu.razor)* kullanılabilir ,`<li>...</li>`ancak bu yaklaşımın yalnızca liste öğesini işlenen çıktıdan kaldırdığını unutmayın. Kullanıcının bileşene gezinmesini engellemez.

İçeriği `<Authorized>` ve `<NotAuthorized>` etiketleri, diğer etkileşimli bileşenler gibi rasgele öğeler içerebilir.

Kullanıcı Arabirimi seçeneklerini veya erişimi kontrol eden roller veya ilkeler gibi yetkilendirme koşulları [Yetkilendirme](#authorization) bölümünde ele alınır.

Yetkilendirme koşulları belirtilmemişse, `AuthorizeView` varsayılan bir ilke kullanır ve şu şekilde davranır:

* Yetkili olarak kimlik doğrulaması (oturum açmış) kullanıcılar.
* Kimliği doğrulanmamış (oturum dışı) kullanıcılar yetkisiz olarak.

### <a name="role-based-and-policy-based-authorization"></a>Rol tabanlı ve ilke tabanlı yetkilendirme

Bileşen `AuthorizeView` *rol tabanlı* veya *ilke tabanlı* yetkilendirmeyi destekler.

Rol tabanlı yetkilendirme için `Roles` parametreyi kullanın:

```razor
<AuthorizeView Roles="admin, superuser">
    <p>You can only see this if you're an admin or superuser.</p>
</AuthorizeView>
```

Daha fazla bilgi için bkz. <xref:security/authorization/roles>.

İlke tabanlı yetkilendirme için `Policy` parametreyi kullanın:

```razor
<AuthorizeView Policy="content-editor">
    <p>You can only see this if you satisfy the "content-editor" policy.</p>
</AuthorizeView>
```

Taleplere dayalı yetkilendirme, özel bir ilke tabanlı yetkilendirme örneğidir. Örneğin, kullanıcıların belirli bir hak talebinde bulunmalarını gerektiren bir ilke tanımlayabilirsiniz. Daha fazla bilgi için bkz. <xref:security/authorization/policies>.

Bu API'ler Blazor Sunucu veya Blazor WebAssembly uygulamalarında kullanılabilir.

Ne `Roles` de `Policy` belirtilmişse, `AuthorizeView` varsayılan ilkeyi kullanır.

### <a name="content-displayed-during-asynchronous-authentication"></a>Eşzamanlı kimlik doğrulama sırasında görüntülenen içerik

Blazorkimlik doğrulama *durumunun eşzamanlı olarak*belirlenmesini sağlar. Bu yaklaşım için birincil Blazor senaryo, kimlik doğrulaması için harici bir bitiş noktasına istekte bulunan WebAssembly uygulamalarıdır.

Kimlik doğrulama devam ederken, `AuthorizeView` varsayılan olarak hiçbir içerik görüntüler. Kimlik doğrulaması gerçekleşirken içeriği görüntülemek `<Authorizing>` için aşağıdaki öğeyi kullanın:

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

Bu yaklaşım normalde Sunucu uygulamaları Blazor için geçerli değildir. BlazorSunucu uygulamaları, durum kurulur kurulmaz kimlik doğrulama durumunu bilir. `Authorizing`içerik bir Blazor Server uygulamasının `AuthorizeView` bileşeninde sağlanabilir, ancak içerik hiçbir zaman görüntülenmez.

## <a name="authorize-attribute"></a>[Yetki] özniteliği

Öznitelik `[Authorize]` Razor bileşenlerinde kullanılabilir:

```razor
@page "/"
@attribute [Authorize]

You can only see this if you're signed in.
```

> [!IMPORTANT]
> Yalnızca `[Authorize]` Blazor Yönlendirici `@page` üzerinden ulaşılan bileşenlerde kullanın. Yetkilendirme, yalnızca bir sayfa içinde işlenen alt bileşenler için *değil,* yönlendirmenin bir yönü olarak gerçekleştirilir. Bir sayfadaki belirli parçaların görüntülenmesini `AuthorizeView` yetkilendirmek için bunun yerine kullanın.

Öznitelik, `[Authorize]` rol tabanlı veya ilke tabanlı yetkilendirmeyi de destekler. Rol tabanlı yetkilendirme için `Roles` parametreyi kullanın:

```razor
@page "/"
@attribute [Authorize(Roles = "admin, superuser")]

<p>You can only see this if you're in the 'admin' or 'superuser' role.</p>
```

İlke tabanlı yetkilendirme için `Policy` parametreyi kullanın:

```razor
@page "/"
@attribute [Authorize(Policy = "content-editor")]

<p>You can only see this if you satisfy the 'content-editor' policy.</p>
```

Ne `Roles` de `Policy` belirtilmişse, `[Authorize]` varsayılan olarak tedavi etmek için varsayılan ilke kullanır:

* Yetkili olarak kimlik doğrulaması (oturum açmış) kullanıcılar.
* Kimliği doğrulanmamış (oturum dışı) kullanıcılar yetkisiz olarak.

## <a name="customize-unauthorized-content-with-the-router-component"></a>Yönlendirici bileşeniyle yetkisiz içeriği özelleştirme

Bileşen, `Router` `AuthorizeRouteView` bileşenle birlikte, aşağıdaki durumlarda uygulamanın özel içerik belirtmesine olanak tanır:

* İçerik bulunamadı.
* Kullanıcı bileşene `[Authorize]` uygulanan bir koşul başarısız olur. Öznitelik `[Authorize]` [ `[Authorize]` öznitelik](#authorize-attribute) bölümünde ele alınmıştır.
* Eşkron kimlik doğrulama devam ediyor.

Varsayılan Blazor Sunucu proje şablonunda, `App` bileşen *(App.razor)* özel içeriğin nasıl ayarlangerektiğini gösterir:

```razor
<Router AppAssembly="@typeof(Program).Assembly">
    <Found Context="routeData">
        <AuthorizeRouteView RouteData="@routeData" DefaultLayout="@typeof(MainLayout)">
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
        <CascadingAuthenticationState>
            <LayoutView Layout="@typeof(MainLayout)">
                <h1>Sorry</h1>
                <p>Sorry, there's nothing at this address.</p>
            </LayoutView>
        </CascadingAuthenticationState>
    </NotFound>
</Router>
```

`<NotFound>`İçeriği , `<NotAuthorized>`ve `<Authorizing>` etiketleri, diğer etkileşimli bileşenler gibi rasgele öğeleri içerebilir.

`<NotAuthorized>` Öğe belirtilmemişse, aşağıdaki `AuthorizeRouteView` geri dönüş iletisini kullanır:

```html
Not authorized.
```

## <a name="notification-about-authentication-state-changes"></a>Kimlik doğrulama durumu değişiklikleri hakkında bildirim

Uygulama, temel kimlik doğrulama durumu verilerinin değiştiğini belirlerse (örneğin, kullanıcı oturumaçtığı veya başka bir kullanıcı rollerini değiştirdiği için), `AuthenticationStateProvider` özel bir [AuthenticationStateProvider](#implement-a-custom-authenticationstateprovider) isteğe bağlı olarak temel sınıftaki yöntemi `NotifyAuthenticationStateChanged` çağırabilir. Bu, tüketicilere kimlik doğrulama durumu verilerini (örneğin,) `AuthorizeView`yeni verileri kullanarak yeniden işlemeleri için haber verar.

## <a name="procedural-logic"></a>Usul mantığı

Uygulamanın yordam mantığının bir parçası olarak yetkilendirme kurallarını denetlemesi gerekiyorsa, `Task<AuthenticationState>` kullanıcının. <xref:System.Security.Claims.ClaimsPrincipal> `Task<AuthenticationState>`ilkeleri değerlendirmek için diğer hizmetlerle `IAuthorizationService`birleştirilebilir.

```razor
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
> Bir Blazor WebAssembly uygulama bileşeninde, aşağıdakileri `Microsoft.AspNetCore.Authorization` ve `Microsoft.AspNetCore.Components.Authorization` ad alanlarını ekleyin:
>
> ```razor
> @using Microsoft.AspNetCore.Authorization
> @using Microsoft.AspNetCore.Components.Authorization
> ```
>
> Bu ad alanları, uygulamanın *_Imports.razor* dosyasına eklenerek genel olarak sağlanabilir.

## <a name="authorization-in-opno-locblazor-webassembly-apps"></a>WebAssembly Blazor uygulamalarında yetkilendirme

WebAssembly uygulamalarında, Blazor istemci tarafı kodu kullanıcılar tarafından değiştirilebildiği için yetkilendirme denetimleri atlanabilir. Aynı durum, javascript SPA çerçeveleri veya herhangi bir işletim sistemi için yerel uygulamalar da dahil olmak üzere tüm istemci tarafındaki uygulama teknolojileri için de geçerlidir.

**İstemci tarafındaki uygulamanız tarafından erişilen api uç noktaları içinde her zaman sunucuda yetkilendirme denetimleri gerçekleştirin.**

Daha fazla bilgi için <xref:security/blazor/webassembly/index>aşağıdaki makalelere bakın.

## <a name="troubleshoot-errors"></a>Sorun giderme hataları

Sık karşılaşılan hatalar:

* **Yetkilendirme türü Görev\<Kimlik DoğrulamaDevlet> basamaklı bir parametre gerektirir. Bunu sağlamak için CascadingAuthenticationState'i kullanmayı düşünün.**

* **`null`değer için alınır`authenticationStateTask`**

Büyük olasılıkla proje kimlik doğrulama sıetkin Blazor bir Sunucu şablonu kullanılarak oluşturulmadı. Örneğin `<CascadingAuthenticationState>` bileşen *(App.razor)* gibi UI `App` ağacının bir bölümünü aşağıdaki gibi sarın:

```razor
<CascadingAuthenticationState>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CascadingAuthenticationState>
```

Basamaklı `CascadingAuthenticationState` parametreyi sağlar `Task<AuthenticationState>` ve bu parametre de `AuthenticationStateProvider` temel DI hizmetinden alır.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/index>
* <xref:security/blazor/server>
* <xref:security/authentication/windowsauth>
* [Awesome Blazor: Kimlik doğrulama](https://github.com/AdrienTorris/awesome-blazor#authentication) topluluk örnek bağlantılar
