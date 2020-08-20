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
ms.openlocfilehash: 5890e56f65712bcd781a3aad278a5aaa7914d0ea
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635027"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="2c524-103">ASP.NET Core Web sunucusu uygulamasını Kestrel</span><span class="sxs-lookup"><span data-stu-id="2c524-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="2c524-104">[Tom Dykstra](https://github.com/tdykstra), [Chris](https://github.com/Tratcher), ve [Stephen halter](https://twitter.com/halter73) tarafından</span><span class="sxs-lookup"><span data-stu-id="2c524-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2c524-105">Kestrel, ASP.NET Core için platformlar arası [Web sunucusudur](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="2c524-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="2c524-106">Kestrel, ASP.NET Core proje şablonlarında varsayılan olarak bulunan Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="2c524-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="2c524-107">Kestrel aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="2c524-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="2c524-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="2c524-108">HTTPS</span></span>
* <span data-ttu-id="2c524-109">[WebSockets](https://github.com/aspnet/websockets) 'i etkinleştirmek için kullanılan donuk yükseltme</span><span class="sxs-lookup"><span data-stu-id="2c524-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="2c524-110">NGINX 'in arkasında yüksek performans için UNIX Yuvaları</span><span class="sxs-lookup"><span data-stu-id="2c524-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="2c524-111">HTTP/2 (macOS hariç &dagger; )</span><span class="sxs-lookup"><span data-stu-id="2c524-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="2c524-112">&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="2c524-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="2c524-113">Kestrel, .NET Core 'un desteklediği tüm platformlarda ve sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="2c524-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="2c524-114">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2c524-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="2c524-115">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="2c524-115">HTTP/2 support</span></span>

<span data-ttu-id="2c524-116">Aşağıdaki temel gereksinimler karşılanıyorsa, [http/2](https://httpwg.org/specs/rfc7540.html) ASP.NET Core uygulamalar için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="2c524-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="2c524-117">İşletim Sistemi&dagger;</span><span class="sxs-lookup"><span data-stu-id="2c524-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="2c524-118">Windows Server 2016/Windows 10 veya üzeri&Dagger;</span><span class="sxs-lookup"><span data-stu-id="2c524-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="2c524-119">OpenSSL 1.0.2 veya üzerini içeren Linux (örneğin, Ubuntu 16,04 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="2c524-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="2c524-120">Hedef Framework: .NET Core 2,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="2c524-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="2c524-121">[Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı</span><span class="sxs-lookup"><span data-stu-id="2c524-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="2c524-122">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="2c524-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="2c524-123">&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="2c524-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="2c524-124">&Dagger;Kestrel, Windows Server 2012 R2 ve Windows 8.1 'de HTTP/2 için sınırlı destek içerir.</span><span class="sxs-lookup"><span data-stu-id="2c524-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="2c524-125">Bu işletim sistemlerinde kullanılabilir olan desteklenen TLS şifre paketlerinin listesi sınırlı olduğundan destek sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="2c524-126">TLS bağlantılarının güvenliğini sağlamak için Eliptik Eğri dijital Imza algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="2c524-127">Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="2c524-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="2c524-128">HTTP/2 varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="2c524-129">Yapılandırma hakkında daha fazla bilgi için [Kestrel Options](#kestrel-options) ve [Listenoptions. Protocols](#listenoptionsprotocols) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="2c524-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="2c524-130">Ters ara sunucu ile Kestrel ne zaman kullanılır?</span><span class="sxs-lookup"><span data-stu-id="2c524-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="2c524-131">Kestrel, kendisi veya [Internet Information Services (IIS)](https://www.iis.net/), [NGINX](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi bir *ters ara sunucu*ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="2c524-132">Ters proxy sunucusu, ağdan gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="2c524-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="2c524-133">Sınır (Internet 'e yönelik) Web sunucusu olarak kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2c524-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel, ters proxy sunucusu olmadan doğrudan Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="2c524-135">Ters Proxy yapılandırmasında kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2c524-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel, IIS, NGINX veya Apache gibi bir ters ara sunucu üzerinden Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="2c524-137">İki yapılandırma de, ters ara sunucu sunucusuyla veya olmadan, desteklenen bir barındırma yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="2c524-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="2c524-138">Ters proxy sunucusu olmayan bir uç sunucu olarak kullanılan Kestrel, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="2c524-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="2c524-139">Kestrel bir bağlantı noktasını dinlemek üzere yapılandırıldığında, Kestrel isteklerin üst bilgilerinden bağımsız olarak bu bağlantı noktası için tüm trafiği işler `Host` .</span><span class="sxs-lookup"><span data-stu-id="2c524-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="2c524-140">Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel 'e iletme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="2c524-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="2c524-141">Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucu kullanılması iyi bir seçim olabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="2c524-142">Ters proxy:</span><span class="sxs-lookup"><span data-stu-id="2c524-142">A reverse proxy:</span></span>

* <span data-ttu-id="2c524-143">, Barındırdığı uygulamaların açığa çıkarılan genel yüzey alanını sınırlayabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="2c524-144">Ek bir yapılandırma ve savunma katmanı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="2c524-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="2c524-145">, Mevcut altyapıyla daha iyi tümleşebilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="2c524-146">Yük dengelemeyi ve güvenli iletişim (HTTPS) yapılandırmasını kolaylaştırın.</span><span class="sxs-lookup"><span data-stu-id="2c524-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="2c524-147">Yalnızca ters proxy sunucusu bir X. 509.440 sertifikası gerektirir ve bu sunucu, düz HTTP kullanarak, iç ağdaki uygulamanın sunucularıyla iletişim kurabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="2c524-148">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="2c524-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="2c524-149">ASP.NET Core uygulamalarda Kestrel</span><span class="sxs-lookup"><span data-stu-id="2c524-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="2c524-150">ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır.</span><span class="sxs-lookup"><span data-stu-id="2c524-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="2c524-151">*Program.cs*içinde Yöntem şunu <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="2c524-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="2c524-152">Konak oluşturma hakkında daha fazla bilgi için, uygulamasının konak ve *Varsayılan Oluşturucu ayarlarını* *ayarlama* bölümüne bakın <xref:fundamentals/host/generic-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="2c524-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="2c524-153">Çağrıldıktan sonra ek yapılandırma sağlamak için `ConfigureWebHostDefaults` şunu kullanın `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="2c524-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="2c524-154">Kestrel seçenekleri</span><span class="sxs-lookup"><span data-stu-id="2c524-154">Kestrel options</span></span>

<span data-ttu-id="2c524-155">Kestrel Web sunucusu, Internet 'e yönelik dağıtımlarda özellikle yararlı olan kısıtlama yapılandırma seçeneklerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="2c524-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="2c524-156">Sınıfının özelliğinde kısıtlamaları ayarlayın <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> .</span><span class="sxs-lookup"><span data-stu-id="2c524-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="2c524-157">`Limits`Özelliği, sınıfının bir örneğini barındırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> .</span><span class="sxs-lookup"><span data-stu-id="2c524-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="2c524-158">Aşağıdaki örnekler <xref:Microsoft.AspNetCore.Server.Kestrel.Core> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="2c524-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="2c524-159">Bu makalenin ilerleyen kısımlarında gösterilen örneklerde, Kestrel seçenekleri C# kodunda yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="2c524-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="2c524-160">Ayrıca, Kestrel seçenekleri bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="2c524-161">Örneğin, [dosya yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider) Kestrel yapılandırmasını bir *appsettings.js* veya appSettings 'ten yükleyebilir *. { Environment}. JSON* dosyası:</span><span class="sxs-lookup"><span data-stu-id="2c524-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="2c524-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> ve [uç nokta yapılandırması](#endpoint-configuration) yapılandırma sağlayıcılarından yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="2c524-163">Kalan Kestrel yapılandırması C# kodunda yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="2c524-164">Aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="2c524-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="2c524-165">Kestrel 'i yapılandırma `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2c524-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="2c524-166">Sınıfına bir örneği `IConfiguration` ekleyin `Startup` .</span><span class="sxs-lookup"><span data-stu-id="2c524-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="2c524-167">Aşağıdaki örnek, eklenen yapılandırmanın özelliğe atandığını varsayar `Configuration` .</span><span class="sxs-lookup"><span data-stu-id="2c524-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="2c524-168">İçinde `Startup.ConfigureServices` , `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="2c524-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="2c524-169">Ana bilgisayarı oluştururken Kestrel yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="2c524-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="2c524-170">*Program.cs*' de, `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="2c524-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="2c524-171">Yukarıdaki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.</span><span class="sxs-lookup"><span data-stu-id="2c524-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="2c524-172">Etkin tut zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="2c524-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="2c524-173">[Etkin tutma zaman aşımını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2c524-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="2c524-174">Varsayılan olarak 2 dakikadır.</span><span class="sxs-lookup"><span data-stu-id="2c524-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="2c524-175">İstemci bağlantıları üst sınırı</span><span class="sxs-lookup"><span data-stu-id="2c524-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="2c524-176">En fazla eş zamanlı açık TCP bağlantısı sayısı tüm uygulama için aşağıdaki kodla ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="2c524-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="2c524-177">HTTP veya HTTPS 'den başka bir protokole (örneğin, bir WebSockets isteğinde) yükseltilen bağlantılara yönelik ayrı bir sınır vardır.</span><span class="sxs-lookup"><span data-stu-id="2c524-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="2c524-178">Bir bağlantı yükseltildikten sonra, bu sınıra göre sayılmaz `MaxConcurrentConnections` .</span><span class="sxs-lookup"><span data-stu-id="2c524-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="2c524-179">En fazla bağlantı sayısı, varsayılan olarak sınırsız (null).</span><span class="sxs-lookup"><span data-stu-id="2c524-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="2c524-180">En büyük istek gövdesi boyutu</span><span class="sxs-lookup"><span data-stu-id="2c524-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="2c524-181">Varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu değer yaklaşık 28,6 MB 'tır.</span><span class="sxs-lookup"><span data-stu-id="2c524-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="2c524-182">ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yaklaşım, <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="2c524-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="2c524-183">Her istekte uygulama için kısıtlamanın nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="2c524-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="2c524-184">Ara yazılım içindeki belirli bir istek üzerindeki ayarı geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="2c524-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="2c524-185">Uygulama isteği okumaya başladıktan sonra bir istek üzerindeki sınırı yapılandırırsa, bir özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="2c524-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="2c524-186">`IsReadOnly`Özelliğin salt okuma durumunda olup olmadığını belirten bir özellik vardır. Bu, `MaxRequestBodySize` sınırı yapılandırmanın çok geç olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="2c524-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="2c524-187">Bir uygulama [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)arkasında [çalıştırıldığında](xref:host-and-deploy/iis/index#out-of-process-hosting-model) , IIS sınırı zaten ayarladığı için Kestrel 'nin istek gövdesi boyut sınırı devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="2c524-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="2c524-188">En az istek gövdesi veri hızı</span><span class="sxs-lookup"><span data-stu-id="2c524-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="2c524-189">Kestrel bayt/saniye cinsinden belirtilen fiyata ulaşan her saniye sonra denetler.</span><span class="sxs-lookup"><span data-stu-id="2c524-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="2c524-190">Oran en düşük değerin altına düşerse bağlantı zaman aşımına uğrar. Yetkisiz kullanım süresi, Kestrel 'in istemciye gönderme oranını en düşük süreye kadar artırabilme Bu süre boyunca fiyat denetlenmez.</span><span class="sxs-lookup"><span data-stu-id="2c524-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="2c524-191">Yetkisiz kullanım süresi, TCP yavaş başlatma nedeniyle başlangıçta verileri yavaş bir hızda gönderen bağlantıların bırakılmasını önlemeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="2c524-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="2c524-192">Varsayılan en düşük oran, 5 saniyelik bir yetkisiz kullanım süresi ile 240 bayt/saniye olur.</span><span class="sxs-lookup"><span data-stu-id="2c524-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="2c524-193">Yanıt için bir minimum oran da geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="2c524-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="2c524-194">İstek sınırını ayarlamaya yönelik kod ve yanıt sınırı, `RequestBody` `Response` özellik ve arabirim adlarında olduğu gibi aynı olur.</span><span class="sxs-lookup"><span data-stu-id="2c524-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="2c524-195">*Program.cs*içinde en düşük veri hızlarının nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="2c524-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="2c524-196">Ara yazılım içindeki istek başına düşen minimum hız sınırlarını geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="2c524-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="2c524-197"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>Önceki örnekte başvurulan, `HttpContext.Features` http/2 isteklerinde hız sınırlarını değiştirmek, protokolün istek çoğullama DESTEĞI nedeniyle http/2 için genel olarak desteklenmediği IÇIN, http/2 istekleri için ' de mevcut değildir.</span><span class="sxs-lookup"><span data-stu-id="2c524-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="2c524-198">Ancak, http <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> `HttpContext.Features` /2 istekleri için yine de vardır, çünkü okuma hızı sınırı, *disabled entirely* `IHttpMinRequestBodyDataRateFeature.MinDataRate` `null` bir http/2 isteği için de olarak ayarlanarak tamamen istek temelli olarak devre dışı bırakılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="2c524-199">`IHttpMinRequestBodyDataRateFeature.MinDataRate`Bunun dışında bir değere ayarlamaya çalışılması veya BIR `null` `NotSupportedException` http/2 isteği verilmeye neden olur.</span><span class="sxs-lookup"><span data-stu-id="2c524-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="2c524-200">`KestrelServerOptions.Limits`Http/1. x ve http/2 bağlantılarına hala uygulanan sunucu genelindeki hız sınırları.</span><span class="sxs-lookup"><span data-stu-id="2c524-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="2c524-201">İstek üst bilgileri zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="2c524-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="2c524-202">Sunucunun istek üst bilgilerini alması için harcadığı en uzun süreyi alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2c524-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="2c524-203">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="2c524-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="2c524-204">Bağlantı başına en fazla akış</span><span class="sxs-lookup"><span data-stu-id="2c524-204">Maximum streams per connection</span></span>

<span data-ttu-id="2c524-205">`Http2.MaxStreamsPerConnection` HTTP/2 bağlantısı başına eşzamanlı istek akışı sayısını sınırlar.</span><span class="sxs-lookup"><span data-stu-id="2c524-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="2c524-206">Fazlalık akışlar reddedildi.</span><span class="sxs-lookup"><span data-stu-id="2c524-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="2c524-207">Varsayılan değer 100’dür.</span><span class="sxs-lookup"><span data-stu-id="2c524-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="2c524-208">Üst bilgi tablosu boyutu</span><span class="sxs-lookup"><span data-stu-id="2c524-208">Header table size</span></span>

<span data-ttu-id="2c524-209">HPACK kod çözücüsü HTTP/2 bağlantıları için HTTP üstbilgilerini açar.</span><span class="sxs-lookup"><span data-stu-id="2c524-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="2c524-210">`Http2.HeaderTableSize` HPACK kod çözücüsünün kullandığı üst bilgi sıkıştırma tablosunun boyutunu sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="2c524-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="2c524-211">Değer sekizli cinsinden sağlanır ve sıfırdan büyük olmalıdır (0).</span><span class="sxs-lookup"><span data-stu-id="2c524-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="2c524-212">Varsayılan değer 4096 ' dir.</span><span class="sxs-lookup"><span data-stu-id="2c524-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="2c524-213">En büyük çerçeve boyutu</span><span class="sxs-lookup"><span data-stu-id="2c524-213">Maximum frame size</span></span>

<span data-ttu-id="2c524-214">`Http2.MaxFrameSize` sunucu tarafından alınan veya gönderilen HTTP/2 bağlantı çerçevesi yükünün izin verilen en büyük boyutunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="2c524-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="2c524-215">Değer sekizli cinsinden sağlanır ve 2 ^ 14 (16.384) ile 2 ^ 24-1 (16.777.215) arasında olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="2c524-216">Varsayılan değer 2 ^ 14 ' dir (16.384).</span><span class="sxs-lookup"><span data-stu-id="2c524-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="2c524-217">En fazla istek üst bilgi boyutu</span><span class="sxs-lookup"><span data-stu-id="2c524-217">Maximum request header size</span></span>

<span data-ttu-id="2c524-218">`Http2.MaxRequestHeaderFieldSize` istek üst bilgisi değerlerinin sekizlisi cinsinden izin verilen en büyük boyutu belirtir.</span><span class="sxs-lookup"><span data-stu-id="2c524-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="2c524-219">Bu sınır, sıkıştırılmış ve sıkıştırılmamış temsillerinde hem ad hem de değer için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="2c524-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="2c524-220">Değer sıfırdan büyük (0) olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="2c524-221">Varsayılan değer 8.192 ' dir.</span><span class="sxs-lookup"><span data-stu-id="2c524-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="2c524-222">İlk bağlantı pencere boyutu</span><span class="sxs-lookup"><span data-stu-id="2c524-222">Initial connection window size</span></span>

<span data-ttu-id="2c524-223">`Http2.InitialConnectionWindowSize` sunucu, bağlantı başına tüm istekler (akışlar) genelinde toplanan tek seferde sunucunun arabelleğe aldığı en fazla istek gövde verilerini bayt cinsinden gösterir.</span><span class="sxs-lookup"><span data-stu-id="2c524-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="2c524-224">İstekleri ile de sınırlıdır `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="2c524-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="2c524-225">Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="2c524-226">Varsayılan değer 128 KB 'tır (131.072).</span><span class="sxs-lookup"><span data-stu-id="2c524-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="2c524-227">İlk akış pencere boyutu</span><span class="sxs-lookup"><span data-stu-id="2c524-227">Initial stream window size</span></span>

<span data-ttu-id="2c524-228">`Http2.InitialStreamWindowSize` sunucu, istek başına bir kez (Stream) arabelleğe alınan en fazla istek gövde verilerini bayt cinsinden gösterir.</span><span class="sxs-lookup"><span data-stu-id="2c524-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="2c524-229">İstekleri ile de sınırlıdır `Http2.InitialConnectionWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="2c524-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="2c524-230">Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="2c524-231">Varsayılan değer 96 KB 'tır (98.304).</span><span class="sxs-lookup"><span data-stu-id="2c524-231">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="2c524-232">Zaman Uyumlu G/Ç</span><span class="sxs-lookup"><span data-stu-id="2c524-232">Synchronous I/O</span></span>

<span data-ttu-id="2c524-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> istek ve yanıt için zaman uyumlu g/ç 'ye izin verilip verilmediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="2c524-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="2c524-234">Varsayılan değer: `false`.</span><span class="sxs-lookup"><span data-stu-id="2c524-234">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="2c524-235">Çok sayıda engelleme zaman uyumlu g/ç işlemi, iş parçacığı havuzuna yol açabilir, bu da uygulamanın yanıt vermemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="2c524-235">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="2c524-236">Yalnızca `AllowSynchronousIO` zaman uyumsuz g/ç desteklemeyen bir kitaplık kullanırken etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="2c524-236">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="2c524-237">Aşağıdaki örnek, zaman uyumlu g/ç 'yi sunar:</span><span class="sxs-lookup"><span data-stu-id="2c524-237">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="2c524-238">Diğer Kestrel seçenekleri ve limitleri hakkında daha fazla bilgi için bkz.:</span><span class="sxs-lookup"><span data-stu-id="2c524-238">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="2c524-239">Uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="2c524-239">Endpoint configuration</span></span>

<span data-ttu-id="2c524-240">Varsayılan olarak, ASP.NET Core bağlar:</span><span class="sxs-lookup"><span data-stu-id="2c524-240">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="2c524-241">`https://localhost:5001` (bir yerel geliştirme sertifikası varsa)</span><span class="sxs-lookup"><span data-stu-id="2c524-241">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="2c524-242">Kullanarak URL 'Leri belirtin:</span><span class="sxs-lookup"><span data-stu-id="2c524-242">Specify URLs using the:</span></span>

* <span data-ttu-id="2c524-243">`ASPNETCORE_URLS` ortam değişkeni.</span><span class="sxs-lookup"><span data-stu-id="2c524-243">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="2c524-244">`--urls` komut satırı bağımsız değişkeni.</span><span class="sxs-lookup"><span data-stu-id="2c524-244">`--urls` command-line argument.</span></span>
* <span data-ttu-id="2c524-245">`urls` Ana bilgisayar yapılandırma anahtarı.</span><span class="sxs-lookup"><span data-stu-id="2c524-245">`urls` host configuration key.</span></span>
* <span data-ttu-id="2c524-246">`UseUrls` genişletme yöntemi.</span><span class="sxs-lookup"><span data-stu-id="2c524-246">`UseUrls` extension method.</span></span>

<span data-ttu-id="2c524-247">Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2c524-247">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="2c524-248">Değeri noktalı virgülle ayrılmış bir liste olarak yapılandırın (örneğin, `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="2c524-248">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="2c524-249">Bu yaklaşımlar hakkında daha fazla bilgi için [sunucu URL 'leri](xref:fundamentals/host/web-host#server-urls) ve [geçersiz kılma yapılandırması](xref:fundamentals/host/web-host#override-configuration)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="2c524-249">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="2c524-250">Geliştirme sertifikası oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="2c524-250">A development certificate is created:</span></span>

* <span data-ttu-id="2c524-251">[.NET Core SDK](/dotnet/core/sdk) yüklendiği zaman.</span><span class="sxs-lookup"><span data-stu-id="2c524-251">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="2c524-252">[Geliştirme-CERT aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2c524-252">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="2c524-253">Bazı tarayıcılarda yerel geliştirme sertifikasına güvenmek için açık izin verilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="2c524-253">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="2c524-254">Proje şablonları, uygulamaları HTTPS üzerinde varsayılan olarak çalışacak şekilde yapılandırır ve [https yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.</span><span class="sxs-lookup"><span data-stu-id="2c524-254">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="2c524-255"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> URL öneklerini ve bağlantı noktalarını Kestrel için yapılandırmak üzere üzerinde çağrı veya Yöntemler.</span><span class="sxs-lookup"><span data-stu-id="2c524-255">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="2c524-256">`UseUrls`, `--urls` komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı ve `ASPNETCORE_URLS` ortam değişkeni de çalışır, ancak bu bölümün ilerleyen kısımlarında belirtilen sınırlamalara sahiptir (https uç noktası yapılandırması için varsayılan sertifika kullanılabilir olmalıdır).</span><span class="sxs-lookup"><span data-stu-id="2c524-256">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="2c524-257">`KestrelServerOptions` yapılandırmada</span><span class="sxs-lookup"><span data-stu-id="2c524-257">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="2c524-258">ConfigureEndpointDefaults Varsayılanları (eylem \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="2c524-258">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="2c524-259">`Action`Belirtilen her bir uç nokta için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="2c524-259">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="2c524-260">`ConfigureEndpointDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="2c524-260">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2c524-261">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="2c524-261">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="2c524-262">ConfigureHttpsDefaults (eylem \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="2c524-262">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="2c524-263">`Action`Her HTTPS uç noktası için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="2c524-263">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="2c524-264">`ConfigureHttpsDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="2c524-264">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2c524-265">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="2c524-265">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="2c524-266">Yapılandırma (Iconation)</span><span class="sxs-lookup"><span data-stu-id="2c524-266">Configure(IConfiguration)</span></span>

<span data-ttu-id="2c524-267">Bir as girişi alan Kestrel ayarlamak için bir yapılandırma yükleyicisi oluşturur <xref:Microsoft.Extensions.Configuration.IConfiguration> .</span><span class="sxs-lookup"><span data-stu-id="2c524-267">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="2c524-268">Yapılandırma, Kestrel için yapılandırma bölümünün kapsamına alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-268">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="2c524-269">ListenOptions. UseHttps</span><span class="sxs-lookup"><span data-stu-id="2c524-269">ListenOptions.UseHttps</span></span>

<span data-ttu-id="2c524-270">Kestrel 'i HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="2c524-270">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="2c524-271">`ListenOptions.UseHttps` uzantılardan</span><span class="sxs-lookup"><span data-stu-id="2c524-271">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="2c524-272">`UseHttps`: Kestrel 'i varsayılan sertifikayla HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="2c524-272">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="2c524-273">Varsayılan sertifika yapılandırılmamışsa bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2c524-273">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="2c524-274">`ListenOptions.UseHttps` parametrelere</span><span class="sxs-lookup"><span data-stu-id="2c524-274">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="2c524-275">`filename` , uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yolu ve dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-275">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="2c524-276">`password` X. 509.440 sertifika verilerine erişmek için parola gereklidir.</span><span class="sxs-lookup"><span data-stu-id="2c524-276">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="2c524-277">`configureOptions` , öğesini `Action` yapılandırmak için kullanılır `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="2c524-277">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="2c524-278">Döndürür `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="2c524-278">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="2c524-279">`storeName` , sertifikanın yükleneceği sertifika deposudur.</span><span class="sxs-lookup"><span data-stu-id="2c524-279">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="2c524-280">`subject` , sertifika için konu adıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-280">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="2c524-281">`allowInvalid` geçersiz sertifikaların, otomatik olarak imzalanan sertifikalar gibi göz önünde bulundurulmayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="2c524-281">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="2c524-282">`location` , sertifikanın yükleneceği mağaza konumudur.</span><span class="sxs-lookup"><span data-stu-id="2c524-282">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="2c524-283">`serverCertificate` , X. 509.440 sertifikasıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-283">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="2c524-284">Üretimde HTTPS 'nin açıkça yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2c524-284">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="2c524-285">En azından, varsayılan bir sertifika sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-285">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="2c524-286">Daha sonra açıklanan desteklenen yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="2c524-286">Supported configurations described next:</span></span>

* <span data-ttu-id="2c524-287">Yapılandırma yok</span><span class="sxs-lookup"><span data-stu-id="2c524-287">No configuration</span></span>
* <span data-ttu-id="2c524-288">Varsayılan sertifikayı yapılandırmadan Değiştir</span><span class="sxs-lookup"><span data-stu-id="2c524-288">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="2c524-289">Koddaki varsayılanları değiştirme</span><span class="sxs-lookup"><span data-stu-id="2c524-289">Change the defaults in code</span></span>

<span data-ttu-id="2c524-290">*Yapılandırma yok*</span><span class="sxs-lookup"><span data-stu-id="2c524-290">*No configuration*</span></span>

<span data-ttu-id="2c524-291">Kestrel tarihinde dinler `http://localhost:5000` ve `https://localhost:5001` (varsayılan bir sertifika varsa).</span><span class="sxs-lookup"><span data-stu-id="2c524-291">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="2c524-292">*Varsayılan sertifikayı yapılandırmadan Değiştir*</span><span class="sxs-lookup"><span data-stu-id="2c524-292">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="2c524-293">`CreateDefaultBuilder``Configure(context.Configuration.GetSection("Kestrel"))`Kestrel yapılandırmasını yüklemek için varsayılan olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="2c524-293">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="2c524-294">Varsayılan bir HTTPS uygulama ayarları yapılandırma şeması Kestrel için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-294">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="2c524-295">Disk üzerindeki bir dosyadan ya da bir sertifika deposundan kullanılacak URL 'Ler ve Sertifikalar dahil olmak üzere birden çok uç nokta yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="2c524-295">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="2c524-296">Aşağıdaki *appsettings.js* aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="2c524-296">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="2c524-297">Geçersiz sertifikaların kullanılmasına izin vermek için **Allowwınvalid** `true` ' i ayarlayın (örneğin, otomatik olarak imzalanan sertifikalar).</span><span class="sxs-lookup"><span data-stu-id="2c524-297">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="2c524-298">Bir sertifika belirtmeyen herhangi bir HTTPS uç noktası (aşağıdaki örnekte bulunan**httpsdefaultcert** ), **Sertifikalar** > **varsayılan** veya geliştirme sertifikası altında tanımlanan sertifikaya geri döner.</span><span class="sxs-lookup"><span data-stu-id="2c524-298">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="2c524-299">Herhangi bir sertifika düğümü için **yol** ve **parola** kullanmanın alternatifi sertifika deposu alanlarını kullanarak sertifikayı belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="2c524-299">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="2c524-300">Örneğin, **sertifika**  >  **varsayılan** sertifikası şu şekilde belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="2c524-300">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="2c524-301">Şema notları:</span><span class="sxs-lookup"><span data-stu-id="2c524-301">Schema notes:</span></span>

* <span data-ttu-id="2c524-302">Uç nokta adları büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-302">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="2c524-303">Örneğin, `HTTPS` ve `Https` geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="2c524-303">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="2c524-304">`Url`Her uç nokta için parametresi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="2c524-304">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="2c524-305">Bu parametrenin biçimi, en üst düzey `Urls` yapılandırma parametresiyle aynıdır, ancak tek bir değerle sınırlı olur.</span><span class="sxs-lookup"><span data-stu-id="2c524-305">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="2c524-306">Bu uç noktalar, üst düzey yapılandırmada tanımlananlar yerine `Urls` bunlara ekleme yerine bunların yerini alır.</span><span class="sxs-lookup"><span data-stu-id="2c524-306">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="2c524-307">Kullanılarak kodda tanımlanan uç noktalar `Listen` yapılandırma bölümünde tanımlanan uç noktalar ile birikimlidir.</span><span class="sxs-lookup"><span data-stu-id="2c524-307">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="2c524-308">Bu `Certificate` bölüm isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-308">The `Certificate` section is optional.</span></span> <span data-ttu-id="2c524-309">`Certificate`Bölüm belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2c524-309">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="2c524-310">Kullanılabilir varsayılan değer yoksa, sunucu bir özel durum oluşturur ve başlayamaz.</span><span class="sxs-lookup"><span data-stu-id="2c524-310">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="2c524-311">Bu `Certificate` bölüm hem **yol** &ndash; **parolasını** hem de **Konu** &ndash; **deposu** sertifikalarını destekler.</span><span class="sxs-lookup"><span data-stu-id="2c524-311">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="2c524-312">Herhangi bir sayıda uç nokta, bağlantı noktası çakışmalarına neden olmadıkları sürece bu şekilde tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-312">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="2c524-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))` yapılandırılmış bir `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` uç noktanın ayarlarını tamamlamak için kullanılabilecek bir yöntemi olan bir döndürür:</span><span class="sxs-lookup"><span data-stu-id="2c524-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="2c524-314">`KestrelServerOptions.ConfigurationLoader` , tarafından sağlana gibi var olan yükleyicisindeki yinelemeye devam etmek için doğrudan erişilebilir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="2c524-314">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="2c524-315">Her uç noktanın yapılandırma bölümü, `Endpoint` özel ayarların okunabilmesi için yöntemindeki seçeneklerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-315">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="2c524-316">Birden çok yapılandırma, `options.Configure(context.Configuration.GetSection("{SECTION}"))` başka bir bölümle yeniden çağırarak yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-316">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="2c524-317">Önceki örneklerde açıkça çağrılmadığı takdirde, yalnızca son yapılandırma kullanılır `Load` .</span><span class="sxs-lookup"><span data-stu-id="2c524-317">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="2c524-318">Metapackage, `Load` varsayılan yapılandırma bölümünün değiştirilmesini sağlayacak şekilde çağırmıyor.</span><span class="sxs-lookup"><span data-stu-id="2c524-318">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="2c524-319">`KestrelConfigurationLoader``Listen`API ailesini `KestrelServerOptions` `Endpoint` aşırı yükleme olarak yansıtır, bu nedenle kod ve yapılandırma uç noktaları aynı yerde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-319">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="2c524-320">Bu aşırı yüklemeler adları kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları kullanır.</span><span class="sxs-lookup"><span data-stu-id="2c524-320">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="2c524-321">*Koddaki varsayılanları değiştirme*</span><span class="sxs-lookup"><span data-stu-id="2c524-321">*Change the defaults in code*</span></span>

<span data-ttu-id="2c524-322">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` `ListenOptions` `HttpsConnectionAdapterOptions` , önceki senaryoda belirtilen varsayılan sertifikayı geçersiz kılma dahil, ve için varsayılan ayarları değiştirmek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-322">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="2c524-323">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-323">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="2c524-324">*SNı için Kestrel desteği*</span><span class="sxs-lookup"><span data-stu-id="2c524-324">*Kestrel support for SNI*</span></span>

<span data-ttu-id="2c524-325">[Sunucu adı belirtme (SNı)](https://tools.ietf.org/html/rfc6066#section-3) , aynı IP adresi ve bağlantı noktası üzerinde birden fazla etki alanını barındırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-325">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="2c524-326">SNı 'nin çalışması için, istemci, TLS el sıkışması sırasında güvenli oturum ana bilgisayar adını, sunucunun doğru sertifikayı sağlayabilmesi için gönderir.</span><span class="sxs-lookup"><span data-stu-id="2c524-326">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="2c524-327">İstemci, TLS anlaşmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için bulunan sertifikayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="2c524-327">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="2c524-328">Kestrel, geri çağırma aracılığıyla SNı destekler `ServerCertificateSelector` .</span><span class="sxs-lookup"><span data-stu-id="2c524-328">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="2c524-329">Geri çağırma, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesini sağlamak için bağlantı başına bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="2c524-329">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="2c524-330">SNı desteği şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="2c524-330">SNI support requires:</span></span>

* <span data-ttu-id="2c524-331">Hedef Framework `netcoreapp2.1` veya sonraki sürümlerde çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="2c524-331">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="2c524-332">`net461`Veya sonraki sürümlerde, geri çağırma çağrılır, ancak `name` her zaman olur `null` .</span><span class="sxs-lookup"><span data-stu-id="2c524-332">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="2c524-333">`name`Ayrıca, `null` ISTEMCI, TLS el sıkışmasının ana bilgisayar adı parametresini sağlamıyorsa de olur.</span><span class="sxs-lookup"><span data-stu-id="2c524-333">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="2c524-334">Tüm Web siteleri aynı Kestrel örneğinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="2c524-334">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="2c524-335">Kestrel, bir IP adresi ve bağlantı noktasının bir ters proxy olmadan birden çok örnek arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="2c524-335">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="2c524-336">Bağlantı günlüğü</span><span class="sxs-lookup"><span data-stu-id="2c524-336">Connection logging</span></span>

<span data-ttu-id="2c524-337"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>Bir bağlantıda bayt düzeyinde iletişim Için hata ayıklama düzeyi günlüklerini yayma çağrısı.</span><span class="sxs-lookup"><span data-stu-id="2c524-337">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="2c524-338">Bağlantı günlüğü, TLS şifreleme ve proxy 'nin arkasındaki gibi alt düzey iletişimde sorunları gidermeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="2c524-338">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="2c524-339">`UseConnectionLogging`Daha önce yerleştirilmişse `UseHttps` , şifrelenmiş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-339">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="2c524-340">`UseConnectionLogging`Öğesinden sonra yerleştirilmişse `UseHttps` , şifresi çözülmüş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-340">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="2c524-341">TCP yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="2c524-341">Bind to a TCP socket</span></span>

<span data-ttu-id="2c524-342"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Yöntemi BIR TCP yuvasına bağlanır ve bir seçenek lambda X. 509.440 sertifika yapılandırmasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="2c524-342">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="2c524-343">Örnek, HTTPS 'yi ile bir uç nokta için yapılandırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="2c524-343">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="2c524-344">Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak üzere aynı API 'yi kullanın.</span><span class="sxs-lookup"><span data-stu-id="2c524-344">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="2c524-345">UNIX yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="2c524-345">Bind to a Unix socket</span></span>

<span data-ttu-id="2c524-346"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>Aşağıdaki örnekte gösterildiği gibi NGINX ile gelişmiş performans için ile birlikte bir UNIX yuvası dinleyin:</span><span class="sxs-lookup"><span data-stu-id="2c524-346">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="2c524-347">NGINX yapılandırma dosyasında, `server`  >  `location`  >  `proxy_pass` girdisini olarak ayarlayın `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="2c524-347">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="2c524-348">`{KESTREL SOCKET}` , için belirtilen yuvanın adıdır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (örneğin, `kestrel-test.sock` Önceki örnekte).</span><span class="sxs-lookup"><span data-stu-id="2c524-348">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="2c524-349">Yuvanın NGINX tarafından yazılabilir olduğundan emin olun (örneğin, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="2c524-349">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="2c524-350">Bağlantı noktası 0</span><span class="sxs-lookup"><span data-stu-id="2c524-350">Port 0</span></span>

<span data-ttu-id="2c524-351">Bağlantı noktası numarası `0` belirtildiğinde, Kestrel dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır.</span><span class="sxs-lookup"><span data-stu-id="2c524-351">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="2c524-352">Aşağıdaki örnek, çalışma zamanında Kestrel gerçekte hangi bağlantı noktasının gerçekten bağlandığını nasıl belirleyeceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="2c524-352">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="2c524-353">Uygulama çalıştırıldığında konsol penceresi çıkışı, uygulamanın erişilebileceği dinamik bağlantı noktasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="2c524-353">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="2c524-354">Sınırlamalar</span><span class="sxs-lookup"><span data-stu-id="2c524-354">Limitations</span></span>

<span data-ttu-id="2c524-355">Aşağıdaki yaklaşımlar ile uç noktaları yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="2c524-355">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="2c524-356">`--urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="2c524-356">`--urls` command-line argument</span></span>
* <span data-ttu-id="2c524-357">`urls` Ana bilgisayar yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="2c524-357">`urls` host configuration key</span></span>
* <span data-ttu-id="2c524-358">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="2c524-358">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="2c524-359">Bu yöntemler, kodun Kestrel dışındaki sunucularla çalışmasını sağlamak için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-359">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="2c524-360">Ancak, aşağıdaki sınırlamalara dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="2c524-360">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="2c524-361">HTTPS uç noktası yapılandırmasında varsayılan bir sertifika sağlanmadığı sürece HTTPS bu yaklaşımlar ile kullanılamaz (örneğin, `KestrelServerOptions` Bu konunun önceki kısımlarında gösterildiği gibi yapılandırma veya yapılandırma dosyası kullanılıyor).</span><span class="sxs-lookup"><span data-stu-id="2c524-361">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="2c524-362">Hem hem de `Listen` `UseUrls` yaklaşımları aynı anda kullanıldığında, uç noktalar `Listen` `UseUrls` uç noktaları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="2c524-362">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="2c524-363">IIS uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="2c524-363">IIS endpoint configuration</span></span>

<span data-ttu-id="2c524-364">IIS kullanırken, IIS geçersiz kılma bağlamaları için URL bağlamaları veya ya da tarafından ayarlanır `Listen` `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="2c524-364">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="2c524-365">Daha fazla bilgi için [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="2c524-365">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="2c524-366">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="2c524-366">ListenOptions.Protocols</span></span>

<span data-ttu-id="2c524-367">`Protocols`Özelliği, `HttpProtocols` bir bağlantı uç noktasında veya sunucu IÇIN etkin HTTP protokollerini () belirler.</span><span class="sxs-lookup"><span data-stu-id="2c524-367">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="2c524-368">`Protocols`Sabit listesinden özelliğe bir değer atayın `HttpProtocols` .</span><span class="sxs-lookup"><span data-stu-id="2c524-368">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="2c524-369">`HttpProtocols` sabit listesi değeri</span><span class="sxs-lookup"><span data-stu-id="2c524-369">`HttpProtocols` enum value</span></span> | <span data-ttu-id="2c524-370">Bağlantı protokolü izin verildi</span><span class="sxs-lookup"><span data-stu-id="2c524-370">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="2c524-371">Yalnızca HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="2c524-371">HTTP/1.1 only.</span></span> <span data-ttu-id="2c524-372">, TLS olmadan veya ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-372">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="2c524-373">Yalnızca HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2c524-373">HTTP/2 only.</span></span> <span data-ttu-id="2c524-374">Yalnızca istemci [önceki bir bilgi modunu](https://tools.ietf.org/html/rfc7540#section-3.4)DESTEKLIYORSA, TLS olmadan kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-374">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="2c524-375">HTTP/1.1 ve HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2c524-375">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="2c524-376">HTTP/2, istemcinin TLS [uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) el sıkışmasında http/2 seçmesini gerektirir; Aksi takdirde, bağlantı varsayılan olarak HTTP/1.1 ' dir.</span><span class="sxs-lookup"><span data-stu-id="2c524-376">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="2c524-377">`ListenOptions.Protocols`Herhangi bir uç nokta için varsayılan değer `HttpProtocols.Http1AndHttp2` .</span><span class="sxs-lookup"><span data-stu-id="2c524-377">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="2c524-378">HTTP/2 için TLS kısıtlamaları:</span><span class="sxs-lookup"><span data-stu-id="2c524-378">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="2c524-379">TLS sürüm 1,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="2c524-379">TLS version 1.2 or later</span></span>
* <span data-ttu-id="2c524-380">Yeniden anlaşma devre dışı</span><span class="sxs-lookup"><span data-stu-id="2c524-380">Renegotiation disabled</span></span>
* <span data-ttu-id="2c524-381">Sıkıştırma devre dışı</span><span class="sxs-lookup"><span data-stu-id="2c524-381">Compression disabled</span></span>
* <span data-ttu-id="2c524-382">En az kısa ömürlü anahtar değişim boyutları:</span><span class="sxs-lookup"><span data-stu-id="2c524-382">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="2c524-383">Eliptik Eğri Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bit minimum</span><span class="sxs-lookup"><span data-stu-id="2c524-383">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="2c524-384">Sınırlı alan Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; : 2048 bit minimum</span><span class="sxs-lookup"><span data-stu-id="2c524-384">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="2c524-385">Şifre paketi yasaklandı.</span><span class="sxs-lookup"><span data-stu-id="2c524-385">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="2c524-386">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; P-256 eliptik eğrisi &lbrack; `FIPS186` &rbrack; Varsayılan olarak desteklenir.</span><span class="sxs-lookup"><span data-stu-id="2c524-386">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="2c524-387">Aşağıdaki örnek, 8000 numaralı bağlantı noktasında HTTP/1.1 ve HTTP/2 bağlantılarına izin verir.</span><span class="sxs-lookup"><span data-stu-id="2c524-387">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="2c524-388">Bağlantılar, sağlanan bir sertifikayla TLS ile güvenlidir:</span><span class="sxs-lookup"><span data-stu-id="2c524-388">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="2c524-389">Gerektiğinde belirli şifrelemeler için TLS el sıkışmaları için bağlantı temelinde filtre uygulamak üzere bağlantı ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="2c524-389">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="2c524-390">Aşağıdaki örnek, <xref:System.NotSupportedException> uygulamanın desteklemediği herhangi bir şifre algoritması için oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2c524-390">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="2c524-391">Alternatif olarak, [ılshandshakefeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) öğesini kabul edilebilir şifreleme paketleri listesi ile tanımlayın ve karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="2c524-391">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="2c524-392">[CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) şifre algoritması ile hiçbir şifreleme kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="2c524-392">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="2c524-393">Bağlantı filtrelemesi, bir lambda aracılığıyla da yapılandırılabilir <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> :</span><span class="sxs-lookup"><span data-stu-id="2c524-393">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="2c524-394">Linux 'ta, <xref:System.Net.Security.CipherSuitesPolicy> TLS el sıkışmaları her bağlantı temelinde filtrelemek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="2c524-394">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="2c524-395">*Protokolü yapılandırmadan ayarla*</span><span class="sxs-lookup"><span data-stu-id="2c524-395">*Set the protocol from configuration*</span></span>

<span data-ttu-id="2c524-396">`CreateDefaultBuilder``serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`Kestrel yapılandırmasını yüklemek için varsayılan olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="2c524-396">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="2c524-397">Aşağıdakiappsettings.js, tüm uç noktaları için varsayılan bağlantı protokolü olarak HTTP/1.1 \* '\* i oluşturur:</span><span class="sxs-lookup"><span data-stu-id="2c524-397">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="2c524-398">Aşağıdaki *appsettings.js* örnek, belirli bir uç nokta için http/1.1 bağlantı protokolünü belirler:</span><span class="sxs-lookup"><span data-stu-id="2c524-398">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="2c524-399">Yapılandırma tarafından ayarlanan kod geçersiz kılma değerlerinde belirtilen protokoller.</span><span class="sxs-lookup"><span data-stu-id="2c524-399">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="2c524-400">Aktarım yapılandırması</span><span class="sxs-lookup"><span data-stu-id="2c524-400">Transport configuration</span></span>

<span data-ttu-id="2c524-401">Libuv () kullanımını gerektiren projeler için <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="2c524-401">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="2c524-402">Uygulamanın proje dosyasına [Microsoft. AspNetCore. Server. Kestrel. Transport. libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) paketi için bir bağımlılık ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2c524-402">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="2c524-403">Şunu çağırın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="2c524-403">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="2c524-404">URL önekleri</span><span class="sxs-lookup"><span data-stu-id="2c524-404">URL prefixes</span></span>

<span data-ttu-id="2c524-405">`UseUrls`, `--urls` Komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı veya `ASPNETCORE_URLS` ortam değişkeni kullanılırken, URL önekleri aşağıdaki biçimlerden birinde olabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-405">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="2c524-406">Yalnızca HTTP URL ön ekleri geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="2c524-406">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="2c524-407">Kestrel, kullanılarak URL bağlamaları yapılandırılırken HTTPS 'YI desteklemez `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="2c524-407">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="2c524-408">Bağlantı noktası numarası olan IPv4 adresi</span><span class="sxs-lookup"><span data-stu-id="2c524-408">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="2c524-409">`0.0.0.0` Tüm IPv4 adreslerine bağlanan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="2c524-409">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="2c524-410">Bağlantı noktası numarasına sahip IPv6 adresi</span><span class="sxs-lookup"><span data-stu-id="2c524-410">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="2c524-411">`[::]` , IPv4 'un IPv6 eşdeğeridir `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="2c524-411">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="2c524-412">Bağlantı noktası numarası olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="2c524-412">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="2c524-413">, Ve ana bilgisayar adları `*` `+` özel değildir.</span><span class="sxs-lookup"><span data-stu-id="2c524-413">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="2c524-414">Geçerli bir IP adresi olarak tanınmayan veya `localhost` tüm IPv4 ve IPv6 IP 'lerine bağlanan her şey.</span><span class="sxs-lookup"><span data-stu-id="2c524-414">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="2c524-415">Farklı ana bilgisayar adlarını aynı bağlantı noktasında farklı ASP.NET Core uygulamalarına bağlamak için, [HTTP.sys](xref:fundamentals/servers/httpsys) veya IIS, NGINX veya Apache gibi bir ters proxy sunucusunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="2c524-415">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="2c524-416">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="2c524-416">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="2c524-417">Port numarası `localhost` veya bağlantı noktası numarası ile geri döngü IP 'si olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="2c524-417">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="2c524-418">Belirtildiğinde `localhost` , Kestrel hem IPv4 hem de IPv6 geri döngü arabirimlerini bağlamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="2c524-418">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="2c524-419">İstenen bağlantı noktası, herhangi bir geri döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="2c524-419">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="2c524-420">Herhangi bir geri döngü arabirimi başka bir nedenle kullanılamıyorsa (genellikle IPv6 desteklenmediği için), Kestrel bir uyarı kaydeder.</span><span class="sxs-lookup"><span data-stu-id="2c524-420">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="2c524-421">Konak filtreleme</span><span class="sxs-lookup"><span data-stu-id="2c524-421">Host filtering</span></span>

<span data-ttu-id="2c524-422">Kestrel gibi önekleri temel alarak yapılandırmayı desteklese `http://example.com:5000` de, Kestrel büyük ölçüde ana bilgisayar adını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="2c524-422">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="2c524-423">Ana bilgisayar `localhost` , geri döngü adreslerine bağlama için kullanılan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="2c524-423">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="2c524-424">Açık IP adresi dışındaki tüm ana bilgisayar tüm genel IP adreslerine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="2c524-424">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="2c524-425">`Host` Üstbilgiler doğrulanmadı.</span><span class="sxs-lookup"><span data-stu-id="2c524-425">`Host` headers aren't validated.</span></span>

<span data-ttu-id="2c524-426">Geçici bir çözüm olarak, ana bilgisayar filtreleme ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="2c524-426">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="2c524-427">Ana bilgisayar filtreleme ara yazılımı, ASP.NET Core uygulamaları için örtük olarak sunulan [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="2c524-427">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="2c524-428">Ara yazılım tarafından eklenir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ve şunları çağırır <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="2c524-428">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="2c524-429">Ana bilgisayar filtreleme ara yazılımı varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-429">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="2c524-430">Ara yazılımı etkinleştirmek için, `AllowedHosts` appSettings *üzerindeappsettings.js*bir anahtar tanımlayın / *. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="2c524-430">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="2c524-431">Değer, bağlantı noktası numaraları olmayan ana bilgisayar adlarının noktalı virgülle ayrılmış listesidir:</span><span class="sxs-lookup"><span data-stu-id="2c524-431">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="2c524-432">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="2c524-432">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="2c524-433">[Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer) da bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçenek içerir.</span><span class="sxs-lookup"><span data-stu-id="2c524-433">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="2c524-434">İletilen üstbilgiler ara yazılımı ve ana bilgisayar filtreleme ara yazılımı, farklı senaryolar için benzer işlevlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="2c524-434">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="2c524-435">`AllowedHosts`Iletilen üstbilgiler ara yazılımı ile, `Host` istekler ters bir ara sunucu veya yük dengeleyici ile iletilirken üst bilgi korunurken, bu işlem için uygun bir ayar vardır.</span><span class="sxs-lookup"><span data-stu-id="2c524-435">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="2c524-436">`AllowedHosts`Ana bilgisayar filtreleme ara yazılımı ile ayarlama, Kestrel herkese açık bir uç sunucu olarak veya `Host` üst bilgi doğrudan iletildiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2c524-436">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="2c524-437">Iletilen üstbilgiler ara yazılımı hakkında daha fazla bilgi için bkz <xref:host-and-deploy/proxy-load-balancer> ..</span><span class="sxs-lookup"><span data-stu-id="2c524-437">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="2c524-438">Kestrel, ASP.NET Core için platformlar arası [Web sunucusudur](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="2c524-438">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="2c524-439">Kestrel, ASP.NET Core proje şablonlarında varsayılan olarak bulunan Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="2c524-439">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="2c524-440">Kestrel aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="2c524-440">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="2c524-441">HTTPS</span><span class="sxs-lookup"><span data-stu-id="2c524-441">HTTPS</span></span>
* <span data-ttu-id="2c524-442">[WebSockets](https://github.com/aspnet/websockets) 'i etkinleştirmek için kullanılan donuk yükseltme</span><span class="sxs-lookup"><span data-stu-id="2c524-442">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="2c524-443">NGINX 'in arkasında yüksek performans için UNIX Yuvaları</span><span class="sxs-lookup"><span data-stu-id="2c524-443">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="2c524-444">HTTP/2 (macOS hariç &dagger; )</span><span class="sxs-lookup"><span data-stu-id="2c524-444">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="2c524-445">&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="2c524-445">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="2c524-446">Kestrel, .NET Core 'un desteklediği tüm platformlarda ve sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="2c524-446">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="2c524-447">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2c524-447">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="2c524-448">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="2c524-448">HTTP/2 support</span></span>

<span data-ttu-id="2c524-449">Aşağıdaki temel gereksinimler karşılanıyorsa, [http/2](https://httpwg.org/specs/rfc7540.html) ASP.NET Core uygulamalar için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="2c524-449">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="2c524-450">İşletim Sistemi&dagger;</span><span class="sxs-lookup"><span data-stu-id="2c524-450">Operating system&dagger;</span></span>
  * <span data-ttu-id="2c524-451">Windows Server 2016/Windows 10 veya üzeri&Dagger;</span><span class="sxs-lookup"><span data-stu-id="2c524-451">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="2c524-452">OpenSSL 1.0.2 veya üzerini içeren Linux (örneğin, Ubuntu 16,04 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="2c524-452">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="2c524-453">Hedef Framework: .NET Core 2,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="2c524-453">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="2c524-454">[Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı</span><span class="sxs-lookup"><span data-stu-id="2c524-454">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="2c524-455">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="2c524-455">TLS 1.2 or later connection</span></span>

<span data-ttu-id="2c524-456">&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="2c524-456">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="2c524-457">&Dagger;Kestrel, Windows Server 2012 R2 ve Windows 8.1 'de HTTP/2 için sınırlı destek içerir.</span><span class="sxs-lookup"><span data-stu-id="2c524-457">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="2c524-458">Bu işletim sistemlerinde kullanılabilir olan desteklenen TLS şifre paketlerinin listesi sınırlı olduğundan destek sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-458">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="2c524-459">TLS bağlantılarının güvenliğini sağlamak için Eliptik Eğri dijital Imza algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-459">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="2c524-460">Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="2c524-460">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="2c524-461">HTTP/2 varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-461">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="2c524-462">Yapılandırma hakkında daha fazla bilgi için [Kestrel Options](#kestrel-options) ve [Listenoptions. Protocols](#listenoptionsprotocols) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="2c524-462">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="2c524-463">Ters ara sunucu ile Kestrel ne zaman kullanılır?</span><span class="sxs-lookup"><span data-stu-id="2c524-463">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="2c524-464">Kestrel, kendisi veya [Internet Information Services (IIS)](https://www.iis.net/), [NGINX](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi bir *ters ara sunucu*ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-464">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="2c524-465">Ters proxy sunucusu, ağdan gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="2c524-465">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="2c524-466">Sınır (Internet 'e yönelik) Web sunucusu olarak kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2c524-466">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel, ters proxy sunucusu olmadan doğrudan Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="2c524-468">Ters Proxy yapılandırmasında kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2c524-468">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel, IIS, NGINX veya Apache gibi bir ters ara sunucu üzerinden Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="2c524-470">İki yapılandırma de, ters ara sunucu sunucusuyla veya olmadan, desteklenen bir barındırma yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="2c524-470">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="2c524-471">Ters proxy sunucusu olmayan bir uç sunucu olarak kullanılan Kestrel, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="2c524-471">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="2c524-472">Kestrel bir bağlantı noktasını dinlemek üzere yapılandırıldığında, Kestrel isteklerin üst bilgilerinden bağımsız olarak bu bağlantı noktası için tüm trafiği işler `Host` .</span><span class="sxs-lookup"><span data-stu-id="2c524-472">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="2c524-473">Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel 'e iletme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="2c524-473">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="2c524-474">Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucu kullanılması iyi bir seçim olabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-474">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="2c524-475">Ters proxy:</span><span class="sxs-lookup"><span data-stu-id="2c524-475">A reverse proxy:</span></span>

* <span data-ttu-id="2c524-476">, Barındırdığı uygulamaların açığa çıkarılan genel yüzey alanını sınırlayabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-476">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="2c524-477">Ek bir yapılandırma ve savunma katmanı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="2c524-477">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="2c524-478">, Mevcut altyapıyla daha iyi tümleşebilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-478">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="2c524-479">Yük dengelemeyi ve güvenli iletişim (HTTPS) yapılandırmasını kolaylaştırın.</span><span class="sxs-lookup"><span data-stu-id="2c524-479">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="2c524-480">Yalnızca ters proxy sunucusu bir X. 509.440 sertifikası gerektirir ve bu sunucu, düz HTTP kullanarak, iç ağdaki uygulamanın sunucularıyla iletişim kurabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-480">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="2c524-481">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="2c524-481">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="2c524-482">ASP.NET Core uygulamalarında Kestrel kullanma</span><span class="sxs-lookup"><span data-stu-id="2c524-482">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="2c524-483">Microsoft. [AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) paketi [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="2c524-483">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="2c524-484">ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır.</span><span class="sxs-lookup"><span data-stu-id="2c524-484">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="2c524-485">*Program.cs*' de, şablon kodu çağırır <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ve bu da <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> arka planda çağrı yapılır.</span><span class="sxs-lookup"><span data-stu-id="2c524-485">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="2c524-486">Ana bilgisayarı hakkında daha fazla bilgi için `CreateDefaultBuilder` , uygulamasının *konak ayarlama* bölümüne bakın <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="2c524-486">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="2c524-487">Çağrıldıktan sonra ek yapılandırma sağlamak için `CreateDefaultBuilder` şunu kullanın `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="2c524-487">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="2c524-488">Uygulama, `CreateDefaultBuilder` Konağı kurmak için çağırmazsa, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> çağrılmadan **önce** çağırın `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="2c524-488">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="2c524-489">Kestrel seçenekleri</span><span class="sxs-lookup"><span data-stu-id="2c524-489">Kestrel options</span></span>

<span data-ttu-id="2c524-490">Kestrel Web sunucusu, Internet 'e yönelik dağıtımlarda özellikle yararlı olan kısıtlama yapılandırma seçeneklerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="2c524-490">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="2c524-491">Sınıfının özelliğinde kısıtlamaları ayarlayın <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> .</span><span class="sxs-lookup"><span data-stu-id="2c524-491">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="2c524-492">`Limits`Özelliği, sınıfının bir örneğini barındırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> .</span><span class="sxs-lookup"><span data-stu-id="2c524-492">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="2c524-493">Aşağıdaki örnekler <xref:Microsoft.AspNetCore.Server.Kestrel.Core> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="2c524-493">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="2c524-494">Aşağıdaki örneklerde C# kodunda yapılandırılan Kestrel seçenekleri de bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-494">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="2c524-495">Örneğin, dosya yapılandırma sağlayıcısı Kestrel yapılandırmasını bir *appsettings.js* veya appSettings 'ten yükleyebilir *. { Environment}. JSON* dosyası:</span><span class="sxs-lookup"><span data-stu-id="2c524-495">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="2c524-496">Aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="2c524-496">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="2c524-497">Kestrel 'i yapılandırma `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2c524-497">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="2c524-498">Sınıfına bir örneği `IConfiguration` ekleyin `Startup` .</span><span class="sxs-lookup"><span data-stu-id="2c524-498">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="2c524-499">Aşağıdaki örnek, eklenen yapılandırmanın özelliğe atandığını varsayar `Configuration` .</span><span class="sxs-lookup"><span data-stu-id="2c524-499">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="2c524-500">İçinde `Startup.ConfigureServices` , `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="2c524-500">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="2c524-501">Ana bilgisayarı oluştururken Kestrel yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="2c524-501">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="2c524-502">*Program.cs*' de, `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="2c524-502">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="2c524-503">Yukarıdaki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.</span><span class="sxs-lookup"><span data-stu-id="2c524-503">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="2c524-504">Etkin tut zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="2c524-504">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="2c524-505">[Etkin tutma zaman aşımını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2c524-505">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="2c524-506">Varsayılan olarak 2 dakikadır.</span><span class="sxs-lookup"><span data-stu-id="2c524-506">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="2c524-507">İstemci bağlantıları üst sınırı</span><span class="sxs-lookup"><span data-stu-id="2c524-507">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="2c524-508">En fazla eş zamanlı açık TCP bağlantısı sayısı tüm uygulama için aşağıdaki kodla ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="2c524-508">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="2c524-509">HTTP veya HTTPS 'den başka bir protokole (örneğin, bir WebSockets isteğinde) yükseltilen bağlantılara yönelik ayrı bir sınır vardır.</span><span class="sxs-lookup"><span data-stu-id="2c524-509">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="2c524-510">Bir bağlantı yükseltildikten sonra, bu sınıra göre sayılmaz `MaxConcurrentConnections` .</span><span class="sxs-lookup"><span data-stu-id="2c524-510">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="2c524-511">En fazla bağlantı sayısı, varsayılan olarak sınırsız (null).</span><span class="sxs-lookup"><span data-stu-id="2c524-511">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="2c524-512">En büyük istek gövdesi boyutu</span><span class="sxs-lookup"><span data-stu-id="2c524-512">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="2c524-513">Varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu değer yaklaşık 28,6 MB 'tır.</span><span class="sxs-lookup"><span data-stu-id="2c524-513">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="2c524-514">ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yaklaşım, <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="2c524-514">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="2c524-515">Her istekte uygulama için kısıtlamanın nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="2c524-515">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="2c524-516">Ara yazılım içindeki belirli bir istek üzerindeki ayarı geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="2c524-516">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="2c524-517">Uygulama isteği okumaya başladıktan sonra bir istek üzerindeki sınırı yapılandırırsa, bir özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="2c524-517">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="2c524-518">`IsReadOnly`Özelliğin salt okuma durumunda olup olmadığını belirten bir özellik vardır. Bu, `MaxRequestBodySize` sınırı yapılandırmanın çok geç olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="2c524-518">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="2c524-519">Bir uygulama [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)arkasında [çalıştırıldığında](xref:host-and-deploy/iis/index#out-of-process-hosting-model) , IIS sınırı zaten ayarladığı için Kestrel 'nin istek gövdesi boyut sınırı devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="2c524-519">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="2c524-520">En az istek gövdesi veri hızı</span><span class="sxs-lookup"><span data-stu-id="2c524-520">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="2c524-521">Kestrel bayt/saniye cinsinden belirtilen fiyata ulaşan her saniye sonra denetler.</span><span class="sxs-lookup"><span data-stu-id="2c524-521">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="2c524-522">Oran en düşük değerin altına düşerse bağlantı zaman aşımına uğrar. Yetkisiz kullanım süresi, Kestrel 'in istemciye gönderme oranını en düşük süreye kadar artırabilme Bu süre boyunca fiyat denetlenmez.</span><span class="sxs-lookup"><span data-stu-id="2c524-522">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="2c524-523">Yetkisiz kullanım süresi, TCP yavaş başlatma nedeniyle başlangıçta verileri yavaş bir hızda gönderen bağlantıların bırakılmasını önlemeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="2c524-523">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="2c524-524">Varsayılan en düşük oran, 5 saniyelik bir yetkisiz kullanım süresi ile 240 bayt/saniye olur.</span><span class="sxs-lookup"><span data-stu-id="2c524-524">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="2c524-525">Yanıt için bir minimum oran da geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="2c524-525">A minimum rate also applies to the response.</span></span> <span data-ttu-id="2c524-526">İstek sınırını ayarlamaya yönelik kod ve yanıt sınırı, `RequestBody` `Response` özellik ve arabirim adlarında olduğu gibi aynı olur.</span><span class="sxs-lookup"><span data-stu-id="2c524-526">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="2c524-527">*Program.cs*içinde en düşük veri hızlarının nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="2c524-527">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="2c524-528">Ara yazılım içindeki istek başına düşen minimum hız sınırlarını geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="2c524-528">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="2c524-529">`HttpContext.Features`İstek çoğullama için protokol desteği nedeniyle, http/2 için bir istek başına hız sınırlarını değiştirmek, http/2 istekleri için, önceki örnekte başvurulan hiçbir oran özelliği mevcut değil.</span><span class="sxs-lookup"><span data-stu-id="2c524-529">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="2c524-530">`KestrelServerOptions.Limits`Http/1. x ve http/2 bağlantılarına hala uygulanan sunucu genelindeki hız sınırları.</span><span class="sxs-lookup"><span data-stu-id="2c524-530">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="2c524-531">İstek üst bilgileri zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="2c524-531">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="2c524-532">Sunucunun istek üst bilgilerini alması için harcadığı en uzun süreyi alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2c524-532">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="2c524-533">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="2c524-533">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="2c524-534">Bağlantı başına en fazla akış</span><span class="sxs-lookup"><span data-stu-id="2c524-534">Maximum streams per connection</span></span>

<span data-ttu-id="2c524-535">`Http2.MaxStreamsPerConnection` HTTP/2 bağlantısı başına eşzamanlı istek akışı sayısını sınırlar.</span><span class="sxs-lookup"><span data-stu-id="2c524-535">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="2c524-536">Fazlalık akışlar reddedildi.</span><span class="sxs-lookup"><span data-stu-id="2c524-536">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="2c524-537">Varsayılan değer 100’dür.</span><span class="sxs-lookup"><span data-stu-id="2c524-537">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="2c524-538">Üst bilgi tablosu boyutu</span><span class="sxs-lookup"><span data-stu-id="2c524-538">Header table size</span></span>

<span data-ttu-id="2c524-539">HPACK kod çözücüsü HTTP/2 bağlantıları için HTTP üstbilgilerini açar.</span><span class="sxs-lookup"><span data-stu-id="2c524-539">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="2c524-540">`Http2.HeaderTableSize` HPACK kod çözücüsünün kullandığı üst bilgi sıkıştırma tablosunun boyutunu sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="2c524-540">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="2c524-541">Değer sekizli cinsinden sağlanır ve sıfırdan büyük olmalıdır (0).</span><span class="sxs-lookup"><span data-stu-id="2c524-541">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="2c524-542">Varsayılan değer 4096 ' dir.</span><span class="sxs-lookup"><span data-stu-id="2c524-542">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="2c524-543">En büyük çerçeve boyutu</span><span class="sxs-lookup"><span data-stu-id="2c524-543">Maximum frame size</span></span>

<span data-ttu-id="2c524-544">`Http2.MaxFrameSize` alacak HTTP/2 bağlantı çerçevesi yükünün en büyük boyutunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="2c524-544">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="2c524-545">Değer sekizli cinsinden sağlanır ve 2 ^ 14 (16.384) ile 2 ^ 24-1 (16.777.215) arasında olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-545">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="2c524-546">Varsayılan değer 2 ^ 14 ' dir (16.384).</span><span class="sxs-lookup"><span data-stu-id="2c524-546">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="2c524-547">En fazla istek üst bilgi boyutu</span><span class="sxs-lookup"><span data-stu-id="2c524-547">Maximum request header size</span></span>

<span data-ttu-id="2c524-548">`Http2.MaxRequestHeaderFieldSize` istek üst bilgisi değerlerinin sekizlisi cinsinden izin verilen en büyük boyutu belirtir.</span><span class="sxs-lookup"><span data-stu-id="2c524-548">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="2c524-549">Bu sınır, hem sıkıştırılmış hem de sıkıştırılmamış temsillerinde birlikte hem ad hem de değer için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="2c524-549">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="2c524-550">Değer sıfırdan büyük (0) olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-550">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="2c524-551">Varsayılan değer 8.192 ' dir.</span><span class="sxs-lookup"><span data-stu-id="2c524-551">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="2c524-552">İlk bağlantı pencere boyutu</span><span class="sxs-lookup"><span data-stu-id="2c524-552">Initial connection window size</span></span>

<span data-ttu-id="2c524-553">`Http2.InitialConnectionWindowSize` sunucu, bağlantı başına tüm istekler (akışlar) genelinde toplanan tek seferde sunucunun arabelleğe aldığı en fazla istek gövde verilerini bayt cinsinden gösterir.</span><span class="sxs-lookup"><span data-stu-id="2c524-553">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="2c524-554">İstekleri ile de sınırlıdır `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="2c524-554">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="2c524-555">Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-555">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="2c524-556">Varsayılan değer 128 KB 'tır (131.072).</span><span class="sxs-lookup"><span data-stu-id="2c524-556">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="2c524-557">İlk akış pencere boyutu</span><span class="sxs-lookup"><span data-stu-id="2c524-557">Initial stream window size</span></span>

<span data-ttu-id="2c524-558">`Http2.InitialStreamWindowSize` sunucu, istek başına bir kez (Stream) arabelleğe alınan en fazla istek gövde verilerini bayt cinsinden gösterir.</span><span class="sxs-lookup"><span data-stu-id="2c524-558">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="2c524-559">İstekleri ile de sınırlıdır `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="2c524-559">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="2c524-560">Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-560">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="2c524-561">Varsayılan değer 96 KB 'tır (98.304).</span><span class="sxs-lookup"><span data-stu-id="2c524-561">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="2c524-562">Zaman Uyumlu G/Ç</span><span class="sxs-lookup"><span data-stu-id="2c524-562">Synchronous I/O</span></span>

<span data-ttu-id="2c524-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> istek ve yanıt için zaman uyumlu g/ç 'ye izin verilip verilmediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="2c524-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="2c524-564">Varsayılan değer `true` .</span><span class="sxs-lookup"><span data-stu-id="2c524-564">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="2c524-565">Çok sayıda engelleme zaman uyumlu g/ç işlemi, iş parçacığı havuzuna yol açabilir, bu da uygulamanın yanıt vermemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="2c524-565">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="2c524-566">Yalnızca `AllowSynchronousIO` zaman uyumsuz g/ç desteklemeyen bir kitaplık kullanırken etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="2c524-566">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="2c524-567">Aşağıdaki örnek, zaman uyumlu g/ç 'yi sunar:</span><span class="sxs-lookup"><span data-stu-id="2c524-567">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="2c524-568">Diğer Kestrel seçenekleri ve limitleri hakkında daha fazla bilgi için bkz.:</span><span class="sxs-lookup"><span data-stu-id="2c524-568">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="2c524-569">Uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="2c524-569">Endpoint configuration</span></span>

<span data-ttu-id="2c524-570">Varsayılan olarak, ASP.NET Core bağlar:</span><span class="sxs-lookup"><span data-stu-id="2c524-570">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="2c524-571">`https://localhost:5001` (bir yerel geliştirme sertifikası varsa)</span><span class="sxs-lookup"><span data-stu-id="2c524-571">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="2c524-572">Kullanarak URL 'Leri belirtin:</span><span class="sxs-lookup"><span data-stu-id="2c524-572">Specify URLs using the:</span></span>

* <span data-ttu-id="2c524-573">`ASPNETCORE_URLS` ortam değişkeni.</span><span class="sxs-lookup"><span data-stu-id="2c524-573">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="2c524-574">`--urls` komut satırı bağımsız değişkeni.</span><span class="sxs-lookup"><span data-stu-id="2c524-574">`--urls` command-line argument.</span></span>
* <span data-ttu-id="2c524-575">`urls` Ana bilgisayar yapılandırma anahtarı.</span><span class="sxs-lookup"><span data-stu-id="2c524-575">`urls` host configuration key.</span></span>
* <span data-ttu-id="2c524-576">`UseUrls` genişletme yöntemi.</span><span class="sxs-lookup"><span data-stu-id="2c524-576">`UseUrls` extension method.</span></span>

<span data-ttu-id="2c524-577">Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2c524-577">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="2c524-578">Değeri noktalı virgülle ayrılmış bir liste olarak yapılandırın (örneğin, `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="2c524-578">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="2c524-579">Bu yaklaşımlar hakkında daha fazla bilgi için [sunucu URL 'leri](xref:fundamentals/host/web-host#server-urls) ve [geçersiz kılma yapılandırması](xref:fundamentals/host/web-host#override-configuration)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="2c524-579">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="2c524-580">Geliştirme sertifikası oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="2c524-580">A development certificate is created:</span></span>

* <span data-ttu-id="2c524-581">[.NET Core SDK](/dotnet/core/sdk) yüklendiği zaman.</span><span class="sxs-lookup"><span data-stu-id="2c524-581">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="2c524-582">[Geliştirme-CERT aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2c524-582">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="2c524-583">Bazı tarayıcılarda yerel geliştirme sertifikasına güvenmek için açık izin verilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="2c524-583">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="2c524-584">Proje şablonları, uygulamaları HTTPS üzerinde varsayılan olarak çalışacak şekilde yapılandırır ve [https yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.</span><span class="sxs-lookup"><span data-stu-id="2c524-584">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="2c524-585"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> URL öneklerini ve bağlantı noktalarını Kestrel için yapılandırmak üzere üzerinde çağrı veya Yöntemler.</span><span class="sxs-lookup"><span data-stu-id="2c524-585">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="2c524-586">`UseUrls`, `--urls` komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı ve `ASPNETCORE_URLS` ortam değişkeni de çalışır, ancak bu bölümün ilerleyen kısımlarında belirtilen sınırlamalara sahiptir (https uç noktası yapılandırması için varsayılan sertifika kullanılabilir olmalıdır).</span><span class="sxs-lookup"><span data-stu-id="2c524-586">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="2c524-587">`KestrelServerOptions` yapılandırmada</span><span class="sxs-lookup"><span data-stu-id="2c524-587">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="2c524-588">ConfigureEndpointDefaults Varsayılanları (eylem \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="2c524-588">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="2c524-589">`Action`Belirtilen her bir uç nokta için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="2c524-589">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="2c524-590">`ConfigureEndpointDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="2c524-590">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2c524-591">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="2c524-591">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="2c524-592">ConfigureHttpsDefaults (eylem \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="2c524-592">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="2c524-593">`Action`Her HTTPS uç noktası için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="2c524-593">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="2c524-594">`ConfigureHttpsDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="2c524-594">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2c524-595">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="2c524-595">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="2c524-596">Yapılandırma (Iconation)</span><span class="sxs-lookup"><span data-stu-id="2c524-596">Configure(IConfiguration)</span></span>

<span data-ttu-id="2c524-597">Bir as girişi alan Kestrel ayarlamak için bir yapılandırma yükleyicisi oluşturur <xref:Microsoft.Extensions.Configuration.IConfiguration> .</span><span class="sxs-lookup"><span data-stu-id="2c524-597">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="2c524-598">Yapılandırma, Kestrel için yapılandırma bölümünün kapsamına alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-598">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="2c524-599">ListenOptions. UseHttps</span><span class="sxs-lookup"><span data-stu-id="2c524-599">ListenOptions.UseHttps</span></span>

<span data-ttu-id="2c524-600">Kestrel 'i HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="2c524-600">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="2c524-601">`ListenOptions.UseHttps` uzantılardan</span><span class="sxs-lookup"><span data-stu-id="2c524-601">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="2c524-602">`UseHttps`: Kestrel 'i varsayılan sertifikayla HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="2c524-602">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="2c524-603">Varsayılan sertifika yapılandırılmamışsa bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2c524-603">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="2c524-604">`ListenOptions.UseHttps` parametrelere</span><span class="sxs-lookup"><span data-stu-id="2c524-604">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="2c524-605">`filename` , uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yolu ve dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-605">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="2c524-606">`password` X. 509.440 sertifika verilerine erişmek için parola gereklidir.</span><span class="sxs-lookup"><span data-stu-id="2c524-606">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="2c524-607">`configureOptions` , öğesini `Action` yapılandırmak için kullanılır `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="2c524-607">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="2c524-608">Döndürür `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="2c524-608">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="2c524-609">`storeName` , sertifikanın yükleneceği sertifika deposudur.</span><span class="sxs-lookup"><span data-stu-id="2c524-609">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="2c524-610">`subject` , sertifika için konu adıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-610">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="2c524-611">`allowInvalid` geçersiz sertifikaların, otomatik olarak imzalanan sertifikalar gibi göz önünde bulundurulmayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="2c524-611">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="2c524-612">`location` , sertifikanın yükleneceği mağaza konumudur.</span><span class="sxs-lookup"><span data-stu-id="2c524-612">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="2c524-613">`serverCertificate` , X. 509.440 sertifikasıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-613">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="2c524-614">Üretimde HTTPS 'nin açıkça yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2c524-614">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="2c524-615">En azından, varsayılan bir sertifika sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-615">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="2c524-616">Daha sonra açıklanan desteklenen yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="2c524-616">Supported configurations described next:</span></span>

* <span data-ttu-id="2c524-617">Yapılandırma yok</span><span class="sxs-lookup"><span data-stu-id="2c524-617">No configuration</span></span>
* <span data-ttu-id="2c524-618">Varsayılan sertifikayı yapılandırmadan Değiştir</span><span class="sxs-lookup"><span data-stu-id="2c524-618">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="2c524-619">Koddaki varsayılanları değiştirme</span><span class="sxs-lookup"><span data-stu-id="2c524-619">Change the defaults in code</span></span>

<span data-ttu-id="2c524-620">*Yapılandırma yok*</span><span class="sxs-lookup"><span data-stu-id="2c524-620">*No configuration*</span></span>

<span data-ttu-id="2c524-621">Kestrel tarihinde dinler `http://localhost:5000` ve `https://localhost:5001` (varsayılan bir sertifika varsa).</span><span class="sxs-lookup"><span data-stu-id="2c524-621">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="2c524-622">*Varsayılan sertifikayı yapılandırmadan Değiştir*</span><span class="sxs-lookup"><span data-stu-id="2c524-622">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="2c524-623">`CreateDefaultBuilder``Configure(context.Configuration.GetSection("Kestrel"))`Kestrel yapılandırmasını yüklemek için varsayılan olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="2c524-623">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="2c524-624">Varsayılan bir HTTPS uygulama ayarları yapılandırma şeması Kestrel için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-624">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="2c524-625">Disk üzerindeki bir dosyadan ya da bir sertifika deposundan kullanılacak URL 'Ler ve Sertifikalar dahil olmak üzere birden çok uç nokta yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="2c524-625">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="2c524-626">Aşağıdaki *appsettings.js* aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="2c524-626">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="2c524-627">Geçersiz sertifikaların kullanılmasına izin vermek için **Allowwınvalid** `true` ' i ayarlayın (örneğin, otomatik olarak imzalanan sertifikalar).</span><span class="sxs-lookup"><span data-stu-id="2c524-627">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="2c524-628">Bir sertifika belirtmeyen herhangi bir HTTPS uç noktası (aşağıdaki örnekte bulunan**httpsdefaultcert** ), **Sertifikalar** > **varsayılan** veya geliştirme sertifikası altında tanımlanan sertifikaya geri döner.</span><span class="sxs-lookup"><span data-stu-id="2c524-628">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="2c524-629">Herhangi bir sertifika düğümü için **yol** ve **parola** kullanmanın alternatifi sertifika deposu alanlarını kullanarak sertifikayı belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="2c524-629">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="2c524-630">Örneğin, **sertifika**  >  **varsayılan** sertifikası şu şekilde belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="2c524-630">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="2c524-631">Şema notları:</span><span class="sxs-lookup"><span data-stu-id="2c524-631">Schema notes:</span></span>

* <span data-ttu-id="2c524-632">Uç nokta adları büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-632">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="2c524-633">Örneğin, `HTTPS` ve `Https` geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="2c524-633">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="2c524-634">`Url`Her uç nokta için parametresi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="2c524-634">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="2c524-635">Bu parametrenin biçimi, en üst düzey `Urls` yapılandırma parametresiyle aynıdır, ancak tek bir değerle sınırlı olur.</span><span class="sxs-lookup"><span data-stu-id="2c524-635">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="2c524-636">Bu uç noktalar, üst düzey yapılandırmada tanımlananlar yerine `Urls` bunlara ekleme yerine bunların yerini alır.</span><span class="sxs-lookup"><span data-stu-id="2c524-636">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="2c524-637">Kullanılarak kodda tanımlanan uç noktalar `Listen` yapılandırma bölümünde tanımlanan uç noktalar ile birikimlidir.</span><span class="sxs-lookup"><span data-stu-id="2c524-637">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="2c524-638">Bu `Certificate` bölüm isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-638">The `Certificate` section is optional.</span></span> <span data-ttu-id="2c524-639">`Certificate`Bölüm belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2c524-639">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="2c524-640">Kullanılabilir varsayılan değer yoksa, sunucu bir özel durum oluşturur ve başlayamaz.</span><span class="sxs-lookup"><span data-stu-id="2c524-640">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="2c524-641">Bu `Certificate` bölüm hem **yol** &ndash; **parolasını** hem de **Konu** &ndash; **deposu** sertifikalarını destekler.</span><span class="sxs-lookup"><span data-stu-id="2c524-641">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="2c524-642">Herhangi bir sayıda uç nokta, bağlantı noktası çakışmalarına neden olmadıkları sürece bu şekilde tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-642">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="2c524-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))` yapılandırılmış bir `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` uç noktanın ayarlarını tamamlamak için kullanılabilecek bir yöntemi olan bir döndürür:</span><span class="sxs-lookup"><span data-stu-id="2c524-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="2c524-644">`KestrelServerOptions.ConfigurationLoader` , tarafından sağlana gibi var olan yükleyicisindeki yinelemeye devam etmek için doğrudan erişilebilir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="2c524-644">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="2c524-645">Her uç noktanın yapılandırma bölümü, `Endpoint` özel ayarların okunabilmesi için yöntemindeki seçeneklerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-645">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="2c524-646">Birden çok yapılandırma, `options.Configure(context.Configuration.GetSection("{SECTION}"))` başka bir bölümle yeniden çağırarak yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-646">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="2c524-647">Önceki örneklerde açıkça çağrılmadığı takdirde, yalnızca son yapılandırma kullanılır `Load` .</span><span class="sxs-lookup"><span data-stu-id="2c524-647">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="2c524-648">Metapackage, `Load` varsayılan yapılandırma bölümünün değiştirilmesini sağlayacak şekilde çağırmıyor.</span><span class="sxs-lookup"><span data-stu-id="2c524-648">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="2c524-649">`KestrelConfigurationLoader``Listen`API ailesini `KestrelServerOptions` `Endpoint` aşırı yükleme olarak yansıtır, bu nedenle kod ve yapılandırma uç noktaları aynı yerde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-649">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="2c524-650">Bu aşırı yüklemeler adları kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları kullanır.</span><span class="sxs-lookup"><span data-stu-id="2c524-650">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="2c524-651">*Koddaki varsayılanları değiştirme*</span><span class="sxs-lookup"><span data-stu-id="2c524-651">*Change the defaults in code*</span></span>

<span data-ttu-id="2c524-652">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` `ListenOptions` `HttpsConnectionAdapterOptions` , önceki senaryoda belirtilen varsayılan sertifikayı geçersiz kılma dahil, ve için varsayılan ayarları değiştirmek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-652">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="2c524-653">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-653">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="2c524-654">*SNı için Kestrel desteği*</span><span class="sxs-lookup"><span data-stu-id="2c524-654">*Kestrel support for SNI*</span></span>

<span data-ttu-id="2c524-655">[Sunucu adı belirtme (SNı)](https://tools.ietf.org/html/rfc6066#section-3) , aynı IP adresi ve bağlantı noktası üzerinde birden fazla etki alanını barındırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-655">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="2c524-656">SNı 'nin çalışması için, istemci, TLS el sıkışması sırasında güvenli oturum ana bilgisayar adını, sunucunun doğru sertifikayı sağlayabilmesi için gönderir.</span><span class="sxs-lookup"><span data-stu-id="2c524-656">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="2c524-657">İstemci, TLS anlaşmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için bulunan sertifikayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="2c524-657">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="2c524-658">Kestrel, geri çağırma aracılığıyla SNı destekler `ServerCertificateSelector` .</span><span class="sxs-lookup"><span data-stu-id="2c524-658">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="2c524-659">Geri çağırma, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesini sağlamak için bağlantı başına bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="2c524-659">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="2c524-660">SNı desteği şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="2c524-660">SNI support requires:</span></span>

* <span data-ttu-id="2c524-661">Hedef Framework `netcoreapp2.1` veya sonraki sürümlerde çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="2c524-661">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="2c524-662">`net461`Veya sonraki sürümlerde, geri çağırma çağrılır, ancak `name` her zaman olur `null` .</span><span class="sxs-lookup"><span data-stu-id="2c524-662">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="2c524-663">`name`Ayrıca, `null` ISTEMCI, TLS el sıkışmasının ana bilgisayar adı parametresini sağlamıyorsa de olur.</span><span class="sxs-lookup"><span data-stu-id="2c524-663">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="2c524-664">Tüm Web siteleri aynı Kestrel örneğinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="2c524-664">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="2c524-665">Kestrel, bir IP adresi ve bağlantı noktasının bir ters proxy olmadan birden çok örnek arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="2c524-665">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="2c524-666">Bağlantı günlüğü</span><span class="sxs-lookup"><span data-stu-id="2c524-666">Connection logging</span></span>

<span data-ttu-id="2c524-667"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>Bir bağlantıda bayt düzeyinde iletişim Için hata ayıklama düzeyi günlüklerini yayma çağrısı.</span><span class="sxs-lookup"><span data-stu-id="2c524-667">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="2c524-668">Bağlantı günlüğü, TLS şifreleme ve proxy 'nin arkasındaki gibi alt düzey iletişimde sorunları gidermeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="2c524-668">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="2c524-669">`UseConnectionLogging`Daha önce yerleştirilmişse `UseHttps` , şifrelenmiş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-669">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="2c524-670">`UseConnectionLogging`Öğesinden sonra yerleştirilmişse `UseHttps` , şifresi çözülmüş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-670">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="2c524-671">TCP yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="2c524-671">Bind to a TCP socket</span></span>

<span data-ttu-id="2c524-672"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Yöntemi BIR TCP yuvasına bağlanır ve bir seçenek lambda X. 509.440 sertifika yapılandırmasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="2c524-672">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="2c524-673">Örnek, HTTPS 'yi ile bir uç nokta için yapılandırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="2c524-673">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="2c524-674">Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak üzere aynı API 'yi kullanın.</span><span class="sxs-lookup"><span data-stu-id="2c524-674">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="2c524-675">UNIX yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="2c524-675">Bind to a Unix socket</span></span>

<span data-ttu-id="2c524-676"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>Aşağıdaki örnekte gösterildiği gibi NGINX ile gelişmiş performans için ile birlikte bir UNIX yuvası dinleyin:</span><span class="sxs-lookup"><span data-stu-id="2c524-676">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="2c524-677">NGINX confiuguration dosyasında, `server`  >  `location`  >  `proxy_pass` girdisini olarak ayarlayın `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="2c524-677">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="2c524-678">`{KESTREL SOCKET}` , için belirtilen yuvanın adıdır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (örneğin, `kestrel-test.sock` Önceki örnekte).</span><span class="sxs-lookup"><span data-stu-id="2c524-678">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="2c524-679">Yuvanın NGINX tarafından yazılabilir olduğundan emin olun (örneğin, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="2c524-679">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="2c524-680">Bağlantı noktası 0</span><span class="sxs-lookup"><span data-stu-id="2c524-680">Port 0</span></span>

<span data-ttu-id="2c524-681">Bağlantı noktası numarası `0` belirtildiğinde, Kestrel dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır.</span><span class="sxs-lookup"><span data-stu-id="2c524-681">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="2c524-682">Aşağıdaki örnek, çalışma zamanında Kestrel gerçekte hangi bağlantı noktasının gerçekten bağlandığını nasıl belirleyeceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="2c524-682">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="2c524-683">Uygulama çalıştırıldığında konsol penceresi çıkışı, uygulamanın erişilebileceği dinamik bağlantı noktasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="2c524-683">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="2c524-684">Sınırlamalar</span><span class="sxs-lookup"><span data-stu-id="2c524-684">Limitations</span></span>

<span data-ttu-id="2c524-685">Aşağıdaki yaklaşımlar ile uç noktaları yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="2c524-685">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="2c524-686">`--urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="2c524-686">`--urls` command-line argument</span></span>
* <span data-ttu-id="2c524-687">`urls` Ana bilgisayar yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="2c524-687">`urls` host configuration key</span></span>
* <span data-ttu-id="2c524-688">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="2c524-688">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="2c524-689">Bu yöntemler, kodun Kestrel dışındaki sunucularla çalışmasını sağlamak için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-689">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="2c524-690">Ancak, aşağıdaki sınırlamalara dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="2c524-690">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="2c524-691">HTTPS uç noktası yapılandırmasında varsayılan bir sertifika sağlanmadığı sürece HTTPS bu yaklaşımlar ile kullanılamaz (örneğin, `KestrelServerOptions` Bu konunun önceki kısımlarında gösterildiği gibi yapılandırma veya yapılandırma dosyası kullanılıyor).</span><span class="sxs-lookup"><span data-stu-id="2c524-691">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="2c524-692">Hem hem de `Listen` `UseUrls` yaklaşımları aynı anda kullanıldığında, uç noktalar `Listen` `UseUrls` uç noktaları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="2c524-692">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="2c524-693">IIS uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="2c524-693">IIS endpoint configuration</span></span>

<span data-ttu-id="2c524-694">IIS kullanırken, IIS geçersiz kılma bağlamaları için URL bağlamaları veya ya da tarafından ayarlanır `Listen` `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="2c524-694">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="2c524-695">Daha fazla bilgi için [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="2c524-695">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="2c524-696">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="2c524-696">ListenOptions.Protocols</span></span>

<span data-ttu-id="2c524-697">`Protocols`Özelliği, `HttpProtocols` bir bağlantı uç noktasında veya sunucu IÇIN etkin HTTP protokollerini () belirler.</span><span class="sxs-lookup"><span data-stu-id="2c524-697">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="2c524-698">`Protocols`Sabit listesinden özelliğe bir değer atayın `HttpProtocols` .</span><span class="sxs-lookup"><span data-stu-id="2c524-698">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="2c524-699">`HttpProtocols` sabit listesi değeri</span><span class="sxs-lookup"><span data-stu-id="2c524-699">`HttpProtocols` enum value</span></span> | <span data-ttu-id="2c524-700">Bağlantı protokolü izin verildi</span><span class="sxs-lookup"><span data-stu-id="2c524-700">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="2c524-701">Yalnızca HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="2c524-701">HTTP/1.1 only.</span></span> <span data-ttu-id="2c524-702">, TLS olmadan veya ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-702">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="2c524-703">Yalnızca HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2c524-703">HTTP/2 only.</span></span> <span data-ttu-id="2c524-704">Yalnızca istemci [önceki bir bilgi modunu](https://tools.ietf.org/html/rfc7540#section-3.4)DESTEKLIYORSA, TLS olmadan kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-704">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="2c524-705">HTTP/1.1 ve HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2c524-705">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="2c524-706">HTTP/2 bir TLS ve [uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı gerektirir; Aksi takdirde, bağlantı varsayılan olarak HTTP/1.1 ' dir.</span><span class="sxs-lookup"><span data-stu-id="2c524-706">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="2c524-707">Varsayılan protokol HTTP/1.1 ' dir.</span><span class="sxs-lookup"><span data-stu-id="2c524-707">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="2c524-708">HTTP/2 için TLS kısıtlamaları:</span><span class="sxs-lookup"><span data-stu-id="2c524-708">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="2c524-709">TLS sürüm 1,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="2c524-709">TLS version 1.2 or later</span></span>
* <span data-ttu-id="2c524-710">Yeniden anlaşma devre dışı</span><span class="sxs-lookup"><span data-stu-id="2c524-710">Renegotiation disabled</span></span>
* <span data-ttu-id="2c524-711">Sıkıştırma devre dışı</span><span class="sxs-lookup"><span data-stu-id="2c524-711">Compression disabled</span></span>
* <span data-ttu-id="2c524-712">En az kısa ömürlü anahtar değişim boyutları:</span><span class="sxs-lookup"><span data-stu-id="2c524-712">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="2c524-713">Eliptik Eğri Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bit minimum</span><span class="sxs-lookup"><span data-stu-id="2c524-713">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="2c524-714">Sınırlı alan Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; : 2048 bit minimum</span><span class="sxs-lookup"><span data-stu-id="2c524-714">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="2c524-715">Şifre paketi engellenmedi</span><span class="sxs-lookup"><span data-stu-id="2c524-715">Cipher suite not blocked</span></span>

<span data-ttu-id="2c524-716">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; P-256 eliptik eğrisi &lbrack; `FIPS186` &rbrack; Varsayılan olarak desteklenir.</span><span class="sxs-lookup"><span data-stu-id="2c524-716">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="2c524-717">Aşağıdaki örnek, 8000 numaralı bağlantı noktasında HTTP/1.1 ve HTTP/2 bağlantılarına izin verir.</span><span class="sxs-lookup"><span data-stu-id="2c524-717">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="2c524-718">Bağlantılar, sağlanan bir sertifikayla TLS ile güvenlidir:</span><span class="sxs-lookup"><span data-stu-id="2c524-718">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="2c524-719">İsteğe bağlı olarak, `IConnectionAdapter` belirli şifrelemeler için bağlantı BAŞıNA TLS el sıkışmaları filtrelemek üzere bir uygulama oluşturun:</span><span class="sxs-lookup"><span data-stu-id="2c524-719">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="2c524-720">*Protokolü yapılandırmadan ayarla*</span><span class="sxs-lookup"><span data-stu-id="2c524-720">*Set the protocol from configuration*</span></span>

<span data-ttu-id="2c524-721"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>`serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`Kestrel yapılandırmasını yüklemek için varsayılan olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="2c524-721"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="2c524-722">Aşağıdaki *appsettings.js* örnekte, tüm Kestrel uç noktaları için varsayılan bir bağlantı protokolü (http/1.1 ve http/2) oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="2c524-722">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="2c524-723">Aşağıdaki yapılandırma dosyası örneği, belirli bir uç nokta için bağlantı protokolü oluşturur:</span><span class="sxs-lookup"><span data-stu-id="2c524-723">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="2c524-724">Yapılandırma tarafından ayarlanan kod geçersiz kılma değerlerinde belirtilen protokoller.</span><span class="sxs-lookup"><span data-stu-id="2c524-724">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="2c524-725">Aktarım yapılandırması</span><span class="sxs-lookup"><span data-stu-id="2c524-725">Transport configuration</span></span>

<span data-ttu-id="2c524-726">ASP.NET Core 2,1 sürümü ile Kestrel 'in varsayılan taşıması artık libuv ' d i temel değildir ancak bunun yerine yönetilen yuvaları temel alır.</span><span class="sxs-lookup"><span data-stu-id="2c524-726">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="2c524-727">Bu, çağrıyı yapan ve aşağıdaki paketlerden birine bağlı olan ASP.NET Core 2,0 2,1 uygulamalarının önemli bir değişikliği olur <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="2c524-727">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="2c524-728">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (doğrudan paket başvurusu)</span><span class="sxs-lookup"><span data-stu-id="2c524-728">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="2c524-729">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="2c524-729">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="2c524-730">Libuv kullanımını gerektiren projeler için:</span><span class="sxs-lookup"><span data-stu-id="2c524-730">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="2c524-731">Uygulamanın proje dosyasına [Microsoft. AspNetCore. Server. Kestrel. Transport. libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) paketi için bir bağımlılık ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2c524-731">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="2c524-732">Çağrı <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="2c524-732">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="2c524-733">URL önekleri</span><span class="sxs-lookup"><span data-stu-id="2c524-733">URL prefixes</span></span>

<span data-ttu-id="2c524-734">`UseUrls`, `--urls` Komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı veya `ASPNETCORE_URLS` ortam değişkeni kullanılırken, URL önekleri aşağıdaki biçimlerden birinde olabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-734">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="2c524-735">Yalnızca HTTP URL ön ekleri geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="2c524-735">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="2c524-736">Kestrel, kullanılarak URL bağlamaları yapılandırılırken HTTPS 'YI desteklemez `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="2c524-736">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="2c524-737">Bağlantı noktası numarası olan IPv4 adresi</span><span class="sxs-lookup"><span data-stu-id="2c524-737">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="2c524-738">`0.0.0.0` Tüm IPv4 adreslerine bağlanan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="2c524-738">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="2c524-739">Bağlantı noktası numarasına sahip IPv6 adresi</span><span class="sxs-lookup"><span data-stu-id="2c524-739">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="2c524-740">`[::]` , IPv4 'un IPv6 eşdeğeridir `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="2c524-740">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="2c524-741">Bağlantı noktası numarası olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="2c524-741">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="2c524-742">, Ve ana bilgisayar adları `*` `+` özel değildir.</span><span class="sxs-lookup"><span data-stu-id="2c524-742">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="2c524-743">Geçerli bir IP adresi olarak tanınmayan veya `localhost` tüm IPv4 ve IPv6 IP 'lerine bağlanan her şey.</span><span class="sxs-lookup"><span data-stu-id="2c524-743">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="2c524-744">Farklı ana bilgisayar adlarını aynı bağlantı noktasında farklı ASP.NET Core uygulamalarına bağlamak için, [HTTP.sys](xref:fundamentals/servers/httpsys) veya IIS, NGINX veya Apache gibi bir ters proxy sunucusunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="2c524-744">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="2c524-745">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="2c524-745">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="2c524-746">Port numarası `localhost` veya bağlantı noktası numarası ile geri döngü IP 'si olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="2c524-746">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="2c524-747">Belirtildiğinde `localhost` , Kestrel hem IPv4 hem de IPv6 geri döngü arabirimlerini bağlamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="2c524-747">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="2c524-748">İstenen bağlantı noktası, herhangi bir geri döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="2c524-748">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="2c524-749">Herhangi bir geri döngü arabirimi başka bir nedenle kullanılamıyorsa (genellikle IPv6 desteklenmediği için), Kestrel bir uyarı kaydeder.</span><span class="sxs-lookup"><span data-stu-id="2c524-749">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="2c524-750">Konak filtreleme</span><span class="sxs-lookup"><span data-stu-id="2c524-750">Host filtering</span></span>

<span data-ttu-id="2c524-751">Kestrel gibi önekleri temel alarak yapılandırmayı desteklese `http://example.com:5000` de, Kestrel büyük ölçüde ana bilgisayar adını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="2c524-751">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="2c524-752">Ana bilgisayar `localhost` , geri döngü adreslerine bağlama için kullanılan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="2c524-752">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="2c524-753">Açık IP adresi dışındaki tüm ana bilgisayar tüm genel IP adreslerine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="2c524-753">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="2c524-754">`Host` Üstbilgiler doğrulanmadı.</span><span class="sxs-lookup"><span data-stu-id="2c524-754">`Host` headers aren't validated.</span></span>

<span data-ttu-id="2c524-755">Geçici bir çözüm olarak, ana bilgisayar filtreleme ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="2c524-755">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="2c524-756">Ana bilgisayar filtreleme ara yazılımı, [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 veya 2,2) ' de yer alan [Microsoft. Aspnetcore. hostfiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="2c524-756">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="2c524-757">Ara yazılım tarafından eklenir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ve şunları çağırır <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="2c524-757">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="2c524-758">Ana bilgisayar filtreleme ara yazılımı varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-758">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="2c524-759">Ara yazılımı etkinleştirmek için, `AllowedHosts` appSettings *üzerindeappsettings.js*bir anahtar tanımlayın / *. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="2c524-759">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="2c524-760">Değer, bağlantı noktası numaraları olmayan ana bilgisayar adlarının noktalı virgülle ayrılmış listesidir:</span><span class="sxs-lookup"><span data-stu-id="2c524-760">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="2c524-761">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="2c524-761">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="2c524-762">[Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer) da bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçenek içerir.</span><span class="sxs-lookup"><span data-stu-id="2c524-762">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="2c524-763">İletilen üstbilgiler ara yazılımı ve ana bilgisayar filtreleme ara yazılımı, farklı senaryolar için benzer işlevlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="2c524-763">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="2c524-764">`AllowedHosts`Iletilen üstbilgiler ara yazılımı ile, `Host` istekler ters bir ara sunucu veya yük dengeleyici ile iletilirken üst bilgi korunurken, bu işlem için uygun bir ayar vardır.</span><span class="sxs-lookup"><span data-stu-id="2c524-764">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="2c524-765">`AllowedHosts`Ana bilgisayar filtreleme ara yazılımı ile ayarlama, Kestrel herkese açık bir uç sunucu olarak veya `Host` üst bilgi doğrudan iletildiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2c524-765">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="2c524-766">Iletilen üstbilgiler ara yazılımı hakkında daha fazla bilgi için bkz <xref:host-and-deploy/proxy-load-balancer> ..</span><span class="sxs-lookup"><span data-stu-id="2c524-766">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="2c524-767">Kestrel, ASP.NET Core için platformlar arası [Web sunucusudur](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="2c524-767">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="2c524-768">Kestrel, ASP.NET Core proje şablonlarında varsayılan olarak bulunan Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="2c524-768">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="2c524-769">Kestrel aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="2c524-769">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="2c524-770">HTTPS</span><span class="sxs-lookup"><span data-stu-id="2c524-770">HTTPS</span></span>
* <span data-ttu-id="2c524-771">[WebSockets](https://github.com/aspnet/websockets) 'i etkinleştirmek için kullanılan donuk yükseltme</span><span class="sxs-lookup"><span data-stu-id="2c524-771">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="2c524-772">NGINX 'in arkasında yüksek performans için UNIX Yuvaları</span><span class="sxs-lookup"><span data-stu-id="2c524-772">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="2c524-773">Kestrel, .NET Core 'un desteklediği tüm platformlarda ve sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="2c524-773">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="2c524-774">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2c524-774">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="2c524-775">Ters ara sunucu ile Kestrel ne zaman kullanılır?</span><span class="sxs-lookup"><span data-stu-id="2c524-775">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="2c524-776">Kestrel, kendisi veya [Internet Information Services (IIS)](https://www.iis.net/), [NGINX](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi bir *ters ara sunucu*ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-776">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="2c524-777">Ters proxy sunucusu, ağdan gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="2c524-777">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="2c524-778">Sınır (Internet 'e yönelik) Web sunucusu olarak kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2c524-778">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel, ters proxy sunucusu olmadan doğrudan Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="2c524-780">Ters Proxy yapılandırmasında kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2c524-780">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel, IIS, NGINX veya Apache gibi bir ters ara sunucu üzerinden Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="2c524-782">İki yapılandırma de, ters ara sunucu sunucusuyla veya olmadan, desteklenen bir barındırma yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="2c524-782">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="2c524-783">Ters proxy sunucusu olmayan bir uç sunucu olarak kullanılan Kestrel, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="2c524-783">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="2c524-784">Kestrel bir bağlantı noktasını dinlemek üzere yapılandırıldığında, Kestrel isteklerin üst bilgilerinden bağımsız olarak bu bağlantı noktası için tüm trafiği işler `Host` .</span><span class="sxs-lookup"><span data-stu-id="2c524-784">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="2c524-785">Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel 'e iletme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="2c524-785">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="2c524-786">Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucu kullanılması iyi bir seçim olabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-786">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="2c524-787">Ters proxy:</span><span class="sxs-lookup"><span data-stu-id="2c524-787">A reverse proxy:</span></span>

* <span data-ttu-id="2c524-788">, Barındırdığı uygulamaların açığa çıkarılan genel yüzey alanını sınırlayabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-788">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="2c524-789">Ek bir yapılandırma ve savunma katmanı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="2c524-789">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="2c524-790">, Mevcut altyapıyla daha iyi tümleşebilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-790">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="2c524-791">Yük dengelemeyi ve güvenli iletişim (HTTPS) yapılandırmasını kolaylaştırın.</span><span class="sxs-lookup"><span data-stu-id="2c524-791">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="2c524-792">Yalnızca ters proxy sunucusu bir X. 509.440 sertifikası gerektirir ve bu sunucu, düz HTTP kullanarak, iç ağdaki uygulamanın sunucularıyla iletişim kurabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-792">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="2c524-793">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="2c524-793">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="2c524-794">ASP.NET Core uygulamalarında Kestrel kullanma</span><span class="sxs-lookup"><span data-stu-id="2c524-794">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="2c524-795">Microsoft. [AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) paketi [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="2c524-795">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="2c524-796">ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır.</span><span class="sxs-lookup"><span data-stu-id="2c524-796">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="2c524-797">*Program.cs*' de, şablon kodu çağırır <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ve bu da <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> arka planda çağrı yapılır.</span><span class="sxs-lookup"><span data-stu-id="2c524-797">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="2c524-798">Çağrıldıktan sonra ek yapılandırma sağlamak için `CreateDefaultBuilder` şunu çağırın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="2c524-798">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="2c524-799">Ana bilgisayarı hakkında daha fazla bilgi için `CreateDefaultBuilder` , uygulamasının *konak ayarlama* bölümüne bakın <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="2c524-799">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="2c524-800">Kestrel seçenekleri</span><span class="sxs-lookup"><span data-stu-id="2c524-800">Kestrel options</span></span>

<span data-ttu-id="2c524-801">Kestrel Web sunucusu, Internet 'e yönelik dağıtımlarda özellikle yararlı olan kısıtlama yapılandırma seçeneklerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="2c524-801">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="2c524-802">Sınıfının özelliğinde kısıtlamaları ayarlayın <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> .</span><span class="sxs-lookup"><span data-stu-id="2c524-802">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="2c524-803">`Limits`Özelliği, sınıfının bir örneğini barındırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> .</span><span class="sxs-lookup"><span data-stu-id="2c524-803">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="2c524-804">Aşağıdaki örnekler <xref:Microsoft.AspNetCore.Server.Kestrel.Core> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="2c524-804">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="2c524-805">Aşağıdaki örneklerde C# kodunda yapılandırılan Kestrel seçenekleri de bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-805">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="2c524-806">Örneğin, dosya yapılandırma sağlayıcısı Kestrel yapılandırmasını bir *appsettings.js* veya appSettings 'ten yükleyebilir *. { Environment}. JSON* dosyası:</span><span class="sxs-lookup"><span data-stu-id="2c524-806">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="2c524-807">Aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="2c524-807">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="2c524-808">Kestrel 'i yapılandırma `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2c524-808">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="2c524-809">Sınıfına bir örneği `IConfiguration` ekleyin `Startup` .</span><span class="sxs-lookup"><span data-stu-id="2c524-809">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="2c524-810">Aşağıdaki örnek, eklenen yapılandırmanın özelliğe atandığını varsayar `Configuration` .</span><span class="sxs-lookup"><span data-stu-id="2c524-810">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="2c524-811">İçinde `Startup.ConfigureServices` , `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="2c524-811">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="2c524-812">Ana bilgisayarı oluştururken Kestrel yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="2c524-812">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="2c524-813">*Program.cs*' de, `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="2c524-813">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="2c524-814">Yukarıdaki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.</span><span class="sxs-lookup"><span data-stu-id="2c524-814">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="2c524-815">Etkin tut zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="2c524-815">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="2c524-816">[Etkin tutma zaman aşımını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2c524-816">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="2c524-817">Varsayılan olarak 2 dakikadır.</span><span class="sxs-lookup"><span data-stu-id="2c524-817">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="2c524-818">İstemci bağlantıları üst sınırı</span><span class="sxs-lookup"><span data-stu-id="2c524-818">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="2c524-819">En fazla eş zamanlı açık TCP bağlantısı sayısı tüm uygulama için aşağıdaki kodla ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="2c524-819">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="2c524-820">HTTP veya HTTPS 'den başka bir protokole (örneğin, bir WebSockets isteğinde) yükseltilen bağlantılara yönelik ayrı bir sınır vardır.</span><span class="sxs-lookup"><span data-stu-id="2c524-820">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="2c524-821">Bir bağlantı yükseltildikten sonra, bu sınıra göre sayılmaz `MaxConcurrentConnections` .</span><span class="sxs-lookup"><span data-stu-id="2c524-821">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="2c524-822">En fazla bağlantı sayısı, varsayılan olarak sınırsız (null).</span><span class="sxs-lookup"><span data-stu-id="2c524-822">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="2c524-823">En büyük istek gövdesi boyutu</span><span class="sxs-lookup"><span data-stu-id="2c524-823">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="2c524-824">Varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu değer yaklaşık 28,6 MB 'tır.</span><span class="sxs-lookup"><span data-stu-id="2c524-824">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="2c524-825">ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yaklaşım, <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="2c524-825">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="2c524-826">Her istekte uygulama için kısıtlamanın nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="2c524-826">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="2c524-827">Ara yazılım içindeki belirli bir istek üzerindeki ayarı geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="2c524-827">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="2c524-828">Uygulama isteği okumaya başladıktan sonra bir istek üzerindeki sınırı yapılandırırsa, bir özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="2c524-828">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="2c524-829">`IsReadOnly`Özelliğin salt okuma durumunda olup olmadığını belirten bir özellik vardır. Bu, `MaxRequestBodySize` sınırı yapılandırmanın çok geç olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="2c524-829">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="2c524-830">Bir uygulama [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)arkasında [çalıştırıldığında](xref:host-and-deploy/iis/index#out-of-process-hosting-model) , IIS sınırı zaten ayarladığı için Kestrel 'nin istek gövdesi boyut sınırı devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="2c524-830">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="2c524-831">En az istek gövdesi veri hızı</span><span class="sxs-lookup"><span data-stu-id="2c524-831">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="2c524-832">Kestrel bayt/saniye cinsinden belirtilen fiyata ulaşan her saniye sonra denetler.</span><span class="sxs-lookup"><span data-stu-id="2c524-832">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="2c524-833">Oran en düşük değerin altına düşerse bağlantı zaman aşımına uğrar. Yetkisiz kullanım süresi, Kestrel 'in istemciye gönderme oranını en düşük süreye kadar artırabilme Bu süre boyunca fiyat denetlenmez.</span><span class="sxs-lookup"><span data-stu-id="2c524-833">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="2c524-834">Yetkisiz kullanım süresi, TCP yavaş başlatma nedeniyle başlangıçta verileri yavaş bir hızda gönderen bağlantıların bırakılmasını önlemeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="2c524-834">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="2c524-835">Varsayılan en düşük oran, 5 saniyelik bir yetkisiz kullanım süresi ile 240 bayt/saniye olur.</span><span class="sxs-lookup"><span data-stu-id="2c524-835">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="2c524-836">Yanıt için bir minimum oran da geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="2c524-836">A minimum rate also applies to the response.</span></span> <span data-ttu-id="2c524-837">İstek sınırını ayarlamaya yönelik kod ve yanıt sınırı, `RequestBody` `Response` özellik ve arabirim adlarında olduğu gibi aynı olur.</span><span class="sxs-lookup"><span data-stu-id="2c524-837">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="2c524-838">*Program.cs*içinde en düşük veri hızlarının nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="2c524-838">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="2c524-839">İstek üst bilgileri zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="2c524-839">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="2c524-840">Sunucunun istek üst bilgilerini alması için harcadığı en uzun süreyi alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="2c524-840">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="2c524-841">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="2c524-841">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="2c524-842">Zaman Uyumlu G/Ç</span><span class="sxs-lookup"><span data-stu-id="2c524-842">Synchronous I/O</span></span>

<span data-ttu-id="2c524-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> istek ve yanıt için zaman uyumlu g/ç 'ye izin verilip verilmediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="2c524-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="2c524-844">Varsayılan değer `true` .</span><span class="sxs-lookup"><span data-stu-id="2c524-844">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="2c524-845">Çok sayıda engelleme zaman uyumlu g/ç işlemi, iş parçacığı havuzuna yol açabilir, bu da uygulamanın yanıt vermemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="2c524-845">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="2c524-846">Yalnızca `AllowSynchronousIO` zaman uyumsuz g/ç desteklemeyen bir kitaplık kullanırken etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="2c524-846">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="2c524-847">Aşağıdaki örnek, zaman uyumlu g/ç 'yi devre dışı bırakır:</span><span class="sxs-lookup"><span data-stu-id="2c524-847">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="2c524-848">Diğer Kestrel seçenekleri ve limitleri hakkında daha fazla bilgi için bkz.:</span><span class="sxs-lookup"><span data-stu-id="2c524-848">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="2c524-849">Uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="2c524-849">Endpoint configuration</span></span>

<span data-ttu-id="2c524-850">Varsayılan olarak, ASP.NET Core bağlar:</span><span class="sxs-lookup"><span data-stu-id="2c524-850">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="2c524-851">`https://localhost:5001` (bir yerel geliştirme sertifikası varsa)</span><span class="sxs-lookup"><span data-stu-id="2c524-851">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="2c524-852">Kullanarak URL 'Leri belirtin:</span><span class="sxs-lookup"><span data-stu-id="2c524-852">Specify URLs using the:</span></span>

* <span data-ttu-id="2c524-853">`ASPNETCORE_URLS` ortam değişkeni.</span><span class="sxs-lookup"><span data-stu-id="2c524-853">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="2c524-854">`--urls` komut satırı bağımsız değişkeni.</span><span class="sxs-lookup"><span data-stu-id="2c524-854">`--urls` command-line argument.</span></span>
* <span data-ttu-id="2c524-855">`urls` Ana bilgisayar yapılandırma anahtarı.</span><span class="sxs-lookup"><span data-stu-id="2c524-855">`urls` host configuration key.</span></span>
* <span data-ttu-id="2c524-856">`UseUrls` genişletme yöntemi.</span><span class="sxs-lookup"><span data-stu-id="2c524-856">`UseUrls` extension method.</span></span>

<span data-ttu-id="2c524-857">Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2c524-857">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="2c524-858">Değeri noktalı virgülle ayrılmış bir liste olarak yapılandırın (örneğin, `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="2c524-858">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="2c524-859">Bu yaklaşımlar hakkında daha fazla bilgi için [sunucu URL 'leri](xref:fundamentals/host/web-host#server-urls) ve [geçersiz kılma yapılandırması](xref:fundamentals/host/web-host#override-configuration)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="2c524-859">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="2c524-860">Geliştirme sertifikası oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="2c524-860">A development certificate is created:</span></span>

* <span data-ttu-id="2c524-861">[.NET Core SDK](/dotnet/core/sdk) yüklendiği zaman.</span><span class="sxs-lookup"><span data-stu-id="2c524-861">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="2c524-862">[Geliştirme-CERT aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2c524-862">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="2c524-863">Bazı tarayıcılarda yerel geliştirme sertifikasına güvenmek için açık izin verilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="2c524-863">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="2c524-864">Proje şablonları, uygulamaları HTTPS üzerinde varsayılan olarak çalışacak şekilde yapılandırır ve [https yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.</span><span class="sxs-lookup"><span data-stu-id="2c524-864">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="2c524-865"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> URL öneklerini ve bağlantı noktalarını Kestrel için yapılandırmak üzere üzerinde çağrı veya Yöntemler.</span><span class="sxs-lookup"><span data-stu-id="2c524-865">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="2c524-866">`UseUrls`, `--urls` komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı ve `ASPNETCORE_URLS` ortam değişkeni de çalışır, ancak bu bölümün ilerleyen kısımlarında belirtilen sınırlamalara sahiptir (https uç noktası yapılandırması için varsayılan sertifika kullanılabilir olmalıdır).</span><span class="sxs-lookup"><span data-stu-id="2c524-866">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="2c524-867">`KestrelServerOptions` yapılandırmada</span><span class="sxs-lookup"><span data-stu-id="2c524-867">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="2c524-868">ConfigureEndpointDefaults Varsayılanları (eylem \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="2c524-868">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="2c524-869">`Action`Belirtilen her bir uç nokta için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="2c524-869">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="2c524-870">`ConfigureEndpointDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="2c524-870">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2c524-871">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="2c524-871">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="2c524-872">ConfigureHttpsDefaults (eylem \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="2c524-872">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="2c524-873">`Action`Her HTTPS uç noktası için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="2c524-873">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="2c524-874">`ConfigureHttpsDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="2c524-874">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2c524-875">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="2c524-875">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="2c524-876">Yapılandırma (Iconation)</span><span class="sxs-lookup"><span data-stu-id="2c524-876">Configure(IConfiguration)</span></span>

<span data-ttu-id="2c524-877">Bir as girişi alan Kestrel ayarlamak için bir yapılandırma yükleyicisi oluşturur <xref:Microsoft.Extensions.Configuration.IConfiguration> .</span><span class="sxs-lookup"><span data-stu-id="2c524-877">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="2c524-878">Yapılandırma, Kestrel için yapılandırma bölümünün kapsamına alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-878">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="2c524-879">ListenOptions. UseHttps</span><span class="sxs-lookup"><span data-stu-id="2c524-879">ListenOptions.UseHttps</span></span>

<span data-ttu-id="2c524-880">Kestrel 'i HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="2c524-880">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="2c524-881">`ListenOptions.UseHttps` uzantılardan</span><span class="sxs-lookup"><span data-stu-id="2c524-881">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="2c524-882">`UseHttps`: Kestrel 'i varsayılan sertifikayla HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="2c524-882">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="2c524-883">Varsayılan sertifika yapılandırılmamışsa bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="2c524-883">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="2c524-884">`ListenOptions.UseHttps` parametrelere</span><span class="sxs-lookup"><span data-stu-id="2c524-884">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="2c524-885">`filename` , uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yolu ve dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-885">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="2c524-886">`password` X. 509.440 sertifika verilerine erişmek için parola gereklidir.</span><span class="sxs-lookup"><span data-stu-id="2c524-886">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="2c524-887">`configureOptions` , öğesini `Action` yapılandırmak için kullanılır `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="2c524-887">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="2c524-888">Döndürür `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="2c524-888">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="2c524-889">`storeName` , sertifikanın yükleneceği sertifika deposudur.</span><span class="sxs-lookup"><span data-stu-id="2c524-889">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="2c524-890">`subject` , sertifika için konu adıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-890">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="2c524-891">`allowInvalid` geçersiz sertifikaların, otomatik olarak imzalanan sertifikalar gibi göz önünde bulundurulmayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="2c524-891">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="2c524-892">`location` , sertifikanın yükleneceği mağaza konumudur.</span><span class="sxs-lookup"><span data-stu-id="2c524-892">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="2c524-893">`serverCertificate` , X. 509.440 sertifikasıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-893">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="2c524-894">Üretimde HTTPS 'nin açıkça yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="2c524-894">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="2c524-895">En azından, varsayılan bir sertifika sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-895">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="2c524-896">Daha sonra açıklanan desteklenen yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="2c524-896">Supported configurations described next:</span></span>

* <span data-ttu-id="2c524-897">Yapılandırma yok</span><span class="sxs-lookup"><span data-stu-id="2c524-897">No configuration</span></span>
* <span data-ttu-id="2c524-898">Varsayılan sertifikayı yapılandırmadan Değiştir</span><span class="sxs-lookup"><span data-stu-id="2c524-898">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="2c524-899">Koddaki varsayılanları değiştirme</span><span class="sxs-lookup"><span data-stu-id="2c524-899">Change the defaults in code</span></span>

<span data-ttu-id="2c524-900">*Yapılandırma yok*</span><span class="sxs-lookup"><span data-stu-id="2c524-900">*No configuration*</span></span>

<span data-ttu-id="2c524-901">Kestrel tarihinde dinler `http://localhost:5000` ve `https://localhost:5001` (varsayılan bir sertifika varsa).</span><span class="sxs-lookup"><span data-stu-id="2c524-901">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="2c524-902">*Varsayılan sertifikayı yapılandırmadan Değiştir*</span><span class="sxs-lookup"><span data-stu-id="2c524-902">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="2c524-903">`CreateDefaultBuilder``Configure(context.Configuration.GetSection("Kestrel"))`Kestrel yapılandırmasını yüklemek için varsayılan olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="2c524-903">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="2c524-904">Varsayılan bir HTTPS uygulama ayarları yapılandırma şeması Kestrel için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-904">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="2c524-905">Disk üzerindeki bir dosyadan ya da bir sertifika deposundan kullanılacak URL 'Ler ve Sertifikalar dahil olmak üzere birden çok uç nokta yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="2c524-905">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="2c524-906">Aşağıdaki *appsettings.js* aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="2c524-906">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="2c524-907">Geçersiz sertifikaların kullanılmasına izin vermek için **Allowwınvalid** `true` ' i ayarlayın (örneğin, otomatik olarak imzalanan sertifikalar).</span><span class="sxs-lookup"><span data-stu-id="2c524-907">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="2c524-908">Bir sertifika belirtmeyen herhangi bir HTTPS uç noktası (aşağıdaki örnekte bulunan**httpsdefaultcert** ), **Sertifikalar** > **varsayılan** veya geliştirme sertifikası altında tanımlanan sertifikaya geri döner.</span><span class="sxs-lookup"><span data-stu-id="2c524-908">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="2c524-909">Herhangi bir sertifika düğümü için **yol** ve **parola** kullanmanın alternatifi sertifika deposu alanlarını kullanarak sertifikayı belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="2c524-909">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="2c524-910">Örneğin, **sertifika**  >  **varsayılan** sertifikası şu şekilde belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="2c524-910">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="2c524-911">Şema notları:</span><span class="sxs-lookup"><span data-stu-id="2c524-911">Schema notes:</span></span>

* <span data-ttu-id="2c524-912">Uç nokta adları büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-912">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="2c524-913">Örneğin, `HTTPS` ve `Https` geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="2c524-913">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="2c524-914">`Url`Her uç nokta için parametresi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="2c524-914">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="2c524-915">Bu parametrenin biçimi, en üst düzey `Urls` yapılandırma parametresiyle aynıdır, ancak tek bir değerle sınırlı olur.</span><span class="sxs-lookup"><span data-stu-id="2c524-915">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="2c524-916">Bu uç noktalar, üst düzey yapılandırmada tanımlananlar yerine `Urls` bunlara ekleme yerine bunların yerini alır.</span><span class="sxs-lookup"><span data-stu-id="2c524-916">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="2c524-917">Kullanılarak kodda tanımlanan uç noktalar `Listen` yapılandırma bölümünde tanımlanan uç noktalar ile birikimlidir.</span><span class="sxs-lookup"><span data-stu-id="2c524-917">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="2c524-918">Bu `Certificate` bölüm isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-918">The `Certificate` section is optional.</span></span> <span data-ttu-id="2c524-919">`Certificate`Bölüm belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2c524-919">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="2c524-920">Kullanılabilir varsayılan değer yoksa, sunucu bir özel durum oluşturur ve başlayamaz.</span><span class="sxs-lookup"><span data-stu-id="2c524-920">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="2c524-921">Bu `Certificate` bölüm hem **yol** &ndash; **parolasını** hem de **Konu** &ndash; **deposu** sertifikalarını destekler.</span><span class="sxs-lookup"><span data-stu-id="2c524-921">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="2c524-922">Herhangi bir sayıda uç nokta, bağlantı noktası çakışmalarına neden olmadıkları sürece bu şekilde tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-922">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="2c524-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))` yapılandırılmış bir `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` uç noktanın ayarlarını tamamlamak için kullanılabilecek bir yöntemi olan bir döndürür:</span><span class="sxs-lookup"><span data-stu-id="2c524-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="2c524-924">`KestrelServerOptions.ConfigurationLoader` , tarafından sağlana gibi var olan yükleyicisindeki yinelemeye devam etmek için doğrudan erişilebilir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="2c524-924">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="2c524-925">Her uç noktanın yapılandırma bölümü, `Endpoint` özel ayarların okunabilmesi için yöntemindeki seçeneklerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-925">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="2c524-926">Birden çok yapılandırma, `options.Configure(context.Configuration.GetSection("{SECTION}"))` başka bir bölümle yeniden çağırarak yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-926">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="2c524-927">Önceki örneklerde açıkça çağrılmadığı takdirde, yalnızca son yapılandırma kullanılır `Load` .</span><span class="sxs-lookup"><span data-stu-id="2c524-927">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="2c524-928">Metapackage, `Load` varsayılan yapılandırma bölümünün değiştirilmesini sağlayacak şekilde çağırmıyor.</span><span class="sxs-lookup"><span data-stu-id="2c524-928">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="2c524-929">`KestrelConfigurationLoader``Listen`API ailesini `KestrelServerOptions` `Endpoint` aşırı yükleme olarak yansıtır, bu nedenle kod ve yapılandırma uç noktaları aynı yerde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-929">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="2c524-930">Bu aşırı yüklemeler adları kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları kullanır.</span><span class="sxs-lookup"><span data-stu-id="2c524-930">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="2c524-931">*Koddaki varsayılanları değiştirme*</span><span class="sxs-lookup"><span data-stu-id="2c524-931">*Change the defaults in code*</span></span>

<span data-ttu-id="2c524-932">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` `ListenOptions` `HttpsConnectionAdapterOptions` , önceki senaryoda belirtilen varsayılan sertifikayı geçersiz kılma dahil, ve için varsayılan ayarları değiştirmek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-932">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="2c524-933">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-933">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="2c524-934">*SNı için Kestrel desteği*</span><span class="sxs-lookup"><span data-stu-id="2c524-934">*Kestrel support for SNI*</span></span>

<span data-ttu-id="2c524-935">[Sunucu adı belirtme (SNı)](https://tools.ietf.org/html/rfc6066#section-3) , aynı IP adresi ve bağlantı noktası üzerinde birden fazla etki alanını barındırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-935">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="2c524-936">SNı 'nin çalışması için, istemci, TLS el sıkışması sırasında güvenli oturum ana bilgisayar adını, sunucunun doğru sertifikayı sağlayabilmesi için gönderir.</span><span class="sxs-lookup"><span data-stu-id="2c524-936">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="2c524-937">İstemci, TLS anlaşmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için bulunan sertifikayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="2c524-937">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="2c524-938">Kestrel, geri çağırma aracılığıyla SNı destekler `ServerCertificateSelector` .</span><span class="sxs-lookup"><span data-stu-id="2c524-938">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="2c524-939">Geri çağırma, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesini sağlamak için bağlantı başına bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="2c524-939">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="2c524-940">SNı desteği şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="2c524-940">SNI support requires:</span></span>

* <span data-ttu-id="2c524-941">Hedef Framework `netcoreapp2.1` veya sonraki sürümlerde çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="2c524-941">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="2c524-942">`net461`Veya sonraki sürümlerde, geri çağırma çağrılır, ancak `name` her zaman olur `null` .</span><span class="sxs-lookup"><span data-stu-id="2c524-942">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="2c524-943">`name`Ayrıca, `null` ISTEMCI, TLS el sıkışmasının ana bilgisayar adı parametresini sağlamıyorsa de olur.</span><span class="sxs-lookup"><span data-stu-id="2c524-943">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="2c524-944">Tüm Web siteleri aynı Kestrel örneğinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="2c524-944">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="2c524-945">Kestrel, bir IP adresi ve bağlantı noktasının bir ters proxy olmadan birden çok örnek arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="2c524-945">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="2c524-946">Bağlantı günlüğü</span><span class="sxs-lookup"><span data-stu-id="2c524-946">Connection logging</span></span>

<span data-ttu-id="2c524-947"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>Bir bağlantıda bayt düzeyinde iletişim Için hata ayıklama düzeyi günlüklerini yayma çağrısı.</span><span class="sxs-lookup"><span data-stu-id="2c524-947">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="2c524-948">Bağlantı günlüğü, TLS şifreleme ve proxy 'nin arkasındaki gibi alt düzey iletişimde sorunları gidermeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="2c524-948">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="2c524-949">`UseConnectionLogging`Daha önce yerleştirilmişse `UseHttps` , şifrelenmiş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-949">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="2c524-950">`UseConnectionLogging`Öğesinden sonra yerleştirilmişse `UseHttps` , şifresi çözülmüş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-950">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="2c524-951">TCP yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="2c524-951">Bind to a TCP socket</span></span>

<span data-ttu-id="2c524-952"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Yöntemi BIR TCP yuvasına bağlanır ve bir seçenek lambda X. 509.440 sertifika yapılandırmasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="2c524-952">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="2c524-953">Örnek, HTTPS 'yi ile bir uç nokta için yapılandırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="2c524-953">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="2c524-954">Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak üzere aynı API 'yi kullanın.</span><span class="sxs-lookup"><span data-stu-id="2c524-954">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="2c524-955">UNIX yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="2c524-955">Bind to a Unix socket</span></span>

<span data-ttu-id="2c524-956"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>Aşağıdaki örnekte gösterildiği gibi NGINX ile gelişmiş performans için ile birlikte bir UNIX yuvası dinleyin:</span><span class="sxs-lookup"><span data-stu-id="2c524-956">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

* <span data-ttu-id="2c524-957">NGINX confiuguration dosyasında, `server`  >  `location`  >  `proxy_pass` girdisini olarak ayarlayın `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="2c524-957">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="2c524-958">`{KESTREL SOCKET}` , için belirtilen yuvanın adıdır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (örneğin, `kestrel-test.sock` Önceki örnekte).</span><span class="sxs-lookup"><span data-stu-id="2c524-958">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="2c524-959">Yuvanın NGINX tarafından yazılabilir olduğundan emin olun (örneğin, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="2c524-959">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="2c524-960">Bağlantı noktası 0</span><span class="sxs-lookup"><span data-stu-id="2c524-960">Port 0</span></span>

<span data-ttu-id="2c524-961">Bağlantı noktası numarası `0` belirtildiğinde, Kestrel dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır.</span><span class="sxs-lookup"><span data-stu-id="2c524-961">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="2c524-962">Aşağıdaki örnek, çalışma zamanında Kestrel gerçekte hangi bağlantı noktasının gerçekten bağlandığını nasıl belirleyeceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="2c524-962">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="2c524-963">Uygulama çalıştırıldığında konsol penceresi çıkışı, uygulamanın erişilebileceği dinamik bağlantı noktasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="2c524-963">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="2c524-964">Sınırlamalar</span><span class="sxs-lookup"><span data-stu-id="2c524-964">Limitations</span></span>

<span data-ttu-id="2c524-965">Aşağıdaki yaklaşımlar ile uç noktaları yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="2c524-965">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="2c524-966">`--urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="2c524-966">`--urls` command-line argument</span></span>
* <span data-ttu-id="2c524-967">`urls` Ana bilgisayar yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="2c524-967">`urls` host configuration key</span></span>
* <span data-ttu-id="2c524-968">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="2c524-968">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="2c524-969">Bu yöntemler, kodun Kestrel dışındaki sunucularla çalışmasını sağlamak için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-969">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="2c524-970">Ancak, aşağıdaki sınırlamalara dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="2c524-970">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="2c524-971">HTTPS uç noktası yapılandırmasında varsayılan bir sertifika sağlanmadığı sürece HTTPS bu yaklaşımlar ile kullanılamaz (örneğin, `KestrelServerOptions` Bu konunun önceki kısımlarında gösterildiği gibi yapılandırma veya yapılandırma dosyası kullanılıyor).</span><span class="sxs-lookup"><span data-stu-id="2c524-971">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="2c524-972">Hem hem de `Listen` `UseUrls` yaklaşımları aynı anda kullanıldığında, uç noktalar `Listen` `UseUrls` uç noktaları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="2c524-972">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="2c524-973">IIS uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="2c524-973">IIS endpoint configuration</span></span>

<span data-ttu-id="2c524-974">IIS kullanırken, IIS geçersiz kılma bağlamaları için URL bağlamaları veya ya da tarafından ayarlanır `Listen` `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="2c524-974">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="2c524-975">Daha fazla bilgi için [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="2c524-975">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="2c524-976">Aktarım yapılandırması</span><span class="sxs-lookup"><span data-stu-id="2c524-976">Transport configuration</span></span>

<span data-ttu-id="2c524-977">ASP.NET Core 2,1 sürümü ile Kestrel 'in varsayılan taşıması artık libuv ' d i temel değildir ancak bunun yerine yönetilen yuvaları temel alır.</span><span class="sxs-lookup"><span data-stu-id="2c524-977">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="2c524-978">Bu, çağrıyı yapan ve aşağıdaki paketlerden birine bağlı olan ASP.NET Core 2,0 2,1 uygulamalarının önemli bir değişikliği olur <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="2c524-978">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="2c524-979">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (doğrudan paket başvurusu)</span><span class="sxs-lookup"><span data-stu-id="2c524-979">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="2c524-980">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="2c524-980">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="2c524-981">Libuv kullanımını gerektiren projeler için:</span><span class="sxs-lookup"><span data-stu-id="2c524-981">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="2c524-982">Uygulamanın proje dosyasına [Microsoft. AspNetCore. Server. Kestrel. Transport. libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) paketi için bir bağımlılık ekleyin:</span><span class="sxs-lookup"><span data-stu-id="2c524-982">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="2c524-983">Çağrı <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="2c524-983">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="2c524-984">URL önekleri</span><span class="sxs-lookup"><span data-stu-id="2c524-984">URL prefixes</span></span>

<span data-ttu-id="2c524-985">`UseUrls`, `--urls` Komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı veya `ASPNETCORE_URLS` ortam değişkeni kullanılırken, URL önekleri aşağıdaki biçimlerden birinde olabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-985">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="2c524-986">Yalnızca HTTP URL ön ekleri geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="2c524-986">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="2c524-987">Kestrel, kullanılarak URL bağlamaları yapılandırılırken HTTPS 'YI desteklemez `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="2c524-987">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="2c524-988">Bağlantı noktası numarası olan IPv4 adresi</span><span class="sxs-lookup"><span data-stu-id="2c524-988">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="2c524-989">`0.0.0.0` Tüm IPv4 adreslerine bağlanan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="2c524-989">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="2c524-990">Bağlantı noktası numarasına sahip IPv6 adresi</span><span class="sxs-lookup"><span data-stu-id="2c524-990">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="2c524-991">`[::]` , IPv4 'un IPv6 eşdeğeridir `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="2c524-991">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="2c524-992">Bağlantı noktası numarası olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="2c524-992">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="2c524-993">, Ve ana bilgisayar adları `*` `+` özel değildir.</span><span class="sxs-lookup"><span data-stu-id="2c524-993">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="2c524-994">Geçerli bir IP adresi olarak tanınmayan veya `localhost` tüm IPv4 ve IPv6 IP 'lerine bağlanan her şey.</span><span class="sxs-lookup"><span data-stu-id="2c524-994">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="2c524-995">Farklı ana bilgisayar adlarını aynı bağlantı noktasında farklı ASP.NET Core uygulamalarına bağlamak için, [HTTP.sys](xref:fundamentals/servers/httpsys) veya IIS, NGINX veya Apache gibi bir ters proxy sunucusunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="2c524-995">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="2c524-996">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="2c524-996">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="2c524-997">Port numarası `localhost` veya bağlantı noktası numarası ile geri döngü IP 'si olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="2c524-997">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="2c524-998">Belirtildiğinde `localhost` , Kestrel hem IPv4 hem de IPv6 geri döngü arabirimlerini bağlamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="2c524-998">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="2c524-999">İstenen bağlantı noktası, herhangi bir geri döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="2c524-999">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="2c524-1000">Herhangi bir geri döngü arabirimi başka bir nedenle kullanılamıyorsa (genellikle IPv6 desteklenmediği için), Kestrel bir uyarı kaydeder.</span><span class="sxs-lookup"><span data-stu-id="2c524-1000">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="2c524-1001">Konak filtreleme</span><span class="sxs-lookup"><span data-stu-id="2c524-1001">Host filtering</span></span>

<span data-ttu-id="2c524-1002">Kestrel gibi önekleri temel alarak yapılandırmayı desteklese `http://example.com:5000` de, Kestrel büyük ölçüde ana bilgisayar adını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="2c524-1002">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="2c524-1003">Ana bilgisayar `localhost` , geri döngü adreslerine bağlama için kullanılan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="2c524-1003">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="2c524-1004">Açık IP adresi dışındaki tüm ana bilgisayar tüm genel IP adreslerine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="2c524-1004">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="2c524-1005">`Host` Üstbilgiler doğrulanmadı.</span><span class="sxs-lookup"><span data-stu-id="2c524-1005">`Host` headers aren't validated.</span></span>

<span data-ttu-id="2c524-1006">Geçici bir çözüm olarak, ana bilgisayar filtreleme ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="2c524-1006">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="2c524-1007">Ana bilgisayar filtreleme ara yazılımı, [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 veya 2,2) ' de yer alan [Microsoft. Aspnetcore. hostfiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="2c524-1007">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="2c524-1008">Ara yazılım tarafından eklenir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ve şunları çağırır <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="2c524-1008">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="2c524-1009">Ana bilgisayar filtreleme ara yazılımı varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-1009">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="2c524-1010">Ara yazılımı etkinleştirmek için, `AllowedHosts` appSettings *üzerindeappsettings.js*bir anahtar tanımlayın / *. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="2c524-1010">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="2c524-1011">Değer, bağlantı noktası numaraları olmayan ana bilgisayar adlarının noktalı virgülle ayrılmış listesidir:</span><span class="sxs-lookup"><span data-stu-id="2c524-1011">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="2c524-1012">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="2c524-1012">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="2c524-1013">[Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer) da bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçenek içerir.</span><span class="sxs-lookup"><span data-stu-id="2c524-1013">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="2c524-1014">İletilen üstbilgiler ara yazılımı ve ana bilgisayar filtreleme ara yazılımı, farklı senaryolar için benzer işlevlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="2c524-1014">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="2c524-1015">`AllowedHosts`Iletilen üstbilgiler ara yazılımı ile, `Host` istekler ters bir ara sunucu veya yük dengeleyici ile iletilirken üst bilgi korunurken, bu işlem için uygun bir ayar vardır.</span><span class="sxs-lookup"><span data-stu-id="2c524-1015">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="2c524-1016">`AllowedHosts`Ana bilgisayar filtreleme ara yazılımı ile ayarlama, Kestrel herkese açık bir uç sunucu olarak veya `Host` üst bilgi doğrudan iletildiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="2c524-1016">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="2c524-1017">Iletilen üstbilgiler ara yazılımı hakkında daha fazla bilgi için bkz <xref:host-and-deploy/proxy-load-balancer> ..</span><span class="sxs-lookup"><span data-stu-id="2c524-1017">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="2c524-1018">HTTP/1.1 istek boşaltma</span><span class="sxs-lookup"><span data-stu-id="2c524-1018">HTTP/1.1 request draining</span></span>

<span data-ttu-id="2c524-1019">HTTP bağlantılarının açılması zaman alabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-1019">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="2c524-1020">HTTPS için de kaynak kullanımı yoğun bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="2c524-1020">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="2c524-1021">Bu nedenle Kestrel, HTTP/1.1 protokolü başına bağlantıları yeniden kullanmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="2c524-1021">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="2c524-1022">Bağlantının yeniden kullanılmasına izin vermek için bir istek gövdesi tam olarak tüketilmelidir.</span><span class="sxs-lookup"><span data-stu-id="2c524-1022">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="2c524-1023">Uygulama, `POST` sunucunun yeniden yönlendirme veya 404 yanıtı döndürdüğü bir istek gibi her zaman istek gövdesini tüketmez.</span><span class="sxs-lookup"><span data-stu-id="2c524-1023">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="2c524-1024">`POST`Yeniden yönlendirme durumunda:</span><span class="sxs-lookup"><span data-stu-id="2c524-1024">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="2c524-1025">İstemci, verilerin bir bölümünü zaten göndermiş olabilir `POST` .</span><span class="sxs-lookup"><span data-stu-id="2c524-1025">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="2c524-1026">Sunucu 301 yanıtını yazar.</span><span class="sxs-lookup"><span data-stu-id="2c524-1026">The server writes the 301 response.</span></span>
* <span data-ttu-id="2c524-1027">`POST`Önceki istek gövdesinden gelen veriler tam olarak okunana kadar bağlantı yeni bir istek için kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="2c524-1027">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="2c524-1028">Kestrel, istek gövdesini boşaltmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="2c524-1028">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="2c524-1029">İstek gövdesini boşaltma işlemi işlemeden verileri okuma ve atma anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="2c524-1029">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="2c524-1030">Boşaltma işlemi bağlantının yeniden kullanılmasına izin verme ve kalan verilerin boşaltıma süresi arasında bir ilerleme gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="2c524-1030">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="2c524-1031">Boşaltma, yapılandırılabilir olmayan beş saniyelik bir zaman aşımına sahip olur.</span><span class="sxs-lookup"><span data-stu-id="2c524-1031">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="2c524-1032">Veya üstbilgisi tarafından belirtilen tüm veriler `Content-Length` `Transfer-Encoding` zaman aşımından önce okunmadıysa bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="2c524-1032">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="2c524-1033">Bazen, yanıtı yazmadan önce veya sonra isteği hemen sonlandırmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="2c524-1033">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="2c524-1034">Örneğin, istemciler sınırlı verilerin büyük bir düzeyi olabilir, bu nedenle karşıya yüklenen verileri sınırlamak bir öncelik olabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-1034">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="2c524-1035">Bu tür durumlarda bir isteği sonlandırmak için bir denetleyici, sayfa veya ara yazılım aracılığıyla [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) ' ı çağırın Razor .</span><span class="sxs-lookup"><span data-stu-id="2c524-1035">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="2c524-1036">Çağırmanın uyarıları vardır `Abort` :</span><span class="sxs-lookup"><span data-stu-id="2c524-1036">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="2c524-1037">Yeni bağlantı oluşturma yavaş ve pahalı olabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-1037">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="2c524-1038">İstemcinin bağlantı kapanmadan önce yanıtı okuduğunuzdan emin olmaz.</span><span class="sxs-lookup"><span data-stu-id="2c524-1038">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="2c524-1039">Çağırma `Abort` , yaygın hatalara değil önemli hata durumları için nadir ve ayrılmış olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="2c524-1039">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="2c524-1040">Yalnızca `Abort` belirli bir sorunun çözülmesi gerektiğinde çağırın.</span><span class="sxs-lookup"><span data-stu-id="2c524-1040">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="2c524-1041">Örneğin, `Abort` kötü amaçlı istemciler veri almaya çalışıyorsa `POST` veya istemci kodunda büyük veya çok sayıda isteğe neden olan bir hata olduğunda çağırın.</span><span class="sxs-lookup"><span data-stu-id="2c524-1041">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="2c524-1042">`Abort`HTTP 404 (bulunamadı) gibi yaygın hata durumları için çağrı yapmayın.</span><span class="sxs-lookup"><span data-stu-id="2c524-1042">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="2c524-1043">Çağrılmadan önce [HttpResponse. tamamlana eşitlemesini](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) çağırmak `Abort` Sunucunun yanıtı yazmayı tamamlamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="2c524-1043">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="2c524-1044">Ancak, istemci davranışı tahmin edilebilir değildir ve bağlantı durdurulmadan önce yanıtı okuyamayabilir.</span><span class="sxs-lookup"><span data-stu-id="2c524-1044">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="2c524-1045">Bu işlem HTTP/2 için farklıdır çünkü protokol, bağlantıyı kapatmadan bağımsız istek akışlarını iptal etme işlemini destekler.</span><span class="sxs-lookup"><span data-stu-id="2c524-1045">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="2c524-1046">Beş saniyelik boşaltma zaman aşımı uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="2c524-1046">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="2c524-1047">Yanıt tamamlandıktan sonra herhangi bir okunmamış istek gövdesi verisi varsa, sunucu bir HTTP/2 RST çerçevesi gönderir.</span><span class="sxs-lookup"><span data-stu-id="2c524-1047">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="2c524-1048">Ek istek gövdesi veri çerçeveleri yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="2c524-1048">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="2c524-1049">Mümkünse, istemcilerin [Beklenen: 100-Continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) istek üst bilgisini kullanabilmesi ve istek gövdesini göndermeden önce sunucunun yanıt vermesini beklemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="2c524-1049">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="2c524-1050">Bu, istemciye gereksiz verileri göndermeden önce yanıtı İnceleme ve iptal etme olanağı sunar.</span><span class="sxs-lookup"><span data-stu-id="2c524-1050">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2c524-1051">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="2c524-1051">Additional resources</span></span>

* <span data-ttu-id="2c524-1052">Linux üzerinde UNIX yuvaları kullanırken, yuva uygulama kapatılırken otomatik olarak silinmez.</span><span class="sxs-lookup"><span data-stu-id="2c524-1052">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="2c524-1053">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/aspnetcore/issues/14134)bakın.</span><span class="sxs-lookup"><span data-stu-id="2c524-1053">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="2c524-1054">RFC 7230: Ileti sözdizimi ve yönlendirme (Bölüm 5,4: Ana bilgisayar)</span><span class="sxs-lookup"><span data-stu-id="2c524-1054">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
