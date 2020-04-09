---
title: ASP.NET Core için Visual Studio'da geliştirme zamanı IIS desteği
author: rick-anderson
description: Windows Server'da IIS ile çalışırken ASP.NET Core uygulamaları nın hata ayıklama desteğini keşfedin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/iis/development-time-iis-support
ms.openlocfilehash: f87a1d8cf41248f14932908c0633f98a7198853f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78664047"
---
# <a name="development-time-iis-support-in-visual-studio-for-aspnet-core"></a>ASP.NET Core için Visual Studio'da geliştirme zamanı IIS desteği

Yazar: [Sourabh Shirhatti](https://twitter.com/sshirhatti)

::: moniker range=">= aspnetcore-3.0"

Bu makalede, Windows Server'da IIS ile çalışan ASP.NET Core uygulamaları hata ayıklama için [Visual Studio](https://visualstudio.microsoft.com) desteği açıklanmaktadır. Bu konu, bu senaryoyu etkinleştirme ve bir proje ayarlama yoluyla yürür.

## <a name="prerequisites"></a>Ön koşullar

* [Windows için Visual Studio](https://visualstudio.microsoft.com/downloads/)
* **ASP.NET ve web geliştirme** iş yükü
* **.NET Core çapraz platform geliştirme** iş yükü
* X.509 güvenlik sertifikası (HTTPS desteği için)

## <a name="enable-iis"></a>IIS'yi etkinleştir

1. Windows'da **Denetim Masası** > **Programları** > **ve Özellikleri** > **Windows özelliklerini açık veya kapalı** olarak açın (ekranın sol tarafında).
1. Internet **Bilgi Hizmetleri** onay kutusunu seçin. **Tamam'ı**seçin.

IIS yüklemesi bir sistemin yeniden başlatılmasını gerektirebilir.

## <a name="configure-iis"></a>IIS hizmetini yapılandırma

IIS'nin aşağıdakilerle yapılandırılmış bir web sitesi olmalıdır:

* **Ana bilgisayar adı** &ndash; Genellikle, **Varsayılan Web Sitesi** Ana Bilgisayar **adı** ile `localhost`kullanılır. Ancak, benzersiz bir ana bilgisayar adı ile herhangi bir geçerli IIS web sitesi çalışır.
* **Site Bağlama**
  * HTTPS gerektiren uygulamalar için, sertifikalı 443 bağlantı noktasına bağlama oluşturun. Genellikle, **IIS Express Geliştirme Sertifikası** kullanılır, ancak geçerli sertifika çalışır.
  * HTTP kullanan uygulamalar için, 80'i göndermek için bir bağlamanın varlığını onaylayın veya yeni bir site için bağlantı noktası 80'e bağlama oluşturun.
  * HTTP veya HTTPS için tek bir bağlama kullanın. **Aynı anda hem HTTP hem de HTTPS bağlantı noktalarına bağlama desteklenmez.**

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Visual Studio'da geliştirme zamanı IIS desteğini etkinleştirin

1. Visual Studio yükleyicisini başlatın.
1. IIS geliştirme zamanı desteği için kullanmayı planladığınız Visual Studio yüklemesi için **Değiştir'i** seçin.
1. ASP.NET **ve web geliştirme** iş yükü için **Geliştirme süresi IIS destek** bileşenini bulun ve yükleyin.

   Bileşen, yükleme **ayrıntıları** panelinde iş yüklerinin sağındaki **Geliştirme süresi IIS desteğiyle** **İsteğe Bağlı** bölümde listelenir. Bileşen, IIS ile Core uygulamalarını çalıştırmak için gerekli olan yerel bir IIS modülü olan [ASP.NET Çekirdek Modül'ASP.NET](xref:host-and-deploy/aspnet-core-module)yükler.

## <a name="configure-the-project"></a>Projeyi yapılandırma

### <a name="https-redirection"></a>HTTPS yeniden yönlendirme

HTTPS gerektiren yeni bir proje için, Yeni bir ASP.NET **Çekirdek Web Uygulaması Oluşturma** penceresinde HTTPS için **Yapılandırmak için** onay kutusunu seçin. Onay kutusunu seçmek, oluşturulduğunda uygulamaya [HTTPS Yönlendirme ve HSTS Middleware](xref:security/enforcing-ssl) ekler.

HTTPS gerektiren varolan bir proje için, HTTPS Yeniden Yönlendirme `Startup.Configure`ve HSTS Middleware'i .'da kullanın. Daha fazla bilgi için bkz. <xref:security/enforcing-ssl>.

HTTP kullanan bir proje için, [HTTPS Yeniden Yönlendirme ve HSTS Middleware](xref:security/enforcing-ssl) uygulamaya eklenmez. Uygulama yapılandırması gerekmez.

### <a name="iis-launch-profile"></a>IIS fırlatma profili

Geliştirme zamanı IIS desteği eklemek için yeni bir başlatma profili oluşturun:

1. **Çözüm Gezgini'ndeki**projeye sağ tıklayın. **Özellikleri**seçin. Hata **Ayıklama** sekmesini açın.
1. **Profil**için **Yeni** düğmesini seçin. Açılır penceredeki "IIS" profilini adlandırın. Profili oluşturmak için **Tamam'ı** seçin.
1. **Başlatma** ayarı için listeden **IIS'yi** seçin.
1. **Başlat tarayıcısı** için onay kutusunu seçin ve bitiş noktası URL'sini sağlayın.

   Uygulama HTTPS gerektiriyorsa, bir HTTPS`https://`bitiş noktası (). HTTP için bir HTTP`http://`( ) bitiş noktası kullanın.

   Önceki kullanımları belirtilen [IIS yapılandırması](#configure-iis)ile aynı ana `localhost`bilgisayar adı ve bağlantı noktasını sağlayın , genellikle .

   URL'nin sonunda uygulamanın adını sağlayın.

   Örneğin, `https://localhost/WebApplication1` (HTTPS) `http://localhost/WebApplication1` veya (HTTP) geçerli uç nokta URL'leridir.
1. Çevre **değişkenleri** bölümünde **Ekle** düğmesini seçin. **Bir Ad** `ASPNETCORE_ENVIRONMENT` ve **Değeri** olan bir `Development`ortam değişkeni sağlayın.
1. Web **Sunucusu Ayarları** alanında, **Uygulama URL'sini** **Başlat tarayıcıuç** noktası URL'si için kullanılan değere ayarlayın.
1. Visual Studio 2019 veya sonraki yerlerde **barındırma modeli** ayarı için, proje tarafından kullanılan barındırma modelini kullanmak için **Varsayılan'ı** seçin. Proje `<AspNetCoreHostingModel>` özelliği proje dosyasında ayarlarsa, özelliğin`InProcess` değeri `OutOfProcess`(veya ) kullanılır. Özellik yoksa, uygulamanın varsayılan barındırma modeli kullanılır ve bu da işlem aşamasındadır. Uygulama, uygulamanın normal barındırma modelinden farklı açık bir barındırma modeli ayarı `In Process` `Out Of Process` gerektiriyorsa, **Barındırma Modeli'ni** gerektiğinden biri olarak ayarlayın.
1. Profili kaydet.

Visual Studio'yu kullanmadığınızda, *Özellikler* klasöründeki [launchSettings.json](https://json.schemastore.org/launchsettings) dosyasına el ile bir başlatma profili ekleyin. Aşağıdaki örnek, profili HTTPS protokolünü kullanacak şekilde yapılandırır:

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

Uç `applicationUrl` noktaların `launchUrl` EŞLEŞTİrİlDIĞINI ve HTTP veya HTTPS gibi IIS bağlama yapılandırması ile aynı protokolü kullandığını doğrulayın.

## <a name="run-the-project"></a>Projeyi çalıştırma

Visual Studio'u yönetici olarak çalıştırın:

* Yapı yapılandırma açılır listesinin **Hata Ayıklama**olarak ayarlı olduğunu doğrulayın.
* Hata [Ayıklama Başlat düğmesini](/visualstudio/debugger/debugger-feature-tour) **IIS** profiline ayarlayın ve uygulamayı başlatmak için düğmeyi seçin.

Visual Studio yönetici olarak çalışmıyorsa yeniden başlatma isteminde bulunabilir. İstenirse Visual Studio'yı yeniden başlatın.

Güvenilmeyen bir geliştirme sertifikası kullanılırsa, tarayıcı güvenilmeyen sertifika için bir özel durum oluşturmanızı isteyebilir.

> [!NOTE]
> Bir Sürüm yapı yapılandırması [hata](/visualstudio/debugger/just-my-code) ayıklama Just My Code ve derleyici optimizasyonları ile bozulmuş bir deneyim sonuçlanır. Örneğin, kesme noktaları vurulmaz.

## <a name="additional-resources"></a>Ek kaynaklar

* [IIS'de IIS Yöneticisi ile Başlarken](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu makalede, Windows Server'da IIS ile çalışan ASP.NET Core uygulamaları hata ayıklama için [Visual Studio](https://visualstudio.microsoft.com) desteği açıklanmaktadır. Bu konu, bu senaryoyu etkinleştirme ve bir proje ayarlama yoluyla yürür.

## <a name="prerequisites"></a>Ön koşullar

* [Windows için Visual Studio](https://visualstudio.microsoft.com/downloads/)
* **ASP.NET ve web geliştirme** iş yükü
* **.NET Core çapraz platform geliştirme** iş yükü
* X.509 güvenlik sertifikası (HTTPS desteği için)

## <a name="enable-iis"></a>IIS'yi etkinleştir

1. Windows'da **Denetim Masası** > **Programları** > **ve Özellikleri** > **Windows özelliklerini açık veya kapalı** olarak açın (ekranın sol tarafında).
1. Internet **Bilgi Hizmetleri** onay kutusunu seçin. **Tamam'ı**seçin.

IIS yüklemesi bir sistemin yeniden başlatılmasını gerektirebilir.

## <a name="configure-iis"></a>IIS hizmetini yapılandırma

IIS'nin aşağıdakilerle yapılandırılmış bir web sitesi olmalıdır:

* **Ana bilgisayar adı** &ndash; Genellikle, **Varsayılan Web Sitesi** Ana Bilgisayar **adı** ile `localhost`kullanılır. Ancak, benzersiz bir ana bilgisayar adı ile herhangi bir geçerli IIS web sitesi çalışır.
* **Site Bağlama**
  * HTTPS gerektiren uygulamalar için, sertifikalı 443 bağlantı noktasına bağlama oluşturun. Genellikle, **IIS Express Geliştirme Sertifikası** kullanılır, ancak geçerli sertifika çalışır.
  * HTTP kullanan uygulamalar için, 80'i göndermek için bir bağlamanın varlığını onaylayın veya yeni bir site için bağlantı noktası 80'e bağlama oluşturun.
  * HTTP veya HTTPS için tek bir bağlama kullanın. **Aynı anda hem HTTP hem de HTTPS bağlantı noktalarına bağlama desteklenmez.**

## <a name="enable-development-time-iis-support-in-visual-studio"></a>Visual Studio'da geliştirme zamanı IIS desteğini etkinleştirin

1. Visual Studio yükleyicisini başlatın.
1. IIS geliştirme zamanı desteği için kullanmayı planladığınız Visual Studio yüklemesi için **Değiştir'i** seçin.
1. ASP.NET **ve web geliştirme** iş yükü için **Geliştirme süresi IIS destek** bileşenini bulun ve yükleyin.

   Bileşen, yükleme **ayrıntıları** panelinde iş yüklerinin sağındaki **Geliştirme süresi IIS desteğiyle** **İsteğe Bağlı** bölümde listelenir. Bileşen, IIS ile Core uygulamalarını çalıştırmak için gerekli olan yerel bir IIS modülü olan [ASP.NET Çekirdek Modül'ASP.NET](xref:host-and-deploy/aspnet-core-module)yükler.

## <a name="configure-the-project"></a>Projeyi yapılandırma

### <a name="https-redirection"></a>HTTPS yeniden yönlendirme

HTTPS gerektiren yeni bir proje için, Yeni bir ASP.NET **Çekirdek Web Uygulaması Oluşturma** penceresinde HTTPS için **Yapılandırmak için** onay kutusunu seçin. Onay kutusunu seçmek, oluşturulduğunda uygulamaya [HTTPS Yönlendirme ve HSTS Middleware](xref:security/enforcing-ssl) ekler.

HTTPS gerektiren varolan bir proje için, HTTPS Yeniden Yönlendirme `Startup.Configure`ve HSTS Middleware'i .'da kullanın. Daha fazla bilgi için bkz. <xref:security/enforcing-ssl>.

HTTP kullanan bir proje için, [HTTPS Yeniden Yönlendirme ve HSTS Middleware](xref:security/enforcing-ssl) uygulamaya eklenmez. Uygulama yapılandırması gerekmez.

### <a name="iis-launch-profile"></a>IIS fırlatma profili

Geliştirme zamanı IIS desteği eklemek için yeni bir başlatma profili oluşturun:

1. **Çözüm Gezgini'ndeki**projeye sağ tıklayın. **Özellikleri**seçin. Hata **Ayıklama** sekmesini açın.
1. **Profil**için **Yeni** düğmesini seçin. Açılır penceredeki "IIS" profilini adlandırın. Profili oluşturmak için **Tamam'ı** seçin.
1. **Başlatma** ayarı için listeden **IIS'yi** seçin.
1. **Başlat tarayıcısı** için onay kutusunu seçin ve bitiş noktası URL'sini sağlayın.

   Uygulama HTTPS gerektiriyorsa, bir HTTPS`https://`bitiş noktası (). HTTP için bir HTTP`http://`( ) bitiş noktası kullanın.

   Önceki kullanımları belirtilen [IIS yapılandırması](#configure-iis)ile aynı ana `localhost`bilgisayar adı ve bağlantı noktasını sağlayın , genellikle .

   URL'nin sonunda uygulamanın adını sağlayın.

   Örneğin, `https://localhost/WebApplication1` (HTTPS) `http://localhost/WebApplication1` veya (HTTP) geçerli uç nokta URL'leridir.
1. Çevre **değişkenleri** bölümünde **Ekle** düğmesini seçin. **Bir Ad** `ASPNETCORE_ENVIRONMENT` ve **Değeri** olan bir `Development`ortam değişkeni sağlayın.
1. Web **Sunucusu Ayarları** alanında, **Uygulama URL'sini** **Başlat tarayıcıuç** noktası URL'si için kullanılan değere ayarlayın.
1. Visual Studio 2019 veya sonraki yerlerde **barındırma modeli** ayarı için, proje tarafından kullanılan barındırma modelini kullanmak için **Varsayılan'ı** seçin. Proje `<AspNetCoreHostingModel>` özelliği proje dosyasında ayarlarsa, özelliğin`InProcess` değeri `OutOfProcess`(veya ) kullanılır. Özellik yoksa, uygulamanın varsayılan barındırma modeli kullanılır ve bu da işlem dışıdır. Uygulama, uygulamanın normal barındırma modelinden farklı açık bir barındırma modeli ayarı `In Process` `Out Of Process` gerektiriyorsa, **Barındırma Modeli'ni** gerektiğinden biri olarak ayarlayın.
1. Profili kaydet.

Visual Studio'yu kullanmadığınızda, *Özellikler* klasöründeki [launchSettings.json](https://json.schemastore.org/launchsettings) dosyasına el ile bir başlatma profili ekleyin. Aşağıdaki örnek, profili HTTPS protokolünü kullanacak şekilde yapılandırır:

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

Uç `applicationUrl` noktaların `launchUrl` EŞLEŞTİrİlDIĞINI ve HTTP veya HTTPS gibi IIS bağlama yapılandırması ile aynı protokolü kullandığını doğrulayın.

## <a name="run-the-project"></a>Projeyi çalıştırma

Visual Studio'u yönetici olarak çalıştırın:

* Yapı yapılandırma açılır listesinin **Hata Ayıklama**olarak ayarlı olduğunu doğrulayın.
* Hata [Ayıklama Başlat düğmesini](/visualstudio/debugger/debugger-feature-tour) **IIS** profiline ayarlayın ve uygulamayı başlatmak için düğmeyi seçin.

Visual Studio yönetici olarak çalışmıyorsa yeniden başlatma isteminde bulunabilir. İstenirse Visual Studio'yı yeniden başlatın.

Güvenilmeyen bir geliştirme sertifikası kullanılırsa, tarayıcı güvenilmeyen sertifika için bir özel durum oluşturmanızı isteyebilir.

> [!NOTE]
> Bir Sürüm yapı yapılandırması [hata](/visualstudio/debugger/just-my-code) ayıklama Just My Code ve derleyici optimizasyonları ile bozulmuş bir deneyim sonuçlanır. Örneğin, kesme noktaları vurulmaz.

## <a name="additional-resources"></a>Ek kaynaklar

* [IIS'de IIS Yöneticisi ile Başlarken](/iis/get-started/getting-started-with-iis/getting-started-with-the-iis-manager-in-iis-7-and-iis-8)
* <xref:security/enforcing-ssl>

::: moniker-end
