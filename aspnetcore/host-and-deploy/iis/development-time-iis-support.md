---
title: ASP.NET Core için Visual Studio'da geliştirme zamanı IIS desteği
author: rick-anderson
description: Windows Server 'da IIS ile çalışırken ASP.NET Core uygulamalarda hata ayıklama desteğini bulur.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: a6719b4f84b1bc60c7c2aea2aa3a97ef79f43e2e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777026"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a>ASP.NET Core için Visual Studio'da geliştirme zamanı IIS desteği

, [Sourabh Shirhatti](https://twitter.com/sshirhatti)

::: moniker range=">= aspnetcore-3.0"

Bu makalede, Windows Server 'da IIS ile çalışan ASP.NET Core hata ayıklama için [Visual Studio](https://visualstudio.microsoft.com) desteği açıklanmaktadır. Bu konu başlığı altında, bu senaryonun etkinleştirilmesi ve bir projenin kurulması anlatılmaktadır.

## <a name="prerequisites"></a>Önkoşullar

* [Windows için Visual Studio](https://visualstudio.microsoft.com/downloads/)
* **ASP.net ve Web geliştirme** iş yükü
* **.NET Core platformlar arası geliştirme** iş yükü
* X. 509.440 güvenlik sertifikası (HTTPS desteği için)

## <a name="enable-iis"></a>IIS 'yi etkinleştirme

1. Windows 'ta, **Denetim Masası** > **programları** > **Programlar ve Özellikler** > **Windows özelliklerini aç veya kapat** (ekranın sol tarafı) bölümüne gidin.
1. **Internet Information Services** onay kutusunu seçin. **Tamam**’ı seçin.

IIS yüklemesi için sistemin yeniden başlatılması gerekebilir.

## <a name="configure-iis"></a>IIS hizmetini yapılandırma

IIS 'nin aşağıdaki ile yapılandırılmış bir Web sitesine sahip olması gerekir:

* **Ana bilgisayar adı** &ndash; genellikle, **varsayılan Web sitesi** bir **ana bilgisayar adıyla** kullanılır `localhost`. Ancak, benzersiz bir ana bilgisayar adına sahip geçerli bir IIS Web sitesi çalışmaktadır.
* **Site bağlama**
  * HTTPS gerektiren uygulamalar için, sertifika ile 443 numaralı bağlantı noktasına bir bağlama oluşturun. Genellikle **IIS Express geliştirme sertifikası** kullanılır, ancak geçerli bir sertifika çalışıyor olur.
  * HTTP kullanan uygulamalar için, 80 veya yeni bir site için bağlantı noktası 80 ' e bir bağlama oluşturmak için bir bağlamanın mevcut olduğunu onaylayın.
  * HTTP veya HTTPS için tek bir bağlama kullanın. **Aynı anda hem HTTP hem de HTTPS bağlantı noktalarına bağlama desteklenmez.**

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Visual Studio 'da geliştirme zamanı IIS desteğini etkinleştirme

1. Visual Studio yükleyicisi 'ni başlatın.
1. IIS geliştirme zamanı desteği için kullanmayı planladığınız Visual Studio yüklemesi için **Değiştir** ' i seçin.
1. **ASP.net ve Web geliştirme** iş yükü için **geliştirme zamanı IIS destek** bileşeni ' ni bulun ve yükler.

   Bileşen, iş yüklerinin sağındaki **Yükleme ayrıntıları** panelinde, **geliştirme zamanı IIS desteği** altındaki **isteğe bağlı** bölümde listelenmiştir. Bileşen, IIS ile ASP.NET Core uygulamaları çalıştırmak için gerekli yerel bir IIS modülü olan [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module)yüklüyor.

## <a name="configure-the-project"></a>Projeyi yapılandırma

### <a name="https-redirection"></a>HTTPS yönlendirmesi

HTTPS gerektiren yeni bir proje için **yeni ASP.NET Core Web uygulaması oluşturma** penceresinde **https için yapılandırmak** üzere onay kutusunu seçin. Onay kutusunun belirlenmesi, uygulama oluşturulduğunda [https yeniden yönlendirme ve HSTS ara yazılımı](xref:security/enforcing-ssl) ekler.

HTTPS gerektiren mevcut bir proje için ' de `Startup.Configure`https yeniden yönlendirme ve HSTS ara yazılımı kullanın. Daha fazla bilgi için bkz. <xref:security/enforcing-ssl>.

HTTP kullanan bir proje için, [https yeniden yönlendirme ve HSTS ara yazılımı](xref:security/enforcing-ssl) uygulamaya eklenmez. Uygulama yapılandırması gerekli değildir.

### <a name="iis-launch-profile"></a>IIS başlatma profili

Geliştirme zamanı IIS desteği eklemek için yeni bir başlatma profili oluşturun:

1. **Çözüm Gezgini**projeye sağ tıklayın. **Özellikler**’i seçin. **Hata Ayıkla** sekmesini açın.
1. **Profil**için **Yeni** düğmesini seçin. Profili, açılan pencerede "IIS" olarak adlandırın. Profili oluşturmak için **Tamam ' ı** seçin.
1. **Başlatma** ayarı Için listeden **IIS** ' yi seçin.
1. **Başlat tarayıcısı** onay kutusunu seçin ve uç nokta URL 'sini sağlayın.

   Uygulama HTTPS gerektirdiğinde, bir HTTPS uç noktası (`https://`) kullanın. HTTP için bir HTTP (`http://`) uç noktası kullanın.

   [Daha önce belirtilen IIS yapılandırmasıyla](#configure-iis)aynı ana bilgisayar adını ve bağlantı noktasını (genellikle `localhost`) sağlayın.

   URL 'nin sonundaki uygulamanın adını belirtin.

   Örneğin, `https://localhost/WebApplication1` (https) veya `http://localhost/WebApplication1` (http) geçerli uç nokta URL 'lardır.
1. **Ortam değişkenleri** bölümünde **Ekle** düğmesini seçin. **Adı** `ASPNETCORE_ENVIRONMENT` ve **değeri** olan bir ortam değişkeni sağlayın `Development`.
1. **Web sunucusu ayarları** alanında, **Uygulama URL** 'sini **başlatma tarayıcısı** uç noktası URL 'si için kullanılan aynı değere ayarlayın.
1. Visual Studio 2019 veya sonraki sürümlerde **barındırma modeli** ayarı için, proje tarafından kullanılan barındırma modelini kullanmak üzere **varsayılan** ' ı seçin. Proje, proje dosyasında `<AspNetCoreHostingModel>` özelliği ayarlarsa, özelliğin değeri (`InProcess` veya `OutOfProcess`) kullanılır. Özellik mevcut değilse, uygulamanın varsayılan barındırma modeli, işlem içi kullanılır. Uygulama, uygulamanın normal barındırma modelinden farklı bir açık barındırma modeli ayarı gerektiriyorsa, **barındırma modelini** gereken ya da `In Process` `Out Of Process` gerektiği şekilde ayarlayın.
1. Profili kaydedin.

Visual Studio kullanmadığınız durumlarda, *Özellikler* klasöründeki [launchsettings. JSON](https://json.schemastore.org/launchsettings) dosyasına el ile bir başlatma profili ekleyin. Aşağıdaki örnek, HTTPS protokolünü kullanmak için profili yapılandırır:

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

`applicationUrl` Ve `launchUrl` BITIŞ noktalarının eşleştiğini ve IIS bağlama YAPıLANDıRMASıYLA aynı Protokolü (http veya https) kullandığını doğrulayın.

## <a name="run-the-project"></a>Projeyi çalıştırma

Visual Studio 'Yu yönetici olarak çalıştırın:

* Derleme yapılandırması açılır listesinin **hata ayıklama**olarak ayarlandığını onaylayın.
* [Hata ayıklamayı Başlat düğmesini](/visualstudio/debugger/debugger-feature-tour) **IIS** profiline ayarlayın ve uygulamayı başlatmak için düğmeyi seçin.

Visual Studio, yönetici olarak çalışmıyorsa bir yeniden başlatma isteyebilir. İstenirse, Visual Studio 'Yu yeniden başlatın.

Güvenilmeyen bir geliştirme sertifikası kullanılırsa, tarayıcı güvenilmeyen sertifika için bir özel durum oluşturmanız gerekebilir.

> [!NOTE]
> [Yalnızca kendi kodum](/visualstudio/debugger/just-my-code) ve derleyici Iyileştirmeleriyle yayın derleme yapılandırması hata ayıklaması, düzeyi düşürülmüş bir deneyimle sonuçlanır. Örneğin, kesme noktaları isabet edilmez.

## <a name="additional-resources"></a>Ek kaynaklar

* [IIS 'de IIS Yöneticisi 'Ni kullanmaya başlama](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu makalede, Windows Server 'da IIS ile çalışan ASP.NET Core hata ayıklama için [Visual Studio](https://visualstudio.microsoft.com) desteği açıklanmaktadır. Bu konu başlığı altında, bu senaryonun etkinleştirilmesi ve bir projenin kurulması anlatılmaktadır.

## <a name="prerequisites"></a>Önkoşullar

* [Windows için Visual Studio](https://visualstudio.microsoft.com/downloads/)
* **ASP.net ve Web geliştirme** iş yükü
* **.NET Core platformlar arası geliştirme** iş yükü
* X. 509.440 güvenlik sertifikası (HTTPS desteği için)

## <a name="enable-iis"></a>IIS 'yi etkinleştirme

1. Windows 'ta, **Denetim Masası** > **programları** > **Programlar ve Özellikler** > **Windows özelliklerini aç veya kapat** (ekranın sol tarafı) bölümüne gidin.
1. **Internet Information Services** onay kutusunu seçin. **Tamam**’ı seçin.

IIS yüklemesi için sistemin yeniden başlatılması gerekebilir.

## <a name="configure-iis"></a>IIS hizmetini yapılandırma

IIS 'nin aşağıdaki ile yapılandırılmış bir Web sitesine sahip olması gerekir:

* **Ana bilgisayar adı** &ndash; genellikle, **varsayılan Web sitesi** bir **ana bilgisayar adıyla** kullanılır `localhost`. Ancak, benzersiz bir ana bilgisayar adına sahip geçerli bir IIS Web sitesi çalışmaktadır.
* **Site bağlama**
  * HTTPS gerektiren uygulamalar için, sertifika ile 443 numaralı bağlantı noktasına bir bağlama oluşturun. Genellikle **IIS Express geliştirme sertifikası** kullanılır, ancak geçerli bir sertifika çalışıyor olur.
  * HTTP kullanan uygulamalar için, 80 veya yeni bir site için bağlantı noktası 80 ' e bir bağlama oluşturmak için bir bağlamanın mevcut olduğunu onaylayın.
  * HTTP veya HTTPS için tek bir bağlama kullanın. **Aynı anda hem HTTP hem de HTTPS bağlantı noktalarına bağlama desteklenmez.**

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Visual Studio 'da geliştirme zamanı IIS desteğini etkinleştirme

1. Visual Studio yükleyicisi 'ni başlatın.
1. IIS geliştirme zamanı desteği için kullanmayı planladığınız Visual Studio yüklemesi için **Değiştir** ' i seçin.
1. **ASP.net ve Web geliştirme** iş yükü için **geliştirme zamanı IIS destek** bileşeni ' ni bulun ve yükler.

   Bileşen, iş yüklerinin sağındaki **Yükleme ayrıntıları** panelinde, **geliştirme zamanı IIS desteği** altındaki **isteğe bağlı** bölümde listelenmiştir. Bileşen, IIS ile ASP.NET Core uygulamaları çalıştırmak için gerekli yerel bir IIS modülü olan [ASP.NET Core modülünü](xref:host-and-deploy/aspnet-core-module)yüklüyor.

## <a name="configure-the-project"></a>Projeyi yapılandırma

### <a name="https-redirection"></a>HTTPS yönlendirmesi

HTTPS gerektiren yeni bir proje için **yeni ASP.NET Core Web uygulaması oluşturma** penceresinde **https için yapılandırmak** üzere onay kutusunu seçin. Onay kutusunun belirlenmesi, uygulama oluşturulduğunda [https yeniden yönlendirme ve HSTS ara yazılımı](xref:security/enforcing-ssl) ekler.

HTTPS gerektiren mevcut bir proje için ' de `Startup.Configure`https yeniden yönlendirme ve HSTS ara yazılımı kullanın. Daha fazla bilgi için bkz. <xref:security/enforcing-ssl>.

HTTP kullanan bir proje için, [https yeniden yönlendirme ve HSTS ara yazılımı](xref:security/enforcing-ssl) uygulamaya eklenmez. Uygulama yapılandırması gerekli değildir.

### <a name="iis-launch-profile"></a>IIS başlatma profili

Geliştirme zamanı IIS desteği eklemek için yeni bir başlatma profili oluşturun:

1. **Çözüm Gezgini**projeye sağ tıklayın. **Özellikler**’i seçin. **Hata Ayıkla** sekmesini açın.
1. **Profil**için **Yeni** düğmesini seçin. Profili, açılan pencerede "IIS" olarak adlandırın. Profili oluşturmak için **Tamam ' ı** seçin.
1. **Başlatma** ayarı Için listeden **IIS** ' yi seçin.
1. **Başlat tarayıcısı** onay kutusunu seçin ve uç nokta URL 'sini sağlayın.

   Uygulama HTTPS gerektirdiğinde, bir HTTPS uç noktası (`https://`) kullanın. HTTP için bir HTTP (`http://`) uç noktası kullanın.

   [Daha önce belirtilen IIS yapılandırmasıyla](#configure-iis)aynı ana bilgisayar adını ve bağlantı noktasını (genellikle `localhost`) sağlayın.

   URL 'nin sonundaki uygulamanın adını belirtin.

   Örneğin, `https://localhost/WebApplication1` (https) veya `http://localhost/WebApplication1` (http) geçerli uç nokta URL 'lardır.
1. **Ortam değişkenleri** bölümünde **Ekle** düğmesini seçin. **Adı** `ASPNETCORE_ENVIRONMENT` ve **değeri** olan bir ortam değişkeni sağlayın `Development`.
1. **Web sunucusu ayarları** alanında, **Uygulama URL** 'sini **başlatma tarayıcısı** uç noktası URL 'si için kullanılan aynı değere ayarlayın.
1. Visual Studio 2019 veya sonraki sürümlerde **barındırma modeli** ayarı için, proje tarafından kullanılan barındırma modelini kullanmak üzere **varsayılan** ' ı seçin. Proje, proje dosyasında `<AspNetCoreHostingModel>` özelliği ayarlarsa, özelliğin değeri (`InProcess` veya `OutOfProcess`) kullanılır. Özellik mevcut değilse, uygulamanın varsayılan barındırma modeli kullanılır ve bu işlem, işlem dışı olur. Uygulama, uygulamanın normal barındırma modelinden farklı bir açık barındırma modeli ayarı gerektiriyorsa, **barındırma modelini** gereken ya da `In Process` `Out Of Process` gerektiği şekilde ayarlayın.
1. Profili kaydedin.

Visual Studio kullanmadığınız durumlarda, *Özellikler* klasöründeki [launchsettings. JSON](https://json.schemastore.org/launchsettings) dosyasına el ile bir başlatma profili ekleyin. Aşağıdaki örnek, HTTPS protokolünü kullanmak için profili yapılandırır:

```json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iis": {
      "applicationUrl": "https://localhost/WebApplication1",
      "sslPort": 0
    }
  },
  "profiles": {
    "IIS": {
      "commandName": "IIS",
      "launchBrowser": true,
      "launchUrl": "https://localhost/WebApplication1",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

`applicationUrl` Ve `launchUrl` BITIŞ noktalarının eşleştiğini ve IIS bağlama YAPıLANDıRMASıYLA aynı Protokolü (http veya https) kullandığını doğrulayın.

## <a name="run-the-project"></a>Projeyi çalıştırma

Visual Studio 'Yu yönetici olarak çalıştırın:

* Derleme yapılandırması açılır listesinin **hata ayıklama**olarak ayarlandığını onaylayın.
* [Hata ayıklamayı Başlat düğmesini](/visualstudio/debugger/debugger-feature-tour) **IIS** profiline ayarlayın ve uygulamayı başlatmak için düğmeyi seçin.

Visual Studio, yönetici olarak çalışmıyorsa bir yeniden başlatma isteyebilir. İstenirse, Visual Studio 'Yu yeniden başlatın.

Güvenilmeyen bir geliştirme sertifikası kullanılırsa, tarayıcı güvenilmeyen sertifika için bir özel durum oluşturmanız gerekebilir.

> [!NOTE]
> [Yalnızca kendi kodum](/visualstudio/debugger/just-my-code) ve derleyici Iyileştirmeleriyle yayın derleme yapılandırması hata ayıklaması, düzeyi düşürülmüş bir deneyimle sonuçlanır. Örneğin, kesme noktaları isabet edilmez.

## <a name="additional-resources"></a>Ek kaynaklar

* [IIS 'de IIS Yöneticisi 'Ni kullanmaya başlama](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
