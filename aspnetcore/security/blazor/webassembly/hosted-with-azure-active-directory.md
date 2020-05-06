---
title: Azure Active Directory bir ASP.NET Core Blazor weelsembly barındırılan uygulamasının güvenliğini sağlama
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 3a2f3bdd194b9153c5d59af7adfad3a3c8c56b23
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776044"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a>Azure Active Directory bir ASP.NET Core Blazor weelsembly barındırılan uygulamasının güvenliğini sağlama

, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Bu makalede, kimlik doğrulaması için [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) kullanan bir [ Blazor webassembly barındırılan uygulamasının](xref:blazor/hosting-models#blazor-webassembly) nasıl oluşturulacağı açıklanır.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Uygulamaları AAD B2C kaydetme ve çözüm oluşturma

### <a name="create-a-tenant"></a>Kiracı oluşturma

Hızlı başlangıç: AAD 'de kiracı oluşturmak için [bir kiracı ayarlama](/azure/active-directory/develop/quickstart-create-new-tenant) bölümündeki yönergeleri izleyin.

### <a name="register-a-server-api-app"></a>Sunucu API 'SI uygulaması kaydetme

Hızlı Başlangıç bölümündeki yönergeleri izleyin: *sunucu API uygulaması* için bir AAD uygulamasını [Microsoft Identity platformu ile kaydetme](/azure/active-directory/develop/quickstart-register-app) ve sonraki Azure AAD konuları Azure Portal **Azure Active Directory** > **uygulama kayıtları** alanı:

1. **Yeni kayıt**seçeneğini belirleyin.
1. Uygulama için bir **ad** sağlayın (örneğin, ** Blazor sunucu AAD**).
1. Desteklenen bir **Hesap türü**seçin. Bu deneyim için **yalnızca bu kuruluş dizininde** (tek kiracı) hesaplar seçebilirsiniz.
1. *Sunucu API 'si uygulaması* Bu senaryoda **yeniden yönlendirme URI 'si** gerektirmez, bu nedenle açılan kutudan **Web** 'e ve yeniden yönlendirme URI 'si girmeyin.
1.  > **Yönetici tarafından OpenID ve offline_access izinleri için** izin ver onay kutusunu devre dışı bırakın. **Permissions**
1. **Kaydol**’u seçin.

**API izinlerinde**, uygulama oturum açma veya uer profil erişimi gerektirmediğinden **Microsoft Graph** > **User. Read** iznini kaldırın.

**API 'Yi kullanıma**sunma bölümünde:

1. **Kapsam ekle**’yi seçin.
1. **Kaydet ve devam et**’i seçin.
1. Bir **kapsam adı** sağlayın (örneğin, `API.Access`).
1. **Yönetici izni görünen adı** sağlayın (örneğin, `Access API`).
1. **Yönetici onay açıklaması** sağlayın (örneğin, `Allows the app to access server app API endpoints.`).
1. **Durumun** **etkin**olarak ayarlandığını onaylayın.
1. **Kapsam Ekle**' yi seçin.

Aşağıdaki bilgileri kaydedin:

* *Sunucu API 'si uygulaması* Uygulama KIMLIĞI (Istemci KIMLIĞI) (örneğin, `11111111-1111-1111-1111-111111111111`)
* Uygulama kimliği URI 'si (örneğin `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111`,, veya belirttiğiniz özel değer)
* Dizin KIMLIĞI (kiracı KIMLIĞI) (örneğin, `222222222-2222-2222-2222-222222222222`)
* AAD kiracı etki alanı (örneğin, `contoso.onmicrosoft.com`)
* Varsayılan kapsam (örneğin, `API.Access`)

### <a name="register-a-client-app"></a>İstemci uygulamasını kaydetme

Hızlı Başlangıç bölümündeki yönergeleri izleyin: *istemci uygulaması* için bir AAD uygulamasını [Microsoft Identity platformu ile kaydetme](/azure/active-directory/develop/quickstart-register-app) ve sonraki Azure AAD konuları Azure Portal **Azure Active Directory** > **uygulama kayıtları** alanında.

1. **Yeni kayıt**seçeneğini belirleyin.
1. Uygulama için bir **ad** sağlayın (örneğin, ** Blazor istemci AAD**).
1. Desteklenen bir **Hesap türü**seçin. Bu deneyim için **yalnızca bu kuruluş dizininde** (tek kiracı) hesaplar seçebilirsiniz.
1. **Yeniden yönlendirme URI 'si** açılan listesini `https://localhost:5001/authentication/login-callback` **Web**olarak ayarlayın ve bir yeniden yönlendirme URI 'si sağlayın.
1.  > **Yönetici tarafından OpenID ve offline_access izinleri için** izin ver onay kutusunu devre dışı bırakın. **Permissions**
1. **Kaydol**’u seçin.

**Kimlik doğrulama** > **platformu yapılandırması** > **Web**:

1. **Yeniden YÖNLENDIRME URI** 'sinin mevcut `https://localhost:5001/authentication/login-callback` olduğunu onaylayın.
1. **Örtük izin**Için, **erişim belirteçleri** ve **Kimlik belirteçleri**onay kutularını seçin.
1. Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.
1. **Kaydet** düğmesini seçin.

**API izinleri**:

1. Uygulamanın **Microsoft Graph** > **User. Read** iznine sahip olduğunu doğrulayın.
1. **Izin Ekle** ' yi ve ardından **API 'lerim**' i seçin.
1. **Ad** sütunundan *sunucu API uygulamasını* (örneğin, ** Blazor sunucu AAD**) seçin.
1. **API** listesini açın.
1. API 'ye erişimi etkinleştirin (örneğin, `API.Access`).
1. **Izin Ekle**' yi seçin.
1. **{Tenant Name} için yönetici Içeriği ver** düğmesini seçin. Onaylamak için **Evet**'i seçin.

*İstemci* UYGULAMASı uygulama kimliğini (istemci kimliği) kaydedin (örneğin, `33333333-3333-3333-3333-333333333333`).

### <a name="create-the-app"></a>Uygulama oluşturma

Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample`). Klasör adı Ayrıca projenin adının bir parçası haline gelir.

> [!NOTE]
> Uygulama KIMLIĞI URI 'sini `app-id-uri` seçeneğe geçirin, ancak [erişim belirteci kapsamları](#access-token-scopes) bölümünde açıklanan istemci uygulamasında bir yapılandırma değişikliği gerekli olabilir.

## <a name="server-app-configuration"></a>Sunucu uygulaması yapılandırması

*Bu bölüm, çözümün **sunucu** uygulamasıyla ilgilidir.*

### <a name="authentication-package"></a>Kimlik doğrulama paketi

ASP.NET Core Web API 'Lerine yönelik kimlik doğrulama ve yetkilendirme desteği şu şekilde sağlanır `Microsoft.AspNetCore.Authentication.AzureAD.UI`:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="{VERSION}" />
```

### <a name="authentication-service-support"></a>Kimlik doğrulama hizmeti desteği

`AddAuthentication` Yöntemi, uygulama içinde kimlik doğrulama hizmetlerini ayarlar ve JWT taşıyıcı işleyicisini varsayılan kimlik doğrulama yöntemi olarak yapılandırır. `AddAzureADBearer` Yöntemi, Azure Active Directory tarafından yayılan belirteçleri doğrulamak IÇIN gereken JWT taşıyıcı işleyicisinde belirli parametreleri ayarlar:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

`UseAuthentication`aşağıdakileri `UseAuthorization` doğrulayın:

* Uygulama, gelen isteklerde belirteçleri ayrıştırmaya ve doğrulamaya çalışır.
* Uygun kimlik bilgileri olmadan korunan kaynağa erişmeye çalışan istekler başarısız olur.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>Kullanıcısını. Identity. Ada

Varsayılan olarak, sunucu uygulaması API 'SI `User.Identity.Name` `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` talep türünden değer ile doldurulur (örneğin, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com`).

Uygulamayı `name` talep türünden değeri alacak şekilde yapılandırmak için, <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> içindeki `Startup.ConfigureServices` [tokenvalidationparameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) ' ı yapılandırın:

```csharp
services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Uygulama ayarları

*AppSettings. JSON* dosyası, erişim belirteçlerini doğrulamak IÇIN kullanılan JWT taşıyıcı işleyicisini yapılandırma seçeneklerini içerir.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

Örnek:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a>Hava tahmin denetleyicisi

Dalgalı tahmin denetleyicisi (*denetleyiciler/dalgalı Therforetcontroller. cs*), BIR korumalı API `[Authorize]` 'yi denetleyiciye uygulanmış şekilde gösterir. Bunun anlaşılması **önemlidir** :

* Bu `[Authorize]` API denetleyicisindeki özniteliği, bu API 'yi yetkisiz erişime karşı koruyan tek şeydir.
* Blazor Webassembly uygulamasında kullanılan `[Authorize]` özniteliği yalnızca uygulamanın, uygulamanın düzgün şekilde çalışması için yetkilendirilmiş olması gerektiğine yönelik bir ipucu görevi görür.

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

## <a name="client-app-configuration"></a>İstemci uygulama yapılandırması

*Bu bölüm, çözümün **istemci** uygulaması ile ilgilidir.*

### <a name="authentication-package"></a>Kimlik doğrulama paketi

Iş veya okul hesaplarını (`SingleOrg`) kullanmak üzere bir uygulama oluşturulduğunda, uygulama [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) (`Microsoft.Authentication.WebAssembly.Msal`) için bir paket başvurusu otomatik olarak alır. Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.

Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Yukarıdaki `{VERSION}` paket başvurusunda, `Microsoft.AspNetCore.Blazor.Templates` <xref:blazor/get-started> makalede gösterilen paketin sürümüyle değiştirin.

`Microsoft.Authentication.WebAssembly.Msal` Paket geçişli olarak uygulamayı uygulamaya ekler `Microsoft.AspNetCore.Components.WebAssembly.Authentication` .

### <a name="authentication-service-support"></a>Kimlik doğrulama hizmeti desteği

Sunucu projesine `HttpClient` istek yaparken erişim belirteçlerini içeren örnekler için destek eklenmiştir.

*Program.cs*:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal` paket tarafından sağlanmış uzantı yöntemiyle kaydedilir. Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken tüm hizmetleri ayarlar.

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

Yöntemi `AddMsalAuthentication` , bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder. Uygulamanın yapılandırılması için gereken değerler, uygulamayı kaydettiğinizde Azure Portal AAD yapılandırmasından elde edilebilir.

Yapılandırma *Wwwroot/appSettings. JSON* dosyası tarafından sağlanır:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

Örnek:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a>Erişim belirteci kapsamları

Varsayılan erişim belirteci kapsamları, erişim belirteci kapsamlarının listesini temsil eder:

* Oturum açma isteğine varsayılan olarak dahildir.
* Kimlik doğrulamasından hemen sonra bir erişim belirteci sağlamak için kullanılır.

Tüm kapsamlar Azure Active Directory kuralları başına aynı uygulamaya ait olmalıdır. Gerektiğinde ek API uygulamaları için ek kapsamlar eklenebilir:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> Azure portal bir kapsam URI 'SI sağlıyorsa ve uygulama API 'den *401 Yetkisiz* bir yanıt aldığında **işlenmeyen bir özel durum oluşturursa** , düzeni ve Konağı içermeyen bir kapsam URI 'si kullanmayı deneyin. Örneğin, Azure portal aşağıdaki kapsam URI biçimlerinden birini verebilir:
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> Kapsam URI 'sini düzen ve ana bilgisayar olmadan sağlayın:
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Daha fazla bilgi için *ek senaryolar* makalesinin aşağıdaki bölümlerine bakın:

* [Ek erişim belirteçleri isteyin](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [Giden isteklere belirteç iliştirme](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)


### <a name="imports-file"></a>Dosya içeri aktarmalar

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

Uygulamayı sunucu projesinden çalıştırın. Visual Studio 'Yu kullanırken **Çözüm Gezgini** ' de sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya uygulamayı **Hata Ayıkla** menüsünden başlatın.

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/blazor/webassembly/additional-scenarios>
* <xref:security/authentication/azure-active-directory/index>
* [Microsoft kimlik platformu belgeleri](/azure/active-directory/develop/)
