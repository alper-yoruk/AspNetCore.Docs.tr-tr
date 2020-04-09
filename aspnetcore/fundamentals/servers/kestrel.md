---
title: ASP.NET Core'da kerkenez web sunucusu uygulaması
author: rick-anderson
description: ASP.NET Core'un çapraz platform web sunucusu Kestrel hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 18846d60fd5c29f17cb4e59192795fd92251e2d0
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976774"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a>ASP.NET Core'da kerkenez web sunucusu uygulaması

Yazar: [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), ve [Stephen Halter](https://twitter.com/halter73)

::: moniker range=">= aspnetcore-3.0"

Kerkenez core ASP.NET [için](xref:fundamentals/servers/index)bir çapraz platform web sunucusudur. Kerkenez, ASP.NET Core proje şablonlarında varsayılan olarak bulunan web sunucusudur.

Kerkenez aşağıdaki senaryoları destekler:

* HTTPS
* [WebSockets'i](https://github.com/aspnet/websockets) etkinleştirmek için kullanılan opak yükseltme
* Nginx arkasında yüksek performans için Unix soketleri
* HTTP/2 (macOS&dagger;hariç)

&dagger;HTTP/2 gelecekte yayınlanacak bir sürümde macOS'ta desteklenecektir.

Kerkenez, .NET Core'un desteklediği tüm platformlarda ve sürümlerde desteklenir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="http2-support"></a>HTTP/2 desteği

Aşağıdaki temel gereksinimler yerine [getirildiğinde, http/2](https://httpwg.org/specs/rfc7540.html) ASP.NET Core uygulamaları için kullanılabilir:

* İşletim sistemi&dagger;
  * Windows Server 2016/Windows 10 veya sonrası&Dagger;
  * OpenSSL 1.0.2 veya sonrası (örneğin, Ubuntu 16.04 veya sonrası) ile Linux
* Hedef çerçeve: .NET Core 2.2 veya sonrası
* [Uygulama Katmanı Protokolü Anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı
* TLS 1.2 veya daha sonra bağlantı

&dagger;HTTP/2 gelecekte yayınlanacak bir sürümde macOS'ta desteklenecektir.
&Dagger;Kerkenez, Windows Server 2012 R2 ve Windows 8.1'de HTTP/2 için sınırlı bir desteğe sahiptir. Bu işletim sistemlerinde bulunan desteklenen TLS şifreleme paketlerinin listesi sınırlı olduğundan destek sınırlıdır. TLS bağlantılarını güvence altına almak için Eliptik Eğri Dijital İmza Algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekebilir.

Bir HTTP/2 bağlantısı kuruluysa, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) bildirir. `HTTP/2`

HTTP/2 varsayılan olarak devre dışı bırakılır. Yapılandırma hakkında daha fazla bilgi için [Kerkenez seçenekleri](#kestrel-options) ve [ListenOptions.Protocols](#listenoptionsprotocols) bölümlerine bakın.

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Ters proxy ile Kerkenez ne zaman kullanılır

Kerkenez kendisi tarafından veya *ters proxy sunucusu*ile kullanılabilir , Internet Bilgi Hizmetleri gibi [(IIS)](https://www.iis.net/), [Nginx](https://nginx.org), veya [Apache](https://httpd.apache.org/). Ters proxy sunucusu ağdan HTTP isteklerini alır ve bunları Kestrel'e ileter.

Kerkenez bir kenar (Internet bakan) web sunucusu olarak kullanılır:

![Kerkenez ters proxy sunucusu olmadan internet ile doğrudan iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

Ters proxy yapılandırmasında kullanılan kerkenez:

![Kerkenez, IIS, Nginx veya Apache gibi ters proxy sunucusu aracılığıyla Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

Ters proxy sunucusu olan veya olmayan yapılandırma, desteklenen bir barındırma yapılandırmasıdır.

Ters proxy sunucusu olmayan bir kenar sunucusu olarak kullanılan kerkenez, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez. Kestrel bir bağlantı noktasında dinleyecek şekilde yapılandırıldığında, Kestrel isteklerin `Host` üstbilgilerinden bağımsız olarak o bağlantı noktasının tüm trafiğini işler. Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel'e iletme yeteneğine sahiptir.

Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucusu kullanmak iyi bir seçim olabilir.

Ters proxy:

* Barındırdığı uygulamaların açıkta kalan ortak yüzey alanını sınırlayabilir.
* Ek bir yapılandırma ve savunma katmanı sağlayın.
* Mevcut altyapı ile daha iyi entegre olabilir.
* Yük dengeleme ve güvenli iletişim (HTTPS) yapılandırması basitleştirin. Yalnızca ters proxy sunucusu bir X.509 sertifikası gerektirir ve bu sunucu apaçık HTTP kullanarak dahili ağdaki uygulamasunucularıyla iletişim kurabilir.

> [!WARNING]
> Ters proxy yapılandırmasında barındırma [ana bilgisayar filtreleme](#host-filtering)gerektirir.

## <a name="kestrel-in-aspnet-core-apps"></a>ASP.NET Core uygulamalarında kerkenez

ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır. *Program.cs*yılında <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> , <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>yöntem çağırır:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

Ana bilgisayar oluşturma hakkında daha fazla bilgi için, ana bilgisayar <xref:fundamentals/host/generic-host#set-up-a-host>ve Varsayılan *oluşturucu ayarları* bölümlerini *ayarlama* bölümüne bakın.

Aradıktan `ConfigureWebHostDefaults`sonra ek yapılandırma `ConfigureKestrel`sağlamak için , kullanın:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

## <a name="kestrel-options"></a>Kerkenez seçenekleri

Kestrel web sunucusu, özellikle Internet'e dönük dağıtımlarda kullanışlı olan kısıtlama yapılandırma seçeneklerine sahiptir.

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> Sınıfın <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> özelliği üzerindeki kısıtlamaları ayarlayın. Özellik `Limits` sınıfın bir örneğini <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> tutar.

Aşağıdaki <xref:Microsoft.AspNetCore.Server.Kestrel.Core> örneklerde ad alanı kullanılır:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

Bu makalede daha sonra gösterilen örneklerde Kerkenez seçenekleri C# kodunda yapılandırılır. Kerkenez seçenekleri de bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir. Örneğin, [Dosya Yapılandırma Sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider) kestrel yapılandırmasını bir *appsettings.json* veya *appsettings'ten yükleyebilir.{ Çevre}.json* dosyası:

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>ve [uç nokta yapılandırması](#endpoint-configuration) yapılandırma sağlayıcılarından yapılandırılabilir. Kalan Kerkenez yapılandırması C# kodunda yapılandırılmalıdır.

Aşağıdaki **yaklaşımlardan birini** kullanın:

* Kerkenezi `Startup.ConfigureServices`yapılandır:

  1. Sınıfa bir `IConfiguration` örnek `Startup` enjekte edin. Aşağıdaki örnek, enjekte edilen yapılandırmanın `Configuration` özelliğe atandığını varsayar.
  2. In `Startup.ConfigureServices`, `Kestrel` Kerkenez yapılandırma içine yapılandırma bölümü yükleyin:

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* Ev sahibini kurarken Kerkenezi yapılandırın:

  *Program.cs,* yapılandırma `Kestrel` bölümünü Kestrel'in yapılandırmasına yükleyin:

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

Önceki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.

### <a name="keep-alive-timeout"></a>Zaman ayarı tutma

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

Canlı kalma [zaman ını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar. Varsayılan olarak 2 dakika dır.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a>Maksimum istemci bağlantıları

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

Aşağıdaki kodla tüm uygulama için eşzamanlı açık TCP bağlantısı sayısı ayarlanabilir:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

HTTP veya HTTPS'den başka bir protokole yükseltilmiş bağlantılar için ayrı bir sınır vardır (örneğin, bir WebSockets isteğinde). Bir bağlantı yükseltildikten sonra, sınıra `MaxConcurrentConnections` göre sayılmaz.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

Maksimum bağlantı sayısı varsayılan olarak sınırsızdır (null).

### <a name="maximum-request-body-size"></a>Maksimum istek gövde boyutu

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

Varsayılan maksimum istek gövde boyutu yaklaşık 28,6 MB olan 30.000.000 bayttır.

Bir ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> önerilen yaklaşım, özniteliği bir eylem yönteminde kullanmaktır:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Aşağıda, her istekte uygulamanın kısıtlamasının nasıl yapılandırılabildiğini gösteren bir örnek verilmiştir:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

Ara yazılımda belirli bir istek üzerindeki ayarı geçersiz kılın:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Uygulama isteği okumaya başladıktan sonra uygulama istek sınırını yapılandırırsa bir özel durum atılır. Özelliğin `MaxRequestBodySize` salt `IsReadOnly` okunur durumda olup olmadığını gösteren bir özellik vardır, bu da sınırı yapılandırmak için çok geç olduğu anlamına gelir.

Bir uygulama [ASP.NET Çekirdek Modülü'nün](xref:host-and-deploy/aspnet-core-module)arkasında [işlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model) kaldığında, IIS sınırı zaten belirlediği için Kestrel'in istek gövde boyutu sınırı devre dışı bırakılır.

### <a name="minimum-request-body-data-rate"></a>Minimum istek gövde veri oranı

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kerkenez, verilerin bayt/saniye cinsinden belirtilen oranda gelişip ulaşmadığında her saniye kontrol eder. Oran minimumun altına düşerse, bağlantı zamanlanır. Yetkisiz kullanım süresi, Kestrel'in müşteriye gönderim oranını en aza kadar yükseltmesi için verdiği süredir; bu süre içinde fiyat kontrol edilir. Yetkisiz kullanım süresi, TCP'nin yavaş başlaması nedeniyle başlangıçta veri gönderen bağlantıların düşmesini önlemeye yardımcı olur.

Varsayılan minimum oran, 5 saniyelik yetkisiz kullanım süresine sahip 240 bayt/saniyedir.

Yanıt için de minimum ücret uygulanır. İstek sınırını ve yanıt sınırını ayarlamak için kod, `RequestBody` özellik `Response` ve arabirim adlarına sahip olmak veya bunlar dışında aynıdır.

Aşağıda, *Program.cs*minimum veri oranlarının nasıl yapılandırılabildiğini gösteren bir örnek verilmiştir:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

Ara yazılımda istek başına minimum ücret limitlerini geçersiz kılın:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

Önceki <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> örnekte başvurulan http/2 `HttpContext.Features` istekleri için mevcut değildir, çünkü istek başına fiyat sınırlarının değiştirilmesi genellikle protokolün istek çoklama desteği nedeniyle HTTP/2 için desteklenmez. Ancak, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> okuma oranı `HttpContext.Features` sınırı bir HTTP/2 isteği `IHttpMinRequestBodyDataRateFeature.MinDataRate` için `null` bile ayarlayarak istek başına bazda *tamamen devre dışı* bırakıldığından, HTTP/2 istekleri için hala mevcut. Okumaya `IHttpMinRequestBodyDataRateFeature.MinDataRate` çalışmak veya başka bir değere ayarlamaya `null` çalışmak, `NotSupportedException` http/2 isteği verilen bir değerin atılmasına neden olur.

Http/1.x ve `KestrelServerOptions.Limits` HTTP/2 bağlantıları için de sunucu genelinde yapılandırılan oran sınırları hala geçerlidir.

### <a name="request-headers-timeout"></a>Üstbilgi zaman alabı isteği

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Sunucunun istek üstbilgilerini almak için harcadığı maksimum süreyi alır veya ayarlar. Varsayılan olarak 30 saniye.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a>Bağlantı başına maksimum akış

`Http2.MaxStreamsPerConnection`http/2 bağlantısı başına eşzamanlı istek akışlarının sayısını sınırlar. Fazla akışreddedilir.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

Varsayılan değer 100’dür.

### <a name="header-table-size"></a>Üstbilgi tablo boyutu

HPACK kod çözücü http/2 bağlantıları için HTTP başlıkları decompresses. `Http2.HeaderTableSize`HPACK kod çözücünün kullandığı üstbilgi sıkıştırma tablosunun boyutunu sınırlar. Değer sekizli olarak sağlanır ve sıfırdan büyük olmalıdır (0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

Varsayılan değer 4096'dır.

### <a name="maximum-frame-size"></a>Maksimum kare boyutu

`Http2.MaxFrameSize`sunucu tarafından alınan veya gönderilen BIR HTTP/2 bağlantı çerçevesi yükünün izin verilen maksimum boyutunu gösterir. Değeri sekizli olarak sağlanır ve 2^14 (16.384) ile 2^24-1 (16.777.215) arasında olmalıdır.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

Varsayılan değer 2^14 (16.384) 'dir.

### <a name="maximum-request-header-size"></a>Maksimum istek üstbilgi boyutu

`Http2.MaxRequestHeaderFieldSize`istek üstbilgi değerlerinin sekizlisinde izin verilen maksimum boyutu gösterir. Bu sınır, sıkıştırılmış ve sıkıştırılmamış gösterimlerindeki hem ad hem de değer için geçerlidir. Değer sıfırdan büyük olmalıdır (0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

Varsayılan değer 8.192'dir.

### <a name="initial-connection-window-size"></a>İlk bağlantı penceresi boyutu

`Http2.InitialConnectionWindowSize`bağlantı başına tüm istekler (akışlar) arasında toplanan sunucu arabellekleri baytlar maksimum istek gövde verileri gösterir. İstekler de `Http2.InitialStreamWindowSize`. Değer 65.535'ten büyük veya eşit ve 2^31'den az olmalıdır (2,147,483,648).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

Varsayılan değer 128 KB (131.072) 'dir.

### <a name="initial-stream-window-size"></a>İlk akış penceresi boyutu

`Http2.InitialStreamWindowSize`istek başına sunucu arabelleklerini baytlar halinde ki maksimum istek gövdesi verilerini gösterir (akış). İstekler de `Http2.InitialConnectionWindowSize`. Değer 65.535'ten büyük veya eşit ve 2^31'den az olmalıdır (2,147,483,648).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

Varsayılan değer 96 KB 'dir (98.304).

### <a name="synchronous-io"></a>Zaman Uyumlu G/Ç

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>istek ve yanıt için senkron G/Ç'ye izin verilip verilmediğini denetler. Varsayılan değer: `false`.

> [!WARNING]
> Çok sayıda senkron G/Ç işlemini engelleme, iş parçacığı havuzunun açlığa yol açabilir ve bu da uygulamanın yanıt vermemesini sağlar. Yalnızca `AllowSynchronousIO` eşzamanlı G/Ç'yi desteklemeyen bir kitaplık kullanırken etkinleştirin.

Aşağıdaki örnek, senkron G/Ç'yi sağlar:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

Diğer Kerkenez seçenekleri ve sınırları hakkında bilgi için bkz:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>Bitiş noktası yapılandırması

Varsayılan olarak, ASP.NET Core bağlanır:

* `http://localhost:5000`
* `https://localhost:5001`(yerel bir geliştirme sertifikası varsa)

Aşağıdakileri kullanarak URL'leri belirtin:

* `ASPNETCORE_URLS`çevre değişkenidir.
* `--urls`komut satırı bağımsız değişkeni.
* `urls`ana bilgisayar yapılandırma anahtarı.
* `UseUrls`uzatma yöntemi.

Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS). Değeri yarı sütunlu ayrılmış bir liste olarak `"Urls": "http://localhost:8000;http://localhost:8001"`yapılandırın (örneğin, ).

Bu yaklaşımlar hakkında daha fazla bilgi için [Sunucu URL'leri](xref:fundamentals/host/web-host#server-urls) ve [Geçersiz Kılma yapılandırması'na](xref:fundamentals/host/web-host#override-configuration)bakın.

Geliştirme sertifikası oluşturulur:

* [.NET Core SDK](/dotnet/core/sdk) yüklendiğinde.
* [Dev-certs aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.

Bazı tarayıcılar, yerel geliştirme sertifikasına güvenmek için açık izin verilmesini gerektirir.

Proje şablonları uygulamaları varsayılan olarak HTTPS'de çalışacak şekilde yapılandırır ve [HTTPS yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.

Kestrel <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> için URL önekleri ve bağlantı noktalarını yapılandırmak için arama <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> veya <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> yöntemler.

`UseUrls`, `--urls` komut satırı bağımsız `urls` değişkeni, ana `ASPNETCORE_URLS` bilgisayar yapılandırma anahtarı ve ortam değişkeni de çalışır, ancak bu bölümde daha sonra belirtilen sınırlamalar vardır (varsayılan bir sertifika HTTPS bitiş noktası yapılandırması için kullanılabilir olmalıdır).

`KestrelServerOptions`Yapılandırma:

### <a name="configureendpointdefaultsactionlistenoptions"></a>YapılandırmaEndpointDefaults(Eylem\<DinlemeSeçenekleri>)

Belirtilen her bitiş `Action` noktası için çalışacak bir yapılandırma belirtir. Birden `ConfigureEndpointDefaults` çok kez `Action`arama, öncekilerin yerine geçen son `Action` belirtilenin yerini alır.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> Aramadan <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> **önce** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> arayarak oluşturulan uç noktalarda varsayılanlar uygulanmaz.

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>YapılandırmaHttpsDefaults(Eylem\<HttpsConnectionAdapterOptions>)

Her HTTPS bitiş `Action` noktası için çalışacak bir yapılandırma belirtir. Birden `ConfigureHttpsDefaults` çok kez `Action`arama, öncekilerin yerine geçen son `Action` belirtilenin yerini alır.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> Aramadan <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> **önce** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> arayarak oluşturulan uç noktalarda varsayılanlar uygulanmaz.

### <a name="configureiconfiguration"></a>Yapılandırma(IConfiguration)

Kestrel'i ayarlamak için bir yapılandırma yükleyicisi oluşturur ve bu <xref:Microsoft.Extensions.Configuration.IConfiguration> da girdi olarak alır. Yapılandırma Kerkenez için yapılandırma bölümüne kapsamı olmalıdır.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Kestrel'i HTTPS'yi kullanacak şekilde yapılandırın.

`ListenOptions.UseHttps`Uzantı -ları:

* `UseHttps`&ndash; Kestrel'i varsayılan sertifikayla HTTPS'yi kullanacak şekilde yapılandırın. Varsayılan sertifika yapılandırılmamışsa özel durum oluşturur.
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

`ListenOptions.UseHttps`Parametre:

* `filename`uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yol ve dosya adıdır.
* `password`X.509 sertifika verilerine erişmek için gereken paroladır.
* `configureOptions`bir `Action` yapılandırmak `HttpsConnectionAdapterOptions`için . ' `ListenOptions`yi döndürür.
* `storeName`sertifikayı yüklemek için sertifika deposudur.
* `subject`sertifikanın özne adıdır.
* `allowInvalid`kendi imzalı sertifikalar gibi geçersiz sertifikaların dikkate alınıp alınmaması gerektiğini gösterir.
* `location`sertifikayı yüklemek için mağaza konumudur.
* `serverCertificate`X.509 sertifikasıdır.

Üretimde, HTTPS açıkça yapılandırılmalıdır. En azından varsayılan sertifika sağlanmalıdır.

Sonraki açıklanan desteklenen yapılandırmalar:

* Yapılandırma yok
* Yapılandırmadan varsayılan sertifikayı değiştirme
* Koddaki varsayılanları değiştirme

*Yapılandırma yok*

Kerkenez üzerinde `http://localhost:5000` dinler `https://localhost:5001` ve (varsayılan bir sertifika varsa).

<a name="configuration"></a>

*Yapılandırmadan varsayılan sertifikayı değiştirme*

`CreateDefaultBuilder`kerkenez yapılandırmasını yüklemek için varsayılan olarak çağırır. `Configure(context.Configuration.GetSection("Kestrel"))` Kestrel için varsayılan bir HTTPS uygulama ayarları yapılandırma şeması kullanılabilir. Diskteki bir dosyadan veya bir sertifika deposundan kullanılacak URL'ler ve kullanılacak sertifikalar da dahil olmak üzere birden çok uç noktayı yapılandırın.

Aşağıdaki *appsettings.json* örnekte:

* Geçersiz sertifikaların `true` kullanımına izin vermek için **AllowInvalid'i** ayarlayın (örneğin, kendi imzalı sertifikalar).
* Sertifika belirtmeyen herhangi bir HTTPS bitiş noktası (Aşağıdaki örnekte**HttpsDefaultCert)** **Varsayılan** **Sertifikalar** > veya geliştirme sertifikası altında tanımlanan sertifikaya geri döner.

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },
      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

Herhangi bir sertifika düğümü için **Yol** ve **Parola** kullanmanın alternatifi, sertifika deposu alanlarını kullanarak sertifikayı belirtmektir. Örneğin, **Sertifikalar** > **Varsayılan** sertifika olarak belirtilebilir:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Şema notları:

* Uç nokta adları büyük/küçük harf duyarsızdır. Örneğin, `HTTPS` ve `Https` geçerlidir.
* Parametre `Url` her bitiş noktası için gereklidir. Bu parametrenin biçimi, tek bir değerle sınırlı olması dışında üst düzey `Urls` yapılandırma parametresi ile aynıdır.
* Bu uç noktalar, bunlara eklemek `Urls` yerine üst düzey yapılandırmada tanımlanan noktaların yerini alır. Kodda tanımlanan uç `Listen` noktalar, yapılandırma bölümünde tanımlanan uç noktalarla birlikte kümülatiftir.
* Bölüm `Certificate` isteğe bağlıdır. `Certificate` Bölüm belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır. Varsayılan yoksa, sunucu bir özel durum atar ve başlatılmaz.
* Bu `Certificate` bölüm hem **Yol**&ndash;**Parolası** hem de **Konu**&ndash;**Deposu** sertifikalarını destekler.
* Bağlantı noktası çakışmalarına neden olmadıkları sürece, herhangi bir uç nokta sayısı bu şekilde tanımlanabilir.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))`yapılandırılmış `KestrelConfigurationLoader` bir `.Endpoint(string name, listenOptions => { })` uç nokta nın ayarlarını tamamlamak için kullanılabilecek bir yöntemle döndürür:

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

`KestrelServerOptions.ConfigurationLoader`tarafından sağlanan gibi varolan yükleyici üzerinde yinelemeye devam etmek için <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>doğrudan erişilebilir.

* Her bitiş noktası için yapılandırma bölümü, özel `Endpoint` ayarların okunabilmesi için yöntemdeki seçeneklerde kullanılabilir.
* Başka bir bölümle yeniden `options.Configure(context.Configuration.GetSection("{SECTION}"))` arayarak birden çok yapılandırma yüklenebilir. Önceki örneklerde açıkça çağrılmadığı sürece `Load` yalnızca son yapılandırma kullanılır. Metapackage, varsayılan yapılandırma `Load` bölümünün değiştirilebilecek şekilde çağrılmaz.
* `KestrelConfigurationLoader`api `Listen` ailesi aşırı yükler `KestrelServerOptions` `Endpoint` olarak aynalar, bu nedenle kod ve config uç noktaları aynı yerde yapılandırılabilir. Bu aşırı yüklemeler adlar kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları tüketir.

*Koddaki varsayılanları değiştirme*

`ConfigureEndpointDefaults`ve `ConfigureHttpsDefaults` önceki senaryoda belirtilen `ListenOptions` varsayılan `HttpsConnectionAdapterOptions`sertifikayı geçersiz kılmak da dahil olmak üzere varsayılan ayarları değiştirmek için kullanılabilir. `ConfigureEndpointDefaults`ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

*SNI için Kerkenez desteği*

[Sunucu Adı Göstergesi (SNI),](https://tools.ietf.org/html/rfc6066#section-3) aynı IP adresi ve bağlantı noktasında birden çok etki alanı barındırmak için kullanılabilir. SNI'nin çalışması için istemci, sunucunun doğru sertifikayı sağlayabilmesi için TLS el sıkışması sırasında güvenli oturumun ana bilgisayar adını sunucuya gönderir. İstemci, TLS el sıkışmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için verilen sertifikayı kullanır.

Kerkenez `ServerCertificateSelector` geri arama yoluyla SNI destekler. Geri arama, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesine izin vermek için bağlantı başına bir kez çağrılır.

SNI desteği şunları gerektirir:

* Hedef çerçevede `netcoreapp2.1` veya daha sonra çalışma. On `net461` veya daha sonra, geri arama `name` çağrılır ama her zaman `null`. `name` İstemci `null` TLS el sıkışmasında ana bilgisayar adı parametresini sağlamazsa da öyledir.
* Tüm web siteleri aynı Kerkenez örneğinde çalışır. Kestrel ters bir proxy olmadan birden çok örnek arasında bir IP adresi ve bağlantı noktası paylaşımı desteklemez.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(
                StringComparer.OrdinalIgnoreCase);
            certs["localhost"] = localhostCert;
            certs["example.com"] = exampleCert;
            certs["sub.example.com"] = subExampleCert;

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="connection-logging"></a>Bağlantı günlüğü

Bağlantıdaki bayt düzeyinde iletişim için Hata Ayıklama düzeyi günlüklerini yaramak için arayın. <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> Bağlantı günlüğü, TLS şifreleme sırasında ve yakınlık ların arkasında olduğu gibi düşük düzeyli iletişimdeki sorun giderme sorunları için yararlıdır. Önce `UseConnectionLogging` `UseHttps`yerleştirilirse, şifreli trafik günlüğe kaydedilir. Sonra `UseConnectionLogging` `UseHttps`yerleştirilirse, şifresi çözülmüş trafik günlüğe kaydedilir.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a>TCP soketine bağlama

Yöntem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> bir TCP soketine bağlanır ve lambda seçeneği X.509 sertifika yapılandırmasına izin verir:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

Örnek, HTTPS'yi <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>bir bitiş noktası için . Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak için aynı API'yi kullanın.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Unix soketine bağlanma

Bu örnekte gösterildiği <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> gibi, Nginx ile geliştirilmiş performans için bir Unix soketinde dinleyin:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* `server`  >  `location`  >  `proxy_pass` Nginx yapılandırma dosyasında, girişini `http://unix:/tmp/{KESTREL SOCKET}:/;`. `{KESTREL SOCKET}`verilen soketin adıdır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (örneğin, `kestrel-test.sock` önceki örnekte).
* Soketin Nginx tarafından yazılabilir olduğundan `chmod go+w /tmp/kestrel-test.sock`emin olun (örneğin, ).

### <a name="port-0"></a>Bağlantı Noktası 0

Bağlantı noktası `0` numarası belirtildiğinde, Kerkenez dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır. Aşağıdaki örnek, Kestrel'in çalışma zamanında gerçekte hangi bağlantı noktasına bağlı olduğunu nasıl belirleyeceklerini gösterir:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Uygulama çalıştırıldığında, konsol penceresi çıkışı uygulamaya ulaşılabilen dinamik bağlantı noktasını gösterir:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>Sınırlamalar

Uç noktaları aşağıdaki yaklaşımlarla yapılandırın:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* `--urls`komut satırı bağımsız değişkeni
* `urls`ana bilgisayar yapılandırma anahtarı
* `ASPNETCORE_URLS`çevre değişkeni

Bu yöntemler, Kerkenez dışındaki sunucularla kod çalışması yapmak için yararlıdır. Ancak, aşağıdaki sınırlamalar dikkat edin:

* HTTPS bitiş noktası yapılandırmasında varsayılan bir sertifika sağlanmadıkça (örneğin, bu konuda `KestrelServerOptions` daha önce gösterildiği gibi yapılandırma veya yapılandırma dosyasını kullanmak gibi) HTTPS bu yaklaşımlarla kullanılamaz.
* Hem yaklaşımlar `Listen` `UseUrls` hem de yaklaşımlar aynı `Listen` anda kullanıldığında, `UseUrls` uç noktalar uç noktaları geçersiz kılar.

### <a name="iis-endpoint-configuration"></a>IIS uç nokta yapılandırması

IIS kullanırken, IIS geçersiz kılma bağlamaları için URL `Listen` `UseUrls`bağlamaları ya da . Daha fazla bilgi için [ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.

### <a name="listenoptionsprotocols"></a>ListenOptions.Protokoller

Özellik, `Protocols` bağlantı bitiş noktasında`HttpProtocols`veya sunucu için etkinleştirilmiş HTTP protokollerini ( ) kurar. `HttpProtocols` Enumdan `Protocols` özelliğe bir değer atayın.

| `HttpProtocols`enum değeri | Bağlantı protokolüne izin verilir |
| -------------------------- | ----------------------------- |
| `Http1`                    | Sadece HTTP/1.1. TLS ile veya TLS olmadan kullanılabilir. |
| `Http2`                    | Sadece HTTP/2. TLS olmadan kullanılabilir, ancak istemci [Bir Ön Bilgi modunu](https://tools.ietf.org/html/rfc7540#section-3.4)destekliyorsa. |
| `Http1AndHttp2`            | HTTP/1.1 ve HTTP/2. HTTP/2, TLS [Uygulama Katmanı Protokolü Anlaşması 'nda (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) el sıkışmasında istemcinin HTTP/2'yi seçmesini gerektirir; aksi takdirde, bağlantı varsayılan OLARAK HTTP/1.1'e olur. |

Herhangi `ListenOptions.Protocols` bir uç nokta `HttpProtocols.Http1AndHttp2`için varsayılan değer.

HTTP/2 için TLS kısıtlamaları:

* TLS sürüm 1.2 veya sonrası
* Yeniden anlaşma devre dışı
* Sıkıştırma devre dışı
* Minimum geçici anahtar değişim boyutları:
  * Eliptik eğri Diffie-Hellman &lbrack;(ECDHE) [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; &ndash; 224 bit minimum
  * Sonlu alan Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; &ndash; 2048 bit minimum
* Şifreli paket kara listeye alınmadı

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack; &lbrack; `FIPS186` &rbrack; P-256 eliptik eğrisi ile varsayılan olarak desteklenir. `TLS-ECDHE` &rbrack;

Aşağıdaki örnek, 8000 no'l'daki porttaki HTTP/1.1 ve HTTP/2 bağlantılarına izin verir. Bağlantılar, sağlanan bir sertifika ile TLS tarafından güvence altına alınır:

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

TLS el sıkışmalarını gerekirse belirli şifreler için bağlantı başına filtrelemek için Bağlantı Middleware'i kullanın.

Aşağıdaki örnek, <xref:System.NotSupportedException> uygulamanın desteklemediği herhangi bir şifreleme algoritması için atar. Alternatif olarak, [iTlsHandshakeFeature.CipherAlgorithm'ı](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) kabul edilebilir şifre paketleri listesiyle tanımlayın ve karşılaştırın.

[CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) şifreleme algoritması ile şifreleme kullanılmaz.

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

Bağlantı filtreleme bir <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda ile de yapılandırılabilir:

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

Linux'ta <xref:System.Net.Security.CipherSuitesPolicy> TLS el sıkışmalarını bağlantı başına filtrelemek için kullanılabilir:

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

*Protokolü yapılandırmadan ayarlama*

`CreateDefaultBuilder`kerkenez yapılandırmasını yüklemek için varsayılan olarak çağırır. `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`

Aşağıdaki *appsettings.json* örneği, tüm uç noktalar için varsayılan bağlantı protokolü olarak HTTP/1.1'i oluşturur:

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

Aşağıdaki *appsettings.json* örneği belirli bir bitiş noktası için HTTP/1.1 bağlantı protokolünü oluşturur:

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

Kodda belirtilen protokoller yapılandırma tarafından ayarlanan değerleri geçersiz kılar.

## <a name="transport-configuration"></a>Aktarım yapılandırması

Libuv kullanımını gerektiren projeler için<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>( ):

* Uygulamanın proje dosyasına [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) paketine bağımlılık ekleyin:

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* Çağrı: <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> `IWebHostBuilder`

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateHostBuilder(args).Build().Run();
       }

       public static IHostBuilder CreateHostBuilder(string[] args) =>
           Host.CreateDefaultBuilder(args)
               .ConfigureWebHostDefaults(webBuilder =>
               {
                   webBuilder.UseLibuv();
                   webBuilder.UseStartup<Startup>();
               });
   }
   ```

### <a name="url-prefixes"></a>URL önekleri

URL `UseUrls`önekleri, komut `--urls` `urls` satırı bağımsız değişkeni, ana bilgisayar yapılandırma anahtarı veya `ASPNETCORE_URLS` ortam değişkeni kullanırken aşağıdaki biçimlerin herhangi birinde bulunabilir.

Yalnızca HTTP URL önekleri geçerlidir. Kestrel, URL bağlamalarını kullanarak `UseUrls`yapılandırırken HTTPS'yi desteklemez.

* Bağlantı noktası numarası olan IPv4 adresi

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0`tüm IPv4 adreslerine bağlanan özel bir durumdur.

* Bağlantı noktası numarası olan IPv6 adresi

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]`IPv4'ün `0.0.0.0`IPv6 eşdeğeridir.

* Bağlantı noktası numarası olan ana bilgisayar adı

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Ev sahibi `*`adları `+`ve özel değildir. Geçerli bir IP adresi olarak `localhost` tanınmayan veya tüm IPv4 ve IPv6 IP'lerine bağlanan her şey. Farklı ana bilgisayar adlarını aynı bağlantı noktasındaki farklı ASP.NET Core uygulamalarına bağlamak için [HTTP.sys](xref:fundamentals/servers/httpsys) veya IIS, Nginx veya Apache gibi ters proxy sunucusu kullanın.

  > [!WARNING]
  > Ters proxy yapılandırmasında barındırma [ana bilgisayar filtreleme](#host-filtering)gerektirir.

* Bağlantı `localhost` noktası numarası veya bağlantı noktası numarası olan loopback IP ile ana bilgisayar adı

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Belirtildiğinde, `localhost` Kerkenez hem IPv4 hem de IPv6 geri dönüş arabirimlerine bağlanmaya çalışır. İstenen bağlantı noktası her iki döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılmaz. Geri dönüş arabirimi başka bir nedenle kullanılamıyorsa (en sık IPv6 desteklenmediği için), Kestrel bir uyarı yı günlüğe kaydeder.

## <a name="host-filtering"></a>Ana bilgisayar filtresi

Kerkenez gibi öneklere dayalı yapılandırmayı `http://example.com:5000`desteklerken, Kerkenez büyük ölçüde ana bilgisayar adını yok sayar. Ana `localhost` bilgisayar, geri dönüş adreslerine bağlanmak için kullanılan özel bir durumdur. Açık bir IP adresi dışındaki tüm ana bilgisayar, tüm genel IP adreslerine bağlanır. `Host`üstbilgi doğrulanmadı.

Geçici çözüm olarak, Ana Bilgisayar Filtreleme Ara Ware'i kullanın. Ana Bilgisayar Filtreleme Middleware, ASP.NET Core uygulamaları için örtülü olarak sağlanan [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır. Middleware tarafından <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>eklenir , <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>hangi çağırır:

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Ana Bilgisayar Filtreleme Middleware varsayılan olarak devre dışı bırakılır. Ara yazılımı etkinleştirmek için `AllowedHosts` *appsettings.json*/uygulama ayarlarında bir anahtar*tanımlayın.\< EnvironmentName>.json*. Değer, bağlantı noktası numarası olmayan ana bilgisayar adlarının yarı sütunlu sınırlı bir listesidir:

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [İlezli Üstbilgi Middleware](xref:host-and-deploy/proxy-load-balancer) de bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçenek vardır. İletilenmiş Üstbilgi Middleware ve Ana Bilgisayar Filtreleme Middleware farklı senaryolar için benzer işlevsellik var. Üstbilgi nin ters proxy sunucusu veya `Host` yük dengeleyicisi ilerlerken üstbilgi korunmadığında Iletili Üstbilgi Middleware ile ayarlama `AllowedHosts` uygundur. Kerkenez genel kullanıma dönük kenar sunucusu olarak kullanıldığında veya `AllowedHosts` `Host` üstbilgi doğrudan iledildiğinde Ana Bilgisayar Filtreleme Middleware ile ayarlama uygundur.
>
> İlliyeli Üstbilgiler Middleware hakkında <xref:host-and-deploy/proxy-load-balancer>daha fazla bilgi için bkz.

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Kerkenez core ASP.NET [için](xref:fundamentals/servers/index)bir çapraz platform web sunucusudur. Kerkenez, ASP.NET Core proje şablonlarında varsayılan olarak bulunan web sunucusudur.

Kerkenez aşağıdaki senaryoları destekler:

* HTTPS
* [WebSockets'i](https://github.com/aspnet/websockets) etkinleştirmek için kullanılan opak yükseltme
* Nginx arkasında yüksek performans için Unix soketleri
* HTTP/2 (macOS&dagger;hariç)

&dagger;HTTP/2 gelecekte yayınlanacak bir sürümde macOS'ta desteklenecektir.

Kerkenez, .NET Core'un desteklediği tüm platformlarda ve sürümlerde desteklenir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="http2-support"></a>HTTP/2 desteği

Aşağıdaki temel gereksinimler yerine [getirildiğinde, http/2](https://httpwg.org/specs/rfc7540.html) ASP.NET Core uygulamaları için kullanılabilir:

* İşletim sistemi&dagger;
  * Windows Server 2016/Windows 10 veya sonrası&Dagger;
  * OpenSSL 1.0.2 veya sonrası (örneğin, Ubuntu 16.04 veya sonrası) ile Linux
* Hedef çerçeve: .NET Core 2.2 veya sonrası
* [Uygulama Katmanı Protokolü Anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı
* TLS 1.2 veya daha sonra bağlantı

&dagger;HTTP/2 gelecekte yayınlanacak bir sürümde macOS'ta desteklenecektir.
&Dagger;Kerkenez, Windows Server 2012 R2 ve Windows 8.1'de HTTP/2 için sınırlı bir desteğe sahiptir. Bu işletim sistemlerinde bulunan desteklenen TLS şifreleme paketlerinin listesi sınırlı olduğundan destek sınırlıdır. TLS bağlantılarını güvence altına almak için Eliptik Eğri Dijital İmza Algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekebilir.

Bir HTTP/2 bağlantısı kuruluysa, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) bildirir. `HTTP/2`

HTTP/2 varsayılan olarak devre dışı bırakılır. Yapılandırma hakkında daha fazla bilgi için [Kerkenez seçenekleri](#kestrel-options) ve [ListenOptions.Protocols](#listenoptionsprotocols) bölümlerine bakın.

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Ters proxy ile Kerkenez ne zaman kullanılır

Kerkenez kendisi tarafından veya *ters proxy sunucusu*ile kullanılabilir , Internet Bilgi Hizmetleri gibi [(IIS)](https://www.iis.net/), [Nginx](https://nginx.org), veya [Apache](https://httpd.apache.org/). Ters proxy sunucusu ağdan HTTP isteklerini alır ve bunları Kestrel'e ileter.

Kerkenez bir kenar (Internet bakan) web sunucusu olarak kullanılır:

![Kerkenez ters proxy sunucusu olmadan internet ile doğrudan iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

Ters proxy yapılandırmasında kullanılan kerkenez:

![Kerkenez, IIS, Nginx veya Apache gibi ters proxy sunucusu aracılığıyla Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

Ters proxy sunucusu olan veya olmayan yapılandırma, desteklenen bir barındırma yapılandırmasıdır.

Ters proxy sunucusu olmayan bir kenar sunucusu olarak kullanılan kerkenez, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez. Kestrel bir bağlantı noktasında dinleyecek şekilde yapılandırıldığında, Kestrel isteklerin `Host` üstbilgilerinden bağımsız olarak o bağlantı noktasının tüm trafiğini işler. Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel'e iletme yeteneğine sahiptir.

Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucusu kullanmak iyi bir seçim olabilir.

Ters proxy:

* Barındırdığı uygulamaların açıkta kalan ortak yüzey alanını sınırlayabilir.
* Ek bir yapılandırma ve savunma katmanı sağlayın.
* Mevcut altyapı ile daha iyi entegre olabilir.
* Yük dengeleme ve güvenli iletişim (HTTPS) yapılandırması basitleştirin. Yalnızca ters proxy sunucusu bir X.509 sertifikası gerektirir ve bu sunucu apaçık HTTP kullanarak dahili ağdaki uygulamasunucularıyla iletişim kurabilir.

> [!WARNING]
> Ters proxy yapılandırmasında barındırma [ana bilgisayar filtreleme](#host-filtering)gerektirir.

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a>ASP.NET Core uygulamalarında Kerkenez nasıl kullanılır?

[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) paketi [Microsoft.AspNetCore.App metapaketinde](xref:fundamentals/metapackage-app)yer almaktadır.

ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır. Program.cs *Program.cs*, şablon kodu <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>aramaları <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> , hangi arka planda çağırır.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

Ana bilgisayar `CreateDefaultBuilder` hakkında daha fazla bilgi ve bina için <xref:fundamentals/host/web-host#set-up-a-host>ana bilgisayar bölümünü *ayarla'ya* bakın.

Aradıktan `CreateDefaultBuilder`sonra ek yapılandırma `ConfigureKestrel`sağlamak için , kullanın:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

Uygulama ana bilgisayarını `CreateDefaultBuilder` ayarlamak için aramazsa, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> aramadan **önce** arayın: `ConfigureKestrel`

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a>Kerkenez seçenekleri

Kestrel web sunucusu, özellikle Internet'e dönük dağıtımlarda kullanışlı olan kısıtlama yapılandırma seçeneklerine sahiptir.

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> Sınıfın <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> özelliği üzerindeki kısıtlamaları ayarlayın. Özellik `Limits` sınıfın bir örneğini <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> tutar.

Aşağıdaki <xref:Microsoft.AspNetCore.Server.Kestrel.Core> örneklerde ad alanı kullanılır:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

Aşağıdaki örneklerde C# koduyla yapılandırılan kerkenez seçenekleri de bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir. Örneğin, Dosya Yapılandırma Sağlayıcısı kestrel yapılandırmasını bir *appsettings.json* veya *appsettings'ten yükleyebilir.{ Çevre}.json* dosyası:

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

Aşağıdaki **yaklaşımlardan birini** kullanın:

* Kerkenezi `Startup.ConfigureServices`yapılandır:

  1. Sınıfa bir `IConfiguration` örnek `Startup` enjekte edin. Aşağıdaki örnek, enjekte edilen yapılandırmanın `Configuration` özelliğe atandığını varsayar.
  2. In `Startup.ConfigureServices`, `Kestrel` Kerkenez yapılandırma içine yapılandırma bölümü yükleyin:

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* Ev sahibini kurarken Kerkenezi yapılandırın:

  *Program.cs,* yapılandırma `Kestrel` bölümünü Kestrel'in yapılandırmasına yükleyin:

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

Önceki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.

### <a name="keep-alive-timeout"></a>Zaman ayarı tutma

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

Canlı kalma [zaman ını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar. Varsayılan olarak 2 dakika dır.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a>Maksimum istemci bağlantıları

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

Aşağıdaki kodla tüm uygulama için eşzamanlı açık TCP bağlantısı sayısı ayarlanabilir:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

HTTP veya HTTPS'den başka bir protokole yükseltilmiş bağlantılar için ayrı bir sınır vardır (örneğin, bir WebSockets isteğinde). Bir bağlantı yükseltildikten sonra, sınıra `MaxConcurrentConnections` göre sayılmaz.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

Maksimum bağlantı sayısı varsayılan olarak sınırsızdır (null).

### <a name="maximum-request-body-size"></a>Maksimum istek gövde boyutu

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

Varsayılan maksimum istek gövde boyutu yaklaşık 28,6 MB olan 30.000.000 bayttır.

Bir ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> önerilen yaklaşım, özniteliği bir eylem yönteminde kullanmaktır:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Aşağıda, her istekte uygulamanın kısıtlamasının nasıl yapılandırılabildiğini gösteren bir örnek verilmiştir:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

Ara yazılımda belirli bir istek üzerindeki ayarı geçersiz kılın:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Uygulama isteği okumaya başladıktan sonra uygulama istek sınırını yapılandırırsa bir özel durum atılır. Özelliğin `MaxRequestBodySize` salt `IsReadOnly` okunur durumda olup olmadığını gösteren bir özellik vardır, bu da sınırı yapılandırmak için çok geç olduğu anlamına gelir.

Bir uygulama [ASP.NET Çekirdek Modülü'nün](xref:host-and-deploy/aspnet-core-module)arkasında [işlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model) kaldığında, IIS sınırı zaten belirlediği için Kestrel'in istek gövde boyutu sınırı devre dışı bırakılır.

### <a name="minimum-request-body-data-rate"></a>Minimum istek gövde veri oranı

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kerkenez, verilerin bayt/saniye cinsinden belirtilen oranda gelişip ulaşmadığında her saniye kontrol eder. Oran minimumun altına düşerse, bağlantı zamanlanır. Yetkisiz kullanım süresi, Kestrel'in müşteriye gönderim oranını en aza kadar yükseltmesi için verdiği süredir; bu süre içinde fiyat kontrol edilir. Yetkisiz kullanım süresi, TCP'nin yavaş başlaması nedeniyle başlangıçta veri gönderen bağlantıların düşmesini önlemeye yardımcı olur.

Varsayılan minimum oran, 5 saniyelik yetkisiz kullanım süresine sahip 240 bayt/saniyedir.

Yanıt için de minimum ücret uygulanır. İstek sınırını ve yanıt sınırını ayarlamak için kod, `RequestBody` özellik `Response` ve arabirim adlarına sahip olmak veya bunlar dışında aynıdır.

Aşağıda, *Program.cs*minimum veri oranlarının nasıl yapılandırılabildiğini gösteren bir örnek verilmiştir:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

Ara yazılımda istek başına minimum ücret limitlerini geçersiz kılın:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

Önceki örnekte başvurulan oran özelliği `HttpContext.Features` nin hiçbiri HTTP/2 istekleri için bulunmaz, çünkü istek başına fiyat sınırlarının değiştirilmesi, protokolün istek çoklama desteği nedeniyle HTTP/2 için desteklenmez. Http/1.x ve `KestrelServerOptions.Limits` HTTP/2 bağlantıları için de sunucu genelinde yapılandırılan oran sınırları hala geçerlidir.

### <a name="request-headers-timeout"></a>Üstbilgi zaman alabı isteği

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Sunucunun istek üstbilgilerini almak için harcadığı maksimum süreyi alır veya ayarlar. Varsayılan olarak 30 saniye.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a>Bağlantı başına maksimum akış

`Http2.MaxStreamsPerConnection`http/2 bağlantısı başına eşzamanlı istek akışlarının sayısını sınırlar. Fazla akışreddedilir.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

Varsayılan değer 100’dür.

### <a name="header-table-size"></a>Üstbilgi tablo boyutu

HPACK kod çözücü http/2 bağlantıları için HTTP başlıkları decompresses. `Http2.HeaderTableSize`HPACK kod çözücünün kullandığı üstbilgi sıkıştırma tablosunun boyutunu sınırlar. Değer sekizli olarak sağlanır ve sıfırdan büyük olmalıdır (0).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

Varsayılan değer 4096'dır.

### <a name="maximum-frame-size"></a>Maksimum kare boyutu

`Http2.MaxFrameSize`almak için HTTP/2 bağlantı çerçevesi yükünün maksimum boyutunu gösterir. Değeri sekizli olarak sağlanır ve 2^14 (16.384) ile 2^24-1 (16.777.215) arasında olmalıdır.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

Varsayılan değer 2^14 (16.384) 'dir.

### <a name="maximum-request-header-size"></a>Maksimum istek üstbilgi boyutu

`Http2.MaxRequestHeaderFieldSize`istek üstbilgi değerlerinin sekizlisinde izin verilen maksimum boyutu gösterir. Bu sınır, sıkıştırılmış ve sıkıştırılmamış gösterimlerinde birlikte hem ad hem de değer için geçerlidir. Değer sıfırdan büyük olmalıdır (0).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

Varsayılan değer 8.192'dir.

### <a name="initial-connection-window-size"></a>İlk bağlantı penceresi boyutu

`Http2.InitialConnectionWindowSize`bağlantı başına tüm istekler (akışlar) arasında toplanan sunucu arabellekleri baytlar maksimum istek gövde verileri gösterir. İstekler de `Http2.InitialStreamWindowSize`. Değer 65.535'ten büyük veya eşit ve 2^31'den az olmalıdır (2,147,483,648).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

Varsayılan değer 128 KB (131.072) 'dir.

### <a name="initial-stream-window-size"></a>İlk akış penceresi boyutu

`Http2.InitialStreamWindowSize`istek başına sunucu arabelleklerini baytlar halinde ki maksimum istek gövdesi verilerini gösterir (akış). İstekler de `Http2.InitialStreamWindowSize`. Değer 65.535'ten büyük veya eşit ve 2^31'den az olmalıdır (2,147,483,648).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

Varsayılan değer 96 KB 'dir (98.304).

### <a name="synchronous-io"></a>Zaman Uyumlu G/Ç

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>istek ve yanıt için senkron G/Ç'ye izin verilip verilmediğini denetler. Varsayılan `true`değer.

> [!WARNING]
> Çok sayıda senkron G/Ç işlemini engelleme, iş parçacığı havuzunun açlığa yol açabilir ve bu da uygulamanın yanıt vermemesini sağlar. Yalnızca `AllowSynchronousIO` eşzamanlı G/Ç'yi desteklemeyen bir kitaplık kullanırken etkinleştirin.

Aşağıdaki örnek, senkron G/Ç'yi sağlar:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

Diğer Kerkenez seçenekleri ve sınırları hakkında bilgi için bkz:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>Bitiş noktası yapılandırması

Varsayılan olarak, ASP.NET Core bağlanır:

* `http://localhost:5000`
* `https://localhost:5001`(yerel bir geliştirme sertifikası varsa)

Aşağıdakileri kullanarak URL'leri belirtin:

* `ASPNETCORE_URLS`çevre değişkenidir.
* `--urls`komut satırı bağımsız değişkeni.
* `urls`ana bilgisayar yapılandırma anahtarı.
* `UseUrls`uzatma yöntemi.

Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS). Değeri yarı sütunlu ayrılmış bir liste olarak `"Urls": "http://localhost:8000;http://localhost:8001"`yapılandırın (örneğin, ).

Bu yaklaşımlar hakkında daha fazla bilgi için [Sunucu URL'leri](xref:fundamentals/host/web-host#server-urls) ve [Geçersiz Kılma yapılandırması'na](xref:fundamentals/host/web-host#override-configuration)bakın.

Geliştirme sertifikası oluşturulur:

* [.NET Core SDK](/dotnet/core/sdk) yüklendiğinde.
* [Dev-certs aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.

Bazı tarayıcılar, yerel geliştirme sertifikasına güvenmek için açık izin verilmesini gerektirir.

Proje şablonları uygulamaları varsayılan olarak HTTPS'de çalışacak şekilde yapılandırır ve [HTTPS yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.

Kestrel <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> için URL önekleri ve bağlantı noktalarını yapılandırmak için arama <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> veya <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> yöntemler.

`UseUrls`, `--urls` komut satırı bağımsız `urls` değişkeni, ana `ASPNETCORE_URLS` bilgisayar yapılandırma anahtarı ve ortam değişkeni de çalışır, ancak bu bölümde daha sonra belirtilen sınırlamalar vardır (varsayılan bir sertifika HTTPS bitiş noktası yapılandırması için kullanılabilir olmalıdır).

`KestrelServerOptions`Yapılandırma:

### <a name="configureendpointdefaultsactionlistenoptions"></a>YapılandırmaEndpointDefaults(Eylem\<DinlemeSeçenekleri>)

Belirtilen her bitiş `Action` noktası için çalışacak bir yapılandırma belirtir. Birden `ConfigureEndpointDefaults` çok kez `Action`arama, öncekilerin yerine geçen son `Action` belirtilenin yerini alır.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> Aramadan <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> **önce** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> arayarak oluşturulan uç noktalarda varsayılanlar uygulanmaz.

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>YapılandırmaHttpsDefaults(Eylem\<HttpsConnectionAdapterOptions>)

Her HTTPS bitiş `Action` noktası için çalışacak bir yapılandırma belirtir. Birden `ConfigureHttpsDefaults` çok kez `Action`arama, öncekilerin yerine geçen son `Action` belirtilenin yerini alır.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> Aramadan <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> **önce** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> arayarak oluşturulan uç noktalarda varsayılanlar uygulanmaz.


### <a name="configureiconfiguration"></a>Yapılandırma(IConfiguration)

Kestrel'i ayarlamak için bir yapılandırma yükleyicisi oluşturur ve bu <xref:Microsoft.Extensions.Configuration.IConfiguration> da girdi olarak alır. Yapılandırma Kerkenez için yapılandırma bölümüne kapsamı olmalıdır.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Kestrel'i HTTPS'yi kullanacak şekilde yapılandırın.

`ListenOptions.UseHttps`Uzantı -ları:

* `UseHttps`&ndash; Kestrel'i varsayılan sertifikayla HTTPS'yi kullanacak şekilde yapılandırın. Varsayılan sertifika yapılandırılmamışsa özel durum oluşturur.
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

`ListenOptions.UseHttps`Parametre:

* `filename`uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yol ve dosya adıdır.
* `password`X.509 sertifika verilerine erişmek için gereken paroladır.
* `configureOptions`bir `Action` yapılandırmak `HttpsConnectionAdapterOptions`için . ' `ListenOptions`yi döndürür.
* `storeName`sertifikayı yüklemek için sertifika deposudur.
* `subject`sertifikanın özne adıdır.
* `allowInvalid`kendi imzalı sertifikalar gibi geçersiz sertifikaların dikkate alınıp alınmaması gerektiğini gösterir.
* `location`sertifikayı yüklemek için mağaza konumudur.
* `serverCertificate`X.509 sertifikasıdır.

Üretimde, HTTPS açıkça yapılandırılmalıdır. En azından varsayılan sertifika sağlanmalıdır.

Sonraki açıklanan desteklenen yapılandırmalar:

* Yapılandırma yok
* Yapılandırmadan varsayılan sertifikayı değiştirme
* Koddaki varsayılanları değiştirme

*Yapılandırma yok*

Kerkenez üzerinde `http://localhost:5000` dinler `https://localhost:5001` ve (varsayılan bir sertifika varsa).

<a name="configuration"></a>

*Yapılandırmadan varsayılan sertifikayı değiştirme*

`CreateDefaultBuilder`kerkenez yapılandırmasını yüklemek için varsayılan olarak çağırır. `Configure(context.Configuration.GetSection("Kestrel"))` Kestrel için varsayılan bir HTTPS uygulama ayarları yapılandırma şeması kullanılabilir. Diskteki bir dosyadan veya bir sertifika deposundan kullanılacak URL'ler ve kullanılacak sertifikalar da dahil olmak üzere birden çok uç noktayı yapılandırın.

Aşağıdaki *appsettings.json* örnekte:

* Geçersiz sertifikaların `true` kullanımına izin vermek için **AllowInvalid'i** ayarlayın (örneğin, kendi imzalı sertifikalar).
* Sertifika belirtmeyen herhangi bir HTTPS bitiş noktası (Aşağıdaki örnekte**HttpsDefaultCert)** **Varsayılan** **Sertifikalar** > veya geliştirme sertifikası altında tanımlanan sertifikaya geri döner.

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

Herhangi bir sertifika düğümü için **Yol** ve **Parola** kullanmanın alternatifi, sertifika deposu alanlarını kullanarak sertifikayı belirtmektir. Örneğin, **Sertifikalar** > **Varsayılan** sertifika olarak belirtilebilir:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Şema notları:

* Uç nokta adları büyük/küçük harf duyarsızdır. Örneğin, `HTTPS` ve `Https` geçerlidir.
* Parametre `Url` her bitiş noktası için gereklidir. Bu parametrenin biçimi, tek bir değerle sınırlı olması dışında üst düzey `Urls` yapılandırma parametresi ile aynıdır.
* Bu uç noktalar, bunlara eklemek `Urls` yerine üst düzey yapılandırmada tanımlanan noktaların yerini alır. Kodda tanımlanan uç `Listen` noktalar, yapılandırma bölümünde tanımlanan uç noktalarla birlikte kümülatiftir.
* Bölüm `Certificate` isteğe bağlıdır. `Certificate` Bölüm belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır. Varsayılan yoksa, sunucu bir özel durum atar ve başlatılmaz.
* Bu `Certificate` bölüm hem **Yol**&ndash;**Parolası** hem de **Konu**&ndash;**Deposu** sertifikalarını destekler.
* Bağlantı noktası çakışmalarına neden olmadıkları sürece, herhangi bir uç nokta sayısı bu şekilde tanımlanabilir.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))`yapılandırılmış `KestrelConfigurationLoader` bir `.Endpoint(string name, listenOptions => { })` uç nokta nın ayarlarını tamamlamak için kullanılabilecek bir yöntemle döndürür:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

`KestrelServerOptions.ConfigurationLoader`tarafından sağlanan gibi varolan yükleyici üzerinde yinelemeye devam etmek için <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>doğrudan erişilebilir.

* Her bitiş noktası için yapılandırma bölümü, özel `Endpoint` ayarların okunabilmesi için yöntemdeki seçeneklerde kullanılabilir.
* Başka bir bölümle yeniden `options.Configure(context.Configuration.GetSection("{SECTION}"))` arayarak birden çok yapılandırma yüklenebilir. Önceki örneklerde açıkça çağrılmadığı sürece `Load` yalnızca son yapılandırma kullanılır. Metapackage, varsayılan yapılandırma `Load` bölümünün değiştirilebilecek şekilde çağrılmaz.
* `KestrelConfigurationLoader`api `Listen` ailesi aşırı yükler `KestrelServerOptions` `Endpoint` olarak aynalar, bu nedenle kod ve config uç noktaları aynı yerde yapılandırılabilir. Bu aşırı yüklemeler adlar kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları tüketir.

*Koddaki varsayılanları değiştirme*

`ConfigureEndpointDefaults`ve `ConfigureHttpsDefaults` önceki senaryoda belirtilen `ListenOptions` varsayılan `HttpsConnectionAdapterOptions`sertifikayı geçersiz kılmak da dahil olmak üzere varsayılan ayarları değiştirmek için kullanılabilir. `ConfigureEndpointDefaults`ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

*SNI için Kerkenez desteği*

[Sunucu Adı Göstergesi (SNI),](https://tools.ietf.org/html/rfc6066#section-3) aynı IP adresi ve bağlantı noktasında birden çok etki alanı barındırmak için kullanılabilir. SNI'nin çalışması için istemci, sunucunun doğru sertifikayı sağlayabilmesi için TLS el sıkışması sırasında güvenli oturumun ana bilgisayar adını sunucuya gönderir. İstemci, TLS el sıkışmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için verilen sertifikayı kullanır.

Kerkenez `ServerCertificateSelector` geri arama yoluyla SNI destekler. Geri arama, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesine izin vermek için bağlantı başına bir kez çağrılır.

SNI desteği şunları gerektirir:

* Hedef çerçevede `netcoreapp2.1` veya daha sonra çalışma. On `net461` veya daha sonra, geri arama `name` çağrılır ama her zaman `null`. `name` İstemci `null` TLS el sıkışmasında ana bilgisayar adı parametresini sağlamazsa da öyledir.
* Tüm web siteleri aynı Kerkenez örneğinde çalışır. Kestrel ters bir proxy olmadan birden çok örnek arasında bir IP adresi ve bağlantı noktası paylaşımı desteklemez.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="connection-logging"></a>Bağlantı günlüğü

Bağlantıdaki bayt düzeyinde iletişim için Hata Ayıklama düzeyi günlüklerini yaramak için arayın. <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> Bağlantı günlüğü, TLS şifreleme sırasında ve yakınlık ların arkasında olduğu gibi düşük düzeyli iletişimdeki sorun giderme sorunları için yararlıdır. Önce `UseConnectionLogging` `UseHttps`yerleştirilirse, şifreli trafik günlüğe kaydedilir. Sonra `UseConnectionLogging` `UseHttps`yerleştirilirse, şifresi çözülmüş trafik günlüğe kaydedilir.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a>TCP soketine bağlama

Yöntem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> bir TCP soketine bağlanır ve lambda seçeneği X.509 sertifika yapılandırmasına izin verir:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

Örnek, HTTPS'yi <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>bir bitiş noktası için . Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak için aynı API'yi kullanın.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Unix soketine bağlanma

Bu örnekte gösterildiği <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> gibi, Nginx ile geliştirilmiş performans için bir Unix soketinde dinleyin:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* Nginx sırdaşlık dosyasında, `server`  >  `location`  >  `proxy_pass` girişini `http://unix:/tmp/{KESTREL SOCKET}:/;`. `{KESTREL SOCKET}`verilen soketin adıdır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (örneğin, `kestrel-test.sock` önceki örnekte).
* Soketin Nginx tarafından yazılabilir olduğundan `chmod go+w /tmp/kestrel-test.sock`emin olun (örneğin, ). 

### <a name="port-0"></a>Bağlantı Noktası 0

Bağlantı noktası `0` numarası belirtildiğinde, Kerkenez dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır. Aşağıdaki örnek, Kestrel'in çalışma zamanında gerçekte hangi bağlantı noktasına bağlı olduğunu nasıl belirleyeceklerini gösterir:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Uygulama çalıştırıldığında, konsol penceresi çıkışı uygulamaya ulaşılabilen dinamik bağlantı noktasını gösterir:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>Sınırlamalar

Uç noktaları aşağıdaki yaklaşımlarla yapılandırın:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* `--urls`komut satırı bağımsız değişkeni
* `urls`ana bilgisayar yapılandırma anahtarı
* `ASPNETCORE_URLS`çevre değişkeni

Bu yöntemler, Kerkenez dışındaki sunucularla kod çalışması yapmak için yararlıdır. Ancak, aşağıdaki sınırlamalar dikkat edin:

* HTTPS bitiş noktası yapılandırmasında varsayılan bir sertifika sağlanmadıkça (örneğin, bu konuda `KestrelServerOptions` daha önce gösterildiği gibi yapılandırma veya yapılandırma dosyasını kullanmak gibi) HTTPS bu yaklaşımlarla kullanılamaz.
* Hem yaklaşımlar `Listen` `UseUrls` hem de yaklaşımlar aynı `Listen` anda kullanıldığında, `UseUrls` uç noktalar uç noktaları geçersiz kılar.

### <a name="iis-endpoint-configuration"></a>IIS uç nokta yapılandırması

IIS kullanırken, IIS geçersiz kılma bağlamaları için URL `Listen` `UseUrls`bağlamaları ya da . Daha fazla bilgi için [ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.

### <a name="listenoptionsprotocols"></a>ListenOptions.Protokoller

Özellik, `Protocols` bağlantı bitiş noktasında`HttpProtocols`veya sunucu için etkinleştirilmiş HTTP protokollerini ( ) kurar. `HttpProtocols` Enumdan `Protocols` özelliğe bir değer atayın.

| `HttpProtocols`enum değeri | Bağlantı protokolüne izin verilir |
| -------------------------- | ----------------------------- |
| `Http1`                    | Sadece HTTP/1.1. TLS ile veya TLS olmadan kullanılabilir. |
| `Http2`                    | Sadece HTTP/2. TLS olmadan kullanılabilir, ancak istemci [Bir Ön Bilgi modunu](https://tools.ietf.org/html/rfc7540#section-3.4)destekliyorsa. |
| `Http1AndHttp2`            | HTTP/1.1 ve HTTP/2. HTTP/2 tls ve [Uygulama Katmanı Protokolü Anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı gerektirir; aksi takdirde, bağlantı varsayılan OLARAK HTTP/1.1'e olur. |

Varsayılan protokol HTTP/1.1'dir.

HTTP/2 için TLS kısıtlamaları:

* TLS sürüm 1.2 veya sonrası
* Yeniden anlaşma devre dışı
* Sıkıştırma devre dışı
* Minimum geçici anahtar değişim boyutları:
  * Eliptik eğri Diffie-Hellman &lbrack;(ECDHE) [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; &ndash; 224 bit minimum
  * Sonlu alan Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; &ndash; 2048 bit minimum
* Şifreli paket kara listeye alınmadı

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack; &lbrack; `FIPS186` &rbrack; P-256 eliptik eğrisi ile varsayılan olarak desteklenir. `TLS-ECDHE` &rbrack;

Aşağıdaki örnek, 8000 no'l'daki porttaki HTTP/1.1 ve HTTP/2 bağlantılarına izin verir. Bağlantılar, sağlanan bir sertifika ile TLS tarafından güvence altına alınır:

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

İsteğe bağlı `IConnectionAdapter` olarak, TLS el sıkışmalarını belirli şifreler için bağlantı başına filtrelemek için bir uygulama oluşturun:

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

*Protokolü yapılandırmadan ayarlama*

<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>kerkenez yapılandırmasını yüklemek için varsayılan olarak çağırır. `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`

Aşağıdaki *appsettings.json* örneğinde, Kestrel'in tüm uç noktaları için varsayılan bağlantı protokolü (HTTP/1.1 ve HTTP/2) oluşturulmuştur:

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

Aşağıdaki yapılandırma dosyası örneği, belirli bir bitiş noktası için bir bağlantı protokolü kurar:

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

Kodda belirtilen protokoller yapılandırma tarafından ayarlanan değerleri geçersiz kılar.

## <a name="transport-configuration"></a>Aktarım yapılandırması

Core 2.1ASP.NET piyasaya sürülmesiyle, Kestrel'in varsayılan taşıması artık Libuv'a değil, yönetilen soketlere dayanmaktadır. Bu, çağrı yapan <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> ve aşağıdaki paketlerden herhangi birini oluşturan 2.1'e yükseltilen ASP.NET Core 2.0 uygulamaları için bir kırılma değişikliğidir:

* [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (doğrudan paket başvurusu)
* [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

Libuv kullanımını gerektiren projeler için:

* Uygulamanın proje dosyasına [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) paketine bağımlılık ekleyin:

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* Çağrı <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a>URL önekleri

URL `UseUrls`önekleri, komut `--urls` `urls` satırı bağımsız değişkeni, ana bilgisayar yapılandırma anahtarı veya `ASPNETCORE_URLS` ortam değişkeni kullanırken aşağıdaki biçimlerin herhangi birinde bulunabilir.

Yalnızca HTTP URL önekleri geçerlidir. Kestrel, URL bağlamalarını kullanarak `UseUrls`yapılandırırken HTTPS'yi desteklemez.

* Bağlantı noktası numarası olan IPv4 adresi

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0`tüm IPv4 adreslerine bağlanan özel bir durumdur.

* Bağlantı noktası numarası olan IPv6 adresi

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]`IPv4'ün `0.0.0.0`IPv6 eşdeğeridir.

* Bağlantı noktası numarası olan ana bilgisayar adı

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Ev sahibi `*`adları `+`ve özel değildir. Geçerli bir IP adresi olarak `localhost` tanınmayan veya tüm IPv4 ve IPv6 IP'lerine bağlanan her şey. Farklı ana bilgisayar adlarını aynı bağlantı noktasındaki farklı ASP.NET Core uygulamalarına bağlamak için [HTTP.sys](xref:fundamentals/servers/httpsys) veya IIS, Nginx veya Apache gibi ters proxy sunucusu kullanın.

  > [!WARNING]
  > Ters proxy yapılandırmasında barındırma [ana bilgisayar filtreleme](#host-filtering)gerektirir.

* Bağlantı `localhost` noktası numarası veya bağlantı noktası numarası olan loopback IP ile ana bilgisayar adı

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Belirtildiğinde, `localhost` Kerkenez hem IPv4 hem de IPv6 geri dönüş arabirimlerine bağlanmaya çalışır. İstenen bağlantı noktası her iki döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılmaz. Geri dönüş arabirimi başka bir nedenle kullanılamıyorsa (en sık IPv6 desteklenmediği için), Kestrel bir uyarı yı günlüğe kaydeder.

## <a name="host-filtering"></a>Ana bilgisayar filtresi

Kerkenez gibi öneklere dayalı yapılandırmayı `http://example.com:5000`desteklerken, Kerkenez büyük ölçüde ana bilgisayar adını yok sayar. Ana `localhost` bilgisayar, geri dönüş adreslerine bağlanmak için kullanılan özel bir durumdur. Açık bir IP adresi dışındaki tüm ana bilgisayar, tüm genel IP adreslerine bağlanır. `Host`üstbilgi doğrulanmadı.

Geçici çözüm olarak, Ana Bilgisayar Filtreleme Ara Ware'i kullanın. Ana Bilgisayar Filtreleme Middleware, [Microsoft.AspNetCore.App metapaketinde](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 veya 2.2) bulunan [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır. Middleware tarafından <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>eklenir , <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>hangi çağırır:

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Ana Bilgisayar Filtreleme Middleware varsayılan olarak devre dışı bırakılır. Ara yazılımı etkinleştirmek için `AllowedHosts` *appsettings.json*/uygulama ayarlarında bir anahtar*tanımlayın.\< EnvironmentName>.json*. Değer, bağlantı noktası numarası olmayan ana bilgisayar adlarının yarı sütunlu sınırlı bir listesidir:

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [İlezli Üstbilgi Middleware](xref:host-and-deploy/proxy-load-balancer) de bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçenek vardır. İletilenmiş Üstbilgi Middleware ve Ana Bilgisayar Filtreleme Middleware farklı senaryolar için benzer işlevsellik var. Üstbilgi nin ters proxy sunucusu veya `Host` yük dengeleyicisi ilerlerken üstbilgi korunmadığında Iletili Üstbilgi Middleware ile ayarlama `AllowedHosts` uygundur. Kerkenez genel kullanıma dönük kenar sunucusu olarak kullanıldığında veya `AllowedHosts` `Host` üstbilgi doğrudan iledildiğinde Ana Bilgisayar Filtreleme Middleware ile ayarlama uygundur.
>
> İlliyeli Üstbilgiler Middleware hakkında <xref:host-and-deploy/proxy-load-balancer>daha fazla bilgi için bkz.

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Kerkenez core ASP.NET [için](xref:fundamentals/servers/index)bir çapraz platform web sunucusudur. Kerkenez, ASP.NET Core proje şablonlarında varsayılan olarak bulunan web sunucusudur.

Kerkenez aşağıdaki senaryoları destekler:

* HTTPS
* [WebSockets'i](https://github.com/aspnet/websockets) etkinleştirmek için kullanılan opak yükseltme
* Nginx arkasında yüksek performans için Unix soketleri

Kerkenez, .NET Core'un desteklediği tüm platformlarda ve sürümlerde desteklenir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Ters proxy ile Kerkenez ne zaman kullanılır

Kerkenez kendisi tarafından veya *ters proxy sunucusu*ile kullanılabilir , Internet Bilgi Hizmetleri gibi [(IIS)](https://www.iis.net/), [Nginx](https://nginx.org), veya [Apache](https://httpd.apache.org/). Ters proxy sunucusu ağdan HTTP isteklerini alır ve bunları Kestrel'e ileter.

Kerkenez bir kenar (Internet bakan) web sunucusu olarak kullanılır:

![Kerkenez ters proxy sunucusu olmadan internet ile doğrudan iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

Ters proxy yapılandırmasında kullanılan kerkenez:

![Kerkenez, IIS, Nginx veya Apache gibi ters proxy sunucusu aracılığıyla Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

Ters proxy sunucusu olan veya olmayan yapılandırma, desteklenen bir barındırma yapılandırmasıdır.

Ters proxy sunucusu olmayan bir kenar sunucusu olarak kullanılan kerkenez, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez. Kestrel bir bağlantı noktasında dinleyecek şekilde yapılandırıldığında, Kestrel isteklerin `Host` üstbilgilerinden bağımsız olarak o bağlantı noktasının tüm trafiğini işler. Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel'e iletme yeteneğine sahiptir.

Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucusu kullanmak iyi bir seçim olabilir.

Ters proxy:

* Barındırdığı uygulamaların açıkta kalan ortak yüzey alanını sınırlayabilir.
* Ek bir yapılandırma ve savunma katmanı sağlayın.
* Mevcut altyapı ile daha iyi entegre olabilir.
* Yük dengeleme ve güvenli iletişim (HTTPS) yapılandırması basitleştirin. Yalnızca ters proxy sunucusu bir X.509 sertifikası gerektirir ve bu sunucu apaçık HTTP kullanarak dahili ağdaki uygulamasunucularıyla iletişim kurabilir.

> [!WARNING]
> Ters proxy yapılandırmasında barındırma [ana bilgisayar filtreleme](#host-filtering)gerektirir.

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a>ASP.NET Core uygulamalarında Kerkenez nasıl kullanılır?

[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) paketi [Microsoft.AspNetCore.App metapaketinde](xref:fundamentals/metapackage-app)yer almaktadır.

ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır. Program.cs *Program.cs*, şablon kodu <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>aramaları <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> , hangi arka planda çağırır.

Aradıktan `CreateDefaultBuilder`sonra ek yapılandırma <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>sağlamak için , arayın:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

Ana bilgisayar `CreateDefaultBuilder` hakkında daha fazla bilgi ve bina için <xref:fundamentals/host/web-host#set-up-a-host>ana bilgisayar bölümünü *ayarla'ya* bakın.

## <a name="kestrel-options"></a>Kerkenez seçenekleri

Kestrel web sunucusu, özellikle Internet'e dönük dağıtımlarda kullanışlı olan kısıtlama yapılandırma seçeneklerine sahiptir.

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> Sınıfın <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> özelliği üzerindeki kısıtlamaları ayarlayın. Özellik `Limits` sınıfın bir örneğini <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> tutar.

Aşağıdaki <xref:Microsoft.AspNetCore.Server.Kestrel.Core> örneklerde ad alanı kullanılır:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

Aşağıdaki örneklerde C# koduyla yapılandırılan kerkenez seçenekleri de bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir. Örneğin, Dosya Yapılandırma Sağlayıcısı kestrel yapılandırmasını bir *appsettings.json* veya *appsettings'ten yükleyebilir.{ Çevre}.json* dosyası:

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

Aşağıdaki **yaklaşımlardan birini** kullanın:

* Kerkenezi `Startup.ConfigureServices`yapılandır:

  1. Sınıfa bir `IConfiguration` örnek `Startup` enjekte edin. Aşağıdaki örnek, enjekte edilen yapılandırmanın `Configuration` özelliğe atandığını varsayar.
  2. In `Startup.ConfigureServices`, `Kestrel` Kerkenez yapılandırma içine yapılandırma bölümü yükleyin:

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* Ev sahibini kurarken Kerkenezi yapılandırın:

  *Program.cs,* yapılandırma `Kestrel` bölümünü Kestrel'in yapılandırmasına yükleyin:

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

Önceki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.

### <a name="keep-alive-timeout"></a>Zaman ayarı tutma

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

Canlı kalma [zaman ını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar. Varsayılan olarak 2 dakika dır.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a>Maksimum istemci bağlantıları

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

Aşağıdaki kodla tüm uygulama için eşzamanlı açık TCP bağlantısı sayısı ayarlanabilir:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

HTTP veya HTTPS'den başka bir protokole yükseltilmiş bağlantılar için ayrı bir sınır vardır (örneğin, bir WebSockets isteğinde). Bir bağlantı yükseltildikten sonra, sınıra `MaxConcurrentConnections` göre sayılmaz.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

Maksimum bağlantı sayısı varsayılan olarak sınırsızdır (null).

### <a name="maximum-request-body-size"></a>Maksimum istek gövde boyutu

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

Varsayılan maksimum istek gövde boyutu yaklaşık 28,6 MB olan 30.000.000 bayttır.

Bir ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> önerilen yaklaşım, özniteliği bir eylem yönteminde kullanmaktır:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Aşağıda, her istekte uygulamanın kısıtlamasının nasıl yapılandırılabildiğini gösteren bir örnek verilmiştir:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

Ara yazılımda belirli bir istek üzerindeki ayarı geçersiz kılın:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Uygulama isteği okumaya başladıktan sonra uygulama istek sınırını yapılandırırsa bir özel durum atılır. Özelliğin `MaxRequestBodySize` salt `IsReadOnly` okunur durumda olup olmadığını gösteren bir özellik vardır, bu da sınırı yapılandırmak için çok geç olduğu anlamına gelir.

Bir uygulama [ASP.NET Çekirdek Modülü'nün](xref:host-and-deploy/aspnet-core-module)arkasında [işlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model) kaldığında, IIS sınırı zaten belirlediği için Kestrel'in istek gövde boyutu sınırı devre dışı bırakılır.

### <a name="minimum-request-body-data-rate"></a>Minimum istek gövde veri oranı

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kerkenez, verilerin bayt/saniye cinsinden belirtilen oranda gelişip ulaşmadığında her saniye kontrol eder. Oran minimumun altına düşerse, bağlantı zamanlanır. Yetkisiz kullanım süresi, Kestrel'in müşteriye gönderim oranını en aza kadar yükseltmesi için verdiği süredir; bu süre içinde fiyat kontrol edilir. Yetkisiz kullanım süresi, TCP'nin yavaş başlaması nedeniyle başlangıçta veri gönderen bağlantıların düşmesini önlemeye yardımcı olur.

Varsayılan minimum oran, 5 saniyelik yetkisiz kullanım süresine sahip 240 bayt/saniyedir.

Yanıt için de minimum ücret uygulanır. İstek sınırını ve yanıt sınırını ayarlamak için kod, `RequestBody` özellik `Response` ve arabirim adlarına sahip olmak veya bunlar dışında aynıdır.

Aşağıda, *Program.cs*minimum veri oranlarının nasıl yapılandırılabildiğini gösteren bir örnek verilmiştir:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a>Üstbilgi zaman alabı isteği

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Sunucunun istek üstbilgilerini almak için harcadığı maksimum süreyi alır veya ayarlar. Varsayılan olarak 30 saniye.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a>Zaman Uyumlu G/Ç

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>istek ve yanıt için senkron G/Ç'ye izin verilip verilmediğini denetler. Varsayılan `true`değer.

> [!WARNING]
> Çok sayıda senkron G/Ç işlemini engelleme, iş parçacığı havuzunun açlığa yol açabilir ve bu da uygulamanın yanıt vermemesini sağlar. Yalnızca `AllowSynchronousIO` eşzamanlı G/Ç'yi desteklemeyen bir kitaplık kullanırken etkinleştirin.

Aşağıdaki örnek senkron G/Ç'yi devre dışı kılabilir:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

Diğer Kerkenez seçenekleri ve sınırları hakkında bilgi için bkz:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>Bitiş noktası yapılandırması

Varsayılan olarak, ASP.NET Core bağlanır:

* `http://localhost:5000`
* `https://localhost:5001`(yerel bir geliştirme sertifikası varsa)

Aşağıdakileri kullanarak URL'leri belirtin:

* `ASPNETCORE_URLS`çevre değişkenidir.
* `--urls`komut satırı bağımsız değişkeni.
* `urls`ana bilgisayar yapılandırma anahtarı.
* `UseUrls`uzatma yöntemi.

Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS). Değeri yarı sütunlu ayrılmış bir liste olarak `"Urls": "http://localhost:8000;http://localhost:8001"`yapılandırın (örneğin, ).

Bu yaklaşımlar hakkında daha fazla bilgi için [Sunucu URL'leri](xref:fundamentals/host/web-host#server-urls) ve [Geçersiz Kılma yapılandırması'na](xref:fundamentals/host/web-host#override-configuration)bakın.

Geliştirme sertifikası oluşturulur:

* [.NET Core SDK](/dotnet/core/sdk) yüklendiğinde.
* [Dev-certs aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.

Bazı tarayıcılar, yerel geliştirme sertifikasına güvenmek için açık izin verilmesini gerektirir.

Proje şablonları uygulamaları varsayılan olarak HTTPS'de çalışacak şekilde yapılandırır ve [HTTPS yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.

Kestrel <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> için URL önekleri ve bağlantı noktalarını yapılandırmak için arama <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> veya <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> yöntemler.

`UseUrls`, `--urls` komut satırı bağımsız `urls` değişkeni, ana `ASPNETCORE_URLS` bilgisayar yapılandırma anahtarı ve ortam değişkeni de çalışır, ancak bu bölümde daha sonra belirtilen sınırlamalar vardır (varsayılan bir sertifika HTTPS bitiş noktası yapılandırması için kullanılabilir olmalıdır).

`KestrelServerOptions`Yapılandırma:

### <a name="configureendpointdefaultsactionlistenoptions"></a>YapılandırmaEndpointDefaults(Eylem\<DinlemeSeçenekleri>)

Belirtilen her bitiş `Action` noktası için çalışacak bir yapılandırma belirtir. Birden `ConfigureEndpointDefaults` çok kez `Action`arama, öncekilerin yerine geçen son `Action` belirtilenin yerini alır.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> Aramadan <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> **önce** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> arayarak oluşturulan uç noktalarda varsayılanlar uygulanmaz.

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>YapılandırmaHttpsDefaults(Eylem\<HttpsConnectionAdapterOptions>)

Her HTTPS bitiş `Action` noktası için çalışacak bir yapılandırma belirtir. Birden `ConfigureHttpsDefaults` çok kez `Action`arama, öncekilerin yerine geçen son `Action` belirtilenin yerini alır.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> Aramadan <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> **önce** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> arayarak oluşturulan uç noktalarda varsayılanlar uygulanmaz.

### <a name="configureiconfiguration"></a>Yapılandırma(IConfiguration)

Kestrel'i ayarlamak için bir yapılandırma yükleyicisi oluşturur ve bu <xref:Microsoft.Extensions.Configuration.IConfiguration> da girdi olarak alır. Yapılandırma Kerkenez için yapılandırma bölümüne kapsamı olmalıdır.

### <a name="listenoptionsusehttps"></a>ListenOptions.UseHttps

Kestrel'i HTTPS'yi kullanacak şekilde yapılandırın.

`ListenOptions.UseHttps`Uzantı -ları:

* `UseHttps`&ndash; Kestrel'i varsayılan sertifikayla HTTPS'yi kullanacak şekilde yapılandırın. Varsayılan sertifika yapılandırılmamışsa özel durum oluşturur.
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

`ListenOptions.UseHttps`Parametre:

* `filename`uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yol ve dosya adıdır.
* `password`X.509 sertifika verilerine erişmek için gereken paroladır.
* `configureOptions`bir `Action` yapılandırmak `HttpsConnectionAdapterOptions`için . ' `ListenOptions`yi döndürür.
* `storeName`sertifikayı yüklemek için sertifika deposudur.
* `subject`sertifikanın özne adıdır.
* `allowInvalid`kendi imzalı sertifikalar gibi geçersiz sertifikaların dikkate alınıp alınmaması gerektiğini gösterir.
* `location`sertifikayı yüklemek için mağaza konumudur.
* `serverCertificate`X.509 sertifikasıdır.

Üretimde, HTTPS açıkça yapılandırılmalıdır. En azından varsayılan sertifika sağlanmalıdır.

Sonraki açıklanan desteklenen yapılandırmalar:

* Yapılandırma yok
* Yapılandırmadan varsayılan sertifikayı değiştirme
* Koddaki varsayılanları değiştirme

*Yapılandırma yok*

Kerkenez üzerinde `http://localhost:5000` dinler `https://localhost:5001` ve (varsayılan bir sertifika varsa).

<a name="configuration"></a>

*Yapılandırmadan varsayılan sertifikayı değiştirme*

`CreateDefaultBuilder`kerkenez yapılandırmasını yüklemek için varsayılan olarak çağırır. `Configure(context.Configuration.GetSection("Kestrel"))` Kestrel için varsayılan bir HTTPS uygulama ayarları yapılandırma şeması kullanılabilir. Diskteki bir dosyadan veya bir sertifika deposundan kullanılacak URL'ler ve kullanılacak sertifikalar da dahil olmak üzere birden çok uç noktayı yapılandırın.

Aşağıdaki *appsettings.json* örnekte:

* Geçersiz sertifikaların `true` kullanımına izin vermek için **AllowInvalid'i** ayarlayın (örneğin, kendi imzalı sertifikalar).
* Sertifika belirtmeyen herhangi bir HTTPS bitiş noktası (Aşağıdaki örnekte**HttpsDefaultCert)** **Varsayılan** **Sertifikalar** > veya geliştirme sertifikası altında tanımlanan sertifikaya geri döner.

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

Herhangi bir sertifika düğümü için **Yol** ve **Parola** kullanmanın alternatifi, sertifika deposu alanlarını kullanarak sertifikayı belirtmektir. Örneğin, **Sertifikalar** > **Varsayılan** sertifika olarak belirtilebilir:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Şema notları:

* Uç nokta adları büyük/küçük harf duyarsızdır. Örneğin, `HTTPS` ve `Https` geçerlidir.
* Parametre `Url` her bitiş noktası için gereklidir. Bu parametrenin biçimi, tek bir değerle sınırlı olması dışında üst düzey `Urls` yapılandırma parametresi ile aynıdır.
* Bu uç noktalar, bunlara eklemek `Urls` yerine üst düzey yapılandırmada tanımlanan noktaların yerini alır. Kodda tanımlanan uç `Listen` noktalar, yapılandırma bölümünde tanımlanan uç noktalarla birlikte kümülatiftir.
* Bölüm `Certificate` isteğe bağlıdır. `Certificate` Bölüm belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır. Varsayılan yoksa, sunucu bir özel durum atar ve başlatılmaz.
* Bu `Certificate` bölüm hem **Yol**&ndash;**Parolası** hem de **Konu**&ndash;**Deposu** sertifikalarını destekler.
* Bağlantı noktası çakışmalarına neden olmadıkları sürece, herhangi bir uç nokta sayısı bu şekilde tanımlanabilir.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))`yapılandırılmış `KestrelConfigurationLoader` bir `.Endpoint(string name, listenOptions => { })` uç nokta nın ayarlarını tamamlamak için kullanılabilecek bir yöntemle döndürür:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

`KestrelServerOptions.ConfigurationLoader`tarafından sağlanan gibi varolan yükleyici üzerinde yinelemeye devam etmek için <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>doğrudan erişilebilir.

* Her bitiş noktası için yapılandırma bölümü, özel `Endpoint` ayarların okunabilmesi için yöntemdeki seçeneklerde kullanılabilir.
* Başka bir bölümle yeniden `options.Configure(context.Configuration.GetSection("{SECTION}"))` arayarak birden çok yapılandırma yüklenebilir. Önceki örneklerde açıkça çağrılmadığı sürece `Load` yalnızca son yapılandırma kullanılır. Metapackage, varsayılan yapılandırma `Load` bölümünün değiştirilebilecek şekilde çağrılmaz.
* `KestrelConfigurationLoader`api `Listen` ailesi aşırı yükler `KestrelServerOptions` `Endpoint` olarak aynalar, bu nedenle kod ve config uç noktaları aynı yerde yapılandırılabilir. Bu aşırı yüklemeler adlar kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları tüketir.

*Koddaki varsayılanları değiştirme*

`ConfigureEndpointDefaults`ve `ConfigureHttpsDefaults` önceki senaryoda belirtilen `ListenOptions` varsayılan `HttpsConnectionAdapterOptions`sertifikayı geçersiz kılmak da dahil olmak üzere varsayılan ayarları değiştirmek için kullanılabilir. `ConfigureEndpointDefaults`ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

*SNI için Kerkenez desteği*

[Sunucu Adı Göstergesi (SNI),](https://tools.ietf.org/html/rfc6066#section-3) aynı IP adresi ve bağlantı noktasında birden çok etki alanı barındırmak için kullanılabilir. SNI'nin çalışması için istemci, sunucunun doğru sertifikayı sağlayabilmesi için TLS el sıkışması sırasında güvenli oturumun ana bilgisayar adını sunucuya gönderir. İstemci, TLS el sıkışmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için verilen sertifikayı kullanır.

Kerkenez `ServerCertificateSelector` geri arama yoluyla SNI destekler. Geri arama, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesine izin vermek için bağlantı başına bir kez çağrılır.

SNI desteği şunları gerektirir:

* Hedef çerçevede `netcoreapp2.1` veya daha sonra çalışma. On `net461` veya daha sonra, geri arama `name` çağrılır ama her zaman `null`. `name` İstemci `null` TLS el sıkışmasında ana bilgisayar adı parametresini sağlamazsa da öyledir.
* Tüm web siteleri aynı Kerkenez örneğinde çalışır. Kestrel ters bir proxy olmadan birden çok örnek arasında bir IP adresi ve bağlantı noktası paylaşımı desteklemez.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="connection-logging"></a>Bağlantı günlüğü

Bağlantıdaki bayt düzeyinde iletişim için Hata Ayıklama düzeyi günlüklerini yaramak için arayın. <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> Bağlantı günlüğü, TLS şifreleme sırasında ve yakınlık ların arkasında olduğu gibi düşük düzeyli iletişimdeki sorun giderme sorunları için yararlıdır. Önce `UseConnectionLogging` `UseHttps`yerleştirilirse, şifreli trafik günlüğe kaydedilir. Sonra `UseConnectionLogging` `UseHttps`yerleştirilirse, şifresi çözülmüş trafik günlüğe kaydedilir.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a>TCP soketine bağlama

Yöntem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> bir TCP soketine bağlanır ve lambda seçeneği X.509 sertifika yapılandırmasına izin verir:

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

Örnek, HTTPS'yi <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>bir bitiş noktası için . Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak için aynı API'yi kullanın.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>Unix soketine bağlanma

Bu örnekte gösterildiği <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> gibi, Nginx ile geliştirilmiş performans için bir Unix soketinde dinleyin:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

* Nginx sırdaşlık dosyasında, `server`  >  `location`  >  `proxy_pass` girişini `http://unix:/tmp/{KESTREL SOCKET}:/;`. `{KESTREL SOCKET}`verilen soketin adıdır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (örneğin, `kestrel-test.sock` önceki örnekte).
* Soketin Nginx tarafından yazılabilir olduğundan `chmod go+w /tmp/kestrel-test.sock`emin olun (örneğin, ). 

### <a name="port-0"></a>Bağlantı Noktası 0

Bağlantı noktası `0` numarası belirtildiğinde, Kerkenez dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır. Aşağıdaki örnek, Kestrel'in çalışma zamanında gerçekte hangi bağlantı noktasına bağlı olduğunu nasıl belirleyeceklerini gösterir:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Uygulama çalıştırıldığında, konsol penceresi çıkışı uygulamaya ulaşılabilen dinamik bağlantı noktasını gösterir:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>Sınırlamalar

Uç noktaları aşağıdaki yaklaşımlarla yapılandırın:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* `--urls`komut satırı bağımsız değişkeni
* `urls`ana bilgisayar yapılandırma anahtarı
* `ASPNETCORE_URLS`çevre değişkeni

Bu yöntemler, Kerkenez dışındaki sunucularla kod çalışması yapmak için yararlıdır. Ancak, aşağıdaki sınırlamalar dikkat edin:

* HTTPS bitiş noktası yapılandırmasında varsayılan bir sertifika sağlanmadıkça (örneğin, bu konuda `KestrelServerOptions` daha önce gösterildiği gibi yapılandırma veya yapılandırma dosyasını kullanmak gibi) HTTPS bu yaklaşımlarla kullanılamaz.
* Hem yaklaşımlar `Listen` `UseUrls` hem de yaklaşımlar aynı `Listen` anda kullanıldığında, `UseUrls` uç noktalar uç noktaları geçersiz kılar.

### <a name="iis-endpoint-configuration"></a>IIS uç nokta yapılandırması

IIS kullanırken, IIS geçersiz kılma bağlamaları için URL `Listen` `UseUrls`bağlamaları ya da . Daha fazla bilgi için [ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.

## <a name="transport-configuration"></a>Aktarım yapılandırması

Core 2.1ASP.NET piyasaya sürülmesiyle, Kestrel'in varsayılan taşıması artık Libuv'a değil, yönetilen soketlere dayanmaktadır. Bu, çağrı yapan <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> ve aşağıdaki paketlerden herhangi birini oluşturan 2.1'e yükseltilen ASP.NET Core 2.0 uygulamaları için bir kırılma değişikliğidir:

* [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (doğrudan paket başvurusu)
* [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

Libuv kullanımını gerektiren projeler için:

* Uygulamanın proje dosyasına [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) paketine bağımlılık ekleyin:

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* Çağrı <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a>URL önekleri

URL `UseUrls`önekleri, komut `--urls` `urls` satırı bağımsız değişkeni, ana bilgisayar yapılandırma anahtarı veya `ASPNETCORE_URLS` ortam değişkeni kullanırken aşağıdaki biçimlerin herhangi birinde bulunabilir.

Yalnızca HTTP URL önekleri geçerlidir. Kestrel, URL bağlamalarını kullanarak `UseUrls`yapılandırırken HTTPS'yi desteklemez.

* Bağlantı noktası numarası olan IPv4 adresi

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0`tüm IPv4 adreslerine bağlanan özel bir durumdur.

* Bağlantı noktası numarası olan IPv6 adresi

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]`IPv4'ün `0.0.0.0`IPv6 eşdeğeridir.

* Bağlantı noktası numarası olan ana bilgisayar adı

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  Ev sahibi `*`adları `+`ve özel değildir. Geçerli bir IP adresi olarak `localhost` tanınmayan veya tüm IPv4 ve IPv6 IP'lerine bağlanan her şey. Farklı ana bilgisayar adlarını aynı bağlantı noktasındaki farklı ASP.NET Core uygulamalarına bağlamak için [HTTP.sys](xref:fundamentals/servers/httpsys) veya IIS, Nginx veya Apache gibi ters proxy sunucusu kullanın.

  > [!WARNING]
  > Ters proxy yapılandırmasında barındırma [ana bilgisayar filtreleme](#host-filtering)gerektirir.

* Bağlantı `localhost` noktası numarası veya bağlantı noktası numarası olan loopback IP ile ana bilgisayar adı

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  Belirtildiğinde, `localhost` Kerkenez hem IPv4 hem de IPv6 geri dönüş arabirimlerine bağlanmaya çalışır. İstenen bağlantı noktası her iki döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılmaz. Geri dönüş arabirimi başka bir nedenle kullanılamıyorsa (en sık IPv6 desteklenmediği için), Kestrel bir uyarı yı günlüğe kaydeder.

## <a name="host-filtering"></a>Ana bilgisayar filtresi

Kerkenez gibi öneklere dayalı yapılandırmayı `http://example.com:5000`desteklerken, Kerkenez büyük ölçüde ana bilgisayar adını yok sayar. Ana `localhost` bilgisayar, geri dönüş adreslerine bağlanmak için kullanılan özel bir durumdur. Açık bir IP adresi dışındaki tüm ana bilgisayar, tüm genel IP adreslerine bağlanır. `Host`üstbilgi doğrulanmadı.

Geçici çözüm olarak, Ana Bilgisayar Filtreleme Ara Ware'i kullanın. Ana Bilgisayar Filtreleme Middleware, [Microsoft.AspNetCore.App metapaketinde](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 veya 2.2) bulunan [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır. Middleware tarafından <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>eklenir , <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>hangi çağırır:

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Ana Bilgisayar Filtreleme Middleware varsayılan olarak devre dışı bırakılır. Ara yazılımı etkinleştirmek için `AllowedHosts` *appsettings.json*/uygulama ayarlarında bir anahtar*tanımlayın.\< EnvironmentName>.json*. Değer, bağlantı noktası numarası olmayan ana bilgisayar adlarının yarı sütunlu sınırlı bir listesidir:

*appsettings.json*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [İlezli Üstbilgi Middleware](xref:host-and-deploy/proxy-load-balancer) de bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçenek vardır. İletilenmiş Üstbilgi Middleware ve Ana Bilgisayar Filtreleme Middleware farklı senaryolar için benzer işlevsellik var. Üstbilgi nin ters proxy sunucusu veya `Host` yük dengeleyicisi ilerlerken üstbilgi korunmadığında Iletili Üstbilgi Middleware ile ayarlama `AllowedHosts` uygundur. Kerkenez genel kullanıma dönük kenar sunucusu olarak kullanıldığında veya `AllowedHosts` `Host` üstbilgi doğrudan iledildiğinde Ana Bilgisayar Filtreleme Middleware ile ayarlama uygundur.
>
> İlliyeli Üstbilgiler Middleware hakkında <xref:host-and-deploy/proxy-load-balancer>daha fazla bilgi için bkz.

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* Linux'ta UNIX soketleri kullanırken, priz uygulama kapatıldığınızda otomatik olarak silinmez. Daha fazla bilgi için [bu GitHub sorununa](https://github.com/dotnet/aspnetcore/issues/14134)bakın.
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [RFC 7230: İleti Sözdizimi ve Yönlendirme (Bölüm 5.4: Ana Bilgisayar)](https://tools.ietf.org/html/rfc7230#section-5.4)
