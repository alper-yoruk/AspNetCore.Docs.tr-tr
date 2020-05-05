---
title: ASP.NET Core ile Microsoft hesabı dış oturum açma kurulumu
author: rick-anderson
description: Bu örnek, Microsoft hesabı kullanıcı kimlik doğrulamasının mevcut bir ASP.NET Core uygulamasına tümleştirilmesini gösterir.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 731a17085a1fd01852bb3fe2f0fc9f3e7a9ac30f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775667"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>ASP.NET Core ile Microsoft hesabı dış oturum açma kurulumu

Tarafından [Valeriy Novyıtskyy](https://github.com/01binary) ve [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu örnek, [önceki sayfada](xref:security/authentication/social/index)oluşturulan ASP.NET Core 3,0 projesini kullanarak, kullanıcıların iş, okul veya kişisel Microsoft hesabı oturum açmasını nasıl olanaklı hale kullanabileceğinizi gösterir.

## <a name="create-the-app-in-microsoft-developer-portal"></a>Uygulamayı Microsoft Geliştirici Portalında oluşturma

* Projeye [Microsoft. AspNetCore. Authentication. MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet paketini ekleyin.
* [Azure portal uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) sayfasına gidin ve bir Microsoft hesabı oluşturun veya oturum açın:

Microsoft hesabı yoksa **bir tane oluştur**' u seçin. Oturum açtıktan sonra, **uygulama kayıtları** sayfasına yönlendirilirsiniz:

* **Yeni kayıt** Seç
* Bir **ad**girin.
* **Desteklenen hesap türleri**için bir seçenek belirleyin.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* **Yeniden yönlendirme URI 'si**altında, `/signin-microsoft` eklenen geliştirme URL 'nizi girin. Örneğin, `https://localhost:5001/signin-microsoft`. Bu örnekte daha sonra yapılandırılan Microsoft kimlik doğrulama şeması, OAuth akışını uygulamak için `/signin-microsoft` rotadaki istekleri otomatik olarak işleymeyecektir.
* **Kaydol** ' u seçin

### <a name="create-client-secret"></a>İstemci parolası oluştur

* Sol bölmede **sertifikalar & gizlilikler**' ı seçin.
* **İstemci gizli**dizileri altında **yeni istemci parolası** ' nı seçin.

  * İstemci parolası için bir açıklama ekleyin.
  * **Ekle** düğmesini seçin.

* **İstemci**gizli dizileri altında, istemci parolasının değerini kopyalayın.

URI segmenti `/signin-microsoft` , Microsoft kimlik doğrulama sağlayıcısı 'nın varsayılan geri çağırması olarak ayarlanır. Microsoft kimlik doğrulama ara yazılımını, [Microsoftaccountoptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) sınıfının devralınmış [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) özelliği aracılığıyla YAPıLANDıRıRKEN varsayılan geri çağırma URI 'sini değiştirebilirsiniz.

## <a name="store-the-microsoft-client-id-and-secret"></a>Microsoft istemci KIMLIĞINI ve gizli anahtarını depolayın

[Gizli yönetici](xref:security/app-secrets)ile MICROSOFT istemci kimliği ve gizli anahtar değerleri gibi hassas ayarları depolayın. Bu örnek için aşağıdaki adımları kullanın:

1. [Gizli depolamayı etkinleştirme](xref:security/app-secrets#enable-secret-storage)konusundaki yönergeler temelinde projeyi gizli depolama için başlatın.
1. Gizli anahtar ayarlarını gizli anahtarlar ve `Authentication:Microsoft:ClientId` `Authentication:Microsoft:ClientSecret`gizli anahtarlar ile yerel gizli dizi deposunda depolayın:

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Microsoft hesabı kimlik doğrulamasını yapılandırma

Microsoft hesabı hizmetini öğesine ekleyin `Startup.ConfigureServices`:

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

Microsoft hesabı kimlik doğrulaması tarafından desteklenen yapılandırma seçenekleri hakkında daha fazla bilgi için bkz. [Microsoftaccountoptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API başvurusu. Bu, Kullanıcı hakkında farklı bilgiler istemek için kullanılabilir.

## <a name="sign-in-with-microsoft-account"></a>Microsoft hesabıyla oturum açın hesabı

Uygulamayı çalıştırın ve **oturum aç**' a tıklayın. Microsoft ile oturum açma seçeneği görüntülenir. Microsoft 'a tıkladığınızda, kimlik doğrulaması için Microsoft 'a yönlendirilirsiniz. Microsoft hesabınızla oturum açtıktan sonra uygulamanın bilgilerinize erişmesine izin vermek isteyip istemediğiniz sorulur:

**Evet** ' e dokunduktan sonra, e-postanızı ayarlayabileceğiniz Web sitesine geri yönlendirilirsiniz.

Şu anda Microsoft kimlik bilgilerinizi kullanarak oturum açtınız:

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Sorun giderme

* Microsoft hesabı sağlayıcısı sizi bir oturum açma hatası sayfasına yönlendirirse, URI 'deki `#` (hashtag) hata başlığına ve açıklama sorgu dizesi parametrelerine göz önüne alın.

  Hata iletisi Microsoft kimlik doğrulamasıyla ilgili bir sorun olduğunu gösteriyorsa, en sık karşılaşılan neden, uygulama URI 'niz **Web** platformu Için belirtilen **yeniden yönlendirme URI** 'lerinden hiçbiriyle eşleşmemedir.
* Identity ' De `services.AddIdentity` `ConfigureServices`çağırarak yapılandırılmamışsa, kimlik doğrulamaya çalışmak ArgumentException Ile sonuçlanır *: ' signınscheme ' seçeneği sağlanmalıdır*. Bu örnekte kullanılan proje şablonu bunun yapılmasını sağlar.
* Site veritabanı ilk geçiş uygulanarak oluşturulmadıysa, *istek hatasını Işlerken bir veritabanı işlemi başarısız* olur. Veritabanını oluşturmak için **geçişleri Uygula** ' ya dokunun ve hatanın ötesinde devam etmek için yenileyin.

## <a name="next-steps"></a>Sonraki adımlar

* Bu makale, Microsoft ile nasıl kimlik doğrulayacağınızı gösterdi. [Önceki sayfada](xref:security/authentication/social/index)listelenen diğer sağlayıcılarla kimlik doğrulaması yapmak için benzer bir yaklaşımı izleyebilirsiniz.

* Web sitenizi Azure Web App 'e yayımladığınızda, Microsoft Geliştirici Portalında yeni bir istemci gizli dizileri oluşturun.

* `Authentication:Microsoft:ClientId` Ve `Authentication:Microsoft:ClientSecret` Azure Portal uygulama ayarları olarak ayarlayın. Yapılandırma sistemi, ortam değişkenlerinden anahtarları okumak üzere ayarlanır.
