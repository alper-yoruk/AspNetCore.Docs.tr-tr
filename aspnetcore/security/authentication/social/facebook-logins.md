---
title: ASP.NET Core'da Facebook harici giriş kurulumu
author: rick-anderson
description: Facebook hesabı kullanıcı kimlik doğrulamasının varolan bir ASP.NET Core uygulamasına entegrasyonunu gösteren kod örnekleri içeren öğretici.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 9b3128addafb41ad6ec44af5cb12e89607e1ae59
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277307"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>ASP.NET Core'da Facebook harici giriş kurulumu

Yazar: [Valeriy Novytskyy](https://github.com/01binary) ve [Rick Anderson](https://twitter.com/RickAndMSFT)

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

Kod örnekleri içeren bu öğretici, kullanıcılarınızın [önceki sayfada](xref:security/authentication/social/index)oluşturulan Core 3.0 ASP.NET bir örnek kullanarak Facebook hesabıyla oturum açmalarını nasıl sağlayacağını gösterir. [Resmi adımları](https://developers.facebook.com)izleyerek bir Facebook App ID oluşturarak işe başlıyoruz.

## <a name="create-the-app-in-facebook"></a>Uygulamayı Facebook'ta oluşturun

* Projeye [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet paketini ekleyin.

* [Facebook Geliştiricileri uygulama](https://developers.facebook.com/apps/) sayfasına gidin ve oturum açın. Zaten bir Facebook hesabınız yoksa, giriş sayfasındafacebook için **Kaydol** bağlantısını kullanarak bir hesap oluşturun.  Bir Facebook hesabınız olduktan sonra, Facebook Geliştiricisi olarak kaydolmak için talimatları izleyin.

* **Uygulamalarım** menüsünden yeni bir Uygulama Kimliği oluşturmak için **Uygulama Oluştur'u** seçin.

   ![Microsoft Edge'de geliştiriciler portalı için Facebook açıldı](index/_static/FBMyApps.png)

* Formu doldurun ve **Uygulama Kimliği Oluştur** düğmesine dokunun.

  ![Yeni Uygulama Kimliği formu oluşturma](index/_static/FBNewAppId.png)

* Yeni Uygulama kartında **Ürün Ekle'yi**seçin.  Facebook **Giriş** kartında, **Ayarla'yı** tıklatın 

  ![Ürün Kurulumu sayfası](index/_static/FBProductSetup.png)

* **Quickstart** sihirbazı, ilk sayfa olarak **Bir Platform Seç** ile başlatılır. Sol alttaki menüdeki **FaceBook Giriş** **Ayarları** bağlantısını tıklayarak sihirbazı şimdilik atlayın:

  ![Hızlı Başlat'ı Atla](index/_static/FBSkipQuickStart.png)

* **Size Client OAuth Ayarları** sayfası sunulur:

  ![İstemci OAuth Ayarları sayfası](index/_static/FBOAuthSetup.png)

* **Geçerli OAuth Yönlendirme URI** alanına eklenen */signin-facebook* ile geliştirme URI `https://localhost:44320/signin-facebook`girin (örneğin: ). Daha sonra bu öğreticide yapılandırılan Facebook kimlik doğrulaması, OAuth akışını uygulamak için */signin-facebook rotasındaki* istekleri otomatik olarak işler.

> [!NOTE]
> URI */signin-facebook,* Facebook kimlik doğrulama sağlayıcısının varsayılan geri araması olarak ayarlanır. [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) sınıfının devralınan [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) özelliği aracılığıyla Facebook kimlik doğrulama aracını yapılandırırken varsayılan geri arama URI'yi değiştirebilirsiniz.

* **Değişiklikleri Kaydet**’e tıklayın.

* Sol navigasyonda **Ayarlar** > **Temel** bağlantısını tıklatın.

  Bu sayfada, sizin `App ID` ve . `App Secret` Bir sonraki bölümde ASP.NET Core uygulamanıza her ikisini de eklersiniz:

* Siteyi dağıtırken **Facebook Giriş** kurulum sayfasını yeniden ziyaret etmeli ve yeni bir genel URI kaydettirmeniz gerekir.

## <a name="store-the-facebook-app-id-and-secret"></a>Facebook uygulama kimliğini ve gizlisini saklama

[Gizli Yönetici](xref:security/app-secrets)ile Facebook uygulama kimliği ve gizli değerler gibi hassas ayarları saklayın. Bu örnek için aşağıdaki adımları kullanın:

1. [Gizli depolamayı etkinleştir'deki](xref:security/app-secrets#enable-secret-storage)yönergelere göre gizli depolama için projeyi başlatma.
1. Hassas ayarları gizli anahtarlarla `Authentication:Facebook:AppId` yerel gizli mağazada saklayın ve: `Authentication:Facebook:AppSecret`

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>Facebook Kimlik Doğrulamasını Yapılandırma

`ConfigureServices` *Startup.cs* dosyasındaki yöntemde Facebook hizmetini ekleyin:

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a>Facebook ile oturum açın

* Uygulamayı çalıştırın ve **Giriş Yap'ı**seçin. 
* Oturum **açmak için başka bir hizmet kullan altında.**, Facebook'u seçin.
* Kimlik doğrulaması için **Facebook'a** yönlendirilirsiniz.
* Facebook kimlik bilgilerinizi girin.
* E-postanızı ayarlayabildiğiniz sitenize yönlendirilirsiniz.

Artık Facebook kimlik bilgilerinizi kullanarak oturum açtınız:

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a>Harici oturum açma yetkisinin iptaline tepki

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>kullanıcı istenen yetkilendirme talebini onaylamadığında kullanıcı aracısına yönlendirme yolu sağlayabilir.

Aşağıdaki kod şu `AccessDeniedPath` `"/AccessDeniedPathInfo"`şekilde ayarlar:

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

Sayfanın `AccessDeniedPath` aşağıdaki bilgileri içermesini öneririz:

*  Uzaktan kimlik doğrulama iptal edildi.
* Bu uygulama kimlik doğrulaması gerektirir.
* Yeniden oturum açmayı denemek için Giriş bağlantısını seçin.

### <a name="test-accessdeniedpath"></a>Test AccessDeniedPath

* [facebook.com'ye](https://www.facebook.com/) gidin
* Oturum açmışsanız, oturum açmanız gerekir.
* Uygulamayı çalıştırın ve Facebook oturum aç'ını seçin.
* **Şimdi Değil'i**seçin. Belirtilen `AccessDeniedPath` sayfaya yönlendirilirsiniz.

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Facebook kimlik doğrulaması tarafından desteklenen yapılandırma seçenekleri hakkında daha fazla bilgi için [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API başvurusuna bakın. Yapılandırma seçenekleri şu şekilde kullanılabilir:

* Kullanıcı hakkında farklı bilgiler isteyin.
* Oturum açma deneyimini özelleştirmek için sorgu dizebağımsız değişkenleri ekleyin.

## <a name="troubleshooting"></a>Sorun giderme

* **ASP.NET Core 2.x yalnızca:** Kimlik arayarak `services.AddIdentity` `ConfigureServices`yapılandırılmamışsa, kimlik doğrulamaya çalışmak ArgumentException ile *sonuçlanır: 'SignInScheme' seçeneği sağlanmalıdır.* Bu öğreticide kullanılan proje şablonu bunun yapılmasını sağlar.
* Site veritabanı ilk geçiş uygulanarak oluşturulmamışsa, *istek hatasını işlerken bir veritabanı işlemi başarısız olur.* Veritabanını oluşturmak için **Geçişleri Uygula'ya** dokunun ve hatayı geride devam etmek için yenileyin.

## <a name="next-steps"></a>Sonraki adımlar

* Bu makalede, Facebook ile kimlik doğrulaması nasıl gösterilebilir. [Önceki sayfada](xref:security/authentication/social/index)listelenen diğer sağlayıcılarla kimlik doğrulaması yapmak için benzer bir yaklaşım izleyebilirsiniz.

* Web sitenizi Azure web uygulamasında yayımladıktan sonra, `AppSecret` Facebook geliştirici portalındaki web sitesini sıfırlamanız gerekir.

* Azure `Authentication:Facebook:AppId` portalında uygulama ayarlarını ve uygulama `Authentication:Facebook:AppSecret` ayarlarını ayarlayın. Yapılandırma sistemi, ortam değişkenlerinden anahtarları okumak üzere ayarlanmıştır.
