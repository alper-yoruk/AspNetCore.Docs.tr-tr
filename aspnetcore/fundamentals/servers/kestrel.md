---
title: ASP.NET Core Web sunucusu uygulamasını Kestrel
author: rick-anderson
description: ASP.NET Core platformlar arası Web sunucusu olan Kestrel hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/servers/kestrel
ms.openlocfilehash: cd05aabb7b8ce5c7d30af881228ef2dab34f2592
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776454"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a>ASP.NET Core Web sunucusu uygulamasını Kestrel

[Tom Dykstra](https://github.com/tdykstra), [Chris](https://github.com/Tratcher), ve [Stephen halter](https://twitter.com/halter73) tarafından

::: moniker range=">= aspnetcore-3.0"

Kestrel, ASP.NET Core için platformlar arası [Web sunucusudur](xref:fundamentals/servers/index). Kestrel, ASP.NET Core proje şablonlarında varsayılan olarak bulunan Web sunucusudur.

Kestrel aşağıdaki senaryoları destekler:

* HTTPS
* [WebSockets](https://github.com/aspnet/websockets) 'i etkinleştirmek için kullanılan donuk yükseltme
* NGINX 'in arkasında yüksek performans için UNIX Yuvaları
* HTTP/2 (macOS&dagger;hariç)

&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.

Kestrel, .NET Core 'un desteklediği tüm platformlarda ve sürümlerde desteklenir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="http2-support"></a>HTTP/2 desteği

Aşağıdaki temel gereksinimler karşılanıyorsa, [http/2](https://httpwg.org/specs/rfc7540.html) ASP.NET Core uygulamalar için kullanılabilir:

* İşletim sistemi&dagger;
  * Windows Server 2016/Windows 10 veya üzeri&Dagger;
  * OpenSSL 1.0.2 veya üzerini içeren Linux (örneğin, Ubuntu 16,04 veya üzeri)
* Hedef Framework: .NET Core 2,2 veya üzeri
* [Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı
* TLS 1,2 veya üzeri bağlantı

&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.
&Dagger;Kestrel, Windows Server 2012 R2 ve Windows 8.1 'de HTTP/2 için sınırlı destek içerir. Bu işletim sistemlerinde kullanılabilir olan desteklenen TLS şifre paketlerinin listesi sınırlı olduğundan destek sınırlıdır. TLS bağlantılarının güvenliğini sağlamak için Eliptik Eğri dijital Imza algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekli olabilir.

Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2`.

HTTP/2 varsayılan olarak devre dışıdır. Yapılandırma hakkında daha fazla bilgi için [Kestrel Options](#kestrel-options) ve [Listenoptions. Protocols](#listenoptionsprotocols) bölümlerine bakın.

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Ters ara sunucu ile Kestrel ne zaman kullanılır?

Kestrel, kendisi veya [Internet Information Services (IIS)](https://www.iis.net/), [NGINX](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi bir *ters ara sunucu*ile kullanılabilir. Ters proxy sunucusu, ağdan gelen HTTP isteklerini alır ve Kestrel 'e iletir.

Sınır (Internet 'e yönelik) Web sunucusu olarak kullanılan Kestrel:

![Kestrel, ters proxy sunucusu olmadan doğrudan Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

Ters Proxy yapılandırmasında kullanılan Kestrel:

![Kestrel, IIS, NGINX veya Apache gibi bir ters ara sunucu üzerinden Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

İki yapılandırma de, ters ara sunucu sunucusuyla veya olmadan, desteklenen bir barındırma yapılandırması.

Ters proxy sunucusu olmayan bir uç sunucu olarak kullanılan Kestrel, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez. Kestrel bir bağlantı noktasını dinlemek üzere yapılandırıldığında, Kestrel isteklerin `Host` üst bilgilerinden bağımsız olarak bu bağlantı noktası için tüm trafiği işler. Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel 'e iletme yeteneğine sahiptir.

Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucu kullanılması iyi bir seçim olabilir.

Ters proxy:

* , Barındırdığı uygulamaların açığa çıkarılan genel yüzey alanını sınırlayabilir.
* Ek bir yapılandırma ve savunma katmanı sağlayın.
* , Mevcut altyapıyla daha iyi tümleşebilir.
* Yük dengelemeyi ve güvenli iletişim (HTTPS) yapılandırmasını kolaylaştırın. Yalnızca ters proxy sunucusu bir X. 509.440 sertifikası gerektirir ve bu sunucu, düz HTTP kullanarak, iç ağdaki uygulamanın sunucularıyla iletişim kurabilir.

> [!WARNING]
> Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.

## <a name="kestrel-in-aspnet-core-apps"></a>ASP.NET Core uygulamalarda Kestrel

ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır. *Program.cs*içinde <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> Yöntem şunu çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

Konak oluşturma hakkında daha fazla bilgi için, uygulamasının <xref:fundamentals/host/generic-host#set-up-a-host>konak ve *Varsayılan Oluşturucu ayarlarını* *ayarlama* bölümüne bakın.

Çağrıldıktan `ConfigureWebHostDefaults`sonra ek yapılandırma sağlamak için şunu kullanın `ConfigureKestrel`:

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

## <a name="kestrel-options"></a>Kestrel seçenekleri

Kestrel Web sunucusu, Internet 'e yönelik dağıtımlarda özellikle yararlı olan kısıtlama yapılandırma seçeneklerine sahiptir.

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> Sınıfının <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> özelliğinde kısıtlamaları ayarlayın. Özelliği `Limits` , <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> sınıfının bir örneğini barındırır.

Aşağıdaki örnekler <xref:Microsoft.AspNetCore.Server.Kestrel.Core> ad alanını kullanır:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

Bu makalenin ilerleyen kısımlarında gösterilen örneklerde, Kestrel seçenekleri C# kodunda yapılandırılır. Ayrıca, Kestrel seçenekleri bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir. Örneğin, [dosya yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider) bir *appSettings. JSON* veya appSettings 'ten Kestrel yapılandırmasını yükleyebilir *. { Environment}. JSON* dosyası:

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>ve [uç nokta yapılandırması](#endpoint-configuration) yapılandırma sağlayıcılarından yapılandırılabilir. Kalan Kestrel yapılandırması C# kodunda yapılandırılmalıdır.

Aşağıdaki yaklaşımlardan **birini** kullanın:

* Kestrel 'i `Startup.ConfigureServices`yapılandırma:

  1. `Startup` Sınıfına bir örneği `IConfiguration` ekleyin. Aşağıdaki örnek, eklenen yapılandırmanın `Configuration` özelliğe atandığını varsayar.
  2. İçinde `Startup.ConfigureServices`, yapılandırma `Kestrel` bölümünü Kestrel 'in yapılandırmasına yükleyin:

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

* Ana bilgisayarı oluştururken Kestrel yapılandırma:

  *Program.cs*' de, yapılandırma `Kestrel` bölümünü Kestrel 'in yapılandırmasına yükleyin:

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

Yukarıdaki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.

### <a name="keep-alive-timeout"></a>Etkin tut zaman aşımı

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

[Etkin tutma zaman aşımını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar. Varsayılan olarak 2 dakikadır.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a>İstemci bağlantıları üst sınırı

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

En fazla eş zamanlı açık TCP bağlantısı sayısı tüm uygulama için aşağıdaki kodla ayarlanabilir:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

HTTP veya HTTPS 'den başka bir protokole (örneğin, bir WebSockets isteğinde) yükseltilen bağlantılara yönelik ayrı bir sınır vardır. Bir bağlantı yükseltildikten sonra, bu `MaxConcurrentConnections` sınıra göre sayılmaz.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

En fazla bağlantı sayısı, varsayılan olarak sınırsız (null).

### <a name="maximum-request-body-size"></a>En büyük istek gövdesi boyutu

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

Varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu değer yaklaşık 28,6 MB 'tır.

ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yaklaşım, bir eylem yönteminde <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> özniteliğini kullanmaktır:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Her istekte uygulama için kısıtlamanın nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

Ara yazılım içindeki belirli bir istek üzerindeki ayarı geçersiz kılın:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Uygulama isteği okumaya başladıktan sonra bir istek üzerindeki sınırı yapılandırırsa, bir özel durum oluşturulur. Özelliğin salt okuma `IsReadOnly` durumunda olup olmadığını belirten bir özellik vardır. Bu, sınırı yapılandırmanın çok geç olduğunu gösterir. `MaxRequestBodySize`

Bir uygulama [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)arkasında [çalıştırıldığında](xref:host-and-deploy/iis/index#out-of-process-hosting-model) , IIS sınırı zaten ayarladığı için Kestrel 'nin istek gövdesi boyut sınırı devre dışı bırakılır.

### <a name="minimum-request-body-data-rate"></a>En az istek gövdesi veri hızı

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel bayt/saniye cinsinden belirtilen fiyata ulaşan her saniye sonra denetler. Oran en düşük değerin altına düşerse bağlantı zaman aşımına uğrar. Yetkisiz kullanım süresi, Kestrel 'in istemciye gönderme oranını en düşük süreye kadar artırabilme Bu süre boyunca fiyat denetlenmez. Yetkisiz kullanım süresi, TCP yavaş başlatma nedeniyle başlangıçta verileri yavaş bir hızda gönderen bağlantıların bırakılmasını önlemeye yardımcı olur.

Varsayılan en düşük oran, 5 saniyelik bir yetkisiz kullanım süresi ile 240 bayt/saniye olur.

Yanıt için bir minimum oran da geçerlidir. İstek sınırını ayarlamaya yönelik kod ve yanıt sınırı, özellik ve arabirim adlarında olduğu gibi aynı `RequestBody` `Response` olur.

*Program.cs*içinde en düşük veri hızlarının nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

Ara yazılım içindeki istek başına düşen minimum hız sınırlarını geçersiz kılın:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

Önceki <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> örnekte başvurulan, http/2 isteklerinde hız sınırlarını `HttpContext.Features` değiştirmek, protokolün istek ÇOĞULLAMA desteği nedeniyle http/2 için genel olarak desteklenmediği için, http/2 istekleri için ' de mevcut değildir. <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> Ancak, http/2 istekleri `HttpContext.Features` için yine de vardır, çünkü okuma hızı sınırı, bir http/2 isteği için de olarak `IHttpMinRequestBodyDataRateFeature.MinDataRate` `null` ayarlanarak tamamen istek temelli olarak *devre dışı* bırakılabilir. Bunun dışında bir `IHttpMinRequestBodyDataRateFeature.MinDataRate` değere ayarlamaya çalışılması veya bir http/2 isteği verilmeye neden `null` `NotSupportedException` olur.

HTTP/1. x ve HTTP `KestrelServerOptions.Limits` /2 bağlantılarına hala uygulanan sunucu genelindeki hız sınırları.

### <a name="request-headers-timeout"></a>İstek üst bilgileri zaman aşımı

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Sunucunun istek üst bilgilerini alması için harcadığı en uzun süreyi alır veya ayarlar. Varsayılan değer 30 saniyedir.

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a>Bağlantı başına en fazla akış

`Http2.MaxStreamsPerConnection`HTTP/2 bağlantısı başına eşzamanlı istek akışı sayısını sınırlar. Fazlalık akışlar reddedildi.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

Varsayılan değer 100’dür.

### <a name="header-table-size"></a>Üst bilgi tablosu boyutu

HPACK kod çözücüsü HTTP/2 bağlantıları için HTTP üstbilgilerini açar. `Http2.HeaderTableSize`HPACK kod çözücüsünün kullandığı üst bilgi sıkıştırma tablosunun boyutunu sınırlandırır. Değer sekizli cinsinden sağlanır ve sıfırdan büyük olmalıdır (0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

Varsayılan değer 4096 ' dir.

### <a name="maximum-frame-size"></a>En büyük çerçeve boyutu

`Http2.MaxFrameSize`sunucu tarafından alınan veya gönderilen HTTP/2 bağlantı çerçevesi yükünün izin verilen en büyük boyutunu belirtir. Değer sekizli cinsinden sağlanır ve 2 ^ 14 (16.384) ile 2 ^ 24-1 (16.777.215) arasında olmalıdır.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

Varsayılan değer 2 ^ 14 ' dir (16.384).

### <a name="maximum-request-header-size"></a>En fazla istek üst bilgi boyutu

`Http2.MaxRequestHeaderFieldSize`istek üst bilgisi değerlerinin sekizlisi cinsinden izin verilen en büyük boyutu belirtir. Bu sınır, sıkıştırılmış ve sıkıştırılmamış temsillerinde hem ad hem de değer için geçerlidir. Değer sıfırdan büyük (0) olmalıdır.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

Varsayılan değer 8.192 ' dir.

### <a name="initial-connection-window-size"></a>İlk bağlantı pencere boyutu

`Http2.InitialConnectionWindowSize`sunucu, bağlantı başına tüm istekler (akışlar) genelinde toplanan tek seferde sunucunun arabelleğe aldığı en fazla istek gövde verilerini bayt cinsinden gösterir. İstekleri ile `Http2.InitialStreamWindowSize`de sınırlıdır. Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

Varsayılan değer 128 KB 'tır (131.072).

### <a name="initial-stream-window-size"></a>İlk akış pencere boyutu

`Http2.InitialStreamWindowSize`sunucu, istek başına bir kez (Stream) arabelleğe alınan en fazla istek gövde verilerini bayt cinsinden gösterir. İstekleri ile `Http2.InitialConnectionWindowSize`de sınırlıdır. Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

Varsayılan değer 96 KB 'tır (98.304).

### <a name="synchronous-io"></a>Zaman Uyumlu G/Ç

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>istek ve yanıt için zaman uyumlu g/ç 'ye izin verilip verilmediğini denetler. Varsayılan değer: `false`.

> [!WARNING]
> Çok sayıda engelleme zaman uyumlu g/ç işlemi, iş parçacığı havuzuna yol açabilir, bu da uygulamanın yanıt vermemesine neden olur. Yalnızca zaman `AllowSynchronousIO` uyumsuz g/ç desteklemeyen bir kitaplık kullanırken etkinleştirin.

Aşağıdaki örnek, zaman uyumlu g/ç 'yi sunar:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

Diğer Kestrel seçenekleri ve limitleri hakkında daha fazla bilgi için bkz.:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>Uç nokta yapılandırması

Varsayılan olarak, ASP.NET Core bağlar:

* `http://localhost:5000`
* `https://localhost:5001`(bir yerel geliştirme sertifikası varsa)

Kullanarak URL 'Leri belirtin:

* `ASPNETCORE_URLS`ortam değişkeni.
* `--urls`komut satırı bağımsız değişkeni.
* `urls`Ana bilgisayar yapılandırma anahtarı.
* `UseUrls`genişletme yöntemi.

Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS). Değeri noktalı virgülle ayrılmış bir liste olarak yapılandırın (örneğin, `"Urls": "http://localhost:8000;http://localhost:8001"`).

Bu yaklaşımlar hakkında daha fazla bilgi için [sunucu URL 'leri](xref:fundamentals/host/web-host#server-urls) ve [geçersiz kılma yapılandırması](xref:fundamentals/host/web-host#override-configuration)bölümüne bakın.

Geliştirme sertifikası oluşturuldu:

* [.NET Core SDK](/dotnet/core/sdk) yüklendiği zaman.
* [Geliştirme-CERT aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.

Bazı tarayıcılarda yerel geliştirme sertifikasına güvenmek için açık izin verilmesi gerekir.

Proje şablonları, uygulamaları HTTPS üzerinde varsayılan olarak çalışacak şekilde yapılandırır ve [https yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.

URL <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> öneklerini <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> ve bağlantı <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> noktalarını Kestrel için yapılandırmak üzere üzerinde çağrı veya Yöntemler.

`UseUrls`, `--urls` komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı ve `ASPNETCORE_URLS` ortam değişkeni de çalışır, ancak bu bölümün Ilerleyen kısımlarında belirtilen sınırlamalara sahiptir (https uç noktası yapılandırması için varsayılan sertifika kullanılabilir olmalıdır).

`KestrelServerOptions`yapılandırmada

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults Varsayılanları (\<eylem listenoptions>)

Belirtilen her bir `Action` uç nokta için çalıştırılacak bir yapılandırma belirtir. Birden `ConfigureEndpointDefaults` çok kez çağırma önceki `Action`s 'yi son `Action` belirtilen ile değiştirir.

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** Çağrılmadan <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> önce çağırarak oluşturulan uç noktalara varsayılan değer uygulanmaz.

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults (ACTION\<httpsconnectionadapteroptions>)

Her HTTPS uç `Action` noktası için çalıştırılacak bir yapılandırma belirtir. Birden `ConfigureHttpsDefaults` çok kez çağırma önceki `Action`s 'yi son `Action` belirtilen ile değiştirir.

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** Çağrılmadan <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> önce çağırarak oluşturulan uç noktalara varsayılan değer uygulanmaz.

### <a name="configureiconfiguration"></a>Yapılandırma (Iconation)

Bir <xref:Microsoft.Extensions.Configuration.IConfiguration> as girişi alan Kestrel ayarlamak için bir yapılandırma yükleyicisi oluşturur. Yapılandırma, Kestrel için yapılandırma bölümünün kapsamına alınmalıdır.

### <a name="listenoptionsusehttps"></a>ListenOptions. UseHttps

Kestrel 'i HTTPS kullanacak şekilde yapılandırın.

`ListenOptions.UseHttps`uzantılardan

* `UseHttps`&ndash; Kestrel 'i varsayılan sertifikayla HTTPS kullanacak şekilde yapılandırın. Varsayılan sertifika yapılandırılmamışsa bir özel durum oluşturur.
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

`ListenOptions.UseHttps`parametrelere

* `filename`, uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yolu ve dosya adıdır.
* `password`X. 509.440 sertifika verilerine erişmek için parola gereklidir.
* `configureOptions`, `HttpsConnectionAdapterOptions`öğesini `Action` yapılandırmak için kullanılır. Döndürür `ListenOptions`.
* `storeName`, sertifikanın yükleneceği sertifika deposudur.
* `subject`, sertifika için konu adıdır.
* `allowInvalid`geçersiz sertifikaların, otomatik olarak imzalanan sertifikalar gibi göz önünde bulundurulmayacağını gösterir.
* `location`, sertifikanın yükleneceği mağaza konumudur.
* `serverCertificate`, X. 509.440 sertifikasıdır.

Üretimde HTTPS 'nin açıkça yapılandırılması gerekir. En azından, varsayılan bir sertifika sağlanmalıdır.

Daha sonra açıklanan desteklenen yapılandırma:

* Yapılandırma yok
* Varsayılan sertifikayı yapılandırmadan Değiştir
* Koddaki varsayılanları değiştirme

*Yapılandırma yok*

Kestrel tarihinde `http://localhost:5000` dinler ve `https://localhost:5001` (varsayılan bir sertifika varsa).

<a name="configuration"></a>

*Varsayılan sertifikayı yapılandırmadan Değiştir*

`CreateDefaultBuilder`Kestrel `Configure(context.Configuration.GetSection("Kestrel"))` yapılandırmasını yüklemek için varsayılan olarak çağırır. Varsayılan bir HTTPS uygulama ayarları yapılandırma şeması Kestrel için kullanılabilir. Disk üzerindeki bir dosyadan ya da bir sertifika deposundan kullanılacak URL 'Ler ve Sertifikalar dahil olmak üzere birden çok uç nokta yapılandırın.

Aşağıdaki *appSettings. JSON* örneğinde:

* Geçersiz sertifikaların kullanılmasına izin vermek `true` Için **allowwınvalid** ' i ayarlayın (örneğin, otomatik olarak imzalanan sertifikalar).
* Bir sertifika belirtmeyen herhangi bir HTTPS uç noktası (aşağıdaki örnekte bulunan**httpsdefaultcert** ), **Sertifikalar** > **varsayılan** veya geliştirme sertifikası altında tanımlanan sertifikaya geri döner.

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

Herhangi bir sertifika düğümü için **yol** ve **parola** kullanmanın alternatifi sertifika deposu alanlarını kullanarak sertifikayı belirtmektir. Örneğin, **sertifika** > **varsayılan** sertifikası şu şekilde belirtilebilir:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Şema notları:

* Uç nokta adları büyük/küçük harfe duyarlıdır. Örneğin, `HTTPS` ve `Https` geçerlidir.
* Her `Url` uç nokta için parametresi gereklidir. Bu parametrenin biçimi, en üst düzey `Urls` yapılandırma parametresiyle aynıdır, ancak tek bir değerle sınırlı olur.
* Bu uç noktalar, üst düzey `Urls` yapılandırmada tanımlananlar yerine bunlara ekleme yerine bunların yerini alır. Kullanılarak `Listen` kodda tanımlanan uç noktalar yapılandırma bölümünde tanımlanan uç noktalar ile birikimlidir.
* Bu `Certificate` bölüm isteğe bağlıdır. `Certificate` Bölüm belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır. Kullanılabilir varsayılan değer yoksa, sunucu bir özel durum oluşturur ve başlayamaz.
* Bu `Certificate` bölüm hem **yol**&ndash;**parolasını** hem de **Konu**&ndash;**deposu** sertifikalarını destekler.
* Herhangi bir sayıda uç nokta, bağlantı noktası çakışmalarına neden olmadıkları sürece bu şekilde tanımlanabilir.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))`yapılandırılmış bir `KestrelConfigurationLoader` uç noktanın `.Endpoint(string name, listenOptions => { })` ayarlarını tamamlamak için kullanılabilecek bir yöntemi olan bir döndürür:

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

`KestrelServerOptions.ConfigurationLoader`, tarafından <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>sağlana gibi var olan yükleyicisindeki yinelemeye devam etmek için doğrudan erişilebilir.

* Her uç noktanın yapılandırma bölümü, özel ayarların okunabilmesi için `Endpoint` yöntemindeki seçeneklerde kullanılabilir.
* Birden çok yapılandırma, başka bir bölümle `options.Configure(context.Configuration.GetSection("{SECTION}"))` yeniden çağırarak yüklenebilir. Önceki örneklerde açıkça çağrılmadığı takdirde `Load` , yalnızca son yapılandırma kullanılır. Metapackage, varsayılan yapılandırma `Load` bölümünün değiştirilmesini sağlayacak şekilde çağırmıyor.
* `KestrelConfigurationLoader`API `Listen` ailesini aşırı yükleme `KestrelServerOptions` olarak `Endpoint` yansıtır, bu nedenle kod ve yapılandırma uç noktaları aynı yerde yapılandırılabilir. Bu aşırı yüklemeler adları kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları kullanır.

*Koddaki varsayılanları değiştirme*

`ConfigureEndpointDefaults`ve `ConfigureHttpsDefaults` , önceki senaryoda belirtilen varsayılan sertifikayı geçersiz kılma `ListenOptions` dahil `HttpsConnectionAdapterOptions`, ve için varsayılan ayarları değiştirmek üzere kullanılabilir. `ConfigureEndpointDefaults`ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.

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

*SNı için Kestrel desteği*

[Sunucu adı belirtme (SNı)](https://tools.ietf.org/html/rfc6066#section-3) , aynı IP adresi ve bağlantı noktası üzerinde birden fazla etki alanını barındırmak için kullanılabilir. SNı 'nin çalışması için, istemci, TLS el sıkışması sırasında güvenli oturum ana bilgisayar adını, sunucunun doğru sertifikayı sağlayabilmesi için gönderir. İstemci, TLS anlaşmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için bulunan sertifikayı kullanır.

Kestrel, `ServerCertificateSelector` geri çağırma aracılığıyla SNI destekler. Geri çağırma, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesini sağlamak için bağlantı başına bir kez çağrılır.

SNı desteği şunları gerektirir:

* Hedef Framework `netcoreapp2.1` veya sonraki sürümlerde çalışıyor. `net461` Veya sonraki sürümlerde, geri çağırma çağrılır, ancak her `name` zaman `null`olur. Ayrıca `name` , ISTEMCI `null` , TLS el sıkışmasının ana bilgisayar adı parametresini sağlamıyorsa de olur.
* Tüm Web siteleri aynı Kestrel örneğinde çalışır. Kestrel, bir IP adresi ve bağlantı noktasının bir ters proxy olmadan birden çok örnek arasında paylaşılmasını desteklemez.

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

Bir <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> bağlantıda bayt düzeyinde Iletişim Için hata ayıklama düzeyi günlüklerini yayma çağrısı. Bağlantı günlüğü, TLS şifreleme ve proxy 'nin arkasındaki gibi alt düzey iletişimde sorunları gidermeye yardımcı olur. `UseConnectionLogging` Daha önce `UseHttps`yerleştirilmişse, şifrelenmiş trafik günlüğe kaydedilir. `UseConnectionLogging` Öğesinden sonra `UseHttps`yerleştirilmişse, şifresi çözülmüş trafik günlüğe kaydedilir.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a>TCP yuvasına bağlama

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> YÖNTEMI bir TCP yuvasına bağlanır ve bir seçenek lambda X. 509.440 sertifika yapılandırmasına izin verir:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

Örnek, HTTPS 'yi ile <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>bir uç nokta için yapılandırır. Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak üzere aynı API 'yi kullanın.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>UNIX yuvasına bağlama

Aşağıdaki örnekte gösterildiği gibi NGINX ile gelişmiş performans için ile birlikte <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> bir UNIX yuvası dinleyin:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* NGINX `server`  >  `location`  >  `proxy_pass` yapılandırma dosyasında, girdisini olarak `http://unix:/tmp/{KESTREL SOCKET}:/;`ayarlayın. `{KESTREL SOCKET}`, için <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> belirtilen yuvanın adıdır (örneğin, `kestrel-test.sock` önceki örnekte).
* Yuvanın NGINX tarafından yazılabilir olduğundan emin olun (örneğin, `chmod go+w /tmp/kestrel-test.sock`).

### <a name="port-0"></a>Bağlantı noktası 0

Bağlantı noktası numarası `0` belirtildiğinde, Kestrel dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır. Aşağıdaki örnek, çalışma zamanında Kestrel gerçekte hangi bağlantı noktasının gerçekten bağlandığını nasıl belirleyeceğini göstermektedir:

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Uygulama çalıştırıldığında konsol penceresi çıkışı, uygulamanın erişilebileceği dinamik bağlantı noktasını gösterir:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>Sınırlamalar

Aşağıdaki yaklaşımlar ile uç noktaları yapılandırın:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* `--urls`komut satırı bağımsız değişkeni
* `urls`Ana bilgisayar yapılandırma anahtarı
* `ASPNETCORE_URLS`ortam değişkeni

Bu yöntemler, kodun Kestrel dışındaki sunucularla çalışmasını sağlamak için yararlıdır. Ancak, aşağıdaki sınırlamalara dikkat edin:

* HTTPS uç noktası yapılandırmasında varsayılan bir sertifika sağlanmadığı sürece HTTPS bu yaklaşımlar ile kullanılamaz (örneğin, bu konunun önceki kısımlarında `KestrelServerOptions` gösterildiği gibi yapılandırma veya yapılandırma dosyası kullanılıyor).
* Hem `Listen` hem `UseUrls` de yaklaşımları aynı anda kullanıldığında, `Listen` uç noktalar `UseUrls` uç noktaları geçersiz kılar.

### <a name="iis-endpoint-configuration"></a>IIS uç nokta yapılandırması

IIS kullanırken, IIS geçersiz kılma bağlamaları için URL bağlamaları veya `Listen` `UseUrls`ya da tarafından ayarlanır. Daha fazla bilgi için [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.

### <a name="listenoptionsprotocols"></a>ListenOptions. Protocols

`Protocols` Özelliği, bir bağlantı uç noktasında veya`HttpProtocols`sunucu için etkin HTTP protokollerini () belirler. `HttpProtocols` Sabit listesinden `Protocols` özelliğe bir değer atayın.

| `HttpProtocols`sabit listesi değeri | Bağlantı protokolü izin verildi |
| -------------------------- | ----------------------------- |
| `Http1`                    | Yalnızca HTTP/1.1. , TLS olmadan veya ile kullanılabilir. |
| `Http2`                    | Yalnızca HTTP/2. Yalnızca istemci [önceki bir bilgi modunu](https://tools.ietf.org/html/rfc7540#section-3.4)DESTEKLIYORSA, TLS olmadan kullanılabilir. |
| `Http1AndHttp2`            | HTTP/1.1 ve HTTP/2. HTTP/2, istemcinin TLS [uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) el sıkışmasında http/2 seçmesini gerektirir; Aksi takdirde, bağlantı varsayılan olarak HTTP/1.1 ' dir. |

Herhangi bir `ListenOptions.Protocols` uç nokta için varsayılan değer `HttpProtocols.Http1AndHttp2`.

HTTP/2 için TLS kısıtlamaları:

* TLS sürüm 1,2 veya üzeri
* Yeniden anlaşma devre dışı
* Sıkıştırma devre dışı
* En az kısa ömürlü anahtar değişim boyutları:
  * Eliptik Eğri Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; &ndash; 224 bit minimum
  * Sınırlı alan Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; &ndash; 2048 bit minimum
* Şifre paketi kara listede değil

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack; &lbrack; `FIPS186` &rbrack; P-256 eliptik eğrisi varsayılan olarak desteklenir. `TLS-ECDHE` &rbrack;

Aşağıdaki örnek, 8000 numaralı bağlantı noktasında HTTP/1.1 ve HTTP/2 bağlantılarına izin verir. Bağlantılar, sağlanan bir sertifikayla TLS ile güvenlidir:

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

Gerektiğinde belirli şifrelemeler için TLS el sıkışmaları için bağlantı temelinde filtre uygulamak üzere bağlantı ara yazılımı kullanın.

Aşağıdaki örnek, uygulamanın <xref:System.NotSupportedException> desteklemediği herhangi bir şifre algoritması için oluşturur. Alternatif olarak, [ılshandshakefeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) öğesini kabul edilebilir şifreleme paketleri listesi ile tanımlayın ve karşılaştırın.

[CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) şifre algoritması ile hiçbir şifreleme kullanılmaz.

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

Bağlantı filtrelemesi, bir <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda aracılığıyla da yapılandırılabilir:

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

Linux 'ta, <xref:System.Net.Security.CipherSuitesPolicy> TLS el sıkışmaları her bağlantı temelinde filtrelemek için kullanılabilir:

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

*Protokolü yapılandırmadan ayarla*

`CreateDefaultBuilder`Kestrel `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` yapılandırmasını yüklemek için varsayılan olarak çağırır.

Aşağıdaki *appSettings. JSON* örneği, tüm uç noktalar için varsayılan bağlantı protokolü olarak http/1.1 oluşturur:

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

Aşağıdaki *appSettings. JSON* örneği, belirli bir uç nokta için http/1.1 bağlantı protokolünü belirler:

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

Yapılandırma tarafından ayarlanan kod geçersiz kılma değerlerinde belirtilen protokoller.

## <a name="transport-configuration"></a>Aktarım yapılandırması

Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>) kullanımını gerektiren projeler için:

* Uygulamanın proje dosyasına [Microsoft. AspNetCore. Server. Kestrel. Transport. libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) paketi için bir bağımlılık ekleyin:

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* Şunu <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> çağırın `IWebHostBuilder`:

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

, `UseUrls` `--urls` Komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı veya `ASPNETCORE_URLS` ortam değişkeni kullanılırken, URL önekleri aşağıdaki biçimlerden birinde olabilir.

Yalnızca HTTP URL ön ekleri geçerlidir. Kestrel, kullanılarak `UseUrls`URL BAĞLAMALARı yapılandırılırken https 'yi desteklemez.

* Bağlantı noktası numarası olan IPv4 adresi

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0`Tüm IPv4 adreslerine bağlanan özel bir durumdur.

* Bağlantı noktası numarasına sahip IPv6 adresi

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]`, IPv4 `0.0.0.0`'un IPv6 eşdeğeridir.

* Bağlantı noktası numarası olan ana bilgisayar adı

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  , Ve `+`ana `*`bilgisayar adları özel değildir. Geçerli bir IP adresi olarak tanınmayan veya `localhost` tüm IPv4 ve IPv6 IP 'lerine bağlanan her şey. Farklı ana bilgisayar adlarını aynı bağlantı noktasında farklı ASP.NET Core uygulamalarına bağlamak için, [http. sys](xref:fundamentals/servers/httpsys) veya IIS, NGINX veya Apache gibi bir ters proxy sunucusu kullanın.

  > [!WARNING]
  > Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.

* Port `localhost` numarası veya bağlantı noktası numarası ile geri döngü IP 'si olan ana bilgisayar adı

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  `localhost` Belirtildiğinde, Kestrel hem IPv4 hem de IPv6 geri döngü arabirimlerini bağlamaya çalışır. İstenen bağlantı noktası, herhangi bir geri döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılamaz. Herhangi bir geri döngü arabirimi başka bir nedenle kullanılamıyorsa (genellikle IPv6 desteklenmediği için), Kestrel bir uyarı kaydeder.

## <a name="host-filtering"></a>Konak filtreleme

Kestrel gibi önekleri temel alarak yapılandırmayı desteklese `http://example.com:5000`de, Kestrel büyük ölçüde ana bilgisayar adını yoksayar. Ana `localhost` bilgisayar, geri döngü adreslerine bağlama için kullanılan özel bir durumdur. Açık IP adresi dışındaki tüm ana bilgisayar tüm genel IP adreslerine bağlanır. `Host`Üstbilgiler doğrulanmadı.

Geçici bir çözüm olarak, ana bilgisayar filtreleme ara yazılımı kullanın. Ana bilgisayar filtreleme ara yazılımı, ASP.NET Core uygulamaları için örtük olarak sunulan [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır. Ara yazılım tarafından <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>eklenir ve şunları çağırır <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Ana bilgisayar filtreleme ara yazılımı varsayılan olarak devre dışıdır. Ara yazılımı etkinleştirmek için `AllowedHosts` *appSettings. JSON*/appSettings içinde bir anahtar tanımlayın *.\< EnvironmentName>. JSON*. Değer, bağlantı noktası numaraları olmayan ana bilgisayar adlarının noktalı virgülle ayrılmış listesidir:

*appSettings. JSON*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer) da bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçenek içerir. İletilen üstbilgiler ara yazılımı ve ana bilgisayar filtreleme ara yazılımı, farklı senaryolar için benzer işlevlere sahiptir. Iletilen `AllowedHosts` üstbilgiler ara yazılımı ile, istekler ters bir `Host` ara sunucu veya yük dengeleyici ile iletilirken üst bilgi korunurken, bu işlem için uygun bir ayar vardır. Ana `AllowedHosts` bilgisayar filtreleme ara yazılımı ile ayarlama, Kestrel herkese açık bir uç sunucu olarak veya `Host` üst bilgi doğrudan iletildiğinde kullanılır.
>
> Iletilen üstbilgiler ara yazılımı hakkında daha fazla bilgi için <xref:host-and-deploy/proxy-load-balancer>bkz..

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Kestrel, ASP.NET Core için platformlar arası [Web sunucusudur](xref:fundamentals/servers/index). Kestrel, ASP.NET Core proje şablonlarında varsayılan olarak bulunan Web sunucusudur.

Kestrel aşağıdaki senaryoları destekler:

* HTTPS
* [WebSockets](https://github.com/aspnet/websockets) 'i etkinleştirmek için kullanılan donuk yükseltme
* NGINX 'in arkasında yüksek performans için UNIX Yuvaları
* HTTP/2 (macOS&dagger;hariç)

&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.

Kestrel, .NET Core 'un desteklediği tüm platformlarda ve sürümlerde desteklenir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="http2-support"></a>HTTP/2 desteği

Aşağıdaki temel gereksinimler karşılanıyorsa, [http/2](https://httpwg.org/specs/rfc7540.html) ASP.NET Core uygulamalar için kullanılabilir:

* İşletim sistemi&dagger;
  * Windows Server 2016/Windows 10 veya üzeri&Dagger;
  * OpenSSL 1.0.2 veya üzerini içeren Linux (örneğin, Ubuntu 16,04 veya üzeri)
* Hedef Framework: .NET Core 2,2 veya üzeri
* [Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı
* TLS 1,2 veya üzeri bağlantı

&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.
&Dagger;Kestrel, Windows Server 2012 R2 ve Windows 8.1 'de HTTP/2 için sınırlı destek içerir. Bu işletim sistemlerinde kullanılabilir olan desteklenen TLS şifre paketlerinin listesi sınırlı olduğundan destek sınırlıdır. TLS bağlantılarının güvenliğini sağlamak için Eliptik Eğri dijital Imza algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekli olabilir.

Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2`.

HTTP/2 varsayılan olarak devre dışıdır. Yapılandırma hakkında daha fazla bilgi için [Kestrel Options](#kestrel-options) ve [Listenoptions. Protocols](#listenoptionsprotocols) bölümlerine bakın.

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Ters ara sunucu ile Kestrel ne zaman kullanılır?

Kestrel, kendisi veya [Internet Information Services (IIS)](https://www.iis.net/), [NGINX](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi bir *ters ara sunucu*ile kullanılabilir. Ters proxy sunucusu, ağdan gelen HTTP isteklerini alır ve Kestrel 'e iletir.

Sınır (Internet 'e yönelik) Web sunucusu olarak kullanılan Kestrel:

![Kestrel, ters proxy sunucusu olmadan doğrudan Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

Ters Proxy yapılandırmasında kullanılan Kestrel:

![Kestrel, IIS, NGINX veya Apache gibi bir ters ara sunucu üzerinden Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

İki yapılandırma de, ters ara sunucu sunucusuyla veya olmadan, desteklenen bir barındırma yapılandırması.

Ters proxy sunucusu olmayan bir uç sunucu olarak kullanılan Kestrel, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez. Kestrel bir bağlantı noktasını dinlemek üzere yapılandırıldığında, Kestrel isteklerin `Host` üst bilgilerinden bağımsız olarak bu bağlantı noktası için tüm trafiği işler. Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel 'e iletme yeteneğine sahiptir.

Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucu kullanılması iyi bir seçim olabilir.

Ters proxy:

* , Barındırdığı uygulamaların açığa çıkarılan genel yüzey alanını sınırlayabilir.
* Ek bir yapılandırma ve savunma katmanı sağlayın.
* , Mevcut altyapıyla daha iyi tümleşebilir.
* Yük dengelemeyi ve güvenli iletişim (HTTPS) yapılandırmasını kolaylaştırın. Yalnızca ters proxy sunucusu bir X. 509.440 sertifikası gerektirir ve bu sunucu, düz HTTP kullanarak, iç ağdaki uygulamanın sunucularıyla iletişim kurabilir.

> [!WARNING]
> Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a>ASP.NET Core uygulamalarında Kestrel kullanma

Microsoft. [AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) paketi [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunur.

ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır. *Program.cs*' de, şablon kodu çağırır <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>ve bu da <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> arka planda çağrı yapılır.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

Ana bilgisayarı hakkında `CreateDefaultBuilder` daha fazla bilgi için, uygulamasının <xref:fundamentals/host/web-host#set-up-a-host> *konak ayarlama* bölümüne bakın.

Çağrıldıktan `CreateDefaultBuilder`sonra ek yapılandırma sağlamak için şunu kullanın `ConfigureKestrel`:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

Uygulama, Konağı kurmak için `CreateDefaultBuilder` çağırmazsa, çağrılmadan <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** `ConfigureKestrel`önce çağırın:

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

## <a name="kestrel-options"></a>Kestrel seçenekleri

Kestrel Web sunucusu, Internet 'e yönelik dağıtımlarda özellikle yararlı olan kısıtlama yapılandırma seçeneklerine sahiptir.

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> Sınıfının <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> özelliğinde kısıtlamaları ayarlayın. Özelliği `Limits` , <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> sınıfının bir örneğini barındırır.

Aşağıdaki örnekler <xref:Microsoft.AspNetCore.Server.Kestrel.Core> ad alanını kullanır:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

Aşağıdaki örneklerde C# kodunda yapılandırılan Kestrel seçenekleri de bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir. Örneğin, dosya yapılandırma sağlayıcısı bir *appSettings. JSON* veya appSettings 'ten Kestrel yapılandırmasını yükleyebilir *. { Environment}. JSON* dosyası:

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

Aşağıdaki yaklaşımlardan **birini** kullanın:

* Kestrel 'i `Startup.ConfigureServices`yapılandırma:

  1. `Startup` Sınıfına bir örneği `IConfiguration` ekleyin. Aşağıdaki örnek, eklenen yapılandırmanın `Configuration` özelliğe atandığını varsayar.
  2. İçinde `Startup.ConfigureServices`, yapılandırma `Kestrel` bölümünü Kestrel 'in yapılandırmasına yükleyin:

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

* Ana bilgisayarı oluştururken Kestrel yapılandırma:

  *Program.cs*' de, yapılandırma `Kestrel` bölümünü Kestrel 'in yapılandırmasına yükleyin:

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

Yukarıdaki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.

### <a name="keep-alive-timeout"></a>Etkin tut zaman aşımı

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

[Etkin tutma zaman aşımını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar. Varsayılan olarak 2 dakikadır.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a>İstemci bağlantıları üst sınırı

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

En fazla eş zamanlı açık TCP bağlantısı sayısı tüm uygulama için aşağıdaki kodla ayarlanabilir:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

HTTP veya HTTPS 'den başka bir protokole (örneğin, bir WebSockets isteğinde) yükseltilen bağlantılara yönelik ayrı bir sınır vardır. Bir bağlantı yükseltildikten sonra, bu `MaxConcurrentConnections` sınıra göre sayılmaz.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

En fazla bağlantı sayısı, varsayılan olarak sınırsız (null).

### <a name="maximum-request-body-size"></a>En büyük istek gövdesi boyutu

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

Varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu değer yaklaşık 28,6 MB 'tır.

ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yaklaşım, bir eylem yönteminde <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> özniteliğini kullanmaktır:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Her istekte uygulama için kısıtlamanın nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

Ara yazılım içindeki belirli bir istek üzerindeki ayarı geçersiz kılın:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Uygulama isteği okumaya başladıktan sonra bir istek üzerindeki sınırı yapılandırırsa, bir özel durum oluşturulur. Özelliğin salt okuma `IsReadOnly` durumunda olup olmadığını belirten bir özellik vardır. Bu, sınırı yapılandırmanın çok geç olduğunu gösterir. `MaxRequestBodySize`

Bir uygulama [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)arkasında [çalıştırıldığında](xref:host-and-deploy/iis/index#out-of-process-hosting-model) , IIS sınırı zaten ayarladığı için Kestrel 'nin istek gövdesi boyut sınırı devre dışı bırakılır.

### <a name="minimum-request-body-data-rate"></a>En az istek gövdesi veri hızı

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel bayt/saniye cinsinden belirtilen fiyata ulaşan her saniye sonra denetler. Oran en düşük değerin altına düşerse bağlantı zaman aşımına uğrar. Yetkisiz kullanım süresi, Kestrel 'in istemciye gönderme oranını en düşük süreye kadar artırabilme Bu süre boyunca fiyat denetlenmez. Yetkisiz kullanım süresi, TCP yavaş başlatma nedeniyle başlangıçta verileri yavaş bir hızda gönderen bağlantıların bırakılmasını önlemeye yardımcı olur.

Varsayılan en düşük oran, 5 saniyelik bir yetkisiz kullanım süresi ile 240 bayt/saniye olur.

Yanıt için bir minimum oran da geçerlidir. İstek sınırını ayarlamaya yönelik kod ve yanıt sınırı, özellik ve arabirim adlarında olduğu gibi aynı `RequestBody` `Response` olur.

*Program.cs*içinde en düşük veri hızlarının nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

Ara yazılım içindeki istek başına düşen minimum hız sınırlarını geçersiz kılın:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

İstek çoğullama için protokol desteği nedeniyle, http/2 `HttpContext.Features` için bir istek başına hız sınırlarını değiştirmek, http/2 istekleri için, önceki örnekte başvurulan hiçbir oran özelliği mevcut değil. HTTP/1. x ve HTTP `KestrelServerOptions.Limits` /2 bağlantılarına hala uygulanan sunucu genelindeki hız sınırları.

### <a name="request-headers-timeout"></a>İstek üst bilgileri zaman aşımı

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Sunucunun istek üst bilgilerini alması için harcadığı en uzun süreyi alır veya ayarlar. Varsayılan değer 30 saniyedir.

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a>Bağlantı başına en fazla akış

`Http2.MaxStreamsPerConnection`HTTP/2 bağlantısı başına eşzamanlı istek akışı sayısını sınırlar. Fazlalık akışlar reddedildi.

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

### <a name="header-table-size"></a>Üst bilgi tablosu boyutu

HPACK kod çözücüsü HTTP/2 bağlantıları için HTTP üstbilgilerini açar. `Http2.HeaderTableSize`HPACK kod çözücüsünün kullandığı üst bilgi sıkıştırma tablosunun boyutunu sınırlandırır. Değer sekizli cinsinden sağlanır ve sıfırdan büyük olmalıdır (0).

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

Varsayılan değer 4096 ' dir.

### <a name="maximum-frame-size"></a>En büyük çerçeve boyutu

`Http2.MaxFrameSize`alacak HTTP/2 bağlantı çerçevesi yükünün en büyük boyutunu belirtir. Değer sekizli cinsinden sağlanır ve 2 ^ 14 (16.384) ile 2 ^ 24-1 (16.777.215) arasında olmalıdır.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

Varsayılan değer 2 ^ 14 ' dir (16.384).

### <a name="maximum-request-header-size"></a>En fazla istek üst bilgi boyutu

`Http2.MaxRequestHeaderFieldSize`istek üst bilgisi değerlerinin sekizlisi cinsinden izin verilen en büyük boyutu belirtir. Bu sınır, hem sıkıştırılmış hem de sıkıştırılmamış temsillerinde birlikte hem ad hem de değer için geçerlidir. Değer sıfırdan büyük (0) olmalıdır.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

Varsayılan değer 8.192 ' dir.

### <a name="initial-connection-window-size"></a>İlk bağlantı pencere boyutu

`Http2.InitialConnectionWindowSize`sunucu, bağlantı başına tüm istekler (akışlar) genelinde toplanan tek seferde sunucunun arabelleğe aldığı en fazla istek gövde verilerini bayt cinsinden gösterir. İstekleri ile `Http2.InitialStreamWindowSize`de sınırlıdır. Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

Varsayılan değer 128 KB 'tır (131.072).

### <a name="initial-stream-window-size"></a>İlk akış pencere boyutu

`Http2.InitialStreamWindowSize`sunucu, istek başına bir kez (Stream) arabelleğe alınan en fazla istek gövde verilerini bayt cinsinden gösterir. İstekleri ile `Http2.InitialStreamWindowSize`de sınırlıdır. Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

Varsayılan değer 96 KB 'tır (98.304).

### <a name="synchronous-io"></a>Zaman Uyumlu G/Ç

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>istek ve yanıt için zaman uyumlu g/ç 'ye izin verilip verilmediğini denetler. Varsayılan değer `true`.

> [!WARNING]
> Çok sayıda engelleme zaman uyumlu g/ç işlemi, iş parçacığı havuzuna yol açabilir, bu da uygulamanın yanıt vermemesine neden olur. Yalnızca zaman `AllowSynchronousIO` uyumsuz g/ç desteklemeyen bir kitaplık kullanırken etkinleştirin.

Aşağıdaki örnek, zaman uyumlu g/ç 'yi sunar:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

Diğer Kestrel seçenekleri ve limitleri hakkında daha fazla bilgi için bkz.:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>Uç nokta yapılandırması

Varsayılan olarak, ASP.NET Core bağlar:

* `http://localhost:5000`
* `https://localhost:5001`(bir yerel geliştirme sertifikası varsa)

Kullanarak URL 'Leri belirtin:

* `ASPNETCORE_URLS`ortam değişkeni.
* `--urls`komut satırı bağımsız değişkeni.
* `urls`Ana bilgisayar yapılandırma anahtarı.
* `UseUrls`genişletme yöntemi.

Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS). Değeri noktalı virgülle ayrılmış bir liste olarak yapılandırın (örneğin, `"Urls": "http://localhost:8000;http://localhost:8001"`).

Bu yaklaşımlar hakkında daha fazla bilgi için [sunucu URL 'leri](xref:fundamentals/host/web-host#server-urls) ve [geçersiz kılma yapılandırması](xref:fundamentals/host/web-host#override-configuration)bölümüne bakın.

Geliştirme sertifikası oluşturuldu:

* [.NET Core SDK](/dotnet/core/sdk) yüklendiği zaman.
* [Geliştirme-CERT aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.

Bazı tarayıcılarda yerel geliştirme sertifikasına güvenmek için açık izin verilmesi gerekir.

Proje şablonları, uygulamaları HTTPS üzerinde varsayılan olarak çalışacak şekilde yapılandırır ve [https yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.

URL <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> öneklerini <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> ve bağlantı <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> noktalarını Kestrel için yapılandırmak üzere üzerinde çağrı veya Yöntemler.

`UseUrls`, `--urls` komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı ve `ASPNETCORE_URLS` ortam değişkeni de çalışır, ancak bu bölümün Ilerleyen kısımlarında belirtilen sınırlamalara sahiptir (https uç noktası yapılandırması için varsayılan sertifika kullanılabilir olmalıdır).

`KestrelServerOptions`yapılandırmada

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults Varsayılanları (\<eylem listenoptions>)

Belirtilen her bir `Action` uç nokta için çalıştırılacak bir yapılandırma belirtir. Birden `ConfigureEndpointDefaults` çok kez çağırma önceki `Action`s 'yi son `Action` belirtilen ile değiştirir.

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** Çağrılmadan <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> önce çağırarak oluşturulan uç noktalara varsayılan değer uygulanmaz.

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults (ACTION\<httpsconnectionadapteroptions>)

Her HTTPS uç `Action` noktası için çalıştırılacak bir yapılandırma belirtir. Birden `ConfigureHttpsDefaults` çok kez çağırma önceki `Action`s 'yi son `Action` belirtilen ile değiştirir.

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** Çağrılmadan <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> önce çağırarak oluşturulan uç noktalara varsayılan değer uygulanmaz.


### <a name="configureiconfiguration"></a>Yapılandırma (Iconation)

Bir <xref:Microsoft.Extensions.Configuration.IConfiguration> as girişi alan Kestrel ayarlamak için bir yapılandırma yükleyicisi oluşturur. Yapılandırma, Kestrel için yapılandırma bölümünün kapsamına alınmalıdır.

### <a name="listenoptionsusehttps"></a>ListenOptions. UseHttps

Kestrel 'i HTTPS kullanacak şekilde yapılandırın.

`ListenOptions.UseHttps`uzantılardan

* `UseHttps`&ndash; Kestrel 'i varsayılan sertifikayla HTTPS kullanacak şekilde yapılandırın. Varsayılan sertifika yapılandırılmamışsa bir özel durum oluşturur.
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

`ListenOptions.UseHttps`parametrelere

* `filename`, uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yolu ve dosya adıdır.
* `password`X. 509.440 sertifika verilerine erişmek için parola gereklidir.
* `configureOptions`, `HttpsConnectionAdapterOptions`öğesini `Action` yapılandırmak için kullanılır. Döndürür `ListenOptions`.
* `storeName`, sertifikanın yükleneceği sertifika deposudur.
* `subject`, sertifika için konu adıdır.
* `allowInvalid`geçersiz sertifikaların, otomatik olarak imzalanan sertifikalar gibi göz önünde bulundurulmayacağını gösterir.
* `location`, sertifikanın yükleneceği mağaza konumudur.
* `serverCertificate`, X. 509.440 sertifikasıdır.

Üretimde HTTPS 'nin açıkça yapılandırılması gerekir. En azından, varsayılan bir sertifika sağlanmalıdır.

Daha sonra açıklanan desteklenen yapılandırma:

* Yapılandırma yok
* Varsayılan sertifikayı yapılandırmadan Değiştir
* Koddaki varsayılanları değiştirme

*Yapılandırma yok*

Kestrel tarihinde `http://localhost:5000` dinler ve `https://localhost:5001` (varsayılan bir sertifika varsa).

<a name="configuration"></a>

*Varsayılan sertifikayı yapılandırmadan Değiştir*

`CreateDefaultBuilder`Kestrel `Configure(context.Configuration.GetSection("Kestrel"))` yapılandırmasını yüklemek için varsayılan olarak çağırır. Varsayılan bir HTTPS uygulama ayarları yapılandırma şeması Kestrel için kullanılabilir. Disk üzerindeki bir dosyadan ya da bir sertifika deposundan kullanılacak URL 'Ler ve Sertifikalar dahil olmak üzere birden çok uç nokta yapılandırın.

Aşağıdaki *appSettings. JSON* örneğinde:

* Geçersiz sertifikaların kullanılmasına izin vermek `true` Için **allowwınvalid** ' i ayarlayın (örneğin, otomatik olarak imzalanan sertifikalar).
* Bir sertifika belirtmeyen herhangi bir HTTPS uç noktası (aşağıdaki örnekte bulunan**httpsdefaultcert** ), **Sertifikalar** > **varsayılan** veya geliştirme sertifikası altında tanımlanan sertifikaya geri döner.

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

Herhangi bir sertifika düğümü için **yol** ve **parola** kullanmanın alternatifi sertifika deposu alanlarını kullanarak sertifikayı belirtmektir. Örneğin, **sertifika** > **varsayılan** sertifikası şu şekilde belirtilebilir:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Şema notları:

* Uç nokta adları büyük/küçük harfe duyarlıdır. Örneğin, `HTTPS` ve `Https` geçerlidir.
* Her `Url` uç nokta için parametresi gereklidir. Bu parametrenin biçimi, en üst düzey `Urls` yapılandırma parametresiyle aynıdır, ancak tek bir değerle sınırlı olur.
* Bu uç noktalar, üst düzey `Urls` yapılandırmada tanımlananlar yerine bunlara ekleme yerine bunların yerini alır. Kullanılarak `Listen` kodda tanımlanan uç noktalar yapılandırma bölümünde tanımlanan uç noktalar ile birikimlidir.
* Bu `Certificate` bölüm isteğe bağlıdır. `Certificate` Bölüm belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır. Kullanılabilir varsayılan değer yoksa, sunucu bir özel durum oluşturur ve başlayamaz.
* Bu `Certificate` bölüm hem **yol**&ndash;**parolasını** hem de **Konu**&ndash;**deposu** sertifikalarını destekler.
* Herhangi bir sayıda uç nokta, bağlantı noktası çakışmalarına neden olmadıkları sürece bu şekilde tanımlanabilir.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))`yapılandırılmış bir `KestrelConfigurationLoader` uç noktanın `.Endpoint(string name, listenOptions => { })` ayarlarını tamamlamak için kullanılabilecek bir yöntemi olan bir döndürür:

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

`KestrelServerOptions.ConfigurationLoader`, tarafından <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>sağlana gibi var olan yükleyicisindeki yinelemeye devam etmek için doğrudan erişilebilir.

* Her uç noktanın yapılandırma bölümü, özel ayarların okunabilmesi için `Endpoint` yöntemindeki seçeneklerde kullanılabilir.
* Birden çok yapılandırma, başka bir bölümle `options.Configure(context.Configuration.GetSection("{SECTION}"))` yeniden çağırarak yüklenebilir. Önceki örneklerde açıkça çağrılmadığı takdirde `Load` , yalnızca son yapılandırma kullanılır. Metapackage, varsayılan yapılandırma `Load` bölümünün değiştirilmesini sağlayacak şekilde çağırmıyor.
* `KestrelConfigurationLoader`API `Listen` ailesini aşırı yükleme `KestrelServerOptions` olarak `Endpoint` yansıtır, bu nedenle kod ve yapılandırma uç noktaları aynı yerde yapılandırılabilir. Bu aşırı yüklemeler adları kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları kullanır.

*Koddaki varsayılanları değiştirme*

`ConfigureEndpointDefaults`ve `ConfigureHttpsDefaults` , önceki senaryoda belirtilen varsayılan sertifikayı geçersiz kılma `ListenOptions` dahil `HttpsConnectionAdapterOptions`, ve için varsayılan ayarları değiştirmek üzere kullanılabilir. `ConfigureEndpointDefaults`ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.

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

*SNı için Kestrel desteği*

[Sunucu adı belirtme (SNı)](https://tools.ietf.org/html/rfc6066#section-3) , aynı IP adresi ve bağlantı noktası üzerinde birden fazla etki alanını barındırmak için kullanılabilir. SNı 'nin çalışması için, istemci, TLS el sıkışması sırasında güvenli oturum ana bilgisayar adını, sunucunun doğru sertifikayı sağlayabilmesi için gönderir. İstemci, TLS anlaşmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için bulunan sertifikayı kullanır.

Kestrel, `ServerCertificateSelector` geri çağırma aracılığıyla SNI destekler. Geri çağırma, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesini sağlamak için bağlantı başına bir kez çağrılır.

SNı desteği şunları gerektirir:

* Hedef Framework `netcoreapp2.1` veya sonraki sürümlerde çalışıyor. `net461` Veya sonraki sürümlerde, geri çağırma çağrılır, ancak her `name` zaman `null`olur. Ayrıca `name` , ISTEMCI `null` , TLS el sıkışmasının ana bilgisayar adı parametresini sağlamıyorsa de olur.
* Tüm Web siteleri aynı Kestrel örneğinde çalışır. Kestrel, bir IP adresi ve bağlantı noktasının bir ters proxy olmadan birden çok örnek arasında paylaşılmasını desteklemez.

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

Bir <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> bağlantıda bayt düzeyinde Iletişim Için hata ayıklama düzeyi günlüklerini yayma çağrısı. Bağlantı günlüğü, TLS şifreleme ve proxy 'nin arkasındaki gibi alt düzey iletişimde sorunları gidermeye yardımcı olur. `UseConnectionLogging` Daha önce `UseHttps`yerleştirilmişse, şifrelenmiş trafik günlüğe kaydedilir. `UseConnectionLogging` Öğesinden sonra `UseHttps`yerleştirilmişse, şifresi çözülmüş trafik günlüğe kaydedilir.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a>TCP yuvasına bağlama

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> YÖNTEMI bir TCP yuvasına bağlanır ve bir seçenek lambda X. 509.440 sertifika yapılandırmasına izin verir:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

Örnek, HTTPS 'yi ile <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>bir uç nokta için yapılandırır. Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak üzere aynı API 'yi kullanın.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>UNIX yuvasına bağlama

Aşağıdaki örnekte gösterildiği gibi NGINX ile gelişmiş performans için ile birlikte <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> bir UNIX yuvası dinleyin:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* NGINX confiuguration dosyasında `server`  >  `location`  >  `proxy_pass` , girdisini olarak `http://unix:/tmp/{KESTREL SOCKET}:/;`ayarlayın. `{KESTREL SOCKET}`, için <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> belirtilen yuvanın adıdır (örneğin, `kestrel-test.sock` önceki örnekte).
* Yuvanın NGINX tarafından yazılabilir olduğundan emin olun (örneğin, `chmod go+w /tmp/kestrel-test.sock`). 

### <a name="port-0"></a>Bağlantı noktası 0

Bağlantı noktası numarası `0` belirtildiğinde, Kestrel dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır. Aşağıdaki örnek, çalışma zamanında Kestrel gerçekte hangi bağlantı noktasının gerçekten bağlandığını nasıl belirleyeceğini göstermektedir:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Uygulama çalıştırıldığında konsol penceresi çıkışı, uygulamanın erişilebileceği dinamik bağlantı noktasını gösterir:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>Sınırlamalar

Aşağıdaki yaklaşımlar ile uç noktaları yapılandırın:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* `--urls`komut satırı bağımsız değişkeni
* `urls`Ana bilgisayar yapılandırma anahtarı
* `ASPNETCORE_URLS`ortam değişkeni

Bu yöntemler, kodun Kestrel dışındaki sunucularla çalışmasını sağlamak için yararlıdır. Ancak, aşağıdaki sınırlamalara dikkat edin:

* HTTPS uç noktası yapılandırmasında varsayılan bir sertifika sağlanmadığı sürece HTTPS bu yaklaşımlar ile kullanılamaz (örneğin, bu konunun önceki kısımlarında `KestrelServerOptions` gösterildiği gibi yapılandırma veya yapılandırma dosyası kullanılıyor).
* Hem `Listen` hem `UseUrls` de yaklaşımları aynı anda kullanıldığında, `Listen` uç noktalar `UseUrls` uç noktaları geçersiz kılar.

### <a name="iis-endpoint-configuration"></a>IIS uç nokta yapılandırması

IIS kullanırken, IIS geçersiz kılma bağlamaları için URL bağlamaları veya `Listen` `UseUrls`ya da tarafından ayarlanır. Daha fazla bilgi için [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.

### <a name="listenoptionsprotocols"></a>ListenOptions. Protocols

`Protocols` Özelliği, bir bağlantı uç noktasında veya`HttpProtocols`sunucu için etkin HTTP protokollerini () belirler. `HttpProtocols` Sabit listesinden `Protocols` özelliğe bir değer atayın.

| `HttpProtocols`sabit listesi değeri | Bağlantı protokolü izin verildi |
| -------------------------- | ----------------------------- |
| `Http1`                    | Yalnızca HTTP/1.1. , TLS olmadan veya ile kullanılabilir. |
| `Http2`                    | Yalnızca HTTP/2. Yalnızca istemci [önceki bir bilgi modunu](https://tools.ietf.org/html/rfc7540#section-3.4)DESTEKLIYORSA, TLS olmadan kullanılabilir. |
| `Http1AndHttp2`            | HTTP/1.1 ve HTTP/2. HTTP/2 bir TLS ve [uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı gerektirir; Aksi takdirde, bağlantı varsayılan olarak HTTP/1.1 ' dir. |

Varsayılan protokol HTTP/1.1 ' dir.

HTTP/2 için TLS kısıtlamaları:

* TLS sürüm 1,2 veya üzeri
* Yeniden anlaşma devre dışı
* Sıkıştırma devre dışı
* En az kısa ömürlü anahtar değişim boyutları:
  * Eliptik Eğri Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; &ndash; 224 bit minimum
  * Sınırlı alan Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; &ndash; 2048 bit minimum
* Şifre paketi kara listede değil

`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack; &lbrack; `FIPS186` &rbrack; P-256 eliptik eğrisi varsayılan olarak desteklenir. `TLS-ECDHE` &rbrack;

Aşağıdaki örnek, 8000 numaralı bağlantı noktasında HTTP/1.1 ve HTTP/2 bağlantılarına izin verir. Bağlantılar, sağlanan bir sertifikayla TLS ile güvenlidir:

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

İsteğe bağlı olarak `IConnectionAdapter` , belirli şifrelemeler için bağlantı başına TLS el sıkışmaları filtrelemek üzere bir uygulama oluşturun:

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

*Protokolü yapılandırmadan ayarla*

<xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>Kestrel `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` yapılandırmasını yüklemek için varsayılan olarak çağırır.

Aşağıdaki *appSettings. JSON* örneğinde, tüm Kestrel uç noktaları için varsayılan bir bağlantı protokolü (http/1.1 ve http/2) oluşturulur:

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

Aşağıdaki yapılandırma dosyası örneği, belirli bir uç nokta için bağlantı protokolü oluşturur:

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

Yapılandırma tarafından ayarlanan kod geçersiz kılma değerlerinde belirtilen protokoller.

## <a name="transport-configuration"></a>Aktarım yapılandırması

ASP.NET Core 2,1 sürümü ile Kestrel 'in varsayılan taşıması artık libuv ' d i temel değildir ancak bunun yerine yönetilen yuvaları temel alır. Bu, çağrıyı <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> yapan ve aşağıdaki paketlerden birine bağlı olan ASP.NET Core 2,0 2,1 uygulamalarının önemli bir değişikliği olur:

* [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (doğrudan paket başvurusu)
* [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

Libuv kullanımını gerektiren projeler için:

* Uygulamanın proje dosyasına [Microsoft. AspNetCore. Server. Kestrel. Transport. libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) paketi için bir bağımlılık ekleyin:

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

, `UseUrls` `--urls` Komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı veya `ASPNETCORE_URLS` ortam değişkeni kullanılırken, URL önekleri aşağıdaki biçimlerden birinde olabilir.

Yalnızca HTTP URL ön ekleri geçerlidir. Kestrel, kullanılarak `UseUrls`URL BAĞLAMALARı yapılandırılırken https 'yi desteklemez.

* Bağlantı noktası numarası olan IPv4 adresi

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0`Tüm IPv4 adreslerine bağlanan özel bir durumdur.

* Bağlantı noktası numarasına sahip IPv6 adresi

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]`, IPv4 `0.0.0.0`'un IPv6 eşdeğeridir.

* Bağlantı noktası numarası olan ana bilgisayar adı

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  , Ve `+`ana `*`bilgisayar adları özel değildir. Geçerli bir IP adresi olarak tanınmayan veya `localhost` tüm IPv4 ve IPv6 IP 'lerine bağlanan her şey. Farklı ana bilgisayar adlarını aynı bağlantı noktasında farklı ASP.NET Core uygulamalarına bağlamak için, [http. sys](xref:fundamentals/servers/httpsys) veya IIS, NGINX veya Apache gibi bir ters proxy sunucusu kullanın.

  > [!WARNING]
  > Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.

* Port `localhost` numarası veya bağlantı noktası numarası ile geri döngü IP 'si olan ana bilgisayar adı

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  `localhost` Belirtildiğinde, Kestrel hem IPv4 hem de IPv6 geri döngü arabirimlerini bağlamaya çalışır. İstenen bağlantı noktası, herhangi bir geri döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılamaz. Herhangi bir geri döngü arabirimi başka bir nedenle kullanılamıyorsa (genellikle IPv6 desteklenmediği için), Kestrel bir uyarı kaydeder.

## <a name="host-filtering"></a>Konak filtreleme

Kestrel gibi önekleri temel alarak yapılandırmayı desteklese `http://example.com:5000`de, Kestrel büyük ölçüde ana bilgisayar adını yoksayar. Ana `localhost` bilgisayar, geri döngü adreslerine bağlama için kullanılan özel bir durumdur. Açık IP adresi dışındaki tüm ana bilgisayar tüm genel IP adreslerine bağlanır. `Host`Üstbilgiler doğrulanmadı.

Geçici bir çözüm olarak, ana bilgisayar filtreleme ara yazılımı kullanın. Ana bilgisayar filtreleme ara yazılımı, [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 veya 2,2) ' de yer alan [Microsoft. Aspnetcore. hostfiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır. Ara yazılım tarafından <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>eklenir ve şunları çağırır <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Ana bilgisayar filtreleme ara yazılımı varsayılan olarak devre dışıdır. Ara yazılımı etkinleştirmek için `AllowedHosts` *appSettings. JSON*/appSettings içinde bir anahtar tanımlayın *.\< EnvironmentName>. JSON*. Değer, bağlantı noktası numaraları olmayan ana bilgisayar adlarının noktalı virgülle ayrılmış listesidir:

*appSettings. JSON*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer) da bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçenek içerir. İletilen üstbilgiler ara yazılımı ve ana bilgisayar filtreleme ara yazılımı, farklı senaryolar için benzer işlevlere sahiptir. Iletilen `AllowedHosts` üstbilgiler ara yazılımı ile, istekler ters bir `Host` ara sunucu veya yük dengeleyici ile iletilirken üst bilgi korunurken, bu işlem için uygun bir ayar vardır. Ana `AllowedHosts` bilgisayar filtreleme ara yazılımı ile ayarlama, Kestrel herkese açık bir uç sunucu olarak veya `Host` üst bilgi doğrudan iletildiğinde kullanılır.
>
> Iletilen üstbilgiler ara yazılımı hakkında daha fazla bilgi için <xref:host-and-deploy/proxy-load-balancer>bkz..

::: moniker-end

::: moniker range="= aspnetcore-2.1"

Kestrel, ASP.NET Core için platformlar arası [Web sunucusudur](xref:fundamentals/servers/index). Kestrel, ASP.NET Core proje şablonlarında varsayılan olarak bulunan Web sunucusudur.

Kestrel aşağıdaki senaryoları destekler:

* HTTPS
* [WebSockets](https://github.com/aspnet/websockets) 'i etkinleştirmek için kullanılan donuk yükseltme
* NGINX 'in arkasında yüksek performans için UNIX Yuvaları

Kestrel, .NET Core 'un desteklediği tüm platformlarda ve sürümlerde desteklenir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a>Ters ara sunucu ile Kestrel ne zaman kullanılır?

Kestrel, kendisi veya [Internet Information Services (IIS)](https://www.iis.net/), [NGINX](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi bir *ters ara sunucu*ile kullanılabilir. Ters proxy sunucusu, ağdan gelen HTTP isteklerini alır ve Kestrel 'e iletir.

Sınır (Internet 'e yönelik) Web sunucusu olarak kullanılan Kestrel:

![Kestrel, ters proxy sunucusu olmadan doğrudan Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

Ters Proxy yapılandırmasında kullanılan Kestrel:

![Kestrel, IIS, NGINX veya Apache gibi bir ters ara sunucu üzerinden Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

İki yapılandırma de, ters ara sunucu sunucusuyla veya olmadan, desteklenen bir barındırma yapılandırması.

Ters proxy sunucusu olmayan bir uç sunucu olarak kullanılan Kestrel, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez. Kestrel bir bağlantı noktasını dinlemek üzere yapılandırıldığında, Kestrel isteklerin `Host` üst bilgilerinden bağımsız olarak bu bağlantı noktası için tüm trafiği işler. Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel 'e iletme yeteneğine sahiptir.

Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucu kullanılması iyi bir seçim olabilir.

Ters proxy:

* , Barındırdığı uygulamaların açığa çıkarılan genel yüzey alanını sınırlayabilir.
* Ek bir yapılandırma ve savunma katmanı sağlayın.
* , Mevcut altyapıyla daha iyi tümleşebilir.
* Yük dengelemeyi ve güvenli iletişim (HTTPS) yapılandırmasını kolaylaştırın. Yalnızca ters proxy sunucusu bir X. 509.440 sertifikası gerektirir ve bu sunucu, düz HTTP kullanarak, iç ağdaki uygulamanın sunucularıyla iletişim kurabilir.

> [!WARNING]
> Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a>ASP.NET Core uygulamalarında Kestrel kullanma

Microsoft. [AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) paketi [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunur.

ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır. *Program.cs*' de, şablon kodu çağırır <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>ve bu da <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> arka planda çağrı yapılır.

Çağrıldıktan `CreateDefaultBuilder`sonra ek yapılandırma sağlamak için şunu çağırın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

Ana bilgisayarı hakkında `CreateDefaultBuilder` daha fazla bilgi için, uygulamasının <xref:fundamentals/host/web-host#set-up-a-host> *konak ayarlama* bölümüne bakın.

## <a name="kestrel-options"></a>Kestrel seçenekleri

Kestrel Web sunucusu, Internet 'e yönelik dağıtımlarda özellikle yararlı olan kısıtlama yapılandırma seçeneklerine sahiptir.

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> Sınıfının <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> özelliğinde kısıtlamaları ayarlayın. Özelliği `Limits` , <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> sınıfının bir örneğini barındırır.

Aşağıdaki örnekler <xref:Microsoft.AspNetCore.Server.Kestrel.Core> ad alanını kullanır:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

Aşağıdaki örneklerde C# kodunda yapılandırılan Kestrel seçenekleri de bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir. Örneğin, dosya yapılandırma sağlayıcısı bir *appSettings. JSON* veya appSettings 'ten Kestrel yapılandırmasını yükleyebilir *. { Environment}. JSON* dosyası:

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

Aşağıdaki yaklaşımlardan **birini** kullanın:

* Kestrel 'i `Startup.ConfigureServices`yapılandırma:

  1. `Startup` Sınıfına bir örneği `IConfiguration` ekleyin. Aşağıdaki örnek, eklenen yapılandırmanın `Configuration` özelliğe atandığını varsayar.
  2. İçinde `Startup.ConfigureServices`, yapılandırma `Kestrel` bölümünü Kestrel 'in yapılandırmasına yükleyin:

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

* Ana bilgisayarı oluştururken Kestrel yapılandırma:

  *Program.cs*' de, yapılandırma `Kestrel` bölümünü Kestrel 'in yapılandırmasına yükleyin:

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

Yukarıdaki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.

### <a name="keep-alive-timeout"></a>Etkin tut zaman aşımı

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

[Etkin tutma zaman aşımını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar. Varsayılan olarak 2 dakikadır.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a>İstemci bağlantıları üst sınırı

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

En fazla eş zamanlı açık TCP bağlantısı sayısı tüm uygulama için aşağıdaki kodla ayarlanabilir:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

HTTP veya HTTPS 'den başka bir protokole (örneğin, bir WebSockets isteğinde) yükseltilen bağlantılara yönelik ayrı bir sınır vardır. Bir bağlantı yükseltildikten sonra, bu `MaxConcurrentConnections` sınıra göre sayılmaz.

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

En fazla bağlantı sayısı, varsayılan olarak sınırsız (null).

### <a name="maximum-request-body-size"></a>En büyük istek gövdesi boyutu

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

Varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu değer yaklaşık 28,6 MB 'tır.

ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yaklaşım, bir eylem yönteminde <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> özniteliğini kullanmaktır:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Her istekte uygulama için kısıtlamanın nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

Ara yazılım içindeki belirli bir istek üzerindeki ayarı geçersiz kılın:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Uygulama isteği okumaya başladıktan sonra bir istek üzerindeki sınırı yapılandırırsa, bir özel durum oluşturulur. Özelliğin salt okuma `IsReadOnly` durumunda olup olmadığını belirten bir özellik vardır. Bu, sınırı yapılandırmanın çok geç olduğunu gösterir. `MaxRequestBodySize`

Bir uygulama [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)arkasında [çalıştırıldığında](xref:host-and-deploy/iis/index#out-of-process-hosting-model) , IIS sınırı zaten ayarladığı için Kestrel 'nin istek gövdesi boyut sınırı devre dışı bırakılır.

### <a name="minimum-request-body-data-rate"></a>En az istek gövdesi veri hızı

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel bayt/saniye cinsinden belirtilen fiyata ulaşan her saniye sonra denetler. Oran en düşük değerin altına düşerse bağlantı zaman aşımına uğrar. Yetkisiz kullanım süresi, Kestrel 'in istemciye gönderme oranını en düşük süreye kadar artırabilme Bu süre boyunca fiyat denetlenmez. Yetkisiz kullanım süresi, TCP yavaş başlatma nedeniyle başlangıçta verileri yavaş bir hızda gönderen bağlantıların bırakılmasını önlemeye yardımcı olur.

Varsayılan en düşük oran, 5 saniyelik bir yetkisiz kullanım süresi ile 240 bayt/saniye olur.

Yanıt için bir minimum oran da geçerlidir. İstek sınırını ayarlamaya yönelik kod ve yanıt sınırı, özellik ve arabirim adlarında olduğu gibi aynı `RequestBody` `Response` olur.

*Program.cs*içinde en düşük veri hızlarının nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:

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

### <a name="request-headers-timeout"></a>İstek üst bilgileri zaman aşımı

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Sunucunun istek üst bilgilerini alması için harcadığı en uzun süreyi alır veya ayarlar. Varsayılan değer 30 saniyedir.

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

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>istek ve yanıt için zaman uyumlu g/ç 'ye izin verilip verilmediğini denetler. Varsayılan değer `true`.

> [!WARNING]
> Çok sayıda engelleme zaman uyumlu g/ç işlemi, iş parçacığı havuzuna yol açabilir, bu da uygulamanın yanıt vermemesine neden olur. Yalnızca zaman `AllowSynchronousIO` uyumsuz g/ç desteklemeyen bir kitaplık kullanırken etkinleştirin.

Aşağıdaki örnek, zaman uyumlu g/ç 'yi devre dışı bırakır:

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

Diğer Kestrel seçenekleri ve limitleri hakkında daha fazla bilgi için bkz.:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a>Uç nokta yapılandırması

Varsayılan olarak, ASP.NET Core bağlar:

* `http://localhost:5000`
* `https://localhost:5001`(bir yerel geliştirme sertifikası varsa)

Kullanarak URL 'Leri belirtin:

* `ASPNETCORE_URLS`ortam değişkeni.
* `--urls`komut satırı bağımsız değişkeni.
* `urls`Ana bilgisayar yapılandırma anahtarı.
* `UseUrls`genişletme yöntemi.

Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS). Değeri noktalı virgülle ayrılmış bir liste olarak yapılandırın (örneğin, `"Urls": "http://localhost:8000;http://localhost:8001"`).

Bu yaklaşımlar hakkında daha fazla bilgi için [sunucu URL 'leri](xref:fundamentals/host/web-host#server-urls) ve [geçersiz kılma yapılandırması](xref:fundamentals/host/web-host#override-configuration)bölümüne bakın.

Geliştirme sertifikası oluşturuldu:

* [.NET Core SDK](/dotnet/core/sdk) yüklendiği zaman.
* [Geliştirme-CERT aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.

Bazı tarayıcılarda yerel geliştirme sertifikasına güvenmek için açık izin verilmesi gerekir.

Proje şablonları, uygulamaları HTTPS üzerinde varsayılan olarak çalışacak şekilde yapılandırır ve [https yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.

URL <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> öneklerini <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> ve bağlantı <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> noktalarını Kestrel için yapılandırmak üzere üzerinde çağrı veya Yöntemler.

`UseUrls`, `--urls` komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı ve `ASPNETCORE_URLS` ortam değişkeni de çalışır, ancak bu bölümün Ilerleyen kısımlarında belirtilen sınırlamalara sahiptir (https uç noktası yapılandırması için varsayılan sertifika kullanılabilir olmalıdır).

`KestrelServerOptions`yapılandırmada

### <a name="configureendpointdefaultsactionlistenoptions"></a>ConfigureEndpointDefaults Varsayılanları (\<eylem listenoptions>)

Belirtilen her bir `Action` uç nokta için çalıştırılacak bir yapılandırma belirtir. Birden `ConfigureEndpointDefaults` çok kez çağırma önceki `Action`s 'yi son `Action` belirtilen ile değiştirir.

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** Çağrılmadan <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> önce çağırarak oluşturulan uç noktalara varsayılan değer uygulanmaz.

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a>ConfigureHttpsDefaults (ACTION\<httpsconnectionadapteroptions>)

Her HTTPS uç `Action` noktası için çalıştırılacak bir yapılandırma belirtir. Birden `ConfigureHttpsDefaults` çok kez çağırma önceki `Action`s 'yi son `Action` belirtilen ile değiştirir.

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** Çağrılmadan <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> önce çağırarak oluşturulan uç noktalara varsayılan değer uygulanmaz.

### <a name="configureiconfiguration"></a>Yapılandırma (Iconation)

Bir <xref:Microsoft.Extensions.Configuration.IConfiguration> as girişi alan Kestrel ayarlamak için bir yapılandırma yükleyicisi oluşturur. Yapılandırma, Kestrel için yapılandırma bölümünün kapsamına alınmalıdır.

### <a name="listenoptionsusehttps"></a>ListenOptions. UseHttps

Kestrel 'i HTTPS kullanacak şekilde yapılandırın.

`ListenOptions.UseHttps`uzantılardan

* `UseHttps`&ndash; Kestrel 'i varsayılan sertifikayla HTTPS kullanacak şekilde yapılandırın. Varsayılan sertifika yapılandırılmamışsa bir özel durum oluşturur.
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

`ListenOptions.UseHttps`parametrelere

* `filename`, uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yolu ve dosya adıdır.
* `password`X. 509.440 sertifika verilerine erişmek için parola gereklidir.
* `configureOptions`, `HttpsConnectionAdapterOptions`öğesini `Action` yapılandırmak için kullanılır. Döndürür `ListenOptions`.
* `storeName`, sertifikanın yükleneceği sertifika deposudur.
* `subject`, sertifika için konu adıdır.
* `allowInvalid`geçersiz sertifikaların, otomatik olarak imzalanan sertifikalar gibi göz önünde bulundurulmayacağını gösterir.
* `location`, sertifikanın yükleneceği mağaza konumudur.
* `serverCertificate`, X. 509.440 sertifikasıdır.

Üretimde HTTPS 'nin açıkça yapılandırılması gerekir. En azından, varsayılan bir sertifika sağlanmalıdır.

Daha sonra açıklanan desteklenen yapılandırma:

* Yapılandırma yok
* Varsayılan sertifikayı yapılandırmadan Değiştir
* Koddaki varsayılanları değiştirme

*Yapılandırma yok*

Kestrel tarihinde `http://localhost:5000` dinler ve `https://localhost:5001` (varsayılan bir sertifika varsa).

<a name="configuration"></a>

*Varsayılan sertifikayı yapılandırmadan Değiştir*

`CreateDefaultBuilder`Kestrel `Configure(context.Configuration.GetSection("Kestrel"))` yapılandırmasını yüklemek için varsayılan olarak çağırır. Varsayılan bir HTTPS uygulama ayarları yapılandırma şeması Kestrel için kullanılabilir. Disk üzerindeki bir dosyadan ya da bir sertifika deposundan kullanılacak URL 'Ler ve Sertifikalar dahil olmak üzere birden çok uç nokta yapılandırın.

Aşağıdaki *appSettings. JSON* örneğinde:

* Geçersiz sertifikaların kullanılmasına izin vermek `true` Için **allowwınvalid** ' i ayarlayın (örneğin, otomatik olarak imzalanan sertifikalar).
* Bir sertifika belirtmeyen herhangi bir HTTPS uç noktası (aşağıdaki örnekte bulunan**httpsdefaultcert** ), **Sertifikalar** > **varsayılan** veya geliştirme sertifikası altında tanımlanan sertifikaya geri döner.

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

Herhangi bir sertifika düğümü için **yol** ve **parola** kullanmanın alternatifi sertifika deposu alanlarını kullanarak sertifikayı belirtmektir. Örneğin, **sertifika** > **varsayılan** sertifikası şu şekilde belirtilebilir:

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

Şema notları:

* Uç nokta adları büyük/küçük harfe duyarlıdır. Örneğin, `HTTPS` ve `Https` geçerlidir.
* Her `Url` uç nokta için parametresi gereklidir. Bu parametrenin biçimi, en üst düzey `Urls` yapılandırma parametresiyle aynıdır, ancak tek bir değerle sınırlı olur.
* Bu uç noktalar, üst düzey `Urls` yapılandırmada tanımlananlar yerine bunlara ekleme yerine bunların yerini alır. Kullanılarak `Listen` kodda tanımlanan uç noktalar yapılandırma bölümünde tanımlanan uç noktalar ile birikimlidir.
* Bu `Certificate` bölüm isteğe bağlıdır. `Certificate` Bölüm belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır. Kullanılabilir varsayılan değer yoksa, sunucu bir özel durum oluşturur ve başlayamaz.
* Bu `Certificate` bölüm hem **yol**&ndash;**parolasını** hem de **Konu**&ndash;**deposu** sertifikalarını destekler.
* Herhangi bir sayıda uç nokta, bağlantı noktası çakışmalarına neden olmadıkları sürece bu şekilde tanımlanabilir.
* `options.Configure(context.Configuration.GetSection("{SECTION}"))`yapılandırılmış bir `KestrelConfigurationLoader` uç noktanın `.Endpoint(string name, listenOptions => { })` ayarlarını tamamlamak için kullanılabilecek bir yöntemi olan bir döndürür:

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

`KestrelServerOptions.ConfigurationLoader`, tarafından <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>sağlana gibi var olan yükleyicisindeki yinelemeye devam etmek için doğrudan erişilebilir.

* Her uç noktanın yapılandırma bölümü, özel ayarların okunabilmesi için `Endpoint` yöntemindeki seçeneklerde kullanılabilir.
* Birden çok yapılandırma, başka bir bölümle `options.Configure(context.Configuration.GetSection("{SECTION}"))` yeniden çağırarak yüklenebilir. Önceki örneklerde açıkça çağrılmadığı takdirde `Load` , yalnızca son yapılandırma kullanılır. Metapackage, varsayılan yapılandırma `Load` bölümünün değiştirilmesini sağlayacak şekilde çağırmıyor.
* `KestrelConfigurationLoader`API `Listen` ailesini aşırı yükleme `KestrelServerOptions` olarak `Endpoint` yansıtır, bu nedenle kod ve yapılandırma uç noktaları aynı yerde yapılandırılabilir. Bu aşırı yüklemeler adları kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları kullanır.

*Koddaki varsayılanları değiştirme*

`ConfigureEndpointDefaults`ve `ConfigureHttpsDefaults` , önceki senaryoda belirtilen varsayılan sertifikayı geçersiz kılma `ListenOptions` dahil `HttpsConnectionAdapterOptions`, ve için varsayılan ayarları değiştirmek üzere kullanılabilir. `ConfigureEndpointDefaults`ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.

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

*SNı için Kestrel desteği*

[Sunucu adı belirtme (SNı)](https://tools.ietf.org/html/rfc6066#section-3) , aynı IP adresi ve bağlantı noktası üzerinde birden fazla etki alanını barındırmak için kullanılabilir. SNı 'nin çalışması için, istemci, TLS el sıkışması sırasında güvenli oturum ana bilgisayar adını, sunucunun doğru sertifikayı sağlayabilmesi için gönderir. İstemci, TLS anlaşmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için bulunan sertifikayı kullanır.

Kestrel, `ServerCertificateSelector` geri çağırma aracılığıyla SNI destekler. Geri çağırma, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesini sağlamak için bağlantı başına bir kez çağrılır.

SNı desteği şunları gerektirir:

* Hedef Framework `netcoreapp2.1` veya sonraki sürümlerde çalışıyor. `net461` Veya sonraki sürümlerde, geri çağırma çağrılır, ancak her `name` zaman `null`olur. Ayrıca `name` , ISTEMCI `null` , TLS el sıkışmasının ana bilgisayar adı parametresini sağlamıyorsa de olur.
* Tüm Web siteleri aynı Kestrel örneğinde çalışır. Kestrel, bir IP adresi ve bağlantı noktasının bir ters proxy olmadan birden çok örnek arasında paylaşılmasını desteklemez.

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

Bir <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> bağlantıda bayt düzeyinde Iletişim Için hata ayıklama düzeyi günlüklerini yayma çağrısı. Bağlantı günlüğü, TLS şifreleme ve proxy 'nin arkasındaki gibi alt düzey iletişimde sorunları gidermeye yardımcı olur. `UseConnectionLogging` Daha önce `UseHttps`yerleştirilmişse, şifrelenmiş trafik günlüğe kaydedilir. `UseConnectionLogging` Öğesinden sonra `UseHttps`yerleştirilmişse, şifresi çözülmüş trafik günlüğe kaydedilir.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a>TCP yuvasına bağlama

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> YÖNTEMI bir TCP yuvasına bağlanır ve bir seçenek lambda X. 509.440 sertifika yapılandırmasına izin verir:

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

Örnek, HTTPS 'yi ile <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>bir uç nokta için yapılandırır. Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak üzere aynı API 'yi kullanın.

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a>UNIX yuvasına bağlama

Aşağıdaki örnekte gösterildiği gibi NGINX ile gelişmiş performans için ile birlikte <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> bir UNIX yuvası dinleyin:

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

* NGINX confiuguration dosyasında `server`  >  `location`  >  `proxy_pass` , girdisini olarak `http://unix:/tmp/{KESTREL SOCKET}:/;`ayarlayın. `{KESTREL SOCKET}`, için <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> belirtilen yuvanın adıdır (örneğin, `kestrel-test.sock` önceki örnekte).
* Yuvanın NGINX tarafından yazılabilir olduğundan emin olun (örneğin, `chmod go+w /tmp/kestrel-test.sock`). 

### <a name="port-0"></a>Bağlantı noktası 0

Bağlantı noktası numarası `0` belirtildiğinde, Kestrel dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır. Aşağıdaki örnek, çalışma zamanında Kestrel gerçekte hangi bağlantı noktasının gerçekten bağlandığını nasıl belirleyeceğini göstermektedir:

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

Uygulama çalıştırıldığında konsol penceresi çıkışı, uygulamanın erişilebileceği dinamik bağlantı noktasını gösterir:

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a>Sınırlamalar

Aşağıdaki yaklaşımlar ile uç noktaları yapılandırın:

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* `--urls`komut satırı bağımsız değişkeni
* `urls`Ana bilgisayar yapılandırma anahtarı
* `ASPNETCORE_URLS`ortam değişkeni

Bu yöntemler, kodun Kestrel dışındaki sunucularla çalışmasını sağlamak için yararlıdır. Ancak, aşağıdaki sınırlamalara dikkat edin:

* HTTPS uç noktası yapılandırmasında varsayılan bir sertifika sağlanmadığı sürece HTTPS bu yaklaşımlar ile kullanılamaz (örneğin, bu konunun önceki kısımlarında `KestrelServerOptions` gösterildiği gibi yapılandırma veya yapılandırma dosyası kullanılıyor).
* Hem `Listen` hem `UseUrls` de yaklaşımları aynı anda kullanıldığında, `Listen` uç noktalar `UseUrls` uç noktaları geçersiz kılar.

### <a name="iis-endpoint-configuration"></a>IIS uç nokta yapılandırması

IIS kullanırken, IIS geçersiz kılma bağlamaları için URL bağlamaları veya `Listen` `UseUrls`ya da tarafından ayarlanır. Daha fazla bilgi için [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.

## <a name="transport-configuration"></a>Aktarım yapılandırması

ASP.NET Core 2,1 sürümü ile Kestrel 'in varsayılan taşıması artık libuv ' d i temel değildir ancak bunun yerine yönetilen yuvaları temel alır. Bu, çağrıyı <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> yapan ve aşağıdaki paketlerden birine bağlı olan ASP.NET Core 2,0 2,1 uygulamalarının önemli bir değişikliği olur:

* [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (doğrudan paket başvurusu)
* [Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

Libuv kullanımını gerektiren projeler için:

* Uygulamanın proje dosyasına [Microsoft. AspNetCore. Server. Kestrel. Transport. libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) paketi için bir bağımlılık ekleyin:

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

, `UseUrls` `--urls` Komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı veya `ASPNETCORE_URLS` ortam değişkeni kullanılırken, URL önekleri aşağıdaki biçimlerden birinde olabilir.

Yalnızca HTTP URL ön ekleri geçerlidir. Kestrel, kullanılarak `UseUrls`URL BAĞLAMALARı yapılandırılırken https 'yi desteklemez.

* Bağlantı noktası numarası olan IPv4 adresi

  ```
  http://65.55.39.10:80/
  ```

  `0.0.0.0`Tüm IPv4 adreslerine bağlanan özel bir durumdur.

* Bağlantı noktası numarasına sahip IPv6 adresi

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  `[::]`, IPv4 `0.0.0.0`'un IPv6 eşdeğeridir.

* Bağlantı noktası numarası olan ana bilgisayar adı

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  , Ve `+`ana `*`bilgisayar adları özel değildir. Geçerli bir IP adresi olarak tanınmayan veya `localhost` tüm IPv4 ve IPv6 IP 'lerine bağlanan her şey. Farklı ana bilgisayar adlarını aynı bağlantı noktasında farklı ASP.NET Core uygulamalarına bağlamak için, [http. sys](xref:fundamentals/servers/httpsys) veya IIS, NGINX veya Apache gibi bir ters proxy sunucusu kullanın.

  > [!WARNING]
  > Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.

* Port `localhost` numarası veya bağlantı noktası numarası ile geri döngü IP 'si olan ana bilgisayar adı

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  `localhost` Belirtildiğinde, Kestrel hem IPv4 hem de IPv6 geri döngü arabirimlerini bağlamaya çalışır. İstenen bağlantı noktası, herhangi bir geri döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılamaz. Herhangi bir geri döngü arabirimi başka bir nedenle kullanılamıyorsa (genellikle IPv6 desteklenmediği için), Kestrel bir uyarı kaydeder.

## <a name="host-filtering"></a>Konak filtreleme

Kestrel gibi önekleri temel alarak yapılandırmayı desteklese `http://example.com:5000`de, Kestrel büyük ölçüde ana bilgisayar adını yoksayar. Ana `localhost` bilgisayar, geri döngü adreslerine bağlama için kullanılan özel bir durumdur. Açık IP adresi dışındaki tüm ana bilgisayar tüm genel IP adreslerine bağlanır. `Host`Üstbilgiler doğrulanmadı.

Geçici bir çözüm olarak, ana bilgisayar filtreleme ara yazılımı kullanın. Ana bilgisayar filtreleme ara yazılımı, [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 veya 2,2) ' de yer alan [Microsoft. Aspnetcore. hostfiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır. Ara yazılım tarafından <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>eklenir ve şunları çağırır <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

Ana bilgisayar filtreleme ara yazılımı varsayılan olarak devre dışıdır. Ara yazılımı etkinleştirmek için `AllowedHosts` *appSettings. JSON*/appSettings içinde bir anahtar tanımlayın *.\< EnvironmentName>. JSON*. Değer, bağlantı noktası numaraları olmayan ana bilgisayar adlarının noktalı virgülle ayrılmış listesidir:

*appSettings. JSON*:

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> [Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer) da bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçenek içerir. İletilen üstbilgiler ara yazılımı ve ana bilgisayar filtreleme ara yazılımı, farklı senaryolar için benzer işlevlere sahiptir. Iletilen `AllowedHosts` üstbilgiler ara yazılımı ile, istekler ters bir `Host` ara sunucu veya yük dengeleyici ile iletilirken üst bilgi korunurken, bu işlem için uygun bir ayar vardır. Ana `AllowedHosts` bilgisayar filtreleme ara yazılımı ile ayarlama, Kestrel herkese açık bir uç sunucu olarak veya `Host` üst bilgi doğrudan iletildiğinde kullanılır.
>
> Iletilen üstbilgiler ara yazılımı hakkında daha fazla bilgi için <xref:host-and-deploy/proxy-load-balancer>bkz..

::: moniker-end

## <a name="http11-request-draining"></a>HTTP/1.1 istek boşaltma

HTTP bağlantılarının açılması zaman alabilir. HTTPS için de kaynak kullanımı yoğun bir işlemdir. Bu nedenle Kestrel, HTTP/1.1 protokolü başına bağlantıları yeniden kullanmaya çalışır. Bağlantının yeniden kullanılmasına izin vermek için bir istek gövdesi tam olarak tüketilmelidir. Uygulama, sunucunun yeniden yönlendirme veya 404 yanıtı döndürdüğü bir `POST` istek gibi her zaman istek gövdesini tüketmez. `POST`Yeniden yönlendirme durumunda:

* İstemci, `POST` verilerin bir bölümünü zaten göndermiş olabilir.
* Sunucu 301 yanıtını yazar.
* Önceki istek gövdesinden gelen `POST` veriler tam olarak okunana kadar bağlantı yeni bir istek için kullanılamaz.
* Kestrel, istek gövdesini boşaltmaya çalışır. İstek gövdesini boşaltma işlemi işlemeden verileri okuma ve atma anlamına gelir.

Boşaltma işlemi bağlantının yeniden kullanılmasına izin verme ve kalan verilerin boşaltıma süresi arasında bir ilerleme gerçekleştirir:

* Boşaltma, yapılandırılabilir olmayan beş saniyelik bir zaman aşımına sahip olur.
* `Content-Length` Veya `Transfer-Encoding` üstbilgisi tarafından belirtilen tüm veriler zaman aşımından önce okunmadıysa bağlantı kapatılır.

Bazen, yanıtı yazmadan önce veya sonra isteği hemen sonlandırmak isteyebilirsiniz. Örneğin, istemciler sınırlı verilerin büyük bir düzeyi olabilir, bu nedenle karşıya yüklenen verileri sınırlamak bir öncelik olabilir. Bu tür durumlarda bir isteği sonlandırmak için bir denetleyici, Razor sayfa veya ara yazılım aracılığıyla [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) ' ı çağırın.

Çağırmanın `Abort`uyarıları vardır:

* Yeni bağlantı oluşturma yavaş ve pahalı olabilir.
* İstemcinin bağlantı kapanmadan önce yanıtı okuduğunuzdan emin olmaz.
* Çağırma `Abort` , yaygın hatalara değil önemli hata durumları için nadir ve ayrılmış olmalıdır.
  * Yalnızca belirli `Abort` bir sorunun çözülmesi gerektiğinde çağırın. Örneğin, kötü amaçlı `Abort` istemciler veri almaya `POST` çalışıyorsa veya istemci kodunda büyük veya çok sayıda isteğe neden olan bir hata olduğunda çağırın.
  * HTTP 404 `Abort` (bulunamadı) gibi yaygın hata durumları için çağrı yapmayın.

Çağrılmadan `Abort` önce [HttpResponse. tamamlana eşitlemesini](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) çağırmak sunucunun yanıtı yazmayı tamamlamasını sağlar. Ancak, istemci davranışı tahmin edilebilir değildir ve bağlantı durdurulmadan önce yanıtı okuyamayabilir.

Bu işlem HTTP/2 için farklıdır çünkü protokol, bağlantıyı kapatmadan bağımsız istek akışlarını iptal etme işlemini destekler. Beş saniyelik boşaltma zaman aşımı uygulanmaz. Yanıt tamamlandıktan sonra herhangi bir okunmamış istek gövdesi verisi varsa, sunucu bir HTTP/2 RST çerçevesi gönderir. Ek istek gövdesi veri çerçeveleri yok sayılır.

Mümkünse, istemcilerin [Beklenen: 100-Continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) istek üst bilgisini kullanabilmesi ve istek gövdesini göndermeden önce sunucunun yanıt vermesini beklemek daha iyidir. Bu, istemciye gereksiz verileri göndermeden önce yanıtı İnceleme ve iptal etme olanağı sunar.

## <a name="additional-resources"></a>Ek kaynaklar

* Linux üzerinde UNIX yuvaları kullanırken, yuva uygulama kapatılırken otomatik olarak silinmez. Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/aspnetcore/issues/14134)bakın.
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [RFC 7230: Ileti sözdizimi ve yönlendirme (Bölüm 5,4: Ana bilgisayar)](https://tools.ietf.org/html/rfc7230#section-5.4)
