---
Başlık: ASP.NET Core ' Blazor kimlik doğrulama kitaplığıyla ' Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- ' SignalR ' uid: 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a>BlazorKimlik doğrulama kitaplığıyla ASP.NET Core webassembly tek başına uygulamasının güvenliğini sağlama

, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre

*Azure Active Directory (AAD) ve Azure Active Directory B2C (AAD B2C) için, bu konudaki yönergeleri izleyin. Bu içindekiler tablosu düğümündeki AAD ve AAD B2C konularına bakın.*

Blazor [Microsoft. Aspnetcore. components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) kitaplığını kullanan bir webassembly tek başına uygulaması oluşturmak için, komut kabuğu 'nda aşağıdaki komutu yürütün:

```dotnetcli
dotnet new blazorwasm -au Individual
```

Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample` ). Klasör adı Ayrıca projenin adının bir parçası haline gelir.

Visual Studio 'da [bir Blazor webassembly uygulaması oluşturun](xref:blazor/get-started). **Uygulama içi kullanıcı hesaplarını depola** seçeneğiyle **bireysel kullanıcı hesapları** için **kimlik doğrulamasını** ayarlayın.

## <a name="authentication-package"></a>Kimlik doğrulama paketi

Tek tek kullanıcı hesaplarını kullanmak üzere bir uygulama oluşturulduğunda, uygulama uygulamanın proje dosyasındaki [Microsoft. AspNetCore. components. WebAssembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) paketi için otomatik olarak bir paket başvurusu alır. Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.

Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a>Kimlik doğrulama hizmeti desteği

Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısına <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> [Microsoft. Aspnetcore. components. WebAssembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) paketi tarafından sağlanmış olan uzantı yöntemiyle kaydedilir. Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken hizmetleri ayarlar.

*Program.cs*:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

Yapılandırma *Wwwroot/appSettings. JSON* dosyası tarafından sağlanır:

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

Tek başına uygulamalar için kimlik doğrulama desteği, Open ID Connect (OıDC) kullanılarak sunulur. <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>Yöntemi, OıDC kullanarak bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder. Uygulamayı yapılandırmak için gereken değerler OıDC ile uyumlu IP 'den elde edilebilir. Uygulamayı kaydettiğinizde, genellikle çevrimiçi portalında gerçekleşen değerleri alın.

## <a name="access-token-scopes"></a>Erişim belirteci kapsamları

BlazorWebassembly şablonu, uygulamayı güvenli BIR API için erişim belirteci isteyecek şekilde otomatik olarak yapılandırmaz. Oturum açma akışının bir parçası olarak bir erişim belirteci sağlamak için, kapsamı varsayılan belirteç kapsamlarına ekleyin <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> :

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

Daha fazla bilgi için *ek senaryolar* makalesinin aşağıdaki bölümlerine bakın:

* [Ek erişim belirteçleri isteyin](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [Giden isteklere belirteç iliştirme](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>Dosya içeri aktarmalar

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Dizin sayfası

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a>Uygulama bileşeni

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin bileşeni

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay bileşeni

`LoginDisplay`Bileşen (*paylaşılan/logindisplay.* Razor), `MainLayout` bileşende (*paylaşılan/mainlayout. Razor*) işlenir ve aşağıdaki davranışları yönetir:

* Kimliği doğrulanmış kullanıcılar için:
  * Geçerli Kullanıcı adını görüntüler.
  * Uygulamanın oturumunu kapatmak için bir düğme sunar.
* Anonim kullanıcılar için oturum açma seçeneğini sunar.

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

## <a name="authentication-component"></a>Kimlik doğrulama bileşeni

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/blazor/webassembly/additional-scenarios>
* [Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
