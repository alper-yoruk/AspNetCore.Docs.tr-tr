---
title: ASP.NET Core 'de WS-Federation ile kullanıcıların kimliğini doğrulama
author: chlowell
description: Bu öğreticide, ASP.NET Core uygulamasında WS-Federation kullanımı gösterilmektedir.
ms.author: scaddie
ms.custom: mvc
ms.date: 01/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/ws-federation
ms.openlocfilehash: fede3887ad7dacd40cf3bb5d1b785392a9bc1480
ms.sourcegitcommit: 4a9321db7ca4e69074fa08a678dcc91e16215b1e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82850467"
---
# <a name="authenticate-users-with-ws-federation-in-aspnet-core"></a>ASP.NET Core 'de WS-Federation ile kullanıcıların kimliğini doğrulama

Bu öğreticide, kullanıcıların Active Directory Federasyon Hizmetleri (AD FS) (ADFS) veya [Azure Active Directory](/azure/active-directory/) (AAD) gıbı bir WS-Federasyon kimlik doğrulama sağlayıcısıyla oturum açmasını sağlama gösterilmektedir. [Facebook, Google ve dış sağlayıcı kimlik doğrulamasında](xref:security/authentication/social/index)açıklanan ASP.NET Core örnek uygulamasını kullanır.

ASP.NET Core uygulamalar için, [Microsoft. AspNetCore. Authentication. WSFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation)tarafından WS-Federation desteği sağlanır. Bu bileşen [Microsoft. Owin. Security. WsFederation](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation) 'tan ve bu bileşenin mekanizması 'nın birçoğunu paylaşır. Ancak, bileşenler birkaç önemli şekilde farklılık gösterir.

Varsayılan olarak, yeni ara yazılım:

* , İstenmeyen oturum açma işlemlerine izin vermez. WS-Federation protokolünün bu özelliği XSRF saldırılarına karşı savunmasızdır. Ancak, `AllowUnsolicitedLogins` seçeneğiyle etkinleştirilebilir.
* Oturum açma iletileri için her form gönderisini denetlemez. Yalnızca öğesine yapılan istekler `CallbackPath` , oturum açma işlemleri için denetlenir. `CallbackPath` varsayılan olarak `/signin-wsfed` , ancak [WsFederationOptions](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions) sınıfının devralınmış [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) özelliği aracılığıyla değiştirilebilir. Bu yol, [Skipunrecognizedrequests](/dotnet/api/microsoft.aspnetcore.authentication.wsfederation.wsfederationoptions.skipunrecognizedrequests) seçeneği etkinleştirilerek diğer kimlik doğrulama sağlayıcılarıyla paylaşılabilir.

## <a name="register-the-app-with-active-directory"></a>Uygulamayı Active Directory kaydetme

### <a name="active-directory-federation-services"></a>Active Directory Federasyon Hizmetleri

* ADFS yönetim konsolundan sunucunun **bağlı olan taraf güveni Ekleme Sihirbazı 'nı** açın:

![Bağlı olan taraf güveni Ekleme Sihirbazı: hoş geldiniz](ws-federation/_static/AdfsAddTrust.png)

* Verileri el ile girmeyi seçin:

![Bağlı olan taraf güveni Ekleme Sihirbazı: veri kaynağını seçin](ws-federation/_static/AdfsSelectDataSource.png)

* Bağlı olan taraf için bir görünen ad girin. Ad ASP.NET Core uygulaması için önemli değildir.

* [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) , belirteç şifreleme desteği yok, bu nedenle bir belirteç şifreleme sertifikası yapılandırmayın:

![Bağlı olan taraf güveni Ekleme Sihirbazı: sertifikayı yapılandırma](ws-federation/_static/AdfsConfigureCert.png)

* Uygulamanın URL 'sini kullanarak WS-Federation pasif Protokolü desteğini etkinleştirin. Uygulamanın bağlantı noktasının doğru olduğundan emin olun:

