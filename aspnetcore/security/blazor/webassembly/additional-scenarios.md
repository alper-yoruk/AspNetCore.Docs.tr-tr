---
title: ASP.NET Blazor Core WebAssembly ek güvenlik senaryoları
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 516132379ae20bd31c0f3b3261bb09b3f5b218f2
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501119"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor WebAssembly ek güvenlik senaryoları

Yazar: [Javier Calvarro Nelson](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a>Ek erişim belirteçleri isteme

Çoğu uygulama, yalnızca kullandıkları korumalı kaynaklarla etkileşim kurmak için bir erişim jetonu gerektirir. Bazı senaryolarda, bir uygulamanın iki veya daha fazla kaynakla etkileşim kurabilmesi için birden fazla belirteç gerekebilir.

Aşağıdaki örnekte, bir uygulamanın kullanıcı verilerini okuması ve posta göndermesi için ek Azure Etkin Dizin (AAD) Microsoft Graph API kapsamları gereklidir. Azure AAD portalına Microsoft Graph API izinleri ekledikten sonra, ek kapsamlar`Program.Main`İstemci uygulamasında (, *Program.cs)* yapılandırılır:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...

    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/Mail.Send");
    options.ProviderOptions.AdditionalScopesToConsent.Add(
        "https://graph.microsoft.com/User.Read");
}
```

Yöntem, `IAccessTokenProvider.RequestToken` bir uygulamanın aşağıdaki örnekte görüldüğü gibi, belirli bir kapsam kümesiyle bir belirteç sağlamasına olanak tanıyan aşırı yük sağlar:

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
    new AccessTokenRequestOptions
    {
        Scopes = new[] { "https://graph.microsoft.com/Mail.Send", 
            "https://graph.microsoft.com/User.Read" }
    });

if (tokenResult.TryGetToken(out var token))
{
    ...
}
```

`TryGetToken`Döndürür:

* `true`kullanım `token` için.
* `false`belirteç geri alınmazsa.

## <a name="handle-token-request-errors"></a>Belirteç isteği hatalarını işleme

Tek Sayfalı Bir Uygulama (SPA) Open ID Connect (OIDC) kullanarak bir kullanıcının kimliğini doğruladığında, kimlik doğrulama durumu SPA içinde ve Kimlik Sağlayıcısı'nda (IP) kullanıcının kimlik bilgilerini sağlaması sonucu ayarlanan oturum çerezi biçiminde yerel olarak tutulur.

Kullanıcı için IP'nin yadığı belirteçler genellikle kısa süreler için geçerlidir, normalde yaklaşık bir saat, bu nedenle istemci uygulaması düzenli olarak yeni belirteçler getirmelidir. Aksi takdirde, verilen belirteçlerin süresi dolduktan sonra kullanıcı oturumdan çıkar. Çoğu durumda, OIDC istemcileri IP içinde tutulan kimlik doğrulama durumu veya "oturum" sayesinde kullanıcının yeniden kimlik doğrulaması gerektirmeden yeni belirteçler sağlama da mümkün.

Örneğin, kullanıcı herhangi bir nedenle IP'den açıkça oturum açtıklarında, istemcinin kullanıcı etkileşimi olmadan bir belirteç alabildiği bazı durumlar vardır. Bu senaryo, bir kullanıcı `https://login.microsoftonline.com` ziyaret edip oturumu kaplarsa oluşur. Bu senaryolarda, uygulama kullanıcının oturum açtığını hemen bilmez. İstemcinin tuttuğu herhangi bir belirteç artık geçerli olmayabilir. Ayrıca, istemci geçerli belirteç sona erdikten sonra kullanıcı etkileşimi olmadan yeni bir belirteç sağlamak mümkün değildir.

Bu senaryolar belirteç tabanlı kimlik doğrulamasına özgü değildir. Onlar SPA'ların doğasının bir parçasıdır. Tanımlama bilgileri kullanan bir SPA, kimlik doğrulama çerezi kaldırılırsa sunucu API'sini de çağıramaz.

Bir uygulama korumalı kaynaklara API çağrıları gerçekleştirdiğinde, aşağıdakilerden haberdar olmalısınız:

* API'yi aramak için yeni bir erişim belirteci sağlamak için, kullanıcının yeniden kimlik doğrulaması gerekebilir.
* İstemci geçerli gibi görünen bir belirteci olsa bile, belirteç kullanıcı tarafından iptal edildiğiiçin sunucuya yapılan çağrı başarısız olabilir.

