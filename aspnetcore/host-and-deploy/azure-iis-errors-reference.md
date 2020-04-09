---
title: Azure Uygulama Hizmeti ve IIS için ASP.NET Core ile ilgili sık karşılaşılan hatalar referans
author: rick-anderson
description: Azure Apps Hizmeti ve IIS'de ASP.NET Core uygulamalarını barındırırken sık karşılaşılan hatalar için sorun giderme önerileri edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/azure-iis-errors-reference
ms.openlocfilehash: 635c4cf6f12e62ca7e795b3b3b47e9445b945551
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "79511606"
---
# <a name="common-errors-reference-for-azure-app-service-and-iis-with-aspnet-core"></a>Azure Uygulama Hizmeti ve IIS için ASP.NET Core ile ilgili sık karşılaşılan hatalar referans

::: moniker range=">= aspnetcore-2.2"

Bu konu, sık karşılaşılan hataları açıklar ve Azure Apps Hizmeti ve IIS'de ASP.NET Core uygulamalarını barındırırken belirli hatalar için sorun giderme önerileri sağlar.

Genel sorun giderme <xref:test/troubleshoot-azure-iis>kılavuzu için bkz.

Aşağıdaki bilgileri toplayın:

* Tarayıcı davranışı (durum kodu ve hata iletisi)
* Uygulama Olay Günlüğü girişleri
  * Azure Uygulama &ndash; <xref:test/troubleshoot-azure-iis>Hizmeti Bkz.
  * IIS
    1. **Windows** menüsünde **Başlat'ı** seçin, *Olay Görüntüleyicisi*yazın ve **Enter**tuşuna basın.
    1. Olay **Görüntüleyicisi** açıldıktan sonra kenar çubuğundaki **Windows Günlükleri** > **Uygulamasını** genişletin.
* ASP.NET Core Modülü stdout ve hata ayıklama günlüğü girdileri
  * Azure Uygulama &ndash; <xref:test/troubleshoot-azure-iis>Hizmeti Bkz.
  * IIS, &ndash; ASP.NET Çekirdek Modülü konusunun [Log oluşturma ve yeniden yönlendirme](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) ve Gelişmiş [tanılama günlükleri](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) bölümlerindeki yönergeleri izleyin.

Hata bilgilerini aşağıdaki yaygın hatalarla karşılaştırın. Bir eşleşme bulunursa, sorun giderme tavsiyesini uygulayın.

Bu konudaki hataların listesi ayrıntılı değildir. Burada listelenmemiş bir hatayla karşılaşırsanız, hatanın nasıl çoğaltılacağına ilişkin ayrıntılı talimatlar içeren bu konunun altındaki **İçerik geri bildirimi** düğmesini kullanarak yeni bir sorun açın.

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>İşletim sistemi yükseltmesi 32 bit ASP.NET Çekirdek Modülü kaldırıldı

**Başvuru Günlüğü:** Modül DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** yüklenemedi. Veriler hatadır.

Sorun Giderme:

**C:\Windows\SysWOW64\inetsrv** dizinindeki işletim sistemi dışı dosyalar işletim sistemi yükseltmesi sırasında korunmaz. ASP.NET Çekirdek Modülü işletim sistemi yükseltmesi öncesinde yüklenirse ve işletim sistemi yükseltmesi sonrasında herhangi bir uygulama havuzu 32 bit modunda çalıştırılırsa, bu sorunla karşılaşılır. İşletim sistemi yükseltmesi yapıldıktan sonra ASP.NET Çekirdek Modül'ünonarımını tamamla. Bkz. [.NET Çekirdek Barındırma paketini yükleyin.](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) Yükleyici çalıştırıldığında **Onarım'ı** seçin.

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a>Eksik site uzantısı, 32-bit (x86) ve 64-bit (x64) site uzantıları yüklü veya yanlış işlem bitlik kümesi

*Azure Uygulama Hizmetleri tarafından barındırılan uygulamalar için geçerlidir.*

* **Tarayıcı:** HTTP Hata 500.0 - ANCM İşlem İşleyicisi Yük Arızası

* **Başvuru Günlüğü:** İşleme isteği işleyicisini bulmak için hostfxr çağırmak herhangi bir yerel bağımlılık bulmadan başarısız oldu. İşlenme devam isteği işleyicisi bulamadım. Hostfxr çağırarak yakalanan çıktı: Uyumlu bir çerçeve sürümü bulmak mümkün değildi. Belirtilen çerçeve 'Microsoft.AspNetCore.App', sürüm '{VERSION}-preview-\*' bulunamadı. Uygulama başlatmak için başarısız oldu '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.

* **ASP.NET Çekirdek Modül stdout Günlüğü:** Uyumlu bir çerçeve sürümü bulmak mümkün değildi. Belirtilen çerçeve 'Microsoft.AspNetCore.App', sürüm '{VERSION}-preview-\*' bulunamadı.

* **ASP.NET Çekirdek Modülü Hata Ayıklama Günlüğü:** İşleme isteği işleyicisini bulmak için hostfxr çağırmak herhangi bir yerel bağımlılık bulmadan başarısız oldu. Bu büyük olasılıkla uygulama yanlış yapılandırılmış olduğu anlamına gelir, uygulama tarafından hedeflenen ve makineye yüklü Microsoft.NetCore.App ve Microsoft.AspNetCore.App sürümlerini kontrol edin. Başarısız HRESULT döndü: 0x8000ffff. İşlenme devam isteği işleyicisi bulamadım. Uyumlu bir çerçeve sürümü bulmak mümkün değildi. Belirtilen çerçeve 'Microsoft.AspNetCore.App', sürüm '{VERSION}-preview-\*' bulunamadı.

Sorun Giderme:

* Uygulamayı önizleme çalışma zamanında çalıştırıyorsanız, uygulamanın bitliği yle ve uygulamanın çalışma zamanı sürümüyle eşleşen 32 bit (x86) **veya** 64 bit (x64) site uzantısını yükleyin. **Uzantının her iki uzantısını veya birden çok çalışma zamanı sürümlerini yüklemeyin.**

  * ASP.NET Core {RUNTIME VERSION} (x86) Çalışma Süresi
  * ASP.NET Çekirdek {RUNTIME VERSION} (x64) Çalışma Süresi

  Uygulamayı yeniden başlatın. Uygulamanın yeniden başlatılması için birkaç saniye bekleyin.

