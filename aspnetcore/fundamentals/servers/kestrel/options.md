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
# <a name="configure-options-for-the-aspnet-core-kestrel-web-server"></a><span data-ttu-id="4043e-103">ASP.NET Core Kestrel Web sunucusu seçeneklerini yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4043e-103">Configure options for the ASP.NET Core Kestrel web server</span></span>

<span data-ttu-id="4043e-104">Kestrel Web sunucusu, Internet 'e yönelik dağıtımlarda özellikle yararlı olan kısıtlama yapılandırma seçeneklerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="4043e-104">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="4043e-105">Çağrıldıktan sonra ek yapılandırma sağlamak için `ConfigureWebHostDefaults` şunu kullanın `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="4043e-105">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

<span data-ttu-id="4043e-106">Sınıfının özelliğinde kısıtlamaları ayarlayın <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> .</span><span class="sxs-lookup"><span data-stu-id="4043e-106">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="4043e-107">`Limits`Özelliği, sınıfının bir örneğini barındırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> .</span><span class="sxs-lookup"><span data-stu-id="4043e-107">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="4043e-108">Aşağıdaki örnekler <xref:Microsoft.AspNetCore.Server.Kestrel.Core> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="4043e-108">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="4043e-109">Bu makalenin ilerleyen kısımlarında gösterilen örneklerde, Kestrel seçenekleri C# kodunda yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="4043e-109">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="4043e-110">Ayrıca, Kestrel seçenekleri bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="4043e-110">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="4043e-111">Örneğin, [dosya yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider) Kestrel yapılandırmasını bir *appsettings.json* veya appSettings 'ten yükleyebilir *. {1} Environment}. JSON* dosyası:</span><span class="sxs-lookup"><span data-stu-id="4043e-111">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="4043e-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> ve [uç nokta yapılandırması](xref:fundamentals/servers/kestrel/endpoints) yapılandırma sağlayıcılarından yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="4043e-112"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](xref:fundamentals/servers/kestrel/endpoints) are configurable from configuration providers.</span></span> <span data-ttu-id="4043e-113">Kalan Kestrel yapılandırması C# kodunda yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4043e-113">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="4043e-114">Aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="4043e-114">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="4043e-115">Kestrel 'i yapılandırma `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4043e-115">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="4043e-116">Sınıfına bir örneği `IConfiguration` ekleyin `Startup` .</span><span class="sxs-lookup"><span data-stu-id="4043e-116">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="4043e-117">Aşağıdaki örnek, eklenen yapılandırmanın özelliğe atandığını varsayar `Configuration` .</span><span class="sxs-lookup"><span data-stu-id="4043e-117">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="4043e-118">İçinde `Startup.ConfigureServices` , `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="4043e-118">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="4043e-119">Ana bilgisayarı oluştururken Kestrel yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="4043e-119">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="4043e-120">*Program.cs*' de, `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="4043e-120">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="4043e-121">Yukarıdaki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.</span><span class="sxs-lookup"><span data-stu-id="4043e-121">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

