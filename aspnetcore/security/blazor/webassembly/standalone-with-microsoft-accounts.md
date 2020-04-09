---
title: Microsoft Hesapları Blazor ile ASP.NET Core WebAssembly bağımsız bir uygulama güvenli
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: 8c409651b3338c2baeae497bef43b994823a20f9
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977086"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a>Microsoft Hesapları Blazor ile ASP.NET Core WebAssembly bağımsız bir uygulama güvenli

Yazar: [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

Kimlik doğrulama Blazor için Azure Etkin [Dizinli (AAD) microsoft hesaplarını](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal) kullanan bağımsız bir WebAssembly uygulaması oluşturmak için:

1. [AAD kiracısı ve web uygulaması oluşturma](/azure/active-directory/develop/v2-overview)

   Azure portalının Azure **Etkin Dizin** > **Uygulaması kayıtları** alanında bir AAD uygulaması kaydolun:

   1\. Uygulama için bir **Ad** sağlayın (örneğin, ** Blazor İstemci AAD).**<br>
   2\. Desteklenen hesap **türlerinde, herhangi bir kuruluş dizinindeki Hesapları**seçin. **Supported account types**<br>
   3\. **Redirect URI** damlasını **Web'e**bırak ve uri'yi yeniden yönlendirmeyi `https://localhost:5001/authentication/login-callback`sağlar.<br>
   4\. **İzinleri** > devre dışı**kaldırın, Yönetici yi openid ve offline_access izinleri** onay kutusunu açmak için yoğunlaşın.<br>
   5\. **Kaydol**’u seçin.

   **Kimlik** > Doğrulama**Platformu yapılandırmalarında** > **Web**:

   1\. **Redirect** URI'nin `https://localhost:5001/authentication/login-callback` mevcut olduğunu doğrulayın.<br>
   2\. **Örtülü hibe**için, Erişim **belirteçleri** ve **kimlik belirteçleri**için onay kutularını seçin.<br>
   3\. Uygulama için kalan varsayılanlar bu deneyim için kabul edilebilir.<br>
   4\. **Kaydet** düğmesini seçin.

   Uygulama Kimliğini (İstemci Kimliği) `11111111-1111-1111-1111-111111111111`kaydetme (örneğin, ).

1. Aşağıdaki komuttaki yer tutucuları daha önce kaydedilen bilgilerle değiştirin ve komutu bir komut kabuğunda uygulayın:

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   Yoksa proje klasörü oluşturan çıktı konumunu belirtmek için, çıkış seçeneğini bir yolu olan komuta ekleyin `-o BlazorSample`(örneğin, ). Klasör adı da projenin adının bir parçası olur.

Uygulamayı oluşturduktan sonra şunları yapabilmeniz gerekir:

* Bir Microsoft Hesabı kullanarak uygulamaya giriş yapın.
* Uygulamayı doğru şekilde yapılandırmanız koşuluyla, bağımsız Blazor uygulamalarla aynı yaklaşımı kullanarak Microsoft API'leri için erişim belirteçleri isteyin. Daha fazla bilgi için [Bkz. Quickstart: Web API'lerini ortaya çıkarmak için bir uygulamayı yapılandırın.](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)

## <a name="authentication-package"></a>Kimlik doğrulama paketi

İş veya Okul Hesaplarını kullanmak için`SingleOrg`bir uygulama oluşturulduğunda ( ), uygulama otomatik olarak`Microsoft.Authentication.WebAssembly.Msal`Microsoft Kimlik Doğrulama [Kitaplığı](/azure/active-directory/develop/msal-overview) için bir paket başvurusu alır ( ). Paket, uygulamanın kullanıcıları doğrulamasına ve korumalı API'leri aramak için belirteçler almasına yardımcı olan bir dizi ilkel lik sağlar.

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
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

Yöntem, `AddMsalAuthentication` bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri arama kabul eder. Uygulamayı yapılandırmak için gereken değerler, uygulamayı kaydettirdiğinizde Microsoft Hesapları yapılandırmasından elde edilebilir.

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
* [Quickstart: Microsoft kimlik platformuile bir uygulama kaydetme](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [Quickstart: Web API'lerini ortaya çıkarmak için bir uygulamayı yapılandırma](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
