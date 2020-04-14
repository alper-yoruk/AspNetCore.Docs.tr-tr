---
title: ASP.NET Core ile Microsoft Hesabı harici giriş kurulumu
author: rick-anderson
description: Bu örnek, Microsoft hesabı kullanıcı kimlik doğrulamasının varolan bir ASP.NET Core uygulamasına entegrasyonunu gösterir.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 32315267e0672b0747917228f08591a15e4449f8
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277255"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>ASP.NET Core ile Microsoft Hesabı harici giriş kurulumu

Yazar: [Valeriy Novytskyy](https://github.com/01binary) ve [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu örnek, önceki [sayfada](xref:security/authentication/social/index)oluşturulan ASP.NET Core 3.0 projesini kullanarak kullanıcıların Microsoft hesaplarıyla oturum açmalarını nasıl sağlayacağınızı gösterir.

## <a name="create-the-app-in-microsoft-developer-portal"></a>Uygulamayı Microsoft Geliştirici Portalı'nda oluşturun

* Projeye [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet paketini ekleyin.
* [Azure portalına](https://go.microsoft.com/fwlink/?linkid=2083908) gidin - Uygulama kayıtları sayfasına gidin ve bir Microsoft hesabı oluşturun veya oturum açın:

Microsoft hesabınız yoksa, bir tane **oluştur'u**seçin. Oturum açmadan **sonra, Uygulama kayıtları** sayfasına yönlendirilirsiniz:

* **Yeni kayıt** seçin
* Bir **Ad**girin.
* Desteklenen hesap **türleri**için bir seçenek seçin.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* **Redirect URI**altında, `/signin-microsoft` eklenen geliştirme URL'nizi girin. Örneğin, `https://localhost:5001/signin-microsoft`. Bu örnekte daha sonra yapılandırılan Microsoft kimlik doğrulama `/signin-microsoft` düzeni, OAuth akışını uygulamak için rotadaki istekleri otomatik olarak işler.
* **Kaydol'u** Seçin

### <a name="create-client-secret"></a>İstemci sırrı oluşturma

* Sol bölmede, **Sertifikalar & sırları**seçin.
* **İstemci sırları**altında, **Yeni istemci sırrını** seçin

  * İstemci sırrı için bir açıklama ekleyin.
  * **Ekle** düğmesini seçin.

* **İstemci sırları**altında, istemci sırrının değerini kopyalayın.

URI kesimi, `/signin-microsoft` Microsoft kimlik doğrulama sağlayıcısının varsayılan geri araması olarak ayarlanır. [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) sınıfının devralınan [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) özelliği aracılığıyla Microsoft kimlik doğrulama aracını yapılandırırken varsayılan geri arama URI'sini değiştirebilirsiniz.

## <a name="store-the-microsoft-client-id-and-secret"></a>Microsoft istemci kimliğini ve gizlisini depolama

Microsoft istemci kimliği ve gizli değerler gibi hassas ayarları [Gizli Yönetici](xref:security/app-secrets)ile saklayın. Bu örnek için aşağıdaki adımları kullanın:

1. [Gizli depolamayı etkinleştir'deki](xref:security/app-secrets#enable-secret-storage)yönergelere göre gizli depolama için projeyi başlatma.
1. Hassas ayarları gizli anahtarlarla `Authentication:Microsoft:ClientId` yerel gizli mağazada saklayın ve: `Authentication:Microsoft:ClientSecret`

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Microsoft Hesap Kimlik Doğrulamasını Yapılandırma

Microsoft Hesabı hizmetini aşağıdakilere `Startup.ConfigureServices`ekleyin:

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

Microsoft Hesabı kimlik doğrulaması tarafından desteklenen yapılandırma seçenekleri hakkında daha fazla bilgi için [Microsoft AccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API başvurusuna bakın. Bu, kullanıcı hakkında farklı bilgiler istemek için kullanılabilir.

## <a name="sign-in-with-microsoft-account"></a>Microsoft Hesabı ile oturum açın

Uygulamayı çalıştırın ve **Giriş Yap'a**tıklayın. Microsoft ile oturum açma seçeneği görüntülenir. Microsoft'u tıklattığınızda, kimlik doğrulaması için Microsoft'a yönlendirilirsiniz. Microsoft Hesabınızla oturum açtıktan sonra, uygulamanın bilgilerinize erişmesine izin vermeniz istenir:

**Evet'e** dokunduğunuzda e-postanızı ayarlayabileceğiniz web sitesine geri yönlendirilirsiniz.

Artık Microsoft kimlik bilgilerinizi kullanarak oturum açtınız:

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Sorun giderme

* Microsoft Hesabı sağlayıcısı sizi bir oturum açma hatası sayfasına yönlendirirse, Uri'deki `#` (hashtag) doğrudan aşağıdaki hata başlığı ve açıklama sorgusu dize parametrelerine dikkat edin.

  Hata iletisi Microsoft kimlik doğrulamasıyla ilgili bir sorun olduğunu gösterse de, en yaygın neden uygulamanız Uri'nin **Web** platformu için belirtilen **Yönlendirme URL'lerinden** herhangi biriyle eşleşmemesidir.
* Kimlik arayarak `services.AddIdentity` `ConfigureServices`yapılandırılmamışsa, kimlik doğrulamaya çalışmak ArgumentException ile *sonuçlanır: 'SignInScheme' seçeneği sağlanmalıdır.* Bu örnekte kullanılan proje şablonu bunun yapılmasını sağlar.
* Site veritabanı ilk geçiş uygulanarak oluşturulmamışsa, *istek hatasını işlerken bir veritabanı işlemi başarısız olur.* Veritabanını oluşturmak için **Geçişleri Uygula'ya** dokunun ve hatayı geride devam etmek için yenileyin.

## <a name="next-steps"></a>Sonraki adımlar

* Bu makalede, Microsoft ile kimlik doğrulaması nasıl gösterilebilir. [Önceki sayfada](xref:security/authentication/social/index)listelenen diğer sağlayıcılarla kimlik doğrulaması yapmak için benzer bir yaklaşım izleyebilirsiniz.

* Web sitenizi Azure web uygulamasında yayımladıktan sonra Microsoft Geliştirici Portalı'nda yeni bir istemci sırları oluşturun.

* Azure `Authentication:Microsoft:ClientId` portalında uygulama ayarlarını ve uygulama `Authentication:Microsoft:ClientSecret` ayarlarını ayarlayın. Yapılandırma sistemi, ortam değişkenlerinden anahtarları okumak üzere ayarlanmıştır.