## <a name="general-limits"></a><span data-ttu-id="4043e-122">Genel sınırlar</span><span class="sxs-lookup"><span data-stu-id="4043e-122">General limits</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="4043e-123">Etkin tut zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="4043e-123">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="4043e-124">[Etkin tutma zaman aşımını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="4043e-124">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="4043e-125">Varsayılan olarak 2 dakikadır.</span><span class="sxs-lookup"><span data-stu-id="4043e-125">Defaults to 2 minutes.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="4043e-126">İstemci bağlantıları üst sınırı</span><span class="sxs-lookup"><span data-stu-id="4043e-126">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="4043e-127">En fazla eş zamanlı açık TCP bağlantısı sayısı tüm uygulama için aşağıdaki kodla ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="4043e-127">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="4043e-128">HTTP veya HTTPS 'den başka bir protokole (örneğin, bir WebSockets isteğinde) yükseltilen bağlantılara yönelik ayrı bir sınır vardır.</span><span class="sxs-lookup"><span data-stu-id="4043e-128">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="4043e-129">Bir bağlantı yükseltildikten sonra, bu sınıra göre sayılmaz `MaxConcurrentConnections` .</span><span class="sxs-lookup"><span data-stu-id="4043e-129">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="4043e-130">En fazla bağlantı sayısı, varsayılan olarak sınırsız (null).</span><span class="sxs-lookup"><span data-stu-id="4043e-130">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="4043e-131">En büyük istek gövdesi boyutu</span><span class="sxs-lookup"><span data-stu-id="4043e-131">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="4043e-132">Varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu değer yaklaşık 28,6 MB 'tır.</span><span class="sxs-lookup"><span data-stu-id="4043e-132">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="4043e-133">ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yaklaşım, <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="4043e-133">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="4043e-134">Her istekte uygulama için kısıtlamanın nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="4043e-134">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="4043e-135">Ara yazılım içindeki belirli bir istek üzerindeki ayarı geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="4043e-135">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="4043e-136">Uygulama isteği okumaya başladıktan sonra bir istek üzerindeki sınırı yapılandırırsa, bir özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="4043e-136">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="4043e-137">`IsReadOnly`Özelliğin salt okuma durumunda olup olmadığını belirten bir özellik vardır. Bu, `MaxRequestBodySize` sınırı yapılandırmanın çok geç olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="4043e-137">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="4043e-138">Bir uygulama [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)arkasında [işlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model) çalıştığında, Kestrel 'nin istek gövdesi boyut sınırı devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="4043e-138">When an app runs [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled.</span></span> <span data-ttu-id="4043e-139">IIS sınırı zaten ayarlıyor.</span><span class="sxs-lookup"><span data-stu-id="4043e-139">IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="4043e-140">En az istek gövdesi veri hızı</span><span class="sxs-lookup"><span data-stu-id="4043e-140">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="4043e-141">Kestrel bayt/saniye cinsinden belirtilen fiyata ulaşan her saniye sonra denetler.</span><span class="sxs-lookup"><span data-stu-id="4043e-141">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="4043e-142">Oran en düşük değerin altına düşerse bağlantı zaman aşımına uğrar. Yetkisiz kullanım süresi, Kestrel istemcisinin, gönderme hızını en düşüye artırması için izin verir.</span><span class="sxs-lookup"><span data-stu-id="4043e-142">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time Kestrel allows the client to increase its send rate up to the minimum.</span></span> <span data-ttu-id="4043e-143">Bu süre boyunca fiyat denetlenmez.</span><span class="sxs-lookup"><span data-stu-id="4043e-143">The rate isn't checked during that time.</span></span> <span data-ttu-id="4043e-144">Yetkisiz kullanım süresi, TCP yavaş başlatma nedeniyle başlangıçta verileri yavaş bir hızda gönderen bağlantıların bırakılmasını önlemeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="4043e-144">The grace period helps avoid dropping connections that are initially sending data at a slow rate because of TCP slow-start.</span></span>

<span data-ttu-id="4043e-145">Varsayılan en düşük oran, 5 saniyelik bir yetkisiz kullanım süresi ile 240 bayt/saniye olur.</span><span class="sxs-lookup"><span data-stu-id="4043e-145">The default minimum rate is 240 bytes/second with a 5-second grace period.</span></span>

