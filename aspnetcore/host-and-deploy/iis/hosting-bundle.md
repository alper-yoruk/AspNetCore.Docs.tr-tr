---
title: Hosting Paketi
author: rick-anderson
description: .NET Core barındırma paketi 'nin nasıl yapılandırılacağını öğrenin.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: host-and-deploy/iis/hosting-bundle
ms.openlocfilehash: ecf3dd45575390eee263a275e7f1fb9ec50011bb
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93058447"
---
# <a name="the-net-core-hosting-bundle"></a>.NET Core barındırma paketi

.NET Core barındırma paketi, .NET Core çalışma zamanı ve [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module)için bir yükleyicidir. Paket, ASP.NET Core uygulamaların IIS ile çalışmasına izin verir.

## <a name="install-the-net-core-hosting-bundle"></a>.NET Core barındırma paketi 'ni yükler

> [!IMPORTANT]
> Barındırma paketi IIS 'den önce yüklendiyse, paket yüklemesi onarılması gerekir. IIS yükledikten sonra barındırma paketi yükleyicisini yeniden çalıştırın.
>
> .NET Core 'un 64 bit (x64) sürümünü yükledikten sonra barındırma paketi yüklenirse, SDK 'lar eksik gibi görünebilir ([hiçbir .NET Core SDK 'sı algılanmadı](xref:test/troubleshoot#no-net-core-sdks-were-detected)). Sorunu çözmek için bkz <xref:test/troubleshoot#missing-sdk-after-installing-the-net-core-hosting-bundle> ..

### <a name="direct-download-current-version"></a>Doğrudan indirme (geçerli sürüm)

Aşağıdaki bağlantıyı kullanarak yükleyiciyi indirin:

[Geçerli .NET Core barındırma paketi yükleyicisi (doğrudan indirme)](https://dotnet.microsoft.com/permalink/dotnetcore-current-windows-runtime-bundle-installer)

### <a name="earlier-versions-of-the-installer"></a>Yükleyicinin önceki sürümleri

Yükleyicinin önceki bir sürümünü elde etmek için:

1. [.NET Core 'U indir](https://dotnet.microsoft.com/download/dotnet-core) sayfasına gidin.
1. İstediğiniz .NET Core sürümünü seçin.
1. **Uygulamaları Çalıştır-çalışma zamanı** sütununda, Istenen .NET Core çalışma zamanı sürümünün satırını bulun.
1. **Barındırma paketi** bağlantısını kullanarak yükleyiciyi indirin.

> [!WARNING]
> Bazı yükleyicilerle yaşam süresi (EOL) gelmiş olan ve artık Microsoft tarafından desteklenmeyen yayın sürümlerini içerir. Daha fazla bilgi için bkz. [destek ilkesi](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).

### <a name="install-the-hosting-bundle"></a>Barındırma paketini yükler

1. Yükleyiciyi sunucuda çalıştırın. Aşağıdaki parametreler, yükleyiciyi yönetici komut kabuğu 'ndan çalıştırırken kullanılabilir:

   * `OPT_NO_ANCM=1`: ASP.NET Core modülünü yüklemeyi atlayın.
   * `OPT_NO_RUNTIME=1`: .NET Core çalışma zamanını yüklemeyi atlayın. Sunucu yalnızca [kendi kendine içerilen dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığınızda kullanılır.
   * `OPT_NO_SHAREDFX=1`: ASP.NET paylaşılan çerçevesini (ASP.NET çalışma zamanı) yüklemeyi atlayın. Sunucu yalnızca [kendi kendine içerilen dağıtımları (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd)barındırdığınızda kullanılır.
   * `OPT_NO_X86=1`: X86 çalışma zamanlarını yüklemeyi atlayın. 32 bitlik uygulamalar barındırmayabildiğinizi bildiğiniz durumlarda bu parametreyi kullanın. Gelecekte 32-bit ve 64 bit uygulamaları barındırabilmeniz gereken herhangi bir şansınız varsa, bu parametreyi kullanmayın ve her iki çalışma zamanını da yüklemeyin.
   * `OPT_NO_SHARED_CONFIG_CHECK=1`: Paylaşılan yapılandırma ( `applicationHost.config` ), IIS yüklemesiyle aynı makineli olduğunda IIS paylaşılan yapılandırması kullanma denetimini devre dışı bırakın. *Yalnızca ASP.NET Core 2,2 veya sonraki bir sürümü Paketcisi yükleyicilerini barındırmak için kullanılabilir.* Daha fazla bilgi için bkz. <xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration>.
1. Sistemi yeniden başlatın veya komut kabuğu 'nda aşağıdaki komutları yürütün:

   ```console
   net stop was /y
   net start w3svc
   ```
   IIS 'nin yeniden başlatılması, yükleyici tarafından oluşturulan bir ortam değişkeni olan sistem yolunda bir değişiklik seçer.

ASP.NET Core, paylaşılan çerçeve paketlerinin yama sürümleri için geri iletme davranışını benimsemez. Yeni bir barındırma paketi yükleyerek paylaşılan çerçeveyi yükselttikten sonra, sistemi yeniden başlatın veya komut kabuğu 'nda aşağıdaki komutları yürütün:

```console
net stop was /y
net start w3svc
```

> [!NOTE]
> IIS paylaşılan Yapılandırması hakkında bilgi için bkz. [IIS paylaşılan yapılandırması ile ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module#aspnet-core-module-with-an-iis-shared-configuration).

## <a name="module-version-and-hosting-bundle-installer-logs"></a>Modül sürümü ve barındırma paketi yükleyici günlükleri

Yüklü ASP.NET Core modülünün sürümünü öğrenmek için:

1. Barındırma sisteminde öğesine gidin `%PROGRAMFILES%\IIS\Asp.Net Core Module\V2` .
1. Dosyayı bulun `aspnetcorev2.dll` .
1. Dosyaya sağ tıklayın ve bağlam menüsünden **Özellikler** ' i seçin.
1. **Ayrıntılar** sekmesini seçin. **Dosya sürümü** ve **ürün sürümü** , modülün yüklü sürümünü temsil eder.

Modülün barındırma paketi yükleyici günlükleri konumunda bulunur `C:\Users\%UserName%\AppData\Local\Temp` . Dosyanın adı `dd_DotNetCoreWinSvrHosting__{TIMESTAMP}_000_AspNetCoreModule_x64.log` , yer tutucunun `{TIMESTAMP}` dosyanın zaman damgası olduğu yerdir.