![Bağlı olan taraf güveni Ekleme Sihirbazı: URL 'YI yapılandırma](ws-federation/_static/AdfsConfigureUrl.png)

> [!NOTE]
> Bu bir HTTPS URL 'SI olmalıdır. IIS Express, geliştirme sırasında uygulamayı barındırırken otomatik olarak imzalanan bir sertifika sağlayabilir. Kestrel el ile sertifika yapılandırması gerektirir. Daha fazla bilgi için [Kestrel belgelerine](xref:fundamentals/servers/kestrel) bakın.

* Sihirbazın geri kalanında **İleri** ' ye tıklayın ve sonda da **kapatın** .

* ASP.NET Core Identity BIR **ad kimliği** talebi gerektiriyor. **Talep kurallarını Düzenle** iletişim kutusundan bir tane ekleyin:

![Talep kurallarını Düzenle](ws-federation/_static/EditClaimRules.png)

* **Dönüşüm talebi kuralı Ekle sihirbazında**, varsayılan **LDAP özniteliklerini talep şablonu olarak gönder** ' i seçili bırakın ve **İleri**' ye tıklayın. **SAM-Account-Name** LDAP ÖZNITELIĞINI **ad kimliği** giden talebine eşleyen bir kural ekleyin:

![Dönüşüm talep kuralı ekleme Sihirbazı: talep kuralını yapılandırma](ws-federation/_static/AddTransformClaimRule.png)

*  >  **Talep kurallarını Düzenle** penceresinde**Tamam** **' a**tıklayın.

### <a name="azure-active-directory"></a>Azure Active Directory

* AAD kiracının uygulama kayıtları dikey penceresine gidin. **Yeni uygulama kaydı**' na tıklayın:

![Azure Active Directory: Uygulama kayıtları](ws-federation/_static/AadNewAppRegistration.png)

* Uygulama kaydı için bir ad girin. Bu, ASP.NET Core uygulaması için önemli değildir.
* Uygulamanın **oturum açma URL 'si**olarak dinlediği URL 'yi girin:

![Azure Active Directory: uygulama kaydı oluşturma](ws-federation/_static/AadCreateAppRegistration.png)

* **Uç noktalar** ' a tıklayın ve **Federasyon meta veri belgesi** URL 'sine göz önünde Bu, WS-Federation ara yazılımı ' `MetadataAddress`dır:

![Azure Active Directory: uç noktalar](ws-federation/_static/AadFederationMetadataDocument.png)

* Yeni uygulama kaydına gidin. **Ayarlar** > **Özellikler** ' e tıklayın ve **uygulama kimliği URI**'sini unutmayın. Bu, WS-Federation ara yazılımı ' `Wtrealm`dır:

![Azure Active Directory: uygulama kaydı özellikleri](ws-federation/_static/AadAppIdUri.png)

## <a name="use-ws-federation-without-aspnet-core-identity"></a>ASP.NET Core olmadan WS-Federation kullanınIdentity

WS-Federation ara yazılımı, olmadan Identitykullanılabilir. Örneğin:
::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/StartupNon21.cs?name=snippet)]
::: moniker-end

## <a name="add-ws-federation-as-an-external-login-provider-for-aspnet-core-identity"></a>WS-Federation ' i ASP.NET Core için dış oturum açma sağlayıcısı olarak ekleyinIdentity

* Projeye [Microsoft. AspNetCore. Authentication. WsFederation](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.WsFederation) bağımlılığı ekleyin.
* WS-Federation Ekle `Startup.ConfigureServices`:

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup31.cs?name=snippet)]
::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"
[!code-csharp[](ws-federation/samples/Startup21.cs?name=snippet)]
::: moniker-end

[!INCLUDE [default settings configuration](social/includes/default-settings.md)]

### <a name="log-in-with-ws-federation"></a>WS-Federation ile oturum açma

Uygulamaya gidin ve NAV üstbilgisindeki **oturum aç** bağlantısına tıklayın. WsFederation: ![oturum açma sayfasında oturum açma seçeneği vardır](ws-federation/_static/WsFederationButton.png)

Sağlayıcı olarak ADFS ile, düğme bir ADFS oturum açma sayfasına yönlendirir: ![ADFS oturum açma sayfası](ws-federation/_static/AdfsLoginPage.png)

Sağlayıcı olarak Azure Active Directory, düğme bir AAD oturum açma sayfasına yeniden yönlendirir: ![AAD oturum açma sayfası](ws-federation/_static/AadSignIn.png)

Yeni bir kullanıcı için başarılı bir oturum açma işlemi, uygulamanın kullanıcı kayıt sayfasına yeniden yönlendiriliyor: ![kayıt sayfası](ws-federation/_static/Register.png)
