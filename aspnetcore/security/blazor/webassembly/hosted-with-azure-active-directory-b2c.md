---
title: Azure Active Blazor Directory B2C ile ASP.NET Core WebAssembly barındırılan uygulamayı güvenli hale
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: e2bb0c1bd807d590331b714e3b80d8c4ab434e2f
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123465"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a>Azure Active Blazor Directory B2C ile ASP.NET Core WebAssembly barındırılan uygulamayı güvenli hale

Yazar: [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Bu makalede, kimlik Blazor doğrulaması için Azure [Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) kullanan bir WebAssembly bağımsız uygulaması nasıl oluşturulacak açıklanmaktadır.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Uygulamaları AAD B2C'ye kaydedin ve çözüm oluşturun

### <a name="create-a-tenant"></a>Kiracı oluşturma

Öğretici: AAD B2C kiracısı oluşturmak ve aşağıdaki bilgileri kaydetmek için [Bir Azure Etkin Dizin B2C kiracısı oluşturun: Öğretici'deki](/azure/active-directory-b2c/tutorial-create-tenant) kılavuzu izleyin:

* AAD B2C örneği (örneğin, `https://contoso.b2clogin.com/`sondaki eğik çizgiyi içerir)
* AAD B2C Kiracı etki `contoso.onmicrosoft.com`alanı (örneğin, )

### <a name="register-a-server-api-app"></a>Sunucu API uygulamasını kaydetme

Öğretici' deki kılavuzu izleyin: Azure portalının **Azure Active Directory** > **App kayıtları** alanında Sunucu *API uygulaması* için bir AAD uygulaması kaydetmek için bir uygulamayı Azure Active [Directory B2C'ye kaydedin:](/azure/active-directory-b2c/tutorial-register-applications)

1. **Yeni kayıt**seçin.
1. Uygulama için bir **Ad** sağlayın (örneğin, ** Blazor Sunucu AAD B2C).**
1. **Desteklenen hesap türleri için,** herhangi bir kuruluş **dizinindeki veya herhangi bir kimlik sağlayıcısındaki Hesapları seçin. Azure AD B2C ile kullanıcıların kimliğini doğrulamak için.** (çok kiracılı) bu deneyim için.
1. *Sunucu API uygulaması* bu senaryoda bir Yönlendirme **URI** gerektirmez, bu nedenle açılan kümeyi **Web'e** bırakın ve yeniden yönlendirme URI'si girmeyin.
1. **İzinler** > **Grant yöneticisinin openid ve offline_access izinleri için yoğunlaştığını** onaylayın.
1. **Kaydol**’u seçin.

Bir **API ortaya çıkarmak:**

1. **Kapsam ekle**’yi seçin.
1. **Kaydet ve devam et**’i seçin.
1. Kapsam **adı** sağlayın (örneğin, `API.Access`).
1. Yönetici **onay görüntüleme adı** sağlayın `Access API`(örneğin, ).
1. Yönetici **onayı açıklaması** sağlayın (örneğin, `Allows the app to access server app API endpoints.`).
1. **Durum'un** **Etkin**olarak ayarladığını doğrulayın.
1. **Kapsam Ekle'yi**seçin.

Aşağıdaki bilgileri kaydedin:

* *Sunucu API uygulaması* Uygulama Kimliği (İstemci Kimliği) (örneğin, `11111111-1111-1111-1111-111111111111`)
* Uygulama Kimliği URI (örneğin, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`, `api://11111111-1111-1111-1111-111111111111`, veya sağladığınız özel değer)
* Dizin Kimliği (Kiracı Kimliği) `222222222-2222-2222-2222-222222222222`(örneğin, )
* *Sunucu API uygulaması* Uygulama Kimliği URI (örneğin, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111`Azure portalı Istemci Kimliği'nin değerini varsayılan olarak alabilir)
* Varsayılan kapsam (örneğin, `API.Access`)

### <a name="register-a-client-app"></a>İstemci uygulamasını kaydetme

Öğretici: Azure portalının **Azure Active Directory** > **App kayıtları** alanında Müşteri uygulaması için bir AAD *uygulaması* kaydetmek için bir uygulamayı Azure [Active Directory B2C'ye yeniden kaydedin:](/azure/active-directory-b2c/tutorial-register-applications)

1. **Yeni kayıt**seçin.
1. Uygulama için bir **Ad** sağlayın (örneğin, ** Blazor İstemci AAD B2C).**
1. **Desteklenen hesap türleri için,** herhangi bir kuruluş **dizinindeki veya herhangi bir kimlik sağlayıcısındaki Hesapları seçin. Azure AD B2C ile kullanıcıların kimliğini doğrulamak için.** (çok kiracılı) bu deneyim için.
1. **Redirect URI** damlasını **Web'e**bırak ve uri'yi yeniden yönlendirmeyi `https://localhost:5001/authentication/login-callback`sağlar.
1. **İzinler** > **Grant yöneticisinin openid ve offline_access izinleri için yoğunlaştığını** onaylayın.
1. **Kaydol**’u seçin.

**Kimlik** > Doğrulama**Platformu yapılandırmalarında** > **Web**:

1. **Redirect** URI'nin `https://localhost:5001/authentication/login-callback` mevcut olduğunu doğrulayın.
1. **Örtülü hibe**için, Erişim **belirteçleri** ve **kimlik belirteçleri**için onay kutularını seçin.
1. Uygulama için kalan varsayılanlar bu deneyim için kabul edilebilir.
1. **Kaydet** düğmesini seçin.

**API izinlerinde:**

1. Uygulamanın Microsoft **Graph** > **User.Read** iznine sahip olduğunu doğrulayın.
1. **API'lerim'i**izleyen **bir izin ekle'yi** seçin.
1. **Ad** sütunundan *Server API uygulamasını* seçin (örneğin, ** Blazor Sunucu AAD B2C).**
1. **API** listesini açın.
1. API'ye erişimi etkinleştirin `API.Access`(örneğin. ).
1. **İzin Ekle'yi**seçin.
1. **{KIRACı ADI}** düğmesi için Hibe yöneticisi içeriğini seçin. Onaylamak için **Evet**'i seçin.

**Evde** > **Azure AD B2C** > Kullanıcı**akışları:**

[Kaydolma ve kaydolma kullanıcı akışı oluşturma](/azure/active-directory-b2c/tutorial-create-user-flows)

En azından, `context.User.Identity.Name` bileşendeki *(Shared/LoginDisplay.razor)* doldurmak `LoginDisplay` için Uygulama **talepleri** > Display**Name** kullanıcı özniteliğini seçin.

Aşağıdaki bilgileri kaydedin:

* *İstemci uygulaması* Uygulama Kimliğini (İstemci `33333333-3333-3333-3333-333333333333`Kimliği) kaydetme (örneğin, ).
* Uygulama için oluşturulan kaydolma ve kaydolma kullanıcı akış adını `B2C_1_signupsignin`kaydedin (örneğin, ).

### <a name="create-the-app"></a>Uygulama oluşturma

Aşağıdaki komuttaki yer tutucuları daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğunda uygulayın:

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho -ssp "{SIGN UP OR SIGN IN POLICY}" --tenant-id "{TENANT ID}"
```

Yoksa proje klasörü oluşturan çıktı konumunu belirtmek için, çıkış seçeneğini bir yolu olan komuta ekleyin `-o BlazorSample`(örneğin, ). Klasör adı da projenin adının bir parçası olur.

> [!NOTE]
> App ID URI'yi `app-id-uri` seçenegeç, ancak [Erişim belirteç kapsamları](#access-token-scopes) bölümünde açıklanan istemci uygulamasında bir yapılandırma değişikliği gerekebileceğini unutmayın.

## <a name="server-app-configuration"></a>Sunucu uygulaması yapılandırması

*Bu bölüm çözümün **Server** uygulamasıyla ilgilidir.*

### <a name="authentication-package"></a>Kimlik doğrulama paketi

ASP.NET Çekirdek Web API'lerine yapılan çağrıları doğrulamak ve yetkilendirmek için destek `Microsoft.AspNetCore.Authentication.AzureADB2C.UI`aşağıdakiler tarafından sağlanır:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a>Kimlik doğrulama hizmeti desteği

Yöntem, `AddAuthentication` uygulama içinde kimlik doğrulama hizmetleri ayarlar ve Varsayılan kimlik doğrulama yöntemi olarak JWT Taşıyıcı işleyicisi yapılandırır. Yöntem, `AddAzureADB2CBearer` Azure Etkin Dizin idenb'i B2C tarafından yayılan belirteçleri doğrulamak için gereken JWT Taşıyıcı işleyicisindeki belirli parametreleri ayarlar:

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

`UseAuthentication`ve `UseAuthorization` şunları sağlamak:

* Uygulama, gelen istekler üzerindeki belirteçleri ayrışdırmaya ve doğrulamaya çalışır.
* Uygun kimlik bilgileri olmadan korumalı bir kaynağa erişmeye çalışan tüm istek başarısız olur.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>User.Identity.Name

Varsayılan olarak, `User.Identity.Name` doldurulur değil.

Uygulamanın `name` talep türünden değer alacak şekilde yapılandırılması için, [TokenValidationParameters.NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) in: <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices`

```csharp
services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Uygulama ayarları

*appsettings.json* dosyası, erişim belirteçlerini doğrulamak için kullanılan JWT taşıyıcı işleyicisini yapılandırma seçeneklerini içerir.

```json
{
  "AzureAd": {
    "Instance": "https://{ORGANIZATION}.b2clogin.com/",
    "ClientId": "{API CLIENT ID}",
    "Domain": "{DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

### <a name="weatherforecast-controller"></a>WeatherForecast denetleyicisi

WeatherForecast denetleyicisi *(Controllers/WeatherForecastController.cs)* denetleyiciye uygulanan `[Authorize]` öznitelik ile korunan bir API ortaya çıkarır. Şunu anlamak **önemlidir:**

* `[Authorize]` Bu API denetleyicisindeki öznitelik, bu API'yi yetkisiz erişimden koruyan tek şeydir.
* `[Authorize]` WebAssembly uygulamasında kullanılan öznitelik, yalnızca uygulamanın doğru çalışması için kullanıcıya yetki verilmesi gerektiğine dair bir ipucu görevi göremez. Blazor

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>İstemci uygulaması yapılandırması

*Bu bölüm çözümün **İstemci** uygulamasıyla ilgilidir.*

### <a name="authentication-package"></a>Kimlik doğrulama paketi

Bir uygulama Bireysel B2C Hesabı kullanmak`IndividualB2C`için oluşturulduğunda ( ), uygulama otomatik olarak Microsoft`Microsoft.Authentication.WebAssembly.Msal`Kimlik Doğrulama [Kitaplığı](/azure/active-directory/develop/msal-overview) için bir paket başvurusu alır ( ). Paket, uygulamanın kullanıcıları doğrulamasına ve korumalı API'leri aramak için belirteçler almasına yardımcı olan bir dizi ilkel lik sağlar.

Bir uygulamaya kimlik doğrulama ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Önceki `{VERSION}` paket başvuruyu makalede gösterilen `Microsoft.AspNetCore.Blazor.Templates` paketin sürümüyle <xref:blazor/get-started> değiştirin.

Paket, `Microsoft.Authentication.WebAssembly.Msal` `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paketi geçici olarak uygulamaya ekler.

### <a name="authentication-service-support"></a>Kimlik doğrulama hizmeti desteği

Kullanıcıların kimlik doğrulaması için destek, paket `AddMsalAuthentication` tarafından sağlanan uzantı yöntemiyle servis kapsayıcısında `Microsoft.Authentication.WebAssembly.Msal` kaydedilir. Bu yöntem, uygulamanın Kimlik Sağlayıcısı (IP) ile etkileşim kurması için gereken tüm hizmetleri ayarlar.

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

Yöntem, `AddMsalAuthentication` bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri arama kabul eder. Uygulamayı yapılandırmak için gereken değerler, uygulamayı kaydettirdiğinizde Azure Portal AAD yapılandırmasından elde edilebilir.

### <a name="access-token-scopes"></a>Belirteç kapsamlarına erişim

Varsayılan erişim belirteç kapsamları, aşağıdakiler için erişim belirteç kapsamları listesini temsil eder:

* Oturum açma isteğine varsayılan olarak dahil edilir.
* Kimlik doğrulamadan hemen sonra bir erişim belirteci sağlamak için kullanılır.

Tüm kapsamlar Azure Etkin Dizin kurallarına göre aynı uygulamaya ait olmalıdır. Gerektiğinde ek API uygulamaları için ek kapsamlar eklenebilir:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Azure portalı bir kapsam oluşturuyorsa URI ve uygulama API'den *401 Yetkisiz* yanıt aldığında **işlenmemiş bir özel durum atarsa,** düzeni ve ana bilgisayarı içermeyen bir KAPSAM URI kullanmayı deneyin. Örneğin, Azure portalı aşağıdaki kapsam URI biçimlerinden birini sağlayabilir:
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Şema ve ana bilgisayar olmadan URI kapsamını tedarik edin:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Daha fazla bilgi için bkz. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

### <a name="imports-file"></a>Dosyayı alma

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

### <a name="index-page"></a>Dizin sayfası

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>Uygulama bileşeni

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin bileşeni

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay bileşeni

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>Kimlik doğrulama bileşeni

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>FetchData bileşeni

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>Uygulamayı çalıştırma

Uygulamayı Sunucu projesinden çalıştırın. Visual Studio'yu kullanırken, **Solution Explorer'daki** Sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya **Hata Ayıklama** menüsünden uygulamayı başlatın.

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ek kaynaklar

* [Ek erişim belirteçleri isteme](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [Öğretici: Azure Active Directory B2C kiracısı oluşturma](/azure/active-directory-b2c/tutorial-create-tenant)
* [Microsoft kimlik platformu belgeleri](/azure/active-directory/develop/)