* Uygulamayı önizleme çalışma zamanında çalıştırıyorsanız ve hem 32 bit (x86) hem de 64 bit (x64) [site uzantıları](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) yüklüyse, uygulamanın bitliğiyle eşleşmeyen site uzantısını kaldırın. Site uzantısını kaldırdıktan sonra uygulamayı yeniden başlatın. Uygulamanın yeniden başlatılması için birkaç saniye bekleyin.

* Uygulamayı önizleme çalışma zamanında çalıştırmak ve site uzantısıbitness uygulamanınbitliğiyle eşleşiyorsa, önizleme sitesi uzantısının *çalışma zamanı sürümünün* uygulamanın çalışma zamanı sürümüyle eşleştiğini doğrulayın.

* **Uygulama Ayarları'ndaki** **uygulama** platformunun uygulamanın bitliğiyle eşleştiğini doğrulayın.

Daha fazla bilgi için bkz. <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>Bir x86 uygulaması dağıtıldı, ancak uygulama havuzu 32 bit uygulamalar için etkinleştirildi

* **Tarayıcı:** HTTP Hatası 500.30 - ANCM İşlem Içi Başlatma Hatası

* **Başvuru Günlüğü:** Uygulama '/LM/W3SVC/5/ROOT' fiziksel kök '{PATH}' hit beklenmeyen yönetilen özel durum, özel durum kodu = '0xe0434352'. Daha fazla bilgi için lütfen stderr günlüklerini kontrol edin. Uygulama '/LM/W3SVC/5/ROOT' fiziksel kök '{PATH}' clr ve yönetilen uygulama yüklemek için başarısız oldu. CLR işçi iş parçacığı erken çıktı

* **ASP.NET Çekirdek Modül stdout Günlüğü:** Günlük dosyası oluşturulur, ancak boştur.

* **ASP.NET Çekirdek Modülü Hata Ayıklama Günlüğü:** Başarısız HRESULT döndü: 0x8007023e

Bu senaryo, kendi kendine yeten bir uygulama yayımlarken SDK tarafından sıkışıp kalır. RID platform hedefiyle eşleşmiyorsa SDK bir hata `win10-x64` üretir `<PlatformTarget>x86</PlatformTarget>` (örneğin, proje dosyasındaki RID).

Sorun Giderme:

X86 framework-bağımlı dağıtım`<PlatformTarget>x86</PlatformTarget>`için ( ), 32 bit uygulamalar için IIS uygulama havuzunu etkinleştirin. IIS Manager'da uygulama havuzunun **Gelişmiş Ayarlarını** açın ve **32 Bit Uygulamaları** **True'ya**etkinleştir'i ayarlayın.

## <a name="platform-conflicts-with-rid"></a>RID ile platform çakışmaları

* **Tarayıcı:** HTTP Hatası 502.5 - İşlem Hatası

* **Başvuru Günlüğü:** Fiziksel kökü 'C:\{PATH} olan 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' uygulaması '"C:\{PATH}{ASSEMBLY}\' komut satırıile işleme başlayamadı. {exe|dll}" ', ErrorCode = '0x80004005 : ff.

* **ASP.NET Çekirdek Modül stdout Günlüğü:** İşlenmemiş Özel Durum: System.BadImageFormatException: Dosya veya derleme '{ASSEMBLY}.dll' yükleyemedi. Bir programı yanlış biçime yükleme girişiminde bulunuldu.

Sorun Giderme:

* Uygulamanın Kestrel'de yerel olarak çalıştığını doğrulayın. İşlem hatası, uygulama içindeki bir sorunun sonucu olabilir. Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.

* Bu özel durum, bir uygulamayı yükseltirken ve daha yeni derlemeler dağıtırken Azure Apps dağıtımı için oluşursa, önceki dağıtımdaki tüm dosyaları el ile silin. Kalan uyumsuz derlemeler, yükseltilmiş `System.BadImageFormatException` bir uygulamayı dağıtırken bir özel durumla sonuçlanabilir.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>URI uç noktası yanlış veya durdurulmuş web sitesi

* **Tarayıcı:** ERR_CONNECTION_REFUSED **--VEYA--** Bağlanamıyor

* **Başvuru Günlüğü:** Giriş yok

* **ASP.NET Çekirdek Modül stdout Günlüğü:** Günlük dosyası oluşturulmamadı.

* **ASP.NET Çekirdek Modülü Hata Ayıklama Günlüğü:** Günlük dosyası oluşturulmamadı.

Sorun Giderme:

* Uygulama için doğru URI uç noktasının kullanımda olduğunu onaylayın. Ciltleri kontrol edin.

* IIS web sitesinin *Durduruldu* durumunda olmadığını doğrulayın.

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>CoreWebEngine veya W3SVC sunucu devre dışı özellikleri

**İşletim Sistemi Özel Durumu:** iIS 7.0 CoreWebEngine ve W3SVC özellikleri ASP.NET Core Modül kullanmak için yüklü olmalıdır.

Sorun Giderme:

