---
title: ASP.NET Core ile Microsoft hesabı dış oturum açma kurulumu
author: rick-anderson
description: Bu örnek, Microsoft hesabı kullanıcı kimlik doğrulamasının mevcut bir ASP.NET Core uygulamasına tümleştirilmesini gösterir.
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 3161e4f0f735294d69dd51634b424d1ed573e615
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060306"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>ASP.NET Core ile Microsoft hesabı dış oturum açma kurulumu

Tarafından [Valeriy Novyıtskyy](https://github.com/01binary) ve [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu örnek, [önceki sayfada](xref:security/authentication/social/index)oluşturulan ASP.NET Core 3,0 projesini kullanarak, kullanıcıların iş, okul veya kişisel Microsoft hesabı oturum açmasını nasıl olanaklı hale kullanabileceğinizi gösterir.

## <a name="create-the-app-in-microsoft-developer-portal"></a>Uygulamayı Microsoft Geliştirici Portalında oluşturma

* Projeye [Microsoft. AspNetCore. Authentication. MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet paketini ekleyin.
* [Azure portal uygulama kayıtları](https://go.microsoft.com/fwlink/?linkid=2083908) sayfasına gidin ve bir Microsoft hesabı oluşturun veya oturum açın:

Microsoft hesabı yoksa **bir tane oluştur** ' u seçin. Oturum açtıktan sonra, **uygulama kayıtları** sayfasına yönlendirilirsiniz:

* **Yeni kayıt** Seç
* Bir **ad** girin.
* **Desteklenen hesap türleri** için bir seçenek belirleyin.  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
  * `MicrosoftAccount`Paket, varsayılan olarak "herhangi bir kuruluş dizinindeki hesaplar" veya "herhangi bir kurumsal dizin ve Microsoft hesabı Içindeki hesaplar" seçenekleri kullanılarak oluşturulan uygulama kayıtlarını destekler.
  * Diğer seçenekleri kullanmak için, `AuthorizationEndpoint` `TokenEndpoint` `MicrosoftAccountOptions` Microsoft hesabı kimlik doğrulamasını, oluşturulduktan sonra uygulama kaydının **uç noktalar** sayfasında görüntülenir ( **genel bakış** sayfasındaki uç noktalar ' a tıklayarak kullanılabilir).
* **Yeniden yönlendirme URI 'si** altında, eklenen geliştirme URL 'nizi girin `/signin-microsoft` . Örneğin, `https://localhost:5001/signin-microsoft`. Bu örnekte daha sonra yapılandırılan Microsoft kimlik doğrulama şeması, `/signin-microsoft` OAuth akışını uygulamak için rotadaki istekleri otomatik olarak işleymeyecektir.
* **Kaydol** ' u seçin

### <a name="create-client-secret"></a>İstemci parolası oluştur

* Sol bölmede **sertifikalar & gizlilikler** ' ı seçin.
* **İstemci gizli** dizileri altında **yeni istemci parolası** ' nı seçin.

  * İstemci parolası için bir açıklama ekleyin.
  * **Ekle** düğmesini seçin.

* **İstemci** gizli dizileri altında, istemci parolasının değerini kopyalayın.

URI segmenti, `/signin-microsoft` Microsoft kimlik doğrulama sağlayıcısı 'nın varsayılan geri çağırması olarak ayarlanır. Microsoft kimlik doğrulama ara yazılımını, [Microsoftaccountoptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) sınıfının devralınmış [remoteauthenticationoptions. callbackpath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) özelliği aracılığıyla YAPıLANDıRıRKEN varsayılan geri çağırma URI 'sini değiştirebilirsiniz.

## <a name="store-the-microsoft-client-id-and-secret"></a>Microsoft istemci KIMLIĞINI ve gizli anahtarını depolayın

[Gizli yönetici](xref:security/app-secrets)ile MICROSOFT istemci kimliği ve gizli anahtar değerleri gibi hassas ayarları depolayın. Bu örnek için aşağıdaki adımları kullanın:

1. [Gizli depolamayı etkinleştirme](xref:security/app-secrets#enable-secret-storage)konusundaki yönergeler temelinde projeyi gizli depolama için başlatın.
1. Gizli anahtar ayarlarını gizli anahtarlar ve gizli anahtarlar ile yerel gizli dizi deposunda `Authentication:Microsoft:ClientId` depolayın `Authentication:Microsoft:ClientSecret` :

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>Microsoft hesabı kimlik doğrulamasını yapılandırma

Microsoft hesabı hizmetini öğesine ekleyin `Startup.ConfigureServices` :

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

Microsoft hesabı kimlik doğrulaması tarafından desteklenen yapılandırma seçenekleri hakkında daha fazla bilgi için bkz. [Microsoftaccountoptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API başvurusu. Bu, Kullanıcı hakkında farklı bilgiler istemek için kullanılabilir.

## <a name="sign-in-with-microsoft-account"></a>Microsoft hesabıyla oturum açın hesabı

Uygulamayı çalıştırın ve **oturum aç** ' a tıklayın. Microsoft ile oturum açma seçeneği görüntülenir. Microsoft 'a tıkladığınızda, kimlik doğrulaması için Microsoft 'a yönlendirilirsiniz. Microsoft hesabınızla oturum açtıktan sonra uygulamanın bilgilerinize erişmesine izin vermek isteyip istemediğiniz sorulur:

**Evet** ' e dokunduktan sonra, e-postanızı ayarlayabileceğiniz Web sitesine geri yönlendirilirsiniz.

Şu anda Microsoft kimlik bilgilerinizi kullanarak oturum açtınız:

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>Sorun giderme

* Microsoft hesabı sağlayıcısı sizi bir oturum açma hatası sayfasına yönlendirirse, `#` URI 'deki (hashtag) hata başlığına ve açıklama sorgu dizesi parametrelerine göz önüne alın.

  Hata iletisi Microsoft kimlik doğrulamasıyla ilgili bir sorun olduğunu gösteriyorsa, en sık karşılaşılan neden, uygulama URI 'niz **Web** platformu Için belirtilen **yeniden yönlendirme URI** 'lerinden hiçbiriyle eşleşmemedir.
* Identity `services.AddIdentity` ' De çağırarak yapılandırılmamışsa `ConfigureServices` , kimlik doğrulamaya çalışmak ArgumentException ile sonuçlanır *: ' signınscheme ' seçeneği sağlanmalıdır* . Bu örnekte kullanılan proje şablonu bunun yapılmasını sağlar.
* Site veritabanı ilk geçiş uygulanarak oluşturulmadıysa, *istek hatasını Işlerken bir veritabanı işlemi başarısız* olur. Veritabanını oluşturmak için **geçişleri Uygula** ' ya dokunun ve hatanın ötesinde devam etmek için yenileyin.

## <a name="next-steps"></a>Sonraki adımlar

* Bu makale, Microsoft ile nasıl kimlik doğrulayacağınızı gösterdi. [Önceki sayfada](xref:security/authentication/social/index)listelenen diğer sağlayıcılarla kimlik doğrulaması yapmak için benzer bir yaklaşımı izleyebilirsiniz.

* Web sitenizi Azure Web App 'e yayımladığınızda, Microsoft Geliştirici Portalında yeni bir istemci gizli dizileri oluşturun.

* `Authentication:Microsoft:ClientId`Ve `Authentication:Microsoft:ClientSecret` Azure Portal uygulama ayarları olarak ayarlayın. Yapılandırma sistemi, ortam değişkenlerinden anahtarları okumak üzere ayarlanır.
