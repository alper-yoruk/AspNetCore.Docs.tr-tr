---
title: Azure Active Directory bir ASP.NET Core Blazor weelsembly barındırılan uygulamasının güvenliğini sağlama
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 9e76b300c159a2a1432aa4b1c6e47b3d91084a85
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/05/2020
ms.locfileid: "84215099"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a>Azure Active Directory bir ASP.NET Core Blazor weelsembly barındırılan uygulamasının güvenliğini sağlama

, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre

Bu makalede, kimlik doğrulaması için [Azure Active Directory (AAD)](https://azure.microsoft.com/services/active-directory/) kullanan bir [ Blazor webassembly barındırılan uygulamasının](xref:blazor/hosting-models#blazor-webassembly) nasıl oluşturulacağı açıklanır.

## <a name="register-apps-in-aad-and-create-solution"></a>AAD 'de uygulama kaydetme ve çözüm oluşturma

### <a name="create-a-tenant"></a>Kiracı oluşturma

Hızlı başlangıç: AAD 'de kiracı oluşturmak için [bir kiracı ayarlama](/azure/active-directory/develop/quickstart-create-new-tenant) bölümündeki yönergeleri izleyin.

### <a name="register-a-server-api-app"></a>Sunucu API 'SI uygulaması kaydetme

Hızlı Başlangıç bölümündeki yönergeleri izleyin: *sunucu API uygulaması* IÇIN bir AAD uygulaması kaydetmek üzere Microsoft Identity platformu ve sonrakı Azure AAD konularıyla [bir uygulama kaydetme](/azure/active-directory/develop/quickstart-register-app) ve ardından aşağıdakileri yapın:

1. **Azure Active Directory**  >  **uygulama kayıtları** **Yeni kayıt**' ı seçin.
1. Uygulama için bir **ad** sağlayın (örneğin, ** Blazor sunucu AAD**).
1. Desteklenen bir **Hesap türü**seçin. Bu deneyim için **yalnızca bu kuruluş dizininde** (tek kiracı) hesaplar seçebilirsiniz.
1. *Sunucu API 'si uygulaması* Bu senaryoda **yeniden yönlendirme URI 'si** gerektirmez, bu nedenle açılan kutudan **Web** 'e ve yeniden yönlendirme URI 'si girmeyin.
1. Yönetici tarafından **Permissions**  >  **OpenID ve offline_access izinleri için izin ver** onay kutusunu devre dışı bırakın.
1. **Kaydol**’u seçin.

Aşağıdaki bilgileri kaydedin:

* *Sunucu API 'si uygulaması* Uygulama KIMLIĞI (Istemci KIMLIĞI) (örneğin, `11111111-1111-1111-1111-111111111111` )
* Dizin KIMLIĞI (kiracı KIMLIĞI) (örneğin, `222222222-2222-2222-2222-222222222222` )
* AAD kiracı etki alanı (örneğin, `contoso.onmicrosoft.com` ): etki alanı, kayıtlı uygulama için Azure Portal **marka** dikey penceresinde **Yayımcı etki alanı** olarak kullanılabilir.

**API izinlerinde**, **Microsoft Graph**  >  uygulama oturum açma veya Kullanıcı profili erişimi gerektirmediğinden Microsoft Graph**User. Read** iznini kaldırın.

**API 'Yi kullanıma**sunma bölümünde:

1. **Kapsam ekle**’yi seçin.
1. **Kaydet ve devam et**’i seçin.
1. Bir **kapsam adı** sağlayın (örneğin, `API.Access` ).
1. **Yönetici izni görünen adı** sağlayın (örneğin, `Access API` ).
1. **Yönetici onay açıklaması** sağlayın (örneğin, `Allows the app to access server app API endpoints.` ).
1. **Durumun** **etkin**olarak ayarlandığını onaylayın.
1. **Kapsam Ekle**' yi seçin.

Aşağıdaki bilgileri kaydedin:

* Uygulama KIMLIĞI URI 'SI (örneğin, `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` , `api://11111111-1111-1111-1111-111111111111` veya belirttiğiniz özel değer)
* Varsayılan kapsam (örneğin, `API.Access` )

### <a name="register-a-client-app"></a>İstemci uygulamasını kaydetme

[Hızlı başlangıç: bir uygulamayı Microsoft Identity platformu Ile kaydetme](/azure/active-directory/develop/quickstart-register-app) ve sonrakı Azure AAD konularındaki yönergeleri izleyerek *istemci UYGULAMASı* için bir AAD uygulaması kaydedin ve aşağıdakileri yapın:

1. **Azure Active Directory**  >  **uygulama kayıtları** **Yeni kayıt**' ı seçin.
1. Uygulama için bir **ad** sağlayın (örneğin, ** Blazor istemci AAD**).
1. Desteklenen bir **Hesap türü**seçin. Bu deneyim için **yalnızca bu kuruluş dizininde** (tek kiracı) hesaplar seçebilirsiniz.
1. **Yeniden yönlendirme URI 'si** açılan öğesini **Web** 'e ayarlı bırakın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` . Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir. Uygulama farklı bir Kestrel bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktasını kullanın. IIS Express için, uygulama için rastgele oluşturulan bağlantı noktası, **hata ayıklama** panelinde sunucu uygulamasının özelliklerinde bulunabilir. Uygulama bu noktada mevcut olmadığından ve IIS Express bağlantı noktası bilinmediğinden, uygulama oluşturulduktan sonra bu adıma geri dönün ve yeniden yönlendirme URI 'sini güncelleştirin. [Uygulama oluştur](#create-the-app) bölümünde, kullanıcıların YENIDEN yönlendirme URI 'sini güncelleştirmesi IIS Express hatırlatmak için bir açıklama belirir.
1. Yönetici tarafından **Permissions**  >  **OpenID ve offline_access izinleri için izin ver** onay kutusunu devre dışı bırakın.
1. **Kaydol**’u seçin.

*İstemci* UYGULAMASı uygulama kimliğini (istemci kimliği) kaydedin (örneğin, `33333333-3333-3333-3333-333333333333` ).

**Kimlik doğrulama**  >  **platformu yapılandırması**  >  **Web**:

1. **Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.
1. **Örtük izin**Için, **erişim belirteçleri** ve **Kimlik belirteçleri**onay kutularını seçin.
1. Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.
1. **Kaydet** düğmesini seçin.

**API izinleri**:

1. Uygulamanın **Microsoft Graph**  >  **User. Read** iznine sahip olduğunu doğrulayın.
1. **Izin Ekle** ' yi ve ardından **API 'lerim**' i seçin.
1. **Ad** SÜTUNUNDAN *sunucu API uygulamasını* (örneğin, ** Blazor sunucu AAD**) seçin.
1. **API** listesini açın.
1. API 'ye erişimi etkinleştirin (örneğin, `API.Access` ).
1. **Izin Ekle**' yi seçin.
1. **{Tenant Name} için yönetici Içeriği ver** düğmesini seçin. Onaylamak için **Evet**'i seçin.

### <a name="create-the-app"></a>Uygulama oluşturma

Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

Mevcut değilse bir proje klasörü oluşturan çıkış konumunu belirtmek için, komutuna bir yol ile çıkış seçeneğini ekleyin (örneğin, `-o BlazorSample` ). Klasör adı Ayrıca projenin adının bir parçası haline gelir.

> [!NOTE]
> Uygulama KIMLIĞI URI 'sini `app-id-uri` seçeneğe geçirin, ancak [erişim belirteci kapsamları](#access-token-scopes) bölümünde açıklanan istemci uygulamasında bir yapılandırma değişikliği gerekli olabilir.

> [!NOTE]
> Azure Portal, *istemci uygulamasının* **kimlik doğrulama**  >  **platformu yapılandırması**  >  **Web**  >  **yeniden yönlendirme URI 'si** , Kestrel sunucusunda varsayılan ayarlarla çalışan uygulamalar için bağlantı noktası 5001 için yapılandırılır.
>
> *İstemci uygulaması* rastgele bir IIS Express bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktası **hata ayıklama** panelinde *sunucu uygulamasının* özelliklerinde bulunabilir.
>
> Bağlantı noktası, *istemci uygulamasının* bilinen bağlantı noktasıyla daha önce yapılandırılmamışsa, Azure Portal *istemci uygulamanın* kaydına dönün ve yeniden yönlendirme URI 'sini doğru bağlantı noktasıyla güncelleştirin.

## <a name="server-app-configuration"></a>Sunucu uygulaması yapılandırması

*Bu bölüm, çözümün **sunucu** uygulamasıyla ilgilidir.*

### <a name="authentication-package"></a>Kimlik doğrulama paketi

ASP.NET Core Web API 'Lerine yönelik kimlik doğrulama ve yetkilendirme desteği [Microsoft. AspNetCore. Authentication. AzureAD. UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/) paketi tarafından sağlanır:

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a>Kimlik doğrulama hizmeti desteği

`AddAuthentication`Yöntemi, uygulama içinde kimlik doğrulama hizmetlerini ayarlar ve JWT taşıyıcı işleyicisini varsayılan kimlik doğrulama yöntemi olarak yapılandırır. <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A>Yöntemi, Azure Active Directory tarafından yayılan belirteçleri doğrulamak için gereken JWT taşıyıcı işleyicisinde belirli parametreleri ayarlar:

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A><xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>aşağıdakileri doğrulayın:

* Uygulama, gelen isteklerde belirteçleri ayrıştırmaya ve doğrulamaya çalışır.
* Uygun kimlik bilgileri olmadan korunan kaynağa erişmeye çalışan istekler başarısız olur.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>Kullanıcı. Identity . Ada

Varsayılan olarak, sunucu uygulaması API 'SI `User.Identity.Name` talep türünden değer ile doldurulur `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` (örneğin, `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` ).

Uygulamayı talep türünden değeri alacak şekilde yapılandırmak için, `name` Içindeki [Tokenvalidationparameters. NameClaimType](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) ' ı yapılandırın <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices` :

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Uygulama ayarları

*AppSettings. JSON* dosyası, erişim belirteçlerini doğrulamak IÇIN kullanılan JWT taşıyıcı işleyicisini yapılandırma seçeneklerini içerir:

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

Dalgalı tahmin denetleyicisi (*denetleyiciler/dalgalı Therforetcontroller. cs*), BIR korumalı API 'yi [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) denetleyiciye uygulanmış şekilde gösterir. Bunun anlaşılması **önemlidir** :

* Bu [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) API denetleyicisindeki özniteliği, bu API 'yi yetkisiz erişime karşı koruyan tek şeydir.
* [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) Blazor Webassembly uygulamasında kullanılan özniteliği yalnızca uygulamanın, uygulamanın düzgün şekilde çalışması için yetkilendirilmiş olması gerektiğine yönelik bir ipucu görevi görür.

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

Iş veya okul hesaplarını () kullanmak üzere bir uygulama oluşturulduğunda `SingleOrg` , uygulama [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) için otomatik olarak bir paket başvurusu alır ([Microsoft. Authentication. Webassembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)). Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.

Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

[Microsoft. Authentication. WebAssembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) paketi, [Microsoft. Aspnetcore. components. Webassembly. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) paketini uygulamaya göre geçişli olarak ekler.

### <a name="authentication-service-support"></a>Kimlik doğrulama hizmeti desteği

<xref:System.Net.Http.HttpClient>Sunucu projesine istek yaparken erişim belirteçlerini içeren örnekler için destek eklenmiştir.

*Program.cs*:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısına <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> [Microsoft. Authentication. WebAssembly. msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) paketi tarafından sağlanmış uzantı yöntemiyle kaydedilir. Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken hizmetleri ayarlar.

*Program.cs*:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Yöntemi, bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder. Uygulamanın yapılandırılması için gereken değerler, uygulamayı kaydettiğinizde Azure Portal AAD yapılandırmasından elde edilebilir.

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

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

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

Uygulamayı sunucu projesinden çalıştırın. Visual Studio 'yu kullanırken şunlardan birini yapın:

* Araç çubuğundaki **başlangıç projeleri** açılan listesini *sunucu API 'si uygulamasına* ayarlayın ve **Çalıştır** düğmesini seçin.
* **Çözüm Gezgini** ' de sunucu projesini seçin ve araç çubuğundaki **Çalıştır** düğmesini seçin veya uygulamayı **Hata Ayıkla** menüsünden başlatın.

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:security/blazor/webassembly/additional-scenarios>
* [Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Microsoft kimlik platformu belgeleri](/azure/active-directory/develop/)
