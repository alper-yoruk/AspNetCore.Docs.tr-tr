---
title: Blazor WebAssemblyAzure Active Directory B2C ile ASP.NET Core barındırılan bir uygulamanın güvenliğini sağlama
author: guardrex
description: ASP.NET Core barındırılan bir uygulamanın güvenliğini Azure Active Directory B2C ile nasıl sağlayacağınızı öğrenin Blazor WebAssembly .
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/02/2020
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
uid: blazor/security/webassembly/hosted-with-azure-active-directory-b2c
ms.openlocfilehash: 8727fa52acbcf59549c326bd5106e5dfe23c36be
ms.sourcegitcommit: fe2e3174c34bee1e425c6e52dd8f663fe52b8756
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96174322"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-hosted-app-with-azure-active-directory-b2c"></a>Blazor WebAssemblyAzure Active Directory B2C ile ASP.NET Core barındırılan bir uygulamanın güvenliğini sağlama

, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre

Bu makalede kimlik doğrulaması için [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) kullanan bir [barındırılan Blazor WebAssembly uygulamanın](xref:blazor/hosting-models#blazor-webassembly) nasıl oluşturulacağı açıklanır.

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>Uygulamaları AAD B2C kaydetme ve çözüm oluşturma

### <a name="create-a-tenant"></a>Kiracı oluşturma

Öğretici: AAD B2C kiracı oluşturmak için [Azure Active Directory B2C kiracı oluşturma](/azure/active-directory-b2c/tutorial-create-tenant) ' daki yönergeleri izleyin.

AAD B2C örneğini kaydedin (örneğin, `https://contoso.b2clogin.com/` sonunda eğik çizgi içeren). Örnek, Azure portal **uygulama kayıtları** sayfasından **uç noktalar** penceresi AÇıLARAK bulunan bir Azure B2C uygulama kaydının şeması ve barındırmadır.

### <a name="register-a-server-api-app"></a>Sunucu API 'SI uygulaması kaydetme

Eğitim bölümündeki yönergeleri izleyin: *sunucu API uygulaması* IÇIN bir AAD uygulaması kaydetmek üzere [Azure Active Directory B2C bir uygulamayı kaydetme](/azure/active-directory-b2c/tutorial-register-applications) ve ardından aşağıdakileri yapın:

1. **Azure Active Directory**  >  **uygulama kayıtları** **Yeni kayıt**' ı seçin.
1. Uygulama için bir **ad** sağlayın (örneğin, **Blazor Server AAD B2C**).
1. **Desteklenen hesap türleri** için, çok kiracılı seçeneğini belirleyin: **herhangi bir kimlik sağlayıcısındaki veya kuruluş dizinindeki hesaplar (Kullanıcı akışları olan kullanıcıların kimliğini doğrulamak için)**
1. *Sunucu API 'si uygulaması* Bu senaryoda **yeniden yönlendirme URI 'si** gerektirmez, bu nedenle açılan kutudan **Web** 'e ve yeniden yönlendirme URI 'si girmeyin.
1. **İzinlerin**  >  **OpenID 'ye yönetici onayı verdiğini ve offline_access izinlerinin** seçili olduğunu onaylayın.
1. **Kaydet**’i seçin.

Aşağıdaki bilgileri kaydedin:

* *Sunucu API 'si uygulaması* Uygulama (istemci) KIMLIĞI (örneğin, `41451fa7-82d9-4673-8fa5-69eff5a761fd` )
* AAD birincil/yayımcı/kiracı etki alanı (örneğin, `contoso.onmicrosoft.com` ): etki alanı, kayıtlı uygulama için Azure Portal **marka** dikey penceresinde **Yayımcı etki alanı** olarak kullanılabilir.

**API 'Yi kullanıma** sunma bölümünde:

1. **Kapsam ekle**’yi seçin.
1. **Kaydet ve devam et**’i seçin.
1. Bir **kapsam adı** sağlayın (örneğin, `API.Access` ).
1. **Yönetici izni görünen adı** sağlayın (örneğin, `Access API` ).
1. **Yönetici onay açıklaması** sağlayın (örneğin, `Allows the app to access server app API endpoints.` ).
1. **Durumun** **etkin** olarak ayarlandığını onaylayın.
1. **Kapsam Ekle**' yi seçin.

Aşağıdaki bilgileri kaydedin:

* Uygulama KIMLIĞI URI 'SI (örneğin, `api://41451fa7-82d9-4673-8fa5-69eff5a761fd` , `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` veya belirttiğiniz özel değer)
* Kapsam adı (örneğin, `API.Access` )

### <a name="register-a-client-app"></a>İstemci uygulamasını kaydetme

Eğitim bölümündeki yönergeleri izleyin [:](/azure/active-directory-b2c/tutorial-register-applications) uygulamayı bir AAD uygulaması kaydetmek için Azure Active Directory B2C yeniden kaydedin *`Client`* ve ardından aşağıdakileri yapın:

::: moniker range=">= aspnetcore-5.0"

1. **Azure Active Directory** > **uygulama kayıtları** **Yeni kayıt**' ı seçin.
1. Uygulama için bir **ad** sağlayın (örneğin, **Blazor istemci AAD B2C**).
1. **Desteklenen hesap türleri** için, çok kiracılı seçeneğini belirleyin: **herhangi bir kimlik sağlayıcısındaki veya kuruluş dizinindeki hesaplar (Kullanıcı akışları olan kullanıcıların kimliğini doğrulamak için)**
1. **Yeniden yönlendirme URI 'si** açılan öğesini **tek SAYFALı uygulama (Spa)** olarak ayarlayın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` . Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir. Uygulama farklı bir Kestrel bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktasını kullanın. IIS Express için, uygulama için rastgele oluşturulan bağlantı noktası, *`Server`* **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir. Uygulama bu noktada mevcut olmadığından ve IIS Express bağlantı noktası bilinmediğinden, uygulama oluşturulduktan sonra bu adıma geri dönün ve yeniden yönlendirme URI 'sini güncelleştirin. [Uygulama oluştur](#create-the-app) bölümünde, kullanıcıların YENIDEN yönlendirme URI 'sini güncelleştirmesi IIS Express hatırlatmak için bir açıklama belirir.
1. **İzinlerin** > **OpenID 'ye yönetici onayı verdiğini ve offline_access izinlerinin** seçili olduğunu onaylayın.
1. **Kaydet**’i seçin.

1. Uygulama (istemci) KIMLIĞINI (örneğin, `4369008b-21fa-427c-abaa-9b53bf58e538` ) kaydedin.

**Kimlik doğrulama** > **platformu yapılandırmalarında** > **tek sayfalı uygulama (Spa)**:

1. **Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.
1. **Örtük verme** Için, **erişim belirteçleri** ve **Kimlik belirteçleri** onay kutularının seçili **olmadığından** emin olun.
1. Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.
1. **Kaydet** düğmesini seçin.

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. **Azure Active Directory** > **uygulama kayıtları** **Yeni kayıt**' ı seçin.
1. Uygulama için bir **ad** sağlayın (örneğin, **Blazor istemci AAD B2C**).
1. **Desteklenen hesap türleri** için, çok kiracılı seçeneğini belirleyin: **herhangi bir kimlik sağlayıcısındaki veya kuruluş dizinindeki hesaplar (Kullanıcı akışları olan kullanıcıların kimliğini doğrulamak için)**
1. **Yeniden yönlendirme URI 'si** açılan öğesini **Web** 'e ayarlı bırakın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` . Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir. Uygulama farklı bir Kestrel bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktasını kullanın. IIS Express için, uygulama için rastgele oluşturulan bağlantı noktası, *`Server`* **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir. Uygulama bu noktada mevcut olmadığından ve IIS Express bağlantı noktası bilinmediğinden, uygulama oluşturulduktan sonra bu adıma geri dönün ve yeniden yönlendirme URI 'sini güncelleştirin. [Uygulama oluştur](#create-the-app) bölümünde, kullanıcıların YENIDEN yönlendirme URI 'sini güncelleştirmesi IIS Express hatırlatmak için bir açıklama belirir.
1. **İzinlerin** > **OpenID 'ye yönetici onayı verdiğini ve offline_access izinlerinin** seçili olduğunu onaylayın.
1. **Kaydet**’i seçin.

Uygulama (istemci) KIMLIĞINI (örneğin, `4369008b-21fa-427c-abaa-9b53bf58e538` ) kaydedin.

**Kimlik doğrulama** > **platformu yapılandırması** > **Web**:

1. **Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.
1. **Örtük izin** Için, **erişim belirteçleri** ve **Kimlik belirteçleri** onay kutularını seçin.
1. Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.
1. **Kaydet** düğmesini seçin.

::: moniker-end

**API izinleri**:

1. **Izin Ekle** ' yi ve ardından **API 'lerim**' i seçin.
1. **Ad** SÜTUNUNDAN *sunucu API uygulamasını* seçin (örneğin, **Blazor Server AAD B2C**).
1. **API** listesini açın.
1. API 'ye erişimi etkinleştirin (örneğin, `API.Access` ).
1. **Izin Ekle**' yi seçin.
1. **{Tenant Name} için yönetici onayı Izni ver** düğmesini seçin. Onaylamak için **Evet**’i seçin.

**Giriş**  >  **Azure AD B2C**  >  **Kullanıcı akışları**' nda:

[Kaydolma ve oturum açma Kullanıcı akışı oluşturma](/azure/active-directory-b2c/tutorial-create-user-flows)

En azından, **Application claims**  >  **Display Name** `context.User.Identity.Name` `LoginDisplay` bileşen () içinde doldurmak için uygulama talepleri görünen adı Kullanıcı özniteliğini seçin `Shared/LoginDisplay.razor` .

Uygulama için oluşturulan kaydolma ve oturum açma Kullanıcı akış adını kaydedin (örneğin, `B2C_1_signupsignin` ).

### <a name="create-the-app"></a>Uygulama oluşturma

Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| Yer tutucu                   | Azure portal adı                                     | Örnek                                        |
| ----------------------------- | ----------------------------------------------------- | ---------------------------------------------- |
| `{AAD B2C INSTANCE}`          | Örnek                                              | `https://contoso.b2clogin.com/`                |
| `{APP NAME}`                  | &mdash;                                               | `BlazorSample`                                 |
| `{CLIENT APP CLIENT ID}`      | Uygulamanın uygulama (istemci) KIMLIĞI *`Client`*        | `4369008b-21fa-427c-abaa-9b53bf58e538`         |
| `{DEFAULT SCOPE}`             | Kapsam adı                                            | `API.Access`                                   |
| `{SERVER API APP CLIENT ID}`  | *Sunucu API uygulaması* için uygulama (ISTEMCI) kimliği      | `41451fa7-82d9-4673-8fa5-69eff5a761fd`         |
| `{SERVER API APP ID URI}`     | Uygulama KIMLIĞI URI 'SI&dagger;                            | `41451fa7-82d9-4673-8fa5-69eff5a761fd`&dagger; |
| `{SIGN UP OR SIGN IN POLICY}` | Kaydolma/oturum açma Kullanıcı akışı                             | `B2C_1_signupsignin1`                          |
| `{TENANT DOMAIN}`             | Birincil/yayımcı/kiracı etki alanı                       | `contoso.onmicrosoft.com`                      |

&dagger;Blazor WebAssemblyŞablon, `api://` komutta GEÇIRILEN uygulama kimliği URI bağımsız değişkenine otomatik olarak bir şeması ekler `dotnet new` . Yer tutucu için uygulama KIMLIĞI URI 'SI sağlarken `{SERVER API APP ID URI}` ve düzen ise, `api://` `api://` Yukarıdaki tabloda örnek değer olarak gösterildiği gibi, bağımsız değişkenden düzeni () kaldırın. Uygulama KIMLIĞI URI 'SI özel bir değer ise veya başka bir şemaya sahipse (örneğin, `https://` güvenilir olmayan bir yayımcı etki alanı için `https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd` ), varsayılan kapsam URI 'sini el ile güncelleştirmeniz ve `api://` *`Client`* uygulama şablon tarafından oluşturulduktan sonra düzeni kaldırmanız gerekir. Daha fazla bilgi için, [erişim belirteci kapsamları](#access-token-scopes) bölümündeki nota bakın. Blazor WebAssemblyŞablon bu senaryolara yönelik ASP.NET Core gelecek bir sürümünde değiştirilebilir. Daha fazla bilgi için bkz. [IStream şablonuyla uygulama kimliği URI 'si Için çift düzen ( Blazor barındırılan, tek kuruluş) (DotNet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).

Seçeneğiyle belirtilen çıktı konumu, `-o|--output` mevcut değilse bir proje klasörü oluşturur ve uygulamanın adının bir parçası haline gelir.

> [!NOTE]
> Barındırılan şablon tarafından ayarlanan kapsamda Blazor uygulama KIMLIĞI URI ana bilgisayarı yineleniyor olabilir. Koleksiyon için yapılandırılan kapsamın `DefaultAccessTokenScopes` `Program.Main` uygulamanın () içinde doğru olduğundan emin olun `Program.cs` *`Client`* .

> [!NOTE]
> Azure portal, *`Client`* uygulamanın platform yapılandırması **yeniden yönlendirme URI 'Si** , Kestrel sunucusunda varsayılan ayarlarla çalışan uygulamalar için bağlantı noktası 5001 için yapılandırılır.
>
> *`Client`* Uygulama rastgele bir IIS Express bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktası **hata ayıklama** panelindeki *sunucu API 'si uygulamasının* özelliklerinde bulunabilir.
>
> Bağlantı noktası, *`Client`* uygulamanın bilinen bağlantı noktasıyla daha önce yapılandırılmamışsa, *`Client`* Azure Portal uygulamanın kaydına dönün ve yenıden yönlendirme URI 'sini doğru bağlantı noktasıyla güncelleştirin.

## <a name="server-app-configuration"></a>*`Server`* Uygulama yapılandırması

*Bu bölüm, çözümün uygulaması ile ilgilidir **`Server`** .*

### <a name="authentication-package"></a>Kimlik doğrulama paketi

ASP.NET Core Web API 'Lerine yönelik kimlik doğrulama ve yetkilendirme desteği paket tarafından sağlanır [`Microsoft.AspNetCore.Authentication.AzureADB2C.UI`](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureADB2C.UI) :

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureADB2C.UI" 
  Version="{VERSION}" />
```

Yer tutucu için `{VERSION}` , uygulamanın paylaşılan Framework sürümüyle eşleşen en son kararlı sürümü paketin [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI)adresindeki **sürüm geçmişinde** bulunabilir.

### <a name="authentication-service-support"></a>Kimlik doğrulama hizmeti desteği

`AddAuthentication`Yöntemi, uygulama içinde kimlik doğrulama hizmetlerini ayarlar ve JWT taşıyıcı işleyicisini varsayılan kimlik doğrulama yöntemi olarak yapılandırır. <xref:Microsoft.AspNetCore.Authentication.AzureADB2CAuthenticationBuilderExtensions.AddAzureADB2CBearer%2A>Yöntemi, Azure Active Directory B2C tarafından yayılan belirteçleri doğrulamak için gereken JWT taşıyıcı işleyicisinde belirli parametreleri ayarlar:

```csharp
services.AddAuthentication(AzureADB2CDefaults.BearerAuthenticationScheme)
    .AddAzureADB2CBearer(options => Configuration.Bind("AzureAdB2C", options));
```

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A><xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A>aşağıdakileri doğrulayın:

* Uygulama, gelen isteklerde belirteçleri ayrıştırmaya ve doğrulamaya çalışır.
* Uygun kimlik bilgileri olmadan korunan kaynağa erişmeye çalışan istekler başarısız olur.

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="userno-locidentityname"></a>Kullanıcı. Identity . Ada

Varsayılan olarak, `User.Identity.Name` doldurulmaz.

Uygulamayı talep türünden değeri alacak şekilde yapılandırmak için, `name` <xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType?displayProperty=nameWithType> ' ın <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> ' de ' yi yapılandırın `Startup.ConfigureServices` :

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADB2CDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>Uygulama ayarları

`appsettings.json`Dosya, erişim belirteçlerini doğrulamak için kullanılan JWT taşıyıcı işleyicisini yapılandırma seçeneklerini içerir.

```json
{
  "AzureAdB2C": {
    "Instance": "https://{TENANT}.b2clogin.com/",
    "ClientId": "{SERVER API APP CLIENT ID}",
    "Domain": "{TENANT DOMAIN}",
    "SignUpSignInPolicyId": "{SIGN UP OR SIGN IN POLICY}"
  }
}
```

Örnek:

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com/",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "Domain": "contoso.onmicrosoft.com",
    "SignUpSignInPolicyId": "B2C_1_signupsignin1",
  }
}
```

### <a name="weatherforecast-controller"></a>Hava tahmin denetleyicisi

Dalgalı tahmin denetleyicisi (*denetleyiciler/dalgalı Therforetcontroller. cs*), BIR korumalı API 'yi [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) denetleyiciye uygulanmış şekilde gösterir. Bunun anlaşılması **önemlidir** :

* Bu [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) API denetleyicisindeki özniteliği, bu API 'yi yetkisiz erişime karşı koruyan tek şeydir.
* [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)Uygulamada kullanılan özniteliği yalnızca uygulamanın, Blazor WebAssembly uygulamanın düzgün şekilde çalışması için yetkilendirilmiş olması gerektiğine yönelik bir ipucu görevi görür.

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

## <a name="client-app-configuration"></a>*`Client`* Uygulama yapılandırması

*Bu bölüm, çözümün uygulaması ile ilgilidir **`Client`** .*

### <a name="authentication-package"></a>Kimlik doğrulama paketi

Tek bir B2C hesabı () kullanmak üzere bir uygulama oluşturulduğunda `IndividualB2C` , uygulama otomatik olarak [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) () için bir paket başvurusu alır [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) . Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.

Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

Yer tutucu için `{VERSION}` , uygulamanın paylaşılan Framework sürümüyle eşleşen en son kararlı sürümü paketin [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)adresindeki **sürüm geçmişinde** bulunabilir.

[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)Paket geçişli [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) olarak uygulamayı uygulamaya ekler.

### <a name="authentication-service-support"></a>Kimlik doğrulama hizmeti desteği

<xref:System.Net.Http.HttpClient>Sunucu projesine istek yaparken erişim belirteçlerini içeren örnekler için destek eklenmiştir.

`Program.cs`:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
    client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddScoped(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

Yer tutucu, `{APP ASSEMBLY}` uygulamanın derleme adıdır (örneğin, `BlazorSample.ServerAPI` ).

Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> paket tarafından sağlanmış uzantı yöntemiyle kaydedilir [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) . Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken hizmetleri ayarlar.

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Yöntemi, bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder. Uygulamanın yapılandırılması için gereken değerler, uygulamayı kaydettiğinizde Azure Portal AAD yapılandırmasından elde edilebilir.

Yapılandırma dosya tarafından sağlanır `wwwroot/appsettings.json` :

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{TENANT DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

Örnek:

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": false
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
> Blazor WebAssemblyŞablon, `api://` komutta GEÇIRILEN uygulama kimliği URI bağımsız değişkenine otomatik olarak bir şeması ekler `dotnet new` . Proje şablonundan bir uygulama oluştururken Blazor , varsayılan erişim belirteci kapsamı değerinin Azure Portal belirttiğiniz doğru özel uygulama KIMLIĞI URI değerini ya da aşağıdaki biçimlerden **birine** sahip bir değeri kullandığını doğrulayın:
>
> * Dizinin yayımcı etki alanına **güvenilirse**, varsayılan erişim belirteci kapsamı genellikle aşağıdaki örneğe benzer bir değerdir; burada `API.Access` varsayılan kapsam adıdır:
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "api://41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   Çift düzen () için değeri inceleyin `api://api://...` . Bir çift düzen varsa, ilk `api://` düzeni değerden kaldırın.
>
> * Dizinin yayımcı etki alanı **güvenilir** olmadığında, varsayılan erişim belirteci kapsamı genellikle aşağıdaki örneğe benzer bir değerdir; burada `API.Access` varsayılan kapsam adıdır:
>
>   ```csharp
>   options.ProviderOptions.DefaultAccessTokenScopes.Add(
>       "https://contoso.onmicrosoft.com/41451fa7-82d9-4673-8fa5-69eff5a761fd/API.Access");
>   ```
>
>   Ek bir `api://` düzenin () değerini inceleyin `api://https://contoso.onmicrosoft.com/...` . Ek bir `api://` düzen varsa, `api://` düzeni değerden kaldırın.
>
> Blazor WebAssemblyŞablon bu senaryolara yönelik ASP.NET Core gelecek bir sürümünde değiştirilebilir. Daha fazla bilgi için bkz. [IStream şablonuyla uygulama kimliği URI 'si Için çift düzen ( Blazor barındırılan, tek kuruluş) (DotNet/aspnetcore #27417)](https://github.com/dotnet/aspnetcore/issues/27417).

İle ek kapsamlar belirtin `AdditionalScopesToConsent` :

```csharp
options.ProviderOptions.AdditionalScopesToConsent.Add("{ADDITIONAL SCOPE URI}");
```

Daha fazla bilgi için *ek senaryolar* makalesinin aşağıdaki bölümlerine bakın:

* [Ek erişim belirteçleri isteyin](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Giden isteklere belirteç iliştirme](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

### <a name="login-mode"></a>Oturum açma modu

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

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

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/security/webassembly/additional-scenarios>
* [Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [Öğretici: Azure Active Directory B2C kiracısı oluşturma](/azure/active-directory-b2c/tutorial-create-tenant)
* [Microsoft kimlik platformu belgeleri](/azure/active-directory/develop/)
