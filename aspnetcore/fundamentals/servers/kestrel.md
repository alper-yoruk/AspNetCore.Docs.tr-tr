---
title: ASP.NET Core Web sunucusu uygulamasını Kestrel
author: rick-anderson
description: ASP.NET Core platformlar arası Web sunucusu olan Kestrel hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
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
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 5c9e1717ad603687343f015826a113e6945e4a41
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854619"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="70337-103">ASP.NET Core Web sunucusu uygulamasını Kestrel</span><span class="sxs-lookup"><span data-stu-id="70337-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="70337-104">[Tom Dykstra](https://github.com/tdykstra), [Chris](https://github.com/Tratcher), ve [Stephen halter](https://twitter.com/halter73) tarafından</span><span class="sxs-lookup"><span data-stu-id="70337-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="70337-105">Kestrel, ASP.NET Core için platformlar arası [Web sunucusudur](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="70337-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="70337-106">Kestrel, ASP.NET Core proje şablonlarında varsayılan olarak bulunan Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="70337-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="70337-107">Kestrel aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="70337-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="70337-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="70337-108">HTTPS</span></span>
* <span data-ttu-id="70337-109">[WebSockets](https://github.com/aspnet/websockets) 'i etkinleştirmek için kullanılan donuk yükseltme</span><span class="sxs-lookup"><span data-stu-id="70337-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="70337-110">NGINX 'in arkasında yüksek performans için UNIX Yuvaları</span><span class="sxs-lookup"><span data-stu-id="70337-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="70337-111">HTTP/2 (macOS hariç &dagger; )</span><span class="sxs-lookup"><span data-stu-id="70337-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="70337-112">&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="70337-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="70337-113">Kestrel, .NET Core 'un desteklediği tüm platformlarda ve sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="70337-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="70337-114">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="70337-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="70337-115">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="70337-115">HTTP/2 support</span></span>

<span data-ttu-id="70337-116">Aşağıdaki temel gereksinimler karşılanıyorsa, [http/2](https://httpwg.org/specs/rfc7540.html) ASP.NET Core uygulamalar için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="70337-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="70337-117">İşletim sistemi&dagger;</span><span class="sxs-lookup"><span data-stu-id="70337-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="70337-118">Windows Server 2016/Windows 10 veya üzeri&Dagger;</span><span class="sxs-lookup"><span data-stu-id="70337-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="70337-119">OpenSSL 1.0.2 veya üzerini içeren Linux (örneğin, Ubuntu 16,04 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="70337-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="70337-120">Hedef Framework: .NET Core 2,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="70337-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="70337-121">[Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı</span><span class="sxs-lookup"><span data-stu-id="70337-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="70337-122">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="70337-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="70337-123">&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="70337-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="70337-124">&Dagger;Kestrel, Windows Server 2012 R2 ve Windows 8.1 'de HTTP/2 için sınırlı destek içerir.</span><span class="sxs-lookup"><span data-stu-id="70337-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="70337-125">Bu işletim sistemlerinde kullanılabilir olan desteklenen TLS şifre paketlerinin listesi sınırlı olduğundan destek sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="70337-126">TLS bağlantılarının güvenliğini sağlamak için Eliptik Eğri dijital Imza algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="70337-127">Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="70337-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="70337-128">HTTP/2 varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="70337-129">Yapılandırma hakkında daha fazla bilgi için [Kestrel Options](#kestrel-options) ve [Listenoptions. Protocols](#listenoptionsprotocols) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="70337-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="70337-130">Ters ara sunucu ile Kestrel ne zaman kullanılır?</span><span class="sxs-lookup"><span data-stu-id="70337-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="70337-131">Kestrel, kendisi veya [Internet Information Services (IIS)](https://www.iis.net/), [NGINX](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi bir *ters ara sunucu* ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="70337-132">Ters proxy sunucusu, ağdan gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="70337-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="70337-133">Sınır (Internet 'e yönelik) Web sunucusu olarak kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="70337-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel, ters proxy sunucusu olmadan doğrudan Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="70337-135">Ters Proxy yapılandırmasında kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="70337-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel, IIS, NGINX veya Apache gibi bir ters ara sunucu üzerinden Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="70337-137">İki yapılandırma de, ters ara sunucu sunucusuyla veya olmadan, desteklenen bir barındırma yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="70337-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="70337-138">Ters proxy sunucusu olmayan bir uç sunucu olarak kullanılan Kestrel, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="70337-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="70337-139">Kestrel bir bağlantı noktasını dinlemek üzere yapılandırıldığında, Kestrel isteklerin üst bilgilerinden bağımsız olarak bu bağlantı noktası için tüm trafiği işler `Host` .</span><span class="sxs-lookup"><span data-stu-id="70337-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="70337-140">Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel 'e iletme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="70337-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="70337-141">Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucu kullanılması iyi bir seçim olabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="70337-142">Ters proxy:</span><span class="sxs-lookup"><span data-stu-id="70337-142">A reverse proxy:</span></span>

* <span data-ttu-id="70337-143">, Barındırdığı uygulamaların açığa çıkarılan genel yüzey alanını sınırlayabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="70337-144">Ek bir yapılandırma ve savunma katmanı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="70337-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="70337-145">, Mevcut altyapıyla daha iyi tümleşebilir.</span><span class="sxs-lookup"><span data-stu-id="70337-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="70337-146">Yük dengelemeyi ve güvenli iletişim (HTTPS) yapılandırmasını kolaylaştırın.</span><span class="sxs-lookup"><span data-stu-id="70337-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="70337-147">Yalnızca ters proxy sunucusu bir X. 509.440 sertifikası gerektirir ve bu sunucu, düz HTTP kullanarak, iç ağdaki uygulamanın sunucularıyla iletişim kurabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="70337-148">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="70337-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="70337-149">ASP.NET Core uygulamalarda Kestrel</span><span class="sxs-lookup"><span data-stu-id="70337-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="70337-150">ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır.</span><span class="sxs-lookup"><span data-stu-id="70337-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="70337-151">*Program.cs* içinde Yöntem şunu <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="70337-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="70337-152">Konak oluşturma hakkında daha fazla bilgi için, uygulamasının konak ve *Varsayılan Oluşturucu ayarlarını* *ayarlama* bölümüne bakın <xref:fundamentals/host/generic-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="70337-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="70337-153">Çağrıldıktan sonra ek yapılandırma sağlamak için `ConfigureWebHostDefaults` şunu kullanın `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="70337-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="70337-154">Kestrel seçenekleri</span><span class="sxs-lookup"><span data-stu-id="70337-154">Kestrel options</span></span>

<span data-ttu-id="70337-155">Kestrel Web sunucusu, Internet 'e yönelik dağıtımlarda özellikle yararlı olan kısıtlama yapılandırma seçeneklerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="70337-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="70337-156">Sınıfının özelliğinde kısıtlamaları ayarlayın <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> .</span><span class="sxs-lookup"><span data-stu-id="70337-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="70337-157">`Limits`Özelliği, sınıfının bir örneğini barındırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> .</span><span class="sxs-lookup"><span data-stu-id="70337-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="70337-158">Aşağıdaki örnekler <xref:Microsoft.AspNetCore.Server.Kestrel.Core> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="70337-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="70337-159">Bu makalenin ilerleyen kısımlarında gösterilen örneklerde, Kestrel seçenekleri C# kodunda yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="70337-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="70337-160">Ayrıca, Kestrel seçenekleri bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="70337-161">Örneğin, [dosya yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider) Kestrel yapılandırmasını bir *appsettings.json* veya appSettings 'ten yükleyebilir *. {1} Environment}. JSON* dosyası:</span><span class="sxs-lookup"><span data-stu-id="70337-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="70337-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> ve [uç nokta yapılandırması](#endpoint-configuration) yapılandırma sağlayıcılarından yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="70337-163">Kalan Kestrel yapılandırması C# kodunda yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="70337-164">Aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="70337-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="70337-165">Kestrel 'i yapılandırma `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="70337-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="70337-166">Sınıfına bir örneği `IConfiguration` ekleyin `Startup` .</span><span class="sxs-lookup"><span data-stu-id="70337-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="70337-167">Aşağıdaki örnek, eklenen yapılandırmanın özelliğe atandığını varsayar `Configuration` .</span><span class="sxs-lookup"><span data-stu-id="70337-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="70337-168">İçinde `Startup.ConfigureServices` , `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="70337-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="70337-169">Ana bilgisayarı oluştururken Kestrel yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="70337-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="70337-170">*Program.cs*' de, `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="70337-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="70337-171">Yukarıdaki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.</span><span class="sxs-lookup"><span data-stu-id="70337-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="70337-172">Etkin tut zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="70337-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="70337-173">[Etkin tutma zaman aşımını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="70337-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="70337-174">Varsayılan olarak 2 dakikadır.</span><span class="sxs-lookup"><span data-stu-id="70337-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="70337-175">İstemci bağlantıları üst sınırı</span><span class="sxs-lookup"><span data-stu-id="70337-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="70337-176">En fazla eş zamanlı açık TCP bağlantısı sayısı tüm uygulama için aşağıdaki kodla ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="70337-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="70337-177">HTTP veya HTTPS 'den başka bir protokole (örneğin, bir WebSockets isteğinde) yükseltilen bağlantılara yönelik ayrı bir sınır vardır.</span><span class="sxs-lookup"><span data-stu-id="70337-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="70337-178">Bir bağlantı yükseltildikten sonra, bu sınıra göre sayılmaz `MaxConcurrentConnections` .</span><span class="sxs-lookup"><span data-stu-id="70337-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="70337-179">En fazla bağlantı sayısı, varsayılan olarak sınırsız (null).</span><span class="sxs-lookup"><span data-stu-id="70337-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="70337-180">En büyük istek gövdesi boyutu</span><span class="sxs-lookup"><span data-stu-id="70337-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="70337-181">Varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu değer yaklaşık 28,6 MB 'tır.</span><span class="sxs-lookup"><span data-stu-id="70337-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="70337-182">ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yaklaşım, <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="70337-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="70337-183">Her istekte uygulama için kısıtlamanın nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="70337-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="70337-184">Ara yazılım içindeki belirli bir istek üzerindeki ayarı geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="70337-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="70337-185">Uygulama isteği okumaya başladıktan sonra bir istek üzerindeki sınırı yapılandırırsa, bir özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="70337-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="70337-186">`IsReadOnly`Özelliğin salt okuma durumunda olup olmadığını belirten bir özellik vardır. Bu, `MaxRequestBodySize` sınırı yapılandırmanın çok geç olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="70337-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="70337-187">Bir uygulama [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)arkasında [çalıştırıldığında](xref:host-and-deploy/iis/index#out-of-process-hosting-model) , IIS sınırı zaten ayarladığı için Kestrel 'nin istek gövdesi boyut sınırı devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="70337-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="70337-188">En az istek gövdesi veri hızı</span><span class="sxs-lookup"><span data-stu-id="70337-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="70337-189">Kestrel bayt/saniye cinsinden belirtilen fiyata ulaşan her saniye sonra denetler.</span><span class="sxs-lookup"><span data-stu-id="70337-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="70337-190">Oran en düşük değerin altına düşerse bağlantı zaman aşımına uğrar. Yetkisiz kullanım süresi, Kestrel 'in istemciye gönderme oranını en düşük süreye kadar artırabilme Bu süre boyunca fiyat denetlenmez.</span><span class="sxs-lookup"><span data-stu-id="70337-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="70337-191">Yetkisiz kullanım süresi, TCP yavaş başlatma nedeniyle başlangıçta verileri yavaş bir hızda gönderen bağlantıların bırakılmasını önlemeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="70337-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="70337-192">Varsayılan en düşük oran, 5 saniyelik bir yetkisiz kullanım süresi ile 240 bayt/saniye olur.</span><span class="sxs-lookup"><span data-stu-id="70337-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="70337-193">Yanıt için bir minimum oran da geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="70337-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="70337-194">İstek sınırını ayarlamaya yönelik kod ve yanıt sınırı, `RequestBody` `Response` özellik ve arabirim adlarında olduğu gibi aynı olur.</span><span class="sxs-lookup"><span data-stu-id="70337-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="70337-195">*Program.cs* içinde en düşük veri hızlarının nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="70337-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="70337-196">Ara yazılım içindeki istek başına düşen minimum hız sınırlarını geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="70337-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="70337-197"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>Önceki örnekte başvurulan, `HttpContext.Features` http/2 isteklerinde hız sınırlarını değiştirmek, protokolün istek çoğullama DESTEĞI nedeniyle http/2 için genel olarak desteklenmediği IÇIN, http/2 istekleri için ' de mevcut değildir.</span><span class="sxs-lookup"><span data-stu-id="70337-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="70337-198">Ancak, http <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> `HttpContext.Features` /2 istekleri için yine de vardır, çünkü okuma hızı sınırı,  `IHttpMinRequestBodyDataRateFeature.MinDataRate` `null` bir http/2 isteği için de olarak ayarlanarak tamamen istek temelli olarak devre dışı bırakılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="70337-199">`IHttpMinRequestBodyDataRateFeature.MinDataRate`Bunun dışında bir değere ayarlamaya çalışılması veya BIR `null` `NotSupportedException` http/2 isteği verilmeye neden olur.</span><span class="sxs-lookup"><span data-stu-id="70337-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="70337-200">`KestrelServerOptions.Limits`Http/1. x ve http/2 bağlantılarına hala uygulanan sunucu genelindeki hız sınırları.</span><span class="sxs-lookup"><span data-stu-id="70337-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="70337-201">İstek üst bilgileri zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="70337-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="70337-202">Sunucunun istek üst bilgilerini alması için harcadığı en uzun süreyi alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="70337-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="70337-203">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="70337-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="70337-204">Bağlantı başına en fazla akış</span><span class="sxs-lookup"><span data-stu-id="70337-204">Maximum streams per connection</span></span>

<span data-ttu-id="70337-205">`Http2.MaxStreamsPerConnection` HTTP/2 bağlantısı başına eşzamanlı istek akışı sayısını sınırlar.</span><span class="sxs-lookup"><span data-stu-id="70337-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="70337-206">Fazlalık akışlar reddedildi.</span><span class="sxs-lookup"><span data-stu-id="70337-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="70337-207">Varsayılan değer 100’dür.</span><span class="sxs-lookup"><span data-stu-id="70337-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="70337-208">Üst bilgi tablosu boyutu</span><span class="sxs-lookup"><span data-stu-id="70337-208">Header table size</span></span>

<span data-ttu-id="70337-209">HPACK kod çözücüsü HTTP/2 bağlantıları için HTTP üstbilgilerini açar.</span><span class="sxs-lookup"><span data-stu-id="70337-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="70337-210">`Http2.HeaderTableSize` HPACK kod çözücüsünün kullandığı üst bilgi sıkıştırma tablosunun boyutunu sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="70337-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="70337-211">Değer sekizli cinsinden sağlanır ve sıfırdan büyük olmalıdır (0).</span><span class="sxs-lookup"><span data-stu-id="70337-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="70337-212">Varsayılan değer 4096 ' dir.</span><span class="sxs-lookup"><span data-stu-id="70337-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="70337-213">En büyük çerçeve boyutu</span><span class="sxs-lookup"><span data-stu-id="70337-213">Maximum frame size</span></span>

<span data-ttu-id="70337-214">`Http2.MaxFrameSize` sunucu tarafından alınan veya gönderilen HTTP/2 bağlantı çerçevesi yükünün izin verilen en büyük boyutunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="70337-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="70337-215">Değer sekizli cinsinden sağlanır ve 2 ^ 14 (16.384) ile 2 ^ 24-1 (16.777.215) arasında olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="70337-216">Varsayılan değer 2 ^ 14 ' dir (16.384).</span><span class="sxs-lookup"><span data-stu-id="70337-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="70337-217">En fazla istek üst bilgi boyutu</span><span class="sxs-lookup"><span data-stu-id="70337-217">Maximum request header size</span></span>

<span data-ttu-id="70337-218">`Http2.MaxRequestHeaderFieldSize` istek üst bilgisi değerlerinin sekizlisi cinsinden izin verilen en büyük boyutu belirtir.</span><span class="sxs-lookup"><span data-stu-id="70337-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="70337-219">Bu sınır, sıkıştırılmış ve sıkıştırılmamış temsillerinde hem ad hem de değer için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="70337-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="70337-220">Değer sıfırdan büyük (0) olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="70337-221">Varsayılan değer 8.192 ' dir.</span><span class="sxs-lookup"><span data-stu-id="70337-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="70337-222">İlk bağlantı pencere boyutu</span><span class="sxs-lookup"><span data-stu-id="70337-222">Initial connection window size</span></span>

<span data-ttu-id="70337-223">`Http2.InitialConnectionWindowSize` sunucu, bağlantı başına tüm istekler (akışlar) genelinde toplanan tek seferde sunucunun arabelleğe aldığı en fazla istek gövde verilerini bayt cinsinden gösterir.</span><span class="sxs-lookup"><span data-stu-id="70337-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="70337-224">İstekleri ile de sınırlıdır `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="70337-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="70337-225">Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="70337-226">Varsayılan değer 128 KB 'tır (131.072).</span><span class="sxs-lookup"><span data-stu-id="70337-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="70337-227">İlk akış pencere boyutu</span><span class="sxs-lookup"><span data-stu-id="70337-227">Initial stream window size</span></span>

<span data-ttu-id="70337-228">`Http2.InitialStreamWindowSize` sunucu, istek başına bir kez (Stream) arabelleğe alınan en fazla istek gövde verilerini bayt cinsinden gösterir.</span><span class="sxs-lookup"><span data-stu-id="70337-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="70337-229">İstekleri ile de sınırlıdır `Http2.InitialConnectionWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="70337-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="70337-230">Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="70337-231">Varsayılan değer 96 KB 'tır (98.304).</span><span class="sxs-lookup"><span data-stu-id="70337-231">The default value is 96 KB (98,304).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

### <a name="http2-keep-alive-ping-configuration"></a><span data-ttu-id="70337-232">HTTP/2 canlı tutmayı ping yapılandırması</span><span class="sxs-lookup"><span data-stu-id="70337-232">HTTP/2 keep alive ping configuration</span></span>

<span data-ttu-id="70337-233">Kestrel, bağlı istemcilere HTTP/2 pingler gönderecek şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-233">Kestrel can be configured to send HTTP/2 pings to connected clients.</span></span> <span data-ttu-id="70337-234">HTTP/2 ping işlemleri birden çok amaca hizmet eder:</span><span class="sxs-lookup"><span data-stu-id="70337-234">HTTP/2 pings serve multiple purposes:</span></span>

* <span data-ttu-id="70337-235">Boştaki bağlantıları canlı tutun.</span><span class="sxs-lookup"><span data-stu-id="70337-235">Keep idle connections alive.</span></span> <span data-ttu-id="70337-236">Bazı istemciler ve proxy sunucular, boşta olan bağlantıları kapatır.</span><span class="sxs-lookup"><span data-stu-id="70337-236">Some clients and proxy servers close connections that are idle.</span></span> <span data-ttu-id="70337-237">HTTP/2 pingler bir bağlantıda etkinlik olarak değerlendirilir ve bağlantının boşta olarak kapatılmasını önler.</span><span class="sxs-lookup"><span data-stu-id="70337-237">HTTP/2 pings are considered as activity on a connection and prevent the connection from being closed as idle.</span></span>
* <span data-ttu-id="70337-238">Sağlıksız bağlantıları kapatın.</span><span class="sxs-lookup"><span data-stu-id="70337-238">Close unhealthy connections.</span></span> <span data-ttu-id="70337-239">İstemcinin yapılandırılan süre içinde canlı tut ping komutuna yanıt vermediği bağlantılar sunucu tarafından kapalıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-239">Connections where the client doesn't respond to the keep alive ping in the configured time are closed by the server.</span></span>

<span data-ttu-id="70337-240">HTTP/2 canlı tut ping işlemleri ile ilgili iki yapılandırma seçeneği vardır:</span><span class="sxs-lookup"><span data-stu-id="70337-240">There are two configuration options related to HTTP/2 keep alive pings:</span></span>

* <span data-ttu-id="70337-241">`Http2.KeepAlivePingInterval` , `TimeSpan` ping iç öğesini yapılandıran bir.</span><span class="sxs-lookup"><span data-stu-id="70337-241">`Http2.KeepAlivePingInterval` is a `TimeSpan` that configures the ping internal.</span></span> <span data-ttu-id="70337-242">Sunucu, bu süre boyunca herhangi bir çerçeve almazsa istemciye canlı bir ping gönderir.</span><span class="sxs-lookup"><span data-stu-id="70337-242">The server sends a keep alive ping to the client if it doesn't receive any frames for this period of time.</span></span> <span data-ttu-id="70337-243">Bu seçenek olarak ayarlandığında Canlı ping pingler devre dışı bırakılır `TimeSpan.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="70337-243">Keep alive pings are disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="70337-244">`TimeSpan.MaxValue` varsayılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="70337-244">The default value is `TimeSpan.MaxValue`.</span></span>
* <span data-ttu-id="70337-245">`Http2.KeepAlivePingTimeout` , `TimeSpan` ping zaman aşımını yapılandıran bir.</span><span class="sxs-lookup"><span data-stu-id="70337-245">`Http2.KeepAlivePingTimeout` is a `TimeSpan` that configures the ping timeout.</span></span> <span data-ttu-id="70337-246">Sunucu yanıt ping gibi bir çerçeve almazsa, bu zaman aşımı sırasında bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="70337-246">If the server doesn't receive any frames, such as a response ping, during this timeout then the connection is closed.</span></span> <span data-ttu-id="70337-247">Bu seçenek olarak ayarlandığında Canlı kalma zaman aşımını devre dışı bırakın `TimeSpan.MaxValue` .</span><span class="sxs-lookup"><span data-stu-id="70337-247">Keep alive timeout is disabled when this option is set to `TimeSpan.MaxValue`.</span></span> <span data-ttu-id="70337-248">Varsayılan değer 20 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="70337-248">The default value is 20 seconds.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.KeepAlivePingInterval = TimeSpan.FromSeconds(30);
    serverOptions.Limits.Http2.KeepAlivePingTimeout = TimeSpan.FromSeconds(60);
});
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### <a name="trailers"></a><span data-ttu-id="70337-249">Larına</span><span class="sxs-lookup"><span data-stu-id="70337-249">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="70337-250">Sıfırla</span><span class="sxs-lookup"><span data-stu-id="70337-250">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a><span data-ttu-id="70337-251">Zaman Uyumlu G/Ç</span><span class="sxs-lookup"><span data-stu-id="70337-251">Synchronous I/O</span></span>

<span data-ttu-id="70337-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> istek ve yanıt için zaman uyumlu g/ç 'ye izin verilip verilmediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="70337-252"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="70337-253">`false` varsayılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="70337-253">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="70337-254">Çok sayıda engelleme zaman uyumlu g/ç işlemi, iş parçacığı havuzuna yol açabilir, bu da uygulamanın yanıt vermemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="70337-254">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="70337-255">Yalnızca `AllowSynchronousIO` zaman uyumsuz g/ç desteklemeyen bir kitaplık kullanırken etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="70337-255">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="70337-256">Aşağıdaki örnek, zaman uyumlu g/ç 'yi sunar:</span><span class="sxs-lookup"><span data-stu-id="70337-256">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="70337-257">Diğer Kestrel seçenekleri ve limitleri hakkında daha fazla bilgi için bkz.:</span><span class="sxs-lookup"><span data-stu-id="70337-257">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="70337-258">Uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="70337-258">Endpoint configuration</span></span>

<span data-ttu-id="70337-259">Varsayılan olarak, ASP.NET Core bağlar:</span><span class="sxs-lookup"><span data-stu-id="70337-259">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="70337-260">`https://localhost:5001` (bir yerel geliştirme sertifikası varsa)</span><span class="sxs-lookup"><span data-stu-id="70337-260">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="70337-261">Kullanarak URL 'Leri belirtin:</span><span class="sxs-lookup"><span data-stu-id="70337-261">Specify URLs using the:</span></span>

* <span data-ttu-id="70337-262">`ASPNETCORE_URLS` ortam değişkeni.</span><span class="sxs-lookup"><span data-stu-id="70337-262">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="70337-263">`--urls` komut satırı bağımsız değişkeni.</span><span class="sxs-lookup"><span data-stu-id="70337-263">`--urls` command-line argument.</span></span>
* <span data-ttu-id="70337-264">`urls` Ana bilgisayar yapılandırma anahtarı.</span><span class="sxs-lookup"><span data-stu-id="70337-264">`urls` host configuration key.</span></span>
* <span data-ttu-id="70337-265">`UseUrls` genişletme yöntemi.</span><span class="sxs-lookup"><span data-stu-id="70337-265">`UseUrls` extension method.</span></span>

<span data-ttu-id="70337-266">Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS).</span><span class="sxs-lookup"><span data-stu-id="70337-266">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="70337-267">Değeri noktalı virgülle ayrılmış bir liste olarak yapılandırın (örneğin, `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="70337-267">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="70337-268">Bu yaklaşımlar hakkında daha fazla bilgi için [sunucu URL 'leri](xref:fundamentals/host/web-host#server-urls) ve [geçersiz kılma yapılandırması](xref:fundamentals/host/web-host#override-configuration)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="70337-268">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="70337-269">Geliştirme sertifikası oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="70337-269">A development certificate is created:</span></span>

* <span data-ttu-id="70337-270">[.NET Core SDK](/dotnet/core/sdk) yüklendiği zaman.</span><span class="sxs-lookup"><span data-stu-id="70337-270">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="70337-271">[Geliştirme-CERT aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="70337-271">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="70337-272">Bazı tarayıcılarda yerel geliştirme sertifikasına güvenmek için açık izin verilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="70337-272">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="70337-273">Proje şablonları, uygulamaları HTTPS üzerinde varsayılan olarak çalışacak şekilde yapılandırır ve [https yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.</span><span class="sxs-lookup"><span data-stu-id="70337-273">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="70337-274"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> URL öneklerini ve bağlantı noktalarını Kestrel için yapılandırmak üzere üzerinde çağrı veya Yöntemler.</span><span class="sxs-lookup"><span data-stu-id="70337-274">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="70337-275">`UseUrls`, `--urls` komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı ve `ASPNETCORE_URLS` ortam değişkeni de çalışır, ancak bu bölümün ilerleyen kısımlarında belirtilen sınırlamalara sahiptir (https uç noktası yapılandırması için varsayılan sertifika kullanılabilir olmalıdır).</span><span class="sxs-lookup"><span data-stu-id="70337-275">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="70337-276">`KestrelServerOptions` yapılandırmada</span><span class="sxs-lookup"><span data-stu-id="70337-276">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="70337-277">ConfigureEndpointDefaults Varsayılanları (eylem \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="70337-277">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="70337-278">`Action`Belirtilen her bir uç nokta için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="70337-278">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="70337-279">`ConfigureEndpointDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="70337-279">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="70337-280">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="70337-280">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="70337-281">ConfigureHttpsDefaults (eylem \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="70337-281">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="70337-282">`Action`Her HTTPS uç noktası için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="70337-282">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="70337-283">`ConfigureHttpsDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="70337-283">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="70337-284">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="70337-284">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="70337-285">Yapılandırma (Iconation)</span><span class="sxs-lookup"><span data-stu-id="70337-285">Configure(IConfiguration)</span></span>

<span data-ttu-id="70337-286">Bir as girişi alan Kestrel ayarlamak için bir yapılandırma yükleyicisi oluşturur <xref:Microsoft.Extensions.Configuration.IConfiguration> .</span><span class="sxs-lookup"><span data-stu-id="70337-286">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="70337-287">Yapılandırma, Kestrel için yapılandırma bölümünün kapsamına alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-287">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="70337-288">ListenOptions. UseHttps</span><span class="sxs-lookup"><span data-stu-id="70337-288">ListenOptions.UseHttps</span></span>

<span data-ttu-id="70337-289">Kestrel 'i HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="70337-289">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="70337-290">`ListenOptions.UseHttps` uzantılardan</span><span class="sxs-lookup"><span data-stu-id="70337-290">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="70337-291">`UseHttps`: Kestrel 'i varsayılan sertifikayla HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="70337-291">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="70337-292">Varsayılan sertifika yapılandırılmamışsa bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="70337-292">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="70337-293">`ListenOptions.UseHttps` parametrelere</span><span class="sxs-lookup"><span data-stu-id="70337-293">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="70337-294">`filename` , uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yolu ve dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-294">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="70337-295">`password` X. 509.440 sertifika verilerine erişmek için parola gereklidir.</span><span class="sxs-lookup"><span data-stu-id="70337-295">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="70337-296">`configureOptions` , öğesini `Action` yapılandırmak için kullanılır `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="70337-296">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="70337-297">Döndürür `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="70337-297">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="70337-298">`storeName` , sertifikanın yükleneceği sertifika deposudur.</span><span class="sxs-lookup"><span data-stu-id="70337-298">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="70337-299">`subject` , sertifika için konu adıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-299">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="70337-300">`allowInvalid` geçersiz sertifikaların, otomatik olarak imzalanan sertifikalar gibi göz önünde bulundurulmayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="70337-300">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="70337-301">`location` , sertifikanın yükleneceği mağaza konumudur.</span><span class="sxs-lookup"><span data-stu-id="70337-301">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="70337-302">`serverCertificate` , X. 509.440 sertifikasıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-302">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="70337-303">Üretimde HTTPS 'nin açıkça yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="70337-303">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="70337-304">En azından, varsayılan bir sertifika sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-304">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="70337-305">Daha sonra açıklanan desteklenen yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="70337-305">Supported configurations described next:</span></span>

* <span data-ttu-id="70337-306">Yapılandırma yok</span><span class="sxs-lookup"><span data-stu-id="70337-306">No configuration</span></span>
* <span data-ttu-id="70337-307">Varsayılan sertifikayı yapılandırmadan Değiştir</span><span class="sxs-lookup"><span data-stu-id="70337-307">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="70337-308">Koddaki varsayılanları değiştirme</span><span class="sxs-lookup"><span data-stu-id="70337-308">Change the defaults in code</span></span>

<span data-ttu-id="70337-309">*Yapılandırma yok*</span><span class="sxs-lookup"><span data-stu-id="70337-309">*No configuration*</span></span>

<span data-ttu-id="70337-310">Kestrel tarihinde dinler `http://localhost:5000` ve `https://localhost:5001` (varsayılan bir sertifika varsa).</span><span class="sxs-lookup"><span data-stu-id="70337-310">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="70337-311">*Varsayılan sertifikayı yapılandırmadan Değiştir*</span><span class="sxs-lookup"><span data-stu-id="70337-311">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="70337-312">`CreateDefaultBuilder``Configure(context.Configuration.GetSection("Kestrel"))`Kestrel yapılandırmasını yüklemek için varsayılan olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="70337-312">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="70337-313">Varsayılan bir HTTPS uygulama ayarları yapılandırma şeması Kestrel için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-313">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="70337-314">Disk üzerindeki bir dosyadan ya da bir sertifika deposundan kullanılacak URL 'Ler ve Sertifikalar dahil olmak üzere birden çok uç nokta yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="70337-314">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="70337-315">Aşağıdaki *appsettings.json* örnekte:</span><span class="sxs-lookup"><span data-stu-id="70337-315">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="70337-316">Geçersiz sertifikaların kullanılmasına izin vermek için **Allowwınvalid** `true` ' i ayarlayın (örneğin, otomatik olarak imzalanan sertifikalar).</span><span class="sxs-lookup"><span data-stu-id="70337-316">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="70337-317">Bir sertifika belirtmeyen herhangi bir HTTPS uç noktası (aşağıdaki örnekte bulunan **httpsdefaultcert** ), **Sertifikalar** > **varsayılan** veya geliştirme sertifikası altında tanımlanan sertifikaya geri döner.</span><span class="sxs-lookup"><span data-stu-id="70337-317">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="70337-318">Herhangi bir sertifika düğümü için **yol** ve **parola** kullanmanın alternatifi sertifika deposu alanlarını kullanarak sertifikayı belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="70337-318">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="70337-319">Örneğin, **sertifika**  >  **varsayılan** sertifikası şu şekilde belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="70337-319">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="70337-320">Şema notları:</span><span class="sxs-lookup"><span data-stu-id="70337-320">Schema notes:</span></span>

* <span data-ttu-id="70337-321">Uç nokta adları büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-321">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="70337-322">Örneğin, `HTTPS` ve `Https` geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="70337-322">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="70337-323">`Url`Her uç nokta için parametresi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="70337-323">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="70337-324">Bu parametrenin biçimi, en üst düzey `Urls` yapılandırma parametresiyle aynıdır, ancak tek bir değerle sınırlı olur.</span><span class="sxs-lookup"><span data-stu-id="70337-324">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="70337-325">Bu uç noktalar, üst düzey yapılandırmada tanımlananlar yerine `Urls` bunlara ekleme yerine bunların yerini alır.</span><span class="sxs-lookup"><span data-stu-id="70337-325">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="70337-326">Kullanılarak kodda tanımlanan uç noktalar `Listen` yapılandırma bölümünde tanımlanan uç noktalar ile birikimlidir.</span><span class="sxs-lookup"><span data-stu-id="70337-326">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="70337-327">Bu `Certificate` bölüm isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-327">The `Certificate` section is optional.</span></span> <span data-ttu-id="70337-328">`Certificate`Bölüm belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır.</span><span class="sxs-lookup"><span data-stu-id="70337-328">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="70337-329">Kullanılabilir varsayılan değer yoksa, sunucu bir özel durum oluşturur ve başlayamaz.</span><span class="sxs-lookup"><span data-stu-id="70337-329">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="70337-330">Bu `Certificate` bölüm hem **yol** &ndash; **parolasını** hem de **Konu** &ndash; **deposu** sertifikalarını destekler.</span><span class="sxs-lookup"><span data-stu-id="70337-330">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="70337-331">Herhangi bir sayıda uç nokta, bağlantı noktası çakışmalarına neden olmadıkları sürece bu şekilde tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-331">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="70337-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))` yapılandırılmış bir `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` uç noktanın ayarlarını tamamlamak için kullanılabilecek bir yöntemi olan bir döndürür:</span><span class="sxs-lookup"><span data-stu-id="70337-332">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="70337-333">`KestrelServerOptions.ConfigurationLoader` , tarafından sağlana gibi var olan yükleyicisindeki yinelemeye devam etmek için doğrudan erişilebilir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="70337-333">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="70337-334">Her uç noktanın yapılandırma bölümü, `Endpoint` özel ayarların okunabilmesi için yöntemindeki seçeneklerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-334">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="70337-335">Birden çok yapılandırma, `options.Configure(context.Configuration.GetSection("{SECTION}"))` başka bir bölümle yeniden çağırarak yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="70337-335">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="70337-336">Önceki örneklerde açıkça çağrılmadığı takdirde, yalnızca son yapılandırma kullanılır `Load` .</span><span class="sxs-lookup"><span data-stu-id="70337-336">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="70337-337">Metapackage, `Load` varsayılan yapılandırma bölümünün değiştirilmesini sağlayacak şekilde çağırmıyor.</span><span class="sxs-lookup"><span data-stu-id="70337-337">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="70337-338">`KestrelConfigurationLoader``Listen`API ailesini `KestrelServerOptions` `Endpoint` aşırı yükleme olarak yansıtır, bu nedenle kod ve yapılandırma uç noktaları aynı yerde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-338">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="70337-339">Bu aşırı yüklemeler adları kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları kullanır.</span><span class="sxs-lookup"><span data-stu-id="70337-339">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="70337-340">*Koddaki varsayılanları değiştirme*</span><span class="sxs-lookup"><span data-stu-id="70337-340">*Change the defaults in code*</span></span>

<span data-ttu-id="70337-341">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` `ListenOptions` `HttpsConnectionAdapterOptions` , önceki senaryoda belirtilen varsayılan sertifikayı geçersiz kılma dahil, ve için varsayılan ayarları değiştirmek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-341">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="70337-342">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-342">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="70337-343">*SNı için Kestrel desteği*</span><span class="sxs-lookup"><span data-stu-id="70337-343">*Kestrel support for SNI*</span></span>

<span data-ttu-id="70337-344">[Sunucu adı belirtme (SNı)](https://tools.ietf.org/html/rfc6066#section-3) , aynı IP adresi ve bağlantı noktası üzerinde birden fazla etki alanını barındırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-344">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="70337-345">SNı 'nin çalışması için, istemci, TLS el sıkışması sırasında güvenli oturum ana bilgisayar adını, sunucunun doğru sertifikayı sağlayabilmesi için gönderir.</span><span class="sxs-lookup"><span data-stu-id="70337-345">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="70337-346">İstemci, TLS anlaşmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için bulunan sertifikayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="70337-346">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="70337-347">Kestrel, geri çağırma aracılığıyla SNı destekler `ServerCertificateSelector` .</span><span class="sxs-lookup"><span data-stu-id="70337-347">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="70337-348">Geri çağırma, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesini sağlamak için bağlantı başına bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="70337-348">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="70337-349">SNı desteği şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="70337-349">SNI support requires:</span></span>

* <span data-ttu-id="70337-350">Hedef Framework `netcoreapp2.1` veya sonraki sürümlerde çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="70337-350">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="70337-351">`net461`Veya sonraki sürümlerde, geri çağırma çağrılır, ancak `name` her zaman olur `null` .</span><span class="sxs-lookup"><span data-stu-id="70337-351">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="70337-352">`name`Ayrıca, `null` ISTEMCI, TLS el sıkışmasının ana bilgisayar adı parametresini sağlamıyorsa de olur.</span><span class="sxs-lookup"><span data-stu-id="70337-352">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="70337-353">Tüm Web siteleri aynı Kestrel örneğinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="70337-353">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="70337-354">Kestrel, bir IP adresi ve bağlantı noktasının bir ters proxy olmadan birden çok örnek arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="70337-354">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="70337-355">Bağlantı günlüğü</span><span class="sxs-lookup"><span data-stu-id="70337-355">Connection logging</span></span>

<span data-ttu-id="70337-356"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>Bir bağlantıda bayt düzeyinde iletişim Için hata ayıklama düzeyi günlüklerini yayma çağrısı.</span><span class="sxs-lookup"><span data-stu-id="70337-356">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="70337-357">Bağlantı günlüğü, TLS şifreleme ve proxy 'nin arkasındaki gibi alt düzey iletişimde sorunları gidermeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="70337-357">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="70337-358">`UseConnectionLogging`Daha önce yerleştirilmişse `UseHttps` , şifrelenmiş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="70337-358">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="70337-359">`UseConnectionLogging`Öğesinden sonra yerleştirilmişse `UseHttps` , şifresi çözülmüş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="70337-359">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="70337-360">TCP yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="70337-360">Bind to a TCP socket</span></span>

<span data-ttu-id="70337-361"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Yöntemi BIR TCP yuvasına bağlanır ve bir seçenek lambda X. 509.440 sertifika yapılandırmasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="70337-361">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="70337-362">Örnek, HTTPS 'yi ile bir uç nokta için yapılandırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="70337-362">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="70337-363">Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak üzere aynı API 'yi kullanın.</span><span class="sxs-lookup"><span data-stu-id="70337-363">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="70337-364">UNIX yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="70337-364">Bind to a Unix socket</span></span>

<span data-ttu-id="70337-365"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>Aşağıdaki örnekte gösterildiği gibi NGINX ile gelişmiş performans için ile birlikte bir UNIX yuvası dinleyin:</span><span class="sxs-lookup"><span data-stu-id="70337-365">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="70337-366">NGINX yapılandırma dosyasında, `server`  >  `location`  >  `proxy_pass` girdisini olarak ayarlayın `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="70337-366">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="70337-367">`{KESTREL SOCKET}` , için belirtilen yuvanın adıdır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (örneğin, `kestrel-test.sock` Önceki örnekte).</span><span class="sxs-lookup"><span data-stu-id="70337-367">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="70337-368">Yuvanın NGINX tarafından yazılabilir olduğundan emin olun (örneğin, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="70337-368">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="70337-369">Bağlantı noktası 0</span><span class="sxs-lookup"><span data-stu-id="70337-369">Port 0</span></span>

<span data-ttu-id="70337-370">Bağlantı noktası numarası `0` belirtildiğinde, Kestrel dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır.</span><span class="sxs-lookup"><span data-stu-id="70337-370">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="70337-371">Aşağıdaki örnek, çalışma zamanında Kestrel gerçekte hangi bağlantı noktasının gerçekten bağlandığını nasıl belirleyeceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="70337-371">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="70337-372">Uygulama çalıştırıldığında konsol penceresi çıkışı, uygulamanın erişilebileceği dinamik bağlantı noktasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="70337-372">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="70337-373">Sınırlamalar</span><span class="sxs-lookup"><span data-stu-id="70337-373">Limitations</span></span>

<span data-ttu-id="70337-374">Aşağıdaki yaklaşımlar ile uç noktaları yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="70337-374">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="70337-375">`--urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="70337-375">`--urls` command-line argument</span></span>
* <span data-ttu-id="70337-376">`urls` Ana bilgisayar yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="70337-376">`urls` host configuration key</span></span>
* <span data-ttu-id="70337-377">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="70337-377">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="70337-378">Bu yöntemler, kodun Kestrel dışındaki sunucularla çalışmasını sağlamak için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-378">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="70337-379">Ancak, aşağıdaki sınırlamalara dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="70337-379">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="70337-380">HTTPS uç noktası yapılandırmasında varsayılan bir sertifika sağlanmadığı sürece HTTPS bu yaklaşımlar ile kullanılamaz (örneğin, `KestrelServerOptions` Bu konunun önceki kısımlarında gösterildiği gibi yapılandırma veya yapılandırma dosyası kullanılıyor).</span><span class="sxs-lookup"><span data-stu-id="70337-380">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="70337-381">Hem hem de `Listen` `UseUrls` yaklaşımları aynı anda kullanıldığında, uç noktalar `Listen` `UseUrls` uç noktaları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="70337-381">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="70337-382">IIS uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="70337-382">IIS endpoint configuration</span></span>

<span data-ttu-id="70337-383">IIS kullanırken, IIS geçersiz kılma bağlamaları için URL bağlamaları veya ya da tarafından ayarlanır `Listen` `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="70337-383">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="70337-384">Daha fazla bilgi için [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="70337-384">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="70337-385">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="70337-385">ListenOptions.Protocols</span></span>

<span data-ttu-id="70337-386">`Protocols`Özelliği, `HttpProtocols` bir bağlantı uç noktasında veya sunucu IÇIN etkin HTTP protokollerini () belirler.</span><span class="sxs-lookup"><span data-stu-id="70337-386">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="70337-387">`Protocols`Sabit listesinden özelliğe bir değer atayın `HttpProtocols` .</span><span class="sxs-lookup"><span data-stu-id="70337-387">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="70337-388">`HttpProtocols` sabit listesi değeri</span><span class="sxs-lookup"><span data-stu-id="70337-388">`HttpProtocols` enum value</span></span> | <span data-ttu-id="70337-389">Bağlantı protokolü izin verildi</span><span class="sxs-lookup"><span data-stu-id="70337-389">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="70337-390">Yalnızca HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="70337-390">HTTP/1.1 only.</span></span> <span data-ttu-id="70337-391">, TLS olmadan veya ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-391">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="70337-392">Yalnızca HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="70337-392">HTTP/2 only.</span></span> <span data-ttu-id="70337-393">Yalnızca istemci [önceki bir bilgi modunu](https://tools.ietf.org/html/rfc7540#section-3.4)DESTEKLIYORSA, TLS olmadan kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-393">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="70337-394">HTTP/1.1 ve HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="70337-394">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="70337-395">HTTP/2, istemcinin TLS [uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) el sıkışmasında http/2 seçmesini gerektirir; Aksi takdirde, bağlantı varsayılan olarak HTTP/1.1 ' dir.</span><span class="sxs-lookup"><span data-stu-id="70337-395">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="70337-396">`ListenOptions.Protocols`Herhangi bir uç nokta için varsayılan değer `HttpProtocols.Http1AndHttp2` .</span><span class="sxs-lookup"><span data-stu-id="70337-396">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="70337-397">HTTP/2 için TLS kısıtlamaları:</span><span class="sxs-lookup"><span data-stu-id="70337-397">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="70337-398">TLS sürüm 1,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="70337-398">TLS version 1.2 or later</span></span>
* <span data-ttu-id="70337-399">Yeniden anlaşma devre dışı</span><span class="sxs-lookup"><span data-stu-id="70337-399">Renegotiation disabled</span></span>
* <span data-ttu-id="70337-400">Sıkıştırma devre dışı</span><span class="sxs-lookup"><span data-stu-id="70337-400">Compression disabled</span></span>
* <span data-ttu-id="70337-401">En az kısa ömürlü anahtar değişim boyutları:</span><span class="sxs-lookup"><span data-stu-id="70337-401">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="70337-402">Eliptik Eğri Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bit minimum</span><span class="sxs-lookup"><span data-stu-id="70337-402">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="70337-403">Sınırlı alan Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; : 2048 bit minimum</span><span class="sxs-lookup"><span data-stu-id="70337-403">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="70337-404">Şifre paketi yasaklandı.</span><span class="sxs-lookup"><span data-stu-id="70337-404">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="70337-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; P-256 eliptik eğrisi &lbrack; `FIPS186` &rbrack; Varsayılan olarak desteklenir.</span><span class="sxs-lookup"><span data-stu-id="70337-405">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="70337-406">Aşağıdaki örnek, 8000 numaralı bağlantı noktasında HTTP/1.1 ve HTTP/2 bağlantılarına izin verir.</span><span class="sxs-lookup"><span data-stu-id="70337-406">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="70337-407">Bağlantılar, sağlanan bir sertifikayla TLS ile güvenlidir:</span><span class="sxs-lookup"><span data-stu-id="70337-407">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="70337-408">Gerektiğinde belirli şifrelemeler için TLS el sıkışmaları için bağlantı temelinde filtre uygulamak üzere bağlantı ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="70337-408">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="70337-409">Aşağıdaki örnek, <xref:System.NotSupportedException> uygulamanın desteklemediği herhangi bir şifre algoritması için oluşturur.</span><span class="sxs-lookup"><span data-stu-id="70337-409">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="70337-410">Alternatif olarak, [ılshandshakefeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) öğesini kabul edilebilir şifreleme paketleri listesi ile tanımlayın ve karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="70337-410">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="70337-411">[CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) şifre algoritması ile hiçbir şifreleme kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="70337-411">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="70337-412">Bağlantı filtrelemesi, bir lambda aracılığıyla da yapılandırılabilir <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> :</span><span class="sxs-lookup"><span data-stu-id="70337-412">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="70337-413">Linux 'ta, <xref:System.Net.Security.CipherSuitesPolicy> TLS el sıkışmaları her bağlantı temelinde filtrelemek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="70337-413">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="70337-414">*Protokolü yapılandırmadan ayarla*</span><span class="sxs-lookup"><span data-stu-id="70337-414">*Set the protocol from configuration*</span></span>

<span data-ttu-id="70337-415">`CreateDefaultBuilder``serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`Kestrel yapılandırmasını yüklemek için varsayılan olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="70337-415">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="70337-416">Aşağıdaki *appsettings.json* örnek, tüm uç noktalar için varsayılan bağlantı protokolü olarak http/1.1 'yi kurar:</span><span class="sxs-lookup"><span data-stu-id="70337-416">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="70337-417">Aşağıdaki *appsettings.json* örnek, belirli bir uç nokta IÇIN http/1.1 bağlantı protokolünü belirler:</span><span class="sxs-lookup"><span data-stu-id="70337-417">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="70337-418">Yapılandırma tarafından ayarlanan kod geçersiz kılma değerlerinde belirtilen protokoller.</span><span class="sxs-lookup"><span data-stu-id="70337-418">Protocols specified in code override values set by configuration.</span></span>

### <a name="url-prefixes"></a><span data-ttu-id="70337-419">URL önekleri</span><span class="sxs-lookup"><span data-stu-id="70337-419">URL prefixes</span></span>

<span data-ttu-id="70337-420">`UseUrls`, `--urls` Komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı veya `ASPNETCORE_URLS` ortam değişkeni kullanılırken, URL önekleri aşağıdaki biçimlerden birinde olabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-420">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="70337-421">Yalnızca HTTP URL ön ekleri geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="70337-421">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="70337-422">Kestrel, kullanılarak URL bağlamaları yapılandırılırken HTTPS 'YI desteklemez `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="70337-422">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="70337-423">Bağlantı noktası numarası olan IPv4 adresi</span><span class="sxs-lookup"><span data-stu-id="70337-423">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="70337-424">`0.0.0.0` Tüm IPv4 adreslerine bağlanan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="70337-424">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="70337-425">Bağlantı noktası numarasına sahip IPv6 adresi</span><span class="sxs-lookup"><span data-stu-id="70337-425">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="70337-426">`[::]` , IPv4 'un IPv6 eşdeğeridir `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="70337-426">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="70337-427">Bağlantı noktası numarası olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="70337-427">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="70337-428">, Ve ana bilgisayar adları `*` `+` özel değildir.</span><span class="sxs-lookup"><span data-stu-id="70337-428">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="70337-429">Geçerli bir IP adresi olarak tanınmayan veya `localhost` tüm IPv4 ve IPv6 IP 'lerine bağlanan her şey.</span><span class="sxs-lookup"><span data-stu-id="70337-429">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="70337-430">Farklı ana bilgisayar adlarını aynı bağlantı noktasında farklı ASP.NET Core uygulamalarına bağlamak için, [HTTP.sys](xref:fundamentals/servers/httpsys) veya IIS, NGINX veya Apache gibi bir ters proxy sunucusunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="70337-430">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="70337-431">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="70337-431">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="70337-432">Port numarası `localhost` veya bağlantı noktası numarası ile geri döngü IP 'si olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="70337-432">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="70337-433">Belirtildiğinde `localhost` , Kestrel hem IPv4 hem de IPv6 geri döngü arabirimlerini bağlamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="70337-433">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="70337-434">İstenen bağlantı noktası, herhangi bir geri döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="70337-434">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="70337-435">Herhangi bir geri döngü arabirimi başka bir nedenle kullanılamıyorsa (genellikle IPv6 desteklenmediği için), Kestrel bir uyarı kaydeder.</span><span class="sxs-lookup"><span data-stu-id="70337-435">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="70337-436">Konak filtreleme</span><span class="sxs-lookup"><span data-stu-id="70337-436">Host filtering</span></span>

<span data-ttu-id="70337-437">Kestrel gibi önekleri temel alarak yapılandırmayı desteklese `http://example.com:5000` de, Kestrel büyük ölçüde ana bilgisayar adını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="70337-437">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="70337-438">Ana bilgisayar `localhost` , geri döngü adreslerine bağlama için kullanılan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="70337-438">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="70337-439">Açık IP adresi dışındaki tüm ana bilgisayar tüm genel IP adreslerine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="70337-439">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="70337-440">`Host` Üstbilgiler doğrulanmadı.</span><span class="sxs-lookup"><span data-stu-id="70337-440">`Host` headers aren't validated.</span></span>

<span data-ttu-id="70337-441">Geçici bir çözüm olarak, ana bilgisayar filtreleme ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="70337-441">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="70337-442">Ana bilgisayar filtreleme ara yazılımı, ASP.NET Core uygulamaları için örtük olarak sunulan [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="70337-442">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="70337-443">Ara yazılım tarafından eklenir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ve şunları çağırır <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="70337-443">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="70337-444">Ana bilgisayar filtreleme ara yazılımı varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-444">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="70337-445">Ara yazılımı etkinleştirmek için `AllowedHosts` appSettings 'de bir anahtar tanımlayın *appsettings.json* / *. \<EnvironmentName> . JSON*.</span><span class="sxs-lookup"><span data-stu-id="70337-445">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="70337-446">Değer, bağlantı noktası numaraları olmayan ana bilgisayar adlarının noktalı virgülle ayrılmış listesidir:</span><span class="sxs-lookup"><span data-stu-id="70337-446">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="70337-447">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="70337-447">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="70337-448">[Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer) da bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçenek içerir.</span><span class="sxs-lookup"><span data-stu-id="70337-448">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="70337-449">İletilen üstbilgiler ara yazılımı ve ana bilgisayar filtreleme ara yazılımı, farklı senaryolar için benzer işlevlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="70337-449">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="70337-450">`AllowedHosts`Iletilen üstbilgiler ara yazılımı ile, `Host` istekler ters bir ara sunucu veya yük dengeleyici ile iletilirken üst bilgi korunurken, bu işlem için uygun bir ayar vardır.</span><span class="sxs-lookup"><span data-stu-id="70337-450">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="70337-451">`AllowedHosts`Ana bilgisayar filtreleme ara yazılımı ile ayarlama, Kestrel herkese açık bir uç sunucu olarak veya `Host` üst bilgi doğrudan iletildiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="70337-451">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="70337-452">Iletilen üstbilgiler ara yazılımı hakkında daha fazla bilgi için bkz <xref:host-and-deploy/proxy-load-balancer> ..</span><span class="sxs-lookup"><span data-stu-id="70337-452">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

## <a name="libuv-transport-configuration"></a><span data-ttu-id="70337-453">Libuv aktarım yapılandırması</span><span class="sxs-lookup"><span data-stu-id="70337-453">Libuv transport configuration</span></span>

<span data-ttu-id="70337-454">ASP.NET Core 5,0 itibariyle Kestrel 'in libuv taşıması artık kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="70337-454">As of ASP.NET Core 5.0, Kestrel's Libuv transport is obsolete.</span></span> <span data-ttu-id="70337-455">Libuv taşıması, Windows ARM64 gibi yeni işletim sistemi platformlarını desteklemek için güncelleştirmeleri almaz ve gelecek sürümlerde kaldırılacaktır.</span><span class="sxs-lookup"><span data-stu-id="70337-455">Libuv transport doesn't receive updates to support new OS platforms, such as Windows ARM64, and will be removed in a future release.</span></span> <span data-ttu-id="70337-456">Kullanılmayan yönteme yapılan tüm çağrıları kaldırın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> ve bunun yerine Kestrel 'in varsayılan yuva taşımasını kullanın.</span><span class="sxs-lookup"><span data-stu-id="70337-456">Remove any calls to the obsolete <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> method and use Kestrel's default Socket transport instead.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

## <a name="libuv-transport-configuration"></a><span data-ttu-id="70337-457">Libuv aktarım yapılandırması</span><span class="sxs-lookup"><span data-stu-id="70337-457">Libuv transport configuration</span></span>

<span data-ttu-id="70337-458">Libuv () kullanımını gerektiren projeler için <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> :</span><span class="sxs-lookup"><span data-stu-id="70337-458">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>):</span></span>

* <span data-ttu-id="70337-459">[`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv)Uygulamanın proje dosyasına paket için bir bağımlılık ekleyin:</span><span class="sxs-lookup"><span data-stu-id="70337-459">Add a dependency for the [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="70337-460">Şunu çağırın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="70337-460">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> on the `IWebHostBuilder`:</span></span>

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

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="70337-461">Kestrel, ASP.NET Core için platformlar arası [Web sunucusudur](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="70337-461">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="70337-462">Kestrel, ASP.NET Core proje şablonlarında varsayılan olarak bulunan Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="70337-462">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="70337-463">Kestrel aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="70337-463">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="70337-464">HTTPS</span><span class="sxs-lookup"><span data-stu-id="70337-464">HTTPS</span></span>
* <span data-ttu-id="70337-465">[WebSockets](https://github.com/aspnet/websockets) 'i etkinleştirmek için kullanılan donuk yükseltme</span><span class="sxs-lookup"><span data-stu-id="70337-465">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="70337-466">NGINX 'in arkasında yüksek performans için UNIX Yuvaları</span><span class="sxs-lookup"><span data-stu-id="70337-466">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="70337-467">HTTP/2 (macOS hariç &dagger; )</span><span class="sxs-lookup"><span data-stu-id="70337-467">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="70337-468">&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="70337-468">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="70337-469">Kestrel, .NET Core 'un desteklediği tüm platformlarda ve sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="70337-469">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="70337-470">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="70337-470">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="70337-471">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="70337-471">HTTP/2 support</span></span>

<span data-ttu-id="70337-472">Aşağıdaki temel gereksinimler karşılanıyorsa, [http/2](https://httpwg.org/specs/rfc7540.html) ASP.NET Core uygulamalar için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="70337-472">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="70337-473">İşletim sistemi&dagger;</span><span class="sxs-lookup"><span data-stu-id="70337-473">Operating system&dagger;</span></span>
  * <span data-ttu-id="70337-474">Windows Server 2016/Windows 10 veya üzeri&Dagger;</span><span class="sxs-lookup"><span data-stu-id="70337-474">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="70337-475">OpenSSL 1.0.2 veya üzerini içeren Linux (örneğin, Ubuntu 16,04 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="70337-475">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="70337-476">Hedef Framework: .NET Core 2,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="70337-476">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="70337-477">[Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı</span><span class="sxs-lookup"><span data-stu-id="70337-477">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="70337-478">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="70337-478">TLS 1.2 or later connection</span></span>

<span data-ttu-id="70337-479">&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="70337-479">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="70337-480">&Dagger;Kestrel, Windows Server 2012 R2 ve Windows 8.1 'de HTTP/2 için sınırlı destek içerir.</span><span class="sxs-lookup"><span data-stu-id="70337-480">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="70337-481">Bu işletim sistemlerinde kullanılabilir olan desteklenen TLS şifre paketlerinin listesi sınırlı olduğundan destek sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-481">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="70337-482">TLS bağlantılarının güvenliğini sağlamak için Eliptik Eğri dijital Imza algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-482">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="70337-483">Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="70337-483">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="70337-484">HTTP/2 varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-484">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="70337-485">Yapılandırma hakkında daha fazla bilgi için [Kestrel Options](#kestrel-options) ve [Listenoptions. Protocols](#listenoptionsprotocols) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="70337-485">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="70337-486">Ters ara sunucu ile Kestrel ne zaman kullanılır?</span><span class="sxs-lookup"><span data-stu-id="70337-486">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="70337-487">Kestrel, kendisi veya [Internet Information Services (IIS)](https://www.iis.net/), [NGINX](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi bir *ters ara sunucu* ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-487">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="70337-488">Ters proxy sunucusu, ağdan gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="70337-488">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="70337-489">Sınır (Internet 'e yönelik) Web sunucusu olarak kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="70337-489">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel, ters proxy sunucusu olmadan doğrudan Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="70337-491">Ters Proxy yapılandırmasında kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="70337-491">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel, IIS, NGINX veya Apache gibi bir ters ara sunucu üzerinden Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="70337-493">İki yapılandırma de, ters ara sunucu sunucusuyla veya olmadan, desteklenen bir barındırma yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="70337-493">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="70337-494">Ters proxy sunucusu olmayan bir uç sunucu olarak kullanılan Kestrel, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="70337-494">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="70337-495">Kestrel bir bağlantı noktasını dinlemek üzere yapılandırıldığında, Kestrel isteklerin üst bilgilerinden bağımsız olarak bu bağlantı noktası için tüm trafiği işler `Host` .</span><span class="sxs-lookup"><span data-stu-id="70337-495">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="70337-496">Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel 'e iletme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="70337-496">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="70337-497">Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucu kullanılması iyi bir seçim olabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-497">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="70337-498">Ters proxy:</span><span class="sxs-lookup"><span data-stu-id="70337-498">A reverse proxy:</span></span>

* <span data-ttu-id="70337-499">, Barındırdığı uygulamaların açığa çıkarılan genel yüzey alanını sınırlayabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-499">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="70337-500">Ek bir yapılandırma ve savunma katmanı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="70337-500">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="70337-501">, Mevcut altyapıyla daha iyi tümleşebilir.</span><span class="sxs-lookup"><span data-stu-id="70337-501">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="70337-502">Yük dengelemeyi ve güvenli iletişim (HTTPS) yapılandırmasını kolaylaştırın.</span><span class="sxs-lookup"><span data-stu-id="70337-502">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="70337-503">Yalnızca ters proxy sunucusu bir X. 509.440 sertifikası gerektirir ve bu sunucu, düz HTTP kullanarak, iç ağdaki uygulamanın sunucularıyla iletişim kurabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-503">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="70337-504">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="70337-504">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="70337-505">ASP.NET Core uygulamalarında Kestrel kullanma</span><span class="sxs-lookup"><span data-stu-id="70337-505">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="70337-506">Microsoft. [AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) paketi [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="70337-506">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="70337-507">ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır.</span><span class="sxs-lookup"><span data-stu-id="70337-507">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="70337-508">*Program.cs*' de, şablon kodu çağırır <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ve bu da <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> arka planda çağrı yapılır.</span><span class="sxs-lookup"><span data-stu-id="70337-508">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="70337-509">Ana bilgisayarı hakkında daha fazla bilgi için `CreateDefaultBuilder` , uygulamasının *konak ayarlama* bölümüne bakın <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="70337-509">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="70337-510">Çağrıldıktan sonra ek yapılandırma sağlamak için `CreateDefaultBuilder` şunu kullanın `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="70337-510">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="70337-511">Uygulama, `CreateDefaultBuilder` Konağı kurmak için çağırmazsa, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> çağrılmadan **önce** çağırın `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="70337-511">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="70337-512">Kestrel seçenekleri</span><span class="sxs-lookup"><span data-stu-id="70337-512">Kestrel options</span></span>

<span data-ttu-id="70337-513">Kestrel Web sunucusu, Internet 'e yönelik dağıtımlarda özellikle yararlı olan kısıtlama yapılandırma seçeneklerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="70337-513">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="70337-514">Sınıfının özelliğinde kısıtlamaları ayarlayın <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> .</span><span class="sxs-lookup"><span data-stu-id="70337-514">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="70337-515">`Limits`Özelliği, sınıfının bir örneğini barındırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> .</span><span class="sxs-lookup"><span data-stu-id="70337-515">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="70337-516">Aşağıdaki örnekler <xref:Microsoft.AspNetCore.Server.Kestrel.Core> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="70337-516">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="70337-517">Aşağıdaki örneklerde C# kodunda yapılandırılan Kestrel seçenekleri de bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-517">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="70337-518">Örneğin, dosya yapılandırma sağlayıcısı Kestrel yapılandırmasını bir *appsettings.json* veya appSettings 'ten yükleyebilir *. {1} Environment}. JSON* dosyası:</span><span class="sxs-lookup"><span data-stu-id="70337-518">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="70337-519">Aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="70337-519">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="70337-520">Kestrel 'i yapılandırma `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="70337-520">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="70337-521">Sınıfına bir örneği `IConfiguration` ekleyin `Startup` .</span><span class="sxs-lookup"><span data-stu-id="70337-521">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="70337-522">Aşağıdaki örnek, eklenen yapılandırmanın özelliğe atandığını varsayar `Configuration` .</span><span class="sxs-lookup"><span data-stu-id="70337-522">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="70337-523">İçinde `Startup.ConfigureServices` , `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="70337-523">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="70337-524">Ana bilgisayarı oluştururken Kestrel yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="70337-524">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="70337-525">*Program.cs*' de, `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="70337-525">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="70337-526">Yukarıdaki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.</span><span class="sxs-lookup"><span data-stu-id="70337-526">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="70337-527">Etkin tut zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="70337-527">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="70337-528">[Etkin tutma zaman aşımını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="70337-528">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="70337-529">Varsayılan olarak 2 dakikadır.</span><span class="sxs-lookup"><span data-stu-id="70337-529">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="70337-530">İstemci bağlantıları üst sınırı</span><span class="sxs-lookup"><span data-stu-id="70337-530">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="70337-531">En fazla eş zamanlı açık TCP bağlantısı sayısı tüm uygulama için aşağıdaki kodla ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="70337-531">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="70337-532">HTTP veya HTTPS 'den başka bir protokole (örneğin, bir WebSockets isteğinde) yükseltilen bağlantılara yönelik ayrı bir sınır vardır.</span><span class="sxs-lookup"><span data-stu-id="70337-532">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="70337-533">Bir bağlantı yükseltildikten sonra, bu sınıra göre sayılmaz `MaxConcurrentConnections` .</span><span class="sxs-lookup"><span data-stu-id="70337-533">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="70337-534">En fazla bağlantı sayısı, varsayılan olarak sınırsız (null).</span><span class="sxs-lookup"><span data-stu-id="70337-534">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="70337-535">En büyük istek gövdesi boyutu</span><span class="sxs-lookup"><span data-stu-id="70337-535">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="70337-536">Varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu değer yaklaşık 28,6 MB 'tır.</span><span class="sxs-lookup"><span data-stu-id="70337-536">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="70337-537">ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yaklaşım, <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="70337-537">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="70337-538">Her istekte uygulama için kısıtlamanın nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="70337-538">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="70337-539">Ara yazılım içindeki belirli bir istek üzerindeki ayarı geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="70337-539">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="70337-540">Uygulama isteği okumaya başladıktan sonra bir istek üzerindeki sınırı yapılandırırsa, bir özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="70337-540">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="70337-541">`IsReadOnly`Özelliğin salt okuma durumunda olup olmadığını belirten bir özellik vardır. Bu, `MaxRequestBodySize` sınırı yapılandırmanın çok geç olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="70337-541">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="70337-542">Bir uygulama [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)arkasında [çalıştırıldığında](xref:host-and-deploy/iis/index#out-of-process-hosting-model) , IIS sınırı zaten ayarladığı için Kestrel 'nin istek gövdesi boyut sınırı devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="70337-542">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="70337-543">En az istek gövdesi veri hızı</span><span class="sxs-lookup"><span data-stu-id="70337-543">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="70337-544">Kestrel bayt/saniye cinsinden belirtilen fiyata ulaşan her saniye sonra denetler.</span><span class="sxs-lookup"><span data-stu-id="70337-544">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="70337-545">Oran en düşük değerin altına düşerse bağlantı zaman aşımına uğrar. Yetkisiz kullanım süresi, Kestrel 'in istemciye gönderme oranını en düşük süreye kadar artırabilme Bu süre boyunca fiyat denetlenmez.</span><span class="sxs-lookup"><span data-stu-id="70337-545">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="70337-546">Yetkisiz kullanım süresi, TCP yavaş başlatma nedeniyle başlangıçta verileri yavaş bir hızda gönderen bağlantıların bırakılmasını önlemeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="70337-546">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="70337-547">Varsayılan en düşük oran, 5 saniyelik bir yetkisiz kullanım süresi ile 240 bayt/saniye olur.</span><span class="sxs-lookup"><span data-stu-id="70337-547">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="70337-548">Yanıt için bir minimum oran da geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="70337-548">A minimum rate also applies to the response.</span></span> <span data-ttu-id="70337-549">İstek sınırını ayarlamaya yönelik kod ve yanıt sınırı, `RequestBody` `Response` özellik ve arabirim adlarında olduğu gibi aynı olur.</span><span class="sxs-lookup"><span data-stu-id="70337-549">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="70337-550">*Program.cs* içinde en düşük veri hızlarının nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="70337-550">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="70337-551">Ara yazılım içindeki istek başına düşen minimum hız sınırlarını geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="70337-551">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="70337-552">`HttpContext.Features`İstek çoğullama için protokol desteği nedeniyle, http/2 için bir istek başına hız sınırlarını değiştirmek, http/2 istekleri için, önceki örnekte başvurulan hiçbir oran özelliği mevcut değil.</span><span class="sxs-lookup"><span data-stu-id="70337-552">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="70337-553">`KestrelServerOptions.Limits`Http/1. x ve http/2 bağlantılarına hala uygulanan sunucu genelindeki hız sınırları.</span><span class="sxs-lookup"><span data-stu-id="70337-553">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="70337-554">İstek üst bilgileri zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="70337-554">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="70337-555">Sunucunun istek üst bilgilerini alması için harcadığı en uzun süreyi alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="70337-555">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="70337-556">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="70337-556">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="70337-557">Bağlantı başına en fazla akış</span><span class="sxs-lookup"><span data-stu-id="70337-557">Maximum streams per connection</span></span>

<span data-ttu-id="70337-558">`Http2.MaxStreamsPerConnection` HTTP/2 bağlantısı başına eşzamanlı istek akışı sayısını sınırlar.</span><span class="sxs-lookup"><span data-stu-id="70337-558">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="70337-559">Fazlalık akışlar reddedildi.</span><span class="sxs-lookup"><span data-stu-id="70337-559">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="70337-560">Varsayılan değer 100’dür.</span><span class="sxs-lookup"><span data-stu-id="70337-560">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="70337-561">Üst bilgi tablosu boyutu</span><span class="sxs-lookup"><span data-stu-id="70337-561">Header table size</span></span>

<span data-ttu-id="70337-562">HPACK kod çözücüsü HTTP/2 bağlantıları için HTTP üstbilgilerini açar.</span><span class="sxs-lookup"><span data-stu-id="70337-562">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="70337-563">`Http2.HeaderTableSize` HPACK kod çözücüsünün kullandığı üst bilgi sıkıştırma tablosunun boyutunu sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="70337-563">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="70337-564">Değer sekizli cinsinden sağlanır ve sıfırdan büyük olmalıdır (0).</span><span class="sxs-lookup"><span data-stu-id="70337-564">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="70337-565">Varsayılan değer 4096 ' dir.</span><span class="sxs-lookup"><span data-stu-id="70337-565">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="70337-566">En büyük çerçeve boyutu</span><span class="sxs-lookup"><span data-stu-id="70337-566">Maximum frame size</span></span>

<span data-ttu-id="70337-567">`Http2.MaxFrameSize` alacak HTTP/2 bağlantı çerçevesi yükünün en büyük boyutunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="70337-567">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="70337-568">Değer sekizli cinsinden sağlanır ve 2 ^ 14 (16.384) ile 2 ^ 24-1 (16.777.215) arasında olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-568">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="70337-569">Varsayılan değer 2 ^ 14 ' dir (16.384).</span><span class="sxs-lookup"><span data-stu-id="70337-569">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="70337-570">En fazla istek üst bilgi boyutu</span><span class="sxs-lookup"><span data-stu-id="70337-570">Maximum request header size</span></span>

<span data-ttu-id="70337-571">`Http2.MaxRequestHeaderFieldSize` istek üst bilgisi değerlerinin sekizlisi cinsinden izin verilen en büyük boyutu belirtir.</span><span class="sxs-lookup"><span data-stu-id="70337-571">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="70337-572">Bu sınır, hem sıkıştırılmış hem de sıkıştırılmamış temsillerinde birlikte hem ad hem de değer için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="70337-572">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="70337-573">Değer sıfırdan büyük (0) olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-573">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="70337-574">Varsayılan değer 8.192 ' dir.</span><span class="sxs-lookup"><span data-stu-id="70337-574">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="70337-575">İlk bağlantı pencere boyutu</span><span class="sxs-lookup"><span data-stu-id="70337-575">Initial connection window size</span></span>

<span data-ttu-id="70337-576">`Http2.InitialConnectionWindowSize` sunucu, bağlantı başına tüm istekler (akışlar) genelinde toplanan tek seferde sunucunun arabelleğe aldığı en fazla istek gövde verilerini bayt cinsinden gösterir.</span><span class="sxs-lookup"><span data-stu-id="70337-576">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="70337-577">İstekleri ile de sınırlıdır `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="70337-577">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="70337-578">Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-578">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="70337-579">Varsayılan değer 128 KB 'tır (131.072).</span><span class="sxs-lookup"><span data-stu-id="70337-579">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="70337-580">İlk akış pencere boyutu</span><span class="sxs-lookup"><span data-stu-id="70337-580">Initial stream window size</span></span>

<span data-ttu-id="70337-581">`Http2.InitialStreamWindowSize` sunucu, istek başına bir kez (Stream) arabelleğe alınan en fazla istek gövde verilerini bayt cinsinden gösterir.</span><span class="sxs-lookup"><span data-stu-id="70337-581">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="70337-582">İstekleri ile de sınırlıdır `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="70337-582">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="70337-583">Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-583">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="70337-584">Varsayılan değer 96 KB 'tır (98.304).</span><span class="sxs-lookup"><span data-stu-id="70337-584">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="70337-585">Zaman Uyumlu G/Ç</span><span class="sxs-lookup"><span data-stu-id="70337-585">Synchronous I/O</span></span>

<span data-ttu-id="70337-586"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> istek ve yanıt için zaman uyumlu g/ç 'ye izin verilip verilmediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="70337-586"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="70337-587">Varsayılan değer `true` .</span><span class="sxs-lookup"><span data-stu-id="70337-587">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="70337-588">Çok sayıda engelleme zaman uyumlu g/ç işlemi, iş parçacığı havuzuna yol açabilir, bu da uygulamanın yanıt vermemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="70337-588">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="70337-589">Yalnızca `AllowSynchronousIO` zaman uyumsuz g/ç desteklemeyen bir kitaplık kullanırken etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="70337-589">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="70337-590">Aşağıdaki örnek, zaman uyumlu g/ç 'yi sunar:</span><span class="sxs-lookup"><span data-stu-id="70337-590">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="70337-591">Diğer Kestrel seçenekleri ve limitleri hakkında daha fazla bilgi için bkz.:</span><span class="sxs-lookup"><span data-stu-id="70337-591">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="70337-592">Uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="70337-592">Endpoint configuration</span></span>

<span data-ttu-id="70337-593">Varsayılan olarak, ASP.NET Core bağlar:</span><span class="sxs-lookup"><span data-stu-id="70337-593">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="70337-594">`https://localhost:5001` (bir yerel geliştirme sertifikası varsa)</span><span class="sxs-lookup"><span data-stu-id="70337-594">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="70337-595">Kullanarak URL 'Leri belirtin:</span><span class="sxs-lookup"><span data-stu-id="70337-595">Specify URLs using the:</span></span>

* <span data-ttu-id="70337-596">`ASPNETCORE_URLS` ortam değişkeni.</span><span class="sxs-lookup"><span data-stu-id="70337-596">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="70337-597">`--urls` komut satırı bağımsız değişkeni.</span><span class="sxs-lookup"><span data-stu-id="70337-597">`--urls` command-line argument.</span></span>
* <span data-ttu-id="70337-598">`urls` Ana bilgisayar yapılandırma anahtarı.</span><span class="sxs-lookup"><span data-stu-id="70337-598">`urls` host configuration key.</span></span>
* <span data-ttu-id="70337-599">`UseUrls` genişletme yöntemi.</span><span class="sxs-lookup"><span data-stu-id="70337-599">`UseUrls` extension method.</span></span>

<span data-ttu-id="70337-600">Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS).</span><span class="sxs-lookup"><span data-stu-id="70337-600">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="70337-601">Değeri noktalı virgülle ayrılmış bir liste olarak yapılandırın (örneğin, `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="70337-601">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="70337-602">Bu yaklaşımlar hakkında daha fazla bilgi için [sunucu URL 'leri](xref:fundamentals/host/web-host#server-urls) ve [geçersiz kılma yapılandırması](xref:fundamentals/host/web-host#override-configuration)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="70337-602">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="70337-603">Geliştirme sertifikası oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="70337-603">A development certificate is created:</span></span>

* <span data-ttu-id="70337-604">[.NET Core SDK](/dotnet/core/sdk) yüklendiği zaman.</span><span class="sxs-lookup"><span data-stu-id="70337-604">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="70337-605">[Geliştirme-CERT aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="70337-605">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="70337-606">Bazı tarayıcılarda yerel geliştirme sertifikasına güvenmek için açık izin verilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="70337-606">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="70337-607">Proje şablonları, uygulamaları HTTPS üzerinde varsayılan olarak çalışacak şekilde yapılandırır ve [https yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.</span><span class="sxs-lookup"><span data-stu-id="70337-607">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="70337-608"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> URL öneklerini ve bağlantı noktalarını Kestrel için yapılandırmak üzere üzerinde çağrı veya Yöntemler.</span><span class="sxs-lookup"><span data-stu-id="70337-608">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="70337-609">`UseUrls`, `--urls` komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı ve `ASPNETCORE_URLS` ortam değişkeni de çalışır, ancak bu bölümün ilerleyen kısımlarında belirtilen sınırlamalara sahiptir (https uç noktası yapılandırması için varsayılan sertifika kullanılabilir olmalıdır).</span><span class="sxs-lookup"><span data-stu-id="70337-609">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="70337-610">`KestrelServerOptions` yapılandırmada</span><span class="sxs-lookup"><span data-stu-id="70337-610">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="70337-611">ConfigureEndpointDefaults Varsayılanları (eylem \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="70337-611">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="70337-612">`Action`Belirtilen her bir uç nokta için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="70337-612">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="70337-613">`ConfigureEndpointDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="70337-613">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="70337-614">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="70337-614">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="70337-615">ConfigureHttpsDefaults (eylem \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="70337-615">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="70337-616">`Action`Her HTTPS uç noktası için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="70337-616">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="70337-617">`ConfigureHttpsDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="70337-617">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="70337-618">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="70337-618">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="70337-619">Yapılandırma (Iconation)</span><span class="sxs-lookup"><span data-stu-id="70337-619">Configure(IConfiguration)</span></span>

<span data-ttu-id="70337-620">Bir as girişi alan Kestrel ayarlamak için bir yapılandırma yükleyicisi oluşturur <xref:Microsoft.Extensions.Configuration.IConfiguration> .</span><span class="sxs-lookup"><span data-stu-id="70337-620">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="70337-621">Yapılandırma, Kestrel için yapılandırma bölümünün kapsamına alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-621">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="70337-622">ListenOptions. UseHttps</span><span class="sxs-lookup"><span data-stu-id="70337-622">ListenOptions.UseHttps</span></span>

<span data-ttu-id="70337-623">Kestrel 'i HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="70337-623">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="70337-624">`ListenOptions.UseHttps` uzantılardan</span><span class="sxs-lookup"><span data-stu-id="70337-624">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="70337-625">`UseHttps`: Kestrel 'i varsayılan sertifikayla HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="70337-625">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="70337-626">Varsayılan sertifika yapılandırılmamışsa bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="70337-626">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="70337-627">`ListenOptions.UseHttps` parametrelere</span><span class="sxs-lookup"><span data-stu-id="70337-627">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="70337-628">`filename` , uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yolu ve dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-628">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="70337-629">`password` X. 509.440 sertifika verilerine erişmek için parola gereklidir.</span><span class="sxs-lookup"><span data-stu-id="70337-629">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="70337-630">`configureOptions` , öğesini `Action` yapılandırmak için kullanılır `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="70337-630">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="70337-631">Döndürür `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="70337-631">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="70337-632">`storeName` , sertifikanın yükleneceği sertifika deposudur.</span><span class="sxs-lookup"><span data-stu-id="70337-632">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="70337-633">`subject` , sertifika için konu adıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-633">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="70337-634">`allowInvalid` geçersiz sertifikaların, otomatik olarak imzalanan sertifikalar gibi göz önünde bulundurulmayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="70337-634">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="70337-635">`location` , sertifikanın yükleneceği mağaza konumudur.</span><span class="sxs-lookup"><span data-stu-id="70337-635">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="70337-636">`serverCertificate` , X. 509.440 sertifikasıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-636">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="70337-637">Üretimde HTTPS 'nin açıkça yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="70337-637">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="70337-638">En azından, varsayılan bir sertifika sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-638">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="70337-639">Daha sonra açıklanan desteklenen yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="70337-639">Supported configurations described next:</span></span>

* <span data-ttu-id="70337-640">Yapılandırma yok</span><span class="sxs-lookup"><span data-stu-id="70337-640">No configuration</span></span>
* <span data-ttu-id="70337-641">Varsayılan sertifikayı yapılandırmadan Değiştir</span><span class="sxs-lookup"><span data-stu-id="70337-641">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="70337-642">Koddaki varsayılanları değiştirme</span><span class="sxs-lookup"><span data-stu-id="70337-642">Change the defaults in code</span></span>

<span data-ttu-id="70337-643">*Yapılandırma yok*</span><span class="sxs-lookup"><span data-stu-id="70337-643">*No configuration*</span></span>

<span data-ttu-id="70337-644">Kestrel tarihinde dinler `http://localhost:5000` ve `https://localhost:5001` (varsayılan bir sertifika varsa).</span><span class="sxs-lookup"><span data-stu-id="70337-644">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="70337-645">*Varsayılan sertifikayı yapılandırmadan Değiştir*</span><span class="sxs-lookup"><span data-stu-id="70337-645">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="70337-646">`CreateDefaultBuilder``Configure(context.Configuration.GetSection("Kestrel"))`Kestrel yapılandırmasını yüklemek için varsayılan olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="70337-646">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="70337-647">Varsayılan bir HTTPS uygulama ayarları yapılandırma şeması Kestrel için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-647">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="70337-648">Disk üzerindeki bir dosyadan ya da bir sertifika deposundan kullanılacak URL 'Ler ve Sertifikalar dahil olmak üzere birden çok uç nokta yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="70337-648">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="70337-649">Aşağıdaki *appsettings.json* örnekte:</span><span class="sxs-lookup"><span data-stu-id="70337-649">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="70337-650">Geçersiz sertifikaların kullanılmasına izin vermek için **Allowwınvalid** `true` ' i ayarlayın (örneğin, otomatik olarak imzalanan sertifikalar).</span><span class="sxs-lookup"><span data-stu-id="70337-650">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="70337-651">Bir sertifika belirtmeyen herhangi bir HTTPS uç noktası (aşağıdaki örnekte bulunan **httpsdefaultcert** ), **Sertifikalar** > **varsayılan** veya geliştirme sertifikası altında tanımlanan sertifikaya geri döner.</span><span class="sxs-lookup"><span data-stu-id="70337-651">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="70337-652">Herhangi bir sertifika düğümü için **yol** ve **parola** kullanmanın alternatifi sertifika deposu alanlarını kullanarak sertifikayı belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="70337-652">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="70337-653">Örneğin, **sertifika**  >  **varsayılan** sertifikası şu şekilde belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="70337-653">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="70337-654">Şema notları:</span><span class="sxs-lookup"><span data-stu-id="70337-654">Schema notes:</span></span>

* <span data-ttu-id="70337-655">Uç nokta adları büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-655">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="70337-656">Örneğin, `HTTPS` ve `Https` geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="70337-656">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="70337-657">`Url`Her uç nokta için parametresi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="70337-657">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="70337-658">Bu parametrenin biçimi, en üst düzey `Urls` yapılandırma parametresiyle aynıdır, ancak tek bir değerle sınırlı olur.</span><span class="sxs-lookup"><span data-stu-id="70337-658">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="70337-659">Bu uç noktalar, üst düzey yapılandırmada tanımlananlar yerine `Urls` bunlara ekleme yerine bunların yerini alır.</span><span class="sxs-lookup"><span data-stu-id="70337-659">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="70337-660">Kullanılarak kodda tanımlanan uç noktalar `Listen` yapılandırma bölümünde tanımlanan uç noktalar ile birikimlidir.</span><span class="sxs-lookup"><span data-stu-id="70337-660">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="70337-661">Bu `Certificate` bölüm isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-661">The `Certificate` section is optional.</span></span> <span data-ttu-id="70337-662">`Certificate`Bölüm belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır.</span><span class="sxs-lookup"><span data-stu-id="70337-662">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="70337-663">Kullanılabilir varsayılan değer yoksa, sunucu bir özel durum oluşturur ve başlayamaz.</span><span class="sxs-lookup"><span data-stu-id="70337-663">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="70337-664">Bu `Certificate` bölüm hem **yol** &ndash; **parolasını** hem de **Konu** &ndash; **deposu** sertifikalarını destekler.</span><span class="sxs-lookup"><span data-stu-id="70337-664">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="70337-665">Herhangi bir sayıda uç nokta, bağlantı noktası çakışmalarına neden olmadıkları sürece bu şekilde tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-665">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="70337-666">`options.Configure(context.Configuration.GetSection("{SECTION}"))` yapılandırılmış bir `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` uç noktanın ayarlarını tamamlamak için kullanılabilecek bir yöntemi olan bir döndürür:</span><span class="sxs-lookup"><span data-stu-id="70337-666">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="70337-667">`KestrelServerOptions.ConfigurationLoader` , tarafından sağlana gibi var olan yükleyicisindeki yinelemeye devam etmek için doğrudan erişilebilir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="70337-667">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="70337-668">Her uç noktanın yapılandırma bölümü, `Endpoint` özel ayarların okunabilmesi için yöntemindeki seçeneklerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-668">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="70337-669">Birden çok yapılandırma, `options.Configure(context.Configuration.GetSection("{SECTION}"))` başka bir bölümle yeniden çağırarak yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="70337-669">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="70337-670">Önceki örneklerde açıkça çağrılmadığı takdirde, yalnızca son yapılandırma kullanılır `Load` .</span><span class="sxs-lookup"><span data-stu-id="70337-670">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="70337-671">Metapackage, `Load` varsayılan yapılandırma bölümünün değiştirilmesini sağlayacak şekilde çağırmıyor.</span><span class="sxs-lookup"><span data-stu-id="70337-671">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="70337-672">`KestrelConfigurationLoader``Listen`API ailesini `KestrelServerOptions` `Endpoint` aşırı yükleme olarak yansıtır, bu nedenle kod ve yapılandırma uç noktaları aynı yerde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-672">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="70337-673">Bu aşırı yüklemeler adları kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları kullanır.</span><span class="sxs-lookup"><span data-stu-id="70337-673">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="70337-674">*Koddaki varsayılanları değiştirme*</span><span class="sxs-lookup"><span data-stu-id="70337-674">*Change the defaults in code*</span></span>

<span data-ttu-id="70337-675">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` `ListenOptions` `HttpsConnectionAdapterOptions` , önceki senaryoda belirtilen varsayılan sertifikayı geçersiz kılma dahil, ve için varsayılan ayarları değiştirmek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-675">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="70337-676">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-676">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="70337-677">*SNı için Kestrel desteği*</span><span class="sxs-lookup"><span data-stu-id="70337-677">*Kestrel support for SNI*</span></span>

<span data-ttu-id="70337-678">[Sunucu adı belirtme (SNı)](https://tools.ietf.org/html/rfc6066#section-3) , aynı IP adresi ve bağlantı noktası üzerinde birden fazla etki alanını barındırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-678">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="70337-679">SNı 'nin çalışması için, istemci, TLS el sıkışması sırasında güvenli oturum ana bilgisayar adını, sunucunun doğru sertifikayı sağlayabilmesi için gönderir.</span><span class="sxs-lookup"><span data-stu-id="70337-679">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="70337-680">İstemci, TLS anlaşmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için bulunan sertifikayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="70337-680">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="70337-681">Kestrel, geri çağırma aracılığıyla SNı destekler `ServerCertificateSelector` .</span><span class="sxs-lookup"><span data-stu-id="70337-681">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="70337-682">Geri çağırma, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesini sağlamak için bağlantı başına bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="70337-682">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="70337-683">SNı desteği şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="70337-683">SNI support requires:</span></span>

* <span data-ttu-id="70337-684">Hedef Framework `netcoreapp2.1` veya sonraki sürümlerde çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="70337-684">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="70337-685">`net461`Veya sonraki sürümlerde, geri çağırma çağrılır, ancak `name` her zaman olur `null` .</span><span class="sxs-lookup"><span data-stu-id="70337-685">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="70337-686">`name`Ayrıca, `null` ISTEMCI, TLS el sıkışmasının ana bilgisayar adı parametresini sağlamıyorsa de olur.</span><span class="sxs-lookup"><span data-stu-id="70337-686">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="70337-687">Tüm Web siteleri aynı Kestrel örneğinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="70337-687">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="70337-688">Kestrel, bir IP adresi ve bağlantı noktasının bir ters proxy olmadan birden çok örnek arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="70337-688">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="70337-689">Bağlantı günlüğü</span><span class="sxs-lookup"><span data-stu-id="70337-689">Connection logging</span></span>

<span data-ttu-id="70337-690"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>Bir bağlantıda bayt düzeyinde iletişim Için hata ayıklama düzeyi günlüklerini yayma çağrısı.</span><span class="sxs-lookup"><span data-stu-id="70337-690">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="70337-691">Bağlantı günlüğü, TLS şifreleme ve proxy 'nin arkasındaki gibi alt düzey iletişimde sorunları gidermeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="70337-691">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="70337-692">`UseConnectionLogging`Daha önce yerleştirilmişse `UseHttps` , şifrelenmiş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="70337-692">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="70337-693">`UseConnectionLogging`Öğesinden sonra yerleştirilmişse `UseHttps` , şifresi çözülmüş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="70337-693">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="70337-694">TCP yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="70337-694">Bind to a TCP socket</span></span>

<span data-ttu-id="70337-695"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Yöntemi BIR TCP yuvasına bağlanır ve bir seçenek lambda X. 509.440 sertifika yapılandırmasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="70337-695">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="70337-696">Örnek, HTTPS 'yi ile bir uç nokta için yapılandırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="70337-696">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="70337-697">Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak üzere aynı API 'yi kullanın.</span><span class="sxs-lookup"><span data-stu-id="70337-697">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="70337-698">UNIX yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="70337-698">Bind to a Unix socket</span></span>

<span data-ttu-id="70337-699"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>Aşağıdaki örnekte gösterildiği gibi NGINX ile gelişmiş performans için ile birlikte bir UNIX yuvası dinleyin:</span><span class="sxs-lookup"><span data-stu-id="70337-699">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="70337-700">NGINX confiuguration dosyasında, `server`  >  `location`  >  `proxy_pass` girdisini olarak ayarlayın `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="70337-700">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="70337-701">`{KESTREL SOCKET}` , için belirtilen yuvanın adıdır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (örneğin, `kestrel-test.sock` Önceki örnekte).</span><span class="sxs-lookup"><span data-stu-id="70337-701">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="70337-702">Yuvanın NGINX tarafından yazılabilir olduğundan emin olun (örneğin, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="70337-702">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="70337-703">Bağlantı noktası 0</span><span class="sxs-lookup"><span data-stu-id="70337-703">Port 0</span></span>

<span data-ttu-id="70337-704">Bağlantı noktası numarası `0` belirtildiğinde, Kestrel dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır.</span><span class="sxs-lookup"><span data-stu-id="70337-704">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="70337-705">Aşağıdaki örnek, çalışma zamanında Kestrel gerçekte hangi bağlantı noktasının gerçekten bağlandığını nasıl belirleyeceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="70337-705">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="70337-706">Uygulama çalıştırıldığında konsol penceresi çıkışı, uygulamanın erişilebileceği dinamik bağlantı noktasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="70337-706">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="70337-707">Sınırlamalar</span><span class="sxs-lookup"><span data-stu-id="70337-707">Limitations</span></span>

<span data-ttu-id="70337-708">Aşağıdaki yaklaşımlar ile uç noktaları yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="70337-708">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="70337-709">`--urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="70337-709">`--urls` command-line argument</span></span>
* <span data-ttu-id="70337-710">`urls` Ana bilgisayar yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="70337-710">`urls` host configuration key</span></span>
* <span data-ttu-id="70337-711">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="70337-711">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="70337-712">Bu yöntemler, kodun Kestrel dışındaki sunucularla çalışmasını sağlamak için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-712">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="70337-713">Ancak, aşağıdaki sınırlamalara dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="70337-713">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="70337-714">HTTPS uç noktası yapılandırmasında varsayılan bir sertifika sağlanmadığı sürece HTTPS bu yaklaşımlar ile kullanılamaz (örneğin, `KestrelServerOptions` Bu konunun önceki kısımlarında gösterildiği gibi yapılandırma veya yapılandırma dosyası kullanılıyor).</span><span class="sxs-lookup"><span data-stu-id="70337-714">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="70337-715">Hem hem de `Listen` `UseUrls` yaklaşımları aynı anda kullanıldığında, uç noktalar `Listen` `UseUrls` uç noktaları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="70337-715">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="70337-716">IIS uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="70337-716">IIS endpoint configuration</span></span>

<span data-ttu-id="70337-717">IIS kullanırken, IIS geçersiz kılma bağlamaları için URL bağlamaları veya ya da tarafından ayarlanır `Listen` `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="70337-717">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="70337-718">Daha fazla bilgi için [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="70337-718">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="70337-719">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="70337-719">ListenOptions.Protocols</span></span>

<span data-ttu-id="70337-720">`Protocols`Özelliği, `HttpProtocols` bir bağlantı uç noktasında veya sunucu IÇIN etkin HTTP protokollerini () belirler.</span><span class="sxs-lookup"><span data-stu-id="70337-720">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="70337-721">`Protocols`Sabit listesinden özelliğe bir değer atayın `HttpProtocols` .</span><span class="sxs-lookup"><span data-stu-id="70337-721">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="70337-722">`HttpProtocols` sabit listesi değeri</span><span class="sxs-lookup"><span data-stu-id="70337-722">`HttpProtocols` enum value</span></span> | <span data-ttu-id="70337-723">Bağlantı protokolü izin verildi</span><span class="sxs-lookup"><span data-stu-id="70337-723">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="70337-724">Yalnızca HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="70337-724">HTTP/1.1 only.</span></span> <span data-ttu-id="70337-725">, TLS olmadan veya ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-725">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="70337-726">Yalnızca HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="70337-726">HTTP/2 only.</span></span> <span data-ttu-id="70337-727">Yalnızca istemci [önceki bir bilgi modunu](https://tools.ietf.org/html/rfc7540#section-3.4)DESTEKLIYORSA, TLS olmadan kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-727">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="70337-728">HTTP/1.1 ve HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="70337-728">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="70337-729">HTTP/2 bir TLS ve [uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı gerektirir; Aksi takdirde, bağlantı varsayılan olarak HTTP/1.1 ' dir.</span><span class="sxs-lookup"><span data-stu-id="70337-729">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="70337-730">Varsayılan protokol HTTP/1.1 ' dir.</span><span class="sxs-lookup"><span data-stu-id="70337-730">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="70337-731">HTTP/2 için TLS kısıtlamaları:</span><span class="sxs-lookup"><span data-stu-id="70337-731">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="70337-732">TLS sürüm 1,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="70337-732">TLS version 1.2 or later</span></span>
* <span data-ttu-id="70337-733">Yeniden anlaşma devre dışı</span><span class="sxs-lookup"><span data-stu-id="70337-733">Renegotiation disabled</span></span>
* <span data-ttu-id="70337-734">Sıkıştırma devre dışı</span><span class="sxs-lookup"><span data-stu-id="70337-734">Compression disabled</span></span>
* <span data-ttu-id="70337-735">En az kısa ömürlü anahtar değişim boyutları:</span><span class="sxs-lookup"><span data-stu-id="70337-735">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="70337-736">Eliptik Eğri Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bit minimum</span><span class="sxs-lookup"><span data-stu-id="70337-736">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="70337-737">Sınırlı alan Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; : 2048 bit minimum</span><span class="sxs-lookup"><span data-stu-id="70337-737">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="70337-738">Şifre paketi engellenmedi</span><span class="sxs-lookup"><span data-stu-id="70337-738">Cipher suite not blocked</span></span>

<span data-ttu-id="70337-739">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; P-256 eliptik eğrisi &lbrack; `FIPS186` &rbrack; Varsayılan olarak desteklenir.</span><span class="sxs-lookup"><span data-stu-id="70337-739">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="70337-740">Aşağıdaki örnek, 8000 numaralı bağlantı noktasında HTTP/1.1 ve HTTP/2 bağlantılarına izin verir.</span><span class="sxs-lookup"><span data-stu-id="70337-740">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="70337-741">Bağlantılar, sağlanan bir sertifikayla TLS ile güvenlidir:</span><span class="sxs-lookup"><span data-stu-id="70337-741">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="70337-742">İsteğe bağlı olarak, `IConnectionAdapter` belirli şifrelemeler için bağlantı BAŞıNA TLS el sıkışmaları filtrelemek üzere bir uygulama oluşturun:</span><span class="sxs-lookup"><span data-stu-id="70337-742">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="70337-743">*Protokolü yapılandırmadan ayarla*</span><span class="sxs-lookup"><span data-stu-id="70337-743">*Set the protocol from configuration*</span></span>

<span data-ttu-id="70337-744"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>`serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`Kestrel yapılandırmasını yüklemek için varsayılan olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="70337-744"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="70337-745">Aşağıdaki *appsettings.json* örnekte, Kestrel 'in tüm uç noktaları için varsayılan bir bağlantı protokolü (http/1.1 ve http/2) oluşturulmuştur:</span><span class="sxs-lookup"><span data-stu-id="70337-745">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="70337-746">Aşağıdaki yapılandırma dosyası örneği, belirli bir uç nokta için bağlantı protokolü oluşturur:</span><span class="sxs-lookup"><span data-stu-id="70337-746">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="70337-747">Yapılandırma tarafından ayarlanan kod geçersiz kılma değerlerinde belirtilen protokoller.</span><span class="sxs-lookup"><span data-stu-id="70337-747">Protocols specified in code override values set by configuration.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="70337-748">Libuv aktarım yapılandırması</span><span class="sxs-lookup"><span data-stu-id="70337-748">Libuv transport configuration</span></span>

<span data-ttu-id="70337-749">ASP.NET Core 2,1 sürümü ile Kestrel 'in varsayılan taşıması artık libuv ' d i temel değildir ancak bunun yerine yönetilen yuvaları temel alır.</span><span class="sxs-lookup"><span data-stu-id="70337-749">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="70337-750">Bu, çağrıyı yapan ve aşağıdaki paketlerden birine bağlı olan ASP.NET Core 2,0 2,1 uygulamalarının önemli bir değişikliği olur <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="70337-750">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="70337-751">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (doğrudan paket başvurusu)</span><span class="sxs-lookup"><span data-stu-id="70337-751">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="70337-752">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="70337-752">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="70337-753">Libuv kullanımını gerektiren projeler için:</span><span class="sxs-lookup"><span data-stu-id="70337-753">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="70337-754">Uygulamanın proje dosyasına [Microsoft. AspNetCore. Server. Kestrel. Transport. libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) paketi için bir bağımlılık ekleyin:</span><span class="sxs-lookup"><span data-stu-id="70337-754">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="70337-755">Çağrı <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="70337-755">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="70337-756">URL önekleri</span><span class="sxs-lookup"><span data-stu-id="70337-756">URL prefixes</span></span>

<span data-ttu-id="70337-757">`UseUrls`, `--urls` Komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı veya `ASPNETCORE_URLS` ortam değişkeni kullanılırken, URL önekleri aşağıdaki biçimlerden birinde olabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-757">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="70337-758">Yalnızca HTTP URL ön ekleri geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="70337-758">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="70337-759">Kestrel, kullanılarak URL bağlamaları yapılandırılırken HTTPS 'YI desteklemez `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="70337-759">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="70337-760">Bağlantı noktası numarası olan IPv4 adresi</span><span class="sxs-lookup"><span data-stu-id="70337-760">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="70337-761">`0.0.0.0` Tüm IPv4 adreslerine bağlanan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="70337-761">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="70337-762">Bağlantı noktası numarasına sahip IPv6 adresi</span><span class="sxs-lookup"><span data-stu-id="70337-762">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="70337-763">`[::]` , IPv4 'un IPv6 eşdeğeridir `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="70337-763">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="70337-764">Bağlantı noktası numarası olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="70337-764">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="70337-765">, Ve ana bilgisayar adları `*` `+` özel değildir.</span><span class="sxs-lookup"><span data-stu-id="70337-765">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="70337-766">Geçerli bir IP adresi olarak tanınmayan veya `localhost` tüm IPv4 ve IPv6 IP 'lerine bağlanan her şey.</span><span class="sxs-lookup"><span data-stu-id="70337-766">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="70337-767">Farklı ana bilgisayar adlarını aynı bağlantı noktasında farklı ASP.NET Core uygulamalarına bağlamak için, [HTTP.sys](xref:fundamentals/servers/httpsys) veya IIS, NGINX veya Apache gibi bir ters proxy sunucusunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="70337-767">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="70337-768">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="70337-768">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="70337-769">Port numarası `localhost` veya bağlantı noktası numarası ile geri döngü IP 'si olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="70337-769">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="70337-770">Belirtildiğinde `localhost` , Kestrel hem IPv4 hem de IPv6 geri döngü arabirimlerini bağlamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="70337-770">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="70337-771">İstenen bağlantı noktası, herhangi bir geri döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="70337-771">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="70337-772">Herhangi bir geri döngü arabirimi başka bir nedenle kullanılamıyorsa (genellikle IPv6 desteklenmediği için), Kestrel bir uyarı kaydeder.</span><span class="sxs-lookup"><span data-stu-id="70337-772">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="70337-773">Konak filtreleme</span><span class="sxs-lookup"><span data-stu-id="70337-773">Host filtering</span></span>

<span data-ttu-id="70337-774">Kestrel gibi önekleri temel alarak yapılandırmayı desteklese `http://example.com:5000` de, Kestrel büyük ölçüde ana bilgisayar adını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="70337-774">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="70337-775">Ana bilgisayar `localhost` , geri döngü adreslerine bağlama için kullanılan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="70337-775">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="70337-776">Açık IP adresi dışındaki tüm ana bilgisayar tüm genel IP adreslerine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="70337-776">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="70337-777">`Host` Üstbilgiler doğrulanmadı.</span><span class="sxs-lookup"><span data-stu-id="70337-777">`Host` headers aren't validated.</span></span>

<span data-ttu-id="70337-778">Geçici bir çözüm olarak, ana bilgisayar filtreleme ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="70337-778">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="70337-779">Ana bilgisayar filtreleme ara yazılımı, [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 veya 2,2) ' de yer alan [Microsoft. Aspnetcore. hostfiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="70337-779">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="70337-780">Ara yazılım tarafından eklenir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ve şunları çağırır <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="70337-780">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="70337-781">Ana bilgisayar filtreleme ara yazılımı varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-781">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="70337-782">Ara yazılımı etkinleştirmek için `AllowedHosts` appSettings 'de bir anahtar tanımlayın *appsettings.json* / *. \<EnvironmentName> . JSON*.</span><span class="sxs-lookup"><span data-stu-id="70337-782">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="70337-783">Değer, bağlantı noktası numaraları olmayan ana bilgisayar adlarının noktalı virgülle ayrılmış listesidir:</span><span class="sxs-lookup"><span data-stu-id="70337-783">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="70337-784">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="70337-784">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="70337-785">[Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer) da bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçenek içerir.</span><span class="sxs-lookup"><span data-stu-id="70337-785">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="70337-786">İletilen üstbilgiler ara yazılımı ve ana bilgisayar filtreleme ara yazılımı, farklı senaryolar için benzer işlevlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="70337-786">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="70337-787">`AllowedHosts`Iletilen üstbilgiler ara yazılımı ile, `Host` istekler ters bir ara sunucu veya yük dengeleyici ile iletilirken üst bilgi korunurken, bu işlem için uygun bir ayar vardır.</span><span class="sxs-lookup"><span data-stu-id="70337-787">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="70337-788">`AllowedHosts`Ana bilgisayar filtreleme ara yazılımı ile ayarlama, Kestrel herkese açık bir uç sunucu olarak veya `Host` üst bilgi doğrudan iletildiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="70337-788">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="70337-789">Iletilen üstbilgiler ara yazılımı hakkında daha fazla bilgi için bkz <xref:host-and-deploy/proxy-load-balancer> ..</span><span class="sxs-lookup"><span data-stu-id="70337-789">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="70337-790">Kestrel, ASP.NET Core için platformlar arası [Web sunucusudur](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="70337-790">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="70337-791">Kestrel, ASP.NET Core proje şablonlarında varsayılan olarak bulunan Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="70337-791">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="70337-792">Kestrel aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="70337-792">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="70337-793">HTTPS</span><span class="sxs-lookup"><span data-stu-id="70337-793">HTTPS</span></span>
* <span data-ttu-id="70337-794">[WebSockets](https://github.com/aspnet/websockets) 'i etkinleştirmek için kullanılan donuk yükseltme</span><span class="sxs-lookup"><span data-stu-id="70337-794">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="70337-795">NGINX 'in arkasında yüksek performans için UNIX Yuvaları</span><span class="sxs-lookup"><span data-stu-id="70337-795">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="70337-796">Kestrel, .NET Core 'un desteklediği tüm platformlarda ve sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="70337-796">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="70337-797">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="70337-797">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="70337-798">Ters ara sunucu ile Kestrel ne zaman kullanılır?</span><span class="sxs-lookup"><span data-stu-id="70337-798">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="70337-799">Kestrel, kendisi veya [Internet Information Services (IIS)](https://www.iis.net/), [NGINX](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi bir *ters ara sunucu* ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-799">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="70337-800">Ters proxy sunucusu, ağdan gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="70337-800">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="70337-801">Sınır (Internet 'e yönelik) Web sunucusu olarak kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="70337-801">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel, ters proxy sunucusu olmadan doğrudan Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="70337-803">Ters Proxy yapılandırmasında kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="70337-803">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel, IIS, NGINX veya Apache gibi bir ters ara sunucu üzerinden Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="70337-805">İki yapılandırma de, ters ara sunucu sunucusuyla veya olmadan, desteklenen bir barındırma yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="70337-805">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="70337-806">Ters proxy sunucusu olmayan bir uç sunucu olarak kullanılan Kestrel, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="70337-806">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="70337-807">Kestrel bir bağlantı noktasını dinlemek üzere yapılandırıldığında, Kestrel isteklerin üst bilgilerinden bağımsız olarak bu bağlantı noktası için tüm trafiği işler `Host` .</span><span class="sxs-lookup"><span data-stu-id="70337-807">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="70337-808">Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel 'e iletme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="70337-808">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="70337-809">Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucu kullanılması iyi bir seçim olabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-809">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="70337-810">Ters proxy:</span><span class="sxs-lookup"><span data-stu-id="70337-810">A reverse proxy:</span></span>

* <span data-ttu-id="70337-811">, Barındırdığı uygulamaların açığa çıkarılan genel yüzey alanını sınırlayabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-811">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="70337-812">Ek bir yapılandırma ve savunma katmanı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="70337-812">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="70337-813">, Mevcut altyapıyla daha iyi tümleşebilir.</span><span class="sxs-lookup"><span data-stu-id="70337-813">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="70337-814">Yük dengelemeyi ve güvenli iletişim (HTTPS) yapılandırmasını kolaylaştırın.</span><span class="sxs-lookup"><span data-stu-id="70337-814">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="70337-815">Yalnızca ters proxy sunucusu bir X. 509.440 sertifikası gerektirir ve bu sunucu, düz HTTP kullanarak, iç ağdaki uygulamanın sunucularıyla iletişim kurabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-815">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="70337-816">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="70337-816">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="70337-817">ASP.NET Core uygulamalarında Kestrel kullanma</span><span class="sxs-lookup"><span data-stu-id="70337-817">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="70337-818">Microsoft. [AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) paketi [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="70337-818">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="70337-819">ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır.</span><span class="sxs-lookup"><span data-stu-id="70337-819">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="70337-820">*Program.cs*' de, şablon kodu çağırır <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ve bu da <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> arka planda çağrı yapılır.</span><span class="sxs-lookup"><span data-stu-id="70337-820">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="70337-821">Çağrıldıktan sonra ek yapılandırma sağlamak için `CreateDefaultBuilder` şunu çağırın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="70337-821">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="70337-822">Ana bilgisayarı hakkında daha fazla bilgi için `CreateDefaultBuilder` , uygulamasının *konak ayarlama* bölümüne bakın <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="70337-822">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="70337-823">Kestrel seçenekleri</span><span class="sxs-lookup"><span data-stu-id="70337-823">Kestrel options</span></span>

<span data-ttu-id="70337-824">Kestrel Web sunucusu, Internet 'e yönelik dağıtımlarda özellikle yararlı olan kısıtlama yapılandırma seçeneklerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="70337-824">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="70337-825">Sınıfının özelliğinde kısıtlamaları ayarlayın <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> .</span><span class="sxs-lookup"><span data-stu-id="70337-825">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="70337-826">`Limits`Özelliği, sınıfının bir örneğini barındırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> .</span><span class="sxs-lookup"><span data-stu-id="70337-826">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="70337-827">Aşağıdaki örnekler <xref:Microsoft.AspNetCore.Server.Kestrel.Core> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="70337-827">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="70337-828">Aşağıdaki örneklerde C# kodunda yapılandırılan Kestrel seçenekleri de bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-828">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="70337-829">Örneğin, dosya yapılandırma sağlayıcısı Kestrel yapılandırmasını bir *appsettings.json* veya appSettings 'ten yükleyebilir *. {1} Environment}. JSON* dosyası:</span><span class="sxs-lookup"><span data-stu-id="70337-829">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="70337-830">Aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="70337-830">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="70337-831">Kestrel 'i yapılandırma `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="70337-831">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="70337-832">Sınıfına bir örneği `IConfiguration` ekleyin `Startup` .</span><span class="sxs-lookup"><span data-stu-id="70337-832">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="70337-833">Aşağıdaki örnek, eklenen yapılandırmanın özelliğe atandığını varsayar `Configuration` .</span><span class="sxs-lookup"><span data-stu-id="70337-833">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="70337-834">İçinde `Startup.ConfigureServices` , `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="70337-834">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="70337-835">Ana bilgisayarı oluştururken Kestrel yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="70337-835">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="70337-836">*Program.cs*' de, `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="70337-836">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="70337-837">Yukarıdaki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.</span><span class="sxs-lookup"><span data-stu-id="70337-837">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="70337-838">Etkin tut zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="70337-838">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="70337-839">[Etkin tutma zaman aşımını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="70337-839">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="70337-840">Varsayılan olarak 2 dakikadır.</span><span class="sxs-lookup"><span data-stu-id="70337-840">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="70337-841">İstemci bağlantıları üst sınırı</span><span class="sxs-lookup"><span data-stu-id="70337-841">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="70337-842">En fazla eş zamanlı açık TCP bağlantısı sayısı tüm uygulama için aşağıdaki kodla ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="70337-842">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="70337-843">HTTP veya HTTPS 'den başka bir protokole (örneğin, bir WebSockets isteğinde) yükseltilen bağlantılara yönelik ayrı bir sınır vardır.</span><span class="sxs-lookup"><span data-stu-id="70337-843">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="70337-844">Bir bağlantı yükseltildikten sonra, bu sınıra göre sayılmaz `MaxConcurrentConnections` .</span><span class="sxs-lookup"><span data-stu-id="70337-844">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="70337-845">En fazla bağlantı sayısı, varsayılan olarak sınırsız (null).</span><span class="sxs-lookup"><span data-stu-id="70337-845">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="70337-846">En büyük istek gövdesi boyutu</span><span class="sxs-lookup"><span data-stu-id="70337-846">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="70337-847">Varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu değer yaklaşık 28,6 MB 'tır.</span><span class="sxs-lookup"><span data-stu-id="70337-847">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="70337-848">ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yaklaşım, <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="70337-848">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="70337-849">Her istekte uygulama için kısıtlamanın nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="70337-849">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="70337-850">Ara yazılım içindeki belirli bir istek üzerindeki ayarı geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="70337-850">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="70337-851">Uygulama isteği okumaya başladıktan sonra bir istek üzerindeki sınırı yapılandırırsa, bir özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="70337-851">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="70337-852">`IsReadOnly`Özelliğin salt okuma durumunda olup olmadığını belirten bir özellik vardır. Bu, `MaxRequestBodySize` sınırı yapılandırmanın çok geç olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="70337-852">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="70337-853">Bir uygulama [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)arkasında [çalıştırıldığında](xref:host-and-deploy/iis/index#out-of-process-hosting-model) , IIS sınırı zaten ayarladığı için Kestrel 'nin istek gövdesi boyut sınırı devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="70337-853">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="70337-854">En az istek gövdesi veri hızı</span><span class="sxs-lookup"><span data-stu-id="70337-854">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="70337-855">Kestrel bayt/saniye cinsinden belirtilen fiyata ulaşan her saniye sonra denetler.</span><span class="sxs-lookup"><span data-stu-id="70337-855">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="70337-856">Oran en düşük değerin altına düşerse bağlantı zaman aşımına uğrar. Yetkisiz kullanım süresi, Kestrel 'in istemciye gönderme oranını en düşük süreye kadar artırabilme Bu süre boyunca fiyat denetlenmez.</span><span class="sxs-lookup"><span data-stu-id="70337-856">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="70337-857">Yetkisiz kullanım süresi, TCP yavaş başlatma nedeniyle başlangıçta verileri yavaş bir hızda gönderen bağlantıların bırakılmasını önlemeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="70337-857">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="70337-858">Varsayılan en düşük oran, 5 saniyelik bir yetkisiz kullanım süresi ile 240 bayt/saniye olur.</span><span class="sxs-lookup"><span data-stu-id="70337-858">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="70337-859">Yanıt için bir minimum oran da geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="70337-859">A minimum rate also applies to the response.</span></span> <span data-ttu-id="70337-860">İstek sınırını ayarlamaya yönelik kod ve yanıt sınırı, `RequestBody` `Response` özellik ve arabirim adlarında olduğu gibi aynı olur.</span><span class="sxs-lookup"><span data-stu-id="70337-860">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="70337-861">*Program.cs* içinde en düşük veri hızlarının nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="70337-861">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="70337-862">İstek üst bilgileri zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="70337-862">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="70337-863">Sunucunun istek üst bilgilerini alması için harcadığı en uzun süreyi alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="70337-863">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="70337-864">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="70337-864">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="70337-865">Zaman Uyumlu G/Ç</span><span class="sxs-lookup"><span data-stu-id="70337-865">Synchronous I/O</span></span>

<span data-ttu-id="70337-866"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> istek ve yanıt için zaman uyumlu g/ç 'ye izin verilip verilmediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="70337-866"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="70337-867">Varsayılan değer `true` .</span><span class="sxs-lookup"><span data-stu-id="70337-867">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="70337-868">Çok sayıda engelleme zaman uyumlu g/ç işlemi, iş parçacığı havuzuna yol açabilir, bu da uygulamanın yanıt vermemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="70337-868">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="70337-869">Yalnızca `AllowSynchronousIO` zaman uyumsuz g/ç desteklemeyen bir kitaplık kullanırken etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="70337-869">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="70337-870">Aşağıdaki örnek, zaman uyumlu g/ç 'yi devre dışı bırakır:</span><span class="sxs-lookup"><span data-stu-id="70337-870">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="70337-871">Diğer Kestrel seçenekleri ve limitleri hakkında daha fazla bilgi için bkz.:</span><span class="sxs-lookup"><span data-stu-id="70337-871">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="70337-872">Uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="70337-872">Endpoint configuration</span></span>

<span data-ttu-id="70337-873">Varsayılan olarak, ASP.NET Core bağlar:</span><span class="sxs-lookup"><span data-stu-id="70337-873">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="70337-874">`https://localhost:5001` (bir yerel geliştirme sertifikası varsa)</span><span class="sxs-lookup"><span data-stu-id="70337-874">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="70337-875">Kullanarak URL 'Leri belirtin:</span><span class="sxs-lookup"><span data-stu-id="70337-875">Specify URLs using the:</span></span>

* <span data-ttu-id="70337-876">`ASPNETCORE_URLS` ortam değişkeni.</span><span class="sxs-lookup"><span data-stu-id="70337-876">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="70337-877">`--urls` komut satırı bağımsız değişkeni.</span><span class="sxs-lookup"><span data-stu-id="70337-877">`--urls` command-line argument.</span></span>
* <span data-ttu-id="70337-878">`urls` Ana bilgisayar yapılandırma anahtarı.</span><span class="sxs-lookup"><span data-stu-id="70337-878">`urls` host configuration key.</span></span>
* <span data-ttu-id="70337-879">`UseUrls` genişletme yöntemi.</span><span class="sxs-lookup"><span data-stu-id="70337-879">`UseUrls` extension method.</span></span>

<span data-ttu-id="70337-880">Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS).</span><span class="sxs-lookup"><span data-stu-id="70337-880">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="70337-881">Değeri noktalı virgülle ayrılmış bir liste olarak yapılandırın (örneğin, `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="70337-881">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="70337-882">Bu yaklaşımlar hakkında daha fazla bilgi için [sunucu URL 'leri](xref:fundamentals/host/web-host#server-urls) ve [geçersiz kılma yapılandırması](xref:fundamentals/host/web-host#override-configuration)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="70337-882">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="70337-883">Geliştirme sertifikası oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="70337-883">A development certificate is created:</span></span>

* <span data-ttu-id="70337-884">[.NET Core SDK](/dotnet/core/sdk) yüklendiği zaman.</span><span class="sxs-lookup"><span data-stu-id="70337-884">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="70337-885">[Geliştirme-CERT aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="70337-885">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="70337-886">Bazı tarayıcılarda yerel geliştirme sertifikasına güvenmek için açık izin verilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="70337-886">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="70337-887">Proje şablonları, uygulamaları HTTPS üzerinde varsayılan olarak çalışacak şekilde yapılandırır ve [https yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.</span><span class="sxs-lookup"><span data-stu-id="70337-887">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="70337-888"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> URL öneklerini ve bağlantı noktalarını Kestrel için yapılandırmak üzere üzerinde çağrı veya Yöntemler.</span><span class="sxs-lookup"><span data-stu-id="70337-888">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="70337-889">`UseUrls`, `--urls` komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı ve `ASPNETCORE_URLS` ortam değişkeni de çalışır, ancak bu bölümün ilerleyen kısımlarında belirtilen sınırlamalara sahiptir (https uç noktası yapılandırması için varsayılan sertifika kullanılabilir olmalıdır).</span><span class="sxs-lookup"><span data-stu-id="70337-889">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="70337-890">`KestrelServerOptions` yapılandırmada</span><span class="sxs-lookup"><span data-stu-id="70337-890">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="70337-891">ConfigureEndpointDefaults Varsayılanları (eylem \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="70337-891">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="70337-892">`Action`Belirtilen her bir uç nokta için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="70337-892">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="70337-893">`ConfigureEndpointDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="70337-893">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="70337-894">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="70337-894">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="70337-895">ConfigureHttpsDefaults (eylem \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="70337-895">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="70337-896">`Action`Her HTTPS uç noktası için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="70337-896">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="70337-897">`ConfigureHttpsDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="70337-897">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="70337-898">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="70337-898">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="70337-899">Yapılandırma (Iconation)</span><span class="sxs-lookup"><span data-stu-id="70337-899">Configure(IConfiguration)</span></span>

<span data-ttu-id="70337-900">Bir as girişi alan Kestrel ayarlamak için bir yapılandırma yükleyicisi oluşturur <xref:Microsoft.Extensions.Configuration.IConfiguration> .</span><span class="sxs-lookup"><span data-stu-id="70337-900">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="70337-901">Yapılandırma, Kestrel için yapılandırma bölümünün kapsamına alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-901">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="70337-902">ListenOptions. UseHttps</span><span class="sxs-lookup"><span data-stu-id="70337-902">ListenOptions.UseHttps</span></span>

<span data-ttu-id="70337-903">Kestrel 'i HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="70337-903">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="70337-904">`ListenOptions.UseHttps` uzantılardan</span><span class="sxs-lookup"><span data-stu-id="70337-904">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="70337-905">`UseHttps`: Kestrel 'i varsayılan sertifikayla HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="70337-905">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="70337-906">Varsayılan sertifika yapılandırılmamışsa bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="70337-906">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="70337-907">`ListenOptions.UseHttps` parametrelere</span><span class="sxs-lookup"><span data-stu-id="70337-907">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="70337-908">`filename` , uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yolu ve dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-908">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="70337-909">`password` X. 509.440 sertifika verilerine erişmek için parola gereklidir.</span><span class="sxs-lookup"><span data-stu-id="70337-909">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="70337-910">`configureOptions` , öğesini `Action` yapılandırmak için kullanılır `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="70337-910">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="70337-911">Döndürür `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="70337-911">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="70337-912">`storeName` , sertifikanın yükleneceği sertifika deposudur.</span><span class="sxs-lookup"><span data-stu-id="70337-912">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="70337-913">`subject` , sertifika için konu adıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-913">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="70337-914">`allowInvalid` geçersiz sertifikaların, otomatik olarak imzalanan sertifikalar gibi göz önünde bulundurulmayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="70337-914">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="70337-915">`location` , sertifikanın yükleneceği mağaza konumudur.</span><span class="sxs-lookup"><span data-stu-id="70337-915">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="70337-916">`serverCertificate` , X. 509.440 sertifikasıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-916">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="70337-917">Üretimde HTTPS 'nin açıkça yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="70337-917">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="70337-918">En azından, varsayılan bir sertifika sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-918">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="70337-919">Daha sonra açıklanan desteklenen yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="70337-919">Supported configurations described next:</span></span>

* <span data-ttu-id="70337-920">Yapılandırma yok</span><span class="sxs-lookup"><span data-stu-id="70337-920">No configuration</span></span>
* <span data-ttu-id="70337-921">Varsayılan sertifikayı yapılandırmadan Değiştir</span><span class="sxs-lookup"><span data-stu-id="70337-921">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="70337-922">Koddaki varsayılanları değiştirme</span><span class="sxs-lookup"><span data-stu-id="70337-922">Change the defaults in code</span></span>

<span data-ttu-id="70337-923">*Yapılandırma yok*</span><span class="sxs-lookup"><span data-stu-id="70337-923">*No configuration*</span></span>

<span data-ttu-id="70337-924">Kestrel tarihinde dinler `http://localhost:5000` ve `https://localhost:5001` (varsayılan bir sertifika varsa).</span><span class="sxs-lookup"><span data-stu-id="70337-924">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="70337-925">*Varsayılan sertifikayı yapılandırmadan Değiştir*</span><span class="sxs-lookup"><span data-stu-id="70337-925">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="70337-926">`CreateDefaultBuilder``Configure(context.Configuration.GetSection("Kestrel"))`Kestrel yapılandırmasını yüklemek için varsayılan olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="70337-926">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="70337-927">Varsayılan bir HTTPS uygulama ayarları yapılandırma şeması Kestrel için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-927">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="70337-928">Disk üzerindeki bir dosyadan ya da bir sertifika deposundan kullanılacak URL 'Ler ve Sertifikalar dahil olmak üzere birden çok uç nokta yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="70337-928">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="70337-929">Aşağıdaki *appsettings.json* örnekte:</span><span class="sxs-lookup"><span data-stu-id="70337-929">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="70337-930">Geçersiz sertifikaların kullanılmasına izin vermek için **Allowwınvalid** `true` ' i ayarlayın (örneğin, otomatik olarak imzalanan sertifikalar).</span><span class="sxs-lookup"><span data-stu-id="70337-930">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="70337-931">Bir sertifika belirtmeyen herhangi bir HTTPS uç noktası (aşağıdaki örnekte bulunan **httpsdefaultcert** ), **Sertifikalar** > **varsayılan** veya geliştirme sertifikası altında tanımlanan sertifikaya geri döner.</span><span class="sxs-lookup"><span data-stu-id="70337-931">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="70337-932">Herhangi bir sertifika düğümü için **yol** ve **parola** kullanmanın alternatifi sertifika deposu alanlarını kullanarak sertifikayı belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="70337-932">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="70337-933">Örneğin, **sertifika**  >  **varsayılan** sertifikası şu şekilde belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="70337-933">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="70337-934">Şema notları:</span><span class="sxs-lookup"><span data-stu-id="70337-934">Schema notes:</span></span>

* <span data-ttu-id="70337-935">Uç nokta adları büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-935">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="70337-936">Örneğin, `HTTPS` ve `Https` geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="70337-936">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="70337-937">`Url`Her uç nokta için parametresi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="70337-937">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="70337-938">Bu parametrenin biçimi, en üst düzey `Urls` yapılandırma parametresiyle aynıdır, ancak tek bir değerle sınırlı olur.</span><span class="sxs-lookup"><span data-stu-id="70337-938">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="70337-939">Bu uç noktalar, üst düzey yapılandırmada tanımlananlar yerine `Urls` bunlara ekleme yerine bunların yerini alır.</span><span class="sxs-lookup"><span data-stu-id="70337-939">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="70337-940">Kullanılarak kodda tanımlanan uç noktalar `Listen` yapılandırma bölümünde tanımlanan uç noktalar ile birikimlidir.</span><span class="sxs-lookup"><span data-stu-id="70337-940">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="70337-941">Bu `Certificate` bölüm isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-941">The `Certificate` section is optional.</span></span> <span data-ttu-id="70337-942">`Certificate`Bölüm belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır.</span><span class="sxs-lookup"><span data-stu-id="70337-942">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="70337-943">Kullanılabilir varsayılan değer yoksa, sunucu bir özel durum oluşturur ve başlayamaz.</span><span class="sxs-lookup"><span data-stu-id="70337-943">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="70337-944">Bu `Certificate` bölüm hem **yol** &ndash; **parolasını** hem de **Konu** &ndash; **deposu** sertifikalarını destekler.</span><span class="sxs-lookup"><span data-stu-id="70337-944">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="70337-945">Herhangi bir sayıda uç nokta, bağlantı noktası çakışmalarına neden olmadıkları sürece bu şekilde tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-945">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="70337-946">`options.Configure(context.Configuration.GetSection("{SECTION}"))` yapılandırılmış bir `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` uç noktanın ayarlarını tamamlamak için kullanılabilecek bir yöntemi olan bir döndürür:</span><span class="sxs-lookup"><span data-stu-id="70337-946">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="70337-947">`KestrelServerOptions.ConfigurationLoader` , tarafından sağlana gibi var olan yükleyicisindeki yinelemeye devam etmek için doğrudan erişilebilir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="70337-947">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="70337-948">Her uç noktanın yapılandırma bölümü, `Endpoint` özel ayarların okunabilmesi için yöntemindeki seçeneklerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-948">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="70337-949">Birden çok yapılandırma, `options.Configure(context.Configuration.GetSection("{SECTION}"))` başka bir bölümle yeniden çağırarak yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="70337-949">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="70337-950">Önceki örneklerde açıkça çağrılmadığı takdirde, yalnızca son yapılandırma kullanılır `Load` .</span><span class="sxs-lookup"><span data-stu-id="70337-950">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="70337-951">Metapackage, `Load` varsayılan yapılandırma bölümünün değiştirilmesini sağlayacak şekilde çağırmıyor.</span><span class="sxs-lookup"><span data-stu-id="70337-951">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="70337-952">`KestrelConfigurationLoader``Listen`API ailesini `KestrelServerOptions` `Endpoint` aşırı yükleme olarak yansıtır, bu nedenle kod ve yapılandırma uç noktaları aynı yerde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-952">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="70337-953">Bu aşırı yüklemeler adları kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları kullanır.</span><span class="sxs-lookup"><span data-stu-id="70337-953">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="70337-954">*Koddaki varsayılanları değiştirme*</span><span class="sxs-lookup"><span data-stu-id="70337-954">*Change the defaults in code*</span></span>

<span data-ttu-id="70337-955">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` `ListenOptions` `HttpsConnectionAdapterOptions` , önceki senaryoda belirtilen varsayılan sertifikayı geçersiz kılma dahil, ve için varsayılan ayarları değiştirmek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-955">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="70337-956">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-956">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="70337-957">*SNı için Kestrel desteği*</span><span class="sxs-lookup"><span data-stu-id="70337-957">*Kestrel support for SNI*</span></span>

<span data-ttu-id="70337-958">[Sunucu adı belirtme (SNı)](https://tools.ietf.org/html/rfc6066#section-3) , aynı IP adresi ve bağlantı noktası üzerinde birden fazla etki alanını barındırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-958">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="70337-959">SNı 'nin çalışması için, istemci, TLS el sıkışması sırasında güvenli oturum ana bilgisayar adını, sunucunun doğru sertifikayı sağlayabilmesi için gönderir.</span><span class="sxs-lookup"><span data-stu-id="70337-959">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="70337-960">İstemci, TLS anlaşmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için bulunan sertifikayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="70337-960">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="70337-961">Kestrel, geri çağırma aracılığıyla SNı destekler `ServerCertificateSelector` .</span><span class="sxs-lookup"><span data-stu-id="70337-961">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="70337-962">Geri çağırma, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesini sağlamak için bağlantı başına bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="70337-962">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="70337-963">SNı desteği şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="70337-963">SNI support requires:</span></span>

* <span data-ttu-id="70337-964">Hedef Framework `netcoreapp2.1` veya sonraki sürümlerde çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="70337-964">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="70337-965">`net461`Veya sonraki sürümlerde, geri çağırma çağrılır, ancak `name` her zaman olur `null` .</span><span class="sxs-lookup"><span data-stu-id="70337-965">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="70337-966">`name`Ayrıca, `null` ISTEMCI, TLS el sıkışmasının ana bilgisayar adı parametresini sağlamıyorsa de olur.</span><span class="sxs-lookup"><span data-stu-id="70337-966">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="70337-967">Tüm Web siteleri aynı Kestrel örneğinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="70337-967">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="70337-968">Kestrel, bir IP adresi ve bağlantı noktasının bir ters proxy olmadan birden çok örnek arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="70337-968">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="70337-969">Bağlantı günlüğü</span><span class="sxs-lookup"><span data-stu-id="70337-969">Connection logging</span></span>

<span data-ttu-id="70337-970"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>Bir bağlantıda bayt düzeyinde iletişim Için hata ayıklama düzeyi günlüklerini yayma çağrısı.</span><span class="sxs-lookup"><span data-stu-id="70337-970">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="70337-971">Bağlantı günlüğü, TLS şifreleme ve proxy 'nin arkasındaki gibi alt düzey iletişimde sorunları gidermeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="70337-971">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="70337-972">`UseConnectionLogging`Daha önce yerleştirilmişse `UseHttps` , şifrelenmiş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="70337-972">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="70337-973">`UseConnectionLogging`Öğesinden sonra yerleştirilmişse `UseHttps` , şifresi çözülmüş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="70337-973">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="70337-974">TCP yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="70337-974">Bind to a TCP socket</span></span>

<span data-ttu-id="70337-975"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Yöntemi BIR TCP yuvasına bağlanır ve bir seçenek lambda X. 509.440 sertifika yapılandırmasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="70337-975">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="70337-976">Örnek, HTTPS 'yi ile bir uç nokta için yapılandırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="70337-976">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="70337-977">Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak üzere aynı API 'yi kullanın.</span><span class="sxs-lookup"><span data-stu-id="70337-977">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="70337-978">UNIX yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="70337-978">Bind to a Unix socket</span></span>

<span data-ttu-id="70337-979"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>Aşağıdaki örnekte gösterildiği gibi NGINX ile gelişmiş performans için ile birlikte bir UNIX yuvası dinleyin:</span><span class="sxs-lookup"><span data-stu-id="70337-979">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

* <span data-ttu-id="70337-980">NGINX confiuguration dosyasında, `server`  >  `location`  >  `proxy_pass` girdisini olarak ayarlayın `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="70337-980">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="70337-981">`{KESTREL SOCKET}` , için belirtilen yuvanın adıdır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (örneğin, `kestrel-test.sock` Önceki örnekte).</span><span class="sxs-lookup"><span data-stu-id="70337-981">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="70337-982">Yuvanın NGINX tarafından yazılabilir olduğundan emin olun (örneğin, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="70337-982">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="70337-983">Bağlantı noktası 0</span><span class="sxs-lookup"><span data-stu-id="70337-983">Port 0</span></span>

<span data-ttu-id="70337-984">Bağlantı noktası numarası `0` belirtildiğinde, Kestrel dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır.</span><span class="sxs-lookup"><span data-stu-id="70337-984">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="70337-985">Aşağıdaki örnek, çalışma zamanında Kestrel gerçekte hangi bağlantı noktasının gerçekten bağlandığını nasıl belirleyeceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="70337-985">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="70337-986">Uygulama çalıştırıldığında konsol penceresi çıkışı, uygulamanın erişilebileceği dinamik bağlantı noktasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="70337-986">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="70337-987">Sınırlamalar</span><span class="sxs-lookup"><span data-stu-id="70337-987">Limitations</span></span>

<span data-ttu-id="70337-988">Aşağıdaki yaklaşımlar ile uç noktaları yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="70337-988">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="70337-989">`--urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="70337-989">`--urls` command-line argument</span></span>
* <span data-ttu-id="70337-990">`urls` Ana bilgisayar yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="70337-990">`urls` host configuration key</span></span>
* <span data-ttu-id="70337-991">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="70337-991">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="70337-992">Bu yöntemler, kodun Kestrel dışındaki sunucularla çalışmasını sağlamak için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-992">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="70337-993">Ancak, aşağıdaki sınırlamalara dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="70337-993">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="70337-994">HTTPS uç noktası yapılandırmasında varsayılan bir sertifika sağlanmadığı sürece HTTPS bu yaklaşımlar ile kullanılamaz (örneğin, `KestrelServerOptions` Bu konunun önceki kısımlarında gösterildiği gibi yapılandırma veya yapılandırma dosyası kullanılıyor).</span><span class="sxs-lookup"><span data-stu-id="70337-994">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="70337-995">Hem hem de `Listen` `UseUrls` yaklaşımları aynı anda kullanıldığında, uç noktalar `Listen` `UseUrls` uç noktaları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="70337-995">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="70337-996">IIS uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="70337-996">IIS endpoint configuration</span></span>

<span data-ttu-id="70337-997">IIS kullanırken, IIS geçersiz kılma bağlamaları için URL bağlamaları veya ya da tarafından ayarlanır `Listen` `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="70337-997">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="70337-998">Daha fazla bilgi için [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="70337-998">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="70337-999">Libuv aktarım yapılandırması</span><span class="sxs-lookup"><span data-stu-id="70337-999">Libuv transport configuration</span></span>

<span data-ttu-id="70337-1000">ASP.NET Core 2,1 sürümü ile Kestrel 'in varsayılan taşıması artık libuv ' d i temel değildir ancak bunun yerine yönetilen yuvaları temel alır.</span><span class="sxs-lookup"><span data-stu-id="70337-1000">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="70337-1001">Bu, çağrıyı yapan ve aşağıdaki paketlerden birine bağlı olan ASP.NET Core 2,0 2,1 uygulamalarının önemli bir değişikliği olur <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="70337-1001">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="70337-1002">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (doğrudan paket başvurusu)</span><span class="sxs-lookup"><span data-stu-id="70337-1002">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="70337-1003">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="70337-1003">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="70337-1004">Libuv kullanımını gerektiren projeler için:</span><span class="sxs-lookup"><span data-stu-id="70337-1004">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="70337-1005">Uygulamanın proje dosyasına [Microsoft. AspNetCore. Server. Kestrel. Transport. libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) paketi için bir bağımlılık ekleyin:</span><span class="sxs-lookup"><span data-stu-id="70337-1005">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="70337-1006">Çağrı <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="70337-1006">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="70337-1007">URL önekleri</span><span class="sxs-lookup"><span data-stu-id="70337-1007">URL prefixes</span></span>

<span data-ttu-id="70337-1008">`UseUrls`, `--urls` Komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı veya `ASPNETCORE_URLS` ortam değişkeni kullanılırken, URL önekleri aşağıdaki biçimlerden birinde olabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-1008">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="70337-1009">Yalnızca HTTP URL ön ekleri geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="70337-1009">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="70337-1010">Kestrel, kullanılarak URL bağlamaları yapılandırılırken HTTPS 'YI desteklemez `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="70337-1010">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="70337-1011">Bağlantı noktası numarası olan IPv4 adresi</span><span class="sxs-lookup"><span data-stu-id="70337-1011">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="70337-1012">`0.0.0.0` Tüm IPv4 adreslerine bağlanan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="70337-1012">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="70337-1013">Bağlantı noktası numarasına sahip IPv6 adresi</span><span class="sxs-lookup"><span data-stu-id="70337-1013">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="70337-1014">`[::]` , IPv4 'un IPv6 eşdeğeridir `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="70337-1014">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="70337-1015">Bağlantı noktası numarası olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="70337-1015">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="70337-1016">, Ve ana bilgisayar adları `*` `+` özel değildir.</span><span class="sxs-lookup"><span data-stu-id="70337-1016">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="70337-1017">Geçerli bir IP adresi olarak tanınmayan veya `localhost` tüm IPv4 ve IPv6 IP 'lerine bağlanan her şey.</span><span class="sxs-lookup"><span data-stu-id="70337-1017">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="70337-1018">Farklı ana bilgisayar adlarını aynı bağlantı noktasında farklı ASP.NET Core uygulamalarına bağlamak için, [HTTP.sys](xref:fundamentals/servers/httpsys) veya IIS, NGINX veya Apache gibi bir ters proxy sunucusunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="70337-1018">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="70337-1019">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="70337-1019">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="70337-1020">Port numarası `localhost` veya bağlantı noktası numarası ile geri döngü IP 'si olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="70337-1020">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="70337-1021">Belirtildiğinde `localhost` , Kestrel hem IPv4 hem de IPv6 geri döngü arabirimlerini bağlamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="70337-1021">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="70337-1022">İstenen bağlantı noktası, herhangi bir geri döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="70337-1022">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="70337-1023">Herhangi bir geri döngü arabirimi başka bir nedenle kullanılamıyorsa (genellikle IPv6 desteklenmediği için), Kestrel bir uyarı kaydeder.</span><span class="sxs-lookup"><span data-stu-id="70337-1023">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="70337-1024">Konak filtreleme</span><span class="sxs-lookup"><span data-stu-id="70337-1024">Host filtering</span></span>

<span data-ttu-id="70337-1025">Kestrel gibi önekleri temel alarak yapılandırmayı desteklese `http://example.com:5000` de, Kestrel büyük ölçüde ana bilgisayar adını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="70337-1025">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="70337-1026">Ana bilgisayar `localhost` , geri döngü adreslerine bağlama için kullanılan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="70337-1026">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="70337-1027">Açık IP adresi dışındaki tüm ana bilgisayar tüm genel IP adreslerine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="70337-1027">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="70337-1028">`Host` Üstbilgiler doğrulanmadı.</span><span class="sxs-lookup"><span data-stu-id="70337-1028">`Host` headers aren't validated.</span></span>

<span data-ttu-id="70337-1029">Geçici bir çözüm olarak, ana bilgisayar filtreleme ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="70337-1029">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="70337-1030">Ana bilgisayar filtreleme ara yazılımı, [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 veya 2,2) ' de yer alan [Microsoft. Aspnetcore. hostfiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="70337-1030">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="70337-1031">Ara yazılım tarafından eklenir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ve şunları çağırır <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="70337-1031">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="70337-1032">Ana bilgisayar filtreleme ara yazılımı varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-1032">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="70337-1033">Ara yazılımı etkinleştirmek için `AllowedHosts` appSettings 'de bir anahtar tanımlayın *appsettings.json* / *. \<EnvironmentName> . JSON*.</span><span class="sxs-lookup"><span data-stu-id="70337-1033">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="70337-1034">Değer, bağlantı noktası numaraları olmayan ana bilgisayar adlarının noktalı virgülle ayrılmış listesidir:</span><span class="sxs-lookup"><span data-stu-id="70337-1034">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="70337-1035">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="70337-1035">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="70337-1036">[Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer) da bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçenek içerir.</span><span class="sxs-lookup"><span data-stu-id="70337-1036">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="70337-1037">İletilen üstbilgiler ara yazılımı ve ana bilgisayar filtreleme ara yazılımı, farklı senaryolar için benzer işlevlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="70337-1037">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="70337-1038">`AllowedHosts`Iletilen üstbilgiler ara yazılımı ile, `Host` istekler ters bir ara sunucu veya yük dengeleyici ile iletilirken üst bilgi korunurken, bu işlem için uygun bir ayar vardır.</span><span class="sxs-lookup"><span data-stu-id="70337-1038">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="70337-1039">`AllowedHosts`Ana bilgisayar filtreleme ara yazılımı ile ayarlama, Kestrel herkese açık bir uç sunucu olarak veya `Host` üst bilgi doğrudan iletildiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="70337-1039">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="70337-1040">Iletilen üstbilgiler ara yazılımı hakkında daha fazla bilgi için bkz <xref:host-and-deploy/proxy-load-balancer> ..</span><span class="sxs-lookup"><span data-stu-id="70337-1040">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="70337-1041">HTTP/1.1 istek boşaltma</span><span class="sxs-lookup"><span data-stu-id="70337-1041">HTTP/1.1 request draining</span></span>

<span data-ttu-id="70337-1042">HTTP bağlantılarının açılması zaman alabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-1042">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="70337-1043">HTTPS için de kaynak kullanımı yoğun bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="70337-1043">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="70337-1044">Bu nedenle Kestrel, HTTP/1.1 protokolü başına bağlantıları yeniden kullanmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="70337-1044">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="70337-1045">Bağlantının yeniden kullanılmasına izin vermek için bir istek gövdesi tam olarak tüketilmelidir.</span><span class="sxs-lookup"><span data-stu-id="70337-1045">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="70337-1046">Uygulama, `POST` sunucunun yeniden yönlendirme veya 404 yanıtı döndürdüğü bir istek gibi her zaman istek gövdesini tüketmez.</span><span class="sxs-lookup"><span data-stu-id="70337-1046">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="70337-1047">`POST`Yeniden yönlendirme durumunda:</span><span class="sxs-lookup"><span data-stu-id="70337-1047">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="70337-1048">İstemci, verilerin bir bölümünü zaten göndermiş olabilir `POST` .</span><span class="sxs-lookup"><span data-stu-id="70337-1048">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="70337-1049">Sunucu 301 yanıtını yazar.</span><span class="sxs-lookup"><span data-stu-id="70337-1049">The server writes the 301 response.</span></span>
* <span data-ttu-id="70337-1050">`POST`Önceki istek gövdesinden gelen veriler tam olarak okunana kadar bağlantı yeni bir istek için kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="70337-1050">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="70337-1051">Kestrel, istek gövdesini boşaltmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="70337-1051">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="70337-1052">İstek gövdesini boşaltma işlemi işlemeden verileri okuma ve atma anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="70337-1052">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="70337-1053">Boşaltma işlemi bağlantının yeniden kullanılmasına izin verme ve kalan verilerin boşaltıma süresi arasında bir ilerleme gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="70337-1053">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="70337-1054">Boşaltma, yapılandırılabilir olmayan beş saniyelik bir zaman aşımına sahip olur.</span><span class="sxs-lookup"><span data-stu-id="70337-1054">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="70337-1055">Veya üstbilgisi tarafından belirtilen tüm veriler `Content-Length` `Transfer-Encoding` zaman aşımından önce okunmadıysa bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="70337-1055">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="70337-1056">Bazen, yanıtı yazmadan önce veya sonra isteği hemen sonlandırmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="70337-1056">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="70337-1057">Örneğin, istemciler sınırlı verilerin büyük bir düzeyi olabilir, bu nedenle karşıya yüklenen verileri sınırlamak bir öncelik olabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-1057">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="70337-1058">Bu tür durumlarda bir isteği sonlandırmak için bir denetleyici, sayfa veya ara yazılım aracılığıyla [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) ' ı çağırın Razor .</span><span class="sxs-lookup"><span data-stu-id="70337-1058">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="70337-1059">Çağırmanın uyarıları vardır `Abort` :</span><span class="sxs-lookup"><span data-stu-id="70337-1059">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="70337-1060">Yeni bağlantı oluşturma yavaş ve pahalı olabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-1060">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="70337-1061">İstemcinin bağlantı kapanmadan önce yanıtı okuduğunuzdan emin olmaz.</span><span class="sxs-lookup"><span data-stu-id="70337-1061">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="70337-1062">Çağırma `Abort` , yaygın hatalara değil önemli hata durumları için nadir ve ayrılmış olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="70337-1062">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="70337-1063">Yalnızca `Abort` belirli bir sorunun çözülmesi gerektiğinde çağırın.</span><span class="sxs-lookup"><span data-stu-id="70337-1063">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="70337-1064">Örneğin, `Abort` kötü amaçlı istemciler veri almaya çalışıyorsa `POST` veya istemci kodunda büyük veya çok sayıda isteğe neden olan bir hata olduğunda çağırın.</span><span class="sxs-lookup"><span data-stu-id="70337-1064">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="70337-1065">`Abort`HTTP 404 (bulunamadı) gibi yaygın hata durumları için çağrı yapmayın.</span><span class="sxs-lookup"><span data-stu-id="70337-1065">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="70337-1066">Çağrılmadan önce [HttpResponse. tamamlana eşitlemesini](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) çağırmak `Abort` Sunucunun yanıtı yazmayı tamamlamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="70337-1066">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="70337-1067">Ancak, istemci davranışı tahmin edilebilir değildir ve bağlantı durdurulmadan önce yanıtı okuyamayabilir.</span><span class="sxs-lookup"><span data-stu-id="70337-1067">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="70337-1068">Bu işlem HTTP/2 için farklıdır çünkü protokol, bağlantıyı kapatmadan bağımsız istek akışlarını iptal etme işlemini destekler.</span><span class="sxs-lookup"><span data-stu-id="70337-1068">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="70337-1069">Beş saniyelik boşaltma zaman aşımı uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="70337-1069">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="70337-1070">Yanıt tamamlandıktan sonra herhangi bir okunmamış istek gövdesi verisi varsa, sunucu bir HTTP/2 RST çerçevesi gönderir.</span><span class="sxs-lookup"><span data-stu-id="70337-1070">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="70337-1071">Ek istek gövdesi veri çerçeveleri yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="70337-1071">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="70337-1072">Mümkünse, istemcilerin [Beklenen: 100-Continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) istek üst bilgisini kullanabilmesi ve istek gövdesini göndermeden önce sunucunun yanıt vermesini beklemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="70337-1072">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="70337-1073">Bu, istemciye gereksiz verileri göndermeden önce yanıtı İnceleme ve iptal etme olanağı sunar.</span><span class="sxs-lookup"><span data-stu-id="70337-1073">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="70337-1074">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="70337-1074">Additional resources</span></span>

* <span data-ttu-id="70337-1075">Linux üzerinde UNIX yuvaları kullanırken, yuva uygulama kapatılırken otomatik olarak silinmez.</span><span class="sxs-lookup"><span data-stu-id="70337-1075">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="70337-1076">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/aspnetcore/issues/14134)bakın.</span><span class="sxs-lookup"><span data-stu-id="70337-1076">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="70337-1077">RFC 7230: Ileti sözdizimi ve yönlendirme (Bölüm 5,4: Ana bilgisayar)</span><span class="sxs-lookup"><span data-stu-id="70337-1077">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