Uygulama bir belirteç istediğinde, iki olası sonucu vardır:

* İstek başarılı olur ve uygulamanın geçerli bir belirteci vardır.
* İstek başarısız olur ve uygulamanın yeni bir belirteç elde etmek için kullanıcının kimliğini yeniden doğrulaması gerekir.

Belirteç isteği başarısız olduğunda, yeniden yönlendirme gerçekleştirmeden önce geçerli durumu kaydetmek isteyip istemediğiniz konusunda karar vermeniz gerekir. Çeşitli yaklaşımlar karmaşıklık artan düzeylerde var:

* Geçerli sayfa durumunu oturum depolama alanında depolayın. Sırasında, `OnInitializeAsync`devam etmeden önce durum geri yüklenebilir olup olmadığını denetleyin.
* Bir sorgu dize parametresi ekleyin ve uygulamanın önceden kaydedilmiş durumu yeniden nemlendirmesi için gereken sinyali vermek için bunu kullanın.
* Diğer öğelerle çakışma riski olmadan verileri oturum depolama alanında depolamak için benzersiz bir tanımlayıcıiçeren bir sorgu dize parametresi ekleyin.

Aşağıdaki örnek, nasıl yapılacağını gösterir:

* Oturum açma sayfasına yönlendirmeden önce durumu koruyun.
* Sorgu dizesi parametresini kullanarak önceki durumu sonra kimlik doğrulaması kurtarın.

```razor
<EditForm Model="User" @onsubmit="OnSaveAsync">
    <label>User
        <InputText @bind-Value="User.Name" />
    </label>
    <label>Last name
        <InputText @bind-Value="User.LastName" />
    </label>
</EditForm>

@code {
    public class Profile
    {
        public string Name { get; set; }
        public string LastName { get; set; }
    }

    public Profile User { get; set; } = new Profile();

    protected async override Task OnInitializedAsync()
    {
        var currentQuery = new Uri(Navigation.Uri).Query;

        if (currentQuery.Contains("state=resumeSavingProfile"))
        {
            User = await JS.InvokeAsync<Profile>("sessionStorage.getState", 
                "resumeSavingProfile");
        }
    }

    public async Task OnSaveAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var resumeUri = Navigation.Uri + $"?state=resumeSavingProfile";

        var tokenResult = await AuthenticationService.RequestAccessToken(
            new AccessTokenRequestOptions
            {
                ReturnUrl = resumeUri
            });

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            await httpClient.PostJsonAsync("Save", User);
        }
        else
        {
            await JS.InvokeVoidAsync("sessionStorage.setState", 
                "resumeSavingProfile", User);
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }
    }
}
```

## <a name="save-app-state-before-an-authentication-operation"></a>Kimlik doğrulama işleminden önce uygulama durumunu kaydetme

Kimlik doğrulama işlemi sırasında, tarayıcı IP'ye yönlendirilmeden önce uygulama durumunu kaydetmek istediğiniz durumlar vardır. Bu durum, durum kapsayıcısı gibi bir şey kullanıyorsanız ve kimlik doğrulaması başarılı olduktan sonra durumu geri yüklemek istediğinizde de geçerli olabilir. Uygulamaya özgü durumu veya başvuruyu korumak için özel bir kimlik doğrulama durumu nesnesi kullanabilir ve kimlik doğrulama işlemi başarıyla tamamlandıktan sonra bu durumu geri yükleyebilirsiniz.

`Authentication`bileşen (*Sayfa/Authentication.razor*):

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLoginSucceded="RestoreState" 
    OnLogoutSucceded="RestoreState" />