<span data-ttu-id="4043e-146">Yanıt için bir minimum oran da geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="4043e-146">A minimum rate also applies to the response.</span></span> <span data-ttu-id="4043e-147">İstek sınırını ayarlamaya yönelik kod ve yanıt sınırı, `RequestBody` `Response` özellik ve arabirim adlarında olduğu gibi aynı olur.</span><span class="sxs-lookup"><span data-stu-id="4043e-147">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="4043e-148">*Program.cs* içinde en düşük veri hızlarının nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="4043e-148">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="4043e-149">Ara yazılım içindeki istek başına düşen minimum hız sınırlarını geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="4043e-149">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="4043e-150"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>Önceki örnekte başvurulan, `HttpContext.Features` http/2 isteklerinde ' de yer almıyor.</span><span class="sxs-lookup"><span data-stu-id="4043e-150">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample isn't present in `HttpContext.Features` for HTTP/2 requests.</span></span> <span data-ttu-id="4043e-151">İstek çoğullama için protokolün desteği nedeniyle, istek başına hız sınırlarını değiştirme, HTTP/2 için genellikle desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="4043e-151">Modifying rate limits on a per-request basis is generally not supported for HTTP/2 because of the protocol's support for request multiplexing.</span></span> <span data-ttu-id="4043e-152">Ancak, http <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> `HttpContext.Features` /2 istekleri için yine de vardır, çünkü okuma hızı sınırı,  `IHttpMinRequestBodyDataRateFeature.MinDataRate` `null` bir http/2 isteği için de olarak ayarlanarak tamamen istek temelli olarak devre dışı bırakılabilir.</span><span class="sxs-lookup"><span data-stu-id="4043e-152">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="4043e-153">`IHttpMinRequestBodyDataRateFeature.MinDataRate`Bunun dışında bir değere ayarlamaya çalışılması veya BIR `null` `NotSupportedException` http/2 isteği verilmeye neden olur.</span><span class="sxs-lookup"><span data-stu-id="4043e-153">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="4043e-154">`KestrelServerOptions.Limits`Http/1. x ve http/2 bağlantılarına hala uygulanan sunucu genelindeki hız sınırları.</span><span class="sxs-lookup"><span data-stu-id="4043e-154">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="4043e-155">İstek üst bilgileri zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="4043e-155">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="4043e-156">Sunucunun istek üst bilgilerini alması için harcadığı en uzun süreyi alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="4043e-156">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="4043e-157">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="4043e-157">Defaults to 30 seconds.</span></span>

