---
title: Windows Hizmetinde Ana Bilgisayar ASP.NET Core
author: rick-anderson
description: Windows Hizmetinde bir ASP.NET Core uygulamasını nasıl barındırılacın öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/windows-service
ms.openlocfilehash: 5cb61d330df7e15fbd54396207792596ae018fd3
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80417587"
---
# <a name="host-aspnet-core-in-a-windows-service"></a>Windows Hizmetinde Ana Bilgisayar ASP.NET Core

::: moniker range=">= aspnetcore-3.0"

Bir ASP.NET Core uygulaması, IIS kullanmadan [Windows'da Windows Hizmeti](/dotnet/framework/windows-services/introduction-to-windows-service-applications) olarak barındırılabilir. Windows Hizmeti olarak barındırıldığında, uygulama sunucu yeniden başlatıldıktan sonra otomatik olarak başlar.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Ön koşullar

* [ASP.NET Core SDK 2.1 veya sonrası](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 veya sonrası](https://github.com/PowerShell/PowerShell)

## <a name="worker-service-template"></a>İşçi Hizmeti şablonu

ASP.NET Çekirdek İşçi Hizmeti şablonu, uzun süre çalışan hizmet uygulamalarını yazmak için bir başlangıç noktası sağlar. Şablonu bir Windows Hizmeti uygulaması için temel olarak kullanmak için:

1. .NET Core şablonundan bir İşçi Hizmeti uygulaması oluşturun.
1. Windows Hizmeti olarak çalıştırılabilmek için İşçi Hizmeti uygulamasını güncellemek için [Uygulama yapılandırma](#app-configuration) bölümündeki kılavuzu izleyin.

[!INCLUDE[](~/includes/worker-template-instructions.md)]

## <a name="app-configuration"></a>Uygulama yapılandırması

Uygulama [Microsoft.Extensions.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.Extensions.Hosting.WindowsServices)için bir paket başvuru gerektirir.

`IHostBuilder.UseWindowsService`ana bilgisayar inşa ederken denir. Uygulama Windows Hizmeti olarak çalışıyorsa, yöntem:

* Ana bilgisayar ömrünü `WindowsServiceLifetime`' ne ayarlar.
* İçerik [kökünü](xref:fundamentals/index#content-root) [AppContext.BaseDirectory](xref:System.AppContext.BaseDirectory)olarak ayarlar. Daha fazla bilgi için [Geçerli dizini ve içerik kökü](#current-directory-and-content-root) bölümüne bakın.
* Olay günlüğüne günlüğe kaydetmeyi sağlar:
  * Uygulama adı varsayılan kaynak adı olarak kullanılır.
  * Varsayılan günlük düzeyi, ana bilgisayarı oluşturmak için çağrıda `CreateDefaultBuilder` bulunan ASP.NET Core şablonuna dayalı bir uygulama için *Uyarı* veya daha yüksektir.
  * `Logging:EventLog:LogLevel:Default` *appsettings.json*/appsettings'teki anahtarla varsayılan günlük düzeyini geçersiz*kılın.{ Environment}.json* veya diğer yapılandırma sağlayıcısı.
  * Yalnızca yöneticiler yeni olay kaynakları oluşturabilir. Bir olay kaynağı uygulama adı kullanılarak oluşturuladığında, uygulama *kaynağına* bir uyarı günlüğe kaydedilir ve olay günlükleri devre dışı bırakılır.

`CreateHostBuilder` *Program.cs:*

```csharp
Host.CreateDefaultBuilder(args)
    .UseWindowsService()
    ...
```

Aşağıdaki örnek uygulamalar bu konuya eşlik eder:

* Arka Plan &ndash; İşçi Sat›r›m Örneği Arka plan görevleri için [barındır›lan hizmetleri](xref:fundamentals/host/hosted-services) kullanan İşçi [Hizmeti şablonuna](#worker-service-template) dayanan web olmayan bir uygulama örneği.
* Web App &ndash; Service Örnek Arka plan görevleri için [barındırılan hizmetlerle](xref:fundamentals/host/hosted-services) windows hizmeti olarak çalışan Bir Jilet Sayfaları web uygulaması örneği.

MVC kılavuzu için, altındaki <xref:mvc/overview> <xref:migration/22-to-30>makalelere bakın ve .

## <a name="deployment-type"></a>Dağıtım türü

Dağıtım senaryoları hakkında bilgi ve tavsiye için [bkz.](/dotnet/core/deploying/)

### <a name="sdk"></a>SDK

Razor Pages veya MVC çerçevelerini kullanan web uygulaması tabanlı bir hizmet için, proje dosyasında Web SDK'yı belirtin:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Hizmet yalnızca arka plan görevlerini yürütürse (örneğin, [barındırılan hizmetler),](xref:fundamentals/host/hosted-services)Proje dosyasında İşçi SDK'sını belirtin:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Çerçeveye bağımlı dağıtım (FDD)

Çerçeveye bağımlı dağıtım (FDD), hedef sistemde .NET Core'un sistem genelinde paylaşılan bir sürümünün bulunmasına dayanır. FDD senaryosu bu makaledeki kılavuzdan sonra kabul edildiğinde, *SDK, çerçeveye bağımlı yürütülebilir*olarak adlandırılan bir yürütülebilir (*.exe*) üretir.

Bir ASP.NET Core uygulaması yayımlanırken normalde üretilen *web SDK* kullanıyorsanız, bir Windows Hizmetleri uygulaması için gereksizdir. [Web SDK](#sdk) *web.config* dosyasının oluşturulmasını devre dışı bırakabilmek `<IsTransformWebConfigDisabled>` `true`için, ayarlanan özelliği ekleyin.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp3.0</TargetFramework>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Bağımsız dağıtım (SCD)

Bağımsız dağıtım (SCD), ana bilgisayar sisteminde paylaşılan bir çerçevenin varlığına güvenmez. Çalışma süresi ve uygulamanın bağımlılıkları uygulamayla birlikte dağıtılır.

Bir Windows [Runtime Tanımlayıcısı (RID)](/dotnet/core/rid-catalog) `<PropertyGroup>` hedef çerçeveyi içeren dahildir:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Birden çok RID için yayımlamak için:

* RID'leri yarı sütunlu sınırlı bir listede sağlayın.
* [ \<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (çoğul) özellik adını kullanın.

Daha fazla bilgi için [.NET Core RID Kataloğu'na](/dotnet/core/rid-catalog)bakın.

## <a name="service-user-account"></a>Hizmet kullanıcı hesabı

Bir hizmet için kullanıcı hesabı oluşturmak için, yönetimsel powershell 6 komut kabuğundan [Yeni-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet'ini kullanın.

Windows 10 Ekim 2018 Güncelleştirmesi'nde (sürüm 1809/build 10.0.17763) veya daha sonra:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

Windows 10 Ekim 2018 Güncelleştirmesinden önceki Windows İşletim Sistemi'nde (sürüm 1809/build 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

İstendiğinde güçlü bir [parola](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) sağlayın.

Parametre, sona ermiş <xref:System.DateTime> [yeni yerel kullanıcı](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet'e sağatılmadığı sürece, hesabın süresi dolmaz. `-AccountExpires`

Daha fazla bilgi için [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) ve [Hizmet Kullanıcı Hesapları'na](/windows/desktop/services/service-user-accounts)bakın.

Active Directory kullanırken kullanıcıları yönetmek için alternatif bir yaklaşım Yönetilen Hizmet Hesapları kullanmaktır. Daha fazla bilgi için [Grup Yönetilen Hizmet Hesaplarına Genel Bakış'a](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)bakın.

## <a name="log-on-as-a-service-rights"></a>Hizmet hakları olarak oturum açma

Bir hizmet kullanıcı hesabı için hizmet hakları *olarak Oturum Açma'yı* kurmak için:

1. *Secpol.msc*çalıştırarak Yerel Güvenlik Politikası düzenleyicisini açın.
1. Yerel **İlkeler** düğümünü genişletin ve **Kullanıcı Hakları Ataması'nı**seçin.
1. Hizmet ilkesi **olarak Oturum Aç'ı** açın.
1. **Kullanıcı veya Grup Ekle'yi**seçin.
1. Aşağıdaki yaklaşımlardan birini kullanarak nesne adını (kullanıcı hesabı) sağlayın:
   1. Nesne adı alanına`{DOMAIN OR COMPUTER NAME\USER}`kullanıcı hesabı ( ) yazın ve kullanıcıyı ilkeye eklemek için **Tamam'ı** seçin.
   1. **Gelişmiş**'i seçin. **Şimdi Bul'u**seçin. Listeden kullanıcı hesabını seçin. **Tamam'ı**seçin. Kullanıcıyı ilkeye eklemek için tekrar **Tamam'ı** seçin.
1. Değişiklikleri kabul etmek için **Tamam** veya **Uygula'yı** seçin.

## <a name="create-and-manage-the-windows-service"></a>Windows Hizmeti oluşturma ve yönetme

### <a name="create-a-service"></a>Hizmet oluşturma

Bir hizmeti kaydetmek için PowerShell komutlarını kullanın. İdari powershell 6 komut uyruktan aşağıdaki komutları uygulayın:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`&ndash; Uygulamanın ana bilgisayardaki klasörüne giden yol `d:\myservice`(örneğin, ). Uygulamanın çalıştırılabilir uygulamasını yola eklemeyin. İzbırakan bir kesik gerekli değildir.
* `{DOMAIN OR COMPUTER NAME\USER}`&ndash; Hizmet kullanıcı hesabı (örneğin, `Contoso\ServiceUser`).
* `{SERVICE NAME}`&ndash; Hizmet adı (örneğin, `MyService`).
* `{EXE FILE PATH}`&ndash; Uygulamanın çalıştırılabilir yolu (örneğin, `d:\myservice\myservice.exe`). Yürütülebilir dosya adını uzantılı olarak ekleyin.
* `{DESCRIPTION}`&ndash; Hizmet açıklaması (örneğin, `My sample service`).
* `{DISPLAY NAME}`&ndash; Hizmet görüntü adı (örneğin, `My Service`).

### <a name="start-a-service"></a>Bir hizmet başlatın

Aşağıdaki PowerShell 6 komutuyla bir hizmet başlatın:

```powershell
Start-Service -Name {SERVICE NAME}
```

Komutun hizmeti başlatması birkaç saniye sürer.

### <a name="determine-a-services-status"></a>Bir hizmetin durumunu belirleme

Bir hizmetin durumunu kontrol etmek için aşağıdaki PowerShell 6 komutunu kullanın:

```powershell
Get-Service -Name {SERVICE NAME}
```

Durum aşağıdaki değerlerden biri olarak bildirilir:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Bir hizmet durdurun

Aşağıdaki Powershell 6 komutuna sahip bir hizmeti durdurun:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Bir hizmeti kaldırma

Bir hizmeti durdurmak için kısa bir gecikmeden sonra, aşağıdaki Powershell 6 komutuna sahip bir hizmeti kaldırın:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

Internet'ten veya şirket ağından gelen isteklerle etkileşimde olan ve proxy veya yük dengeleyicisinin arkasında olan hizmetler ek yapılandırma gerektirebilir. Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Uç noktaları yapılandırma

Varsayılan olarak, ASP.NET Core `http://localhost:5000`bağlanır. Ortam değişkenini ayarlayarak `ASPNETCORE_URLS` URL'yi ve bağlantı noktasını yapılandırın.

Ek URL ve bağlantı noktası yapılandırma yaklaşımları için ilgili sunucu makalesine bakın:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

Önceki kılavuz, HTTPS uç noktaları nın desteğini kapsar. Örneğin, kimlik doğrulama bir Windows Hizmeti ile kullanıldığında uygulamayı HTTPS için yapılandırın.

> [!NOTE]
> Bir hizmet bitiş noktasını güvence altına almak için ASP.NET Core HTTPS geliştirme sertifikasının kullanımı desteklenmez.

## <a name="current-directory-and-content-root"></a>Geçerli dizin ve içerik kökü

Bir Windows Hizmeti için <xref:System.IO.Directory.GetCurrentDirectory*> arayarak döndürülen geçerli çalışma dizini *C:\\WINDOWS\\system32* klasörüdür. *System32* klasörü, bir hizmetin dosyalarını depolamak için uygun bir konum değildir (örneğin, ayarlar dosyaları). Bir hizmetin varlıklarını ve ayar dosyalarını korumak ve bu dosyalara erişmek için aşağıdaki yaklaşımlardan birini kullanın.

### <a name="use-contentrootpath-or-contentrootfileprovider"></a>ContentRootPath veya ContentRootFileProvider kullanın

[IHostEnvironment.ContentRootPath'i](xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath) kullanın veya <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootFileProvider> bir uygulamanın kaynaklarını bulun.

Uygulama bir hizmet olarak <xref:Microsoft.Extensions.Hosting.WindowsServiceLifetimeHostBuilderExtensions.UseWindowsService*> çalıştığında, <xref:Microsoft.Extensions.Hosting.IHostEnvironment.ContentRootPath> [AppContext.BaseDirectory'ye](xref:System.AppContext.BaseDirectory)ayarlar.

Uygulamanın varsayılan ayarları dosyaları, *appsettings.json* ve *appsettings.{ Environment}.json*, [ana bilgisayar inşaatı sırasında CreateDefaultBuilder'ı](xref:fundamentals/host/generic-host#set-up-a-host)arayarak uygulamanın içerik kökünden yüklenir.

Geliştirici koduyla <xref:Microsoft.Extensions.Hosting.HostBuilder.ConfigureAppConfiguration*>yüklenen diğer ayarlar için aramanız <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*>gerekmez. Aşağıdaki örnekte, *custom_settings.json* dosyası uygulamanın içerik kökünde bulunur ve açıkça bir temel yol belirlemeden yüklenir:

[!code-csharp[](windows-service/samples_snapshot/CustomSettingsExample.cs?highlight=13)]

Bir Windows Service <xref:System.IO.Directory.GetCurrentDirectory*> uygulaması *C:\\WINDOWS\\system32* klasörünü geçerli dizini olarak döndürür, çünkü bir kaynak yolu elde etmek için kullanmayı denemeyin.

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Bir hizmetin dosyalarını diskte uygun bir konumda depolama

Dosyaları içeren klasöre <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> bir <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> kullanırken ile mutlak bir yol belirtin.

## <a name="troubleshoot"></a>Sorun giderme

Bir Windows Hizmeti uygulamasını gidermek <xref:test/troubleshoot>için bkz.

### <a name="common-errors"></a>Sık karşılaşılan hatalar

* PowerShell'in eski veya ön sürüm sürümü kullanımdadır.
* Kayıtlı hizmet, uygulamanın [yayınlanan dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutundan **çıkan** çıktısını kullanmaz. [Dotnet yapı](/dotnet/core/tools/dotnet-build) komutunun çıktısı uygulama dağıtımı için desteklenmez. Yayımlanmış varlıklar dağıtım türüne bağlı olarak aşağıdaki klasörlerden birinde bulunur:
  * *bin/Release/{TARGET FRAMEWORK}/publish* (FDD)
  * *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)
* Hizmet RUNNING durumunda değil.
* Uygulamanın kullandığı kaynaklara giden yollar (örneğin, sertifikalar) yanlıştır. Bir Windows Hizmetinin temel yolu *c:\\Windows\\System32.*
* Kullanıcının hizmet olarak *Oturum* Açma'sı yoktur.
* PowerShell komutunu çalıştırırken kullanıcının parolasının `New-Service` süresi doldu veya yanlış geçti.
* Uygulama ASP.NET Çekirdek kimlik doğrulaması gerektirir, ancak güvenli bağlantılar (HTTPS) için yapılandırılmamıştır.
* İstek URL bağlantı noktası yanlış veya uygulamada doğru yapılandırılmamıştır.

### <a name="system-and-application-event-logs"></a>Sistem ve Uygulama Etkinlik Günlükleri

Sistem ve Uygulama Etkinlik Günlüklerine Erişin:

1. Başlat menüsünü açın, *Olay Görüntüleyicisi'ni*arayın ve **Olay Görüntüleyicisi** uygulamasını seçin.
1. **Olay Görüntüleyici'de** **Windows Günlükleri** düğümlerini açın.
1. Sistem Olay Günlüğü'nü açmak için **Sistem'i** seçin. Uygulama Olay Günlüğü'nü açmak için **Uygulama'yı** seçin.
1. Başarısız uygulamayla ilişkili hataları arayın.

### <a name="run-the-app-at-a-command-prompt"></a>Uygulamayı komut istemiyle çalıştırma

Birçok başlangıç hatası olay günlüklerinde yararlı bilgiler üretmez. Bazı hataların nedenini, uygulamayı barındırma sistemindeki bir komut isteminde çalıştırarak bulabilirsiniz. Uygulamadan ek ayrıntı günlüğe kaydetmek [için, günlük düzeyini](xref:fundamentals/logging/index#log-level) düşürün veya [uygulamayı Geliştirme ortamında](xref:fundamentals/environments)çalıştırın.

### <a name="clear-package-caches"></a>Paket önbelleklerini temizle

İşleyen bir uygulama, geliştirme makinesindeki .NET Core SDK'yı yükselttikten veya uygulama içindeki paket sürümlerini değiştirdikten hemen sonra başarısız olabilir. Bazı durumlarda, tutarsız paketler büyük yükseltmeler gerçekleştirirken bir uygulamayı bozabilir. Bu sorunların çoğu aşağıdaki yönergeleri izleyerek giderilebilir:

1. *Bin* ve *obj* klasörlerini silin.
1. [Noktanet nuget yerel tüm](/dotnet/core/tools/dotnet-nuget-locals) çalıştırarak paket önbellekleri temizleyin - bir komut kabuğundan temizleyin.

   Paket önbellekleri takas da [nuget.exe](https://www.nuget.org/downloads) aracı ile gerçekleştirilebilir `nuget locals all -clear`ve komutuyguluyor. *nuget.exe,* Windows masaüstü işletim sistemiyle birlikte yüklenmez ve [NuGet web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.

1. Projeyi geri yükleyin ve yeniden oluşturun.
1. Uygulamayı yeniden dağıtmadan önce sunucudaki dağıtım klasöründeki tüm dosyaları silin.

### <a name="slow-or-hanging-app"></a>Yavaş veya asma uygulaması

*Kilitlenme dökümü,* sistemin belleğindebir anlık görüntüdür ve bir uygulamanın çökmesinin, başlangıç hatasının veya yavaş uygulamanın nedenini belirlemeye yardımcı olabilir.

#### <a name="app-crashes-or-encounters-an-exception"></a>Uygulama çöküyor veya bir özel durumla karşılaşıyor

Windows Hata Raporlama [(WER)](/windows/desktop/wer/windows-error-reporting)bir dökümü edinin ve analiz:

1. Kilitlenme döküm dosyalarını '' adresinde `c:\dumps`tutmak için bir klasör oluşturun
1. [EnableDumps PowerShell komut dosyasını](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/EnableDumps.ps1) uygulama çalıştırılabilir adla çalıştırın:

   ```powershell
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Uygulamayı çökmenin oluşmasına neden olan koşullar altında çalıştırın.
1. Çökme oluştuktan sonra, [DisableDumps PowerShell komut dosyası](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/samples/scripts/DisableDumps.ps1)çalıştırın:

   ```powershell
   .\DisableDumps {APPLICATION EXE}
   ```

Bir uygulama çöktükten ve döküm toplama tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir. PowerShell komut dosyası, WER'i uygulama başına en fazla beş çöplük toplamak üzere yapılandırır.

> [!WARNING]
> Kilitlenme dökümleri büyük miktarda disk alanı kapsayabilir (her biri birkaç gigabayta kadar).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Uygulama askıda kalır, başlangıç sırasında başarısız olur veya normal çalışır

Bir uygulama *askıda kaldığında* (yanıt vermeyi durdurur ancak kilitlenmez), başlangıç sırasında başarısız olduğunda veya normal olarak çalıştığında, [bkz.](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)

#### <a name="analyze-the-dump"></a>Dökümü analiz edin

Bir dökümü çeşitli yaklaşımlar kullanılarak analiz edilebilir. Daha fazla bilgi için [bkz.](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)

## <a name="additional-resources"></a>Ek kaynaklar

* [Kestrel uç nokta yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration) (HTTPS yapılandırması ve SNI desteği içerir)
* <xref:fundamentals/host/generic-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Bir ASP.NET Core uygulaması, IIS kullanmadan [Windows'da Windows Hizmeti](/dotnet/framework/windows-services/introduction-to-windows-service-applications) olarak barındırılabilir. Windows Hizmeti olarak barındırıldığında, uygulama sunucu yeniden başlatıldıktan sonra otomatik olarak başlar.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Ön koşullar

* [ASP.NET Core SDK 2.1 veya sonrası](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 veya sonrası](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>Uygulama yapılandırması

Uygulama [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) ve [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog)için paket referansları gerektirir.

Bir hizmetin dışında çalışırken test etmek ve hata ayıklamak için, uygulamanın bir hizmet veya konsol uygulaması olarak mı çalıştığını belirlemek için kod ekleyin. Hata ayıklayıcının bağlı olup olmadığını `--console` veya bir anahtarın olup olmadığını denetleyin. Her iki koşul da doğruysa (uygulama bir hizmet <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>olarak çalıştırılmıyorsa), arayın. Koşullar yanlışsa (uygulama bir hizmet olarak çalıştırılır):

* Uygulamanın yayımlanmış konumuna giden yolu arayın <xref:System.IO.Directory.SetCurrentDirectory*> ve kullanın. Bir Windows <xref:System.IO.Directory.GetCurrentDirectory*> Service uygulaması çağrıldığında *C:\\WINDOWS\\system32* klasörünü döndürdüğünde <xref:System.IO.Directory.GetCurrentDirectory*> yolu almak için aramayın. Daha fazla bilgi için [Geçerli dizini ve içerik kökü](#current-directory-and-content-root) bölümüne bakın. Bu adım, uygulama 'de `CreateWebHostBuilder`yapılandırılmadan önce gerçekleştirilir.
* Uygulamayı <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> hizmet olarak çalıştırmak için arayın.

Komut [satırı Yapılandırma Sağlayıcısı](xref:fundamentals/configuration/index#command-line-configuration-provider) komut satırı bağımsız değişkenleri için ad `--console` değeri çiftleri gerektirdiğinden, geçiş bağımsız değişkenleri almadan önce <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> bağımsız değişkenlerden kaldırılır.

Windows Olay Günlüğü'ne yazmak için <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>EventLog sağlayıcısını ekleyin. Uygulama ayarlarındaki `Logging:LogLevel:Default` anahtarla günlük düzeyini *ayarlayın. Production.json* dosyası.

Örnek uygulamadan aşağıdaki örnekte, `RunAsCustomService` uygulama <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> içindeki yaşam boyu olayları işlemek için yerine çağrılır. Daha fazla bilgi [için, Tanıtıcı başlangıç ve durdurma olayları](#handle-starting-and-stopping-events) bölümüne bakın.

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Dağıtım türü

Dağıtım senaryoları hakkında bilgi ve tavsiye için [bkz.](/dotnet/core/deploying/)

### <a name="sdk"></a>SDK

Razor Pages veya MVC çerçevelerini kullanan web uygulaması tabanlı bir hizmet için, proje dosyasında Web SDK'yı belirtin:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Hizmet yalnızca arka plan görevlerini yürütürse (örneğin, [barındırılan hizmetler),](xref:fundamentals/host/hosted-services)Proje dosyasında İşçi SDK'sını belirtin:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Çerçeveye bağımlı dağıtım (FDD)

Çerçeveye bağımlı dağıtım (FDD), hedef sistemde .NET Core'un sistem genelinde paylaşılan bir sürümünün bulunmasına dayanır. FDD senaryosu bu makaledeki kılavuzdan sonra kabul edildiğinde, *SDK, çerçeveye bağımlı yürütülebilir*olarak adlandırılan bir yürütülebilir (*.exe*) üretir.

Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>) ](/dotnet/core/tools/csproj#runtimeidentifier)hedef çerçeveiçerir. Aşağıdaki örnekte, RID `win7-x64`olarak ayarlanır. `false`Özellik. `<SelfContained>` Bu özellikler, SDK'ya Windows için yürütülebilir (*.exe*) dosyası ve paylaşılan .NET Core çerçevesine bağlı bir uygulama oluşturması talimatını verir.

Normalde bir ASP.NET Core uygulaması yayımlarken üretilen *bir web.config* dosyası, bir Windows Hizmetleri uygulaması için gereksizdir. *web.config* dosyasının oluşturulmasını devre dışı bırakabilmek `<IsTransformWebConfigDisabled>` `true`için, ayarlanan özelliği ekleyin.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Bağımsız dağıtım (SCD)

Bağımsız dağıtım (SCD), ana bilgisayar sisteminde paylaşılan bir çerçevenin varlığına güvenmez. Çalışma süresi ve uygulamanın bağımlılıkları uygulamayla birlikte dağıtılır.

Bir Windows [Runtime Tanımlayıcısı (RID)](/dotnet/core/rid-catalog) `<PropertyGroup>` hedef çerçeveyi içeren dahildir:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Birden çok RID için yayımlamak için:

* RID'leri yarı sütunlu sınırlı bir listede sağlayın.
* [ \<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (çoğul) özellik adını kullanın.

Daha fazla bilgi için [.NET Core RID Kataloğu'na](/dotnet/core/rid-catalog)bakın.

Bir `<SelfContained>` özellik ayarlanır: `true`

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Hizmet kullanıcı hesabı

Bir hizmet için kullanıcı hesabı oluşturmak için, yönetimsel powershell 6 komut kabuğundan [Yeni-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet'ini kullanın.

Windows 10 Ekim 2018 Güncelleştirmesi'nde (sürüm 1809/build 10.0.17763) veya daha sonra:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

Windows 10 Ekim 2018 Güncelleştirmesinden önceki Windows İşletim Sistemi'nde (sürüm 1809/build 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

İstendiğinde güçlü bir [parola](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) sağlayın.

Parametre, sona ermiş <xref:System.DateTime> [yeni yerel kullanıcı](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet'e sağatılmadığı sürece, hesabın süresi dolmaz. `-AccountExpires`

Daha fazla bilgi için [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) ve [Hizmet Kullanıcı Hesapları'na](/windows/desktop/services/service-user-accounts)bakın.

Active Directory kullanırken kullanıcıları yönetmek için alternatif bir yaklaşım Yönetilen Hizmet Hesapları kullanmaktır. Daha fazla bilgi için [Grup Yönetilen Hizmet Hesaplarına Genel Bakış'a](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)bakın.

## <a name="log-on-as-a-service-rights"></a>Hizmet hakları olarak oturum açma

Bir hizmet kullanıcı hesabı için hizmet hakları *olarak Oturum Açma'yı* kurmak için:

1. *Secpol.msc*çalıştırarak Yerel Güvenlik Politikası düzenleyicisini açın.
1. Yerel **İlkeler** düğümünü genişletin ve **Kullanıcı Hakları Ataması'nı**seçin.
1. Hizmet ilkesi **olarak Oturum Aç'ı** açın.
1. **Kullanıcı veya Grup Ekle'yi**seçin.
1. Aşağıdaki yaklaşımlardan birini kullanarak nesne adını (kullanıcı hesabı) sağlayın:
   1. Nesne adı alanına`{DOMAIN OR COMPUTER NAME\USER}`kullanıcı hesabı ( ) yazın ve kullanıcıyı ilkeye eklemek için **Tamam'ı** seçin.
   1. **Gelişmiş**'i seçin. **Şimdi Bul'u**seçin. Listeden kullanıcı hesabını seçin. **Tamam'ı**seçin. Kullanıcıyı ilkeye eklemek için tekrar **Tamam'ı** seçin.
1. Değişiklikleri kabul etmek için **Tamam** veya **Uygula'yı** seçin.

## <a name="create-and-manage-the-windows-service"></a>Windows Hizmeti oluşturma ve yönetme

### <a name="create-a-service"></a>Hizmet oluşturma

Bir hizmeti kaydetmek için PowerShell komutlarını kullanın. İdari powershell 6 komut uyruktan aşağıdaki komutları uygulayın:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`&ndash; Uygulamanın ana bilgisayardaki klasörüne giden yol `d:\myservice`(örneğin, ). Uygulamanın çalıştırılabilir uygulamasını yola eklemeyin. İzbırakan bir kesik gerekli değildir.
* `{DOMAIN OR COMPUTER NAME\USER}`&ndash; Hizmet kullanıcı hesabı (örneğin, `Contoso\ServiceUser`).
* `{SERVICE NAME}`&ndash; Hizmet adı (örneğin, `MyService`).
* `{EXE FILE PATH}`&ndash; Uygulamanın çalıştırılabilir yolu (örneğin, `d:\myservice\myservice.exe`). Yürütülebilir dosya adını uzantılı olarak ekleyin.
* `{DESCRIPTION}`&ndash; Hizmet açıklaması (örneğin, `My sample service`).
* `{DISPLAY NAME}`&ndash; Hizmet görüntü adı (örneğin, `My Service`).

### <a name="start-a-service"></a>Bir hizmet başlatın

Aşağıdaki PowerShell 6 komutuyla bir hizmet başlatın:

```powershell
Start-Service -Name {SERVICE NAME}
```

Komutun hizmeti başlatması birkaç saniye sürer.

### <a name="determine-a-services-status"></a>Bir hizmetin durumunu belirleme

Bir hizmetin durumunu kontrol etmek için aşağıdaki PowerShell 6 komutunu kullanın:

```powershell
Get-Service -Name {SERVICE NAME}
```

Durum aşağıdaki değerlerden biri olarak bildirilir:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Bir hizmet durdurun

Aşağıdaki Powershell 6 komutuna sahip bir hizmeti durdurun:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Bir hizmeti kaldırma

Bir hizmeti durdurmak için kısa bir gecikmeden sonra, aşağıdaki Powershell 6 komutuna sahip bir hizmeti kaldırın:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Olayları başlatma ve durdurma işlemlerini işleme

Ve <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*> <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> olayları <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>işlemek için:

1. , `OnStarted`, ve `OnStopping` yöntemleri <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> ile türetilmiştir bir sınıf oluşturun: `OnStarting`

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Bunun için <xref:Microsoft.AspNetCore.Hosting.IWebHost> bir uzantı `CustomWebHostService` <xref:System.ServiceProcess.ServiceBase.Run*>yöntemi oluşturun:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. In `Program.Main`, `RunAsCustomService` yerine uzantı <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>yöntemi ni arayın:

   ```csharp
   host.RunAsCustomService();
   ```

   In'in <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> `Program.Main`yerini görmek için [Dağıtım türü](#deployment-type) bölümünde gösterilen kod örneğine bakın.

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

Internet'ten veya şirket ağından gelen isteklerle etkileşimde olan ve proxy veya yük dengeleyicisinin arkasında olan hizmetler ek yapılandırma gerektirebilir. Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Uç noktaları yapılandırma

Varsayılan olarak, ASP.NET Core `http://localhost:5000`bağlanır. Ortam değişkenini ayarlayarak `ASPNETCORE_URLS` URL'yi ve bağlantı noktasını yapılandırın.

Ek URL ve bağlantı noktası yapılandırma yaklaşımları için ilgili sunucu makalesine bakın:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

Önceki kılavuz, HTTPS uç noktaları nın desteğini kapsar. Örneğin, kimlik doğrulama bir Windows Hizmeti ile kullanıldığında uygulamayı HTTPS için yapılandırın.

> [!NOTE]
> Bir hizmet bitiş noktasını güvence altına almak için ASP.NET Core HTTPS geliştirme sertifikasının kullanımı desteklenmez.

## <a name="current-directory-and-content-root"></a>Geçerli dizin ve içerik kökü

Bir Windows Hizmeti için <xref:System.IO.Directory.GetCurrentDirectory*> arayarak döndürülen geçerli çalışma dizini *C:\\WINDOWS\\system32* klasörüdür. *System32* klasörü, bir hizmetin dosyalarını depolamak için uygun bir konum değildir (örneğin, ayarlar dosyaları). Bir hizmetin varlıklarını ve ayar dosyalarını korumak ve bu dosyalara erişmek için aşağıdaki yaklaşımlardan birini kullanın.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>İçerik kökü yolunu uygulamanın klasörüne ayarlama

Bir <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> hizmet oluşturulduğunda `binPath` bağımsız değişkene sağlanan aynı yoldur. Ayarlar dosyalarına giden yollar oluşturmak için aramak `GetCurrentDirectory` yerine, uygulamanın içerik <xref:System.IO.Directory.SetCurrentDirectory*> [köküne](xref:fundamentals/index#content-root)giden yolu arayın.

' `Program.Main`de, hizmetin yürütülebilir klasörüne giden yolu belirleyin ve uygulamanın içerik kökünü oluşturmak için yolu kullanın:

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Bir hizmetin dosyalarını diskte uygun bir konumda depolama

Dosyaları içeren klasöre <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> bir <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> kullanırken ile mutlak bir yol belirtin.

## <a name="troubleshoot"></a>Sorun giderme

Bir Windows Hizmeti uygulamasını gidermek <xref:test/troubleshoot>için bkz.

### <a name="common-errors"></a>Sık karşılaşılan hatalar

* PowerShell'in eski veya ön sürüm sürümü kullanımdadır.
* Kayıtlı hizmet, uygulamanın [yayınlanan dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutundan **çıkan** çıktısını kullanmaz. [Dotnet yapı](/dotnet/core/tools/dotnet-build) komutunun çıktısı uygulama dağıtımı için desteklenmez. Yayımlanmış varlıklar dağıtım türüne bağlı olarak aşağıdaki klasörlerden birinde bulunur:
  * *bin/Release/{TARGET FRAMEWORK}/publish* (FDD)
  * *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)
* Hizmet RUNNING durumunda değil.
* Uygulamanın kullandığı kaynaklara giden yollar (örneğin, sertifikalar) yanlıştır. Bir Windows Hizmetinin temel yolu *c:\\Windows\\System32.*
* Kullanıcının hizmet olarak *Oturum* Açma'sı yoktur.
* PowerShell komutunu çalıştırırken kullanıcının parolasının `New-Service` süresi doldu veya yanlış geçti.
* Uygulama ASP.NET Çekirdek kimlik doğrulaması gerektirir, ancak güvenli bağlantılar (HTTPS) için yapılandırılmamıştır.
* İstek URL bağlantı noktası yanlış veya uygulamada doğru yapılandırılmamıştır.

### <a name="system-and-application-event-logs"></a>Sistem ve Uygulama Etkinlik Günlükleri

Sistem ve Uygulama Etkinlik Günlüklerine Erişin:

1. Başlat menüsünü açın, *Olay Görüntüleyicisi'ni*arayın ve **Olay Görüntüleyicisi** uygulamasını seçin.
1. **Olay Görüntüleyici'de** **Windows Günlükleri** düğümlerini açın.
1. Sistem Olay Günlüğü'nü açmak için **Sistem'i** seçin. Uygulama Olay Günlüğü'nü açmak için **Uygulama'yı** seçin.
1. Başarısız uygulamayla ilişkili hataları arayın.

### <a name="run-the-app-at-a-command-prompt"></a>Uygulamayı komut istemiyle çalıştırma

Birçok başlangıç hatası olay günlüklerinde yararlı bilgiler üretmez. Bazı hataların nedenini, uygulamayı barındırma sistemindeki bir komut isteminde çalıştırarak bulabilirsiniz. Uygulamadan ek ayrıntı günlüğe kaydetmek [için, günlük düzeyini](xref:fundamentals/logging/index#log-level) düşürün veya [uygulamayı Geliştirme ortamında](xref:fundamentals/environments)çalıştırın.

### <a name="clear-package-caches"></a>Paket önbelleklerini temizle

İşleyen bir uygulama, geliştirme makinesindeki .NET Core SDK'yı yükselttikten veya uygulama içindeki paket sürümlerini değiştirdikten hemen sonra başarısız olabilir. Bazı durumlarda, tutarsız paketler büyük yükseltmeler gerçekleştirirken bir uygulamayı bozabilir. Bu sorunların çoğu aşağıdaki yönergeleri izleyerek giderilebilir:

1. *Bin* ve *obj* klasörlerini silin.
1. [Noktanet nuget yerel tüm](/dotnet/core/tools/dotnet-nuget-locals) çalıştırarak paket önbellekleri temizleyin - bir komut kabuğundan temizleyin.

   Paket önbellekleri takas da [nuget.exe](https://www.nuget.org/downloads) aracı ile gerçekleştirilebilir `nuget locals all -clear`ve komutuyguluyor. *nuget.exe,* Windows masaüstü işletim sistemiyle birlikte yüklenmez ve [NuGet web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.

1. Projeyi geri yükleyin ve yeniden oluşturun.
1. Uygulamayı yeniden dağıtmadan önce sunucudaki dağıtım klasöründeki tüm dosyaları silin.

### <a name="slow-or-hanging-app"></a>Yavaş veya asma uygulaması

*Kilitlenme dökümü,* sistemin belleğindebir anlık görüntüdür ve bir uygulamanın çökmesinin, başlangıç hatasının veya yavaş uygulamanın nedenini belirlemeye yardımcı olabilir.

#### <a name="app-crashes-or-encounters-an-exception"></a>Uygulama çöküyor veya bir özel durumla karşılaşıyor

Windows Hata Raporlama [(WER)](/windows/desktop/wer/windows-error-reporting)bir dökümü edinin ve analiz:

1. Kilitlenme döküm dosyalarını '' adresinde `c:\dumps`tutmak için bir klasör oluşturun
1. [EnableDumps PowerShell komut dosyasını](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) uygulama çalıştırılabilir adla çalıştırın:

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Uygulamayı çökmenin oluşmasına neden olan koşullar altında çalıştırın.
1. Çökme oluştuktan sonra, [DisableDumps PowerShell komut dosyası](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)çalıştırın:

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Bir uygulama çöktükten ve döküm toplama tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir. PowerShell komut dosyası, WER'i uygulama başına en fazla beş çöplük toplamak üzere yapılandırır.

> [!WARNING]
> Kilitlenme dökümleri büyük miktarda disk alanı kapsayabilir (her biri birkaç gigabayta kadar).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Uygulama askıda kalır, başlangıç sırasında başarısız olur veya normal çalışır

Bir uygulama *askıda kaldığında* (yanıt vermeyi durdurur ancak kilitlenmez), başlangıç sırasında başarısız olduğunda veya normal olarak çalıştığında, [bkz.](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)

#### <a name="analyze-the-dump"></a>Dökümü analiz edin

Bir dökümü çeşitli yaklaşımlar kullanılarak analiz edilebilir. Daha fazla bilgi için [bkz.](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)

## <a name="additional-resources"></a>Ek kaynaklar

* [Kestrel uç nokta yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration) (HTTPS yapılandırması ve SNI desteği içerir)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

Bir ASP.NET Core uygulaması, IIS kullanmadan [Windows'da Windows Hizmeti](/dotnet/framework/windows-services/introduction-to-windows-service-applications) olarak barındırılabilir. Windows Hizmeti olarak barındırıldığında, uygulama sunucu yeniden başlatıldıktan sonra otomatik olarak başlar.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/host-and-deploy/windows-service/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="prerequisites"></a>Ön koşullar

* [ASP.NET Core SDK 2.1 veya sonrası](https://dotnet.microsoft.com/download)
* [PowerShell 6.2 veya sonrası](https://github.com/PowerShell/PowerShell)

## <a name="app-configuration"></a>Uygulama yapılandırması

Uygulama [Microsoft.AspNetCore.Hosting.WindowsServices](https://www.nuget.org/packages/Microsoft.AspNetCore.Hosting.WindowsServices) ve [Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog)için paket referansları gerektirir.

Bir hizmetin dışında çalışırken test etmek ve hata ayıklamak için, uygulamanın bir hizmet veya konsol uygulaması olarak mı çalıştığını belirlemek için kod ekleyin. Hata ayıklayıcının bağlı olup olmadığını `--console` veya bir anahtarın olup olmadığını denetleyin. Her iki koşul da doğruysa (uygulama bir hizmet <xref:Microsoft.AspNetCore.Hosting.WebHostExtensions.Run*>olarak çalıştırılmıyorsa), arayın. Koşullar yanlışsa (uygulama bir hizmet olarak çalıştırılır):

* Uygulamanın yayımlanmış konumuna giden yolu arayın <xref:System.IO.Directory.SetCurrentDirectory*> ve kullanın. Bir Windows <xref:System.IO.Directory.GetCurrentDirectory*> Service uygulaması çağrıldığında *C:\\WINDOWS\\system32* klasörünü döndürdüğünde <xref:System.IO.Directory.GetCurrentDirectory*> yolu almak için aramayın. Daha fazla bilgi için [Geçerli dizini ve içerik kökü](#current-directory-and-content-root) bölümüne bakın. Bu adım, uygulama 'de `CreateWebHostBuilder`yapılandırılmadan önce gerçekleştirilir.
* Uygulamayı <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> hizmet olarak çalıştırmak için arayın.

Komut [satırı Yapılandırma Sağlayıcısı](xref:fundamentals/configuration/index#command-line-configuration-provider) komut satırı bağımsız değişkenleri için ad `--console` değeri çiftleri gerektirdiğinden, geçiş bağımsız değişkenleri almadan önce <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> bağımsız değişkenlerden kaldırılır.

Windows Olay Günlüğü'ne yazmak için <xref:Microsoft.AspNetCore.Hosting.WebHostBuilder.ConfigureLogging*>EventLog sağlayıcısını ekleyin. Uygulama ayarlarındaki `Logging:LogLevel:Default` anahtarla günlük düzeyini *ayarlayın. Production.json* dosyası.

Örnek uygulamadan aşağıdaki örnekte, `RunAsCustomService` uygulama <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> içindeki yaşam boyu olayları işlemek için yerine çağrılır. Daha fazla bilgi [için, Tanıtıcı başlangıç ve durdurma olayları](#handle-starting-and-stopping-events) bölümüne bakın.

[!code-csharp[](windows-service/samples/2.x/AspNetCoreService/Program.cs?name=snippet_Program)]

## <a name="deployment-type"></a>Dağıtım türü

Dağıtım senaryoları hakkında bilgi ve tavsiye için [bkz.](/dotnet/core/deploying/)

### <a name="sdk"></a>SDK

Razor Pages veya MVC çerçevelerini kullanan web uygulaması tabanlı bir hizmet için, proje dosyasında Web SDK'yı belirtin:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

Hizmet yalnızca arka plan görevlerini yürütürse (örneğin, [barındırılan hizmetler),](xref:fundamentals/host/hosted-services)Proje dosyasında İşçi SDK'sını belirtin:

```xml
<Project Sdk="Microsoft.NET.Sdk.Worker">
```

### <a name="framework-dependent-deployment-fdd"></a>Çerçeveye bağımlı dağıtım (FDD)

Çerçeveye bağımlı dağıtım (FDD), hedef sistemde .NET Core'un sistem genelinde paylaşılan bir sürümünün bulunmasına dayanır. FDD senaryosu bu makaledeki kılavuzdan sonra kabul edildiğinde, *SDK, çerçeveye bağımlı yürütülebilir*olarak adlandırılan bir yürütülebilir (*.exe*) üretir.

Windows [Runtime Identifier (RID)](/dotnet/core/rid-catalog) ([\<RuntimeIdentifier>) ](/dotnet/core/tools/csproj#runtimeidentifier)hedef çerçeveiçerir. Aşağıdaki örnekte, RID `win7-x64`olarak ayarlanır. `false`Özellik. `<SelfContained>` Bu özellikler, SDK'ya Windows için yürütülebilir (*.exe*) dosyası ve paylaşılan .NET Core çerçevesine bağlı bir uygulama oluşturması talimatını verir.

`true`Özellik. `<UseAppHost>` Bu özellik, hizmete bir FDD için etkinleştirme yolu (yürütülebilir, *.exe)* sağlar.

Normalde bir ASP.NET Core uygulaması yayımlarken üretilen *bir web.config* dosyası, bir Windows Hizmetleri uygulaması için gereksizdir. *web.config* dosyasının oluşturulmasını devre dışı bırakabilmek `<IsTransformWebConfigDisabled>` `true`için, ayarlanan özelliği ekleyin.

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.2</TargetFramework>
  <RuntimeIdentifier>win7-x64</RuntimeIdentifier>
  <UseAppHost>true</UseAppHost>
  <SelfContained>false</SelfContained>
  <IsTransformWebConfigDisabled>true</IsTransformWebConfigDisabled>
</PropertyGroup>
```

### <a name="self-contained-deployment-scd"></a>Bağımsız dağıtım (SCD)

Bağımsız dağıtım (SCD), ana bilgisayar sisteminde paylaşılan bir çerçevenin varlığına güvenmez. Çalışma süresi ve uygulamanın bağımlılıkları uygulamayla birlikte dağıtılır.

Bir Windows [Runtime Tanımlayıcısı (RID)](/dotnet/core/rid-catalog) `<PropertyGroup>` hedef çerçeveyi içeren dahildir:

```xml
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```

Birden çok RID için yayımlamak için:

* RID'leri yarı sütunlu sınırlı bir listede sağlayın.
* [ \<RuntimeIdentifiers>](/dotnet/core/tools/csproj#runtimeidentifiers) (çoğul) özellik adını kullanın.

Daha fazla bilgi için [.NET Core RID Kataloğu'na](/dotnet/core/rid-catalog)bakın.

Bir `<SelfContained>` özellik ayarlanır: `true`

```xml
<SelfContained>true</SelfContained>
```

## <a name="service-user-account"></a>Hizmet kullanıcı hesabı

Bir hizmet için kullanıcı hesabı oluşturmak için, yönetimsel powershell 6 komut kabuğundan [Yeni-LocalUser](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet'ini kullanın.

Windows 10 Ekim 2018 Güncelleştirmesi'nde (sürüm 1809/build 10.0.17763) veya daha sonra:

```powershell
New-LocalUser -Name {SERVICE NAME}
```

Windows 10 Ekim 2018 Güncelleştirmesinden önceki Windows İşletim Sistemi'nde (sürüm 1809/build 10.0.17763):

```console
powershell -Command "New-LocalUser -Name {SERVICE NAME}"
```

İstendiğinde güçlü bir [parola](/windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements) sağlayın.

Parametre, sona ermiş <xref:System.DateTime> [yeni yerel kullanıcı](/powershell/module/microsoft.powershell.localaccounts/new-localuser) cmdlet'e sağatılmadığı sürece, hesabın süresi dolmaz. `-AccountExpires`

Daha fazla bilgi için [Microsoft.PowerShell.LocalAccounts](/powershell/module/microsoft.powershell.localaccounts/) ve [Hizmet Kullanıcı Hesapları'na](/windows/desktop/services/service-user-accounts)bakın.

Active Directory kullanırken kullanıcıları yönetmek için alternatif bir yaklaşım Yönetilen Hizmet Hesapları kullanmaktır. Daha fazla bilgi için [Grup Yönetilen Hizmet Hesaplarına Genel Bakış'a](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)bakın.

## <a name="log-on-as-a-service-rights"></a>Hizmet hakları olarak oturum açma

Bir hizmet kullanıcı hesabı için hizmet hakları *olarak Oturum Açma'yı* kurmak için:

1. *Secpol.msc*çalıştırarak Yerel Güvenlik Politikası düzenleyicisini açın.
1. Yerel **İlkeler** düğümünü genişletin ve **Kullanıcı Hakları Ataması'nı**seçin.
1. Hizmet ilkesi **olarak Oturum Aç'ı** açın.
1. **Kullanıcı veya Grup Ekle'yi**seçin.
1. Aşağıdaki yaklaşımlardan birini kullanarak nesne adını (kullanıcı hesabı) sağlayın:
   1. Nesne adı alanına`{DOMAIN OR COMPUTER NAME\USER}`kullanıcı hesabı ( ) yazın ve kullanıcıyı ilkeye eklemek için **Tamam'ı** seçin.
   1. **Gelişmiş**'i seçin. **Şimdi Bul'u**seçin. Listeden kullanıcı hesabını seçin. **Tamam'ı**seçin. Kullanıcıyı ilkeye eklemek için tekrar **Tamam'ı** seçin.
1. Değişiklikleri kabul etmek için **Tamam** veya **Uygula'yı** seçin.

## <a name="create-and-manage-the-windows-service"></a>Windows Hizmeti oluşturma ve yönetme

### <a name="create-a-service"></a>Hizmet oluşturma

Bir hizmeti kaydetmek için PowerShell komutlarını kullanın. İdari powershell 6 komut uyruktan aşağıdaki komutları uygulayın:

```powershell
$acl = Get-Acl "{EXE PATH}"
$aclRuleArgs = {DOMAIN OR COMPUTER NAME\USER}, "Read,Write,ReadAndExecute", "ContainerInherit,ObjectInherit", "None", "Allow"
$accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule($aclRuleArgs)
$acl.SetAccessRule($accessRule)
$acl | Set-Acl "{EXE PATH}"

New-Service -Name {SERVICE NAME} -BinaryPathName {EXE FILE PATH} -Credential {DOMAIN OR COMPUTER NAME\USER} -Description "{DESCRIPTION}" -DisplayName "{DISPLAY NAME}" -StartupType Automatic
```

* `{EXE PATH}`&ndash; Uygulamanın ana bilgisayardaki klasörüne giden yol `d:\myservice`(örneğin, ). Uygulamanın çalıştırılabilir uygulamasını yola eklemeyin. İzbırakan bir kesik gerekli değildir.
* `{DOMAIN OR COMPUTER NAME\USER}`&ndash; Hizmet kullanıcı hesabı (örneğin, `Contoso\ServiceUser`).
* `{SERVICE NAME}`&ndash; Hizmet adı (örneğin, `MyService`).
* `{EXE FILE PATH}`&ndash; Uygulamanın çalıştırılabilir yolu (örneğin, `d:\myservice\myservice.exe`). Yürütülebilir dosya adını uzantılı olarak ekleyin.
* `{DESCRIPTION}`&ndash; Hizmet açıklaması (örneğin, `My sample service`).
* `{DISPLAY NAME}`&ndash; Hizmet görüntü adı (örneğin, `My Service`).

### <a name="start-a-service"></a>Bir hizmet başlatın

Aşağıdaki PowerShell 6 komutuyla bir hizmet başlatın:

```powershell
Start-Service -Name {SERVICE NAME}
```

Komutun hizmeti başlatması birkaç saniye sürer.

### <a name="determine-a-services-status"></a>Bir hizmetin durumunu belirleme

Bir hizmetin durumunu kontrol etmek için aşağıdaki PowerShell 6 komutunu kullanın:

```powershell
Get-Service -Name {SERVICE NAME}
```

Durum aşağıdaki değerlerden biri olarak bildirilir:

* `Starting`
* `Running`
* `Stopping`
* `Stopped`

### <a name="stop-a-service"></a>Bir hizmet durdurun

Aşağıdaki Powershell 6 komutuna sahip bir hizmeti durdurun:

```powershell
Stop-Service -Name {SERVICE NAME}
```

### <a name="remove-a-service"></a>Bir hizmeti kaldırma

Bir hizmeti durdurmak için kısa bir gecikmeden sonra, aşağıdaki Powershell 6 komutuna sahip bir hizmeti kaldırın:

```powershell
Remove-Service -Name {SERVICE NAME}
```

## <a name="handle-starting-and-stopping-events"></a>Olayları başlatma ve durdurma işlemlerini işleme

Ve <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarting*> <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStopping*> olayları <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService.OnStarted*>işlemek için:

1. , `OnStarted`, ve `OnStopping` yöntemleri <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostService> ile türetilmiştir bir sınıf oluşturun: `OnStarting`

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/CustomWebHostService.cs?name=snippet_CustomWebHostService)]

2. Bunun için <xref:Microsoft.AspNetCore.Hosting.IWebHost> bir uzantı `CustomWebHostService` <xref:System.ServiceProcess.ServiceBase.Run*>yöntemi oluşturun:

   [!code-csharp[](windows-service/samples/2.x/AspNetCoreService/WebHostServiceExtensions.cs?name=ExtensionsClass)]

3. In `Program.Main`, `RunAsCustomService` yerine uzantı <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*>yöntemi ni arayın:

   ```csharp
   host.RunAsCustomService();
   ```

   In'in <xref:Microsoft.AspNetCore.Hosting.WindowsServices.WebHostWindowsServiceExtensions.RunAsService*> `Program.Main`yerini görmek için [Dağıtım türü](#deployment-type) bölümünde gösterilen kod örneğine bakın.

## <a name="proxy-server-and-load-balancer-scenarios"></a>Proxy sunucusu ve yük dengeleyici senaryoları

Internet'ten veya şirket ağından gelen isteklerle etkileşimde olan ve proxy veya yük dengeleyicisinin arkasında olan hizmetler ek yapılandırma gerektirebilir. Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.

## <a name="configure-endpoints"></a>Uç noktaları yapılandırma

Varsayılan olarak, ASP.NET Core `http://localhost:5000`bağlanır. Ortam değişkenini ayarlayarak `ASPNETCORE_URLS` URL'yi ve bağlantı noktasını yapılandırın.

Ek URL ve bağlantı noktası yapılandırma yaklaşımları için ilgili sunucu makalesine bakın:

* <xref:fundamentals/servers/kestrel#endpoint-configuration>
* <xref:fundamentals/servers/httpsys#configure-windows-server>

Önceki kılavuz, HTTPS uç noktaları nın desteğini kapsar. Örneğin, kimlik doğrulama bir Windows Hizmeti ile kullanıldığında uygulamayı HTTPS için yapılandırın.

> [!NOTE]
> Bir hizmet bitiş noktasını güvence altına almak için ASP.NET Core HTTPS geliştirme sertifikasının kullanımı desteklenmez.

## <a name="current-directory-and-content-root"></a>Geçerli dizin ve içerik kökü

Bir Windows Hizmeti için <xref:System.IO.Directory.GetCurrentDirectory*> arayarak döndürülen geçerli çalışma dizini *C:\\WINDOWS\\system32* klasörüdür. *System32* klasörü, bir hizmetin dosyalarını depolamak için uygun bir konum değildir (örneğin, ayarlar dosyaları). Bir hizmetin varlıklarını ve ayar dosyalarını korumak ve bu dosyalara erişmek için aşağıdaki yaklaşımlardan birini kullanın.

### <a name="set-the-content-root-path-to-the-apps-folder"></a>İçerik kökü yolunu uygulamanın klasörüne ayarlama

Bir <xref:Microsoft.Extensions.Hosting.IHostingEnvironment.ContentRootPath*> hizmet oluşturulduğunda `binPath` bağımsız değişkene sağlanan aynı yoldur. Ayarlar dosyalarına giden yollar oluşturmak için aramak `GetCurrentDirectory` yerine, uygulamanın içerik <xref:System.IO.Directory.SetCurrentDirectory*> [köküne](xref:fundamentals/index#content-root)giden yolu arayın.

' `Program.Main`de, hizmetin yürütülebilir klasörüne giden yolu belirleyin ve uygulamanın içerik kökünü oluşturmak için yolu kullanın:

```csharp
var pathToExe = Process.GetCurrentProcess().MainModule.FileName;
var pathToContentRoot = Path.GetDirectoryName(pathToExe);
Directory.SetCurrentDirectory(pathToContentRoot);

CreateWebHostBuilder(args)
    .Build()
    .RunAsService();
```

### <a name="store-a-services-files-in-a-suitable-location-on-disk"></a>Bir hizmetin dosyalarını diskte uygun bir konumda depolama

Dosyaları içeren klasöre <xref:Microsoft.Extensions.Configuration.FileConfigurationExtensions.SetBasePath*> bir <xref:Microsoft.Extensions.Configuration.IConfigurationBuilder> kullanırken ile mutlak bir yol belirtin.

## <a name="troubleshoot"></a>Sorun giderme

Bir Windows Hizmeti uygulamasını gidermek <xref:test/troubleshoot>için bkz.

### <a name="common-errors"></a>Sık karşılaşılan hatalar

* PowerShell'in eski veya ön sürüm sürümü kullanımdadır.
* Kayıtlı hizmet, uygulamanın [yayınlanan dotnet yayımlama](/dotnet/core/tools/dotnet-publish) komutundan **çıkan** çıktısını kullanmaz. [Dotnet yapı](/dotnet/core/tools/dotnet-build) komutunun çıktısı uygulama dağıtımı için desteklenmez. Yayımlanmış varlıklar dağıtım türüne bağlı olarak aşağıdaki klasörlerden birinde bulunur:
  * *bin/Release/{TARGET FRAMEWORK}/publish* (FDD)
  * *bin/Release/{TARGET FRAMEWORK}/{RUNTIME IDENTIFIER}/publish* (SCD)
* Hizmet RUNNING durumunda değil.
* Uygulamanın kullandığı kaynaklara giden yollar (örneğin, sertifikalar) yanlıştır. Bir Windows Hizmetinin temel yolu *c:\\Windows\\System32.*
* Kullanıcının hizmet olarak *Oturum* Açma'sı yoktur.
* PowerShell komutunu çalıştırırken kullanıcının parolasının `New-Service` süresi doldu veya yanlış geçti.
* Uygulama ASP.NET Çekirdek kimlik doğrulaması gerektirir, ancak güvenli bağlantılar (HTTPS) için yapılandırılmamıştır.
* İstek URL bağlantı noktası yanlış veya uygulamada doğru yapılandırılmamıştır.

### <a name="system-and-application-event-logs"></a>Sistem ve Uygulama Etkinlik Günlükleri

Sistem ve Uygulama Etkinlik Günlüklerine Erişin:

1. Başlat menüsünü açın, *Olay Görüntüleyicisi'ni*arayın ve **Olay Görüntüleyicisi** uygulamasını seçin.
1. **Olay Görüntüleyici'de** **Windows Günlükleri** düğümlerini açın.
1. Sistem Olay Günlüğü'nü açmak için **Sistem'i** seçin. Uygulama Olay Günlüğü'nü açmak için **Uygulama'yı** seçin.
1. Başarısız uygulamayla ilişkili hataları arayın.

### <a name="run-the-app-at-a-command-prompt"></a>Uygulamayı komut istemiyle çalıştırma

Birçok başlangıç hatası olay günlüklerinde yararlı bilgiler üretmez. Bazı hataların nedenini, uygulamayı barındırma sistemindeki bir komut isteminde çalıştırarak bulabilirsiniz. Uygulamadan ek ayrıntı günlüğe kaydetmek [için, günlük düzeyini](xref:fundamentals/logging/index#log-level) düşürün veya [uygulamayı Geliştirme ortamında](xref:fundamentals/environments)çalıştırın.

### <a name="clear-package-caches"></a>Paket önbelleklerini temizle

İşleyen bir uygulama, geliştirme makinesindeki .NET Core SDK'yı yükselttikten veya uygulama içindeki paket sürümlerini değiştirdikten hemen sonra başarısız olabilir. Bazı durumlarda, tutarsız paketler büyük yükseltmeler gerçekleştirirken bir uygulamayı bozabilir. Bu sorunların çoğu aşağıdaki yönergeleri izleyerek giderilebilir:

1. *Bin* ve *obj* klasörlerini silin.
1. [Noktanet nuget yerel tüm](/dotnet/core/tools/dotnet-nuget-locals) çalıştırarak paket önbellekleri temizleyin - bir komut kabuğundan temizleyin.

   Paket önbellekleri takas da [nuget.exe](https://www.nuget.org/downloads) aracı ile gerçekleştirilebilir `nuget locals all -clear`ve komutuyguluyor. *nuget.exe,* Windows masaüstü işletim sistemiyle birlikte yüklenmez ve [NuGet web sitesinden](https://www.nuget.org/downloads)ayrı olarak alınmalıdır.

1. Projeyi geri yükleyin ve yeniden oluşturun.
1. Uygulamayı yeniden dağıtmadan önce sunucudaki dağıtım klasöründeki tüm dosyaları silin.

### <a name="slow-or-hanging-app"></a>Yavaş veya asma uygulaması

*Kilitlenme dökümü,* sistemin belleğindebir anlık görüntüdür ve bir uygulamanın çökmesinin, başlangıç hatasının veya yavaş uygulamanın nedenini belirlemeye yardımcı olabilir.

#### <a name="app-crashes-or-encounters-an-exception"></a>Uygulama çöküyor veya bir özel durumla karşılaşıyor

Windows Hata Raporlama [(WER)](/windows/desktop/wer/windows-error-reporting)bir dökümü edinin ve analiz:

1. Kilitlenme döküm dosyalarını '' adresinde `c:\dumps`tutmak için bir klasör oluşturun
1. [EnableDumps PowerShell komut dosyasını](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/EnableDumps.ps1) uygulama çalıştırılabilir adla çalıştırın:

   ```console
   .\EnableDumps {APPLICATION EXE} c:\dumps
   ```

1. Uygulamayı çökmenin oluşmasına neden olan koşullar altında çalıştırın.
1. Çökme oluştuktan sonra, [DisableDumps PowerShell komut dosyası](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/windows-service/scripts/DisableDumps.ps1)çalıştırın:

   ```console
   .\DisableDumps {APPLICATION EXE}
   ```

Bir uygulama çöktükten ve döküm toplama tamamlandıktan sonra, uygulamanın normal olarak sonlandırılmasına izin verilir. PowerShell komut dosyası, WER'i uygulama başına en fazla beş çöplük toplamak üzere yapılandırır.

> [!WARNING]
> Kilitlenme dökümleri büyük miktarda disk alanı kapsayabilir (her biri birkaç gigabayta kadar).

#### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>Uygulama askıda kalır, başlangıç sırasında başarısız olur veya normal çalışır

Bir uygulama *askıda kaldığında* (yanıt vermeyi durdurur ancak kilitlenmez), başlangıç sırasında başarısız olduğunda veya normal olarak çalıştığında, [bkz.](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)

#### <a name="analyze-the-dump"></a>Dökümü analiz edin

Bir dökümü çeşitli yaklaşımlar kullanılarak analiz edilebilir. Daha fazla bilgi için [bkz.](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)

## <a name="additional-resources"></a>Ek kaynaklar

* [Kestrel uç nokta yapılandırması](xref:fundamentals/servers/kestrel#endpoint-configuration) (HTTPS yapılandırması ve SNI desteği içerir)
* <xref:fundamentals/host/web-host>
* <xref:test/troubleshoot>

::: moniker-end
