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
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="f715a-103">ASP.NET Core'da kerkenez web sunucusu uygulaması</span><span class="sxs-lookup"><span data-stu-id="f715a-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="f715a-104">Yazar: [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), ve [Stephen Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="f715a-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f715a-105">Kerkenez core ASP.NET [için](xref:fundamentals/servers/index)bir çapraz platform web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="f715a-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="f715a-106">Kerkenez, ASP.NET Core proje şablonlarında varsayılan olarak bulunan web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="f715a-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="f715a-107">Kerkenez aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="f715a-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="f715a-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="f715a-108">HTTPS</span></span>
* <span data-ttu-id="f715a-109">[WebSockets'i](https://github.com/aspnet/websockets) etkinleştirmek için kullanılan opak yükseltme</span><span class="sxs-lookup"><span data-stu-id="f715a-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="f715a-110">Nginx arkasında yüksek performans için Unix soketleri</span><span class="sxs-lookup"><span data-stu-id="f715a-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="f715a-111">HTTP/2 (macOS&dagger;hariç)</span><span class="sxs-lookup"><span data-stu-id="f715a-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="f715a-112">&dagger;HTTP/2 gelecekte yayınlanacak bir sürümde macOS'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="f715a-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="f715a-113">Kerkenez, .NET Core'un desteklediği tüm platformlarda ve sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="f715a-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="f715a-114">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f715a-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="f715a-115">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="f715a-115">HTTP/2 support</span></span>

<span data-ttu-id="f715a-116">Aşağıdaki temel gereksinimler yerine [getirildiğinde, http/2](https://httpwg.org/specs/rfc7540.html) ASP.NET Core uygulamaları için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="f715a-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="f715a-117">İşletim sistemi&dagger;</span><span class="sxs-lookup"><span data-stu-id="f715a-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="f715a-118">Windows Server 2016/Windows 10 veya sonrası&Dagger;</span><span class="sxs-lookup"><span data-stu-id="f715a-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="f715a-119">OpenSSL 1.0.2 veya sonrası (örneğin, Ubuntu 16.04 veya sonrası) ile Linux</span><span class="sxs-lookup"><span data-stu-id="f715a-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="f715a-120">Hedef çerçeve: .NET Core 2.2 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="f715a-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="f715a-121">[Uygulama Katmanı Protokolü Anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı</span><span class="sxs-lookup"><span data-stu-id="f715a-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="f715a-122">TLS 1.2 veya daha sonra bağlantı</span><span class="sxs-lookup"><span data-stu-id="f715a-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="f715a-123">&dagger;HTTP/2 gelecekte yayınlanacak bir sürümde macOS'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="f715a-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="f715a-124">&Dagger;Kerkenez, Windows Server 2012 R2 ve Windows 8.1'de HTTP/2 için sınırlı bir desteğe sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f715a-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="f715a-125">Bu işletim sistemlerinde bulunan desteklenen TLS şifreleme paketlerinin listesi sınırlı olduğundan destek sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="f715a-126">TLS bağlantılarını güvence altına almak için Eliptik Eğri Dijital İmza Algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="f715a-127">Bir HTTP/2 bağlantısı kuruluysa, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) bildirir. `HTTP/2`</span><span class="sxs-lookup"><span data-stu-id="f715a-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="f715a-128">HTTP/2 varsayılan olarak devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="f715a-129">Yapılandırma hakkında daha fazla bilgi için [Kerkenez seçenekleri](#kestrel-options) ve [ListenOptions.Protocols](#listenoptionsprotocols) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="f715a-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="f715a-130">Ters proxy ile Kerkenez ne zaman kullanılır</span><span class="sxs-lookup"><span data-stu-id="f715a-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="f715a-131">Kerkenez kendisi tarafından veya *ters proxy sunucusu*ile kullanılabilir , Internet Bilgi Hizmetleri gibi [(IIS)](https://www.iis.net/), [Nginx](https://nginx.org), veya [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="f715a-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="f715a-132">Ters proxy sunucusu ağdan HTTP isteklerini alır ve bunları Kestrel'e ileter.</span><span class="sxs-lookup"><span data-stu-id="f715a-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="f715a-133">Kerkenez bir kenar (Internet bakan) web sunucusu olarak kullanılır:</span><span class="sxs-lookup"><span data-stu-id="f715a-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kerkenez ters proxy sunucusu olmadan internet ile doğrudan iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="f715a-135">Ters proxy yapılandırmasında kullanılan kerkenez:</span><span class="sxs-lookup"><span data-stu-id="f715a-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kerkenez, IIS, Nginx veya Apache gibi ters proxy sunucusu aracılığıyla Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="f715a-137">Ters proxy sunucusu olan veya olmayan yapılandırma, desteklenen bir barındırma yapılandırmasıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="f715a-138">Ters proxy sunucusu olmayan bir kenar sunucusu olarak kullanılan kerkenez, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="f715a-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="f715a-139">Kestrel bir bağlantı noktasında dinleyecek şekilde yapılandırıldığında, Kestrel isteklerin `Host` üstbilgilerinden bağımsız olarak o bağlantı noktasının tüm trafiğini işler.</span><span class="sxs-lookup"><span data-stu-id="f715a-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="f715a-140">Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel'e iletme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f715a-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="f715a-141">Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucusu kullanmak iyi bir seçim olabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="f715a-142">Ters proxy:</span><span class="sxs-lookup"><span data-stu-id="f715a-142">A reverse proxy:</span></span>

* <span data-ttu-id="f715a-143">Barındırdığı uygulamaların açıkta kalan ortak yüzey alanını sınırlayabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="f715a-144">Ek bir yapılandırma ve savunma katmanı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="f715a-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="f715a-145">Mevcut altyapı ile daha iyi entegre olabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="f715a-146">Yük dengeleme ve güvenli iletişim (HTTPS) yapılandırması basitleştirin.</span><span class="sxs-lookup"><span data-stu-id="f715a-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="f715a-147">Yalnızca ters proxy sunucusu bir X.509 sertifikası gerektirir ve bu sunucu apaçık HTTP kullanarak dahili ağdaki uygulamasunucularıyla iletişim kurabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="f715a-148">Ters proxy yapılandırmasında barındırma [ana bilgisayar filtreleme](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f715a-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="f715a-149">ASP.NET Core uygulamalarında kerkenez</span><span class="sxs-lookup"><span data-stu-id="f715a-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="f715a-150">ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır.</span><span class="sxs-lookup"><span data-stu-id="f715a-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="f715a-151">*Program.cs*yılında <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> , <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>yöntem çağırır:</span><span class="sxs-lookup"><span data-stu-id="f715a-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="f715a-152">Ana bilgisayar oluşturma hakkında daha fazla bilgi için, ana bilgisayar <xref:fundamentals/host/generic-host#set-up-a-host>ve Varsayılan *oluşturucu ayarları* bölümlerini *ayarlama* bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="f715a-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="f715a-153">Aradıktan `ConfigureWebHostDefaults`sonra ek yapılandırma `ConfigureKestrel`sağlamak için , kullanın:</span><span class="sxs-lookup"><span data-stu-id="f715a-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="f715a-154">Kerkenez seçenekleri</span><span class="sxs-lookup"><span data-stu-id="f715a-154">Kestrel options</span></span>

<span data-ttu-id="f715a-155">Kestrel web sunucusu, özellikle Internet'e dönük dağıtımlarda kullanışlı olan kısıtlama yapılandırma seçeneklerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f715a-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="f715a-156"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> Sınıfın <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> özelliği üzerindeki kısıtlamaları ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f715a-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="f715a-157">Özellik `Limits` sınıfın bir örneğini <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> tutar.</span><span class="sxs-lookup"><span data-stu-id="f715a-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="f715a-158">Aşağıdaki <xref:Microsoft.AspNetCore.Server.Kestrel.Core> örneklerde ad alanı kullanılır:</span><span class="sxs-lookup"><span data-stu-id="f715a-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="f715a-159">Bu makalede daha sonra gösterilen örneklerde Kerkenez seçenekleri C# kodunda yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="f715a-160">Kerkenez seçenekleri de bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="f715a-161">Örneğin, [Dosya Yapılandırma Sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider) kestrel yapılandırmasını bir *appsettings.json* veya *appsettings'ten yükleyebilir.{ Çevre}.json* dosyası:</span><span class="sxs-lookup"><span data-stu-id="f715a-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="f715a-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>ve [uç nokta yapılandırması](#endpoint-configuration) yapılandırma sağlayıcılarından yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="f715a-163">Kalan Kerkenez yapılandırması C# kodunda yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="f715a-164">Aşağıdaki **yaklaşımlardan birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="f715a-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="f715a-165">Kerkenezi `Startup.ConfigureServices`yapılandır:</span><span class="sxs-lookup"><span data-stu-id="f715a-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="f715a-166">Sınıfa bir `IConfiguration` örnek `Startup` enjekte edin.</span><span class="sxs-lookup"><span data-stu-id="f715a-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="f715a-167">Aşağıdaki örnek, enjekte edilen yapılandırmanın `Configuration` özelliğe atandığını varsayar.</span><span class="sxs-lookup"><span data-stu-id="f715a-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="f715a-168">In `Startup.ConfigureServices`, `Kestrel` Kerkenez yapılandırma içine yapılandırma bölümü yükleyin:</span><span class="sxs-lookup"><span data-stu-id="f715a-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="f715a-169">Ev sahibini kurarken Kerkenezi yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="f715a-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="f715a-170">*Program.cs,* yapılandırma `Kestrel` bölümünü Kestrel'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="f715a-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="f715a-171">Önceki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.</span><span class="sxs-lookup"><span data-stu-id="f715a-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="f715a-172">Zaman ayarı tutma</span><span class="sxs-lookup"><span data-stu-id="f715a-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="f715a-173">Canlı kalma [zaman ını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f715a-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="f715a-174">Varsayılan olarak 2 dakika dır.</span><span class="sxs-lookup"><span data-stu-id="f715a-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="f715a-175">Maksimum istemci bağlantıları</span><span class="sxs-lookup"><span data-stu-id="f715a-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="f715a-176">Aşağıdaki kodla tüm uygulama için eşzamanlı açık TCP bağlantısı sayısı ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="f715a-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="f715a-177">HTTP veya HTTPS'den başka bir protokole yükseltilmiş bağlantılar için ayrı bir sınır vardır (örneğin, bir WebSockets isteğinde).</span><span class="sxs-lookup"><span data-stu-id="f715a-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="f715a-178">Bir bağlantı yükseltildikten sonra, sınıra `MaxConcurrentConnections` göre sayılmaz.</span><span class="sxs-lookup"><span data-stu-id="f715a-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="f715a-179">Maksimum bağlantı sayısı varsayılan olarak sınırsızdır (null).</span><span class="sxs-lookup"><span data-stu-id="f715a-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="f715a-180">Maksimum istek gövde boyutu</span><span class="sxs-lookup"><span data-stu-id="f715a-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="f715a-181">Varsayılan maksimum istek gövde boyutu yaklaşık 28,6 MB olan 30.000.000 bayttır.</span><span class="sxs-lookup"><span data-stu-id="f715a-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="f715a-182">Bir ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> önerilen yaklaşım, özniteliği bir eylem yönteminde kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="f715a-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="f715a-183">Aşağıda, her istekte uygulamanın kısıtlamasının nasıl yapılandırılabildiğini gösteren bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="f715a-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="f715a-184">Ara yazılımda belirli bir istek üzerindeki ayarı geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="f715a-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="f715a-185">Uygulama isteği okumaya başladıktan sonra uygulama istek sınırını yapılandırırsa bir özel durum atılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="f715a-186">Özelliğin `MaxRequestBodySize` salt `IsReadOnly` okunur durumda olup olmadığını gösteren bir özellik vardır, bu da sınırı yapılandırmak için çok geç olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="f715a-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="f715a-187">Bir uygulama [ASP.NET Çekirdek Modülü'nün](xref:host-and-deploy/aspnet-core-module)arkasında [işlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model) kaldığında, IIS sınırı zaten belirlediği için Kestrel'in istek gövde boyutu sınırı devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="f715a-188">Minimum istek gövde veri oranı</span><span class="sxs-lookup"><span data-stu-id="f715a-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="f715a-189">Kerkenez, verilerin bayt/saniye cinsinden belirtilen oranda gelişip ulaşmadığında her saniye kontrol eder.</span><span class="sxs-lookup"><span data-stu-id="f715a-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="f715a-190">Oran minimumun altına düşerse, bağlantı zamanlanır. Yetkisiz kullanım süresi, Kestrel'in müşteriye gönderim oranını en aza kadar yükseltmesi için verdiği süredir; bu süre içinde fiyat kontrol edilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="f715a-191">Yetkisiz kullanım süresi, TCP'nin yavaş başlaması nedeniyle başlangıçta veri gönderen bağlantıların düşmesini önlemeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="f715a-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="f715a-192">Varsayılan minimum oran, 5 saniyelik yetkisiz kullanım süresine sahip 240 bayt/saniyedir.</span><span class="sxs-lookup"><span data-stu-id="f715a-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="f715a-193">Yanıt için de minimum ücret uygulanır.</span><span class="sxs-lookup"><span data-stu-id="f715a-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="f715a-194">İstek sınırını ve yanıt sınırını ayarlamak için kod, `RequestBody` özellik `Response` ve arabirim adlarına sahip olmak veya bunlar dışında aynıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="f715a-195">Aşağıda, *Program.cs*minimum veri oranlarının nasıl yapılandırılabildiğini gösteren bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="f715a-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="f715a-196">Ara yazılımda istek başına minimum ücret limitlerini geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="f715a-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="f715a-197">Önceki <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> örnekte başvurulan http/2 `HttpContext.Features` istekleri için mevcut değildir, çünkü istek başına fiyat sınırlarının değiştirilmesi genellikle protokolün istek çoklama desteği nedeniyle HTTP/2 için desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="f715a-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="f715a-198">Ancak, <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> okuma oranı `HttpContext.Features` sınırı bir HTTP/2 isteği `IHttpMinRequestBodyDataRateFeature.MinDataRate` için `null` bile ayarlayarak istek başına bazda *tamamen devre dışı* bırakıldığından, HTTP/2 istekleri için hala mevcut.</span><span class="sxs-lookup"><span data-stu-id="f715a-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="f715a-199">Okumaya `IHttpMinRequestBodyDataRateFeature.MinDataRate` çalışmak veya başka bir değere ayarlamaya `null` çalışmak, `NotSupportedException` http/2 isteği verilen bir değerin atılmasına neden olur.</span><span class="sxs-lookup"><span data-stu-id="f715a-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="f715a-200">Http/1.x ve `KestrelServerOptions.Limits` HTTP/2 bağlantıları için de sunucu genelinde yapılandırılan oran sınırları hala geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f715a-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="f715a-201">Üstbilgi zaman alabı isteği</span><span class="sxs-lookup"><span data-stu-id="f715a-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="f715a-202">Sunucunun istek üstbilgilerini almak için harcadığı maksimum süreyi alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f715a-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="f715a-203">Varsayılan olarak 30 saniye.</span><span class="sxs-lookup"><span data-stu-id="f715a-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="f715a-204">Bağlantı başına maksimum akış</span><span class="sxs-lookup"><span data-stu-id="f715a-204">Maximum streams per connection</span></span>

<span data-ttu-id="f715a-205">`Http2.MaxStreamsPerConnection`http/2 bağlantısı başına eşzamanlı istek akışlarının sayısını sınırlar.</span><span class="sxs-lookup"><span data-stu-id="f715a-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="f715a-206">Fazla akışreddedilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="f715a-207">Varsayılan değer 100’dür.</span><span class="sxs-lookup"><span data-stu-id="f715a-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="f715a-208">Üstbilgi tablo boyutu</span><span class="sxs-lookup"><span data-stu-id="f715a-208">Header table size</span></span>

<span data-ttu-id="f715a-209">HPACK kod çözücü http/2 bağlantıları için HTTP başlıkları decompresses.</span><span class="sxs-lookup"><span data-stu-id="f715a-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="f715a-210">`Http2.HeaderTableSize`HPACK kod çözücünün kullandığı üstbilgi sıkıştırma tablosunun boyutunu sınırlar.</span><span class="sxs-lookup"><span data-stu-id="f715a-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="f715a-211">Değer sekizli olarak sağlanır ve sıfırdan büyük olmalıdır (0).</span><span class="sxs-lookup"><span data-stu-id="f715a-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="f715a-212">Varsayılan değer 4096'dır.</span><span class="sxs-lookup"><span data-stu-id="f715a-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="f715a-213">Maksimum kare boyutu</span><span class="sxs-lookup"><span data-stu-id="f715a-213">Maximum frame size</span></span>

<span data-ttu-id="f715a-214">`Http2.MaxFrameSize`sunucu tarafından alınan veya gönderilen BIR HTTP/2 bağlantı çerçevesi yükünün izin verilen maksimum boyutunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="f715a-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="f715a-215">Değeri sekizli olarak sağlanır ve 2^14 (16.384) ile 2^24-1 (16.777.215) arasında olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="f715a-216">Varsayılan değer 2^14 (16.384) 'dir.</span><span class="sxs-lookup"><span data-stu-id="f715a-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="f715a-217">Maksimum istek üstbilgi boyutu</span><span class="sxs-lookup"><span data-stu-id="f715a-217">Maximum request header size</span></span>

<span data-ttu-id="f715a-218">`Http2.MaxRequestHeaderFieldSize`istek üstbilgi değerlerinin sekizlisinde izin verilen maksimum boyutu gösterir.</span><span class="sxs-lookup"><span data-stu-id="f715a-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="f715a-219">Bu sınır, sıkıştırılmış ve sıkıştırılmamış gösterimlerindeki hem ad hem de değer için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f715a-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="f715a-220">Değer sıfırdan büyük olmalıdır (0).</span><span class="sxs-lookup"><span data-stu-id="f715a-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="f715a-221">Varsayılan değer 8.192'dir.</span><span class="sxs-lookup"><span data-stu-id="f715a-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="f715a-222">İlk bağlantı penceresi boyutu</span><span class="sxs-lookup"><span data-stu-id="f715a-222">Initial connection window size</span></span>

<span data-ttu-id="f715a-223">`Http2.InitialConnectionWindowSize`bağlantı başına tüm istekler (akışlar) arasında toplanan sunucu arabellekleri baytlar maksimum istek gövde verileri gösterir.</span><span class="sxs-lookup"><span data-stu-id="f715a-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="f715a-224">İstekler de `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="f715a-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="f715a-225">Değer 65.535'ten büyük veya eşit ve 2^31'den az olmalıdır (2,147,483,648).</span><span class="sxs-lookup"><span data-stu-id="f715a-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="f715a-226">Varsayılan değer 128 KB (131.072) 'dir.</span><span class="sxs-lookup"><span data-stu-id="f715a-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="f715a-227">İlk akış penceresi boyutu</span><span class="sxs-lookup"><span data-stu-id="f715a-227">Initial stream window size</span></span>

<span data-ttu-id="f715a-228">`Http2.InitialStreamWindowSize`istek başına sunucu arabelleklerini baytlar halinde ki maksimum istek gövdesi verilerini gösterir (akış).</span><span class="sxs-lookup"><span data-stu-id="f715a-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="f715a-229">İstekler de `Http2.InitialConnectionWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="f715a-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="f715a-230">Değer 65.535'ten büyük veya eşit ve 2^31'den az olmalıdır (2,147,483,648).</span><span class="sxs-lookup"><span data-stu-id="f715a-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="f715a-231">Varsayılan değer 96 KB 'dir (98.304).</span><span class="sxs-lookup"><span data-stu-id="f715a-231">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="f715a-232">Zaman Uyumlu G/Ç</span><span class="sxs-lookup"><span data-stu-id="f715a-232">Synchronous I/O</span></span>

<span data-ttu-id="f715a-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>istek ve yanıt için senkron G/Ç'ye izin verilip verilmediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="f715a-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="f715a-234">Varsayılan değer: `false`.</span><span class="sxs-lookup"><span data-stu-id="f715a-234">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="f715a-235">Çok sayıda senkron G/Ç işlemini engelleme, iş parçacığı havuzunun açlığa yol açabilir ve bu da uygulamanın yanıt vermemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="f715a-235">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="f715a-236">Yalnızca `AllowSynchronousIO` eşzamanlı G/Ç'yi desteklemeyen bir kitaplık kullanırken etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="f715a-236">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="f715a-237">Aşağıdaki örnek, senkron G/Ç'yi sağlar:</span><span class="sxs-lookup"><span data-stu-id="f715a-237">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="f715a-238">Diğer Kerkenez seçenekleri ve sınırları hakkında bilgi için bkz:</span><span class="sxs-lookup"><span data-stu-id="f715a-238">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="f715a-239">Bitiş noktası yapılandırması</span><span class="sxs-lookup"><span data-stu-id="f715a-239">Endpoint configuration</span></span>

<span data-ttu-id="f715a-240">Varsayılan olarak, ASP.NET Core bağlanır:</span><span class="sxs-lookup"><span data-stu-id="f715a-240">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="f715a-241">`https://localhost:5001`(yerel bir geliştirme sertifikası varsa)</span><span class="sxs-lookup"><span data-stu-id="f715a-241">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="f715a-242">Aşağıdakileri kullanarak URL'leri belirtin:</span><span class="sxs-lookup"><span data-stu-id="f715a-242">Specify URLs using the:</span></span>

* <span data-ttu-id="f715a-243">`ASPNETCORE_URLS`çevre değişkenidir.</span><span class="sxs-lookup"><span data-stu-id="f715a-243">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="f715a-244">`--urls`komut satırı bağımsız değişkeni.</span><span class="sxs-lookup"><span data-stu-id="f715a-244">`--urls` command-line argument.</span></span>
* <span data-ttu-id="f715a-245">`urls`ana bilgisayar yapılandırma anahtarı.</span><span class="sxs-lookup"><span data-stu-id="f715a-245">`urls` host configuration key.</span></span>
* <span data-ttu-id="f715a-246">`UseUrls`uzatma yöntemi.</span><span class="sxs-lookup"><span data-stu-id="f715a-246">`UseUrls` extension method.</span></span>

<span data-ttu-id="f715a-247">Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS).</span><span class="sxs-lookup"><span data-stu-id="f715a-247">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="f715a-248">Değeri yarı sütunlu ayrılmış bir liste olarak `"Urls": "http://localhost:8000;http://localhost:8001"`yapılandırın (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="f715a-248">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="f715a-249">Bu yaklaşımlar hakkında daha fazla bilgi için [Sunucu URL'leri](xref:fundamentals/host/web-host#server-urls) ve [Geçersiz Kılma yapılandırması'na](xref:fundamentals/host/web-host#override-configuration)bakın.</span><span class="sxs-lookup"><span data-stu-id="f715a-249">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="f715a-250">Geliştirme sertifikası oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="f715a-250">A development certificate is created:</span></span>

* <span data-ttu-id="f715a-251">[.NET Core SDK](/dotnet/core/sdk) yüklendiğinde.</span><span class="sxs-lookup"><span data-stu-id="f715a-251">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="f715a-252">[Dev-certs aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-252">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="f715a-253">Bazı tarayıcılar, yerel geliştirme sertifikasına güvenmek için açık izin verilmesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f715a-253">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="f715a-254">Proje şablonları uygulamaları varsayılan olarak HTTPS'de çalışacak şekilde yapılandırır ve [HTTPS yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.</span><span class="sxs-lookup"><span data-stu-id="f715a-254">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="f715a-255">Kestrel <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> için URL önekleri ve bağlantı noktalarını yapılandırmak için arama <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> veya <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> yöntemler.</span><span class="sxs-lookup"><span data-stu-id="f715a-255">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="f715a-256">`UseUrls`, `--urls` komut satırı bağımsız `urls` değişkeni, ana `ASPNETCORE_URLS` bilgisayar yapılandırma anahtarı ve ortam değişkeni de çalışır, ancak bu bölümde daha sonra belirtilen sınırlamalar vardır (varsayılan bir sertifika HTTPS bitiş noktası yapılandırması için kullanılabilir olmalıdır).</span><span class="sxs-lookup"><span data-stu-id="f715a-256">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="f715a-257">`KestrelServerOptions`Yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="f715a-257">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="f715a-258">YapılandırmaEndpointDefaults(Eylem\<DinlemeSeçenekleri>)</span><span class="sxs-lookup"><span data-stu-id="f715a-258">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="f715a-259">Belirtilen her bitiş `Action` noktası için çalışacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="f715a-259">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="f715a-260">Birden `ConfigureEndpointDefaults` çok kez `Action`arama, öncekilerin yerine geçen son `Action` belirtilenin yerini alır.</span><span class="sxs-lookup"><span data-stu-id="f715a-260">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="f715a-261">Aramadan <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> **önce** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> arayarak oluşturulan uç noktalarda varsayılanlar uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="f715a-261">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="f715a-262">YapılandırmaHttpsDefaults(Eylem\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="f715a-262">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="f715a-263">Her HTTPS bitiş `Action` noktası için çalışacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="f715a-263">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="f715a-264">Birden `ConfigureHttpsDefaults` çok kez `Action`arama, öncekilerin yerine geçen son `Action` belirtilenin yerini alır.</span><span class="sxs-lookup"><span data-stu-id="f715a-264">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="f715a-265">Aramadan <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> **önce** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> arayarak oluşturulan uç noktalarda varsayılanlar uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="f715a-265">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="f715a-266">Yapılandırma(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="f715a-266">Configure(IConfiguration)</span></span>

<span data-ttu-id="f715a-267">Kestrel'i ayarlamak için bir yapılandırma yükleyicisi oluşturur ve bu <xref:Microsoft.Extensions.Configuration.IConfiguration> da girdi olarak alır.</span><span class="sxs-lookup"><span data-stu-id="f715a-267">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="f715a-268">Yapılandırma Kerkenez için yapılandırma bölümüne kapsamı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-268">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="f715a-269">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="f715a-269">ListenOptions.UseHttps</span></span>

<span data-ttu-id="f715a-270">Kestrel'i HTTPS'yi kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f715a-270">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="f715a-271">`ListenOptions.UseHttps`Uzantı -ları:</span><span class="sxs-lookup"><span data-stu-id="f715a-271">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="f715a-272">`UseHttps`&ndash; Kestrel'i varsayılan sertifikayla HTTPS'yi kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f715a-272">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="f715a-273">Varsayılan sertifika yapılandırılmamışsa özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f715a-273">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="f715a-274">`ListenOptions.UseHttps`Parametre:</span><span class="sxs-lookup"><span data-stu-id="f715a-274">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="f715a-275">`filename`uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yol ve dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-275">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="f715a-276">`password`X.509 sertifika verilerine erişmek için gereken paroladır.</span><span class="sxs-lookup"><span data-stu-id="f715a-276">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="f715a-277">`configureOptions`bir `Action` yapılandırmak `HttpsConnectionAdapterOptions`için .</span><span class="sxs-lookup"><span data-stu-id="f715a-277">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="f715a-278">' `ListenOptions`yi döndürür.</span><span class="sxs-lookup"><span data-stu-id="f715a-278">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="f715a-279">`storeName`sertifikayı yüklemek için sertifika deposudur.</span><span class="sxs-lookup"><span data-stu-id="f715a-279">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="f715a-280">`subject`sertifikanın özne adıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-280">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="f715a-281">`allowInvalid`kendi imzalı sertifikalar gibi geçersiz sertifikaların dikkate alınıp alınmaması gerektiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="f715a-281">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="f715a-282">`location`sertifikayı yüklemek için mağaza konumudur.</span><span class="sxs-lookup"><span data-stu-id="f715a-282">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="f715a-283">`serverCertificate`X.509 sertifikasıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-283">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="f715a-284">Üretimde, HTTPS açıkça yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-284">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="f715a-285">En azından varsayılan sertifika sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-285">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="f715a-286">Sonraki açıklanan desteklenen yapılandırmalar:</span><span class="sxs-lookup"><span data-stu-id="f715a-286">Supported configurations described next:</span></span>

* <span data-ttu-id="f715a-287">Yapılandırma yok</span><span class="sxs-lookup"><span data-stu-id="f715a-287">No configuration</span></span>
* <span data-ttu-id="f715a-288">Yapılandırmadan varsayılan sertifikayı değiştirme</span><span class="sxs-lookup"><span data-stu-id="f715a-288">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="f715a-289">Koddaki varsayılanları değiştirme</span><span class="sxs-lookup"><span data-stu-id="f715a-289">Change the defaults in code</span></span>

<span data-ttu-id="f715a-290">*Yapılandırma yok*</span><span class="sxs-lookup"><span data-stu-id="f715a-290">*No configuration*</span></span>

<span data-ttu-id="f715a-291">Kerkenez üzerinde `http://localhost:5000` dinler `https://localhost:5001` ve (varsayılan bir sertifika varsa).</span><span class="sxs-lookup"><span data-stu-id="f715a-291">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="f715a-292">*Yapılandırmadan varsayılan sertifikayı değiştirme*</span><span class="sxs-lookup"><span data-stu-id="f715a-292">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="f715a-293">`CreateDefaultBuilder`kerkenez yapılandırmasını yüklemek için varsayılan olarak çağırır. `Configure(context.Configuration.GetSection("Kestrel"))`</span><span class="sxs-lookup"><span data-stu-id="f715a-293">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="f715a-294">Kestrel için varsayılan bir HTTPS uygulama ayarları yapılandırma şeması kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-294">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="f715a-295">Diskteki bir dosyadan veya bir sertifika deposundan kullanılacak URL'ler ve kullanılacak sertifikalar da dahil olmak üzere birden çok uç noktayı yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f715a-295">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="f715a-296">Aşağıdaki *appsettings.json* örnekte:</span><span class="sxs-lookup"><span data-stu-id="f715a-296">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="f715a-297">Geçersiz sertifikaların `true` kullanımına izin vermek için **AllowInvalid'i** ayarlayın (örneğin, kendi imzalı sertifikalar).</span><span class="sxs-lookup"><span data-stu-id="f715a-297">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="f715a-298">Sertifika belirtmeyen herhangi bir HTTPS bitiş noktası (Aşağıdaki örnekte**HttpsDefaultCert)** **Varsayılan** **Sertifikalar** > veya geliştirme sertifikası altında tanımlanan sertifikaya geri döner.</span><span class="sxs-lookup"><span data-stu-id="f715a-298">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="f715a-299">Herhangi bir sertifika düğümü için **Yol** ve **Parola** kullanmanın alternatifi, sertifika deposu alanlarını kullanarak sertifikayı belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="f715a-299">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="f715a-300">Örneğin, **Sertifikalar** > **Varsayılan** sertifika olarak belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="f715a-300">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="f715a-301">Şema notları:</span><span class="sxs-lookup"><span data-stu-id="f715a-301">Schema notes:</span></span>

* <span data-ttu-id="f715a-302">Uç nokta adları büyük/küçük harf duyarsızdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-302">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="f715a-303">Örneğin, `HTTPS` ve `Https` geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f715a-303">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="f715a-304">Parametre `Url` her bitiş noktası için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="f715a-304">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="f715a-305">Bu parametrenin biçimi, tek bir değerle sınırlı olması dışında üst düzey `Urls` yapılandırma parametresi ile aynıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-305">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="f715a-306">Bu uç noktalar, bunlara eklemek `Urls` yerine üst düzey yapılandırmada tanımlanan noktaların yerini alır.</span><span class="sxs-lookup"><span data-stu-id="f715a-306">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="f715a-307">Kodda tanımlanan uç `Listen` noktalar, yapılandırma bölümünde tanımlanan uç noktalarla birlikte kümülatiftir.</span><span class="sxs-lookup"><span data-stu-id="f715a-307">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="f715a-308">Bölüm `Certificate` isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-308">The `Certificate` section is optional.</span></span> <span data-ttu-id="f715a-309">`Certificate` Bölüm belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-309">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="f715a-310">Varsayılan yoksa, sunucu bir özel durum atar ve başlatılmaz.</span><span class="sxs-lookup"><span data-stu-id="f715a-310">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="f715a-311">Bu `Certificate` bölüm hem **Yol**&ndash;**Parolası** hem de **Konu**&ndash;**Deposu** sertifikalarını destekler.</span><span class="sxs-lookup"><span data-stu-id="f715a-311">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="f715a-312">Bağlantı noktası çakışmalarına neden olmadıkları sürece, herhangi bir uç nokta sayısı bu şekilde tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-312">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="f715a-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))`yapılandırılmış `KestrelConfigurationLoader` bir `.Endpoint(string name, listenOptions => { })` uç nokta nın ayarlarını tamamlamak için kullanılabilecek bir yöntemle döndürür:</span><span class="sxs-lookup"><span data-stu-id="f715a-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="f715a-314">`KestrelServerOptions.ConfigurationLoader`tarafından sağlanan gibi varolan yükleyici üzerinde yinelemeye devam etmek için <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>doğrudan erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-314">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="f715a-315">Her bitiş noktası için yapılandırma bölümü, özel `Endpoint` ayarların okunabilmesi için yöntemdeki seçeneklerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-315">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="f715a-316">Başka bir bölümle yeniden `options.Configure(context.Configuration.GetSection("{SECTION}"))` arayarak birden çok yapılandırma yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-316">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="f715a-317">Önceki örneklerde açıkça çağrılmadığı sürece `Load` yalnızca son yapılandırma kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-317">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="f715a-318">Metapackage, varsayılan yapılandırma `Load` bölümünün değiştirilebilecek şekilde çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="f715a-318">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="f715a-319">`KestrelConfigurationLoader`api `Listen` ailesi aşırı yükler `KestrelServerOptions` `Endpoint` olarak aynalar, bu nedenle kod ve config uç noktaları aynı yerde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-319">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="f715a-320">Bu aşırı yüklemeler adlar kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları tüketir.</span><span class="sxs-lookup"><span data-stu-id="f715a-320">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="f715a-321">*Koddaki varsayılanları değiştirme*</span><span class="sxs-lookup"><span data-stu-id="f715a-321">*Change the defaults in code*</span></span>

<span data-ttu-id="f715a-322">`ConfigureEndpointDefaults`ve `ConfigureHttpsDefaults` önceki senaryoda belirtilen `ListenOptions` varsayılan `HttpsConnectionAdapterOptions`sertifikayı geçersiz kılmak da dahil olmak üzere varsayılan ayarları değiştirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-322">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="f715a-323">`ConfigureEndpointDefaults`ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-323">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="f715a-324">*SNI için Kerkenez desteği*</span><span class="sxs-lookup"><span data-stu-id="f715a-324">*Kestrel support for SNI*</span></span>

<span data-ttu-id="f715a-325">[Sunucu Adı Göstergesi (SNI),](https://tools.ietf.org/html/rfc6066#section-3) aynı IP adresi ve bağlantı noktasında birden çok etki alanı barındırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-325">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="f715a-326">SNI'nin çalışması için istemci, sunucunun doğru sertifikayı sağlayabilmesi için TLS el sıkışması sırasında güvenli oturumun ana bilgisayar adını sunucuya gönderir.</span><span class="sxs-lookup"><span data-stu-id="f715a-326">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="f715a-327">İstemci, TLS el sıkışmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için verilen sertifikayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="f715a-327">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="f715a-328">Kerkenez `ServerCertificateSelector` geri arama yoluyla SNI destekler.</span><span class="sxs-lookup"><span data-stu-id="f715a-328">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="f715a-329">Geri arama, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesine izin vermek için bağlantı başına bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-329">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="f715a-330">SNI desteği şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="f715a-330">SNI support requires:</span></span>

* <span data-ttu-id="f715a-331">Hedef çerçevede `netcoreapp2.1` veya daha sonra çalışma.</span><span class="sxs-lookup"><span data-stu-id="f715a-331">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="f715a-332">On `net461` veya daha sonra, geri arama `name` çağrılır ama her zaman `null`.</span><span class="sxs-lookup"><span data-stu-id="f715a-332">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="f715a-333">`name` İstemci `null` TLS el sıkışmasında ana bilgisayar adı parametresini sağlamazsa da öyledir.</span><span class="sxs-lookup"><span data-stu-id="f715a-333">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="f715a-334">Tüm web siteleri aynı Kerkenez örneğinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="f715a-334">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="f715a-335">Kestrel ters bir proxy olmadan birden çok örnek arasında bir IP adresi ve bağlantı noktası paylaşımı desteklemez.</span><span class="sxs-lookup"><span data-stu-id="f715a-335">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="f715a-336">Bağlantı günlüğü</span><span class="sxs-lookup"><span data-stu-id="f715a-336">Connection logging</span></span>

<span data-ttu-id="f715a-337">Bağlantıdaki bayt düzeyinde iletişim için Hata Ayıklama düzeyi günlüklerini yaramak için arayın. <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*></span><span class="sxs-lookup"><span data-stu-id="f715a-337">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="f715a-338">Bağlantı günlüğü, TLS şifreleme sırasında ve yakınlık ların arkasında olduğu gibi düşük düzeyli iletişimdeki sorun giderme sorunları için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-338">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="f715a-339">Önce `UseConnectionLogging` `UseHttps`yerleştirilirse, şifreli trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-339">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="f715a-340">Sonra `UseConnectionLogging` `UseHttps`yerleştirilirse, şifresi çözülmüş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-340">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="f715a-341">TCP soketine bağlama</span><span class="sxs-lookup"><span data-stu-id="f715a-341">Bind to a TCP socket</span></span>

<span data-ttu-id="f715a-342">Yöntem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> bir TCP soketine bağlanır ve lambda seçeneği X.509 sertifika yapılandırmasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="f715a-342">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="f715a-343">Örnek, HTTPS'yi <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>bir bitiş noktası için .</span><span class="sxs-lookup"><span data-stu-id="f715a-343">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="f715a-344">Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak için aynı API'yi kullanın.</span><span class="sxs-lookup"><span data-stu-id="f715a-344">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="f715a-345">Unix soketine bağlanma</span><span class="sxs-lookup"><span data-stu-id="f715a-345">Bind to a Unix socket</span></span>

<span data-ttu-id="f715a-346">Bu örnekte gösterildiği <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> gibi, Nginx ile geliştirilmiş performans için bir Unix soketinde dinleyin:</span><span class="sxs-lookup"><span data-stu-id="f715a-346">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="f715a-347">`server`  >  `location`  >  `proxy_pass` Nginx yapılandırma dosyasında, girişini `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="f715a-347">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="f715a-348">`{KESTREL SOCKET}`verilen soketin adıdır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (örneğin, `kestrel-test.sock` önceki örnekte).</span><span class="sxs-lookup"><span data-stu-id="f715a-348">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="f715a-349">Soketin Nginx tarafından yazılabilir olduğundan `chmod go+w /tmp/kestrel-test.sock`emin olun (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="f715a-349">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="f715a-350">Bağlantı Noktası 0</span><span class="sxs-lookup"><span data-stu-id="f715a-350">Port 0</span></span>

<span data-ttu-id="f715a-351">Bağlantı noktası `0` numarası belirtildiğinde, Kerkenez dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır.</span><span class="sxs-lookup"><span data-stu-id="f715a-351">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="f715a-352">Aşağıdaki örnek, Kestrel'in çalışma zamanında gerçekte hangi bağlantı noktasına bağlı olduğunu nasıl belirleyeceklerini gösterir:</span><span class="sxs-lookup"><span data-stu-id="f715a-352">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="f715a-353">Uygulama çalıştırıldığında, konsol penceresi çıkışı uygulamaya ulaşılabilen dinamik bağlantı noktasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="f715a-353">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="f715a-354">Sınırlamalar</span><span class="sxs-lookup"><span data-stu-id="f715a-354">Limitations</span></span>

<span data-ttu-id="f715a-355">Uç noktaları aşağıdaki yaklaşımlarla yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="f715a-355">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="f715a-356">`--urls`komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="f715a-356">`--urls` command-line argument</span></span>
* <span data-ttu-id="f715a-357">`urls`ana bilgisayar yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="f715a-357">`urls` host configuration key</span></span>
* <span data-ttu-id="f715a-358">`ASPNETCORE_URLS`çevre değişkeni</span><span class="sxs-lookup"><span data-stu-id="f715a-358">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="f715a-359">Bu yöntemler, Kerkenez dışındaki sunucularla kod çalışması yapmak için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-359">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="f715a-360">Ancak, aşağıdaki sınırlamalar dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="f715a-360">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="f715a-361">HTTPS bitiş noktası yapılandırmasında varsayılan bir sertifika sağlanmadıkça (örneğin, bu konuda `KestrelServerOptions` daha önce gösterildiği gibi yapılandırma veya yapılandırma dosyasını kullanmak gibi) HTTPS bu yaklaşımlarla kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="f715a-361">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="f715a-362">Hem yaklaşımlar `Listen` `UseUrls` hem de yaklaşımlar aynı `Listen` anda kullanıldığında, `UseUrls` uç noktalar uç noktaları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="f715a-362">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="f715a-363">IIS uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="f715a-363">IIS endpoint configuration</span></span>

<span data-ttu-id="f715a-364">IIS kullanırken, IIS geçersiz kılma bağlamaları için URL `Listen` `UseUrls`bağlamaları ya da .</span><span class="sxs-lookup"><span data-stu-id="f715a-364">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="f715a-365">Daha fazla bilgi için [ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="f715a-365">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="f715a-366">ListenOptions.Protokoller</span><span class="sxs-lookup"><span data-stu-id="f715a-366">ListenOptions.Protocols</span></span>

<span data-ttu-id="f715a-367">Özellik, `Protocols` bağlantı bitiş noktasında`HttpProtocols`veya sunucu için etkinleştirilmiş HTTP protokollerini ( ) kurar.</span><span class="sxs-lookup"><span data-stu-id="f715a-367">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="f715a-368">`HttpProtocols` Enumdan `Protocols` özelliğe bir değer atayın.</span><span class="sxs-lookup"><span data-stu-id="f715a-368">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="f715a-369">`HttpProtocols`enum değeri</span><span class="sxs-lookup"><span data-stu-id="f715a-369">`HttpProtocols` enum value</span></span> | <span data-ttu-id="f715a-370">Bağlantı protokolüne izin verilir</span><span class="sxs-lookup"><span data-stu-id="f715a-370">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="f715a-371">Sadece HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="f715a-371">HTTP/1.1 only.</span></span> <span data-ttu-id="f715a-372">TLS ile veya TLS olmadan kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-372">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="f715a-373">Sadece HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="f715a-373">HTTP/2 only.</span></span> <span data-ttu-id="f715a-374">TLS olmadan kullanılabilir, ancak istemci [Bir Ön Bilgi modunu](https://tools.ietf.org/html/rfc7540#section-3.4)destekliyorsa.</span><span class="sxs-lookup"><span data-stu-id="f715a-374">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="f715a-375">HTTP/1.1 ve HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="f715a-375">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="f715a-376">HTTP/2, TLS [Uygulama Katmanı Protokolü Anlaşması 'nda (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) el sıkışmasında istemcinin HTTP/2'yi seçmesini gerektirir; aksi takdirde, bağlantı varsayılan OLARAK HTTP/1.1'e olur.</span><span class="sxs-lookup"><span data-stu-id="f715a-376">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="f715a-377">Herhangi `ListenOptions.Protocols` bir uç nokta `HttpProtocols.Http1AndHttp2`için varsayılan değer.</span><span class="sxs-lookup"><span data-stu-id="f715a-377">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="f715a-378">HTTP/2 için TLS kısıtlamaları:</span><span class="sxs-lookup"><span data-stu-id="f715a-378">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="f715a-379">TLS sürüm 1.2 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="f715a-379">TLS version 1.2 or later</span></span>
* <span data-ttu-id="f715a-380">Yeniden anlaşma devre dışı</span><span class="sxs-lookup"><span data-stu-id="f715a-380">Renegotiation disabled</span></span>
* <span data-ttu-id="f715a-381">Sıkıştırma devre dışı</span><span class="sxs-lookup"><span data-stu-id="f715a-381">Compression disabled</span></span>
* <span data-ttu-id="f715a-382">Minimum geçici anahtar değişim boyutları:</span><span class="sxs-lookup"><span data-stu-id="f715a-382">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="f715a-383">Eliptik eğri Diffie-Hellman &lbrack;(ECDHE) [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; &ndash; 224 bit minimum</span><span class="sxs-lookup"><span data-stu-id="f715a-383">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="f715a-384">Sonlu alan Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; &ndash; 2048 bit minimum</span><span class="sxs-lookup"><span data-stu-id="f715a-384">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="f715a-385">Şifreli paket kara listeye alınmadı</span><span class="sxs-lookup"><span data-stu-id="f715a-385">Cipher suite not blacklisted</span></span>

<span data-ttu-id="f715a-386">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack; &lbrack; `FIPS186` &rbrack; P-256 eliptik eğrisi ile varsayılan olarak desteklenir. `TLS-ECDHE` &rbrack;</span><span class="sxs-lookup"><span data-stu-id="f715a-386">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="f715a-387">Aşağıdaki örnek, 8000 no'l'daki porttaki HTTP/1.1 ve HTTP/2 bağlantılarına izin verir.</span><span class="sxs-lookup"><span data-stu-id="f715a-387">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="f715a-388">Bağlantılar, sağlanan bir sertifika ile TLS tarafından güvence altına alınır:</span><span class="sxs-lookup"><span data-stu-id="f715a-388">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="f715a-389">TLS el sıkışmalarını gerekirse belirli şifreler için bağlantı başına filtrelemek için Bağlantı Middleware'i kullanın.</span><span class="sxs-lookup"><span data-stu-id="f715a-389">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="f715a-390">Aşağıdaki örnek, <xref:System.NotSupportedException> uygulamanın desteklemediği herhangi bir şifreleme algoritması için atar.</span><span class="sxs-lookup"><span data-stu-id="f715a-390">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="f715a-391">Alternatif olarak, [iTlsHandshakeFeature.CipherAlgorithm'ı](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) kabul edilebilir şifre paketleri listesiyle tanımlayın ve karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="f715a-391">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="f715a-392">[CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) şifreleme algoritması ile şifreleme kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="f715a-392">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="f715a-393">Bağlantı filtreleme bir <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda ile de yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="f715a-393">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="f715a-394">Linux'ta <xref:System.Net.Security.CipherSuitesPolicy> TLS el sıkışmalarını bağlantı başına filtrelemek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="f715a-394">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="f715a-395">*Protokolü yapılandırmadan ayarlama*</span><span class="sxs-lookup"><span data-stu-id="f715a-395">*Set the protocol from configuration*</span></span>

<span data-ttu-id="f715a-396">`CreateDefaultBuilder`kerkenez yapılandırmasını yüklemek için varsayılan olarak çağırır. `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`</span><span class="sxs-lookup"><span data-stu-id="f715a-396">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="f715a-397">Aşağıdaki *appsettings.json* örneği, tüm uç noktalar için varsayılan bağlantı protokolü olarak HTTP/1.1'i oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f715a-397">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="f715a-398">Aşağıdaki *appsettings.json* örneği belirli bir bitiş noktası için HTTP/1.1 bağlantı protokolünü oluşturur:</span><span class="sxs-lookup"><span data-stu-id="f715a-398">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="f715a-399">Kodda belirtilen protokoller yapılandırma tarafından ayarlanan değerleri geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="f715a-399">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="f715a-400">Aktarım yapılandırması</span><span class="sxs-lookup"><span data-stu-id="f715a-400">Transport configuration</span></span>

<span data-ttu-id="f715a-401">Libuv kullanımını gerektiren projeler için<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>( ):</span><span class="sxs-lookup"><span data-stu-id="f715a-401">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="f715a-402">Uygulamanın proje dosyasına [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) paketine bağımlılık ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f715a-402">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="f715a-403">Çağrı: <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> `IWebHostBuilder`</span><span class="sxs-lookup"><span data-stu-id="f715a-403">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="f715a-404">URL önekleri</span><span class="sxs-lookup"><span data-stu-id="f715a-404">URL prefixes</span></span>

<span data-ttu-id="f715a-405">URL `UseUrls`önekleri, komut `--urls` `urls` satırı bağımsız değişkeni, ana bilgisayar yapılandırma anahtarı veya `ASPNETCORE_URLS` ortam değişkeni kullanırken aşağıdaki biçimlerin herhangi birinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-405">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="f715a-406">Yalnızca HTTP URL önekleri geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f715a-406">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="f715a-407">Kestrel, URL bağlamalarını kullanarak `UseUrls`yapılandırırken HTTPS'yi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="f715a-407">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="f715a-408">Bağlantı noktası numarası olan IPv4 adresi</span><span class="sxs-lookup"><span data-stu-id="f715a-408">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="f715a-409">`0.0.0.0`tüm IPv4 adreslerine bağlanan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="f715a-409">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="f715a-410">Bağlantı noktası numarası olan IPv6 adresi</span><span class="sxs-lookup"><span data-stu-id="f715a-410">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="f715a-411">`[::]`IPv4'ün `0.0.0.0`IPv6 eşdeğeridir.</span><span class="sxs-lookup"><span data-stu-id="f715a-411">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="f715a-412">Bağlantı noktası numarası olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="f715a-412">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="f715a-413">Ev sahibi `*`adları `+`ve özel değildir.</span><span class="sxs-lookup"><span data-stu-id="f715a-413">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="f715a-414">Geçerli bir IP adresi olarak `localhost` tanınmayan veya tüm IPv4 ve IPv6 IP'lerine bağlanan her şey.</span><span class="sxs-lookup"><span data-stu-id="f715a-414">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="f715a-415">Farklı ana bilgisayar adlarını aynı bağlantı noktasındaki farklı ASP.NET Core uygulamalarına bağlamak için [HTTP.sys](xref:fundamentals/servers/httpsys) veya IIS, Nginx veya Apache gibi ters proxy sunucusu kullanın.</span><span class="sxs-lookup"><span data-stu-id="f715a-415">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="f715a-416">Ters proxy yapılandırmasında barındırma [ana bilgisayar filtreleme](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f715a-416">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="f715a-417">Bağlantı `localhost` noktası numarası veya bağlantı noktası numarası olan loopback IP ile ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="f715a-417">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="f715a-418">Belirtildiğinde, `localhost` Kerkenez hem IPv4 hem de IPv6 geri dönüş arabirimlerine bağlanmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="f715a-418">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="f715a-419">İstenen bağlantı noktası her iki döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılmaz.</span><span class="sxs-lookup"><span data-stu-id="f715a-419">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="f715a-420">Geri dönüş arabirimi başka bir nedenle kullanılamıyorsa (en sık IPv6 desteklenmediği için), Kestrel bir uyarı yı günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="f715a-420">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="f715a-421">Ana bilgisayar filtresi</span><span class="sxs-lookup"><span data-stu-id="f715a-421">Host filtering</span></span>

<span data-ttu-id="f715a-422">Kerkenez gibi öneklere dayalı yapılandırmayı `http://example.com:5000`desteklerken, Kerkenez büyük ölçüde ana bilgisayar adını yok sayar.</span><span class="sxs-lookup"><span data-stu-id="f715a-422">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="f715a-423">Ana `localhost` bilgisayar, geri dönüş adreslerine bağlanmak için kullanılan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="f715a-423">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="f715a-424">Açık bir IP adresi dışındaki tüm ana bilgisayar, tüm genel IP adreslerine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="f715a-424">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="f715a-425">`Host`üstbilgi doğrulanmadı.</span><span class="sxs-lookup"><span data-stu-id="f715a-425">`Host` headers aren't validated.</span></span>

<span data-ttu-id="f715a-426">Geçici çözüm olarak, Ana Bilgisayar Filtreleme Ara Ware'i kullanın.</span><span class="sxs-lookup"><span data-stu-id="f715a-426">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="f715a-427">Ana Bilgisayar Filtreleme Middleware, ASP.NET Core uygulamaları için örtülü olarak sağlanan [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="f715a-427">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="f715a-428">Middleware tarafından <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>eklenir , <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>hangi çağırır:</span><span class="sxs-lookup"><span data-stu-id="f715a-428">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="f715a-429">Ana Bilgisayar Filtreleme Middleware varsayılan olarak devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-429">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="f715a-430">Ara yazılımı etkinleştirmek için `AllowedHosts` *appsettings.json*/uygulama ayarlarında bir anahtar*tanımlayın.\< EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="f715a-430">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="f715a-431">Değer, bağlantı noktası numarası olmayan ana bilgisayar adlarının yarı sütunlu sınırlı bir listesidir:</span><span class="sxs-lookup"><span data-stu-id="f715a-431">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="f715a-432">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f715a-432">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="f715a-433">[İlezli Üstbilgi Middleware](xref:host-and-deploy/proxy-load-balancer) de bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçenek vardır.</span><span class="sxs-lookup"><span data-stu-id="f715a-433">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="f715a-434">İletilenmiş Üstbilgi Middleware ve Ana Bilgisayar Filtreleme Middleware farklı senaryolar için benzer işlevsellik var.</span><span class="sxs-lookup"><span data-stu-id="f715a-434">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="f715a-435">Üstbilgi nin ters proxy sunucusu veya `Host` yük dengeleyicisi ilerlerken üstbilgi korunmadığında Iletili Üstbilgi Middleware ile ayarlama `AllowedHosts` uygundur.</span><span class="sxs-lookup"><span data-stu-id="f715a-435">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="f715a-436">Kerkenez genel kullanıma dönük kenar sunucusu olarak kullanıldığında veya `AllowedHosts` `Host` üstbilgi doğrudan iledildiğinde Ana Bilgisayar Filtreleme Middleware ile ayarlama uygundur.</span><span class="sxs-lookup"><span data-stu-id="f715a-436">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="f715a-437">İlliyeli Üstbilgiler Middleware hakkında <xref:host-and-deploy/proxy-load-balancer>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="f715a-437">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="f715a-438">Kerkenez core ASP.NET [için](xref:fundamentals/servers/index)bir çapraz platform web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="f715a-438">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="f715a-439">Kerkenez, ASP.NET Core proje şablonlarında varsayılan olarak bulunan web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="f715a-439">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="f715a-440">Kerkenez aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="f715a-440">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="f715a-441">HTTPS</span><span class="sxs-lookup"><span data-stu-id="f715a-441">HTTPS</span></span>
* <span data-ttu-id="f715a-442">[WebSockets'i](https://github.com/aspnet/websockets) etkinleştirmek için kullanılan opak yükseltme</span><span class="sxs-lookup"><span data-stu-id="f715a-442">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="f715a-443">Nginx arkasında yüksek performans için Unix soketleri</span><span class="sxs-lookup"><span data-stu-id="f715a-443">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="f715a-444">HTTP/2 (macOS&dagger;hariç)</span><span class="sxs-lookup"><span data-stu-id="f715a-444">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="f715a-445">&dagger;HTTP/2 gelecekte yayınlanacak bir sürümde macOS'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="f715a-445">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="f715a-446">Kerkenez, .NET Core'un desteklediği tüm platformlarda ve sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="f715a-446">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="f715a-447">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f715a-447">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="f715a-448">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="f715a-448">HTTP/2 support</span></span>

<span data-ttu-id="f715a-449">Aşağıdaki temel gereksinimler yerine [getirildiğinde, http/2](https://httpwg.org/specs/rfc7540.html) ASP.NET Core uygulamaları için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="f715a-449">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="f715a-450">İşletim sistemi&dagger;</span><span class="sxs-lookup"><span data-stu-id="f715a-450">Operating system&dagger;</span></span>
  * <span data-ttu-id="f715a-451">Windows Server 2016/Windows 10 veya sonrası&Dagger;</span><span class="sxs-lookup"><span data-stu-id="f715a-451">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="f715a-452">OpenSSL 1.0.2 veya sonrası (örneğin, Ubuntu 16.04 veya sonrası) ile Linux</span><span class="sxs-lookup"><span data-stu-id="f715a-452">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="f715a-453">Hedef çerçeve: .NET Core 2.2 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="f715a-453">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="f715a-454">[Uygulama Katmanı Protokolü Anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı</span><span class="sxs-lookup"><span data-stu-id="f715a-454">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="f715a-455">TLS 1.2 veya daha sonra bağlantı</span><span class="sxs-lookup"><span data-stu-id="f715a-455">TLS 1.2 or later connection</span></span>

<span data-ttu-id="f715a-456">&dagger;HTTP/2 gelecekte yayınlanacak bir sürümde macOS'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="f715a-456">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="f715a-457">&Dagger;Kerkenez, Windows Server 2012 R2 ve Windows 8.1'de HTTP/2 için sınırlı bir desteğe sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f715a-457">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="f715a-458">Bu işletim sistemlerinde bulunan desteklenen TLS şifreleme paketlerinin listesi sınırlı olduğundan destek sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-458">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="f715a-459">TLS bağlantılarını güvence altına almak için Eliptik Eğri Dijital İmza Algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekebilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-459">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="f715a-460">Bir HTTP/2 bağlantısı kuruluysa, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) bildirir. `HTTP/2`</span><span class="sxs-lookup"><span data-stu-id="f715a-460">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="f715a-461">HTTP/2 varsayılan olarak devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-461">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="f715a-462">Yapılandırma hakkında daha fazla bilgi için [Kerkenez seçenekleri](#kestrel-options) ve [ListenOptions.Protocols](#listenoptionsprotocols) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="f715a-462">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="f715a-463">Ters proxy ile Kerkenez ne zaman kullanılır</span><span class="sxs-lookup"><span data-stu-id="f715a-463">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="f715a-464">Kerkenez kendisi tarafından veya *ters proxy sunucusu*ile kullanılabilir , Internet Bilgi Hizmetleri gibi [(IIS)](https://www.iis.net/), [Nginx](https://nginx.org), veya [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="f715a-464">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="f715a-465">Ters proxy sunucusu ağdan HTTP isteklerini alır ve bunları Kestrel'e ileter.</span><span class="sxs-lookup"><span data-stu-id="f715a-465">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="f715a-466">Kerkenez bir kenar (Internet bakan) web sunucusu olarak kullanılır:</span><span class="sxs-lookup"><span data-stu-id="f715a-466">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kerkenez ters proxy sunucusu olmadan internet ile doğrudan iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="f715a-468">Ters proxy yapılandırmasında kullanılan kerkenez:</span><span class="sxs-lookup"><span data-stu-id="f715a-468">Kestrel used in a reverse proxy configuration:</span></span>

![Kerkenez, IIS, Nginx veya Apache gibi ters proxy sunucusu aracılığıyla Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="f715a-470">Ters proxy sunucusu olan veya olmayan yapılandırma, desteklenen bir barındırma yapılandırmasıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-470">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="f715a-471">Ters proxy sunucusu olmayan bir kenar sunucusu olarak kullanılan kerkenez, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="f715a-471">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="f715a-472">Kestrel bir bağlantı noktasında dinleyecek şekilde yapılandırıldığında, Kestrel isteklerin `Host` üstbilgilerinden bağımsız olarak o bağlantı noktasının tüm trafiğini işler.</span><span class="sxs-lookup"><span data-stu-id="f715a-472">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="f715a-473">Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel'e iletme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f715a-473">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="f715a-474">Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucusu kullanmak iyi bir seçim olabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-474">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="f715a-475">Ters proxy:</span><span class="sxs-lookup"><span data-stu-id="f715a-475">A reverse proxy:</span></span>

* <span data-ttu-id="f715a-476">Barındırdığı uygulamaların açıkta kalan ortak yüzey alanını sınırlayabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-476">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="f715a-477">Ek bir yapılandırma ve savunma katmanı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="f715a-477">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="f715a-478">Mevcut altyapı ile daha iyi entegre olabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-478">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="f715a-479">Yük dengeleme ve güvenli iletişim (HTTPS) yapılandırması basitleştirin.</span><span class="sxs-lookup"><span data-stu-id="f715a-479">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="f715a-480">Yalnızca ters proxy sunucusu bir X.509 sertifikası gerektirir ve bu sunucu apaçık HTTP kullanarak dahili ağdaki uygulamasunucularıyla iletişim kurabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-480">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="f715a-481">Ters proxy yapılandırmasında barındırma [ana bilgisayar filtreleme](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f715a-481">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="f715a-482">ASP.NET Core uygulamalarında Kerkenez nasıl kullanılır?</span><span class="sxs-lookup"><span data-stu-id="f715a-482">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="f715a-483">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) paketi [Microsoft.AspNetCore.App metapaketinde](xref:fundamentals/metapackage-app)yer almaktadır.</span><span class="sxs-lookup"><span data-stu-id="f715a-483">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f715a-484">ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır.</span><span class="sxs-lookup"><span data-stu-id="f715a-484">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="f715a-485">Program.cs *Program.cs*, şablon kodu <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>aramaları <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> , hangi arka planda çağırır.</span><span class="sxs-lookup"><span data-stu-id="f715a-485">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="f715a-486">Ana bilgisayar `CreateDefaultBuilder` hakkında daha fazla bilgi ve bina için <xref:fundamentals/host/web-host#set-up-a-host>ana bilgisayar bölümünü *ayarla'ya* bakın.</span><span class="sxs-lookup"><span data-stu-id="f715a-486">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="f715a-487">Aradıktan `CreateDefaultBuilder`sonra ek yapılandırma `ConfigureKestrel`sağlamak için , kullanın:</span><span class="sxs-lookup"><span data-stu-id="f715a-487">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="f715a-488">Uygulama ana bilgisayarını `CreateDefaultBuilder` ayarlamak için aramazsa, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> aramadan **önce** arayın: `ConfigureKestrel`</span><span class="sxs-lookup"><span data-stu-id="f715a-488">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="f715a-489">Kerkenez seçenekleri</span><span class="sxs-lookup"><span data-stu-id="f715a-489">Kestrel options</span></span>

<span data-ttu-id="f715a-490">Kestrel web sunucusu, özellikle Internet'e dönük dağıtımlarda kullanışlı olan kısıtlama yapılandırma seçeneklerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f715a-490">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="f715a-491"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> Sınıfın <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> özelliği üzerindeki kısıtlamaları ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f715a-491">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="f715a-492">Özellik `Limits` sınıfın bir örneğini <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> tutar.</span><span class="sxs-lookup"><span data-stu-id="f715a-492">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="f715a-493">Aşağıdaki <xref:Microsoft.AspNetCore.Server.Kestrel.Core> örneklerde ad alanı kullanılır:</span><span class="sxs-lookup"><span data-stu-id="f715a-493">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="f715a-494">Aşağıdaki örneklerde C# koduyla yapılandırılan kerkenez seçenekleri de bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-494">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="f715a-495">Örneğin, Dosya Yapılandırma Sağlayıcısı kestrel yapılandırmasını bir *appsettings.json* veya *appsettings'ten yükleyebilir.{ Çevre}.json* dosyası:</span><span class="sxs-lookup"><span data-stu-id="f715a-495">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="f715a-496">Aşağıdaki **yaklaşımlardan birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="f715a-496">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="f715a-497">Kerkenezi `Startup.ConfigureServices`yapılandır:</span><span class="sxs-lookup"><span data-stu-id="f715a-497">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="f715a-498">Sınıfa bir `IConfiguration` örnek `Startup` enjekte edin.</span><span class="sxs-lookup"><span data-stu-id="f715a-498">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="f715a-499">Aşağıdaki örnek, enjekte edilen yapılandırmanın `Configuration` özelliğe atandığını varsayar.</span><span class="sxs-lookup"><span data-stu-id="f715a-499">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="f715a-500">In `Startup.ConfigureServices`, `Kestrel` Kerkenez yapılandırma içine yapılandırma bölümü yükleyin:</span><span class="sxs-lookup"><span data-stu-id="f715a-500">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="f715a-501">Ev sahibini kurarken Kerkenezi yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="f715a-501">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="f715a-502">*Program.cs,* yapılandırma `Kestrel` bölümünü Kestrel'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="f715a-502">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="f715a-503">Önceki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.</span><span class="sxs-lookup"><span data-stu-id="f715a-503">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="f715a-504">Zaman ayarı tutma</span><span class="sxs-lookup"><span data-stu-id="f715a-504">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="f715a-505">Canlı kalma [zaman ını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f715a-505">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="f715a-506">Varsayılan olarak 2 dakika dır.</span><span class="sxs-lookup"><span data-stu-id="f715a-506">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="f715a-507">Maksimum istemci bağlantıları</span><span class="sxs-lookup"><span data-stu-id="f715a-507">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="f715a-508">Aşağıdaki kodla tüm uygulama için eşzamanlı açık TCP bağlantısı sayısı ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="f715a-508">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="f715a-509">HTTP veya HTTPS'den başka bir protokole yükseltilmiş bağlantılar için ayrı bir sınır vardır (örneğin, bir WebSockets isteğinde).</span><span class="sxs-lookup"><span data-stu-id="f715a-509">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="f715a-510">Bir bağlantı yükseltildikten sonra, sınıra `MaxConcurrentConnections` göre sayılmaz.</span><span class="sxs-lookup"><span data-stu-id="f715a-510">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="f715a-511">Maksimum bağlantı sayısı varsayılan olarak sınırsızdır (null).</span><span class="sxs-lookup"><span data-stu-id="f715a-511">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="f715a-512">Maksimum istek gövde boyutu</span><span class="sxs-lookup"><span data-stu-id="f715a-512">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="f715a-513">Varsayılan maksimum istek gövde boyutu yaklaşık 28,6 MB olan 30.000.000 bayttır.</span><span class="sxs-lookup"><span data-stu-id="f715a-513">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="f715a-514">Bir ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> önerilen yaklaşım, özniteliği bir eylem yönteminde kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="f715a-514">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="f715a-515">Aşağıda, her istekte uygulamanın kısıtlamasının nasıl yapılandırılabildiğini gösteren bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="f715a-515">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="f715a-516">Ara yazılımda belirli bir istek üzerindeki ayarı geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="f715a-516">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="f715a-517">Uygulama isteği okumaya başladıktan sonra uygulama istek sınırını yapılandırırsa bir özel durum atılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-517">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="f715a-518">Özelliğin `MaxRequestBodySize` salt `IsReadOnly` okunur durumda olup olmadığını gösteren bir özellik vardır, bu da sınırı yapılandırmak için çok geç olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="f715a-518">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="f715a-519">Bir uygulama [ASP.NET Çekirdek Modülü'nün](xref:host-and-deploy/aspnet-core-module)arkasında [işlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model) kaldığında, IIS sınırı zaten belirlediği için Kestrel'in istek gövde boyutu sınırı devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-519">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="f715a-520">Minimum istek gövde veri oranı</span><span class="sxs-lookup"><span data-stu-id="f715a-520">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="f715a-521">Kerkenez, verilerin bayt/saniye cinsinden belirtilen oranda gelişip ulaşmadığında her saniye kontrol eder.</span><span class="sxs-lookup"><span data-stu-id="f715a-521">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="f715a-522">Oran minimumun altına düşerse, bağlantı zamanlanır. Yetkisiz kullanım süresi, Kestrel'in müşteriye gönderim oranını en aza kadar yükseltmesi için verdiği süredir; bu süre içinde fiyat kontrol edilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-522">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="f715a-523">Yetkisiz kullanım süresi, TCP'nin yavaş başlaması nedeniyle başlangıçta veri gönderen bağlantıların düşmesini önlemeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="f715a-523">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="f715a-524">Varsayılan minimum oran, 5 saniyelik yetkisiz kullanım süresine sahip 240 bayt/saniyedir.</span><span class="sxs-lookup"><span data-stu-id="f715a-524">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="f715a-525">Yanıt için de minimum ücret uygulanır.</span><span class="sxs-lookup"><span data-stu-id="f715a-525">A minimum rate also applies to the response.</span></span> <span data-ttu-id="f715a-526">İstek sınırını ve yanıt sınırını ayarlamak için kod, `RequestBody` özellik `Response` ve arabirim adlarına sahip olmak veya bunlar dışında aynıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-526">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="f715a-527">Aşağıda, *Program.cs*minimum veri oranlarının nasıl yapılandırılabildiğini gösteren bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="f715a-527">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="f715a-528">Ara yazılımda istek başına minimum ücret limitlerini geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="f715a-528">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="f715a-529">Önceki örnekte başvurulan oran özelliği `HttpContext.Features` nin hiçbiri HTTP/2 istekleri için bulunmaz, çünkü istek başına fiyat sınırlarının değiştirilmesi, protokolün istek çoklama desteği nedeniyle HTTP/2 için desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="f715a-529">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="f715a-530">Http/1.x ve `KestrelServerOptions.Limits` HTTP/2 bağlantıları için de sunucu genelinde yapılandırılan oran sınırları hala geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f715a-530">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="f715a-531">Üstbilgi zaman alabı isteği</span><span class="sxs-lookup"><span data-stu-id="f715a-531">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="f715a-532">Sunucunun istek üstbilgilerini almak için harcadığı maksimum süreyi alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f715a-532">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="f715a-533">Varsayılan olarak 30 saniye.</span><span class="sxs-lookup"><span data-stu-id="f715a-533">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="f715a-534">Bağlantı başına maksimum akış</span><span class="sxs-lookup"><span data-stu-id="f715a-534">Maximum streams per connection</span></span>

<span data-ttu-id="f715a-535">`Http2.MaxStreamsPerConnection`http/2 bağlantısı başına eşzamanlı istek akışlarının sayısını sınırlar.</span><span class="sxs-lookup"><span data-stu-id="f715a-535">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="f715a-536">Fazla akışreddedilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-536">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="f715a-537">Varsayılan değer 100’dür.</span><span class="sxs-lookup"><span data-stu-id="f715a-537">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="f715a-538">Üstbilgi tablo boyutu</span><span class="sxs-lookup"><span data-stu-id="f715a-538">Header table size</span></span>

<span data-ttu-id="f715a-539">HPACK kod çözücü http/2 bağlantıları için HTTP başlıkları decompresses.</span><span class="sxs-lookup"><span data-stu-id="f715a-539">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="f715a-540">`Http2.HeaderTableSize`HPACK kod çözücünün kullandığı üstbilgi sıkıştırma tablosunun boyutunu sınırlar.</span><span class="sxs-lookup"><span data-stu-id="f715a-540">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="f715a-541">Değer sekizli olarak sağlanır ve sıfırdan büyük olmalıdır (0).</span><span class="sxs-lookup"><span data-stu-id="f715a-541">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="f715a-542">Varsayılan değer 4096'dır.</span><span class="sxs-lookup"><span data-stu-id="f715a-542">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="f715a-543">Maksimum kare boyutu</span><span class="sxs-lookup"><span data-stu-id="f715a-543">Maximum frame size</span></span>

<span data-ttu-id="f715a-544">`Http2.MaxFrameSize`almak için HTTP/2 bağlantı çerçevesi yükünün maksimum boyutunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="f715a-544">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="f715a-545">Değeri sekizli olarak sağlanır ve 2^14 (16.384) ile 2^24-1 (16.777.215) arasında olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-545">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="f715a-546">Varsayılan değer 2^14 (16.384) 'dir.</span><span class="sxs-lookup"><span data-stu-id="f715a-546">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="f715a-547">Maksimum istek üstbilgi boyutu</span><span class="sxs-lookup"><span data-stu-id="f715a-547">Maximum request header size</span></span>

<span data-ttu-id="f715a-548">`Http2.MaxRequestHeaderFieldSize`istek üstbilgi değerlerinin sekizlisinde izin verilen maksimum boyutu gösterir.</span><span class="sxs-lookup"><span data-stu-id="f715a-548">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="f715a-549">Bu sınır, sıkıştırılmış ve sıkıştırılmamış gösterimlerinde birlikte hem ad hem de değer için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f715a-549">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="f715a-550">Değer sıfırdan büyük olmalıdır (0).</span><span class="sxs-lookup"><span data-stu-id="f715a-550">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="f715a-551">Varsayılan değer 8.192'dir.</span><span class="sxs-lookup"><span data-stu-id="f715a-551">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="f715a-552">İlk bağlantı penceresi boyutu</span><span class="sxs-lookup"><span data-stu-id="f715a-552">Initial connection window size</span></span>

<span data-ttu-id="f715a-553">`Http2.InitialConnectionWindowSize`bağlantı başına tüm istekler (akışlar) arasında toplanan sunucu arabellekleri baytlar maksimum istek gövde verileri gösterir.</span><span class="sxs-lookup"><span data-stu-id="f715a-553">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="f715a-554">İstekler de `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="f715a-554">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="f715a-555">Değer 65.535'ten büyük veya eşit ve 2^31'den az olmalıdır (2,147,483,648).</span><span class="sxs-lookup"><span data-stu-id="f715a-555">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="f715a-556">Varsayılan değer 128 KB (131.072) 'dir.</span><span class="sxs-lookup"><span data-stu-id="f715a-556">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="f715a-557">İlk akış penceresi boyutu</span><span class="sxs-lookup"><span data-stu-id="f715a-557">Initial stream window size</span></span>

<span data-ttu-id="f715a-558">`Http2.InitialStreamWindowSize`istek başına sunucu arabelleklerini baytlar halinde ki maksimum istek gövdesi verilerini gösterir (akış).</span><span class="sxs-lookup"><span data-stu-id="f715a-558">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="f715a-559">İstekler de `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="f715a-559">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="f715a-560">Değer 65.535'ten büyük veya eşit ve 2^31'den az olmalıdır (2,147,483,648).</span><span class="sxs-lookup"><span data-stu-id="f715a-560">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="f715a-561">Varsayılan değer 96 KB 'dir (98.304).</span><span class="sxs-lookup"><span data-stu-id="f715a-561">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="f715a-562">Zaman Uyumlu G/Ç</span><span class="sxs-lookup"><span data-stu-id="f715a-562">Synchronous I/O</span></span>

<span data-ttu-id="f715a-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>istek ve yanıt için senkron G/Ç'ye izin verilip verilmediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="f715a-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="f715a-564">Varsayılan `true`değer.</span><span class="sxs-lookup"><span data-stu-id="f715a-564">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="f715a-565">Çok sayıda senkron G/Ç işlemini engelleme, iş parçacığı havuzunun açlığa yol açabilir ve bu da uygulamanın yanıt vermemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="f715a-565">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="f715a-566">Yalnızca `AllowSynchronousIO` eşzamanlı G/Ç'yi desteklemeyen bir kitaplık kullanırken etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="f715a-566">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="f715a-567">Aşağıdaki örnek, senkron G/Ç'yi sağlar:</span><span class="sxs-lookup"><span data-stu-id="f715a-567">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="f715a-568">Diğer Kerkenez seçenekleri ve sınırları hakkında bilgi için bkz:</span><span class="sxs-lookup"><span data-stu-id="f715a-568">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="f715a-569">Bitiş noktası yapılandırması</span><span class="sxs-lookup"><span data-stu-id="f715a-569">Endpoint configuration</span></span>

<span data-ttu-id="f715a-570">Varsayılan olarak, ASP.NET Core bağlanır:</span><span class="sxs-lookup"><span data-stu-id="f715a-570">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="f715a-571">`https://localhost:5001`(yerel bir geliştirme sertifikası varsa)</span><span class="sxs-lookup"><span data-stu-id="f715a-571">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="f715a-572">Aşağıdakileri kullanarak URL'leri belirtin:</span><span class="sxs-lookup"><span data-stu-id="f715a-572">Specify URLs using the:</span></span>

* <span data-ttu-id="f715a-573">`ASPNETCORE_URLS`çevre değişkenidir.</span><span class="sxs-lookup"><span data-stu-id="f715a-573">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="f715a-574">`--urls`komut satırı bağımsız değişkeni.</span><span class="sxs-lookup"><span data-stu-id="f715a-574">`--urls` command-line argument.</span></span>
* <span data-ttu-id="f715a-575">`urls`ana bilgisayar yapılandırma anahtarı.</span><span class="sxs-lookup"><span data-stu-id="f715a-575">`urls` host configuration key.</span></span>
* <span data-ttu-id="f715a-576">`UseUrls`uzatma yöntemi.</span><span class="sxs-lookup"><span data-stu-id="f715a-576">`UseUrls` extension method.</span></span>

<span data-ttu-id="f715a-577">Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS).</span><span class="sxs-lookup"><span data-stu-id="f715a-577">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="f715a-578">Değeri yarı sütunlu ayrılmış bir liste olarak `"Urls": "http://localhost:8000;http://localhost:8001"`yapılandırın (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="f715a-578">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="f715a-579">Bu yaklaşımlar hakkında daha fazla bilgi için [Sunucu URL'leri](xref:fundamentals/host/web-host#server-urls) ve [Geçersiz Kılma yapılandırması'na](xref:fundamentals/host/web-host#override-configuration)bakın.</span><span class="sxs-lookup"><span data-stu-id="f715a-579">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="f715a-580">Geliştirme sertifikası oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="f715a-580">A development certificate is created:</span></span>

* <span data-ttu-id="f715a-581">[.NET Core SDK](/dotnet/core/sdk) yüklendiğinde.</span><span class="sxs-lookup"><span data-stu-id="f715a-581">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="f715a-582">[Dev-certs aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-582">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="f715a-583">Bazı tarayıcılar, yerel geliştirme sertifikasına güvenmek için açık izin verilmesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f715a-583">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="f715a-584">Proje şablonları uygulamaları varsayılan olarak HTTPS'de çalışacak şekilde yapılandırır ve [HTTPS yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.</span><span class="sxs-lookup"><span data-stu-id="f715a-584">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="f715a-585">Kestrel <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> için URL önekleri ve bağlantı noktalarını yapılandırmak için arama <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> veya <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> yöntemler.</span><span class="sxs-lookup"><span data-stu-id="f715a-585">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="f715a-586">`UseUrls`, `--urls` komut satırı bağımsız `urls` değişkeni, ana `ASPNETCORE_URLS` bilgisayar yapılandırma anahtarı ve ortam değişkeni de çalışır, ancak bu bölümde daha sonra belirtilen sınırlamalar vardır (varsayılan bir sertifika HTTPS bitiş noktası yapılandırması için kullanılabilir olmalıdır).</span><span class="sxs-lookup"><span data-stu-id="f715a-586">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="f715a-587">`KestrelServerOptions`Yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="f715a-587">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="f715a-588">YapılandırmaEndpointDefaults(Eylem\<DinlemeSeçenekleri>)</span><span class="sxs-lookup"><span data-stu-id="f715a-588">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="f715a-589">Belirtilen her bitiş `Action` noktası için çalışacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="f715a-589">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="f715a-590">Birden `ConfigureEndpointDefaults` çok kez `Action`arama, öncekilerin yerine geçen son `Action` belirtilenin yerini alır.</span><span class="sxs-lookup"><span data-stu-id="f715a-590">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="f715a-591">Aramadan <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> **önce** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> arayarak oluşturulan uç noktalarda varsayılanlar uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="f715a-591">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="f715a-592">YapılandırmaHttpsDefaults(Eylem\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="f715a-592">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="f715a-593">Her HTTPS bitiş `Action` noktası için çalışacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="f715a-593">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="f715a-594">Birden `ConfigureHttpsDefaults` çok kez `Action`arama, öncekilerin yerine geçen son `Action` belirtilenin yerini alır.</span><span class="sxs-lookup"><span data-stu-id="f715a-594">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="f715a-595">Aramadan <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> **önce** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> arayarak oluşturulan uç noktalarda varsayılanlar uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="f715a-595">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="f715a-596">Yapılandırma(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="f715a-596">Configure(IConfiguration)</span></span>

<span data-ttu-id="f715a-597">Kestrel'i ayarlamak için bir yapılandırma yükleyicisi oluşturur ve bu <xref:Microsoft.Extensions.Configuration.IConfiguration> da girdi olarak alır.</span><span class="sxs-lookup"><span data-stu-id="f715a-597">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="f715a-598">Yapılandırma Kerkenez için yapılandırma bölümüne kapsamı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-598">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="f715a-599">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="f715a-599">ListenOptions.UseHttps</span></span>

<span data-ttu-id="f715a-600">Kestrel'i HTTPS'yi kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f715a-600">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="f715a-601">`ListenOptions.UseHttps`Uzantı -ları:</span><span class="sxs-lookup"><span data-stu-id="f715a-601">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="f715a-602">`UseHttps`&ndash; Kestrel'i varsayılan sertifikayla HTTPS'yi kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f715a-602">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="f715a-603">Varsayılan sertifika yapılandırılmamışsa özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f715a-603">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="f715a-604">`ListenOptions.UseHttps`Parametre:</span><span class="sxs-lookup"><span data-stu-id="f715a-604">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="f715a-605">`filename`uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yol ve dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-605">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="f715a-606">`password`X.509 sertifika verilerine erişmek için gereken paroladır.</span><span class="sxs-lookup"><span data-stu-id="f715a-606">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="f715a-607">`configureOptions`bir `Action` yapılandırmak `HttpsConnectionAdapterOptions`için .</span><span class="sxs-lookup"><span data-stu-id="f715a-607">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="f715a-608">' `ListenOptions`yi döndürür.</span><span class="sxs-lookup"><span data-stu-id="f715a-608">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="f715a-609">`storeName`sertifikayı yüklemek için sertifika deposudur.</span><span class="sxs-lookup"><span data-stu-id="f715a-609">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="f715a-610">`subject`sertifikanın özne adıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-610">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="f715a-611">`allowInvalid`kendi imzalı sertifikalar gibi geçersiz sertifikaların dikkate alınıp alınmaması gerektiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="f715a-611">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="f715a-612">`location`sertifikayı yüklemek için mağaza konumudur.</span><span class="sxs-lookup"><span data-stu-id="f715a-612">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="f715a-613">`serverCertificate`X.509 sertifikasıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-613">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="f715a-614">Üretimde, HTTPS açıkça yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-614">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="f715a-615">En azından varsayılan sertifika sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-615">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="f715a-616">Sonraki açıklanan desteklenen yapılandırmalar:</span><span class="sxs-lookup"><span data-stu-id="f715a-616">Supported configurations described next:</span></span>

* <span data-ttu-id="f715a-617">Yapılandırma yok</span><span class="sxs-lookup"><span data-stu-id="f715a-617">No configuration</span></span>
* <span data-ttu-id="f715a-618">Yapılandırmadan varsayılan sertifikayı değiştirme</span><span class="sxs-lookup"><span data-stu-id="f715a-618">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="f715a-619">Koddaki varsayılanları değiştirme</span><span class="sxs-lookup"><span data-stu-id="f715a-619">Change the defaults in code</span></span>

<span data-ttu-id="f715a-620">*Yapılandırma yok*</span><span class="sxs-lookup"><span data-stu-id="f715a-620">*No configuration*</span></span>

<span data-ttu-id="f715a-621">Kerkenez üzerinde `http://localhost:5000` dinler `https://localhost:5001` ve (varsayılan bir sertifika varsa).</span><span class="sxs-lookup"><span data-stu-id="f715a-621">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="f715a-622">*Yapılandırmadan varsayılan sertifikayı değiştirme*</span><span class="sxs-lookup"><span data-stu-id="f715a-622">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="f715a-623">`CreateDefaultBuilder`kerkenez yapılandırmasını yüklemek için varsayılan olarak çağırır. `Configure(context.Configuration.GetSection("Kestrel"))`</span><span class="sxs-lookup"><span data-stu-id="f715a-623">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="f715a-624">Kestrel için varsayılan bir HTTPS uygulama ayarları yapılandırma şeması kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-624">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="f715a-625">Diskteki bir dosyadan veya bir sertifika deposundan kullanılacak URL'ler ve kullanılacak sertifikalar da dahil olmak üzere birden çok uç noktayı yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f715a-625">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="f715a-626">Aşağıdaki *appsettings.json* örnekte:</span><span class="sxs-lookup"><span data-stu-id="f715a-626">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="f715a-627">Geçersiz sertifikaların `true` kullanımına izin vermek için **AllowInvalid'i** ayarlayın (örneğin, kendi imzalı sertifikalar).</span><span class="sxs-lookup"><span data-stu-id="f715a-627">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="f715a-628">Sertifika belirtmeyen herhangi bir HTTPS bitiş noktası (Aşağıdaki örnekte**HttpsDefaultCert)** **Varsayılan** **Sertifikalar** > veya geliştirme sertifikası altında tanımlanan sertifikaya geri döner.</span><span class="sxs-lookup"><span data-stu-id="f715a-628">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="f715a-629">Herhangi bir sertifika düğümü için **Yol** ve **Parola** kullanmanın alternatifi, sertifika deposu alanlarını kullanarak sertifikayı belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="f715a-629">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="f715a-630">Örneğin, **Sertifikalar** > **Varsayılan** sertifika olarak belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="f715a-630">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="f715a-631">Şema notları:</span><span class="sxs-lookup"><span data-stu-id="f715a-631">Schema notes:</span></span>

* <span data-ttu-id="f715a-632">Uç nokta adları büyük/küçük harf duyarsızdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-632">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="f715a-633">Örneğin, `HTTPS` ve `Https` geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f715a-633">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="f715a-634">Parametre `Url` her bitiş noktası için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="f715a-634">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="f715a-635">Bu parametrenin biçimi, tek bir değerle sınırlı olması dışında üst düzey `Urls` yapılandırma parametresi ile aynıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-635">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="f715a-636">Bu uç noktalar, bunlara eklemek `Urls` yerine üst düzey yapılandırmada tanımlanan noktaların yerini alır.</span><span class="sxs-lookup"><span data-stu-id="f715a-636">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="f715a-637">Kodda tanımlanan uç `Listen` noktalar, yapılandırma bölümünde tanımlanan uç noktalarla birlikte kümülatiftir.</span><span class="sxs-lookup"><span data-stu-id="f715a-637">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="f715a-638">Bölüm `Certificate` isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-638">The `Certificate` section is optional.</span></span> <span data-ttu-id="f715a-639">`Certificate` Bölüm belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-639">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="f715a-640">Varsayılan yoksa, sunucu bir özel durum atar ve başlatılmaz.</span><span class="sxs-lookup"><span data-stu-id="f715a-640">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="f715a-641">Bu `Certificate` bölüm hem **Yol**&ndash;**Parolası** hem de **Konu**&ndash;**Deposu** sertifikalarını destekler.</span><span class="sxs-lookup"><span data-stu-id="f715a-641">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="f715a-642">Bağlantı noktası çakışmalarına neden olmadıkları sürece, herhangi bir uç nokta sayısı bu şekilde tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-642">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="f715a-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))`yapılandırılmış `KestrelConfigurationLoader` bir `.Endpoint(string name, listenOptions => { })` uç nokta nın ayarlarını tamamlamak için kullanılabilecek bir yöntemle döndürür:</span><span class="sxs-lookup"><span data-stu-id="f715a-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="f715a-644">`KestrelServerOptions.ConfigurationLoader`tarafından sağlanan gibi varolan yükleyici üzerinde yinelemeye devam etmek için <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>doğrudan erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-644">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="f715a-645">Her bitiş noktası için yapılandırma bölümü, özel `Endpoint` ayarların okunabilmesi için yöntemdeki seçeneklerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-645">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="f715a-646">Başka bir bölümle yeniden `options.Configure(context.Configuration.GetSection("{SECTION}"))` arayarak birden çok yapılandırma yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-646">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="f715a-647">Önceki örneklerde açıkça çağrılmadığı sürece `Load` yalnızca son yapılandırma kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-647">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="f715a-648">Metapackage, varsayılan yapılandırma `Load` bölümünün değiştirilebilecek şekilde çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="f715a-648">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="f715a-649">`KestrelConfigurationLoader`api `Listen` ailesi aşırı yükler `KestrelServerOptions` `Endpoint` olarak aynalar, bu nedenle kod ve config uç noktaları aynı yerde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-649">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="f715a-650">Bu aşırı yüklemeler adlar kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları tüketir.</span><span class="sxs-lookup"><span data-stu-id="f715a-650">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="f715a-651">*Koddaki varsayılanları değiştirme*</span><span class="sxs-lookup"><span data-stu-id="f715a-651">*Change the defaults in code*</span></span>

<span data-ttu-id="f715a-652">`ConfigureEndpointDefaults`ve `ConfigureHttpsDefaults` önceki senaryoda belirtilen `ListenOptions` varsayılan `HttpsConnectionAdapterOptions`sertifikayı geçersiz kılmak da dahil olmak üzere varsayılan ayarları değiştirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-652">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="f715a-653">`ConfigureEndpointDefaults`ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-653">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="f715a-654">*SNI için Kerkenez desteği*</span><span class="sxs-lookup"><span data-stu-id="f715a-654">*Kestrel support for SNI*</span></span>

<span data-ttu-id="f715a-655">[Sunucu Adı Göstergesi (SNI),](https://tools.ietf.org/html/rfc6066#section-3) aynı IP adresi ve bağlantı noktasında birden çok etki alanı barındırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-655">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="f715a-656">SNI'nin çalışması için istemci, sunucunun doğru sertifikayı sağlayabilmesi için TLS el sıkışması sırasında güvenli oturumun ana bilgisayar adını sunucuya gönderir.</span><span class="sxs-lookup"><span data-stu-id="f715a-656">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="f715a-657">İstemci, TLS el sıkışmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için verilen sertifikayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="f715a-657">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="f715a-658">Kerkenez `ServerCertificateSelector` geri arama yoluyla SNI destekler.</span><span class="sxs-lookup"><span data-stu-id="f715a-658">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="f715a-659">Geri arama, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesine izin vermek için bağlantı başına bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-659">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="f715a-660">SNI desteği şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="f715a-660">SNI support requires:</span></span>

* <span data-ttu-id="f715a-661">Hedef çerçevede `netcoreapp2.1` veya daha sonra çalışma.</span><span class="sxs-lookup"><span data-stu-id="f715a-661">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="f715a-662">On `net461` veya daha sonra, geri arama `name` çağrılır ama her zaman `null`.</span><span class="sxs-lookup"><span data-stu-id="f715a-662">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="f715a-663">`name` İstemci `null` TLS el sıkışmasında ana bilgisayar adı parametresini sağlamazsa da öyledir.</span><span class="sxs-lookup"><span data-stu-id="f715a-663">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="f715a-664">Tüm web siteleri aynı Kerkenez örneğinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="f715a-664">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="f715a-665">Kestrel ters bir proxy olmadan birden çok örnek arasında bir IP adresi ve bağlantı noktası paylaşımı desteklemez.</span><span class="sxs-lookup"><span data-stu-id="f715a-665">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="f715a-666">Bağlantı günlüğü</span><span class="sxs-lookup"><span data-stu-id="f715a-666">Connection logging</span></span>

<span data-ttu-id="f715a-667">Bağlantıdaki bayt düzeyinde iletişim için Hata Ayıklama düzeyi günlüklerini yaramak için arayın. <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*></span><span class="sxs-lookup"><span data-stu-id="f715a-667">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="f715a-668">Bağlantı günlüğü, TLS şifreleme sırasında ve yakınlık ların arkasında olduğu gibi düşük düzeyli iletişimdeki sorun giderme sorunları için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-668">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="f715a-669">Önce `UseConnectionLogging` `UseHttps`yerleştirilirse, şifreli trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-669">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="f715a-670">Sonra `UseConnectionLogging` `UseHttps`yerleştirilirse, şifresi çözülmüş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-670">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="f715a-671">TCP soketine bağlama</span><span class="sxs-lookup"><span data-stu-id="f715a-671">Bind to a TCP socket</span></span>

<span data-ttu-id="f715a-672">Yöntem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> bir TCP soketine bağlanır ve lambda seçeneği X.509 sertifika yapılandırmasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="f715a-672">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="f715a-673">Örnek, HTTPS'yi <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>bir bitiş noktası için .</span><span class="sxs-lookup"><span data-stu-id="f715a-673">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="f715a-674">Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak için aynı API'yi kullanın.</span><span class="sxs-lookup"><span data-stu-id="f715a-674">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="f715a-675">Unix soketine bağlanma</span><span class="sxs-lookup"><span data-stu-id="f715a-675">Bind to a Unix socket</span></span>

<span data-ttu-id="f715a-676">Bu örnekte gösterildiği <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> gibi, Nginx ile geliştirilmiş performans için bir Unix soketinde dinleyin:</span><span class="sxs-lookup"><span data-stu-id="f715a-676">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="f715a-677">Nginx sırdaşlık dosyasında, `server`  >  `location`  >  `proxy_pass` girişini `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="f715a-677">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="f715a-678">`{KESTREL SOCKET}`verilen soketin adıdır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (örneğin, `kestrel-test.sock` önceki örnekte).</span><span class="sxs-lookup"><span data-stu-id="f715a-678">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="f715a-679">Soketin Nginx tarafından yazılabilir olduğundan `chmod go+w /tmp/kestrel-test.sock`emin olun (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="f715a-679">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="f715a-680">Bağlantı Noktası 0</span><span class="sxs-lookup"><span data-stu-id="f715a-680">Port 0</span></span>

<span data-ttu-id="f715a-681">Bağlantı noktası `0` numarası belirtildiğinde, Kerkenez dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır.</span><span class="sxs-lookup"><span data-stu-id="f715a-681">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="f715a-682">Aşağıdaki örnek, Kestrel'in çalışma zamanında gerçekte hangi bağlantı noktasına bağlı olduğunu nasıl belirleyeceklerini gösterir:</span><span class="sxs-lookup"><span data-stu-id="f715a-682">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="f715a-683">Uygulama çalıştırıldığında, konsol penceresi çıkışı uygulamaya ulaşılabilen dinamik bağlantı noktasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="f715a-683">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="f715a-684">Sınırlamalar</span><span class="sxs-lookup"><span data-stu-id="f715a-684">Limitations</span></span>

<span data-ttu-id="f715a-685">Uç noktaları aşağıdaki yaklaşımlarla yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="f715a-685">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="f715a-686">`--urls`komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="f715a-686">`--urls` command-line argument</span></span>
* <span data-ttu-id="f715a-687">`urls`ana bilgisayar yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="f715a-687">`urls` host configuration key</span></span>
* <span data-ttu-id="f715a-688">`ASPNETCORE_URLS`çevre değişkeni</span><span class="sxs-lookup"><span data-stu-id="f715a-688">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="f715a-689">Bu yöntemler, Kerkenez dışındaki sunucularla kod çalışması yapmak için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-689">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="f715a-690">Ancak, aşağıdaki sınırlamalar dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="f715a-690">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="f715a-691">HTTPS bitiş noktası yapılandırmasında varsayılan bir sertifika sağlanmadıkça (örneğin, bu konuda `KestrelServerOptions` daha önce gösterildiği gibi yapılandırma veya yapılandırma dosyasını kullanmak gibi) HTTPS bu yaklaşımlarla kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="f715a-691">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="f715a-692">Hem yaklaşımlar `Listen` `UseUrls` hem de yaklaşımlar aynı `Listen` anda kullanıldığında, `UseUrls` uç noktalar uç noktaları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="f715a-692">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="f715a-693">IIS uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="f715a-693">IIS endpoint configuration</span></span>

<span data-ttu-id="f715a-694">IIS kullanırken, IIS geçersiz kılma bağlamaları için URL `Listen` `UseUrls`bağlamaları ya da .</span><span class="sxs-lookup"><span data-stu-id="f715a-694">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="f715a-695">Daha fazla bilgi için [ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="f715a-695">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="f715a-696">ListenOptions.Protokoller</span><span class="sxs-lookup"><span data-stu-id="f715a-696">ListenOptions.Protocols</span></span>

<span data-ttu-id="f715a-697">Özellik, `Protocols` bağlantı bitiş noktasında`HttpProtocols`veya sunucu için etkinleştirilmiş HTTP protokollerini ( ) kurar.</span><span class="sxs-lookup"><span data-stu-id="f715a-697">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="f715a-698">`HttpProtocols` Enumdan `Protocols` özelliğe bir değer atayın.</span><span class="sxs-lookup"><span data-stu-id="f715a-698">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="f715a-699">`HttpProtocols`enum değeri</span><span class="sxs-lookup"><span data-stu-id="f715a-699">`HttpProtocols` enum value</span></span> | <span data-ttu-id="f715a-700">Bağlantı protokolüne izin verilir</span><span class="sxs-lookup"><span data-stu-id="f715a-700">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="f715a-701">Sadece HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="f715a-701">HTTP/1.1 only.</span></span> <span data-ttu-id="f715a-702">TLS ile veya TLS olmadan kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-702">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="f715a-703">Sadece HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="f715a-703">HTTP/2 only.</span></span> <span data-ttu-id="f715a-704">TLS olmadan kullanılabilir, ancak istemci [Bir Ön Bilgi modunu](https://tools.ietf.org/html/rfc7540#section-3.4)destekliyorsa.</span><span class="sxs-lookup"><span data-stu-id="f715a-704">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="f715a-705">HTTP/1.1 ve HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="f715a-705">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="f715a-706">HTTP/2 tls ve [Uygulama Katmanı Protokolü Anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı gerektirir; aksi takdirde, bağlantı varsayılan OLARAK HTTP/1.1'e olur.</span><span class="sxs-lookup"><span data-stu-id="f715a-706">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="f715a-707">Varsayılan protokol HTTP/1.1'dir.</span><span class="sxs-lookup"><span data-stu-id="f715a-707">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="f715a-708">HTTP/2 için TLS kısıtlamaları:</span><span class="sxs-lookup"><span data-stu-id="f715a-708">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="f715a-709">TLS sürüm 1.2 veya sonrası</span><span class="sxs-lookup"><span data-stu-id="f715a-709">TLS version 1.2 or later</span></span>
* <span data-ttu-id="f715a-710">Yeniden anlaşma devre dışı</span><span class="sxs-lookup"><span data-stu-id="f715a-710">Renegotiation disabled</span></span>
* <span data-ttu-id="f715a-711">Sıkıştırma devre dışı</span><span class="sxs-lookup"><span data-stu-id="f715a-711">Compression disabled</span></span>
* <span data-ttu-id="f715a-712">Minimum geçici anahtar değişim boyutları:</span><span class="sxs-lookup"><span data-stu-id="f715a-712">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="f715a-713">Eliptik eğri Diffie-Hellman &lbrack;(ECDHE) [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; &ndash; 224 bit minimum</span><span class="sxs-lookup"><span data-stu-id="f715a-713">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="f715a-714">Sonlu alan Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; &ndash; 2048 bit minimum</span><span class="sxs-lookup"><span data-stu-id="f715a-714">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="f715a-715">Şifreli paket kara listeye alınmadı</span><span class="sxs-lookup"><span data-stu-id="f715a-715">Cipher suite not blacklisted</span></span>

<span data-ttu-id="f715a-716">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack; &lbrack; `FIPS186` &rbrack; P-256 eliptik eğrisi ile varsayılan olarak desteklenir. `TLS-ECDHE` &rbrack;</span><span class="sxs-lookup"><span data-stu-id="f715a-716">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="f715a-717">Aşağıdaki örnek, 8000 no'l'daki porttaki HTTP/1.1 ve HTTP/2 bağlantılarına izin verir.</span><span class="sxs-lookup"><span data-stu-id="f715a-717">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="f715a-718">Bağlantılar, sağlanan bir sertifika ile TLS tarafından güvence altına alınır:</span><span class="sxs-lookup"><span data-stu-id="f715a-718">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="f715a-719">İsteğe bağlı `IConnectionAdapter` olarak, TLS el sıkışmalarını belirli şifreler için bağlantı başına filtrelemek için bir uygulama oluşturun:</span><span class="sxs-lookup"><span data-stu-id="f715a-719">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="f715a-720">*Protokolü yapılandırmadan ayarlama*</span><span class="sxs-lookup"><span data-stu-id="f715a-720">*Set the protocol from configuration*</span></span>

<span data-ttu-id="f715a-721"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>kerkenez yapılandırmasını yüklemek için varsayılan olarak çağırır. `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`</span><span class="sxs-lookup"><span data-stu-id="f715a-721"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="f715a-722">Aşağıdaki *appsettings.json* örneğinde, Kestrel'in tüm uç noktaları için varsayılan bağlantı protokolü (HTTP/1.1 ve HTTP/2) oluşturulmuştur:</span><span class="sxs-lookup"><span data-stu-id="f715a-722">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="f715a-723">Aşağıdaki yapılandırma dosyası örneği, belirli bir bitiş noktası için bir bağlantı protokolü kurar:</span><span class="sxs-lookup"><span data-stu-id="f715a-723">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="f715a-724">Kodda belirtilen protokoller yapılandırma tarafından ayarlanan değerleri geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="f715a-724">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="f715a-725">Aktarım yapılandırması</span><span class="sxs-lookup"><span data-stu-id="f715a-725">Transport configuration</span></span>

<span data-ttu-id="f715a-726">Core 2.1ASP.NET piyasaya sürülmesiyle, Kestrel'in varsayılan taşıması artık Libuv'a değil, yönetilen soketlere dayanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f715a-726">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="f715a-727">Bu, çağrı yapan <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> ve aşağıdaki paketlerden herhangi birini oluşturan 2.1'e yükseltilen ASP.NET Core 2.0 uygulamaları için bir kırılma değişikliğidir:</span><span class="sxs-lookup"><span data-stu-id="f715a-727">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="f715a-728">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (doğrudan paket başvurusu)</span><span class="sxs-lookup"><span data-stu-id="f715a-728">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="f715a-729">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="f715a-729">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="f715a-730">Libuv kullanımını gerektiren projeler için:</span><span class="sxs-lookup"><span data-stu-id="f715a-730">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="f715a-731">Uygulamanın proje dosyasına [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) paketine bağımlılık ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f715a-731">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="f715a-732">Çağrı <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="f715a-732">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="f715a-733">URL önekleri</span><span class="sxs-lookup"><span data-stu-id="f715a-733">URL prefixes</span></span>

<span data-ttu-id="f715a-734">URL `UseUrls`önekleri, komut `--urls` `urls` satırı bağımsız değişkeni, ana bilgisayar yapılandırma anahtarı veya `ASPNETCORE_URLS` ortam değişkeni kullanırken aşağıdaki biçimlerin herhangi birinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-734">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="f715a-735">Yalnızca HTTP URL önekleri geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f715a-735">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="f715a-736">Kestrel, URL bağlamalarını kullanarak `UseUrls`yapılandırırken HTTPS'yi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="f715a-736">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="f715a-737">Bağlantı noktası numarası olan IPv4 adresi</span><span class="sxs-lookup"><span data-stu-id="f715a-737">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="f715a-738">`0.0.0.0`tüm IPv4 adreslerine bağlanan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="f715a-738">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="f715a-739">Bağlantı noktası numarası olan IPv6 adresi</span><span class="sxs-lookup"><span data-stu-id="f715a-739">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="f715a-740">`[::]`IPv4'ün `0.0.0.0`IPv6 eşdeğeridir.</span><span class="sxs-lookup"><span data-stu-id="f715a-740">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="f715a-741">Bağlantı noktası numarası olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="f715a-741">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="f715a-742">Ev sahibi `*`adları `+`ve özel değildir.</span><span class="sxs-lookup"><span data-stu-id="f715a-742">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="f715a-743">Geçerli bir IP adresi olarak `localhost` tanınmayan veya tüm IPv4 ve IPv6 IP'lerine bağlanan her şey.</span><span class="sxs-lookup"><span data-stu-id="f715a-743">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="f715a-744">Farklı ana bilgisayar adlarını aynı bağlantı noktasındaki farklı ASP.NET Core uygulamalarına bağlamak için [HTTP.sys](xref:fundamentals/servers/httpsys) veya IIS, Nginx veya Apache gibi ters proxy sunucusu kullanın.</span><span class="sxs-lookup"><span data-stu-id="f715a-744">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="f715a-745">Ters proxy yapılandırmasında barındırma [ana bilgisayar filtreleme](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f715a-745">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="f715a-746">Bağlantı `localhost` noktası numarası veya bağlantı noktası numarası olan loopback IP ile ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="f715a-746">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="f715a-747">Belirtildiğinde, `localhost` Kerkenez hem IPv4 hem de IPv6 geri dönüş arabirimlerine bağlanmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="f715a-747">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="f715a-748">İstenen bağlantı noktası her iki döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılmaz.</span><span class="sxs-lookup"><span data-stu-id="f715a-748">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="f715a-749">Geri dönüş arabirimi başka bir nedenle kullanılamıyorsa (en sık IPv6 desteklenmediği için), Kestrel bir uyarı yı günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="f715a-749">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="f715a-750">Ana bilgisayar filtresi</span><span class="sxs-lookup"><span data-stu-id="f715a-750">Host filtering</span></span>

<span data-ttu-id="f715a-751">Kerkenez gibi öneklere dayalı yapılandırmayı `http://example.com:5000`desteklerken, Kerkenez büyük ölçüde ana bilgisayar adını yok sayar.</span><span class="sxs-lookup"><span data-stu-id="f715a-751">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="f715a-752">Ana `localhost` bilgisayar, geri dönüş adreslerine bağlanmak için kullanılan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="f715a-752">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="f715a-753">Açık bir IP adresi dışındaki tüm ana bilgisayar, tüm genel IP adreslerine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="f715a-753">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="f715a-754">`Host`üstbilgi doğrulanmadı.</span><span class="sxs-lookup"><span data-stu-id="f715a-754">`Host` headers aren't validated.</span></span>

<span data-ttu-id="f715a-755">Geçici çözüm olarak, Ana Bilgisayar Filtreleme Ara Ware'i kullanın.</span><span class="sxs-lookup"><span data-stu-id="f715a-755">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="f715a-756">Ana Bilgisayar Filtreleme Middleware, [Microsoft.AspNetCore.App metapaketinde](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 veya 2.2) bulunan [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="f715a-756">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="f715a-757">Middleware tarafından <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>eklenir , <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>hangi çağırır:</span><span class="sxs-lookup"><span data-stu-id="f715a-757">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="f715a-758">Ana Bilgisayar Filtreleme Middleware varsayılan olarak devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-758">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="f715a-759">Ara yazılımı etkinleştirmek için `AllowedHosts` *appsettings.json*/uygulama ayarlarında bir anahtar*tanımlayın.\< EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="f715a-759">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="f715a-760">Değer, bağlantı noktası numarası olmayan ana bilgisayar adlarının yarı sütunlu sınırlı bir listesidir:</span><span class="sxs-lookup"><span data-stu-id="f715a-760">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="f715a-761">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f715a-761">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="f715a-762">[İlezli Üstbilgi Middleware](xref:host-and-deploy/proxy-load-balancer) de bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçenek vardır.</span><span class="sxs-lookup"><span data-stu-id="f715a-762">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="f715a-763">İletilenmiş Üstbilgi Middleware ve Ana Bilgisayar Filtreleme Middleware farklı senaryolar için benzer işlevsellik var.</span><span class="sxs-lookup"><span data-stu-id="f715a-763">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="f715a-764">Üstbilgi nin ters proxy sunucusu veya `Host` yük dengeleyicisi ilerlerken üstbilgi korunmadığında Iletili Üstbilgi Middleware ile ayarlama `AllowedHosts` uygundur.</span><span class="sxs-lookup"><span data-stu-id="f715a-764">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="f715a-765">Kerkenez genel kullanıma dönük kenar sunucusu olarak kullanıldığında veya `AllowedHosts` `Host` üstbilgi doğrudan iledildiğinde Ana Bilgisayar Filtreleme Middleware ile ayarlama uygundur.</span><span class="sxs-lookup"><span data-stu-id="f715a-765">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="f715a-766">İlliyeli Üstbilgiler Middleware hakkında <xref:host-and-deploy/proxy-load-balancer>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="f715a-766">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="f715a-767">Kerkenez core ASP.NET [için](xref:fundamentals/servers/index)bir çapraz platform web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="f715a-767">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="f715a-768">Kerkenez, ASP.NET Core proje şablonlarında varsayılan olarak bulunan web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="f715a-768">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="f715a-769">Kerkenez aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="f715a-769">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="f715a-770">HTTPS</span><span class="sxs-lookup"><span data-stu-id="f715a-770">HTTPS</span></span>
* <span data-ttu-id="f715a-771">[WebSockets'i](https://github.com/aspnet/websockets) etkinleştirmek için kullanılan opak yükseltme</span><span class="sxs-lookup"><span data-stu-id="f715a-771">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="f715a-772">Nginx arkasında yüksek performans için Unix soketleri</span><span class="sxs-lookup"><span data-stu-id="f715a-772">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="f715a-773">Kerkenez, .NET Core'un desteklediği tüm platformlarda ve sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="f715a-773">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="f715a-774">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f715a-774">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="f715a-775">Ters proxy ile Kerkenez ne zaman kullanılır</span><span class="sxs-lookup"><span data-stu-id="f715a-775">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="f715a-776">Kerkenez kendisi tarafından veya *ters proxy sunucusu*ile kullanılabilir , Internet Bilgi Hizmetleri gibi [(IIS)](https://www.iis.net/), [Nginx](https://nginx.org), veya [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="f715a-776">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="f715a-777">Ters proxy sunucusu ağdan HTTP isteklerini alır ve bunları Kestrel'e ileter.</span><span class="sxs-lookup"><span data-stu-id="f715a-777">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="f715a-778">Kerkenez bir kenar (Internet bakan) web sunucusu olarak kullanılır:</span><span class="sxs-lookup"><span data-stu-id="f715a-778">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kerkenez ters proxy sunucusu olmadan internet ile doğrudan iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="f715a-780">Ters proxy yapılandırmasında kullanılan kerkenez:</span><span class="sxs-lookup"><span data-stu-id="f715a-780">Kestrel used in a reverse proxy configuration:</span></span>

![Kerkenez, IIS, Nginx veya Apache gibi ters proxy sunucusu aracılığıyla Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="f715a-782">Ters proxy sunucusu olan veya olmayan yapılandırma, desteklenen bir barındırma yapılandırmasıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-782">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="f715a-783">Ters proxy sunucusu olmayan bir kenar sunucusu olarak kullanılan kerkenez, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="f715a-783">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="f715a-784">Kestrel bir bağlantı noktasında dinleyecek şekilde yapılandırıldığında, Kestrel isteklerin `Host` üstbilgilerinden bağımsız olarak o bağlantı noktasının tüm trafiğini işler.</span><span class="sxs-lookup"><span data-stu-id="f715a-784">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="f715a-785">Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel'e iletme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f715a-785">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="f715a-786">Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucusu kullanmak iyi bir seçim olabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-786">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="f715a-787">Ters proxy:</span><span class="sxs-lookup"><span data-stu-id="f715a-787">A reverse proxy:</span></span>

* <span data-ttu-id="f715a-788">Barındırdığı uygulamaların açıkta kalan ortak yüzey alanını sınırlayabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-788">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="f715a-789">Ek bir yapılandırma ve savunma katmanı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="f715a-789">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="f715a-790">Mevcut altyapı ile daha iyi entegre olabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-790">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="f715a-791">Yük dengeleme ve güvenli iletişim (HTTPS) yapılandırması basitleştirin.</span><span class="sxs-lookup"><span data-stu-id="f715a-791">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="f715a-792">Yalnızca ters proxy sunucusu bir X.509 sertifikası gerektirir ve bu sunucu apaçık HTTP kullanarak dahili ağdaki uygulamasunucularıyla iletişim kurabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-792">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="f715a-793">Ters proxy yapılandırmasında barındırma [ana bilgisayar filtreleme](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f715a-793">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="f715a-794">ASP.NET Core uygulamalarında Kerkenez nasıl kullanılır?</span><span class="sxs-lookup"><span data-stu-id="f715a-794">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="f715a-795">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) paketi [Microsoft.AspNetCore.App metapaketinde](xref:fundamentals/metapackage-app)yer almaktadır.</span><span class="sxs-lookup"><span data-stu-id="f715a-795">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f715a-796">ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır.</span><span class="sxs-lookup"><span data-stu-id="f715a-796">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="f715a-797">Program.cs *Program.cs*, şablon kodu <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>aramaları <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> , hangi arka planda çağırır.</span><span class="sxs-lookup"><span data-stu-id="f715a-797">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="f715a-798">Aradıktan `CreateDefaultBuilder`sonra ek yapılandırma <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>sağlamak için , arayın:</span><span class="sxs-lookup"><span data-stu-id="f715a-798">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="f715a-799">Ana bilgisayar `CreateDefaultBuilder` hakkında daha fazla bilgi ve bina için <xref:fundamentals/host/web-host#set-up-a-host>ana bilgisayar bölümünü *ayarla'ya* bakın.</span><span class="sxs-lookup"><span data-stu-id="f715a-799">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="f715a-800">Kerkenez seçenekleri</span><span class="sxs-lookup"><span data-stu-id="f715a-800">Kestrel options</span></span>

<span data-ttu-id="f715a-801">Kestrel web sunucusu, özellikle Internet'e dönük dağıtımlarda kullanışlı olan kısıtlama yapılandırma seçeneklerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="f715a-801">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="f715a-802"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> Sınıfın <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> özelliği üzerindeki kısıtlamaları ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="f715a-802">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="f715a-803">Özellik `Limits` sınıfın bir örneğini <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> tutar.</span><span class="sxs-lookup"><span data-stu-id="f715a-803">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="f715a-804">Aşağıdaki <xref:Microsoft.AspNetCore.Server.Kestrel.Core> örneklerde ad alanı kullanılır:</span><span class="sxs-lookup"><span data-stu-id="f715a-804">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="f715a-805">Aşağıdaki örneklerde C# koduyla yapılandırılan kerkenez seçenekleri de bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-805">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="f715a-806">Örneğin, Dosya Yapılandırma Sağlayıcısı kestrel yapılandırmasını bir *appsettings.json* veya *appsettings'ten yükleyebilir.{ Çevre}.json* dosyası:</span><span class="sxs-lookup"><span data-stu-id="f715a-806">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="f715a-807">Aşağıdaki **yaklaşımlardan birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="f715a-807">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="f715a-808">Kerkenezi `Startup.ConfigureServices`yapılandır:</span><span class="sxs-lookup"><span data-stu-id="f715a-808">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="f715a-809">Sınıfa bir `IConfiguration` örnek `Startup` enjekte edin.</span><span class="sxs-lookup"><span data-stu-id="f715a-809">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="f715a-810">Aşağıdaki örnek, enjekte edilen yapılandırmanın `Configuration` özelliğe atandığını varsayar.</span><span class="sxs-lookup"><span data-stu-id="f715a-810">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="f715a-811">In `Startup.ConfigureServices`, `Kestrel` Kerkenez yapılandırma içine yapılandırma bölümü yükleyin:</span><span class="sxs-lookup"><span data-stu-id="f715a-811">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="f715a-812">Ev sahibini kurarken Kerkenezi yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="f715a-812">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="f715a-813">*Program.cs,* yapılandırma `Kestrel` bölümünü Kestrel'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="f715a-813">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="f715a-814">Önceki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.</span><span class="sxs-lookup"><span data-stu-id="f715a-814">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="f715a-815">Zaman ayarı tutma</span><span class="sxs-lookup"><span data-stu-id="f715a-815">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="f715a-816">Canlı kalma [zaman ını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f715a-816">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="f715a-817">Varsayılan olarak 2 dakika dır.</span><span class="sxs-lookup"><span data-stu-id="f715a-817">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="f715a-818">Maksimum istemci bağlantıları</span><span class="sxs-lookup"><span data-stu-id="f715a-818">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="f715a-819">Aşağıdaki kodla tüm uygulama için eşzamanlı açık TCP bağlantısı sayısı ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="f715a-819">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="f715a-820">HTTP veya HTTPS'den başka bir protokole yükseltilmiş bağlantılar için ayrı bir sınır vardır (örneğin, bir WebSockets isteğinde).</span><span class="sxs-lookup"><span data-stu-id="f715a-820">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="f715a-821">Bir bağlantı yükseltildikten sonra, sınıra `MaxConcurrentConnections` göre sayılmaz.</span><span class="sxs-lookup"><span data-stu-id="f715a-821">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="f715a-822">Maksimum bağlantı sayısı varsayılan olarak sınırsızdır (null).</span><span class="sxs-lookup"><span data-stu-id="f715a-822">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="f715a-823">Maksimum istek gövde boyutu</span><span class="sxs-lookup"><span data-stu-id="f715a-823">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="f715a-824">Varsayılan maksimum istek gövde boyutu yaklaşık 28,6 MB olan 30.000.000 bayttır.</span><span class="sxs-lookup"><span data-stu-id="f715a-824">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="f715a-825">Bir ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> önerilen yaklaşım, özniteliği bir eylem yönteminde kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="f715a-825">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="f715a-826">Aşağıda, her istekte uygulamanın kısıtlamasının nasıl yapılandırılabildiğini gösteren bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="f715a-826">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="f715a-827">Ara yazılımda belirli bir istek üzerindeki ayarı geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="f715a-827">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="f715a-828">Uygulama isteği okumaya başladıktan sonra uygulama istek sınırını yapılandırırsa bir özel durum atılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-828">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="f715a-829">Özelliğin `MaxRequestBodySize` salt `IsReadOnly` okunur durumda olup olmadığını gösteren bir özellik vardır, bu da sınırı yapılandırmak için çok geç olduğu anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="f715a-829">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="f715a-830">Bir uygulama [ASP.NET Çekirdek Modülü'nün](xref:host-and-deploy/aspnet-core-module)arkasında [işlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model) kaldığında, IIS sınırı zaten belirlediği için Kestrel'in istek gövde boyutu sınırı devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-830">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="f715a-831">Minimum istek gövde veri oranı</span><span class="sxs-lookup"><span data-stu-id="f715a-831">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="f715a-832">Kerkenez, verilerin bayt/saniye cinsinden belirtilen oranda gelişip ulaşmadığında her saniye kontrol eder.</span><span class="sxs-lookup"><span data-stu-id="f715a-832">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="f715a-833">Oran minimumun altına düşerse, bağlantı zamanlanır. Yetkisiz kullanım süresi, Kestrel'in müşteriye gönderim oranını en aza kadar yükseltmesi için verdiği süredir; bu süre içinde fiyat kontrol edilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-833">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="f715a-834">Yetkisiz kullanım süresi, TCP'nin yavaş başlaması nedeniyle başlangıçta veri gönderen bağlantıların düşmesini önlemeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="f715a-834">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="f715a-835">Varsayılan minimum oran, 5 saniyelik yetkisiz kullanım süresine sahip 240 bayt/saniyedir.</span><span class="sxs-lookup"><span data-stu-id="f715a-835">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="f715a-836">Yanıt için de minimum ücret uygulanır.</span><span class="sxs-lookup"><span data-stu-id="f715a-836">A minimum rate also applies to the response.</span></span> <span data-ttu-id="f715a-837">İstek sınırını ve yanıt sınırını ayarlamak için kod, `RequestBody` özellik `Response` ve arabirim adlarına sahip olmak veya bunlar dışında aynıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-837">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="f715a-838">Aşağıda, *Program.cs*minimum veri oranlarının nasıl yapılandırılabildiğini gösteren bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="f715a-838">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="f715a-839">Üstbilgi zaman alabı isteği</span><span class="sxs-lookup"><span data-stu-id="f715a-839">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="f715a-840">Sunucunun istek üstbilgilerini almak için harcadığı maksimum süreyi alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="f715a-840">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="f715a-841">Varsayılan olarak 30 saniye.</span><span class="sxs-lookup"><span data-stu-id="f715a-841">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="f715a-842">Zaman Uyumlu G/Ç</span><span class="sxs-lookup"><span data-stu-id="f715a-842">Synchronous I/O</span></span>

<span data-ttu-id="f715a-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>istek ve yanıt için senkron G/Ç'ye izin verilip verilmediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="f715a-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="f715a-844">Varsayılan `true`değer.</span><span class="sxs-lookup"><span data-stu-id="f715a-844">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="f715a-845">Çok sayıda senkron G/Ç işlemini engelleme, iş parçacığı havuzunun açlığa yol açabilir ve bu da uygulamanın yanıt vermemesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="f715a-845">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="f715a-846">Yalnızca `AllowSynchronousIO` eşzamanlı G/Ç'yi desteklemeyen bir kitaplık kullanırken etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="f715a-846">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="f715a-847">Aşağıdaki örnek senkron G/Ç'yi devre dışı kılabilir:</span><span class="sxs-lookup"><span data-stu-id="f715a-847">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="f715a-848">Diğer Kerkenez seçenekleri ve sınırları hakkında bilgi için bkz:</span><span class="sxs-lookup"><span data-stu-id="f715a-848">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="f715a-849">Bitiş noktası yapılandırması</span><span class="sxs-lookup"><span data-stu-id="f715a-849">Endpoint configuration</span></span>

<span data-ttu-id="f715a-850">Varsayılan olarak, ASP.NET Core bağlanır:</span><span class="sxs-lookup"><span data-stu-id="f715a-850">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="f715a-851">`https://localhost:5001`(yerel bir geliştirme sertifikası varsa)</span><span class="sxs-lookup"><span data-stu-id="f715a-851">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="f715a-852">Aşağıdakileri kullanarak URL'leri belirtin:</span><span class="sxs-lookup"><span data-stu-id="f715a-852">Specify URLs using the:</span></span>

* <span data-ttu-id="f715a-853">`ASPNETCORE_URLS`çevre değişkenidir.</span><span class="sxs-lookup"><span data-stu-id="f715a-853">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="f715a-854">`--urls`komut satırı bağımsız değişkeni.</span><span class="sxs-lookup"><span data-stu-id="f715a-854">`--urls` command-line argument.</span></span>
* <span data-ttu-id="f715a-855">`urls`ana bilgisayar yapılandırma anahtarı.</span><span class="sxs-lookup"><span data-stu-id="f715a-855">`urls` host configuration key.</span></span>
* <span data-ttu-id="f715a-856">`UseUrls`uzatma yöntemi.</span><span class="sxs-lookup"><span data-stu-id="f715a-856">`UseUrls` extension method.</span></span>

<span data-ttu-id="f715a-857">Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS).</span><span class="sxs-lookup"><span data-stu-id="f715a-857">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="f715a-858">Değeri yarı sütunlu ayrılmış bir liste olarak `"Urls": "http://localhost:8000;http://localhost:8001"`yapılandırın (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="f715a-858">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="f715a-859">Bu yaklaşımlar hakkında daha fazla bilgi için [Sunucu URL'leri](xref:fundamentals/host/web-host#server-urls) ve [Geçersiz Kılma yapılandırması'na](xref:fundamentals/host/web-host#override-configuration)bakın.</span><span class="sxs-lookup"><span data-stu-id="f715a-859">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="f715a-860">Geliştirme sertifikası oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="f715a-860">A development certificate is created:</span></span>

* <span data-ttu-id="f715a-861">[.NET Core SDK](/dotnet/core/sdk) yüklendiğinde.</span><span class="sxs-lookup"><span data-stu-id="f715a-861">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="f715a-862">[Dev-certs aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-862">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="f715a-863">Bazı tarayıcılar, yerel geliştirme sertifikasına güvenmek için açık izin verilmesini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f715a-863">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="f715a-864">Proje şablonları uygulamaları varsayılan olarak HTTPS'de çalışacak şekilde yapılandırır ve [HTTPS yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.</span><span class="sxs-lookup"><span data-stu-id="f715a-864">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="f715a-865">Kestrel <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> için URL önekleri ve bağlantı noktalarını yapılandırmak için arama <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> veya <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> yöntemler.</span><span class="sxs-lookup"><span data-stu-id="f715a-865">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="f715a-866">`UseUrls`, `--urls` komut satırı bağımsız `urls` değişkeni, ana `ASPNETCORE_URLS` bilgisayar yapılandırma anahtarı ve ortam değişkeni de çalışır, ancak bu bölümde daha sonra belirtilen sınırlamalar vardır (varsayılan bir sertifika HTTPS bitiş noktası yapılandırması için kullanılabilir olmalıdır).</span><span class="sxs-lookup"><span data-stu-id="f715a-866">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="f715a-867">`KestrelServerOptions`Yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="f715a-867">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="f715a-868">YapılandırmaEndpointDefaults(Eylem\<DinlemeSeçenekleri>)</span><span class="sxs-lookup"><span data-stu-id="f715a-868">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="f715a-869">Belirtilen her bitiş `Action` noktası için çalışacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="f715a-869">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="f715a-870">Birden `ConfigureEndpointDefaults` çok kez `Action`arama, öncekilerin yerine geçen son `Action` belirtilenin yerini alır.</span><span class="sxs-lookup"><span data-stu-id="f715a-870">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="f715a-871">Aramadan <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> **önce** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> arayarak oluşturulan uç noktalarda varsayılanlar uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="f715a-871">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="f715a-872">YapılandırmaHttpsDefaults(Eylem\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="f715a-872">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="f715a-873">Her HTTPS bitiş `Action` noktası için çalışacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="f715a-873">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="f715a-874">Birden `ConfigureHttpsDefaults` çok kez `Action`arama, öncekilerin yerine geçen son `Action` belirtilenin yerini alır.</span><span class="sxs-lookup"><span data-stu-id="f715a-874">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="f715a-875">Aramadan <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> **önce** <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> arayarak oluşturulan uç noktalarda varsayılanlar uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="f715a-875">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="f715a-876">Yapılandırma(IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="f715a-876">Configure(IConfiguration)</span></span>

<span data-ttu-id="f715a-877">Kestrel'i ayarlamak için bir yapılandırma yükleyicisi oluşturur ve bu <xref:Microsoft.Extensions.Configuration.IConfiguration> da girdi olarak alır.</span><span class="sxs-lookup"><span data-stu-id="f715a-877">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="f715a-878">Yapılandırma Kerkenez için yapılandırma bölümüne kapsamı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-878">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="f715a-879">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="f715a-879">ListenOptions.UseHttps</span></span>

<span data-ttu-id="f715a-880">Kestrel'i HTTPS'yi kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f715a-880">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="f715a-881">`ListenOptions.UseHttps`Uzantı -ları:</span><span class="sxs-lookup"><span data-stu-id="f715a-881">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="f715a-882">`UseHttps`&ndash; Kestrel'i varsayılan sertifikayla HTTPS'yi kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f715a-882">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="f715a-883">Varsayılan sertifika yapılandırılmamışsa özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="f715a-883">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="f715a-884">`ListenOptions.UseHttps`Parametre:</span><span class="sxs-lookup"><span data-stu-id="f715a-884">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="f715a-885">`filename`uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yol ve dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-885">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="f715a-886">`password`X.509 sertifika verilerine erişmek için gereken paroladır.</span><span class="sxs-lookup"><span data-stu-id="f715a-886">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="f715a-887">`configureOptions`bir `Action` yapılandırmak `HttpsConnectionAdapterOptions`için .</span><span class="sxs-lookup"><span data-stu-id="f715a-887">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="f715a-888">' `ListenOptions`yi döndürür.</span><span class="sxs-lookup"><span data-stu-id="f715a-888">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="f715a-889">`storeName`sertifikayı yüklemek için sertifika deposudur.</span><span class="sxs-lookup"><span data-stu-id="f715a-889">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="f715a-890">`subject`sertifikanın özne adıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-890">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="f715a-891">`allowInvalid`kendi imzalı sertifikalar gibi geçersiz sertifikaların dikkate alınıp alınmaması gerektiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="f715a-891">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="f715a-892">`location`sertifikayı yüklemek için mağaza konumudur.</span><span class="sxs-lookup"><span data-stu-id="f715a-892">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="f715a-893">`serverCertificate`X.509 sertifikasıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-893">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="f715a-894">Üretimde, HTTPS açıkça yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-894">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="f715a-895">En azından varsayılan sertifika sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-895">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="f715a-896">Sonraki açıklanan desteklenen yapılandırmalar:</span><span class="sxs-lookup"><span data-stu-id="f715a-896">Supported configurations described next:</span></span>

* <span data-ttu-id="f715a-897">Yapılandırma yok</span><span class="sxs-lookup"><span data-stu-id="f715a-897">No configuration</span></span>
* <span data-ttu-id="f715a-898">Yapılandırmadan varsayılan sertifikayı değiştirme</span><span class="sxs-lookup"><span data-stu-id="f715a-898">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="f715a-899">Koddaki varsayılanları değiştirme</span><span class="sxs-lookup"><span data-stu-id="f715a-899">Change the defaults in code</span></span>

<span data-ttu-id="f715a-900">*Yapılandırma yok*</span><span class="sxs-lookup"><span data-stu-id="f715a-900">*No configuration*</span></span>

<span data-ttu-id="f715a-901">Kerkenez üzerinde `http://localhost:5000` dinler `https://localhost:5001` ve (varsayılan bir sertifika varsa).</span><span class="sxs-lookup"><span data-stu-id="f715a-901">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="f715a-902">*Yapılandırmadan varsayılan sertifikayı değiştirme*</span><span class="sxs-lookup"><span data-stu-id="f715a-902">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="f715a-903">`CreateDefaultBuilder`kerkenez yapılandırmasını yüklemek için varsayılan olarak çağırır. `Configure(context.Configuration.GetSection("Kestrel"))`</span><span class="sxs-lookup"><span data-stu-id="f715a-903">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="f715a-904">Kestrel için varsayılan bir HTTPS uygulama ayarları yapılandırma şeması kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-904">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="f715a-905">Diskteki bir dosyadan veya bir sertifika deposundan kullanılacak URL'ler ve kullanılacak sertifikalar da dahil olmak üzere birden çok uç noktayı yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="f715a-905">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="f715a-906">Aşağıdaki *appsettings.json* örnekte:</span><span class="sxs-lookup"><span data-stu-id="f715a-906">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="f715a-907">Geçersiz sertifikaların `true` kullanımına izin vermek için **AllowInvalid'i** ayarlayın (örneğin, kendi imzalı sertifikalar).</span><span class="sxs-lookup"><span data-stu-id="f715a-907">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="f715a-908">Sertifika belirtmeyen herhangi bir HTTPS bitiş noktası (Aşağıdaki örnekte**HttpsDefaultCert)** **Varsayılan** **Sertifikalar** > veya geliştirme sertifikası altında tanımlanan sertifikaya geri döner.</span><span class="sxs-lookup"><span data-stu-id="f715a-908">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="f715a-909">Herhangi bir sertifika düğümü için **Yol** ve **Parola** kullanmanın alternatifi, sertifika deposu alanlarını kullanarak sertifikayı belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="f715a-909">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="f715a-910">Örneğin, **Sertifikalar** > **Varsayılan** sertifika olarak belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="f715a-910">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="f715a-911">Şema notları:</span><span class="sxs-lookup"><span data-stu-id="f715a-911">Schema notes:</span></span>

* <span data-ttu-id="f715a-912">Uç nokta adları büyük/küçük harf duyarsızdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-912">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="f715a-913">Örneğin, `HTTPS` ve `Https` geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f715a-913">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="f715a-914">Parametre `Url` her bitiş noktası için gereklidir.</span><span class="sxs-lookup"><span data-stu-id="f715a-914">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="f715a-915">Bu parametrenin biçimi, tek bir değerle sınırlı olması dışında üst düzey `Urls` yapılandırma parametresi ile aynıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-915">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="f715a-916">Bu uç noktalar, bunlara eklemek `Urls` yerine üst düzey yapılandırmada tanımlanan noktaların yerini alır.</span><span class="sxs-lookup"><span data-stu-id="f715a-916">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="f715a-917">Kodda tanımlanan uç `Listen` noktalar, yapılandırma bölümünde tanımlanan uç noktalarla birlikte kümülatiftir.</span><span class="sxs-lookup"><span data-stu-id="f715a-917">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="f715a-918">Bölüm `Certificate` isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-918">The `Certificate` section is optional.</span></span> <span data-ttu-id="f715a-919">`Certificate` Bölüm belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-919">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="f715a-920">Varsayılan yoksa, sunucu bir özel durum atar ve başlatılmaz.</span><span class="sxs-lookup"><span data-stu-id="f715a-920">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="f715a-921">Bu `Certificate` bölüm hem **Yol**&ndash;**Parolası** hem de **Konu**&ndash;**Deposu** sertifikalarını destekler.</span><span class="sxs-lookup"><span data-stu-id="f715a-921">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="f715a-922">Bağlantı noktası çakışmalarına neden olmadıkları sürece, herhangi bir uç nokta sayısı bu şekilde tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-922">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="f715a-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))`yapılandırılmış `KestrelConfigurationLoader` bir `.Endpoint(string name, listenOptions => { })` uç nokta nın ayarlarını tamamlamak için kullanılabilecek bir yöntemle döndürür:</span><span class="sxs-lookup"><span data-stu-id="f715a-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="f715a-924">`KestrelServerOptions.ConfigurationLoader`tarafından sağlanan gibi varolan yükleyici üzerinde yinelemeye devam etmek için <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>doğrudan erişilebilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-924">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="f715a-925">Her bitiş noktası için yapılandırma bölümü, özel `Endpoint` ayarların okunabilmesi için yöntemdeki seçeneklerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-925">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="f715a-926">Başka bir bölümle yeniden `options.Configure(context.Configuration.GetSection("{SECTION}"))` arayarak birden çok yapılandırma yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-926">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="f715a-927">Önceki örneklerde açıkça çağrılmadığı sürece `Load` yalnızca son yapılandırma kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-927">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="f715a-928">Metapackage, varsayılan yapılandırma `Load` bölümünün değiştirilebilecek şekilde çağrılmaz.</span><span class="sxs-lookup"><span data-stu-id="f715a-928">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="f715a-929">`KestrelConfigurationLoader`api `Listen` ailesi aşırı yükler `KestrelServerOptions` `Endpoint` olarak aynalar, bu nedenle kod ve config uç noktaları aynı yerde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-929">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="f715a-930">Bu aşırı yüklemeler adlar kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları tüketir.</span><span class="sxs-lookup"><span data-stu-id="f715a-930">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="f715a-931">*Koddaki varsayılanları değiştirme*</span><span class="sxs-lookup"><span data-stu-id="f715a-931">*Change the defaults in code*</span></span>

<span data-ttu-id="f715a-932">`ConfigureEndpointDefaults`ve `ConfigureHttpsDefaults` önceki senaryoda belirtilen `ListenOptions` varsayılan `HttpsConnectionAdapterOptions`sertifikayı geçersiz kılmak da dahil olmak üzere varsayılan ayarları değiştirmek için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-932">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="f715a-933">`ConfigureEndpointDefaults`ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-933">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="f715a-934">*SNI için Kerkenez desteği*</span><span class="sxs-lookup"><span data-stu-id="f715a-934">*Kestrel support for SNI*</span></span>

<span data-ttu-id="f715a-935">[Sunucu Adı Göstergesi (SNI),](https://tools.ietf.org/html/rfc6066#section-3) aynı IP adresi ve bağlantı noktasında birden çok etki alanı barındırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-935">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="f715a-936">SNI'nin çalışması için istemci, sunucunun doğru sertifikayı sağlayabilmesi için TLS el sıkışması sırasında güvenli oturumun ana bilgisayar adını sunucuya gönderir.</span><span class="sxs-lookup"><span data-stu-id="f715a-936">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="f715a-937">İstemci, TLS el sıkışmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için verilen sertifikayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="f715a-937">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="f715a-938">Kerkenez `ServerCertificateSelector` geri arama yoluyla SNI destekler.</span><span class="sxs-lookup"><span data-stu-id="f715a-938">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="f715a-939">Geri arama, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesine izin vermek için bağlantı başına bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-939">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="f715a-940">SNI desteği şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="f715a-940">SNI support requires:</span></span>

* <span data-ttu-id="f715a-941">Hedef çerçevede `netcoreapp2.1` veya daha sonra çalışma.</span><span class="sxs-lookup"><span data-stu-id="f715a-941">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="f715a-942">On `net461` veya daha sonra, geri arama `name` çağrılır ama her zaman `null`.</span><span class="sxs-lookup"><span data-stu-id="f715a-942">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="f715a-943">`name` İstemci `null` TLS el sıkışmasında ana bilgisayar adı parametresini sağlamazsa da öyledir.</span><span class="sxs-lookup"><span data-stu-id="f715a-943">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="f715a-944">Tüm web siteleri aynı Kerkenez örneğinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="f715a-944">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="f715a-945">Kestrel ters bir proxy olmadan birden çok örnek arasında bir IP adresi ve bağlantı noktası paylaşımı desteklemez.</span><span class="sxs-lookup"><span data-stu-id="f715a-945">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="f715a-946">Bağlantı günlüğü</span><span class="sxs-lookup"><span data-stu-id="f715a-946">Connection logging</span></span>

<span data-ttu-id="f715a-947">Bağlantıdaki bayt düzeyinde iletişim için Hata Ayıklama düzeyi günlüklerini yaramak için arayın. <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*></span><span class="sxs-lookup"><span data-stu-id="f715a-947">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="f715a-948">Bağlantı günlüğü, TLS şifreleme sırasında ve yakınlık ların arkasında olduğu gibi düşük düzeyli iletişimdeki sorun giderme sorunları için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-948">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="f715a-949">Önce `UseConnectionLogging` `UseHttps`yerleştirilirse, şifreli trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-949">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="f715a-950">Sonra `UseConnectionLogging` `UseHttps`yerleştirilirse, şifresi çözülmüş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-950">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="f715a-951">TCP soketine bağlama</span><span class="sxs-lookup"><span data-stu-id="f715a-951">Bind to a TCP socket</span></span>

<span data-ttu-id="f715a-952">Yöntem <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> bir TCP soketine bağlanır ve lambda seçeneği X.509 sertifika yapılandırmasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="f715a-952">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="f715a-953">Örnek, HTTPS'yi <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>bir bitiş noktası için .</span><span class="sxs-lookup"><span data-stu-id="f715a-953">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="f715a-954">Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak için aynı API'yi kullanın.</span><span class="sxs-lookup"><span data-stu-id="f715a-954">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="f715a-955">Unix soketine bağlanma</span><span class="sxs-lookup"><span data-stu-id="f715a-955">Bind to a Unix socket</span></span>

<span data-ttu-id="f715a-956">Bu örnekte gösterildiği <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> gibi, Nginx ile geliştirilmiş performans için bir Unix soketinde dinleyin:</span><span class="sxs-lookup"><span data-stu-id="f715a-956">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

* <span data-ttu-id="f715a-957">Nginx sırdaşlık dosyasında, `server`  >  `location`  >  `proxy_pass` girişini `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="f715a-957">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="f715a-958">`{KESTREL SOCKET}`verilen soketin adıdır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (örneğin, `kestrel-test.sock` önceki örnekte).</span><span class="sxs-lookup"><span data-stu-id="f715a-958">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="f715a-959">Soketin Nginx tarafından yazılabilir olduğundan `chmod go+w /tmp/kestrel-test.sock`emin olun (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="f715a-959">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="f715a-960">Bağlantı Noktası 0</span><span class="sxs-lookup"><span data-stu-id="f715a-960">Port 0</span></span>

<span data-ttu-id="f715a-961">Bağlantı noktası `0` numarası belirtildiğinde, Kerkenez dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır.</span><span class="sxs-lookup"><span data-stu-id="f715a-961">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="f715a-962">Aşağıdaki örnek, Kestrel'in çalışma zamanında gerçekte hangi bağlantı noktasına bağlı olduğunu nasıl belirleyeceklerini gösterir:</span><span class="sxs-lookup"><span data-stu-id="f715a-962">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="f715a-963">Uygulama çalıştırıldığında, konsol penceresi çıkışı uygulamaya ulaşılabilen dinamik bağlantı noktasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="f715a-963">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="f715a-964">Sınırlamalar</span><span class="sxs-lookup"><span data-stu-id="f715a-964">Limitations</span></span>

<span data-ttu-id="f715a-965">Uç noktaları aşağıdaki yaklaşımlarla yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="f715a-965">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="f715a-966">`--urls`komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="f715a-966">`--urls` command-line argument</span></span>
* <span data-ttu-id="f715a-967">`urls`ana bilgisayar yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="f715a-967">`urls` host configuration key</span></span>
* <span data-ttu-id="f715a-968">`ASPNETCORE_URLS`çevre değişkeni</span><span class="sxs-lookup"><span data-stu-id="f715a-968">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="f715a-969">Bu yöntemler, Kerkenez dışındaki sunucularla kod çalışması yapmak için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="f715a-969">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="f715a-970">Ancak, aşağıdaki sınırlamalar dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="f715a-970">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="f715a-971">HTTPS bitiş noktası yapılandırmasında varsayılan bir sertifika sağlanmadıkça (örneğin, bu konuda `KestrelServerOptions` daha önce gösterildiği gibi yapılandırma veya yapılandırma dosyasını kullanmak gibi) HTTPS bu yaklaşımlarla kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="f715a-971">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="f715a-972">Hem yaklaşımlar `Listen` `UseUrls` hem de yaklaşımlar aynı `Listen` anda kullanıldığında, `UseUrls` uç noktalar uç noktaları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="f715a-972">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="f715a-973">IIS uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="f715a-973">IIS endpoint configuration</span></span>

<span data-ttu-id="f715a-974">IIS kullanırken, IIS geçersiz kılma bağlamaları için URL `Listen` `UseUrls`bağlamaları ya da .</span><span class="sxs-lookup"><span data-stu-id="f715a-974">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="f715a-975">Daha fazla bilgi için [ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="f715a-975">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="f715a-976">Aktarım yapılandırması</span><span class="sxs-lookup"><span data-stu-id="f715a-976">Transport configuration</span></span>

<span data-ttu-id="f715a-977">Core 2.1ASP.NET piyasaya sürülmesiyle, Kestrel'in varsayılan taşıması artık Libuv'a değil, yönetilen soketlere dayanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="f715a-977">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="f715a-978">Bu, çağrı yapan <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> ve aşağıdaki paketlerden herhangi birini oluşturan 2.1'e yükseltilen ASP.NET Core 2.0 uygulamaları için bir kırılma değişikliğidir:</span><span class="sxs-lookup"><span data-stu-id="f715a-978">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="f715a-979">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (doğrudan paket başvurusu)</span><span class="sxs-lookup"><span data-stu-id="f715a-979">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="f715a-980">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="f715a-980">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="f715a-981">Libuv kullanımını gerektiren projeler için:</span><span class="sxs-lookup"><span data-stu-id="f715a-981">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="f715a-982">Uygulamanın proje dosyasına [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) paketine bağımlılık ekleyin:</span><span class="sxs-lookup"><span data-stu-id="f715a-982">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="f715a-983">Çağrı <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="f715a-983">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="f715a-984">URL önekleri</span><span class="sxs-lookup"><span data-stu-id="f715a-984">URL prefixes</span></span>

<span data-ttu-id="f715a-985">URL `UseUrls`önekleri, komut `--urls` `urls` satırı bağımsız değişkeni, ana bilgisayar yapılandırma anahtarı veya `ASPNETCORE_URLS` ortam değişkeni kullanırken aşağıdaki biçimlerin herhangi birinde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="f715a-985">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="f715a-986">Yalnızca HTTP URL önekleri geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="f715a-986">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="f715a-987">Kestrel, URL bağlamalarını kullanarak `UseUrls`yapılandırırken HTTPS'yi desteklemez.</span><span class="sxs-lookup"><span data-stu-id="f715a-987">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="f715a-988">Bağlantı noktası numarası olan IPv4 adresi</span><span class="sxs-lookup"><span data-stu-id="f715a-988">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="f715a-989">`0.0.0.0`tüm IPv4 adreslerine bağlanan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="f715a-989">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="f715a-990">Bağlantı noktası numarası olan IPv6 adresi</span><span class="sxs-lookup"><span data-stu-id="f715a-990">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="f715a-991">`[::]`IPv4'ün `0.0.0.0`IPv6 eşdeğeridir.</span><span class="sxs-lookup"><span data-stu-id="f715a-991">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="f715a-992">Bağlantı noktası numarası olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="f715a-992">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="f715a-993">Ev sahibi `*`adları `+`ve özel değildir.</span><span class="sxs-lookup"><span data-stu-id="f715a-993">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="f715a-994">Geçerli bir IP adresi olarak `localhost` tanınmayan veya tüm IPv4 ve IPv6 IP'lerine bağlanan her şey.</span><span class="sxs-lookup"><span data-stu-id="f715a-994">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="f715a-995">Farklı ana bilgisayar adlarını aynı bağlantı noktasındaki farklı ASP.NET Core uygulamalarına bağlamak için [HTTP.sys](xref:fundamentals/servers/httpsys) veya IIS, Nginx veya Apache gibi ters proxy sunucusu kullanın.</span><span class="sxs-lookup"><span data-stu-id="f715a-995">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="f715a-996">Ters proxy yapılandırmasında barındırma [ana bilgisayar filtreleme](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="f715a-996">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="f715a-997">Bağlantı `localhost` noktası numarası veya bağlantı noktası numarası olan loopback IP ile ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="f715a-997">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="f715a-998">Belirtildiğinde, `localhost` Kerkenez hem IPv4 hem de IPv6 geri dönüş arabirimlerine bağlanmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="f715a-998">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="f715a-999">İstenen bağlantı noktası her iki döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılmaz.</span><span class="sxs-lookup"><span data-stu-id="f715a-999">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="f715a-1000">Geri dönüş arabirimi başka bir nedenle kullanılamıyorsa (en sık IPv6 desteklenmediği için), Kestrel bir uyarı yı günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="f715a-1000">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="f715a-1001">Ana bilgisayar filtresi</span><span class="sxs-lookup"><span data-stu-id="f715a-1001">Host filtering</span></span>

<span data-ttu-id="f715a-1002">Kerkenez gibi öneklere dayalı yapılandırmayı `http://example.com:5000`desteklerken, Kerkenez büyük ölçüde ana bilgisayar adını yok sayar.</span><span class="sxs-lookup"><span data-stu-id="f715a-1002">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="f715a-1003">Ana `localhost` bilgisayar, geri dönüş adreslerine bağlanmak için kullanılan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="f715a-1003">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="f715a-1004">Açık bir IP adresi dışındaki tüm ana bilgisayar, tüm genel IP adreslerine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="f715a-1004">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="f715a-1005">`Host`üstbilgi doğrulanmadı.</span><span class="sxs-lookup"><span data-stu-id="f715a-1005">`Host` headers aren't validated.</span></span>

<span data-ttu-id="f715a-1006">Geçici çözüm olarak, Ana Bilgisayar Filtreleme Ara Ware'i kullanın.</span><span class="sxs-lookup"><span data-stu-id="f715a-1006">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="f715a-1007">Ana Bilgisayar Filtreleme Middleware, [Microsoft.AspNetCore.App metapaketinde](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 veya 2.2) bulunan [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="f715a-1007">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="f715a-1008">Middleware tarafından <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>eklenir , <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>hangi çağırır:</span><span class="sxs-lookup"><span data-stu-id="f715a-1008">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="f715a-1009">Ana Bilgisayar Filtreleme Middleware varsayılan olarak devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="f715a-1009">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="f715a-1010">Ara yazılımı etkinleştirmek için `AllowedHosts` *appsettings.json*/uygulama ayarlarında bir anahtar*tanımlayın.\< EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="f715a-1010">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="f715a-1011">Değer, bağlantı noktası numarası olmayan ana bilgisayar adlarının yarı sütunlu sınırlı bir listesidir:</span><span class="sxs-lookup"><span data-stu-id="f715a-1011">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="f715a-1012">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="f715a-1012">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="f715a-1013">[İlezli Üstbilgi Middleware](xref:host-and-deploy/proxy-load-balancer) de bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçenek vardır.</span><span class="sxs-lookup"><span data-stu-id="f715a-1013">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="f715a-1014">İletilenmiş Üstbilgi Middleware ve Ana Bilgisayar Filtreleme Middleware farklı senaryolar için benzer işlevsellik var.</span><span class="sxs-lookup"><span data-stu-id="f715a-1014">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="f715a-1015">Üstbilgi nin ters proxy sunucusu veya `Host` yük dengeleyicisi ilerlerken üstbilgi korunmadığında Iletili Üstbilgi Middleware ile ayarlama `AllowedHosts` uygundur.</span><span class="sxs-lookup"><span data-stu-id="f715a-1015">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="f715a-1016">Kerkenez genel kullanıma dönük kenar sunucusu olarak kullanıldığında veya `AllowedHosts` `Host` üstbilgi doğrudan iledildiğinde Ana Bilgisayar Filtreleme Middleware ile ayarlama uygundur.</span><span class="sxs-lookup"><span data-stu-id="f715a-1016">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="f715a-1017">İlliyeli Üstbilgiler Middleware hakkında <xref:host-and-deploy/proxy-load-balancer>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="f715a-1017">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f715a-1018">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f715a-1018">Additional resources</span></span>

* <span data-ttu-id="f715a-1019">Linux'ta UNIX soketleri kullanırken, priz uygulama kapatıldığınızda otomatik olarak silinmez.</span><span class="sxs-lookup"><span data-stu-id="f715a-1019">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="f715a-1020">Daha fazla bilgi için [bu GitHub sorununa](https://github.com/dotnet/aspnetcore/issues/14134)bakın.</span><span class="sxs-lookup"><span data-stu-id="f715a-1020">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="f715a-1021">RFC 7230: İleti Sözdizimi ve Yönlendirme (Bölüm 5.4: Ana Bilgisayar)</span><span class="sxs-lookup"><span data-stu-id="f715a-1021">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
