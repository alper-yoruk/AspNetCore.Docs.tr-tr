---
title: Apache ile Linux'ta Ana ASP.NET Core'u barındırın
author: rick-anderson
description: Http trafiğini Kestrel'de çalışan ASP.NET Core web uygulamasına yönlendirmek için Apache'yi CentOS'ta ters proxy sunucusu olarak nasıl ayarlayabilirsiniz öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: shboyer
ms.custom: mvc
ms.date: 04/10/2020
uid: host-and-deploy/linux-apache
ms.openlocfilehash: 1256f6d21f94ef6c4baad7aae4bd0e751af5c675
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81224043"
---
# <a name="host-aspnet-core-on-linux-with-apache"></a><span data-ttu-id="4dbc7-103">Apache ile Linux'ta Ana ASP.NET Core'u barındırın</span><span class="sxs-lookup"><span data-stu-id="4dbc7-103">Host ASP.NET Core on Linux with Apache</span></span>

<span data-ttu-id="4dbc7-104">Yazar: [Shayne Boyer](https://github.com/spboyer)</span><span class="sxs-lookup"><span data-stu-id="4dbc7-104">By [Shayne Boyer](https://github.com/spboyer)</span></span>

<span data-ttu-id="4dbc7-105">Bu kılavuzu kullanarak, Http trafiğini [Kestrel](xref:fundamentals/servers/kestrel) sunucusunda çalışan ASP.NET Core web uygulamasına yönlendirmek için [Apache'yi](https://httpd.apache.org/) [CentOS 7'de](https://www.centos.org/) ters proxy sunucusu olarak nasıl ayarlayabilirsiniz öğrenin.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-105">Using this guide, learn how to set up [Apache](https://httpd.apache.org/) as a reverse proxy server on [CentOS 7](https://www.centos.org/) to redirect HTTP traffic to an ASP.NET Core web app running on [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span> <span data-ttu-id="4dbc7-106">[mod_proxy uzantısı](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) ve ilgili modüller sunucunun ters proxy'sini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-106">The [mod_proxy extension](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html) and related modules create the server's reverse proxy.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="4dbc7-107">Ön koşullar</span><span class="sxs-lookup"><span data-stu-id="4dbc7-107">Prerequisites</span></span>

* <span data-ttu-id="4dbc7-108">Sudo ayrıcalığına sahip standart bir kullanıcı hesabı ile CentOS 7 çalıştıran sunucu.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-108">Server running CentOS 7 with a standard user account with sudo privilege.</span></span>
* <span data-ttu-id="4dbc7-109">Sunucuya .NET Core çalışma saatini yükleyin.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-109">Install the .NET Core runtime on the server.</span></span>
   1. <span data-ttu-id="4dbc7-110">İndir [.NET Çekirdek sayfasını](https://dotnet.microsoft.com/download/dotnet-core)ziyaret edin.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-110">Visit the [Download .NET Core page](https://dotnet.microsoft.com/download/dotnet-core).</span></span>
   1. <span data-ttu-id="4dbc7-111">En son önizleme olmayan .NET Core sürümünü seçin.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-111">Select the latest non-preview .NET Core version.</span></span>
   1. <span data-ttu-id="4dbc7-112">Uygulamaları Çalıştır - Runtime altında tabloda en son önizleme olmayan çalışma **süresini indirin.**</span><span class="sxs-lookup"><span data-stu-id="4dbc7-112">Download the latest non-preview runtime in the table under **Run apps - Runtime**.</span></span>
   1. <span data-ttu-id="4dbc7-113">Linux **Paketi yöneticisi yönergeleri** bağlantısını seçin ve CentOS yönergelerini izleyin.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-113">Select the Linux **Package manager instructions** link and follow the CentOS instructions.</span></span>
* <span data-ttu-id="4dbc7-114">Mevcut bir ASP.NET Core uygulaması.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-114">An existing ASP.NET Core app.</span></span>

<span data-ttu-id="4dbc7-115">Paylaşılan çerçeveyi yükselttikten sonra gelecekte herhangi bir noktada, sunucu tarafından barındırılan ASP.NET Core uygulamalarını yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-115">At any point in the future after upgrading the shared framework, restart the ASP.NET Core apps hosted by the server.</span></span>

## <a name="publish-and-copy-over-the-app"></a><span data-ttu-id="4dbc7-116">Uygulama üzerinde yayımlama ve kopyalama</span><span class="sxs-lookup"><span data-stu-id="4dbc7-116">Publish and copy over the app</span></span>

<span data-ttu-id="4dbc7-117">Uygulamayı [çerçeveye bağımlı bir dağıtım](/dotnet/core/deploying/#framework-dependent-deployments-fdd)için yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-117">Configure the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd).</span></span>

<span data-ttu-id="4dbc7-118">Uygulama yerel olarak çalıştırılırsa ve güvenli bağlantılar (HTTPS) yapacak şekilde yapılandırılmamışsa, aşağıdaki yaklaşımlardan birini benimseyin:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-118">If the app is run locally and isn't configured to make secure connections (HTTPS), adopt either of the following approaches:</span></span>

* <span data-ttu-id="4dbc7-119">Uygulamayı güvenli yerel bağlantıları işleyeceğini zedele.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-119">Configure the app to handle secure local connections.</span></span> <span data-ttu-id="4dbc7-120">Daha fazla bilgi için [HTTPS yapılandırma](#https-configuration) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-120">For more information, see the [HTTPS configuration](#https-configuration) section.</span></span>
* <span data-ttu-id="4dbc7-121">`https://localhost:5001` *Properties/launchSettings.json* dosyasındaki `applicationUrl` özellikten (varsa) kaldırın.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-121">Remove `https://localhost:5001` (if present) from the `applicationUrl` property in the *Properties/launchSettings.json* file.</span></span>

<span data-ttu-id="4dbc7-122">Bir uygulamayı sunucuda çalıştırabilen bir dizine (örneğin, *&lt;bin/Release/ target_framework_moniker&gt;/publish)* paketlemek için geliştirme ortamından [dotnet yayımlamayı](/dotnet/core/tools/dotnet-publish) çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-122">Run [dotnet publish](/dotnet/core/tools/dotnet-publish) from the development environment to package an app into a directory (for example, *bin/Release/&lt;target_framework_moniker&gt;/publish*) that can run on the server:</span></span>

```dotnetcli
dotnet publish --configuration Release
```

<span data-ttu-id="4dbc7-123">Sunucuda .NET Core çalışma süresini korumamak isterseniz, uygulama bağımsız bir [dağıtım](/dotnet/core/deploying/#self-contained-deployments-scd) olarak da yayınlanabilir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-123">The app can also be published as a [self-contained deployment](/dotnet/core/deploying/#self-contained-deployments-scd) if you prefer not to maintain the .NET Core runtime on the server.</span></span>

<span data-ttu-id="4dbc7-124">kuruluşun iş akışına entegre olan bir aracı kullanarak ASP.NET Core uygulamasını sunucuya kopyalayın (örneğin, SCP, SFTP).</span><span class="sxs-lookup"><span data-stu-id="4dbc7-124">Copy the ASP.NET Core app to the server using a tool that integrates into the organization's workflow (for example, SCP, SFTP).</span></span> <span data-ttu-id="4dbc7-125">Var *dizininin* altında web uygulamalarını bulmak yaygındır (örneğin, *var/www/helloapp).*</span><span class="sxs-lookup"><span data-stu-id="4dbc7-125">It's common to locate web apps under the *var* directory (for example, *var/www/helloapp*).</span></span>

> [!NOTE]
> <span data-ttu-id="4dbc7-126">Üretim dağıtım senaryosunda, sürekli tümleştirme iş akışı, uygulamayı yayımlama ve varlıkları sunucuya kopyalama işini yapar.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-126">Under a production deployment scenario, a continuous integration workflow does the work of publishing the app and copying the assets to the server.</span></span>

## <a name="configure-a-proxy-server"></a><span data-ttu-id="4dbc7-127">Proxy sunucusu yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4dbc7-127">Configure a proxy server</span></span>

<span data-ttu-id="4dbc7-128">Ters proxy, dinamik web uygulamaları için ortak bir kurulumdur.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-128">A reverse proxy is a common setup for serving dynamic web apps.</span></span> <span data-ttu-id="4dbc7-129">Ters proxy HTTP isteğini sona erdirir ve ASP.NET uygulamasına iletilir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-129">The reverse proxy terminates the HTTP request and forwards it to the ASP.NET app.</span></span>

<span data-ttu-id="4dbc7-130">Proxy sunucusu, istekleriyerine yerine getirmek yerine istemci isteklerini başka bir sunucuya iletir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-130">A proxy server is one which forwards client requests to another server instead of fulfilling requests itself.</span></span> <span data-ttu-id="4dbc7-131">Ters proxy, genellikle rasgele istemciler adına sabit bir hedefe iletir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-131">A reverse proxy forwards to a fixed destination, typically on behalf of arbitrary clients.</span></span> <span data-ttu-id="4dbc7-132">Bu kılavuzda, Apache Kestrel'in ASP.NET Core uygulamasına hizmet verdiği sunucuda çalışan ters proxy olarak yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-132">In this guide, Apache is configured as the reverse proxy running on the same server that Kestrel is serving the ASP.NET Core app.</span></span>

<span data-ttu-id="4dbc7-133">İstekler ters proxy ile iletilir olduğundan, [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) paketinden [Iletili Üstbilgi Middleware](xref:host-and-deploy/proxy-load-balancer) kullanın.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-133">Because requests are forwarded by reverse proxy, use the [Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package.</span></span> <span data-ttu-id="4dbc7-134">Orta `Request.Scheme`yazılım, üstbilgi `X-Forwarded-Proto` kullanarak, URI'leri ve diğer güvenlik ilkelerini yeniden yönlendirmenin doğru çalışmasını güncelleştirir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-134">The middleware updates the `Request.Scheme`, using the `X-Forwarded-Proto` header, so that redirect URIs and other security policies work correctly.</span></span>

<span data-ttu-id="4dbc7-135">Kimlik doğrulama, bağlantı oluşturma, yeniden yönlendirmeler ve coğrafi konum gibi şemaya bağlı olan tüm bileşen, İlliyeli Üstbilgi Middleware'i çağırdıktan sonra yerleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-135">Any component that depends on the scheme, such as authentication, link generation, redirects, and geolocation, must be placed after invoking the Forwarded Headers Middleware.</span></span> <span data-ttu-id="4dbc7-136">Genel bir kural olarak, Iletili Üstbilgi Middleware tanılama ve hata işleme middleware dışında diğer ara önce çalışması gerekir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-136">As a general rule, Forwarded Headers Middleware should run before other middleware except diagnostics and error handling middleware.</span></span> <span data-ttu-id="4dbc7-137">Bu sıralama, iletilen üstbilgiler bilgilerine güvenen ara yazılımın işlem için üstbilgi değerlerini tüketmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-137">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span>

<span data-ttu-id="4dbc7-138"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> Diğer ara yazılımları `Startup.Configure` çağırmadan önce yöntemi en üstteki yönteme çağırın.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-138">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method at the top of `Startup.Configure` before calling other middleware.</span></span> <span data-ttu-id="4dbc7-139">Orta yazılımı ve `X-Forwarded-For` üstbilgiileri `X-Forwarded-Proto` iletecek şekilde yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-139">Configure the middleware to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

app.UseForwardedHeaders(new ForwardedHeadersOptions
{
    ForwardedHeaders = ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto
});

app.UseAuthentication();
```

<span data-ttu-id="4dbc7-140">Ara <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> yazılımda hayır belirtilmemişse, ileriye dönük `None`varsayılan üstbilgi .</span><span class="sxs-lookup"><span data-stu-id="4dbc7-140">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default headers to forward are `None`.</span></span>

<span data-ttu-id="4dbc7-141">Standart localhost adresi (127.0.0.1) dahil olmak üzere geri dönüş adreslerinde çalışan ekseksenler varsayılan olarak güvenilir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-141">Proxies running on loopback addresses (127.0.0.0/8, [::1]), including the standard localhost address (127.0.0.1), are trusted by default.</span></span> <span data-ttu-id="4dbc7-142">Kuruluş içindeki diğer güvenilir vekiller veya ağlar, Internet ve web sunucusu arasındaki istekleri <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> işlerse, bunları listeye <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-142">If other trusted proxies or networks within the organization handle requests between the Internet and the web server, add them to the list of <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies*> or <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks*> with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>.</span></span> <span data-ttu-id="4dbc7-143">Aşağıdaki örnek, IP adresi 10.0.0.100'de güvenilir bir proxy sunucusunu `KnownProxies` `Startup.ConfigureServices`Ilileli Üstbilgi Orta Ware'e ekler:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-143">The following example adds a trusted proxy server at IP address 10.0.0.100 to the Forwarded Headers Middleware `KnownProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
// using System.Net;

services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

<span data-ttu-id="4dbc7-144">Daha fazla bilgi için bkz. <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-144">For more information, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

### <a name="install-apache"></a><span data-ttu-id="4dbc7-145">Apache'yi Yükleyin</span><span class="sxs-lookup"><span data-stu-id="4dbc7-145">Install Apache</span></span>

<span data-ttu-id="4dbc7-146">CentOS paketlerini en son kararlı sürümlerine güncelleyin:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-146">Update CentOS packages to their latest stable versions:</span></span>

```bash
sudo yum update -y
```

<span data-ttu-id="4dbc7-147">Apache web sunucusunu CentOS'a `yum` tek bir komutla yükleyin:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-147">Install the Apache web server on CentOS with a single `yum` command:</span></span>

```bash
sudo yum -y install httpd mod_ssl
```

<span data-ttu-id="4dbc7-148">Komutu çalıştırdıktan sonra örnek çıktı:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-148">Sample output after running the command:</span></span>

```bash
Downloading packages:
httpd-2.4.6-40.el7.centos.4.x86_64.rpm               | 2.7 MB  00:00:01
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
Installing : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 
Verifying  : httpd-2.4.6-40.el7.centos.4.x86_64      1/1 

Installed:
httpd.x86_64 0:2.4.6-40.el7.centos.4

Complete!
```

> [!NOTE]
> <span data-ttu-id="4dbc7-149">Bu örnekte, CentOS 7 sürümü 64 bit olduğundan çıktı httpd.86_64 yansıtır.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-149">In this example, the output reflects httpd.86_64 since the CentOS 7 version is 64 bit.</span></span> <span data-ttu-id="4dbc7-150">Apache'nin nerede yüklü olduğunu `whereis httpd` doğrulamak için bir komut isteminden çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-150">To verify where Apache is installed, run `whereis httpd` from a command prompt.</span></span>

### <a name="configure-apache"></a><span data-ttu-id="4dbc7-151">Apache'yi Yapılandır</span><span class="sxs-lookup"><span data-stu-id="4dbc7-151">Configure Apache</span></span>

<span data-ttu-id="4dbc7-152">Apache için yapılandırma dosyaları dizinde `/etc/httpd/conf.d/` yer alır.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-152">Configuration files for Apache are located within the `/etc/httpd/conf.d/` directory.</span></span> <span data-ttu-id="4dbc7-153">*.conf* uzantılı tüm dosyalar, modülyüklemek için gerekli yapılandırma dosyalarını içeren modül yapılandırma dosyalarına `/etc/httpd/conf.modules.d/`ek olarak alfabetik sıraya göre işlenir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-153">Any file with the *.conf* extension is processed in alphabetical order in addition to the module configuration files in `/etc/httpd/conf.modules.d/`, which contains any configuration files necessary to load modules.</span></span>

<span data-ttu-id="4dbc7-154">Uygulama için *helloapp.conf*adlı bir yapılandırma dosyası oluşturun:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-154">Create a configuration file, named *helloapp.conf*, for the app:</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ServerName www.example.com
    ServerAlias *.example.com
    ErrorLog ${APACHE_LOG_DIR}helloapp-error.log
    CustomLog ${APACHE_LOG_DIR}helloapp-access.log common
</VirtualHost>
```

<span data-ttu-id="4dbc7-155">Blok, `VirtualHost` bir sunucudaki bir veya daha fazla dosyada birden çok kez görünebilir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-155">The `VirtualHost` block can appear multiple times, in one or more files on a server.</span></span> <span data-ttu-id="4dbc7-156">Önceki yapılandırma dosyasında, Apache bağlantı noktası 80'deki genel trafiği kabul eder.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-156">In the preceding configuration file, Apache accepts public traffic on port 80.</span></span> <span data-ttu-id="4dbc7-157">Etki `www.example.com` alanı sunuluyor ve `*.example.com` diğer ad aynı web sitesinde çözülür.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-157">The domain `www.example.com` is being served, and the `*.example.com` alias resolves to the same website.</span></span> <span data-ttu-id="4dbc7-158">Daha fazla bilgi için [Ad tabanlı sanal ana bilgisayar desteğine](https://httpd.apache.org/docs/current/vhosts/name-based.html) bakın.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-158">See [Name-based virtual host support](https://httpd.apache.org/docs/current/vhosts/name-based.html) for more information.</span></span> <span data-ttu-id="4dbc7-159">İstekler 127.0.0.1 adresindesunucunun 5000 bağlantı noktasına kökten yakınlanır.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-159">Requests are proxied at the root to port 5000 of the server at 127.0.0.1.</span></span> <span data-ttu-id="4dbc7-160">Çift yönlü iletişim `ProxyPass` için `ProxyPassReverse` gereklidir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-160">For bi-directional communication, `ProxyPass` and `ProxyPassReverse` are required.</span></span> <span data-ttu-id="4dbc7-161">Kerkenez'in IP/bağlantı noktasını değiştirmek için [Kerkenez: Bitiş Noktası yapılandırmasına](xref:fundamentals/servers/kestrel#endpoint-configuration)bakın.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-161">To change Kestrel's IP/port, see [Kestrel: Endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!WARNING]
> <span data-ttu-id="4dbc7-162">**VirtualHost** bloğunda uygun bir [ServerName yönergesi](https://httpd.apache.org/docs/current/mod/core.html#servername) belirtilmemesi, uygulamanızın güvenlik açıklarına maruz kalır.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-162">Failure to specify a proper [ServerName directive](https://httpd.apache.org/docs/current/mod/core.html#servername) in the **VirtualHost** block exposes your app to security vulnerabilities.</span></span> <span data-ttu-id="4dbc7-163">Alt etki alanı joker karakter `*.example.com`bağlama (örneğin, ) tüm üst etki alanını denetlerseniz (bunun `*.com`aksine, savunmasız dır) bu güvenlik riski oluşturmaz.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-163">Subdomain wildcard binding (for example, `*.example.com`) doesn't pose this security risk if you control the entire parent domain (as opposed to `*.com`, which is vulnerable).</span></span> <span data-ttu-id="4dbc7-164">Daha fazla bilgi için [rfc7230 bölüm-5.4'e](https://tools.ietf.org/html/rfc7230#section-5.4) bakın.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-164">See [rfc7230 section-5.4](https://tools.ietf.org/html/rfc7230#section-5.4) for more information.</span></span>

<span data-ttu-id="4dbc7-165">Günlük kullanımı `ErrorLog` ve `CustomLog` `VirtualHost` yönergeleri başına yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-165">Logging can be configured per `VirtualHost` using `ErrorLog` and `CustomLog` directives.</span></span> <span data-ttu-id="4dbc7-166">`ErrorLog`sunucunun hataları günlüğe kaydettiği `CustomLog` ve günlük dosyasının dosya adını ve biçimini ayarladığı yerdir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-166">`ErrorLog` is the location where the server logs errors, and `CustomLog` sets the filename and format of log file.</span></span> <span data-ttu-id="4dbc7-167">Bu durumda, istek bilgilerinin günlüğe kaydedildiği yer burasıdır.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-167">In this case, this is where request information is logged.</span></span> <span data-ttu-id="4dbc7-168">Her istek için bir satır vardır.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-168">There's one line for each request.</span></span>

<span data-ttu-id="4dbc7-169">Dosyayı kaydedin ve yapılandırmayı test edin.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-169">Save the file and test the configuration.</span></span> <span data-ttu-id="4dbc7-170">Her şey geçerse, yanıt `Syntax [OK]`.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-170">If everything passes, the response should be `Syntax [OK]`.</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="4dbc7-171">Apache'yi yeniden başlatın:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-171">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
sudo systemctl enable httpd
```

## <a name="monitor-the-app"></a><span data-ttu-id="4dbc7-172">Uygulamayı izleme</span><span class="sxs-lookup"><span data-stu-id="4dbc7-172">Monitor the app</span></span>

<span data-ttu-id="4dbc7-173">Apache şimdi kerkenez `http://localhost:80` `http://127.0.0.1:5000`üzerinde çalışan ASP.NET Core uygulamasına yapılan istekleri iletmek için kurulum.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-173">Apache is now setup to forward requests made to `http://localhost:80` to the ASP.NET Core app running on Kestrel at `http://127.0.0.1:5000`.</span></span> <span data-ttu-id="4dbc7-174">Ancak, Apache Kerkenez işlemini yönetmek için kurulmaz.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-174">However, Apache isn't set up to manage the Kestrel process.</span></span> <span data-ttu-id="4dbc7-175">*Sistemli* kullanın ve temel web uygulamasını başlatmak ve izlemek için bir hizmet dosyası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-175">Use *systemd* and create a service file to start and monitor the underlying web app.</span></span> <span data-ttu-id="4dbc7-176">*sistemli,* süreçleri başlatmak, durdurmak ve yönetmek için birçok güçlü özellik sağlayan bir init sistemidir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-176">*systemd* is an init system that provides many powerful features for starting, stopping, and managing processes.</span></span>

### <a name="create-the-service-file"></a><span data-ttu-id="4dbc7-177">Hizmet dosyasını oluşturma</span><span class="sxs-lookup"><span data-stu-id="4dbc7-177">Create the service file</span></span>

<span data-ttu-id="4dbc7-178">Hizmet tanımı dosyasını oluşturun:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-178">Create the service definition file:</span></span>

```bash
sudo nano /etc/systemd/system/kestrel-helloapp.service
```

<span data-ttu-id="4dbc7-179">Uygulama için örnek bir hizmet dosyası:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-179">An example service file for the app:</span></span>

```
[Unit]
Description=Example .NET Web API App running on CentOS 7

[Service]
WorkingDirectory=/var/www/helloapp
ExecStart=/usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=dotnet-example
User=apache
Environment=ASPNETCORE_ENVIRONMENT=Production 

[Install]
WantedBy=multi-user.target
```

<span data-ttu-id="4dbc7-180">Önceki örnekte, hizmeti yöneten kullanıcı `User` seçeneği tarafından belirtilir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-180">In the preceding example, the user that manages the service is specified by the `User` option.</span></span> <span data-ttu-id="4dbc7-181">Kullanıcı (`apache`) var olmalı ve uygulamanın dosyalarının düzgün sahipliğine sahip olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-181">The user (`apache`) must exist and have proper ownership of the app's files.</span></span>

<span data-ttu-id="4dbc7-182">Uygulamanın ilk kesme sinyalini aldıktan sonra kapanmasını beklemek için süreyi yapılandırmak için kullanın. `TimeoutStopSec`</span><span class="sxs-lookup"><span data-stu-id="4dbc7-182">Use `TimeoutStopSec` to configure the duration of time to wait for the app to shut down after it receives the initial interrupt signal.</span></span> <span data-ttu-id="4dbc7-183">Bu süre içinde uygulama kapanmazsa, uygulamayı sonlandırmak için SIGKILL verilir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-183">If the app doesn't shut down in this period, SIGKILL is issued to terminate the app.</span></span> <span data-ttu-id="4dbc7-184">Değeri birimsiz saniye (örneğin, `150`), zaman aralığı değeri (örneğin, `infinity` `2min 30s`) olarak veya zaman aralığını devre dışı düşürün.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-184">Provide the value as unitless seconds (for example, `150`), a time span value (for example, `2min 30s`), or `infinity` to disable the timeout.</span></span> <span data-ttu-id="4dbc7-185">`TimeoutStopSec``DefaultTimeoutStopSec` yönetici yapılandırma dosyasındaki değere varsayılanlar (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span><span class="sxs-lookup"><span data-stu-id="4dbc7-185">`TimeoutStopSec` defaults to the value of `DefaultTimeoutStopSec` in the manager configuration file (*systemd-system.conf*, *system.conf.d*, *systemd-user.conf*, *user.conf.d*).</span></span> <span data-ttu-id="4dbc7-186">Çoğu dağıtım için varsayılan zaman ası 90 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-186">The default timeout for most distributions is 90 seconds.</span></span>

```
# The default value is 90 seconds for most distributions.
TimeoutStopSec=90
```

<span data-ttu-id="4dbc7-187">Yapılandırma sağlayıcılarının ortam değişkenlerini okuyabilmesi için bazı değerlerin (örneğin, SQL bağlantı dizeleri) kaçılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-187">Some values (for example, SQL connection strings) must be escaped for the configuration providers to read the environment variables.</span></span> <span data-ttu-id="4dbc7-188">Yapılandırma dosyasında kullanılmak üzere düzgün bir şekilde kaçan bir değer oluşturmak için aşağıdaki komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-188">Use the following command to generate a properly escaped value for use in the configuration file:</span></span>

```console
systemd-escape "<value-to-escape>"
```

<span data-ttu-id="4dbc7-189">Kolon`:`( ) ayırıcıları çevre değişken adlarında desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-189">Colon (`:`) separators aren't supported in environment variable names.</span></span> <span data-ttu-id="4dbc7-190">Bir kolon yerine`__`bir çift alt çizgi ( ) kullanın.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-190">Use a double underscore (`__`) in place of a colon.</span></span> <span data-ttu-id="4dbc7-191">[Çevre Değişkenleri yapılandırma sağlayıcısı,](xref:fundamentals/configuration/index#environment-variables-configuration-provider) ortam değişkenleri yapılandırmaya okunduğunda çift alt puanları iki nokta üst üste dönüştürür.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-191">The [Environment Variables configuration provider](xref:fundamentals/configuration/index#environment-variables-configuration-provider) converts double-underscores into colons when environment variables are read into configuration.</span></span> <span data-ttu-id="4dbc7-192">Aşağıdaki örnekte, bağlantı dizesi anahtarı `ConnectionStrings:DefaultConnection` hizmet tanım `ConnectionStrings__DefaultConnection`dosyasına şu şekilde ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-192">In the following example, the connection string key `ConnectionStrings:DefaultConnection` is set into the service definition file as `ConnectionStrings__DefaultConnection`:</span></span>

```
Environment=ConnectionStrings__DefaultConnection={Connection String}
```

<span data-ttu-id="4dbc7-193">Dosyayı kaydedin ve hizmeti etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-193">Save the file and enable the service:</span></span>

```bash
sudo systemctl enable kestrel-helloapp.service
```

<span data-ttu-id="4dbc7-194">Hizmeti başlatın ve çalıştığını doğrulayın:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-194">Start the service and verify that it's running:</span></span>

```bash
sudo systemctl start kestrel-helloapp.service
sudo systemctl status kestrel-helloapp.service

● kestrel-helloapp.service - Example .NET Web API App running on CentOS 7
    Loaded: loaded (/etc/systemd/system/kestrel-helloapp.service; enabled)
    Active: active (running) since Thu 2016-10-18 04:09:35 NZDT; 35s ago
Main PID: 9021 (dotnet)
    CGroup: /system.slice/kestrel-helloapp.service
            └─9021 /usr/local/bin/dotnet /var/www/helloapp/helloapp.dll
```

<span data-ttu-id="4dbc7-195">Ters proxy yapılandırılan ve Kerkenez *sistemi*ile yönetilen ile, web uygulaması tamamen yapılandırılmış ve yerel makinede bir tarayıcıdan erişilebilir `http://localhost`.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-195">With the reverse proxy configured and Kestrel managed through *systemd*, the web app is fully configured and can be accessed from a browser on the local machine at `http://localhost`.</span></span> <span data-ttu-id="4dbc7-196">Yanıt üstbilgilerini inceleyen **Sunucu** üstbilgi, ASP.NET Core uygulamasının Kestrel tarafından sunulduğunu gösterir:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-196">Inspecting the response headers, the **Server** header indicates that the ASP.NET Core app is served by Kestrel:</span></span>

```
HTTP/1.1 200 OK
Date: Tue, 11 Oct 2016 16:22:23 GMT
Server: Kestrel
Keep-Alive: timeout=5, max=98
Connection: Keep-Alive
Transfer-Encoding: chunked
```

### <a name="view-logs"></a><span data-ttu-id="4dbc7-197">Günlükleri görüntüleme</span><span class="sxs-lookup"><span data-stu-id="4dbc7-197">View logs</span></span>

<span data-ttu-id="4dbc7-198">Kestrel kullanarak web uygulaması *systemd*kullanılarak yönetildiği için, olaylar ve işlemler merkezi bir günlükte günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-198">Since the web app using Kestrel is managed using *systemd*, events and processes are logged to a centralized journal.</span></span> <span data-ttu-id="4dbc7-199">Ancak, bu *günlük, systemd*tarafından yönetilen tüm hizmet ve işlemleriçin girişleri içerir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-199">However, this journal includes entries for all of the services and processes managed by *systemd*.</span></span> <span data-ttu-id="4dbc7-200">`kestrel-helloapp.service`-belirli öğeleri görüntülemek için aşağıdaki komutu kullanın:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-200">To view the `kestrel-helloapp.service`-specific items, use the following command:</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service
```

<span data-ttu-id="4dbc7-201">Zaman filtreleme için, komutla zaman seçeneklerini belirtin.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-201">For time filtering, specify time options with the command.</span></span> <span data-ttu-id="4dbc7-202">Örneğin, geçerli `--since today` gün için filtre `--until 1 hour ago` uygulama veya önceki saatin girişlerini görmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-202">For example, use `--since today` to filter for the current day or `--until 1 hour ago` to see the previous hour's entries.</span></span> <span data-ttu-id="4dbc7-203">Daha fazla bilgi [için journalctl için adam sayfasına](https://www.unix.com/man-page/centos/1/journalctl/)bakın.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-203">For more information, see the [man page for journalctl](https://www.unix.com/man-page/centos/1/journalctl/).</span></span>

```bash
sudo journalctl -fu kestrel-helloapp.service --since "2016-10-18" --until "2016-10-18 04:00"
```

## <a name="data-protection"></a><span data-ttu-id="4dbc7-204">Veri koruma</span><span class="sxs-lookup"><span data-stu-id="4dbc7-204">Data protection</span></span>

<span data-ttu-id="4dbc7-205">[ASP.NET Çekirdek Veri Koruma yığını,](xref:security/data-protection/introduction) kimlik doğrulama ara yazılımları (örneğin, çerez ara yazılımları) ve site ler arası istek sahteciliği (CSRF) korumaları da dahil olmak üzere birkaç ASP.NET Core ara [yazılımtarafından](xref:fundamentals/middleware/index)kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-205">The [ASP.NET Core Data Protection stack](xref:security/data-protection/introduction) is used by several ASP.NET Core [middlewares](xref:fundamentals/middleware/index), including authentication middleware (for example, cookie middleware) and cross-site request forgery (CSRF) protections.</span></span> <span data-ttu-id="4dbc7-206">Veri Koruma API'leri kullanıcı koduyla çağrılmasa bile, veri koruması kalıcı bir şifreleme [anahtar deposu](xref:security/data-protection/implementation/key-management)oluşturacak şekilde yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-206">Even if Data Protection APIs aren't called by user code, data protection should be configured to create a persistent cryptographic [key store](xref:security/data-protection/implementation/key-management).</span></span> <span data-ttu-id="4dbc7-207">Veri koruması yapılandırılmamışsa, anahtarlar bellekte tutulur ve uygulama yeniden başlatıldığında atılır.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-207">If data protection isn't configured, the keys are held in memory and discarded when the app restarts.</span></span>

<span data-ttu-id="4dbc7-208">Uygulama yeniden başlatıldığında anahtarlık bellekte depolanırsa:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-208">If the key ring is stored in memory when the app restarts:</span></span>

* <span data-ttu-id="4dbc7-209">Tüm çerez tabanlı kimlik doğrulama belirteçleri geçersiz kılındı.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-209">All cookie-based authentication tokens are invalidated.</span></span>
* <span data-ttu-id="4dbc7-210">Kullanıcıların bir sonraki istekleri üzerine yeniden oturum açmaları gerekmektedir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-210">Users are required to sign in again on their next request.</span></span>
* <span data-ttu-id="4dbc7-211">Anahtarlıkla korunan tüm verilerin şifresi artık çözülemez.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-211">Any data protected with the key ring can no longer be decrypted.</span></span> <span data-ttu-id="4dbc7-212">Buna [CSRF belirteçleri](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) ve [ASP.NET Core MVC TempData tanımlama bilgileri](xref:fundamentals/app-state#tempdata)de dahil olabilir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-212">This may include [CSRF tokens](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration) and [ASP.NET Core MVC TempData cookies](xref:fundamentals/app-state#tempdata).</span></span>

<span data-ttu-id="4dbc7-213">Anahtarlık devam etmek ve şifrelemek için veri korumayapılandırmak için bkz:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-213">To configure data protection to persist and encrypt the key ring, see:</span></span>

* <xref:security/data-protection/implementation/key-storage-providers>
* <xref:security/data-protection/implementation/key-encryption-at-rest>

## <a name="secure-the-app"></a><span data-ttu-id="4dbc7-214">Uygulamayı güvenli hale</span><span class="sxs-lookup"><span data-stu-id="4dbc7-214">Secure the app</span></span>

### <a name="configure-firewall"></a><span data-ttu-id="4dbc7-215">Güvenlik duvarını yapılandırma</span><span class="sxs-lookup"><span data-stu-id="4dbc7-215">Configure firewall</span></span>

<span data-ttu-id="4dbc7-216">*Firewalld* ağ bölgeleri desteği ile güvenlik duvarı yönetmek için dinamik bir daemon olduğunu.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-216">*Firewalld* is a dynamic daemon to manage the firewall with support for network zones.</span></span> <span data-ttu-id="4dbc7-217">Bağlantı noktaları ve paket filtreleme iptables tarafından yönetilebilir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-217">Ports and packet filtering can still be managed by iptables.</span></span> <span data-ttu-id="4dbc7-218">*Firewalld* varsayılan olarak yüklenmelidir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-218">*Firewalld* should be installed by default.</span></span> <span data-ttu-id="4dbc7-219">`yum`paketi yüklemek veya yüklü olduğunu doğrulamak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-219">`yum` can be used to install the package or verify it's installed.</span></span>

```bash
sudo yum install firewalld -y
```

<span data-ttu-id="4dbc7-220">Yalnızca `firewalld` uygulama için gereken bağlantı noktalarını açmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-220">Use `firewalld` to open only the ports needed for the app.</span></span> <span data-ttu-id="4dbc7-221">Bu durumda, port 80 ve 443 kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-221">In this case, port 80 and 443 are used.</span></span> <span data-ttu-id="4dbc7-222">Aşağıdaki komutlar kalıcı olarak 80 ve 443 bağlantı noktalarını açmak üzere ayarlar:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-222">The following commands permanently set ports 80 and 443 to open:</span></span>

```bash
sudo firewall-cmd --add-port=80/tcp --permanent
sudo firewall-cmd --add-port=443/tcp --permanent
```

<span data-ttu-id="4dbc7-223">Güvenlik duvarı ayarlarını yeniden yükleyin.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-223">Reload the firewall settings.</span></span> <span data-ttu-id="4dbc7-224">Varsayılan bölgede kullanılabilir hizmetleri ve bağlantı noktalarını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-224">Check the available services and ports in the default zone.</span></span> <span data-ttu-id="4dbc7-225">Seçenekler inceleyerek `firewall-cmd -h`kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-225">Options are available by inspecting `firewall-cmd -h`.</span></span>

```bash
sudo firewall-cmd --reload
sudo firewall-cmd --list-all
```

```bash
public (default, active)
interfaces: eth0
sources: 
services: dhcpv6-client
ports: 443/tcp 80/tcp
masquerade: no
forward-ports: 
icmp-blocks: 
rich rules: 
```

### <a name="https-configuration"></a><span data-ttu-id="4dbc7-226">HTTPS yapılandırması</span><span class="sxs-lookup"><span data-stu-id="4dbc7-226">HTTPS configuration</span></span>

<span data-ttu-id="4dbc7-227">**Uygulamayı güvenli (HTTPS) yerel bağlantılar için yapılandırın**</span><span class="sxs-lookup"><span data-stu-id="4dbc7-227">**Configure the app for secure (HTTPS) local connections**</span></span>

<span data-ttu-id="4dbc7-228">[Dotnet çalıştır](/dotnet/core/tools/dotnet-run) komutu, uygulamanın özellik tarafından sağlanan URL'leri dinleyecek şekilde yapılandıran *Özellikler/launchSettings.json* dosyasını `applicationUrl` kullanır (örneğin, `https://localhost:5001;http://localhost:5000`).</span><span class="sxs-lookup"><span data-stu-id="4dbc7-228">The [dotnet run](/dotnet/core/tools/dotnet-run) command uses the app's *Properties/launchSettings.json* file, which configures the app to listen on the URLs provided by the `applicationUrl` property (for example, `https://localhost:5001;http://localhost:5000`).</span></span>

<span data-ttu-id="4dbc7-229">Aşağıdaki yaklaşımlardan birini kullanarak uygulamayı `dotnet run` komut veya geliştirme ortamı (Visual Studio Code'ta F5 veya Ctrl+F5) için geliştirme aşamasında bir sertifika kullanacak şekilde yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-229">Configure the app to use a certificate in development for the `dotnet run` command or development environment (F5 or Ctrl+F5 in Visual Studio Code) using one of the following approaches:</span></span>

* <span data-ttu-id="4dbc7-230">[Yapılandırmadan varsayılan sertifikayı değiştirme](xref:fundamentals/servers/kestrel#configuration) (*Önerilen*)</span><span class="sxs-lookup"><span data-stu-id="4dbc7-230">[Replace the default certificate from configuration](xref:fundamentals/servers/kestrel#configuration) (*Recommended*)</span></span>
* [<span data-ttu-id="4dbc7-231">KestrelServerOptions.ConfigureHttpsDefaults</span><span class="sxs-lookup"><span data-stu-id="4dbc7-231">KestrelServerOptions.ConfigureHttpsDefaults</span></span>](xref:fundamentals/servers/kestrel#configurehttpsdefaultsactionhttpsconnectionadapteroptions)

<span data-ttu-id="4dbc7-232">**Güvenli (HTTPS) istemci bağlantıları için ters proxy'yi yapılandırın**</span><span class="sxs-lookup"><span data-stu-id="4dbc7-232">**Configure the reverse proxy for secure (HTTPS) client connections**</span></span>

<span data-ttu-id="4dbc7-233">Https için Apache yapılandırmak için *mod_ssl* modülü kullanılır.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-233">To configure Apache for HTTPS, the *mod_ssl* module is used.</span></span> <span data-ttu-id="4dbc7-234">*Httpd* modülü *kurulduğunda, mod_ssl* modülü de kuruldu.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-234">When the *httpd* module was installed, the *mod_ssl* module was also installed.</span></span> <span data-ttu-id="4dbc7-235">Yüklenmiyorsa, yapılandırmaya `yum` eklemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-235">If it wasn't installed, use `yum` to add it to the configuration.</span></span>

```bash
sudo yum install mod_ssl
```

<span data-ttu-id="4dbc7-236">HTTPS'yi zorlamak `mod_rewrite` için, URL yeniden yazmayı etkinleştirmek için modülü yükleyin:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-236">To enforce HTTPS, install the `mod_rewrite` module to enable URL rewriting:</span></span>

```bash
sudo yum install mod_rewrite
```

<span data-ttu-id="4dbc7-237">URL yeniden yazma ve port 443 üzerinde güvenli iletişim etkinleştirmek için *helloapp.conf* dosyasını değiştirin:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-237">Modify the *helloapp.conf* file to enable URL rewriting and secure communication on port 443:</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPreserveHost On
    ProxyPass / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5000/
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

> [!NOTE]
> <span data-ttu-id="4dbc7-238">Bu örnek, yerel olarak oluşturulan bir sertifika kullanıyor.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-238">This example is using a locally-generated certificate.</span></span> <span data-ttu-id="4dbc7-239">**SSLCertificateFile** etki alanı adı için birincil sertifika dosyası olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-239">**SSLCertificateFile** should be the primary certificate file for the domain name.</span></span> <span data-ttu-id="4dbc7-240">**SSLCertificateKeyFile** CSR oluşturulduğunda oluşturulan anahtar dosyası olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-240">**SSLCertificateKeyFile** should be the key file generated when CSR is created.</span></span> <span data-ttu-id="4dbc7-241">**SSLCertificateChainFile,** sertifika yetkilisi tarafından sağlanan ara sertifika dosyası (varsa) olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-241">**SSLCertificateChainFile** should be the intermediate certificate file (if any) that was supplied by the certificate authority.</span></span>

<span data-ttu-id="4dbc7-242">Dosyayı kaydedin ve yapılandırmayı test edin:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-242">Save the file and test the configuration:</span></span>

```bash
sudo service httpd configtest
```

<span data-ttu-id="4dbc7-243">Apache'yi yeniden başlatın:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-243">Restart Apache:</span></span>

```bash
sudo systemctl restart httpd
```

## <a name="additional-apache-suggestions"></a><span data-ttu-id="4dbc7-244">Ek Apaçi önerileri</span><span class="sxs-lookup"><span data-stu-id="4dbc7-244">Additional Apache suggestions</span></span>

### <a name="restart-apps-with-shared-framework-updates"></a><span data-ttu-id="4dbc7-245">Paylaşılan çerçeve güncellemeleriyle uygulamaları yeniden başlatın</span><span class="sxs-lookup"><span data-stu-id="4dbc7-245">Restart apps with shared framework updates</span></span>

<span data-ttu-id="4dbc7-246">Sunucuda paylaşılan çerçeveyi yükselttikten sonra, sunucu tarafından barındırılan ASP.NET Core uygulamalarını yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-246">After upgrading the shared framework on the server, restart the ASP.NET Core apps hosted by the server.</span></span>

### <a name="additional-headers"></a><span data-ttu-id="4dbc7-247">Ek üstbilgi</span><span class="sxs-lookup"><span data-stu-id="4dbc7-247">Additional headers</span></span>

<span data-ttu-id="4dbc7-248">Kötü amaçlı saldırılara karşı güvenli hale getirmek için, değiştirilmesi veya eklenmesi gereken birkaç üstbilgi vardır.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-248">In order to secure against malicious attacks, there are a few headers that should either be modified or added.</span></span> <span data-ttu-id="4dbc7-249">Modülün `mod_headers` yüklü olduğundan emin olun:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-249">Ensure that the `mod_headers` module is installed:</span></span>

```bash
sudo yum install mod_headers
```

#### <a name="secure-apache-from-clickjacking-attacks"></a><span data-ttu-id="4dbc7-250">Apaçi'yi clickjacking saldırılarından koruma</span><span class="sxs-lookup"><span data-stu-id="4dbc7-250">Secure Apache from clickjacking attacks</span></span>

<span data-ttu-id="4dbc7-251">*UI telafi saldırısı*olarak da bilinen [Clickjacking,](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger)bir web sitesi ziyaretçisi şu anda ziyaret ettiklerinden farklı bir sayfadaki bir bağlantıyı veya düğmeyi tıklatmak için kandırıldığı kötü amaçlı bir saldırıdır.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-251">[Clickjacking](https://blog.qualys.com/securitylabs/2015/10/20/clickjacking-a-common-implementation-mistake-that-can-put-your-websites-in-danger), also known as a *UI redress attack*, is a malicious attack where a website visitor is tricked into clicking a link or button on a different page than they're currently visiting.</span></span> <span data-ttu-id="4dbc7-252">Siteyi `X-FRAME-OPTIONS` güvenli hale getirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-252">Use `X-FRAME-OPTIONS` to secure the site.</span></span>

<span data-ttu-id="4dbc7-253">Tıklama kaçırma saldırılarını azaltmak için:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-253">To mitigate clickjacking attacks:</span></span>

1. <span data-ttu-id="4dbc7-254">*httpd.conf* dosyasını edin:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-254">Edit the *httpd.conf* file:</span></span>

   ```bash
   sudo nano /etc/httpd/conf/httpd.conf
   ```

   <span data-ttu-id="4dbc7-255">`Header append X-FRAME-OPTIONS "SAMEORIGIN"` satırını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-255">Add the line `Header append X-FRAME-OPTIONS "SAMEORIGIN"`.</span></span>
1. <span data-ttu-id="4dbc7-256">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-256">Save the file.</span></span>
1. <span data-ttu-id="4dbc7-257">Apaçi'yi yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-257">Restart Apache.</span></span>

#### <a name="mime-type-sniffing"></a><span data-ttu-id="4dbc7-258">MIME tipi koklama</span><span class="sxs-lookup"><span data-stu-id="4dbc7-258">MIME-type sniffing</span></span>

<span data-ttu-id="4dbc7-259">Üstbilgi, Internet Explorer'ın `X-Content-Type-Options` *MIME koklamasını* (dosyanın `Content-Type` içeriğinden bir dosyanın kini belirleme) engeller.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-259">The `X-Content-Type-Options` header prevents Internet Explorer from *MIME-sniffing* (determining a file's `Content-Type` from the file's content).</span></span> <span data-ttu-id="4dbc7-260">Sunucu üstbilgiyi `Content-Type` `text/html` `nosniff` seçenek kümesiyle ayarlarsa, Internet Explorer `text/html` içeriği dosyanın içeriğinden bağımsız olarak işler.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-260">If the server sets the `Content-Type` header to `text/html` with the `nosniff` option set, Internet Explorer renders the content as `text/html` regardless of the file's content.</span></span>

<span data-ttu-id="4dbc7-261">*httpd.conf* dosyasını edin:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-261">Edit the *httpd.conf* file:</span></span>

```bash
sudo nano /etc/httpd/conf/httpd.conf
```

<span data-ttu-id="4dbc7-262">`Header set X-Content-Type-Options "nosniff"` satırını ekleyin.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-262">Add the line `Header set X-Content-Type-Options "nosniff"`.</span></span> <span data-ttu-id="4dbc7-263">Dosyayı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-263">Save the file.</span></span> <span data-ttu-id="4dbc7-264">Apaçi'yi yeniden başlatın.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-264">Restart Apache.</span></span>

### <a name="load-balancing"></a><span data-ttu-id="4dbc7-265">Yük Dengeleme</span><span class="sxs-lookup"><span data-stu-id="4dbc7-265">Load Balancing</span></span>

<span data-ttu-id="4dbc7-266">Bu örnek, CentOS 7 ve Kestrel'de Apache'nin aynı örnek makinede nasıl ayarlandığı ve yapılandırılabildiği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-266">This example shows how to setup and configure Apache on CentOS 7 and Kestrel on the same instance machine.</span></span> <span data-ttu-id="4dbc7-267">Tek bir başarısızlık noktası nın olmaması için; *mod_proxy_balancer* kullanarak ve **VirtualHost** değiştirerek Apache proxy sunucusu arkasında web uygulamaları birden fazla örneklerini yönetmek için izin verecek.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-267">In order to not have a single point of failure; using *mod_proxy_balancer* and modifying the **VirtualHost** would allow for managing multiple instances of the web apps behind the Apache proxy server.</span></span>

```bash
sudo yum install mod_proxy_balancer
```

<span data-ttu-id="4dbc7-268">Aşağıda gösterilen yapılandırma dosyasında, bağlantı `helloapp` noktası 5001'de çalışacak şekilde ek bir örnek ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-268">In the configuration file shown below, an additional instance of the `helloapp` is set up to run on port 5001.</span></span> <span data-ttu-id="4dbc7-269">*Proxy* bölümü, bakiye *yan isteklerini*yüklemek için iki üyeli bir denge yapılandırması ile ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-269">The *Proxy* section is set with a balancer configuration with two members to load balance *byrequests*.</span></span>

```
<VirtualHost *:*>
    RequestHeader set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
</VirtualHost>

<VirtualHost *:80>
    RewriteEngine On
    RewriteCond %{HTTPS} !=on
    RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>

<VirtualHost *:443>
    ProxyPass / balancer://mycluster/ 

    ProxyPassReverse / http://127.0.0.1:5000/
    ProxyPassReverse / http://127.0.0.1:5001/

    <Proxy balancer://mycluster>
        BalancerMember http://127.0.0.1:5000
        BalancerMember http://127.0.0.1:5001 
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location />
        SetHandler balancer
    </Location>
    ErrorLog /var/log/httpd/helloapp-error.log
    CustomLog /var/log/httpd/helloapp-access.log common
    SSLEngine on
    SSLProtocol all -SSLv2
    SSLCipherSuite ALL:!ADH:!EXPORT:!SSLv2:!RC4+RSA:+HIGH:+MEDIUM:!LOW:!RC4
    SSLCertificateFile /etc/pki/tls/certs/localhost.crt
    SSLCertificateKeyFile /etc/pki/tls/private/localhost.key
</VirtualHost>
```

### <a name="rate-limits"></a><span data-ttu-id="4dbc7-270">Fiyat Limitleri</span><span class="sxs-lookup"><span data-stu-id="4dbc7-270">Rate Limits</span></span>

<span data-ttu-id="4dbc7-271">*httpd* modülünde yer alan *mod_ratelimit*kullanarak istemcilerin bant genişliği sınırlanabilir:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-271">Using *mod_ratelimit*, which is included in the *httpd* module, the bandwidth of clients can be limited:</span></span>

```bash
sudo nano /etc/httpd/conf.d/ratelimit.conf
```

<span data-ttu-id="4dbc7-272">Örnek dosya, kök konumu altında bant genişliğini 600 KB/sn olarak sınırlar:</span><span class="sxs-lookup"><span data-stu-id="4dbc7-272">The example file limits bandwidth as 600 KB/sec under the root location:</span></span>

```
<IfModule mod_ratelimit.c>
    <Location />
        SetOutputFilter RATE_LIMIT
        SetEnv rate-limit 600
    </Location>
</IfModule>
```

### <a name="long-request-header-fields"></a><span data-ttu-id="4dbc7-273">Uzun istek üstbilgi alanları</span><span class="sxs-lookup"><span data-stu-id="4dbc7-273">Long request header fields</span></span>

<span data-ttu-id="4dbc7-274">Proxy sunucusu varsayılan ayarları genellikle istek üstbilgi alanları 8.190 bayt ile sınırlandırın.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-274">Proxy server default settings typically limit request header fields to 8,190 bytes.</span></span> <span data-ttu-id="4dbc7-275">Bir uygulama varsayılan dan daha uzun alanlar gerektirebilir (örneğin, [Azure Etkin Dizinkullanan](https://azure.microsoft.com/services/active-directory/)uygulamalar).</span><span class="sxs-lookup"><span data-stu-id="4dbc7-275">An app may require fields longer than the default (for example, apps that use [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)).</span></span> <span data-ttu-id="4dbc7-276">Daha uzun alanlar gerekiyorsa, proxy sunucusunun [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) yönergesi ayarlama gerektirir.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-276">If longer fields are required, the proxy server's [LimitRequestFieldSize](https://httpd.apache.org/docs/2.4/mod/core.html#LimitRequestFieldSize) directive requires adjustment.</span></span> <span data-ttu-id="4dbc7-277">Uygulanacak değer senaryoya bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-277">The value to apply depends on the scenario.</span></span> <span data-ttu-id="4dbc7-278">Daha fazla bilgi için sunucunuzun belgelerine bakın.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-278">For more information, see your server's documentation.</span></span>

> [!WARNING]
> <span data-ttu-id="4dbc7-279">Gerekli `LimitRequestFieldSize` olmadıkça varsayılan değerini artırmayın.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-279">Don't increase the default value of `LimitRequestFieldSize` unless necessary.</span></span> <span data-ttu-id="4dbc7-280">Değeri artırmak, arabellek taşması (taşması) ve kötü amaçlı kullanıcıların Hizmet Reddi (DoS) saldırılarına riskini artırır.</span><span class="sxs-lookup"><span data-stu-id="4dbc7-280">Increasing the value increases the risk of buffer overrun (overflow) and Denial of Service (DoS) attacks by malicious users.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4dbc7-281">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="4dbc7-281">Additional resources</span></span>

* [<span data-ttu-id="4dbc7-282">Linux'ta .NET Core için ön koşullar</span><span class="sxs-lookup"><span data-stu-id="4dbc7-282">Prerequisites for .NET Core on Linux</span></span>](/dotnet/core/linux-prerequisites)
* <xref:test/troubleshoot>
* <xref:host-and-deploy/proxy-load-balancer>
