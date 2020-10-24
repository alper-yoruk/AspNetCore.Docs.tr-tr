---
title: ASP.NET Core Web sunucusu uygulamasını Kestrel
author: rick-anderson
description: ASP.NET Core platformlar arası Web sunucusu olan Kestrel hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
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
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 50bf2a60f14238c9b71fe90a64c284da202bff59
ms.sourcegitcommit: d5ecad1103306fac8d5468128d3e24e529f1472c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2020
ms.locfileid: "92491606"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="79fe6-103">ASP.NET Core Web sunucusu uygulamasını Kestrel</span><span class="sxs-lookup"><span data-stu-id="79fe6-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="79fe6-104">[Tom Dykstra](https://github.com/tdykstra), [Chris](https://github.com/Tratcher), ve [Stephen halter](https://twitter.com/halter73) tarafından</span><span class="sxs-lookup"><span data-stu-id="79fe6-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="79fe6-105">Kestrel, ASP.NET Core için platformlar arası [Web sunucusudur](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="79fe6-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="79fe6-106">Kestrel, ASP.NET Core proje şablonlarında varsayılan olarak bulunan Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="79fe6-107">Kestrel aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="79fe6-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="79fe6-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="79fe6-108">HTTPS</span></span>
* <span data-ttu-id="79fe6-109">[WebSockets](https://github.com/aspnet/websockets) 'i etkinleştirmek için kullanılan donuk yükseltme</span><span class="sxs-lookup"><span data-stu-id="79fe6-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="79fe6-110">NGINX 'in arkasında yüksek performans için UNIX Yuvaları</span><span class="sxs-lookup"><span data-stu-id="79fe6-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="79fe6-111">HTTP/2 (macOS hariç &dagger; )</span><span class="sxs-lookup"><span data-stu-id="79fe6-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="79fe6-112">&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="79fe6-113">Kestrel, .NET Core 'un desteklediği tüm platformlarda ve sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="79fe6-114">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="79fe6-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="79fe6-115">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="79fe6-115">HTTP/2 support</span></span>

<span data-ttu-id="79fe6-116">Aşağıdaki temel gereksinimler karşılanıyorsa, [http/2](https://httpwg.org/specs/rfc7540.html) ASP.NET Core uygulamalar için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="79fe6-117">İşletim Sistemi&dagger;</span><span class="sxs-lookup"><span data-stu-id="79fe6-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="79fe6-118">Windows Server 2016/Windows 10 veya üzeri&Dagger;</span><span class="sxs-lookup"><span data-stu-id="79fe6-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="79fe6-119">OpenSSL 1.0.2 veya üzerini içeren Linux (örneğin, Ubuntu 16,04 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="79fe6-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="79fe6-120">Hedef Framework: .NET Core 2,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="79fe6-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="79fe6-121">[Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı</span><span class="sxs-lookup"><span data-stu-id="79fe6-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="79fe6-122">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="79fe6-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="79fe6-123">&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="79fe6-124">&Dagger;Kestrel, Windows Server 2012 R2 ve Windows 8.1 'de HTTP/2 için sınırlı destek içerir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="79fe6-125">Bu işletim sistemlerinde kullanılabilir olan desteklenen TLS şifre paketlerinin listesi sınırlı olduğundan destek sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="79fe6-126">TLS bağlantılarının güvenliğini sağlamak için Eliptik Eğri dijital Imza algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="79fe6-127">Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="79fe6-128">HTTP/2 varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="79fe6-129">Yapılandırma hakkında daha fazla bilgi için [Kestrel Options](#kestrel-options) ve [Listenoptions. Protocols](#listenoptionsprotocols) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="79fe6-130">Ters ara sunucu ile Kestrel ne zaman kullanılır?</span><span class="sxs-lookup"><span data-stu-id="79fe6-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="79fe6-131">Kestrel, kendisi veya [Internet Information Services (IIS)](https://www.iis.net/), [NGINX](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi bir *ters ara sunucu*ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="79fe6-132">Ters proxy sunucusu, ağdan gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="79fe6-133">Sınır (Internet 'e yönelik) Web sunucusu olarak kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="79fe6-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel, ters proxy sunucusu olmadan doğrudan Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="79fe6-135">Ters Proxy yapılandırmasında kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="79fe6-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel, IIS, NGINX veya Apache gibi bir ters ara sunucu üzerinden Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="79fe6-137">İki yapılandırma de, ters ara sunucu sunucusuyla veya olmadan, desteklenen bir barındırma yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="79fe6-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="79fe6-138">Ters proxy sunucusu olmayan bir uç sunucu olarak kullanılan Kestrel, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="79fe6-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="79fe6-139">Kestrel bir bağlantı noktasını dinlemek üzere yapılandırıldığında, Kestrel isteklerin üst bilgilerinden bağımsız olarak bu bağlantı noktası için tüm trafiği işler `Host` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="79fe6-140">Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel 'e iletme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="79fe6-141">Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucu kullanılması iyi bir seçim olabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="79fe6-142">Ters proxy:</span><span class="sxs-lookup"><span data-stu-id="79fe6-142">A reverse proxy:</span></span>

* <span data-ttu-id="79fe6-143">, Barındırdığı uygulamaların açığa çıkarılan genel yüzey alanını sınırlayabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="79fe6-144">Ek bir yapılandırma ve savunma katmanı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="79fe6-145">, Mevcut altyapıyla daha iyi tümleşebilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="79fe6-146">Yük dengelemeyi ve güvenli iletişim (HTTPS) yapılandırmasını kolaylaştırın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="79fe6-147">Yalnızca ters proxy sunucusu bir X. 509.440 sertifikası gerektirir ve bu sunucu, düz HTTP kullanarak, iç ağdaki uygulamanın sunucularıyla iletişim kurabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="79fe6-148">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="79fe6-149">ASP.NET Core uygulamalarda Kestrel</span><span class="sxs-lookup"><span data-stu-id="79fe6-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="79fe6-150">ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="79fe6-151">*Program.cs*içinde Yöntem şunu <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="79fe6-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="79fe6-152">Konak oluşturma hakkında daha fazla bilgi için, uygulamasının konak ve *Varsayılan Oluşturucu ayarlarını* *ayarlama* bölümüne bakın <xref:fundamentals/host/generic-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="79fe6-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="79fe6-153">Çağrıldıktan sonra ek yapılandırma sağlamak için `ConfigureWebHostDefaults` şunu kullanın `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="79fe6-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="79fe6-154">Kestrel seçenekleri</span><span class="sxs-lookup"><span data-stu-id="79fe6-154">Kestrel options</span></span>

<span data-ttu-id="79fe6-155">Kestrel Web sunucusu, Internet 'e yönelik dağıtımlarda özellikle yararlı olan kısıtlama yapılandırma seçeneklerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="79fe6-156">Sınıfının özelliğinde kısıtlamaları ayarlayın <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> .</span><span class="sxs-lookup"><span data-stu-id="79fe6-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="79fe6-157">`Limits`Özelliği, sınıfının bir örneğini barındırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> .</span><span class="sxs-lookup"><span data-stu-id="79fe6-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="79fe6-158">Aşağıdaki örnekler <xref:Microsoft.AspNetCore.Server.Kestrel.Core> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="79fe6-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="79fe6-159">Bu makalenin ilerleyen kısımlarında gösterilen örneklerde, Kestrel seçenekleri C# kodunda yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="79fe6-160">Ayrıca, Kestrel seçenekleri bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="79fe6-161">Örneğin, [dosya yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider) Kestrel yapılandırmasını bir *appsettings.js* veya appSettings 'ten yükleyebilir *. { Environment}. JSON* dosyası:</span><span class="sxs-lookup"><span data-stu-id="79fe6-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="79fe6-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> ve [uç nokta yapılandırması](#endpoint-configuration) yapılandırma sağlayıcılarından yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="79fe6-163">Kalan Kestrel yapılandırması C# kodunda yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="79fe6-164">Aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="79fe6-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="79fe6-165">Kestrel 'i yapılandırma `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="79fe6-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="79fe6-166">Sınıfına bir örneği `IConfiguration` ekleyin `Startup` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="79fe6-167">Aşağıdaki örnek, eklenen yapılandırmanın özelliğe atandığını varsayar `Configuration` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="79fe6-168">İçinde `Startup.ConfigureServices` , `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="79fe6-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="79fe6-169">Ana bilgisayarı oluştururken Kestrel yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="79fe6-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="79fe6-170">*Program.cs*' de, `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="79fe6-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="79fe6-171">Yukarıdaki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="79fe6-172">Etkin tut zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="79fe6-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="79fe6-173">[Etkin tutma zaman aşımını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="79fe6-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="79fe6-174">Varsayılan olarak 2 dakikadır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="79fe6-175">İstemci bağlantıları üst sınırı</span><span class="sxs-lookup"><span data-stu-id="79fe6-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="79fe6-176">En fazla eş zamanlı açık TCP bağlantısı sayısı tüm uygulama için aşağıdaki kodla ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="79fe6-177">HTTP veya HTTPS 'den başka bir protokole (örneğin, bir WebSockets isteğinde) yükseltilen bağlantılara yönelik ayrı bir sınır vardır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="79fe6-178">Bir bağlantı yükseltildikten sonra, bu sınıra göre sayılmaz `MaxConcurrentConnections` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="79fe6-179">En fazla bağlantı sayısı, varsayılan olarak sınırsız (null).</span><span class="sxs-lookup"><span data-stu-id="79fe6-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="79fe6-180">En büyük istek gövdesi boyutu</span><span class="sxs-lookup"><span data-stu-id="79fe6-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="79fe6-181">Varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu değer yaklaşık 28,6 MB 'tır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="79fe6-182">ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yaklaşım, <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="79fe6-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="79fe6-183">Her istekte uygulama için kısıtlamanın nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="79fe6-184">Ara yazılım içindeki belirli bir istek üzerindeki ayarı geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="79fe6-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="79fe6-185">Uygulama isteği okumaya başladıktan sonra bir istek üzerindeki sınırı yapılandırırsa, bir özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="79fe6-186">`IsReadOnly`Özelliğin salt okuma durumunda olup olmadığını belirten bir özellik vardır. Bu, `MaxRequestBodySize` sınırı yapılandırmanın çok geç olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="79fe6-187">Bir uygulama [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)arkasında [çalıştırıldığında](xref:host-and-deploy/iis/index#out-of-process-hosting-model) , IIS sınırı zaten ayarladığı için Kestrel 'nin istek gövdesi boyut sınırı devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="79fe6-188">En az istek gövdesi veri hızı</span><span class="sxs-lookup"><span data-stu-id="79fe6-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="79fe6-189">Kestrel bayt/saniye cinsinden belirtilen fiyata ulaşan her saniye sonra denetler.</span><span class="sxs-lookup"><span data-stu-id="79fe6-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="79fe6-190">Oran en düşük değerin altına düşerse bağlantı zaman aşımına uğrar. Yetkisiz kullanım süresi, Kestrel 'in istemciye gönderme oranını en düşük süreye kadar artırabilme Bu süre boyunca fiyat denetlenmez.</span><span class="sxs-lookup"><span data-stu-id="79fe6-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="79fe6-191">Yetkisiz kullanım süresi, TCP yavaş başlatma nedeniyle başlangıçta verileri yavaş bir hızda gönderen bağlantıların bırakılmasını önlemeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="79fe6-192">Varsayılan en düşük oran, 5 saniyelik bir yetkisiz kullanım süresi ile 240 bayt/saniye olur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="79fe6-193">Yanıt için bir minimum oran da geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="79fe6-194">İstek sınırını ayarlamaya yönelik kod ve yanıt sınırı, `RequestBody` `Response` özellik ve arabirim adlarında olduğu gibi aynı olur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="79fe6-195">*Program.cs*içinde en düşük veri hızlarının nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="79fe6-196">Ara yazılım içindeki istek başına düşen minimum hız sınırlarını geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="79fe6-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="79fe6-197"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>Önceki örnekte başvurulan, `HttpContext.Features` http/2 isteklerinde hız sınırlarını değiştirmek, protokolün istek çoğullama DESTEĞI nedeniyle http/2 için genel olarak desteklenmediği IÇIN, http/2 istekleri için ' de mevcut değildir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="79fe6-198">Ancak, http <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> `HttpContext.Features` /2 istekleri için yine de vardır, çünkü okuma hızı sınırı, *disabled entirely* `IHttpMinRequestBodyDataRateFeature.MinDataRate` `null` bir http/2 isteği için de olarak ayarlanarak tamamen istek temelli olarak devre dışı bırakılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="79fe6-199">`IHttpMinRequestBodyDataRateFeature.MinDataRate`Bunun dışında bir değere ayarlamaya çalışılması veya BIR `null` `NotSupportedException` http/2 isteği verilmeye neden olur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="79fe6-200">`KestrelServerOptions.Limits`Http/1. x ve http/2 bağlantılarına hala uygulanan sunucu genelindeki hız sınırları.</span><span class="sxs-lookup"><span data-stu-id="79fe6-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="79fe6-201">İstek üst bilgileri zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="79fe6-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="79fe6-202">Sunucunun istek üst bilgilerini alması için harcadığı en uzun süreyi alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="79fe6-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="79fe6-203">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="79fe6-204">Bağlantı başına en fazla akış</span><span class="sxs-lookup"><span data-stu-id="79fe6-204">Maximum streams per connection</span></span>

<span data-ttu-id="79fe6-205">`Http2.MaxStreamsPerConnection` HTTP/2 bağlantısı başına eşzamanlı istek akışı sayısını sınırlar.</span><span class="sxs-lookup"><span data-stu-id="79fe6-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="79fe6-206">Fazlalık akışlar reddedildi.</span><span class="sxs-lookup"><span data-stu-id="79fe6-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="79fe6-207">Varsayılan değer 100’dür.</span><span class="sxs-lookup"><span data-stu-id="79fe6-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="79fe6-208">Üst bilgi tablosu boyutu</span><span class="sxs-lookup"><span data-stu-id="79fe6-208">Header table size</span></span>

<span data-ttu-id="79fe6-209">HPACK kod çözücüsü HTTP/2 bağlantıları için HTTP üstbilgilerini açar.</span><span class="sxs-lookup"><span data-stu-id="79fe6-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="79fe6-210">`Http2.HeaderTableSize` HPACK kod çözücüsünün kullandığı üst bilgi sıkıştırma tablosunun boyutunu sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="79fe6-211">Değer sekizli cinsinden sağlanır ve sıfırdan büyük olmalıdır (0).</span><span class="sxs-lookup"><span data-stu-id="79fe6-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="79fe6-212">Varsayılan değer 4096 ' dir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="79fe6-213">En büyük çerçeve boyutu</span><span class="sxs-lookup"><span data-stu-id="79fe6-213">Maximum frame size</span></span>

<span data-ttu-id="79fe6-214">`Http2.MaxFrameSize` sunucu tarafından alınan veya gönderilen HTTP/2 bağlantı çerçevesi yükünün izin verilen en büyük boyutunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="79fe6-215">Değer sekizli cinsinden sağlanır ve 2 ^ 14 (16.384) ile 2 ^ 24-1 (16.777.215) arasında olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="79fe6-216">Varsayılan değer 2 ^ 14 ' dir (16.384).</span><span class="sxs-lookup"><span data-stu-id="79fe6-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="79fe6-217">En fazla istek üst bilgi boyutu</span><span class="sxs-lookup"><span data-stu-id="79fe6-217">Maximum request header size</span></span>

<span data-ttu-id="79fe6-218">`Http2.MaxRequestHeaderFieldSize` istek üst bilgisi değerlerinin sekizlisi cinsinden izin verilen en büyük boyutu belirtir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="79fe6-219">Bu sınır, sıkıştırılmış ve sıkıştırılmamış temsillerinde hem ad hem de değer için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="79fe6-220">Değer sıfırdan büyük (0) olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="79fe6-221">Varsayılan değer 8.192 ' dir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="79fe6-222">İlk bağlantı pencere boyutu</span><span class="sxs-lookup"><span data-stu-id="79fe6-222">Initial connection window size</span></span>

<span data-ttu-id="79fe6-223">`Http2.InitialConnectionWindowSize` sunucu, bağlantı başına tüm istekler (akışlar) genelinde toplanan tek seferde sunucunun arabelleğe aldığı en fazla istek gövde verilerini bayt cinsinden gösterir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="79fe6-224">İstekleri ile de sınırlıdır `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="79fe6-225">Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="79fe6-226">Varsayılan değer 128 KB 'tır (131.072).</span><span class="sxs-lookup"><span data-stu-id="79fe6-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="79fe6-227">İlk akış pencere boyutu</span><span class="sxs-lookup"><span data-stu-id="79fe6-227">Initial stream window size</span></span>

<span data-ttu-id="79fe6-228">`Http2.InitialStreamWindowSize` sunucu, istek başına bir kez (Stream) arabelleğe alınan en fazla istek gövde verilerini bayt cinsinden gösterir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="79fe6-229">İstekleri ile de sınırlıdır `Http2.InitialConnectionWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="79fe6-230">Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="79fe6-231">Varsayılan değer 96 KB 'tır (98.304).</span><span class="sxs-lookup"><span data-stu-id="79fe6-231">The default value is 96 KB (98,304).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="79fe6-232">HTTP/2 canlı tutmayı ping yapılandırması</span><span class="sxs-lookup"><span data-stu-id="79fe6-232">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="79fe6-233">Kestrel, bağlı istemcilere HTTP/2 pingler gönderecek şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-233">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="79fe6-234">HTTP/2 ping işlemleri birden çok amaca hizmet eder:</span><span class="sxs-lookup"><span data-stu-id="79fe6-234">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="79fe6-235">Boştaki bağlantıları canlı tutun.</span><span class="sxs-lookup"><span data-stu-id="79fe6-235">Keep idle connections alive.</span></span> <span data-ttu-id="79fe6-236">Bazı istemciler ve proxy sunucular, boşta olan bağlantıları kapatır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-236">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="79fe6-237">HTTP/2 pingler bir bağlantıda etkinlik olarak değerlendirilir ve bağlantının boşta olarak kapatılmasını önler.</span><span class="sxs-lookup"><span data-stu-id="79fe6-237">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="79fe6-238">Sağlıksız bağlantıları kapatın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-238">Close unhealthy connections.</span></span> <span data-ttu-id="79fe6-239">İstemcinin yapılandırılan süre içinde canlı tut ping komutuna yanıt vermediği bağlantılar sunucu tarafından kapalıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-239">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="79fe6-240">HTTP/2 canlı tut ping işlemleri ile ilgili iki yapılandırma seçeneği vardır:</span><span class="sxs-lookup"><span data-stu-id="79fe6-240">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="79fe6-241">`Http2.KeepAlivePingInterval` , `TimeSpan` ping iç öğesini yapılandıran bir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-241">`Http2.KeepAlivePingInterval` is a `TimeSpan` that configures the ping internal.</span></span> <span data-ttu-id="79fe6-242">Sunucu, bu süre boyunca herhangi bir çerçeve almazsa istemciye canlı bir ping gönderir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-242">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="79fe6-243">Bu seçenek olarak ayarlandığında Canlı ping pingler devre dışı bırakılır `TimeSpan.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-243">Keep alive pings are disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="79fe6-244">Varsayılan değer: `TimeSpan.MaxValue`.</span><span class="sxs-lookup"><span data-stu-id="79fe6-244">The default value is `TimeSpan.MaxValue`.</span></span>
* <span data-ttu-id="79fe6-245">`Http2.KeepAlivePingTimeout` , `TimeSpan` ping zaman aşımını yapılandıran bir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-245">`Http2.KeepAlivePingTimeout` is a `TimeSpan` that configures the ping timeout.</span></span> <span data-ttu-id="79fe6-246">Sunucu yanıt ping gibi bir çerçeve almazsa, bu zaman aşımı sırasında bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-246">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="79fe6-247">Bu seçenek olarak ayarlandığında Canlı kalma zaman aşımını devre dışı bırakın `TimeSpan.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-247">Keep alive timeout is disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="79fe6-248">Varsayılan değer 20 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-248">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="trailers"></a><span data-ttu-id="79fe6-249">Larına</span><span class="sxs-lookup"><span data-stu-id="79fe6-249">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="79fe6-250">Sıfırla</span><span class="sxs-lookup"><span data-stu-id="79fe6-250">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a><span data-ttu-id="79fe6-251">Zaman Uyumlu G/Ç</span><span class="sxs-lookup"><span data-stu-id="79fe6-251">Synchronous I/O</span></span>

<span data-ttu-id="79fe6-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> istek ve yanıt için zaman uyumlu g/ç 'ye izin verilip verilmediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="79fe6-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="79fe6-253">Varsayılan değer: `false`.</span><span class="sxs-lookup"><span data-stu-id="79fe6-253">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="79fe6-254">Çok sayıda engelleme zaman uyumlu g/ç işlemi, iş parçacığı havuzuna yol açabilir, bu da uygulamanın yanıt vermemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-254">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="79fe6-255">Yalnızca `AllowSynchronousIO` zaman uyumsuz g/ç desteklemeyen bir kitaplık kullanırken etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="79fe6-255">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="79fe6-256">Aşağıdaki örnek, zaman uyumlu g/ç 'yi sunar:</span><span class="sxs-lookup"><span data-stu-id="79fe6-256">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="79fe6-257">Diğer Kestrel seçenekleri ve limitleri hakkında daha fazla bilgi için bkz.:</span><span class="sxs-lookup"><span data-stu-id="79fe6-257">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="79fe6-258">Uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="79fe6-258">Endpoint configuration</span></span>

<span data-ttu-id="79fe6-259">Varsayılan olarak, ASP.NET Core bağlar:</span><span class="sxs-lookup"><span data-stu-id="79fe6-259">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="79fe6-260">`https://localhost:5001` (bir yerel geliştirme sertifikası varsa)</span><span class="sxs-lookup"><span data-stu-id="79fe6-260">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="79fe6-261">Kullanarak URL 'Leri belirtin:</span><span class="sxs-lookup"><span data-stu-id="79fe6-261">Specify URLs using the:</span></span>

* <span data-ttu-id="79fe6-262">`ASPNETCORE_URLS` ortam değişkeni.</span><span class="sxs-lookup"><span data-stu-id="79fe6-262">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="79fe6-263">`--urls` komut satırı bağımsız değişkeni.</span><span class="sxs-lookup"><span data-stu-id="79fe6-263">`--urls` command-line argument.</span></span>
* <span data-ttu-id="79fe6-264">`urls` Ana bilgisayar yapılandırma anahtarı.</span><span class="sxs-lookup"><span data-stu-id="79fe6-264">`urls` host configuration key.</span></span>
* <span data-ttu-id="79fe6-265">`UseUrls` genişletme yöntemi.</span><span class="sxs-lookup"><span data-stu-id="79fe6-265">`UseUrls` extension method.</span></span>

<span data-ttu-id="79fe6-266">Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS).</span><span class="sxs-lookup"><span data-stu-id="79fe6-266">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="79fe6-267">Değeri noktalı virgülle ayrılmış bir liste olarak yapılandırın (örneğin, `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="79fe6-267">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="79fe6-268">Bu yaklaşımlar hakkında daha fazla bilgi için [sunucu URL 'leri](xref:fundamentals/host/web-host#server-urls) ve [geçersiz kılma yapılandırması](xref:fundamentals/host/web-host#override-configuration)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-268">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="79fe6-269">Geliştirme sertifikası oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="79fe6-269">A development certificate is created:</span></span>

* <span data-ttu-id="79fe6-270">[.NET Core SDK](/dotnet/core/sdk) yüklendiği zaman.</span><span class="sxs-lookup"><span data-stu-id="79fe6-270">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="79fe6-271">[Geliştirme-CERT aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-271">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="79fe6-272">Bazı tarayıcılarda yerel geliştirme sertifikasına güvenmek için açık izin verilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-272">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="79fe6-273">Proje şablonları, uygulamaları HTTPS üzerinde varsayılan olarak çalışacak şekilde yapılandırır ve [https yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-273">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="79fe6-274"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> URL öneklerini ve bağlantı noktalarını Kestrel için yapılandırmak üzere üzerinde çağrı veya Yöntemler.</span><span class="sxs-lookup"><span data-stu-id="79fe6-274">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="79fe6-275">`UseUrls`, `--urls` komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı ve `ASPNETCORE_URLS` ortam değişkeni de çalışır, ancak bu bölümün ilerleyen kısımlarında belirtilen sınırlamalara sahiptir (https uç noktası yapılandırması için varsayılan sertifika kullanılabilir olmalıdır).</span><span class="sxs-lookup"><span data-stu-id="79fe6-275">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="79fe6-276">`KestrelServerOptions` yapılandırmada</span><span class="sxs-lookup"><span data-stu-id="79fe6-276">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="79fe6-277">ConfigureEndpointDefaults Varsayılanları (eylem \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="79fe6-277">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="79fe6-278">`Action`Belirtilen her bir uç nokta için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-278">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="79fe6-279">`ConfigureEndpointDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-279">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="79fe6-280">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="79fe6-280">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="79fe6-281">ConfigureHttpsDefaults (eylem \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="79fe6-281">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="79fe6-282">`Action`Her HTTPS uç noktası için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-282">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="79fe6-283">`ConfigureHttpsDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-283">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="79fe6-284">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="79fe6-284">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="79fe6-285">Yapılandırma (Iconation)</span><span class="sxs-lookup"><span data-stu-id="79fe6-285">Configure(IConfiguration)</span></span>

<span data-ttu-id="79fe6-286">Bir as girişi alan Kestrel ayarlamak için bir yapılandırma yükleyicisi oluşturur <xref:Microsoft.Extensions.Configuration.IConfiguration> .</span><span class="sxs-lookup"><span data-stu-id="79fe6-286">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="79fe6-287">Yapılandırma, Kestrel için yapılandırma bölümünün kapsamına alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-287">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="79fe6-288">ListenOptions. UseHttps</span><span class="sxs-lookup"><span data-stu-id="79fe6-288">ListenOptions.UseHttps</span></span>

<span data-ttu-id="79fe6-289">Kestrel 'i HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-289">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="79fe6-290">`ListenOptions.UseHttps` uzantılardan</span><span class="sxs-lookup"><span data-stu-id="79fe6-290">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="79fe6-291">`UseHttps`: Kestrel 'i varsayılan sertifikayla HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-291">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="79fe6-292">Varsayılan sertifika yapılandırılmamışsa bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-292">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="79fe6-293">`ListenOptions.UseHttps` parametrelere</span><span class="sxs-lookup"><span data-stu-id="79fe6-293">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="79fe6-294">`filename` , uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yolu ve dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-294">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="79fe6-295">`password` X. 509.440 sertifika verilerine erişmek için parola gereklidir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-295">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="79fe6-296">`configureOptions` , öğesini `Action` yapılandırmak için kullanılır `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-296">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="79fe6-297">Döndürür `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-297">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="79fe6-298">`storeName` , sertifikanın yükleneceği sertifika deposudur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-298">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="79fe6-299">`subject` , sertifika için konu adıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-299">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="79fe6-300">`allowInvalid` geçersiz sertifikaların, otomatik olarak imzalanan sertifikalar gibi göz önünde bulundurulmayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-300">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="79fe6-301">`location` , sertifikanın yükleneceği mağaza konumudur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-301">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="79fe6-302">`serverCertificate` , X. 509.440 sertifikasıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-302">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="79fe6-303">Üretimde HTTPS 'nin açıkça yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-303">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="79fe6-304">En azından, varsayılan bir sertifika sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-304">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="79fe6-305">Daha sonra açıklanan desteklenen yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="79fe6-305">Supported configurations described next:</span></span>

* <span data-ttu-id="79fe6-306">Yapılandırma yok</span><span class="sxs-lookup"><span data-stu-id="79fe6-306">No configuration</span></span>
* <span data-ttu-id="79fe6-307">Varsayılan sertifikayı yapılandırmadan Değiştir</span><span class="sxs-lookup"><span data-stu-id="79fe6-307">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="79fe6-308">Koddaki varsayılanları değiştirme</span><span class="sxs-lookup"><span data-stu-id="79fe6-308">Change the defaults in code</span></span>

<span data-ttu-id="79fe6-309">*Yapılandırma yok*</span><span class="sxs-lookup"><span data-stu-id="79fe6-309">*No configuration*</span></span>

<span data-ttu-id="79fe6-310">Kestrel tarihinde dinler `http://localhost:5000` ve `https://localhost:5001` (varsayılan bir sertifika varsa).</span><span class="sxs-lookup"><span data-stu-id="79fe6-310">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="79fe6-311">*Varsayılan sertifikayı yapılandırmadan Değiştir*</span><span class="sxs-lookup"><span data-stu-id="79fe6-311">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="79fe6-312">`CreateDefaultBuilder``Configure(context.Configuration.GetSection("Kestrel"))`Kestrel yapılandırmasını yüklemek için varsayılan olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-312">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="79fe6-313">Varsayılan bir HTTPS uygulama ayarları yapılandırma şeması Kestrel için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-313">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="79fe6-314">Disk üzerindeki bir dosyadan ya da bir sertifika deposundan kullanılacak URL 'Ler ve Sertifikalar dahil olmak üzere birden çok uç nokta yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-314">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="79fe6-315">Aşağıdaki *appsettings.js* aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-315">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="79fe6-316">Geçersiz sertifikaların kullanılmasına izin vermek için **Allowwınvalid** `true` ' i ayarlayın (örneğin, otomatik olarak imzalanan sertifikalar).</span><span class="sxs-lookup"><span data-stu-id="79fe6-316">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="79fe6-317">Bir sertifika belirtmeyen herhangi bir HTTPS uç noktası (aşağıdaki örnekte bulunan**httpsdefaultcert** ), **Sertifikalar** > **varsayılan** veya geliştirme sertifikası altında tanımlanan sertifikaya geri döner.</span><span class="sxs-lookup"><span data-stu-id="79fe6-317">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="79fe6-318">Herhangi bir sertifika düğümü için **yol** ve **parola** kullanmanın alternatifi sertifika deposu alanlarını kullanarak sertifikayı belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-318">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="79fe6-319">Örneğin, **sertifika**  >  **varsayılan** sertifikası şu şekilde belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-319">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="79fe6-320">Şema notları:</span><span class="sxs-lookup"><span data-stu-id="79fe6-320">Schema notes:</span></span>

* <span data-ttu-id="79fe6-321">Uç nokta adları büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-321">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="79fe6-322">Örneğin, `HTTPS` ve `Https` geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-322">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="79fe6-323">`Url`Her uç nokta için parametresi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-323">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="79fe6-324">Bu parametrenin biçimi, en üst düzey `Urls` yapılandırma parametresiyle aynıdır, ancak tek bir değerle sınırlı olur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-324">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="79fe6-325">Bu uç noktalar, üst düzey yapılandırmada tanımlananlar yerine `Urls` bunlara ekleme yerine bunların yerini alır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-325">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="79fe6-326">Kullanılarak kodda tanımlanan uç noktalar `Listen` yapılandırma bölümünde tanımlanan uç noktalar ile birikimlidir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-326">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="79fe6-327">Bu `Certificate` bölüm isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-327">The `Certificate` section is optional.</span></span> <span data-ttu-id="79fe6-328">`Certificate`Bölüm belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-328">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="79fe6-329">Kullanılabilir varsayılan değer yoksa, sunucu bir özel durum oluşturur ve başlayamaz.</span><span class="sxs-lookup"><span data-stu-id="79fe6-329">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="79fe6-330">Bu `Certificate` bölüm hem **yol** &ndash; **parolasını** hem de **Konu** &ndash; **deposu** sertifikalarını destekler.</span><span class="sxs-lookup"><span data-stu-id="79fe6-330">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="79fe6-331">Herhangi bir sayıda uç nokta, bağlantı noktası çakışmalarına neden olmadıkları sürece bu şekilde tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-331">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="79fe6-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))` yapılandırılmış bir `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` uç noktanın ayarlarını tamamlamak için kullanılabilecek bir yöntemi olan bir döndürür:</span><span class="sxs-lookup"><span data-stu-id="79fe6-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="79fe6-333">`KestrelServerOptions.ConfigurationLoader` , tarafından sağlana gibi var olan yükleyicisindeki yinelemeye devam etmek için doğrudan erişilebilir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="79fe6-333">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="79fe6-334">Her uç noktanın yapılandırma bölümü, `Endpoint` özel ayarların okunabilmesi için yöntemindeki seçeneklerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-334">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="79fe6-335">Birden çok yapılandırma, `options.Configure(context.Configuration.GetSection("{SECTION}"))` başka bir bölümle yeniden çağırarak yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-335">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="79fe6-336">Önceki örneklerde açıkça çağrılmadığı takdirde, yalnızca son yapılandırma kullanılır `Load` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-336">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="79fe6-337">Metapackage, `Load` varsayılan yapılandırma bölümünün değiştirilmesini sağlayacak şekilde çağırmıyor.</span><span class="sxs-lookup"><span data-stu-id="79fe6-337">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="79fe6-338">`KestrelConfigurationLoader``Listen`API ailesini `KestrelServerOptions` `Endpoint` aşırı yükleme olarak yansıtır, bu nedenle kod ve yapılandırma uç noktaları aynı yerde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-338">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="79fe6-339">Bu aşırı yüklemeler adları kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları kullanır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-339">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="79fe6-340">*Koddaki varsayılanları değiştirme*</span><span class="sxs-lookup"><span data-stu-id="79fe6-340">*Change the defaults in code*</span></span>

<span data-ttu-id="79fe6-341">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` `ListenOptions` `HttpsConnectionAdapterOptions` , önceki senaryoda belirtilen varsayılan sertifikayı geçersiz kılma dahil, ve için varsayılan ayarları değiştirmek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-341">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="79fe6-342">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-342">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="79fe6-343">*SNı için Kestrel desteği*</span><span class="sxs-lookup"><span data-stu-id="79fe6-343">*Kestrel support for SNI*</span></span>

<span data-ttu-id="79fe6-344">[Sunucu adı belirtme (SNı)](https://tools.ietf.org/html/rfc6066#section-3) , aynı IP adresi ve bağlantı noktası üzerinde birden fazla etki alanını barındırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-344">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="79fe6-345">SNı 'nin çalışması için, istemci, TLS el sıkışması sırasında güvenli oturum ana bilgisayar adını, sunucunun doğru sertifikayı sağlayabilmesi için gönderir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-345">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="79fe6-346">İstemci, TLS anlaşmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için bulunan sertifikayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-346">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="79fe6-347">Kestrel, geri çağırma aracılığıyla SNı destekler `ServerCertificateSelector` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-347">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="79fe6-348">Geri çağırma, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesini sağlamak için bağlantı başına bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-348">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="79fe6-349">SNı desteği şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-349">SNI support requires:</span></span>

* <span data-ttu-id="79fe6-350">Hedef Framework `netcoreapp2.1` veya sonraki sürümlerde çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="79fe6-350">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="79fe6-351">`net461`Veya sonraki sürümlerde, geri çağırma çağrılır, ancak `name` her zaman olur `null` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-351">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="79fe6-352">`name`Ayrıca, `null` ISTEMCI, TLS el sıkışmasının ana bilgisayar adı parametresini sağlamıyorsa de olur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-352">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="79fe6-353">Tüm Web siteleri aynı Kestrel örneğinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-353">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="79fe6-354">Kestrel, bir IP adresi ve bağlantı noktasının bir ters proxy olmadan birden çok örnek arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="79fe6-354">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="79fe6-355">Bağlantı günlüğü</span><span class="sxs-lookup"><span data-stu-id="79fe6-355">Connection logging</span></span>

<span data-ttu-id="79fe6-356"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>Bir bağlantıda bayt düzeyinde iletişim Için hata ayıklama düzeyi günlüklerini yayma çağrısı.</span><span class="sxs-lookup"><span data-stu-id="79fe6-356">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="79fe6-357">Bağlantı günlüğü, TLS şifreleme ve proxy 'nin arkasındaki gibi alt düzey iletişimde sorunları gidermeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-357">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="79fe6-358">`UseConnectionLogging`Daha önce yerleştirilmişse `UseHttps` , şifrelenmiş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-358">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="79fe6-359">`UseConnectionLogging`Öğesinden sonra yerleştirilmişse `UseHttps` , şifresi çözülmüş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-359">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="79fe6-360">TCP yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="79fe6-360">Bind to a TCP socket</span></span>

<span data-ttu-id="79fe6-361"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Yöntemi BIR TCP yuvasına bağlanır ve bir seçenek lambda X. 509.440 sertifika yapılandırmasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-361">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="79fe6-362">Örnek, HTTPS 'yi ile bir uç nokta için yapılandırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="79fe6-362">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="79fe6-363">Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak üzere aynı API 'yi kullanın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-363">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="79fe6-364">UNIX yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="79fe6-364">Bind to a Unix socket</span></span>

<span data-ttu-id="79fe6-365"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>Aşağıdaki örnekte gösterildiği gibi NGINX ile gelişmiş performans için ile birlikte bir UNIX yuvası dinleyin:</span><span class="sxs-lookup"><span data-stu-id="79fe6-365">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="79fe6-366">NGINX yapılandırma dosyasında, `server`  >  `location`  >  `proxy_pass` girdisini olarak ayarlayın `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-366">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="79fe6-367">`{KESTREL SOCKET}` , için belirtilen yuvanın adıdır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (örneğin, `kestrel-test.sock` Önceki örnekte).</span><span class="sxs-lookup"><span data-stu-id="79fe6-367">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="79fe6-368">Yuvanın NGINX tarafından yazılabilir olduğundan emin olun (örneğin, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="79fe6-368">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="79fe6-369">Bağlantı noktası 0</span><span class="sxs-lookup"><span data-stu-id="79fe6-369">Port 0</span></span>

<span data-ttu-id="79fe6-370">Bağlantı noktası numarası `0` belirtildiğinde, Kestrel dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-370">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="79fe6-371">Aşağıdaki örnek, çalışma zamanında Kestrel gerçekte hangi bağlantı noktasının gerçekten bağlandığını nasıl belirleyeceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-371">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="79fe6-372">Uygulama çalıştırıldığında konsol penceresi çıkışı, uygulamanın erişilebileceği dinamik bağlantı noktasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-372">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="79fe6-373">Sınırlamalar</span><span class="sxs-lookup"><span data-stu-id="79fe6-373">Limitations</span></span>

<span data-ttu-id="79fe6-374">Aşağıdaki yaklaşımlar ile uç noktaları yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="79fe6-374">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="79fe6-375">`--urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="79fe6-375">`--urls` command-line argument</span></span>
* <span data-ttu-id="79fe6-376">`urls` Ana bilgisayar yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="79fe6-376">`urls` host configuration key</span></span>
* <span data-ttu-id="79fe6-377">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="79fe6-377">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="79fe6-378">Bu yöntemler, kodun Kestrel dışındaki sunucularla çalışmasını sağlamak için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-378">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="79fe6-379">Ancak, aşağıdaki sınırlamalara dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="79fe6-379">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="79fe6-380">HTTPS uç noktası yapılandırmasında varsayılan bir sertifika sağlanmadığı sürece HTTPS bu yaklaşımlar ile kullanılamaz (örneğin, `KestrelServerOptions` Bu konunun önceki kısımlarında gösterildiği gibi yapılandırma veya yapılandırma dosyası kullanılıyor).</span><span class="sxs-lookup"><span data-stu-id="79fe6-380">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="79fe6-381">Hem hem de `Listen` `UseUrls` yaklaşımları aynı anda kullanıldığında, uç noktalar `Listen` `UseUrls` uç noktaları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="79fe6-381">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="79fe6-382">IIS uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="79fe6-382">IIS endpoint configuration</span></span>

<span data-ttu-id="79fe6-383">IIS kullanırken, IIS geçersiz kılma bağlamaları için URL bağlamaları veya ya da tarafından ayarlanır `Listen` `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-383">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="79fe6-384">Daha fazla bilgi için [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-384">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="79fe6-385">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="79fe6-385">ListenOptions.Protocols</span></span>

<span data-ttu-id="79fe6-386">`Protocols`Özelliği, `HttpProtocols` bir bağlantı uç noktasında veya sunucu IÇIN etkin HTTP protokollerini () belirler.</span><span class="sxs-lookup"><span data-stu-id="79fe6-386">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="79fe6-387">`Protocols`Sabit listesinden özelliğe bir değer atayın `HttpProtocols` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-387">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="79fe6-388">`HttpProtocols` sabit listesi değeri</span><span class="sxs-lookup"><span data-stu-id="79fe6-388">`HttpProtocols` enum value</span></span> | <span data-ttu-id="79fe6-389">Bağlantı protokolü izin verildi</span><span class="sxs-lookup"><span data-stu-id="79fe6-389">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="79fe6-390">Yalnızca HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="79fe6-390">HTTP/1.1 only.</span></span> <span data-ttu-id="79fe6-391">, TLS olmadan veya ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-391">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="79fe6-392">Yalnızca HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="79fe6-392">HTTP/2 only.</span></span> <span data-ttu-id="79fe6-393">Yalnızca istemci [önceki bir bilgi modunu](https://tools.ietf.org/html/rfc7540#section-3.4)DESTEKLIYORSA, TLS olmadan kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-393">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="79fe6-394">HTTP/1.1 ve HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="79fe6-394">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="79fe6-395">HTTP/2, istemcinin TLS [uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) el sıkışmasında http/2 seçmesini gerektirir; Aksi takdirde, bağlantı varsayılan olarak HTTP/1.1 ' dir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-395">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="79fe6-396">`ListenOptions.Protocols`Herhangi bir uç nokta için varsayılan değer `HttpProtocols.Http1AndHttp2` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-396">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="79fe6-397">HTTP/2 için TLS kısıtlamaları:</span><span class="sxs-lookup"><span data-stu-id="79fe6-397">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="79fe6-398">TLS sürüm 1,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="79fe6-398">TLS version 1.2 or later</span></span>
* <span data-ttu-id="79fe6-399">Yeniden anlaşma devre dışı</span><span class="sxs-lookup"><span data-stu-id="79fe6-399">Renegotiation disabled</span></span>
* <span data-ttu-id="79fe6-400">Sıkıştırma devre dışı</span><span class="sxs-lookup"><span data-stu-id="79fe6-400">Compression disabled</span></span>
* <span data-ttu-id="79fe6-401">En az kısa ömürlü anahtar değişim boyutları:</span><span class="sxs-lookup"><span data-stu-id="79fe6-401">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="79fe6-402">Eliptik Eğri Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bit minimum</span><span class="sxs-lookup"><span data-stu-id="79fe6-402">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="79fe6-403">Sınırlı alan Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; : 2048 bit minimum</span><span class="sxs-lookup"><span data-stu-id="79fe6-403">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="79fe6-404">Şifre paketi yasaklandı.</span><span class="sxs-lookup"><span data-stu-id="79fe6-404">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="79fe6-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; P-256 eliptik eğrisi &lbrack; `FIPS186` &rbrack; Varsayılan olarak desteklenir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="79fe6-406">Aşağıdaki örnek, 8000 numaralı bağlantı noktasında HTTP/1.1 ve HTTP/2 bağlantılarına izin verir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-406">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="79fe6-407">Bağlantılar, sağlanan bir sertifikayla TLS ile güvenlidir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-407">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="79fe6-408">Gerektiğinde belirli şifrelemeler için TLS el sıkışmaları için bağlantı temelinde filtre uygulamak üzere bağlantı ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-408">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="79fe6-409">Aşağıdaki örnek, <xref:System.NotSupportedException> uygulamanın desteklemediği herhangi bir şifre algoritması için oluşturur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-409">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="79fe6-410">Alternatif olarak, [ılshandshakefeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) öğesini kabul edilebilir şifreleme paketleri listesi ile tanımlayın ve karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-410">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="79fe6-411">[CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) şifre algoritması ile hiçbir şifreleme kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="79fe6-411">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="79fe6-412">Bağlantı filtrelemesi, bir lambda aracılığıyla da yapılandırılabilir <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> :</span><span class="sxs-lookup"><span data-stu-id="79fe6-412">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="79fe6-413">Linux 'ta, <xref:System.Net.Security.CipherSuitesPolicy> TLS el sıkışmaları her bağlantı temelinde filtrelemek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-413">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="79fe6-414">*Protokolü yapılandırmadan ayarla*</span><span class="sxs-lookup"><span data-stu-id="79fe6-414">*Set the protocol from configuration*</span></span>

<span data-ttu-id="79fe6-415">`CreateDefaultBuilder``serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`Kestrel yapılandırmasını yüklemek için varsayılan olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-415">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="79fe6-416">Aşağıdakiappsettings.js, tüm uç noktaları için varsayılan bağlantı protokolü olarak HTTP/1.1 \* '\* i oluşturur:</span><span class="sxs-lookup"><span data-stu-id="79fe6-416">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="79fe6-417">Aşağıdaki *appsettings.js* örnek, belirli bir uç nokta için http/1.1 bağlantı protokolünü belirler:</span><span class="sxs-lookup"><span data-stu-id="79fe6-417">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="79fe6-418">Yapılandırma tarafından ayarlanan kod geçersiz kılma değerlerinde belirtilen protokoller.</span><span class="sxs-lookup"><span data-stu-id="79fe6-418">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="79fe6-419">Aktarım yapılandırması</span><span class="sxs-lookup"><span data-stu-id="79fe6-419">Transport configuration</span></span>

<span data-ttu-id="79fe6-420">Libuv () kullanımını gerektiren projeler için <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="79fe6-420">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="79fe6-421">Uygulamanın proje dosyasına [Microsoft. AspNetCore. Server. Kestrel. Transport. libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) paketi için bir bağımlılık ekleyin:</span><span class="sxs-lookup"><span data-stu-id="79fe6-421">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="79fe6-422">Şunu çağırın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="79fe6-422">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="79fe6-423">URL önekleri</span><span class="sxs-lookup"><span data-stu-id="79fe6-423">URL prefixes</span></span>

<span data-ttu-id="79fe6-424">`UseUrls`, `--urls` Komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı veya `ASPNETCORE_URLS` ortam değişkeni kullanılırken, URL önekleri aşağıdaki biçimlerden birinde olabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-424">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="79fe6-425">Yalnızca HTTP URL ön ekleri geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-425">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="79fe6-426">Kestrel, kullanılarak URL bağlamaları yapılandırılırken HTTPS 'YI desteklemez `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-426">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="79fe6-427">Bağlantı noktası numarası olan IPv4 adresi</span><span class="sxs-lookup"><span data-stu-id="79fe6-427">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="79fe6-428">`0.0.0.0` Tüm IPv4 adreslerine bağlanan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-428">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="79fe6-429">Bağlantı noktası numarasına sahip IPv6 adresi</span><span class="sxs-lookup"><span data-stu-id="79fe6-429">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="79fe6-430">`[::]` , IPv4 'un IPv6 eşdeğeridir `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-430">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="79fe6-431">Bağlantı noktası numarası olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="79fe6-431">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="79fe6-432">, Ve ana bilgisayar adları `*` `+` özel değildir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-432">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="79fe6-433">Geçerli bir IP adresi olarak tanınmayan veya `localhost` tüm IPv4 ve IPv6 IP 'lerine bağlanan her şey.</span><span class="sxs-lookup"><span data-stu-id="79fe6-433">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="79fe6-434">Farklı ana bilgisayar adlarını aynı bağlantı noktasında farklı ASP.NET Core uygulamalarına bağlamak için, [HTTP.sys](xref:fundamentals/servers/httpsys) veya IIS, NGINX veya Apache gibi bir ters proxy sunucusunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-434">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="79fe6-435">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-435">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="79fe6-436">Port numarası `localhost` veya bağlantı noktası numarası ile geri döngü IP 'si olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="79fe6-436">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="79fe6-437">Belirtildiğinde `localhost` , Kestrel hem IPv4 hem de IPv6 geri döngü arabirimlerini bağlamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-437">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="79fe6-438">İstenen bağlantı noktası, herhangi bir geri döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="79fe6-438">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="79fe6-439">Herhangi bir geri döngü arabirimi başka bir nedenle kullanılamıyorsa (genellikle IPv6 desteklenmediği için), Kestrel bir uyarı kaydeder.</span><span class="sxs-lookup"><span data-stu-id="79fe6-439">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="79fe6-440">Konak filtreleme</span><span class="sxs-lookup"><span data-stu-id="79fe6-440">Host filtering</span></span>

<span data-ttu-id="79fe6-441">Kestrel gibi önekleri temel alarak yapılandırmayı desteklese `http://example.com:5000` de, Kestrel büyük ölçüde ana bilgisayar adını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="79fe6-441">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="79fe6-442">Ana bilgisayar `localhost` , geri döngü adreslerine bağlama için kullanılan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-442">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="79fe6-443">Açık IP adresi dışındaki tüm ana bilgisayar tüm genel IP adreslerine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-443">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="79fe6-444">`Host` Üstbilgiler doğrulanmadı.</span><span class="sxs-lookup"><span data-stu-id="79fe6-444">`Host` headers aren't validated.</span></span>

<span data-ttu-id="79fe6-445">Geçici bir çözüm olarak, ana bilgisayar filtreleme ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-445">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="79fe6-446">Ana bilgisayar filtreleme ara yazılımı, ASP.NET Core uygulamaları için örtük olarak sunulan [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-446">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="79fe6-447">Ara yazılım tarafından eklenir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ve şunları çağırır <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="79fe6-447">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="79fe6-448">Ana bilgisayar filtreleme ara yazılımı varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-448">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="79fe6-449">Ara yazılımı etkinleştirmek için, `AllowedHosts` appSettings *üzerindeappsettings.js*bir anahtar tanımlayın / *. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="79fe6-449">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="79fe6-450">Değer, bağlantı noktası numaraları olmayan ana bilgisayar adlarının noktalı virgülle ayrılmış listesidir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-450">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="79fe6-451">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="79fe6-451">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="79fe6-452">[Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer) da bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçenek içerir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-452">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="79fe6-453">İletilen üstbilgiler ara yazılımı ve ana bilgisayar filtreleme ara yazılımı, farklı senaryolar için benzer işlevlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-453">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="79fe6-454">`AllowedHosts`Iletilen üstbilgiler ara yazılımı ile, `Host` istekler ters bir ara sunucu veya yük dengeleyici ile iletilirken üst bilgi korunurken, bu işlem için uygun bir ayar vardır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-454">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="79fe6-455">`AllowedHosts`Ana bilgisayar filtreleme ara yazılımı ile ayarlama, Kestrel herkese açık bir uç sunucu olarak veya `Host` üst bilgi doğrudan iletildiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-455">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="79fe6-456">Iletilen üstbilgiler ara yazılımı hakkında daha fazla bilgi için bkz <xref:host-and-deploy/proxy-load-balancer> ..</span><span class="sxs-lookup"><span data-stu-id="79fe6-456">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="79fe6-457">Kestrel, ASP.NET Core için platformlar arası [Web sunucusudur](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="79fe6-457">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="79fe6-458">Kestrel, ASP.NET Core proje şablonlarında varsayılan olarak bulunan Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-458">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="79fe6-459">Kestrel aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="79fe6-459">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="79fe6-460">HTTPS</span><span class="sxs-lookup"><span data-stu-id="79fe6-460">HTTPS</span></span>
* <span data-ttu-id="79fe6-461">[WebSockets](https://github.com/aspnet/websockets) 'i etkinleştirmek için kullanılan donuk yükseltme</span><span class="sxs-lookup"><span data-stu-id="79fe6-461">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="79fe6-462">NGINX 'in arkasında yüksek performans için UNIX Yuvaları</span><span class="sxs-lookup"><span data-stu-id="79fe6-462">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="79fe6-463">HTTP/2 (macOS hariç &dagger; )</span><span class="sxs-lookup"><span data-stu-id="79fe6-463">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="79fe6-464">&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-464">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="79fe6-465">Kestrel, .NET Core 'un desteklediği tüm platformlarda ve sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-465">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="79fe6-466">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="79fe6-466">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="79fe6-467">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="79fe6-467">HTTP/2 support</span></span>

<span data-ttu-id="79fe6-468">Aşağıdaki temel gereksinimler karşılanıyorsa, [http/2](https://httpwg.org/specs/rfc7540.html) ASP.NET Core uygulamalar için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-468">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="79fe6-469">İşletim Sistemi&dagger;</span><span class="sxs-lookup"><span data-stu-id="79fe6-469">Operating system&dagger;</span></span>
  * <span data-ttu-id="79fe6-470">Windows Server 2016/Windows 10 veya üzeri&Dagger;</span><span class="sxs-lookup"><span data-stu-id="79fe6-470">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="79fe6-471">OpenSSL 1.0.2 veya üzerini içeren Linux (örneğin, Ubuntu 16,04 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="79fe6-471">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="79fe6-472">Hedef Framework: .NET Core 2,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="79fe6-472">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="79fe6-473">[Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı</span><span class="sxs-lookup"><span data-stu-id="79fe6-473">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="79fe6-474">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="79fe6-474">TLS 1.2 or later connection</span></span>

<span data-ttu-id="79fe6-475">&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-475">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="79fe6-476">&Dagger;Kestrel, Windows Server 2012 R2 ve Windows 8.1 'de HTTP/2 için sınırlı destek içerir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-476">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="79fe6-477">Bu işletim sistemlerinde kullanılabilir olan desteklenen TLS şifre paketlerinin listesi sınırlı olduğundan destek sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-477">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="79fe6-478">TLS bağlantılarının güvenliğini sağlamak için Eliptik Eğri dijital Imza algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-478">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="79fe6-479">Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-479">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="79fe6-480">HTTP/2 varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-480">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="79fe6-481">Yapılandırma hakkında daha fazla bilgi için [Kestrel Options](#kestrel-options) ve [Listenoptions. Protocols](#listenoptionsprotocols) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-481">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="79fe6-482">Ters ara sunucu ile Kestrel ne zaman kullanılır?</span><span class="sxs-lookup"><span data-stu-id="79fe6-482">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="79fe6-483">Kestrel, kendisi veya [Internet Information Services (IIS)](https://www.iis.net/), [NGINX](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi bir *ters ara sunucu*ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-483">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="79fe6-484">Ters proxy sunucusu, ağdan gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-484">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="79fe6-485">Sınır (Internet 'e yönelik) Web sunucusu olarak kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="79fe6-485">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel, ters proxy sunucusu olmadan doğrudan Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="79fe6-487">Ters Proxy yapılandırmasında kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="79fe6-487">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel, IIS, NGINX veya Apache gibi bir ters ara sunucu üzerinden Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="79fe6-489">İki yapılandırma de, ters ara sunucu sunucusuyla veya olmadan, desteklenen bir barındırma yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="79fe6-489">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="79fe6-490">Ters proxy sunucusu olmayan bir uç sunucu olarak kullanılan Kestrel, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="79fe6-490">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="79fe6-491">Kestrel bir bağlantı noktasını dinlemek üzere yapılandırıldığında, Kestrel isteklerin üst bilgilerinden bağımsız olarak bu bağlantı noktası için tüm trafiği işler `Host` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-491">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="79fe6-492">Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel 'e iletme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-492">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="79fe6-493">Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucu kullanılması iyi bir seçim olabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-493">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="79fe6-494">Ters proxy:</span><span class="sxs-lookup"><span data-stu-id="79fe6-494">A reverse proxy:</span></span>

* <span data-ttu-id="79fe6-495">, Barındırdığı uygulamaların açığa çıkarılan genel yüzey alanını sınırlayabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-495">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="79fe6-496">Ek bir yapılandırma ve savunma katmanı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-496">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="79fe6-497">, Mevcut altyapıyla daha iyi tümleşebilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-497">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="79fe6-498">Yük dengelemeyi ve güvenli iletişim (HTTPS) yapılandırmasını kolaylaştırın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-498">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="79fe6-499">Yalnızca ters proxy sunucusu bir X. 509.440 sertifikası gerektirir ve bu sunucu, düz HTTP kullanarak, iç ağdaki uygulamanın sunucularıyla iletişim kurabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-499">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="79fe6-500">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-500">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="79fe6-501">ASP.NET Core uygulamalarında Kestrel kullanma</span><span class="sxs-lookup"><span data-stu-id="79fe6-501">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="79fe6-502">Microsoft. [AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) paketi [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-502">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="79fe6-503">ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-503">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="79fe6-504">*Program.cs*' de, şablon kodu çağırır <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ve bu da <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> arka planda çağrı yapılır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-504">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="79fe6-505">Ana bilgisayarı hakkında daha fazla bilgi için `CreateDefaultBuilder` , uygulamasının *konak ayarlama* bölümüne bakın <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="79fe6-505">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="79fe6-506">Çağrıldıktan sonra ek yapılandırma sağlamak için `CreateDefaultBuilder` şunu kullanın `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="79fe6-506">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="79fe6-507">Uygulama, `CreateDefaultBuilder` Konağı kurmak için çağırmazsa, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> çağrılmadan **önce** çağırın `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="79fe6-507">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="79fe6-508">Kestrel seçenekleri</span><span class="sxs-lookup"><span data-stu-id="79fe6-508">Kestrel options</span></span>

<span data-ttu-id="79fe6-509">Kestrel Web sunucusu, Internet 'e yönelik dağıtımlarda özellikle yararlı olan kısıtlama yapılandırma seçeneklerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-509">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="79fe6-510">Sınıfının özelliğinde kısıtlamaları ayarlayın <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> .</span><span class="sxs-lookup"><span data-stu-id="79fe6-510">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="79fe6-511">`Limits`Özelliği, sınıfının bir örneğini barındırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> .</span><span class="sxs-lookup"><span data-stu-id="79fe6-511">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="79fe6-512">Aşağıdaki örnekler <xref:Microsoft.AspNetCore.Server.Kestrel.Core> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="79fe6-512">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="79fe6-513">Aşağıdaki örneklerde C# kodunda yapılandırılan Kestrel seçenekleri de bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-513">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="79fe6-514">Örneğin, dosya yapılandırma sağlayıcısı Kestrel yapılandırmasını bir *appsettings.js* veya appSettings 'ten yükleyebilir *. { Environment}. JSON* dosyası:</span><span class="sxs-lookup"><span data-stu-id="79fe6-514">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="79fe6-515">Aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="79fe6-515">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="79fe6-516">Kestrel 'i yapılandırma `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="79fe6-516">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="79fe6-517">Sınıfına bir örneği `IConfiguration` ekleyin `Startup` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-517">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="79fe6-518">Aşağıdaki örnek, eklenen yapılandırmanın özelliğe atandığını varsayar `Configuration` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-518">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="79fe6-519">İçinde `Startup.ConfigureServices` , `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="79fe6-519">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="79fe6-520">Ana bilgisayarı oluştururken Kestrel yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="79fe6-520">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="79fe6-521">*Program.cs*' de, `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="79fe6-521">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="79fe6-522">Yukarıdaki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-522">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="79fe6-523">Etkin tut zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="79fe6-523">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="79fe6-524">[Etkin tutma zaman aşımını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="79fe6-524">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="79fe6-525">Varsayılan olarak 2 dakikadır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-525">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="79fe6-526">İstemci bağlantıları üst sınırı</span><span class="sxs-lookup"><span data-stu-id="79fe6-526">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="79fe6-527">En fazla eş zamanlı açık TCP bağlantısı sayısı tüm uygulama için aşağıdaki kodla ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-527">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="79fe6-528">HTTP veya HTTPS 'den başka bir protokole (örneğin, bir WebSockets isteğinde) yükseltilen bağlantılara yönelik ayrı bir sınır vardır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-528">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="79fe6-529">Bir bağlantı yükseltildikten sonra, bu sınıra göre sayılmaz `MaxConcurrentConnections` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-529">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="79fe6-530">En fazla bağlantı sayısı, varsayılan olarak sınırsız (null).</span><span class="sxs-lookup"><span data-stu-id="79fe6-530">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="79fe6-531">En büyük istek gövdesi boyutu</span><span class="sxs-lookup"><span data-stu-id="79fe6-531">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="79fe6-532">Varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu değer yaklaşık 28,6 MB 'tır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-532">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="79fe6-533">ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yaklaşım, <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="79fe6-533">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="79fe6-534">Her istekte uygulama için kısıtlamanın nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-534">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="79fe6-535">Ara yazılım içindeki belirli bir istek üzerindeki ayarı geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="79fe6-535">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="79fe6-536">Uygulama isteği okumaya başladıktan sonra bir istek üzerindeki sınırı yapılandırırsa, bir özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-536">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="79fe6-537">`IsReadOnly`Özelliğin salt okuma durumunda olup olmadığını belirten bir özellik vardır. Bu, `MaxRequestBodySize` sınırı yapılandırmanın çok geç olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-537">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="79fe6-538">Bir uygulama [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)arkasında [çalıştırıldığında](xref:host-and-deploy/iis/index#out-of-process-hosting-model) , IIS sınırı zaten ayarladığı için Kestrel 'nin istek gövdesi boyut sınırı devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-538">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="79fe6-539">En az istek gövdesi veri hızı</span><span class="sxs-lookup"><span data-stu-id="79fe6-539">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="79fe6-540">Kestrel bayt/saniye cinsinden belirtilen fiyata ulaşan her saniye sonra denetler.</span><span class="sxs-lookup"><span data-stu-id="79fe6-540">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="79fe6-541">Oran en düşük değerin altına düşerse bağlantı zaman aşımına uğrar. Yetkisiz kullanım süresi, Kestrel 'in istemciye gönderme oranını en düşük süreye kadar artırabilme Bu süre boyunca fiyat denetlenmez.</span><span class="sxs-lookup"><span data-stu-id="79fe6-541">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="79fe6-542">Yetkisiz kullanım süresi, TCP yavaş başlatma nedeniyle başlangıçta verileri yavaş bir hızda gönderen bağlantıların bırakılmasını önlemeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-542">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="79fe6-543">Varsayılan en düşük oran, 5 saniyelik bir yetkisiz kullanım süresi ile 240 bayt/saniye olur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-543">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="79fe6-544">Yanıt için bir minimum oran da geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-544">A minimum rate also applies to the response.</span></span> <span data-ttu-id="79fe6-545">İstek sınırını ayarlamaya yönelik kod ve yanıt sınırı, `RequestBody` `Response` özellik ve arabirim adlarında olduğu gibi aynı olur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-545">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="79fe6-546">*Program.cs*içinde en düşük veri hızlarının nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-546">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="79fe6-547">Ara yazılım içindeki istek başına düşen minimum hız sınırlarını geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="79fe6-547">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="79fe6-548">`HttpContext.Features`İstek çoğullama için protokol desteği nedeniyle, http/2 için bir istek başına hız sınırlarını değiştirmek, http/2 istekleri için, önceki örnekte başvurulan hiçbir oran özelliği mevcut değil.</span><span class="sxs-lookup"><span data-stu-id="79fe6-548">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="79fe6-549">`KestrelServerOptions.Limits`Http/1. x ve http/2 bağlantılarına hala uygulanan sunucu genelindeki hız sınırları.</span><span class="sxs-lookup"><span data-stu-id="79fe6-549">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="79fe6-550">İstek üst bilgileri zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="79fe6-550">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="79fe6-551">Sunucunun istek üst bilgilerini alması için harcadığı en uzun süreyi alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="79fe6-551">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="79fe6-552">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-552">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="79fe6-553">Bağlantı başına en fazla akış</span><span class="sxs-lookup"><span data-stu-id="79fe6-553">Maximum streams per connection</span></span>

<span data-ttu-id="79fe6-554">`Http2.MaxStreamsPerConnection` HTTP/2 bağlantısı başına eşzamanlı istek akışı sayısını sınırlar.</span><span class="sxs-lookup"><span data-stu-id="79fe6-554">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="79fe6-555">Fazlalık akışlar reddedildi.</span><span class="sxs-lookup"><span data-stu-id="79fe6-555">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="79fe6-556">Varsayılan değer 100’dür.</span><span class="sxs-lookup"><span data-stu-id="79fe6-556">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="79fe6-557">Üst bilgi tablosu boyutu</span><span class="sxs-lookup"><span data-stu-id="79fe6-557">Header table size</span></span>

<span data-ttu-id="79fe6-558">HPACK kod çözücüsü HTTP/2 bağlantıları için HTTP üstbilgilerini açar.</span><span class="sxs-lookup"><span data-stu-id="79fe6-558">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="79fe6-559">`Http2.HeaderTableSize` HPACK kod çözücüsünün kullandığı üst bilgi sıkıştırma tablosunun boyutunu sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-559">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="79fe6-560">Değer sekizli cinsinden sağlanır ve sıfırdan büyük olmalıdır (0).</span><span class="sxs-lookup"><span data-stu-id="79fe6-560">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="79fe6-561">Varsayılan değer 4096 ' dir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-561">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="79fe6-562">En büyük çerçeve boyutu</span><span class="sxs-lookup"><span data-stu-id="79fe6-562">Maximum frame size</span></span>

<span data-ttu-id="79fe6-563">`Http2.MaxFrameSize` alacak HTTP/2 bağlantı çerçevesi yükünün en büyük boyutunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-563">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="79fe6-564">Değer sekizli cinsinden sağlanır ve 2 ^ 14 (16.384) ile 2 ^ 24-1 (16.777.215) arasında olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-564">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="79fe6-565">Varsayılan değer 2 ^ 14 ' dir (16.384).</span><span class="sxs-lookup"><span data-stu-id="79fe6-565">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="79fe6-566">En fazla istek üst bilgi boyutu</span><span class="sxs-lookup"><span data-stu-id="79fe6-566">Maximum request header size</span></span>

<span data-ttu-id="79fe6-567">`Http2.MaxRequestHeaderFieldSize` istek üst bilgisi değerlerinin sekizlisi cinsinden izin verilen en büyük boyutu belirtir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-567">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="79fe6-568">Bu sınır, hem sıkıştırılmış hem de sıkıştırılmamış temsillerinde birlikte hem ad hem de değer için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-568">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="79fe6-569">Değer sıfırdan büyük (0) olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-569">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="79fe6-570">Varsayılan değer 8.192 ' dir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-570">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="79fe6-571">İlk bağlantı pencere boyutu</span><span class="sxs-lookup"><span data-stu-id="79fe6-571">Initial connection window size</span></span>

<span data-ttu-id="79fe6-572">`Http2.InitialConnectionWindowSize` sunucu, bağlantı başına tüm istekler (akışlar) genelinde toplanan tek seferde sunucunun arabelleğe aldığı en fazla istek gövde verilerini bayt cinsinden gösterir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-572">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="79fe6-573">İstekleri ile de sınırlıdır `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-573">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="79fe6-574">Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-574">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="79fe6-575">Varsayılan değer 128 KB 'tır (131.072).</span><span class="sxs-lookup"><span data-stu-id="79fe6-575">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="79fe6-576">İlk akış pencere boyutu</span><span class="sxs-lookup"><span data-stu-id="79fe6-576">Initial stream window size</span></span>

<span data-ttu-id="79fe6-577">`Http2.InitialStreamWindowSize` sunucu, istek başına bir kez (Stream) arabelleğe alınan en fazla istek gövde verilerini bayt cinsinden gösterir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-577">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="79fe6-578">İstekleri ile de sınırlıdır `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-578">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="79fe6-579">Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-579">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="79fe6-580">Varsayılan değer 96 KB 'tır (98.304).</span><span class="sxs-lookup"><span data-stu-id="79fe6-580">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="79fe6-581">Zaman Uyumlu G/Ç</span><span class="sxs-lookup"><span data-stu-id="79fe6-581">Synchronous I/O</span></span>

<span data-ttu-id="79fe6-582"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> istek ve yanıt için zaman uyumlu g/ç 'ye izin verilip verilmediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="79fe6-582"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="79fe6-583">Varsayılan değer `true` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-583">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="79fe6-584">Çok sayıda engelleme zaman uyumlu g/ç işlemi, iş parçacığı havuzuna yol açabilir, bu da uygulamanın yanıt vermemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-584">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="79fe6-585">Yalnızca `AllowSynchronousIO` zaman uyumsuz g/ç desteklemeyen bir kitaplık kullanırken etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="79fe6-585">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="79fe6-586">Aşağıdaki örnek, zaman uyumlu g/ç 'yi sunar:</span><span class="sxs-lookup"><span data-stu-id="79fe6-586">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="79fe6-587">Diğer Kestrel seçenekleri ve limitleri hakkında daha fazla bilgi için bkz.:</span><span class="sxs-lookup"><span data-stu-id="79fe6-587">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="79fe6-588">Uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="79fe6-588">Endpoint configuration</span></span>

<span data-ttu-id="79fe6-589">Varsayılan olarak, ASP.NET Core bağlar:</span><span class="sxs-lookup"><span data-stu-id="79fe6-589">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="79fe6-590">`https://localhost:5001` (bir yerel geliştirme sertifikası varsa)</span><span class="sxs-lookup"><span data-stu-id="79fe6-590">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="79fe6-591">Kullanarak URL 'Leri belirtin:</span><span class="sxs-lookup"><span data-stu-id="79fe6-591">Specify URLs using the:</span></span>

* <span data-ttu-id="79fe6-592">`ASPNETCORE_URLS` ortam değişkeni.</span><span class="sxs-lookup"><span data-stu-id="79fe6-592">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="79fe6-593">`--urls` komut satırı bağımsız değişkeni.</span><span class="sxs-lookup"><span data-stu-id="79fe6-593">`--urls` command-line argument.</span></span>
* <span data-ttu-id="79fe6-594">`urls` Ana bilgisayar yapılandırma anahtarı.</span><span class="sxs-lookup"><span data-stu-id="79fe6-594">`urls` host configuration key.</span></span>
* <span data-ttu-id="79fe6-595">`UseUrls` genişletme yöntemi.</span><span class="sxs-lookup"><span data-stu-id="79fe6-595">`UseUrls` extension method.</span></span>

<span data-ttu-id="79fe6-596">Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS).</span><span class="sxs-lookup"><span data-stu-id="79fe6-596">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="79fe6-597">Değeri noktalı virgülle ayrılmış bir liste olarak yapılandırın (örneğin, `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="79fe6-597">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="79fe6-598">Bu yaklaşımlar hakkında daha fazla bilgi için [sunucu URL 'leri](xref:fundamentals/host/web-host#server-urls) ve [geçersiz kılma yapılandırması](xref:fundamentals/host/web-host#override-configuration)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-598">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="79fe6-599">Geliştirme sertifikası oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="79fe6-599">A development certificate is created:</span></span>

* <span data-ttu-id="79fe6-600">[.NET Core SDK](/dotnet/core/sdk) yüklendiği zaman.</span><span class="sxs-lookup"><span data-stu-id="79fe6-600">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="79fe6-601">[Geliştirme-CERT aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-601">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="79fe6-602">Bazı tarayıcılarda yerel geliştirme sertifikasına güvenmek için açık izin verilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-602">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="79fe6-603">Proje şablonları, uygulamaları HTTPS üzerinde varsayılan olarak çalışacak şekilde yapılandırır ve [https yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-603">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="79fe6-604"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> URL öneklerini ve bağlantı noktalarını Kestrel için yapılandırmak üzere üzerinde çağrı veya Yöntemler.</span><span class="sxs-lookup"><span data-stu-id="79fe6-604">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="79fe6-605">`UseUrls`, `--urls` komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı ve `ASPNETCORE_URLS` ortam değişkeni de çalışır, ancak bu bölümün ilerleyen kısımlarında belirtilen sınırlamalara sahiptir (https uç noktası yapılandırması için varsayılan sertifika kullanılabilir olmalıdır).</span><span class="sxs-lookup"><span data-stu-id="79fe6-605">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="79fe6-606">`KestrelServerOptions` yapılandırmada</span><span class="sxs-lookup"><span data-stu-id="79fe6-606">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="79fe6-607">ConfigureEndpointDefaults Varsayılanları (eylem \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="79fe6-607">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="79fe6-608">`Action`Belirtilen her bir uç nokta için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-608">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="79fe6-609">`ConfigureEndpointDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-609">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="79fe6-610">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="79fe6-610">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="79fe6-611">ConfigureHttpsDefaults (eylem \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="79fe6-611">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="79fe6-612">`Action`Her HTTPS uç noktası için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-612">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="79fe6-613">`ConfigureHttpsDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-613">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="79fe6-614">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="79fe6-614">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="79fe6-615">Yapılandırma (Iconation)</span><span class="sxs-lookup"><span data-stu-id="79fe6-615">Configure(IConfiguration)</span></span>

<span data-ttu-id="79fe6-616">Bir as girişi alan Kestrel ayarlamak için bir yapılandırma yükleyicisi oluşturur <xref:Microsoft.Extensions.Configuration.IConfiguration> .</span><span class="sxs-lookup"><span data-stu-id="79fe6-616">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="79fe6-617">Yapılandırma, Kestrel için yapılandırma bölümünün kapsamına alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-617">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="79fe6-618">ListenOptions. UseHttps</span><span class="sxs-lookup"><span data-stu-id="79fe6-618">ListenOptions.UseHttps</span></span>

<span data-ttu-id="79fe6-619">Kestrel 'i HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-619">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="79fe6-620">`ListenOptions.UseHttps` uzantılardan</span><span class="sxs-lookup"><span data-stu-id="79fe6-620">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="79fe6-621">`UseHttps`: Kestrel 'i varsayılan sertifikayla HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-621">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="79fe6-622">Varsayılan sertifika yapılandırılmamışsa bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-622">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="79fe6-623">`ListenOptions.UseHttps` parametrelere</span><span class="sxs-lookup"><span data-stu-id="79fe6-623">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="79fe6-624">`filename` , uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yolu ve dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-624">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="79fe6-625">`password` X. 509.440 sertifika verilerine erişmek için parola gereklidir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-625">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="79fe6-626">`configureOptions` , öğesini `Action` yapılandırmak için kullanılır `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-626">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="79fe6-627">Döndürür `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-627">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="79fe6-628">`storeName` , sertifikanın yükleneceği sertifika deposudur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-628">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="79fe6-629">`subject` , sertifika için konu adıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-629">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="79fe6-630">`allowInvalid` geçersiz sertifikaların, otomatik olarak imzalanan sertifikalar gibi göz önünde bulundurulmayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-630">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="79fe6-631">`location` , sertifikanın yükleneceği mağaza konumudur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-631">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="79fe6-632">`serverCertificate` , X. 509.440 sertifikasıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-632">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="79fe6-633">Üretimde HTTPS 'nin açıkça yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-633">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="79fe6-634">En azından, varsayılan bir sertifika sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-634">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="79fe6-635">Daha sonra açıklanan desteklenen yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="79fe6-635">Supported configurations described next:</span></span>

* <span data-ttu-id="79fe6-636">Yapılandırma yok</span><span class="sxs-lookup"><span data-stu-id="79fe6-636">No configuration</span></span>
* <span data-ttu-id="79fe6-637">Varsayılan sertifikayı yapılandırmadan Değiştir</span><span class="sxs-lookup"><span data-stu-id="79fe6-637">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="79fe6-638">Koddaki varsayılanları değiştirme</span><span class="sxs-lookup"><span data-stu-id="79fe6-638">Change the defaults in code</span></span>

<span data-ttu-id="79fe6-639">*Yapılandırma yok*</span><span class="sxs-lookup"><span data-stu-id="79fe6-639">*No configuration*</span></span>

<span data-ttu-id="79fe6-640">Kestrel tarihinde dinler `http://localhost:5000` ve `https://localhost:5001` (varsayılan bir sertifika varsa).</span><span class="sxs-lookup"><span data-stu-id="79fe6-640">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="79fe6-641">*Varsayılan sertifikayı yapılandırmadan Değiştir*</span><span class="sxs-lookup"><span data-stu-id="79fe6-641">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="79fe6-642">`CreateDefaultBuilder``Configure(context.Configuration.GetSection("Kestrel"))`Kestrel yapılandırmasını yüklemek için varsayılan olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-642">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="79fe6-643">Varsayılan bir HTTPS uygulama ayarları yapılandırma şeması Kestrel için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-643">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="79fe6-644">Disk üzerindeki bir dosyadan ya da bir sertifika deposundan kullanılacak URL 'Ler ve Sertifikalar dahil olmak üzere birden çok uç nokta yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-644">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="79fe6-645">Aşağıdaki *appsettings.js* aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-645">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="79fe6-646">Geçersiz sertifikaların kullanılmasına izin vermek için **Allowwınvalid** `true` ' i ayarlayın (örneğin, otomatik olarak imzalanan sertifikalar).</span><span class="sxs-lookup"><span data-stu-id="79fe6-646">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="79fe6-647">Bir sertifika belirtmeyen herhangi bir HTTPS uç noktası (aşağıdaki örnekte bulunan**httpsdefaultcert** ), **Sertifikalar** > **varsayılan** veya geliştirme sertifikası altında tanımlanan sertifikaya geri döner.</span><span class="sxs-lookup"><span data-stu-id="79fe6-647">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="79fe6-648">Herhangi bir sertifika düğümü için **yol** ve **parola** kullanmanın alternatifi sertifika deposu alanlarını kullanarak sertifikayı belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-648">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="79fe6-649">Örneğin, **sertifika**  >  **varsayılan** sertifikası şu şekilde belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-649">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="79fe6-650">Şema notları:</span><span class="sxs-lookup"><span data-stu-id="79fe6-650">Schema notes:</span></span>

* <span data-ttu-id="79fe6-651">Uç nokta adları büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-651">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="79fe6-652">Örneğin, `HTTPS` ve `Https` geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-652">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="79fe6-653">`Url`Her uç nokta için parametresi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-653">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="79fe6-654">Bu parametrenin biçimi, en üst düzey `Urls` yapılandırma parametresiyle aynıdır, ancak tek bir değerle sınırlı olur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-654">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="79fe6-655">Bu uç noktalar, üst düzey yapılandırmada tanımlananlar yerine `Urls` bunlara ekleme yerine bunların yerini alır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-655">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="79fe6-656">Kullanılarak kodda tanımlanan uç noktalar `Listen` yapılandırma bölümünde tanımlanan uç noktalar ile birikimlidir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-656">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="79fe6-657">Bu `Certificate` bölüm isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-657">The `Certificate` section is optional.</span></span> <span data-ttu-id="79fe6-658">`Certificate`Bölüm belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-658">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="79fe6-659">Kullanılabilir varsayılan değer yoksa, sunucu bir özel durum oluşturur ve başlayamaz.</span><span class="sxs-lookup"><span data-stu-id="79fe6-659">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="79fe6-660">Bu `Certificate` bölüm hem **yol** &ndash; **parolasını** hem de **Konu** &ndash; **deposu** sertifikalarını destekler.</span><span class="sxs-lookup"><span data-stu-id="79fe6-660">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="79fe6-661">Herhangi bir sayıda uç nokta, bağlantı noktası çakışmalarına neden olmadıkları sürece bu şekilde tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-661">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="79fe6-662">`options.Configure(context.Configuration.GetSection("{SECTION}"))` yapılandırılmış bir `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` uç noktanın ayarlarını tamamlamak için kullanılabilecek bir yöntemi olan bir döndürür:</span><span class="sxs-lookup"><span data-stu-id="79fe6-662">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="79fe6-663">`KestrelServerOptions.ConfigurationLoader` , tarafından sağlana gibi var olan yükleyicisindeki yinelemeye devam etmek için doğrudan erişilebilir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="79fe6-663">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="79fe6-664">Her uç noktanın yapılandırma bölümü, `Endpoint` özel ayarların okunabilmesi için yöntemindeki seçeneklerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-664">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="79fe6-665">Birden çok yapılandırma, `options.Configure(context.Configuration.GetSection("{SECTION}"))` başka bir bölümle yeniden çağırarak yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-665">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="79fe6-666">Önceki örneklerde açıkça çağrılmadığı takdirde, yalnızca son yapılandırma kullanılır `Load` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-666">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="79fe6-667">Metapackage, `Load` varsayılan yapılandırma bölümünün değiştirilmesini sağlayacak şekilde çağırmıyor.</span><span class="sxs-lookup"><span data-stu-id="79fe6-667">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="79fe6-668">`KestrelConfigurationLoader``Listen`API ailesini `KestrelServerOptions` `Endpoint` aşırı yükleme olarak yansıtır, bu nedenle kod ve yapılandırma uç noktaları aynı yerde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-668">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="79fe6-669">Bu aşırı yüklemeler adları kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları kullanır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-669">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="79fe6-670">*Koddaki varsayılanları değiştirme*</span><span class="sxs-lookup"><span data-stu-id="79fe6-670">*Change the defaults in code*</span></span>

<span data-ttu-id="79fe6-671">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` `ListenOptions` `HttpsConnectionAdapterOptions` , önceki senaryoda belirtilen varsayılan sertifikayı geçersiz kılma dahil, ve için varsayılan ayarları değiştirmek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-671">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="79fe6-672">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-672">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="79fe6-673">*SNı için Kestrel desteği*</span><span class="sxs-lookup"><span data-stu-id="79fe6-673">*Kestrel support for SNI*</span></span>

<span data-ttu-id="79fe6-674">[Sunucu adı belirtme (SNı)](https://tools.ietf.org/html/rfc6066#section-3) , aynı IP adresi ve bağlantı noktası üzerinde birden fazla etki alanını barındırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-674">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="79fe6-675">SNı 'nin çalışması için, istemci, TLS el sıkışması sırasında güvenli oturum ana bilgisayar adını, sunucunun doğru sertifikayı sağlayabilmesi için gönderir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-675">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="79fe6-676">İstemci, TLS anlaşmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için bulunan sertifikayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-676">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="79fe6-677">Kestrel, geri çağırma aracılığıyla SNı destekler `ServerCertificateSelector` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-677">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="79fe6-678">Geri çağırma, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesini sağlamak için bağlantı başına bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-678">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="79fe6-679">SNı desteği şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-679">SNI support requires:</span></span>

* <span data-ttu-id="79fe6-680">Hedef Framework `netcoreapp2.1` veya sonraki sürümlerde çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="79fe6-680">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="79fe6-681">`net461`Veya sonraki sürümlerde, geri çağırma çağrılır, ancak `name` her zaman olur `null` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-681">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="79fe6-682">`name`Ayrıca, `null` ISTEMCI, TLS el sıkışmasının ana bilgisayar adı parametresini sağlamıyorsa de olur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-682">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="79fe6-683">Tüm Web siteleri aynı Kestrel örneğinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-683">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="79fe6-684">Kestrel, bir IP adresi ve bağlantı noktasının bir ters proxy olmadan birden çok örnek arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="79fe6-684">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="79fe6-685">Bağlantı günlüğü</span><span class="sxs-lookup"><span data-stu-id="79fe6-685">Connection logging</span></span>

<span data-ttu-id="79fe6-686"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>Bir bağlantıda bayt düzeyinde iletişim Için hata ayıklama düzeyi günlüklerini yayma çağrısı.</span><span class="sxs-lookup"><span data-stu-id="79fe6-686">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="79fe6-687">Bağlantı günlüğü, TLS şifreleme ve proxy 'nin arkasındaki gibi alt düzey iletişimde sorunları gidermeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-687">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="79fe6-688">`UseConnectionLogging`Daha önce yerleştirilmişse `UseHttps` , şifrelenmiş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-688">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="79fe6-689">`UseConnectionLogging`Öğesinden sonra yerleştirilmişse `UseHttps` , şifresi çözülmüş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-689">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="79fe6-690">TCP yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="79fe6-690">Bind to a TCP socket</span></span>

<span data-ttu-id="79fe6-691"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Yöntemi BIR TCP yuvasına bağlanır ve bir seçenek lambda X. 509.440 sertifika yapılandırmasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-691">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="79fe6-692">Örnek, HTTPS 'yi ile bir uç nokta için yapılandırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="79fe6-692">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="79fe6-693">Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak üzere aynı API 'yi kullanın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-693">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="79fe6-694">UNIX yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="79fe6-694">Bind to a Unix socket</span></span>

<span data-ttu-id="79fe6-695"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>Aşağıdaki örnekte gösterildiği gibi NGINX ile gelişmiş performans için ile birlikte bir UNIX yuvası dinleyin:</span><span class="sxs-lookup"><span data-stu-id="79fe6-695">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="79fe6-696">NGINX confiuguration dosyasında, `server`  >  `location`  >  `proxy_pass` girdisini olarak ayarlayın `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-696">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="79fe6-697">`{KESTREL SOCKET}` , için belirtilen yuvanın adıdır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (örneğin, `kestrel-test.sock` Önceki örnekte).</span><span class="sxs-lookup"><span data-stu-id="79fe6-697">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="79fe6-698">Yuvanın NGINX tarafından yazılabilir olduğundan emin olun (örneğin, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="79fe6-698">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="79fe6-699">Bağlantı noktası 0</span><span class="sxs-lookup"><span data-stu-id="79fe6-699">Port 0</span></span>

<span data-ttu-id="79fe6-700">Bağlantı noktası numarası `0` belirtildiğinde, Kestrel dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-700">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="79fe6-701">Aşağıdaki örnek, çalışma zamanında Kestrel gerçekte hangi bağlantı noktasının gerçekten bağlandığını nasıl belirleyeceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-701">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="79fe6-702">Uygulama çalıştırıldığında konsol penceresi çıkışı, uygulamanın erişilebileceği dinamik bağlantı noktasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-702">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="79fe6-703">Sınırlamalar</span><span class="sxs-lookup"><span data-stu-id="79fe6-703">Limitations</span></span>

<span data-ttu-id="79fe6-704">Aşağıdaki yaklaşımlar ile uç noktaları yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="79fe6-704">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="79fe6-705">`--urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="79fe6-705">`--urls` command-line argument</span></span>
* <span data-ttu-id="79fe6-706">`urls` Ana bilgisayar yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="79fe6-706">`urls` host configuration key</span></span>
* <span data-ttu-id="79fe6-707">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="79fe6-707">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="79fe6-708">Bu yöntemler, kodun Kestrel dışındaki sunucularla çalışmasını sağlamak için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-708">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="79fe6-709">Ancak, aşağıdaki sınırlamalara dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="79fe6-709">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="79fe6-710">HTTPS uç noktası yapılandırmasında varsayılan bir sertifika sağlanmadığı sürece HTTPS bu yaklaşımlar ile kullanılamaz (örneğin, `KestrelServerOptions` Bu konunun önceki kısımlarında gösterildiği gibi yapılandırma veya yapılandırma dosyası kullanılıyor).</span><span class="sxs-lookup"><span data-stu-id="79fe6-710">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="79fe6-711">Hem hem de `Listen` `UseUrls` yaklaşımları aynı anda kullanıldığında, uç noktalar `Listen` `UseUrls` uç noktaları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="79fe6-711">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="79fe6-712">IIS uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="79fe6-712">IIS endpoint configuration</span></span>

<span data-ttu-id="79fe6-713">IIS kullanırken, IIS geçersiz kılma bağlamaları için URL bağlamaları veya ya da tarafından ayarlanır `Listen` `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-713">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="79fe6-714">Daha fazla bilgi için [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-714">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="79fe6-715">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="79fe6-715">ListenOptions.Protocols</span></span>

<span data-ttu-id="79fe6-716">`Protocols`Özelliği, `HttpProtocols` bir bağlantı uç noktasında veya sunucu IÇIN etkin HTTP protokollerini () belirler.</span><span class="sxs-lookup"><span data-stu-id="79fe6-716">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="79fe6-717">`Protocols`Sabit listesinden özelliğe bir değer atayın `HttpProtocols` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-717">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="79fe6-718">`HttpProtocols` sabit listesi değeri</span><span class="sxs-lookup"><span data-stu-id="79fe6-718">`HttpProtocols` enum value</span></span> | <span data-ttu-id="79fe6-719">Bağlantı protokolü izin verildi</span><span class="sxs-lookup"><span data-stu-id="79fe6-719">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="79fe6-720">Yalnızca HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="79fe6-720">HTTP/1.1 only.</span></span> <span data-ttu-id="79fe6-721">, TLS olmadan veya ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-721">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="79fe6-722">Yalnızca HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="79fe6-722">HTTP/2 only.</span></span> <span data-ttu-id="79fe6-723">Yalnızca istemci [önceki bir bilgi modunu](https://tools.ietf.org/html/rfc7540#section-3.4)DESTEKLIYORSA, TLS olmadan kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-723">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="79fe6-724">HTTP/1.1 ve HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="79fe6-724">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="79fe6-725">HTTP/2 bir TLS ve [uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı gerektirir; Aksi takdirde, bağlantı varsayılan olarak HTTP/1.1 ' dir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-725">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="79fe6-726">Varsayılan protokol HTTP/1.1 ' dir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-726">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="79fe6-727">HTTP/2 için TLS kısıtlamaları:</span><span class="sxs-lookup"><span data-stu-id="79fe6-727">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="79fe6-728">TLS sürüm 1,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="79fe6-728">TLS version 1.2 or later</span></span>
* <span data-ttu-id="79fe6-729">Yeniden anlaşma devre dışı</span><span class="sxs-lookup"><span data-stu-id="79fe6-729">Renegotiation disabled</span></span>
* <span data-ttu-id="79fe6-730">Sıkıştırma devre dışı</span><span class="sxs-lookup"><span data-stu-id="79fe6-730">Compression disabled</span></span>
* <span data-ttu-id="79fe6-731">En az kısa ömürlü anahtar değişim boyutları:</span><span class="sxs-lookup"><span data-stu-id="79fe6-731">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="79fe6-732">Eliptik Eğri Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bit minimum</span><span class="sxs-lookup"><span data-stu-id="79fe6-732">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="79fe6-733">Sınırlı alan Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; : 2048 bit minimum</span><span class="sxs-lookup"><span data-stu-id="79fe6-733">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="79fe6-734">Şifre paketi engellenmedi</span><span class="sxs-lookup"><span data-stu-id="79fe6-734">Cipher suite not blocked</span></span>

<span data-ttu-id="79fe6-735">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; P-256 eliptik eğrisi &lbrack; `FIPS186` &rbrack; Varsayılan olarak desteklenir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-735">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="79fe6-736">Aşağıdaki örnek, 8000 numaralı bağlantı noktasında HTTP/1.1 ve HTTP/2 bağlantılarına izin verir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-736">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="79fe6-737">Bağlantılar, sağlanan bir sertifikayla TLS ile güvenlidir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-737">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="79fe6-738">İsteğe bağlı olarak, `IConnectionAdapter` belirli şifrelemeler için bağlantı BAŞıNA TLS el sıkışmaları filtrelemek üzere bir uygulama oluşturun:</span><span class="sxs-lookup"><span data-stu-id="79fe6-738">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="79fe6-739">*Protokolü yapılandırmadan ayarla*</span><span class="sxs-lookup"><span data-stu-id="79fe6-739">*Set the protocol from configuration*</span></span>

<span data-ttu-id="79fe6-740"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>`serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`Kestrel yapılandırmasını yüklemek için varsayılan olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-740"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="79fe6-741">Aşağıdaki *appsettings.js* örnekte, tüm Kestrel uç noktaları için varsayılan bir bağlantı protokolü (http/1.1 ve http/2) oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="79fe6-741">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="79fe6-742">Aşağıdaki yapılandırma dosyası örneği, belirli bir uç nokta için bağlantı protokolü oluşturur:</span><span class="sxs-lookup"><span data-stu-id="79fe6-742">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="79fe6-743">Yapılandırma tarafından ayarlanan kod geçersiz kılma değerlerinde belirtilen protokoller.</span><span class="sxs-lookup"><span data-stu-id="79fe6-743">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="79fe6-744">Aktarım yapılandırması</span><span class="sxs-lookup"><span data-stu-id="79fe6-744">Transport configuration</span></span>

<span data-ttu-id="79fe6-745">ASP.NET Core 2,1 sürümü ile Kestrel 'in varsayılan taşıması artık libuv ' d i temel değildir ancak bunun yerine yönetilen yuvaları temel alır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-745">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="79fe6-746">Bu, çağrıyı yapan ve aşağıdaki paketlerden birine bağlı olan ASP.NET Core 2,0 2,1 uygulamalarının önemli bir değişikliği olur <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="79fe6-746">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="79fe6-747">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (doğrudan paket başvurusu)</span><span class="sxs-lookup"><span data-stu-id="79fe6-747">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="79fe6-748">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="79fe6-748">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="79fe6-749">Libuv kullanımını gerektiren projeler için:</span><span class="sxs-lookup"><span data-stu-id="79fe6-749">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="79fe6-750">Uygulamanın proje dosyasına [Microsoft. AspNetCore. Server. Kestrel. Transport. libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) paketi için bir bağımlılık ekleyin:</span><span class="sxs-lookup"><span data-stu-id="79fe6-750">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="79fe6-751">Çağrı <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="79fe6-751">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="79fe6-752">URL önekleri</span><span class="sxs-lookup"><span data-stu-id="79fe6-752">URL prefixes</span></span>

<span data-ttu-id="79fe6-753">`UseUrls`, `--urls` Komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı veya `ASPNETCORE_URLS` ortam değişkeni kullanılırken, URL önekleri aşağıdaki biçimlerden birinde olabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-753">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="79fe6-754">Yalnızca HTTP URL ön ekleri geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-754">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="79fe6-755">Kestrel, kullanılarak URL bağlamaları yapılandırılırken HTTPS 'YI desteklemez `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-755">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="79fe6-756">Bağlantı noktası numarası olan IPv4 adresi</span><span class="sxs-lookup"><span data-stu-id="79fe6-756">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="79fe6-757">`0.0.0.0` Tüm IPv4 adreslerine bağlanan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-757">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="79fe6-758">Bağlantı noktası numarasına sahip IPv6 adresi</span><span class="sxs-lookup"><span data-stu-id="79fe6-758">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="79fe6-759">`[::]` , IPv4 'un IPv6 eşdeğeridir `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-759">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="79fe6-760">Bağlantı noktası numarası olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="79fe6-760">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="79fe6-761">, Ve ana bilgisayar adları `*` `+` özel değildir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-761">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="79fe6-762">Geçerli bir IP adresi olarak tanınmayan veya `localhost` tüm IPv4 ve IPv6 IP 'lerine bağlanan her şey.</span><span class="sxs-lookup"><span data-stu-id="79fe6-762">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="79fe6-763">Farklı ana bilgisayar adlarını aynı bağlantı noktasında farklı ASP.NET Core uygulamalarına bağlamak için, [HTTP.sys](xref:fundamentals/servers/httpsys) veya IIS, NGINX veya Apache gibi bir ters proxy sunucusunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-763">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="79fe6-764">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-764">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="79fe6-765">Port numarası `localhost` veya bağlantı noktası numarası ile geri döngü IP 'si olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="79fe6-765">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="79fe6-766">Belirtildiğinde `localhost` , Kestrel hem IPv4 hem de IPv6 geri döngü arabirimlerini bağlamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-766">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="79fe6-767">İstenen bağlantı noktası, herhangi bir geri döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="79fe6-767">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="79fe6-768">Herhangi bir geri döngü arabirimi başka bir nedenle kullanılamıyorsa (genellikle IPv6 desteklenmediği için), Kestrel bir uyarı kaydeder.</span><span class="sxs-lookup"><span data-stu-id="79fe6-768">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="79fe6-769">Konak filtreleme</span><span class="sxs-lookup"><span data-stu-id="79fe6-769">Host filtering</span></span>

<span data-ttu-id="79fe6-770">Kestrel gibi önekleri temel alarak yapılandırmayı desteklese `http://example.com:5000` de, Kestrel büyük ölçüde ana bilgisayar adını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="79fe6-770">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="79fe6-771">Ana bilgisayar `localhost` , geri döngü adreslerine bağlama için kullanılan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-771">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="79fe6-772">Açık IP adresi dışındaki tüm ana bilgisayar tüm genel IP adreslerine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-772">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="79fe6-773">`Host` Üstbilgiler doğrulanmadı.</span><span class="sxs-lookup"><span data-stu-id="79fe6-773">`Host` headers aren't validated.</span></span>

<span data-ttu-id="79fe6-774">Geçici bir çözüm olarak, ana bilgisayar filtreleme ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-774">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="79fe6-775">Ana bilgisayar filtreleme ara yazılımı, [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 veya 2,2) ' de yer alan [Microsoft. Aspnetcore. hostfiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-775">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="79fe6-776">Ara yazılım tarafından eklenir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ve şunları çağırır <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="79fe6-776">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="79fe6-777">Ana bilgisayar filtreleme ara yazılımı varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-777">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="79fe6-778">Ara yazılımı etkinleştirmek için, `AllowedHosts` appSettings *üzerindeappsettings.js*bir anahtar tanımlayın / *. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="79fe6-778">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="79fe6-779">Değer, bağlantı noktası numaraları olmayan ana bilgisayar adlarının noktalı virgülle ayrılmış listesidir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-779">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="79fe6-780">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="79fe6-780">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="79fe6-781">[Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer) da bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçenek içerir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-781">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="79fe6-782">İletilen üstbilgiler ara yazılımı ve ana bilgisayar filtreleme ara yazılımı, farklı senaryolar için benzer işlevlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-782">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="79fe6-783">`AllowedHosts`Iletilen üstbilgiler ara yazılımı ile, `Host` istekler ters bir ara sunucu veya yük dengeleyici ile iletilirken üst bilgi korunurken, bu işlem için uygun bir ayar vardır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-783">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="79fe6-784">`AllowedHosts`Ana bilgisayar filtreleme ara yazılımı ile ayarlama, Kestrel herkese açık bir uç sunucu olarak veya `Host` üst bilgi doğrudan iletildiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-784">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="79fe6-785">Iletilen üstbilgiler ara yazılımı hakkında daha fazla bilgi için bkz <xref:host-and-deploy/proxy-load-balancer> ..</span><span class="sxs-lookup"><span data-stu-id="79fe6-785">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="79fe6-786">Kestrel, ASP.NET Core için platformlar arası [Web sunucusudur](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="79fe6-786">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="79fe6-787">Kestrel, ASP.NET Core proje şablonlarında varsayılan olarak bulunan Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-787">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="79fe6-788">Kestrel aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="79fe6-788">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="79fe6-789">HTTPS</span><span class="sxs-lookup"><span data-stu-id="79fe6-789">HTTPS</span></span>
* <span data-ttu-id="79fe6-790">[WebSockets](https://github.com/aspnet/websockets) 'i etkinleştirmek için kullanılan donuk yükseltme</span><span class="sxs-lookup"><span data-stu-id="79fe6-790">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="79fe6-791">NGINX 'in arkasında yüksek performans için UNIX Yuvaları</span><span class="sxs-lookup"><span data-stu-id="79fe6-791">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="79fe6-792">Kestrel, .NET Core 'un desteklediği tüm platformlarda ve sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-792">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="79fe6-793">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="79fe6-793">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="79fe6-794">Ters ara sunucu ile Kestrel ne zaman kullanılır?</span><span class="sxs-lookup"><span data-stu-id="79fe6-794">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="79fe6-795">Kestrel, kendisi veya [Internet Information Services (IIS)](https://www.iis.net/), [NGINX](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi bir *ters ara sunucu*ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-795">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="79fe6-796">Ters proxy sunucusu, ağdan gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-796">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="79fe6-797">Sınır (Internet 'e yönelik) Web sunucusu olarak kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="79fe6-797">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel, ters proxy sunucusu olmadan doğrudan Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="79fe6-799">Ters Proxy yapılandırmasında kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="79fe6-799">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel, IIS, NGINX veya Apache gibi bir ters ara sunucu üzerinden Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="79fe6-801">İki yapılandırma de, ters ara sunucu sunucusuyla veya olmadan, desteklenen bir barındırma yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="79fe6-801">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="79fe6-802">Ters proxy sunucusu olmayan bir uç sunucu olarak kullanılan Kestrel, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="79fe6-802">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="79fe6-803">Kestrel bir bağlantı noktasını dinlemek üzere yapılandırıldığında, Kestrel isteklerin üst bilgilerinden bağımsız olarak bu bağlantı noktası için tüm trafiği işler `Host` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-803">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="79fe6-804">Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel 'e iletme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-804">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="79fe6-805">Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucu kullanılması iyi bir seçim olabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-805">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="79fe6-806">Ters proxy:</span><span class="sxs-lookup"><span data-stu-id="79fe6-806">A reverse proxy:</span></span>

* <span data-ttu-id="79fe6-807">, Barındırdığı uygulamaların açığa çıkarılan genel yüzey alanını sınırlayabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-807">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="79fe6-808">Ek bir yapılandırma ve savunma katmanı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-808">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="79fe6-809">, Mevcut altyapıyla daha iyi tümleşebilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-809">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="79fe6-810">Yük dengelemeyi ve güvenli iletişim (HTTPS) yapılandırmasını kolaylaştırın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-810">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="79fe6-811">Yalnızca ters proxy sunucusu bir X. 509.440 sertifikası gerektirir ve bu sunucu, düz HTTP kullanarak, iç ağdaki uygulamanın sunucularıyla iletişim kurabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-811">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="79fe6-812">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-812">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="79fe6-813">ASP.NET Core uygulamalarında Kestrel kullanma</span><span class="sxs-lookup"><span data-stu-id="79fe6-813">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="79fe6-814">Microsoft. [AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) paketi [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-814">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="79fe6-815">ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-815">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="79fe6-816">*Program.cs*' de, şablon kodu çağırır <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ve bu da <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> arka planda çağrı yapılır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-816">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="79fe6-817">Çağrıldıktan sonra ek yapılandırma sağlamak için `CreateDefaultBuilder` şunu çağırın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="79fe6-817">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="79fe6-818">Ana bilgisayarı hakkında daha fazla bilgi için `CreateDefaultBuilder` , uygulamasının *konak ayarlama* bölümüne bakın <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="79fe6-818">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="79fe6-819">Kestrel seçenekleri</span><span class="sxs-lookup"><span data-stu-id="79fe6-819">Kestrel options</span></span>

<span data-ttu-id="79fe6-820">Kestrel Web sunucusu, Internet 'e yönelik dağıtımlarda özellikle yararlı olan kısıtlama yapılandırma seçeneklerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-820">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="79fe6-821">Sınıfının özelliğinde kısıtlamaları ayarlayın <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> .</span><span class="sxs-lookup"><span data-stu-id="79fe6-821">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="79fe6-822">`Limits`Özelliği, sınıfının bir örneğini barındırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> .</span><span class="sxs-lookup"><span data-stu-id="79fe6-822">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="79fe6-823">Aşağıdaki örnekler <xref:Microsoft.AspNetCore.Server.Kestrel.Core> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="79fe6-823">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="79fe6-824">Aşağıdaki örneklerde C# kodunda yapılandırılan Kestrel seçenekleri de bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-824">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="79fe6-825">Örneğin, dosya yapılandırma sağlayıcısı Kestrel yapılandırmasını bir *appsettings.js* veya appSettings 'ten yükleyebilir *. { Environment}. JSON* dosyası:</span><span class="sxs-lookup"><span data-stu-id="79fe6-825">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="79fe6-826">Aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="79fe6-826">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="79fe6-827">Kestrel 'i yapılandırma `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="79fe6-827">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="79fe6-828">Sınıfına bir örneği `IConfiguration` ekleyin `Startup` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-828">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="79fe6-829">Aşağıdaki örnek, eklenen yapılandırmanın özelliğe atandığını varsayar `Configuration` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-829">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="79fe6-830">İçinde `Startup.ConfigureServices` , `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="79fe6-830">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="79fe6-831">Ana bilgisayarı oluştururken Kestrel yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="79fe6-831">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="79fe6-832">*Program.cs*' de, `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="79fe6-832">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="79fe6-833">Yukarıdaki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-833">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="79fe6-834">Etkin tut zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="79fe6-834">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="79fe6-835">[Etkin tutma zaman aşımını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="79fe6-835">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="79fe6-836">Varsayılan olarak 2 dakikadır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-836">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="79fe6-837">İstemci bağlantıları üst sınırı</span><span class="sxs-lookup"><span data-stu-id="79fe6-837">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="79fe6-838">En fazla eş zamanlı açık TCP bağlantısı sayısı tüm uygulama için aşağıdaki kodla ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-838">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="79fe6-839">HTTP veya HTTPS 'den başka bir protokole (örneğin, bir WebSockets isteğinde) yükseltilen bağlantılara yönelik ayrı bir sınır vardır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-839">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="79fe6-840">Bir bağlantı yükseltildikten sonra, bu sınıra göre sayılmaz `MaxConcurrentConnections` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-840">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="79fe6-841">En fazla bağlantı sayısı, varsayılan olarak sınırsız (null).</span><span class="sxs-lookup"><span data-stu-id="79fe6-841">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="79fe6-842">En büyük istek gövdesi boyutu</span><span class="sxs-lookup"><span data-stu-id="79fe6-842">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="79fe6-843">Varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu değer yaklaşık 28,6 MB 'tır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-843">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="79fe6-844">ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yaklaşım, <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="79fe6-844">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="79fe6-845">Her istekte uygulama için kısıtlamanın nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-845">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="79fe6-846">Ara yazılım içindeki belirli bir istek üzerindeki ayarı geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="79fe6-846">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="79fe6-847">Uygulama isteği okumaya başladıktan sonra bir istek üzerindeki sınırı yapılandırırsa, bir özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-847">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="79fe6-848">`IsReadOnly`Özelliğin salt okuma durumunda olup olmadığını belirten bir özellik vardır. Bu, `MaxRequestBodySize` sınırı yapılandırmanın çok geç olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-848">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="79fe6-849">Bir uygulama [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)arkasında [çalıştırıldığında](xref:host-and-deploy/iis/index#out-of-process-hosting-model) , IIS sınırı zaten ayarladığı için Kestrel 'nin istek gövdesi boyut sınırı devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-849">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="79fe6-850">En az istek gövdesi veri hızı</span><span class="sxs-lookup"><span data-stu-id="79fe6-850">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="79fe6-851">Kestrel bayt/saniye cinsinden belirtilen fiyata ulaşan her saniye sonra denetler.</span><span class="sxs-lookup"><span data-stu-id="79fe6-851">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="79fe6-852">Oran en düşük değerin altına düşerse bağlantı zaman aşımına uğrar. Yetkisiz kullanım süresi, Kestrel 'in istemciye gönderme oranını en düşük süreye kadar artırabilme Bu süre boyunca fiyat denetlenmez.</span><span class="sxs-lookup"><span data-stu-id="79fe6-852">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="79fe6-853">Yetkisiz kullanım süresi, TCP yavaş başlatma nedeniyle başlangıçta verileri yavaş bir hızda gönderen bağlantıların bırakılmasını önlemeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-853">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="79fe6-854">Varsayılan en düşük oran, 5 saniyelik bir yetkisiz kullanım süresi ile 240 bayt/saniye olur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-854">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="79fe6-855">Yanıt için bir minimum oran da geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-855">A minimum rate also applies to the response.</span></span> <span data-ttu-id="79fe6-856">İstek sınırını ayarlamaya yönelik kod ve yanıt sınırı, `RequestBody` `Response` özellik ve arabirim adlarında olduğu gibi aynı olur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-856">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="79fe6-857">*Program.cs*içinde en düşük veri hızlarının nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-857">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="79fe6-858">İstek üst bilgileri zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="79fe6-858">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="79fe6-859">Sunucunun istek üst bilgilerini alması için harcadığı en uzun süreyi alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="79fe6-859">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="79fe6-860">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-860">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="79fe6-861">Zaman Uyumlu G/Ç</span><span class="sxs-lookup"><span data-stu-id="79fe6-861">Synchronous I/O</span></span>

<span data-ttu-id="79fe6-862"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> istek ve yanıt için zaman uyumlu g/ç 'ye izin verilip verilmediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="79fe6-862"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="79fe6-863">Varsayılan değer `true` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-863">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="79fe6-864">Çok sayıda engelleme zaman uyumlu g/ç işlemi, iş parçacığı havuzuna yol açabilir, bu da uygulamanın yanıt vermemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-864">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="79fe6-865">Yalnızca `AllowSynchronousIO` zaman uyumsuz g/ç desteklemeyen bir kitaplık kullanırken etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="79fe6-865">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="79fe6-866">Aşağıdaki örnek, zaman uyumlu g/ç 'yi devre dışı bırakır:</span><span class="sxs-lookup"><span data-stu-id="79fe6-866">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="79fe6-867">Diğer Kestrel seçenekleri ve limitleri hakkında daha fazla bilgi için bkz.:</span><span class="sxs-lookup"><span data-stu-id="79fe6-867">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="79fe6-868">Uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="79fe6-868">Endpoint configuration</span></span>

<span data-ttu-id="79fe6-869">Varsayılan olarak, ASP.NET Core bağlar:</span><span class="sxs-lookup"><span data-stu-id="79fe6-869">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="79fe6-870">`https://localhost:5001` (bir yerel geliştirme sertifikası varsa)</span><span class="sxs-lookup"><span data-stu-id="79fe6-870">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="79fe6-871">Kullanarak URL 'Leri belirtin:</span><span class="sxs-lookup"><span data-stu-id="79fe6-871">Specify URLs using the:</span></span>

* <span data-ttu-id="79fe6-872">`ASPNETCORE_URLS` ortam değişkeni.</span><span class="sxs-lookup"><span data-stu-id="79fe6-872">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="79fe6-873">`--urls` komut satırı bağımsız değişkeni.</span><span class="sxs-lookup"><span data-stu-id="79fe6-873">`--urls` command-line argument.</span></span>
* <span data-ttu-id="79fe6-874">`urls` Ana bilgisayar yapılandırma anahtarı.</span><span class="sxs-lookup"><span data-stu-id="79fe6-874">`urls` host configuration key.</span></span>
* <span data-ttu-id="79fe6-875">`UseUrls` genişletme yöntemi.</span><span class="sxs-lookup"><span data-stu-id="79fe6-875">`UseUrls` extension method.</span></span>

<span data-ttu-id="79fe6-876">Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS).</span><span class="sxs-lookup"><span data-stu-id="79fe6-876">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="79fe6-877">Değeri noktalı virgülle ayrılmış bir liste olarak yapılandırın (örneğin, `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="79fe6-877">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="79fe6-878">Bu yaklaşımlar hakkında daha fazla bilgi için [sunucu URL 'leri](xref:fundamentals/host/web-host#server-urls) ve [geçersiz kılma yapılandırması](xref:fundamentals/host/web-host#override-configuration)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-878">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="79fe6-879">Geliştirme sertifikası oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="79fe6-879">A development certificate is created:</span></span>

* <span data-ttu-id="79fe6-880">[.NET Core SDK](/dotnet/core/sdk) yüklendiği zaman.</span><span class="sxs-lookup"><span data-stu-id="79fe6-880">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="79fe6-881">[Geliştirme-CERT aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-881">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="79fe6-882">Bazı tarayıcılarda yerel geliştirme sertifikasına güvenmek için açık izin verilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-882">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="79fe6-883">Proje şablonları, uygulamaları HTTPS üzerinde varsayılan olarak çalışacak şekilde yapılandırır ve [https yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-883">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="79fe6-884"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> URL öneklerini ve bağlantı noktalarını Kestrel için yapılandırmak üzere üzerinde çağrı veya Yöntemler.</span><span class="sxs-lookup"><span data-stu-id="79fe6-884">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="79fe6-885">`UseUrls`, `--urls` komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı ve `ASPNETCORE_URLS` ortam değişkeni de çalışır, ancak bu bölümün ilerleyen kısımlarında belirtilen sınırlamalara sahiptir (https uç noktası yapılandırması için varsayılan sertifika kullanılabilir olmalıdır).</span><span class="sxs-lookup"><span data-stu-id="79fe6-885">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="79fe6-886">`KestrelServerOptions` yapılandırmada</span><span class="sxs-lookup"><span data-stu-id="79fe6-886">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="79fe6-887">ConfigureEndpointDefaults Varsayılanları (eylem \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="79fe6-887">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="79fe6-888">`Action`Belirtilen her bir uç nokta için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-888">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="79fe6-889">`ConfigureEndpointDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-889">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="79fe6-890">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="79fe6-890">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="79fe6-891">ConfigureHttpsDefaults (eylem \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="79fe6-891">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="79fe6-892">`Action`Her HTTPS uç noktası için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-892">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="79fe6-893">`ConfigureHttpsDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-893">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="79fe6-894">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="79fe6-894">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="79fe6-895">Yapılandırma (Iconation)</span><span class="sxs-lookup"><span data-stu-id="79fe6-895">Configure(IConfiguration)</span></span>

<span data-ttu-id="79fe6-896">Bir as girişi alan Kestrel ayarlamak için bir yapılandırma yükleyicisi oluşturur <xref:Microsoft.Extensions.Configuration.IConfiguration> .</span><span class="sxs-lookup"><span data-stu-id="79fe6-896">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="79fe6-897">Yapılandırma, Kestrel için yapılandırma bölümünün kapsamına alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-897">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="79fe6-898">ListenOptions. UseHttps</span><span class="sxs-lookup"><span data-stu-id="79fe6-898">ListenOptions.UseHttps</span></span>

<span data-ttu-id="79fe6-899">Kestrel 'i HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-899">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="79fe6-900">`ListenOptions.UseHttps` uzantılardan</span><span class="sxs-lookup"><span data-stu-id="79fe6-900">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="79fe6-901">`UseHttps`: Kestrel 'i varsayılan sertifikayla HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-901">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="79fe6-902">Varsayılan sertifika yapılandırılmamışsa bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-902">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="79fe6-903">`ListenOptions.UseHttps` parametrelere</span><span class="sxs-lookup"><span data-stu-id="79fe6-903">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="79fe6-904">`filename` , uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yolu ve dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-904">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="79fe6-905">`password` X. 509.440 sertifika verilerine erişmek için parola gereklidir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-905">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="79fe6-906">`configureOptions` , öğesini `Action` yapılandırmak için kullanılır `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-906">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="79fe6-907">Döndürür `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-907">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="79fe6-908">`storeName` , sertifikanın yükleneceği sertifika deposudur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-908">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="79fe6-909">`subject` , sertifika için konu adıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-909">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="79fe6-910">`allowInvalid` geçersiz sertifikaların, otomatik olarak imzalanan sertifikalar gibi göz önünde bulundurulmayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-910">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="79fe6-911">`location` , sertifikanın yükleneceği mağaza konumudur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-911">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="79fe6-912">`serverCertificate` , X. 509.440 sertifikasıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-912">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="79fe6-913">Üretimde HTTPS 'nin açıkça yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-913">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="79fe6-914">En azından, varsayılan bir sertifika sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-914">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="79fe6-915">Daha sonra açıklanan desteklenen yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="79fe6-915">Supported configurations described next:</span></span>

* <span data-ttu-id="79fe6-916">Yapılandırma yok</span><span class="sxs-lookup"><span data-stu-id="79fe6-916">No configuration</span></span>
* <span data-ttu-id="79fe6-917">Varsayılan sertifikayı yapılandırmadan Değiştir</span><span class="sxs-lookup"><span data-stu-id="79fe6-917">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="79fe6-918">Koddaki varsayılanları değiştirme</span><span class="sxs-lookup"><span data-stu-id="79fe6-918">Change the defaults in code</span></span>

<span data-ttu-id="79fe6-919">*Yapılandırma yok*</span><span class="sxs-lookup"><span data-stu-id="79fe6-919">*No configuration*</span></span>

<span data-ttu-id="79fe6-920">Kestrel tarihinde dinler `http://localhost:5000` ve `https://localhost:5001` (varsayılan bir sertifika varsa).</span><span class="sxs-lookup"><span data-stu-id="79fe6-920">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="79fe6-921">*Varsayılan sertifikayı yapılandırmadan Değiştir*</span><span class="sxs-lookup"><span data-stu-id="79fe6-921">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="79fe6-922">`CreateDefaultBuilder``Configure(context.Configuration.GetSection("Kestrel"))`Kestrel yapılandırmasını yüklemek için varsayılan olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-922">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="79fe6-923">Varsayılan bir HTTPS uygulama ayarları yapılandırma şeması Kestrel için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-923">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="79fe6-924">Disk üzerindeki bir dosyadan ya da bir sertifika deposundan kullanılacak URL 'Ler ve Sertifikalar dahil olmak üzere birden çok uç nokta yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-924">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="79fe6-925">Aşağıdaki *appsettings.js* aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-925">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="79fe6-926">Geçersiz sertifikaların kullanılmasına izin vermek için **Allowwınvalid** `true` ' i ayarlayın (örneğin, otomatik olarak imzalanan sertifikalar).</span><span class="sxs-lookup"><span data-stu-id="79fe6-926">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="79fe6-927">Bir sertifika belirtmeyen herhangi bir HTTPS uç noktası (aşağıdaki örnekte bulunan**httpsdefaultcert** ), **Sertifikalar** > **varsayılan** veya geliştirme sertifikası altında tanımlanan sertifikaya geri döner.</span><span class="sxs-lookup"><span data-stu-id="79fe6-927">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="79fe6-928">Herhangi bir sertifika düğümü için **yol** ve **parola** kullanmanın alternatifi sertifika deposu alanlarını kullanarak sertifikayı belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-928">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="79fe6-929">Örneğin, **sertifika**  >  **varsayılan** sertifikası şu şekilde belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-929">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="79fe6-930">Şema notları:</span><span class="sxs-lookup"><span data-stu-id="79fe6-930">Schema notes:</span></span>

* <span data-ttu-id="79fe6-931">Uç nokta adları büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-931">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="79fe6-932">Örneğin, `HTTPS` ve `Https` geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-932">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="79fe6-933">`Url`Her uç nokta için parametresi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-933">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="79fe6-934">Bu parametrenin biçimi, en üst düzey `Urls` yapılandırma parametresiyle aynıdır, ancak tek bir değerle sınırlı olur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-934">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="79fe6-935">Bu uç noktalar, üst düzey yapılandırmada tanımlananlar yerine `Urls` bunlara ekleme yerine bunların yerini alır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-935">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="79fe6-936">Kullanılarak kodda tanımlanan uç noktalar `Listen` yapılandırma bölümünde tanımlanan uç noktalar ile birikimlidir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-936">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="79fe6-937">Bu `Certificate` bölüm isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-937">The `Certificate` section is optional.</span></span> <span data-ttu-id="79fe6-938">`Certificate`Bölüm belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-938">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="79fe6-939">Kullanılabilir varsayılan değer yoksa, sunucu bir özel durum oluşturur ve başlayamaz.</span><span class="sxs-lookup"><span data-stu-id="79fe6-939">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="79fe6-940">Bu `Certificate` bölüm hem **yol** &ndash; **parolasını** hem de **Konu** &ndash; **deposu** sertifikalarını destekler.</span><span class="sxs-lookup"><span data-stu-id="79fe6-940">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="79fe6-941">Herhangi bir sayıda uç nokta, bağlantı noktası çakışmalarına neden olmadıkları sürece bu şekilde tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-941">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="79fe6-942">`options.Configure(context.Configuration.GetSection("{SECTION}"))` yapılandırılmış bir `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` uç noktanın ayarlarını tamamlamak için kullanılabilecek bir yöntemi olan bir döndürür:</span><span class="sxs-lookup"><span data-stu-id="79fe6-942">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="79fe6-943">`KestrelServerOptions.ConfigurationLoader` , tarafından sağlana gibi var olan yükleyicisindeki yinelemeye devam etmek için doğrudan erişilebilir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="79fe6-943">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="79fe6-944">Her uç noktanın yapılandırma bölümü, `Endpoint` özel ayarların okunabilmesi için yöntemindeki seçeneklerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-944">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="79fe6-945">Birden çok yapılandırma, `options.Configure(context.Configuration.GetSection("{SECTION}"))` başka bir bölümle yeniden çağırarak yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-945">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="79fe6-946">Önceki örneklerde açıkça çağrılmadığı takdirde, yalnızca son yapılandırma kullanılır `Load` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-946">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="79fe6-947">Metapackage, `Load` varsayılan yapılandırma bölümünün değiştirilmesini sağlayacak şekilde çağırmıyor.</span><span class="sxs-lookup"><span data-stu-id="79fe6-947">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="79fe6-948">`KestrelConfigurationLoader``Listen`API ailesini `KestrelServerOptions` `Endpoint` aşırı yükleme olarak yansıtır, bu nedenle kod ve yapılandırma uç noktaları aynı yerde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-948">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="79fe6-949">Bu aşırı yüklemeler adları kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları kullanır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-949">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="79fe6-950">*Koddaki varsayılanları değiştirme*</span><span class="sxs-lookup"><span data-stu-id="79fe6-950">*Change the defaults in code*</span></span>

<span data-ttu-id="79fe6-951">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` `ListenOptions` `HttpsConnectionAdapterOptions` , önceki senaryoda belirtilen varsayılan sertifikayı geçersiz kılma dahil, ve için varsayılan ayarları değiştirmek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-951">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="79fe6-952">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-952">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="79fe6-953">*SNı için Kestrel desteği*</span><span class="sxs-lookup"><span data-stu-id="79fe6-953">*Kestrel support for SNI*</span></span>

<span data-ttu-id="79fe6-954">[Sunucu adı belirtme (SNı)](https://tools.ietf.org/html/rfc6066#section-3) , aynı IP adresi ve bağlantı noktası üzerinde birden fazla etki alanını barındırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-954">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="79fe6-955">SNı 'nin çalışması için, istemci, TLS el sıkışması sırasında güvenli oturum ana bilgisayar adını, sunucunun doğru sertifikayı sağlayabilmesi için gönderir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-955">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="79fe6-956">İstemci, TLS anlaşmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için bulunan sertifikayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-956">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="79fe6-957">Kestrel, geri çağırma aracılığıyla SNı destekler `ServerCertificateSelector` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-957">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="79fe6-958">Geri çağırma, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesini sağlamak için bağlantı başına bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-958">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="79fe6-959">SNı desteği şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-959">SNI support requires:</span></span>

* <span data-ttu-id="79fe6-960">Hedef Framework `netcoreapp2.1` veya sonraki sürümlerde çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="79fe6-960">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="79fe6-961">`net461`Veya sonraki sürümlerde, geri çağırma çağrılır, ancak `name` her zaman olur `null` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-961">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="79fe6-962">`name`Ayrıca, `null` ISTEMCI, TLS el sıkışmasının ana bilgisayar adı parametresini sağlamıyorsa de olur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-962">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="79fe6-963">Tüm Web siteleri aynı Kestrel örneğinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-963">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="79fe6-964">Kestrel, bir IP adresi ve bağlantı noktasının bir ters proxy olmadan birden çok örnek arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="79fe6-964">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="79fe6-965">Bağlantı günlüğü</span><span class="sxs-lookup"><span data-stu-id="79fe6-965">Connection logging</span></span>

<span data-ttu-id="79fe6-966"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>Bir bağlantıda bayt düzeyinde iletişim Için hata ayıklama düzeyi günlüklerini yayma çağrısı.</span><span class="sxs-lookup"><span data-stu-id="79fe6-966">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="79fe6-967">Bağlantı günlüğü, TLS şifreleme ve proxy 'nin arkasındaki gibi alt düzey iletişimde sorunları gidermeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-967">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="79fe6-968">`UseConnectionLogging`Daha önce yerleştirilmişse `UseHttps` , şifrelenmiş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-968">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="79fe6-969">`UseConnectionLogging`Öğesinden sonra yerleştirilmişse `UseHttps` , şifresi çözülmüş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-969">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="79fe6-970">TCP yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="79fe6-970">Bind to a TCP socket</span></span>

<span data-ttu-id="79fe6-971"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Yöntemi BIR TCP yuvasına bağlanır ve bir seçenek lambda X. 509.440 sertifika yapılandırmasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-971">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="79fe6-972">Örnek, HTTPS 'yi ile bir uç nokta için yapılandırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="79fe6-972">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="79fe6-973">Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak üzere aynı API 'yi kullanın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-973">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="79fe6-974">UNIX yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="79fe6-974">Bind to a Unix socket</span></span>

<span data-ttu-id="79fe6-975"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>Aşağıdaki örnekte gösterildiği gibi NGINX ile gelişmiş performans için ile birlikte bir UNIX yuvası dinleyin:</span><span class="sxs-lookup"><span data-stu-id="79fe6-975">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

* <span data-ttu-id="79fe6-976">NGINX confiuguration dosyasında, `server`  >  `location`  >  `proxy_pass` girdisini olarak ayarlayın `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-976">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="79fe6-977">`{KESTREL SOCKET}` , için belirtilen yuvanın adıdır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (örneğin, `kestrel-test.sock` Önceki örnekte).</span><span class="sxs-lookup"><span data-stu-id="79fe6-977">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="79fe6-978">Yuvanın NGINX tarafından yazılabilir olduğundan emin olun (örneğin, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="79fe6-978">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="79fe6-979">Bağlantı noktası 0</span><span class="sxs-lookup"><span data-stu-id="79fe6-979">Port 0</span></span>

<span data-ttu-id="79fe6-980">Bağlantı noktası numarası `0` belirtildiğinde, Kestrel dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-980">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="79fe6-981">Aşağıdaki örnek, çalışma zamanında Kestrel gerçekte hangi bağlantı noktasının gerçekten bağlandığını nasıl belirleyeceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-981">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="79fe6-982">Uygulama çalıştırıldığında konsol penceresi çıkışı, uygulamanın erişilebileceği dinamik bağlantı noktasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-982">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="79fe6-983">Sınırlamalar</span><span class="sxs-lookup"><span data-stu-id="79fe6-983">Limitations</span></span>

<span data-ttu-id="79fe6-984">Aşağıdaki yaklaşımlar ile uç noktaları yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="79fe6-984">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="79fe6-985">`--urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="79fe6-985">`--urls` command-line argument</span></span>
* <span data-ttu-id="79fe6-986">`urls` Ana bilgisayar yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="79fe6-986">`urls` host configuration key</span></span>
* <span data-ttu-id="79fe6-987">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="79fe6-987">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="79fe6-988">Bu yöntemler, kodun Kestrel dışındaki sunucularla çalışmasını sağlamak için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-988">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="79fe6-989">Ancak, aşağıdaki sınırlamalara dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="79fe6-989">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="79fe6-990">HTTPS uç noktası yapılandırmasında varsayılan bir sertifika sağlanmadığı sürece HTTPS bu yaklaşımlar ile kullanılamaz (örneğin, `KestrelServerOptions` Bu konunun önceki kısımlarında gösterildiği gibi yapılandırma veya yapılandırma dosyası kullanılıyor).</span><span class="sxs-lookup"><span data-stu-id="79fe6-990">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="79fe6-991">Hem hem de `Listen` `UseUrls` yaklaşımları aynı anda kullanıldığında, uç noktalar `Listen` `UseUrls` uç noktaları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="79fe6-991">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="79fe6-992">IIS uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="79fe6-992">IIS endpoint configuration</span></span>

<span data-ttu-id="79fe6-993">IIS kullanırken, IIS geçersiz kılma bağlamaları için URL bağlamaları veya ya da tarafından ayarlanır `Listen` `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-993">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="79fe6-994">Daha fazla bilgi için [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-994">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="79fe6-995">Aktarım yapılandırması</span><span class="sxs-lookup"><span data-stu-id="79fe6-995">Transport configuration</span></span>

<span data-ttu-id="79fe6-996">ASP.NET Core 2,1 sürümü ile Kestrel 'in varsayılan taşıması artık libuv ' d i temel değildir ancak bunun yerine yönetilen yuvaları temel alır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-996">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="79fe6-997">Bu, çağrıyı yapan ve aşağıdaki paketlerden birine bağlı olan ASP.NET Core 2,0 2,1 uygulamalarının önemli bir değişikliği olur <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="79fe6-997">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="79fe6-998">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (doğrudan paket başvurusu)</span><span class="sxs-lookup"><span data-stu-id="79fe6-998">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="79fe6-999">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="79fe6-999">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="79fe6-1000">Libuv kullanımını gerektiren projeler için:</span><span class="sxs-lookup"><span data-stu-id="79fe6-1000">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="79fe6-1001">Uygulamanın proje dosyasına [Microsoft. AspNetCore. Server. Kestrel. Transport. libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) paketi için bir bağımlılık ekleyin:</span><span class="sxs-lookup"><span data-stu-id="79fe6-1001">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="79fe6-1002">Çağrı <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="79fe6-1002">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="79fe6-1003">URL önekleri</span><span class="sxs-lookup"><span data-stu-id="79fe6-1003">URL prefixes</span></span>

<span data-ttu-id="79fe6-1004">`UseUrls`, `--urls` Komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı veya `ASPNETCORE_URLS` ortam değişkeni kullanılırken, URL önekleri aşağıdaki biçimlerden birinde olabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1004">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="79fe6-1005">Yalnızca HTTP URL ön ekleri geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1005">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="79fe6-1006">Kestrel, kullanılarak URL bağlamaları yapılandırılırken HTTPS 'YI desteklemez `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-1006">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="79fe6-1007">Bağlantı noktası numarası olan IPv4 adresi</span><span class="sxs-lookup"><span data-stu-id="79fe6-1007">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="79fe6-1008">`0.0.0.0` Tüm IPv4 adreslerine bağlanan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1008">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="79fe6-1009">Bağlantı noktası numarasına sahip IPv6 adresi</span><span class="sxs-lookup"><span data-stu-id="79fe6-1009">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="79fe6-1010">`[::]` , IPv4 'un IPv6 eşdeğeridir `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-1010">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="79fe6-1011">Bağlantı noktası numarası olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="79fe6-1011">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="79fe6-1012">, Ve ana bilgisayar adları `*` `+` özel değildir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1012">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="79fe6-1013">Geçerli bir IP adresi olarak tanınmayan veya `localhost` tüm IPv4 ve IPv6 IP 'lerine bağlanan her şey.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1013">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="79fe6-1014">Farklı ana bilgisayar adlarını aynı bağlantı noktasında farklı ASP.NET Core uygulamalarına bağlamak için, [HTTP.sys](xref:fundamentals/servers/httpsys) veya IIS, NGINX veya Apache gibi bir ters proxy sunucusunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1014">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="79fe6-1015">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1015">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="79fe6-1016">Port numarası `localhost` veya bağlantı noktası numarası ile geri döngü IP 'si olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="79fe6-1016">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="79fe6-1017">Belirtildiğinde `localhost` , Kestrel hem IPv4 hem de IPv6 geri döngü arabirimlerini bağlamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1017">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="79fe6-1018">İstenen bağlantı noktası, herhangi bir geri döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1018">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="79fe6-1019">Herhangi bir geri döngü arabirimi başka bir nedenle kullanılamıyorsa (genellikle IPv6 desteklenmediği için), Kestrel bir uyarı kaydeder.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1019">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="79fe6-1020">Konak filtreleme</span><span class="sxs-lookup"><span data-stu-id="79fe6-1020">Host filtering</span></span>

<span data-ttu-id="79fe6-1021">Kestrel gibi önekleri temel alarak yapılandırmayı desteklese `http://example.com:5000` de, Kestrel büyük ölçüde ana bilgisayar adını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1021">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="79fe6-1022">Ana bilgisayar `localhost` , geri döngü adreslerine bağlama için kullanılan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1022">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="79fe6-1023">Açık IP adresi dışındaki tüm ana bilgisayar tüm genel IP adreslerine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1023">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="79fe6-1024">`Host` Üstbilgiler doğrulanmadı.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1024">`Host` headers aren't validated.</span></span>

<span data-ttu-id="79fe6-1025">Geçici bir çözüm olarak, ana bilgisayar filtreleme ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1025">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="79fe6-1026">Ana bilgisayar filtreleme ara yazılımı, [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 veya 2,2) ' de yer alan [Microsoft. Aspnetcore. hostfiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1026">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="79fe6-1027">Ara yazılım tarafından eklenir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ve şunları çağırır <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="79fe6-1027">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="79fe6-1028">Ana bilgisayar filtreleme ara yazılımı varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1028">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="79fe6-1029">Ara yazılımı etkinleştirmek için, `AllowedHosts` appSettings *üzerindeappsettings.js*bir anahtar tanımlayın / *. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1029">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="79fe6-1030">Değer, bağlantı noktası numaraları olmayan ana bilgisayar adlarının noktalı virgülle ayrılmış listesidir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-1030">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="79fe6-1031">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="79fe6-1031">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="79fe6-1032">[Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer) da bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçenek içerir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1032">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="79fe6-1033">İletilen üstbilgiler ara yazılımı ve ana bilgisayar filtreleme ara yazılımı, farklı senaryolar için benzer işlevlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1033">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="79fe6-1034">`AllowedHosts`Iletilen üstbilgiler ara yazılımı ile, `Host` istekler ters bir ara sunucu veya yük dengeleyici ile iletilirken üst bilgi korunurken, bu işlem için uygun bir ayar vardır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1034">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="79fe6-1035">`AllowedHosts`Ana bilgisayar filtreleme ara yazılımı ile ayarlama, Kestrel herkese açık bir uç sunucu olarak veya `Host` üst bilgi doğrudan iletildiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1035">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="79fe6-1036">Iletilen üstbilgiler ara yazılımı hakkında daha fazla bilgi için bkz <xref:host-and-deploy/proxy-load-balancer> ..</span><span class="sxs-lookup"><span data-stu-id="79fe6-1036">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="79fe6-1037">HTTP/1.1 istek boşaltma</span><span class="sxs-lookup"><span data-stu-id="79fe6-1037">HTTP/1.1 request draining</span></span>

<span data-ttu-id="79fe6-1038">HTTP bağlantılarının açılması zaman alabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1038">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="79fe6-1039">HTTPS için de kaynak kullanımı yoğun bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1039">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="79fe6-1040">Bu nedenle Kestrel, HTTP/1.1 protokolü başına bağlantıları yeniden kullanmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1040">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="79fe6-1041">Bağlantının yeniden kullanılmasına izin vermek için bir istek gövdesi tam olarak tüketilmelidir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1041">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="79fe6-1042">Uygulama, `POST` sunucunun yeniden yönlendirme veya 404 yanıtı döndürdüğü bir istek gibi her zaman istek gövdesini tüketmez.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1042">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="79fe6-1043">`POST`Yeniden yönlendirme durumunda:</span><span class="sxs-lookup"><span data-stu-id="79fe6-1043">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="79fe6-1044">İstemci, verilerin bir bölümünü zaten göndermiş olabilir `POST` .</span><span class="sxs-lookup"><span data-stu-id="79fe6-1044">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="79fe6-1045">Sunucu 301 yanıtını yazar.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1045">The server writes the 301 response.</span></span>
* <span data-ttu-id="79fe6-1046">`POST`Önceki istek gövdesinden gelen veriler tam olarak okunana kadar bağlantı yeni bir istek için kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1046">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="79fe6-1047">Kestrel, istek gövdesini boşaltmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1047">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="79fe6-1048">İstek gövdesini boşaltma işlemi işlemeden verileri okuma ve atma anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1048">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="79fe6-1049">Boşaltma işlemi bağlantının yeniden kullanılmasına izin verme ve kalan verilerin boşaltıma süresi arasında bir ilerleme gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="79fe6-1049">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="79fe6-1050">Boşaltma, yapılandırılabilir olmayan beş saniyelik bir zaman aşımına sahip olur.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1050">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="79fe6-1051">Veya üstbilgisi tarafından belirtilen tüm veriler `Content-Length` `Transfer-Encoding` zaman aşımından önce okunmadıysa bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1051">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="79fe6-1052">Bazen, yanıtı yazmadan önce veya sonra isteği hemen sonlandırmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1052">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="79fe6-1053">Örneğin, istemciler sınırlı verilerin büyük bir düzeyi olabilir, bu nedenle karşıya yüklenen verileri sınırlamak bir öncelik olabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1053">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="79fe6-1054">Bu tür durumlarda bir isteği sonlandırmak için bir denetleyici, sayfa veya ara yazılım aracılığıyla [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) ' ı çağırın Razor .</span><span class="sxs-lookup"><span data-stu-id="79fe6-1054">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="79fe6-1055">Çağırmanın uyarıları vardır `Abort` :</span><span class="sxs-lookup"><span data-stu-id="79fe6-1055">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="79fe6-1056">Yeni bağlantı oluşturma yavaş ve pahalı olabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1056">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="79fe6-1057">İstemcinin bağlantı kapanmadan önce yanıtı okuduğunuzdan emin olmaz.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1057">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="79fe6-1058">Çağırma `Abort` , yaygın hatalara değil önemli hata durumları için nadir ve ayrılmış olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1058">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="79fe6-1059">Yalnızca `Abort` belirli bir sorunun çözülmesi gerektiğinde çağırın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1059">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="79fe6-1060">Örneğin, `Abort` kötü amaçlı istemciler veri almaya çalışıyorsa `POST` veya istemci kodunda büyük veya çok sayıda isteğe neden olan bir hata olduğunda çağırın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1060">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="79fe6-1061">`Abort`HTTP 404 (bulunamadı) gibi yaygın hata durumları için çağrı yapmayın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1061">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="79fe6-1062">Çağrılmadan önce [HttpResponse. tamamlana eşitlemesini](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) çağırmak `Abort` Sunucunun yanıtı yazmayı tamamlamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1062">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="79fe6-1063">Ancak, istemci davranışı tahmin edilebilir değildir ve bağlantı durdurulmadan önce yanıtı okuyamayabilir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1063">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="79fe6-1064">Bu işlem HTTP/2 için farklıdır çünkü protokol, bağlantıyı kapatmadan bağımsız istek akışlarını iptal etme işlemini destekler.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1064">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="79fe6-1065">Beş saniyelik boşaltma zaman aşımı uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1065">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="79fe6-1066">Yanıt tamamlandıktan sonra herhangi bir okunmamış istek gövdesi verisi varsa, sunucu bir HTTP/2 RST çerçevesi gönderir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1066">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="79fe6-1067">Ek istek gövdesi veri çerçeveleri yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1067">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="79fe6-1068">Mümkünse, istemcilerin [Beklenen: 100-Continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) istek üst bilgisini kullanabilmesi ve istek gövdesini göndermeden önce sunucunun yanıt vermesini beklemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1068">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="79fe6-1069">Bu, istemciye gereksiz verileri göndermeden önce yanıtı İnceleme ve iptal etme olanağı sunar.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1069">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="79fe6-1070">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="79fe6-1070">Additional resources</span></span>

* <span data-ttu-id="79fe6-1071">Linux üzerinde UNIX yuvaları kullanırken, yuva uygulama kapatılırken otomatik olarak silinmez.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1071">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="79fe6-1072">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/aspnetcore/issues/14134)bakın.</span><span class="sxs-lookup"><span data-stu-id="79fe6-1072">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="79fe6-1073">RFC 7230: Ileti sözdizimi ve yönlendirme (Bölüm 5,4: Ana bilgisayar)</span><span class="sxs-lookup"><span data-stu-id="79fe6-1073">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
