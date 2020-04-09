---
title: Azure Active Blazor Directory B2C ile ASP.NET Core WebAssembly bağımsız bir uygulamasını güvenli hale
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 0734bad2d4281eb856783a362ef8c608a303c17a
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977060"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>Azure Active Blazor Directory B2C ile ASP.NET Core WebAssembly bağımsız bir uygulamasını güvenli hale

Yazar: [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Kimlik doğrulaması için [Azure Active Directory (AAD) B2C](/azure/active-directory-b2c/overview) kullanan bağımsız bir Blazor WebAssembly uygulaması oluşturmak için:

1. Bir kiracı oluşturmak ve Azure Portalı'nda bir web uygulaması kaydetmek için aşağıdaki konulardaki kılavuzu izleyin:

   * [AAD B2C kiracı](/azure/active-directory-b2c/tutorial-create-tenant) &ndash; oluşturma Aşağıdaki bilgileri kaydedin:

     1\. AAD B2C örneği (örneğin, `https://contoso.b2clogin.com/`sondaki eğik çizgiyi içerir)<br>
     2\. AAD B2C Kiracı etki `contoso.onmicrosoft.com`alanı (örneğin, )

   * [Bir web uygulaması](/azure/active-directory-b2c/tutorial-register-applications) &ndash; kaydolun Uygulama kaydı sırasında aşağıdaki seçimleri yapın:

     1\. **Web App / Web API'yi** **Evet**olarak ayarlayın.<br>
     2\. Küme Örtülü akışı **Evet'e** **bırak.**<br>
     3\. 'nin Yanıt `https://localhost:5001/authentication/login-callback` **URL'si** ekle

     Uygulama Kimliğini (İstemci Kimliği) `11111111-1111-1111-1111-111111111111`kaydetme (örneğin, ).

   * [Kullanıcı akışları](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash; oluşturun Kaydolma ve kaydolma kullanıcı akışı oluşturun.

     En azından, `context.User.Identity.Name` bileşendeki *(Shared/LoginDisplay.razor)* doldurmak `LoginDisplay` için Uygulama **talepleri** > Display**Name** kullanıcı özniteliğini seçin.

     Uygulama için oluşturulan kaydolma ve kaydolma kullanıcı akış adını `B2C_1_signupsignin`kaydedin (örneğin, ).

1. Aşağıdaki komuttaki yer tutucuları daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğunda uygulayın:

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   Yoksa proje klasörü oluşturan çıktı konumunu belirtmek için, çıkış seçeneğini bir yolu olan komuta ekleyin `-o BlazorSample`(örneğin, ). Klasör adı da projenin adının bir parçası olur.

## <a name="authentication-package"></a>Kimlik doğrulama paketi

Bir uygulama Bireysel B2C Hesabı kullanmak`IndividualB2C`için oluşturulduğunda ( ), uygulama otomatik olarak Microsoft`Microsoft.Authentication.WebAssembly.Msal`Kimlik Doğrulama [Kitaplığı](/azure/active-directory/develop/msal-overview) için bir paket başvurusu alır ( ). Paket, uygulamanın kullanıcıları doğrulamasına ve korumalı API'leri aramak için belirteçler almasına yardımcı olan bir dizi ilkel lik sağlar.

Bir uygulamaya kimlik doğrulama ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

Önceki `{VERSION}` paket başvuruyu makalede gösterilen `Microsoft.AspNetCore.Blazor.Templates` paketin sürümüyle <xref:blazor/get-started> değiştirin.

Paket, `Microsoft.Authentication.WebAssembly.Msal` `Microsoft.AspNetCore.Components.WebAssembly.Authentication` paketi geçici olarak uygulamaya ekler.

## <a name="authentication-service-support"></a>Kimlik doğrulama hizmeti desteği

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
});
```

Yöntem, `AddMsalAuthentication` bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri arama kabul eder. Uygulamayı yapılandırmak için gereken değerler, uygulamayı kaydettirdiğinizde Azure Portal AAD yapılandırmasından elde edilebilir.

## <a name="access-token-scopes"></a>Belirteç kapsamlarına erişim

Blazor WebAssembly şablonu, güvenli bir API için erişim belirteci istemek üzere uygulamayı otomatik olarak yapılandırmaz. Oturum açma akışının bir parçası olarak bir belirteç sağlamak için, kapsamı aşağıdakilerin varsayılan erişim belirteç kapsamına `MsalProviderOptions`ekleyin:

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

## <a name="imports-file"></a>Dosyayı alma

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

* [Ek erişim belirteçleri isteme](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [Öğretici: Azure Active Directory B2C kiracısı oluşturma](/azure/active-directory-b2c/tutorial-create-tenant)
* [Microsoft kimlik platformu belgeleri](/azure/active-directory/develop/)
