---
title: Azure Uygulama Hizmeti ve IIS'de ASP.NET Core sorun giderme
author: rick-anderson
description: Azure Uygulama Hizmeti ve Internet Bilgi Hizmetleri (IIS) dağıtımlarıyla ilgili sorunları ASP.NET Temel uygulamalarla ilgili sorunları nasıl tanılayabileceğinizi öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: test/troubleshoot-azure-iis
ms.openlocfilehash: 671f68da2ea261cb8ae32a9d5ef875217859054d
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78655332"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service-and-iis"></a>Azure Uygulama Hizmeti ve IIS'de ASP.NET Core sorun giderme

Yazar: [Justin Kotalik](https://github.com/jkotalik)

::: moniker range=">= aspnetcore-3.0"

Bu makalede, bir uygulama Azure Uygulama Hizmeti veya IIS'ye dağıtıldığında hataların nasıl tanılanacağına ilişkin yaygın uygulama başlangıç hataları ve yönergeler hakkında bilgiler verilmektedir:

[Uygulama başlatma hataları](#app-startup-errors)  
Ortak başlangıç HTTP durum kodu senaryolarını açıklar.

[Azure Uygulama Hizmetinde Sorun Giderme](#troubleshoot-on-azure-app-service)  
Azure Uygulama Hizmeti'ne dağıtılan uygulamalar için sorun giderme önerileri sağlar.

[IIS üzerinde sorun giderme](#troubleshoot-on-iis)  
IIS'ye dağıtılan veya IIS Express'te yerel olarak çalışan uygulamalar için sorun giderme önerileri sağlar. Kılavuz, hem Windows Server hem de Windows masaüstü dağıtımları için geçerlidir.

[Paket önbelleklerini temizle](#clear-package-caches)  
Büyük yükseltmeler yaparken veya paket sürümlerini değiştirirken tutarsız paketler bir uygulamayı kırdığında ne yapması gerektiğini açıklar.

[Ek kaynaklar](#additional-resources)  
Ek sorun giderme konularını listeler.

## <a name="app-startup-errors"></a>Uygulama başlatma hataları

Visual Studio'da, ASP.NET Core projesi hata ayıklama sırasında [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) barındırma için varsayılandır. A *502.5 - İşlem Hatası* veya *500.30 -* Yerel hata ayıklama zaman ortaya çıkan Başlat Hatası bu konuda ki tavsiye kullanılarak teşhis edilebilir.

### <a name="40314-forbidden"></a>403.14 Yasak

Uygulama başlatılmaz. Aşağıdaki hata günlüğe kaydedilir:

```
The Web server is configured to not list the contents of this directory.
```

Hata genellikle aşağıdaki senaryolardan herhangi birini içeren barındırma sisteminde ki bozuk dağıtımdan kaynaklanır:

* Uygulama barındırma sisteminde yanlış klasöre dağıtılır.
* Dağıtım işlemi, uygulamanın tüm dosya ve klasörlerini barındırma sistemindeki dağıtım klasörüne taşıyamadı.
* *web.config* dosyası dağıtımda eksik veya *web.config* dosyası içeriği yanlış biçimlendirilmiştir.

Aşağıdaki adımları uygulayın:

1. Barındırma sistemindeki dağıtım klasöründeki tüm dosya ve klasörleri silin.
1. Visual Studio, PowerShell veya manuel dağıtım gibi normal dağıtım yönteminizi kullanarak uygulamanın *yayımlama* klasörünün içeriğini barındırma sistemine yeniden dağıtın:
   * *web.config* dosyasının dağıtımda mevcut olduğunu ve içeriğinin doğru olduğunu doğrulayın.
   * Azure Uygulama Hizmeti'nde barındırma yaparken, uygulamanın `D:\home\site\wwwroot` klasöre dağıtıldığından onaylayın.
   * Uygulama IIS tarafından barındırıldığında, uygulamanın **IIS Yöneticisi'nin**Temel **Ayarları'nda**gösterilen IIS **Fiziksel yoluna** dağıtıldığından onaylayın.
1. Barındırma sistemindeki dağıtımı projenin *yayımlama* klasörünün içeriğiyle karşılaştırarak uygulamanın tüm dosya ve klasörlerinin dağıtılmış olduğunu doğrulayın.

Yayınlanan bir ASP.NET Core uygulamasının düzeni hakkında <xref:host-and-deploy/directory-structure>daha fazla bilgi için bkz. *web.config* dosyasında daha fazla <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>bilgi için bkz.

### <a name="500-internal-server-error"></a>500 İç Sunucu Hatası

Uygulama başlar, ancak bir hata sunucunun isteği yerine getirmesini engeller.

Bu hata, başlangıç sırasında veya yanıt oluştururken uygulamanın kodu içinde oluşur. Yanıt hiçbir içerik içermeyebilir veya yanıt tarayıcıda *500 Dahili Sunucu Hatası* olarak görünebilir. Uygulama Olay Günlüğü genellikle uygulamanın normal olarak başladığını belirtir. Sunucunun bakış açısından, bu doğru. Uygulama başladı, ancak geçerli bir yanıt oluşturamıyor. Uygulamayı sunucudaki bir komut isteminde çalıştırın veya sorunu gidermek için ASP.NET Çekirdek Modülü stdout günlüğünü etkinleştirin.

### <a name="5000-in-process-handler-load-failure"></a>500.0 İşlem Içi İşleyici Yük Arızası

Alt işlem başarısız olur. Uygulama başlatılmıyor.

[Core Module bileşenleriASP.NET](xref:host-and-deploy/aspnet-core-module) yüklemede bilinmeyen bir hata oluştu. Aşağıdaki eylemlerden birini uygulayın:

* [Microsoft Desteği'ne](https://support.microsoft.com/oas/default.aspx?prid=15832) başvurun **(Geliştirici Araçları'nı** seçin ve **ardından ASP.NET Core' u**seçin).
* Stack Taşma hakkında bir soru sorun.
* [GitHub depomuzda](https://github.com/dotnet/AspNetCore)bir sorun dosyala.

### <a name="50030-in-process-startup-failure"></a>500.30 İşlem Içi Başlatma Hatası

Alt işlem başarısız olur. Uygulama başlatılmıyor.

[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) .NET Core CLR'yi başlatmaya çalışır, ancak başlatılmaz. İşlem başlatma hatasının nedeni genellikle Uygulama Olay Günlüğü'ndeki girişlerden ve ASP.NET Çekirdek Modülü stdout günlüğünden belirlenebilir.

Yaygın arıza koşulları:

* Uygulama, ASP.NET Core paylaşılan çerçevesinin mevcut olmayan bir sürümünü hedeflemesi nedeniyle yanlış yapılandırılmıştır. hedef makineye ASP.NET Core paylaşılan çerçevesinin hangi sürümlerinin yüklü olduğunu denetleyin.
* Azure Key Vault'u kullanarak, Anahtar Kasası'na izin verilmemesi. Doğru izinlerin verildiğinden emin olmak için hedeflenen Anahtar Kasası'ndaki erişim ilkelerini denetleyin.

### <a name="50031-ancm-failed-to-find-native-dependencies"></a>500.31 ANCM Yerel Bağımlılıkları Bulamayı Başaramadı

Alt işlem başarısız olur. Uygulama başlatılmıyor.

[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) .NET Core çalışma süresini başlatmaya çalışır, ancak başlatılmaz. Bu başlangıç hatasının en yaygın `Microsoft.NETCore.App` nedeni, çalışma `Microsoft.AspNetCore.App` zamanının yüklenmemesidir. Uygulama Core 3.0 ASP.NET hedeflemek üzere dağıtılırsa ve bu sürüm makinede yoksa, bu hata oluşur. Örnek bir hata iletisi aşağıdaki gibidir:

```
The specified framework 'Microsoft.NETCore.App', version '3.0.0' was not found.
  - The following frameworks were found:
      2.2.1 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview5-27626-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27713-13 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27714-15 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
      3.0.0-preview6-27723-08 at [C:\Program Files\dotnet\x64\shared\Microsoft.NETCore.App]
```

Hata iletisi, yüklenen tüm .NET Core sürümlerini ve uygulama tarafından istenen sürümü listeler. Bu hatayı düzeltmek için aşağıdakileri de:

* Makineye .NET Core'un uygun sürümünü yükleyin.
* Uygulamayı makinede bulunan .NET Core sürümünü hedef almak için değiştirin.
* Uygulamayı [bağımsız bir dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)olarak yayımlayın.

Geliştirme aşamasında çalışırken `ASPNETCORE_ENVIRONMENT` (ortam değişkeni `Development`ayarlanır), belirli bir hata HTTP yanıtına yazılır. İşlem başlatma hatasının nedeni, Uygulama Olayı Günlüğü'nde de bulunur.

### <a name="50032-ancm-failed-to-load-dll"></a>500.32 ANCM dll Yüklemek için başarısız oldu

Alt işlem başarısız olur. Uygulama başlatılmıyor.

Bu hatanın en yaygın nedeni, uygulamanın uyumsuz bir işlemci mimarisi için yayımlanmış olmasıdır. Alt işlem 32 bit uygulama olarak çalışıyorsa ve uygulama 64 bit hedef olarak yayımlanmışsa, bu hata oluşur.

Bu hatayı düzeltmek için aşağıdakileri de:

* Alt işlemle aynı işlemci mimarisi için uygulamayı yeniden yayımlayın.
* Uygulamayı [çerçeveye bağımlı dağıtım](/dotnet/core/deploying/#framework-dependent-executables-fde)olarak yayımlayın.

### <a name="50033-ancm-request-handler-load-failure"></a>500.33 ANCM İstek Işleyici Yük Arızası

Alt işlem başarısız olur. Uygulama başlatılmıyor.

Uygulama çerçeveye `Microsoft.AspNetCore.App` atıfta bulunmuyordu. Yalnızca çerçeveyi `Microsoft.AspNetCore.App` hedefleyen uygulamalar ASP.NET [Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module)tarafından barındırılabilir.

Bu hatayı düzeltmek için, uygulamanın `Microsoft.AspNetCore.App` çerçeveyi hedeflediğini doğrulayın. Uygulama `.runtimeconfig.json` tarafından hedeflenen çerçeveyi doğrulamak için kontrol edin.

### <a name="50034-ancm-mixed-hosting-models-not-supported"></a>500.34 ANCM Karma Hosting Modelleri Desteklenmiyor

Alt işlem, aynı işlemde hem bir işlem içi uygulama hem de işlem dışı bir uygulamayı çalıştıramaz.

Bu hatayı düzeltmek için uygulamaları ayrı IIS uygulama havuzlarında çalıştırın.

### <a name="50035-ancm-multiple-in-process-applications-in-same-process"></a>500.35 AYNı İşlemde ANCM Çoklu İşlem Uygulamaları

Alt işlem aynı işlemde birden çok işlem içi uygulama çalıştıramaz.

Bu hatayı düzeltmek için uygulamaları ayrı IIS uygulama havuzlarında çalıştırın.

### <a name="50036-ancm-out-of-process-handler-load-failure"></a>500.36 ANCM İşlem Dışı İşleyici Yük Arızası

İşlem dışı istek işleyicisi, *aspnetcorev2_outofprocess.dll,* *aspnetcorev2.dll* dosyasının yanında değildir. Bu, [ASP.NET Çekirdek Modülü'nün](xref:host-and-deploy/aspnet-core-module)bozuk bir yüklemesini gösterir.

Bu hatayı gidermek için [.NET Core Hosting Paketinin](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) (IIS için) veya Visual Studio (IIS Express için) kurulumunu onarın.

### <a name="50037-ancm-failed-to-start-within-startup-time-limit"></a>500.37 ANCM Başlangıç Süresi İçinde Başlayamamış

ANCM, önlenen başlangıç süresi içinde başlayamadı. Varsayılan olarak, zaman ası 120 saniyedir.

Bu hata, aynı makinede çok sayıda uygulama başlatırken oluşabilir. Başlangıç sırasında sunucuda CPU/Bellek kullanım artışlarını denetleyin. Birden çok uygulamanın başlangıç işlemini sendemeniz gerekebilir.

### <a name="5025-process-failure"></a>502.5 İşlem Hatası

Alt işlem başarısız olur. Uygulama başlatılmıyor.

[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) alt işlemi başlatmaya çalışır, ancak başlatılmaz. İşlem başlatma hatasının nedeni genellikle Uygulama Olay Günlüğü'ndeki girişlerden ve ASP.NET Çekirdek Modülü stdout günlüğünden belirlenebilir.

Yaygın bir hata koşulu, uygulamanın ASP.NET Core paylaşılan çerçevesinin mevcut olmayan bir sürümünü hedeflemesi nedeniyle yanlış yapılandırılmış olmasıdır. hedef makineye ASP.NET Core paylaşılan çerçevesinin hangi sürümlerinin yüklü olduğunu denetleyin. *Paylaşılan çerçeve,* makineye yüklenen *.dll* ve meta package gibi `Microsoft.AspNetCore.App`bir meta paketle başvurulan derlemeler kümesidir . Metapackage başvurusu en az gerekli sürümü belirtebilir. Daha fazla bilgi için [paylaşılan çerçeveye](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)bakın.

*502.5 İşlem Hatası* hatası sayfası, bir barındırma veya uygulama yanlış yapılandırması alt işlemin başarısız lığa neden olduğunda döndürülür:

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Uygulama başlatılamamış (ErrorCode '0x800700c1')

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Uygulamanın derlemesi *(.dll)* yüklenemediği için uygulama başlatılamadı.

Bu hata, yayınlanan uygulama ile w3wp/iisexpress işlemi arasında bir bitness uyuşmazlığı olduğunda oluşur.

Uygulama havuzunun 32 bit ayarı doğru olduğunu doğrulayın:

1. IIS Manager'ın **Uygulama Havuzları'ndaki**uygulama havuzunu seçin.
1. **Eylemler** panelinde **Uygulama Havuzu** altında Gelişmiş **Ayarlar'ı** seçin.
1. **32 Bit Uygulamaları Etkinleştir'i**Ayarlayın :
   * 32 bit (x86) bir uygulama dağıtıyorsanız, `True`değeri .
   * 64 bit (x64) bir uygulama dağıtıyorsanız, `False`değeri .

Proje dosyasındaki BIR `<Platform>` MSBuild özelliği ile uygulamanın yayınlanan bitliği arasında bir çakışma olmadığını doğrulayın.

### <a name="connection-reset"></a>Bağlantı sıfırlama

Üstbilgi gönderildikten sonra bir hata oluşursa, bir hata oluştuğunda sunucunun **500 Dahili Sunucu Hatası** göndermesi için çok geç olur. Bu genellikle bir yanıt için karmaşık nesnelerin serileştirme sırasında bir hata oluşur oluşur. Bu hata türü istemcide *bağlantı sıfırlama* hatası olarak görünür. [Uygulama günlüğe kaydetme,](xref:fundamentals/logging/index) bu tür hataları gidermede yardımcı olabilir.

### <a name="default-startup-limits"></a>Varsayılan başlangıç sınırları

[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) 120 saniyelik varsayılan *başlangıçTimeLimit* ile yapılandırılır. Varsayılan değerde bırakıldığında, bir uygulamanın bir işlem hatası günlüğe kaydetmesi iki dakika kadar sürebilir. Modülü yapılandırma hakkında daha fazla bilgi için [aspNetCore öğesinin Öznitelikleri'ne](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)bakın.

## <a name="troubleshoot-on-azure-app-service"></a>Azure Uygulama Hizmetinde Sorun Giderme

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Uygulama Etkinlik Günlüğü (Azure Uygulama Hizmeti)

Uygulama Olay Günlüğü'ne erişmek için Azure portalındaki **Tanılama ve sorunları çözme** sorununu kullanın:

1. Azure portalında Uygulamayı Uygulama **Hizmetleri'nde**açın.
1. **Sorunları tanılama ve çözme** seçeneğini belirleyin.
1. **Tanılama Araçları** başlığını seçin.
1. **Destek Araçları** **altında, Uygulama Etkinlikleri** düğmesini seçin.
1. **Kaynak** sütundaki *IIS AspNetCoreModule* veya *IIS AspNetCoreModule V2* girişi tarafından sağlanan en son hatayı inceleyin.

**Tanılama ve sorunları çözmenin** alternatifi, Uygulama Olay Günlüğü dosyasını doğrudan [Kudu](https://github.com/projectkudu/kudu/wiki)kullanarak incelemektir:

1. **Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın. **Git&rarr; ** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.
1. **LogFiles** klasörünü açın.
1. *eventlog.xml* dosyasının yanındaki kalem simgesini seçin.
1. Günlüğü inceleyin. En son olayları görmek için günlüğün altına gidin.

### <a name="run-the-app-in-the-kudu-console"></a>Uygulamayı Kudu konsolunda çalıştırın

Birçok başlangıç hatası, Uygulama Olay Günlüğü'nde yararlı bilgiler üretmez. Hatayı bulmak için uygulamayı [Kudu](https://github.com/projectkudu/kudu/wiki) Uzaktan Yürütme Konsolu'nda çalıştırabilirsiniz:

1. **Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın. **Git&rarr; ** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.

#### <a name="test-a-32-bit-x86-app"></a>32 bit (x86) uygulamayı test edin

**Geçerli sürüm**

1. `cd d:\home\site\wwwroot`
1. Uygulamayı çalıştırın:
   * Uygulama [çerçeveye bağımlı](/dotnet/core/deploying/#framework-dependent-deployments-fdd)bir dağıtım ise:

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:

     ```console
     {ASSEMBLY NAME}.exe
     ```

Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.

**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**

*Core {VERSION} (x86) Runtime site uzantısı ASP.NET yüklemeyi gerektirir.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` çalışma zamanı sürümüdür)
1. Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.

#### <a name="test-a-64-bit-x64-app"></a>64 bit (x64) uygulamayı test edin

**Geçerli sürüm**

* Uygulama 64 bit (x64) [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:
  1. `cd D:\Program Files\dotnet`
  1. Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:
  1. `cd D:\home\site\wwwroot`
  1. Uygulamayı çalıştırın: `{ASSEMBLY NAME}.exe`

Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.

**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**

*Core {VERSION} (x64) Runtime site uzantısı ASP.NET yüklemeyi gerektirir.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` çalışma zamanı sürümüdür)
1. Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>ASP.NET Çekirdek Modül stdout günlüğü (Azure Uygulama Hizmeti)

ASP.NET Çekirdek Modülü stdout günlüğü genellikle Uygulama Olay Günlüğü'nde bulunmayan yararlı hata iletileri kaydeder. Stdout günlüklerini etkinleştirmek ve görüntülemek için:

1. Azure portalında **Tanıla ve sorunları çöz'** e gidin.
1. **PROBLEM KATEGORİsİ SEÇ'in** **altında, Web Uygulaması Aşağı** düğmesini seçin.
1. **Önerilen Çözümler** > Altında **Stdout Log Redirection etkinleştirin**, **Web.Config'i yeniden etkinleştirmek için Kudu Konsolu'nu açın**düğmesini seçin.
1. Kudu **Tanı Konsolu,** yol **sitesi** > **wwwroot**klasörleri açın. Listenin altındaki *web.config* dosyasını ortaya çıkarmak için aşağı kaydırın.
1. *web.config* dosyasının yanındaki kalem simgesine tıklayın.
1. **StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** `\\?\%home%\LogFiles\stdout`yolunu şu şekilde değiştirin: .
1. Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet'i** seçin.
1. Uygulamaya bir istekte bulunun.
1. Azure portalına dönün. **GELİşTİrME ARAÇLARI** alanında **Gelişmiş Araçlar** bıçağını seçin. **Git&rarr; ** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.
1. Günlük **Dosyaları** klasörünü seçin.
1. **Değiştirilen** sütunu inceleyin ve en son değişiklik tarihiyle birlikte stdout günlüğünü deletmek için kalem simgesini seçin.
1. Günlük dosyası açıldığında, hata görüntülenir.

Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı edin:

1. Kudu **Tanı Konsolu,** *web.config* dosyasını ortaya çıkarmak için yol **sitesi** > **wwwroot** dönün. Kalem simgesini seçerek **web.config** dosyasını yeniden açın.
1. Set **stdoutLogEnabled** için `false`.
1. Dosyayı kaydetmek için **Kaydet'i** seçin.

Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir. Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur. Yalnızca uygulama başlatma sorunlarını gidermek için stdout günlük kullanın.
>
> Başlangıç tarihinden sonra bir ASP.NET Core uygulamasında genel oturum açmak için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın. Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.

### <a name="aspnet-core-module-debug-log-azure-app-service"></a>ASP.NET Çekirdek Modül hata ayıklama günlüğü (Azure Uygulama Hizmeti)

ASP.NET Çekirdek Modülü hata ayıklama günlüğü, ASP.NET Çekirdek Modülünden ek, daha derin günlüğe kaydetme sağlar. Stdout günlüklerini etkinleştirmek ve görüntülemek için:

1. Gelişmiş tanılama günlüğünü etkinleştirmek için aşağıdakilerden birini gerçekleştirin:
   * Gelişmiş bir tanı günlüğü için uygulamayı yapılandırmak için [Gelişmiş tanı günlükleri](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) yönergeleri izleyin. Uygulamayı yeniden dağıtın.
   * Kudu `<handlerSettings>` konsolu kullanarak canlı uygulamanın *web.config* dosyasına [Gelişmiş tanı günlüklerinde](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) gösterilenleri ekleyin:
     1. **Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın. **Git&rarr; ** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.
     1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.
     1. Klasörleri yol **sitesine** > aç**wwwroot**. Kalem düğmesini seçerek *web.config* dosyasını düzenleme. Gelişmiş `<handlerSettings>` [tanı günlüklerinde](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)gösterildiği gibi bölümü ekleyin. **Kaydet** düğmesini seçin.
1. **Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın. **Git&rarr; ** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.
1. Klasörleri yol **sitesine** > aç**wwwroot**. *aspnetcore-debug.log* dosyası için bir yol sağlamadıysanız, dosya listede görünür. Bir yol sağladıysanız, günlük dosyasının konumuna gidin.
1. Dosya adının yanındaki kalem düğmesiyle günlük dosyasını açın.

Sorun giderme tamamlandığında hata ayıklama günlemasını devre dışı kındırın:

Gelişmiş hata ayıklama günlüğünü devre dışı kalmak için aşağıdakilerden birini gerçekleştirin:

* `<handlerSettings>` *Web.config* dosyasını yerel olarak kaldırın ve uygulamayı yeniden dağıtın.
* *Web.config* dosyasını ve bölümü kaldırmak için Kudu konsoluna `<handlerSettings>` bakın. Dosyayı kaydedin.

Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

> [!WARNING]
> Hata ayıklama günlüğünün devre dışı edilmemesi uygulama veya sunucu hatasına neden olabilir. Günlük dosyası boyutunda bir sınır yoktur. Yalnızca uygulama başlatma sorunlarını gidermek için hata ayıklama günlüğe kaydetmeyi kullanın.
>
> Başlangıç tarihinden sonra bir ASP.NET Core uygulamasında genel oturum açmak için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın. Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.

### <a name="slow-or-hanging-app-azure-app-service"></a>Yavaş veya asma uygulaması (Azure Uygulama Hizmeti)

Bir uygulama yavaş yanıt verdiğinde veya bir isteğe bağlı kaldığında aşağıdaki makalelere bakın:

* [Azure App Service web uygulamasında yavaş performans sorunlarını giderme](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Azure Web Uygulamasında Aralıklı Özel Durum sorunları veya performans sorunları için Döküm'ü yakalamak için Kilitlenme Tanılayıcı Site Uzantısı'nı kullanma](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>İzleme bıçakları

İzleme bıçakları, konunun daha önce açıklanan yöntemlerine alternatif bir sorun giderme deneyimi sağlar. Bu bıçaklar 500 seri hataları teşhis etmek için kullanılabilir.

ASP.NET Çekirdek Uzantılarının yüklü olduğunu doğrulayın. Uzantılar yüklenmiyorsa, bunları el ile yükleyin:

1. **GELİşİm ARAÇLARI** bıçak bölümünde **Uzantılar** bıçağını seçin.
1. **ASP.NET Çekirdek Uzantıları** listede görünmelidir.
1. Uzantılar yüklenmiyorsa **Ekle** düğmesini seçin.
1. Listeden **ASP.NET Çekirdek Uzantıları'nı** seçin.
1. Yasal koşulları kabul etmek için **Tamam'ı** seçin.
1. **Ekle uzantı** bıçağında **Tamam'ı** seçin.
1. Bilgilendirimiolan bir açılır ileti, uzantıların başarıyla ne zaman yüklenir olduğunu gösterir.

Stdout günlüğe kaydetme etkin değilse, aşağıdaki adımları izleyin:

1. Azure portalında, **GELIŞTIRME ARAÇLARı** alanındaki **Gelişmiş Araçlar** bıçak larını seçin. **Git&rarr; ** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.
1. Klasörleri yol **sitesi** > **wwwroot'a** açın ve listenin altındaki *web.config* dosyasını ortaya çıkarmak için aşağı kaydırın.
1. *web.config* dosyasının yanındaki kalem simgesine tıklayın.
1. **StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** `\\?\%home%\LogFiles\stdout`yolunu şu şekilde değiştirin: .
1. Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet'i** seçin.

Tanısal günlüğü etkinleştirmeye devam edin:

1. Azure portalında **Tanılama günlükleri** bıçağını seçin.
1. **Uygulama Günlüğe Kaydetme (Filesystem)** ve **Ayrıntılı hata iletileri**için **Açık** anahtarını seçin. Bıçağın üst kısmındaki **Kaydet** düğmesini seçin.
1. Başarısız İstek Olay Arabelleği (FREB) günlüğe kaydetme olarak da bilinen başarısız istek izlemesini eklemek için, **Başarısız istek izleme**için **Açık** anahtarını seçin.
1. Portaldaki **Teşhis günlükleri** bıçağının hemen altında listelenen **Log akış** bıçağını seçin.
1. Uygulamaya bir istekte bulunun.
1. Günlük akışı verileri içinde, hatanın nedeni belirtilir.

Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı bettiğinizden emin olun.

Başarısız istek izleme günlüklerini (FREB günlükleri) görüntülemek için:

1. Azure portalında **Tanıla ve sorunları çöz'** e gidin.
1. Kenar çubuğunun **DESTEK ARAÇLARI** alanından **Başarısız İstek İzleme Günlükleri'ni** seçin.

Azure Uygulama Hizmeti konusundaki web uygulamaları için etkinleştir meslüpleri oturum açma [bölümünün başarısız istek izlemeleri bölümüne](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) ve [Azure'daki Web Apps Uygulamaları için Uygulama performansı SSS'lerine bakın: Daha](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) fazla bilgi için başarısız istek izlemesini nasıl açarım?

Daha fazla bilgi için azure [Uygulama Hizmeti'ndeki web uygulamaları için tanılama günlüğe kaydetme'ye](/azure/app-service/web-sites-enable-diagnostic-log)bakın.

> [!WARNING]
> Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir. Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.
>
> ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın. Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.

## <a name="troubleshoot-on-iis"></a>IIS üzerinde sorun giderme

### <a name="application-event-log-iis"></a>Uygulama Etkinlik Günlüğü (IIS)

Uygulama Etkinlik Günlüğüne Erişin:

1. Başlat menüsünü açın, *Olay Görüntüleyicisi'ni*arayın ve **Olay Görüntüleyicisi** uygulamasını seçin.
1. **Olay Görüntüleyici'de** **Windows Günlükleri** düğümlerini açın.
1. Uygulama Olay Günlüğü'nü açmak için **Uygulama'yı** seçin.
1. Başarısız uygulamayla ilişkili hataları arayın. Hatalar *Kaynak* sütunda *IIS AspNetCore Modülü* veya *IIS Express AspNetCore Modülü* değerine sahiptir.

### <a name="run-the-app-at-a-command-prompt"></a>Uygulamayı komut istemiyle çalıştırma

Birçok başlangıç hatası, Uygulama Olay Günlüğü'nde yararlı bilgiler üretmez. Bazı hataların nedenini, uygulamayı barındırma sistemindeki bir komut isteminde çalıştırarak bulabilirsiniz.

#### <a name="framework-dependent-deployment"></a>Çerçeveye bağımlı dağıtım

Uygulama [çerçeveye bağımlı](/dotnet/core/deploying/#framework-dependent-deployments-fdd)bir dağıtım ise:

1. Komut isteminde dağıtım klasörüne gidin ve uygulamanın montajını *dotnet.exe*ile çalıştırarak uygulamayı çalıştırın. Aşağıdaki komutta, uygulamanın derlemesinin adını assembly_name \<> `dotnet .\<assembly_name>.dll`yerine değiştirin: .
1. Uygulamadan gelen konsol çıkışı, herhangi bir hata gösterirken, konsol penceresine yazılır.
1. Uygulama için istekte bulunurken hatalar oluşursa, Kerkenez'in dinlediği ana bilgisayara ve bağlantı noktasına bir istekte bulunun. Varsayılan ana bilgisayar ve gönderiyi `http://localhost:5000/`kullanarak, '' için bir istekte bulunun. Uygulama Kestrel bitiş noktası adresinde normal yanıt veriyorsa, sorun daha çok barındırma yapılandırması ile ilgilidir ve uygulama içinde daha az olasıdır.

#### <a name="self-contained-deployment"></a>Bağımsız dağıtım

Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:

1. Komut isteminde dağıtım klasörüne gidin ve uygulamanın yürütülebilir uygulamasını çalıştırın. Aşağıdaki komutta, uygulamanın derlemesinin adını assembly_name \<> `<assembly_name>.exe`yerine değiştirin: .
1. Uygulamadan gelen konsol çıkışı, herhangi bir hata gösterirken, konsol penceresine yazılır.
1. Uygulama için istekte bulunurken hatalar oluşursa, Kerkenez'in dinlediği ana bilgisayara ve bağlantı noktasına bir istekte bulunun. Varsayılan ana bilgisayar ve gönderiyi `http://localhost:5000/`kullanarak, '' için bir istekte bulunun. Uygulama Kestrel bitiş noktası adresinde normal yanıt veriyorsa, sorun daha çok barındırma yapılandırması ile ilgilidir ve uygulama içinde daha az olasıdır.

### <a name="aspnet-core-module-stdout-log-iis"></a>ASP.NET Çekirdek Modülü stdout log (IIS)

Stdout günlüklerini etkinleştirmek ve görüntülemek için:

1. Barındırma sisteminde sitenin dağıtım klasörüne gidin.
1. *Günlükler* klasörü yoksa, klasörü oluşturun. MSBuild'in dağıtımdaki *günlükler* klasörünü otomatik olarak oluşturmasına nasıl etkinleştirilen talimatlar için [Dizin yapısı](xref:host-and-deploy/directory-structure) konusuna bakın.
1. *Web.config* dosyasını edin. **StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** yolunu *günlükler* klasörüne (örneğin) `.\logs\stdout`işaret etmek için değiştirin. `stdout`yoldaki günlük dosya adı önekidir. Günlük oluşturulduğunda bir zaman damgası, işlem kimliği ve dosya uzantısı otomatik olarak eklenir. Dosya `stdout` adı öneki olarak kullanarak, tipik bir günlük dosyası *stdout_20180205184032_5412.log*olarak adlandırılır.
1. Uygulama havuzunuzun kimliğinin *günlükler* klasörüne yazma izinleri olduğundan emin olun.
1. Güncelleştirilmiş *web.config* dosyasını kaydedin.
1. Uygulamaya bir istekte bulunun.
1. *Günlükler* klasörüne gidin. En son stdout günlüğünü bulun ve açın.
1. Hatalar için günlüğü inceleyin.

Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı edin:

1. *Web.config* dosyasını edin.
1. Set **stdoutLogEnabled** için `false`.
1. Dosyayı kaydedin.

Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir. Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.
>
> ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın. Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.

### <a name="aspnet-core-module-debug-log-iis"></a>ASP.NET Çekirdek Modülü hata ayıklama günlüğü (IIS)

Temel Modül hata ayıklama günlüğünü ASP.NET etkinleştirmek için uygulamanın *web.config* dosyasına aşağıdaki işleyici ayarlarını ekleyin:

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

Günlük için belirtilen yolun var olduğunu ve uygulama havuzunun kimliğinin konuma yazma izinleri olduğunu doğrulayın.

Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

### <a name="enable-the-developer-exception-page"></a>Geliştirici Özel Durum Sayfasını Etkinleştirme

Ortamı `ASPNETCORE_ENVIRONMENT` geliştirme ortamında çalıştırmak için [web.config'e ortam değişkeni eklenebilir.](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) Ortam, ana bilgisayar `UseEnvironment` oluşturucutarafından uygulama başlatmada geçersiz kılınmadığı sürece, ortam değişkenini ayarlamak, uygulama çalıştırıldığında Geliştirici Özel Durum [Sayfasının](xref:fundamentals/error-handling) görünmesini sağlar.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

Ortam değişkenini `ASPNETCORE_ENVIRONMENT` ayarlamak yalnızca Internet'e maruz olmayan hazırlama ve sınama sunucularında kullanılması önerilir. Sorun giderme den sonra *web.config* dosyasından ortam değişkenini kaldırın. *web.config'de*ortam değişkenlerini ayarlama hakkında bilgi için [bkz.](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)

### <a name="obtain-data-from-an-app"></a>Bir uygulamadan veri alma

Bir uygulama isteklere yanıt verebiliyorsa, terminal sıralı ara yazılımları kullanarak uygulamadan istek, bağlantı ve ek veri alın. Daha fazla bilgi ve <xref:test/troubleshoot#obtain-data-from-an-app>örnek kod için bkz.

### <a name="slow-or-hanging-app-iis"></a>Yavaş veya asılı uygulama (IIS)

*Kilitlenme dökümü,* sistemin belleğindebir anlık görüntüdür ve bir uygulamanın çökmesinin, başlangıç hatasının veya yavaş uygulamanın nedenini belirlemeye yardımcı olabilir.

#### <a name="app-crashes-or-encounters-an-exception"></a>Uygulama çöküyor veya bir özel durumla karşılaşıyor

Windows Hata Raporlama [(WER)](/windows/desktop/wer/windows-error-reporting)bir dökümü edinin ve analiz:

1. Kilitlenme döküm dosyalarını '' adresinde `c:\dumps`tutmak için bir klasör oluşturun Uygulama havuzunun klasöre yazma erişimi olmalıdır.
1. [EnableDumps PowerShell komut dosyasını](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)çalıştırın:
   * Uygulama [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için komut dosyası çalıştırın:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için komut dosyası çalıştırın:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Uygulamayı çökmenin oluşmasına neden olan koşullar altında çalıştırın.
1. Çökme oluştuktan sonra, [DisableDumps PowerShell komut dosyası](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)çalıştırın:
   * Uygulama [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için komut dosyası çalıştırın:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için komut dosyası çalıştırın:

     ```console
     .\DisableDumps dotnet.exe
     ```

Bir uygulama çöktükten ve döküm toplama tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir. PowerShell komut dosyası, WER'i uygulama başına en fazla beş çöplük toplamak üzere yapılandırır.

> [!WARNING]
> Kilitlenme dökümleri büyük miktarda disk alanı kapsayabilir (her biri birkaç gigabayta kadar).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Uygulama askıda kalır, başlangıç sırasında başarısız olur veya normal çalışır

Bir uygulama *askıda kaldığında* (yanıt vermeyi durdurur ancak kilitlenmez), başlangıç sırasında başarısız olduğunda veya normal olarak çalıştığında, [bkz.](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)

#### <a name="analyze-the-dump"></a>Dökümü analiz edin

Bir dökümü çeşitli yaklaşımlar kullanılarak analiz edilebilir. Daha fazla bilgi için [bkz.](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)

## <a name="clear-package-caches"></a>Paket önbelleklerini temizle

İşleyen bir uygulama, geliştirme makinesindeki .NET Core SDK'yı yükselttikten veya uygulama içindeki paket sürümlerini değiştirdikten hemen sonra başarısız olabilir. Bazı durumlarda, tutarsız paketler büyük yükseltmeler gerçekleştirirken bir uygulamayı bozabilir. Bu sorunların çoğu aşağıdaki yönergeleri izleyerek giderilebilir:

1. *Bin* ve *obj* klasörlerini silin.
1. [Noktanet nuget yerel tüm](/dotnet/core/tools/dotnet-nuget-locals) çalıştırarak paket önbellekleri temizleyin - bir komut kabuğundan temizleyin.

   Paket önbellekleri takas da [nuget.exe](https://www.nuget.org/downloads) aracı ile gerçekleştirilebilir `nuget locals all -clear`ve komutuyguluyor. *nuget.exe,* Windows masaüstü işletim sistemiyle birlikte yüklenmez ve [NuGet web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.

1. Projeyi geri yükleyin ve yeniden oluşturun.
1. Uygulamayı yeniden dağıtmadan önce sunucudaki dağıtım klasöründeki tüm dosyaları silin.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Azure belgeleri

* [ASP.NET Core için Application Insights](/azure/application-insights/app-insights-asp-net-core)
* [Visual Studio'yu kullanarak Azure Uygulama Hizmeti'ndeki bir web uygulamasını sorun giderme bölümünün uzaktan hata ayıklama web uygulamaları bölümü](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Azure Uygulama Hizmeti tanılama genel bakış](/azure/app-service/app-service-diagnostics)
* [Nasıl Yapılır: Azure App Service’te Uygulamaları İzleme](/azure/app-service/web-sites-monitor)
* [Visual Studio'yu kullanarak Azure Uygulama Hizmeti'ndeki bir web uygulamasını sorun giderme](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Azure web uygulamalarınızda HTTP hatalarını "502 kötü ağ geçidi" ve "503 hizmeti kullanılamıyor" hatalarını giderme](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Azure App Service web uygulamasında yavaş performans sorunlarını giderme](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Azure'daki Web Apps için Uygulama performansı SSS'leri](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Azure Web App sandbox (App Service çalışma zamanı yürütme sınırlamaları)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Azure Cuma: Azure Uygulama Hizmeti Tanılama ve Sorun Giderme Deneyimi (12 dakikalık video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Visual Studio belgeleri

* [Visual Studio 2017'de Azure'da IIS'de Uzaktan Hata Ayıklama ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)
* [Visual Studio 2017'de Uzaktan IIS Bilgisayarında Uzaktan Hata Ayıklama ASP.NET Core](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Visual Studio kullanarak hata ayıklamayı öğrenin](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Visual Studio Code dokümantasyonu

* [Visual Studio Code ile Hata Ayıklama](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Bu makalede, bir uygulama Azure Uygulama Hizmeti veya IIS'ye dağıtıldığında hataların nasıl tanılanacağına ilişkin yaygın uygulama başlangıç hataları ve yönergeler hakkında bilgiler verilmektedir:

[Uygulama başlatma hataları](#app-startup-errors)  
Ortak başlangıç HTTP durum kodu senaryolarını açıklar.

[Azure Uygulama Hizmetinde Sorun Giderme](#troubleshoot-on-azure-app-service)  
Azure Uygulama Hizmeti'ne dağıtılan uygulamalar için sorun giderme önerileri sağlar.

[IIS üzerinde sorun giderme](#troubleshoot-on-iis)  
IIS'ye dağıtılan veya IIS Express'te yerel olarak çalışan uygulamalar için sorun giderme önerileri sağlar. Kılavuz, hem Windows Server hem de Windows masaüstü dağıtımları için geçerlidir.

[Paket önbelleklerini temizle](#clear-package-caches)  
Büyük yükseltmeler yaparken veya paket sürümlerini değiştirirken tutarsız paketler bir uygulamayı kırdığında ne yapması gerektiğini açıklar.

[Ek kaynaklar](#additional-resources)  
Ek sorun giderme konularını listeler.

## <a name="app-startup-errors"></a>Uygulama başlatma hataları

Visual Studio'da, ASP.NET Core projesi hata ayıklama sırasında [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) barındırma için varsayılandır. A *502.5 - İşlem Hatası* veya *500.30 -* Yerel hata ayıklama zaman ortaya çıkan Başlat Hatası bu konuda ki tavsiye kullanılarak teşhis edilebilir.

### <a name="40314-forbidden"></a>403.14 Yasak

Uygulama başlatılmaz. Aşağıdaki hata günlüğe kaydedilir:

```
The Web server is configured to not list the contents of this directory.
```

Hata genellikle aşağıdaki senaryolardan herhangi birini içeren barındırma sisteminde ki bozuk dağıtımdan kaynaklanır:

* Uygulama barındırma sisteminde yanlış klasöre dağıtılır.
* Dağıtım işlemi, uygulamanın tüm dosya ve klasörlerini barındırma sistemindeki dağıtım klasörüne taşıyamadı.
* *web.config* dosyası dağıtımda eksik veya *web.config* dosyası içeriği yanlış biçimlendirilmiştir.

Aşağıdaki adımları uygulayın:

1. Barındırma sistemindeki dağıtım klasöründeki tüm dosya ve klasörleri silin.
1. Visual Studio, PowerShell veya manuel dağıtım gibi normal dağıtım yönteminizi kullanarak uygulamanın *yayımlama* klasörünün içeriğini barındırma sistemine yeniden dağıtın:
   * *web.config* dosyasının dağıtımda mevcut olduğunu ve içeriğinin doğru olduğunu doğrulayın.
   * Azure Uygulama Hizmeti'nde barındırma yaparken, uygulamanın `D:\home\site\wwwroot` klasöre dağıtıldığından onaylayın.
   * Uygulama IIS tarafından barındırıldığında, uygulamanın **IIS Yöneticisi'nin**Temel **Ayarları'nda**gösterilen IIS **Fiziksel yoluna** dağıtıldığından onaylayın.
1. Barındırma sistemindeki dağıtımı projenin *yayımlama* klasörünün içeriğiyle karşılaştırarak uygulamanın tüm dosya ve klasörlerinin dağıtılmış olduğunu doğrulayın.

Yayınlanan bir ASP.NET Core uygulamasının düzeni hakkında <xref:host-and-deploy/directory-structure>daha fazla bilgi için bkz. *web.config* dosyasında daha fazla <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>bilgi için bkz.

### <a name="500-internal-server-error"></a>500 İç Sunucu Hatası

Uygulama başlar, ancak bir hata sunucunun isteği yerine getirmesini engeller.

Bu hata, başlangıç sırasında veya yanıt oluştururken uygulamanın kodu içinde oluşur. Yanıt hiçbir içerik içermeyebilir veya yanıt tarayıcıda *500 Dahili Sunucu Hatası* olarak görünebilir. Uygulama Olay Günlüğü genellikle uygulamanın normal olarak başladığını belirtir. Sunucunun bakış açısından, bu doğru. Uygulama başladı, ancak geçerli bir yanıt oluşturamıyor. Uygulamayı sunucudaki bir komut isteminde çalıştırın veya sorunu gidermek için ASP.NET Çekirdek Modülü stdout günlüğünü etkinleştirin.

### <a name="5000-in-process-handler-load-failure"></a>500.0 İşlem Içi İşleyici Yük Arızası

Alt işlem başarısız olur. Uygulama başlatılmıyor.

[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) .NET Core CLR'yi ve işlem içi istek işleyicisini *(aspnetcorev2_inprocess.dll)* bulamaz. Şuna bakın:

* Uygulama ya [Microsoft.AspNetCore.Server.IIS](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) NuGet paketi veya [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app)hedefliyor.
* ASP.NET Core'un, uygulama hedeflerinin hedef makineye yüklenmiş olduğu paylaşılan çerçevesi nin sürümü.

### <a name="5000-out-of-process-handler-load-failure"></a>500.0 İşlem Dışı İşleyici Yük Arızası

Alt işlem başarısız olur. Uygulama başlatılmıyor.

[ASP.NET Çekirdek Modülü,](xref:host-and-deploy/aspnet-core-module) işlem dışı barındırma isteği işleyicisini bulamaz. *aspnetcorev2_outofprocess.dll'nin* *aspnetcorev2.dll'nin*yanındaki bir alt klasörde bulunduğundan emin olun.

### <a name="5025-process-failure"></a>502.5 İşlem Hatası

Alt işlem başarısız olur. Uygulama başlatılmıyor.

[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) alt işlemi başlatmaya çalışır, ancak başlatılmaz. İşlem başlatma hatasının nedeni genellikle Uygulama Olay Günlüğü'ndeki girişlerden ve ASP.NET Çekirdek Modülü stdout günlüğünden belirlenebilir.

Yaygın bir hata koşulu, uygulamanın ASP.NET Core paylaşılan çerçevesinin mevcut olmayan bir sürümünü hedeflemesi nedeniyle yanlış yapılandırılmış olmasıdır. hedef makineye ASP.NET Core paylaşılan çerçevesinin hangi sürümlerinin yüklü olduğunu denetleyin. *Paylaşılan çerçeve,* makineye yüklenen *.dll* ve meta package gibi `Microsoft.AspNetCore.App`bir meta paketle başvurulan derlemeler kümesidir . Metapackage başvurusu en az gerekli sürümü belirtebilir. Daha fazla bilgi için [paylaşılan çerçeveye](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)bakın.

*502.5 İşlem Hatası* hatası sayfası, bir barındırma veya uygulama yanlış yapılandırması alt işlemin başarısız lığa neden olduğunda döndürülür:

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Uygulama başlatılamamış (ErrorCode '0x800700c1')

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Uygulamanın derlemesi *(.dll)* yüklenemediği için uygulama başlatılamadı.

Bu hata, yayınlanan uygulama ile w3wp/iisexpress işlemi arasında bir bitness uyuşmazlığı olduğunda oluşur.

Uygulama havuzunun 32 bit ayarı doğru olduğunu doğrulayın:

1. IIS Manager'ın **Uygulama Havuzları'ndaki**uygulama havuzunu seçin.
1. **Eylemler** panelinde **Uygulama Havuzu** altında Gelişmiş **Ayarlar'ı** seçin.
1. **32 Bit Uygulamaları Etkinleştir'i**Ayarlayın :
   * 32 bit (x86) bir uygulama dağıtıyorsanız, `True`değeri .
   * 64 bit (x64) bir uygulama dağıtıyorsanız, `False`değeri .

Proje dosyasındaki BIR `<Platform>` MSBuild özelliği ile uygulamanın yayınlanan bitliği arasında bir çakışma olmadığını doğrulayın.

### <a name="connection-reset"></a>Bağlantı sıfırlama

Üstbilgi gönderildikten sonra bir hata oluşursa, bir hata oluştuğunda sunucunun **500 Dahili Sunucu Hatası** göndermesi için çok geç olur. Bu genellikle bir yanıt için karmaşık nesnelerin serileştirme sırasında bir hata oluşur oluşur. Bu hata türü istemcide *bağlantı sıfırlama* hatası olarak görünür. [Uygulama günlüğe kaydetme,](xref:fundamentals/logging/index) bu tür hataları gidermede yardımcı olabilir.

### <a name="default-startup-limits"></a>Varsayılan başlangıç sınırları

[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) 120 saniyelik varsayılan *başlangıçTimeLimit* ile yapılandırılır. Varsayılan değerde bırakıldığında, bir uygulamanın bir işlem hatası günlüğe kaydetmesi iki dakika kadar sürebilir. Modülü yapılandırma hakkında daha fazla bilgi için [aspNetCore öğesinin Öznitelikleri'ne](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)bakın.

## <a name="troubleshoot-on-azure-app-service"></a>Azure Uygulama Hizmetinde Sorun Giderme

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Uygulama Etkinlik Günlüğü (Azure Uygulama Hizmeti)

Uygulama Olay Günlüğü'ne erişmek için Azure portalındaki **Tanılama ve sorunları çözme** sorununu kullanın:

1. Azure portalında Uygulamayı Uygulama **Hizmetleri'nde**açın.
1. **Sorunları tanılama ve çözme** seçeneğini belirleyin.
1. **Tanılama Araçları** başlığını seçin.
1. **Destek Araçları** **altında, Uygulama Etkinlikleri** düğmesini seçin.
1. **Kaynak** sütundaki *IIS AspNetCoreModule* veya *IIS AspNetCoreModule V2* girişi tarafından sağlanan en son hatayı inceleyin.

**Tanılama ve sorunları çözmenin** alternatifi, Uygulama Olay Günlüğü dosyasını doğrudan [Kudu](https://github.com/projectkudu/kudu/wiki)kullanarak incelemektir:

1. **Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın. **Git&rarr; ** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.
1. **LogFiles** klasörünü açın.
1. *eventlog.xml* dosyasının yanındaki kalem simgesini seçin.
1. Günlüğü inceleyin. En son olayları görmek için günlüğün altına gidin.

### <a name="run-the-app-in-the-kudu-console"></a>Uygulamayı Kudu konsolunda çalıştırın

Birçok başlangıç hatası, Uygulama Olay Günlüğü'nde yararlı bilgiler üretmez. Hatayı bulmak için uygulamayı [Kudu](https://github.com/projectkudu/kudu/wiki) Uzaktan Yürütme Konsolu'nda çalıştırabilirsiniz:

1. **Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın. **Git&rarr; ** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.

#### <a name="test-a-32-bit-x86-app"></a>32 bit (x86) uygulamayı test edin

**Geçerli sürüm**

1. `cd d:\home\site\wwwroot`
1. Uygulamayı çalıştırın:
   * Uygulama [çerçeveye bağımlı](/dotnet/core/deploying/#framework-dependent-deployments-fdd)bir dağıtım ise:

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:

     ```console
     {ASSEMBLY NAME}.exe
     ```

Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.

**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**

*Core {VERSION} (x86) Runtime site uzantısı ASP.NET yüklemeyi gerektirir.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` çalışma zamanı sürümüdür)
1. Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.

#### <a name="test-a-64-bit-x64-app"></a>64 bit (x64) uygulamayı test edin

**Geçerli sürüm**

* Uygulama 64 bit (x64) [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:
  1. `cd D:\Program Files\dotnet`
  1. Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:
  1. `cd D:\home\site\wwwroot`
  1. Uygulamayı çalıştırın: `{ASSEMBLY NAME}.exe`

Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.

**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**

*Core {VERSION} (x64) Runtime site uzantısı ASP.NET yüklemeyi gerektirir.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` çalışma zamanı sürümüdür)
1. Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>ASP.NET Çekirdek Modül stdout günlüğü (Azure Uygulama Hizmeti)

ASP.NET Çekirdek Modülü stdout günlüğü genellikle Uygulama Olay Günlüğü'nde bulunmayan yararlı hata iletileri kaydeder. Stdout günlüklerini etkinleştirmek ve görüntülemek için:

1. Azure portalında **Tanıla ve sorunları çöz'** e gidin.
1. **PROBLEM KATEGORİsİ SEÇ'in** **altında, Web Uygulaması Aşağı** düğmesini seçin.
1. **Önerilen Çözümler** > Altında **Stdout Log Redirection etkinleştirin**, **Web.Config'i yeniden etkinleştirmek için Kudu Konsolu'nu açın**düğmesini seçin.
1. Kudu **Tanı Konsolu,** yol **sitesi** > **wwwroot**klasörleri açın. Listenin altındaki *web.config* dosyasını ortaya çıkarmak için aşağı kaydırın.
1. *web.config* dosyasının yanındaki kalem simgesine tıklayın.
1. **StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** `\\?\%home%\LogFiles\stdout`yolunu şu şekilde değiştirin: .
1. Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet'i** seçin.
1. Uygulamaya bir istekte bulunun.
1. Azure portalına dönün. **GELİşTİrME ARAÇLARI** alanında **Gelişmiş Araçlar** bıçağını seçin. **Git&rarr; ** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.
1. Günlük **Dosyaları** klasörünü seçin.
1. **Değiştirilen** sütunu inceleyin ve en son değişiklik tarihiyle birlikte stdout günlüğünü deletmek için kalem simgesini seçin.
1. Günlük dosyası açıldığında, hata görüntülenir.

Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı edin:

1. Kudu **Tanı Konsolu,** *web.config* dosyasını ortaya çıkarmak için yol **sitesi** > **wwwroot** dönün. Kalem simgesini seçerek **web.config** dosyasını yeniden açın.
1. Set **stdoutLogEnabled** için `false`.
1. Dosyayı kaydetmek için **Kaydet'i** seçin.

Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir. Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur. Yalnızca uygulama başlatma sorunlarını gidermek için stdout günlük kullanın.
>
> Başlangıç tarihinden sonra bir ASP.NET Core uygulamasında genel oturum açmak için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın. Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.

### <a name="aspnet-core-module-debug-log-azure-app-service"></a>ASP.NET Çekirdek Modül hata ayıklama günlüğü (Azure Uygulama Hizmeti)

ASP.NET Çekirdek Modülü hata ayıklama günlüğü, ASP.NET Çekirdek Modülünden ek, daha derin günlüğe kaydetme sağlar. Stdout günlüklerini etkinleştirmek ve görüntülemek için:

1. Gelişmiş tanılama günlüğünü etkinleştirmek için aşağıdakilerden birini gerçekleştirin:
   * Gelişmiş bir tanı günlüğü için uygulamayı yapılandırmak için [Gelişmiş tanı günlükleri](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) yönergeleri izleyin. Uygulamayı yeniden dağıtın.
   * Kudu `<handlerSettings>` konsolu kullanarak canlı uygulamanın *web.config* dosyasına [Gelişmiş tanı günlüklerinde](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) gösterilenleri ekleyin:
     1. **Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın. **Git&rarr; ** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.
     1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.
     1. Klasörleri yol **sitesine** > aç**wwwroot**. Kalem düğmesini seçerek *web.config* dosyasını düzenleme. Gelişmiş `<handlerSettings>` [tanı günlüklerinde](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)gösterildiği gibi bölümü ekleyin. **Kaydet** düğmesini seçin.
1. **Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın. **Git&rarr; ** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.
1. Klasörleri yol **sitesine** > aç**wwwroot**. *aspnetcore-debug.log* dosyası için bir yol sağlamadıysanız, dosya listede görünür. Bir yol sağladıysanız, günlük dosyasının konumuna gidin.
1. Dosya adının yanındaki kalem düğmesiyle günlük dosyasını açın.

Sorun giderme tamamlandığında hata ayıklama günlemasını devre dışı kındırın:

Gelişmiş hata ayıklama günlüğünü devre dışı kalmak için aşağıdakilerden birini gerçekleştirin:

* `<handlerSettings>` *Web.config* dosyasını yerel olarak kaldırın ve uygulamayı yeniden dağıtın.
* *Web.config* dosyasını ve bölümü kaldırmak için Kudu konsoluna `<handlerSettings>` bakın. Dosyayı kaydedin.

Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

> [!WARNING]
> Hata ayıklama günlüğünün devre dışı edilmemesi uygulama veya sunucu hatasına neden olabilir. Günlük dosyası boyutunda bir sınır yoktur. Yalnızca uygulama başlatma sorunlarını gidermek için hata ayıklama günlüğe kaydetmeyi kullanın.
>
> Başlangıç tarihinden sonra bir ASP.NET Core uygulamasında genel oturum açmak için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın. Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.

### <a name="slow-or-hanging-app-azure-app-service"></a>Yavaş veya asma uygulaması (Azure Uygulama Hizmeti)

Bir uygulama yavaş yanıt verdiğinde veya bir isteğe bağlı kaldığında aşağıdaki makalelere bakın:

* [Azure App Service web uygulamasında yavaş performans sorunlarını giderme](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Azure Web Uygulamasında Aralıklı Özel Durum sorunları veya performans sorunları için Döküm'ü yakalamak için Kilitlenme Tanılayıcı Site Uzantısı'nı kullanma](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>İzleme bıçakları

İzleme bıçakları, konunun daha önce açıklanan yöntemlerine alternatif bir sorun giderme deneyimi sağlar. Bu bıçaklar 500 seri hataları teşhis etmek için kullanılabilir.

ASP.NET Çekirdek Uzantılarının yüklü olduğunu doğrulayın. Uzantılar yüklenmiyorsa, bunları el ile yükleyin:

1. **GELİşİm ARAÇLARI** bıçak bölümünde **Uzantılar** bıçağını seçin.
1. **ASP.NET Çekirdek Uzantıları** listede görünmelidir.
1. Uzantılar yüklenmiyorsa **Ekle** düğmesini seçin.
1. Listeden **ASP.NET Çekirdek Uzantıları'nı** seçin.
1. Yasal koşulları kabul etmek için **Tamam'ı** seçin.
1. **Ekle uzantı** bıçağında **Tamam'ı** seçin.
1. Bilgilendirimiolan bir açılır ileti, uzantıların başarıyla ne zaman yüklenir olduğunu gösterir.

Stdout günlüğe kaydetme etkin değilse, aşağıdaki adımları izleyin:

1. Azure portalında, **GELIŞTIRME ARAÇLARı** alanındaki **Gelişmiş Araçlar** bıçak larını seçin. **Git&rarr; ** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.
1. Klasörleri yol **sitesi** > **wwwroot'a** açın ve listenin altındaki *web.config* dosyasını ortaya çıkarmak için aşağı kaydırın.
1. *web.config* dosyasının yanındaki kalem simgesine tıklayın.
1. **StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** `\\?\%home%\LogFiles\stdout`yolunu şu şekilde değiştirin: .
1. Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet'i** seçin.

Tanısal günlüğü etkinleştirmeye devam edin:

1. Azure portalında **Tanılama günlükleri** bıçağını seçin.
1. **Uygulama Günlüğe Kaydetme (Filesystem)** ve **Ayrıntılı hata iletileri**için **Açık** anahtarını seçin. Bıçağın üst kısmındaki **Kaydet** düğmesini seçin.
1. Başarısız İstek Olay Arabelleği (FREB) günlüğe kaydetme olarak da bilinen başarısız istek izlemesini eklemek için, **Başarısız istek izleme**için **Açık** anahtarını seçin.
1. Portaldaki **Teşhis günlükleri** bıçağının hemen altında listelenen **Log akış** bıçağını seçin.
1. Uygulamaya bir istekte bulunun.
1. Günlük akışı verileri içinde, hatanın nedeni belirtilir.

Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı bettiğinizden emin olun.

Başarısız istek izleme günlüklerini (FREB günlükleri) görüntülemek için:

1. Azure portalında **Tanıla ve sorunları çöz'** e gidin.
1. Kenar çubuğunun **DESTEK ARAÇLARI** alanından **Başarısız İstek İzleme Günlükleri'ni** seçin.

Azure Uygulama Hizmeti konusundaki web uygulamaları için etkinleştir meslüpleri oturum açma [bölümünün başarısız istek izlemeleri bölümüne](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) ve [Azure'daki Web Apps Uygulamaları için Uygulama performansı SSS'lerine bakın: Daha](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) fazla bilgi için başarısız istek izlemesini nasıl açarım?

Daha fazla bilgi için azure [Uygulama Hizmeti'ndeki web uygulamaları için tanılama günlüğe kaydetme'ye](/azure/app-service/web-sites-enable-diagnostic-log)bakın.

> [!WARNING]
> Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir. Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.
>
> ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın. Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.

## <a name="troubleshoot-on-iis"></a>IIS üzerinde sorun giderme

### <a name="application-event-log-iis"></a>Uygulama Etkinlik Günlüğü (IIS)

Uygulama Etkinlik Günlüğüne Erişin:

1. Başlat menüsünü açın, *Olay Görüntüleyicisi'ni*arayın ve **Olay Görüntüleyicisi** uygulamasını seçin.
1. **Olay Görüntüleyici'de** **Windows Günlükleri** düğümlerini açın.
1. Uygulama Olay Günlüğü'nü açmak için **Uygulama'yı** seçin.
1. Başarısız uygulamayla ilişkili hataları arayın. Hatalar *Kaynak* sütunda *IIS AspNetCore Modülü* veya *IIS Express AspNetCore Modülü* değerine sahiptir.

### <a name="run-the-app-at-a-command-prompt"></a>Uygulamayı komut istemiyle çalıştırma

Birçok başlangıç hatası, Uygulama Olay Günlüğü'nde yararlı bilgiler üretmez. Bazı hataların nedenini, uygulamayı barındırma sistemindeki bir komut isteminde çalıştırarak bulabilirsiniz.

#### <a name="framework-dependent-deployment"></a>Çerçeveye bağımlı dağıtım

Uygulama [çerçeveye bağımlı](/dotnet/core/deploying/#framework-dependent-deployments-fdd)bir dağıtım ise:

1. Komut isteminde dağıtım klasörüne gidin ve uygulamanın montajını *dotnet.exe*ile çalıştırarak uygulamayı çalıştırın. Aşağıdaki komutta, uygulamanın derlemesinin adını assembly_name \<> `dotnet .\<assembly_name>.dll`yerine değiştirin: .
1. Uygulamadan gelen konsol çıkışı, herhangi bir hata gösterirken, konsol penceresine yazılır.
1. Uygulama için istekte bulunurken hatalar oluşursa, Kerkenez'in dinlediği ana bilgisayara ve bağlantı noktasına bir istekte bulunun. Varsayılan ana bilgisayar ve gönderiyi `http://localhost:5000/`kullanarak, '' için bir istekte bulunun. Uygulama Kestrel bitiş noktası adresinde normal yanıt veriyorsa, sorun daha çok barındırma yapılandırması ile ilgilidir ve uygulama içinde daha az olasıdır.

#### <a name="self-contained-deployment"></a>Bağımsız dağıtım

Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:

1. Komut isteminde dağıtım klasörüne gidin ve uygulamanın yürütülebilir uygulamasını çalıştırın. Aşağıdaki komutta, uygulamanın derlemesinin adını assembly_name \<> `<assembly_name>.exe`yerine değiştirin: .
1. Uygulamadan gelen konsol çıkışı, herhangi bir hata gösterirken, konsol penceresine yazılır.
1. Uygulama için istekte bulunurken hatalar oluşursa, Kerkenez'in dinlediği ana bilgisayara ve bağlantı noktasına bir istekte bulunun. Varsayılan ana bilgisayar ve gönderiyi `http://localhost:5000/`kullanarak, '' için bir istekte bulunun. Uygulama Kestrel bitiş noktası adresinde normal yanıt veriyorsa, sorun daha çok barındırma yapılandırması ile ilgilidir ve uygulama içinde daha az olasıdır.

### <a name="aspnet-core-module-stdout-log-iis"></a>ASP.NET Çekirdek Modülü stdout log (IIS)

Stdout günlüklerini etkinleştirmek ve görüntülemek için:

1. Barındırma sisteminde sitenin dağıtım klasörüne gidin.
1. *Günlükler* klasörü yoksa, klasörü oluşturun. MSBuild'in dağıtımdaki *günlükler* klasörünü otomatik olarak oluşturmasına nasıl etkinleştirilen talimatlar için [Dizin yapısı](xref:host-and-deploy/directory-structure) konusuna bakın.
1. *Web.config* dosyasını edin. **StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** yolunu *günlükler* klasörüne (örneğin) `.\logs\stdout`işaret etmek için değiştirin. `stdout`yoldaki günlük dosya adı önekidir. Günlük oluşturulduğunda bir zaman damgası, işlem kimliği ve dosya uzantısı otomatik olarak eklenir. Dosya `stdout` adı öneki olarak kullanarak, tipik bir günlük dosyası *stdout_20180205184032_5412.log*olarak adlandırılır.
1. Uygulama havuzunuzun kimliğinin *günlükler* klasörüne yazma izinleri olduğundan emin olun.
1. Güncelleştirilmiş *web.config* dosyasını kaydedin.
1. Uygulamaya bir istekte bulunun.
1. *Günlükler* klasörüne gidin. En son stdout günlüğünü bulun ve açın.
1. Hatalar için günlüğü inceleyin.

Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı edin:

1. *Web.config* dosyasını edin.
1. Set **stdoutLogEnabled** için `false`.
1. Dosyayı kaydedin.

Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir. Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.
>
> ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın. Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.

### <a name="aspnet-core-module-debug-log-iis"></a>ASP.NET Çekirdek Modülü hata ayıklama günlüğü (IIS)

Temel Modül hata ayıklama günlüğünü ASP.NET etkinleştirmek için uygulamanın *web.config* dosyasına aşağıdaki işleyici ayarlarını ekleyin:

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

Günlük için belirtilen yolun var olduğunu ve uygulama havuzunun kimliğinin konuma yazma izinleri olduğunu doğrulayın.

Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs>.

### <a name="enable-the-developer-exception-page"></a>Geliştirici Özel Durum Sayfasını Etkinleştirme

Ortamı `ASPNETCORE_ENVIRONMENT` geliştirme ortamında çalıştırmak için [web.config'e ortam değişkeni eklenebilir.](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) Ortam, ana bilgisayar `UseEnvironment` oluşturucutarafından uygulama başlatmada geçersiz kılınmadığı sürece, ortam değişkenini ayarlamak, uygulama çalıştırıldığında Geliştirici Özel Durum [Sayfasının](xref:fundamentals/error-handling) görünmesini sağlar.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

Ortam değişkenini `ASPNETCORE_ENVIRONMENT` ayarlamak yalnızca Internet'e maruz olmayan hazırlama ve sınama sunucularında kullanılması önerilir. Sorun giderme den sonra *web.config* dosyasından ortam değişkenini kaldırın. *web.config'de*ortam değişkenlerini ayarlama hakkında bilgi için [bkz.](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)

### <a name="obtain-data-from-an-app"></a>Bir uygulamadan veri alma

Bir uygulama isteklere yanıt verebiliyorsa, terminal sıralı ara yazılımları kullanarak uygulamadan istek, bağlantı ve ek veri alın. Daha fazla bilgi ve <xref:test/troubleshoot#obtain-data-from-an-app>örnek kod için bkz.

### <a name="slow-or-hanging-app-iis"></a>Yavaş veya asılı uygulama (IIS)

*Kilitlenme dökümü,* sistemin belleğindebir anlık görüntüdür ve bir uygulamanın çökmesinin, başlangıç hatasının veya yavaş uygulamanın nedenini belirlemeye yardımcı olabilir.

#### <a name="app-crashes-or-encounters-an-exception"></a>Uygulama çöküyor veya bir özel durumla karşılaşıyor

Windows Hata Raporlama [(WER)](/windows/desktop/wer/windows-error-reporting)bir dökümü edinin ve analiz:

1. Kilitlenme döküm dosyalarını '' adresinde `c:\dumps`tutmak için bir klasör oluşturun Uygulama havuzunun klasöre yazma erişimi olmalıdır.
1. [EnableDumps PowerShell komut dosyasını](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)çalıştırın:
   * Uygulama [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için komut dosyası çalıştırın:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için komut dosyası çalıştırın:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Uygulamayı çökmenin oluşmasına neden olan koşullar altında çalıştırın.
1. Çökme oluştuktan sonra, [DisableDumps PowerShell komut dosyası](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)çalıştırın:
   * Uygulama [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için komut dosyası çalıştırın:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için komut dosyası çalıştırın:

     ```console
     .\DisableDumps dotnet.exe
     ```

Bir uygulama çöktükten ve döküm toplama tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir. PowerShell komut dosyası, WER'i uygulama başına en fazla beş çöplük toplamak üzere yapılandırır.

> [!WARNING]
> Kilitlenme dökümleri büyük miktarda disk alanı kapsayabilir (her biri birkaç gigabayta kadar).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Uygulama askıda kalır, başlangıç sırasında başarısız olur veya normal çalışır

Bir uygulama *askıda kaldığında* (yanıt vermeyi durdurur ancak kilitlenmez), başlangıç sırasında başarısız olduğunda veya normal olarak çalıştığında, [bkz.](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)

#### <a name="analyze-the-dump"></a>Dökümü analiz edin

Bir dökümü çeşitli yaklaşımlar kullanılarak analiz edilebilir. Daha fazla bilgi için [bkz.](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)

## <a name="clear-package-caches"></a>Paket önbelleklerini temizle

İşleyen bir uygulama, geliştirme makinesindeki .NET Core SDK'yı yükselttikten veya uygulama içindeki paket sürümlerini değiştirdikten hemen sonra başarısız olabilir. Bazı durumlarda, tutarsız paketler büyük yükseltmeler gerçekleştirirken bir uygulamayı bozabilir. Bu sorunların çoğu aşağıdaki yönergeleri izleyerek giderilebilir:

1. *Bin* ve *obj* klasörlerini silin.
1. [Noktanet nuget yerel tüm](/dotnet/core/tools/dotnet-nuget-locals) çalıştırarak paket önbellekleri temizleyin - bir komut kabuğundan temizleyin.

   Paket önbellekleri takas da [nuget.exe](https://www.nuget.org/downloads) aracı ile gerçekleştirilebilir `nuget locals all -clear`ve komutuyguluyor. *nuget.exe,* Windows masaüstü işletim sistemiyle birlikte yüklenmez ve [NuGet web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.

1. Projeyi geri yükleyin ve yeniden oluşturun.
1. Uygulamayı yeniden dağıtmadan önce sunucudaki dağıtım klasöründeki tüm dosyaları silin.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Azure belgeleri

* [ASP.NET Core için Application Insights](/azure/application-insights/app-insights-asp-net-core)
* [Visual Studio'yu kullanarak Azure Uygulama Hizmeti'ndeki bir web uygulamasını sorun giderme bölümünün uzaktan hata ayıklama web uygulamaları bölümü](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Azure Uygulama Hizmeti tanılama genel bakış](/azure/app-service/app-service-diagnostics)
* [Nasıl Yapılır: Azure App Service’te Uygulamaları İzleme](/azure/app-service/web-sites-monitor)
* [Visual Studio'yu kullanarak Azure Uygulama Hizmeti'ndeki bir web uygulamasını sorun giderme](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Azure web uygulamalarınızda HTTP hatalarını "502 kötü ağ geçidi" ve "503 hizmeti kullanılamıyor" hatalarını giderme](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Azure App Service web uygulamasında yavaş performans sorunlarını giderme](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Azure'daki Web Apps için Uygulama performansı SSS'leri](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Azure Web App sandbox (App Service çalışma zamanı yürütme sınırlamaları)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Azure Cuma: Azure Uygulama Hizmeti Tanılama ve Sorun Giderme Deneyimi (12 dakikalık video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Visual Studio belgeleri

* [Visual Studio 2017'de Azure'da IIS'de Uzaktan Hata Ayıklama ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)
* [Visual Studio 2017'de Uzaktan IIS Bilgisayarında Uzaktan Hata Ayıklama ASP.NET Core](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Visual Studio kullanarak hata ayıklamayı öğrenin](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Visual Studio Code dokümantasyonu

* [Visual Studio Code ile Hata Ayıklama](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Bu makalede, bir uygulama Azure Uygulama Hizmeti veya IIS'ye dağıtıldığında hataların nasıl tanılanacağına ilişkin yaygın uygulama başlangıç hataları ve yönergeler hakkında bilgiler verilmektedir:

[Uygulama başlatma hataları](#app-startup-errors)  
Ortak başlangıç HTTP durum kodu senaryolarını açıklar.

[Azure Uygulama Hizmetinde Sorun Giderme](#troubleshoot-on-azure-app-service)  
Azure Uygulama Hizmeti'ne dağıtılan uygulamalar için sorun giderme önerileri sağlar.

[IIS üzerinde sorun giderme](#troubleshoot-on-iis)  
IIS'ye dağıtılan veya IIS Express'te yerel olarak çalışan uygulamalar için sorun giderme önerileri sağlar. Kılavuz, hem Windows Server hem de Windows masaüstü dağıtımları için geçerlidir.

[Paket önbelleklerini temizle](#clear-package-caches)  
Büyük yükseltmeler yaparken veya paket sürümlerini değiştirirken tutarsız paketler bir uygulamayı kırdığında ne yapması gerektiğini açıklar.

[Ek kaynaklar](#additional-resources)  
Ek sorun giderme konularını listeler.

## <a name="app-startup-errors"></a>Uygulama başlatma hataları

Visual Studio'da, ASP.NET Core projesi hata ayıklama sırasında [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) barındırma için varsayılandır. Yerel hata ayıklama zaman ortaya çıkan bir *502.5 İşlem Hatası* bu konuda ki tavsiye kullanılarak teşhis edilebilir.

### <a name="40314-forbidden"></a>403.14 Yasak

Uygulama başlatılmaz. Aşağıdaki hata günlüğe kaydedilir:

```
The Web server is configured to not list the contents of this directory.
```

Hata genellikle aşağıdaki senaryolardan herhangi birini içeren barındırma sisteminde ki bozuk dağıtımdan kaynaklanır:

* Uygulama barındırma sisteminde yanlış klasöre dağıtılır.
* Dağıtım işlemi, uygulamanın tüm dosya ve klasörlerini barındırma sistemindeki dağıtım klasörüne taşıyamadı.
* *web.config* dosyası dağıtımda eksik veya *web.config* dosyası içeriği yanlış biçimlendirilmiştir.

Aşağıdaki adımları uygulayın:

1. Barındırma sistemindeki dağıtım klasöründeki tüm dosya ve klasörleri silin.
1. Visual Studio, PowerShell veya manuel dağıtım gibi normal dağıtım yönteminizi kullanarak uygulamanın *yayımlama* klasörünün içeriğini barındırma sistemine yeniden dağıtın:
   * *web.config* dosyasının dağıtımda mevcut olduğunu ve içeriğinin doğru olduğunu doğrulayın.
   * Azure Uygulama Hizmeti'nde barındırma yaparken, uygulamanın `D:\home\site\wwwroot` klasöre dağıtıldığından onaylayın.
   * Uygulama IIS tarafından barındırıldığında, uygulamanın **IIS Yöneticisi'nin**Temel **Ayarları'nda**gösterilen IIS **Fiziksel yoluna** dağıtıldığından onaylayın.
1. Barındırma sistemindeki dağıtımı projenin *yayımlama* klasörünün içeriğiyle karşılaştırarak uygulamanın tüm dosya ve klasörlerinin dağıtılmış olduğunu doğrulayın.

Yayınlanan bir ASP.NET Core uygulamasının düzeni hakkında <xref:host-and-deploy/directory-structure>daha fazla bilgi için bkz. *web.config* dosyasında daha fazla <xref:host-and-deploy/aspnet-core-module#configuration-with-webconfig>bilgi için bkz.

### <a name="500-internal-server-error"></a>500 İç Sunucu Hatası

Uygulama başlar, ancak bir hata sunucunun isteği yerine getirmesini engeller.

Bu hata, başlangıç sırasında veya yanıt oluştururken uygulamanın kodu içinde oluşur. Yanıt hiçbir içerik içermeyebilir veya yanıt tarayıcıda *500 Dahili Sunucu Hatası* olarak görünebilir. Uygulama Olay Günlüğü genellikle uygulamanın normal olarak başladığını belirtir. Sunucunun bakış açısından, bu doğru. Uygulama başladı, ancak geçerli bir yanıt oluşturamıyor. Uygulamayı sunucudaki bir komut isteminde çalıştırın veya sorunu gidermek için ASP.NET Çekirdek Modülü stdout günlüğünü etkinleştirin.

### <a name="5025-process-failure"></a>502.5 İşlem Hatası

Alt işlem başarısız olur. Uygulama başlatılmıyor.

[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) alt işlemi başlatmaya çalışır, ancak başlatılmaz. İşlem başlatma hatasının nedeni genellikle Uygulama Olay Günlüğü'ndeki girişlerden ve ASP.NET Çekirdek Modülü stdout günlüğünden belirlenebilir.

Yaygın bir hata koşulu, uygulamanın ASP.NET Core paylaşılan çerçevesinin mevcut olmayan bir sürümünü hedeflemesi nedeniyle yanlış yapılandırılmış olmasıdır. hedef makineye ASP.NET Core paylaşılan çerçevesinin hangi sürümlerinin yüklü olduğunu denetleyin. *Paylaşılan çerçeve,* makineye yüklenen *.dll* ve meta package gibi `Microsoft.AspNetCore.App`bir meta paketle başvurulan derlemeler kümesidir . Metapackage başvurusu en az gerekli sürümü belirtebilir. Daha fazla bilgi için [paylaşılan çerçeveye](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)bakın.

*502.5 İşlem Hatası* hatası sayfası, bir barındırma veya uygulama yanlış yapılandırması alt işlemin başarısız lığa neden olduğunda döndürülür:

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>Uygulama başlatılamamış (ErrorCode '0x800700c1')

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

Uygulamanın derlemesi *(.dll)* yüklenemediği için uygulama başlatılamadı.

Bu hata, yayınlanan uygulama ile w3wp/iisexpress işlemi arasında bir bitness uyuşmazlığı olduğunda oluşur.

Uygulama havuzunun 32 bit ayarı doğru olduğunu doğrulayın:

1. IIS Manager'ın **Uygulama Havuzları'ndaki**uygulama havuzunu seçin.
1. **Eylemler** panelinde **Uygulama Havuzu** altında Gelişmiş **Ayarlar'ı** seçin.
1. **32 Bit Uygulamaları Etkinleştir'i**Ayarlayın :
   * 32 bit (x86) bir uygulama dağıtıyorsanız, `True`değeri .
   * 64 bit (x64) bir uygulama dağıtıyorsanız, `False`değeri .

Proje dosyasındaki BIR `<Platform>` MSBuild özelliği ile uygulamanın yayınlanan bitliği arasında bir çakışma olmadığını doğrulayın.

### <a name="connection-reset"></a>Bağlantı sıfırlama

Üstbilgi gönderildikten sonra bir hata oluşursa, bir hata oluştuğunda sunucunun **500 Dahili Sunucu Hatası** göndermesi için çok geç olur. Bu genellikle bir yanıt için karmaşık nesnelerin serileştirme sırasında bir hata oluşur oluşur. Bu hata türü istemcide *bağlantı sıfırlama* hatası olarak görünür. [Uygulama günlüğe kaydetme,](xref:fundamentals/logging/index) bu tür hataları gidermede yardımcı olabilir.

### <a name="default-startup-limits"></a>Varsayılan başlangıç sınırları

[ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) 120 saniyelik varsayılan *başlangıçTimeLimit* ile yapılandırılır. Varsayılan değerde bırakıldığında, bir uygulamanın bir işlem hatası günlüğe kaydetmesi iki dakika kadar sürebilir. Modülü yapılandırma hakkında daha fazla bilgi için [aspNetCore öğesinin Öznitelikleri'ne](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)bakın.

## <a name="troubleshoot-on-azure-app-service"></a>Azure Uygulama Hizmetinde Sorun Giderme

[!INCLUDE [Azure App Service Preview Notice](~/includes/azure-apps-preview-notice.md)]

### <a name="application-event-log-azure-app-service"></a>Uygulama Etkinlik Günlüğü (Azure Uygulama Hizmeti)

Uygulama Olay Günlüğü'ne erişmek için Azure portalındaki **Tanılama ve sorunları çözme** sorununu kullanın:

1. Azure portalında Uygulamayı Uygulama **Hizmetleri'nde**açın.
1. **Sorunları tanılama ve çözme** seçeneğini belirleyin.
1. **Tanılama Araçları** başlığını seçin.
1. **Destek Araçları** **altında, Uygulama Etkinlikleri** düğmesini seçin.
1. **Kaynak** sütundaki *IIS AspNetCoreModule* veya *IIS AspNetCoreModule V2* girişi tarafından sağlanan en son hatayı inceleyin.

**Tanılama ve sorunları çözmenin** alternatifi, Uygulama Olay Günlüğü dosyasını doğrudan [Kudu](https://github.com/projectkudu/kudu/wiki)kullanarak incelemektir:

1. **Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın. **Git&rarr; ** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.
1. **LogFiles** klasörünü açın.
1. *eventlog.xml* dosyasının yanındaki kalem simgesini seçin.
1. Günlüğü inceleyin. En son olayları görmek için günlüğün altına gidin.

### <a name="run-the-app-in-the-kudu-console"></a>Uygulamayı Kudu konsolunda çalıştırın

Birçok başlangıç hatası, Uygulama Olay Günlüğü'nde yararlı bilgiler üretmez. Hatayı bulmak için uygulamayı [Kudu](https://github.com/projectkudu/kudu/wiki) Uzaktan Yürütme Konsolu'nda çalıştırabilirsiniz:

1. **Geliştirme Araçları** alanında **Gelişmiş Araçlar'ı** açın. **Git&rarr; ** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.

#### <a name="test-a-32-bit-x86-app"></a>32 bit (x86) uygulamayı test edin

**Geçerli sürüm**

1. `cd d:\home\site\wwwroot`
1. Uygulamayı çalıştırın:
   * Uygulama [çerçeveye bağımlı](/dotnet/core/deploying/#framework-dependent-deployments-fdd)bir dağıtım ise:

     ```dotnetcli
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:

     ```console
     {ASSEMBLY NAME}.exe
     ```

Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.

**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**

*Core {VERSION} (x86) Runtime site uzantısı ASP.NET yüklemeyi gerektirir.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`(`{X.Y}` çalışma zamanı sürümüdür)
1. Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.

#### <a name="test-a-64-bit-x64-app"></a>64 bit (x64) uygulamayı test edin

**Geçerli sürüm**

* Uygulama 64 bit (x64) [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)ise:
  1. `cd D:\Program Files\dotnet`
  1. Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:
  1. `cd D:\home\site\wwwroot`
  1. Uygulamayı çalıştırın: `{ASSEMBLY NAME}.exe`

Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.

**Önizleme sürümünde çalışan çerçeveye bağımlı dağıtım**

*Core {VERSION} (x64) Runtime site uzantısı ASP.NET yüklemeyi gerektirir.*

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`(`{X.Y}` çalışma zamanı sürümüdür)
1. Uygulamayı çalıştırın: `dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

Uygulamadan alınan ve hataları gösteren konsol çıktısı, tüm Kudu konsoluna gönderilir.

### <a name="aspnet-core-module-stdout-log-azure-app-service"></a>ASP.NET Çekirdek Modül stdout günlüğü (Azure Uygulama Hizmeti)

ASP.NET Çekirdek Modülü stdout günlüğü genellikle Uygulama Olay Günlüğü'nde bulunmayan yararlı hata iletileri kaydeder. Stdout günlüklerini etkinleştirmek ve görüntülemek için:

1. Azure portalında **Tanıla ve sorunları çöz'** e gidin.
1. **PROBLEM KATEGORİsİ SEÇ'in** **altında, Web Uygulaması Aşağı** düğmesini seçin.
1. **Önerilen Çözümler** > Altında **Stdout Log Redirection etkinleştirin**, **Web.Config'i yeniden etkinleştirmek için Kudu Konsolu'nu açın**düğmesini seçin.
1. Kudu **Tanı Konsolu,** yol **sitesi** > **wwwroot**klasörleri açın. Listenin altındaki *web.config* dosyasını ortaya çıkarmak için aşağı kaydırın.
1. *web.config* dosyasının yanındaki kalem simgesine tıklayın.
1. **StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** `\\?\%home%\LogFiles\stdout`yolunu şu şekilde değiştirin: .
1. Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet'i** seçin.
1. Uygulamaya bir istekte bulunun.
1. Azure portalına dönün. **GELİşTİrME ARAÇLARI** alanında **Gelişmiş Araçlar** bıçağını seçin. **Git&rarr; ** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.
1. Günlük **Dosyaları** klasörünü seçin.
1. **Değiştirilen** sütunu inceleyin ve en son değişiklik tarihiyle birlikte stdout günlüğünü deletmek için kalem simgesini seçin.
1. Günlük dosyası açıldığında, hata görüntülenir.

Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı edin:

1. Kudu **Tanı Konsolu,** *web.config* dosyasını ortaya çıkarmak için yol **sitesi** > **wwwroot** dönün. Kalem simgesini seçerek **web.config** dosyasını yeniden açın.
1. Set **stdoutLogEnabled** için `false`.
1. Dosyayı kaydetmek için **Kaydet'i** seçin.

Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir. Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur. Yalnızca uygulama başlatma sorunlarını gidermek için stdout günlük kullanın.
>
> Başlangıç tarihinden sonra bir ASP.NET Core uygulamasında genel oturum açmak için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın. Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.

### <a name="slow-or-hanging-app-azure-app-service"></a>Yavaş veya asma uygulaması (Azure Uygulama Hizmeti)

Bir uygulama yavaş yanıt verdiğinde veya bir isteğe bağlı kaldığında aşağıdaki makalelere bakın:

* [Azure App Service web uygulamasında yavaş performans sorunlarını giderme](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Azure Web Uygulamasında Aralıklı Özel Durum sorunları veya performans sorunları için Döküm'ü yakalamak için Kilitlenme Tanılayıcı Site Uzantısı'nı kullanma](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

### <a name="monitoring-blades"></a>İzleme bıçakları

İzleme bıçakları, konunun daha önce açıklanan yöntemlerine alternatif bir sorun giderme deneyimi sağlar. Bu bıçaklar 500 seri hataları teşhis etmek için kullanılabilir.

ASP.NET Çekirdek Uzantılarının yüklü olduğunu doğrulayın. Uzantılar yüklenmiyorsa, bunları el ile yükleyin:

1. **GELİşİm ARAÇLARI** bıçak bölümünde **Uzantılar** bıçağını seçin.
1. **ASP.NET Çekirdek Uzantıları** listede görünmelidir.
1. Uzantılar yüklenmiyorsa **Ekle** düğmesini seçin.
1. Listeden **ASP.NET Çekirdek Uzantıları'nı** seçin.
1. Yasal koşulları kabul etmek için **Tamam'ı** seçin.
1. **Ekle uzantı** bıçağında **Tamam'ı** seçin.
1. Bilgilendirimiolan bir açılır ileti, uzantıların başarıyla ne zaman yüklenir olduğunu gösterir.

Stdout günlüğe kaydetme etkin değilse, aşağıdaki adımları izleyin:

1. Azure portalında, **GELIŞTIRME ARAÇLARı** alanındaki **Gelişmiş Araçlar** bıçak larını seçin. **Git&rarr; ** düğmesini seçin. Kudu konsolu yeni bir tarayıcı sekmesinde veya pencerede açılır.
1. Sayfanın üst kısmındaki gezinti çubuğunu kullanarak **Hata Ayıklama** konsolu'nu açın ve **CMD'yi**seçin.
1. Klasörleri yol **sitesi** > **wwwroot'a** açın ve listenin altındaki *web.config* dosyasını ortaya çıkarmak için aşağı kaydırın.
1. *web.config* dosyasının yanındaki kalem simgesine tıklayın.
1. **StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** `\\?\%home%\LogFiles\stdout`yolunu şu şekilde değiştirin: .
1. Güncelleştirilmiş *web.config* dosyasını kaydetmek için **Kaydet'i** seçin.

Tanısal günlüğü etkinleştirmeye devam edin:

1. Azure portalında **Tanılama günlükleri** bıçağını seçin.
1. **Uygulama Günlüğe Kaydetme (Filesystem)** ve **Ayrıntılı hata iletileri**için **Açık** anahtarını seçin. Bıçağın üst kısmındaki **Kaydet** düğmesini seçin.
1. Başarısız İstek Olay Arabelleği (FREB) günlüğe kaydetme olarak da bilinen başarısız istek izlemesini eklemek için, **Başarısız istek izleme**için **Açık** anahtarını seçin.
1. Portaldaki **Teşhis günlükleri** bıçağının hemen altında listelenen **Log akış** bıçağını seçin.
1. Uygulamaya bir istekte bulunun.
1. Günlük akışı verileri içinde, hatanın nedeni belirtilir.

Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı bettiğinizden emin olun.

Başarısız istek izleme günlüklerini (FREB günlükleri) görüntülemek için:

1. Azure portalında **Tanıla ve sorunları çöz'** e gidin.
1. Kenar çubuğunun **DESTEK ARAÇLARI** alanından **Başarısız İstek İzleme Günlükleri'ni** seçin.

Azure Uygulama Hizmeti konusundaki web uygulamaları için etkinleştir meslüpleri oturum açma [bölümünün başarısız istek izlemeleri bölümüne](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces) ve [Azure'daki Web Apps Uygulamaları için Uygulama performansı SSS'lerine bakın: Daha](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing) fazla bilgi için başarısız istek izlemesini nasıl açarım?

Daha fazla bilgi için azure [Uygulama Hizmeti'ndeki web uygulamaları için tanılama günlüğe kaydetme'ye](/azure/app-service/web-sites-enable-diagnostic-log)bakın.

> [!WARNING]
> Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir. Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.
>
> ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın. Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.

## <a name="troubleshoot-on-iis"></a>IIS üzerinde sorun giderme

### <a name="application-event-log-iis"></a>Uygulama Etkinlik Günlüğü (IIS)

Uygulama Etkinlik Günlüğüne Erişin:

1. Başlat menüsünü açın, *Olay Görüntüleyicisi'ni*arayın ve **Olay Görüntüleyicisi** uygulamasını seçin.
1. **Olay Görüntüleyici'de** **Windows Günlükleri** düğümlerini açın.
1. Uygulama Olay Günlüğü'nü açmak için **Uygulama'yı** seçin.
1. Başarısız uygulamayla ilişkili hataları arayın. Hatalar *Kaynak* sütunda *IIS AspNetCore Modülü* veya *IIS Express AspNetCore Modülü* değerine sahiptir.

### <a name="run-the-app-at-a-command-prompt"></a>Uygulamayı komut istemiyle çalıştırma

Birçok başlangıç hatası, Uygulama Olay Günlüğü'nde yararlı bilgiler üretmez. Bazı hataların nedenini, uygulamayı barındırma sistemindeki bir komut isteminde çalıştırarak bulabilirsiniz.

#### <a name="framework-dependent-deployment"></a>Çerçeveye bağımlı dağıtım

Uygulama [çerçeveye bağımlı](/dotnet/core/deploying/#framework-dependent-deployments-fdd)bir dağıtım ise:

1. Komut isteminde dağıtım klasörüne gidin ve uygulamanın montajını *dotnet.exe*ile çalıştırarak uygulamayı çalıştırın. Aşağıdaki komutta, uygulamanın derlemesinin adını assembly_name \<> `dotnet .\<assembly_name>.dll`yerine değiştirin: .
1. Uygulamadan gelen konsol çıkışı, herhangi bir hata gösterirken, konsol penceresine yazılır.
1. Uygulama için istekte bulunurken hatalar oluşursa, Kerkenez'in dinlediği ana bilgisayara ve bağlantı noktasına bir istekte bulunun. Varsayılan ana bilgisayar ve gönderiyi `http://localhost:5000/`kullanarak, '' için bir istekte bulunun. Uygulama Kestrel bitiş noktası adresinde normal yanıt veriyorsa, sorun daha çok barındırma yapılandırması ile ilgilidir ve uygulama içinde daha az olasıdır.

#### <a name="self-contained-deployment"></a>Bağımsız dağıtım

Uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd)ise:

1. Komut isteminde dağıtım klasörüne gidin ve uygulamanın yürütülebilir uygulamasını çalıştırın. Aşağıdaki komutta, uygulamanın derlemesinin adını assembly_name \<> `<assembly_name>.exe`yerine değiştirin: .
1. Uygulamadan gelen konsol çıkışı, herhangi bir hata gösterirken, konsol penceresine yazılır.
1. Uygulama için istekte bulunurken hatalar oluşursa, Kerkenez'in dinlediği ana bilgisayara ve bağlantı noktasına bir istekte bulunun. Varsayılan ana bilgisayar ve gönderiyi `http://localhost:5000/`kullanarak, '' için bir istekte bulunun. Uygulama Kestrel bitiş noktası adresinde normal yanıt veriyorsa, sorun daha çok barındırma yapılandırması ile ilgilidir ve uygulama içinde daha az olasıdır.

### <a name="aspnet-core-module-stdout-log-iis"></a>ASP.NET Çekirdek Modülü stdout log (IIS)

Stdout günlüklerini etkinleştirmek ve görüntülemek için:

1. Barındırma sisteminde sitenin dağıtım klasörüne gidin.
1. *Günlükler* klasörü yoksa, klasörü oluşturun. MSBuild'in dağıtımdaki *günlükler* klasörünü otomatik olarak oluşturmasına nasıl etkinleştirilen talimatlar için [Dizin yapısı](xref:host-and-deploy/directory-structure) konusuna bakın.
1. *Web.config* dosyasını edin. **StdoutLogEnabled'ı** `true` ayarlayın ve **stdoutLogFile** yolunu *günlükler* klasörüne (örneğin) `.\logs\stdout`işaret etmek için değiştirin. `stdout`yoldaki günlük dosya adı önekidir. Günlük oluşturulduğunda bir zaman damgası, işlem kimliği ve dosya uzantısı otomatik olarak eklenir. Dosya `stdout` adı öneki olarak kullanarak, tipik bir günlük dosyası *stdout_20180205184032_5412.log*olarak adlandırılır.
1. Uygulama havuzunuzun kimliğinin *günlükler* klasörüne yazma izinleri olduğundan emin olun.
1. Güncelleştirilmiş *web.config* dosyasını kaydedin.
1. Uygulamaya bir istekte bulunun.
1. *Günlükler* klasörüne gidin. En son stdout günlüğünü bulun ve açın.
1. Hatalar için günlüğü inceleyin.

Sorun giderme tamamlandığında stdout günlüğe kaydetmeyi devre dışı edin:

1. *Web.config* dosyasını edin.
1. Set **stdoutLogEnabled** için `false`.
1. Dosyayı kaydedin.

Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>.

> [!WARNING]
> Stdout günlüğü devre dışı aksitakdirde uygulama veya sunucu hatasına neden olabilir. Günlük dosyası boyutunda veya oluşturulan günlük dosyalarının sayısında bir sınır yoktur.
>
> ASP.NET Core uygulamasında rutin günlüğe kaydetme için, günlük dosya boyutunu sınırlayan ve günlükleri döndüren bir günlük kitaplığı kullanın. Daha fazla bilgi için [üçüncü taraf günlük sağlayıcılarına](xref:fundamentals/logging/index#third-party-logging-providers)bakın.

### <a name="enable-the-developer-exception-page"></a>Geliştirici Özel Durum Sayfasını Etkinleştirme

Ortamı `ASPNETCORE_ENVIRONMENT` geliştirme ortamında çalıştırmak için [web.config'e ortam değişkeni eklenebilir.](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) Ortam, ana bilgisayar `UseEnvironment` oluşturucutarafından uygulama başlatmada geçersiz kılınmadığı sürece, ortam değişkenini ayarlamak, uygulama çalıştırıldığında Geliştirici Özel Durum [Sayfasının](xref:fundamentals/error-handling) görünmesini sağlar.

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

Ortam değişkenini `ASPNETCORE_ENVIRONMENT` ayarlamak yalnızca Internet'e maruz olmayan hazırlama ve sınama sunucularında kullanılması önerilir. Sorun giderme den sonra *web.config* dosyasından ortam değişkenini kaldırın. *web.config'de*ortam değişkenlerini ayarlama hakkında bilgi için [bkz.](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)

### <a name="obtain-data-from-an-app"></a>Bir uygulamadan veri alma

Bir uygulama isteklere yanıt verebiliyorsa, terminal sıralı ara yazılımları kullanarak uygulamadan istek, bağlantı ve ek veri alın. Daha fazla bilgi ve <xref:test/troubleshoot#obtain-data-from-an-app>örnek kod için bkz.

### <a name="slow-or-hanging-app-iis"></a>Yavaş veya asılı uygulama (IIS)

*Kilitlenme dökümü,* sistemin belleğindebir anlık görüntüdür ve bir uygulamanın çökmesinin, başlangıç hatasının veya yavaş uygulamanın nedenini belirlemeye yardımcı olabilir.

#### <a name="app-crashes-or-encounters-an-exception"></a>Uygulama çöküyor veya bir özel durumla karşılaşıyor

Windows Hata Raporlama [(WER)](/windows/desktop/wer/windows-error-reporting)bir dökümü edinin ve analiz:

1. Kilitlenme döküm dosyalarını '' adresinde `c:\dumps`tutmak için bir klasör oluşturun Uygulama havuzunun klasöre yazma erişimi olmalıdır.
1. [EnableDumps PowerShell komut dosyasını](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/EnableDumps.ps1)çalıştırın:
   * Uygulama [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için komut dosyası çalıştırın:

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için komut dosyası çalıştırın:

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. Uygulamayı çökmenin oluşmasına neden olan koşullar altında çalıştırın.
1. Çökme oluştuktan sonra, [DisableDumps PowerShell komut dosyası](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/test/troubleshoot-azure-iis/scripts/DisableDumps.ps1)çalıştırın:
   * Uygulama [işlem içi barındırma modelini](xref:host-and-deploy/iis/index#in-process-hosting-model)kullanıyorsa, *w3wp.exe*için komut dosyası çalıştırın:

     ```console
     .\DisableDumps w3wp.exe
     ```

   * Uygulama [işlem dışı barındırma modelini](xref:host-and-deploy/iis/index#out-of-process-hosting-model)kullanıyorsa, *dotnet.exe*için komut dosyası çalıştırın:

     ```console
     .\DisableDumps dotnet.exe
     ```

Bir uygulama çöktükten ve döküm toplama tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir. PowerShell komut dosyası, WER'i uygulama başına en fazla beş çöplük toplamak üzere yapılandırır.

> [!WARNING]
> Kilitlenme dökümleri büyük miktarda disk alanı kapsayabilir (her biri birkaç gigabayta kadar).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Uygulama askıda kalır, başlangıç sırasında başarısız olur veya normal çalışır

Bir uygulama *askıda kaldığında* (yanıt vermeyi durdurur ancak kilitlenmez), başlangıç sırasında başarısız olduğunda veya normal olarak çalıştığında, [bkz.](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)

#### <a name="analyze-the-dump"></a>Dökümü analiz edin

Bir dökümü çeşitli yaklaşımlar kullanılarak analiz edilebilir. Daha fazla bilgi için [bkz.](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)

## <a name="clear-package-caches"></a>Paket önbelleklerini temizle

İşleyen bir uygulama, geliştirme makinesindeki .NET Core SDK'yı yükselttikten veya uygulama içindeki paket sürümlerini değiştirdikten hemen sonra başarısız olabilir. Bazı durumlarda, tutarsız paketler büyük yükseltmeler gerçekleştirirken bir uygulamayı bozabilir. Bu sorunların çoğu aşağıdaki yönergeleri izleyerek giderilebilir:

1. *Bin* ve *obj* klasörlerini silin.
1. [Noktanet nuget yerel tüm](/dotnet/core/tools/dotnet-nuget-locals) çalıştırarak paket önbellekleri temizleyin - bir komut kabuğundan temizleyin.

   Paket önbellekleri takas da [nuget.exe](https://www.nuget.org/downloads) aracı ile gerçekleştirilebilir `nuget locals all -clear`ve komutuyguluyor. *nuget.exe,* Windows masaüstü işletim sistemiyle birlikte yüklenmez ve [NuGet web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.

1. Projeyi geri yükleyin ve yeniden oluşturun.
1. Uygulamayı yeniden dağıtmadan önce sunucudaki dağıtım klasöründeki tüm dosyaları silin.

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:test/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/aspnet-core-module>

### <a name="azure-documentation"></a>Azure belgeleri

* [ASP.NET Core için Application Insights](/azure/application-insights/app-insights-asp-net-core)
* [Visual Studio'yu kullanarak Azure Uygulama Hizmeti'ndeki bir web uygulamasını sorun giderme bölümünün uzaktan hata ayıklama web uygulamaları bölümü](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)
* [Azure Uygulama Hizmeti tanılama genel bakış](/azure/app-service/app-service-diagnostics)
* [Nasıl Yapılır: Azure App Service’te Uygulamaları İzleme](/azure/app-service/web-sites-monitor)
* [Visual Studio'yu kullanarak Azure Uygulama Hizmeti'ndeki bir web uygulamasını sorun giderme](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Azure web uygulamalarınızda HTTP hatalarını "502 kötü ağ geçidi" ve "503 hizmeti kullanılamıyor" hatalarını giderme](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [Azure App Service web uygulamasında yavaş performans sorunlarını giderme](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Azure'daki Web Apps için Uygulama performansı SSS'leri](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Azure Web App sandbox (App Service çalışma zamanı yürütme sınırlamaları)](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Azure Cuma: Azure Uygulama Hizmeti Tanılama ve Sorun Giderme Deneyimi (12 dakikalık video)](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)

### <a name="visual-studio-documentation"></a>Visual Studio belgeleri

* [Visual Studio 2017'de Azure'da IIS'de Uzaktan Hata Ayıklama ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)
* [Visual Studio 2017'de Uzaktan IIS Bilgisayarında Uzaktan Hata Ayıklama ASP.NET Core](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)
* [Visual Studio kullanarak hata ayıklamayı öğrenin](/visualstudio/debugger/getting-started-with-the-debugger)

### <a name="visual-studio-code-documentation"></a>Visual Studio Code dokümantasyonu

* [Visual Studio Code ile Hata Ayıklama](https://code.visualstudio.com/docs/editor/debugging)

::: moniker-end
