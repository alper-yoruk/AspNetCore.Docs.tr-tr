---
title: Blazor WebAssemblyAzure Active Directory B2C ile ASP.NET Core tek başına uygulamanın güvenliğini sağlama
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/08/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 5a461c73b6e157ebb9fa165aa4ba298e1f9e99ab
ms.sourcegitcommit: ec41ab354952b75557240923756a8c2ac79b49f8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88202697"
---
# <a name="secure-an-aspnet-core-no-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>Blazor WebAssemblyAzure Active Directory B2C ile ASP.NET Core tek başına uygulamanın güvenliğini sağlama

, [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex) 'e göre

Kimlik doğrulaması için [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) kullanan [tek başına bir Blazor WebAssembly uygulama](xref:blazor/hosting-models#blazor-webassembly) oluşturmak için:

Bir kiracı oluşturmak ve bir Web uygulamasını Azure portalında kaydetmek için aşağıdaki konulardaki yönergeleri izleyin:

[AAD B2C kiracı oluşturma](/azure/active-directory-b2c/tutorial-create-tenant)

Aşağıdaki bilgileri kaydedin:

* AAD B2C örneği (örneğin, `https://contoso.b2clogin.com/` sonunda eğik çizgi içeren): örnek, Azure portal **uygulama kayıtları** sayfasından **uç noktalar** PENCERESI açılarak bulunan bir Azure B2C uygulama kaydının şeması ve barındırmadır.
* Birincil/yayımcı/kiracı etki alanını AAD B2C (örneğin, `contoso.onmicrosoft.com` ): etki alanı, kayıtlı uygulama için Azure Portal **marka** dikey penceresinde **Yayımcı etki alanı** olarak kullanılabilir.

Eğitim bölümündeki yönergeleri izleyin: *istemci uygulaması* için AAD uygulamasını kaydetmek üzere [Azure Active Directory B2C bir uygulamayı yeniden kaydedin](/azure/active-directory-b2c/tutorial-register-applications) ve ardından aşağıdakileri yapın:

1. **Azure Active Directory**  >  **uygulama kayıtları** **Yeni kayıt**' ı seçin.
1. Uygulama için bir **ad** sağlayın (örneğin, ** Blazor tek başına AAD B2C**).
1. **Desteklenen hesap türleri**için, birden çok kiracılı seçeneği seçin: **herhangi bir kuruluş dizinindeki hesaplar veya herhangi bir kimlik sağlayıcısı. Azure AD B2C kullanıcıları kimlik doğrulaması için.**
1. **Yeniden yönlendirme URI 'si** açılan öğesini **Web** 'e ayarlı bırakın ve aşağıdaki yeniden yönlendirme URI 'sini sağlayın: `https://localhost:{PORT}/authentication/login-callback` . Kestrel üzerinde çalışan bir uygulamanın varsayılan bağlantı noktası 5001 ' dir. Uygulama farklı bir Kestrel bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktasını kullanın. IIS Express için, uygulama için rastgele oluşturulan bağlantı noktası, **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir. Uygulama bu noktada mevcut olmadığından ve IIS Express bağlantı noktası bilinmediğinden, uygulama oluşturulduktan sonra bu adıma geri dönün ve yeniden yönlendirme URI 'sini güncelleştirin. Bu konunun ilerleyen kısımlarında bir açıklama görüntülenerek IIS Express kullanıcıların yeniden yönlendirme URI 'sini güncelleştirmesini hatırlatır.
1. **İzinlerin**  >  **OpenID 'ye yönetici onayı verdiğini ve offline_access izinlerinin** etkin olduğunu onaylayın.
1. **Kaydet**’i seçin.

Uygulama (istemci) KIMLIĞINI (örneğin, `41451fa7-82d9-4673-8fa5-69eff5a761fd` ) kaydedin.

**Kimlik doğrulama**  >  **platformu yapılandırması**  >  **Web**:

1. **Yeniden YÖNLENDIRME URI** 'sinin `https://localhost:{PORT}/authentication/login-callback` mevcut olduğunu onaylayın.
1. **Örtük izin**Için, **erişim belirteçleri** ve **Kimlik belirteçleri**onay kutularını seçin.
1. Uygulamanın kalan varsayılan değerleri bu deneyim için kabul edilebilir.
1. **Kaydet** düğmesini seçin.

**Giriş**  >  **Azure AD B2C**  >  **Kullanıcı akışları**' nda:

[Kaydolma ve oturum açma Kullanıcı akışı oluşturma](/azure/active-directory-b2c/tutorial-create-user-flows)

En azından, **Application claims**  >  **Display Name** `context.User.Identity.Name` `LoginDisplay` bileşen () içinde doldurmak için uygulama talepleri görünen adı Kullanıcı özniteliğini seçin `Shared/LoginDisplay.razor` .

Uygulama için oluşturulan kaydolma ve oturum açma Kullanıcı akış adını kaydedin (örneğin, `B2C_1_signupsignin` ).

Boş bir klasörde, aşağıdaki komutta yer tutucuları daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğu 'nda yürütün:

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -o {APP NAME} -ssp "{SIGN UP OR SIGN IN POLICY}"
```

| Yer tutucu                   | Azure portal adı               | Örnek                                |
| ----------------------------- | ------------------------------- | -------------------------------------- |
| `{AAD B2C INSTANCE}`          | Örnek                        | `https://contoso.b2clogin.com/`        |
| `{APP NAME}`                  | &mdash;                         | `BlazorSample`                         |
| `{CLIENT ID}`                 | Uygulama (istemci) kimliği         | `41451fa7-82d9-4673-8fa5-69eff5a761fd` |
| `{SIGN UP OR SIGN IN POLICY}` | Kaydolma/oturum açma Kullanıcı akışı       | `B2C_1_signupsignin1`                  |
| `{TENANT DOMAIN}`             | Birincil/yayımcı/kiracı etki alanı | `contoso.onmicrosoft.com`              |

Seçeneğiyle belirtilen çıktı konumu, `-o|--output` mevcut değilse bir proje klasörü oluşturur ve uygulamanın adının bir parçası haline gelir.

> [!NOTE]
> Azure Portal, uygulamanın **kimlik doğrulama**  >  **platformu yapılandırması**  >  **Web**  >  **yeniden yönlendirme URI 'si** , Kestrel sunucusunda varsayılan ayarlarla çalışan uygulamalar için bağlantı noktası 5001 için yapılandırılır.
>
> Uygulama rastgele bir IIS Express bağlantı noktasında çalışıyorsa, uygulamanın bağlantı noktası **hata ayıklama** panelinde uygulamanın özelliklerinde bulunabilir.
>
> Bağlantı noktası, uygulamanın bilinen bağlantı noktasıyla daha önce yapılandırılmamışsa, Azure portal uygulamanın kaydına dönün ve yeniden yönlendirme URI 'sini doğru bağlantı noktasıyla güncelleştirin.

Uygulamayı oluşturduktan sonra şunları yapmanız gerekir:

* AAD Kullanıcı hesabını kullanarak uygulamada oturum açın.
* Microsoft API 'Leri için erişim belirteçleri isteyin. Daha fazla bilgi için bkz.
  * [Erişim belirteci kapsamları](#access-token-scopes)
  * [Hızlı başlangıç: Web API 'lerini kullanıma sunmak için bir uygulama yapılandırma](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis).

## <a name="authentication-package"></a>Kimlik doğrulama paketi

Tek bir B2C hesabı () kullanmak üzere bir uygulama oluşturulduğunda `IndividualB2C` , uygulama otomatik olarak [Microsoft kimlik doğrulama kitaplığı](/azure/active-directory/develop/msal-overview) () için bir paket başvurusu alır [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) . Paket, uygulamanın kullanıcıların kimliğini doğrulamasına ve korunan API 'Leri çağırmak için belirteçleri almasına yardımcı olan bir dizi temel sunar.

Bir uygulamaya kimlik doğrulaması ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

[`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)Paket geçişli [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) olarak uygulamayı uygulamaya ekler.

## <a name="authentication-service-support"></a>Kimlik doğrulama hizmeti desteği

Kullanıcıları kimlik doğrulama desteği, hizmet kapsayıcısında <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> paket tarafından sağlanmış uzantı yöntemiyle kaydedilir [`Microsoft.Authentication.WebAssembly.Msal`](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/) . Bu yöntem, uygulamanın Identity sağlayıcı (IP) ile etkileşim kurması için gereken tüm hizmetleri ayarlar.

`Program.cs`:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>Yöntemi, bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri çağırma işlemini kabul eder. Uygulamayı yapılandırmak için gereken değerler, uygulamayı kaydettiğinizde AAD yapılandırmasından elde edilebilir.

Yapılandırma dosya tarafından sağlanır `wwwroot/appsettings.json` :

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

Örnek:

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
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

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:blazor/security/webassembly/additional-scenarios>
* [Güvenli bir varsayılan istemciyle bir uygulamada kimliği doğrulanmamış veya yetkilendirilmemiş Web API istekleri](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [Öğretici: Azure Active Directory B2C kiracısı oluşturma](/azure/active-directory-b2c/tutorial-create-tenant)
* [Microsoft kimlik platformu belgeleri](/azure/active-directory/develop/)
