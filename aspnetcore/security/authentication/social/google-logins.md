---
title: ASP.NET Core 'de Google dış oturum açma kurulumu
author: rick-anderson
description: Bu öğreticide, Google hesabı kullanıcı kimlik doğrulamasının mevcut bir ASP.NET Core uygulamasına tümleştirilmesi gösterilmektedir.
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/19/2020
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
uid: security/authentication/google-logins
ms.openlocfilehash: 7a10b66be7f4dda25f94437b55bad9746bda97e0
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020489"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>ASP.NET Core 'de Google dış oturum açma kurulumu

Tarafından [Valeriy Novyıtskyy](https://github.com/01binary) ve [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu öğreticide, kullanıcıların [önceki sayfada](xref:security/authentication/social/index)oluşturulan ASP.NET Core 3,0 projesini kullanarak Google hesabıyla oturum açmasını nasıl etkinleştireceğinizi gösterilmektedir.

## <a name="create-a-google-api-console-project-and-client-id"></a>Google API konsol projesi ve istemci KIMLIĞI oluşturma

* [Microsoft. AspNetCore. Authentication. Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google)'ı yükler.
* [Google oturum açma ile Web uygulamanızı tümleştirme](https://developers.google.com/identity/sign-in/web/sign-in) ve **Proje yapılandırma**' yı seçme bölümüne gidin.
* **OAuth Istemcinizi yapılandırın** Iletişim kutusunda **Web sunucusu**' nu seçin.
* **Yetkili yeniden yönlendirme URI 'leri** metin girişi kutusunda, YENIDEN yönlendirme URI 'sini ayarlayın. Örneğin, `https://localhost:44312/signin-google`
* **ISTEMCI kimliğini** ve **istemci parolasını**kaydedin.
* Siteyi dağıttığınızda, **Google konsolundan**yeni ortak URL 'yi kaydedin.

## <a name="store-the-google-client-id-and-secret"></a>Google istemci KIMLIĞINI ve gizli anahtarını depolayın

Google istemci KIMLIĞI ve gizli anahtar değerleri gibi gizli ayarları [gizli yönetici](xref:security/app-secrets)olarak depolayın. Bu örnek için aşağıdaki adımları kullanın:

1. [Gizli depolamayı etkinleştirme](xref:security/app-secrets#enable-secret-storage)konusundaki yönergeler temelinde projeyi gizli depolama için başlatın.
1. Gizli anahtar ayarlarını gizli anahtarlar ve gizli anahtarlar ile yerel gizli dizi deposunda `Authentication:Google:ClientId` depolayın `Authentication:Google:ClientSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

API [konsolunuzun](https://console.developers.google.com/apis/dashboard)API kimlik bilgilerini ve kullanımını yönetebilirsiniz.

## <a name="configure-google-authentication"></a>Google kimlik doğrulamasını yapılandırma

Google hizmetini şu şekilde ekleyin `Startup.ConfigureServices` :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>Google ile oturum açın

* Uygulamayı çalıştırın ve **oturum aç**' a tıklayın. Google ile oturum açma seçeneği görüntülenir.
* Kimlik doğrulaması için Google 'a yönlendiren **Google** düğmesine tıklayın.
* Google kimlik bilgilerinizi girdikten sonra, Web sitesine geri yönlendirilirsiniz.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions>Google kimlik doğrulaması tarafından desteklenen yapılandırma seçenekleri hakkında daha fazla bilgi için bkz. API başvurusu. Bu, Kullanıcı hakkında farklı bilgiler istemek için kullanılabilir.

## <a name="change-the-default-callback-uri"></a>Varsayılan geri çağırma URI 'sini değiştirme

URI segmenti, `/signin-google` Google kimlik doğrulama sağlayıcısı 'nın varsayılan geri çağırması olarak ayarlanır. [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) sınıfının devralınmış [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) özelliği aracılığıyla Google kimlik doğrulama ara yazılımını YAPıLANDıRıRKEN varsayılan geri çağırma URI 'sini değiştirebilirsiniz.

## <a name="troubleshooting"></a>Sorun giderme

* Oturum açma işe yaramazsa ve herhangi bir hata alamıyorsanız, sorunu ayıklamanın daha kolay olması için geliştirme moduna geçin.
* IdentityÇağrısı yaparak yapılandırılmamışsa `services.AddIdentity` `ConfigureServices` , ArgumentException 'de sonuçların kimlik doğrulaması deneniyor *: ' signınscheme ' seçeneğinin sağlanması gerekir*. Bu öğreticide kullanılan proje şablonu bunun yapılmasını sağlar.
* Site veritabanı ilk geçiş uygulanarak oluşturulmadıysa, *istek hatasını Işlerken bir veritabanı işlemi başarısız oldu* . Veritabanını oluşturmak için **geçişleri Uygula** ' yı seçin ve hatanın ötesinde devam etmek için sayfayı yenileyin.

## <a name="next-steps"></a>Sonraki adımlar

* Bu makalede, Google ile kimlik doğrulaması yapabilirsiniz. [Önceki sayfada](xref:security/authentication/social/index)listelenen diğer sağlayıcılarla kimlik doğrulaması yapmak için benzer bir yaklaşımı izleyebilirsiniz.
* Uygulamayı Azure 'da yayımladıktan sonra `ClientSecret` Google API konsolundaki öğesini sıfırlayın.
* `Authentication:Google:ClientId`Ve `Authentication:Google:ClientSecret` Azure Portal uygulama ayarları olarak ayarlayın. Yapılandırma sistemi, ortam değişkenlerinden anahtarları okumak üzere ayarlanır.
