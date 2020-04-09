---
title: Kimlik Doğrulama Blazor kitaplığı yla ASP.NET Core WebAssembly bağımsız bir uygulama güvenli
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 893fff10df37e1c2be549604f4cb83cd20049108
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977047"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a>Kimlik Doğrulama Blazor kitaplığı yla ASP.NET Core WebAssembly bağımsız bir uygulama güvenli

Yazar: [Javier Calvarro Nelson](https://github.com/javiercn) ve [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

*Azure Etkin Dizin (AAD) ve Azure Etkin Dizini B2C (AAD B2C) için bu konudaki yönergeye uymayın. Bu içerik düğümü tablosundaki AAD ve AAD B2C konularına bakın.*

Kitaplık Blazor kullanan `Microsoft.AspNetCore.Components.WebAssembly.Authentication` bir WebAssembly bağımsız uygulaması oluşturmak için, komut kabuğunda aşağıdaki komutu uygulayın:

```dotnetcli
dotnet new blazorwasm -au Individual
```

Yoksa proje klasörü oluşturan çıktı konumunu belirtmek için, çıkış seçeneğini bir yolu olan komuta ekleyin `-o BlazorSample`(örneğin, ). Klasör adı da projenin adının bir parçası olur.

Visual Studio'da [bir Blazor WebAssembly uygulaması oluşturun.](xref:blazor/get-started) Mağaza kullanıcı hesapları uygulama içi seçeneğiyle **Kimlik Doğrulamayı** **Bireysel Kullanıcı Hesaplarına** ayarlayın. **Store user accounts in-app**

## <a name="authentication-package"></a>Kimlik doğrulama paketi

Bireysel Kullanıcı Hesapları'nı kullanmak için bir uygulama oluşturulduğunda, uygulama `Microsoft.AspNetCore.Components.WebAssembly.Authentication` otomatik olarak uygulamanın proje dosyasındaki paket için bir paket başvurusu alır. Paket, uygulamanın kullanıcıları doğrulamasına ve korumalı API'leri aramak için belirteçler almasına yardımcı olan bir dizi ilkel lik sağlar.

Bir uygulamaya kimlik doğrulama ekliyorsanız, paketi uygulamanın proje dosyasına el ile ekleyin:

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

Önceki `{VERSION}` paket başvuruyu makalede gösterilen `Microsoft.AspNetCore.Blazor.Templates` paketin sürümüyle <xref:blazor/get-started> değiştirin.

## <a name="authentication-service-support"></a>Kimlik doğrulama hizmeti desteği

Kullanıcıların kimlik doğrulaması için destek, paket `AddOidcAuthentication` tarafından sağlanan uzantı yöntemiyle servis kapsayıcısında `Microsoft.AspNetCore.Components.WebAssembly.Authentication` kaydedilir. Bu yöntem, uygulamanın Kimlik Sağlayıcısı (IP) ile etkileşim kurması için gereken tüm hizmetleri ayarlar.

*Program.cs*:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

Bağımsız uygulamalar için kimlik doğrulama desteği Open ID Connect (OIDC) kullanılarak sunulur. Yöntem, `AddOidcAuthentication` OIDC kullanarak bir uygulamanın kimliğini doğrulamak için gereken parametreleri yapılandırmak için bir geri arama kabul eder. Uygulamanın yapılandırılması için gereken değerler OIDC uyumlu IP'den elde edilebilir. Genellikle çevrimiçi portallarında bulunan uygulamayı kaydederken değerleri edinin.

## <a name="access-token-scopes"></a>Belirteç kapsamlarına erişim

Blazor WebAssembly şablonu, güvenli bir API için erişim belirteci istemek üzere uygulamayı otomatik olarak yapılandırmaz. Oturum açma akışının bir parçası olarak bir belirteç sağlamak için, kapsamı `OidcProviderOptions`aşağıdakilerin varsayılan belirteç kapsamına ekleyin:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
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
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

Daha fazla bilgi için bkz. <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>.

## <a name="imports-file"></a>Dosyayı alma

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>Dizin sayfası

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

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
