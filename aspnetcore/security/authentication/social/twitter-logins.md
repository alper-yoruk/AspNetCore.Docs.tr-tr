---
title: ASP.NET Core ile Twitter harici oturum açma kurulumu
author: rick-anderson
description: Bu öğretici, Twitter hesabı kullanıcı kimlik doğrulamasının mevcut bir ASP.NET Core uygulamasına entegrasyonunu göstermektedir.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 1f5d667e905e49ae05f5aa31bd5b69ad126f6e28
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277294"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>ASP.NET Core ile Twitter harici oturum açma kurulumu

Yazar: [Valeriy Novytskyy](https://github.com/01binary) ve [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu örnek, kullanıcıların [önceki sayfada](xref:security/authentication/social/index)oluşturulan Core 3.0 projesi ASP.NET bir örnek kullanarak [Twitter hesabıyla oturum açmalarını](https://dev.twitter.com/web/sign-in/desktop-browser) nasıl sağlayacaklarını gösterir.

## <a name="create-the-app-in-twitter"></a>Uygulamayı Twitter'da oluşturun

* Projeye [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet paketini ekleyin.

* Gidin [https://apps.twitter.com/](https://apps.twitter.com/) ve oturum açın. Zaten bir Twitter hesabınız yoksa, bir tane oluşturmak için **[şimdi Kaydol](https://twitter.com/signup)** bağlantısını kullanın.

* **Uygulama Oluştur'u**seçin. **Uygulama adını,** **Uygulama açıklamasını** ve genel Web **Sitesi** URI'yi doldurun (alan adını kaydettirene kadar geçici olabilir):

* Twitter ile **Oturum Açma'yı Etkinleştirmek** için yanındaki kutuyu işaretleyin

* Microsoft.AspNetCore.Identity, kullanıcıların varsayılan olarak bir e-posta adresine sahip olmasını gerektirir. **İzinler** sekmesine gidin, **Edit** düğmesini tıklatın ve **kullanıcılardan e-posta adresi isteyin**yanındaki kutuyu işaretleyin.

* **Geri Arama URL'leri** alanına `https://webapp128.azurewebsites.net/signin-twitter` `/signin-twitter` eklenen geliştirme URI'nizi girin (örneğin: ). Daha sonra bu örnekte yapılandırılan Twitter kimlik doğrulama `/signin-twitter` şeması, OAuth akışını uygulamak için rotadaki istekleri otomatik olarak işler.

  > [!NOTE]
  > URI segmenti, `/signin-twitter` Twitter kimlik doğrulama sağlayıcısının varsayılan geri araması olarak ayarlanır. [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) sınıfının devralınan [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) özelliği üzerinden Twitter kimlik doğrulama ara yazılımını yapılandırırken varsayılan geri arama URI'yi değiştirebilirsiniz.

* Formun geri kalanını doldurun ve **Oluştur'u**seçin. Yeni uygulama ayrıntıları görüntülenir:

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>Twitter tüketici API anahtarı nı ve gizlisini saklayın

[Gizli Yöneticisi](xref:security/app-secrets)ile Twitter tüketici API anahtarı ve gizli gibi hassas ayarları saklayın. Bu örnek için aşağıdaki adımları kullanın:

1. [Gizli depolamayı etkinleştir'deki](xref:security/app-secrets#enable-secret-storage)yönergelere göre gizli depolama için projeyi başlatma.
1. Sırlar anahtarları `Authentication:Twitter:ConsumerKey` ile yerel gizli mağazada `Authentication:Twitter:ConsumerSecret`hassas ayarları saklayın ve:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

Bu belirteçler, yeni bir Twitter uygulaması oluşturduktan sonra **Anahtarlar ve Erişim Belirteçleri** sekmesinde bulunabilir:

## <a name="configure-twitter-authentication"></a>Twitter Kimlik Doğrulama'yı yapılandırma

`ConfigureServices` *Startup.cs* dosyasında yöntemde Twitter hizmeti ekleyin:

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Twitter kimlik doğrulaması tarafından desteklenen yapılandırma seçenekleri hakkında daha fazla bilgi için [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API başvurusuna bakın. Bu, kullanıcı hakkında farklı bilgiler istemek için kullanılabilir.

## <a name="sign-in-with-twitter"></a>Twitter ile oturum açın

Uygulamayı çalıştırın ve **Giriş Yap'ı**seçin. Twitter ile oturum açma seçeneği görüntülenir:

**Twitter'a** tıkladığınızda kimlik doğrulaması için Twitter'a yönlendirir:

Twitter kimlik bilgilerinizi girdikten sonra, e-postanızı ayarlayabileceğiniz web sitesine geri yönlendirilirsiniz.

Artık Twitter kimlik bilgilerinizi kullanarak oturum açtınız:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a>Sorun giderme

* **ASP.NET Core 2.x yalnızca:** Kimlik arayarak `services.AddIdentity` `ConfigureServices`yapılandırılmamışsa, kimlik doğrulamaya çalışmak ArgumentException ile *sonuçlanır: 'SignInScheme' seçeneği sağlanmalıdır.* Bu örnekte kullanılan proje şablonu bunun yapılmasını sağlar.
* Site veritabanı ilk geçiş uygulanarak oluşturulmamışsa, *istek hatasını işlerken bir veritabanı işlemi başarısız olur.* Veritabanını oluşturmak için **Geçişleri Uygula'ya** dokunun ve hatayı geride devam etmek için yenileyin.

## <a name="next-steps"></a>Sonraki adımlar

* Bu makale, Twitter ile nasıl doğrulayabilirsiniz gösterdi. [Önceki sayfada](xref:security/authentication/social/index)listelenen diğer sağlayıcılarla kimlik doğrulaması yapmak için benzer bir yaklaşım izleyebilirsiniz.

* Web sitenizi Azure web uygulamasında yayımladıktan sonra, `ConsumerSecret` Twitter geliştirici portalındaki web sitenizi sıfırlamanız gerekir.

* Azure `Authentication:Twitter:ConsumerKey` portalında uygulama ayarlarını ve uygulama `Authentication:Twitter:ConsumerSecret` ayarlarını ayarlayın. Yapılandırma sistemi, ortam değişkenlerinden anahtarları okumak üzere ayarlanmıştır.
