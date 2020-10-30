---
title: ASP.NET Core Facebook, Google ve dış sağlayıcı kimlik doğrulaması
author: rick-anderson
description: Bu öğreticide, dış kimlik doğrulama sağlayıcılarıyla OAuth 2,0 kullanarak bir ASP.NET Core uygulamasının nasıl oluşturulacağı gösterilmektedir.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
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
uid: security/authentication/social/index
ms.openlocfilehash: 1f7c8cd0716f1ada3517add0d37a09e419f38774
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053312"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a>ASP.NET Core Facebook, Google ve dış sağlayıcı kimlik doğrulaması

Tarafından [Valeriy Novyıtskyy](https://github.com/01binary) ve [Rick Anderson](https://twitter.com/RickAndMSFT)

Bu öğreticide, kullanıcıların dış kimlik doğrulama sağlayıcılarından kimlik bilgileriyle OAuth 2,0 kullanarak oturum açmasını sağlayan ASP.NET Core 3,0 uygulamasının nasıl oluşturulacağı gösterilmektedir.

[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins)ve [Microsoft](xref:security/authentication/microsoft-logins) sağlayıcıları aşağıdaki bölümlerde ele alınmıştır ve bu makalede oluşturulan Başlatıcı projesini kullanır. Diğer sağlayıcılar, [Aspnet. Security. OAuth. Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) ve [Aspnet. Security. OpenID. Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers)gibi üçüncü taraf paketlerinde kullanılabilir.

Kullanıcıların mevcut kimlik bilgileriyle oturum açmasını sağlama:

* Kullanıcılar için kullanışlıdır.
* Oturum açma işlemini üçüncü bir tarafa yönetmenin karmaşıklıklarını birçok kaydırır.

Sosyal oturumların trafik ve müşteri dönüştürmelerini nasıl ve ne şekilde, [Facebook](https://www.facebook.com/unsupportedbrowser) ve [Twitter](https://dev.twitter.com/resources/case-studies)tarafından örnek olay incelemeleri hakkında örnekler için bkz..

## <a name="create-a-new-aspnet-core-project"></a>Yeni bir ASP.NET Core projesi oluştur

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* Yeni bir proje oluşturma.
* **ASP.NET Core Web uygulaması** ' nı ve **İleri ' yi** seçin.
* Bir **Proje adı** girin ve **konumu** onaylayın veya değiştirin. **Oluştur** ’u seçin.
* Açılan kutuda ASP.NET Core en son sürümünü ( **ASP.NET Core {X. Y}** ) seçin ve ardından **Web uygulaması** ' nı seçin.
* **Kimlik doğrulaması** altında **Değiştir** ' i seçin ve kimlik doğrulamasını **bireysel kullanıcı hesapları** olarak ayarlayın. **Tamam** ’ı seçin.
* **Yeni ASP.NET Core Web uygulaması oluştur** penceresinde **Oluştur** ' u seçin.

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code/Mac için Visual Studio](#tab/visual-studio-code+visual-studio-mac)

* Terminali açın.  Visual Studio Code için [Tümleşik Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal)' i açabilirsiniz.

* Dizinleri ( `cd` ), projeyi içerecek bir klasöre değiştirin.

* Windows için şu komutu çalıştırın:

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  MacOS ve Linux için aşağıdaki komutu çalıştırın:

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * `dotnet new`Komut WebApp1 klasöründe yeni bir Razor Sayfalar projesi oluşturur. *WebApp1*
  * `-au Individual` Tek kimlik doğrulaması için kodu oluşturur.
  * `-uld` Windows için SQL Server Express basit bir sürümü olan LocalDB 'yi kullanır. `-uld`SQLite kullanmak için atlayın.
  * `code`Komutu, Visual Studio Code yeni bir örneğinde *WebApp1* klasörünü açar.

---

## <a name="apply-migrations"></a>Geçişleri Uygula

* Uygulamayı çalıştırın ve **Kaydet** bağlantısını seçin.
* Yeni hesap için e-posta ve parolayı girip **Kaydet** ' i seçin.
* Geçişleri uygulamak için yönergeleri izleyin.

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a>Oturum açma sağlayıcıları tarafından atanan belirteçleri depolamak için SecretManager kullanın

Sosyal oturum açma sağlayıcıları, kayıt işlemi sırasında **uygulama kimliği** ve **uygulama gizli** belirteçleri atar. Tam belirteç adları sağlayıcıya göre farklılık gösterir. Bu belirteçler, uygulamanızın API 'lerine erişmek için kullandığı kimlik bilgilerini temsil eder. Belirteçler, [gizli yöneticinin](xref:security/app-secrets#secret-manager)yardımıyla uygulama yapılandırmanızla bağlantılı olabilecek "gizli dizileri" oluşturur. Gizli dizi, belirteçleri bir yapılandırma dosyasında depolamanın daha güvenli bir alternatifidir *appsettings.json* .

> [!IMPORTANT]
> Gizli yönetici yalnızca geliştirme amaçlıdır. [Azure Key Vault yapılandırma sağlayıcısıyla](xref:security/key-vault-configuration)Azure test ve üretim gizli dizilerini saklayabilir ve koruyabilirsiniz.

Aşağıdaki her bir oturum açma sağlayıcısı tarafından atanan belirteçleri depolamak için [ASP.NET Core konusundaki geliştirme sırasında uygulama gizli dizileri Için güvenli depolama](xref:security/app-secrets) bölümündeki adımları izleyin.

## <a name="setup-login-providers-required-by-your-application"></a>Uygulamanız için gereken oturum açma sağlayıcılarını ayarlama

Uygulamanızı ilgili sağlayıcıları kullanacak şekilde yapılandırmak için aşağıdaki konuları kullanın:

* [Facebook](xref:security/authentication/facebook-logins) yönergeleri
* [Twitter](xref:security/authentication/twitter-logins) yönergeleri
* [Google](xref:security/authentication/google-logins) yönergeleri
* [Microsoft](xref:security/authentication/microsoft-logins) yönergeleri
* [Diğer sağlayıcı](xref:security/authentication/otherlogins) yönergeleri

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a>İsteğe bağlı olarak parola ayarla

Bir dış oturum açma sağlayıcısına kaydolduysanız, uygulamaya kayıtlı bir parolanız yok demektir. Bu, site için bir parola oluşturup hatırlamanızı konuma almayı azaltır, ancak aynı zamanda dış oturum açma sağlayıcısına bağımlı hale getirir. Dış oturum açma sağlayıcısı kullanılamıyorsa, Web sitesinde oturum açamazsınız.

Bir parola oluşturmak ve dış sağlayıcılar ile oturum açma işlemi sırasında ayarladığınız e-postanızı kullanarak oturum açmak için:

* **Yönet** görünümüne gitmek için sağ üst köşedeki **Merhaba &lt; e-posta diğer adı &gt;** bağlantısını seçin.

![Web uygulaması yönetme görünümü](index/_static/pass1a.png)

* **Oluştur** ’u seçin

![Parola sayfanızı ayarlama](index/_static/pass2a.png)

* Geçerli bir parola ayarlayın ve bunu kullanarak e-postalarınız ile oturum açabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* Oturum açma düğmelerinin nasıl özelleştirileceği hakkında bilgi edinmek için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/10563) bakın.
* Bu makalede dış kimlik doğrulaması tanıtılmıştır ve ASP.NET Core uygulamanıza dış oturum açma bilgileri eklemek için gereken önkoşullar açıklanacaktır.
* Uygulamanız için gereken sağlayıcıları için oturum açma işlemlerini yapılandırmak üzere sağlayıcıya özgü sayfalara başvurun.
* Kullanıcı ve bunların erişim ve yenileme belirteçleriyle ilgili ek verileri kalıcı hale getirmek isteyebilirsiniz. Daha fazla bilgi için bkz. <xref:security/authentication/social/additional-claims>.
