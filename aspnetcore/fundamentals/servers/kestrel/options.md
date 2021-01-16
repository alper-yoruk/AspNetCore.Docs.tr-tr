---
title: ASP.NET Core Kestrel Web sunucusu seçeneklerini yapılandırma
author: rick-anderson
description: ASP.NET Core platformlar arası Web sunucusu olan Kestrel için seçenekleri yapılandırma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel/options
ms.openlocfilehash: 198d509a68224077d3764cc836121b89e96c6853
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253981"
---
# <a name="configure-options-for-the-aspnet-core-kestrel-web-server"></a>ASP.NET Core Kestrel Web sunucusu seçeneklerini yapılandırma

Kestrel Web sunucusu, Internet 'e yönelik dağıtımlarda özellikle yararlı olan kısıtlama yapılandırma seçeneklerine sahiptir.

Çağrıldıktan sonra ek yapılandırma sağlamak için `ConfigureWebHostDefaults` şunu kullanın `ConfigureKestrel` :

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

Sınıfının özelliğinde kısıtlamaları ayarlayın <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> . `Limits`Özelliği, sınıfının bir örneğini barındırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> .

Aşağıdaki örnekler <xref:Microsoft.AspNetCore.Server.Kestrel.Core> ad alanını kullanır:

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

Bu makalenin ilerleyen kısımlarında gösterilen örneklerde, Kestrel seçenekleri C# kodunda yapılandırılır. Ayrıca, Kestrel seçenekleri bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir. Örneğin, [dosya yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider) Kestrel yapılandırmasını bir *appsettings.json* veya appSettings 'ten yükleyebilir *. {1} Environment}. JSON* dosyası:

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
> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> ve [uç nokta yapılandırması](xref:fundamentals/servers/kestrel/endpoints) yapılandırma sağlayıcılarından yapılandırılabilir. Kalan Kestrel yapılandırması C# kodunda yapılandırılmalıdır.

Aşağıdaki yaklaşımlardan **birini** kullanın:

* Kestrel 'i yapılandırma `Startup.ConfigureServices` :

  1. Sınıfına bir örneği `IConfiguration` ekleyin `Startup` . Aşağıdaki örnek, eklenen yapılandırmanın özelliğe atandığını varsayar `Configuration` .
  2. İçinde `Startup.ConfigureServices` , `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:

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

  *Program.cs*' de, `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:

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

## <a name="general-limits"></a>Genel sınırlar

### <a name="keep-alive-timeout"></a>Etkin tut zaman aşımı

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

