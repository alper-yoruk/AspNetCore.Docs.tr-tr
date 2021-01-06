---
title: Uygulama çevrimdışı dosyası (app_offline.htm)
author: rick-anderson
description: Uygulama çevrimdışı dosyasının ( `app_offline.htm` ) ASP.NET Core modülüyle nasıl çalıştığını öğrenin.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/13/2020
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
uid: host-and-deploy/iis/app-offline
ms.openlocfilehash: 29f3fc5ecd18196d914a46629bc9eb50b183bf61
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "94431036"
---
# <a name="app-offline-file-app_offlinehtm"></a>Uygulama çevrimdışı dosyası ( `app_offline.htm` )

Uygulama çevrimdışı dosyası ( `app_offline.htm` ), bir uygulamayı kapatmak için ASP.NET Core modülü tarafından kullanılır.

Uygulamanın kök dizininde ada sahip bir dosya `app_offline.htm` algılanırsa, ASP.NET Core modülü uygulamayı düzgün bir şekilde kapatmaya ve gelen istekleri işlemeyi durdurmaya çalışır. Uygulama, içinde tanımlanan saniye sayısından sonra hala çalışıyorsa `shutdownTimeLimit` , ASP.NET Core modülü çalışan işlemi sonlandırır.

`app_offline.htm`Dosya mevcut olsa da, ASP.NET Core modülü dosya içeriğini geri göndererek isteklere yanıt verir `app_offline.htm` . `app_offline.htm`Dosya kaldırıldığında, sonraki istek uygulamayı başlatır.

İşlem dışı barındırma modeli kullanılırken, açık bir bağlantı varsa uygulama hemen kapanmayabilir. Örneğin, bir WebSocket bağlantısı, uygulamanın kapatılmasını erteleyebilir.

## <a name="locked-deployment-files"></a>Kilitli dağıtım dosyaları

Dağıtım klasöründeki dosyalar, uygulama çalışırken kilitlenir. Dağıtım sırasında kilitli dosyaların üzerine yazılamaz.

`app_offline.htm` , kilitli dosyaları serbest bırakmaya yönelik birincil mekanizmadır. `app_offline.htm` , uygulamayı düzgün bir şekilde durdurmak ve başlatmak için Web Dağıtımı tarafından kullanılır.

`app_offline.htm` uygulamayı başlatmak ve durdurmak için el ile kullanılabilir (PowerShell 5 veya üzerini gerektirir):

```powershell
$pathToApp = '{PATH TO APP}'


New-Item -Path $pathToApp -Name "app_offline.htm" -ItemType "file"

# Provide script commands here to deploy the app

Remove-Item -Path $pathToApp\app_offline.htm
```

Önceki PowerShell betiğinden:

* Yer tutucu, `{PATH TO APP}` uygulamanın yoludur.
* `New-Item`Komut, uygulama havuzunu sonlandırır.
* `Remove-Item`Komut, uygulama havuzunu başlatır.
* `New-Item`Komutu ile komutu arasındaki komutlar, `Remove-Item` uygulamayı dağıtmak için geliştirici tarafından sağlanır.

Ayrıca, sunucudaki IIS Yöneticisi 'ndeki uygulama havuzunu el ile durdurarak dosyaların kilidi açılabilir. `app_offine.htm`Uygulama havuzunu durdurup yeniden başlatmak IÇIN IIS Yöneticisi 'ni kullanırken dosyayı kullanmayın.
