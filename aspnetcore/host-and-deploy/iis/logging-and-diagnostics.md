---
title: ASP.NET Core modülüyle günlük oluşturma ve yeniden yönlendirme
author: rick-anderson
description: Günlükleri ve tanılama bilgilerini yakalamak için IIS ve ASP.NET Core modülünü yapılandırın.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
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
uid: host-and-deploy/iis/logging-and-diagnostics
ms.openlocfilehash: 9af2311dd1f42cce3547c8215af22d2613453510
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91755314"
---
# <a name="log-creation-and-redirection"></a>Günlük oluşturma ve yeniden yönlendirme

ASP.NET Core modülü, `stdoutLogEnabled` `stdoutLogFile` öğesinin ve öznitelikleri ayarlandıysa stdout ve stderr konsol çıkışını diske yeniden yönlendirir `aspNetCore` . Yoldaki tüm klasörler, `stdoutLogFile` günlük dosyası oluşturulduğunda modül tarafından oluşturulur. Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir ( `IIS AppPool\{APP POOL NAME}` yer tutucunun `{APP POOL NAME}` uygulama havuzu adı olduğu yazma iznini sağlamak için kullanın).

İşlem geri dönüştürme/yeniden başlatma gerçekleşmediği sürece Günlükler döndürülemez. Bu, günlüklerin tükettiği disk alanını sınırlamak için barındırıcının sorumluluğundadır.

Stdout günlüğünün kullanılması yalnızca IIS 'de barındırırken veya [Visual Studio Ile IIS için geliştirme zamanı desteği](xref:host-and-deploy/iis/development-time-iis-support)kullanılırken değil, yerel olarak hata ayıklarken ve uygulamayı IIS Express ile çalıştırırken yalnızca uygulama başlatma sorunlarını gidermek için önerilir.

Genel uygulama günlüğü amaçları için stdout günlüğünü kullanmayın. ASP.NET Core uygulamasında rutin günlük kaydı için, günlük dosyası boyutunu sınırlayan ve günlükleri döndüren bir günlüğe kaydetme kitaplığı kullanın. Daha fazla bilgi için bkz. [üçüncü taraf günlüğü sağlayıcıları](xref:fundamentals/logging/index#third-party-logging-providers).

Günlük dosyası oluşturulduğunda zaman damgası ve dosya uzantısı otomatik olarak eklenir. Günlük dosyası adı, zaman damgası, işlem KIMLIĞI ve dosya uzantısı ( `.log` ), yolun son kesimine `stdoutLogFile` (genellikle `stdout` ) alt çizgi ile ayrılmış olarak eklenerek oluşur. `stdoutLogFile`Yol ile sonlanıyorsa `stdout` , 1934 ' de 19:42:32 2/5/2018 ' de oluşturulan bir uygulama için günlük kaydı dosya adına sahiptir `stdout_20180205194132_1934.log` .

`stdoutLogEnabled`Yanlış ise, uygulama başlangıcında oluşan hatalar yakalanır ve 30 KB 'a kadar olay günlüğüne yayınlanır. Başlangıçtan sonra tüm ek Günlükler atılır.

Aşağıdaki örnek `aspNetCore` öğesi, göreli yoldaki stdout günlüğünü yapılandırır `.\log\` . AppPool Kullanıcı kimliğinin, belirtilen yola yazma izni olduğunu doğrulayın.

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

Azure App Service dağıtım için bir uygulama yayımlarken, Web SDK `stdoutLogFile` değeri olarak ayarlanır `\\?\%home%\LogFiles\stdout` . `%home`Ortam değişkeni, Azure App Service tarafından barındırılan uygulamalar için önceden tanımlanmıştır.

Günlüğe kaydetme filtresi kuralları oluşturmak için ASP.NET Core günlük belgelerinin [yapılandırma](xref:fundamentals/logging/index#log-filtering) ve [günlük filtreleme](xref:fundamentals/logging/index#log-filtering) bölümlerine bakın.

Yol biçimleri hakkında daha fazla bilgi için bkz. [Windows sistemlerinde dosya yolu biçimleri](/dotnet/standard/io/file-path-formats).

## <a name="enhanced-diagnostic-logs"></a>Gelişmiş tanılama günlükleri

ASP.NET Core modülü, gelişmiş tanılama günlükleri sağlamak için yapılandırılabilir. `<handlerSettings>`Öğesini `<aspNetCore>` içindeki öğesine ekleyin `web.config` . İçin ayarı `debugLevel` , `TRACE` Tanılama bilgilerini daha yüksek bir şekilde kullanıma sunar:

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

Yoldaki tüm klasörler ( `logs` Önceki örnekte), günlük dosyası oluşturulduğunda modül tarafından oluşturulur. Uygulama havuzunun, günlüklerin yazıldığı konuma yazma erişimi olması gerekir ( `IIS AppPool\{APP POOL NAME}` yer tutucunun `{APP POOL NAME}` uygulama havuzu adı olduğu yazma iznini sağlamak için kullanın).

Hata ayıklama düzeyi ( `debugLevel` ) değerleri hem düzeyi hem de konumu içerebilir.

Düzeyler (en az ayrıntıdan en fazla ayrıntı sırasına göre):

* HATA
* UYARI
* BILGISINE
* TRACE

Konumlar (birden çok konuma izin verilir):

* KONSOLA
* EVENTLOG
* DOSYA

İşleyici ayarları, ortam değişkenleri aracılığıyla da kullanılabilir:

* `ASPNETCORE_MODULE_DEBUG_FILE`: Hata ayıklama günlük dosyasının yolu. (Varsayılan: `aspnetcore-debug.log` )
* `ASPNETCORE_MODULE_DEBUG`: Hata ayıklama düzeyi ayarı.

> [!WARNING]
> Bir sorunu gidermek için dağıtımda hata ayıklama günlüğü 'nün gerekenden uzun süre **etkin bırakmayın.** Günlüğün boyutu sınırlı değil. Hata ayıklama günlüğünün etkin bırakılması, kullanılabilir disk alanını tüketebilir ve sunucu veya App Service 'i kilitlemez.

Dosyadaki [ `web.config` ](xref:host-and-deploy/iis/web-config#configuration-with-webconfig) öğesinin bir örneği Için bkz. yapılandırma `aspNetCore` `web.config` .