@code {
    [Parameter]
    public string Action { get; set; }

    public class ApplicationAuthenticationState : RemoteAuthenticationState
    {
        public string Id { get; set; }
    }

    protected async override Task OnInitializedAsync()
    {
        if (RemoteAuthenticationActions.IsAction(RemoteAuthenticationActions.LogIn, 
            Action))
        {
            AuthenticationState.Id = Guid.NewGuid().ToString();
            await JS.InvokeVoidAsync("sessionStorage.setKey", 
                AuthenticationState.Id, State.Store());
        }
    }

    public async Task RestoreState(ApplicationAuthenticationState state)
    {
        var stored = await JS.InvokeAsync<string>("sessionStorage.getKey", 
            state.Id);
        State.FromStore(stored);
    }

    public ApplicationAuthenticationState AuthenticationState { get; set; } = 
        new ApplicationAuthenticationState();
}
```

## <a name="customize-app-routes"></a>Uygulama rotalarını özelleştirme

Varsayılan olarak, `Microsoft.AspNetCore.Components.WebAssembly.Authentication` kitaplık farklı kimlik doğrulama durumlarını temsil etmek için aşağıdaki tabloda gösterilen yolları kullanır.

| Yol                            | Amaç |
| -------------------------------- | ------- |
| `authentication/login`           | Oturum açma işlemini tetikler. |
| `authentication/login-callback`  | Herhangi bir oturum açma işleminin sonucunu işler. |
| `authentication/login-failed`    | Oturum açma işlemi bazı nedenlerden dolayı başarısız olduğunda hata iletileri görüntüler. |
| `authentication/logout`          | Bir oturum açma işlemini tetikler. |
| `authentication/logout-callback` | Bir oturum son işlem sonucunu işler. |
| `authentication/logout-failed`   | Oturum açma işlemi bazı nedenlerden dolayı başarısız olduğunda hata iletileri görüntüler. |
| `authentication/logged-out`      | Kullanıcının başarılı bir şekilde oturumu nettigini gösterir. |
| `authentication/profile`         | Kullanıcı profilini yeniden düzenledirecek bir işlemi tetikler. |
| `authentication/register`        | Yeni bir kullanıcıyı kaydetmek için bir işlemi tetikler. |

Önceki tabloda gösterilen yollar . `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths` Özel rotalar sağlamak için seçenekleri ayarlarken, uygulamanın her yolu işleyen bir rotası olduğunu onaylayın.

Aşağıdaki örnekte, tüm yollar `/security`.

`Authentication`bileşen (*Sayfa/Authentication.razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main`(*Program.cs*):

```csharp
builder.Services.AddApiAuthorization(options => { 
    options.AuthenticationPaths.LogInPath = "security/login";
    options.AuthenticationPaths.LogInCallbackPath = "security/login-callback";
    options.AuthenticationPaths.LogInFailedPath = "security/login-failed";
    options.AuthenticationPaths.LogOutPath = "security/logout";
    options.AuthenticationPaths.LogOutCallbackPath = "security/logout-callback";
    options.AuthenticationPaths.LogOutFailedPath = "security/logout-failed";
    options.AuthenticationPaths.LogOutSucceededPath = "security/logged-out";
    options.AuthenticationPaths.ProfilePath = "security/profile";
    options.AuthenticationPaths.RegisterPath = "security/register";
});
```

Gereksinim tamamen farklı yollar gerektiriyorsa, yolları daha önce `RemoteAuthenticatorView` açıklandığı gibi ayarlayın ve açık bir eylem parametresi ile işiçin:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

İsterseniz ui'yi farklı sayfalara ayırabilirsiniz.

## <a name="customize-the-authentication-user-interface"></a>Kimlik doğrulama kullanıcı arabirimini özelleştirme

`RemoteAuthenticatorView`her kimlik doğrulama durumu için varsayılan bir ui parçası kümesi içerir. Her durum özel olarak geçirilerek `RenderFragment`özelleştirilebilir. İlk oturum açma işlemi sırasında görüntülenen metni özelleştirmek `RemoteAuthenticatorView` için aşağıdaki gibi değiştirebilirsiniz.

`Authentication`bileşen (*Sayfa/Authentication.razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action">
    <LoggingIn>
        You are about to be redirected to https://login.microsoftonline.com.
    </LoggingIn>
</RemoteAuthenticatorView>

@code{
    [Parameter]
    public string Action { get; set; }
}
```

Aşağıdaki `RemoteAuthenticatorView` tabloda gösterilen kimlik doğrulama yolu başına kullanılabilecek bir parçası vardır.

| Yol                            | Parça                |
| -------------------------------- | ----------------------- |
| `authentication/login`           | `<LoggingIn>`           |
| `authentication/login-callback`  | `<CompletingLoggingIn>` |
| `authentication/login-failed`    | `<LogInFailed>`         |
| `authentication/logout`          | `<LogOut>`              |
| `authentication/logout-callback` | `<CompletingLogOut>`    |
| `authentication/logout-failed`   | `<LogOutFailed>`        |
| `authentication/logged-out`      | `<LogOutSucceeded>`     |
| `authentication/profile`         | `<UserProfile>`         |
| `authentication/register`        | `<Registering>`         |