[Etkin tutma zaman aşımını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar. Varsayılan olarak 2 dakikadır.

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a>İstemci bağlantıları üst sınırı

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

En fazla eş zamanlı açık TCP bağlantısı sayısı tüm uygulama için aşağıdaki kodla ayarlanabilir:

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

HTTP veya HTTPS 'den başka bir protokole (örneğin, bir WebSockets isteğinde) yükseltilen bağlantılara yönelik ayrı bir sınır vardır. Bir bağlantı yükseltildikten sonra, bu sınıra göre sayılmaz `MaxConcurrentConnections` .

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

En fazla bağlantı sayısı, varsayılan olarak sınırsız (null).

### <a name="maximum-request-body-size"></a>En büyük istek gövdesi boyutu

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

Varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu değer yaklaşık 28,6 MB 'tır.

ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yaklaşım, <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

Her istekte uygulama için kısıtlamanın nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

Ara yazılım içindeki belirli bir istek üzerindeki ayarı geçersiz kılın:

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

Uygulama isteği okumaya başladıktan sonra bir istek üzerindeki sınırı yapılandırırsa, bir özel durum oluşturulur. `IsReadOnly`Özelliğin salt okuma durumunda olup olmadığını belirten bir özellik vardır. Bu, `MaxRequestBodySize` sınırı yapılandırmanın çok geç olduğunu gösterir.

Bir uygulama [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)arkasında [işlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model) çalıştığında, Kestrel 'nin istek gövdesi boyut sınırı devre dışıdır. IIS sınırı zaten ayarlıyor.

### <a name="minimum-request-body-data-rate"></a>En az istek gövdesi veri hızı

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

Kestrel bayt/saniye cinsinden belirtilen fiyata ulaşan her saniye sonra denetler. Oran en düşük değerin altına düşerse bağlantı zaman aşımına uğrar. Yetkisiz kullanım süresi, Kestrel istemcisinin, gönderme hızını en düşüye artırması için izin verir. Bu süre boyunca fiyat denetlenmez. Yetkisiz kullanım süresi, TCP yavaş başlatma nedeniyle başlangıçta verileri yavaş bir hızda gönderen bağlantıların bırakılmasını önlemeye yardımcı olur.

Varsayılan en düşük oran, 5 saniyelik bir yetkisiz kullanım süresi ile 240 bayt/saniye olur.

Yanıt için bir minimum oran da geçerlidir. İstek sınırını ayarlamaya yönelik kod ve yanıt sınırı, `RequestBody` `Response` özellik ve arabirim adlarında olduğu gibi aynı olur.

*Program.cs* içinde en düşük veri hızlarının nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

Ara yazılım içindeki istek başına düşen minimum hız sınırlarını geçersiz kılın:

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>Önceki örnekte başvurulan, `HttpContext.Features` http/2 isteklerinde ' de yer almıyor. İstek çoğullama için protokolün desteği nedeniyle, istek başına hız sınırlarını değiştirme, HTTP/2 için genellikle desteklenmez. Ancak, http <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> `HttpContext.Features` /2 istekleri için yine de vardır, çünkü okuma hızı sınırı,  `IHttpMinRequestBodyDataRateFeature.MinDataRate` `null` bir http/2 isteği için de olarak ayarlanarak tamamen istek temelli olarak devre dışı bırakılabilir. `IHttpMinRequestBodyDataRateFeature.MinDataRate`Bunun dışında bir değere ayarlamaya çalışılması veya BIR `null` `NotSupportedException` http/2 isteği verilmeye neden olur.

`KestrelServerOptions.Limits`Http/1. x ve http/2 bağlantılarına hala uygulanan sunucu genelindeki hız sınırları.

### <a name="request-headers-timeout"></a>İstek üst bilgileri zaman aşımı

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

Sunucunun istek üst bilgilerini alması için harcadığı en uzun süreyi alır veya ayarlar. Varsayılan değer 30 saniyedir.

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

## <a name="http2-limits"></a>HTTP/2 sınırları

### <a name="maximum-streams-per-connection"></a>Bağlantı başına en fazla akış

`Http2.MaxStreamsPerConnection` HTTP/2 bağlantısı başına eşzamanlı istek akışı sayısını sınırlar. Fazlalık akışlar reddedildi.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

Varsayılan değer 100’dür.

### <a name="header-table-size"></a>Üst bilgi tablosu boyutu

HPACK kod çözücüsü HTTP/2 bağlantıları için HTTP üstbilgilerini açar. `Http2.HeaderTableSize` HPACK kod çözücüsünün kullandığı üst bilgi sıkıştırma tablosunun boyutunu sınırlandırır. Değer sekizli cinsinden sağlanır ve sıfırdan büyük olmalıdır (0).

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

Varsayılan değer 4096 ' dir.

### <a name="maximum-frame-size"></a>En büyük çerçeve boyutu

`Http2.MaxFrameSize` sunucu tarafından alınan veya gönderilen HTTP/2 bağlantı çerçevesi yükünün izin verilen en büyük boyutunu belirtir. Değer sekizli cinsinden sağlanır ve 2 ^ 14 (16.384) ile 2 ^ 24-1 (16.777.215) arasında olmalıdır.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

Varsayılan değer 2 ^ 14 ' dir (16.384).

### <a name="maximum-request-header-size"></a>En fazla istek üst bilgi boyutu

`Http2.MaxRequestHeaderFieldSize` istek üst bilgisi değerlerinin sekizlisi cinsinden izin verilen en büyük boyutu belirtir. Bu sınır, sıkıştırılmış ve sıkıştırılmamış temsillerinde hem ad hem de değer için geçerlidir. Değer sıfırdan büyük (0) olmalıdır.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

Varsayılan değer 8.192 ' dir.

### <a name="initial-connection-window-size"></a>İlk bağlantı pencere boyutu

`Http2.InitialConnectionWindowSize` sunucu, bağlantı başına tüm istekler (akışlar) genelinde toplanan tek seferde sunucunun arabelleğe aldığı en fazla istek gövde verilerini bayt cinsinden gösterir. İstekleri ile de sınırlıdır `Http2.InitialStreamWindowSize` . Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

Varsayılan değer 128 KB 'tır (131.072).

### <a name="initial-stream-window-size"></a>İlk akış pencere boyutu

`Http2.InitialStreamWindowSize` sunucu, istek başına bir kez (Stream) arabelleğe alınan en fazla istek gövde verilerini bayt cinsinden gösterir. İstekleri ile de sınırlıdır `Http2.InitialConnectionWindowSize` . Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

Varsayılan değer 96 KB 'tır (98.304).

### <a name="http2-keep-alive-ping-configuration"></a>HTTP/2 canlı tutmayı ping yapılandırması

Kestrel, bağlı istemcilere HTTP/2 pingler gönderecek şekilde yapılandırılabilir. HTTP/2 ping işlemleri birden çok amaca hizmet eder:

* Boştaki bağlantıları canlı tutun. Bazı istemciler ve proxy sunucular, boşta olan bağlantıları kapatır. HTTP/2 pingler bir bağlantıda etkinlik olarak değerlendirilir ve bağlantının boşta olarak kapatılmasını önler.
* Sağlıksız bağlantıları kapatın. İstemcinin yapılandırılan süre içinde canlı tut ping komutuna yanıt vermediği bağlantılar sunucu tarafından kapalıdır.

HTTP/2 canlı tut ping işlemleri ile ilgili iki yapılandırma seçeneği vardır:

* `Http2.KeepAlivePingInterval` , `TimeSpan` ping aralığını yapılandıran bir. Sunucu, bu süre boyunca herhangi bir çerçeve almazsa istemciye canlı bir ping gönderir. Bu seçenek olarak ayarlandığında Canlı ping pingler devre dışı bırakılır `TimeSpan.MaxValue` . `TimeSpan.MaxValue` varsayılan değerdir.
* `Http2.KeepAlivePingTimeout` , `TimeSpan` ping zaman aşımını yapılandıran bir. Sunucu yanıt ping gibi bir çerçeve almazsa, bu zaman aşımı sırasında bağlantı kapatılır. Bu seçenek olarak ayarlandığında Canlı kalma zaman aşımını devre dışı bırakın `TimeSpan.MaxValue` . Varsayılan değer 20 saniyedir.

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

## <a name="other-options"></a>Diğer seçenekler

### <a name="synchronous-io"></a>Zaman Uyumlu G/Ç

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> istek ve yanıt için zaman uyumlu g/ç 'ye izin verilip verilmediğini denetler. `false` varsayılan değerdir.

> [!WARNING]
> Çok sayıda engelleme zaman uyumlu g/ç işlemi, iş parçacığı havuzuna yol açabilir, bu da uygulamanın yanıt vermemesine neden olur. Yalnızca `AllowSynchronousIO` zaman uyumsuz g/ç desteklemeyen bir kitaplık kullanırken etkinleştirin.

Aşağıdaki örnek, zaman uyumlu g/ç 'yi sunar:

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

Diğer Kestrel seçenekleri ve limitleri hakkında daha fazla bilgi için bkz.:

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>