[!code-csharp[](samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

## <a name="http2-limits"></a><span data-ttu-id="4043e-158">HTTP/2 sınırları</span><span class="sxs-lookup"><span data-stu-id="4043e-158">HTTP/2 limits</span></span>

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="4043e-159">Bağlantı başına en fazla akış</span><span class="sxs-lookup"><span data-stu-id="4043e-159">Maximum streams per connection</span></span>

<span data-ttu-id="4043e-160">`Http2.MaxStreamsPerConnection` HTTP/2 bağlantısı başına eşzamanlı istek akışı sayısını sınırlar.</span><span class="sxs-lookup"><span data-stu-id="4043e-160">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="4043e-161">Fazlalık akışlar reddedildi.</span><span class="sxs-lookup"><span data-stu-id="4043e-161">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="4043e-162">Varsayılan değer 100’dür.</span><span class="sxs-lookup"><span data-stu-id="4043e-162">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="4043e-163">Üst bilgi tablosu boyutu</span><span class="sxs-lookup"><span data-stu-id="4043e-163">Header table size</span></span>

<span data-ttu-id="4043e-164">HPACK kod çözücüsü HTTP/2 bağlantıları için HTTP üstbilgilerini açar.</span><span class="sxs-lookup"><span data-stu-id="4043e-164">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="4043e-165">`Http2.HeaderTableSize` HPACK kod çözücüsünün kullandığı üst bilgi sıkıştırma tablosunun boyutunu sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="4043e-165">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="4043e-166">Değer sekizli cinsinden sağlanır ve sıfırdan büyük olmalıdır (0).</span><span class="sxs-lookup"><span data-stu-id="4043e-166">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="4043e-167">Varsayılan değer 4096 ' dir.</span><span class="sxs-lookup"><span data-stu-id="4043e-167">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="4043e-168">En büyük çerçeve boyutu</span><span class="sxs-lookup"><span data-stu-id="4043e-168">Maximum frame size</span></span>

<span data-ttu-id="4043e-169">`Http2.MaxFrameSize` sunucu tarafından alınan veya gönderilen HTTP/2 bağlantı çerçevesi yükünün izin verilen en büyük boyutunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="4043e-169">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="4043e-170">Değer sekizli cinsinden sağlanır ve 2 ^ 14 (16.384) ile 2 ^ 24-1 (16.777.215) arasında olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4043e-170">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="4043e-171">Varsayılan değer 2 ^ 14 ' dir (16.384).</span><span class="sxs-lookup"><span data-stu-id="4043e-171">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="4043e-172">En fazla istek üst bilgi boyutu</span><span class="sxs-lookup"><span data-stu-id="4043e-172">Maximum request header size</span></span>

<span data-ttu-id="4043e-173">`Http2.MaxRequestHeaderFieldSize` istek üst bilgisi değerlerinin sekizlisi cinsinden izin verilen en büyük boyutu belirtir.</span><span class="sxs-lookup"><span data-stu-id="4043e-173">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="4043e-174">Bu sınır, sıkıştırılmış ve sıkıştırılmamış temsillerinde hem ad hem de değer için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="4043e-174">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="4043e-175">Değer sıfırdan büyük (0) olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4043e-175">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="4043e-176">Varsayılan değer 8.192 ' dir.</span><span class="sxs-lookup"><span data-stu-id="4043e-176">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="4043e-177">İlk bağlantı pencere boyutu</span><span class="sxs-lookup"><span data-stu-id="4043e-177">Initial connection window size</span></span>

<span data-ttu-id="4043e-178">`Http2.InitialConnectionWindowSize` sunucu, bağlantı başına tüm istekler (akışlar) genelinde toplanan tek seferde sunucunun arabelleğe aldığı en fazla istek gövde verilerini bayt cinsinden gösterir.</span><span class="sxs-lookup"><span data-stu-id="4043e-178">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="4043e-179">İstekleri ile de sınırlıdır `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="4043e-179">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="4043e-180">Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4043e-180">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="4043e-181">Varsayılan değer 128 KB 'tır (131.072).</span><span class="sxs-lookup"><span data-stu-id="4043e-181">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="4043e-182">İlk akış pencere boyutu</span><span class="sxs-lookup"><span data-stu-id="4043e-182">Initial stream window size</span></span>

<span data-ttu-id="4043e-183">`Http2.InitialStreamWindowSize` sunucu, istek başına bir kez (Stream) arabelleğe alınan en fazla istek gövde verilerini bayt cinsinden gösterir.</span><span class="sxs-lookup"><span data-stu-id="4043e-183">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="4043e-184">İstekleri ile de sınırlıdır `Http2.InitialConnectionWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="4043e-184">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="4043e-185">Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4043e-185">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="4043e-186">Varsayılan değer 96 KB 'tır (98.304).</span><span class="sxs-lookup"><span data-stu-id="4043e-186">The default value is 96 KB (98,304).</span></span>

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="4043e-187">HTTP/2 canlı tutmayı ping yapılandırması</span><span class="sxs-lookup"><span data-stu-id="4043e-187">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="4043e-188">Kestrel, bağlı istemcilere HTTP/2 pingler gönderecek şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="4043e-188">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="4043e-189">HTTP/2 ping işlemleri birden çok amaca hizmet eder:</span><span class="sxs-lookup"><span data-stu-id="4043e-189">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="4043e-190">Boştaki bağlantıları canlı tutun.</span><span class="sxs-lookup"><span data-stu-id="4043e-190">Keep idle connections alive.</span></span> <span data-ttu-id="4043e-191">Bazı istemciler ve proxy sunucular, boşta olan bağlantıları kapatır.</span><span class="sxs-lookup"><span data-stu-id="4043e-191">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="4043e-192">HTTP/2 pingler bir bağlantıda etkinlik olarak değerlendirilir ve bağlantının boşta olarak kapatılmasını önler.</span><span class="sxs-lookup"><span data-stu-id="4043e-192">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="4043e-193">Sağlıksız bağlantıları kapatın.</span><span class="sxs-lookup"><span data-stu-id="4043e-193">Close unhealthy connections.</span></span> <span data-ttu-id="4043e-194">İstemcinin yapılandırılan süre içinde canlı tut ping komutuna yanıt vermediği bağlantılar sunucu tarafından kapalıdır.</span><span class="sxs-lookup"><span data-stu-id="4043e-194">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="4043e-195">HTTP/2 canlı tut ping işlemleri ile ilgili iki yapılandırma seçeneği vardır:</span><span class="sxs-lookup"><span data-stu-id="4043e-195">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="4043e-196">`Http2.KeepAlivePingInterval` , `TimeSpan` ping aralığını yapılandıran bir.</span><span class="sxs-lookup"><span data-stu-id="4043e-196">`Http2.KeepAlivePingInterval` is a `TimeSpan` that configures the ping interval.</span></span> <span data-ttu-id="4043e-197">Sunucu, bu süre boyunca herhangi bir çerçeve almazsa istemciye canlı bir ping gönderir.</span><span class="sxs-lookup"><span data-stu-id="4043e-197">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="4043e-198">Bu seçenek olarak ayarlandığında Canlı ping pingler devre dışı bırakılır `TimeSpan.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="4043e-198">Keep alive pings are disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="4043e-199">`TimeSpan.MaxValue` varsayılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="4043e-199">The default value is `TimeSpan.MaxValue`.</span></span>
* <span data-ttu-id="4043e-200">`Http2.KeepAlivePingTimeout` , `TimeSpan` ping zaman aşımını yapılandıran bir.</span><span class="sxs-lookup"><span data-stu-id="4043e-200">`Http2.KeepAlivePingTimeout` is a `TimeSpan` that configures the ping timeout.</span></span> <span data-ttu-id="4043e-201">Sunucu yanıt ping gibi bir çerçeve almazsa, bu zaman aşımı sırasında bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="4043e-201">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="4043e-202">Bu seçenek olarak ayarlandığında Canlı kalma zaman aşımını devre dışı bırakın `TimeSpan.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="4043e-202">Keep alive timeout is disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="4043e-203">Varsayılan değer 20 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="4043e-203">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

## <a name="other-options"></a><span data-ttu-id="4043e-204">Diğer seçenekler</span><span class="sxs-lookup"><span data-stu-id="4043e-204">Other options</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="4043e-205">Zaman Uyumlu G/Ç</span><span class="sxs-lookup"><span data-stu-id="4043e-205">Synchronous I/O</span></span>

<span data-ttu-id="4043e-206"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> istek ve yanıt için zaman uyumlu g/ç 'ye izin verilip verilmediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="4043e-206"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="4043e-207">`false` varsayılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="4043e-207">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="4043e-208">Çok sayıda engelleme zaman uyumlu g/ç işlemi, iş parçacığı havuzuna yol açabilir, bu da uygulamanın yanıt vermemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="4043e-208">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="4043e-209">Yalnızca `AllowSynchronousIO` zaman uyumsuz g/ç desteklemeyen bir kitaplık kullanırken etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="4043e-209">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="4043e-210">Aşağıdaki örnek, zaman uyumlu g/ç 'yi sunar:</span><span class="sxs-lookup"><span data-stu-id="4043e-210">The following example enables synchronous I/O:</span></span>

[!code-csharp[](samples/5.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="4043e-211">Diğer Kestrel seçenekleri ve limitleri hakkında daha fazla bilgi için bkz.:</span><span class="sxs-lookup"><span data-stu-id="4043e-211">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>
