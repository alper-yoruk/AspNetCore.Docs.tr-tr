---
title: ASP.NET Core 'da Facebook dış oturum açma kurulumu
author: rick-anderson
description: Facebook hesabı kullanıcı kimlik doğrulamasının mevcut bir ASP.NET Core uygulamasına tümleştirilmesini gösteren kod örnekleri ile öğretici.
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/facebook-logins
ms.openlocfilehash: df91b6f324de70b8492ccf0aef74c9264c3e9711
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403956"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>ASP.NET Core 'da Facebook dış oturum açma kurulumu

Tarafından [Valeriy Novyıtskyy](https://github.com/01binary) ve [Rick Anderson](https://twitter.com/RickAndMSFT)

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

Kod örnekleri ile bu öğreticide, kullanıcılarınızın [önceki sayfada](xref:security/authentication/social/index)oluşturulmuş bir örnek ASP.NET Core 3,0 projesi kullanarak Facebook hesabıyla oturum açmasını nasıl etkinleştireceğinizi gösterilmektedir. [Resmi adımları](https://developers.facebook.com)Izleyerek Facebook uygulama kimliği oluşturarak başladık.

## <a name="create-the-app-in-facebook"></a>Facebook 'ta uygulama oluşturma

* Projeye [Microsoft. AspNetCore. Authentication. Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet paketini ekleyin.

* [Facebook geliştiricileri uygulama](https://developers.facebook.com/apps/) sayfasına gidin ve oturum açın. Henüz bir Facebook hesabınız yoksa, bir tane oluşturmak için oturum açma sayfasındaki **Facebook 'A kaydolun** bağlantısını kullanın.  Facebook hesabınız olduğunda, Facebook geliştiricisi olarak kaydolmak için yönergeleri izleyin.

* Yeni bir uygulama KIMLIĞI oluşturmak için **uygulamalarım** menüsünde **uygulama oluştur** ' u seçin.

   ![Microsoft Edge 'de açık geliştiriciler portalı için Facebook](index/_static/FBMyApps.png)

* Formu doldurun ve **uygulama kimliği oluştur** düğmesine dokunun.

  ![Yeni bir uygulama KIMLIĞI formu oluşturun](index/_static/FBNewAppId.png)

* Yeni uygulama kartında **Ürün Ekle**' yi seçin.  **Facebook oturum açma** kartında **Ayarla** ' ya tıklayın. 

  ![Ürün kurulum sayfası](index/_static/FBProductSetup.png)

* **Hızlı başlangıç** Sihirbazı, ilk sayfa olarak **bir platform Seç** ile başlatılır. Sol alt taraftaki menüdeki **Facebook oturum açma** **ayarları** bağlantısına tıklayarak Sihirbazı Şimdi atlayın:

  ![hızlı başlangıç atla](index/_static/FBSkipQuickStart.png)

* **Istemci OAuth ayarları** sayfası görüntülenir:

  ![İstemci OAuth ayarları sayfası](index/_static/FBOAuthSetup.png)

* **Geçerli OAuth yeniden yönlendirme URI 'leri** alanına */SignIn-Facebook* eklenmiş olan geliştirme URI 'nizi girin (örneğin: `https://localhost:44320/signin-facebook` ). Bu öğreticide daha sonra yapılandırılan Facebook kimlik doğrulaması, OAuth akışını uygulamak için */SignIn-Facebook* rotasındaki istekleri otomatik olarak işleymeyecektir.

> [!NOTE]
> URI */SignIn-Facebook* , Facebook kimlik doğrulama sağlayıcısı 'nın varsayılan geri çağırması olarak ayarlanır. Facebook kimlik doğrulama ara yazılımını, çok [yönlü önyükleme](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) sınıfının devralınan [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) özelliği aracılığıyla YAPıLANDıRıRKEN varsayılan geri çağırma URI 'sini değiştirebilirsiniz.

* **Değişiklikleri Kaydet**’e tıklayın.

* **Settings**  >  Sol gezinti bölmesinde ayarlar**temel** bağlantısı ' na tıklayın.

  Bu sayfada, ve bilgilerinizi bir yere unutmayın `App ID` `App Secret` . Bir sonraki bölümde ASP.NET Core uygulamanıza her ikisini de ekleyeceksiniz:

* Siteyi dağıttığınızda **Facebook oturum açma** kurulumu sayfasını yeniden ziyaret etmeniz ve yeni BIR ortak URI kaydetmeniz gerekir.

## <a name="store-the-facebook-app-id-and-secret"></a>Facebook uygulama KIMLIĞI ve gizli anahtarını depolayın

Facebook uygulama KIMLIĞI ve gizli anahtar değerleri gibi hassas ayarları [gizli bir yöneticiye](xref:security/app-secrets)depolayın. Bu örnek için aşağıdaki adımları kullanın:

1. [Gizli depolamayı etkinleştirme](xref:security/app-secrets#enable-secret-storage)konusundaki yönergeler temelinde projeyi gizli depolama için başlatın.
1. Gizli anahtar ayarlarını gizli anahtarlar ve gizli anahtarlar ile yerel gizli dizi deposunda `Authentication:Facebook:AppId` depolayın `Authentication:Facebook:AppSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>Facebook kimlik doğrulamasını yapılandırma

`ConfigureServices` *Startup.cs* dosyasındaki yönteme Facebook hizmetini ekleyin:

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a>Facebook ile oturum açma

* Uygulamayı çalıştırın ve **oturum aç '** ı seçin. 
* **Oturum açmak için başka bir hizmet kullan**' ın altında Facebook ' ı seçin.
* Kimlik doğrulaması için **Facebook** 'a yönlendirilirsiniz.
* Facebook kimlik bilgilerinizi girin.
* Sitenize geri yönlendirilirsiniz ve e-postanızı ayarlayabiliyorsunuz.

Artık Facebook kimlik bilgilerinizi kullanarak oturumunuz açıldı:

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a>Yetkilendirmeyi dış oturum açma işlemini iptal etme tepki verme

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>Kullanıcı İstenen Yetkilendirme talebini onaylamazsa kullanıcı aracısına yeniden yönlendirme yolu sağlayabilir.

Aşağıdaki kod öğesini olarak ayarlar `AccessDeniedPath` `"/AccessDeniedPathInfo"` :

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

`AccessDeniedPath`Sayfada aşağıdaki bilgileri içermesi önerilir:

*  Uzaktan kimlik doğrulama iptal edildi.
* Bu uygulama kimlik doğrulaması gerektiriyor.
* Yeniden oturum açmayı denemek için oturum açma bağlantısını seçin.

### <a name="test-accessdeniedpath"></a>Test AccessDeniedPath

* [Facebook.com](https://www.facebook.com/) adresine gidin
* Oturum açtıysanız oturumunuzu kapatmalısınız.
* Uygulamayı çalıştırın ve Facebook oturum açma ' yı seçin.
* **Şimdi değil**' i seçin. Belirtilen `AccessDeniedPath` sayfaya yönlendirilirsiniz.

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

Facebook kimlik doğrulaması tarafından desteklenen yapılandırma seçenekleri hakkında daha fazla bilgi için bkz. çok [yönlü Bookoptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API başvurusu. Yapılandırma seçenekleri şu şekilde kullanılabilir:

* Kullanıcı hakkında farklı bilgiler isteyin.
* Oturum açma deneyimini özelleştirmek için sorgu dizesi bağımsız değişkenleri ekleyin.

## <a name="troubleshooting"></a>Sorun giderme

* **Yalnızca 2. x ASP.NET Core:** Identity `services.AddIdentity` ' De çağırarak yapılandırılmamışsa `ConfigureServices` , kimlik doğrulamaya çalışmak ArgumentException ile sonuçlanır *: ' signınscheme ' seçeneği sağlanmalıdır*. Bu öğreticide kullanılan proje şablonu bunun yapılmasını sağlar.
* Site veritabanı ilk geçiş uygulanarak oluşturulmadıysa, *istek hatasını Işlerken bir veritabanı işlemi başarısız oldu* . Veritabanını oluşturmak için **geçişleri Uygula** ' ya dokunun ve hatanın ötesinde devam etmek için yenileyin.

## <a name="next-steps"></a>Sonraki adımlar

* Bu makalede Facebook ile kimlik doğrulaması yapabilirsiniz. [Önceki sayfada](xref:security/authentication/social/index)listelenen diğer sağlayıcılarla kimlik doğrulaması yapmak için benzer bir yaklaşımı izleyebilirsiniz.

* Web sitenizi Azure Web App 'e yayımladığınızda, Facebook Geliştirici portalındaki ' ı sıfırlamanız gerekir `AppSecret` .

* `Authentication:Facebook:AppId`Ve `Authentication:Facebook:AppSecret` Azure Portal uygulama ayarları olarak ayarlayın. Yapılandırma sistemi, ortam değişkenlerinden anahtarları okumak üzere ayarlanır.