Uygun rolün ve özelliklerin etkin olduğunu doğrulayın. Bkz. [IIS Yapılandırması.](xref:host-and-deploy/iis/index#iis-configuration)

## <a name="incorrect-website-physical-path-or-app-missing"></a>Yanlış web sitesi fiziksel yol veya uygulama eksik

* **Tarayıcı:** 403 Yasak - Erişim reddedildi **--VEYA--** 403.14 Yasak - Web sunucusu bu dizinin içeriğini listelemeyecek şekilde yapılandırılır.

* **Başvuru Günlüğü:** Giriş yok

* **ASP.NET Çekirdek Modül stdout Günlüğü:** Günlük dosyası oluşturulmamadı.

* **ASP.NET Çekirdek Modülü Hata Ayıklama Günlüğü:** Günlük dosyası oluşturulmamadı.

Sorun Giderme:

IIS web sitesi **Temel Ayarları** ve fiziksel uygulama klasörünü kontrol edin. Uygulamanın IIS web sitesi **Fiziksel yoldaki**klasörde olduğunu doğrulayın.

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>Yanlış rol, ASP.NET Çekirdek Modülü yüklü değil veya yanlış izinler

* **Tarayıcı:** 500.19 Dahili Sunucu Hatası - Sayfanın ilgili yapılandırma verileri geçersiz olduğundan istenen sayfaya erişilemiyor. **--VEYA--** Bu sayfa görüntülenemez

* **Başvuru Günlüğü:** Giriş yok

* **ASP.NET Çekirdek Modül stdout Günlüğü:** Günlük dosyası oluşturulmamadı.

* **ASP.NET Çekirdek Modülü Hata Ayıklama Günlüğü:** Günlük dosyası oluşturulmamadı.

Sorun Giderme:

* Uygun rolün etkin olduğunu doğrulayın. Bkz. [IIS Yapılandırması.](xref:host-and-deploy/iis/index#iis-configuration)

* **Açık Programlar & Özellikler** veya Uygulamalar özellikleri **&** ve Windows **Server Hosting** yüklü olduğunu onaylayın. **Windows Server Hosting** yüklü programlar listesinde yoksa,.NET Çekirdek Barındırma Paketini indirin ve yükleyin.

  [Geçerli .NET Çekirdek Hosting Paketi yükleyici (doğrudan indir)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Daha fazla bilgi için [.NET Core Hosting Paketini yükleyin.](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)

* **Uygulama Havuzu** > **İşlemi Modeli** > **Kimliğinin** **ApplicationPoolIdentity** olarak ayarlandıklarından veya özel kimliğin uygulamanın dağıtım klasörüne erişmek için doğru izinlere sahip olduğundan emin olun.

* ASP.NET Core Hosting Paketini kaldırıp barındırma paketinin önceki bir sürümünü yüklediyseniz, *applicationHost.config* dosyası ASP.NET Çekirdek Modülü için bir bölüm içermez. Açık *uygulamaHost.config* at *%windir%/System32/inetsrv/config* ve `<configuration><configSections><sectionGroup name="system.webServer">` bölüm grubunu bulun. ASP.NET Çekirdek Modülü bölümü bölüm grubundan eksikse, bölüm öğesini ekleyin:

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  Alternatif olarak, ASP.NET Core Hosting Paketinin en son sürümünü yükleyin. En son sürüm, desteklenen ASP.NET Core uygulamalarıyla geriye doğru uyumludur.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>Yanlış processPath, eksik PATH değişkeni, Hosting Paketi yüklü değil, sistem/IIS yeniden başlatılmadı, VC++ Yeniden Dağıtılamaz yüklenmedi veya dotnet.exe erişim ihlali

* **Tarayıcı:** HTTP Hata 500.0 - ANCM İşlem İşleyicisi Yük Arızası

* **Başvuru Günlüğü:** Fiziksel kökü 'C:\{PATH} komut satırı '"{...}"\' ile başlatılamamış olan 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' uygulaması ', ErrorCode = '0x80070002 : 0. Uygulama '{PATH}' başlatılamıyordu. Çalıştırılabilir '{PATH}' adresinde bulunamadı. Uygulama başlatmak için başarısız oldu '/LM/W3SVC/2/ROOT', ErrorCode '0x8007023e'.

* **ASP.NET Çekirdek Modül stdout Günlüğü:** Günlük dosyası oluşturulmamadı.

* **ASP.NET Çekirdek Modülü Hata Ayıklama Günlüğü:** Olay Günlüğü: 'Uygulama '{PATH}' başlatılamadı. Çalıştırılabilir '{PATH}' adresinde bulunamadı. Başarısız HRESULT döndü: 0x8007023e

Sorun Giderme:

* Uygulamanın Kestrel'de yerel olarak çalıştığını doğrulayın. İşlem hatası, uygulama içindeki bir sorunun sonucu olabilir. Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.

* Framework'e bağımlı bir dağıtım (FDD) `.\{ASSEMBLY}.exe` veya bağımsız `dotnet` bir dağıtım [(SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)için olduğunu doğrulamak için *web.config'deki* `<aspNetCore>` öğedeki *işlemYolu* özniteliğini denetleyin.

* Bir FDD için *dotnet.exe'ye* PATH ayarları üzerinden erişilemeyebilir. *C:\Program\\ Files\dotnet'in* Sistem YOLU ayarlarında olduğunu doğrulayın.

* Bir FDD için *dotnet.exe'ye* uygulama havuzunun kullanıcı kimliği için erişilemeyebilir. Uygulama havuzu kullanıcı kimliğinin *C:\Program Files\dotnet* dizinine erişimi olduğunu doğrulayın. *C:\Program Files\dotnet* ve uygulama dizinlerinde uygulama havuzu kullanıcı kimliği için yapılandırılan reddedilmiş kurallar olmadığını doğrulayın.

* Bir FDD dağıtılmış ve .NET Core IIS'yi yeniden başlatmadan yüklenmiş olabilir. Ya sunucuyu yeniden başlatın ya da **net stop** çalıştırarak IIS yeniden başlatma / y bir komut istemi net start **w3svc** izledi.

* Bir FDD barındırma sistemine .NET Core çalışma süresini yüklemeden dağıtılmış olabilir. .NET Core çalışma süresi yüklenmediyse, sistemdeki **.NET Core Hosting Paketi yükleyicisini** çalıştırın.

  [Geçerli .NET Çekirdek Hosting Paketi yükleyici (doğrudan indir)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Daha fazla bilgi için [.NET Core Hosting Paketini yükleyin.](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)

  Belirli bir çalışma süresi gerekiyorsa, çalışma saatini [.NET İndirarşivlerinden](https://dotnet.microsoft.com/download/archives) indirin ve sisteme yükleyin. Sistemi yeniden başlatarak veya net stop gerçekleştirerek IIS yeniden başlatarak yüklemeyi tamamlayın **/ y** bir komut istemi net start **w3svc** izledi.

## <a name="incorrect-arguments-of-aspnetcore-element"></a>aspNetCore> öğesinin \<yanlış bağımsız değişkenleri

* **Tarayıcı:** HTTP Hata 500.0 - ANCM İşlem İşleyicisi Yük Arızası

* **Başvuru Günlüğü:** İşleme isteği işleyicisini bulmak için hostfxr çağırmak herhangi bir yerel bağımlılık bulmadan başarısız oldu. Bu büyük olasılıkla uygulama yanlış yapılandırılmış olduğu anlamına gelir, uygulama tarafından hedeflenen ve makineye yüklü Microsoft.NetCore.App ve Microsoft.AspNetCore.App sürümlerini kontrol edin. İşlenme devam isteği işleyicisi bulamadım. Hostfxr çağıran yakalanan çıktı: Dotnet SDK komutları çalıştırmak anlamına mı geliyor? Lütfen dotnet SDK'yı şu telefondan yükleyin: https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 '/LM/W3SVC/3/ROOT', ErrorCode '0x8000ffff' uygulamasını başlatamadı.

* **ASP.NET Çekirdek Modül stdout Günlüğü:** Dotnet SDK komutlarını çalıştırmak mı demek istemedin? Lütfen dotnet SDK yükleyin:https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409

* **ASP.NET Çekirdek Modülü Hata Ayıklama Günlüğü:** İşleme isteği işleyicisini bulmak için hostfxr çağırmak herhangi bir yerel bağımlılık bulmadan başarısız oldu. Bu büyük olasılıkla uygulama yanlış yapılandırılmış olduğu anlamına gelir, uygulama tarafından hedeflenen ve makineye yüklü Microsoft.NetCore.App ve Microsoft.AspNetCore.App sürümlerini kontrol edin. Başarısız HRESULT döndürülür: 0x8000ffff işlenme devam isteği işleyicisi bulamadım. Hostfxr çağıran yakalanan çıktı: Dotnet SDK komutları çalıştırmak anlamına mı geliyor? Lütfen dotnet SDK https://go.microsoft.com/fwlink/?LinkID=798306&clcid=0x409 yükleyin: Başarısız HRESULT döndürülür: 0x8000ffff

Sorun Giderme:

* Uygulamanın Kestrel'de yerel olarak çalıştığını doğrulayın. İşlem hatası, uygulama içindeki bir sorunun sonucu olabilir. Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.

* Framework'e bağımlı bir `<aspNetCore>` dağıtım (FDD) için ya (a) `.\{ASSEMBLY}.dll` olduğunu doğrulamak için *web.config'deki* öğedeki *bağımsız değişken* özniteliğini inceleyin; veya (b) mevcut değil,`arguments=""`boş bir dize ( ),`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`veya uygulamanın bağımsız bir dağıtım (SCD) için bağımsız değişkenlerinin bir listesi.

## <a name="missing-net-core-shared-framework"></a>Eksik .NET Core paylaşılan çerçeve

* **Tarayıcı:** HTTP Hata 500.0 - ANCM İşlem İşleyicisi Yük Arızası

* **Başvuru Günlüğü:** İşleme isteği işleyicisini bulmak için hostfxr çağırmak herhangi bir yerel bağımlılık bulmadan başarısız oldu. Bu büyük olasılıkla uygulama yanlış yapılandırılmış olduğu anlamına gelir, uygulama tarafından hedeflenen ve makineye yüklü Microsoft.NetCore.App ve Microsoft.AspNetCore.App sürümlerini kontrol edin. İşlenme devam isteği işleyicisi bulamadım. Hostfxr çağırarak yakalanan çıktı: Uyumlu bir çerçeve sürümü bulmak mümkün değildi. Belirtilen çerçeve 'Microsoft.AspNetCore.App', sürüm '{VERSION}' bulunamadı.

Uygulama başlatmak için başarısız oldu '/LM/W3SVC/5/ROOT', ErrorCode '0x8000ffff'.

* **ASP.NET Çekirdek Modül stdout Günlüğü:** Uyumlu bir çerçeve sürümü bulmak mümkün değildi. Belirtilen çerçeve 'Microsoft.AspNetCore.App', sürüm '{VERSION}' bulunamadı.

* **ASP.NET Çekirdek Modülü Hata Ayıklama Günlüğü:** Başarısız HRESULT döndü: 0x8000ffff

Sorun Giderme:

Çerçeveye bağımlı dağıtım (FDD) için, sistemde doğru çalışma zamanının yüklü olduğunu doğrulayın.

## <a name="stopped-application-pool"></a>Durdurulan Uygulama Havuzu

* **Tarayıcı:** 503 Hizmet Kullanılamıyor

* **Başvuru Günlüğü:** Giriş yok

* **ASP.NET Çekirdek Modül stdout Günlüğü:** Günlük dosyası oluşturulmamadı.

* **ASP.NET Çekirdek Modülü Hata Ayıklama Günlüğü:** Günlük dosyası oluşturulmamadı.

Sorun Giderme:

Uygulama Havuzunun *Durduruldu* durumunda olmadığını doğrulayın.

## <a name="sub-application-includes-a-handlers-section"></a>Alt uygulama bir \<işleyicileri> bölümü içerir

* **Tarayıcı:** HTTP Hatası 500.19 - Dahili Sunucu Hatası

* **Başvuru Günlüğü:** Giriş yok

* **ASP.NET Çekirdek Modül stdout Günlüğü:** Kök uygulamanın günlük dosyası oluşturulur ve normal çalışma gösterir. Alt uygulamanın günlük dosyası oluşturulmaz.

* **ASP.NET Çekirdek Modülü Hata Ayıklama Günlüğü:** Kök uygulamanın günlük dosyası oluşturulur ve normal çalışma gösterir. Alt uygulamanın günlük dosyası oluşturulmaz.

Sorun Giderme:

Alt uygulamanın *web.config* dosyasının bir `<handlers>` bölüm içermediğini veya alt uygulamanın üst uygulamanın işleyicilerini devralmadığını doğrulayın.

*Web.config'in* `<location>` `<system.webServer>` ana uygulama bölümü bir öğenin içine yerleştirilir. Özellik, <xref:System.Configuration.SectionInformation.InheritInChildApplications*> konum `false` [ \<>](/iis/manage/managing-your-configuration-settings/understanding-iis-configuration-delegation#the-concept-of-location) öğesi içinde belirtilen ayarların, ana uygulamanın bir alt dizininde bulunan uygulamalar tarafından devralınıolmadığını belirtmek üzere ayarlanmıştır. Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module>.

## <a name="stdout-log-path-incorrect"></a>stdout günlük yolu yanlış

* **Tarayıcı:** Uygulama normal yanıt verir.

* **Başvuru Günlüğü:** C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll'de stdout yönlendirmebaşlatılamadı. Özel durum iletisi: HRESULT 0x80070005 {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84 adresinde döndürülür. C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll'de stdout yönlendirmeyi durduramadı. Özel durum iletisi: HRESULT 0x80070002 {PATH} adresinde döndürülür. {PATH}\aspnetcorev2_inprocess.dll'de stdout yeniden yönlendirmebaşlatılamadı.

* **ASP.NET Çekirdek Modül stdout Günlüğü:** Günlük dosyası oluşturulmamadı.

* **ASP.NET Çekirdek Modülü hata ayıklama Günlüğü:** C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll'de stdout yönlendirmebaşlatılamadı. Özel durum iletisi: HRESULT 0x80070005 {PATH}\aspnetcoremodulev2\commonlib\fileoutputmanager.cpp:84 adresinde döndürülür. C:\Program Files\IIS\Asp.Net Core Module\V2\aspnetcorev2.dll'de stdout yönlendirmeyi durduramadı. Özel durum iletisi: HRESULT 0x80070002 {PATH} adresinde döndürülür. {PATH}\aspnetcorev2_inprocess.dll'de stdout yeniden yönlendirmebaşlatılamadı.

Sorun Giderme:

* `stdoutLogFile` *web.config* `<aspNetCore>` öğesinde belirtilen yol yok. Daha fazla bilgi için [ASP.NET Çekirdek Modülü: Log oluşturma ve yeniden yönlendirme.](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)

* Uygulama havuzu kullanıcısının stdout günlük yoluna yazma erişimi yoktur.

## <a name="application-configuration-general-issue"></a>Uygulama yapılandırması genel sorunu

* **Tarayıcı:** HTTP Hata 500.0 - ANCM İşlem Içi İşleyici Yük Arızası **--VEYA--** HTTP Hatası 500,30 - ANCM İşlem Içi Başlatma Hatası

* **Başvuru Günlüğü:** Değişken

* **ASP.NET Çekirdek Modül stdout Günlüğü:** Günlük dosyası oluşturulur, ancak boş veya uygulama başarısız olana kadar normal girişlerle oluşturulur.

* **ASP.NET Çekirdek Modülü Hata Ayıklama Günlüğü:** Değişken

Sorun Giderme:

İşlem, büyük olasılıkla bir uygulama yapılandırması veya programlama sorunu nedeniyle başlatılamamış.

Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Bu konu, sık karşılaşılan hataları açıklar ve Azure Apps Hizmeti ve IIS'de ASP.NET Core uygulamalarını barındırırken belirli hatalar için sorun giderme önerileri sağlar.

Genel sorun giderme <xref:test/troubleshoot-azure-iis>kılavuzu için bkz.

Aşağıdaki bilgileri toplayın:

* Tarayıcı davranışı (durum kodu ve hata iletisi)
* Uygulama Olay Günlüğü girişleri
  * Azure Uygulama &ndash; <xref:test/troubleshoot-azure-iis>Hizmeti Bkz.
  * IIS
    1. **Windows** menüsünde **Başlat'ı** seçin, *Olay Görüntüleyicisi*yazın ve **Enter**tuşuna basın.
    1. Olay **Görüntüleyicisi** açıldıktan sonra kenar çubuğundaki **Windows Günlükleri** > **Uygulamasını** genişletin.
* ASP.NET Core Modülü stdout ve hata ayıklama günlüğü girdileri
  * Azure Uygulama &ndash; <xref:test/troubleshoot-azure-iis>Hizmeti Bkz.
  * IIS, &ndash; ASP.NET Çekirdek Modülü konusunun [Log oluşturma ve yeniden yönlendirme](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection) ve Gelişmiş [tanılama günlükleri](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs) bölümlerindeki yönergeleri izleyin.

Hata bilgilerini aşağıdaki yaygın hatalarla karşılaştırın. Bir eşleşme bulunursa, sorun giderme tavsiyesini uygulayın.

Bu konudaki hataların listesi ayrıntılı değildir. Burada listelenmemiş bir hatayla karşılaşırsanız, hatanın nasıl çoğaltılacağına ilişkin ayrıntılı talimatlar içeren bu konunun altındaki **İçerik geri bildirimi** düğmesini kullanarak yeni bir sorun açın.

[!INCLUDE[Azure App Service Preview Notice](../includes/azure-apps-preview-notice.md)]

## <a name="os-upgrade-removed-the-32-bit-aspnet-core-module"></a>İşletim sistemi yükseltmesi 32 bit ASP.NET Çekirdek Modülü kaldırıldı

**Başvuru Günlüğü:** Modül DLL **C:\WINDOWS\system32\inetsrv\aspnetcore.dll** yüklenemedi. Veriler hatadır.

Sorun Giderme:

**C:\Windows\SysWOW64\inetsrv** dizinindeki işletim sistemi dışı dosyalar işletim sistemi yükseltmesi sırasında korunmaz. ASP.NET Çekirdek Modülü işletim sistemi yükseltmesi öncesinde yüklenirse ve işletim sistemi yükseltmesi sonrasında herhangi bir uygulama havuzu 32 bit modunda çalıştırılırsa, bu sorunla karşılaşılır. İşletim sistemi yükseltmesi yapıldıktan sonra ASP.NET Çekirdek Modül'ünonarımını tamamla. Bkz. [.NET Çekirdek Barındırma paketini yükleyin.](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle) Yükleyici çalıştırıldığında **Onarım'ı** seçin.

## <a name="missing-site-extension-32-bit-x86-and-64-bit-x64-site-extensions-installed-or-wrong-process-bitness-set"></a>Eksik site uzantısı, 32-bit (x86) ve 64-bit (x64) site uzantıları yüklü veya yanlış işlem bitlik kümesi

*Azure Uygulama Hizmetleri tarafından barındırılan uygulamalar için geçerlidir.*

* **Tarayıcı:** HTTP Hata 500.0 - ANCM İşlem İşleyicisi Yük Arızası

* **Başvuru Günlüğü:** İşleme isteği işleyicisini bulmak için hostfxr çağırmak herhangi bir yerel bağımlılık bulmadan başarısız oldu. İşlenme devam isteği işleyicisi bulamadım. Hostfxr çağırarak yakalanan çıktı: Uyumlu bir çerçeve sürümü bulmak mümkün değildi. Belirtilen çerçeve 'Microsoft.AspNetCore.App', sürüm '{VERSION}-preview-\*' bulunamadı. Uygulama başlatmak için başarısız oldu '/LM/W3SVC/1416782824/ROOT', ErrorCode '0x8000ffff'.

* **ASP.NET Çekirdek Modül stdout Günlüğü:** Uyumlu bir çerçeve sürümü bulmak mümkün değildi. Belirtilen çerçeve 'Microsoft.AspNetCore.App', sürüm '{VERSION}-preview-\*' bulunamadı.

Sorun Giderme:

* Uygulamayı önizleme çalışma zamanında çalıştırıyorsanız, uygulamanın bitliği yle ve uygulamanın çalışma zamanı sürümüyle eşleşen 32 bit (x86) **veya** 64 bit (x64) site uzantısını yükleyin. **Uzantının her iki uzantısını veya birden çok çalışma zamanı sürümlerini yüklemeyin.**

  * ASP.NET Core {RUNTIME VERSION} (x86) Çalışma Süresi
  * ASP.NET Çekirdek {RUNTIME VERSION} (x64) Çalışma Süresi

  Uygulamayı yeniden başlatın. Uygulamanın yeniden başlatılması için birkaç saniye bekleyin.

* Uygulamayı önizleme çalışma zamanında çalıştırıyorsanız ve hem 32 bit (x86) hem de 64 bit (x64) [site uzantıları](xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension) yüklüyse, uygulamanın bitliğiyle eşleşmeyen site uzantısını kaldırın. Site uzantısını kaldırdıktan sonra uygulamayı yeniden başlatın. Uygulamanın yeniden başlatılması için birkaç saniye bekleyin.

* Uygulamayı önizleme çalışma zamanında çalıştırmak ve site uzantısıbitness uygulamanınbitliğiyle eşleşiyorsa, önizleme sitesi uzantısının *çalışma zamanı sürümünün* uygulamanın çalışma zamanı sürümüyle eşleştiğini doğrulayın.

* **Uygulama Ayarları'ndaki** **uygulama** platformunun uygulamanın bitliğiyle eşleştiğini doğrulayın.

Daha fazla bilgi için bkz. <xref:host-and-deploy/azure-apps/index#install-the-preview-site-extension>.

## <a name="an-x86-app-is-deployed-but-the-app-pool-isnt-enabled-for-32-bit-apps"></a>Bir x86 uygulaması dağıtıldı, ancak uygulama havuzu 32 bit uygulamalar için etkinleştirildi

* **Tarayıcı:** HTTP Hatası 500.30 - ANCM İşlem Içi Başlatma Hatası

* **Başvuru Günlüğü:** Uygulama '/LM/W3SVC/5/ROOT' fiziksel kök '{PATH}' hit beklenmeyen yönetilen özel durum, özel durum kodu = '0xe0434352'. Daha fazla bilgi için lütfen stderr günlüklerini kontrol edin. Uygulama '/LM/W3SVC/5/ROOT' fiziksel kök '{PATH}' clr ve yönetilen uygulama yüklemek için başarısız oldu. CLR işçi iş parçacığı erken çıktı

* **ASP.NET Çekirdek Modül stdout Günlüğü:** Günlük dosyası oluşturulur, ancak boştur.

Bu senaryo, kendi kendine yeten bir uygulama yayımlarken SDK tarafından sıkışıp kalır. RID platform hedefiyle eşleşmiyorsa SDK bir hata `win10-x64` üretir `<PlatformTarget>x86</PlatformTarget>` (örneğin, proje dosyasındaki RID).

Sorun Giderme:

X86 framework-bağımlı dağıtım`<PlatformTarget>x86</PlatformTarget>`için ( ), 32 bit uygulamalar için IIS uygulama havuzunu etkinleştirin. IIS Manager'da uygulama havuzunun **Gelişmiş Ayarlarını** açın ve **32 Bit Uygulamaları** **True'ya**etkinleştir'i ayarlayın.

## <a name="platform-conflicts-with-rid"></a>RID ile platform çakışmaları

* **Tarayıcı:** HTTP Hatası 502.5 - İşlem Hatası

* **Başvuru Günlüğü:** Fiziksel kökü 'C:\{PATH} olan 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' uygulaması '"C:\{PATH}{ASSEMBLY}\' komut satırıile işleme başlayamadı. {exe|dll}" ', ErrorCode = '0x80004005 : ff.

* **ASP.NET Çekirdek Modül stdout Günlüğü:** İşlenmemiş Özel Durum: System.BadImageFormatException: Dosya veya derleme '{ASSEMBLY}.dll' yükleyemedi. Bir programı yanlış biçime yükleme girişiminde bulunuldu.

Sorun Giderme:

* Uygulamanın Kestrel'de yerel olarak çalıştığını doğrulayın. İşlem hatası, uygulama içindeki bir sorunun sonucu olabilir. Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.

* Bu özel durum, bir uygulamayı yükseltirken ve daha yeni derlemeler dağıtırken Azure Apps dağıtımı için oluşursa, önceki dağıtımdaki tüm dosyaları el ile silin. Kalan uyumsuz derlemeler, yükseltilmiş `System.BadImageFormatException` bir uygulamayı dağıtırken bir özel durumla sonuçlanabilir.

## <a name="uri-endpoint-wrong-or-stopped-website"></a>URI uç noktası yanlış veya durdurulmuş web sitesi

* **Tarayıcı:** ERR_CONNECTION_REFUSED **--VEYA--** Bağlanamıyor

* **Başvuru Günlüğü:** Giriş yok

* **ASP.NET Çekirdek Modül stdout Günlüğü:** Günlük dosyası oluşturulmamadı.

Sorun Giderme:

* Uygulama için doğru URI uç noktasının kullanımda olduğunu onaylayın. Ciltleri kontrol edin.

* IIS web sitesinin *Durduruldu* durumunda olmadığını doğrulayın.

## <a name="corewebengine-or-w3svc-server-features-disabled"></a>CoreWebEngine veya W3SVC sunucu devre dışı özellikleri

**İşletim Sistemi Özel Durumu:** iIS 7.0 CoreWebEngine ve W3SVC özellikleri ASP.NET Core Modül kullanmak için yüklü olmalıdır.

Sorun Giderme:

Uygun rolün ve özelliklerin etkin olduğunu doğrulayın. Bkz. [IIS Yapılandırması.](xref:host-and-deploy/iis/index#iis-configuration)

## <a name="incorrect-website-physical-path-or-app-missing"></a>Yanlış web sitesi fiziksel yol veya uygulama eksik

* **Tarayıcı:** 403 Yasak - Erişim reddedildi **--VEYA--** 403.14 Yasak - Web sunucusu bu dizinin içeriğini listelemeyecek şekilde yapılandırılır.

* **Başvuru Günlüğü:** Giriş yok

* **ASP.NET Çekirdek Modül stdout Günlüğü:** Günlük dosyası oluşturulmamadı.

Sorun Giderme:

IIS web sitesi **Temel Ayarları** ve fiziksel uygulama klasörünü kontrol edin. Uygulamanın IIS web sitesi **Fiziksel yoldaki**klasörde olduğunu doğrulayın.

## <a name="incorrect-role-aspnet-core-module-not-installed-or-incorrect-permissions"></a>Yanlış rol, ASP.NET Çekirdek Modülü yüklü değil veya yanlış izinler

* **Tarayıcı:** 500.19 Dahili Sunucu Hatası - Sayfanın ilgili yapılandırma verileri geçersiz olduğundan istenen sayfaya erişilemiyor. **--VEYA--** Bu sayfa görüntülenemez

* **Başvuru Günlüğü:** Giriş yok

* **ASP.NET Çekirdek Modül stdout Günlüğü:** Günlük dosyası oluşturulmamadı.

Sorun Giderme:

* Uygun rolün etkin olduğunu doğrulayın. Bkz. [IIS Yapılandırması.](xref:host-and-deploy/iis/index#iis-configuration)

* **Açık Programlar & Özellikler** veya Uygulamalar özellikleri **&** ve Windows **Server Hosting** yüklü olduğunu onaylayın. **Windows Server Hosting** yüklü programlar listesinde yoksa,.NET Çekirdek Barındırma Paketini indirin ve yükleyin.

  [Geçerli .NET Çekirdek Hosting Paketi yükleyici (doğrudan indir)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Daha fazla bilgi için [.NET Core Hosting Paketini yükleyin.](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)

* **Uygulama Havuzu** > **İşlemi Modeli** > **Kimliğinin** **ApplicationPoolIdentity** olarak ayarlandıklarından veya özel kimliğin uygulamanın dağıtım klasörüne erişmek için doğru izinlere sahip olduğundan emin olun.

* ASP.NET Core Hosting Paketini kaldırıp barındırma paketinin önceki bir sürümünü yüklediyseniz, *applicationHost.config* dosyası ASP.NET Çekirdek Modülü için bir bölüm içermez. Açık *uygulamaHost.config* at *%windir%/System32/inetsrv/config* ve `<configuration><configSections><sectionGroup name="system.webServer">` bölüm grubunu bulun. ASP.NET Çekirdek Modülü bölümü bölüm grubundan eksikse, bölüm öğesini ekleyin:

  ```xml
  <section name="aspNetCore" overrideModeDefault="Allow" />
  ```

  Alternatif olarak, ASP.NET Core Hosting Paketinin en son sürümünü yükleyin. En son sürüm, desteklenen ASP.NET Core uygulamalarıyla geriye doğru uyumludur.

## <a name="incorrect-processpath-missing-path-variable-hosting-bundle-not-installed-systemiis-not-restarted-vc-redistributable-not-installed-or-dotnetexe-access-violation"></a>Yanlış processPath, eksik PATH değişkeni, Hosting Paketi yüklü değil, sistem/IIS yeniden başlatılmadı, VC++ Yeniden Dağıtılamaz yüklenmedi veya dotnet.exe erişim ihlali

* **Tarayıcı:** HTTP Hatası 502.5 - İşlem Hatası

* **Başvuru Günlüğü:** Fiziksel kökü 'C:\{PATH} komut satırı '"{...}"\' ile başlatılamamış olan 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' uygulaması ', ErrorCode = '0x80070002 : 0.

* **ASP.NET Çekirdek Modül stdout Günlüğü:** Günlük dosyası oluşturulur, ancak boştur.

Sorun Giderme:

* Uygulamanın Kestrel'de yerel olarak çalıştığını doğrulayın. İşlem hatası, uygulama içindeki bir sorunun sonucu olabilir. Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.

* Framework'e bağımlı bir dağıtım (FDD) `.\{ASSEMBLY}.exe` veya bağımsız `dotnet` bir dağıtım [(SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)için olduğunu doğrulamak için *web.config'deki* `<aspNetCore>` öğedeki *işlemYolu* özniteliğini denetleyin.

* Bir FDD için *dotnet.exe'ye* PATH ayarları üzerinden erişilemeyebilir. *C:\Program\\ Files\dotnet'in* Sistem YOLU ayarlarında olduğunu doğrulayın.

* Bir FDD için *dotnet.exe'ye* uygulama havuzunun kullanıcı kimliği için erişilemeyebilir. Uygulama havuzu kullanıcı kimliğinin *C:\Program Files\dotnet* dizinine erişimi olduğunu doğrulayın. *C:\Program Files\dotnet* ve uygulama dizinlerinde uygulama havuzu kullanıcı kimliği için yapılandırılan reddedilmiş kurallar olmadığını doğrulayın.

* Bir FDD dağıtılmış ve .NET Core IIS'yi yeniden başlatmadan yüklenmiş olabilir. Ya sunucuyu yeniden başlatın ya da **net stop** çalıştırarak IIS yeniden başlatma / y bir komut istemi net start **w3svc** izledi.

* Bir FDD barındırma sistemine .NET Core çalışma süresini yüklemeden dağıtılmış olabilir. .NET Core çalışma süresi yüklenmediyse, sistemdeki **.NET Core Hosting Paketi yükleyicisini** çalıştırın.

  [Geçerli .NET Çekirdek Hosting Paketi yükleyici (doğrudan indir)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

  Daha fazla bilgi için [.NET Core Hosting Paketini yükleyin.](xref:host-and-deploy/iis/index#install-the-net-core-hosting-bundle)

  Belirli bir çalışma süresi gerekiyorsa, çalışma saatini [.NET İndirarşivlerinden](https://dotnet.microsoft.com/download/archives) indirin ve sisteme yükleyin. Sistemi yeniden başlatarak veya net stop gerçekleştirerek IIS yeniden başlatarak yüklemeyi tamamlayın **/ y** bir komut istemi net start **w3svc** izledi.

## <a name="incorrect-arguments-of-aspnetcore-element"></a>aspNetCore> öğesinin \<yanlış bağımsız değişkenleri

* **Tarayıcı:** HTTP Hatası 502.5 - İşlem Hatası

* **Başvuru Günlüğü:** Uygulama 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}' fiziksel kökü\{'C: PATH}\' komut satırı '"dotnet" ile işlemi başlatamadı. \{ASSEMBLY}.dll', ErrorCode = '0x80004005 : 80008081.

* **ASP.NET Çekirdek Modül stdout Günlüğü:** Yürütülecek uygulama yok: 'PATH\{ASSEMBLY}.dll'

Sorun Giderme:

* Uygulamanın Kestrel'de yerel olarak çalıştığını doğrulayın. İşlem hatası, uygulama içindeki bir sorunun sonucu olabilir. Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.

* Framework'e bağımlı bir `<aspNetCore>` dağıtım (FDD) için ya (a) `.\{ASSEMBLY}.dll` olduğunu doğrulamak için *web.config'deki* öğedeki *bağımsız değişken* özniteliğini inceleyin; veya (b) mevcut değil,`arguments=""`boş bir dize ( ),`arguments="{ARGUMENT_1}, {ARGUMENT_2}, ... {ARGUMENT_X}"`veya uygulamanın bağımsız bir dağıtım (SCD) için bağımsız değişkenlerinin bir listesi.

Sorun Giderme:

Çerçeveye bağımlı dağıtım (FDD) için, sistemde doğru çalışma zamanının yüklü olduğunu doğrulayın.

## <a name="stopped-application-pool"></a>Durdurulan Uygulama Havuzu

* **Tarayıcı:** 503 Hizmet Kullanılamıyor

* **Başvuru Günlüğü:** Giriş yok

* **ASP.NET Çekirdek Modül stdout Günlüğü:** Günlük dosyası oluşturulmamadı.

Sorun Giderme:

Uygulama Havuzunun *Durduruldu* durumunda olmadığını doğrulayın.

## <a name="sub-application-includes-a-handlers-section"></a>Alt uygulama bir \<işleyicileri> bölümü içerir

* **Tarayıcı:** HTTP Hatası 500.19 - Dahili Sunucu Hatası

* **Başvuru Günlüğü:** Giriş yok

* **ASP.NET Çekirdek Modül stdout Günlüğü:** Kök uygulamanın günlük dosyası oluşturulur ve normal çalışma gösterir. Alt uygulamanın günlük dosyası oluşturulmaz.

Sorun Giderme:

Alt uygulamanın *web.config* dosyasının bir `<handlers>` bölüm içermediğini doğrulayın.

## <a name="stdout-log-path-incorrect"></a>stdout günlük yolu yanlış

* **Tarayıcı:** Uygulama normal yanıt verir.

* **Başvuru Günlüğü:** Uyarı: StdoutLogFile \\oluşturamadı? \{PATH}\path_doesnt_exist\stdout_{PROCESS ID}_{TIMESTAMP}.log, ErrorCode = -2147024893.

* **ASP.NET Çekirdek Modül stdout Günlüğü:** Günlük dosyası oluşturulmamadı.

Sorun Giderme:

* `stdoutLogFile` *web.config* `<aspNetCore>` öğesinde belirtilen yol yok. Daha fazla bilgi için [ASP.NET Çekirdek Modülü: Log oluşturma ve yeniden yönlendirme.](xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection)

* Uygulama havuzu kullanıcısının stdout günlük yoluna yazma erişimi yoktur.

## <a name="application-configuration-general-issue"></a>Uygulama yapılandırması genel sorunu

* **Tarayıcı:** HTTP Hatası 502.5 - İşlem Hatası

* **Başvuru Günlüğü:** Fiziksel\{kökü 'C: PATH}\' komut satırı '"C:\{PATH}\{ASSEMBLY} ile oluşturulan işlem ile uygulama 'MACHINE/WEBROOT/APPHOST/{ASSEMBLY}. {exe|dll}" ' ama çöktü ya da yanıt vermedi ya da verilen bağlantı noktası '{PORT}', ErrorCode = '{ERROR CODE}' üzerinde dinlemedi

* **ASP.NET Çekirdek Modül stdout Günlüğü:** Günlük dosyası oluşturulur, ancak boştur.

Sorun Giderme:

İşlem, büyük olasılıkla bir uygulama yapılandırması veya programlama sorunu nedeniyle başlatılamamış.

Daha fazla bilgi edinmek için aşağıdaki kaynaklara bakın:

* <xref:test/troubleshoot-azure-iis>
* <xref:test/troubleshoot>

::: moniker-end
