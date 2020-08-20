---
title: Blazor WebAssemblyMicrosoft hesaplarıyla ASP.NET Core tek başına uygulamayı güvenli hale getirme
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
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
uid: blazor/security/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: faa2c1ec142ca5794f940653c76d41c5aba90653
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626083"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a>Blazor WebAssemblyMicrosoft hesaplarıyla ASP.NET Core tek başına uygulamayı güvenli hale getirme

, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre

Kimlik doğrulaması için [Azure Active Directory (AAD) Ile Microsoft hesapları](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) kullanan [tek başına bir Blazor WebAssembly uygulama](xref:blazor/hosting-models#blazor-webassembly) oluşturmak için:

[AAD kiracısı ve Web uygulaması oluşturma](/azure/active-directory/develop/v2-overview)

Azure Portal **Azure Active Directory**  >  **uygulama kayıtları** alanına bir AAD uygulaması kaydedin:

1. Uygulama için bir **ad** sağlayın (örneğin, ** Blazor tek başına AAD Microsoft hesapları**).
1. **Desteklenen hesap türleri**' nde, **herhangi bir kuruluş dizininde hesaplar**' ı seçin.
1. **Yeniden yönlendirme URI 'si** açılan öğesini **Web** 'e ayarlı bırakın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` . Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir. Uygulama farklı bir Kestrel bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktasını kullanın. IIS Express için, uygulama için rastgele oluşturulan bağlantı noktası, **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir. Uygulama bu noktada mevcut olmadığından ve IIS Express bağlantı noktası bilinmediğinden, uygulama oluşturulduktan sonra bu adıma geri dönün ve yeniden yönlendirme URI 'sini güncelleştirin. Bu konunun ilerleyen kısımlarında bir açıklama görüntülenerek IIS Express kullanıcıların yeniden yönlendirme URI 'sini güncelleştirmesini hatırlatır.
1. **Permissions**  >  **OpenID ve offline_access izinleri için yönetici onayı izni ver** onay kutusunu devre dışı bırakın.
1. **Kaydet**’i seçin.

Uygulama (istemci) KIMLIĞINI (örneğin, `41451fa7-82d9-4673-8fa5-69eff5a761fd` ) kaydedin.

**Kimlik doğrulama**  >  **platformu yapılandırması**  >  **Web**:

1. **Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.
1. **Örtük izin**Için, **erişim belirteçleri** ve **Kimlik belirteçleri**onay kutularını seçin.
1. Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.
1. **Kaydet** düğmesini seçin.

Uygulamayı oluşturun. Aşağıdaki komutta yer tutucuları, daha önce kaydedilen bilgilerle değiştirin ve komut kabuğu 'nda aşağıdaki komutu yürütün:

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common" -o {APP NAME}
```

| Yer tutucu   | Azure portal adı       | Örnek                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | Uygulama (istemci) kimliği | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |

Seçeneğiyle belirtilen çıktı konumu, `-o|--output` mevcut değilse bir proje klasörü oluşturur ve uygulamanın adının bir parçası haline gelir.

> [!NOTE]
> Azure Portal, uygulamanın **kimlik doğrulama**  >  **platformu yapılandırması**  >  **Web**  >  **yeniden yönlendirme URI 'si** , Kestrel sunucusunda varsayılan ayarlarla çalışan uygulamalar için bağlantı noktası 5001 için yapılandırılır.
>
> Uygulama rastgele bir IIS Express bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktası **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir.
>
> Bağlantı noktası, uygulamanın bilinen bağlantı noktasıyla daha önce yapılandırılmamışsa, Azure portal uygulamanın kaydına dönün ve yeniden yönlendirme URI 'sini doğru bağlantı noktasıyla güncelleştirin.

Uygulamayı oluşturduktan sonra şunları yapmanız gerekir:

* Microsoft hesabı kullanarak uygulamada oturum açın.
* Microsoft API 'Leri için erişim belirteçleri isteyin. Daha fazla bilgi için bkz.
  * [Erişim belirteci kapsamları](#access-token-scopes)
  * [Hızlı başlangıç: Web API 'lerini kullanıma sunmak için bir uygulama yapılandırma](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).

## <a name="authentication-package"></a>Kimlik doğrulama paketi

Iş veya okul hesaplarını () kullanmak üzere bir uygulama oluşturulduğunda `SingleOrg` , uygulama [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) () için bir paket başvurusu otomatik olarak alır [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) . Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.

Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="{VERSION}" />
```

Yer tutucu için `{VERSION}` , uygulamanın paylaşılan Framework sürümüyle eşleşen en son kararlı sürümü paketin [NuGet.org](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)adresindeki **sürüm geçmişinde** bulunabilir.

[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal)Paket geçişli [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) olarak uygulamayı uygulamaya ekler.

## <a name="authentication-service-support"></a>Kimlik doğrulama hizmeti desteği

Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> paket tarafından sağlanmış uzantı yöntemiyle kaydedilir [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal) . Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken tüm hizmetleri ayarlar.

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

<xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Yöntemi, bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder. Uygulamayı yapılandırmak için gereken değerler, uygulamayı kaydettiğinizde AAD yapılandırmasından elde edilebilir.

Yapılandırma dosya tarafından sağlanır `wwwroot/appsettings.json` :

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

Örnek:

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a>Erişim belirteci kapsamları

Blazor WebAssemblyŞablon, uygulamayı güvenli BIR API için erişim belirteci isteyecek şekilde otomatik olarak yapılandırmaz. Oturum açma akışının bir parçası olarak bir erişim belirteci sağlamak için, kapsamı varsayılan erişim belirteci kapsamlarına ekleyin <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

Daha fazla bilgi için *ek senaryolar* makalesinin aşağıdaki bölümlerine bakın:

* [Ek erişim belirteçleri isteyin](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [Giden isteklere belirteç iliştirme](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

::: moniker range=">= aspnetcore-5.0"

## <a name="login-mode"></a>Oturum açma modu

[!INCLUDE[](~/includes/blazor-security/msal-login-mode.md)]

::: moniker-end

## <a name="imports-file"></a>Dosya içeri aktarmalar

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Dizin sayfası

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a>Uygulama bileşeni

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin bileşeni

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay bileşeni

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>Kimlik doğrulama bileşeni

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/security/webassembly/additional-scenarios>
* [Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:blazor/security/webassembly/aad-groups-roles>
* [Hızlı başlangıç: Microsoft Identity platformu ile uygulama kaydetme](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [Hızlı başlangıç: Web API 'Lerini kullanıma sunmak için uygulama yapılandırma](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
