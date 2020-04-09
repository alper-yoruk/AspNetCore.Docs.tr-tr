---
title: ASP.NET Core'da Facebook, Google ve harici sağlayıcı kimlik doğrulaması
author: rick-anderson
description: Bu öğretici, harici kimlik doğrulama sağlayıcıları yla OAuth 2.0 kullanarak bir ASP.NET Core uygulamasının nasıl oluşturulabildiğini göstermektedir.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/authentication/social/index
ms.openlocfilehash: c698edbd85d665509366287b1dcad08e276e71cc
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78668044"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a>ASP.NET Core'da Facebook, Google ve harici sağlayıcı kimlik doğrulaması

Yazar: [Valeriy Novytskyy](https://github.com/01binary) ve [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu öğretici, kullanıcıların OAuth 2.0 kullanarak harici kimlik doğrulama sağlayıcılarının kimlik bilgileriyle oturum açmalarını sağlayan bir ASP.NET Core 3.0 uygulamasının nasıl oluşturulabildiğini göstermektedir.

[Facebook,](xref:security/authentication/facebook-logins) [Twitter,](xref:security/authentication/twitter-logins) [Google](xref:security/authentication/google-logins)ve [Microsoft](xref:security/authentication/microsoft-logins) sağlayıcıları aşağıdaki bölümlerde ele alınır ve bu makalede oluşturulan başlangıç projesini kullanır. Diğer sağlayıcılar [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) ve [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers)gibi üçüncü taraf paketlerde mevcuttur.

Kullanıcıların varolan kimlik bilgileriyle oturum açmalarını etkinleştirme:

* Kullanıcılar için uygundur.
* Oturum açma işlemini yönetme karmaşıklıklarının çoğunu üçüncü bir tarafa kaydırıyor.

Sosyal girişlerin trafiği ve müşteri dönüşümlerini nasıl yönlendirebileceğine örnekler için [Facebook](https://www.facebook.com/unsupportedbrowser) ve [Twitter'ın](https://dev.twitter.com/resources/case-studies)örnek araştırmalarına bakın.

## <a name="create-a-new-aspnet-core-project"></a>Yeni bir ASP.NET Çekirdek Projesi Oluşturun

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Yeni bir proje oluşturma.
* Core Web Application ve **NextASP.NETi** seçin. **Next**
* Proje **adı** sağlayın ve **Konumu**onaylayın veya değiştirin. **Oluştur'u**seçin.
* açılır **(ASP.NET Core {X.Y}**(ASP.NET Core'da ASP.NET Core'un en son sürümünü seçin ve ardından **Web Uygulamasını**seçin.
* **Kimlik Doğrulama**altında **Değiştir'i** seçin ve kimlik doğrulamasını Bireysel **Kullanıcı Hesapları**olarak ayarlayın. **Tamam'ı**seçin.
* Yeni **bir ASP.NET Çekirdek Web Uygulaması Oluştur** penceresinde **Oluştur'u**seçin.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Mac için Visual Studio Code / Visual Studio](#tab/visual-studio-code+visual-studio-mac)

* Terminali aç.  Visual Studio Code için [entegre terminali](https://code.visualstudio.com/docs/editor/integrated-terminal)açabilirsiniz.

* Dizinleri`cd`( ) projeyi içeren bir klasörle değiştirin.

* Windows için şu komutu çalıştırın:

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  macOS ve Linux için aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * Komut, `dotnet new` *WebApp1* klasöründe yeni bir Razor Pages projesi oluşturur.
  * `-au Individual`Bireysel kimlik doğrulaması için kod oluşturur.
  * `-uld`Windows için SQL Server Express'in hafif bir sürümü olan LocalDB'yi kullanır. SQLite `-uld` kullanmak için atla.
  * Komut, `code` Visual Studio Code'un yeni bir örneğinde *WebApp1* klasörünü açar.

---

## <a name="apply-migrations"></a>Geçişleri uygulama

* Uygulamayı çalıştırın ve **Kayıt bağlantısını** seçin.
* Yeni hesabın e-postasını ve parolasını girin ve ardından **Kaydol'u**seçin.
* Geçişleri uygulamak için yönergeleri izleyin.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a>Giriş sağlayıcıları tarafından atanan belirteçleri depolamak için SecretManager'ı kullanma

Sosyal giriş sağlayıcıları kayıt işlemi sırasında **Uygulama Kimliği** ve **Uygulama Gizli** belirteçleri atar. Tam belirteç adları sağlayıcıya göre değişir. Bu belirteçler, uygulamanızın API'lerine erişmek için kullandığı kimlik bilgilerini temsil eder. Belirteçler, [Secret Manager](xref:security/app-secrets#secret-manager)yardımıyla uygulama yapılandırmanıza bağlanabilecek "sırları" oluşturur. Gizli Yönetici, belirteçleri *appsettings.json*gibi bir yapılandırma dosyasında depolamak için daha güvenli bir alternatiftir.

> [!IMPORTANT]
> Gizli Müdür sadece geliştirme amaçlıdır. Azure Test ve üretim sırlarını Azure [Anahtar Kasası yapılandırma sağlayıcısıyla](xref:security/key-vault-configuration)saklayabilir ve koruyabilirsiniz.

Aşağıdaki her giriş sağlayıcısı tarafından atanan belirteçleri depolamak için ASP.NET Temel konu [geliştirme uygulama sırlarını güvenli depolama](xref:security/app-secrets) adımları izleyin.

## <a name="setup-login-providers-required-by-your-application"></a>Uygulamanızın gerektirdiği kurulum giriş sağlayıcıları

Uygulamanızı ilgili sağlayıcıları kullanacak şekilde yapılandırmak için aşağıdaki konuları kullanın:

* [Facebook](xref:security/authentication/facebook-logins) talimatları
* [Twitter](xref:security/authentication/twitter-logins) talimatları
* [Google](xref:security/authentication/google-logins) yönergeleri
* [Microsoft](xref:security/authentication/microsoft-logins) yönergeleri
* [Diğer sağlayıcı](xref:security/authentication/otherlogins) talimatları

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a>İsteğe bağlı olarak parolayı ayarlayın

Harici bir giriş sağlayıcısına kaydolduğunuzda, uygulamaya kayıtlı bir parolanız yoktur. Bu, site için bir parola oluşturmanızı ve hatırlamanızı kolaylaştırır, ancak aynı zamanda sizi harici giriş sağlayıcısına bağımlı hale getirir. Harici oturum açma sağlayıcısı kullanılamıyorsa, web sitesinde oturum açamazsınız.

Oturum açma işlemi sırasında harici sağlayıcılarla belirlediğiniz e-postanızı kullanarak parola oluşturmak ve oturum unuzu imzalamak için:

* **Yönet** görünümüne gitmek için sağ üst köşedeki **Merhaba &lt;e-posta takma adını&gt; ** seçin.

![Web uygulaması Görünümü yönet](index/_static/pass1a.png)

* **Oluştur**’u seçin

![Parola sayfanızı ayarlama](index/_static/pass2a.png)

* Geçerli bir parola ayarlayın ve bunu e-postanızla oturum kurmak için kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* Giriş düğmelerini özelleştirme hakkında bilgi almak için [bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/10563) bakın.
* Bu makalede, harici kimlik doğrulaması tanıtıldı ve ASP.NET Core uygulamanıza harici oturum açmak için gereken ön koşullar açıklanmıştır.
* Uygulamanızın gerektirdiği sağlayıcılar için oturum açma işlemlerini yapılandırmak için sağlayıcıya özel sayfalara başvurun.
* Kullanıcı ve bunların erişim ve yenileme belirteçleri hakkında ek veri kalıcı isteyebilirsiniz. Daha fazla bilgi için bkz. <xref:security/authentication/social/additional-claims>.
